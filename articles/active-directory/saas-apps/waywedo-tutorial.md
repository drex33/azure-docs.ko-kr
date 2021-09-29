---
title: '자습서: Way We Do와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Way We Do 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.openlocfilehash: 26e55c1bbb1c20781a28dda8ad635c99d058da2d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124731519"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>자습서: Way We Do와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Way We Do를 통합하는 방법에 대해 알아봅니다. Azure AD와 Way We Do를 통합하는 경우 다음을 수행할 수 있습니다.

* Way We Do에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어할 수 있습니다.
* 사용자가 해당 Azure AD 계정으로 Way We Do에 자동으로 로그인되도록 합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

Azure AD와 SaaS 앱 통합에 대한 자세한 내용은 [Azure Active Directory를 사용한 애플리케이션 액세스 및 Single Sign-On이란 무엇인가요?](../manage-apps/what-is-single-sign-on.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [여기](https://azure.microsoft.com/pricing/free-trial/)에서 1개월 평가판을 다운로드할 수 있습니다.
* Way We Do SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Way We Do는 **SP** 시작 SSO를 지원합니다.
* Way We Do는 **Just In Time** 사용자 프로비전을 지원합니다.

## <a name="adding-way-we-do-from-the-gallery"></a>갤러리에서 Way We Do 추가

Way We Do의 Azure AD 통합을 구성하려면 갤러리의 Way We Do를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. [Azure Portal](https://portal.azure.com)에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Way We Do** 를 입력합니다.
1. 결과 패널에서 **Way We Do** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD Single Sign-On 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Way We Do에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Way We Do의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Way We Do에서 Azure AD SSO를 구성하고 테스트하려면 다음 구성 요소를 완료합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
2. **[Way We Do SSO 구성](#configure-way-we-do-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
3. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
4. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
5. **[Way We Do 테스트 사용자 만들기](#create-way-we-do-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Way We Do에 만듭니다.
6. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)의 **Way We Do** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 페이지에서 다음 필드에 대한 값을 입력합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에서 `https://<SUBDOMAIN>.waywedo.com` 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 식별자로 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Way We Do 클라이언트 지원 팀](mailto:support@waywedo.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(원시)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificateraw.png)

1. **Way We Do 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

   ![구성 URL 복사](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Way We Do SSO 구성

1. Way We Do 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Way We Do 설정** 을 클릭하면 Way We Do 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Way We Do에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

1. Way We Do를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Way We Do 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. Way We Do 페이지의 오른쪽 위 모서리에서 **사용자 아이콘** 을 클릭한 다음, 드롭다운 메뉴에서 **계정** 을 클릭합니다.

    ![Way We Do 계정](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. **메뉴 아이콘** 을 클릭하여 푸시 탐색 메뉴를 열고 **Single Sign On** 을 클릭합니다.

    ![Way We Do 단일](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. **Single Sign-On 설정** 페이지에서 다음 단계를 수행합니다.

    ![Way We Do 저장](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    1. **Single Sign-On 사용** 토글을 **예** 로 클릭하여 Single Sign-On을 사용하도록 설정합니다.

    1. **Single Sign-On 이름** 텍스트 상자에 이름을 입력합니다.

    1. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **엔터티 ID** 텍스트 상자에 붙여넣습니다.

    1. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML SSO URL** 텍스트 상자에 붙여넣습니다.

    1. **인증서** 옆에 있는 **선택** 단추를 클릭하여 인증서를 업로드합니다.

    1. **선택적 설정** -

        * 암호 사용 - 이 옵션을 사용하지 않도록 설정하면 사용자가 Single Sign-On만을 사용할 수 있도록 Way We Do에 대한 일반 암호가 작동합니다.

        * 자동 프로비전 사용 - 이 옵션을 사용하도록 설정하면 로그온에 사용된 이메일 주소가 Way We Do의 사용자 목록과 자동으로 비교됩니다. 이메일 주소가 Way We Do에 활성 사용자와 일치하지 않으면 로그인하는 사용자에 대한 새 사용자 계정을 자동으로 추가하여 누락된 정보를 요청합니다.

          > [!NOTE]
          > Single Sign-On을 통해 추가된 사용자는 일반 사용자로 추가되고 시스템에서 역할이 할당되지 않습니다. 관리자는 편집자 또는 관리자 권한으로 해당 보안 역할에 들어가서 수정할 수 있고 하나 또는 여러 개의 조직 자트 역할을 할당할 수도 있습니다.

    1. **저장** 을 클릭하여 설정을 유지합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Way We Do에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Way We Do** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.

   !["사용자 및 그룹" 링크](common/users-groups-blade.png)

1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.

    ![사용자 추가 링크](common/add-assign-user.png)

1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. SAML 어설션에 역할 값이 필요한 경우 **역할 선택** 대화 상자의 목록에서 사용자에 대한 적절한 역할을 선택한 다음, 화면의 아래쪽에 있는 **선택** 단추를 클릭합니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="create-way-we-do-test-user"></a>Way We Do 테스트 사용자 만들기

이 섹션에서는 Britta Simon이라는 사용자를 Way We Do에서 만듭니다. Way We Do는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비전을 지원합니다. 이 섹션에 작업 항목이 없습니다. Way We Do에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우, [Way We Do 클라이언트 지원 팀](mailto:support@waywedo.com)에 문의하세요.

### <a name="test-sso"></a>SSO 테스트

액세스 패널에서 Way We Do 타일을 선택하면 SSO가 설정된 Way We Do에 자동으로 로그인됩니다. 액세스 패널에 대한 자세한 내용은 [액세스 패널 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="additional-resources"></a>추가 리소스

- [Azure Active Directory와 SaaS Apps를 통합하는 방법에 대한 자습서 목록](./tutorial-list.md)

- [Azure Active Directory로 애플리케이션 액세스 및 Single Sign-On을 구현하는 방법](../manage-apps/what-is-single-sign-on.md)

- [Azure Active Directory의 조건부 액세스란?](../conditional-access/overview.md)