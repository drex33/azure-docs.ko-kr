---
title: Azure AD B2C에서 Restful 서비스 보호
titleSuffix: Azure AD B2C
description: Azure AD B2C에서 사용자 지정 REST API 클레임 교환을 보호합니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/28/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 55034efe35ae572fb7b2d5d8eeacb6048bcb8e51
ms.sourcegitcommit: 516eb79d62b8dbb2c324dff2048d01ea50715aa1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2021
ms.locfileid: "108175445"
---
# <a name="secure-your-api-connector"></a>API 커넥터 보호 


Azure AD B2C 사용자 흐름 내에서 REST API를 통합하는 경우 인증을 사용하여 REST API 엔드포인트를 보호해야 합니다. REST API 인증을 사용하면 Azure AD B2C와 같이 적절한 자격 증명이 있는 서비스만 엔드포인트에 대한 호출을 수행할 수 있습니다. 이 문서에서는 REST API를 보호하는 방법을 살펴봅니다. 

## <a name="prerequisites"></a>사전 요구 사항

[연습: 가입 사용자 흐름에 API 커넥터 추가](add-api-connector.md) 가이드의 단계를 완료합니다.

## <a name="http-basic-authentication"></a>HTTP 기본 인증

HTTP 기본 인증은 [RFC 2617](https://tools.ietf.org/html/rfc2617)에 정의되어 있습니다. 기본 인증은 다음과 같이 작동합니다. Azure AD B2C는 인증 헤더에서 클라이언트 자격 증명을 사용하여 HTTP 요청을 보냅니다. 자격 증명은 base64로 인코딩된 문자열 "name:password"로 형식이 지정됩니다.  

::: zone pivot="b2c-user-flow"

HTTP 기본 인증을 사용하여 API 커넥터를 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다.
1. **API 커넥터(미리 보기)** 를 선택한 다음, 구성하려는 **API 커넥터** 를 선택합니다.
1. **인증 형식** 으로 **기본** 을 선택합니다.
1. REST API 엔드포인트의 **사용자 이름** 및 **암호** 를 제공합니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-rest-api-username-and-password-policy-keys"></a>REST API 사용자 이름 및 암호 정책 키 추가

HTTP 기본 인증을 사용하여 REST API 기술 프로필을 구성하려면 다음 암호화 키를 만들어 사용자 이름 및 암호를 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 에서 **수동** 을 선택합니다.
1. **이름** 에 **RestApiUsername** 을 입력합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 사용자 이름을 입력합니다.
1. **키 사용** 에는 **암호화** 를 선택합니다.
1. **만들기** 를 선택합니다.
1. **정책 키** 를 다시 선택합니다.
1. **추가** 를 선택합니다.
1. **옵션** 에서 **수동** 을 선택합니다.
1. **이름** 에 **RestApiPassword** 를 입력합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 암호를 입력합니다.
1. **키 사용** 에는 **암호화** 를 선택합니다.
1. **만들기** 를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-http-basic-authentication"></a>HTTP 기본 인증을 사용하도록 REST API 기술 프로필 구성

필요한 키를 만든 후에 REST API 기술 프로필 메타데이터를 구성하여 자격 증명을 참조합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예: `REST-ValidateProfile` 또는 `REST-GetProfile`
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을 `Basic`로 변경합니다.
1. *AllowInsecureAuthInProduction* 을 `false`로 변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
    </CryptographicKeys>
    ```

다음 XML 코드 조각은 HTTP 기본 인증을 사용하여 구성된 RESTful 기술 프로필의 예제입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Basic</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_RestApiUsername" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_RestApiPassword" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```
::: zone-end

## <a name="https-client-certificate-authentication"></a>HTTPS 클라이언트 인증서 인증

클라이언트 인증서 인증은 클라이언트, Azure AD B2C에서 해당 ID를 증명하기 위해 해당 클라이언트 인증서를 서버에 제공하는 상호 인증서 기반 인증입니다. 이는 SSL 핸드셰이크의 일부로 발생합니다. Azure AD B2C와 같은 적절한 인증서를 가진 서비스만이 REST API 서비스에 액세스할 수 있습니다. 클라이언트 인증서는 X.509 디지털 인증서입니다. 프로덕션 환경에서는 인증 기관에서 서명해야 합니다.

### <a name="prepare-a-self-signed-certificate-optional"></a>자체 서명된 인증서 준비(선택 사항)

[!INCLUDE [active-directory-b2c-create-self-signed-certificate](../../includes/active-directory-b2c-create-self-signed-certificate.md)]

::: zone pivot="b2c-user-flow"

### <a name="configure-your-api-connector"></a>API 커넥터 구성

클라이언트 인증서 인증을 사용하여 API 커넥터를 구성하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다.
1. **API 커넥터(미리 보기)** 를 선택한 다음, 구성하려는 **API 커넥터** 를 선택합니다.
1. **인증 형식** 으로 **인증서** 를 선택합니다.
1. **인증서 업로드** 상자에서 프라이빗 키가 있는 인증서의 .pfx 파일을 선택합니다.
1. **암호 입력** 상자에 인증서의 암호를 입력합니다.
1. **저장** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="add-a-client-certificate-policy-key"></a>클라이언트 인증서 정책 키 추가

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 상자에서 **업로드** 를 선택합니다.
1. **이름** 상자에 **RestApiClientCertificate** 를 입력합니다.
    *B2C_1A_* 접두사가 자동으로 추가됩니다.
1. **파일 업로드** 상자에서 프라이빗 키가 있는 인증서 .pfx 파일을 선택합니다.
1. **암호** 상자에 인증서의 암호를 입력합니다.
1. **만들기** 를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-client-certificate-authentication"></a>클라이언트 인증서 인증을 사용하도록 REST API 기술 프로필 구성

필요한 키를 만든 후에 REST API 기술 프로필 메타데이터를 구성하여 클라이언트 인증서를 참조합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예: `REST-ValidateProfile` 또는 `REST-GetProfile`
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을 `ClientCertificate`로 변경합니다.
1. *AllowInsecureAuthInProduction* 을 `false`로 변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
       <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
    </CryptographicKeys>
    ```

다음 XML 코드 조각은 HTTP 클라이언트 인증서를 사용하여 구성된 RESTful 기술 프로필의 예제입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ClientCertificate</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_RestApiClientCertificate" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="oauth2-bearer-authentication"></a>OAuth2 전달자 인증 

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

전달자 토큰 인증은 [OAuth 2.0 권한 부여 프레임워크: 전달자 토큰 사용(RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt)에 정의되어 있습니다. 전달자 토큰 인증에서 Azure AD B2C는 인증 헤더에서 토큰을 사용하여 HTTP 요청을 보냅니다.

```http
Authorization: Bearer <token>
```

전달자 토큰은 불투명 문자열입니다. 이 토큰은 JWT 액세스 토큰이거나 REST API에서 Azure AD B2C가 인증 헤더에서 보낼 것으로 예상하는 문자열일 수 있습니다. Azure AD B2C는 다음과 같은 유형을 지원합니다.

- **전달자 토큰** Restful 기술 프로필에서 전달자 토큰을 보낼 수 있으려면 먼저 정책에서 전달자 토큰을 획득한 다음, RESTful 기술 프로필에서 사용해야 합니다.  
- **정적 전달자 토큰** REST API에서 장기 액세스 토큰을 발급하는 경우 이 방법을 사용합니다. 정적 전달자 토큰을 사용하려면 정책 키를 만들고 RESTful 기술 프로필에서 정책 키로 참조를 만듭니다. 


## <a name="using-oauth2-bearer"></a>OAuth2 전달자 사용  

다음 단계에서는 클라이언트 자격 증명을 사용하여 전달자 토큰을 가져와서 REST API 호출의 인증 헤더에 전달하는 방법을 보여 줍니다.  

### <a name="define-a-claim-to-store-the-bearer-token"></a>전달자 토큰을 저장할 클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. [클레임 스키마](claimsschema.md)에서 클레임을 선언합니다. 액세스 토큰은 나중에 사용하기 위해 클레임에 저장되어야 합니다. 

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 다음 클레임을 추가합니다.  

```xml
<ClaimType Id="bearerToken">
  <DisplayName>Bearer token</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="grant_type">
  <DisplayName>Grant type</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="scope">
  <DisplayName>scope</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

### <a name="acquiring-an-access-token"></a>액세스 토큰 획득 

[페더레이션 ID 공급자에서](idp-pass-through-user-flow.md) 가져오거나 액세스 토큰을 반환하는 REST API를 호출하거나 [ROPC 흐름](../active-directory/develop/v2-oauth-ropc.md)을 사용하거나 [클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)을 사용하여 액세스 토큰을 가져올 수 있습니다. 클라이언트 자격 증명 흐름은 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다.

#### <a name="acquiring-an-azure-ad-access-token"></a>Azure AD 액세스 토큰 획득 

다음 예제에서는 REST API 기술 프로필을 사용하여 HTTP 기본 인증으로 전달된 클라이언트 자격 증명을 사용하여 Azure AD 토큰 엔드포인트에 대한 요청을 만듭니다. 자세한 내용은 [Microsoft ID 플랫폼 및 OAuth 2.0 클라이언트 자격 증명 흐름](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)을 참조하세요. 

기술 프로필이 Azure AD와 상호 작용하여 액세스 토큰을 가져오려면 애플리케이션을 등록해야 합니다. Azure AD B2C는 Azure AD 플랫폼을 사용합니다. Azure AD B2C 테넌트 또는 관리하는 모든 Azure AD 테넌트에서 앱을 만들 수 있습니다. 애플리케이션을 등록하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure AD 또는 Azure AD B2C 테넌트가 포함된 디렉터리를 선택합니다.
1. 왼쪽 메뉴에서 **Azure Active Directory** 를 선택합니다. 또는 **모든 서비스** 를 선택하고 **Azure Active Directory** 를 검색하여 선택합니다.
1. **앱 등록** 을 선택한 다음, **새 등록** 을 선택합니다.
1. 애플리케이션의 **이름** 을 입력합니다. 예를 들어 *Client_Credentials_Auth_app* 을 입력합니다.
1. **지원되는 계정 유형** 에서 **이 조직 디렉터리의 계정만** 을 선택합니다.
1. **등록** 을 선택합니다.
2. **애플리케이션(클라이언트) ID** 를 기록해 둡니다. 


클라이언트 자격 증명 흐름의 경우 애플리케이션 비밀을 만들어야 합니다. 클라이언트 암호는 애플리케이션 암호라고도 합니다. 이 비밀은 애플리케이션에서 액세스 토큰을 획득하는 데 사용됩니다.

1. **Azure AD - 앱 등록** 페이지에서 만든 애플리케이션을 선택합니다(예: *Client_Credentials_Auth_app*).
1. 왼쪽 메뉴의 **관리** 에서 **인증서 및 비밀** 을 선택합니다.
1. **새 클라이언트 비밀** 을 선택합니다.
1. **설명** 상자에 클라이언트 암호에 대한 설명을 입력합니다. 예: *clientsecret1*.
1. **만료** 에서 암호가 유효한 기간을 선택한 다음 **추가** 를 선택합니다.
1. 클라이언트 애플리케이션 코드에서 사용할 비밀 **값** 을 기록합니다. 이 비밀 값은 이 페이지에서 나가면 다시 표시되지 않습니다. 이 값을 애플리케이션의 코드에서 애플리케이션 비밀로 사용합니다.

#### <a name="create-azure-ad-b2c-policy-keys"></a>Azure AD B2C 정책 키 만들기

이전에 Azure AD B2C 테넌트에 기록한 클라이언트 ID와 클라이언트 암호를 저장해야 합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 최상위 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 테넌트가 포함된 디렉터리를 선택합니다.
3. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
4. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
5. **정책 키**, **추가** 를 차례로 선택합니다.
6. **옵션** 으로는 `Manual`을 선택합니다.
7. 정책 키 `SecureRESTClientId`의 **이름** 을 입력합니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
8. 이전에 기록한 클라이언트 ID를 **비밀** 에 입력합니다.
9. **키 사용** 에서 `Signature`를 선택합니다.
10. **만들기** 를 선택합니다.
11. 다음 설정을 사용하여 다른 정책 키를 만듭니다.
    -   **이름**: `SecureRESTClientSecret`.
    -   **비밀**: 이전에 기록한 클라이언트 암호를 입력합니다.

ServiceUrl의 경우 테넌트 이름을 Azure AD 테넌트 이름으로 바꿉니다. 사용 가능한 모든 옵션은 [RESTful 기술 프로필](restful-technical-profile.md) 참조를 참조하세요.

```xml
<TechnicalProfile Id="REST-AcquireAccessToken">
  <DisplayName></DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://login.microsoftonline.com/your-tenant-name.onmicrosoft.com/oauth2/v2.0/token</Item>
    <Item Key="AuthenticationType">Basic</Item>
     <Item Key="SendClaimsIn">Form</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_SecureRESTClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_SecureRESTClientSecret" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="client_credentials" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="https://graph.microsoft.com/.default" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="bearerToken" PartnerClaimType="access_token" />
  </OutputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

### <a name="change-the-rest-technical-profile-to-use-bearer-token-authentication"></a>전달자 토큰 인증을 사용하도록 REST 기술 프로필 변경

사용자 지정 정책에서 전달자 토큰 인증을 지원하려면 REST API 기술 프로필을 다음과 같이 수정합니다.

1. 작업 디렉터리에서 *TrustFrameworkExtensions.xml* 확장 정책 파일을 엽니다.
1. `Id="REST-API-SignUp"`이 포함된 `<TechnicalProfile>` 노드를 검색합니다.
1. `<Metadata>` 요소를 찾습니다.
1. 다음과 같이 *AuthenticationType* 을 *Bearer* 로 변경합니다.
    ```xml
    <Item Key="AuthenticationType">Bearer</Item>
    ```
1. 다음과 같이 *UseClaimAsBearerToken* 을 *bearerToken* 으로 변경하거나 추가합니다. *bearerToken* 은 전달자 토큰이 검색되는 클레임의 이름입니다(`REST-AcquireAccessToken`에서 출력 클레임).

    ```xml
    <Item Key="UseClaimAsBearerToken">bearerToken</Item>
    ```
    
1. 위에서 사용된 클레임을 입력 클레임으로 추가해야 합니다.

    ```xml
    <InputClaim ClaimTypeReferenceId="bearerToken"/>
    ```    

위의 코드 조각을 추가한 후에 기술 프로필은 다음 XML 코드와 비슷하게 표시됩니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="UseClaimAsBearerToken">bearerToken</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="bearerToken"/>
      </InputClaims>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="using-a-static-oauth2-bearer"></a>정적 OAuth2 전달자 사용 

### <a name="add-the-oauth2-bearer-token-policy-key"></a>OAuth2 전달자 토큰 정책 키 추가

OAuth2 전달자 토큰으로 REST API 기술 프로필을 구성하려면 REST API 소유자로부터 액세스 토큰을 받습니다. 그리고 다음 암호화 키를 만들어 전달자 토큰을 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `RestApiBearerToken`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Encryption`를 선택합니다.
1. **만들기** 를 선택합니다.

### <a name="configure-your-rest-api-technical-profile-to-use-the-bearer-token-policy-key"></a>전달자 토큰 정책 키를 사용하도록 REST API 기술 프로필 구성

필요한 키를 만든 후에 REST API 기술 프로필 메타데이터를 구성하여 전달자 토큰을 참조합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예: `REST-ValidateProfile` 또는 `REST-GetProfile`
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을 `Bearer`로 변경합니다.
1. *AllowInsecureAuthInProduction* 을 `false`로 변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
       <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
    </CryptographicKeys>
    ```

다음 XML 코드 조각은 전달자 토큰 인증을 사용하여 구성된 RESTful 기술 프로필의 예제입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">Bearer</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_RestApiBearerToken" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="api-key-authentication"></a>API 키 인증

API 키는 REST API 엔드포인트 액세스를 위해 사용자를 인증하는 데 사용되는 고유 식별자입니다. 키는 사용자 지정 HTTP 헤더로 전송됩니다. 예를 들어 [Azure Functions HTTP 트리거](../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)는 `x-functions-key` HTTP 헤더를 사용하여 요청자를 식별합니다.  

### <a name="add-api-key-policy-keys"></a>API 키 정책 키 추가

API 키 인증을 사용하여 REST API 기술 프로필을 구성하려면 다음 암호화 키를 만들어 API 키를 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택하고 Azure AD B2C 디렉터리를 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 에서 **수동** 을 선택합니다.
1. **이름** 에 **RestApiKey** 를 입력합니다.
    *B2C_1A_* 접두사를 자동으로 추가할 수 있습니다.
1. **비밀** 상자에 REST API 키를 입력합니다.
1. **키 사용** 에는 **암호화** 를 선택합니다.
1. **만들기** 를 선택합니다.


### <a name="configure-your-rest-api-technical-profile-to-use-api-key-authentication"></a>API 키 인증을 사용하도록 REST API 기술 프로필 구성

필요한 키를 만든 후 자격 증명을 참조하도록 REST API 기술 프로필 메타데이터를 구성합니다.

1. 작업 디렉터리에서 확장 정책 파일(TrustFrameworkExtensions.xml)을 엽니다.
1. REST API 기술 프로필을 검색합니다. 예: `REST-ValidateProfile` 또는 `REST-GetProfile`
1. `<Metadata>` 요소를 찾습니다.
1. *AuthenticationType* 을 `ApiKeyHeader`로 변경합니다.
1. *AllowInsecureAuthInProduction* 을 `false`로 변경합니다.
1. `</Metadata>` 요소를 닫은 직후 다음 XML 코드 조각을 추가합니다.
    ```xml
    <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
    </CryptographicKeys>
    ```

암호화 키의 **Id** 는 HTTP 헤더를 정의합니다. 이 예에서 API 키는 **x-functions-key** 로 전송됩니다.

다음 XML 코드 조각은 API 키 인증을 사용하여 Azure Function을 호출하도록 구성된 RESTful 기술 프로필의 예제입니다.

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AuthenticationType">ApiKeyHeader</Item>
        <Item Key="AllowInsecureAuthInProduction">false</Item>
      </Metadata>
      <CryptographicKeys>
        <Key Id="x-functions-key" StorageReferenceId="B2C_1A_RestApiKey" />
      </CryptographicKeys>
      ...
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="next-steps"></a>다음 단계

- 사용자 지정 정책 참조의 [Restful 기술 프로필](restful-technical-profile.md) 요소에 대해 자세히 알아봅니다.

::: zone-end