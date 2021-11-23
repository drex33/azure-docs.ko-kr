---
title: 복원 가능한 최신 타임스탬프, 사용 사례, Azure Cosmos DB 컨테이너 예제
description: 복원 가능한 최신 타임스탬프 API는 연속 모드 백업이 있는 계정의 컨테이너에 대해 복원 가능한 최신 타임스탬프를 제공합니다. 이 API를 사용하면 복원 가능한 타임스탬프를 통해 라이브 계정 복원을 트리거하거나 백업 중인 데이터를 모니터링할 수 있습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/22/2021
ms.author: sngun
ms.topic: how-to
ms.reviewer: sngun
ms.openlocfilehash: ae7c503c2dd5ab7502c72111966ba7df8eba75a9
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132943473"
---
# <a name="latest-restorable-timestamp-for-azure-cosmos-db-accounts-with-continuous-backup-mode"></a>연속 백업 모드가 있는 Azure Cosmos DB 계정에 대한 복원 가능한 최신 타임스탬프
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

Azure Cosmos DB는 컨테이너의 복원 가능한 최신 타임스탬프를 얻기 위한 API를 제공합니다. 이 API는 연속 백업을 사용하도록 설정된 계정에 사용할 수 있습니다. 복원 가능한 최신 타임스탬프는 데이터가 성공적으로 백업된 UTC 형식의 최신 타임스탬프를 나타냅니다. 이 API를 사용하면 복원 가능한 타임스탬프를 통해 라이브 계정 복원을 트리거하거나 성공적으로 백업되는 데이터를 모니터링할 수 있습니다.

이 API는 계정 위치를 입력 매개 변수로 사용하며 이 위치의 컨테이너에 대해 복원 가능한 최신 타임스탬프를 반환합니다. 계정이 여러 위치에 있는 경우 다른 위치에 있는 컨테이너에 대해 복원 가능한 최신 타임스탬프가 다를 수 있습니다. 다른 계정 위치의 백업은 독립적으로 수행되므로

기본적으로 API는 컨테이너 수준에서만 작동하지만 전체 데이터베이스 또는 계정에 대해 쉽게 확장할 수 있습니다. 데이터베이스 또는 컨테이너에 대해 복원 가능한 최신 타임스탬프는 지정된 데이터베이스 또는 계정에 대한 모든 파티션이 입력으로 전달된 위치에 백업이 있는 최소 타임스탬프입니다. 이 문서에서는 최신 복원 타임스탬프가 필요한 사용 사례를 설명하고 예제를 제공합니다. 자세한 내용은 SQL 및 MongoDB 계정에 대한 [최신 복원 타임스탬프를 얻는 방법을](get-latest-restore-timestamp.md) 참조하세요.

## <a name="use-cases"></a>사용 사례

다음과 같은 사용 사례에서 최신 복원 가능한 타임스탬프를 사용할 수 있습니다.

* 컨테이너, 데이터베이스 또는 계정에 대해 복원 가능한 최신 타임스탬프를 얻고 복원을 트리거하는 데 사용할 수 있습니다. 지정된 리소스 또는 모든 기본 리소스의 모든 데이터가 성공적으로 백업된 최신 타임스탬프입니다.

* 이 API를 사용하여 계정을 삭제하기 전에 데이터가 성공적으로 기록되었는지 식별할 수 있습니다. 이 API에서 반환된 타임스탬프가 마지막 쓰기 타임스탬프보다 작으면 아직 백업되지 않은 일부 데이터가 있음을 의미합니다. 이러한 경우 타임스탬프가 마지막 쓰기 타임스탬프보다 크거나 같아질 때까지 이 API를 호출해야 합니다. 계정이 여러 위치에 있는 경우 계정을 삭제하기 전에 모든 지역에서 데이터가 백업되었는지 확인하기 위해 모든 위치에서 복원 가능한 최신 타임스탬프를 받아야 합니다.

* 이 API를 사용하여 데이터가 제때 백업되고 있는지 모니터링할 수 있습니다. 이 타임스탬프는 현재 타임스탬프에서 몇 백 초 이내입니다. 경우에 따라 약간 더 오래 걸릴 수 있습니다.

## <a name="semantics"></a>의미 체계

복원 가능한 최신 타임스탬프 API는 데이터가 마지막으로 백업된 컨테이너의 모든 파티션에 대한 최신 시간을 가져오고 이러한 모든 타임스탬프의 최소값을 반환합니다. 모든 파티션에 대한 데이터가 백업되고 해당 파티션에 기록된 새 데이터가 없는 경우 현재 타임스탬프의 최대값과 마지막 데이터 백업 타임스탬프를 반환합니다. 이 API는 반환된 타임스탬프에서 특정 컨테이너의 모든 파티션이 백업되었는지 확인합니다.

파티션이 아직 백업을 수행하지 않았거나 백업할 데이터가 있는 경우 최소 Unix(epoch) 타임스탬프 즉, 1970년 1월 1일 자정 UTC(협정 세계시)를 반환합니다. 이 경우 epoch 타임스탬프보다 큰 타임스탬프를 제공할 때까지 다시 시도해야 합니다.

## <a name="examples"></a>예

컨테이너 "cont1"에 "미국 동부" 및 "미국 서부"의 두 개의 파티션이 있는 경우 타임스탬프 t3에서 요청을 보내 복원 가능한 최신 타임스탬프를 받는 것으로 가정합니다.

### <a name="case1-data-for-all-the-partitions-has-not-yet-been-backed-up"></a>사례1: 모든 파티션에 대한 데이터가 아직 백업되지 않았습니다.

**미국 동부:**

* 파티션 1(마지막 백업 시간: t2)
* 파티션 2(마지막 백업 시간: t3)
* 최신 복원 가능한 타임스탬프 = min(t2, t3) = t2

**미국 서부:**

* 파티션 1(마지막 백업 시간: t1)
* 파티션 2(마지막 백업 시간: t3)
* 최신 복원 가능한 타임스탬프 = min(t1, t3) = t1

### <a name="case2-data-for-all-partitions-is-backed-up"></a>사례2: 모든 파티션에 대한 데이터가 백업됩니다.

**미국 동부:**

* 파티션 1(마지막 백업 시간: t2)(이 파티션에 대한 모든 데이터가 t2에서 백업되었으며 새 데이터가 기록되지 않았습니다.)
* 파티션 2(마지막 백업 시간: t3)
* 최신 복원 가능한 타임스탬프 = max(현재 타임스탬프, t2, t3)

**미국 서부:**

* 파티션 1(마지막 백업 시간: t3)
* 파티션 2(마지막 백업 시간: t3)
* 최신 복원 가능한 타임스탬프 = max(현재 타임스탬프, t3, t3)

### <a name="case3-when-one-of-the-partitions-has-not-taken-any-backup-yet"></a>사례3: 파티션 중 하나가 아직 백업을 수행하지 않은 경우

**미국 동부:**

* 파티션 1: (아직 백업이 수행되지 않았습니다.)
* 파티션 2: (마지막 백업 시간: t3)
* 최신 복원 가능한 타임스탬프 = 1970/1/1 오전 12:00:00

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-use-this-api-for-accounts-with-periodic-backup"></a>주기적 백업이 있는 계정에 이 API를 사용할 수 있나요?

아니요. 이 API는 연속 백업 모드가 있는 계정에만 사용할 수 있습니다.

### <a name="can-i-use-this-api-for-accounts-migrated-to-continuous-mode"></a>연속 모드로 마이그레이션된 계정에 이 API를 사용할 수 있나요?

예.

### <a name="what-is-the-typical-delay-between-the-latest-write-timestamp-and-the-latest-restorable-timestamp"></a>최신 쓰기 타임스탬프와 복원 가능한 최신 타임스탬프 간의 일반적인 지연은 무엇인가요?

일반적으로 데이터는 100초 이내에 백업됩니다(즉, 1분 반 이내) 데이터 쓰기 작업 후 그러나 일부 예외적인 경우에는 백업이 100초 이상 지연될 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [시점 복원을 사용하여 연속 백업 모드 소개](continuous-backup-restore-introduction.md)

* [연속 백업 모드 리소스 모델](continuous-backup-restore-resource-model.md)

* Azure Portal 사용하여 [연속 백업 모드를 구성하고 관리합니다.](continuous-backup-restore-portal.md)