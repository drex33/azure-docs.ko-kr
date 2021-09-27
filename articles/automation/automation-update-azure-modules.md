---
title: Azure Automation에서 Azure PowerShell 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반적인 Azure PowerShell 모듈을 업데이트하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/16/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: a2ff4799b335376f6f99004d45034ba6a4796059
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128654369"
---
# <a name="update-azure-automation-powershell-modules"></a>Azure Automation PowerShell 모듈 업데이트

기본적으로 각 Automation 계정에는 가장 일반적인 Azure PowerShell 모듈이 제공됩니다. [기본 모듈](shared-resources/modules.md#default-modules)을 참조하세요. Azure 팀이 Azure 모듈을 정기적으로 업데이트하므로, 포함된 cmdlet을 통해 변경 작업이 수행될 수 있습니다. 예를 들어 매개 변수의 이름을 바꾸거나 cmdlet을 완전히 사용 중단하는 변경 내용은 Runbook에 부정적인 영향을 미칠 수 있습니다. 

> [!NOTE]
> Automation에서 기본적으로 제공하는 모듈인 글로벌 모듈은 삭제할 수 없습니다.

## <a name="set-up-an-automation-account"></a>Automation 계정 설정

Runbook 및 Runbook이 자동화하는 프로세스에 영향을 주지 않으려면 업데이트할 때 테스트 및 유효성 검사를 꼭 수행해야 합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈 업데이트 같은 반복적인 변경도 포함되어야 합니다.

Automation 계정에 [시스템 할당 관리 ID 또는 사용자 할당 관리 ID 가 추가되었는지 확인합니다.](quickstarts/enable-managed-identity.md)

로컬로 스크립트를 개발하는 경우 동일한 결과를 얻기 위해 테스트할 때 Automation 계정에서 사용하는 것과 동일한 모듈 버전을 로컬에서 사용하는 것이 좋습니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

## <a name="obtain-a-runbook-to-use-for-updates"></a>업데이트에 사용할 Runbook 가져오기

Automation 계정에서 Azure 모듈을 업데이트하려면 오픈 소스로 제공되는 [Update-AutomationAzureModulesForAccount](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update) Runbook을 사용해야 합니다. 이 Runbook을 사용하여 Azure 모듈을 업데이트하려면 GitHub 리포지토리에서 다운로드합니다. 그런 다음, Automation 계정으로 가져오거나 스크립트로 실행할 수 있습니다. Automation 계정에서 Runbook을 가져오는 방법은 [Runbook 가져오기](manage-runbooks.md#import-a-runbook)를 참조하세요.

**Update-AutomationAzureModulesForAccount** Runbook은 기본적으로 Azure, AzureRM 및 Az 모듈의 업데이트를 지원합니다. 이 Runbook으로 Az.Automation 모듈을 업데이트하는 방법에 대한 자세한 내용은 [Azure 모듈 Runbook 업데이트 README](https://github.com/microsoft/AzureAutomation-Account-Modules-Update/blob/master/README.md)를 참조하세요. Automation 계정에서 Az 모듈을 사용할 때 고려해야 하는 또 다른 중요한 요소가 있습니다. 자세한 내용은 [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.

## <a name="use-update-runbook-code-as-a-regular-powershell-script"></a>업데이트 Runbook 코드를 일반 PowerShell 스크립트로 사용

Runbook 대신 Runbook 코드를 일반 PowerShell 스크립트로 사용할 수 있습니다. 이렇게 하려면 [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) cmdlet을 사용하여 Azure에 로그인한 다음, 스크립트에 `-Login $false`를 전달합니다.

## <a name="use-the-update-runbook-on-sovereign-clouds"></a>소버린 클라우드에서 업데이터 Runbook 사용

소버린 클라우드에서 이 Runbook을 사용하려면 `AzEnvironment` 매개 변수를 사용하여 올바른 환경을 Runbook에 전달합니다. 허용되는 값은 AzureCloud(Azure 퍼블릭 클라우드), AzureChinaCloud, AzureGermanCloud 및 AzureUSGovernment입니다. 이러한 값은 `Get-AzEnvironment | select Name`을 사용하여 검색할 수 있습니다. 이 cmdlet에 값을 전달하지 않으면 Runbook은 기본적으로 AzureCloud를 사용합니다.

## <a name="use-the-update-runbook-to-update-a-specific-module-version"></a>업데이트 Runbook을 사용하여 특정 모듈 버전 업데이트

PowerShell 갤러리에 제공되는 최신 모듈 버전 대신 특정 Azure PowerShell 모듈 버전을 사용하려면 해당 버전을 **Update-AutomationAzureModulesForAccount** Runbook의 선택적 `ModuleVersionOverrides` 매개 변수에 전달합니다. 예제는 [Update-AutomationAzureModulesForAccount.ps1](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update/blob/master/Update-AutomationAzureModulesForAccount.ps1) Runbook을 참조하세요. `ModuleVersionOverrides` 매개 변수에 언급되지 않은 Azure PowerShell 모듈은 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. `ModuleVersionOverrides` 매개 변수에 아무 것도 전달하지 않으면 모든 모듈이 PowerShell 갤러리의 최신 모듈 버전으로 업데이트됩니다. 이 동작은 Azure Portal의 **Azure 모듈 업데이트** 단추와 동일합니다.

## <a name="next-steps"></a>다음 단계

* 모듈 사용에 대한 자세한 내용은 [Azure Automation에서 모듈 관리](shared-resources/modules.md)를 참조하세요.
* 업데이트 Runbook에 대한 자세한 내용은 [Azure 모듈 Runbook 업데이트](https://github.com/Microsoft/AzureAutomation-Account-Modules-Update)를 참조하세요.
