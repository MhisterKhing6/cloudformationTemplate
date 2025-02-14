# CloudFormation Template for IAM Users, Groups, and EventBridge/Lambda Integration

This CloudFormation template creates IAM users, groups, and integrates EventBridge with a Lambda function to log user creation events.

## Parameters

- `S3UserEmail`: Email address for the S3 user.
- `EC2UserEmail`: Email address for the EC2 user.

## Resources

### Secret Manager for One-Time Password

- **OTPSecret**: A secret in AWS Secrets Manager to store the one-time password for all users.

### IAM Groups and Policies

- **S3Group**: An IAM group with read access to S3.
- **S3Policy**: An IAM policy that grants read access to S3 and attaches it to the S3Group.
- **EC2Group**: An IAM group with read access to EC2.
- **EC2Policy**: An IAM policy that grants read access to EC2 and attaches it to the EC2Group.

### IAM Users

- **S3User**: An IAM user that belongs to the S3Group and uses the one-time password from Secrets Manager.
- **EC2User**: An IAM user that belongs to the EC2Group and uses the one-time password from Secrets Manager.

### SSM Parameters

- **S3UserEmailParameter**: Stores the email address of the S3 user in SSM Parameter Store.
- **EC2UserEmailParameter**: Stores the email address of the EC2 user in SSM Parameter Store.

### Lambda Function

- **LambdaFunction**: A Lambda function that logs user creation events. It retrieves the email address from SSM Parameter Store and the one-time password from Secrets Manager.

### Lambda Execution Role

- **LambdaExecutionRole**: An IAM role that the Lambda function assumes. It has permissions to get parameters from SSM, get secrets from Secrets Manager, and write logs to CloudWatch.

### EventBridge Rule

- **EventBridgeRule**: An EventBridge rule that triggers the Lambda function on IAM user creation events.

### Lambda Permission

- **LambdaPermission**: Grants EventBridge permission to invoke the Lambda function.

## Outputs

- **OTPSecretARN**: The ARN of the one-time password secret in Secrets Manager.

## Usage

1. Deploy the CloudFormation stack with the required parameters (`S3UserEmail` and `EC2UserEmail`).
2. The stack will create IAM users, groups, and policies, and set up the EventBridge rule and Lambda function.
3. When a new IAM user is created, the Lambda function will log the event, including the user's email address and one-time password.

## Example

To deploy the stack, use the following AWS CLI command:

```sh
aws cloudformation create-stack --stack-name MyStack --template-body file://cloudformation.yaml --parameters ParameterKey=S3UserEmail,ParameterValue=s3user@example.com ParameterKey=EC2UserEmail,ParameterValue=ec2user@example.com
