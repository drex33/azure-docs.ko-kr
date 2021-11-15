---
title: Azure AD 조건부 액세스에 대한 복원력 기본값
description: 복원력 기본값 및 Azure AD Backup 인증 서비스
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 09/13/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: dawoo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e2bebe9769d38502a37134326228d41979cad5a
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432684"
---
# <a name="conditional-access-resilience-defaults-preview"></a>조건부 액세스: 복원력 기본값(미리 보기)

기본 인증 서비스의 작동이 중단된 경우 Azure AD(Azure Active Directory) Backup 인증 서비스에서 기존 세션에 대한 애플리케이션에 액세스 토큰을 자동으로 발급할 수 있습니다. 기존 세션의 재인증이 Azure AD에 대한 인증의 90%를 초과하기 때문에 이 기능은 Azure AD 복원력을 크게 향상시킬 수 있습니다. 백업 인증 서비스는 게스트 사용자가 새 세션 또는 인증을 지원하지 않습니다.

조건부 액세스로 보호되는 인증의 경우 다음을 확인하기 위해 액세스 토큰이 발급되기 전에 정책이 다시 평가됩니다.

1.  적용되는 조건부 액세스 정책
1.  적용되는 정책의 경우 필수 컨트롤이 충족되었나요?

가동 중단 중에는 Backup 인증 서비스에서 일부 조건을 실시간으로 평가하여 조건부 액세스 정책을 적용할지 여부를 결정할 수 없습니다. 조건부 액세스 복원력 기본값은 관리자가 중단 시 인증을 차단할지 여부를 결정할 수 있도록 하는 새 세션 컨트롤입니다. 이 컨트롤은 정책 조건을 실시간으로 평가할 수 없거나 사용자 세션의 시작 부분에 수집된 데이터를 사용하여 정책을 평가할 수 있습니다. 

> [!IMPORTANT]
> 복원력 기본값은 모든 신규 및 기존 정책에 대해 자동으로 사용하도록 설정되며, Microsoft는 복원력 기본값을 사용하도록 설정된 상태로 유지하여 가동 중단의 영향을 완화할 수 있습니다. 관리자는 개별 조건부 액세스 정책에 대해 복원력 기본값을 사용하지 않도록 설정할 수 있습니다. 

## <a name="how-does-it-work"></a>작동 원리

가동 중단 중에 Backup 인증 서비스는 특정 세션에 대한 액세스 토큰을 자동으로 다시 발급합니다.

| 세션 설명 | 액세스 권한 부여됨 |
| --- | --- |
| 새 세션 | 예 |
| 기존 세션 - 조건부 액세스 정책이 구성되지 않음 | 예 |
| 기존 세션 – 구성된 조건부 액세스 정책 및 MFA와 같은 필수 컨트롤이 이전에 충족되었음 | 예 |
| 기존 세션 – 구성된 조건부 액세스 정책 및 MFA와 같은 필수 컨트롤이 이전에 충족되지 않았음 | 복원력 기본값에 의해 결정됨 |

Azure AD 중단 중 기존 세션이 만료되면 새 액세스 토큰에 대한 요청이 Backup 인증 서비스로 라우팅되고 모든 조건부 액세스 정책이 재평가됩니다. 조건부 액세스 정책이 없거나 MFA와 같은 모든 필수 컨트롤이 이전에 세션을 시작할 때 충족된 경우 Backup 인증 서비스에서 새 액세스 토큰을 발급하여 세션을 확장합니다. 

정책의 필수 컨트롤이 이전에 충족되지 않은 경우 정책을 다시 평가하여 액세스를 허용할지 또는 거부할지를 결정합니다. 그러나 중단 중에는 일부 조건을 실시간으로 다시 평가하지 못할 수 있습니다. 이러한 조건은 다음과 같습니다. 

- 그룹 멤버 자격
- 역할 멤버 자격
- 로그인 위험
- 사용자 위험
- 국가 위치(새 IP 또는 GPS 좌표 확인)

## <a name="resilience-defaults-enabled"></a>복원력 기본값 사용

복원력 기본값을 사용하는 경우 Backup 인증 서비스는 세션의 시작 부분에 수집된 데이터를 사용하여 실시간 데이터가 없을 때 정책을 적용할지 여부를 평가할 수 있습니다. 기본적으로 모든 정책의 복원력 기본값은 사용하도록 설정됩니다. 가동 중단 중에 중요한 애플리케이션에 액세스하기 위해 실시간 정책 평가가 필요할 때 개별 정책에 대해 이 설정을 비활성화할 수 있습니다.

**예제**: 복원력 기본값을 사용하는 정책에는 MFA를 수행하기 위해 Azure Portal에 액세스하는 모든 전역 관리자가 필요합니다. 중단 전에는 전역 관리자가 아닌 사용자가 Azure Portal에 액세스하면 정책이 적용되지 않으며 사용자에게 MFA를 요구하는 메시지가 표시되지 않고 액세스 권한이 부여됩니다. 가동 중단 중에 Backup 인증 서비스는 정책을 다시 평가하여 사용자에게 MFA를 묻는 메시지를 표시할지 여부를 결정합니다. **Backup 인증 서비스는 역할 멤버 자격을 실시간으로 평가할 수 없으므로 사용자 세션의 시작 부분에 수집된 데이터를 사용하여 정책이 적용되지 않아야 하는지 확인합니다. 따라서 사용자에게 MFA를 요청하지 않고 액세스 권한이 부여됩니다.**

## <a name="resilience-defaults-disabled"></a>복원력 기본값 사용 안 함

복원력 기본값을 사용하지 않도록 설정하는 경우 Backup 인증 서비스는 세션의 시작 부분에 수집된 데이터를 사용하여 조건을 평가하지 않습니다. 가동 중단 중에 정책 조건을 실시간으로 평가할 수 없는 경우 액세스가 거부됩니다.

**예제**: 복원력 기본값을 사용하지 않는 정책에는 MFA를 수행하기 위해 Azure Portal에 액세스하는 모든 전역 관리자가 필요합니다. 중단 전에는 전역 관리자가 아닌 사용자가 Azure Portal에 액세스하면 정책이 적용되지 않으며 사용자에게 MFA를 요구하는 메시지가 표시되지 않고 액세스 권한이 부여됩니다. 가동 중단 중에 Backup 인증 서비스는 정책을 다시 평가하여 사용자에게 MFA를 묻는 메시지를 표시할지 여부를 결정합니다. **Backup 인증 서비스는 역할 멤버 자격을 실시간으로 평가할 수 없으므로 사용자가 Azure Portal에 액세스하지 못하도록 차단합니다.**

> [!WARNING]
> 그룹 또는 역할에 적용되는 정책에 대해 복원력 기본값을 사용하지 않도록 설정하면 테넌트의 모든 사용자에 대한 복원력이 줄어듭니다. 가동 중단 중에는 그룹 및 역할 멤버 자격을 실시간으로 평가할 수 없으므로 정책 할당의 그룹 또는 역할에 속하지 않는 사용자도 정책 범위에서 애플리케이션에 대한 액세스가 거부됩니다. 정책 범위에 없는 모든 사용자에 대한 복원력을 줄이려면 그룹 또는 역할이 아닌 개별 사용자에게 정책을 적용하는 것이 좋습니다. 

## <a name="testing-resilience-defaults"></a>복원력 기본값 테스트

Backup 인증 서비스를 사용하여 시험 실행을 수행하거나 현재 복원력 기본값을 사용 또는 사용하지 않도록 설정하여 정책 결과를 시뮬레이션할 수 없습니다. Azure AD는 Backup 인증 서비스를 사용하여 월간 연습을 수행하고, Backup 인증 서비스를 사용하여 액세스 토큰을 발급한 경우 로그인 로그를 표시합니다.

## <a name="configuring-resilience-defaults"></a>복원력 기본값 구성

Azure Portal, MS Graph API 또는 PowerShell에서 조건부 액세스 복원력 기본값을 구성할 수 있습니다. 

### <a name="azure-portal"></a>Azure Portal

1.  **Azure Portal** > **보안** > **조건부 액세스** 로 이동
1.  새 정책 만들기 또는 기존 정책 선택
1.  세션 컨트롤 설정 열기
1.  복원력 기본값 사용 안 함을 선택하여 이 정책에 대한 설정을 사용하지 않습니다. Azure AD 중단 시 정책 범위의 로그인이 차단됩니다.
1.  정책에 대한 변경 내용 저장

### <a name="ms-graph-apis"></a>MS Graph API

MS Graph API 및 [Microsoft Graph Explorer](/graph/graph-explorer/graph-explorer-overview)를 사용하여 조건부 액세스 정책에 대한 복원력 기본값을 관리할 수도 있습니다. 

샘플 요청 URL: 

`PATCH https://graph.microsoft.com/beta/identity/conditionalAccess/policies/policyId`

샘플 요청 본문: 

```json

{
"sessionControls": {
"disableResilienceDefaults": true
}
}
```

### <a name="powershell"></a>PowerShell

Microsoft.Graph.Authentication 모듈을 설치한 후 Microsoft PowerShell을 사용하여 이 패치 작업을 배포할 수 있습니다. 이 모듈을 설치하려면 관리자 권한 PowerShell 프롬프트를 열고 실행합니다.

`Install-Module Microsoft.Graph.Authentication`

필요한 범위를 요청하는 Microsoft Graph에 연결 -

`Connect-MgGraph -Scopes Policy.Read.All,Policy.ReadWrite.ConditionalAccess,Application.Read.All -TenantId <TenantID>`

메시지가 표시되면 인증합니다.

PATCH 요청에 대한 JSON 본문 만들기 –

`$patchBody = '{"sessionControls": {"disableResilienceDefaults": true}}'`

패치 작업 실행 –

`Invoke-MgGraphRequest -Method PATCH -Uri https://graph.microsoft.com/beta/identity/conditionalAccess/policies/<PolicyID> -Body $patchBody`

## <a name="recommendations"></a>권장 사항

복원력 기본값을 설정하는 것이 좋습니다. 직접적인 보안 문제는 없지만, 고객은 세션의 시작 부분에서 수집된 데이터를 실시간으로 사용하여 작동 중단 중에 Backup 인증 서비스에서 조건부 액세스 정책을 평가하도록 허용할지 여부를 평가해야 합니다. 

세션이 시작된 이후에 사용자의 역할 또는 그룹 멤버 자격이 변경될 수 있습니다. [CAE(지속적인 액세스 권한 평가)](concept-continuous-access-evaluation.md)를 사용하는 경우 액세스 토큰은 24시간 동안 유효하지만 인스턴트 해지 이벤트의 영향을 받습니다. Backup 인증 서비스는 동일한 해지 이벤트 CAE를 구독합니다. 사용자의 토큰이 CAE의 일부로 취소되는 경우 사용자는 중단 중에 로그인할 수 없습니다. 복원력 기본값을 사용하도록 설정하면 가동 중단 중에 만료되는 기존 세션이 확장됩니다. 로그인 빈도를 적용하기 위해 세션 제어를 사용하여 정책을 구성한 경우에도 세션이 확장됩니다. 예를 들어 복원력 기본값을 사용하도록 설정된 정책은 사용자가 1시간마다 다시 인증해야 SharePoint 사이트에 액세스할 수 있습니다. 사용자를 다시 인증하는 데 Azure AD를 사용할 수 없는 경우에도 가동 중단 중에 사용자의 세션이 확장됩니다. 

## <a name="next-steps"></a>다음 단계

- [CAE(지속적인 액세스 권한 평가)](concept-continuous-access-evaluation.md)
