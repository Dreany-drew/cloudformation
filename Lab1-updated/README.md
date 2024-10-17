Here’s a `README.md` file that explains your CloudFormation template and provides context on how it works:

---

# CloudFormation EC2 Instance with Dynamic AMI Selection

## Objective

The purpose of this project is to demonstrate the deployment of an Amazon EC2 instance using an AWS CloudFormation template that dynamically selects the appropriate Amazon Machine Image (AMI) based on the AWS region in which the stack is deployed. This is achieved using the **Mappings** feature of CloudFormation to define AMI IDs for different regions.

By using this approach, you can deploy the same CloudFormation stack across multiple regions without hardcoding the AMI ID for each region, making the template flexible and reusable.

## CloudFormation Template Overview

### Mappings

The **Mappings** section contains a mapping of AMI IDs for various AWS regions. This allows the template to automatically select the correct AMI based on the region where the stack is being deployed. In this case, the AMI IDs for regions such as `us-east-1`, `us-west-1`, and others are specified.

Here’s a sample of the mapping:

```yaml
Mappings:
  RegionBasedAMIMapping:
    us-east-1:
      MyAMI18: ami-0ff8a91507f77f867
    us-west-1:
      MyAMI18: ami-0bdb828fd58c52235
    eu-west-1:
      MyAMI18: ami-047bb4163c506cd98
    ap-southeast-1:
      MyAMI18: ami-08569b978cc4dfa10
    ap-northeast-1:
      MyAMI18: ami-06cd52961ce9f0d85
```

### Parameters

The template defines a parameter, `InstanceTypeParameter`, which allows the user to choose the type of EC2 instance to be deployed. The allowed values are `t2.micro`, `m1.small`, and `m1.large`, with a default value of `t2.micro`.

```yaml
Parameters:
  InstanceTypeParameter:
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - m1.small
      - m1.large
```

### Resources

The **Resources** section defines the EC2 instance that will be created. The `ImageId` property is dynamically populated using the `FindInMap` function, which looks up the appropriate AMI ID from the mapping based on the region. The `InstanceType` is set using the value of the `InstanceTypeParameter`.

Additionally, tags are added to the EC2 instance to provide metadata, such as the name of the instance (`Andre-webserver`) and the department (`IT`).

```yaml
Resources:
  myEC2Instance:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: !FindInMap [RegionBasedAMIMapping, !Ref AWS::Region, MyAMI18]
      InstanceType: !Ref InstanceTypeParameter
      Tags:
        - Key: Name
          Value: Andre-webserver
        - Key: Department
          Value: IT
```

## Key Concepts

1. **Mappings and FindInMap:**
   - The `Mappings` feature is used to define AMI IDs for different AWS regions.
   - The `FindInMap` function dynamically retrieves the correct AMI based on the region where the CloudFormation stack is deployed.

2. **Parameters:**
   - The template includes a parameter for the EC2 instance type, allowing flexibility in choosing the instance size during deployment.

3. **Resources:**
   - The EC2 instance is created with the correct AMI and instance type, and additional tags are added for organization.

## How to Deploy

### Step 1: Create a CloudFormation Stack

1. Go to the AWS Management Console.
2. Navigate to the **CloudFormation** service.
3. Choose **Create Stack** and select **With new resources (standard)**.
4. Upload the CloudFormation template file (e.g., `ec2-dynamic-ami.yml`).
5. Choose **Next**.

### Step 2: Configure Stack Parameters

- **Stack Name**: Enter a name for your stack (e.g., `EC2DynamicAMIStack`).
- **Instance Type**: Choose the instance type (`t2.micro`, `m1.small`, or `m1.large`).
  
### Step 3: Deploy and Monitor

1. Click **Next** to review your settings, then **Create Stack**.
2. After the stack is created, you can view the EC2 instance in the **EC2** section of the AWS Console.
3. The instance will be running with the correct AMI based on the region where the stack was deployed.

### Step 4: Clean-Up

To avoid incurring ongoing costs, be sure to delete the CloudFormation stack once you're done:

1. Go to the **CloudFormation** console.
2. Select your stack.
3. Click **Delete Stack**.

