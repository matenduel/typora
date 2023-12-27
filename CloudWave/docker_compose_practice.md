## 2. Docker Compose

- https://docs.docker.com/compose/production/
- 



## Compose CLI

> https://docs.docker.com/engine/reference/commandline/compose/

### 시작하기 전에

> https://docs.docker.com/compose/reference/

```
Usage:  docker compose [OPTIONS] COMMAND

Define and run multi-container applications with Docker.

Options:
      --ansi string                Control when to print ANSI control characters ("never"|"always"|"auto") (default "auto")
      --compatibility              Run compose in backward compatibility mode
      --env-file stringArray       Specify an alternate environment file
  -f, --file stringArray           Compose configuration files
      --parallel int               Control max parallelism, -1 for unlimited (default -1)
      --profile stringArray        Specify a profile to enable
      --project-directory string   Specify an alternate working directory
                                   (default: the path of the, first specified, Compose file)
  -p, --project-name string        Project name
```



**주요 Options**

| Instruction    | Short | Description                                |
| :------------- | ----- | :----------------------------------------- |
| `env-file`     |       | Add local or remote files and directories. |
| `file`         | `-f`  | Use build-time variables.                  |
| `profile`      |       | Specify a profile to enable                |
| `project-name` | `-p`  | Project name                               |



#### 주의사항

- `-f`를 통해 사용할 `compose` 파일을 지정하지 않는 경우, `docker-compose.yml` 또는 `docker-compose.yaml` 파일이 사용됩니다. 
- `Docker compose`파일의 `version`에 따라서 사용할 수 있는 기능과 변수명이 달라질 수 있습니다. 
  따라서, 서버에 설치된 `Docker Engine`의 버젼과 호환되는지 체크하는 것이 좋습니다. 
- 



| **Compose file format** | **Docker Engine release** |
| ----------------------- | ------------------------- |
| Compose specification   | 19.03.0+                  |
| 3.8                     | 19.03.0+                  |
| 3.7                     | 18.06.0+                  |
| 3.6                     | 18.02.0+                  |
| 3.5                     | 17.12.0+                  |
| 3.4                     | 17.09.0+                  |
| 3.3                     | 17.06.0+                  |
| 3.2                     | 17.04.0+                  |
| 3.1                     | 1.13.1+                   |
| 3.0                     | 1.13.0+                   |
| 2.4                     | 17.12.0+                  |
| 2.3                     | 17.06.0+                  |
| 2.2                     | 1.13.0+                   |
| 2.1                     | 1.12.0+                   |
| 2.0                     | 1.10.0+                   |



### 버젼 확인

> https://docs.docker.com/engine/reference/commandline/compose_version/

```
docker compose version [OPTIONS]
```





### 서비스 실행

> https://docs.docker.com/engine/reference/commandline/compose_up/

```sh
docker compose up [OPTIONS] [SERVICE...]
```



서비스 삭제?

> https://docs.docker.com/engine/reference/commandline/compose_down/

```sh
docker compose down [OPTIONS] [SERVICES]

```



서비스 중지

> https://docs.docker.com/engine/reference/commandline/compose_stop/

```
docker compose stop [OPTIONS] [SERVICE...]

```

-> 삭제 x



서비스 실행

> https://docs.docker.com/engine/reference/commandline/compose_start/
>
> start -> 컨테이너 실행

```
docker compose start [SERVICE...]

```





서비스 빌드

> https://docs.docker.com/engine/reference/commandline/compose_build/

```sh
docker compose build [OPTIONS] [SERVICE...]

```





컴포즈 프로젝트 목록

> https://docs.docker.com/engine/reference/commandline/compose_ls/

```sh
docker compose ls [OPTIONS]
```



컴포즈 컨테이너 목록

> https://docs.docker.com/engine/reference/commandline/compose_ps/

```
docker compose ps [OPTIONS] [SERVICE...]

```





컴포즈 이미지 목록

> https://docs.docker.com/engine/reference/commandline/compose_images/

```
docker compose images [OPTIONS] [SERVICE...]
```



재실행

> https://docs.docker.com/engine/reference/commandline/compose_restart/

```
docker compose restart [OPTIONS] [SERVICE...]

```

Restarts all stopped and running services, or the specified services only.

If you make changes to your `compose.yml` configuration, these changes are not reflected after running this command. For example, changes to environment variables (which are added after a container is built, but before the container's command is executed) are not updated after restarting.



로그 확인

> https://docs.docker.com/engine/reference/commandline/compose_logs/

```
docker compose logs [OPTIONS] [SERVICE...]

```



프로세스 확인

> https://docs.docker.com/engine/reference/commandline/top/

```
docker top CONTAINER [ps OPTIONS]

```



변경사항 체크?

> https://docs.docker.com/engine/reference/commandline/compose_watch/

```
docker compose watch [SERVICE...]

```



빌드하기

> https://docs.docker.com/engine/reference/commandline/compose_build/

```
docker compose build [OPTIONS] [SERVICE...]

```



정지한 컨테이너 삭제

> https://docs.docker.com/engine/reference/commandline/compose_rm/

```
docker compose rm [OPTIONS] [SERVICE...]

```





run -> 명령어 실행

- exec랑 차이점은?



### 연습문제

#### [연습] 프로젝트 실행 및 `Network` 확인하기

다음 파일을 `example_1.yaml`로 저장합니다. 

```yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - "apt-get update && apt-get upgrade && apt-get install -y curl && sleep 3600"
    restart: no

  nginx:
    image: nginx:latest
    expose:
      - 80
    restart: always
```



명령어를 이용하여 `project`를 실행합니다. 

```cmd
$ docker compose -f example_1.yaml -p ex1 up -d
[+] Building 0.0s (0/0) 
[+] Running 3/3
 ✔ Network ex1_default     Created 
 ✔ Container ex1-nginx-1   Started 
 ✔ Container ex1-ubuntu-1  Started   
```



다음과 같이 `ex1_default`라는 네트워크가 새로 생성된 것을 확인할 수 있습니다. 

```cmd
$ docker network ls
NETWORK ID     NAME             DRIVER    SCOPE
50f36ebc860b   bridge           bridge    local
5480e9b993ea   ex1_default      bridge    local
```



다음과 같이 생성된 `ex1-ubuntu-1` 컨테이너의 정보를 조회하면 `docker run`을 통해서 실행하였을 때와 다른점을 확인해볼 수 있습니다. 

- `default` 네트워크가 아닌 `ex1_default` 네트워크만 할당되어 있습니다. 
- `service`의 이름과 컨테이너의 이름이 `alias`로 등록되어 있는 것을 확인할 수 있습니다. 

```cmd
$ docker inspect ex1-ubuntu-1 -f "{{ println .NetworkSettings.Networks }}"
map[ex1_default:0xc000000240]

$ docker inspect ex1-ubuntu-1 -f "Alias:{{ println .NetworkSettings.Networks.ex1_default.Aliases }}IP:{{ println .NetworkSettings.Networks.ex1_default.IPAddress }}"
Alias:[ex1-ubuntu-1 ubuntu f5cfe44b6bee]
IP:172.21.0.3
```


실제로 `ubuntu` 서버에서 다음과 같이 `curl` 명령어를 사용하면, 정상적으로 `nginx` 서버에서 결과를 얻어 오는 것을 확인할 수 있습니다. 

```cmd
$ curl nginx:80
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```



#### [연습] 프로젝트 업데이트

위에서 생성한 프로젝트의 `nginx`는 포트가 열려있지 않으므로 `host`에서 다음과 같이 접근할 수 없는 상태입니다. 

```cmd
$ curl localhost:80
curl: (7) Failed to connect to localhost port 80 after 2237 ms: Couldn't connect to server
```



`host`에서도 접근할 수 있도록, 다음과 같이 `example_1.yaml`를 수정합니다. 

```yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - "apt-get update && apt-get upgrade && apt-get install -y curl && sleep 3600"
    restart: no

  nginx:
    image: nginx:latest
    # expose 대신 ports를 사용합니다. 
    ports:
      - 80:80
    restart: always
```



다음 명령어를 이용하여 프로젝트를 업데이트 합니다. 

```cmd
$ docker compose -f example_1.yaml -p ex1 up -d
```



다시 `host`에서 접근을 시도하면 다음과 같이 정상적으로 접근이 되는 것을 확인할 수 있습니다. 

```cmd
$ curl localhost:80
```





#### [연습] 마지막 5개 로그만 확인하기

```cmd
$ docker compose -p ex1 logs -n 5 
ex1-ubuntu-1  | Processing triggers for ca-certificates (20230311ubuntu0.22.04.1) ...
ex1-nginx-1   | 2023/12/25 16:17:36 [notice] 1#1: start worker process 41
ex1-nginx-1   | 2023/12/25 16:17:36 [notice] 1#1: start worker process 42
ex1-nginx-1   | 2023/12/25 16:17:36 [notice] 1#1: start worker process 43
ex1-nginx-1   | 2023/12/25 16:17:36 [notice] 1#1: start worker process 44
ex1-ubuntu-1  | Updating certificates in /etc/ssl/certs...
ex1-ubuntu-1  | 0 added, 0 removed; done.
ex1-ubuntu-1  | Running hooks in /etc/ca-certificates/update.d...
ex1-ubuntu-1  | done.
ex1-nginx-1   | 172.21.0.3 - - [25/Dec/2023:16:27:38 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/7.81.0" "-"
```





[연습] 

[연습] 

[연습] 



## Compose file - Basic

> https://docs.docker.com/compose/compose-file/03-compose-file/

`docker-compose`를 위한 `configuration` 파일은 `YAML`을 이용하여 작성합니다. 



`Python`과 같이 들여쓰기를 기반으로 구조를 결정합니다. 



**`docker-compose.yaml` 파일 예시**

```yaml
version: "3.8"

services:
  frontend:
    image: example/webapp
    ports:
      - "443:8043"
    networks:
      - front-tier
      - back-tier
    configs:
      - httpd-config
    secrets:
      - server-certificate

  backend:
    image: example/database
    volumes:
      - db-data:/etc/data
    networks:
      - back-tier

volumes:
  db-data:
    driver: flocker
    driver_opts:
      size: "10GiB"

configs:
  httpd-config:
    external: true

secrets:
  server-certificate:
    external: true

networks:
  # The presence of these objects is sufficient to define them
  front-tier: {}
  back-tier: {}
```

### Version



### Service



### volume



### network



### config



### secret



### 연습 문제

#### Docker compose에서 build 사용하기

``` yaml
version: '3.8'

services:
  ubuntu:
    container_name: server
    build:
      context: .
      dockerfile_inline: |
        FROM ubuntu:22.04
        RUN apt-get update && apt-get upgrade && apt-get install -y curl
    image: cloudwave/compose:inline_build.v1
    restart: no

  nginx:
    image: nginx:latest
    expose:
      - 80
    restart: always
```



```cmd
$ docker compose -f example_1.yaml -p ex1 up -d --build
```









## Compose file - Advance

### .env



### profile



### depend_on



### build



### health check 

https://docs.docker.com/compose/startup-order/



### Secret관리

> https://docs.docker.com/compose/use-secrets/



### volume

- ro
- rw



### 연습문제



[연습] depend_on을 이용하여 db 생성 후 application 실행하기



[연습] health_check 사용하기



[연습] .env 파일 및 secret 관리



[연습] build를 사용하여 `base`이미지에 패키지 추가로 설치하기



[연습] `network`를 이용하여 서로다른 프로젝트를 연결하기



[연습] `profile` 예제 -> like airflow





## 종합 문제

[실습-1] `cloudwave:base.v1`에 `terraform` 설치하기





 [실습-2] 컨테이너를 사용하여 `terraform` 코드 실행하기





[실습 -3] Web & DB로 구성된 docker compose 구성하기 + PgAdmin은 profile로 구상 + Init용 컨테이너 추가







