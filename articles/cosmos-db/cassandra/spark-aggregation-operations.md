---
title: Spark에서 Azure Cosmos DB Cassandra API의 테이블에 대한 집계 연산
description: 이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API 테이블에 대한 기본적인 집계 연산을 다룹니다.
author: TheovanKraay
ms.author: thvankra
ms.reviewer: sngun
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 09/24/2018
ms.openlocfilehash: 23ec7563c192ae8457e3d888dc4b0982b361afe2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529340"
---
# <a name="aggregate-operations-on-azure-cosmos-db-cassandra-api-tables-from-spark"></a>Spark에서 Azure Cosmos DB Cassandra API의 테이블에 대한 집계 연산 
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

이 문서에서는 Spark에서 Azure Cosmos DB Cassandra API 테이블에 대한 기본적인 집계 연산에 대해 설명합니다. 

> [!NOTE]
> Azure Cosmos DB Cassandra API에서는 현재 서버 쪽 필터링과 서버 쪽 집계가 지원되지 않습니다.

## <a name="cassandra-api-configuration"></a>Cassandra API 구성

```scala
import org.apache.spark.sql.cassandra._
//Spark connector
import com.datastax.spark.connector._
import com.datastax.spark.connector.cql.CassandraConnector
import org.apache.spark.sql.functions._

//if using Spark 2.x, CosmosDB library for multiple retry
//import com.microsoft.azure.cosmosdb.cassandra

//Connection-related
spark.conf.set("spark.cassandra.connection.host","YOUR_ACCOUNT_NAME.cassandra.cosmosdb.azure.com")
spark.conf.set("spark.cassandra.connection.port","10350")
spark.conf.set("spark.cassandra.connection.ssl.enabled","true")
spark.conf.set("spark.cassandra.auth.username","YOUR_ACCOUNT_NAME")
spark.conf.set("spark.cassandra.auth.password","YOUR_ACCOUNT_KEY")
// if using Spark 2.x
// spark.conf.set("spark.cassandra.connection.factory", "com.microsoft.azure.cosmosdb.cassandra.CosmosDbConnectionFactory")

//Throughput-related...adjust as needed
spark.conf.set("spark.cassandra.output.batch.size.rows", "1")
//spark.conf.set("spark.cassandra.connection.connections_per_executor_max", "10") // Spark 2.x
spark.conf.set("spark.cassandra.connection.remoteConnectionsPerExecutor", "10") // Spark 3.x
spark.conf.set("spark.cassandra.output.concurrent.writes", "1000")
spark.conf.set("spark.cassandra.concurrent.reads", "512")
spark.conf.set("spark.cassandra.output.batch.grouping.buffer.size", "1000")
spark.conf.set("spark.cassandra.connection.keep_alive_ms", "600000000")
```

> [!NOTE]
> Spark 3.0 또는 그 이상 버전을 사용하는 경우 Cosmos DB 도우미와 연결 팩터리를 설치할 필요가 없습니다. 또한 Spark 3 커넥터에 `connections_per_executor_max` 대신 `remoteConnectionsPerExecutor`을 사용해야 합니다(상단 참조). 상단의 Notebook 내에서 연결 관련 속성이 정의된 것을 볼 수 있습니다. 아래 구문을 사용하여 클러스터 수준(Spark 컨텍스트 초기화)에서 정의하지 않고도 이러한 방식으로 연결 속성을 정의할 수 있습니다. 그러나 Spark 컨텍스트가 필요한 작업을 사용하는 경우(예: 아래에 표시된 일부 작업에 대한 `CassandraConnector(sc)`의 경우) 클러스터 수준에서 연결 속성을 정의해야 합니다.

## <a name="sample-data-generator"></a>샘플 데이터 생성기

```scala
// Generate a simple dataset containing five values
val booksDF = Seq(
   ("b00001", "Arthur Conan Doyle", "A study in scarlet", 1887,11.33),
   ("b00023", "Arthur Conan Doyle", "A sign of four", 1890,22.45),
   ("b01001", "Arthur Conan Doyle", "The adventures of Sherlock Holmes", 1892,19.83),
   ("b00501", "Arthur Conan Doyle", "The memoirs of Sherlock Holmes", 1893,14.22),
   ("b00300", "Arthur Conan Doyle", "The hounds of Baskerville", 1901,12.25)
).toDF("book_id", "book_author", "book_name", "book_pub_year","book_price")

booksDF.write
  .mode("append")
  .format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks", "output.consistency.level" -> "ALL", "ttl" -> "10000000"))
  .save()
```

## <a name="count-operation"></a>개수(Count) 연산


### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").count
```

**출력:**
```bash
count: Long = 5
```

### <a name="dataframe-api"></a>데이터 프레임 API

데이터 프레임 수 계산은 현재 지원되지 않습니다.  아래 샘플은 임시 해결책으로 데이터 프레임을 메모리에 지속시킨 후 데이터 프레임 수 계산을 실행하는 방법을 보여줍니다.

&quot;메모리 부족&quot; 문제가 발생하지 않도록 다음 옵션 중에서 [스토리지 옵션]( https://spark.apache.org/docs/2.2.0/rdd-programming-guide.html#which-storage-level-to-choose)을 선택합니다.

* MEMORY_ONLY: 이 옵션은 기본 스토리지 옵션입니다. RDD를 JVM에 역직렬화된 Java 개체로 저장합니다. RDD가 메모리에 맞지 않으면 파티션이 캐시되지 않고 필요할 때마다 즉석에서 다시 계산됩니다.

* MEMORY_AND_DISK: RDD를 JVM에 역직렬화된 Java 개체로 저장합니다. RDD가 메모리에 맞지 않으면 디스크에 맞지 않는 파티션을 저장하고 필요할 때마다 저장된 위치에서 읽어옵니다.

* MEMORY_ONLY_SER(Java/Scala): RDD를 직렬화된 Java 개체(파티션 당 1바이트 배열)로 저장합니다. 이 옵션은 역직렬화된 개체에 비해 공간 효율적이며, 특히 고속 직렬 변환기를 사용하는 경우에는 더 그렇습니다. 단, 읽기 작업에 CPU가 많이 사용됩니다.

* MEMORY_AND_DISK_SER(Java/Scala): 이 스토리지 옵션은 MEMORY_ONLY_SER와 비슷합니다. 유일한 차이점은 필요할 때 디스크 메모리를 다시 계산하지 않고 디스크 메모리에 맞지 않는 파티션을 분산하는 것입니다.

* DISK_ONLY: RDD 파티션을 디스크에만 저장합니다.

* MEMORY_ONLY_2, MEMORY_AND_DISK_2…: 위의 수준과 같지만 두 개의 클러스터 노드에 있는 각 파티션을 복제합니다.

* OFF_HEAP(실험적임): MEMORY_ONLY_SER과 유사하지만 오프-힙(off-heap) 메모리에 데이터를 저장하므로 오프-힙(off-heap) 메모리를 사용하도록 미리 설정해야 합니다. 

```scala
//Workaround
import org.apache.spark.storage.StorageLevel

//Read from source
val readBooksDF = spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()

//Explain plan
readBooksDF.explain

//Materialize the dataframe
readBooksDF.persist(StorageLevel.MEMORY_ONLY)

//Subsequent execution against this DF hits the cache 
readBooksDF.count

//Persist as temporary view
readBooksDF.createOrReplaceTempView("books_vw")
```

### <a name="sql"></a>SQL

```sql
%sql
select * from books_vw;
select count(*) from books_vw where book_pub_year > 1900;
select count(book_id) from books_vw;
select book_author, count(*) as count from books_vw group by book_author;
select count(*) from books_vw;
```

## <a name="average-operation"></a>평균(Average) 연산

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Double) => c).mean
```

**출력:**
```
res24: Double = 16.016000175476073
```

### <a name="dataframe-api"></a>데이터 프레임 API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(avg("book_price"))
  .show
```

**출력:**
```
+------------------+
|   avg(book_price)|
+------------------+
|16.016000175476073|
+------------------+
```

### <a name="sql"></a>SQL

```sql
select avg(book_price) from books_vw;
```
**출력:**
```
16.016000175476073
```

## <a name="min-operation"></a>최솟값(Min) 연산

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).min
```

**출력:**
```
res31: Float = 11.33
```

### <a name="dataframe-api"></a>데이터 프레임 API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_id","book_price")
  .agg(min("book_price"))
  .show
```

**출력:**
```
+---------------+
|min(book_price)|
+---------------+
|          11.33|
+---------------+
```

### <a name="sql"></a>SQL

```sql
%sql
select avg(book_price) from books_vw;
```

**출력:**
```
11.33
```

## <a name="max-operation"></a>최댓값(Max) 연산

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).max
```

### <a name="dataframe-api"></a>데이터 프레임 API

```scala 
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(max("book_price"))
  .show
```

**출력:**
```
+---------------+
|max(book_price)|
+---------------+
|          22.45|
+---------------+
```

### <a name="sql"></a>SQL

```sql
%sql
select max(book_price) from books_vw;
```
**출력:**
```
22.45
```

## <a name="sum-operation"></a>합계(Sum) 연산

### <a name="rdd-api"></a>RDD API

```scala
sc.cassandraTable("books_ks", "books").select("book_price").as((c: Float) => c).sum
```

**출력:**
```
res46: Double = 80.08000087738037
```

### <a name="dataframe-api"></a>데이터 프레임 API

```scala
spark
  .read
  .cassandraFormat("books", "books_ks", "")
  .load()
  .select("book_price")
  .agg(sum("book_price"))
  .show
```
**출력:**
```
+-----------------+
|  sum(book_price)|
+-----------------+
|80.08000087738037|
+-----------------+
```

### <a name="sql"></a>SQL

```sql
select sum(book_price) from books_vw;
```

**출력:**
```
80.08000087738037
```

## <a name="top-or-comparable-operation"></a>상위 또는 유사한 연산

### <a name="rdd-api"></a>RDD API

```scala
val readCalcTopRDD = sc.cassandraTable("books_ks", "books").select("book_name","book_price").sortBy(_.getFloat(1), false)
readCalcTopRDD.zipWithIndex.filter(_._2 < 3).collect.foreach(println)
//delivers the first top n items without collecting the rdd to the driver.
```
**출력:**
```
(CassandraRow{book_name: A sign of four, book_price: 22.45},0)
(CassandraRow{book_name: The adventures of Sherlock Holmes, book_price: 19.83},1)
(CassandraRow{book_name: The memoirs of Sherlock Holmes, book_price: 14.22},2)
readCalcTopRDD: org.apache.spark.rdd.RDD[com.datastax.spark.connector.CassandraRow] = MapPartitionsRDD[430] at sortBy at command-2371828989676374:1
```
### <a name="dataframe-api"></a>데이터 프레임 API

```scala
import org.apache.spark.sql.functions._

val readBooksDF = spark.read.format("org.apache.spark.sql.cassandra")
  .options(Map( "table" -> "books", "keyspace" -> "books_ks"))
  .load
  .select("book_name","book_price")
  .orderBy(desc("book_price"))
  .limit(3)

//Explain plan
readBooksDF.explain

//Top
readBooksDF.show
```

**출력:**
```
== Physical Plan ==
TakeOrderedAndProject(limit=3, orderBy=[book_price#1840 DESC NULLS LAST], output=[book_name#1839,book_price#1840])
+- *(1) Scan org.apache.spark.sql.cassandra.CassandraSourceRelation@29cd5f58 [book_name#1839,book_price#1840] PushedFilters: [], ReadSchema: struct<book_name:string,book_price:float>
+--------------------+----------+
|           book_name|book_price|
+--------------------+----------+
|      A sign of four|     22.45|
|The adventures of...|     19.83|
|The memoirs of Sh...|     14.22|
+--------------------+----------+

import org.apache.spark.sql.functions._
readBooksDF: org.apache.spark.sql.Dataset[org.apache.spark.sql.Row] = [book_name: string, book_price: float]
```

### <a name="sql"></a>SQL

```sql
select book_name,book_price from books_vw order by book_price desc limit 3;
```

## <a name="next-steps"></a>다음 단계

테이블 복사 작업을 수행하려면 다음을 참조하세요.

* [테이블 복사 작업](spark-table-copy-operations.md)
