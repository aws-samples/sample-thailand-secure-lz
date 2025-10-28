# Thailand Secure Landing Zone (SLZ) Cost Analysis Estimate Report
## Service Overview
The Thailand Secure Landing Zone (SLZ) is designed for Thailand public sector ministries, agencies (customers) and partners to deploy "Secure by Default" guardrails for their AWS multi-account landing zone. National Cyber Security Agency (NCSA) cloud security requirements (where applicable) are transposed into configurable infrastructure as code (IaC) scripts. This comprehensive solution uses multiple AWS services following a pay-as-you-go pricing model, making it cost-effective for various government workloads.
## Pricing Model
This cost analysis estimate is based on the following pricing model:
- **ON DEMAND** pricing (pay-as-you-go) unless otherwise specified
- Standard service configurations without reserved capacity or savings plans
## Pricing Methodology
### Regions Used for Pricing
The pricing in this report is based on:
- **Primary Region**: Thailand ap-southeast-7
- **Secondary Region**: US East (N. Virginia) us-east-1 for global services
- **Pricing Date**: As of October 2025
### How Pricing Was Obtained
1. **AWS Pricing Pages**: [AWS service pricing](https://aws.amazon.com/pricing/)
2. **AWS Pricing Calculator**: Used for multi-service calculations
### Validation Method
To ensure accuracy, customers should:
1. Check the AWS Pricing Calculator with "ap-southeast-7" selected
2. Review actual billing after deployment for any regional variations
3. Contact AWS sales for official quotes specific to Thailand region
### AWS Pricing Calculator Template
For customized cost estimation, use our pre-configured AWS Pricing Calculator template:
**Pricing Template URL**: https://calculator.aws/#/estimate?id=a25858f5959192b4e4922d754e59da211b1457df
This pricing template includes:
- All Thailand SLZ core services pre-configured
- Baseline assumptions for 8-account setup
- Thailand (ap-southeast-7) region pricing
- Standard usage patterns for government workloads
**How to use the template:**
1. Click the template URL above
2. Modify service quantities based on your specific requirements
3. Adjust regions if deploying to different locations
4. Add or remove services as needed for your use case
5. Export the estimate for budget planning and approval processes
The template serves as a starting point - customize it based on your organization's actual requirements and usage patterns.
## Assumptions
- Minimum setup with 8 core AWS accounts:
  - Management Account (Organization root)
  - Audit Account (Security and compliance monitoring)
  - Log Archive Account (Centralized logging)
  - Shared Services Account (Shared tools and services)
  - Network Account (Centralized networking)
  - Central Backup Account (Backup management)
  - Production Account (Production workloads)
  - Non-Production Account (Development/testing)
- 2 AWS regions enabled: ap-southeast-7 (Thailand) and us-east-1 (N. Virginia)
- 50x EC2 instances across all accounts
- 2TB of data processed through Network Firewall monthly
- 50GB of VPC Flow Logs and DNS logs for GuardDuty
- 500 configuration items per account for AWS Config
- Daily and weekly backup policies enabled
- 3x Availability Zones in use for Network Firewall
- 6x VPC endpoints for essential AWS services
- Standard workload patterns with moderate API usage
## Limitations and Exclusions
- Data transfer costs between regions
- EC2 instance costs
- Storage costs for S3 buckets (except backup storage)
- Third-party firewall appliances if chosen over AWS Network Firewall
- AWS Support plans
- Training and implementation costs
- Costs for optional services not yet available in Thailand region (Inspector, Detective, Firewall Manager)

### Cost Calculation - Minimum Usage (8 Core Accounts)
For organizations implementing the minimum viable Thailand SLZ with 8 core accounts:
| Service | Usage | Monthly Cost |
|---------|-------|-------------|
| AWS Control Tower & Organizations | Management of 8 AWS accounts across 2 regions (Management, Audit, Log Archive, Shared Services, Network, Central Backup, Production, Non-Production) | $0.00 |
| AWS GuardDuty | Threat detection across 8 accounts in 2 regions (Cloudtrail Events: 16 million events/month, Vpc Flow Logs: 200 GB/month, Dns Logs: 50 GB/month) | $437.80 |
| AWS Security Hub | Security posture management for 8 accounts (Security Checks: 4,000 checks/month (500 per account), Finding Ingestion: 80,000 events/month, Automation Rules: 15 rules with 5 criteria) | $10.40 |
| AWS Config | Configuration tracking for all resources (Configuration Items: 8,000 items/month (1000 per account), Rule Evaluations: 40,000 evaluations/month) | $13.00 |
| AWS Network Firewall | Centralized firewall across 3 AZs with 5TB traffic (Endpoints: 3 endpoints (1 per AZ), Traffic: 5,000 GB/month) | $1,510.40 |
| AWS Transit Gateway | Central connectivity hub for VPCs (Attachments: 3 VPC attachments, Data Transfer: 500 GB/month) | $168.30 |
| VPC Endpoints | Interface endpoints for AWS services (S3, SSM, EC2, etc.) (Endpoints: 6 interface endpoints × 3 AZs, Data Transfer: 200 GB/month) | $107.49 |
| AWS KMS | Customer managed keys for encryption (Keys: 8 KMS keys (including replicas), Requests: 200,000 requests/month) | $8.60 |
| AWS Backup | Centralized backup management (Ebs Backups: 2,000 GB, Efs Backups: 800 GB, Cross Region Copies: 400 GB) | $103.63 |
| AWS CloudTrail | Organization-wide audit logging (Data Events: 20 million S3 events, Insights Events: 4 million events) | $10.00 |
| AWS Systems Manager | EC2 fleet management and patching (Managed Instances: 200 instances, Parameter Requests: 400K/month, Patch Scans: 200 instances × 4 scans) | $2.00 |
| AWS IAM Identity Center | Single sign-on for all accounts (Users: Unlimited, Applications: Unlimited) | $0.00 |
| **Total** | **All services** | **$2,369.62/month** |

## Annual Cost Summary
Based on the minimum 8 accounts configuration:
- **Monthly Cost**: $2,369.62
- **Annual Cost**: $28,435.44
- **Cost per Account**: $296.20/month
## Cost Saving Opportunities
Top 5 general AWS cost optimization strategies:
### 1. Right-size Your Resources
Regularly review and adjust EC2 instance types, RDS instances, and other compute resources based on actual usage patterns. Use AWS Compute Optimizer for recommendations.
### 2. Leverage Reserved Instances and Savings Plans
Commit to 1-3 year terms for predictable workloads to save 20-72% on compute costs. Consider Convertible Reserved Instances for flexibility.
### 3. Implement Auto Scaling and Scheduling
Use Auto Scaling Groups to automatically adjust capacity based on demand. Schedule non-production resources to run only during business hours.
### 4. Optimize Storage Costs
Use S3 Intelligent Tiering, lifecycle policies to move data to cheaper storage classes, and delete unused EBS snapshots and volumes.
### 5. Monitor and Set Up Cost Controls
Enable AWS Cost Explorer, set up billing alerts, use AWS Budgets, and implement cost allocation tags for better visibility and control.

## Conclusion

The Thailand Secure Landing Zone provides comprehensive security and governance capabilities for AWS multi-account environments. The minimum viable setup with 8 core accounts has an estimated monthly cost of $2,827.70, covering essential security services, networking, backup, and compliance monitoring.
