---
layout: post
title: "[AWS] An Introduction to Amazon Web Services Part 2"
author: "Carlos Requena"
tags:
   - Amazon Web Services (AWS)
---

# AWS Fundamentals

Amazon Web Services (AWS) is a secure cloud services platform, AWS provides on-demand delivery of IT resources via the 
Internet on a secure cloud services platform, offering compute power, storage, databases, content delivery, and other 
functionality to help businesses scale and grow.

AWS provides the key advantages of cloud computing: Capacity exactly matches your need, you pay only for what you use, 
economies of scale result in lower costs, and the service is provided by a vendor experienced in running 
large-scale networks.

The AWS Cloud provides a broad set of infrastructure services, such as computing power, storage options, networking and 
databases, delivered as a utility: on-demand, available in seconds, with pay-as-you-go pricing. From data warehousing to 
deployment tools, directories to content delivery, over 50 services are available in just a few mouse clicks with AWS. 

New services are quick to provision, without upfront capital expense, allowing enterprises, start-ups, SMBs and customers 
in the public sector to access the building blocks they need to respond quickly to changing business requirements.


# AWS Global Infrastructure

AWS serves over one million active customers in more than 190 countries, and it continues to expand its global 
infrastructure steadily to help organizations achieve lower latency and higher throughput for their business needs.

AWS provides a highly available technology infrastructure platform with multiple locations worldwide. These locations 
are composed of regions and Availability Zones. Each region is a separate geographic area. Each region has multiple, 
isolated locations known as Availability Zones. 

AWS enables the placement of resources and data in multiple locations. Resources aren’t replicated across regions unless
organizations choose to do so.

Each region is completely independent and is designed to be completely isolated from the other regions. This achieves the
greatest possible fault tolerance and stability. 

Each Availability Zone is also isolated, but the Availability Zones in a region are connected through low-latency links. 

Availability Zones are physically separated within a typical metropolitan region and are located in lower-risk flood 
plains (specific flood zone categorization varies by region). 

Availability Zones are all redundantly connected to multiple tier-1 transit providers. By placing resources in separate 
Availability Zones, you can protect your website or application from a service disruption impacting a single location.

# AWS Well-Architected Framework

The Well-Architected framework has been developed to help cloud architects build the most secure, high-performing, 
resilient, and efficient infrastructure possible for their applications. This framework provides a consistent approach 
for customers and partners to evaluate architectures, and provides guidance to help implement designs that will scale 
with your application needs over time.

AWS Well-Architected Framework Principles

	1. Stop guessing capacity needs
	2. Test systems at production scale
	3. Automate to ease architectural experimentation
	4. Allow for evolutionary architectures
	5. Drive your architecture using data
	6. Improve through Game Days

The AWS Well-Architected framework includes strategies to help you to obtain guidance to produce stable and efficient 
systems so you can focus on functional requirements.

You can get started by downloading the AWS Well-Architected Framework whitepaper [PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)

## AWS Well-Architected Framework Pillars

### Operational Excellence

The operational excellence pillar focuses on running and monitoring systems to deliver business value, and continually 
improving processes and procedures. Key topics include managing and automating changes, responding to events, and 
defining standards to successfully manage daily operations.

Download The Operational Excellence whitepaper 
[PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS-Operational-Excellence-Pillar.pdf) |
[Kindle](https://www.amazon.com/Operational-Excellence-Pillar-Well-Architected-Whitepaper-ebook/dp/B077NTC6S5/ref=sr_1_1?s=digital-text&ie=UTF8&qid=1511827693&sr=1-1&keywords=operational+excellence+well-architected)

### Security and Compliance

The security pillar focuses on protecting information & systems. Key topics include confidentiality and integrity of data, 
identifying and managing who can do what with privilege management, protecting systems, and establishing controls to 
detect security events.

Download The Security Pillar whitepaper 
[PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS-Security-Pillar.pdf) | 
[Kindle](https://www.amazon.com/Security-Pillar-Well-Architected-Framework-Whitepaper-ebook/dp/B01MXRQFUX/ref=sr_1_4?s=digital-text&ie=UTF8&qid=1503806675&sr=1-4&keywords=aws+well-architected)
	
### Reliability

The reliability pillar focuses on the ability to prevent, and quickly recover from failures to meet business and customer
demand. Key topics include foundational elements around setup, cross project requirements, recovery planning, and how we 
handle change.

Download the Reliability Pillar whitepaper 
[PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS-Reliability-Pillar.pdf) | 
[Kindle](https://www.amazon.com/Reliability-Pillar-Well-Architected-Framework-Whitepaper-ebook/dp/B01MRQXRW4/ref=sr_1_3?s=digital-text&ie=UTF8&qid=1503806675&sr=1-3&keywords=aws+well-architected)

### Performance Eficiency

The performance efficiency pillar focuses on using IT and computing resources efficiently. Key topics include selecting 
the right resource types and sizes based on workload requirements, monitoring performance, and making informed decisions
to maintain efficiency as business needs evolve.

Download the Performance Efficiency whitepaper 
[PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS-Performance-Efficiency-Pillar.pdf) | 
[Kindle](https://www.amazon.com/Performance-Efficiency-Pillar-Well-Architected-Whitepaper-ebook/dp/B01MSSLHBX/ref=sr_1_2?s=digital-text&ie=UTF8&qid=1503806675&sr=1-2&keywords=aws+well-architected)

### Cost Optimization

Cost Optimization focuses on avoiding un-needed costs. Key topics include understanding and controlling where money is 
being spent, selecting the most appropriate and right number of resource types, analyzing spend over time, and scaling 
to meet business needs without overspending.

Download the Cost Optimization whitepaper 
[PDF](https://d1.awsstatic.com/whitepapers/architecture/AWS-Cost-Optimization-Pillar.pdf) | 
[Kindle](https://www.amazon.com/Cost-Optimization-Pillar-Well-Architected-Whitepaper-ebook/dp/B01LW7KXRG/ref=sr_1_5?s=digital-text&ie=UTF8&qid=1503806675&sr=1-5&keywords=aws+well-architected)

# Compute and Networking Services

AWS provides a variety of compute and networking services to deliver core functionality for businesses to develop and 
run their workloads. 

These compute and networking services can be leveraged with the storage, database, and application services to provide a 
complete solution for computing, query processing, and storage across a wide range of applications.

  - [Amazon Simple Storage Service (Amazon S3)](https://aws.amazon.com/s3/)
  - [Amazon Glacier](https://aws.amazon.com/glacier/)
  - [Amazon Elastic Compute Cloud (Amazon EC2)](https://aws.amazon.com/ec2/)
  - [Amazon Elastic Block Store (Amazon EBS)](https://aws.amazon.com/ebs/)
  - [AWS Lambda](https://aws.amazon.com/lambda/)
  - [Auto Scaling](https://aws.amazon.com/autoscaling/)
  - [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)
  - [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)
  - [Amazon Virtual Private Cloud (Amazon VPC)](https://aws.amazon.com/vpc/)
  - [AWS Direct Connect](https://aws.amazon.com/directconnect/)
  - [Amazon Route 53](https://aws.amazon.com/route53/)
  - [AWS Storage Gateway](https://aws.amazon.com/storagegateway/)
  - [Amazon CloudFront](https://aws.amazon.com/cloudfront/)
  - [Amazon Relational Database Service (Amazon RDS)](https://aws.amazon.com/rds/)
  - [Amazon DynamoDB](https://aws.amazon.com/dynamodb/)
  - [Amazon Redshift](https://aws.amazon.com/redshift/)
  - [Amazon ElastiCache](https://aws.amazon.com/elasticache/)
  - [Management Tools](https://aws.amazon.com/products/management-tools/)
  - [Amazon CloudWatch](https://aws.amazon.com/cloudwatch/)
  - [AWS CloudFormation](https://aws.amazon.com/cloudformation/)
  - [AWS CloudTrail](https://aws.amazon.com/cloudtrail/)
  - [AWS Config](https://aws.amazon.com/config/)
  - [Security and Identity](https://aws.amazon.com/security/)
  - [AWS Identity and Access Management (IAM)](https://aws.amazon.com/iam/)
  - [AWS Key Management Service (KMS)](https://aws.amazon.com/kms/)
  - [AWS Directory Service](https://aws.amazon.com/directoryservice/)
  - [AWS Certificate Manager](https://aws.amazon.com/certificate-manager/)
  - [AWS Web Application Firewall (WAF)](https://aws.amazon.com/waf/)
  - [Amazon API Gateway](https://aws.amazon.com/api-gateway/)
  - [Amazon Elastic Transcoder](https://aws.amazon.com/elastictranscoder/)
  - [Amazon Simple Notification Service (Amazon SNS)](https://aws.amazon.com/sns/)
  - [Amazon Simple Email Service (Amazon SES)](https://aws.amazon.com/ses/)
  - [Amazon Simple Workflow Service (Amazon SWF)](https://aws.amazon.com/swf/)
  - [Amazon Simple Queue Service (Amazon SQS)](https://aws.amazon.com/sqs/)
 