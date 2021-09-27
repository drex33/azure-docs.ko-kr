---
title: 논리적 조직의 리소스, 리소스 그룹 및 구독에 태그 지정
description: 태그를 적용하여 대금 청구 및 관리를 위해 Azure 리소스를 구성하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 07/29/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: cf8b4ceb70eec2ac6dbb79b8193276997f8e06f1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664526"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>태그를 사용하여 Azure 리소스 및 관리 계층 구조 구성

Azure 리소스, 리소스 그룹, 구독에 태그를 적용하여 논리적인 분류법으로 구성합니다. 각 태그는 이름과 값 쌍으로 이루어져 있습니다. 예를 들어 프로덕션의 모든 리소스에 _환경_ 이름과 _프로덕션_ 값을 적용할 수 있습니다.

태그 지정 전략을 구현하는 방법에 관한 권장 사항은 [리소스 명명 및 태그 지정 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)를 참조하세요.

> [!IMPORTANT]
> 태그 이름은 작업에서 대/소문자를 구분하지 않습니다. 대/소문자에 관계없이 태그 이름을 가진 태그가 업데이트되거나 검색됩니다. 그러나 리소스 공급자는 태그 이름에 지정된 대/소문자를 유지할 수 있습니다. 비용 보고서에서 해당 대/소문자를 확인할 수 있습니다.
>
> 태그 값은 대/소문자를 구분합니다.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>필요한 액세스

태그 리소스에 필요한 액세스 권한을 얻는 방법에는 두 가지가 있습니다.

- `Microsoft.Resources/tags` 리소스 유형에 대한 쓰기 액세스 권한을 가질 수 있습니다. 이 액세스 권한이 있으면 리소스 자체에 대한 액세스 권한이 없는 경우에도 리소스에 태그를 지정할 수 있습니다. [태그 기여자](../../role-based-access-control/built-in-roles.md#tag-contributor) 역할은 이 액세스 권한을 부여합니다. 현재 태그 기여자 역할은 포털을 통해 리소스 또는 리소스 그룹에 태그를 적용할 수 없습니다. 포털을 통해 구독에 태그를 적용할 수 있습니다. PowerShell 및 REST API를 통한 모든 태그 작업을 지원합니다.

- 리소스 자체에 대한 쓰기 액세스 권한을 가질 수 있습니다. [기여자](../../role-based-access-control/built-in-roles.md#contributor) 역할은 모든 엔터티에 태그를 적용하는 데 필요한 액세스 권한을 부여합니다. 하나의 리소스 형식에만 태그를 적용하려면 해당 리소스에 대한 기여자 역할을 사용합니다. 예를 들어, 가상 머신에 태그를 적용하려면 [가상 머신 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)를 사용합니다.

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>태그 적용

Azure PowerShell은 태그를 적용하기 위한 두 가지 명령([New-AzTag](/powershell/module/az.resources/new-aztag) 및 [Update-AzTag](/powershell/module/az.resources/update-aztag))을 제공합니다. `Az.Resources` 모듈 1.12.0 이상이 설치되어 있어야 합니다. `Get-InstalledModule -Name Az.Resources`를 사용하여 버전을 확인할 수 있습니다. 해당 모듈을 설치하거나 [Azure PowerShell 3.6.1 이상을 설치](/powershell/azure/install-az-ps)할 수 있습니다.

`New-AzTag`는 리소스, 리소스 그룹 또는 구독에 대한 모든 태그를 대체합니다. 이 명령을 호출할 때는 태그를 지정할 엔터티의 리소스 ID를 전달합니다.

다음 예제에서는 스토리지 계정에 태그 집합을 적용합니다.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

명령이 완료되면 리소스에 두 개의 태그가 지정됩니다.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

명령을 다시 실행하되, 이번에는 다른 태그를 사용할 경우 이전 태그가 제거됩니다.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

이미 태그가 있는 리소스에 태그를 추가하려면 `Update-AzTag`를 사용합니다. `-Operation` 매개 변수를 `Merge`로 설정합니다.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

두 개의 새 태그가 기존의 두 태그에 추가되었습니다.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

각 태그 이름에는 값이 하나만 있을 수 있습니다. 태그에 새 값을 제공하면 병합 작업을 사용하는 경우에도 이전 값이 바뀝니다. 다음 예제에서는 `Status` 태그를 _Normal_ 에서 _Green_ 으로 변경합니다.

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

`-Operation` 매개 변수를 `Replace`로 설정하면 기존 태그가 새 태그 집합으로 바뀝니다.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

새 태그만 리소스에 남아 있습니다.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

리소스 그룹 또는 구독에도 동일한 명령을 사용할 수 있습니다. 태그를 지정할 리소스 그룹 또는 구독에 대한 식별자를 전달합니다.

리소스 그룹에 새 태그 집합을 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

리소스 그룹의 태그를 업데이트하려면 다음을 사용합니다.

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

구독에 새 태그 집합을 추가하려면 다음을 사용합니다.

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

구독의 태그를 업데이트하려면 다음을 사용합니다.

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

리소스 그룹에 동일한 이름의 리소스가 둘 이상 있을 수 있습니다. 이 경우 다음 명령을 사용하여 각 리소스를 설정할 수 있습니다.

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>태그 나열

리소스, 리소스 그룹 또는 구독의 태그를 가져오려면 [Get-AzTag](/powershell/module/az.resources/get-aztag) 명령을 사용하여 엔터티의 리소스 ID를 전달합니다.

리소스의 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

리소스 그룹의 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

구독의 태그를 보려면 다음을 사용합니다.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>태그로 나열

특정 태그 이름 및 값이 있는 리소스를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

태그 값에 관계없이 특정 태그 이름이 있는 리소스를 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

특정 태그 이름 및 값이 있는 리소스 그룹을 가져오려면 다음을 사용합니다.

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>태그 제거

특정 태그를 제거하려면 `Update-AzTag`를 사용하고 `-Operation`을 `Delete`로 설정합니다. 삭제하려는 태그를 전달합니다.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

지정된 태그가 제거됩니다.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

모든 태그를 제거하려면 [Remove-AzTag](/powershell/module/az.resources/remove-aztag) 명령을 사용합니다.

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>태그 적용

Azure CLI는 태그 적용을 위한 두 가지 명령([az tag create](/cli/azure/tag#az_tag_create) 및 [az tag update](/cli/azure/tag#az_tag_update))을 제공합니다. Azure CLI 2.10.0 이상이 있어야 합니다. `az version`을 사용하여 버전을 확인할 수 있습니다. 업데이트하거나 설치하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

`az tag create`는 리소스, 리소스 그룹 또는 구독에 대한 모든 태그를 대체합니다. 이 명령을 호출할 때는 태그를 지정할 엔터티의 리소스 ID를 전달합니다.

다음 예제에서는 스토리지 계정에 태그 집합을 적용합니다.

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag create --resource-id $resource --tags Dept=Finance Status=Normal
```

명령이 완료되면 리소스에 두 개의 태그가 지정됩니다.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Status": "Normal"
  }
},
```

명령을 다시 실행하되, 이번에는 다른 태그를 사용할 경우 이전 태그가 제거됩니다.

```azurecli-interactive
az tag create --resource-id $resource --tags Team=Compliance Environment=Production
```

```output
"properties": {
  "tags": {
    "Environment": "Production",
    "Team": "Compliance"
  }
},
```

이미 태그가 있는 리소스에 태그를 추가하려면 `az tag update`를 사용합니다. `--operation` 매개 변수를 `Merge`로 설정합니다.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Dept=Finance Status=Normal
```

두 개의 새 태그가 기존의 두 태그에 추가되었습니다.

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Normal",
    "Team": "Compliance"
  }
},
```

각 태그 이름에는 값이 하나만 있을 수 있습니다. 태그에 새 값을 제공하면 병합 작업을 사용하는 경우에도 이전 값이 바뀝니다. 다음 예제에서는 `Status` 태그를 _Normal_ 에서 _Green_ 으로 변경합니다.

```azurecli-interactive
az tag update --resource-id $resource --operation Merge --tags Status=Green
```

```output
"properties": {
  "tags": {
    "Dept": "Finance",
    "Environment": "Production",
    "Status": "Green",
    "Team": "Compliance"
  }
},
```

`--operation` 매개 변수를 `Replace`로 설정하면 기존 태그가 새 태그 집합으로 바뀝니다.

```azurecli-interactive
az tag update --resource-id $resource --operation Replace --tags Project=ECommerce CostCenter=00123 Team=Web
```

새 태그만 리소스에 남아 있습니다.

```output
"properties": {
  "tags": {
    "CostCenter": "00123",
    "Project": "ECommerce",
    "Team": "Web"
  }
},
```

리소스 그룹 또는 구독에도 동일한 명령을 사용할 수 있습니다. 태그를 지정할 리소스 그룹 또는 구독에 대한 식별자를 전달합니다.

리소스 그룹에 새 태그 집합을 추가하려면 다음을 사용합니다.

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag create --resource-id $group --tags Dept=Finance Status=Normal
```

리소스 그룹의 태그를 업데이트하려면 다음을 사용합니다.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags CostCenter=00123 Environment=Production
```

구독에 새 태그 집합을 추가하려면 다음을 사용합니다.

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag create --resource-id /subscriptions/$sub --tags CostCenter=00123 Environment=Dev
```

구독의 태그를 업데이트하려면 다음을 사용합니다.

```azurecli-interactive
az tag update --resource-id /subscriptions/$sub --operation Merge --tags Team="Web Apps"
```

### <a name="list-tags"></a>태그 나열

리소스, 리소스 그룹 또는 구독의 태그를 가져오려면 [az tag list](/cli/azure/tag#az_tag_list) 명령을 사용하여 엔터티의 리소스 ID를 전달합니다.

리소스의 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
resource=$(az resource show -g demoGroup -n demoStorage --resource-type Microsoft.Storage/storageAccounts --query "id" --output tsv)
az tag list --resource-id $resource
```

리소스 그룹의 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
group=$(az group show -n demoGroup --query id --output tsv)
az tag list --resource-id $group
```

구독의 태그를 보려면 다음을 사용합니다.

```azurecli-interactive
sub=$(az account show --subscription "Demo Subscription" --query id --output tsv)
az tag list --resource-id /subscriptions/$sub
```

### <a name="list-by-tag"></a>태그로 나열

특정 태그 이름 및 값이 있는 리소스를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az resource list --tag CostCenter=00123 --query [].name
```

태그 값에 관계없이 특정 태그 이름이 있는 리소스를 가져오려면 다음을 사용합니다.

```azurecli-interactive
az resource list --tag Team --query [].name
```

특정 태그 이름 및 값이 있는 리소스 그룹을 가져오려면 다음을 사용합니다.

```azurecli-interactive
az group list --tag Dept=Finance
```

### <a name="remove-tags"></a>태그 제거

특정 태그를 제거하려면 `az tag update`를 사용하고 `--operation`을 `Delete`로 설정합니다. 삭제하려는 태그를 전달합니다.

```azurecli-interactive
az tag update --resource-id $resource --operation Delete --tags Project=ECommerce Team=Web
```

지정된 태그가 제거됩니다.

```output
"properties": {
  "tags": {
    "CostCenter": "00123"
  }
},
```

모든 태그를 제거하려면 [az tag delete](/cli/azure/tag#az_tag_delete) 명령을 사용합니다.

```azurecli-interactive
az tag delete --resource-id $resource
```

### <a name="handling-spaces"></a>공백 처리

태그 이름 또는 값에 공백이 포함되어 있는 경우 큰따옴표로 묶습니다.

```azurecli-interactive
az tag update --resource-id $group --operation Merge --tags "Cost Center"=Finance-1222 Location="West US"
```

## <a name="arm-templates"></a>ARM 템플릿

ARM 템플릿(Azure Resource Manager 템플릿)을 사용하여 배포하는 동안 리소스, 리소스 그룹 및 구독에 태그를 지정할 수 있습니다.

> [!NOTE]
> ARM 템플릿 또는 Bicep 파일을 통해 적용하는 태그는 기존 태그를 덮어씁니다.

### <a name="apply-values"></a>값 적용

다음 예제에서는 세 개의 태그를 사용하여 스토리지 계정을 배포합니다. 두 개의 태그(`Dept` 및 `Environment`)는 리터럴 값으로 설정됩니다. 한 태그(`LastDeployed`)는 기본적으로 현재 날짜로 설정되는 매개 변수로 설정됩니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "utcShort": {
      "type": "string",
      "defaultValue": "[utcNow('d')]"
    },
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {
        "Dept": "Finance",
        "Environment": "Production",
        "LastDeployed": "[parameters('utcShort')]"
      },
      "properties": {}
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param location string = resourceGroup().location
param utcShort string = utcNow('d')

resource stgAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {
    Dept: 'Finance'
    Environment: 'Production'
    LastDeployed: utcShort
  }
}
```

---

### <a name="apply-an-object"></a>개체 적용

여러 태그를 저장하는 개체 매개 변수를 정의하고 해당 개체를 태그 요소에 적용할 수 있습니다. 이 접근법은 개체가 여러 속성을 가질 수 있기 때문에 이전 예제보다 더 많은 유연성을 제공합니다. 개체의 각 속성은 리소스에 대한 별도의 태그가 됩니다. 다음 예제에는 태그 요소에 적용되는 `tagValues`라는 매개 변수가 있습니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    },
    "tagValues": {
      "type": "object",
      "defaultValue": {
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": "[parameters('tagValues')]",
      "properties": {}
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param location string = resourceGroup().location
param tagValues object = {
  Dept: 'Finance'
  Environment: 'Production'
}

resource stgAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: tagValues
}
```

---

### <a name="apply-a-json-string"></a>JSON 문자열 적용

단일 태그에 여러 값을 저장하려면 값을 나타내는 JSON 문자열을 적용합니다. 전체 JSON 문자열은 256자를 초과할 수 없는 하나의 태그로 저장됩니다. 다음 예제에는 JSON 문자열의 여러 값을 포함하는 `CostCenter`라는 단일 태그를 포함합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {
        "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
      },
      "properties": {}
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param location string = resourceGroup().location

resource stgAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {
    CostCenter: '{"Dept":"Finance","Environment":"Production"}'
  }
}
```

---

### <a name="apply-tags-from-resource-group"></a>리소스 그룹의 태그 적용

리소스 그룹의 태그를 리소스에 적용하려면 [resourceGroup()](../templates/template-functions-resource.md#resourcegroup) 함수를 사용합니다. 태그 값을 가져올 때는 일부 문자가 점 표기법에서 올바르게 구문 분석되지 않으므로 `tags.tag-name` 구문 대신 `tags[tag-name]` 구문을 사용합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "location": {
      "type": "string",
      "defaultValue": "[resourceGroup().location]"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-04-01",
      "name": "[concat('storage', uniqueString(resourceGroup().id))]",
      "location": "[parameters('location')]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "tags": {
        "Dept": "[resourceGroup().tags['Dept']]",
        "Environment": "[resourceGroup().tags['Environment']]"
      },
      "properties": {}
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param location string = resourceGroup().location

resource stgAccount 'Microsoft.Storage/storageAccounts@2021-04-01' = {
  name: 'storage${uniqueString(resourceGroup().id)}'
  location: location
  sku: {
    name: 'Standard_LRS'
  }
  kind: 'Storage'
  tags: {
    Dept: resourceGroup().tags['Dept']
    Environment: resourceGroup().tags['Environment']
  }
}
```

---

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>리소스 그룹 또는 구독에 태그 적용

`Microsoft.Resources/tags` 리소스 종류를 배포하여 리소스 그룹 또는 구독에 태그를 추가할 수 있습니다. 태그는 배포의 대상 리소스 그룹 또는 구독에 적용됩니다. 템플릿을 배포할 때마다 이전에 적용된 태그를 바꿉니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tagName": {
      "type": "string",
      "defaultValue": "TeamName"
    },
    "tagValue": {
      "type": "string",
      "defaultValue": "AppTeam1"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/tags",
      "name": "default",
      "apiVersion": "2021-04-01",
      "properties": {
        "tags": {
          "[parameters('tagName')]": "[parameters('tagValue')]"
        }
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
param tagName string = 'TeamName'
param tagValue string = 'AppTeam1'

resource applyTags 'Microsoft.Resources/tags@2021-04-01' = {
  name: 'default'
  properties: {
    tags: {
      '${tagName}': tagValue
    }
  }
}
```

---

리소스 그룹에 태그를 적용하려면 PowerShell 또는 Azure CLI를 사용합니다. 태그를 지정하려는 리소스 그룹을 배포합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

구독에 태그를 적용하려면 PowerShell 또는 Azure CLI를 사용합니다. 태그를 지정할 구독에 배포합니다.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

구독 배포에 대한 자세한 내용은 [구독 수준에서 리소스 그룹 및 리소스 만들기](../templates/deploy-to-subscription.md)를 참조하세요.

다음 템플릿은 개체의 태그를 리소스 그룹 또는 구독에 추가합니다.

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "tags": {
      "type": "object",
      "defaultValue": {
        "TeamName": "AppTeam1",
        "Dept": "Finance",
        "Environment": "Production"
      }
    }
  },
  "resources": [
    {
      "type": "Microsoft.Resources/tags",
      "apiVersion": "2021-04-01",
      "name": "default",
      "properties": {
        "tags": "[parameters('tags')]"
      }
    }
  ]
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```Bicep
targetScope = 'subscription'

param tagObject object = {
  TeamName: 'AppTeam1'
  Dept: 'Finance'
  Environment: 'Production'
}

resource applyTags 'Microsoft.Resources/tags@2021-04-01' = {
  name: 'default'
  properties: {
    tags: tagObject
  }
}
```

---

## <a name="portal"></a>포털

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Azure REST API를 통해 태그 작업을 수행하려면 다음을 사용합니다.

* [태그 - 범위에서 만들기 또는 업데이트](/rest/api/resources/tags/createorupdateatscope)(PUT 작업)
* [태그 - 범위에서 업데이트](/rest/api/resources/tags/updateatscope)(PATCH 작업)
* [태그 - 범위에서 가져오기](/rest/api/resources/tags/getatscope)(GET 작업)
* [태그 - 범위에서 삭제](/rest/api/resources/tags/deleteatscope)(DELETE 작업)

## <a name="inherit-tags"></a>태그 상속

리소스 그룹 또는 구독에 적용되는 태그는 리소스에서 상속하지 않습니다. 구독 또는 리소스 그룹의 태그를 리소스에 적용하려면 [Azure 정책 - 태그](tag-policies.md)를 참조하세요.

## <a name="tags-and-billing"></a>태그 및 청구

태그를 사용하여 청구 데이터를 그룹화할 수 있습니다. 예를 들어 다양한 구성에 여러 VM을 실행하는 경우 태그를 사용하여 비용 센터별로 사용량을 그룹화할 수 있습니다. 또한 프로덕션 환경에서 실행 중인 VM에 대한 청구 사용량과 같이 런타임 환경별로 비용을 분류하는 데 태그를 사용할 수도 있습니다.

Azure Portal에서 사용할 수 있는 CSV(쉼표로 구분 된 값) 파일인 사용법 파일을 다운로드하여 태그에 대한 정보를 검색할 수 있습니다. 자세한 내용은 [Azure 청구서 및 일간 사용량 데이터 다운로드 또는 보기](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md)를 참조하세요. 대금 청구에 태그를 지원하는 서비스의 경우 **태그** 열에 태그가 나타납니다.

REST API 작업에 대한 내용은 [Azure 청구 REST API 참조](/rest/api/billing/)를 참조하세요.

## <a name="limitations"></a>제한 사항

다음 제한 사항이 태그에 적용됩니다.

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 각 리소스, 리소스 그룹 및 구독에는 최대 50개의 태그 이름/값 쌍이 지정될 수 있습니다. 허용되는 최대 수보다 많은 태그를 적용해야 하는 경우 태그 값에 JSON 문자열을 사용합니다. JSON 문자열은 단일 태그 이름에 적용되는 여러 값을 포함할 수 있습니다. 리소스 그룹 또는 구독은 각각 50개의 태그 이름/값 쌍이 있는 여러 리소스를 포함할 수 있습니다.
* 태그 이름은 512자로 제한되며 태그 값은 256자로 제한됩니다. 스토리지 계정에서 태그 이름은 128자로 제한되며 태그 값은 256자로 제한됩니다.
* Cloud Services와 같은 클래식 리소스에는 태그를 적용할 수 없습니다.
* Azure IP 그룹 및 Azure Firewall 정책은 PATCH 작업을 지원하지 않습니다. 즉, 포털을 통해 태그를 업데이트할 수 없습니다. 대신 해당 리소스에 대한 업데이트 명령을 사용합니다. 예를 들어 [az network ip-group update](/cli/azure/network/ip-group#az_network_ip_group_update) 명령을 사용하여 IP 그룹에 대한 태그를 업데이트할 수 있습니다.
* 태그 이름에는 다음 문자를 포함할 수 없습니다: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > * Azure DNS 영역 및 Traffic Manager는 태그 또는 숫자로 시작하는 태그의 공백 사용을 지원하지 않습니다.
   >
   > * Azure Front Door는 태그 이름에 `#` 또는 `:` 사용을 지원하지 않습니다.
   >
   > * 다음 Azure 리소스는 15개의 태그만 지원합니다.
   >     * Azure Automation
   >     * Azure CDN
   >     * Azure DNS(영역 및 A 레코드)
   >     * Azure 프라이빗 DNS(영역, A 레코드 및 가상 네트워크 링크)

## <a name="next-steps"></a>다음 단계

* 일부 리소스 유형은 태그를 지원하지 않습니다. 리소스 유형에 태그를 적용할 수 있는지 확인하려면 [Azure 리소스에 대한 태그 지원](tag-support.md)을 참조하세요.
* 태그 지정 전략을 구현하는 방법에 관한 권장 사항은 [리소스 명명 및 태그 지정 결정 가이드](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)를 참조하세요.
