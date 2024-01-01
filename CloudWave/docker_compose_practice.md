

## 2. Docker Compose

- https://docs.docker.com/compose/production/
- 



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

`docker-compose`를 위한 `configuration` 파일은 `YAML`을 이용하여 작성합니다. 



`Python`과 같이 들여쓰기를 기반으로 구조를 결정합니다. 



생성, 삭제, 업데이트 시 `config file`에 명시된 이름을 기반으로 움직입니다. 

-> network 이름을 수정후 down하는 경우 기존 네트워크 삭제 x



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

The top-level `version` property is defined by the Compose Specification for backward compatibility. It is only informative.

Compose doesn't use `version` to select an exact schema to validate the Compose file, but prefers the most recent schema when it's implemented.

Compose validates whether it can fully parse the Compose file. If some fields are unknown, typically because the Compose file was written with fields defined by a newer version of the Specification, you'll receive a warning message. Compose offers options to ignore unknown fields (as defined by ["loose"](https://docs.docker.com/compose/compose-file/01-status/#requirements-and-optional-attributes) mode).



### 2.2.2. Name

프로젝트의 이름을 의미합니다. 

프로젝트의 이름은 다음과 같은 순서로 결정됩니다. 

1. `Compose CLI`의 `-p / --project-name`가 설정된 경우
2. `configuration file(docker-compose.yaml)`에 `name`이 설정된 경우
3. `directory` 이름







### 2.2.3. Service

> https://docs.docker.com/compose/compose-file/03-compose-file/

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

- 필수값
- 



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
	- INT_OR_STRING
```

- `expose` defines the ports that Compose exposes from the container. These ports must be accessible to linked services and should not be published to the host machine. Only the internal container ports can be specified.



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



- `HOST` is `[IP:](port | range)`
- `CONTAINER` is `port | range`
- `PROTOCOL` to restrict port to specified protocol. `tcp` and `udp` values are defined by the Specification, Compose offers support for platform-specific protocol names.



#### entrypoints

```yaml
# String
entrypoint: STRING
# List
entrypoint:
	- STRING
	- STRING
```

`entrypoint` declares the default entrypoint for the service container. This overrides the `ENTRYPOINT` instruction from the service's Dockerfile.

If `entrypoint` is non-null, Compose ignores any default command from the image, for example the `CMD` instruction in the Dockerfile.

See also [`command`](https://docs.docker.com/compose/compose-file/05-services/#command) to set or override the default command to be executed by the entrypoint process.



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



`command` overrides the default command declared by the container image, for example by Dockerfile's `CMD`.

If the value is `null`, the default command from the image is used.

If the value is `[]` (empty list) or `''` (empty string), the default command declared by the image is ignored, i.e. overridden to be empty.



#### environment

`environment` defines environment variables set in the container. `environment` can use either an array or a map. Any boolean values; true, false, yes, no, should be enclosed in quotes to ensure they are not converted to True or False by the YAML parser.

Environment variables can be declared by a single key (no value to equals sign). In this case Compose relies on you to resolve the value. If the value is not resolved, the variable is unset and is removed from the service container environment.

Map syntax:

content_copy

```yml
environment:
  RACK_ENV: development
  SHOW: "true"
  USER_INPUT:
```

Array syntax:

```yml
environment:
  - RACK_ENV=development
  - SHOW=true
  - USER_INPUT
```

When both `env_file` and `environment` are set for a service, values set by `environment` have precedence.



#### env_file

`env_file` adds environment variables to the container based on the file content.

```yml
env_file: .env
```

`env_file` can also be a list. The files in the list are processed from the top down. For the same variable specified in two env files, the value from the last file in the list stands.

```yml
env_file:
  - ./a.env
  - ./b.env
```

Relative path are resolved from the Compose file's parent folder. As absolute paths prevent the Compose file from being portable, Compose warns you when such a path is used to set `env_file`.

Environment variables declared in the [environment](https://docs.docker.com/compose/compose-file/05-services/#environment) section override these values. This holds true even if those values are empty or undefined.



Each line in an `.env` file must be in `VAR[=[VAL]]` format. The following syntax rules apply:



#### build

> https://docs.docker.com/compose/compose-file/build/





#### pull_policy

`pull_policy` defines the decisions Compose makes when it starts to pull images. Possible values are:

- `always`: Compose always pulls the image from the registry.
- `never`: Compose doesn't pull the image from a registry and relies on the platform cached image. If there is no cached image, a failure is reported.
- `missing`: Compose pulls the image only if it's not available in the platform cache. This is the default option if you are not also using the [Compose Build Specification](https://docs.docker.com/compose/compose-file/build/). `if_not_present` is considered an alias for this value for backward compatibility.
- `build`: Compose builds the image. Compose rebuilds the image if it's already present.

If `pull_policy` and `build` are both present, Compose builds the image by default. This behavior may be overridden in the toolchain, depending on the implementation.

#### restart

`restart` defines the policy that the platform applies on container termination.

- `no`: The default restart policy. It does not restart the container under any circumstances.
- `always`: The policy always restarts the container until its removal.
- `on-failure`: The policy restarts the container if the exit code indicates an error.
- `unless-stopped`: The policy restarts the container irrespective of the exit code but stops restarting when the service is stopped or removed.

```yml
    restart: "no"
    restart: always
    restart: on-failure
    restart: unless-stopped
```



#### platform

`platform` defines the target platform the containers for the service run on. It uses the `os[/arch[/variant]]` syntax.

The values of `os`, `arch`, and `variant` must conform to the convention used by the [OCI Image Specopen_in_new](https://github.com/opencontainers/image-spec/blob/v1.0.2/image-index.md).

Compose uses this attribute to determine which version of the image is pulled and/or on which platform the service’s build is performed.

```yml
platform: darwin
platform: windows/amd64
platform: linux/arm64/v8
```



#### health check 

`healthcheck` declares a check that's run to determine whether or not the service containers are "healthy". It works in the same way, and has the same default values, as the [HEALTHCHECK Dockerfile instructionopen_in_new](https://docs.docker.com/engine/reference/builder/#healthcheck) set by the service's Docker image. Your Compose file can override the values set in the Dockerfile.

```yml
healthcheck:
  test: ["CMD", "curl", "-f", "http://localhost"]
  interval: 1m30s
  timeout: 10s
  retries: 3
  start_period: 40s
  start_interval: 5s
```

`interval`, `timeout`, `start_period`, and `start_interval` are [specified as durations](https://docs.docker.com/compose/compose-file/11-extension/#specifying-durations).

`test` defines the command Compose runs to check container health. It can be either a string or a list. If it's a list, the first item must be either `NONE`, `CMD` or `CMD-SHELL`. If it's a string, it's equivalent to specifying `CMD-SHELL` followed by that string.

```yml
# Hit the local web app
test: ["CMD", "curl", "-f", "http://localhost"]
```

Using `CMD-SHELL` runs the command configured as a string using the container's default shell (`/bin/sh` for Linux). Both forms below are equivalent:

```yml
test: ["CMD-SHELL", "curl -f http://localhost || exit 1"]
test: curl -f https://localhost || exit 1
```

`NONE` disables the healthcheck, and is mostly useful to disable the Healthcheck Dockerfile instruction set by the service's Docker image. Alternatively, the healthcheck set by the image can be disabled by setting `disable: true`:

```yml
healthcheck:
  disable: true
```



#### CLI option <-> Service elements

|      | CLI Options | Service Elements |
| ---- | ----------- | ---------------- |
|      | -i          | stdin_open       |
|      | -t          |                  |
|      | -p          |                  |
|      | -e          |                  |
|      | --env-files |                  |
|      | --name      | container_name   |







#### 연습 문제

##### [연습] `ubuntu` 서버 실행하기

아래에 있는 `docker-compose.yaml` 파일을 이용하여 프로젝트를 실행할 경우, `ubuntu` 서비스가 생성 후 바로 종료되는 것을 확인할 수 있습니다. 

`docker-compose.yaml`

```yaml
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



##### [연습] `command`에서 컨테이너 환경변수 사용하

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



##### TODO [연습] health check

```yaml
services:
  numbers-api:
    image: diamol/ch08-numbers-api:v3
    ports:
      - "8087:80"
    healthcheck:
      interval: 5s
      timeout: 1s
      retries: 2
      start_period: 5s
    networks:
      - app-net
```

CASE

- Health check 설정하여 체크
- timeout 유도하여 체크
- start_period 유도하여 체크





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

`volume`은 다음 5가지 속성값을 통해 정의합니다. 

1. name
2. external
3. labels
4. driver
5. driver_opts



**주의 사항**

- `volume`을 정의하였더라도 service에서 사용하지 않았다면 생성되지 않습니다. 



#### 볼륨 사용하기

정의된 볼륨은 서비스의 `volumes`에서 사용할 수 있습니다. 

두 가지 방식으로 사용할 volumes를 작성할 수 있습니다. long / short



##### Short Syntax

short는 `docker run`에서 사용하던 방식과 동일합니다. 

The short syntax uses a single string with colon-separated values to specify a volume mount (`VOLUME:CONTAINER_PATH`), or an access mode (`VOLUME:CONTAINER_PATH:ACCESS_MODE`).

- `VOLUME`: Can be either a host path on the platform hosting containers (bind mount) or a volume name.

- `CONTAINER_PATH`: The path in the container where the volume is mounted.

- ```
    ACCESS_MODE
    ```

    : A comma-separated

     

    ```
    ,
    ```

     

    list of options:

    - `rw`: Read and write access. This is the default if none is specified.
    - `ro`: Read-only access.
    - `z`: SELinux option indicating that the bind mount host content is shared among multiple containers.
    - `Z`: SELinux option indicating that the bind mount host content is private and unshared for other containers.



##### Long Syntax

`long syntax` 방식은 short보다 더 많은 속성을 제어할 수 있습니다. 

- `type`: The mount type. Either `volume`, `bind`, `tmpfs`, `npipe`, or `cluster`

- `source`: The source of the mount, a path on the host for a bind mount, or the name of a volume defined in the [top-level `volumes` key](https://docs.docker.com/compose/compose-file/07-volumes/). Not applicable for a tmpfs mount.

- `target`: The path in the container where the volume is mounted.

- `read_only`: Flag to set the volume as read-only.

- ```
    bind
    ```

    : Used to configure additional bind options:

    - `propagation`: The propagation mode used for the bind.
    - `create_host_path`: Creates a directory at the source path on host if there is nothing present. Compose does nothing if there is something present at the path. This is automatically implied by short syntax for backward compatibility with `docker-compose` legacy.
    - `selinux`: The SELinux re-labeling option `z` (shared) or `Z` (private)

- ```
    volume
    ```

    : Configures additional volume options:

    - `nocopy`: Flag to disable copying of data from a container when a volume is created.

- ```
    tmpfs
    ```

    : Configures additional tmpfs options:

    - `size`: The size for the tmpfs mount in bytes (either numeric or as bytes unit).
    - `mode`: The file mode for the tmpfs mount as Unix permission bits as an octal number.

- `consistency`: The consistency requirements of the mount. Available values are platform specific.

 



#### 연습 문제

##### [연습] External volume 사용하기

```cmd
$ docker volume create vault
vault
```



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

TODO nocopy 추가





##### [연습] read_only 로 설정하여 사용하기

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
$ docker compose -f example2/docker-compose.yaml up -d
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 2/2
 ✔ Container volume-external-master-1  Started                                                                                                                                                                                     0.0s 
 ✔ Container volume-external-slave-1   Started                                                                                                                                                                                     0.0s 

```





```cmd
$ docker exec volume-external-master-1 /bin/bash -c "echo master > /root/vault/temp.txt"

$ docker exec volume-external-master-1 /bin/bash -c "cat /root/vault/temp.txt"        
master
```



```cmd
$ docker exec volume-external-slave-1 /bin/bash -c "cat /root/vault/temp.txt"         
master
$ docker exec volume-external-slave-1 /bin/bash -c "echo slave > /root/vault/temp.txt"
/bin/bash: line 1: /root/vault/temp.txt: Read-only file system
```









##### [연습] volumes_from

> https://docs.docker.com/compose/compose-file/05-services/#volumes



```yaml
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
 ~/repo/typora/CloudWave/code/compose  main +13 !12 ?3                                                                                                                                                                                 
> docker compose -f example2/docker-compose2.yaml up -d                       
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 2/2
 ✔ Network volume-external2_default    Created                                                                                                                                                                                     0.1s 
 ✔ Container volume-external2-other-1  Started                                                                                                                                                                                     0.0s 

 ~/repo/typora/CloudWave/code/compose  main +13 !12 ?3                                                                                                                                                                                 
> docker exec volume-external2-other-1 /bin/bash -c "cat /root/vault/temp.txt"
master

```





#### [실습] `git-sync`를 이용하여 `FastAPI` 서버 실행하기

조건 

- `git-sync`는 rw
- `FastAPI`는 ro
- 디렉토리 지정
- 



### 2.2.5. network

```yaml
services:
  frontend:
    image: example/webapp
    networks:
      - front-tier
      - back-tier

networks:
  front-tier:
  back-tier:
```



Attributes

- external
- internal
- labels
- name
- driver





#### 네트워크 정의하기





#### 네트워크 사용하기





#### 연습 문제 

##### TODO [연습] `host`네트워크 사용하기 

```cmd
$ docker network ls -f driver=host
NETWORK ID     NAME      DRIVER    SCOPE
b107e82764b5   host      host      local
```



```yaml
version: '3.8'
name: 'network-host'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      host:

networks:
  host:
    name: "host"
    external: true
```



```cmd
$  docker compose up -d
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/0
 ✔ Container network-host-ubuntu-1  Created                                                                                                                                                                                        0.0s 
Error response from daemon: network-scoped alias is supported only for containers in user defined networks
```

-> 에러 있는 듯

```yaml
```









##### [연습] 이미 생성된 네트워크 사용하기

```cmd
$ docker network create private -d bridge
1be27c483205a3b2dfbdf4ae36c2cc20ae0a53bc939eb7bd050a65574a9b9299

$ docker network ls -f name=private      
NETWORK ID     NAME                    DRIVER    SCOPE
1be27c483205   private                 bridge    local
```





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
    external: true
```



```cmd
$ docker compose up -d                                                                             
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/1
 ✔ Container network-external-ubuntu-1  Started   
```



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







##### [연습] alias 설정하기

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

networks:
  private:
```



```cmd
$ docker inspect network-alias-ubuntu-1 | jq ".[0].NetworkSettings.Networks" | jq -r '.[].Aliases' 
[
  "network-alias-ubuntu-1",
  "ubuntu",
  "d1295a0aae14"
]
```





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
$  docker inspect network-alias-ubuntu-1 | jq ".[0].NetworkSettings.Networks" | jq -r '.[].Aliases' 
[
  "network-alias-ubuntu-1",
  "ubuntu",
  "server",
  "1679779c1978"
]
```







#### [실습] 생성된 `network`를 이용하여 서로 다른 프로젝트의 서비스 연결하기

조건

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
- `ubuntu`서버에서 curl을 이용하여 `web`을 호출하세요
    - IP
    - DNS(alias)



```cmd
$ docker network create across_project
24ca828a4c797fb8a6f13d125af6493e0ba5871c5ac1b94a6bdaf687aa1620cc

```





```yaml
version: '3.8'
name: 'network-across-project-1'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private: {}

networks:
  private:
    name: "across_project"
    external: true
---
version: '3.8'
name: 'network-across-project-2'

services:
  ubuntu:
    image: ubuntu:22.04
    entrypoint: /bin/bash
    command:
      - -c
      - sleep infinity
    networks:
      private: {}

networks:
  private:
    name: "across_project"
    external: true
```



```cmd
$ docker compose -f project-1.yaml up -d
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/1
 ✔ Container network-across-project-1-ubuntu-1  Started      
 
$ docker compose -f project-2.yaml up -d
[+] Building 0.0s (0/0)                                                                                                                                                                                            docker:desktop-linux
[+] Running 1/1
 ✔ Container network-across-project-2-ubuntu-1  Started            
```



```cmd
$  docker network inspect across_project | jq '.[].Containers'
{
  "29e55aa6d000ec061e25b5f1d1dccd176e44a49b76cacc57cd8d0b2c8c8e5c76": {
    "Name": "network-across-project-2-ubuntu-1",
    "EndpointID": "a816b9271bc9e9c379d139e88dfb50acc9dd5af18e0fbcee500006321eaa9324",
    "MacAddress": "02:42:ac:1b:00:03",
    "IPv4Address": "172.27.0.3/16",
    "IPv6Address": ""
  },
  "2e4086cf6fa0378e330992491d2cf50a08108fdd730034b4a5ee82b697ae7bdb": {
    "Name": "network-across-project-1-ubuntu-1",
    "EndpointID": "2ce7ba6805c0839b91c16f4b2a363935b830aba544546b71f91d7bce7c8709dc",
    "MacAddress": "02:42:ac:1b:00:02",
    "IPv4Address": "172.27.0.2/16",
    "IPv6Address": ""
  }
}
```







### config & secret

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
configs:
  my_config:
    external: true
  http_config:
    file: ./httpd.conf
```



```yaml
services:
  frontend:
    image: example/webapp
    secrets:
      - source: server-certificate
        target: server.cert
        uid: "103"
        gid: "103"
        mode: 0440
secrets:
  server-certificate:
    file: ./server.cert
```



Secrets are a flavor of [Configs](https://docs.docker.com/compose/compose-file/08-configs/) focusing on sensitive data, with specific constraint for this usage.

Services can only access secrets when explicitly granted by a [`secrets`](https://docs.docker.com/compose/compose-file/05-services/#secrets) attribute within the `services` top-level element.



#### Secret관리

> https://docs.docker.com/compose/use-secrets/



#### Config Vs. Secret

-> swarm을 쓰지 않는 상황에서 차이는 없음



#### Config Vs. Mount

-> 차이 거의 없음

-> 세부 권한을 다르게 가능?



#### mode

이런 문제가 생기는 경우를 방지하기 위해, 리눅스에서는 각 파일 및 디렉토리에 대해 **"읽기(r)", "쓰기(w)", "실행(x)"** 권한(permission)을 파일 개별적으로 지정할 수 있도록 만들어 놓았습니다. 그리고 이 세 가지 권한을 **"파일을 소유한 사용자(user)", "특정 그룹(group)에 소속된 사용자", "그 외 사용자(others)"**에 대해 각각 지정할 수 있게 만들었습니다.



#### 연습 문제

##### [연습] config를 특정 디렉토리에 mount하기



다음 명령어를 사용하여 파일 생성

```cmd
$ openssl rand -base64 14 > password.txt 
```



```yaml
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







## Compose file - Advance



#### anchor & Alias

> https://medium.com/@kinghuang/docker-compose-anchors-aliases-extensions-a1e4105d70bd
>
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

##### `x-` prefix



##### `<<:` 의미 



#### profile

> https://docs.docker.com/compose/profiles/
>
> https://docs.docker.com/compose/compose-file/15-profiles/

```yaml
services:
  frontend:
    image: frontend
    profiles: ["frontend"]

  phpmyadmin:
    image: phpmyadmin
    depends_on:
      - db
    profiles:
      - debug

  backend:
    image: backend

  db:
    image: mysql
```





#### deploy

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







#### depend_on

> https://docs.docker.com/compose/startup-order/
>
> https://docs.docker.com/compose/compose-file/05-services/#depends_on

```yaml
# Short
services:
  web:
    build: .
    depends_on:
      - db
      - redis
  redis:
    image: redis
  db:
    image: postgres

# Long
services:
  web:
    build: .
    depends_on:
      db:
        condition: service_healthy
        restart: true
      redis:
        condition: service_started
  redis:
    image: redis
  db:
    image: postgres
```







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





#### [연습] depend_on을 이용하여 db 생성 후 application 실행하기



#### [연습] `profile` 예제 -> like airflow

```yaml
# PostgreSQL & PgAdmin
```





#### [연습] deploy 이용하여 동일한 서비스 여러개 띄우기

```yaml
```





#### [연습] deploy 이용하여 update 설정 제어하기

```yaml
```







## 종합 문제

### [실습-1] 컨테이너를 사용하여 `terraform` 코드 실행하기

- `source_code`이름을 가진 `volume`을 생성합니다. 

- `GitSync` 컨테이너에 실행합니다.

    > https://github.com/kubernetes/git-sync/releases/tag/v4.1.0

    - 이미지는 `registry.k8s.io/git-sync/git-sync:v4.1.0`를 사용합니다.

    - `source_code` 볼륨을 `/code`에 마운트합니다.

    - 다음과 같이 환경 변수를 설정합니다. 

        ```tex
        GITSYNC_REPO=https://github.com/matenduel/code_kata
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

- 



### [실습-3] Web Appliation 서버?

> (deploy) replica 설정
>
> health check 설정
>
> anchor&alias 설정 -> Anchor는 제시해주기?



### [실습-2] EC2 인스턴스에서 컨테이너 실행하기

> Web & DB로 구성된 docker compose 구성하기 + PgAdmin은 profile로 구상 + Init용 컨테이너 추가







