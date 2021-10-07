---
title: '자습서: HubSpot과 Azure AD SSO 통합'
description: Azure Active Directory와 HubSpot 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 09/07/2021
ms.author: jeedes
ms.openlocfilehash: b4e91ab02e02da602010cb06ff0b9e3f88d31c64
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670164"
---
# <a name="tutorial-azure-ad-sso-integration-with-hubspot"></a>자습서: HubSpot과 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 HubSpot을 통합하는 방법에 대해 알아봅니다. Azure AD와 HubSpot을 통합하면 다음을 수행할 수 있습니다.

* HubSpot에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 HubSpot에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

HubSpot과 Azure AD 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* Single Sign-On을 사용하도록 설정한 HubSpot 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트한 후 Azure AD에 HubSpot을 통합합니다.

HubSpot에서 지원하는 기능은 다음과 같습니다.

* **SP 시작 Single Sign-On**.
* **IDP 시작 Single Sign-On**.

## <a name="add-hubspot-from-the-gallery"></a>갤러리에서 HubSpot 추가

HubSpot의 Azure AD 통합을 구성하려면 갤러리의 HubSpot을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **HubSpot** 을 입력합니다.
1. 결과 패널에서 **HubSpot** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-hubspot"></a>HubSpot에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 HubSpot에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 HubSpot의 관련 사용자 간에 연결 관계를 설정해야 합니다.

HubSpot에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[HubSpot SSO 구성](#configure-hubspot-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[HubSpot 테스트 사용자 만들기](#create-hubspot-test-user)** - B.Simon의 Azure AD 표현과 연결되는 해당 사용자를 HubSpot에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

1. Azure Portal의 **HubSpot** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 창에서 **IDP 시작 모드** 를 구성하려면 다음 단계를 수행합니다.

    1. **식별자** 상자에 https:\//api.hubspot.com/login-api/v1/saml/login?portalId=\<CUSTOMER ID\> 패턴을 사용하는 URL을 입력합니다.

    1. **회신 URL** 상자에 https:\//api.hubspot.com/login-api/v1/saml/acs?portalId=\<CUSTOMER ID\> 패턴을 사용하는 URL을 입력합니다.

    > [!NOTE]
    > URL 서식을 지정하기 위해 Azure Portal의 **기본 SAML 구성** 창에 표시된 패턴을 참조할 수도 있습니다.

1. *SP 시작* 모드로 애플리케이션을 구성하려는 경우 다음을 수행합니다.

    1. **추가 URL 설정** 을 선택합니다.

    1. **로그온 URL** 상자에 **https:\//app.hubspot.com/login** 을 입력합니다.

1. **SAML로 Single Sign-On 설정** 창의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 옆에 있는 **다운로드** 를 선택합니다. 요구 사항에 따라 다운로드 옵션을 선택합니다. 인증서를 컴퓨터에 저장합니다.

    ![인증서(Base64) 다운로드 옵션](common/certificatebase64.png)

1. **HubSpot 설정** 섹션에서 요구 사항에 따라 다음 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 HubSpot에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **HubSpot** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-hubspot-sso"></a>HubSpot SSO 구성

1. 브라우저에서 새 탭을 열고 HubSpot 관리자 계정으로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에서 **설정** 아이콘을 선택합니다.

    ![HubSpot의 설정 아이콘](./media/hubspot-tutorial/icon.png)

1. **계정 기본값** 을 선택합니다.

    ![HubSpot이 계정 기본값 옵션](./media/hubspot-tutorial/account.png)

1. 아래로 스크롤하여 **보안** 섹션으로 이동한 후 **설정** 을 선택합니다.

    ![HubSpot의 설정 옵션](./media/hubspot-tutorial/security.png)

1. **Single Sign-On 설정** 섹션에서 다음 단계를 수행합니다.

    1. **대상 그룹 URl(서비스 공급자 엔터티 ID)** 상자에서 **복사** 를 선택하여 값을 복사합니다. Azure Portal의 **기본 SAML 구성** 창에서 **식별자** 상자에 해당 값을 붙여넣습니다.

    1. **로그인 URL, ACS, 수신자 또는 리디렉션** 상자에서 **복사** 를 선택하여 값을 복사합니다. Azure Portal의 **기본 SAML 구성** 창에서 **회신 URL** 상자에 해당 값을 붙여넣습니다.

    1. HubSpot의 **ID 공급자 식별자 또는 발급자 URL** 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    1. HubSpot의 **ID 공급자 Single Sign-On URL** 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    1. Windows 메모장에서 다운로드한 **인증서(Base64)** 파일을 엽니다. 파일 콘텐츠를 선택한 후 복사합니다. 그런 다음, HubSpot에서 **X.509 인증서** 상자에 붙여넣습니다.

    1. **확인** 을 선택합니다.

        ![HubSpot의 Single Sign-On 설정 섹션](./media/hubspot-tutorial/certificate.png)

### <a name="create-hubspot-test-user"></a>HubSpot 테스트 사용자 만들기

Azure AD 사용자가 HubSpot에 로그인하려면 HubSpot에 프로비저닝되어야 합니다. HubSpot의 경우, 수동으로 프로비저닝합니다.

HubSpot에서 사용자 계정을 프로비저닝하려면

1. HubSpot 회사 사이트에 관리자로 로그인합니다.

1. 페이지의 오른쪽 위 모서리에서 **설정** 아이콘을 선택합니다.

    ![HubSpot의 설정 아이콘](./media/hubspot-tutorial/icon.png)

1. **사용자 및 팀** 을 선택합니다.

    ![HubSpot의 사용자 및 팀 옵션](./media/hubspot-tutorial/users.png)

1. **사용자 만들기** 를 선택합니다.

    ![HubSpot에서 사용자 만들기 옵션](./media/hubspot-tutorial/teams.png)

1. **메일 추가** 상자에 brittasimon\@contoso.com 형식으로 사용자의 메일 주소를 입력하고 **다음** 을 선택합니다.

    ![HubSpot의 사용자 만들기 섹션에 있는 메일 주소 추가 상자](./media/hubspot-tutorial/add-user.png)

1. **사용자 만들기** 섹션에서 각 탭을 선택합니다. 각 탭에서 사용자에 대해 관련 옵션 및 권한을 설정합니다. 그다음에 **다음** 을 선택합니다.

    ![HubSpot의 사용자 만들기 섹션에 포함된 탭](./media/hubspot-tutorial/create-user.png)

1. 사용자에게 초대를 보내려면 **보내기** 를 선택합니다.

    ![HubSpot의 보내기 옵션](./media/hubspot-tutorial/invitation.png)

    > [!NOTE]
    > 사용자는 초대를 수락하면 활성화됩니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 HubSpot 로그온 URL로 리디렉션됩니다.  

* HubSpot 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 SSO를 설정한 HubSpot에 자동으로 로그인되어야 합니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 HubSpot 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 HubSpot에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

HubSpot이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).