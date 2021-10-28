---
title: SAML 기반 Single Sign-On 디버그
titleSuffix: Azure AD
description: Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그합니다.
services: active-directory
ms.author: davidmu
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: troubleshooting
ms.workload: identity
ms.date: 02/18/2019
ms.reviewer: ergreenl
ms.openlocfilehash: 52bb2f004aaf37803ca2e0b8e46630274daffb9a
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620681"
---
# <a name="debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Azure Active Directory에서 SAML 기반 Single Sign-On을 애플리케이션에 디버그

SAML 기반 Single Sign-On을 사용하는 Azure AD(Azure Active Directory)에서 애플리케이션의 [Single Sign-On](what-is-single-sign-on.md) 문제를 찾고 수정하는 방법을 알아봅니다.

## <a name="before-you-begin"></a>시작하기 전에

[내 앱 보안 로그인 확장](https://support.microsoft.com/account-billing/troubleshoot-problems-with-the-my-apps-portal-d228da80-fcb7-479c-b960-a1e2535cbdff#im-having-trouble-installing-the-my-apps-secure-sign-in-extension)을 설치하는 것이 좋습니다. 이 브라우저 확장을 사용하면 Single Sign-On과 관련된 문제를 해결하는 데 필요한 SAML 요청 및 SAML 응답 정보를 쉽게 수집할 수 있습니다. 확장을 설치할 수 없는 경우 이 문서에서는 확장이 설치되거나 설치되지 않았을 때의 문제를 해결하는 방법을 보여줍니다.

내 앱 보안 로그인 확장을 다운로드하고 설치하려면 다음 링크 중 하나를 사용합니다.

- [Chrome](https://go.microsoft.com/fwlink/?linkid=866367)
- [Microsoft Edge](https://microsoftedge.microsoft.com/addons/detail/my-apps-secure-signin-ex/gaaceiggkkiffbfdpmfapegoiohkiipl)

## <a name="test-saml-based-single-sign-on"></a>SAML 기반 Single Sign-On 테스트

Azure AD와 대상 애플리케이션 간에 SAML 기반 Single Sign-On을 테스트하려면:

1. 전역 관리자 또는 애플리케이션을 관리할 권한이 있는 다른 관리자로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 왼쪽 블레이드에서 **Azure Active Directory** 를 선택한 후 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. Enterprise 애플리케이션 목록에서 Single Sign-On을 테스트하려는 애플리케이션을 선택한 다음, 왼쪽에 있는 옵션에서 **Single Sign-On** 을 선택합니다.
1. SAML 기반 Single Sign-On 테스트 환경을 열려면 **Single Sign-On 테스트**(5단계)로 이동합니다. **테스트** 단추가 회색으로 표시되면 먼저 **기본 SAML 구성** 섹션에서 필요한 특성을 작성하고 저장해야 합니다.
1. **Single Sign-On 테스트** 블레이드에서 대상 애플리케이션에 로그인하려면 회사 자격 증명을 사용합니다. 현재 사용자 또는 다른 사용자로 로그인할 수 있습니다. 다른 사용자로 로그인하는 경우 인증을 요구하는 메시지가 나타납니다.

    ![SAML SSO 테스트 페이지를 보여 주는 스크린샷](./media/debug-saml-sso-issues/test-single-sign-on.png)

성공적으로 로그인하면 테스트가 통과된 것입니다. 이 경우 Azure AD는 애플리케이션에 SAML 응답 토큰을 발급합니다. 애플리케이션은 SAML 토큰을 사용해 성공적으로 로그인했습니다.

회사 로그인 페이지 또는 애플리케이션 페이지에 오류가 있는 경우 해당 오류를 해결하려면 다음 섹션 중 하나를 사용합니다.

## <a name="resolve-a-sign-in-error-on-your-company-sign-in-page"></a>회사 로그인 페이지에서 로그인 오류 해결

로그인을 시도하면 회사 로그인 페이지에 다음 예제와 비슷한 오류가 표시될 수 있습니다.

![회사 로그인 페이지의 오류를 보여 주는 예제](./media/debug-saml-sso-issues/error.png)

이 오류를 디버그하려면 오류 메시지 및 SAML 요청이 필요합니다. 내 앱 보안 로그인 확장은 자동으로 이 정보를 수집하고 Azure AD에 해결 지침을 표시합니다.

### <a name="to-resolve-the-sign-in-error-with-the-my-apps-secure-sign-in-extension-installed"></a>내 앱 보안 로그인 확장을 설치하여 로그인 오류를 해결하려면

1. 오류가 발생하면 확장은 다시 Azure AD **Single Sign-On 테스트** 블레이드로 리디렉션됩니다.
1. **Single Sign-On 테스트** 블레이드에서 **SAML 요청 다운로드** 를 선택합니다.
1. SAML 요청의 오류 및 값에 따른 특정 해결 지침이 표시되어야 합니다.
1. 문제를 해결하기 위해 Azure AD에서 구성을 자동으로 업데이트하는 **문제 해결** 단추가 표시됩니다. 이 단추가 표시되지 않으면 Azure AD의 잘못된 구성으로 인해 로그인 문제가 발생한 것이 아닙니다.

로그인 오류의 해결 방법이 제공되지 않으면 피드백 텍스트 상자를 사용하여 Microsoft에 알려 주시기 바랍니다.

### <a name="to-resolve-the-error-without-installing-the-my-apps-secure-sign-in-extension"></a>내 앱 보안 로그인 확장을 설치하지 않고 로그인 오류를 해결하려면

1. 페이지의 오른쪽 아래 모서리에서 오류 메시지를 복사합니다. 오류 메시지에는 다음이 포함됩니다.
    - CorrelationID 및 TimeStamp. 이러한 값은 엔지니어가 문제를 식별하고 문제에 대한 정확한 해결책을 제공하는 데 유용하므로 Microsoft를 통해 지원 사례를 만들 때 중요합니다.
    - 문제의 근본 원인을 확인하는 명령문입니다.
1. Azure AD로 다시 돌아가 **Single Sign-On 테스트** 블레이드를 찾습니다.
1. 위의 **해결 지침 가져오기** 텍스트 상자에서 오류 메시지를 붙여넣습니다.
1. **해결 지침 가져오기** 를 클릭하여 문제 해결을 위한 단계를 표시합니다. 이 지침은 SAML 요청 또는 SAML 응답에서 정보를 요구할 수 있습니다. 내 앱 보안 로그인 확장을 사용하지 않는 경우 SAML 요청 및 응답을 검색하려면 [Fiddler](https://www.telerik.com/fiddler)와 같은 도구가 필요할 수 있습니다.
1. SAML 요청의 대상이 Azure AD에서 가져온 SAML Single Sign-On 서비스 URL에 해당하는지 확인합니다.
1. SAML 요청의 발급자가 Azure AD의 애플리케이션에 대해 구성한 것과 동일한 식별자인지 확인입니다. Azure AD는 사용자 디렉터리에서 애플리케이션을 찾는 데 발급자를 사용합니다.
1. 애플리케이션이 Azure AD에서 SAML 토큰을 수신해야 하는 위치에 AssertionConsumerServiceURL이 있는지 확인합니다. Azure AD에서 이 값을 구성할 수 있지만 SAML 요청에 포함된 경우에는 필수가 아닙니다.

## <a name="resolve-a-sign-in-error-on-the-application-page"></a>애플리케이션 페이지에서 로그인 오류 해결

성공적으로 로그인한 다음, 애플리케이션 페이지에 오류가 표시될 수 있습니다. Azure AD가 애플리케이션에 토큰을 발급했지만 애플리케이션이 응답을 적용하지 않는 경우에 발생합니다.

오류를 해결하려면 다음 단계를 수행하거나 [Azure AD를 사용하여 SAML SSO 문제를 해결하는 방법에 관한 다음 짧은 비디오](https://www.youtube.com/watch?v=poQCJK0WPUk&list=PLLasX02E8BPBm1xNMRdvP6GtA6otQUqp0&index=8)를 시청하세요.

1. 애플리케이션이 Azure AD 갤러리에 있는 경우 애플리케이션을 Azure AD와 통합하기 위한 모든 단계를 수행했는지 확인합니다. 애플리케이션에 대한 통합 지침을 찾으려면 [SaaS 애플리케이션 통합 자습서 목록](../saas-apps/tutorial-list.md)을 참조합니다.
1. SAML 응답을 검색합니다.
    - 내 앱 보안 로그인 확장이 설치된 경우 **Single Sign-On 테스트** 블레이드에서 **SAML 응답 다운로드** 를 클릭합니다.
    - 확장이 설치되지 않은 경우 [Fiddler](https://www.telerik.com/fiddler) 같은 도구를 사용하여 SAML 응답을 검색합니다.
1. SAML 응답 토큰에서 이러한 요소를 확인합니다.
   - NameID 값과 형식의 사용자 고유 식별자
   - 토큰에서 발급된 클레임
   - 토큰에 서명하는 데 사용된 인증서입니다.

     SAML 응답에 대한 자세한 내용은 [Single Sign-On SAML 프로토콜](../develop/single-sign-on-saml-protocol.md?toc=/azure/active-directory/azuread-dev/toc.json&bc=/azure/active-directory/azuread-dev/breadcrumb/toc.json)을 참조합니다.

1. SAML 응답을 검토했으므로 이제 문제를 해결하는 방법에 관한 지침은 [로그인한 후 애플리케이션 페이지의 오류](application-sign-in-problem-application-error.md)를 참조하세요.
1. 아직 성공적으로 로그인할 수 없는 경우 SAML 응답에서 누락된 항목을 애플리케이션 공급업체에 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Single Sign-On이 애플리케이션에 대해 작동하므로 이제 [SaaS 애플리케이션에 대한 사용자 프로비저닝 및 프로비저닝 해제를 자동화](../app-provisioning/user-provisioning.md)하거나 [조건부 액세스를 시작](../conditional-access/app-based-conditional-access.md)할 수 있습니다.