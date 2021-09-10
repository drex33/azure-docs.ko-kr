---
title: Azure AD Multi-Factor Authentication 프롬프트 및 세션 수명
description: Azure AD Multi-Factor Authentication으로 재인증 프롬프트, 및 세션 수명을 적용하는 방법에 대한 권장 구성에 대해 알아봅니다.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/12/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: inbarc
ms.collection: M365-identity-device-management
ms.openlocfilehash: 93f5af54992189d553c9e5c5c141a6ca08b7018e
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567447"
---
# <a name="optimize-reauthentication-prompts-and-understand-session-lifetime-for-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication의 재인증 프롬프트를 최적화하고 세션 수명 이해하기

Azure AD (Azure Active Directory)에는 사용자가 다시 인증해야 하는 빈도를 결정하는 여러 설정이 있습니다. 이러한 재인증은 암호, FIDO 또는 암호 없는 Microsoft Authenticator 등의 첫 번째 요소를 사용하거나, MFA(다단계 인증)를 수행할 수 있습니다. 이러한 재인증 설정을 현재 사용자 환경 및 원하는 사용자 환경에 맞게 구성할 수 있습니다.

사용자 로그인 빈도에 대한 Azure AD 기본 구성은 90일의 이동 기간입니다. 사용자에게 자격 증명을 요청하는 것은 합리적으로 보이는 경우가 많지만, 역효과를 낳을 수도 있습니다. 사용자가 생각하지도 않고 자격 증명을 입력하도록 학습된 경우, 이를 실수로 악의적인 자격 증명 프롬프트에 제공할 수 있습니다.

IT 정책을 위반하면 세션이 해지되긴 하지만, 사용자가 다시 로그인하도록 요청하지 않는 것이 걱정스러울 수 있습니다. 일부 이러한 예로는 암호 변경, 비준수 디바이스 또는 계정 비활성화 작업이 포함됩니다. [PowerShell을 사용하여 사용자 세션을 명시적으로 해지](/powershell/module/azuread/revoke-azureaduserallrefreshtoken)할 수도 있습니다.

이 문서에서는 권장되는 구성과 다양한 설정이 작동하고 상호 작용하는 방법에 대해 자세히 설명합니다.

## <a name="recommended-settings"></a>권장 설정

사용자에게 적절한 빈도로 로그인 하도록 요청하여 보안과 사용 편의성 간의 올바른 균형을 이루기 위해서는 다음 구성을 사용하는 것이 좋습니다.

* Azure AD Premium이 있는 경우, 다음을 수행합니다.
    * [관리 디바이스](../devices/overview.md) 또는 [원활한 SSO](../hybrid/how-to-connect-sso.md)를 사용하여 애플리케이션에서 SSO(Single Sign-On)를 사용하도록 설정합니다.
    * 재인증이 필요한 경우 조건부 액세스 [로그인 빈도 정책](../conditional-access/howto-conditional-access-session-lifetime.md)을 사용합니다.
    * 비관리 디바이스 또는 모바일 디바이스 시나리오에서 로그인하는 사용자의 경우 영구 브라우저 세션이 바람직하지 않을 수 있으며 조건부 액세스를 사용하여 로그인 빈도 정책을 통해 영구 브라우저 세션을 사용하도록 설정할 수 있습니다. 위험 수준이 낮은 사용자의 세션 기간이 더 긴 로그인 위험에 따라 적절한 시간으로 기간을 제한합니다.
* Microsoft 365 앱 라이선스 또는 무료 Azure AD 계층이 있는 경우, 다음을 수행합니다.
    * [관리 디바이스](../devices/overview.md) 또는 [원활한 SSO](../hybrid/how-to-connect-sso.md)를 사용하여 애플리케이션에서 SSO(Single Sign-On)를 사용하도록 설정합니다.
    * *로그인 유지* 옵션을 계속 사용하도록 설정하고, 사용자에게 동의하도록 안내합니다.
* 모바일 디바이스 시나리오의 경우, 사용자가 Microsoft Authenticator 앱을 사용하는지 확인합니다. 이 앱은 다른 Azure AD 페더레이션된 앱에 대한 브로커로 사용 되며, 디바이스에서 인증 프롬프트가 줄어듭니다.

당사의 연구 결과는 이러한 설정이 대부분의 테넌트에 적합하다는 것을 보여 줍니다. *MFA 기억하기*, *로그인 유지* 등의 일부 설정 조합으로 인해 지나치게 자주 인증하라는 메시지가 사용자에게 표시될 수 있습니다. 일반 재인증 프롬프트는 사용자 생산성에 좋지 않으며, 공격에 더 취약해질 수 있습니다.

## <a name="azure-ad-session-lifetime-configuration-settings"></a>Azure AD 세션 수명 구성 설정

사용자에 대한 인증 프롬프트의 빈도를 최적화하기 위해, Azure AD 세션 수명 옵션을 구성할 수 있습니다. 비즈니스 및 사용자의 요구 사항을 이해하고, 사용자 환경에 가장 적합한 균형을 제공하는 설정을 구성합니다.

### <a name="evaluate-session-lifetime-policies"></a>세션 수명 정책 평가

세션 수명 설정이 없으면 브라우저 세션에 영구적 쿠키도 없습니다. 사용자가 브라우저를 닫고 열 때마다 재인증에 대한 프롬프트가 표시됩니다. Office 클라이언트에서 기본 기간은 90일의 이동 기간입니다. 이 기본 Office 구성을 사용하는 경우, 사용자가 암호를 재설정하거나 90일 넘게 비활성 상태인 경우, 사용자는 모든 필수 요소(첫 번째 및 두 번째 요소)를 사용하여 다시 인증해야 합니다.

사용자에게 Azure AD에서 ID가 없는 디바이스에 여러 개의 MFA 프롬프트가 표시될 수 있습니다. 각 애플리케이션에 다른 클라이언트 앱과 공유되지 않는 자체 OAuth Refresh Token이 있는 경우, 프롬프트가 여러 개 표시됩니다. 이 시나리오에서 각 애플리케이션이 MFA를 사용하여 OAuth Refresh Token을 요청하여 유효성을 검사하므로, MFA는 여러 번 프롬프트를 표시합니다.

Azure AD에서 세션 수명에 가장 제한적인 정책이 사용자의 재인증 시기를 결정합니다. 다음 시나리오를 고려하세요.

* 영구적 브라우저 쿠키를 사용하는 *로그인 유지* 를 사용하도록 설정합니다. 그리고
* *14일 동안 MFA 기억* 을 사용하도록 설정할 수도 있습니다.

이 예제 시나리오에서는 사용자가 14일마다 재인증해야 합니다. 이 동작은 *로그인 상태 유지* 가 사용자에게 브라우저에서 재인증을 요구하지 않더라도 가장 제한적인 정책을 따릅니다.

### <a name="managed-devices"></a>관리되는 디바이스

Azure AD Join 또는 Hybrid Azure AD Join을 사용하여 Azure AD에 조인 디바이스는 애플리케이션 에서 SSO (Single Sign-On)를 사용하는 [PRT (Primary Refresh Tokens)](../devices/concept-primary-refresh-token.md)를 수신합니다. 이 PRT를 사용하면 사용자가 디바이스에서 한 번 로그인 할 수 있으며, IT 직원이 보안 및 규정 준수에 대한 표준을 충족하는지 확인할 수 있습니다. 일부 앱 또는 시나리오에서 조인 디바이스에 대해 더 자주 로그인 하도록 사용자에게 요청해야 하는 경우, [조건부 액세스 로그인 빈도](../conditional-access/howto-conditional-access-session-lifetime.md)를 사용하여 이를 수행할 수 있습니다.

### <a name="show-option-to-remain-signed-in"></a>로그인 상태를 유지하는 옵션 표시

로그인하는 동안 사용자가 *로그인 유지?* 옵션에서 **네** 를 선택하면, 브라우저에 영구적 쿠키가 설정됩니다. 이 영구적 쿠키는 첫 번째와 두 번째 요소를 모두 기억하며, 브라우저의 인증 요청에만 적용됩니다.

![로그인 상태를 유지하는 예제 프롬프트 스크린샷](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/stay-signed-in-prompt.png)

Azure AD Premium 1 라이선스가 있는 경우, *영구 브라우저 세션* 에 대한 조건부 액세스 정책을 사용하는 것이 좋습니다. 이 정책은 *로그인 유지?* 설정을 덮어쓰므로 향상된 사용자 환경을 제공합니다. Azure AD Premium 1 라이선스가 없는 경우, 사용자가 로그인 상태 유지 설정을 사용하는 것이 좋습니다.

사용자가 로그인 상태를 유지하도록 하는 옵션을 구성하는 방법에 대한 자세한 내용은 [AZURE AD 로그인 페이지 사용자 지정](../fundamentals/customize-branding.md#customize-your-azure-ad-sign-in-page)을 참조하세요.

### <a name="remember-multi-factor-authentication"></a>Multi-Factor Authentication 저장  

이 설정을 사용하면 사용자가 로그인 시 **X일 동안 다시 묻지 않음** 옵션을 선택하면, 1~365일 사이의 값을 구성하고 브라우저에서 영구 쿠키를 설정할 수 있습니다.

![로그인 요청을 승인하는 예제 프롬프트 스크린샷](./media/concepts-azure-multi-factor-authentication-prompts-session-lifetime/approve-sign-in-request.png)

이 설정은 웹앱에서 인증 수를 줄이면서도, Office 클라이언트와 같은 최신 인증 클라이언트에 대한 인증 수를 늘립니다. 이러한 클라이언트는 보통 암호 재설정 후 또는 90일 간 사용하지 않은 후에만 프롬프트를 표시합니다. 그러나 이 값을 90일 미만으로 설정하면 Office 클라이언트에 대한 기본 MFA 프롬프트 빈도가 짧아지고 재인증 빈도가 높아집니다. **로그인 유지** 또는 조건부 액세스 정책과 함께 사용되는 경우, 인증 요청 수가 늘어날 수 있습니다.

*MFA 기억* 을 사용하고 Azure AD Premium 1 라이선스가 있는 경우, 이러한 설정을 조건부 액세스 로그인 빈도로 마이그레이션하는 것이 좋습니다. 그렇지 않으면 *로그인 상태 유지?* 를 대신 사용하는 것이 좋습니다.

자세한 내용은 [다단계 인증 기억](howto-mfa-mfasettings.md#remember-multi-factor-authentication)을 참조하세요.

### <a name="authentication-session-management-with-conditional-access"></a>조건부 액세스를 사용하여 인증 세션 관리

관리자는 **로그인 빈도** 를 사용하여 클라이언트와 브라우저 모두의 첫 번째와 두 번째 요소에 적용되는 로그인 빈도를 선택할 수 있습니다. 중요 한 비즈니스 애플리케이션의 경우와 같이 인증 세션을 제한해야 하는 경우, 관리 디바이스와 함께 이러한 설정을 사용하는 것이 좋습니다.

**영구 브라우저 세션** 을 사용하면 사용자가 브라우저 창을 닫았다가 다시 연 후 로그인 상태를 유지할 수 있습니다. *로그인 유지* 설정과 마찬가지로 이것은 브라우저에서 영구적 쿠키를 설정합니다. 그러나 관리자가 구성했기 때문에 사용자가 *로그인 유지?* 옵션에서 **예** 를 선택하지 않아도 되므로 더 나은 사용자 환경을 제공합니다. *로그인 유지?* 옵션을 사용하는 경우, 대신 **영구 브라우저 세션** 정책을 사용하도록 설정하는 것이 좋습니다.

자세한 내용은 [조건부 액세스를 사용하여 인증 세션 관리를 구성](../conditional-access/howto-conditional-access-session-lifetime.md)을 참조하세요.

### <a name="configurable-token-lifetimes"></a>구성 가능한 토큰 수명

이 설정으로 Azure Active Directory에서 발급한 토큰의 수명을 구성할 수 있습니다. 이 정책은 *조건부 액세스를 사용하여 인증 세션 관리* 로 바뀝니다. 지금 *구성 가능한 토큰 수명* 을 사용하는 경우, 조건부 액세스 정책으로의 마이그레이션을 시작하는 것이 좋습니다.

## <a name="review-your-tenant-configuration"></a>테넌트 구성 검토  

이제 다양한 설정의 작동 원리와 권장 구성을 이해했으므로, 테넌트 구성을 확인하고 적절 하 게 변경하는 것을 시도할 차례입니다.

*로그인 유지* 옵션을 구성하거나 검토하려면 다음 단계를 완료합니다.

1. Azure Portal에서 *Azure Active Directory* 를 검색하여 선택합니다.
1. **회사 브랜딩** 을 선택한 다음, 각 로캘에 대해 **로그인 유지 옵션 표시** 를 선택합니다.
1. *예* 를 선택한 다음, **저장** 을 선택합니다.

신뢰할 수 있는 디바이스에서 다단계 인증을 기억하려면 다음 단계를 완료합니다.

1. Azure Portal에서 *Azure Active Directory* 를 검색하여 선택합니다.
1. **보안** 을 선택한 다음, **MFA** 를 선택합니다.
1. **구성** 아래에서 **추가 클라우드 기반 MFA 설정** 을 선택합니다.
1. *다단계 인증 서비스 설정* 페이지에서 **다단계 인증 설정 기억** 을 스크롤합니다. 확인란의 선택을 취소하여 설정을 해제합니다.

로그인 빈도 및 영구 브라우저 세션에 조건부 액세스 정책을 구성하려면, 다음 단계를 완료합니다.

1. Azure Portal에서 *Azure Active Directory* 를 검색하여 선택합니다.
1. **보안**, **조건부 액세스** 를 차례로 선택합니다.
1. 이 문서에 설명된 권장 세션 관리 옵션을 사용하여 정책을 구성합니다.

토큰 수명을 검토하려면 [AZURE Ad PowerShell을 사용하여 Azure AD 정책을 쿼리합니다](../develop/configure-token-lifetimes.md#get-started). 현재 적용되는 모든 정책을 사용하지 않도록 설정합니다.

테넌트에서 둘 이상의 설정을 사용하는 경우, 사용 가능한 라이선스에 따라 설정을 업데이트하는 것이 좋습니다. 예를 들어, Azure AD Premium 라이선스가 있는 경우, *로그인 빈도* 및 *영구 브라우저 세션* 의 조건부 액세스 정책만 사용해야 합니다. Microsoft 365 앱 또는 Azure AD 무료 라이선스가 있는 경우, *로그인 유지?* 구성을 사용해야 합니다.

구성 가능한 토큰 수명을 사용하는 경우, 이 기능은 곧 제거될 예정이라는 점에 주의하십시오. 조건부 액세스 정책으로의 마이그레이션을 계획합니다.

다음 표에는 라이선스에 따른 권장 사항이 요약되어 있습니다.

|              | Azure AD Free 및 Microsoft 365 앱 | Azure AD Premium |
|------------------------------|-----------------------------------|------------------|
| **SSO**                      | [Azure AD 조인](../devices/concept-azure-ad-join.md)이나 [하이브리드 Azure AD 조인](../devices/concept-azure-ad-join-hybrid.md), 또는 관리되지 않는 디바이스에 대한 [원활한 SSO](../hybrid/how-to-connect-sso.md) . | Azure AD 조인<br />하이브리드 Azure AD 조인 |
| **재인증 설정** | 로그인 유지                  | 로그인 빈도 및 영구 브라우저 세션에 대한 조건부 액세스 정책 사용 |

## <a name="next-steps"></a>다음 단계

시작하기 전에, [Azure AD Multi-Factor Authentication을 사용하여 사용자 로그인 이벤트 보호](tutorial-enable-azure-mfa.md) 또는 [사용자 로그인에 대한 위험 검색을 사용하여 Azure AD Multi-Factor Authentication를 트리거](tutorial-risk-based-sspr-mfa.md)에 대한 자습서를 충분히 참조합니다.
