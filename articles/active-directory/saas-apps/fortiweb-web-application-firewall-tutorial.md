---
title: '자습서: FortiWeb Web Application Firewall과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 FortiWeb Web Application Firewall 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 11/24/2020
ms.author: jeedes
ms.openlocfilehash: 9331b950a5093f94625938626d4d79d8fc6dd08b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fortiweb-web-application-firewall"></a>자습서: FortiWeb Web Application Firewall과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 FortiWeb Web Application Firewall을 통합하는 방법에 대해 알아봅니다. Azure AD와 FortiWeb Web Application Firewall을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 FortiWeb Web Application Firewall에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 FortiWeb Web Application Firewall에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* FortiWeb Web Application Firewall SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* FortiWeb Web Application Firewall에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-fortiweb-web-application-firewall-from-the-gallery"></a>갤러리에서 FortiWeb Web Application Firewall 추가

FortiWeb Web Application Firewall이 Azure AD에 통합되도록 구성하려면 갤러리의 FortiWeb Web Application Firewall을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **FortiWeb Web Application Firewall** 을 입력합니다.
1. 결과 패널에서 **FortiWeb Web Application Firewall** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-fortiweb-web-application-firewall"></a>FortiWeb Web Application Firewall에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 FortiWeb Web Application Firewall에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 FortiWeb Web Application Firewall의 관련 사용자 간에 연결 관계를 설정해야 합니다.

FortiWeb Web Application Firewall에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[FortiWeb Web Application Firewall SSO 구성](#configure-fortiweb-web-application-firewall-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[FortiWeb Web Application Firewall 테스트 사용자 만들기](#create-fortiweb-web-application-firewall-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 FortiWeb Web Application Firewall에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **FortiWeb Web Application Firewall** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    1. **식별자(엔터티 ID)** 텍스트 상자에서 `https://www.<CUSTOMER_DOMAIN>.com` 패턴을 사용하는 URL을 입력합니다.

    1. **회신 URL** 텍스트 상자에서 `https://www.<CUSTOMER_DOMAIN>.com/<FORTIWEB_NAME>/saml.sso/SAML2/POST` 패턴을 사용하여 URL을 입력합니다.

    1. **로그온 URL** 텍스트 상자에서 `https://www.<CUSTOMER_DOMAIN>.com` 패턴을 사용하는 URL을 입력합니다.

    1. **로그아웃 URL** 텍스트 상자에 `https://www.<CUSTOMER_DOMAIN>.info/<FORTIWEB_NAME>/saml.sso/SLO/POST` 패턴을 사용하여 URL을 입력합니다.
 
    > [!NOTE]
    > `<FORTIWEB_NAME>`은 나중에 구성을 FortiWeb에 제공할 때 사용할 이름 식별자입니다.
    > 실제 URL 값을 얻으려면 [FortiWeb Web Application Firewall 지원 팀](mailto:support@fortinet.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

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

이 섹션에서는 B.Simon에게 FortiWeb Web Application Firewall에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **FortiWeb Web Application Firewall** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-fortiweb-web-application-firewall-sso"></a>FortiWeb Web Application Firewall SSO 구성

1.  `https://<address>:8443`으로 이동합니다. 여기서 `<address>`는 FortiWeb VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2.  FortiWeb VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.

1. 다음 페이지에서 다음 단계를 수행합니다.

    ![SAML 서버 페이지](./media/fortiweb-web-application-firewall-tutorial/configure-sso.png)

    a.  왼쪽 메뉴에서 **User(사용자)** 를 클릭합니다.

    b.  User 아래에서 **Remote Server(원격 서버)** 를 클릭합니다.

    다.  **SAML Server(SAML 서버)** 를 클릭합니다.

    d.  **새로 만들기** 를 클릭합니다.

    e.  **Name(이름)** 필드에서 [Azure AD 구성] 섹션에서 사용된 `<fwName>`에 대한 값을 입력합니다.

    f.  Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    g. **Metadata(메타데이터)** 옆에서 **Choose File(파일 선택)** 을 클릭하고, Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 파일을 선택합니다.

    h.  **확인** 을 클릭합니다.

### <a name="create-a-site-publishing-rule"></a>사이트 게시 규칙 만들기

1.  `https://<address>:8443`으로 이동합니다. 여기서 `<address>`는 FortiWeb VM에 할당된 FQDN 또는 공용 IP 주소입니다.

1.  FortiWeb VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
1. 다음 페이지에서 다음 단계를 수행합니다.

    ![사이트 게시 규칙](./media/fortiweb-web-application-firewall-tutorial/site-publish-rule.png)

    a.  왼쪽 메뉴에서 **Application Delivery(애플리케이션 전송)** 를 클릭합니다.
    
    b.  **Application Delivery** 아래에서 **Site Publish(사이트 게시)** 를 클릭합니다.
    
    다.  **Site Publish** 아래에서 **Site Publish** 를 클릭합니다.
    
    d.  **Site Publish Rule(사이트 게시 규칙)** 을 클릭합니다.
    
    e.  **새로 만들기** 를 클릭합니다.
    
    f.  사이트 게시 규칙 이름을 제공합니다.
    
    g.  **Published Site Type(게시된 사이트 유형)** 옆에서 **Regular Expression(정규식)** 을 클릭합니다.
    
    i.  **Published Site(게시된 사이트)** 옆에서 게시하려는 웹 사이트의 호스트 헤더와 일치하는 문자열을 제공합니다.
    
    j.  **Path(경로)** 옆에서 /를 제공합니다.
    
    k.  **Client Authentication Method(클라이언트 인증 방법)** 옆에서 **SAML Authentication(SAML 인증)** 을 선택합니다.
    
    l.  **SAML Server(SAML 서버)** 드롭다운에서 이전에 만든 SAML 서버를 선택합니다.
    
    m.  **확인** 을 클릭합니다.


### <a name="create-a-site-publishing-policy"></a>사이트 게시 정책 만들기

1.  `https://<address>:8443`으로 이동합니다. 여기서 `<address>`는 FortiWeb VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2.  FortiWeb VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.

1. 다음 페이지에서 다음 단계를 수행합니다.

    ![사이트 게시 정책](./media/fortiweb-web-application-firewall-tutorial/site-publish-policy.png)

    a.  왼쪽 메뉴에서 **Application Delivery(애플리케이션 전송)** 를 클릭합니다.

    b.  **Application Delivery** 아래에서 **Site Publish(사이트 게시)** 를 클릭합니다.

    다.  **Site Publish** 아래에서 **Site Publish** 를 클릭합니다.

    d.  **Site Publish Policy(사이트 게시 정책)** 를 클릭합니다.

    e.  **새로 만들기** 를 클릭합니다.

    f.  사이트 게시 정책 이름을 제공합니다.

    g.  **확인** 을 클릭합니다.

    h.  **새로 만들기** 를 클릭합니다.

    i.  **Rule(규칙)** 드롭다운에서 이전에 만든 사이트 게시 규칙을 선택합니다.

    j.  **확인** 을 클릭합니다.

### <a name="create-and-assign-a-web-protection-profile"></a>웹 보호 프로필 만들기 및 할당

1.  `https://<address>:8443`으로 이동합니다. 여기서 `<address>`는 FortiWeb VM에 할당된 FQDN 또는 공용 IP 주소입니다.

2.  FortiWeb VM 배포 중에 제공된 관리자 자격 증명을 사용하여 로그인합니다.
3.  왼쪽 메뉴에서 **Policy(정책)** 를 클릭합니다.
4.  **Policy(정책)** 아래에서 **Web Protection Profile(웹 보호 프로필)** 을 클릭합니다.
5.  **Inline Standard Protection(인라인 표준 보호)** , **Clone(복제)** 을 차례로 클릭합니다.
6.  새 웹 보호 프로필 이름을 제공하고, **OK(확인)** 를 클릭합니다.
7.  새 웹 보호 프로필을 선택하고, **Edit(편집)** 를 클릭합니다.
8.  **Site Publish(사이트 게시)** 옆에서 이전에 만든 사이트 게시 정책을 선택합니다.
9.  **확인** 을 클릭합니다.
 
    ![사이트 게시](./media/fortiweb-web-application-firewall-tutorial/web-protection.png)

10. 왼쪽 메뉴에서 **Policy(정책)** 를 클릭합니다.
11. **Policy(정책)** 아래에서 **Server Policy(서버 정책)** 를 클릭합니다.
12. Azure Active Directory를 인증에 사용하도록 하려는 웹 사이트를 게시하는 데 사용되는 서버 정책을 선택합니다.
13. **편집** 을 클릭합니다.
14. **Web Protection Profile(웹 보호 프로필)** 드롭다운에서 방금 만든 웹 보호 프로필을 선택합니다.
15. **확인** 을 클릭합니다.
16. FortiWeb에서 웹 사이트를 게시하는 외부 URL에 액세스하려고 시도합니다. 인증을 위해 Azure Active Directory로 리디렉션됩니다.


### <a name="create-fortiweb-web-application-firewall-test-user"></a>FortiWeb Web Application Firewall 테스트 사용자 만들기

이 섹션에서는 FortiWeb Web Application Firewall에서 Britta Simon이라는 사용자를 만듭니다. [FortiWeb Web Application Firewall 지원 팀](mailto:support@fortinet.com)과 협력하여 사용자를 FortiWeb Web Application Firewall 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 FortiWeb Web Application 로그온 URL로 리디렉션됩니다. 

* FortiWeb Web Application 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 FortiWeb Web Application 타일을 클릭하면 FortiWeb Web Application 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

FortiWeb Web Application Firewall이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
