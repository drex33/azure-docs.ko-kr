---
title: Azure Cache for Redis Redis 버전 설정
description: Redis 버전을 구성하는 방법 알아보기
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 10/07/2021
ms.openlocfilehash: 41d61ccce3602b1d3e823eb6a4c46afe39159c46
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129728223"
---
# <a name="set-redis-version-for-azure-cache-for-redis"></a>Azure Cache for Redis Redis 버전 설정
이 문서에서는 캐시 인스턴스와 함께 사용할 Redis 소프트웨어 버전을 구성하는 방법을 알아봅니다. Azure Cache for Redis는 Redis의 최신 주 버전과 하나 이상의 이전 버전을 제공합니다. 최신 Redis 소프트웨어가 출시되면 이러한 버전이 정기적으로 업데이트됩니다. 사용 가능한 두 버전 중에서 선택할 수 있습니다. 현재 사용 중인 버전이 더 이상 지원되지 않는 경우 캐시가 다음 버전으로 자동 업그레이드된다는 점을 기억하십시오.

> [!NOTE]
> 현재 Redis 6은 ACL 및 Redis 4와 6 캐시 간의 지역 복제를 지원하지 않습니다.
>

## <a name="prerequisites"></a>사전 요구 사항
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)

## <a name="create-a-cache-using-the-azure-portal"></a>Azure Portal 사용하여 캐시 만들기
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
    | **캐시 유형** | [캐시의 계층 및 크기](https://azure.microsoft.com/pricing/details/cache/)를 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |
   
1. **고급** 페이지에서 사용할 Redis 버전을 선택합니다.
   
    :::image type="content" source="media/cache-how-to-version/select-redis-version.png" alt-text="Redis 버전":::

1. **만들기** 를 선택합니다. 
   
    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.


## <a name="create-a-cache-using-azure-powershell"></a>Azure PowerShell 사용하여 캐시 만들기

```azurepowershell
    New-AzRedisCache -ResourceGroupName "ResourceGroupName" -Name "CacheName" -Location "West US 2" -Size 250MB -Sku "Standard" -RedisVersion "6"
```
Azure PowerShell 사용하여 Azure Cache for Redis 관리하는 방법에 대한 자세한 내용은 [여기를 참조하세요.](cache-how-to-manage-redis-cache-powershell.md)

## <a name="create-a-cache-using-azure-cli"></a>Azure CLI 사용하여 캐시 만들기

```azurecli-interactive
az redis create --resource-group resourceGroupName --name cacheName --location westus2 --sku Standard --vm-size c0 --redisVersion="6"
```
Azure CLI 사용하여 Azure Cache for Redis 관리하는 방법에 대한 자세한 내용은 [여기를 참조하세요.](cli-samples.md)

## <a name="upgrade-an-existing-redis-4-cache-to-redis-6"></a>기존 Redis 4 캐시를 Redis 6으로 업그레이드
Azure Cache for Redis Redis Cache 서버 주 버전을 Redis 4에서 Redis 6으로 업그레이드할 수 있습니다. 업그레이드는 영구적이며 잠시 연결이 끊어질 수 있습니다. 예방 조치로, 업그레이드하기 전에 기존 Redis 4 캐시에서 데이터를 내보내고 낮은 환경에서 Redis 6 캐시를 사용하여 클라이언트 애플리케이션을 테스트하는 것이 좋습니다. 내보내는 방법에 대한 자세한 내용은 [여기를](cache-how-to-import-export-data.md) 참조하세요.

> [!NOTE]
> 지역 복제 링크가 있는 캐시에서는 업그레이드가 지원되지 않으므로 업그레이드하기 전에 캐시 인스턴스의 연결을 수동으로 해제해야 합니다. 
>

캐시를 업그레이드하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **Azure Cache for Redis** 를 검색합니다. 그런 다음 <Enter> 키를 누르거나 검색 제안에서 선택합니다.

    :::image type="content" source="media/cache-private-link/4-search-for-cache.png" alt-text="Azure Cache for Redis를 검색합니다.":::

1. Redis 4에서 Redis 6으로 업그레이드하려는 캐시 인스턴스를 선택합니다.

1. 화면 왼쪽에서 **고급 설정** 을 선택합니다. 

1. 캐시 인스턴스를 업그레이드할 수 있는 경우 다음과 같은 파란색 배너가 표시됩니다. 계속하려면 배너의 텍스트를 선택합니다.

    :::image type="content" source="media/cache-how-to-version/blue-banner-upgrade-cache.png" alt-text="개발자 생산성과 사용 편의성을 향상시키는 추가 기능 및 명령으로 Redis 6 캐시를 업그레이드할 수 있다고 표시된 파란색 배너입니다. 캐시 인스턴스 업그레이드는 되돌릴 수 없습니다.":::
    
1. 그런 다음 업그레이드가 영구적이며 짧은 연결 블립이 발생할 수 있음을 알리는 팝업이 표시됩니다. 캐시 인스턴스를 업그레이드하려면 예를 선택합니다.

    :::image type="content" source="media/cache-how-to-version/dialog-version-upgrade.png" alt-text="캐시 업그레이드에 대한 자세한 정보가 있는 대화 상자":::

1. 업그레이드 상태를 확인하려면 **개요로** 이동합니다.

    :::image type="content" source="media/cache-how-to-version/upgrade-status.png" alt-text="개요는 업그레이드 중인 캐시의 상태를 보여줍니다.":::

## <a name="faq"></a>FAQ

### <a name="what-features-arent-supported-with-redis-6"></a>Redis 6에서 지원되지 않는 기능은 무엇인가요?

현재 Redis 6은 ACL 및 Redis 4와 6 캐시 간의 지역 복제를 지원하지 않습니다.

### <a name="can-i-change-the-version-of-my-cache-after-its-created"></a>캐시를 만든 후 버전을 변경할 수 있나요?

기존 Redis 4 캐시를 Redis 6으로 업그레이드할 수 있습니다. 자세한 내용은 [여기를](#upgrade-an-existing-redis-4-cache-to-redis-6) 참조하세요. 캐시 인스턴스 업그레이드는 영구적이며 Redis 6 캐시를 Redis 4 캐시로 다운그레이드할 수 없습니다.

## <a name="next-steps"></a>다음 단계

- Redis 6 기능에 대한 자세한 내용은 [Redis 6.0으로 나누기를 참조하세요.](https://redis.com/blog/diving-into-redis-6/)
- Azure Cache for Redis 기능에 대해 자세히 알아보려면 다음을 수행합니다.

> [!div class="nextstepaction"]
> [Azure Cache for Redis 프리미엄 서비스 계층](cache-overview.md#service-tiers)
