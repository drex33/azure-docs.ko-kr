---
title: '자습서: Fluxx Labs와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Fluxx Labs 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/20/2021
ms.author: jeedes
ms.openlocfilehash: 51056c5c28fa15026a409f9baeb0f7a9ec5fd076
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124834567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-fluxx-labs"></a>자습서: Fluxx Labs와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Fluxx Labs를 통합하는 방법에 대해 알아봅니다. Fluxx Labs를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Fluxx Labs에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Fluxx Labs에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Fluxx Labs SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Fluxx Labs에서 **IDP** 시작 SSO를 지원합니다.

## <a name="add-fluxx-labs-from-the-gallery"></a>갤러리에서 Fluxx Labs 추가

Fluxx Labs의 Azure AD 통합을 구성하려면 갤러리의 Fluxx Labs를 관리하는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Fluxx Labs** 를 입력합니다.
1. 결과 패널에서 **Fluxx Labs** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-fluxx-labs"></a>Fluxx Labs에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Fluxx Labs에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Fluxx Labs의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Fluxx Labs에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Fluxx Labs SSO 구성](#configure-fluxx-labs-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Fluxx Labs 테스트 사용자 만들기](#create-fluxx-labs-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Fluxx Labs에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Fluxx Labs** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **SAML로 Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | Environment | URL 패턴|
    |-------------|------------|
    | 프로덕션 | `https://<subdomain>.fluxx.io` |
    | 사전 프로덕션 | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. **회신 URL** 텍스트 상자에서 다음 패턴 중 하나를 사용하여 URL을 입력합니다.

    | Environment | URL 패턴|
    |-------------|------------|
    | 프로덕션 | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | 사전 프로덕션 | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 이러한 값을 얻으려면 [Fluxx Labs 클라이언트 지원 팀](https://fluxx.zendesk.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Fluxx Labs 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Fluxx Labs에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Fluxx Labs** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-fluxx-labs-sso"></a>Fluxx Labs SSO 구성

1. 다른 웹 브라우저 창에서 Fluxx Labs 회사 사이트에 관리자로 로그인합니다.

2. **설정** 섹션 아래에서 **관리** 를 선택합니다.

    !["관리자"가 선택된 "설정" 섹션을 보여주는 스크린샷.](./media/fluxxlabs-tutorial/configure-1.png)

3. 관리 패널에서 **플러그 인** > **통합** 을 차례로 선택한 다음, **SAML SSO - (사용 안 함)** 을 선택합니다.

    !["SAML SSO(사용 안 함)"가 선택된 "통합" 탭을 보여주는 스크린샷.](./media/fluxxlabs-tutorial/configure-2.png)

4. 특성 섹션에서 다음 단계를 수행합니다.

    !["SAML SSO"가 선택되고, 필드에 값이 입력되고, "저장" 단추가 선택된 "특성" 섹션을 보여주는 스크린샷.](./media/fluxxlabs-tutorial/configure-3.png)

    a. **SAML SSO** 확인란을 선택합니다.

    b. **요청 경로** 텍스트 상자에 **/auth/saml** 을 입력합니다.

    다. **콜백 경로** 텍스트 상자에 **/auth/saml/callback** 을 입력합니다.

    d. Azure Portal에서 입력한 **회신 URL** 값을 **Assertion Consumer Service URL(Single Sign-On URL)** 텍스트 상자에 입력합니다.

    e. Azure Portal에서 입력한 **식별자** 값을 **Audience(SP Entity ID)** (대상 그룹(SP 엔터티 ID)) 텍스트 상자에 입력합니다.

    f. Azure Portal에서 복사한 **로그인 URL** 값을 **ID 공급자 SSO 대상 URL** 텍스트 상자에 붙여넣습니다.

    g. Base 64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음 **ID 공급자 인증서** 텍스트 상자에 붙여넣습니다.

    h. **이름 식별자 형식** 텍스트 상자에 `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress` 값을 입력합니다.

    i. **저장** 을 클릭합니다.

    > [!NOTE]
    > 콘텐츠가 저장되면 보안을 위해 해당 필드가 빈 상태로 표시되지만 값은 구성에 저장되었습니다.

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs 테스트 사용자 만들기

Azure AD 사용자가 Fluxx Labs에 로그인하려면 Fluxx Labs에 프로비저닝되어야 합니다. Fluxx Labs의 경우 프로비전은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Fluxx Labs 회사 사이트에 관리자 권한으로 로그인합니다.

2. 아래 표시된 **아이콘** 을 클릭합니다.

    !["대시보드가 비어 있습니다."에서 "더하기" 아이콘이 선택된 관리자 옵션을 보여주는 스크린샷.](./media/fluxxlabs-tutorial/configure-6.png)

3. 대시보드에서 아래 표시된 아이콘을 클릭하여 **새 사람** 카드를 엽니다.

    !["사람" 옆에 "더하기" 아이콘이 선택된 "연락처 관리" 메뉴를 보여주는 스크린샷.](./media/fluxxlabs-tutorial/configure-4.png)

4. **새 사람** 섹션에서 다음 단계를 수행합니다.

    ![Fluxx Labs 구성](./media/fluxxlabs-tutorial/configure-5.png)

    a. Fluxx Labs에서는 SSO 로그인에 대해 고유 식별자로 이메일을 사용합니다. **SSO UID** 필드를 사용자의 이메일 주소로 채웁니다. 해당 주소는SSO를 사용하여 로그인할 때 사용한 이메일 주소와 일치해야 합니다.

    b. **저장** 을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Fluxx Labs에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Fluxx Labs 타일을 클릭하면 SSO를 설정한 Fluxx Labs에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Fluxx Labs가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).