---
title: '자습서: Knowledge Anywhere LMS와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Knowledge Anywhere LMS 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/09/2021
ms.author: jeedes
ms.openlocfilehash: ecb309362b1c7171e7f3a2782de75f7ebd88b310
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132313644"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>자습서: Azure Active Directory와 Knowledge Anywhere LMS 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Knowledge Anywhere LMS를 통합하는 방법에 대해 알아봅니다. Knowledge Anywhere LMS를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Knowledge Anywhere LMS에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Knowledge Anywhere LMS에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Knowledge Anywhere LMS SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다. 
* Knowledge Anywhere LMS는 **SP** 시작 SSO를 지원합니다.
* Knowledge Anywhere LMS는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-knowledge-anywhere-lms-from-the-gallery"></a>갤러리에서 Knowledge Anywhere LMS 추가

Knowledge Anywhere LMS가 Azure AD에 통합되도록 구성하려면 갤러리의 Knowledge Anywhere LMS를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Knowledge Anywhere LMS** 를 입력합니다.
1. 결과 패널에서 **Knowledge Anywhere LMS** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-knowledge-anywhere-lms"></a>Knowledge Anywhere LMS에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Knowledge Anywhere LMS에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Knowledge Anywhere LMS의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Knowledge Anywhere LMS에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Knowledge Anywhere LMS SSO 구성](#configure-knowledge-anywhere-lms-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Knowledge Anywhere LMS 테스트 사용자 만들기](#create-knowledge-anywhere-lms-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Knowledge Anywhere LMS에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Knowledge Anywhere LMS** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자** 텍스트 상자에서 `https://<CLIENT_NAME>.knowledgeanywhere.com/` 패턴을 사용하여 URL을 입력합니다.

    1. **회신 URL** 텍스트 상자에서 `https://<CLIENT_NAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDP_NAME>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 이러한 값을 자습서 뒷부분에 설명된 실제 식별자 및 회신 URL로 업데이트합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<CLIENTNAME>.knowledgeanywhere.com/` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 로그온 URL 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Knowledge Anywhere LMS Client 지원 팀](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Knowledge Anywhere LMS 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B. Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예들 들어 `BrittaSimon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Knowledge Anywhere LMS에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Knowledge Anywhere LMS** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-knowledge-anywhere-lms-sso"></a>Knowledge Anywhere LMS SSO 구성

1. Knowledge Anywhere LMS 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **My Apps 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Knowledge Anywhere LMS 설정** 을 클릭하면 Knowledge Anywhere LMS 애플리케이션으로 이동됩니다. 여기서 Knowledge Anywhere LMS에 로그인할 수 있는 관리자 자격 증명을 입력합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3-7단계를 자동화합니다.

    ![설정 구성](common/setup-sso.png)

3. Knowledge Anywhere LMS를 수동으로 설정하려면 새 웹 브라우저 창을 열고, Knowledge Anywhere LMS 회사 사이트에 관리자 권한으로 로그인하고, 다음 단계를 수행합니다.

4. **사이트** 탭을 선택합니다.

    ![스크린샷은 사이트 탭을 보여줍니다.](./media/knowledge-anywhere-lms-tutorial/site.png)

5. **SAML 설정** 탭을 선택합니다.

    ![스크린샷은 SAML 설정이 선택된 Knowledge Anywhere 페이지를 보여줍니다.](./media/knowledge-anywhere-lms-tutorial/settings.png)

6. **새로 추가** 를 클릭합니다.

    ![스크린샷은 서비스 공급자 설정의 새로 추가 단추를 보여줍니다.](./media/knowledge-anywhere-lms-tutorial/add-settings.png)

7. **SAML 설정 추가/업데이트** 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 여기에 설명된 대로 변경할 수 있는 SAML 설정 추가/업데이트 페이지를 보여줍니다.](./media/knowledge-anywhere-lms-tutorial/update-settings.png)

    a. 조직별 IDP 이름을 입력합니다. 예:- `Azure`.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IDP Entity ID**(IDP 엔터티 ID) 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 을 **IDP URL** 텍스트 상자에 붙여넣습니다.

    d. Azure Portal에서 다운로드한 인증서 파일을 메모장에서 열고 인증서 내용을 복사하여 **인증서** 텍스트 상자에 붙여넣습니다.

    e. Azure Portal에서 복사한 **로그아웃 URL** 을 **Logout URL**(로그아웃 URL) 텍스트 상자에 붙여넣습니다.

    f. **도메인** 에 대한 드롭다운 목록에서 **주 사이트** 를 선택합니다.

    g. **SP 엔터티 ID** 값을 복사하고 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

    h. **SP 응답(ACS) URL** 값을 복사하고 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.

    i. **저장** 을 클릭합니다.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Knowledge Anywhere LMS 테스트 사용자 만들기

이 섹션에서는 Knowledge Anywhere LMS에서 B. Simon이라는 사용자를 만듭니다. Knowledge Anywhere LMS는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Knowledge Anywhere LMS에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Knowledge Anywhere LMS 로그온 URL로 리디렉션됩니다. 

* Knowledge Anywhere LMS 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Knowledge Anywhere LMS 타일을 클릭하면 Knowledge Anywhere LMS 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Knowledge Anywhere LMS를 구성하면 세션 제어를 적용하여 조직에서 중요한 데이터의 반출과 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
