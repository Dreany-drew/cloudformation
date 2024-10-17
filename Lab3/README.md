Here’s a `README.md` file that explains your CloudFormation template and deployment instructions.

---

# Launching a Windows EC2 Instance with CloudFormation and Dynamic AMI ID

## Overview

This project demonstrates how to deploy a Windows EC2 instance using an AWS CloudFormation template. Instead of hardcoding the Amazon Machine Image (AMI) ID, the template leverages the AWS Systems Manager (SSM) Parameter Store to dynamically retrieve the latest AMI ID for Windows Server. This ensures that your CloudFormation template always uses the most up-to-date AMI for the selected Windows Server version.

The template allows for customization through parameters such as the VPC ID, subnet, EC2 instance type, and key pair. It also creates a security group to enable RDP access.

## Features

- **Dynamic AMI Selection**: Uses the SSM Parameter Store to retrieve the latest Windows Server AMI ID for EC2 instances, making the template future-proof.
- **Customizable Parameters**: Allows you to specify the VPC ID, subnet, EC2 instance type, and key pair.
- **RDP Access**: Sets up a security group to allow Remote Desktop Protocol (RDP) access to the instance (port 3389).
- **Tagging**: Automatically tags the instance with the name `Andreserver` and department `IT`.

## CloudFormation Template Breakdown

### Parameters

- **VpcId**: 
  - Description: The ID of the Virtual Private Cloud (VPC) where the EC2 instance will be launched.
  - Type: `AWS::EC2::VPC::Id`.
  
- **PublicSubnetId**: 
  - Description: The ID of a public subnet within the specified VPC. The instance will be launched in this subnet.
  - Type: `AWS::EC2::Subnet::Id`.

- **KeyName**: 
  - Description: The name of an existing EC2 KeyPair to enable SSH access to the instance.
  - Type: `AWS::EC2::KeyPair::KeyName`.

- **InstanceTypeParameter**: 
  - Description: Allows you to specify the EC2 instance type (default: `t2.micro`).
  - Type: `String`.
  - Allowed values: `t2.micro`, `m1.small`, `m1.large`.

- **SSMParameterwindowAMIId**: 
  - Description: The SSM Parameter Store name that stores the latest Windows Server AMI ID.
  - Default: `/aws/service/ami-windows-latest/EC2LaunchV2-Windows_Server-2016-English-Full-Base`.

### Resources

- **InstanceSecurityGroup**: 
  - Creates a security group that allows inbound RDP traffic (port 3389) from any IP address.
  - VPC ID: The VPC where the security group is created.

- **WindowEc2Instance**: 
  - Launches a Windows EC2 instance using the dynamically retrieved AMI ID.
  - The instance will be tagged as:
    - Name: `Andreserver`
    - Department: `IT`
  - The EC2 instance will have:
    - A public IP address associated with it.
    - A security group allowing RDP access.

## How It Works

1. **Dynamic AMI Retrieval**: The AMI ID is retrieved dynamically using the AWS Systems Manager (SSM) Parameter Store. The template uses the parameter `/aws/service/ami-windows-latest/EC2LaunchV2-Windows_Server-2016-English-Full-Base`, which always points to the latest Windows Server 2016 AMI. You can modify this to target other Windows versions by changing the SSM parameter.

2. **Instance Configuration**: 
   - The instance type (e.g., `t2.micro`) is selected through a parameter, allowing flexibility in resource allocation.
   - The EC2 instance is placed in a specific VPC and subnet, and it is assigned a public IP address to allow remote access.

3. **RDP Access**: A security group is automatically created and assigned to the instance to allow RDP access over port 3389. By default, access is open to all IPs (0.0.0.0/0), but you should restrict this to your specific IP range in a production environment.

## Prerequisites

Before launching this CloudFormation stack, ensure you have the following:

1. **VPC and Subnet**: The IDs of an existing VPC and a public subnet where the EC2 instance will be deployed.
2. **EC2 KeyPair**: The name of an existing EC2 KeyPair that you will use to access the instance via RDP.
3. **IAM Permissions**: Ensure your IAM user or role has the necessary permissions to create EC2 instances, security groups, and read from SSM Parameter Store.

## Deployment Instructions

### Step 1: Retrieve Required Values

- **VPC ID**: Get the ID of the VPC where you want to launch the EC2 instance.
- **Subnet ID**: Get the ID of the public subnet within that VPC.
- **Key Pair**: Ensure you have an EC2 KeyPair created to allow RDP access to the instance.

### Step 2: Deploy the CloudFormation Stack

1. Open the **AWS CloudFormation** console.
2. Click **Create Stack** and choose **With new resources (standard)**.
3. Upload the CloudFormation template file (`windows-ec2-template.yml`).
4. Click **Next**.

### Step 3: Configure Stack Parameters

Fill out the required parameters:

- **VpcId**: Enter your VPC ID.
- **PublicSubnetId**: Enter your public subnet ID.
- **KeyName**: Enter the name of your existing EC2 KeyPair.
- **InstanceTypeParameter**: Choose the instance type (default: `t2.micro`).
- **SSMParameterwindowAMIId**: Use the default value to retrieve the latest Windows Server 2016 AMI.

### Step 4: Review and Create Stack

1. Review your configurations.
2. Acknowledge the creation of IAM resources if prompted.
3. Click **Create Stack** to deploy the Windows EC2 instance.

### Step 5: Connect to Your Instance

Once the stack creation is complete:

1. Navigate to the **EC2** console.
2. Select the newly launched Windows EC2 instance.
3. Click **Connect** and follow the instructions to connect via RDP using your KeyPair.

## Clean-Up

To avoid ongoing charges, make sure to delete the CloudFormation stack when you're done:

1. Go to the **CloudFormation** console.
2. Select your stack.
3. Click **Delete Stack**.

This will delete the EC2 instance and all associated resources.

