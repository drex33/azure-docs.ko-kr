---
title: 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 자체 어설션된 기술 프로필을 정의하는 방법을 설명합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 031b7b1ae4d776ce08a18da5e292d83206c4c3b2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131040155"
---
# <a name="define-a-self-asserted-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 자체 어설션된 기술 프로필 정의

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C(Azure Active Directory B2C)에서 사용자가 입력해야 하는 모든 상호 작용은 자체 어설션된 기술 프로필입니다. 등록 페이지, 로그인 페이지, 암호 재설정 페이지 등을 예로 들 수 있습니다.

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `Proprietary`로 설정해야 합니다. 프로필이 자체 어설션되도록 하려면 **handler** 특성이 Azure AD B2C에서 사용되는 프로토콜 처리기 어셈블리의 정규화된 이름을 포함해야 합니다. `Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`

다음 예제에서는 전자 메일 등록에 사용되는 자체 어설션된 기술 프로필을 보여 줍니다.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
```

## <a name="input-claims"></a>입력 클레임

자체 어설션된 기술 프로필에서 **InputClaims** 및 **InputClaimsTransformations** 요소를 사용하여 자체 어설션된 페이지(표시 클레임)에 표시되는 클레임 값을 미리 채울 수 있습니다. 예를 들어 프로필 편집 정책의 사용자 경험에서는 먼저 Azure AD B2C 디렉터리 서비스에서 사용자 프로필을 읽습니다. 그러면 자체 어설션된 기술 프로필이 사용자 프로필에 저장된 사용자 데이터로 입력 클레임을 설정합니다. 이러한 클레임은 사용자 프로필에서 수집된 후 사용자에게 표시되며, 그러면 사용자가 기존 데이터를 편집할 수 있습니다.

```xml
<TechnicalProfile Id="SelfAsserted-ProfileUpdate">
...
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
    <InputClaim ClaimTypeReferenceId="userPrincipalName" />
    <InputClaim ClaimTypeReferenceId="givenName" />
    <InputClaim ClaimTypeReferenceId="surname" />
  </InputClaims>
```

## <a name="display-claims"></a>표시 클레임

표시 클레임 기능은 현재 **미리 보기** 상태입니다.

**DisplayClaims** 요소에는 사용자로부터 데이터를 수집하기 위해 화면에 표시할 클레임 목록이 포함되어 있습니다. 표시 클레임 값을 미리 채우려면 앞에서 설명한 입력 클레임을 사용합니다. 이 요소는 기본값을 포함할 수도 있습니다.

**DisplayClaims** 의 클레임 순서는 Azure AD B2C가 화면에서 클레임을 렌더링하는 순서를 지정합니다. 사용자가 특정 클레임에 대한 값을 입력하도록 강제 지정하려면 **DisplayClaim** 요소의 **Required** 특성을 `true`로 설정합니다.

Azure AD B2C에서 지원하는 모든 사용자 입력 유형에 대해 **DisplayClaims** 컬렉션의 **ClaimType** 요소는 **UserInputType** 요소로 설정해야 합니다. 예를 들어 `TextBox` 또는 `DropdownSingleSelect`입니다.

### <a name="add-a-reference-to-a-displaycontrol"></a>DisplayControl에 참조 추가

표시 클레임 컬렉션에서 사용자가 만든 [DisplayControl](display-controls.md)에 대한 참조를 포함할 수 있습니다. 표시 컨트롤은 특수 기능이 있고 Azure AD B2C 백 엔드 서비스와 상호 작용하는 사용자 인터페이스 요소입니다. 이를 통해 사용자는 백 엔드에서 유효성 검사 기술 프로필을 호출하는 페이지에서 작업을 수행할 수 있습니다. 예를 들어 메일 주소, 전화 번호 또는 고객 전용 번호를 확인합니다.

다음 예에서는 `TechnicalProfile`은 표시 컨트롤에 표시 클레임을 사용하는 방법을 보여 줍니다.

* 첫 번째 표시 클레임은 메일 주소를 수집하고 확인하는 `emailVerificationControl` 표시 컨트롤을 참조합니다.
* 다섯 번째 표시 클레임은 전화 번호를 수집하고 확인하는 `phoneVerificationControl` 표시 컨트롤을 참조합니다.
* 다른 표시 클레임은 사용자로부터 수집되는 ClaimTypes입니다.

```xml
<TechnicalProfile Id="Id">
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim DisplayControlReferenceId="phoneVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
</TechnicalProfile>
```

앞서 언급했듯이 표시 컨트롤을 참조하는 표시 클레임은 메일 주소를 확인하는 등 고유한 유효성 검사를 실행할 수 있습니다. 또한 자체 어설션된 페이지는 다음 오케스트레이션 단계로 이동하기 전에 유효성 검사 기술 프로필을 사용하여 사용자 입력(클레임 유형 또는 표시 컨트롤)을 비롯한 전체 페이지의 유효성을 검사하도록 지원합니다.

### <a name="combine-usage-of-display-claims-and-output-claims-carefully"></a>표시 클레임의 사용과 출력 클레임의 사용을 신중하게 결합

자체 어설션된 기술 프로필에서 하나 이상의 **DisplayClaim** 요소를 지정하는 경우 화면에 표시하고 사용자로부터 수집할 ‘모든’ 클레임에 DisplayClaim을 사용해야 합니다. 하나 이상의 표시 클레임이 포함된 자체 어설션된 기술 프로필에서 표시하는 출력 클레임은 없습니다.

`age` 클레임이 기본 정책에서 **출력** 클레임으로 정의되는 다음 예를 살펴보겠습니다. 자체 어설션된 기술 프로필에 표시 클레임을 추가하기 전에 사용자의 데이터 컬렉션에 대한 `age` 클레임이 화면에 표시됩니다.

```xml
<TechnicalProfile Id="id">
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="age" />
  </OutputClaims>
</TechnicalProfile>
```

해당 기본 정책을 상속하는 리프 정책이 이후에 `officeNumber`를 **표시** 클레임으로 지정하는 경우:

```xml
<TechnicalProfile Id="id">
  <DisplayClaims>
    <DisplayClaim ClaimTypeReferenceId="officeNumber" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="officeNumber" />
  </OutputClaims>
</TechnicalProfile>
```

기본 정책의 `age` 클레임은 화면에서 사용자에게 더 이상 표시되지 않으며 실제로 “숨김” 상태입니다. `age` 클레임을 표시하고 사용자로부터 age 값을 수집하려면 `age` **DisplayClaim** 를 추가해야 합니다.

## <a name="output-claims"></a>출력 클레임

**OutputClaims** 요소에는 다음 오케스트레이션 단계로 반환되는 클레임 목록이 포함되어 있습니다. **DefaultValue** 특성은 클레임이 설정된 적이 없는 경우에만 적용됩니다. 이전 오케스트레이션 단계에서 설정된 경우에는 사용자가 값을 비워 두더라도 기본값이 적용되지 않습니다. 기본값을 강제로 사용하려면 **AlwaysUseDefaultValue** 특성을 `true`로 설정합니다.

보안상의 이유로 암호 클레임 값(`Password`로 설정된 `UserInputType`)은 자체 어설션된 기술 프로필의 유효성 검사 기술 프로필에만 사용할 수 있습니다. 다음 오케스트레이션 단계에서는 암호 클레임을 사용할 수 없습니다. 

> [!NOTE]
> 이전 버전의 IEF(Identity Experience Framework)에서 출력 클레임은 사용자로부터 데이터를 수집하는 데 사용되었습니다. 사용자로부터 데이터를 수집하려면 **DisplayClaims** 컬렉션을 대신 사용합니다.

**OutputClaimsTransformations** 요소는 출력 클레임을 수정하거나 새 출력 클레임을 생성하는 데 사용되는 **OutputClaimsTransformation** 요소 컬렉션을 포함할 수 있습니다.

### <a name="when-you-should-use-output-claims"></a>출력 클레임을 사용해야 하는 경우

자체 어설션된 기술 프로필에서 출력 클레임 컬렉션은 다음 오케스트레이션 단계로 클레임을 반환합니다.

다음 경우에 출력 클레임을 사용합니다.

- **클레임이 출력 클레임 변환에 의해 출력되는 경우**.
- 사용자로부터 데이터를 수집하거나 유효성 검사 기술 프로필에서 데이터를 반환하지 않고 **출력 클레임에서 기본값을 설정하는 경우**. `LocalAccountSignUpWithLogonEmail` 자체 어설션된 기술 프로필은 **executed-SelfAsserted-Input** 클레임을 `true`로 설정합니다.
- **유효성 검사 기술 프로필이 출력 클레임 반환** - 기술 프로필이 일부 클레임을 반환하는 유효성 검사 기술 프로필을 호출할 수 있습니다. 해당 클레임을 확장하여 사용자 경험의 다음 오케스트레이션 단계로 반환할 수 있습니다. 로컬 계정으로 로그인할 때 자체 어설션된 기술 프로필 `SelfAsserted-LocalAccountSignin-Email`이 유효성 검사 기술 프로필 `login-NonInteractive`를 호출하는 경우를 예로 들어 보겠습니다. 이 기술 프로필은 사용자 자격 증명의 유효성을 검사하며 사용자 프로필도 반환합니다. 즉, 'userPrincipalName', 'displayName', 'givenName', 'surName' 등의 프로필을 반환할 수 있습니다.
- **표시 컨트롤은 출력 클레임을 반환합니다**. 기술 프로필은 [표시 컨트롤](display-controls.md)을 참조할 수 있습니다. 표시 컨트롤은 확인된 메일 주소와 같은 일부 클레임을 반환합니다. 해당 클레임을 확장하여 사용자 경험의 다음 오케스트레이션 단계로 반환할 수 있습니다. 표시 컨트롤 기능은 현재 **미리 보기** 상태입니다.

다음 예에서는 표시 클레임과 출력 클레임을 모두 사용하는 자체 어설션된 기술 프로필을 사용하는 방법을 보여 줍니다.

```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <DisplayClaims>
    <DisplayClaim DisplayControlReferenceId="emailVerificationControl" />
    <DisplayClaim DisplayControlReferenceId="SecondaryEmailVerificationControl" />
    <DisplayClaim ClaimTypeReferenceId="displayName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="givenName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="surName" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <DisplayClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
  </DisplayClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

### <a name="output-claims-sign-up-or-sign-in-page"></a>출력 클레임 등록 또는 로그인 페이지

결합된 등록 및 로그인 페이지에서 콘텐츠 정의 [DataUri](contentdefinitions.md#datauri) 요소를 사용할 때 `unifiedssp` 또는 `unifiedssd` 페이지 유형을 지정하는 다음 사항에 유의하세요.

- 사용자 이름 및 암호 클레임만 렌더링됩니다.
- 처음 두 개의 출력 클레임은 사용자 이름과 암호여야 합니다(이 순서대로). 
- 다른 모든 클레임은 렌더링되지 않습니다. 해당 클레임의 경우 `defaultValue`를 설정하거나 클레임 양식 유효성 검사 기술 프로필을 호출해야 합니다. 

## <a name="persist-claims"></a>영구 클레임

PersistedClaims 요소는 사용되지 않습니다. 자체 어설션된 기술 프로필은 Azure AD B2C에 데이터를 유지하지 않습니다. 대신 데이터 유지를 수행하는 유효성 검사 기술 프로필을 호출합니다. 등록 정책이 자체 어설션된 기술 프로필 `LocalAccountSignUpWithLogonEmail`을 사용하여 새 사용자 프로필을 수집하는 경우를 예로 들어 보겠습니다. `LocalAccountSignUpWithLogonEmail` 기술 프로필은 Azure AD B2C에 계정을 만들기 위해 유효성 검사 기술 프로필을 호출합니다.

## <a name="validation-technical-profiles"></a>유효성 검사 기술 프로필

유효성 검사 기술 프로필은 참조 기술 프로필의 일부 또는 모든 출력 클레임 유효성을 검사하는 데 사용됩니다. 유효성 검사 기술 프로필의 입력 클레임은 자체 어설션된 기술 프로필의 출력 클레임에 표시되어야 합니다. 유효성 검사 기술 프로필은 사용자 입력의 유효성을 검사하며, 사용자에게 오류를 반환할 수 있습니다.

[Azure Active Directory](active-directory-technical-profile.md) 또는 [REST API](restful-technical-profile.md) 기술 프로필과 같이 정책에 포함된 어떤 기술 프로필이든 유효성 검사 기술 프로필로 사용될 수 있습니다. 위의 예제에서 `LocalAccountSignUpWithLogonEmail` 기술 프로필은 signinName이 디렉터리에 없는지 유효성을 검사합니다. signinName이 디렉터리에 없으면 유효성 검사 기술 프로필은 로컬 계정을 만들고 objectId, authenticationSource, newUser를 반환합니다. `SelfAsserted-LocalAccountSignin-Email` 기술 프로필은 사용자 자격 증명의 유효성을 검사하기 위해 `login-NonInteractive` 유효성 검사 기술 프로필을 호출합니다.

비즈니스 논리를 사용하여 REST API 기술 프로필을 호출하거나, 입력 클레임을 덮어쓰거나, 회사 LOB(기간 업무) 애플리케이션과 추가로 통합하여 사용자 데이터를 보강할 수도 있습니다. 자세한 내용은 [유효성 검사 기술 프로필](validation-technical-profile.md)을 참조하세요.

## <a name="metadata"></a>메타데이터

| attribute | 필수 | 설명 |
| --------- | -------- | ----------- |
| setting.operatingMode <sup>1</sup>| 예 | 로그인 페이지의 경우 이 속성은 입력 유효성 검사, 오류 메시지 등 username 필드의 동작을 제어합니다. 필요한 값은 `Username` 또는 `Email`입니다.  |
| AllowGenerationOfClaimsWithNullValues| 예| null 값이 포함된 클레임을 생성할 수 있습니다. 예를 들어 사용자가 확인란을 선택하지 않은 경우입니다.|
| ContentDefinitionReferenceId | 예 | 이 기술 프로필과 연결된 [콘텐츠 정의](contentdefinitions.md)의 식별자입니다. |
| EnforceEmailVerification | 예 | 등록 또는 프로필 편집의 경우 전자 메일 확인을 적용합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.retryLimit | 예 | 사용자가 유효성 검사 기술 프로필에 대해 확인된 데이터를 제공하려고 시도할 수 있는 횟수를 제어합니다. 사용자가 이미 있는 계정으로 등록을 계속 시도할 수 있는 제한 횟수를 예로 들 수 있습니다.
| SignUpTarget <sup>1</sup>| 예 | 등록 대상 교환 식별자입니다. 사용자가 등록 단추를 클릭하면 Azure AD B2C는 지정된 교환 식별자를 실행합니다. |
| setting.showCancelButton | 예 | 취소 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.showContinueButton | 예 | 계속 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.showSignupLink <sup>2</sup>| 예 | 등록 단추를 표시합니다. 가능한 값은 `true`(기본값) 또는 `false`입니다. |
| setting.forgotPasswordLinkLocation <sup>2</sup>| 예| 암호 찾기 링크를 표시합니다. 가능한 값은 레이블 바로 뒤 또는 레이블이 없는 경우 암호 입력 필드 뒤에 링크를 표시하는 `AfterLabel`(기본값), 암호 입력 필드 뒤에 링크를 표시하는 `AfterInput`, 단추 뒤의 양식 하단에 링크를 표시하는 `AfterButtons` 또는 암호 찾기 링크를 제거하는 `None`입니다.|
| setting.enableRememberMe <sup>2</sup>| 예| [로그인 유지](session-behavior.md?pivots=b2c-custom-policy#enable-keep-me-signed-in-kmsi) 확인란을 표시합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. |
| setting.inputVerificationDelayTimeInMilliseconds <sup>3</sup>| 예| 사용자가 입력을 중지할 때까지 기다린 다음 값의 유효성을 검사하여 사용자 환경을 개선합니다. 기본값은 2,000밀리초입니다. |
| IncludeClaimResolvingInClaimsHandling  | 예 | 입력 및 출력 클레임의 경우 기술 프로필에 [클레임 해결](claim-resolver-overview.md)이 포함되는지 여부를 지정합니다. 가능한 값은 `true` 또는 `false`(기본값)입니다. 기술 프로필에서 클레임 해결 프로그램을 사용하려면 이를 `true`로 설정합니다. |
|forgotPasswordLinkOverride <sup>4</sup>| 예 | 실행할 암호 재설정 클레임 교환입니다. 자세한 내용은 [셀프 서비스 암호 재설정](add-password-reset-policy.md)을 참조하세요. |

참고:
1. `unifiedssp` 또는 `unifiedssd`의 콘텐츠 정의 [DataUri](contentdefinitions.md#datauri) 형식에 사용할 수 있습니다.
1. `unifiedssp` 또는 `unifiedssd`의 콘텐츠 정의 [DataUri](contentdefinitions.md#datauri) 형식에 사용할 수 있습니다. [페이지 레이아웃 버전](page-layout.md) 1.1.0 이상.
1. [페이지 레이아웃 버전](page-layout.md) 1.2.0 이상에 사용할 수 있습니다.
1. `unifiedssp`의 콘텐츠 정의 [DataUri](contentdefinitions.md#datauri) 형식에 사용할 수 있습니다. [페이지 레이아웃 버전](page-layout.md) 2.1.2 이상.

## <a name="cryptographic-keys"></a>암호화 키

**CryptographicKeys** 요소는 사용되지 않습니다.
