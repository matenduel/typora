

## 2. Docker Compose

[TOC]



## 2.1. Compose CLI

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



**주요 옵션**

| Instruction    | Short | Description                           |
| :------------- | ----- | :------------------------------------ |
| `env-file`     |       | 환경 변수를 저장한 파일을 설정합니다. |
| `file`         | `-f`  | 사용할 `compose` 파일을 지정합니다.   |
| `profile`      |       | 사용할 프로파일을 지정합니다.         |
| `project-name` | `-p`  | 프로젝트 이름을 설정합니다.           |



#### 주의사항

- `-f`를 통해 사용할 `compose` 파일을 지정하지 않는 경우, `docker-compose.yml` 또는 `docker-compose.yaml` 파일이 사용됩니다. 
- `Docker compose`파일의 `version`에 따라서 사용할 수 있는 기능과 변수명이 달라질 수 있습니다. 
  따라서, 서버에 설치된 `Docker Engine`의 버젼과 호환되는지 체크하는 것이 좋습니다. 
- 프로젝트 이름이 설정되지 않은 경우, 현재 디렉토리 이름이 프로젝트 이름으로 설정됩니다. 



#### Compose file 버전별 호환 목록

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



### `docker compose` 버젼 확인

> https://docs.docker.com/engine/reference/commandline/compose_version/

```
docker compose version [OPTIONS]
```

```cmd
$ docker compose version
Docker Compose version v2.18.1
```



### 프로젝트 실행

> https://docs.docker.com/engine/reference/commandline/compose_up/

```sh
docker compose up [OPTIONS] [SERVICE...]
```



**주요 옵션**

| Option                      | Short | Default | Description                                                  |
| --------------------------- | ----- | ------- | ------------------------------------------------------------ |
| `--abort-on-container-exit` |       |         | 1개 이상의 컨테이너가 멈춘(`stopped`) 경우, 모든 컨테이너를 종료합니다.<br />`-d`와는 함께 사용할 수 없습니다. |
| `--build`                   |       |         | 컨테이너 실행 전에 이미지를 빌드합니다.                      |
| `--detach`                  | `-d`  |         | 컨테이너를 백그라운드에서 실행시킵니다.                      |
| `--force-recreate`          |       |         | 변경 여부와 관계없이 모든 컨테이너를 재생성합니다.           |
| `--remove-orphans`          |       |         | `compose` 파일에 정의되어 있지 않은 서비스를 위한 컨테이너를 삭제합니다. |



**주의사항**

- 서로 다른  `docker-compose.yaml`의 프로젝트 명이 중복된 경우, 각 파일에 명시된 모든  `component`들이 적용됩니다. 



#### [예시] 프로젝트 백그라운드로 실행하기

```cmd
$ docker compose up -d
[+] Building 0.0s (0/0)                                                                                      
[+] Running 6/6
 ✔ Network private                  Created                        0.0s
 ✔ Network db                       Created                        0.0s
 ✔ Volume "db-data"                 Created                        0.0s
 ✔ Container cloud_wave-frontend-1  Started                        0.6s
 ✔ Container cloud_wave-server-1    Started                        0.7s
 ✔ Container db_postgres            Started                        0.6s
```



#### [예시] `--abort-on-container-exit` 사용하기

```yaml
# docker-compose.yaml
version: '3.8'
name: 'cloudwave'

services:
  success:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private: {}

  fail:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep fail
    networks:
      private: {}

networks:
  private:
```



`fail` 서비스를 위한 컨테이너가 정상적으로 실행되지 않으므로, 다음과 같이 프로젝트의 모든 컨테이너가 종료되는 것을 확인할 수 있습니다. 

```cmd
$ docker compose up --abort-on-container-exit
[+] Building 0.0s (0/0)                                                                    docker:desktop-linux
[+] Running 2/0
 ✔ Container cloudwave-success-1  Created                                                                  0.0s 
 ✔ Container cloudwave-fail-1     Created                                                                  0.0s 
Attaching to cloudwave-fail-1, cloudwave-success-1
cloudwave-fail-1     | sleep: invalid time interval 'fail'
cloudwave-fail-1     | Try 'sleep --help' for more information.
cloudwave-fail-1 exited with code 1
Aborting on container exit...
[+] Stopping 2/2
 ✔ Container cloudwave-success-1  Stopped                                                                 10.1s 
 ✔ Container cloudwave-fail-1     Stopped                                                                  0.0s 
```





### 프로젝트 목록

> https://docs.docker.com/engine/reference/commandline/compose_ls/

```sh
docker compose ls [OPTIONS]
```



**주요 옵션**

| Option     | Default | Short | Description                                          |
| ---------- | ------- | ----- | ---------------------------------------------------- |
| `--all`    |         | `-a`  | 종료된 프로젝트를 포함한 모든 프로젝트를 표시합니다. |
| `--filter` |         |       | 필터 조건을 추가합니다.                              |
| `--quiet`  |         | `-q`  | 프로젝트의 이름만 표기합니다.                        |



#### [예시] 이름에 `cloud`가 포함된 모든 프로젝트 목록 보기

```cmd
$ docker compose ls --filter "name=cloud" -a
NAME               STATUS              CONFIG FILES
cloud_wave         running(3)          <PROJECT_PATH>/docker-compose.yaml
cloud_wave2        exited(2)           <PROJECT_PATH>/docker-compose.yaml
```



### 컨테이너 목록

> https://docs.docker.com/engine/reference/commandline/compose_ps/

```
docker compose ps [OPTIONS] [SERVICE...]
```



**주요 Options**

| Option       | Short | Default | Description                                                  |
| ------------ | ----- | ------- | ------------------------------------------------------------ |
| `--all`      | `-a`  |         | 종료된 컨테이너를 포함한 모든 컨테이너를 표시합니다.         |
| `--quiet`    | `-q`  |         | 컨테이너 ID만 표기합니다.                                    |
| `--services` |       |         | 서비스만 표기합니다.                                         |
| `--status`   |       |         | 상태값을 기반으로 서비스를 필터링합니다. <br />[paused \| restarting \| removing \| running \| dead \| created \| exited] |



**주의 사항**

- CLI를 통해 전달된 `project-name`또는  `configuration file`에 명시된 `name`을 기반으로 조회합니다.  



#### [예시] 이름이 `cloud_wave`인 프로젝트의 실행중인 컨테이너 목록 보기

```cmd
$ docker compose -p cloud_wave ps
NAME                    IMAGE                    COMMAND                  SERVICE             CREATED             STATUS              PORTS
cloud_wave-frontend-1   nginx:latest             "/docker-entrypoint.…"   frontend            21 minutes ago      Up 21 minutes       0.0.0.0:80->80/tcp
cloud_wave-server-1     ubuntu:22.04             "/bin/bash -c 'sleep…"   server              21 minutes ago      Up 21 minutes       
db_postgres             postgres:16.1-bullseye   "docker-entrypoint.s…"   postgres            19 minutes ago      Up 19 minutes       5432/tcp
```



### 이미지 목록

> https://docs.docker.com/engine/reference/commandline/compose_images/

```
docker compose images [OPTIONS] [SERVICE...]
```



**주의 사항**

- CLI를 통해 전달된 `project-name`또는  `configuration file`에 명시된 `name`을 기반으로 조회합니다.  



#### [예시] 이름이 `cloud_wave`인 프로젝트의 이미지 목록 보기

```cmd
$ docker compose images
CONTAINER               REPOSITORY          TAG                 IMAGE ID            SIZE
cloud_wave-frontend-1   nginx               latest              d453dd892d93        187MB
cloud_wave-server-1     ubuntu              22.04               174c8c134b2a        77.9MB
db_postgres             postgres            16.1-bullseye       aab92def9ff2        384MB
```



### 프로젝트 삭제

> https://docs.docker.com/engine/reference/commandline/compose_down/

```sh
docker compose down [OPTIONS] [SERVICES]
```



**주요 옵션**

| Option             | Short | Default | Description                                                  |
| ------------------ | ----- | ------- | ------------------------------------------------------------ |
| `--remove-orphans` |       |         | `Compose` 파일에 정의되지 않은 서비스를 구성하는 컨테이너도 삭제합니다. |
| `--volumes`        | `-v`  |         | `Copose` 파일에 정의된 `anonymous` 볼륨을 같이 삭제합니다.   |



**주의 사항**

- 현재 디렉토리에 `docker-compose.yaml` 파일이 존재하지 않는 경우, `-p` 또는 `-f`를 이용하여야 합니다. 
- `--remove-orphans`를 사용하지 않을 경우, `compose` 파일에 정의된 서비스를 구성하는 컨테이너만 삭제합니다. 
- `external`로 정의된 `network`와 `volume`은 삭제되지 않습니다. 
- 다른 프로젝트에서 사용중인 리소스(`volume`, `network`, ...)는 삭제되지 않습니다. 



#### [예시] 이름이 `cloud_wave`인 프로젝트 삭제하기

```cmd
$ docker compose -p cloud_wave down
[+] Running 3/3
 ✔ Container cloud_wave-postgres-1  Removed                                                               0.1s 
 ✔ Container cloud_wave-frontend-1  Removed                                                               0.0s 
 ✔ Container cloud_wave-server-1    Removed                                                               10.1s 
```



#### [예시] 프로젝트 삭제시 `volume`도 함께 제거하기

```yaml
# docker-compose.yaml
version: '3.8'
name: 'cloud_wave'

services:
  success:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private: {}
    volumes:
      - anonymous:/anonymous
      - named:/named
      - external:/external

networks:
  private:

volumes:
  anonymous:
  named:
    name: "named_volume"
  external:
    name: "external"
    external: true
```



`--volumes`을 사용하는 경우, 다음과 같이 `external`로 설정된 볼륨을 제외한 나머지 2개 볼륨이 함께 삭제되는 것을 확인할 수 있습니다.  

```cmd
$ docker compose -p cloud_wave down --volumes
[+] Running 5/5
 ✔ Container cloud_wave-fail-1     Removed                                                                 0.0s 
 ✔ Container cloud_wave-success-1  Removed                                                                10.1s 
 ✔ Volume named_volume             Removed                                                                 0.0s 
 ✔ Volume cloud_wave_anonymous     Removed                                                                 0.0s 
 ✔ Network cloud_wave_private      Removed                                                                 0.1s 
```



### 재실행

> https://docs.docker.com/engine/reference/commandline/compose_restart/

```shell
docker compose restart [OPTIONS] [SERVICE...]
```



**주의 사항**

- 서비스를 명시하지 않은 경우, 모든 서비스에 대해서 종료된 컨테이너를 포함하여 재실행됩니다. 
- 반영되지 않은 변경사항이 `compose` 파일에 존재하더라도, 해당 변경사항은 **반영되지 않습니다.** 



#### [예시]  `cloud_wave` 프로젝트의 모든 서비스 재실행하기

```cmd
$ docker compose -p cloud_wave restart
[+] Restarting 3/3
 ✔ Container cloud_wave-frontend-1  Started                                                                0.5s 
 ✔ Container db_postgres            Started                                                                0.5s 
 ✔ Container cloud_wave-server-1    Started                                                                0.3s
```



#### [예시] `cloud_wave` 프로젝트의 특정 서비스만 재실행하기

```cmd
$ docker compose -p cloud_wave restart server
[+] Restarting 1/1
 ✔ Container cloud_wave-server-1  Started                                                                    0.1s 
```





### 종료 & 시작

> https://docs.docker.com/engine/reference/commandline/compose_stop/
>
> https://docs.docker.com/engine/reference/commandline/compose_start/

```shell
# 종료
docker compose stop [OPTIONS] [SERVICE...]
# 시작
docker compose start [SERVICE...]
```



#### [예시]  `cloud_wave` 프로젝트 종료하기

```cmd
$  docker compose -p cloud_wave stop
[+] Stopping 2/3
 ✔ Container cloud_wave-postgres-1  Stopped                                                                0.1s  
 ✔ Container cloud_wave-server-1    Stopped                                                               10.2s 
 ✔ Container cloud_wave-frontend-1  Stopped                                                                0.1s 
```



#### [예시]  `cloud_wave` 프로젝트 실행하기

```cmd
$ docker compose -p cloud_wave start
[+] Running 3/3
 ✔ Container cloud_wave-frontend-1  Started                                                                 0.6s 
 ✔ Container cloud_wave-postgres-1  Started                                                                 0.4s 
 ✔ Container cloud_wave-server-1    Started                                                                 0.5s 
```



### 중지 & 실행

> https://docs.docker.com/engine/reference/commandline/compose_pause/
>
> https://docs.docker.com/engine/reference/commandline/compose_unpause/

```shell
# 중지
docker compose pause [OPTIONS] [SERVICE...]
# 실행
docker compose unpause [SERVICE...]
```



#### [예시]  `cloud_wave` 프로젝트 중지하기

```cmd
$  docker compose -p cloud_wave pause
[+] Pausing 2/0
 ✔ Container cloud_wave-frontend-1  Paused                                                                 0.0s 
 ✔ Container db_postgres            Paused                                                                 0.0s 
```

#### [예시]  `cloud_wave` 프로젝트 실행하기

```cmd
$ docker compose -p cloud_wave unpause
[+] Running 2/0
 ✔ Container db_postgres            Unpaused                                                               0.0s 
 ✔ Container cloud_wave-frontend-1  Unpaused                                                               0.0s 
```



### 서비스 빌드하기

> https://docs.docker.com/engine/reference/commandline/compose_build/

```shell
docker compose build [OPTIONS] [SERVICE...]
```



**주요  옵션**

| Option        | Short | Default | Description                                |
| ------------- | ----- | ------- | ------------------------------------------ |
| `--build-arg` |       |         | 빌드시에 사용할 `ARG`를 설정합니다.        |
| `--builder`   |       |         | 사용할 `builder`를 설정합니다.             |
| `--no-cache`  |       |         | 이미지 빌드시 `Cache`를 사용하지 않습니다. |
| `--pull`      |       |         | 항상 이미지를 새로 다운로드 받습니다.      |
| `--push`      |       |         | 서비스의 이미지를 `Push` 합니다.           |



**주의 사항** 

- `yaml` 파일에 명시된 서비스의 `image`를 이름으로 한 이미지가 생성됩니다. 



#### [예시] 프로젝트를 위한 이미지 빌드하기

```yaml
# docker-compose.yaml
version: "3.8"

services:
  server:
    image: compose:build.v1
    build:
      dockerfile_inline: |
        FROM ubuntu:22.04
        RUN apt-get update && apt-get upgrade
      # Or Use `dockerfile`
      # dockerfile: "server.dockerfile"
    entrypoint: /bin/bash
    command:
      - -c
      - "sleep 3600"
    restart: no
```



다음과 같이 `server` 서비스의 `image`로 명시된 `compose:build.v1`로 명명된 것을 확인할 수 있습니다. 

```cmd
$ docker compose build
[+] Building 0.3s (6/6) FINISHED                                                                                
 => [server internal] load build definition from Dockerfile                                                0.0s
 => => transferring dockerfile: 92B                                                                        0.0s
 => [server internal] load .dockerignore                                                                   0.0s
 => => transferring context: 2B                                                                            0.0s
 => [server internal] load metadata for docker.io/library/ubuntu:22.04                                     0.0s
 => [server 1/2] FROM docker.io/library/ubuntu:22.04                                                       0.0s
 => [server 2/2] RUN apt-get update && apt-get upgrade                                                     0.2s
 => [server] exporting to image                                                                            0.0s
 => => exporting layers                                                                                    0.0s
 => => writing image sha256:d30066830e7283c6f7c777b698277a666934dfdfc279fca4cd847be7157aaa1d               0.0s
 => => naming to docker.io/library/compose:build.v1                                                        0.0s
```





### 프로젝트 로그 확인하기

> https://docs.docker.com/engine/reference/commandline/compose_logs/

```
docker compose logs [OPTIONS] [SERVICE...]
```



**주요 옵션**

| Option         | Short | Default | Description                                                  |
| -------------- | ----- | ------- | ------------------------------------------------------------ |
| `--follow`     | `-f`  |         | 계속 로그를 표시합니다.                                      |
| `--tail`       | `-n`  | `all`   | 마지막 `n`개의 로그를 표시합니다.                            |
| `--timestamps` | `-t`  |         | `timestamp`를 표기합니다.                                    |
| `--since`      |       |         | 특정 시간 이후에 발생한 로그만 표시합니다. <br />- timestamp (e.g. `2013-01-02T13:23:37Z`) <br />- relative (e.g. `42m`, `10s`, ...) |
| `--until`      |       |         | 특정 시간 이전에 발생한 로그만 표시합니다. <br />- timestamp (e.g. `2013-01-02T13:23:37Z`) <br />- relative (e.g. `42m`, `10s`, ...) |



**주의사항**

- `--tail`은 전체가 아닌 각 컨테이너별 로그 수를 의미합니다. 



#### [예시] `cloud_wave` 프로젝트의 모든 서비스의 로그를 2개 출력하기

```cmd
$ docker compose -p cloud_wave logs -n 2
cloud_wave-frontend-1  | 2023/12/27 08:28:52 [notice] 1#1: start worker process 23
cloud_wave-frontend-1  | 2023/12/27 08:28:52 [notice] 1#1: start worker process 24
db_postgres            | 2023-12-27 08:28:52.215 UTC [29] LOG:  database system was shut down at 2023-12-27 08:28:51 UTC
db_postgres            | 2023-12-27 08:28:52.219 UTC [1] LOG:  database system is ready to accept connections
```



### 프로세스 확인하기

> https://docs.docker.com/engine/reference/commandline/compose_top/

```shell
docker compose top [SERVICES...]
```



#### [예시] `cloud_wave` 프로젝트의 컨테이너별 프로세스 확인하기

```cmd
$ docker compose -p cloud_wave top              
cloud_wave-frontend-1
UID     PID     PPID    C    STIME   TTY   TIME       CMD
root    38700   38649   0    08:58   ?     00:00:00   nginx: master process nginx -g daemon off;   
uuidd   38783   38700   0    08:58   ?     00:00:00   nginx: worker process                        
uuidd   38784   38700   0    08:58   ?     00:00:00   nginx: worker process                        
uuidd   38785   38700   0    08:58   ?     00:00:00   nginx: worker process                        

cloud_wave-server-1
UID    PID     PPID    C    STIME   TTY   TIME       CMD
root   38513   38472   0    08:58   ?     00:00:00   sleep 3600   

db_postgres
UID   PID     PPID    C    STIME   TTY   TIME       CMD
999   38671   38622   0    08:58   ?     00:00:00   postgres                                 
999   38786   38671   0    08:58   ?     00:00:00   postgres: checkpointer                   
999   38787   38671   0    08:58   ?     00:00:00   postgres: background writer              
999   38789   38671   0    08:58   ?     00:00:00   postgres: walwriter                      
999   38790   38671   0    08:58   ?     00:00:00   postgres: autovacuum launcher            
999   38791   38671   0    08:58   ?     00:00:00   postgres: logical replication launcher  
```



### Config 확인

> https://docs.docker.com/engine/reference/commandline/compose_config/

```shell
docker compose config [OPTIONS] [SERVICE...]
```



#### [예시] `cloud_wave`의 `config` 확인하기

```cmd
$ docker compose -p cloud_wave config    
name: cloud_wave
services:
  frontend:
    configs:
    - source: server-config
    image: nginx:latest
    networks:
      private: null
    ports:
    - mode: ingress
      target: 80
      published: "80"
      protocol: tcp
    restart: always
  ...
networks:
	...
volumes:
  db-data:
    name: db-data
    labels:
      description: PostgreSQL 16.1 volume
secrets:
	...
configs:
  ...
```



### 종료한 서비스 삭제

> https://docs.docker.com/engine/reference/commandline/compose_rm/

```shell
docker compose rm [OPTIONS] [SERVICE...]
```

**주요 Options**

| Option      | Short | Default | Description                                         |
| ----------- | ----- | ------- | --------------------------------------------------- |
| `--all`     | `-a`  |         | Deprecated - no effect                              |
| `--force`   | `-f`  |         | Don't ask to confirm removal                        |
| `--stop`    | `-s`  |         | Stop the containers, if required, before removing   |
| `--volumes` | `-v`  |         | Remove any anonymous volumes attached to containers |



#### [예시] 프로젝트 삭제하기

```yaml
version: '3.8'
name: 'cloud_wave'

services:
  success:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private: {}
  fail:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep fail
    networks:
      private: {}

networks:
  private:
```



프로젝트의 모든 컨테이너 목록을 다음과 같이 확인합니다. 

```cmd
$ docker compose -p cloud_wave ps -a
NAME                   IMAGE          COMMAND                   SERVICE   CREATED         STATUS                     PORTS
cloud_wave-fail-1      ubuntu:22.04   "/bin/bash -c 'sleep…"   fail      6 seconds ago   Exited (1) 6 seconds ago   
cloud_wave-success-1   ubuntu:22.04   "/bin/bash -c 'sleep…"   success   6 seconds ago   Up 6 seconds       
```



해당 프로젝트를 삭제하면 다음과 같이 종료된 컨테이너만 삭제되는 것을 볼 수 있습니다. 

```cmd
$ docker compose -p cloud_wave rm
? Going to remove cloud_wave-fail-1 Yes
[+] Removing 1/0
 ✔ Container cloud_wave-fail-1  Removed                                                                    0.0s 
```



#### [예시] 프로젝트 종료 후 삭제하기

`-s` 또는 `--stop`을 이용하여 프로젝트를 삭제하면, 다음과 같이 삭제 전 컨테이너를 종료하는 것을 확인할 수 있습니다. 

```cmd
$ docker compose -p cloud_wave rm -s
[+] Stopping 2/2
 ✔ Container cloud_wave-fail-1     Stopped                                                                 0.0s 
 ✔ Container cloud_wave-success-1  Stopped                                                                10.1s 
? Going to remove cloud_wave-fail-1, cloud_wave-success-1 Yes
[+] Removing 2/0
 ✔ Container cloud_wave-success-1  Removed                                                                 0.0s 
 ✔ Container cloud_wave-fail-1     Removed                                                                 0.0s 
```



### 연습문제

#### [연습] 프로젝트 실행 및 `Network` 확인하기

다음 파일을 `example.yaml`로 저장합니다. 

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



#### [연습] 프로젝트 업데이트 - 서비스 포트 추가하기 

위에서 생성한 `ex1` 프로젝트의 `nginx` 서비스는 외부에 포트가 열려있지 않아 다음과 같이 `host`에서 접근할 수 없는 상태입니다. 

```cmd
$ curl localhost:80
curl: (7) Failed to connect to localhost port 80 after 2237 ms: Couldn't connect to server
```



`host`에서도 접근할 수 있도록, 다음과 같이 `example.yaml`를 수정합니다. 

```yaml
# exaple.yaml
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
$ docker compose -f example.yaml -p ex1 up -d
```



다시 `host`에서 접근을 시도하면 다음과 같이 정상적으로 접근이 되는 것을 확인할 수 있습니다. 

```cmd
$ curl localhost:80
```



#### [연습] 이미지 빌드 후 프로젝트 실행하기

`server.dockerfile`와 `docker-compose.yaml`파일을 다음과 같이 작성하고 저장합니다. 

`폴더 구조`

```tex
.
├── Dockerfile
└── docker-compose.yaml
```

`server.dockerfile`

```dockerfile
FROM ubuntu:22.04
RUN apt-get update && apt-get upgrade
```

`docker-compose.yaml`

```yaml
version: '3.8'

services:
  ubuntu:
    image: cloudwave:example.1
    build:
      dockerfile: "server.dockerfile"
    entrypoint: /bin/bash
    command:
      - -c
      - "sleep 3600"
    restart: no

  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    restart: always
```



`--build` 옵션을 이용하여 다음과 같이 프로젝트를 실행합니다. 

```cmd
$ docker compose -p cloud_wave up -d --build

[+] Building 0.0s (6/6) FINISHED                                                           docker:desktop-linux
 => [ubuntu internal] load .dockerignore                                                                   0.0s
 => => transferring context: 2B                                                                            0.0s
 => [ubuntu internal] load build definition from server.dockerfile                                         0.0s
 => => transferring dockerfile: 99B                                                                        0.0s
 => [ubuntu internal] load metadata for docker.io/library/ubuntu:22.04                                     0.0s
 => [ubuntu 1/2] FROM docker.io/library/ubuntu:22.04                                                       0.0s
 => CACHED [ubuntu 2/2] RUN apt-get update && apt-get upgrade                                              0.0s
 => [ubuntu] exporting to image                                                                            0.0s
 => => exporting layers                                                                                    0.0s
 => => writing image sha256:9fe790212381bd4b76c35994d028ce911c774b9719577f36ffbc6e8817eefe28               0.0s
 => => naming to docker.io/library/cloudwave:example.1                                                     0.0s
[+] Running 3/3
 ✔ Network cloud_wave_default     Created                                                                  0.0s 
 ✔ Container cloud_wave-nginx-1   Started                                                                  0.0s 
 ✔ Container cloud_wave-ubuntu-1  Started                                                                  0.0s 
```



`docker images` 또는 `docker compose images`를 통해 생성된 이미지를 확인해볼 수 있습니다. 

```cmd
$ docker compose -p cloud_wave images ubuntu
CONTAINER             REPOSITORY          TAG                 IMAGE ID            SIZE
cloud_wave-ubuntu-1   cloudwave           example.1           9fe790212381        125MB
```



#### [연습] 서비스별로 마지막 5개 로그 출력 후 `follow`하기

```cmd
$ docker compose logs -n 5 -f
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
...
```



#### [연습] 프로젝트 삭제하기

다음 `docker-compose.yaml`파일을 이용하여 프로젝트를 실행합니다. 

```yaml
# docker-compose.yaml
version: "3.8"
name: "cloud_wave"

services:
  frontend:
    image: nginx:latest
    ports:
      - "80:80"
    networks:
      - private
    configs:
      - server-config
    restart: always

  server:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - "sleep 3600"
    networks:
      - private
      - db
    restart: no

  postgres:
    container_name: db_postgres
    image: postgres:16.1-bullseye
    networks:
      - db
    expose:
      - 5432
    volumes:
      - db-data:/var/lib/postgresql/data:rw
    secrets:
      - db_password
    env_file:
      - .env
    environment:
      DB_PASSWORD_PATH: /run/secrets/db_password
    restart: always


volumes:
  db-data:
    name: "db-data"
    labels:
      description: "PostgreSQL 16.1 volume"

configs:
  server-config:
    file: "server.conf"

# Secret not encrypted when swarm is off
secrets:
  db_password:
    file: "secret.txt"

networks:
  private:
    name: "private"
  db:
    name: "db"
```



```cmd
$ docker compose up -d
[+] Building 0.0s (0/0)                                                                    docker:desktop-linux
[+] Running 6/6
 ✔ Network db                       Created                                                                0.0s 
 ✔ Network private                  Created                                                                0.0s 
 ✔ Volume "db-data"                 Created                                                                0.0s 
 ✔ Container db_postgres            Started                                                                0.0s 
 ✔ Container cloud_wave-frontend-1  Started                                                                0.0s 
 ✔ Container cloud_wave-server-1    Started                                                                0.0s 
```



`docker-compose.yaml`에서 `frontend` 서비스를 제거합니다.

```yaml
# docker-compose.yaml
version: "3.8"
name: "cloud_wave"

services:
  server:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - "sleep 3600"
    networks:
      - private
      - db
    restart: no

  postgres:
    container_name: db_postgres
    image: postgres:16.1-bullseye
    networks:
      - db
    expose:
      - 5432
    volumes:
      - db-data:/var/lib/postgresql/data:rw
    secrets:
      - db_password
    env_file:
      - .env
    environment:
      DB_PASSWORD_PATH: /run/secrets/db_password
    restart: always


volumes:
  db-data:
    name: "db-data"
    labels:
      description: "PostgreSQL 16.1 volume"

configs:
  server-config:
    file: "server.conf"

# Secret not encrypted when swarm is off
secrets:
  db_password:
    file: "secret.txt"

networks:
  private:
    name: "private"
  db:
    name: "db"
```

이후, 다음과 같이 삭제를 시도하면 `frontend` 서비스와  `volume`을 제외한 나머지 컴포넌트에 대해서만 삭제를 시도하는 것을 확인할 수 있습니다. 

```cmd
$ docker compose -p cloud_wave down
[+] Running 4/4
 ✔ Container db_postgres          Removed                                                                  0.1s 
 ✔ Container cloud_wave-server-1  Removed                                                                 10.2s 
 ! Network private                Resource is still in use                                                 0.0s 
 ✔ Network db                     Removed                                                                  0.0s 
```



다음과 같이 `docker-compose.yaml`에서 제거된  `frontend` 서비스가 살아있는 것을 확인할 수 있습니다. 

```cmd
$ docker compose ps 
NAME                    IMAGE          COMMAND                   SERVICE    CREATED         STATUS         PORTS
cloud_wave-frontend-1   nginx:latest   "/docker-entrypoint.…"   frontend   5 minutes ago   Up 5 minutes   0.0.0.0:80->80/tcp
```



다음과 같이  `--volumes`와 `--remove-orphans` 옵션을 이용하면, 남아있는 컨테이너와 볼륨을 삭제할 수 있습니다.  

```cmd
$ docker compose down --volumes --remove-orphans                
[+] Running 3/3
 ✔ Container cloud_wave-frontend-1  Removed                                                                0.2s 
 ✔ Volume db-data                   Removed                                                                0.0s 
 ✔ Network private                  Removed                                                                0.1s 
```



## 2.2. Compose file - Basic

- `docker-compose`를 위한 `configuration` 파일은 `YAML`을 이용하여 작성합니다. 

- `Python`과 같이 들여쓰기를 기반으로 구조를 결정합니다. 



**`docker-compose.yaml` 파일 예시**

```yaml
version: "3.8"
name: "cloud_wave"

services:
  frontend:
    image: nginx:latest
    ports:
      - "80:80"
    networks:
      - private
    configs:
      - server-config
    restart: always

  server:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - "sleep 3600"
    networks:
      - private
      - db
    restart: no

  postgres:
    container_name: db_postgres
    image: postgres:16.1-bullseye
    networks:
      - db
    expose:
      - 5432
    volumes:
      - db-data:/var/lib/postgresql/data:rw
    secrets:
      - db_password
    env_file:
      - .env
    environment:
      DB_PASSWORD_PATH: /run/secrets/db_password
    restart: always

volumes:
  db-data:
    name: "db-data"
    labels:
      description: "PostgreSQL 16.1 volume"

configs:
  server-config:
    file: "server.conf"

# Secret not encrypted when swarm is off
secrets:
  db_password:
    file: "secret.txt"

networks:
  private:
    name: "private"
  db:
    name: "db"
```



### 2.2.1. Version

- `version`은 해당 `compose` 파일에서 사용한 버전을 의미합니다. 

- `version`을 선언하여도 `docker compose`는 해당 파일을 실행할 수 있는 가장 최신 버전을 사용합니다. 



### 2.2.2. Name

- 프로젝트의 이름을 의미합니다. 

- 프로젝트의 이름은 다음과 같은 순서로 결정됩니다. 

    1. `Compose CLI`의 `-p / --project-name`가 설정된 경우

    2. `configuration file(docker-compose.yaml)`에 `name`이 설정된 경우

    3. `directory` 이름



### 2.2.3. Service

> https://docs.docker.com/compose/compose-file/05-services/

```yaml
# Example
services:
  frontend:
    image: nginx:latest
    ports:
      - "80:80"
    configs:
      - server-config
    restart: always
```



#### image

```yaml
image: string
```

- 컨테이너에서 사용할 이미지입니다. 
- 이미지를 `build`하는 경우 생성된 이미지의 이름으로 사용됩니다. 



#### container_name

```yaml
container_name: string
```

- 값을 명시하지 않은 경우, 프로젝트, 서비스, 컨테이너 번호를 기반으로 생성됩니다. 
- 알파벳 대소문자, 숫자 및 일부 특수기호(`.`, `_`, `-`)만 사용할 수 있습니다. 
- `container_name`이 설정되어 있는 경우, 해당 서비스를 위한 컨테이너를 1개만 생성할 수 있습니다. 



#### expose

```yaml
expose:
	- 80  # INT
	- "8080" # STRING
	- "1000-1010" # RANGE
```

- 같은 네트워크를 사용하는 서비스에서만 접근 가능하며, `host`로 `publish`되지 않습니다. 
- `-`를 이용하여 노출할 포트 범위를 지정할 수 있습니다. 



#### ports

```yaml
ports:
	# Short Syntax
	- [HOST:]CONTAINER[/PROTOCOL]
	# Long Syntax
	- target: 80
    host_ip: HOST
    published: "8080"
    protocol: tcp
```

- 외부에서 접근할 수 있도록 `host`로 `publish`할 포트를 정의합니다. 
- `-`를 이용하여 노출할 포트 범위를 지정할 수 있습니다. 



#### entrypoint

```yaml
# String
entrypoint: STRING
# List
entrypoint:
	- STRING
	- STRING
```

- 컨테이너의 `Dockerfile`에서 정의된 `ENTRYPOINT`를 `override`합니다.
- `ENTRYPOINT`가 선언된 경우, `Dockerfile`에서 정의된 `CMD`는 무시됩니다. 



#### command

```yaml
# String
command: bundle exec thin -p 3000
# List
command: [ "bundle", "exec", "thin", "-p", "3000" ]
# List - yaml
command: 
	- STRING
	- STRING
```

- 컨테이너의 `Dockerfile`에서 정의된 `CMD`를 `override`합니다.
- 값이 `null`인 경우 `Image`의 `command`가 사용되지만, `[]` 또는 `''`로 설정된 경우  `Image`의 `command`는 무시됩니다. 



#### environment

```yaml
# Map Syntax
environment:
  KEY1: value
  KEY2: "value"
  KEY3:
  
# Array Syntax
environment:
  - KEY1=value
  - KEY2="value"
  - KEY3
```

- `Map` 또는 `Array`형식으로 작성할 수 있습니다. 
- 값이 정의되지 않은 경우, `env_file`등으로 제공되지 않으면 `unset` 됩니다. 
- 동일한 변수가 `env_file`에도 선언되어 있는 경우, `environment`의 값이 설정됩니다. 



#### env_file

```yaml
# Single
env_file: .env  # string

# Multiple
env_file:
  - ./a.env
  - ./b.env
```

- 기본적으로 `.env` 파일이 사용됩니다. 
- 여러개의 파일에 동일한 변수가 선언된 경우, 가장 마지막 파일에 있는 값으로 설정됩니다. 
- `env_file`은 다음과 같은 양식으로 작성합니다. 
    - `VAR[=[VAL]]`



#### build

> https://docs.docker.com/compose/compose-file/build/

```yaml
services:
	# Short
  frontend:
    image: example/webapp
    build: ./webapp
	
	# Long - dockerfile
  backend:
    image: example/database
    build:
      context: backend
      dockerfile: ../backend.Dockerfile
      args:
        GIT_COMMIT: cdc3b19
```

- 서비스의 컨테이너를 `build`할 때 사용하며, 각 서비스별로 `context` 경로를 설정할 수 있습니다. 
- `dockerfile` 대신 `dockerfile_inline`을 이용하여 사용할 수도 있습니다. 

```yaml
build:
  context: .
  dockerfile_inline: |
    FROM baseimage
    RUN some command    
```



#### pull_policy

```yaml
pull_policy: string
```

- `Compose`가 이미지를 어떻게 가져올 것인지를 설정합니다. 
- 사용할 수 있는 값은 다음과 같습니다. 

| Value     | Description                                                  |
| --------- | ------------------------------------------------------------ |
| `always`  | 매번 `registry`에서 이미지를 다운로드 합니다.                |
| `never`   | 저장된 이미지만을 사용합니다. 이미지가 존재하지 않는 경우 실행되지 않습니다. |
| `missing` | 저장된 이미지가 없는 경우에만 `registry`에서 다운로드 합니다. |
| `build`   | 매번 이미지를 `build`합니다.                                 |



#### restart

```yaml
restart: string
```

- 컨테이너가 종료된 경우 어떻게 처리할 것인지를 정의합니다. 
- 사용할 수 있는 값은 다음과 같습니다.

| Value            | Description                                              |
| ---------------- | -------------------------------------------------------- |
| `no`             | 어떠한 경우에도 컨테이너를 재실행하지 않습니다.          |
| `always`         | 제거되지 않는 한, 컨테이너를 항상 재실행합니다.          |
| `on-failure`     | `exit code`가 `error`인 경우에만 재실행합니다.           |
| `unless-stopped` | `종료`또는 `삭제`하는 경우를 제외하고 항상 재실행합니다. |



#### platform

```yaml
platform: string

# Example
platform: windows/amd64
platform: linux/arm64/v8
```

- 컨테이너의 `platform`을 명시합니다. 



#### CLI option <-> Service elements

| Docker CLI Options | Service Elements |
| ------------------ | ---------------- |
| -i                 | stdin_open       |
| -t                 | tty              |
| -p                 | ports            |
| -e                 | environment      |
| --env-files        | env_file         |
| --name             | container_name   |



#### 연습 문제

##### [연습] `ubuntu` 서버 실행하기

아래에 있는 `docker-compose.yaml` 파일을 이용하여 프로젝트를 실행할 경우, `ubuntu` 서비스가 생성 후 바로 종료되는 것을 확인할 수 있습니다. 

```yaml
# docker-compose.yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    restart: no
```

```cmd
$ docker compose up
$ docker compose ps -a
NAME                IMAGE          COMMAND       SERVICE   CREATED          STATUS                     PORTS
example2-ubuntu-1   ubuntu:22.04   "/bin/bash"   ubuntu    15 seconds ago   Exited (0) 4 seconds ago   
```



`docker run` 때와 마찬가지로 두가지 방식을 이용하여 유지시킬 수 있습니다. 

1. `stdin_open`과 `tty`를 이용합니다.
    - `docker run`에서의 `-it` 옵션과 동일합니다. 

```yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    tty: true
    stdin_open: true
    restart: no
```

2. `sleep`과 같은 명령어를 실행하여 프로세스를 유지합니다. 

```yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    restart: no
```



##### [연습] 환경변수를 이용하여 `compose file` 제어하기

환경변수를 통해 `docker compose` 파일을 설정하는 방법은 일반적으로 다음과 같습니다. 

1. `Host`에서 환경변수 설정하기 
2. 환경 변수파일(`.env`)를 사용하기
    - `CLI`

다음 `.env`와 `docker-compose.yaml`을 이용하여 프로젝트를 빌드 후 실행합니다. 

```tex
# .env
FROM=".env file"
```

```yaml
# project-1.yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - echo 'env from "$FROM"'
    restart: no
```



`docker compose up`을 이용하여 실행하면 다음과 같이 `.env`의 환경변수가 기입된 것을 확인할 수 있습니다. 

```cmd
$ docker compose up
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/0
 ✔ Container example3-ubuntu-1  Recreated                                                                                                                                                                                          0.0s 
Attaching to example3-ubuntu-1
example3-ubuntu-1  | env from ".env file"
example3-ubuntu-1 exited with code 0
```



이번에는 `host`에서 다음 명령어를 통해 다음과 같이 환경변수를 설정한 뒤, 실행해보면 다음과 같은 결과를 얻을 수 있습니다. 

```cmd
$ export FROM="host"
$ docker compose up
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/0
 ✔ Container example3-ubuntu-1  Recreated                                                                                                                                                                                          0.0s 
Attaching to example3-ubuntu-1
example3-ubuntu-1  | env from host
example3-ubuntu-1 exited with code 0
```



환경변수에 대해 `default` 값을 설정하고 싶은 경우 다음과 같이 작성하면 됩니다. 

- `${ENV:-DEFAULT_VALUE}`

```yaml
# project-1.yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - echo 'env from "$FROM"' && echo 'env from ${BY:-default}'
    restart: no
```

위의 `docker-compose.yaml`을 실행하면 다음과 같이 "default" 값이 출력된 것을 확인할 수 있습니다. 

```cmd
$ docker compose up
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/0
 ✔ Container example3-ubuntu-1  Recreated                                                                                                                                                                                          0.0s 
Attaching to example3-ubuntu-1
example3-ubuntu-1  | env from host
example3-ubuntu-1  | env from default
example3-ubuntu-1 exited with code 0
```



##### [연습] `command`에서 컨테이너 환경변수 사용하기

`docker-compose.yaml` 파일에서 `$`를 이용하여 환경변수를 사용하는 경우,  컨테이너 내부의 환경변수가 사용되지 않습니다. 

`command`에서 컨테이너 내부의 환경변수를 사용하고 싶은 경우 다음과 같이 `$$`를 이용하면 됩니다. 

```yaml
version: '3.8'

services:
  ubuntu:
    image: ubuntu:22.04
    environment:
      - FROM="env definition"
    entrypoint: /bin/bash
    command:
      - -c
      - echo 'env from ${FROM}' && echo env from $${FROM}
    restart: no
```



위의 `yaml` 파일을 실행하면 다음과 같은 결과 값을 확인할 수 있습니다.  

```cmd
$ docker compose up
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/0
 ✔ Container example3-ubuntu-1  Recreated                                                                                                                                                                                          0.0s 
Attaching to example3-ubuntu-1
example3-ubuntu-1  | env from host
example3-ubuntu-1  | env from "env definition"
example3-ubuntu-1 exited with code 0
```



`docker inspect` 명령어를 통해서도 확인해볼 수 있습니다. 

```cmd
$ docker inspect example3-ubuntu-1 -f "{{ .Args }}"
[-c echo 'env from host' && echo env from ${FROM}]
```



##### [연습] Docker compose에서 build 사용하기

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







### 2.2.4. volume

```yaml
services:
  backend:
    image: example/database
    volumes:
      - db-data:/etc/data

  backup:
    image: backup-service
    volumes:
      - type: volume
        source: mydata
        target: /data
        volume:
          nocopy: true
        read_only: true
      - db-data:/var/lib/backup/data:rw

volumes:
  db-data:
    name: "my-app-data"
    external: true
    labels:
      com.example.description: "Database volume"
```



#### 볼륨 정의하기

```yaml
volumes:
  db-data:  # volume_key
    name: "db-volume"
    external: true
    labels:
      com.example.description: "Database volume"
```



##### Name

- 볼륨의 이름입니다. 
- 설정되지 않은 경우 `{project_name}_{volume_key}` 형태의 이름으로 명명됩니다.



##### External

- 생성되어 있는 기존 볼륨의 사용 여부입니다. 
- 해당 볼륨이 생성되어 있지 않은 경우, 프로젝트가 실행되지 않습니다. 
- `name`이 지정되어 있지 않은 경우, `volumes`에 정의된 `key`가 사용됩니다. 
    - `db-volume`이 명시되지 않았다면, `db_data`란 이름을 가진 `volume`을 탐색합니다. 



##### Labels

- 관리를 위한 라벨을 설정합니다. 



**주의 사항**

- `volume`을 정의하였더라도 `service`에서 사용하지 않는 경우, 생성되지 않습니다. 



#### 볼륨 사용하기

```yaml
services:
  backend:
    image: example/database
    volumes:
      - db-data:/etc/data

  backup:
    image: backup-service
    volumes:
      - type: volume
        source: mydata
        target: /data
        volume:
          nocopy: true
        read_only: true
      - db-data:/var/lib/backup/data:rw
```

- 정의된 볼륨은 서비스의 `volumes`에서 사용할 수 있습니다. 

- `Short Syntax`, `Long Syntax` 두 가지 방식으로 서비스의 `volumes`를 작성할 수 있습니다.



##### Short Syntax

```tex
volumes:
	- VOLUME:CONTAINER_PATH:ACCESS_MODE
```

| Attributes       | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| `VOLUME`         | `host`의 경로 또는 `volume` 이름을 사용합니다. <br />`volume` 이름을 사용하는 경우 경로를 지정할 수 없습니다. |
| `CONTAINER_PATH` | 볼륨이 마운트될 컨테이너의 경로를 의미합니다.                |
| `ACCESS_MODE`    | 해당 볼륨의 `Access mode`를 설정합니다. <br />- `rw`: 읽기&쓰기 모두 가능합니다.<br />- `ro`: 읽기 전용으로 설정합니다. |



##### Long Syntax

> https://docs.docker.com/compose/compose-file/05-services/#long-syntax-5

```yaml
volumes:
  - type: volume
    source: mydata
    target: /data
    read_only: true
```

| Attributes  | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| `type`      | 해당 볼륨의 `mount type`을 설정합니다. <br />- `volume`: `source`로 `volume`을 사용합니다.<br />- `bind`: `source`로 `host`의 `directory`를 사용합니다 |
| `source`    | `volume`이름 또는 `host`의 `directory` 경로를 설정합니다.    |
| `target`    | 컨테이너에서 볼륨이 `mount`될 `directory` 경로를 설정합니다. |
| `read_only` | 읽기 전용 여부를 설정합니다.                                 |



#### 연습 문제

##### [연습] External volume 사용하기

다음과 같이 `volume`을 생성합니다.

```cmd
$ docker volume create vault
vault
```



`volume`을 `ubuntu` 컨테이너의 `/root/vault`에 마운트하도록 `docker-compose.yaml`을 작성합니다.

```yaml
version: '3.8'
name: 'volume-external'

services:
  master:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    volumes:
      - vault:/root/vault

volumes:
  vault:
    external: true
    name: 'vault'
```



##### [연습] read_only 로 설정하여 사용하기

다음 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다. 

```yaml
# docker-compose.yaml
version: '3.8'
name: 'volume-external'

services:
  master:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    volumes:
      - vault:/root/vault:rw

  slave:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    volumes:
      - vault:/root/vault:ro

volumes:
  vault:
    external: true
    name: 'vault'
```

```cmd
$ docker compose -f docker-compose.yaml up -d
[+] Building 0.0s (0/0)                                                      docker:desktop-linux
[+] Running 2/2
 ✔ Container volume-external-master-1  Started                                              0.0s 
 ✔ Container volume-external-slave-1   Started                                              0.0s 
```



다음과 같이 `exec`를 이용하여 `master` 서비스의 컨테이너에서 `/root/vault`에 파일을 생성합니다. 

```cmd
$ docker exec volume-external-master-1 /bin/bash -c "echo master > /root/vault/temp.txt"
```

생성한 파일이 정상적으로 읽어지는지 다음과 같이 확인해볼 수 있습니다. 

```cmd
$ docker exec volume-external-master-1 /bin/bash -c "cat /root/vault/temp.txt"        
master
```



`slave` 서비스의 컨테이너에서는 `/root/vault`에 저장한 파일을 읽을 수 있지만, 파일을 기록하는 것은 불가능한 것을 확인할 수 있습니다.

```cmd
$ docker exec volume-external-slave-1 /bin/bash -c "cat /root/vault/temp.txt"         
master
$ docker exec volume-external-slave-1 /bin/bash -c "echo slave > /root/vault/temp.txt"
/bin/bash: line 1: /root/vault/temp.txt: Read-only file system
```



##### [연습] `volumes_from`을 이용하여 `volume` 사용하기

> https://docs.docker.com/compose/compose-file/05-services/#volumes

다음 파일을 이용하여 새로운 프로젝트를 실행합니다. 

- 위에서 생성한 `volume-external` 프로젝트는 실행중이어야 합니다.

```yaml
# docker-compose.yaml
version: '3.8'
name: 'volume-external2'

services:
  other:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    volumes_from:
      - container:volume-external-slave-1:ro
```



```cmd
$ docker compose -f docker-compose.yaml up -d                       
[+] Building 0.0s (0/0)                                                       docker:desktop-linux
[+] Running 2/2
 ✔ Network volume-external2_default    Created                                                0.1s 
 ✔ Container volume-external2-other-1  Started                                                0.0s 
```



별도의 `volumes`를 정의하지 않고도 `volumes_from`을 이용하여 다른 서비스의 `volume`을 사용할 수 있는것을 확인할 수 있습니다. 

```cmd
$ docker exec volume-external2-other-1 /bin/bash -c "cat /root/vault/temp.txt"
master
$ docker exec volume-externa2-other-1 /bin/bash -c "echo other > /root/vault/temp.txt"
/bin/bash: line 1: /root/vault/temp.txt: Read-only file system
```



### 2.2.5. network

```yaml
services:
  frontend:
    image: example/webapp
    networks:
      - private

networks:
  private:
    name: "private_net"
    external: true
    labels:
      com.example.description: "private network"
```



#### 네트워크 정의하기

```yaml
networks:
  private:  # network_key
    name: "private_net"
    external: true
    labels:
      com.example.description: "private network"
```



##### Name

- 네트워크의 이름입니다. 
- 설정되지 않은 경우 `{project_name}_{network_key}` 형태의 이름으로 명명됩니다.



##### External

- 생성되어 있는 기존 네트워크 사용 여부입니다. 
- 해당 네트워크가 생성되어 있지 않은 경우, 프로젝트가 실행되지 않습니다. 
- `name`이 지정되어 있지 않은 경우, `networks`에 정의된 `key`가 사용됩니다. 
    - `private_net`이 명시되지 않았다면, `private`란 이름을 가진 `network`를 탐색합니다. 



##### Labels

- 관리를 위한 라벨을 설정합니다. 



**주의 사항**

- `network`를 정의하였더라도 `service`에서 사용하지 않는 경우, 생성되지 않습니다. 





#### 네트워크 사용하기

```yaml
services:
  backend:
    image: example/database
    # List Syntax
    networks:
      - private

  backup:
    image: backup-service
    # Map Syntax
    networks:
      private:
        aliases:
          - alias1
          
  bastion:
    image: bastion
    network_mode: host
```

- 정의된 볼륨은 서비스의 `networks`에서 사용할 수 있습니다. 
- `host` 또는 `none` 네트워크를 사용하려는 경우, `network_mode`를 이용하여 설정합니다.



**주의사항**

- 1개 이상의 컨테이너에서 동일한 `Alias`를 사용할 경우, 응답할 컨테이너를 특정지을 수 없습니다.



#### 연습 문제 

##### [연습] `host` 네트워크를 사용하는 컨테이너 만들기 

`docker-compose.yaml`을 이용하여 `host`를 사용하는 컨테이너를 실행합니다. 

```yaml
# docker-compose.yaml
version: '3.8'
name: 'network-host'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    network_mode: host
```

```cmd
$  docker compose up -d
[+] Building 0.0s (0/0)                                                      docker:desktop-linux
[+] Running 1/0
 ✔ Container network-host-ubuntu-1  Created                                                  0.0s 
```



테스트를 위해서 다음과 같이 `nginx` 컨테이너를 실행합니다. 

```cmd
$ docker run -d -p 80:80 --name nginx nginx:latest 
```



`ubuntu` 컨테이너에 `curl`을 설치합니다. 

```cmd
$ docker exec network-host-ubuntu-1 /bin/bash -c "apt-get update && apt-get upgrade && apt-get install -y curl"
```



다음과 같이 컨테이너 내부에서 `localhost:80`으로 `nginx` 응답이 오는 것을 확인합니다. 

```cmd
$ docker exec network-host-ubuntu-1 /bin/bash -c "curl localhost:80"
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



##### [연습] 이미 생성된 네트워크 사용하기

`bridge` 드라이버를 사용하는 네트워크를 생성합니다. 

```cmd
$ docker network create private -d bridge
1be27c483205a3b2dfbdf4ae36c2cc20ae0a53bc939eb7bd050a65574a9b9299

$ docker network ls -f name=private      
NETWORK ID     NAME                    DRIVER    SCOPE
1be27c483205   private                 bridge    local
```



다음 `compose`파일을 이용하여 `private` 네트워크를 사용하는 프로젝트를 실행합니다. 

```yaml
# docker-compose.yaml
version: '3.8'
name: 'network-external'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private:

networks:
  private:
  	name: "private"
    external: true
```

```cmd
$ docker compose up -d                                                                             
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/1
 ✔ Container network-external-ubuntu-1  Started   
```



다음과 같이 `name`을 제거하여도 프로젝트가 정상적으로 실행됩니다. 

```yaml
# docker-compose.yaml
version: '3.8'
name: 'network-external'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private:

networks:
  private:
    external: true
```

```cmd
$ docker compose up -d                                                                             
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/1
 ✔ Container network-external-ubuntu-1  Started   
```



하지만, `name`을 `my-private`로 변경하면 다음과 같이 프로젝트가 실행되지 않는 것을 확인할 수 있습니다. 

```yaml
version: '3.8'
name: 'network-external'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private:

networks:
  private:
    name: "my-private"
    external: true
```

```cmd
$ docker compose up -d
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
network my-private declared as external, but could not be found
```





##### [연습] `alias` 설정하기

다음 `docker-compose.yaml`을 이용하여 프로젝트를 생성합니다. 

```yaml
# docker-compose.yaml
version: '3.8'
name: 'network-alias'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private:

networks:
  private:
```



`inspect`를 이용하여 컨테이너에 설정된 `alias`를 확인하면 다음과 같이 3개가 표기되는 것을 확인할 수 있습니다. 

- 컨테이너 이름
- 서비스 이름
- 컨테이너 ID

```cmd
$ docker inspect network-alias-ubuntu-1 | jq ".[0].NetworkSettings.Networks" | jq -r '.[].Aliases' 
[
  "network-alias-ubuntu-1",
  "ubuntu",
  "d1295a0aae14"
]
```



다음과 같이 `docker-compose.yaml`에  `alias`를 추가하여 업데이트 해줍니다. 

```yaml
version: '3.8'
name: 'network-alias'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private:
        aliases:
          - server

networks:
  private:
```

```cmd
$ docker compose up -d
```



그러면 다음과 같이 기존에 있던 `alias`에 추가로 설정한 `server`가 추가된 것을 확인해볼 수 있습니다. 

```cmd
$  docker inspect network-alias-ubuntu-1 | jq ".[0].NetworkSettings.Networks" | jq -r '.[].Aliases' 
[
  "network-alias-ubuntu-1",
  "ubuntu",
  "server",
  "1679779c1978"
]
```



#### [실습] 생성된 `network`를 이용하여 서로 다른 프로젝트의 서비스와 연결하기

- `across_project` 네트워크를 생성하세요.
- `network-across-project-1` 프로젝트를 생성하세요
    - `ubuntu:22.04`
    - `curl`이 설치되어 있어야합니다. 
    - `across_project` 네트워크의 alias를 `main`으로 설정하세요
- `network-across-project-2` 프로젝트를 생성하세요
    - `nginx:latest`
    - `80` 포트를 `expose`
    - `across_project`네트워크의 alias를 `web`으로 설정하세요
- `docker network inspect`를 이용하여 각 서비스의 IP를 확인하세요

```cmd
$ docker network inspect across_project | jq '.[].Containers'
```

- `ubuntu`서버에서 curl을 이용하여 `web`을 호출하세요
    - IP
    - DNS(alias)



### 2.2.6. config & secret

```yaml
services:
  redis:
    image: redis:latest
    configs:
      - http_config
      - source: my_config
        target: /redis_config
        uid: "103"
        gid: "103"
        mode: 0440  # Octal notation
    secrets:
      - source: server-certificate
        target: server.cert
        uid: "103"
        gid: "103"
        mode: 0440  # Octal notation
configs:
  http_config:
    file: ./httpd.conf
secrets:
  server-certificate:
    file: ./server.cert
```



#### Config Vs. Secret

|                 | Config                                | Sercret                                    |
| --------------- | ------------------------------------- | ------------------------------------------ |
| 목적            | 외부에서 서버 설정을 위한 변수를 제공 | 비밀번호와 같은 민감한 데이터를 제공       |
| 기본 Mount 위치 | `/<config-name>`                      | `/run/secrets/<secret-name>`               |
| 암호화 여부     | X                                     | O (`Swarm`을 사용하는 경우)<br />X (그 외) |



#### Config Vs. Mount(Volume)

- 컨테이너 외부에서 파일을 제공한다는 점에서는 두개는 유사합니다. 
- `Volume`과는 달리 `Config`는 `Mode`를 이용하여 파일의 권한을 세부적으로 관리할 수 있습니다. 



#### 연습 문제

##### [연습] config를 특정 디렉토리에 mount하기

다음 명령어를 이용하여 `password.txt`를 생성합니다. 

> 임의의 텍스트 파일을 메모장등을 이용해서 생성해도 무방합니다. 

```cmd
# Window - CMD
$ echo %RANDOM% > password.txt 

# Linux
$ openssl rand -base64 14 > password.txt 
```



다음 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다. 

```yaml
# docker-compose.yaml
version: '3.8'
name: 'config-mount'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    configs:
      - source: password
        target: /root/web_password.txt

configs:
  password:
    file: password.txt
```



`docker exec`를 통해 `config` 파일을 확인해볼 수 있습니다. 

```cmd
$ docker exec config-mount-ubuntu-1 /bin/bash -c "cat /root/web_password.txt && ls -al /root/"  
6M9tLBo4u8EEIbb6Pis=
total 20
drwx------ 1 root root 4096 Dec 28 07:58 .
drwxr-xr-x 1 root root 4096 Dec 28 07:58 ..
-rw-r--r-- 1 root root 3106 Oct 15  2021 .bashrc
-rw-r--r-- 1 root root  161 Jul  9  2019 .profile
-rw-r--r-- 1 root root   21 Dec 28 07:51 web_password.txt
```





## 2.3. Compose file - Advance

### 2.3.1. anchor & Alias

> https://docs.docker.com/compose/compose-file/11-extension/

```yaml
version: '3.8'

x-common:
  &common
  restart: always
  volumes:
    - source:/code
  environment:
    &default-env
    BY: "x-common"

x-value: &v1 x

services:
  ubuntu:
    <<: *common
    image: ubuntu:22.04
    environment:
      <<: *default-env
      FROM: "env definition"
      X: *v1
    entrypoint: /bin/bash
    command:
      - -c
      - echo 'env from ${FROM}' && echo env from $${BY}
    restart: no

volumes:
  source:
```

- `Anchor & Alias`를 이용하여 반복적으로 사용되는 요소들을 모듈화하여 사용할 수 있습니다. 
- 재사용을 위한 모듈은 `x-`를 `prefix`로 사용하여야 합니다. 
-  `<<:`를 사용하면 `yaml`을 병합하는 형태로 사용하게 됩니다. 



#### [예시] `Anchor & Alias`를 이용한 `YAML` 파일 확인하기

위에 있는 `docker-compose.yaml`를 대상으로 `docker compose config`를 사용하면,
다음과 같이 `Anchor & Alias`가 반영된 `YAML`을 확인할 수 있습니다. 

```cmd
$ docker compose config
WARN[0000] The "FROM" variable is not set. Defaulting to a blank string. 
name: compose
services:
  ubuntu:
    command:
      - -c
      - echo 'env from ' && echo env from $${BY} && echo env from alias $${X}
    entrypoint:
      - /bin/bash
    environment:
      BY: x-common
      FROM: env definition
      X: x
    image: ubuntu:22.04
    networks:
      default: null
    restart: "no"
    volumes:
      - type: volume
        source: source
        target: /code
        volume: {}
networks:
  default:
    name: compose_default
volumes:
  source:
    name: compose_source
x-common:
  environment:
    BY: x-common
  restart: always
  volumes:
    - source:/code
x-value: x
```



### 2.3.2. profile

> https://docs.docker.com/compose/profiles/
>
> https://docs.docker.com/compose/compose-file/15-profiles/

```yaml
version: '3.8'

services:
  postgres:
    image: postgres:16.1-bullseye
    environment:
      - POSTGRES_PASSWORD=mysecretpassword

  server:
    image: ubuntu:22.04
    stdin_open: true # docker run -i
    tty: true        # docker run -t
    depends_on:
      - postgres

  pgadmin:
    image: dpage/pgadmin4:7.4
    environment:
      - PGADMIN_DEFAULT_EMAIL=user@sample.com
      - PGADMIN_DEFAULT_PASSWORD=SuperSecret
    depends_on:
      - postgres
      - server
    profiles:
      - debug
```



#### [예시] `profile`을 이용하여 일부 서비스만 실행하기

```yaml
# docker-compose.yaml
version: '3.8'

services:
  postgres:
    image: postgres:16.1-bullseye
    environment:
      - POSTGRES_PASSWORD=mysecretpassword

  server:
    image: ubuntu:22.04
    stdin_open: true # docker run -i
    tty: true        # docker run -t
    depends_on:
      - postgres

  pgadmin:
    image: dpage/pgadmin4:7.4
    environment:
      - PGADMIN_DEFAULT_EMAIL=user@sample.com
      - PGADMIN_DEFAULT_PASSWORD=SuperSecret
    depends_on:
      - postgres
      - server
    profiles:
      - debug
```

`--profile` 없이 프로젝트를 실행시키면 다음과 같이 `profiles`가 선언되지 않은 2개의 서비스만 실행되는 것을 확인할 수 있습니다. 

```cmd
$ docker compose -f docker-compose.yaml up -d
[+] Building 0.0s (0/0)                                        docker-container:multi-arch-builder
[+] Running 4/4
 ✔ Network compose_default         Created                     0.1s 
 ✔ Container compose-postgres-1    Started                     0.1s 
 ✔ Container compose-server-1      Started                     0.1s 
```



`debug` 프로파일을 이용하여 프로젝트를 재실행하면, 기존에 생성되지 않았던 `pgadmin` 서비스가 실행되는 것을 확인할 수 있습니다. 

```cmd
$ docker compose -f docker-compose.yaml --profile debug up -d
[+] Building 0.0s (0/0)                                         docker-container:multi-arch-builder
[+] Running 3/3
 ✔ Container compose-postgres-1  Running                        0.0s 
 ✔ Container compose-server-1    Running                        0.0s 
 ✔ Container compose-pgadmin-1   Started                        0.1s 
```



### 2.3.3. deploy

> https://docs.docker.com/compose/compose-file/deploy/

```yaml
deploy:
  resources:
    limits:
      cpus: '0.50'
      memory: 50M
      pids: 1
    reservations:
      cpus: '0.25'
      memory: 20M
  restart_policy:
    condition: on-failure
    delay: 5s
    max_attempts: 3
    window: 120s
  update_config:
    parallelism: 2
    delay: 10s
    order: stop-first
```



#### [예시] 서비스를 컨테이너 3개로 구성하기

다음 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다.

```yaml
# docker-compose.yaml
name: deploy-replica
services:
  web:
    image: nginx:latest
    expose:
      - 80
    deploy:
      replicas: 3
```

```cmd
$ docker compose up -d
[+] Building 0.0s (0/0)                                                                                      docker:desktop-linux
[+] Running 3/3
 ✔ Container deploy-replica-web-3  Started                                                                                   0.1s 
 ✔ Container deploy-replica-web-2  Started                                                                                   0.1s 
 ✔ Container deploy-replica-web-1  Started                                                                                   0.1s 
```



다음과 같이 `web` 서비스가  `nginx` 컨테이너 3개로 구성되어진 것을 확인할 수 있습니다. 

```cmd
$ docker compose -p deploy-replica ps
NAME                   IMAGE          COMMAND                   SERVICE   CREATED          STATUS          PORTS
deploy-replica-web-1   nginx:latest   "/docker-entrypoint.…"   web       47 seconds ago   Up 46 seconds   80/tcp
deploy-replica-web-2   nginx:latest   "/docker-entrypoint.…"   web       47 seconds ago   Up 46 seconds   80/tcp
deploy-replica-web-3   nginx:latest   "/docker-entrypoint.…"   web       47 seconds ago   Up 46 seconds   80/tcp
```



하지만, 다음과 같이 `container_name`을 설정하게되면 실행시 에러가 발생하는 것을 확인할 수 있습니다. 

```yaml
# docker-compose.yaml
name: deploy-replica
services:
  web:
    container_name: "web"
    image: nginx:latest
    expose:
      - 80
    deploy:
      replicas: 3
```



```cmd
$ docker compose up -d               
[+] Building 0.0s (0/0)                                                                                      docker:desktop-linux
WARNING: The "web" service is using the custom container name "web". Docker requires each container to have a unique name. Remove the custom name to scale the service.
```



#### [예시] `resource` 할당하기

다음 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다.

```yaml
# docker-compose.yaml
name: deploy-replica
services:
  web:
    image: nginx:latest
    expose:
      - 80
    deploy:
      replicas: 1
      resources:
        limits:
          memory: 500M
```

```cmd
$ docker compose up -d
[+] Building 0.0s (0/0)                                                                                      docker:desktop-linux
[+] Running 3/3
 ✔ Container deploy-replica-web-1  Started                                                                                   0.6s 
```



`docker stats`를 이용하여 메모리 제한을 확인할 수 있습니다. 

```cmd
$ docker stats deploy-replica-web-1
CONTAINER ID   NAME                   CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O     PIDS
e07340054e9a   deploy-replica-web-1   0.00%     5.805MiB / 500MiB   1.16%     806B / 0B   0B / 12.3kB   7
```



### 2.3.4. depend_on

> https://docs.docker.com/compose/startup-order/
>
> https://docs.docker.com/compose/compose-file/05-services/#depends_on

```yaml
# docker-compose.yaml
version: '3.8'

services:
  postgres:
    image: postgres:16.1-bullseye
    environment:
      - POSTGRES_PASSWORD=mysecretpassword

  server:
    image: ubuntu:22.04
    stdin_open: true
    tty: true        
    # Short Syntax - List
    depends_on:
      - postgres

  pgadmin:
    image: dpage/pgadmin4:7.4
    environment:
      - PGADMIN_DEFAULT_EMAIL=user@sample.com
      - PGADMIN_DEFAULT_PASSWORD=SuperSecret
    # Long Syntax - Map
    depends_on:
      postgres:
        condition: service_started
        restart: false
      server:
        condition: service_started
    profiles:
      - debug
```



#### Long Syntax

| Attributes  | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| `condition` | 상위 서비스의 상태 조건을 정의합니다. <br />- `service_started`: 서비스가 실행된 상태<br />- `service_healthy`: 서비스가 `healthy`인 상태<br />- `service_completed_successfully`: 서비스가 실행 완료된 상태 |
| `restart`   | 상위 서비스가 업데이트된 경우, 서비스를 재실행합니다.        |
| `required`  | `false`인 경우 상위 서비스가 실행되지 않았더라도 서비스를 실행합니다. |



### 2.3.5. health check 

> https://docs.docker.com/engine/reference/builder/#healthcheck

```yaml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
  start_interval: 5s
```

- `healthcheck`를 이용하여 서비스의 상태를 확인할 수 있습니다. 
- 시간은 `{value}{unit}` 형태로 작성하며, 지원하는 시간 단위는 다음과 같습니다. 
  - `us(microseconds)`
  - `ms(milliseconds)`
  - `s(seconds)`
  - `m(minutes)`
  - `h(hours)`



| Attributes       | Description                                                  |
| ---------------- | ------------------------------------------------------------ |
| `test`           | 컨테이너 상태 체크를 위한 명령어를 설정합니다. <br />`NONE`으로 설정한 경우 `Dockerfile`에 정의된 `HEALTHCHECK`를 비활성화 합니다. |
| `interval`       | 상태 체크 간격을 의미합니다.                                 |
| `timeout`        | 응답까지 걸리는 최대 시간을 의미합니다.<br />만약 해당 시간까지 응답이 도착하지 않으면 `fail`로 판단합니다. |
| `start_period`   | 컨테이너 실행까지 소요되는 시간을 의미합니다. <br />해당 시간동안에는 상태가 `fail`인 경우에도 카운트되지 않습니다. |
| `start_interval` | `start_period`내에서의 체크 간격을 의미합니다.               |
| `disable`        | `HEALTHCHECK`를 비활성화 합니다. <br />`test: ["NONE"]`과 동일합니다. |



### 연습문제

#### [연습] Anchor & Alias 이용하여 `yaml`파일 작성하기

```yaml
version: '3.8'

x-common:
  &common
  restart: always
  volumes:
    - source:/code
  environment:
    &default-env
    BY: "x-common"

services:
  ubuntu:
    <<: *common
    image: ubuntu:22.04
    environment:
      <<: *default-env
      FROM: "env definition"
    entrypoint: /bin/bash
    command:
      - -c
      - echo 'env from ${FROM}' && echo env from $${FROM}
    restart: no

volumes:
  source:

```



#### [연습] `depend_on`을 이용하여 `DB` 생성 후 `application` 실행하기

```yaml
# docker-compose.yaml
version: '3.8'

services:
  postgres:
    image: postgres:16.1-bullseye
    environment:
      - POSTGRES_PASSWORD=mysecretpassword

  server:
    image: ubuntu:22.04
    stdin_open: true # docker run -i
    tty: true        # docker run -t
    depends_on:
      - postgres

  pgadmin:
    image: dpage/pgadmin4:7.4
    environment:
      - PGADMIN_DEFAULT_EMAIL=user@sample.com
      - PGADMIN_DEFAULT_PASSWORD=SuperSecret
    depends_on:
      - postgres
      - server
    profiles:
      - debug
```



## 종합 문제

### [실습-1] `code-server` 실행을 위한 `docker-compose.yaml` 작성하기

- 다음과 같이 `secret`을 생성 및 정의합니다.

  - 다음 명령어를 이용하여 `password.txt`를 생성합니다. 

    > 임의의 텍스트 파일을 메모장등을 이용해서 생성해도 무방합니다. 

    ```cmd
    # Window - CMD
    $ echo %RANDOM% > password.txt 
    
    # Linux
    $ openssl rand -base64 14 > password.txt 
    ```

  - 위에서 생성한 `password.txt` 파일을 `docker-compose.yaml`에 정의합니다.

    - 이름은 `code-server-password`로 설정합니다.

- 다음과 같이 `volume`을 생성 및 정의합니다. 

  - `local_code` 이름으로 `volume`을 생성합니다. 
  - 생성한 `volume`을 `docker-compose.yaml`에 정의합니다.

- `.env` 파일을 다음과 같이 생성합니다. 

```tex
DEFAULT_WORKSPACE=/code
PUID=1000
PGID=1000
```

- `code-server` 서비스를 다음과 같이 정의합니다

  - 이미지는 `Docker Hub`에 `Push` 했던 `cloudwave:practice.v1`를 사용합니다.

  - 컨테이너 이름은 `ide`로 설정합니다. 

  - 다음과 같이 환경 변수를 설정합니다.

    ```tex
    FILE__PASSWORD: /run/secrets/<SECRET_NAME>
    ```

  - `.env` 파일을 이용하여 추가로 환경 변수를 설정합니다. 

  - `/code` 디렉토리를 `Working directory`로 설정합니다. 

  - 로컬 머신의 `8443` 포트와 컨테이너의 `8443` 포트를 바인딩합니다.

  - 다음 `secret`을 사용할 수 있도록 설정합니다. 

    - `code-server-password`

  - 다음과 같이 `volume`을 정의합니다. 

    - 로컬 머신의 `/var/run/docker.sock`를 컨테이너의 `/var/run/docker.sock`로 마운트합니다. 
    - `volume`의 `local_code`를 컨테이너의 `/code/local`에 마운트합니다. 

- 작성한 `docker-compose.yaml`을 이용하여 프로젝트를 실행합니다.

- 브라우저에서 `localhost:8443`을 입력하고 `VSC`에 접속합니다. 

  - 비밀번호는 생성한 `password.txt` 파일을 보고 확인합니다. 



### [실습-2] `git-sync` 서비스 추가하기

- 다음과 같이 `volume`을 추가로 생성 및 정의합니다. 

  - `remote_code` 이름으로 `volume`을 생성합니다. 
  - 생성한 `volume`을 `docker-compose.yaml`에 정의합니다.

- `GitSync` 서비스를 다음과 같이 정의합니다.

  - 이미지는 `registry.k8s.io/git-sync/git-sync:v4.1.0`를 사용합니다.

  - `source_code` 볼륨을 `/tmp`에 마운트합니다.

  - 다음과 같이 환경 변수를 설정합니다. 

    ```tex
    GITSYNC_REPO=https://github.com/matenduel/cloudwave
    GITSYNC_ROOT=/tmp/git
    GITSYNC_REF=main
    GITSYNC_DEPTH=1
    GITSYNC_ONE_TIME=1
    ```

  - `profile`이 `init`인 경우에만 실행되도록 설정하세요.

- `code-server` 서비스에 다음 사항을 추가로 정의합니다.

  - `volume`의 `remote_code`를 컨테이너의 `/code/remote`에 마운트합니다. 
  - `depend_on`을 이용하여 `GitSync` 컨테이너가 생성된 다음 컨테이너가 실행되도록 설정하세요.
    - `GITSYNC_ONE_TIME`가 `1`이라면 `service_completed_successfully` 로 설정하세요.
    - `GITSYNC_ONE_TIME`가 설정되지 않았다면 `service_started` 로 설정하세요.


### [실습-3] `build`를 이용한 `docker compose` 프로젝트 생성하기

- 다음 조건을 만족하는 `Dockerfile`을 작성합니다. 

  - `ubuntu:22.04`이미지를 베이스로 사용합니다. 
  - `apt-get update`와 `apt-get upgrade`를 수행해야 합니다. 
  - `apt`를 이용해서 `dnsutils`와 `wget`을 설치합니다.

- `ubuntu` 서비스를 다음과 같이 정의합니다.

  - 컨테이너의 이름을 `server`로 설정합니다. 
  - 이미지 이름은 `cloudwave:ubuntu.dig.v1`으로 설정합니다.
  - 위에서 만든 `Dockerfile`을 기반으로 빌드되도록 설정합니다. 
  - 컨테이너가 정지되지 않도록 다음과 같이 `command`를 설정합니다.
    - `sleep infinity`

- `web-app` 서비스를 다음과 같이 정의합니다. 

  - 이미지는 `nginx:latest`를 사용합니다. 
  - `replicas`를 3으로 설정합니다.
  - 80번 포트를 `Expose` 합니다. 

- 작성한 `docker-compose.yaml`을 이용하여 이미지를 빌드한 다음 프로젝트를 실행합니다.

- `server` 컨테이너에 접속하여 다음 명령어를 실행합니다. 

  ```
  dig web-app
  ```
