---
title: Azure Cosmos DB 통합 캐시를 구성하는 방법
description: Azure Cosmos DB 통합 캐시를 구성하는 방법 알아보기
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: dea28832bb607da722653c398cc234d54d2ffcc9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128618631"
---
# <a name="how-to-configure-the-azure-cosmos-db-integrated-cache-preview"></a>Azure Cosmos DB 통합 캐시를 구성하는 방법(미리 보기)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 전용 게이트웨이를 프로비저닝하고, 통합 캐시를 구성하고, 애플리케이션을 연결하는 방법을 설명합니다. 

## <a name="prerequisites"></a>사전 요구 사항

- [Azure 구독](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)을 만듭니다.
- Azure Cosmos DB를 사용하는 기존 애플리케이션입니다. 계정이 없는 경우 [여기에 몇 가지 예가 있습니다](https://github.com/AzureCosmosDB/labs).
- 기존 [Azure Cosmos DB SQL(핵심) API 계정](create-cosmosdb-resources-portal.md).

## <a name="provision-a-dedicated-gateway-cluster"></a>전용 게이트웨이 클러스터 프로비저닝

1. Azure Portal에서 Azure Cosmos DB 계정으로 이동하고 **전용 게이트웨이** 탭을 선택합니다.

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" alt-text="전용 게이트웨이 탭으로 이동하는 방법을 보여 주는 이미지" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-tab.png" border="false":::

2. 다음 세부 정보로 **전용 게이트웨이** 양식을 작성합니다.

   * **전용 게이트웨이** - **프로비저닝됨** 으로 토글을 켭니다. 
   * **SKU** - 필요한 컴퓨팅 및 메모리 크기가 있는 SKU를 선택합니다. 
   *  **인스턴스 수** - 노드 수입니다. 개발 목적을 위해 D4 크기의 노드 하나에서 시작하는 것이 좋습니다. 캐시해야 하는 데이터의 양에 따라 초기 테스트 후 노드 크기를 늘릴 수 있습니다.

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" alt-text="전용 게이트웨이 클러스터를 만들기 위한 샘플 입력 설정을 보여 주는 이미지" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-input.png" border="false":::

3. **저장** 을 선택하고 전용 게이트웨이 프로비저닝이 완료될 때까지 약 5~10분 정도 기다립니다. 프로비저닝이 완료되면 다음 알림이 표시됩니다.

   :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" alt-text="전용 게이트웨이 프로비저닝이 완료되었는지 확인하는 방법을 보여 주는 이미지" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-notification.png" border="false":::

## <a name="configuring-the-integrated-cache"></a>통합 캐시 구성

1. 전용 게이트웨이를 만들면 통합 캐시가 자동으로 프로비저닝됩니다. 통합 캐시는 전용 게이트웨이 메모리의 약 70%를 사용합니다. 전용 게이트웨이의 나머지 30%는 백 엔드 파티션으로 요청을 라우팅하는 데 사용됩니다.

2.  새 전용 게이트웨이 엔드포인트를 사용하도록 애플리케이션의 연결 문자열을 수정합니다.

      업데이트된 전용 게이트웨이 연결 문자열은 **키** 블레이드에 있습니다.
   
      :::image type="content" source="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" alt-text="전용 게이트웨이 연결 문자열을 보여 주는 이미지" lightbox="./media/how-to-configure-integrated-cache/dedicated-gateway-connection-string.png" border="false":::

      모든 전용 게이트웨이 연결 문자열은 동일한 패턴을 따릅니다. 원래 연결 문자열에서 `documents.azure.com`을 제거하고 `sqlx.cosmos.azure.com`으로 바꿉니다. 전용 게이트웨이는 제거했다가 다시 프로비저닝하더라도 항상 동일한 연결 문자열을 갖습니다.

      동일한 Azure Cosmos DB 계정을 사용하는 모든 애플리케이션에서 연결 문자열을 수정할 필요가 없습니다. 예를 들어, 하나의 `CosmosClient`는 게이트웨이 모드와 전용 게이트웨이 엔드포인트를 사용하여 연결하고 다른 `CosmosClient`는 직접 모드를 사용할 수 있습니다. 즉, 전용 게이트웨이를 추가해도 기존 Azure Cosmos DB 연결 방법에 영향을 주지 않습니다.

3. .NET 또는 Java SDK를 사용하는 경우 연결 모드를 [게이트웨이 모드](sql-sdk-connection-modes.md#available-connectivity-modes)로 설정합니다. Python 및 Node.js SDK에는 게이트웨이 모드 외에 추가 연결 옵션이 없기 때문에 이 단계가 필요하지 않습니다.

> [!NOTE]
> 최신 .NET 또는 Java SDK 버전을 사용하는 경우 기본 연결 모드는 직접 모드입니다. 통합 캐시를 사용하려면 이 기본값을 재정의해야 합니다.

Java SDK를 사용하는 경우 `CosmosClientBuilder` 내에서 수동으로 [contentResponseOnWriteEnabled](/java/api/com.azure.cosmos.cosmosclientbuilder.contentresponseonwriteenabled?view=azure-java-stable&preserve-view=true)도 `true`로 설정해야 합니다. 다른 SDK를 사용하는 경우 이 값은 기본값인 `true`로 이미 설정되어 있으므로 변경할 필요가 없습니다.

## <a name="adjust-request-consistency"></a>요청 일관성 조정

최종으로 요청 일관성을 조정해야 합니다. 그러지 않으면 요청은 항상 통합 캐시를 바이패스합니다. 모든 읽기 작업에 대해 최종 일관성을 구성하는 가장 쉬운 방법은 [계정 수준에서 설정](consistency-levels.md#configure-the-default-consistency-level)하는 것입니다. 또한 [요청 수준](how-to-manage-consistency.md#override-the-default-consistency-level)에서 일관성을 구성할 수도 있습니다. 이는 읽기의 하위 집합만 통합 캐시를 활용하려는 경우에 권장됩니다.

> [!NOTE]
> Python SDK를 사용하는 경우 각 요청에 대해 일관성 수준을 명시적으로 **설정해야 합니다**. 기본 계정 수준 설정은 자동으로 적용되지 않습니다.

## <a name="adjust-maxintegratedcachestaleness"></a>MaxIntegratedCacheStaleness 조정

부실 캐시 데이터를 허용할 최대 시간인 `MaxIntegratedCacheStaleness`를 구성합니다. 반복되는 지점 읽기 및 쿼리가 캐시 적중이 될 가능성이 높아지므로 `MaxIntegratedCacheStaleness`를 최대한 높게 설정하는 것이 좋습니다. `MaxIntegratedCacheStaleness`를 0으로 설정하면 일관성 수준에 관계없이 읽기 요청이 통합 캐시를 **전혀** 사용하지 않습니다. 구성되지 않은 경우 `MaxIntegratedCacheStaleness` 기본값은 5분입니다.

**.NET**

```csharp
FeedIterator<Food> myQuery = container.GetItemQueryIterator<Food>(new QueryDefinition("SELECT * FROM c"), requestOptions: new QueryRequestOptions
        {
            ConsistencyLevel = ConsistencyLevel.Eventual,
            DedicatedGatewayRequestOptions = new DedicatedGatewayRequestOptions 
            { 
                MaxIntegratedCacheStaleness = TimeSpan.FromMinutes(30) 
            }
        }
);
```

> [!NOTE]
> 현재는 최신 [.NET](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.17.0-preview) 및 [Java](https://mvnrepository.com/artifact/com.azure/azure-cosmos/4.16.0-beta.1) 미리 보기 SDK를 사용하여 MaxIntegratedCacheStaleness만 조정할 수 있습니다.

## <a name="verify-cache-hits"></a>캐시 적중 확인

마지막으로, 애플리케이션을 다시 시작하고 반복된 지점 읽기 또는 쿼리에 대한 통합 캐시 적중 횟수를 확인할 수 있습니다. 전용 게이트웨이 엔드포인트를 사용하도록 `CosmosClient`를 수정하면 모든 요청이 전용 게이트웨이를 통해 라우팅됩니다.

읽기 요청(포인트 읽기 또는 쿼리)에서 통합 캐시를 활용하려면 다음 기준이 **모두** 충족되어야 합니다.

-   클라이언트가 전용 게이트웨이 엔드포인트에 연결됩니다.
-  클라이언트가 게이트웨이 모드를 사용합니다(Python 및 Node.js SDK는 항상 게이트웨이 모드를 사용함).
-   요청에 대 한 일관성은 session 또는 최종적인로 설정 되어야 합니다.

> [!NOTE]
> 통합 캐시에 관한 피드백이 있나요? 많은 의견 부탁드립니다. 언제든지 자유롭게 Azure Cosmos DB 엔지니어링 팀(cosmoscachefeedback@microsoft.com)과 피드백을 직접 공유해 주세요.


## <a name="next-steps"></a>다음 단계

- [통합 캐시 FAQ](integrated-cache-faq.md)
- [통합 캐시 개요](integrated-cache.md)
- [전용 게이트웨이](dedicated-gateway.md)