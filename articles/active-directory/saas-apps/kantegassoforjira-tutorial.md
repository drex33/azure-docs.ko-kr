---
title: '자습서: Kantega SSO for JIRA와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Kantega SSO for JIRA 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/27/2021
ms.author: jeedes
ms.openlocfilehash: cb972e9ac5b04f4f27c3717fc7b99a4c2d3eff8e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313767"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-jira"></a>자습서: Kantega SSO for JIRA와 Azure Active Directory 통합

이 자습서에서는 Kantega SSO for JIRA를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Azure AD와 Kantega SSO for JIRA를 통합하면 다음을 수행할 수 있습니다.

* Kantega SSO for JIRA에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Kantega SSO for JIRA에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Kantega SSO for JIRA와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

* Kantega SSO for JIRA Single Sign-On이 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Kantega SSO for JIRA에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-kantega-sso-for-jira-from-the-gallery"></a>갤러리에서 Kantega SSO for JIRA 추가

Kantega SSO for JIRA가 Azure AD에 통합되도록 구성하려면 갤러리에서 Kantega SSO for JIRA를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Kantega SSO for JIRA** 를 입력합니다.
1. 결과 패널에서 **Kantega SSO for JIRA** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-kantega-sso-for-jira"></a>Kantega SSO for JIRA에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Kantega SSO for JIRA에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Kantega SSO for JIRA의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Kantega SSO for JIRA에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Kantega SSO for JIRA SSO 구성](#configure-kantega-sso-for-jira-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Kantega SSO for JIRA 테스트 사용자 만들기](#create-kantega-sso-for-jira-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Kantega SSO for JIRA에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Kantega SSO for JIRA** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<UNIQUE_ID>/login` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL로 업데이트합니다. 이러한 값은 JIRA 플러그 인 구성 중에 수신되며 자습서의 뒷부분에 설명되어 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Kantega SSO for JIRA 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Kantega SSO for JIRA에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Kantega SSO for JIRA** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-kantega-sso-for-jira-sso"></a>Kantega SSO for JIRA SSO 구성

1. 다른 웹 브라우저 창에서 JIRA 온-프레미스 서버에 관리자로 로그인합니다.

1. 그러나 톱니바퀴에서 **추가 기능** 을 클릭합니다.

    ![선택된 "Cog" 아이콘과 드롭다운에서 선택된 "추가 기능"을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/settings.png)

1. [추가 기능] 탭 섹션에서 **새 추가 기능 찾기** 를 클릭합니다. **Kantega SSO for JIRA(SAML & Kerberos)** 를 검색하고 **설치** 단추를 클릭하여 새 SAML 플러그 인을 설치합니다.

    ![검색 상자의 "Kantego SSO for JIRA(SAML 및 Kerberos)" 및 "설치" 단추가 선택된 "새 추가 기능 찾기" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/install-tab.png)

1. 플러그 인 설치가 시작됩니다.

    ![플러그 인 "설치" 대화 상자를 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/installation.png)

1. 설치가 완료되면 **닫기** 를 클릭합니다.

    !["설치 및 준비 완료!"를 보여주는 스크린샷. "닫기" 작업이 선택된 대화 상자.](./media/kantegassoforjira-tutorial/close-tab.png)

1.  **관리** 를 클릭합니다.

    !["관리" 단추가 선택된 "Kantega SSO" 앱 페이지를 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/manage-tab.png)
    
1. 새 플러그 인이 **INTEGRATIONS**(통합) 아래 나열됩니다. **구성** 을 클릭하여 새 플러그 인을 구성합니다.

    ![왼쪽 탐색 메뉴에서 "통합"이 강조 표시되고 "추가 기능 관리" 섹션에서 선택된 "구성" 단추를 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/integration.png)

1. **SAML** 섹션의 **ID 공급자 추가** 드롭다운에서 **Azure AD(Azure Active Directory)** 를 선택합니다.

    !["Azure AD(Azure Active Directory)"가 선택된 "ID 공급자 추가" 드롭다운을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/identity-provider.png)

1. 구독 수준을 **기본** 으로 선택합니다.

    !["기본 사항"이 선택된 "Azure AD 준비" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/basic-tab.png)

1. **앱 속성** 섹션에서 다음 단계를 수행합니다. 

    !["앱 ID URL" 텍스트 상자와 복사 단추가 강조 표시되고 "다음" 단추가 선택된 "앱 속성" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/properties.png)

    1. **앱 ID URI** 를 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에서 **식별자, 회신 URL 및 로그온 URL** 로 사용합니다.

    1. **다음** 을 클릭합니다.

1. **Metadata import**(메타데이터 가져오기) 섹션에서 다음 단계를 수행합니다. 

    !["내 컴퓨터의 메타데이터 파일"이 선택된 "메타데이터 가져오기" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/metadata.png)

    1. **Metadata file on my computer**(내 컴퓨터의 메타데이터 파일)를 클릭하여 Azure Portal에서 다운로드한 메타데이터 파일을 업로드합니다.

    1. **다음** 을 클릭합니다.

1. **Name and SSO location**(이름 및 SSO 위치) 섹션에서 다음 단계를 수행합니다.

    !["ID 공급자 이름" 텍스트 상자가 강조 표시되고 "다음" 단추가 선택된 "이름 및 SSO 위치"를 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/location.png)

    1. **ID 공급자 이름** 텍스트 상자에 ID 공급자의 이름(예: Azure AD)을 입력합니다.

    1. **다음** 을 클릭합니다.

1. 서명 인증서를 확인하고 **다음** 을 클릭합니다.

    !["다음" 단추가 선택된 "서명 확인" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/certificate.png)

1. **JIRA 사용자 계정** 섹션에서 다음 단계를 수행합니다.

    !["필요한 경우 JIRA의 내부 디렉터리에 사용자 만들기" 옵션이 강조 표시되고 "다음" 단추가 선택된 "JIRA 사용자 계정"을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/accounts.png)

    1. **필요한 경우 JIRA의 내부 디렉터리에 사용자 만들기** 를 선택하고 사용자에 대한 적절한 그룹 이름을 입력합니다(그룹이 여러 개인 경우 쉼표로 구분 가능).

    1. **다음** 을 클릭합니다.

1. **Finish** 를 클릭합니다.

    !["마침" 단추가 선택된 "요약" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/finish-tab.png)

1. **Known domains for Azure AD**(Azure AD에 알려진 도메인) 섹션에서 다음 단계를 수행합니다.

    ![Single Sign-on 구성](./media/kantegassoforjira-tutorial/save-tab.png)

    1. 페이지의 왼쪽 창에서 **Known domains**(알려진 도메인)를 선택합니다.

    2. **Known domains**(알려진 도메인) 텍스트 상자에 도메인 이름을 입력합니다.

    3. **저장** 을 클릭합니다.

### <a name="create-kantega-sso-for-jira-test-user"></a>Kantega SSO for JIRA 테스트 사용자 만들기

Azure AD 사용자가 JIRA에 로그인하려면 JIRA에 프로비저닝되어야 합니다. Kantega SSO for JIRA에서는 프로비전이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. JIRA 온-프레미스 서버에 관리자로 로그인합니다.

1. 마우스로 선 위를 가리키고 **사용자 관리** 를 클릭합니다.

    ![선택된 "Cog" 아이콘과 드롭다운에서 선택된 "사용자 관리"를 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/user.png) 

1. **사용자 관리** 탭 섹션에서 **사용자 만들기** 를 클릭합니다.

    !["사용자 만들기" 단추가 선택된 "사용자 관리" 섹션을 보여주는 스크린샷.](./media/kantegassoforjira-tutorial/create-user.png) 

1. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![직원 추가](./media/kantegassoforjira-tutorial/new-user.png) 

    1. **이메일 주소** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    2. **전체 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 전체 이름을 입력합니다.

    3. **사용자 이름** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    4. **암호** 텍스트 상자에서 사용자에 대한 암호를 입력합니다.

    5. **사용자 만들기** 를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Kantega SSO for JIRA 로그온 URL로 리디렉션됩니다.  

* Kantega SSO for JIRA 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Kantega SSO for JIRA에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Kantega SSO for JIRA 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Kantega SSO for JIRA에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Kantega SSO for JIRA가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
