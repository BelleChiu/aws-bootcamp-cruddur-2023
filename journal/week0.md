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