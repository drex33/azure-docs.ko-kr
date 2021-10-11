---
title: 프리미엄 Azure Cache for Redis 인스턴스에 대한 지역에서 복제 구성
description: Azure 지역 간에 Azure Cache for Redis 프리미엄 인스턴스를 복제하는 방법을 알아봅니다.
author: curib
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: cauribeg
ms.openlocfilehash: 989284bd10fc5d452a738d027c693a15f7871b9b
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129730009"
---
# <a name="configure-geo-replication-for-premium-azure-cache-for-redis-instances"></a>프리미엄 Azure Cache for Redis 인스턴스에 대한 지역에서 복제 구성

이 문서에서는 Azure Portal을 사용하여 지리적으로 복제된 Azure Cache의 구성 방법을 알아봅니다.

지역에서 복제는 두 개의 프리미엄 Azure Cache for Redis 인스턴스를 함께 연결하고 데이터 복제 관계를 만듭니다. 해당 캐시 인스턴스는 일반적으로 다른 Azure 지역에 있지만 반드시 필요한 것은 아닙니다. 하나의 인스턴스가 기본 인스턴스로 사용되고 다른 인스턴스가 보조로 사용됩니다. 기본 인스턴스는 읽기 및 쓰기 요청을 처리하고 변경 내용을 보조로 전파합니다. 이 프로세스는 두 인스턴스 간의 연결이 제거될 때까지 계속됩니다.

> [!NOTE]
> 지역에서 복제는 재해 복구 솔루션으로 만들어졌습니다.
>
>

## <a name="geo-replication-prerequisites"></a>지역에서 복제 필수 조건

두 캐시 간에 지역에서 복제를 구성하려면 다음 필수 조건을 충족해야 합니다.

- 두 캐시 모두 [프리미엄 계층](cache-overview.md#service-tiers) 캐시입니다.
- 두 캐시 모두 동일한 Azure 구독에 있습니다.
- 보조 연결 캐시는 동일한 캐시 크기이거나 기본 연결 캐시보다 큰 캐시 크기입니다.
- 두 캐시가 생성되고 실행 중인 상태입니다.

> [!NOTE]
> Azure 지역 간 데이터 전송은 표준 [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구됩니다.

일부 기능은 지역에서 복제에서 지원되지 않습니다.

- 영역 중복은 지역에서 복제에서 지원되지 않습니다.
- 지속성은 지역에서 복제에서 지원되지 않습니다.
- 두 캐시에 모두 클러스터링이 사용 설정되어 있고 분할 수가 같은 경우 클러스터링이 지원됩니다.
- 동일한 Virtual Network(VNet)의 캐시가 지원됩니다.
- 다른 VNet의 캐시는 주의할 사항과 함께 지원됩니다. 자세한 내용은 [VNet에서 내 캐시와 함께 지역에서 복제를 사용할 수 있나요?를](#can-i-use-geo-replication-with-my-caches-in-a-vnet) 참조하세요.

지역에서 복제를 구성한 후에는 연결된 캐시 쌍에 다음과 같은 제한이 적용됩니다.

- 보조 연결된 캐시는 읽기 전용입니다. 이 캐시에서 읽을 수는 있지만 데이터를 쓸 수 없습니다. Geo-Secondary 인스턴스에서 읽기를 선택하는 경우 Geo-Primary와 Geo-Secondary 간에 전체 데이터 동기화가 발생할 때마다(Geo-Primary 또는 Geo-Secondary가 업데이트되고 일부 재부팅 시나리오에서도 발생함), Geo-Secondary 인스턴스는 Geo-Primary와 Geo-Secondary 간의 전체 데이터 동기화가 완료될 때까지 모든 Redis 작업에 대해 오류(전체 데이터 동기화가 진행 중임을 나타냄)를 throw합니다. Geo-Secondary에서 읽는 애플리케이션은 Geo-Secondary에서 해당 오류가 throw할 때마다 Geo-Primary로 폴백하도록 빌드해야 합니다.
- 링크를 추가하기 전에 보조 연결된 캐시에 있던 모든 데이터가 제거됩니다. 그러나 나중에 지역에서 복제를 제거하면 복제된 데이터는 보조 연결된 캐시에 유지됩니다.
- 캐시를 연결하는 동안에는 캐시를 [스케일링](cache-how-to-scale.md)할 수 없습니다.
- 캐시에 클러스터링이 사용 설정된 경우에는 [분할 수를 변경](cache-how-to-premium-clustering.md)할 수 없습니다.
- 어느 캐시에서도 지속성을 사용하도록 설정할 수 없습니다.
- 두 캐시에서 [내보낼](cache-how-to-import-export-data.md#export) 수 있습니다.
- 보조 연결 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 없습니다.
- 캐시 연결을 해제할 때까지 연결된 캐시 또는 캐시가 포함된 리소스 그룹을 삭제할 수 없습니다. 자세한 내용은 [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)를 참조하세요.
- 캐시가 서로 다른 지역에 있는 경우 네트워크 송신 비용이 지역 간에 이동되는 데이터에 적용됩니다. 자세한 내용은 [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)를 참조하세요.
- 자동 장애 조치(failover)는 기본 및 보조 연결 캐시 사이에서 발생하지 않습니다. 클라이언트 애플리케이션을 장애 조치(failover)하는 방법에 대한 자세한 내용 및 정보는 [보조 연결 캐시에 대한 장애 조치(failover) 작동 방식](#how-does-failing-over-to-the-secondary-linked-cache-work)을 참조하세요.

## <a name="add-a-geo-replication-link"></a>지역에서 복제 연결 추가

1. 지역에서 복제를 위해 두 캐시를 연결하려면 먼저 기본 연결 캐시로 사용할 캐시의 리소스 메뉴에서 **지역에서 복제** 를 클릭합니다. 그런 다음, 왼쪽에 있는 **지역에서 복제** 에서 **캐시 복제 링크 추가** 를 클릭합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-menu.png" alt-text="캐시 지역 복제 메뉴":::

1. **호환 가능한 캐시** 목록에서 원하는 보조 캐시의 이름을 선택합니다. 보조 캐시가 목록에 표시되지 않으면 보조 캐시에 대한 [지역에서 복제 필수 조건](#geo-replication-prerequisites)이 충족되는지 확인합니다. 지역별로 캐시를 필터링하려면 지도에서 해당 지역을 선택하여 **호환 가능한 캐시** 목록에 해당 캐시만 표시합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link.png" alt-text="호환되는 캐시 선택":::

    상황에 맞는 메뉴를 사용하여 연결 프로세스를 시작하거나 보조 캐시에 대한 세부 정보를 볼 수도 있습니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-select-link-context-menu.png" alt-text="지역에서 복제 상황에 맞는 메뉴":::

1. **연결** 을 선택하여 두 캐시를 함께 연결하고 복제 프로세스를 시작합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-confirm-link.png" alt-text="캐시 연결":::

1. 왼쪽에 있는 **지역에서 복제** 에서 복제 프로세스의 진행률을 볼 수 있습니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-linking.png" alt-text="연결 상태":::

    주 캐시와 보조 캐시에 대한 **개요** 를 사용하여 왼쪽에서 연결 상태를 볼 수도 있습니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-status.png" alt-text="기본 및 보조 캐시의 연결 상태 확인 방법을 보여 주는 스크린샷":::

    복제 프로세스가 완료되면 **링크 상태** 가 **성공** 으로 바뀝니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-link-successful.png" alt-text="캐시 상태":::

    연결 프로세스 중에도 기본 연결 캐시를 계속 사용할 수 있습니다. 연결 프로세스가 완료될 때까지 보조 연결 캐시를 사용할 수 없습니다.

## <a name="remove-a-geo-replication-link"></a>지역에서 복제 연결 제거

1. 두 캐시 간 연결을 제거하고 지역에서 복제를 중지하려면 왼쪽에 있는 **지역에서 복제** 에서 **캐시 연결 해제** 를 클릭합니다.

    :::image type="content" source="media/cache-how-to-geo-replication/cache-geo-location-unlink.png" alt-text="캐시 연결 해제":::

    연결 해제 프로세스가 완료되면 보조 캐시를 읽기 및 쓰기에 사용할 수 있습니다.

>[!NOTE]
>지역에서 복제 연결을 제거해도 기본 연결 캐시에서 복제된 데이터는 보조 캐시에 유지됩니다.
>
>

## <a name="geo-replication-faq"></a>지역에서 복제 FAQ

- [표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-a-standard-or-basic-tier-cache)
- [연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?](#is-my-cache-available-for-use-during-the-linking-or-unlinking-process)
- [둘 이상의 캐시를 함께 연결할 수 있나요?](#can-i-link-more-than-two-caches-together)
- [서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-from-different-azure-subscriptions)
- [크기가 다른 두 캐시를 연결할 수 있나요?](#can-i-link-two-caches-with-different-sizes)
- [클러스터링이 설정된 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-clustering-enabled)
- [VNet에서 내 캐시와 함께 지역에서 복제를 사용할 수 있나요?](#can-i-use-geo-replication-with-my-caches-in-a-vnet)
- [Redis 지역에서 복제의 복제 일정이란?](#what-is-the-replication-schedule-for-redis-geo-replication)
- [지역에서 복제에 걸리는 기간은 얼마나 되나요?](#how-long-does-geo-replication-replication-take)
- [복제 복구 지점이 보장되나요?](#is-the-replication-recovery-point-guaranteed)
- [PowerShell 또는 Azure CLI를 사용하여 지역에서 복제를 관리할 수 있나요?](#can-i-use-powershell-or-azure-cli-to-manage-geo-replication)
- [Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-regions)
- [연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?](#why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache)
- [보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?](#what-region-should-i-use-for-my-secondary-linked-cache)
- [보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?](#how-does-failing-over-to-the-secondary-linked-cache-work)
- [지역에서 복제를 사용하여 방화벽을 구성할 수 있나요?](#can-i-configure-a-firewall-with-geo-replication)

### <a name="can-i-use-geo-replication-with-a-standard-or-basic-tier-cache"></a>표준 또는 기본 계층 캐시에서 지역에서 복제를 사용할 수 있나요?

아니요, 지역에서 복제는 프리미엄 계층 캐시에만 사용할 수 있습니다.

### <a name="is-my-cache-available-for-use-during-the-linking-or-unlinking-process"></a>연결 또는 연결 해제 프로세스 중에 캐시를 사용할 수 있나요?

- 연결할 때 연결 프로세스가 완료되는 동안 기본 연결 캐시는 계속 사용할 수 있습니다.
- 연결할 때 연결 프로세스가 완료될 때까지 보조 연결 캐시는 사용할 수 없습니다.
- 연결을 해제할 때 연결 해제 프로세스가 완료되는 동안 두 캐시 모두 계속 사용할 수 있습니다.

### <a name="can-i-link-more-than-two-caches-together"></a>둘 이상의 캐시를 함께 연결할 수 있나요?

아니요. 캐시는 두 개만 연결할 수 있습니다.

### <a name="can-i-link-two-caches-from-different-azure-subscriptions"></a>서로 다른 Azure 구독의 두 캐시를 연결할 수 있나요?

아니요, 두 캐시 모두 동일한 Azure 구독에 있어야 합니다.

### <a name="can-i-link-two-caches-with-different-sizes"></a>크기가 다른 두 캐시를 연결할 수 있나요?

예, 보조 연결된 캐시가 주 연결된 캐시보다 큰 경우 연결할 수 있습니다.

### <a name="can-i-use-geo-replication-with-clustering-enabled"></a>클러스터링이 설정된 지역에서 복제를 사용할 수 있나요?

예, 두 캐시의 분할된 데이터베이스 수가 동일한 경우 사용할 수 있습니다.

### <a name="can-i-use-geo-replication-with-my-caches-in-a-vnet"></a>VNet에서 내 캐시와 함께 지역에서 복제를 사용할 수 있나요?

예, VNet에서 캐시의 지역 복제는 다음과 같은 주의 사항과 함께 지원됩니다.

- 동일한 VNet의 캐시 간에 지역에서 복제가 지원됩니다.
- 다른 VNet의 캐시 간 지역에서 복제도 지원됩니다.
  - VNet이 동일한 지역에 있는 경우 [VNet 피어링](../virtual-network/virtual-network-peering-overview.md) 또는 VPN Gateway [VNet-VNet 연결을](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)사용하여 연결할 수 있습니다.
  - VNet이 다른 지역에 있는 경우 VNet 피어링을 사용한 지역에서 복제가 지원되지만 VNet 1(지역 1)의 클라이언트 VM은 기본 내부 부하 분산 장치를 사용하는 제약 조건으로 인해 DNS 이름을 사용하여 VNet 2(지역 2)의 캐시에 액세스할 수 없습니다. VNet 피어링 제약 조건에 대한 자세한 내용은 [Virtual Network - 피어링 - 요구 사항 및 제약 조건을 참조하세요.](../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints) VPN Gateway VNet 대 VNet 연결을 사용하는 것이 좋습니다.
  
[이 Azure 템플릿](https://azure.microsoft.com/resources/templates/redis-vnet-geo-replication/)를 사용하면 VPN Gateway VNet 간 연결로 연결된 VNet에 두 개의 지역 복제 캐시를 신속하게 배포할 수 있습니다.

### <a name="what-is-the-replication-schedule-for-redis-geo-replication"></a>Redis 지역에서 복제의 복제 일정이란?

복제는 연속적이고 비동기입니다. 특정 일정에 따라 발생하지 않습니다. 기본 복제본에 대해 수행된 모든 쓰기는 보조 복제본에 즉시 비동기적으로 복제됩니다.

### <a name="how-long-does-geo-replication-replication-take"></a>지역에서 복제에 걸리는 기간은 얼마나 되나요?

복제는 점진적이고, 비동기적이고, 연속되며, 소요되는 시간은 지역 간 대기 시간과는 크게 다르지 않습니다. 특정 상황에서는 주 캐시에서 데이터의 전체 동기화를 수행하는 데 보조 캐시가 필요할 수 있습니다. 이 경우 복제 시간은 주 캐시의 로드, 사용 가능한 네트워크 대역폭 및 지역 간 대기 시간과 같은 여러 요인에 따라 달라집니다. 전체 53GB의 지역에서 복제 쌍에 대한 복제 시간이 5분에서 10분 사이인 것으로 확인했습니다.

### <a name="is-the-replication-recovery-point-guaranteed"></a>복제 복구 지점이 보장되나요?

지역에서 복제 모드인 캐시의 경우 지속성이 지원되지 않습니다. 지역에서 복제된 쌍의 연결을 해제할 경우(예: 고객이 시작한 장애 조치(failover)) 보조 연결 캐시는 동기화된 데이터를 해당 시점까지 유지합니다. 이와 같은 상황에서 복구 지점은 보장되지 않습니다.

복구 지점을 확보하려면 두 캐시에서 [내보냅니다](cache-how-to-import-export-data.md#export). 나중에 기본 연결 캐시로 [가져올](cache-how-to-import-export-data.md#import) 수 있습니다.

### <a name="can-i-use-powershell-or-azure-cli-to-manage-geo-replication"></a>PowerShell 또는 Azure CLI를 사용하여 지역에서 복제를 관리할 수 있나요?

네. 지역에서 복제는 Azure Portal, PowerShell 또는 Azure CLI로 관리할 수 있습니다. 자세한 내용은 [PowerShell 문서](/powershell/module/az.rediscache/#redis_cache) 또는 [Azure CLI 문서](/cli/azure/redis/server-link)를 참조하세요.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-regions"></a>Azure 지역 간에 데이터를 복제하는 비용은 어느 정도인가요?

지역에서 복제를 사용하면 기본 연결 캐시의 데이터가 보조 연결 캐시에 복제됩니다. 두 연결 캐시가 동일한 지역에 있는 경우에는 데이터 전송에 대한 요금이 부과되지 않습니다. 두 연결 캐시가 서로 다른 지역에 있는 경우에는 지역을 이용하는 데이터의 네트워크 송신 비용이 데이터 전송 요금으로 부과됩니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

### <a name="why-did-the-operation-fail-when-i-tried-to-delete-my-linked-cache"></a>연결된 캐시를 삭제하려고 할 때 작업이 실패한 이유는 무엇인가요?

지역에서 복제 연결을 제거할 때까지 연결된 상태에서는 지역에서 복제된 캐시와 해당 리소스 그룹을 삭제할 수 없습니다. 연결된 캐시 중 하나 또는 둘 다를 포함하는 리소스 그룹을 삭제하려고 하면, 리소스 그룹의 다른 리소스는 삭제되지만 해당 리소스 그룹은 `deleting` 상태로 유지되고 이 리소스 그룹의 연결된 캐시는 모두 `running` 상태로 남아 있습니다. 리소스 그룹 및 내부의 연결된 캐시를 완전히 삭제하려면 [지역에서 복제 링크 제거](#remove-a-geo-replication-link)에서 설명한 대로 지역에서 복제의 연결을 해제합니다.

### <a name="what-region-should-i-use-for-my-secondary-linked-cache"></a>보조 연결된 캐시에는 어떤 지역을 사용해야 하나요?

일반적으로 캐시는 해당 캐시에 액세스하는 애플리케이션과 동일한 Azure 지역에 있는 것이 좋습니다. 애플리케이션에 별도의 기본 및 대체 지역이 있는 경우에도 기본 및 보조 캐시가 동일한 지역에 존재하는 것이 좋습니다. 쌍을 이루는 지역에 대한 자세한 내용은 [모범 사례 - Azure 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)을 참조하세요.

### <a name="how-does-failing-over-to-the-secondary-linked-cache-work"></a>보조 연결된 캐시로 장애 조치(failover)는 어떻게 작동하나요?

Azure 지역 간 자동 장애 조치(failover)는 지역에서 복제된 캐시에 대해 지원되지 않습니다. 재해 복구 시나리오에서 고객은 전체 애플리케이션 스택을 백업 지역에서 조정된 방식으로 가져와야 합니다. 개별 애플리케이션 구성 요소가 백업으로 전환할 시기를 자체적으로 결정하도록 허용하면 성능에 부정적인 영향을 줄 수 있습니다. 

Redis의 주요 이점 중 하나는 대기 시간이 낮은 저장소라는 점입니다. 고객의 주요 애플리케이션이 캐시와 다른 지역에 있는 경우 추가된 왕복 시간이 성능에 상당한 영향을 줄 수 있습니다. 이와 같은 이유로 일시적인 가용성 문제로 인한 자동 장애 조치(failover)는 하지 않습니다.

고객이 시작한 장애 조치(failover)를 시작하려면 먼저 캐시의 연결을 해제합니다. 그런 다음 이전에 연결된 보조 캐시의 연결 엔드포인트를 사용하도록 Redis 클라이언트를 변경합니다. 두 캐시의 연결이 해제되면 보조 캐시는 다시 일반적인 읽기-쓰기 캐시가 되며 Redis 클라이언트에서 직접 요청을 받습니다.

### <a name="can-i-configure-a-firewall-with-geo-replication"></a>지역에서 복제를 사용하여 방화벽을 구성할 수 있나요?

예, 지역에서 복제를 사용하여 [방화벽](./cache-configure.md#firewall)을 구성할 수 있습니다. 지역에서 복제가 방화벽과 함께 작동하려면 보조 캐시의 IP 주소가 기본 캐시의 방화벽 규칙에 추가되어야 합니다.

## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 기능에 대해 자세히 알아보세요.

- [Azure Cache for Redis - 서비스 계층](cache-overview.md#service-tiers)
- [Azure Cache for Redis의 고가용성](cache-high-availability.md)
