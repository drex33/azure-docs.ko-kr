---
title: '자습서: Samsung Knox and Business Services와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Samsung Knox and Business Services 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: a501c12c81da0e2fbacfa6db3a46d590f2501a54
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124746415"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-samsung-knox-and-business-services"></a>자습서: Samsung Knox and Business Services와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Samsung Knox and Business Services를 통합하는 방법에 대해 알아봅니다. Azure AD와 Samsung Knox and Business Services를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Samsung Knox and Business Services에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Samsung Knox and Business Services에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Samsung Knox 계정.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Samsung Knox and Business Services에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-samsung-knox-and-business-services-from-the-gallery"></a>갤러리에서 Samsung Knox and Business Services 추가

Samsung Knox and Business Services가 Azure AD에 통합되도록 구성하려면 갤러리의 Samsung Knox and Business Services를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Samsung Knox and Business Services** 를 입력합니다.
1. 결과 패널에서 **Samsung Knox and Business Services** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-samsung-knox-and-business-services"></a>Samsung Knox and Business Services에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Samsung Knox and Business Services에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 [SamsungKnox.com](https://samsungknox.com/)의 관련 사용자 간에 연결이 형성되어야 합니다.

Samsung Knox and Business Services에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Samsung Knox and Business Services SSO 구성](#configure-samsung-knox-and-business-services-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Samsung Knox and Business Services 테스트 사용자 만들기](#create-samsung-knox-and-business-services-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Samsung Knox and Business Services에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Samsung Knox and Business Services** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

    * **로그온 URL** 텍스트 상자에 `https://www2.samsungknox.com/en/sso/login/ad` URL을 입력합니다.
    * **회신 URL(Assertion Consumer Service URL)** 텍스트 상자에 `https://central.samsungknox.com/ams/ad/saml/acs` URL을 입력합니다.
    
    ![기본 SAML 구성 값](https://docs.samsungknox.com/assets/merge/ad-sso/basic-saml-configuration.png)

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

이 섹션에서는 B.Simon에게 Samsung Knox and Business Services에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Samsung Knox and Business Services** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-samsung-knox-and-business-services-sso"></a>Samsung Knox and Business Services SSO 구성

1. 다른 웹 브라우저 창에서 [SamsungKnox.com](https://samsungknox.com/)에 관리자로 로그인합니다.

1. 오른쪽 위 모서리에서 **아바타** 를 클릭합니다.

    ![Samsung Knox 아바타](./media/samsung-knox-and-business-services-tutorial/avatar.png)

1. 왼쪽 사이드바에서 **ACTIVE DIRECTORY SETTINGS(Active Directory 설정)** 를 클릭하고, 다음 단계를 수행합니다.

    ![ACTIVE DIRECTORY SETTINGS](https://docs.samsungknox.com/assets/merge/ad-sso/ad-5.png)

    a. Azure Portal에서 입력한 **식별자** 값을 **Identifier(entity ID)(식별자(엔터티 ID))** 텍스트 상자에 붙여넣습니다.

    b. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **App federation metadata URL(앱 페더레이션 메타데이터 URL)** 텍스트 상자에 붙여넣습니다.

    다. **CONNECT TO AD SSO(SSO에 연결)** 를 클릭합니다.

### <a name="create-samsung-knox-and-business-services-test-user"></a>Samsung Knox and Business Services 테스트 사용자 만들기

이 섹션에서는 Samsung Knox and Business Services에서 Britta Simon이라는 사용자를 만듭니다. 하위 관리자 또는 테스트 사용자를 Samsung Knox 조직에 초대하는 방법에 대한 지침은 [Knox 구성](https://docs.samsungknox.com/admin/knox-configure/Administrators.htm) 또는 [Knox Mobile 등록](https://docs.samsungknox.com/admin/knox-mobile-enrollment/kme-add-an-admin.htm) 관리자 가이드를 참조하세요. Single Sign-On을 사용하려면 먼저 사용자를 만들고 활성화해야 합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 [SamsungKnox.com](https://samsungknox.com/)으로 리디렉션됩니다. 

* [SamsungKnox.com](https://samsungknox.com/)으로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Samsung Knox 및 Business Services 타일을 클릭하면 [SamsungKnox.com](https://samsungknox.com/)으로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Samsung Knox and Business Services가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).