---
title: '자습서: New Relic과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 New Relic 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/31/2021
ms.author: jeedes
ms.openlocfilehash: 67bbd26e4c565a5c7372be81fdf2398afe53c277
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132307229"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-new-relic"></a>자습서: New Relic과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 New Relic을 통합하는 방법에 대해 알아봅니다. Azure AD와 New Relic을 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 New Relic에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 New Relic에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* [New Relic One 계정/사용자 모델](https://docs.newrelic.com/docs/accounts/original-accounts-billing/original-product-based-pricing/overview-changes-pricing-user-model/#user-models) 및 Pro 또는 엔터프라이즈 버전에 대한 New Relic 조직입니다. 자세한 내용은 [New Relic 요구사항](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more)을 참조하세요.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* New Relic에서 서비스 공급자 또는 ID 공급자가 시작한 SSO를 지원합니다.

* New Relic에서 [자동화된 사용자 프로비저닝](new-relic-by-organization-provisioning-tutorial.md)을 지원합니다.

## <a name="add-new-relic-from-the-gallery"></a>갤러리에서 New Relic 추가

New Relic이 Azure AD에 통합되도록 구성하려면 갤러리의 **New Relic (By Organization)** 을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사나 학교 계정 또는 개인 Microsoft 계정으로 로그인합니다.
1. **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** > **새 애플리케이션** 을 차례로 선택합니다.
1. **Azure AD 갤러리 찾아보기** 페이지의 검색 상자에서 **New Relic (By Organization)** 을 입력합니다.
1. 결과에서 **New Relic (By Organization)** 을 선택한 다음, **만들기** 를 선택합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-new-relic"></a>New Relic에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 New Relic에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 New Relic의 관련 사용자 간에 연결된 관계를 설정해야 합니다.

New Relic에서 Azure AD SSO를 구성하고 테스트하려면 다음을 수행합니다.

1. [Azure AD SSO 구성](#configure-azure-ad-sso) - 사용자가 이 기능을 사용할 수 있도록 구성합니다.
   1. [Azure AD 테스트 사용자를 생성](#create-an-azure-ad-test-user)하여 B.Simon으로 Azure AD Single Sign-On을 테스트합니다.
   1. [Azure AD 테스트 사용자를 할당](#assign-the-azure-ad-test-user)하여 B.Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. [New Relic SSO 구성](#configure-new-relic-sso) - New Relic 쪽에서 Single Sign-On 설정을 구성합니다.
   1. [New Relic 테스트 사용자 만들기](#create-a-new-relic-test-user) - B.Simon의 Azure AD 사용자와 연결된 해당 사용자를 New Relic에 만듭니다.
1. [SSO 테스트](#test-sso) - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **New Relic by Organization** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾습니다. 그런 다음, **Single Sign-On** 을 선택합니다.

1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.

1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 선택하여 설정을 편집합니다.

   ![연필 아이콘이 강조 표시된 SAML로 Single Sign-On 설정의 스크린샷](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 **식별자** 및 **회신 URL** 에 대한 값을 입력합니다.

   * [New Relic 인증 도메인 UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui)에서 이러한 값을 검색합니다. 여기에서: 
      1. 인증 도메인이 둘 이상이 있는 경우 Azure AD SSO를 연결할 도메인을 선택합니다. 대부분의 회사에는 **Default(기본)** 라는 하나의 인증 도메인만 있습니다. 하나의 인증 도메인만 있으면 선택할 필요가 없습니다.
      1. **Authentication(인증)** 섹션의 **Assertion consumer URL(인증 소비자 URL)** 에는 **회신 URL** 에 사용할 값이 포함되어 있습니다.
      1. **Authentication** 섹션의 **Our entity ID(엔터티 ID)** 에는 **식별자** 에 사용할 값이 포함되어 있습니다.

1. **사용자 특성 및 클레임** 섹션에서 **고유한 사용자 ID** 가 New Relic에서 사용되는 이메일 주소가 포함된 필드에 매핑되었는지 확인합니다.

   * **user.userprincipalname** 기본 필드의 값이 New Relic 이메일 주소와 동일한 경우 이 기본 필드가 작동합니다.
   * **user.userprincipalname** 필드가 New Relic 이메일 주소가 아닌 경우 **user.mail** 필드가 더 효율적으로 작동할 수 있습니다.

1. **SAML 서명 인증서** 섹션에서 **앱 페더레이션 메타데이터 URL** 을 복사하고, 나중에 사용할 수 있도록 해당 값을 저장합니다.

1. **New Relic by Organization 설정** 섹션에서 **로그인 URL** 을 복사하고, 나중에 사용할 수 있도록 해당 값을 저장합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 New Relic에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **New Relic** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-new-relic-sso"></a>New Relic SSO 구성

New Relic에서 다음 단계에 따라 SSO를 구성합니다. 

1. [New Relic](https://login.newrelic.com/)에 로그인합니다.

1. [인증 도메인 UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#ui)로 이동합니다. 

1. Azure AD SSO에서 연결하도록 하려는 인증 도메인을 선택합니다(둘 이상의 인증 도메인이 있는 경우). 대부분의 회사에는 **Default(기본)** 라는 하나의 인증 도메인만 있습니다. 하나의 인증 도메인만 있으면 선택할 필요가 없습니다.

1. **Authentication(인증)** 섹션에서 **Configure(구성)** 를 선택합니다.

   1. **Source of SAML metadata(SAML 메타데이터 원본)** 에 대해 이전에 Azure AD **앱 페더레이션 메타데이터 URL** 필드에서 저장한 값을 입력합니다.

   1. **SSO target URL(SSO 대상 URL)** 필드에 대해 이전에 Azure AD **로그인 URL** 필드에서 저장한 값을 입력합니다.

   1. Azure AD 및 New Relic 쪽 모두에서 설정이 올바른지 확인한 다음, **Save(저장)** 를 클릭합니다. 양쪽 모두가 제대로 구성되지 않으면 사용자가 New Relic에 로그인할 수 없습니다.

### <a name="create-a-new-relic-test-user"></a>New Relic 테스트 사용자 만들기

이 섹션에서는 New Relic에서 B.Simon이라는 사용자를 만듭니다.

1. [New Relic](https://login.newrelic.com/)에 로그인합니다.

1. [**사용자 관리** UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where)로 이동합니다.

1. **사용자 추가** 를 선택합니다.

   1. **Name(이름)** 에 대해 **B.Simon** 을 입력합니다.
   
   1. **Email(이메일)** 에 대해 Azure AD SSO에서 보낼 값을 입력합니다.
   
   1. 사용자에 대한 사용자 **Type(유형)** 및 사용자 **Group(그룹)** 을 선택합니다. 테스트 사용자의 경우 Type에 대해 **기본 사용자** 를 선택하고 Group에 대해 **사용자** 를 선택하는 것이 좋습니다.
   
   1. 사용자를 저장하려면 **Add User(사용자 추가)** 를 선택합니다.

> [!NOTE]
> New Relic은 자동 사용자 프로비저닝도 지원합니다. 자동 사용자 프로비저닝 구성 방법에 대한 자세한 내용은 [여기](./new-relic-by-organization-provisioning-tutorial.md)에서 제공합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 New Relic 로그온 URL로 리디렉션됩니다.  

* New Relic 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 New Relic에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 New Relic 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 New Relic에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

완료되면 [**사용자 관리** UI](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#where)로 이동하여 사용자가 New Relic에 추가되었는지 확인할 수 있습니다. 

다음으로, 사용자를 특정 New Relic 계정 또는 역할에 할당하는 것이 좋습니다. 이에 대한 자세한 내용은 [사용자 관리 개념](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/add-manage-users-groups-roles/#understand-concepts)을 참조하세요. 

New Relic의 인증 도메인 UI에서 세션 제어와 같은 [다른 설정](https://docs.newrelic.com/docs/accounts/accounts-billing/new-relic-one-user-management/authentication-domains-saml-sso-scim-more/#session-mgmt)을 구성하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
