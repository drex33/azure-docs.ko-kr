---
title: 공유 메타데이터 테이블
description: Azure Synapse Analytics는 서버리스 Apache Spark 풀에서 테이블을 만들면 데이터를 복제하지 않고 서버리스 SQL 풀 및 전용 SQL 풀에서 액세스할 수 있는 공유 메타데이터 모델을 제공합니다.
services: sql-data-warehouse
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 1f9cb59e7216f038baf96c3a809fb88c6e584d6c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124735154"
---
# <a name="azure-synapse-analytics-shared-metadata-tables"></a>Azure Synapse Analytics 공유 메타데이터 테이블


Azure Synapse Analytics를 사용하면 서로 다른 작업 영역 컴퓨팅 엔진에서 Apache Spark 풀과 서버리스 SQL 풀 간에 데이터베이스와 Parquet 지원 테이블을 공유할 수 있습니다.

Spark 작업을 통해 데이터베이스가 만들어지면 Parquet를 스토리지 형식으로 사용하는 Spark를 사용하여 테이블을 이 데이터베이스에 만들 수 있습니다. 테이블 이름은 소문자로 변환되며 소문자 이름을 사용하여 쿼리해야 합니다. 이러한 테이블은 모든 Azure Synapse 작업 영역 Spark 풀에서 쿼리하는 데 즉시 사용할 수 있게 됩니다. 권한이 있는 모든 Spark 작업에서도 사용할 수 있습니다.

또한 Spark에서 만든 관리형 및 외부 테이블은 서버리스 SQL 풀의 해당 동기화된 데이터베이스에서 동일한 이름의 외부 테이블로 사용할 수 있습니다. [SQL에서 Spark 테이블을 공개](#expose-a-spark-table-in-sql)하면 테이블 동기화에 대한 자세한 정보가 제공됩니다.

테이블은 서버리스 SQL 풀에 비동기적으로 동기화되므로 표시될 때까지 지연됩니다.

## <a name="manage-a-spark-created-table"></a>Spark에서 만든 테이블 관리

Spark를 사용하여 Spark에서 만든 데이터베이스를 관리합니다. 예를 들어 서버리스 Apache Spark 풀 작업을 통해 삭제하고 Spark에서 테이블을 만듭니다.

서버리스 SQL 풀에서 이러한 데이터베이스에 개체를 만들거나 데이터베이스를 삭제하려고 하면 작업이 실패합니다. 원본 Spark 데이터베이스는 서버리스 SQL 풀을 통해 변경할 수 없습니다.

## <a name="expose-a-spark-table-in-sql"></a>SQL에서 Spark 테이블 공개

### <a name="shared-spark-tables"></a>공유 Spark 테이블

Spark는 Azure Synapse를 통해 SQL에서 자동으로 공개되는 두 가지 유형의 테이블을 제공합니다.

- 관리형 테이블

  Spark는 TEXT, CSV, JSON, JDBC, PARQUET, ORC, HIVE, DELTA 및 LIBSVM과 같은 관리형 테이블에 데이터를 저장하는 방법에 대한 다양한 옵션을 제공합니다. 이러한 파일은 일반적으로 관리형 테이블 데이터가 저장된 `warehouse` 디렉터리에 저장됩니다.

- 외부 테이블

  Spark는 `LOCATION` 옵션을 제공하거나 Hive 형식을 사용하여 기존 데이터에 대한 외부 테이블을 만드는 방법도 제공합니다. 이러한 외부 테이블은 Parquet를 포함하여 다양한 데이터 형식일 수 있습니다.

Azure Synapse는 현재 데이터를 Parquet 또는 CSV 형식으로 저장하는 관리형 테이블과 외부 Spark 테이블만 SQL 엔진과 공유합니다. 다른 형식으로 지원되는 테이블은 자동으로 동기화되지 않습니다. SQL 엔진에서 테이블의 기본 형식을 지원하는 경우 이러한 테이블을 사용자의 SQL 데이터베이스에 있는 외부 테이블로 명시적으로 동기화할 수 있습니다.

### <a name="share-spark-tables"></a>Spark 테이블 공유

공유 가능한 관리형 및 외부 Spark 테이블은 SQL 엔진에서 다음과 같은 속성이 있는 외부 테이블로 공개됩니다.

- SQL 외부 테이블의 데이터 원본은 Spark 테이블의 위치 폴더를 나타내는 데이터 원본입니다.
- SQL 외부 테이블의 파일 형식은 Parquet입니다.
- SQL 외부 테이블의 액세스 자격 증명은 통과입니다.

모든 Spark 테이블 이름은 유효한 SQL 테이블 이름이고 모든 Spark 열 이름은 유효한 SQL 열 이름이므로 Spark 테이블 및 열 이름은 SQL 외부 테이블에 사용됩니다.

Spark 테이블은 Synapse SQL 엔진과 다른 데이터 형식을 제공합니다. 다음 표에서는 Spark 테이블 데이터 형식을 SQL 형식에 매핑하고 있습니다.

| Spark 데이터 형식 | SQL 데이터 형식 | 주석 |
|---|---|---|
| `byte`      | `smallint`       ||
| `short`     | `smallint`       ||
| `integer`   |    `int`            ||
| `long`      |    `bigint`         ||
| `float`     | `real`           |<!-- need precision and scale-->|
| `double`    | `float`          |<!-- need precision and scale-->|
| `decimal`      | `decimal`        |<!-- need precision and scale-->|
| `timestamp` |    `datetime2`      |<!-- need precision and scale-->|
| `date`      | `date`           ||
| `string`    |    `varchar(max)`   | `Latin1_General_100_BIN2_UTF8` 데이터 정렬 사용 |
| `binary`    |    `varbinary(max)` ||
| `boolean`   |    `bit`            ||
| `array`     |    `varchar(max)`   | `Latin1_General_100_BIN2_UTF8` 데이터 정렬을 사용하여 JSON으로 직렬화 |
| `map`       |    `varchar(max)`   | `Latin1_General_100_BIN2_UTF8` 데이터 정렬을 사용하여 JSON으로 직렬화 |
| `struct`    |    `varchar(max)`   | `Latin1_General_100_BIN2_UTF8` 데이터 정렬을 사용하여 JSON으로 직렬화 |

## <a name="security-model"></a>보안 모델

Spark 데이터베이스와 테이블뿐만 아니라 SQL 엔진에서 동기화된 해당 표현도 기본 스토리지 수준에서 보호됩니다. 현재 개체 자체에 대한 권한이 없으므로 개체는 개체 탐색기에서 볼 수 있습니다.

관리형 테이블을 만드는 보안 주체는 해당 테이블의 소유자로 간주되며 기본 폴더 및 파일 외에도 테이블에 대한 모든 권한을 갖습니다. 또한 데이터베이스 소유자는 자동으로 테이블의 공동 소유자가 됩니다.

인증 통과를 사용하여 Spark 또는 SQL 외부 테이블을 만드는 경우 데이터는 폴더 및 파일 수준에서만 보호됩니다. 다른 사용자가 이 유형의 외부 테이블을 쿼리하면 쿼리 제출자의 보안 ID가 파일 시스템으로 전달되어 액세스 권한이 확인됩니다.

폴더 및 파일에 대한 권한을 설정하는 방법에 대한 자세한 내용은 [Synapse Analytics 공유 데이터베이스](database.md)를 참조하세요.

## <a name="examples"></a>예제

### <a name="create-a-managed-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Spark에서 Parquet로 지원되는 관리형 테이블 만들기 및 서버리스 SQL 풀에서 쿼리

이 시나리오에는 `mytestdb`라는 Spark 데이터베이스가 있습니다. [서버리스 SQL 풀을 사용하여 Spark 데이터베이스 만들기 및 연결](database.md#create-and-connect-to-spark-database-with-serverless-sql-pool)을 참조하세요.

다음 명령을 실행하여 SparkSQL을 통해 관리형 Spark 테이블을 만듭니다.

```sql
    CREATE TABLE mytestdb.myparquettable(id int, name string, birthdate date) USING Parquet
```

이 명령은 `mytestdb` 데이터베이스에 `myparquettable` 테이블을 만듭니다. 테이블 이름은 소문자로 변환됩니다. 잠시 후 서버리스 SQL 풀에서 테이블을 볼 수 있습니다. 예를 들어 서버리스 SQL 풀에서 다음 명령문을 실행합니다.

```sql
    USE mytestdb;
    SELECT * FROM sys.tables;
```

결과에 `myparquettable`이 포함되어 있는지 확인합니다.

>[!NOTE]
>Parquet를 해당 스토리지 형식으로 사용하지 않는 테이블은 동기화되지 않습니다.

다음으로, 몇 가지 값을 Spark의 테이블에 삽입합니다(예: C# Notebook에서 다음 C# Spark 문 사용).

```csharp
using Microsoft.Spark.Sql.Types;

var data = new List<GenericRow>();

data.Add(new GenericRow(new object[] { 1, "Alice", new Date(2010, 1, 1)}));
data.Add(new GenericRow(new object[] { 2, "Bob", new Date(1990, 1, 1)}));

var schema = new StructType
    (new List<StructField>()
        {
            new StructField("id", new IntegerType()),
            new StructField("name", new StringType()),
            new StructField("birthdate", new DateType())
        }
    );

var df = spark.CreateDataFrame(data, schema);
df.Write().Mode(SaveMode.Append).InsertInto("mytestdb.myparquettable");
```

이제 다음과 같이 서버리스 SQL 풀에서 데이터를 읽을 수 있습니다.

```sql
SELECT * FROM mytestdb.dbo.myparquettable WHERE name = 'Alice';
```

다음 행을 결과로 가져옵니다.

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

### <a name="create-an-external-table-backed-by-parquet-in-spark-and-query-from-serverless-sql-pool"></a>Spark에서 Parquet로 지원되는 외부 테이블 만들기 및 서버리스 SQL 풀에서 쿼리

다음 예제에서는 관리형 테이블에 대해 이전 예제에서 만든 Parquet 데이터 파일에 외부 Spark 테이블을 만듭니다.

예를 들어 SparkSQL을 사용하여 다음을 실행합니다.

```sql
CREATE TABLE mytestdb.myexternalparquettable
    USING Parquet
    LOCATION "abfss://<fs>@arcadialake.dfs.core.windows.net/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/"
```

`<fs>` 자리 표시자를 작업 영역 기본 파일 시스템인 파일 시스템 이름으로 바꾸고, `<synapse_ws>` 자리 표시자를 이 예제를 실행하는 데 사용하는 Synapse 작업 영역의 이름으로 바꿉니다.

이전 예제에서는 `myexternalparquettable` 테이블을 `mytestdb` 데이터베이스에 만듭니다. 잠시 후 서버리스 SQL 풀에서 테이블을 볼 수 있습니다. 예를 들어 서버리스 SQL 풀에서 다음 명령문을 실행합니다.

```sql
USE mytestdb;
SELECT * FROM sys.tables;
```

결과에 `myexternalparquettable`이 포함되어 있는지 확인합니다.

이제 다음과 같이 서버리스 SQL 풀에서 데이터를 읽을 수 있습니다.

```sql
SELECT * FROM mytestdb.dbo.myexternalparquettable WHERE name = 'Alice';
```

다음 행을 결과로 가져옵니다.

```
id | name | birthdate
---+-------+-----------
1 | Alice | 2010-01-01
```

## <a name="next-steps"></a>다음 단계

- [Azure Synapse Analytics의 공유 메타데이터에 대한 자세한 정보](overview.md)
- [Azure Synapse Analytics의 공유 메타데이터 데이터베이스에 대한 자세한 정보](database.md)
