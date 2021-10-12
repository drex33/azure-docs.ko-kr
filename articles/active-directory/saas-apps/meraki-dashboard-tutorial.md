---
title: '자습서: Meraki Dashboard와 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory 및 Meraki Dashboard 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/28/2021
ms.author: jeedes
ms.openlocfilehash: 5adf1f5c2c811bdc2b48e03c1e2892746d36da4b
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129402924"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-meraki-dashboard"></a>자습서: Meraki Dashboard와 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Meraki Dashboard를 통합하는 방법에 대해 알아봅니다. Azure AD와 Meraki Dashboard를 통합하면 다음을 수행할 수 있습니다.

- Azure AD에서 Meraki Dashboard에 액세스할 수 있는 사용자를 제어합니다.
- 사용자가 해당 Azure AD 계정으로 Meraki Dashboard에 자동으로 로그인되도록 설정합니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- Meraki Dashboard SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- Meraki Dashboard에서 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="adding-meraki-dashboard-from-the-gallery"></a>갤러리에서 Meraki Dashboard 추가

Meraki Dashboard가 Azure AD에 통합되도록 구성하려면 갤러리의 Meraki Dashboard를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Meraki Dashboard** 를 입력합니다.
1. 결과 패널에서 **Meraki Dashboard** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-meraki-dashboard"></a>Meraki Dashboard에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Meraki Dashboard에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Meraki Dashboard의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Meraki Dashboard에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
   1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
   1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Meraki Dashboard SSO 구성](#configure-meraki-dashboard-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   1. **[Meraki Dashboard 관리자 역할 만들기](#create-meraki-dashboard-admin-roles)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Meraki Dashboard에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Meraki Dashboard** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 편집(연필 모양) 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

   **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://n27.meraki.com/saml/login/m9ZEgb/< UNIQUE ID >`

   > [!NOTE]
   > [회신 URL] 값은 실제 값이 아닙니다. 이 값을 자습서의 뒷부분에서 설명하는 실제 회신 URL로 업데이트합니다.

1. **저장** 단추를 클릭합니다.

1. Meraki Dashboard 애플리케이션에는 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 하는 특정 형식의 SAML 어설션이 필요합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

   ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Meraki Dashboard 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

   | Name                                                    | 원본 특성       |
   | ------------------------------------------------------- | ---------------------- |
   | `https://dashboard.meraki.com/saml/attributes/username` | user.userprincipalname |
   | `https://dashboard.meraki.com/saml/attributes/role`     | user.assignedroles     |

   > [!NOTE]
   > Azure AD에서 역할을 구성하는 방법을 이해하려면 [여기](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)를 참조하세요.

1. **SAML 서명 인증서** 섹션에서 **편집** 단추를 클릭하여 **SAML 서명 인증서** 대화 상자를 엽니다.

   ![SAML 서명 인증서 편집](common/edit-certificate.png)

1. **SAML 서명 인증서** 섹션에서 **지문 값** 을 복사하여 컴퓨터에 저장합니다. Meraki 대시보드에서 이 값을 이해하기 위해서는 콜론을 포함하도록 변환해야 합니다. 예를 들어 Azure의 지문이 `C2569F50A4AAEDBB8E`인 경우 나중에 Meraki 대시보드에서 사용하려면 `C2:56:9F:50:A4:AA:ED:BB:8E`로 변경해야 합니다.

   ![지문 값 복사](common/copy-thumbprint.png)

1. **Meraki Dashboard 설정** 섹션에서 로그아웃 URL 값을 복사하여 컴퓨터에 저장합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Meraki Dashboard에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Meraki Dashboard** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.

   ![사용자 역할](./media/meraki-dashboard-tutorial/user-role.png)

   > [!NOTE]
   > **역할 선택** 옵션이 비활성화되고 선택한 사용자의 기본 역할은 USER입니다.

1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-meraki-dashboard-sso"></a>Meraki Dashboard SSO 구성

1. Meraki Dashboard 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

   ![내 앱 확장](common/install-myappssecure-extension.png)

2. 브라우저에 확장을 추가한 후 **Meraki Dashboard 설정** 을 클릭하면 Meraki Dashboard 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Meraki Dashboard에 로그인합니다. 브라우저 확장에서 애플리케이션을 자동으로 구성하고 3-7단계를 자동화합니다.

   ![설정 구성](common/setup-sso.png)

3. Meraki Dashboard를 수동으로 설정하려면 다른 웹 브라우저 창에서 관리자 권한으로 Meraki Dashboard 회사 사이트에 로그인합니다.

4. **Organization(조직)**  -> **Settings(설정)** 로 차례로 이동합니다.

   ![Meraki Dashboard 설정 탭](./media/meraki-dashboard-tutorial/configure-1.png)

5. Authentication(인증) 아래에서 **SAML SSO** 를 **SAML SSO enabled(SAML SSO 사용)** 로 변경합니다.

   ![Meraki Dashboard 인증](./media/meraki-dashboard-tutorial/configure-2.png)

6. **Add a SAML IdP(SAML IdP 추가)** 를 클릭합니다.

   ![Meraki Dashboard SAML IdP 추가](./media/meraki-dashboard-tutorial/configure-3.png)

7. 이전 섹션의 9단계에서 설명한 대로 Azure Portal에서 복사하고 지정된 형식으로 변환된 **지문** 값을 **X.590 cert SHA1 지문** 텍스트 상자에 붙여넣습니다. 그런 다음 **Save** 를 클릭합니다. 저장되면 Consumer URL(소비자 URL)이 표시됩니다. Consumer URL 값을 복사하고, Azure Portal에 있는 **기본 SAML 구성** 섹션의 **회신 URL** 텍스트 상자에 붙여넣습니다.

   ![Meraki Dashboard 구성](./media/meraki-dashboard-tutorial/configure-4.png)

### <a name="create-meraki-dashboard-admin-roles"></a>Meraki Dashboard 관리자 역할 만들기

1. 다른 웹 브라우저 창에서 관리자 권한으로 Meraki Dashboard 웹 사이트에 로그인합니다.

1. **Organization(조직)**  -> **Administrators(관리자)** 로 차례로 이동합니다.

   ![Meraki Dashboard 관리자](./media/meraki-dashboard-tutorial/user-1.png)

1. SAML administrator roles(SAML 관리자 역할) 섹션에서 **Add SAML role(SAML 역할 추가)** 단추를 클릭합니다.

   ![Meraki Dashboard SAML 역할 추가 단추](./media/meraki-dashboard-tutorial/user-2.png)

1. **meraki_full_admin** 역할을 입력하고, **Organization access(조직 액세스)** 를 **전체(Full)** 로 표시하고 **Create role(역할 만들기)** 을 클릭합니다. **meraki_readonly_admin** 에 대해 이 프로세스를 반복합니다. 이번에는 **Organization access** 를 **Read-only(읽기 전용)** 상자로 표시합니다.

   ![Meraki Dashboard 사용자 만들기](./media/meraki-dashboard-tutorial/user-3.png)

1. 아래 단계에 따라 Meraki Dashboard 역할을 Azure AD SAML 역할에 매핑합니다.

   ![앱 역할의 스크린샷](./media/meraki-dashboard-tutorial/app-role.png)

   a. Azure Portal에서 **앱 등록** 을 클릭합니다.

   b. 모든 애플리케이션을 선택하고 **Meraki Dashboard** 를 클릭합니다.

   c. **앱 역할** 을 클릭하고 **앱 역할 만들기** 를 클릭합니다.

   d. 표시 이름을 `Meraki Full Admin`으로 입력합니다.
   
   e. 허용되는 멤버를 `Users/Groups`로 선택합니다.

   f. 값을 `meraki_full_admin`으로 입력합니다.

   g. 설명을 `Meraki Full Admin`으로 입력합니다.

   h. **저장** 을 클릭합니다. 

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

- Azure Portal에서 이 애플리케이션 테스트를 클릭하면 SSO를 설정한 Meraki Dashboard에 자동으로 로그인됩니다.

- Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Meraki Dashboard 타일을 클릭하면 SSO를 설정한 Meraki Dashboard에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Meraki Dashboard가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).