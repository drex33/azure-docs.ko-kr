---
title: Azure Cosmos DB Cassandra API 계정의 인덱싱
description: Azure Cosmos DB Cassandra API 계정에서 보조 인덱싱이 작동하는 방식을 알아봅니다.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 09/03/2021
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: a3ac64590c2978ccd052a9dd294cf3ce49bb847e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123479451"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Azure Cosmos DB Cassandra API의 보조 인덱싱
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

Azure Cosmos DB의 Cassandra API는 기본 인덱싱 인프라를 활용하여 플랫폼에 내재된 높은 인덱싱 수준을 노출합니다. 그러나 핵심 SQL API와 달리 Azure Cosmos DB의 Cassandra API는 기본적으로 모든 특성을 인덱싱하지는 않습니다. 대신 Apache Cassandra와 동일하게, 특정 특성에 대한 인덱스를 만드는 보조 인덱싱을 지원합니다.  

일반적으로 분할되지 않은 열에서 필터 쿼리를 실행하는 것은 바람직하지 않습니다. ALLOW FILTERING 구문을 명시적으로 사용해야 하며, 이 경우 작업이 제대로 수행되지 않을 수 있습니다. Azure Cosmos DB에서는 낮은 카디널리티 특성에 대해 해당 쿼리를 실행할 수 있습니다. 쿼리가 파티션 간에 팬아웃하여 결과를 검색하기 때문입니다.

자주 업데이트되는 열에 인덱스를 만드는 것은 바람직하지 않습니다. 테이블을 정의할 때 인덱스를 만드는 것이 좋습니다. 이렇게 하면 데이터와 인덱스가 일관된 상태를 유지합니다. 기존 데이터에 새 인덱스를 만드는 경우, 지금은 테이블의 인덱스 진행률 변경을 추적할 수 없습니다. 이 작업의 진행률을 추적해야 하는 경우 [지원 티켓](../../azure-portal/supportability/how-to-create-azure-support-request.md)을 통해 진행률 변경을 요청해야 합니다.


> [!NOTE]
> 다음은 보조 인덱스가 지원되지 않는 개체입니다.
> - 고정된 컬렉션 형식, decimal, variant 형식 등의 데이터 형식
> - 정적 열
> - 클러스터링 키

> [!WARNING]
> 테이블에 복합 기본 키가 있고 [복합 기본 키의](cassandra-partitioning.md#compound-primary-key) 파티션 키 값 *요소만* 필터링하려는 경우 파티션 키 *에 보조 인덱스* 를 명시적으로 추가해야 합니다. Azure Cosmos DB Cassandra API 기본적으로 파티션 키에 인덱스를 적용하지 않으며 이 시나리오의 인덱스는 쿼리 성능을 크게 향상시킬 수 있습니다. 자세한 내용은 [분할에](cassandra-partitioning.md) 대한 문서를 검토하세요.

## <a name="indexing-example"></a>인덱싱 예제

먼저 CQL 셸 프롬프트에서 다음 명령을 실행하여 샘플 키스페이스와 테이블을 만듭니다.

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

그런 후에 다음 명령을 사용하여 샘플 사용자 데이터를 삽입합니다.

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

다음 문을 실행하려고 하면 `ALLOW FILTERING`를 사용하라는 오류가 발생합니다. 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

이전 섹션에서 설명한 것처럼 Cassandra API는 ALLOW FILTERING을 지원하지만 권장되지는 않습니다. 대신, 다음 예제와 같이 인덱스를 만들어야 합니다.

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
“lastname” 필드에 인덱스를 만든 후에는 이전 쿼리를 성공적으로 실행할 수 있습니다. Azure Cosmos DB의 Cassandra API를 사용할 때는 인덱스 이름을 제공하지 않아도 됩니다. `tablename_columnname_idx` 형식의 기본 인덱스가 사용됩니다. 예를 들어 이전 테이블의 인덱스 이름은 ` t1_lastname_idx`입니다.

## <a name="dropping-the-index"></a>인덱스 삭제 
인덱스를 삭제할 인덱스 이름을 알고 있어야 합니다. `desc schema` 명령을 실행하여 테이블에 대한 설명을 가져옵니다. 이 명령의 출력에는 `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)` 형식의 인덱스 이름이 포함됩니다. 다음 예제와 같이 인덱스 이름을 사용하여 인덱스를 삭제할 수 있습니다.

```shell
drop index sampleks.t1_lastname_idx;
```



## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB에서 [자동 인덱싱](../index-overview.md)이 작동하는 방식에 대한 자세한 정보
* [Azure Cosmos DB Cassandra API에서 지원하는 Apache Cassandra 기능](cassandra-support.md)
