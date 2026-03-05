# Secure Landing Zone (SLZ) for Thailand Public Sector

The Secure Landing Zone (SLZ) is for Thailand public sector ministries, agencies (customers) and partners to deploy "Secure by Default" guardrails for their AWS multi-account landing zone. National Cyber Security Agency of Thailand (NCSA) cloud security requirements (where applicable) are transposed into configurable infrastructure as code (IaC) scripts. These scripts help customers and partners to accelerate their implementation at the early foundation stages of implementation.

**[Deployment Workbook and CloudFormation Scripts](/cloudformation/readme.md)**

## Technical requirements mapping to NCSA Cybersecurity Standards for Cloud Services

|Security Domain|Control Implementation|NCSA Control Reference|SLZ Configuration|
| --- | --- | --- | --- |
| Identity Access Management | IAM Identity Center (Administrator, Developer, Security, Infrastructure Operations, DevOps Permission Sets) | 5.2.3 | lz-iam-idc-permissionsets.json |
| Identity Access Management | IAM Password Policy | 5.2.3.8 | lz-account-baseline.yaml |
| Data Sovereignty | IAM Service Control Policy | 5.2.3.6 | lz-organization-scp-guardrails.json |
| Data Protection | IAM Service Control Policy | 5.2.3.6, 5.2.4 | lz-organization-scp-guardrails.json |
| Data Protection | EC2 EBS Default Encryption | 5.1.1, 5.1.3.3, 5.1.2 | lz-account-baseline.yaml |
| Data Protection | S3 Block Public Access, enforce TLS for S3 | 5.1.1, 5.1.3.3, 5.1.2 | lz-organization-rcp-guardrails.json |
| Network Security | VPC, Transit Gateway | 5.2.3, 5.2.7.2  | lz-central-network.json, lz-account-vpc-template.yaml |
| Network Security | Centralized VPC interface endpoints (S3, DynamoDB, KMS, CloudWatch Log, Secrets Manager, EC2, SSM, SSM-Messages, ECR, GuardDuty)  | 5.2.3, 5.2.7.2 | lz-central-network.json |
| Network Security | AWS Network Firewall with IPS (as Suricata rules) | 5.2.7.2 | lz-central-network.json |
| Network Security | AWS Route53 DNS Resolver Firewall | 5.2.7.2 | PENDING |
| Security Assurance | Security Hub | 5.2.6.6, 5.2.10.1 | Manual Configuration and Organization Security Hub Policy |
| Logging and Monitoring | Organization CloudTrail (multi-region, management events), as part of Control Tower | 5.2.6.4, 5.2.6.5, 5.2.6.6 | Manual Configuration | 
| Logging and Monitoring | SSM Session Manager | 5.2.6.4, 5.2.6.5, 5.2.6.6 | lz-account-baseline.yaml |
| Logging and Monitoring | S3 Access Logs | 5.2.6.4, 5.2.6.5, 5.2.6.6 | PENDING |  
| Threat Detection | Amazon GuardDuty | 5.2.6.6 | Organization Security Hub Policy, lz-audit-guardduty-notifications.yaml |
| Vulnerability Management | Amazon Inspector | 5.2.6.8, 5.2.9.2 | Organization Inspector Policy |
| Backup | Control Tower, Backup Vault and backup policies | 5.2.6.3 | Manual Configuration |


## Feature Components
1. Management: AWS Organization and Control Tower
    - Service Control Policy: region deny, enforce data encryption for data resources, and restrict access to approved AWS services.
    - Resource Policy: enforce TLS connections for S3, prevent cross deputy, enforce access to STS, SQS, KMS and Secrets Manager are from IAM principals of own organization.
    - Declarative Policy for EC2: set default settings for IMDSv2, block public access for EBS snapshots and AMI.
    - AI Services Opt-Out Policy: set opt-out for your organization out of having its content used for service improvement AI services.
    - (optional) Control Tower Proactive controls can be configured by the customer
2. Logging: Control Tower using log-archive account
    - Organization CloudTrail
    - S3 Access Logs
    - BACKLOG: SSM Session Logs
    - BACKLOG: WAF Logs
    - BACKLOG: VPC Flow Logs
3. Security: Control Tower using audit account as the delegated security admin for GuardDuty, and Security Hub
    - Threat Detection: GuardDuty
    - Compliance Monitoring: Security Hub and Config
    - Security Alert Notification: SNS Topic
    - Vulnerability Patch Management: Inspector, with SSM Patch Manager Security Baseline
    - BACKLOG: Detective, Firewall Manager (pending availability in region)
4. IAM: Control Tower IAM Identity Center with identity federation to organization's Identity Provider (IdP). 
    - IAM Access Analyzer with Zone of Trust to "Organization" 
    - Organization Central Root management
5. Network: central network account, with ANFW and Route53 DNS Firewall, TGW and centralized VPC endpoints
    - VPC created subnets (app-private, db-private, public) across 3 availability zones.
    - Use VPC interface endpoints for privatelink access to AWS services (S3, SSM, SSMMessages, EC2, Log, KMS, Secrets Manager, ECR)
    - (optional) Customers can choose to deploy either AWS Network Firewall or their preferred network firewall e.g. Palo Alto or Fortinet as virtual appliances running as EC2 instances.
    - BACKLOG: Firewall Manager Policies.
    - BACKLOG: WAF: Baseline WAF configuration to attach to publicly accessible resources.
6. Data Protection:
    - KMS Customer Managed Keys (KMS-CMK) 
    - Service Control Policy - enforce data-at-rest encryption, and block public access.
7. Backup: shared services account, and backup vault account 
    - Centrally managed using Control Tower Backup feature.
    - Backup policies (daily, weekly) configured and member accounts have local backup vaults. 
    - Backup policies move snapshots up to central backup vault at a later stage.
8. Block Public Access at account level: 
    - Declarative Policies for EC2 - IMDSv2, AMI, Snapshots, 
    - S3
    - BACKLOG: VPC BPA
9. Compute Management:
    - EC2: Default Host Configuration Management, EBS Default encryption with KMS-CMK, with SSM Quick Starts for Host Management and Resource Explorer
    - Containers: ECR has Inspector Enhanced Scanning 
    - Lambda: None
    - Aurora, RDS, EFS: enforce data-at-rest encryption with KMS-CMK
10. Forensics: OU

## Pricing Estimate
Refer to this [Thailand SLZ cost estimate report](/cloudformation/thailand_slz_cost_estimation_simple.md) to guide you on the pricing considerations in the running the Thailand SLZ.

## Security
See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License
This library is licensed under the MIT-0 License. See the LICENSE file.
