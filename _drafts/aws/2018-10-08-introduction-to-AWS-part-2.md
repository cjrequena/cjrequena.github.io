---
layout: post
title: "[AWS] An Introduction to Amazon Web Services Part 2"
author: "Carlos Requena"
tags:
   - Amazon Web Services, AWS, Cloud Computing
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

You can get started by downloading the AWS Well-Architected Framework whitepaper PDF (https://d1.awsstatic.com/whitepapers/architecture/AWS_Well-Architected_Framework.pdf)

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

  - [Amazon Elastic Compute Cloud (Amazon EC2)](https://aws.amazon.com/ec2/)
  - [AWS Lambda](https://aws.amazon.com/lambda/)
  - [Auto Scaling](https://aws.amazon.com/autoscaling/)
  - [Elastic Load Balancing](https://aws.amazon.com/elasticloadbalancing/)
  - [AWS Elastic Beanstalk](https://aws.amazon.com/elasticbeanstalk/)
  - [Amazon Virtual Private Cloud (Amazon VPC)](https://aws.amazon.com/vpc/)
  - [AWS Direct Connect](https://aws.amazon.com/directconnect/)
  - Amazon Route 53
  - Amazon Simple Storage Service (Amazon S3)
  - Amazon Glacier
  - Amazon Elastic Block Store (Amazon EBS)
  - AWS Storage Gateway
  - Amazon CloudFront
  - Amazon Relational Database Service (Amazon RDS)
  - Amazon DynamoDB
  - Amazon Redshift
  - Amazon ElastiCache
  - Management Tools
  - Amazon CloudWatch
  - AWS CloudFormation
  - AWS CloudTrail
  - AWS Config
  - Security and Identity
  - AWS Identity and Access Management (IAM)
  - AWS Key Management Service (KMS)
  - AWS Directory Service
  - AWS Certificate Manager
  - AWS Web Application Firewall (WAF)
  - Amazon API Gateway
  - Amazon Elastic Transcoder
  - Amazon Simple Notification Service (Amazon SNS)
  - Amazon Simple Email Service (Amazon SES)
  - Amazon Simple Workflow Service (Amazon SWF)
  - Amazon Simple Queue Service (Amazon SQS)
 