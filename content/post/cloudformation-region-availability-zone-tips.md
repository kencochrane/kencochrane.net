+++
date = "2016-12-16"
tags = ["aws", "docker", "CloudFormation", "availabilityZones"]
categories = ["blog"]
topics = ["blog"]
title = "Understanding AWS Regions and Availability Zones in CloudFormation"
+++

I recently talked about [my work on Docker for AWS]({{< relref "post/docker-for-aws.md" >}}) while working on this project I had to do a lot of research around AWS Regions and Availability Zones, and how they work with CloudFormation. Here are the notes that I gathered.

### What is the difference between an AWS Region and an Availability Zone?
The easiest way to answer this questions is to start backwards.

1. Servers live in a data center.
2. One or more data centers make up an Availability Zone.
3. Two or more Availability Zones in a given area, make up an AWS Region.

If a region was a Hotel, the data center would be the hotel room, and the hotel floor would be the Availability Zone. All of the hotel floors are in the same Hotel.

Availability Zones are usually close enough to each other so that their is limited delay between network calls between the two AZ's. But they are far enough away where an issue with one (power outage, flood, fire, etc) would not affect the other. They usually have separate utilities (power, internet, etc), and don't depend on either other. This way if one goes away, the other can keep going, like nothing happened.

Here is a link to the [current list of AWS regions and the number of availability Zones](https://aws.amazon.com/about-aws/global-infrastructure/) in each region.

### The availability zone lie
If you look at the link above, you will notice that it will show the number of availability zones for each region, but that is kind of a lie. Just because a region has that many availability zones, doesn't mean you have access to them. For example, `us-west-1` Northern California says it has 3 availability Zones, but if you log into your EC2 dashboard and look at which AZ's you have access too, you most likely will only see 2. The same goes with us-east-1
, you will see it say 5, but you will only have access to 4.

It can do this for a number of reasons, maybe one of the AZ's is running out of space, and they stopped allowing new accounts use that Zone. Or maybe in the case with us-east-1, you really don't need 5 AZ's in one region, and so they spread out the accounts across the five, randomly assigning new accounts to 4 of them.

Either way, you should double check to make sure you know how many you have access too, and not rely on marketing materials.

### my us-east-1a is not equal to your us-east-1a
Another trick that AWS does is they randomly assign the AZ names between accounts. So the AZ that points to us-east-1a in my accounts, is probably different then the one in your account. I think it does this so that in case everyone puts everything in us-east-1a it will at least spread the load between the different AZ's in the region. But I'm sure there might be other reasons as well. Keep this mind if you are trying to setup services between accounts, you might think you are loading servers in the same AZ, but they might not be. Since the AZ's are next to each other, and the network between them is super fast, you shouldn't have to worry.

### EC2-Classic vs EC2-VPC AWS Accounts
If you have an AWS account that was created before December 4th, 2013 you have what is known as an EC2-Classic account on regions where you have previously deployed resources. EC2-Classic accounts don’t have default VPC’s or the associated subnets, etc. This causes a problem when using a CloudFormation template that uses the [Fn:GetAZs](http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/intrinsic-function-reference-getavailabilityzones.html) function they provide to determine which availability zones you have access too. When used in a region where you have EC2-Classic, this function will return all availability zones for the region, even ones you don’t have access too. When you have an EC2-VPC account, it will return only the availability zones you have access to.

This makes it hard to develop a CloudFormation template where you want to automatically assign AZ's, since it will have different functionality for different users.

A common CloudFormation error looks something like this:

“Value (us-east-1a) for parameter availabilityZone is invalid. Subnets can currently only be created in the following availability zones: us-east-1d, us-east-1c, us-east-1b, us-east-1e.”

The error above would happen if you have an EC2-Classic account, and you don’t have access to the `a` and `b` availability zones for that region.

There isn’t anything we can do right now to fix this issue, we have contacted Amazon, and we are hoping they will be able to fix the function, or provide us with a way to determine if an account is either EC2-Classic or EC2-VPC, so we can act accordingly.

How to tell if you have this issue? This AWS documentation page will describe how you can tell if you have EC2-Classic, EC2-VPC or both. http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-platforms.html

How to fix:
There are a few work arounds that you can have people try, but they aren't great.

- Use a region that doesn’t have EC2-Classic. The most common region with this issue is `us-east-1`. So try another region, `us-west-1`, `us-west-2`, or the new `us-east-2`. These regions will more then likely be setup with EC2-VPC and you will not longer have this issue.

- Create an new AWS account, all new accounts will be setup using EC2-VPC and will not have this problem.
You can try and contact AWS support to convert your EC2-Classic account to a EC2-VPC account. For more information checkout the following answer for “Q. I really want a default VPC for my existing EC2 account. Is that possible?” on https://aws.amazon.com/vpc/faqs/#Default_VPCs

Helpful links:

- http://docs.aws.amazon.com/AmazonVPC/latest/UserGuide/default-vpc.html
- http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/ec2-supported-platforms.html
- http://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-vpc.html
- https://aws.amazon.com/vpc/faqs/#Default_VPCs
- https://aws.amazon.com/blogs/aws/amazon-ec2-update-virtual-private-clouds-for-everyone/


#### Suggested Improvements
1. Change `Fn:GetAZs` to only return Availability Zones you have access too, regardless of which type of account you have.
2. Add a CFN function that would let you know if an account in a region was an EC2-Classic or EC2-VPC, so that you could code around the issue yourself.

### How to configure a CloudFormation template to work on all Regions regardless if they have 2 and 3 Availability Zones.
Due to the way Docker Swarm works, we try to spread out the Managers so they are each in their own Availability Zone. Ideally 3 managers, on 3 different Availability Zones. If we lose an Availability Zone, we only lose one manager, and we still have a quorum, so there are no downtime issues.

The problem is that half of the AWS regions have 2 AZ's the other half have 3 or more. Originally we setup our CloudFormation for the least common denominator, which was 2. This wasn't ideal, because that means on a 3 manager swarm, we have two managers on one AZ and one on another AZ. If we lose the AZ with two managers, we lose quorum and Docker Swarm will have issues.

So we had a few different options.
1. Ask AWS to make sure all regions have at least 3 Availability Zones.
2. Only support the regions that have 3 Availability Zones or more.
3. Have more than one template, one for 2 AZ regions, and one for 3 or more AZ regions.
4. Some how get the CloudFormation template to work with both 3 and 2 AZ regions.

Number one wasn't going to happen anytime soon, even if they agreed, it would take a while to implement. So that option was out.
Number two would leave off half of the AWS regions, and people wouldn't like that.
Number three would be a pain to maintain, and would cause unneeded confusion for people.

That left number four, which seems easy at first, unless you have ever used CloudFormation before. CloudFormation is good, but it is very limited and doesn't offer much in the ways of dynamic logic or functionality. After racking my brain for a few days, I decided to think out of the box, and I came up with a solution that handles our requirements, and isn't too much of a hack.

How it works.

I created a new Mapping called `AWSRegion2AZ`, this is a hard coded mapping that lists the region, along with the Name, number of AZ's and three variables. AZ0, AZ1, and AZ2. Here is what it looks like.

```
"AWSRegion2AZ": {
    "ap-northeast-1": {
        "Name": "Tokyo",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-northeast-2": {
        "Name": "Seoul",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-south-1": {
        "Name": "Mumbai",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-southeast-1": {
        "Name": "Singapore",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "ap-southeast-2": {
        "Name": "Sydney",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "ca-central-1": {
        "Name": "Central",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "eu-central-1": {
        "Name": "Frankfurt",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "eu-west-1": {
        "Name": "Ireland",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "eu-west-2": {
        "Name": "London",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "sa-east-1": {
        "Name": "Sao Paulo",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "us-east-1": {
        "Name": "N. Virgina",
        "NumAZs": "4",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "us-east-2": {
        "Name": "Ohio",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    },
    "us-west-1": {
        "Name": "N. California",
        "NumAZs": "2",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "0"
    },
    "us-west-2": {
        "Name": "Oregon",
        "NumAZs": "3",
        "AZ0": "0",
        "AZ1": "1",
        "AZ2": "2"
    }
}
```

The Name and number of AZ's is self explanatory, so lets me jump to AZ0, AZ1, and AZ2. They are index references for the GetAZs function. For 2 AZ regions, we will have values of 0,1,0 which will reuse the first AZ. For 3 or more AZ regions, we will have values of 0,1,2. Since we have 3 AZs we can use all three.

When you create a Subnet inside of a VPC you do something like this:

```
"PubSubnetAz1" : {
    "DependsOn" : "Vpc",
    "Type" : "AWS::EC2::Subnet",
    "Properties" : {
        "VpcId" : { "Ref" : "Vpc" },
        "CidrBlock" : { "Fn::FindInMap" : [ "VpcCidrs", "pubsubnet1", "cidr" ] },
        "AvailabilityZone" : { "Fn::Select" : [
            {"Fn::FindInMap": ["AWSRegion2AZ", { "Ref": "AWS::Region" }, "AZ0"]},
            { "Fn::GetAZs" : { "Ref" : "AWS::Region" } } ] },
        "Tags": [
            { "Key" : "Name", "Value" : { "Fn::Join": [ "-", [ { "Ref": "AWS::StackName"}, "Subnet1"] ] } }
        ]
    }
},
```

This takes a number of parameters but the one we care about is the `AvailabilityZone` one. We create 3 of these subnets no matter way, even if regions only have 2 availability zones. (Basically the same as above, but only minor changes) When we have a region with only two AZ's there will be 3 subnets, 2 in the first AZ, 1 in the second. When we have a region with 3 or more AZ's there will be a subnet for each of the first 3 AZ's.

We assign the AZ by using this function.

```
{ "Fn::Select" : [
    {"Fn::FindInMap": ["AWSRegion2AZ", { "Ref": "AWS::Region" }, "AZ0"]},
    { "Fn::GetAZs" : { "Ref" : "AWS::Region" } }
    ] }
```

It is a little much to look at, so lets break it up in two smaller parts. First thing it does is gets the list of AZs for the given region. It returns something like this. ['us-east-1a', 'us-east-1b', 'us-east-1c', 'us-east-1d']

```
{ "Fn::GetAZs" : { "Ref" : "AWS::Region" } }
```

Now given the region we are in, lets assume us-east-1, we want to get the `"AWSRegion2AZ"."us-east-1"."AZ0"` which gives us "0"
```
{"Fn::FindInMap": ["AWSRegion2AZ", { "Ref": "AWS::Region" }, "AZ0"]},
```

That gives us the following: which means give us the first element from the AZ list, which is 'us-east-1a'

```
{ "Fn::Select" : [
    "0",
    "['us-east-1a', 'us-east-1b', 'us-east-1c', 'us-east-1d']" ] }
```
This doesn't matter much for the first 2 subnets, but it does matter on the 3rd one. It will either select the first AZ (for 2 AZ regions), or it will select the 3rd AZ for Regions with 3 or more.

This allows us to dynamically put subnets in either 2 or 3 AZ regions. You might be thinking, so what, how does this help me? Well the reason why it matters, is because when you are setting up an AutoScalingGroup, you decide where the servers are put by giving it a list of subnets to shuffle through when placing them. Since subnets are in an AZ, this is how they get spread evenly across AZs.

Here is a snippet of the AutoScalingGroup to show how you use either 2 or 3 subnets. This is using a conditional called `HasOnly2AZs`, which is listed below. If `HasOnly2AZs` is true, then it uses the 2 subnet list, or else it uses the 3.

```
"Type" : "AWS::AutoScaling::AutoScalingGroup",
"Properties" : {
    "VPCZoneIdentifier" : [{
        "Fn::If": [
          "HasOnly2AZs",
            { "Fn::Join" : [",", [ { "Ref" : "PubSubnetAz1" }, { "Ref" : "PubSubnetAz2" } ] ] },
            { "Fn::Join" : [",", [ { "Ref" : "PubSubnetAz1" }, { "Ref" : "PubSubnetAz2" }, { "Ref" : "PubSubnetAz3" } ] ] }
        ]
    }],
}

```

Here is the `HasOnly2AZs` Condition. It uses the `NumAZs` property of the `AWSRegion2AZ` mapping, that we talked about above. If the given region, has a NumAZs value of 2, then it is True. Else it is false.

```
"Conditions" : {
    "HasOnly2AZs" : {"Fn::Equals" : [{"Fn::FindInMap": ["AWSRegion2AZ", { "Ref": "AWS::Region" }, "NumAZs"]}, "2"]}
},
```

Now that we have this, we have have one CloudFormation template that will use as many AZs as available. We can recommend that people only use regions with 3 AZs, but if they decide they need to use a region that only has 2 AZs then they can still down.

The downsides to this approach is that we have hard coded the list of regions and AZs into the CloudFormation template, when they add a new region, or a new AZ, we will need to also update the template. Considering this doesn't happen often, and there is usually stuff we need to do anyway when a new region goes online (copy AMIs to the new region), this is a low risk.

That is all that I have for now, I hope it was helpful. I'll try and keep this page updated. If you have something you think I should add, send me a message on twitter (@kencochrane).
