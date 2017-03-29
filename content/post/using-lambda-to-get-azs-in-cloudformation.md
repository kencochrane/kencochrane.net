+++
title = "Using Lambda to get a users Availability Zone list in Cloud Formation"
date = "2017-03-29"
tags = ["docker", "aws", "CloudFormation", "lambda", "availabilityZones"]
categories = ["blog"]
topics = ["blog"]
slug = "using-lambda-to-get-azs-in-cloudformation"
url = "2017/03/29/using-lambda-to-get-azs-in-cloudformation"
banner = "banners/aws.png"
+++

This is a follow up to my [Understanding AWS Regions and Availability Zones in Cloud Formation]({{< relref "post/cloudformation-region-availability-zone-tips.md" >}}) post that I made a little while ago.

Previously I talked about how you can dynamically select the number of AvailabilityZones (AZs) that you
want to deploy your instances too, based on the region. Since some regions only offer two AZs and others
have more, you will always want to deploy to as many as possible, to make your application better handle
an outage if an AZ were to go down.

One of the issues that I mentioned was the problem with the `Fn::GetAZs` function not always returning
the correct values. To make things better, I have decided to create a lambda function to make an API call
to get the value, and return that. Since the API calls should be more accurate, it will be easier to use,
and it will also allow me to standardize the output.

## Step 1: Create lambda function.
We need to create the Lambda function. Here is the python code.

```
import cfnresponse
import boto3
def handler(event, context):
    ec2c = boto3.client('ec2')
    r = ec2c.describe_availability_zones()
    azs = r.get('AvailabilityZones')
    az_list = [az.get('ZoneName') for az in azs if az.get('State') == 'available']
    az0 = az_list[0]
    az1 = az_list[1]
    if len(az_list) > 2:
        az2 = az_list[2]
    else:
        az2 = az0
    resp = {'AZ0': az0, 'AZ1': az1, 'AZ2': az2}
    cfnresponse.send(event, context, cfnresponse.SUCCESS, resp)
    return resp
```
Nothing too fancy, all that I'm doing is importing some libraries, making an API call,
parsing the results, and building the response, and sending it back. It is important to note that
for my use case, I always wanted 3 results to be returned, if there is only two AZ's in a region,
the third AZ (AZ2) will be the same as the first (AZ0). You might not need this, so change it to
match your use case.

## Step 2: Add Lambda Function to CloudFormation
All that I am doing is taking the code from above, and adding it into a CloudFormation Resource
With some properties.

```
"AZInfoFunction": {
  "Type": "AWS::Lambda::Function",
  "Condition": "LambdaSupported",
  "Properties": {
    "Code": {
        "ZipFile" : { "Fn::Join" : ["\n", [
            "import cfnresponse",
            "import boto3",
            "def handler(event, context):",
            "    ec2c = boto3.client('ec2')",
            "    r = ec2c.describe_availability_zones()",
            "    azs = r.get('AvailabilityZones')",
            "    az_list = [az.get('ZoneName') for az in azs if az.get('State') == 'available']",
            "    az0 = az_list[0]",
            "    az1 = az_list[1]",
            "    if len(az_list) > 2:",
            "        az2 = az_list[2]",
            "    else:",
            "        az2 = az0",
            "    resp = {'AZ0': az0, 'AZ1': az1, 'AZ2': az2}",
            "    cfnresponse.send(event, context, cfnresponse.SUCCESS, resp)",
            "    return resp"
        ]]}
    },
    "Handler": "index.handler",
    "Role": { "Fn::GetAtt" : ["LambdaExecutionRole", "Arn"] },
    "Runtime": "python2.7",
    "MemorySize": "128",
    "Timeout": "10"
  }
}
```
In order to run this code you need a few other things which are listed below.

Here is an IAM role for the lambda Function so that it has the correct API permissions.
This allows the ability to write to cloudwatch logs, and make the ec2:DescribeAvailabilityZones API call.
Anything else will be blocked.

```
"LambdaExecutionRole": {
  "Type": "AWS::IAM::Role",
  "Condition": "LambdaSupported",
  "Properties": {
    "AssumeRolePolicyDocument": {
      "Version": "2012-10-17",
      "Statement": [{
          "Effect": "Allow",
          "Principal": {"Service": ["lambda.amazonaws.com"]},
          "Action": ["sts:AssumeRole"]
      }]
    },
    "Path": "/",
    "Policies": [{
      "PolicyName": "root",
      "PolicyDocument": {
        "Version": "2012-10-17",
        "Statement": [{
            "Effect": "Allow",
            "Action": ["logs:CreateLogGroup",
                       "logs:CreateLogStream",
                       "logs:PutLogEvents"],
            "Resource": "arn:aws:logs:*:*:*"
        },{
            "Effect": "Allow",
            "Action": ["ec2:DescribeAvailabilityZones"],
            "Resource": "*"
        }]
      }
    }]
  }
}
```

Now we make a custom resource, which we will use when we want to call our Lambda function from the
template.

```
"AZInfo": {
  "Type": "Custom::AZInfo",
  "Condition": "LambdaSupported",
  "Properties": {
    "ServiceToken": { "Fn::GetAtt" : ["AZInfoFunction", "Arn"] },
    "Region": { "Ref": "AWS::Region" }
  }
 ```

## 3. Make Lambda Conditional
Lambda is a new service, and still isn't available in all of the different regions, because of this,
I still fall back to my old way of getting Availability Zones when Lambda isn't supported. To decide
if I'm going to use lambda or not, I'm using a condition statement.

```
"LambdaSupported": {
    "Fn::Equals": [
        {
            "Fn::FindInMap": [
                "AWSRegion2AZ",
                {
                    "Ref": "AWS::Region"
                },
                "LambdaSupport"
            ]
        },
        "yes"
    ]
}
```

This looks a my `AWSRegion2AZ` Mapping to see if Lambda is available in a given region, and if so, return true.

```
"AWSRegion2AZ": {
    "ap-northeast-1": {
        "Name": "Tokyo",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-northeast-2": {
        "Name": "Seoul",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-south-1": {
        "Name": "Mumbai",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-southeast-1": {
        "Name": "Singapore",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-southeast-2": {
        "Name": "Sydney",
        "LambdaSupport": "yes",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "ca-central-1": {
        "Name": "Central",
        "LambdaSupport": "no",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "eu-central-1": {
        "Name": "Frankfurt",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "eu-west-1": {
        "Name": "Ireland",
        "LambdaSupport": "yes",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "eu-west-2": {
        "Name": "London",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "sa-east-1": {
        "Name": "Sao Paulo",
        "LambdaSupport": "no",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "us-east-1": {
        "Name": "N. Virgina",
        "LambdaSupport": "yes",
        "NumAZs": "4",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "us-east-2": {
        "Name": "Ohio",
        "LambdaSupport": "yes",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "us-west-1": {
        "Name": "N. California",
        "LambdaSupport": "yes",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "us-west-2": {
        "Name": "Oregon",
        "LambdaSupport": "yes",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    }
}
```

## Step 4. Using the Function
Now that you have everything in place, you can use your custom lambda function. Here is how you use it, inside your template.

#### Get the first AZ in this region.
```
{"Fn::GetAtt": ["AZInfo", "AZ0"]}
```

#### Get the second AZ in this region.
```
{"Fn::GetAtt": ["AZInfo", "AZ1"]}
```

#### Get the third AZ in this region.
```
{"Fn::GetAtt": ["AZInfo", "AZ2"]}
```

#### Full example for selecting Availability Zones for a Subnet.
In this example, we will select the Availability Zone for the given subnet.

First, we check to see if `LambdaSupported`, and if so, we use Lambda. If not, we fall back to our old approach.
```
"PubSubnetAz1": {
    "DependsOn": "Vpc",
    "Properties": {
        "AvailabilityZone": {
            "Fn::If": [
                "LambdaSupported",
                {
                    "Fn::GetAtt": [
                        "AZInfo",
                        "AZ0"
                    ]
                },
                {
                    "Fn::Select": [
                        {
                            "Fn::FindInMap": [
                                "AWSRegion2AZ",
                                {
                                    "Ref": "AWS::Region"
                                },
                                "AZ0"
                            ]
                        },
                        {
                            "Fn::GetAZs": {
                                "Ref": "AWS::Region"
                            }
                        }
                    ]
                }
            ]
        },
        "CidrBlock": {
            "Fn::FindInMap": [
                "VpcCidrs",
                "pubsubnet1",
                "cidr"
            ]
        },
        "Tags": [
            {
                "Key": "Name",
                "Value": {
                    "Fn::Join": [
                        "-",
                        [
                            {
                                "Ref": "AWS::StackName"
                            },
                            "Subnet1"
                        ]
                    ]
                }
            }
        ],
        "VpcId": {
            "Ref": "Vpc"
        }
    },
    "Type": "AWS::EC2::Subnet"
},
```

I hope that was helpful, if you have any questions, hit me up on Twitter [@KenCochrane](https://twitter.com/kencochrane).
