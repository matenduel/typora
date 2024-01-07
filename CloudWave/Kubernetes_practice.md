# Kubernetes

# 1. Install

## 1.1. Kubectl

> https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-linux/
>
> https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-windows/

- `Kubernetes` 클러스터를 제어하기 위한 `CLI`
- `Kubernetes API`를 이용하여 클러스터의 `Control Plane`과 통신합니다. 



### Kubens & Kubectx

> https://github.com/ahmetb/kubectx

####  apt (Debian)

```
sudo apt install kubectx
```



#### Homebrew (macOS & Linux)

```
brew install kubectx
```



#### Windows (Chocolatey)

```
choco install kubens kubectx
```



## 1.2. OpenLens

> `Kubernetes` 관리를 위한 GUI 툴

- `Lens`의 오픈소스 버전



**Homebrew**

```cmd
$ brew install --cask openlens
```



**Window**

```cmd
$ choco install -y openlens
```



### Extentions

> `Menu` > `Extentions`

- `Pod Log` 확인

``` 
@alebcay/openlens-node-pod-menu
```



- `NetworkPolicy` 확인

```
lens-extension-network-policy-viewer
```





# 2. CLI

> https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/#%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1

### 2.1. Kubectl

```yaml
Basic Commands (Beginner):
  create          Create a resource from a file or from stdin
  expose          Take a replication controller, service, deployment or pod and expose it as a new Kubernetes service
  run             Run a particular image on the cluster
  set             Set specific features on objects

Basic Commands (Intermediate):
  explain         Get documentation for a resource
  get             Display one or many resources
  edit            Edit a resource on the server
  delete          Delete resources by file names, stdin, resources and names, or by resources and label selector

Deploy Commands:
  rollout         Manage the rollout of a resource
  scale           Set a new size for a deployment, replica set, or replication controller
  autoscale       Auto-scale a deployment, replica set, stateful set, or replication controller

Cluster Management Commands:
  certificate     Modify certificate resources
  cluster-info    Display cluster information
  top             Display resource (CPU/memory) usage
  cordon          Mark node as unschedulable
  uncordon        Mark node as schedulable
  drain           Drain node in preparation for maintenance
  taint           Update the taints on one or more nodes

Troubleshooting and Debugging Commands:
  describe        Show details of a specific resource or group of resources
  logs            Print the logs for a container in a pod
  attach          Attach to a running container
  exec            Execute a command in a container
  port-forward    Forward one or more local ports to a pod
  proxy           Run a proxy to the Kubernetes API server
  cp              Copy files and directories to and from containers
  auth            Inspect authorization
  debug           Create debugging sessions for troubleshooting workloads and nodes
  events          List events

Advanced Commands:
  diff            Diff the live version against a would-be applied version
  apply           Apply a configuration to a resource by file name or stdin
  patch           Update fields of a resource
  replace         Replace a resource by file name or stdin
  wait            Experimental: Wait for a specific condition on one or many resources
  kustomize       Build a kustomization target from a directory or URL

Settings Commands:
  label           Update the labels on a resource
  annotate        Update the annotations on a resource
  completion      Output shell completion code for the specified shell (bash, zsh, fish, or powershell)

Other Commands:
  api-resources   Print the supported API resources on the server
  api-versions    Print the supported API versions on the server, in the form of "group/version"
  config          Modify kubeconfig files
  plugin          Provides utilities for interacting with plugins
  version         Print the client and server version information
```



**주요 옵션**

| Option        | Short | Default | Description                           |
| ------------- | ----- | ------- | ------------------------------------- |
| `--namespace` | `-n`  |         | CLI 요청의 네임스페이스를 지정합니다. |



#### 클러스터 정보 조회

```cmd
$ kubectl cluster-info
Kubernetes control plane is running at https://127.0.0.1:6443
CoreDNS is running at https://127.0.0.1:6443/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
```



#### `kubeconfig` 조회

```cmd
$ kubectl config view
```



#### `context` 조회하기 

```cmd
$ kubectl config get-contexts
```



#### 클러스터 변경하기 

```cmd
$ kubectl config use-context NAME
```



#### 네임스페이스 변경하기 

```cmd
$ kubectl config set-context --current --namespace=<namespace-name>
```



#### 지원하는 리소스 확인하기

```cmd
$ kubectl api-resources [flags] [options]

NAME                              SHORTNAMES   APIVERSION                             NAMESPACED   KIND
...
daemonsets                        ds           apps/v1                                true         DaemonSet
deployments                       deploy       apps/v1                                true         Deployment
replicasets                       rs           apps/v1                                true         ReplicaSet
statefulsets                      sts          apps/v1                                true         StatefulSet
...
```



#### `Configuration` 적용하기

```cmd
$ kubectl apply (-f FILENAME | -k DIRECTORY) [options]
```



#### 리소스 목록보기

```cmd
$  kubectl get (TYPE[.VERSION][.GROUP] [NAME | -l label] | TYPE[.VERSION][.GROUP]/NAME ...) [flags] [options]
```



##### [예시] 라벨까지 표기된 `POD` 목록 보기

```cmd
$ kubectl get po --show-labels=true
NAME     READY   STATUS             RESTARTS         AGE   LABELS
nginx    2/3     CrashLoopBackOff   81 (3m22s ago)   9h    env=dev,project=cloudwave
ubuntu   1/1     Running            1 (170m ago)     9h    env=dev,project=cloudwave
```

##### [예시] `Namespace` 목록보기

```cmd
$  kubectl get ns
NAME              STATUS   AGE
default           Active   9h
kube-node-lease   Active   9h
kube-public       Active   9h
kube-system       Active   9h
```



#### 리소스 상세 조회하기

```cmd
$ kubectl describe (-f FILENAME | TYPE [NAME_PREFIX | -l label] | TYPE/NAME) [options]
```



#### 리소스 삭제하기

```cmd
$ kubectl delete ([-f FILENAME] | [-k DIRECTORY] | TYPE [(NAME | -l label | --all)]) [options]
```



##### [예시] `deployment` 삭제하기

```cmd
$ kubectl delete deployment nginx-deployment
deployment.apps "nginx-deployment" deleted
```

##### [예시] `pod` 삭제하기

```cmd
$ kubectl delete pod nginx 
pod "nginx" deleted
```





### 2.2. Kubens

```cmd
USAGE:
  kubens                    : list the namespaces in the current context
  kubens <NAME>             : change the active namespace of current context
  kubens -                  : switch to the previous namespace in this context
  kubens -c, --current      : show the current namespace
  kubens -h,--help          : show this message
```



#### 네임스페이스 목록보기

```cmd
$ kubens
default
kube-node-lease
kube-public
kube-system
```



#### 네임스페이스 변경하기

```cmd
$ kubens kube-system
Context "docker-desktop" modified.
Active namespace is "kube-system".
```



#### 현재 사용중인 네임스페이스 확인하기

```cmd
$ kubens -c
default
```



### 2.3. Kubectx

```cmd
USAGE:
  kubectx                       : list the contexts
  kubectx <NAME>                : switch to context <NAME>
  kubectx -                     : switch to the previous context
  kubectx -c, --current         : show the current context name
  kubectx <NEW_NAME>=<NAME>     : rename context <NAME> to <NEW_NAME>
  kubectx <NEW_NAME>=.          : rename current-context to <NEW_NAME>
  kubectx -d <NAME> [<NAME...>] : delete context <NAME> ('.' for current-context)
                                  (this command won't delete the user/cluster entry
                                  that is used by the context)
  kubectx -u, --unset           : unset the current context

  kubectx -h,--help             : show this message
```



#### `context` 목록보기

```cmd
$ kubectx
dev_common
dev_data
docker-desktop
```



#### `context` 이름 설정하기 

```cmd
$ kubectx <NEW_NAME>=<NAME>
```



#### `context` 변경하기 

```cmd
$ kubectx <NAME>
```





# 3. YAML

> https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/kubernetes-objects/

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
  labels:
    project: cloudwave
    env: dev
  annotations:
    project.cloudwave.pod: sample
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
      ports:
        - containerPort: 80
      imagePullPolicy: Always
      
    - image: ubuntu:22.04
      name: "ubuntu"
      stdin: true
      tty: true
```

## apiVersion

- 사용할 쿠버네티스 `API`버전을 의미합니다. 



## Kind

- `Object`의 종류를 의미합니다. 



## Metadata

### Name

- `Object`의 이름을 의미합니다. 
- 동일한 `resource` 내에서 고유해야 합니다. 



### Namespace

- 사용할 `namespace`를 의미합니다. 



### Label

- `Key/Value`로 이루어져 있습니다. 
- 여러 `Object`가 동일한 라벨을 가질 수 있습니다. 
- `lableSelector`를 이용하여 특정 조건에 맞는 라벨을 가진 `Object`를 식별할 수 있습니다. 



### Annotation

- `Key/Value`로 이루어져 있습니다. 
- `Label`과 달리 `Object`를 선별하는데 사용되지 않습니다. 
- `Annotation`을 이용하여 프로그램이나 오퍼레이터 등을 제어할 수 있습니다. 
    - ex) `Prometheus`, `Vault Injector`, ...



## Spec

- 리소스의 세부 내용을 정의합니다. 



# 4. Pod

- 하나 이상의 컨테이너로 구성된 최소 배포 단위입니다. 
- `Pod`내의 컨테이너들은 네트워크 및 스토리지를 공유합니다. 
- `Pod`내의 컨테이너는 항상 같은 노드에 위치합니다. 





## YAML

> https://kubernetes.io/docs/reference/kubernetes-api/workload-resources/pod-v1

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    project: cloudwave
    env: dev
  annotations:
    project.cloudwave.pod: "sample"
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
      imagePullPolicy: Always
    - image: ubuntu:22.04
      name: "ubuntu"
      stdin: true
      tty: true
  restartPolicy: Always
```





### RestartPolicy

| Attributes | Description                                           |
| ---------- | ----------------------------------------------------- |
| *Always    | 항상 재시작합니다.                                    |
| OnFailure  | 비정상적으로 컨테이너가 종료된 경우에만 재시작합니다. |
| Never      | 절대 재시작하지 않습니다.                             |

- 재시작시 지수 백오프(`exponential back-off`)를 이용하여 재시도하며, 최대 5분까지 기다립니다.
    - ex) 10s, 20s,40s, …
- 컨테이너가 10분 이상 실행된 경우, 백오프 타이머를 초기화 합니다.



### Containers

#### image

- 사용할 이미지를 정의합니다. 



#### imagePullPolicy

- 이미지 `Pull` 정책을 정의합니다. 

| Attributes   | Description                                    |
| ------------ | ---------------------------------------------- |
| *Always      | 항상 다운로드합니다.                           |
| Never        | 이미지를 다운로드하지 않습니다.                |
| IfNotPresent | 노드에 이미지가 없는 경우에만 다운로드 합니다. |



#### name

- 컨테이너의 이름을 정의합니다. 



#### ports

- 노출할 포트를 정의합니다. 
- 컨테이너 내에서 `0.0.0.0`를 이용하여 수신하고 있는 포트가 있는 경우, `ports`가 정의되지 않았더라도 네트워크에서 접근할 수 있습니다. 



#### env

```yaml
env:
  - name: KEY
    value: VALUE
```

- 컨테이너에서 사용할 환경변수를 정의합니다. 
- `envFrom`을 이용하여 `Source`에서 값을 가져올 수 있습니다. 
    - ConfigMap
    - metadata
    - secret
    - ...



#### Probe

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    test: liveness
  name: liveness-exec
spec:
  containers:
  - name: liveness
    image: registry.k8s.io/busybox
    args:
      - /bin/sh
      - -c
      - touch /tmp/healthy; sleep 30; rm -f /tmp/healthy; sleep 600
    livenessProbe:
      exec:
        command:
        - cat
        - /tmp/healthy
      initialDelaySeconds: 5
      periodSeconds: 5
```



##### 작동 방식

| Attributes | Description                                                  |
| ---------- | ------------------------------------------------------------ |
| exec       | 컨테이너 내에서 명령어를 실행합니다.<br />명령어의 exit code가 0인 경우 성공으로 간주합니다. |
| httpGet    | 지정된 경로와 포트로 HTTP Get을 호출합니다. <br />status code가 200이상 400미만인 경우 성공으로 간주합니다. |
| grpc       | `grpc`를 이용하여 수행합니다. <br />상태값이 `SERVING`인 경우 성공으로 간주됩니다. |
| tcpSocket  | 지정된 포트를 대상으로  `TCP` 체크를 진행합니다. <br />포트가 열려있는 경우 성공으로 취급합니다. |



##### Liveness

```yaml
containers:
  - image: nginx:latest
    name: "nginx"
    livenessProbe:
      httpGet:
        path: /
        port: 80
      initialDelaySeconds: 10
      periodSeconds: 5
```

- 컨테이너의 동작 여부를 체크합니다. 
- `fail`인 경우, `kubelet`은 컨테이너를 종료하고 정의된 `restart policy`에 따라 행동합니다. 
- 별도의 `liveness`가 정의되지 않은 경우 기본 상태는 `Success` 입니다. 





##### Readiness

```yaml
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
      readinessProbe:
        httpGet:
          path: /
          port: 80
        initialDelaySeconds: 10
        periodSeconds: 5
```

- 컨테이너가 요청을 처리할 수 있는지 여부를 체크합니다. 
- `fail`인 경우, `endpoint controller`는 `POD`의 `IP`를 모든 서비스들의 `endpoints`에서 제거합니다. 
- 별도의 `Readiness`가 정의되지 않은 경우 기본 상태는 `Success` 입니다. 



##### Startup

```yaml
startupProbe:
  httpGet:
    path: /healthz
    port: 8080
    httpHeaders:
      - name: User-Agent
        value: MyUserAgent
```

- 컨테이너 내의 `Application`이 시작되었는지 여부를 체크합니다. 
- `Startup Probe`가 정의된 경우, 상태가 `Success`가 되기 전까지 다른 ` Probe`는 활성화되지 않습니다. 
- `fail`인 경우, `kubelet`은 컨테이너를 종료하고 정의된 `restart policy`에 따라 행동합니다. 
- 별도의 `Startup`이 정의되지 않은 경우 기본 상태는 `Success` 입니다. 



## Pod 상태

| Value       | Description                                                  |
| :---------- | :----------------------------------------------------------- |
| `Pending`   | The Pod has been accepted by the Kubernetes cluster, but one or more of the containers has not been set up and made ready to run. This includes time a Pod spends waiting to be scheduled as well as the time spent downloading container images over the network. |
| `Running`   | The Pod has been bound to a node, and all of the containers have been created. At least one container is still running, or is in the process of starting or restarting. |
| `Succeeded` | All containers in the Pod have terminated in success, and will not be restarted. |
| `Failed`    | All containers in the Pod have terminated, and at least one container has terminated in failure. That is, the container either exited with non-zero status or was terminated by the system. |
| `Unknown`   | For some reason the state of the Pod could not be obtained. This phase typically occurs due to an error in communicating with the node where the Pod should be running. |



## 컨테이너 상태

| Value        | Description                                                  |
| :----------- | :----------------------------------------------------------- |
| `Waiting`    | 컨테이너가 `Running` 또는 `Terminated` 상태가 아닌 경우를 의미합니다. |
| `Running`    | 아무런 문제 없이 컨테이너가 실행중인 상태입니다.             |
| `Terminated` | 모종의 이유로 컨테이너가 종료된 상황입니다.                  |



## 연습 문제

### [연습] `nginx`와 `ubuntu` 배포하기

```yaml
# nginx_ubuntu.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-ubuntu
  labels:
    project: cloudwave
    env: dev
  annotations:
    project.cloudwave.pod: "sample"
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
    - image: ubuntu:22.04
      name: "ubuntu"
      stdin: true
      tty: true
```



`apply`를 이용하여 `Pod`을 배포합니다. 

```cmd
$ kubectl apply -f nginx_ubuntu.yaml
```



생성된 `Pod`은 다음 명령어를 이용하여 확인할 수 있습니다. 

```cmd
$ kubectl get po nginx-ubuntu
NAME           READY   STATUS    RESTARTS   AGE
nginx-ubuntu   2/2     Running   0          2m41s
```



### [연습] `Pod` 정보 조회하기

```cmd
$ kubectl describe po/nginx-ubuntu
Name:             nginx-ubuntu
Namespace:        default
Priority:         0
Service Account:  default
Node:             docker-desktop/192.168.65.3
Start Time:       Mon, 08 Jan 2024 00:46:08 +0900
Labels:           env=dev
                  project=cloudwave
Annotations:      project.cloudwave.pod: sample
Status:           Running
IP:               10.1.0.20
IPs:
  IP:  10.1.0.20
Containers:
  nginx:
    Container ID:   docker://e032e618b2f7272b153c905fd9ef4ab6cb7af64e344e23a3fbe43f0b7a7558c6
    Image:          nginx:latest
    Image ID:       docker-pullable://nginx@sha256:2bdc49f2f8ae8d8dc50ed00f2ee56d00385c6f8bc8a8b320d0a294d9e3b49026
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 08 Jan 2024 00:46:13 +0900
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8zhm2 (ro)
  ubuntu:
    Container ID:   docker://d84c063ce31ad1f0da2b58da92988dd6eb651658094756d85f2bf87ee08d60c5
    Image:          ubuntu:22.04
    Image ID:       docker-pullable://ubuntu@sha256:6042500cf4b44023ea1894effe7890666b0c5c7871ed83a97c36c76ae560bb9b
    Port:           <none>
    Host Port:      <none>
    State:          Running
      Started:      Mon, 08 Jan 2024 00:46:13 +0900
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-8zhm2 (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  kube-api-access-8zhm2:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    ConfigMapOptional:       <nil>
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type    Reason     Age    From               Message
  ----    ------     ----   ----               -------
  Normal  Scheduled  4m47s  default-scheduler  Successfully assigned default/nginx-ubuntu to docker-desktop
  Normal  Pulling    4m46s  kubelet            Pulling image "nginx:latest"
  Normal  Pulled     4m42s  kubelet            Successfully pulled image "nginx:latest" in 4.318s (4.318s including waiting)
  Normal  Created    4m42s  kubelet            Created container nginx
  Normal  Started    4m42s  kubelet            Started container nginx
  Normal  Pulled     4m42s  kubelet            Container image "ubuntu:22.04" already present on machine
  Normal  Created    4m42s  kubelet            Created container ubuntu
  Normal  Started    4m42s  kubelet            Started container ubuntu
```



### [연습] 컨테이너 접속하기

`exec`를 이용하여 다음과 같이 컨테이너에 접속할 수 있습니다. 

```cmd
$ kubectl exec -it po/nginx-ubuntu -c ubuntu -- /bin/bash
root@nginx-ubuntu:/# 
```



컨테이너를 명시하지 않은 경우, `YAML` 내에서 선언된 첫번째 컨테이너를 대상으로 작동합니다. 

```cmd
$ kubectl exec -it po/nginx-ubuntu -- /bin/bash 
Defaulted container "nginx" out of: nginx, ubuntu
root@nginx-ubuntu:/# 
```



### [연습] 컨테이너 로그 조회하기 

```cmd
$ kubectl logs pod/nginx-ubuntu -c nginx 
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/01/07 15:46:13 [notice] 1#1: using the "epoll" event method
2024/01/07 15:46:13 [notice] 1#1: nginx/1.25.3
2024/01/07 15:46:13 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/01/07 15:46:13 [notice] 1#1: OS: Linux 6.4.16-linuxkit
2024/01/07 15:46:13 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/01/07 15:46:13 [notice] 1#1: start worker processes
2024/01/07 15:46:13 [notice] 1#1: start worker process 29
2024/01/07 15:46:13 [notice] 1#1: start worker process 30
2024/01/07 15:46:13 [notice] 1#1: start worker process 31
2024/01/07 15:46:13 [notice] 1#1: start worker process 32
2024/01/07 15:46:13 [notice] 1#1: start worker process 33
2024/01/07 15:46:13 [notice] 1#1: start worker process 34

```



### [연습] `livenessProbe` 설정하기

```yaml
# nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
      livenessProbe:
        httpGet:
          path: /no_exists
          port: 80
        initialDelaySeconds: 10
        periodSeconds: 5
```

```cmd
$ kubectl apply -f pod.yaml 
pod/nginx created
```



`Pod` 생성 후, `kubectl get po`를 사용하면 다음과 같이 정상적으로 `POD`이 실행된 것을 확인할 수 있습니다.  

```cmd
$ kubectl get po            
NAME     READY   STATUS    RESTARTS   AGE
nginx    1/1     Running   0          7s
```



하지만, 잠시 후 확인해보면 `livenessProbe`에 실패하여 `POD`이 재실행된 것을 확인할 수 있습니다. 

```cmd
$ kubectl get po
NAME     READY   STATUS    RESTARTS      AGE
nginx    1/1     Running   1 (11s ago)   36s
```



`describe`를 이용하여 자세한 `Event`를 확인할 수 있습니다. 

```cmd
$ kubectl describe po/nginx
...
Events:
  Type     Reason     Age                    From               Message
  ----     ------     ----                   ----               -------
  Normal   Scheduled  3m28s                  default-scheduler  Successfully assigned default/nginx to docker-desktop
  Normal   Pulled     3m25s                  kubelet            Successfully pulled image "nginx:latest" in 2.633s (2.633s including waiting)
  Normal   Pulled     3m1s                   kubelet            Successfully pulled image "nginx:latest" in 2.042s (2.042s including waiting)
  Normal   Created    2m35s (x3 over 3m25s)  kubelet            Created container nginx
  Normal   Started    2m35s (x3 over 3m25s)  kubelet            Started container nginx
  Normal   Pulled     2m35s                  kubelet            Successfully pulled image "nginx:latest" in 2.481s (2.481s including waiting)
  Normal   Pulling    2m13s (x4 over 3m28s)  kubelet            Pulling image "nginx:latest"
  Warning  Unhealthy  2m13s (x9 over 3m13s)  kubelet            Liveness probe failed: HTTP probe failed with statuscode: 404
  Normal   Killing    2m13s (x3 over 3m3s)   kubelet            Container nginx failed liveness probe, will be restarted
```



### [연습] `readinessProbe` 설정하기

```yaml
# nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
```

```cmd
$ kubectl apply -f pod.yaml 
pod/nginx created
```



`readinessProbe`를 설정하지 않은 경우, 다음과 같이 `READY`가 1인 것을 확인할 수 있습니다. 

```cmd
$ kubectl get po            
NAME     READY   STATUS    RESTARTS   AGE
nginx    1/1     Running   0          10s
```



이번에는 `readinessProbe`를 설정한 `POD`을 생성해보겠습니다. 

```yaml
# nginx.yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-probe
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
      readinessProbe:
        httpGet:
          path: /test
          port: 80
        initialDelaySeconds: 10
        periodSeconds: 5
```



다음과 같이 시간이 흘러도 계속해서 `READY`가 0인것을 확인할 수 있습니다. 

```cmd
$ kubectl get po           
NAME     			 READY   STATUS    RESTARTS   AGE
nginx-probe    0/1     Running   0          2m42s
```



### [실습] `PgAdmin` + `PostgreSQL` 실행하기

- `Pod`을 다음과 같이 정의합니다. 
    - 이름은 `cloudwave-pg`로 설정합니다.
    - 라벨을 다음과 같이 설정합니다.

- `psql` 서비스를 다음과 같이 정의합니다. 

    - 이미지는 `postgres:16.1-bullseye`를 사용합니다.

    - 포트를 `Expose`합니다

        - 5432

    - 다음과 같이 환경 변수를 설정합니다. 

        ```tex
        POSTGRES_PASSWORD=secretPassword
        ```

- `pgadmin` 서비스를 다음과 같이 정의합니다. 

    - 이미지는 `dpage/pgadmin4:7.4`를 사용합니다.

    - 다음과 같이 환경 변수를 설정합니다. 

        ```tex
        PGADMIN_DEFAULT_EMAIL=user@sample.com
        PGADMIN_DEFAULT_PASSWORD=SuperSecret
        ```

- 다음 명령어를 이용하여 컨테이너 포트를 포워딩합니다.

```cmd
$ kubectl port-forward po/cloudwave-pg 8000:80
```

- `PgAdmin`페이지에 접속합니다.

```
http://localhost:8000
```





# 5. Workload Resource

## YAML

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: hello
  labels:
    app: hello
spec:
  selector:
    matchLabels:
      app: hello
      track: stable
    matchExpressions:
      - {key: tier, operator: In, values: [backend, backend2, backend3]}
  replicas: 3
  template:
    metadata:
      labels:
        app: hello
        tier: backend
        track: stable
    spec:
      containers:
        - name: hello
          image: "gcr.io/google-samples/hello-go-gke:1.0"
          ports:
            - name: http
              containerPort: 80
```





## ReplicaSet

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend
  labels:
    app: guestbook
    tier: frontend
spec:
  # 케이스에 따라 레플리카를 수정한다.
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: php-redis
        image: gcr.io/google_samples/gb-frontend:v3
```



## Deployments

> "파드와 레플리카셋(ReplicaSet)에 대한 선언적 업데이트를 제공한다"

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
  labels:
    app: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.14.2
        ports:
        - containerPort: 80
```





### [예시]

```cmd
$ kubectl describe deployments
```





## StatefulSets





### [예시]



## DaemonSet



### [예시]



## Jobs



### [예시]



## CronJob





### [예시]



## ReplicationController





### [예시]











# 6. Service

## YAML

```yaml
apiVersion: v1
kind: Service
metadata:
  name: hello
  labels:
    app: hello
spec:
  selector: # matchLabels가 존재하지 않는다.
    app: hello
    tier: backend
  ports:
  - protocol: TCP
    port: 80
    targetPort: http
```





## ClusterIP



## NodePorts



## LoadBalancer





























