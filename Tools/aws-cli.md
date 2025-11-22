# AWS CLI Reference Guide

A comprehensive reference for AWS Command Line Interface covering installation, configuration, common commands, and powerful hidden features.

---

## Quick Start: Installation & Configuration

### Installation

**macOS:**
```bash
curl "https://awscli.amazonaws.com/AWSCLIV2.pkg" -o "AWSCLIV2.pkg"
sudo installer -pkg AWSCLIV2.pkg -target /
```

**Linux:**
```bash
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install
```

**Windows:**
Download and run the MSI installer from:
```
https://awscli.amazonaws.com/AWSCLIV2.msi
```

**Verify Installation:**
```bash
aws --version
```

### Configuration

**Basic Configuration:**
```bash
aws configure
```
This will prompt for:
- AWS Access Key ID
- AWS Secret Access Key
- Default region (e.g., us-east-1)
- Default output format (json, yaml, text, table)

**Named Profiles:**
```bash
aws configure --profile production
aws configure --profile development
```

**Using Profiles:**
```bash
aws s3 ls --profile production
export AWS_PROFILE=production  # Set for entire session
```

**Quick Profile Switching:**
```bash
# List all configured profiles
aws configure list-profiles

# View current configuration
aws configure list
```

**Configuration Files Location:**
- Credentials: `~/.aws/credentials`
- Config: `~/.aws/config`

---

## Essential Commands by Service

### EC2 (Elastic Compute Cloud)

**List Instances:**
```bash
aws ec2 describe-instances
```

**List Running Instances Only:**
```bash
aws ec2 describe-instances --filters "Name=instance-state-name,Values=running"
```

**Get Instance Details by Name:**
```bash
aws ec2 describe-instances --filters "Name=tag:Name,Values=MyServer"
```

**Start/Stop/Reboot Instances:**
```bash
aws ec2 start-instances --instance-ids i-1234567890abcdef0
aws ec2 stop-instances --instance-ids i-1234567890abcdef0
aws ec2 reboot-instances --instance-ids i-1234567890abcdef0
```

**List AMIs (Amazon Machine Images):**
```bash
aws ec2 describe-images --owners self
```

**Create EC2 Instance:**
```bash
aws ec2 run-instances \
  --image-id ami-0abcdef1234567890 \
  --instance-type t2.micro \
  --key-name MyKeyPair \
  --security-group-ids sg-0123456789abcdef0 \
  --subnet-id subnet-0123456789abcdef0
```

**List Security Groups:**
```bash
aws ec2 describe-security-groups
```

**List Key Pairs:**
```bash
aws ec2 describe-key-pairs
```

### S3 (Simple Storage Service)

**List All Buckets:**
```bash
aws s3 ls
```

**List Bucket Contents:**
```bash
aws s3 ls s3://my-bucket/
aws s3 ls s3://my-bucket/folder/ --recursive
```

**Copy Files:**
```bash
# Upload file
aws s3 cp file.txt s3://my-bucket/

# Download file
aws s3 cp s3://my-bucket/file.txt ./

# Copy between buckets
aws s3 cp s3://source-bucket/file.txt s3://dest-bucket/
```

**Sync Directories:**
```bash
# Upload directory
aws s3 sync ./local-folder s3://my-bucket/remote-folder/

# Download directory
aws s3 sync s3://my-bucket/remote-folder/ ./local-folder

# Sync with delete (removes files not in source)
aws s3 sync ./local-folder s3://my-bucket/ --delete
```

**Create/Delete Bucket:**
```bash
aws s3 mb s3://my-new-bucket
aws s3 rb s3://my-old-bucket --force  # --force deletes non-empty bucket
```

**Set Bucket Permissions:**
```bash
aws s3api put-bucket-acl --bucket my-bucket --acl private
```

**Enable Versioning:**
```bash
aws s3api put-bucket-versioning --bucket my-bucket --versioning-configuration Status=Enabled
```

### IAM (Identity and Access Management)

**List Users:**
```bash
aws iam list-users
```

**Create User:**
```bash
aws iam create-user --user-name john-doe
```

**List Groups:**
```bash
aws iam list-groups
```

**List Roles:**
```bash
aws iam list-roles
```

**List Policies Attached to User:**
```bash
aws iam list-attached-user-policies --user-name john-doe
```

**Create Access Key for User:**
```bash
aws iam create-access-key --user-name john-doe
```

**List Account Password Policy:**
```bash
aws iam get-account-password-policy
```

### Lambda

**List Functions:**
```bash
aws lambda list-functions
```

**Invoke Function:**
```bash
aws lambda invoke --function-name my-function output.json
```

**Get Function Configuration:**
```bash
aws lambda get-function-configuration --function-name my-function
```

**Update Function Code:**
```bash
aws lambda update-function-code \
  --function-name my-function \
  --zip-file fileb://function.zip
```

**View Function Logs:**
```bash
aws logs tail /aws/lambda/my-function --follow
```

### RDS (Relational Database Service)

**List DB Instances:**
```bash
aws rds describe-db-instances
```

**Create DB Snapshot:**
```bash
aws rds create-db-snapshot \
  --db-instance-identifier mydb \
  --db-snapshot-identifier mydb-snapshot-2024
```

**List Snapshots:**
```bash
aws rds describe-db-snapshots
```

**Stop/Start DB Instance:**
```bash
aws rds stop-db-instance --db-instance-identifier mydb
aws rds start-db-instance --db-instance-identifier mydb
```

### CloudFormation

**List Stacks:**
```bash
aws cloudformation list-stacks --stack-status-filter CREATE_COMPLETE
```

**Describe Stack:**
```bash
aws cloudformation describe-stacks --stack-name my-stack
```

**Create Stack:**
```bash
aws cloudformation create-stack \
  --stack-name my-stack \
  --template-body file://template.yaml \
  --parameters ParameterKey=KeyName,ParameterValue=MyKey
```

**Update Stack:**
```bash
aws cloudformation update-stack \
  --stack-name my-stack \
  --template-body file://template.yaml
```

**Delete Stack:**
```bash
aws cloudformation delete-stack --stack-name my-stack
```

### CloudWatch

**List Alarms:**
```bash
aws cloudwatch describe-alarms
```

**Get Metric Statistics:**
```bash
aws cloudwatch get-metric-statistics \
  --namespace AWS/EC2 \
  --metric-name CPUUtilization \
  --dimensions Name=InstanceId,Value=i-1234567890abcdef0 \
  --start-time 2024-01-01T00:00:00Z \
  --end-time 2024-01-02T00:00:00Z \
  --period 3600 \
  --statistics Average
```

**Tail Log Group:**
```bash
aws logs tail /aws/lambda/my-function --follow
```

**Filter Log Events:**
```bash
aws logs filter-log-events \
  --log-group-name /aws/lambda/my-function \
  --filter-pattern "ERROR"
```

### ECS (Elastic Container Service)

**List Clusters:**
```bash
aws ecs list-clusters
```

**List Services:**
```bash
aws ecs list-services --cluster my-cluster
```

**List Tasks:**
```bash
aws ecs list-tasks --cluster my-cluster
```

**Describe Service:**
```bash
aws ecs describe-services --cluster my-cluster --services my-service
```

**Update Service (Force New Deployment):**
```bash
aws ecs update-service --cluster my-cluster --service my-service --force-new-deployment
```

---

## Easter Eggs & Advanced Features

### 1. Output Formatting Magic

**Table Output (Human-Readable):**
```bash
aws ec2 describe-instances --output table
```

**YAML Output:**
```bash
aws ec2 describe-instances --output yaml
```

**JMESPath Queries (Powerful Filtering):**
```bash
# Get only instance IDs
aws ec2 describe-instances --query 'Reservations[*].Instances[*].InstanceId' --output text

# Get instance ID and state
aws ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,State.Name]' --output table

# Filter running instances and get specific fields
aws ec2 describe-instances \
  --filters "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].[InstanceId,InstanceType,PrivateIpAddress,Tags[?Key==`Name`].Value|[0]]' \
  --output table
```

### 2. CLI Auto-Prompt (Interactive Mode)

Enable auto-prompt for command completion and help:
```bash
aws --cli-auto-prompt
# Or set permanently
aws configure set cli_auto_prompt on
```

### 3. Dry Run Commands

Test commands without actually executing them:
```bash
aws ec2 run-instances --dry-run \
  --image-id ami-0abcdef1234567890 \
  --instance-type t2.micro
```

### 4. Wizard Mode

Interactive command builder:
```bash
aws ec2 run-instances --cli-input-json file://config.json --generate-cli-skeleton
```

Generate skeleton JSON:
```bash
aws ec2 run-instances --generate-cli-skeleton > ec2-template.json
```

### 5. Pagination Control

Control how many results are returned:
```bash
# Get all results (no pagination)
aws s3api list-objects-v2 --bucket my-bucket --no-paginate

# Set page size
aws s3api list-objects-v2 --bucket my-bucket --page-size 100

# Get only first page
aws s3api list-objects-v2 --bucket my-bucket --max-items 10
```

### 6. Wait Commands

Wait for resources to reach a specific state:
```bash
# Wait for instance to be running
aws ec2 wait instance-running --instance-ids i-1234567890abcdef0

# Wait for instance to be stopped
aws ec2 wait instance-stopped --instance-ids i-1234567890abcdef0

# Wait for S3 bucket to exist
aws s3api wait bucket-exists --bucket my-bucket
```

### 7. Assumed Role Credentials

Assume a role and execute commands:
```bash
# Get temporary credentials
aws sts assume-role \
  --role-arn arn:aws:iam::123456789012:role/MyRole \
  --role-session-name my-session

# Set environment variables for assumed role
export AWS_ACCESS_KEY_ID=...
export AWS_SECRET_ACCESS_KEY=...
export AWS_SESSION_TOKEN=...
```

### 8. SSM Parameter Store Quick Access

Store and retrieve configuration values:
```bash
# Put parameter
aws ssm put-parameter --name /myapp/database/password --value "supersecret" --type SecureString

# Get parameter
aws ssm get-parameter --name /myapp/database/password --with-decryption

# Get parameter value only
aws ssm get-parameter --name /myapp/database/password --with-decryption --query 'Parameter.Value' --output text

# Get all parameters by path
aws ssm get-parameters-by-path --path /myapp/ --recursive
```

### 9. Resource Tagging at Scale

Tag multiple resources at once:
```bash
# Tag multiple instances
aws ec2 create-tags \
  --resources i-1234567890abcdef0 i-0987654321fedcba0 \
  --tags Key=Environment,Value=Production Key=Owner,Value=DevOps
```

### 10. CloudShell Integration

AWS CloudShell comes with AWS CLI pre-installed and configured. Access via AWS Console.

### 11. Batch Commands with xargs

Process multiple resources:
```bash
# Stop all running instances with a specific tag
aws ec2 describe-instances \
  --filters "Name=tag:Environment,Values=Development" "Name=instance-state-name,Values=running" \
  --query 'Reservations[*].Instances[*].InstanceId' \
  --output text | xargs -n 1 aws ec2 stop-instances --instance-ids

# Delete all snapshots older than a date
aws ec2 describe-snapshots --owner-ids self \
  --query 'Snapshots[?StartTime<=`2023-01-01`].SnapshotId' \
  --output text | xargs -n 1 aws ec2 delete-snapshot --snapshot-id
```

### 12. Output to File with Metadata

Save command output with metadata:
```bash
aws ec2 describe-instances > instances-$(date +%Y%m%d).json
```

### 13. Debug and Logging

Enable debug output:
```bash
aws s3 ls --debug
aws s3 ls --debug 2> debug.log  # Save debug output to file
```

### 14. Shorthand Syntax

Use shorthand for complex parameters:
```bash
# Instead of JSON
aws dynamodb put-item --table-name MyTable --item '{"id":{"S":"123"},"name":{"S":"John"}}'

# Use shorthand
aws dynamodb put-item --table-name MyTable --item id=S=123,name=S=John
```

### 15. CLI Aliases

Create custom aliases in `~/.aws/cli/alias`:
```ini
[toplevel]
whoami = sts get-caller-identity
running = ec2 describe-instances --filters "Name=instance-state-name,Values=running"
my-ips = ec2 describe-instances --query 'Reservations[*].Instances[*].[InstanceId,PublicIpAddress,PrivateIpAddress]' --output table
```

Usage:
```bash
aws whoami
aws running
aws my-ips
```

---

## Query Tips & Tricks

### Find Your Account ID
```bash
aws sts get-caller-identity --query Account --output text
```

### List All Regions
```bash
aws ec2 describe-regions --query 'Regions[*].RegionName' --output text
```

### Get Cost and Usage
```bash
aws ce get-cost-and-usage \
  --time-period Start=2024-01-01,End=2024-01-31 \
  --granularity MONTHLY \
  --metrics BlendedCost
```

### Check Service Quotas
```bash
aws service-quotas list-service-quotas --service-code ec2
```

### List All Resources in Account (Using Resource Groups)
```bash
aws resourcegroupstaggingapi get-resources
```

---

## Troubleshooting

**Check Configuration:**
```bash
aws configure list
aws configure get region
```

**Verify Credentials:**
```bash
aws sts get-caller-identity
```

**Clear Cached Credentials:**
```bash
rm -rf ~/.aws/cli/cache/
```

**Test Network Connectivity:**
```bash
aws ec2 describe-regions --debug
```

**Common Error: "Unable to locate credentials"**
- Check `~/.aws/credentials` file exists
- Verify environment variables: `echo $AWS_ACCESS_KEY_ID`
- Try `aws configure` to set credentials

**Common Error: "Region not specified"**
- Set default region: `aws configure set region us-east-1`
- Or use `--region` flag: `aws ec2 describe-instances --region us-west-2`

---

## Performance Tips

1. **Use `--no-paginate`** for large datasets when you need all results
2. **Use `--page-size`** to control memory usage with large result sets
3. **Use `--query`** to filter data server-side before transmission
4. **Use `--output text`** with `--query` for parsing in scripts
5. **Enable CLI caching** for faster repeated queries:
   ```bash
   aws configure set cli_cache on
   ```

---

## Security Best Practices

1. **Never hardcode credentials** in scripts or code
2. **Use IAM roles** instead of access keys whenever possible
3. **Enable MFA** for sensitive operations
4. **Use named profiles** to separate environments
5. **Rotate access keys** regularly
6. **Use AWS SSO** for centralized authentication
7. **Set minimal IAM permissions** following least privilege principle
8. **Use CloudTrail** to audit CLI activity

---

## Additional Resources

- **Official Documentation:** https://docs.aws.amazon.com/cli/
- **Command Reference:** https://awscli.amazonaws.com/v2/documentation/api/latest/index.html
- **JMESPath Tutorial:** https://jmespath.org/tutorial.html
- **AWS CLI on GitHub:** https://github.com/aws/aws-cli

---

*This guide covers AWS CLI v2. Keep your CLI updated for latest features: `aws --version` and check for updates periodically.*