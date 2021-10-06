---
title: '빠른 시작: Redis Enterprise 캐시 만들기'
description: 이 빠른 시작에서는 엔터프라이즈 계층에서 Azure Cache for Redis의 인스턴스를 만드는 방법을 알아봅니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: mvc
ms.topic: quickstart
ms.date: 02/08/2021
ms.openlocfilehash: 7733f9df7f314dfdc0369075b96477e301825ce6
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537322"
---
# <a name="quickstart-create-a-redis-enterprise-cache"></a>빠른 시작: Redis Enterprise 캐시 만들기

Azure Cache for Redis Enterprise 계층은 Azure에서 완전히 통합되고 관리되는 [Redis Enterprise](https://redislabs.com/redis-enterprise/)를 제공합니다. 이러한 새로운 계층은 다음과 같습니다.

* Enterprise는 가상 머신에서 휘발성 메모리(DRAM)를 사용하여 데이터를 저장합니다.
* Enterprise Flash는 휘발성 및 비휘발성 메모리(NVMe 또는 SSD)를 사용하여 데이터를 저장합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 Azure 구독이 필요합니다. 계정이 없는 경우 [계정](https://azure.microsoft.com/)을 만들 수 있습니다. 자세한 내용은 [엔터프라이즈 계층에 대한 특별 고려 사항](cache-overview.md#special-considerations-for-enterprise-tiers)을 참조하세요.

## <a name="create-a-cache"></a>캐시 만들기

1. 캐시를 만들려면 Azure Portal에 로그인하여 **리소스 만들기** 를 선택합니다.

1. **새로 만들기** 페이지에서 **데이터베이스** 를 선택한 다음, **Azure Cache for Redis** 를 선택합니다.

   :::image type="content" source="media/cache-create/new-cache-menu.png" alt-text="Azure Cache for Redis 선택":::

1. **새 Redis Cache** 페이지에서 새 캐시의 설정을 구성합니다.

   | 설정      |  값 선택  | Description |
   | ------------ |  ------- | -------------------------------------------------- |
   | **구독** | 드롭다운하여 구독을 선택합니다. | 이 구독 아래에 새 Azure Cache for Redis 인스턴스가 만들어집니다. |
   | **리소스 그룹** | 드롭다운하여 리소스 그룹을 선택하거나, **새로 만들기** 를 선택하고 새 리소스 그룹 이름을 입력합니다. | 캐시 및 기타 리소스를 만들 새 리소스 그룹의 이름입니다. 모든 앱 리소스를 하나의 리소스 그룹에 배치하면 앱 리소스를 쉽게 관리하거나 삭제할 수 있습니다. |
   | **DNS 이름** | 지역에서 고유한 이름을 입력합니다. | 캐시 이름은 1~63자의 문자열이어야 하며 숫자, 문자, 하이픈만 포함할 수 있습니다. 이름은 숫자 또는 문자로 시작하고 끝나야 하며 연속 하이픈을 포함할 수 없습니다. 캐시 인스턴스의 *호스트 이름* 은 *\<DNS name\>.\<Azure region\>.redisenterprise.cache.azure.net* 입니다. |
   | **위치** | 드롭다운하여 위치를 선택합니다. | 엔터프라이즈 계층은 선택한 Azure 지역에서 사용할 수 있습니다. |
   | **캐시 유형** | *Enterprise* 또는 *Enterprise Flash* 계층 및 크기를 드롭다운하여 선택합니다. |  계층에 따라 캐시에 사용 가능한 크기, 성능 및 기능이 결정됩니다. |

   :::image type="content" source="media/cache-create/enterprise-tier-basics.png" alt-text="엔터프라이즈 계층 기본 사항 탭":::

   > [!IMPORTANT]
   > 계속하기 전에 **사용 약관** 을 선택해야 합니다.
   >

1. 완료되면 **다음: 네트워킹** 을 선택하고 건너뜁니다.

1. **다음: 고급** 을 선택하고 비클러스터형 캐시에 대해 **클러스터링 정책** 을 **엔터프라이즈** 로 설정합니다. TLS를 사용하지 않고 새 캐시에 연결하려는 경우에만 **비 TLS 액세스 전용** 을 사용하도록 설정합니다. 그러나 TLS를 사용하지 않도록 설정하는 것은 권장되지 **않습니다**.

   :::image type="content" source="media/cache-create/enterprise-tier-advanced.png" alt-text="엔터프라이즈 계층 고급 탭을 보여 주는 스크린샷":::

   > [!NOTE]
   > Redis Enterprise는 두 개의 클러스터링 정책을 지원합니다. **엔터프라이즈** 정책을 사용하여 일반 Redis API를 사용하여 캐시에 액세스하고 OSS 클러스터 API를 **OSS** 합니다.
   >

   > [!NOTE]
   > 캐시 인스턴스를 만든 후에는 모듈을 변경할 수 없습니다. 설정은 만들기 전용입니다.
   >

1. 완료되면 **다음: 태그** 를 선택하고 건너뜁니다.

1. 완료되면 **다음: 리뷰 + 만들기** 를 클릭합니다.

   :::image type="content" source="media/cache-create/enterprise-tier-summary.png" alt-text="엔터프라이즈 계층 검토 + 만들기 탭":::

1. 설정을 검토하고 **만들기** 를 선택합니다.

   캐시를 만드는 데는 약간의 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cache for Redis의 Enterprise 계층 인스턴스를 만드는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)
