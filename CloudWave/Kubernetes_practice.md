# Kubernetes

- 선언적
- 



# 1. Install

## Kubectl

> https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-linux/
>
> https://kubernetes.io/ko/docs/tasks/tools/install-kubectl-windows/

- `Kubernetes` 클러스터를 제어하기 위한 `CLI`
- `Kubernetes API`를 이용하여 클러스터의 `Control Plane`과 통신합니다. 



### Kubens & Kubectx

> https://github.com/ahmetb/kubectx

#### Kubectl Plugins (macOS and Linux)

You can install and use the [Krew](https://github.com/kubernetes-sigs/krew/) kubectl plugin manager to get `kubectx` and `kubens`.

**Note:** This will not install the shell completion scripts. If you want them, *choose another installation method or install the scripts [manually](https://github.com/ahmetb/kubectx?tab=readme-ov-file#manual-installation-macos-and-linux).

```
kubectl krew install ctx
kubectl krew install ns
```



After installing, the tools will be available as `kubectl ctx` and `kubectl ns`.

#### Homebrew (macOS and Linux)

If you use [Homebrew](https://brew.sh/) you can install like this:

```
brew install kubectx
```



This command will set up bash/zsh/fish completion scripts automatically. Make sure you [configure your shell](https://docs.brew.sh/Shell-Completion) to load completions for installed Homebrew formulas.



#### Windows Installation (using Chocolatey)

Available as packages on [Chocolatey](https://chocolatey.org/why-chocolatey)

```
choco install kubens kubectx
```



## OpenLens

> `Kubernetes` 관리를 위한 GUI 툴

- `Lens`의 오픈소스 버전



**Homebrew**

```
brew install --cask openlens
```



**Window**

```cmd
$ choco install -y openlens
```





# 2. Architecture

### Data Plane & Control Plane

> https://kubernetes.io/docs/concepts/architecture/control-plane-node-communication/





### Pod, Node, Cluster



### Controller

> https://kubernetes.io/docs/concepts/architecture/controller/

https://kubernetes.io/docs/concepts/architecture/cloud-controller/



### Namespace

> https://kubernetes.io/ko/docs/concepts/overview/working-with-objects/namespaces/





### Components





# CLI

> https://kubernetes.io/ko/docs/reference/kubectl/cheatsheet/#%EC%98%A4%EB%B8%8C%EC%A0%9D%ED%8A%B8-%EC%83%9D%EC%84%B1



```
kubectl apply -f https://k8s.io/examples/pods/simple-pod.yaml
```



```
kubectl destroy -f https://k8s.io/examples/pods/simple-pod.yaml
```







# 3. YAML

### Label

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: label-demo
  labels:
    environment: production
    app: nginx
spec:
  containers:
  - name: nginx
    image: nginx:1.14.2
    ports:
    - containerPort: 80
```



### Selector

```yaml
selector:
  matchLabels:
    component: redis
  matchExpressions:
    - { key: tier, operator: In, values: [cache] }
    - { key: environment, operator: NotIn, values: [dev] }
```







### Annotation

> 쿠버네티스 어노테이션을 사용하여 임의의 비-식별 메타데이터를 오브젝트에 첨부할 수 있다. 도구 및 라이브러리와 같은 클라이언트는 이 메타데이터를 검색할 수 있다

- 필드는 선언적 구성 계층에 의해 관리된다. 이러한 필드를 어노테이션으로 첨부하는 것은 클라이언트 또는 서버가 설정한 기본 값, 자동 생성된 필드, 그리고 오토사이징 또는 오토스케일링 시스템에 의해 설정된 필드와 구분된다.
- 빌드, 릴리스, 또는 타임 스탬프, 릴리스 ID, git 브랜치, PR 번호, 이미지 해시 및 레지스트리 주소와 같은 이미지 정보.
- 로깅, 모니터링, 분석 또는 감사 리포지터리에 대한 포인터.
- 디버깅 목적으로 사용될 수 있는 클라이언트 라이브러리 또는 도구 정보: 예를 들면, 이름, 버전, 그리고 빌드 정보.
- 다른 생태계 구성 요소의 관련 오브젝트 URL과 같은 사용자 또는 도구/시스템 출처 정보.
- 경량 롤아웃 도구 메타데이터. 예: 구성 또는 체크포인트
- 책임자의 전화번호 또는 호출기 번호, 또는 팀 웹 사이트 같은 해당 정보를 찾을 수 있는 디렉터리 진입점.
- 행동을 수정하거나 비표준 기능을 수행하기 위한 최종 사용자의 지시 사항.



# 4. Pod

하나 이상의 [컨테이너](https://kubernetes.io/ko/docs/concepts/containers/)의 그룹이다. 이 그룹은 스토리지 및 네트워크를 공유하고, 해당 컨테이너를 구동하는 방식에 대한 명세를 갖는다. 파드의 콘텐츠는 항상 함께 배치되고, 함께 스케줄되며, 공유 콘텍스트에서 실행된다. 



### 파드 스토리지

파드는 공유 스토리지 [볼륨](https://kubernetes.io/ko/docs/concepts/storage/volumes/)의 집합을 지정할 수 있다. 파드의 모든 컨테이너는 공유 볼륨에 접근할 수 있으므로, 해당 컨테이너가 데이터를 공유할 수 있다. 또한 볼륨은 내부 컨테이너 중 하나를 다시 시작해야 하는 경우 파드의 영구 데이터를 유지하도록 허용한다. 쿠버네티스가 공유 스토리지를 구현하고 파드에서 사용할 수 있도록 하는 방법에 대한 자세한 내용은 [스토리지](https://kubernetes.io/ko/docs/concepts/storage/)를 참고한다.

### 파드 네트워킹

각 파드에는 각 주소 패밀리에 대해 고유한 IP 주소가 할당된다. 파드의 모든 컨테이너는 네트워크 네임스페이스를 공유하며, 여기에는 IP 주소와 네트워크 포트가 포함된다. 파드 내부(이 경우에 **만** 해당)에서, 파드에 속한 컨테이너는 `localhost` 를 사용하여 서로 통신할 수 있다. 파드의 컨테이너가 *파드 외부의* 엔티티와 통신할 때, 공유 네트워크 리소스(포트와 같은)를 사용하는 방법을 조정해야 한다. 파드 내에서 컨테이너는 IP 주소와 포트 공간을 공유하며, `localhost` 를 통해 서로를 찾을 수 있다. 파드의 컨테이너는 SystemV 세마포어 또는 POSIX 공유 메모리와 같은 표준 프로세스 간 통신을 사용하여 서로 통신할 수도 있다. 다른 파드의 컨테이너는 고유한 IP 주소를 가지며 특별한 구성 없이 OS 수준의 IPC로 통신할 수 없다. 다른 파드에서 실행되는 컨테이너와 상호 작용하려는 컨테이너는 IP 네트워킹을 사용하여 통신할 수 있다.

파드 내의 컨테이너는 시스템 호스트명이 파드에 대해 구성된 `name` 과 동일한 것으로 간주한다. [네트워킹](https://kubernetes.io/ko/docs/concepts/cluster-administration/networking/) 섹션에 이에 대한 자세한 내용이 있다



## YAML

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    project: cloudwave
    env: dev
  annotations:
    project.cloudwave.pod: "nginx"
spec:
  containers:
    - image: nginx:latest
      name: "nginx"
      ports:
        - containerPort: 80
```



### Probe

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



#### Liveness

Indicates whether the container is running. If the liveness probe fails, the kubelet kills the container, and the container is subjected to its [restart policy](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy). If a container does not provide a liveness probe, the default state is `Success`.



#### Readiness

```yaml
readinessProbe:
  exec:
    command:
    - cat
    - /tmp/healthy
  initialDelaySeconds: 5
  periodSeconds: 5
```

Indicates whether the container is ready to respond to requests. If the readiness probe fails, the endpoints controller removes the Pod's IP address from the endpoints of all Services that match the Pod. The default state of readiness before the initial delay is `Failure`. If a container does not provide a readiness probe, the default state is `Success`.





#### Startup

```yaml
startupProbe:
  httpGet:
    httpHeaders:
      - name: User-Agent
        value: MyUserAgent
```

Indicates whether the application within the container is started. All other probes are disabled if a startup probe is provided, until it succeeds. If the startup probe fails, the kubelet kills the container, and the container is subjected to its [restart policy](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#restart-policy). If a container does not provide a startup probe, the default state is `Success`.





## Pod phase

A Pod's `status` field is a [PodStatus](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.29/#podstatus-v1-core) object, which has a `phase` field.

The phase of a Pod is a simple, high-level summary of where the Pod is in its lifecycle. The phase is not intended to be a comprehensive rollup of observations of container or Pod state, nor is it intended to be a comprehensive state machine.

The number and meanings of Pod phase values are tightly guarded. Other than what is documented here, nothing should be assumed about Pods that have a given `phase` value.

Here are the possible values for `phase`:

| Value       | Description                                                  |
| :---------- | :----------------------------------------------------------- |
| `Pending`   | The Pod has been accepted by the Kubernetes cluster, but one or more of the containers has not been set up and made ready to run. This includes time a Pod spends waiting to be scheduled as well as the time spent downloading container images over the network. |
| `Running`   | The Pod has been bound to a node, and all of the containers have been created. At least one container is still running, or is in the process of starting or restarting. |
| `Succeeded` | All containers in the Pod have terminated in success, and will not be restarted. |
| `Failed`    | All containers in the Pod have terminated, and at least one container has terminated in failure. That is, the container either exited with non-zero status or was terminated by the system. |
| `Unknown`   | For some reason the state of the Pod could not be obtained. This phase typically occurs due to an error in communicating with the node where the Pod should be running. |



## Container states

As well as the [phase](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-phase) of the Pod overall, Kubernetes tracks the state of each container inside a Pod. You can use [container lifecycle hooks](https://kubernetes.io/docs/concepts/containers/container-lifecycle-hooks/) to trigger events to run at certain points in a container's lifecycle.

Once the [scheduler](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-scheduler/) assigns a Pod to a Node, the kubelet starts creating containers for that Pod using a [container runtime](https://kubernetes.io/docs/setup/production-environment/container-runtimes). There are three possible container states: `Waiting`, `Running`, and `Terminated`.

To check the state of a Pod's containers, you can use `kubectl describe pod <name-of-pod>`. The output shows the state for each container within that Pod.

Each state has a specific meaning:

### `Waiting`

If a container is not in either the `Running` or `Terminated` state, it is `Waiting`. A container in the `Waiting` state is still running the operations it requires in order to complete start up: for example, pulling the container image from a container image registry, or applying [Secret](https://kubernetes.io/docs/concepts/configuration/secret/) data. When you use `kubectl` to query a Pod with a container that is `Waiting`, you also see a Reason field to summarize why the container is in that state.

### `Running`

The `Running` status indicates that a container is executing without issues. If there was a `postStart` hook configured, it has already executed and finished. When you use `kubectl` to query a Pod with a container that is `Running`, you also see information about when the container entered the `Running` state.

### `Terminated`

A container in the `Terminated` state began execution and then either ran to completion or failed for some reason. When you use `kubectl` to query a Pod with a container that is `Terminated`, you see a reason, an exit code, and the start and finish time for that container's period of execution.

If a container has a `preStop` hook configured, this hook runs before the container enters the `Terminated` state.



## Container restart policy

The `spec` of a Pod has a `restartPolicy` field with possible values Always, OnFailure, and Never. The default value is Always.

The `restartPolicy` for a Pod applies to [app containers](https://kubernetes.io/docs/reference/glossary/?all=true#term-app-container) in the Pod and to regular [init containers](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/). [Sidecar containers](https://kubernetes.io/docs/concepts/workloads/pods/sidecar-containers/) ignore the Pod-level `restartPolicy` field: in Kubernetes, a sidecar is defined as an entry inside `initContainers` that has its container-level `restartPolicy` set to `Always`. For init containers that exit with an error, the kubelet restarts the init container if the Pod level `restartPolicy` is either `OnFailure` or `Always`.

When the kubelet is handling container restarts according to the configured restart policy, that only applies to restarts that make replacement containers inside the same Pod and running on the same node. After containers in a Pod exit, the kubelet restarts them with an exponential back-off delay (10s, 20s,40s, …), that is capped at five minutes. Once a container has executed for 10 minutes without any problems, the kubelet resets the restart backoff timer for that container. [Sidecar containers and Pod lifecycle](https://kubernetes.io/docs/concepts/workloads/pods/sidecar-containers/#sidecar-containers-and-pod-lifecycle) explains the behaviour of `init containers` when specify `restartpolicy` field on it.



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





## StatefulSets





## DaemonSet



## Jobs



## CronJob



## ReplicationController













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





























