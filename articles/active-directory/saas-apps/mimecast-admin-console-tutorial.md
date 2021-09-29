---
title: '자습서: Mimecast 관리 콘솔과 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Mimecast 관리 콘솔 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/15/2021
ms.author: jeedes
ms.openlocfilehash: 79b583f51349378a40512e9e79763ec3ae3a192f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124795339"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mimecast-admin-console"></a>자습서: Mimecast Admin Console과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Mimecast Admin Console을 통합하는 방법에 대해 알아봅니다. Azure AD와 Mimecast Admin Console을 통합하면 다음을 수행할 수 있습니다.

* Mimecast Admin Console에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Mimecast Admin Console에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Mimecast Admin Console SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Mimecast Admin Console은 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-mimecast-admin-console-from-the-gallery"></a>갤러리에서 Mimecast Admin Console 추가

Mimecast 관리 콘솔이 Azure AD에 통합되도록 구성하려면 갤러리에서 Mimecast 관리 콘솔을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Mimecast Admin Console** 을 입력합니다.
1. 결과 패널에서 **Mimecast Admin Console** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-mimecast-admin-console"></a>Mimecast Admin Console에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Mimecast Admin Console에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Mimecast Admin Console의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Mimecast Admin Console에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Mimecast Admin Console SSO 구성](#configure-mimecast-admin-console-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Mimecast Admin Console 테스트 사용자 만들기](#create-mimecast-admin-console-test-user)** - Azure AD 표현과 연결된 B.Simon에 해당하는 사용자를 Mimecast Admin Console에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Mimecast Admin Console** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. IDP 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 다음 패턴 을 사용하여 URL을 입력합니다.

    | 지역  |  값 | 
    | --------------- | --------------- |
    | 유럽          | `https://eu-api.mimecast.com/sso/<accountcode>`|
    | 미국   | `https://us-api.mimecast.com/sso/<accountcode>`|
    | 남아프리카    | `https://za-api.mimecast.com/sso/<accountcode>`|
    | 오스트레일리아       | `https://au-api.mimecast.com/sso/<accountcode>`|
    | 역외        | `https://jer-api.mimecast.com/sso/<accountcode>`|

    > [!NOTE]
    > **계정** > **설정** > **계정 코드** 에서 Mimecast Admin Console의 `accountcode` 값을 확인할 수 있습니다. 식별자에 `accountcode`를 추가합니다.

    b. **회신 URL** 텍스트 상자에 URL 를 입력합니다. 

    | 지역  |  값 | 
    | --------------- | --------------- | 
    | 유럽          | `https://eu-api.mimecast.com/login/saml`|
    | 미국   | `https://us-api.mimecast.com/login/saml`|
    | 남아프리카    | `https://za-api.mimecast.com/login/saml`|
    | 오스트레일리아       | `https://au-api.mimecast.com/login/saml`|
    | 역외        | `https://jer-api.mimecast.com/login/saml`|

1. **SP** 시작 모드에서 애플리케이션을 구성하려면:

    **로그온 URL** 텍스트 상자에서 URL을 입력합니다. 

    | 지역  |  값 | 
    | --------------- | --------------- | 
    | 유럽          | `https://login-eu.mimecast.com/administration/app/#/administration-dashboard`|
    | 미국   | `https://login-us.mimecast.com/administration/app/#/administration-dashboard`|
    | 남아프리카    | `https://login-za.mimecast.com/administration/app/#/administration-dashboard`|
    | 오스트레일리아       | `https://login-au.mimecast.com/administration/app/#/administration-dashboard`|
    | 역외        | `https://login-jer.mimecast.com/administration/app/#/administration-dashboard`|

1. **저장** 을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/copy-metadataurl.png)

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 Mimecast Admin Console에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Mimecast 관리 콘솔** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-mimecast-admin-console-sso"></a>Mimecast Admin Console SSO 구성

1. 다른 웹 브라우저 창에서 Mimecast Administration Console에 로그인합니다.

1. **관리** > **서비스** > **애플리케이션** 으로 이동합니다.

    ![스크린샷은 애플리케이션이 선택된 Mimecast 창을 보여줍니다.](./media/mimecast-admin-console-tutorial/services.png)

1. **인증 프로필** 탭을 클릭합니다.
    
    ![스크린샷은 인증 프로필이 선택된 애플리케이션 탭을 보여줍니다.](./media/mimecast-admin-console-tutorial/authentication-profiles.png)

1. **새 인증 프로필** 탭을 클릭합니다.

    ![스크린샷은 선택된 새 인증 프로필을 보여줍니다.](./media/mimecast-admin-console-tutorial/new-authenticatio-profile.png)

1. **설명** 텍스트 상자에 올바른 설명을 입력하고 **Administration Console에 SAML 인증 적용** 확인란을 선택합니다.

    ![스크린샷은 관리 콘솔에 SAML 인증 적용을 선택하는 위치를 보여줍니다.](./media/mimecast-admin-console-tutorial/selecting-admin-consle.png)

1. **Administration Console의 SAML 구성** 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 관리 콘솔에 대한 SAML 구성 페이지를 보여줍니다.](./media/mimecast-admin-console-tutorial/sso-settings.png)

    a. 드롭다운 목록에서 **공급자** 에 **Azure Active Directory** 를 선택합니다.

    b. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL** 텍스트 상자에 붙여넣습니다.

    다. **가져오기** 를 클릭합니다. 메타데이터 URL을 가져오면 필드가 자동으로 채워지며 이러한 필드에 대해 아무 작업도 수행할 필요가 없습니다.

    d. **암호로 보호된 컨텍스트 사용** 및 **통합 인증 컨텍스트 사용** 확인란의 선택을 해제합니다.

    e. **저장** 을 클릭합니다.

### <a name="create-mimecast-admin-console-test-user"></a>Mimecast 관리 콘솔 테스트 사용자 만들기

1. 다른 웹 브라우저 창에서 Mimecast Administration Console에 로그인합니다.

1. **관리** > **디렉터리** > **내부 디렉터리** 로 이동합니다.

    ![스크린샷은 내부 디렉터리가 선택된 Mimecast 창을 보여줍니다.](./media/mimecast-admin-console-tutorial/internal-directories.png)

1. 아래에 도메인이 언급된 경우 도메인을 선택하고, 그렇지 않은 경우 **새 도메인** 을 클릭하여 새 도메인을 만듭니다.

    ![스크린샷은 선택된 도메인을 보여줍니다.](./media/mimecast-admin-console-tutorial/domain-name.png)

1. **새 주소** 탭을 클릭합니다.

    ![스크린샷은 선택된 새 주소를 보여줍니다.](./media/mimecast-admin-console-tutorial/new-address.png)

1. 다음 페이지에 필수 사용자 정보를 제공합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 페이지를 보여줍니다.](./media/mimecast-admin-console-tutorial/user-information.png)

    a. **이메일 주소** 텍스트 상자에 사용자의 이메일 주소(예: `B.Simon@yourdomainname.com`)를 입력합니다.

    b. **글로벌 이름** 텍스트 상자에 사용자의 **전체 이름** 을 입력합니다.

    다. **암호** 및 **암호 확인** 텍스트 상자에 사용자의 암호를 입력합니다.

    d. **로그인할 때 변경 적용** 확인란을 선택합니다.

    e. **저장** 을 클릭합니다.

    f. 사용자에게 역할을 할당하려면 **역할 편집** 을 클릭하고 조직 요구 사항에 따라 사용자에게 필요한 역할을 할당합니다.

    ![스크린샷은 역할 편집을 선택할 수 있는 주소 설정을 보여줍니다.](./media/mimecast-admin-console-tutorial/assign-role.png)

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Mimecast Admin Console 로그온 URL로 리디렉션됩니다.  

* Mimecast Admin Console 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Mimecast Admin Console에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Mimecast Admin Console 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Mimecast Admin Console에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Mimecast Admin Console이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).