# Terraform AWS EC2 Provisioning with Jenkins

## 1. Problem Statement Overview

Modern infrastructure provisioning often depends on manual steps, inconsistent configuration, and environment drift. In this project, the goal is to avoid manual AWS resource creation by defining infrastructure as code and automating its deployment through Jenkins.

The repository provisions a basic Amazon EC2 instance in the AWS ap-south-1 region using Terraform, so that infrastructure can be created repeatedly, reliably, and with version control.

## 2. Solution Approach

This project uses:

- Terraform for infrastructure-as-code (IaC)
- AWS provider configuration for resource provisioning
- Jenkins pipeline automation for initialization, planning, and deployment

The Terraform configuration in `main.tf`:

- defines the required Terraform version
- configures the AWS provider for the ap-south-1 region
- fetches the latest Amazon Linux 2023 AMI
- creates an EC2 instance of type `t3.micro`
- applies tags such as `Name` and `Environment`

The Jenkins pipeline in `Jenkinsfile` automates the deployment flow:

1. Checks out the source code
2. Runs `terraform init`
3. Runs `terraform plan -out=tfplan`
4. Applies the saved plan using `terraform apply -input=false tfplan`

This approach ensures repeatable provisioning and makes infrastructure changes easy to review and track.

## 3. Dependencies and Setup Instructions

### Prerequisites

Before running this project, ensure the following are available:

- Terraform installed locally (version >= 1.0.0)
- Jenkins installed and configured
- AWS account with valid credentials
- Access to the AWS region `ap-south-1`

### Required Terraform Provider

The configuration uses the AWS Terraform provider:

- `hashicorp/aws` version `~> 5.0`

### AWS Credentials

Jenkins is configured to read AWS access and secret keys from Jenkins credentials named:

- `AWS_ACCESS_KEY`
- `AWS_SECRET_KEY`

These credentials must be created in Jenkins under the credentials manager and granted the necessary permissions to manage EC2 resources.

### IAM Permissions

The AWS principal used by Terraform should have permissions such as:

- EC2:RunInstances
- EC2:DescribeInstances
- EC2:DescribeImages
- EC2:DescribeVpcs
- EC2:DescribeSecurityGroups
- EC2:DescribeSubnets
- EC2:CreateTags

> If required, attach a policy with EC2 administrative access or the minimum set needed for your environment.

## 4. Execution Steps

### Option A: Run Terraform Manually

From the project root, run:

```bash
terraform init
terraform plan
terraform apply
```

To destroy the infrastructure when no longer needed:

```bash
terraform destroy
```

### Option B: Run Through Jenkins

1. Open Jenkins and create or configure a pipeline job for this repository.
2. Ensure the repository is connected to the correct Git branch.
3. Add Jenkins credentials for:
   - `AWS_ACCESS_KEY`
   - `AWS_SECRET_KEY`
4. Trigger the pipeline.
5. Jenkins will execute the stages defined in `Jenkinsfile`:
   - checkout
   - terraform init
   - terraform plan
   - terraform apply

### Expected Result

After a successful deployment, an EC2 instance named `Jenkins-Terraform` will be created in the `ap-south-1` region using an Amazon Linux 2023 AMI and the `t3.micro` instance type.

## Project Files

- `main.tf` — Terraform configuration for the AWS EC2 instance
- `Jenkinsfile` — CI/CD pipeline for automated provisioning
