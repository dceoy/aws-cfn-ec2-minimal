# aws-cfn-ec2-windows

AWS CloudFormation template for Windows Server on Amazon EC2

## Overview

This repository provides a CloudFormation template that launches a Windows Server 2022 EC2 instance pre-configured for [MetaTrader 5](https://www.metatrader5.com/). The template handles networking (security group with RDP access), IAM (SSM-managed instance role), and automated MetaTrader 5 installation via a PowerShell UserData script.

## Template

| File | Description |
|------|-------------|
| `ec2-windows-mt5.cfn.yaml` | Windows Server 2022 EC2 instance with MetaTrader 5 |

## Parameters

| Parameter | Default | Description |
|-----------|---------|-------------|
| `ProjectName` | `mt5` | Project name used for resource tagging |
| `VpcId` | *(required)* | VPC ID where the instance will be launched |
| `SubnetId` | *(required)* | Subnet ID for the instance |
| `InstanceType` | `t3.large` | EC2 instance type |
| `WindowsAmiId` | Latest Windows Server 2022 | SSM parameter for the AMI ID |
| `KeyPairName` | *(empty)* | EC2 key pair for password decryption |
| `RdpCidr` | `0.0.0.0/0` | CIDR block allowed for RDP access |
| `VolumeSize` | `100` | Root EBS volume size in GiB |
| `VolumeType` | `gp3` | Root EBS volume type |

## Usage

```bash
aws cloudformation deploy \
  --template-file ec2-windows-mt5.cfn.yaml \
  --stack-name mt5-windows \
  --parameter-overrides \
    VpcId=vpc-xxxxxxxx \
    SubnetId=subnet-xxxxxxxx \
    KeyPairName=my-key-pair \
    RdpCidr=203.0.113.1/32 \
  --capabilities CAPABILITY_NAMED_IAM
```

After the stack is created, retrieve the administrator password using the EC2 console or the AWS CLI:

```bash
aws ec2 get-password-data \
  --instance-id <InstanceId> \
  --priv-launch-key my-key-pair.pem
```

Connect to the instance via RDP and MetaTrader 5 will be available on the desktop.
