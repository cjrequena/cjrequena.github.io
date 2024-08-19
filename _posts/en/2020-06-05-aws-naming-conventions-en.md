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

**{RegionCode}**     
``(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)``

**{AvailabilityZoneCode}**  
``([1-2]{1})([a-c]{1})``

**{EnvironmentCode}**    
``(dev|test|stg|prod)`` 

**{ApplicationCode}**     
``([a-z0-9\-]+)`` 

**{SubnetRouteCode}**   
``(public|private)``

**{ResourceCode}**   
``(ec2|alb|nlb)``

-----------
## VPC Naming Conventions

**Default Pattern Format**  

``vpc-{RegionCode}-{EnvironmentCode}-{ApplicationCode}``

**RegExp**  

``^vpc-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-(dev|test|stg|prod)-([a-z0-9\-]+)$``

**Examples**    

<span style="color:silver;">
vpc-us-east-1-prod-bigdataappstack
<br>
vpc-us-west-2-prod-webappstack
</span>

-----------
## Subnet Naming Conventions

**Default Pattern Format**      
``subnet-{RegionCode}-{AvailabilityZoneCode}-{SubnetRouteCode}-{EnvironmentCode}-{ApplicationStackCode}``

**RegExp**      
``^subnet-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-([1-2]{1})([a-c]{1})-(public|private)-(dev|test|stg|prod)-([a-z0-9\-]+)$``

**Examples**            
<span style="color:silver;">
subnet-us-east-1-2a-public-prod-webappstack
<br>
subnet-us-west-1-2b-private-prod-databasestack
</span>

-----------
## Security Group Naming Conventions

**Default Pattern Format**      
``sgr_{ResourceCode}_{RegionCode}_{EnvironmentCode}-{ApplicationCode}``

**RegExp**      
``^sgr_(ec2|alb|nlb)_(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)_(dev|test|stg|prod)_([a-z0-9\-]+)$``

**Examples**        
<span style="color:silver;">
sgr_ec2_us-west-1_dev_sample-app-instance-1
</span>

-----------
## EC2 Instance Naming Conventions

**Default Pattern Format**      
``ec2_{RegionCode}_{AvailabilityZoneCode}_{EnvironmentCode}_{ApplicationCode}``

**RegExp**      
``^ec2_(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)_([1-2]{1})([a-c]{1})_(dev|test|stg|prod)_([a-z0-9\-]+)$``

**Examples**        
<span style="color:silver;">
ec2_us-east-1_2a_prod_tomcat
<br>
ec2_us-west-1_2b_prod_nodejs 
</span>

-----------
## PEM Key Naming Conventions

**Default Pattern Format**      
``key-RegionCode-EnvironmentCode-ApplicationCode``

**RegExp**      
``^key-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-(dev|test|stg|prod)-([a-z0-9\-]+)$``

**Examples**        
<span style="color:silver;">
key-us-east-1-prod-web-server
<br>
key-us-west-1-prod-database 
</span>

-----------
## ECS Cluster Naming Conventions

**Default Pattern Format**      
``ecs-cluster_{RegionCode}_{EnvironmentCode}_{ApplicationCode}``

**RegExp**      
``^ecs-cluster_(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)_(dev|test|stg|prod)_([a-z0-9\-]+)$``

**Examples**        
<span style="color:silver;">
ecs-cluster_us-east-1_prod_tomcat
<br>
ecs-cluster_us-east-1_prod_micro-app
</span>

## ECS Task Definition Naming Conventions

**Default Pattern Format**      
``ecstd-{RegionCode}-{EnvironmentCode}-{ApplicationCode}-{Vesion}``

**RegExp**      
``^ecstd-(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)-(dev|test|stg|prod)-([a-z0-9\-]+)-v1$``

**Examples**        
<span style="color:silver;">
ecstd-us-east-1-prod-tomcat-v1
<br>
ecstd-us-east-1-prod-micro_app-v1
</span>

## ECS Service Naming Conventions

**Default Pattern Format**      
``ecs-service_{RegionCode}_{EnvironmentCode}_{ApplicationCode}``

**RegExp**      
``^ecs-service_(us-east-1|us-west-1|us-west-2|eu-west-1|eu-central-1|ap-northeast-1|ap-northeast-2|ap-southeast-1|ap-southeast-2|sa-east-1)_(dev|test|stg|prod)_([a-z0-9\-]+)$``

**Examples**        
<span style="color:silver;">
ecs-service_us-east-1_prod_tomcat
<br>
ecs-service_us-east-1_prod_micro-app
</span>


<br>
> 
<span style="color:silver;">References</span>
- [Cloud Conformity VPCs](https://www.cloudconformity.com/knowledge-base/aws/VPC/vpc-naming-conventions.html#)
- [Cloud Conformity EC2](https://www.cloudconformity.com/knowledge-base/aws/EC2/ec2-instance-naming-conventions.html#)
