# AWS-Route53-Report
Create a full report of all hosted zones and record sets in your AWS Organisation scanning all accounts.

**Table of contents**
- [AWS-Route53-Report](#aws-route53-report)
- [Intro](#intro)
- [What it does](#what-it-does)
- [Deployment](#deployment)
- [License](#license)

# Intro
This project builds all you need in order to get a report of all your Route53 hosted zones and record sets saved to S3 and delivered to your mailbox.

# What it does
In this repo you can find 2 templates: 
* `route53-query-basic-iam-grants`: this template creates an IAM role with the grants needed to query Route53
* `route53-query`: This template creates multiple resources:
  * a S3 bucket to host all your reports
  * 3 lifecycle rules for your S3 bucket:
    * Expire and delete objects after 5 years
    * Transition to Glacier after 90 days
    * Delete Incomplete Multipart Uploads after 7 days
  * a lambda function to query Route53 in all accounts of your organisation (it assumes the role created by the `route53-query-basic-iam-grants` template)
  * a SNS topic to send a notification email
  * an EventBridge rule to schedule the lambda execution
  * an IAM role with the minimum permission needed by the lambda funtion to do its job

# Deployment
Connect to the account you use to deploy StackSets and:
* deploy the `route53-query-basic-iam-grants` template:
  * as a Stack in the account itself
  * as a StackSet across all members of your organisation (you may want to have Automatic Deployment Activated) 
* deploy the `route53-query` template on the account used for management purposes

The lambda function will be executed as per schedule, the report will be saved in S3, and you'll get an email with a link, valid for 12 hours to wonload the report.

# License
See licence.