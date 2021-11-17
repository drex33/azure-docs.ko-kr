---
title: '자습서: Elium과 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Elium 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/27/2021
ms.author: jeedes
ms.openlocfilehash: 799496f912f27dc41944df71e68cac409ae97282
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132348904"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>자습서: Elium과 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Elium을 연결하는 방법에 대해 알아봅니다. Azure AD와 Elium을 연결하는 경우 다음을 수행할 수 있습니다.

* Elium에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Elium에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Elium SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Elium에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Elium에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.
* Elium에서 [자동화된 사용자 프로비저닝](elium-provisioning-tutorial.md)을 지원합니다.

## <a name="add-elium-from-the-gallery"></a>갤러리에서 Elium 추가

Elium과 Azure AD의 통합을 구성하려면 갤러리의 Elium을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Elium** 을 입력합니다.
1. 결과 패널에서 **Elium** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-elium"></a>Elium에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Elium에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Elium의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Elium에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Elium SSO 구성](#configure-elium-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Elium 테스트 사용자 만들기](#create-elium-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Elium에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Elium** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://<platform-domain>.elium.com/login/saml2/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<platform-domain>.elium.com/login/saml2/acs` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<platform-domain>.elium.com/login/saml2/login` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. `https://<platform-domain>.elium.com/login/saml2/metadata`에서 다운로드할 수 있는 **SP 메타데이터 파일**(이 자습서의 뒷부분에서 설명함)에서 이러한 값을 얻을 수 있습니다.

1. Elium 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Elium 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

    | Name | 원본 특성|
    | ---------------| ----------------|
    | 이메일   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | company| user.companyname|

    > [!NOTE]
    > 이러한 특성은 기본 클레임입니다. 하지만 **이메일 클레임만 필요합니다**. 또한 JIT 프로비전의 경우 이메일 클레임만 필수입니다. 다른 사용자 지정 클레임은 고객 플랫폼마다 다를 수 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Elium 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Elium에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Elium** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-elium-sso"></a>Elium SSO 구성

1. Elium 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Elium 설정** 을 클릭하면 Elium 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Elium에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Elium을 수동으로 설정하려면 새 웹 브라우저 창을 열고 Elium 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. 오른쪽 위 모서리에서 **사용자 프로필** 을 클릭한 다음, **설정** 을 선택합니다.

    ![Single Sign-On 사용자 프로필을 구성합니다.](./media/elium-tutorial/profile.png)

1. **고급** 에서 **보안** 을 선택합니다.

    ![Single Sign-On 고급을 구성합니다.](./media/elium-tutorial/security.png)

1. **SSO(Single Sign-On)** 섹션까지 아래로 스크롤하여 다음 단계를 수행합니다.

    ![Single Sign-On을 구성합니다.](./media/elium-tutorial/configuration.png)

    a. **계정에서 SAML2 인증이 작동하는지 확인** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 텍스트 상자에 붙여넣습니다.

    > [!NOTE]
    > SSO가 구성되면 언제든지 `https://<platform_domain>/login/regular/login` URL에서 기본 원격 로그인 페이지에 액세스할 수 있습니다. 

    b. **SAML2 페더레이션 사용** 확인란을 선택합니다.

    다. **JIT 프로비전** 확인란을 선택합니다.

    d. **다운로드** 단추를 클릭하여 **SP 메타데이터** 를 엽니다.

    e. **SP 메타데이터** 파일에서 **entityID** 를 검색하고, 해당 **entityID** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 텍스트 상자에 붙여넣습니다. 

    ![Single Sign-On 구성을 구성합니다.](./media/elium-tutorial/metadata.png)

    f. **SP 메타데이터** 파일에서 **AssertionConsumerService** 를 검색하고, **위치** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

    ![Single Sign-On AssertionConsumerService를 구성합니다.](./media/elium-tutorial/service.png)

    g. Azure Portal에서 다운로드한 메타데이터 파일을 메모장에 열고, 내용을 복사한 다음, **IdP 메타데이터** 텍스트 상자에 붙여넣습니다.

    h. **저장** 을 클릭합니다.

### <a name="create-elium-test-user"></a>Elium 테스트 사용자 만들기

이 섹션에서는 Elium에서 B.Simon이라는 사용자를 만듭니다. Elium은 기본적으로 사용하도록 설정되는 **Just-In-Time 프로비전** 을 지원합니다. 이 섹션에 작업 항목이 없습니다. Elium에 사용자가 아직 없는 경우 Elium에 액세스하려고 할 때 새 사용자가 만들어집니다.

Elium은 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./elium-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 
 
#### <a name="sp-initiated"></a>SP 시작:
 
* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Elium 로그온 URL로 리디렉션됩니다.  
 
* Elium 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.
 
#### <a name="idp-initiated"></a>IDP 시작:
 
* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Elium에 자동으로 로그인됩니다. 
 
Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Elium 타일을 클릭할 때 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Elium에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Elium이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
