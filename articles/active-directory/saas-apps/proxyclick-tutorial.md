---
title: '자습서: Proxyclick과 Azure Active Directory 통합 | Microsoft Docs'
description: 이 자습서에서는 Azure Active Directory 및 Proxyclick 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/27/2021
ms.author: jeedes
ms.openlocfilehash: 31f4390541020a7315d9168369898dff1cd854f7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727476"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>자습서: Proxyclick과 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Proxyclick을 통합하는 방법에 대해 알아봅니다. Azure AD와 Proxyclick을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Proxyclick에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 Azure AD 계정으로 Proxyclick에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Proxyclick SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Proxyclick에서 SP 및 IdP 시작 SSO를 지원합니다.

## <a name="add-proxyclick-from-the-gallery"></a>갤러리에서 Proxyclick 추가

Azure AD에 Proxyclick을 통합하도록 구성하려면 갤러리의 Proxyclick을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Proxyclick** 을 입력합니다.
1. 결과 패널에서 **Proxyclick** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-proxyclick"></a>Proxyclick에서 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Proxyclick에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Proxyclick의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Proxyclick에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Proxyclick SSO 구성](#configure-proxyclick-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Proxyclick 테스트 사용자 만들기](#create-proxyclick-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Proxyclick에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Proxyclick** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 대화 상자에서 IdP 시작 모드로 애플리케이션을 구성하려면 다음 단계를 수행합니다.
    
    a. **식별자** 텍스트 상자에서 `https://saml.proxyclick.com/init/<COMPANY_ID>` 패턴을 사용하는 URL을 입력합니다.

    b. **회신 URL** 상자에 `https://saml.proxyclick.com/consume/<COMPANY_ID>` 패턴을 사용하여 URL을 입력합니다.

5. SP 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 선택합니다. **로그온 URL** 텍스트 상자에서 다음 패턴을 사용하여 URL을 입력합니다.
   
   `https://saml.proxyclick.com/init/<COMPANY_ID>`

    > [!NOTE]
    > 이러한 값은 자리 표시자입니다. 실제 식별자, 회신 URL, 로그온 URL을 사용해야 합니다. 이러한 값을 가져오기 위한 단계는 이 자습서의 뒷부분에서 설명합니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 요구 사항에 따라 **인증서(Base64)** 옆의 **다운로드** 링크를 선택하고 해당 인증서를 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **Proxyclick 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@companydomain.extension을 입력합니다. `B.Simon@contoso.com`)을 입력합니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Proxyclick에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Proxyclick** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-proxyclick-sso"></a>Proxyclick SSO 구성

1. 새 웹 브라우저 창에서 Proxyclick 회사 사이트에 관리자 권한으로 로그인합니다.

2. **계정 및 설정** 을 선택합니다.

    ![계정 및 설정 선택](./media/proxyclick-tutorial/account.png)

3. **통합** 섹션까지 아래로 스크롤하여 **SAML** 을 선택합니다.

    ![SAML 선택](./media/proxyclick-tutorial/settings.png)

4. **SAML** 섹션에서 다음 단계를 수행합니다.

    ![SAML 섹션](./media/proxyclick-tutorial/configuration.png)

    1. **SAML Consumer URL**(SAML 소비자 URL) 값을 복사하여 Azure Portal의 **기본 SAML 구성** 대화 상자에 있는 **회신 URL** 상자에 붙여넣습니다.

    1. **SAML SSO Redirect URL**(SAML SSO 리디렉션 URL) 값을 복사하여 Azure Portal의 **기본 SAML 구성** 대화 상자에 있는 **로그온 URL** 및 **식별자** 상자에 붙여넣습니다.

    1. **SAML Request Method**(SAML 요청 메서드) 목록에서 **HTTP Redirect**(HTTP 리디렉션)를 선택합니다.

    1. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **Issuer**(발급자) 상자에 붙여넣습니다.

    1. Azure Portal에서 복사한 **로그인 URL** 을 **SAML 2.0 Endpoint URL**(SAML 2.0 엔드포인트 URL) 상자에 붙여넣습니다.

    1. Azure Portal에서 다운로드한 인증서 파일을 메모장에서 엽니다. 이 파일의 내용을 **Certificate**(인증서) 상자에 붙여넣습니다.

    1. **변경 내용 저장** 을 선택합니다.

### <a name="create-proxyclick-test-user"></a>Proxyclick 테스트 사용자 만들기

Azure AD 사용자가 Proxyclick에 로그인할 수 있도록 설정하려면 해당 사용자를 Proxyclick에 추가해야 합니다. 수동으로 추가해야 합니다.

사용자 계정을 만들려면 다음 단계를 수행합니다.

1. Proxyclick 회사 사이트에 관리자 권한으로 로그인합니다.

1. 창 위쪽에서 **Colleagues**(동료)를 선택합니다.

    ![Colleagues(동료) 선택](./media/proxyclick-tutorial/user.png)

1. **Add Colleague**(동료 추가)를 선택합니다.

    ![Add Colleague(동료 추가) 선택](./media/proxyclick-tutorial/add-user.png)

1. **Add a colleague**(동료 추가) 섹션에서 다음 단계를 수행합니다.

    ![Add a colleague(동료 추가) 섹션](./media/proxyclick-tutorial/create-user.png)

    1. **이메일** 상자에서 사용자의 이메일 주소를 입력합니다. 여기서는 **brittasimon\@contoso.com** 입니다.

    1. **First Name**(이름) 텍스트 상자에서 사용자의 이름을 입력합니다. 여기서는 **Britta** 입니다.

    1. **Last Name**(성) 텍스트 상자에서 사용자의 성을 입력합니다. 여기서는 **Simon** 입니다.

    1. **사용자 추가** 를 선택합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Proxyclick 로그온 URL로 리디렉션됩니다.  

* Proxyclick 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Proxyclick에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Proxyclick 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Proxyclick에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Proxyclick이 구성되면 세션 제어를 적용하여 조직에서 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).