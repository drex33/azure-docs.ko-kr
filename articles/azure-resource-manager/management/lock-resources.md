---
title: 변경을 방지하기 위해 리소스 잠그기
description: 모든 사용자 및 역할에 대해 잠금을 적용하여 사용자가 Azure 리소스를 업데이트하거나 삭제하지 못하도록 합니다.
ms.topic: conceptual
ms.date: 07/01/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 324aed15446e83e0853f4b590c7d679a7f598abe
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491396"
---
# <a name="lock-resources-to-prevent-unexpected-changes"></a>예기치 않은 변경을 방지하기 위해 리소스 잠그기

관리자는 구독, 리소스 그룹 또는 리소스에 잠금을 설정하여 조직의 다른 사용자가 실수로 중요한 리소스를 삭제 또는 수정하지 못하게 할 수 있습니다. 잠금은 사용자가 가질 수 있는 사용 권한을 재정의합니다.

잠금 수준을 **CanNotDelete** 또는 **ReadOnly** 로 설정할 수 있습니다. 포털에서 잠금은 각각 **삭제** 및 **읽기 전용** 으로 지칭됩니다.

- **CanNotDelete** 는 권한이 부여된 사용자가 리소스를 읽고 수정할 수 있지만 삭제할 수 없음을 의미합니다.
- **ReadOnly** 는 권한이 부여된 사용자가 리소스를 읽을 수 있지만 해당 리소스를 삭제하거나 업데이트할 수 없음을 의미합니다. 이 잠금을 적용하는 것은 권한 있는 모든 사용자에게 **판독기** 역할에서 부여한 권한을 제한하는 것과 유사합니다.

역할 기반 액세스 제어와 달리 관리 잠금을 사용하면 모든 사용자와 역할에 제한이 적용됩니다. 사용자 및 역할에 대한 권한 설정에 대해 알아보려면 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../role-based-access-control/role-assignments-portal.md)를 참조하세요.

## <a name="lock-inheritance"></a>상속 잠금

부모 범위에서 잠금을 적용하면 해당 범위 내 모든 리소스가 동일한 잠금을 상속합니다. 나중에 추가하는 리소스도 부모의 잠금을 상속합니다. 상속의 가장 제한적인 잠금이 우선 적용됩니다.

## <a name="understand-scope-of-locks"></a>잠금 범위 이해

> [!NOTE]
> 잠금이 모든 유형의 작업에 적용되지는 않는다는 점을 이해해야 합니다. Azure 작업은 컨트롤 플레인과 데이터 평면이라는 두 가지 범주로 나눌 수 있습니다. **잠금은 컨트롤 플레인 작업에만 적용됩니다.**

컨트롤 플레인 작업은 `https://management.azure.com`에 전송되는 작업입니다. 데이터 평면 작업은 `https://myaccount.blob.core.windows.net/`처럼 서비스 인스턴스에 전송되는 작업입니다. 자세한 내용은 [Azure 컨트롤 플레인 및 데이터 평면](control-plane-and-data-plane.md)을 참조하세요. 어떤 작업이 컨트롤 플레인 URL을 사용하는지 검색하려면 [Azure REST API](/rest/api/azure/)를 참조하세요.

이러한 차이는 잠금이 리소스 변경을 방지하지만 리소스가 자체 기능을 수행하는 방법을 제한하지 않는다는 것을 의미합니다.  예를 들어, SQL Database 논리 서버에 대해 ReadOnly 잠금이 지정되면 서버를 삭제하거나 수정할 수 없습니다. 해당 서버의 데이터베이스에서 데이터를 생성, 업데이트 또는 삭제할 수는 있습니다. 이러한 작업은 `https://management.azure.com`으로 전송되지 않으므로 데이터 트랜잭션이 허용됩니다.

컨트롤 플레인과 데이터 평면 작업 간의 차이점에 대한 추가 예제는 다음 섹션에서 설명합니다.

## <a name="considerations-before-applying-locks"></a>잠금 적용 전 고려 사항

리소스를 수정하지 않는 일부 작업에 실제로 잠금에 의해 차단되는 작업이 필요하므로 잠금을 적용하면 예기치 않은 결과가 발생할 수 있습니다. 잠금은 Azure Resource Manager API에 대한 POST 요청이 필요한 작업을 방지합니다. 잠금에 의해 차단되는 작업의 몇 가지 일반적인 예제는 다음과 같습니다.

- **스토리지 계정** 에 읽기 전용 잠금을 설정하면 사용자가 키를 나열할 수 없습니다. Azure Storage [키 나열](/rest/api/storagerp/storageaccounts/listkeys) 작업은 POST 요청을 통해 처리되어 계정 키에 대한 액세스를 보호하며 스토리지 계정의 데이터에 대한 모든 액세스 권한을 제공합니다. 스토리지 계정에 대한 읽기 전용 잠금을 구성하는 경우 계정 키를 소유하지 않은 사용자는 Azure AD 자격 증명을 사용하여 Blob 또는 큐 데이터에 액세스해야 합니다. 또한 읽기 전용 잠금은 스토리지 계정 또는 데이터 컨테이너(Blob 컨테이너 또는 큐)로 범위가 지정된 Azure RBAC 역할의 할당을 방지합니다.

- **스토리지 계정** 에 대한 삭제 불가 잠금은 해당 계정 내의 데이터가 삭제되거나 수정되는 것을 방지하지 않습니다. 이 유형의 잠금은 스토리지 계정 자체가 삭제되지 않도록 보호합니다. 요청이 [데이터 영역 작업](control-plane-and-data-plane.md#data-plane)을 사용하는 경우 스토리지 계정에 대한 잠금은 해당 스토리지 계정 내의 Blob, 큐, 테이블 또는 파일 데이터를 보호하지 않습니다. 그러나 요청이 [컨트롤 플레인 작업](control-plane-and-data-plane.md#control-plane)을 사용하는 경우 잠금이 해당 리소스를 보호합니다.

  예를 들어 요청에서 컨트롤 플레인 작업인 [파일 공유 - 삭제](/rest/api/storagerp/file-shares/delete)를 사용하는 경우 삭제가 거부됩니다. 요청에서 데이터 영역 작업인 [공유 삭제](/rest/api/storageservices/delete-share)를 사용하는 경우 삭제가 성공합니다. 컨트롤 플레인 작업을 사용하는 것이 좋습니다.

- **스토리지 계정** 에 대한 읽기 전용 잠금을 설정해도 해당 계정의 데이터는 삭제되거나 수정되지 않습니다. 이 잠금 유형은 스토리지 계정 자체를 삭제 또는 수정하지 않도록 보호하고 해당 스토리지 계정 내에서 Blob, 큐, 테이블 또는 파일 데이터를 보호하지 않습니다.

- **App Service** 리소스에 읽기 전용 잠금을 설정하면 해당 상호 작용이 쓰기 액세스를 필요로 하기 때문에 Visual Studio 서버 탐색기가 리소스에 대한 파일을 표시하지 않도록 방지합니다.

- **App Service 계획** 을 포함하는 **리소스 그룹** 에 대한 읽기 전용 잠금의 경우 [계획을 확장하거나 축소할](../../app-service/manage-scale-up.md)수 없습니다.

- **가상 머신** 을 포함하는 **리소스 그룹** 에 대한 읽기 전용 잠금은 모든 사용자가 가상 머신을 시작하거나 다시 시작하지 못하게 합니다. 이러한 작업에는 POST 요청이 필요합니다.

- **리소스 그룹** 에 대한 삭제 불가 잠금을 설정하면 Azure Resource Manager가 기록에서 [배포를 자동으로 삭제](../templates/deployment-history-deletions.md)할 수 없습니다. 기록에서 800개의 배포에 도달하는 경우 배포에 실패합니다.

- **Azure Backup 서비스** 가 생성한 **리소스 그룹** 에 대해 잠금을 삭제할 수 없으므로 백업이 실패합니다. 이 서비스는 최대 18개의 복원 지점을 지원합니다. 잠겨 있으면 백업 서비스에서 복원 지점이 정리할 수 없습니다. 자세한 내용은 [질문과 대답-Azure VM 백업](../../backup/backup-azure-vm-backup-faq.yml)을 참조하세요.

- **리소스 그룹** 에 대한 삭제할 수 없는 잠금의 경우 **Azure Machine Learning** 이 [Azure Machine Learning 컴퓨팅 클러스터](../../machine-learning/concept-compute-target.md#azure-machine-learning-compute-managed)를 자동 크기 조정하여 사용하지 않는 노드를 제거하지 못합니다.

- **구독** 에 대해 읽기 전용 잠금을 설정하면 **Azure Advisor** 가 제대로 작동하지 않습니다. Advisor가 쿼리 결과를 저장할 수 없습니다.

- **Application Gateway** 에 대한 읽기 전용 잠금은 애플리케이션 게이트웨이의 백 엔드 상태를 가져오지 못하게 합니다. 이 [작업은 읽기 전용 잠금으로 차단된 POST](/rest/api/application-gateway/application-gateways/backend-health)를 사용합니다.

- **AKS 클러스터** 에 대한 읽기 전용 잠금은 모든 사용자가 Azure Portal에 있는 AKS 클러스터 왼쪽 블레이드의 **Kubernetes 리소스** 섹션에서 클러스터 리소스에 액세스하는 것을 방지합니다. 이러한 작업에는 인증을 위한 POST 요청이 필요합니다.

## <a name="who-can-create-or-delete-locks"></a>잠금을 만들거나 삭제할 수 있는 사람

관리 잠금을 만들거나 삭제하려면 `Microsoft.Authorization/*` 또는 `Microsoft.Authorization/locks/*` 작업에 대한 액세스 권한이 있어야 합니다. 기본 제공 역할의 경우 **소유자** 및 **사용자 액세스 관리자** 에게만 이러한 작업의 권한이 부여됩니다.

## <a name="managed-applications-and-locks"></a>관리형 애플리케이션 및 잠금

Azure Databricks와 같은 일부 Azure 서비스는 [관리형 애플리케이션](../managed-applications/overview.md)을 사용하여 서비스를 구현합니다. 이 경우 서비스는 두 개의 리소스 그룹을 만듭니다. 한 리소스 그룹에는 서비스에 대한 개요가 포함되어 있으며 잠겨 있지 않습니다. 다른 리소스 그룹에는 서비스에 대 한 인프라가 포함되어 있으며 잠겨집니다.

인프라 리소스 그룹을 삭제하려고 하면 리소스 그룹이 잠겨 있음을 나타내는 오류가 표시됩니다. 인프라 리소스 그룹에 대한 잠금을 삭제하려고 하면 시스템 애플리케이션이 소유하고 있으므로 잠금을 삭제할 수 없다는 오류 메시지가 표시됩니다.

대신, 서비스를 삭제합니다. 그러면 인프라 리소스 그룹도 삭제됩니다.

관리형 애플리케이션의 경우 배포한 서비스를 선택합니다.

![서비스 선택](./media/lock-resources/select-service.png)

서비스에는 **관리형 리소스 그룹** 에 대한 링크가 포함되어 있습니다. 해당 리소스 그룹은 인프라를 보유하고 있으며 잠겨집니다. 직접 삭제할 수 없습니다.

![관리형 그룹 표시](./media/lock-resources/show-managed-group.png)

잠긴 인프라 리소스 그룹을 포함하여 서비스에 대한 모든 항목을 삭제하려면 서비스에 대해 **삭제** 를 선택합니다.

![서비스 삭제](./media/lock-resources/delete-service.png)

## <a name="configure-locks"></a>잠금 구성

### <a name="portal"></a>포털

[!INCLUDE [resource-manager-lock-resources](../../../includes/resource-manager-lock-resources.md)]

### <a name="template"></a>템플릿

Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 잠금을 배포할 때 잠금의 범위와 배포 범위를 알고 있어야 합니다. 리소스 그룹 또는 구독 잠금과 같은 배포 범위에서 잠금을 적용하려면 범위 속성을 설정하지 마세요. 배포 범위 내에서 리소스를 잠그면 범위 속성을 설정합니다.

다음 템플릿은 배포되는 리소스 그룹에 잠금을 적용합니다. 잠금 범위가 배포 범위와 일치하기 때문에 잠금 리소스에 범위 속성이 없습니다. 이 템플릿은 리소스 그룹 수준에서 배포됩니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "resources": [
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "rgLock",
      "properties": {
        "level": "CanNotDelete",
        "notes": "Resource group should not be deleted."
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group should not be deleted.'
  }
}
```

---

리소스 그룹을 만들어 잠그려면 해당 구독 수준에서 다음 템플릿을 배포합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "rgName": {
      "type": "string"
    },
    "rgLocation": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.Resources/resourceGroups",
      "apiVersion": "2021-04-01",
      "name": "[parameters('rgName')]",
      "location": "[parameters('rgLocation')]",
      "properties": {}
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2021-04-01",
      "name": "lockDeployment",
      "resourceGroup": "[parameters('rgName')]",
      "dependsOn": [
        "[resourceId('Microsoft.Resources/resourceGroups/', parameters('rgName'))]"
      ],
      "properties": {
        "mode": "Incremental",
        "template": {
          "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {},
          "variables": {},
          "resources": [
            {
              "type": "Microsoft.Authorization/locks",
              "apiVersion": "2016-09-01",
              "name": "rgLock",
              "properties": {
                "level": "CanNotDelete",
                "notes": "Resource group and its resources should not be deleted."
              }
            }
          ],
          "outputs": {}
        }
      }
    }
  ],
  "outputs": {}
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

주 Bicep 파일은 리소스 그룹을 만들고 [모듈](../bicep/modules.md)을 사용하여 잠금을 만듭니다.

```Bicep
targetScope = 'subscription'

param rgName string
param rgLocation string

resource createRg 'Microsoft.Resources/resourceGroups@2021-04-01' = {
  name: rgName
  location: rgLocation
}

module deployRgLock './lockRg.bicep' = {
  name: 'lockDeployment'
  scope: resourceGroup(createRg.name)
}
```

이 모듈은 리소스 그룹 잠금을 추가하는 _lockRg.bicep_ 이라는 Bicep 파일을 사용합니다.

```bicep
resource createRgLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'rgLock'
  properties: {
    level: 'CanNotDelete'
    notes: 'Resource group and its resources should not be deleted.'
  }
}
```

---

리소스 그룹 내의 **리소스** 에 잠금을 적용하는 경우 범위 속성을 추가합니다. 잠그려는 리소스의 이름으로 범위를 설정합니다.

다음 예제에서는 웹 사이트에 App Service 계획, 웹 사이트 및 잠금을 만드는 템플릿을 보여 줍니다. 잠금 범위는 웹 사이트로 설정됩니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "hostingPlanName": {
      "type": "string"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "variables": {
    "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
  },
  "resources": [
    {
      "type": "Microsoft.Web/serverfarms",
      "apiVersion": "2020-12-01",
      "name": "[parameters('hostingPlanName')]",
      "location": "[parameters('location')]",
      "sku": {
        "tier": "Free",
        "name": "f1",
        "capacity": 0
      },
      "properties": {
        "targetWorkerCount": 1
      }
    },
    {
      "type": "Microsoft.Web/sites",
      "apiVersion": "2020-12-01",
      "name": "[variables('siteName')]",
      "location": "[parameters('location')]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      }
    },
    {
      "type": "Microsoft.Authorization/locks",
      "apiVersion": "2016-09-01",
      "name": "siteLock",
      "scope": "[concat('Microsoft.Web/sites/', variables('siteName'))]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/sites', variables('siteName'))]"
      ],
      "properties": {
        "level": "CanNotDelete",
        "notes": "Site should not be deleted."
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param hostingPlanName string
param location string = resourceGroup().location

var siteName = concat('ExampleSite', uniqueString(resourceGroup().id))

resource serverFarm 'Microsoft.Web/serverfarms@2020-12-01' = {
  name: hostingPlanName
  location: location
  sku: {
    tier: 'Free'
    name: 'f1'
    capacity: 0
  }
  properties: {
    targetWorkerCount: 1
  }
}

resource webSite 'Microsoft.Web/sites@2020-12-01' = {
  name: siteName
  location: location
  properties: {
    serverFarmId: serverFarm.name
  }
}

resource siteLock 'Microsoft.Authorization/locks@2016-09-01' = {
  name: 'siteLock'
  scope: webSite
  properties:{
    level: 'CanNotDelete'
    notes: 'Site should not be deleted.'
  }
}
```

---

### <a name="azure-powershell"></a>Azure PowerShell

[New-AzResourceLock](/powershell/module/az.resources/new-azresourcelock) 명령을 사용하여 Azure PowerShell을 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurepowershell-interactive
New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [Get-AzResourceLock](/powershell/module/az.resources/get-azresourcelock)을 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock -ResourceName examplesite -ResourceType Microsoft.Web/sites -ResourceGroupName exampleresourcegroup
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
Get-AzResourceLock -ResourceGroupName exampleresourcegroup
```

리소스에 대한 잠금을 삭제하려면 다음을 사용합니다.

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup -ResourceName examplesite -ResourceType Microsoft.Web/sites).LockId
Remove-AzResourceLock -LockId $lockId
```

리소스 그룹에 대한 잠금을 삭제하려면 다음을 사용합니다.

```azurepowershell-interactive
$lockId = (Get-AzResourceLock -ResourceGroupName exampleresourcegroup).LockId
Remove-AzResourceLock -LockId $lockId
```

### <a name="azure-cli"></a>Azure CLI

[az lock create](/cli/azure/lock#az_lock_create) 명령을 사용하여 Azure CLI를 통해 배포된 리소스를 잠급니다.

리소스를 잠그려면 리소스 이름, 리소스 종류 및 해당 리소스 그룹 이름을 제공합니다.

```azurecli
az lock create --name LockSite --lock-type CanNotDelete --resource-group exampleresourcegroup --resource-name examplesite --resource-type Microsoft.Web/sites
```

리소스 그룹을 잠그려면 해당 리소스 그룹의 이름을 제공합니다.

```azurecli
az lock create --name LockGroup --lock-type CanNotDelete --resource-group exampleresourcegroup
```

잠금에 대한 정보를 가져오려면 [az lock list](/cli/azure/lock#az_lock_list)를 사용합니다. 구독의 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list
```

리소스에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup --resource-name examplesite --namespace Microsoft.Web --resource-type sites --parent ""
```

리소스 그룹에 대한 모든 잠금을 가져오려면 다음을 사용합니다.

```azurecli
az lock list --resource-group exampleresourcegroup
```

리소스에 대한 잠금을 삭제하려면 다음을 사용합니다.

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup --resource-type Microsoft.Web/sites --resource-name examplesite --output tsv --query id)
az lock delete --ids $lockid
```

리소스 그룹에 대한 잠금을 삭제하려면 다음을 사용합니다.

```azurecli
lockid=$(az lock show --name LockSite --resource-group exampleresourcegroup  --output tsv --query id)
az lock delete --ids $lockid
```

### <a name="rest-api"></a>REST API

[관리 잠금을 위한 REST API](/rest/api/resources/managementlocks)를 사용하여 배포된 리소스를 잠글 수 있습니다. REST API를 사용하여 잠금을 만들고, 삭제하고, 기존 잠금에 대한 정보를 검색할 수 있습니다.

잠금을 만들려면 다음을 실행합니다.

```http
PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}
```

범위는 구독, 리소스 그룹 또는 리소스일 수 있습니다. lock-name은 원하는 잠금 이름입니다. api-version에는 **2016-09-01** 을 사용합니다.

요청에서 잠금에 대한 속성을 지정하는 JSON 개체를 포함합니다.

```json
{
  "properties": {
  "level": "CanNotDelete",
  "notes": "Optional text notes."
  }
}
```

## <a name="next-steps"></a>다음 단계

- 리소스를 논리적으로 구성하는 방법에 대한 자세한 내용은 [태그를 사용하여 리소스 구성](tag-resources.md)
- 사용자 지정된 정책을 사용하여 구독을 통해 제한 사항 및 규칙을 적용할 수 있습니다. 자세한 내용은 [Azure Policy란?](../../governance/policy/overview.md)을 참조하세요.
- 엔터프라이즈에서 리소스 관리자를 사용하여 구독을 효과적으로 관리할 수 있는 방법에 대한 지침은 [Azure 엔터프라이즈 스캐폴드 - 규범적 구독 거버넌스](/azure/architecture/cloud-adoption-guide/subscription-governance)를 참조하세요.
