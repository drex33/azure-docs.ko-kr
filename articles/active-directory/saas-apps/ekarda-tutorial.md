---
title: '자습서: ekarda와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 ekarda 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/24/2020
ms.author: jeedes
ms.openlocfilehash: e5b0abc3cd6aa0850f48bced115f0b43d326b081
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132285976"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>자습서: ekarda와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 ekarda를 통합하는 방법에 대해 알아봅니다. Azure AD와 ekarda를 통합하면 다음을 수행할 수 있습니다.

* ekarda에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정을 사용하여 ekarda에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)에 대해 ekarda 구독을 사용하도록 설정했습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* ekarda에서 SP 및 IDP 시작 SSO를 지원합니다.
* ekarda에서 Just-In-Time 사용자 프로비저닝을 지원합니다.

## <a name="add-ekarda-from-the-gallery"></a>갤러리에서 ekarda 추가

ekarda가 Azure AD에 통합되도록 구성하려면 갤러리의 ekarda를 관리형 SaaS 앱 목록에 추가합니다.

1. 회사 계정, 학교 계정 또는 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.

1. 왼쪽 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **ekarda** 를 입력합니다.
1. 결과 패널에서 **ekarda** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-ekarda"></a>ekarda에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 ekarda에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 ekarda의 관련 사용자 간에 연결 관계를 설정해야 합니다.

ekarda에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.

    1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [ekarda SSO 구성](#configure-ekarda-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    * [ekarda 테스트 사용자 만들기](#create-an-ekarda-test-user) - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 ekarda에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 다음 단계를 수행하여 Azure AD SSO를 사용하도록 설정합니다.

1. Azure Portal에 로그인합니다.
1. **ekarda** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 연필 아이콘을 선택하여 **기본 SAML 구성** 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정 페이지의 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 **서비스 공급자 메타데이터 파일** 이 있는 경우, 다음 단계를 따릅니다.
    1. **메타데이터 파일 업로드** 를 선택합니다.
    1. 폴더 아이콘을 선택하여 메타데이터 파일을 선택한 다음, **업로드** 를 선택합니다.
    1. 메타데이터 파일이 성공적으로 업로드되면 ekarda 섹션 텍스트 상자에서 **식별자** 및 **회신 URL** 값이 자동으로 나타납니다.

    > [!Note]
    > **식별자** 및 **회신 URL** 값이 자동으로 나타나지 않으면 요구 사항에 따라 값을 수동으로 입력합니다.

1. **서비스 공급자 메타데이터 파일** 이 없는 경우 IDP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    1. **식별자** 텍스트 상자에서 다음과 같은 패턴을 따르는 URL을 입력합니다. `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. **회신 URL** 텍스트 상자에서 다음과 같은 패턴을 따르는 URL을 입력합니다. `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 선택하고 다음을 수행합니다.

    **로그온 URL** 텍스트 상자에서 다음(`https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`)과 같은 패턴을 따르는 URL을 입력합니다.

    > [!NOTE]
    > 위의 두 단계에 있는 값은 실제 값이 아닙니다. 이러한 값을 실제 식별자, 회신 URL 및 로그온 URL 값으로 업데이트합니다. 이러한 값을 얻으려면 [ekarda 클라이언트 지원 팀](mailto:contact@ekarda.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 선택하여 **인증서(Base64)** 를 컴퓨터에 저장합니다.

    ![Base64 인증서에 대해 다운로드 링크가 강조 표시된 SAML로 Single Sign-On 설정 페이지의 SAML 서명 인증서 섹션에 대한 스크린샷](common/certificatebase64.png)

1. **ekarda 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![URL 복사 링크가 강조 표시된 SAML로 Single Sign-On 설정 페이지의 ekarda 설정 섹션의 스크린샷](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal을 사용하여 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory** > **사용자** > **모든 사용자** 를 차례로 선택합니다.

1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. 예를 들어 다음과 같이 입력합니다. `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택하고, **암호** 상자에 표시되는 값을 적어 둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 ekarda에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** > **모든 애플리케이션** 을 차례로 선택합니다.
1. 애플리케이션 목록에서 **ekarda** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.

1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택합니다. 그런 다음, 화면 하단에서 **선택** 을 선택합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 을 선택합니다.

## <a name="configure-ekarda-sso"></a>ekarda SSO 구성

1. ekarda 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **ekarda 설정** 을 클릭하면 ekarda 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 ekarda에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. ekarda를 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 ekarda 회사 사이트에 로그인합니다.

1. **관리자** > **내 계정** 을 선택합니다.

    ![관리자 메뉴에서 내 계정이 강조 표시된 ekarda 사이트 UI의 스크린샷](./media/ekarda-tutorial/ekarda.png)

1. 페이지 맨 아래에서 **SAML 설정** 섹션을 찾습니다. 이 섹션에서는 SAML 통합을 구성합니다.
1. **SAML 설정** 섹션에서 다음 단계를 수행합니다.

    ![SAML 구성 필드가 강조 표시된 ekarda SAML 설정 페이지의 스크린샷](./media/ekarda-tutorial/ekarda1.png)

    1. **서비스 공급자 메타데이터** 링크를 선택하여 컴퓨터에 파일로 저장합니다.
    1. **SAML 사용** 확인란을 선택합니다.
    1. 이전에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IDP 엔터티 ID** 텍스트 상자에 붙여넣습니다.
    1. 이전에 Azure Portal에서 복사한 **로그인 URL** 값을 **IDP 로그인 URL** 텍스트 상자에 붙여넣습니다.
    1. 이전에 Azure Portal에서 복사한 **로그아웃 URL** 값을 **IDP 로그아웃 URL** 텍스트 상자에 붙여넣습니다.
    1. 메모장을 사용하여 Azure Portal에서 다운로드한 **인증서(Base64)** 파일을 엽니다. 해당 콘텐츠를 **IDP x509 인증서** 텍스트 상자에 붙여넣습니다.
    1. **옵션** 섹션에서 **SLO 사용** 확인란을 선택합니다.
    1. **업데이트** 를 선택합니다.

### <a name="create-an-ekarda-test-user"></a>ekarda 테스트 사용자 만들기

이 섹션에서는 ekarda에서 B.Simon이라는 사용자를 만듭니다. ekarda는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에는 수행할 작업이 없습니다. B.Simon이라는 사용자가 ekarda에 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 ekarda 로그온 URL로 리디렉션됩니다.

* ekarda 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 ekarda에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 ekarda 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 ekarda에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

ekarda가 구성되면 세션 제어를 적용할 수 있습니다. 이 예방 조치는 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호합니다. 세션 제어는 조건부 액세스 앱 제어에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
