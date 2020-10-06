---
layout: post
title: "[AWS] Tagging Resources"
author: "Carlos Requena"
lang: en
ref: aws-tagging-resources
tags:
   -  "[AWS] Tagging Resources" 
---

<div style="text-align:center"><span style="color:red;font-weight: bold">A witty saying proves nothing." </span> <span style="color:black;font-weight: bold">--Voltaire</span></div>
<br>

# [AWS] Tagging Resources
Amazon Web Services allows customers to assign metadata to their AWS resources in the form of tags. Each tag is a simple label consisting of a customer-defined key and an optional value that can make it easier to manage, search for, and filter resources by purpose, owner, environment, or other criteria. AWS tags can be usedfor many purposes.

> <div style="text-align:left"><span style="color:red;font-weight: bold">Important</span></div>
Do not add personally identifiable information (PII) or other confidential or sensitive information in tags. Tags are accessible to many AWS services, including billing. Tags are not intended to be used for private or sensitive data. 

# Best practices
**As you create a tagging strategy for AWS resources, follow best practices:**

-  Do not store personally identifiable information (PII) or other confidential or sensitive information in tags.

-  Use a standardized, case-sensitive format for tags, and apply it consistently across all resource types.

-  Consider tag guidelines that support multiple purposes, like managing resource access control, cost tracking, automation, and organization.

-  Use automated tools to help manage resource tags. [AWS Resource Groups](https://docs.aws.amazon.com/ARG/latest/userguide/) and the [Resource Groups Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/) enable programmatic control of tags, making it easier to automatically manage, search, and filter tags and resources.

-  Use too many tags rather than too few tags.

-  Remember that it is easy to change tags to accommodate changing business requirements, but consider the consequences of future changes. For example, changing access control tags means you must also update the policies that reference those tags and control access to your resources.

## Best Practices for Naming Tags and Resources

### Adopt a Standardized Approach for Tag Names 

Keep in mind that names for AWS tags are case sensitive so ensure that they are used consistently. For example, the tags CostCenter and costcenter are different, so one might be configured as a cost allocation tag for financial analysis and reporting and the other one might not be. Similarly, the **Name** tag appears in the AWS Console for many resources, but the name tag does not. 

A number of tags are predefined by AWS or created automatically by various AWS services. Many AWS-defined tags are named using all lowercase, with hyphens separating words in the name, and prefixes to identify the source service for the tag. 

**For example:**

- **aws:ec2spot:fleet-request-id** identifies the Amazon EC2 Spot Instance Request that launched the instance

- **aws:cloudformation:stack-name** identifies the AWS CloudFormation stack that created the resource

- **lambda-console:blueprint** identifies blueprint used as a template for an AWS Lambda function

- **elasticbeanstalk:environment-name** identifies the application that created the resource

> <div style="text-align:left"><span style="color:red;font-weight: bold">Important</span></div>
Consider naming your tags using all lowercase, with hyphens separating words, and a prefix identifying the organization name or abbreviated name. 

<br>
**For example:**

-  **anycompany:cost-center** to identify the internal Cost Center code

-  **anycompany:environment-type** to identifywhether the environment is development, test, or production

-  **anycompany:application-id** to identify the application the resource was created for

The prefix ensures that tags are clearly identified as having been defined by your organization and not by AWS or athird-party tool that you may be using. Using all lowercase with hyphens for separators avoids confusion about how to capitalize a tag name. For example, anycompany:project-idis simpler to rememberthan ANYCOMPANY:ProjectID, anycompany:projectID, or Anycompany:ProjectId

### Standardize Names for AWS Resources
[[AWS] Naming Conventions Best Practices](https://cjrequena.com/2020-06-05/aws-naming-conventions-en)

# Tagging categories
## Technical Tags
-   **name** – Identify individual resources 

-   **application-id** – Identify resources that are related to a specific application 

-   **application-role** – Describe the function of a particular resource (such as web server, message broker, database)

-   **cluster** – Identify resource farms that share a common configuration and perform a specific function for an application

-   **environment** – Distinguish between development, test, and production resources

-   **version** – Help distinguish between versions of resources or applications

## Tags for Automation

-   **date/time** – Identify the date or time a resource should be started, stopped, deleted, or rotated

-   **opt-in/opt-out** – Indicate whether a resource should be included in an automated activity such as starting, stopping, or resizing instances

-   **security** – Determine requirements, such as encryption or enabling of Amazon VPC flow logs; identify route tables or security groups that need extra scrutiny


## Business Tags

-   **project** – Identify projects that the resource supports

-   **owner** – Identify who is responsible for the resource

-   **cost-center/business-unit** – Identify the cost center or business unit associated with a resource, typically for cost allocation and tracking

-   **customer** – Identify a specific client that a particular group of resources serves


## Security Tags

-   **confidentiality** – An identifier for the specific data confidentiality level a resource supports

-   **compliance** – An identifier for workloads that must adhere to specific compliance requirements



# Tagging Use Cases
## Tags for AWS Console Organization and Resource Groups    
## Tags for Cost Allocation   
## Tags for Automation  
## Tags for Operations Support   
## Tags for Access Control 
## Tags for Security Risk Management   

# Tag naming limits and requirements

