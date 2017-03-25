+++
title = "My CloudFormation wish list"
date = "2017-03-25"
tags = ["aws", "CloudFormation"]
categories = ["blog"]
topics = ["blog"]
slug = "my-cloudformation-wishlist"
url = "2017/03/25/my-cloudformation-wishlist"
banner = "banners/aws.png"
+++

I have been using [CloudFormation](https://aws.amazon.com/cloudformation/) a lot over the past year with my work on [Docker for AWS](https://www.docker.com/aws). It is a pretty good product, but there are still some rough edges that make using it a lot harder then it needs to be. I have compiled a list of changes and feature requests that I would like to see added to CloudFormation to make my life easier. I hope this blog post gets read by the CloudFormation team and helps guide their roadmap. If they have any questions, feel free to reach out to me on Twitter, Linkedin, etc any time.

## Intrinsic Functions
These functions allow you to do limited programming inside of a CloudFormation template. As of this writing, there are currently only 9, not counting Condition Functions. They include `Base64`, `FindInMap`, `GetAtt`, `GetAZs`, `ImportValue`, `Join`, `Select`, `Split`, and `Sub`. They have added a few recently, and I have some suggestions on some more they could add.

### Fn::Size
Would accept a String or a List, If a List it will return the size of the list. If a String it will return the length of the string.

This could also be broken up into two functions, one for string, and one for list, if that is easier.

For List:
```
{ "Fn::Size" : [ list of values ] }
```

For String
```
{ "Fn::Size" : {"Ref": "aParameter"} }
```

If you combine this with the `Fn::Split` function it will allow you to see how many items were entered when someone has a `CommaDelimitedList` parameter, which is helpful when it comes to Conditionals.

```
{ "Fn::Size" : { "Fn::Split" : [ "delimiter", "source string" ] } }
```

### Fn::RandomStr
This would return a random string of given length.

Example: random string with a length of 10.
```
{ "Fn::RandomStr" : {"length": 10} }
```

This is helpful when you need a unique name for a resource, or you need a temp password. I'm aware this wouldn't
be a good way to generate a password, but it is better then what is available now, which would be to hard code the password.

### Fn::GenPasswd
Building off of the `Fn::RandomStr`, a way to generate a unique password. The main difference with `Fn::RandomStr` is the ability to add a salt. Still not great since the salt would be hard coded into the template, but I'm sure the security folks can come up with a better approach, that is more secure. I'm just throwing it out there, to get the discussion started.

```
{ "Fn::GenPasswd" : {"length": 10, "salt": "mysalt" } }
```

### Fn::Lower
Given a string return the string all in lower case. Great for standardizing input.

```
{ "Fn::Lower" : {"Ref": "aParameter"} }
```

### Fn::Upper
Given a string return the string all in upper case. Great for standardizing input.

```
{ "Fn::Upper" : {"Ref": "aParameter"} }
```

### Fn::Trim
Given a string return the string with any whitespace before and after removed. Sometimes people add extra spaces to their parameters, and this causes problems, this would allow an easy way to standardize a string, to make sure it doesn't have any extra spaces.

```
{ "Fn::Trim" : {"Ref": "aParameter"} }
```

### Fn::Sort
This will take a List, and return it sorted. You can pass in ascending, or descending as parameters along with the list.

```
{ "Fn::Sort" : [ "ascending", [ list of values ] ] }
```

## Condition Functions
These are function that are used when building Conditionals.

### Fn::In
Will return True if a given string is in the list of values.

```
{ "Fn::In" : [ {"Ref": "aParameter"}, [ list of values ] ] }
```

Here is how it works with a Python example.
```
>>> names = ['ken', 'zander', 'maddox', 'emily']
>>> "ken" in names
True
>>> "charlie" in names
False
```

### Fn::Empty
Will return True if the given string is empty. It will first call a trim on the string to remove any whitespace.

```
{ "Fn::Empty": {"Ref": "aParameter"} }
```
The current way around this is to check if the given parameter is equal to "", but what if there is " ". If we had a trim Function it would make it a little easier, but we don't.

### Fix Fn::GetAzs
Right now `Fn::GetAzs` returns different values based on your account type. If you have an EC2-Classic account it returns all availability zones in the region, even if you don't have access to them. This is inconsistent with EC2-VPC accounts which will only return AZ's they have access too. Because of this, you can't really trust this value, it would be nice if it was fixed.

## Parameters
Parameters are one of the main parts of a CloudFormation template, that make it possible to customize the resulting stack, based on the requirements of the user. The current set of parameters are very limiting. I would like to see some changes to improve the UI/UX for the user, as well as some changes to help the CloudFormation developer.

### New AWS Specific Parameters
There are currently some AWS Specific parameters that are available, that make it easier to fill out the CloudFormation template. This is because it lets you pick values based on your account. These are a great start, but there are a few problems with them. It would also be nice if there were a few more added to make our lives better.

#### New Parameters
Here are a few of the new parameters I would like to see.

##### AWS::EC2::InstanceType
Each region allows a different set of EC2 Instance Types, and the list changes all of the time. The current approach is that you have a hard coded list in your CloudFormation template that has this list. You have currently have two options:

1. Add one list for all regions, this might result in a user selecting an instance type in a region that isn't supported.
2. Add a mapping of available instance types by region.

Number one is easier to maintain, but is more error prone for your users. Number two is less error prone, but a pain to keep up to date since the list changes all of the time.

Since AWS knows which instances are available in which region, why don't you provide a new parameter type that dynamically populates with only the available instances in each region. If someone wants a template that doesn't support all instance types, they can still do what they do now, and go with a hard coded list. But in my experience, most people don't care, and they want to offer all available instance types in a given region. Providing this parameter, will make me, and a lot of other people much happier.

##### AWS::EC2AccountType
There are a couple of different account types based on when your account was created. You are either EC2-Classic, or EC2-VPC. I wrote a blog post about this a little while ago, to out more, [check it out]({{< relref "post/cloudformation-region-availability-zone-tips.md" >}}). The main issue is that you don't know what kind of account a user has, and because of this, you don't know if you can trust the results from `Fn::GetAzs`. If we could find out which account type the user has, we can add some logic into the template to decide the best approach for getting availability zones.

### Conditional Parameters
It would be nice if you could add conditions to parameters and only show them if the condition is met. For example, in some regions EFS and Lambda are not available, so if you are in one of those regions, then don't bother showing parameters related to those services.

### Constraints for AWS Specific Parameter Types
The AWS Specific Parameter types are great, but currently you can't add any constraints on those values. Or if you can, I can't find the documentation on how to do it.

#### List Parameters
It would be helpful if you could have some constraints for the List parameters.

For example, if you offered a `List<AWS::EC2::AvailabilityZone::Name>` parameter, it would be nice to be able to say pick 2, and if they pick less or more than 2, then throw an error. Right now, they can pick what ever number they want, and you have to deal with that in the template.

Another problem is when you offer one of these parameters you need to have a value in it, you can't leave it blank. This causes a problem if you want to offer a template where someone brings their own VPC, and they need to enter the VPC along with the Subnets in each of the AZ's.

If you want to support three AZ's in the regions that have three AZ's then you will need to add three `AWS::EC2::Subnet::Id` fields. But if you do that, then what happens in regions that only have two Availability Zones? You are required to enter a value. It would be nice to allow some of the fields to be empty, to satisfy those cases. Combine that with my other request above where you can have conditional parameters, and that would make it easier for the user.

Another issue is that there is no way to cause the parameters to fail validation before the stack is launched. The most common problem is people forgetting to populate `AWS::EC2::KeyPair::KeyName`. If we could add a validation to these parameters, so that the template doesn't validate unless populated, before the stack is launched, it will help prevent these issues.

There is a little hack that works for KeyPair, where you can put a MinLength of 1, but this is undocumented, and may break in the future. It also doesn't work with [Troposhere](https://github.com/cloudtools/troposphere) since it is undocumented.

#### Min
Pick the minimum number of required selections

#### Max
Pick the maximum number of required selections

#### AllowEmpty
Allow the parameter to be empty.

#### Required
If specified parameter will be required, and will fail validation.

##### Examples
These values can be combined when appropriate.

```
"PubSubnetAz1": {
    "Description": "Public Subnet 1",
    "Type": "AWS::EC2::Subnet::Id",
    "AllowEmpty": true
},
"AZs": {
    "Description": "Pick your Availability Zones",
    "Type": "List<AWS::EC2::AvailabilityZone::Name>",
    "ConstraintDescription": "must be an Availability Zone",
    "Min": 2,
    "Max": 3,
    "Required": true
  }
```

### UI/UX changes
Right now there are only two types of input types, text boxes and select boxes. This is ok, but limiting. What happens if you need to enter a license key, or some extra user data shell scripts, it gets really ugly trying to cram that all into a one line text box.

It would be nice, if there was a way to specify what type of input you can have on a given parameter. Two of the input types I would really like are text area and checkbox.

#### TextArea and Checkbox
You could do this a couple of different ways, you could add a section under the `"Metadata":"AWS::CloudFormation::Interface"` section where you can specify the widget type, or you can add to the parameter section directly.

```
"License": {
    "ConstraintDescription": "Please enter your license key",
    "Description": "License Key",
    "Type": "String",
    "Widget": "TextArea"
},
"AutoUpdate": {
    "Description": "Enable Auto Update?",
    "Type": "String",
    "Default": "no",
    "Widget": "Checkbox"
}
```

`Widget` would only be a valid parameter for Type `String`, and default to what they have now, if not specified, to maintain backwards compatibility.

When you pick a checkbox widget it will render a checkbox, default will specify if it is checked or not by default. The two values are "yes" and "no". "yes" for when checked, "no" when not checked.

#### Advanced section
Some templates get really complicated with lots of parameters, most of the people don't need a lot of these parameters. It would be nice if there was a way to have an advanced section, where you can hide the advanced settings, and have the section collapsed by default. This will make it easier to use for basic users, but still give the advanced settings that pro users will want.

I know this is possible, since it is currently available on the second page of the wizard today. If we could have the same option on the first page, along with the ability specify which parameter groups go in which section. This could be done with the `"Metadata":"AWS::CloudFormation::Interface":"ParameterGroups"` section. You could add another parameter, that specifies which section it belongs in.

```
"Metadata": {
    "AWS::CloudFormation::Interface": {
        "ParameterGroups": [
            {   
                "Section": "Top",
                "Label": {
                    "default": "My first label"
                },
                "Parameters": [
                    "firstParam",
                    "secondParam"
                ]
            },
            {
                "Section": "Advanced",
                "Label": {
                    "default": "Advanced Properties"
                },
                "Parameters": [
                    "advParam1",
                    "advParam2",
                    "advParam3",
                ]
            }
        ]
    }
}
```

#### Validations

##### Validate by step
Right now the validations don't happen until the form is completely filled out, it would be nice if the validation happened when they finish from one step to the next, this would go a long way to improve usability.

##### Better Validation
Check the resources that someone has picked, if they are trying to pick something that isn't available in the given region, fail before you start, instead of starting the stack and failing after. For example, if they are trying to use a feature (Lambda, EFS) and it isn't available in the given region, don't even try to start the stack, fail right there. Same goes for instance type, if the instance type isn't available, don't even try to create the stack.

##### Quota checks
Before you try to launch the stack, you know exactly what resources are part of the stack, the currently running resources and what the user has for a quota for those resources. You have everything you need to know, if they can start the stack without causing a quota issue. Right now, you allow the stack to get created, and then fail mid stack deploy. You shouldn't allow the stack to even be created if it will fail. It will save the user time, and money.

## Variables
If you want to have some local variables in your template, you can't do it. You could hack the mappings section to store some constants but this isn't the same. It would be nice if there was a way to declare variables in your template, and reference them like you do with parameters and other Ref's. Azure ARM templates support variables, and it is a nice feature I think CloudFormation should add as well.

There are two good use cases for using variables. The first is to store constants in the template, like version numbers, etc. The other is a way to store values that are computed, and reference the variable everywhere instead of copying and pasting the calculation that gave you the computed value everywhere.

Here is an example:

Lets say you have three parameters and based on a condition you need to do something a little different.

```
"Fn::If": [
    "HasOnly2AZs", {
        "Fn::Join": [
            ",",
            [{"Ref": "PubSubnetAz1"},
             {"Ref": "PubSubnetAz2"}]
        ]
    },{"Fn::Join": [",",
        [{"Ref": "PubSubnetAz1"},
         {"Ref": "PubSubnetAz2"},
         {"Ref": "PubSubnetAz3"}]]
    }]
```

Without variables you would need to duplicate this code all over your template. If you had variables, you could store this value in the variable, and then use the variable in it's place. It will reduce the number of potential bugs, and reduces the size of the template, as well as makes it a little faster since the calculation is only done once.

To get this done, we could add a new section called "Variables" and a new built in function called "Variable"

```
{
    "Variables": {
        "version": "0.1.1",
        "myVariable": {
            "Fn::If": [
                "HasOnly2AZs", {
                    "Fn::Join": [
                        ",",
                        [{"Ref": "PubSubnetAz1"},
                         {"Ref": "PubSubnetAz2"}]
                    ]
                },{"Fn::Join": [",",
                    [{"Ref": "PubSubnetAz1"},
                     {"Ref": "PubSubnetAz2"},
                     {"Ref": "PubSubnetAz3"}]]
                }]
        }
    }
}

```

To use it you would so something like this. Similar to `"Ref"`

```
{"Variable": "myVariable"}
```

## Resources
One of my current pet peeves is the inability to get properties for resources that are not part of your stack. For example, if you have a `AWS::EC2::VPC::Id` parameter in your template, you can't get any properties from that VPC even though you know the Id. It would be nice to be able to get the CIDR, etc and use that inside of the template. If we could do that, it would remove the need of adding yet another parameter to the template.

## Conclusion
I hope this blog post finds it's way to the CloudFormation team inside of AWS. If so, I hope they don't consider this blog post as a bad thing, that wasn't my goal. I really like what they have done so far, and I understand how hard it is to maintain a product that is used by a large user base, and keeping everyone happy. They have to deal with keeping the service up and running, as well as implementing internal changes, and then once those are all done, they probably have very little time to add new features.

If anyone from the CloudFormation team wants to talk, I would love to chat and answer any questions about the stuff I have mentioned above. You can find me on Twitter, linkedin, and my email address is all over the place on github.

If you are also a CloudFormation user and you have ideas for stuff that I haven't already listed, feel free to reach out as well, and I'll update the blog post to include the best ideas.
