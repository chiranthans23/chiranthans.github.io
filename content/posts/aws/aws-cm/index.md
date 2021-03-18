---
title: "AWS Certificate Manager"
date: "2021-02-17"
menu:
  sidebar:
    name: AWS Certificate Manager
    identifier: aws-acm
    parent: aws
    weight: 10
---

# AWS certificate manager (ACM)

- Helps in managing SSL certificates

## Ways to renew certificates
1. Buy and upload using AWS CLI
2. ACM will provision and renew for free

## Integrations for which SSL certificates are loaded
1. Load balancers
2. Cloudfront distribution
3. APIs on API gateways

## Creating SSL certificate
- Management console -> ACM
- Select public or private certificate
- Can import existing certificate or create new one
- Provide the domain name
- Choose validation criteria
- If CNAME is not created for the domain, create one
- Once the validation is completed, domain should be accessible using https request and certificate can also be viewed

## Pricing
- $400.00 per month for each ACM private CA until you delete the CA.
- https://aws.amazon.com/certificate-manager/pricing/
