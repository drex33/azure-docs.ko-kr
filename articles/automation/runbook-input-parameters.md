---
title: Azure Automation에서 Runbook 입력 매개 변수 구성
description: 이 문서에서는 Runbook이 시작될 때 Runbook에 데이터를 전달할 수 있도록 하는 Runbook 입력 매개 변수를 구성하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/22/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b1b97aaba9a51679775338ecaa5e1215bfe3372b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031711"
---
# <a name="configure-runbook-input-parameters-in-automation"></a>Automation에서 Runbook 입력 매개 변수 구성

Runbook 입력 매개 변수는 시작하는 Runbook에 데이터를 전달할 수 있도록 하여 Runbook의 유연성을 늘립니다. 이러한 매개 변수는 Runbook 동작이 특정 시나리오 및 환경에 대한 대상이 되도록 합니다. 이 문서에서는 Runbook에서 입력 매개 변수를 구성하고 사용하는 방법을 설명합니다.

PowerShell, PowerShell 워크플로, 그래픽 및 Python Runbook에 대한 입력 매개 변수를 구성할 수 있습니다. Runbook에는 여러 데이터 형식을 가진 여러 매개 변수가 있거나 매개 변수가 전혀 없을 수 있습니다. 입력 매개 변수는 필수 또는 선택 사항일 수 있으며, 선택적 매개 변수에 기본값을 사용할 수 있습니다.

Runbook을 시작할 때 Runbook의 입력 매개 변수에 값을 할당합니다. Runbook은 Azure Portal, 웹 서비스 또는 PowerShell에서 시작할 수 있습니다. 또한 다른 Runbook에서 인라인으로 호출되는 하위 Runbook으로 시작할 수 있습니다.

### <a name="configure-input-parameters-in-powershell-runbooks"></a>PowerShell Runbook에서 입력 매개 변수 구성

Azure Automation의 PowerShell 및 PowerShell 워크플로 Runbook은 다음과 같은 속성을 통해 정의된 입력 매개 변수를 지원합니다. 

| **속성** | **설명** |
|:--- |:--- |
| Type |필수 사항입니다. 매개 변수 값에 필요한 데이터 형식입니다. 모든 .NET 형식이 유효합니다. |
| 속성 |필수 사항입니다. 매개 변수의 이름입니다. 이 이름은 Runbook 내에서 고유해야 하고, 문자로 시작해야 하며, 문자, 숫자 또는 밑줄 문자만 포함할 수 있습니다. |
| 필수 |(선택 사항) 매개 변수에 값이 필요한지 여부를 지정하는 부울 값입니다. 이 값을 True로 설정한 경우 Runbook이 시작될 때 값을 지정해야 합니다. 이 값을 False로 설정한 경우 값은 선택 사항입니다. `Mandatory` 속성에 값을 지정하지 않으면 PowerShell은 기본적으로 입력 매개 변수를 선택 사항으로 간주합니다. |
| 기본값 |(선택 사항) Runbook이 시작할 때 입력 값이 전달되지 않는 경우 매개 변수에 사용되는 값입니다. Runbook은 모든 매개 변수에 기본값을 설정할 수 있습니다. |

Windows PowerShell은 유효성 검사, 별칭, 매개 변수 설정과 같이 위에 나열된 것보다 많은 입력 매개 변수 특성을 지원합니다. 그러나 현재 Azure Automation은 나열된 입력 매개 변수 특성만 지원합니다.

예를 들어 PowerShell 워크플로 Runbook의 매개 변수 정의를 살펴보겠습니다. 이 정의의 일반적인 형식은 다음과 같습니다. 여기서 여러 매개 변수는 쉼표로 구분됩니다.

```powershell
Param
(
  [Parameter (Mandatory= $true/$false)]
  [Type] $Name1 = <Default value>,

  [Parameter (Mandatory= $true/$false)]
  [Type] $Name2 = <Default value>
)
```

이제 가상 머신(단일 VM 또는 리소스 그룹 내의 모든 VM)에 대한 세부 정보를 출력하는 PowerShell 워크플로 Runbook에 대한 입력 매개 변수를 구성해보겠습니다. 다음 스크린샷에 표시된 대로 이 Runbook에는 가상 머신의 이름(`VMName`) 및 리소스 그룹 이름(`resourceGroupName`)이라는 두 가지 매개 변수가 있습니다.

![Automation PowerShell 워크플로](media/automation-runbook-input-parameters/automation-01-powershellworkflow.png)

이 매개 변수 정의에서 입력 매개 변수는 문자열 형식의 단순 매개 변수입니다.

PowerShell 및 PowerShell 워크플로 Runbook은 입력 매개 변수에 대해 `Object` 또는 `PSCredential`과 같은 단순 형식 및 복합 형식을 모두 지원합니다. Runbook에 개체 형식 입력 매개 변수가 있는 경우 값에 전달하려면 이름-값 쌍이 있는 PowerShell 해시 테이블을 사용해야 합니다. 예를 들어 Runbook에 다음 매개 변수가 있습니다.

```powershell
[Parameter (Mandatory = $true)]
[object] $FullName
```

이 경우 매개 변수에 다음 값을 전달할 수 있습니다.

```powershell
@{"FirstName"="Joe";"MiddleName"="Bob";"LastName"="Smith"}
```

> [!NOTE]
> Null 기본값을 사용하여 선택적 문자열 매개 변수에 값을 전달하지 않는 경우 이 매개 변수의 값은 Null이 아닌 빈 문자열입니다.

### <a name="configure-input-parameters-in-graphical-runbooks"></a>그래픽 Runbook에서 입력 매개 변수 구성

그래픽 Runbook에 대한 입력 매개 변수 구성을 설명하기 위해 가상 머신(단일 VM 또는 리소스 그룹 내의 모든 VM)에 대한 세부 정보를 출력하는 그래픽 Runbook을 만들겠습니다. 자세한 내용은 [내 첫 번째 그래픽 Runbook](./learn/powershell-runbook-managed-identity.md)을 참조하세요.

그래픽 Runbook은 다음과 같은 주요 Runbook 활동을 사용합니다.

* Azure로 인증하는 Azure 실행 계정을 구성합니다. 
* VM 속성을 가져오는 [Get-AzVM](/powershell/module/az.compute/get-azvm) cmdlet을 정의합니다.
* [쓰기 출력](/powershell/module/microsoft.powershell.utility/write-output) 활동을 사용하여 VM 이름을 출력합니다. 

`Get-AzVM` 활동은 두 개의 입력인 VM 이름과 리소스 그룹 이름을 정의합니다. Runbook이 시작할 때마다 이러한 이름이 다를 수 있으므로 입력 매개 변수를 Runbook에 추가하여 이러한 입력을 수락해야 합니다. [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)을 참조하세요.

입력 매개 변수를 구성하려면 다음 단계를 수행합니다.

1. Runbook 페이지에서 그래픽 Runbook을 선택한 다음 **편집** 을 클릭합니다.
2. 그래픽 편집기에서 **입력 및 출력** 단추를 클릭한 다음 **입력 추가** 를 선택하여 Runbook 입력 매개 변수 창을 엽니다.

   ![자동화 그래픽 Runbook](media/automation-runbook-input-parameters/automation-02-graphical-runbok-editor.png)

3. 입력 및 출력 컨트롤에는 Runbook에 정의된 입력 매개 변수 목록이 표시됩니다. 여기에서 새 입력 매개 변수를 추가하거나 기존의 입력 매개 변수의 구성을 편집합니다. Runbook에 대한 새 매개 변수를 추가하려면 **입력 추가** 를 클릭하여 **Runbook 입력 매개 변수** 블레이드를 엽니다. 여기서 [Azure Automation에서 그래픽 작성](automation-graphical-authoring-intro.md)에 정의된 속성을 사용하여 매개 변수를 구성할 수 있습니다.

    ![새 입력 추가](media/automation-runbook-input-parameters/automation-runbook-input-parameter-new.png)
4. 다음 속성을 사용하여 `Get-AzVM` 활동에서 사용할 두 매개 변수를 만든 다음 **확인** 을 클릭합니다.

   * 매개 변수 1:
        * **이름** -- **VMName**
        * **형식** -- String
        * **필수** -- **아니요**

   * 매개 변수 2:
        * **이름** -- **resourceGroupName**
        * **형식** -- String
        * **필수** -- **아니요**
        * **기본값** -- **사용자 지정**
        * 사용자 지정 기본값 -- VM을 포함하는 리소스 그룹의 이름

5. 입력 및 출력 컨트롤의 매개 변수를 확인합니다. 
6. **확인** 을 다시 클릭하고 **저장** 을 클릭합니다.
7. **게시** 를 클릭하여 Runbook을 게시합니다.

### <a name="configure-input-parameters-in-python-runbooks"></a>Python Runbook에서 입력 매개 변수 구성

PowerShell, PowerShell 워크플로 및 그래픽 Runbook과 달리 Python Runbook에서는 명명된 매개 변수를 사용하지 않습니다. Runbook 편집기는 모든 입력 매개 변수를 인수 값 배열로 구문 분석합니다. `sys` 모듈을 Python 스크립트에 가져온 다음 `sys.argv` 배열을 사용하여 배열에 액세스할 수 있습니다. 배열의 첫 번째 요소인 `sys.argv[0]`는 스크립트의 이름입니다. 따라서 첫 번째 실제 입력 매개 변수는 `sys.argv[1]`입니다.

Python Runbook에서 입력 매개 변수를 사용하는 방법의 예제를 보려면 [Azure Automation의 내 첫 번째 Python Runbook](./learn/automation-tutorial-runbook-textual-python-3.md)을 참조하세요.

## <a name="assign-values-to-input-parameters-in-runbooks"></a>Runbook의 입력 매개 변수에 값 할당

이 섹션에서는 Runbook의 입력 매개 변수에 값을 전달하는 여러 가지 방법에 대해 설명합니다. 다음 작업을 수행할 때 매개 변수 값을 할당할 수 있습니다.

* [Runbook 시작](#start-a-runbook-and-assign-parameters)
* [Runbook 테스트](#test-a-runbook-and-assign-parameters)
* [Runbook 일정 연결](#link-a-schedule-to-a-runbook-and-assign-parameters)
* [Runbook에 대한 웹후크 만들기](#create-a-webhook-for-a-runbook-and-assign-parameters)

### <a name="start-a-runbook-and-assign-parameters"></a>Runbook 시작 및 매개 변수 할당

Runbook은 Azure Portal, 웹후크, PowerShell cmdlet, REST API, SDK 등 여러 가지 방법을 통해 시작할 수 있습니다. 

#### <a name="start-a-published-runbook-using-the-azure-portal-and-assign-parameters"></a>Azure Portal을 사용하여 게시된 Runbook 시작 및 매개 변수 할당

Azure Portal에서 [Runbook을 시작](start-runbooks.md#start-a-runbook-with-the-azure-portal)하면 **Runbook 시작** 블레이드가 열려 만든 매개 변수의 값을 입력할 수 있습니다.

![포털을 사용하여 시작하기](media/automation-runbook-input-parameters/automation-04-startrunbookusingportal.png)

입력 상자 아래의 레이블에 매개 변수 특성을 정의하도록 설정된 속성을 볼 수 있습니다(예: 필수 또는 선택, 형식, 기본값). 또한 매개 변수 이름 옆에 있는 도움말 풍선에는 매개 변수 입력 값을 결정하는 데 필요한 모든 핵심 정보가 정의됩니다. 

> [!NOTE]
> 문자열 매개 변수는 문자열 형식의 빈 값을 지원합니다. 입력 매개 변수 상자에 `[EmptyString]`을 입력하면 빈 문자열을 매개 변수에 전달합니다. 또한 문자열 매개 변수는 Null을 지원하지 않습니다. 문자열 매개 변수에 값을 전달하지 않으면 PowerShell은 이를 Null로 해석합니다.

#### <a name="start-a-published-runbook-using-powershell-cmdlets-and-assign-parameters"></a>PowerShell cmdlet을 사용하여 게시된 Runbook 시작 및 매개 변수 할당

* **Azure Resource Manager cmdlet:** [Start-AzAutomationRunbook](/powershell/module/Az.Automation/Start-AzAutomationRunbook)을 사용하여 리소스 그룹에 생성된 자동화 Runbook을 시작할 수 있습니다.

   ```powershell
     $params = @{"VMName"="WSVMClassic";"resourceGroupeName"="WSVMClassicSG"}
  
     Start-AzAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" –ResourceGroupName $resourceGroupName -Parameters $params
   ```

* **Azure 클래식 배포 모델 cmdlet:** [Start-AzureAutomationRunbook](/powershell/module/servicemanagement/azure.service/start-azureautomationrunbook)을 사용하여 기본 리소스 그룹에 생성된 자동화 Runbook을 시작할 수 있습니다.
  
   ```powershell
     $params = @{"VMName"="WSVMClassic"; "ServiceName"="WSVMClassicSG"}
  
     Start-AzureAutomationRunbook -AutomationAccountName "TestAutomation" -Name "Get-AzureVMGraphical" -Parameters $params
   ```

> [!NOTE]
> PowerShell cmdlet을 사용하여 Runbook을 시작하는 경우 기본 매개 변수 `MicrosoftApplicationManagementStartedBy`가 `PowerShell` 값을 사용하여 만들어집니다. 작업 세부 정보 창에서 이 매개 변수를 볼 수 있습니다.  

#### <a name="start-a-runbook-using-an-sdk-and-assign-parameters"></a>SDK를 사용하여 Runbook 시작 및 매개 변수 할당

* **Azure Resource Manager 방법:** 프로그래밍 언어 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
  {
    var response = AutomationClient.Jobs.Create(resourceGroupName, automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
      {
        Runbook = new RunbookAssociationProperty
        {
          Name = runbookName
        },
        Parameters = parameters
      }
    });
    return response.Job;
  }
  ```

* **Azure 클래식 배포 모델 방법:** 프로그래밍 언어의 SDK를 사용하여 Runbook을 시작할 수 있습니다. 다음은 Automation 계정의 Runbook을 시작하기 위한 C# 코드 조각입니다. [GitHub 리포지토리](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/automation/Microsoft.Azure.Management.Automation/tests/TestSupport/AutomationTestBase.cs)에서 모든 코드를 볼 수 있습니다.

  ```csharp
  public Job StartRunbook(string runbookName, IDictionary<string, string> parameters = null)
  {
    var response = AutomationClient.Jobs.Create(automationAccount, new JobCreateParameters
    {
      Properties = new JobCreateProperties
      {
        Runbook = new RunbookAssociationProperty
        {
          Name = runbookName
        },
        Parameters = parameters
      }
    });
    return response.Job;
  }
  ```

  이 메서드를 시작하려면 Runbook 매개 변수 `VMName` 및 `resourceGroupName`과 해당 값을 저장하는 사전을 만듭니다. 그런 다음 Runbook을 시작합니다. 다음은 위에 정의된 메서드를 호출하기 위한 C# 코드 조각입니다.

  ```csharp
  IDictionary<string, string> RunbookParameters = new Dictionary<string, string>();
  
  // Add parameters to the dictionary.
  RunbookParameters.Add("VMName", "WSVMClassic");
  RunbookParameters.Add("resourceGroupName", "WSSC1");
  
  //Call the StartRunbook method with parameters
  StartRunbook("Get-AzureVMGraphical", RunbookParameters);
  ```

#### <a name="start-a-runbook-using-the-rest-api-and-assign-parameters"></a>REST API를 사용하여 Runbook 시작 및 매개 변수 할당

`PUT` 메서드를 다음 요청 URI와 함께 사용하여 Azure Automation REST API로 Runbook 작업을 만들고 시작할 수 있습니다. `https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Automation/automationAccounts/{automationAccountName}/jobs/{jobName}?api-version=2017-05-15-preview`

요청 URI에서 다음 매개 변수를 바꿉니다.

* `subscriptionId`: Azure 구독 ID입니다.  
* `resourceGroupName`: Automation 계정에 대한 리소스 그룹의 이름입니다.
* `automationAccountName`: 지정된 클라우드 서비스 내에 호스트되는 Automation 계정의 이름입니다.  
* `jobName`: 작업의 GUID입니다. PowerShell의 GUID는 `[GUID]::NewGuid().ToString()*`을 사용하여 만들 수 있습니다.

Runbook 작업에 매개 변수를 전달하려면 요청 본문을 사용합니다. 요청 본문은 JSON 형식으로 제공되는 다음 정보를 사용합니다.

* Runbook 이름: 필수 사항입니다. 시작할 작업 시작에 대한 Runbook의 이름입니다.  
* Runbook 매개 변수: (선택 사항) 이름이 문자열 형식이고 값은 유효한 JSON 값일 수 있는 (이름, 값) 형식인 매개 변수 목록의 사전입니다.

앞서 `VMName` 및 `resourceGroupName`을 사용하여 만든 **Get-AzureVMTextual** Runbook을 시작하려는 경우 요청 본문에 다음 JSON 형식을 사용합니다.

```json
    {
      "properties":{
        "runbook":{
        "name":"Get-AzureVMTextual"},
      "parameters":{
         "VMName":"WindowsVM",
         "resourceGroupName":"ContosoSales"}
        }
    }
```

작업이 성공적으로 만들어진 경우 HTTP 상태 코드 201이 반환됩니다. 응답 헤더 및 응답 본문에 대한 자세한 내용은 [REST API를 사용하여 Runbook 작업 만들기](/rest/api/automation/job/create)를 참조하세요.

### <a name="test-a-runbook-and-assign-parameters"></a>Runbook 테스트 및 매개 변수 할당

테스트 옵션을 사용하여 [Runbook의 초안 버전을 테스트](./manage-runbooks.md)하면 테스트 페이지가 열립니다. 이 페이지를 사용하여 만든 매개 변수의 값을 구성할 수 있습니다.

![매개 변수 테스트 및 할당](media/automation-runbook-input-parameters/automation-06-testandassignparameters.png)

### <a name="link-a-schedule-to-a-runbook-and-assign-parameters"></a>Runbook에 일정 연결 및 매개 변수 할당

Runbook이 특정 시간에 시작하도록 Runbook에 [일정을 연결](./shared-resources/schedules.md)할 수 있습니다. 일정을 만드는 동안 입력 매개 변수를 할당하고 Runbook이 일정에 따라 시작될 때 이러한 값을 사용합니다. 모든 필수 매개 변수 값이 제공될 때까지 일정을 저장할 수 없습니다.

![매개 변수 예약 및 할당](media/automation-runbook-input-parameters/automation-07-scheduleandassignparameters.png)

### <a name="create-a-webhook-for-a-runbook-and-assign-parameters"></a>Runbook에 대한 webhook 만들기 및 매개 변수 할당

Runbook에 대한 [webhook](automation-webhooks.md) 을 만들고 Runbook 입력 매개 변수를 구성할 수 있습니다. 모든 필수 매개 변수 값이 제공될 때까지 웹후크를 저장할 수 없습니다.

![Webhook 만들기 및 매개 변수 할당](media/automation-runbook-input-parameters/automation-08-createwebhookandassignparameters.png)

웹후크를 사용하여 Runbook을 실행할 때 미리 정의된 입력 매개 변수 `[WebhookData](automation-webhooks.md)`가 사용자가 정의한 입력 매개 변수와 함께 전송됩니다. 

![WebhookData 매개 변수](media/automation-runbook-input-parameters/automation-09-webhook-data-parameters.png)

## <a name="pass-a-json-object-to-a-runbook"></a>JSON 개체를 Runbook에 전달합니다.

JSON 파일에서 Runbook에 전달하려는 데이터를 저장하는 것이 유용할 수 있습니다. 예를 들어 Runbook에 전달하려는 모든 매개 변수가 포함된 JSON 파일을 만들 수 있습니다. 이렇게 하려면 JSON 코드를 문자열로 변환한 다음 해당 문자열을 PowerShell 개체로 변환한 후 해당 개체를 Runbook에 전달해야 합니다.

이 섹션에서는 PowerShell 스크립트가 [Start-AzAutomationRunbook](/powershell/module/az.automation/start-azautomationrunbook)를 호출하여 PowerShell Runbook을 시작하고 JSON 파일의 내용을 Runbook에 전달하는 예제를 사용합니다. PowerShell Runbook은 JSON 개체에서 VM에 대한 매개 변수를 검색하여 Azure VM을 시작합니다.

### <a name="create-the-json-file"></a>JSON 파일 만들기

텍스트 파일에 다음 코드를 입력하고 로컬 컴퓨터의 어딘가에 **test.json** 으로 저장합니다.

```json
{
   "VmName" : "TestVM",
   "ResourceGroup" : "AzureAutomationTest"
}
```

### <a name="create-the-runbook"></a>Runbook 만들기

Azure Automation에서 **Test-Json** 이라는 새 PowerShell Runbook을 만듭니다.

JSON 데이터를 허용하려면 Runbook에서 개체를 입력 매개 변수로 사용해야 합니다. 그러면 Runbook이 JSON 파일에 정의된 속성을 사용할 수 있습니다.

```powershell
Param(
     [parameter(Mandatory=$true)]
     [object]$json
)

# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Convert object to actual JSON
$json = $json | ConvertFrom-Json

# Use the values from the JSON object as the parameters for your command
Start-AzVM -Name $json.VMName -ResourceGroupName $json.ResourceGroup -DefaultProfile $AzureContext
```

시스템이 할당한 관리 ID를 사용하여 Runbook을 실행하려면 코드를 그대로 둡니다. 사용자가 할당한 관리 ID를 사용하려면 다음을 수행합니다.
1. 줄 10에서 를 제거합니다. `$AzureContext = (Connect-AzAccount -Identity).context`
1. `$AzureContext = (Connect-AzAccount -Identity -AccountId <ClientId>).context`로 바꿉니다.
1. 클라이언트 ID를 입력합니다.

이 Runbook을 Automation 계정에 저장하고 게시합니다.

### <a name="call-the-runbook-from-powershell"></a>PowerShell에서 Runbook 호출

이제 로컬 컴퓨터에서 Azure PowerShell을 사용하여 Runbook을 호출할 수 있습니다. 

1. 표시된 대로 Azure에 로그인합니다. 그러면 Azure 자격 증명을 입력하라는 메시지가 표시됩니다.

   ```powershell
   Connect-AzAccount
   ```

    >[!NOTE]
    >PowerShell Runbook의 경우 `Add-AzAccount` 및 `Add-AzureRMAccount`는 `Connect-AzAccount`에 대한 별칭입니다. 이러한 별칭은 그래픽 Runbook에서 사용할 수 없습니다. `Connect-AzAccount` 자체만 그래픽 Runbook에서 사용할 수 있습니다.

1. 저장된 JSON 파일의 내용을 가져와 문자열로 변환합니다. `JsonPath`는 JSON 파일을 저장한 경로입니다.

   ```powershell
   $json =  (Get-content -path 'JsonPath\test.json' -Raw) | Out-string
   ```

1. `$json`의 문자열 내용을 PowerShell 개체로 변환합니다.

   ```powershell
   $JsonParams = @{"json"=$json}
   ```

1. `Start-AzAutomationRunbook`의 매개 변수에 대한 해시 테이블을 만듭니다. 

   ```powershell
   $RBParams = @{
        AutomationAccountName = 'AATest'
        ResourceGroupName = 'RGTest'
        Name = 'Test-Json'
        Parameters = $JsonParams
   }
   ```

   `Parameters`의 값을 JSON 파일의 값이 포함된 PowerShell 개체로 설정하고 있는 것입니다.
1. Runbook을 시작합니다.

   ```powershell
   $job = Start-AzAutomationRunbook @RBParams
   ```

## <a name="next-steps"></a>다음 단계

* 텍스트 Runbook을 준비하려면 [Azure Automation에서 텍스트 Runbook 편집](automation-edit-textual-runbook.md)을 참조하세요.
* 그래픽 Runbook을 준비하려면 [Azure Automation에서 그래픽 Runbook 작성](automation-graphical-authoring-intro.md)을 참조하세요.