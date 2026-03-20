# aws-cfn-ec2-windows

AWS CloudFormation template for Windows Server on Amazon EC2

## Overview

This repository provides a CloudFormation template that launches a Windows Server 2022 EC2 instance pre-configured for [MetaTrader 5](https://www.metatrader5.com/). The instance is accessible via [AWS Systems Manager Session Manager](https://docs.aws.amazon.com/systems-manager/latest/userguide/session-manager.html) — no RDP or key pair required. The template sets up networking (internet gateway, egress-only internet gateway, route table, security group), IAM (SSM-managed instance role), and automated software installation via a PowerShell UserData script. It installs [winget](https://github.com/microsoft/winget-cli) (Windows Package Manager) and uses it to provision the following tools:

- Windows Terminal
- PowerShell
- Vim
- Visual Studio Code
- PowerToys
- Git
- uv
- Google Chrome

## Template

| File                  | Description                                        |
| --------------------- | -------------------------------------------------- |
| `ec2-windows.cfn.yml` | Windows Server 2022 EC2 instance with MetaTrader 5 |

## Parameters

| Parameter          | Default                    | Description                                      |
| ------------------ | -------------------------- | ------------------------------------------------ |
| `SystemName`       | `win`                      | System name used for resource naming and tagging |
| `EnvType`          | `dev`                      | Environment type used for resource naming and tagging |
| `Ec2VpcId`         | _(required)_               | VPC ID where the instance will be launched       |
| `Ec2SubnetId`      | _(required)_               | Subnet ID for the instance                       |
| `Ec2InstanceType`  | `t3.large`                 | EC2 instance type                                |
| `Ec2WindowsAmiId`  | Latest Windows Server 2022 | SSM parameter for the AMI ID                     |
| `Ec2VolumeSize`    | `100`                      | Root EBS volume size in GiB                      |
| `Ec2VolumeType`    | `gp3`                      | Root EBS volume type                             |

## Usage

```bash
aws cloudformation deploy \
  --template-file ec2-windows.cfn.yml \
  --stack-name win-dev-windows \
  --parameter-overrides \
    Ec2VpcId=vpc-xxxxxxxx \
    Ec2SubnetId=subnet-xxxxxxxx \
  --capabilities CAPABILITY_NAMED_IAM
```

After the stack is created, start a Session Manager session using the AWS CLI:

```bash
aws ssm start-session --target <Ec2InstanceId>
```

MetaTrader 5 will be available on the desktop.
