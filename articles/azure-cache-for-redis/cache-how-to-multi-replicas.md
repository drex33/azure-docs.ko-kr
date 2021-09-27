---
title: Azure Cache for Redis에 복제본 추가
description: 프리미엄 계층 Azure Cache for Redis 인스턴스에 복제본을 추가하는 방법을 알아봅니다.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/11/2020
ms.openlocfilehash: 373928398595beb7e6564e3d290fa1ec9f702691
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828935"
---
# <a name="add-replicas-to-azure-cache-for-redis"></a>Azure Cache for Redis에 복제본 추가

이 문서에서는 Azure Portal를 사용 하 여 추가 복제본이 있는 Azure Cache for Redis 인스턴스를 설정 하는 방법에 대해 알아봅니다.

Azure Cache for Redis 표준 및 프리미엄 계층은 두 개의 전용 VM(가상 머신)에서 각 캐시를 호스트하여 중복을 제공합니다. 두 VM은 주 VM과 복제본으로 구성됩니다. 주 VM을 사용할 수 없게 되면 복제본이 감지하고 자동으로 새로운 주 VM 역할을 수행합니다. 이제 프리미엄 캐시의 복제본 수를 최대 3개까지 늘릴 수 있으므로 총 4개의 VM이 캐시를 지원합니다. 복제본이 여러 개 있으면 단일 복제본이 제공할 수 있는 것보다 복원력이 향상됩니다.

## <a name="prerequisites"></a>필수 구성 요소

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
    | **캐시 유형** | [프리미엄 계층](https://azure.microsoft.com/pricing/details/cache/) 캐시를 선택합니다. |  가격 책정 계층은 캐시에 사용 가능한 크기, 성능 및 기능을 결정합니다. 자세한 내용은 [Azure Cache for Redis 개요](cache-overview.md)를 참조하세요. |

1. **고급** 페이지에서 **복제본 수** 를 선택합니다.

    :::image type="content" source="media/cache-how-to-multi-replicas/create-multi-replicas.png" alt-text="복제본 수":::

    > [!NOTE]
    > 현재는 여러 복제본(둘 이상의 복제본)에서 AOF(추가 전용 파일) 지속성 또는 지역에서 복제를 사용할 수 없습니다.
    >

1. 다른 옵션은 기본 설정으로 그대로 둡니다.

1. **만들기** 를 선택합니다.

    캐시를 만드는 데 잠시 시간이 걸립니다. Azure Cache for Redis **개요** 페이지에서 진행률을 모니터링할 수 있습니다. **상태** 가 **실행 중** 으로 표시되면 캐시를 사용할 준비가 된 것입니다.

    > [!NOTE]
    > 캐시를 만든 후에는 캐시의 복제본 수를 변경할 수 없습니다.
    >

## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Azure Cache for Redis 프리미엄 서비스 계층](cache-overview.md#service-tiers)
