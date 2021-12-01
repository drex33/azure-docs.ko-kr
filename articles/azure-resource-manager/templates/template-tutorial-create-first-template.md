---
title: 자습서 - 템플릿 만들기 및 배포
description: 첫 번째 ARM 템플릿(Azure Resource Manager 템플릿)을 만듭니다. 이 자습서에서는 템플릿 파일 구문 및 스토리지 계정을 배포하는 방법에 대해 알아봅니다.
author: mumian
ms.date: 12/01/2021
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: ceffb4fe49b1e1c24d60560ac3a21dc667d1b683
ms.sourcegitcommit: 9567c42d1e5270af16a1a8090f11a3b12131010d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133426078"
---
# <a name="tutorial-create-and-deploy-your-first-arm-template"></a>자습서: 첫 번째 ARM 템플릿 만들기 및 배포

이 자습서에서는 ARM 템플릿(Azure Resource Manager 템플릿)을 소개합니다. 시작 템플릿을 만들어 Azure에 배포하는 방법을 보여 줍니다. 템플릿의 구조와 템플릿을 사용하는 데 필요한 도구에 대해 알아봅니다. 이 자습서를 완료하는 데 약 **12분** 정도 걸리지만, 실제 시간은 설치해야 하는 도구 수에 따라 달라집니다.

이 자습서는 시리즈의 첫 번째 자습서입니다. 시리즈를 진행하면서 ARM 템플릿의 모든 핵심 부분을 살펴볼 때까지 시작 템플릿을 단계별로 수정합니다. 이러한 요소는 훨씬 더 복잡한 템플릿의 구성 요소입니다. 시리즈가 끝날 때쯤에는 자신만의 고유한 템플릿을 만들고, 이 템플릿을 사용하여 배포를 자동화할 준비가 됩니다.

템플릿을 사용하면 얻을 수 있는 이점과 템플릿을 사용하여 배포를 자동화해야 하는 이유에 대해 알아보려면 [ARM 템플릿](overview.md)을 참조하세요. Microsoft Learn의 단계별 모듈 세트를 통한 ARM 템플릿에 대한 자세한 내용은 [ARM 템플릿을 사용하여 Azure에서 리소스 배포 및 관리](/learn/paths/deploy-manage-resource-manager-templates/)를 참조하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

> [!TIP]
> ARM 템플릿을 접하는 경우 Bicep을 대신 학습하는 것이 좋습니다. Bicep은 ARM 템플릿과 동일한 기능을 제공하지만 사용하기 쉬운 구문을 제공하는 새로운 언어입니다. Bicep 학습을 시작하려면 [빠른 시작: Visual Studio Code 사용하여 Bicep 파일 만들기를](../bicep/quickstart-create-bicep-use-visual-studio-code.md)참조하세요.

## <a name="get-tools"></a>도구 가져오기

먼저 템플릿을 만들고 배포하는 데 필요한 도구가 있는지 확인합니다. 이러한 도구를 로컬 머신에 설치합니다.

### <a name="editor"></a>편집기

템플릿은 JSON 파일입니다. 템플릿을 만들려면 적절한 JSON 편집기가 필요합니다. Resource Manager 도구 확장이 있는 Visual Studio Code를 사용하는 것이 좋습니다. 이러한 도구를 설치해야 하는 경우 [빠른 시작: Visual Studio Code를 사용하여 ARM 템플릿 만들기](quickstart-create-templates-use-visual-studio-code.md)를 참조하세요.

### <a name="command-line-deployment"></a>명령줄 배포

템플릿을 배포하려면 Azure PowerShell 또는 Azure CLI도 필요합니다. Azure CLI를 사용하는 경우 최신 버전을 사용해야 합니다. 설치 지침은 다음을 참조하세요.

- [Azure PowerShell 설치](/powershell/azure/install-az-ps)
- [Windows에 Azure CLI 설치](/cli/azure/install-azure-cli-windows)
- [Linux에 Azure CLI 설치](/cli/azure/install-azure-cli-linux)
- [macOS에 Azure CLI 설치](/cli/azure/install-azure-cli-macos)

Azure PowerShell 또는 Azure CLI가 설치되면 처음으로 로그인해야 합니다. 도움을 받으려면 [로그인 - PowerShell](/powershell/azure/install-az-ps#sign-in) 또는 [로그인 - Azure CLI](/cli/azure/get-started-with-azure-cli#sign-in)를 참조하세요.

> [!IMPORTANT]
> Azure CLI를 사용하는 경우 버전 2.6 이상이 있는지 확인합니다. 이전 버전을 사용하는 경우 이 자습서에 표시된 명령이 작동하지 않습니다. `az --version`을 실행하여 설치된 버전을 확인합니다.

이제 템플릿에 대한 학습을 시작할 준비가 되었습니다.

## <a name="create-your-first-template"></a>첫 번째 템플릿 만들기

1. Resource Manager 도구 확장이 설치된 Visual Studio Code를 엽니다.
1. **파일** 메뉴에서 **새 파일** 을 선택하여 새 파일을 만듭니다.
1. **파일** 메뉴에서 **다른 이름으로 저장** 을 선택합니다.
1. 파일 이름을 _azuredeploy_ 로 지정하고, _json_ 파일 확장명을 선택합니다. 파일의 전체 이름은 _azuredeploy.json_ 입니다.
1. 파일을 워크스테이션에 저장합니다. 나중에 템플릿을 배포할 때 해당 경로를 제공하므로 기억하기 쉬운 경로를 선택합니다.
1. 다음 JSON을 복사하여 파일에 붙여넣습니다.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "resources": []
    }
    ```

    Visual Studio Code 환경은 다음과 같습니다.

    ![ARM 템플릿 Visual Studio Code 첫 번째 템플릿](./media/template-tutorial-create-first-template/resource-manager-visual-studio-code-first-template.png)

    이 템플릿은 리소스를 배포하지 않습니다. 빈 템플릿으로 시작하면 템플릿 배포 단계를 익히는 동시에 문제가 발생할 가능성을 최소화할 수 있습니다.

    JSON 파일에 있는 요소는 다음과 같습니다.

    - `$schema`: JSON 스키마 파일의 위치를 지정합니다. 스키마 파일은 템플릿 내에서 사용할 수 있는 속성을 설명합니다. 예를 들어 스키마는 `resources`를 템플릿의 유효한 속성 중 하나로 정의합니다. 스키마의 날짜가 2019-04-01이라는 것에 걱정하지 마세요. 이 스키마 버전은 최신 버전이며, 최신 기능이 모두 포함되어 있습니다. 도입된 이후 호환성이 손상되는 변경이 없었으므로 스키마 날짜가 변경되지 않았습니다.
    - `contentVersion`: 템플릿의 버전(예: 1.0.0.0)을 지정합니다. 이 요소에 값을 제공할 수 있습니다. 이 값을 사용하여 템플릿에서 중요한 변경 내용을 문서화할 수 있습니다. 템플릿을 사용하여 리소스를 배포할 때 이 값을 사용하면 정확한 템플릿이 사용되도록 할 수 있습니다.
    - `resources`: 배포하거나 업데이트하려는 리소스가 포함됩니다. 현재 비어 있지만 나중에 리소스를 추가할 수 있습니다.

1. 파일을 저장합니다.

축하합니다. 첫 번째 템플릿을 만들었습니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

Azure PowerShell/Azure CLI를 사용하여 시작하려면 Azure 자격 증명으로 로그인합니다.

다음 코드 섹션에서 탭을 선택하여 Azure PowerShell과 Azure CLI 중 하나를 선택합니다. 이 문서의 CLI 예제는 Bash 셸에 대해 작성되었습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Connect-AzAccount
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az login
```

---

Azure 구독이 여러 개 있는 경우 사용할 구독을 선택합니다. `SubscriptionName`을 구독 이름으로 바꿉니다. 구독 이름 대신 구독 ID를 사용할 수도 있습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzContext SubscriptionName
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az account set --subscription SubscriptionName
```

---

## <a name="create-resource-group"></a>리소스 그룹 만들기

템플릿을 배포하는 경우 리소스를 포함할 리소스 그룹을 지정합니다. 배포 명령을 실행하기 전에 먼저 Azure CLI 또는 Azure PowerShell을 사용하여 리소스 그룹을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
New-AzResourceGroup `
  -Name myResourceGroup `
  -Location "Central US"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group create \
  --name myResourceGroup \
  --location "Central US"
```

---

## <a name="deploy-template"></a>템플릿 배포

템플릿을 배포하려면 Azure CLI 또는 Azure PowerShell을 사용합니다. 만든 리소스 그룹을 사용합니다. 배포 이름은 배포 기록에서 쉽게 식별할 수 있도록 지정합니다. 편의를 위해 템플릿 파일의 경로를 저장하는 변수도 만듭니다. 이 변수를 사용하면 배포할 때마다 경로를 다시 입력할 필요가 없으므로 배포 명령을 더 쉽게 실행할 수 있습니다. `{provide-the-path-to-the-template-file}` 및 중괄호 `{}`을(를) 템플릿 파일의 경로로 바꿉니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
$templateFile = "{provide-the-path-to-the-template-file}"
New-AzResourceGroupDeployment `
  -Name blanktemplate `
  -ResourceGroupName myResourceGroup `
  -TemplateFile $templateFile
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이 배포 명령을 실행하려면 Azure CLI의 [최신 버전](/cli/azure/install-azure-cli)이 있어야 합니다.

```azurecli
templateFile="{provide-the-path-to-the-template-file}"
az deployment group create \
  --name blanktemplate \
  --resource-group myResourceGroup \
  --template-file $templateFile
```

---

배포 명령에서 결과를 반환합니다. `ProvisioningState`를 찾아서 배포에 성공했는지 확인합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

![PowerShell 배포 프로비저닝 상태](./media/template-tutorial-create-first-template/resource-manager-deployment-provisioningstate.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

![Azure CLI 배포 프로비저닝 상태](./media/template-tutorial-create-first-template/azure-cli-provisioning-state.png)

---

> [!NOTE]
> 배포에 실패한 경우 `verbose` 스위치를 사용하여 생성 중인 리소스에 대한 정보를 가져옵니다. 디버깅에 대한 자세한 정보를 보려면 `debug` 스위치를 사용합니다.

## <a name="verify-deployment"></a>배포 확인

Azure Portal에서 리소스 그룹을 탐색하여 배포를 확인할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

1. 마지막 절차에서 리소스 그룹 배포를 선택합니다. 기본 이름은 **myResourceGroup** 입니다. 리소스 그룹 내에 배포된 리소스는 표시되지 않습니다.

1. 개요의 오른쪽 위에 배포 상태가 표시됩니다. **1 성공** 을 선택합니다.

   ![배포 상태 보기](./media/template-tutorial-create-first-template/deployment-status.png)

1. 리소스 그룹에 대한 배포 기록이 표시됩니다. **blanktemplate** 을 선택합니다.

   ![배포 선택](./media/template-tutorial-create-first-template/select-from-deployment-history.png)

1. 배포 요약이 표시됩니다. 이 경우 리소스가 배포되지 않았으므로 많은 내용이 표시되지 않습니다. 이 시리즈의 뒷부분에서는 배포 기록에서 요약을 검토하는 것이 도움이 될 수 있습니다. 왼쪽에서는 배포 중에 사용되는 입력, 출력 및 템플릿을 볼 수 있습니다.

   ![배포 요약 보기](./media/template-tutorial-create-first-template/view-deployment-summary.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 이동하는 경우에는 리소스 그룹을 삭제할 필요가 없습니다.

지금 중지하는 경우 리소스 그룹을 삭제하는 것이 좋습니다.

1. Azure Portal의 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.
2. **이름으로 필터링** 필드에서 리소스 그룹 이름을 입력합니다.
3. 해당 리소스 그룹 이름을 선택합니다.
4. 위쪽 메뉴에서 **리소스 그룹 삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

Azure에 배포할 간단한 템플릿을 만들었습니다. 다음 자습서에서는 스토리지 계정을 템플릿에 추가하고, 이를 리소스 그룹에 배포합니다.

> [!div class="nextstepaction"]
> [리소스 추가](template-tutorial-add-resource.md)
