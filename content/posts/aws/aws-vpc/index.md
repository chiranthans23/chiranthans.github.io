---
title: "AWS VPC"
date: "2021-02-03"
menu:
  sidebar:
    name: AWS VPC Fundamentals
    identifier: aws-vpc
    parent: aws
    weight: 40
---


# AWS VPC fundamentals


## Overview
- VPC, Virutal private cloud is a private network to deploy our resources
- Through subnets we can partition our network inside a VPC
- public subnet is one which is accessible from the internet and private subnet is not accessible
- Route tables are used to define access from internet and between subnets

## Architecture of a VPC
![](https://i.imgur.com/YVKEFIb.png)

- VPC is at region level - having multiple AZs
- An AZ has public and private subnets

## More on VPC
- Internet gateway helps VPC instances to connect to the internet
- Public subnet through internet gateway connects to internet

![](https://i.imgur.com/tqJXMIQ.png)

- Public subnet has route to IGW, private subnet has route to NAT gateway and instance

## Network ACL
- Acts as a firewall controlling traffic from and to subnet
- It can have allow or deny rules
- Attached at subnet level
- Rules can have only IP addresses

## Security groups
- Firewall controlling traffic to and from ENI or EC2 instance
- It can only have allow rules
- Rules can have IP addressed or other security groups

![](https://i.imgur.com/3wxaQ5e.png)

## VPC flow logs
- Captures information from VPC flow logs, subnet flow logs, ENI flow logs
- Helps to monitor and trouble shoot network issues
- Captures network information from managed services like RDS, Aurora and so on too
- Can store the logs to an S3 of be sent to cloudwatch logs

## VPC peering
- Connection two VPCs through AWS network
- For this IP address range on two VPCs should not be overlapping
- VPC connection is not transitive, it'll only between two VPCs

## VPC endpoints
- Allows VPC to connect to AWS services using private network instead of public network
- They have advantage of low latency and secure connection
- VPC endpoint gateway for DynamoDB and S3
- VPC endpoint interface for other services
![](https://i.imgur.com/jR6Ao9g.png)

## Site to site VPN and Direct connection
- We can connect our on-prem VPN to AWS using public encrypted network - Site to site VPN. This can be setup quickly
- We can also physically connect them through private, secured network - Direct connection. This can take over a month to establish
- ![](https://i.imgur.com/9mciQaT.png)
