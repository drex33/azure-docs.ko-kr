---
title: '자습서: DocuSign과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 DocuSign 간에 SSO(Single Sign-On)을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 03/26/2021
ms.author: jeedes
ms.openlocfilehash: 1cf1da4b583e78ebf9e5cf9e17da4674d9401388
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770247"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>자습서: DocuSign과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Microsoft Azure AD(Azure Active Directory)와 DocuSign을 통합하는 방법에 대해 알아봅니다. Azure AD와 DocuSign을 통합하는 경우 다음을 수행할 수 있습니다.

* Azure AD를 사용하여 DocuSign에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정을 통해 DocuSign에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SSO(Single Sign-On)를 사용하도록 설정된 DocuSign 구독

> [!NOTE]
> 이 통합은 Azure AD 미국 정부 클라우드 환경에서도 사용할 수 있습니다. 이 애플리케이션은 Azure AD 미국 정부 클라우드 애플리케이션 갤러리에서 찾을 수 있으며 퍼블릭 클라우드에서와 동일한 방법으로 구성할 수 있습니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트하여 다음을 확인합니다.

* DocuSign에서 **SP** 시작 SSO를 지원합니다.

* DocuSign에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

* DocuSign에서 [자동 사용자 프로비저닝](./docusign-provisioning-tutorial.md)을 지원합니다.

## <a name="adding-docusign-from-the-gallery"></a>갤러리에서 DocuSign 추가

DocuSign이 Azure AD에 통합되도록 구성하려면 갤러리에서 DocuSign을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. 회사 또는 학교 계정을 사용하거나 개인 Microsoft 계정을 사용하여 Azure Portal에 로그인합니다.
1. 왼쪽의 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **DocuSign** 을 입력합니다.
1. 결과 패널에서 **DocuSign** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-docusign"></a>DocuSign에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 DocuSign에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 DocuSign의 해당 사용자 간에 연결 관계를 설정해야 합니다.

DocuSign에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
    1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [DocuSign SSO 구성](#configure-docusign-sso) - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. [DocuSign 테스트 사용자 만들기](#create-docusign-test-user) - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 DocuSign에 만듭니다.
1. [SSO 테스트](#test-sso)는 구성이 작동하는지 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **DocuSign** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 다음 패턴을 사용하는 URL을 입력합니다.

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. **식별자(엔터티 ID)** 텍스트 상자에서 다음 패턴을 사용하는 URL을 입력합니다.

    `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    다. **회신 URL** 텍스트 상자에 다음 URL 패턴 중 하나를 입력합니다.
    
    | 회신 URL |
    |-------------|
    | 프로덕션: |
    | `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/<IDPID>` |
    | `https://<subdomain>.docusign.net/SAML/` |
    | QA 인스턴스:|
    | `https://<SUBDOMAIN>.docusign.com/organizations/saml2` |

    > [!NOTE]
    > 대괄호로 묶은 이러한 값은 자리 표시자입니다. 실제 로그온 URL, 식별자 및 회신 URL의 값으로 바꿉니다. 이러한 세부 정보는 이 자습서의 뒷부분에 나오는 "SAML 2.0 엔드포인트 보기" 섹션에 설명되어 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾습니다. **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **DocuSign 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B. Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에서 **B.Simon** 을 입력합니다.  
   1. **사용자 이름** 필드에서 `<username>@<companydomain>.<extension>`을 입력합니다. 예: `B.Simon@contoso.com`
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어 둡니다.
   1. **만들기** 를 선택합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 이 사용자가 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 DocuSign에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **DocuSign** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고, **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 **사용자** 목록에서 **B.Simon** 을 선택한 다음, 화면의 아래쪽에서 **선택** 단추를 누릅니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 선택합니다.

## <a name="configure-docusign-sso"></a>DocuSign SSO 구성

1. DocuSign에서 구성을 자동화하려면 **확장 설치** 를 선택하여 내 앱 보안 로그인 브라우저 확장을 설치합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

2. 확장이 브라우저에 추가되면 **DocuSign 설정** 을 선택합니다. DocuSign 애플리케이션으로 이동합니다. 여기서는 DocuSign에 로그인하는 데 필요한 관리자 자격 증명을 제공합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3-5단계를 자동화합니다.

    ![설정 구성](common/setup-sso.png)

3. DocuSign을 수동으로 설정하려면 새 웹 브라우저 창을 열고 DocuSign 회사 사이트에 관리자 권한으로 로그인합니다.

4. 페이지의 오른쪽 위 모서리에서 프로필 로고를 선택한 다음, **Go to Admin**(관리로 이동)을 선택합니다.
  
    ![Profile(프로필) 아래의 Go to Admin][51]

5. Domain Solutions(도메인 솔루션) 페이지에서 **Domains**(도메인)를 선택합니다.

    ![Domain Solutions/Domains][50]

6. **Domains**(도메인) 섹션에서 **CLAIM DOMAIN**(도메인 클레임)을 선택합니다.

    ![Claim Domain 옵션][52]

7. **Claim a Domain**(도메인 클레임) 대화 상자의 **Domain Name**(도메인 이름) 상자에서 회사 도메인을 입력한 다음, **CLAIM**(클레임)을 선택합니다. 도메인 및 해당 상태가 활성인지의 여부를 확인합니다.

    ![Claim a Domain/Domain Name 대화 상자][53]

8. Domain Solutions 페이지에서 **Identity Providers**(ID 공급자)를 선택합니다.
  
    ![Identity Providers 옵션][54]

9. **Identity Providers**(ID 공급자) 섹션에서 **ADD IDENTITY PROVIDER**(ID 공급자 추가)를 선택합니다.

    ![Add Identity Provider 옵션][55]

10. **Identity Provider Settings**(ID 공급자 설정) 페이지에서 다음 단계를 수행합니다.

    ![Identity Provider Settings 필드][56]

    a. **Name**(이름) 상자에서 고유한 구성 이름을 입력합니다. 공백은 사용하지 마세요.

    b. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **Identity Provider Issuer**(ID 공급자 발급자) 상자에 붙여넣습니다.

    다. Azure Portal에서 복사한 **로그인 URL** 값을 **Identity Provider Login URL**(ID 공급자 로그인 URL) 상자에 붙여넣습니다.

    d. Azure Portal에서 복사한 **로그아웃 URL** 값을 **Identity Provider Logout URL**(ID 공급자 로그아웃 URL) 상자에 붙여넣습니다.

    e. **Sign AuthN 요청** 을 선택합니다.

    f. **Send AuthN request by**(다음을 통해 AuthN 요청 보내기)에 대해 **POST** 를 선택합니다.

    g. **Send logout request by**(다음을 통해 로그아웃 요청 보내기)에 대해 **GET** 을 선택합니다.

    h. **Custom Attribute Mapping**(사용자 지정 특성 매핑) 섹션에서 **ADD NEW MAPPING**(새 매핑 추가)을 선택합니다.

       ![Custom Attribute Mapping UI][62]

    i. Azure AD 클레임에 매핑하려는 필드를 선택합니다. 다음 예에서 **emailaddress** 클레임은 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`의 값으로 매핑됩니다. 이는 이메일 클레임에 대한 Azure AD의 기본 클레임 이름입니다. **SAVE**(저장)를 선택합니다.

       ![Custom Attribute Mapping 필드][57]

       > [!NOTE]
       > 적절한 **사용자 ID** 를 사용하여 Azure AD에서 DocuSign 사용자 매핑으로 사용자를 매핑합니다. 적절한 필드를 선택하고, 조직 설정에 따라 적절한 값을 입력합니다.

    j. **Identity Provider Certificates**(ID 공급자 인증서) 섹션에서 **ADD CERTIFICATE**(인증서 추가)를 선택하고, Azure AD 포털에서 다운로드한 인증서를 업로드한 다음, **SAVE**(저장)를 선택합니다.

       ![Identity Provider Certificates/Add Certificate][58]

    k. **Identity Providers**(ID 공급자) 섹션에서 **ACTIONS**(작업), **Endpoints**(엔드포인트)를 차례로 선택합니다.

       ![Identity Providers/Endpoints][59]

    l. DocuSign 관리 포털의 **View SAML 2.0 Endpoints**(SAML 2.0 엔드포인트 보기) 섹션에서 다음 단계를 수행합니다.

       ![SAML 2.0 엔드포인트 보기][60]
       
       1. **Service Provider Issuer URL**(서비스 공급자 발급자 URL)을 복사한 다음, Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자** 상자에 붙여넣습니다.
       
       1. **Service Provider Assertion Consumer Service URL** 을 복사한 다음, Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 상자에 붙여넣습니다.
       
       1. **Service Provider Login URL**(서비스 공급자 발급자 URL)을 복사한 다음, Azure Portal의 **기본 SAML 구성** 섹션에 있는 **로그온 URL** 상자에 붙여넣습니다. **서비스 공급자 로그인 URL** 의 끝에 IDPID 값을 가져옵니다.

       1. **닫기** 를 선택합니다.

### <a name="create-docusign-test-user"></a>DocuSign 테스트 사용자 만들기

이 섹션에서는 DocuSign에서 B.Simon이라는 사용자를 만듭니다. DocuSign은 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. DocuSign에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우 [DocuSign 지원 팀](https://support.docusign.com/)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

1. Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 DocuSign 로그온 URL로 리디렉션됩니다. 

2. DocuSign 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

3. Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 DocuSign 타일을 클릭하면 SSO를 설정한 DocuSign으로 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.


## <a name="next-steps"></a>다음 단계

DocuSign이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법 알아보기](/cloud-app-security/proxy-deployment-aad)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial-docusign-18.png
[51]: ./media/docusign-tutorial/tutorial-docusign-21.png
[52]: ./media/docusign-tutorial/tutorial-docusign-22.png
[53]: ./media/docusign-tutorial/tutorial-docusign-23.png
[54]: ./media/docusign-tutorial/tutorial-docusign-19.png
[55]: ./media/docusign-tutorial/tutorial-docusign-20.png
[56]: ./media/docusign-tutorial/tutorial-docusign-24.png
[57]: ./media/docusign-tutorial/tutorial-docusign-25.png
[58]: ./media/docusign-tutorial/tutorial-docusign-26.png
[59]: ./media/docusign-tutorial/tutorial-docusign-27.png
[60]: ./media/docusign-tutorial/tutorial-docusign-28.png
[61]: ./media/docusign-tutorial/tutorial-docusign-29.png
[62]: ./media/docusign-tutorial/tutorial-docusign-30.png