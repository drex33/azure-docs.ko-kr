---
title: 토큰 구성 - Azure Active Directory B2C
description: Azure Active Directory B2C에서 토큰 수명 및 호환성 설정을 구성하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2021
ms.custom: project-no-code
ms.author: kengaderdus
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 70be31920e96c651285c4e77ace161b65ccd3118
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130066037"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Azure Active Directory B2C에서 토큰 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)에서 [토큰 수명 및 호환성](tokens-overview.md)을 구성하는 방법을 알아봅니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>토큰 수명 동작

다음을 포함하여 토큰 수명을 구성할 수 있습니다.

- **액세스 및 ID 토큰 수명(분)** - OAuth 2.0 전달자 토큰 및 ID 토큰의 수명입니다. 기본값은 60분(1시간)입니다. 최솟값(포함)은 5분입니다. 최댓값(포함)은 1,440분(24시간)입니다.
- **새로 고침 토큰 수명(일)** - 애플리케이션에 `offline_access` 범위가 부여된 경우 새 액세스 토큰을 획득하는 데 새로 고침 토큰을 사용할 수 있기 전까지의 최대 기간입니다. 기본값은 14일입니다. 최솟값(포함)은 1일입니다. 최대(포함) 90일입니다.
- **새로 고침 토큰 슬라이딩 윈도우 수명** - 새로 고침 토큰 슬라이딩 윈도우 유형입니다. `Bounded`는 **수명 길이(일)** 에 지정된 대로 새로 고침 토큰을 연장할 수 있음을 나타냅니다. `No expiry`는 새로 고침 토큰 슬라이딩 윈도우 수명이 만료되지 않음을 나타냅니다.
- **수명 길이(일)** - 이 기간이 경과하면 애플리케이션이 획득한 대부분의 최근 새로 고침 토큰의 유효 기간에 관계없이 사용자는 강제로 다시 인증을 받게 됩니다. 값은 **새로 고침 토큰 수명** 값보다 크거나 같아야 합니다.

다음 다이어그램은 새로 고침 토큰 슬라이딩 윈도우 수명 동작을 보여 줍니다.

![새로 고침 토큰 수명](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >PKCE에서 인증 코드 흐름을 사용하는 단일 페이지 애플리케이션에는 항상 24시간의 새로 고침 토큰 수명이 있지만 모바일 앱, 데스크톱 앱 및 웹앱에는 이 제한 사항이 없습니다. [브라우저에서 새로 고침 토큰의 보안 관련 사항에 대해 자세히 알아봅니다](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).


## <a name="configure-token-lifetime"></a>토큰 수명 구성

::: zone pivot="b2c-user-flow"

사용자 흐름 토큰 수명을 구성하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **토큰 수명** 에서 애플리케이션의 요구 사항에 맞게 속성을 조정합니다.
1. **저장** 을 클릭합니다.



:::image type="content" source="./media/configure-tokens/configure-tokens.png" alt-text="Azure Portal에서 사용자 흐름 토큰을 구성합니다.":::


::: zone-end

::: zone pivot="b2c-custom-policy"

토큰 호환성의 설정을 변경하려면 확장에서 [토큰 발급자](jwt-issuer-technical-profile.md) 기술 프로필 메타데이터를 설정하거나 영향을 주려는 정책의 신뢰 당사자 파일을 설정합니다. 토큰 발급자 기술 프로필은 다음 예제와 같이 표시됩니다.

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

위의 예제에서는 다음 값이 설정됩니다.

- **token_lifetime_secs** - 액세스 토큰 수명(초)입니다. 기본값은 3,600(1시간)입니다. 최솟값은 300(5분)입니다. 최댓값은 86,400(24시간)입니다. 
- **id_token_lifetime_secs** - ID 토큰 수명(초)입니다. 기본값은 3,600(1시간)입니다. 최솟값은 300(5분)입니다. 최댓값은 86,400(24시간)입니다. 
- **refresh_token_lifetime_secs** - 새로 고침 토큰 수명(초)입니다. 기본값은 120,9600(14일)입니다. 최솟값은 86,400(24시간)입니다. 최댓값은 7,776,000(90일)입니다. 
- **rolling_refresh_token_lifetime_secs** - 새로 고침 토큰 슬라이딩 윈도우 수명(초)입니다. 기본값은 7,776,000(90일)입니다. 최솟값은 86,400(24시간)입니다. 최댓값은 31,536,000(365일)입니다. 슬라이딩 윈도우 수명을 적용하지 않으려면 `allow_infinite_rolling_refresh_token` 값을 `true`로 설정합니다. 
- **allow_infinite_rolling_refresh_token** - 새로 고침 토큰 슬라이딩 윈도우 수명이 만료되지 않습니다. 

::: zone-end


## <a name="token-compatibility-settings"></a>토큰 호환성 설정

다음을 포함하여 토큰 호환성을 구성할 수 있습니다.

- **발급자(iss) 클레임** - 액세스 및 ID 토큰 발급자 형식입니다.
- **주체(sub) 클레임** - 애플리케이션 사용자 등 토큰이 정보를 어설션하는 보안 주체입니다. 이 값은 변경할 수 없으며 재할당 또는 재사용할 수 없습니다. 예를 들어 리소스 액세스에 토큰을 사용할 때 이 값을 사용하면 안전하게 인증 검사를 수행할 수 있습니다. 기본적으로 주체 클레임은 디렉터리에 있는 사용자의 개체 ID로 채워집니다.
- **사용자 흐름을 나타내는 클레임** - 이 클레임은 실행된 사용자 흐름을 식별합니다. 가능한 값은 `tfp`(기본값) 또는 `acr`입니다.

::: zone pivot="b2c-user-flow"

사용자 흐름 호환성 설정을 구성하려면:

1. **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **속성** 을 선택합니다.
1. **토큰 호환성 설정** 에서 애플리케이션의 요구 사항에 맞게 속성을 조정합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

토큰 호환성의 설정을 변경하려면 확장에서 [토큰 발급자](jwt-issuer-technical-profile.md) 기술 프로필 메타데이터를 설정하거나 영향을 주려는 정책의 신뢰 당사자 파일을 설정합니다. 토큰 발급자 기술 프로필은 다음 예제와 같이 표시됩니다.

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **발급자(iss) 클레임** - **IssuanceClaimPattern** 메타데이터 항목을 통해 발급자(iss) 클레임을 설정합니다. 적용 가능한 값은 `AuthorityAndTenantGuid` 및 `AuthorityWithTfp`입니다.
- **정책 ID를 나타내는 클레임 설정** - 이 값을 설정하기 위한 옵션은 `TFP`(보안 프레임워크 정책) 및 `ACR`(인증 컨텍스트 참조)입니다. 권장 값은 `TFP`입니다. `None` 값으로 **AuthenticationContextReferenceClaimPattern** 을 설정하고

    **ClaimsSchema** 요소에서 이 요소를 추가합니다.

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    [신뢰 당사자 정책](relyingparty.md)의 **OutputClaims** 요소 아래에 다음 출력 클레임을 추가합니다.

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" PartnerClaimType="tfp" />
    ```

    ACR의 경우 **AuthenticationContextReferenceClaimPattern** 항목을 제거합니다.

- **주체(sub) 클레임** - 이 옵션은 기본적으로 ObjectID입니다. 이 설정을 `Not Supported`로 전환하려면 아래 줄을

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    다음 줄로 바꿉니다.

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>앱에 선택적 클레임 제공

애플리케이션 클레임은 애플리케이션에 반환되는 값입니다. 원하는 클레임을 포함하도록 사용자 흐름을 업데이트합니다.

::: zone pivot="b2c-user-flow"

1. **사용자 흐름(정책)** 을 선택합니다.
1. 이전에 만든 사용자 흐름을 엽니다.
1. **애플리케이션 클레임** 을 선택합니다.
1. 애플리케이션에 다시 전송하려는 클레임 및 특성을 선택합니다.
1. **저장** 을 클릭합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

[신뢰 당사자 정책 기술 프로필](relyingparty.md#technicalprofile) 출력 클레임은 애플리케이션에 반환되는 값입니다. 출력 클레임을 추가하면 성공적인 사용자 경험 이후 토큰에 클레임이 발급되며 애플리케이션으로 전송됩니다. 신뢰 당사자 섹션 내에서 기술 프로필 요소를 수정하여 원하는 클레임을 출력 클레임으로 추가합니다.

1. 사용자 지정 정책 파일(예: SignUpOrSignin.xml)을 엽니다.
1. OutputClaims 요소를 찾습니다. 토큰에 포함하려는 OutputClaim을 추가합니다. 
1. 출력 클레임 특성을 설정합니다. 

다음 예제에서는 `accountBalance` 클레임을 추가합니다. accountBalance 클레임은 애플리케이션에 잔액으로 전송됩니다. 

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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

OutputClaim 요소에는 다음 특성이 포함됩니다.

- **ClaimTypeReferenceId** - 정책 파일 또는 부모 정책 파일의 [ClaimsSchema](claimsschema.md) 섹션에 이미 정의된 클레임 유형의 식별자입니다.
- **PartnerClaimType** - 토큰에서 클레임 이름을 변경할 수 있습니다. 
- **DefaultValue** - 기본값입니다. 기본값을 테넌트 ID와 같은 [클레임 해결 프로그램](claim-resolver-overview.md)으로 설정할 수도 있습니다.
- **AlwaysUseDefaultValue** - 기본값을 강제로 사용합니다.

::: zone-end

## <a name="authorization-code-lifetime"></a>인증 코드 수명

[OAuth 2.0 인증 코드 흐름](authorization-code-flow.md)을 사용하는 경우 앱은 인증 코드를 사용하여 대상 리소스에 대한 액세스 토큰을 요청할 수 있습니다. 인증 코드는 약 10분 후에 만료되는 수명이 짧은 코드입니다. 인증 코드 수명은 구성할 수 없습니다. 애플리케이션이 10분 내에 인증 코드를 사용해야 합니다. 

## <a name="next-steps"></a>다음 단계

[액세스 토큰을 요청하는 방법](access-tokens.md)에 대해 자세히 알아보세요.
