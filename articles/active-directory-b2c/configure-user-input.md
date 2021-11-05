---
title: 사용자 특성 추가 및 사용자 입력 사용자 지정
titleSuffix: Azure AD B2C
description: 사용자 입력을 사용자 지정하고 Azure Active Directory B2C의 가입 또는 로그인 경험에 사용자 특성을 추가하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 042829ac525ed911f1a9ec984843931e8029b0a6
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007470"
---
#  <a name="add-user-attributes-and-customize-user-input-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 사용자 특성 추가 및 사용자 입력 사용자 지정

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 가입 과정 중 새 특성을 수집합니다. 사용자의 도시를 가져오고, 드롭다운으로 구성하고, 제공해야 하는지 여부를 정의합니다.

> [!IMPORTANT]
> 이 샘플에서는 기본 제공 클레임 'city'를 사용합니다. 대신 지원되는 [Azure AD B2C 기본 제공 특성](user-profile-attributes.md) 또는 사용자 지정 특성 중 하나를 선택할 수 있습니다. 사용자 지정 특성을 사용하려면 [사용자 지정 특성을 사용하도록 설정합니다](user-flow-custom-attributes.md). 다른 기본 제공 또는 사용자 지정 특성을 사용하려면 'city'를 선택한 특성(예: 기본 제공 특성 *jobTitle* 또는 *extension_loyaltyId* 와 같은 사용자 지정 특성)으로 대체합니다.  

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="add-user-attributes-your-user-flow"></a>사용자 흐름에 사용자 특성 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. Azure AD B2C 테넌트에서 **사용자 흐름** 을 선택합니다.
1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. **사용자 특성** 을 선택한 다음, 사용자 특성을 선택합니다(예: "City"). 
1. **저장** 을 선택합니다.

## <a name="provide-optional-claims-to-your-app"></a>앱에 선택적인 클레임 제공

애플리케이션 클레임은 애플리케이션에 반환되는 값입니다. 원하는 클레임을 포함하도록 사용자 흐름을 업데이트합니다.

1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. **애플리케이션 클레임** 을 선택합니다.
1. 애플리케이션으로 다시 보낼 특성을 선택합니다(예: "City").
1. **저장** 을 선택합니다.
 
## <a name="configure-user-attributes-input-type"></a>사용자 특성 입력 형식 구성

1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. **페이지 레이아웃** 을 선택합니다.
1. **로컬 계정 회원가입 페이지** 를 선택합니다.
1. **사용자 특성** 아래에서 **City** 를 선택합니다.
    1. **사용자 입력 형식** 드롭다운에서 **DropdownSingleSelect** 를 선택합니다. 선택 사항: "위로/아래로 이동" 단추를 사용하여 가입 페이지에서 텍스트 순서를 정렬합니다.
    1. **선택 사항** 드롭다운에서 **아니요** 를 선택합니다.
1. **저장** 을 선택합니다. 

### <a name="provide-a-list-of-values-by-using-localized-collections"></a>지역화된 컬렉션을 사용하여 값 목록 제공

city 특성에 대한 값의 집합 목록을 제공하려면 다음을 수행합니다. 

1. [사용자 흐름에서 언어 사용자 지정 사용](language-customization.md#support-requested-languages-for-ui_locales)
1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. 사용자 흐름에 대한 **언어** 페이지에서 사용자 지정하려는 언어를 선택합니다.
1. **페이지 수준 리소스 파일** 아래에서 **로컬 계정 가입 페이지** 를 선택합니다.
1. **기본값 다운로드**(또는 이전에 이 언어를 편집한 경우 **재정의 다운로드**)를 선택합니다.
1. `LocalizedCollections` 특성을 만듭니다.

`LocalizedCollections`는 `Name` 및 `Value` 쌍의 배열입니다. 항목의 순서대로 항목이 표시됩니다. 

* `ElementId`는 이 `LocalizedCollections` 특성이 응답인 사용자 특성입니다.
* `Name`은 사용자에게 표시되는 값입니다.
* `Value`는 이 옵션이 선택될 때 클레임에 반환된 항목입니다.

```json
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [
    {
      "ElementType": "ClaimType",
      "ElementId": "city",
      "TargetCollection": "Restriction",
      "Override": true,
      "Items": [
        {
          "Name": "Berlin",
          "Value": "Berlin"
        },
        {
          "Name": "London",
          "Value": "London"
        },
        {
          "Name": "Seattle",
          "Value": "Seattle"
        }
      ]
    }
  ]
}
```

#### <a name="upload-your-changes"></a>변경 내용 업로드

1. JSON 파일 변경을 완료했으면 B2C 테넌트로 돌아갑니다.
1. **사용자 흐름** 을 선택하고 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. **언어** 를 선택합니다.
1. 번역하려는 언어를 선택합니다.
1. **페이지 수준 리소스 파일** 아래에서 **로컬 계정 가입 페이지** 를 선택합니다.
1. 폴더 아이콘을 선택하고 업로드할 JSON 파일을 선택합니다. 변경 내용이 자동으로 사용자 흐름에 저장됩니다.

## <a name="test-your-user-flow"></a>사용자 흐름 테스트

1. 정책(예: "B2C_1_SignupSignin")을 선택하여 엽니다.
1. 정책을 테스트하려면 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 의 경우 이전에 등록한 *testapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 실행** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="overview"></a>개요

가입 또는 로그인 사용자 경험을 사용하여 사용자로부터 초기 데이터를 수집할 수 있습니다. 나중에 프로필 편집 사용자 경험을 통해 추가 클레임을 수집할 수 있습니다. Azure AD B2C는 대화형으로 사용자로부터 직접 정보를 수집할 때마다 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)을 사용합니다. 이 샘플에서는 다음을 수행합니다.

1. "city" 클레임을 정의합니다. 
1. 사용자에게 city를 요청합니다.
1. Azure AD B2C 디렉터리에서 사용자 프로필에 city를 유지합니다.
1. 각 로그인의 Azure AD B2C 디렉터리에서 city 클레임을 읽습니다.
1. 로그인 또는 가입 후 신뢰 당사자 애플리케이션에 city를 반환합니다.  

## <a name="define-a-claim"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. [클레임 스키마](claimsschema.md)에서 클레임을 선언합니다. 다음 요소는 클레임을 정의하는 데 사용됩니다.

- **DisplayName** - 사용자에게 표시되는 레이블을 정의하는 문자열입니다.
- [DataType](claimsschema.md#datatype) - 클레임의 형식입니다.
- **UserHelpText** - 사용자가 필요한 내용을 파악할 수 있도록 도와줍니다.
- [UserInputType](claimsschema.md#userinputtype) - 텍스트 상자, 라디오 선택, 드롭다운 목록 또는 여러 선택 항목과 같은 입력 컨트롤의 형식입니다.

정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.

1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 city 클레임을 추가합니다.  

```xml
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="city">
      <DisplayName>City where you work</DisplayName>
      <DataType>string</DataType>
      <UserInputType>DropdownSingleSelect</UserInputType>
      <Restriction>
        <Enumeration Text="Berlin" Value="berlin" />
        <Enumeration Text="London" Value="london" />
        <Enumeration Text="Seattle" Value="seattle" />
      </Restriction>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks>-->
```

페이지가 처음 로드될 때 기본적으로 선택되도록 `Enumeration` 요소에 [SelectByDefault](claimsschema.md#enumeration) 특성을 포함합니다. 예를 들어 *London* 항목을 미리 선택하려면 `Enumeration` 요소를 다음 예제로 변경합니다.

```xml
<Restriction>
  <Enumeration Text="Berlin" Value="berlin" />
  <Enumeration Text="London" Value="london" SelectByDefault="true" />
  <Enumeration Text="Seattle" Value="seattle" />
</Restriction>
```

## <a name="add-a-claim-to-the-user-interface"></a>사용자 인터페이스에 클레임 추가

다음 기술 프로필은 사용자가 입력을 제공해야 할 때 호출되는 [자체 어설션](self-asserted-technical-profile.md)됩니다.

- **LocalAccountSignUpWithLogonEmail** - 로컬 계정 가입 흐름입니다.
- **SelfAsserted-Social** - 페더레이션 계정 첫 번째 사용자 로그인입니다.
- **SelfAsserted-ProfileUpdate** - 프로필 흐름을 편집합니다.

가입하는 동안 city 클레임을 수집하려면 `LocalAccountSignUpWithLogonEmail` 기술 프로필에 출력 클레임으로 추가해야 합니다. 확장 파일로 이 기술 프로필을 재정의합니다. 전체 출력 클레임 목록을 지정하여 화면에 클레임이 표시되는 순서를 제어합니다. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 ClaimsProvider를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

페더레이션 계정으로 초기 로그인한 후 city 클레임을 수집하려면 `SelfAsserted-Social` 기술 프로필에 출력 클레임으로 추가해야 합니다. 로컬 및 페더레이션 계정 사용자가 나중에 프로필 데이터를 편집할 수 있도록 `SelfAsserted-ProfileUpdate` 기술 프로필에 입력 및 출력 클레임을 추가합니다. 확장 파일로 해당 기술 프로필을 재정의합니다. 전체 출력 클레임 목록을 지정하여 화면에 클레임이 표시되는 순서를 제어합니다. **ClaimsProviders** 요소를 찾습니다. 다음과 같이 새 ClaimsProvider를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="city" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>클레임 읽기 및 쓰기

다음 기술 프로필은 Azure Active Directory에서 데이터를 읽고 쓰는 [Active Directory 기술 프로필](active-directory-technical-profile.md)입니다.  
`PersistedClaims`를 사용하여 사용자 프로필에 데이터를 쓰고 `OutputClaims`를 사용하여 해당 Active Directory 기술 프로필 내에서 사용자 프로필의 데이터를 읽습니다.

확장 파일로 해당 기술 프로필을 재정의합니다. **ClaimsProviders** 요소를 찾습니다.  다음과 같이 새 ClaimsProvider를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user resets the password. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>토큰에 클레임 포함하기 

city 클레임을 신뢰 당사자 애플리케이션에 다시 반환하려면 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 파일에 출력 클레임을 추가합니다. 사용자 경험이 성공하면 출력 클레임이 토큰에 추가되고 애플리케이션으로 전송됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정하여 city를 출력 클레임으로 추가합니다.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="upload-and-test-your-updated-custom-policy"></a>업데이트된 사용자 지정 정책 업로드 및 테스트

1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택합니다.
1. 이전에 변경한 정책 파일을 업로드합니다.

### <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 가입 또는 로그인 페이지에서 **지금 가입** 을 선택하여 가입합니다. 도시 이름을 포함한 사용자 정보를 입력한 다음, **만들기** 를 클릭합니다. 반환된 토큰의 콘텐츠가 표시됩니다.

::: zone-end

가입 화면은 다음 스크린샷과 비슷해야 합니다.

![수정된 등록 옵션의 스크린샷](./media/configure-user-input/signup-with-city-claim-drop-down-example.png)

애플리케이션으로 다시 전송되는 토큰에는 `city` 클레임이 포함됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Berlin"
  ...
}
```

::: zone pivot="b2c-custom-policy"

## <a name="optional-localize-the-ui"></a>[선택 사항] UI 지역화

Azure AD B2C를 사용하면 다른 언어에 정책을 적용할 수 있습니다. 자세한 내용은 [언어 환경을 사용자 지정하는 방법을 알아봅니다](language-customization.md). 가입 페이지를 지역화하려면 [지원되는 언어 목록을 설정](language-customization.md#set-up-the-list-of-supported-languages)하고, [언어별 레이블을 제공](language-customization.md#provide-language-specific-labels)합니다.

> [!NOTE]
> 언어별 레이블과 함께 `LocalizedCollection`을 사용하는 경우 [클레임 정의](#define-a-claim)에서 `Restriction` 컬렉션을 제거할 수 있습니다.

다음 예제에서는 영어 및 스페인어의 도시 목록을 제공하는 방법을 보여 줍니다. 둘 다 영어 및 스페인어 항목 목록을 사용하여 *city* 클레임의 `Restriction` 컬렉션을 설정합니다. [SelectByDefault](claimsschema.md#enumeration)를 사용하면 페이지가 처음 로드될 때 항목이 기본적으로 선택됩니다.
   
```xml
<!-- 
<BuildingBlocks>-->
  <Localization Enabled="true">
    <SupportedLanguages DefaultLanguage="en" MergeBehavior="Append">
      <SupportedLanguage>en</SupportedLanguage>
      <SupportedLanguage>es</SupportedLanguage>
    </SupportedLanguages>
    <LocalizedResources Id="api.localaccountsignup.en">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlin" Value="Berlin"></Item>
          <Item Text="London" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
    <LocalizedResources Id="api.localaccountsignup.es">
      <LocalizedCollections>
        <LocalizedCollection ElementType="ClaimType" ElementId="city" TargetCollection="Restriction">
          <Item Text="Berlina" Value="Berlin"></Item>
          <Item Text="Londres" Value="London" SelectByDefault="true"></Item>
          <Item Text="Seattle" Value="Seattle"></Item>
        </LocalizedCollection>
      </LocalizedCollections>
    </LocalizedResources>
  </Localization>
<!-- 
</BuildingBlocks>-->
```

localization 요소를 추가한 후 [지역화를 사용하여 콘텐츠 정의를 편집](language-customization.md#edit-the-content-definition-with-the-localization)합니다. 다음 예제에서는 영어(en) 및 스페인어(es) 사용자 지정 지역화된 리소스가 가입 페이지에 추가됩니다.
   
```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
   <ContentDefinition Id="api.localaccountsignup">
    <LocalizedResourcesReferences MergeBehavior="Prepend">
        <LocalizedResourcesReference Language="en" LocalizedResourcesReferenceId="api.localaccountsignup.en" />
        <LocalizedResourcesReference Language="es" LocalizedResourcesReferenceId="api.localaccountsignup.es" />
    </LocalizedResourcesReferences>
   </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks>-->
```

## <a name="next-steps"></a>다음 단계

- IEF 참조에서 [ClaimsSchema](claimsschema.md) 요소에 대한 자세한 내용을 알아보세요.
- [Azure AD B2C에서 사용자 지정 특성을 사용](user-flow-custom-attributes.md)하는 방법을 알아봅니다.

::: zone-end
