# 개요



## 사용 목적



## 구조







# 사용하기

##  IAM 설정하기

1. Athena 사용 권한
2. Glue 사용 권한
3. S3 사용 권한
   1. Put, Get
   2. 결과 파일을 저장할 bucket && directory
   3. 쿼리 대상이 되는 파일이 저장된 bucket && directory





## TIP

- Partitioning 활용
  - directory의 이름을 `{key}={value}` 형태로 작성
  - value에 DATE를 기입해도 string으로 처리된다
  - 파일 이름에서는 작동하지 않는다. 
  - partition을 활용하면 파일을 읽기 전 파티션을 사용하여 필요한 파일만 읽으므로, 속도 개선 및 요금 감소의 효과가 있다.
- 압축 활용
  - gzip, zstandard 등을 활용하여 압축을 하게되면 저장 용량이 줄어든다. 
  - 따라서, s3 보관 비용 및 athena 요금 감소 효과를 가질 수 있다. 
- parquet 포맷 활용
  - parquet의 경우 열 기반으로 데이터를 저장한다. 
  - select에서 특정 컬럼만을 사용하는 경우, parquet은 파일 전체를 읽지 않고 해당 컬럼만 데이터를 읽는다. 
  - csv와 같은 행 기반 형식은 일부 컬럼만 필요한 경우에도 전체 파일을 읽어야하므로 비효율적이다. 





