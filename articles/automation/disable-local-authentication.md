---
title: Azure Automation에서 로컬 인증 사용 안 함
description: 이 문서에서는 Azure Automation에서 로컬 인증을 비활성화 하는 방법을 설명 합니다.
services: automation
ms.subservice: process-automation
ms.date: 09/28/2021
ms.topic: how-to
ms.openlocfilehash: 29caef661b7078844991c3ca3867a1f03eb0114e
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129294383"
---
# <a name="disable-local-authentication-in-automation"></a>Automation에서 로컬 인증 사용 안 함

Azure Automation는 모든 Automation 서비스 공용 끝점에 대 한 Microsoft Azure Active Directory (Azure AD) 인증 지원을 제공 합니다. 이 중요 한 보안 기능은 인증서 종속성을 제거 하 고 조직에서 로컬 인증 방법을 사용 하지 않도록 설정 하는 권한을 부여 합니다. 이 기능은 Azure AD를 통해 id 및 리소스 자격 증명을 중앙 집중식으로 제어 하 고 관리 해야 하는 경우 원활한 통합을 제공 합니다.

Azure Automation는 Azure 정책 [구성 Azure Automation 계정을 사용 하 여 로컬 인증을 사용 하지 않도록 설정](../automation/policy-reference.md#azure-automation)하는 Automation 계정 수준에서 "로컬 인증 사용 안 함"의 선택적 기능을 제공 합니다. 기본적으로이 플래그는 계정에서 false로 설정 되므로 로컬 인증과 Azure AD 인증을 모두 사용할 수 있습니다. 로컬 인증을 사용 하지 않도록 선택 하는 경우 Automation 서비스는 Azure AD 기반 인증만 허용 합니다.

인증을 사용 하지 않도록 설정한 경우 Azure Portal에서 선택한 Automation 계정에 대 한 경고 메시지가 표시 될 수 있습니다.로컬 인증 정책이 사용 되는지 확인 하려면 PowerShell cmdlet [AzAutomationAccount](/powershell/module/az.automation/get-azautomationaccount) 및 check 속성을 사용 합니다 `DisableLocalAuth` . 값은 `true` 로컬 인증을 사용 하지 않음을 의미 합니다.

로컬 인증을 사용 하지 않도록 설정 하는 것은 즉시 적용 되지 않습니다. 서비스가 향후 인증 요청을 차단 하는 데 몇 분이 걸립니다.
 
## <a name="re-enable-local-authentication"></a>로컬 인증 다시 사용

로컬 인증을 다시 사용 하도록 설정 하려면 매개 변수를 사용 하 여 PowerShell cmdlet을 실행 합니다 `Set-AzAutomationAccount` `-DisableLocalAuth false` .서비스에서 로컬 인증 요청을 허용 하기 위해 변경 내용을 적용 하는 데 몇 분 정도 걸릴 수 있습니다. 

## <a name="compatibility"></a>호환성

다음 표에서는 로컬 인증을 사용 하지 않도록 설정 하 여 작동 하지 않는 동작 또는 기능을 설명 합니다.

|시나리오 | 대체 |
|---|---|
|Webhook를 사용 하 여 runbook 시작 | Azure AD 인증을 사용 하는 Azure Resource Manager 템플릿을 사용 하 여 runbook 작업을 시작 합니다. |
|Automation Desired State Configuration를 사용 합니다.| [Azure Policy 게스트 구성을](/governance/policy/concepts/guest-configuration)사용 합니다.  |
|에이전트 기반 Hybrid Runbook Worker를 사용 합니다.| [확장 기반 Hybrid Runbook worker (미리 보기)](./extension-based-hybrid-runbook-worker-install.md)를 사용 합니다.|

## <a name="limitations"></a>제한 사항

로컬 인증을 사용 하지 않도록 설정 하면 업데이트 관리 패치가 작동 하지 않습니다.


## <a name="next-steps"></a>다음 단계
- [Azure Automation 계정 인증 개요](./automation-security-overview.md)