---
title: MongoDB의 Azure Cosmos DB API용 Resource Manager 템플릿
description: Azure Resource Manager 템플릿을 사용하여 MongoDB의 Azure Cosmos DB API를 만들고 구성합니다.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 08/26/2021
ms.author: mjbrown
ms.openlocfilehash: 67e07ada1b4782d2dc1a13c2bc7bb6c22371c52a
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035662"
---
# <a name="manage-azure-cosmos-db-mongodb-api-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB MongoDB API 리소스 관리
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 MongoDB API용 Azure Cosmos DB 계정, 데이터베이스 및 컬렉션을 배포 및 관리하는 방법을 알아봅니다.

이 문서에는 MongoDB용 Azure Cosmos DB API의 예제만 있으며, 다른 API 형식 계정의 예제를 찾으려면 [Cassandra](../cassandra/templates-samples.md), [Gremlin](../templates-samples-gremlin.md), [SQL](../templates-samples-sql.md), [Table](../table/resource-manager-templates.md)의 Azure Cosmos DB API에서 Azure Resource Manager 템플릿 사용 문서를 참조하세요.

> [!IMPORTANT]
>
> * 계정 이름은 44자(모두 소문자)로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s로 템플릿을 다시 배포합니다.
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.

아래의 Azure Cosmos DB 리소스를 만들려면 다음 예제 템플릿을 새 json 파일에 복사합니다. 필요에 따라 이름 및 값이 다른 동일한 리소스의 여러 인스턴스를 배포할 때 매개 변수 json 파일을 만들 수 있습니다. [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) 및 [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md)를 비롯한 Azure Resource Manager 템플릿을 배포할 수 있는 여러 가지 방법이 있습니다.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-for-mongodb-with-autoscale-provisioned-throughput"></a>자동 크기 조정 프로비저닝된 처리량을 포함하는 MongoDB용 Azure Cosmos 계정

이 템플릿은 데이터베이스 수준에서 자동 크기 조정 처리량을 공유하는 두 개의 컬렉션을 사용하여 MongoDB API(3.2 또는 3.6)용 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-mongodb-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb-autoscale/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-for-mongodb-with-standard-provisioned-throughput"></a>표준 프로비저닝된 처리량을 포함하는 MongoDB용 Azure Cosmos 계정

이 템플릿은 데이터베이스 수준에서 400 RU/초 표준(수동) 처리량을 공유하는 두 개의 컬렉션을 사용하여 MongoDB API(3.2 또는 3.6)용 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-mongodb%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-mongodb/azuredeploy.json":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)
* [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.DocumentDB&pageNumber=1&sort=Popular)
* [일반적인 Azure Resource Manager 배포 오류 문제 해결](../../azure-resource-manager/templates/common-deployment-errors.md)
* Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하시나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    * 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    * 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.