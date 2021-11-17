---
title: '자습서: Andromeda와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory 및 Andromeda 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 08/12/2021
ms.author: jeedes
ms.openlocfilehash: e15c0cf41010fb5336dcc49ebba4ea43c7c91b7b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132289417"
---
# <a name="tutorial-azure-active-directory-integration-with-andromeda"></a>자습서: Andromeda와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Andromeda를 통합하는 방법에 대해 알아봅니다. Azure AD와 Andromeda를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Andromeda에 액세스할 수 있는 사용자를 제어합니다.
* 사용자가 Azure AD 계정으로 Andromeda에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Andromeda와 Azure AD를 통합하도록 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Andromeda Single Sign-On이 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Andromeda는 **SP 및 IDP** 시작 SSO를 지원합니다.
* Andromeda는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="add-andromeda-from-the-gallery"></a>갤러리에서 Andromeda 추가

Azure AD에 Andromeda를 통합하도록 구성하려면 갤러리의 Andromeda를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Andromeda** 를 입력합니다.
1. 결과 패널에서 **Andromeda** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-andromeda"></a>Andromeda에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Andromeda에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Andromeda의 관련 사용자 간에 링크 관계를 설정해야 합니다.

Andromeda에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
   1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
   1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[Andromeda SSO 구성](#configure-andromeda-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   1. **[Andromeda 테스트 사용자 만들기](#create-andromeda-test-user)** - Britta Simon의 Azure AD 표현과 연결된 해당 사용자를 Andromeda에 만듭니다.
3. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Andromeda** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 다음, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다. 

   a. **식별자** 텍스트 상자에서 `https://<tenantURL>.ngcxpress.com/` 패턴을 사용하여 URL을 입력합니다.

   b. **회신 URL** 텍스트 상자에서 `https://<tenantURL>.ngcxpress.com/SAMLConsumer.aspx` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

   **로그인 URL** 텍스트 상자에서 `https://<tenantURL>.ngcxpress.com/SAMLLogon.aspx` 패턴을 사용하여 URL을 입력합니다.

   > [!NOTE]
   > 이러한 값은 실제 값이 아닙니다. 자습서 뒷부분에 설명된 실제 식별자, 응답 URL 및 로그온 URL로 값을 업데이트하게 됩니다.

1. Andromeda 애플리케이션은 특정 형식의 SAML 어설션이 필요합니다. 이 애플리케이션에 대해 다음 클레임을 구성합니다. 애플리케이션 통합 페이지의 **사용자 특성** 섹션에서 이러한 특성의 값을 관리할 수 있습니다. **SAML로 Single Sign-On 설정** 페이지에서 **편집** 단추를 클릭하여 **사용자 특성** 대화 상자를 엽니다.

   ![사용자 특성을 보여 주는 스크린샷](common/edit-attribute.png)

   > [!NOTE]
   > 이러한 설정 동안 네임스페이스 정의를 지웁니다.

1. 위의 이미지와 같이 SAML 토큰 특성을 구성하기 위해 **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 **편집 아이콘** 을 사용하여 클레임을 편집하거나 **새 클레임 추가** 를 사용하여 클레임을 추가하고, 다음 단계를 수행합니다.

   | 속성    | 원본 특성  |
   | ------- | ----------------- |
   | 역할(role)    | 앱 특정 역할 |
   | type    | 앱 유형          |
   | company | CompanyName       |

   > [!NOTE]
   > Andromeda는 애플리케이션에 할당된 사용자의 역할이 필요합니다. 사용자가 적절한 역할을 할당 받을 수 있도록 Azure AD에서 이러한 역할을 설정하세요. Azure AD에서 역할을 구성하는 방법을 이해하려면 [여기](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)를 참조하세요.

   a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

   ![스크린샷은 새 클레임이 추가되고 저장 옵션이 포함된 사용자 클레임을 보여줍니다.](common/new-save-attribute.png)

   ![스크린샷은 이 단계에서 설명한 값을 입력할 수 있는 사용자 클레임 관리를 보여줍니다.](common/new-attribute-details.png)

   b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

   다. **네임스페이스** 를 비워 둡니다.

   d. 원본을 **특성** 으로 선택합니다.

   e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

   f. **확인** 을 클릭합니다.

   g. **저장** 을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Andromeda 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Andromeda에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Andromeda** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 위에서 설명한 대로 역할을 설정한 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

### <a name="configure-andromeda-sso"></a>Andromeda SSO 구성

1. Andromeda 회사 사이트에 관리자로 로그인합니다.

2. 메뉴 모음 상단에 있는 **관리자** 를 클릭하고 **관리** 로 이동합니다.

   ![Andromeda 관리자](./media/andromedascm-tutorial/admin.png)

3. 도구 모음 왼쪽의 **인터페이스** 섹션 아래에서 **SAML 구성** 을 클릭합니다.

   ![Andromeda SAML](./media/andromedascm-tutorial/interface.png)

4. **SAML 구성** 섹션 페이지에서 다음 단계를 수행합니다.

   ![Andromeda 구성](./media/andromedascm-tutorial/configure.png)

   a. **SAML에서 SSO 사용** 을 선택합니다.

   b. **Andromeda 정보** 섹션 아래에서 **SP ID** 값을 복사한 다음, **기본 SAML 구성** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

   다. **소비자 URL** 값을 복사하여 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.

   d. **로그온 URL** 값을 복사하여 **기본 SAML 구성** 섹션의 **로그온 URL** 텍스트 상자에 붙여넣습니다.

   e. **SAML ID 공급자** 섹션 아래에 IDP 이름을 입력합니다.

   f. Azure Portal에서 복사한 **로그인 URL** 값을 **Single Sign-On 엔드포인트** 텍스트 상자에 붙여넣습니다.

   g. Azure Portal에서 다운로드한 **Base64로 인코딩된 인증서** 를 메모장에서 열고 **X 509 인증서** 텍스트 상자에 붙여넣습니다.

   h. Azure AD에서 SSO 로그인을 편리하게 수행하려면 다음 특성을 해당 값에 매핑합니다. **사용자 ID** 특성은 로그인하는 데 필요합니다. 프로비전의 경우 **전자 메일**, **회사**, **UserType** 및 **역할** 이 필요합니다. 이 섹션에서는 Azure Portal 내에서 정의된 특성 매핑(이름 및 값)과 상호 연관되는 특성 매핑을 정의합니다.

   ![Andromeda 특성](./media/andromedascm-tutorial/mapping.png)

   i. **저장** 을 클릭합니다.

### <a name="create-andromeda-test-user"></a>Andromeda 테스트 사용자 만들기

이 섹션에서는 Andromeda에서 Britta Simon이라는 사용자를 만듭니다. Andromeda는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Andromeda에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다. 사용자를 수동으로 생성해야 하는 경우 [Andromeda 클라이언트 지원 팀](https://www.ngcsoftware.com/support/)에 문의하세요.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Andromeda 로그온 URL로 리디렉션됩니다.

* Andromeda 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Andromeda에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Andromeda 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Andromeda에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Andromeda를 구성한 후에는 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 침입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
