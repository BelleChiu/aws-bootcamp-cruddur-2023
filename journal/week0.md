# Week 0 — Billing and Architecture
## Get the AWS CLI Working
We'll be using the AWS CLI often in the bootcamp, so we'll proceed to install this account.

### Install AWS CLI
- We are going to install the AWS CLI when our Gitpod environment lanuches.
- We are going to set AWS CLI to use partial autoprompt mode to make it easier to debug CLI commands.
- The bash commands we are using are the same as the [AWS CLI install Instructions](https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html)

Update our `.gitpod.yml` to include the following task.

```
tasks:
  - name: aws-cli
    env:
      AWS_CLI_AUTO_PROMPT: on-partial
    init: |
      cd /workspace
      curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
      unzip awscliv2.zip
      sudo ./aws/install
      cd $THEIA_WORKSPACE_ROOT

```
We'll also run these commands indivually to perform the install manually.

### Create a new User and Generate AWS Credenitals
- Go to (IAM Users Console)(https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/users)
- `Enable console access` for the user
- Create a new `Admin` Gorup and apply `AdministratorAccess`
- Ceate the user and go find and click into the user
- Click on `Security Credentials` and `Create Access Key`
- Choose AWS CLI Access
- Download the CSV with the credentials

### Set Env Vars
We will set these credentials for the current bash terminal

```
export AWS_ACCESS_KEY_ID=""
export AWS_SECRET_ACCESS_KEY=""
export AWS_DEFAULT_REGION=us-east-1
```
We'll tell Gitpod to remember the these credentials if we relaunch our workspaces

```
gp AWS_ACCESS_KEY_ID=""
gp AWS_SECRET_ACCESS_KEY=""
gp AWS_DEFAULT_REGION=us-east-1
```

### Check that the AWS CLI is working and you are the expected user

```
aws sts get-caller-identity
```

You should see somehting like this:
```
{
    "UserId": "AIDAZI2LEUHXYXXH6RPBW",
    "Account": "637423362543",
    "Arn": "arn:aws:iam::637423362543:user/iamadmin"
}
```

### Enabling Billing
- We need to turn on Billing Alerts to reccieve alerts...
- Under `Billing Preferences` Choose `Receive Billing Alerts`
- Save Preferences

## Create a Billing Alarm
### Create SNS Topic
- We need an SNS topic before we create an alarm
- The SNS topic is what will delivery us an alert when we get overbilled
- [as sns creat topic](https://docs.aws.amazon.com/cli/latest/reference/sns/create-topic.html)

We'll create a SNS Topic

```
aws sns create-topic --name billing-alarm
```
which will return a TopicARN

We'll create a subscription supply the TopicArn and our Email

```
aws sns subscribe \
    --topic-arn TopicARN \
    --protocol email \
    --notification-endpoint your@email.com
```
Check your email and confirm the subscription

### Create Alarm
- [aws cloudwatch put-metric-alarm](https://docs.aws.amazon.com/cli/latest/reference/cloudwatch/put-metric-alarm.html)
- [Create an Alarm via AWS CLI](https://repost.aws/zh-Hant/knowledge-center/cloudwatch-estimatedcharges-alarm)
- We need to update the configuration json script with the TopicARN we generated earlier
- We are just a json file because --metrics is required for expressions and so its easier to us a JSON file.

```
aws cloudwatch put-metric-alarm --cli-input-json file://aws/json/alarm_config.json
```

## Create an AWS Budget
Get your AWS Account ID
```
aws sts get-caller-identity --query Account --output text
```

Set the variable of ACOUNT_ID in env 
```
export ACCOUNT_ID=$(aws sts get-caller-identity --query Account --output text)
gp env AWS_ACCOUNT_ID=""
```

- Supply your AWS Account ID
- Update this json files
- This is another case with AWS CLI its just much easier to json files due to lots of nested json
```
aws budgets create-budget \
    --account-id AccountID \
    --budget file://aws/json/budget.json \
    --notifications-with-subscribers file://aws/json/budget-notifications-with-subscribers.json
```

## AWS Organization and AWS IAM 
### AWS IAM User/Group/Role
- Create AWS IAM User and enable MFA
- Create AWS IAM Group and add the new IAM User into IAM Group
- Assign permissions to the IAM User


### AWS Organization
- Create AWS Organization/AWS OU
- Invitate other aws account to join into AWS Organization 
- Add the other aws account into AWS OU
- Create SCP "deny all S3" and attach SCP to the AWS OU

### Create Lucid Charts for the Cruddur Logical Diagraam
[Curddur Logical Diagram](https://lucid.app/lucidchart/ac6ee166-b10e-44e3-95c2-f693669cb317/edit?invitationId=inv_37bd3262-2c1d-49c5-826d-97d01c94c61a&page=0_0#)