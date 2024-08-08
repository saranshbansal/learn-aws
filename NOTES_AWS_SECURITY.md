# AWS Certificate Manager

Service: AWS Certificate Manager (ACM) helps you provision, manage, and deploy SSL/TLS certificates for your AWS-based websites and applications.

Purpose: Ensures secure communication between clients and servers by encrypting data transmitted over networks.

## Key Concepts

- SSL/TLS Certificates: Certificates used to secure network communications by encrypting data between a client and server.
- Certificate Management: ACM automates the process of obtaining, renewing, and deploying certificates, reducing manual overhead.

## Features

- Certificate Issuance: ACM provides free public and private SSL/TLS certificates.
  - Public Certificates: Used to secure websites accessible from the internet.
  - Private Certificates: Used for internal applications within an organization (requires AWS Private CA).

- Automatic Renewal: ACM automatically renews certificates before they expire, reducing the risk of service disruptions due to expired certificates.
- Certificate Deployment: Easily deploy certificates to various AWS services like Elastic Load Balancers (ELBs), Amazon
- CloudFront distributions, and APIs via API Gateway.
- Certificate Validation: Supports two methods for validating domain ownership:
    - DNS Validation: ACM provides a CNAME record to add to your DNS configuration.
    - Email Validation: ACM sends validation emails to domain registrants.

## ACM vs. AWS Private CA

- ACM: Manages public certificates and integrates with AWS services for SSL/TLS.
- AWS Private CA: For organizations needing internal certificates. ACM Private CA offers additional features for managing private certificates and their lifecycle.

## Using ACM with AWS Services

- Elastic Load Balancer (ELB): Associate ACM certificates with your ELBs to enable HTTPS traffic.
- Amazon CloudFront: Deploy ACM certificates to secure content delivery with CloudFront.
- Amazon API Gateway: Use ACM certificates to secure APIs exposed via API Gateway.
- Amazon Elastic Beanstalk: Integrate ACM certificates for secure communication with applications deployed on Elastic Beanstalk.

## Steps to Request and Deploy a Certificate

### Request a Certificate:
- Navigate to the ACM console.
- Choose "Request a certificate" and select either public or private.
- Enter domain names and select validation method.

### Validate Domain:
- Follow the instructions for DNS or email validation.
- For DNS validation, add the CNAME record to your DNS provider.
- For email validation, follow the link in the validation email.

### Deploy the Certificate:
Once validated, choose the appropriate AWS service (e.g., ELB, CloudFront) to deploy the certificate.

## Managing Certificates

- Monitoring: Use the ACM console to view certificate status and expiration dates.
- Renewal: ACM automatically handles renewal, but ensure DNS records or email settings remain valid to avoid disruptions.
- Revocation: You can manually revoke certificates from the ACM console if needed.

## Best Practices

- Use DNS Validation: Prefer DNS validation over email validation for automation and reliability.
- Monitor Expiration: Regularly check for certificate expirations and ensure automatic renewal is working.
- Minimize Exposure: Use ACM for SSL/TLS on public-facing services and AWS Private CA for internal communications.
- Secure Key Storage: ACM handles private key storage securely, reducing the risk of exposure.

## Exam Tips

- Understand Validation Methods: Be familiar with DNS and email validation processes.
- Know Deployment Scenarios: Understand how to deploy certificates with various AWS services.
- Familiarize with ACM Console: Practice navigating the ACM console and managing certificates.

# AWS KMS
