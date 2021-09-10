---
title: 사용자 지정 정책을 사용하여 앱에 Azure Active Directory B2C 사용자 인터페이스 포함
titleSuffix: Azure AD B2C
description: 사용자 지정 정책을 사용하여 앱에 Azure Active Directory B2C 사용자 인터페이스를 포함하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 7874224304f5466c8b166c1e5bd709b915a5d325
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567918"
---
# <a name="embedded-sing-up-or-sign-in-experience"></a>포함된 가입 또는 로그인 환경

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

더 간단한 가입 또는 로그인 환경을 위해 사용자를 별도의 가입 또는 로그인 페이지로 리디렉션하거나 팝업 창을 생성하지 않도록 방지할 수 있습니다. 인라인 프레임 &lt;iframe&gt; HTML 요소를 사용하여 Azure AD B2C 로그인 사용자 인터페이스를 웹 애플리케이션에 직접 포함할 수 있습니다. 

> [!TIP]
> &lt;iframe&gt; HTML 요소를 사용하여 [가입 또는 로그인](add-sign-up-and-sign-in-policy.md), [프로필 편집](add-profile-editing-policy.md) 또는 [암호 변경](add-password-change-policy.md) 사용자 지정 정책을 웹 또는 단일 페이지 앱에 포함합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="web-application-embedded-sign-in"></a>웹 애플리케이션 포함된 로그인

인라인 프레임 요소 `<iframe>`은 HTML5 웹 페이지에 문서를 포함하는 데 사용됩니다. 다음 예에 표시된 것처럼 iframe 요소를 사용하여 Azure AD B2C 로그인 사용자 인터페이스를 웹 애플리케이션에 직접 포함할 수 있습니다.

![마우스로 가리키기 DIV 환경으로 로그인](media/embedded-login/login-hovering.png)

iframe을 사용하는 경우 다음 사항을 고려하세요.

- 포함된 가입 또는 로그인에서 로컬 계정만 지원합니다. 대부분의 소셜 ID 공급자(예: Google 및 Facebook)는 로그인 페이지가 인라인 프레임에서 렌더링되지 않도록 차단합니다.
- iframe 내의 Azure AD B2C 세션 쿠키는 타사 쿠키로 간주되기 때문에 특정 브라우저(예: Safari 또는 시크릿 모드의 Chrome)는 이러한 쿠키를 차단하거나 지워 바람직하지 않은 사용자 환경이 됩니다. 이 문제를 방지하려면 애플리케이션 도메인 이름과 Azure AD B2C 도메인의 *원본이 동일한지* 확인합니다. 동일한 원본을 사용하려면 Azure AD B2C 테넌트에 대해 [사용자 지정 도메인을 사용하도록 설정한](custom-domain.md) 다음 동일한 원본으로 웹앱을 구성합니다. 예를 들어 'https://app.contoso.com '에서 호스팅되는 애플리케이션의 원본은 'https://login.contoso.com '에서 실행되는 Azure AD B2C와 동일합니다.

## <a name="prerequisites"></a>필수 구성 요소

* [Active Directory B2C에서 사용자 지정 정책을 사용하여 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)에 있는 단계를 완료합니다.
* 정책에 대해 [사용자 지정 도메인을 사용하도록 설정](custom-domain.md)합니다.

## <a name="configure-your-policy"></a>정책 구성

Azure AD B2C 사용자 인터페이스를 iframe에 포함할 수 있도록 하려면 Azure AD B2C HTTP 응답 헤더에 콘텐츠 보안 정책 `Content-Security-Policy` 및 프레임 옵션 `X-Frame-Options`를 포함해야 합니다. 이러한 헤더를 사용하면 Azure AD B2C 사용자 인터페이스가 애플리케이션 도메인 이름으로 실행될 수 있습니다.

[RelyingParty](relyingparty.md) 요소 내에 **JourneyFraming** 요소를 추가합니다.  **UserJourneyBehaviors** 요소는 **DefaultUserJourney** 뒤에 있어야 합니다. **UserJourneyBehavors** 요소는 다음 예와 같아야 합니다.

```xml
<!--
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" /> -->
  <UserJourneyBehaviors> 
    <JourneyFraming Enabled="true" Sources="https://somesite.com https://anothersite.com" /> 
  </UserJourneyBehaviors>
<!--
</RelyingParty> -->
```

**Sources** 특성에는 웹 애플리케이션의 URI가 포함됩니다. URI 사이에 공백을 추가합니다. 각 URI는 다음 요구 사항을 충족해야 합니다.

- 애플리케이션에서 URI를 신뢰하고 소유해야 합니다.
- URI는 https 체계를 사용해야 합니다.  
- 웹앱의 전체 URI를 지정해야 합니다. 와일드카드는 지원되지 않습니다.

또한 애플리케이션 페이지에서 각각 콘텐츠-보안 정책 및 X 프레임 옵션 헤더를 설정하여 자체 도메인 이름이 iframe에 포함되지 않도록 차단하는 것이 좋습니다. 이렇게 하면 iframe의 중첩된 포함과 관련된 이전 브라우저의 보안 문제를 완화할 수 있습니다.

## <a name="adjust-policy-user-interface"></a>정책 사용자 인터페이스 조정

Azure AD B2C [사용자 인터페이스 사용자 지정](customize-ui.md)을 사용하면 사용자에게 제공되는 HTML 및 CSS 콘텐츠를 거의 완전히 제어할 수 있습니다. 콘텐츠 정의를 사용하여 HTML 페이지를 사용자 지정하는 단계를 수행합니다. Azure AD B2C 사용자 인터페이스를 iframe 크기에 맞추려면 배경 및 추가 공간 없이 완전한 HTML 페이지를 제공합니다.  

다음 CSS 코드는 Azure AD B2C HTML 요소를 숨기고 iframe을 채우도록 패널의 크기를 조정합니다.

```css
div.social, div.divider {
    display: none;
}

div.api_container{
    padding-top:0;
}

.panel {
    width: 100%!important
}
```

경우에 따라 현재 표시되는 Azure AD B2C 페이지를 애플리케이션에 알릴 수 있습니다. 예를 들어 사용자가 등록 옵션을 선택하면 애플리케이션이 소셜 계정으로 로그인 링크 및 iframe 크기를 조정하기 위한 링크를 숨기도록 하는 것이 좋습니다.

애플리케이션에 현재 Azure AD B2C 페이지를 알리려면 [JavaScript에 대한 정책을 사용하도록 설정](./javascript-and-page-layout.md)한 다음 HTML5 게시 메시지를 사용합니다. 다음 JavaScript 코드는 `signUp`를 사용하여 게시 메시지를 앱에 보냅니다.

```javascript
window.parent.postMessage("signUp", '*');
```

## <a name="configure-a-web-application"></a>웹앱 구성

사용자가 로그인 단추를 선택하면 [웹앱](integrate-with-app-code-samples.md#web-apps-and-apis)은 사용자에게 Azure AD B2C 로그인 환경을 제공하기 위한 권한 부여 요청을 생성합니다. 로그인이 완료되면 Azure AD B2C는 애플리케이션 내의 구성된 리디렉션 URI에 ID 토큰 또는 권한 부여 코드를 반환합니다.

포함된 로그인을 지원하기 위해 iframe **src** 속성은 권한 부여 요청을 생성하고 사용자를 Azure AD B2C 정책으로 리디렉션하는 로그인 컨트롤러(예: `/account/SignUpSignIn`)를 가리킵니다.

```html
<iframe id="loginframe" frameborder="0" src="/account/SignUpSignIn"></iframe>
``` 

애플리케이션에서 ID 토큰을 받고 유효성을 검사한 후에는 권한 부여 흐름이 완료되며 애플리케이션은 사용자를 인식하고 신뢰합니다. 권한 부여 흐름이 iframe 내에서 발생하기 때문에 기본 페이지를 다시 로드해야 합니다. 페이지가 다시 로드되면 로그인 단추가 "로그아웃"으로 바뀌고 사용자 이름이 UI에 표시됩니다.  

다음은 로그인 리디렉션 URI가 기본 페이지를 새로 고칠 수 있는 방법을 보여 주는 예입니다.

```javascript
window.top.location.reload();
```

### <a name="add-sign-in-with-social-accounts-to-a-web-app"></a>웹앱에 소셜 계정으로 로그인 옵션 추가

소셜 ID 공급자는 인라인 프레임에서의 로그인 페이지가 렌더링되지 않도록 차단합니다. 소셜 계정에 대해 별도의 정책을 사용할 수도 있고, 로컬 및 소셜 계정으로 로그인 및 등록에 모두 단일 정책을 사용할 수도 있습니다. 그런 다음 `domain_hint` 쿼리 문자열 매개 변수를 사용할 수 있습니다. 도메인 힌트 매개 변수는 사용자를 소셜 ID 공급자의 로그인 페이지로 곧바로 이동시킵니다.

애플리케이션에서 소셜 계정으로 로그인 단추를 추가합니다. 사용자가 소셜 계정 단추 중 하나를 클릭하면 컨트롤에서 정책 이름을 변경하거나 도메인 힌트 매개 변수를 설정해야 합니다.

<!-- TBD: add a diagram -->

리디렉션 URI는 iframe에서 사용되는 것과 동일한 리디렉션 URI일 수 있습니다. 페이지 다시 로드를 건너뛸 수 있습니다.

## <a name="configure-a-single-page-application"></a>단일 페이지 애플리케이션 구성

단일 페이지 애플리케이션의 경우 iframe에 로드되는 두 번째 "로그인" HTML 페이지도 필요합니다. 이 로그인 페이지는 인증 코드를 생성하는 인증 라이브러리 코드를 호스팅하고 토큰을 반환합니다.

단일 페이지 애플리케이션에 액세스 토큰이 필요한 경우 JavaScript 코드를 사용하여 이 토큰이 포함된 iframe 및 개체에서 액세스 토큰을 가져옵니다.

> [!NOTE]
> 현재 iframe에서 MSAL 2.0을 실행하는 것은 지원되지 않습니다.

다음 코드는 기본 페이지에서 실행되며 iframe의 JavaScript 코드를 호출하는 예입니다.

```javascript
function getToken()
{
  var token = document.getElementById("loginframe").contentWindow.getToken("adB2CSignInSignUp");

  if (token === "LoginIsRequired")
    document.getElementById("tokenTextarea").value = "Please login!!!"
  else
    document.getElementById("tokenTextarea").value = token.access_token;
}

function logOut()
{
  document.getElementById("loginframe").contentWindow.policyLogout("adB2CSignInSignUp", "B2C_1A_SignUpOrSignIn");
}
```

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.

- [사용자 인터페이스 사용자 지정](customize-ui.md)
- [RelyingParty](relyingparty.md) 요소 참조
- [JavaScript에 대한 정책 사용 설정](./javascript-and-page-layout.md)
- [코드 샘플](integrate-with-app-code-samples.md)

::: zone-end
