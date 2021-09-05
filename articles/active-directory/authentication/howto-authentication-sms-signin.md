---
title: Azure Active Directory에 대한 SMS 기반 사용자 로그인
description: SMS를 사용하여 Azure Active Directory에 로그인하도록 구성하고 사용자를 활성화하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/18/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: anjusingh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 284a1c9d8b79a0d4e9c479afea9713a09c8c0fa4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122531022"
---
# <a name="configure-and-enable-users-for-sms-based-authentication-using-azure-active-directory"></a>Azure Active Directory를 사용한 SMS 기반 인증 구성 및 사용자 활성화 

애플리케이션 및 서비스에 대한 로그인을 단순화하고 보호하기 위해 Azure AD(Azure Active Directory)는 여러 인증 옵션을 제공합니다. SMS 기반 인증을 사용하면 사용자는 사용자 이름 및 암호를 제공하지 못하거나 심지어 알지 못해도 로그인할 수 있습니다. ID 관리자가 계정을 만든 후 로그인 프롬프트에 전화 번호를 입력할 수 있습니다. 로그인을 완료하기 위해 제공할 수 있는 문자 메시지를 통해 인증 코드를 수신합니다. 해당 인증 방법을 사용하면 특히 일선 작업자가 애플리케이션 및 서비스에 간편하게 액세스할 수 있습니다.

이 문서에서는 Azure AD에서 일부 사용자 또는 그룹에 대해 SMS 기반 인증을 사용하도록 설정하는 방법을 보여 줍니다. SMS 기반 로그인 사용을 지원하는 앱 목록은 [SMS 기반 인증을 위한 앱 지원](how-to-authentication-sms-supported-apps.md)을 참조하세요.

## <a name="before-you-begin"></a>시작하기 전에

이 문서를 완료하는 데 필요한 리소스와 권한은 다음과 같습니다.

* 활성화된 Azure 구독.
    * Azure 구독이 없는 경우 [계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* 구독과 연결된 Azure Active Directory 테넌트.
    * 필요한 경우 [Azure Active Directory 테넌트를 만들거나][create-azure-ad-tenant][Azure 구독을 계정에 연결합니다][associate-azure-ad-tenant].
* SMS 기반 인증을 사용하도록 설정하려면 Azure AD 테넌트에 전역 관리자 권한이 필요합니다.
* 문자 메시지 인증 방법 정책에서 설정된 각 사용자는 이 방법을 사용하지 않더라도 라이선스가 있어야 합니다. 설정된 각 사용자에게는 다음 Azure AD, EMS Microsoft 365 라이선스 중 하나가 있어야 합니다.
    * [Microsoft 365 F1 또는 F3][m365-firstline-workers-licensing]
    * [Azure Active Directory Premium P1 또는 P2][azure-ad-pricing]
    * [EMS(Enterprise Mobility + Security) E3 또는 E5][ems-licensing] 또는 [Microsoft 365 E3 또는 E5][m365-licensing]
    * [Office 365 F3][o365-f3]

## <a name="known-issues"></a>알려진 문제

몇 가지 알려진 문제는 다음과 같습니다.

* SMS 기반 인증은 현재 Azure AD Multi-Factor Authentication과 호환되지 않습니다.
* Teams를 제외한 SMS 기반 인증은 기본 Office 애플리케이션과 호환되지 않습니다.
* B2B 계정에는 SMS 기반 인증을 사용하지 않는 것이 좋습니다.
* 페더레이션 사용자는 홈 테넌트에서 인증되지 않습니다. 클라우드에서만 인증됩니다.
* 사용자의 기본 로그인 방법이 전화번호에 대한 문자 또는 통화인 경우에는 다단계 인증 중에 SMS 코드 또는 음성 통화가 자동으로 전송됩니다. 2021년 6월 현재, 일부 앱은 사용자에게 먼저 **문자** 또는 **통화** 를 선택하도록 요청합니다. 이 옵션은 다른 앱에 대해 너무 많은 보안 코드를 보내지 못하도록 합니다. 기본 로그인 방법이 Microsoft Authenticator 앱([매우 권장](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/it-s-time-to-hang-up-on-phone-transports-for-authentication/ba-p/1751752))인 경우 앱 알림이 자동으로 전송됩니다.

## <a name="enable-the-sms-based-authentication-method"></a>SMS 기반 인증 방법 사용

조직에서 SMS 기반 인증을 설정 및 사용하는 세 가지 주요 단계는 다음과 같습니다.

* 인증 방법 정책을 사용하도록 설정합니다.
* SMS 기반 인증 방법을 사용할 수 있는 사용자 또는 그룹을 선택합니다.
* 각 사용자 계정의 전화 번호를 할당합니다.
    * 이 전화 번호는 Azure Portal(이 문서에 표시됨)에서 그리고 ‘내 직원’ 또는 ‘내 계정’에서 할당할 수 있습니다. 

먼저 Azure AD 테넌트에 대해 SMS 기반 인증을 사용하도록 설정하겠습니다.

1. 전역 관리자로 [Azure Portal][azure-portal]에 로그인합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. Azure Active Directory 창 왼쪽의 탐색 메뉴에서 **보안 > 인증 방법 > 인증 방법 정책** 을 선택합니다.

    [![Azure Portal의 인증 방법 정책 창으로 이동하고 선택합니다.](media/howto-authentication-sms-signin/authentication-method-policy-cropped.png)](media/howto-authentication-sms-signin/authentication-method-policy.png#lightbox)

1. 사용 가능한 인증 방법 목록에서 **문자 메시지** 를 선택합니다.
1. **사용** 을 예로 설정합니다.

    ![인증 방법 정책 창에서 텍스트 인증을 사용하도록 설정](./media/howto-authentication-sms-signin/enable-text-authentication-method.png)

    모든 사용자 또는 일부 사용자 및 그룹에 대해 SMS 기반 인증을 사용하도록 설정할 수 있습니다. 다음 섹션에서는 테스트 사용자에 대해 SMS 기반 인증을 사용하도록 설정합니다.

## <a name="assign-the-authentication-method-to-users-and-groups"></a>사용자 및 그룹에 인증 방법 할당

Azure AD 테넌트에서 SMS 기반 인증을 사용하도록 설정한 상태에서 이 인증 방법을 사용할 수 있는 사용자 또는 그룹을 선택합니다.

1. 문자 메시지 인증 정책 창에서 **대상** 을 사용자 설정으로 설정합니다.
1. **사용자 또는 그룹 추가** 를 선택한 다음 테스트 사용자 또는 그룹(예: *Contoso User* 또는 *Contoso SMS Users*)을 선택합니다.

    [![Azure Portal에서 SMS 기반 인증을 사용할 사용자 또는 그룹을 선택합니다.](media/howto-authentication-sms-signin/add-users-or-groups-cropped.png)](media/howto-authentication-sms-signin/add-users-or-groups.png#lightbox)

1. 사용자 또는 그룹을 선택한 경우 **선택** 을 선택하고 업데이트된 인증 방법 정책을 **저장** 합니다.

문자 메시지 인증 방법 정책에서 설정된 각 사용자는 이 방법을 사용하지 않더라도 라이선스가 있어야 합니다. 인증 방법 정책에서 설정하는 사용자를 위한 적절한 라이선스가 있는지 확인하세요. 대규모 사용자 그룹에 대해 이 기능을 사용하도록 설정하는 경우에는 특히 주의해야 합니다.

## <a name="set-a-phone-number-for-user-accounts"></a>사용자 계정에 대한 전화 번호 설정

이제 사용자가 SMS 기반 인증을 사용할 수 있지만 로그인하려면 사용자의 전화 번호가 Azure AD의 사용자 프로필과 연결되어 있어야 합니다. 사용자가 ‘내 계정’에서 [직접 해당 전화 번호를 설정](../user-help/sms-sign-in-explainer.md)하거나 관리자가 Azure Portal을 사용하여 전화 번호를 할당할 수 있습니다. 전화 번호는 전역 관리자, 인증 관리자 또는 권한 있는 인증 관리자가 설정할 수 있습니다.

SMS 서명에 대해 설정된 전화 번호는 [Azure AD Multi-Factor Authentication][tutorial-azure-mfa] 및 [셀프 서비스 암호 재설정][tutorial-sspr]에도 사용될 수 있습니다.

1. **Azure Active Directory** 를 검색하고 선택합니다.
1. Azure Active Directory 창의 왼쪽 탐색 메뉴에서 **사용자** 를 선택합니다.
1. 이전 섹션에서 SMS 기반 인증을 사용하도록 설정한 사용자(예: *Contoso User*)를 선택한 다음 **인증 방법** 을 선택합니다.
1. **+ 인증 방법 추가** 를 선택하고 ‘방법 선택’ 드롭다운 메뉴에서 **전화 번호** 를 선택합니다.

    사용자의 전화 번호를 국가 번호를 포함하여 입력합니다(예: *+1 xxxxxxxxx*). Azure Portal은 전화 번호가 올바른 형식인지 확인합니다.

    그런 다음 ‘전화 유형’ 드롭다운 메뉴에서 ‘휴대폰’, ‘다른 휴대폰’ 또는 필요에 따라 ‘기타’를 선택합니다.   

    :::image type="content" source="media/howto-authentication-sms-signin/set-user-phone-number.png" alt-text="Azure Portal에서 SMS 기반 인증에 사용할 사용자의 전화 번호 설정":::

    전화 번호는 테넌트에서 고유해야 합니다. 동일한 전화 번호를 여러 사용자에 사용하려고 하면 오류 메시지가 표시됩니다.

1. 사용자 계정에 전화 번호를 적용하려면 **추가** 를 선택합니다.

성공적으로 프로비전되면 SMS 로그인 사용에 대한 확인 표시가 나타납니다.

## <a name="test-sms-based-sign-in"></a>SMS 기반 로그인 테스트

이제 SMS 기반 로그인을 사용하도록 설정된 사용자 계정을 테스트하려면 다음 단계를 수행합니다.

1. 새 InPrivate 또는 Incognito 웹 브라우저 창을 엽니다. [https://www.office.com][office]
1. 오른쪽 위 모서리에서 **로그인** 을 선택합니다.
1. 로그인 프롬프트에서 이전 섹션의 사용자와 연결된 전화 번호를 입력하고 **다음** 을 선택합니다.

    ![테스트 사용자 로그인 프롬프트에 전화 번호 입력](./media/howto-authentication-sms-signin/sign-in-with-phone-number.png)

1. 문자 메시지가 제공된 전화 번호로 발송됩니다. 로그인 프로세스를 완료하려면 로그인 프롬프트에서 문자 메시지에 제공된 6자리 코드를 입력합니다.

    ![문자 메시지를 통해 사용자의 전화 번호로 보낸 확인 코드를 입력](./media/howto-authentication-sms-signin/sign-in-with-phone-number-confirmation-code.png)

1. 이제 사용자가 사용자 이름 또는 암호를 입력할 필요 없이 로그인됩니다.


## <a name="troubleshoot-sms-based-sign-in"></a>SMS 기반 로그인 문제 해결

SMS 기반 로그인을 설정 및 사용하는 데 문제가 있는 경우 다음 시나리오 및 문제 해결 단계를 사용할 수 있습니다. SMS 기반 로그인 사용을 지원하는 앱 목록은 [SMS 기반 인증을 위한 앱 지원](how-to-authentication-sms-supported-apps.md)을 참조하세요.


### <a name="phone-number-already-set-for-a-user-account"></a>사용자 계정에 이미 전화 번호가 설정됨

사용자가 이미 Azure AD Multi-Factor Authentication 및/또는 SSPR(셀프 서비스 암호 재설정)에 등록한 경우 해당 계정과 연결된 전화 번호가 있습니다. 이 전화 번호는 SMS 기반 로그인에 자동으로 사용할 수 없습니다.

계정에 이미 설정된 전화 번호가 있는 사용자는 **내 프로필** 페이지에 SMS 로그인에 사용 단추가 표시됩니다. 해당 단추를 선택하면 계정이 SMS 기반 로그인 및 이전 Azure AD Multi-Factor Authentication 또는 SSPR 등록에 사용할 수 있도록 설정됩니다.

최종 사용자 환경에 대한 자세한 내용은 [SMS 로그인 사용자 환경 - 전화 번호 사용](../user-help/sms-sign-in-explainer.md)을 참조하세요.

### <a name="error-when-trying-to-set-a-phone-number-on-a-users-account"></a>사용자 계정에 전화 번호를 설정하는 동안 오류가 발생

Azure Portal에서 사용자 계정에 전화 번호를 설정하려고 할 때 오류가 표시되면 다음 문제 해결 단계를 검토합니다.

1. SMS 기반 로그인을 사용할 수 있는지 확인합니다.
1. 문자 메시지 인증 방법 정책에서 사용자 계정이 설정되었는지 확인합니다.
1. Azure Portal에서 유효성을 검사하여 적절한 형식으로 전화 번호를 설정해야 합니다(예: *+1 4251234567*).
1. 전화 번호가 테넌트의 다른 곳에서 사용되지 않는지 확인합니다.
1. 계정에 설정된 음성 번호가 없는지 확인합니다. 음성 번호가 설정된 경우 해당 전화 번호를 삭제하고 다시 시도합니다.

## <a name="next-steps"></a>다음 단계

- SMS 기반 로그인 사용을 지원하는 앱 목록은 [SMS 기반 인증을 위한 앱 지원](how-to-authentication-sms-supported-apps.md)을 참조하세요.
- 암호 없이 Azure AD에 로그인하는 다른 방법(예: Microsoft Authenticator App 또는 FIDO2 보안 키)은 [암호 없는 인증 옵션][concepts-passwordless]을 참조하세요.
- Microsoft Graph REST API를 사용하여 SMS 기반 로그인을 [사용][rest-enable]하거나 [사용하지 않을][rest-disable] 수도 있습니다.


<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../fundamentals/active-directory-how-subscriptions-associated-directory.md
[concepts-passwordless]: concept-authentication-passwordless.md
[tutorial-azure-mfa]: tutorial-enable-azure-mfa.md
[tutorial-sspr]: tutorial-enable-sspr.md
[rest-enable]: /graph/api/phoneauthenticationmethod-enablesmssignin?tabs=http
[rest-disable]: /graph/api/phoneauthenticationmethod-disablesmssignin?tabs=http

<!-- EXTERNAL LINKS -->
[azure-portal]: https://portal.azure.com
[office]: https://www.office.com
[m365-firstline-workers-licensing]: https://www.microsoft.com/licensing/news/m365-firstline-workers
[azuread-licensing]: https://azure.microsoft.com/pricing/details/active-directory/
[ems-licensing]: https://www.microsoft.com/microsoft-365/enterprise-mobility-security/compare-plans-and-pricing
[m365-licensing]: https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans
[o365-f1]: https://www.microsoft.com/microsoft-365/business/office-365-f1?market=af
[o365-f3]: https://www.microsoft.com/microsoft-365/business/office-365-f3?activetab=pivot%3aoverviewtab
[azure-ad-pricing]: https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing
