# Thailand Secure Landing Zone (SLZ) Cost Analysis Estimate Report

## Service Overview
The Thailand Secure Landing Zone (SLZ) is designed for Thailand public sector ministries, agencies (customers) and partners to deploy "Secure by Default" guardrails for their AWS multi-account landing zone. This comprehensive solution uses multiple AWS services following a pay-as-you-go pricing model, making it cost-effective for various government workloads.

## Scope of This Estimate
This report estimates the cost of the **SLZ foundation setup only** — the landing zone infrastructure deployed by the SLZ CloudFormation scripts and Control Tower, before any workloads are onboarded. Workload consumption (EC2 instances, application traffic, backup storage of workload data, S3 data events, vulnerability scans of workload resources) is explicitly **excluded**. See "Costs That Scale With Workloads" for what changes once applications are deployed.

## Pricing Model
This cost analysis estimate is based on the following pricing model:
- **ON DEMAND** pricing (pay-as-you-go) unless otherwise specified
- Standard service configurations without reserved capacity or savings plans
- 730 hours per month convention (matching the AWS Pricing Calculator)

## Pricing Methodology
### Regions Used for Pricing
The pricing in this report is based on:
- **Primary Region**: Thailand ap-southeast-7 (all network infrastructure and home-region services)
- **Secondary Region**: US East (N. Virginia) us-east-1 (Control Tower governed region for global services; only Config, GuardDuty, Security Hub and a small number of KMS keys bill here)
- **Pricing Date**: July 2026

### How Pricing Was Obtained
1. **AWS Price List API**: Unit prices retrieved from the public AWS Price List (bulk offer files) for ap-southeast-7
2. **AWS Pricing Pages**: [AWS service pricing](https://aws.amazon.com/pricing/) for billing-model verification (e.g., Network Firewall endpoint billing per AZ)
3. **SLZ CloudFormation templates**: Resource counts (firewall endpoints, NAT gateways, VPC endpoints, KMS keys) taken directly from the templates in this repository

### Validation Method
To ensure accuracy, customers should:
1. Check the AWS Pricing Calculator with "ap-southeast-7" selected
2. Review actual billing after deployment for any regional variations
3. Contact AWS sales for official quotes specific to Thailand region

### AWS Pricing Calculator Template
For customized cost estimation, use our pre-configured AWS Pricing Calculator template:
**Pricing Template URL**: https://calculator.aws/#/estimate?id=a25858f5959192b4e4922d754e59da211b1457df

Use the template as a starting point and adjust service quantities to match your organization's requirements and expected workload profile.

## Assumptions
- Minimum setup with 8 core AWS accounts:
  - Management Account (Organization root)
  - Audit Account (Security and compliance monitoring)
  - Log Archive Account (Centralized logging)
  - Shared Services Account (Shared tools and services)
  - Network Account (Centralized networking)
  - Central Backup Account (Backup management)
  - Production Account (empty, no workloads deployed)
  - Non-Production Account (empty, no workloads deployed)
- 2 AWS regions governed by Control Tower: ap-southeast-7 (Thailand) and us-east-1 (N. Virginia)
- Central network account as deployed by `lz-central-network.json`:
  - 1x AWS Network Firewall with endpoints in 3 Availability Zones
  - 1x Transit Gateway with 2 VPC attachments (Inspection VPC, Endpoints VPC)
  - 3x NAT Gateways (one per AZ) with 3 public IPv4 addresses
  - 8x interface VPC endpoints (CloudWatch Logs, KMS, EC2, SSM, SSM-Messages, Secrets Manager, ECR-API, ECR-DKR) across 3 AZs = 24 endpoint-AZs
  - Gateway endpoints for S3 and DynamoDB (free)
- ~40 KMS customer managed keys across the organization: control-tower-key (1), Control Tower backup key + us-east-1 replica (2), CloudWatch Logs key (1), account baseline StackSet 7 member accounts x 2 regions x 2 keys (28), audit account keys (4), delegation stack keys (3), central network key (1)
- Idle-state activity levels (no workloads): ~50 GB/month inspected traffic, ~3M CloudTrail events analyzed by GuardDuty, ~25,000 Security Hub CSPM checks, ~4,000 Config configuration items across 16 account-regions
- Security Hub organization policy enables GuardDuty, Security Hub CSPM and Inspector org-wide; Inspector bills $0 with no EC2/ECR/Lambda resources to scan
- Backup plans configured but no resources tagged for backup (no workload data)

## Limitations and Exclusions
- Workload compute, storage, and application traffic (EC2, EBS, RDS, S3 workload buckets, etc.)
- Data processing charges above the idle allowances stated in the assumptions
- Spoke VPC costs in workload accounts (`lz-account-vpc-template.yaml` — see scaling section)
- Backup storage for workload data
- Organization CloudTrail S3 data events (no workload buckets at setup)
- Data transfer costs between regions and to on-premises
- Third-party firewall appliances if chosen over AWS Network Firewall
- Firewall Manager policies (SLZ backlog item; $100/policy/region when adopted)
- Services not yet available in the Thailand region (e.g., Detective)
- AWS Support plans, training and implementation costs

### Cost Calculation — SLZ Setup Only (8 Core Accounts, No Workloads)
| Service | Unit Pricing (ap-southeast-7) | Calculation | Monthly Cost |
|---------|-------------------------------|-------------|-------------|
| AWS Network Firewall (endpoints) | $0.66 per endpoint-hour | 3 AZ endpoints × $0.66 × 730 hrs | $1,445.40 |
| AWS Network Firewall (data processing) | $0.065 per GB | ~50 GB idle traffic × $0.065 | $3.25 |
| VPC Interface Endpoints | $0.0117 per endpoint-AZ-hour | 8 services × 3 AZs × $0.0117 × 730 hrs | $204.98 |
| VPC Interface Endpoints (data) | $0.01 per GB (first 1 PB) | ~50 GB × $0.01 | $0.50 |
| NAT Gateways | $0.05015 per hour + $0.05015 per GB | 3 × $0.05015 × 730 hrs + ~20 GB | $110.83 |
| AWS Transit Gateway | $0.06 per attachment-hour + $0.02 per GB | 2 attachments × $0.06 × 730 hrs + ~50 GB | $88.60 |
| Public IPv4 Addresses | $0.005 per hour | 3 NAT gateway EIPs × $0.005 × 730 hrs | $10.95 |
| AWS KMS | $1.00 per key per month + $0.03 per 10K requests | ~40 customer managed keys + requests | $41.00 |
| AWS Security Hub (CSPM) | $0.001 per check (first 100K) + $0.00003 per ingested finding (first 10K free) | ~25,000 checks + ingestion, 8 accounts × 2 regions | $27.00 |
| AWS Config | $0.003 per configuration item + $0.001 per rule evaluation | ~4,000 items + evaluations, 16 account-regions | $15.00 |
| Amazon GuardDuty | $3.91 per 1M CloudTrail events; $0.9775 per GB flow/DNS logs (first 500 GB) | ~3M events + minimal flow/DNS logs | $15.00 |
| Amazon CloudWatch Logs | $0.50 per GB ingested (vended) + $0.03 per GB-month storage | VPC flow logs, firewall logs, SSM session logs (~5 GB) | $3.00 |
| Amazon S3 (log archive) | $0.0225 per GB-month | CloudTrail/Config logs, ~2–5 GB/month (grows over time) | $2.00 |
| SNS / EventBridge / Lambda | per-request pricing | Security alert plumbing, near-idle | $1.00 |
| Amazon Inspector | per instance/image scanned | No EC2/ECR/Lambda resources at setup | $0.00 |
| AWS Control Tower, Organizations, IAM Identity Center, SCPs/RCPs, IAM Access Analyzer (org), SSM Session Manager, Backup framework | n/a | Free — no service charge | $0.00 |
| **Total** | | | **$1,968.51/month** |

## Annual Cost Summary
Based on the minimum 8-account, setup-only configuration:
- **Monthly Cost**: $1,968.51 (~$1,970)
- **Annual Cost**: ~$23,622
- **Cost per Account**: ~$246/month

Cost distribution: the central network account represents ~95% of the total (~$1,865/month), and AWS Network Firewall alone is ~73%. The 3-AZ firewall footprint is a resilience and CGSO-alignment design decision fixed in the template. Note that the Network Firewall endpoint rate in Thailand ($0.66/hr) is higher than in us-east-1 ($0.395/hr).

First-month costs differ slightly: the initial AWS Config recording of all resources creates a one-time spike, offset by the 30-day free trials for GuardDuty and Security Hub and the 15-day Inspector trial.

## Costs That Scale With Workloads
The following are excluded from the setup baseline and grow as workloads are onboarded:

| Driver | Unit Pricing (ap-southeast-7) | Notes |
|--------|-------------------------------|-------|
| Spoke VPC Transit Gateway attachment | $0.06/hr (~$43.80/month each) | One per workload account VPC |
| Spoke VPC NAT Gateways | $0.05015/hr each | `lz-account-vpc-template.yaml` can deploy up to 4 per account |
| Network Firewall data processing | $0.065 per GB | All inspected east-west and egress traffic; bytes already processed by ANFW receive a discount on NAT Gateway processing charges |
| Transit Gateway data processing | $0.02 per GB | All inter-VPC traffic |
| GuardDuty | Tiered per events/GB analyzed | Grows with CloudTrail activity, VPC flow logs, DNS queries; protection plans (S3, Runtime, Malware, RDS, Lambda) bill on usage |
| Amazon Inspector | $0.0021 per EC2 instance-hour (~$1.53/instance/month); $0.11 per container image scan | Per workload resource scanned |
| AWS Backup | Per GB-month by resource type + cross-account copy | Resources tagged with Control Tower backup tags |
| CloudTrail S3 data events | $1.00 per 1M events | If the optional organization data-event trail is enabled |
| Security Hub / Config | Per check / per configuration item | Grow with resource counts and change frequency |
| Firewall Manager policies | $100 per policy per region | SLZ backlog item, when adopted |

## Cost Saving Opportunities
Five SLZ-relevant optimization strategies:

### 1. Right-size the Centralized Interface Endpoint List
Each interface endpoint costs ~$8.54/month per AZ ($25.63 across 3 AZs). The 8 pre-configured endpoints total ~$205/month. Remove endpoints for services you do not use, and add new ones only when a workload needs private access.

### 2. Understand the Network Firewall Cost Profile Before Scaling Traffic
ANFW endpoint hours are the dominant fixed cost (~$1,445/month), and data processing ($0.065/GB) becomes the dominant variable cost at volume. Measure expected traffic before onboarding heavy workloads, route only traffic that requires inspection through the firewall, and note that bytes processed by ANFW receive a discount on NAT Gateway data processing charges.

### 3. Apply Log Lifecycle Policies Early
CloudWatch log groups in the SLZ default to 14-day (network) and 90-day (session) retention. Configure S3 lifecycle policies on the log-archive buckets to transition CloudTrail/Config logs to cheaper storage classes within your 1-year retention requirement.

### 4. Review GuardDuty Protection Plans Per Account
Protection plans (S3 Protection, Runtime Monitoring, Malware Protection, RDS and Lambda monitoring) bill on usage. Enable them org-wide per the SLZ baseline, but monitor per-account spend as workloads grow and confirm each plan maps to a real risk for that account type.

### 5. Set Up Cost Controls From Day One
Enable AWS Cost Explorer, AWS Budgets with alerts, and Cost Anomaly Detection in the management account. Activate cost allocation tags so network and security shared costs can be attributed to consuming workloads via chargeback/showback.

## Conclusion

The Thailand Secure Landing Zone provides comprehensive security and governance capabilities for AWS multi-account environments. The minimum viable setup with 8 core accounts and no workloads has an estimated monthly cost of **~$1,970 (~$23,600/year)**, dominated by the centralized network inspection infrastructure (Network Firewall, VPC endpoints, NAT gateways and Transit Gateway) in the network account. Security and governance services (Security Hub, GuardDuty, Config, KMS) add roughly $100/month at idle and scale with workload activity thereafter.

