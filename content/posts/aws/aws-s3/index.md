---
title: "AWS S3"
date: "2021-02-17"
menu:
  sidebar:
    name: AWS S3
    identifier: aws-s3
    parent: aws
    weight: 10
---

# AWS S3

## Overview
- Allows users to store object in buckets(directories)
- Name should be unique globally
- buckets are define regionally
- Naming conventions
    - No uppercase, underscore, 3-63 long, not an IP, must start with lowercase or number
    
## S3 objects
- Stored in s3://bucket-name/prefix/objectname
- prefix can be series of folders
- Max size of object 5TB
- Each object can have tags, version ID(if enabled)


## Creating a bucket and adding objects
- Management console -> Amazon S3
- Create bucket
- Click on add objects and objects (folders can be created as well)
- Note that object can't be viewed using the link to the path provided as the public access from bucket level and object level would have been blocked, changing the permissions will help us to see.
- But without changing permissions as well, we can view by object action-> open

## S3 bucket versioning
- Maintains different version of same file
- Need to be enabled at bucket level(else each file will have null as the version)
- if same file is uploaded again, a new version will be created
- This helps in protecting against unintended deletion and also rolling back to any version
- Even if delete an object, the object is not actually deleted, but a delete marker will be created, which can be viewed in List versions button
- But we can delete specific version, delete marker, which will be a _permanent delete_

## S3 encryptions
- Methods to encrypt
    - SSE-S3: encrypts using keys handeled and managed by AWS (AES-256). Header "x-amz-server-side-encryption":"AES256"
    - SSE-KMS: use AWS KMS to manage the encryption keys. KMS provides advantages like user control and audit trail. Header "x-amz-server-side-encryption":"aws:kms"
    - SSE-C:managing encryption by our own keys. Here S3 does not store the key provided. Encryption key must be provided for every request sent
    - Client side encryption. Client libraries such as Amazon S3 encryption client must be used. client has to encrypt before sending and has to decrypt before retrieving from S3

## Encrypting objects in S3
- Encryption has to be enabled in S3 first
- Encryption method can be set at bucket level and different method can also be used at object level
- In bucket properties-> default encryption-> enable versioning-> select the encryption method
- Once a method is set at bucket level, we can again override at object level

## S3 security
- User based- Using IAM policies, which tells which user can make which API calls
- Resource based- Bucket policies(json based), object ACL, bucket ACL

## Using bucket policy
- Bucket-> permissions-> bucket policy
- We can write on our own or use policy genertor provided by AWS itself
- We can allow a resource or deny access of a resource
- action for which condition has to be placed is to be selected under actions
- Using ARN we can identify bucket for which policy has to be placed
- Using add conditions, we can add one or more conditions for the policy and then we can generate the whole policy

## Static website hosting in S3
- We can host a static website at `<bucket-name>.s3-website-/.<aws-region>.amazonaws.com`
- Bucket policy should be updated appropriately to allow access to the objects and public access should be given, else we see 403 error.

## S3-CORS
- If in our bucket if we are accessing something outside the bucket, for e.g in another bucket, we need to enable CORS so that the request knows it has to check if it's accessible
- We can alow that under bucket settings by allowing actions on the bucket from list of origins


## S3 MFA-Delete
- We can setup multi-factor authentication for deleting object versions in a S3 bucket, this can be setup by bucket owner through SDK, CLI, APIs
- Using CLI:
    - `aws configure --profile <prof-name>`, credentials can be found from management console
    - `aws s3api put-bucket-versioning --bucket chiras-mfa-test --versioning-configuration Status=Enabled,MFADelete=Enabled --mfa "arn:aws:iam::929050680739:mfa/chiras@cisco.com 177459" --profile "chiras-aws-account"` to enable MFA on the bucket
- Versioning has to enabled for this


## S3 access logs
- We can set access logs of a bucket - any get, put, delete actions on that bucket can be monitored
- Another bucket can be created where all the monitored logs will be stored. Note: Using same monitored bucket for saving logs will increase the bucket size rapidly as it creates a loop of logs
- In bucket properties-> enable server logging-> choose the bucket to be monitored-> preferrably create a logs folder
- Once the bucket being monitored is accessed, in sometime we should be able see the logs in the bucket that's storing it

## S3 replication
- Same region and cross region replication is possible in S3
- The bucket need not necessarily belong to same account
- Versioning has to enabled for this
- Additional things like fast replication(within 15 mins), notification, etc can also be set
![](https://i.imgur.com/5sonZg5.png)
- Replication can be set in the bucket details-> management

## S3 pre-signed URL
- Useful when we want to share a content with only people, and only for limited amount of time
-  `aws s3 presign "s3://chiras-static-website/object-name" --region ap-south-1 --expires-in time-in-seconds`
-  Above CLI command generates the pre-signed URL that can be shared with only necessary members

## S3 storage classes
- S3 offers different storage classes which differs in availability, cost, and time for access
- S3 standard, s3 intellingent tiering, s3 standard-IA, s3 one zone-IA, s3 glacier, s3 glacier deep archive
![](https://i.imgur.com/WdgqUpe.png)


![](https://i.imgur.com/dD53NIT.png)

## S3 storage lifecycle
- Since there are multiple storage classes, each having it's own advantage and also keeping cost in mind, we can set lifecycles for our objects so that we move our objects to different classes based on need. For, e.g for the first 1 month if we want high availability of object we can keep in S3 standard and then if we know we can wait for upto 5 hours for retireval, we can move the object to S3 Glacier
- S3 object lifecycle can be set under bucket management section-> create lifefcycle and rules can be set for previous, current versions, delete markers and so on

     
## More on storage classes
![](https://i.imgur.com/qNJOisR.png)


![](https://i.imgur.com/DE38CvB.png)

## S3 performance
- S3 offers high performance, with unlimited number of prefixes in a bucket
- Around 3500 PUT, POST, COPY, DELETE requests and around 5500 GET, HEAD requests can be made to a prefix per second
- If KMS encryption is used on the objects, it can limit the performance
- Multi-part uploads is reocmmended for files above 100MB, and is must if it's> 5GB, this will help speed up the upload process by uploading parallely
- Transfer accelaration helps by transferring file to edge location and then to target region
- Edge location to target region is fast, it uses private AWS network
- S3 Byte-range fetches helps download to be faster by getting specific byteranges of object in parallel

## S3 select and glacier select
- By performing filtering on the server side, we can retrieve only objects that are necessary for us- ensure less network traffic, lower costs, fast retrieval
- e.g retrieving only mp3 files from the bucket

## S3 notifications
- We can set notifications for different operations on the bucket, to SNS, SQS, Lambda function
- For e.g, we can create a Sqs, set policy to send message to it by anyone and then add notifications under bucket
- We can see the messages in the SQS when appropriate actions are done on the bucket

## S3 Athena
- Its a service that helps us to analyse operations on our bucket using SQL, with this we can analyse how many put requests were called, how many requests were denied and so on
- Go to Athena from management console-> choose where you want to write your results of queries(a bucket)
- Create a  database-> and a table inside it with columns as required and set the location as the table where your logs of bucket that's monitored are present
- Now run queries on the table and extract details

## Pricing
- As part of the AWS Free Tier, you can get started with Amazon S3 for free. Upon sign-up, new AWS customers receive 5GB of Amazon S3 storage in the S3 Standard storage class; 20,000 GET Requests; 2,000 PUT, COPY, POST, or LIST Requests; and 15GB of Data Transfer Out each month for one year.

![](https://i.imgur.com/Wneee6x.png)
