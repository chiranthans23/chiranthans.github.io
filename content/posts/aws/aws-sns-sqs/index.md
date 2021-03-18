---
title: "AWS SNS and SQS"
date: "2021-03-04"
menu:
  sidebar:
    name: AWS SNS and SQS
    identifier: aws-sns-sqs
    parent: aws
    weight: 40
---

# AWS SNS and SQS
## Overview
- AWS service that helps in decoupling the applications and still communicate
- Unlimited throughput, message stays for 4-14 days in the queue
- Has a producer and a consumer

## SQS with ASG
- Can setup CloudWatch alarm to check on the number of messages in the queue and based on that scale up or down, the number of consumers

## Create a Queue
- Management Console-> SQS-> Create Queue
- We can add messages and when polled, messages will appear in the queue
- Deleting the message signals that it's been read

## Message visibility timeout
- Time given for a consumer to process a message. While it's being processed, the message won't be visible in the queue

## Dead letter queue
- If the time taken to process a message crosses the max time(visibility timeout), and if it happens multiple times(the number can be set), we can tell the SQS to move it to the Dead letter queue. This will help in debugging the message later.
- Create another queue-> Go back to the previous queueu and, enable DLQ-> provide the queue for DLQ name

## Message delay
- Queues also provide the option to set the delay time for messages- how long the messages have to be held within queue, before the consumers are able to read them
- Can be overwridden at message level 

## Long Polling
- When there are no messages in the queue, the consumers can be made to wait for a set time, instead of polling repeatedly. This improves efficiency of the application
- This can be set at the queue level, using `WaitTimeSeconds`

## AWS extended client
- It is helpful if we want to send large messages, >256kb
- We can use AWS extended client for this
- We can send the larger message to S3 bucket, while only sending small message(metadata) to the SQS, similarly, using metadata consumer fetches the larget message from the S3

## AWS SQS FIFO
- As the name says, it processes the message in FIFO order
- The consumer recieves messages in the same order as sent by the producer

## SQS DeDuplication
- With this in a **5 mins interval**, we can make sure, same message doesn't come to the queue multiple times
- 5 mins is the deduplication time
- Deduplication check is done in two ways
    - SHA of the body of the message is checked
    - Deduplication ID is checked

## SQS Message grouping
- Only one consumer can be placed for one groupID
- Using GroupID, we can consolidate messages into different groups
- In this way, multiple consumers can consume messages parallely
- FIFO is not maintained among groups

<hr/>


## SNS Overview
- Amazon SNS is similar to SQS, but in this case, there can be more than one recievers
- By using Topics, many subscribers can receive same message
- AWS support 10^5 topics and 10^5 subscribers
- Subscibers can be SQS, HTTPS, Lambda, etc
- Supports Security similar to SQS, KMS for at-rest encryption and HTTPS for in-flight

## Create a topic
- SNS-> create topic-> create subcription


## Fan-out
- A SNS topic can send message to multiple SQS queues
- With this applications can be decoupled and additionally, the messages can be delayed, persisted and etc, by the features of SQS
- A noteworthy point is that, currently, SNS topic cannot send messages to FIFO SQS queue



## Lambda as a subscriber
- Create a lambda that can be called async
- make it as a subscriber to a sns topic, for e.g
- when a message is sent to sns topic, it should be seen in cloudwatch logs
- lambda can be called by invoking from cli too
- for e.g
```shell=
aws lambda invoke --function-name chiras-sns-handling-lambda --cli-binary-format raw-in-base64-out --payload '   {
        "Message": " chiras example message"
    }' --invocation-type Event --region ap-south-1 response.json
```