---
title: 문제 해결 템플릿 만들기
description: ARM 템플릿(Azure Resource Manager 템플릿) 또는 Bicep 파일을 사용하여 배포된 Azure 리소스 문제를 해결하는 템플릿을 만드는 방법을 설명합니다.
tags: top-support-issue
ms.topic: troubleshooting
ms.date: 11/02/2021
ms.openlocfilehash: 0c9b1a6ebd35a6ebe58b568a1a56e44c0395b630
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482890"
---
# <a name="create-a-troubleshooting-template"></a>문제 해결 템플릿 만들기

경우에 따라 템플릿 문제를 해결하는 가장 좋은 방법은 템플릿의 특정 부분을 격리하고 테스트하는 것입니다. 오류가 발생한다고 생각되는 리소스에 중점을 둔 문제 해결 템플릿을 만들 수 있습니다.

예를 들어 배포 템플릿이 기존 리소스를 참조할 때 오류가 발생합니다. 전체 배포 템플릿을 평가하는 대신 리소스에 대한 데이터를 반환하는 문제 해결 템플릿을 만듭니다. 출력은 올바른 매개 변수를 전달하고, 템플릿 함수를 올바르게 사용하고, 예상하는 리소스를 얻는지 여부를 찾는 데 도움이 됩니다.

## <a name="deploy-a-troubleshooting-template"></a>문제 해결 템플릿 배포

다음 ARM 템플릿 및 Bicep 파일은 기존 스토리지 계정에서 정보를 가져옵니다. [Azure PowerShell New-AzResourceGroupDeployment를](/powershell/module/az.resources/new-azresourcegroupdeployment) 사용하여 배포를 실행하거나 [az deployment group create를](/cli/azure/deployment/group#az_deployment_group_create)Azure CLI. 스토리지 계정의 이름 및 리소스 그룹을 지정합니다. 출력은 스토리지 계정의 속성 이름 및 값을 가진 개체입니다.

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
      "type": "string"
    },
    "storageResourceGroup": {
      "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
      "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2021-04-01')]",
      "type": "object"
    }
  }
}
```

Bicep에서 `existing` 키워드를 사용하고 스토리지 계정이 있는 리소스 그룹에서 배포를 실행합니다. 를 사용하여 `scope` 다른 리소스 그룹의 리소스에 액세스합니다. 자세한 내용은 [기존 리소스를 참조하세요.](../bicep/resource-declaration.md#existing-resources)

```bicep
param storageName string

resource stg 'Microsoft.Storage/storageAccounts@2021-04-01' existing = {
  name: storageName
}

output exampleOutput object = stg.properties
```

## <a name="alternate-troubleshooting-method"></a>대체 문제 해결 방법

잘못된 의존성으로 인해 배포 오류가 발생한다고 생각되는 경우 템플릿을 간소화된 템플릿으로 분리하여 테스트를 실행할 수 있습니다. 먼저 단일 리소스(예: SQL Server)를 배포하는 템플릿을 만듭니다. 리소스 배포가 올바른지 확인되면 리소스 배포에 의존하는 리소스(예: SQL Database)를 추가합니다. 이러한 두 리소스가 올바르게 정의되면 다른 종속 리소스(예: 감사 정책)를 추가합니다. 각 테스트 배포 간에 리소스 그룹을 삭제하여 해당 리소스 그룹을 적절하게 테스트하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

- [일반적인 배포 오류](common-deployment-errors.md)
- [오류 코드 찾기](find-error-code.md)
- [디버그 로깅 활성화](enable-debug-logging.md)
