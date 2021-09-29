---
title: '자습서: Azure Active Directory와 LaunchDarkly 통합 | Microsoft Docs'
description: Azure Active Directory 및 LaunchDarkly 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/17/2021
ms.author: jeedes
ms.openlocfilehash: 8831666015663eedd3ab779cd23b58bb8cf0df24
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124739136"
---
# <a name="tutorial-azure-active-directory-integration-with-launchdarkly"></a>자습서: Azure Active Directory와 LaunchDarkly 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 LaunchDarkly를 통합하는 방법에 대해 알아봅니다. Azure AD와 LaunchDarkly를 통합하는 경우 다음을 수행할 수 있습니다.

* LaunchDarkly에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 LaunchDarkly에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

    > [!NOTE]
    > LaunchDarkly Azure Active Directory 통합은 단방향입니다. 통합을 구성한 후에는 Azure AD를 사용하여 LaunchDarkly에서 사용자, SSO 및 계정을 관리할 수 있지만 LaunchDarkly를 사용하여 Azure에서 사용자, SSO 및 계정을 관리할 수는 **없습니다** .

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* LaunchDarkly Single Sign-On이 설정된 구독.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* LaunchDarkly에서 **IDP** 시작 SSO를 지원합니다.
* LaunchDarkly에서 **Just In Time** 사용자 프로비저닝을 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-launchdarkly-from-the-gallery"></a>갤러리에서 LaunchDarkly 추가

LaunchDarkly와 Azure AD의 통합을 구성하려면 갤러리의 LaunchDarkly를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **LaunchDarkly** 를 입력합니다.
1. 결과 패널에서 **LaunchDarkly** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-launchdarkly"></a>LaunchDarkly에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 LaunchDarkly에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 LaunchDarkly의 관련 사용자 간에 연결 관계를 설정해야 합니다.

LaunchDarkly에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[LaunchDarkly SSO 구성](#configure-launchdarkly-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[LaunchDarkly 테스트 사용자 만들기](#create-launchdarkly-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 LaunchDarkly에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **LaunchDarkly** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `app.launchdarkly.com` URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://app.launchdarkly.com/trust/saml2/acs/<customers-unique-id>` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > [회신 URL] 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 회신 URL로 값을 업데이트하게 됩니다. 애플리케이션을 **IDP** 모드에서 사용하려면 **로그온 URL** 필드를 비워 둬야 합니다. 그렇지 않으면 **IDP** 에서 로그인을 시작할 수 없게 됩니다. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

7. **LaunchDarkly 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 LaunchDarkly에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **LaunchDarkly** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-launchdarkly-sso"></a>LaunchDarkly SSO 구성

1. 다른 웹 브라우저 창에서 LaunchDarkly 회사 사이트에 관리자로 로그인합니다.

2. 왼쪽 탐색 패널에서 **계정 설정** 을 선택합니다.

    ![스크린샷은 프로덕션에서 선택한 계정 설정 항목을 보여줍니다.](./media/launchdarkly-tutorial/configure-1.png)

3. **보안** 탭을 클릭합니다.

    ![스크린샷은 계정 설정의 보안 탭을 보여줍니다.](./media/launchdarkly-tutorial/configure-2.png)

4. **ENABLE SSO**(SSO 사용)를 클릭한 다음, **EDIT SAML CONFIGURATION**(SAML 구성 편집)을 클릭합니다.

    ![스크린샷은 SSO를 활성화하고 SAML 구성을 편집할 수 있는 Single Sign-On 페이지를 보여줍니다.](./media/launchdarkly-tutorial/configure-3.png)

5. **Edit your SAML configuration**(SAML 구성 편집) 섹션에서 다음 단계를 수행합니다.

    ![스크린샷은 여기에 설명된 대로 변경할 수 있는 SAML 구성 편집 섹션을 보여줍니다.](./media/launchdarkly-tutorial/configure-4.png)

    a. 인스턴스에 대한 **SAML 소비자 서비스 URL** 을 복사하여 Azure Portal의 **LaunchDarkly 도메인 및 URL** 섹션에서 [회신 URL] 텍스트 상자에 붙여넣습니다.

    b. **로그온 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    다. Azure Portal에서 다운로드한 인증서를 메모장으로 열어 내용을 복사한 다음, **X.509 인증서** 상자에 붙여넣거나 **upload one**(업로드)을 클릭하여 인증서를 직접 업로드합니다.

    d. **저장** 을 클릭합니다.

### <a name="create-launchdarkly-test-user"></a>LaunchDarkly 테스트 사용자 만들기

이 섹션에서는 LaunchDarkly에서 B.Simon이라는 사용자를 만듭니다. LaunchDarkly는 Just-In-Time 사용자 프로비저닝을 지원하며, 기본적으로 사용하도록 설정되어 있습니다. 이 섹션에는 작업 항목이 없습니다. LaunchDarkly에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 LaunchDarkly에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 LaunchDarkly 타일을 클릭하면 SSO를 설정한 LaunchDarkly에 자동으로 로그인되어야 합니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

LaunchDarkly가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).