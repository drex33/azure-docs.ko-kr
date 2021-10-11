---
title: 자습서 - Azure Automation에서 PowerShell 워크플로 Runbook 만들기
description: 이 자습서에서는 PowerShell 워크플로 Runbook을 만들고, 테스트하고, 게시하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/23/2021
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e1550caff2fbd28a08e89c3fa570216ff8002430
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057703"
---
# <a name="tutorial-create-a-powershell-workflow-runbook-in-automation"></a>자습서: Automation에서 PowerShell 워크플로 Runbook 만들기

이 자습서는 Azure Automation에서 [PowerShell 워크플로 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)을 만드는 과정을 안내합니다. PowerShell 워크플로 Runbook은 Windows PowerShell 워크플로를 기반으로 하는 텍스트 Runbook입니다. Azure Portal에서 텍스트 편집기를 사용하여 Runbook 코드를 만들고 편집할 수 있습니다. 

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * PowerShell 워크플로 Runbook 만들기
> * Runbook 테스트 및 게시
> * Runbook 작업 실행 및 상태 추적
> * Azure 리소스를 관리하는 인증 추가
> * Azure 가상 머신을 시작하도록 Runbook 매개 변수 업데이트

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

* 사용자 할당 관리 ID가 하나 이상 있는 Azure Automation 계정. 자세한 내용은 [관리 ID 사용](../quickstarts/enable-managed-identity.md)을 참조하세요.
* Automation 계정으로 가져온 Az 모듈 `Az.Accounts` 및 `Az.Compute`. 자세한 내용은 [Az 모듈 가져오기](../shared-resources/modules.md#import-az-modules)를 참조하세요.
* 2개 이상의 [Azure 가상 머신](../../virtual-machines/windows/quick-create-powershell.md). 이러한 머신을 중지하고 시작하기 때문에 프로덕션 VM이면 안 됩니다.
* 로컬 컴퓨터에 설치된 [Azure Az PowerShell 모듈](/powershell/azure/new-azureps-module-az)입니다. 설치 또는 업그레이드하려면 [Azure Az PowerShell 모듈을 설치하는 방법](/powershell/azure/install-az-ps)을 참조하세요.

## <a name="assign-permissions-to-managed-identities"></a>관리 ID에 권한 할당

적절한 [관리 ID](../automation-security-overview.md#managed-identities-preview)에 권한을 할당하여 가상 머신을 중지할 수 있도록 허용합니다. Runbook은 Automation 계정의 시스템이 할당한 관리 ID 또는 사용자가 할당한 관리 ID를 사용할 수 있습니다. 각 ID에 권한을 할당하는 단계가 제공됩니다. 아래 단계에서는 Azure Portal을 사용합니다. PowerShell을 사용하려면 [Azure PowerShell을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-powershell.md)을 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Automation 계정으로 이동합니다.

1. **계정 설정** 에서 **ID(미리 보기)** 를 선택합니다.

1. **시스템 할당** 탭의 **권한** 에서 **Azure 역할 할당** 을 선택하여 **Azure 역할 할당** 페이지를 선택합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-role-assignments-portal.png" alt-text="포털에서 Azure 역할 할당을 선택합니다.":::

1. **+ 역할 할당 추가(미리 보기)** 를 선택하여 **역할 할당 추가(미리 보기)** 페이지를 엽니다. 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/system-assigned-add-role-assignment-portal.png" alt-text="포털에서 역할 할당을 추가합니다.":::

1. 적절한 값을 선택합니다.

   |속성 |Description |
   |---|---|
   |범위| 범위는 리소스 할당이 적용되는 리소스 세트입니다. 드롭다운 목록에서 **리소스 그룹** 을 선택합니다.|
   |구독|이 필드는 자동으로 구독으로 채워집니다.|
   |리소스 그룹|드롭다운 목록에서 ID 권한을 부여할 리소스 그룹을 선택합니다.|
   |역할|드롭다운 목록에서 **DevTest Labs 사용자** 를 선택합니다.|

1. **저장** 을 선택한 다음, **Azure 역할 할당** 페이지를 닫고 **시스템 할당** 탭으로 돌아갑니다.

1. **사용자 할당** 탭을 선택합니다.

1. 목록에서 사용자가 할당한 관리 ID를 선택하여 **관리 ID** 페이지를 엽니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/select-user-assigned-identity-portal.png" alt-text="포털에서 사용자가 할당한 관리 ID를 선택합니다.":::

1. 나중에 사용할 수 있도록 **클라이언트 ID** 를 기록해 둡니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/managed-identity-client-id-portal.png" alt-text="포털에 표시된 관리 ID의 클라이언트 ID":::

1. 왼쪽 메뉴에서 **Azure 역할 할당** 을 선택한 다음, **+ 역할 할당 추가(미리 보기)** 를 선택하여 **역할 할당 추가(미리 보기)** 페이지를 엽니다. 

   :::image type="content" source="../media/automation-tutorial-runbook-textual/user-assigned-add-role-assignment-portal.png" alt-text="포털에서 사용자가 할당한 ID에 대한 역할 할당을 추가합니다.":::

1. 적절한 값을 선택합니다.

   |속성 |Description |
   |---|---|
   |범위| 드롭다운 목록에서 **리소스 그룹** 을 선택합니다.|
   |구독|이 필드는 자동으로 구독으로 채워집니다.|
   |리소스 그룹|드롭다운 목록에서 ID 권한을 부여할 리소스 그룹을 선택합니다.|
   |역할|드롭다운 목록에서 **DevTest Labs 사용자** 를 선택합니다.|

1. **저장** 을 선택한 다음, **Azure 역할 할당** 페이지를 닫고 **사용자 할당** 탭으로 돌아갑니다.

## <a name="create-new-runbook"></a>새 Runbook 만들기

먼저 간단한 [PowerShell 워크플로 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)을 만듭니다. Windows PowerShell 워크플로의 한 가지 장점은 일반적인 스크립트처럼 명령 집합을 순차적으로 수행하지 않고 병렬로 수행할 수 있다는 점입니다.

1. 열려 있는 Automation 계정 페이지의 **프로세스 자동화** 에서 **Runbook** 을 선택합니다.

1. **+ Runbook 만들기** 를 선택합니다.
    1. Runbook 이름을 `MyFirstRunbook-Workflow`로 지정합니다.
    1. **Runbook 형식** 드롭다운 메뉴에서 **PowerShell 워크플로** 를 선택합니다.
    1. **생성** 를 선택합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/create-powershell-workflow-runbook.png" alt-text="포털에서 PowerShell 워크플로 Runbook 만들기":::

## <a name="add-code-to-the-runbook"></a>Runbook에 코드 추가

코드를 Runbook에 직접 입력하거나, [라이브러리] 컨트롤에서 cmdlet, Runbook 및 자산을 선택하고 관련 매개 변수를 사용하여 Runbook에 추가할 수 있습니다. 이 자습서에서는 코드를 Runbook에 직접 입력합니다.

Runbook에는 현재 필수 `Workflow` 키워드, Runbook 이름 및 전체 워크플로가 포함되는 중괄호만 있고 나머지 부분은 비어 있습니다.

```powershell
Workflow MyFirstRunbook-Workflow
{
}
```

1. `Parallel` 키워드를 사용하여 동시에 실행되는 여러 명령이 포함된 스크립트 블록을 만들 수 있습니다. 중괄호 *안에* 다음 코드를 입력합니다.

   ```powershell
   Parallel {
        Write-Output "Parallel"
        Get-Date
        Start-Sleep -s 3
        Get-Date
    }

   Write-Output " `r`n"
   Write-Output "Non-Parallel"
   Get-Date
   Start-Sleep -s 3
   Get-Date  
   ```

1. **저장** 을 선택하여 Runbook을 저장합니다.

## <a name="test-the-runbook"></a>Runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 먼저 해당 Runbook을 테스트하여 제대로 작동하는지 확인해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 출력을 대화형으로 볼 수 있습니다.

1. **테스트 창** 을 선택하여 **테스트** 페이지를 엽니다.

1. **시작** 을 선택하여 테스트를 시작합니다.  [Runbook 작업](../automation-runbook-execution.md)이 생성되고 해당 상태가 이 창에 표시됩니다.

   작업 상태는 '대기'로 시작됩니다. 이는 클라우드에서 runbook worker를 사용할 수 있을 때까지 작업에서 기다리고 있음을 나타냅니다. 작업자에서 작업을 요청하면 상태가 '시작 중'으로 변경됩니다. 마지막으로 Runbook이 실제로 실행되기 시작하면 상태가 '실행 중'이 됩니다.

1. Runbook 작업이 완료되면 **테스트** 페이지에 출력이 표시됩니다. 출력은 다음 이미지와 비슷합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-parallel-output.png" alt-text="PowerShell 워크플로 Runbook 병렬 출력":::

   출력을 검토합니다. `Parallel` 명령을 포함하여 `Start-Sleep` 블록의 모든 것이 동시에 실행됩니다. 다른 날짜 타임스탬프를 통해 표시된 것처럼, `Parallel` 블록 외부의 동일한 명령이 순차적으로 실행되었습니다. 

1. **테스트** 페이지를 닫고 캔버스로 돌아갑니다.

## <a name="publish-and-start-the-runbook"></a>Runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하려면 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시** 를 선택하여 Runbook을 게시한 다음, 확인 메시지가 표시되면 **예** 를 선택합니다.

1. 이제 **상태** 필드가 **게시됨** 으로 표시됩니다. 지금 Runbook을 시작하거나, 향후 시작 시간을 예약하거나, [webhook](../automation-webhooks.md)를 만들어 HTTP 호출을 통해 Runbook을 시작할 수 있는 위쪽의 옵션을 검토합니다. **시작** 을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예** 를 선택합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/workflow-runbook-overview.png" alt-text="PowerShell 워크플로 Runbook 개요 페이지":::

1. 만든 Runbook 작업에 대한 **작업** 페이지가 열립니다. 여기서는 작업 진행 상황을 볼 수 있도록 이 페이지를 열어 둡니다. **상태** 필드는 Runbook을 테스트할 때 표시된 상태와 일치합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-page-overview.png" alt-text="Runbook 작업 페이지의 스크린샷":::

1. Runbook 상태가 **완료됨** 으로 표시되면 **출력** 을 선택합니다. 출력은 테스트 출력과 비슷합니다.

1. **작업** 페이지를 닫고 Runbook **개요** 페이지로 돌아갑니다.

1. **리소스** 아래에서 **작업** 을 선택합니다. 이 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 여기서는 작업을 한 번만 실행했으므로 하나의 작업만 나열됩니다.

1. 작업을 선택하여 Runbook을 시작할 때 표시된 것과 동일한 **작업** 창을 엽니다. 이 페이지를 사용하여 Runbook에 대해 만들어진 작업의 세부 정보를 볼 수 있습니다. **작업** 페이지를 닫고 Runbook **개요** 페이지로 돌아갑니다.

## <a name="add-authentication-to-manage-azure-resources"></a>Azure 리소스를 관리하는 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 구독에 대한 자격 증명을 사용하여 인증하지 않으면 이 작업을 수행할 수 없습니다. 이 Runbook은 Automation 계정의 시스템이 할당한 관리 ID를 사용하여 VM에 대한 관리 작업을 수행하도록 Azure를 인증합니다. 이 Runbook은 사용자가 할당한 관리 ID를 사용하도록 쉽게 수정할 수 있습니다.

1. **개요** 를 선택한 다음, **편집** 을 선택하여 텍스트 편집기를 엽니다.

1. 모든 기존 코드를 다음으로 바꿉니다.

   ```powershell
   workflow MyFirstRunbook-Workflow
   {
   $resourceGroup = "resourceGroupName"
    
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process
    
   # Connect to Azure with system-assigned managed identity
   $AzureContext = (Connect-AzAccount -Identity).context
    
   # set and store context
   $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
   }
   ```

   `$resourceGroup` 변수를 리소스 그룹을 나타내는 유효한 값으로 편집합니다.

1. 시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.
    1. 9번째 줄에서 `$AzureContext = (Connect-AzAccount -Identity).context`를 제거합니다.
    1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
    1. 앞에서 얻은 클라이언트 ID를 입력합니다.

1. **저장** 을 선택한 후 **테스트 창** 을 선택합니다.

1. **시작** 을 선택하여 테스트를 시작합니다. 완료되면 다음과 비슷한 출력이 표시됩니다. 여기서는 계정의 기본 정보를 보여 줍니다. 이 작업에서는 자격 증명이 유효한지 확인합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/runbook-auth-output.png" alt-text="자격 증명을 확인하는 기본 정보":::

1. **테스트** 페이지를 닫고 캔버스로 돌아갑니다.

## <a name="add-code-to-start-a-virtual-machine"></a>가상 머신을 시작하기 위한 코드 추가

이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작하는 명령을 추가합니다. Azure 구독에서 VM을 선택할 수 있지만, 지금은 해당 이름을 Runbook에 하드 코딩합니다. 

1. 아래 코드를 닫는 중괄호 바로 앞의 마지막 줄로 추가합니다. `VMName`을 VM의 실제 이름으로 바꿉니다. 

   ```powershell
   Start-AzVM -Name "VMName" -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Runbook을 테스트하고 VM이 시작되었는지 확인합니다. 그런 다음, 캔버스로 돌아갑니다.

## <a name="add-input-parameters-to-the-runbook"></a>Runbook에 입력 매개 변수 추가

현재 Runbook은 Runbook에 하드 코딩된 VM을 시작합니다. Runbook이 시작될 때 VM을 지정할 수 있는 경우 더 유용합니다. Runbook에 입력 매개 변수를 추가하여 해당 기능을 제공합니다.

1. 3번째 줄 `$resourceGroup = "resourceGroupName"`을 다음 코드로 바꿉니다.

    ```powershell
    Param(
        [string]$resourceGroup,
        [string]$VMName
    )
   ```

1. 이전 명령 `Start-AzVM`을 다음으로 바꿉니다.

   ```powershell
   Start-AzVM -Name $VMName -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
   ```

1. Runbook을 테스트하고 VM이 시작되었는지 확인합니다. 그런 다음, 캔버스로 돌아갑니다.

## <a name="manage-multiple-vms-simultaneously"></a>동시에 여러 VM 관리

`ForEach -Parallel` 구문을 사용하여 컬렉션의 각 항목에 대한 명령을 동시에 처리할 수 있습니다. 이제 Runbook이 다음을 수행하도록 코드를 수정합니다.
- 가상 머신 이름 컬렉션 수락
- 가상 머신을 중지하거나 시작하는 매개 변수 수락
- 모든 가상 머신에 대해 병렬로 작업 수행

1. 모든 기존 코드를 다음으로 바꿉니다.

    ```powershell
    workflow MyFirstRunbook-Workflow
    {
    Param(
        [string]$resourceGroup,
        [string[]]$VMs,
        [string]$action
    )
    
    # Ensures you do not inherit an AzContext in your runbook
    Disable-AzContextAutosave -Scope Process
    
    # Connect to Azure with system-assigned managed identity
    $AzureContext = (Connect-AzAccount -Identity).context
    
    # set and store context
    $AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext   
    
    # Start or stop VMs in parallel
    if($action -eq "Start")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Start-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext
            }
        }
    elseif ($action -eq "Stop")
        {
            ForEach -Parallel ($vm in $VMs)
            {
                Stop-AzVM -Name $vm -ResourceGroupName $resourceGroup -DefaultProfile $AzureContext -Force
            }
        }
    else {
            Write-Output "`r`n Action not allowed. Please enter 'stop' or 'start'."
        }
    }
    ```

1. 시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.
    1. 13번째 줄에서 `$AzureContext = (Connect-AzAccount -Identity).context`를 제거합니다.
    1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
    1. 앞에서 얻은 클라이언트 ID를 입력합니다.

1. **저장** 을 선택하고 **게시** 를 선택한 다음, 확인 메시지가 표시되면 **예** 를 선택합니다.

1. **개요** 페이지에서 **시작** 을 선택합니다.

1. 매개 변수를 채우고 **확인** 을 선택합니다.

   |매개 변수 |Description |
   |---|---|
   |RESOURCEGROUP|VM의 리소스 그룹 이름을 입력합니다.|
   |VM|`["VM1","VM2","VM3"]` 구문을 사용하여 가상 머신의 이름을 입력합니다.|
   |작업|`stop` 또는 `start`를 입력합니다.|

1. 가상 머신 목록으로 이동하고, 몇 초마다 페이지를 새로 고칩니다. 각 VM에 대한 작업이 병렬로 수행되는지 관찰합니다. `-Parallel` 키워드가 없으면 작업이 순차적으로 수행됩니다. VM은 순차적으로 시작되지만, 각 VM은 각 VM의 특성에 따라 약간 다른 시간에 **실행 중** 단계에 도달할 수 있습니다.

## <a name="clean-up-resources"></a>리소스 정리

이 Runbook을 더 이상 사용하지 않으려면 다음 단계에 따라 삭제합니다.

1. Automation 계정으로 이동합니다.
1. **프로세스 자동화** 아래에서 **Runbook** 을 선택합니다.
1. Runbook을 선택합니다.
1. Runbook **개요** 페이지에서 **삭제** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 PowerShell 워크플로 Runbook을 만들었습니다. Python 3 Runbook에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: Python 3 Runbook(미리 보기) 만들기](automation-tutorial-runbook-textual-python-3.md)