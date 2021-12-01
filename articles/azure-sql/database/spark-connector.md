---
title: Microsoft Azure SQL 및 SQL Server와 함께 Spark 커넥터 사용
description: Azure SQL Database, Azure SQL Managed Instance, SQL Server와 함께 Spark 커넥터를 사용하는 방법을 알아봅니다.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: LitKnd
ms.author: kendralittle
ms.date: 09/02/2020
ms.openlocfilehash: a1544a1bbaf8b9f3eb5b016aa6920736087aa83b
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133369031"
---
# <a name="accelerate-real-time-big-data-analytics-using-the-spark-connector"></a>Spark 커넥터를 사용하여 실시간 빅 데이터 분석 가속화
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 2020년 9월 현재, 이 커넥터는 활발하게 관리되고 있지 않습니다. 그러나 이제는 [SQL Server 및 Azure SQL용 Apache Spark 커넥터](/sql/connect/spark/connector)를 사용할 수 있으며, Python 및 R 바인딩 지원, 사용하기 쉬운 인터페이스로 데이터 대량 삽입 등, 기타 여러 가지 기능이 향상되었습니다. 이전 커넥터 대신 새 커넥터를 평가하고 사용하기를 적극 권장합니다. 이전 커넥터에 대한 정보(이 페이지)는 보관 목적으로만 유지됩니다.

Spark 커넥터를 사용하면 Azure SQL Database, Azure SQL Managed Instance 및 SQL Server의 데이터베이스가 Spark 작업에 대한 입력 데이터 원본 또는 출력 데이터 싱크로 작동할 수 있습니다. 빅 데이터 분석에서 실시간 트랜잭션 데이터를 활용하고 임시 쿼리 또는 보고에 대한 결과를 유지할 수 있습니다. 기본 제공 JDBC 커넥터와 비교해서 이 커넥터는 사용자의 데이터베이스에 데이터를 대량으로 삽입하는 기능을 제공합니다. 10배~20배 빠른 성능으로 행 단위로 삽입할 때 뛰어난 성능을 제공합니다. Spark 커넥터는 Azure SQL Database 및 Azure SQL Managed Instance에 연결하기 위한 Azure AD(Azure Active Directory) 인증을 지원하므로 Azure AD 계정을 사용하여 Azure Databricks에서 데이터베이스를 연결할 수 있습니다. 기본 제공 JDBC 커넥터와 유사한 인터페이스를 제공합니다. 기존의 Spark 작업을 이 새로운 커넥터를 사용하도록 쉽게 마이그레이션할 수 있습니다.

## <a name="download-and-build-a-spark-connector"></a>Spark 커넥터 다운로드 및 빌드

이전에 이 페이지에서 링크했던 이전 커넥터의 GitHub 리포지토리는 적극적으로 관리되지 않습니다. 대신 [새 커넥터](https://github.com/microsoft/sql-spark-connector)를 평가하고 사용하기를 적극 권합니다.

### <a name="official-supported-versions"></a>공식 지원되는 버전

| 구성 요소                             | 버전                  |
| :-----------------------------------  | :----------------------- |
| Apache Spark                          | 2.0.2 이상           |
| 스칼라                                 | 2.10 이상            |
| SQL Server용 Microsoft JDBC Driver  | 6.2 이상             |
| Microsoft SQL Server                  | SQL Server 2008 이상 |
| Azure SQL Database                    | 지원됨                |
| Azure SQL Managed Instance            | 지원됨                |

Spark 커넥터는 SQL Server용 Microsoft JDBC Driver를 사용해 Spark 작업자 노드와 데이터베이스 사이에서 데이터를 이동합니다.

데이터 흐름은 다음과 같습니다.

1. Spark 마스터 노드는 SQL Database 또는 SQL Server의 데이터베이스에 연결하고 특정 테이블에서, 또는 특정 SQL 쿼리를 사용하여 데이터를 로드합니다.
2. Spark 마스터 노드는 변환을 위해 데이터를 작업자 노드에 배포합니다.
3. 작업자 노드는 SQL Database 및 SQL Server와 연결된 데이터베이스에 연결해서 데이터베이스에 데이터를 작성합니다. 사용자는 행 단위 삽입 또는 대량 삽입을 사용할지 선택할 수 있습니다.

아래 다이어그램은 데이터 흐름을 보여 줍니다.

   ![다이어그램에서는 마스터 노드가 데이터베이스에 직접 연결해 데이터베이스와 연결된 세 개의 작업자 노드에 연결하는 흐름을 설명합니다.](./media/spark-connector/architecture.png)

### <a name="build-the-spark-connector"></a>Spark 커넥터 빌드

현재 커넥터 프로젝트는 maven을 사용합니다. 종속성 없이 커넥터를 빌드하려면 다음을 실행할 수 있습니다.

- mvn clean package
- 릴리스 폴더에서 최신 버전의 JAR 다운로드
- SQL Database Spark JAR 포함

## <a name="connect-and-read-data-using-the-spark-connector"></a>Spark 커넥터를 사용하여 데이터 연결 및 읽기

Spark 작업에서 SQL Database 및 SQL Server의 데이터베이스에 연결하여 데이터를 읽거나 쓸 수 있습니다. SQL Database 및 SQL Server 데이터베이스에서 DML 또는 DDL 쿼리를 실행할 수도 있습니다.

### <a name="read-data-from-azure-sql-and-sql-server"></a>Azure SQL과 SQL Server에서 데이터 읽기

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "dbTable"        -> "dbo.Clients",
  "user"           -> "username",
  "password"       -> "*********",
  "connectTimeout" -> "5", //seconds
  "queryTimeout"   -> "5"  //seconds
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="read-data-from-azure-sql-and-sql-server-with-specified-sql-query"></a>지정된 SQL 쿼리를 사용하여 Azure SQL과 SQL Server에서 데이터를 읽습니다

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "queryCustom"  -> "SELECT TOP 100 * FROM dbo.Clients WHERE PostalCode = 98074" //Sql query
  "user"         -> "username",
  "password"     -> "*********",
))

//Read all data in table dbo.Clients
val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="write-data-to-azure-sql-and-sql-server"></a>Azure SQL 및 SQL Server에 데이터 작성

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

// Aquire a DataFrame collection (val collection)

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "dbTable"      -> "dbo.Clients",
  "user"         -> "username",
  "password"     -> "*********"
))

import org.apache.spark.sql.SaveMode
collection.write.mode(SaveMode.Append).sqlDB(config)
```

### <a name="run-dml-or-ddl-query-in-azure-sql-and-sql-server"></a>Azure SQL 또는 SQL Server에서 DML이나 DDL 쿼리 실행

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.query._
val query = """
              |UPDATE Customers
              |SET ContactName = 'Alfred Schmidt', City = 'Frankfurt'
              |WHERE CustomerID = 1;
            """.stripMargin

val config = Config(Map(
  "url"          -> "mysqlserver.database.windows.net",
  "databaseName" -> "MyDatabase",
  "user"         -> "username",
  "password"     -> "*********",
  "queryCustom"  -> query
))

sqlContext.sqlDBQuery(config)
```

## <a name="connect-from-spark-using-azure-ad-authentication"></a>Azure AD 인증을 사용하여 Spark에서 연결

Azure AD 인증을 사용하여 Azure SQL Database 및 SQL Managed Instance에 연결할 수 있습니다. 중앙에서 데이터베이스 사용자의 ID를 관리할 수 있는 Azure AD 인증은 SQL Server 인증 대신으로도 사용할 수 있습니다.

### <a name="connecting-using-activedirectorypassword-authentication-mode"></a>ActiveDirectoryPassword 인증 모드를 사용하여 연결

#### <a name="setup-requirement"></a>설치 요구 사항

ActiveDirectoryPassword 인증 모드를 사용하는 경우 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 및 해당 종속성을 다운로드하여 Java 빌드 경로에 포함시켜야 합니다.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"            -> "mysqlserver.database.windows.net",
  "databaseName"   -> "MyDatabase",
  "user"           -> "username",
  "password"       -> "*********",
  "authentication" -> "ActiveDirectoryPassword",
  "encrypt"        -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

### <a name="connecting-using-an-access-token"></a>액세스 토큰을 사용하여 연결

#### <a name="setup-requirement"></a>설치 요구 사항

액세스 토큰 기반 인증 모드를 사용하는 경우 [azure-activedirectory-library-for-java](https://github.com/AzureAD/azure-activedirectory-library-for-java) 및 해당 종속성을 다운로드하여 Java 빌드 경로에 포함시켜야 합니다.

[인증을 위한 Azure Active Directory Authentication 사용](authentication-aad-overview.md)을 참조하여 Azure SQL Database나 Azure SQL Managed Instance의 데이터베이스에 대한 액세스 토큰을 가져오는 방법을 알아봅니다.

```scala
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

val config = Config(Map(
  "url"                   -> "mysqlserver.database.windows.net",
  "databaseName"          -> "MyDatabase",
  "accessToken"           -> "access_token",
  "hostNameInCertificate" -> "*.database.windows.net",
  "encrypt"               -> "true"
))

val collection = sqlContext.read.sqlDB(config)
collection.show()
```

## <a name="write-data-using-bulk-insert"></a>대량 삽입을 사용하여 데이터 쓰기

기존 JDBC 커넥터는 행별 삽입을 사용하여 데이터베이스에 데이터를 씁니다. Spark 커넥터를 사용하여 대량 삽입으로 Azure SQL과 SQL Server에 데이터를 쓸 수 있습니다. 대량의 데이터 집합을 로드하거나 열 저장소 인덱스가 사용되는 테이블에 데이터를 로드할 때 쓰기 성능이 크게 향상됩니다.

```scala
import com.microsoft.azure.sqldb.spark.bulkcopy.BulkCopyMetadata
import com.microsoft.azure.sqldb.spark.config.Config
import com.microsoft.azure.sqldb.spark.connect._

/**
  Add column Metadata.
  If not specified, metadata is automatically added
  from the destination table, which may suffer performance.
*/
var bulkCopyMetadata = new BulkCopyMetadata
bulkCopyMetadata.addColumnMetadata(1, "Title", java.sql.Types.NVARCHAR, 128, 0)
bulkCopyMetadata.addColumnMetadata(2, "FirstName", java.sql.Types.NVARCHAR, 50, 0)
bulkCopyMetadata.addColumnMetadata(3, "LastName", java.sql.Types.NVARCHAR, 50, 0)

val bulkCopyConfig = Config(Map(
  "url"               -> "mysqlserver.database.windows.net",
  "databaseName"      -> "MyDatabase",
  "user"              -> "username",
  "password"          -> "*********",
  "dbTable"           -> "dbo.Clients",
  "bulkCopyBatchSize" -> "2500",
  "bulkCopyTableLock" -> "true",
  "bulkCopyTimeout"   -> "600"
))

df.bulkCopyToSqlDB(bulkCopyConfig, bulkCopyMetadata)
//df.bulkCopyToSqlDB(bulkCopyConfig) if no metadata is specified.
```

## <a name="next-steps"></a>다음 단계

아직 없는 경우 [azure-sqldb-spark GitHub 리포지토리](https://github.com/Azure/azure-sqldb-spark) 에서 Spark 커넥터를 다운로드하고 해당 리포지토리에서 추가 리소스를 탐색합니다.

- [Azure Databricks Notebooks 샘플](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/notebooks)
- [샘플 스크립트(Scala)](https://github.com/Azure/azure-sqldb-spark/tree/master/samples/scripts)

또한 [Apache Spark SQL, DataFrames 및 Datasets 가이드](https://spark.apache.org/docs/latest/sql-programming-guide.html) 및 [Azure Databricks 설명서](/azure/azure-databricks/)를 검토할 수도 있습니다.