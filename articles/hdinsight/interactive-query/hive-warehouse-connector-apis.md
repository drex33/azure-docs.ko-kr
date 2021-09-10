---
title: Azure HDInsight의 Hive Warehouse Connector API
description: Hive Warehouse Connector의 다양한 API에 대해 알아봅니다.
author: adesh-rao
ms.author: adrao
ms.service: hdinsight
ms.topic: how-to
ms.date: 07/29/2021
ms.openlocfilehash: 4816830feac9aceb34ecfaa701ea0b7aec9c728b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536683"
---
# <a name="hive-warehouse-connector-apis-in-azure-hdinsight"></a>Azure HDInsight의 Hive Warehouse Connector API

이 문서에는 Hive Warehouse Connector에서 지원하는 모든 API가 나와 있습니다. 아래에 표시된 모든 예제는 spark-shell 및 Hive Warehouse Connector 세션을 사용하여 실행됩니다.

Hive Warehouse Connector 세션을 만드는 방법:

```scala
import com.hortonworks.hwc.HiveWarehouseSession
val hive = HiveWarehouseSession.session(spark).build()
```

## <a name="prerequisite"></a>필수 요소

[Hive Warehouse Connector 설정](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) 단계를 완료합니다.


## <a name="supported-apis"></a>지원되는 API

- 데이터베이스 설정:
    ```scala
    hive.setDatabase("<database-name>")
    ```

- 모든 데이터베이스 나열:
    ```scala
    hive.showDatabases()
    ```

- 현재 데이터베이스에 있는 모든 테이블 나열
    ```scala
    hive.showTables()
    ```

- 테이블 설명
    
    ```scala
   // Describes the table <table-name> in the current database
    hive.describeTable("<table-name>")
    ```
    
    ```scala
   // Describes the table <table-name> in <database-name>
    hive.describeTable("<database-name>.<table-name>")
    ```

- 데이터베이스 삭제
    
    ```scala
   // ifExists and cascade are boolean variables
    hive.dropDatabase("<database-name>", ifExists, cascade)
    ```

- 현재 데이터베이스에서 테이블 삭제
    
    ```scala
    // ifExists and purge are boolean variables
    hive.dropTable("<table-name>", ifExists, purge)
    ```

- 데이터베이스 만들기
    ```scala
   // ifNotExists is boolean variable
    hive.createDatabase("<database-name>", ifNotExists)
    ```

- 현재 데이터베이스에서 테이블 만들기
    ```scala
   // Returns a builder to create table
    val createTableBuilder = hive.createTable("<table-name>")
    ```
    
    테이블 생성용 작성기는 아래 작업만 지원합니다. 
    
    ```scala
   // Create only if table does not exists already
    createTableBuilder = createTableBuilder.ifNotExists()
    ```
    
    ```scala
   // Add columns
    createTableBuilder = createTableBuilder.column("<column-name>", "<datatype>")
    ```
    
    ```scala
    // Add partition column
    createTableBuilder = createTableBuilder.partition("<partition-column-name>", "<datatype>")
    ```
    ```scala
   // Add table properties
    createTableBuilder = createTableBuilder.prop("<key>", "<value>")
    ```
    ```scala
    // Creates a bucketed table,
    // Parameters are numOfBuckets (integer) followed by column names for bucketing
    createTableBuilder = createTableBuilder.clusterBy(numOfBuckets, "<column1>", .... , "<columnN>")
    ```
    
    ```scala
    // Creates the table
    createTableBuilder.create()
    ```

    > [!NOTE]
    > 이 API는 기본 위치에 ORC 형식의 테이블을 만듭니다. 다른 기능/옵션을 사용하거나 Hive 쿼리를 사용하여 테이블을 만들려면 `executeUpdate` API를 사용합니다.


- 테이블 읽기

    ```scala
   // Returns a Dataset<Row> that contains data of <table-name> in the current database
    hive.table("<table-name>")
    ```

- HiveServer2에서 DDL 명령 실행 

    ```scala
    // Executes the <hive-query> against HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>")
    ```
    
    ```scala
    // Executes the <hive-query> against HiveServer2
    // Throws exception, if propagateException is true and query threw excpetion in HiveServer2
    // Returns true or false if the query succeeded or failed respectively
    hive.executeUpdate("<hive-query>", propagateException) // propagate exception is boolean value
    ```

- 데이터 세트에서 Hive 쿼리 실행 및 결과 로드
    
  - LLAP 디먼을 통해 쿼리 실행 **[권장]**
    ```scala
    // <hive-query> should be a hive query 
    hive.executeQuery("<hive-query>")
    ```
  - JDBC를 통해 HiveServer2에서 쿼리 실행

    이 API를 사용하려면 Spark 세션을 시작하기 전에 Spark 구성에서 `spark.datasource.hive.warehouse.smartExecution`을 `false`로 설정합니다.
    ```scala
    hive.execute("<hive-query>")
    ```

- Hive Warehouse Connector 세션 닫기
    ```scala
    // Closes all the open connections and
    // release resources/locks from HiveServer2
    hive.close()
    ```

- Hive 병합 쿼리 실행
    
    이 API는 아래 형식의 Hive 병합 쿼리를 만듭니다.
    
    ```
    MERGE INTO <current-db>.<target-table> AS <targetAlias> USING <source expression/table> AS <sourceAlias>
    ON <onExpr>
    WHEN MATCHED [AND <updateExpr>] THEN UPDATE SET <nameValuePair1> ... <nameValuePairN>
    WHEN MATCHED [AND <deleteExpr>] THEN DELETE
    WHEN NOT MATCHED [AND <insertExpr>] THEN INSERT VALUES <value1> ... <valueN>
    ```

    ```scala
    val mergeBuilder = hive.mergeBuilder() // Returns a builder for merge query
    ```
    작성기는 다음 작업을 지원합니다.
    
    ```scala
    mergeBuilder.mergeInto("<taget-table>", "<targetAlias>")
    ```
    
    ```scala
    mergeBuilder.using("<source-expression/table>", "<sourceAlias>")
    ```
    
    ```scala
    mergeBuilder.on("<onExpr>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenUpdate("<updateExpr>", "<nameValuePair1>", ... , "<nameValuePairN>")
    ```
    
    ```scala
    mergeBuilder.whenMatchedThenDelete("<deleteExpr>")
    ```
    
    ```scala
    mergeBuilder.whenNotMatchedInsert("<insertExpr>", "<value1>", ... , "<valueN>");
    ```

    ```scala
    // Executes the merge query
    mergeBuilder.merge()
    ```

- 일괄 처리로 Hive 테이블에 데이터 세트 쓰기
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector")
       .option("table", tableName)
       .mode(SaveMode.Type)
       .save()
    ```
   - TableName은 `<db>.<table>` 또는 `<table>` 형식이어야 합니다. 데이터베이스 이름을 제공하지 않으면 현재 데이터베이스에서 테이블이 검색/생성됩니다.
    
   - SaveMode 유형은 다음과 같습니다.
    
     - Append: 지정된 테이블에 데이터 세트를 추가합니다.
    
     - Overwrite: 지정된 테이블의 데이터를 데이터 세트로 덮어씁니다.
    
     - Ignore: 테이블이 이미 있는 경우 쓰기를 건너뛰고 오류가 throw되지 않습니다.
    
     - ErrorIfExists: 테이블이 이미 있는 경우 오류를 throw합니다.


- HiveStreaming을 사용하여 Hive 테이블에 데이터 세트 쓰기
    ```scala
    df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("metastoreUri", "<HMS_URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    
     // To write to static partition
     df.write.format("com.hortonworks.spark.sql.hive.llap.HiveStreamingDataSource")
       .option("database", databaseName)
       .option("table", tableName)
       .option("partition", partition)
       .option("metastoreUri", "<HMS URI>")
    // .option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
       .save()
    ```
    > [!NOTE]
    > 스트림 쓰기는 항상 데이터를 추가합니다.


- Hive 테이블에 Spark 스트림 쓰기
    ```scala
    stream.writeStream
        .format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource")
        .option("metastoreUri", "<HMS_URI>")
        .option("database", databaseName)
        .option("table", tableName)
      //.option("partition", partition) , add if inserting data in partition
      //.option("metastoreKrbPrincipal", principal), add if executing in ESP cluster
        .start()
    ```
