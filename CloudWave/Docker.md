# Docker & Docker Compose

> 소개 ~~~



# Docker

## Docker란 무엇인가?

> 서비스의 추상화

https://docs.docker.com/get-started/overview/



## 장단점(Pro&Cons)

**장점**

- 



**단점**

- 



## Docker 구조 (Architecture)

### Image & Container

https://sunrise-min.tistory.com/entry/Docker-Container%EC%99%80-Image%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80



### Layer

https://woochan-autobiography.tistory.com/468



### 주의사항?

- Linux 이야기
- Platform 이야기

> arm vs amd 64

https://hub.docker.com/_/ubuntu/tags





Uname provides your Linux system information and kernel version

```cmd
$ uname -m
x86_64
```



Docker Image Arch 확인해보기

```cmd
$ docker inspect ubuntu
[
    {
        "Id": "sha256:b6548eacb0639263e9d8abfee48f8ac8b327102a05335b67572f715c580a968e",
        "RepoTags": [
            "ubuntu:latest"
        ],
        "RepoDigests": [
            "ubuntu@sha256:8eab65df33a6de2844c9aefd19efe8ddb87b7df5e9185a4ab73af936225685bb"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-12-01T07:49:50.47166003Z",
        "Container": "4ed7528f35fc250695e3f439165effcfb665cc9c3abb1f390d7e61b1d609e285",
        "ContainerConfig": {
            "Hostname": "4ed7528f35fc",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/sh",
                "-c",
                "#(nop) ",
                "CMD [\"/bin/bash\"]"
            ],
            "Image": "sha256:7c0bcaf4bbe2d12c08b11f40bbbe4934298aac7ce746f4ce4c2980d2d273a29b",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "DockerVersion": "20.10.21",
        "Author": "",
        "Config": {
            "Hostname": "",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            ],
            "Cmd": [
                "/bin/bash"
            ],
            "Image": "sha256:7c0bcaf4bbe2d12c08b11f40bbbe4934298aac7ce746f4ce4c2980d2d273a29b",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": null,
            "OnBuild": null,
            "Labels": {
                "org.opencontainers.image.ref.name": "ubuntu",
                "org.opencontainers.image.version": "22.04"
            }
        },
        "Architecture": "amd64",
        "Os": "linux",
        "Size": 77849732,
        "VirtualSize": 77849732,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/9da8d6e9eae15773c300c398a700253cf1f21742b9ed4784d2474fd29bb9c636/merged",
                "UpperDir": "/var/lib/docker/overlay2/9da8d6e9eae15773c300c398a700253cf1f21742b9ed4784d2474fd29bb9c636/diff",
                "WorkDir": "/var/lib/docker/overlay2/9da8d6e9eae15773c300c398a700253cf1f21742b9ed4784d2474fd29bb9c636/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:8ceb9643fb36a8ac65882c07e7b2fff9fd117673d6784221a83d3ad076a9733e"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]

```







## Docker Hub

> https://hub.docker.com/

Registry 설명

-> 왜 필요한가?

Github, Gitlab, Bitbucket 등과 빗대어서 설명하기





## 시작하기에 앞서

### Docker Desktop 설치

> https://docs.docker.com/engine/install/

#### Window

> https://docs.docker.com/desktop/install/windows-install/



#### Mac

> https://docs.docker.com/desktop/install/mac-install/



#### Linux

> https://docs.docker.com/desktop/install/linux-install/



## Dockerfile & 문법

#### Docker file 예제#1

```dockerfile
# Dockerfile
FROM apache/airflow:2.7.3

USER root

RUN apt-get update \
  && apt-get install -y --no-install-recommends \
         vim \
  && apt-get autoremove -yqq --purge \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/*

USER airflow
```



#### Docker file 예제#2

```dockerfile
FROM apache/airflow:2.7.3

ARG 

ENV

EXPOSE 8080

COPY

WORKDIR

RUN
```



### .dockerignore

- 사용 목적



## Command line (Docker CLI)

**version**

```cmd
# Window
$ docker version
Client:
 Cloud integration: v1.0.35+desktop.5
 Version:           24.0.6
 API version:       1.43
 Go version:        go1.20.7
 Git commit:        ed223bc
 Built:             Mon Sep  4 12:32:48 2023
 OS/Arch:           windows/amd64
 Context:           default

Server: Docker Desktop 4.25.2 (129061)
 Engine:
  Version:          24.0.6
  API version:      1.43 (minimum version 1.12)
  Go version:       go1.20.7
  Git commit:       1a79695
  Built:            Mon Sep  4 12:32:16 2023
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.6.22
  GitCommit:        8165feabfdfe38c65b599c4993d227328c231fca
 runc:
  Version:          1.1.8
  GitCommit:        v1.1.8-0-g82f18fe
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```



### Image 관련

**pull**

```cmd
# By Tag
$ docker pull <IMAGE_NAME>:<TAGS>

# By Digest
$ docker pull <IMAGE_NAME>:<DIGEST>
```





**list**

```cmd
# docker image ls [OPTIONS] [REPOSITORY[:TAG]]
$ docker image ls

# docker images [OPTIONS] [REPOSITORY[:TAG]]
$ docker images
```



```cmd
$ docker images --digests
REPOSITORY                         TAG       DIGEST                                                                    IMAGE ID       CREATED         SIZE
ubuntu                             <none>    sha256:38f5c03e32f9b3d2349bb3a096462866f8b44566b63a7fc3d5b4c75b55d108e1   246044f4effa   2 days ago      56.4MB
ubuntu                             latest    sha256:8eab65df33a6de2844c9aefd19efe8ddb87b7df5e9185a4ab73af936225685bb   b6548eacb063   2 days ago      77.8MB
```





**push**

```cmd
$ docker push <IMAGE_NAME>:<TAGS>
```



> Image_name에 host가 포함된 경우
>
> ex) **docker push** `aws_account_id.dkr.ecr.us-west-2.amazonaws.com`**/**`my-repository:tag`
>
> 





**rmi**

```cmd
# docker rmi [OPTIONS] IMAGE [IMAGE...]
$ docker rmi <IMAGE_ID>
```



**build**

```cmd
# docker build [OPTIONS] PATH | URL | - [-f <PATH_TO_FILE>]
$ docker build . [-f <PATH_TO_FILE>]

# or
$ docker buildx build [OPTIONS] PATH | URL | - [-f <PATH_TO_FILE>]
```



**tag**

```cmd
# docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
$ docker tag <SOURCE_IMAGE> <TARGET_IMAGE>
```







### Conatiner 관련

**run**

```cmd
$ docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```



**ps**

```cmd
$ docker ps [OPTIONS]
```



**rm**

```cmd
$ docker rm [OPTIONS] CONTAINER [CONTAINER...]
```



**top**

```cmd
$ docker top CONTAINER [ps OPTIONS]
```



**attach**

```cmd
$ docker attach [OPTIONS] CONTAINER
```



```cmd

```



### Volume 관련

**create**

```cmd

```

**inspect**

```cmd

```

**ls**

```cmd

```

**prune**

```cmd

```

**rm**

```cmd

```



### Network 관련

**connect**

> Connect a container to a network

```cmd

```

**disconnect**

> Disconnect a container from a network

```cmd

```

**create**

> Create a network

```cmd

```

**inspect**

> Display detailed information on one or more networks

```cmd

```

**ls**

> List networks

```cmd

```

**prune**

> Remove all unused networks

```cmd

```

**rm**

> Remove one or more networks

```cmd

```



### Buildx 관련

**buildx**

```cmd

```



#### Options

-d, --detach



-e, --env



--name



--rm



-i



-t



-v, --version 

> mount



-p

**port**



## 실습 & 연습문제

#### 이미지 Pull 해보기 

- By name
- with specific tags
- with digest



- Ubuntu22.04
- Postgresql
- dpage/pgadmin4

#### 이미지 삭제해보기





#### 이미지 Build 해보기

- Ubuntu에 ~~을 설치하기
- ~~ 코드/스크립트를 복사하기



#### Tag 변경하기



#### Container Shell에 접근하기



#### `.dockerignore`를 사용하여 특정 파일 제외하기



#### ARG를 활용하여 base Image를 변경(?)하기





#### Image를 이용하여 Application 배포하기

- PgAdmin4



#### Volume을 이용하여 (DB) 데이터 보존하기











# Docker Compose

## 사용 이유

### 컨테이너 관리의 필요성

- 서비스는 일반적으로 여러개의 서버로 구성됨(Web, DB, Cache, ...)
- 필요한 갯수만큼 항상 동작되고 있어야함. (종료된 경우에도 다시 실행되어야함)
- 



### 장점 & 단점

**장점**



**단점**



## Docker Compose CLI

#### up

```
```



#### down



#### stop



#### logs



#### restart



#### top





## YAML 구조

#### Docker Compose file 예제#1

> TODO Web server 예제로 교체 필요함 

```yaml
version: '3.8'
x-airflow-common:
  &airflow-common
  # In order to add custom dependencies or upgrade provider packages you can use your extended image.
  # Comment the image line, place your Dockerfile in the directory where you placed the docker-compose.yaml
  # and uncomment the "build" line below, Then run `docker-compose build` to build the images.
  image: ${AIRFLOW_IMAGE_NAME:-apache/airflow:2.7.1}
  # build: .
  environment:
    &airflow-common-env
    AIRFLOW__CORE__EXECUTOR: CeleryExecutor
    AIRFLOW__DATABASE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    # For backward compatibility, with Airflow <2.3
    AIRFLOW__CORE__SQL_ALCHEMY_CONN: postgresql+psycopg2://airflow:airflow@postgres/airflow
    AIRFLOW__CELERY__RESULT_BACKEND: db+postgresql://airflow:airflow@postgres/airflow
    AIRFLOW__CELERY__BROKER_URL: redis://:@redis:6379/0
    AIRFLOW__CORE__FERNET_KEY: ''
    AIRFLOW__CORE__DAGS_ARE_PAUSED_AT_CREATION: 'true'
    AIRFLOW__CORE__LOAD_EXAMPLES: 'true'
    AIRFLOW__API__AUTH_BACKENDS: 'airflow.api.auth.backend.basic_auth,airflow.api.auth.backend.session'
    # yamllint disable rule:line-length
    # Use simple http server on scheduler for health checks
    # See https://airflow.apache.org/docs/apache-airflow/stable/administration-and-deployment/logging-monitoring/check-health.html#scheduler-health-check-server
    # yamllint enable rule:line-length
    AIRFLOW__SCHEDULER__ENABLE_HEALTH_CHECK: 'true'
    # WARNING: Use _PIP_ADDITIONAL_REQUIREMENTS option ONLY for a quick checks
    # for other purpose (development, test and especially production usage) build/extend Airflow image.
    _PIP_ADDITIONAL_REQUIREMENTS: ${_PIP_ADDITIONAL_REQUIREMENTS:-}
  volumes:
    - airflow:/opt/airflow/dags
    - airflow:/opt/airflow/logs
    - airflow:/opt/airflow/config
    - airflow:/opt/airflow/plugins
  user: "${AIRFLOW_UID:-50000}:0"
  depends_on:
    &airflow-common-depends-on
    redis:
      condition: service_healthy
    postgres:
      condition: service_healthy

services:
  postgres:
    image: postgres:13
    environment:
      POSTGRES_USER: airflow
      POSTGRES_PASSWORD: airflow
      POSTGRES_DB: airflow
    volumes:
      - postgres-db-volume:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD", "pg_isready", "-U", "airflow"]
      interval: 10s
      retries: 5
      start_period: 5s
    restart: always

  redis:
    image: redis:latest
    expose:
      - 6379
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 30s
      retries: 50
      start_period: 30s
    restart: always

  airflow-webserver:
    <<: *airflow-common
    command: webserver
    ports:
      - "8080:8080"
    healthcheck:
      test: ["CMD", "curl", "--fail", "http://localhost:8080/health"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 30s
    restart: always
    depends_on:
      <<: *airflow-common-depends-on
      airflow-init:
        condition: service_completed_successfully

  airflow-scheduler:
    <<: *airflow-common
    command: scheduler
    healthcheck:
      test: ["CMD", "curl", "--fail", "http://localhost:8974/health"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 30s
    restart: always
    depends_on:
      <<: *airflow-common-depends-on
      airflow-init:
        condition: service_completed_successfully

  airflow-worker:
    <<: *airflow-common
    command: celery worker
    healthcheck:
      # yamllint disable rule:line-length
      test:
        - "CMD-SHELL"
        - 'celery --app airflow.providers.celery.executors.celery_executor.app inspect ping -d "celery@$${HOSTNAME}" || celery --app airflow.executors.celery_executor.app inspect ping -d "celery@$${HOSTNAME}"'
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 30s
    environment:
      <<: *airflow-common-env
      # Required to handle warm shutdown of the celery workers properly
      # See https://airflow.apache.org/docs/docker-stack/entrypoint.html#signal-propagation
      DUMB_INIT_SETSID: "0"
    restart: always
    depends_on:
      <<: *airflow-common-depends-on
      airflow-init:
        condition: service_completed_successfully

  airflow-triggerer:
    <<: *airflow-common
    command: triggerer
    healthcheck:
      test: ["CMD-SHELL", 'airflow jobs check --job-type TriggererJob --hostname "$${HOSTNAME}"']
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 30s
    restart: always
    depends_on:
      <<: *airflow-common-depends-on
      airflow-init:
        condition: service_completed_successfully

  airflow-init:
    <<: *airflow-common
    entrypoint: /bin/bash
    # yamllint disable rule:line-length
    command:
      - -c
      - |
        function ver() {
          printf "%04d%04d%04d%04d" $${1//./ }
        }
        airflow_version=$$(AIRFLOW__LOGGING__LOGGING_LEVEL=INFO && gosu airflow airflow version)
        airflow_version_comparable=$$(ver $${airflow_version})
        min_airflow_version=2.2.0
        min_airflow_version_comparable=$$(ver $${min_airflow_version})
        if (( airflow_version_comparable < min_airflow_version_comparable )); then
          echo
          echo -e "\033[1;31mERROR!!!: Too old Airflow version $${airflow_version}!\e[0m"
          echo "The minimum Airflow version supported: $${min_airflow_version}. Only use this or higher!"
          echo
          exit 1
        fi
        if [[ -z "${AIRFLOW_UID}" ]]; then
          echo
          echo -e "\033[1;33mWARNING!!!: AIRFLOW_UID not set!\e[0m"
          echo "If you are on Linux, you SHOULD follow the instructions below to set "
          echo "AIRFLOW_UID environment variable, otherwise files will be owned by root."
          echo "For other operating systems you can get rid of the warning with manually created .env file:"
          echo "    See: https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html#setting-the-right-airflow-user"
          echo
        fi
        one_meg=1048576
        mem_available=$$(($$(getconf _PHYS_PAGES) * $$(getconf PAGE_SIZE) / one_meg))
        cpus_available=$$(grep -cE 'cpu[0-9]+' /proc/stat)
        disk_available=$$(df / | tail -1 | awk '{print $$4}')
        warning_resources="false"
        if (( mem_available < 4000 )) ; then
          echo
          echo -e "\033[1;33mWARNING!!!: Not enough memory available for Docker.\e[0m"
          echo "At least 4GB of memory required. You have $$(numfmt --to iec $$((mem_available * one_meg)))"
          echo
          warning_resources="true"
        fi
        if (( cpus_available < 2 )); then
          echo
          echo -e "\033[1;33mWARNING!!!: Not enough CPUS available for Docker.\e[0m"
          echo "At least 2 CPUs recommended. You have $${cpus_available}"
          echo
          warning_resources="true"
        fi
        if (( disk_available < one_meg * 10 )); then
          echo
          echo -e "\033[1;33mWARNING!!!: Not enough Disk space available for Docker.\e[0m"
          echo "At least 10 GBs recommended. You have $$(numfmt --to iec $$((disk_available * 1024 )))"
          echo
          warning_resources="true"
        fi
        if [[ $${warning_resources} == "true" ]]; then
          echo
          echo -e "\033[1;33mWARNING!!!: You have not enough resources to run Airflow (see above)!\e[0m"
          echo "Please follow the instructions to increase amount of resources available:"
          echo "   https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html#before-you-begin"
          echo
        fi
        mkdir -p /sources/logs /sources/dags /sources/plugins
        chown -R "${AIRFLOW_UID}:0" /sources/{logs,dags,plugins}
        exec /entrypoint airflow version
    # yamllint enable rule:line-length
    environment:
      <<: *airflow-common-env
      _AIRFLOW_DB_MIGRATE: 'true'
      _AIRFLOW_WWW_USER_CREATE: 'true'
      _AIRFLOW_WWW_USER_USERNAME: ${_AIRFLOW_WWW_USER_USERNAME:-airflow}
      _AIRFLOW_WWW_USER_PASSWORD: ${_AIRFLOW_WWW_USER_PASSWORD:-airflow}
      _PIP_ADDITIONAL_REQUIREMENTS: ''
    user: "0:0"
    volumes:
      - airflow:/sources

  airflow-cli:
    <<: *airflow-common
    profiles:
      - debug
    environment:
      <<: *airflow-common-env
      CONNECTION_CHECK_MAX_COUNT: "0"
    # Workaround for entrypoint issue. See: https://github.com/apache/airflow/issues/16252
    command:
      - bash
      - -c
      - airflow

  # You can enable flower by adding "--profile flower" option e.g. docker-compose --profile flower up
  # or by explicitly targeted on the command line e.g. docker-compose up flower.
  # See: https://docs.docker.com/compose/profiles/
  flower:
    <<: *airflow-common
    command: celery flower
    ports:
      - "5555:5555"
    healthcheck:
      test: ["CMD", "curl", "--fail", "http://localhost:5555/"]
      interval: 30s
      timeout: 10s
      retries: 5
      start_period: 30s
    restart: always
    depends_on:
      <<: *airflow-common-depends-on
      airflow-init:
        condition: service_completed_successfully

volumes:
  postgres-db-volume:
  airflow:
```



#### Docker Compose file 예제#2

```yaml
```





## 기능

### Volume

### Network

### Secret

- https://docs.docker.com/compose/use-secrets/

### depend_on (StartUp)

### Profile



## 실습 & 연습문제

#### 서비스 배포하기



#### 2개의 서비스를 동일 Network로 묶기

> 별도의 expose없이 DB 접근하기, ...

- PostgreSQL + PgAdmin



#### Volume을 이용하여 Local File system을 이용하지 않고 파일 공유하기

-> Git Sync



#### depend_on을 이용하여 초기 설정 세팅하기?

-> 소규모 Web Server (Java? Python?)





















