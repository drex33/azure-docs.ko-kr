---
title: Azure Automation 컨텍스트 전환
description: 이 문서에서는 컨텍스트 전환 및 Runbook 문제를 방지하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/27/2021
ms.topic: conceptual
ms.openlocfilehash: 121e302708fecd20934f6ba57bd08590e45a7429
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233203"
---
# <a name="context-switching-in-azure-automation"></a>Azure Automation 컨텍스트 전환

컨텍스트 전환은 한 프로세스의 컨텍스트가 다른 프로세스에서 컨텍스트를 변경하는 경우입니다. Azure 컨텍스트는 Azure PowerShell cmdlet의 대상을 정의하는 정보 집합입니다. 컨텍스트는 다음 속성으로 구성됩니다.

|속성 | Description |
|---|---|
|Name | 컨텍스트의 이름입니다.|
|계정 | Azure와의 통신을 인증하는 데 사용되는 사용자 이름 또는 서비스 주체입니다.|
|환경 | Azure 글로벌 또는 국가별 Azure 클라우드 중 하나(예: Azure Government)를 나타냅니다. Azure Stack 같은 하이브리드 클라우드 플랫폼을 지정할 수도 있습니다.|
|구독 | 관리하려는 리소스가 포함된 Azure 구독을 나타냅니다.|
|테넌트 | 단일 조직을 나타내는 Azure Active Directory 신뢰할 수 있는 전용 인스턴스입니다.|
|자격 증명 | Azure에서 ID를 확인하고 Azure의 리소스에 액세스하기 위한 권한 부여를 확인하는 데 사용하는 정보입니다.|

여러 구독에 액세스할 수 있는 계정이 로그인하면 해당 구독이 사용자의 컨텍스트에 추가될 수 있습니다. 올바른 구독을 보장하려면 연결할 때 선언해야 합니다. 예를 들면 `Add-AzAccount -Credential $Cred -subscription 'cd4dxxxx-xxxx-xxxx-xxxx-xxxxxxxx9749'`를 사용합니다. 그러나 한 구독을 관리하는 Runbook이 동일한 Automation 계정에서 다른 구독의 리소스를 관리하는 다른 Runbook과 동일한 샌드박스 프로세스에서 실행되는 경우 문제가 발생할 수 있습니다. 한 Runbook에서 변경한 컨텍스트는 기본 컨텍스트를 사용하는 다른 Runbook에 영향을 줄 수 있습니다. 컨텍스트에 사용할 자격 증명 및 대상 구독과 같은 정보가 포함되기 때문에 cmdlet은 잘못된 구독을 대상으로 지정하여 또는 사용 권한 오류가 발생할 수 `not found` 있습니다. 이 문제를 **컨텍스트 전환이라고 합니다.**

## <a name="manage-azure-contexts"></a>Azure 컨텍스트 관리

Runbook이 잘못된 구독의 리소스에 대해 실행하지 않도록 하려면 다음 권장 사항을 검토합니다.

1. 각 Runbook의 시작 부분에 다음 명령을 사용하여 Automation Runbook 내에서 샌드박스 컨텍스트 저장을 사용하지 않도록 `Disable-AzContextAutosave -Scope Process` 설정합니다.
1. Azure PowerShell cmdlet은 `-DefaultProfile` 매개 변수를 지원합니다. 이 매개 변수는 동일한 프로세스에서 여러 스크립트 실행을 지원하기 위해 모든 Az 및 Azure Resource Manager(AzureRM) cmdlet에 추가되어 각 cmdlet에 사용할 컨텍스트를 지정할 수 있습니다. 컨텍스트 개체를 만들 때와 변경할 때마다 Runbook에 저장합니다. 그런 다음 Az 또는 AzureRM cmdlet을 사용한 모든 호출에서 참조합니다. 예들 들어 `$AzureContext = Set-AzContext -SubscriptionId $subID`입니다.
1. 컨텍스트 개체를 PowerShell cmdlet에 전달합니다(예: `Get-AzVM -ResourceGroupName "myGroup" -DefaultProfile $AzureContext` ).

컨텍스트 전환을 방지하기 위한 권장 사항에 따라 시스템 할당 관리 ID를 사용하는 PowerShell Runbook 코드 조각은 다음과 같습니다.

```powershell
# Ensures you do not inherit an AzContext in your runbook
Disable-AzContextAutosave -Scope Process

# Connect to Azure with system-assigned managed identity
$AzureContext = (Connect-AzAccount -Identity).context

# set and store context
$AzureContext = Set-AzContext -SubscriptionName $AzureContext.Subscription -DefaultProfile $AzureContext

# Pass context object - even though the context had just been set
# This is the step that guarantees the context will not be switched.
Get-AzVM -ResourceGroupName "resourceGroupName" -DefaultProfile $AzureContext | Select Name
```

## <a name="possible-symptoms"></a>가능한 증상

이러한 권장 사항을 따르지 않으면 문제가 발생하지 않을 수 있지만 기회가 있습니다. 이 상황의 기본 문제는 타이밍입니다. 다른 Runbook이 컨텍스트를 전환할 때 각 Runbook이 수행하는 내용에 따라 달라집니다. 다음은 몇 가지 가능한 오류 메시지입니다. 그러나 이러한 오류 메시지는 비 컨텍스트 전환 조건으로 인해 발생할 수 있습니다.

`The subscription named <subscription name> cannot be found.`

```error
Get-AzVM : The client '<automation-runas-account-guid>' with object id '<automation-runas-account-guid>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope '/subscriptions/<subcriptionIdOfSubscriptionWichDoesntContainTheVM>/resourceGroups/REsourceGroupName/providers/Microsoft.Compute/virtualMachines/VMName '.
   ErrorCode: AuthorizationFailed
   StatusCode: 403
   ReasonPhrase: Forbidden Operation
   ID : <AGuidRepresentingTheOperation> At line:51 char:7 + $vm = Get-AzVM -ResourceGroupName $ResourceGroupName -Name $UNBV... +
```

```error
Get-AzureRmResource : Resource group "SomeResourceGroupName" could not be found.
... resources = Get-AzResource -ResourceGroupName $group.ResourceGro ...
                 ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : CloseError: (:) [Get-AzResource], CloudException
    + FullyQualifiedErrorId : Microsoft.Azure.Commands.ResourceManager.Cmdlets.Implementation.GetAzureResourceCmdlet
```

## <a name="next-steps"></a>다음 단계

- [Azure Automation 계정 인증 개요](automation-security-overview.md)
- [Azure Automation에서 Runbook 실행](automation-runbook-execution.md)
- [Azure Automation에서 모듈 관리](./shared-resources/modules.md)

