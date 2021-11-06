---
title: Redis 인스턴스로 Azure Cache를 다른 지역으로 이동
description: Redis 인스턴스에 대 한 Azure 캐시를 다른 Azure 지역으로 이동 하는 방법입니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 8/27/2021
ms.openlocfilehash: 931c4aec854b2512c649570b5fe8fc24bfe11c81
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131894573"
---
# <a name="move-azure-cache-for-redis-instances-to-different-regions"></a>Redis 인스턴스로 Azure Cache를 다른 지역으로 이동

이 문서에서는 Azure Cache for Redis 인스턴스를 다른 Azure 지역으로 이동 하는 방법에 대해 알아봅니다. 여러 가지 이유로 리소스를 다른 지역으로 이동할 수 있습니다.
- 를 사용 하 여 새 Azure 지역을 활용할 수 있습니다.
- 특정 지역 에서만 사용할 수 있는 기능 또는 서비스를 배포 합니다.
- 내부 정책 및 거 버 넌 스 요구 사항을 충족할 수 있습니다.
- 용량 계획 요구 사항에 응답 합니다.

사용 하는 Redis에 대 한 Azure 캐시의 계층에 따라 가장 적합 한 옵션이 결정 됩니다.

| 캐시 계층 | 옵션  | 
| ------------ |  ------- | 
| Premium | 지역에서 복제, 새 캐시 만들기, 두 개의 캐시로 이중 쓰기 또는 RDB 파일을 통해 데이터 내보내기 및 가져오기| 
| 기본 또는 표준 | 두 개의 캐시로 새 캐시 또는 이중 쓰기를 만듭니다.| 
| Enterprise 또는 Enterprise 플래시 | 새 캐시 만들기 또는 RDB 파일을 사용 하 여 데이터 내보내기 및 가져오기 | 

## <a name="geo-replication-premium"></a>지역에서 복제 (Premium)

### <a name="prerequisites"></a>필수 조건 

두 캐시 간에 지역에서 복제를 구성하려면 다음 필수 조건을 충족해야 합니다.

- 두 캐시 모두 [프리미엄 계층](cache-overview.md#service-tiers) 캐시입니다.
- 두 캐시 모두 동일한 Azure 구독에 있습니다.
- 보조 연결 캐시는 동일한 캐시 크기이거나 기본 연결 캐시보다 큰 캐시 크기입니다.
- 두 캐시가 이미 존재 하 고 실행 중입니다.

### <a name="prepare"></a>준비

캐시 인스턴스를 다른 지역으로 이동 하려면 원하는 지역에 [두 번째 프리미엄 캐시 인스턴스를 만들어야](quickstart-create-redis.md) 합니다. 두 캐시를 모두 실행 한 후에는 두 캐시 인스턴스 간의 지역에서 복제를 설정할 수 있습니다. 

> [!NOTE]
> Azure 지역 간의 데이터 전송은 표준 [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구 됩니다.

일부 기능은 지역에서 복제에서 지원되지 않습니다.

- 영역 중복은 지역에서 복제에서 지원되지 않습니다.
- 지속성은 지역에서 복제에서 지원되지 않습니다.

지역에서 복제 지원에 대 한 조건:

- 두 캐시에 모두 클러스터링이 사용 설정되어 있고 분할 수가 같은 경우 클러스터링이 지원됩니다.
- 다른 Vnet의 캐시는 주의 하 여 지원 됩니다. 자세한 내용은 [VNet에서 내 캐시로 지역에서 복제를 사용할 수 있나요?](cache-how-to-geo-replication.md#can-i-use-geo-replication-with-my-caches-in-a-vnet) 를 참조 하세요.

지역에서 복제를 구성한 후에는 연결된 캐시 쌍에 다음과 같은 제한이 적용됩니다.

- 보조 연결 된 캐시는 읽기 전용입니다. 읽을 수는 있지만 데이터를 쓸 수는 없습니다. 
    - Geo-Secondary 인스턴스에서 읽기를 선택 하는 경우 Geo-Primary와 지리적 보조 사이에서 전체 데이터 동기화가 발생할 때마다 (예: Geo-Primary 또는 Geo-Secondary 업데이트 되 고 일부 재부팅 시나리오의 경우) Geo-Secondary 인스턴스는 Geo-Primary와 Geo-Secondary 간의 전체 데이터 동기화가 완료 될 때까지 Redis 작업에 대 한 오류를 throw 합니다.
    - Geo-Secondary에서 읽는 애플리케이션은 Geo-Secondary에서 해당 오류가 throw할 때마다 Geo-Primary로 폴백하도록 빌드해야 합니다.
- 링크를 추가하기 전에 보조 연결된 캐시에 있던 모든 데이터가 제거됩니다. 그러나 나중에 지역에서 복제를 제거하면 복제된 데이터는 보조 연결된 캐시에 유지됩니다.
- 캐시를 연결하는 동안에는 캐시를 [스케일링](cache-how-to-scale.md)할 수 없습니다.
- 캐시에 클러스터링이 사용 설정된 경우에는 [분할 수를 변경](cache-how-to-premium-clustering.md)할 수 없습니다.
- 어느 캐시에서도 지속성을 사용하도록 설정할 수 없습니다.
- 두 캐시에서 [내보낼](cache-how-to-import-export-data.md#export) 수 있습니다.
- 보조 연결 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 없습니다.
- 캐시 연결을 해제할 때까지 연결된 캐시 또는 캐시가 포함된 리소스 그룹을 삭제할 수 없습니다. 자세한 내용은 [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](cache-how-to-geo-replication.md#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)를 참조하세요.
- 캐시가 서로 다른 지역에 있는 경우 네트워크 송신 비용이 지역 간에 이동되는 데이터에 적용됩니다. 자세한 내용은 [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](cache-how-to-geo-replication.md#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)를 참조하세요.
- 자동 장애 조치(failover)는 기본 및 보조 연결 캐시 사이에서 발생하지 않습니다. 클라이언트 애플리케이션을 장애 조치(failover)하는 방법에 대한 자세한 내용 및 정보는 [보조 연결 캐시에 대한 장애 조치(failover) 작동 방식](cache-how-to-geo-replication.md#how-does-failing-over-to-the-secondary-linked-cache-work)을 참조하세요.

### <a name="move"></a>이동

1. 지역에서 복제를 위해 두 캐시를 연결하려면 먼저 기본 연결 캐시로 사용할 캐시의 리소스 메뉴에서 **지역에서 복제** 를 클릭합니다. 그런 다음, 왼쪽에 있는 **지역에서 복제** 에서 **캐시 복제 링크 추가** 를 클릭합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-menu.png" alt-text="링크 추가":::

1. **호환 가능한 캐시** 목록에서 원하는 보조 캐시의 이름을 선택합니다. 보조 캐시가 목록에 표시되지 않으면 보조 캐시에 대한 [지역에서 복제 필수 조건](#prerequisites)이 충족되는지 확인합니다. 지역별로 캐시를 필터링하려면 지도에서 해당 지역을 선택하여 **호환 가능한 캐시** 목록에 해당 캐시만 표시합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link.png" alt-text="지역에서 복제 호환되는 캐시":::

    상황에 맞는 메뉴를 사용하여 연결 프로세스를 시작하거나 보조 캐시에 대한 세부 정보를 볼 수도 있습니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png" alt-text="지역에서 복제 상황에 맞는 메뉴":::

1. **연결** 을 선택하여 두 캐시를 함께 연결하고 복제 프로세스를 시작합니다.
   
    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png" alt-text="캐시 연결":::

### <a name="verify"></a>확인

1. 왼쪽에 있는 **지역에서 복제** 에서 복제 프로세스의 진행률을 볼 수 있습니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-linking.png" alt-text="연결 상태":::

    주 캐시와 보조 캐시에 대한 **개요** 를 사용하여 왼쪽에서 연결 상태를 볼 수도 있습니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-status.png" alt-text="기본 및 보조 캐시의 연결 상태 확인 방법을 보여 주는 스크린샷":::

    복제 프로세스가 완료되면 **링크 상태** 가 **성공** 으로 바뀝니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-successful.png" alt-text="캐시 상태":::

    연결 프로세스 중에도 기본 연결 캐시를 계속 사용할 수 있습니다. 연결 프로세스가 완료될 때까지 보조 연결 캐시를 사용할 수 없습니다.

### <a name="clean-up-source-resources"></a>원본 리소스 정리 

대상 지역의 새 캐시가 필요한 모든 데이터로 채워지면 두 캐시 간의 링크를 제거 하 고 원본 인스턴스를 삭제 합니다.

1. 두 캐시 간의 링크를 제거 하 고 지역에서 복제를 중지 하려면 왼쪽의 지역에서 **복제** 에서 **캐시 연결 해제** 를 클릭 합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-unlink.png" alt-text="캐시 연결 해제":::

    연결 해제 프로세스가 완료되면 보조 캐시를 읽기 및 쓰기에 사용할 수 있습니다.

>[!NOTE]
>지역에서 복제 연결을 제거해도 기본 연결 캐시에서 복제된 데이터는 보조 캐시에 유지됩니다.
>
>

2. 원래 인스턴스를 삭제합니다.

## <a name="create-a-new-cache-all-tiers"></a>새 캐시 만들기 (모든 계층)

### <a name="prerequisites"></a>필수 구성 요소
- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

### <a name="prepare"></a>준비
이동 중에 데이터를 유지할 필요가 없는 경우 영역을 이동 하는 가장 쉬운 방법은 대상 지역에 새 캐시 인스턴스를 만들고 응용 프로그램을 연결 하는 것입니다. 예를 들어 Redis를 데이터베이스 레코드의 조회 캐시로 사용하는 경우 간단하게 캐시를 처음부터 다시 작성할 수 있습니다.

### <a name="move"></a>이동

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

마지막으로 새 인스턴스를 사용 하도록 응용 프로그램을 업데이트 합니다. 

### <a name="clean-up-source-resources"></a>원본 리소스 정리 
대상 영역에서 새 캐시가 실행 되 면 원래 인스턴스를 삭제 합니다.


## <a name="export-and-import-data-with-an-rdb-file-premium-enterprise-enterprise-flash"></a>RDB 파일을 사용 하 여 데이터 내보내기 및 가져오기 (Premium, Enterprise, Enterprise 플래시)
오픈 소스 Redis는 캐시의 메모리 내 데이터 세트 스냅샷을 만들어서 파일에 저장하는 표준 메커니즘을 정의합니다. 이 RDB 파일은 다른 Redis 캐시에서 읽을 수 있습니다. [Redis Premium 및 Enterprise 용 Azure cache](cache-overview.md#service-tiers) 는 RDB 파일을 사용 하 여 캐시 인스턴스로 데이터를 가져오는 것을 지원 합니다. RDB 파일을 사용하여 기존 캐시에서 Azure Cache for Redis로 데이터를 전송할 수 있습니다.

> [!IMPORTANT]
> RDB 파일 형식은 Redis 버전 간에 변경 될 수 있으며 이전 버전과의 호환성을 유지 하지 않을 수 있습니다. 내보내는 캐시의 Redis 버전은 새 캐시 인스턴스의 버전 보다 동일 하거나 더 작아야 합니다.
>

### <a name="prerequisites"></a>필수 조건
- 두 캐시는 모두 [Premium 계층 또는 Enterprise 계층](cache-overview.md#service-tiers) 캐시입니다.
- 두 번째 캐시는 원래 캐시와 동일한 캐시 크기 또는 더 큰 캐시 크기입니다.
- 내보내는 캐시의 Redis 버전은 새 캐시 인스턴스의 버전과 동일하거나 낮아야 합니다.

### <a name="prepare"></a>준비
캐시 인스턴스를 다른 지역으로 이동하려면 원하는 지역에 [두 번째 프리미엄 캐시 인스턴스](quickstart-create-redis.md) 또는 두 번째 엔터프라이즈 캐시 [인스턴스를](quickstart-create-redis-enterprise.md) 만들어야 합니다.

### <a name="move"></a>이동
1. Azure Cache for Redis 데이터를 가져오고 내보내는 방법에 대한 자세한 내용은 [여기를](cache-how-to-import-export-data.md) 참조하세요.

2. 새 캐시 인스턴스를 사용하도록 애플리케이션을 업데이트합니다.

### <a name="verify"></a>확인
Azure Portal의 알림에 따르거나 [감사 로그](../azure-monitor/essentials/activity-log.md)의 이벤트를 확인하여 가져오기 작업의 진행 상황을 모니터링할 수 있습니다.

### <a name="clean-up-source-resources"></a>원본 리소스 정리 
대상 지역의 새 캐시가 실행되면 원래 인스턴스를 삭제합니다.

## <a name="dual-write-to-two-caches-basic-standard-and-premium"></a>두 캐시에 이중 쓰기(기본, 표준 및 Premium)
캐시 간에 데이터를 직접 이동하는 대신 애플리케이션을 사용하여 기존 캐시와 설정 중인 새 캐시 모두에 데이터를 쓸 수 있습니다. 애플리케이션은 처음에 기존 캐시에서 데이터를 읽습니다. 새 캐시에 필요한 데이터가 생기면 애플리케이션을 해당 캐시로 전환하고 이전 캐시를 사용 중지합니다. 예를 들어 Redis를 세션 저장소로 사용하며, 애플리케이션 세션은 7일 동안 유효하다고 가정해 보겠습니다. 일주일 동안 2개의 캐시에 쓰면 새 캐시에 만료되지 않은 모든 세션 정보가 포함됩니다. 해당 시점부터 데이터 손실에 대한 걱정 없이 안전하게 새 캐시를 사용할 수 있습니다.

### <a name="prerequisites"></a>필수 조건
- 두 번째 캐시는 원래 캐시와 동일한 캐시 크기 또는 더 큰 캐시 크기입니다.

### <a name="prepare"></a>준비
캐시 인스턴스를 다른 지역으로 이동하려면 원하는 지역에 [두 번째 캐시 인스턴스를 만들어야](quickstart-create-redis.md) 합니다.

### <a name="move"></a>이동
이 옵션을 구현하는 일반적인 단계는 다음과 같습니다.

1. 새 인스턴스와 원래 인스턴스 모두에 쓰도록 애플리케이션 코드를 수정합니다.

2. 새 인스턴스에 데이터가 충분히 채워질 때까지 계속해서 원래 인스턴스에서 데이터를 읽습니다.

3. 새 인스턴스에서만 데이터를 읽고 쓰도록 애플리케이션 코드를 업데이트합니다.

### <a name="clean-up-source-resources"></a>원본 리소스 정리 
대상 지역의 새 캐시가 실행되면 원래 인스턴스를 삭제합니다.

## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 기능에 대해 자세히 알아보세요.
- [지역에서 복제 FAQ](cache-how-to-geo-replication.md#geo-replication-faq)
- [Azure Cache for Redis - 서비스 계층](cache-overview.md#service-tiers)
- [Azure Cache for Redis의 고가용성](cache-high-availability.md)


