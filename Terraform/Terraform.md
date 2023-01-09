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




# 2. 설치 방법

## 2.1. Homebrew

```shell
brew install terraform
```

현재 사용가능한 Terraform의 최신 버젼이 설치된다. 



## 2.2. tfenv

```sh
# tfenv 설치
brew install tfenv

# 설치가능한 테라폼 버전 목록 보기
tfenv list-remote

# terraform 설치
tfenv use <Terraform_Version>
```

각 Project의 상황에 맞춰서 Terraform 버젼을 다르게 사용할 수 있다. 



## 2.3. Shell Tab-completion

```shell
terraform -install-autocomplete
```



# 3. 용어 정리

## Initializing

새로운 Terraform 프로젝트나 기존의 Version Control(git, ...)에서 복사해온 Terraform을 실행하기 전 Provider, Module등을 다운로드하는 과정을 의미한다. 



## workspace



## Module

통적으로 활용할 수 있는 인프라 코드를 한 곳으로 모아서 정의하는 부분입니다. Module을 사용하면 변수만 바꿔서 동일한 리소스를 손쉽게 생성할 수 있다는 장점이 있습니다.



## tfstate



## provider

Terraform으로 정의할 Infrastructure Provider를 의미합니다.



```hcl
provider "aws" {
  alias = "balaan-vpc"
  region  = var.balaan-vpc-region
  shared_config_files = ["$HOME/.aws/config"]
  shared_credentials_files = ["$HOME/.aws/credentials"]
  profile = var.balaan-vpc-peer-profile
}
```



## resource

실제로 생성할 인프라 자원을 의미합니다



```hcl
resource "aws_vpc" "vpc-example" {
  cidr_block = var.data-vpc-eks-cidr
  enable_dns_support = true
  enable_dns_hostnames = true
  tags = {
    Name = "VPC-${var.env-prefix}-EKS"
    "kubernetes.io/cluster/${var.cluster-name}" = "shared"
  }
}
```





## Variables 목록



### Input variables

input 변수는 사용자의 입력을 받을 수 있는 변수 입니다. input 변수를 정의 할때 default 필드가 존재하지 않는 경우, 사용자에게 직접 입력을 받는 프롬프트를 발생시켜서 값을 받을 수 있습니다. 예를 들어 string 타입의 변수를 사용자에게 입력받아야 하는 경우, 다음과 같이 variable을 정의할 수 있습니다.

**Example**

```go
variable "env-prefix" {
  description = "Run Environment (DEV-DATA | STAGE-DATA | PROD-DATA)"
  default = "DEV-DATA"
  type = string
}
```

#### Validation

> **Note:** Input variable validation is available in Terraform v0.13.0 and later.
>
> https://developer.hashicorp.com/terraform/language/expressions/custom-conditions#input-variable-validation

```
variable "image_id" {
  type        = string
  description = "The id of the machine image (AMI) to use for the server."

  validation {
    condition     = length(var.image_id) > 4 && substr(var.image_id, 0, 4) == "ami-"
    error_message = "The image_id value must be a valid AMI id, starting with \"ami-\"."
  }
}
```





### data

Data source는 이미 생성되어있는 리소스를 가져와서 변수로 저장할때 사용됩니다. 예를들어 아래 예시와 같이 원하는 filter값을 설정하여 ami정보를 가져올 수 있습니다.

data 사용은 `data.<TYPE>.<NAME>.<ATTRIBUTE>` 과 같이 사용할 수 있습니다.

#### Example

```
# Get caller identity
data "aws_caller_identity" "balaan-vpc" {
  provider = aws.balaan-vpc
}

# Find the latest available AMI that is tagged with Component = web
data "aws_ami" "web" {
  filter {
    name   = "state"
    values = ["available"]
  }

  filter {
    name   = "tag:Component"
    values = ["web"]
  }

  most_recent = true
}

# Use Data source
resource "aws_instance" "web" {
  ami           = data.aws_ami.web.id
  instance_type = "t1.micro"
}
```





### locals

local 변수는 현재 실행 파일에서 사용되는 지역 변수 입니다. 주로 특정 값들을 연산하여 하나의 변수로 만들어야 할때 사용 됩니다. 아래와 같이 merge, concat, max 와 같은 함수를 사용하여 변수를 만들 수 있습니다.

#### Example

```hcl
locals {
    tags = merge(var.tags1, var.tags2)

    instance_ids = concat(aws_instance.blue.*.id, aws_instance.green.*.id)

    max_subnet_length = max(
    length(var.public_subnets),
    length(var.private_subnets)
    )
}
```



### outputs

프라를 프로비저닝 한 후에 생성된 자원을 output 부분으로 뽑을 수 있습니다. Output으로 추출한 부분은 이후에 `remote state`에서 활용할 수 있습니다.

output 변수는 terraform 수행 후 결과를 사용자에게 출력 해주는 변수 입니다. output 변수로 정의한 값은 외부로 노출 시켜주어 모듈간 리소스를 참고할 수 있도록 해줍니다.

모듈A에서 만든 aws_vpc 정보를 모듈B에서 사용해야할때 모듈A에 output을 정의하면 해당 output 값이 state 파일에 기록이 되며 모듈B에서는 다음과 같이 값을 사용할 수 있습니다. `moodule.<MODULE NAME>.<OUTPUT NAME>`

#### Example

```hcl
output "instance_ip_addr" {
  value = aws_instance.server.private_ip
}

# TODO 참조 예시 
```







## Backend (Remote Backend)

terraform의 상태를 저장할 공간을 지정하는 부분입니다. backend를 사용하면 현재 배포된 최신 상태를 외부에 저장하기 때문에 다른 사람과의 협업이 가능합니다. 가장 대표적으로는 AWS S3가 있습니다.

remote state를 사용하면 VPC, IAM 등과 같은 공용 서비스를 다른 서비스에서 참조할 수 있습니다. tfstate파일(최신 테라폼 상태정보)이 저장되어 있는 backend 정보를 명시하면, terraform이 해당 backend에서 output 정보들을 가져옵니다.





# 4. CLI 사용법

### Options

```tex
# Global
-chdir=DIR
Switch to a different working directory before executing the given subcommand.

# Local

```



## 4.1. init

> Prepare your working directory for other commands

The `terraform init` command initializes a working directory containing Terraform configuration files. This is the first command that should be run after writing a new Terraform configuration or cloning an existing one from version control. It is safe to run this command multiple times.



- 지정한 backend에 상태 저장을 위한 `.tfstate` 파일을 생성합니다. 여기에는 가장 마지막에 적용한 테라폼 내역이 저장됩니다.
- init 작업을 완료하면, local에는 `.tfstate`에 정의된 내용을 담은 `.terraform` 파일이 생성됩니다.
- 기존에 다른 개발자가 이미 `.tfstate`에 인프라를 정의해 놓은 것이 있다면, 다른 개발자는 init작업을 통해서 local에 sync를 맞출 수 있습니다.



* 멱등성이 보장되므로 `Provider`나 `Configuration`이 수정된 것이 아니라면 여러번 `init`을 수행하더라도 동일한 상태를 보장한다. (Like pip install)



## 4.2. plan

> Show changes required by the current configuration



- 정의한 코드가 어떤 인프라를 만들게 되는지 미리 예측 결과를 보여줍니다. 단, plan을 한 내용에 에러가 없다고 하더라도, 실제 적용되었을 때는 에러가 발생할 수 있습니다.
- **Plan 명령어는 어떠한 형상에도 변화를 주지 않습니다.**



## 4.3. apply

> Create or update infrastructure

- 실제로 인프라를 배포하기 위한 명령어입니다. apply를 완료하면, AWS 상에 실제로 해당 인프라가 생성되고 작업 결과가 backend의 `.tfstate` 파일에 저장됩니다.
- 해당 결과는 local의 `.terraform` 파일에도 저장됩니다.



## 4.4. destroy

> Destroy previously-created infrastructure



## 4.5. workspace 

> Workspace management

```shell 
terraform workspace <subcommand> [options] [args]
```

### 4.5.1. list

> The command will list all existing workspaces. The current workspace is indicated using an asterisk (`*`) marker.

```shell
terraform workspace list [DIR]

# Example 
$ terraform workspace list
  default
* development
  jsmith-test
```

### 4.5.2. select

> This command will select another workspace. The named workspace must already exist.

```shell
terraform workspace select NAME [DIR]

# Example 
$ terraform workspace select default
Switched to workspace "default".
```

### 4.5.3. new

> This command will create a new workspace with the given name. A workspace with this name must not already exist.

```shell
terraform workspace new [OPTIONS] NAME [DIR]

# Example
$ terraform workspace new -state=old.terraform.tfstate example
Created and switched to workspace "example"!

You're now on a new, empty workspace. Workspaces isolate their state,
so if you run "terraform plan" Terraform will not see any existing state
for this configuration.
```

### 4.5.4. delete

> This command will delete the specified workspace.

```shell
terraform workspace delete [OPTIONS] NAME [DIR]

# Example 
$ terraform workspace delete example
Deleted workspace "example".
```

### 4.5.5. show

> The command will display the current workspace.

```shell
terraform workspace show

# Example 
$ terraform workspace show
development
```



## 4.6. Provider

> Show the providers required for this configuration



## 4.7. refresh

> Update the state to match remote systems



## 4.8. output

> Show output values from your root module



## 4.9. import

> Associate existing infrastructure with a Terraform resource

- AWS 인프라에 배포된 리소스를 `terraform state`로 옮겨주는 작업입니다.
- 이는 local의 .terraform에 해당 리소스의 상태 정보를 저장해주는 역할을 합니다. (절대 코드를 생성해주지 않습니다.)
  - Apply 전까지는 backend에 저장되지 않습니다.
  - Import 이후에 plan을 하면 로컬에 해당 코드가 없기 때문에 리소스가 삭제 또는 변경된다는 결과를 보여줍니다. 이 결과를 바탕으로 코드를 작성하실 수 있습니다.

만약 기존에 인프라를 AWS에 배포한 상태에서 테라폼을 적용하고 싶으면 모든 리소스를 `terraform import`로 옮겨야 합니다. 번거로운 경우에는 처음부터 다시 작업해서 리소스를 올릴 수 있지만, 실제 서비스가 되는 인프라를 내리는 건 위험할 수 있습니다.



## 4.10. graph

> Generate a Graphviz graph of the steps in an operation



# 5. Expressions (문법?)

> https://developer.hashicorp.com/terraform/language/expressions



## Operators

### Arithmetic Operators

The arithmetic operators all expect number values and produce number values as results:

- [`a + b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b) returns the result of adding `a` and `b` together.
- [`a - b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-1) returns the result of subtracting `b` from `a`.
- [`a * b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-2) returns the result of multiplying `a` and `b`.
- [`a / b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-3) returns the result of dividing `a` by `b`.
- [`a % b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-4) returns the remainder of dividing `a` by `b`. This operator is generally useful only when used with whole numbers.
- [`-a`](https://developer.hashicorp.com/terraform/language/expressions/operators#a) returns the result of multiplying `a` by `-1`.

Terraform supports some other less-common numeric operations as [functions](https://developer.hashicorp.com/terraform/language/expressions/function-calls). For example, you can calculate exponents using [the `pow` function](https://developer.hashicorp.com/terraform/language/functions/pow).

### Equality Operators

The equality operators both take two values of any type and produce boolean values as results.

- [`a == b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-5) returns `true` if `a` and `b` both have the same type and the same value, or `false` otherwise.
- [`a != b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-6) is the opposite of `a == b`.

Because the equality operators require both arguments to be of exactly the same type in order to decide equality, we recommend using these operators only with values of primitive types or using explicit type conversion functions to indicate which type you are intending to use for comparison.

Comparisons between structural types may produce surprising results if you are not sure about the types of each of the arguments. For example, `var.list == []` may seem like it would return `true` if `var.list` were an empty list, but `[]` actually builds a value of type `tuple([])` and so the two values can never match. In this situation it's often clearer to write `length(var.list) == 0` instead.

### Comparison Operators

The comparison operators all expect number values and produce boolean values as results.

- [`a < b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-7) returns `true` if `a` is less than `b`, or `false` otherwise.
- [`a <= b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-8) returns `true` if `a` is less than or equal to `b`, or `false` otherwise.
- [`a > b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-9) returns `true` if `a` is greater than `b`, or `false` otherwise.
- [`a >= b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-10) returns `true` if `a` is greater than or equal to `b`, or `false` otherwise.

### Logical Operators

The logical operators all expect bool values and produce bool values as results.

- [`a || b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-11) returns `true` if either `a` or `b` is `true`, or `false` if both are `false`.
- [`a && b`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-b-12) returns `true` if both `a` and `b` are `true`, or `false` if either one is `false`.
- [`!a`](https://developer.hashicorp.com/terraform/language/expressions/operators#a-1) returns `true` if `a` is `false`, and `false` if `a` is `true`.

Terraform does not have an operator for the "exclusive OR" operation. If you know that both operators are boolean values then exclusive OR is equivalent to the `!=` ("not equal") operator.

The logical operators in Terraform do not short-circuit, meaning `var.foo || var.foo.bar` will produce an error message if `var.foo` is `null` because both `var.foo` and `var.foo.bar` are evaluated.



## Function calls



## Conditional Expressions

> A *conditional expression* uses the value of a boolean expression to select one of two values.

**Syntax**

```
condition ? true_val : false_val
```

**Conditions**

`condition`은 `boolean`  타입을 반환하며, 일반적으로 일치여부(`==`, `!=`), 비교(`<`, `>`, ...), 논리연산자(`&&`, `||`, ...) 등을 사용한다.  ->  Operators 참조 

**Custom Condition Checks**

You can create conditions that produce custom error messages for several types of objects in a configuration. For example, you can add a condition to an input variable that checks whether incoming image IDs are formatted properly.

Custom conditions can help capture assumptions, helping future maintainers understand the configuration design and intent. They also return useful information about errors earlier and in context, helping consumers more easily diagnose issues in their configurations.

Refer to [Custom Condition Checks](https://developer.hashicorp.com/terraform/language/expressions/custom-conditions#input-variable-validation) for details.





## For Expressions



## Splat Expressions



## Dynamic Blocks



## Custom Condition Checks



## Type Constraints



## Version Constraints



# 5. Terraform Backend

> https://developer.hashicorp.com/terraform/language/settings/backends/configuration

Terraform “[Backend](https://www.terraform.io/docs/backends/index.html)” 는 Terraform의 state file을 어디에 저장을 하고, 가져올지에 대한 설정입니다. 기본적으로는는 로컬 스토리지에 저장을 하지만, 설정에 따라서 s3, consul, etcd 등 다양한 “[Backend type](https://www.terraform.io/docs/backends/types/index.html)“을 사용할 수 있습니다.

## 5.1. 사용 목적

- **Locking**: 보통 Terraform 코드를 혼자 작성하지 않습니다. 인프라를 변경한다는 것은 굉장히 민감한 작업이 될 수 있습니다. 원격 저장소를 사용함으로써 동시에 같은 state를 접근하는 것을 막아 의도치 않은 변경을 방지할 수 있습니다.
- **Backup**: 로컬 스토리지에 저장한다는건 유실할 수 있다는 가능성을 내포합니다. S3와 같은 원격저장소를 사용함으로써 state 파일의 유실을 방지합니다.



## 5.2. 종류



## 5.3. 장점 Vs. 단점



## 5.4. 설정 방법

```hcl
terraform {
    backend "s3" { # 강의는 
      bucket         = "terraform-s3-bucket" # s3 bucket 이름
      key            = "terraform/own-your-path/terraform.tfstate" # s3 내에서 저장되는 경로를 의미합니다.
      region         = "ap-northeast-2"  
      encrypt        = true
      dynamodb_table = "terraform-lock"
    }
}
```





# 6. Resource

> *Resources* are the most important element in the Terraform language. Each resource block describes one or more infrastructure objects, such as virtual networks, compute instances, or higher-level components such as DNS records.
>
> https://developer.hashicorp.com/terraform/language/resources

## 6.1. Meta-Arguments

### 6.1.1. depends_on

> **Note:** Module support for `depends_on` was added in Terraform version 0.13, and prior versions can only use it with resources.

### 6.1.2. count

> **Version note:** Module support for `count` was added in Terraform 0.13, and previous versions can only use it with resources.

기본적으로 모든 리소스가 가지고 있는 count 파라미터를 이용하 반복되는 리소스를 간단하게 생성할 수 있습니다. count 에 부여한 숫자만큼, 리소스는 반복되어 생성되고 자동으로 테라폼내에서 resource_name[0] 처럼 리스트화 됩니다.

### 6.1.3. for_each

> **Version note:** `for_each` was added in Terraform 0.12.6. Module support for `for_each` was added in Terraform 0.13; previous versions can only use it with resources.

### 6.1.4. provider

```
# default configuration
provider "google" {
  region = "us-central1"
}

# alternate configuration, whose alias is "europe"
provider "google" {
  alias  = "europe"
  region = "europe-west1"
}

resource "google_compute_instance" "example" {
  # This "provider" meta-argument selects the google provider
  # configuration whose alias is "europe", rather than the
  # default configuration.
  provider = google.europe

  # ...
}
```



### 6.1.5. lifecycle

The [Resource Behavior](https://developer.hashicorp.com/terraform/language/resources/behavior) page describes the general lifecycle for resources. Some details of that behavior can be customized using the special nested `lifecycle` block within a resource block body:

```
resource "azurerm_resource_group" "example" {
  # ...

  lifecycle {
    create_before_destroy = true
  }
}
```

`lifecycle` is a nested block that can appear within a resource block. The `lifecycle` block and its contents are meta-arguments, available for all `resource` blocks regardless of type.

#### Syntax & Arguments

* create_before_destroy
* prevent_destroy
* ignore_changes
* replace_triggered_by





#### Custom Condition Checks

```
resource "aws_instance" "example" {
  instance_type = "t2.micro"
  ami           = "ami-abc123"

  lifecycle {
    # The AMI ID must refer to an AMI that contains an operating system
    # for the `x86_64` architecture.
    precondition {
      condition     = data.aws_ami.example.architecture == "x86_64"
      error_message = "The selected AMI must be for the x86_64 architecture."
    }
  }
}
```

## Resource Behavior



## Provisioners -> TODO

> https://developer.hashicorp.com/terraform/language/resources/provisioners/syntax

```
resource "aws_instance" "web" {
  # ...

  provisioner "file" {
    source      = "script.sh"
    destination = "/tmp/script.sh"
  }

  provisioner "remote-exec" {
    inline = [
      "chmod +x /tmp/script.sh",
      "/tmp/script.sh args",
    ]
  }
}
```





# 7. Import

Terraform에서 Import는 Terrafomr을 통해서 생성된 Resource가 아니라 AWS Console을 통해서 직접 추가한 Resource나 다른 Terraform 환경에서 생성한 Resource를 가져오기 위해 제공되는 CLI 명령이다.

사용하는 이유는 Terrafomr은 tfstate파일에 있는 Resource만 본다는 점 때문이다.



## 7.1. 사용 목적



## 7.2. 사용 예시



# 6. Functions

> d

Count, For each와 같은 문법은 -> Resource.meta-argument로 가세요 

## 6.1. Numeric



## 6.2. String



## 6.3. Collection



## 6.4. Encoding



## 6.5. Filesystem



## 6.6. Data & Time



## 6.7. Hash & Ctypto



## 6.8. IP Network

### 6.8.1. cidrhost

### 6.8.2. cidrnetmask

### 6.8.3. cidrsubnet

`cidrsubnet` calculates a subnet address within given IP network address prefix.

```
cidrsubnet(prefix, newbits, netnum)
```

`prefix` must be given in CIDR notation, as defined in [RFC 4632 section 3.1](https://tools.ietf.org/html/rfc4632#section-3.1).

`newbits` is the number of additional bits with which to extend the prefix. For example, if given a prefix ending in `/16` and a `newbits` value of `4`, the resulting subnet address will have length `/20`.

`netnum` is a whole number that can be represented as a binary integer with no more than `newbits` binary digits, which will be used to populate the additional bits added to the prefix.



#### Example

```
> cidrsubnet("172.16.0.0/12", 4, 2)
172.18.0.0/16
> cidrsubnet("10.1.2.0/24", 4, 15)
10.1.2.240/28
> cidrsubnet("fd00:fd12:3456:7890::/56", 16, 162)
fd00:fd12:3456:7800:a200::/72
```



### 6.8.4. sidrsubnets

`cidrsubnets` calculates a sequence of consecutive IP address ranges within a particular CIDR prefix.

```
cidrsubnets(prefix, newbits...)
```

`prefix` must be given in CIDR notation, as defined in [RFC 4632 section 3.1](https://tools.ietf.org/html/rfc4632#section-3.1).

The remaining arguments, indicated as `newbits` above, each specify the number of additional network prefix bits for one returned address range. The return value is therefore a list with one element per `newbits` argument, each a string containing an address range in CIDR notation.

For more information on IP addressing concepts, see the documentation for the related function [`cidrsubnet`](https://developer.hashicorp.com/terraform/language/functions/cidrsubnet). `cidrsubnet` calculates a single subnet address within a prefix while allowing you to specify its subnet number, while `cidrsubnets` can calculate many at once, potentially of different sizes, and assigns subnet numbers automatically.

When using this function to partition an address space as part of a network address plan, you must not change any of the existing arguments once network addresses have been assigned to real infrastructure, or else later address assignments will be invalidated. However, you *can* append new arguments to existing calls safely, as long as there is sufficient address space available.

This function accepts both IPv6 and IPv4 prefixes, and the result always uses the same addressing scheme as the given prefix.

#### Example

```
> cidrsubnets("10.1.0.0/16", 4, 4, 8, 4)
[
  "10.1.0.0/20",
  "10.1.16.0/20",
  "10.1.32.0/24",
  "10.1.48.0/20",
]

> cidrsubnets("fd00:fd12:3456:7890::/56", 16, 16, 16, 32)
[
  "fd00:fd12:3456:7800::/72",
  "fd00:fd12:3456:7800:100::/72",
  "fd00:fd12:3456:7800:200::/72",
  "fd00:fd12:3456:7800:300::/88",
]

```







## 6.9. Type Conversion





# Example

## AWS

### VPC & Subnet



### EKS



## GCP









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



# Reference

1. https://developer.hashicorp.com/terraform/cli/commands
2. https://developer.hashicorp.com/terraform/language/resources/behavior
3. https://ch4njun.tistory.com/181
