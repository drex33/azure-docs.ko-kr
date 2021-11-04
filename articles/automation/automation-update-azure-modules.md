---
title: Azure Automation에서 Azure PowerShell 모듈 업데이트
description: 이 문서에서는 Azure Automation에 기본적으로 제공되는 일반적인 Azure PowerShell 모듈을 업데이트하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/24/2021
ms.topic: conceptual
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 4344d8c12c139c428f44d417ccde2c5e950ed307
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131477171"
---
# <a name="update-azure-powershell-modules-in-automation"></a>Automation에서 Azure PowerShell 모듈 업데이트

기본적으로 각 Automation 계정에는 가장 일반적인 Azure PowerShell 모듈이 제공됩니다. [기본 모듈](shared-resources/modules.md#default-modules)을 참조하세요. Azure 팀이 Azure 모듈을 정기적으로 업데이트하므로, 포함된 cmdlet을 통해 변경 작업이 수행될 수 있습니다. 예를 들어 매개 변수의 이름을 바꾸거나 cmdlet을 완전히 사용 중단하는 변경 내용은 Runbook에 부정적인 영향을 미칠 수 있습니다. 

> [!NOTE]
> Automation에서 기본적으로 제공하는 모듈인 글로벌 모듈은 삭제할 수 없습니다.

## <a name="set-up-an-automation-account"></a>Automation 계정 설정

Runbook 및 Runbook이 자동화하는 프로세스에 영향을 주지 않으려면 업데이트할 때 테스트 및 유효성 검사를 꼭 수행해야 합니다. 이 용도로 설계된 전용 Automation 계정이 없는 경우 만들어서 Runbook을 개발하는 동안 다양한 시나리오를 테스트할 수 있습니다. 이 테스트에는 PowerShell 모듈 업데이트 같은 반복적인 변경도 포함되어야 합니다.

Automation 계정에 [시스템 할당 관리 id 또는 사용자 할당 관리 id](quickstarts/enable-managed-identity.md)가 추가 되었는지 확인 합니다.

로컬로 스크립트를 개발하는 경우 동일한 결과를 얻기 위해 테스트할 때 Automation 계정에서 사용하는 것과 동일한 모듈 버전을 로컬에서 사용하는 것이 좋습니다. 결과의 유효성을 검사하고 필수 변경 내용을 적용한 후에 변경 내용을 프로덕션으로 이동할 수 있습니다.

> [!NOTE]
> 새 자동화 계정에 최신 모듈이 없을 수도 있습니다.

## <a name="update-az-modules"></a>업데이트 Az modules

현재 업데이트 AZ modules는 포털을 통해서만 사용할 수 있습니다. PowerShell 및 ARM 템플릿을 통한 업데이트는 나중에 제공 될 예정입니다. 다음 단계를 수행할 때 기본 Az 모듈만 업데이트 됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Automation 계정으로 이동합니다.
1.  **공유 리소스** 아래에서 **모듈** 을 선택 합니다.
1.  **업데이트 Az modules** 을 선택 합니다.
1. **업데이트할 모듈을** 선택 합니다. 기본적으로 Az module이 표시 됩니다.  
1. 드롭다운 목록에서 **모듈 버전** 및 **runtype 버전** 을 선택 합니다.
1. **업데이트** 를 선택 하 여 Az module을 선택한 버전으로 업데이트 합니다.
   **모듈** 페이지에서 아래와 같이 목록을 볼 수 있습니다.

   :::image type="content" source="./media/automation-update-azure-modules/update-az-modules-portal.png" alt-text="선택 항목으로 AZ modules 페이지를 업데이트 합니다.":::

Automation 계정에서 가져온 기존 Az module 버전 보다 낮은 버전을 선택 하는 경우 업데이트 작업은 선택한 낮은 버전으로 롤백을 수행 합니다.  

**PowerShell 모듈** 의 **모듈** 목록에 표시 된 업데이트 된 모듈의 모듈 버전 및 상태 속성을 확인 하 여 업데이트 작업을 확인할 수 있습니다. 

Azure 팀은 모듈 버전을 정기적으로 업데이트 하 고 드롭다운 목록에서 모듈 버전을 선택 하 여 **기본** Az 모듈을 업데이트 하는 옵션을 제공 합니다.  

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
