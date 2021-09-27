---
title: 템플릿 사양 만들기 및 배포
description: 템플릿 사양을 만들고 이를 조직 내 다른 사용자와 공유하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 05/04/2021
ms.author: tomfitz
ms.custom: devx-track-azurepowershell
author: tfitzmac
ms.openlocfilehash: bf417b6ca70012a3cf4bce17640aa4bc9ab9fd08
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611209"
---
# <a name="azure-resource-manager-template-specs"></a>Azure Resource Manager 템플릿 사양

템플릿 사양은 나중에 배포할 수 있도록 Azure에 Azure Resource Manager 템플릿(ARM 템플릿)을 저장하기 위한 리소스 유형입니다. 이 리소스 유형을 사용하여 조직 내 다른 사용자와 ARM 템플릿을 공유할 수 있습니다. 다른 Azure 리소스와 마찬가지로, Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 템플릿 사양을 공유할 수 있습니다.

**Microsoft.Resources/templateSpecs** 는 템플릿 사양에 대한 리소스 유형입니다. 기본 템플릿과 연결된 템플릿으로 구성됩니다. Azure는 리소스 그룹에 템플릿 사양을 안전하게 저장합니다. 템플릿 사양은 [버전 관리](#versioning)를 지원합니다.

템플릿 사양을 배포하려면 PowerShell, Azure CLI, Azure Portal, REST 및 기타 지원되는 SDK 및 클라이언트와 같은 표준 Azure 도구를 사용합니다. 템플릿에서와 동일한 명령을 사용합니다.

> [!NOTE]
> Azure PowerShell에서 템플릿 사양을 사용하려면 [버전 5.0.0 이상](/powershell/azure/install-az-ps)을 설치해야 합니다. Azure CLI에서 사용하려면 [버전 2.14.2 이상](/cli/azure/install-azure-cli)을 사용합니다.

### <a name="microsoft-learn"></a>Microsoft Learn

템플릿 사양에 대 한 자세한 내용 및 실습 지침은 **Microsoft Learn** 에서 [템플릿 사양을 사용 하 여 재사용 가능한 인프라 코드의 라이브러리 게시](/learn/modules/arm-template-specs) 를 참조 하세요.

## <a name="why-use-template-specs"></a>템플릿 사양을 사용하는 이유는 무엇인가요?

템플릿 사양은 다음과 같은 이점을 제공합니다.

* 템플릿 사양에 대해 표준 ARM 템플릿을 사용합니다.
* SAS 토큰이 아닌 Azure RBAC를 통해 액세스를 관리합니다.
* 사용자가 템플릿에 쓰기 액세스 권한이 없어도 템플릿 사양을 배포할 수 있습니다.
* PowerShell 스크립트 또는 DevOps 파이프라인과 같은 기존 배포 프로세스에 템플릿 사양을 통합할 수 있습니다.

템플릿 사양을 사용하면 정식 템플릿을 만들고 조직 내 팀과 이를 공유할 수 있습니다. 템플릿 사양은 배포를 위해 Azure Resource Manager에 제공되지만 올바른 권한이 없는 사용자가 액세스할 수 없기 때문에 안전합니다. 사용자가 템플릿을 배포하기 위해서는 템플릿 사양에 대해 읽기 액세스 권한만 필요하므로, 다른 사람에게 수정 권한 없이 템플릿을 공유할 수 있습니다.

GitHub 리포지토리 또는 스토리지 계정에 현재 템플릿이 있으면 템플릿을 공유하거나 사용하려고 할 때 여러 문제가 발생합니다. 템플릿을 배포하려면 템플릿을 공개적으로 액세스할 수 있게 하거나 SAS 토큰으로 액세스를 관리해야 합니다. 이러한 제한을 우회하기 위해서는 사용자가 결국 원래 템플릿으로부터 분기되는 로컬 복사본을 만들 수 있습니다. 템플릿 사양은 템플릿 공유를 단순화합니다.

조직 내 관리자는 템플릿 사양에 포함된 템플릿이 조직 요구 사항 및 지침을 따르는지 확인해야 합니다.

## <a name="create-template-spec"></a>템플릿 사양 만들기

다음 예제에서는 Azure에서 스토리지 계정을 만들기 위한 간단한 템플릿을 보여줍니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ]
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2019-06-01",
      "name": "[concat('store', uniquestring(resourceGroup().id))]",
      "location": "[resourceGroup().location]",
      "kind": "StorageV2",
      "sku": {
        "name": "[parameters('storageAccountType')]"
      }
    }
  ]
}
```

템플릿 사양을 만들 때는 PowerShell 또는 CLI 명령이 기본 템플릿 파일에 전달됩니다. 기본 템플릿이 연결된 템플릿을 참조하는 경우 이러한 명령이 이를 찾아 패키지하여 템플릿 사양을 만듭니다. 자세한 내용은 [연결된 템플릿으로 템플릿 사양 만들기](#create-a-template-spec-with-linked-templates)를 참조하세요.

다음을 사용하여 템플릿 사양을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec -Name storageSpec -Version 1.0a -ResourceGroupName templateSpecsRg -Location westus2 -TemplateFile ./mainTemplate.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

---

다음을 사용하여 구독에 있는 모든 템플릿 사양을 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts list
```

---

해당 버전을 포함하여 템플릿 사양의 세부 정보를 확인할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Get-AzTemplateSpec -ResourceGroupName templateSpecsRG -Name storageSpec
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts show \
    --name storageSpec \
    --resource-group templateSpecRG \
    --version "1.0a"
```

---

## <a name="deploy-template-spec"></a>템플릿 사양 배포

템플릿 사양을 만든 후에는 템플릿 사양에 대해 **읽기** 액세스 권한이 있는 사용자가 이를 배포할 수 있습니다. 액세스 권한 부여에 대한 자세한 내용은 [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 그룹 액세스 권한 부여](../../role-based-access-control/tutorial-role-assignments-group-powershell.md)를 참조하세요.

포털, PowerShell, Azure CLI 또는 더 큰 템플릿 배포에 있는 연결된 템플릿을 통해 템플릿 사양을 배포할 수 있습니다. 조직 내 사용자는 Azure의 모든 범위(리소스 그룹, 구독, 관리 그룹 또는 테넌트)에 템플릿 사양을 배포할 수 있습니다.

템플릿의 경로 또는 URI를 전달하는 대신 해당 리소스 ID를 제공하여 템플릿 사양을 배포합니다. 리소스 ID의 형식은 다음과 같습니다.

**/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/Microsoft.Resources/templateSpecs/{template-spec-name}/versions/{template-spec-version}**

리소스 ID에는 템플릿 사양의 버전 번호가 포함됩니다.

예를 들어 다음 명령으로 템플릿 사양을 배포합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/templateSpecsRG/providers/Microsoft.Resources/templateSpecs/storageSpec/versions/1.0a"

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

실제로, 일반적으로 `Get-AzTemplateSpec` 또는 `az ts show`를 실행하여 배포하려는 템플릿 사양의 ID를 가져옵니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$id = (Get-AzTemplateSpec -Name storageSpec -ResourceGroupName templateSpecsRg -Version 1.0a).Versions.Id

New-AzResourceGroupDeployment `
  -ResourceGroupName demoRG `
  -TemplateSpecId $id
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0a" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

---

또한 다음 형식으로 URL을 열어서 템플릿 사양을 배포할 수 있습니다.

```url
https://portal.azure.com/#create/Microsoft.Template/templateSpecVersionId/%2fsubscriptions%2f{subscription-id}%2fresourceGroups%2f{resource-group-name}%2fproviders%2fMicrosoft.Resources%2ftemplateSpecs%2f{template-spec-name}%2fversions%2f{template-spec-version}
```

## <a name="parameters"></a>매개 변수

템플릿 사양에 매개 변수를 전달하는 것은 ARM 템플릿에 매개 변수를 전달하는 것과 같습니다. 인라인으로 또는 매개 변수 파일에 매개 변수 값을 추가합니다.

매개 변수를 인라인으로 전달하려면 다음을 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -StorageAccountType Standard_GRS
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters storageAccountType='Standard_GRS'
```

---

로컬 매개 변수 파일을 만들려면 다음을 사용합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "StorageAccountType": {
      "value": "Standard_GRS"
    }
  }
}
```

그리고 다음으로 매개 변수 파일을 전달합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -TemplateSpecId $id `
  -ResourceGroupName demoRG `
  -TemplateParameterFile ./mainTemplate.parameters.json
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az deployment group create \
  --resource-group demoRG \
  --template-spec $id \
  --parameters "./mainTemplate.parameters.json"
```

---

## <a name="versioning"></a>버전 관리

템플릿 사양을 만들 때는 이에 대한 버전 이름을 제공합니다. 템플릿 코드로 반복할 때 기존 버전(핫픽스용)을 업데이트하거나 새 버전을 게시할 수 있습니다. 버전은 텍스트 문자열입니다. 유의적 버전을 포함하여 특정 버전 관리 시스템을 따르도록 선택할 수 있습니다. 템플릿 사양의 사용자는 이를 배포할 때 사용할 버전 이름을 제공할 수 있습니다.

## <a name="use-tags"></a>태그 사용

[태그](../management/tag-resources.md)를 통해 리소스를 논리적으로 구성할 수 있습니다. Azure PowerShell 및 Azure CLI를 사용하여 템플릿 사양에 태그를 추가할 수 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzTemplateSpec `
  -Name storageSpec `
  -Version 1.0a `
  -ResourceGroupName templateSpecsRg `
  -Location westus2 `
  -TemplateFile ./mainTemplate.json `
  -Tag @{Dept="Finance";Environment="Production"}
```

# <a name="cli"></a>[CLI](#tab/azure-cli)

```azurecli
az ts update \
  --name storageSpec \
  --version "1.0a" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json" \
  --tags Dept=Finance Environment=Production
```

---

지정된 버전 매개 변수를 사용하지만 태그/태그 매개 변수 없이 템플릿 사양을 만들거나 수정하는 경우:

- 템플릿 사양이 존재하고 태그가 있지만 버전이 존재하지 않으면 새 버전이 기존 템플릿 사양과 동일한 태그를 상속합니다.

태그/태그 매개 변수와 버전 매개 변수가 모두 지정된 템플릿 사양을 만들거나 수정하는 경우:

- 템플릿 사양과 버전이 모두 존재하지 않으면 새 템플릿 사양 및 새 버전 모두에 태그가 추가됩니다.
- 템플릿 사양이 존재하지만 버전이 존재하지 않으면 태그가 새 버전에만 추가됩니다.
- 템플릿 사양과 버전이 모두 존재하면 태그가 버전에만 적용됩니다.

태그/태그 매개 변수가 지정되었지만 지정된 버전 매개 변수가 없는 템플릿을 수정하는 경우 템플릿 사양에만 태그가 추가됩니다.

## <a name="create-a-template-spec-with-linked-templates"></a>연결된 템플릿을 사용하여 템플릿 사양 만들기

템플릿 사양의 기본 템플릿이 연결된 템플릿을 참조하는 경우 PowerShell 및 CLI 명령이 로컬 드라이브에서 연결된 템플릿을 자동으로 찾고 패키지할 수 있습니다. 템플릿 사양을 호스트하기 위해 스토리지 계정 또는 리포지토리를 수동으로 구성할 필요가 없습니다. 모든 것이 템플릿 사양 리소스에 자체 포함된 상태입니다.

다음 예는 연결된 템플릿이 2개 있는 기본 템플릿으로 구성됩니다. 이 예제는 템플릿에서 발췌한 내용입니다. 여기에서는 다른 템플릿에 연결하기 위해 `relativePath`라는 이름의 속성이 사용됩니다. 배포 리소스에 대해 `2020-06-01` 또는 그 이후의 `apiVersion`을 사용해야 합니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/webapp.json"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "relativePath": "artifacts/database.json"
        }
      }
    }
  ],
  "outputs": {}
}
```

이전 예제에서 템플릿 사양을 만들기 위해 PowerShell 또는 CLI 명령을 실행하면 명령이 기본 템플릿, 웹앱 템플릿(`webapp.json`) 및 데이터베이스 템플릿(`database.json`)의 세 가지 파일을 찾고 이를 템플릿 사양으로 패키지합니다.

자세한 내용은 [자습서: 연결된 템플릿을 사용하여 템플릿 사양 만들기](template-specs-create-linked.md)를 참조하세요.

## <a name="deploy-template-spec-as-a-linked-template"></a>템플릿 사양을 연결된 템플릿으로 배포

템플릿 사양을 만든 후에는 ARM 템플릿 또는 다른 템플릿 사양으로부터 이를 쉽게 다시 사용할 수 있습니다. 리소스 ID를 템플릿에 추가하여 템플릿 사양에 연결합니다. 연결된 템플릿 사양은 기본 템플릿을 배포할 때 자동으로 배포됩니다. 이 동작을 활용해서 모듈식 템플릿 사양을 개발하고 필요에 따라 이를 다시 사용할 수 있습니다.

예를 들어 네트워킹 리소스를 배포하는 템플릿 사양과 스토리지 리소스를 배포하는 또 다른 템플릿 사양을 만들 수 있습니다. ARM 템플릿에서는 네트워킹 또는 스토리지 리소스를 구성해야 할 때마다 이러한 2개 템플릿 사양에 연결할 수 있습니다.

다음 예제는 앞의 예제와 비슷하지만, `relativePath` 속성을 사용하여 로컬 템플릿에 연결하는 대신 `id` 속성을 사용하여 템플릿 사양에 연결합니다. 배포 리소스에 대한 API 버전으로 `2020-06-01`을 사용합니다. 이 예제에서 템플릿 사양은 **templateSpecsRG** 라는 리소스 그룹에 있습니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  ...
  "resources": [
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "networkingDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'networkingSpec', '1.0a')]"
        }
      }
    },
    {
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2020-06-01",
      "name": "storageDeployment",
      ...
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "id": "[resourceId('templateSpecsRG', 'Microsoft.Resources/templateSpecs/versions', 'storageSpec', '1.0a')]"
        }
      }
    }
  ],
  "outputs": {}
}
```

템플릿 사양 연결에 대한 자세한 내용은 [자습서: 템플릿 사양을 연결된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 템플릿 사양을 만들고 배포하려면 [빠른 시작: 템플릿 사양 만들기 및 배포](quickstart-create-template-specs.md)를 참조하세요.

* 템플릿 사양에서 템플릿 연결에 대한 자세한 내용은 [자습서: 연결된 템플릿을 사용하여 템플릿 사양 만들기](template-specs-create-linked.md)를 참조하세요.

* 템플릿 사양을 연결된 템플릿으로 배포에 대한 자세한 내용은 [자습서: 템플릿 사양을 연결된 템플릿으로 배포](template-specs-deploy-linked-template.md)를 참조하세요.
