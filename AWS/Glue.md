# What is Glue?

* serverless
* ETL solutions



## AWS Glue Data Catalog

#### Persistent Metadata Store

> Example of Meta Data = Data Location, Schema, Data Types, Data Classification, ...

It is a managed service that lets you store, annotate, and share metadata which can be used to query and transform data

One AWS Glue Data Catalog per AWS region

Identity and Access Management (IAM) policies control access

Can be used for data governance

 

#### 목차 

> Databases
>
> ​	Tables
>
> ​	Connections
>
> Crawlers
>
> ​	Classifers
>
> Schema registries
>
> ​	Schemas
>
> Settings

##### Databases

​	A set of associated Data Catalog table definitions organized into a logical group

​	-> 가상의 테이블 정보등을 가지고 있는 데이터 베이스 

##### 	Tables

​		The metadata definition that represents your data. The data resides in its original store. This is just a representation of the schema.

##### Crawlers

​	A program that connects to a data store (source or target), progresses through a prioritized list of classifiers to determine the schema for your data, and then creates metadata tables in the AWS Glue Data Catalog.







### Trouble shooting

1. HIVE_PARTITION_SCHEMA_MISMATCH

   1. HIVE_PARTITION_SCHEMA_MISMATCH: There is a mismatch between the table and partition schemas. The types are incompatible and cannot be coerced. The column 'custom user properties' in table 'airbridge_test.raw_v1_2_1' is declared as type 'string', but partition 'date=2022-06-20' declared column 'custom user properties' as type 'struct<birth_date:string,gender:string,m_no:int,name:string,nickname:string>'.

      -> Table의 정의와 partition의 정의가 달라서 생기는 문제

      -> parquet로 저장된 파일도 해당 에러가 일어날지는 확인해봐야함. 당장은 csv파일에서 증상이 나타남.

      

2. [Airbridge] CSV 파일을 읽을 때 ""로 escape 처리되는데 그러지 않고 ,로 전부 column을 나누어서 테이블이 제대로 생성되지 않음. (1번과 관련됨)
   1. parquet로 변환해서 테스트해보려고 하는중 