# Docker Practice



### [실습-1] `cloudwave:base.v1`에 `terraform` 설치하기

- `cloudwave:base.v1` 이미지를 사용하여 컨테이너를 생성합니다.
  - 컨테이너의 이름은 `base`로 설정합니다. 

- `base` 컨테이너에 접속합니다. 
- 다음 스크립트를 이용하여 `terraform`을 설치합니다. 

```cmd
$ apt-get update && apt-get install -y gnupg software-properties-common wget

$ wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | tee /usr/share/keyrings/hashicorp-archive-keyring.gpg

$ gpg --no-default-keyring \
--keyring /usr/share/keyrings/hashicorp-archive-keyring.gpg \
--fingerprint
# Output
/usr/share/keyrings/hashicorp-archive-keyring.gpg
-------------------------------------------------
pub   rsa4096 2023-01-10 [SC] [expires: 2028-01-09]
      798A EC65 4E5C 1542 8C8E  42EE AA16 FCBC A621 E701
uid           [ unknown] HashiCorp Security (HashiCorp Package Signing) <security+packaging@hashicorp.com>
sub   rsa4096 2023-01-10 [S] [expires: 2028-01-09]

$ echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] \
https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
tee /etc/apt/sources.list.d/hashicorp.list

$ apt update

$ apt-get install -y terraform=1.6.6-1

$ terraform --version
Terraform v1.6.6
on linux_amd64
```

- `commit`을 이용하여 `base` 컨테이너를 저장합니다. 
  - 이미지 이름은 `cloudwave:practice.v1`으로 설정합니다. 

- 



### [실습-2] 컨테이너를 사용하여 `terraform` 코드 실행하기

> `terraform destroy` 명령어를 통해 전개된 AWS 인프라를 제거할 수 있습니다. 
>
> ! `volume`을 삭제하면 `terraform`을 통해 생성한 인프라를 관리할 수 없게 됩니다.

- `source_code`이름을 가진 `volume`을 생성합니다. 

- `GitSync` 컨테이너를 실행합니다.

  > https://github.com/kubernetes/git-sync/releases/tag/v4.1.0

  - 이미지는 `registry.k8s.io/git-sync/git-sync:v4.1.0`를 사용합니다.

  - `source_code` 볼륨을 `/code`에 마운트합니다.

  - 다음과 같이 환경 변수를 설정합니다. 

    ```tex
    GITSYNC_REPO=<REPO_URL>
    GITSYNC_ROOT=/code/git
    GITSYNC_REF=main
    GITSYNC_DEPTH=1
    GITSYNC_ONE_TIME=1
    ```

- `cloud_wave:practice.v1` 컨테이너를 실행합니다. 

  - `source_code` 볼륨을 `/terraform`에 마운트합니다.

  - 다음과 같이 환경 변수를 설정합니다.

    ``` tex
    AWS_ACCESS_KEY_ID=<ACCESS_KEY>
    AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
    AWS_DEFAULT_REGION=ap-northeast-2
    ```

- `exec`를 사용하여 `/terraform` 디렉토리에서 다음 명령어를 실행합니다. 

  ```shell
  # Terraform 사용을 위해 초기화 합니다. 
  terraform init
  # Infra 변경사항들을 보여줍니다.
  terraform plan
  # Infra 변경사항을 적용합니다. 
  terraform apply -var="aws_access_key=${AWS_ACCESS_KEY_ID}" -var="aws_secret_key=${AWS_SECRET_ACCESS_KEY}" -auto-approve
  ```

- `AWS Console`을 이용하여 생성된 인프라를 확인합니다.



### [실습-3] 실습용 이미지 제작하기

- `driver`가 `docker-container`인 `builder`를 생성합니다. 
- `cloudwave:base.v1`을 기반으로 `terraform`과 `awscli`를 설치하는 `Dockerfile`을 작성합니다. 
  - `[실습-1]`을 참고하여 작성하면 됩니다. 

- 위에서 생성한 `Dockerfile`을 이용하여 `multi-platform`용 이미지를 생성하고 `ECR` 업로드합니다.
  - 해당 이미지는 `linux/amd64`와 `linux/arm/v6`을 지원해야 합니다. 
  - 이미지의 이름은 `cloudwave:practice.v1`으로 설정합니다. 



### [실습-4] EC2 인스턴스에서 컨테이너 실행하기

- `ssh`를 이용하여 `EC2`에 접속합니다. 

```cmd
$ ssh -i <PATH> <IP>
```

- `EC2`에 `Docker`를 설치합니다. 
- `network`와 `volume`을 생성합니다.
  - `network` 이름은 `aws_net`으로 설정합니다. 
  - `driver`는 `bridge`로 설정합니다. 
- `volume`을 생성합니다.
  - `volume` 이름은 `pg_data`로 설정합니다. 
- 다음 조건을 만족하는 `DB` 컨테이너를 실행합니다.
  - 위에서 생성한 `aws_net`만 연결되어 있어야합니다.
  - `volume`을 `/var/lib/postgresql/data`에 마운트합니다. 
- 다음 조건을 만족하는 `PgAdmin` 컨테이너를 실행합니다. 
  - `aws_net`와 `default` 네트워크가 연결되어 있어야합니다.
  - 외부에서 접근할 수 있도록 `host`의 80 포트와 컨테이너의 80포트를 바인딩합니다. 
  - `POSTGRES_PASSWORD` 환경 변수를 설정합니다.
- `EC2`의 `IP`를 이용하여 `PgAdmin`에 접속합니다. 
  - `http://IP:80`
- `DB`를 연결합니다. 





# Docker Compose Practice



### [실습-1] 컨테이너를 사용하여 `terraform` 코드 실행하기

- `source_code`이름을 가진 `volume`을 생성합니다. 

- `GitSync` 서비스를 다음과 같이 정의합니다.

  - 이미지는 `registry.k8s.io/git-sync/git-sync:v4.1.0`를 사용합니다.

  - `source_code` 볼륨을 `/tmp`에 마운트합니다.

  - 다음과 같이 환경 변수를 설정합니다. 

    ```tex
    GITSYNC_REPO=https://github.com/matenduel/cloudwave
    GITSYNC_ROOT=/tmp/git
    GITSYNC_REF=main
    GITSYNC_DEPTH=1
    ```

- `terraform` 서비스를 다음과 같이 정의합니다. 

  - 이미지는 `cloud_wave:practice.v1`를 사용합니다.

  - `source_code` 볼륨을 `/terraform`에 마운트합니다.

  - 다음과 같이 환경 변수를 설정합니다.

    ``` tex
    AWS_ACCESS_KEY_ID=<ACCESS_KEY>
    AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
    AWS_SESSION_TOKEN=<SESSION_TOKEN>
    AWS_DEFAULT_REGION=ap-northeast-2
    ```

  - `depend_on`을 이용하여 `GitSync` 컨테이너가 생성된 다음 컨테이너가 실행되도록 설정하세요.

- 작성한 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다.

- `exec`를 사용하여 `/terraform` 디렉토리에서 다음 명령어를 실행합니다. 

  ```shell
  # Terraform 사용을 위해 초기화 합니다. 
  terraform init
  # Infra 변경사항들을 보여줍니다.
  terraform plan -var="aws_access_key=${AWS_ACCESS_KEY_ID}" -var="aws_secret_key=${AWS_SECRET_ACCESS_KEY}" -var="aws_session_token=${AWS_SESSION_TOKEN}"
  # Infra 변경사항을 적용합니다. 
  terraform apply -var="aws_access_key=${AWS_ACCESS_KEY_ID}" -var="aws_secret_key=${AWS_SECRET_ACCESS_KEY}" -var="aws_session_token=${AWS_SESSION_TOKEN}"
  ```



### [실습-2] EC2 인스턴스에서 컨테이너 실행하기

- 다음 명령어를 사용하여 파일을 생성합니다. 

  - `echo mysecretpassword > psql_pw.txt`

- `networks`를 다음과 같이 정의합니다.

  - 이름을 `private`로 설정합니다. 

- `volumes`를 다음과 같이 정의합니다. 

  - 이름을 `psql_data`로 설정합니다. 

- `secrets`를 다음과 같이 정의합니다. 

  - `psql_pw.txt` 파일을 사용하도록 설정합니다. 

- `psql` 서비스를 다음과 같이 정의합니다. 

  - 이미지는 `postgres:16.1-bullseye`를 사용합니다.

  - 포트를 `Expose`합니다

    - 5432

  - 다음과 같이 환경 변수를 설정합니다. 

    ```tex
    POSTGRES_PASSWORD_FILE=/run/secrets/postgres-passwd
    ```

  - `psql_data`볼륨을 `/var/lib/postgresql/data`에 마운트 합니다. 

  - `psql_pw` secret을 사용하도록 설정합니다.

  - `private` 네트워크에 2개의 `Alias`를 설정합니다. 

    ``` 
    psql
    db
    ```

- `pgadmin` 서비스를 다음과 같이 정의합니다. 

  - 이미지는 `dpage/pgadmin4:7.4`를 사용합니다.

  - 다음과 같이 환경 변수를 설정합니다. 

    ```tex
    PGADMIN_DEFAULT_EMAIL=user@sample.com
    PGADMIN_DEFAULT_PASSWORD=SuperSecret
    ```

  - `private` 네트워크를 사용하도록 설정합니다. 

  - 포트를 `publish`합니다. 

    - Host: 8000
    - Container: 80

  - `depend_on`을 이용하여 `psql` 서비스가 생성된 다음 컨테이너가 실행되도록 설정합니다.

- 작성한 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다.

- 웹 브라우저를 이용하여 `http://<Server IP>:8000`에 접속합니다. 

- `DB` 컨테이너의 `Alias`를 이용하여 `PgAdmin`에서 DB를 연결합니다. 





### [실습-3] `build`를 이용한 `docker compose` 프로젝트 생성하기

- `ubuntu:22.04`이미지에 `dnsutils`, `wget` 설치하기 by Dockerfile & build

- 다음 조건을 만족하는 `Dockerfile`을 작성합니다. 

  - `ubuntu:22.04`이미지를 베이스로 사용합니다. 
  - `apt-get update`와 `apt-get upgrade`를 수행해야 합니다. 
  - `apt`를 이용해서 `dnsutils`와 `wget`을 설치합니다.

- `ubuntu` 서비스를 다음과 같이 정의합니다.

  - 컨테이너의 이름을 `server`로 설정합니다. 
  - 이미지 이름은 `cloudwave:ubuntu.dig.v1`으로 설정합니다.
  - 위에서 만든 `Dockerfile`을 기반으로 빌드되도록 설정합니다. 

- `web_app` 서비스를 다음과 같이 정의합니다. 

  - `replicas`를 3으로 설정합니다.
  - 80번 포트를 `Expose` 합니다. 

- 작성한 `docker-compose.yaml`을 이용하여 이미지를 빌드한 다음 프로젝트를 실행합니다.

- `server` 컨테이너에 접속하여 다음 명령어를 실행합니다. 

  ```
  dig web_app
  ```



