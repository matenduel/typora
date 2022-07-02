해야할 일



<토요일>



## glue + athena 공부

* 테스트가 끝나고 체크해야하는 항목
  * [balaan-airbridge-parquet](https://s3.console.aws.amazon.com/s3/buckets/balaan-airbridge-parquet?region=ap-northeast-2) 삭제
  * Glue studio job 삭제
  * crawler 삭제
  * database 삭제 



* 질문
  * airflow를 통해서 parquet 변환을 하면 glue나 lambda를 쓰는 것보다 비용이 많이 나오나?
  * 왜 중간에 Custom User Properties가 사라지지?
  * 왜 parquet 변환후에 #0같은게 붙지>?
  * 분류자란?
  * 폴더 구분해야함
  * 중간에 version이 변경되는 경우 어떻게 대처해야하는가?
  * crawler가 struct를 잘 못다루는거 같은데 방법은?



우선 parquet으로 변환한 후에 athena 잘 작동함





## Terraform

IAM Role & Policy

S3



### Helm

Kafka 헬름 차트 공부



### bdp_utils

S3 Class 작성



### Airflow 정리 





### 데이터팀 문서 정리 

커밋 메시지 규칙
FEAT : 새로운 기능의 추가(Data Job Request 의 경우 신규 요청)
FIX : 수정 (코드 리뷰나 사용자 요청에 의해 코드 변경시)
DOCS : 주석이나 DocString 수정
STYLE : Code Convention 규칙에 어긋나는 것들 수정시, 스타일 수정시 (예: json 포맷팅 등)
REFACTOR :기존 코드를 개선하는 작업 (기능은 동일)
ENV : 환경파일 수정, 메타 파일 수정 (ini , env , .gitignore 파일 수정등)

---

DocString

1. 파일 레벨 :

   1. Data Request Job 의 경우 요청 링크

   1. 간단한 데이터 작업 요청 내용 요약(목적 과 결과 명확히)

1. 함수 레벨 :

   1. 매개변수 설명

   1. 함수 설명 (목적, 결과 명확히)

---



git, pr 규칙, 배송 정보, ...