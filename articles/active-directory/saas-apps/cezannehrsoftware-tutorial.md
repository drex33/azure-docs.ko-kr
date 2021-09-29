---
title: '자습서: Cezanne HR Software와 Azure Active Directory 통합| Microsoft Docs'
description: Azure Active Directory 및 Cezanne HR Software 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 05/11/2021
ms.author: jeedes
ms.openlocfilehash: 8128c30bc0dddd5f58c1c6ebe7386cb3c2902367
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124773268"
---
# <a name="tutorial-azure-active-directory-integration-with-cezanne-hr-software"></a>자습서: Cezanne HR Software와 Azure Active Directory 통합

이 자습서에서는 Cezanne HR Software를 Azure AD(Azure Active Directory)와 통합하는 방법에 대해 알아봅니다. Cezanne HR Software를 Azure AD와 통합하면 다음을 수행할 수 있습니다.

* Cezanne HR Software에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 Cezanne HR Software에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Cezanne HR Software SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Cezanne HR Software에서 **SP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-cezanne-hr-software-from-the-gallery"></a>갤러리에서 Cezanne HR Software 추가

Cezanne HR Software의 Azure AD 통합을 구성하려면 갤러리의 Cezanne HR Software를 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **Cezanne HR Software** 를 입력합니다.
1. 결과 패널에서 **Cezanne HR Software** 를 선택한 후 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-cezanne-hr-software"></a>Cezanne HR Software에 대한 Azure AD SSO 구성 및 테스트

**B. Simon** 이라는 테스트 사용자를 사용하여 Cezanne HR Software에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Cezanne HR Software의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Cezanne HR Software에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Cezanne HR Software SSO 구성](#configure-cezanne-hr-software-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Cezanne HR Software 테스트 사용자 만들기](#create-cezanne-hr-software-test-user)** - B.Simon의 Azure AD 표현과 연결되는 대응 사용자를 Cezanne HR Software에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Cezanne HR Software** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾은 후 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **로그온 URL** 텍스트 상자에서 `https://w3.cezanneondemand.com/CezanneOnDemand/-/<tenantidentifier>` 패턴을 사용하는 URL을 입력합니다.

    b. **식별자(엔터티 ID)** 텍스트 상자에 `https://w3.cezanneondemand.com/CezanneOnDemand/` URL을 입력합니다.

    다. **회신 URL** 텍스트 상자에 다음 패턴으로 URL을 입력합니다.`https://w3.cezanneondemand.com:443/cezanneondemand/-/<tenantidentifier>/Saml/samlp`
    
    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 로그온 URL 및 회신 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 가져오려면 [Cezanne HR Software 클라이언트 지원 팀](https://cezannehr.com/services/support/)에 문의합니다.

5. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **다운로드** 를 클릭하여 요구 사항에 따라 제공된 옵션에서 **인증서(Base64)** 를 다운로드한 다음, 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

6. **Cezanne HR Software 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B. Simon에게 Cezanne HR Software에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Cezanne HR Software** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-cezanne-hr-software-sso"></a>Cezanne HR Software SSO 구성

1. 다른 웹 브라우저 창에서 Cezanne HR Software 테넌트에 관리자로 로그인합니다.

2. 사이드 메뉴에서 **관리** 를 선택합니다. 그런 다음, **보안 설정** 으로 이동하여 **Single Sign-On** 을 클릭합니다.

    ![[보안 설정] 및 [Single Sign-On 구성]이 선택된 Cezanne H R Software 테넌트를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/settings.png)

3. **다음 SSO(Single Sign-On) Service를 사용한 사용자 로그온 허용** 패널에서 **SAML 2.0** 상자를 선택하고 **고급 구성** 옵션을 선택합니다.

    ![SAML 2.0 및 [고급 구성]이 선택된 [사용자 허용] 창을 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/configuration.png)

4. **새로 추가** 단추를 클릭합니다.

    ![[새로 추가] 단추를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/new-button.png)

5. **SAML 2.0 IDENTITY PROVIDERS** 섹션에서 다음 필드를 입력하고 **확인** 을 클릭합니다.

    ![이 단계에서 설명한 값을 입력할 수 있는 창을 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/identity-provider.png)

    a. **표시 이름** - ID 공급자의 이름을 표시 이름으로 입력합니다.

    b. **엔터티 식별자** - Azure Portal에서 복사한 Azure Ad 식별자 값을 엔터티 식별자 텍스트 상자에 붙여넣습니다.

    다. **SAML 바인딩** - SAML 바인딩을 'POST'로 변경합니다.

    d. **보안 토큰 서비스 엔드포인트** - Azure Portal에서 복사한 로그인 URL의 값을 보안 토큰 서비스 엔드포인트 텍스트 상자에 붙여넣습니다.

    e. **사용자 ID 특성 이름** - 사용자 ID 특성 이름 텍스트 상자에 'http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress '를 입력합니다.

    f. **공개 키 인증서** - 업로드 아이콘을 클릭하여 Azure Portal에서 다운로드한 인증서를 업로드합니다.

6. 확인을 클릭합니다.

7. 저장 단추를 클릭합니다. 

    ![Single Sign-On 구성의 [저장] 단추를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/save-button.png)

### <a name="create-cezanne-hr-software-test-user"></a>Cezanne HR Software 테스트 사용자 만들기

Azure AD 사용자가 Cezanne HR Software에 로그인 할 수 있도록 하려면 Cezanne HR Software로 프로비전되어야 합니다. Cezanne HR Software의 경우 프로비저닝은 수동 작업입니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Cezanne HR Software 회사 사이트에 관리자 권한으로 로그인합니다.

2. 사이드 메뉴에서 **관리** 를 선택합니다. 그런 다음, **사용자** 로 이동하여 **새 사용자 추가** 를 클릭합니다.

    ![[사용자 관리] 및 [새 사용자 추가]가 선택된 Cezanne H R Software 테넌트를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/manage-users.png "새 사용자")

3. **사람 세부 정보** 섹션에서 다음 단계를 수행합니다.

    ![이 단계에서 설명한 값을 입력할 수 있는 [사용자 세부 정보] 섹션을 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/details.png "새 사용자")

    a. **내부 사용자** 를 OFF로 설정합니다.

    b. 이름 입력   

    다. 성 입력

    d. 이메일 주소를 입력합니다.

4. **계정 정보** 섹션에서 다음 단계를 수행합니다.

    ![이 단계에서 설명한 값을 입력할 수 있는 [계정 정보]를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/account.png "새 사용자")

    a. **사용자 이름** 텍스트 상자에서 Brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    b. **암호** 텍스트 상자에서 사용자에 대한 암호를 입력합니다.

    다. **보안 역할** 로 **HR 전문가** 를 선택합니다.

    d. **확인** 을 클릭합니다.
    ![스크린샷은 확인 단추를 보여줍니다.](https://user-images.githubusercontent.com/80324891/115694644-f6eb5700-a358-11eb-9b23-a87a24921052.png)

5. **Single Sign-On** 탭으로 이동하고 **SAML 2.0 식별자** 영역에서 **새로 추가** 를 선택합니다.

    ![[새로 추가]를 선택할 수 있는 Single Sign-On 탭을 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/single-sign-on.png "사용자")

6. **ID 공급자** 에 대해 ID 공급자를 선택하고 **사용자 식별자** 텍스트 상자에 사용자 이메일 주소를 입력합니다.

    ![ID 공급자 및 사용자 식별자를 선택할 수 있는 SAML 2.0 식별자를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/user-identifier.png "사용자")

7. **저장** 단추를 클릭합니다.

    ![[사용자 설정]의 [저장] 단추를 보여주는 스크린샷](./media/cezannehrsoftware-tutorial/save.png "사용자")

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Cezanne HR Software 로그온 URL로 리디렉션됩니다. 

* Cezanne HR Software 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Cezanne HR Software 타일을 클릭하면 Cezanne HR Software 로그온 URL로 리디렉션됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Cezanne HR Software가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).