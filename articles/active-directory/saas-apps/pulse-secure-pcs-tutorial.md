---
title: '자습서: Pulse Secure PCS와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Pulse Secure PCS 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/11/2020
ms.author: jeedes
ms.openlocfilehash: 743557b3171a0bee6615a875ff647107e81481d1
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749569"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-pulse-secure-pcs"></a>자습서: Pulse Secure PCS와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Pulse Secure PCS를 통합하는 방법에 대해 알아봅니다. Azure AD와 Pulse Secure PCS를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Pulse Secure PCS에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Pulse Secure PCS에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Pulse Secure PCS SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Pulse Secure PCS에서 **SP** 시작 SSO를 지원합니다.

## <a name="adding-pulse-secure-pcs-from-the-gallery"></a>갤러리에서 Pulse Secure PCS 추가

Pulse Secure PCS가 Azure AD에 통합되도록 구성하려면 갤러리의 Pulse Secure PCS를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Pulse Secure PCS** 를 입력합니다.
1. 결과 패널에서 **Pulse Secure PCS** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-pulse-secure-pcs"></a>Pulse Secure PCS에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Pulse Secure PCS에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Pulse Secure PCS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Pulse Secure PCS에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Pulse Secure PCS SSO 구성](#configure-pulse-secure-pcs-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Pulse Secure PCS 테스트 사용자 만들기](#create-pulse-secure-pcs-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Pulse Secure PCS에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Pulse Secure PCS** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<FQDN of PCS>/dana-na/auth/saml-consumer.cgi` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<FQDN of PCS>/dana-na/auth/saml-endpoint.cgi?p=sp1` 패턴을 사용하는 URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에서 `https://[FQDN of PCS]/dana-na/auth/saml-consumer.cgi` 패턴을 사용하여 URL을 입력합니다.


    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL, 회신 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Pulse Secure PCS 클라이언트 지원 팀](mailto:support@pulsesecure.net)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Pulse Secure PCS 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon에게 Pulse Secure PCS에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Pulse Secure PCS** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-pulse-secure-pcs-sso"></a>Pulse Secure PCS SSO 구성

이 섹션에서는 PCS를 SAML SP로 구성하는 데 필요한 SAML 구성에 대해 설명합니다. 영역 및 역할 만들기와 같은 다른 기본 구성은 다루지 않습니다.

**Pulse Connect Secure 구성은 다음과 같습니다.**

* Azure AD를 SAML 메타데이터 공급자로 구성
* SAML 인증 서버 구성
* 각 영역 및 역할에 할당

#### <a name="configuring-azure-ad-as-saml-metadata-provider"></a>Azure AD를 SAML 메타데이터 공급자로 구성

다음 페이지에서 다음 단계를 수행합니다.

![Pulse Connect Secure 구성](./media/pulse-secure-pcs-tutorial/saml-configuration.png)

1. Pulse Connect Secure 관리 콘솔에 로그인합니다.
1. **System -> Configuration -> SAML**(시스템 -> 구성 -> SAML)로 차례로 이동합니다.
1. **New Metadata Provider**(새 메타데이터 공급자)를 클릭합니다.
1. **Name**(이름) 텍스트 상자에서 올바른 이름을 입력합니다.
1. Azure Portal에서 다운로드한 메타데이터 XML 파일을 **Azure AD metadata file**(Azure AD 메타데이터 파일)에 업로드합니다.
1. **Accept Unsigned Metadata**(서명되지 않은 메타데이터 허용)를 선택합니다.
1. Roles(역할)를 **Identity Provider**(ID 공급자)로 선택합니다.
1. **Save changes**(변경 내용 저장)를 클릭합니다.

#### <a name="steps-to-create-a-saml-auth-server"></a>SAML 인증 서버를 만드는 단계:

1. **Authentication -> Auth Servers**(인증 -> 인증 서버)로 차례로 이동합니다.
1. **New: SAML Server**(새로 만들기: SAML 서버)를 선택하고, **New Server**(새 서버)를 클릭합니다.

    ![Pulse Connect Secure 인증 서버](./media/pulse-secure-pcs-tutorial/new-saml-server.png)

1. 설정 페이지에서 다음 단계를 수행합니다.

    ![Pulse Connect Secure 인증 서버 설정](./media/pulse-secure-pcs-tutorial/server-settings.png)

    a. 텍스트 상자에서 **Server Name**(서버 이름)을 제공합니다.

    b. **SAML Version 2.0**(SAML 버전 2.0) 및 **Configuration Mode**(구성 모드)를 **Metadata**(메타데이터)로 선택합니다.

    다. **Connect Secure Entity Id**(Connect Secure 엔터티 ID) 값을 복사하여 Azure Portal의 **기본 SAML 구성** 대화 상자에 있는 **식별자 URL** 상자에 붙여넣습니다.

    d. **Identity Provider Entity Id(ID 공급자 엔터티 ID) 드롭다운 목록** 에서 Azure AD 엔터티 ID 값을 선택합니다.

    e. **Identity Provider Single Sign-On Service URL(ID 공급자 Single Sign-On 서비스 URL) 드롭다운 목록** 에서 Azure AD 로그인 URL 값을 선택합니다.

    f. **Single Logout**(단일 로그아웃)은 선택적 설정입니다. 이 옵션을 선택하면 로그아웃한 후에 새 인증을 요구하는 메시지가 표시됩니다. 이 옵션을 선택하지 않고 브라우저를 닫지 않은 경우 인증 없이 다시 연결할 수 있습니다.

    g. **Requested Authn Context Class**(요청된 인증 컨텍스트 클래스)를 **Password** 로 선택하고, **Comparison Method**(비교 메서드)를 **exact** 로 선택합니다.

    h. **Metadata Validity**(메타데이터 유효성)를 일 수를 기준으로 설정합니다.
    
    i. **변경 내용 저장** 을 클릭합니다.

### <a name="create-pulse-secure-pcs-test-user"></a>Pulse Secure PCS 테스트 사용자 만들기

이 섹션에서는 Pulse Secure PCS에서 Britta Simon이라는 사용자를 만듭니다. [Pulse Secure PCS 지원 팀](mailto:support@pulsesecure.net)과 협력하여 사용자를 Pulse Secure PCS 플랫폼에 추가합니다. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

1. Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Pulse Secure PCS 로그온 URL로 리디렉션됩니다. 

2. Pulse Secure PCS 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

3. Microsoft 액세스 패널을 사용할 수 있습니다. 액세스 패널에서 Pulse Secure PCS 타일을 클릭하면 Pulse Secure PCS 로그온 URL로 리디렉션됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Pulse Secure PCS가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).