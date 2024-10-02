# A Complete guide to deploy AWS Resource Using GitLab

In this blog, we will explore how to automate AWS resource deployment using GitLab SaaS pipelines and AWS CloudFormation. We will define a pipeline that deploys infrastructure to two separate AWS environments, Dev and Prod, ensuring a consistent deployment process across both environments. The CloudFormation templates define a basic network and an EC2 instance, with parameters customized for each environment using separate parameter files.

## Repository Structure 
The repository structure will now include a parameters folder where JSON files for CloudFormation parameters will be stored:

```
.
├── .gitlab-ci.yml
├── cloudformation/
│   ├── network-stack.yml
│   ├── app-stack.yml
├── parameters/
│   ├── dev-<stake_name>-parameters.json
│   ├── prod-<stack_name>-parameters.json
└── scripts/bash
    ├── deploy-cft.sh
    ├── review-changeset-cft.sh
    ├── create-changeset-cft.sh

```

## AWS Accounts and Environments

- **Dev Account**: Resources are deployed for development purposes.
- **Prod Account**: Resources are deployed after successful testing in the Dev account.

The pipeline is triggered on each branch push, with Dev deployments triggered from the `dev` branch and Prod deployments triggered from the `main` branch.

## GitLab Pipeline Stages

The pipeline consists of four key stages:

1. **Validate**: Validate the syntax and configuration of the CloudFormation templates.
2. **Create Change Set**: Generate change sets for review before deploying the resources.
3. **Review Change Set**: Verify the generated change sets.
4. **Execute Change Set**: Deploy the infrastructure to AWS.

Below is the complete pipeline configuration, CloudFormation templates, and parameter files used in this setup.

## CloudFormation Templates

The project consists of two CloudFormation templates: one for the network infrastructure and one for the application infrastructure. These templates define key resources such as a VPC, subnets, and an EC2 instance.

### Network Stack (network-stack.yml)

The network stack CloudFormation template creates the network infrastructure, including a VPC and a subnet.

- **VpcCidr**: The CIDR block for the VPC.
- **SubnetCidr**: The CIDR block for the subnet.
- **Resources**: Creates the VPC and Subnet.
- **Outputs**: The subnet ID is exported for use in other CloudFormation templates.

### Application Stack (app-stack.yml)

The application stack CloudFormation template defines an EC2 instance. The instance is launched in the subnet created by the network stack.

- **InstanceType**: The type of EC2 instance (e.g., `t2.micro`).
- **KeyName**: The SSH KeyPair name for EC2 access.
- **ImageId**: The Amazon Machine Image (AMI) ID for the EC2 instance.

## Parameter Files

Separate parameter files are used for the Dev and Prod environments. This allows different configurations for each environment while using the same CloudFormation templates.

### Dev Environment Parameters

- **File**: `dev-app-stack-parameters.json` and `dev-network-stack-parameters.json`
- **Content**: Contains environment-specific values such as the instance type, VPC CIDR, and Subnet CIDR for the Dev environment.

### Prod Environment Parameters

- **File**: `prod-app-stack-parameters.json` and `prod-network-stack-parameters.json`
- **Content**: Contains environment-specific values for the Prod environment, ensuring that the production infrastructure is configured differently than Dev.

---

By leveraging GitLab CI/CD pipelines, CloudFormation, and parameter files, this setup ensures that AWS infrastructure is deployed consistently across both Dev and Prod environments. The use of parameter files allows customization for each environment, while the change set process provides an additional layer of review before executing any infrastructure changes.

