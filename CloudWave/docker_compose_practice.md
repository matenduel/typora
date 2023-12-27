

## 2. Docker Compose

- https://docs.docker.com/compose/production/
- 



## 2.1. Compose CLI

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
- 프로젝트 이름이 설정되지 않은 경우, 현재 디렉토리 이름이 프로젝트 이름으로 설정됩니다. 



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



### `compose` 버젼 확인

> https://docs.docker.com/engine/reference/commandline/compose_version/

```
docker compose version [OPTIONS]
```



**출력 예시**

```cmd
$ docker compose version
Docker Compose version v2.18.1
```



### 프로젝트 실행

> https://docs.docker.com/engine/reference/commandline/compose_up/

```sh
docker compose up [OPTIONS] [SERVICE...]
```



**주요 Options**

| Option                      | Short | Default | Description                                                  |
| --------------------------- | ----- | ------- | ------------------------------------------------------------ |
| `--abort-on-container-exit` |       |         | Stops all containers if any container was stopped. Incompatible with -d |
| `--build`                   |       |         | Build images before starting containers.                     |
| `--detach`                  | `-d`  |         | Detached mode: Run containers in the background              |
| `--force-recreate`          |       |         | Recreate containers even if their configuration and image haven't changed. |
| `--remove-orphans`          |       |         | Remove containers for services not defined in the Compose file. |



**주의사항**

- 서로 다른  `docker-compose.yaml`의 프로젝트 명이 중복된 경우, 각 파일에 명시된 모든  `component`들이 적용됩니다. 



**출력 예시**

```cmd
$ docker compose up -d
[+] Building 0.0s (0/0)                                                                                         
[+] Running 6/6
 ✔ Network private                  Created                                                                0.0s 
 ✔ Network db                       Created                                                                0.0s 
 ✔ Volume "db-data"                 Created                                                                0.0s 
 ✔ Container cloud_wave-frontend-1  Started                                                                0.6s 
 ✔ Container cloud_wave-server-1    Started                                                                0.7s 
 ✔ Container db_postgres            Started                                                                0.6s 
```



### 프로젝트 목록

> https://docs.docker.com/engine/reference/commandline/compose_ls/

```sh
docker compose ls [OPTIONS]
```



**출력 예시**

```cmd
$ docker compose ls
NAME               STATUS              CONFIG FILES
cloud_wave         running(3)          <PROJECT_PATH>/docker-compose.yaml
```



### 컨테이너 목록

> https://docs.docker.com/engine/reference/commandline/compose_ps/

```
docker compose ps [OPTIONS] [SERVICE...]
```



**주요 Options**

| Option     | Short | Default | Description                                                  |
| ---------- | ----- | ------- | ------------------------------------------------------------ |
| `--all`    | `-a`  |         | Show all stopped containers (including those created by the run command) |
| `--filter` |       |         | Filter services by a property (supported filters: status).   |
| `--status` |       |         | Filter services by status. Values: [paused \| restarting \| removing \| running \| dead \| created \| exited] |



**주의 사항**

- CLI를 통해 전달된 `project-name`또는  `configuration file`에 명시된 `name`을 기반으로 조회합니다.  



**출력 예시**

```cmd
$ docker compose ps
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
- 

**출력 예시**

```cmd
$ docker compose images
docker compose images
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



**주요 Options**

| Option             | Short | Default | Description                                                  |
| ------------------ | ----- | ------- | ------------------------------------------------------------ |
| `--remove-orphans` |       |         | Remove containers for services not defined in the Compose file. |
| `--rmi`            |       |         | Remove images used by services. "local" remove only images that don't have a custom tag ("local"\|"all") |
| `--timeout`        | `-t`  |         | Specify a shutdown timeout in seconds                        |
| `--volumes`        | `-v`  |         | Remove named volumes declared in the "volumes" section of the Compose file and anonymous volumes attached to containers. |



**주의 사항**

- 현재 디렉토리에 `docker-compose.yaml` 파일이 존재하지 않는 경우, `-p` 또는 `-f`를 이용하여야 합니다. 
- 다른 프로젝트에서 사용중인 리소스(`volume`, `network`, ...)는 삭제되지 않습니다. 



**출력 예시**



**출력 예시**

```cmd
$ docker compose -p cloud_wave down
[+] Running 3/3
 ✔ Container cloud_wave-postgres-1  Removed                                                               0.1s 
 ✔ Container cloud_wave-frontend-1  Removed                                                               0.0s 
 ✔ Container cloud_wave-server-1    Removed                                                               10.1s 
```



### 재실행

> https://docs.docker.com/engine/reference/commandline/compose_restart/

```shell
docker compose restart [OPTIONS] [SERVICE...]
```

**주의 사항** 

Restarts all stopped and running services, or the specified services only.

If you make changes to your `compose.yml` configuration, these changes are not reflected after running this command. For example, changes to environment variables (which are added after a container is built, but before the container's command is executed) are not updated after restarting.

**출력 예시**

```cmd
$ docker compose -p cloud_wave restart
[+] Restarting 3/3
 ✔ Container cloud_wave-frontend-1  Started                                                                0.5s 
 ✔ Container db_postgres            Started                                                                0.5s 
 ✔ Container cloud_wave-server-1    Started                                                                0.3s
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



**출력 예시 - Stop** 

```cmd
$  docker compose -p cloud_wave stop
[+] Stopping 2/3
 ✔ Container cloud_wave-postgres-1  Stopped                                                                0.1s  
 ✔ Container cloud_wave-server-1    Stopped                                                               10.2s 
 ✔ Container cloud_wave-frontend-1  Stopped                                                                0.1s 
```

**출력 예시 - Start**

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



**출력 예시 - Pause** 

```cmd
$  docker compose -p cloud_wave pause
[+] Pausing 2/0
 ✔ Container cloud_wave-frontend-1  Paused                                                                 0.0s 
 ✔ Container db_postgres            Paused                                                                 0.0s 
```

**출력 예시 - Unpause**

```cmd
$ docker compose -p cloud_wave unpause
[+] Running 2/0
 ✔ Container db_postgres            Unpaused                                                               0.0s 
 ✔ Container cloud_wave-frontend-1  Unpaused                                                               0.0s 
```



### 서비스 빌드

> https://docs.docker.com/engine/reference/commandline/compose_build/

```shell
docker compose build [OPTIONS] [SERVICE...]
```



**주요  Options**

| Option        | Short | Default | Description                                                  |
| ------------- | ----- | ------- | ------------------------------------------------------------ |
| `--build-arg` |       |         | Set build-time variables for services.                       |
| `--builder`   |       |         | Set builder to use.                                          |
| `--memory`    | `-m`  |         | Set memory limit for the build container. Not supported by BuildKit. |
| `--no-cache`  |       |         | Do not use cache when building the image                     |
| `--progress`  |       | `auto`  | Set type of ui output (auto, tty, plain, quiet)              |
| `--pull`      |       |         | Always attempt to pull a newer version of the image.         |
| `--push`      |       |         | Push service images.                                         |
| `--quiet`     | `-q`  |         | Don't print anything to STDOUT                               |
| `--ssh`       |       |         | Set SSH authentications used when building service images. (use 'default' for using your default SSH Agent) |



**주의 사항** 

- `yaml` 파일에 명시된 서비스의 `image`를 이름으로 한 이미지가 생성됩니다. 



**출력 예시**

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







### 로그 확인

> https://docs.docker.com/engine/reference/commandline/compose_logs/

```
docker compose logs [OPTIONS] [SERVICE...]
```



**주요 Options**

| Option            | Short | Default | Description                                                  |
| ----------------- | ----- | ------- | ------------------------------------------------------------ |
| `--follow`        | `-f`  |         | Follow log output.                                           |
| `--no-color`      |       |         | Produce monochrome output.                                   |
| `--no-log-prefix` |       |         | Don't print prefix in logs.                                  |
| `--since`         |       |         | Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes) |
| `--tail`          | `-n`  | `all`   | Number of lines to show from the end of the logs for each container. |
| `--timestamps`    | `-t`  |         | Show timestamps.                                             |
| `--until`         |       |         | Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes) |



**주의사항**

- `--tail`은 전체가 아닌 각 컨테이너별 로그 수를 의미합니다. 



**출력 예시**

```cmd
$ docker compose -p cloud_wave logs -n 2
cloud_wave-frontend-1  | 2023/12/27 08:28:52 [notice] 1#1: start worker process 23
cloud_wave-frontend-1  | 2023/12/27 08:28:52 [notice] 1#1: start worker process 24
db_postgres            | 2023-12-27 08:28:52.215 UTC [29] LOG:  database system was shut down at 2023-12-27 08:28:51 UTC
db_postgres            | 2023-12-27 08:28:52.219 UTC [1] LOG:  database system is ready to accept connections
```



### 프로세스 확인

> https://docs.docker.com/engine/reference/commandline/compose_top/

```shell
docker compose top [SERVICES...]
```

**출력 예시**

```cmd
$ docker compose top              
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

**출력 예시**

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



**출력 예시**

```cmd
$ docker compose -p cloud_wave rm  
? Going to remove db_postgres, cloud_wave-frontend-1, cloud_wave-server-1 Yes
[+] Removing 3/0
 ✔ Container cloud_wave-frontend-1  Removed                                                                0.0s 
 ✔ Container db_postgres            Removed                                                                0.0s 
 ✔ Container cloud_wave-server-1    Removed                                                                0.0s 
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



#### [연습] 마지막 5개 로그만 확인하기

```cmd
$ docker compose logs -n 5 
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



#### [연습] 프로젝트 삭제하기

다음 `docker-compose.yaml`파일을 이용하여 프로젝트를 실행합니다. 

`docker-compose.yaml`

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

**출력 예시**

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



##### [연습] `.env`를 이용하여 서비스 설정 변경하기



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



[연습] env 설정하기 

-> override 예시



### volume

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





### network

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





### config

```yaml
configs:
  http_config:
    file: ./httpd.conf
```





### secret

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



### 연습 문제

#### [연습] 읽기 전용 `volume` 마운트하기



#### [연습] 생성된 `network`를 이용하여 서로 다른 프로젝트의 서비스 연결하기

```yaml
```





#### [연습]

#### [연습] 



#### [실습] `git-sync`를 이용하여 `FastAPI` 서버 실행하기







[연습] depend_on을 이용하여 db 생성 후 application 실행하기



[연습] health_check 사용하기



[연습] .env 파일 및 secret 관리



[연습] build를 사용하여 `base`이미지에 패키지 추가로 설치하기



[연습] `network`를 이용하여 서로다른 프로젝트를 연결하기



[연습] `profile` 예제 -> like airflow







## Compose file - Advance



#### anchor & Alias

> https://medium.com/@kinghuang/docker-compose-anchors-aliases-extensions-a1e4105d70bd

#### profile

#### deploy

> https://docs.docker.com/compose/compose-file/deploy/



#### depend_on

> https://docs.docker.com/compose/startup-order/





### 연습문제





## 종합 문제

[실습-1] `cloudwave:base.v1`에 `terraform` 설치하기





 [실습-2] 컨테이너를 사용하여 `terraform` 코드 실행하기





[실습 -3] Web & DB로 구성된 docker compose 구성하기 + PgAdmin은 profile로 구상 + Init용 컨테이너 추가







