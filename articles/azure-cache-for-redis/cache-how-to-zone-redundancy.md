---
title: Azure Cache for Redis에 대한 영역 중복 사용
description: Premium 및 Enterprise 계층 Azure Cache for Redis 인스턴스에 영역 중복을 설정하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 522cf0ad115e1149d7746ecadef96ae9494a1d8c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529095"
---
# <a name="enable-zone-redundancy-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 영역 중복 사용
이 문서에서는 Azure Portal을 사용하여 영역 중복 Azure Cache 인스턴스를 구성하는 방법을 알아봅니다.

Azure Cache for Redis Standard, Premium, Enterprise 계층은 두 개의 전용 가상 머신(VM)에서 각 캐시를 호스트하여 중복을 기본 제공합니다. 이와 같은 VM은 별도의 [Azure 장애 및 업데이트 도메인](../virtual-machines/availability.md)에 있고 가용성이 높지만 데이터 센터 수준의 오류에 취약합니다. 또한 Azure Cache for Redis는 Premium 및 Enterprise 계층에서 영역 중복을 지원합니다. 영역 중복 캐시는 여러 [가용성 영역](../availability-zones/az-overview.md)에 걸쳐 분산된 VM에서 실행됩니다. 이는 더 높은 복원력 및 가용성을 제공합니다.

> [!NOTE]
> Azure 가용성 영역 간 데이터 전송은 표준 [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구됩니다.

## <a name="prerequisites"></a>사전 요구 사항
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

## <a name="create-a-cache"></a>캐시 만들기
캐시를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고, **리소스 만들기** 를 선택합니다.
  
1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

    :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Azure Cache for Redis를 선택합니다.":::
   
1. **기본 사항** 페이지에서 새 캐시의 설정을 구성합니다.
   
    | 설정      | 제안 값  | 설명 |
    | ------------ |  ------- | -------------------------------------------------- |
    | **구독** | 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. | 
    | **리소스 그룹** | 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. | 
    | **DNS 이름** | 전역적으로 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자 및 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name>.redis.cache.windows.net* 입니다. | 
    | **위치** | 위치를 선택합니다. | 캐시를 사용할 다른 서비스와 가까이 있는 [Azure 지역](https://azure.microsoft.com/regions/)을 선택합니다. |
    | **캐시 유형** | [Premium 또는 Enterprise 계층](https://azure.microsoft.com/pricing/details/cache/) 캐시를 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
   
1. **고급** 페이지에서 Premium 계층 캐시에 대해 **복제본 수** 를 선택합니다.
   
    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="복제본 수":::

1. **가용성 영역** 을 선택합니다. 
   
    :::image type="content" source="media/cache-how-to-zone-redundancy/create-zones.png" alt-text="가용성 영역":::

1. 클러스터링 및/또는 RDB 지속성에 대한 설정을 구성합니다.  

    > [!NOTE]
    > 영역 중복은 AOF 지속성을 지원하지 않거나 현재 지역에서 복제를 사용하지 않습니다.
    >

1. **만들기** 를 선택합니다. 
   
    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.
   
    > [!NOTE]
    > 캐시를 만든 후에는 가용성 영역을 변경하거나 사용하도록 설정할 수 없습니다. 
    >

## <a name="zone-redundancy-faq"></a>영역 중복 FAQ

- [프리미엄 캐시를 만들 때 영역 중복을 사용하도록 설정할 수 없는 이유는 무엇인가요?](#why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache)
- [캐시를 만드는 동안 세 영역을 모두 선택할 수 없는 이유는 무엇인가요?](#why-cant-i-select-all-three-zones-during-cache-create)
- [영역 중복을 사용하도록 기존 프리미엄 캐시를 업데이트할 수 있나요?](#can-i-update-my-existing-premium-cache-to-use-zone-redundancy)
- [Azure 가용성 영역 간에 데이터를 복제하는 비용은 어느 정도인가요?](#how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones)

### <a name="why-cant-i-enable-zone-redundancy-when-creating-a-premium-cache"></a>프리미엄 캐시를 만들 때 영역 중복을 사용하도록 설정할 수 없는 이유는 무엇인가요?

영역 중복은 가용성 영역이 있는 Azure 지역에서만 사용할 수 있습니다. 최신 목록은 [가용성 영역이 있는 Azure 지역](../availability-zones/az-region.md#azure-services-supporting-availability-zones)을 참조하세요.

### <a name="why-cant-i-select-all-three-zones-during-cache-create"></a>캐시를 만드는 동안 세 영역을 모두 선택할 수 없는 이유는 무엇인가요?

프리미엄 캐시에는 기본적으로 주 노드와 복제본 노드가 하나씩 있습니다. 두 개가 넘는 가용성 영역에 대해 영역 중복을 구성하려면 만들고 있는 캐시에 [더 많은 복제본](cache-how-to-multi-replicas.md)을 추가해야 합니다.

### <a name="can-i-update-my-existing-premium-cache-to-use-zone-redundancy"></a>영역 중복을 사용하도록 기존 프리미엄 캐시를 업데이트할 수 있나요?

아니요. 이 기능은 현재 지원되지 않습니다.

### <a name="how-much-does-it-cost-to-replicate-my-data-across-azure-availability-zones"></a>Azure 가용성 영역 간에 데이터를 복제하는 비용은 어느 정도인가요?

여러 가용성 영역으로 구성된 영역 중복을 사용하는 경우 한 영역의 주 캐시 노드에서 다른 영역의 다른 노드로 데이터가 복제됩니다. 데이터 전송 요금은 선택한 가용성 영역 간에 이동하는 데이터의 네트워크 송신 비용입니다. 자세한 내용은 [대역폭 가격 정보](https://azure.microsoft.com/pricing/details/bandwidth/)를 참조하세요.

## <a name="next-steps"></a>다음 단계
Azure Cache for Redis 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Cache for Redis 프리미엄 서비스 계층](cache-overview.md#service-tiers)
