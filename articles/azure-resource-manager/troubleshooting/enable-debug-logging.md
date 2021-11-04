---
title: 디버그 로깅 활성화
description: 디버그 로깅을 사용하도록 설정하여 ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 사용하여 배포된 Azure 리소스 문제를 해결하는 방법을 설명합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: d68041953979b594c83059a28a78e3440ca297ac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483083"
---
# <a name="enable-debug-logging"></a>디버그 로깅 활성화

배포 오류를 해결하려면 자세한 정보를 수집하는 것이 도움이 됩니다. Azure PowerShell 사용하여 디버그 로깅을 사용하도록 설정합니다. 문제의 원인을 알아보기 위해 배포의 요청 및 응답에 대한 데이터를 얻을 수 있습니다. 디버그 로깅은 arm 템플릿(Azure Resource Manager 템플릿) 및 Bicep 파일에서 작동합니다.

## <a name="get-debug-information"></a>디버그 정보 얻기

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[New-AzResourceGroupDeployment를](/powershell/module/az.resources/new-azresourcegroupdeployment) 사용하여 `DeploymentDebugLogLevel` 매개 변수를 , 또는 로 `All` `ResponseContent` `RequestContent` 설정합니다. 디버그 로깅을 사용하도록 설정하면 암호와 같은 비밀이 `listKeys` 표시되거나 [Get-AzResourceGroupDeploymentOperation과](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation)같은 명령을 통해 기록될 수 있습니다.

```azurepowershell
New-AzResourceGroupDeployment `
  -Name exampledeployment `
  -ResourceGroupName examplegroup `
  -TemplateFile azuredeploy.json `
  -DeploymentDebugLogLevel All
```

출력에는 디버그 로깅이 표시됩니다.

```Output
DeploymentDebugLogLevel : RequestContent, ResponseContent
```

배포 작업의 모든 속성을 보려면 다음을 수행합니다.

```azurepowershell
Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup
```

속성을 지정할 수 있습니다. 예를 들어 `StatusMessage` 속성은 Azure CLI 속성과 동일한 데이터를 `response` 출력합니다.

```azurepowershell
(Get-AzResourceGroupDeploymentOperation `
  -DeploymentName exampledeployment `
  -ResourceGroupName examplegroup).StatusMessage
```

Azure CLI 사용하여 디버그 `request` 및 정보를 얻습니다. `response` Az 모듈 버전 4.8 이상에서는 `Get-AzResourceGroupDeploymentOperation` 출력에 이러한 속성이 포함되지 않습니다. 사용 가능한 속성 목록은 [출력을 참조하세요.](/powershell/module/az.resources/get-azresourcegroupdeploymentoperation#outputs)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI 디버그 로깅을 사용하도록 설정할 수는 없지만 디버그 로깅 데이터를 검색할 수 있습니다.

다음 명령을 통해 배포 작업을 얻습니다.

```azurecli
az deployment operation group list \
  --resource-group examplegroup \
  --name exampledeployment
```

다음 명령을 통해 요청 콘텐츠를 얻습니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.request
```

다음 명령을 통해 응답 콘텐츠를 얻습니다.

```azurecli
az deployment operation group list \
  --name exampledeployment \
  --resource-group examplegroup \
  --query [].properties.response
```

---

## <a name="nested-template"></a>중첩된 템플릿

[중첩된](../templates/linked-templates.md#nested-template) ARM 템플릿에 대한 디버그 정보를 기록하려면 [Microsoft.Resources/deployments](/azure/templates/microsoft.resources/deployments) `debugSetting` 요소를 사용합니다.

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

Bicep은 대신 [모듈을](../bicep/modules.md) `Microsoft.Resources/deployments` 사용합니다. 모듈을 사용하면 코드를 다시 사용하여 다른 Bicep 파일에서 Bicep 파일을 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [일반적인 배포 오류](common-deployment-errors.md)
- [오류 코드 찾기](find-error-code.md)
- [문제 해결 템플릿 만들기](create-troubleshooting-template.md)
