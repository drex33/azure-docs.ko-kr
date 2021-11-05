---
title: 위험 정책 - Azure Active Directory Identity Protection
description: Azure Active Directory Identity Protection에서 위험 정책 사용 및 구성
services: active-directory
ms.service: active-directory
ms.subservice: identity-protection
ms.topic: how-to
ms.date: 10/26/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: sahandle
ms.collection: M365-identity-device-management
ms.openlocfilehash: c9e3bfc88442f495d49116e01cc06e876be95787
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051594"
---
# <a name="how-to-configure-and-enable-risk-policies"></a>방법: 위험 정책 구성 및 사용

이전 문서에서 설명한 대로 [ID 보호 정책](concept-identity-protection-policies.md)에는 디렉터리에서 사용할 수 있는 두 가지 위험 정책이 있습니다. 

- 로그인 위험 정책
- 사용자 위험 정책

![사용자 및 로그인 위험 정책을 사용하도록 설정하는 보안 개요 페이지](./media/howto-identity-protection-configure-risk-policies/identity-protection-security-overview.png)

두 정책은 모두 해당 환경에서 위험 검색에 대한 대응을 자동화하며 위험이 검색될 때 사용자가 직접 수정할 수 있도록 합니다. 

## <a name="choosing-acceptable-risk-levels"></a>허용 가능한 위험 수준 선택

조직은 허용 가능한 위험 수준을 결정하여 사용자 환경과 보안 상태의 균형을 맞추어야 합니다. 

Microsoft의 권장 사항은 사용자 위험 정책 임계값을 **높음** 으로 설정하고 로그인 위험 정책을 **중간 이상** 으로 설정하고 자체 수정 옵션을 허용하는 것입니다. 암호 변경 및 다단계 인증과 같은 자체 수정 옵션을 허용하지 않고 액세스를 차단하도록 선택하면 사용자와 관리자에게 영향을 줍니다. 정책을 구성할 때 이 선택을 고려합니다.

**높음** 임계값을 선택하면 정책이 트리거되는 횟수를 줄이고 사용자에게 미치는 영향을 최소화합니다. 그러나 정책에서 **낮음** 및 **중간** 위험 검색을 제외하므로 손상된 ID를 악용하지 못하도록 공격자를 차단하지 못할 수 있습니다. **낮음** 임계값을 선택하면 더 많은 사용자 중단이 발생됩니다.

구성된 신뢰할 수 있는 [네트워크 위치](../conditional-access/location-condition.md)는 일부 위험 검색에서 Identity Protection이 가양성을 줄이는 데 사용됩니다.

### <a name="risk-remediation"></a>위험 수정

조직은 위험이 감지되면 액세스를 차단하도록 선택할 수 있습니다. 차단은 합법적인 사용자가 필요한 작업을 수행하지 못하게 하는 경우가 있습니다. 더 나은 솔루션은 Azure AD MFA(Multi-Factor Authentication) 및 SSPR(셀프 서비스 암호 재설정)을 사용하여 자체 수정을 허용하는 것입니다.

- 사용자 위험 정책이 트리거되는 경우: 
   - 관리자는 사용자가 SSPR을 통해 새 암호를 만들어 사용자 위험을 다시 설정하기 전에 Azure AD MFA를 수행해야 하는 보안 암호 재설정을 요구할 수 있습니다. 
- 로그인 위험 정책이 트리거되는 경우: 
   - Azure AD MFA를 트리거하여 사용자가 등록된 인증 방법 중 하나를 사용하여 본인임을 증명하고 로그인 위험을 다시 설정할 수 있습니다. 

> [!WARNING]
> 사용자는 수정이 필요한 상황에 직면하기 전에 Azure AD MFA 및 SSPR에 등록해야 합니다. 등록되지 않은 사용자는 차단되며 관리자 개입이 필요합니다.
> 
> 위험한 사용자 정책 수정 흐름 외부의 암호 변경(내 암호를 알고 새 암호로 변경하려는 경우)이 보안 암호 재설정 요구 사항을 충족하지 않습니다.

## <a name="exclusions"></a>제외

정책을 통해 [긴급 액세스 또는 비상 관리자 계정](../roles/security-emergency-access.md)과 같은 사용자를 제외할 수 있습니다. 조직은 계정이 사용되는 방식에 따라 특정 정책에서 다른 계정을 제외해야 할 수 있습니다. 제외 항목을 정기적으로 검토하여 해당 항목이 계속 적용되는지 확인해야 합니다.

## <a name="enable-policies"></a>정책 사용

이러한 정책을 구성할 수 있는 위치에는 조건부 액세스 및 ID 보호의 두 가지가 있습니다. 조건부 액세스 정책을 사용하는 구성은 다음을 비롯한 더 많은 컨텍스트를 제공하는 기본 방법입니다. 

   - 향상된 진단 데이터
   - 보고서 전용 모드 통합
   - Graph API 지원
   - 정책에서 더 많은 조건부 액세스 특성 사용

> [!VIDEO https://www.youtube.com/embed/zEsbbik-BTE]

수정 정책을 사용하기 전에 조직은 모든 액티브 위험을 [조사](howto-identity-protection-investigate-risk.md) 및 [해결](howto-identity-protection-remediate-unblock.md)하는 것이 좋습니다.

### <a name="user-risk-with-conditional-access"></a>조건부 액세스로 인한 사용자 위험

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.
   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **모든 클라우드 앱** 을 선택합니다.
1. **조건** > **사용자 위험** 에서 **구성** 을 **예** 로 설정합니다. **정책을 적용하기 위해 필요한 사용자 위험 수준 구성** 에서 **높음**, **완료** 를 차례로 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **암호 변경 필요**, **선택** 을 차례로 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **생성** 를 선택하여 정책을 사용하도록 생성합니다.

### <a name="sign-in-risk-with-conditional-access"></a>조건부 액세스를 사용한 로그인 위함

1. **Azure Portal** 에 전역 관리자, 보안 관리자 또는 조건부 액세스 관리자로 로그인합니다.
1. **Azure Active Directory** > **Security** > **조건부 액세스** 로 이동합니다.
1. **새 정책** 을 선택합니다.
1. 정책에 이름을 지정합니다. 조직에서 정책 이름에 의미 있는 표준을 만드는 것이 좋습니다.
1. **할당** 에서 **사용자 및 그룹** 을 선택합니다.
   1. **포함** 에서 **모든 사용자** 를 선택합니다.
   1. **제외** 에서 **사용자 및 그룹** 을 선택하고 조직의 응급 액세스 또는 비상 계정을 선택합니다. 
   1. **완료** 를 선택합니다.
1. **클라우드 앱 또는 작업** > **포함** 에서 **모든 클라우드 앱** 을 선택합니다.
1. **조건** > **로그인 위험** 에서 **구성** 을 **예** 로 설정합니다. **이 정책을 적용할 로그인 위험 수준 선택** 에서 
   1. **높음** 및 **중간** 을 선택합니다.
   1. **완료** 를 선택합니다.
1. **액세스 제어** > **권한 부여** 에서 **액세스 권한 부여**, **다단계 인증 요구**, **선택** 을 차례로 선택합니다.
1. 설정을 확인하고 **정책 사용** 을 **켜기** 로 설정합니다.
1. **만들기** 를 선택하여 정책을 만들어 사용하도록 설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD Multi-Factor Authentication 등록 정책 사용](howto-identity-protection-configure-mfa-policy.md)

- [위험이란?](concept-identity-protection-risks.md)

- [위험 검색 조사](howto-identity-protection-investigate-risk.md)

- [위험 검색 시뮬레이션](howto-identity-protection-simulate-risk.md)
