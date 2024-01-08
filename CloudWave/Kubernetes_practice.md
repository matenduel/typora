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

### Selector

- 소유해야하는 또는 대상이 되는 파드(`POD`)를 선택하기 위한 조건을 명시합니다. 
- 일반적으로 `metadata.labels`와 동일하게 작성합니다. 



### Replicas

- 필요한 파드(`POD`) 수를 정의합니다. 



### Template

- 리소스를 통해서 생성 및 관리될 파드(`POD`)에 대해서 정의 합니다.



## ReplicaSet

> https://kubernetes.io/ko/docs/concepts/workloads/controllers/replicaset

```yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: replica-set-example
  labels:
    project: cloudwave
    resource: replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
      tier: frontend
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

- 파드(`POD`)를 배포하는 기본적인 방식입니다. 
- 지정된 수의 파드(`POD`)가 항상 실행되도록 관리합니다. 



### [예시] `Nginx` 파드 3개 배포하기

```yaml
# replicaset.yaml
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: rs-example
  labels:
    project: cloudwave
    resource: replicaset
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```



`apply`를 이용하여 `ReplicaSet`을 생성합니다. 

```cmd
$ kubectl apply -f replicaset.yaml 
replicaset.apps/rs-example created
```



생성된 `ReplicaSet`과 `Pod`는 다음과 같이 확인해볼 수 있습니다. 

```cmd
# ReplicaSet 조회
$ kubectl get rs
NAME         DESIRED   CURRENT   READY   AGE
rs-example   3         3         3       18s

# Pod 조회
$ kubectl get po -l app=nginx
NAME                        READY   STATUS    RESTARTS   AGE
rs-example-5xn65            1/1     Running   0          59s
rs-example-hbjcz            1/1     Running   0          59s
rs-example-zthj2            1/1     Running   0          59s
```



`delete`를 이용하여 생성된 `POD`을 삭제하면 다음과 같이 새로운 `POD`이 생성되는 것을 확인할 수 있습니다. 

```cmd
$ kubectl delete po/rs-example-5xn65
pod "rs-example-5xn65" deleted

$ kubectl get po
NAME                        READY   STATUS    RESTARTS   AGE
rs-example-4mknc            1/1     Running   0          5s
rs-example-hbjcz            1/1     Running   0          4m50s
rs-example-zthj2            1/1     Running   0          4m50s
```



`describe`를 이용하여 생성 이벤트를 포함한 자세한 정보를 확인할 수 있습니다. 

```cmd
$ kubectl describe rs/rs-example
Name:         rs-example
Namespace:    default
Selector:     app=nginx
Labels:       project=cloudwave
              resource=replicaset
Annotations:  <none>
Replicas:     3 current / 3 desired
Pods Status:  3 Running / 0 Waiting / 0 Succeeded / 0 Failed
Pod Template:
  Labels:  app=nginx
           tier=frontend
  Containers:
   nginx:
    Image:        nginx:latest
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Events:
  Type    Reason            Age    From                   Message
  ----    ------            ----   ----                   -------
  Normal  SuccessfulCreate  7m18s  replicaset-controller  Created pod: rs-example-5xn65
  Normal  SuccessfulCreate  7m18s  replicaset-controller  Created pod: rs-example-zthj2
  Normal  SuccessfulCreate  7m18s  replicaset-controller  Created pod: rs-example-hbjcz
  Normal  SuccessfulCreate  2m33s  replicaset-controller  Created pod: rs-example-4mknc

```



## Deployment

> https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/
>
> "파드와 레플리카셋(ReplicaSet)에 대한 선언적 업데이트를 제공한다"

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-example
  labels:
    project: cloudwave
    resource: deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
      tier: frontend
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

`Deployment` 는 `Deployment` 의 모든 `Pod` 가 필요 시 교체 또는 상호 교체 가능한 경우, 클러스터의 스테이트리스 애플리케이션 워크로드를 관리하기에 적합하다.

- `Stateless`한 어플리케이션을 배포할 때 주로 사용합니다. 
- `ReplicaSet`을 통해서 파드(`POD`)를 관리합니다. 
- `Pod-template-hash`를 이용하여 관리하는 `ReplicaSet`이 중복되지 않도록 보장합니다. 
- 자신이 수행한 모든 `rollout` 기록을 저장하고 있어, 해당 기록을 이용하여 롤백을 수행할 수 있습니다. 
- 별도의 업데이트 전략을 설정하지 않은 경우, `Rolling Update`가 사용됩니다. 



### [예시] `Deployment`가 생성한 `ReplicaSet` 확인하기

```cmd
$ kubectl describe deploy/deploy-example
Name:                   deploy-example
Namespace:              default
CreationTimestamp:      Tue, 09 Jan 2024 02:14:36 +0900
Labels:                 project=cloudwave
                        resource=deployment
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               app=nginx
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=nginx
           tier=frontend
  Containers:
   nginx:
    Image:        nginx:latest
    Port:         <none>
    Host Port:    <none>
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  <none>
NewReplicaSet:   deploy-example-849f9769f8 (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  49s   deployment-controller  Scaled up replica set deploy-example-849f9769f8 to 2
```



### [예시] `rollout`기록 확인하기

다음 `yaml` 파일을 이용하여 `deployment`를 생성합니다. 

```yaml
# deployment.yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: deploy-example
  labels:
    project: cloudwave
    resource: deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: nginx
      tier: frontend
  template:
    metadata:
      labels:
        app: nginx
        tier: frontend
    spec:
      containers:
        - name: nginx
          image: nginx:latest
```

```cmd
$ kubectl apply -f deployment.yaml
deployment.apps/deploy-example created
```



생성 후, `kubectl rollout history`를 이용하여 해당 `deployment`의 기록을 확인할 수 있습니다. 

```cmd
$ kubectl rollout history deployment/deploy-example  
deployment.apps/deploy-example 
REVISION  CHANGE-CAUSE
1         <none>
```



`nginx`의 버전을 `latest`에서 `1.16.1`로 변경한 뒤, `apply`를 통해서 업데이트하게 되면 다음과 같이 `REVISION`이 추가되는 것을 확인할 수 있습니다. 

```cmd
$ kubectl apply -f deployment.yaml                 
deployment.apps/deploy-example configured

$ kubectl rollout history deployment/deploy-example
deployment.apps/deploy-example 
REVISION  CHANGE-CAUSE
1         <none>
2         <none>
```



## StatefulSets

> https://kubernetes.io/ko/docs/concepts/workloads/controllers/statefulset/

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: sts-example
spec:
  selector:
    matchLabels:
      app: nginx # .spec.template.metadata.labels 와 일치해야 한다
  serviceName: "nginx"
  replicas: 3 # 기본값은 1
  minReadySeconds: 10 # 기본값은 0
  template:
    metadata:
      labels:
        app: nginx # .spec.selector.matchLabels 와 일치해야 한다
    spec:
      terminationGracePeriodSeconds: 10
      containers:
        - name: nginx
          image: registry.k8s.io/nginx-slim:0.8
          ports:
            - containerPort: 80
              name: web
          volumeMounts:
            - name: www
              mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
    - metadata:
        name: www
      spec:
        accessModes: [ "ReadWriteOnce" ]
        resources:
          requests:
            storage: 1Gi
```



- `Stateful`한 어플리케이션을 배포할 때 사용합니다.
- `ReplicaSet`을 통해서 파드(`POD`)를 관리하지 않으므로, `Rollback`이 불가능합니다.
- `deployment`를 통해 배포된 파드(`POD`)와 달리 이름에 숫자가 표기됩니다. 
- `StatefulSets`를 삭제하여도 관련된 볼륨은 유지됩니다.



## DaemonSet

```yaml
apiVersion: apps/v1
kind: DaemonSet
metadata:
  name: fluentd-elasticsearch
  namespace: kube-system
  labels:
    k8s-app: fluentd-logging
spec:
  selector:
    matchLabels:
      name: fluentd-elasticsearch
  template:
    metadata:
      labels:
        name: fluentd-elasticsearch
    spec:
      tolerations:
      # 이 톨러레이션(toleration)은 데몬셋이 컨트롤 플레인 노드에서 실행될 수 있도록 만든다.
      # 컨트롤 플레인 노드가 이 파드를 실행해서는 안 되는 경우, 이 톨러레이션을 제거한다.
      - key: node-role.kubernetes.io/control-plane
        operator: Exists
        effect: NoSchedule
      - key: node-role.kubernetes.io/master
        operator: Exists
        effect: NoSchedule
      containers:
      - name: fluentd-elasticsearch
        image: quay.io/fluentd_elasticsearch/fluentd:v2.5.2
        resources:
          limits:
            memory: 200Mi
          requests:
            cpu: 100m
            memory: 200Mi
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      terminationGracePeriodSeconds: 30
      volumes:
      - name: varlog
        hostPath:
          path: /var/log

```



- 모든 노드 또는 지정된 노드에서 `1개`의 파드가 실행되어야만 하는 상황에서 사용합니다.  
- 신규 노드가 추가될 경우 클러스터는 자동으로 `DaemonSet`에 정의된 파드를 스케쥴링합니다. 
- 모니터링, 로그 수집과 같은 목적으로 운영되는 어플리케이션을 배포할 때 사용합니다. 



## Jobs

> https://kubernetes.io/ko/docs/concepts/workloads/controllers/job/#%EB%8C%80%EC%95%88

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```

- 1회성으로 실행 후 종료되는 작업을 정의합니다. 
- `completed`된 파드는 별도의 메모리 또는 CPU를 차지하지 않습니다. 
- 완료된 파드를 삭제하기 전까지만 로그를 확인할 수 있습니다. 



### [예시] `Job` 실행 후, 결과 확인하기

```yaml
# job.yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: pi
spec:
  template:
    spec:
      containers:
      - name: pi
        image: perl:5.34.0
        command: ["perl",  "-Mbignum=bpi", "-wle", "print bpi(2000)"]
      restartPolicy: Never
  backoffLimit: 4
```



`apply`를 이용하여 다음과 같이 `job`을 생성합니다.

```cmd
$ kubectl apply -f job.yaml

$ kubectl get job
NAME   COMPLETIONS   DURATION   AGE
pi     1/1           20s        147m
```



`Job`을 통해 생성된 파드는 다음과 같이 `label`을 이용하여 조회할 수 있습니다. 

```cmd
$ kubectl get po -l job-name=pi    
NAME       READY   STATUS      RESTARTS   AGE
pi-f4z96   0/1     Completed   0          148m
```



`logs` 명령어를 이용하여 해당 파드가 출력한 정보를 확인할 수 있습니다. 

```cmd
$  kubectl logs pi-f4z96
3.1415926535897932...
```





## CronJob

> https://kubernetes.io/ko/docs/concepts/workloads/controllers/cron-jobs/

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "* * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo Hello from the Kubernetes cluster
          restartPolicy: OnFailure
```

- 크론잡은 주어진 일정에 따라 `Job`을 관리합니다. 
- 주기적으로 수행해야하는 작업이 있는 경우 사용됩니다. 



### Cron Expression

> https://crontab.guru/

```txt
# ┌───────────── 분 (0 - 59)
# │ ┌───────────── 시 (0 - 23)
# │ │ ┌───────────── 일 (1 - 31)
# │ │ │ ┌───────────── 월 (1 - 12)
# │ │ │ │ ┌───────────── 요일 (0 - 6) (일요일부터 토요일까지;
# │ │ │ │ │                                   특정 시스템에서는 7도 일요일)
# │ │ │ │ │                                   또는 sun, mon, tue, wed, thu, fri, sat
# │ │ │ │ │
# * * * * *
```



## 연습문제

### [연습] 템플릿을 사용하지 않는 파드의 획득

단독(bare) 파드를 생성하는 것에는 문제가 없지만, 단독 파드가 레플리카셋의 셀렉터와 일치하는 레이블을 가지지 않도록 하는 것을 강력하게 권장한다. 그 이유는 레플리카셋이 소유하는 파드가 템플릿에 명시된 파드에만 국한되지 않고, 이전 섹션에서 명시된 방식에 의해서도 다른 파드의 획득이 가능하기 때문이다.

이전 프런트엔드 레플리카셋 예제와 다음의 매니페스트에 명시된 파드를 가져와 참조한다.

[`pods/pod-rs.yaml` ](https://raw.githubusercontent.com/kubernetes/website/main/content/ko/examples/pods/pod-rs.yaml)![Copy pods/pod-rs.yaml to clipboard](https://kubernetes.io/images/copycode.svg)

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod1
  labels:
    tier: frontend
spec:
  containers:
  - name: hello1
    image: gcr.io/google-samples/hello-app:2.0

---

apiVersion: v1
kind: Pod
metadata:
  name: pod2
  labels:
    tier: frontend
spec:
  containers:
  - name: hello2
    image: gcr.io/google-samples/hello-app:1.0
```

기본 파드는 소유자 관련 정보에 컨트롤러(또는 오브젝트)를 가지지 않기 때문에 프런트엔드 레플리카셋의 셀렉터와 일치하면 즉시 레플리카셋에 소유된다.

프런트엔드 레플리카셋이 배치되고 초기 파드 레플리카가 셋업된 이후에, 레플리카 수 요구 사항을 충족시키기 위해서 신규 파드를 생성한다고 가정해보자.

```shell
kubectl apply -f https://kubernetes.io/examples/pods/pod-rs.yaml
```

새로운 파드는 레플리카셋에 의해 인식되며 레플리카셋이 필요한 수량을 초과하면 즉시 종료된다.

파드를 가져온다.

```shell
kubectl get pods
```

결과에는 새로운 파드가 이미 종료되었거나 종료가 진행 중인 것을 보여준다.

```
NAME             READY   STATUS        RESTARTS   AGE
frontend-b2zdv   1/1     Running       0          10m
frontend-vcmts   1/1     Running       0          10m
frontend-wtsmm   1/1     Running       0          10m
pod1             0/1     Terminating   0          1s
pod2             0/1     Terminating   0          1s
```

파드를 먼저 생성한다.

```shell
kubectl apply -f https://kubernetes.io/examples/pods/pod-rs.yaml
```

그 다음 레플리카셋을 생성한다.

```shell
kubectl apply -f https://kubernetes.io/examples/controllers/frontend.yaml
```

레플리카셋이 해당 파드를 소유한 것을 볼 수 있으며 새 파드 및 기존 파드의 수가 레플리카셋이 필요로 하는 수와 일치할 때까지 사양에 따라 신규 파드만 생성한다. 파드를 가져온다.

```shell
kubectl get pods
```

다음 출력에서 볼 수 있다.

```
NAME             READY   STATUS    RESTARTS   AGE
frontend-hmmj2   1/1     Running   0          9s
pod1             1/1     Running   0          36s
pod2             1/1     Running   0          36s
```

이러한 방식으로 레플리카셋은 템플릿을 사용하지 않는 파드를 소유하게 된다.



### [연습] 디플로이먼트 롤백

때때로 디플로이먼트의 롤백을 원할 수도 있다. 예를 들어 디플로이먼트가 지속적인 충돌로 안정적이지 않은 경우. 기본적으로 모든 디플로이먼트의 롤아웃 기록은 시스템에 남아있어 언제든지 원할 때 롤백이 가능하다 (이 사항은 수정 기록에 대한 상한 수정을 통해서 변경할 수 있다).

**참고:** 디플로이먼트의 수정 버전은 디플로이먼트 롤아웃시 생성된다. 이는 디플로이먼트 파드 템플릿 (`.spec.template`)이 변경되는 경우에만 새로운 수정 버전이 생성된다는 것을 의미한다. 예를 들어 템플릿의 레이블 또는 컨테이너 이미지를 업데이트 하는 경우. 디플로이먼트의 스케일링과 같은 다른 업데이트시 디플로이먼트 수정 버전은 생성되지 않으며 수동-스케일링 또는 자동-스케일링을 동시에 수행할 수 있다. 이는 이전 수정 버전으로 롤백을 하는 경우에 디플로이먼트 파드 템플릿 부분만 롤백된다는 것을 의미한다.

- 디플로이먼트를 업데이트하는 동안 이미지 이름을 `nginx:1.16.1` 이 아닌 `nginx:1.161` 로 입력해서 오타를 냈다고 가정한다.

    ```shell
    kubectl set image deployment/nginx-deployment nginx=nginx:1.161 
    ```

    이와 유사하게 출력된다.

    ```
    deployment.apps/nginx-deployment image updated
    ```

- 롤아웃이 고착 된다. 고착된 롤아웃 상태를 확인할 수 있다.

    ```shell
    kubectl rollout status deployment/nginx-deployment
    ```

    이와 유사하게 출력된다.

    ```
    Waiting for rollout to finish: 1 out of 3 new replicas have been updated...
    ```

- Ctrl-C 를 눌러 위의 롤아웃 상태 보기를 중지한다. 고착된 롤아웃 상태에 대한 자세한 정보는 [이 것을 더 읽어본다](https://kubernetes.io/ko/docs/concepts/workloads/controllers/deployment/#디플로이먼트-상태).

- 이전 레플리카는 2개(`nginx-deployment-1564180365` 과 `nginx-deployment-2035384211`), 새 레플리카는 1개(nginx-deployment-3066724191)임을 알 수 있다.

    ```shell
    kubectl get rs
    ```

    이와 유사하게 출력된다.

    ```
    NAME                          DESIRED   CURRENT   READY   AGE
    nginx-deployment-1564180365   3         3         3       25s
    nginx-deployment-2035384211   0         0         0       36s
    nginx-deployment-3066724191   1         1         0       6s
    ```

- 생성된 파드를 보면, 새로운 레플리카셋에 생성된 1개의 파드가 이미지 풀 루프(pull loop)에서 고착된 것을 볼 수 있다.

    ```shell
    kubectl get pods
    ```

    이와 유사하게 출력된다.

    ```
    NAME                                READY     STATUS             RESTARTS   AGE
    nginx-deployment-1564180365-70iae   1/1       Running            0          25s
    nginx-deployment-1564180365-jbqqo   1/1       Running            0          25s
    nginx-deployment-1564180365-hysrc   1/1       Running            0          25s
    nginx-deployment-3066724191-08mng   0/1       ImagePullBackOff   0          6s
    ```

    **참고:** 디플로이먼트 컨트롤러가 잘못된 롤아웃을 자동으로 중지하고, 새로운 레플리카셋의 스케일 업을 중지한다. 이는 지정한 롤링 업데이트의 파라미터(구체적으로 `maxUnavailable`)에 따라 달라진다. 쿠버네티스는 기본값으로 25%를 설정한다.

- 디플로이먼트에 대한 설명 보기

    ```shell
    kubectl describe deployment
    ```

    이와 유사하게 출력된다.

    ```
    Name:           nginx-deployment
    Namespace:      default
    CreationTimestamp:  Tue, 15 Mar 2016 14:48:04 -0700
    Labels:         app=nginx
    Selector:       app=nginx
    Replicas:       3 desired | 1 updated | 4 total | 3 available | 1 unavailable
    StrategyType:       RollingUpdate
    MinReadySeconds:    0
    RollingUpdateStrategy:  25% max unavailable, 25% max surge
    Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.161
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    ReplicaSetUpdated
    OldReplicaSets:     nginx-deployment-1564180365 (3/3 replicas created)
    NewReplicaSet:      nginx-deployment-3066724191 (1/1 replicas created)
    Events:
      FirstSeen LastSeen    Count   From                    SubObjectPath   Type        Reason              Message
      --------- --------    -----   ----                    -------------   --------    ------              -------
      1m        1m          1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled up replica set nginx-deployment-2035384211 to 3
      22s       22s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled up replica set nginx-deployment-1564180365 to 1
      22s       22s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled down replica set nginx-deployment-2035384211 to 2
      22s       22s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled up replica set nginx-deployment-1564180365 to 2
      21s       21s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled down replica set nginx-deployment-2035384211 to 1
      21s       21s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled up replica set nginx-deployment-1564180365 to 3
      13s       13s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled down replica set nginx-deployment-2035384211 to 0
      13s       13s         1       {deployment-controller }                Normal      ScalingReplicaSet   Scaled up replica set nginx-deployment-3066724191 to 1
    ```

    이 문제를 해결하려면 디플로이먼트를 안정적인 이전 수정 버전으로 롤백해야 한다.

#### 디플로이먼트의 롤아웃 기록 확인

다음 순서에 따라 롤아웃 기록을 확인한다.

1. 먼저 이 디플로이먼트의 수정 사항을 확인한다.

    ```shell
    kubectl rollout history deployment/nginx-deployment
    ```

    이와 유사하게 출력된다.

    ```
    deployments "nginx-deployment"
    REVISION    CHANGE-CAUSE
    1           kubectl apply --filename=https://k8s.io/examples/controllers/nginx-deployment.yaml
    2           kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
    3           kubectl set image deployment/nginx-deployment nginx=nginx:1.161
    ```

    `CHANGE-CAUSE` 는 수정 생성시 디플로이먼트 주석인 `kubernetes.io/change-cause` 에서 복사한다. 다음에 대해 `CHANGE-CAUSE` 메시지를 지정할 수 있다.

    - 디플로이먼트에 `kubectl annotate deployment/nginx-deployment kubernetes.io/change-cause="image updated to 1.16.1"` 로 주석을 단다.
    - 수동으로 리소스 매니페스트 편집.

2. 각 수정 버전의 세부 정보를 보려면 다음을 실행한다.

    ```shell
    kubectl rollout history deployment/nginx-deployment --revision=2
    ```

    이와 유사하게 출력된다.

    ```
    deployments "nginx-deployment" revision 2
      Labels:       app=nginx
              pod-template-hash=1159050644
      Annotations:  kubernetes.io/change-cause=kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
      Containers:
       nginx:
        Image:      nginx:1.16.1
        Port:       80/TCP
         QoS Tier:
            cpu:      BestEffort
            memory:   BestEffort
        Environment Variables:      <none>
      No volumes.
    ```

#### 이전 수정 버전으로 롤백

다음 단계에 따라 디플로이먼트를 현재 버전에서 이전 버전인 버전 2로 롤백한다.

1. 이제 현재 롤아웃의 실행 취소 및 이전 수정 버전으로 롤백 하기로 결정했다.

    ```shell
    kubectl rollout undo deployment/nginx-deployment
    ```

    이와 유사하게 출력된다.

    ```
    deployment.apps/nginx-deployment rolled back
    ```

    또는 특정 수정 버전으로 롤백하려면 `--to-revision` 옵션에 해당 수정 버전을 명시한다.

    ```shell
    kubectl rollout undo deployment/nginx-deployment --to-revision=2
    ```

    이와 유사하게 출력된다.

    ```
    deployment.apps/nginx-deployment rolled back
    ```

    롤아웃 관련 명령에 대한 자세한 내용은 [`kubectl rollout`](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#rollout)을 참조한다.

    이제 디플로이먼트가 이전 안정 수정 버전으로 롤백 된다. 버전 2로 롤백하기 위해 `DeploymentRollback` 이벤트가 디플로이먼트 컨트롤러에서 생성되는 것을 볼 수 있다.

2. 만약 롤백에 성공하고, 디플로이먼트가 예상대로 실행되는지 확인하려면 다음을 실행한다.

    ```shell
    kubectl get deployment nginx-deployment
    ```

    이와 유사하게 출력된다.

    ```
    NAME               READY   UP-TO-DATE   AVAILABLE   AGE
    nginx-deployment   3/3     3            3           30m
    ```

3. 디플로이먼트의 설명 가져오기.

    ```shell
    kubectl describe deployment nginx-deployment
    ```

    이와 유사하게 출력된다.

    ```
    Name:                   nginx-deployment
    Namespace:              default
    CreationTimestamp:      Sun, 02 Sep 2018 18:17:55 -0500
    Labels:                 app=nginx
    Annotations:            deployment.kubernetes.io/revision=4
                            kubernetes.io/change-cause=kubectl set image deployment/nginx-deployment nginx=nginx:1.16.1
    Selector:               app=nginx
    Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
    StrategyType:           RollingUpdate
    MinReadySeconds:        0
    RollingUpdateStrategy:  25% max unavailable, 25% max surge
    Pod Template:
      Labels:  app=nginx
      Containers:
       nginx:
        Image:        nginx:1.16.1
        Port:         80/TCP
        Host Port:    0/TCP
        Environment:  <none>
        Mounts:       <none>
      Volumes:        <none>
    Conditions:
      Type           Status  Reason
      ----           ------  ------
      Available      True    MinimumReplicasAvailable
      Progressing    True    NewReplicaSetAvailable
    OldReplicaSets:  <none>
    NewReplicaSet:   nginx-deployment-c4747d96c (3/3 replicas created)
    Events:
      Type    Reason              Age   From                   Message
      ----    ------              ----  ----                   -------
      Normal  ScalingReplicaSet   12m   deployment-controller  Scaled up replica set nginx-deployment-75675f5897 to 3
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled up replica set nginx-deployment-c4747d96c to 1
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled down replica set nginx-deployment-75675f5897 to 2
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled up replica set nginx-deployment-c4747d96c to 2
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled down replica set nginx-deployment-75675f5897 to 1
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled up replica set nginx-deployment-c4747d96c to 3
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled down replica set nginx-deployment-75675f5897 to 0
      Normal  ScalingReplicaSet   11m   deployment-controller  Scaled up replica set nginx-deployment-595696685f to 1
      Normal  DeploymentRollback  15s   deployment-controller  Rolled back deployment "nginx-deployment" to revision 2
      Normal  ScalingReplicaSet   15s   deployment-controller  Scaled down replica set nginx-deployment-595696685f to 0
    ```





