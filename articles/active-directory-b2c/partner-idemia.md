---
title: Azure Active Directory B2C를 통해 IDEMIA 구성(미리 보기)
titleSuffix: Azure AD B2C
description: 신뢰 당사자가 IDEMIA 또는 미국 주 발급 모바일 ID를 사용하기 위해 IDEMIA와 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/21/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 27c83d2ade4552150793aa8fbe777e376a2ae762
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012880"
---
# <a name="tutorial-configure-idemia-with-azure-active-directory-b2c-for-relying-party-to-consume-idemia-or-us-state-issued-mobile-identity-credentials-preview"></a>자습서: 신뢰 당사자가 IDEMIA 또는 미국 주 발급 모바일 ID 자격 증명을 사용할 수 있도록 Azure Active Directory B2C를 사용하여 IDEMIA 구성(미리 보기)
[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

이 기능은 사용자 지정 정책에만 사용할 수 있습니다. 설정 단계의 경우 이전 선택기의 **사용자 지정 정책** 을 선택합니다.

::: zone-end

::: zone pivot="b2c-custom-policy"

이 샘플 자습서에서는 Azure AD(Azure Active Directory) B2C를 [IDEMIA](https://www.idemia.com/)와 통합하는 방법에 대한 지침을 제공합니다. IDEMIA는 암호 없는 인증 공급자로, 얼굴 ID 및 지문 인식과 같은 생체 인식 인증을 통해 실시간 동의 기반 서비스를 제공하여 사기 행위 및 자격 증명 재사용을 제거합니다. IDEMIA의 모바일 ID를 사용하면 시민이 실제 ID를 보완하기 위해 정부에서 발급한 신뢰할 수 있는 디지털 ID를 활용할 수 있습니다. 이 애플리케이션은 자체 선택된 PIN 또는 터치 ID/얼굴 ID를 사용하여 ID를 확인하는 데 사용됩니다. 모바일 ID를 사용하면 시민이 트랜잭션에 필요한 정보만 공유하도록 허용하여 ID를 제어할 수 있으며 사기 보호를 사용할 수 있습니다.



## <a name="scenario-description"></a>시나리오 설명

IDEMIA 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C** - 사용자의 자격 증명(ID 공급자(IdP)라고도 함)을 확인하는 권한 부여 서버입니다.

- **IDEMIA mID** - [Azure AD B2C 외부 공급자](add-identity-provider.md)로 구성된 OIDC(OpenID 커넥트) 공급자입니다.

- **[IDEMIA mID 애플리케이션](https://idemia-mobile-id.com/)** - 신뢰할 수 있는 정부 발급 디지털 ID입니다. 모바일 ID는 휴대폰의 앱에 있는 운전 면허증 또는 주 발급 ID의 디지털 버전입니다. [IDEMIA](https://idemia-mobile-id.com/)

IDEMIA는 많은 미국 주 자동차 부서(DMV)에 mID를 제공합니다.

mID는 식별 문서를 강력한 모바일 ID 토큰으로 디지털화하여 인증을 위해 이동성이 높으며 **mID 서비스** 승인을 위한 색인 역할을 합니다. mID 서비스를 사용하면 DMV는 발급된 운전 면허증 및 생체 인식 **셀피** 대 자격 증명 얼굴 인식 일치 서비스를 사용하는 자격 증명 문서 인증을 통해 개인의 ID를 증명할 수 있습니다.  

mID가 만들어지면 최종 사용자의 휴대폰에 **에지에서 디지털 서명된 ID** 로 저장됩니다. 이제 최종 사용자는 해당 서명된 자격 증명을 사용하여 연령 증명, 재무 정보 고객, 보안이 매우 중요한 계정 액세스와 같은 기타 ID에 민감한 서비스에 액세스할 수 있습니다.

Microsoft에 대한 제안은 mID 소유자가 보낸 속성을 사용하여 서비스를 제공하기 위해 주에서 발급한 mID를 사용하는 RP(신뢰 당사자)로서 이러한 서비스를 지원하는 것입니다.

다음 다이어그램은 웹 또는 온-프레미스 시나리오에 대한 구현을 보여줍니다.

![스크린샷은 온-프레미스 확인을 보여줍니다.](./media/partner-idemia/idemia-architecture-diagram.png)

| 단계 | 설명 |
|:--------|:--------|
| 1. | 사용자가 디바이스에서 회신 당사자인 Azure AD B2C 로그인 페이지를 방문하여 트랜잭션을 수행하고 mID 앱을 통해 로그인합니다. |
| 2. | Azure AD B2C는 ID 검사가 필요하며 OIDC 인증 코드 흐름을 사용하여 사용자를 IDEMIA 라우터로 리디렉션합니다.|
| 3. | IDEMIA 라우터는 인증 및 권한 부여 요청의 모든 컨텍스트 세부 정보를 포함하여 사용자 모바일 앱에 생체 인식 챌린지를 보냅니다.|
| 4. | 필요한 보안 수준에 따라 사용자는 추가 세부 정보를 제공하거나, PIN을 입력하거나, PIN을 입력하거나, 라이브 스트리밍을 수행하거나, 모두 수행해야 할 수 있습니다.|
| 5. | 최종 인증 응답은 소유, 존재 및 동의의 증거를 제공합니다. 응답은 IDEMIA 라우터에 반환됩니다.|
| 6. | IDEMIA 라우터는 사용자가 제공한 정보를 확인하고 인증 결과를 Azure AD B2C로 보냅니다.|
|7. | 인증 결과에 따라 사용자에게 액세스 권한이 부여/거부됩니다. |

## <a name="onboard-with-idemia"></a>IDEMIA로 온보딩

데모를 요청하려면 [IDEMIA](https://www.idemia.com/get-touch/)에 문의하세요. 요청 양식을 작성하는 동안 메시지 필드에 Azure AD B2C로 온보딩하려고 한다는 점을 표시합니다.

## <a name="integrate-idemia-with-azure-ad-b2c"></a>IDEMIA와 Azure AD B2C 통합

## <a name="prerequisites"></a>사전 요구 사항

시작하려면 다음이 필요합니다.

- IDEMIA가 있는 최종 사용자에 대한 액세스 - 미국 주 발급 mID(모바일 ID 자격 증명) 또는 테스트 단계 중 [IDEMIA](https://www.idemia.com/)에서 제공하는 mID 데모 애플리케이션

- Azure AD 구독 계정이 없으면  [체험 계정](https://azure.microsoft.com/free/)을 얻습니다.

- Azure 구독에 연결되어 있는  [An Azure AD B2C 테넌트](tutorial-create-tenant.md). 

- Azure AD B2C 테넌트에서 등록된 비즈니스 웹 애플리케이션입니다. 테스트를 위해 토큰의 디코딩된 콘텐츠를 표시하는 Microsoft 소유 웹 애플리케이션인 https://jwt.ms 를 구성할 수 있습니다.

>[!NOTE]
>토큰의 내용은 브라우저에서 떠나지 않습니다.


### <a name="part-1---submit-a-relying-party-application-on-boarding-for-mid"></a>1부 - mID에 대한 신뢰 당사자 애플리케이션 온보딩 제출

IDEMIA 통합의 일부로 다음 정보가 제공됩니다.

| 속성 | 설명 |
|:---------|:----------|
| 애플리케이션 이름 | Azure AD B2C 또는 원하는 애플리케이션 이름 |
| Client_ID | IdP에서 제공하는 고유 식별자입니다. |
| 클라이언트 암호 | 신뢰 당사자 애플리케이션이 IDEMIA IdP를 사용하여 인증하는 데 사용할 암호 |
| 메타데이터 엔드포인트 | OpenID의 잘 알려진 구성 엔드포인트라고도 하는 토큰 발급자 구성 문서를 가리키는 URL입니다. |
|리디렉션 URI | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp`<br>예 - `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.<br><br>사용자 지정 도메인을 사용하는 경우 `https://your-domain-name/your-tenant-name.onmicrosoft.com/oauth2/authresp`를 입력합니다.<br>your-domain-name을 사용자 지정 도메인으로 바꾸고 your-tenant-name을 테넌트 이름으로 바꿉니다. |
|로그아웃 후 리디렉션 URI | `https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/{policy}/oauth2/v2.0/logout`<br>로그아웃 요청 보내기. |

>[!NOTE]
>나중에 Azure AD B2C에서 IdP를 구성하려면 IDEMIA 클라이언트 ID 및 클라이언트 암호가 필요합니다.

### <a name="part-2---create-a-policy-key"></a>2부 - 정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 IDEMIA 클라이언트 암호를 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.

3. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.

4. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.

5. **개요** 페이지에서 **Identity Experience Framework** 를 선택합니다.

6. **정책 키**, **추가** 를 차례로 선택합니다.

7. **옵션** 에서 **수동** 을 선택합니다.

8. 정책 키의 **이름** 을 입력합니다. 예를 들어 IdemiaAppSecret입니다. B2C_1A_ 접두사가 키의 이름에 자동으로 추가됩니다.

9. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.

10. **키** 사용으로는 **서명** 을 선택합니다.

11. **만들기** 를 선택합니다.

### <a name="part-3---configure-idemia-as-an-external-idp"></a>3부 - IDEMIA를 외부 IdP로 구성

사용자가 IDEMIA 모바일 ID 암호 없는 ID를 사용하여 로그인할 수 있도록 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 IDEMIA를 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 특정 사용자가 디바이스에서 사용 가능한 지문 또는 얼굴 검사와 같은 생체 인식을 사용하여 인증했는지 확인하는 데 사용되는 클레임 집합을 제공하여 사용자 ID를 증명합니다.
정책의 확장 파일에서 **ClaimsProvider** 요소를 추가하여 IDEMIA를 클레임 공급자로 정의할 수 있습니다.

```PowerShell
     <TechnicalProfile Id="Idemia-Oauth2">
          <DisplayName>IDEMIA</DisplayName>
          <Description>Login with your IDEMIA identity</Description>
          <Protocol Name="OAuth2" />
          <Metadata>
            <Item Key="METADATA">https://idp.XXXX.net/oxauth/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid id_basic mt_scope</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="token_endpoint_auth_method">client_secret_basic</Item>
            <Item Key="ClaimsEndpoint">https://idp.XXXX.net/oxauth/restv1/userinfo</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_IdemiaAppSecret" />
</CryptographicKeys>
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="acr" PartnerClaimType="acr_values" DefaultValue="loa-2" />
          </InputClaims>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName1" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="lastName1" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="idemia" />
            <OutputClaim ClaimTypeReferenceId="documentId" />
            <OutputClaim ClaimTypeReferenceId="address1" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
           
```

client_id를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.

|속성 | 설명|
|:------|:-------|
|범위| OIDC(OpenID 커넥트)의 경우 범위 매개 변수를 **openid** 로 설정해야 합니다. 추가 범위는 공백으로 구분된 목록으로 추가될 수 있습니다.|
|redirect_uri | 이는 사용자 에이전트가 권한 부여 코드를 Azure AD B2C 다시 보내는 위치를 정의합니다.|
|response_type| 권한 부여 코드 흐름의 경우 **코드** 로 설정됩니다.|
|acr_values| 이 매개 변수는 인증 프로세스 중에 사용자가 수행해야 하는 인증 방법을 제어합니다. |

다음 값 중 하나를 선택해야 합니다.

|매개 변수 값| 사용자 인증 프로세스에 미치는 영향 |
|:------|:-------|
|`loa-2`| 암호화 기반 Azure AD 다단계 인증만|
|`loa-3`| 암호화 기반 Azure AD 다단계 인증 및 추가 요소 1개|
|`loa-4`| 사용자가 핀 기반 및 생체 인식 인증도 수행해야 하는 요구 사항이 있는 암호화 기반 Azure AD 다단계 인증 |

**/userinfo** 엔드포인트는 권한 부여 요청에서 요청된 범위에 대한 클레임을 제공합니다. **<mt_scope>** 경우 여기에는 다른 항목 중에서 이름, 성 및 운전 면허증 번호와 같은 클레임이 포함됩니다.
지정된 범위에 대해 설정된 클레임은 검색 API의 **scope_to_claims_mapping** 섹션에 게시됩니다.
Azure AD B2C 클레임 엔드포인트에서 클레임을 요청하고 OutputClaims 요소에 해당 클레임을 반환합니다. 정책에 정의된 클레임의 이름을 IdP에 정의된 이름에 매핑하여 [ClaimSchema 요소](claimsschema.md)에 클레임 유형을 정의해야 할 수 있습니다.

```PowerShell
<ClaimType Id="documentId">
     <DisplayName>documentId</DisplayName>
     <DataType>string</DataType>
</ClaimType>
<ClaimType Id="address1">
     <DisplayName>address</DisplayName>
     <DataType>string</DataType>
</ClaimType>
```

### <a name="part-4---add-a-user-journey"></a>4부 - 사용자 경험 추가

이 시점에서 IdP가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 자체 사용자 지정 사용자 경험이 없는 경우 기존 템플릿 사용자 경험의 복제본을 만듭니다. 그렇지 않으면 다음 단계를 계속 진행합니다.

1. 시작 팩에서 `TrustFrameworkBase.xml` 파일을 엽니다.

2. `ID=SignUpOrSignIn`이 포함된 **UserJourneys** 요소를 찾아서 전체 콘텐츠를 복사합니다.

3. `TrustFrameworkExtensions.xml`을 열고 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.

4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 UserJourneys 요소의 자식으로 붙여넣습니다.

5. 사용자 경험 ID의 이름을 바꿉니다. 예들 들어 `ID=CustomSignUpSignIn`입니다.

### <a name="part-5---add-the-idp-to-a-user-journey"></a>5부 - 사용자 경험에 IdP 추가

이제 사용자 경험이 있으므로 사용자 경험에 새 IdP를 추가합니다. 먼저 로그인 단추를 추가한 다음, 단추를 작업에 연결합니다. 이 작업은 이전에 만든 기술 프로필입니다.

1. 사용자 경험의 Type=`CombinedSignInAndSignUp` 또는 Type=`ClaimsProviderSelection`을 포함하는 오케스트레이션 단계 요소를 찾습니다. 일반적으로 첫 번째 오케스트레이션 단계입니다. **ClaimsProviderSelections** 요소에는 사용자가 로그인 할 수 있는 IdP의 목록이 포함되어 있습니다. 요소의 순서는 사용자에게 표시되는 로그인 단추의 순서를 제어합니다. **ClaimsProviderSelection** XML 요소를 추가합니다. **TargetClaimsExchangeId** 의 값을 친숙한 이름으로 설정합니다.

2. 다음 오케스트레이션 단계에서 **ClaimsExchange** 요소를 추가합니다. **Id** 를 대상 클레임 교환 ID 값으로 설정합니다. **TechnicalProfileReferenceId** 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다.

다음 XML은 IdP를 사용하여 사용자 경험의 처음 두 오케스트레이션 단계를 보여줍니다.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="IdemiaExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="IdemiaExchange" TechnicalProfileReferenceId="Idemia-Oauth2" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>6부 - 신뢰 당사자 정책 구성

신뢰 당사자 정책(예: [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml))은 Azure AD B2C에서 실행할 사용자 경험을 지정합니다. 신뢰 당사자 내에서 **DefaultUserJourney** 요소를 찾습니다. IdP를 추가한 사용자 경험 ID와 일치하도록 **ReferenceId** 를 업데이트합니다.

다음 예제에서는 `CustomSignUpOrSignIn` 사용자 경험에 대해 **ReferenceId** 가 `CustomSignUpOrSignIn`으로 설정됩니다.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>7부 - 사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com/#home)에 로그인합니다.

2. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.

3. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.

4. [Azure Portal](https://portal.azure.com/#home)에서 **Azure AD B2C** 를 검색하고 선택합니다.

5. 정책에서 **Identity Experience Framework** 를 선택합니다.

**사용자 지정 정책 업로드** 를 선택한 다음, 변경한 두 정책 파일을 확장 정책(예: `TrustFrameworkExtensions.xml`), 신뢰 당사자 정책(예: `SignUpSignIn.xml`) 순으로 업로드합니다.

### <a name="part-8---test-your-custom-policy"></a>8부 - 사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.

2. **애플리케이션** 에서 [이전에 등록된](./tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.

3. **지금 실행** 단추를 선택합니다.

4. 등록 또는 로그인 페이지에서 **IDEMIA** 를 선택하여 IDEMIA - 미국 주 발급 mID(모바일 ID 자격 증명)로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 `https://jwt.ms`로 리디렉션되며, Azure AD B2C에서 반환된 토큰의 내용이 표시됩니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](tutorial-create-user-flows.md?pivots=b2c-custom-policy)

- [IDEMIA mID에 대해 자세히 알아보기](https://www.idemia.com/mobile-id)

::: zone-end
