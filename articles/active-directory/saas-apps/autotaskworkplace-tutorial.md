---
title: '자습서: Autotask Workplace와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Autotask Workplace 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/02/2021
ms.author: jeedes
ms.openlocfilehash: 19b1708be1bda3f381262a826fa58b0517ffe245
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124822837"
---
# <a name="tutorial-azure-active-directory-integration-with-autotask-workplace"></a>자습서: Autotask Workplace와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Autotask Workplace를 통합하는 방법에 대해 알아봅니다. Azure AD와 Autotask Workplace를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Autotask Workplace에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Autotask Workplace에 자동으로 로그인되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Autotask Workplace SSO(Single Sign-On)가 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Autotask Workplace에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-autotask-workplace-from-the-gallery"></a>갤러리에서 Autotask Workplace 추가

Autotask Workplace의 Azure AD 통합을 구성하려면 갤러리의 Autotask Workplace를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Autotask Workplace** 를 입력합니다.
1. 결과 패널에서 **Autotask Workplace** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-autotask-workplace"></a>Autotask Workplace에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Autotask Workplace에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Autotask Workplace의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Autotask Workplace에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Autotask Workplace SSO 구성](#configure-autotask-workplace-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Autotask Workplace 테스트 사용자 만들기](#create-autotask-workplace-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Autotask Workplace에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Autotask Workplace** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    ![스크린샷은 식별자, 회신 URL을 입력하고 저장을 선택할 수 있는 기본 SAML 구성을 보여줍니다.](common/idp-intiated.png)

    a. **식별자** 텍스트 상자에서 `https://<subdomain>.awp.autotask.net/singlesignon/saml/metadata` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<subdomain>.awp.autotask.net/singlesignon/saml/SSO` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    ![스크린샷은 로그온 URL을 입력할 수 있는 추가 URL 설정을 보여줍니다.](common/metadata-upload-additional-signon.png)

    **로그인 URL** 텍스트 상자에서 `https://<subdomain>.awp.autotask.net/loginsso` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Autotask Workplace 클라이언트 지원팀](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Autotask Workplace 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Autotask Workplace에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Autotask Workplace** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-autotask-workplace-sso"></a>Autotask Workplace SSO 구성

1. 다른 웹 브라우저 창에서 관리자 자격 증명을 사용하여 Workplace Online에 로그인합니다.

    > [!Note]
    > IdP를 구성할 때 하위 도메인을 지정해야 합니다. 올바른 하위 도메인을 확인하려면 Workplace Online에 로그인합니다. 로그인되면 URL에서 하위 도메인을 만듭니다. 하위 도메인은“https://“ 와 “.awp.autotask.net/“ 사이의 부분이며 us, eu, ca 또는 au여야 합니다.

2. **구성** > **Single Sign-On** 으로 이동하고 다음 단계를 수행합니다.

    ![Autotask Single Sign-On 구성](./media/autotaskworkplace-tutorial/configuration-1.png)

    a. **XML 메타데이터 파일** 옵션을 선택한 다음, Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 을 업로드합니다.

    b. **SSO 사용** 을 클릭합니다.

    ![Autotask Single Sign-On 승인 구성](./media/autotaskworkplace-tutorial/configuration-2.png)

    다. **I confirm this information is correct and I trust this IdP(이 정보가 정확한지 확인하고, 이 IdP를 신뢰합니다)** 확인란을 선택합니다.

    d. **승인** 을 클릭합니다.

> [!Note]
> Autotask Workplace 구성에 대한 도움이 필요한 경우 [이 페이지](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)를 참조하여 Workplace 계정으로 지원을 받을 수 있습니다.

### <a name="create-autotask-workplace-test-user"></a>Autotask Workplace 테스트 사용자 만들기

이 섹션에서는 Autotask Workplace에서 Britta Simon이라는 사용자를 만듭니다. [Autotask Workplace 지원팀](https://awp.autotask.net/help/Content/0_HOME/Support_for_End_Clients.htm)에 문의하여 Autotask Workplace 플랫폼에 사용자를 추가하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Autotask Workplace 로그온 URL로 리디렉션됩니다.  

* Autotask Workplace 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Autotask Workplace에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Autotask Workplace 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Autotask Workplace에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Autotask Workplace를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).