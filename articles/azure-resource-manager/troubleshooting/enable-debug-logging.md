---
title: 디버그 로깅 활성화
description: 디버그 로깅을 사용 하 여 Azure Resource Manager 템플릿 (ARM 템플릿) 또는 Bicep 파일을 사용 하 여 배포 된 Azure 리소스의 문제를 해결 하는 방법을 설명 합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/05/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: fcd2cbdf052f934bb797b3f1dab148d951d09167
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131989652"
---
# <a name="enable-debug-logging"></a>디버그 로깅 활성화

배포 오류 문제를 해결 하기 위해 자세한 정보를 수집 하는 데 도움이 됩니다. Azure PowerShell를 사용 하 여 디버그 로깅을 사용 하도록 설정 합니다. 배포의 요청 및 응답에 대 한 데이터를 가져와 문제의 원인을 파악할 수 있습니다. 디버그 로깅은 Azure Resource Manager 템플릿 (ARM 템플릿) 및 Bicep 파일을 사용 하 여 작동 합니다.

## <a name="get-debug-information"></a>디버그 정보 가져오기

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment) 를 사용 하 여 `DeploymentDebugLogLevel` 매개 변수를 `All` , 또는로 설정 `ResponseContent` `RequestContent` 합니다. 디버그 로깅을 사용 하는 경우 암호와 같은 암호 또는 `listKeys` [AzResourceGroupDeploymentOperation](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation)같은 명령으로 기록할 수 있는 경고가 표시 됩니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json `
  -DeploymentDebugLogLevel All
```

출력에는 디버그 로깅이 표시 됩니다.

```Output
DeploymentDebugLogLevel : RequestContent, ResponseContent
```

배포 작업의 모든 속성을 보려면 다음을 수행 합니다.

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

또는와 같은 속성을 지정 하 여 출력을 필터링 할 수 있습니다 `StatusMessage` `StatusCode` .

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI에서 디버그 로깅을 사용 하도록 설정할 수 없지만 디버그 로깅 데이터를 검색할 수 있습니다.

[Az deployment operation group list](/cli/azure/deployment/operation/group#az_deployment_operation_group_list) 명령을 사용 하 여 배포 작업을 가져옵니다.

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

다음 명령을 사용 하 여 요청 콘텐츠를 가져옵니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

다음 명령을 사용 하 여 응답 콘텐츠를 가져옵니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

---

## <a name="nested-template"></a>중첩된 템플릿

[중첩](../templates/linked-templates.md#nested-template) 된 ARM 템플릿에 대 한 디버그 정보를 기록 하려면 [Microsoft .resources/배포](/azure/templates/microsoft.resources/deployments) 요소를 사용 `debugSetting` 합니다.

```json
{
  "type": "Microsoft.Resources/deployments",
  "apiVersion": "2020-10-01",
  "name": "nestedTemplate",
  "properties": {
    "mode": "Incremental",
    "templateLink": {
      "uri": "{template-uri}",
      "contentVersion": "1.0.0.0"
    },
    "debugSetting": {
       "detailLevel": "requestContent, responseContent"
    }
  }
}
```

Bicep는 대신 [모듈](../bicep/modules.md) `Microsoft.Resources/deployments` 을 사용 합니다. 모듈을 사용 하면 코드를 다시 사용 하 여 다른 Bicep 파일에서 Bicep 파일을 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [일반적인 배포 오류](common-deployment-errors.md)
- [오류 코드 찾기](find-error-code.md)
- [문제 해결 템플릿 만들기](create-troubleshooting-template.md)
