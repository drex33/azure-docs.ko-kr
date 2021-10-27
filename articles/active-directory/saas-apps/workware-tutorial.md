---
title: '자습서: Workware와 Azure AD SSO 통합'
description: Azure Active Directory와 Workware 간에 Single Sign-On을 구성하는 방법을 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/12/2021
ms.author: jeedes
ms.openlocfilehash: 9d213c63be608f2b28ec28f6597d33ff622b8cb5
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130005773"
---
# <a name="tutorial-azure-ad-sso-integration-with-workware"></a>자습서: Workware와 Azure AD SSO 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Workware를 통합하는 방법에 대해 알아봅니다. Azure AD와 Workware를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Workware에 액세스할 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Workware에 자동으로 로그인되도록 합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Workware SSO(Single Sign-On)를 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* Workware에서 **IDP** 시작 SSO를 지원합니다.

## <a name="add-workware-from-the-gallery"></a>갤러리에서 Workware 추가

Workware가 Azure AD에 통합되도록 구성하려면 갤러리에서 Workware를 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Workware** 를 입력합니다.
1. 결과 패널에서 **Workware** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-workware"></a>Workware에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Workware에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Workware의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Workware에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Workware SSO 구성](#configure-workware-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Workware 테스트 사용자 만들기](#create-workware-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Workware에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Workware** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    a. **식별자** 텍스트 상자에 `<WORKWARE_URL>/WW/AuthServices` 패턴을 사용하여 값을 입력합니다.

    b. **회신 URL** 텍스트 상자에 `<WORKWARE_URL>/WW/AuthServices/Acs` 패턴을 사용하여 값을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자 및 회신 URL로 해당 값을 업데이트합니다. 해당 값을 얻으려면 [Workware 클라이언트 지원 팀](mailto:support@activeops.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **페더레이션 메타데이터 XML** 을 찾고, **다운로드** 를 선택하여 인증서를 컴퓨터에 다운로드 및 저장합니다.

    ![인증서 다운로드 링크](common/metadataxml.png)

1. **Workware 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 B.Simon에게 Workware에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Workware** 를 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-workware-sso"></a>Workware SSO 구성

Workware에서 SSO 기능을 사용하려면 다음 설정을 완료해야 합니다.

#### <a name="enable-sso-permissions-for-workware-system-administrators"></a>Workware 시스템 관리자에 대한 SSO 권한 사용

* Workware 시스템 관리자가 SSO 인증을 설정하도록 허용하려면(**System Settings(시스템 설정) > Permissions to Role(역할에 대한 권한)의 Administration(관리) > 시스템 구성 권한 범주** 화면에서) Workware 시스템 관리자에 대한 SSO 인증 권한을 사용하도록 설정해야 합니다.

    ![SSO 인증 권한](./media/workware-tutorial/permission.png)

#### <a name="set-up-sso-authentication-in-workware"></a>Workware에서 SSO 인증 설정

1. **시스템 설정** 페이지로 이동하여 **SSO 인증** 을 클릭합니다.

1. **SSO Authentication** 섹션에서 **Add SSO Authentication(SSO 인증 추가)** 단추를 클릭하고, 다음 단계를 수행합니다. 

    ![SSO Authentication](./media/workware-tutorial/authentication.png)

    1. **External Identity Provider(외부 ID 공급자)** 에서 IDP 이름을 입력합니다.
    1. **인증 유형** 으로 **SAML2.0** 을 선택합니다.
    1. Azure Portal에서 복사한 **로그인 URL** 값을 **Identity Provider SignIn URL(ID 공급자 로그인 URL)** 텍스트 상자에 입력합니다.
    1. Azure Portal에서 복사한 **Azure AD 식별자** 값을 **Identity Provider Issuer URL(ID 공급자 발급자 URL)** 텍스트 상자에 입력합니다.
    1. Azure Portal에서 복사한 **로그아웃 URL** 값을 **Identity Provider Logout URL(ID 공급자 로그아웃 URL)** 텍스트 상자에 입력합니다.
    1. **Enable(사용)** 을 클릭합니다.
    1. 다운로드한 **Certificate(인증서)** 를 Azure Portal의 **ID 공급자 인증서** 에 업로드합니다.
    1. **저장** 을 클릭합니다.

### <a name="create-workware-test-user"></a>Workware 테스트 사용자 만들기

1. 관리자 권한으로 Workware 웹 사이트에 로그인합니다.

1. **관리 > 만들기/보기 > 사용자 계정 > 새로 추가** 를 차례로 선택합니다.

1. 다음 페이지에서 다음 단계를 수행합니다.

    ![Test user](./media/workware-tutorial/create-user.png)

    a. **Name(이름)** 필드에서 새 이름을 지정합니다.

    b. **Authentication Type(인증 유형)** 으로 **SSO** 를 선택합니다.

    다. 필수 필드를 입력하고, **Save(저장)** 를 클릭합니다.

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다.

* Azure Portal에서 [이 애플리케이션 테스트]를 클릭하면 SSO를 설정한 Workware에 자동으로 로그인됩니다.

* Microsoft 내 앱을 사용할 수 있습니다. 내 앱에서 Workware 타일을 클릭하면 SSO를 설정한 Workware에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](../user-help/my-apps-portal-end-user-access.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Workware가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Cloud App Security를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).