---
title: '자습서: SAML SSO for Bitbucket by resolution GmbH와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 SAML SSO for Bitbucket by resolution GmbH 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/12/2021
ms.author: jeedes
ms.openlocfilehash: 25c96d6e69a6b76e41cc84a88b5b470bbeeb4bc0
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814048"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bitbucket-by-resolution-gmbh"></a>자습서: SAML SSO for Bitbucket by resolution GmbH와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SAML SSO for Bitbucket by resolution GmbH를 통합하는 방법에 대해 알아봅니다. Azure AD와 SAML SSO for Bitbucket by resolution GmbH를 통합하면 다음을 수행할 수 있습니다.

* SAML SSO for Bitbucket by resolution GmbH에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 SAML SSO for Bitbucket by resolution GmbH에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.


## <a name="prerequisites"></a>사전 요구 사항

SAML SSO for Bitbucket by resolution GmbH와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 구할 수 있습니다.
* SAML SSO for Bitbucket by resolution GmbH Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* SAML SSO for Bitbucket by resolution GmbH에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* SAML SSO for Bitbucket by resolution GmbH에서 **Just-In-Time** 사용자 프로비전을 지원합니다.


## <a name="add-saml-sso-for-bitbucket-by-resolution-gmbh-from-the-gallery"></a>갤러리에서 SAML SSO for Bitbucket by resolution GmbH 추가

SAML SSO for Bitbucket by resolution GmbH가 Azure AD에 통합하도록 구성하려면 SAML SSO for Bitbucket by resolution GmbH를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 창에서 **Azure Active Directory** 를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SAML SSO for Bitbucket by resolution GmbH** 를 입력합니다.
1. 결과에서 **SAML SSO for Bitbucket by resolution GmbH** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-saml-sso-for-bitbucket-by-resolution-gmbh"></a>SAML SSO for Bitbucket by resolution GmbH에서 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SAML SSO for Bitbucket by resolution GmbH에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SAML SSO for Bitbucket by resolution GmbH의 관련 사용자 간에 연결된 관계를 설정해야 합니다.

SAML SSO for Bitbucket by resolution GmbH에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.


1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[SAML SSO for Bitbucket by resolution GmbH SSO 구성](#configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SAML SSO for Bitbucket by resolution GmbH 테스트 사용자 만들기](#create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 SAML SSO for Bitbucket by resolution GmbH에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

이 섹션에서는 Azure Portal에서 Azure AD SSO를 사용하도록 설정합니다.
 
1. Azure Portal의 **SAML SSO for Bitbucket by resolution GmbH** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

    ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.


    a. **식별자** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    다. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.
    
    **로그인 URL** 텍스트 상자에서 `https://<server-base-url>/plugins/servlet/samlsso` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [SAML SSO for Bitbucket by resolution GmbH 지원 팀](https://marketplace.atlassian.com/apps/1217045/saml-single-sign-on-sso-bitbucket?hosting=server&tab=support)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, 암호를 적어둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 SAML SSO for Bitbucket by resolution GmbH에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **SAML SSO for Bitbucket by resolution GmbH** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택합니다. 화면 하단에서 **선택** 을 누릅니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-saml-sso-for-bitbucket-by-resolution-gmbh-sso"></a>SAML SSO for Bitbucket by resolution GmbH SSO 구성

1. SAML SSO for Bitbucket by resolution GmbH 회사 사이트에 관리자로 로그인합니다.

2. 주 도구 모음의 오른쪽에서 **설정** 을 클릭합니다.

3. ACCOUNTS 섹션으로 이동하여 메뉴 모음의 **SAML SingleSignOn** 을 클릭합니다.

    ![Samlsingle](./media/bitbucket-tutorial/tutorial_bitbucket_samlsingle.png)

4. **SAML SIngleSignOn Plugin Configuration page**(SAML SIngleSignOn 플러그 인 구성 페이지)에서 **Add idp**(idp 추가)를 클릭합니다. 

    ![idp 추가](./media/bitbucket-tutorial/tutorial_bitbucket_addidp.png)

5. **Choose your SAML Identity Provider**(SAML ID 공급자 선택) 페이지에서 다음 단계를 수행합니다.

    ![ID 공급자](./media/bitbucket-tutorial/tutorial_bitbucket_identityprovider.png)

    a. **Idp Type**(Idp 유형)을 **AZURE AD** 로 선택합니다.

    b. **이름** 텍스트 상자에 이름을 입력합니다.

    다. **설명** 텍스트 상자에 설명을 입력합니다.

    d. **다음** 을 클릭합니다.

6. **Identity provider configuration**(ID 공급자 구성) 페이지에서 **다음** 을 클릭합니다.

    ![ID 구성](./media/bitbucket-tutorial/tutorial_bitbucket_identityconfig.png)

7.  **Import SAML Idp Metadata**(SAML Idp 메타데이터 가져 오기) 페이지에서 **파일 로드** 를 클릭하여 Azure Portal에서 다운로드한 **메타데이터 XML** 파일을 업로드합니다.

    ![idpmetadata](./media/bitbucket-tutorial/tutorial_bitbucket_idpmetadata.png)

8. **다음** 을 클릭합니다.

9. **설정 저장** 을 클릭합니다.

    ![저장](./media/bitbucket-tutorial/tutorial_bitbucket_save.png)


## <a name="create-saml-sso-for-bitbucket-by-resolution-gmbh-test-user"></a>SAML SSO for Bitbucket by resolution GmbH 테스트 사용자 만들기

이 섹션은 SAML SSO for Bitbucket by resolution GmbH에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. SAML SSO for Bitbucket by resolution GmbH는 Just-In-Time 프로비전을 지원하며 사용자를 수동으로 만들 수도 있습니다. 사용자의 요구 사항에 따라 [SAML SSO for Bitbucket by resolution GmbH 클라이언트 지원팀](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bitbucket/server/support)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SAML SSO for Bitbucket by resolution GmbH 로그온 URL로 리디렉션됩니다.  

* SAML SSO for Bitbucket by resolution GmbH 로그온 URL로 바로 이동하고 거기서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 SAML SSO for Bitbucket by resolution GmbH에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 SAML SSO for Bitbucket by resolution GmbH 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 SAML SSO for Bitbucket by resolution GmbH에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

SAML SSO for Bitbucket by resolution GmbH가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).