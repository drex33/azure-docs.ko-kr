---
title: Facebook 계정으로 가입 및 로그인 설정
titleSuffix: Azure AD B2C
description: 고객에게 Azure Active Directory B2C를 사용하여 애플리케이션에서 Facebook 계정으로 등록 및 로그인을 제공합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: d9fb37bd05c6cddf1509d40433783692864e588d
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137833"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Azure Active Directory B2C를 사용하여 Facebook 계정으로 등록 설정 및 로그인

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Facebook 애플리케이션 만들기

Azure AD B2C(Azure Active Directory B2C)에서 Facebook 계정을 통한 사용자 로그인을 지원하려면 [Facebook 앱 대시보드](https://developers.facebook.com/)에서 애플리케이션을 만들어야 합니다. 자세한 내용은 [App Development](https://developers.facebook.com/docs/development)(앱 개발)를 참조하세요.

Facebook 계정이 없는 경우 [https://www.facebook.com](https://www.facebook.com)에서 가입하세요. Facebook 계정으로 가입하거나 로그인한 후 [Facebook 개발자 계정 등록 프로세스](https://developers.facebook.com/async/registration)를 시작합니다. 자세한 내용은 [Facebook 개발자로 등록](https://developers.facebook.com/docs/development/register)을 참조하세요.

1. Facebook 개발자 계정 자격 증명으로 [개발자용 Facebook](https://developers.facebook.com/apps)에 로그인합니다.
1. **앱 만들기** 를 선택합니다.
1. **앱 유형 선택** 에서 **소비자** 를 선택한 후, **다음** 을 선택합니다.
1. **앱 표시 이름** 및 유효한 **앱 연락처 이메일** 을 입력합니다.
1. **앱 만들기** 를 선택합니다. 이 단계에서는 Facebook 플랫폼 정책을 수용하고 온라인 보안 검사를 완료해야 합니다.
1. **설정** > **기본** 을 선택합니다.
    1. **앱 ID** 의 값을 복사합니다.
    1. **표시** 를 선택하고 **앱 비밀** 값을 복사합니다. 테넌트에서 Facebook을 ID 공급자로 구성하려면 둘 다 사용합니다. **앱 암호** 는 중요한 보안 자격 증명입니다.
    1. **개인 정보 취급 방침 URL** 의 URL(예: `https://www.contoso.com/privacy`)을 입력합니다. 정책 URL은 애플리케이션에 대한 개인 정보를 제공하기 위해 유지 관리하는 페이지입니다.
    1. **서비스 약관 URL** 에 URL을 입력합니다(예: `https://www.contoso.com/tos`). 정책 URL은 애플리케이션에 대한 사용 약관을 제공하기 위해 유지 관리하는 페이지입니다.
    1. **사용자 데이터 삭제** 에 대한 URL을 입력합니다(예: `https://www.contoso.com/delete_my_data`). 사용자 데이터 삭제 URL은 사용자가 데이터 삭제를 요청할 수 있도록 유지 관리하는 페이지입니다. 
    1. `Business and Pages` 등의 **범주** 를 선택합니다. 이 값은 Facebook의 경우 필수이지만 Azure AD B2C에서는 사용되지 않습니다.
1. 페이지의 맨 아래에서 **플랫폼 추가** 를 선택한 후 **웹 사이트** 를 선택합니다.
1. **사이트 URL** 에 웹 사이트 주소를 입력합니다(예: `https://contoso.com`). 
1. **변경 내용 저장** 을 선택합니다.
1. 메뉴에서 **제품** 옆에 있는 **더하기** 기호 또는 **제품 추가** 링크를 선택합니다. **앱에 제품 추가** 에서 **Facebook 로그인** 아래의 **설정** 을 선택합니다.
1. 메뉴에서 **Facebook 로그인** 을 선택하고 **설정** 을 선택합니다.
1. **유효한 OAuth 리디렉션 URI** 에 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. [사용자 지정 도메인](custom-domain.md)을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다. `your-tenant-name`을 테넌트 이름으로, `your-domain-name`을 사용자 지정 도메인으로 바꿉니다. 
1. 페이지 아래쪽에 있는 **변경 내용 저장** 을 선택합니다.
1. Azure AD B2C에서 Facebook 애플리케이션을 사용할 수 있도록 하려면 페이지 오른쪽 위에 있는 상태 선택기를 선택한 후, **켜기** 로 바꾸어 애플리케이션을 공용으로 만들고 **모드 전환** 을 선택합니다. 이때 상태가 **개발** 에서 **라이브** 로 변경됩니다. 자세한 내용은 [Facebook 앱 개발](https://developers.facebook.com/docs/development/release)을 참조하세요.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Facebook을 ID 공급자로 구성

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **ID 공급자** 를 선택한 다음, **Facebook** 을 선택합니다.
1. **이름** 을 입력합니다. 예를 들어 *Facebook* 입니다.
1. **클라이언트 ID** 에 대해 이전에 만든 Facebook 애플리케이션의 앱 ID를 입력합니다.
1. **클라이언트 암호** 에는 기록했던 앱 비밀을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>사용자 흐름에 Facebook ID 공급자 추가 

이 시점에서 Facebook ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 사용자 흐름에 Facebook ID 공급자를 추가하려면 다음을 수행합니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. Facebook ID 공급자를 추가하려는 사용자 흐름을 클릭합니다.
1. **소셜 ID 공급자** 에서 **Facebook** 을 선택합니다.
1. **저장** 을 선택합니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Facebook** 을 선택하여 Facebook 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.


::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="create-a-policy-key"></a>정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 앱 비밀을 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `FacebookSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 앱 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 클릭합니다.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook 계정을 ID 공급자로 구성

1. `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 파일에서 `client_id`의 값을 Facebook 애플리케이션 ID로 바꿉니다.

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>정책 업로드 및 테스트

만든 사용자 경험을 시작하는 RP(신뢰 당사자) 파일을 업데이트합니다.

1. 테넌트에 *TrustFrameworkExtensions.xml* 파일을 업로드합니다.
1. **사용자 지정 정책** 에서 **B2C_1A_signup_signin** 을 선택합니다.
1. **애플리케이션 선택** 에서 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **Facebook** 을 선택하여 Facebook 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

::: zone-end

## <a name="next-steps"></a>다음 단계

[Facebook 토큰을 애플리케이션에 전달](idp-pass-through-user-flow.md)하는 방법을 알아봅니다.
