---
title: 손상된 Azure Automanage 계정 복구
description: Automanage 계정을 포함하는 구독을 최근에 새 테넌트로 이동한 경우 다시 구성해야 합니다. 이 문서에서는 다음을 수행하는 방법을 알아봅니다.
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 11/05/2020
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 2ce8bc5c26eb99ed19578a036a4569829bc44879
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458305"
---
# <a name="repair-an-automanage-account"></a>Automanage 계정 복구하기
[Azure Automanage 계정](./automanage-virtual-machines.md#automanage-account)은 자동화된 작업이 수행되는 ID 또는 보안 컨텍스트입니다. Automanage 계정을 포함하는 구독을 최근에 새 테넌트로 이동한 경우 계정을 다시 구성해야 합니다. 다시 구성하려면 ID 유형을 다시 설정하고 계정에 적절한 역할을 할당해야 합니다.

## <a name="step-1-reset-the-automanage-account-identity-type"></a>1단계: Automanage 계정 ID 유형 초기화
다음 ARM(Azure Resource Manager) 템플릿을 사용하여 자동 관리 계정 ID 유형을 다시 설정합니다. 파일을 armdeploy.json이나 비슷한 이름으로 로컬에 저장합니다. Automanage 계정 이름과 위치는 ARM 템플릿에서 필수 매개 변수이므로 적어 둡니다.

1. 다음 템플릿을 사용하여 Resource Manager 배포를 만듭니다. `identityType = None`을 사용하세요.
    * `az deployment sub create`를 사용하여 Azure CLI에서 배포를 만들 수 있습니다. 자세한 내용은 [az deployment sub](/cli/azure/deployment/sub)를 참조하세요.
    * `New-AzDeployment` 모듈을 사용하여 PowerShell에서 배포를 만들 수 있습니다. 자세한 내용은 [New-AzDeployment](/powershell/module/az.resources/new-azdeployment)를 참조하세요.

1. `identityType = SystemAssigned`로 다시 동일한 ARM 템플릿을 실행합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "identityType": {
            "type": "string",
            "allowedValues": [ "None", "SystemAssigned" ]
        }
    },
    "resources": [
        {
            "apiVersion": "2020-06-30-preview",
            "name": "[parameters('accountName')]",
            "location": "[parameters('location')]",
            "type": "Microsoft.Automanage/accounts",
            "identity": {
                "type": "[parameters('identityType')]"
            }
        }
    ]
}

```

## <a name="step-2-assign-appropriate-roles-for-the-automanage-account"></a>2단계: Automanage 계정에 적절한 역할 할당
Automanage 계정에는 Automanage에서 관리하는 VM을 포함하는 구독에 대한 기여자 및 리소스 정책 기여자 역할이 필요합니다. Azure Portal, ARM 템플릿, Azure CLI를 사용하여 이러한 역할을 할당할 수 있습니다.

ARM 템플릿이나 Azure CLI를 사용하는 경우 Automanage 계정의 보안 주체 ID(개체 ID 라고도 함)가 필요합니다. (Azure Portal를 사용하는 경우 ID가 필요하지 않습니다.) 이러한 방법을 사용하여 이 ID를 찾을 수 있습니다.

- [Azure CLI](/cli/azure/ad/sp): `az ad sp list --display-name <name of your Automanage Account>` 명령을 사용합니다.

- Azure Portal: **Azure Active Directory** 로 이동하여 이름으로 Automanage 계정을 검색합니다. 표시되는 경우 **엔터프라이즈 애플리케이션** 에서 Automanage 계정 이름을 선택합니다.

### <a name="azure-portal"></a>Azure portal
1. **구독** 에서 자동 관리되는 VM이 포함된 구독으로 이동합니다.
1. **액세스 제어(IAM)** 로 이동합니다.
1. **역할 할당 추가** 를 선택합니다.
1. **기여자** 역할을 선택하고 Automanage 계정의 이름을 입력합니다.
1. **저장** 을 선택합니다.
1. 3~5 단계를 반복하고 이번에는 **리소스 정책 기여자** 역할을 사용합니다.

### <a name="arm-template"></a>ARM 템플릿
다음 ARM 템플릿을 실행합니다. Automanage 계정의 보안 주체 ID가 필요합니다. 이를 가져오는 단계는 이 섹션의 시작 부분에 있습니다. 메시지가 나타나면 ID를 입력합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "principalId": {
            "type": "string",
            "metadata": {
                "description": "The principal to assign the role to"
            }
        }
    },
    "variables": {
        "Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'b24988ac-6180-42a0-ab88-20f7382dd24c')]",
        "Resource Policy Contributor": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', '36243c78-bf99-498c-9df9-86d9f8d28608')]"
    },
    "resources": [
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        },
        {
            "type": "Microsoft.Authorization/roleAssignments",
            "apiVersion": "2018-09-01-preview",
            "name": "[guid(uniqueString(variables('Resource Policy Contributor')))]",
            "properties": {
                "roleDefinitionId": "[variables('Resource Policy Contributor')]",
                "principalId": "[parameters('principalId')]"
            }
        }
    ]
}
```

### <a name="azure-cli"></a>Azure CLI
다음 명령을 실행하세요.

```azurecli
az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Contributor" --scope /subscriptions/<your subscription ID>

az role assignment create --assignee-object-id <your Automanage Account Object ID> --role "Resource Policy Contributor" --scope /subscriptions/<your subscription ID>
```

## <a name="next-steps"></a>다음 단계
[Azure Automation에 대한 자세한 정보](./automanage-virtual-machines.md)
