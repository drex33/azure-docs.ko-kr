---
title: '자습서: Azure Active Directory와 Ziflow 통합| Microsoft Docs'
description: Azure Active Directory와 Ziflow 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2021
ms.author: jeedes
ms.openlocfilehash: eccd83b7c29ec31c40271efc09d5318d35ba6d2a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764909"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>자습서: Ziflow와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Ziflow를 통합하는 방법에 대해 알아봅니다. Azure AD와 Ziflow를 통합하면 다음을 수행할 수 있습니다.

* Ziflow에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Ziflow에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Ziflow와 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Ziflow Single Sign-On이 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Ziflow에서 **SP** 시작 SSO를 지원합니다.

## <a name="add-ziflow-from-the-gallery"></a>갤러리에서 Ziflow 추가

Ziflow의 Azure AD 통합을 구성하려면 갤러리의 Ziflow를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Ziflow** 를 입력합니다.
1. 결과 패널에서 **Ziflow** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-ziflow"></a>Ziflow에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Ziflow에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Ziflow의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Ziflow에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Ziflow SSO 구성](#configure-ziflow-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Ziflow 테스트 사용자 만들기](#create-ziflow-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Ziflow에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Ziflow** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자(엔터티 ID)** 텍스트 상자에서 `urn:auth0:ziflow-production:<UNIQUE_ID>` 패턴을 사용하는 값을 입력합니다.

    b. **로그온 URL** 텍스트 상자에서 `https://ziflow-production.auth0.com/login/callback?connection=<UNIQUE_ID>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 위의 값은 실제가 아닙니다. 식별자 및 로그온 URL의 고유한 ID 값을 자습서 뒷부분에 설명된 실제 값으로 업데이트하게 됩니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Ziflow 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Ziflow에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Ziflow** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-ziflow-sso"></a>Ziflow SSO 구성

1. 다른 웹 브라우저 창에서 Ziflow에 보안 관리자로 로그인합니다.

2. 오른쪽 위 모서리에서 Avatar를 클릭한 다음 **계정 관리** 를 클릭합니다.

    ![Ziflow 구성 관리](./media/ziflow-tutorial/manage-account.png)

3. 왼쪽 위에서 **Single Sign On** 을 클릭합니다.

    ![Ziflow 구성 기호](./media/ziflow-tutorial/configuration.png)

4. **Single Sign-On** 페이지에서 다음 단계를 수행합니다.

    ![Ziflow 구성 단일](./media/ziflow-tutorial/page.png)

    a. **유형** 으로 **SAML2.0** 을 선택합니다.

    b. Azure Portal에서 복사한 **로그인 URL** 값을 **로그인 URL** 텍스트 상자에 붙여넣습니다.

    다. Azure Portal에서 다운로드한 base 64 인코딩 인증서를 **X509 서명 인증서** 에 업로드합니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **로그아웃 URL** 텍스트 상자에 붙여넣습니다.

    e. **식별자 공급자에 대한 구성 설정** 섹션에서 강조 표시된 고유 ID 값을 복사하고, 식별자 및 로그온 URL과 함께 Azure Portal의 **기본 SAML 구성** 에 추가합니다.

### <a name="create-ziflow-test-user"></a>Ziflow 테스트 사용자 만들기

Azure AD 사용자가 Ziflow에 로그인할 수 있도록 하려면 Ziflow로 프로비전되어야 합니다. Ziflow의 경우, 수동으로 프로비전합니다.

사용자 계정을 프로비저닝하려면 다음 단계를 수행합니다.

1. 보안 관리자 권한으로 Ziflow에 로그인합니다.

2. 맨 위의 **사람** 으로 이동합니다.

    ![Ziflow 구성 사람](./media/ziflow-tutorial/people.png)

3. **추가** 를 클릭한 후 **사용자 추가** 를 클릭합니다.

    ![스크린샷은 선택된 사용자 추가 옵션을 보여줍니다.](./media/ziflow-tutorial/add-tab.png)

4. **사용자 추가** 팝업에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 사용자 추가 대화 상자를 보여줍니다.](./media/ziflow-tutorial/add-user.png)

    a. **전자 메일** 텍스트 상자에 사용자의 전자 메일(예: brittasimon@contoso.com)을 입력합니다.

    b. **이름** 텍스트 상자에 사용자의 이름(예: Britta)을 입력합니다.

    다. **성** 텍스트 상자에 사용자의 성(예: Simon)을 입력합니다.

    d. Ziflow 역할을 선택합니다.

    e. **사용자 1명 추가** 를 클릭합니다.

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Ziflow 로그온 URL로 리디렉션됩니다. 

* Ziflow 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Ziflow 타일을 클릭하면 Ziflow 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Ziflow를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).