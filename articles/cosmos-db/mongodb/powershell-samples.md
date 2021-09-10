---
title: Azure Cosmos DB API for MongoDB에 대한 Azure PowerShell 샘플
description: Azure Cosmos DB API for MongoDB에서 일반 작업 수행을 위한 Azure PowerShell 가져오기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 4f30f265a3e4865ea68782088661f9153662d1e5
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123039716"
---
# <a name="azure-powershell-samples-for-azure-cosmos-db-api-for-mongodb"></a>Azure Cosmos DB API for MongoDB에 대한 Azure PowerShell 샘플
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

다음 표에는 Azure Cosmos DB에 일반적으로 사용되는 Azure PowerShell 스크립트의 링크가 포함되어 있습니다. 오른쪽의 링크를 사용하여 API 관련 샘플로 이동합니다. 일반적인 샘플은 모든 API에서 동일합니다. 모든 Azure Cosmos DB PowerShell cmdlet에 대한 참조 페이지는 [Azure PowerShell 참조](/powershell/module/az.cosmosdb)에 제공됩니다. `Az.CosmosDB` 모듈은 이제 `Az` 모듈의 일부입니다. 최신 버전의 Az 모듈을 [다운로드하고 설치](/powershell/azure/install-az-ps)하여 Azure Cosmos DB cmdlet을 가져옵니다. [PowerShell 갤러리](https://www.powershellgallery.com/packages/Az/5.4.0)에서 최신 버전을 가져올 수도 있습니다. [GitHub의 Cosmos DB PowerShell 샘플](https://github.com/Azure/azure-docs-powershell-samples/tree/master/cosmosdb)에서 GitHub 리포지토리의 Cosmos DB에 대한 PowerShell 샘플을 포크할 수도 있습니다.

## <a name="common-samples"></a>일반적인 샘플

|Task | 설명 |
|---|---|
|[계정 업데이트](../scripts/powershell/common/account-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB 계정의 기본 일관성 수준을 업데이트합니다. |
|[계정 지역 업데이트](../scripts/powershell/common/update-region.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Cosmos DB 계정의 지역을 업데이트합니다. |
|[장애 조치(failover) 우선순위 변경 또는 장애 조치(failover) 트리거](../scripts/powershell/common/failover-priority-update.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정의 장애 조치(failover) 우선순위를 변경하거나 수동 장애 조치(failover)를 트리거합니다. |
|[계정 키 또는 연결 문자열](../scripts/powershell/common/keys-connection-strings.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 기본 및 보조 키, 연결 문자열을 가져오거나 Azure Cosmos DB 계정의 계정 키를 다시 생성합니다. |
|[IP 방화벽을 사용하여 Cosmos 계정 만들기](../scripts/powershell/common/firewall-create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 활성화된 IP 방화벽을 사용하여 Azure Cosmos DB 계정을 만듭니다. |
|||

## <a name="mongo-db-api-samples"></a>Mongo DB API 샘플

|Task | 설명 |
|---|---|
|[계정, 데이터베이스 및 컬렉션 만들기](../scripts/powershell/mongodb/create.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Azure Cosmos 계정, 데이터베이스 및 컬렉션을 만듭니다. |
|[자동 스케일링되는 계정, 데이터베이스 및 컬렉션 만들기](../scripts/powershell/mongodb/autoscale.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 스케일링되는 Azure Cosmos 계정, 데이터베이스 및 컬렉션을 만듭니다. |
|[데이터베이스 또는 컬렉션 나열 또는 가져오기](../scripts/powershell/mongodb/list-get.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 데이터베이스 또는 컬렉션을 나열하거나 가져옵니다. |
|[처리량 작업](../scripts/powershell/mongodb/throughput.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 자동 크기 조정과 표준 처리량 간의 가져오기, 업데이트 및 마이그레이션을 포함하여 데이터베이스 또는 컬렉션에 대한 처리량 작업입니다. |
|[삭제에서 리소스 잠그기](../scripts/powershell/mongodb/lock.md?toc=%2fpowershell%2fmodule%2ftoc.json)| 리소스 잠금을 사용하여 리소스를 삭제하지 않습니다. |
|||

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.