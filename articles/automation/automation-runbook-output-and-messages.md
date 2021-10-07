---
title: Runbook 출력 및 메시지 스트림 구성
description: 이 문서에서는 오류 처리 논리를 구현하는 방법을 설명하고 Azure Automation Runbook의 출력 및 메시지 스트림에 대해 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 11/03/2020
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f2392484c8ee7c72daa0da7c3ff9632a981a52ec
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129619693"
---
# <a name="configure-runbook-output-and-message-streams"></a>Runbook 출력 및 메시지 스트림 구성

대부분의 Azure Automation Runbook은 일종의 출력 양식을 갖습니다. 이 출력은 사용자에 대한 오류 메시지일 수도 있고 또는 다른 Runbook과 함께 사용하도록 설정된 복합 개체일 수도 있습니다. Windows PowerShell은 [여러 스트림](/powershell/module/microsoft.powershell.core/about/about_redirection) 제공하여 스크립트 또는 워크플로에서 출력을 보냅니다. Azure Automation은 이러한 스트림에서 각각 다르게 작동합니다. Runbook을 만들 때는 스트림 사용 모범 사례를 따라야 합니다.

다음 표에는 각 스트림에 대한 간략한 설명과 Azure Portal에서 게시된 Runbook에 대한 스트림의 동작과 [Runbook 테스트](./manage-runbooks.md) 중 스트림의 동작이 나와 있습니다. 출력 스트림은 Runbook 간 통신에 사용되는 주 스트림입니다. 다른 스트림은 사용자에게 정보를 전달하기 위한 메시지 스트림으로 분류됩니다.

| STREAM | Description | 게시 날짜 | 테스트 |
|:--- |:--- |:--- |:--- |
| Error |사용자를 위한 오류 메시지입니다. 예외와 달리, Runbook은 기본적으로 오류 메시지 이후에 계속됩니다. |작업 기록에 기록됨 |테스트 출력 창에 표시됨 |
| 디버그 |대화형 사용자를 위한 메시지입니다. Runbook에서 사용하지 않아야 합니다. |작업 기록에 기록되지 않음 |테스트 출력 창에 표시되지 않음 |
| 출력 |다른 runbook에서 사용할 수 있도록 의도된 개체입니다. |작업 기록에 기록됨 |테스트 출력 창에 표시됨 |
| 진행 |runbook의 각 작업 전과 후에 레코드를 자동으로 생성했습니다. 자체 진행률 레코드는 대화형 사용자를 위한 것이기 때문에 Runbook이 만들려고 하지 않아야 합니다. |Runbook에 진행률 로깅이 설정되어 있는 경우 작업 기록에 기록됨 |테스트 출력 창에 표시되지 않음 |
| 자세히 |일반 또는 디버깅 정보를 제공하는 메시지입니다. |Runbook에 자세한 정보 표시 로깅이 설정되어 있는 경우 작업 기록에 기록됨 |Runbook에서 `VerbosePreference` 변수가 계속으로 설정되어 있는 경우에만 테스트 출력 창에 표시됨 |
| Warning |사용자를 위한 경고 메시지입니다. |작업 기록에 기록됨 |테스트 출력 창에 표시됨 |

## <a name="use-the-output-stream"></a>출력 스트림 사용

출력 스트림이 올바르게 실행되면 스크립트 또는 워크플로에 의해 생성된 개체의 출력을 위해 사용됩니다. Azure Automation에서 이 스트림은 주로 [현재 Runbook](automation-child-runbooks.md)을 호출하는 부모 Runbook에서 사용하는 개체에서 사용됩니다. 부모가 [인라인으로 Runbook을 호출](automation-child-runbooks.md#call-a-child-runbook-by-using-inline-execution)하면 자식은 출력 스트림에서 부모에게 데이터를 반환합니다.

Runbook은 다른 Runbook에 의해 호출되지 않는 경우에만 출력 스트림을 사용하여 클라이언트에 일반 정보를 커뮤니케이션합니다. Runbook에서 [자세한 정보 표시 스트림](#write-output-to-verbose-stream)을 사용하여 사용자에게 일반 정보를 전달하는 것이 좋습니다.

[Write-Output](/powershell/module/microsoft.powershell.utility/write-output)을 사용하여 Runbook이 출력 스트림으로 데이터를 쓰도록 설정합니다. 또는 스크립트에서 개체를 자체 줄에 배치할 수도 있습니다.

```powershell
#The following lines both write an object to the output stream.
Write-Output -InputObject $object
$object
```

### <a name="handle-output-from-a-function"></a>함수에서 출력 처리

Runbook 함수에서 출력 스트림에 기록하면 출력은 Runbook으로 다시 전달됩니다. Runbook이 해당 출력을 변수에 할당하면 출력이 출력 스트림에 기록되지 않습니다. 함수 내에서 다른 스트림에 기록하면 Runbook의 해당하는 스트림에 기록됩니다. 아래의 샘플 PowerShell 워크플로 Runbook을 살펴보겠습니다.

```powershell
Workflow Test-Runbook
{
  Write-Verbose "Verbose outside of function" -Verbose
  Write-Output "Output outside of function"
  $functionOutput = Test-Function
  $functionOutput

  Function Test-Function
  {
    Write-Verbose "Verbose inside of function" -Verbose
    Write-Output "Output inside of function"
  }
}
```

Runbook 작업의 출력 스트림은 다음과 같습니다.

```output
Output inside of function
Output outside of function
```

Runbook 작업의 자세한 정보 표시 스트림은 다음과 같습니다.

```output
Verbose outside of function
Verbose inside of function
```

Runbook을 게시한 다음 시작하기 전에 자세한 정보 표시 스트림 출력을 얻으려면 Runbook 설정에서도 자세한 정보 표시 로깅을 켜야 합니다.

### <a name="declare-output-data-type"></a>출력 데이터 형식 선언

다음은 출력 데이터 형식의 예입니다.

* `System.String`
* `System.Int32`
* `System.Collections.Hashtable`
* `Microsoft.Azure.Commands.Compute.Models.PSVirtualMachine`

#### <a name="declare-output-data-type-in-a-workflow"></a>워크플로에서 출력 데이터 형식 선언

워크플로는 [OutputType 특성](/powershell/module/microsoft.powershell.core/about/about_functions_outputtypeattribute)을 사용하여 출력의 데이터 형식을 지정합니다. 이 특성은 런타임 시 영향을 주지 않지만 Runbook의 예상 출력 디자인 타임에 표시를 제공합니다. Runbook용 도구 집합이 계속 진화하기 때문에 디자인 타임에 출력 데이터 형식을 선언하는 일이 더욱 중요해지고 있습니다. 따라서 생성하는 모든 Runbook에 이 선언을 포함하는 것이 좋습니다.

다음 샘플 runbook은 문자열 개체를 출력하고 해당 출력 형식의 선언을 포함합니다. Runbook이 특정 유형의 배열을 출력하면 형식의 배열과 달리 형식을 지정해야 합니다.

```powershell
Workflow Test-Runbook
{
  [OutputType([string])]

  $output = "This is some string output."
  Write-Output $output
}
```

#### <a name="declare-output-data-type-in-a-graphical-runbook"></a>그래픽 Runbook에서 출력 데이터 형식 선언

그래픽 또는 그래픽 PowerShell 워크플로 Runbook에서 출력 형식을 선언하려면 **입력 및 출력** 메뉴 옵션을 선택하고 출력 형식을 입력하면 됩니다. 부모 Runbook에서 참조하는 경우 쉽게 식별할 수 있도록 전체 .NET 클래스 이름을 사용하는 것이 좋습니다. 전체 이름을 사용하면 Runbook의 데이터 버스에 대한 해당 클래스의 모든 속성이 노출되고 속성이 조건부 논리 및 로깅을 위해 사용되거나 Runbook의 다른 활동에 대한 값으로 참조될 경우 향상된 유연성을 제공합니다.<br> ![Runbook 입력 및 출력 옵션](media/automation-runbook-output-and-messages/runbook-menu-input-and-output-option.png)

> [!NOTE]
> 입력 및 출력 속성 창의 **출력 형식** 필드에 값을 입력한 후에는 입력한 내용이 인식될 수 있도록 컨트롤 바깥쪽을 클릭해야 합니다.

다음 예제에서는 입력 및 출력 기능을 보여 주는 두 개의 그래픽 Runbook을 볼 수 있습니다. 모듈식 Runbook 디자인 모델을 적용하는 경우 실행 계정을 사용하여 Azure로 인증을 관리하는 인증 Runbook 템플릿으로 실행되는 하나의 Runbook이 있습니다. 일반적으로 주어진 시나리오를 자동화하는 핵심 논리를 수행하는 두 번째 Runbook은 여기서는 Runbook 인증 템플릿을 실행합니다. 테스트 출력 창에 결과를 표시합니다. 정상적인 상황에서 자식 Runbook의 출력을 활용하는 리소스에 대해 이 Runbook이 작업을 수행하도록 합니다.

**AuthenticateTo-Azure** Runbook의 기본 논리는 다음과 같습니다.<br> ![Runbook 템플릿 예제 인증](media/automation-runbook-output-and-messages/runbook-authentication-template.png)할 경우 Azure 관리 포털에서 각 스트림 및 동작에 대한 간략한 설명을 제공합니다.

Runbook은 인증 프로필 속성을 반환하는 출력 형식 `Microsoft.Azure.Commands.Profile.Models.PSAzureContext`를 포함합니다.<br> ![Runbook 출력 형식 예제](media/automation-runbook-output-and-messages/runbook-input-and-output-add-blade.png)

이 Runbook은 단순하지만 여기에서 살펴보아야 하는 한 가지 구성 항목이 있습니다. 마지막 작업은 `Write-Output` cmdlet을 실행하고 `Inputobject` 매개 변수에 대해 PowerShell 식을 사용하여 변수에 프로필 데이터를 기록합니다. 이 매개 변수는 `Write-Output`에 필요합니다.

이 예제에서 **Test-ChildOutputType** 이라는 두 번째 Runbook의 경우 단순히 두 개의 작업을 정의합니다.<br> ![예제 자식 출력 형식 Runbook](media/automation-runbook-output-and-messages/runbook-display-authentication-results-example.png)

첫 번째 작업은 **AuthenticateTo-Azure** Runbook을 호출합니다. 두 번째 작업은 **데이터 원본** 을 **작업 출력** 으로 설정하여 `Write-Verbose` cmdlet을 실행합니다. 또한 **필드 경로** 는 **AuthenticateTo-Azure** Runbook의 컨텍스트 출력인 **Context.Subscription.SubscriptionName** 으로 설정됩니다.<br> ![Write-Verbose Cmdlet 매개 변수 데이터 원본](media/automation-runbook-output-and-messages/runbook-write-verbose-parameters-config.png)

결과 출력은 구독의 이름입니다.<br> ![Test-ChildOutputType Runbook 결과](media/automation-runbook-output-and-messages/runbook-test-childoutputtype-results.png)

## <a name="working-with-message-streams"></a>메시지 스트림 작업

메시지 스트림은 출력 스트림과 달리 사용자에게 정보를 커뮤니케이션합니다. 다양한 유형의 정보를 위한 다양한 메시지 스트림이 있으며, Azure Automation은 각 스트림을 서로 다른 방식으로 처리합니다.

### <a name="write-output-to-warning-and-error-streams"></a>경고 및 오류 스트림에 출력 쓰기

경고 및 오류 스트림은 Runbook에서 발생하는 문제를 로깅합니다. Azure Automation은 Runbook을 실행할 때 이러한 스트림을 작업 기록에 기록합니다. Automation은 Runbook이 테스트될 때 Azure Portal의 테스트 출력 창에 스트림을 포함합니다.

기본적으로 Runbook은 경고나 오류가 발생한 후에도 계속 실행됩니다. 메시지를 만들기 전에 Runbook의 [기본 설정 변수](#work-with-preference-variables)를 설정하여 경고 또는 오류가 발생할 경우 Runbook이 일시 중단되어야 할지를 지정할 수 있습니다. 예를 들어 Runbook이 예외와 마찬가지로 오류가 발생한 경우에도 일시 중단되도록 하려면 `ErrorActionPreference` 변수를 중지로 설정합니다.

[Write-Warning](/powershell/module/microsoft.powershell.utility/write-warning) 또는 [Write-Error](/powershell/module/microsoft.powershell.utility/write-error) cmdlet를 사용하여 경고 또는 오류 메시지를 만듭니다. 활동도 경고 및 오류 스트림에 기록할 수 있습니다.

```powershell
#The following lines create a warning message and then an error message that will suspend the runbook.

$ErrorActionPreference = "Stop"
Write-Warning -Message "This is a warning message."
Write-Error -Message "This is an error message that will stop the runbook because of the preference variable."
```

### <a name="write-output-to-debug-stream"></a>디버그 스트림에 출력 쓰기

Azure Automation은 대화형 사용자를 위해 디버그 메시지 스트림을 사용합니다. 기본적으로 Azure Automation은 디버그 스트림 데이터를 캡처하지 않으며 Runbook이 캡처하도록 구성된 경우 자세한 정보 표시 데이터뿐만 아니라 출력, 오류, 경고 데이터만 캡처됩니다.

디버그 스트림 데이터를 캡처하려면 Runbook에서 다음 두 가지 작업을 수행해야 합니다.

1. 디버그 메시지가 나타날 때마다 PowerShell에 계속 진행하도록 지시하는 `$GLOBAL:DebugPreference="Continue"`변수를 설정합니다.  **$GLOBAL:** 부분은 문이 실행될 때 스크립트가 있는 로컬 범위가 아닌 전역 범위에서 이 작업을 수행하도록 PowerShell에 지시합니다.

1. 캡처하지 않는 디버그 스트림을 ‘출력’과 같이 캡처할 스트림으로 리디렉션합니다. 이 작업은 실행할 문에 대해 PowerShell 리디렉션을 설정하여 수행합니다. PowerShell 리디렉션에 대한 자세한 내용은 [리디렉션 정보](/powershell/module/microsoft.powershell.core/about/about_redirection)를 참조하세요.

#### <a name="examples"></a>예

이 예제에서 Runbook은 두 개의 서로 다른 스트림을 출력하기 위해 `Write-Output` 및 `Write-Debug` cmdlet을 사용하여 구성합니다.

```powershell
Write-Output "This is an output message." 
Write-Debug "This is a debug message."
```

이 Runbook을 그대로 실행하는 경우 Runbook 작업의 출력 창에서 다음 출력을 스트림합니다.

```output
This is an output message.
```

이 예제에서 Runbook은 앞의 예제와 유사하게 구성됩니다. 단, `$GLOBAL:DebugPreference="Continue"` 문은 `Write-Debug` 문의 끝에 `5>&1`을 추가하여 포함됩니다.

```powershell
Write-Output "This is an output message." 
$GLOBAL:DebugPreference="Continue" 
Write-Debug "This is a debug message." 5>&1
```

이 Runbook을 실행하는 경우 Runbook 작업의 출력 창에서 다음 출력을 스트림합니다.

```output
This is an output message.
This is a debug message.
```

`$GLOBAL:DebugPreference="Continue"` 문이 디버그 메시지를 표시하도록 PowerShell에 지시하고 `Write-Debug` 문 끝에 `5>&1`을 추가하면 PowerShell에 스트림 5(디버그)를 스트림 1(출력)로 리디렉션하도록 지시하기 때문입니다.

### <a name="write-output-to-verbose-stream"></a>자세한 정보 표시 스트림에 출력 쓰기

자세한 정보 표시 메시지 스트림은 Runbook 작업에 대한 일반 정보를 지원합니다. Runbook에서는 디버그 스트림을 사용할 수 없으므로 디버그 정보를 표시하려면 자세한 정보 표시 메시지를 사용해야 합니다.

기본적으로 작업 기록은 성능상의 이유로 게시된 Runbook의 자세한 정보 표시 메시지를 저장하지 않습니다. 자세한 정보 표시 메시지를 저장하려면 Azure Portal **구성** 탭에서 **상세 레코드 기록** 설정을 사용하여 게시된 Runbook이 자세한 정보 표시 메시지를 로깅하도록 구성합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다. 대부분의 경우에는 자세한 정보 표시 레코드를 기록하지 않는 기본 설정을 유지해야 합니다.

[Runbook을 테스트](./manage-runbooks.md)할 경우 Runbook이 자세한 정보 표시 레코드를 기록하도록 구성되면 자세한 정보 표시 메시지는 표시되지 않습니다. [Runbook을 테스트](./manage-runbooks.md)하는 동안 자세한 정보 표시 메시지를 표시하려면 `VerbosePreference` 변수를 계속으로 설정해야 합니다. 이 변수를 설정하면 자세한 정보 표시 메시지가 Azure Portal의 테스트 출력 창에 표시됩니다.

다음 코드는 [Write-Verbose](/powershell/module/microsoft.powershell.utility/write-verbose) cmdlet을 사용하여 자세한 정보 표시 메시지를 만듭니다.

```powershell
#The following line creates a verbose message.

Write-Verbose -Message "This is a verbose message."
```

## <a name="handle-progress-records"></a>진행률 레코드 처리

Azure Portal의 **구성** 탭을 사용하여 Runbook이 진행률 레코드를 로깅하도록 구성할 수 있습니다. 기본 설정은 성능 최대화를 위해 레코드를 로깅하지 않는 것입니다. 대부분의 경우에는 기본 설정을 유지해야 합니다. 옵션을 켜서 문제를 해결하거나 runbook 디버그합니다.

진행률 레코드 로깅을 사용하도록 설정하면 Runbook은 각 작업이 실행되기 전과 후에 작업 기록에 레코드를 기록합니다. Runbook을 테스트하는 경우에는 Runbook이 진행률 레코드를 로깅하도록 구성되어 있어도 진행률 메시지가 표시되지 않습니다.

> [!NOTE]
> [Write-progress](/powershell/module/microsoft.powershell.utility/write-progress) cmdlet은 대화형 사용자로 사용하기 위한 것이기 때문에 Runbook에 유효하지 않습니다.

## <a name="work-with-preference-variables"></a>기본 설정 변수로 작업

Runbook에서 특정 Windows PowerShell [기본 설정 변수](/powershell/module/microsoft.powershell.core/about/about_preference_variables)를 설정하여 서로 다른 출력 스트림으로 전송된 데이터에 대한 응답을 제어할 수 있습니다. 다음 표에는 Runbook에서 사용할 수 있는 기본 설정 변수와 그 기본값 및 유효한 값이 나와 있습니다. Azure Automation 외부의 Windows PowerShell에서 사용할 경우 기본 설정 변수의 추가 값을 사용할 수 있습니다.

| 변수 | 기본값 | 유효한 값 |
|:--- |:--- |:--- |
| `WarningPreference` |계속 |중지<br>계속<br>SilentlyContinue |
| `ErrorActionPreference` |계속 |중지<br>계속<br>SilentlyContinue |
| `VerbosePreference` |SilentlyContinue |중지<br>계속<br>SilentlyContinue |

다음 표에는 Runbook에서 유효한 기본 설정 변수 값의 동작이 나와 있습니다.

| 값 | 동작 |
|:--- |:--- |
| 계속 |메시지를 기록하고 runbook 실행을 계속합니다. |
| SilentlyContinue |메시지를 기록하지 않고 Runbook을 계속 실행합니다. 이 값은 메시지를 무시하는 효과가 있습니다. |
| 중지 |메시지를 기록하고 runbook을 일시 중단합니다. |

## <a name="retrieve-runbook-output-and-messages"></a><a name="runbook-output"></a>Runbook 출력 및 메시지 가져오기

### <a name="retrieve-runbook-output-and-messages-in-azure-portal"></a>Azure Portal에서 Runbook 출력 및 메시지 가져오기

Runbook의 **작업** 탭을 사용하여 Azure Portal에서 Runbook 작업의 세부 정보를 볼 수 있습니다. 작업 요약은 작업 및 발생한 예외에 대한 일반 정보 외에도 입력 매개 변수 및 [출력 스트림](#use-the-output-stream)을 표시합니다. 작업 기록은 출력 스트림의 메시지와 [경고 및 오류 스트림](#write-output-to-warning-and-error-streams)을 포함합니다. Runbook이 자세한 정보 표시 레코드와 진행률 레코드를 로깅하도록 구성된 경우 [자세한 정보 표시 스트림](#write-output-to-verbose-stream)과 [진행률 레코드](#handle-progress-records)의 메시지도 포함합니다.

### <a name="retrieve-runbook-output-and-messages-in-windows-powershell"></a>Windows PowerShell에서 Runbook 출력 및 메시지 가져오기

Windows PowerShell에서 [Get-AzAutomationJobOutput](/powershell/module/Az.Automation/Get-AzAutomationJobOutput) cmdlet을 사용하여 Runbook에서 출력 및 메시지를 가져올 수 있습니다. 이 cmdlet에는 작업의 ID가 필요하며 가져올 스트림을 지정할 `Stream`이라는 매개 변수가 있습니다. 이 매개 변수에 값 Any를 지정하면 작업의 모든 스트림을 가져올 수 있습니다.

다음 예제는 샘플 runbook를 시작한 다음 완료되기를 기다립니다. Runbook의 실행이 완료되면 스크립트가 작업으로부터 Runbook 출력 스트림을 수집합니다.

```powershell
$job = Start-AzAutomationRunbook -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Name "Test-Runbook"

$doLoop = $true
While ($doLoop) {
  $job = Get-AzAutomationJob -ResourceGroupName "ResourceGroup01" `
    -AutomationAccountName "MyAutomationAccount" -Id $job.JobId
  $status = $job.Status
  $doLoop = (($status -ne "Completed") -and ($status -ne "Failed") -and ($status -ne "Suspended") -and ($status -ne "Stopped"))
}

Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Output

# For more detailed job output, pipe the output of Get-AzAutomationJobOutput to Get-AzAutomationJobOutputRecord
Get-AzAutomationJobOutput -ResourceGroupName "ResourceGroup01" `
  -AutomationAccountName "MyAutomationAccount" -Id $job.JobId -Stream Any | Get-AzAutomationJobOutputRecord
```

### <a name="retrieve-runbook-output-and-messages-in-graphical-runbooks"></a>그래픽 Runbook에서 Runbook 출력 및 메시지 가져오기

그래픽 Runbook의 경우 작업 수준 추적의 형식으로 추가 출력 및 메시지 로깅을 사용할 수 있습니다. 추적에는 두 가지 수준이 있습니다. 기본 및 자세히 추적 기본 추적에서는 Runbook의 각 작업의 시작 및 종료 시간과 작업 다시 시도와 관련된 정보를 볼 수 있습니다. 작업의 시도 횟수 및 시작 시간을 예로 들 수 있습니다. 자세한 추적에는 기본 추적 기능과 각 작업의 입력 및 출력 데이터 로깅이 포함됩니다.

현재 작업 수준 추적은 자세한 정보 표시 스트림을 사용하여 레코드를 기록합니다. 따라서 추적을 사용하도록 설정할 경우 자세한 정보 표시 로깅을 사용하도록 설정해야 합니다. 추적이 활성화된 그래픽 Runbook의 경우 진행률 레코드를 기록할 필요가 없습니다. 기본 추적은 동일한 용도를 제공하며 정보가 더욱 자세합니다.

![그래픽 작성 작업 스트림 보기](media/automation-runbook-output-and-messages/job-streams-view-blade.png)

위 이미지에서, 그래픽 Runbook에 대해 자세한 정보 표시 로깅 및 추적을 사용하도록 설정하면 프로덕션 **작업 스트림** 보기에서 훨씬 더 많은 정보를 사용할 수 있음을 알 수 있습니다. 이러한 추가 정보는 Runbook을 사용하는 프로덕션 문제 해결에 꼭 필요할 수 있습니다.

그러나 문제 해결을 위해 Runbook의 진행률을 추적하는 용도로 이 정보가 필요하지 않은 한 추적을 해제한 상태로 유지하는 것이 좋습니다. 추적 레코드는 그 수가 특히 클 수 있습니다. 그래픽 Runbook 추적을 사용하면 기본 추적과 자세히 추적 중 어느 것을 구성했는지에 따라 작업당 2~4개의 레코드가 생성될 수 있습니다.

**작업 수준 추적을 설정하려면:**

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화** 아래에서 **Runbook** 을 선택하여 Runbook 목록을 엽니다.
3. Runbook 페이지의 Runbook 목록에서 그래픽 Runbook을 선택합니다.
4. **설정** 에서 **로깅 및 추적** 을 클릭합니다.
5. 로깅 및 추적 페이지의 **상세 레코드 기록** 에서 **켜기** 를 클릭하여 자세한 정보 표시 로깅을 사용하도록 설정합니다.
6. **작업 수준 추적** 에서 필요한 추적 수준에 따라 추적 수준을 **기본** 또는 **자세히** 로 변경합니다.<br>

   ![그래픽 작성 로깅 및 추적 페이지](media/automation-runbook-output-and-messages/logging-and-tracing-settings-blade.png)

### <a name="retrieve-runbook-output-and-messages-in-microsoft-azure-monitor-logs"></a>Microsoft Azure Monitor 로그에서 Runbook 출력 및 메시지 가져오기

Azure Automation에서 Log Analytics 작업 영역으로 Runbook 작업 상태 및 작업 스트림을 보낼 수 있습니다. Azure Monitor는 다음과 같은 작업을 수행할 수 있도록 로그를 지원합니다.

* Automation 작업에 대한 통찰력 확보
* Runbook 작업 상태(예: 실패 또는 일시 중단)를 기반으로 메일 또는 경고 트리거
* 작업 스트림에서 고급 쿼리 작성
* Automation 계정 간에 작업 상호 연결
* 작업 기록 시각화

Azure Monitor 로그와의 통합을 구성하여 작업 데이터를 수집하고 상관 관계를 지정하고 작업을 수행하는 방법에 대한 자세한 내용은 [Automation에서 Azure Monitor 로그로 작업 상태 및 작업 스트림 전달](automation-manage-send-joblogs-log-analytics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* Runbook을 사용하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.
* PowerShell 스크립팅에 익숙하지 않은 경우 [PowerShell](/powershell/scripting/overview) 설명서를 참조하세요.
* Azure Automation PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
