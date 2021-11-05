---
title: 토큰 보강 - Azure Active Directory B2C
description: API를 사용하여 외부 소스의 클레임으로 토큰을 보강합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/29/2021
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8f5bf34424754106c6d86195c37d23c3c9060c1c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008354"
---
# <a name="enrich-tokens-with-claims-from-external-sources-using-api-connectors"></a>API 커넥터를 사용하여 외부 소스의 클레임으로 토큰 보강

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Azure AD B2C(Azure Active Directory B2C)를 사용하면 ID 개발자가 [API 커넥터](api-connectors-overview.md)를 사용하여 RESTful API와의 상호 작용을 사용자 흐름에 통합할 수 있습니다. 이 연습이 끝나면 API와 상호 작용하는 Azure AD B2C 사용자 흐름을 만들어 외부 원본의 정보로 토큰을 보강할 수 있습니다.

::: zone pivot="b2c-user-flow"

**토큰을 보내기 전(미리 보기)** 단계에 적용된 API 커넥터를 사용하여 외부 원본의 정보로 애플리케이션에 대한 토큰을 보강할 수 있습니다. 사용자가 로그인하거나 가입하면 Azure AD B2C 클라우드 서비스, 사용자 지정 사용자 저장소, 사용자 지정 권한 시스템, 레거시 ID 시스템 등과 같은 다운스트림 서비스에서 사용자에 대한 정보를 쿼리할 수 있는 API 커넥터에 구성된 API 엔드포인트를 호출합니다.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

[샘플](api-connector-samples.md#api-connector-rest-api-samples) 중 하나를 사용하여 API 엔드포인트를 만들 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-api-connector"></a>API 커넥터 만들기

[API 커넥터](api-connectors-overview.md)를 사용하려면 먼저 API 커넥터를 만든 다음, 사용자 흐름에서 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다.
4. **API 커넥터** 를 선택한 다음 **새 API 커넥터** 를 선택합니다.

   ![기본 API 커넥터 구성의 스크린샷](media/add-api-connector-token-enrichment/api-connector-new.png)

5. 호출의 표시 이름을 제공합니다. 예: **외부 원본에서 토큰 보강**.
6. API 호출에 대한 **엔드포인트 URL** 을 제공합니다.
7. **인증 유형** 을 선택하고 API 호출에 대한 인증 정보를 구성합니다. [API 커넥터 보호](secure-rest-api.md) 방법에 대해 알아봅니다.

   ![API 커넥터에 대한 인증 구성 스크린샷](media/add-api-connector-token-enrichment/api-connector-config.png)

8. **저장** 을 선택합니다.

## <a name="enable-the-api-connector-in-a-user-flow"></a>사용자 흐름에서 API 커넥터를 사용하도록 설정

등록 사용자 흐름에 API 커넥터를 추가하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다.
4. **사용자 흐름** 을 선택하고 API 커넥터를 추가하려는 사용자 흐름을 선택합니다.
5. **API 커넥터** 를 선택한 다음 사용자 흐름의 **토큰을 보내기 전(미리 보기)** 단계에서 호출할 API 엔드포인트를 선택합니다.

   ![사용자 흐름 단계에 대한 API 커넥터 선택 스크린샷](media/add-api-connector-token-enrichment/api-connectors-user-flow-select.png)

6. **저장** 을 선택합니다.

이 단계는 **가입 및 로그인(권장)** , **가입(권장)** 및 **로그인(권장)** 사용자 흐름에만 존재합니다.

## <a name="example-request-sent-to-the-api-at-this-step"></a>이 단계에서 API로 보낸 예제 요청

이 단계에서 API 커넥터는 로그인 및 가입 중에 토큰이 발급되려고 할 때 호출됩니다. 

API 커넥터는 **HTTP POST** 요청으로 구체화하여 사용자 특성('클레임')을 JSON 본문의 키-값 쌍으로 보냅니다. 특성은 [Microsoft Graph](/graph/api/resources/user#properties) 사용자 속성과 유사하게 직렬화됩니다. 

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "objectId": "ab3ec3b2-a435-45e4-b93a-56a005e88bb7",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "client_id": "231c70e8-8424-48ac-9b5d-5623b9e4ccf3",
 "step": "PreTokenIssuance",
 "ui_locales":"en-US"
}
```

API로 전송되는 클레임은 사용자에 대해 정의된 정보에 따라 다릅니다.

**Azure AD B2C**  >  **사용자 특성** 환경에 나열된 사용자 속성 및 사용자 지정 특성만 요청으로 보낼 수 있습니다.

사용자 지정 특성은 디렉터리의 **extension_\<extensions-app-id>_CustomAttribute** 형식으로 존재합니다. API는 동일한 직렬화된 형식으로 클레임을 수신해야 합니다. 사용자 지정 특성에 대한 자세한 내용은 [Azure AD B2C에서 사용자 지정 특성 정의](user-flow-custom-attributes.md)를 참조하세요.

또한 이러한 클레임은 일반적으로 이 단계의 모든 요청에서 전송됩니다.
- **UI 로캘('ui_locales')** - 디바이스에 구성된 최종 사용자의 로캘입니다. 이는 API에서 다국어 응답을 반환하는 데 사용할 수 있습니다.
- **단계('step')** - API 커넥터가 호출된 사용자 흐름의 단계 또는 지점입니다. 이 단계의 값은 `
- **클라이언트 ID('client_id')** - 최종 사용자가 사용자 흐름에서 인증하는 애플리케이션의 `appId` 값입니다. 이는 액세스 토큰에 있는 리소스 애플리케이션의 `appId`가 *아닙니다*.
- **objectId** - 사용자의 식별자입니다. 이를 사용하여 다운스트림 서비스에서 사용자에 대한 정보를 쿼리할 수 있습니다.
  
> [!IMPORTANT]
> API 엔드포인트가 호출 될 때 클레임에 값이 없으면 클레임은 API로 전송되지 않습니다. API는 클레임이 요청에 없는 경우를 명시적으로 확인하고 처리하도록 설계해야 합니다.

## <a name="expected-response-types-from-the-web-api-at-this-step"></a>이 단계에서 웹 API의 예상 응답 유형

웹 API가 사용자 흐름 중에 Azure AD에서 HTTP 요청을 받으면 "연속 응답"을 반환할 수 있습니다.

### <a name="continuation-response"></a>연속 응답

연속 응답은 사용자 흐름이 다음 단계인 토큰 발행으로 계속 진행되어야 함을 나타냅니다.

연속 응답에서 API는 추가 클레임을 반환할 수 있습니다. 토큰에 반환하려는 API에서 반환하는 클레임은 기본 제공 클레임이거나 [사용자 지정 특성으로 정의](user-flow-custom-attributes.md)되어야 하며 사용자 흐름의 **애플리케이션 클레임** 구성에서 선택해야 합니다. 

토큰의 클레임 값은 디렉터리의 값이 아니라 API에서 반환하는 값입니다. 일부 클레임 값은 API 응답으로 덮어쓸 수 없습니다. API에서 반환할 수 있는 클레임은 `email`을 제외하고 **사용자 특성** 아래에 있는 집합에 해당합니다.

> [!NOTE]
> API는 초기 인증 중에만 호출됩니다. 새로 고침 토큰을 사용하여 새 액세스 또는 ID 토큰을 자동으로 가져오는 경우 토큰에는 초기 인증 중에 평가된 값이 포함됩니다. 

## <a name="example-response"></a>예제 응답

### <a name="example-of-a-continuation-response"></a>연속 응답의 예

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| 매개 변수                                          | 형식              | 필수 | Description                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 버전     | String | 예      | API의 버전입니다.                                                    |
| action                                             | String            | 예      | 값은 `Continue`이어야 합니다.                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | 아니요       | **애플리케이션 클레임** 으로 선택된 경우 토큰에서 반환될 수 있습니다.                                        |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | No       | 클레임은 `_<extensions-app-id>_`를 포함할 필요가 없으며 *선택 사항* 입니다. **애플리케이션 클레임** 으로 선택된 경우 토큰에서 반환될 수 있습니다.  |

::: zone-end

::: zone pivot="b2c-custom-policy"

이 시나리오에서는 회사 LOB(기간 업무) 워크플로와 통합하여 사용자의 토큰 데이터를 보강합니다. 로컬 또는 페더레이션된 계정으로 가입 또는 로그인하는 동안 Azure AD B2C는 REST API를 호출하여 원격 데이터 원본에서 사용자의 확장된 프로필 데이터를 가져옵니다. 이 샘플에서는 Azure AD B2C는 사용자의 고유 식별자인 objectId를 보냅니다. 그런 다음, REST API는 사용자 계정의 잔액(난수)을 반환합니다. 이 샘플을 시작 지점으로 사용하여 자체 CRM 시스템, 마케팅 데이터베이스 또는 LOB(기간 업무) 워크플로와 통합할 수 있습니다.

또한 상호 작용은 유효성 기술 프로필로 설계할 수 있습니다. 이는 REST API가 화면에서 데이터의 유효성을 검사하고 클레임을 반환하는 경우에 적합합니다. 자세한 내용은 [연습: 등록 사용자 흐름에 API 커넥터 추가](add-api-connector.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [사용자 지정 정책 시작](tutorial-create-user-flows.md?pivots=b2c-custom-policy)의 단계를 완료합니다. 로컬 계정을 사용하여 등록 및 로그인하기 위한 사용자 지정 정책이 작동해야 합니다.
- [Azure AD B2C 사용자 지정 정책에서 REST API 클레임 교환을 통합](api-connectors-overview.md)하는 방법에 대해 알아봅니다.

## <a name="prepare-a-rest-api-endpoint"></a>REST API 엔드포인트 준비

이 연습에서는 사용자의 Azure AD B2C objectId가 백 엔드 시스템에 등록되었는지 여부를 확인하는 REST API가 있어야 합니다. 등록된 경우 REST API는 사용자 계정 잔액을 반환합니다. 그렇지 않으면 REST API는 디렉터리에 새 계정을 등록하고 시작 잔액 `50.00`을 반환합니다.

다음 JSON 코드는 Azure AD B2C가 REST API 엔드포인트로 전송하는 데이터를 보여줍니다. 

```json
{
    "objectId": "User objectId",
    "lang": "Current UI language"
}
```

REST API가 데이터의 유효성을 검사한 후에는 다음 JSON 데이터를 사용하여 HTTP 200(Ok)를 반환해야 합니다.

```json
{
    "balance": "760.50"
}
```

REST API 엔드포인트의 설정은 이 문서에서 다루지 않습니다. [Azure Functions](../azure-functions/functions-reference.md) 샘플은 링크를 통해 확인하세요. 전체 Azure 함수 코드는 [GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)에서 액세스할 수 있습니다.

## <a name="define-claims"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. [클레임 스키마](claimsschema.md) 섹션 내에서 클레임을 선언할 수 있습니다. 

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가합니다.  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="add-the-restful-api-technical-profile"></a>RESTful API 기술 프로필 추가 

[RESTful 기술 프로필](restful-technical-profile.md)은 자체 RESTful 서비스와의 상호 작용을 지원합니다. Azure AD B2C는 `InputClaims` 컬렉션에서 RESTful 서비스로 데이터를 보내고 `OutputClaims` 컬렉션에서 데이터를 다시 수신합니다. <em> **`TrustFrameworkExtensions.xml`**</em> 파일에서 **ClaimsProviders** 요소를 찾고 다음과 같이 새 클레임 공급자를 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- Set the ServiceUrl with your own REST API endpoint -->
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
``` 

이 예제에서 `userLanguage`는 JSON 페이로드 내에서 `lang`으로 REST 서비스에 전송됩니다. `userLanguage` 클레임의 값은 현재 사용자 언어 ID를 포함합니다. 자세한 내용은 [클레임 해결 프로그램](claim-resolver-overview.md)을 참조하세요.

### <a name="configure-the-restful-api-technical-profile"></a>RESTful API 기술 프로필 구성 

REST API를 배포한 후에는 다음을 포함하여 고유한 REST API를 반영하도록 `REST-GetProfile` 기술 프로필 메타데이터를 설정하세요.

- **ServiceUrl**. REST API 엔드포인트 URL을 설정합니다.
- **SendClaimsIn**. 입력 클레임이 RESTful 클레임 공급자로 전송되는 방법을 지정합니다.
- **AuthenticationType**. RESTful 클레임 공급자가 수행하는 인증 유형을 설정합니다. 
- **AllowInsecureAuthInProduction**. 프로덕션 환경에서 이 메타데이터가 `true`로 설정되었는지 확인합니다.
    
자세한 구성은 [RESTful 기술 프로필 메타데이터](restful-technical-profile.md#metadata)를 참조하세요.

위의 `AuthenticationType` 및 `AllowInsecureAuthInProduction` 설명은 프로덕션 환경으로 이동할 때 수행해야 하는 변경 내용을 지정합니다. 프로덕션을 위해 RESTful API를 보호하는 방법을 알아보려면 [RESTful API 보안](secure-rest-api.md)을 참조하세요.

## <a name="add-an-orchestration-step"></a>오케스트레이션 단계 추가

[사용자 경험](userjourneys.md)은 명시적 경로를 지정합니다. 이 경로를 통해 정책은 신뢰 당사자 애플리케이션이 사용자에 대해 원하는 클레임을 가져오도록 허용합니다. 사용자 경험은 트랜잭션을 정상적으로 수행하려면 따라야 하는 오케스트레이션 시퀀스로 표시됩니다. 오케스트레이션 단계를 추가하거나 뺄 수 있습니다. 이 경우 사용자가 REST API 호출을 통해 가입하거나 로그인한 후 애플리케이션에 제공된 정보를 보강하는 데 사용되는 새 오케스트레이션 단계를 추가합니다.

1. 정책의 기본 파일을 엽니다(예: 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkBase.xml`**</em>입니다.
1. `<UserJourneys>` 요소를 검색합니다. 전체 요소를 복사하고 삭제합니다.
1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. `<ClaimsProviders>` 요소를 닫은 후 확장 파일에 `<UserJourneys>`를 붙여넣습니다.
1. `<UserJourney Id="SignUpOrSignIn">`를 찾고 마지막 오케스트레이션 앞에 다음 오케스트레이션 단계를 추가합니다.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. `Order`를 `8`로 변경하여 마지막 오케스트레이션 단계를 리팩터링합니다. 최종 두 가지 오케스트레이션 단계는 다음과 같습니다.

    ```xml
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. **ProfileEdit** 및 **PasswordReset** 사용자 경험에 대해 마지막 두 단계를 반복합니다.


## <a name="include-a-claim-in-the-token"></a>토큰에 클레임 포함하기 

`balance` 클레임을 신뢰 당사자 애플리케이션에 다시 반환하려면 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 파일에 출력 클레임을 추가합니다. 출력 클레임을 추가하면 성공적인 사용자 경험 이후 토큰에 클레임이 발행되며 애플리케이션으로 전송됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정하여 `balance`를 출력 클레임으로 추가합니다.
 
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
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

**ProfileEdit.xml** 및 **PasswordReset.xml** 사용자 경험에 대해 이 단계를 반복합니다.

변경한 파일을 저장합니다. *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*. 

## <a name="test-the-custom-policy"></a>사용자 지정 정책 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택하여 Azure AD 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택한 다음, **앱 등록** 을 검색하여 선택합니다.
1. **ID 경험 프레임워크** 를 선택합니다.
1. **사용자 지정 정책 업로드** 를 선택한 후 변경한 정책 파일을 업로드합니다. *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*. 
1. 업로드한 등록 또는 로그인 정책을 선택하고 **지금 실행** 단추를 클릭합니다.
1. 이메일 주소나 Facebook 계정을 사용하여 등록할 수 있습니다.
1. 애플리케이션으로 다시 전송되는 토큰에는 `balance` 클레임이 포함됩니다.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```
::: zone-end

::: zone pivot="b2c-user-flow"

## <a name="best-practices-and-how-to-troubleshoot"></a>모범 사례 및 문제 해결 방법

### <a name="using-serverless-cloud-functions"></a>서버리스 클라우드 함수 사용

[Azure Functions의 HTTP 트리거](../azure-functions/functions-bindings-http-webhook-trigger.md)와 같은 서버리스 함수는 API 커넥터에서 사용할 API 엔드포인트를 만드는 방법을 제공합니다. 서버리스 클라우드 함수는 보다 복잡한 시나리오에 대해 다른 웹 API, 데이터 저장소 및 기타 클라우드 서비스를 호출할 수도 있습니다.

### <a name="best-practices"></a>모범 사례

다음 사항을 확인합니다.
* API는 위에 설명된 대로 API 요청 및 응답 계약을 따릅니다. 
* API 커넥터의 **엔드포인트 URL** 은 올바른 API 엔드포인트를 가리킵니다.
* API는 의존하는 수신된 클레임의 null 값을 명시적으로 확인합니다.
* API는 [API 커넥터 보호](secure-rest-api.md)에 설명된 인증 방법을 구현합니다.
* API는 유연하게 사용자 환경을 보장하기 위해 최대한 신속하게 응답합니다.
    * Azure AD B2C 응답을 받을 때까지 최대 *20초* 동안 기다립니다. 아무 것도 수신되지 않으면 API를 호출할 때 *한번 더 시도(재시도)* 합니다.
    * 서버리스 함수 또는 확장성 있는 웹 서비스를 사용하는 경우 프로덕션 환경에서 API를 "활성" 또는 "웜" 상태로 유지하는 호스팅 계획을 사용합니다. Azure Functions의 경우 프로덕션에서 최소한 [프리미엄 플랜](../azure-functions/functions-scale.md)을 사용하는 것이 좋습니다.
* API의 고가용성을 보장합니다.
* 다운스트림 API, 데이터베이스 또는 기타 API 종속성의 성능을 모니터링하고 최적화합니다.
  
[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

### <a name="use-logging"></a>로깅 사용

일반적으로 [Application Insights](../azure-functions/functions-monitoring.md)와 같은 웹 API 서비스에서 사용하도록 설정된 로깅 도구를 사용하여 API에서 예기치 않은 오류 코드, 예외 및 성능 저하 문제를 모니터링하는 것이 유용합니다.
* HTTP 200 또는 400이 아닌 HTTP 상태 코드를 모니터링합니다.
* 401 또는 403 HTTP 상태 코드는 일반적으로 인증에 문제가 있음을 나타냅니다. API 커넥터에서 API의 인증 계층 및 해당 구성을 한 번 더 확인합니다.
* 필요한 경우 개발에서 좀 더 적극적인 로깅 수준(예: "추적" 또는 "디버그")을 사용합니다.
* API에서 긴 응답 시간이 나타나는지 모니터링합니다. 

또한 Azure AD B2C는 사용자 흐름을 통해 사용자 인증 중에 발생하는 API 트랜잭션에 대한 메타데이터를 기록합니다. 이러한 데이터를 찾으려면 다음을 수행합니다.
1. **Azure AD B2C** 로 이동합니다.
2. **활동** 아래에서 **감사 로그** 를 선택합니다.
3. 목록 보기를 필터링합니다. **날짜** 에 대해 원하는 시간 간격을 선택하고 **작업** 에 대해 **사용자 흐름의 일부로 API가 호출됨** 을 선택합니다.
4. 개별 로그를 검사합니다. 각 행은 사용자 흐름 중에 호출하려는 API 커넥터를 나타냅니다. API 호출이 실패하고 다시 시도하면 여전히 단일 행으로 표시됩니다. `numberOfAttempts`는 API가 호출된 횟수를 나타냅니다. 이 값은 `1` 또는 `2`일 수 있습니다. API 호출에 대한 기타 정보는 로그에 자세히 설명되어 있습니다.

   ![API 커넥터 트랜잭션이 있는 예제 감사 로그의 스크린샷](media/add-api-connector-token-enrichment/example-anonymized-audit-log.png)

::: zone-end

## <a name="next-steps"></a>다음 단계

::: zone pivot="b2c-user-flow"

- [샘플](api-connector-samples.md#api-connector-rest-api-samples)을 시작하세요.
- [API 커넥터 보호](secure-rest-api.md)

::: zone-end

::: zone pivot="b2c-custom-policy"

API를 보호하는 방법을 알아보려면 다음 문서를 참조하세요.

- [연습: Azure AD B2C 사용자 경험에서 REST API 클레임 교환을 오케스트레이션 단계로 통합](add-api-connector-token-enrichment.md)
- [RESTful API 보안](secure-rest-api.md)
- [참조: RESTful 기술 프로필](restful-technical-profile.md)

::: zone-end
