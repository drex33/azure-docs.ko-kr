---
title: 세션 호스트 Azure Automation 크기 조정 - Azure
description: Azure Automation을 사용하여 Azure Virtual Desktop 세션 호스트 크기를 자동으로 조정하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/09/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: ac3caf51337b87125f10cc7a6cd1378d7bfdb923
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/29/2021
ms.locfileid: "113005315"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Azure Automation을 사용하여 세션 호스트 크기 조정

VM(가상 머신)을 크기 조정하여 총 Azure Virtual Desktop 배포 비용을 줄일 수 있습니다. VM을 크기 조정한다는 것은 사용량이 적은 시간에는 세션 호스트 VM을 종료 및 할당 취소하고 사용량이 많은 시간에는 다시 켜서 할당하는 것을 의미합니다.

이 문서에서는 Azure Automation 계정을 사용하여 빌드된 크기 조정 도구와 Azure Virtual Desktop 환경에서 세션 호스트 VM을 자동으로 크기 조정하는 Azure Logic App에 대해 알아봅니다. 크기 조정 도구를 사용하는 방법을 알아보려면 [사전 요구 사항](#prerequisites)으로 건너뛰세요.

## <a name="how-the-scaling-tool-works"></a>크기 조정 도구의 작동 방식

크기 조정 도구는 세션 호스트 VM 비용을 최적화하려는 고객을 위해 저비용 자동화 옵션을 제공합니다.

크기 조정 도구를 사용하여 다음을 수행할 수 있습니다.

- 사용량이 많은 시간과 적은 시간을 기준으로 VM이 시작 및 중지되도록 예약합니다.
- CPU 코어당 세션 수를 기준으로 VM을 스케일 아웃합니다.
- 사용량이 적은 시간에는 VM을 스케일 인하여 실행 중인 세션 호스트 VM의 수를 최소로 유지합니다.

크기 조정 도구는 Azure Automation 계정, PowerShell Runbook, 웹후크 및 Azure Logic App의 조합을 사용하여 작동합니다. 도구가 실행되면 Azure Logic App이 웹후크를 호출하여 Azure Automation Runbook을 시작합니다. 그런 다음 Runbook이 작업을 만듭니다.

사용량이 많은 시간에는 이 작업이 각 호스트 풀에 대해 현재 세션 수와 현재 실행 중인 세션 호스트의 VM 용량을 확인합니다. 이 정보를 사용하여 실행 중인 세션 호스트 VM이 **CreateOrUpdateAzLogicApp.ps1** 파일에 대해 정의된 *SessionThresholdPerCPU* 매개 변수를 기준으로 기존 세션을 지원할 수 있는지 여부를 계산합니다. 세션 호스트 VM이 기존 세션을 지원할 수 없는 경우 작업은 호스트 풀에서 추가 세션 호스트 VM을 시작합니다.

>[!NOTE]
>*SessionThresholdPerCPU* 는 VM의 세션 수를 제한하지 않습니다. 이 매개 변수는 연결의 부하를 분산하기 위해 새 VM을 시작해야 하는 시점만 결정합니다. 세션 수를 제한하려면 [Update-AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing) 지침에 따라 *MaxSessionLimit* 매개 변수를 적절하게 구성해야 합니다.

사용량이 적은 시간에는 작업이 *MinimumNumberOfRDSH* 매개 변수를 기준으로 몇 개의 세션 호스트 VM을 종료해야 하는지 결정합니다. *LimitSecondsToForceLogOffUser* 매개 변수를 0이 아닌 양수 값으로 설정하는 경우 작업은 세션 호스트 VM을 드레이닝 모드로 설정하여 새 세션이 호스트에 연결되지 않도록 합니다. 그러면 작업은 현재 로그인한 사용자에게 작업을 저장하라고 알리고 구성된 시간 동안 기다린 다음, 사용자를 강제로 로그아웃시킵니다. 세션 호스트 VM의 모든 사용자 세션이 로그아웃되면 작업이 VM을 종료합니다. VM이 종료된 후 작업은 세션 호스트 드레이닝 모드를 다시 설정합니다.

>[!NOTE]
>세션 호스트 VM을 드레이닝 모드로 수동으로 설정하면 작업에서 세션 호스트 VM을 관리하지 않습니다. 세션 호스트 VM이 실행 중이고 드레이닝 모드로 설정된 경우 사용할 수 없는 것으로 처리됩니다. 그러면 작업에서 부하를 처리하기 위해 추가 VM을 시작하게 됩니다. 수동으로 드레이닝 모드로 설정하기 전에 Azure VM에 태그를 지정하는 것이 좋습니다. 나중에 Azure Logic App Scheduler를 만들 때 *MaintenanceTagName* 매개 변수를 사용하여 태그의 이름을 지정할 수 있습니다. 태그는 이러한 VM을 크기 조정 도구에서 관리하는 것과 구별하는 데 도움이 됩니다. 유지 관리 태그를 설정하면 사용자가 태그를 제거할 때까지 크기 조정 도구가 VM을 변경하지 못하게 됩니다.

*LimitSecondsToForceLogOffUser* 매개 변수를 0으로 설정할 경우 작업은 지정된 그룹 정책의 세션 구성 설정이 사용자 세션 로그아웃을 처리하도록 허용합니다. 이러한 그룹 정책을 보려면 **컴퓨터 구성** > **정책** > **관리 템플릿** > **Windows 구성 요소** > **원격 데스크톱 서비스** > **원격 데스크톱 세션 호스트** > **세션 시간 제한** 으로 이동합니다. 세션 호스트 VM에 활성 세션이 있으면 작업이 세션 호스트 VM을 실행 상태로 유지합니다. 활성 세션이 없으면 작업이 세션 호스트 VM을 종료합니다.

언제든지 작업은 호스트 풀의 *MaxSessionLimit* 을 고려하여 현재 세션 수가 최대 용량 중 90%를 초과하는지 확인합니다. 초과하는 경우 작업에서 추가 세션 호스트 VM을 시작합니다.

작업은 설정된 되풀이 간격에 따라 주기적으로 실행됩니다. Azure Virtual Desktop 환경의 크기에 따라 이 간격을 변경할 수 있지만, VM을 시작하고 종료하는 데 다소 시간이 걸릴 수 있으므로 지연 시간을 고려해야 합니다. 되풀이 간격은 15분으로 설정하는 것이 좋습니다.

단, 이 도구에는 다음과 같은 제한 사항도 있습니다.

- 이 솔루션은 풀링된 다중 세션 호스트 VM에만 적용됩니다.
- 이 솔루션은 모든 지역의 VM을 관리하지만 Azure Automation 계정 및 Azure Logic App과 동일한 구독에서만 사용할 수 있습니다.
- Runbook의 최대 작업 런타임은 3시간입니다. 호스트 풀에서 VM을 시작하거나 중지하는 데 시간이 더 오래 걸리면 작업이 실패합니다. 자세한 내용은 [공유 리소스](../automation/automation-runbook-execution.md#fair-share)를 참조하세요.
- 크기 조정 알고리즘이 제대로 작동하려면 하나 이상의 VM 또는 세션 호스트를 설정해야 합니다.
- 크기 조정 도구는 CPU 또는 메모리에 따라 크기 조정을 지원하지 않습니다.
- 크기 조정은 호스트 풀의 기존 호스트로만 작동합니다. 크기 조정 도구는 새로운 세션 호스트의 크기 조정을 지원하지 않습니다.

>[!NOTE]
>크기 조정 도구는 도구가 크기를 조정하는 대상 호스트 풀의 부하 분산 모드를 제어합니다. 도구는 사용량이 많은 시간과 적은 시간 모두에 대해 폭 우선 부하 분산 모드를 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

크기 조정 도구를 설정하기 전에 먼저 다음을 준비해야 합니다.

- [Azure Virtual Desktop 호스트 풀](create-host-pools-azure-marketplace.md)
- Azure Virtual Desktop 서비스를 사용하여 구성 및 등록된 세션 호스트 풀 VM
- Azure 구독에 대한 [기여자 액세스](../role-based-access-control/role-assignments-portal.md)를 갖는 사용자

도구를 배포하는 데 사용할 머신에는 다음이 필요합니다.

- Windows PowerShell 5.1 이상
- Microsoft Az PowerShell 모듈

모두 준비됐으면 이제 시작하겠습니다.

## <a name="create-or-update-an-azure-automation-account"></a>Azure Automation 계정 만들기 또는 업데이트

>[!NOTE]
>이전 버전의 크기 조정 스크립트를 실행 하는 Runbook이 있는 Azure Automation 계정이 이미 있는 경우 다음 지침에 따라 업데이트되었는지 확인해야 합니다.

먼저 PowerShell Runbook을 실행할 Azure Automation 계정이 필요합니다. 이 섹션에서 설명하는 프로세스는 PowerShell Runbook을 설정하는 데 사용하려는 기존 Azure Automation 계정이 있는 경우에도 유효합니다. 설정 방법은 다음과 같습니다.

1. Windows PowerShell을 엽니다.

2. 다음 cmdlet을 실행하여 Azure 계정에 로그인합니다.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >계정에는 크기 조정 도구를 배포할 Azure 구독에 대한 기여자 권한이 있어야 합니다.

3. 다음 cmdlet을 실행하여 Azure Automation 계정을 만드는 스크립트를 다운로드합니다.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. 다음 cmdlet을 실행하여 스크립트를 실행하고 Azure Automation 계정을 만듭니다. 매개 변수에 대한 값을 입력하거나 기본값을 사용하도록 주석을 지정할 수 있습니다.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. cmdlet의 출력에 웹후크 URI가 포함됩니다. URI를 기록해 두세요. 이 값은 Azure Logic App에 대해 실행 일정을 설정할 때 매개 변수로 사용합니다.

6. Log Analytics에 대해 **WorkspaceName** 매개 변수를 지정한 경우 cmdlet의 출력에는 Log Analytics 작업 영역 ID 및 해당 기본 키도 포함됩니다. URI를 기록해 두세요. 이 값은 나중에 Azure Logic App에 대해 실행 일정을 설정할 때 다시 매개 변수로 사용합니다.

7. Azure Automation 계정을 설정한 후에는 Azure 구독에 로그인하고 다음 그림과 같이 Azure Automation 계정 및 관련 Runbook이 지정된 리소스 그룹에 표시되는지 확인합니다.

    >[!div class="mx-imgBorder"]
    >![새로 만든 Azure Automation 계정 및 Runbook을 보여 주는 Azure 개요 페이지 이미지.](media/automation-account.png)

    웹후크가 제자리에 있는지 확인하려면 Runbook의 이름을 선택합니다. 그런 다음 Runbook의 리소스 섹션으로 이동하여 **웹후크** 를 선택합니다.

## <a name="create-an-azure-automation-run-as-account"></a>Azure Automation 실행 계정 만들기

Azure Automation 계정을 만들었으니 이번에는 Azure Automation 실행 계정이 없는 경우 하나 만들어야 합니다. 이 계정을 통해 도구에서 Azure 리소스에 액세스할 수 있습니다.

[Azure Automation 실행 계정](../automation/manage-runas-account.md)은 Azure cmdlet을 사용하여 Azure의 리소스를 관리하는 데 필요한 인증을 제공합니다. 실행 계정을 만들면 Azure Active Directory에 새로운 서비스 주체 사용자가 생성되며 구독 수준에서 이 서비스 주체 사용자에게 기여자 역할을 할당합니다. Azure 실행 계정은 자격 증명 개체에서 사용자 이름 및 암호를 저장할 필요 없이 인증서와 서비스 주체 이름으로 안전하게 인증하는 훌륭한 방법입니다. 실행 계정 인증에 대해 자세히 알아보려면 [실행 계정 권한 제한](../automation/manage-runas-account.md#limit-run-as-account-permissions)을 참조하세요.

구독 관리자 역할의 구성원이고 구독의 공동 관리자인 사용자는 실행 계정을 만들 수 있습니다.

Azure Automation 계정에서 실행 계정을 만들려면 다음을 수행합니다.

1. Azure Portal에서 **모든 서비스** 를 선택합니다. 리소스 목록에서 **Automation 계정** 을 입력하고 선택합니다.

2. **Automation 계정** 페이지에서 Azure Automation 계정의 이름을 선택합니다.

3. 창의 왼쪽 창에서 **계정 설정** 섹션 아래에 있는 **실행 계정** 을 선택합니다.

4. **Azure 실행 계정** 을 선택합니다. **Azure 실행 계정 추가** 창이 나타나면 개요 정보를 검토한 다음, **만들기** 를 선택하여 계정 만들기 프로세스를 시작합니다.

5. Azure가 실행 계정을 만드는 동안 몇 분 정도 기다립니다. 알림 아래에 있는 메뉴에서 만들기 진행률을 추적할 수 있습니다.

6. 프로세스가 완료되면 지정된 Azure Automation 계정에 **AzureRunAsConnection** 이라는 자산이 생성됩니다. **Azure 실행 계정** 을 선택합니다. 이 연결 자산은 애플리케이션 ID, 테넌트 ID, 구독 ID 및 인증서 지문을 보유합니다. **연결** 페이지에서 동일한 정보를 찾을 수도 있습니다. 이 페이지로 이동하려면 창의 왼쪽 창에서 **공유 리소스** 섹션 아래에 있는 **연결** 을 선택하고 **AzureRunAsConnection** 이라는 연결 자산을 클릭합니다.

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Azure Logic App 및 실행 일정 만들기

마지막으로, Azure Logic App을 만들고 새 크기 조정 도구의 실행 일정을 설정해야 합니다. 먼저 PowerShell 세션에서 사용할 [Virtual Desktop PowerShell 모듈](powershell-module.md)을 다운로드하고 가져옵니다(아직 없는 경우).

1. Windows PowerShell을 엽니다.

2. 다음 cmdlet을 실행하여 Azure 계정에 로그인합니다.

    ```powershell
    Login-AzAccount
    ```

3. 다음 cmdlet을 실행하여 Azure Logic App을 만들기 위한 스크립트를 다운로드합니다.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. 다음 PowerShell 스크립트를 실행하여 호스트 풀에 대한 Azure Logic App 및 실행 일정을 만듭니다.

    >[!NOTE]
    >자동 크기 조정을 수행하려는 각 호스트 풀에 대해 이 스크립트를 실행해야 하지만 Azure Automation 계정은 하나만 필요합니다.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    스크립트를 실행한 후 다음 그림처럼 Azure Logic App이 리소스 그룹에 표시됩니다.

    >[!div class="mx-imgBorder"]
    >![예제 Azure Logic App의 개요 페이지 이미지.](media/logic-app.png)

    실행 일정을 변경하려면(예: 되풀이 간격 또는 표준 시간대 변경) Azure Logic App 자동 크기 조정 스케줄러로 이동하고 **편집** 을 선택하여 Azure Logic App 디자이너로 이동합니다.

    >[!div class="mx-imgBorder"]
    >![Azure Logic App 디자이너 이미지. 사용자가 되풀이 시간 및 웹후크 파일을 편집할 수 있는 되풀이 및 웹후크 메뉴가 열립니다.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>크기 조정 도구 관리

크기 조정 도구를 만들었으니 이제 출력에 액세스할 수 있습니다. 이 섹션에서는 몇 가지 유용한 기능에 대해 설명합니다.

### <a name="view-job-status"></a>작업 상태 보기

모든 Runbook 작업의 요약 상태를 보거나 Azure Portal에서 특정 Runbook 작업의 세부 상태를 볼 수 있습니다.

선택한 Azure Automation 계정 오른쪽에 있는 “작업 통계” 아래에서 모든 Runbook 작업의 요약을 볼 수 있습니다. 창 왼쪽에서 **작업** 페이지를 열면 현재 작업 상태, 시작 시간 및 완료 시간이 표시됩니다.

>[!div class="mx-imgBorder"]
>![작업 상태 페이지 스크린샷.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>로그 및 크기 조정 도구 출력 보기

Runbook을 열고 작업을 선택하여 스케일 아웃 및 스케일 인 작업의 로그를 볼 수 있습니다.

Azure Automation 계정을 호스트하는 리소스 그룹의 Runbook으로 이동하여 **개요** 를 선택합니다. 다음 그림처럼 개요 페이지의 **최근 작업** 아래에서 작업을 선택하면 크기 조정 도구 출력을 볼 수 있습니다.

>[!div class="mx-imgBorder"]
>![크기 조정 도구의 출력 창 이미지.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Runbook 스크립트 버전 번호 확인

Azure Automation 계정에서 Runbook 파일을 열고 **보기** 를 선택하여 사용 중인 Runbook 스크립트 버전을 확인할 수 있습니다. Runbook에 대한 스크립트가 화면 오른쪽에 표시됩니다. 스크립트에서 버전 번호는 `v#.#.#` 형식으로 `SYNOPSIS` 섹션 아래에 표시됩니다. 최신 버전은 [여기](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1)에서 찾을 수 있습니다. Runbook 스크립트에 버전 번호가 표시되지 않으면 이전 버전의 스크립트를 실행 중임을 의미하며, 즉시 업데이트해야 합니다. Runbook 스크립트를 업데이트해야 하는 경우 [Azure Automation 계정 만들기 또는 업데이트](#create-or-update-an-azure-automation-account)의 지침을 따르세요.

### <a name="reporting-issues"></a>문제 보고

문제를 보고하는 경우 문제 해결에 도움이 되도록 다음 정보를 제공해야 합니다.

- 문제의 원인이 된 작업의 **모든 로그** 탭에 있는 전체 로그입니다. 로그를 가져오는 방법을 알아보려면 [로그 보기 및 크기 조정 도구 출력](#view-logs-and-scaling-tool-output)의 지침을 따르세요. 로그에 중요한 정보나 개인 정보가 있는 경우 문제를 Microsoft에 제출하기 전에 제거할 수 있습니다.

- 사용 중인 Runbook 스크립트의 버전입니다. 버전 번호를 가져오는 방법을 알아보려면 [Runbook 스크립트 버전 번호 확인](#check-the-runbook-script-version-number)을 참조하세요.

- Azure Automation 계정에 설치된 다음 PowerShell 모듈의 버전 번호입니다. 이러한 모듈을 찾으려면 Azure Automation 계정을 열고 창의 왼쪽 창에 있는 **공유 리소스** 섹션에서 **모듈** 을 선택한 다음, 모듈의 이름을 검색합니다.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OMSIngestionAPI
    - Az.DesktopVirtualization

- [실행 계정](#create-an-azure-automation-run-as-account)에 대한 만료 날짜입니다. 이를 찾으려면 Azure Automation 계정을 연 다음, 창의 왼쪽 창에서 **계정 설정** 아래에 있는 **실행 계정** 을 선택합니다. 만료 날짜는 **Azure 실행 계정** 에 있어야 합니다.

### <a name="log-analytics"></a>Log Analytics

Log Analytics를 사용하기로 결정한 경우 Log Analytics 작업 영역의 **로그** 보기에서 **사용자 지정 로그** 아래의 **WVDTenantScale_CL** 이라는 사용자 지정 로그의 모든 로그 데이터를 볼 수 있습니다. 다음은 유용하게 사용할 수 있는 몇 가지 샘플 쿼리를 나열한 것입니다.

- 호스트 풀의 모든 로그를 보려면 다음 쿼리를 입력합니다.

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 호스트 풀에서 현재 실행 중인 세션 호스트 VM 및 활성 사용자 세션의 총 수를 보려면 다음 쿼리를 입력합니다.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 호스트 풀에 있는 모든 세션 호스트 VM의 상태를 보려면 다음 쿼리를 입력합니다.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- 오류 및 경고를 보려면 다음 쿼리를 입력합니다.

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>문제 보고

크기 조정 도구에 대한 문제 보고서는 현재 Microsoft 지원에서 처리되고 있습니다. 문제 보고를 작성하는 경우 [문제 보고](#reporting-issues)의 지침을 따라야 합니다. 도구에 대한 의견이 있거나 새 기능을 요청하려는 경우 [RDS GitHub 페이지](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool)에서 “4-WVD-scaling-tool”이라는 레이블이 지정된 GitHub 문제를 엽니다.
