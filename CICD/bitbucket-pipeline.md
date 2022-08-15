# Bit-bucket Pipeline

Bit-bucket에서 제공하는 CICD를 위한 서비스

지정한 Image를 사용하여 yaml 파일(=yml 파일)로 정의된 작업을 수행한다. 

특정 상황에 맞춰 코드 테스팅, 이미지 빌드, 알림 등 여러가지 작업을 수행할 수 있다.



##### Format Example

```yaml
image: atlassian/default-image:3

pipelines:
  branches:
    main:
      - step:
          name: "Build Docker Image"
          services:
            - docker
          script:
            - do something
```



[TOC]

### Definitions 

ddd



### Condition

> Step별로 Condition을 설정하여 특정 상황에서만 해당 Step을 진행하도록 한다. 

##### Example Code

```yaml
      - step:
          name: "Name"
          image: atlassian/default-image:3
          condition:
              changesets:  # 아래에 선언된 path에서 변경점이 일어난 경우에만 실행
                 includePaths:
                   - "dags/**"  # dags 폴더 내부 전체 파일
```





### YAML anchors

* YAML 파일 내에서 코드를 재사용할 수 있게 만들어준다.

##### Example Code

```yaml
image: openjdk:11

build-hello-world: &build-project
  - step:
      script:
        - chmod +x gradlew
        - ./gradlew clean && ./gradlew build

pipelines:
  branches:
    main:
      - <<: *build-project
```



### Repository Variables

> Repository settings > PIPELINES > Repository variables 
>
> (해당 repo의 admin 권한이 필요)

* secured 여부를 클릭하여 키값 노출 여부를 설정할 수 있다. 
* secured를 체크한 경우 기존에 설정한 값을 확인할 수 없으며, pipeline 내에서 실행할 때에도 ** 등으로 대체되거나 아예 표시되지 않는다. ![example of a build log](https://images.ctfassets.net/zsv3d0ugroxu/6KNq1QbIqOyjwziZxP3AgX/4c1f38cd8236a4fa7b42b1589fbef95d/log_example)

* Pipeline 내에서 사용할 때는 `${Variable_name}` 형태로 사용한다. 



### SH 파일 실행하기

> 현재 정확한 원인은 알 수 없지만, sh파일을 repo의 루트 폴더에 위치하면 파일을 찾을 수 없다는 에러가 등장한다. 따라서, scripts 등의 폴더를 만들어서 그 안에 넣어 실행해야 한다.

* sh파일을 실행하려면 `/bin/bash file/path/file_name.sh` 형태로 입력해야 한다. 

```yaml
      - step:
          name: "Modify Helm Manifest and push to helm-repo"
          image: atlassian/default-image:3
          script:
            - /bin/bash scripts/helm_push.sh
```

 



### SSH Keys

> Repository settings > PIPELINES > SSH Keys
>
> (해당 repo의 admin 권한이 필요)

* Pipeline에서 사용할 SSH Key를 발급 & 관리하는 곳
* 한개의 SSH키만 발급이 가능하며, 추가로 필요한 경우 Repository Variables를 사용하여 변수로 전달해야한다. 
* Bit-Bucket에서 `repo`에 SSH Key를 등록한 경우 Clone만 가능하고 Push는 할 수 없다. 
  * 단, 현재 작업중인 `repo`는 가능하다. 



### OpenID Connect

https://velog.io/@jakeseo_me/Oauth-2.0%EA%B3%BC-OpenID-Connect-%ED%94%84%EB%A1%9C%ED%86%A0%EC%BD%9C-%EC%A0%95%EB%A6%AC



























## Trouble Shooting

#### sh 파일 실행시 `No such file or directory` 에러

- 현재 정확한 원인은 알 수 없지만, sh파일을 repo의 루트 폴더에 위치하면 파일을 찾을 수 없다는 에러가 등장한다. 따라서, scripts 등의 폴더를 만들어서 그 안에 넣어 실행해야 한다.