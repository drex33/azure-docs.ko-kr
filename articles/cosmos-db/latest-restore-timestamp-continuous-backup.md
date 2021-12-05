---
title: 최신 복원 가능한 타임 스탬프, 사용 사례, Azure Cosmos DB 컨테이너에 대 한 예제
description: 최신 복원 가능한 timestamp API는 연속 모드 백업을 사용 하는 계정의 컨테이너에 대 한 최신 복원 가능한 타임 스탬프를 제공 합니다. 이 API를 사용 하 여 라이브 계정 복원을 트리거하거나 백업 중인 데이터를 모니터링 하는 복원 가능한 타임 스탬프를 가져올 수 있습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 12/03/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: 23e14499c390ca0915d82ade14f5df7c3a5ee5c1
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570728"
---
# <a name="latest-restorable-timestamp-for-azure-cosmos-db-accounts-with-continuous-backup-mode"></a>연속 백업 모드를 사용 하는 Azure Cosmos DB 계정의 최신 복원 가능한 타임 스탬프
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB는 컨테이너의 최신 복원 가능한 타임 스탬프를 가져오는 API를 제공 합니다. 이 API는 연속 백업 모드를 사용 하는 계정에 사용할 수 있습니다. 최신 복원 가능한 타임 스탬프는 데이터가 성공적으로 백업 된 UTC 형식의 최신 타임 스탬프를 나타냅니다. 이 API를 사용 하 여 복원 가능한 타임 스탬프를 가져와 라이브 계정 복원 또는 데이터를 시간에 백업 하는 모니터를 트리거할 수 있습니다.

이 API는 또한 계정 위치를 입력 매개 변수로 사용 하 고이 위치에서 지정 된 컨테이너에 대 한 최신 복원 가능한 타임 스탬프를 반환 합니다. 계정이 여러 위치에 있는 경우 각 위치의 백업이 독립적으로 수행 되므로 다른 위치에 있는 컨테이너에 대 한 최신 복원 가능한 타임 스탬프가 다를 수 있습니다.

기본적으로 API는 컨테이너 수준 에서만 작동 하지만 데이터베이스 또는 계정 수준에서 작동 하도록 쉽게 확장할 수 있습니다. 이 문서는 최신 복원 가능한 timestamp api의 의미 체계를 이해 하 고 계산 및 사용 사례를 이해 하는 데 도움이 됩니다. 자세히 알아보려면 SQL 및 MongoDB 계정에 대 한 [최신 복원 타임 스탬프를 가져오는 방법](get-latest-restore-timestamp.md) 을 참조 하세요.

## <a name="use-cases"></a>사용 사례

다음 사용 사례에서 최신 복원 가능한 타임 스탬프를 사용할 수 있습니다.

* 컨테이너, 데이터베이스 또는 계정에 대 한 최신 복원 가능한 타임 스탬프를 가져오고이를 사용 하 여 복원을 트리거할 수 있습니다. 지정 된 리소스의 모든 데이터 또는 모든 기본 리소스가 성공적으로 백업 된 최신 타임 스탬프입니다.

* 이 API를 사용 하 여 계정을 삭제 하기 전에 데이터가 성공적으로 백업 되었음을 식별할 수 있습니다. 이 API에서 반환 된 타임 스탬프가 마지막 쓰기 타임 스탬프 보다 작은 경우 아직 백업 되지 않은 일부 데이터가 있음을 의미 합니다. 이러한 경우 타임 스탬프가 마지막 쓰기 타임 스탬프 보다 크거나 같을 때까지이 API를 호출 해야 합니다. 계정이 여러 위치에 있는 경우 계정을 삭제 하기 전에 모든 지역에서 데이터가 백업 되도록 모든 위치에서 최신 복원 가능한 타임 스탬프를 가져와야 합니다.

* 이 API를 사용 하 여 데이터를 시간에 백업 하 고 있는지 모니터링할 수 있습니다. 이 타임 스탬프는 일반적으로 더 다를 수 있지만, 경우에 따라 현재 타임 스탬프의 몇 백 초 내에 있습니다.

## <a name="semantics"></a>의미 체계

컨테이너의 최신 복원 가능한 타임 스탬프는 지정 된 위치에서 모든 파티션이 백업을 성공적으로 수행 하는 데 필요한 최소 타임 스탬프입니다. 이 Api는 지정 된 위치에서 지정 된 컨테이너의 각 파티션에 대 한 최신 백업 타임 스탬프를 검색 하 여 최신 복원 가능한 타임 스탬프를 계산 하 고 이러한 타임 스탬프의 최소값을 반환 합니다. 모든 파티션에 대 한 데이터를 백업 하 고 해당 파티션에 기록 된 새 데이터가 없는 경우 최신 타임 스탬프 및 마지막 데이터 백업 타임 스탬프의 최대값을 반환 합니다.

파티션이 아직 백업을 수행 하지 않았지만 일부 데이터를 백업 해야 하는 경우에는 최소 Unix (epoch) 타임 스탬프, 즉 1 월 1 일 1970, 자정 UTC (협정 세계시)를 반환 합니다. 이러한 경우에는 사용자가 epoch 타임 스탬프 보다 큰 타임 스탬프를 제공할 때까지 다시 시도해 야 합니다.

## <a name="latest-restorable-timestamp-calculation"></a>최신 복원 가능한 timestamp 계산

다음 예제에서는 다양 한 시나리오에서 최신 복원 가능한 timestamp Api의 예상 결과를 설명 합니다. 각 시나리오에서는 파티션의 현재 로그 백업 상태, 백업할 보류 중인 데이터 및 컨테이너에 대 한 전반적인 최신 복원 가능한 timestamp 계산에 미치는 영향에 대해 설명 합니다.

두 지역 (미국 동부 및 미국 서 부)에 존재 하는 계정이 있다고 가정해 보겠습니다. 파티션 2 개 (Partition1 및 파티션 2)가 있는 "cont1" 컨테이너가 있습니다. 이 컨테이너의 타임 스탬프에 대 한 최신 복원 가능한 타임 스탬프를 가져오기 위해 요청을 보내는 경우이 컨테이너의 전체 최신 복원 가능한 타임 스탬프는 다음과 같이 계산 됩니다.

##### <a name="case1-data-for-all-the-partitions-has-not-been-backed-up-yet"></a>Case1: 모든 파티션에 대 한 데이터가 아직 백업 되지 않았습니다.

*미국 동부 지역:*

* 파티션 1: 마지막 backup time = t2 이지만 t2 이후에 백업할 데이터가 일부 있습니다.
* 파티션 2: 마지막 백업 시간 = t3 및 모든 데이터를 백업 합니다.
* 최신 복원 가능한 timestamp = min (t2, t3) = t2

*미국 서 부 지역:*

* 파티션 1: 마지막 백업 시간 = t1 이지만 t1 이후에 백업할 데이터가 몇 가지 있습니다.
* 파티션 2: 마지막 백업 시간 = t2 이지만 t2 이후에 백업할 데이터가 일부 있습니다.
* 최신 복원 가능한 timestamp = min (t1, t2) = t1

##### <a name="case2-data-for-all-the-partitions-is-backed-up"></a>Case2: 모든 파티션에 대 한 데이터를 백업 합니다.

*미국 동부 지역:*

* 파티션 1: 마지막 백업 시간 = t2 및 모든 데이터를 백업 합니다.
* 파티션 2: 마지막 백업 시간 = t3 및 모든 데이터를 백업 합니다.
* 최신 복원 가능한 timestamp = max (current timestamp, t2, t3)

*미국 서 부 지역:*

* 파티션 1: 마지막 백업 시간 = t3 및 모든 데이터를 백업 합니다.
* 파티션 2: 마지막 백업 시간 = t3 및 모든 데이터를 백업 합니다.
* 최신 복원 가능한 timestamp = max (현재 타임 스탬프, t3, t3)

##### <a name="case3-when-one-or-more-partitions-has-not-taken-any-backup-yet"></a>Case3: 하나 이상의 파티션이 아직 백업을 수행 하지 않은 경우

*미국 동부 지역:*

* 파티션 1:이 파티션에 대해 아직 로그 백업이 수행 되지 않았습니다.
* 파티션 2: 마지막 백업 시간 = t3
* 최신 복원 가능한 타임 스탬프 = 오전 1/1/1970 12:00:00

## <a name="frequently-asked-questions"></a>질문과 대답

#### <a name="can-i-use-this-api-for-accounts-with-periodic-backup"></a>정기적 백업이 있는 계정에이 API를 사용할 수 있나요?
아니요. 이 API는 연속 백업 모드의 계정에만 사용할 수 있습니다.

#### <a name="can-i-use-this-api-for-accounts-migrated-to-continuous-mode"></a>연속 모드로 마이그레이션된 계정에이 API를 사용할 수 있나요?
예. 이 API는 연속 백업 모드로 프로 비전 된 계정이 나 연속 백업 모드로 성공적으로 마이그레이션된 계정에 사용할 수 있습니다.

#### <a name="what-is-the-typical-delay-between-the-latest-write-timestamp-and-the-latest-restorable-timestamp"></a>최신 쓰기 타임 스탬프와 최신 복원 가능한 타임 스탬프 간의 일반적인 지연은 무엇 인가요?
로그 백업 데이터는 100 초 마다 백업 됩니다. 그러나 일부 예외적인 경우에는 100 초 넘게 백업이 지연 될 수 있습니다.

#### <a name="will-restorable-timestamp-work-for-deleted-accounts"></a>삭제 된 계정에 대 한 타임 스탬프 작동을 복원 가능한?
아니요. 이는 라이브 계정에만 적용 됩니다. 복원 가능한 타임 스탬프를 가져와서 라이브 계정 복원 또는 데이터를 시간에 백업 하는 모니터를 트리거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [지정 시간 복원을 사용한 연속 백업 모드 소개](continuous-backup-restore-introduction.md)

* [연속 백업 모드 리소스 모델](continuous-backup-restore-resource-model.md)

* Azure Portal를 사용 하 여 [연속 백업 모드를 구성 및 관리](continuous-backup-restore-portal.md) 합니다.
