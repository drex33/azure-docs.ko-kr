---
title: Azure Cosmos DB 체험 계층
description: Azure Cosmos DB 무료 계층을 사용하여 시작하고 애플리케이션을 개발 및 테스트합니다. 무료 계층을 사용하는 경우 계정의 첫 1000RU/s 및 25GB의 스토리지를 무료로 얻을 수 있습니다.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 05/25/2021
ms.author: sngun
ms.openlocfilehash: 55b37ce3f96bc187e4420fc16924b02639c9a7df
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567327"
---
# <a name="azure-cosmos-db-free-tier"></a>Azure Cosmos DB 체험 계층 
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB 무료 계층을 사용하면 쉽게 시작하고, 애플리케이션을 개발 및 테스트하거나, 소규모 프로덕션 워크로드를 무료로 실행할 수도 있습니다. 계정에서 무료 계층을 사용하는 경우 계정의 첫 1000RU/s 및 25GB의 스토리지를 무료로 얻을 수 있습니다. 이러한 제한을 초과하여 사용한 처리량 및 스토리지에는 일반 가격으로 요금이 청구됩니다. 무료 계층은 프로비전된 처리량, 자동 크기 조정 처리량, 단일 또는 다중 쓰기 지역이 있는 모든 API 계정에 사용할 수 있습니다.

무료 계층은 계정의 수명 기간 동안 무기한 지속되며 일반 Azure Cosmos DB 계정의 모든 [이점과 기능](introduction.md#key-benefits)이 함께 제공됩니다. 이러한 이점에는 무제한 스토리지 및 처리량(RU/s), SLA, 고가용성, 모든 Azure 지역의 턴키 글로벌 배포 등이 포함됩니다.

Azure 구독당 최대 1개의 무료 계층 Azure Cosmos DB 계정을 사용할 수 있으며 계정을 만들 때 옵트인해야 합니다. 체험 계층 할인을 적용하는 옵션이 표시되지 않으면 구독의 다른 계정에서 이미 체험 계층을 사용하도록 설정되었음을 의미합니다. 무료 계층을 사용하여 계정을 만든 후 삭제하는 경우 새 계정에 대해 무료 계층을 적용할 수 있습니다. 새 계정을 만들 때 사용 가능한 경우 무료 계층 할인을 사용하도록 설정하는 것이 좋습니다.

> [!NOTE]
> 무료 계층은 현재 서버리스 계정에 사용할 수 없습니다.

## <a name="free-tier-with-shared-throughput-database"></a>공유 처리량 데이터베이스가 있는 무료 계층

공유 처리량 모델에서 데이터베이스의 처리량을 프로비전하는 경우 데이터베이스의 모든 컨테이너 간에 처리량이 공유됩니다. 무료 계층을 사용하는 경우 최대 1000RU/s의 공유 데이터베이스를 무료로 프로비전할 수 있습니다. 데이터베이스의 모든 컨테이너는 처리량을 공유합니다. 

무료 계층 계정에서도 일반 계정과 마찬가지로 공유 처리량 데이터베이스에 최대 25개의 컨테이너가 있을 수 있습니다. 공유 처리량을 포함하는 모든 추가 데이터베이스 또는 1000RU/s를 초과하는 전용 처리량을 포함하는 컨테이너에는 일반 가격으로 요금이 청구됩니다. 무료 계층 계정에서 최대 5개의 공유 처리량 데이터베이스를 만들 수 있습니다.

## <a name="free-tier-with-azure-discount"></a>Azure 할인이 적용되는 무료 계층

Azure Cosmos DB 무료 계층은 [Azure 체험 계정](optimize-dev-test.md#azure-free-account)과 호환됩니다. 옵트인하려면 Azure 체험 계정 구독에서 Azure Cosmos DB 무료 계층 계정을 만듭니다. 첫 12개월 동안 총 1400RU/s(Azure Cosmos DB 무료 계층에서 1000RU/s 및 Azure 체험 계정에서 400RU/s) 및 50GB 스토리지(Azure Cosmos DB 무료 계층에서 25GB 및 Azure 체험 계정에서 25GB)를 할인받을 수 있습니다. 12개월이 만료된 후에도 Azure Cosmos DB 계정의 수명 기간 동안 Azure Cosmos DB 무료 계층에서 1000RU/s 및 25GB를 계속 받을 수 있습니다. 요금이 누적되는 방법의 예는 [무료 계층 계정을 사용한 청구 예](understand-your-bill.md#azure-free-tier)를 참조하세요.

> [!NOTE]
> Azure Cosmos DB 무료 계층은 Azure 무료 계정과 다릅니다. Azure 무료 계정은 제한된 시간 동안 Azure 크레딧 및 리소스를 무료로 제공합니다. 이 무료 계정의 일부로 Azure Cosmos DB를 사용하는 경우 12개월 동안 프로비저닝된 처리량에 대한 25GB의 스토리지 및 400RU/s를 얻을 수 있습니다.

## <a name="best-practices-to-keep-your-account-free"></a>계정을 무료로 유지하는 모범 사례

Azure Cosmos DB 무료 계층을 사용하는 경우 계정을 완전히 무료로 유지하려면 계정에 무료 계층에서 제공하는 것 외에 추가로 소비하는 RU/s 또는 스토리지가 없어야 합니다.

예를 들어 월별 요금을 초래하지 않는 몇 가지 옵션은 다음과 같습니다.

* 최대 1000RU/s의 처리량이 프로비전된 데이터베이스 하나
* 최대 400RU/s의 처리량이 프로비전된 컨테이너 하나와 최대 600RU/s의 처리량이 프로비전된 컨테이너 하나를 포함한 두 개의 컨테이너
* 최대 500RU/s의 처리량이 프로비전된 컨테이너 하나가 있는 단일 지역을 포함한 2개 지역이 있는 계정

## <a name="create-an-account-with-free-tier"></a>무료 계층을 사용하여 계정 만들기

Azure Portal, PowerShell, CLI 또는 ARM(Azure Resource Manager) 템플릿으로 무료 계층 계정을 만들 수 있습니다. 계정을 만드는 동안 무료 계층을 선택할 수 있으며 계정을 만든 후에는 설정할 수 없습니다.

### <a name="azure-portal"></a>Azure portal

Azure Portal을 사용하여 계정을 만들 때 **무료 계층 할인 적용** 옵션을 **적용** 으로 설정합니다. 단계별 지침은 [무료 계층으로 새 계정 만들기](create-cosmosdb-resources-portal.md) 문서를 참조하세요.

### <a name="arm-template"></a>ARM 템플릿

ARM 템플릿을 사용하여 무료 계층 계정을 만들려면 `"enableFreeTier": true` 속성을 설정합니다. 전체 템플릿은 [무료 계층을 사용한 ARM 템플릿](manage-with-templates.md#free-tier) 배포 예제를 참조하세요.

### <a name="cli"></a>CLI

CLI를 사용하여 무료 계층이 있는 계정을 만들려면 `--enable-free-tier` 매개 변수를 true로 설정합니다.

```azurecli-interactive
# Create a free tier account for SQL API
az cosmosdb create \
    -n "Myaccount" \
    -g "MyResourcegroup" \
    --enable-free-tier true \
    --default-consistency-level "Session"
    
```

### <a name="powershell"></a>PowerShell

Azure PowerShell을 사용하여 무료 계층이 있는 계정을 만들려면 `-EnableFreeTier` 매개 변수를 true로 설정합니다.

```powershell-interactive
# Create a free tier account for SQL API. 
New-AzCosmosDBAccount -ResourceGroupName "MyResourcegroup" `
    -Name "Myaccount" `
    -ApiKind "sql" `
    -EnableFreeTier true `
    -DefaultConsistencyLevel "Session" `
```

## <a name="next-steps"></a>다음 단계

무료 계층 계정을 만든 후 다음 문서를 참고하여 Azure Cosmos DB로 앱 빌드를 시작할 수 있습니다.

* [.NET V4 SDK를 사용하는 콘솔 앱을 빌드](create-sql-api-dotnet-v4.md)하여 Azure Cosmos DB 리소스를 관리합니다.
* [Azure Cosmos DB의 API for MongoDB를 사용하는 .NET 웹앱 빌드](mongodb/create-mongodb-dotnet.md)
* [갤러리에서 Notebook을 다운로드](publish-notebook-gallery.md#download-a-notebook-from-the-gallery)하고 데이터를 분석합니다.
* [Azure Cosmos DB 요금 청구 이해](understand-your-bill.md)에 대한 자세한 정보
