Here’s a `README.md` file that explains your CloudFormation template and provides deployment instructions.

---

# PostgreSQL RDS Deployment with AWS CloudFormation and SSM Parameter Store

## Overview

This project demonstrates how to deploy a PostgreSQL RDS instance using an AWS CloudFormation template. The template securely retrieves the master username and password from AWS Systems Manager (SSM) Parameter Store during the deployment. This approach ensures sensitive information like passwords are never hardcoded or exposed within the CloudFormation template.

The template also provides customizable parameters for `DBName`, `DBInstanceIdentifier`, and `MasterUsername` to make the deployment flexible and adaptable for different environments.

## Template Features

- **PostgreSQL RDS Instance**: Creates an RDS instance using PostgreSQL as the database engine.
- **SSM Parameter Store Integration**: Securely retrieves the database username and password from SSM Parameter Store.
- **Customizable Parameters**: Allows customization of the database name (`DBName`), instance identifier (`DBInstanceID`), and other configurations via parameters.

## CloudFormation Template Breakdown

### Parameters

- **DBInstanceID**: 
  - Description: The identifier for the RDS instance.
  - Default: `mydbinstance`.
  - Constraints: Must start with a letter and only contain alphanumeric characters.

- **DBName**: 
  - Description: The name of the PostgreSQL database.
  - Default: `AndreDB`.
  - Constraints: Must be between 1 and 64 characters.

- **DBUsernameSSMParameters**: 
  - Description: The master username for database access, stored securely in SSM Parameter Store.
  - Type: `AWS::SSM::Parameter::Value<String>`.
  - Default: `/Andre/rds/Instancetype`.

- **DBPassword**: 
  - Description: The master password for database access, stored securely in SSM Parameter Store.
  - Type: `AWS::SSM::Parameter::Value<String>`.
  - Default: `/Andre/rds/Instancetype/password`.

### Resources

- **MyDB**: 
  - Creates the PostgreSQL RDS instance using the following properties:
    - `DBInstanceIdentifier`: Set from the `DBInstanceID` parameter.
    - `DBName`: Set from the `DBName` parameter.
    - `AllocatedStorage`: Allocates 20 GB of storage for the database.
    - `DBInstanceClass`: Uses the `db.t3.micro` instance type for a cost-effective solution.
    - `Engine`: Uses PostgreSQL as the database engine.
    - `EngineVersion`: Set to version 15.7 of PostgreSQL.
    - `MasterUsername` and `MasterUserPassword`: Securely retrieved from SSM Parameter Store using `DBUsernameSSMParameters` and `DBPassword`.

## Prerequisites

1. **SSM Parameter Store Entries**: 
   You need to create SSM Parameter Store entries to store your database username and password:
   
   - Store the **master username**:
     - Name: `/Andre/rds/Instancetype`
     - Value: Your chosen master username (e.g., `admin`).
   
   - Store the **master password**:
     - Name: `/Andre/rds/Instancetype/password`
     - Value: Your chosen password (e.g., `MySecurePassword123!`).

2. **AWS IAM Permissions**: 
   Ensure that your IAM user/role has permission to:
   - Create RDS instances.
   - Access SSM Parameter Store to retrieve the stored parameters.

## Deployment Instructions

### Step 1: Store Credentials in SSM Parameter Store

To securely manage the database credentials, create SSM Parameter Store entries for both the username and password:

1. **Store the Master Username**:
   - Navigate to **AWS Systems Manager**.
   - Select **Parameter Store**.
   - Click **Create parameter**.
   - Set **Name** as `/Andre/rds/Instancetype`.
   - Set **Type** to **String**.
   - Enter the **Value** (e.g., `admin`).
   - Click **Create Parameter**.

2. **Store the Master Password**:
   - Repeat the above process, but this time:
   - Set **Name** to `/Andre/rds/Instancetype/password`.
   - Set **Type** to **String**.
   - Enter your secure password (e.g., `MySecurePassword123!`).
   - Click **Create Parameter**.

### Step 2: Deploy the CloudFormation Stack

1. Go to the **AWS CloudFormation** console.
2. Click **Create Stack** and choose **With new resources (standard)**.
3. Upload the CloudFormation template file (e.g., `rds-postgresql-template.yml`).
4. Click **Next**.

### Step 3: Configure Stack Parameters

- **Stack Name**: Provide a name for your stack (e.g., `PostgresRDSStack`).
- **DBInstanceID**: Choose the identifier for your database instance (default: `mydbinstance`).
- **DBName**: Enter the name for your database (default: `AndreDB`).

### Step 4: Review and Create

1. Review your configurations and parameters.
2. Acknowledge any IAM or SSM-related permissions.
3. Click **Create Stack** to start the deployment.

### Step 5: Verify the RDS Instance

Once the stack is successfully created, navigate to the **RDS** section in the AWS Management Console to verify the status of the PostgreSQL instance.

## Clean-Up

To avoid incurring additional costs, make sure to delete the stack when you're done:

1. Go to the **AWS CloudFormation** console.
2. Select your stack.
3. Click **Delete Stack**. This will delete the RDS instance and associated resources.

