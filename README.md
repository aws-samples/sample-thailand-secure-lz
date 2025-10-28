# Secure Landing Zone (SLZ) for Thailand Public Sector

The Secure Landing Zone (SLZ) provides a secure cloud foundation for Thai government ministries, agencies, and authorized partners deploying AWS multi-account environments. Built to align with Thailand National Cyber Security Agency (NCSA) requirements, the solution uses Infrastructure as Code (IaC) scripts to automate security guardrails implementation. This streamlined approach allows organizations to rapidly establish a compliant AWS environment while maintaining security best practices from day one.

**[Deployment Workbook and CloudFormation Scripts](/cloudformation/readme.md)**


## Feature Components
1. Management: AWS Organizations and Control Tower
    - Service Control Policy: region deny, enforce data encryption for data resources, and restrict access to The approved AWS services.
    - Resource Policy: enforce TLS connections, prevent cross deputy
    - (optional) Control Tower Proactive controls can be configured by the customer
2. Logging: Control Tower using log-archive account
    - Organization CloudTrail
    - Amazon S3 Access Logs
    - SSM Session Logs
    - BACKLOG: WAF Logs
    - BACKLOG: VPC Flow Logs
3. Security: Control Tower using audit account as the delegated security admin for Amazon GuardDuty, and AWS Security Hub
    - Threat Detection: Amazon GuardDuty
    - Compliance Monitoring: AWS Security Hub and Config
    - Security Alert Notification: SNS Topic
    - Vulnerability Patch Management: Amazon Inspector, with SSM Patch Manager Security Baseline
    - AWS Firewall Manager: centralized network configuration management for security groups, and WAF by policy enforcement
    - BACKLOG: Amazon Detective (pending availability in region)
4. IAM: Control Towwer AWS IAM Identity Center with identity federation to organization's Identity Provider (IdP). 
    - IAM Access Analyzer with Zone of Trust to "Organization" 
    - Organization Central Root management
5. Network: central network account, with ANFW and Amazon Route 53 DNS Firewall, TGW and centralized VPC endpoints
    - VPC created subnets (app-private, db-private, public) across 3 availability zones.
    - Use VPC interface endpoints for privatelink access to AWS services (Amazon S3, SSM, SSMMessages,Amazon EC2 , Log, KMS, Secrets Manager, Amazon ECR)
    - (optional) Customers can choose to deploy either AWS Network Firewall or their preferred network firewall e.g. Palo Alto or Fortinet as virtual appliances running as EC2 instances.
    - BACKLOG: AWS Firewall Manager Policies
    - BACKLOG: WAF: Baseline WAF configuration to attach to publicly accessible resources.
6. Data Protection:
    - KMS Customer Managed Keys (KMS-CMK) 
    - Service Control Policy - enforce data-at-rest encryption, and block public access.
7. Backup: shared services account, and backup vault account 
    - Centrally managed using Control Tower Backup feature.
    - Backup policies (daily, weekly) configured and member accounts have local backup vaults. 
    - Backup policies move snapshots up to central backup vault at a later stage.
8. Block Public Access at account level: 
    - Declarative Policies for Amazon EC2 - IMDSv2, AMI, Snapshots, 
    - Amazon S3
    - BACKLOG: VPC BPA
9. Compute Management:
    - Amazon EC2: Default Host Configuration Management, EBS Default encryption with KMS-CMK, with SSM Quick Starts for Host Management and Resource Explorer
    - Containers: ECR has Inspector Enhanced Scanning 
    - AWS Lambda: None
    - Amazon Aurora, Amazon RDS, Amazon EFS: enforce data-at-rest encryption with KMS-CMK
10. Forensics: OU

## Pricing Estimate
Refer to this [Thailand SLZ cost estimate report](/cloudformation/thailand_slz_cost_estimation_simple.md) to guide you on the pricing considerations in the running the Malaysia SLZ.

## Security
See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License
This library is licensed under the MIT-0 License. See the LICENSE file.
