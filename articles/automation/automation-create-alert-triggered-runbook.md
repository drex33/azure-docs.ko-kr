---
title: 경고를 사용하여 Azure Automation Runbook 트리거
description: 이 문서에서는 Azure 경고가 발생할 때 실행할 Runbook을 트리거하는 방법을 알아봅니다.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2584390610aaef5ddd8364d33f3c065ecf3bd321
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131443835"
---
# <a name="use-an-alert-to-trigger-an-azure-automation-runbook"></a>경고를 사용하여 Azure Automation Runbook 트리거

[Azure Monitor](../azure-monitor/overview.md)를 사용하여 대부분의 Azure 서비스에 대한 기본 수준의 메트릭과 로그를 모니터링합니다. [작업 그룹](../azure-monitor/alerts/action-groups.md)을 사용하거나 경고에 따라 작업을 자동화하기 위해 Azure Automation Runbook을 호출할 수 있습니다. 이 문서에서는 경고를 사용하여 Runbook을 구성하고 실행하는 방법을 보여 줍니다.


## <a name="prerequisites"></a>사전 요구 사항

* 사용자 할당 관리 ID가 하나 이상 있는 Azure Automation 계정. 자세한 내용은 [Azure Automation 계정에 대해 사용자 할당 관리 ID 사용](./add-user-assigned-identity.md)을 참조하세요.
* Automation 계정으로 가져온 Az 모듈 `Az.Accounts` 및 `Az.Compute`. 자세한 내용은 [Az 모듈 가져오기](./shared-resources/modules.md#import-az-modules)를 참조하세요.
* [Azure 가상 머신](../virtual-machines/windows/quick-create-powershell.md).
* 로컬 컴퓨터에 설치된 [Azure Az PowerShell 모듈](/powershell/azure/new-azureps-module-az)입니다. 설치 또는 업그레이드하려면 [Azure Az PowerShell 모듈을 설치하는 방법](/powershell/azure/install-az-ps)을 참조하세요.
* [Automation Runbook](./manage-runbooks.md)에 대해 일반적으로 잘 알고 있습니다.

## <a name="alert-types"></a>경고 유형

3가지 경고 유형으로 Automation Runbook을 사용할 수 있습니다.

* 일반적인 경고
* 활동 로그 경고
* 실시간에 가까운 메트릭 경고

> [!NOTE]
> 일반적인 경고 스키마는 Azure에서 경고 알림에 대한 사용 환경을 표준화합니다. 지금까지 Azure의 세 가지 경고 유형(메트릭, 로그 및 활동 로그)에는 고유한 이메일 템플릿, webhook 스키마 등이 있었습니다. 자세한 내용은 일반적인 경고 스키마 를 [참조하세요.](../azure-monitor/alerts/alerts-common-schema.md)

경고가 Runbook을 호출할 때 실제 호출은 웹후크에 대한 HTTP POST 요청입니다. POST 요청의 본문에는 경고와 관련된 유용한 속성을 포함하는 JSON으로 포맷된 개체가 포함되어 있습니다. 다음 표에는 각 경고 유형에 대한 페이로드 스키마 링크가 나열되어 있습니다.

|경고  |Description|페이로드 스키마  |
|---------|---------|---------|
|[일반 경고](../azure-monitor/alerts/alerts-common-schema.md)|현재 Azure에서 경고 알림에 대한 소비 환경을 표준화하는 일반 경고 스키마입니다.|일반적인 경고 페이로드 스키마입니다.|
|[활동 로그 경고](../azure-monitor/alerts/activity-log-alerts.md) |Azure 활동 로그의 새 이벤트가 특정 조건과 일치하는 경우 알림을 보냅니다. 예를 들어 `Delete VM` 작업이 **myProductionResourceGroup** 에서 발생하거나 새 Azure Service Health 이벤트가 활성 상태로 표시되는 경우입니다.| [활동 로그 경고 페이로드 스키마](../azure-monitor/alerts/activity-log-alerts-webhook.md)     |
|[근 실시간 메트릭 경고 만들기](../azure-monitor/alerts/alerts-metric-near-real-time.md) | 하나 이상의 플랫폼 수준 메트릭이 지정된 조건을 충족하는 경우 메트릭 경고보다 빠르게 알림을 보냅니다. 예를 들어 VM의 **CPU %** 값이 90보다 큰 경우 및 **네트워크 입력** 의 값이 지난 5분 동안 500MB보다 큰 경우입니다.| [근 실시간 메트릭 경고 페이로드 스키마](../azure-monitor/alerts/alerts-webhooks.md#payload-schema)          |

각 유형의 경고에서 제공되는 데이터가 다르기 때문에 각 경고 유형이 다르게 처리됩니다. 다음 섹션에서는 다양한 유형의 경고를 처리하는 Runbook을 만드는 방법에 대해 배웁니다.

## <a name="assign-permissions-to-managed-identities"></a>관리 ID에 권한 할당

적절한 [관리 ID](./automation-security-overview.md#managed-identities)에 권한을 할당하여 가상 머신을 중지할 수 있도록 허용합니다. Runbook은 Automation 계정의 시스템이 할당한 관리 ID 또는 사용자가 할당한 관리 ID를 사용할 수 있습니다. 각 ID에 권한을 할당하는 단계가 제공됩니다. 아래 단계에서는 PowerShell을 사용합니다. 포털을 사용하려면 Azure Portal [사용하여 Azure 역할 할당을](./../role-based-access-control/role-assignments-portal.md)참조하세요.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 대화형으로 Azure에 로그인하고 지침을 따릅니다.

    ```powershell
    # Sign in to your Azure subscription
    $sub = Get-AzSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Connect-AzAccount
    }
    
    # If you have multiple subscriptions, set the one to use
    # Select-AzSubscription -SubscriptionId <SUBSCRIPTIONID>
    ```

1. 아래 변수에 적절한 값을 제공한 다음, 스크립트를 실행합니다.

    ```powershell
    $resourceGroup = "resourceGroup"
    $automationAccount = "AutomationAccount"
    $userAssignedManagedIdentity = "userAssignedManagedIdentity"
    ```

1. PowerShell cmdlet [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment)를 사용하여 시스템 할당 관리 ID에 역할을 할당합니다.

    ```powershell
    $SAMI = (Get-AzAutomationAccount -ResourceGroupName $resourceGroup -Name $automationAccount).Identity.PrincipalId
    New-AzRoleAssignment `
        -ObjectId $SAMI `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. 사용자 할당 관리 ID에 역할을 할당합니다.

    ```powershell
    $UAMI = (Get-AzUserAssignedIdentity -ResourceGroupName $resourceGroup -Name $userAssignedManagedIdentity)
    New-AzRoleAssignment `
        -ObjectId $UAMI.PrincipalId `
        -ResourceGroupName $resourceGroup `
        -RoleDefinitionName "DevTest Labs User"
    ```

1. 시스템 할당 관리 ID의 경우 `ClientId` 나중에 사용할 값을 표시하고 기록합니다.

   ```powershell
   $UAMI.ClientId
   ```

## <a name="create-a-runbook-to-handle-alerts"></a>경고를 처리할 Runbook 만들기

경고와 함께 Automation을 사용하려면 Runbook에 전달되는 경고 JSON 페이로드를 관리하는 Runbook이 필요합니다. 다음 Runbook 예제는 Azure 경고에서 호출되어야 합니다.

앞 섹션에서 설명한 것처럼 각 경고 유형은 서로 다른 스키마를 사용합니다. 스크립트는 `WebhookData` Runbook 입력 매개 변수의 경고에서 웹후크 데이터를 가져옵니다. 그런 다음, 스크립트가 JSON 페이로드를 평가하여 사용 중인 경고 유형을 확인합니다.

이 예제에서는 Azure VM(가상 머신)의 경고를 사용합니다. 페이로드에서 VM 데이터를 검색한 다음 해당 정보를 사용하여 VM을 중지합니다. Runbook이 실행되는 Automation 계정에서 연결을 설정해야 합니다. 경고를 사용하여 Runbook을 트리거하는 경우 트리거되는 Runbook에서 경고 상태를 확인하는 것이 중요합니다. 경고 상태가 변경될 때마다 Runbook이 트리거됩니다. 경고에는 여러 상태가 있으며 가장 일반적인 두 가지 상태는 활성화됨 및 해결됨입니다. Runbook 논리에서 상태를 확인하여 Runbook이 두 번 이상 실행되지 않는지 확인합니다. 이 문서의 예제에서는 활성화된 상태인 경고만 찾는 방법을 보여 줍니다.

Runbook은 Automation 계정 [시스템 할당 관리 ID를](./automation-security-overview.md#managed-identities) 사용하여 Azure에서 인증하여 VM에 대한 관리 작업을 수행합니다. 이 Runbook은 사용자가 할당한 관리 ID를 사용하도록 쉽게 수정할 수 있습니다.

이 예제를 사용하여 Runbook에서 호출한 **Stop-AzureVmInResponsetoVMAlert** 을 만듭니다. PowerShell 스크립트를 수정하고 여러 다른 리소스와 함께 사용할 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고, Azure Automation 계정으로 이동합니다.

1. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.

1. **+ Runbook 만들기** 를 선택합니다.
    1. Runbook 이름을 `Stop-AzureVmInResponsetoVMAlert`로 지정합니다.
    1. **Runbook 유형** 드롭다운 목록에서 **PowerShell** 을 선택합니다.
    1. **만들기** 를 선택합니다.

1. Runbook 편집기에서 다음 코드를 붙여넣습니다.

    ```powershell
    [OutputType("PSAzureOperationResponse")]
    param
    (
        [Parameter (Mandatory=$false)]
        [object] $WebhookData
    )
    $ErrorActionPreference = "stop"
    
    if ($WebhookData)
    {
        # Get the data object from WebhookData
        $WebhookBody = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    
        # Get the info needed to identify the VM (depends on the payload schema)
        $schemaId = $WebhookBody.schemaId
        Write-Verbose "schemaId: $schemaId" -Verbose
        if ($schemaId -eq "azureMonitorCommonAlertSchema") {
            # This is the common Metric Alert schema (released March 2019)
            $Essentials = [object] ($WebhookBody.data).essentials
            # Get the first target only as this script doesn't handle multiple
            $alertTargetIdArray = (($Essentials.alertTargetIds)[0]).Split("/")
            $SubId = ($alertTargetIdArray)[2]
            $ResourceGroupName = ($alertTargetIdArray)[4]
            $ResourceType = ($alertTargetIdArray)[6] + "/" + ($alertTargetIdArray)[7]
            $ResourceName = ($alertTargetIdArray)[-1]
            $status = $Essentials.monitorCondition
        }
        elseif ($schemaId -eq "AzureMonitorMetricAlert") {
            # This is the near-real-time Metric Alert schema
            $AlertContext = [object] ($WebhookBody.data).context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq "Microsoft.Insights/activityLogs") {
            # This is the Activity Log Alert schema
            $AlertContext = [object] (($WebhookBody.data).context).activityLog
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = (($AlertContext.resourceId).Split("/"))[-1]
            $status = ($WebhookBody.data).status
        }
        elseif ($schemaId -eq $null) {
            # This is the original Metric Alert schema
            $AlertContext = [object] $WebhookBody.context
            $SubId = $AlertContext.subscriptionId
            $ResourceGroupName = $AlertContext.resourceGroupName
            $ResourceType = $AlertContext.resourceType
            $ResourceName = $AlertContext.resourceName
            $status = $WebhookBody.status
        }
        else {
            # Schema not supported
            Write-Error "The alert data schema - $schemaId - is not supported."
        }
    
        Write-Verbose "status: $status" -Verbose
        if (($status -eq "Activated") -or ($status -eq "Fired"))
        {
            Write-Verbose "resourceType: $ResourceType" -Verbose
            Write-Verbose "resourceName: $ResourceName" -Verbose
            Write-Verbose "resourceGroupName: $ResourceGroupName" -Verbose
            Write-Verbose "subscriptionId: $SubId" -Verbose
    
            # Determine code path depending on the resourceType
            if ($ResourceType -eq "Microsoft.Compute/virtualMachines")
            {
                # This is an Resource Manager VM
                Write-Verbose "This is an Resource Manager VM." -Verbose
    
                # Ensures you do not inherit an AzContext in your runbook
                Disable-AzContextAutosave -Scope Process
     
                # Connect to Azure with system-assigned managed identity
                $AzureContext = (Connect-AzAccount -Identity).context
  
                # set and store context
                $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext
    
                # Stop the Resource Manager VM
                Write-Verbose "Stopping the VM - $ResourceName - in resource group - $ResourceGroupName -" -Verbose
                Stop-AzVM -Name $ResourceName -ResourceGroupName $ResourceGroupName -DefaultProfile $AzureContext -Force
                # [OutputType(PSAzureOperationResponse")]
            }
            else {
                # ResourceType not supported
                Write-Error "$ResourceType is not a supported resource type for this runbook."
            }
        }
        else {
            # The alert status was not 'Activated' or 'Fired' so no action taken
            Write-Verbose ("No action taken. Alert status: " + $status) -Verbose
        }
    }
    else {
        # Error
        Write-Error "This runbook is meant to be started from an Azure alert webhook only."
    }
    ```

1. 시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.
    1. 줄 78에서 를 제거합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
    1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
    1. 앞에서 얻은 클라이언트 ID를 입력합니다.

1. **저장,** **게시를** 선택한 다음, 메시지가 표시되면 **예를** 선택합니다.

1. **Runbook** 페이지를 닫고 **Automation 계정** 페이지로 돌아갑니다.

## <a name="create-the-alert"></a>경고 만들기

경고는 경고에 의해 트리거되는 작업 컬렉션인 작업 그룹을 사용합니다. Runbook은 작업 그룹에서 사용할 수 있는 많은 작업 중 하나일 뿐입니다.

1. Automation 계정의 **모니터링** 아래에서 **경고를** 선택합니다.

1. **+ 새 경고 규칙을** 선택하여 경고 규칙 **만들기** 페이지를 엽니다.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-alert-rule-portal.png" alt-text="경고 규칙 만들기 페이지 및 하위 섹션입니다.":::

1. **범위** 아래에서 **리소스 편집을** 선택합니다. 

1. 리소스 **선택** 페이지의 **리소스 종류별로 필터링** 드롭다운 목록에서 **가상 머신을** 선택합니다.

1. 모니터링하려는 가상 머신 옆의 확인란을 선택합니다. 그런 **다음, 완료를** 선택하여 **경고 규칙 만들기** 페이지로 돌아갑니다.

1. **조건** 에서 **조건 추가** 를 선택합니다.

1. 신호 **선택** 페이지의 검색 `Percentage CPU` 텍스트 상자에 를 입력한 다음, 결과에서 **CPU 백분율을** 선택합니다.

1. 신호 **논리 구성** 페이지의 **임계값에서** 테스트 목적으로 초기 낮은 값(예: )을 입력합니다. `5` 경고가 예상대로 작동하는지 확인한 후 돌아가서 이 값을 업데이트할 수 있습니다. 그런 **다음, 완료를** 선택하여 **경고 규칙 만들기** 페이지로 돌아갑니다.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-signal-logic-portal.png" alt-text="CPU 백분율 임계값 입력":::
 
1. **작업에서** **작업 그룹 추가를** 선택한 **다음, +작업 그룹 만들기를** 선택합니다.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/create-action-group-portal.png" alt-text="기본 탭이 열려 있는 작업 그룹 만들기 페이지입니다.":::

1. 작업 그룹 만들기 페이지에서 **다음을 수행합니다.**
    1. 기본 **사항** 탭에서 **작업 그룹 이름과** 표시 **이름을** 입력합니다.
    1. **작업** 탭의 **이름** 텍스트 상자에 이름을 입력합니다. 그런 다음 **작업 유형** 드롭다운 목록에서 Automation **Runbook을** 선택하여 **Runbook 구성** 페이지를 엽니다.
        1. **Runbook 원본** 항목에 **대해 사용자** 를 선택합니다.  
        1. **구독** 드롭다운 목록에서 구독을 선택합니다.
        1. Automation **계정** 드롭다운 목록에서 Automation 계정을 선택합니다.
        1. **Runbook 드롭다운** 목록에서 **Stop-AzureVmInResponsetoVMAlert를** 선택합니다.
        1. 일반 **경고 스키마 사용 항목에서** **예를** 선택합니다.
        1. **확인을** 선택하여 작업 **그룹 만들기** 페이지로 돌아갑니다.
        
            :::image type="content" source="./media/automation-create-alert-triggered-runbook/configure-runbook-portal.png" alt-text="값을 가진 Runbook 페이지를 구성합니다.":::

    1. **검토 + 만들기를** 선택한 **다음, 만들기를** 선택하여 **경고 규칙 만들기** 페이지로 돌아갑니다.

1. **경고 규칙 세부 정보** 아래에서 경고 규칙 **이름** 텍스트 상자에 대해 를 선택합니다.

1. **경고 규칙 만들기** 를 선택합니다.  활동 로그 경고 및 만든 거의 실시간 [경고에서](../azure-monitor/alerts/activity-log-alerts.md) 작업 그룹을 사용할 수 [있습니다.](../azure-monitor/alerts/alerts-overview.md)

## <a name="verification"></a>확인

VM이 실행 중인지 확인합니다. **Runbook Stop-AzureVmInResponsetoVMAlert로** 이동하여 최근 **작업** 목록이 채워지도록 확인합니다. 완료된 작업이 나타나면 작업을 선택하고 출력을 검토합니다. 또한 VM이 중지되었는지 확인합니다.

   :::image type="content" source="./media/automation-create-alert-triggered-runbook/job-result-portal.png" alt-text="작업의 출력을 표시합니다.":::


## <a name="next-steps"></a>다음 단계

* Runbook을 시작하는 다양한 방법을 알아보려면 [Runbook 시작](./start-runbooks.md)을 참조하세요.
* 활동 로그 경고를 만들려면 [활동 로그 경고 만들기](../azure-monitor/alerts/activity-log-alerts.md)를 참조하세요.
* 근 실시간 경고를 만드는 방법을 보려면 [Azure Portal에서 경고 규칙 만들기](../azure-monitor/alerts/alerts-metric.md?toc=/azure/azure-monitor/toc.json)를 참조하세요.

