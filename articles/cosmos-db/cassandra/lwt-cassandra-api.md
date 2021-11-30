---
title: Azure Cosmos DB Cassandra API 경량 트랜잭션
description: Azure Cosmos DB Cassandra API 경량 트랜잭션 지원에 대해 알아봅니다.
author: IriaOsara
ms.author: IriaOsara
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 11/19/2021
ms.custom: template-how-to
ms.openlocfilehash: 7ca6fad09a8f80aad0b4b794f49a7235ebbc3382
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133271932"
---
# <a name="azure-cosmos-db-cassandra-api-lightweight-transactions-with-conditions"></a>조건이 있는 경량 트랜잭션을 Cassandra API Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

> [!IMPORTANT]
> Cassandra용 Azure Cosmos DB API용 경량 트랜잭션은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공됩니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

대부분의 NoSQL 데이터베이스 플랫폼인 Apache Cassandra는 관계형 데이터베이스와 마찬가지로 ACID 트랜잭션을 지원하지 않으므로 일관성보다 가용성 및 파티션 허용 오차가 우선합니다. 일관성 수준이 LWT에서 작동하는 방식에 대한 자세한 내용은 [Azure Cosmos DB Cassandra API 일관성 수준을 참조하세요.](apache-cassandra-consistency-mapping.md) Cassandra는 ACID에 테두리를 두는 LWT(경량 트랜잭션)를 지원합니다. 데이터 삽입 또는 업데이트가 필요한 작업의 경우 쓰기 전에 읽기를 수행하는 데 도움이 됩니다. 

## <a name="lwt-support-within-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API 내의 LWT 지원
Azure Cosmos DB Cassandra API 내에서 LWT를 사용하려면 테이블 만들기 수준에서 다음 플래그를 설정하는 것이 좋습니다. 

```sql
with cosmosdb_cell_level_timestamp=true and cosmosdb_cell_level_timestamp_tombstones=true and cosmosdb_cell_level_timetolive=true
``` 
플래그를 사용하지 않는 경우보다 전체 행 삽입의 성능이 저하될 수 있습니다.

> [!NOTE]
> LWT는 다중 지역 쓰기 시나리오에서 지원되지 않습니다.

## <a name="lwt-with-flags-enabled"></a>플래그가 설정된 LWT
```sql
CREATE KEYSPACE IF NOT EXISTS lwttesting WITH REPLICATION= {'class': 'org.apache.cassandra.locator.SimpleStrategy', 'replication_factor' : '1'};
```

```sql
CREATE TABLE IF NOT EXISTS lwttesting.users (
  name text,
  userID int,
  address text,
  phoneCode int,
  vendorName text STATIC,
  PRIMARY KEY ((name), userID)) with cosmosdb_cell_level_timestamp=true and cosmosdb_cell_level_timestamp_tombstones=true and cosmosdb_cell_level_timetolive=true; 
```

아래 쿼리는 TRUE를 반환합니다.
```sql
INSERT INTO lwttesting.users(name, userID, phoneCode, vendorName)
VALUES('Sara', 103, 832, 'vendor21') IF NOT EXISTS; 
``` 

플래그를 사용하는 몇 가지 알려진 제한 사항도 있습니다. 테이블에 행을 삽입한 경우 정적 행을 삽입하려고 하면 FALSE가 반환됩니다. 
```sql
INSERT INTO lwttesting.users (userID, vendorName)
VALUES (104, 'staticVendor') IF NOT EXISTS;
```
위의 쿼리는 현재 FALSE를 반환하지만 TRUE여야 합니다.

## <a name="lwt-with-flags-disabled"></a>플래그가 비활성화된 LWT
플래그를 사용하지 않는 경우 IF 조건과 결합된 행 삭제는 지원되지 않습니다.

```sql
CREATE TABLE IF NOT EXISTS lwttesting.vendor_users (
  name text,
  userID int,
  areaCode int,
  vendor text STATIC,
  PRIMARY KEY ((userID), name)
);
```

```sql
DELETE FROM lwttesting.vendor_users 
WHERE userID =103 AND name = 'Sara' 
IF areaCode = 832;
```
오류 메시지: 전체 행의 조건부 삭제는 지원되지 않습니다. 

## <a name="lwt-with-flags-enabled-or-disabled"></a>플래그를 사용하거나 사용하지 않도록 설정된 LWT
정적 열과 일반 열의 할당 및 조건 조합을 포함하는 모든 요청은 IF 조건에서 지원되지 않습니다.
두 열이 모두 일반 열이기 때문에 이 쿼리는 오류 메시지를 반환하지 않습니다.
```sql
DELETE areaCode 
FROM lwttesting.vendor_users 
WHERE name= 'Sara' 
AND userID = 103 IF areaCode = 832;   
```
그러나 아래 쿼리는 오류 메시지를 반환합니다. `Conditions and assignments containing combination of Static and Regular columns are not supported.`
```sql
DELETE areaCode 
FROM lwttesting.vendor_users 
WHERE name= 'Sara' AND userID = 103 IF vendor = 'vendor21';  
```

## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure Cosmos DB Cassandra API 경량 트랜잭션이 작동하는 방식을 배웠습니다. 다음 문서로 진행할 수 있습니다.
- [Cassandra API 계정으로 데이터 마이그레이션](migrate-data.md)
- [Azure Cosmos DB Cassandra API에서 Glowroot 실행](glowroot-cassandra.md)