

# 1. Introduction

## 1.1. KEDA 란?

![keda-arch](./KEDA.assets/keda-arch.png)

**KEDA** is a [Kubernetes](https://kubernetes.io/)-based Event Driven Autoscaler. With KEDA, you can drive the scaling of any container in Kubernetes based on the number of events needing to be processed.

**KEDA** is a single-purpose and lightweight component that can be added into any Kubernetes cluster. KEDA works alongside standard Kubernetes components like the [Horizontal Pod Autoscaler](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) and can extend functionality without overwriting or duplication. With KEDA you can explicitly map the apps you want to use event-driven scale, with other apps continuing to function. This makes KEDA a flexible and safe option to run alongside any number of any other Kubernetes applications or frameworks.



## 1.2. 사용이유

* Kubernetes에서 제공하는 AutoScaling은 CPU, RAM을 기반으로 작동한다. 하지만, 일반적으로 부하가 발생할 시 CPU나 RAM 사용량이 변하지 않는 경우도 존재하며, Resource 사용량만으로 스케일링 하는 것은 사후작업이므로 반응이 늦을 수 있음
* KEDA를 이용할 경우, Cron이나 외부 Metrics을 사용하여 사전에 Autosacling 작업을 진행할 수 있다.
* 또한, API 호출량, Message 발행량 등 다양한 Metric을 사용하여 해당 서비스를 운영함에 있어 가장 적합한 Metric을 사용할 수 있다. 



## 1.2. Pros & Cons

* 외부 Trigger를 사용할 수 있으므로 Scale to Zero가 가능하다.
* 각 서비스 또는 서버에 맞는 최적화된 Metrics를 사용할 수 있다.
* 설계 또는 Trigger 종류에 따라서 부하가 발생하기 전에 Scaling을 진행할 수 있다.  





# 2. Installation

## 2.1. Kubernetes ([Document](https://keda.sh/docs/2.8/deploy/#helm))

1. Add Helm repo

    ```sh
    helm repo add kedacore https://kedacore.github.io/charts
    ```

2. Update Helm repo

    ```sh
    helm repo update
    ```



# 3. Scaler List

> https://keda.sh/docs/2.8/scalers/

* Apache Kafka
* Prometheus
* MySQL
* MongoDB
* PostgreSQL
* Cron
* Elasticsearch
* ...



## 3.1. Apache Kafka

#### 주의사항

- Kafka 특성상, Topic의 Partition 수보다 `consumer` 가 더 많은 경우 일부 `consumer`는 Idle(유휴) 상태로 대기한다. 



#### Example

```yaml
triggers:
- type: kafka
  metadata:
    bootstrapServers: "bk0.balaan.dev:9094,bk1.balaan.dev:9094,bk2.balaan.dev:9094"
    consumerGroup: my-group
    topic: test-topic
    lagThreshold: '5'
    activationLagThreshold: '3'
    offsetResetPolicy: latest
    allowIdleConsumers: false
    scaleToZeroOnInvalidOffset: false
    version: 1.0.0
```



#### Properties

- `bootstrapServers`
    - String 타입
    - `,(comma)`로 구분된 Kafka broker 주소
        - Format: `hostname:port`
- `consumerGroup` 
    - Consumer Group 이름
- `topic`  - Optional
    - Topic 이름
    - `topic`을 지정하지 않은 경우, 다음 조건에 해당되는 모든 Topic을 대상으로 Lag을 합산한다. 
        - `Active` 상태의 consumer가 존재하는 경우
            - 현재 구독중인 Topic
            - offset 기록 (commit history)가 존재하는 Topic
        - `Active` 상태의 consumer가 존재하지 않는 경우
            - offset 기록 (commit history)가 존재하는 Topic
- `lagThreshold`  - Optional
    - 해당 값을 기준으로 Scale 양을 조정한다. 
    - String 형식으로 기입해야한다!
    - `gt - Greater than`으로 파악되므로 주의!
- `activationLagThreshold`  - Optional
    - 해당 값을 기준으로 Scale to Zero 여부를 결정한다. (gte 로 추정됨)
    - threshold보다 높은 값을 기입할 경우, Metric 값이 `activationThreshold` 보다 낮다면 서버가 시작하지 않으므로 주의할 것
    - 값이 0인 경우 항상 1개 이상의 Pod이 남아있게 된다. 
- `offsetResetPolicy`  - Optional
    - Offset이 발견되지 않은 경우 어떠한 기준으로 offset을 처리할 것인지를 결정한다. 
        - latest - 저장된 Message 중에서 가장 최신 message부터 소비
        - earliest - 저장된 Message 중에서 가장 오래된  message부터 소비
- `allowIdleConsumers`  - Optional
    - `True`로 설정한 경우, Topic의 Partition 수보다 많은 서버를 생성할 수 있도록 한다. 
- `scaleToZeroOnInvalidOffset`  - Optional
    - This parameter controls what the scaler does when a partition doesn’t have a valid offset. If ‘false’ (the default), the scaler will keep a single consumer for that partition. Otherwise (’true’), the consumers for that partition will be scaled to zero. See the [discussion](https://github.com/kedacore/keda/issues/2612) about this parameter.
- `version`  - Optional
    - Version of your Kafka brokers. See [samara](https://github.com/Shopify/sarama) version (Default: `1.0.0`, Optional)





## 3.2. Prometheus

#### Example

```yaml
triggers:
- type: prometheus
  metadata:
    # Required fields:
    serverAddress: http://prometheus-server.monitoring.svc.cluster.local
    metricName: http_requests_total # Note: name to identify the metric, generated value would be `prometheus-http_requests_total`
    query: sum(rate(http_requests_total[2m])) # Note: query must return a vector/scalar single element response
    threshold: '500.00'
    activationThreshold: '5.5'
    # Optional fields:
    namespace: example-namespace  # for namespaced queries, eg. Thanos
    cortexOrgID: my-org # Optional. X-Scope-OrgID header for Cortex.
    ignoreNullValues: false # Default is `true`, which means ignoring the empty value list from Prometheus. Set to `false` the scaler will return error when Prometheus target is lost
```



#### Properties

- `serverAddress` 
    - Prometheus 서버 주소
        - Kubernetes DNS
        - ...
- `metricName` 
    - Metric 별 고유한 이름 
    - Name to identify the Metric in the external.metrics.k8s.io API. If using more than one trigger it is required that all `metricName`(s) be unique.
- `query` 
    - 단일 값을 반환해야하므로 주의할 것!
- `threshold` 
    - 해당 값을 기준으로 Scale 양을 조정한다. 
    - String 형식으로 기입해야한다!
- `activationThreshold` 
    - 해당 값을 기준으로 Scale to Zero 여부를 결정한다. (gte 로 추정됨)
    - threshold보다 높은 값을 기입할 경우, Metric 값이 `activationThreshold` 보다 낮다면 서버가 시작하지 않으므로 주의할 것
    - 값이 0인 경우 항상 1개 이상의 Pod이 남아있게 된다. 
- `namespace` 
    - A namespace that should be used for namespaced queries. These are required by some highly available Prometheus setups, such as [Thanos](https://thanos.io/). (Optional)
- `cortexOrgID` 
    - The `X-Scope-OrgID` header to query multi tenant [Cortex](https://cortexmetrics.io/) or [Mimir](https://grafana.com/oss/mimir/). (Optional)
- `ignoreNullValues` 
    - Value to reporting error when Prometheus target is lost (Values: `true`,`false`, Default: `true`, Optional)



# 8. Monitoring

## 8.1. Prometheus

### 8.1.1. Metric 목록

1. `keda_metrics_adapter_scaler_error_totals` 
    * The total number of errors encountered for all scalers.
2. `keda_metrics_adapter_scaled_object_error_totals`
    * The number of errors that have occurred for each scaled object.
3. `keda_metrics_adapter_scaler_errors` 
    * The number of errors that have occurred for each scaler.
4. `keda_metrics_adapter_scaler_metrics_value`
    * The current value for each scaler’s metric that would be used by the HPA in computing the target average.





# 9. TIP & 주의사항

- 여러개의 Trigger를 사용하는 경우 `AND`가 아니라 `OR`로 작동한다. 
- HPA를 통해 `Pod`이  AutoScaling되므로 Deployment의 `replicas`가 2 이상이더라도 Pod이 지정된 갯수만 큼 증가한다. 
    - Deployments가 증가하는 것이 아니다!
- 당연하게도 scaledObject의 이름은 Unique해야한다. 