---
title: '자습서: Standard for Success Accreditation과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Standard for Success Accreditation 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/18/2021
ms.author: jeedes
ms.openlocfilehash: 64341d762eb05b77946f560f474ab1c4c52e3f52
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124800767"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-standard-for-success-accreditation"></a>자습서: Standard for Success Accreditation과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Standard for Success Accreditation을 통합하는 방법을 알아봅니다. Standard for Success Accreditation을 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Standard for Success Accreditation에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Standard for Success Accreditation에 자동으로 로그인되도록 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Standard for Success Accreditation SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Standard for Success Accreditation에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-standard-for-success-accreditation-from-the-gallery"></a>갤러리에서 Standard for Success Accreditation 추가

Standard for Success Accreditation이 Azure AD에 통합되도록 구성하려면 갤러리의 Standard for Success Accreditation을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Standard for Success Accreditation** 을 입력합니다.
1. 결과 패널에서 **Standard for Success Accreditation** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-standard-for-success-accreditation"></a>Standard for Success Accreditation에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Standard for Success Accreditation에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Standard for Success Accreditation의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Standard for Success Accreditation에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Standard for Success Accreditation SSO 구성](#configure-standard-for-success-accreditation-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Standard for Success Accreditation 테스트 사용자 만들기](#create-standard-for-success-accreditation-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Standard for Success Accreditation에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Standard for Success Accreditation** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 `api://<ApplicationId>` 패턴을 사용하여 값을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://edu.sfsed.com/access/saml_consume?did=<INSTITUTION-ID>` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 을 클릭하고 다음 단계를 수행합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://edu.sfsed.com/access/saml_int?did=<INSTITUTION-ID>` 패턴을 사용하여 URL을 입력합니다.

    b. **릴레이 상태** 텍스트 상자에서 `https://edu.sfsed.com/access/saml_consume?did=<INSTITUTION-ID>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL, 로그온 URL 및 릴레이 상태로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Standard for Success Accreditation 클라이언트 지원 팀](mailto:help_he@standardforsuccess.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

    ![SAML 서명 인증서 편집](common/edit-certificate.png)

1. **SAML 서명 인증서** 섹션에서 **지문 값** 을 복사하여 컴퓨터에 저장합니다.

    ![지문 값 복사](common/copy-thumbprint.png)

1. **Standard for Success Accreditation 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

    ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD 테스트 사용자 만들기

이 섹션에서는 Azure Portal에서 B.Simon이라는 테스트 사용자를 만듭니다.

1. Azure Portal의 왼쪽 창에서 **Azure Active Directory**, **사용자**, **모든 사용자** 를 차례로 선택합니다.
1. 화면 위쪽에서 **새 사용자** 를 선택합니다.
1. **사용자** 속성에서 다음 단계를 수행합니다.
   1. **이름** 필드에 `B.Simon`을 입력합니다.  
   1. **사용자 이름** 필드에서 username@institutiondomain.extension을 입력합니다. 예들 들어 `B.Simon@contoso.com`입니다.
   1. **암호 표시** 확인란을 선택한 다음, **암호** 상자에 표시된 값을 적어둡니다.
   1. **만들기** 를 클릭합니다.

### <a name="assign-the-azure-ad-test-user"></a>Azure AD 테스트 사용자 할당

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Standard for Success Accreditation에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Standard for Success Accreditation** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-standard-for-success-accreditation-sso"></a>Standard for Success Accreditation SSO 구성

1. 새 웹 브라우저 창을 열고 Standard for Success Accreditation 사이트에 슈퍼 사용자 액세스 권한이 있는 관리자로 로그인합니다.

1. 메뉴에서 **관리 포털** 을 클릭합니다.

1. **Single Sign On 설정** 까지 아래로 스크롤하여 **Microsoft Azure Single Sign On** 링크를 클릭하고 다음 단계를 수행합니다.

    :::image type="content" source="./media/standard-for-success-accreditation-tutorial/configuration.png" alt-text="Standard for Success Accreditation에서 Azure Single Sign-On을 사용하도록 설정하는 방법을 보여 주는 스크린샷":::

    a. **Azure Single Sign-On 사용** 확인란을 선택합니다.

    b. URL 및 식별자 필드를 Azure Portal SAML 설정에서 복사한 적절한 URL로 채웁니다.

    c. **애플리케이션 ID** 텍스트 상자에 애플리케이션 ID를 입력합니다.

    d. **인증서 지문** 텍스트 상자에 Azure Portal에서 복사한 **지문 값** 을 붙여넣습니다.

    e. **저장** 을 클릭합니다. 

### <a name="create-standard-for-success-accreditation-test-user"></a>Standard for Success Accreditation 테스트 사용자 만들기

1.  Standard for Success Accreditation에 슈퍼 사용자 권한이 있는 관리자로 로그인합니다.

1. 메뉴에서 **관리 포털** > **새 평가 대상 만들기** 를 클릭하고 다음 단계를 수행합니다.

    ![테스트 사용자를 만드는 중입니다.](./media/standard-for-success-accreditation-tutorial/new-user.png)

    a. **이름** 텍스트 상자에 B를 입력합니다.

    b. **성** 텍스트 상자에 Simon을 입력합니다.

    다. **대학교 메일** 텍스트 상자에 Azure 내에서 B.Simon에 대해 추가한 메일 주소를 입력합니다.

    d. 맨 아래로 스크롤하여 **사용자 만들기를** 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Standard for Success Accreditation 로그온 URL로 리디렉션됩니다.  

* Standard for Success Accreditation 로그온 URL로 직접 이동하여 거기에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Standard for Success Accreditation에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Standard for Success Accreditation 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Standard for Success Accreditation에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Standard for Success Accreditation이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).