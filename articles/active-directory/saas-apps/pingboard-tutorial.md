---
title: '자습서: Pingboard와 Azure Active Directory 통합 | Microsoft Docs'
description: Azure Active Directory와 Pingboard 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/01/2021
ms.author: jeedes
ms.openlocfilehash: a4f72b89d24f3563f47a1e3ed94d29c6fe7665ff
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124770038"
---
# <a name="tutorial-azure-active-directory-integration-with-pingboard"></a>자습서: Pingboard와 Azure Active Directory 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Pingboard를 통합하는 방법에 대해 알아봅니다. Azure AD와 Pingboard를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Pingboard에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Pingboard에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Pingboard SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Pingboard에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

* Pingboard에서 [자동 사용자 프로비저닝](./pingboard-provisioning-tutorial.md)을 지원합니다. 

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-pingboard-from-the-gallery"></a>갤러리에서 Pingboard 추가

Pingboard와 Azure AD의 통합을 구성하려면 갤러리의 Pingboard를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Pingboard** 를 입력합니다.
1. 결과 패널에서 **Pingboard** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-pingboard"></a>Pingboard에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Pingboard에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Pingboard의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Pingboard에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Pingboard SSO 구성](#configure-pingboard-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Pingboard 테스트 사용자 만들기](#create-pingboard-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Pingboard에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Pingboard** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에서 `http://app.pingboard.com/sp` URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<ENTITY_ID>.pingboard.com/auth/saml/consume` 패턴을 사용하여 URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<SUBDOMAIN>.pingboard.com/sign_in` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [Pingboard 클라이언트 지원 팀](https://support.pingboard.com/)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **페더레이션 메타데이터 XML** 을 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

7. **Pingboard 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon에게 Pingboard에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Pingboard** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-pingboard-sso"></a>Pingboard SSO 구성

1. Pingboard 쪽에서 SSO를 구성하려면 새 브라우저 창을 열고 Pingboard 계정에 로그인합니다. Single Sign-On을 설정하려면 Pingboard 관리자여야 합니다.

2. 상단 메뉴에서 **앱 > 통합** 을 선택합니다.

    ![Single Sign-on 구성](./media/pingboard-tutorial/integration.png)

3. **통합** 페이지에서 **"Azure Active Directory"** 타일을 찾아 클릭합니다.

    ![Pingboard Single Sign-on 통합](./media/pingboard-tutorial/directory.png)

4. 다음에 나오는 모달에서 **"구성"** 을 클릭합니다.

    ![Pingboard 구성 단추](./media/pingboard-tutorial/configure.png)

5. 다음 페이지에서 "Azure SSO 통합을 사용합니다."라는 메시지를 확인할 수 있습니다. 다운로드한 메타데이터 XML 파일을 메모장에서 열고 **IDP 메타데이터** 에 콘텐츠를 붙여 넣습니다.

    ![Pingboard SSO 구성 화면](./media/pingboard-tutorial/metadata.png)

6. 파일 유효성을 검사하여 모든 것이 올바르면 지금부터 Single Sign-on이 사용됩니다.

### <a name="create-pingboard-test-user"></a>Pingboard 테스트 사용자 만들기

이 섹션은 Pingboard에서 Britta Simon이라는 사용자를 만들기 위한 것입니다. Pingboard는 자동 사용자 프로비전을 지원하며 기본적으로 사용하도록 설정되어 있습니다. 자동 사용자 프로비전 구성 방법에 대한 자세한 내용은 [여기](pingboard-provisioning-tutorial.md)에서 제공합니다.

**사용자를 수동으로 만들어야 할 경우 다음 단계를 수행합니다.**

1. Pingboard 회사 사이트에 관리자로 로그인합니다.

2. **디렉터리** 페이지에서 **"직원 추가"** 단추를 클릭합니다.

    ![직원 추가](./media/pingboard-tutorial/test-user.png)

3. **“직원 추가”** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![피플 초대](./media/pingboard-tutorial/create-name.png)

    a. **전체 이름** 텍스트 상자에서 **Britta Simon** 과 같은 사용자의 전체 이름을 입력합니다.

    b. **이메일** 텍스트 상자에서 **brittasimon@contoso.com** 과 같은 사용자의 이메일 주소를 입력합니다.

    다. **직함** 텍스트 상자에 Britta Simon의 직함을 입력합니다.

    d. **위치** 드롭다운에서 Britta Simon의 위치를 선택합니다.

    e. **추가** 를 클릭합니다.

4. 사용자 추가를 확인하는 확인 화면이 나타납니다.

    ![확인](./media/pingboard-tutorial/confirm-user.png)

    > [!NOTE]
    > Azure Active Directory 계정 보유자는 활성화되기 전에 메일을 받고 링크를 따라 계정을 확인합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Pingboard 로그온 URL로 리디렉션됩니다.  

* Pingboard 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Pingboard에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Pingboard 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우 SSO를 설정한 Pingboard에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Pingboard가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).