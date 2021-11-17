---
title: '자습서: Zscaler와 Azure Active Directory SSO(Single Sign-On) 연결 | Microsoft Docs'
description: Azure Active Directory와 Zscaler 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: jeedes
ms.openlocfilehash: 428fde009f0b932d6dbc80e4cbcd986a7aff6aec
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319893"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zscaler"></a>자습서: Zscaler와 Azure Active Directory SSO(Single Sign-On) 연결

이 자습서에서는 Azure AD(Azure Active Directory)와 Zscaler를 연결하는 방법에 대해 알아봅니다. Azure AD와 Zscaler를 연결하는 경우 다음을 수행할 수 있습니다.

- Zscaler에 액세스할 수 있는 사용자를 Azure AD에서 제어합니다.
- 사용자가 자신의 Azure AD 계정으로 Zscaler에 자동으로 로그인되도록 설정할 수 있습니다.
- 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
- Zscaler SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

- Zscaler는 **SP** 시작 SSO를 지원합니다.
- Zscaler는 **Just-In-Time** 사용자 프로비저닝을 지원합니다.

## <a name="adding-zscaler-from-the-gallery"></a>갤러리에서 Zscaler 추가

Zscaler가 Azure AD에 통합되도록 구성하려면 갤러리의 Zscaler를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Zscaler** 를 입력합니다.
1. 결과 패널에서 **Zscaler** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-zscaler"></a>Zscaler에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Zscaler에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Zscaler의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Zscaler에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
   1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
   1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Zscaler SSO 구성](#configure-zscaler-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
   1. **[Zscaler 테스트 사용자 만들기](#create-zscaler-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Zscaler에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Zscaler** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 필드에 대한 값을 입력합니다.

   **로그인 URL** 텍스트 상자에서 `https://<companyname>.zscaler.net` 패턴을 사용하여 URL을 입력합니다.

   > [!NOTE]
   > 이 값은 실제 값이 아닙니다. 이 값을 실제 로그온 URL로 업데이트합니다. 이 값을 얻으려면 [Zscaler 클라이언트 지원 팀](https://www.zscaler.com/company/contact)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. Zscaler 애플리케이션에는 특정 형식의 SAML 어설션이 필요하므로 사용자 지정 특성 매핑을 SAML 토큰 특성 구성에 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다. **편집** 아이콘을 클릭하여 **사용자 특성** 대화 상자를 엽니다.

   ![스크린샷은 편집 아이콘이 선택된 사용자 특성을 보여줍니다.](common/edit-attribute.png)

1. 위에서 언급한 특성 외에도, Zscaler 애플리케이션에는 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. **사용자 특성** 대화 상자의 **사용자 클레임** 섹션에서 다음 단계를 수행하여 아래 표와 같은 SAML 토큰 특성을 추가합니다.

   | 속성     | 원본 특성   |
   | -------- | ------------------ |
   | memberOf | user.assignedroles |

   a. **새 클레임 추가** 를 클릭하여 **사용자 클레임 관리** 대화 상자를 엽니다.

   b. **이름** 텍스트 상자에서 해당 행에 표시된 특성 이름을 입력합니다.

   다. **네임스페이스** 를 비워 둡니다.

   d. 원본을 **특성** 으로 선택합니다.

   e. **원본 특성** 목록에서 해당 행에 표시된 특성 값을 입력합니다.

   f. **저장** 을 클릭합니다.

   > [!NOTE]
   > [여기](../develop/howto-add-app-roles-in-azure-ad-apps.md#app-roles-ui)를 클릭하여 Azure AD에서 역할을 구성하는 방법을 알아봅니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드하고 본인의 컴퓨터에 저장합니다.

   ![인증서 다운로드 링크](common/certificatebase64.png)

1. **Zscaler 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Zscaler에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Zscaler** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 위에서 설명한 대로 역할을 설정한 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-zscaler-sso"></a>Zscaler SSO 구성

1. Zscaler 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

   ![내 앱 확장](common/install-myappssecure-extension.png)

1. 브라우저에 확장을 추가한 후 **Zscaler 설정** 을 클릭하면 Zscaler 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Zscaler에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-6단계를 자동으로 수행합니다.

   ![SSO 설정](common/setup-sso.png)

1. Zscaler를 수동으로 설정하려면 새 웹 브라우저 창을 열고 Zscaler 회사 사이트에 관리자로 로그인하여 다음 단계를 수행합니다.

1. **관리 > 인증 > 인증 설정** 으로 이동하고 다음 단계를 수행합니다.

   ![스크린샷은 설명된 단계가 있는 Zscaler One 사이트를 보여줍니다.](./media/zscaler-tutorial/ic800206.png "관리")

   a. 인증 형식에서 **SAML** 을 선택합니다.

   b. **SAML 구성** 을 클릭합니다.

1. **SAML 편집** 창에서 다음 단계를 수행하고 저장을 클릭합니다.

   ![사용자 및 인증 관리](./media/zscaler-tutorial/ic800208.png "사용자 & 인증 관리")

   a. Azure Portal에서 복사한 **로그인 URL** 값을 **SAML 포털 URL** 텍스트 상자에 붙여넣습니다.

   b. **로그인 이름 특성** 텍스트 상자에 **NameID** 을 입력합니다.

   다. **업로드** 를 클릭하여 Azure Portal에서 다운로드한 Azure SAML 서명 인증서를 **공용 SSL 인증서** 에 업로드합니다.

   d. **SAML 자동 프로비전 사용** 을 선택/해제합니다.

   e. displayName 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **사용자 표시 이름 특성** 텍스트 상자에 **displayName** 을 입력합니다.

   f. memberOf 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **그룹 이름 특성** 텍스트 상자에 **memberOf** 를 입력합니다.

   g. 부서 특성에 대해 SAML 자동 프로비전을 사용하도록 설정하려는 경우 **부서 이름 특성** 텍스트 상자에 **부서** 를 입력합니다.

   h. **저장** 을 클릭합니다.

1. **사용자 인증 구성** 대화 상자 페이지에서 다음 단계를 수행합니다.

   ![스크린샷은 활성화가 선택된 사용자 인증 구성 대화 상자를 보여줍니다.](./media/zscaler-tutorial/ic800207.png)

   a. 왼쪽 아래 근처에 있는 **활성화** 메뉴를 마우스로 가리킵니다.

   b. **활성화** 를 클릭합니다.

## <a name="configuring-proxy-settings"></a>프록시 설정 구성

### <a name="to-configure-the-proxy-settings-in-internet-explorer"></a>Internet Explorer에서 프록시 설정을 구성하려면

1. **Internet Explorer** 를 시작합니다.

1. **도구** 메뉴에서 **인터넷 옵션** 을 선택하여 **인터넷 옵션** 대화 상자를 엽니다.

   ![인터넷 옵션](./media/zscaler-tutorial/ic769492.png "인터넷 옵션")

1. **연결** 탭을 클릭합니다.

   ![연결](./media/zscaler-tutorial/ic769493.png "Connections")

1. **LAN 설정** 을 클릭하여 **LAN 설정** 대화 상자를 엽니다.

1. 프록시 서버 섹션에서 다음 단계를 수행합니다.

   ![프록시 서버](./media/zscaler-tutorial/ic769494.png "프록시 서버")

   a. **사용자 LAN의 프록시 서버 사용** 을 선택합니다.

   b. [주소] 텍스트 상자에 **gateway.zscaler.net** 을 입력합니다.

   다. 포트 텍스트 상자에 **80** 을 입력합니다.

   d. **로컬 주소의 바이패스 프록시 서버** 를 선택합니다.

   e. **확인** 을 클릭하여 **LAN(Local Area Network) 설정** 대화 상자를 닫습니다.

1. **확인** 을 클릭하여 **인터넷 옵션** 대화 상자를 닫습니다.

### <a name="create-zscaler-test-user"></a>Zscaler 테스트 사용자 만들기

이 섹션에서는 Zscaler에서 Britta Simon이라는 사용자를 만듭니다. Zscaler는 기본적으로 사용하도록 설정되는 Just-In-Time 사용자 프로비저닝을 지원합니다. 이 섹션에 작업 항목이 없습니다. Zscaler에 사용자가 아직 없는 경우 인증 후에 새 사용자가 만들어집니다.

> [!Note]
> 사용자를 수동으로 만들어야 하는 경우 [Zscaler 지원 팀](https://www.zscaler.com/company/contact)에 문의해야 합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

- Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Zscaler 로그온 URL로 리디렉션됩니다.

- Zscaler 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

- Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Zscaler 타일을 클릭하면 Zscaler 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Zscaler가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
