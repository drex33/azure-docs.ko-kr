---
title: Azure Active Directory B2C의 언어 사용자 지정
description: Azure Active Directory B2C의 사용자 흐름에서 언어 환경을 사용자 지정하는 방법을 알아봅니다.
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
ms.openlocfilehash: 5464e14cddc6319ad82eaaa054e39ba661312f97
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007071"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Azure Active Directory B2C의 언어 사용자 지정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C(Azure Active Directory B2C)의 언어 사용자 지정을 사용하면 고객 요구에 적합하도록 사용자 흐름을 다른 언어로 맞출 수 있습니다. Microsoft는 [36개 언어](#supported-languages)에 대한 번역을 제공하지만 사용자가 언어에 대한 고유한 번역을 제공할 수 있습니다. 단일 언어로만 환경이 제공되더라도 페이지에 있는 텍스트를 사용자 지정할 수 있습니다.

## <a name="how-language-customization-works"></a>언어 사용자 지정이 작동하는 방식

언어 사용자 지정을 사용하여 사용자 흐름이 제공되는 언어를 선택할 수 있습니다. 기능을 사용하도록 설정하면 사용자 애플리케이션에서 쿼리 문자열 매개 변수 `ui_locales`를 제공할 수 있습니다. Azure AD B2C를 호출하면 페이지가 사용자가 표시한 로캘로 번역됩니다. 이 구성 형식을 통해 사용자 흐름의 언어를 완전히 제어하고 고객의 브라우저 언어 설정을 무시합니다.

고객이 볼 수 있는 언어에 대한 제어 수준이 필요하지 않을 수 있습니다. `ui_locales` 매개 변수를 제공하지 않으면 고객의 환경이 브라우저 설정으로 결정됩니다. 언어를 지원 언어로 추가하여 사용자 흐름이 번역된 언어를 계속 제어할 수 있습니다. 고객의 브라우저가 지원하지 않으려는 언어를 표시하도록 설정된 경우 지원되는 문화에서 기본값으로 선택한 언어가 대신 표시됩니다.

* **ui-locales 지정 언어**: 언어 사용자 지정을 사용하도록 설정하면, 사용자 흐름이 여기에서 지정된 언어로 변경됩니다.
* **브라우저 요청 언어**: `ui_locales` 매개 변수를 지정하지 않은 경우, 사용자 흐름은 *해당 언어가 지원되는 경우* 브라우저 요청 언어로 변경됩니다.
* **정책 기본 언어**: 브라우저가 언어를 지정하지 않거나 지원하지 않는 언어를 지정하는 경우 사용자 흐름은 사용자 흐름 기본 언어로 변경됩니다.

> [!NOTE]
> 사용자 지정 사용자 특성을 사용하는 경우 사용자 고유의 번역을 제공해야 합니다. 자세한 내용은 [문자열 사용자 지정](#customize-your-strings)을 참조하세요.

Azure AD B2C를 사용하여 언어를 지역화하거나 사용자 지정하는 방법을 알아보려면 이 동영상을 시청합니다.

>[!Video https://www.youtube.com/embed/yqrX5_tA7Ms]

::: zone pivot="b2c-custom-policy"

지역화에는 다음 세 단계가 필요합니다. 

1. 지원하는 언어의 명시적 목록 설정
1. 언어별 문자열 및 컬렉션 제공
1. 해당 페이지의 [콘텐츠 정의](contentdefinitions.md) 편집 

::: zone-end 

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="support-requested-languages-for-ui_locales"></a>ui_locales에 요청된 언어 지원

언어 사용자 지정의 일반 공급 전에 만들어진 정책은 가장 먼저 이 기능을 사용하도록 설정해야 합니다. 그 이후에 만들어진 정책 및 사용자 흐름은 기본적으로 언어 사용자 지정을 사용하도록 설정됩니다.

사용자 흐름에서 언어 사용자 지정을 사용하도록 설정하면 `ui_locales` 매개 변수를 추가하여 사용자 흐름의 언어를 제어할 수 있습니다.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. 번역에 사용할 사용자 흐름을 클릭합니다.
1. **언어** 를 선택합니다.
1. **언어 사용자 지정 사용** 을 선택합니다.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>사용할 사용자 흐름의 언어를 선택합니다.

`ui_locales` 매개 변수 없이 브라우저에서 신청할 때 사용자 흐름을 번역할 언어 세트를 설정합니다.

1. 이전 지침에서 사용자 흐름에 언어 사용자 지정이 설정되었는지 확인합니다.
1. 사용자 흐름에 대한 **언어** 페이지에서 지원하려는 언어를 선택합니다.
1. 속성 창에서 **사용** 을 **예** 로 전환합니다.
1. 속성 창 위에서 **저장** 을 선택합니다.

>[!NOTE]
>`ui_locales` 매개 변수가 제공되지 않으면 페이지가 고객의 브라우저 언어로 번역됩니다(사용하도록 설정된 경우에만).
>

## <a name="customize-your-strings"></a>문자열 사용자 지정

언어 사용자 지정을 통해 사용자 흐름의 모든 문자열을 사용자 지정할 수 있습니다.

1. 이전 지침에서 사용자 흐름에 언어 사용자 지정이 설정되었는지 확인합니다.
1. 사용자 흐름에 대한 **언어** 페이지에서 사용자 지정하려는 언어를 선택합니다.
1. **페이지 수준 리소스 파일** 아래에서 편집하려는 페이지를 선택합니다.
1. **기본값 다운로드**(또는 이전에 이 언어를 편집한 경우 **재정의 다운로드**)를 선택합니다.

이러한 단계는 문자열 편집을 시작하는 데 사용할 수 있는 JSON 파일을 제공합니다.

### <a name="change-any-string-on-the-page"></a>페이지에서 문자열 변경

1. 이전 지침에서 다운로드한 JSON 파일을 JSON 편집기에서 엽니다.
1. 변경할 요소를 찾습니다. 찾으려는 문자열의 `StringId`를 찾거나 변경할 `Value` 특성을 찾을 수 있습니다.
1. `Value` 특성을 표시하기를 원하는 항목으로 업데이트합니다.
1. 변경할 모든 문자열의 `Override`를 `true`로 변경합니다.
1. 파일을 저장하고 변경 내용을 업로드합니다. (JSON 파일을 다운로드한 곳과 동일한 위치에서 업로드 컨트롤을 찾을 수 있습니다.)

> [!IMPORTANT]
> 문자열을 재정의해야 하는 경우 `Override` 값을 `true`로 설정해야 합니다. 값이 변경되지 않으면 항목이 무시됩니다.

### <a name="change-extension-attributes"></a>확장 특성 변경

사용자 지정 사용자 특성의 문자열을 변경하려는 경우 또는 JSON에 문자열을 추가하려는 경우 다음 형식을 사용합니다.

```json
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
  ]
}
```

`<ExtensionAttribute>`를 사용자 지정 사용자 특성 이름으로 바꿉니다.

`<ExtensionAttributeValue>`를 표시할 새 문자열로 바꿉니다.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>LocalizedCollections를 사용하여 값 목록을 제공합니다.

응답에 대한 값 목록 집합을 제공하려면 `LocalizedCollections` 특성을 만들어야 합니다. `LocalizedCollections`는 `Name` 및 `Value` 쌍의 배열입니다. 항목의 순서대로 항목이 표시됩니다. `LocalizedCollections`를 추가하려면 다음 형식을 사용하세요.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
        {
          "Name":"<Response1>",
          "Value":"<Value1>"
        },
        {
          "Name":"<Response2>",
          "Value":"<Value2>"
        }
      ]
    }
  ]
}
```

* `ElementId`는 이 `LocalizedCollections` 특성이 응답인 사용자 특성입니다.
* `Name`은 사용자에게 표시되는 값입니다.
* `Value`는 이 옵션이 선택될 때 클레임에 반환된 항목입니다.

### <a name="upload-your-changes"></a>변경 내용 업로드

1. JSON 파일 변경을 완료했으면 B2C 테넌트로 돌아갑니다.
1. **사용자 흐름** 을 선택하고 번역에 사용할 사용자 흐름을 클릭합니다.
1. **언어** 를 선택합니다.
1. 번역하려는 언어를 선택합니다.
1. 번역을 제공하려는 페이지를 선택합니다.
1. 폴더 아이콘을 선택하고 업로드할 JSON 파일을 선택합니다.

변경 내용이 자동으로 사용자 흐름에 저장됩니다.

## <a name="customize-the-page-ui-by-using-language-customization"></a>언어 사용자 지정을 사용하여 페이지 UI 사용자 지정

[HTML 콘텐츠](customize-ui-with-html.md)를 지역화하는 방법은 두 가지가 있습니다. 하나는 [언어 사용자 지정](language-customization.md)을 켜는 것입니다. 이 기능을 사용하도록 설정하면 Azure AD B2C가 OpenID Connect 매개 변수 `ui-locales`를 엔드포인트로 전달할 수 있습니다. 콘텐츠 서버는 이 매개 변수를 사용하여 언어 관련 사용자 지정된 HTML 페이지를 제공할 수 있습니다.

또는 사용되는 로캘에 따라 다른 위치에서 콘텐츠를 끌어올 수 있습니다. CORS 사용 엔드포인트에서 특정 언어에 대한 콘텐츠를 호스트하도록 폴더 구조를 설정할 수 있습니다. 와일드 카드 값 `{Culture:RFC5646}`을 사용하는 경우 적합한 구조를 호출합니다. 예를 들어 사용자 지정 페이지 URI가 다음과 같다고 가정해 봅시다.

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

`fr`로 페이지를 로드할 수 있습니다. 페이지가 HTML 및 CSS 콘텐츠를 가져올 때 다음 위치에서 가져옵니다.

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>사용자 지정 언어 추가

또한 Microsoft가 현재 번역을 제공하지 않는 언어를 추가할 수 있습니다. 사용자 흐름에서 모든 문자열에 대한 번역을 제공해야 합니다. 언어 및 로캘 코드는 ISO 639-1 표준으로 제한됩니다. 로캘 코드 형식은 반드시 "ISO_639-1_code"-"CountryCode"여야 합니다(예: `en-GB`). 자세한 내용은 [로캘 ID 형식](/openspecs/office_standards/ms-oe376/6c085406-a698-4e12-9d4d-c3b0ee3dbc4a)을 참조하세요.

1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
2. 사용자 지정 언어를 추가하려는 사용자 흐름을 클릭한 다음, **언어** 를 클릭합니다.
3. 페이지의 위쪽에서 **사용자 지정 언어 추가** 를 선택합니다.
4. 열린 컨텍스트 창에서 유효한 로캘 코드를 입력하여 번역을 제공하는 언어를 식별합니다.
5. 각 페이지에서 영어에 대한 재정의의 집합을 다운로드하고 번역에 사용할 수 있습니다.
6. JSON 파일을 완료하면 각 페이지에 업로드할 수 있습니다.
7. **사용** 을 선택하면 이제 사용자 흐름에서 사용자에게 이 언어를 표시할 수 있습니다.
8. 언어를 저장합니다.

>[!IMPORTANT]
>사용자 지정 언어를 사용하도록 설정하거나 재정의를 업로드해야 언어를 저장할 수 있습니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="set-up-the-list-of-supported-languages"></a>지원되는 언어 목록 설정

정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. 지원되는 언어인 영어(기본값) 및 스페인어를 사용하여 `Localization` 요소를 추가합니다.  


```xml
<Localization Enabled="true">
  <SupportedLanguages DefaultLanguage="en" MergeBehavior="ReplaceAll">
    <SupportedLanguage>en</SupportedLanguage>
    <SupportedLanguage>es</SupportedLanguage>
  </SupportedLanguages>
</Localization>
```

## <a name="provide-language-specific-labels"></a>언어별 레이블 제공

`Localization` 요소의 [LocalizedResources](localization.md#localizedresources)에는 지역화된 문자열 목록이 포함되어 있습니다. 지역화된 리소스 요소에는 지역화된 리소스를 고유하게 식별하는 데 사용되는 식별자가 있습니다. 이 식별자는 나중에 [콘텐츠 정의](contentdefinitions.md) 요소에서 사용됩니다.

콘텐츠 정의에 대해 지역화된 리소스 요소 및 지원하려는 모든 언어를 구성합니다. 영어 및 스페인어의 통합 등록 또는 로그인 페이지를 사용자 지정하려면 `</SupportedLanguages>` 요소를 닫은 후 다음 `LocalizedResources` 요소를 추가합니다.

> [!NOTE]
> 다음 샘플에서는 각 줄의 시작에 파운드 `#` 기호를 추가하여 화면에서 지역화된 레이블을 편리하게 찾도록 하였습니다.

```xml
<!--Local account sign-up or sign-in page English-->
<Localization Enabled="true">
  ...
  <LocalizedResources Id="api.signuporsignin.en">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Email Address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Please enter your email</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Username</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Sign up now</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Please enter your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#Don't have an account?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#Forgot your password?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#OR</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#The user has forgotten their password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Sign in</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Sign in with your social account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Please enter your password</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#The password you entered is not in the expected format.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Sign in with your user name</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Sign in with your existing account</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Please enter a valid email address</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#We are having trouble signing you in. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Your password is incorrect.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#We can't seem to find your account.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Looks like you used an old password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#Invalid username or password.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Your account has been locked. Contact your support person to unlock it, then try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Your account is temporarily locked to prevent unauthorized use. Try again later.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up or sign-in page Spanish-->
  <LocalizedResources Id="api.signuporsignin.es">
    <LocalizedStrings>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_email">#Correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_email">#Este campo es obligatorio</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="logonIdentifier_username">#Nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="password">#Contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_link">#Registrarse ahora</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_username">#Escriba su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="createaccount_intro">#¿No tiene una cuenta?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="forgotpassword_link">#¿Olvidó su contraseña?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="divider_title">#O</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="cancel_message">#El usuario ha olvidado su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_signin">#Iniciar sesión</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="social_intro">#Iniciar sesión con su cuenta de redes sociales</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="requiredField_password">#Escriba su contraseña</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_password">#La contraseña que ha escrito no está en el formato esperado.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_username">#Iniciar sesión con su nombre de usuario</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="local_intro_email">#Iniciar sesión con su cuenta existente</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="invalid_email">#Escriba una dirección de correo electrónico válida</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="unknown_error">#Tenemos problemas para iniciar su sesión. Vuelva a intentarlo más tarde.  </LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="email_pattern">^[a-zA-Z0-9.!#$%&amp;'^_`{}~-]+@[a-zA-Z0-9-]+(?:\.[a-zA-Z0-9-]+)*$</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidPassword">#Su contraseña es incorrecta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalDoesNotExist">#Parece que no podemos encontrar su cuenta.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfOldPasswordUsed">#Parece que ha usado una contraseña antigua.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="DefaultMessage">#El nombre de usuario o la contraseña no son válidos.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountDisabled">#Se bloqueó su cuenta. Póngase en contacto con la persona responsable de soporte técnico para desbloquearla y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfUserAccountLocked">#Su cuenta se bloqueó temporalmente para impedir un uso no autorizado. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="AADRequestsThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page English-->
  <LocalizedResources Id="api.localaccountsignup.en">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Email Address</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Email address that can be used to contact you.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Please enter a valid email address.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Enter new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirm New Password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirm new password</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8-16 characters, containing 3 out of 4 of the following: Lowercase characters, uppercase characters, digits (0-9), and one or more of the following symbols: @ # $ % ^ &amp; * - _ + = [ ] { } | \ : ' , ? / ` ~ " ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Display Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Your display name.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Surname</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Your surname (also known as family name or last name).  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Given Name</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Your given name (also known as first name).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Create</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#One or more fields are filled out incorrectly. Please check your entries and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#The password entry fields do not match. Please enter the same password in both fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#A required field is missing. Please fill out all required fields and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#What is this?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Please provide the following details.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Please wait</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#This information is required.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Change e-mail</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Send new code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Send verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Verify code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#That code is expired. Please request a new code.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#You've made too many incorrect attempts. Please try again later.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#That code is incorrect. Please try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#We are having trouble verifying your email address. Please enter a valid email address and try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#There have been too many requests to verify this email address. Please wait a while, then try again.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Verification code has been sent to your inbox. Please copy it to the input box below.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Verification code</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#Verification is necessary. Please click Send button.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#E-mail address verified. You can now continue.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#There are too many requests at this moment. Please wait for some time and try again.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Claim not verified: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#A user with the specified ID already exists. Please choose a different one.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Incorrect pattern for: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} has invalid input.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Missing required element: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error in validation by: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
  <!--Local account sign-up page Spanish-->
  <LocalizedResources Id="api.localaccountsignup.es">
    <LocalizedStrings>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="DisplayName">#Dirección de correo electrónico</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="UserHelpText">#Dirección de correo electrónico que puede usarse para ponerse en contacto con usted.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="email" StringId="PatternHelpText">#Introduzca una dirección de correo electrónico válida.  </LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="DisplayName">#Nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="UserHelpText">#Escriba la contraseña nueva</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="newPassword" StringId="PatternHelpText">#De 8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="DisplayName">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="UserHelpText">#Confirmar nueva contraseña</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="reenterPassword" StringId="PatternHelpText">#8 a 16 caracteres, que contengan 3 de los 4 tipos siguientes: caracteres en minúsculas, caracteres en mayúsculas, dígitos (0-9) y uno o más de los siguientes símbolos: @ # $ % ^ &amp; * - _ + = [ ] { } | \\ : ' , ? / ` ~ \" ( ) ; .</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="DisplayName">#Nombre para mostrar</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="displayName" StringId="UserHelpText">#Su nombre para mostrar.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="DisplayName">#Apellido</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="surname" StringId="UserHelpText">#Su apellido.</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="DisplayName">#Nombre</LocalizedString>
      <LocalizedString ElementType="ClaimType" ElementId="givenName" StringId="UserHelpText">#Su nombre (también conocido como nombre de pila).</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="button_continue">#Crear</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_fieldIncorrect">#Hay uno o varios campos rellenados de forma incorrecta. Compruebe las entradas y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_passwordEntryMismatch">#Los campos de entrada de contraseña no coinciden. Escriba la misma contraseña en ambos campos y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="error_requiredFieldMissing">#Falta un campo obligatorio. Rellene todos los campos necesarios y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="helplink_text">#¿Qué es esto?</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="initial_intro">#Proporcione los siguientes detalles.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="preloader_alt">#Espere</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="required_field">#Esta información es obligatoria.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_edit">#Cambiar correo electrónico</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_resend">#Enviar nuevo código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_send">#Enviar código de comprobación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_but_verify">#Comprobar código</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_code_expired">#El código ha expirado. Solicite otro nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_no_retry">#Ha realizado demasiados intentos incorrectos. Vuelva a intentarlo más tarde.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_retry">#Ese código es incorrecto. Inténtelo de nuevo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_server">#Tenemos problemas para comprobar la dirección de correo electrónico. Escriba una dirección de correo electrónico válida y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_fail_throttled">#Ha habido demasiadas solicitudes para comprobar esta dirección de correo electrónico. Espere un poco y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_info_msg">#Se ha enviado el código de verificación a su Bandeja de entrada. Cópielo en el siguiente cuadro de entrada.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_input">#Código de verificación</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_intro_msg">#La comprobación es obligatoria. Haga clic en el botón Enviar.</LocalizedString>
      <LocalizedString ElementType="UxElement" StringId="ver_success_msg">#Dirección de correo electrónico comprobada. Puede continuar.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="ServiceThrottled">#Hay demasiadas solicitudes en este momento. Espere un momento y vuelva a intentarlo.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimNotVerified">#Reclamación no comprobada: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfClaimsPrincipalAlreadyExists">#Ya existe un usuario con el id. especificado. Elija otro diferente.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfIncorrectPattern">#Patrón incorrecto para: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfInvalidInput">#{0} tiene una entrada no válida.</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfMissingRequiredElement">#Falta un elemento obligatorio: {0}</LocalizedString>
      <LocalizedString ElementType="ErrorMessage" StringId="UserMessageIfValidationError">#Error en la validación de: {0}</LocalizedString>
    </LocalizedStrings>
  </LocalizedResources>
</Localization>
```

## <a name="edit-the-content-definition-with-the-localization"></a>지역화를 사용하여 콘텐츠 정의 편집

복사한 ContentDefinitions의 전체 내용을 BuildingBlocks 요소의 자식으로 붙여넣습니다.

다음 예제에서는 영어(en) 및 스페인어(es) 사용자 지정 문자열이 등록 또는 로그인 페이지 및 로컬 계정 회원가입 페이지에 추가됩니다. 각 **LocalizedResourcesReference** 의 **LocalizedResourcesReferenceId** 는 해당 로캘과 동일하지만 임의 문자열을 식별자로 사용할 수 있습니다. 각 언어 및 페이지 조합에 대해 이전에 만든 해당 **LocalizedResources** 를 가리킵니다.

```xml
<ContentDefinitions>
  <ContentDefinition Id="api.signuporsignin">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.signuporsignin.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.signuporsignin.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>

  <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
  </ContentDefinition>
</ContentDefinitions>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>업데이트된 사용자 지정 정책 업로드 및 테스트

### <a name="upload-the-custom-policy"></a>사용자 지정 정책 업로드

1. 확장 파일을 저장합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택합니다.
1. 이전에 변경한 확장 파일을 업로드합니다.

### <a name="test-the-custom-policy-by-using-run-now"></a>**지금 실행** 을 사용하여 사용자 지정 정책 테스트

1. 업로드했던 정책을 선택한 다음 **지금 실행** 을 선택합니다.
1. 지역화된 등록 또는 로그인 페이지가 표시됩니다.
1. 등록 링크를 클릭하면 지역화된 등록 페이지를 볼 수 있습니다.
1. 브라우저의 기본 언어를 스페인어로 전환합니다. 또는 쿼리 문자열 매개 변수 `ui_locales`를 권한 부여 요청에 추가할 수 있습니다. 예를 들면 다음과 같습니다. 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize&client_id=0239a9cc-309c-4d41-12f1-31299feb2e82&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&ui_locales=es
```

::: zone-end


## <a name="additional-information"></a>추가 정보

::: zone pivot="b2c-user-flow"

### <a name="page-ui-customization-labels-as-overrides"></a>재정의로 페이지 UI 사용자 지정 레이블

언어 사용자 지정을 사용하도록 설정하면 페이지 UI 사용자 지정을 사용한 레이블에 대한 이전 편집이 영어(en)용 JSON 파일에서 유지됩니다. 언어 사용자 지정에서 언어 리소스를 업로드하여 레이블 및 기타 문자열을 계속해서 변경할 수 있습니다.

::: zone-end

### <a name="up-to-date-translations"></a>최신 번역

Microsoft는 사용자가 사용할 수 있는 가장 최신의 번역을 제공하기 위해 최선을 다하고 있습니다. Microsoft는 지속적으로 번역을 개선하고 사용자에 맞게 유지할 예정입니다. Microsoft는 버그 및 글로벌 용어의 변화를 파악하고 사용자 흐름에서 원활하게 작동하도록 업데이트할 예정입니다.

### <a name="support-for-right-to-left-languages"></a>오른쪽에서 왼쪽 언어 지원

현재 Microsoft는 오른쪽에서 왼쪽 언어에 대한 지원을 제공하지 않습니다. 사용자 지정 로캘을 사용하고 CSS를 사용하여 문자열이 표시되는 방식을 변경하여 지원할 수 있습니다. 이 기능이 필요한 경우 [Azure 피드백](https://feedback.azure.com/d365community/idea/10a7e89c-c325-ec11-b6e6-000d3a4f0789)에서 이 기능에 투표해 주세요.

### <a name="social-identity-provider-translations"></a>소셜 ID 공급자 변환

Microsoft는 소셜 로그인에 대한 `ui_locales` OIDC 매개 변수를 제공합니다. 하지만 Facebook과 Google을 포함한 일부 소셜 ID 공급자는 이러한 매개 변수를 인식하지 않습니다.

### <a name="browser-behavior"></a>브라우저 동작

Chrome과 Firefox는 설정된 언어를 요청합니다. 이 언어가 지원되는 언어이면 기본 언어보다 먼저 표시됩니다. Microsoft Edge에서는 현재 언어를 요청하지 않으며 기본 언어로 바로 이동합니다.

## <a name="supported-languages"></a>지원되는 언어

Azure AD B2C에는 다음 언어에 대한 지원이 포함되어 있습니다. 사용자 흐름 언어는 Azure AD B2C에서 제공합니다. MFA(다단계 인증) 알림 언어는 [Azure AD MFA](../active-directory/authentication/concept-mfa-howitworks.md)에서 제공합니다.

| 언어              | 언어 코드 | 사용자 흐름         | MFA 알림  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| 아랍어                | ar            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 불가리아어             | bg            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 벵골어                | bn            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 카탈로니아어               | ca            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 체코어                 | cs            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 덴마크어                | da            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 독일어                | de            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 그리스어                 | el            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 영어               | en            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 스페인어               | es            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 에스토니아어              | et            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 바스크어                | eu            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 핀란드어               | fi            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 프랑스어                | fr            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 갈리시아어              | gl            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 구자라트어              | gu            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 히브리어                | he            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 힌디어                 | hi            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 크로아티아어              | hr            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 헝가리어             | hu            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 인도네시아어            | id            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 이탈리아어               | it            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 일본어              | ja            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 카자흐어                | kk            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 칸나다어               | kn            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 한국어                | ko            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 리투아니아어            | lt            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 라트비아어               | lv            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 말라얄람어             | ml            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 마라티어               | mr            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 말레이어                 | ms            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 노르웨이어 복말      | nb            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 네덜란드어                 | nl            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 노르웨이어             | no            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 펀잡어               | pa            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 폴란드어                | pl            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 포르투갈어 - 브라질   | pt-br         | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 포르투갈어 - 포르투갈 | pt-pt         | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 루마니아어              | ro            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 러시아어               | ru            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 슬로바키아어                | sk            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 슬로베니아어             | sl            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 세르비아어 - 키릴 자모    | sr-cryl-cs    | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 세르비아어 - 라틴 문자       | sr-latn-cs    | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 스웨덴어               | sv            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 타밀어                 | ta            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 텔루구어                | te            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) |
| 태국어                  | th            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 터키어               | tr            | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 우크라이나어             | uk            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 베트남어            | vi            | ![아니요를 나타내는 X입니다.](./media/user-flow-language-customization/no.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 중국어 - 간체  | zh-hans       | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |
| 중국어 - 번체 | zh-hant       | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) | ![녹색 확인 표시](./media/user-flow-language-customization/yes.png) |


## <a name="next-steps"></a>다음 단계

::: zone pivot="b2c-user-flow"

[Azure Active Directory B2C에서 애플리케이션의 사용자 인터페이스 사용자 지정](customize-ui-with-html.md)에서 애플리케이션의 사용자 인터페이스를 사용자 지정하는 방법에 대한 자세한 정보를 확인합니다.

::: zone-end 

::: zone pivot="b2c-custom-policy"

- IEF 참조에서 [지역화](localization.md) 요소에 대한 자세한 내용을 알아보세요.
- Azure AD B2C에서 사용할 수 있는 [지역화 문자열 ID](localization-string-ids.md) 목록을 참조하세요.

::: zone-end 
