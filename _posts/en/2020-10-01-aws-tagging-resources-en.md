---
layout: post
title: "[AWS] Tagging Resources"
author: "Carlos Requena"
lang: en
ref: aws-tagging-resources
tags:
   -  "[AWS] Amazon Web Services"
---

<div style="text-align:center"><span style="color:red;font-weight: bold">"A witty saying proves nothing." </span> <span style="color:black;font-weight: bold">--Voltaire</span></div>
<br>

# [AWS] Tagging Resources
Amazon Web Services allows customers to assign metadata to their AWS resources in the form of tags. Each tag is a simple label consisting of a customer-defined key and an optional 
value that can make it easier to manage, search for, and filter resources by purpose, owner, environment, or other criteria. AWS tags can be used for many purposes.

> <div style="text-align:left"><span style="color:red;font-weight: bold">Important</span></div>
Do not add personally identifiable information (PII) or other confidential or sensitive information in tags. Tags are accessible to many AWS services, including billing. Tags are 
not intended to be used for private or sensitive data. 

# Best practices
**As you create a tagging strategy for AWS resources, follow best practices:**

-  Do not store personally identifiable information (PII) or other confidential or sensitive information in tags.

-  Use a standardized, case-sensitive format for tags, and apply it consistently across all resource types.

-  Consider tag guidelines that support multiple purposes, like managing resource access control, cost tracking, automation, and organization.

-  Use automated tools to help manage resource tags. [AWS Resource Groups](https://docs.aws.amazon.com/ARG/latest/userguide/) and the [Resource Groups Tagging API](https://docs.aws.amazon.com/resourcegroupstagging/latest/APIReference/) enable programmatic control of tags, making it easier to automatically manage, search, and filter tags and resources.

-  Use too many tags rather than too few tags.

-  Remember that it is easy to change tags to accommodate changing business requirements, but consider the consequences of future changes. For example, changing access control tags means you must also update the policies that reference those tags and control access to your resources.

# Best Practices for Naming Tags

## Adopt a Standardized Approach for Tag Names 

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

## Standardize Names for AWS Resources
[[AWS] Naming Conventions Best Practices](https://cjrequena.com/2020-06-05/aws-naming-conventions-en)

# Tagging categories
## Technical Tags:
1. **name**: Used to give a descriptive name to the resource. For example, "web-server-prod" or "db-dev".
2. **owner**: Specifies the person or team responsible for the resource. This can be an individual's name, team name, or email address.
3. **availability-zone**: Specifies the availability zone or region where the resource is located.
4. **service**: Indicates the AWS service associated with the resource, such as "ec2", "s3", "rds", etc.
5. **lifecycle**: Specifies the lifecycle stage of the resource, such as "active", "inactive", "archived".
6. **version**: Indicates the version of the resource if applicable.
7. **created-date**: Specifies the date when the resource was created.
8. **last-modified-date**: Specifies the date when the resource was last modified.
9. **resource-id**: Unique identifier for the resource.
10. **environment-type**: Specifies the type of environment, such as "test", "qa", or "production".

## Tags for Automation:
1. **automated**: Indicates whether the resource is managed by automation tools. Values can be "yes" or "no".
2. **auto-scaling-group**: Indicates the auto-scaling group associated with the resource.
3. **automation-tool**: Specifies the automation tool used to manage the resource, such as "cloudformation", "terraform", etc.
4. **scheduled-automation**: Indicates whether the resource is part of scheduled automation tasks. Values can be "yes" or "no".
5. **continuous-integration**: Indicates whether the resource is integrated with a continuous integration (CI) system.
6. **automated-backup**: Specifies whether automated backups are enabled for the resource.
7. **auto-shutdown**: Indicates whether the resource has an automated shutdown schedule.
8. **auto-recovery**: Specifies whether the resource is configured for automatic recovery in case of failure.
9. **auto-scaling-policy**: Indicates the auto-scaling policy associated with the resource.
10. **automated-monitoring**: Specifies whether automated monitoring is enabled for the resource.

## Business Tags:
1. **cost-center**: Associates the resource with a specific cost center or project code for financial tracking and allocation.
2. **department**: Indicates the department or business unit that owns or uses the resource. Useful for chargebacks and cost allocation.
3. **project**: Associates the resource with a specific project or initiative within the organization.
4. **business-unit**: Specifies the business unit that the resource belongs to.
5. **customer-id**: Identifies the customer or client associated with the resource.
6. **revenue-center**: Associates the resource with a specific revenue center or profit center.
7. **business-criticality**: Indicates the business criticality of the resource, such as "high", "medium", or "low".
8. **contract-id**: Specifies the contract ID related to the resource.
9. **service-level-agreement**: Indicates the service level agreement (SLA) associated with the resource.
10. **business-impact**: Specifies the potential business impact of the resource being unavailable.

## Security Tags:
1. **security-classification**: Specifies the security classification or sensitivity level of the resource, such as "public", "internal", "confidential".
2. **compliance**: Specifies the compliance status of the resource, such as "hipaa", "pci-dss", "gdpr", etc.
3. **backup**: Indicates whether the resource is included in regular backups or not. Values can be "yes" or "no".
4. **encryption**: Indicates whether the resource data is encrypted at rest and/or in transit.
5. **access-control-list**: Specifies the access control list (ACL) associated with the resource.
6. **security-group**: Indicates the security group associated with the resource.
7. **firewall-rule**: Specifies the firewall rule associated with the resource.
8. **vulnerability**: Indicates the vulnerability status of the resource, such as "vulnerable", "patched", etc.
9. **data-classification**: Specifies the data classification level of the resource, such as "sensitive", "confidential", etc.
10. **security-policy**: Indicates the security policy applied to the resource.

## Tags for Classification:
1. **environment**: Indicates the environment the resource belongs to, such as "production", "development", or "staging".
2. **application**: Identifies the application or project to which the resource belongs. Useful for multi-application environments.
3. **region**: Specifies the AWS region where the resource is located.
4. **zone**: Indicates the availability zone within a region where the resource is located.
5. **role**: Specifies the role or function of the resource within the system architecture.
6. **service-type**: Specifies the type of service provided by the resource, such as "web", "database", etc.
7. **deployment-stage**: Indicates the deployment stage of the resource, such as "alpha", "beta", "release-candidate", etc.
8. **cost-allocation**: Specifies the cost allocation tag for financial tracking purposes.
9. **cost-savings**: Indicates whether the resource is part of a cost-saving initiative. Values can be "yes" or "no".
10. **expiration-date**: Specifies the date when the resource should be reviewed or decommissioned. Useful for managing resource lifecycle.

# Tagging Use Cases
## Tags for AWS Console Organization and Resource Groups

- **organize-resources:** Use tags to group and categorize resources based on their purpose, project, environment, or any other relevant attribute. This makes it easier to navigate and manage your resources within the AWS Management Console.
- **resource-group-classification:** Leverage tags to create resource groups based on specific criteria, such as tagging all production resources together or grouping resources belonging to a specific department or business unit.

## Tags for Cost Allocation

- **cost-tracking:** Assign tags to AWS resources to identify the associated costs and allocate them to specific cost centers, departments, projects, or customers. This enables you to track and analyze your AWS spending accurately.
- **cost-allocation-reports:** Utilize tags to filter and generate detailed cost allocation reports using AWS Cost Explorer or Cost and Usage Reports. These reports can provide insights into cost breakdowns based on different tag values.

## Tags for Automation

- **automated-resource-management:** Use tags to trigger automation scripts, workflows, or AWS Lambda functions for managing resources automatically. For example, you can use tags to automatically start or stop EC2 instances based on predefined schedules or resource utilization.
- **automated-backup-and-recovery:** Tagging resources can be useful in automated backup and recovery scenarios. By assigning specific tags to resources, you can create backup policies or automation scripts that target resources with those tags for backup or recovery operations.

## Tags for Operations Support

- **resource-monitoring-and-alerts:** Assign tags to resources to facilitate monitoring and alerting systems. Tags can be used to set up monitoring dashboards, define alarm rules, or enable automated actions based on resource state changes or performance metrics.
- **incident-response-and-troubleshooting:** Tags can assist in incident response and troubleshooting by providing additional context or categorization for resources. During an incident, tags can help identify affected resources, their interdependencies, or associated configurations.

## Tags for Access Control

- **access-control-policies:** Use tags to define fine-grained access control policies through AWS Identity and Access Management (IAM). Tags can be used as condition keys in IAM policies, allowing you to grant or restrict access to resources based on their tag values.
- **resource-sharing-and-cross-account-access:** Leverage tags to control resource sharing and enable cross-account access. You can use tags to specify which resources are accessible to other AWS accounts or specific IAM roles based on tag-based permissions.

## Tags for Security Risk Management

- **security-group-and-nacl-rules:** Assign tags to security groups and network ACLs (NACLs) to manage and audit security policies effectively. Tags can help categorize or identify the purpose of security rules, allowing you to track and analyze security configurations and potential risks.
- **asset-inventory-and-vulnerability-management:** Utilize tags to maintain an inventory of your AWS assets and support vulnerability management practices. Tags can help identify resources that require regular security assessments, vulnerability scanning, or patch management.

