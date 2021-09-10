---
title: Enterprise Azure Cache for Redis 인스턴스에 대해 활성 지역 복제 구성
description: Azure 지역 간에 Azure Cache for Redis Enterprise 인스턴스를 복제하는 방법을 알아봅니다.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.author: yegu
ms.openlocfilehash: 5f7bd55cfbdd0e38e2c53784dd24760fb051c0f1
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112233586"
---
# <a name="configure-active-geo-replication-for-enterprise-azure-cache-for-redis-instances-preview"></a>Enterprise Azure Cache for Redis 인스턴스에 대해 활성 지역 복제 구성(미리 보기)

이 문서에서는 Azure Portal을 사용하여 활성 지역 복제 Azure Cache를 구성하는 방법에 대해 설명합니다.

활성 지역 복제는 두 개의 Enterprise Azure Cache for Redis 인스턴스를 Azure 지역 전체를 포괄하는 단일 캐시로 그룹화합니다. 두 인스턴스는 모두 로컬 기본 인스턴스 역할을 합니다. 애플리케이션은 읽기 및 쓰기 요청에 사용할 인스턴스를 결정합니다.

> [!NOTE]
> Azure 지역 간 데이터 전송은 표준 [대역폭 요금](https://azure.microsoft.com/pricing/details/bandwidth/)으로 청구됩니다.

## <a name="create-or-join-an-active-geo-replication-group"></a>활성 지역 복제 그룹 만들기 또는 조인하기

> [!IMPORTANT]
> Azure Cache for Redis를 만들 때 활성 지역 복제를 사용해야 합니다.
>
>

1. **새 Redis Cache** 만들기 UI의 **고급** 탭에서 **클러스터링 정책** 에 **Enterprise** 를 선택합니다.

    ![활성 지역 복제 구성](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-not-configured.png)

1. **구성** 을 선택하여 **활성 지역 복제** 를 설정합니다.

1. 첫 번째 캐시 인스턴스에 대해 새 복제 그룹을 만들거나 목록에서 기존 복제 그룹을 선택합니다.

    ![캐시 연결](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-new-group.png)

1. **구성** 을 선택하여 완료합니다.

    ![활성 지역 복제가 구성됨](./media/cache-how-to-active-geo-replication/cache-active-geo-replication-configured.png)

1. 첫 번째 캐시가 성공적으로 만들어질 때까지 기다립니다. 지역 복제 그룹의 추가 캐시 인스턴스별로 위 단계를 반복합니다.

## <a name="remove-from-an-active-geo-replication-group"></a>활성 지역 복제 그룹에서 제거

활성 지역 복제 그룹에서 캐시 인스턴스를 제거하려는 경우 해당 인스턴스를 삭제하기만 하면 됩니다. 나머지 인스턴스는 자동으로 다시 구성됩니다.

## <a name="next-steps"></a>다음 단계

Azure Cache for Redis 기능에 대해 자세히 알아보세요.

* [Azure Cache for Redis - 서비스 계층](cache-overview.md#service-tiers)
* [Azure Cache for Redis의 고가용성](cache-high-availability.md)
