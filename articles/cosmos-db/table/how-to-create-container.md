---
title: Azure Cosmos DB Table API에서 컨테이너 만들기
description: Azure Portal, .NET, Java, Python, Node.js 및 기타 SDK를 사용하여 Azure Cosmos DB SQL Table API에서 컨테이너를 만드는 방법에 대해 알아봅니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/16/2020
ms.author: mjbrown
ms.custom: devx-track-csharp
ms.openlocfilehash: 9320e34ccd4cdb217ac574eeef0d5c0b0f1eeb42
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124812082"
---
# <a name="create-a-container-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB Table API에서 컨테이너 만들기
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

이 문서에서는 Azure Cosmos DB Table API에서 컨테이너를 만드는 다양한 방법을 설명합니다. Azure Portal, Azure CLI, PowerShell 또는 지원되는 SDK를 사용하여 컨테이너를 만드는 방법을 보여줍니다. 이 문서에서는 컨테이너를 만들고 파티션 키를 지정하고 처리량을 프로비저닝하는 방법을 보여줍니다.

이 문서에서는 Azure Cosmos DB Table API에서 컨테이너를 만드는 다양한 방법을 설명합니다. 다른 API를 사용하는 경우 [API for MongoDB](../mongodb/how-to-create-container-mongodb.md), [Cassandra API](../cassandra/how-to-create-container-cassandra.md), [Gremlin API](../how-to-create-container-gremlin.md) 및 [SQL API](../how-to-create-container.md) 문서를 참조하여 컨테이너를 만듭니다.

> [!NOTE]
> 컨테이너를 만들 때 이름은 같지만 대/소문자가 다른 두 개의 컨테이너를 만들지 않도록 해야 합니다. Azure 플랫폼의 일부 부분에서는 대/소문자를 구분하지 않으므로 이로 인해 이러한 이름을 가진 컨테이너에 대한 원격 분석 및 작업의 혼동/충돌이 발생할 수 있습니다.

## <a name="create-using-azure-portal"></a><a id="portal-table"></a>Azure Portal을 사용하여 만들기

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. [새 Azure Cosmos 계정을 만들거나](create-table-dotnet.md#1---create-an-azure-cosmos-db-account) 기존 계정을 선택합니다.

1. **데이터 탐색기** 창을 열고 **새 테이블** 을 선택합니다. 다음으로, 다음과 같은 세부 정보를 제공합니다.

   * 테이블 ID를 입력합니다.
   * 프로비저닝할 처리량을 입력합니다(예: 1000RU).
   * **확인** 을 선택합니다.

    :::image type="content" source="../media/how-to-create-container/partitioned-collection-create-table.png" alt-text="Table API, 테이블 추가 대화 상자의 스크린샷":::

> [!Note]
> Table API의 경우 새 행을 추가할 때마다 파티션 키가 지정됩니다.

## <a name="create-using-azure-cli"></a><a id="cli-mongodb"></a>Azure CLI를 사용하여 만들기

[Azure CLI를 사용하여 Table API 테이블을 만듭니다](../scripts/cli/table/create.md). 모든 Azure Cosmos DB API에 대한 모든 Azure CLI 샘플 목록은 [Azure Cosmos DB에 대한 Azure CLI 샘플](cli-samples.md)을 참조하세요.

## <a name="create-using-powershell"></a>PowerShell을 사용하여 만들기

[PowerShell을 사용하여 Table API 테이블을 만듭니다](../scripts/powershell/table/create.md). 모든 Azure Cosmos DB API에 대한 모든 PowerShell 샘플 목록은 [PowerShell Samples](powershell-samples.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB에서 분할](../partitioning-overview.md)
* [Azure Cosmos DB의 요청 단위](../request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](../set-throughput.md)
* [Azure Cosmos 계정 작업](../account-databases-containers-items.md)