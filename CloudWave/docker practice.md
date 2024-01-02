[TOC]

# 0. 시작하기 전에

일부 실습은 AWS 환경에서 진행되며 `Terraform`을 이용하여 생성할 예정입니다. 

자세한 세팅 방법은 `Appendix`를 참조해주세요.



## Docker 설치하기

### Windows

> 다운로드 [링크](https://desktop.docker.com/win/main/amd64/Docker%20Desktop%20Installer.exe)

- Docker Desktop을 설치합니다. 



### Ubuntu

```shell
sudo apt-get update
sudo apt-get install docker.io
sudo ln -sf /usr/bin/docker.io /usr/local/bin/docker

# 권한 변경 
sudo chmod 666 /var/run/docker.sock
```





# 1. Docker

## 1.1. Docker 이미지 관리

### 이미지 검색

> https://docs.docker.com/engine/reference/commandline/search/

```shell
docker search [OPTIONS] <Keyword>
```



다음은 `ubuntu` 이미지를 검색한 결과입니다. 

![cmd_docker_search_result](docker practice.assets/cmd_docker_search_result.PNG)



CLI 대신 Web에서도 사용할 이미지를 검색할 수 있습니다. 

> https://hub.docker.com/

![docker_search_hub_result](docker practice.assets/docker_search_hub_result.PNG)



### 이미지 다운로드

> https://docs.docker.com/engine/reference/commandline/pull/

```shell
docker pull [OPTIONS] REPOSITORY[:TAG|@DIGEST]

# Default
docker pull <REPOSITORY>:latest

# By Tag
docker pull <REPOSITORY>:<TAG>

# By digest
docker pull <REPOSITORY>@<DIGEST>
```



**주요 옵션**

| Option       | Short | Default | Description                          |
| ------------ | ----- | ------- | ------------------------------------ |
| `--all-tags` | `-a`  |         | 태그된 모든 이미지를 다운로드합니다. |
| `--platform` |       |         | 이미지의 `platform`을 설정합니다.    |



`Tag` 또는 `Digest`를 사용하지 않는 경우, `latest` 이미지를 다운로드 받습니다. 

```cmd
$ docker pull ubuntu

Using default tag: latest
latest: Pulling from library/ubuntu
Digest: sha256:6042500cf4b44023ea1894effe7890666b0c5c7871ed83a97c36c76ae560bb9b
Status: Downloaded newer image for ubuntu:latest
docker.io/library/ubuntu:latest
```



#### Tag & Digest

- `Tag`는 사용자가 특정 이미지에 부여한 값이므로, 같은 `Tag`라도 다운로드 시점에 따라 다른 이미지를 나타낼 수 있습니다. 
  - ex) `latest` 태그
- `Digest`는 변경이 불가능한 고유의 값이며, 동일한 방식으로 생성된 이미지라면 동일한 `Digest`가 생성됩니다. 



**주의사항**

- 동일한 `Tag`를 가진 이미지가 존재하는 경우, 이미지를 교체하지 않습니다.
- 동일한 이미지더라도 `Tag`가 다른 경우 이미지가 추가됩니다. 
- 이미지마다 지원하는 `arch`가 다를 수 있습니다. 
  - `arch` = arm, amd64, ...




### 이미지 목록 보기

> https://docs.docker.com/engine/reference/commandline/images/

```shell
docker images [OPTIONS] [REPOSITORY[:TAG]]

# Alias
docker image list
docker image ls
```



**주요 옵션**

| Option       | Short | Default | Description                                     |
| ------------ | ----- | ------- | ----------------------------------------------- |
| `--all`      | `-a`  |         | 중간 이미지(`intermediate image`)도 표시합니다. |
| `--digests`  |       |         | `Digest`를 표기합니다.                          |
| `--filter`   | `-f`  |         | 필터 조건에 맞는 이미지만 표시합니다.           |
| `--no-trunc` |       |         | `sha256` 형식의 전체 이미지 ID를 표기합니다.    |
| `--quiet`    | `-q`  |         | 이미지 ID만 표기합니다.                         |



`Repository`를 명시한 경우, 해당 이름을 가진 이미지만 검색합니다. 

```cmd
$ docker images ubuntu
REPOSITORY   TAG       IMAGE ID       CREATED       SIZE
ubuntu       22.04     174c8c134b2a   2 weeks ago   77.9MB
ubuntu       latest    174c8c134b2a   2 weeks ago   77.9MB
```



### 이미지 삭제하기

> https://docs.docker.com/engine/reference/commandline/rmi/

```shell
docker rmi [OPTIONS] IMAGE [IMAGE...]

# Alias
docker image rm
```

`docker rmi`는 한 개 이상의 명시된 이미지들을 삭제하는 명령입니다. 



**주요 옵션**

| Option    | Short | Default | Description                                  |
| --------- | ----- | ------- | -------------------------------------------- |
| `--force` | `-f`  |         | 컨테이너가 존재하더라도 이미지를 삭제합니다. |



**주의사항**

- 동일한 `Image ID`를 가진 이미지가 2개 이상인 경우 `Image ID`를 이용하여 삭제할 수 없습니다. 
  -  `-f` 옵션을 사용한 경우 모든 이미지를 삭제합니다. 



### 모든 이미지 삭제하기

> https://docs.docker.com/engine/reference/commandline/image_prune/

```sh
docker image prune [OPTIONS]
```

`docker image prune`은 모든 `dangling` 이미지를 삭제하는 명령어 입니다.



#### `dangling` 이미지

- `Repository`가 `none`으로 표기되는 이미지입니다. 
- 일반적으로 다음과 같은 상황에서 발생합니다. 
  - 동일한 이름(`Name`&`Tag`)을 가진 다른 이미지를 생성한 경우
  - `Multi-Stage` 빌드중 생성된 중간 이미지 

```cmd
$ docker images -f dangling=true
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
<none>       <none>    5b95739d14ad   About a minute ago   1.02GB
```



**주요 옵션**

| Option     | Short | Default | Description                                                  |
| ---------- | ----- | ------- | ------------------------------------------------------------ |
| `--all`    | `-a`  |         | `dangling` 이미지를 포함한 사용하지 않는 모든 이미지를 삭제합니다. |
| `--filter` |       |         | Provide filter values (e.g. `until=<timestamp>`)             |



### 이미지 정보 조회

> https://docs.docker.com/engine/reference/commandline/inspect/

```shell
docker inspect [OPTIONS] NAME|ID [NAME|ID...]
```



#### [예시]  `ubuntu:22.04`이미지 정보 조회하기

```cmd
$ docker inspect ubuntu:22.04
[
    {
        "Id": "sha256:174c8c134b2a94b5bb0b37d9a2b6ba0663d82d23ebf62bd51f74a2fd457333da",
        "RepoTags": [
            "ubuntu:22.04",
            "ubuntu:latest"
        ],
        "RepoDigests": [
            "ubuntu@sha256:6042500cf4b44023ea1894effe7890666b0c5c7871ed83a97c36c76ae560bb9b"
        ],
        "Parent": "",
        "Comment": "",
        "Created": "2023-12-12T11:38:59.637410824Z",
        "Container": "24376bd380dd052e15c3e218153b3ba996b290ab24a579085cb3dd4d4b44e5ad",
        "ContainerConfig": {
            "Hostname": "24376bd380dd",
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
            "Image": "sha256:76cebcaf36a483db6a1f4eeebafda25bcfd25839a3c97101aa578d3120a8b0f8",
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
            "Image": "sha256:76cebcaf36a483db6a1f4eeebafda25bcfd25839a3c97101aa578d3120a8b0f8",
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
        "Size": 77850032,
        "VirtualSize": 77850032,
        "GraphDriver": {
            "Data": {
                "MergedDir": "/var/lib/docker/overlay2/08fca0476137c27031e67bdadf559dd8e4d5e5fcebeec0c5e0534024e880c322/merged",
                "UpperDir": "/var/lib/docker/overlay2/08fca0476137c27031e67bdadf559dd8e4d5e5fcebeec0c5e0534024e880c322/diff",
                "WorkDir": "/var/lib/docker/overlay2/08fca0476137c27031e67bdadf559dd8e4d5e5fcebeec0c5e0534024e880c322/work"
            },
            "Name": "overlay2"
        },
        "RootFS": {
            "Type": "layers",
            "Layers": [
                "sha256:a1360aae5271bbbf575b4057cb4158dbdfbcae76698189b55fb1039bc0207400"
            ]
        },
        "Metadata": {
            "LastTagTime": "0001-01-01T00:00:00Z"
        }
    }
]
```



---

### 연습 문제

#### [연습] ARM용 Ubuntu 다운로드하기

다음 명령어를 이용하여 `ubuntu:22.04`의 `linux/arm`용 이미지를 다운로드할 수 있습니다. 

```cmd
$ docker pull ubuntu:22.04 --platform linux/arm
22.04: Pulling from library/ubuntu
Digest: sha256:6042500cf4b44023ea1894effe7890666b0c5c7871ed83a97c36c76ae560bb9b
Status: Downloaded newer image for ubuntu:22.04
docker.io/library/ubuntu:22.04
```



다운로드한 이미지의 `arch`를 `inpect`를 이용하여 확인합니다. 

```cmd
$ docker inspect ubuntu:22.04 -f "{{ .Architecture }}"
arm
```



#### [연습] PostgreSQL 이미지의 레이어 갯수 확인하기

> 조건
>
> - digest: `sha256:a2282ad0db623c27f03bab803975c9e3942a24e974f07142d5d69b6b8eaaf9e2`

제시된 `Digets`를 이용하여 다음과 같이 이미지를 다운로드합니다. 

```cmd
$ docker pull postgres@sha256:a2282ad0db623c27f03bab803975c9e3942a24e974f07142d5d69b6b8eaaf9e2
```



다운로드한 이미지를 확인합니다. 

```cmd
$ docker images postgres
REPOSITORY   TAG             IMAGE ID       CREATED        SIZE
postgres     <none>          391a00ec7cac   13 days ago    425MB

# Digest도 함께 표시
$ docker images --digests postgres
```



다음과 같이 `Docker Id`를 이용하여 해당 이미지를 구성하고 있는 레이어들을 확인할 수 있습니다. 

```cmd
$ docker inspect 391a00ec7cac -f "{{range $v := .RootFS.Layers}}{{println $v}}{{end}}"
sha256:92770f546e065c4942829b1f0d7d1f02c2eb1e6acf0d1bc08ef0bf6be4972839
sha256:94ef9904d4df70d048f10800d60a22f6df9f45fe3c4d2a12e485761b7d695892
sha256:65c0efddc8b86104633e023acee9707dfa41249636f3690d315c01c987da56fe
sha256:7e28e769eedfd948a6c6d1dd70ee5a4b0d14b4576f38bb23e64ee30d9afd4d48
sha256:7fad9b4e65a17abc549695d9529b286d97c2453cb2e43e288dcc9a31f87b01b0
sha256:b693edccc3930a496794bd45d6e0fb1d0e7a2d00c3ec72130fe944696479e379
sha256:4f0b1281c6dcf2ceca4094d9730ea0a6184894ca020f94ff43ede84742b4da03
sha256:059a984b41a09df6a5309e7928fdb61772b9db821cae4dad464b4091b126d78b
sha256:b885153181c241ae470af66c8ee62619bb667a0579f9a93fcbecaf1482bbafc3
sha256:931d2dae8d071197900c6b9fd55756e9db68d383fd863f9c1dc40e4e9545f46e
sha256:983e1162f18556cbac001e4a4ae18766a64d157ad14bfef23bdab9a836be4b63
sha256:e59308f2f1f587291d3de81d2df9893e8ee395981e5f753dc370f3b8eda44b24
sha256:39db9e416d9745d9160895485c1d22543edbcffb365bc7ececd44ceab38a9c67
```





---

## 1.2. Docker 컨테이너 관리

### 컨테이너 실행하기

> https://docs.docker.com/engine/reference/commandline/run/

```shell
docker run [OPTIONS] IMAGE [COMMAND] [ARG...]
```



**주요 옵션**

| Option          | Short | Default | Description                              |
| --------------- | ----- | ------- | ---------------------------------------- |
| `--name`        |       |         | 컨테이너의 이름을 지정합니다.            |
| `--detach`      | `-d`  |         | 컨테이너를 백그라운드에서 실행합니다.    |
| `--env`         | `-e`  |         | 환경 변수를 설정합니다.                  |
| `--env-file`    |       |         | 환경 변수를 저장한 파일을 설정합니다.    |
| `--expose`      |       |         | 포트 또는 포트 범위를 노출합니다.        |
| `--publish`     | `-p`  |         | 컨테이너의 포트를 공개합니다.            |
| `--rm`          |       |         | 컨테이너가 종료되면 자동으로 삭제합니다. |
| `--interactive` | `-i`  |         | `STDIN`을 활성화합니다.                  |
| `--tty`         | `-t`  |         | `pseudo-TTY`를 할당합니다.               |
| `--volume`      | `-v`  |         | 볼륨을 설정합니다.                       |



#### Expose와 Publish 차이점

- 둘 다 컨테이너 외부 접근을 허용하기 위해 사용됩니다. 
- `Expose`는 동일한 `Network`를 사용하는 컨테이너에서만 접근이 가능합니다. 
- `Publish`는 `host`의 포트와 바인드하여, `host` 또는 외부에서도 접근이 가능합니다. 



**주의사항**

- `COMMAND`는 `Entrypoint`의 추가 인자로 활용됩니다. 



#### [예시] `Ubuntu`에서 명령어 실행하기

```cmd
$ docker run ubuntu:22.04 /bin/bash -c "whoami && date"
root
Sun Dec 17 07:06:20 UTC 2023
```



#### [예시] 80 포트를 이용하여 `nginx` 서비스하기

```cmd
$ docker run -d -p 80:80 nginx:latest

# Result
$ CURL localhost:80
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





### 컨테이너 목록 보기

> https://docs.docker.com/engine/reference/commandline/ps/

```shell
docker ps [OPTIONS]

# Include stopped container
docker ps -a
```



**주요 옵션**

| Option     | Short | Default | Description                                 |
| ---------- | ----- | ------- | ------------------------------------------- |
| `--all`    | `-a`  |         | 상태와 관계없이 모든 컨테이너를 표시합니다. |
| `--filter` | `-f`  |         | 지정된 조건에 맞는 컨테이너만 표시합니다.   |
| `--size`   | `-s`  |         | 전체 파일 사이즈도 표기합니다.              |



#### [예시] 종료된 컨테이너도 포함하여 목록 보기

```cmd
$ docker ps -a 
CONTAINER ID   IMAGE          COMMAND                   CREATED         STATUS                      PORTS     NAMES
803739f30047   nginx:latest   "/docker-entrypoint.…"   5 minutes ago   Exited (0) 17 seconds ago             stoic_diffie
```



#### [예시] 이름에 `server`가 들어간 컨테이너만 보기

```cmd
$ docker ps -a -f "name=server"
CONTAINER ID   IMAGE          COMMAND       CREATED      STATUS                     PORTS     NAMES
1e0d665497a7   ubuntu:22.04   "/bin/bash"   4 days ago   Exited (255) 3 hours ago             server3
939184ad2fe4   ubuntu:22.04   "/bin/bash"   4 days ago   Exited (255) 3 hours ago             server2
5c2817a20a00   ubuntu:22.04   "/bin/bash"   4 days ago   Exited (255) 3 hours ago             server
```



#### [예시] 컨테이너가 사용중인 볼륨(사이즈) 확인하기

```cmd
# 이미지를 실행한 직후
$ docker ps -s
CONTAINER ID   IMAGE          COMMAND       CREATED       STATUS       PORTS     NAMES                SIZE
a2306aa1de21   ubuntu:22.04   "/bin/bash"   3 hours ago   Up 3 hours             blissful_engelbart   0B (virtual 77.9MB)

# `apt-get update && apt-get upgrade` 실행 후
$ docker ps -s
CONTAINER ID   IMAGE          COMMAND       CREATED       STATUS       PORTS     NAMES                SIZE
a2306aa1de21   ubuntu:22.04   "/bin/bash"   3 hours ago   Up 3 hours             blissful_engelbart   46.8MB (virtual 125MB)
```





### 컨테이너 로그 확인하기

> https://docs.docker.com/engine/reference/commandline/logs/

```shell
docker logs [OPTIONS] <NAME_OR_ID>
```



**주요 옵션**

| Option         | Short | Default | Description                                                  |
| -------------- | ----- | ------- | ------------------------------------------------------------ |
| `--follow`     | `-f`  |         | 계속 로그를 표시합니다.                                      |
| `--tail`       | `-n`  | `all`   | 마지막 `n`개의 로그를 표시합니다.                            |
| `--timestamps` | `-t`  |         | `timestamp`를 표기합니다.                                    |
| `--since`      |       |         | 특정 시간 이후에 발생한 로그만 표시합니다. <br />- timestamp (e.g. `2013-01-02T13:23:37Z`) <br />- relative (e.g. `42m`, `10s`, ...) |
| `--until`      |       |         | 특정 시간 이전에 발생한 로그만 표시합니다. <br />- timestamp (e.g. `2013-01-02T13:23:37Z`) <br />- relative (e.g. `42m`, `10s`, ...) |



#### [예시] `nginx`의 마지막 로그 확인하기

`nginx` 컨테이너를 실행합니다. 

```cmd
$ docker run --rm -d -p 80:80 --name nginx nginx:latest
```



다음 명령어를 통해 `nginx` 컨테이너에 API를 호출합니다. 

```cmd
$ curl 127.0.0.1:80 
```



`nginx`의 마지막 1개 로그를 확인합니다. 

```cmd
$ docker logs --tail 1 nginx
172.17.0.1 - - [21/Dec/2023:15:47:34 +0000] "GET / HTTP/1.1" 200 615 "-" "curl/8.4.0" "-"
```





#### [예시] 특정 기간의 로그 확인하기

다음과 같이 매초마다 날짜를 출력하는 컨테이너를 실행합니다. 

```cmd
$ docker run --name clock -d busybox sh -c "while true; do $(echo date); sleep 1; done"
```



현재 시간으로부터 `20초` 전부터 `10초`전까지 발생한 로그를 다음과 같이 확인합니다. 

```cmd
$ docker logs -f --since 20s --until 10s clock && echo current time is %time%
Fri Dec 29 18:44:37 UTC 2023
Fri Dec 29 18:44:38 UTC 2023
Fri Dec 29 18:44:39 UTC 2023
Fri Dec 29 18:44:40 UTC 2023
Fri Dec 29 18:44:41 UTC 2023
Fri Dec 29 18:44:42 UTC 2023
Fri Dec 29 18:44:43 UTC 2023
Fri Dec 29 18:44:44 UTC 2023
Fri Dec 29 18:44:45 UTC 2023
Fri Dec 29 18:44:46 UTC 2023
current time is  3:44:57.61
```



### 컨테이너 접속하기

#### Attach

> https://docs.docker.com/engine/reference/commandline/attach/

```shell
docker attach [OPTIONS] CONTAINER
```

현재 실행중인 컨테이너의 터미널의 `STDIN`, `STDOUT`, `STDERR`에 접근합니다. 



**주의사항**

- `Ctrl+C`를 통해 프로세스를 종료하게 되면 컨테이너도 종료됩니다. 
- 컨테이너를 종료하지 않고 접속을 종료하기 위해서는 `Ctrl + P + Q`를 사용해야합니다. 



#### Exec

> https://docs.docker.com/engine/reference/commandline/exec/

```
docker exec -it [OPTIONS] <ID_OR_NAME> /bin/bash|sh|...
```

현재 실행중인 컨테이너에서 새로운 명령어를 실행합니다. 



**주의사항**

- 메인 프로세스가 실행되는 동안에만 명령어를 실행할 수 있습니다. 
  
- `exec`로 실행한 명령은 컨테이너 재실행(`start`)시 실행되지 않습니다. 
  
- 일부 컨테이너는 `bash`, `sh`와 같은 `shell`이 없을 수 있습니다. 
  - ex) `Scracth` 이미지, ... 

- `-it` 옵션이 없다면 원격으로 명령어를 실행하고 프로세스가 종료됩니다.  

```cmd
# `-it` 옵션이 없는 경우
$ docker exec clock sh -c "echo $$"
3031
$ docker exec clock sh -c "echo $$"
3039
$ docker exec clock sh -c "echo $$"
3047

# `-it` 옵션을 사용한 경우
$ docker exec -it clock sh
root@963c4fdec415:/ # echo $$
3221
root@963c4fdec415:/ # echo $$
3221
```



#### `Attach`와 `Exec` 차이점

|                | Attach                                 | Exec                      |
| -------------- | -------------------------------------- | ------------------------- |
| 접근 터미널    | 컨테이너의 `STDIN`, `STDOUT`, `STDERR` | 새로운 process의 Terminal |
| 접속 종료 방법 | `Ctrl + p + q`                         | `Ctrl + c`                |
| PID            | 1 (Main Process)                       | != 1                      |



#### [예시] `Attach`와 `Exec`를 각각 사용하여 `PID` 출력하기

다음 명령어를 사용하여 `ubuntu` 컨테이너를 실행합니다. 

```cmd
$ docker run --rm -it -d --name server ubuntu:22.04
```



**Attach 사용한 경우**

`PID` 출력 시 메인 프로세스인 `1`이 출력됩니다. 

```cmd
$ docker attach server
root@89834e856287:/# ps -aux
root@89834e856287:/# echo $$
1
```



**Exec 사용한 경우**

`PID` 출력 시 `1`이 아닌 다른 값이 출력되는 것을 볼 수 있습니다. 

```cmd
$ docker exec -it server /bin/bash
root@89834e856287:/# echo $$
21
```





### 컨테이너 명령어 실행하기

> https://docs.docker.com/engine/reference/commandline/exec/

```shell
docker exec [OPTIONS] CONTAINER COMMAND [ARG...]
```



**주의사항**

- 명령어는 `Working Directory`를 기준으로 실행되므로 파일 사용 시 주의가 필요합니다. 

```cmd
$ docker exec clock sh -c "pwd"
/

$ docker exec --workdir /bin clock sh -c "pwd"
/bin
```



### 컨테이너 종료 & 시작

> https://docs.docker.com/engine/reference/commandline/stop/
>
> https://docs.docker.com/engine/reference/commandline/start/

```shell
# 종료
docker stop <NAME_OR_ID>
# 시작
docker start <NAME_OR_ID>
```



**주의사항**

- `Dockerfile`을 통해 실행되지 않은 프로세스들은 종료 후 재시작할 경우 살아나지 않습니다. 

```cmd
$ docker run -d --name server ubuntu
$ docker exec -d server /bin/bash -c "while true; do $(echo date); sleep 1; done"
$ docker exec server ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 16:57 pts/0    00:00:00 /bin/bash
root         9     0  0 17:05 pts/1    00:00:00 /bin/sh
root       225     0  0 17:08 ?        00:00:00 /bin/bash -c while true; do $(echo date); sleep 1; done

$ docker stop server
$ docker start server
$ docker exec server ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 17:11 pts/0    00:00:00 /bin/bash
root         9     0  0 17:11 ?        00:00:00 ps -ef
```



### 컨테이너 중지 & 재실행

> https://docs.docker.com/engine/reference/commandline/pause/
>
> https://docs.docker.com/engine/reference/commandline/unpause/

```shell
# 중지
docker pause <NAME_OR_ID>
# 재실행
docker unpause <NAME_OR_ID>
```



**주의사항**

- 재실행하는 경우 정지되어 있던 `모든` 프로세스가 재실행됩니다. 

```cmd
$ docker run -d --name server ubuntu
$ docker exec -d server /bin/bash -c "while true; do $(echo date); sleep 1; done"
$ docker exec server ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 16:57 pts/0    00:00:00 /bin/bash
root         9     0  0 17:05 pts/1    00:00:00 /bin/sh
root       225     0  0 17:08 ?        00:00:00 /bin/bash -c while true; do $(echo date); sleep 1; done

$ docker pause server
$ docker unpause server
$ docker exec server ps -ef
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 16:57 pts/0    00:00:00 /bin/bash
root         9     0  0 17:05 pts/1    00:00:00 /bin/sh
root       225     0  0 17:08 ?        00:00:00 /bin/bash -c while true; do $(echo date); sleep 1; done
```



### 컨테이너 삭제

> https://docs.docker.com/engine/reference/commandline/rm/

```
docker rm [OPTIONS] CONTAINER [CONTAINER...]
```



**주요 옵션**

| Option      | Short | Default | Description                                                 |
| ----------- | ----- | ------- | ----------------------------------------------------------- |
| `--force`   | `-f`  |         | `SIGKILL` 시그널을 사용하여 실행중인 컨테이너를 삭제합니다. |
| `--volumes` | `-v`  |         | 컨테이너의 `anonymous volumes`도 함께 삭제합니다.           |



**Tip**

종료된 모든 컨테이너를 삭제하려면 다음과 같이 사용하면 됩니다.  

```cmd
$ docker rm $(docker ps -a -q -f status=exited)
```





### 컨테이너 리소스 사용량 조회

> https://docs.docker.com/engine/reference/commandline/stats/

```shell
docker stats [OPTIONS] [CONTAINER...]
```



**주요 옵션**

| Option        | Short | Default | Description                              |
| ------------- | ----- | ------- | ---------------------------------------- |
| `--all`       | `-a`  |         | 모든 컨테이너를 표시합니다.              |
| `--no-stream` |       |         | 결과를 지속적으로 업데이트하지 않습니다. |



#### [예시] `ubuntu` 컨테이너 리소스 사용량 조회하기

`ubuntu:22.04`를 이용하여 `server`를 실행합니다. 

```cmd
$ docker run --rm -it -d --name server ubuntu:22.04
```



`server`가 사용중인 리소스는 다음과 같이 확인할 수 있습니다. 

```cmd
$ docker stats --no-stream server
CONTAINER ID   NAME      CPU %     MEM USAGE / LIMIT   MEM %     NET I/O     BLOCK I/O   PIDS
5c2817a20a00   server    0.00%     896KiB / 15.56GiB   0.01%     586B / 0B   0B / 0B     1
```





---

### 연습 문제

#### [연습] Ubuntu 실행 및 Package 업데이트

> 조건
>
> - Ubuntu 이미지 Tag = 22.04
> - Container 이름 = server
> - 업데이트 이후에도 컨테이너는 실행중이어야 합니다. 



**Ubuntu 실행하기**

```cmd
$ docker run -it -d --name server ubuntu:22.04
89834e856287c0875da2a9dddc5aa2905a8ad601f9348e014855badc9b345ccc
```



**Package 업데이트하기**

**방법 1 - Exec 사용하기**

- Interactive 모드

```cmd
$ docker exec -it server /bin/bash
root@89834e856287:/# apt-get update && apt-get ugrade
...
```

- Non-Interactive

```cmd
# Single Command
$ docker exec server apt-get update
Get:1 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
...
$ docker exec server apt-get upgrade
Reading package lists...
Building dependency tree...
...

# Or Multiple Command
$ docker exec server /bin/bash -c "apt-get update && apt-get upgrade"
Get:1 http://security.ubuntu.com/ubuntu jammy-security InRelease [110 kB]
...
Reading package lists...
Building dependency tree...
...
```



**방법 2 - Attach 사용하기**

> Ubuntu 이미지의 경우 기본적으로 `/bin/bash`를 실행하므로 `attach`를 사용하여도 무방합니다. 

```cmd
$ docker attach server
root@89834e856287:/# apt-get update && apt-get ugrade
...
```



**방법 3 - Container 실행시 Command 입력하기**

```cmd
$ docker run -it -d --name server ubuntu:22.04 /bin/bash -c "apt-get update && apt-get upgrade && /bin/bash"
Hit:1 http://security.ubuntu.com/ubuntu jammy-security InRelease
Hit:2 http://archive.ubuntu.com/ubuntu jammy InRelease
Hit:3 http://archive.ubuntu.com/ubuntu jammy-updates InRelease
Hit:4 http://archive.ubuntu.com/ubuntu jammy-backports InRelease
Reading package lists...
Reading package lists...
Building dependency tree...
Reading state information...
Calculating upgrade...
0 upgraded, 0 newly installed, 0 to remove and 0 not upgraded.
Done
```



#### [실습] PostgreSQL DB에 Table 생성하기

>조건
>
>- `postgres:16.1-bullseye` 이미지를 사용합니다.
>- 컨테이너의 이름은 `psql_db`로 설정합니다. 
>- Container 생성시 다음 환경변수가 설정되어야 합니다. 
>   - POSTGRES_PASSWORD

1. `PostgreSQL` DB 컨테이너를 실행하세요
2. `exec`를 사용하여 컨테이너 터미널에 접근하세요
3. 터미널에서 `psql -U postgres`를 입력하여 `PostgreSQL`에 접속하세요
4. 다음 `Query`를 실행하세요

```sql
CREATE TABLE IF NOT EXISTS cloud_wave (
    id SERIAL PRIMARY KEY,
    timestamp timestamp
);
```

5. `\dt`를 실행하여 다음과 같이 생성한 테이블이 보이는지 확인하세요

```
postgres=# \dt
               List of relations
 Schema |        Name        | Type  |  Owner
--------+--------------------+-------+----------
 public |     cloud_wave     | table | postgres
(1 row)
```



---

## 1.3. Docker 이미지 생성하기

### 이미지 생성하기

> https://docs.docker.com/engine/reference/commandline/build/

```cmd
# docker build [OPTIONS] PATH | URL | - [-f <PATH_TO_FILE>]
$ docker build . [-f <PATH_TO_FILE>]

# or
$ docker buildx build [OPTIONS] PATH | URL | - [-f <PATH_TO_FILE>]
```



**주요 옵션**

| Option        | Short | Default | Description                                        |
| ------------- | ----- | ------- | -------------------------------------------------- |
| `--build-arg` |       |         | `ARG`를 설정합니다.                                |
| `--file`      | `-f`  |         | `Dockerfile`의 경로를 지정합니다.                  |
| `--label`     |       |         | 라벨을 추가합니다.                                 |
| `--no-cache`  |       |         | 이미지 빌드시 캐시를 사용하지 않습니다.            |
| `--platform`  |       |         | `platform`을 지정합니다.                           |
| `--pull`      |       |         | 관련된 이미지를 저장 유무에 관계없이 `pull`합니다. |
| `--tag`       | `-t`  |         | 이름과 `Tag`를 설정합니다.                         |



### Dockerfile

| Instruction                                                  | Description                               |
| :----------------------------------------------------------- | :---------------------------------------- |
| [`FROM`](https://docs.docker.com/engine/reference/builder/#from) | 사용할 `Base` 이미지를 설정합니다.        |
| [`ARG`](https://docs.docker.com/engine/reference/builder/#arg) | `build`에 사용할 변수를 설정합니다.       |
| [`ENV`](https://docs.docker.com/engine/reference/builder/#env) | 환경 변수를 설정합니다.                   |
| [`ADD`](https://docs.docker.com/engine/reference/builder/#add) | 파일 또는 폴더(`directory`)를 추가합니다. |
| [`COPY`](https://docs.docker.com/engine/reference/builder/#copy) | 파일 또는 폴더(`directory`)를 복사합니다. |
| [`LABEL`](https://docs.docker.com/engine/reference/builder/#label) | 라벨을 추가합니다.                        |
| [`EXPOSE`](https://docs.docker.com/engine/reference/builder/#expose) | 포트를 외부에 노출합니다.                 |
| [`USER`](https://docs.docker.com/engine/reference/builder/#user) | `User`와 `Group`을 설정합니다.            |
| [`WORKDIR`](https://docs.docker.com/engine/reference/builder/#workdir) | `Working Directory`를 변경합니다.         |
| [`RUN`](https://docs.docker.com/engine/reference/builder/#run) | 명령어를 실행합니다.                      |
| [`CMD`](https://docs.docker.com/engine/reference/builder/#cmd) | 기본 명령어를 정의합니다.                 |
| [`ENTRYPOINT`](https://docs.docker.com/engine/reference/builder/#entrypoint) | 필수로 실행할 명령어를 정의합니다.        |



#### 주의사항

- `-f`를 통해 사용할 `Dockerfile`을 명시하지 않는 경우, 파일 이름이 `Dockerfile`인 파일이 사용됩니다. 
- `Dockerfile`내 모든 상대 경로들은 `build`시 입력된 `Path`를 기준으로 계산됩니다. 
- `cache`를 사용할 경우, `apt-get`을 통해 설치한 패키지 버젼이 최신이 아닐 수 있습니다. 



#### 많이 사용하는 `base` 이미지

##### - Scratch 이미지

> https://hub.docker.com/_/scratch

`binary`를 실행하기 위한 최소한의 이미지입니다.
`Dockerfile`에서 사용했을 시, 별도의 `layer`가 추가되지 않습니다. 



##### - Alpine linux

> https://hub.docker.com/_/alpine

`Alpine` 리눅스는 용량이 5Mb 이하로 매우 가벼우며 보안성이 뛰어납니다.
많은 이미지들이 `alpine`을 기반으로한 경량화된 이미지를 함께 제공합니다. 





---

### 연습문제

#### [연습] Go 서버 이미지 제작하기

> 다음 3개 이미지를 기반으로 이미지를 빌드합니다. 
>
> - Ubuntu
> - Alpine
> - Scratch

전체적인 폴더 구조는 다음과 같습니다. 

```
.
├── src
│   ├── go.mod
│   └── main.go
└── Dockerfile
```

**go.mod**

```go
module example/hello
go 1.19
```

**main.go**

```go
package main

import (
    "io"
    "net/http"
    "log"
)

func HelloServer(w http.ResponseWriter, req *http.Request) {
    io.WriteString(w, "Hello, Worlds!\n")
}

func main() {
    http.HandleFunc("/", HelloServer)
    log.Fatal(http.ListenAndServe(":80", nil))
}

```

##### Debian (bullseye)을 이용한 Dockerfile

```dockerfile
FROM golang:1.19-bullseye

WORKDIR /app

COPY src ./

RUN CGO_ENABLED=0 go build -o main

CMD ["/app/main"]
```

##### Alpine을 이용한 Dockerfile

```dockerfile
FROM golang:1.19-alpine

WORKDIR /app

COPY src ./

RUN CGO_ENABLED=0 go build -o main

CMD ["/app/main"]
```

##### Scratch를 이용한 Dockerfile

```dockerfile
FROM golang:1.19-alpine as build
WORKDIR /app
COPY src ./
RUN CGO_ENABLED=0 go build -o main

FROM scratch as release
COPY --from=build /app/main /app/
WORKDIR /app
CMD ["/app/main"]
```



`Dockerfile`들을 이용하여 이미지를 생성합니다. 

``` cmd
$ docker build -t go:<TAG_NAME> .
```



생성된 이미지는 다음과 같이 실행하여 확인해볼 수 있습니다. 

```cmd
$ docker run -d -p 80:80 go:scratch
$ curl localhost:80
Hello, Worlds!
```



각 이미지별 크기는 다음과 같이 확인할 수 있습니다. 

```cmd
$ docker build -t go:<TAG_NAME> .
$ docker images go
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
go           debian    7b5f70dd7edf   About a minute ago   1.02GB
go           alpine    ac6365a46643   6 minutes ago        378MB
go           scratch   97bcb5fd82c5   26 seconds ago       6.47MB
```



#### [연습] 실습용 `ubuntu` 이미지 제작하기

파일 구조는 다음과 같습니다. 

```tex
.
├── install_docker_engine.sh
└── Dockerfile
```



**install_docker_engine.sh**

```shell
apt-get update && apt-get upgrade
apt-get install -y ca-certificates curl gnupg
install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | gpg --dearmor -o /etc/apt/keyrings/docker.gpg
chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  tee /etc/apt/sources.list.d/docker.list > /dev/null
apt-get update && apt-get upgrade
```

**Dockerfile**

```dockerfile
FROM ubuntu:22.04

RUN mkdir -p /scripts
COPY install_docker_engine.sh /scripts

WORKDIR /scripts

RUN chmod +x install_docker_engine.sh
RUN ./install_docker_engine.sh

RUN apt-get install -y docker-ce docker-ce-cli
```



다음 명령어를 통해서 `cloudwave:base.v1` 이미지를 빌드합니다. 

```cmd
$ docker build -t cloudwave:base.v1 .
```



정상적으로 `docker`가 설치되었다면 다음과 같은 결과를 얻을 수 있습니다. 

```cmd
$ docker run cloudwave:base.v1 docker version
Client: Docker Engine - Community
 Version:           24.0.7
 API version:       1.43
 Go version:        go1.20.10
 Git commit:        afdd53b
 Built:             Thu Oct 26 09:07:41 2023
 OS/Arch:           linux/amd64
 Context:           default
Cannot connect to the Docker daemon at unix:///var/run/docker.sock. Is the docker daemon running?
```



#### [실습] `Arg`를 이용하여 `base`이미지 변경하기

1. `Go 서버 이미지 제작하기`에서 사용한 `Dockerfile`을 수정합니다.
   1. `ARG`이름은 `OS`로 설정합니다.
   2. `ARG`를 이용하여 `Base 이미지`를 입력받아야 합니다.  
   3. `ARG`의 값을 환경변수(`BASE`)에 저장합니다.  
2. `--build-args` 옵션을 사용하여 `ubuntu:22.04`, `alpine:latest`를 기반으로한 이미지를 생성합니다. 





---

## 1.4. Docker 이미지 업로드 하기

### 이미지 태그 변경하기

> https://docs.docker.com/engine/reference/commandline/tag/

```shell
docker tag SOURCE_IMAGE[:TAG] TARGET_IMAGE[:TAG]
```



#### [예시] 이미지 이름 변경하기

```cmd
$ docker tag ubuntu:22.04 my-ubuntu:v1
$ docker images
REPOSITORY                         TAG             IMAGE ID       CREATED         SIZE
my-ubnutu                          v1              174c8c134b2a   9 days ago      77.9MB
ubuntu                             22.04           174c8c134b2a   9 days ago      77.9MB
```



### 이미지 업로드하기

```
docker push [OPTIONS] NAME[:TAG]
```

**주요 옵션**

| Option       | Short | Default | Description                                 |
| ------------ | ----- | ------- | ------------------------------------------- |
| `--all-tags` | `-a`  |         | Push all tags of an image to the repository |



**주의사항**

- `Docker hub`가 아닌 `ECR` 또는 자체 구축한 `Hub`와 같이 별도의 `registry`에 업로드하려는 경우, 
  이름은 `full image name` 형식으로 작성되어야 합니다. 
- `Push`전 `docker login`을 통해서 사용할 `registry`에 인증해야 합니다.



#### Full image name 포맷

```tex
[HOST[:PORT_NUMBER]/]PATH
# namespace를 명시하는 경우
[HOST[:PORT_NUMBER]/][NAMESPACE/]REPOSITORY
```



`full image name`의 예시는 다음과 같습니다.

```tex
# Example - ECR
<aws_account_id>.dkr.ecr.ap-northeast-2.amazonaws.com/cloudwave:v1

# Example - private registry
my.registry.com:5000/cloudwave/spring:v1
```



| 컴포넌트    | 설명                                                         |
| ----------- | ------------------------------------------------------------ |
| HOST        | `registry`의 HOST을 의미합니다.                              |
| PORT_NUMBER | `registry` 서버의 포트를 의미합니다.                         |
| NAMESPACE   | 논리적 구분 단위입니다.<br />값이 설정되지 않은 경우, `library`로 설정됩니다. |
| REPOSITORY  | 이미지의 이름을 의미합니다.                                  |



### `Registry` 로그인하기

```shell
docker login [OPTIONS] [SERVER[:PORT]]
```



**주요 옵션**

| Option             | Short | Default | Description                             |
| ------------------ | ----- | ------- | --------------------------------------- |
| `--password`       | `-p`  |         | password                                |
| `--password-stdin` |       |         | `STDIN`을 통해 password를 입력받습니다. |
| `--username`       | `-u`  |         | Username                                |



---

### 연습 문제

#### [연습] Docker hub에 이미지 업로드하기

`Docker hub`에 로그인합니다. 

```cmd
$ docker login -u <USERNAME>
Login with your Docker ID to push and pull images from Docker Hub. If you don't have a Docker ID, head over to https://hub.docker.com to create one.
Username: <USERNAME>
Password: 
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```



`ubuntu:22.04` 이미지의 이름을 `<USERNAME>/cloudwave:ubuntu.22.04`로 변경합니다. 

```cmd
$ docker tag ubuntu:22.04 <USERNAME>/cloudwave:ubuntu.22.04
```

이미지를 `Push`합니다.

```cmd
$ docker push <USERNAME>/cloudwave:ubuntu.22.04
```



업로드한 이미지는 `search` 명령어를 통해 다음과 같이 확인할 수 있습니다. 

```cmd
$ docker search <USERNAME>/cloudwave
NAME                   DESCRIPTION   STARS     OFFICIAL   AUTOMATED
<USERNAME>/cloudwave                  0
```



#### [연습] ECR에 이미지 업로드하기

> - 연습하기에 앞서 AWS IAM 및 ECR 생성이 필요합니다.
>
> - `1.3`에서 제작한 `ubuntu`이미지(`cloudwave:base.v1`)를 사용합니다. 

다음 명령어를 통해 `Host`에 설치된 Docker Daemon을 컨테이너에 바인드합니다. 

> Volume은 다음 챕터에서 자세히 다룰 예정입니다.

```cmd
# Windows
$ docker run --rm -it -d -v "//var/run/docker.sock://var/run/docker.sock" --name practice cloudwave:docker.v1 /bin/bash
```

컨테이너에서 `docker images` 명령을 실행할 시, 다음과 같이 `host`와 동일한 이미지 목록을 가져오는 것을 확인할 수 있습니다. 

```cmd
# Container
$ docker images
REPOSITORY                         TAG             IMAGE ID       CREATED         SIZE
busybox                            latest          9211bbaa0dbd   3 days ago      4.26MB
...

# Host Machine - Windows
$ docker images
REPOSITORY                         TAG             IMAGE ID       CREATED         SIZE
busybox                            latest          9211bbaa0dbd   3 days ago      4.26MB
...
```



`practice` 컨테이너에 접속하여 `aws-cli`를 설치합니다. 

```cmd
$ docker run -it -d cloudwave:base.v1
$ docker exec -it practice /bin/bash
root@6ed755e5c292:/scripts# apt install -y awscli
...
Geographic area: 6
...
Time zone: 69
```



`aws-cli`가 설치되었다면 다음 명령어를 통해 버전을 확인할 수 있습니다. 

```cmd
$ aws --version
aws-cli/1.22.34 Python/3.10.12 Linux/5.15.133.1-microsoft-standard-WSL2 botocore/1.23.34
```



사용할 AWS 계정을 설정합니다. 

- 방법 1 - 환경 변수

```cmd
$ export AWS_ACCESS_KEY_ID=<ACCESS_KEY>
$ export AWS_SECRET_ACCESS_KEY=<SECRET_KEY>
$ export AWS_DEFAULT_REGION=ap-northeast-2
```

- 방법 2 - `aws configure`

```cmd
$ aws configure
AWS Access Key ID [None]: <ACCESS_KEY>
AWS Secret Access Key [None]: <SecretKey>
Default region name [None]: ap-northeast-2
Default output format [None]: json
```



정상적으로 AWS 계정을 설정하였다면, 다음 명령어를 통해서 `ECR private registry`에 로그인할 수 있습니다. 

```cmd
$ aws ecr get-login-password --region ap-northeast-2 | docker login --username AWS --password-stdin <AWS_ACCOUNT_ID>.dkr.ecr.ap-northeast-2.amazonaws.com

WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
```



업로드할 이미지의 `Tag`를 다음과 같이 설정합니다.

```cmd
$ docker tag cloudwave:base.v1 <AWS_ACCOUNT_ID>.dkr.ecr.ap-northeast-2.amazonaws.com/cloudwave:base.v1
```



만약 ECR Repository 정보(Host)를 모르겠다면,  `aws ecr describe-repositories` 명령어를 통해서 확인할 수 있습니다. 

```cmd
# Example
$ aws ecr describe-repositories
{
    "repositories": [
        {
            "repositoryArn": "arn:aws:ecr:ap-northeast-2:300274840224:repository/cloudwave",
            "registryId": "300274840224",
            "repositoryName": "cloudwave",
            "repositoryUri": "300274840224.dkr.ecr.ap-northeast-2.amazonaws.com/cloudwave",
            "createdAt": 1703210889.0,
            "imageTagMutability": "MUTABLE",
            "imageScanningConfiguration": {
                "scanOnPush": false
            },
            "encryptionConfiguration": {
                "encryptionType": "AES256"
            }
        }
    ]
}
```

`ECR`에 이미지를 업로드합니다. 

> Docker Hub Public Registry와 다르게 `repository`이름이 일치하지 않는 경우 에러가 발생합니다. 

```
docker push <AWS_ACCOUNT_ID>.dkr.ecr.ap-northeast-2.amazonaws.com/cloudwave:base.v1

4293f97defd8: Pushed 
base.v1: digest: sha256:e61678f5601ef33803093805be4ae474105d6f6e86b8d268e8688f5c1c9bad35 size: 529
```



업로드한 이미지는 `AWS Console` 또는 `aws cli`를 통해서 확인할 수 있습니다. 

> https://docs.aws.amazon.com/cli/latest/reference/ecr/

```cmd
$ aws ecr list-images --repository-name cloudwave
{
    "imageIds": [
        {
            "imageDigest": "sha256:e61678f5601ef33803093805be4ae474105d6f6e86b8d268e8688f5c1c9bad35",
            "imageTag": "base.v1"
        }
    ]
}
```



---

## 1.5. Docker 볼륨

```
Usage:  docker volume COMMAND

Manage volumes

Commands:
  create      Create a volume
  inspect     Display detailed information on one or more volumes
  ls          List volumes
  prune       Remove all unused local volumes
  rm          Remove one or more volumes
```

- 컨테이너에서 사용 및 관리하는 저장 공간입니다.
- 컨테이너에 종속되어 있지 않으므로, 컨테이너의 라이프 사이클을 따라가지 않습니다. 



### 볼륨 생성

> https://docs.docker.com/engine/reference/commandline/volume_create/

```shell
docker volume create [OPTIONS] [VOLUME]
```



**주요 옵션**

| Option   | Short | Default | Description        |
| -------- | ----- | ------- | ------------------ |
| `--name` |       |         | 이름을 지정합니다. |



### 볼륨 목록 보기

> https://docs.docker.com/engine/reference/commandline/volume_ls/

```shell
docker volume ls [OPTIONS]
```



**주요 옵션**

| Option     | Short | Default | Description                           |
| ---------- | ----- | ------- | ------------------------------------- |
| `--filter` | `-f`  |         | 지정된 조건에 맞는 볼륨만 표시합니다. |
| `--quiet`  | `-q`  |         | 볼륨 이름만 표시합니다.               |



#### [예시] 사용하지 않는 볼륨 목록 보기

```cmd
$ docker volume ls -f "dangling=true"  # or "dangling=1"
DRIVER    VOLUME NAME
local     fecea4ffd139a0d85b735f9ea8fe247bfabc9227df31ac9ff1d8e66f6b77d229
```



### 볼륨 정보 보기

> https://docs.docker.com/engine/reference/commandline/volume_inspect/

```shell
docker volume inspect [OPTIONS] VOLUME [VOLUME...]
```



#### [예시] 볼륨 생성 일자 확인하기

```cmd
$ docker volume inspect --format "{{ .CreatedAt }}" <VOLUME_NAME>
2023-12-22T01:23:07Z
```



### 볼륨 삭제

> https://docs.docker.com/engine/reference/commandline/volume_rm/

```shell
docker volume rm [OPTIONS] VOLUME [VOLUME...]
```

`volume rm`은 한 개 이상의 명시된 볼륨들을 삭제하는 명령입니다. 



### 모든 볼륨 삭제

> https://docs.docker.com/engine/reference/commandline/volume_prune/

```sh
docker volume prune [OPTIONS]
```

`volume prune`은 사용하지 않는 모든 `anonymous ` 볼륨을 삭제하는 명령어 입니다.



**주요 옵션**

| Option     | Short | Default | Description                           |
| ---------- | ----- | ------- | ------------------------------------- |
| `--all`    | `-a`  |         | 사용하지 않는 모든 볼륨을 삭제합니다. |
| `--filter` |       |         | 필터링 조건을 설정합니다.             |



---

### Practice

#### [연습] Volume에 DB 데이터 저장하기

>- `postgres:16.1-bullseye` 이미지를 사용합니다.
>- 컨테이너의 이름은 `psql_db`로 설정합니다. 
>- 컨테이너 생성시 다음 환경변수가 설정되어야 합니다. 
>  - POSTGRES_PASSWORD
>
>- 생성한 Volume은 컨테이너의 `/var/lib/postgresql/data`에 마운트합니다. 



`DB`에서 사용할 볼륨을 다음과 같이 생성합니다. 

```cmd
$ docker volume create db_data
db_data
```



생성된 볼륨은 `docker volume list` 명령어를 통해서 확인할 수 있습니다. 

```cmd
$ docker volume list
DRIVER    VOLUME NAME
local     db_data
```



`PostgreSQL`의 데이터는 `/var/lib/postgresql/data`에 저장됩니다. 
그러므로, `db_data` 볼륨을 해당 디렉토리에 마운트합니다. 

```cmd
$ docker run --rm -d --name psql_db -v db_data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=mysecretpassword postgres:16.1-bullseye
```



컨테이너 터미널에 접근하여 `psql`을 이용해 DB에 접속합니다. 

```cmd
$ docker exec -it psql_db /bin/bash
root@7523c983f729:/# psql -U postgres
psql (16.1 (Debian 16.1-1.pgdg110+1))
Type "help" for help.
```



SQL을 이용하여 테이블을 생성합니다. 

```sql
# SQL
CREATE TABLE IF NOT EXISTS cloud_wave (
    id SERIAL PRIMARY KEY,
    timestamp timestamp
);
```



생성한 테이블을 `\dt`를 이용하여 확인합니다. 

```cmd
postgres=# \dt
               List of relations
 Schema |    Name    | Type  |  Owner
--------+------------+-------+----------
 public | cloud_wave | table | postgres
(1 row)
```



DB를 종료합니다. 

```cmd
$ docker stop psql_db
psql_db

# 컨테이너 이름에 `psql_db`가 포함된 컨테이너 목록을 보여줍니다. 
$ docker ps -a -f name=psql_db
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```



컨테이너를 재생성 합니다.

```cmd
$ docker run --rm -d --name psql_db -v db_data:/var/lib/postgresql/data -e POSTGRES_PASSWORD=mysecretpassword postgres:16.1-bullseye
```



컨테이너 내부에서 DB 테이블이 남아있는 것을 확인합니다. 

```cmd
$ docker exec -it psql_db /bin/bash
root@3f19c9efb04b:/# psql -U postgres
psql (16.1 (Debian 16.1-1.pgdg110+1))
Type "help" for help.

# Table 목록
postgres=# \dt
               List of relations
 Schema |    Name    | Type  |  Owner
--------+------------+-------+----------
 public | cloud_wave | table | postgres
(1 row)
```



#### [연습] `bind mount`를 사용하여 소스코드 변경하기

> - Host의 `./app`는 컨테이너의 `/code/app`와 바인드 되어야 합니다. 
>- 이미지 이름은 `was`로 설정합니다. 

폴더 구조는 다음과 같습니다. 

```
.
├── app
│   ├── __init__.py
│   └── main.py
├── Dockerfile
└── requirements.txt
```



**main.py**

```python
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}
```



**Dockerfile**

```
FROM python:3.9

WORKDIR /code

COPY ./requirements.txt /code/requirements.txt

RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

CMD ["uvicorn", "app.main:app", "--host", "0.0.0.0", "--port", "80", "--reload"]
```



`Dockerfile`이 위치한 폴더에서 다음 명령어를 실행하여 이미지를 빌드합니다.

```cmd
$ docker build -t was:fast.1 .
```



다음 명령어를 통해서 `FastAPI` 서버를 실행합니다. 

```cmd
$ docker run --name bind -p 80:80 -v <FOLDER_PATH>\app:/code/app was:fast.1
INFO:     Will watch for changes in these directories: ['/code']
INFO:     Uvicorn running on http://0.0.0.0:80 (Press CTRL+C to quit)
INFO:     Started reloader process [1] using statreload
INFO:     Started server process [8]
INFO:     Waiting for application startup.
INFO:     Application startup complete.
```



`http://localhost/redoc`에 접속하여 API 목록을 확인합니다. 

![fastapi_redoc_1](docker_practice.assets/fastapi_redoc_1.PNG)



**File 업데이트 하기**

`main.py`를 다음과 같이 수정합니다.

```python
import socket
from typing import Union

from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def read_root():
    return {"Hello": "World"}


@app.get("/hostname")
def get_hostname():
    return {"name": socket.gethostname()}
```



`http://localhost/redoc`에 접속하면 `get_hostname` API가 추가된 것을 확인할 수 있습니다. 

![fastapi_redoc_2](docker_practice.assets/fastapi_redoc_2.PNG)



---

## 1.6. Docker 네트워크

```
Usage:  docker network COMMAND

Manage networks

Commands:
  connect     Connect a container to a network
  create      Create a network
  disconnect  Disconnect a container from a network
  inspect     Display detailed information on one or more networks
  ls          List networks
  prune       Remove all unused networks
  rm          Remove one or more networks
```



### 네트워크 생성

> https://docs.docker.com/engine/reference/commandline/network_create/

```sh
docker network create [OPTIONS] NETWORK
```



**주요 옵션**

| Option     | Short | Default  | Description                                |
| ---------- | ----- | -------- | ------------------------------------------ |
| `--driver` | `-d`  | `bridge` | 네트워크에서 사용할 드라이버를 선언합니다. |
| `--label`  |       |          | 네트워크의 라벨을 설정합니다.              |



#### 네트워크 드라이버

- `bridge`

동일한 `Host` 컴퓨터 내에서 컨테이너끼리 통신하기 위해 사용합니다. 

- `host` 

컨테이너가 `Host`와 동일한 네트워크를 사용합니다.

- `none` 

`Host`와 완벽히 격리된 네트워크입니다. 



**주의사항**

- `host` 네트워크는 인스턴스 별로 한 개만 생성할 수 있습니다. 



#### [예시] `bridge` 네트워크 생성하기

```cmd
$ docker network create -d bridge private
```





### 네트워크 목록 보기

>https://docs.docker.com/engine/reference/commandline/network_ls/

```sh
docker network ls [OPTIONS]

Aliases:
  docker network ls, docker network list
```



**주요 옵션**

| Option     | Short | Default | Description                               |
| ---------- | ----- | ------- | ----------------------------------------- |
| `--filter` | `-f`  |         | 지정된 조건에 맞는 네트워크만 표시합니다. |
| `--quiet`  | `-q`  |         | 네트워크 ID만 표기합니다.                 |



#### [예시] `host` 네트워크만 조회하기

```cmd
$ docker network ls -f driver=host
NETWORK ID     NAME      DRIVER    SCOPE
b107e82764b5   host      host      local
```



### 네트워크 정보 보기

> https://docs.docker.com/engine/reference/commandline/network_inspect/

```sh
docker network inspect [OPTIONS] NETWORK [NETWORK...]
```



#### [예시] `private` 네트워크 조회하기

```cmd
$ docker network inspect private
[
    {
        "Name": "private",
        "Id": "04f294f613b357014305f09af1ae31fded48ae663ad451ed65f6c4043045fb9d",
        "Created": "2023-12-23T05:29:53.303432435Z",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "172.19.0.0/16",
                    "Gateway": "172.19.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```



### 네트워크 연결

> https://docs.docker.com/engine/reference/commandline/network_connect/

```sh
docker network connect [OPTIONS] NETWORK CONTAINER
```



**주요 옵션**

| Option    | Short | Default | Description                  |
| --------- | ----- | ------- | ---------------------------- |
| `--alias` |       |         | 네트워크 alias를 추가합니다. |
| `--ip`    |       |         | IPv4 주소를 지정합니다.      |



### 네트워크 연결 제거

> https://docs.docker.com/engine/reference/commandline/network_disconnect/

```sh
docker network disconnect [OPTIONS] NETWORK CONTAINER
```



### 네트워크 삭제

> https://docs.docker.com/engine/reference/commandline/network_rm/

```sh
docker network rm NETWORK [NETWORK...]
```

`network rm`은 한 개 이상의 명시된 네트워크들을 삭제하는 명령입니다. 



### 모든 네트워크 삭제

> https://docs.docker.com/engine/reference/commandline/network_prune/

```sh
docker network prune [OPTIONS]
```

`network prune`은 **사용하지 않는** 모든 네트워크들을 삭제하는 명령어 입니다.



**주요 옵션**

| Option     | Short | Default | Description               |
| ---------- | ----- | ------- | ------------------------- |
| `--filter` |       |         | 필터링 조건을 설정합니다. |



---

### 연습 문제

#### [연습] `bridge` 네트워크를 이용하여 컨테이너 연결하기

사용할 네트워크를 다음과 같이 생성합니다. 

```cmd
$ docker network -d bridge create private
private
```



생성된 네트워크는 `docker network list` 명령어를 통해서 확인할 수 있습니다. 

```cmd
$ docker network list
NETWORK ID     NAME      DRIVER    SCOPE
71bf83fc2d7c   bridge    bridge    local
00c55e1a5560   host      host      local
ceedb973ae73   none      null      local
5bce335632dc   private   bridge    local
```



`PostgreSQL`과 `PgAdmin` 컨테이너를 생성합니다. 이 때, `PgAdmin` 컨테이너는 위에서 생성한 네트워크를 사용하여 생성합니다. 

```cmd
# PostgreSQL DB 생성
$ docker run --rm -d --name db -e POSTGRES_PASSWORD=mysecretpassword postgres:16.1-bullseye

# PgAdmin Application 생성
$ docker run --rm -d -p 80:80 --name pgadmin -e PGADMIN_DEFAULT_EMAIL=user@sample.com -e PGADMIN_DEFAULT_PASSWORD=SuperSecret --network private dpage/pgadmin4:7.4
```



다음 명령어를 통해서 `db` 컨테이너의 `network`별 IP 주소를 확인합니다. 

```cmd
$ docker inspect db -f '{{range $k, $v := .NetworkSettings.Networks}}{{print $k}}={{println $v.IPAddress}}{{end}}'
bridge=172.17.0.3
```



`bridge` 네트워크에 할당된 `IP`를 이용하여 `PgAdmin`에서 연결을 시도합니다. 

- `PgAdmin` 페이지에 접속합니다. 
  - http://localhost:80

![pgadmin_1](docker_practice.assets/pgadmin_1.PNG)

- `Servers`를 클릭한 다음, 좌측 상단 `Object > Register > Server`를 클릭합니다. 

![pgadmin_2](docker_practice.assets/pgadmin_2.PNG)



- `General` 탭에서 `Name`을 기입합니다. 
- `Connection` 탭에서 접속할 `DB`서버 정보를 입력합니다. 
  - host: `bridge` 네트워크에서 할당된 IP
  - Username: `postgres`
  - Password: 컨테이너 실행시 입력한 `POSTGRES_PASSWORD` 

![pgadmin_3](docker_practice.assets/pgadmin_3.PNG)

- `DB` 연결에 실패한 것을 확인할 수 있습니다. 



이번엔 위에서 생성한 `private` 네트워크를 `DB`에 연결한 이후, `private` 네트워크에서 할당된 `IP` 주소를 확인합니다. 

```cmd
$ docker network connect private db

$ docker inspect db -f '{{range $k, $v := .NetworkSettings.Networks}}{{print $k}}={{println $v.IPAddress}}{{end}}'
bridge=172.17.0.3
private=172.18.0.3

```



해당 `IP`를 이용하여 `PgAdmin`에서 연결의 시도합니다. 

![pgadmin_4](docker_practice.assets/pgadmin_4.PNG)



정상적으로 연결이 되는 것을 확인할 수 있습니다. 

![pgadmin_5](docker_practice.assets/pgadmin_5.PNG)



#### [연습] `alias`를 이용하여 `ip`없이 컨테이너 통신하기

다음과 같이 `ubuntu` 컨테이너를 생성하고 필요한 `Package`를 생성합니다. 

```cmd
$ docker run --name main -itd ubuntu:22.04
$ docker exec main /bin/bash -c "apt-get update && apt-get upgrade && apt-get install -y wget dnsutils"
```



`nginx` 컨테이너를 다음과 같이 3개 생성합니다. 

```cmd
$ docker run --rm -d --net private --net-alias web_app --name nginx1 nginx:latest
$ docker run --rm -d --net private --net-alias web_app --name nginx2 nginx:latest
$ docker run --rm -d --net private --net-alias web_app --net-alias ready --name nginx3 nginx:latest
```



`main` 컨테이너에서 `web_app`에 대해 `dig`을 사용하면, 다음과 같이 `DNS` 질의에 대한 응답이 없는 것을 확인할 수 있습니다. 

> `nslookup web_app`을 이용하여 확인해도 됩니다. 

```cmd
$ docker exec main dig web_app

; <<>> DiG 9.18.18-0ubuntu0.22.04.1-Ubuntu <<>> web_app
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 53782
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 1232
; COOKIE: bcdc7ef17f1d57fd (echoed)
;; QUESTION SECTION:
;web_app.			IN	A

;; Query time: 5 msec
;; SERVER: 127.0.0.11#53(127.0.0.11) (UDP)
;; WHEN: Sat Dec 23 07:59:12 UTC 2023
;; MSG SIZE  rcvd: 48
```



`main` 컨테이너에 `private` 네트워크를 다음과 같이 연결합니다. 

```cmd
$ docker network connect --alias main private main
$ docker inspect main -f "Alias:{{ println .NetworkSettings.Networks.private.Aliases }}IP:{{ println .NetworkSettings.Networks.private.IPAddress }}"
Alias:[main 51c471535b96]
IP:172.19.0.5
```



다시 한번 `main` 서버에서 `dig`를 사용하면 3개 컨테이너의 `IP`가 반환된 것을 확인할 수 있습니다. 

```cmd
$ docker exec main dig web_app

; <<>> DiG 9.18.18-0ubuntu0.22.04.1-Ubuntu <<>> web_app
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 43503
;; flags: qr rd ra; QUERY: 1, ANSWER: 3, AUTHORITY: 0, ADDITIONAL: 0

;; QUESTION SECTION:
;web_app.			IN	A

;; ANSWER SECTION:
web_app.		600	IN	A	172.19.0.4
web_app.		600	IN	A	172.19.0.2
web_app.		600	IN	A	172.19.0.3

;; Query time: 0 msec
;; SERVER: 127.0.0.11#53(127.0.0.11) (UDP)
;; WHEN: Sat Dec 23 07:51:11 UTC 2023
;; MSG SIZE  rcvd: 94
```



다음과 같이 `ping`을 사용할 때마다 응답하는 `IP`가 바뀌는 것을 볼 수 있습니다. 

```cmd
$ docker exec main apt-get install -y iputils-ping
$ docker exec main ping web_app
PING web_app (172.19.0.2) 56(84) bytes of data.
64 bytes from nginx1.private (172.19.0.2): icmp_seq=1 ttl=64 time=0.060 ms
64 bytes from nginx1.private (172.19.0.2): icmp_seq=2 ttl=64 time=0.075 ms
64 bytes from nginx1.private (172.19.0.2): icmp_seq=3 ttl=64 time=0.076 ms
...
$ docker exec main ping web_app
PING web_app (172.19.0.4) 56(84) bytes of data.
64 bytes from nginx3.private (172.19.0.4): icmp_seq=1 ttl=64 time=0.136 ms
64 bytes from nginx3.private (172.19.0.4): icmp_seq=2 ttl=64 time=0.073 ms
...
```



#### [실습]  `DB`를 `alias`를 이용하여 연결하기

> 연습 문제(`bridge` 네트워크를 이용하여 컨테이너 연결하기)에서 `IP` 대신 `DNS`를 이용하세요

- `PostgreSQL`과 `PgAdmin` 컨테이너를 생성합니다
-  `private` 네트워크를 `DB`에 연결하면서 `Alias`를 설정합니다.
- `PgAdmin`에서 `IP` 대신 `Alias`를 이용하여 `DB`에 연결합니다. 



---



## 1.7. Docker Advance - Image

### Docker commit

> https://docs.docker.com/engine/reference/commandline/commit/

```shell
docker commit [OPTIONS] CONTAINER [REPOSITORY[:TAG]]
```



**주요 옵션**

| Option      | Short | Default | Description                                     |
| ----------- | ----- | ------- | ----------------------------------------------- |
| `--author`  | `-a`  |         | `Author`를 설정합니다.                          |
| `--change`  | `-c`  |         | 추가로 적용할 `Dockerfile` 명령어를 설정합니다. |
| `--message` | `-m`  |         | Commit message                                  |
| `--pause`   | `-p`  | `true`  | `commit`동안 컨테이너를 중지합니다.             |



**주의사항**

- `Production`에서 사용할 이미지라면 `commit` 대신  `Dockerfile`을 기반으로 제작하는 것이 좋습니다. 
- 볼륨(`volume`)에 저장된 데이터는 포함되지 않습니다. 
- `--pause` 옵션을 설정하지 않은 경우, `commit`하는 동안 컨테이너를 중지(`pause`)됩니다. 
- `--change`에서 지원하는 명령어는 다음과 같습니다
    - CMD
    - ENTRYPOINT
    - ENV
    - EXPOSE
    - LABEL
    - ONBUILD
    - USER
    - VOLUME
    - WORKDIR



### Docker buildx

> https://docs.docker.com/engine/reference/commandline/buildx/

```
Usage:  docker buildx [OPTIONS] COMMAND

Extended build capabilities with BuildKit

Options:
      --builder string   Override the configured builder instance

Management Commands:
  imagetools  Commands to work on images in registry

Commands:
  bake        Build from a file
  build       Start a build
  create      Create a new builder instance
  du          Disk usage
  inspect     Inspect current builder instance
  ls          List builder instances
  prune       Remove build cache
  rm          Remove a builder instance
  stop        Stop builder instance
  use         Set the current builder instance
  version     Show buildx version information

Run 'docker buildx COMMAND --help' for more information on a command.
```



#### builder 목록 확인하기

> https://docs.docker.com/engine/reference/commandline/buildx_ls/

```shell
docker buildx ls
```



##### [예시] 현재 사용중인 `builder` 확인하기

현재 선택된 `builder`는 `*`이 붙어있습니다. 

```cmd
$ docker buildx ls
NAME/NODE       DRIVER/ENDPOINT STATUS  BUILDKIT             PLATFORMS
default *       docker
  default       default         running v0.11.6+616c3f613b54 linux/amd64, linux/amd64/v2, linux/amd64/v3, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/mips64le, linux/mips64, linux/arm/v7, linux/arm/v6
```



#### builder 생성하기

> https://docs.docker.com/engine/reference/commandline/buildx_create/

```shell
docker buildx create [OPTIONS] [CONTEXT|ENDPOINT]
```



| Option         | Short | Default | Description                                                  |
| -------------- | ----- | ------- | ------------------------------------------------------------ |
| `--driver`     |       |         | 사용할 드라이버를 설정합니다. <br />(`docker-container`, `kubernetes`, `remote`) |
| `--driver-opt` |       |         | 드라이버에 따른 옵션을 설정합니다.                           |
| `--name`       |       |         | `builder` 이름을 지정합니다.                                 |
| `--platform`   |       |         | `platform`을 지정합니다.                                     |
| `--use`        |       |         | 생성 후 해당 `builder`를 사용합니다.                         |



#### builder 정보 조회

> https://docs.docker.com/engine/reference/commandline/buildx_inspect/

```shell
docker buildx inspect [NAME]
```



##### [예시] 현재 사용중인 `builder` 조회하기

```cmd
$ docker buildx inspect --bootstrap
Name:          default
Driver:        docker
Last Activity: 2023-12-22 02:37:31 +0000 UTC

Nodes:
Name:      default
Endpoint:  default
Status:    running
Buildkit:  v0.11.6+616c3f613b54
Platforms: linux/amd64, linux/amd64/v2, linux/amd64/v3, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/mips64le, linux/mips64, linux/arm/v7, linux/arm/v6
Labels:
 org.mobyproject.buildkit.worker.moby.host-gateway-ip: 192.168.65.254
GC Policy rule#0:
 All:           false
 Filters:       type==source.local,type==exec.cachemount,type==source.git.checkout
 Keep Duration: 172.8µs
 Keep Bytes:    2.764GiB
GC Policy rule#1:
 All:           false
 Keep Duration: 5.184ms
 Keep Bytes:    20GiB
GC Policy rule#2:
 All:        false
 Keep Bytes: 20GiB
GC Policy rule#3:
 All:        true
 Keep Bytes: 20GiB
```



#### builder 선택하기

> https://docs.docker.com/engine/reference/commandline/buildx_use/

```shell
docker buildx use [OPTIONS] NAME
```



| Option      | Short | Default | Description             |
| ----------- | ----- | ------- | ----------------------- |
| `--default` |       |         | `default`로 설정합니다. |



#### 이미지 빌드하기

> https://docs.docker.com/engine/reference/commandline/buildx_build/

```shell
docker buildx build [OPTIONS] PATH | URL | -
```



**주요 옵션**

| Option        | Short | Default | Description                                                  |
| ------------- | ----- | ------- | ------------------------------------------------------------ |
| `--build-arg` |       |         | `ARG`를 설정합니다.                                          |
| `--file`      | `-f`  |         | `Dockerfile`의 경로를 지정합니다.                            |
| `--label`     |       |         | 라벨을 추가합니다.                                           |
| `--no-cache`  |       |         | 이미지 빌드시 캐시를 사용하지 않습니다.                      |
| `--platform`  |       |         | `platform`을 지정합니다.                                     |
| `--pull`      |       |         | 관련된 이미지를 저장 유무에 관계없이 `pull`합니다.           |
| `--load`      |       |         | 이미지를 `host`에 저장합니다.<br />(`--output=type=docker`와 동일합니다.) |
| `--push`      |       |         | 이미지를 `registry`에 저장합니다<br />(`--output=type=registry`와 동일합니다.) |
| `--tag`       | `-t`  |         | 이름과 `Tag`를 설정합니다.                                   |



**주의사항**

- `docker` 드라이버를 사용하는 `builder`는 단일 `platform` 이미지만 빌드할 수 있습니다. 
- `docker` 드라이버가 아닌 다른 드라이버를 사용하는 경우 `--load`를 사용하여야 이미지를 가져올 수 있습니다. 
  - `--load`는 `export`를 이용하여 이미지를 추출하고 `import`합니다. 
- 2개 이상의 `platform`을 지원하는 이미지를 제작하는 경우 `--load`가 불가능하므로 `--push`를 통해 `registry`로 바로 업로드해야 합니다. 



##### [예시] `linux/arm/v7`용 이미지 제작하기

현재 `builder`에서 지원하는 `platform`을 다음과 같이 확인합니다. 

```cmd
$ docker buildx inspect --bootstrap
Name:          multi-arch-builder
Driver:        docker-container
Last Activity: 2023-12-23 13:46:16 +0000 UTC

...
Platforms: linux/amd64, linux/amd64/v2, linux/amd64/v3, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/mips64le, linux/mips64, linux/arm/v7, linux/arm/v6
...
```



`--platform` 옵션을 이용하여 `linux/arm/v7`용 이미지를 제작합니다. 

```cmd
$ docker buildx build --platform linux/arm/v7 -t cloudwave:arm .
...

$ docker image inspect cloudwave:arm -f "arch: {{ .Architecture }}/{{ .Variant }}" 
arch: arm/v7
```



---

### 연습 문제

#### [연습] Commit을 이용하여 패키지가 추가로 설치된 이미지 생성하기

다음과 같이 `Ubuntu` 컨테이너를 실행합니다. 

```cmd
$ docker run -it -d --name base ubuntu:22.04
ebb9fe13e5cfb0747e5bea1db7c41ef30bf416bdcd643d7311e161ee4300b628
```



`curl` 설치 여부를 확인합니다. 

```cmd
$ docker exec base apt list --installed "curl*"
WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Listing...
```



다음과 같이 `Curl`을 설치한 후, 설치 여부를 재확인합니다. 

```cmd
$ docker exec base /bin/bash -c "apt-get update && apt-get upgrade && apt-get install -y curl"

$ docker exec base apt list --installed "curl*"

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Listing...
curl/jammy-updates,jammy-security,now 7.81.0-1ubuntu1.15 amd64 [installed]
```



`commit`을 이용하여 `base` 컨테이너를 다음과 같이 저장합니다. 

```cmd
$ docker commit base commit:v1
sha256:56c923d569eccda8bd094286ca7356ea2fa1a3a7794df6f22369980dd78bc943

$ docker images commit
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
commit       v1        56c923d569ec   16 seconds ago   132MB
```



저장된 이미지를 실행하여 `curl`이 설치되어 있는지 확인합니다. 

```cmd
$ docker run --name restore commit:v1 apt list --installed "curl*"

WARNING: apt does not have a stable CLI interface. Use with caution in scripts.

Listing...
curl/jammy-updates,jammy-security,now 7.81.0-1ubuntu1.15 amd64 [installed]
```



#### [연습] 실행중인 컨테이너의 Port를 추가로 `Expose`하기

컨테이너를 다음과 같이 생성하고, `Port`가 노출되지 않은 것을 체크합니다다. 

```cmd
$ docker run -it -d --name base ubuntu:22.04
$ docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED       STATUS       PORTS     NAMES
51c471535b96   ubuntu:22.04   "/bin/bash"              2 hours ago   Up 2 hours             base
```



`--change` 옵션을 이용하여 80번 포트를 `Expose`합니다. 

이 때, 기존에 실행중인 컨테이너가 멈추지 않도록 `--pause`를 `false`로 설정합니다. 

```cmd
$ docker commit --change="EXPOSE 80" --pause=false base commit:v1
sha256:f908d82f79101ec792d5383a627c8ffa9dd92d65bf5674cf0ad8e9c0c7c943b2
```



저장한 이미지를 사용하여 새로운 컨테이너를 생성하고, 80번 포트가 열려있는 것을 확인합니다. 

```cmd
$ docker run -itd commit:v1
c551f14323f9bc6dccf23b0a68fb9c4610e73079e7d76fe09306ed35aa8c0f57

$ docker ps
CONTAINER ID   IMAGE          COMMAND                   CREATED              STATUS              PORTS     NAMES
c551f14323f9   commit:v1      "/bin/bash"               About a minute ago   Up About a minute   80/tcp    commit
51c471535b96   ubuntu:22.04   "/bin/bash"               2 hours ago          Up 2 hours                    base
```



`inspect`를 사용하면 다음과 같이 `commit:v1` 이미지에 새로운 레이어가 추가되어 있는 것을 확인할 수 있습니다. 

> `jq`가 설치되어 있지 않은 경우, `Appendix`를 참고하여 설치해주세요.

```cmd
$ docker inspect ubuntu:22.04 | jq ".[0].RootFS.Layers"
[
  "sha256:a1360aae5271bbbf575b4057cb4158dbdfbcae76698189b55fb1039bc0207400"
]
$ docker inspect commit:v1 | jq ".[0].RootFS.Layers"
[
  "sha256:a1360aae5271bbbf575b4057cb4158dbdfbcae76698189b55fb1039bc0207400",
  "sha256:36005e181ab5ff954b4d4191c6a3f6a69a62cf2d3367c53e6889ee2d14757c44"
]
```



#### [연습] `buildx`를 이용하여 `ARM`용 `cloudwave:base.v1` 이미지 제작하기

> [연습] 실습용 `ubuntu` 이미지 제작하기

`buildx ls`를 이용하여 현재 사용중인 `builder`가 지원하는 `platform` 목록을 확인합니다. 

```cmd
$ NAME/NODE             DRIVER/ENDPOINT                STATUS  BUILDKIT             PLATFORMS
default *             docker
  default             default                        running v0.11.6+616c3f613b54 linux/amd64, linux/amd64/v2, linux/amd64/v3, linux/arm64, linux/riscv64, linux/ppc64le, linux/s390x, linux/386, linux/mips64le, linux/mips64, linux/arm/v7, linux/arm/v6
```



`buildx build` 명령어를 이용하여 `linux/arm/v7`용 이미지를 제작합니다. 

```cmd
# builder의 driver가 docker인 경우
$ docker buildx build --platform linux/arm/v7 -t cloudwave:arm.v1 . 

# builder의 driver가 docker-container인 경우 `--load` 옵션을 추가합니다.
$ docker buildx build --platform linux/arm/v7 -t cloudwave:arm.v1 --load .
```



다음 명령어를 실행하여 생성한 이미지의 `Architecture`를 확인 할 수 있습니다. 

```cmd
$ docker image inspect cloudwave:arm.v1 -f "arch: {{ .Architecture }}/{{ .Variant }}"
arch: arm/v7
```



#### [실습] `buildx`를 이용하여 `Multi-platform` 이미지 제작하기

1. `Docker Hub`에 로그인 합니다. 
2. `docker-container`를 사용하는 `builder`를 생성합니다. 
   - `--use` 옵션을 사용하여 바로 사용할 수 있도록 설정합니다. 
3. 다음 조건을 만족하는 `Multi-platform` 이미지를 `build`합니다
   - `--push`를 이용하여 `Docker Hub`로 업로드해야 합니다.
   - 이미지는 `linux/amd64`, `arm/v6`를 지원해야 합니다. 
4. `Docker Hub`에서 업로드한 이미지를 확인합니다. 



---

## 종합 문제

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



---

# Appendix

## Terraform

```shell
terraform init

terraform plan

terraform apply
```



## `Chocolatey` 설치

> Windows용

### CMD

> Admin 권한으로 실행해야 합니다. 

```cmd
@"%SystemRoot%\System32\WindowsPowerShell\v1.0\powershell.exe" -NoProfile -InputFormat None -ExecutionPolicy Bypass -Command "iex ((New-Object System.Net.WebClient).DownloadString('https://chocolatey.org/install.ps1'))" && SET "PATH=%PATH%;%ALLUSERSPROFILE%\chocolatey\bin"
```



### PowerShell

> Admin 권한으로 실행해야 합니다. 

```powershell
Set-ExecutionPolicy Bypass -Scope Process -Force; [System.Net.ServicePointManager]::SecurityProtocol = [System.Net.ServicePointManager]::SecurityProtocol -bor 3072; iex ((New-Object System.Net.WebClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```





## jq(JSON Parser) 설치

### Windows (Chocolatey)

```cmd
$ choco install jq
```



### Ubuntu apt

```cmd
$ apt install jq
```

























































