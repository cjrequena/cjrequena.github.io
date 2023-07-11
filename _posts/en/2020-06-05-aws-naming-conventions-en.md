---
layout: post
title: "[AWS] Naming Conventions Best Practices"
author: "Carlos Requena"
lang: en
ref: aws-naming-conventions
tags:
   -  "[AWS] Amazon Web Services" 
---

<div style="text-align:center"><span style="color:red;font-weight: bold">"Our true nationality is mankind." </span> <span style="color:black;font-weight: bold">--H. G. Wells</span></div>

# AWS Naming Conventions Best Practices

A naming convention is a well-defined set of rules useful for choosing the name of an AWS resource. Ensure that your AWS resources are using appropriate naming conventions for 
tagging in order to manage them more efficiently and adhere to AWS resource tagging best practices.

Naming (tagging) your AWS resources consistently have several advantages such as providing additional information about the resource location and usage, promoting consistency within the selected AWS region, distinguishing fast similar resources stacks from one another, avoiding naming collisions,
improving clarity in cases of potential ambiguity and enhancing aesthetic and professional appearance.

-----------
## Default Pattern Components

**Region Code**     
``(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)``

**Availability Zone Code**  
```regexp
([1-2]{1})([a-c]{1})
``` 

<span style="color:silver;">(2a|2b|2c) for us-west-2a, us-west-2b, us-west-2c</span>

**Environment Code**    
```regexp
(dev|test|stg|prod)
``` 

<span style="color:silver;">For development, test, staging, production.</span>

**Application Code**     
```regexp
([a-z0-9\-]+)
``` 

<span style="color:silver;">For the application stack that runs within the VPC (e.g. bid-data-app-stack).</span>

**Subnet Route Code**   
```regexp
(public|private)
``` 

<span style="color:silver;">For public, private</span>

-----------
## VPC Naming Conventions

**Default Pattern Format**  

``vpc-RegionCode-EnvironmentCode-ApplicationStackCode``

**RegExp**  

```regexp
^vpc-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-(dev|test|stg|prod)-([a-z0-9\-]+)$
```

**Examples**    

<span style="color:silver;">
vpc-us-east-1-prod-big-data-app-stack
<br>
vpc-us-west-2-prod-web-app-stack
</span>

-----------
## Subnet Naming Conventions

**Default Pattern Format**      
```regexp
subnet-RegionCode-AvailabilityZoneCode-SubnetRouteCode-EnvironmentCode-ApplicationStackCode``
```

**RegExp**      
```regexp
^subnet-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-([1-2]{1})([a-c]{1})-(public|private)-(dev|test|stg|prod)-([a-z0-9\-]+)$
```

**Examples**            
<span style="color:silver;">
subnet-us-east-1-2a-public-prod-web-app-stack
<br>
subnet-us-west-1-2b-private-prod-database-stack
</span>

-----------
## EC2 Instance Naming Conventions

**Default Pattern Format**
```regexp
ec2-RegionCode-AvailabilityZoneCode-EnvironmentCode-ApplicationCode
```

**RegExp**      
```regexp
^ec2-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-([1-2]{1})([a-c]{1})-(dev|test|stg|prod)-([a-z0-9\-]+)$
```

**Examples**
<span style="color:silver;">
ec2-us-east-1-2a-prod-tomcat
<br>
ec2-us-west-1-2b-prod-nodejs 
</span>

-----------
## PEM Key Naming Conventions

**Default Pattern Format**      
```regexp
key-RegionCode-EnvironmentCode-ApplicationCode
```

**RegExp**
```regexp
^key-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-(dev|test|stg|prod)-([a-z0-9\-]+)$
```

**Examples**        
<span style="color:silver;">
key-us-east-1-prod-web-server
<br>
key-us-west-1-prod-database 
</span>


<br>
> 
<span style="color:silver;">References</span>
- [Cloud Conformity VPCs](https://www.cloudconformity.com/knowledge-base/aws/VPC/vpc-naming-conventions.html#)
- [Cloud Conformity EC2](https://www.cloudconformity.com/knowledge-base/aws/EC2/ec2-instance-naming-conventions.html#)
