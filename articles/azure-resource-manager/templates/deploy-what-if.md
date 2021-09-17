---
title: 템플릿 배포 가상
description: Azure Resource Manager 템플릿을 배포하기 전에 리소스 변경 내용을 확인합니다.
author: tfitzmac
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 73ff2c609438ead1e1fa42468e97be801a76e7e2
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450290"
---
# <a name="arm-template-deployment-what-if-operation"></a>ARM 템플릿 배포 가상 작업

Azure Resource Manager 템플릿(ARM 템플릿)을 배포하기 전에 변경 내용을 미리 볼 수 있습니다. Azure Resource Manager는 템플릿을 배포하는 경우 리소스가 어떻게 변경되는지 확인할 수 있도록 하는 가상 작업을 제공합니다. 가상 작업은 기존 리소스를 변경하지 않습니다. 대신, 지정된 템플릿이 배포되는 경우 변경 내용을 예측합니다.

Azure PowerShell, Azure CLI 또는 REST API 작업을 사용하여 가상 작업을 사용할 수 있습니다. 가상은 리소스 그룹, 구독, 관리 그룹, 테넌트 수준 배포에 지원됩니다.

## <a name="install-azure-powershell-module"></a>Azure PowerShell 모듈 설치

PowerShell에서 ‘가상’을 사용하려면 **Az 모듈 4.2 이상 버전** 을 사용해야 합니다.

모듈 설치 시 다음을 사용합니다.

```powershell
Install-Module -Name Az -Force
```

모듈을 설치하는 방법에 관한 자세한 내용은 [Azure PowerShell 설치](/powershell/azure/install-az-ps)를 참조하세요.

## <a name="install-azure-cli-module"></a>Azure CLI 모듈 설치

Azure CLI에서 가상을 사용하려면 Azure CLI 2.14.0 이상이 있어야 합니다. 필요한 경우 [최신 버전의 Azure CLI를 설치](/cli/azure/install-azure-cli)합니다.

## <a name="see-results"></a>결과 보기

PowerShell 또는 Azure CLI에서 가상을 사용하는 경우 다양한 형식의 변경 내용을 볼 수 있도록 하는 색으로 구분된 결과가 출력에 포함됩니다.

![Resource Manager 템플릿 배포 가상 작업 fullresourcepayload 및 변경 형식](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

텍스트 출력은 다음과 같습니다.

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

> [!NOTE]
> 가상 작업은 [reference 함수](template-functions-resource.md#reference)를 확인할 수 없습니다. 속성을 reference 함수를 포함하는 템플릿 식으로 설정할 때마다 가상은 속성 변경을 보고합니다. 해당 동작은 가상이 속성(예: 부울 값에 관한 `true` 또는 `false`)의 현재 값을 확인되지 않은 템플릿 식과 비교하기 때문에 발생합니다. 분명히 이러한 값은 일치하지 않습니다. 템플릿을 배포할 때 속성은 템플릿 식이 다른 값으로 확인되는 경우에만 변경됩니다.

## <a name="what-if-commands"></a>가상 명령

### <a name="azure-powershell"></a>Azure PowerShell

템플릿을 배포하기 전에 변경 내용을 미리 보려면 [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 또는 [New-AzSubscriptionDeployment](/powershell/module/az.resources/new-azdeployment)를 사용합니다. `-Whatif` 스위치 매개 변수를 배포 명령에 추가합니다.

* 리소스 그룹 배포의 경우 `New-AzResourceGroupDeployment -Whatif`
* 구독 수준 배포의 경우 `New-AzSubscriptionDeployment -Whatif` 및 `New-AzDeployment -Whatif`

`-Confirm` 스위치 매개 변수를 사용하여 변경 내용을 미리 보고 배포를 계속하라는 프롬프트를 받을 수 있습니다.

* 리소스 그룹 배포의 경우 `New-AzResourceGroupDeployment -Confirm`
* 구독 수준 배포의 경우 `New-AzSubscriptionDeployment -Confirm` 및 `New-AzDeployment -Confirm`

이전 명령은 수동으로 검사할 수 있는 텍스트 요약을 반환합니다. 프로그래밍 방식으로 변경 내용을 검사할 수 있는 개체를 가져오려면 [Get-AzResourceGroupDeploymentWhatIfResult](/powershell/module/az.resources/get-azresourcegroupdeploymentwhatifresult) 또는 [Get-AzSubscriptionDeploymentWhatIfResult](/powershell/module/az.resources/get-azdeploymentwhatifresult)를 사용합니다.

* 리소스 그룹 배포의 경우 `$results = Get-AzResourceGroupDeploymentWhatIfResult`
* 구독 수준 배포의 경우 `$results = Get-AzSubscriptionDeploymentWhatIfResult` 또는 `$results = Get-AzDeploymentWhatIfResult`

### <a name="azure-cli"></a>Azure CLI

템플릿을 배포하기 전에 변경 내용을 미리 보려면 다음을 사용합니다.

* 리소스 그룹 배포의 경우 [az deployment group what-if](/cli/azure/deployment/group#az_deployment_group_what_if)
* 구독 수준 배포의 경우 [az deployment sub what-if](/cli/azure/deployment/sub#az_deployment_sub_what_if)
* 관리 그룹 배포의 경우 [az deployment mg what-if](/cli/azure/deployment/mg#az_deployment_mg_what_if)
* 테넌트 배포의 경우 [az deployment tenant what-if](/cli/azure/deployment/tenant#az_deployment_tenant_what_if)

`--confirm-with-what-if`스위치(또는 약식인 `-c` )를 사용하여 변경 내용을 미리 보고 배포를 계속하라는 프롬프트를 받을 수 있습니다. 다음에 스위치를 추가합니다.

* [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)
* [az deployment sub create](/cli/azure/deployment/sub#az_deployment_sub_create).
* [az deployment mg create](/cli/azure/deployment/mg#az_deployment_mg_create)
* [az deployment tenant create](/cli/azure/deployment/tenant#az_deployment_tenant_create)

예를 들어 리소스 그룹 배포의 경우 `az deployment group create --confirm-with-what-if` 또는 `-c`를 사용합니다.

이전 명령은 수동으로 검사할 수 있는 텍스트 요약을 반환합니다. 프로그래밍 방식으로 변경 내용을 검사할 수 있는 JSON 개체를 가져오려면 `--no-pretty-print` 스위치를 사용합니다. 예를 들어 리소스 그룹 배포에는 `az deployment group what-if --no-pretty-print`를 사용합니다.

색 없이 결과를 반환하려는 경우 [Azure CLI 구성](/cli/azure/azure-cli-configuration) 파일을 엽니다. **no_color** 를 **예** 로 설정합니다.

### <a name="azure-rest-api"></a>Azure REST API

REST API의 경우 다음을 사용합니다.

* 리소스 그룹 배포의 경우 [Deployments - What If](/rest/api/resources/deployments/whatif)
* 구독 배포의 경우 [Deployments - What If At Subscription Scope](/rest/api/resources/deployments/whatifatsubscriptionscope)
* 관리 그룹 배포의 경우 [Deployments - What If At Management Group Scope](/rest/api/resources/deployments/whatifatmanagementgroupscope)
* 테넌트 배포의 경우 [Deployments - What If At Tenant Scope](/rest/api/resources/deployments/whatifattenantscope).

## <a name="change-types"></a>변경 형식

가상 작업은 다음과 같은 6가지 형식의 변경 내용을 나열합니다.

- **만들기**: 현재는 리소스가 없지만 템플릿에 정의되어 있습니다. 리소스가 생성됩니다.

- **삭제**: 해당 변경 형식은 배포에 [전체 모드](deployment-modes.md)를 사용하는 경우에만 적용됩니다. 리소스가 있지만 템플릿에 정의되어 있지 않습니다. ‘전체 모드’를 사용하면 리소스가 삭제됩니다. [전체 모드 삭제를 지원](./deployment-complete-mode-deletion.md)하는 리소스만 해당 변경 형식에 포함됩니다.

- **무시**: 리소스가 있지만 템플릿에 정의되어 있지 않습니다. 리소스가 배포 또는 수정되지 않습니다.

- **NoChange**: 리소스가 있고 템플릿에 정의되어 있습니다. 리소스가 다시 배포되지만 리소스의 속성이 변경되지 않습니다. 이 변경 형식은 [ResultFormat](#result-format)이 `FullResourcePayloads`(기본값)로 설정된 경우 반환됩니다.

- **수정**: 리소스가 있고 템플릿에 정의되어 있습니다. 리소스가 다시 배포되고 리소스의 속성이 변경됩니다. 이 변경 형식은 [ResultFormat](#result-format)이 `FullResourcePayloads`(기본값)로 설정된 경우 반환됩니다.

- **배포**: 리소스가 있고 템플릿에 정의되어 있습니다. 리소스가 다시 배포됩니다. 리소스의 속성은 변경되거나 변경되지 않을 수 있습니다. 해당 작업은 속성이 변경될지 여부를 확인할 충분한 정보가 없는 경우 이 변경 형식을 반환합니다. 해당 조건은 [ResultFormat](#result-format)이 `ResourceIdOnly`로 설정된 경우 표시됩니다.

## <a name="result-format"></a>결과 형식

예측된 변경 내용에 관해 반환되는 세부 정보의 수준을 제어합니다. 다음 두 가지 옵션을 사용할 수 있습니다.

* **FullResourcePayloads** -변경되는 리소스 목록 및 변경되는 속성에 관한 세부 정보를 반환합니다.
* **ResourceIDOnly** -변경되는 리소스의 목록을 반환합니다.

기본값은 **FullResourcePayloads** 입니다.

PowerShell 배포 명령의 경우 `-WhatIfResultFormat` 매개 변수를 사용합니다. 프로그래매틱 개체 명령에서 `ResultFormat` 매개 변수를 사용합니다.

Azure CLI의 경우 `--result-format` 매개 변수를 사용합니다.

다음 결과는 두 가지 출력 형식을 보여 줍니다.

- 전체 리소스 페이로드

  ```powershell
  Resource and property changes are indicated with these symbols:
    - Delete
    + Create
    ~ Modify

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
      - tags.Owner: "Team A"
      ~ properties.addressSpace.addressPrefixes: [
        - 0: "10.0.0.0/16"
        + 0: "10.0.0.0/15"
        ]
      ~ properties.subnets: [
        - 0:

          name:                     "subnet001"
          properties.addressPrefix: "10.0.0.0/24"

        ]

  Resource changes: 1 to modify.
  ```

- 리소스 ID만 해당

  ```powershell
  Resource and property changes are indicated with this symbol:
    ! Deploy

  The deployment will update the following scope:

  Scope: /subscriptions/./resourceGroups/ExampleGroup

    ! Microsoft.Network/virtualNetworks/vnet-001

  Resource changes: 1 to deploy.
  ```

## <a name="run-what-if-operation"></a>가상 작업 실행

### <a name="set-up-environment"></a>환경 설정

가상이 작동하는 방식을 확인하기 위해 일부 테스트를 실행해 보겠습니다. 먼저 [가상 네트워크를 만드는 템플릿을](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-before.json) 배포합니다. 이 가상 네트워크를 사용하여 변경 내용이 어떻게 가상으로 보고되는지 테스트할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name ExampleGroup `
  -Location centralus
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name ExampleGroup \
  --location "Central US"
az deployment group create \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-before.json"
```

---

### <a name="test-modification"></a>테스트 수정

배포가 완료되면 가상 작업을 테스트할 준비가 된 것입니다. 이번에는 [가상 네트워크를 변경하는 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/what-if-after.json)을 배포합니다. 원래 태그 중 하나가 누락되고, 서브넷이 제거되고, 주소 접두사가 변경되었습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Whatif `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group what-if \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

---

‘가상’ 출력은 다음과 같습니다.

![Resource Manager 템플릿 배포 가상 작업 출력](./media/template-deploy-what-if/resource-manager-deployment-whatif-change-types.png)

텍스트 출력은 다음과 같습니다.

```powershell
Resource and property changes are indicated with these symbols:
  - Delete
  + Create
  ~ Modify

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  ~ Microsoft.Network/virtualNetworks/vnet-001 [2018-10-01]
    - tags.Owner: "Team A"
    ~ properties.addressSpace.addressPrefixes: [
      - 0: "10.0.0.0/16"
      + 0: "10.0.0.0/15"
      ]
    ~ properties.subnets: [
      - 0:

        name:                     "subnet001"
        properties.addressPrefix: "10.0.0.0/24"

      ]

Resource changes: 1 to modify.
```

출력의 위쪽에서 색이 변경 형식을 나타내는 것으로 정의됩니다.

출력 아래쪽에는 태그 소유자가 삭제되었음을 표시합니다. 주소 접두사가 10.0.0.0/16에서 10.0.0.0/15로 변경되었습니다. subnet001이라는 서브넷이 삭제되었습니다. 이러한 변경 내용은 배포되지 않았습니다. 템플릿을 배포하는 경우 변경 내용의 미리 보기가 표시됩니다.

삭제된 것으로 표시되는 일부 속성은 실제로 변경되지 않습니다. 속성은 템플릿에 없을 때 삭제된 것으로 잘못 보고될 수 있지만, 배포 중에 기본값으로 자동 설정됩니다. 이 결과는 가상 응답에서 "노이즈"로 간주됩니다. 최종 배포된 리소스는 속성에 설정된 값을 갖습니다. 가상 작업이 완성됨에 따라 해당 속성은 결과에서 필터링됩니다.

## <a name="programmatically-evaluate-what-if-results"></a>가상 결과를 프로그래밍 방식으로 평가

이제 명령을 변수에 설정하여 가상 결과를 프로그래밍 방식으로 평가해 보겠습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$results = Get-AzResourceGroupDeploymentWhatIfResult `
  -ResourceGroupName ExampleGroup `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json"
```

각 변경 내용에 관한 요약을 볼 수 있습니다.

```azurepowershell
foreach ($change in $results.Changes)
{
  $change.Delta
}
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
results=$(az deployment group what-if --resource-group ExampleGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/what-if-after.json" --no-pretty-print)
```

---

## <a name="confirm-deletion"></a>삭제 확인

[배포 모드](deployment-modes.md)를 사용하는 가상 작업 지원. 전체 모드로 설정된 경우 템플릿에 없는 리소스는 삭제됩니다. 다음 예제에서는 전체 모드에서 [정의된 리소스가 없는 템플릿](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/what-if/azuredeploy.json)을 배포합니다.

템플릿을 배포하기 전에 변경 내용을 미리 보려면 배포 명령에서 confirm switch 매개 변수를 사용합니다. 예상대로 변경되면 배포를 완료하도록 응답합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -ResourceGroupName ExampleGroup `
  -Mode Complete `
  -Confirm `
  -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/azuredeploy.json"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group ExampleGroup \
  --mode Complete \
  --confirm-with-what-if \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/what-if/azuredeploy.json"
```

---

템플릿에 정의된 리소스가 없고 배포 모드가 완료로 설정되어 있기 때문에 가상 네트워크가 삭제됩니다.

![Resource Manager 템플릿 배포 가상 작업 출력 배포 모드 완료](./media/template-deploy-what-if/resource-manager-deployment-whatif-output-mode-complete.png)

텍스트 출력은 다음과 같습니다.

```powershell
Resource and property changes are indicated with this symbol:
  - Delete

The deployment will update the following scope:

Scope: /subscriptions/./resourceGroups/ExampleGroup

  - Microsoft.Network/virtualNetworks/vnet-001

      id:
"/subscriptions/./resourceGroups/ExampleGroup/providers/Microsoft.Network/virtualNet
works/vnet-001&quot;
      location:        &quot;centralus&quot;
      name:            &quot;vnet-001&quot;
      tags.CostCenter: &quot;12345&quot;
      tags.Owner:      &quot;Team A&quot;
      type:            &quot;Microsoft.Network/virtualNetworks&quot;

Resource changes: 1 to delete.

Are you sure you want to execute the deployment?
[Y] Yes  [A] Yes to All  [N] No  [L] No to All  [S] Suspend  [?] Help (default is &quot;Y"):
```

예상되는 변경 내용을 확인하고 배포를 실행하려는 것을 확인할 수 있습니다.

## <a name="sdks"></a>SDK

Azure SDK를 통해 가상 작업을 사용할 수 있습니다.

* Python의 경우 [가상](/python/api/azure-mgmt-resource/azure.mgmt.resource.resources.v2019_10_01.operations.deploymentsoperations#what-if-resource-group-name--deployment-name--properties--location-none--custom-headers-none--raw-false--polling-true----operation-config-)를 사용합니다.

* Java의 경우 [DeploymentWhatIf Class](/java/api/com.microsoft.azure.management.resources.deploymentwhatif)를 사용합니다.

* .NET의 경우 [DeploymentWhatIf Class](/dotnet/api/microsoft.azure.management.resourcemanager.models.deploymentwhatif)를 사용합니다.

## <a name="next-steps"></a>다음 단계

- 파이프라인에서 가상 작업을 사용하려면 [파이프라인에서 가상을 사용하여 ARM 템플릿 테스트](https://4bes.nl/2021/03/06/test-arm-templates-with-what-if/)를 참조하세요.
- 가상 작업에서 잘못된 결과가 표시되는 경우 [https://aka.ms/whatifissues](https://aka.ms/whatifissues)에서 문제를 보고하세요.
- 가상의 사용에 관한 Microsoft Learn 모듈은 [변경 내용 미리 보기 및 가상 및 ARM 템플릿 테스트 도구 키트를 사용하여 Azure 리소스 유효성 검사](/learn/modules/arm-template-test/)를 참조하세요.
