---
title: Azure Active Directory B2C를 사용하여 BioCatch를 구성하는 자습서
titleSuffix: Azure AD B2C
description: 위험한 사용자 및 사기성 사용자를 식별하도록 BioCatch를 사용하여 Azure Active Directory B2C를 구성하는 자습서
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 173ef82e77e44f3b7e2a664d613b1810995fee21
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131044801"
---
# <a name="tutorial-configure-biocatch-with-azure-active-directory-b2c"></a>자습서: Azure Active Directory B2C를 사용하여 BioCatch 구성하기

이 샘플 자습서에서는 [BioCatch](https://www.biocatch.com/)와 Azure Active Directory(AD) B2C 인증을 통합 하여 CIAM(고객 ID 및 액세스 관리) 보안 태세를 추가로 보강하는 방법에 대해 알아봅니다. BioCatch는 사용자의 물리적 및 인지적 디지털 동작을 분석하여 합법적인 고객과 사이버 범죄자를 구분하는 인사이트를 생성합니다.

## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- [Azure AD B2C 테넌트](tutorial-create-tenant.md)를 Azure 구독에 연결

- [BioCatch](https://www.biocatch.com/contact-us) 계정

## <a name="scenario-description"></a>시나리오 설명

BioCatch 통합에는 다음 구성 요소가 포함됩니다.

- **웹앱 또는 웹 서비스** - 사용자는 먼저 이 웹 서비스를 탐색합니다. 이 웹 서비스는 BioCatch로 전송되는 고유한 클라이언트 세션 ID를 인스턴스화합니다. 그러면 클라이언트 세션 ID가 즉시 BioCatch로 사용자 동작 특성을 전송하기 시작합니다.

- **메서드** - Azure AD B2C에 고유한 클라이언트 세션 ID를 보냅니다. 제공된 예제에서 JavaScript는 숨겨진 HTML 필드에 값을 입력하는 데 사용됩니다.

- **Azure AD B2C 사용자 지정 UI** - 위의 메서드를 사용하는 경우 JavaScript에서 클라이언트 세션 ID 입력에 대한 HTML 필드를 숨깁니다.

- **Azure AD B2C 사용자 지정 정책**

  - UI에서 클레임 형식으로 사용자 지정 클라이언트 세션 ID를 사용합니다. 이는 자체 어설션된 기술 프로필을 통해 구현됩니다.

  - REST API 클레임 공급자를 통해 BioCatch와 통합하여 BioCatch 플랫폼에 클라이언트 세션 ID를 전달합니다.

  - BioCatch에서 사용자 지정 정책 논리에 여러 사용자 지정 클레임이 반환된 다음

  - 반환된 클레임을 평가하는 userjouney에 적용됩니다. 예를 들어 세션 위험과 MFA(다단계 인증) 호출과 같은 조건부 작업 실행이 있습니다.

![Bio catch 아키텍처의 다이어그램입니다.](media/partner-biocatch/biocatch-architecture-diagram.png)

| 단계  | Description |
|:---|:-----------------------|
|1a     | 사용자가 웹 서비스를 검색합니다. 그런 다음 웹 서비스는 HTML, CSS 또는 JavaScript 값을 반환하고 BioCatch JavaScript SDK를 로드하도록 구성합니다. 클라이언트 쪽 JavaScript는 BioCatch SDK에 대한 클라이언트 세션 ID를 구성 및 설정합니다. 또는 웹 서비스에서 클라이언트 세션 ID를 미리 구성하고 클라이언트에 보낼 수 있습니다.        |
|1b    |  BioCatch 플랫폼에 대해 인스턴스화된 BioCatch JavaScript SDK를 구성합니다. 1a 단계에서 클라이언트 세션 ID를 사용하여 클라이언트 디바이스에서 BioCatch로 사용자 동작 특성을 즉시 보내기 시작합니다.    |
|2     |  사용자가 등록/로그인을 시도하고 Azure AD B2C으로 리디렉션됩니다.      |
|3a    | userjourney의 일부는 클라이언트 세션 ID를 입력으로 사용하는 자체 어설션된 클레임 공급자입니다. 이 필드는 화면에서 숨겨져 있습니다. JavaScript를 사용하여 필드에 세션 ID를 입력할 수 있습니다. *다음* 단추를 선택하여 등록/로그인 프로세스를 계속합니다.|
|3b     | 클라이언트 세션 ID는 BioCatch 플랫폼에 제출되어 위험 점수를 확인합니다. |
|3c     | BioCatch는 세션에 대한 정보(예: 위험 점수) 및 수행할 작업에 대한 권장 사항 (허용 또는 차단)을 반환합니다. |
|3d    |userjourney에는 반환된 클레임에 대해 작동하는 조건부 검사 단계가 있습니다.|
| 4   | 조건부 검사 결과에 따라 *단계별 MFA* 와 같은 작업이 호출됩니다.|
|5    | 사용자가 웹 서비스 페이지를 처음 방문하는 경우 언제든지 웹 서비스는 클라이언트 세션 ID를 사용하여 BioCatch API를 쿼리하고 위험 점수와 세션 정보를 실시간으로 확인할 수 있습니다. |

## <a name="onboard-with-biocatch"></a>BioCatch로 온보드

[BioCatch](https://www.biocatch.com/contact-us)에 문의하여 계정을 만드세요.

## <a name="configure-the-custom-ui"></a>사용자 지정 UI 구성

클라이언트 세션 ID 필드를 숨기는 것이 좋습니다. CSS, JavaScript 또는 기타 메서드를 사용하여 필드를 숨깁니다. 테스트를 위해 필드를 표시하지 않을 수 있습니다. 예를 들어 JavaScript는 다음과 같이 입력 필드를 숨기는 데 사용됩니다.

```JavaScript
document.getElementById("clientSessionId").style.display = 'none';
```

## <a name="configure--azure-ad-b2c-identity-experience-framework-policies"></a>Azure AD B2C Identity Experience Framework 정책 구성

1. 초기 [사용자 지정 정책 구성](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)을 구성합니다.

2. 확장 파일에서 상속되는 새 파일을 만듭니다.

    ```xml
    <BasePolicy> 

        <TenantId>tenant.onmicrosoft.com</TenantId> 

        <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId> 

      </BasePolicy> 
    ```

3. BuildingBlocks 리소스 아래에서 입력 상자를 숨기도록 사용자 지정 UI에 대한 참조를 만듭니다.

    ```xml
    <ContentDefinitions> 

        <ContentDefinition Id="api.selfasserted"> 

            <LoadUri>https://domain.com/path/to/selfAsserted.cshtml</LoadUri> 

            <DataUri>urn:com:microsoft:aad:b2c:elements:contract:selfasserted:2.1.0</DataUri> 

          </ContentDefinition> 

        </ContentDefinitions>
    ```

4. BuildingBlocks 리소스 아래에 다음 클레임을 추가합니다.

    ```xml
    <ClaimsSchema> 

          <ClaimType Id="riskLevel"> 

            <DisplayName>Session risk level</DisplayName> 

            <DataType>string</DataType>       

          </ClaimType> 

          <ClaimType Id="score"> 

            <DisplayName>Session risk score</DisplayName> 

            <DataType>int</DataType>       

          </ClaimType> 

          <ClaimType Id="clientSessionId"> 

            <DisplayName>The ID of the client session</DisplayName> 

            <DataType>string</DataType> 

            <UserInputType>TextBox</UserInputType> 

          </ClaimType> 

    </ClaimsSchema> 
    ```

5. 클라이언트 세션 ID 필드에 대해 자체 어설션된 클레임 공급자를 구성합니다.

    ```xml
    <ClaimsProvider> 

          <DisplayName>Client Session ID Claims Provider</DisplayName> 

          <TechnicalProfiles> 

            <TechnicalProfile Id="login-NonInteractive-clientSessionId"> 

              <DisplayName>Client Session ID TP</DisplayName> 

              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" /> 

              <Metadata> 

                <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item> 

              </Metadata> 

              <CryptographicKeys> 

                <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" /> 

              </CryptographicKeys> 

            <!—Claim we created earlier --> 

              <OutputClaims> 

                <OutputClaim ClaimTypeReferenceId="clientSessionId" Required="false" DefaultValue="100"/> 

              </OutputClaims> 

            <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" /> 

            </TechnicalProfile> 

          </TechnicalProfiles> 

        </ClaimsProvider> 
    ```

6. BioCatch에 대한 REST API 클레임 공급자를 구성합니다. 

    ```xml
    <TechnicalProfile Id="BioCatch-API-GETSCORE"> 

          <DisplayName>Technical profile for BioCatch API to return session information</DisplayName> 

          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <Metadata> 

            <Item Key="ServiceUrl">https://biocatch-url.com/api/v6/score?customerID=<customerid>&amp;action=getScore&amp;uuid=<uuid>&amp;customerSessionID={clientSessionId}&amp;solution=ATO&amp;activtyType=<activity_type>&amp;brand=<brand></Item>

            <Item Key="SendClaimsIn">Url</Item> 

            <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item> 

            <!-- Set AuthenticationType to Basic or ClientCertificate in production environments --> 

            <Item Key="AuthenticationType">None</Item> 

            <!-- REMOVE the following line in production environments --> 

            <Item Key="AllowInsecureAuthInProduction">true</Item> 

          </Metadata> 

          <InputClaims> 

            <InputClaim ClaimTypeReferenceId="clientsessionId" /> 

          </InputClaims> 

          <OutputClaims> 

            <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

            <OutputClaim ClaimTypeReferenceId="score" /> 

          </OutputClaims> 

          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" /> 

        </TechnicalProfile> 

      </TechnicalProfiles>
    ```

    > [!NOTE]
    > BioCatch는 구성할 URL, 고객 ID 및 UUID(고유 사용자 ID)를 제공합니다. 고객 SessionID 클레임은 BioCatch에 대한 쿼리 문자열 매개 변수로 전달됩니다. 예를 들어 *MAKE_PAYMENT* 작업 유형을 선택할 수 있습니다.

7. userjourney 구성 예제를 따릅니다.

   1. clientSessionID를 클레임으로 가져옵니다.

   1. BioCatch API를 호출하여 세션 정보를 가져옵니다.

   1. 반환된 클레임 ‘위험’이 ‘낮은’ 경우 MFA를 위한 단계를 건너뛰고, 그렇지 않으면 사용자 MFA를 강제합니다. 

    ```xml
    <OrchestrationStep Order="8" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="clientSessionIdInput" TechnicalProfileReferenceId="login-NonInteractive-clientSessionId" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="9" Type="ClaimsExchange"> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="BcGetScore" TechnicalProfileReferenceId=" BioCatch-API-GETSCORE" /> 

          </ClaimsExchanges> 

        </OrchestrationStep> 

        <OrchestrationStep Order="10" Type="ClaimsExchange"> 

          <Preconditions> 

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true"> 

              <Value>riskLevel</Value> 

              <Value>LOW</Value> 

              <Action>SkipThisOrchestrationStep</Action> 

            </Precondition> 

          </Preconditions> 

          <ClaimsExchanges> 

            <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" /> 

          </ClaimsExchanges>
    ```

8. 신뢰 당사자 구성에 대한 구성(선택 사항)

    반환된 BioCatch 정보를 토큰의 클레임(특히 *risklevel* 및 ‘점수’)으로 애플리케이션에 전달하는 것이 유용합니다.

    ```xml
    <RelyingParty> 

    <DefaultUserJourney ReferenceId="SignUpOrSignInMfa" /> 

    <UserJourneyBehaviors> 

      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" /> 

      <SessionExpiryType>Absolute</SessionExpiryType> 

      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds> 

      <ScriptExecution>Allow</ScriptExecution> 

    </UserJourneyBehaviors> 

    <TechnicalProfile Id="PolicyProfile"> 

      <DisplayName>PolicyProfile</DisplayName> 

      <Protocol Name="OpenIdConnect" /> 

      <OutputClaims> 

        <OutputClaim ClaimTypeReferenceId="displayName" /> 

        <OutputClaim ClaimTypeReferenceId="givenName" /> 

        <OutputClaim ClaimTypeReferenceId="surname" /> 

        <OutputClaim ClaimTypeReferenceId="email" /> 

        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" /> 

        <OutputClaim ClaimTypeReferenceId="identityProvider" />                 

        <OutputClaim ClaimTypeReferenceId="riskLevel" /> 

        <OutputClaim ClaimTypeReferenceId="score" /> 

        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" /> 

      </OutputClaims> 

      <SubjectNamingInfo ClaimType="sub" /> 

    </TechnicalProfile> 

  </RelyingParty>
    ```

## <a name="integrate-with-azure-ad-b2c"></a>Azure AD B2C와 통합

Azure AD B2C에 정책 파일을 추가하려면 다음 단계를 수행합니다.

1. Azure AD B2C 테넌트의 전역 관리자로  [**Azure Portal**](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서  **전체 서비스** 를 선택하고 Azure AD B2C를 검색하여 선택합니다.
1.  **Azure AD B2C** > **Identity Experience Framework** 로 이동합니다.
1. 테넌트에 모든 정책 파일을 업로드합니다.

## <a name="test-the-solution"></a>솔루션 테스트

1. [JWT.MS로 리디렉션하는 더미 애플리케이션 등록](./tutorial-register-applications.md?tabs=app-reg-ga)  
1. **Identity Experience Framework** 에서 만든 정책을 선택합니다.
1. 정책 창에서 더미 JWT.MS 애플리케이션을 선택하고 **지금 실행** 을 선택합니다.
1. 등록 흐름을 따라 계정 만들기 JWT.MS로 반환된 토큰에는 riskLevel 및 점수에 대한 2배의 클레임이 있어야 합니다. 예제를 따릅니다.  

    ```JavaScript
    { 

      "typ": "JWT", 

      "alg": "RS256", 

      "kid": "_keyid" 

    }.{ 

      "exp": 1615872580, 

      "nbf": 1615868980, 

      "ver": "1.0", 

      "iss": "https://tenant.b2clogin.com/12345678-1234-1234-1234-123456789012/v2.0/", 

      "sub": "12345678-1234-1234-1234-123456789012", 

      "aud": "12345678-1234-1234-1234-123456789012", 

      "acr": "b2c_1a_signup_signin_biocatch_policy", 

      "nonce": "defaultNonce", 

      "iat": 1615868980, 

      "auth_time": 1615868980, 

      "name": "John Smith", 

      "email": "john.smith@contoso.com", 

      "given_name": "John", 

      "family_name": "Smith", 

      "riskLevel": "LOW", 

      "score": 275, 

      "tid": "12345678-1234-1234-1234-123456789012" 

    }.[Signature]  

    ```

## <a name="additional-resources"></a>추가 리소스

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
