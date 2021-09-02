---
title: Azure Cosmos DB에서 데이터베이스 계정을 관리하는 방법 알아보기
description: Azure portal, PowerShell, CLI, Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 리소스를 관리하는 방법 알아보기
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/13/2021
ms.author: mjbrown
ms.openlocfilehash: 124c5fba529d39a675f92642413d4305c58debda
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101457"
---
# <a name="manage-an-azure-cosmos-account"></a>Azure Cosmos 계정 관리
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

이 문서에서는 Azure Portal, Azure PowerShell, Azure CLI 및 Azure Resource Manager 템플릿을 사용하여 Azure Cosmos 계정에서 다양한 작업을 관리하는 방법을 설명합니다.

## <a name="create-an-account"></a>계정 만들기

### <a name="azure-portal"></a><a id="create-database-account-via-portal"></a>Azure Portal

[!INCLUDE [cosmos-db-create-dbaccount](includes/cosmos-db-create-dbaccount.md)]

### <a name="azure-cli"></a><a id="create-database-account-via-cli"></a>Azure CLI

[Azure CLI를 사용하여 Azure Cosmos DB 계정 만들기](sql/manage-with-cli.md#create-an-azure-cosmos-db-account)를 참조하세요.

### <a name="azure-powershell"></a><a id="create-database-account-via-ps"></a>Azure PowerShell

[PowerShell을 사용하여 Azure Cosmos DB 계정 만들기](manage-with-powershell.md#create-account)를 참조하세요.

### <a name="azure-resource-manager-template"></a><a id="create-database-account-via-arm-template"></a>Azure Resource Manager 템플릿

[Azure Resource Manager 템플릿을 사용하여 Azure Cosmos DB 계정 만들기](./manage-with-templates.md)를 참조하세요.

## <a name="addremove-regions-from-your-database-account"></a>데이터베이스 계정에서 Azure 지역 추가/제거

> [!TIP]
> 새 지역이 추가되면 해당 지역이 사용 가능으로 표시되기 전에 모든 데이터를 완전히 복제하고 새 지역에 커밋해야 합니다. 이 작업에 걸리는 시간은 해당 계정 내에 저장된 데이터 양에 따라 달라집니다.

### <a name="azure-portal"></a><a id="add-remove-regions-via-portal"></a>Azure Portal

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.

1. Azure Cosmos 계정으로 이동하고, **전역적으로 데이터 복제** 메뉴를 엽니다.

1. 지역을 추가하려면 지도에서 원하는 지역에 해당하는 **+** 레이블이 있는 육각형을 선택합니다. 또는, 지역을 추가하려면 **+ 지역 추가** 옵션을 선택하고 드롭다운 메뉴에서 Azure 지역을 선택합니다.

1. 지역을 제거하려면 확인 표시가 있는 파란색 육각형을 선택하여 맵에서 하나 이상의 지역을 지웁니다. 또는 오른쪽의 지역 옆에 있는 "휴지통"(🗑) 아이콘을 선택합니다.

1. 변경 내용을 저장하려면 **확인** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/add-region.png" alt-text="지역 추가 또는 제거 메뉴":::

단일 지역 쓰기 모드에서는 쓰기 지역을 제거할 수 없습니다. 현재 쓰기 지역을 삭제하려면 먼저 다른 Azure 지역으로 장애 조치해야 합니다.

다중 지역 쓰기 모드에서는 하나 이상의 Azure 지역이 있으면 Azure 지역을 추가하거나 제거할 수 있습니다.

### <a name="azure-cli"></a><a id="add-remove-regions-via-cli"></a>Azure CLI

[Azure CLI를 사용하여 영역 추가 또는 제거](sql/manage-with-cli.md#add-or-remove-regions)를 참조하세요.

### <a name="azure-powershell"></a><a id="add-remove-regions-via-ps"></a>Azure PowerShell

[PowerShell을 사용하여 영역 추가 또는 제거](manage-with-powershell.md#update-account)를 참조하세요.

## <a name="configure-multiple-write-regions"></a><a id="configure-multiple-write-regions"></a>다중 쓰기 지역 구성

### <a name="azure-portal"></a><a id="configure-multiple-write-regions-portal"></a>Azure Portal

**전역적으로 데이터 복제** 탭을 열고 **사용** 을 선택하여 다중 지역 쓰기를 사용하도록 설정합니다. 다중 지역 쓰기를 사용하도록 설정한 후에는 현재 계정에 있는 모든 읽기 지역이 읽기 및 쓰기 지역이 됩니다.

:::image type="content" source="./media/how-to-manage-database-account/single-to-multi-master.png" alt-text="다중 지역 쓰기를 구성하는 Azure Cosmos 계정 스크린샷":::

### <a name="azure-cli"></a><a id="configure-multiple-write-regions-cli"></a>Azure CLI

[Azure CLI로 다중 쓰기 영역 사용](sql/manage-with-cli.md#enable-multiple-write-regions)을 참조하세요.

### <a name="azure-powershell"></a><a id="configure-multiple-write-regions-ps"></a>Azure PowerShell

[PowerShell로 다중 쓰기 영역 사용](manage-with-powershell.md#multi-region-writes)을 참조하세요.

### <a name="resource-manager-template"></a><a id="configure-multiple-write-regions-arm"></a>Resource Manager 템플릿

계정 및 설정 `enableMultipleWriteLocations: true`를 생성하는 데 사용되는 Resource Manager 템플릿을 배포하면 단일 쓰기 지역에서 다중 쓰기 지역으로 계정을 마이그레이션할 수 있습니다. 다음 Azure Resource Manager 템플릿은 두 개의 지역 및 다중 쓰기 위치가 활성화된 SQL API용 Azure Cosmos 계정을 배포하는 최소 템플릿입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "String"
        },
        "location": {
            "type": "String",
            "defaultValue": "[resourceGroup().location]"
        },
        "primaryRegion":{
            "type":"string",
            "metadata": {
                "description": "The primary replica region for the Cosmos DB account."
            }
        },
        "secondaryRegion":{
            "type":"string",
            "metadata": {
              "description": "The secondary replica region for the Cosmos DB account."
          }
        }
    },
    "resources": [
        {
            "type": "Microsoft.DocumentDb/databaseAccounts",
            "kind": "GlobalDocumentDB",
            "name": "[parameters('name')]",
            "apiVersion": "2021-03-15",
            "location": "[parameters('location')]",
            "tags": {},
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": { "defaultConsistencyLevel": "Session" },
                "locations":
                [
                    {
                        "locationName": "[parameters('primaryRegion')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    },
                    {
                        "locationName": "[parameters('secondaryRegion')]",
                        "failoverPriority": 1,
                        "isZoneRedundant": false
                    }
                ],
                "enableMultipleWriteLocations": true
            }
        }
    ]
}
```

## <a name="enable-automatic-failover-for-your-azure-cosmos-account"></a><a id="automatic-failover"></a>Azure Cosmos 계정에 자동 장애 조치(failover) 사용

자동 장애 조치(failover) 옵션을 통해 Azure Cosmos DB는 장애 조치(failover) 우선 순위가 가장 높은 지역으로 장애 조치(failover)할 수 있으며, 지역을 사용할 수 없는 경우 사용자 작업이 필요하지 않습니다. 자동 장애 조치(failover)를 사용하도록 설정한 경우 지역 우선 순위를 수정할 수 있습니다. 계정에는 자동 장애 조치(failover)를 사용하도록 설정하기 위해 두 개 이상의 지역이 있어야 합니다.

### <a name="azure-portal"></a><a id="enable-automatic-failover-via-portal"></a>Azure Portal

1. Azure Cosmos 계정에서 **전역적으로 데이터 복제** 창을 엽니다.

2. 창의 위쪽에서 **자동 장애 조치** 를 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[전역적으로 데이터 복제] 메뉴":::

3. **자동 장애 조치** 창에서 **자동 장애 조치 사용** 을 **켜기** 로 설정합니다. 

4. **저장** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="자동 장애 조치(failover) 포털 메뉴":::

### <a name="azure-cli"></a><a id="enable-automatic-failover-via-cli"></a>Azure CLI

[Azure CLI로 자동 장애 조치(failover) 사용](sql/manage-with-cli.md#enable-automatic-failover)을 참조하세요.

### <a name="azure-powershell"></a><a id="enable-automatic-failover-via-ps"></a>Azure PowerShell

[PowerShell로 자동 장애 조치(failover) 사용](manage-with-powershell.md#enable-automatic-failover)을 참조하세요.

## <a name="set-failover-priorities-for-your-azure-cosmos-account"></a>Azure Cosmos 계정의 장애 조치(failover) 우선 순위 설정

Cosmos 계정이 자동 장애 조치(failover)에 대해 구성된 후에는 지역에 대한 장애 조치(failover) 우선 순위를 변경할 수 있습니다.

> [!IMPORTANT]
> 계정이 자동 장애 조치(failover)에 대해 구성된 경우에는 쓰기 지역(장애 조치 우선 순위 0)을 수정할 수 없습니다. 쓰기 지역을 변경하려면 자동 장애 조치(failover)를 사용하지 않도록 설정하고 수동 장애 조치(failover)를 수행해야 합니다.

### <a name="azure-portal"></a><a id="set-failover-priorities-via-portal"></a>Azure Portal

1. Azure Cosmos 계정에서 **전역적으로 데이터 복제** 창을 엽니다.

2. 창의 위쪽에서 **자동 장애 조치** 를 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[전역적으로 데이터 복제] 메뉴":::

3. **자동 장애 조치** 창에서 **자동 장애 조치 사용** 을 **켜기** 로 설정합니다.

4. 장애 조치 우선 순위를 수정하려면 마우스로 가리킬 때 나타나는 행 왼쪽의 점 세 개를 통해 읽기 지역을 끕니다.

5. **저장** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/automatic-failover.png" alt-text="자동 장애 조치(failover) 포털 메뉴":::

### <a name="azure-cli"></a><a id="set-failover-priorities-via-cli"></a>Azure CLI

[Azure CLI로 장애 조치(failover) 우선 순위 설정](sql/manage-with-cli.md#set-failover-priority)을 참조하세요.

### <a name="azure-powershell"></a><a id="set-failover-priorities-via-ps"></a>Azure PowerShell

[PowerShell로 장애 조치(failover) 우선 순위 설정](manage-with-powershell.md#modify-failover-priority)을 참조하세요.

## <a name="perform-manual-failover-on-an-azure-cosmos-account"></a><a id="manual-failover"></a>Azure Cosmos 계정에서 수동 장애 조치(failover) 수행

> [!IMPORTANT]
> 이 작업이 성공하기 위해서는 수동 장애 조치(failover)에 대해 Azure Cosmos 계정을 구성해야 합니다.

수동 장애 조치(failover)를 수행하기 위한 프로세스에는 계정의 쓰기 지역(장애 조치 우선 순위 = 0)을 계정에 대해 구성된 다른 지역으로 변경하는 것이 포함됩니다.

> [!NOTE]
> 쓰기 지역이 여러 개인 계정은 수동으로 장애 조치(failover)할 수 없습니다. Azure Cosmos SDK를 사용하는 애플리케이션의 경우 SDK는 지역을 사용할 수 없게 되는 시기를 탐지한 후, 다음 가장 가까운 지역으로 자동 리디렉션합니다.

### <a name="azure-portal"></a><a id="enable-manual-failover-via-portal"></a>Azure Portal

1. Azure Cosmos 계정으로 이동하고, **전역적으로 데이터 복제** 메뉴를 엽니다.

2. 메뉴의 위쪽에서 **수동 장애 조치** 를 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/replicate-data-globally.png" alt-text="[전역적으로 데이터 복제] 메뉴":::

3. **수동 장애 조치** 메뉴에서 새 쓰기 지역을 선택합니다. 이 옵션이 쓰기 영역을 변경한다는 것을 알고 있음을 나타내는 확인란을 선택합니다.

4. 장애 조치를 트리거하려면 **확인** 을 선택합니다.

   :::image type="content" source="./media/how-to-manage-database-account/manual-failover.png" alt-text="수동 장애 조치(failover) 포털 메뉴":::

### <a name="azure-cli"></a><a id="enable-manual-failover-via-cli"></a>Azure CLI

[Azure CLI로 수동 장애 조치(failover) 트리거](sql/manage-with-cli.md#trigger-manual-failover)를 참조하세요.

### <a name="azure-powershell"></a><a id="enable-manual-failover-via-ps"></a>Azure PowerShell

[PowerShell로 수동 장애 조치(failover) 트리거](manage-with-powershell.md#trigger-manual-failover)를 참조하세요.

## <a name="next-steps"></a>다음 단계

데이터베이스 및 컨테이너와 함께 Azure Cosmos 계정을 관리하는 방법에 대한 자세한 내용 및 예제는 다음 문서를 읽어보세요.

* [Azure PowerShell을 사용하여 Azure Cosmos DB 관리](manage-with-powershell.md)
* [Azure CLI를 사용하여 Azure Cosmos DB 관리](sql/manage-with-cli.md)