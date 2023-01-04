[TOC]



# 1. Introduction

## 1.1. IaC 란?


## 1.2. Terraform 이란?


## 1.3. 비교하기


# 2. Installation

## 2.1. Homebrew

## 2.2. tfenv



# 3. 용어 정리

## tfstate

## provider

## resource

## workspace

## variables

## outputs



# 9. Tip && 주의사항

## 9.1. Multiple Provider
> https://developer.hashicorp.com/terraform/language/providers/configuration

동일한 Provider가 여러개 있는 경우, Alias가 정의되지 않은 Provider가 default로 사용된다. 

따라서, 특정 Resource에서 특정 Provider를 사용하고 싶은 경우 `<PROVIDER NAME>.<ALIAS>` 헝태로 provider를 명시하여야 한다. 

```hcl
# The default provider configuration; resources that begin with `aws_` will use
# it as the default, and it can be referenced as `aws`.
provider "aws" {
  region = "us-east-1"
}

# Additional provider configuration for west coast region; resources can
# reference this as `aws.west`.
provider "aws" {
  alias  = "west"
  region = "us-west-2"
}

# Use `us-west-2` region instead of `us-east-1`
resource "aws_instance" "foo" {
  provider = aws.west

  # ...
}
```




