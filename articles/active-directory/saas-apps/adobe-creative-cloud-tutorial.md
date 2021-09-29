---
title: '자습서: Adobe Creative Cloud와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Adobe Creative Cloud 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jeedes
ms.openlocfilehash: 5ba23a1bc5740eece8aa5e48d4f6cd81fab7b87e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803435"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-adobe-creative-cloud"></a>자습서: Adobe Creative Cloud와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Adobe Creative Cloud를 통합하는 방법에 대해 알아봅니다. Azure AD와 Adobe Creative Cloud를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서 Adobe Creative Cloud에 액세스할 수 있는 사용자를 제어할 수 있습니다.
* 사용자가 자신의 Azure AD 계정으로 Adobe Creative Cloud에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Adobe Creative Cloud SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Adobe Creative Cloud에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-adobe-creative-cloud-from-the-gallery"></a>갤러리에서 Adobe Creative Cloud 추가

Adobe Creative Cloud가 Azure AD에 통합되도록 구성하려면 Adobe Creative Cloud를 갤러리에서 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Adobe Creative Cloud** 를 입력합니다.
1. 결과 패널에서 **Adobe Creative Cloud** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-adobe-creative-cloud"></a>Adobe Creative Cloud에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Adobe Creative Cloud에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Adobe Creative Cloud의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Adobe Creative Cloud에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Adobe Creative Cloud SSO 구성](#configure-adobe-creative-cloud-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Adobe Creative Cloud 테스트 사용자 만들기](#create-adobe-creative-cloud-test-user)** - B.Simon의 Azure AD 표현과 연결되는 해당 사용자를 Adobe Creative Cloud에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Adobe Creative Cloud** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에 `https://adobe.com` URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://www.okta.com/saml2/service-provider/<token>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 식별자 값은 실제 값이 아닙니다. **Adobe Cloud SSO 구성** 섹션의 4단계에 있는 지침을 따릅니다. 여기에서 **페더레이션 메타데이터 XML 파일** 을 열고 엔터티 ID 값을 가져와 Azure AD 구성에 식별자 값으로 넣을 수 있습니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Adobe Creative Cloud 애플리케이션은 특정 형식의 SAML 어설션을 예상하며, 따라서 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도 Adobe Creative Cloud 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email | user.mail |

    > [!NOTE]
    > SAML 응답에 이메일 클레임 값을 입력하려면 사용자에게 유효한 Microsoft 365 ExO 라이선스가 있어야 합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 데이터 XML** 을 찾은 다음, **다운로드** 를 선택하여 XML 메타데이터 파일을 다운로드하여 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Adobe Creative Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Adobe Creative Cloud에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **Adobe Creative Cloud** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택합니다. 그런 후 **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택합니다. 화면 하단에서 **선택** 을 누릅니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-adobe-creative-cloud-sso"></a>Adobe Creative Cloud SSO 구성

1. 다른 웹 브라우저 창에서 [Adobe 관리 콘솔](https://adminconsole.adobe.com)에 시스템 관리자 권한으로 로그인합니다.

1. 위쪽 탐색 모음에서 **설정** 으로 이동한 다음, **ID** 를 선택합니다. 디렉터리 목록이 열립니다. 원하는 페더레이션 디렉터리를 선택합니다.

1. **디렉터리 세부 정보** 페이지에서 **구성** 을 선택합니다.

1. 엔터티 ID와 ACS URL(Assertion Consumer Service URL 또는 회신 URL)을 복사합니다. Azure Portal의 해당 필드에 URL을 입력합니다.

    ![앱 쪽에서 Single Sign-On 구성](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. **앱 설정 구성** 대화 상자에서 **식별자** 에 대해 Adobe가 제공한 엔터티 ID 값을 사용합니다.

    b. **앱 설정 구성** 대화 상자에서 **회신 URL** 에 대해 Adobe가 제공한 ACS URL(Assertion Consumer Service URL) 값을 사용합니다.

1. 페이지 아래쪽 근처에 Azure Portal에서 다운로드한 **페더레이션 데이터 XML** 파일을 업로드합니다. 

    ![페더레이션 데이터 XML 파일](https://helpx.adobe.com/content/dam/help/en/enterprise/kb/configure-microsoft-azure-with-adobe-sso/jcr_content/main-pars/procedure/proc_par/step_228106403/step_par/image_copy/saml_signinig_certificate.png "IdP 메타데이터 XML")

1. **저장** 을 선택합니다.

### <a name="create-adobe-creative-cloud-test-user"></a>Adobe Creative Cloud 테스트 사용자 만들기

Azure AD 사용자가 Adobe Creative Cloud에 로그인할 수 있도록 하려면 Adobe Creative Cloud에 프로비저닝되어야 합니다. Adobe Creative Cloud의 경우 프로비전이 수동 작업입니다.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. [Adobe 관리 콘솔](https://adminconsole.adobe.com) 사이트에 관리자 권한으로 로그인합니다.

2. Adobe의 콘솔에서 사용자를 페더레이션 ID로 추가하고 제품 프로필에 할당합니다. 사용자를 추가하는 방법에 대한 자세한 내용은 [Adobe 관리 콘솔에서 사용자 추가](https://helpx.adobe.com/enterprise/using/users.html#Addusers)를 참조하세요.

3. 이 시점에서 이메일 주소/UPN을 Adobe 로그인 양식에 입력하고 Tab 키를 눌러 다음과 같이 Azure AD에 다시 페더레이션되어야 합니다.
   * 웹 액세스: www\.adobe.com > 로그인
   * 바탕 화면 앱 유틸리티 내에서 > 로그인
   * 애플리케이션 내에서 &gt; 도움말 &gt; 로그인

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Adobe Creative Cloud 로그온 URL로 리디렉션됩니다. 

* Adobe Creative Cloud 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Adobe Creative Cloud 타일을 클릭하면 Adobe Creative Cloud 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Adobe Creative Cloud가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](/cloud-app-security/proxy-deployment-any-app)