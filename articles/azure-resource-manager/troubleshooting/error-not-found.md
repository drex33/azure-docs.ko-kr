---
title: 리소스를 찾을 수 없는 오류
description: 리소스를 찾을 수 없을 때 오류를 해결하는 방법을 설명합니다. Bicep 파일 또는 Azure Resource Manager 템플릿을 배포할 때 또는 관리 작업을 수행할 때 오류가 발생할 수 있습니다.
ms.topic: troubleshooting
ms.date: 11/30/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 893e3e30d6228ca8278f7ab46f4e2a18f94fe860
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133360940"
---
# <a name="resolve-resource-not-found-errors"></a>리소스를 찾을 수 없는 오류 해결

이 문서에서는 작업 중에 리소스를 찾을 수 없을 때 발생할 수 있는 오류에 대해 설명합니다. 일반적으로 Bicep 파일 또는 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 하 여 리소스를 배포할 때이 오류가 표시 됩니다. 또한 관리 작업을 수행할 때 오류가 표시되며, Azure Resource Manager가 필요한 리소스를 찾을 수 없습니다. 예를 들어, 존재하지 않는 리소스에 태그를 추가하려고 하면 이러한 오류가 표시됩니다.

## <a name="symptom"></a>증상

리소스를 찾을 수 없음을 나타내는 두 가지 오류 코드가 있습니다. `NotFound`오류는 다음과 유사한 결과를 반환 합니다.

```Output
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

`ResourceNotFound`오류는 다음과 유사한 결과를 반환 합니다.

```Output
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>원인

리소스 관리자 리소스의 속성을 검색 해야 하지만 구독에서 리소스를 찾을 수 없습니다.

## <a name="solution-1---check-resource-properties"></a>솔루션 1 - 리소스 속성 확인

관리 작업을 수행 하는 동안이 오류가 표시 되 면 리소스에 대해 제공한 값을 확인 합니다. 확인할 세 가지 값은 다음과 같습니다.

- 리소스 이름
- 리소스 그룹 이름
- Subscription

PowerShell 또는 Azure CLI를 사용 하는 경우 리소스를 포함 하는 구독에서 명령이 실행 되 고 있는지 확인 합니다. [Set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) 또는 [az account set](/cli/azure/account#az_account_set)를 사용하여 구독을 변경할 수 있습니다. 많은 명령은 현재 컨텍스트가 아닌 다른 구독을 지정할 수 있는 구독 매개 변수를 제공 합니다.

속성을 확인할 수 없는 경우 [Microsoft Azure 포털](https://portal.azure.com)에 로그인 합니다. 사용하려는 리소스를 찾아서 리소스 이름, 리소스 그룹 및 구독을 검사합니다.

## <a name="solution-2---set-dependencies"></a>솔루션 1 - 종속성 설정

템플릿을 배포할 때 이러한 오류가 발생하는 경우, 종속성을 추가해야 할 수 있습니다. 리소스 관리자 가능한 경우 병렬로 리소스를 만들어 배포를 최적화 합니다.

예를 들어 웹 앱을 배포할 때 App Service 계획이 있어야 합니다. 웹앱이 App Service 계획에 종속된다고 지정하지 않으면 Resource Manager에서 두 리소스를 모두 만듭니다. App Service 계획 리소스는 아직 존재 하지 않기 때문에 해당 리소스를 찾을 수 없다는 오류가 발생 하 여 웹 앱이 실패 합니다. 웹 앱에서 종속성을 설정 하 여이 오류를 방지할 수 있습니다.

# <a name="bicep"></a>[Bicep](#tab/bicep)

[ResourceId](../bicep/bicep-functions-resource.md#resourceid) 함수가 아닌 [암시적 종속성](../bicep/resource-declaration.md#implicit-dependency) 을 사용 합니다. 종속성은 리소스의 [기호화 된 이름](../bicep/file.md#bicep-format) 및 ID 속성을 사용 하 여 만들어집니다.

예를 들어, 웹 앱의 `serverFarmId` 속성은 `servicePlan.id` 를 사용 하 여 App Service 계획에 대 한 종속성을 만듭니다.

```bicep
resource webApp 'Microsoft.Web/sites@2021-02-01' = {
  properties: {
    serverFarmId: servicePlan.id
  }
}

resource servicePlan 'Microsoft.Web/serverfarms@2021-02-01' = {
  name: hostingPlanName
  ...
```

대부분의 배포에서는를 사용 하 여 `dependsOn` [명시적 종속성](../bicep/resource-declaration.md#explicit-dependency)을 만들 필요가 없습니다.

필요 하지 않은 종속성을 설정 하지 마십시오. 리소스가 병렬로 배포 되지 않기 때문에 불필요 한 종속성으로 인해 배포 기간이 연장 됩니다. 또한 배포를 차단 하는 순환 종속성을 만들 수 있습니다.

# <a name="json"></a>[JSON](#tab/json)

한 리소스가 다른 리소스 뒤에 배포되어야 하는 경우 템플릿에서 [dependsOn](../templates/resource-dependency.md#dependson) 요소를 사용합니다.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2021-02-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

필요 하지 않은 종속성을 설정 하지 마십시오. 리소스가 병렬로 배포 되지 않기 때문에 불필요 한 종속성으로 인해 배포 기간이 연장 됩니다. 또한 배포를 차단 하는 순환 종속성을 만들 수 있습니다.

[참조](../templates/template-functions-resource.md#reference) 및 [목록 *](../templates/template-functions-resource.md#list) 함수는 리소스가 동일한 템플릿에서 배포 되 고 이름 (리소스 ID 아님)에서 참조 되는 경우 참조 된 리소스에 대 한 암시적 종속성을 만듭니다. 따라서 속성에 지정 된 종속성 보다 종속성이 많을 수 있습니다 `dependsOn` .

[resourceId](../templates/template-functions-resource.md#resourceid) 함수는 암시적 종속성을 만들지 않거나 리소스가 존재하는지를 확인합니다. [reference](../templates/template-functions-resource.md#reference) 함수 및 [list*](../templates/template-functions-resource.md#list) 함수는 리소스가 해당 리소스 ID로 참조되는 경우 암시적 종속성을 만들지 않습니다. 암시적 종속성을 만들려면 동일한 템플릿에 배포 된 리소스의 이름을 전달 합니다.

---

### <a name="deployment-order"></a>배포 순서

종속성 문제가 발생하는 경우 리소스 배포 순서를 간파할 필요가 있습니다. 포털을 사용 하 여 배포 작업의 순서를 볼 수 있습니다.

1. [포털](https://portal.azure.com)에 로그인합니다.
1. 리소스 그룹의 **개요** 에서 배포 기록에 대 한 링크를 선택 합니다.

    :::image type="content" source="media/error-not-found/select-deployment.png" alt-text="리소스 그룹의 배포 기록에 대 한 링크를 강조 표시 하는 스크린샷":::

1. 검토 하려는 **배포 이름** 에 대해 **관련 이벤트** 를 선택 합니다.

    :::image type="content" source="media/error-not-found/select-deployment-events.png" alt-text="배포의 관련 이벤트에 대 한 링크를 강조 표시 하는 스크린샷":::

1. 각 리소스에 대한 이벤트의 시퀀스를 검사합니다. 각 작업의 상태와 타임 스탬프에 주의 합니다. 예를 들어 다음 이미지는 병렬로 배포된 3개의 스토리지 계정을 보여 줍니다. 3개의 스토리지 계정이 동시에 시작되었다는 것을 볼 수 있습니다.

    :::image type="content" source="media/error-not-found/deployment-events-parallel.png" alt-text="병렬로 배포 된 리소스에 대 한 활동 로그 스크린샷":::

   다음 이미지는 동시에 배포되지 않은 3개의 스토리지 계정을 보여줍니다. 두 번째 스토리지 계정은 첫 번째 스토리지 계정에 종속되고 세 번째 스토리지 계정은 두 번째 스토리지 계정에 종속됩니다. 첫 번째 저장소 계정에는 다음이 시작 되기 전에 **시작** 됨, **수락** 됨 및 **성공** 이 표시 됩니다.

    :::image type="content" source="media/error-not-found/deployment-events-sequence.png" alt-text="순차 순서로 배포 된 리소스에 대 한 활동 로그 스크린샷":::

## <a name="solution-3---get-external-resource"></a>솔루션 3 - 외부 리소스 가져오기

# <a name="bicep"></a>[Bicep](#tab/bicep)

Bicep는 심볼 이름을 사용 하 여 다른 리소스에 대 한 [암시적 종속성](../bicep/resource-declaration.md#implicit-dependency) 을 만듭니다. [기존](../bicep/resource-declaration.md#existing-resources) 키워드는 배포 된 리소스를 참조 합니다. 기존 리소스가 배포 하려는 리소스와 다른 리소스 그룹에 있는 경우 [범위](../bicep/bicep-functions-scope.md#resource-group-example) 를 포함 하 고 [resourceGroup](../bicep/bicep-functions-scope.md#resourcegroup) 함수를 사용 합니다.

이 예제에서는 다른 리소스 그룹의 기존 App Service 계획을 사용 하는 웹 앱을 배포 합니다.

```bicep
resource servicePlan 'Microsoft.Web/serverfarms@2021-02-01' existing = {
  name: hostingPlanName
  scope: resourceGroup(rgname)
}

resource webApp 'Microsoft.Web/sites@2021-02-01' = {
  name: siteName
  properties: {
    serverFarmId: servicePlan.id
  }
}
```

# <a name="json"></a>[JSON](#tab/json)

템플릿을 배포하고 다른 구독 또는 리소스 그룹에 존재하는 리소스를 가져와야 하는 경우, [resourceId 함수](../templates/template-functions-resource.md#resourceid)를 사용합니다. 이 함수는 리소스의 정규화 된 이름을 반환 합니다.

함수의 구독과 리소스 그룹 매개 변수는 `resourceId` 선택 사항입니다. 이것을 제공하지 않으면 현재 구독 및 리소스 그룹으로 기본값이 지정됩니다. 다른 리소스 그룹 또는 구독에서 리소스를 사용하는 경우, 해당 값을 제공해야 합니다.

다음 예제에서는 다른 리소스 그룹에 있는 리소스에 대한 리소스 ID를 가져옵니다.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

---

## <a name="solution-4---get-managed-identity-from-resource"></a>솔루션 4 - 리소스에서 관리 ID 가져오기

# <a name="bicep"></a>[Bicep](#tab/bicep)

[관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 리소스를 배포 하는 경우 관리 되는 id의 값을 검색 하기 전에 해당 리소스가 배포 될 때까지 기다려야 합니다. Id가 적용 되는 리소스에 대해 [암시적 종속성](../bicep/resource-declaration.md#implicit-dependency) 을 사용 합니다. 이 방법을 사용 하면 리소스 관리자 종속성을 사용 하기 전에 리소스와 관리 id가 배포 됩니다.

가상 컴퓨터에 적용 되는 관리 id의 보안 주체 ID 및 테 넌 트 ID를 가져올 수 있습니다. 예를 들어 가상 머신 리소스에의 기호화 된 이름이 있는 경우 `vm` 다음 구문을 사용 합니다.

```bicep
vm.identity.principalId

vm.identity.tenantId
```

# <a name="json"></a>[JSON](#tab/json)

[관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 리소스를 배포 하는 경우 관리 되는 id의 값을 검색 하기 전에 해당 리소스가 배포 될 때까지 기다려야 합니다. 관리 ID 이름을 [참조](../templates/template-functions-resource.md#reference) 함수에 전달하는 경우, 리소스와 ID를 배포하기 전에 Resource Manager에서 참조를 확인하려고 시도합니다. 대신 ID가 적용되는 리소스의 이름을 전달합니다. 이 방법을 사용하면 Resource Manager에서 참조 함수를 확인하기 전에 리소스와 관리 ID가 배포됩니다.

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
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')), '2021-07-01', 'Full').identity.principalId]",
```

또는, 가상 머신 확장 집합에 적용되는 관리 ID에 대한 테넌트 ID를 가져오려면 다음을 사용합니다.

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), '2021-07-01', 'Full').Identity.tenantId]"
```

---

## <a name="solution-5---check-functions"></a>솔루션 5 - 함수 검사

# <a name="bicep"></a>[Bicep](#tab/bicep)

리소스의 기호화 된 이름을 사용 하 여 리소스에서 값을 가져올 수 있습니다. 심볼 이름을 사용 하 여 동일한 리소스 그룹 또는 다른 리소스 그룹의 저장소 계정을 참조할 수 있습니다. 배포 된 리소스에서 값을 가져오려면 [기존](../bicep/resource-declaration.md#existing-resources) 키워드를 사용 합니다. 리소스가 다른 리소스 그룹에 있는 경우 `scope` [resourceGroup](../bicep/bicep-functions-scope.md#resourcegroup) 함수와 함께를 사용 합니다. 대부분의 경우 [reference](../bicep/bicep-functions-resource.md#reference) 함수는 필요 하지 않습니다.

다음 예제에서는 다른 리소스 그룹에서 기존 스토리지 계정을 참조합니다.

```bicep
resource stgAcct 'Microsoft.Storage/storageAccounts@2021-06-01' existing = {
  name: stgname
  scope: resourceGroup(rgname)
}
```

# <a name="json"></a>[JSON](#tab/json)

템플릿을 배포할 때, [참조](../templates/template-functions-resource.md#reference) 또는 [listKeys](../templates/template-functions-resource.md#listkeys) 함수를 사용하는 식을 찾습니다. 리소스가 동일한 템플릿, 리소스 그룹 및 구독에 있는지 여부에 따라 제공하는 값이 다릅니다. 시나리오에 필요한 매개 변수 값을 제공하는지 확인합니다. 리소스가 다른 리소스 그룹에 있으면 전체 리소스 ID를 제공합니다. 예를 들어 다른 리소스 그룹의 스토리지 계정을 참조하려면 다음을 사용합니다.

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2021-06-01')]"
```

---

## <a name="solution-6---after-deleting-resource"></a>솔루션 6 - 리소스 삭제 후

리소스를 삭제 하는 경우 리소스가 포털에 표시 되지만 사용할 수 없는 짧은 시간이 있을 수 있습니다. 리소스를 선택 하면 리소스를 **찾을** 수 없다는 오류가 표시 됩니다.

:::image type="content" source="media/error-not-found/resource-not-found-portal.png" alt-text="리소스를 찾을 수 없다는 것을 보여 주는 포털에서 삭제 된 리소스의 스크린샷":::

포털을 새로 고치고 삭제 된 리소스를 사용 가능한 리소스 목록에서 제거 해야 합니다. 삭제 된 리소스가 몇 분 이상 사용할 수 있는 것으로 계속 표시 되 면 [지원 담당자에 게 문의 하세요](https://azure.microsoft.com/support/options/).
