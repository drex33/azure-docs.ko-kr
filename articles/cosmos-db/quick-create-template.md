---
title: 빠른 시작 - Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 및 컨테이너 만들기
description: Azure Resource Manager 템플릿을 사용하여 Azure Cosmos 데이터베이스와 컨테이너를 만드는 방법을 보여 주는 빠른 시작입니다.
author: SnehaGunda
ms.author: sngun
tags: azure-resource-manager
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: subject-armqs, devx-track-azurepowershell
ms.openlocfilehash: 0870651a0e118577ada7ac0739bf7abcc7ffc550
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123031222"
---
# <a name="quickstart-create-an-azure-cosmos-db-and-a-container-by-using-an-arm-template"></a>빠른 시작: ARM 템플릿을 사용하여 Azure Cosmos DB 및 컨테이너 만들기
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB는 모든 규모의 개방형 API를 포함하는 Microsoft의 빠른 NoSQL 데이터베이스입니다. Azure Cosmos DB를 사용하여 키/값 데이터베이스, 문서 데이터베이스 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있습니다. 이 빠른 시작에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 배포하여 Azure Cosmos 데이터베이스와 해당 데이터베이스 내에 컨테이너를 만드는 프로세스에 대해 중점적으로 설명합니다. 데이터는 나중에 이 컨테이너에 저장할 수 있습니다.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

환경이 필수 구성 요소를 충족하고 ARM 템플릿 사용에 익숙한 경우 **Azure에 배포** 단추를 선택합니다. 그러면 Azure Portal에서 템플릿이 열립니다.

[:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql%2Fazuredeploy.json)

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독 또는 Azure Cosmos DB 체험 계정

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- [!INCLUDE [cosmos-db-emulator-docdb-api](includes/cosmos-db-emulator-docdb-api.md)]

## <a name="review-the-template"></a>템플릿 검토

이 빠른 시작에서 사용되는 템플릿은 [Azure 빠른 시작 템플릿](https://azure.microsoft.com/resources/templates/cosmosdb-sql/)에서 나온 것입니다.

:::code language="json" source="~/quickstart-templates/quickstarts/microsoft.documentdb/cosmosdb-sql/azuredeploy.json":::

템플릿에 정의된 세 가지 Azure 리소스는 다음과 같습니다.

* [Microsoft.DocumentDB/databaseAccounts](/azure/templates/microsoft.documentdb/databaseaccounts): Azure Cosmos 계정을 만듭니다.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases): Azure Cosmos 데이터베이스를 만듭니다.

* [Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers](/azure/templates/microsoft.documentdb/databaseaccounts/sqldatabases/containers): Azure Cosmos 컨테이너를 만듭니다.

더 많은 Azure Cosmos DB 템플릿 샘플은 [빠른 시작 템플릿 갤러리](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Documentdb)에서 찾을 수 있습니다.

## <a name="deploy-the-template"></a>템플릿 배포

1. 다음 이미지를 선택하고 Azure에 로그인하여 템플릿을 엽니다. 이 템플릿은 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 만듭니다.

   [:::image type="content" source="../media/template-deployments/deploy-to-azure.svg" alt-text="Azure에 배포":::](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.documentdb%2Fcosmosdb-sql%2Fazuredeploy.json)

2. 다음 값을 선택하거나 입력합니다.

   :::image type="content" source="./media/quick-create-template/create-cosmosdb-using-template-portal.png" alt-text="ARM 템플릿, Azure Cosmos DB 통합, 배포 포털":::

    지정되지 않은 경우 기본값을 사용하여 Azure Cosmos 리소스를 만듭니다.

    * **구독**: Azure 구독을 선택합니다.
    * **리소스 그룹**: **새로 만들기** 를 선택하고 리소스 그룹에 고유한 이름을 입력한 다음, **확인** 을 클릭합니다.
    * **위치**: 위치를 선택합니다.  예: **미국 중부**
    * **계정 이름**: Azure Cosmos 계정의 이름을 입력합니다. 전역적으로 고유해야 합니다.
    * **위치**: Azure Cosmos 계정을 만들려는 위치를 입력합니다. Azure Cosmos 계정은 리소스 그룹과 동일한 위치에 있을 수 있습니다.
    * **주 지역**: Azure Cosmos 계정의 주 복제본 지역입니다.
    * **보조 지역**: Azure Cosmos 계정의 보조 복제본 지역입니다.
    * **기본 일관성 수준**: Azure Cosmos 계정의 기본 일관성 수준입니다.
    * **최대 부실 접두사**: 최대 부실 요청. BoundedStaleness에 필요합니다.
    * **초당 최대 간격**: 최대 지연 시간입니다. BoundedStaleness에 필요합니다.
    * **데이터베이스 이름**: Azure Cosmos 데이터베이스의 이름입니다.
    * **컨테이너 이름**: Azure Cosmos 컨테이너의 이름입니다.
    * **처리량**:  컨테이너의 처리량입니다. 최소 처리량 값은 400RU/s입니다.
    * **위에 명시된 사용 약관에 동의함**: 선택합니다.

3. **구매** 를 선택합니다. Azure Cosmos 계정이 성공적으로 배포되면 알림을 받게 됩니다.

   :::image type="content" source="./media/quick-create-template/resource-manager-template-portal-deployment-notification.png" alt-text="ARM 템플릿, Cosmos DB 통합, 배포 포털 알림":::

Azure Portal은 템플릿을 배포하는데 사용됩니다. Azure Portal 외에도 Azure PowerShell, Azure CLI 및 REST API를 사용할 수 있습니다. 다른 배포 방법을 알아보려면 [템플릿 배포](../azure-resource-manager/templates/deploy-powershell.md)를 참조하세요.

## <a name="validate-the-deployment"></a>배포 유효성 검사

Azure Portal을 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 확인하거나, 다음 Azure CLI 또는 Azure PowerShell 스크립트를 사용하여 만들어진 비밀을 나열할 수 있습니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter your Azure Cosmos account name:" &&
read cosmosAccountName &&
echo "Enter the resource group where the Azure Cosmos account exists:" &&
read resourcegroupName &&
az cosmosdb show -g $resourcegroupName -n $cosmosAccountName
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name where your Azure Cosmos account exists"
(Get-AzResource -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>리소스 정리

후속 빠른 시작 및 자습서를 계속 사용하려는 경우 이러한 리소스를 그대로 유지할 수 있습니다.
더 이상 필요하지 않은 경우 리소스 그룹을 삭제합니다. 그러면 해당 Azure Cosmos 계정 및 관련 리소스가 삭제됩니다. Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 삭제하려면 다음을 수행합니다.

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 ARM 템플릿을 사용하여 Azure Cosmos 계정, 데이터베이스 및 컨테이너를 만들고 배포의 유효성을 검사했습니다. Azure Cosmos DB 및 Azure Resource Manager에 대해 자세히 알아보려면 아래 문서로 계속 진행하세요.

- [Azure Cosmos DB 개요](introduction.md) 참조
- [Azure Resource Manager](../azure-resource-manager/management/overview.md)에 대해 자세히 알아보기
- 다른 [Azure Cosmos DB Resource Manager 템플릿](./templates-samples-sql.md) 가져오기
- Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
    - 기존 데이터베이스 클러스터의 vcore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
    - 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-with-capacity-planner.md)에 대해 읽어보세요.
