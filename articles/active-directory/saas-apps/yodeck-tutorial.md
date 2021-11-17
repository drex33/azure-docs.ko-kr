---
title: '자습서: Azure Active Directory와 Yodeck 통합 | Microsoft Docs'
description: Azure Active Directory와 Yodeck 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/02/2021
ms.author: jeedes
ms.openlocfilehash: 56f02ba6231570988257d24e6f8ee60aa1beedf5
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132319958"
---
# <a name="tutorial-azure-active-directory-integration-with-yodeck"></a>자습서: Azure Active Directory와 Yodeck 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 Yodeck를 통합하는 방법에 대해 알아봅니다. Azure AD와 Yodeck를 통합하면 다음을 수행할 수 있습니다.

* Azure AD에서 Yodeck에 대한 액세스 권한이 있는 사용자를 제어합니다.
* 사용자가 해당 Azure AD 계정으로 Yodeck에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

Yodeck과 Azure AD의 통합을 구성하려면 다음 항목이 필요합니다.

* Azure AD 구독 Azure AD 환경이 없으면 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* Yodeck Single Sign-On을 사용하도록 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD Single Sign-On을 구성하고 테스트합니다.

* Yodeck에서 **SP** 및 **IDP** 시작 SSO를 지원합니다.

> [!NOTE]
> 이 애플리케이션의 식별자는 고정 문자열 값이므로 하나의 테넌트에서 하나의 인스턴스만 구성할 수 있습니다.

## <a name="add-yodeck-from-the-gallery"></a>갤러리에서 Yodeck 추가

Yodeck의 Azure AD 통합을 구성하려면 갤러리의 Yodeck을 관리되는 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에서 **Yodeck** 를 입력합니다.
1. 결과 패널에서 **Yodeck** 를 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.

## <a name="configure-and-test-azure-ad-sso-for-yodeck"></a>Yodeck에 대한 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 Yodeck에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 Yodeck의 관련 사용자 간에 연결 관계를 설정해야 합니다.

Yodeck에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[Yodeck SSO 구성](#configure-yodeck-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[Yodeck 테스트 사용자 만들기](#create-yodeck-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 Yodeck에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **Yodeck** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾고, **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

4. **IDP** 시작 모드에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 단계를 수행합니다.

    **식별자** 텍스트 상자에서 `https://app.yodeck.com/api/v1/account/metadata/` URL을 입력합니다.

5. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그온 URL** 텍스트 상자에 `https://app.yodeck.com/login` URL을 입력합니다.

6. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 복사 단추를 클릭하여 **앱 페더레이션 메타데이터 URL** 을 복사한 후 컴퓨터에 저장합니다.

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

이 섹션에서는 B.Simon에게 Yodeck에 대한 액세스 권한을 부여하여 해당 사용자가 Azure Single Sign-On을 사용하도록 설정합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Yodeck** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-yodeck-sso"></a>Yodeck SSO 구성

1. **Yodeck** 내에서 구성을 자동화하려면 **확장 설치** 를 클릭하여 **내 앱 보안 로그인 브라우저 확장** 을 설치해야 합니다.

    ![스크린샷은 확장 설치 단추를 보여줍니다.](./media/target-process-tutorial/install_extension.png)

1. 브라우저에 확장을 추가한 후 **Yodeck 설정** 을 클릭하면 Yodeck 애플리케이션으로 이동됩니다. 여기서 관리자 자격 증명을 입력하여 Yodeck에 로그인합니다. 브라우저 확장이 애플리케이션을 자동으로 구성하고 3-5단계를 자동으로 수행합니다.

    ![설정 구성](common/setup-sso.png)

    **애플리케이션을 수동으로 구성하려는 경우 다음 단계를 수행합니다.**

1. 다른 웹 브라우저 창에서 Yodeck 회사 사이트에 관리자로 로그인합니다.

1. 페이지의 오른쪽 맨 위 모서리에서 **사용자 설정** 옵션을 클릭하고 **계정 설정** 을 선택합니다.

    ![스크린샷은 사용자에 대해 선택된 계정 설정을 보여줍니다.](./media/yodeck-tutorial/account.png)

1. **SAML** 을 선택하고 다음 단계를 수행합니다.

    ![스크린샷은 이러한 단계를 수행할 수 있는 SAML 탭을 보여줍니다.](./media/yodeck-tutorial/configure.png)

    a. **URL에서 가져오기** 를 선택합니다.

    b. Azure Portal에서 복사한 **앱 페더레이션 메타데이터 URL** 값을 **URL** 텍스트 상자에 붙여넣고 **가져오기** 를 클릭합니다.
    
    다. **앱 페더레이션 메타데이터 URL** 을 가져오고 나면 나머지 필드가 자동으로 채워집니다.

    d. **저장** 을 클릭합니다.

### <a name="create-yodeck-test-user"></a>Yodeck 테스트 사용자 만들기

Azure AD 사용자가 Yodeck에 로그인할 수 있도록 하려면 Yodeck에 프로비저닝되어야 합니다. Yodeck의 경우, 수동으로 프로비전합니다.

**사용자 계정을 프로비전하려면 다음 단계를 수행합니다.**

1. Yodeck 회사 사이트에 관리자로 로그인합니다.

2. 페이지의 오른쪽 맨 위 모서리에서 **사용자 설정** 옵션을 클릭하고 **사용자** 를 선택합니다.

    ![스크린샷은 사용자에 대해 선택된 사용자를 보여줍니다.](./media/yodeck-tutorial/user.png)

3. **+사용자** 를 클릭하여 **사용자 세부 정보** 탭을 엽니다.

    ![스크린샷은 사용자 단추를 보여줍니다.](./media/yodeck-tutorial/user-details.png)

4. **사용자 정보** 대화 상자 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 이러한 단계를 수행할 수 있는 사용자 세부 정보 탭을 보여줍니다.](./media/yodeck-tutorial/user-page.png)

    a. **이름** 텍스트 상자에 사용자의 이름(예: **Britta**)을 입력합니다.

    b. **성** 텍스트 상자에 사용자의 성(예: **Simon**)을 입력합니다.

    다. **전자 메일** 텍스트 상자에서 brittasimon@contoso.com과 같은 사용자의 이메일 주소를 입력합니다.

    d. 조직 요구 사항에 따라 적절한 **계정 권한** 옵션을 선택합니다.
    
    e. **저장** 을 클릭합니다.

## <a name="test-sso"></a>SSO 테스트

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 Yodeck 로그온 URL로 리디렉션됩니다.  

* Yodeck 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭하면 SSO를 설정한 Yodeck에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 Yodeck 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우 SSO를 설정한 Yodeck에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

Yodeck가 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-aad).
