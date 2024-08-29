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