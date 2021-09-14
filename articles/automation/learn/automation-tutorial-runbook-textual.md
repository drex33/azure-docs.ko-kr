---
title: Azure Automation에서 PowerShell 워크플로 Runbook 만들기
description: 이 문서에서는 간단한 PowerShell 워크플로 Runbook을 만들고, 테스트하고, 게시하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/19/2020
ms.topic: tutorial
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c0efb162b624cd6bc1ca2266c51506b49bb07f2b
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/04/2021
ms.locfileid: "123480285"
---
# <a name="tutorial-create-a-powershell-workflow-runbook"></a>자습서: PowerShell 워크플로 Runbook 만들기

이 자습서는 Azure Automation에서 [PowerShell 워크플로 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)을 만드는 과정을 안내합니다. PowerShell 워크플로 Runbook은 Windows PowerShell 워크플로를 기반으로 하는 텍스트 Runbook입니다. Azure Portal에서 텍스트 편집기를 사용하여 Runbook 코드를 만들고 편집할 수 있습니다. 

> [!div class="checklist"]
> * 간단한 PowerShell 워크플로 Runbook 만들기
> * Runbook 테스트 및 게시
> * Runbook 작업 실행 및 상태 추적
> * Runbook 매개 변수를 사용하여 Azure 가상 머신을 시작하도록 Runbook 업데이트

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음이 필요합니다.

* 동작합니다. 구독이 아직 없는 경우 [MSDN 구독자 혜택을 활성화](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)하거나 [무료 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 등록할 수 있습니다.
* [Automation 계정](../index.yml) . 이 계정은 가상 머신을 시작하고 중지할 수 있는 권한이 있어야 합니다.
* Azure 가상 머신. 이 머신을 중지하고 시작하므로 프로덕션 VM이 아니어야 합니다.

## <a name="step-1---create-new-runbook"></a>1 단계-새 runbook 만들기

먼저 `Hello World`라는 텍스트를 출력하는 간단한 Runbook을 만듭니다.

1. Azure Portal에서 Automation 계정을 엽니다.

   Automation 계정 페이지는 이 계정의 리소스 간략히 보기를 제공합니다. 사용자에게는 이미 일부 자산이 있어야 합니다. 이러한 자산의 대부분은 새 Automation 계정에 자동으로 포함되는 모듈입니다. 또한 구독과 연결된 자격 증명 자산이 있어야 합니다.
 
2. **프로세스 자동화** 아래에서 **Runbook** 을 선택하여 Runbook 목록을 엽니다.

3. **Runbook 만들기** 를 선택하여 새 Runbook을 만듭니다.

4. Runbook 이름을 **MyFirstRunbook-Workflow** 로 지정합니다.

5. 여기서는 [PowerShell 워크플로 Runbook](../automation-runbook-types.md#powershell-workflow-runbooks)을 만듭니다. **Runbook 형식** 에 대해 **PowerShell 워크플로** 를 선택합니다.

6. **만들기** 를 클릭하여 Runbook을 만들고 그래픽 편집기를 엽니다.

## <a name="step-2---add-code-to-the-runbook"></a>2단계 - Runbook에 코드 추가

코드를 Runbook에 직접 입력하거나, [라이브러리] 컨트롤에서 cmdlet, Runbook 및 자산을 선택하고 관련 매개 변수를 사용하여 Runbook에 추가할 수 있습니다. 이 자습서에서는 코드를 Runbook에 직접 입력합니다.

1. Runbook에는 현재 필수 `Workflow` 키워드, Runbook 이름 및 전체 워크플로가 포함되는 중괄호만 있고 나머지 부분은 비어 있습니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   }
   ```

2. 중괄호 안에 `Write-Output "Hello World"`를 입력합니다.

   ```powershell-interactive
   Workflow MyFirstRunbook-Workflow
   {
   Write-Output "Hello World"
   }
   ```

3. **저장** 을 클릭하여 Runbook을 저장합니다.

## <a name="step-3---test-the-runbook"></a>3 단계 - runbook 테스트

프로덕션 환경에서 사용할 수 있도록 Runbook을 게시하기 전에 먼저 해당 Runbook을 테스트하여 제대로 작동하는지 확인해야 합니다. Runbook을 테스트하면 초안 버전이 실행되고 출력을 대화형으로 볼 수 있습니다.

1. **테스트 창** 을 선택하여 [테스트] 창을 엽니다.

2. **시작** 을 클릭하여 사용하도록 설정된 옵션만 테스트하는 테스트를 시작합니다.

3. [Runbook 작업](../automation-runbook-execution.md)이 만들어지고 해당 상태가 창에 표시됩니다.

   작업 상태는 '대기'로 시작됩니다. 이는 클라우드에서 runbook worker를 사용할 수 있을 때까지 작업에서 기다리고 있음을 나타냅니다. 작업자에서 작업을 요청하면 상태가 '시작 중'으로 변경됩니다. 마지막으로 Runbook이 실제로 실행되기 시작하면 상태가 '실행 중'이 됩니다.

4. Runbook 작업이 완료되면 [테스트] 창에 해당 출력이 표시됩니다. 여기서는 `Hello World`가 표시됩니다.

   ![Hello World](../media/automation-tutorial-runbook-textual/test-output-hello-world.png)

5. 캔버스로 돌아가려면 테스트 창을 닫습니다.

## <a name="step-4---publish-and-start-the-runbook"></a>4 단계 - runbook 게시 및 시작

방금 만든 Runbook은 아직 초안 모드입니다. 프로덕션 환경에서 실행하려면 먼저 게시해야 합니다. Runbook을 게시하면 초안 버전으로 기존의 게시된 버전을 덮어씁니다. 이 경우에 Runbook을 방금 만들었으므로 아직 게시된 버전이 없습니다.

1. **게시** 를 클릭하여 Runbook을 게시한 다음 확인 메시지가 표시되면 **예** 를 클릭합니다.

2. **Runbook** 페이지에서 왼쪽으로 스크롤하여 Runbook을 살펴보고, **작성 상태** 필드가 **게시됨** 으로 설정되어 있는지 확인합니다.

3. 오른쪽으로 다시 스크롤하여 **MyFirstRunbook-Workflow** 페이지를 봅니다.

   위쪽의 옵션을 사용하면 지금 Runbook을 시작하거나, 향후의 시작 시간을 예약하거나, [웹후크](../automation-webhooks.md)를 만들어 HTTP 호출을 통해 Runbook을 시작할 수 있습니다.

4. **시작** 을 선택하고 Runbook을 시작할지 확인하는 메시지가 표시되면 **예** 를 선택합니다.

   ![Runbook 시작](../media/automation-tutorial-runbook-textual/automation-runbook-controls-start.png)

5. 만든 Runbook 작업에 대한 [작업] 창이 열립니다. 여기서는 작업 진행 상황을 볼 수 있도록 창을 열어 둡니다.

6. 작업 상태는 **작업 요약** 에 표시됩니다. 이 상태는 Runbook을 테스트할 때 표시된 상태와 일치합니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-jobsummary.png" alt-text="강조 표시된 작업 요약 섹션을 보여주는 Runbook 작업 창의 스크린샷.":::

7. Runbook 상태가 '완료됨'으로 표시되면 **출력** 을 클릭합니다. `Hello World` 메시지가 표시되는 [출력] 페이지가 열립니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-outputtile.png" alt-text="강조 표시된 출력 단추를 보여주는 Runbook 작업 창의 스크린샷.":::

8. 출력 페이지를 닫습니다.

9. **모든 로그** 를 클릭하여 Runbook 작업에 대한 스트림 창을 엽니다. 출력 스트림에는 `Hello World`만 표시됩니다. Runbook에서 [스트림] 창에 기록하는 경우 자세한 정보 및 오류 스트림과 같은 Runbook 작업에 대한 다른 스트림이 표시될 수 있습니다.

   :::image type="content" source="../media/automation-tutorial-runbook-textual/job-pane-status-blade-alllogstile.png" alt-text="강조 표시된 모든 로그 단추를 보여주는 Runbook 작업 창의 스크린샷.":::

10. [스트림] 창과 [작업] 창을 닫고 MyFirstRunbook 페이지로 돌아갑니다.

11. **리소스** 아래에서 **작업** 을 클릭하여 이 Runbook에 대한 [작업] 페이지를 엽니다. 이 페이지에는 Runbook에서 만든 모든 작업이 나열됩니다. 여기서는 작업을 한 번만 실행했으므로 하나의 작업만 나열됩니다.

   ![작업](../media/automation-tutorial-runbook-textual/runbook-control-job-tile.png)

12. 작업 이름을 클릭하여 Runbook을 시작할 때 표시된 것과 동일한 [작업] 창을 엽니다. 이 창을 사용하여 Runbook에 대해 만들어진 작업의 세부 정보를 볼 수 있습니다.

## <a name="step-5---add-authentication-to-manage-azure-resources"></a>5 단계-Azure 리소스를 관리 인증 추가

지금까지 Runbook을 테스트 하고 게시했지만, 딱히 유용하지는 않습니다. Azure 리소스를 관리하려고 합니다. 구독에 대한 자격 증명을 사용하여 인증하지 않으면 이 작업을 수행할 수 없습니다. 인증은 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용합니다.

>[!NOTE]
>PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 이러한 cmdlet을 사용하거나 Automation 계정의 [모듈을 최신 버전으로 업데이트](../automation-update-azure-modules.md)할 수 있습니다. 새 Automation 계정을 만든 경우에도 모듈을 업데이트해야 할 수 있습니다.

1. MyFirstRunbook-Workflow 페이지로 이동하고, **편집** 을 클릭하여 텍스트 편집기를 엽니다.

2. `Write-Output` 줄을 삭제합니다.

3. 중괄호 사이의 빈 줄에 커서를 놓습니다.

4. Automation 실행 계정을 사용하여 인증을 처리하는 다음 코드를 입력하거나 복사하여 붙여넣습니다.

   ```powershell-interactive
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID `
   -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Select-AzSubscription -SubscriptionId $Conn.SubscriptionID
   ```

5. Runbook을 테스트할 수 있도록 **테스트 창** 을 클릭합니다.

6. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면 다음과 비슷한 출력이 표시됩니다. 여기서는 계정의 기본 정보를 보여 줍니다. 이 작업에서는 자격 증명이 유효한지 확인합니다.

   ![Authenticate](../media/automation-tutorial-runbook-textual/runbook-auth-output.png)

## <a name="step-6---add-code-to-start-a-virtual-machine"></a>6단계 - 가상 머신을 시작하기 위한 코드 추가

이제 Runbook에서 Azure 구독을 인증하므로 리소스를 관리할 수 있습니다. 가상 머신을 시작하는 명령을 추가해 보겠습니다. Azure 구독에서 VM을 선택할 수 있지만, 지금은 해당 이름을 Runbook에 하드 코딩합니다. 여러 구독에서 리소스를 관리하는 경우 [Get-AzContext](/powershell/module/az.accounts/get-azcontext) cmdlet에서 `AzContext` 매개 변수를 사용해야 합니다.

1. 아래와 같이 [Start-AzVM](/powershell/module/Az.Compute/Start-AzVM) cmdlet에 대한 호출을 입력하여 시작할 VM의 이름과 리소스 그룹 이름을 제공합니다. 

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
   # Ensures that you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint

   $AzureContext = Get-AzSubscription -SubscriptionId $Conn.SubscriptionID

   Start-AzVM -Name 'VMName' -ResourceGroupName 'ResourceGroupName' -AzContext $AzureContext
   }
   ```

2. Runbook을 저장한 다음, 테스트할 수 있도록 **테스트 창** 을 클릭합니다.

3. **시작** 을 클릭하여 테스트를 시작합니다. 완료되면 VM이 시작되었는지 확인합니다.

## <a name="step-7---add-an-input-parameter-to-the-runbook"></a>7 단계 - runbook에 입력 매개 변수를 추가 합니다.

Runbook에서 현재 Runbook에 하드 코딩된 VM을 시작합니다. Runbook이 시작될 때 VM을 지정할 수 있는 경우 더 유용합니다. 입력 매개 변수를 Runbook에 추가하여 해당 기능을 제공해 보겠습니다.

1. `VMName` 및 `ResourceGroupName` 매개 변수에 대한 변수를 Runbook에 추가하고, `Start-AzVM` cmdlet에서 아래와 같이 변수를 사용합니다.

   ```powershell-interactive
   workflow MyFirstRunbook-Workflow
   {
    Param(
     [string]$VMName,
     [string]$ResourceGroupName
    )
   # Ensures you do not inherit an AzContext in your runbook
   Disable-AzContextAutosave -Scope Process

   $Conn = Get-AutomationConnection -Name AzureRunAsConnection
   Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
   Start-AzVM -Name $VMName -ResourceGroupName $ResourceGroupName
   }
   ```

2. Runbook을 저장하고 테스트 창을 엽니다. 이제 테스트에 있는 두 개의 입력 변수에 값을 제공할 수 있습니다.

3. 창을 닫습니다.

4. **게시** 를 클릭하여 Runbook의 새 버전을 게시합니다.

5. 시작한 VM을 중지합니다.

6. **시작** 을 클릭하여 runbook을 시작합니다. 

7. 시작할 VM에 대한 **VMNAME** 및 **RESOURCEGROUPNAME** 값을 입력합니다.

   ![Runbook 시작](../media/automation-tutorial-runbook-textual/automation-pass-params.png)

8. Runbook이 완료되면 VM이 시작되었는지 확인합니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 PowerShell 워크플로 Runbook을 만들었습니다. Python 3 Runbook에 대한 자세한 내용은 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: Python 3 Runbook(미리 보기) 만들기](automation-tutorial-runbook-textual-python-3.md)
