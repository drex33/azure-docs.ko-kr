---
title: '자습서: TextMagic과 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 TextMagic 간 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/14/2021
ms.author: jeedes
ms.openlocfilehash: 09f00aaa669ca89e3b6d3b8c580f7088bf4a3e83
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132316853"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-textmagic"></a>자습서: TextMagic과 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 TextMagic을 연결하는 방법에 대해 알아봅니다. Azure AD와 TextMagic을 연결하는 경우 다음을 수행할 수 있습니다.

* TextMagic에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 TextMagic에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* TextMagic SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* TextMagic에서 **IDP** 시작 SSO를 지원합니다.

* TextMagic에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-textmagic-from-the-gallery"></a>갤러리에서 TextMagic 추가

Azure AD에 TextMagic 통합을 구성하려면 갤러리의 TextMagic을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **TextMagic** 을 입력합니다.
1. 결과 패널에서 **TextMagic** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-textmagic"></a>TextMagic에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 TextMagic에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 TextMagic의 관련 사용자 간에 연결 관계를 설정해야 합니다.

TextMagic에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[TextMagic SSO 구성](#configure-textmagic-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[TextMagic 테스트 사용자 만들기](#create-textmagic-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 TextMagic에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **TextMagic** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **식별자** 텍스트 상자에서 `https://my.textmagic.com/saml/metadata` URL을 입력합니다.

5. TextMagic 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 **nameidentifier** 가 **user.userprincipalname** 과 매핑되는 기본 특성 목록을 보여줍니다. TextMagic 애플리케이션에서는 **nameidentifier** 가 **user.mail** 과 매핑되므로 특성 매핑을 변경하기 위해 **편집** 아이콘을 클릭하여 특성 매핑을 편집해야 합니다.

    ![이미지](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도 TextMagic 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name |   원본 특성| 네임스페이스  |
    | --------------- | --------------- | --------------- |
    | company | user.companyname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | firstName | user.givenname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastName | user.surname |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | phone | user.telephonenumber |  http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **TextMagic 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 TextMagic에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **TextMagic** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-textmagic-sso"></a>TextMagic SSO 구성

1. TextMagic 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **TextMagic 설정** 을 클릭하면 TextMagic 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 TextMagic에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. TextMagic을 수동으로 설정하려면 새 웹 브라우저 창을 열고 TextMagic 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

4. 사용자 이름에서 **계정 설정** 을 선택합니다.

    ![스크린샷은 사용자에서 선택한 계정 설정을 보여줍니다.](./media/textmagic-tutorial/account.png)

5. **SSO(Single Sign-On)** 탭을 클릭하고 다음 필드를 입력합니다.  

    ![스크린샷은 설명된 값을 입력할 수 있는 Single Sign-On 탭을 보여줍니다.](./media/textmagic-tutorial/settings.png)

    a. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **ID 공급 엔터티 ID:** 텍스트 상자에 붙여넣습니다.

    b. **ID 공급 SSO URL:** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    다. **ID 공급 SLO URL:** 텍스트 상자에 Azure Portal에서 복사한 **로그아웃 URL** 값을 붙여넣습니다.

    d. Azure Portal에서 다운로드한 **base-64로 인코딩된 인증서** 를 메모장에서 열고, 콘텐츠를 클립보드에 복사한 다음, **공용 x509 인증서** 텍스트 상자에 붙여넣습니다.

    e. **저장** 을 클릭합니다.

### <a name="create-textmagic-test-user"></a>TextMagic 테스트 사용자 만들기

이 섹션에서는 TextMagic에서 B.Simon이라는 사용자를 만듭니다. TextMagic은 Just-In-Time 사용자 프로비저닝을 지원하며, 기본적으로 사용하도록 설정됩니다. 이 섹션에는 작업 항목이 없습니다. TextMagic에 사용자가 없는 경우 인증 후 새로 사용자를 만듭니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 TextMagic에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 TextMagic 타일을 클릭하면 SSO를 설정한 TextMagic에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

TextMagic이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
