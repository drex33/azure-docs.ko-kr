---
title: 조건부 액세스 정책 빌드 - Azure Active Directory
description: 조건부 액세스 정책을 빌드하는 데 사용할 수 있는 모든 옵션에는 어떤 것들이 있으며 그 의미는 무엇인가요?
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/17/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 674f4cf80d68095ebd7b134c679b60b45ff2a548
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619347"
---
# <a name="building-a-conditional-access-policy"></a>조건부 액세스 정책 빌드

[조건부 액세스란](overview.md) 문서에 설명된 대로 조건부 액세스 정책은 **할당** 및 **액세스 제어** 의 if-then 문입니다. 조건부 액세스 정책은 결정을 내리고 조직 정책을 적용하기 위해 신호를 함께 제공합니다.

조직에서 해당 정책을 만드는 방식은 무엇이며 필요한 항목과 적용 방식은 어떻게 되나요?

![조건부 액세스(신호 + 결정 + 적용 = 정책)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

언제든지 개별 사용자에게 여러 조건부 액세스 정책을 적용할 수 있습니다. 이 경우 적용되는 모든 정책이 충족되어야 합니다. 예를 들어 한 정책에 MFA(다단계 인증)가 필요하고 다른 정책에 규정 준수 디바이스가 필요한 경우 MFA를 완료한 다음 규정 준수 디바이스를 사용해야 합니다. 모든 할당은 논리적으로 **and** 가 적용됩니다. 하나를 초과하여 할당을 구성한 경우 모든 할당이 충족되어야 정책을 트리거할 수 있습니다.

모든 정책은 다음 두 단계로 적용됩니다.

- 1단계: 세션 세부 정보 수집 
   - 정책 평가에 필요한 네트워크 위치 및 디바이스 ID와 같은 세션 세부 정보를 수집합니다. 
   - 정책 평가의 1단계는 사용하도록 설정된 정책 및 [보고 전용 모드](concept-conditional-access-report-only.md)의 정책에 대해 발생합니다.
- 2단계: 적용 
   - 1단계에서 수집한 세션 세부 정보를 사용하여 충족되지 않은 요구 사항을 확인합니다. 
   - 액세스를 차단하도록 구성된 정책이 있는 경우 차단 권한 부여 컨트롤을 사용하면 적용이 중지되고 사용자가 차단됩니다. 
   - 정책이 충족될 때까지 다음 순서에 따라 1단계에서 충족되지 않은 추가 권한 부여 컨트롤 요구 사항을 완료하라는 메시지가 사용자에게 표시됩니다.  
      - Multi-Factor Authentication 
      - 승인된 클라이언트 앱/앱 보호 정책 
      - 관리 디바이스(규정 준수 또는 하이브리드 Azure AD 조인) 
      - 사용 약관 
      - 사용자 지정 컨트롤  
   - 모든 권한 부여 컨트롤이 충족되면 세션 컨트롤(앱 적용, Microsoft Cloud App Security 및 토큰 수명)을 적용합니다. 
   - 정책 평가의 2단계는 사용하도록 설정된 모든 정책에 대해 발생합니다. 

## <a name="assignments"></a>할당

할당 부분은 조건부 액세스 정책의 사용자, 대상, 위치를 제어합니다.

### <a name="users-and-groups"></a>사용자 및 그룹

[사용자 및 그룹](concept-conditional-access-users-groups.md)은 정책에 포함하거나 정책에서 제외할 사용자를 할당합니다. 이 할당에는 모든 사용자, 특정 사용자 그룹, 디렉터리 역할 또는 외부 게스트 사용자가 포함될 수 있습니다. 

### <a name="cloud-apps-or-actions"></a>클라우드 앱 또는 작업

[클라우드 앱 또는 작업](concept-conditional-access-cloud-apps.md)은 정책이 적용될 클라우드 애플리케이션, 사용자 작업 또는 인증 컨텍스트를 포함하거나 제외할 수 있습니다.

### <a name="conditions"></a>조건

정책에는 여러 [조건](concept-conditional-access-conditions.md)이 포함될 수 있습니다.

#### <a name="sign-in-risk"></a>로그인 위험

[Azure AD ID 보호](../identity-protection/overview-identity-protection.md)를 사용하는 조직의 경우 생성되는 위험 검색이 조건부 액세스 정책에 영향을 미칠 수 있습니다.

#### <a name="device-platforms"></a>디바이스 플랫폼

여러 디바이스 운영 체제 플랫폼을 사용하는 조직에서는 다양한 플랫폼에 특정 정책을 적용하려고 할 수 있습니다. 

디바이스 플랫폼을 계산하는 데 사용되는 정보는 변경할 수 있는 사용자 에이전트 문자열과 같은 확인되지 않은 소스에서 제공됩니다.

#### <a name="locations"></a>위치

위치 데이터는 IP 지리적 위치 데이터에서 제공됩니다. 관리자는 위치를 정의하고 조직의 네트워크 위치와 같이 신뢰할 수 있는 위치로 표시하도록 선택할 수 있습니다.

#### <a name="client-apps"></a>클라이언트 앱

기본값으로 새로 만든 모든 조건부 액세스 정책은 클라이언트 앱 조건이 구성되지 않은 경우에도 모든 클라이언트 앱 형식에 적용됩니다.

클라이언트 앱 조건의 동작은 2020년 8월에 업데이트되었습니다. 기존 조건부 액세스 정책이 있는 경우 변경되지 않은 상태로 유지됩니다. 그러나 기존 정책을 클릭하면 구성 토글이 제거되고 정책이 적용되는 클라이언트 앱이 선택됩니다.

#### <a name="device-state"></a>디바이스 상태

이 컨트롤은 하이브리드 Azure AD 조인되었거나 Intune에서 규정을 준수하는 것으로 표시된 디바이스를 제외하는 데 사용됩니다. 비관리 디바이스를 차단하기 위해 이 제외 작업을 수행할 수 있습니다. 

#### <a name="filters-for-devices-preview"></a>장치용 필터(미리 보기)

이 컨트롤을 사용하여 정책의 특성에 따라 특정 디바이스를 대상으로 지정할 수 있습니다.

## <a name="access-controls"></a>액세스 제어

조건부 액세스 정책의 액세스 제어 부분은 정책이 적용되는 방식을 제어합니다.

### <a name="grant"></a>허용

[허용](concept-conditional-access-grant.md)은 관리자에게 액세스를 차단하거나 액세스 권한을 부여할 수 있는 정책 적용 수단을 제공합니다.

#### <a name="block-access"></a>액세스 차단

액세스를 차단하면 지정된 할당에 따라 액세스를 차단할 수 있습니다. 차단 컨트롤은 강력한 기능이기 때문에 알맞은 지식을 갖춘 채로 행사해야 합니다.

#### <a name="grant-access"></a>액세스 권한 부여

권한 부여 컨트롤은 하나 이상의 컨트롤에 대한 적용을 트리거할 수 있습니다. 

- 다단계 인증 필요(Azure AD Multi-Factor Authentication)
- 디바이스를 규정 준수 상태로 표시해야 함(Intune)
- 하이브리드 Azure AD 조인된 디바이스 필요
- 승인된 클라이언트 앱 필요
- 앱 보호 정책 필요
- 암호 변경 필요
- 사용 약관 필요

관리자는 다음 옵션을 사용하여 이전 컨트롤 또는 모든 선택된 컨트롤 중 하나를 요구하도록 선택할 수 있습니다. 여러 컨트롤의 기본값은 모두를 요구하는 것입니다.

- 선택한 모든 컨트롤 필요(컨트롤 및 컨트롤)
- 선택한 컨트롤 중 하나가 필요(컨트롤 또는 컨트롤)

### <a name="session"></a>세션

[세션 컨트롤](concept-conditional-access-session.md)을 통해 환경을 제한할 수 있습니다. 

- 앱에서 적용된 제한 사항 사용
   - 현재 Exchange Online 및 SharePoint Online에서만 작동합니다.
      - 전체 또는 제한된 액세스 권한을 부여하는 환경을 제어할 수 있도록 디바이스 정보를 전달합니다.
- 조건부 액세스 앱 제어 사용
   - Microsoft Cloud App Security의 신호를 사용하여 다음과 같은 작업을 수행합니다. 
      - 중요한 문서의 다운로드, 잘라내기, 복사, 인쇄를 차단합니다.
      - 위험한 세션 동작을 모니터링합니다.
      - 중요한 파일의 레이블을 지정해야 합니다.
- 로그인 빈도
   - 최신 인증에 대한 기본 로그인 빈도를 변경하는 기능입니다.
- 영구 브라우저 세션
   - 사용자가 브라우저 창을 닫은 후에 다시 열었을 때 로그인 상태가 유지되도록 합니다.

## <a name="simple-policies"></a>단순 정책

조건부 액세스 정책을 적용하려면 최소한 다음을 포함해야 합니다.

- 정책의 **이름**
- **할당**
   - 정책을 적용할 **사용자 및/또는 그룹**
   - 정책을 적용할 **클라우드 앱 또는 작업**
- **액세스 제어**
   - **권한 부여** 또는 **차단** 컨트롤

![비어 있는 조건부 액세스 정책](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

[일반적인 조건부 액세스 정책](concept-conditional-access-policy-common.md) 문서에는 대부분의 조직에 유용한 몇 가지 정책이 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계

[조건부 액세스 정책 만들기](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json#create-a-conditional-access-policy)

[조건부 액세스 What If 도구를 사용하여 로그인 동작 시뮬레이션](troubleshoot-conditional-access-what-if.md)

[클라우드 기반 Azure AD Multi-Factor Authentication 배포 계획](../authentication/howto-mfa-getstarted.md)

[Intune을 사용하여 디바이스 규정 준수 관리](/intune/device-compliance-get-started)

[Microsoft Cloud App Security 및 조건부 액세스](/cloud-app-security/proxy-intro-aad)
