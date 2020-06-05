---
layout: post
title: "[AWS] AWS Naming Conventions Best Practices"
author: "Carlos Requena"
lang: en
ref: aws-naming-conventions
tags:
   -  "[AWS] Amazon Web Services" 
---

# AWS Naming Conventions Best Practices

A naming convention is a well-defined set of rules useful for choosing the name of an AWS resource. Ensure that your AWS resources are using appropriate naming conventions for 
tagging in order to manage them more efficiently and adhere to AWS resource tagging best practices.

Naming (tagging) your AWS resources consistently have several advantages such as providing additional information about the resource location and usage, promoting consistency within the selected AWS region, distinguishing fast similar resources stacks from one another, avoiding naming collisions,
improving clarity in cases of potential ambiguity and enhancing aesthetic and professional appearance.


# Default Pattern Components

## Region Code
``(ue1|uw1|uw2|ew1|ec1|an1|an2|as1|as2|se1)`` <span style="color:silver;">For us-east-1, us-west-1, us-west-2, eu-west-1, eu-central-1, ap-northeast-1, ap-northeast-2, ap-southeast-1, ap-southeast-2, sa-east-1.</span>

## Availability Zone Code
`````([1-2]{1})([a-c]{1})````` <span style="color:silver;">(2a|2b|2c) for us-west-2a, us-west-2b, us-west-2c</span>

## Environment Code
``(d|t|s|p)`` <span style="color:silver;">For development, test, staging, production.</span>

## Application Code
``([a-z0-9\-]+)`` <span style="color:silver;">For the application stack that runs within the VPC (e.g. bid-data-app-stack).</span>

# VPC Naming Conventions

## Default Pattern Format
``vpc-RegionCode-EnvironmentCode-ApplicationStackCode``

## RegExp
``^vpc-(ue1|uw1|uw2|ew1|ec1|an1|an2|as1|as2|se1)-(d|t|s|p)-([a-z0-9\-]+)$``

## Examples
<span style="color:silver;">
vpc-us-east-1-p-bid-data-app-stack
<br>
vpc-us-west-2-p-web-app-stack
</span>



# EC2 Instance Naming Conventions

# Default Pattern Format
``ec2-RegionCode-AvailabilityZoneCode-EnvironmentCode-ApplicationCode``

## RegExp
```^ec2-(ue1|uw1|uw2|ew1|ec1|an1|an2|as1|as2|se1)-([1-2]{1})([a-c]{1})-(d|t|s|p)-([a-z0-9\-]+)$```

## Examples
<span style="color:silver;">
ec2-us-east-1-2a-p-tomcat
<br>
ec2-us-west-1-2b-p-nodejs 
</span>


<br>
> 
<span style="color:silver;">References</span>
- [Cloud Conformity VPCs](https://www.cloudconformity.com/knowledge-base/aws/VPC/vpc-naming-conventions.html#)
- [Cloud Conformity EC2](https://www.cloudconformity.com/knowledge-base/aws/EC2/ec2-instance-naming-conventions.html#)
