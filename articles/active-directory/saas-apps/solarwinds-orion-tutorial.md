---
title: '자습서: SolarWinds Orion과 Azure Active Directory SSO(Single Sign-On) 통합 | Microsoft Docs'
description: Azure Active Directory와 SolarWinds Orion 간에 Single Sign-On을 구성하는 방법에 대해 알아봅니다.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/29/2021
ms.author: jeedes
ms.openlocfilehash: 925ed8e4461fb7c97ba4d29819862fe84c27d82a
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132320662"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-solarwinds-orion"></a>자습서: SolarWinds Orion과 Azure Active Directory SSO(Single Sign-On) 통합

이 자습서에서는 Azure AD(Azure Active Directory)와 SolarWinds Orion을 통합하는 방법에 대해 알아봅니다. Azure AD와 SolarWinds Orion을 통합하면 다음을 수행할 수 있습니다.

* SolarWinds Orion에 대한 액세스 권한이 있는 사용자를 Azure AD에서 제어합니다.
* 사용자가 자신의 Azure AD 계정으로 SolarWinds Orion에 자동으로 로그인되도록 설정합니다.
* 단일 중앙 위치인 Azure Portal에서 계정을 관리합니다.

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음 항목이 필요합니다.

* Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.
* SolarWinds Orion SSO(Single Sign-On)가 설정된 구독

## <a name="scenario-description"></a>시나리오 설명

이 자습서에서는 테스트 환경에서 Azure AD SSO를 구성하고 테스트합니다.

* SolarWinds Orion에서 **SP 및 IDP** 시작 SSO를 지원합니다.

## <a name="add-solarwinds-orion-from-the-gallery"></a>갤러리에서 SolarWinds Orion 추가

SolarWinds Orion이 Azure AD에 통합되도록 구성하려면 갤러리에서 SolarWinds Orion을 관리형 SaaS 앱 목록에 추가해야 합니다.

1. Azure Portal에 회사 또는 학교 계정, 개인 Microsoft 계정으로 로그인합니다.
1. 왼쪽 탐색 창에서 **Azure Active Directory** 서비스를 선택합니다.
1. **엔터프라이즈 애플리케이션** 으로 이동한 다음, **모든 애플리케이션** 을 선택합니다.
1. 새 애플리케이션을 추가하려면 **새 애플리케이션** 을 선택합니다.
1. **갤러리에서 추가** 섹션의 검색 상자에 **SolarWinds Orion** 을 입력합니다.
1. 결과 패널에서 **SolarWinds Orion** 을 선택한 다음, 앱을 추가합니다. 앱이 테넌트에 추가될 때까지 잠시 동안 기다려 주세요.


## <a name="configure-and-test-azure-ad-sso-for-solarwinds-orion"></a>SolarWinds Orion용 Azure AD SSO 구성 및 테스트

**B.Simon** 이라는 테스트 사용자를 사용하여 SolarWinds Orion에서 Azure AD SSO를 구성하고 테스트합니다. SSO가 작동하려면 Azure AD 사용자와 SolarWinds Orion의 관련 사용자 간에 연결 관계를 설정해야 합니다.

SolarWinds Orion에서 Azure AD SSO를 구성하고 테스트하려면 다음 단계를 수행합니다.

1. **[Azure AD SSO 구성](#configure-azure-ad-sso)** - 사용자가 이 기능을 사용할 수 있도록 합니다.
    1. **[Azure AD 테스트 사용자 만들기](#create-an-azure-ad-test-user)** - B.Simon을 사용하여 Azure AD Single Sign-On을 테스트합니다.
    1. **[Azure AD 테스트 사용자 할당](#assign-the-azure-ad-test-user)** - B. Simon이 Azure AD Single Sign-On을 사용할 수 있도록 합니다.
1. **[SolarWinds Orion SSO 구성](#configure-solarwinds-orion-sso)** - 애플리케이션 쪽에서 Single Sign-On 설정을 구성합니다.
    1. **[SolarWinds Orion 테스트 사용자 만들기](#create-solarwinds-orion-test-user)** - B.Simon의 Azure AD 표현과 연결된 해당 사용자를 SolarWinds Orion에 만듭니다.
1. **[SSO 테스트](#test-sso)** - 구성이 작동하는지 여부를 확인합니다.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO 구성

Azure Portal에서 Azure AD SSO를 사용하도록 설정하려면 다음 단계를 수행합니다.

1. Azure Portal의 **SolarWinds Orion** 애플리케이션 통합 페이지에서 **관리** 섹션을 찾아 **Single Sign-On** 을 선택합니다.
1. **Single Sign-On 방법 선택** 페이지에서 **SAML** 을 선택합니다.
1. **SAML로 Single Sign-On 설정** 페이지에서 **기본 SAML 구성** 에 대한 연필 아이콘을 클릭하여 설정을 편집합니다.

   ![기본 SAML 구성 편집](common/edit-urls.png)

1. **IDP** 섹션에서 애플리케이션을 구성하려면 **기본 SAML 구성** 섹션에서 다음 필드 값을 입력합니다.

    a. **식별자** 텍스트 상자에서 `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>` 패턴을 사용하여 URL을 입력합니다.

    b. **회신 URL** 텍스트 상자에서 `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/SAMLLogin.aspx` 패턴을 사용하여 URL을 입력합니다.

1. **SP** 시작 모드에서 애플리케이션을 구성하려면 **추가 URL 설정** 를 클릭하고 다음 단계를 수행합니다.

    **로그인 URL** 텍스트 상자에서 `https://<ORION-HOSTNAME-OR-EXTERNAL-URL>/Orion/Login.aspx` 패턴을 사용하여 URL을 입력합니다.

    > [!NOTE]
    > 이러한 값은 실제 값이 아닙니다. 실제 식별자, 회신 URL 및 로그온 URL을 사용하여 이러한 값을 업데이트합니다. 이러한 값을 얻으려면 [SolarWinds Orion 클라이언트 지원 팀](mailto:technicalsupport@solarwinds.com)에 문의하세요. Azure Portal의 **기본 SAML 구성** 섹션에 표시된 패턴을 참조할 수도 있습니다.

1. SolarWinds Orion 애플리케이션에는 특정 서식의 SAML 어설션이 필요하기 때문에 SAML 토큰 특성 구성에 사용자 지정 특성 매핑을 추가해야 합니다. 다음 스크린샷에서는 기본 특성의 목록을 보여 줍니다.

    ![이미지](common/default-attributes.png)

1. 위에서 언급한 특성 외에도 SolarWinds Orion 애플리케이션에는 아래에 표시된 SAML 응답에서 다시 전달되어야 하는 몇 가지 특성이 추가로 필요합니다. 이러한 특성도 미리 채워져 있지만 요구 사항에 따라 검토할 수 있습니다.
    
    | Name |  원본 특성|
    | ----------- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | Email |user.mail |

1. **사용자 특성 및 클레임** 섹션에서 연필 아이콘을 클릭하여 편집하고 **그룹 클레임 추가** 를 클릭합니다.

    ![사용자 특성 및 클레임의 스크린샷](./media/solarwinds-orion-tutorial/group-claim.png)

1. **보안 그룹** 을 선택합니다.
1. Azure AD가 온-프레미스 AD와 동기화되는 경우 **원본 특성** 을 **sAMAccountName** 으로 변경합니다. 동기화되지 않는 경우에는 그룹 ID로 그대로 둡니다.

1. **고급 옵션** 에서 **그룹 클레임 이름 사용자 지정** 확인란을 선택하고 이름으로 OrionGroups를 지정합니다.

1. **저장** 을 클릭합니다.

1. **SAML로 Single Sign-On 설정** 페이지의 **SAML 서명 인증서** 섹션에서 **인증서(Base64)** 를 찾은 후 **다운로드** 를 선택하여 인증서를 다운로드하고 컴퓨터에 저장합니다.

    ![인증서 다운로드 링크](common/certificatebase64.png)

1. **SolarWinds Orion 설정** 섹션에서 요구 사항에 따라 적절한 URL을 복사합니다.

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

이 섹션에서는 Azure Single Sign-On을 사용할 수 있도록 B.Simon에게 Evergreen에 대한 액세스 권한을 부여합니다.

1. Azure Portal에서 **엔터프라이즈 애플리케이션** 을 선택한 다음, **모든 애플리케이션** 을 선택합니다.
1. 애플리케이션 목록에서 **Evergreen** 을 선택합니다.
1. 앱의 개요 페이지에서 **관리** 섹션을 찾고 **사용자 및 그룹** 을 선택합니다.
1. **사용자 추가** 를 선택한 다음, **할당 추가** 대화 상자에서 **사용자 및 그룹** 을 선택합니다.
1. **사용자 및 그룹** 대화 상자의 사용자 목록에서 **B.Simon** 을 선택한 다음, 화면 아래쪽에서 **선택** 단추를 클릭합니다.
1. 사용자에게 역할을 할당할 것으로 예상되는 경우 **역할 선택** 드롭다운에서 선택할 수 있습니다. 이 앱에 대한 역할이 설정되지 않은 경우 "기본 액세스" 역할이 선택된 것으로 표시됩니다.
1. **할당 추가** 대화 상자에서 **할당** 단추를 클릭합니다.

## <a name="configure-solarwinds-orion-sso"></a>SolarWinds Orion SSO 구성

1. SolarWinds Orion에 로그인하고 **설정** -> **모든 설정** 으로 이동합니다.

    ![스크린샷은 설정에서 선택한 모든 설정을 보여줍니다.](./media/solarwinds-orion-tutorial/settings.png)

1. **사용자 계정** 섹션에서 **SAML 구성** 을 선택합니다.

    ![스크린샷은 사용자 계정에서 선택한 SAML 구성을 보여줍니다.](./media/solarwinds-orion-tutorial/configure-user-accounts.png)

1. **ID 공급자 추가** 를 클릭합니다.

    ![스크린샷은 ID 공급자 추가를 선택할 수 있는 SAML 구성을 보여줍니다.](./media/solarwinds-orion-tutorial/configure-add-identity-provider.png)

1. **ID 공급자 추가** 페이지에서 다음 단계를 수행합니다.

    ![스크린샷은 설명된 값을 입력할 수 있는 ID 공급자 추가 페이지를 보여줍니다.](./media/solarwinds-orion-tutorial/configure-solarwinds.png)

    a. **구성** 탭으로 이동합니다.

    b. **ID 공급자 이름** 텍스트 상자에 유효한 이름(예: `My SSO service`)을 입력합니다.

    다. **SSO 대상 URL** 텍스트 상자에 Azure Portal에서 복사한 **로그인 URL** 값을 붙여넣습니다.

    d.  **발급자 URL** 텍스트 상자에 Azure Portal에서 복사한 **Azure AD 식별자** 값을 붙여넣습니다.

    e. Azure Portal에서 다운로드한 **인증서(Base64)** 를 메모장으로 열고, 콘텐츠를 **X.509 서명 인증서** 텍스트 상자에 붙여넣습니다.

    f. **Save** 를 클릭합니다.

### <a name="create-solarwinds-orion-test-user"></a>SolarWinds Orion 테스트 사용자 만들기

1. SolarWinds Orion 웹 사이트에 로그인하고 **설정** -> **모든 설정** 으로 이동합니다.

    ![스크린샷은 설정에서 선택한 모든 설정을 보여줍니다.](./media/solarwinds-orion-tutorial/settings.png)

1. **사용자 계정** 섹션에서 **계정 관리** 를 선택합니다.

    ![스크린샷은 선택된 SAML 구성을 보여줍니다.](./media/solarwinds-orion-tutorial/user-accounts.png)

1. **개별 계정** 탭에서 **새 계정 추가** 를 클릭합니다.

    ![스크린샷은 계정 관리에서 선택한 새 계정 추가를 보여줍니다.](./media/solarwinds-orion-tutorial/create-user.png)

1. SAML 개별 사용자 또는 그룹을 만드는 데 필요한 계정 유형을 선택합니다.

    ![스크린샷은 계정 유형을 선택할 수 있는 새 계정 추가를 보여줍니다.](./media/solarwinds-orion-tutorial/create-user-new-account.png)

1.  **이름 ID** 텍스트 상자에 이름을 입력합니다. 이 이름은 Azure AD와 마찬가지로 사용자 이름 또는 그룹 이름과 일치해야 합니다.

1.  **다음** 을 클릭하고 페이지를 제출합니다.

    ![스크린샷은 Azure AD에서 이름 ID를 입력할 수 있는 새 계정 추가를 보여줍니다.](./media/solarwinds-orion-tutorial/create-user-name-id.png)

## <a name="test-sso"></a>SSO 테스트 

이 섹션에서는 다음 옵션을 사용하여 Azure AD Single Sign-On 구성을 테스트합니다. 

#### <a name="sp-initiated"></a>SP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클릭합니다. 그러면 로그인 흐름을 시작할 수 있는 SolarWinds Orion 로그온 URL로 리디렉션됩니다.  

* SolarWinds Orion 로그온 URL로 직접 이동하여 해당 위치에서 로그인 흐름을 시작합니다.

#### <a name="idp-initiated"></a>IDP 시작:

* Azure Portal에서 **이 애플리케이션 테스트** 를 클하면 SSO를 설정한 SolarWinds Orion에 자동으로 로그인됩니다. 

Microsoft 내 앱을 사용하여 모든 모드에서 애플리케이션을 테스트할 수도 있습니다. 내 앱에서 SolarWinds Orion 타일을 클릭하면 SP 모드로 구성된 경우 로그인 흐름을 시작하기 위해 애플리케이션 로그온 페이지로 리디렉션되고, IDP 모드로 구성된 경우에는 SSO를 설정한 SolarWinds Orion에 자동으로 로그인됩니다. 내 앱에 대한 자세한 내용은 [내 앱 소개](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510)를 참조하세요.

## <a name="next-steps"></a>다음 단계

SolarWinds Orion이 구성되면 세션 제어를 적용하여 조직의 중요한 데이터의 반출 및 반입을 실시간으로 보호할 수 있습니다. 세션 제어는 조건부 액세스에서 확장됩니다. [Microsoft Defender for Cloud Apps를 사용하여 세션 제어를 적용하는 방법을 알아봅니다](/cloud-app-security/proxy-deployment-any-app).
