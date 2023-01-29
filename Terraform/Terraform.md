

``` tex
# 1.3.X 버젼을 기반으로 작성된 문서입니다. 
```



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

## Working Directory 

테라폼 `working directory`는 다음을 포함합니다. 

1. 테라폼이 관리할 리소스(provider, ...)가 적혀있는 설정 파일
   - A Terraform configuration describing resources Terraform should manage
2. `.terraform` 디렉토리
   1. 캐싱된 `provider plugins`과 `modules`
   2. 현재 활성화된 [workspace](https://developer.hashicorp.com/terraform/cli/workspaces)의 기록(record)
   3. 가장 마지막에 사용된 backend 설정 정보 (state migration시에 사용)
3. State data
   - Local Backend인 경우
     - `terraform.tfstate` file (if the directory only uses the default workspace)
     - `terraform.tfstate.d` directory (if the directory uses multiple workspaces)



## Initializing

`terraform init`은 테라폼 설정파일(Terraform configuration files)을 포함하고 있는 `working directory`를 초기화(`init`)합니다. 기존의 설정 파일이 수정되었거나 Github과 같은 VCS에서 레포지토리를 클론 받은 경우, 제일 먼저 수행해야하는 명령어입니다. 

초기화(`initializing`) 이후, 테라폼은 `.terraform/` 디렉토리를 생성하고 해당 디렉토리 내부에 `terraform.tfstate`를 생성합니다. 단, remote backend를 설정한 경우, 해당 Backend에 state파일을 생성합니다.  

Multiple Workspaces를 지원하는 Backend에서는 `Workspace`별로 `tfstate` 파일을 관리할 수 있습니다. 예를들어, local backend의 경우 `terraform.tfstate.d`디렉토리 내부에 `Workspace`별로 폴더가 생성되어 저장됩니다. 

```
├── dev.tfvars
├── main.tf
├── prod.tfvars
├── terraform.tfstate.d
│   ├── dev
│   │   └── terraform.tfstate
│   └── prod
│       └── terraform.tfstate
└── variables.tf
```



**Summary**

- 멱등성이 보장되므로 `Provider`나 `Configuration`이 수정된 것이 아니라면 여러번 `init`을 수행하더라도 동일한 상태를 보장합니다. (Like pip install)
- `backend`를 설정하였다면 해당 `backend`에 state 저장을 위한 `.tfstate` 파일을 생성합니다.
- `.terraform/` 디렉토리 내부에는 테라폼 CLI를 통해서 제공된 인증 정보도 포함되므로 `Git`에 커밋하지 않도록 `.gitignore`에 추가해야 합니다. 
- init 작업을 완료하면 local에는 `.tfstate`에 정의된 내용을 담은 `.terraform` 파일이 생성됩니다.
- 기존에 다른 개발자가 이미 `.tfstate`에 인프라를 정의해 놓은 것이 있다면, 다른 개발자는 init작업을 통해서 local에 sync를 맞출 수 있습니다. (확인필요)



## workspace

**Key Points**

- With Workspaces, we can set deploy different environment with same terraform configuration files.
- To manage multiple distinct sets of infrastructure resources (e.g. multiple environments), we can use Workspaces.
- Workspaces isolate Terraform state. It is a best practice to have separate state per environment. Workspaces are technically equivalent to renaming your state file.
- Workspaces ensure that the environments are isolated and mirrored.
- Workspaces are the successor to old Terraform Environments.

Workspaces are convenient in a number of situations:

- **Multiple Environments:** One common need in infrastructure management is to build multiple environments, with mostly the same setup but keeping a few variables different, like networking and sizing.
  \1. Production
  \2. Staging
  \3. Development
- **Multiple Regions/Locations:** Replicate infrastructure in multiple places for High Availability (HA) and Disaster Recovery (DR).
  \1. us-east-1
  \2. eu-west-2
- **Multiple Accounts/Subscriptions:** Create infrastructure in multiple accounts.
  \1. Cloud Account 1
  \2. Cloud Account 2
- **Testing and Research:** Quickly create a new infrastructure for temporary pilot testing, freely experiment or R&D purpose, and destroy it with single command.

## Module

`Module`이란 1개 이상의 `resource`로 구성되어있는 `container`를 의미합니다. 일반적으로 프로그래밍에서의 함수와 비슷하며, 테라폼 내에서 특정 `resource` 구성을 위한 코드를 재활용(재사용)하기 위한 용도로 많이 쓰입니다. 



**Summary**

- Module을 사용하면 변수만 바꿔서 동일한 리소스를 손쉽게 생성할 수 있습니다. 



## tfstate

For Local state, Terraform stores the workspace states in a directory called `terraform.tfstate.d`. Within that, it creates a sub-directory for every workspace and sub-directories contain individual state files for the particular workspace. All state files are stored in `/.terraform.state.d/<workspacename>`. This directory should be treated similarly to local-only `terraform.tfstate.`

For [Remote state](https://medium.com/devops-mojo/terraform-remote-states-overview-what-is-terraform-remote-state-storage-introduction-936223a0e9d0), the workspaces are stored directly in the configured [backend](https://www.terraform.io/docs/language/settings/backends/index.html). Usually, the workspaces are stored by appending the workspace name to the state path. To ensure that workspace names are stored correctly and safely in all backends, the name must be valid to use in a URL path segment without escaping.

**purpose**

https://developer.hashicorp.com/terraform/language/state/purpose



## provider

Terraform으로 정의할 Infrastructure Provider를 의미하며, [Terraform Registry](https://registry.terraform.io/browse/providers)에서 사용가능한 `provider`를 확인할 수 있습니다.

각 `provider`는 Terraform이 관리할 수 있는 `resource` 또는 `data source`를 제공합니다. 즉, `provider` 없이는 `resource`를 관리할 수 없습니다. 대부분의 `provider`는 특정 플랫폼을 대상으로 작동하도록 구성되어 있습니다.



**Example**

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

일반적으로 실제로 생성할 인프라 자원을 의미하지만, `iam_policy_attachment`와 같은 행동 또는 행위도 `resource`로 정의될 수 있습니다. 



**Example**

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

**Validation**

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

**Example**

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

**Example**

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

**Example**

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

**Options**

```tex
# Global
-chdir=DIR
Switch to a different working directory before executing the given subcommand.
```



## 4.1. init

> Prepare your working directory for other commands

```
terraform init [options]
```



**General Options**

> The following options apply to all of (or several of) the initialization steps

- [`-input=true`](https://developer.hashicorp.com/terraform/cli/commands/init#input-true) Ask for input if necessary. If false, will error if input was required.
- [`-lock=false`](https://developer.hashicorp.com/terraform/cli/commands/init#lock-false) Disable locking of state files during state-related operations.
- [`-lock-timeout=`](https://developer.hashicorp.com/terraform/cli/commands/init#lock-timeout) Override the time Terraform will wait to acquire a state lock. The default is `0s` (zero seconds), which causes immediate failure if the lock is already held by another process.
- [`-no-color`](https://developer.hashicorp.com/terraform/cli/commands/init#no-color) Disable color codes in the command output.
- [`-upgrade`](https://developer.hashicorp.com/terraform/cli/commands/init#upgrade) Opt to upgrade modules and plugins as part of their respective installation steps. See the sections below for more details.



## 4.2. plan

> Show changes required by the current configuration

```
terraform plan [options]
```



- Reads the current state of any already-existing remote objects to make sure that the Terraform state is up-to-date.
- Compares the current configuration to the prior state and noting any differences.
- Proposes a set of change actions that should, if applied, make the remote objects match the configuration.

- 정의한 코드가 어떤 인프라를 만들게 되는지 미리 예측 결과를 보여줍니다. 단, plan을 한 내용에 에러가 없다고 하더라도, 실제 적용되었을 때는 에러가 발생할 수 있습니다.
- **Plan 명령어는 어떠한 형상에도 변화를 주지 않습니다.**



**Options**

- [`-var 'NAME=VALUE'`](https://developer.hashicorp.com/terraform/cli/commands/plan#var-name-value) - Sets a value for a single [input variable](https://developer.hashicorp.com/terraform/language/values/variables) declared in the root module of the configuration. Use this option multiple times to set more than one variable. Refer to [Input Variables on the Command Line](https://developer.hashicorp.com/terraform/cli/commands/plan#input-variables-on-the-command-line) for more information.

- [`-var-file=FILENAME`](https://developer.hashicorp.com/terraform/cli/commands/plan#var-file-filename) - Sets values for potentially many [input variables](https://developer.hashicorp.com/terraform/language/values/variables) declared in the root module of the configuration, using definitions from a ["tfvars" file](https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files). Use this option multiple times to include values from more than one file.

- [`-out=FILENAME`](https://developer.hashicorp.com/terraform/cli/commands/plan#out-filename) - Writes the generated plan to the given filename in an opaque file format that you can later pass to `terraform apply` to execute the planned changes, and to some other Terraform commands that can work with saved plan files.

  Terraform will allow any filename for the plan file, but a typical convention is to name it `tfplan`. **Do not** name the file with a suffix that Terraform recognizes as another file format; if you use a `.tf` suffix then Terraform will try to interpret the file as a configuration source file, which will then cause syntax errors for subsequent commands.

  The generated file is not in any standard format intended for consumption by other software, but the file *does* contain your full configuration, all of the values associated with planned changes, and all of the plan options including the input variables. If your plan includes any sort of sensitive data, even if obscured in Terraform's terminal output, it will be saved in cleartext in the plan file. You should therefore treat any saved plan files as potentially-sensitive artifacts.

- [`-compact-warnings`](https://developer.hashicorp.com/terraform/cli/commands/plan#compact-warnings) - Shows any warning messages in a compact form which includes only the summary messages, unless the warnings are accompanied by at least one error and thus the warning text might be useful context for the errors.

- 

  [`-detailed-exitcode`](https://developer.hashicorp.com/terraform/cli/commands/plan#detailed-exitcode) - Returns a detailed exit code when the command exits. When provided, this argument changes the exit codes and their meanings to provide more granular information about what the resulting plan contains:

  - 0 = Succeeded with empty diff (no changes)
  - 1 = Error
  - 2 = Succeeded with non-empty diff (changes present)

- [`-input=false`](https://developer.hashicorp.com/terraform/cli/commands/plan#input-false) - Disables Terraform's default behavior of prompting for input for root module input variables that have not otherwise been assigned a value. This option is particularly useful when running Terraform in non-interactive automation systems.

- [`-json`](https://developer.hashicorp.com/terraform/cli/commands/plan#json) - Enables the [machine readable JSON UI](https://developer.hashicorp.com/terraform/internals/machine-readable-ui) output. This implies `-input=false`, so the configuration must have no unassigned variable values to continue.

- [`-lock=false`](https://developer.hashicorp.com/terraform/cli/commands/plan#lock-false) - Don't hold a state lock during the operation. This is dangerous if others might concurrently run commands against the same workspace.

- [`-lock-timeout=DURATION`](https://developer.hashicorp.com/terraform/cli/commands/plan#lock-timeout-duration) - Unless locking is disabled with `-lock=false`, instructs Terraform to retry acquiring a lock for a period of time before returning an error. The duration syntax is a number followed by a time unit letter, such as "3s" for three seconds.

- [`-no-color`](https://developer.hashicorp.com/terraform/cli/commands/plan#no-color) - Disables terminal formatting sequences in the output. Use this if you are running Terraform in a context where its output will be rendered by a system that cannot interpret terminal formatting.



**Example: Destroy Plan**

```
terraform plan -destroy
```



**Example: Refresh Only Plan**

```
terraform plan -refresh-only
```





## 4.3. apply

> Create or update infrastructure

```
terraform apply [options] [plan file]
```



- 실제로 인프라를 배포하기 위한 명령어입니다. apply를 완료하면, AWS 상에 실제로 해당 인프라가 생성되고 작업 결과가 backend의 `.tfstate` 파일에 저장됩니다.
- 해당 결과는 local의 `.terraform` 파일에도 저장됩니다.

**Options**

- [`-var 'NAME=VALUE'`](https://developer.hashicorp.com/terraform/cli/commands/plan#var-name-value) - Sets a value for a single [input variable](https://developer.hashicorp.com/terraform/language/values/variables) declared in the root module of the configuration. Use this option multiple times to set more than one variable. Refer to [Input Variables on the Command Line](https://developer.hashicorp.com/terraform/cli/commands/plan#input-variables-on-the-command-line) for more information.
- [`-var-file=FILENAME`](https://developer.hashicorp.com/terraform/cli/commands/plan#var-file-filename) - Sets values for potentially many [input variables](https://developer.hashicorp.com/terraform/language/values/variables) declared in the root module of the configuration, using definitions from a ["tfvars" file](https://developer.hashicorp.com/terraform/language/values/variables#variable-definitions-tfvars-files). Use this option multiple times to include values from more than one file.



## 4.4. destroy

> Destroy previously-created infrastructure

```
terraform destroy [options]
```

The `terraform destroy` command is a convenient way to destroy all remote objects managed by a particular Terraform configuration.

While you will typically not want to destroy long-lived objects in a production environment, Terraform is sometimes used to manage ephemeral infrastructure for development purposes, in which case you can use `terraform destroy` to conveniently clean up all of those temporary objects once you are finished with your work.







## 4.5. workspace 

> Workspace management

```shell 
terraform workspace <subcommand> [options] [args]
```

### TODO네이밍 방법

```
<COMPONENT>-<ENVIRONMENT>-<REGION>
```

https://developer.hashicorp.com/terraform/cloud-docs/workspaces/naming

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

```
terraform import [options] ADDRESS ID
```

`import`는 테라폼을 통해 전개하지 않은 기존의 Object(Resource)를 `terraform state`로 옮겨주는 작업입니다. 테라폼 코드를 자동으로 생성해주는 것이 아니므로, Resource를 사전 또는 사후에 코드로 작성하여야 합니다. 

일반적으로 테라폼 도입 전에 전개된 인프라 또는 장애 대응과 같은 이유로 Console에서 작업한 내용을 Terraform으로 옮기는 경우에 많이 사용하게 됩니다.



**주의사항**

* Terraform Cloud 사용시 import 작업이 Cloud가 아닌 Local에서 이루어지므로 Terraform Cloud의 Variables를 사용할 수 없습니다. 따라서, Local에서 `var file`등의 형태로 별도로 variables를 제공하여야 합니다. 
* `Remote object`는 꼭 하나의 Terraform Resource와 매칭되어야한다. 동일한 `object`를 여러번 Import하면 안됩니다. 



**Example: 기존 ECR 레포지토리 Import하기**

1. 빈 블럭 생성

```
resource "aws_ecr_repository" "ecr_repo" {

}
```

2. `terraform import`를 사용해 state 가져오기

```
terraform import -var-file=local.tfvars aws_ecr_repository.ecr_repo <ECR_REPO_NAME>

# Output
Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

3. `terraform state show`를 사용해 테라폼 코드 확인

```
terraform state show aws_ecr_repository.ecr_repo

# Output
# aws_ecr_repository.ecr_repo:
resource "aws_ecr_repository" "ecr_repo" {
    arn                  = "<ARN>"
    id                   = "<ECR_REPO_NAME>"
    image_tag_mutability = "MUTABLE"
    name                 = "<ECR_REPO_NAME>"
    registry_id          = "<value>"
    repository_url       = "<url>"
    tags                 = {}
    tags_all             = {
        "managed_by" = "terraform"
    }

    encryption_configuration {
        encryption_type = "AES256"
    }

    image_scanning_configuration {
        scan_on_push = true
    }
}
```

4. 테라폼 코드 재작성

```
resource "aws_ecr_repository" "ecr_repo" {
  name                 = <ECR_REPO_NAME>
  image_tag_mutability = "MUTABLE"
  
  encryption_configuration {
  	encryption_type = "AES256"
  }

  image_scanning_configuration {
    scan_on_push = true
  }
}
```

5. `terraform plan`을 통한 검증



## 4.10. graph

> Generate a Graphviz graph of the steps in an operation

```
terraform graph [options]
```

Outputs the visual execution graph of Terraform resources according to either the current configuration or an execution plan.

The graph is outputted in DOT format. The typical program that can read this format is GraphViz, but many web services are also available to read this format.

The `-type` flag can be used to control the type of graph shown. Terraform creates different graphs for different operations. See the options below for the list of types supported. The default type is "plan" if a configuration is given, and "apply" if a plan file is passed as an argument.

Options:

- [`-plan=tfplan`](https://developer.hashicorp.com/terraform/cli/commands/graph#plan-tfplan) - Render graph using the specified plan file instead of the configuration in the current directory.
- [`-draw-cycles`](https://developer.hashicorp.com/terraform/cli/commands/graph#draw-cycles) - Highlight any cycles in the graph with colored edges. This helps when diagnosing cycle errors.
- [`-type=plan`](https://developer.hashicorp.com/terraform/cli/commands/graph#type-plan) - Type of graph to output. Can be: `plan`, `plan-destroy`, `apply`, `validate`, `input`, `refresh`.
- [`-module-depth=n`](https://developer.hashicorp.com/terraform/cli/commands/graph#module-depth-n) - (deprecated) In prior versions of Terraform, specified the depth of modules to show in the output.



**Example: Generating Images**

```
terraform graph | dot -Tsvg > graph.svg
```

![image-20230120183404529](Terraform.assets/image-20230120183404529.png)



## 4.11. state

> Advanced state management

```sh
terraform state <subcommand> [options] [args]
```

### 4.11.1. list

> The command is used to list resources within a [Terraform state](https://developer.hashicorp.com/terraform/language/state).

Address와 일치하는 Resource를 보여준다. 

**Usage**

```
terraform state list [options] [address...]
```

**Output**

```
aws_instance.foo
aws_instance.bar[0]
aws_instance.bar[1]
module.elb.aws_elb.main
```



#### Example: All Resources

This example will list all resources, including modules:

```mdx-code-blocks_codeBlockMargin__TI7B4
$ terraform state list
aws_instance.foo
aws_instance.bar[0]
aws_instance.bar[1]
module.elb.aws_elb.main
```

#### Example: Filtering by Resource

This example will only list resources for the given name:

```mdx-code-blocks_codeBlockMargin__TI7B4
$ terraform state list aws_instance.bar
aws_instance.bar[0]
aws_instance.bar[1]
```

#### Example: Filtering by Module

This example will list resources in the given module and any submodules:

```mdx-code-blocks_codeBlockMargin__TI7B4
$ terraform state list module.elb
module.elb.aws_elb.main
module.elb.module.secgroups.aws_security_group.sg
```

#### Example: Filtering by ID

This example will only list the resource whose ID is specified on the command line. This is useful to find where in your configuration a specific resource is located.

```mdx-code-blocks_codeBlockMargin__TI7B4
$ terraform state list -id=sg-1234abcd
module.elb.aws_security_group.sg
```



### 4.11.2. show

>The command is used to show the attributes of a single resource in the [Terraform state](https://developer.hashicorp.com/terraform/language/state).

Address에 존재하는 Resource의 속성들을 보여준다. 

Import를 통해 기존 Resource를 가져오는 경우, `show` 명령어를 통해서 해당 리소스를 위한 코드 작성에 필요한 속성 정보들을 확인할 수 있다. 

**Usage**

```
terraform state show [options] ADDRESS
```

#### Example: Show a Resource

The example below shows a `packet_device` resource named `worker`:

```mdx-code-blocks_codeBlockMargin__TI7B4
$ terraform state show 'packet_device.worker'
# packet_device.worker:
resource "packet_device" "worker" {
    billing_cycle = "hourly"
    created       = "2015-12-17T00:06:56Z"
    facility      = "ewr1"
    hostname      = "prod-xyz01"
    id            = "6015bg2b-b8c4-4925-aad2-f0671d5d3b13"
    locked        = false
}
```



### 4.11.3. refresh (Deprecated)

`refresh`명령어 대신에 `apply` 명령어를 `refresh-only`옵션과 함께 사용하세요

```
terraform apply -refresh-only
```



### 4.11.4. taint



### 4.11.5. untaint



### 4.11.6. mv

> https://developer.hashicorp.com/terraform/cli/commands/state/mv

```
terraform state mv [options] SOURCE DESTINATION
```

**Example: Rename a Resource**

Renaming a resource means making a configuration change like the following:

```diff
-resource "kubernetes_config_map" "aws_auth" {
+resource "kubernetes_config_map" "data_eks_aws_auth" {
   # ...
 }
```

To tell Terraform that it should treat the new "helper" resource as a rename of the old "worker" resource, you can pair the above configuration change with the following command:

```shell
terraform state mv kubernetes_config_map.aws_auth kubernetes_config_map.data_eks_aws_auth   

# Output
Acquiring state lock. This may take a few moments...
Move "kubernetes_config_map.aws_auth" to "kubernetes_config_map.data_eks_aws_auth"
Successfully moved 1 object(s).
Releasing state lock. This may take a few moments...
```

### 4.11.7. rm



### 4.11.8. replace-provider



### 4.11.9. pull

### 4.11.10. push

### 4.11.11. force-unlock







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



**Local Backend Vs. Remote Backend** 

- The local backend stores state on the local filesystem, locks that state using system APIs, and performs operations locally.

- The remote backend is unique among all other Terraform backends because it can both store state snapshots and execute operations for Terraform Cloud's [CLI-driven run workflow](https://developer.hashicorp.com/terraform/cloud-docs/run/cli). It used to be called an "enhanced" backend.

  When using full remote operations, operations like `terraform plan` or `terraform apply` can be executed in Terraform Cloud's run environment, with log output streaming to the local terminal. Remote plans and applies use variable values from the associated Terraform Cloud workspace.

  You can also use Terraform Cloud with local operations, in which case only state is stored in the Terraform Cloud backend.



## 5.1. 사용 목적

- **Locking**: 보통 Terraform 코드를 혼자 작성하지 않습니다. 인프라를 변경한다는 것은 굉장히 민감한 작업이 될 수 있습니다. 원격 저장소를 사용함으로써 동시에 같은 state를 접근하는 것을 막아 의도치 않은 변경을 방지할 수 있습니다.
- **Backup**: 로컬 스토리지에 저장한다는건 유실할 수 있다는 가능성을 내포합니다. S3와 같은 원격저장소를 사용함으로써 state 파일의 유실을 방지합니다.



## 5.2. Remote Backend 목록

> 사용가능한 전체 목록은 [공식문서](https://developer.hashicorp.com/terraform/language/settings/backends/remote)에서 확인 가능합니다. 

### 5.2.1 s3

> https://developer.hashicorp.com/terraform/language/settings/backends/s3

Stores the state as a given key in a given bucket on [Amazon S3](https://aws.amazon.com/s3/). This backend also supports state locking and consistency checking via [Dynamo DB](https://aws.amazon.com/dynamodb/), which can be enabled by setting the `dynamodb_table` field to an existing DynamoDB table name. A single DynamoDB table can be used to lock multiple remote state files. Terraform generates key names that include the values of the `bucket` and `key` variables.

**Warning!** It is highly recommended that you enable [Bucket Versioning](https://docs.aws.amazon.com/AmazonS3/latest/userguide/manage-versioning-examples.html) on the S3 bucket to allow for state recovery in the case of accidental deletions and human error.

**Example Configuration**

```hcl
terraform {
  backend "s3" {
    bucket = "mybucket"
    key    = "path/to/my/key"
    region = "us-east-1"
  }
}
```

This assumes we have a bucket created called `mybucket`. The Terraform state is written to the key `path/to/my/key`.



**장점**

- ddd

**단점**

- Locking을 위해서는 추가적인 Dynamo DB 설정이 필요하다



### 5.2.1 gcs

Stores the state as an object in a configurable prefix in a pre-existing bucket on [Google Cloud Storage](https://cloud.google.com/storage/) (GCS). The bucket must exist prior to configuring the backend.

This backend supports [state locking](https://developer.hashicorp.com/terraform/language/state/locking).

**Warning!** It is highly recommended that you enable [Object Versioning](https://cloud.google.com/storage/docs/object-versioning) on the GCS bucket to allow for state recovery in the case of accidental deletions and human error.

**Example Configuration**

```hcl
terraform {
  backend "gcs" {
    bucket  = "tf-state-prod"
    prefix  = "terraform/state"
  }
}
```



### 5.2.1 consul

tores the state in the [Consul](https://www.consul.io/) KV store at a given path.

This backend supports [state locking](https://developer.hashicorp.com/terraform/language/state/locking).

**Example Configuration**

```hcl
terraform {
  backend "consul" {
    address = "consul.example.com"
    scheme  = "https"
    path    = "full/path"
  }
}
```

Copy

Note that for the access credentials we recommend using a [partial configuration](https://developer.hashicorp.com/terraform/language/settings/backends/configuration#partial-configuration).





### 5.2.1 Kubernetes

**Note:** This backend is limited by Kubernetes' maximum Secret size of 1MB. See [Secret restrictions](https://kubernetes.io/docs/concepts/configuration/secret/#restrictions) for details.

Stores the state in a [Kubernetes secret](https://kubernetes.io/docs/concepts/configuration/secret/).

This backend supports [state locking](https://developer.hashicorp.com/terraform/language/state/locking), with locking done using a Lease resource.

**Example Configuration**

```hcl
terraform {
  backend "kubernetes" {
    secret_suffix    = "state"
    config_path      = "~/.kube/config"
  }
}
```

Copy

This assumes the user/service account running terraform has [permissions](https://kubernetes.io/docs/reference/access-authn-authz/authorization/) to read/write secrets in the [namespace](https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/) used to store the secret.

If the `config_path` or `config_paths` attribute is set the backend will attempt to use a [kubeconfig file](https://kubernetes.io/docs/concepts/configuration/organize-cluster-access-kubeconfig/) to gain access to the cluster.

If the `in_cluster_config` flag is set the backend will attempt to use a [service account](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/) to access the cluster. This can be used if Terraform is being run from within a pod running in the Kubernetes cluster.

For most use cases either `in_cluster_config`, `config_path`, or `config_paths` will need to be set. If all flags are set the configuration at `config_path` will be used.

Note that for the access credentials we recommend using a [partial configuration](https://developer.hashicorp.com/terraform/language/settings/backends/configuration#partial-configuration).



### 5.2.1 pg

> https://developer.hashicorp.com/terraform/language/settings/backends/pg

Stores the state in a [Postgres database](https://www.postgresql.org/) version 10 or newer.

This backend supports [state locking](https://developer.hashicorp.com/terraform/language/state/locking).

**Example Configuration**

```hcl
terraform {
  backend "pg" {
    conn_str = "postgres://user:pass@db.example.com/terraform_backend"
  }
}
```

Copy

Before initializing the backend with `terraform init`, the database must already exist:

```mdx-code-blocks_codeBlockMargin__TI7B4
createdb terraform_backend
```

Copy

This `createdb` command is found in [Postgres client applications](https://www.postgresql.org/docs/10/reference-client.html) which are installed along with the database server.

We recommend using a [partial configuration](https://developer.hashicorp.com/terraform/language/settings/backends/configuration#partial-configuration) for the `conn_str` variable, because it typically contains access credentials that should not be committed to source control:

```hcl
terraform {
  backend "pg" {}
}
```

Copy

Then, set the credentials when initializing the configuration:

```mdx-code-blocks_codeBlockMargin__TI7B4
terraform init -backend-config="conn_str=postgres://user:pass@db.example.com/terraform_backend"
```

Copy

To use a Postgres server running on the same machine as Terraform, configure localhost with SSL disabled:

```mdx-code-blocks_codeBlockMargin__TI7B4
terraform init -backend-config="conn_str=postgres://localhost/terraform_backend?sslmode=d
```

### 5.2.1 Terraform Cloud

> **Note:** We introduced the remote backend in Terraform v0.11.13 and Terraform Enterprise v201809-1. As of Terraform v1.1.0 and Terraform Enterprise v202201-1, **we recommend using the Terraform Cloud's built-in [`cloud` integration](https://developer.hashicorp.com/terraform/cli/cloud/settings)** instead of this backend. The `cloud` option includes an improved user experience and more features.

The main module of a Terraform configuration can integrate with Terraform Cloud to enable its [CLI-driven run workflow](https://developer.hashicorp.com/terraform/cloud-docs/run/cli). You only need to configure these settings when you want to use Terraform CLI to interact with Terraform Cloud. Terraform Cloud ignores them when interacting with Terraform through version control or the API.

> **Hands On:** Try the [Migrate State to Terraform Cloud](https://developer.hashicorp.com/terraform/tutorials/cloud/cloud-migrate) tutorial.

**Usage Example**

To configure the Terraform Cloud CLI integration, add a nested `cloud` block within the `terraform` block. You cannot use the CLI integration and a [state backend](https://developer.hashicorp.com/terraform/language/settings/backends/configuration) in the same configuration.

Refer to [Using Terraform Cloud](https://developer.hashicorp.com/terraform/cli/cloud) in the Terraform CLI documentation for full configuration details, migration instructions, and command line arguments.





## 5.3. 장단점

**장점**



**단점**



## 5.4. 설정 방법

**Terraform Cloud**

```
terraform {
  cloud {
    organization = "example_corp"
    ## Required for Terraform Enterprise; Defaults to app.terraform.io for Terraform Cloud
    hostname = "app.terraform.io"

    workspaces {
      tags = ["app"]
    }
  }
}
```



**그외**

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



## 5.5. Summary

TODO - 표를 이용한 도식화



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

`lifecycle`은 `resource` 블록에서 사용하는 중첩 블록이며, 타입과 관계없이 모든 `resource` 블록에서 사용 가능합니다. `lifecycle`은 총 4개의 인자(Arguments)를 가지고 있습니다. 

**Example**

```
resource "azurerm_resource_group" "example" {
  # ...

  lifecycle {
    create_before_destroy = true
  }
}
```



#### Syntax & Arguments

**create_before_destroy**



* By default, when Terraform must change a resource argument that cannot be updated in-place due to remote API limitations, Terraform will instead destroy the existing object and then create a new replacement object with the new configured arguments.
* The `create_before_destroy` meta-argument changes this behavior so that the new replacement object is created *first,* and the prior object is destroyed after the replacement is created.

  This is an opt-in behavior because many remote object types have unique name requirements or other constraints that must be accommodated for both a new and an old object to exist concurrently. Some resource types offer special options to append a random suffix onto each object name to avoid collisions, for example. Terraform CLI cannot automatically activate such features, so you must understand the constraints for each resource type before using `create_before_destroy` with it.

**prevent_destroy**

* This meta-argument, when set to `true`, will cause Terraform to reject with an error any plan that would destroy the infrastructure object associated with the resource, as long as the argument remains present in the configuration.
* This can be used as a measure of safety against the accidental replacement of objects that may be costly to reproduce, such as database instances. However, it will make certain configuration changes impossible to apply, and will prevent the use of the `terraform destroy` command once such objects are created, and so this option should be used sparingly.

  Since this argument must be present in configuration for the protection to apply, note that this setting does not prevent the remote object from being destroyed if the `resource` block were removed from configuration entirely: in that case, the `prevent_destroy` setting is removed along with it, and so Terraform will allow the destroy operation to succeed.

**ignore_changes (list of attribute names)**

* By default, Terraform detects any difference in the current settings of a real infrastructure object and plans to update the remote object to match configuration.
* The `ignore_changes` feature is intended to be used when a resource is created with references to data that may change in the future, but should not affect said resource after its creation. In some rare cases, settings of a remote object are modified by processes outside of Terraform, which Terraform would then attempt to "fix" on the next run. In order to make Terraform share management responsibilities of a single object with a separate process, the `ignore_changes` meta-argument specifies resource attributes that Terraform should ignore when planning updates to the associated remote object.

  The arguments corresponding to the given attribute names are considered when planning a *create* operation, but are ignored when planning an *update*. The arguments are the relative address of the attributes in the resource. Map and list elements can be referenced using index notation, like `tags["Name"]` and `list[0]` respectively.

  ```hcl
  resource "aws_instance" "example" {
    # ...
  
    lifecycle {
      ignore_changes = [
        # Ignore changes to tags, e.g. because a management agent
        # updates these based on some ruleset managed elsewhere.
        tags,
      ]
    }
  }
  ```

  Instead of a list, the special keyword `all` may be used to instruct Terraform to ignore *all* attributes, which means that Terraform can create and destroy the remote object but will never propose updates to it.

  Only attributes defined by the resource type can be ignored. `ignore_changes` cannot be applied to itself or to any other meta-arguments.

**replace_triggered_by (list of resource or attribute references)**

* *Added in Terraform 1.2.* Replaces the resource when any of the referenced items change. Supply a list of expressions referencing managed resources, instances, or instance attributes. When used in a resource that uses `count` or `for_each`, you can use `count.index` or `each.key` in the expression to reference specific instances of other resources that are configured with the same count or collection.

* References trigger replacement in the following conditions:

  - If the reference is to a resource with multiple instances, a plan to update or replace any instance will trigger replacement.
  - If the reference is to a single resource instance, a plan to update or replace that instance will trigger replacement.
  - If the reference is to a single attribute of a resource instance, any change to the attribute value will trigger replacement.

  You can only reference managed resources in `replace_triggered_by` expressions. This lets you modify these expressions without forcing replacement.

  ```hcl
  resource "aws_appautoscaling_target" "ecs_target" {
    # ...
    lifecycle {
      replace_triggered_by = [
        # Replace `aws_appautoscaling_target` each time this instance of
        # the `aws_ecs_service` is replaced.
        aws_ecs_service.svc.id
      ]
    }
  }
  ```





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



## 7.1. 사용 목적

`Terraform`은 `tfstate`에 있는 `Resource`만 관리할 수 있습니다. 따라서, 테라폼을 통해 전개하지 않은 기존의 `Object(Resource)`의 경우 `import`를 사용하여 `terraform state`에 해당 `Resource`를 등록해 주어야합니다. 

일반적으로 테라폼 도입 전에 전개(`Provisioning`)된 인프라 또는 장애 대응 등의 사유로 `Console`에서 작업한 내용을 `Terraform`으로 옮기는 경우에 많이 사용하게 됩니다. 하지만, `import`를 사용하더라도 `state`외에 테라폼 코드가 수정 또는 생성되는 것이 아니므로 `Resource`를 사전 또는 사후에 코드로 작성하여야 합니다. 



**Tip**

* `terraform state show`를 통해서 `import`한 리소스의 tf 코드를 확인할 수 있습니다. 해당 코드를 기반으로 Terraform Resource를 작성하면 편합니다. 



**주의사항**

* Terraform Cloud 사용시 import 작업이 Cloud가 아닌 Local에서 이루어지므로 Terraform Cloud의 Variables를 사용할 수 없습니다. 따라서, Local에서 `var file`등의 형태로 별도로 variables를 제공하여야 합니다. 
* `Remote object`는 꼭 하나의 Terraform Resource와 매칭되어야한다. 동일한 `object`를 여러번 Import하면 안됩니다. 



## 7.2. 사용 예시

### 7.2.1 기존 ECR 레포지토리 Import하기

1. 빈 블럭 생성

```
resource "aws_ecr_repository" "ecr_repo" {

}
```

2. `terraform import`를 사용해 state 가져오기

```
terraform import -var-file=local.tfvars aws_ecr_repository.ecr_repo <ECR_REPO_NAME>

# Output
Import successful!

The resources that were imported are shown above. These resources are now in
your Terraform state and will henceforth be managed by Terraform.
```

3. `terraform state show`를 사용해 테라폼 코드 확인

```
terraform state show aws_ecr_repository.ecr_repo

# Output
# aws_ecr_repository.ecr_repo:
resource "aws_ecr_repository" "ecr_repo" {
    arn                  = "<ARN>"
    id                   = "<ECR_REPO_NAME>"
    image_tag_mutability = "MUTABLE"
    name                 = "<ECR_REPO_NAME>"
    registry_id          = "<value>"
    repository_url       = "<url>"
    tags                 = {}
    tags_all             = {
        "managed_by" = "terraform"
    }

    encryption_configuration {
        encryption_type = "AES256"
    }

    image_scanning_configuration {
        scan_on_push = true
    }
}
```

4. 테라폼 코드 재작성

```
resource "aws_ecr_repository" "ecr_repo" {
  name                 = <ECR_REPO_NAME>
  image_tag_mutability = "MUTABLE"
  
  encryption_configuration {
  	encryption_type = "AES256"
  }

  image_scanning_configuration {
    scan_on_push = true
  }
}
```

5. `terraform plan`을 통한 검증





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



# Terraform Cloud

## Terraform Cloud를 사용하는 이유

TODO 

Remote 화면?

## Provider(VCS) 설정 방법 

1. `Settings` > `Version Control` > `Providers`
2. `Add a VCS Provider` 클릭
3. 연결할 `Provider` 선택![image-20230127201255674](Terraform.assets/image-20230127201255674.png)

### Bitbucket

1. `Workspace` 설정에서 `Oauth consumer` 클릭 

2. `Add consumer` 클릭

3. `Oauth consumer` 설정

   1. `Terraform Cloud`에서 제공하는 정보 기입 (Name, Callback URL, URL)
   2. `This is a private consumer` 클릭

   ![image-20230127203258132](Terraform.assets/image-20230127203258132.png)

4. 권한 설정 ![image-20230127203018413](Terraform.assets/image-20230127203018413.png)

5. `Save` 클릭

6. `Key`와 `Secret`을 Terraform Cloud에 기입하기 ![image-20230127202258578](Terraform.assets/image-20230127202258578.png)

7. `Connect and continue` 클릭 ![image-20230127203540243](Terraform.assets/image-20230127203540243.png)

8. `Bitbucket` 권한 부여

   - 권한 부여시 로그인된 계정이 `Admin`으로 설정된 `Repository`만 연동이 가능하므로 **개인 계정**을 사용하지 않도록 주의!!



## Workspace 관리 방법

https://developer.hashicorp.com/terraform/cloud-docs/workspaces

| Component               | Local Terraform                                              | Terraform Cloud                                              |
| :---------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
| Terraform configuration | On disk                                                      | In linked version control repository, or periodically uploaded via API/CLI |
| Variable values         | As `.tfvars` files, as CLI arguments, or in shell environment | In workspace                                                 |
| State                   | On disk or in remote backend                                 | In workspace                                                 |
| Credentials and secrets | In shell environment or entered at prompts                   | In workspace, stored as sensitive variables                  |



##  Variables  종류

- hcl Variables
- Variables
- Environment variables



## State Migration 방법



## Terraform Cloud 사용시 여러개의 Workspace 사용하는 방법

```
terraform {
  required_version = ">= 1.2.6"  # 사용할 Terraform 버젼
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.49.0"
    }
  }
  cloud {
    organization = "balaan-data-iac"

    workspaces {
      tags = ["test", "dev"]  # 모든걸 만족하는 workspace가 선택됨.
    }
  }
}
```

![terraform_cloud_init](./Terraform.assets/terraform_cloud_init.png)



## Helm 관련 버그 (Kubernetes authentication 관련)

> https://github.com/hashicorp/terraform-provider-kubernetes/issues/1131

현재 클러스터 생성시 Authentication에 실패하지만, 이후 재차 apply를 하는 경우 정상적으로 인증이 완료되는 버그가 존재함. -> depend on 사용해봐야함. 





# Example

## AWS

### VPC & Subnet



### EKS



## GCP





## Helm

### Authentication

The Helm provider can get its configuration in two ways:

1. Explicitly by supplying attributes to the provider block. This includes:

   - [Using a kubeconfig file](https://registry.terraform.io/providers/hashicorp/helm/latest/docs#file-config)

     - ```
       provider "helm" {
         kubernetes {
           config_path = "~/.kube/config"
         }
       }
       ```

   - [Supplying credentials](https://registry.terraform.io/providers/hashicorp/helm/latest/docs#credentials-config)

     - ```
       provider "helm" {
         kubernetes {
           host     = "https://cluster_endpoint:port"
       
           client_certificate     = file("~/.kube/client-cert.pem")
           client_key             = file("~/.kube/client-key.pem")
           cluster_ca_certificate = file("~/.kube/cluster-ca-cert.pem")
         }
       }
       ```

   - [Exec plugins](https://registry.terraform.io/providers/hashicorp/helm/latest/docs#exec-plugins)

     - Some cloud providers have short-lived authentication tokens that can expire relatively quickly. To ensure the Kubernetes provider is receiving valid credentials, an exec-based plugin can be used to fetch a new token before initializing the provider. For example, on EKS, the command `eks get-token` can be used:

     - ```
       provider "helm" {
         kubernetes {
           host                   = var.cluster_endpoint
           cluster_ca_certificate = base64decode(var.cluster_ca_cert)
           exec {
             api_version = "client.authentication.k8s.io/v1beta1"
             args        = ["eks", "get-token", "--cluster-name", var.cluster_name]
             command     = "aws"
           }
         }
       }
       ```

2. Implicitly through environment variables. This includes:

   - [Using the in-cluster config](https://registry.terraform.io/providers/hashicorp/helm/latest/docs#in-cluster-config)

### **Example**

```
provider "helm" {
  kubernetes {
    config_path = "~/.kube/config"
  }

  # localhost registry with password protection
  registry {
    url = "oci://localhost:5000"
    username = "username"
    password = "password"
  }

  # private registry
  registry {
    url = "oci://private.registry"
    username = "username"
    password = "password"
  }
}

resource "helm_release" "nginx_ingress" {
  name       = "nginx-ingress-controller"

  repository = "https://charts.bitnami.com/bitnami"
  chart      = "nginx-ingress-controller"

  set {
    name  = "service.type"
    value = "ClusterIP"
  }
}
```







# 고려사항(?)

## Mono Repo Vs. Multi Repo

https://www.hashicorp.com/blog/terraform-mono-repo-vs-multi-repo-the-great-debate

https://www.youtube.com/watch?v=4Rlwh4YVLRY&ab_channel=HashiCorp



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

## 9.2. Variables(tfvars, ...) 적용 순서

> https://developer.hashicorp.com/terraform/cloud-docs/workspaces/variables#scope

### 1. Command Line Argument Variables

When using a CLI workflow, variables applied to a run with either `-var` or `-var-file` overwrite workspace-specific and variable set variables that have the same key.

### 2. Local Environment Variables Prefixed with `TF_VAR_`

When using a CLI workflow, local environment variables prefixed with `TF_VAR_` (e.g., `TF_VAR_replicas`) overwrite workspace-specific, variable set, and `.auto.tfvars` file variables that have the same key.

### 3. Workspace-Specific Variables

Workspace-specific variables always overwrite variables from variable sets that have the same key. Refer to [overwrite variables from variable sets](https://developer.hashicorp.com/terraform/cloud-docs/workspaces/variables/managing-variables#overwrite-variable-sets) for details.

### 4. Non-Global Variable Sets

Non-global variables are only applied to a subset of workspaces in an organization.

When non-global variable sets have conflicting variables, Terraform Cloud compares the variable set names and uses values from the variable set with lexical precedence. Terraform and Terraform Cloud operate on UTF-8 strings, and Terraform Cloud sorts variable set names based the on lexical order of Unicode code points.

For example, if you apply `A_Variable_Set` and `B_Variable_Set` to the same workspace, Terraform Cloud will use any conflicting variables from `A_Variable_Set`. This will be the case regardless of which variable set has been edited most recently; Terraform Cloud only considers the lexical ordering of variable set names when determining precedence.

### 5. Global Variable Sets

Non-global variable sets always take precedence over global variable sets that are applied to all workspaces within an organization. Terraform does not allow global variable sets to contain variables with the same key, so they cannot conflict.

### 6. `*.auto.tfvars` Variable Files

Variables in the Terraform Cloud workspace and variables provided through the command line always overwrite variables with the same key from files ending in `.auto.tfvars`.

### 7. `terraform.tfvars` Variable File

Variables in the `.auto.tfvars` files take precedence over variables in the `terraform.tfvars` file.



## 9.3. `*.auto.tfvars` Variable Files 관련

`auto.tfvars`로 끝나는 파일의 경우 Workspace와 관계없이 모든 파일이 적용되므로 주의할 것

Terraform also automatically loads a number of variable definitions files if they are present:

- Files named exactly `terraform.tfvars` or `terraform.tfvars.json`.
- Any files with names ending in `.auto.tfvars` or `.auto.tfvars.json`.



## 9.4. cloud backend와 cloud block의 차이점

결론: backend에서 설정하는 것은 과거의 유산
https://developer.hashicorp.com/terraform/language/settings/backends/remote



## 9.5. Workspace별 코드 관리법

1. 해당 방식은 어렵다. 되긴 하나 그 경우에는 해당 레포에 연결된 모든 워크스페이스가 트리거링 → 원하는 스테이지만 manual 하게 apply하고 나머지를 discard하는 식으로 운영 형태로 관리가 됨 (위험성 다분 및 관리 포인트 증가 인수인계 문제)
2. 추천하는 방법은 디렉토리 단위 구분이나 브랜치로 구별하는 것이다. 그 중에서도 게임회사 예제를 드시면서 디렉토리 구분을 추천해주심. 브랜치로 하더라고 디렉토리 구분을 추천하심 그게 제일 안전하다고 하심.

1. Repository 구분

   - 장점

   - 단점

2. Mono Repo + directory 구분

   - 장점
   - 단점

3. Mono Repo + branch 구분 + directory 구분

   - 장점
   - 단점

4. Mono Repo + Single Directory + Cloud Variable

   - 장점
   - 단점



# Reference

1. https://developer.hashicorp.com/terraform/cli/commands
2. https://developer.hashicorp.com/terraform/language/resources/behavior
3. https://ch4njun.tistory.com/181
