## Apache Kafka의 개념([링크](https://www.tibco.com/ko/reference-center/what-is-apache-kafka))



**토픽:** 토픽은 게시/구독 메시징에서 상당히 보편적인 개념입니다. Apache Kafka 및 기타 메시징 솔루션에서 토픽은 지정된 데이터 스트림(일련의 레코드/메시지)에 대한 관심을 표시하는 데 사용되는 주소 지정 가능한 추상화입니다. 토픽은 게시 및 구독할 수 있으며 애플리케이션에서 주어진 데이터 스트림에 대한 관심을 표시하는 데 사용하는 추상화 계층입니다.

**파티션 :** Apache Kafka에서 토픽은 파티션이라는 일련의 순서 대기열로 세분화될 수 있습니다. 이러한 파티션은 연속적으로 추가되어 순차적 커밋 로그를 형성합니다. Kafka 시스템에서 각 레코드/메시지에는 지정된 파티션의 메시지 또는 레코드를 식별하는 데 사용되는 오프셋이라는 순차 ID가 할당됩니다.

**영속성:** Apache Kafka는 레코드/메시지가 게시될 때 지속적으로 유지하는 서버 클러스터를 유지 관리하여 작동합니다. Kafka 클러스터는 구성 가능한 보존 시간 제한을 사용하여 소비에 관계없이 주어진 레코드가 지속되는 기간을 결정합니다. 레코드/메시지가 보존 시간 제한 내에 있는 동안 레코드/메시지를 사용할 수 있습니다. 레코드/메시지가 이 보존 시간 제한을 초과하면 레코드/메시지가 삭제되고 공간이 확보됩니다.

**토픽/파티션 확장 :** Apache Kafka는 서버 클러스터로 작동하기 때문에 주어진 토픽/파티션에서 각 서버에 부하를 공유하여 토픽/파티션을 확장할 수 있습니다. 이 부하 공유를 통해 Kafka 클러스터의 각 서버는 주어진 토픽/파티션에 대한 레코드/메시지의 배포 및 영속성을 처리할 수 있습니다. 개별 서버가 모든 배포 및 영속성을 처리하는 동안 모든 서버는 서버가 실패할 경우 내결함성과 고가용성을 제공하는 데이터를 복제합니다. 파티션은 파티션 리더로 선택된 한개 서버와 팔로워 역할을 하는 다른 모든 서버들로 분할됩니다. 파티션 리더 인 서버는 데이터의 모든 배포 및 영속성 (읽기/쓰기)을 처리하고 팔로워 서버는 내결함성을 위한 복제 서비스를 제공합니다.

**프로듀서:** Apache Kafka에서 프로듀서 개념은 대부분의 메시징 시스템과 다르지 않습니다. 데이터(레코드/메시지) 프로듀서는 주어진 레코드/메시지가 게시되어야 하는 토픽(데이터 스트림)를 정의합니다. 파티션은 추가 확장성을 제공하는 데 사용되므로 프로듀서는 주어진 레코드/메시지가 게시되는 파티션도 정의할 수 있습니다. 프로듀서는 주어진 파티션을 정의할 필요가 없으며 파티션을 정의하지 않음으로써 토픽 파티션에서 순차 순환 대기 방식의 로드 밸런싱을 달성할 수 있습니다.

**컨슈머:** 대부분의 메시징 시스템과 마찬가지로 Kafka의 컨슈머는 레코드/메시지를 처리하는 엔터티입니다. 컨슈머는 개별 워크로드에서 독립적으로 작업하거나 지정된 워크로드에서 다른 컨슈머와 협력하여 작업하도록 구성할 수 있습니다(로드 밸런싱). 컨슈머는 컨슈머 그룹 이름을 기반으로 워크로드를 처리하는 방법을 관리합니다. 컨슈머 그룹 이름을 사용하면 컨슈머를 단일 프로세스 내, 여러 프로세스, 심지어 여러 시스템에 분산시킬 수 있습니다. 컨슈머 그룹 이름을 사용하여 컨슈머는 컨슈머 집합 전체에서 레코드/메시지 소비를 로드 밸런싱(동일한 컨슈머 그룹 이름을 가진 여러 컨슈머)하거나 토픽/파티션을 구독하는 각 컨슈머가 처리 메시지를 받도록 각 레코드/메시지를 고유하게 (고유한 컨슈머 그룹 이름을 가진 여러 컨슈머) 처리할 수 있습니다.



## 특징

1. Push가 아닌 Pull 방식
   1. consumer의 한계치만큼 작동하므로 과도하게 push를 할 경우 생기는 문제점을 방지할 수 있음
   2. 추가로 처리량이 부족한 경우 partition을 이용하여 consumer를 증가하면됨(Kafka Consumer Group)
2. 메모리 기반이 아닌 파일 시스템 기반이므로 메세지가 소비된 이후에도 일정시간 메세지 정보가 살아있음.
   1. 중간에 서비스가 다운되거나 하는 경우에도 과거 기록을 가지고 있으므로 문제가 발생한 시점부터 다시 작업 가능
   2. 상대적으로 많은 메세지가 유입되도 버틸 수 있음
3. 메세지가 consumer의 승인을 받아야 소비됨
   1. 전달과 동시에 메세지 삭제가 이루어지면 서버 에러등으로 인해 해당 메세지가 유실될 수 있음
4. 분산형 (고가용성)



## CLI





## 용어 정리

* Broker
  * Kafka 서버를 의미
* ZooKeeper
  * 분산 코디네이션 시스템
  * 컨슈머와 통신, 카프카의 메타데이터 정보를 저장, 카프카의 상태관리 등 목적으로 이용
* Topic
  * 카프카 클러스터에 데이터를 관리할 시 그 기준이 되는 개념
  * 1개 이상의 파티션(Partition)으로 구성
  * 어떤 데이터를 관리하는 하나의 그룹
  * Topic 자체적으로는 이벤트의 순서를 보장하지 않는다 
* partition
  * 각 Topic 당 데이터를 분산 처리하는 단위
  * 각 파티션 별로 이벤트의 순서를 보장한다 
* Replica
* Producer
* Consumer
* Consumer Group
  * 
* Leader
* Follower





## Kafka streams





## KSQL





## Connector

![img](https://blog.kakaocdn.net/dn/cF4X3U/btrhVsFQhBK/uMAETh8q7SIPKPBdVjcfz0/img.png)

#### 커넥터 종류

* Sink Connector
  * Consumer 역할
  * topic에 담긴 데이터를 특정 data source로 보내는 역할을 하는 connector
* Source Connector
  * Producer 역할
  * data source에 담긴 데이터를 topic에 담는 역할을 하는 connector



#### 지원 목록

> Confluent ([링크](https://www.confluent.io/product/confluent-connectors/?utm_medium=sem&utm_source=google&utm_campaign=ch.sem_br.nonbrand_tp.prs_tgt.kafka-connectors_mt.mbm_rgn.apac_lng.eng_dv.all_con.kafka-connectors&utm_term=%2Bkafka%20%2Bconnector&creative=&device=c&placement=&gclid=CjwKCAjw5s6WBhA4EiwACGncZSdMmVKLpOkwwHaaAI9amznnuhigrv1fZ6PWJyCt2cOD-yKcOEBx9BoCQQ4QAvD_BwE))
>
> Confluent Hub ([링크](https://www.confluent.io/hub/))

* MySQL
* MongoDB
* Amazon S3
* Redis
* Elasticsearch
* PostgreSQL
* 



#### 특이사항

1. connetor 따라서 qeury나 binlog를 이용하여 발행하는 듯
   1. query 베이스인 경우 soft deletion (active 등으로 판단하는 것)만 트랙킹 가능함
   2. https://stackoverflow.com/questions/45044641/delete-events-from-jdbc-kafka-connect-source
   3. https://soojong.tistory.com/entry/Source-Connector-%EC%83%9D%EC%84%B1-%ED%95%98%EA%B8%B0-feat-Topic-%EC%9E%90%EB%8F%99%EC%83%9D%EC%84%B1
   4. https://stackoverflow.com/questions/62966428/how-to-get-full-document-when-using-kafka-mongodb-source-connector-when-tracking
   5. https://presentlee.tistory.com/5
   6. https://presentlee.tistory.com/7





## Parameters (values.yaml)

> Reference Document (https://github.com/bitnami/charts/tree/master/bitnami/kafka/)

![img](https://velog.velcdn.com/images%2Fhyeondev%2Fpost%2Fe1919472-63ec-4846-8230-abce247e5489%2Fimg%20(1).png)

### Kafka

* logsDirs
* logRetentionBytes
* logRetentionCheckIntervalMs
* logRetentionHours
* defaultReplicationFactor
  * 기본 replicationFactor 값
  * 지정된 숫자와 동일한 broker에 저장된다

* numPartitions
* maxMessageBytes

### Statefulset

* replicaCount
  * Kafka Broker 갯수 (Number of Kafka nodes)

### ZooKeeper

* replicaCount
  * zookeeper  갯수 (Number of ZooKeeper nodes)



### Traffic Exposure

* externalAccess.enabled
* externalAccess.autoDiscovery.enabled
* externalAccess.service.type
* 



### DNS

* 



### Other

* serviceAccount.create
* serviceAccount.name
* serviceAccount.annotations
* rbac.create







#### batching

![img](https://miro.medium.com/max/1155/0*qKEVr3lpoCGWlgsY.png)

큐에 존재하는 메세지를 batch로 묶어서 네트워크 호출 횟수등을 줄이고 효율성을 증대

-> 관련 Configuration

* batch.size
* linger.ms

다만 batch 설정에 따라 지연시간 또는 기타 리소스와 관련한 trade-off가 발생하므로 테스트 후에 optimization할 것



#### 요청 쿼터(request quota)





#### 메세지(이벤트) 전달 컨셉

1. At most once
2. At least once
3. Exactly once





#### How to use kafka in Python

https://needjarvis.tistory.com/607



### 구축 방법

helm 이용 [링크](https://blog.advenoh.pe.kr/cloud/%ED%97%AC%EB%A6%84%EC%9C%BC%EB%A1%9C-Kafka-%EC%84%A4%EC%B9%98%ED%95%98%EA%B8%B0/) 



#### 구축 관련 이야기 & Topic 자동 생성 기능

https://epicdevs.com/20



#### Monitoring 방법

[Burrow](https://engineering.linkedin.com/apache-kafka/burrow-kafka-consumer-monitoring-reinvented)

https://docs.bitnami.com/kubernetes/infrastructure/kafka/administration/enable-metrics/





### Ref

1. [공식 Document](https://kafka.apache.org/documentation)
2. [velog](https://velog.io/@jaehyeong/Apache-Kafka%EC%95%84%ED%8C%8C%EC%B9%98-%EC%B9%B4%ED%94%84%EC%B9%B4%EB%9E%80-%EB%AC%B4%EC%97%87%EC%9D%B8%EA%B0%80)
3. https://freedeveloper.tistory.com/397
4. https://blog.voidmainvoid.net/475
5. https://sarc.io/index.php/miscellaneous/1436-kafka
6. https://presentlee.tistory.com/5
7. https://velog.io/@hyeondev/Apache-Kafka-%EC%9D%98-%EA%B8%B0%EB%B3%B8-%EC%95%84%ED%82%A4%ED%85%8D%EC%B3%90