# Terraform Workbook



# Basic

## Configuration

```
terraform {
  required_version = "~> 1.3.6" # 사용할 Terraform 버젼
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.49.0"
    }
    kubernetes = {
      source  = "hashicorp/kubernetes"
      version = "~> 2.16.1"
    }
    helm = {
      source  = "hashicorp/helm"
      version = "~> 2.8.0"
    }
  }
}

```





## Remote Backend 설정하기

### S3

```
```





### Terraform Cloud

```
terraform {
  cloud {
    organization = "my-org-iac"

    workspaces {
      name = "aws-infra"
    }
  }
}

```





## Variables

### variables.tf 생성하기

```

variable "terraform-aws-data-access-key" {
  description = "aws access key"
}


variable "env-postfix" {
  description = "Run Environment (dev | prod)"
  default     = "dev"
  type        = string
}

```





### tf.vars 사용하기





## Provider

### AWS













# AWS

## VPC



```
resource "aws_vpc" "first-vpc" {
  cidr_block           = var.vpc-cidr
  enable_dns_support   = true
  enable_dns_hostnames = true
  tags = {
    Name = "FIRST-VPC"
  }
}
```



## Subnets

Subnet 1개 만들어보기 

```
ddd
```



위에서 생성한 VPC의 attributes를 이용하기



Count를 이용해서 3개 생성해보기

```
resource "aws_subnet" "subnet-data-public" {
  count = length(var.subnet-cidrs-public)

  vpc_id            = aws_vpc.first-vpc.id
  cidr_block        = var.subnet-cidrs-public[count.index]
  tags = {
    Name = "SUBNET-PUBLIC-${var.subnet-cidrs-public[count.index]}"
  }
}
```





## Problem

- seoul region에 subnet 6개 만들기
- 













