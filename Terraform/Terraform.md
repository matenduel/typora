[TOC]



# 1. Introduction

## 1.1. IaC 란?

코드형 인프라(Infrastructure as Code, IaC)는 수동 프로세스가 아닌 코드를 통해 인프라를 관리하고 프로비저닝하는 것을 말합니다.

IaC를 사용하면 인프라 사양을 담은 구성 파일이 생성되므로 구성을 편집하고 배포하기가 더 쉬워집니다. 또한 IaC는 매번 동일한 환경을 프로비저닝하도록 보장합니다. IaC는 구성 사양을 코드화하고 문서화함으로써 [구성 관리](https://www.redhat.com/ko/topics/automation/what-is-configuration-management)를 지원하며, 따라서 구성 변경 사항을 문서화하지 않고 임시로 변경하는 일을 막을 수 있습니다.

버전 제어는 IaC의 중요한 부분입니다. 다른 소프트웨어 소스 코드 파일과 마찬가지로 구성 파일도 소스 제어가 필요합니다. 코드로 인프라를 배포한다는 것은 인프라를 모듈식 구성 요소로 분할하고 자동화를 통해 다양한 방식으로 결합할 수 있다는 뜻이기도 합니다.

IaC로 [인프라 프로비저닝](https://www.redhat.com/ko/topics/automation/what-is-provisioning)을 자동화하면 애플리케이션을 개발하거나 배포할 때마다 개발자가 직접 서버, 운영 체제, 스토리지, 기타 인프라 구성 요소를 수동으로 프로비저닝하고 관리할 필요가 없어집니다. 인프라를 코드화하여 템플릿을 만들고 프로비저닝할 때 이 템플릿을 사용하면 됩니다. 이러한 작업은 수동으로 진행할 수도 있고 [Red HatⓇ Ansible® Automation Platform](https://www.redhat.com/ko/technologies/management/ansible)과 같은 자동화 툴을 사용할 수도 있습니다. 



### 선언형 Vs. 절차형(명령형)

IaC에 대한 접근 방식에는 선언적 방식과 명령형 방식 두 가지가 있습니다. 

선언적 접근 방식에서는 필요한 리소스와 리소스의 속성 등 바람직한 시스템 상태를 정의하면 IaC 툴이 바람직한 상태로 구성해 줍니다. 

또한 선언적 접근 방식에서는 시스템 오브젝트의 현재 상태 목록을 유지하며, 이를 통해 인프라를 더 쉽게 관리할 수 있습니다.

한편 명령적 접근 방식에서는 바람직한 구성을 얻기 위한 특정 명령을 정의하며, 정의된 명령을 올바른 순서로 실행해야 합니다. 

많은 IaC 툴이 선언적 접근 방식에 따라 원하는 인프라를 자동으로 프로비저닝합니다. 원하는 상태를 변경하면 선언적 IaC 툴은 그러한 변경 사항을 적용합니다. 명령형 접근 방식의 툴을 사용하려면 변경 사항을 어떻게 적용하는지 이해해야 합니다.

보통 IaC 툴은 두 가지 방식을 모두 사용하지만, 둘 중 하나를 더 선호하는 경향이 있습니다.

## 1.2. Terraform 이란?

HashiCorp Terraform is an infrastructure as code tool that lets you define both cloud and on-prem resources in human-readable configuration files that you can version, reuse, and share. You can then use a consistent workflow to provision and manage all of your infrastructure throughout its lifecycle. Terraform can manage low-level components like compute, storage, and networking resources, as well as high-level components like DNS entries and SaaS features.



### 작동방식

Terraform creates and manages resources on cloud platforms and other services through their application programming interfaces (APIs). Providers enable Terraform to work with virtually any platform or service with an accessible API.

![how_it_works](Terraform.assets/how_it_works.PNG)

The core Terraform workflow consists of three stages:

- **Write:** You define resources, which may be across multiple cloud providers and services. For example, you might create a configuration to deploy an application on virtual machines in a Virtual Private Cloud (VPC) network with security groups and a load balancer.
- **Plan:** Terraform creates an execution plan describing the infrastructure it will create, update, or destroy based on the existing infrastructure and your configuration.
- **Apply:** On approval, Terraform performs the proposed operations in the correct order, respecting any resource dependencies. For example, if you update the properties of a VPC and change the number of virtual machines in that VPC, Terraform will recreate the VPC before scaling the virtual machines.

![workflow](Terraform.assets/workflow.PNG)




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



# 4. CLI 사용법

## 4.1. init



## 4.2. plan



## 4.3. apply



## 4.4. destroy



## 4.5. workspace 





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



