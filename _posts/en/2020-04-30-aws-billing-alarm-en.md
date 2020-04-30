---
layout: post
title: "[AWS] How to create a billing alarm"
author: "Carlos Requena"
lang: en
ref: aws-billing-alarm
tags:
   -  "[AWS] Amazon Web Services" 
---
<span style="color:red">**“To know that we know what we know, and to know that we do not know what we do not know, that is true knowledge.”**</span>
<span style="color:black;font-weight: bold">--Nicolaus Copernicus</span>

# How to create an AWS billing alarm

1.  Set the billing preference, check and enable the option to receive billing alerts.
2.  Go to Cloud Watch console.
3.  Change the region, make sure your AWS region is settled to US EAST (N. Virginia).
4.  Create an alarm, click on Alarms on the left side of the panel and in the dashboard that pops up click on the orange button that says **"Create alarm"**.
5.  Select a metric, in the panel that pops up, click the button that says **"Select metric"**. This will pop up a window that allows you to choose which
type of service you would like to use. **Select Billing**
6.  Select the service, in the next window you can choose the total estimated charged on your account.
7.  Select the period of time that the alarm will be activated.
8.  Set the threshold.
9.  Create a notification using SNS, you just click on **Create new topic** give it a name and enter the email where you want to receive the notification.
10. Add a description.
11. Click on Preview and create
