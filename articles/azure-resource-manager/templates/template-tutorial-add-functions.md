---
title: 자습서 - 템플릿 함수 추가
description: ARM 템플릿(Azure Resource Manager 템플릿)에 템플릿 함수를 추가하여 값을 생성합니다.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: ''
ms.openlocfilehash: 6f8812e4894a9ba0daa4423fae77b43bcdb758d6
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643995"
---
# <a name="tutorial-add-template-functions-to-your-arm-template"></a>자습서: ARM 템플릿에 템플릿 함수 추가

이 자습서에서는 ARM 템플릿(Azure Resource Manager 템플릿)에 [템플릿 함수](template-functions.md)를 추가하는 방법을 알아봅니다. 함수를 사용하여 값을 동적으로 구성합니다. 이러한 시스템 제공 템플릿 함수 외에도 [사용자 정의 함수](./user-defined-functions.md)를 만들 수 있습니다. 이 자습서를 완료하는 데 **7분** 이 소요됩니다.

## <a name="prerequisites"></a>사전 요구 사항

[매개 변수에 대한 자습서](template-tutorial-add-parameters.md)를 완료하는 것이 좋지만 필수는 아닙니다.

Resource Manager Tools 확장이 포함된 Visual Studio Code 및 Azure PowerShell 또는 Azure CLI가 있어야 합니다. 자세한 내용은 [템플릿 도구](template-tutorial-create-first-template.md#get-tools)를 참조하세요.

## <a name="review-template"></a>템플릿 검토

이전 자습서의 끝 부분에 템플릿에는 다음 JSON이 있습니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-sku/azuredeploy.json":::

스토리지 계정의 위치는 **eastus** 로 하드 코딩되어 있습니다. 하지만 스토리지 계정을 다른 지역에 배포해야 할 수도 있습니다. 유연성이 부족한 템플릿 문제가 다시 발생합니다. 위치에 대한 매개 변수를 추가할 수 있지만 기본값이 하드 코딩된 값보다 적절하면 매우 유용할 수 있습니다.

## <a name="use-function"></a>함수 사용

[매개 변수 자습서](./template-tutorial-add-parameters.md#make-template-reusable)를 완료한 경우 함수를 사용한 것입니다. `"[parameters('storageName')]"`을 추가할 때 [parameters](template-functions-deployment.md#parameters) 함수를 사용했습니다. 대괄호는 대괄호 안의 구문이 [템플릿 표현식](template-expressions.md)임을 나타냅니다. Resource Manager는 구문을 리터럴 값으로 처리하지 않고 구문을 분석합니다.

함수는 배포 중에 값을 동적으로 가져와서 템플릿에 유연성을 추가합니다. 이 자습서에서는 함수를 사용하여 배포에 사용 중인 리소스 그룹의 위치를 가져옵니다.

다음 예제는 `location`이라는 매개 변수를 추가하기 위한 변경 사항을 강조 표시합니다. 매개 변수 기본값은 [resourceGroup](template-functions-resource.md#resourcegroup) 함수를 호출합니다. 이 함수는 배포에 사용되는 리소스 그룹에 대한 정보가 포함된 개체를 반환합니다. 이러한 개체의 속성 중 하나가 위치 속성입니다. 기본값을 사용하면, 스토리지 계정 위치는 리소스 그룹과 동일한 위치입니다. 리소스 그룹 내의 리소스는 동일한 위치를 공유하지 않아도 됩니다. 필요한 경우, 다른 위치를 제공할 수도 있습니다.

전체 파일을 복사하고 템플릿을 해당 콘텐츠로 바꿉니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-location/azuredeploy.json" range="1-44" highlight="24-27,34":::

## <a name="deploy-template"></a>템플릿 배포

이전 자습서에서 스토리지 계정은 미국 동부에 만들었지만 리소스 그룹은 미국 중부에 만들었습니다. 이 자습서에서는 리소스 그룹과 동일한 지역에 스토리지 계정이 만들어집니다. 위치에 기본값을 사용하기 때문에 해당 매개 변수 값을 제공할 필요가 없습니다. 다른 위치에 스토리지 계정을 만들기 때문에 스토리지 계정에 대해 새 이름을 제공해야 합니다. 예를 들어, **store1** 대신 **store2** 를 접두사로 사용합니다.

리소스 그룹을 만들지 않은 경우 [리소스 그룹 만들기](template-tutorial-create-first-template.md#create-resource-group)를 참조하세요. 이 예제에서는 [첫 번째 자습서](template-tutorial-create-first-template.md#deploy-template)에 표시된 대로 `templateFile` 변수를 템플릿 파일의 경로로 설정했다고 가정합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroupDeployment `
  -Name addlocationparameter `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile `
  -storageName "{new-unique-name}"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
az deployment group create \
  --name addlocationparameter \
  --resource-group myResourceGroup \
  --template-file $templateFile \
  --parameters storageName={new-unique-name}
```

---

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
1. 배포한 리소스 그룹을 선택합니다.
1. 스토리지 계정 리소스가 배포되었고 리소스 그룹과 동일한 위치에 있는 것을 볼 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우에는 리소스 그룹을 삭제하여 배포된 리소스를 정리하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 매개 변수의 기본값을 정의할 때 함수를 사용했습니다. 이 자습서 시리즈에서는 함수를 계속 사용합니다. 시리즈의 끝 부분에서 템플릿의 모든 섹션에 함수를 추가합니다.

> [!div class="nextstepaction"]
> [변수 추가](template-tutorial-add-variables.md)
