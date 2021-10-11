---
title: '자습서: Netskope 관리자 콘솔과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 Netskope 관리자 콘솔 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2021
ms.author: jeedes
ms.openlocfilehash: 64551f24d3273f757511a0727779073026fb11c1
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232511"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>자습서: Netskope 관리자 콘솔과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Netskope 관리자 콘솔과 Azure AD(Azure Active Directory)를 통합하는 방법을 알아봅니다. Netskope 관리자 콘솔과 Azure AD를 통합하면 다음을 수행할 수 있습니다.

- Azure AD에서 Netskope 관리자 콘솔에 액세스할 수 있는 사용자를 제어합니다.
- 사용자가 자신의 Azure AD 계정으로 Netskope 관리자 콘솔에 자동으로 로그인되도록 설정합니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음 항목이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- Netskope 관리자 콘솔 SSO(Single Sign-On)가 설정된 구독입니다.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- Netskope Administrator Console은 **SP 및 IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-netskope-administrator-console-from-the-gallery"></a>갤러리에서 Netskope Administrator Console 추가

Azure AD로의 Netskope 관리자 콘솔 통합을 구성하려면 갤러리에서 Netskope 관리자 콘솔을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Netskope 관리자 콘솔** 을 입력합니다.
1. 결과 패널에서 **Netskope 관리자 콘솔** 을 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-netskope-administrator-console"></a>Netskope Administrator Console에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Netskope 관리자 콘솔로 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Netskope 관리자 콘솔의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Netskope Administrator Console에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
   1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
   1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Netskope 관리자 콘솔 SSO 구성](#configure-netskope-administrator-console-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   1. **[Netskope 관리자 콘솔 테스트 사용자 만들기](#create-netskope-administrator-console-test-user)** - 사용자의 Azure AD 표현과 연결된 B. Simon에 해당하는 사용자를 Netskope 관리자 콘솔에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Netskope Administrator Console** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

   **회신 URL** 텍스트 상자에서 `https://<tenant_host_name>/saml/acs` 패턴을 사용하여 URL을 입력합니다.

   > [!NOTE]
   > 이 값은 실제 값이 아닙니다. 실제 회신 URL로 값을 업데이트합니다. 자습서의 뒷부분에서 설명하는 값을 가져옵니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

   **로그인 URL** 텍스트 상자에서 `https://<tenantname>.goskope.com` 패턴을 사용하여 URL을 입력합니다.

   > [!NOTE]
   > 로그온 URL 값은 실제 값이 아닙니다. 로그온 URL 값을 실제 로그온 URL로 업데이트하세요. 로그온 URL 값을 가져오려면 [Netskope 관리자 콘솔 클라이언트 지원 팀](mailto:support@netskope.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Netskope 관리자 콘솔 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로, SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

   ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 Netskope 관리자 콘솔 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.

   | Name       | 원본 특성   |
   | ---------- | ------------------ |
   | 관리자 역할 | user.assignedroles |

   > [!NOTE]
   > [여기](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)를 클릭하여 Azure AD에서 역할을 만드는 방법을 알아봅니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Netskope 관리자 콘솔 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Netskope 관리자 콘솔에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Netskope 관리자 콘솔** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 위에서 설명한 대로 역할을 설정한 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-netskope-administrator-console-sso"></a>Netskope 관리자 콘솔 SSO 구성

1. 브라우저에서 새 탭을 열고, 관리자 권한으로 Netskope 관리자 콘솔 회사 사이트에 로그인합니다.

1. 왼쪽 탐색 창에서 **설정** 을 클릭합니다.

   ![스크린샷은 탐색 창에서 선택한 설정을 보여줍니다.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. **관리** 탭을 클릭합니다.

   ![스크린샷은 설정에서 선택한 관리를 보여줍니다.](./media/netskope-cloud-security-tutorial/administration.png)

1. **SSO** 탭을 클릭합니다.

   ![스크린샷은 관리에서 선택한 SSO를 보여줍니다.](./media/netskope-cloud-security-tutorial/tab.png)

1. **네트워크 설정** 섹션에서 다음 단계를 수행합니다.

   ![스크린샷은 설명된 값을 입력할 수 있는 네트워크 설정을 보여줍니다.](./media/netskope-cloud-security-tutorial/network.png)

   a. **Assertion Consumer Service URL** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **회신 URL** 텍스트 상자에 붙여넣습니다.

   b. **서비스 공급자 엔터티 ID** 값을 복사하고 Azure Portal에 있는 **기본 SAML 구성** 섹션의 **식별자** 텍스트 상자에 붙여넣습니다.

1. **SSO/SLO 설정** 섹션 아래의 **설정 편집** 을 클릭합니다.

   ![스크린샷은 설정 편집을 선택할 수 있는 SSO / SLO 설정을 보여줍니다.](./media/netskope-cloud-security-tutorial/settings.png)

1. **설정** 팝업 창에서 다음 단계를 수행합니다.

   ![스크린샷은 설명된 값을 입력할 수 있는 설정 대화 상자를 보여줍니다.](./media/netskope-cloud-security-tutorial/configuration.png)

   a. **SSO 사용** 을 선택합니다.

   b. Azure Portal에서 복사한 **IDP URL** 값을 **IDP URL** 텍스트 상자에 붙여넣습니다.

   다. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **IDP 엔터티 ID** 텍스트 상자에 붙여넣습니다.

   d. 다운로드한 Base64로 인코딩된 인증서를 메모장에서 열고, 내용을 클립보드에 복사한 다음, **IDP 인증서** 텍스트 상자에 붙여넣습니다.

   e. **SSO 사용** 을 선택합니다.

   f. Azure Portal에서 복사한 **로그아웃 URL** 값을 **IDP SLO URL** 텍스트 상자에 붙여넣습니다.

   g. **제출** 을 클릭합니다.

### <a name="create-netskope-administrator-console-test-user"></a>Netskope 관리자 콘솔 테스트 사용자 만들기

1. 브라우저에서 새 탭을 열고, 관리자 권한으로 Netskope 관리자 콘솔 회사 사이트에 로그인합니다.

1. 왼쪽 탐색 창에서 **설정** 을 클릭합니다.

   ![스크린샷은 선택된 설정을 보여줍니다.](./media/netskope-cloud-security-tutorial/configure-settings.png)

1. **활성 플랫폼** 탭을 클릭합니다.

   ![스크린샷은 설정에서 선택한 활성 플랫폼을 보여줍니다.](./media/netskope-cloud-security-tutorial/user-1.png)

1. **사용자** 탭을 클릭합니다.

   ![스크린샷은 활성 플랫폼에서 선택한 사용자를 보여줍니다.](./media/netskope-cloud-security-tutorial/add-user.png)

1. **사용자 추가** 를 클릭합니다.

   ![스크린샷은 사용자 추가를 선택할 수 있는 사용자 대화 상자를 보여줍니다.](./media/netskope-cloud-security-tutorial/user-add.png)

1. 추가할 사용자의 이메일 주소를 입력하고 **추가** 를 클릭합니다.

   ![스크린샷은 사용자 목록을 입력할 수 있는 사용자 추가를 보여줍니다.](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

#### <a name="sp-initiated"></a>SP 시작:

- Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Netskope Administrator Console 로그온 URL로 리디렉션됩니다.

- Netskope Administrator Console 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

- Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Netskope Administrator Console에 자동으로 로그인됩니다.

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Netskope Administrator Console 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 Netskope Administrator Console에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Netskope Administrator Console이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).