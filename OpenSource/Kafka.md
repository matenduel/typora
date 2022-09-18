[TOC]



# 1. Introduction

## 1.1. Kafka 란?

![img](https://velog.velcdn.com/images%2Fbusybean3%2Fpost%2Ff7e03a74-6014-4538-9792-3eb0cb29c297%2Fimage.png)

> **Apache Kafka**는 빠르고 확장 가능한 작업을 위해 데이터 피드의 분산 스트리밍, 파이프 라이닝 및 재생을 위한 실시간 스트리밍 데이터를 처리하기 위한 목적으로 설계된 오픈 소스 분산형 게시-구독 메시징 플랫폼입니다.
>
> Kafka는 서버 클러스터 내에서 데이터 스트림을 레코드로 유지하는 방식으로 작동하는 브로커 기반 솔루션입니다. Kafka 서버는 여러 데이터 센터에 분산되어 있을 수 있으며 여러 서버 인스턴스에 걸쳐 레코드 스트림(메시지)을 토픽으로 저장하여 데이터 지속성을 제공할 수 있습니다. 토픽은 레코드 또는 메시지를 키, 값 및 타임 스탬프로 구성된 일련의 튜플, 변경 불가능한 Python 객체 시퀀스로 저장합니다.
>
> https://www.tibco.com/ko/reference-center/what-is-apache-kafka





## 1.2. 특징

* Pull 방식

`Consumer`가 `Broker`로부터 메세지를 가져가는(**Pull**) 형태이므로 Consumer의 처리능력만큼 메세지를 가져가므로 최적의 성능을 발휘할 수 있다. 



* 파일 시스템 기반

`Consumer`가 메세지를 처리하더라도 정해진 **보관 주기** 동안 디스크에 메시지를 저장하므로 안정적이며, 차후 장애등으로 문제가 생긴 경우에도 특정 시점부터 메세지를 다시 사용할 수 있다. 



* Commit && Offset

* Commit && Offset

기존 메시징 시스템의 경우, `Message`를 소비하는 경우 즉시 큐에서 삭제되므로  `Queue`를 하나의 Consumer만 활용할 수 있다. 

`Kafka`의 경우, `Message`를 `Consumer`가 **Commit**하는 경우에만 소비한 것으로 취급한다.

또한,  동일한 `Topic`에 대해 각 `Consumer Group` 마다 **Offset**정보를 저장하고 있으므로 하나의 `Topic`에 대해 여러 `Consumer`가 존재할 수 있다. 



## 1.3. 장점과 단점



Apache Kafka는 만족을 위해 구성한 설계 목표로 인해 몇 가지 중요한 이점이 있습니다. Apache Kafka는 다음과 같은 세 가지 주요 요구 사항을 염두에 두고 설계되었습니다.

- 데이터 분배 및 이용을 위한 게시/구독 메시징 모델 제공
- 오랜 시간에 걸쳐 액세스하고 재생할 수 있는 데이터의 장기 저장 허용
- 실시간 스트림 처리를 위해 실시간으로 데이터에 액세스하는 기능 지원

이것이 Apache Kafka가 진정으로 빛을 발하는 곳입니다. 일부 메시징 시스템과 달리 트랜잭션 또는 다른 배포 모델에 대한 매력적인 부가 기능을 제공하지는 않습니다. 스트림 처리를 지원하는 게시/구독 모델에 대한 데이터 분배를 제공하는 데 중점을 둡니다.

둘째, 처음부터 장기간 데이터 저장 및 데이터 재생을 제공하도록 설계되었으므로 Apache Kafka는 데이터 영속성, 내결함성 및 재생에 고유하게 접근할 수 있습니다. 이는 Apache Kafka가 클러스터에서 데이터 복제 영속성, 데이터 볼륨 및 로드 증가를 위해 파티션 간 데이터 공유, 토픽/파티션, 데이터 오프셋 및 컨슈머 그룹 이름을 사용하여 데이터 액세스를 허용함으로써 확장성을 처리하는 방법에서 알아볼 수 있습니다.

마지막으로 Apache Kafka는 원래 실시간 로그 처리를 위한 통신 계층 역할을하도록 설계되었기 때문에 자연스럽게 실시간 스트림 처리 애플리케이션에 어울립니다. 따라서 Apache Kafka는 대량의 데이터를 실시간으로 배포할 수있는 통신 인프라를 활용하는 애플리케이션에 이상적으로 적합합니다.

**원활한 메시징 및 스트리밍 기능 :** 대용량 데이터를 처리할 때 메시징은 레거시 통신 모델에 비해 통신 및 확장성에 상당한 이점을 제공할 수 있습니다. 메시징 및 스트리밍 기능을 결합함으로써 Apache Kafka는 실시간으로 레코드를 게시, 구독, 저장 및 처리하는 고유한 기능을 제공합니다.

**데이터 재생을 위한 시간 기반 데이터 보존 :** Apache Kafka는 기본적으로 클러스터 전체의 디스크에 데이터를 지속적으로 저장하는 기능을 통해 내결함성에 대한 간단한 접근 방식을 허용합니다. 시간 기반 보존 기간을 기반으로 저장된 데이터를 회수하고 순차적 오프셋을 기반으로 데이터에 액세스하는 기능과 함께 Apache Kafka는 클러스터 설정에서 데이터 저장 및 검색에 대한 강력한 접근 방식을 제공합니다.

**스트림 처리를 위한 기본 접근 방식 :** 데이터를 빠르고 효율적으로 이동할 수 있게 하는 것이 상호 연결성의 핵심입니다. Apache Kafka는 데이터를 레코드, 메시지 또는 스트림으로 원활하게 이동할 수 있는 기반을 제공합니다. 데이터를 검사, 변환 및 활용하려면 데이터를 실시간으로 다른 위치로 이동할 수 있는 기능이 필요하며 Apache Kafka는 실시간으로 데이터를 이동하고 저장하기위한 기본 접근 방식을 제공합니다.

**기본 통합 지원 :** 모든 것에 적용하는 것은 결코 좋은 접근 방식이 아니며 Apache Kafka는 Connector API를 사용하여 기본 통합 지점을 제공함으로써 확장 및 성장할 수 있는 기본 기능을 제공합니다. Apache Kafka Connector API를 사용하여 애플리케이션은 사전 구축된 커넥터 또는 오픈 소스 도구를 통해 타사 솔루션, 기타 메시징 시스템 및 레거시 애플리케이션과 통합하거나 애플리케이션 요구 사항에 따라 의도적으로 커넥터를 구축할 수 있습니다.

-> 정리 필요 



## 1.4. Rabbit MQ, Pulsar 와 비교







# 2. Installation

## 2.1. Kubernetes

### 2.1.1. Confluent ([helm Chart](https://github.com/confluentinc/cp-helm-charts))



### 2.1.2. Bitnami ([helm Chart](https://bitnami.com/stack/kafka/helm))



## 2.2. Docker Compose



## 2.3. MSK





# 3. 용어 정리

![img](https://velog.velcdn.com/images%2Fhyeondev%2Fpost%2Fe1919472-63ec-4846-8230-abce247e5489%2Fimg%20(1).png)

## 3.1. Broker

`Kafka` 어플리케이션이 설치되어 있는 서버 또는 노드를 의미한다. 

## 3.2. Zookeeper

분산 어플리케이션을 위한 코디네이션 시스템이다. 분산 어플리케이션이 안정적인 서비스를 할 수 있도록 분산되어 있는 각 어플리케이션의 정보를 중앙에 집중하고 구성 관리, 그룹 네이밍, 동기화 등의 서비스를 제공한다. 

`Leader`역할을 수행하던 서버에 장애가 발생한 경우 과반수 선출(majority voting/quorums)를 통해 다음 `Leader`를 선출한다. 이 때, 남아있는 서버의 갯수가 과반수를 넘지 못하는 경우 서비스가 불가능하며, 서버의 갯수가 짝수로 구성하든, 홀수로 구성하든 결함 허용 수준이 동일하므로 `Zookeeper`의 서버 갯수는 홀수로 설정한다. 

Ex) 4대로 구성 -> 서버 2개가 고장난 경우 잔여 서버(2대)가 과반수(2대)를 넘지 못하므로 장애 

Ex) 3대로 구성 -> 서버 2개가 고장난 경우 잔여 서버(1대)가 과반수(2대)를 넘지 못하므로 장애

## 3.3. Topic

`Message`를 구분하는 단위이자 데이터를 구분하기 위한 추상화 계층

`Topic` 이름은 249자 미만으로 영문, 숫자, `.`, `_`, `-`를 조합하여 만들 수 있다. 

`Topic`내에 `Partition`이 여러개인 경우, 별도로 `Partition`번호를 지정하지 않는다면 `Round-Robin`방식을 통해 메세지가 할당된다. 



## 3.4. Producer

`Message`를 발행하여 생산하는 주체

Apache Kafka에서 프로듀서 개념은 대부분의 메시징 시스템과 다르지 않습니다. 데이터(레코드/메시지) 프로듀서는 주어진 레코드/메시지가 게시되어야 하는 토픽(데이터 스트림)를 정의합니다. 파티션은 추가 확장성을 제공하는 데 사용되므로 프로듀서는 주어진 레코드/메시지가 게시되는 파티션도 정의할 수 있습니다. 프로듀서는 주어진 파티션을 정의할 필요가 없으며 파티션을 정의하지 않음으로써 토픽 파티션에서 순차 순환 대기 방식의 로드 밸런싱을 달성할 수 있습니다.

## 3.5. Consumer

`Message`를 구독하여 소비하 는 주체

대부분의 메시징 시스템과 마찬가지로 Kafka의 컨슈머는 레코드/메시지를 처리하는 엔터티입니다. 컨슈머는 개별 워크로드에서 독립적으로 작업하거나 지정된 워크로드에서 다른 컨슈머와 협력하여 작업하도록 구성할 수 있습니다(로드 밸런싱). 컨슈머는 컨슈머 그룹 이름을 기반으로 워크로드를 처리하는 방법을 관리합니다. 컨슈머 그룹 이름을 사용하면 컨슈머를 단일 프로세스 내, 여러 프로세스, 심지어 여러 시스템에 분산시킬 수 있습니다. 컨슈머 그룹 이름을 사용하여 컨슈머는 컨슈머 집합 전체에서 레코드/메시지 소비를 로드 밸런싱(동일한 컨슈머 그룹 이름을 가진 여러 컨슈머)하거나 토픽/파티션을 구독하는 각 컨슈머가 처리 메시지를 받도록 각 레코드/메시지를 고유하게 (고유한 컨슈머 그룹 이름을 가진 여러 컨슈머) 처리할 수 있습니다.



## 3.6. Consumer Group

`Consumer Group` 마다 Offset 정보가 따로 관리되므로, 하나의 `Topic`에 여러개의 그룹이 동시에 접속하여 메세지를 가져올 수 있으며 서로에게 영향을 주지 않는다. 

`Consumer Group`내의 `Consumer`들은 메세지를 가져오고 있는 `Topic`의 파티션에 대해 소유권을 공유한다. 

`Consumer`가 추가 또는 삭제되는 경우, 기존에 할당된 파티션의 소유권이 자동으로 재조정되며 이를 `rebalance`라고 한다. 단, `Consumer Group`내의 `Consumer`의 숫자는 `partition`의 갯수를 넘을 수 없으며, 초과된 수량의 `Consumer`는 파티션을 할당받지 못한다. 



## 3.7. Partition

![partition-and-consumer-004](http://www.popit.kr/wp-content/uploads/2016/12/partition-and-consumer.004-2-600x450.jpeg)

파티션과 `Consumer`는 **N:1** 관계이므로 , 두개 이상의 `Consumer`가 동일한 `Partition`을 공유할 수 없다. 

`Offset`을 통해 파티션 내에서는 항상 메세지의 순서가 보장된다. 하지만, `Topic`이 2개 이상의 파티션으로 구성된 경우, `Topic`내에서는 메세지의 순서가 보장되지 않는다. 

파티션의 갯수는 추가만 가능하며, 감소는 불가능하다. 따라서, 파티션의 갯수를 줄이고 싶은 경우 `Topic`을 삭제한 후 재생성 해야한다. 



## 3.8. Replication

`Topic`내 파티션의 복제본을 의미하며, `Topic`별로 다르게 설정할 수 있다. 

`replication factor` 만큼 파티션이 생성되므로 복제본은 `replication factor - 1`만큼 생긴다.  



## 3.9. Leader && Followers

`Topic`의 모든 Read/Write는 Leader에서 이루어진다. 

`Follower`는 `Leader`의 복제본을 의미하며, Leader와 동일한 상태값 또는 데이터를 가지고 있을 경우 ISR(In Sync Replica)로 설정되며, `Leader`가 다운된 경우 ISR 중에서 다음 `Leader`르를 선출하게 된다 



### 3.10. Offset

## 3.10. Offset

파티션내 각 메세지의 저장된 상대적 위치를 의미한다. 

새로운 메세지가 발행되는 경우 파티션의 맨 뒤에 추가되며, `Consumer`는 `Offset`을 기준으로 마지막 커밋 시점부터 순서대로 처리한다. 







# 4. Config

> [공식문서](https://kafka.apache.org/31/documentation.html#configuration)에 있는 Configuration을 기준으로 설명되었으며, helm-chart 또는 Docker Image 별로 이름이 다를 수 있습니다. 

## 4.1. [Broker](https://kafka.apache.org/31/documentation.html#brokerconfigs)

* #### auto.create.topics.enable

    * Enable auto creation of topic on the server

        |         Type: | boolean   |
        | ------------: | --------- |
        |      Default: | true      |
        | Valid Values: |           |
        |   Importance: | high      |
        |  Update Mode: | read-only |

* #### auto.leader.rebalance.enable

    * Enables auto leader balancing. A background thread checks the distribution of partition leaders at regular intervals, configurable by `leader.imbalance.check.interval.seconds`. If the leader imbalance exceeds `leader.imbalance.per.broker.percentage`, leader rebalance to the preferred leader for partitions is triggered.

        |         Type: | boolean   |
        | ------------: | --------- |
        |      Default: | true      |
        | Valid Values: |           |
        |   Importance: | high      |
        |  Update Mode: | read-only |

* #### compression.type

    * Specify the final compression type for a given topic. This configuration accepts the standard compression codecs ('gzip', 'snappy', 'lz4', 'zstd'). It additionally accepts 'uncompressed' which is equivalent to no compression; and 'producer' which means retain the original compression codec set by the producer.

        |         Type: | string       |
        | ------------: | ------------ |
        |      Default: | producer     |
        | Valid Values: |              |
        |   Importance: | high         |
        |  Update Mode: | cluster-wide |

* #### Delete.topic.enable

    * Enables delete topic. Delete topic through the admin tool will have no effect if this config is turned off

        |         Type: | boolean   |
        | ------------: | --------- |
        |      Default: | true      |
        | Valid Values: |           |
        |   Importance: | high      |
        |  Update Mode: | read-only |

* #### Log.dirs

    * The directories in which the log data is kept. If not set, the value in log.dir is used

        | Type:         | string    |
        | :------------ | --------- |
        | Default:      | null      |
        | Valid Values: |           |
        | Importance:   | high      |
        | Update Mode:  | read-only |

* #### message.max.bytes

    * The largest record batch size allowed by Kafka (after compression if compression is enabled). If this is increased and there are consumers older than 0.10.2, the consumers' fetch size must also be increased so that they can fetch record batches this large. In the latest message format version, records are always grouped into batches for efficiency. In previous message format versions, uncompressed records are not grouped into batches and this limit only applies to a single record in that case.This can be set per topic with the topic level `max.message.bytes` config.

        | Type:         | int          |
        | :------------ | ------------ |
        | Default:      | 1048588      |
        | Valid Values: | [0,...]      |
        | Importance:   | high         |
        | Update Mode:  | cluster-wide |

* #### default.replication.factor

    * The default replication factors for automatically created topics

        | Type:         | int       |
        | :------------ | --------- |
        | Default:      | 1         |
        | Valid Values: |           |
        | Importance:   | medium    |
        | Update Mode:  | read-only |

* #### num.partitions

    * The default number of log partitions per topic

        | Type:         | int       |
        | :------------ | --------- |
        | Default:      | 1         |
        | Valid Values: | [1,...]   |
        | Importance:   | medium    |
        | Update Mode:  | read-only |

## 4.2. Topic

## 4.3. Producer

## 4.4. Consumer

## 4.5. Admin Client



# 5. Body

## 5.1. batching

![img](https://miro.medium.com/max/1155/0*qKEVr3lpoCGWlgsY.png)

### 목적 

**Producer**의 네트워크 요청 횟수를 줄여 **Kafka**의 처리량을 높인다. 

하지만, 네트워크 요청 횟수를 줄인 만큼 **지연시간은 증가**하므로 지연시간과 처리량 사이에서 적절한 균형점을 찾아야한다. 



### 관련 Configuration

1. #### batch.size

    - 메세지의 묶음이 임계값을 넘는 순간 메시지를 발행한다. 
    - 메세지의 사이즈가 임계값보다 작더라도 다른 조건(`linger.ms`, ...)에 의해 발행될 수 있다. 

2. #### linger.ms

    - 메세지가 `batch.size`를 넘을 때까지 기다리는 최대 시간
    - 값이 0으로 설정된 경우, 메세지를 즉시 발행한다.



## 5.2. request quota





## 5.3. timeout?





# 6. CLI

> bitnamin/kafka → `opt/bitnami/kafka/bin/`

## 6.1. Topic

### 목록

```shell
kafka-topics.sh --bootstrap-server localhost:9092 --list
```

### 정보 보기

```shell
kafka-topics.sh --bootstrap-server localhost:9092 --topic <topic_name> --describe
```

### 생성하기

```shell
kafka-topics.sh --bootstrap-server localhost:9092 --replication-factor 1 --partitions 1 --topic my_topic --create 
```

### 제거하기

```shell
kafka-topics.sh --bootstrap-server localhost:9092 --topic <topic_name> --delete
```



## 6.2. Producer

### Message 생성하기

```shell
kafka-console-producer.sh --broker-list localhost:9092 --topic <topic_name>
```

 

## 6.3. Consumer

### Message 소비하기

```shell
kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --topic <topic_name>
```

### 특정 Partition의 Message 소비하기

```shell
kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --partition <partition_number> --topic <topic_name>
```

### 특정 Consumer Group의 Message 소비하기

```shell
kafka-console-consumer.sh --bootstrap-server localhost:9092 --from-beginning --group group_name --topic <topic_name>
```

 

## 6.4. Consumer Group

### Consumer Group List

```shell
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --list
```

### 상태

```shell
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group <group_name> --describe
```

### 삭제하기

```shell
kafka-consumer-groups.sh --bootstrap-server localhost:9092 --group <group_name> --delete
```





# 



# 7. Kafka Connect

![img](https://blog.kakaocdn.net/dn/cF4X3U/btrhVsFQhBK/uMAETh8q7SIPKPBdVjcfz0/img.png)

## 7.1. Connector 란?



### 7.1.1. Sink Connector

* Consumer 역할
* topic에 담긴 데이터를 특정 data source로 보내는 역할을 하는 connector

### 7.1.2. Source Connector

* Producer 역할
* data source에 담긴 데이터를 topic에 담는 역할을 하는 connector
    * op log (bin log)를 활용하여 모든 operation에 대해 Message를 발행
    * 또는, Query를 사용하여 id, timestamp를 사용하여 Insert, Update에 대해 Message를 발행



## 7.3. 지원 목록

> Confluent ([링크](https://www.confluent.io/product/confluent-connectors/?utm_medium=sem&utm_source=google&utm_campaign=ch.sem_br.nonbrand_tp.prs_tgt.kafka-connectors_mt.mbm_rgn.apac_lng.eng_dv.all_con.kafka-connectors&utm_term=%2Bkafka%20%2Bconnector&creative=&device=c&placement=&gclid=CjwKCAjw5s6WBhA4EiwACGncZSdMmVKLpOkwwHaaAI9amznnuhigrv1fZ6PWJyCt2cOD-yKcOEBx9BoCQQ4QAvD_BwE))
>
> Confluent Hub ([링크](https://www.confluent.io/hub/))

- MySQL
- MongoDB
- Amazon S3
- Redis
- Elasticsearch
- PostgreSQL



# 8. Schema Registry





# 9. Kafka Streams





# 10. KSQL DB







# 11. Monitoring & Dashboard

## 11.1. Prometheus & grafana



## 11.2. UI for Kafka





# 기타

## Production에 배포시 주의 사항

* 보안

## Local Test 방법 (Kubernetes + Internal LB)

1. Telepresence를 사용하여 Cluster에 연결
    1. [Telepresence 설치 및 사용법](https://balaan-dev.atlassian.net/wiki/spaces/DDAT/pages/289472556) 
2. Internal LB 주소를 사용하여 테스트 





# Trouble Shooting





# Reference

1. https://kafka.apache.org/31/documentation.html
2. https://www.popit.kr/kafka-%EC%9A%B4%EC%98%81%EC%9E%90%EA%B0%80-%EB%A7%90%ED%95%98%EB%8A%94-topic-replication/
3. https://www.tibco.com/ko/reference-center/what-is-apache-kafka
4. 