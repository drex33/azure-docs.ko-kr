---
title: Azure Active Directory 애플리케이션 프록시를 사용한 온-프레미스 앱에 대한 SAML Single Sign-On
description: SAML 인증으로 보호되는 온-프레미스 애플리케이션에 대해 Single Sign-On을 제공하는 방법을 알아봅니다. 애플리케이션 프록시로 온-프레미스 앱에 대한 원격 액세스를 제공합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 6c71f912da153ae41b261e639cf99e6554927cdf
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068173"
---
# <a name="saml-single-sign-on-for-on-premises-applications-with-application-proxy"></a>애플리케이션 프록시를 사용하는 온-프레미스 애플리케이션에 대한 SAML Single Sign-On

SAML 인증으로 보호되는 온-프레미스 애플리케이션에 SSO(Single Sign-On)를 제공하고 애플리케이션 프록시를 통해 이러한 애플리케이션에 원격 액세스를 제공합니다. SAML Single Sign-On을 사용하는 Azure Active Directory(Azure AD)는 사용자의 Azure AD 계정을 사용하여 애플리케이션에 인증합니다. Azure AD는 연결 프로토콜을 통해 애플리케이션에 로그온 정보를 통신합니다. SAML 클레임에서 정의하는 규칙에 따라 사용자를 특정 애플리케이션 역할에 매핑할 수도 있습니다. SAML SSO 외에도 애플리케이션 프록시를 사용하도록 설정하면 사용자는 애플리케이션에 대한 외부 액세스 권한과 원활한 SSO 환경을 갖게 됩니다.

애플리케이션은 **Azure Active Directory** 에서 발급한 SAML 토큰을 사용할 수 있어야 합니다. 이 구성은 온-프레미스 ID 공급자를 사용하는 애플리케이션에는 적용되지 않습니다. 이러한 시나리오의 경우 [애플리케이션을 Azure AD로 마이그레이션하기 위한 리소스](../manage-apps/migration-resources.md)를 검토하는 것이 좋습니다.

애플리케이션 프록시를 사용하는 SAML SSO는 SAML 토큰 암호화 기능에서도 작동합니다. 자세한 내용은 [Azure AD SAML 토큰 암호화 구성](../manage-apps/howto-saml-token-encryption.md)을 참조하세요.

아래 프로토콜 다이어그램에서는 서비스 공급자 시작(SP 시작) 흐름과 ID 공급자 시작(IdP 시작) 흐름 모두에 대한 Single Sign-On 시퀀스를 설명합니다. 온-프레미스 애플리케이션에서 SAML 요청 및 응답을 캐싱하면 SAML SSO에서 애플리케이션 프록시가 작동합니다.

  ![다이어그램은 애플리케이션, 애플리케이션 프록시, 클라이언트 및 SP 시작 Single Sign-On에 대한 Azure AD의 상호 작용을 보여 줍니다.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-sp-initiated-flow.png)

  ![다이어그램은 애플리케이션, 애플리케이션 프록시, 클라이언트 및 IdP 시작 Single Sign-On에 대한 Azure AD의 상호 작용을 보여 줍니다.](./media/application-proxy-configure-single-sign-on-on-premises-apps/saml-idp-initiated-flow.png)

## <a name="create-an-application-and-set-up-saml-sso"></a>애플리케이션 만들기 및 SAML SSO 설정

1. Azure Portal에서 **Azure Active Directory > 엔터프라이즈 애플리케이션** 을 선택하고 **새 애플리케이션** 을 선택합니다.

2. 새 애플리케이션에 대한 표시 이름을 입력하고 **갤러리에 없는 다른 애플리케이션 통합** 을 선택한 다음 **만들기** 를 선택합니다.

3. 앱의 **개요** 페이지에서 **Single Sign-On** 을 선택합니다.

4. Single Sign-On 방법으로 **SAML** 을 선택합니다.

5. 회사 네트워크에서 SAML SSO를 설정하고, [갤러리에 없는 다른 애플리케이션 통합](../manage-apps/configure-saml-single-sign-on.md)의 기본 SAML 구성 섹션을 참조하여 애플리케이션에 대한 SAML 기반 인증을 구성합니다.

6. 애플리케이션에 하나 이상의 사용자를 추가하고 테스트 계정이 애플리케이션에 액세스할 수 있는지 확인합니다. 회사 네트워크에 연결되어 있는 동안 테스트 계정을 사용하여 애플리케이션에 대한 Single Sign-On이 있는지 확인합니다. 

   > [!NOTE]
   > 애플리케이션 프록시를 설정한 후에는 다시 돌아와서 SAML **회신 URL** 을 업데이트합니다.

## <a name="publish-the-on-premises-application-with-application-proxy"></a>애플리케이션 프록시를 사용하여 온-프레미스 애플리케이션 게시

온-프레미스 애플리케이션에 대한 SSO를 제공하기 전에 애플리케이션 프록시를 사용하도록 설정하고 커넥터를 설치해야 합니다. 온-프레미스 환경을 준비하고 커넥터를 설치 및 등록하고 커넥터를 테스트하는 방법에 대한 자세한 내용은 [Azure AD에서 애플리케이션 프록시를 통해 원격 액세스를 위한 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md) 자습서를 참조하세요. 다음 단계를 따라 애플리케이션 프록시로 새 애플리케이션을 게시합니다. 아래에서 언급되지 않는 다른 설정은 자습서의 [Azure AD에 온-프레미스 앱 추가](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad) 섹션을 참조하세요.

1. Azure Portal에서 애플리케이션이 열린 상태에서 **애플리케이션 프록시** 를 선택합니다. 애플리케이션에 대한 **내부 URL** 을 제공합니다. 사용자 지정 도메인을 사용하는 경우 애플리케이션에 대한 TLS/SSL 인증서도 업로드해야 합니다. 
   > [!NOTE]
   > 최적화된 사용자 환경을 위해 가능하면 사용자 지정 도메인을 사용하는 것이 좋습니다. [Azure AD 애플리케이션 프록시에서 사용자 지정 도메인 작업](application-proxy-configure-custom-domain.md)에 대해 자세히 알아보세요.

2. 애플리케이션에 대한 **사전 인증** 방법으로 **Azure Active Directory** 를 선택합니다.

3. 애플리케이션에 대한 **외부 URL** 을 복사합니다. SAML 구성을 완료하려면 이 URL이 필요합니다.

4. 테스트 계정을 사용하여 애플리케이션 프록시가 올바르게 설정되었는지 확인하려면 **외부 URL** 을 사용하여 애플리케이션을 엽니다. 문제가 있는 경우 [애플리케이션 프록시 문제 및 오류 메시지 문제 해결](application-proxy-troubleshoot.md)을 참조하세요.

## <a name="update-the-saml-configuration"></a>SAML 구성 업데이트

1. Azure Portal에서 애플리케이션이 열려 있는 상태에서 **Single Sign-On** 을 선택합니다. 

2. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 제목으로 이동하고 **편집** 아이콘(연필 모양)을 선택합니다. 애플리케이션 프록시에서 구성한 **외부 URL** 이 **식별자**, **회신 URL** 및 **로그아웃 URL** 필드에 채워졌는지 확인합니다. 이러한 URL은 애플리케이션 프록시가 올바르게 작동하려면 필요합니다. 

3. 도메인이 애플리케이션 프록시를 통해 인터넷에서 연결할 수 있도록 앞서 구성한 **회신 URL** 을 편집합니다. 예를 들어 **외부 URL** 이 `https://contosotravel-f128.msappproxy.net`이고 원래 **회신 URL** 이 `https://contosotravel.com/acs`였던 경우 원래 **회신 URL** 을 `https://contosotravel-f128.msappproxy.net/acs`로 업데이트해야 합니다.

    ![기본 SAML 구성 데이터 입력](./media/application-proxy-configure-single-sign-on-on-premises-apps/basic-saml-configuration.png)


4. 업데이트된 **회신 URL** 옆에 있는 확인란을 선택하여 기본값으로 표시합니다.

   * 필수 **회신 URL** 을 기본값으로 표시한 이후 내부 URL을 사용하는 이전에 구성한 **회신 URL** 을 삭제할 수도 있습니다.

   * SP 시작 흐름의 경우 인증 토큰 수신을 위해 백 엔드 애플리케이션에서 올바른 **회신 URL** 또는 Assertion Consumer Service URL을 지정하도록 해야 합니다.

    > [!NOTE]
    > 백 엔드 애플리케이션에서 **회신 URL** 이 내부 URL이어야 하는 경우 [사용자 지정 도메인](application-proxy-configure-custom-domain.md)을 사용하여 일치하는 내부 및 외부 URL을 보유하거나 사용자의 디바이스에 내 앱 보안 로그인 확장을 설치해야 합니다. 이 확장은 적절한 애플리케이션 프록시 서비스로 자동으로 리디렉션됩니다. 확장을 설치하려면 [내 앱 보안 로그인 확장](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510#download-and-install-the-my-apps-secure-sign-in-extension)을 참조하세요.
    
## <a name="test-your-app"></a>앱 테스트

이러한 모든 단계를 완료하면 앱이 준비되고 실행되어야 합니다. 앱을 테스트하는 방법은 다음과 같습니다.

1. 브라우저를 열고 앱을 게시할 때 만든 **외부 URL** 로 이동합니다. 
1. 앱에 할당한 테스트 계정으로 로그인합니다. 애플리케이션을 로드할 수 있고 애플리케이션에 SSO가 있어야 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시에서 Single Sign-On을 제공하는 방법](../manage-apps/what-is-single-sign-on.md)
- [애플리케이션 프록시 문제 해결](application-proxy-troubleshoot.md)
