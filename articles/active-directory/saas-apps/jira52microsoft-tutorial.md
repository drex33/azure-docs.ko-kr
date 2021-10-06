---
title: '자습서: JIRA SAML SSO by Microsoft(V5.2)와 Azure AD SSO 통합'
description: Azure Active Directory와 JIRA SAML SSO by Microsoft(V5.2) 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/08/2021
ms.author: jeedes
ms.openlocfilehash: 5254b7bcf851685572adbdc3d18c1fc5560a34d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128633247"
---
# <a name="tutorial-azure-ad-sso-integration-with-jira-saml-sso-by-microsoft-v52"></a>자습서: JIRA SAML SSO by Microsoft(V5.2)와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 JIRA SAML SSO by Microsoft(V5.2)를 통합하는 방법에 대해 알아봅니다. Azure AD와 JIRA SAML SSO by Microsoft(V5.2)를 통합하면 다음 작업을 수행할 수 있습니다.

* JIRA SAML SSO by Microsoft(V5.2)에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 해당 Azure AD 계정으로 JIRA SAML SSO by Microsoft(V5.2)에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="description"></a>Description

Atlassian JIRA 서버와 함께 Microsoft Azure Active Directory 계정을 사용하여 Single Sign-On을 사용하도록 설정합니다. 이러한 방식으로 모든 조직 사용자는 Azure AD 자격 증명을 사용하여 JIRA 애플리케이션에 로그인할 수 있습니다. 이 플러그 인은 페더레이션에 대해 SAML 2.0을 사용합니다.

## <a name="prerequisites"></a>사전 요구 사항

JIRA SAML SSO by Microsoft(V5.2)와 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

- Azure AD 구독
- JIRA Core 및 Software 5.2가 Windows 64비트 버전에 설치 및 구성되어 있어야 합니다.
- JIRA 서버에서 HTTPS를 사용해야 합니다.
- 지원되는 JIRA 플러그 인 버전은 아래 섹션에 설명되어 있습니다.
- JIRA 서버가 인터넷에 연결되어 있고 인증을 위해 특히 Azure AD 로그인 페이지에 접속되고 Azure AD에서 토큰을 받을 수 있어야 합니다.
- JIRA에 관리자 자격 증명이 설정되어 있어야 합니다.
- JIRA에서 WebSudo를 사용하지 않아야 합니다.
- JIRA 서버 애플리케이션에서 생성된 테스트 사용자.

> [!NOTE]
> 이 자습서의 단계를 테스트하기 위해 JIRA의 프로덕션 환경을 사용하는 것은 바람직하지 않습니다. 애플리케이션 개발 또는 스테이징 환경에서 통합을 먼저 테스트 한 다음 프로덕션 환경을 사용하세요.

이 자습서의 단계를 테스트하려면 다음 권장 사항을 준수해야 합니다.

- 꼭 필요한 경우가 아니면 프로덕션 환경을 사용하지 마세요.
- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

## <a name="supported-versions-of-jira"></a>지원되는 JIRA 버전

* JIRA Core 및 Software: 5.2.
* JIRA는 6.0에서 7.12도 지원합니다. 자세한 내용을 보려면 [JIRA SAML SSO by Microsoft](jiramicrosoft-tutorial.md)를 클릭하세요.

> [!NOTE]
> JIRA 플러그 인은 Ubuntu 버전 16.04에서도 작동한다는 점에 유의하세요.

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* JIRA SAML SSO by Microsoft(V5.2)는 **SP** 시작 SSO를 지원합니다.

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>갤러리에서 JIRA SAML SSO by Microsoft(V5.2) 추가

JIRA SAML SSO by Microsoft(V5.2)가 Azure AD에 통합되도록 구성하려면 갤러리에서 JIRA SAML SSO by Microsoft(V5.2)를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **JIRA SAML SSO by Microsoft(V5.2)** 를 입력합니다.
1. 결과 패널에서 **JIRA SAML SSO by Microsoft(V5.2)** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-jira-saml-sso-by-microsoft-v52"></a>JIRA SAML SSO by Microsoft(V5.2)에 대한 Azure AD SSO 구성 및 테스트

이 섹션에서는 **Britta Simon** 이라는 테스트 사용자를 기반으로 하여 JIRA SAML SSO by Microsoft(V5.2)에서 Azure AD Single Sign-On을 구성하고 테스트합니다. Single Sign-On이 작동하려면 Azure AD 사용자와 JIRA SAML SSO by Microsoft(V5.2)의 관련 사용자 간에 연결 관계를 설정해야 합니다.

JIRA SAML SSO by Microsoft(V5.2)에서 Azure AD Single Sign-On을 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - Britta Simon으로 Azure AD Single Sign-On을 테스트하는 데 사용합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - Britta Simon이 Azure AD Single Sign-on을 사용할 수 있도록 합니다.
2. **[JIRA SAML SSO by Microsoft SSO(V5.2) 구성](#configure-jira-saml-sso-by-microsoft-v52-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[JIRA SAML SSO by Microsoft(V5.2) 테스트 사용자 만들기](#create-jira-saml-sso-by-microsoft-v52-test-user)** - Britta Simon의 Azure AD 표현과 연결된 사용자를 JIRA SAML SSO by Microsoft(V5.2)에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

1. Azure Portal의 **JIRA SAML SSO by Microsoft(V5.2)** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `https://<domain:port>/` 패턴을 사용하는 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<domain:port>/plugins/servlet/saml/auth` 패턴을 사용하여 URL을 입력합니다.

    다. **로그인 URL** 텍스트 상자에서 `https://<domain:port>/plugins/servlet/saml/auth` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 명명된 URL인 경우 포트는 선택 사항입니다. 이러한 값은 JIRA 플러그 인 구성 중에 수신되며 자습서의 뒷부분에 설명되어 있습니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

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

이 섹션에서는 B.Simon이 Azure Single Sign-On을 사용할 수 있도록 JIRA SAML SSO by Microsoft(V5.2)에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **JIRA SAML SSO by Microsoft(V5.2)** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-jira-saml-sso-by-microsoft-v52-sso"></a>JIRA SAML SSO by Microsoft(V5.2) SSO 구성

1. 다른 웹 브라우저 창에서 JIRA 인스턴스에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **추가 기능** 을 클릭합니다.

    ![스크린샷은 설정 메뉴에서 선택한 추가 기능을 보여줍니다.](./media/jira52microsoft-tutorial/menu.png)

3. 추가 기능 탭 섹션에서 **추가 기능 관리** 를 클릭합니다.

    ![스크린샷은 추가 기능 탭에서 선택한 추가 기능 관리를 보여줍니다.](./media/jira52microsoft-tutorial/dashboard.png)

4. [Microsoft 다운로드 센터](https://www.microsoft.com/download/details.aspx?id=56521)에서 플러그 인을 다운로드합니다. **업로드 추가 기능** 메뉴를 사용하여 Microsoft에서 제공하는 플러그 인을 수동으로 업로드합니다. 플러그 인 다운로드에는 [Microsoft 서비스 계약](https://www.microsoft.com/servicesagreement/)이 적용됩니다.

    ![스크린샷은 추가 기능 업로드 링크가 호출된 추가 기능 관리를 보여줍니다.](./media/jira52microsoft-tutorial/service.png)

5. 플러그 인이 설치되면 **사용자가 설치한** 추가 기능 섹션에 표시됩니다. **구성** 을 클릭하여 새 플러그 인을 구성합니다.

    ![스크린샷은 구성이 선택된 Jira용 Azure AD SAML Single Sign-On 섹션을 보여줍니다.](./media/jira52microsoft-tutorial/configure-plugin.png)

6. 구성 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 Microsoft Jira SSO Connector 구성 페이지를 보여줍니다.](./media/jira52microsoft-tutorial/configuration.png)

    > [!TIP]
    > 메타데이터를 확인하는 데 오류가 없도록 앱에 매핑된 인증서가 하나만 있는지 확인합니다. 인증서가 여러 개 있으면 메타데이터를 확인할 때 관리자에게 오류가 표시됩니다.

    a. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **메타데이터 URL** 텍스트 상자에 붙여넣고 **해결** 단추를 클릭합니다. 그러면 IdP 메타데이터 URL을 읽어와서 모든 필드 정보가 채워집니다.

    b. **식별자, 회신 URL 및 로그인 URL** 값을 복사하여 Azure Portal의 **기본 SAML 구성** 섹션에 있는 **식별자, 회신 URL 및 로그인 URL** 텍스트 상자에 각각 붙여넣습니다.

    다. **Login Button Name**(로그인 단추 이름)에 조직이 사용자의 로그인 화면에 표시하려는 단추의 이름을 입력합니다.

    d. **SAML User ID Locations**(SAML 사용자 ID 위치)에서 **User ID is in the NameIdentifier element of the Subject statement**(사용자 ID는 Subject 문의 NameIdentifier 요소에 있습니다.) 또는 **User ID is in an Attribute element**(사용자 ID는 Attribute 요소에 있습니다.)를 선택합니다.  이 ID는 JIRA 사용자 ID여야 합니다. 사용자 ID가 일치하지 않으면 시스템에서 사용자 로그인을 허용하지 않습니다.

    > [!Note]
    > 기본 SAML 사용자 ID 위치는 이름 식별자입니다. 이것을 특성 옵션으로 변경하고 적절한 특성 이름을 입력할 수 있습니다.

    e. **User ID is in an Attribute element**(사용자 ID는 Attribute 요소에 있습니다.) 옵션을 선택하는 경우 **특성 이름** 텍스트 상자에 사용자 ID가 필요한 특성의 이름을 입력합니다. 

    f. Azure AD에서 페더레이션된 도메인(예: ADFS 등)을 사용하는 경우 **Enable Home Realm Discovery**(홈 영역 검색 사용) 옵션을 클릭하고 **도메인 이름** 을 구성합니다.

    g. **도메인 이름** 에 ADFS 기반 로그인인 경우 여기에 도메인 이름을 입력합니다.

    h. 사용자가 JIRA에서 로그아웃할 때 Azure AD에서 로그아웃하려면 **Single Sign-Out 사용** 을 선택합니다. 

    i. **저장** 단추를 클릭하여 설정을 저장합니다.

    > [!NOTE]
    > 설치 및 문제 해결에 대한 자세한 내용은 [MS JIRA SSO 커넥터 관리자 가이드](./ms-confluence-jira-plugin-adminguide.md)를 참조하시기 바라며, [FAQ](./ms-confluence-jira-plugin-adminguide.md)도 도움이 될 것입니다.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>JIRA SAML SSO by Microsoft(V5.2) 테스트 사용자 만들기

Azure AD 사용자가 JIRA 온-프레미스 서버에 로그인할 수 있게 하려면 해당 사용자를 JIRA 온-프레미스 서버로 프로비저닝해야 합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. JIRA 온-프레미스 서버에 관리자로 로그인합니다.

2. 마우스로 선 위를 가리키고 **사용자 관리** 를 클릭합니다.

    ![스크린샷은 설정 메뉴에서 선택한 사용자 관리를 보여줍니다.](./media/jira52microsoft-tutorial/user.png)

3. 리디렉션된 [관리자 액세스] 페이지에서 **암호** 를 입력하고 **확인** 단추를 클릭합니다.

    ![스크린샷은 자격 증명을 입력하는 관리자 액세스 페이지를 보여줍니다.](./media/jira52microsoft-tutorial/access.png)

4. **사용자 관리** 탭 섹션 아래에서  **사용자 만들기** 를 클릭합니다.

    ![스크린샷은 사용자를 만들 수 있는 사용자 관리 탭을 보여줍니다.](./media/jira52microsoft-tutorial/create-user.png) 

5. **“새 사용자 만들기”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 이 단계에서 정보를 입력할 수 있는 새 사용자 만들기 대화 상자를 보여줍니다.](./media/jira52microsoft-tutorial/new-user.png)

    a. **이메일 주소** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    b. **전체 이름** 텍스트 상자에서 Britta Simon과 같은 사용자의 전체 이름을 입력합니다.

    다. **사용자 이름** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. **암호** 텍스트 상자에서 사용자에 대한 암호를 입력합니다.

    e. **사용자 만들기** 를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 JIRA SAML SSO by Microsoft(V5.2) 로그온 URL로 리디렉션됩니다. 

* JIRA SAML SSO by Microsoft(V5.2) 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 JIRA SAML SSO by Microsoft(V5.2) 타일을 클릭하면 JIRA SAML SSO by Microsoft(V5.2) 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

JIRA SAML SSO by Microsoft(V5.2)가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).