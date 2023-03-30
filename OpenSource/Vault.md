

[TOC]

---

# 1. Introduction

## 1.1. Vault란 무엇인가?

CNCF 선정 Secrets Managements 부분 1위 선정 [(링크)](https://radar.cncf.io/2021-02-secrets-management)





## 1.2. 장점과 단점

### 1.2.1. 장점

* Dynamic / Static Role
* 유저별로 권한 관리 및 CIDR 적용 가능
* 다양한 Secrete Engine



### 1.2.2. 단점

* 비용 (Hasicorp Cloud 경우)
* Dynamic / Static Role을 사용하기 위해서 코드 개선이 필요함
* 



## 1.3. Secret Manager와 비교

| 구분 | Secret Manager(AWS) | Vault |
| ---- | ------------------- | ----- |
|      |                     |       |
|      |                     |       |
|      |                     |       |



---

# 2. Installation

## 2.1. Docker

```shell
# Docker 실행 명령어
docker run --cap-add=IPC_LOCK -d --name=dev-vault -p 8200:8200 -e 'VAULT_DEV_ROOT_TOKEN_ID=myroot' -e 'VAULT_ADDR=http://127.0.0.1:8200' --rm vault:1.9.5
```

## 2.2. Helm Chart

> Official helm chart 
>
> * Vault - https://github.com/hashicorp/vault-helm
> * Consul - https://github.com/hashicorp/consul-k8s/tree/main/charts/consul

### 2.2.1. Download helm chart

```shell
helm repo add hashicorp https://helm.releases.hashicorp.com
# "hashicorp" has been added to your repositories
```



### 2.2.2. Standalone Mode

* `file` storage backend를 사용
* 할당된 file storage에 비밀을 보관하므로 단독 서버만 접근 및 사용이 가능하다. 



### 2.2.3. HA Mode

* 고가용성이 지원되는 storage backend를 사용하여야 한다. 
    * `Consul`
    * `Raft`
    * `PostgreSQL`
    * ...

* 공유된 하나의 storage backend를 사용한다.

#### YAML Example

```yaml
server:
  ha:
    enabled: true
    replicas: 3
```



### 2.2.4. External mode

> https://developer.hashicorp.com/vault/tutorials/kubernetes/kubernetes-external-vault

* Kubernetes Cluster 외부에 있는 Vault를 사용하여 구성



## 2.3. Hashi Cloud





---

# 3. 설명

## 3.1. Seal && Unseal

### 3.1.1. Seal

* 서버가 시작 또는 재시작 한 경우 **Sealed** 상태
    * KMS 세팅을 한 경우 바로 **Unseal** 됨
* 암호화된 비밀들을 복호화할 수 없는 상태

### 3.1.2. Unseal

* `Seal` 상태에서 설정된 임계값 이상의 `unseal key`를 입력하여 해제한 상태
* 정상적인 이용이 가능

### 3.1.3. Auto Unseal

> [공식 문서](https://developer.hashicorp.com/vault/tutorials/auto-unseal?optInFrom=learn)

* 서버가 `Seal` 상태일 때, 외부 키(KMS, ...)를 이용하여 자동으로 `unseal`하는 기능
* `Auto Unseal`이 활성화된 상태일 경우, `init`후 `unseal key` 대신 `Recovery Key`가 제공된다.  
* 지원 목록
  * Azure Key Vault
  * GCP Cloud KMS
  * AWS KMS



## 3.2. AuthMethod

> Default Policy가 없으면 로그인, Renew(갱신), Revoke 불가함!!

### 3.1.1. Token

- 가장 기본적인 인증 방식이자 Vault 서버와 통신할 때 사용되는 인증 방식

- Token 인증을 제외한 나머지 인증 방법은 로그인후 Vault로부터 API 통신시에 사용할 별도의 token을 발급받아 사용한다.

- 2가지 유형의 토큰이 존재한다. ([공식 문서](https://learn.hashicorp.com/tutorials/vault/tokens?in=vault/tokens))

    - Service Token

        서비스 토큰은 Revoke 되기 전까지 Vault의 Storage backend에 저장된다. 토큰의 모든 정보는 Storage에 보관되어 있으므로 갱신 또는 Revoke를 할 수 있다. 다만, Vault와 상호작용(비밀 읽기 등)을 하기 위해서는 Vault 서버에서 권한 체크등의 추가적인 작업이 필요하므로 사용량이 많을 경우 서버에 부담이 될 수 있다.

        - prefix
            - s. ( ~ 1.9.x)
            - hvs. (1.10 ~ )
        - Vault의 storage backend에 저장
        - 토큰의 모든 정보는 storage에 저장되어있으므로 사용을 위해 Vault 서버에서 권한등을 체크해야함
        - Root 토큰 생성 가능

    - Batch Token

        배치 토큰은 서비스 토큰과 달리 Vault의 Storage backend에 저장되지 않는다. 대신, Vault와 상호작용시에 필요한 모든 정보를 encrypted binary large objects (blobs) 형태로 가지고 있다.

        Vault 서버에서 권한 체크등이 이루어질 필요성이 없으므로 사용량이 많아져도 서버에 부담을 주지 않는다. 다만, 기능적인 부분이나 Renew, Revoke 등 토큰 제어적인 면에서 부족하다.

        - prefix
            - b. ( ~ 1.9.x)
            - hvb. (1.10 ~ )
        - 토큰 자체가 encrypted binary large objects (blobs)으로 Vault Action을 위한 모든 정보를 가지고 있다. (Like JWT)
        - Vault Action을 위한 모든 정보를 가지고 있으므로 가볍고 Vault 서버에 부하를 주지 않는다.
        - 모든 정보를 가지고 있으므로 서비스 토큰에 비해 기능, 제어가 부족하다.
        - Root 토큰 생성 불가능

### 3.1.2. UserPass

- 가장 범용적인 형태의 인증 방식
- CIDR을 통해서 보안성을 더 올릴 수 있다

``` shell
# Login via API
curl --request POST \
     --data '{"password": "foo"}' \
     {VALT_ADDR}/v1/auth/userpass/login/{USER_NAME}
```

```json
# Response
{
  "lease_id": "",
  "renewable": false,
  "lease_duration": 0,
  "data": null,
  "auth": {
    "client_token": "c4f280f6-fdb2-18eb-89d3-589e2e834cdb",
    "policies": ["admins"],
    "metadata": {
      "username": "USER_NAME"
    },
    "lease_duration": 0,
    "renewable": false
  }
}
```





### 3.1.3. Kubernetes (k8s)

`kubernetes` 인증은 다음 명령어를 통해서 활성화할 수 있습니다. 

```sh
vault auth enable -path=kubernetes kubernetes
# Success! Enabled kubernetes auth method at: eks/
```



활성화한 이후, 인증과 관련된 설정을 추가로 진행해야합니다. 다음은 `Pod` 내부에 저장되어있는 `service account` 정보를 사용하여 인증 설정을 업데이트하는 예제입니다. 

```sh
# vault write auth/<path>/config ...
vault write auth/kubernetes/config \
  token_reviewer_jwt="$(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" \
  kubernetes_host="https://$KUBERNETES_PORT_443_TCP_ADDR:443" \
  kubernetes_ca_cert=@/var/run/secrets/kubernetes.io/serviceaccount/ca.crt
```



이후, 해당 인증방식에서 사용할 정책(`Policy`)과 역할(`Role`)을 생성합니다. 

```sh
# scret-read policy와 kubernetes interanl-app role 연결
# vault-test 네임스페이스에 vault-secret-read 서비스어카운트에게 권한부여
vault write auth/kubernetes/role/api-server \
  bound_service_account_names=static \
  bound_service_account_namespaces=vault \
  policies=default \
  ttl=24h
```



다음과 같은 방식으로 로그인할 수 있습니다. 

```sh
vault write auth/kubernetes/login role=demo jwt=<JWT_TOKEN>
```



**주의사항**

- 사용할 클러스터마다 config가 달라지므로 클러스터마다 `path`를 다르게 설정해야한다. 



```
vault-agent: [<https://www.vaultproject.io/docs/agent>](<https://www.vaultproject.io/docs/agent>)

[<https://learn.hashicorp.com/tutorials/vault/kubernetes-external-vault?in=vault/kubernetes>](<https://learn.hashicorp.com/tutorials/vault/kubernetes-external-vault?in=vault/kubernetes>)

[<https://devopstales.github.io/home/k8s-vault-v2/>](<https://devopstales.github.io/home/k8s-vault-v2/>)
```



### 3.1.4. AWS (IAM)

> https://faun.pub/using-aws-iam-auth-method-with-hashicorp-vault-7f174e5d1bdd





## 3.3. Secret

- Vault 내에 저장된 모든 정보를 의미함

    - DB 계정, API Key, 서버 설정 파일, … 등

- CLI 명령어

    ```bash
    # Secret 정보 (path, type, ...)
    vault secrets list
    
    # Secret engine 활성화
    vault secrets enable -path=<prefix_path> <secret_type>
    
    # Secret 목록
    vault list <path>
    
    # Secret 비활성화
    vault secret disable <prefix_path>/ 
    ```



## 3.4. Secret Engine

- KV (Key-Value)

    - CLI 명령어

        ```sql
        # Key-Value 저장하기
        vault kv put <path> <key>=<value>
        
        # Key-Value 가져오기
        vault kv get <path>
        
        # Key-Value 특정 필드(key)만 가져오기
        vault kv get -field=<key> <path>
        
        # Key-Value json 형태로 가져오기
        vault kv get -format=json <path>
        
        # Key-Value 삭제하기
        vault kv delete <path>
        ```

    - The `kv` secrets engine is used to store arbitrary secrets within the configured physical storage for Vault.

    - Dictionary 형태로 Secret을 저장하는 형태

- Databases

    - CLI 명령어 ([공식 문서](https://learn.hashicorp.com/tutorials/vault/database-secrets))

        ```sql
        # Postgresql connection 설정 
        vault write database/config/postgresql \\
             plugin_name=postgresql-database-plugin \\
             connection_url="postgresql://{{username}}:{{password}}@localhost:5432/postgres?sslmode=disable" \\
             allowed_roles=readonly \\
             username="root" \\
             password="rootpassword"
        
        # Role 생성
        vault write database/roles/readonly \\
              db_name=postgresql \\
              creation_statements="CREATE ROLE '{{name}}' WITH LOGIN PASSWORD '{{password}}' VALID UNTIL '{{expiration}}' INHERIT; GRANT ro TO '{{name}}';" \\
              default_ttl=1h \\
              max_ttl=24h
        ```

    - Applications ask Vault for database credentials rather than setting them as environment variables

    - Database Root Credential Rotation

        - Root 권한을 가진 계정의 password를 Vault에서 변경하여 보안성을 높힘

            - 변경된 비밀번호는 Vault만 알고 있으며, 알아낼 수 있는 방법은 없다.

        - 지원 DB 목록

            - MongoDB (Atlas 제외)
            - MySQL
            - MariaDB
            - Elastic Search
            - Redshift

        - Policy 설정법

            ```bash
            # Mount secrets engines
            path "sys/mounts/*" {
              capabilities = [ "create", "read", "update", "delete", "list" ]
            }
            
            # Configure the database secrets engine and create roles
            path "database/*" {
              capabilities = [ "create", "read", "update", "delete", "list" ]
            }
            ```

    - Static Secrets

        - 기존에 생성된 계정을 기반으로 요청이 있을 때마다 password를 변경하여 반환

    - Dynamic Secrets ([공식 문서](https://www.vaultproject.io/docs/secrets/databases))

        - 요청이 있을 때마다 새롭게 계정을 생성하여 username과 password를 반환
        - ttl 만료가 되거나 Revoke 요청이 들어온 경우 발급된 계정을 삭제
        - 각 connection별로 사전에 정의된 Role을 이용하여 계정을 발급





## 3.5. Entity && Group

- Entity & alias
    - Entity는 유저의 개념
    - alias는 해당 유저가 가지고 있는 계정에 가까움
    - Entity의 Policy는 해당 Entity에 등록되어있는 모든 alias에 적용됨
    - 각 alias는 하나의 Auth Method를 가짐
    - alias의 name은 각 Auth Method의 username을 의미함
        - Userpass alias를 “minil_IAC”로 생성한 경우 Userpass Auth를 사용하면서 username이 “minil_IAC”인 계정을 의미함
        - Token의 경우 사실상 매칭이 불가능한것으로 보임 → 그 외의 모든 Auth Method는 사용 가능
        - Mapper
            - Userpass → username
    - 각 alias는 계정이므로 별도의 권한을 가질 수 있음
- Groups & alias
    - Group은 유저의 집합
    - Group의 Policy는 모든 Member에게 전파됨
    - Type
        - internal
            - 내부 인증 방법(userpass, ...)을 쓰는 경우
        - external
            - 외부 인증 방법(github, ...)을 쓰는 경우 Member를 지정할 수 없음
            - 그러므로 Alias를 이용하여 동적으로 Member 여부를 체크함
            - https://learn.hashicorp.com/tutorials/vault/identity
    - Member Group (internal)
        - 해당 그룹에 속한 하위 그룹 목록
    - Member Entity (internal)
        - 해당 그룹에 속한 Entity 목록





## 3.6. Policy





##  





----

# 4. CLI

## 4.1 Base

### 4.1.1. Status

```shell
vault status
```

```SHell
# output
Sealed: false
Key Shares: 5
Key Threshold: 3
Unseal Progress: 0
Unseal Nonce:
Version: x.y.z
Build Date: 2022-05-03T08:34:11Z
Cluster Name: vault-cluster-49ffd45f
Cluster ID: d2dad792-fb99-1c8d-452e-528d073ba205

High-Availability Enabled: false
```



### 4.1.2. Login

```shell 
# Token
vault login
Token (will be hidden):

# UserPass
vault login -method=userpass username=<USER_NAME> password=<PASSWORD>
```

```shell
# output
Token (will be hidden):
Success! You are now authenticated. The token information displayed below
is already stored in the token helper. You do NOT need to run "vault login"
again. Future Vault requests will automatically use this token.

Key                  Value
---                  -----
token                s.nDj4BB2tK8NaFffwBZBxyIa1
token_accessor       ZuaObqdTeCHZ4oa9HWmdQJuZ
token_duration       ∞
token_renewable      false
token_policies       ["root"]
identity_policies    []
policies             ["root"]
```



### 4.1.3. Monitor

```shell
vault monitor -log-level=debug
```

 

## 4.2 Operator

### 4.2.1. Seal

> 서버를 Seal 상태로 설정

```shell
vault operator seal
```

```shell 
# output
Success! Vault is sealed.
```



### 4.2.2. Unseal

> Seal 상태인 서버를 Unseal 상태로 설정
>
> 설정된 수(Key Threshold)만큼의 Key가 필요하다

```shell
vault operator unseal <UNSEAL_KEY>

# or
vault operator unseal
Key (will be hidden): <UNSEAL_KEY>
```

```shell 
# output
Key (will be hidden):
Sealed: false
Key Shares: 1
Key Threshold: 1
Unseal Progress: 0
```



### 4.2.3. Raft

**join**

```sh
Usage: vault operator raft join [options] <leader-api-addr>

  Join the current node as a peer to the Raft cluster by providing the address
  of the Raft leader node.

      $ vault operator raft join "http://127.0.0.2:8200"
```



**list-peers**

```sh
Usage: vault operator raft list-peers

  Provides the details of all the peers in the Raft cluster.

      $ vault operator raft list-peers
```



**remove-peer**

```sh
Usage: vault operator raft remove-peer <server_id>

  Removes a node from the Raft cluster.

      $ vault operator raft remove-peer node1
```



**snapshot**

```sh
Usage: vault operator raft snapshot <subcommand> [options] [args]

  This command groups subcommands for operators interacting with the snapshot
  functionality of the integrated Raft storage backend.

Subcommands:
    restore    Installs the provided snapshot, returning the cluster to the state defined in it
    save       Saves a snapshot of the current state of the Raft cluster into a file
```





## 4.3 Secrets





## 4.4 KV





## 4.5 Database



## 4.6 Policy



## 4.7 Auth



## 4.8 Entity && Group











# 5. HVAC (Python Package)





# 6. Advance

## 6.1. Auto Unseal 설정하기

> Auto Unseal의 경우, Vault init을 하기 전에 설정하여야 한다.

### 6.1.1. AWS

> https://developer.hashicorp.com/vault/docs/configuration/seal/awskms

1. IAM User 발급

   1. Vault 에서 사용할 IAM 사용자를 발급한다. 
   2. 별도의 권한은 설정할 필요 없다. 

2. KMS 발급

   1. 기존에 사용하던 KMS를 사용해도 되지만, 가급적 Vault 전용 KMS를 발급한다. 
   2. Terraform을 사용중이더라도, 만일의 사태를 대비하여 수동으로 발급하자!
   3. 키 사용자에 1번에서 발급한 `user`를 추가한다.

3. helm chart 변경 

   1. `endpoint`가 설정되지 않으면 `region`의 `default API endpoint`를 사용한다. 
   2. 다른 Region의 KMS를 사용하는 것이 아니라면 `endpoint`는 설정할 필요가 없다. 

   ```yaml
   # Example
   server:
     extraEnvironmentVars:
       VAULT_SEAL_TYPE: "awskms"
     ha:
       enabled: true
       replicas: 3
       config: |
         ui = true
   
         listener "tcp" {
           tls_disable = 1
           address = "[::]:8200"
           cluster_address = "[::]:8201"
         }
         storage "consul" {
           path = "vault"
           address = "HOST_IP:8500"
         }
         service_registration "kubernetes" {}
         seal "awskms" {
           region     = "REGION"
           access_key = "ACCESS_KEY"
           secret_key = "SECRET_KEY"
           kms_key_id = "KMS_KEY_ID"
         }
   ```

## 6.2. Storage 설정하기

### Consul

```yaml
# Example
server:
  ha:
    enabled: true
    replicas: 3
    
    config: |
      ui = true

      listener "tcp" {
        tls_disable = 1
        address = "[::]:8200"
        cluster_address = "[::]:8201"
      }
      storage "consul" {
        path = "vault"
        address = "HOST_IP:8500"
      }

      service_registration "kubernetes" {}
```



### Raft

```yaml
# Example
server:
  ha:
    enabled: true
    replicas: 3

    raft:
      enabled: true
      config: |
        ui = true

        listener "tcp" {
          tls_disable = 1
          address = "[::]:8200"
          cluster_address = "[::]:8201"
        }

        storage "raft" {
          path = "/vault/data"
        }

        service_registration "kubernetes" {}
  
  dataStorage:
    enabled: true
    size: 20Gi
    storageClass: "<StorageClass>"
    
  auditStorage:
    enabled: true
    size: 10Gi
    storageClass: "<StorageClass>"

```



## 6.3. Metrics





## 6.4. (Raft) Backup & Restore

**prerequisite**

- User that have the following permissions

    - `sys/storage/raft/snapshot`

- [Optional] port-forwarding 

    ```sh
    # Example - Kubernetes
    kubectl port-forward svc/<SERVICE_NAME> 8200:8200 -n vault
    ```

- 



**Backup Procedure**

1. `Vault` 서버에 로그인하기

```sh
export VAULT_ADDR="http://localhost:8200"

# login with token
vault login
```



2. `snapshot` 만들기

```sh
vault operator raft snapshot save backup.snap
```



**Restore Procedure**

1. 데이터 복원하기

```sh
vault operator raft snapshot restore -force backup.snap
```



2. [Optional] Auto-unseal인 경우 서버를 재실행 한다.







## 6.5. Agent Inject

> Kubernetes 인증방식이 아닌 다른 인증 방식을 사용하는 경우, 인증과 관련된 정보를 함께 전달해야하므로 가급적  `kubernetes` 인증 방식을 사용하는 것이 좋습니다. 

**Annotation List**



- [`vault.hashicorp.com/agent-inject`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-agent-inject) - configures whether injection is explicitly enabled or disabled for a pod. This should be set to a `true` or `false` value. Defaults to `false`.

- [`vault.hashicorp.com/agent-inject-secret`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-agent-inject-secret) - configures Vault Agent to retrieve the secrets from Vault required by the container. The name of the secret is any unique string after `vault.hashicorp.com/agent-inject-secret-`, such as `vault.hashicorp.com/agent-inject-secret-foobar`. The value is the path in Vault where the secret is located.
- [`vault.hashicorp.com/agent-inject-template`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-agent-inject-template) - configures the template Vault Agent should use for rendering a secret. The name of the template is any unique string after `vault.hashicorp.com/agent-inject-template-`, such as `vault.hashicorp.com/agent-inject-template-foobar`. This should map to the same unique value provided in `vault.hashicorp.com/agent-inject-secret-`. If not provided, a default generic template is used.

- [`vault.hashicorp.com/agent-pre-populate`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-agent-pre-populate) - configures whether an init container is included to pre-populate the shared memory volume with secrets prior to the containers starting.
- [`vault.hashicorp.com/agent-pre-populate-only`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-agent-pre-populate-only) - configures whether an init container is the only injected container. If true, no sidecar container will be injected at runtime of the pod. Enabling this option is recommended for workloads of type `CronJob` or `Job` to ensure a clean pod termination.



- [`vault.hashicorp.com/auth-config`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-auth-config) - configures additional parameters for the configured authentication method. The name of the config is any unique string after `vault.hashicorp.com/auth-config-`, such as `vault.hashicorp.com/auth-config-role-id-file-path`. This annotation can be reused multiple times to configure multiple settings for the authentication method. Some authentication methods may require additional secrets and should be mounted via the `vault.hashicorp.com/agent-extra-secret` annotation. For a list of valid authentication configurations, see the Vault Agent [auto-auth documentation](https://developer.hashicorp.com/vault/docs/agent/autoauth/methods).
- [`vault.hashicorp.com/auth-path`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-auth-path) - configures the authentication path for the Kubernetes auth method. Defaults to `auth/kubernetes`.
- [`vault.hashicorp.com/auth-type`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-auth-type) - configures the authentication type for Vault Agent. Defaults to `kubernetes`. For a list of valid authentication methods, see the Vault Agent [auto-auth documentation](https://developer.hashicorp.com/vault/docs/agent/autoauth/methods).
- [`vault.hashicorp.com/role`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-role) - configures the Vault role used by the Vault Agent auto-auth method. Required when `vault.hashicorp.com/agent-configmap` is not set.
- [`vault.hashicorp.com/service`](https://developer.hashicorp.com/vault/docs/platform/k8s/injector/annotations#vault-hashicorp-com-service) - configures the Vault address for the injected Vault Agent to use. This value overrides the default Vault address configured in the injector, and may either be the address of a Vault service within the same Kubernetes cluster as the injector, or an external Vault URL.



**Example - Kubernetes authentication**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: vault-injector-test
  name: vault-injector-test
  namespace: vault-test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vault-injector-test
  template:
    metadata:
      annotations:
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/agent-inject-secret-test: secret/data/test
        vault.hashicorp.com/agent-inject-status: update
        vault.hashicorp.com/role: internal-app
      labels:
        app: vault-injector-test
    spec:
      serviceAccountName: vault-secret-read
      containers:
      - image: alpine
        command:
          - "sh"
          - "-c"
          - "cat /vault/secrets/test && sleep 10000"
        imagePullPolicy: Always
        name: vault-injector-test
```



**Example - Without vault agent**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: vault-injector-test
  name: vault-injector-test
  namespace: vault-test
spec:
  replicas: 1
  selector:
    matchLabels:
      app: vault-injector-test
  template:
    metadata:
      annotations:
        vault.hashicorp.com/agent-inject: "true"
        vault.hashicorp.com/agent-inject-secret-test: secret/data/test
        vault.hashicorp.com/agent-pre-populate-only: 'true'
        vault.hashicorp.com/role: internal-app
      labels:
        app: vault-injector-test
    spec:
      serviceAccountName: vault-secret-read
      containers:
      - image: alpine
        command:
          - "sh"
          - "-c"
          - "cat /vault/secrets/test && sleep 10000"
        imagePullPolicy: Always
        name: vault-injector-test
```



**Example - Apply format**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-deployment
  labels:
    app: web
spec:
  replicas: 1
  selector:
    matchLabels:
      app: web
  template:
    metadata:
      labels:
        app: web
      annotations:
        vault.hashicorp.com/agent-inject: 'true'
        vault.hashicorp.com/role: 'web'
        vault.hashicorp.com/agent-inject-secret-config: 'secret/data/web'
        # Environment variable export template
        vault.hashicorp.com/agent-inject-template-config: |
          {{ with secret "secret/data/web" -}}
            export api_key="{{ .Data.data.payments_api_key }}"
          {{- end }}
    spec:
      serviceAccountName: web
      containers:
        - name: web
          image: alpine:latest
          command:
            ['sh', '-c']
          args:
            ['source /vault/secrets/config && <entrypoint script>']
          ports:
            - containerPort: 9090
```







## Oauth 설정 방법

### Google

> https://developer.hashicorp.com/vault/docs/auth/jwt/oidc-providers/google



### 





# Tip

## Backup 시나리오



## Migration 방법









# Reference

1. https://www.youtube.com/watch?v=Ky9LLQgOv_U
2. https://lejewk.github.io/vault-get-started/
3. https://shanta.tistory.com/13
4. https://learn.hashicorp.com/tutorials/vault/agent-kubernetes