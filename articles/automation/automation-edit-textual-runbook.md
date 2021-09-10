---
title: Azure Automation에서 텍스트 Runbook 편집
description: 이 문서에서는 Azure Automation 텍스트 편집기를 사용하여 PowerShell 및 PowerShell 워크플로 Runbook으로 작업하는 방법을 설명합니다.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
manager: carmonm
ms.openlocfilehash: 48df3f834601410caf83d818ac75b1b7b7e68dc2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114468246"
---
# <a name="edit-textual-runbooks-in-azure-automation"></a>Azure Automation에서 텍스트 Runbook 편집

Azure Automation에서 텍스트 편집기를 사용하여 [PowerShell Runbook](automation-runbook-types.md#powershell-runbooks) 및 [PowerShell 워크플로 Runbook](automation-runbook-types.md#powershell-workflow-runbooks)을 편집할 수 있습니다. 이 편집기에는 IntelliSense와 같은 다른 코드 편집기의 일반적인 기능이 있습니다. 또한 Runbook에 공통적으로 사용되는 리소스에 액세스하는 데 도움이 되는 추가 특수 기능과 색 구분 기능도 있습니다. 

텍스트 편집기에는 cmdlet, 자산 및 자식 Runbook에 대한 코드를 Runbook에 삽입하는 기능이 포함되어 있습니다. 코드를 직접 입력하지 않고 사용 가능한 리소스 목록에서 선택하여 편집기가 적합한 코드를 Runbook에 삽입하도록 할 수 있습니다.

Azure Automation의 각 Runbook에는 초안과 게시 등 두 버전이 있습니다. Runbook의 초안 버전을 편집한 다음 실행할 수 있도록 게시합니다. 게시된 버전은 편집할 수 없습니다. 자세한 내용은 [Runbook 게시](manage-runbooks.md#publish-a-runbook)를 참조하세요.

이 문서에서는 이 편집기를 사용하여 다양한 기능을 수행하기 위한 상세 단계를 제공합니다. 이는 [그래픽 Runbook](automation-runbook-types.md#graphical-runbooks)에는 적용되지 않습니다. 이러한 Runbook으로 작업하려면 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.

> [!IMPORTANT] 
> Az 모듈을 통해 실행되도록 설계된 스크립트에는 "AzureRm" 키워드를 포함하지 마십시오. 주석에도 키워드를 포함하면 AzureRm이 로드된 다음, Az 모듈과 충돌할 수 있습니다.

## <a name="edit-a-runbook-with-the-azure-portal"></a>Azure Portal을 사용하여 Runbook 편집

1. Azure Portal에서 Automation 계정을 선택합니다.
2. **프로세스 자동화** 에서 **Runbook** 을 선택하여 Runbook 목록을 엽니다.
3. 편집할 Runbook을 선택한 다음, **편집** 을 클릭합니다.
4. Runbook을 편집합니다.
5. 편집이 완료되면 **저장** 을 클릭합니다.
6. Runbook의 최신 초안 버전을 게시하려면 **게시** 를 클릭합니다.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Runbook에 cmdlet 삽입

1. 텍스트 편집기의 캔버스에서 cmdlet을 배치하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **Cmdlet** 노드를 확장합니다.
3. 사용할 cmdlet을 포함하는 모듈을 확장합니다.
4. 삽입할 cmdlet 이름을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가** 를 선택합니다. cmdlet에 둘 이상의 매개 변수를 설정한 경우 기본 설정이 추가됩니다. cmdlet를 확장하여 다른 매개 변수 설정을 선택할 수 있습니다.
5. cmdlet의 코드는 전체 매개 변수 목록과 함께 삽입됩니다.
6. 필요한 매개 변수에 꺾쇠 괄호(<>)로 묶인 값 대신 적합한 값을 입력합니다. 필요 없는 매개 변수는 제거합니다.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Runbook에 자식 Runbook에 대한 코드 삽입

1. 텍스트 편집기의 캔버스에서 [자식 Runbook](automation-child-runbooks.md)의 코드를 배치하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **Runbook** 노드를 확장합니다.
3. 삽입할 Runbook을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가** 를 선택합니다.
4. 자식 Runbook의 코드가 Runbook 매개 변수의 자리 표시자와 함께 삽입됩니다.
5. 자리 표시자를 각 매개 변수에 적합한 값으로 바꿉니다.

### <a name="insert-an-asset-into-a-runbook"></a>Runbook에 자산 삽입

1. 텍스트 편집기의 캔버스 컨트롤에서 자식 Runbook의 코드를 배치하려는 위치에 커서를 놓습니다.
2. 라이브러리 컨트롤에서 **자산** 노드를 확장합니다.
3. 원하는 자산 형식에 대한 노드를 확장합니다.
4. 삽입할 자산 이름을 마우스 오른쪽 단추로 클릭하고 **캔버스에 추가** 를 선택합니다. [변수 자산](./shared-resources/variables.md)의 경우 변수를 가져올지 또는 설정할지 여부에 따라 **"변수 가져오기"를 캔버스에 추가** 또는 **"변수 설정"을 캔버스에 추가** 를 선택합니다.
5. 자산에 대한 코드가 Runbook에 삽입됩니다.

## <a name="edit-an-azure-automation-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Azure Automation Runbook 편집

Windows PowerShell을 사용하여 Runbook을 편집하려면 원하는 편집기를 사용하고 Runbook을 **.ps1** 파일에 저장합니다. [Export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) cmdlet을 사용하여 Runbook의 콘텐츠를 검색할 수 있습니다. [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) cmdlet을 사용하여 기존 초안 Runbook을 수정된 Runbook으로 바꿀 수 있습니다.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook의 콘텐츠 검색

다음 예제 명령에서는 Runbook에 대한 스크립트를 검색하고 스크립트 파일에 저장하는 방법을 보여줍니다. 이 예에서는 초안 버전이 검색됩니다. Runbook의 게시 버전을 검색할 수도 있습니다. 단, 이 버전을 변경할 수는 없습니다.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Windows PowerShell을 사용하여 Runbook의 콘텐츠 변경

다음 샘플 명령에서는 스크립트 파일의 콘텐츠로 기존 Runbook 콘텐츠를 대체하는 방법을 보여줍니다. 

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>다음 단계

* [Azure Automation에서 Runbook 관리](manage-runbooks.md)
* [PowerShell 워크플로 알아보기](automation-powershell-workflow.md)
* [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)
* [인증서](./shared-resources/certificates.md)
* [연결](automation-connections.md).
* [자격 증명](./shared-resources/credentials.md).
* [일정](./shared-resources/schedules.md).
* [변수](./shared-resources/variables.md).
* [PowerShell cmdlet 참조](/powershell/module/az.automation)
