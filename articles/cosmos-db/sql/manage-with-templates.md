---
title: Resource Manager 템플릿을 사용하여 Azure Cosmos DB 만들기 및 관리
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB for Core(SQL) API 만들기 및 구성
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/13/2021
ms.author: mjbrown
ms.openlocfilehash: 0f511f51183e167c4ad42c6c2f5a8425b49bcbb4
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116945"
---
# <a name="manage-azure-cosmos-db-core-sql-api-resources-with-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB Core(SQL) API 리소스 관리

[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 계정, 데이터베이스 및 컨테이너를 배포 및 관리하는 방법을 알아봅니다.

이 문서에서는 Core(SQL) API 계정에 대한 Azure Resource Manager 템플릿 예제만 보여줍니다. [Cassandra](../cassandra/templates-samples.md), [Gremlin](../graph/resource-manager-template-samples.md), [MongoDB](../mongodb/resource-manager-template-samples.md) 및 [Table](../table/resource-manager-templates.md) API에 대한 템플릿 예제를 찾을 수도 있습니다.

> [!IMPORTANT]
>
> * 계정 이름은 44자(모두 소문자)로 제한됩니다.
> * 처리량 값을 변경하려면 업데이트된 RU/s로 템플릿을 다시 배포합니다.
> * Azure Cosmos 계정에 위치를 추가하거나 제거하면 다른 속성을 동시에 수정할 수 없습니다. 이러한 작업은 별도로 수행해야 합니다.
> * Azure Resource Manager가 리소스 URI와 함께 작동하는 방식을 위반하므로 Azure Cosmos DB 리소스의 이름을 바꿀 수 없습니다.
> * 데이터베이스 수준에서 처리량을 프로비전하고 모든 컨테이너에서 공유하려면 데이터베이스 옵션 속성에 처리량 값을 적용합니다.

아래의 Azure Cosmos DB 리소스를 만들려면 다음 예제 템플릿을 새 json 파일에 복사합니다. 필요에 따라 이름 및 값이 다른 동일한 리소스의 여러 인스턴스를 배포할 때 매개 변수 json 파일을 만들 수 있습니다. [Azure Portal](../../azure-resource-manager/templates/deploy-portal.md), [Azure CLI](../../azure-resource-manager/templates/deploy-cli.md), [Azure PowerShell](../../azure-resource-manager/templates/deploy-powershell.md) 및 [GitHub](../../azure-resource-manager/templates/deploy-to-azure-button.md)를 비롯한 Azure Resource Manager 템플릿을 배포할 수 있는 여러 가지 방법이 있습니다.

<a id="create-autoscale"></a>

## <a name="azure-cosmos-account-with-autoscale-throughput"></a>자동 크기 조정 처리량을 사용하는 Azure Cosmos 계정

이 템플릿은 일관성 및 장애 조치(failover) 옵션과 함께 대부분의 정책 옵션이 활성화된 자동 크기 조정 처리량에 대해 구성된 데이터베이스 및 컨테이너를 사용하여 두 지역에 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-autoscale%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-autoscale/azuredeploy.json":::

<a id="create-analytical-store"></a>

## <a name="azure-cosmos-account-with-analytical-store"></a>분석 저장소가 있는 Azure Cosmos 계정

이 템플릿은 분석 TTL이 설정된 컨테이너와 수동 또는 자동 크기 조정 처리량에 대한 옵션을 사용하여 한 지역에 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-analytical-store%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-analytical-store/azuredeploy.json":::

<a id="create-manual"></a>

## <a name="azure-cosmos-account-with-standard-provisioned-throughput"></a>프로비저닝된 표준 처리량을 사용하는 Azure Cosmos 계정

이 템플릿은 일관성 및 장애 조치(failover) 옵션과 함께 대부분의 정책 옵션이 활성화된 표준 처리량에 대해 구성된 데이터베이스 및 컨테이너를 사용하여 두 지역에 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/azuredeploy.json":::

<a id="create-sproc"></a>

## <a name="azure-cosmos-db-container-with-server-side-functionality"></a>서버 쪽 기능이 있는 Azure Cosmos DB 컨테이너

이 템플릿은 저장 프로시저, 트리거 및 사용자 정의 함수를 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-container-sprocs%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-container-sprocs/azuredeploy.json":::

<a id="create-rbac"></a>

## <a name="azure-cosmos-db-account-with-azure-ad-and-rbac"></a>Azure AD 및 RBAC를 사용하는 Azure Cosmos DB 계정

이 템플릿은 SQL Cosmos 계정, 고유하게 유지 관리되는 역할 정의 및 AAD ID에 대해 고유하게 유지 관리되는 역할 할당을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql-rbac%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql-rbac/azuredeploy.json":::

<a id="free-tier"></a>

## <a name="free-tier-azure-cosmos-db-account"></a>체험 계층 Azure Cosmos DB 계정

이 템플릿은 최대 25개의 컨테이너와 공유할 수 있는 공유 처리량이 있는 데이터베이스와 체험 계층 Azure Cosmos 계정을 만듭니다. 이 템플릿은 Azure 빠른 시작 템플릿 갤러리에서 한 번 클릭으로 배포하는 경우에도 사용할 수 있습니다.

[:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-free%2Fazuredeploy.json)

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-free/azuredeploy.json":::

## <a name="next-steps"></a>다음 단계

다음은 몇 가지 추가 리소스입니다.

* [Azure Resource Manager 설명서](../../azure-resource-manager/index.yml)
* [Azure Cosmos DB 리소스 공급자 스키마](/azure/templates/microsoft.documentdb/allversions)
* [Azure Cosmos DB 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb&pageNumber=1&sort=Popular)
* [일반적인 Azure Resource Manager 배포 오류 문제 해결](../../azure-resource-manager/templates/common-deployment-errors.md)