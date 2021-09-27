---
title: Azure Automation Runbook 형식
description: 이 문서에서는 Azure Automation에서 사용할 수 있는 Runbook의 유형 및 사용할 형식을 결정하기 위한 고려 사항을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 06/10/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 44923cd63676a6eb2fa589c66726f1c14c76896c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124744814"
---
# <a name="azure-automation-runbook-types"></a>Azure Automation Runbook 형식

Azure Automation 프로세스 자동화 기능은 다음 표에 정의된 대로 여러 유형의 Runbook을 지원합니다. 프로세스 자동화 환경에 대한 자세한 내용은 [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)을 참조하세요.

| Type | Description |
|:--- |:--- |
| [그래픽](#graphical-runbooks)|Windows PowerShell을 기반으로 하며 Azure Portal의 그래픽 편집기로 완전하게 생성 및 편집된 그래픽 Runbook입니다. |
| [그래픽 PowerShell 워크플로](#graphical-runbooks)|Windows PowerShell 워크플로를 기반으로 하며 Azure Portal의 그래픽 편집기로 완전하게 생성 및 편집된 그래픽 Runbook입니다. |
| [PowerShell](#powershell-runbooks) |Windows PowerShell 스크립팅을 기반으로 하는 텍스트 Runbook입니다. |
| [PowerShell 워크플로](#powershell-workflow-runbooks)|Windows PowerShell 워크플로 스크립팅을 기반으로 하는 텍스트 Runbook입니다. |
| [Python](#python-runbooks) |Python 스크립팅을 기반으로 하는 텍스트 Runbook입니다. |

특정 Runbook에 사용할 형식을 결정할 때 다음과 같은 사항을 고려합니다.

* Runbook을 그래픽에서 텍스트 형식으로 또는 그 반대로 변환할 수 없습니다.
* 형식이 다른 Runbook을 자식 Runbook으로 사용하는 경우 제한 사항이 있습니다. 자세한 내용은 [Azure Automation의 자식 Runbook](automation-child-runbooks.md)을 참조하세요.

## <a name="graphical-runbooks"></a>그래픽 Runbook

Azure Portal에서 그래픽 편집기를 사용하여 그래픽 및 그래픽 PowerShell 워크플로 Runbook을 만들고 편집할 수 있습니다. 그러나 다른 도구를 사용하여 이 유형의 Runbook을 만들거나 편집할 수 없습니다. 그래픽 Runbook의 주요 기능:

* Automation 계정의 파일로 내보낸 다음 다른 Automation 계정으로 가져옵니다.
* PowerShell 코드를 생성합니다.
* 가져오는 동안 그래픽 PowerShell 워크플로 Runbook으로 변환되거나 그 반대로 변환됩니다.

### <a name="advantages"></a>장점

* 시각적 삽입-링크-구성 제작 모델을 사용합니다.
* 프로세스를 통해 데이터가 흐르는 방식에 집중합니다.
* 관리 과정을 시각적으로 나타냅니다.
* 다른 Runbook을 자식 Runbook으로 포함하여 고급 워크플로를 만듭니다.
* 모듈식 프로그래밍을 장려합니다.

### <a name="limitations"></a>제한 사항

* Azure Portal 외부에서 만들거나 편집할 수 없습니다.
* 복잡한 논리를 실행하기 위해 PowerShell 코드를 포함하는 코드 작업이 필요할 수 있습니다.
* [텍스트 형식](automation-runbook-types.md) 중 하나로 변환할 수 없으며 텍스트 Runbook을 그래픽 형식으로 변환할 수 없습니다. 
* 그래픽 워크플로가 만든 PowerShell 코드를 보거나 직접 편집할 수 없습니다. 코드 작업에서 만든 코드를 볼 수 있습니다.
* Linux Hybrid Runbook Worker에서 Runbook을 실행할 수 없습니다. [Hybrid Runbook Worker를 사용하여 데이터 센터 또는 클라우드의 리소스 자동화](automation-hybrid-runbook-worker.md)를 참조하세요.

## <a name="powershell-runbooks"></a>PowerShell Runbook

PowerShell Runbook은 Windows PowerShell을 기반으로 합니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다.  오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로의 다른 복잡성 없이 PowerShell 코드로 모든 복잡한 로직을 구현합니다.
* 실행 전에 컴파일이 필요 없기 때문에 PowerShell 워크플로 Runbook보다 빨리 시작됩니다.
* Windows 및 Linux 둘 다 Azure 및 Hybrid Runbook Worker에서 실행합니다.

### <a name="limitations"></a>제한 사항

* PowerShell 스크립팅에 대해 잘 알아야 합니다.
* Runbook에서는 [병렬 처리](automation-powershell-workflow.md#use-parallel-processing)를 사용하여 여러 작업을 병렬로 실행할 수 없습니다.
* Runbook에서는 오류 발생 시 [검사점](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)을 사용하여 Runbook을 다시 시작할 수 없습니다.
* 새 작업을 만드는 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook) cmdlet을 사용하여 자식 Runbook으로서의 PowerShell 워크플로 Runbook 및 그래픽 Runbook만 포함할 수 있습니다.
* Runbook은 PowerShell [#Requires](/powershell/module/microsoft.powershell.core/about/about_requires) 문을 사용할 수 없으며, Azure 샌드박스 또는 Hybrid Runbook Workers에서 지원되지 않으며 작업이 실패합니다.

### <a name="known-issues"></a>알려진 문제

PowerShell Runbook에 대해 현재 알려진 문제는 다음과 같습니다.

* PowerShell Runbook이 null 값을 갖는 암호화되지 않은 [변수 자산](./shared-resources/variables.md)을 가져올 수 없습니다.
* PowerShell Runbook은 이름에 `*~*`가 포함된 변수 자산을 검색할 수 없습니다.
* PowerShell Runbook의 반복적인 [Get-Process](/powershell/module/microsoft.powershell.management/get-process) 작업은 80회 반복 후에 크래시가 발생할 수 있습니다.
* PowerShell Runbook이 한 번에 대량의 데이터를 출력 스트림에 쓰려고 하면 실패할 수 있습니다. Runbook에서 큰 개체로 작업하는 데 필요한 정보만 출력하면 일반적으로 이 문제를 해결할 수 있습니다. 예를 들어 제한 없이 `Get-Process`를 사용하는 대신 `Get-Process | Select ProcessName, CPU`에서와 같이 cmdlet이 필요한 매개 변수만 출력하도록 할 수 있습니다.

## <a name="powershell-workflow-runbooks"></a>PowerShell 워크플로 Runbook

PowerShell 워크플로 Runbook은 [Windows PowerShell 워크플로](automation-powershell-workflow.md)를 기반으로 하는 텍스트 Runbook입니다. Azure 포털의 텍스트 편집기를 사용하여 Runbook을 직접 편집합니다. 오프라인 텍스트 편집기도 사용할 수 있고 Azure Automation으로 [Runbook 가져오기](manage-runbooks.md) 가 가능합니다.

### <a name="advantages"></a>장점

* PowerShell 워크플로 코드로 모든 복잡한 로직을 구현합니다.
* 오류 발생 시 [검사점](automation-powershell-workflow.md#use-checkpoints-in-a-workflow)을 사용하여 작업을 다시 시작합니다.
* [병렬 처리](automation-powershell-workflow.md#use-parallel-processing)를 사용하여 여러 작업을 병렬로 수행합니다.
* 다른 그래픽 Runbook과 PowerShell 워크플로 Runbook을 자식 Runbook으로 포함하여 고급 워크플로를 만들 수 있습니다.

### <a name="limitations"></a>제한 사항

* PowerShell 워크플로를 잘 알아야 합니다.
* Runbook은 [역직렬화된 개체](automation-powershell-workflow.md#deserialized-objects)와 같은 PowerShell 워크플로의 부가적인 복잡성을 다루어야 합니다.
* Runbook은 실행 전에 컴파일해야 하기 때문에 PowerShell Runbook보다 시작 시간이 깁니다.
* `Start-AzAutomationRunbook` cmdlet을 사용하여 PowerShell Runbook을 자식 Runbook으로만 포함할 수 있습니다.
* Runbook은 Linux Hybrid Runbook Worker에서 실행할 수 없습니다.

## <a name="python-runbooks"></a>Python Runbook

Python Runbook은 Python 2 및 Python 3에서 컴파일됩니다. Python 3 Runbook은 현재 미리 보기로 제공됩니다. Azure Portal의 텍스트 편집기를 사용하여 Runbook의 코드를 직접 편집할 수 있습니다. 또한 오프라인 텍스트 편집기를 사용할 수 있고 Azure Automation으로 [Runbook을 가져올 수](manage-runbooks.md) 있습니다.

Python 3 Runbook은 다음과 같은 Azure 글로벌 인프라에서 지원됩니다.

* Azure 글로벌
* Azure Government

### <a name="advantages"></a>장점

* 강력한 Python 라이브러리를 사용합니다.
* Azure 또는 Hybrid Runbook Worker에서 실행할 수 있습니다.
* Python 2의 경우 [Python 2.7](https://www.python.org/downloads/release/latest/python2)이 설치되어 있어야 Windows Hybrid Runbook Worker가 지원됩니다.
* Python 3 Cloud Jobs의 경우 Python 3.8 버전이 지원됩니다. 서로 다른 버전에서 코드가 호환되는 경우에는 3.x 버전의 스크립트 및 패키지가 작동할 수 있습니다.  
* Windows 컴퓨터의 Python 3 하이브리드 작업의 경우 사용하려는 어떤 3.x 버전도 설치하도록 선택할 수 있습니다.  
* Linux 컴퓨터의 Python 3 하이브리드 작업의 경우 컴퓨터에 설치된 Python 3 버전에 따라 DSC OMSConfig 및 Linux Hybrid Worker를 실행할 수 있습니다. Linux 컴퓨터에는 3.6을 설치하는 것이 좋습니다. 하지만 Python 3 버전 간에 호환성이 손상되는 메서드 서명 또는 계약 변경이 없다면 다른 버전도 사용할 수 있습니다.

### <a name="limitations"></a>제한 사항

* Python 스크립팅에 대해 잘 알아야 합니다.
* 타사 라이브러리를 사용하려면 Automation 계정으로 [패키지를 가져와야](python-packages.md) 합니다.
* PowerShell/PowerShell Workflow에서 **Start-AutomationRunbook** cmdlet을 사용하여 Python 3 Runbook(미리 보기)을 시작할 수 없습니다. 이 제한 사항을 해결하려면 Az.Automation 모듈의  **Start-AzAutomationRunbook** cmdlet 또는 AzureRm.Automation 모듈의  **Start-AzureRmAutomationRunbook** cmdlet을 사용하면 됩니다.  
* Azure Automation은  **sys.stderr** 을 지원하지 않습니다.

### <a name="known-issues"></a>알려진 문제

클라우드 작업의 경우 Python 3 작업이 예외 메시지 `invalid interpreter executable path`로 실패하는 경우가 있습니다. 작업이 지연되거나 10분 이상 시작되거나 **Start-AutomationRunbook** 을 사용하여 Python 3 Runbook을 시작하는 경우 이 예외가 표시될 수 있습니다. 작업이 지연되는 경우 Runbook을 다시 시작하는 것으로 충분합니다. 다음 단계를 사용하는 경우 하이브리드 작업은 문제 없이 작동해야 합니다.

1. `PYTHON_3_PATH`라는 새 환경 변수를 만들고 설치 폴더를 지정합니다. 예를 들어 설치 폴더가 `C:\Python3`인 경우 이 경로를 변수에 추가해야 합니다.
1. 환경 변수를 설정하면 컴퓨터를 다시 시작합니다.

## <a name="next-steps"></a>다음 단계

* PowerShell Runbook에 대해 자세히 알아보려면 [자습서: PowerShell Runbook 만들기](./learn/powershell-runbook-managed-identity.md)를 참조하세요.
* PowerShell 워크플로 Runbook에 대한 자세한 내용은 [자습서: PowerShell 워크플로 Runbook 만들기](learn/automation-tutorial-runbook-textual.md)를 참조하세요.
* 그래픽 Runbook에 대한 자세한 내용은 [자습서: 그래픽 Runbook 만들기](./learn/powershell-runbook-managed-identity.md)를 참조하세요.
* Python Runbook에 대한 자세한 내용은 [자습서: Python Runbook 만들기](./learn/automation-tutorial-runbook-textual-python-3.md)를 참조하세요.