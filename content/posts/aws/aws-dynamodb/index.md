---
title: "AWS DynamoDB"
date: "2021-03-12"
menu:
  sidebar:
    name: AWS DynamoDB
    identifier: aws-dynamodb
    parent: aws
    weight: 10
---
# AWS DynamoDB

## Overview
- Fully managed, highly available and has 3 AZ
- Integrated with IAM 
- Max size of an item is 400KB

## Primary key
1. Partition key
2. Partition key+ Sort key

## RCU and WCU throughput
- RCU- Read capacity units- throughput for reads
- WCU- Write capacity units- throughput for writes
- Option to setup auto-scaling to meet the demands
- Usig _burst credit_ throughput can be exceeded
- If _burst credit_ is empty, ProvisionedThroughputException is thrown
- Strongly consistent read:
    - When a read operation is performed after a write, updated item is returned
- Eventually consistent read:
    - When a read operation is perfomed after write, it's possible to get old data, because of replication
- By default DDB uses eventually consistent read. But, GetItem, Query and Scan provide option to set ConsistentRead to true
- WCU:
    - One WCU represents 1  write per second for an item up to 1KB size
    
- RCU:
    - One RCU represents 1 strongly consisted read per second or two eventually consisted read per second for an item up to size 4KB

## DynamoDB basic APIs
- Write data
    - PutItem- Writes to DynamoDB or replaces fully
    - UpdateItem- partial update of attributes
    - ConditionalWrites- Accept a write only if conditions are met. Helps with concurrent access to items
- Delete data
    -  DeleteItem- Delete an individual row, can also delete based on condition
    - DeleteTable- Deltes a whole table. Quicker than DeleteItem for all items
- BatchingWriteItem
    - Upto 25 PutItem/DeleteItem in one call
    - Upto 16MB of data writeen/ 400KB of data per item
    - Saves latency by reducing the number of calls
    - Operations done in parallel
    - Possibility of part of batch items operation to fail, in which case, we've to retry
- Reading data
    - GetItem- Reading based on primary key. By default uses `eventually consistent read`, but `strongly consistent read` can be used too. `ProjectExpression` can be specified to include only certain attributes
    - BatchGetItem- Upto 100 items can be read, upto 16MB of data, read in parallel to reduce latency

- Query
    - Based on partition key
    - Sort key can be used too
    - FilterExpression can be done too, at the client side
    - Upto 1MB of data can be returned or the number of items, as specified
    - Pagination can be done

## GSI and LSI in DynamoDB
- LSI
    - Alternate ranging key(sort key) for the table
    - We can have upto 5 LSIs per table
    - The attribute can be String, Number or Boolean
    - Must be defined at the time of creation

- GSI
    - Helps in speeding up queries on non-key attributes
    - GSI= paritionkey+optional sortkey
    - Index is a new table, we can project attributes on it
        - Partition and sort keys of original table are always projected(KEYS_ONLY)
        - Specific attributes projected(INCLUDE)
        - All attributes projected(ALL)
    - We must define RCU and WCU for the index
    - We can modify GSI, but not LSI
    
- Throttling
    - GSI
        - The main table will be affected if the GSI write is throttled
        - WCU and partition key for GSI should be choosed wisely
        - Having WCU of GSI greater than that of Main table helps solve the problem
        
    - LSI
        - Since this is created at the time of table creation, we can't do much about it
        - RCU and WCU of the LSI remains same as the table for which it's created

- DyanamoDB concurrency
    - DynamoDB has conditional update/delete feature, with which we can check if the item that has to be updated passes the given conditions, i.e the item hasn't changed
    - With which DynamoDB is optimistic locking or concurrency database
    
- DynamoDB DAX
    - Cache for DynamoDB
    - Write operation goes through DAX and then to the DynamoDB
    - For read operation there is only micro second latency if DAX is used
    - 5 mins TTL is default setting for cache, but, can be changed
    - DAX cluster supports upto 10 nodes and multi AZ as well
    - Supports encryption

- DynamoDB Streams
    - Using this, we can track the changes in our DynamoDB
    - Can be read by AWS Lambda, EC2 instances to do different operations
    - But, we have the option to choose what goes into the database
        - KEYS_ONLY- Only key attributes of the modified item
        - NEW_IMAGE- Entire item, after it was modified
        - OLD_IMAGE- New item, after modification
        - NEW_AND_OLD_IMAGE- Both the items(expensive but helpful)
    - Data only stays for 24 hours in the Streams
    - Records are not retroactively populated- only after the stream is enabled, it's populated
    - Streams and Lamda
        - Define event-source mappings
        - Have appropriate permissions for the Lambda function
        - The function is invoked synchronously
        - For this, enable the streams from the table, enable trigger from the triggers section in the table itself and create a Lambda function for reading the data from the streams. Make sure _LambdaDynamoDBExecutionRole_ policy is added to the role
        

- DynamoDB TTL
    - Deleting an item from DynamoDB after a give time
    - By default it's disabled
    - A column is used, based on it's value, expiration condition is evaluated
    - Due to TTL, items in the GSI and LSI also gets deleted
    - But we can use Streams to recover expired items, if needed
    - The expired item stays for upto 48 hours in the DB

- DynamoDB CLI
    - Projection
        ```shell=
        aws dynamodb scan --table-name chiras_dynamo_learning \
        --projection-expression "id,priority" \
        --region ap-south-1
        ```
    - Filter
         ```shell=
        aws dynamodb scan --table-name chiras_dynamo_learning \
        --projection-expression "id,priority" \
          --filter-expression "id = :u" --expression-attribute-values '{ ":u": {"N":"1"}}' \
        --region ap-south-1
        ```
    - Page size
        -  ```shell=
             aws dynamodb scan --table-name chiras_dynamo_learning \
             --region ap-south-1
             ```
             Does one API call
        -  ```shell=
             aws dynamodb scan --table-name chiras_dynamo_learning \
             --region ap-south-1 --page-size 1
             ```
             One API call per item
        -  ```shell=
             aws dynamodb scan --table-name chiras_dynamo_learning \
             --region ap-south-1 --max-items 1
             ```
        -  ```shell=
             aws dynamodb scan --table-name chiras_dynamo_learning \
             --region ap-south-1 --max-items 1 --starting-token eyJFeGNsdXNpdmVTdGFydEtleSI6IG51bGwsICJib3RvX3RydW5jYXRlX2Ftb3VudCI6IDF9
             ```
- Copying table options
    - Through code
    - Using AWS DataPipeline
    - Backup and restore to new table
    
- DDB Security
    - Uses IAM roles to control access and use VPC for availablity
    - KMS, SSL/TLS security for rest and in-flight respectively