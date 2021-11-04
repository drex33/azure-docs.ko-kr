---
title: 오류 코드 찾기
description: Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 배포 된 Azure 리소스 문제를 해결 하는 오류 코드를 찾는 방법을 설명 합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 8343dd5523c57a172dda53a8ad2d758825cccc28
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482913"
---
# <a name="find-error-codes"></a>오류 코드 찾기

Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 Azure 리소스를 배포 하지 못한 경우 오류 코드를 수신 합니다. 이 문서에서는 문제를 해결할 수 있도록 오류 코드를 찾는 방법을 설명 합니다. 오류 코드에 대 한 자세한 내용은 [일반적인 배포 오류](common-deployment-errors.md)를 참조 하세요.

## <a name="error-types"></a>오류 유형

다음과 같은 세 가지 유형의 오류를 받을 수 있습니다.

- **유효성 검사 오류** 는 배포가 시작 되기 전에 발생 하며 구문 오류로 인해 발생 합니다. 유효성 검사 오류를 확인 하려면 최신 [Bicep 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-bicep) 또는 [Azure Resource Manager 도구 확장과](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)함께 [Visual Studio Code](https://code.visualstudio.com) 를 사용 합니다.
- 실행 **전 유효성 검사 오류** 는 배포가 시작 되었지만 리소스가 배포 되지 않은 경우에 발생 합니다. 예를 들어 잘못 된 매개 변수 값 또는 잘못 된 리소스 이름입니다.
- 배포 **오류가** 발생 하는 이유는 배포 중에 발생 합니다.

유효성 검사 및 실행 전 오류 코드는 리소스 그룹의 활동 로그 및 구독의 [활동 로그](../../azure-monitor/essentials/activity-log.md)에 보고 됩니다. 예외는 편집기 또는 배포 명령의 출력에만 표시 되는 Bicep 구문 유효성 검사입니다. 배포 오류 코드는 리소스 그룹의 배포 기록 및 활동 로그에 표시 됩니다.

## <a name="validation-errors"></a>유효성 검사 오류

배포 과정에서 템플릿의 유효성을 검사 하 고 오류 코드를 표시 합니다. 배포를 실행 하기 전에 Azure PowerShell 또는 Azure CLI를 사용 하 여 유효성 검사 테스트를 실행할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

ARM 템플릿을 포털에서 배포할 수 있습니다. 템플릿에 구문 오류가 있으면 배포를 실행 하려고 할 때 유효성 검사 오류가 표시 됩니다. 포털 배포에 대 한 자세한 내용은 [사용자 지정 템플릿에서 리소스 배포](../templates/deploy-portal.md#deploy-resources-from-custom-template)를 참조 하세요.

다음 예에서는 저장소 계정을 배포 하려고 시도 하 고 유효성 검사 오류가 발생 합니다.

:::image type="content" source="media/find-error-code/validation-error.png" alt-text="Azure Portal 유효성 검사 오류의 스크린샷":::

메시지를 선택하여 세부 정보를 확인합니다. 템플릿에 구문 오류가 있습니다. 오류 코드는 오류 코드 `InvalidTemplate` 입니다. **요약** 은 식에 닫는 괄호가 누락 된 것을 보여 줍니다.

:::image type="content" source="media/find-error-code/validation-details.png" alt-text="구문 오류를 표시 하는 유효성 검사 오류 메시지의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

배포 전에 ARM 템플릿의 유효성을 검사 하려면 [AzResourceGroupDeployment](/powershell/module/az.resources/test-azresourcegroupdeployment)를 실행 합니다. 배포를 실행할 때 동일한 오류가 표시 됩니다.

```azurepowershell
Test-AzResourceGroupDeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json
```

출력에는 `InvalidTemplateDeployment` `AccountNameInvalid` 템플릿 문제를 해결 하 고 해결 하는 데 사용할 수 있는 또는와 같은 오류 코드가 표시 됩니다.

Bicep 파일의 경우 구문 유효성 검사 문제에 대 한 출력은 매개 변수 오류를 표시 합니다.

```Output
Test-AzResourceGroupDeployment: Cannot retrieve the dynamic parameters for the cmdlet.
Cannot find path '/tmp/11111111-1111-1111-1111-111111111111/main.json' because it does not exist.
```

더 많은 문제 해결 정보를 보려면 Bicep [build](../bicep/bicep-cli.md) 명령을 사용 합니다. 출력은 각 오류의 줄 및 열 번호를 괄호 안에 표시 하 고 오류 메시지를 표시 합니다.

```bicep
bicep build main.bicep
```

```Output
/azuredeploy.bicep(22,51) : Error BCP064: Found unexpected tokens in interpolated expression.
/azuredeploy.bicep(22,51) : Error BCP004: The string at this location is not terminated due to an
  unexpected new line character.
```

배포 템플릿의 유효성을 검사 하는 데 사용할 수 있는 PowerShell cmdlet이 더 있습니다.

- 구독 수준 배포에 대 한 [AzDeployment](/powershell/module/az.resources/test-azdeployment) 입니다.
- [Test-AzManagementGroupDeployment](/powershell/module/az.resources/test-azmanagementgroupdeployment)
- [Test-AzTenantDeployment](/powershell/module/az.resources/test-aztenantdeployment)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

배포 전에 ARM 템플릿의 유효성을 검사 하려면 [az deployment group validate](/cli/azure/deployment/group#az_deployment_group_validate)를 실행 합니다. 배포를 실행할 때 동일한 오류가 표시 됩니다.

```azurecli
az deployment group validate \
  --resource-group examplegroup \
  --template-file azuredeploy.json
```

출력에는 `InvalidTemplateDeployment` `AccountNameInvalid` 템플릿 문제를 해결 하 고 해결 하는 데 사용할 수 있는 또는와 같은 오류 코드가 표시 됩니다.

Bicep 파일의 경우 출력은 각 오류의 줄 및 열 번호를 괄호 안에 표시 하 고 오류 메시지를 표시 합니다.

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

배포 템플릿의 유효성을 검사 하는 데 사용할 수 있는 더 많은 Azure CLI 명령이 있습니다.

- [az deployment sub validate](/cli/azure/deployment/sub#az_deployment_sub_validate)
- [az deployment mg validate](/cli/azure/deployment/mg#az_deployment_mg_validate)
- [az deployment tenant validate](/cli/azure/deployment/tenant#az_deployment_tenant_validate)

---

## <a name="deployment-errors"></a>배포 오류

Azure 리소스를 배포 하기 위해 몇 가지 작업이 처리 됩니다. 배포 오류는 작업이 유효성 검사를 통과 했지만 배포 중에 실패 하는 경우에 발생 합니다. 각 배포 작업 및 리소스 그룹에 대 한 각 배포에 대 한 메시지를 볼 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

배포 작업에 대 한 메시지를 보려면 리소스 그룹의 **활동 로그** 를 사용 합니다.

1. [Azure 포털](https://portal.azure.com)에 로그인합니다.
1. **리소스 그룹** 으로 이동 하 여 배포의 리소스 그룹 이름을 선택 합니다.
1. **활동 로그** 를 선택합니다.
1. 필터를 사용 하 여 작업의 오류 로그를 찾습니다.

    :::image type="content" source="./media/find-error-code/activity-log.png" alt-text="실패 한 배포를 강조 표시 하는 리소스 그룹 활동 로그의 스크린샷":::

1. 오류 로그를 선택 하 여 작업의 세부 정보를 확인 합니다.

    :::image type="content" source="./media/find-error-code/activity-log-details.png" alt-text="실패 한 배포의 오류 메시지를 보여 주는 활동 로그 정보의 스크린샷":::

배포 결과를 보려면 다음을 수행 합니다.

1. 리소스 그룹으로 이동합니다.
1. **설정**  >  **배포** 를 선택 합니다.
1. 배포에 대 한 **오류 세부 정보** 를 선택 합니다.

    :::image type="content" source="media/find-error-code/deployment-error-details.png" alt-text="실패 한 배포에 대 한 오류 세부 정보에 대 한 리소스 그룹 링크의 스크린샷":::

1. 오류 메시지와 오류 코드가 `NoRegisteredProviderFound` 표시 됩니다.

    :::image type="content" source="media/find-error-code/deployment-error-summary.png" alt-text="배포 오류 세부 정보를 표시 하는 메시지의 스크린샷":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell을 사용 하 여 배포의 작업 메시지를 보려면 [AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation)를 사용 합니다.

배포에 대 한 모든 작업을 표시 하려면 다음을 수행 합니다.

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

특정 속성 유형을 지정 하려면 다음을 수행 합니다.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusCode
```

배포의 결과를 얻으려면 [AzResourceGroupDeployment](/powershell/module/az.resources/get-azresourcegroupdeployment)를 사용 합니다.

```azurepowershell
Get-AzResourceGroupDeployment `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI를 사용 하 여 배포의 작업 메시지를 보려면 [az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list)를 사용 합니다.

배포에 대 한 모든 작업을 표시 하려면 다음을 수행 합니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties"
```

특정 속성 유형을 지정 하려면 다음을 수행 합니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query "[*].properties.statusCode"
```

배포의 결과를 가져오려면 [az deployment group show](/cli/azure/deployment/group#az_deployment_group_show)를 사용 합니다.

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
