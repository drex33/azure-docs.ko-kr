---
title: '자습서: Check Point Remote Secure Access VPN과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Check Point Remote Secure Access VPN 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2021
ms.author: jeedes
ms.openlocfilehash: 33bcb007c1cfd4d09c25fb4abbf9c5fcfb15b58f
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131988753"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-check-point-remote-secure-access-vpn"></a>자습서: Check Point Remote Secure Access VPN과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Check Point Remote Secure Access VPN을 통합하는 방법에 관해 알아봅니다. Azure AD와 Check Point Remote Secure Access VPN을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Check Point Remote Secure Access VPN에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Check Point Remote Secure Access VPN에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Check Point Remote Secure Access VPN SSO(Single Sign-On)가 사용 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Check Point Remote Secure Access VPN은 **SP** 시작 SSO를 지원합니다.

## <a name="adding-check-point-remote-secure-access-vpn-from-the-gallery"></a>갤러리에서 Check Point Remote Secure Access VPN 추가

Azure AD에 Check Point Remote Secure Access VPN을 통합하도록 구성하려면 갤러리의 Check Point Remote Secure Access VPN을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Check Point Remote Secure Access VPN** 을 입력합니다.
1. 결과 패널에서 **Check Point Remote Secure Access VPN** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-check-point-remote-secure-access-vpn"></a>Check Point Remote Secure Access VPN에서 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Check Point Remote Secure Access VPN에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Check Point Remote Secure Access VPN의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Check Point Remote Secure Access VPN에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Check Point Remote Secure Access VPN SSO 구성](#configure-check-point-remote-secure-access-vpn-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.

    1. **[Check Point Remote Secure Access VPN 테스트 사용자 만들기](#create-check-point-remote-secure-access-vpn-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Check Point Remote Secure Access VPN에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Check Point Remote Secure Access VPN** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/ID/<IDENTIFIER_UID>` 패턴을 사용하는 URL을 입력합니다.

    1. **회신 URL** 텍스트 상자에서 `https://<GATEWAY_IP>/saml-vpn/spPortal/ACS/Login/<IDENTIFIER_UID>` 패턴을 사용하여 URL을 입력합니다.

    1. **로그온 URL** 텍스트 상자에서 `https://<GATEWAY_IP>/saml-vpn/` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 해당 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값을 얻으려면 [Check Point Remote Secure Access VPN 클라이언트 지원 팀](mailto:support@checkpoint.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Check Point Remote Secure Access VPN 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Check Point Remote Secure Access VPN에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Check Point Remote Secure Access VPN** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-check-point-remote-secure-access-vpn-sso"></a>Check Point Remote Secure Access VPN SSO 구성

### <a name="configure-an-external-user-profile-object"></a>외부 사용자 프로필 개체 구성

> [!NOTE]
> 이 섹션은 온-프레미스 Active Directory(LDAP)를 사용하지 않으려는 경우에만 필요합니다.

**레거시 SmartDashboard에서 일반 사용자 프로필을 구성합니다**.

1. SmartConsole에서 **관리 및 설정 > 블레이드** 로 이동합니다.

1. **모바일 액세스** 섹션에서 **SmartDashboard 에서 구성** 을 클릭합니다. 레거시 SmartDashboard가 열립니다.

1. 네트워크 **개체** 창에서 **사용자** 를 클릭합니다.

1. 빈 공간을 마우스 오른쪽 단추로 클릭하고 **새로 만들기 > 외부 사용자 프로필 > 모든 사용자 일치** 를 선택합니다.

1. **외부 사용자 프로필** 속성을 구성합니다.

    1. **일반 속성** 페이지에서:
        * **외부 사용자 프로필** 이름 필드에서 기본 이름인 `generic`을 그대로 둡니다.*
        * **만료 날짜** 필드에서 적절한 날짜를 설정합니다.

    1. **인증** 페이지에서 다음을 수행합니다.
        * **인증 체계** 드롭다운 목록에서 `undefined`를 선택합니다.
    1. **위치**, **시간** 및 **암호화** 페이지:
        * 기타 적용 가능한 설정 구성
    1. **확인** 을 클릭합니다.

1. 위쪽 도구 모음에서 **업데이트** 를 클릭하거나 Ctrl+S를 누릅니다.

1. SmartDashboard를 닫습니다.

1. SmartConsole에서 액세스 제어 정책을 설치합니다.

### <a name="configure-remote-access-vpn"></a>Remote Access VPN 구성

1. 해당하는 보안 게이트웨이의 개체를 엽니다.

1. 일반 속성 페이지에서 **IPSec VPN** 소프트웨어 블레이드를 사용하도록 설정합니다.

1. 왼쪽 트리에서 **IPSec VPN** 페이지를 클릭합니다.

1. **This Security Gateway participates in the following VPN communities**(이 보안 게이트웨이가 참가하는 VPN 커뮤니티:) 섹션에서 **추가** 를 클릭하고 **Remote Access Community**(원격 액세스 커뮤니티)를 선택합니다.

1. 왼쪽 트리에서 **VPN 클라이언트 > 원격 액세스** 를 클릭합니다.

1. **Support Visitor Mode**(방문자 모드 지원)를 사용하도록 설정합니다.

1. 왼쪽 트리에서 **VPN 클라이언트 > Office Mode**(Office 모드)를 클릭합니다.

1. **Allow Office Mode**(Office 모드 허용)를 선택하고 해당하는 Office 모드 방법을 선택합니다.

1. 왼쪽 트리에서 **VPN 클라이언트 > SAML Portal Settings**(SAML 포털 설정)를 클릭합니다.

1. 기본 URL에 게이트웨이의 정규화된 도메인 이름이 포함되는지 확인합니다.
이 도메인 이름은 조직에서 등록한 DNS 접미사로 끝나야 합니다.
예: `https://gateway1.company.com/saml-vpn`

1. 최종 사용자의 브라우저에서 인증서를 신뢰할 수 있는지 확인합니다.

1. **확인** 을 클릭합니다.

### <a name="configure-an-identity-provider-object"></a>ID 공급자 개체 구성

1. Remote Access VPN에 참가하는 각 보안 게이트웨이에 대해 다음 단계를 수행합니다.

1. SmartConsole > **Gateways & Servers**(게이트웨이 및 서버) 보기에서 **New > More > User/Identity > Identity Provider**(새로 만들기 > 기타 > 사용자/ID > ID 공급자)를 클릭합니다.

    ![New Identity Provider의 스크린샷](./media/check-point-remote-access-vpn-tutorial/identity-provider.png)

1. **새 ID 공급자** 페이지에서 다음 단계를 수행합니다.

    ![Identity Provider 섹션의 스크린샷](./media/check-point-remote-access-vpn-tutorial/new-identity-provider.png)

    a. **Gateway**(게이트웨이) 필드에서 SAML 인증을 수행해야 하는 보안 게이트웨이를 선택합니다.

    b. **서비스** 필드의 드롭다운에서 **Remote Access VPN** 을 선택합니다.

    다. **Identifier(Entity ID)** (식별자(엔터티 ID)) 값을 복사하고, 이 값을 Azure Portal의 **Basic SAML Configuration**(기본 SAML 구성) 섹션에 있는 **Identifier**(식별자) 텍스트 상자에 붙여넣습니다.

    d. **Reply URL**(회신 URL) 값을 복사하고, 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    e. **메타데이터 파일 가져오기** 를 선택하고 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 을 업로드합니다.

    > [!NOTE]
    > 또는 **Insert Manually**(수동으로 삽입)를 선택하여 수동으로 **Entity ID**(엔터티 ID) 및 **Login URL**(로그인 URL) 값을 해당 필드에 붙여넣고 Azure Portal에서 **인증서 파일** 을 업로드할 수도 있습니다.

    f. **확인** 을 클릭합니다.

### <a name="configure-the-identity-provider-as-an-authentication-method"></a>ID 공급자를 인증 방법으로 구성

1. 해당하는 보안 게이트웨이의 개체를 엽니다.

1. **VPN 클라이언트 > 인증** 페이지에서:

    a. **Allow older clients to connect to this gateway**(이전 클라이언트가 이 게이트웨이에 연결하도록 허용) 확인란의 선택을 취소합니다.

    b. 새 개체를 추가하거나 기존 보안영역을 편집합니다.

    ![새 개체 추가의 스크린샷](./media/check-point-remote-access-vpn-tutorial/add-new-object.png)

1. 이름 및 표시 이름을 입력하고 인증 방법을 추가/편집합니다. MEP에 참가하는 GW에서 로그인 옵션을 사용하는 경우 원활한 사용자 환경을 위해 이름은 “SAMLVPN_” 접두사로 시작해야 합니다.

    ![로그인 옵션에 관한 스크린샷](./media/check-point-remote-access-vpn-tutorial/login-option.png)

1. **ID 공급자** 옵션을 선택하고, 녹색 `+` 단추를 클릭하고, 해당하는 ID 공급자 개체를 선택합니다.

    ![해당하는 ID 공급자 개체를 선택하는 스크린샷](./media/check-point-remote-access-vpn-tutorial/green-button.png)

1. 다중 로그온 옵션 창에서: 왼쪽 창에서 **사용자 디렉터리** 를 클릭한 다음, **수동 구성** 을 선택합니다.
두 가지 옵션 중이 있습니다.
    1. 온-프레미스 Active Directory(LDAP)를 사용하지 않으려면 외부 사용자 프로필만 선택하고 확인을 클릭합니다.
    2. 온-프레미스 Active Directory(LDAP)를 사용하려면 LDAP 사용자만 선택하고 LDAP 조회 유형에서 메일을 선택합니다. 그런 후 확인을 클릭합니다.

    ![수동 구성의 스크린샷.](./media/check-point-remote-access-vpn-tutorial/manual-configuration.png)

1. 관리 데이터베이스에서 필요한 설정을 구성합니다.

    1. SmartConsole을 닫습니다.

    2. GuiDBEdit 도구를 사용하여 관리 서버에 연결합니다([sk13009](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails&solutionid=sk13009) 참조).

    3. 왼쪽 위 창에서 **편집 > 네트워크 개체** 로 이동합니다.

    4. 오른쪽 위 창에서 **보안 게이트웨이 개체** 를 선택합니다.

    5. 아래쪽 창에서 **realms_for_blades > vpn** 으로 이동합니다.

    6. 온-프레미스 Active Directory(LDAP)를 사용하지 않으려면 **do_ldap_fetch** 를 **false** 로 설정하고 **do_generic_fetch** 를 **true** 로 설정합니다. 그런 후 **OK** 를 클릭합니다. 온-프레미스 Active Directory(LDAP)를 사용하려면 **do_ldap_fetch** 를 **true** 로 설정하고 **do_generic_fetch** 를 **false** 로 설정합니다. 그런 후 **OK** 를 클릭합니다.

    7. 적용 가능한 모든 보안 게이트웨이에 대해 4~6단계를 반복합니다.

    8. **파일** > **모두 저장** 을 차례로 선택하여 모든 변경 내용을 저장합니다.

1. GuiDBEdit 도구를 닫습니다.

1. 각 보안 게이트웨이와 각 소프트웨어 블레이드에는 별도의 설정이 있습니다. 인증(VPN, Mobile Access 및 Identity Awareness)을 사용하는 각 보안 게이트웨이 및 각 소프트웨어 블레이드의 설정을 검토합니다.

    * **LDAP 사용자** 옵션은 LDAP를 사용하는 소프트웨어 블레이드에 대해서만 선택해야 합니다.

    * **외부 사용자 프로필** 옵션은 LDAP를 사용하지 않는 소프트웨어 블레이드에 대해서만 선택해야 합니다.

1. 각 보안 게이트웨이에 액세스 제어 정책을 설치합니다.

### <a name="vpn-ra-client-installation-and-configuration"></a>VPN RA 클라이언트 설치 및 구성

1. VPN 클라이언트를 설치합니다.

1. ID 공급자 브라우저 모드를 설정합니다(선택 사항).

    기본적으로 Windows 클라이언트는 포함된 브라우저를 사용하고, macOS 클라이언트는 Safari를 사용하여 ID 공급자의 포털에서 인증합니다. Windows 클라이언트의 경우 Internet Explorer를 대신 사용하도록 이 동작을 변경합니다.

    1. 클라이언트 머신에서 일반 텍스트 편집기를 관리자 권한으로 엽니다.

    2. 텍스트 편집기에서 `trac.defaults` 파일을 엽니다.

       * 32비트 Windows: 

         `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

       * 64비트 Windows: 

         `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

    3. `idp_browser_mode` 특성 값을 `embedded`에서 `IE`로 변경합니다.

    4. 파일을 저장합니다.

    5. Check Point Endpoint Security VPN 클라이언트 서비스를 다시 시작합니다.

   관리자 권한으로 Windows 명령 프롬프트를 열고 다음 명령을 실행합니다.

   `# net stop TracSrvWrapper`

   `# net start TracSrvWrapper`

1. 백그라운드에서 실행되는 브라우저를 사용하여 인증을 시작합니다.

    1. 클라이언트 머신에서 일반 텍스트 편집기를 관리자 권한으로 엽니다.

    2. 텍스트 편집기에서 `trac.defaults` 파일을 엽니다.

        * 32비트 Windows: 

          `%ProgramFiles%\CheckPoint\Endpoint Connect\trac.defaults`

        * 64비트 Windows: 

          `%ProgramFiles(x86)%\CheckPoint\Endpoint Connect\trac.defaults`

        * macOS에서:

          `/Library/Application Support/Checkpoint/Endpoint Security/Endpoint Connect/trac.defaults`

    3. `idp_show_browser_primary_auth_flow`의 값을 `false`로 변경합니다.

    4. 파일을 저장합니다.

    5. Check Point Endpoint Security VPN 클라이언트 서비스를 다시 시작합니다.

       * Windows 클라이언트에서 관리자 권한으로 Windows 명령 프롬프트를 열고 다음 명령을 실행합니다.

         `# net stop TracSrvWrapper`

         `# net start TracSrvWrapper`

       * macOS 클라이언트에서 다음을 실행합니다.

         `sudo launchctl stop com.checkpoint.epc.service`

         `sudo launchctl start com.checkpoint.epc.service`

### <a name="create-check-point-remote-secure-access-vpn-test-user"></a>Check Point Remote Secure Access VPN 테스트 사용자 만들기

이 섹션에서는 Check Point Remote Secure Access VPN에서 Britta Simon이라는 사용자를 만듭니다. Check Point Remote Secure Access VPN 플랫폼에서 사용자를 추가하려면 [Check Point Remote Secure Access VPN 지원 팀](mailto:support@checkpoint.com)에 문의하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트

1. VPN 클라이언트를 열고 **연결...** 을 클릭합니다.

    ![연결의 스크린샷](./media/check-point-remote-access-vpn-tutorial/connect.png)

1. 드롭다운에서 **사이트** 를 선택하고 **연결** 을 클릭합니다.

    ![사이트 선택의 스크린샷](./media/check-point-remote-access-vpn-tutorial/site.png)

1. Azure AD 로그인 팝업의 **Azure AD 테스트 사용자 만들기** 섹션에서 사용자가 만든 Azure AD 자격 증명을 사용하여 로그인합니다.

## <a name="next-steps"></a>다음 단계

Check Point Remote Secure Access VPN이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
