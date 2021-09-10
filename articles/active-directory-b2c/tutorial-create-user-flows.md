---
title: 자습서 - 사용자 흐름 및 사용자 지정 정책 만들기 - Azure Active Directory B2C
description: 이 자습서에 따라 Azure Portal에서 사용자 흐름 및 사용자 지정 정책을 만들어 Azure Active Directory B2C에서 애플리케이션에 대한 등록, 로그인 및 사용자 프로필 편집을 사용하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/17/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: ce49a0a6fc249f3cea6bfdac613beade5322142f
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122323895"
---
# <a name="tutorial-create-user-flows-and-custom-policies-in-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C에서 사용자 흐름 및 사용자 지정 정책 만들기

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

애플리케이션에는 사용자가 가입하거나, 로그인하거나, 자신의 프로필을 관리하도록 설정하는 사용자 흐름이 있을 수 있습니다. Azure AD B2C(Azure Active Directory B2C) 테넌트에서 서로 다른 유형의 여러 사용자 흐름을 만들고, 필요에 따라 애플리케이션에서 이를 사용할 수 있습니다. 애플리케이션에 사용자 흐름을 다시 사용할 수 있습니다.

::: zone pivot="b2c-user-flow"
사용자 흐름을 사용하면 사용자가 로그인, 등록, 프로필 편집 또는 암호 재설정과 같은 작업을 수행할 때 애플리케이션과 상호 작용하는 방식을 결정할 수 있습니다. 이 문서에서는 다음 방법을 설명합니다.
::: zone-end

::: zone pivot="b2c-custom-policy"
[사용자 지정 정책](custom-policy-overview.md)은 Azure AD B2C(Azure Active Directory B2C) 테넌트의 동작을 정의하는 구성 파일입니다. 이 문서에서는 다음 방법을 설명합니다.
::: zone-end

> [!div class="checklist"]
> * 가입 및 로그인 사용자 흐름 만들기
> * 셀프 서비스 암호 재설정 사용
> * 프로필 편집 사용자 흐름 만들기

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> 사용자 흐름 버전을 참조하는 방법이 변경되었습니다. 이전에는 V1(프로덕션 준비) 버전과 V1.1 및 V2(미리 보기) 버전을 제공했습니다. 이제 사용자 흐름을 최신 기능이 포함된 **권장** 사용자 흐름 및 **표준(레거시)** 사용자 흐름 등 두 가지 버전으로 통합했습니다. 모든 레거시 미리 보기 사용자 흐름(V1.1 및 V2)은 더 이상 사용되지 않습니다. 자세한 내용은 [Azure AD B2C의 사용자 흐름 버전](user-flow-versions.md)을 참조하세요. *이러한 변경 사항은 Azure 퍼블릭 클라우드에만 적용됩니다. 다른 환경에서는 [레거시 사용자 흐름 버전 관리](user-flow-versions-legacy.md)를 계속 사용합니다.* 
::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

::: zone pivot="b2c-user-flow"
- Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.
- [웹 애플리케이션을 등록](tutorial-register-applications.md)하고 [ID 토큰 암시적 부여를 사용하도록 설정](tutorial-register-applications.md#enable-id-token-implicit-grant)합니다.
::: zone-end

::: zone pivot="b2c-custom-policy"

- Azure 구독에 연결된 [Azure AD B2C 테넌트](tutorial-create-tenant.md)가 아직 없으면 만듭니다.
- [웹 애플리케이션을 등록](tutorial-register-applications.md)하고 [ID 토큰 암시적 부여를 사용하도록 설정](tutorial-register-applications.md#enable-id-token-implicit-grant)합니다.
- [Facebook 애플리케이션을 만듭니다](identity-provider-facebook.md#create-a-facebook-application). [Facebook 계정으로 가입 및 로그인 설정](identity-provider-facebook.md) 문서의 필수 조건과 나머지 단계를 건너뜁니다. Facebook 애플리케이션은 사용자 지정 정책을 사용하는 데 필요하지 않지만 이 연습에서 사용자 지정 정책에 소셜 로그인을 사용하도록 설정하는 방법을 보여 주기 위해 사용되었습니다.

::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>가입 및 로그인 사용자 흐름 만들기

등록 및 로그인 사용자 흐름은 단일 구성으로 가입 및 로그인 환경을 둘 다 처리합니다. 애플리케이션 사용자는 컨텍스트에 따라 올바른 경로로 안내됩니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.

    ![B2C 테넌트, 디렉터리 및 구독 창, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 아래에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.

    ![새 사용자 흐름 단추가 강조 표시된 포털의 사용자 흐름 페이지](./media/tutorial-create-user-flows/sign-up-sign-in-user-flow.png)

1. **사용자 흐름 만들기** 탭에서 **가입 및 로그인** 사용자 흐름을 선택합니다.

    ![가입 및 로그인 흐름이 강조 표시된 사용자 흐름 페이지 선택](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다. (사용자 흐름 버전에 대해 [자세히 알아보세요](user-flow-versions.md).)

    ![속성이 강조 표시된 Azure Portal의 사용자 흐름 만들기 페이지](./media/tutorial-create-user-flows/select-version.png)

1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *signupsignin1* 과 같습니다.
1. **ID 공급자** 에서 **메일 등록** 을 선택합니다.
1. **사용자 특성 및 클레임** 에 대해 수집한 후 등록 동안 사용자로부터 전송하려는 클레임 및 특성을 선택합니다. 예를 들어 **자세히 보기** 를 선택하고 **국가/지역**, **표시 이름** 및 **우편 번호** 의 특성 및 클레임을 선택합니다. **확인** 을 클릭합니다.

    ![세 개의 클레임이 선택된 특성 및 클레임 선택 페이지](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. **만들기** 를 클릭하여 사용자 흐름을 추가합니다. *B2C_1* 이라는 접두사가 이름 앞에 자동으로 붙습니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭하고 **지금 등록** 을 선택합니다.

    ![사용자 흐름 실행 단추가 강조 표시된 포털의 사용자 흐름 실행 페이지](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. 유효한 이메일 주소를 입력하고, **확인 코드 보내기** 를 클릭하고, 받은 확인 코드를 입력한 다음, **코드 확인** 을 선택합니다.
1. 새 암호를 입력하고 암호를 확인합니다.
1. 국가 및 지역을 선택하고, 표시하려는 이름을 입력하고, 우편 번호를 입력한 다음, **만들기** 를 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.
1. 이제 사용자 흐름을 다시 실행할 수 있으며 만든 계정으로 로그인할 수 있습니다. 반환된 토큰에는 선택한 이름, 국가/지역, 이름 및 우편 번호에 대한 클레임이 포함되어 있습니다.

> [!NOTE]
> "사용자 흐름 실행" 환경은 현재 인증 코드 흐름을 사용하는 SPA 회신 URL 유형과 호환되지 않습니다. 이러한 종류의 앱에서 "사용자 흐름 실행" 환경을 사용하려면 "웹" 유형의 회신 URL을 등록하고 [여기](tutorial-register-spa.md)에 설명된 대로 암시적 흐름을 사용하도록 설정합니다.

## <a name="enable-self-service-password-reset"></a>셀프 서비스 암호 재설정 사용

등록 또는 로그인 사용자 흐름에 대해 [셀프 서비스 암호 재설정](add-password-reset-policy.md)을 사용하도록 설정하려면 다음을 수행합니다.

1. 사용자가 만든 등록 또는 로그인 사용자 흐름을 선택합니다.
1. 왼쪽 메뉴의 **설정** 에서 **속성** 을 선택합니다.
1. **암호 복잡성** 에서 **셀프 서비스 암호 재설정** 을 선택합니다.
1. **저장** 을 선택합니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 선택합니다.
1. 등록 또는 로그인 페이지에서 **암호를 잊으셨나요?** 를 선택합니다.
1. 이전에 만든 계정의 이메일 주소를 확인한 다음, **계속** 을 선택합니다.
1. 이제 사용자의 암호를 변경할 수 있습니다. 암호를 변경하고, **계속** 을 선택합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.

## <a name="create-a-profile-editing-user-flow"></a>프로필 편집 사용자 흐름 만들기

애플리케이션에서 프로필을 편집할 수 있도록 하려면 프로필 편집 사용자 흐름을 사용합니다.

1. Azure AD B2C 테넌트 개요 페이지의 메뉴에서 **사용자 흐름** 을 선택한 다음, **새 사용자 흐름** 을 선택합니다.
1. **사용자 흐름 만들기** 탭에서 **프로필 편집** 사용자 흐름을 선택합니다. 
1. **버전 선택** 아래에서 **추천** 을 선택한 다음, **만들기** 를 선택합니다.
1. 사용자 흐름에 대한 **이름** 을 입력합니다. 예를 들어 *profileediting1* 과 같습니다.
1. **ID 공급자** 로 **로컬 계정 로그인** 을 선택합니다.
2. **사용자 특성** 으로 고객이 프로필에서 편집할 수 있도록 하려는 특성을 선택합니다. 예를 들어 **자세히 보기** 를 선택한 다음, **표시 이름** 및 **작업 제목** 에 대한 특성과 클레임을 모두 선택합니다. **확인** 을 클릭합니다.
3. **만들기** 를 클릭하여 사용자 흐름을 추가합니다. 접두사 *B2C_1* 이 이름을 자동으로 추가됩니다.

### <a name="test-the-user-flow"></a>사용자 흐름 테스트

1. 만든 사용자 흐름을 선택하여 해당 개요 페이지를 연 다음, **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭하고 이전에 만든 계정으로 로그인합니다.
1. 이제 사용자의 표시 이름 및 직함을 변경할 수 있습니다. **계속** 을 클릭합니다. 토큰이 `https://jwt.ms`로 반환되며 사용자에게 표시됩니다.
::: zone-end

::: zone pivot="b2c-custom-policy"
> [!TIP]
> 이 문서에서는 테넌트를 수동으로 설정하는 방법을 설명합니다. 이 문서에서 프로세스 전체를 자동화할 수 있습니다. 자동화하면 등록 및 로그인, 암호 재설정, 프로필 편집 과정을 제공하는 Azure AD B2C [SocialAndLocalAccountsWithMFA 스타터 팩](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack)을 배포합니다. 아래 연습을 자동화하려면 [IEF 설치 앱](https://aka.ms/iefsetup)을 방문하여 지침을 따르세요.


## <a name="add-signing-and-encryption-keys"></a>서명 및 암호화 키 추가

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택한 다음, Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. 개요 페이지의 **정책** 에서 **Identity Experience Framework** 를 선택합니다.

### <a name="create-the-signing-key"></a>서명 키 만들기

1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Generate`을 선택합니다.
1. **이름** 에 `TokenSigningKeyContainer`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **키 유형** 으로는 **RSA** 를 선택합니다.
1. **키 사용** 으로는 **서명** 을 선택합니다.
1. **만들기** 를 선택합니다.

### <a name="create-the-encryption-key"></a>암호화 키 만들기

1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Generate`을 선택합니다.
1. **이름** 에 `TokenEncryptionKeyContainer`를 입력합니다. `B2C_1A`_ 접두사가 자동으로 추가될 수 있습니다.
1. **키 유형** 으로는 **RSA** 를 선택합니다.
1. **키 사용** 에는 **암호화** 를 선택합니다.
1. **만들기** 를 선택합니다.

### <a name="create-the-facebook-key"></a>Facebook 키 만들기

Facebook 애플리케이션의 [앱 비밀](identity-provider-facebook.md)을 정책 키로 추가합니다. 이 문서의 사전 요구 사항에 따라 만든 애플리케이션의 앱 비밀을 사용할 수 있습니다.

1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. **이름** 에 `FacebookSecret`를 입력합니다. `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **비밀** 에 Facebook 애플리케이션의 *앱 비밀*(developers.facebook.com)을 입력합니다. 이 값은 애플리케이션 ID가 아닌 비밀입니다.
1. **키 사용** 으로는 **서명** 을 선택합니다.
1. **만들기** 를 선택합니다.

## <a name="register-identity-experience-framework-applications"></a>Identity Experience Framework 애플리케이션 등록

Azure AD B2C를 사용하려면 로컬 계정으로 사용자를 가입 및 로그인시키는 데 사용되는 IdentityExperienceFramework 앱에 대한 위임된 권한을 가진 *IdentityExperienceFramework*(웹 API) 및 *ProxyIdentityExperienceFramework*(네이티브 앱)라는 두 개의 애플리케이션을 등록해야 합니다. 사용자는 이메일 주소 또는 사용자 이름과 암호로 가입하고 테넌트에 등록된 애플리케이션에 액세스하여 "로컬 계정"을 만들 수 있습니다. 로컬 계정은 Azure AD B2C 테넌트에만 존재합니다.

Azure AD B2C 테넌트에 이러한 두 애플리케이션을 한 번만 등록해야 합니다.

### <a name="register-the-identityexperienceframework-application"></a>IdentityExperienceFramework 애플리케이션 등록

Azure AD B2C 테넌트에 애플리케이션을 등록하려면 **앱 등록** 환경을 사용하면 됩니다.

1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. **이름** 에 `IdentityExperienceFramework`를 입력합니다.
1. **지원되는 계정 유형** 에서 **이 조직 디렉터리의 계정만** 을 선택합니다.
1. **리디렉션 URI** 에서 **웹** 을 선택한 후 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`을 입력합니다. 여기서 `your-tenant-name`은 Azure AD B2C 테넌트 도메인 이름입니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID** 를 기록합니다.

다음으로, 범위를 추가하여 API를 노출합니다.

1. 왼쪽 메뉴의 **관리** 에서 **API 노출** 을 선택합니다.
1. **범위 추가** 를 선택한 다음, **저장 후 계속** 을 선택하여 기본 애플리케이션 ID URI를 적용합니다.
1. Azure AD B2C 테넌트에서 사용자 지정 정책을 실행하도록 허용하는 범위를 만들도록 다음 값을 입력합니다.
    * **범위 이름**: `user_impersonation`
    * **관리자 동의 표시 이름**: `Access IdentityExperienceFramework`
    * **관리자 동의 설명**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **범위 추가** 를 선택합니다.

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>ProxyIdentityExperienceFramework 애플리케이션 등록

1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. **이름** 에 `ProxyIdentityExperienceFramework`를 입력합니다.
1. **지원되는 계정 유형** 에서 **이 조직 디렉터리의 계정만** 을 선택합니다.
1. **리디렉션 URI** 아래에서 드롭다운을 사용하여 **퍼블릭 클라이언트/네이티브(모바일 및 데스크톱)** 를 선택합니다.
1. **리디렉션 URI** 에 `myapp://auth`를 입력합니다.
1. **사용 권한** 아래에서 *openid 및 offline_access 권한에 대한 관리자 동의 허용* 확인란을 선택합니다.
1. **등록** 을 선택합니다.
1. 이후 단계에서 사용할 수 있게 **애플리케이션(클라이언트) ID** 를 기록합니다.

다음으로, 애플리케이션을 퍼블릭 클라이언트로 처리하도록 지정합니다.

1. 왼쪽 메뉴의 **관리** 아래에서 **인증** 을 선택합니다.
1. **고급 설정** 의 **퍼블릭 클라이언트 흐름 허용** 섹션에서 **다음 모바일 및 데스크톱 흐름 사용** 을 **예** 로 설정합니다. 애플리케이션 매니페스트에 **"allowPublicClient": true** 가 설정되었는지 확인합니다. 
1. **저장** 을 선택합니다.

이제 앞서 *IdentityExperienceFramework* 등록 시 제공한 API 범위에 권한을 부여합니다.

1. 왼쪽 메뉴의 **관리** 에서 **API 권한** 을 선택합니다.
1. **구성된 사용 권한** 아래에서 **권한 추가** 를 선택합니다.
1. **내 API** 탭을 선택한 후 **IdentityExperienceFramework** 애플리케이션을 선택합니다.
1. **권한** 에서 이전에 정의한 **user_impersonation** 범위를 선택합니다.
1. **권한 추가** 를 선택합니다. 안내에 따라 몇 분 정도 기다린 후 다음 단계를 진행하세요.
1. **(테넌트 이름)에 대한 관리자 동의 허용** 을 선택합니다.
1. 현재 로그인된 관리자 계정을 선택하거나 Azure AD B2C 테넌트에서 최소한 *클라우드 애플리케이션 관리자* 역할이 할당된 계정으로 로그인합니다.
1. **수락** 을 선택합니다.
1. **새로 고침** 을 선택한 다음, offline_access, openid 및 user_impersonation 범위의 **상태** 아래에 "다음에 대해 허용됨"이 표시되는지 확인합니다. 권한이 전파되려면 몇 분 정도 걸릴 수 있습니다.

* * *

## <a name="custom-policy-starter-pack"></a>사용자 지정 정책 시작 팩

사용자 지정 정책은 기술 프로필 및 사용자 경험을 정의하기 위해 Azure AD B2C 테넌트에 업로드하는 XML 파일 세트입니다. 신속한 진행을 위해 몇 가지 미리 작성된 정책이 포함된 시작 팩이 제공됩니다. 이러한 각 시작 팩 각각에는 설명한 시나리오를 수행하는 데 필요한 최소한의 기술 프로필 및 사용자 경험이 포함되어 있습니다.

- **LocalAccounts** - 로컬 계정만 사용할 수 있습니다.
- **SocialAccounts** - 소셜(또는 페더레이션) 계정만 사용할 수 있습니다.
- **SocialAndLocalAccounts** - 로컬 계정과 소셜 계정을 모두 사용할 수 있습니다.
- **SocialAndLocalAccountsWithMFA** - 소셜, 로컬 및 다단계 인증 옵션을 사용할 수 있습니다.

시작 팩 각각에는 다음이 포함됩니다.

- **기본 파일** - 몇 가지 수정이 필요합니다. 예제: *TrustFrameworkBase.xml*
- **확장 파일** - 이 파일은 대부분의 구성이 변경되었습니다. 예제: *TrustFrameworkExtensions.xml*
- **신뢰 당사자 파일** - 애플리케이션에서 호출하는 작업 관련 파일입니다. 예제: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

이 문서에서는 **SocialAndLocalAccounts** 시작 팩에서 XML 사용자 지정 정책 파일을 편집합니다. XML 편집기가 필요하면 간단한 플랫폼 간 편집기인 [Visual Studio Code를 사용해 보세요](https://code.visualstudio.com/download).

### <a name="get-the-starter-pack"></a>시작 팩 가져오기

GitHub에서 사용자 지정 정책 시작 팩을 가져온 다음, Azure AD B2C 테넌트 이름으로 SocialAndLocalAccounts 시작 팩의 XML 파일을 업데이트합니다.

1. [.zip 파일을 다운로드](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)하거나 리포지토리를 복제합니다.

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. **SocialAndLocalAccounts** 디렉터리의 모든 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다.

    예를 들어 B2C 테넌트의 이름이 *contosotenant* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosotenant.onmicrosoft.com`이 됩니다.

### <a name="add-application-ids-to-the-custom-policy"></a>사용자 지정 정책에 애플리케이션 ID 추가

확장 파일 *TrustFrameworkExtensions.xml* 에 애플리케이션 ID를 추가합니다.

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 을 열고 `<TechnicalProfile Id="login-NonInteractive">` 요소를 찾습니다.
1. `IdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 IdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.
1. `ProxyIdentityExperienceFrameworkAppId`의 두 인스턴스를 이전에 만든 ProxyIdentityExperienceFramework 애플리케이션의 애플리케이션 ID로 바꿉니다.
1. 파일을 저장합니다.

## <a name="upload-the-policies"></a>정책 업로드

1. Azure Portal에서 B2C 테넌트의 **Identity Experience Framework** 메뉴 항목을 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택합니다.
1. 이 순서로 정책 파일을 업로드합니다.
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

파일을 업로드하면 Azure에서 각 파일에 `B2C_1A_` 접두사를 추가합니다.

> [!TIP]
> XML 편집기에서 유효성 검사를 지원하는 경우 시작 팩의 루트 디렉터리에 있는 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 스키마에 대해 파일의 유효성을 검사합니다. 업로드하기 전에 XML 스키마 유효성 검사가 오류를 식별합니다.

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. **사용자 지정 정책** 에서 **B2C_1A_signup_signin** 을 선택합니다.
1. 사용자 지정 정책의 개요 페이지에 있는 **애플리케이션 선택** 에서 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다.
1. **회신 URL** `https://jwt.ms`인지 확인합니다.
1. **지금 실행** 을 선택합니다.
1. 이메일 주소를 사용하여 가입합니다.
1. **지금 실행** 을 다시 선택합니다.
1. 동일한 계정으로 로그인하여 올바르게 구성되었는지 확인합니다.

## <a name="add-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 추가

[사전 요구 사항](#prerequisites)에 설명된 것처럼, Facebook 은 사용자 지정 정책을 사용하는 데 필요하지 *않지만* 여기서는 사용자 지정 정책에서 페더레이션된 소셜 로그인을 사용하도록 설정하는 방법을 보여 주기 위해 사용되었습니다.

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 파일에서 `client_id`의 값을 Facebook 애플리케이션 ID로 바꿉니다.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
1. **사용자 지정 정책** 에서 **B2C_1A_signup_signin** 을 선택합니다.
1. **지금 실행** 을 선택하고 Facebook을 선택하여 Facebook에 로그인하고 사용자 지정 정책을 테스트합니다.


::: zone-end

## <a name="next-steps"></a>다음 단계

이 문서에서는 다음 방법에 대해 알아보았습니다.

> [!div class="checklist"]
> * 가입 및 로그인 사용자 흐름 만들기
> * 프로필 편집 사용자 흐름 만들기
> * 암호 재설정 사용자 흐름 만들기

다음으로, Azure AD B2C를 사용하여 애플리케이션에 사용자를 로그인하고 등록하는 방법에 대해 알아봅니다. 아래 링크된 샘플 앱을 따르세요.

- [샘플 ASP.NET Core 웹앱 구성](configure-authentication-sample-web-app.md)
- [웹 API를 호출하는 샘플 ASP.NET Core 웹앱 구성](configure-authentication-sample-web-app-with-api.md)
- [샘플 Python 웹 애플리케이션에서 인증 구성](configure-authentication-sample-python-web-app.md)
- [샘플 SPA(단일 페이지 애플리케이션) 구성](configure-authentication-sample-spa-app.md)
- [샘플 Angular 단일 페이지 앱 구성](configure-authentication-sample-angular-spa-app.md)
- [샘플 Android 모바일 앱 구성](configure-authentication-sample-android-app.md)
- [샘플 iOS 모바일 앱 구성](configure-authentication-sample-ios-app.md)
- [샘플 WPF 데스크톱 애플리케이션에서 인증 구성](configure-authentication-sample-wpf-desktop-app.md)
- [웹 API에서 인증 사용](enable-authentication-web-api.md)
- [SAML 애플리케이션 구성](saml-service-provider.md) 

[Azure AD B2C 아키텍처 심층 분석 시리즈](https://www.youtube.com/playlist?list=PLOPotgzC07IKXXCTZcrpuLWbVe3y51kfm)에서도 자세히 알아볼 수 있습니다.
