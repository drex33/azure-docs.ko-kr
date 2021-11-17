---
title: '자습서: FortiGate SSL VPN과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: FortiGate SSL VPN을 Azure AD(Azure Active Directory)와 통합하기 위해 수행해야 하는 단계를 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/30/2021
ms.author: jeedes
ms.openlocfilehash: 0799ef2436a672860a7f1863ad76e23d885f5d3b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132292056"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortigate-ssl-vpn"></a>자습서: FortiGate SSL VPN과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 FortiGate SSL VPN을 통합하는 방법에 대해 알아봅니다. FortiGate SSL VPN을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 FortiGate SSL VPN에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 FortiGate SSL VPN에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 FortiGate SSL VPN 구독

## <a name="tutorial-description"></a>자습서 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

FortiGate SSL VPN은 SP 시작 SSO를 지원합니다.


## <a name="add-fortigate-ssl-vpn-from-the-gallery"></a>갤러리에서 FortiGate SSL VPN 추가

FortiGate SSL VPN이 Azure AD에 통합되도록 구성하려면 갤러리의 FortiGate SSL VPN을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **FortiGate SSL VPN** 을 입력합니다.
1. 결과 패널에서 **FortiGate SSL VPN** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-fortigate-ssl-vpn"></a>FortiGate SSL VPN에 대한 Azure AD SSO 구성 및 테스트

B.Simon이라는 테스트 사용자를 사용하여 FortiGate SSL VPN에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 FortiGate SSL VPN의 해당 사용자 간에 연결 관계를 설정해야 합니다.

FortiGate SSL VPN에서 Azure AD SSO를 구성하고 테스트하려면 다음과 같은 개략적인 단계를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자에 대해 기능을 사용하도록 설정합니다.
    1. **[Azure AD 테스트 사용자를 만들어](#create-an-azure-ad-test-user)** Azure AD Single Sign-On을 테스트합니다.
    1. **[테스트 사용자에게 액세스 권한 부여](#grant-access-to-the-test-user)** - 해당 사용자에 대해 Azure AD Single Sign-On을 사용하도록 설정합니다.
1. **[FortiGate SSL VPN SSO 구성](#configure-fortigate-ssl-vpn-sso)** - 애플리케이션 쪽에서 구성합니다.
    1. **FortiGate SSL VPN 테스트 사용자 만들기** - 사용자의 Azure AD 표현에 해당하는 사용자를 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **FortiGate SSL VPN** 애플리케이션 통합 페이지에 있는 **관리** 섹션에서 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성을 편집하기 위한 연필 단추를 보여 주는 스크린샷](common/edit-urls.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 다음 값을 입력합니다.

    a. **로그온 URL** 상자에서 URL을 `https://<FQDN>/remote/saml/login` 패턴으로 입력합니다.

    b. **식별자** 상자에서 URL을 `https://<FQDN>/remote/saml/metadata` 패턴으로 입력합니다.

    다. **회신 URL** 상자에서 URL을 `https://<FQDN>/remote/saml/login` 패턴으로 입력합니다.

    d. **로그아웃 URL** 상자에서 URL을 `https://<FQDN>/remote/saml/logout` 패턴으로 입력합니다.

    > [!NOTE]
    > 이러한 값은 단지 패턴일 뿐입니다. 실제 **로그온 URL**, **식별자**, **회신 URL**, **로그아웃 URL** 을 사용해야 합니다. 지침은 [Fortinet 지원](https://support.fortinet.com)에 문의하세요. Fortinet 설명서 및 Azure Portal의 **기본 SAML 구성** 섹션에 표시된 예제 패턴을 참조할 수도 있습니다.

1. FortiGate SSL VPN 애플리케이션은 특정 서식에서 SAML 어설션을 예상하며 이는 구성에 사용자 지정 특성 매핑을 추가합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![기본 특성을 보여 주는 스크린샷](common/default-attributes.png)

1. 다음 표에서는 FortiGate SSL VPN에서 요구하는 두 가지 추가 클레임을 보여 줍니다. 이러한 클레임의 이름은 이 자습서의 **FortiGate 명령줄 구성 수행** 섹션에서 사용하는 이름과 일치해야 합니다. 

   | Name |  원본 특성|
   | ------------ | --------- |
   | 사용자 이름 | user.userprincipalname |
   | group | user.groups |
   
   이러한 추가 클레임을 만들려면 다음을 수행합니다.

   a. **사용자 특성 및 클레임** 옆에 있는 **편집** 을 선택합니다.

   b. **새 클레임 추가** 를 선택합니다.

   c. **이름** 에 대해 **사용자 이름** 을 입력합니다.

   d. **원본 특성** 에 대해 **user.userprincipalname** 을 선택합니다.

   e. **저장** 을 선택합니다.

   f. **그룹 클레임 추가** 를 선택합니다.

   g. **모든 그룹** 을 선택합니다.

   h. **그룹 클레임 이름 사용자 지정** 확인란을 선택합니다.

   i. **이름** 에 대해 **그룹** 을 입력합니다.
   
   j. **저장** 을 선택합니다.   

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 옆에 있는 **다운로드** 링크를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크를 보여 주는 스크린샷](common/certificatebase64.png)

1. **FortiGate SSL VPN 설정** 섹션에서 요구 사항에 따라 적절한 하나 이상의 URL을 복사합니다.

    ![구성 URL을 보여 주는 스크린샷](common/copy-configuration-urls.png)

#### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 완료합니다.
   1. **이름** 상자에서 **B.Simon** 을 입력합니다.  
   1. **사용자 이름** 상자에 \<username>@\<companydomain>.\<extension>을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 를 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 선택합니다.

#### <a name="grant-access-to-the-test-user"></a>테스트 사용자에게 액세스 권한 부여

이 섹션에서는 B.Simon에게 FortiGate SSL VPN에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션**, **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **FortiGate SSL VPN** 을 선택합니다.
1. 앱의 개요 페이지에 있는 **관리** 섹션에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택하고, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에게 적합한 역할을 선택합니다. 화면의 아래쪽에서 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

#### <a name="create-a-security-group-for-the-test-user"></a>테스트 사용자에 대한 보안 그룹 만들기

이 섹션에서는 테스트 사용자에 대한 보안 그룹을 Azure Active Directory에 만듭니다. FortiGate는 이 보안 그룹을 사용하여 VPN을 통한 사용자 네트워크 액세스 권한을 부여합니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** 를 선택합니다. 그런 다음, **그룹** 을 선택합니다.
1. 화면 위쪽에서 **새 그룹** 을 선택합니다.
1. **새 그룹** 속성에서 다음 단계를 완료합니다.
   1. **그룹 형식** 목록에서 **보안** 을 선택합니다.
   1. **그룹 이름** 상자에서 **FortiGateAccess** 를 입력합니다.
   1. **그룹 설명** 상자에서 **FortiGate VPN 액세스 권한을 부여하기 위한 그룹** 을 입력합니다.
   1. **Azure AD 역할을 그룹에 할당할 수 있음(미리 보기)** 설정에 대해 **아니요** 를 선택합니다.
   1. **멤버 자격 유형** 상자에서 **할당됨** 을 선택합니다.
   1. **멤버** 아래에서 **선택한 멤버가 없음** 을 선택 합니다.
   1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
   1. **만들기** 를 선택합니다.
1. Azure Active Directory의 **그룹** 섹션으로 돌아가서 **FortiGateAccess** 그룹을 찾고, **개체 ID** 를 적어 둡니다. 나중에 필요합니다.

### <a name="configure-fortigate-ssl-vpn-sso"></a>FortiGate SSL VPN SSO 구성

#### <a name="upload-the-base64-saml-certificate-to-the-fortigate-appliance"></a>Base64 SAML 인증서를 FortiGate 어플라이언스에 업로드

테넌트에서 FortiGate 앱의 SAML 구성을 완료한 후에 Base64로 인코딩된 SAML 인증서를 다운로드했습니다. 이 인증서를 FortiGate 어플라이언스에 업로드해야 합니다.

1. FortiGate 어플라이언스의 관리 포털에 로그인합니다.
1. 왼쪽 창에서 **시스템** 을 선택합니다.
1. **시스템** 아래에서 **인증서** 를 선택합니다.
1. **가져오기** > **원격 인증서** 를 차례로 선택합니다.
1. Azure 테넌트의 FortiGate 앱 배포에서 다운로드한 인증서를 찾아서 선택한 다음, **확인** 을 선택합니다.

인증서가 업로드되면 **시스템** > **인증서** > **원격 인증서** 아래에 있는 이름을 적어 둡니다. 기본적으로 이름은 REMOTE_Cert_ *N* 입니다. 여기서 *N* 은 정수 값입니다.

#### <a name="complete-fortigate-command-line-configuration"></a>FortiGate 명령줄 구성 완료

다음 단계를 수행하려면 Azure 로그아웃 URL을 구성해야 합니다. 이 URL에는 물음표(?) 문자가 포함됩니다. 이 문자를 성공적으로 제출하려면 특정 단계를 수행해야 합니다. FortiGate CLI 콘솔에서는 이러한 단계를 완료할 수 없습니다. 대신 PuTTY와 같은 도구를 사용하여 FortiGate 어플라이언스에 대한 SSH 세션을 설정합니다. FortiGate 어플라이언스가 Azure 가상 머신인 경우 Azure 가상 머신에 대한 직렬 콘솔에서 다음 단계를 완료할 수 있습니다.

이러한 단계를 완료하려면 이전에 기록한 값이 필요합니다.

- 엔터티 ID
- 회신 URL
- 로그아웃 URL
- Azure 로그인 URL
- Azure AD 식별자
- Azure 로그아웃 URL
- Base64 SAML 인증서 이름(REMOTE_Cert_ *N*)

1. FortiGate 어플라이언스에 대한 SSH 세션을 설정하고, FortiGate 관리자 계정으로 로그인합니다.
1. 다음 명령을 실행하세요.

   ```console
    config user saml
    edit azure
    set cert <FortiGate VPN Server Certificate Name>
    set entity-id <Entity ID>
    set single-sign-on-url <Reply URL>
    set single-logout-url <Logout URL>
    set idp-single-sign-on-url <Azure Login URL>
    set idp-entity-id <Azure AD Identifier>
    set idp-single-logout-url <Azure Logout URL>
    set idp-cert <Base64 SAML Certificate Name>
    set user-name username
    set group-name group
    end

   ```

   > [!NOTE]
   > Azure 로그아웃 URL에는 `?` 문자가 포함되어 있습니다. 이 URL을 FortiGate 직렬 콘솔에 올바르게 제공하려면 특수 키 시퀀스를 입력해야 합니다. URL은 일반적으로 `https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`입니다.
   >
   > Azure 로그아웃 URL을 직렬 콘솔에 입력하려면 `set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation`을 입력합니다.
   > 
   > 그런 다음, CTRL+V를 선택하고, URL의 나머지 부분을 붙여넣어 줄을 완성합니다(`set idp-single-logout-url https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0`).

#### <a name="configure-fortigate-for-group-matching"></a>그룹 일치를 위한 FortiGate 구성

이 섹션에서는 테스트 사용자가 포함된 보안 그룹의 개체 ID를 인식하도록 FortiGate를 구성합니다. 이 구성을 사용하면 FortiGate에서 그룹 멤버 자격을 기반으로 하여 액세스 결정을 내릴 수 있습니다.

다음 단계를 완료하려면 이 자습서의 앞부분에서 만든 FortiGateAccess 보안 그룹의 개체 ID가 필요합니다.

1. FortiGate 어플라이언스에 대한 SSH 세션을 설정하고, FortiGate 관리자 계정으로 로그인합니다.
1. 다음 명령을 실행하세요.

   ```
    config user group
    edit FortiGateAccess
    set member azure
    config match
    edit 1
    set server-name azure
    set group-name <Object Id>
    next
    end
    next
    end
   ```

#### <a name="create-a-fortigate-vpn-portals-and-firewall-policy"></a>FortiGate VPN 포털 및 방화벽 정책 만들기

이 섹션에서는 이 자습서의 앞부분에서 만든 FortiGateAccess 보안 그룹에 대한 액세스 권한을 부여하는 FortiGate VPN 포털 및 방화벽 정책을 구성합니다.

[FortiGate 지원 팀](mailto:tac_amer@fortinet.com)과 협력하여 VPN 포털 및 방화벽 정책을 FortiGate VPN 플랫폼에 추가합니다. Single Sign-On을 사용하기 전에 이 단계를 완료해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 FortiGate VPN 로그온 URL로 리디렉션됩니다. 

* FortiGate VPN 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 FortiGate VPN 타일을 클릭하면 FortiGate VPN 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

FortiGate VPN이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
