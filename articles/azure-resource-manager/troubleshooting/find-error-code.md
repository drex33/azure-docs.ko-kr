---
title: 오류 코드 찾기
description: ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 사용하여 배포된 Azure 리소스 문제를 해결하기 위해 오류 코드를 찾는 방법을 설명합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/04/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: dc33dec3f043332b4ce5b2e7fe53b9f16d41d54f
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131846314"
---
# <a name="find-error-codes"></a>오류 코드 찾기

ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 사용하여 Azure 리소스 배포에 실패하면 오류 코드가 수신됩니다. 이 문서에서는 문제를 해결할 수 있도록 오류 코드를 찾는 방법을 설명합니다. 오류 코드에 대한 자세한 내용은 일반적인 배포 오류 를 [참조하세요.](common-deployment-errors.md)

## <a name="error-types"></a>오류 유형

배포와 관련된 세 가지 유형의 오류가 있습니다.

- **검증 오류** 는 배포가 시작되기 전에 발생하며 파일의 구문 오류로 인해 발생합니다. 편집기에서 이러한 오류를 식별할 수 있습니다.
- **배포** 명령이 실행되지만 리소스가 배포되지 않은 경우 실행 전 유효성 검사 오류가 발생합니다. 이러한 오류는 배포를 시작하지 않고 발견됩니다. 예를 들어 매개 변수 값이 잘못된 경우 실행 전 유효성 검사에서 오류가 발견됩니다.
- **배포 오류** 는 배포 프로세스 중에 발생하며 배포 진행 상황을 평가해야만 찾을 수 있습니다.

모든 오류 유형에서 배포 문제를 해결하는 데 사용하는 오류 코드를 반환합니다. 유효성 검사 및 실전 오류는 활동 로그에 표시되지만 배포 기록에는 표시되지 않습니다. 구문 오류가 있는 Bicep 파일은 JSON으로 컴파일되지 않으며 활동 로그에 표시되지 않습니다.

구문 오류를 식별하려면 최신 [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) 또는 [Azure Resource Manager](https://code.visualstudio.com) Tools 확장과 [함께 Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)사용할 수 있습니다.

## <a name="validation-errors"></a>유효성 검사 오류

배포 프로세스 중에 템플릿의 유효성이 검사되고 오류 코드가 표시됩니다. 배포를 실행하기 전에 Azure PowerShell 또는 Azure CLI 유효성 검사 테스트를 실행하여 유효성 검사 및 실행 전 오류를 식별할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

ARM 템플릿은 포털에서 배포할 수 있습니다. 템플릿에 구문 오류가 있는 경우 배포를 실행하려고 할 때 유효성 검사 오류가 표시됩니다. 포털 배포에 대한 자세한 내용은 [사용자 지정 템플릿 에서 리소스 배포를 참조하세요.](../templates/deploy-portal.md#deploy-resources-from-custom-template)

다음 예제에서는 스토리지 계정을 배포하려고 시도하며 유효성 검사 오류가 발생합니다.

:::image type="content" source="media/find-error-code/validation-error.png" alt-text="Azure Portal 유효성 검사 오류의 스크린샷.":::

메시지를 선택하여 세부 정보를 확인합니다. 템플릿에 오류 코드가 인 구문 오류가 `InvalidTemplate` 있습니다. **요약** 은 식에 닫는 괄호가 누락된 것을 보여줍니다.

:::image type="content" source="media/find-error-code/validation-details.png" alt-text="구문 오류를 보여 주는 유효성 검사 오류 메시지의 스크린샷.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

배포 전에 ARM 템플릿의 유효성을 검사하려면 [Test-AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)를 실행합니다.

```azurepowershell
Test-AzResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json
```

출력에는 템플릿 문제를 해결하고 수정하는 데 사용할 수 있는 또는 와 같은 오류 `InvalidTemplateDeployment` `AccountNameInvalid` 코드가 표시됩니다.

Bicep 파일의 경우 구문 유효성 검사 문제의 출력에 매개 변수 오류가 표시됩니다.

```Output
Test-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet.
Cannot find path '/tmp/11111111-1111-1111-1111-111111111111/main.json' because it does not exist.
```

더 많은 문제 해결 정보를 얻으려면 Bicep [빌드](../bicep/bicep-cli.md) 명령을 사용합니다. 출력에는 각 오류의 줄과 열 번호가 괄호 안에 표시되고 오류 메시지가 표시됩니다.

```bicep
bicep build main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

배포 템플릿의 유효성을 검사하는 데 사용할 수 있는 더 많은 PowerShell cmdlet이 있습니다.

- 구독 수준 배포에 대한 [Test-AzDeployment입니다.](/powershell/module/az.resources/test-azdeployment)
- [Test-AzManagementGroupDeployment](/powershell/module/az.resources/test-azmanagementgroupdeployment)
- [Test-AzTenantDeployment](/powershell/module/az.resources/test-aztenantdeployment)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

배포 전에 ARM 템플릿의 유효성을 검사하려면 [az deployment group validate 를 실행합니다.](/cli/azure/deployment/group#az_deployment_group_validate)

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file azuredeploy.json
```

출력에는 템플릿 문제를 해결하고 수정하는 데 사용할 수 있는 또는 와 같은 오류 `InvalidTemplateDeployment` `AccountNameInvalid` 코드가 표시됩니다.

Bicep 파일의 경우 출력은 각 오류의 줄과 열 번호를 괄호로 표시하고 오류 메시지를 표시합니다.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

배포 템플릿의 유효성을 검사하는 데 사용할 수 있는 더 많은 Azure CLI 명령이 있습니다.

- [az deployment sub validate](/cli/azure/deployment/sub#az_deployment_sub_validate)
- [az deployment mg validate](/cli/azure/deployment/mg#az_deployment_mg_validate)
- [az deployment tenant validate](/cli/azure/deployment/tenant#az_deployment_tenant_validate)

---

## <a name="deployment-errors"></a>배포 오류

Azure 리소스를 배포하기 위해 여러 작업이 처리됩니다. 배포 오류는 작업이 유효성 검사를 통과하지만 배포 중에 실패할 때 발생합니다. 각 배포 작업 및 리소스 그룹에 대한 각 배포에 대한 메시지를 볼 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

배포 작업에 대한 메시지를 보려면 리소스 그룹의 **활동 로그** 를 사용합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. 리소스 **그룹으로** 이동하여 배포의 리소스 그룹 이름을 선택합니다.
1. **활동 로그** 를 선택합니다.
1. 필터를 사용하여 작업의 오류 로그를 찾습니다.

    :::image type="content" source="./media/find-error-code/activity-log.png" alt-text="실패한 배포를 강조 표시하는 리소스 그룹의 활동 로그 스크린샷.":::

1. 오류 로그를 선택하여 작업의 세부 정보를 확인합니다.

    :::image type="content" source="./media/find-error-code/activity-log-details.png" alt-text="실패한 배포의 오류 메시지를 보여주는 활동 로그 세부 정보의 스크린샷.":::

배포 결과를 보려면 다음을 수행합니다.

1. 리소스 그룹으로 이동합니다.
1. **설정**  >  **배포를 선택합니다.**
1. 배포에 대한 **오류 세부 정보를** 선택합니다.

    :::image type="content" source="media/find-error-code/deployment-error-details.png" alt-text="실패한 배포에 대한 오류 세부 정보의 리소스 그룹 링크 스크린샷.":::

1. 오류 메시지 및 오류 `NoRegisteredProviderFound` 코드가 표시됩니다.

    :::image type="content" source="media/find-error-code/deployment-error-summary.png" alt-text="배포 오류 세부 정보를 보여 주는 메시지의 스크린샷.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용하여 배포의 작업 메시지를 보려면 [Get-AzResourceGroupDeploymentOperation을](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation)사용합니다.

배포에 대한 모든 작업을 표시하려면 다음을 수행합니다.

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

특정 속성 형식을 지정하려면 다음을 수행합니다.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusCode
```

배포 결과를 얻으려면 [Get-AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment)를 사용합니다.

```azurepowershell
Get-AzResourceGroupDeployment `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 있는 배포의 작업 메시지를 보려면 [az deployment operation group list 를](/cli/azure/deployment/operation/group#az_deployment_operation_group_list)사용합니다.

배포에 대한 모든 작업을 표시하려면 다음을 수행합니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties"
```

특정 속성 형식을 지정하려면 다음을 수행합니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties.statusCode"
```

배포 결과를 얻으려면 [az deployment group show 를](/cli/azure/deployment/group#az_deployment_group_show)사용합니다.

```azurecli
az deployment group show \
  --resource-group examplegroup \
  --name exampledeployment
```

---

## <a name="next-steps"></a>다음 단계

- [일반적인 배포 오류](common-deployment-errors.md)
- [디버그 로깅 활성화](enable-debug-logging.md)
- [문제 해결 템플릿 만들기](create-troubleshooting-template.md)
