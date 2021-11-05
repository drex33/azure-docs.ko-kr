---
title: 사용자 지정 정책에서 ID 토큰 힌트 기술 프로필 정의
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 사용자 지정 정책에서 ID 토큰 힌트 기술 프로필을 정의합니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/16/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 8868c506ab80a2ece97882e080caa5abb5642c24
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131007261"
---
# <a name="define-an-id-token-hint-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>Azure Active Directory B2C 사용자 지정 정책에서 ID 토큰 힌트 기술 프로필 정의

Azure AD B2C를 사용하면 신뢰 당사자 애플리케이션이 OAuth2 권한 부여 요청의 일부로 인바운드 JWT를 보낼 수 있습니다. JWT 토큰은 신뢰 당사자 애플리케이션 또는 ID 공급자가 발급할 수 있으며 사용자 또는 권한 부여 요청에 대한 힌트를 전달할 수 있습니다. Azure AD B2C는 서명, 발급자 이름 및 토큰 대상의 유효성을 검사하고 인바운드 토큰에서 클레임을 추출합니다.

## <a name="use-cases"></a>사용 사례

이 솔루션을 사용하여 단일 JWT 토큰으로 캡슐화된 Azure AD B2C에 데이터를 보낼 수 있습니다. 시스템 관리자가 서명된 초대를 사용자에게 보낼 수 있는 [`Signup with email invitation` 솔루션](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md)은 id_token_hint를 기반으로 합니다. 초대 이메일에 대한 액세스 권한이 있는 사용자만 디렉터리에 계정을 만들 수 있습니다.

## <a name="token-signing-approach"></a>토큰 서명 방법

Id_token_hint를 사용하여 토큰 발급자(신뢰 당사자 앱 또는 ID 공급자)는 토큰을 작성한 다음 서명 키를 사용하여 토큰을 서명하고 신뢰할 수 있는 원본에서 오는 토큰을 증명합니다. 서명 키는 대칭 또는 비대칭일 수 있습니다. 대칭 암호화 또는 프라이빗 키 암호화는 공유된 비밀을 사용하여 서명하고 서명의 유효성을 검사합니다. 비대칭 암호화 또는 공개 키 암호화는 프라이빗 키와 공개 키를 모두 사용하는 암호화 시스템입니다. 프라이빗 키는 토큰 발급자에만 알려져 있으며 토큰에 서명하는 데 사용됩니다. 공개 키는 토큰 서명의 유효성을 검사하는 Azure AD B2C 정책과 공유됩니다.

## <a name="token-format"></a>토큰 형식

Id_token_hint는 유효한 JWT 토큰이어야 합니다. 다음 표에는 필수 클레임이 나열되어 있습니다. 추가 클레임은 선택 사항입니다.

| 속성 | 클레임 | 예제 값 | Description |
| ---- | ----- | ------------- | ----------- |
| 사용자 | `aud` | `a489fc44-3cc0-4a78-92f6-e413cd853eae` | 토큰의 의도한 수신자를 식별합니다. 대상은 토큰 발급자가 정의한 임의의 문자열입니다. Azure AD B2C는 이 값의 유효성을 검사하고 토큰이 일치하지 않을 경우 토큰을 거부합니다.  |
| 발급자 | `iss` |`https://localhost` | 보안 토큰 서비스(토큰 발급자)를 식별합니다. 발급자는 토큰 발급자가 정의하는 임의의 URI입니다. Azure AD B2C는 이 값의 유효성을 검사하고 토큰이 일치하지 않을 경우 토큰을 거부합니다.  |
| 만료 시간 | `exp` | `1600087315` | epoch 시간으로 표시된, 토큰이 무효화되는 시간입니다. Azure AD B2C는 이 값의 유효성을 검사하고 토큰이 만료된 경우 토큰을 거부합니다.|
| 이전이 아님 | `nbf` | `1599482515` | epoch 시간으로 표시된, 토큰이 유효화되는 시간입니다. 일반적으로 토큰이 발급된 시간과 같습니다. Azure AD B2C는 이 값의 유효성을 검사하고 토큰 수명이 유효하지 않은 경우 토큰을 거부합니다. |

다음 토큰은 유효한 ID 토큰의 예입니다.

```json
{
  "alg": "HS256",
  "typ": "JWT"
}.{
  "displayName": " John Smith",
  "userId": "john.s@contoso.com",
  "nbf": 1599482515,
  "exp": 1600087315,
  "iss": "https://localhost",
  "aud": "a489fc44-3cc0-4a78-92f6-e413cd853eae"
}
```

## <a name="protocol"></a>프로토콜

**Protocol** 요소의 **Name** 특성은 `None`로 설정해야 합니다. 예를 들어 **IdTokenHint_ExtractClaims** 기술 프로필의 프로토콜은 `None`입니다.

```xml
<TechnicalProfile Id="IdTokenHint_ExtractClaims">
  <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
  <Protocol Name="None" />
  ...
```

기술 프로필은 유형이 `GetClaims`인 오케스트레이션 단계에서 호출됩니다.

```xml
<OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
``` 

## <a name="output-claims"></a>출력 클레임

**Outputclaims** 요소는 JWT 토큰에서 추출할 클레임 목록을 포함합니다. 정책에 정의된 클레임 이름을 JWT 토큰에서 정의된 이름에 매핑해야 할 수도 있습니다. `DefaultValue` 특성만 설정하면, JWT 토큰에서 반환하지 않은 클레임도 포함할 수 있습니다.

## <a name="metadata"></a>메타데이터

다음 메타데이터는 대칭 키를 사용할 때 관련됩니다. 

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| 발급자 | 예 | 보안 토큰 서비스(토큰 발급자)를 식별합니다. 이 값은 JWT 토큰 클레임 내의 `iss` 클레임과 동일해야 합니다. | 
| IdTokenAudience | 예 | 토큰의 의도한 수신자를 식별합니다. JWT 토큰 클레임 내의 `aud` 클레임과 동일해야 합니다. | 

다음 메타데이터는 비대칭 키를 사용할 때 관련됩니다. 

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| METADATA| 예 | OpenID의 잘 알려진 구성 엔드포인트라고도 하는 토큰 발급자 구성 문서를 가리키는 URL입니다.   |
| 발급자 | No | 보안 토큰 서비스(토큰 발급자)를 식별합니다. 이 값은 메타데이터에 구성된 값을 덮어쓰는 데 사용할 수 있으며 `iss` JWT 토큰 클레임 내의 클레임과 동일해야 합니다. |  
| IdTokenAudience | No | 토큰의 의도한 수신자를 식별합니다. JWT 토큰 클레임 내의 `aud` 클레임과 동일해야 합니다. |  

[!INCLUDE [active-directory-b2c-https-cipher-tls-requirements](../../includes/active-directory-b2c-https-cipher-tls-requirements.md)]

## <a name="cryptographic-keys"></a>암호화 키

대칭 키를 사용하는 경우 **CryptographicKeys** 요소에는 다음 특성이 포함됩니다.

| attribute | 필수 | Description |
| --------- | -------- | ----------- |
| client_secret | 예 | JWT 토큰 서명의 유효성을 검사하는 데 사용되는 암호화 키입니다.|

## <a name="how-to-guide"></a>방법 가이드

### <a name="issue-a-token-with-symmetric-keys"></a>대칭 키가 있는 토큰 발급

#### <a name="step-1-create-a-shared-key"></a>1단계. 공유 키를 만들기 

토큰에 서명하는 데 사용할 수 있는 키를 만듭니다. 예를 들어 다음 PowerShell 코드를 사용하 여 키를 생성합니다.

```powershell
$bytes = New-Object Byte[] 32
$rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
$rand.GetBytes($bytes)
$rand.Dispose()
$newClientSecret = [System.Convert]::ToBase64String($bytes)
$newClientSecret
```

이 코드는 `VK62QTn0m1hMcn0DQ3RPYDAr6yIiSvYgdRwjZtU5QhI=`와 같은 비밀 문자열을 만듭니다.

#### <a name="step-2-add-the-signing-key-to-azure-ad-b2c"></a>2단계. Azure AD B2C에 서명 키 추가

토큰 발급자에 사용되는 것과 동일한 키를 Azure AD B2C 정책 키에 만들어야 합니다.  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. 개요 페이지의 **정책** 에서 **Identity Experience Framework** 를 선택합니다.
1. **정책 키** 선택 
1. **수동** 을 선택합니다.
1. **이름** 에는 `IdTokenHintKey`를 사용합니다.  
   `B2C_1A_` 접두사가 자동으로 추가될 수 있습니다.
1. **비밀** 상자에 이전에 생성한 로그인 키를 입력합니다.
1. **키 사용** 에는 **암호화** 를 사용합니다.
1. **만들기** 를 선택합니다.
1. `B2C_1A_IdTokenHintKey` 키를 만들었는지 확인합니다.


#### <a name="step-3-add-the-id-token-hint-technical-profile"></a>3단계: ID 토큰 힌트 기술 프로필 추가

다음 기술 프로필은 토큰의 유효성을 검사하고 클레임을 추출합니다. 

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <Item Key="IdTokenAudience">a489fc44-3cc0-4a78-92f6-e413cd853eae</Item>
        <Item Key="issuer">https://localhost</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="client_secret" StorageReferenceId="B2C_1A_IdTokenHintKey" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-4-prepare-your-policy"></a>4단계. 정책 준비

[정책 구성](#configure-your-policy) 단계를 완료합니다.

#### <a name="step-5-prepare-the-code"></a>5단계. 코드 준비  

[GitHub 샘플](https://github.com/azure-ad-b2c/id_token_hint/tree/master/dotnet_core_symmetric_key)은 대칭 키를 사용하여 서명된 ID 토큰을 생성하는 ASP.NET 웹 애플리케이션 및 콘솔 앱입니다. 


### <a name="issue-a-token-with-asymmetric-keys"></a>비대칭 키가 있는 토큰 발급

비대칭 키를 사용하는 경우 토큰은 RSA 인증서를 사용하여 서명합니다. 이 애플리케이션은 Azure AD B2C에서 ID 토큰 서명의 유효성을 검사하는 데 사용되는 Open ID Connect 메타데이터 엔드포인트와 JWK(JSON 웹 키) 엔드포인트를 호스팅합니다.

토큰 발급자는 다음 엔드포인트를 제공해야 합니다.

* `/.well-known/openid-configuration` -토큰 발급자 이름 및 JWK 엔드포인트에 대한 링크와 같이 토큰에 대한 관련 정보가 포함된 잘 알려진 구성 엔드포인트입니다. 
* `/.well-known/keys` -키(인증서의 프라이빗 키 부분 포함)에 서명하는 데 사용되는 공개 키가 있는 JWK(JSON 웹 키) 엔드포인트입니다.

[`TokenMetadataController.cs`](https://github.com/azure-ad-b2c/id-token-builder/blob/master/source-code/B2CIdTokenBuilder/Controllers/TokenMetadataController.cs) .NET MVC 컨트롤러 샘플을 참조하세요.

#### <a name="step-1-prepare-a-self-signed-certificate"></a>1단계. 자체 서명된 인증서 준비

인증서가 아직 없는 경우 이 방법 가이드에서는 자체 서명된 인증서를 사용할 수 있습니다. Windows에서는 PowerShell의 [New-SelfSignedCertificate](/powershell/module/pki/new-selfsignedcertificate) cmdlet을 사용하여 인증서를 생성할 수 있습니다.

이 PowerShell 명령을 실행하여 자체 서명된 인증서를 실행합니다. `-Subject` 인수를 애플리케이션 및 Azure AD B2C 테넌트 이름에 적절하게 수정합니다. `-NotAfter` 날짜를 조정하여 인증서에 다른 만료 날짜를 지정할 수도 있습니다.

```powershell
New-SelfSignedCertificate `
    -KeyExportPolicy Exportable `
    -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
    -KeyAlgorithm RSA `
    -KeyLength 2048 `
    -KeyUsage DigitalSignature `
    -NotAfter (Get-Date).AddMonths(12) `
    -CertStoreLocation "Cert:\CurrentUser\My"
```


#### <a name="step-2-add-the-id-token-hint-technical-profile"></a>2단계. ID 토큰 힌트 기술 프로필 추가 

다음 기술 프로필은 토큰의 유효성을 검사하고 클레임을 추출합니다. 메타데이터 URI를 토큰 발급자의 잘 알려진 구성 엔드포인트로 변경합니다.

```xml
<ClaimsProvider>
  <DisplayName>My ID Token Hint ClaimsProvider</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="IdTokenHint_ExtractClaims">
      <DisplayName> My ID Token Hint TechnicalProfile</DisplayName>
      <Protocol Name="None" />
      <Metadata>
        <!-- Replace with your endpoint location -->
        <Item Key="METADATA">https://your-app.azurewebsites.net/.well-known/openid-configuration</Item>
        <Item Key="IdTokenAudience">your_optional_audience</Item>
        <!-- <Item Key="issuer">your_optional_token_issuer_override</Item> -->
      </Metadata>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

#### <a name="step-3-prepare-your-policy"></a>3단계: 정책 준비

[정책 구성](#configure-your-policy) 단계를 완료합니다.

#### <a name="step-4-prepare-the-code"></a>4단계. 코드 준비 

이 [GitHub 샘플](https://github.com/azure-ad-b2c/id-token-builder) ASP.NET 웹 애플리케이션은 ID 토큰을 생성하고 Azure AD B2C에서 ‘id_token_hint’ 매개 변수를 사용하는 데 필요한 메타데이터 엔드포인트를 호스팅합니다.


### <a name="configure-your-policy"></a>정책 구성

대칭 및 비대칭 방식 모두에서 `id_token_hint` 기술 프로필은 `GetClaims`의 유형으로 오케스트레이션 단계에서 호출되며 신뢰 당사자 정책의 입력 클레임을 지정해야 합니다.

1. IdTokenHint_ExtractClaims 기술 프로필을 확장 정책에 추가합니다.
1. 다음 오케스트레이션 단계를 사용자 경험에 첫 번째 항목으로 추가합니다.  

    ```xml
    <OrchestrationStep Order="1" Type="GetClaims" CpimIssuerTechnicalProfileReferenceId="IdTokenHint_ExtractClaims" />
    ``` 
1. 신뢰 당사자 정책에서 IdTokenHint_ExtractClaims 기술 프로필에서 구성한 것과 동일한 입력 클레임을 반복합니다. 예를 들면 다음과 같습니다.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUp" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <InputClaims>
          <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="userId" />
        </InputClaims>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="identityProvider" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

비즈니스 요구 사항에 따라 토큰 유효성 검사를 추가해야 할 수도 있습니다. 예를 들어 이메일 주소의 형식을 확인합니다. 이렇게 하려면 [클레임 변환 기술 프로필](claims-transformation-technical-profile.md)을 호출하는 오케스트레이션 단계를 추가합니다. 또한 [자체 어설션된 기술 프로필](self-asserted-technical-profile.md)을 추가하여 오류 메시지를 표시합니다. 

### <a name="create-and-sign-a-token"></a>토큰 만들기 및 서명

GitHub 샘플은 나중에 `id_token_hint` 쿼리 문자열 매개 변수로 전송되는 JWT에 이러한 토큰 발급을 생성하는 방법을 보여줍니다. 다음은 id_token_hint 매개 변수를 사용하는 권한 부여 요청의 예제입니다.
 
```html
https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin/oauth2/v2.0/authorize?client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login&id_token_hint=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJkaXNwbGF5TmFtZSI6IiBKb2huIFNtaXRoIiwidXNlcklkIjoiam9obi5zQGNvbnRvc28uY29tIiwibmJmIjoxNTk5NDgyNTE1LCJleHAiOjE2MDAwODczMTUsImlzcyI6Imh0dHBzOi8vbG9jYWxob3N0IiwiYXVkIjoiYTQ4OWZjNDQtM2NjMC00YTc4LTkyZjYtZTQxM2NkODUzZWFlIn0.nPmLXydI83PQCk5lRBYUZRu_aX58pL1khahHyQuupig
```

## <a name="next-steps"></a>다음 단계

- Azure AD B2C community GitHub 리포지토리에서 [초대 이메일을 사용하여 등록](https://github.com/azure-ad-b2c/samples/blob/master/policies/invite/README.md) 솔루션을 선택합니다.
