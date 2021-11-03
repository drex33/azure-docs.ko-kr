---
title: 리소스를 찾을 수 없는 오류
description: 리소스를 찾을 수 없을 때 오류를 해결하는 방법을 설명합니다. Azure Resource Manager 템플릿을 배포하거나 관리 작업을 수행하는 경우 오류가 발생할 수 있습니다.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f48d35e41929cb9760658fa037f3ab4ef124a6c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102980"
---
# <a name="resolve-resource-not-found-errors"></a>리소스를 찾을 수 없는 오류 해결

이 문서에서는 작업 중에 리소스를 찾을 수 없을 때 발생할 수 있는 오류에 대해 설명합니다. 일반적으로 리소스를 배포할 때 이러한 오류가 표시됩니다. 또한 관리 작업을 수행할 때 오류가 표시되며, Azure Resource Manager가 필요한 리소스를 찾을 수 없습니다. 예를 들어, 존재하지 않는 리소스에 태그를 추가하려고 하면 이러한 오류가 표시됩니다.

## <a name="symptom"></a>증상

리소스를 찾을 수 없음을 나타내는 두 가지 오류 코드가 있습니다. **찾을 수 없음** 오류는 다음과 유사한 결과를 반환합니다.

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

**찾을 수 없는 리소스** 오류는 다음과 유사한 결과를 반환합니다.

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>원인

Resource Manager에서 리소스에 대한 속성을 검색해야 하지만, 구독에서 해당 리소스를 찾을 수 없습니다.

## <a name="solution-1---check-resource-properties"></a>솔루션 1 - 리소스 속성 확인

관리 작업을 수행하는 동안 이러한 오류가 표시되면, 리소스에 제공되는 값을 확인합니다. 확인할 세 가지 값은 다음과 같습니다.

* 리소스 이름
* 리소스 그룹 이름
* Subscription

PowerShell 또는 Azure CLI를 사용하는 경우, 리소스를 포함하는 구독에서 명령을 실행하고 있는지 확인합니다. [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) 또는 [az account set](/cli/azure/account#az_account_set)를 사용하여 구독을 변경할 수 있습니다. 또한 많은 명령은 현재 컨텍스트와 다른 구독을 지정할 수 있는 구독 매개 변수를 제공합니다.

속성을 확인해야 하는 데 문제가 있으면 [포털](https://portal.azure.com)에 로그인합니다. 사용하려는 리소스를 찾아서 리소스 이름, 리소스 그룹 및 구독을 검사합니다.

## <a name="solution-2---set-dependencies"></a>솔루션 1 - 종속성 설정

템플릿을 배포할 때 이러한 오류가 발생하는 경우, 종속성을 추가해야 할 수 있습니다. 리소스 관리자는 가능한 경우 리소스를 병렬로 만들어 배포를 최적화합니다. 한 리소스가 다른 리소스 뒤에 배포되어야 하는 경우 템플릿에서 **dependsOn** 요소를 사용합니다. 예를 들어 웹앱을 배포할 때는 App Service 계획이 반드시 존재해야 합니다. 웹앱이 App Service 계획에 종속된다고 지정하지 않으면 Resource Manager에서 두 리소스를 모두 만듭니다. 웹앱에 속성을 설정하려고 할 때 App Service 계획 리소스가 아직 없기 때문에 이 리소스를 찾을 수 없다는 오류 메시지가 표시됩니다. 웹앱에서 종속성을 설정하면 이러한 오류를 방지할 수 있습니다.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

하지만 필요하지 않은 종속성은 설정하지 않는 것이 좋습니다. 불필요한 종속성이 있으면 서로 종속되지 않은 리소스가 동시에 배포되는 것을 막기 때문에 배포 시간이 길어집니다. 또한 배포를 방해하는 순환 종속성을 만들 수 있습니다. [reference](../templates/template-functions-resource.md#reference) 함수 및 [list*](../templates/template-functions-resource.md#list) 함수는 리소스가 같은 템플릿에 배포되는 경우 참조되는 리소스에 대한 암시적 종속성을 만들고 해당 이름으로 참조됩니다(리소스 ID가 아닌). 따라서 **dependsOn** 속성에 지정된 종속성보다 많은 종속성을 가질 수 있습니다. [resourceId](../templates/template-functions-resource.md#resourceid) 함수는 암시적 종속성을 만들지 않거나 리소스가 존재하는지를 확인합니다. [reference](../templates/template-functions-resource.md#reference) 함수 및 [list*](../templates/template-functions-resource.md#list) 함수는 리소스가 해당 리소스 ID로 참조되는 경우 암시적 종속성을 만들지 않습니다. 암시적 종속성을 만들려면 동일한 템플릿에 배포된 리소스의 이름을 전달합니다.

종속성 문제가 발생하는 경우 리소스 배포 순서를 간파할 필요가 있습니다. 배포 작업의 순서를 보려면 다음을 수행합니다.

1. 리소스 그룹에 대한 배포 기록을 선택합니다.

   ![배포 기록 선택](./media/error-not-found/select-deployment.png)

2. 기록에서 배포를 선택하고 **이벤트** 를 선택합니다.

   ![배포 이벤트 선택](./media/error-not-found/select-deployment-events.png)

3. 각 리소스에 대한 이벤트의 시퀀스를 검사합니다. 각 작업의 상태에 주의합니다. 예를 들어 다음 이미지는 병렬로 배포된 3개의 스토리지 계정을 보여 줍니다. 3개의 스토리지 계정이 동시에 시작되었다는 것을 볼 수 있습니다.

   ![병렬 배포](./media/error-not-found/deployment-events-parallel.png)

   다음 이미지는 동시에 배포되지 않은 3개의 스토리지 계정을 보여줍니다. 두 번째 스토리지 계정은 첫 번째 스토리지 계정에 종속되고 세 번째 스토리지 계정은 두 번째 스토리지 계정에 종속됩니다. 다음 스토리지 계정이 시작되기 전에 첫 번째 스토리지 계정이 시작, 승인, 완료됩니다.

   ![순차 배포](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>솔루션 3 - 외부 리소스 가져오기

템플릿을 배포하고 다른 구독 또는 리소스 그룹에 존재하는 리소스를 가져와야 하는 경우, [resourceId 함수](../templates/template-functions-resource.md#resourceid)를 사용합니다. 이 함수는 리소스의 정규화된 이름을 가져오기 위해 반환됩니다.

ResourceId 함수의 구독 및 리소스 그룹 매개 변수는 선택 사항입니다. 이것을 제공하지 않으면 현재 구독 및 리소스 그룹으로 기본값이 지정됩니다. 다른 리소스 그룹 또는 구독에서 리소스를 사용하는 경우, 해당 값을 제공해야 합니다.

다음 예제에서는 다른 리소스 그룹에 있는 리소스에 대한 리소스 ID를 가져옵니다.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>솔루션 4 - 리소스에서 관리 ID 가져오기

[관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 암시적으로 만드는 리소스를 배포하는 경우, 관리 ID의 값을 검색하기 전에 해당 리소스가 배포될 때까지 기다려야 합니다. 관리 ID 이름을 [참조](../templates/template-functions-resource.md#reference) 함수에 전달하는 경우, 리소스와 ID를 배포하기 전에 Resource Manager에서 참조를 확인하려고 시도합니다. 대신 ID가 적용되는 리소스의 이름을 전달합니다. 이 방법을 사용하면 Resource Manager에서 참조 함수를 확인하기 전에 리소스와 관리 ID가 배포됩니다.

참조 함수에서 `Full`을 사용하여 관리 ID를 비롯한 모든 속성을 가져옵니다.

패턴은 다음과 같습니다.

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> 패턴을 사용하지 않습니다.
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> 템플릿이 실패합니다.

예를 들어, 가상 머신에 적용되는 관리 ID에 대한 보안 주체 ID를 가져오려면 다음을 사용합니다.

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

또는, 가상 머신 확장 집합에 적용되는 관리 ID에 대한 테넌트 ID를 가져오려면 다음을 사용합니다.

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>솔루션 5 - 함수 검사

템플릿을 배포할 때, [참조](../templates/template-functions-resource.md#reference) 또는 [listKeys](../templates/template-functions-resource.md#listkeys) 함수를 사용하는 식을 찾습니다. 리소스가 동일한 템플릿, 리소스 그룹 및 구독에 있는지 여부에 따라 제공하는 값이 다릅니다. 시나리오에 필요한 매개 변수 값을 제공하는지 확인합니다. 리소스가 다른 리소스 그룹에 있으면 전체 리소스 ID를 제공합니다. 예를 들어 다른 리소스 그룹의 스토리지 계정을 참조하려면 다음을 사용합니다.

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>솔루션 6 - 리소스 삭제 후

리소스를 삭제하는 경우, 리소스가 포털에 표시는 되지만 실제로는 사용할 수 없는 짧은 시간이 있을 수 있습니다. 리소스를 선택하면 리소스를 찾을 수 없다는 오류 메시지가 표시됩니다. 최신 보기를 가져오려면 포털을 새로 고칩니다.

잠시 후에도 문제가 계속되면 [고객 지원팀에 문의하세요](https://azure.microsoft.com/support/options/).
