---
title: '자습서: Saba Cloud와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Saba Cloud 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
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
ms.openlocfilehash: 521199020a6b7badeb1e89857c0cc47330d75823
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124752960"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-cloud"></a>자습서: Saba Cloud와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Saba Cloud를 통합하는 방법에 대해 알아봅니다. Azure AD와 Saba Cloud를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Saba Cloud에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Saba Cloud에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Saba Cloud SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Saba Cloud에서 **SP 및 IDP** 시작 SSO를 지원합니다.
* Saba Cloud에서 **Just-In-Time** 사용자 프로비저닝을 지원합니다.
* 이제 SSO를 사용하도록 설정하기 위해 Azure AD를 사용하여 Saba Cloud 모바일 애플리케이션을 구성할 수 있습니다. 이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

## <a name="adding-saba-cloud-from-the-gallery"></a>갤러리에서 Saba Cloud 추가

Saba Cloud가 Azure AD에 통합되도록 구성하려면 갤러리의 Saba Cloud를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Saba Cloud** 를 입력합니다.
1. 결과 패널에서 **Saba Cloud** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-saba-cloud"></a>Saba Cloud에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Saba Cloud에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Saba Cloud의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Saba Cloud에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Saba Cloud SSO 구성](#configure-saba-cloud-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Saba Cloud 테스트 사용자 만들기](#create-saba-cloud-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Saba Cloud에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.
1. **[Saba Cloud(모바일) SSO 테스트](#test-sso-for-saba-cloud-mobile)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Saba Cloud** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에 `<CUSTOMER_NAME>_sp` 패턴을 사용하여 URL을 입력합니다(6단계의 Saba Cloud SSO 구성 섹션에서 이 값을 가져올 수 있지만 일반적으로 `<CUSTOMER_NAME>_sp` 형식임).

    b. **회신 URL** 텍스트 상자에 `https://<CUSTOMER_NAME>.sabacloud.com/Saba/saml/SSO/alias/<ENTITY_ID>` 패턴을 사용하여 URL을 입력합니다(ENTITY_ID는 일반적으로 `<CUSTOMER_NAME>_sp`인 이전 단계를 나타냄).
    
    > [!NOTE]
    > 회신 URL을 잘못 지정하는 경우 **엔터프라이즈 애플리케이션** 섹션이 아닌 Azure AD의 **앱 등록** 섹션에서 조정해야 할 수 있습니다. **기본 SAML 구성** 섹션을 변경해도 회신 URL이 항상 업데이트되지는 않습니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    a. **로그인 URL** 텍스트 상자에서 `https://<CUSTOMER_NAME>.sabacloud.com` 패턴을 사용하여 URL을 입력합니다.

    b. **릴레이 상태** 텍스트 상자에서 `IDP_INIT---SAML_SSO_SITE=<SITE_ID> ` 패턴을 사용하는 URL을 입력하거나, SAML이 마이크로사이트에 대해 구성된 경우 `IDP_INIT---SAML_SSO_SITE=<SITE_ID>---SAML_SSO_MICRO_SITE=<MicroSiteId>` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL, 로그온 URL 및 릴레이 상태로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Saba Cloud 클라이언트 지원 팀](mailto:support@saba.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.
    > 
    > RelayState 구성에 대한 자세한 내용은 [마이크로 사이트에 대한 IdP 및 SP 시작 SSO](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-idp-and-sp-initiated-sso-for-a-microsite.html)를 참조하세요.

1. **사용자 특성 및 클레임** 섹션에서는 조직에서 Saba 사용자의 기본 사용자 이름으로 사용하려는 모든 항목에 대해 고유 사용자 식별자를 조정합니다.

   이 단계는 사용자 이름/암호에서 SSO로 변환하려는 경우에만 필요합니다. 기존 사용자가 없는 새 Saba Cloud 배포인 경우 이 단계를 건너뛸 수 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Saba Cloud 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon에게 Saba Cloud에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Saba Cloud** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-saba-cloud-sso"></a>Saba Cloud SSO 구성

1. Saba Cloud 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Saba Cloud 설정** 을 클릭하면 Saba Cloud 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Saba Cloud에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3~9단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Saba Cloud를 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 Saba Cloud 회사 사이트에 로그인합니다.

1. **Menu(메뉴)** 아이콘을 클릭하고, **Admin(관리자)** 을 클릭한 다음, **System Admin(시스템 관리자)** 탭을 선택합니다.

    ![시스템 관리자의 스크린샷](./media/saba-cloud-tutorial/system.png)

1. **Configure System(시스템 구성)** 에서 **SAML SSO Setup(SAML SSO 설정)** 을 선택하고, **SETUP SAML SSO(SAML SSO 설정)** 단추를 클릭합니다. 

    ![구성의 스크린샷](./media/saba-cloud-tutorial/configure.png)

1. 팝업 창의 드롭다운에서 **Microsite(마이크로사이트)** 를 선택하고, **ADD AND CONFIGURE(추가 및 구성)** 를 클릭합니다.

    ![사이트/마이크로사이트 추가의 스크린샷](./media/saba-cloud-tutorial/microsite.png)

1. **Configure IDP(IDP 구성)** 섹션에서 **BROWSE(찾아보기)** 를 클릭하여 Azure Portal에서 다운로드한 **페더레이션 메타데이터 XML** 파일을 업로드합니다. **Site Specific IDP(사이트별 IDP)** 확인란을 사용하도록 설정하고, **IMPORT(가져오기)** 를 클릭합니다.

    ![인증서 가져오기의 스크린샷](./media/saba-cloud-tutorial/certificate.png) 

1. **Configure SP(SP 구성)** 섹션에서 **Entity Alias(엔터티 별칭)** 값을 복사하고, 이 값을 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자(엔터티 ID)** 텍스트 상자에 붙여넣습니다. **GENERATE(생성)** 를 클릭합니다.

    ![SP 구성의 스크린샷](./media/saba-cloud-tutorial/generate-metadata.png) 

1. **Configure Properties(속성 구성)** 섹션에서 채워진 필드를 확인하고 **SAVE(저장)** 를 클릭합니다. 

    ![속성 구성의 스크린샷](./media/saba-cloud-tutorial/configure-properties.png) 
    
    Azure AD에서 로그인에 허용하는 기본 최대 롤링 기간과 일치하도록 **최대 인증 기간(초)** 을 **7776000(90일)** 으로 설정해야 할 수 있습니다. 이렇게 하지 않으면 오류 `(109) Login failed. Please contact system administrator.`가 발생할 수 있습니다.

### <a name="create-saba-cloud-test-user"></a>Saba Cloud 테스트 사용자 만들기

이 섹션에서는 Saba Cloud에서 Britta Simon이라는 사용자를 만듭니다. Saba Cloud는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Saba Cloud에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!NOTE]
> Saba Cloud를 사용하여 SAML Just-In-Time 사용자 프로비저닝을 사용하도록 설정하려면 [이](https://help.sabacloud.com/sabacloud/help-system/topics/help-system-user-provisioning-with-saml.html) 설명서를 참조하세요.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Saba Cloud 로그온 URL로 리디렉션됩니다.  

* Saba Cloud 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Saba Cloud에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Saba Cloud 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우 SSO를 설정한 Saba Cloud에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

> [!NOTE]
> Azure AD에서 로그인 URL이 채워지지 않으면 애플리케이션이 IDP 시작 모드로 처리되고, 로그인 URL이 채워지면 Azure AD에서 항상 서비스 공급자 시작 흐름에 대해 사용자를 Saba Cloud 애플리케이션으로 리디렉션합니다.

## <a name="test-sso-for-saba-cloud-mobile"></a>Saba Cloud(모바일) SSO 테스트

1. Saba Cloud 모바일 애플리케이션을 열고, 텍스트 상자에서 **Site Name(사이트 이름)** 을 지정하고, **Enter** 키를 클릭합니다.

    ![사이트 이름의 스크린샷](./media/saba-cloud-tutorial/site-name.png)

1. **이메일 주소** 를 입력하고, **Next(다음)** 를 클릭합니다.

    ![이메일 주소의 스크린샷](./media/saba-cloud-tutorial/email-address.png)

1. 마지막으로 성공적으로 로그인하면 애플리케이션 페이지가 표시됩니다.

    ![성공적인 로그인의 스크린샷](./media/saba-cloud-tutorial/dashboard.png)

## <a name="next-steps"></a>다음 단계

Saba Cloud가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).