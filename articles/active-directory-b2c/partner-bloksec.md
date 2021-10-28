---
title: BlokSec를 사용하여 Azure Active Directory B2C를 구성하는 방법에 대한 자습서
titleSuffix: Azure AD B2C
description: 암호 없는 인증을 위해 BlokSec와 Azure AD B2C 인증을 통합하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/20/2021
ms.author: gasinh
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a983da4159f41ae6dfe261b7f42ce20c2d2fa3a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594748"
---
# <a name="tutorial-configure-azure-active-directory-b2c-with-bloksec-for-passwordless-authentication"></a>자습서: 암호 없는 인증을 위해 BlokSec를 사용하여 Azure Active Directory B2C 구성

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"



::: zone-end

이 샘플 자습서에서는 AD(Azure Active Directory) B2C 인증과 [BlokSec](https://bloksec.com/)를 통합하는 방법에 대해 알아봅니다. BlokSec는 고객에게 암호 없는 인증 및 토큰 없는 MFA(다단계 인증)를 제공하여 최종 사용자 로그인 환경을 간소화합니다. BlokSec는 암호 입력, 피싱 및 중간자(man-in-the-middle) 공격과 같은 ID 중심 사이버 공격으로부터 고객을 보호합니다.

## <a name="scenario-description"></a>시나리오 설명

BlokSec 통합에는 다음 구성 요소가 포함됩니다.

- **Azure AD B2C** - 모든 B2C 애플리케이션에 대한 승인 서버/ID 공급자로 구성됩니다.

- **BlokSec 탈중앙화 ID 라우터** - BlokSec의 DIaaS™를 적용하여 인증 및 권한 부여 요청을 최종 사용자의 개인 ID 공급자(PIdP) 애플리케이션으로 라우팅하려는 서비스의 게이트웨이 역할을 합니다. Azure AD B2C에서 OIDC(OpenID Connect) ID 공급자로 구성됩니다.

- **BlokSec SDK 기반 모바일 앱** - 탈중앙화 인증 시나리오에서 사용자의 PIdP 역할을 합니다. 무료로 다운로드할 수 있는 [BlokSec yuID](https://play.google.com/store/apps/details?id=com.bloksec) 애플리케이션은 조직에서 BlokSec SDK를 사용하여 자체 모바일 애플리케이션을 개발하지 않으려는 경우 사용할 수 있습니다.
다음 아키텍처 다이어그램은 구현된 상황을 보여 줍니다.

![아키텍처 다이어그램을 보여 주는 이미지](./media/partner-bloksec/partner-bloksec-architecture-diagram.png)

|단계| 설명|
|:---------------|:----------------|
|1.| 사용자가 Azure AD B2C 애플리케이션에 로그인을 시도하고 Azure AD B2C의 결합된 로그인 및 등록 정책에 전달됩니다.|
|2.| Azure AD B2C는 OIDC 인증 코드 흐름을 사용하여 사용자를 BlokSec 탈중앙화 ID 라우터로 리디렉션합니다.|
|3.| BlokSec 탈중앙화 라우터는 인증 및 권한 부여 요청의 모든 컨텍스트 세부 정보를 포함하여 사용자 모바일 앱에 푸시 알림을 보냅니다.|
|4.| 사용자는 인증 질문을 검토하고 승인되면 디바이스에서 사용할 수 있는 지문 또는 얼굴 검사와 같은 생체 인식 정보를 요청하는 메시지가 표시되어 사용자의 ID를 증명합니다.|
|5.| 응답은 사용자의 고유한 디지털 키로 디지털 서명됩니다. 최종 인증 응답은 소유, 존재 및 동의의 증거를 제공합니다. 응답은 BlokSec 탈중앙화 ID 라우터로 반환됩니다.|
|6.| BlokSec 탈중앙화 ID 라우터는 분산 원장에 저장된 사용자의 변경할 수 없는 고유 공개 키에 대해 디지털 서명을 확인한 다음 인증 결과로 Azure AD B2C에 응답합니다.|
|7.| 인증 결과에 따라 사용자에게 액세스 권한이 부여/거부됩니다.|

## <a name="onboard-to-bloksec"></a>BlokSec에 온보딩

[양식](https://bloksec.com/request-a-demo/)을 작성하여 BlokSec에 데모 테넌트를 요청하세요. 메시지 필드에서 Azure AD B2C를 사용하여 온보딩할 것임을 나타냅니다. 앱 스토어에서 무료 BlokSec yuID 모바일 앱을 다운로드하여 설치합니다. 데모 테넌트가 준비되면 이메일을 받게 됩니다. BlokSec 애플리케이션이 설치된 모바일 디바이스에서 yuID 앱에 관리자 계정을 등록하는 링크를 선택합니다.

::: zone pivot="b2c-user-flow"
## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)입니다.

- BlokSec [평가판 계정](https://bloksec.com/).

- 아직 수행하지 않은 경우 웹 애플리케이션을 [등록](./tutorial-register-applications.md)하고 [ID 토큰 암시적 허용을 사용하도록 설정](./tutorial-register-applications.md#enable-id-token-implicit-grant)합니다.
::: zone-end

::: zone pivot="b2c-custom-policy"
## <a name="prerequisites"></a>필수 구성 요소

시작하려면 다음이 필요합니다.

- Azure AD 구독 구독이 없는 경우 [체험 계정](https://azure.microsoft.com/free/)을 얻을 수 있습니다.

- Azure 구독에 연결된 [Azure AD B2C 테넌트](./tutorial-create-tenant.md)입니다.

- BlokSec [평가판 계정](https://bloksec.com/).

- 아직 수행하지 않은 경우 웹 애플리케이션을 [등록](./tutorial-register-applications.md)하고 [ID 토큰 암시적 허용을 사용하도록 설정](./tutorial-register-applications.md#enable-id-token-implicit-grant)합니다.

- [**Azure Active Directory B2C에서 사용자 지정 정책을 사용하여 시작**](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)의 단계를 완료합니다.
::: zone-end

### <a name="part-1---create-an-application-registration-in-bloksec"></a>1부 - BlokSec에서 애플리케이션 등록 만들기

1. BlokSec 관리 포털에 로그인합니다. BlokSec에 온보딩할 때 받은 계정 등록 이메일의 일부로 링크가 포함됩니다.

2. 기본 대시보드에서 **애플리케이션 추가 > 사용자 지정 만들기** 를 선택합니다.

3. 다음과 같이 애플리케이션 세부 정보를 완료하고 제출합니다.  

   |속성  |값  |
   |---------|---------|
   |  Name         |Azure AD B2C 또는 원하는 애플리케이션 이름|
   |SSO 유형         | OIDC|
   |로고 URI     |[https://bloksec.io/assets/AzureB2C.png](https://bloksec.io/assets/AzureB2C.png) 선택한 이미지에 대한 링크|
   |리디렉션 URI     | https://**your-B2C-tenant-name**.b2clogin.com/**your-B2C-tenant-name**.onmicrosoft.com/oauth2/authresp<BR>**예**:      'https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp ' <BR><BR>사용자 지정도메인을 사용하는 경우 https://**your-domain-name**/**your-tenant-name**.onmicrosoft.com/oauth2/authresp를 입력합니다. <BR> your-domain-name을 사용자 지정 도메인으로 바꾸고 your-tenant-name을 테넌트 이름으로 바꿉니다.         |
   |로그아웃 후 리디렉션 URI  |https://**your-B2C-tenant-name**.b2clogin.com/**your-B2C-tenant-name**.onmicrosoft.com/ **{policy}** /oauth2/v2.0/logout <BR> [로그아웃 요청 보내기](./openid-connect.md#send-a-sign-out-request). |

4. 저장한 후 새로 만든 Azure AD B2C 애플리케이션을 선택하여 애플리케이션 구성을 열고 **앱 비밀 생성** 을 선택합니다.

>[!NOTE]
>Azure AD B2C에서 ID 공급자를 구성하려면 나중에 애플리케이션 ID와 애플리케이션 비밀이 필요합니다.

::: zone pivot="b2c-user-flow"

### <a name="part-2---add-a-new-identity-provider-in-azure-ad-b2c"></a>2부 - Azure AD B2C에서 새 ID 공급자 추가

1. Azure AD B2C 테넌트의 전역 관리자로 [Azure Portal](https://portal.azure.com/#home)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다. 
1. **대시보드 >Azure Active Directory B2C >ID 공급자** 로 이동합니다.
1. 새 **OpenID Connect 공급자** 를 선택합니다.
1. **추가** 를 선택합니다.

### <a name="part-3---configure-an-identity-provider"></a>3부 - ID 공급자 구성

1. **ID 공급자 유형 > OpenID Connect** 를 선택합니다.

1. 양식을 작성하여 ID 공급자를 설정합니다.

|속성  |값  |
|:---------|:---------|
|Name     |BlokSec yuID -암호 없음 또는 원하는 이름을 입력합니다.|
|메타데이터 URL|https://api.bloksec.io/oidc/.well-known/openid-configuration|         
|클라이언트 ID|**1부** 에서 캡처한 BlokSec 관리 UI의 애플리케이션 ID|
|클라이언트 암호|**1부** 에서 캡처한 BlokSec 관리 UI의 애플리케이션 비밀|
|범위|OpenID 이메일 프로필|
|응답 형식|코드|
|도메인 힌트|yuID|

1. **확인** 을 선택합니다.

1. **이 ID 공급자의 클레임 매핑** 을 선택합니다.

1. 양식을 작성하여 ID 공급자를 매핑합니다.

|속성  |값  |
|:---------|:---------|
|사용자 ID|sub|
|표시 이름|name|
|이름|given_name|
|Surname|family_name|
|메일|이메일|

1. **저장** 을 선택하여 새 OIDC ID 공급자에 대한 설정을 완료합니다.  

### <a name="part-4---user-registration"></a>4부 - 사용자 등록

1. 이전에 제공된 자격 증명을 사용하여 BlokSec 관리 콘솔에 로그인합니다.

1. 이전에 만든 Azure AD B2C 애플리케이션으로 이동합니다. 오른쪽 상단에서 기어 아이콘을 선택한 다음 **계정 만들기** 를 선택합니다.  

1. 계정 만들기 양식에 사용자 정보를 입력하고 계정 이름을 기록한 다음 **제출** 을 선택합니다.  

사용자는 제공한 이메일 주소로 **계정 등록 이메일** 을 받게 됩니다. 사용자가 BlokSec yuID 앱이 설치된 모바일 디바이스에서 등록 링크를 따르도록 합니다.

### <a name="part-5---create-a-user-flow-policy"></a>5부 - 사용자 흐름 정책 만들기

이제 B2C ID 공급자 내에 나열된 새 OIDC ID 공급자로 BlokSec가 표시됩니다.  

1. Azure AD B2C 테넌트에서, **정책** 아래 **사용자 흐름** 을 선택합니다.  

1. **새 사용자 흐름** 을 선택합니다.

1. **가입 및 로그인** > **버전** > **만들기** 를 선택합니다.

1. 정책의 **이름** 을 입력합니다.

1. ID 공급자 섹션에서 새로 만든 BlokSec ID 공급자를 선택합니다.  

1. 이메일 및 암호 기반 인증을 사용하지 않도록 설정하려면 로컬 계정에 대해 **없음** 을 선택합니다.

1. **사용자 흐름 실행** 을 선택합니다.

1. 양식에서 회신 URL을 입력합니다(예: https://jwt.ms ).

1. 브라우저가 BlokSec 로그인 페이지로 리디렉션됩니다. 사용자 등록 시 등록한 계정 이름을 입력합니다. 사용자는 BlokSec yuID 애플리케이션이 설치된 모바일 디바이스로 푸시 알림을 받게 됩니다. 알림을 열면 사용자에게 인증 질문이 표시됩니다.

1. 인증 질문이 수락되면 브라우저가 사용자를 회신 URL로 리디렉션합니다.  

## <a name="next-steps"></a>다음 단계 

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end

::: zone pivot="b2c-custom-policy"

>[!NOTE]
>Azure Active Directory B2C에서 [**사용자 지정 정책**](./user-flow-overview.md)은 주로 복잡한 시나리오를 해결하기 위해 설계되었습니다. 대부분의 시나리오에서 기본 제공 [**사용자 흐름**](./user-flow-overview.md)을 사용하는 것이 좋습니다.

### <a name="part-2---create-a-policy-key"></a>2부 - 정책 키 만들기

이전에 Azure AD B2C 테넌트에서 기록했던 클라이언트 암호를 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **모든 서비스** 를 선택하고 **Azure AD B2C** 를 검색하여 선택합니다.
1. 개요 페이지에서 **ID 경험 프레임워크** 를 선택합니다.
1. **정책 키**, **추가** 를 차례로 선택합니다.
1. **옵션** 으로는 `Manual`을 선택합니다.
1. 정책 키의 **이름** 을 입력합니다. 예들 들어 `BlokSecAppSecret`입니다. `B2C_1A_` 접두사가 키의 이름에 자동으로 추가됩니다.
1. 이전에 기록해 두었던 클라이언트 비밀을 **비밀** 에 입력합니다.
1. **키 사용** 에서 `Signature`를 선택합니다.
1. **만들기** 를 선택합니다.

### <a name="part-3---configure-bloksec-as-an-identity-provider"></a>3부 - BlokSec를 ID 공급자로 구성

사용자가 BlokSec 탈중앙화 ID를 사용하여 로그인할 수 있도록 하려면 Azure AD B2C가 엔드포인트를 통해 통신할 수 있는 클레임 공급자로 BlokSec를 정의해야 합니다. 엔드포인트는 Azure AD B2C에서 특정 사용자가 디바이스에서 사용 가능한 지문 또는 얼굴 검사와 같은 생체 인식을 사용하여 인증했는지 확인하는 데 사용되는 클레임 집합을 제공하여 사용자 ID를 증명합니다.

정책의 확장 파일에서 **ClaimsProvider** 요소를 추가하여 BlokSec를 클레임 공급자로 정의할 수 있습니다.

1. `TrustFrameworkExtensions.xml` 파일을 엽니다.

2. **ClaimsProviders** 요소를 찾습니다. 없는 경우 루트 요소 아래에 이 요소를 추가합니다.

3. 다음과 같이 새 **ClaimsProvider** 를 추가합니다.

    ```xml
    <ClaimsProvider>
      <Domain>bloksec</Domain>
      <DisplayName>BlokSec</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="BlokSec-OpenIdConnect">
          <DisplayName>BlokSec</DisplayName>
          <Description>Login with your BlokSec decentriled identity</Description>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="METADATA">https://api.bloksec.io/oidc/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the BlokSec Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <Item Key="ValidTokenIssuerPrefixes">https://api.bloksec.io/oidc</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_BlokSecAppSecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. **client_id** 를 애플리케이션 등록의 애플리케이션 ID로 설정합니다.

5. 파일을 저장합니다.

### <a name="part-4---add-a-user-journey"></a>4부 - 사용자 경험 추가

이 시점에서 ID 공급자가 설정되었지만 로그인 페이지에서 아직 사용할 수는 없습니다. 자체 사용자 지정 사용자 경험이 없는 경우 기존 템플릿 사용자 경험의 복제본을 만듭니다. 그렇지 않으면 다음 단계를 계속 진행합니다.  

1. 시작 팩에서 `TrustFrameworkBase.xml` 파일을 엽니다.

2. ID=`SignUpOrSignIn`이 포함된 **UserJourneys** 요소를 찾아서 전체 콘텐츠를 복사합니다.

3. `TrustFrameworkExtensions.xml`을 열고 **UserJourneys** 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.

4. 이전 단계에서 복사한 **UserJourney** 요소의 전체 콘텐츠를 **UserJourneys** 요소의 자식으로 붙여넣습니다.

5. 사용자 경험 ID의 이름을 바꿉니다. 예: ID=`CustomSignUpSignIn`.  

### <a name="part-5---add-the-identity-provider-to-a-user-journey"></a>5부 - 사용자 경험에 ID 공급자 추가

이제 사용자 경험이 있으므로 사용자 경험에 새 ID 공급자를 추가합니다. 먼저 로그인 단추를 추가한 다음, 단추를 작업에 연결합니다. 이 작업은 이전에 만든 기술 프로필입니다.  

1. 사용자 경험의 Type=`CombinedSignInAndSignUp` 또는 Type=`ClaimsProviderSelection`을 포함하는 오케스트레이션 단계 요소를 찾습니다. 일반적으로 첫 번째 오케스트레이션 단계입니다. **ClaimsProviderSelections** 요소에는 사용자가 로그인 할 수 있는 ID 공급자의 목록이 포함되어 있습니다. 요소의 순서는 사용자에게 표시되는 로그인 단추의 순서를 제어합니다. **ClaimsProviderSelection** XML 요소를 추가합니다. **TargetClaimsExchangeId** 의 값을 친숙한 이름으로 설정합니다.

2. 다음 오케스트레이션 단계에서 **ClaimsExchange** 요소를 추가합니다. **Id** 를 대상 클레임 교환 ID 값으로 설정합니다. **TechnicalProfileReferenceId** 값을 앞에서 만든 기술 프로필의 ID로 업데이트합니다.

다음 XML은 ID 공급자를 사용하여 사용자 경험의 처음 두 오케스트레이션 단계를 보여줍니다.

```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="BlokSecExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="BlokSecExchange" TechnicalProfileReferenceId="BlokSec-OpenIdConnect" />
  </ClaimsExchanges>
</OrchestrationStep>
```

### <a name="part-6---configure-the-relying-party-policy"></a>6부 - 신뢰 당사자 정책 구성

신뢰 당사자 정책(예: [SignUpSignIn.xml](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/blob/master/SocialAndLocalAccounts/SignUpOrSignin.xml))은 Azure AD B2C에서 실행할 사용자 경험을 지정합니다. 신뢰 당사자 내에서 **DefaultUserJourney** 요소를 찾습니다. ID 공급자를 추가한 사용자 경험 ID와 일치하도록 **ReferenceId** 를 업데이트합니다.

다음 예제에서는 `CustomSignUpOrSignIn` 사용자 경험에 대해 ReferenceId가 `CustomSignUpOrSignIn`으로 설정됩니다.  
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="part-7---upload-the-custom-policy"></a>7부 - 사용자 지정 정책 업로드

1. [Azure Portal](https://portal.azure.com/#home)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. [Azure Portal](https://portal.azure.com/#home)에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. 정책에서 **Identity Experience Framework** 를 선택합니다.
**사용자 지정 정책 업로드** 를 선택한 다음, 변경한 두 정책 파일을 확장 정책(예: `TrustFrameworkExtensions.xml`), 신뢰 당사자 정책(예: `SignUpSignIn.xml`) 순으로 업로드합니다.

### <a name="part-8---test-your-custom-policy"></a>8부 - 사용자 지정 정책 테스트

1. 신뢰 당사자 정책(예: `B2C_1A_signup_signin`)을 선택합니다.
1. **애플리케이션** 에서 [이전에 등록된](./tutorial-register-applications.md) 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **지금 실행** 단추를 선택합니다.
1. 등록 또는 로그인 페이지에서 **Google** 을 선택하여 Google 계정으로 로그인합니다.

로그인 프로세스가 성공하면 브라우저가 Azure AD B2C에서 반환된 토큰의 내용을 표시하는 `https://jwt.ms`로 리디렉션됩니다.

## <a name="next-steps"></a>다음 단계 

자세한 내용은 다음 문서를 참조하세요.

- [Azure AD B2C의 사용자 지정 정책](./custom-policy-overview.md)

- [Azure AD B2C의 사용자 지정 정책 시작하기](./tutorial-create-user-flows.md?pivots=b2c-custom-policy)

::: zone-end