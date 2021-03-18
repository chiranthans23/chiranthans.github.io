---
title: "AWS Route53"
date: "2021-02-17"
menu:
  sidebar:
    name: AWS Route53
    identifier: aws-route53
    parent: aws
    weight: 10
---
# AWS Route53

## Overview

- Service in AWS that manages Domain name system(DNS)
- A domain costs around _12 dollars_ per year
- Amazon Route 53 effectively connects user requests to infrastructure running in AWS – such as Amazon EC2 instances, Elastic Load Balancing load balancers, or Amazon S3 buckets – and can also be used to route users to infrastructure outside of AWS

## Setting up a domain name
:::info
Route 53 is a global service. No need to select regions
:::
- Management console - > Route 53
- Register domain -> enter domain name and check availability -> details -> create
- Takes about an hour
- **Active for only one year**

## Route53 for EC2
- Create EC2 instance(s) using management console
- Once it is running
- Go to Load balancer, select AZs, create

## Route53 - TTL
- High TTL is used when there is less traffic and low TTL means many requests
- Can be set in Route53 section in management console.
- Create a record set -> give the public IP -> set the time
- To test the time it's cached , use _dig_ tool

## CNAME and Alias
- Sets up alias for other IPs
- Both can be created under Route53 section
- CNAME can setup alias for only non-root domain e.g, myapp.domainname.com, it cannot be for domainname.com
- Alias is similar to CNAME but it can also be alias for root domain, e.g domainname.com
- To setup go to management console-> route 53-> create a record-> choose cname/ alias-> give the hostname

## Simple route policy
- A route can be created to map one or more IP addresses
- In that case browser itself will choose one among them

## Weighted route policy
- When more than one IP is mapped to a route, we can set weights for browser to choose an IP
- For this create records based on the number of IP to be mapped. Give same name for all the records
- Choose weighted as the policy and assign weights

## Latency routing policy
- Routing can also be done based on the latency for making the call from a particular region
- Create records based on the number of IP to be mapped. Give same name for all the records, give IPs belonging to different regions
- Make a call to the domain, and the result should come from nearest region

## Health check
- Healthy if 3 calls to the IP passes
- Unhealthy if 3 calls to the IP fails
- But it can be changed
- Default health check happens every 30s, but faster check can be done every 10s (higher cost)
- Health checker checks for 15 times
- Can be integrated with Cloudwatch
- Route53-> Health check-> choose an IP or domain name-> threshold of failover-> create
- Can be monitored, set alarms as well

## Failover route policy
- Routes to different IP only when there is failure(health check fails)
- There should be primary and secondary IPs
- Health check *must* be present
- Create record using failover as the policy
- Choose if it's primary or secondary IP
- Select the health check policy created

## Geolocation route policy
- Can route based on the geolocation
- E.g India routes to IPA, US routes to IPB
- But default route should also be set
- Create a record using Geolocation route policy-> Give the IP address and choose the location
- Must create another record with location chosen as *default*, so that there is no failure if the call is made from different location

## Multivalue routing policy
- Can route to multiple IPs
- Create record with multi valued as policy
- When multiple records are created with different IPs, dig tool call to the domain created should return 3 IP addresses

## Pricing
https://aws.amazon.com/route53/pricing/