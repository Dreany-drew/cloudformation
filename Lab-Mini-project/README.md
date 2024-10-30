

## README

### Web Server CloudFormation Template

This CloudFormation template is designed to deploy a web server in an existing AWS network. The server is based on an Amazon EC2 instance, running an Apache HTTP server. It automatically configures the instance with an Elastic IP (EIP) for consistent public access.

### Template Overview

- **Instance Configuration**: You can deploy an EC2 instance running Apache web server (`httpd`).
- **Flexible Network Options**: The instance can be deployed into an existing VPC and subnet. If not specified, default VPC and subnet values are used from another stack (assumed to be pre-existing).
- **Elastic IP**: The template assigns an Elastic IP to the web server, ensuring it has a static public IP for external access.
- **SSH Access**: You can specify an existing EC2 KeyPair to allow SSH access to the web server instance.
- **UserData Script**: Automatically installs Apache and serves a basic "Hello World" HTML page.

### Template Parameters

The template includes the following customizable parameters:

- **VpcId**:
  - Description: The ID of the existing Virtual Private Cloud (VPC). If left blank, it uses the default VPC from the `Network_Template`.
  - Type: `AWS::EC2::VPC::Id`
  - Default: `""`
  
- **PublicSubnetId**:
  - Description: The ID of the existing public subnet within the VPC. If left blank, it uses the default public subnet from the `Network_Template`.
  - Type: `AWS::EC2::Subnet::Id`
  - Default: `""`

- **KeyName**:
  - Description: The name of an existing EC2 KeyPair to enable SSH access to the instance.
  - Type: `AWS::EC2::KeyPair::KeyName`
  - Constraint: Can only contain ASCII characters.

- **InstanceType**:
  - Description: The type of EC2 instance for the web server.
  - Type: `String`
  - Default: `t2.small`
  - Allowed Values: 
    - `t1.micro`, `t2.nano`, `t2.micro`, `t2.small`, `t2.medium`, `t2.large`

- **EnvironmentType**:
  - Description: The environment type for the instance (development or production).
  - Type: `String`
  - Default: `Dev`
  - Allowed Values: `Dev`, `Prod`

### Mappings

The template includes a mapping of AMIs for different AWS regions. This ensures the correct Linux AMI is used in each region where the stack is deployed.

### Resources

- **WebServerInstance**:
  - An EC2 instance that runs the Apache HTTP server. It is assigned a security group, subnet, and key pair for SSH access.
  - **UserData** installs Apache, starts the service, and deploys a basic HTML page with "Hello World from Andre webserver".

- **myEIP**:
  - Allocates an Elastic IP address and associates it with the web server instance for consistent public access.

### Deployment Instructions

#### Prerequisites:

1. **VPC & Subnet**: Ensure you have a VPC and subnet available. You can provide the VPC and subnet IDs during deployment, or the template will use defaults from the `Network_Template` stack (assumed to be deployed separately).
2. **EC2 KeyPair**: Create an EC2 KeyPair in your AWS region if you want to enable SSH access to the instance.

#### Deployment via AWS Console:

1. Download or copy the CloudFormation template (`template.yaml`).
2. Navigate to the [AWS CloudFormation Console](https://console.aws.amazon.com/cloudformation/).
3. Click "Create Stack" and select "With new resources (standard)".
4. Upload the template file or paste its content.
5. Specify the required parameters:
   - `VpcId`, `PublicSubnetId` (optional, leave blank to use defaults).
   - `KeyName`: Enter the name of your EC2 KeyPair.
   - `InstanceType` and `EnvironmentType`: Adjust if necessary.
6. Review and create the stack.

#### Deployment via AWS CLI:

Run the following AWS CLI command to deploy the template:

```bash
aws cloudformation create-stack --stack-name WebServerStack \
--template-body file://template.yaml \
--parameters ParameterKey=KeyName,ParameterValue=your-keypair
```

Replace `your-keypair` with your actual EC2 KeyPair name.

#### Post-Deployment:

- After deployment, the web server will be accessible via the allocated Elastic IP. You can access it using a web browser by visiting `http://<Elastic-IP>` to see the "Hello World" page.
- You can SSH into the instance using the KeyPair specified during deployment.

### Additional Notes

- **UserData Script**: The UserData script in the template installs Apache HTTP server and sets up a basic "Hello World" webpage. You can customize this script to fit your needs.
- **Security Groups**: The instance uses a security group that should allow HTTP (port 80) access to ensure the web server is publicly reachable.

