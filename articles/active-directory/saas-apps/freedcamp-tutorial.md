---
title: '자습서: Freedcamp와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Freedcamp 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2021
ms.author: jeedes
ms.openlocfilehash: 88f942d48e99e33d72e2c207cd8cd48e10208057
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747889"
---
# <a name="tutorial-integrate-freedcamp-with-azure-active-directory"></a>자습서: Freedcamp와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Freedcamp를 통합하는 방법에 대해 알아봅니다. Azure AD와 Freedcamp를 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD에서는 Freedcamp에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Freedcamp에 자동으로 로그온(Single Sign-On)되도록 설정할 수 있습니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Freedcamp SSO(Single Sign-On)가 설정된 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Freedcamp에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-freedcamp-from-the-gallery"></a>갤러리에서 Freedcamp 추가

Freedcamp의 Azure AD 통합을 구성하려면 갤러리의 Freedcamp를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Freedcamp** 를 입력합니다.
1. 결과 패널에서 **Freedcamp** 를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-freedcamp"></a>Freedcamp에 대한 Azure AD SSO 구성 및 테스트

테스트 사용자 **Britta Simon** 을 사용하여 Freedcamp로 Azure AD SSO를 구성 및 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Freedcamp의 관련 사용자 간에 연결이 형성되어야 합니다.

Freedcamp에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Freedcamp SSO 구성](#configure-freedcamp-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Freedcamp 테스트 사용자 만들기](#create-freedcamp-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Freedcamp에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Freedcamp** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    1. **식별자** 텍스트 상자에서 `https://<SUBDOMAIN>.freedcamp.com/sso/<UNIQUEID>` 패턴을 사용하여 URL을 입력합니다.

    2. **회신 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.freedcamp.com/sso/acs/<UNIQUEID>` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.freedcamp.com/login` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 또한 사용자는 고유한 고객 도메인과 관련된 URL 값을 입력할 수 있고, 반드시 `freedcamp.com` 패턴을 사용할 필요는 없으며 애플리케이션 인스턴스에 고유한 고객 도메인 특정 값을 입력할 수도 있습니다. 그뿐 아니라 [Freedcamp 클라이언트 지원 팀](mailto:devops@freedcamp.com)에 URL 패턴에 대한 추가 정보를 문의할 수 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Freedcamp 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 Britta Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `Britta Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `BrittaSimon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 Britta Simon에게 Freedcamp에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Freedcamp** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **Britta Simon** 을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-freedcamp-sso"></a>Freedcamp SSO 구성

1. Freedcamp 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Freedcamp 설정** 을 클릭하면 Freedcamp 애플리케이션으로 이동됩니다. 애플리케이션에서 관리자 자격 증명을 입력하여 Freedcamp에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

3. Freedcamp를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Freedcamp 회사 사이트에 관리자로 로그인한 후에 다음 단계를 수행합니다.

4. 페이지의 오른쪽 위 모서리에서 **프로필** 을 클릭하고 **내 계정** 으로 이동합니다.

    ![선택된 "프로필" 및 "내 계정"을 보여주는 스크린샷.](./media/freedcamp-tutorial/config01.png)

5. 왼쪽 메뉴 모음에서 **SSO** 를 클릭하고 **SSO 연결** 페이지에서 다음 단계를 수행합니다.

    ![왼쪽 메뉴 모음에서 선택된 "SSO"와 값이 입력되고 "제출" 단추가 선택된 "사용자 SSO 연결" 페이지를 보여주는 스크린샷.](./media/freedcamp-tutorial/config02.png)

    a. **제목** 텍스트 상자에 제목을 입력합니다.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    다. **로그인 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    d. 메모장에서 Base64로 인코딩된 인증서를 열고 내용을 복사한 다음, **인증서** 텍스트 상자에 붙여넣습니다.

    e. **제출** 을 클릭합니다.

### <a name="create-freedcamp-test-user"></a>Freedcamp 테스트 사용자 만들기

Azure AD 사용자가 Freedcamp에 로그인할 수 있으려면 해당 사용자가 Freedcamp에 프로비저닝되어야 합니다. Freedcamp에서는 프로비저닝이 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. 다른 웹 브라우저 창에서 Freedcamp에 보안 관리자로 로그인합니다.

2. 페이지의 오른쪽 위 모서리에서 **프로필** 을 클릭한 다음, **시스템 관리** 로 이동합니다.

    ![Freedcamp 구성](./media/freedcamp-tutorial/config03.png)

3. 시스템 관리 페이지의 오른쪽에서 다음 단계를 수행합니다.

    !["사용자 추가 또는 초대" 단추가 선택되고, "이메일" 필드가 강조 표시되고, "사용자 추가" 단추가 선택된 것을 보여주는 스크린샷.](./media/freedcamp-tutorial/config04.png)

    a. **사용자 추가 또는 초대** 를 클릭합니다.

    b. **메일** 텍스트 상자에 사용자의 메일(예: `Brittasimon@contoso.com`)을 입력합니다.

    다. **사용자 추가** 를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Freedcamp 로그온 URL로 리디렉션됩니다.  

* Freedcamp 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Freedcamp에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Freedcamp 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Freedcamp에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Freedcamp가 구성되면 세션 제어를 적용하여 조직에서 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).