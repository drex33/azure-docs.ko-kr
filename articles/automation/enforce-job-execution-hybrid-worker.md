---
title: Azure Automation Hybrid Runbook Worker에서 작업 실행 적용
description: 이 문서에서는 사용자 지정 Azure Policy 정의를 사용하여 Azure Automation Hybrid Runbook Worker에서 작업 실행을 적용하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 05/24/2021
ms.topic: conceptual
ms.openlocfilehash: ced119e7835f6c7fd7bf6aea34a10ebea4af3fc5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131026282"
---
# <a name="use-azure-policy-to-enforce-job-execution-on-hybrid-runbook-worker"></a>Azure Policy를 사용하여 Hybrid Runbook Worker에서 작업 실행을 적용합니다

Hybrid Runbook Worker에서 Runbook을 시작하면 Azure PowerShell 또는 REST API를 사용하여 Azure Portal에서 시작할 때 Hybrid Runbook Worker 그룹의 이름을 지정할 수 있는 **실행** 옵션이 사용됩니다. 그룹이 지정되면 이 그룹에 있는 Worker 중 하나가 Runbook을 검색하여 실행합니다. Runbook에서 이 옵션을 지정하지 않을 경우 Azure Automation이 Azure 샌드박스에서 Runbook을 실행합니다. 

[Automation 작업 운영자](automation-role-based-access-control.md#automation-job-operator) 또는 그 상위 구성원인 조직 내의 모든 사용자는 Runbook 작업을 만들 수 있습니다. Automation 계정에서 Hybrid Runbook Worker 그룹을 대상으로 하는 Runbook 실행을 관리하려면 [Azure Policy](../governance/policy/overview.md)를 사용할 수 있습니다. 이를 통해 조직 표준을 적용하고, 지정된 사람이 Automation 작업을 제어하고 관리할 수 있으며, 모든 사용자는 Azure 샌드박스에서 Runbook을 실행할 수 없고, Hybrid Runbook Worker에서만 실행이 가능합니다. 

이 문서에는 다음의 Automation REST API 작업을 사용하여 이러한 작업을 컨트롤하는 데 도움이 되는 사용자 지정 Azure Policy 정의가 포함되어 있습니다. 특히:

* [작업 만들기](/rest/api/automation/job/create)
* [작업 일정 만들기](/rest/api/automation/jobschedule/create)
* [웹후크 만들기](/rest/api/automation/webhook/createorupdate)

이 정책은 `runOn` 속성에 기반합니다. 이 정책은 속성 값의 유효성을 검사함, 여기에는 기존 Hybrid Runbook Worker 그룹의 이름이 포함되어야 합니다. 값이 Null인 경우에는 작업, 작업 일정 또는 웹후크에 대한 만들기 요청이 Azure 샌드박스에서 사용될 목적이며, 요청이 거부되었다고 해석됩니다.

## <a name="permissions-required"></a>필요한 사용 권한

Azure Policy 리소스 권한을 얻으려면 구독 수준에서 [소유자](../role-based-access-control/built-in-roles.md#owner) 역할 구성원이어야 합니다.

## <a name="create-and-assign-the-policy-definition"></a>정책 정의 만들기 및 할당

여기서는 정책 규칙을 작성한 다음 관리 그룹 또는 구독에 할당하고 필요에 따라 구독에 리소스 그룹을 지정 합니다. 아직 정책 언어에 익숙하지 않은 경우 [정책 정의 구조](../governance/policy/concepts/definition-structure.md)에서 정책 정의를 구성하는 방법을 알아보세요.

1. 다음 JSON 코드 조각을 사용하여 AuditAutomationHRWJobExecution.json이라는 이름의 JSON 파일을 만듭니다.

    ```json
    {
        "properties": {
            "displayName": "Enforce job execution on Automation Hybrid Runbook Worker",
            "description": "Enforce job execution on Hybrid Runbook Workers in your Automation account.",
            "mode": "all",
            "parameters": {
                "effectType": {
                    "type": "string",
                    "defaultValue": "Deny",
                    "allowedValues": [
                       "Deny",
                       "Disabled"
                    ],
                    "metadata": {
                        "displayName": "Effect",
                        "description": "Enable or disable execution of the policy"
                    }
                }
            },
            "policyRule": {
          "if": {
            "anyOf": [
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobs"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/webhooks"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/webhooks/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobSchedules"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobSchedules/runOn'))]",
                    "less": 1
                  }
                ]
              }
            ]
          },
          "then": {
            "effect": "[parameters('effectType')]"
          }
        }
      }
    }
    ```

2. 다음 Azure PowerShell 또는 Azure CLI 명령을 실행하여 AuditAutomationHRWJobExecution.json 파일을 사용하여 정책 정의를 만듭니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy definition create --name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' --display-name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' --description 'This policy enforces job execution on Automation account user Hybrid Runbook Workers.' --rules 'AuditAutomationHRWJobExecution.json' --mode All
    ```

    이 명령은 **Automation Hybrid Runbook Worker에 대한 작업 적용 감사** 라는 이름의 정책 정의를 만듭니다. 사용할 수 있는 다른 매개 변수에 대한 자세한 내용은 [az policy definition create](/cli/azure/policy/definition#az_policy_definition_create)를 참조하세요.

    위치 매개 변수 없이 호출할 경우 `az policy definition create`은 기본적으로 선택한 세션 컨텍스트 구독에 정책 정의를 저장하도록 지정됩니다. 정의를 다른 위치에 저장하려면 다음 매개 변수를 사용합니다.

    * **subscription** - 다른 구독에 저장합니다. 구독 ID에 대해 *GUID* 값 또는 구독 이름에 대해 *문자열* 값이 필요합니다.
    * **management-group** - 관리 그룹에 저장합니다. *문자열* 값이 필요합니다.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -DisplayName 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' -Policy 'AuditAutomationHRWJobExecution.json'
    ```

    이 명령은 **Automation Hybrid Runbook Worker에 대한 작업 적용 감사** 라는 이름의 정책 정의를 만듭니다. 사용할 수 있는 다른 매개 변수에 대한 자세한 내용은 [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition)을 참조하세요.

    위치 매개 변수 없이 호출할 경우 `New-AzPolicyDefinition`은 기본적으로 선택한 세션 컨텍스트 구독에 정책 정의를 저장하도록 지정됩니다. 정의를 다른 위치에 저장하려면 다음 매개 변수를 사용합니다.

    * **SubscriptionId** - 다른 구독에 저장합니다. *GUID* 값이 필요합니다.
    * **ManagementGroupName** - 관리 그룹에 저장합니다. *문자열* 값이 필요합니다.

    ---

3. 정책 정의를 만든 후 다음 명령을 실행하여 정책 할당을 만들 수 있습니다.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
    ```

    `az policy assignment create`의 **scope** 매개 변수는 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 사용하여 작동합니다. 매개 변수는 전체 리소스 경로를 사용합니다. 각 컨테이너에 대한 **scope** 패턴은 다음과 같습니다. `{rName}`, `{rgName}`, `{subId}` 및 `{mgName}`을 각각 리소스 이름, 리소스 그룹 이름, 구독 ID 및 관리 그룹 이름으로 바꿉니다. `{rType}`은 리소스의 **리소스 종류** 로 바꿉니다(예: VM에 대한 `Microsoft.Compute/virtualMachines`).

    - 리소스 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - 리소스 그룹 - `/subscriptions/{subID}/resourceGroups/{rgName}`
    - 구독 - `/subscriptions/{subID}`
    - 관리 그룹 - `/providers/Microsoft.Management/managementGroups/{mgName}`

    다음 명령으로 PowerShell을 사용하여 Azure Policy 정의 ID를 가져올 수 있습니다.
    
    ```azurecli
    az policy definition show --name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers'
    ```
    
    만든 정책 정의에 대한 정책 정의 ID는 다음 예제와 유사합니다.
    
    ```output
    "/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Enforce Jobs on Automation Hybrid Runbook Workers"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $rgName = Get-AzResourceGroup -Name 'ContosoRG'
    $Policy = Get-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers'
    New-AzPolicyAssignment -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -PolicyDefinition $Policy -Scope $rg.ResourceId
    ```

    _ContosoRG_ 를 원하는 리소스 그룹의 이름으로 바꿉니다.

    `New-AzPolicyAssignment`의 **Scope** 매개 변수는 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 사용하여 작동합니다. 이 매개 변수는 `Get-AzResourceGroup`의 **ResourceId** 속성이 반환하는 전체 리소스 경로를 사용합니다. 각 컨테이너에 대한 **Scope** 패턴은 다음과 같습니다. `{rName}`, `{rgName}`, `{subId}` 및 `{mgName}`을 각각 리소스 이름, 리소스 그룹 이름, 구독 ID 및 관리 그룹 이름으로 바꿉니다.
    `{rType}`은 리소스의 **리소스 종류** 로 바꿉니다(예: VM에 대한 `Microsoft.Compute/virtualMachines`).

    - 리소스 - `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - 리소스 그룹 - `/subscriptions/{subId}/resourceGroups/{rgName}`
    - 구독 - `/subscriptions/{subId}`
    - 관리 그룹 - `/providers/Microsoft.Management/managementGroups/{mgName}`

    ---

4. [Azure Portal](https://portal.azure.com)에 로그인합니다.
5. **모든 서비스** 를 선택한 후 **정책** 을 검색하고 선택하여 Azure Portal에서 Azure Policy 서비스를 시작합니다.
6. 페이지 왼쪽에서 **준수** 를 선택합니다. 생성한 정책 할당으로 이동합니다.

   :::image type="content" source="./media/enforce-job-execution-hybrid-worker/azure-policy-dashboard-policy-status.png" alt-text="Azure Policy 대시보드의 스크린샷":::

Automation REST 작업 중 하나가 요청 본문의 Hybrid Runbook Worker 참조 없이 실행되는 경우 403 응답 코드가 Azure 샌드박스에서 작업을 실행하려고 했음을 나타내는 다음 예제와 유사한 오류와 함께 반환됩니다.

```rest
{
  "error": {
    "code": "RequestDisallowedByPolicy",
    "target": "Start_VMS",
    "message": "Resource 'Start_VMS' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917\"},\"policyDefinition\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc\"}}]'.",
    "additionalInfo": [
      {
        "type": "PolicyViolation",
        "info": {
          "policyDefinitionDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "evaluationDetails": {
            "evaluatedExpressions": [
              {
                "result": "True",
                "expressionKind": "Field",
                "expression": "type",
                "path": "type",
                "expressionValue": "Microsoft.Automation/automationAccounts/jobs",
                "targetValue": "Microsoft.Automation/automationAccounts/jobs",
                "operator": "Equals"
              },
              {
                "result": "True",
                "expressionKind": "Value",
                "expression": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                "expressionValue": 0,
                "targetValue": 1,
                "operator": "Less"
              }
            ]
          },
          "policyDefinitionId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionName": "4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionEffect": "Deny",
          "policyAssignmentId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917",
          "policyAssignmentName": "fd5e2cb3842d4eefbc857917",
          "policyAssignmentDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "policyAssignmentScope": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG",
          "policyAssignmentParameters": {}
        }
      }
    ]
  }
}
```

시도된 작업은 다음 예제와 유사하게 Automation 계정의 활동 로그에도 기록됩니다.

:::image type="content" source="./media/enforce-job-execution-hybrid-worker/failed-job-activity-log-example.png" alt-text="실패한 작업 실행에 대한 활동 로그의 예시":::

## <a name="next-steps"></a>다음 단계

Runbook을 사용하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.