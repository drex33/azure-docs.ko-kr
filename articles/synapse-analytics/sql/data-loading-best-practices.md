---
title: 전용 SQL 풀에 대한 데이터 로드 모범 사례
description: Azure Synapse Analytics에서 전용 SQL 풀로 데이터를 로드 하기 위한 권장 사항 및 성능 최적화
services: synapse-analytics
author: joannapea
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 08/26/2021
ms.author: joanpo
ms.reviewer: igorstan
ms.custom: azure-synapse
ms.openlocfilehash: 58ddd616f600e4f36db4cc6da02cb1e6512b7bc2
ms.sourcegitcommit: 991268c548dd47e5f7487cd025c7501b9315e477
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133287444"
---
# <a name="best-practices-for-loading-data-into-a-dedicated-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀로 데이터를 로드 하는 모범 사례

이 문서에서는 데이터 로드에 대한 권장 사항 및 성능 최적화를 찾아볼 수 있습니다.

## <a name="prepare-data-in-azure-storage"></a>Azure Storage에서 데이터 준비

대기 시간을 최소화하려면 스토리지 계층과 전용 SQL 풀을 함께 배치합니다.

ORC 파일 형식으로 데이터를 내보낼 때 큰 텍스트 열이 있으면 Java 메모리 부족 오류가 발생할 수 있습니다. 이러한 제한 사항을 해결하려면 열의 하위 집합만 내보냅니다.

PolyBase는 100만 바이트 이상의 데이터를 포함하는 행을 로드할 수 없습니다. Azure Blob Storage 또는 Azure Data Lake Store의 텍스트 파일에 데이터를 배치한 경우 데이터가 1,000,000바이트 미만이어야 합니다. 바이트 제한은 테이블 스키마에 관계없이 true입니다.

모든 파일 형식에는 서로 다른 성능 특성이 있습니다. 가장 빠르게 로드하려면 압축 구분 텍스트 파일을 사용합니다. UTF-8과 UTF-16의 성능 차이는 미미합니다.

대규모 압축 파일은 더 작은 크기의 압축 파일로 분할합니다.

## <a name="run-loads-with-enough-compute"></a>충분한 컴퓨팅으로 로드 실행

로드 속도를 가장 빠르게 하려면 로드 작업을 한 번에 하나만 실행합니다. 이것이 가능하지 않은 경우 동시에 실행하는 로드 수를 최소화합니다. 대량의 로드 작업이 예상되는 경우 로드 전에 전용 SQL 풀 확장을 고려하는 것이 좋습니다.

적절한 컴퓨팅 리소스가 포함된 로드를 실행하려면 부하를 실행하기 위해 지정된 로드 사용자를 만듭니다. 특정 리소스 클래스 또는 워크로드 그룹에 각 로드 사용자를 할당합니다. 로드를 실행하려면 로드 사용자 중 한 명으로 로그인하고 부하를 실행합니다. 사용자의 리소스 클래스를 사용하여 부하를 실행합니다.  이 메서드는 현재 리소스 클래스 요구 사항에 맞게 사용자의 리소스 클래스를 변경하는 것보다 더 간단합니다.


### <a name="create-a-loading-user"></a>로드 사용자 만들기

이 예에서는 특정 워크로드 그룹으로 분류된 로딩 사용자를 생성합니다. 첫 번째 단계는 **마스터에 연결** 하고 로그인을 만드는 것입니다.

```sql
   -- Connect to master
   CREATE LOGIN loader WITH PASSWORD = 'a123STRONGpassword!';
```

전용 SQL 풀에 연결하고 사용자를 생성합니다. 다음 코드에서는 mySampleDataWarehouse라는 데이터베이스에 연결되어 있다고 가정합니다. 이 코드는 loader라는 사용자를 생성하는 방법을 보여 주고 [COPY 문](/sql/t-sql/statements/copy-into-transact-sql?view=azure-sqldw-latest&preserve-view=true)을 사용하여 테이블을 생성 및 로드할 수 있는 사용자 권한을 부여합니다. 그런 다음, 사용자를 최대 리소스가 있는 DataLoads 워크로드 그룹으로 분류합니다. 

```sql
   -- Connect to the dedicated SQL pool
   CREATE USER loader FOR LOGIN loader;
   GRANT ADMINISTER DATABASE BULK OPERATIONS TO loader;
   GRANT INSERT ON <yourtablename> TO loader;
   GRANT SELECT ON <yourtablename> TO loader;
   GRANT CREATE TABLE TO loader;
   GRANT ALTER ON SCHEMA::dbo TO loader;
   
   CREATE WORKLOAD GROUP DataLoads
   WITH ( 
       MIN_PERCENTAGE_RESOURCE = 0
       ,CAP_PERCENTAGE_RESOURCE = 100
       ,REQUEST_MIN_RESOURCE_GRANT_PERCENT = 100
    );

   CREATE WORKLOAD CLASSIFIER [wgcELTLogin]
   WITH (
         WORKLOAD_GROUP = 'DataLoads'
       ,MEMBERNAME = 'loader'
   );
```

<br><br>
>[!IMPORTANT] 
>이 코드는 단일 부하에 SQL 풀의 리소스 100%를 할당하는 극단적인 예입니다. 이 예제에서는 최대 동시성 1이 제공됩니다. 이 방법은 워크로드 전반에서 리소스의 균형을 맞추기 위해 고유한 구성을 갖춘 추가 워크로드 그룹을 만들어야 하는 초기 로드에만 사용해야 합니다. 

로드 워크로드 그룹의 리소스를 사용하여 로드를 실행하려면 loader로 로그인하고 로드를 실행합니다. 

## <a name="allow-multiple-users-to-load"></a>여러 사용자가 로드하도록 허용

여러 사용자가 데이터 웨어하우스에 데이터를 로드해야 하는 경우가 종종 있습니다. [CREATE TABLE AS SELECT(Transact-SQL)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse?view=azure-sqldw-latest&preserve-view=true)를 사용하여 로드하려면 데이터베이스에 대한 CONTROL 권한이 필요합니다.  CONTROL 권한은 모든 스키마에 대한 제어 액세스를 부여합니다. 모든 로드 사용자가 모든 스키마에 대한 제어 액세스 권한을 갖는 것은 좋지 않습니다. 권한을 제한하려면 DENY CONTROL 문을 사용합니다.

예를 들어, 데이터베이스 스키마, 부서 A에 대한 스키마_A 및 부서 B에 대한 스키마_B를 가정합니다. 데이터베이스 사용자, 사용자_A 및 사용자_B가 부서 A와 B 각각에서 PolyBase 로드에 대한 사용자가 되도록 합니다. 둘 모두 데이터베이스 CONTROL 권한을 부여 받습니다. 스키마 A와 B의 작성자는 이제 DENY를 사용하여 해당 스키마를 잠급니다.

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```

사용자_A 및 사용자_B는 이제 다른 부서의 스키마에서 차단되었습니다.

## <a name="load-to-a-staging-table"></a>준비 테이블에 로드

데이터를 데이터 웨어하우스 테이블로 이동하는 로딩 속도를 가장 빠르게 만들려면 데이터를 준비 테이블에 로드합니다.  준비 테이블을 힙으로 정의하고 배포 옵션에 라운드 로빈을 사용합니다.

로드는 먼저 데이터를 준비 테이블로 로드한 다음 프로덕션 데이터 웨어하우스 테이블에 삽입하는 2단계 프로세스로 간주하는 것이 좋습니다. 프로덕션 테이블이 해시 배포를 사용하는 경우 해시 배포를 사용하여 준비 테이블을 정의하면 로드하고 삽입하는 총 시간이 더 빠를 수 있습니다. 준비 테이블로 로드하는 데 시간이 더 걸리지만 프로덕션 테이블에 행을 삽입하는 두 번째 단계에서는 배포에서 데이터 이동이 발생하지 않습니다.

## <a name="load-to-a-columnstore-index"></a>columnstore 인덱스에 로드

columnstore 인덱스는 고품질 행 그룹으로 데이터를 압축하기 위해 대량의 메모리가 필요합니다. 최상의 압축 및 인덱스 효율성을 위해 columnstore 인덱스는 최대 1,048,576개의 행을 각 행 그룹으로 압축해야 합니다. 메모리 압박이 있는 경우 columnstore 인덱스는 최대 압축률을 달성하지 못할 수 있습니다. 이는 쿼리 성능에 영향을 줍니다. 심층 분석은 [Columnstore 메모리 최적화](data-load-columnstore-compression.md)를 참조하세요

- 로드 사용자가 메모리를 최대 압축률을 충분히 달성할 수 있도록 하려면 중간 규모 또는 대규모 리소스 클래스의 멤버인 로드 사용자를 사용합니다.
- 새로운 행 그룹을 완전히 채울 수 있는 충분한 행을 로드합니다. 대량 로드 중에는 행 수가 1,048,576이 될 때마다 전체 열 그룹으로 직접 columnstore에 압축됩니다. 로드하는 행 수가 102,400 미만인 경우에는 B-트리 인덱스에 행이 보유되는 deltastore에 행을 보냅니다. 너무 적은 행을 로드한 경우 모두 deltastore로 이동하여 columnstore 형식으로 즉시 압축되지 않을 수 있습니다.

## <a name="increase-batch-size-when-using-sqlbulkcopy-api-or-bcp"></a>SQLBulkCopy API 또는 BCP를 사용하는 경우 일괄 처리 크기 늘리기


COPY 문을 사용하여 로드하면 전용 SQL 풀에서 처리량이 극대화됩니다. COPY 문을 사용하여 로드할 수 없고 [SqLBulkCopy API](/dotnet/api/system.data.sqlclient.sqlbulkcopy?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) 또는 [BCP](/sql/tools/bcp-utility?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true)를 사용해야 하는 경우 처리량 향상을 위해 일괄 처리 크기를 늘려야 합니다.

> [!TIP]
> 십만에서 백만 행의 일괄 처리 크기가 최적의 일괄 처리 용량을 판단할 수 있는 권장 기준선입니다. 

## <a name="manage-loading-failures"></a>로드 오류 관리

외부 테이블을 사용하는 로드가 *"쿼리가 중단되었습니다. 외부 소스에서 읽는 동안 최대 거부 임계값에 도달했습니다."* 오류로 인해 실패할 수 있습니다. 이 메시지는 외부 데이터에 더티 레코드가 포함되어 있음을 나타냅니다. 열의 수와 데이터 형식이 외부 테이블의 열 정의와 일치하지 않거나 데이터가 지정된 외부 파일 형식을 준수하지 않는 경우 데이터 레코드가 더티한 것으로 간주됩니다.

더티 레코드 문제를 해결하려면 외부 테이블 및 외부 파일 형식 정의가 올바른지와 외부 데이터가 이러한 정의를 준수하는지 확인합니다. 외부 데이터 레코드의 하위 집합이 더티 인 경우 [' CREATE EXTERNAL TABLE '](/sql/t-sql/statements/create-external-table-transact-sql?view=azure-sqldw-latest&preserve-view=true) 의 거부 옵션을 사용 하 여 쿼리에 대해 이러한 레코드를 거부 하도록 선택할 수 있습니다.

## <a name="insert-data-into-a-production-table"></a>프로덕션 테이블에 데이터 삽입

[INSERT 문](/sql/t-sql/statements/insert-transact-sql?view=azure-sqldw-latest&preserve-view=true)을 사용하는 작은 테이블에 한 번만 로드하거나 정기적으로 조회를 다시 로드하는 경우, `INSERT INTO MyLookup VALUES (1, 'Type 1')`와 같은 명령문으로 충분히 수행할 수 있습니다.  하지만 singleton 삽입은 대량 로드 수행만큼 효율적이지 않습니다.

하루 종일 수천 개 이상의 단일 삽입을 수행하는 경우 대량 로드할 수 있도록 로드를 일괄 처리합니다.  파일에 단일 삽입을 추가하는 프로세스를 개발하고 정기적으로 파일을 로드하는 다른 프로세스를 만듭니다.

## <a name="create-statistics-after-the-load"></a>로드 후 통계 만들기

쿼리 성능을 개선하려면 데이터를 처음 로드하거나 데이터 내에 큰 변화가 생긴 후에, 모든 테이블의 모든 열에서 통계를 만드는 것이 중요합니다. 통계를 수동으로 만들거나 [통계 자동 생성](../sql-data-warehouse/sql-data-warehouse-tables-statistics.md?context=/azure/synapse-analytics/context/context)을 사용할 수 있습니다.

통계에 대한 자세한 설명은 [통계](develop-tables-statistics.md)를 참조하세요. 다음 예는 Customer_Speed 테이블의 5개 열에 대한 통계를 수동으로 만드는 방법을 보여 줍니다.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="rotate-storage-keys"></a>스토리지 키 회전

정기적으로 Blob Storage 액세스 키를 변경하는 것은 좋은 보안 방법입니다. Blob Storage 계정에 두 개의 스토리지 키가 있으므로 키 전환이 가능합니다.

Azure Storage 계정 키를 회전하려면:

키가 변경된 각 스토리지 계정에 대해 [ALTER DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/alter-database-scoped-credential-transact-sql?view=azure-sqldw-latest&preserve-view=true)을 실행합니다.

예:

원래 키를 만드는 경우

```sql
CREATE DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key1'
```

키 1에서 키 2로 키를 회전하는 경우

```sql
ALTER DATABASE SCOPED CREDENTIAL my_credential WITH IDENTITY = 'my_identity', SECRET = 'key2'
```

기본 외부 데이터 원본에 대한 다른 변경은 필요하지 않습니다.

## <a name="next-steps"></a>다음 단계

- PolyBase 및 ELT(추출, 로드, 변환) 프로세스를 디자인하는 방법을 자세히 알아보려면 [Azure Synapse Analytics에 대한 ELT 디자인](../sql-data-warehouse/design-elt-data-loading.md?context=/azure/synapse-analytics/context/context)을 참조하세요.
- 로드 자습서는 [PolyBase를 사용하여 Azure Blob Storage에서 Azure Synapse Analytics로 데이터 로드](../sql-data-warehouse/load-data-from-azure-blob-storage-using-copy.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)를 참조하세요.
- 데이터 로드를 모니터링하려면 [DMV를 사용하여 워크로드 모니터링](../sql-data-warehouse/sql-data-warehouse-manage-monitor.md?context=/azure/synapse-analytics/context/context)을 참조하세요.