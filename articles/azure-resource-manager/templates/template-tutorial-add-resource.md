---
title: 자습서 - 템플릿에 리소스 추가
description: 첫 번째 ARM 템플릿(Azure Resource Manager 템플릿)을 만드는 단계를 설명합니다. 템플릿 파일 구문 및 스토리지 계정을 배포하는 방법에 대해 알아봅니다.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 82c6b6cd0dde6b321e4de8a87f99e1adad69040d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128662118"
---
# <a name="tutorial-add-a-resource-to-your-arm-template"></a>자습서: ARM 템플릿에 리소스 추가

[이전 자습서](template-tutorial-create-first-template.md)에서는 빈 ARM 템플릿(Azure Resource Manager 템플릿)을 만들고 배포하는 방법을 알아보았습니다. 이제 실제 리소스를 배포할 준비가 되었습니다. 이 자습서에서는 스토리지 계정을 추가합니다. 이 자습서를 완료하는 데 **9분** 정도 걸립니다.

## <a name="prerequisites"></a>사전 요구 사항

[템플릿에 대한 입문용 자습서](template-tutorial-create-first-template.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Resource Manager Tools 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [템플릿 도구](template-tutorial-create-first-template.md#get-tools)를 참조하세요.

## <a name="add-resource"></a>리소스 추가

기존 템플릿에 스토리지 계정 정의를 추가하려면 다음 예제에 강조 표시되어 있는 JSON을 살펴보세요. 템플릿의 섹션을 복사하는 대신 전체 파일을 복사하고 해당 내용으로 템플릿을 바꿉니다.

`{provide-unique-name}` 및 중괄호 `{}`을(를) 고유한 스토리지 계정 이름으로 바꿉니다.

> [!IMPORTANT]
> 스토리지 계정 이름은 Azure 내에서 고유해야 합니다. 이름에는 소문자 또는 숫자만 사용할 수 있습니다. 24자 이하여야 합니다. **store1** 을 접두사로 사용하고 이니셜과 오늘 날짜를 추가하는 이름 지정 패턴을 시도해 볼 수 있습니다. 예를 들어 **store1abc09092019** 와 같은 이름을 사용할 수 있습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-storage/azuredeploy.json" range="1-19" highlight="5-17":::

스토리지 계정에 대한 고유 이름을 추측하는 것은 쉽지 않으며 대량 배포를 자동화하는 데 적합하지 않습니다. 이 자습서 시리즈의 뒷부분에서 고유한 이름을 쉽게 만들 수 있는 템플릿 기능을 사용합니다.

## <a name="resource-properties"></a>리소스 속성

각 리소스 종류에 사용할 속성을 찾는 방법이 궁금할 수 있습니다. [ARM 템플릿 참조](/azure/templates/)를 사용하여 배포하려는 리소스 종류를 찾을 수 있습니다.

배포하는 모든 리소스에는 최소한 다음 세 가지 속성이 있습니다.

- `type`: 리소스 유형입니다. 이 값은 리소스 공급자의 네임스페이스와 리소스 유형의 조합(예: `Microsoft.Storage/storageAccounts`)입니다.
- `apiVersion`: 리소스를 만들 때 사용하는 REST API의 버전입니다. 각 리소스 공급자는 자체 API 버전을 게시하므로 이 값은 유형에 따라 다릅니다.
- `name`: 리소스의 이름입니다.

대부분의 리소스에는 `location` 속성이 있으며, 이 속성은 리소스가 배포되는 지역을 설정합니다.

다른 속성은 리소스 유형 및 API 버전에 따라 다릅니다. API 버전과 사용 가능한 속성 간의 연결을 이해하는 것이 중요하므로 좀 더 자세히 살펴보겠습니다.

이 자습서에서는 템플릿에 스토리지 계정을 추가했습니다. [storageAccounts 2021-04-01](/azure/templates/microsoft.storage/2021-04-01/storageaccounts)에서 해당 API 버전을 확인할 수 있습니다. 템플릿에 모든 속성을 추가하지는 않았습니다. 많은 속성은 선택 사항입니다. `Microsoft.Storage` 리소스 공급자가 새로운 API 버전을 출시할 수 있지만, 배포 중인 버전을 변경할 필요는 없습니다. 해당 버전을 계속 사용하고 배포 결과가 일관적이라는 것을 확인할 수 있습니다.

[storageAccounts 2016-05-01](/azure/templates/microsoft.storage/2016-05-01/storageaccounts)와 같은 이전 API 버전이 보이는 경우, 더 작은 속성 집합을 사용할 수 있습니다.

리소스의 API 버전을 변경하려는 경우에는, 해당 버전의 속성을 평가하고 템플릿을 적절히 조정해야 합니다.

## <a name="deploy-template"></a>템플릿 배포

템플릿을 배포하여 스토리지 계정을 만들 수 있습니다. 배포에 다른 이름을 지정하면 기록에서 쉽게 찾을 수 있습니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](template-tutorial-create-first-template.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](template-tutorial-create-first-template.md#deploy-template)에 표시된 대로 `templateFile` 변수를 템플릿 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addstorage `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addstorage \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

발생할 수 있는 두 가지 배포 오류는 다음과 같습니다.

- `Error: Code=AccountNameInvalid; Message={provide-unique-name}`은 유효한 스토리지 계정 이름이 아닙니다. 스토리지 계정 이름은 3자에서 24자 사이여야 하며 숫자와 소문자만 사용해야 합니다.

    템플릿에서 `{provide-unique-name}` 을 고유한 스토리지 계정 이름으로 바꿉니다. [리소스 추가](#add-resource)를 참조하세요.

- `Error: Code=StorageAccountAlreadyTaken; Message=The storage account named store1abc09092019`가 이미 사용 중입니다.

    템플릿에서 다른 스토리지 계정 이름을 시도해 보세요.

이 배포는 스토리지 계정이 생성되기 때문에 빈 템플릿 배포보다 시간이 오래 걸립니다. 약 1분 정도 걸릴 수 있지만 일반적으로 더 빠릅니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 배포한 리소스 그룹을 선택합니다.
1. 스토리지 계정이 배포된 것을 볼 수 있습니다.
1. 배포 레이블이 이제 다음과 같이 표시됩니다. **배포: 2 성공함**.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure 스토리지 계정을 배포하는 간단한 템플릿을 만들었습니다. 이후 자습서에서는 매개 변수, 변수, 리소스 및 출력을 템플릿에 추가하는 방법을 알아봅니다. 이러한 기능은 훨씬 더 복잡한 템플릿을 구성하는 요소입니다.

> [!div class="nextstepaction"]
> [매개 변수 추가](template-tutorial-add-parameters.md)
