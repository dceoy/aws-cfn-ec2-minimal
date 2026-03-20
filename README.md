# aws-cfn-ec2-windows

AWS CloudFormation template for Windows Server on Amazon EC2

## Overview

This repository provides a CloudFormation template that launches a Windows Server 2022 EC2 instance pre-configured for [MetaTrader 5](https://www.metatrader5.com/). The template handles networking (security group with RDP access, egress-only internet gateway), IAM (SSM-managed instance role), and automated software installation via a PowerShell UserData script. It installs [winget](https://github.com/microsoft/winget-cli) (Windows Package Manager) and uses it to provision the following tools:

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

| Parameter        | Default                    | Description                                    |
| ---------------- | -------------------------- | ---------------------------------------------- |
| `SystemName`     | `win`                      | System name used for resource naming and tagging |
| `EnvType`        | `dev`                      | Environment type used for resource naming and tagging |
| `Ec2VpcId`       | _(required)_               | VPC ID where the instance will be launched     |
| `Ec2SubnetId`    | _(required)_               | Subnet ID for the instance                     |
| `Ec2InstanceType` | `t3.large`                | EC2 instance type                              |
| `Ec2WindowsAmiId` | Latest Windows Server 2022 | SSM parameter for the AMI ID                  |
| `Ec2KeyPairName` | _(required)_               | EC2 key pair for password decryption           |
| `Ec2RdpCidr`     | _(required)_               | CIDR block allowed for RDP access              |
| `Ec2VolumeSize`  | `100`                      | Root EBS volume size in GiB                    |
| `Ec2VolumeType`  | `gp3`                      | Root EBS volume type                           |

## Usage

```bash
aws cloudformation deploy \
  --template-file ec2-windows.cfn.yml \
  --stack-name win-dev-windows \
  --parameter-overrides \
    Ec2VpcId=vpc-xxxxxxxx \
    Ec2SubnetId=subnet-xxxxxxxx \
    Ec2KeyPairName=my-key-pair \
    Ec2RdpCidr=203.0.113.1/32 \
  --capabilities CAPABILITY_NAMED_IAM
```

After the stack is created, retrieve the administrator password using the EC2 console or the AWS CLI:

```bash
aws ec2 get-password-data \
  --instance-id <Ec2InstanceId> \
  --priv-launch-key my-key-pair.pem
```

Connect to the instance via RDP and MetaTrader 5 will be available on the desktop.
