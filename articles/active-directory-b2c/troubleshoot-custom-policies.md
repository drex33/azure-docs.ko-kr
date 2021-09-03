---
title: Azure Active Directory B2C에서 사용자 지정 정책 문제 해결
description: Azure Active Directory B2C에서 사용자 지정 정책을 사용하는 경우 오류를 해결하는 방법을 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/25/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0a2f1e40b85eea5f02aec93a0cac97acc5dfeaf9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535912"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Azure AD B2C 사용자 지정 정책 문제 해결

Azure AD B2C(Azure Active Directory B2C) [사용자 지정 정책](custom-policy-overview.md)을 사용하는 경우 정책 언어 XML 형식 또는 런타임 문제가 발생할 수 있습니다. 이 문서에서는 문제를 검색하고 해결하는 데 도움이 되는 몇 가지 도구와 팁에 대해 설명합니다. 

이 문서에서는 Azure AD B2C 사용자 지정 정책 구성의 문제 해결을 중점적으로 다룹니다. 신뢰 당사자 애플리케이션 또는 ID 라이브러리는 다루지 않습니다.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Azure AD B2C 상관 관계 ID 개요

Azure AD B2C 상관 관계 ID는 권한 부여 요청에 연결된 고유 식별자 값입니다. 이 ID는 사용자가 수행하는 모든 오케스트레이션 단계를 통과합니다. 상관 관계 ID를 사용하여 다음을 수행할 수 있습니다.

- 애플리케이션에서 로그인 작업을 식별하고 정책의 성능을 추적합니다.
- 로그인 요청의 Azure Application Insights 로그를 찾습니다.
- REST API에 상관 관계 ID를 전달하고 이를 사용하여 로그인 흐름을 식별합니다. 

상관 관계 ID는 새 세션이 설정될 때마다 변경됩니다. 정책을 디버그할 때 기존 브라우저 탭을 닫거나 새 비공개 모드 브라우저를 열어야 합니다.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Azure AD B2C 상관 관계 ID 가져오기

Azure AD B2C 등록 또는 로그인 페이지에서 상관 관계 ID를 찾을 수 있습니다. 브라우저에서 **소스 보기** 를 선택합니다. 상관 관계가 페이지 맨 위에 주석으로 나타납니다.

![Azure AD B2C 로그인 페이지 소스 보기 스크린샷](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

상관 관계 ID를 복사한 다음, 로그인 흐름을 계속합니다. 상관 관계 ID를 사용하여 로그인 동작을 관찰합니다. 자세한 내용은 [Application Insights 문제 해결](#troubleshooting-with-application-insights)을 참조하세요.

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Azure AD B2C 상관 관계 ID 에코

Azure AD B2C 토큰에 상관 관계 ID를 포함할 수 있습니다. 상관 관계 ID를 포함하려면

1. 정책의 확장 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>입니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 존재하지 않는 경우 추가합니다.
1. [ClaimsSchema](claimsschema.md) 요소를 찾습니다. 요소가 존재하지 않는 경우 추가합니다.
1. **ClaimsSchema** 요소에 상관 관계 ID 클레임을 추가합니다.  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. 정책의 신뢰 당사자 파일을 엽니다. 예를 들어 <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> 파일입니다. 사용자 경험이 성공하면 출력 클레임이 토큰에 추가되고 애플리케이션으로 전송됩니다. 신뢰 당사자 섹션에서 기술 프로필 요소를 수정하여 `correlationId`를 출력 클레임으로 추가합니다.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Application Insights 문제 해결

사용자 지정 정책의 문제를 진단하려면 [Application Insights](troubleshoot-with-application-insights.md)를 사용합니다. Application Insights는 사용자 지정 정책 사용자 경험의 작업을 추적합니다. 또한 예외를 진단하고 Azure AD B2C와 기술 프로필에 정의된 다양한 클레임 공급자(예: ID 공급자, API 기반 서비스, Azure AD B2C 사용자 디렉터리 및 기타 서비스) 간 클레임 교환을 관찰하는 방법을 제공합니다.  

[VS Code](https://code.visualstudio.com/)용 [Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)을 설치하는 것이 좋습니다. Azure AD B2C 확장을 사용하면 로그가 정책 이름, 상관 관계 ID(Application Insights는 상관 관계 ID의 첫 번째 숫자를 나타냄) 및 로그 타임스탬프별로 구성됩니다. 이 기능을 사용하면 로컬 타임스탬프를 기반으로 관련 로그를 찾고 Azure AD B2C에서 실행된 사용자 경험을 확인할 수 있습니다. 

> [!NOTE]
> - Application Insights에 새 로그가 표시되기까지 짧은 지연 시간이 발생합니다(일반적으로 5분 미만).
> - 커뮤니티는 ID 개발자를 지원하기 위해 Azure AD B2C용 Visual Studio Code 확장을 개발했습니다. 이 확장은 Microsoft에서 지원되지 않으며 엄격하게 있는 그대로 제공됩니다.

단일 로그인 흐름에서 둘 이상의 Azure Application Insights 추적을 실행할 수 있습니다. 다음 스크린샷에서 *B2C_1A_signup_signin* 정책에는 세 개의 로그가 있습니다. 각 로그는 로그인 흐름의 일부를 나타냅니다.

다음 스크린샷에서는 Azure Application Insights 추적 탐색기가 있는 VS Code용 Azure AD B2C 확장을 보여 줍니다.

![Azure Application Insights 추적이 있는 VS Code용 Azure AD B2C 확장의 스크린샷](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>추적 로그 필터링

Azure AD B2C 추적 탐색기에 포커스를 둔 상태에서 상관 관계 ID의 첫 번째 숫자 또는 검색하려는 시간을 입력합니다. Azure AD B2C 추적 탐색기의 오른쪽 위에 필터 상자가 표시되어 지금까지 입력한 내용을 보여 주고 일치하는 추적 로그가 강조 표시됩니다.  

![Azure AD B2C 추적 탐색기 필터가 강조 표시된 Azure AD B2C 확장의 스크린샷](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

필터 상자를 마우스로 가리키고 **Enable Filter on Type**(형식을 기준으로 필터링 사용)을 선택하면 일치하는 추적 로그만 표시됩니다. **‘X’ 지우기 단추** 를 사용하여 필터를 지웁니다.

![Azure AD B2C 확장의 Azure AD B2C 추적 탐색기 필터 스크린샷](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Application Insights 추적 로그 세부 정보

Azure Application Insights 추적을 선택하면 확장에서 다음 정보가 포함된 **Application Insights details**(Application Insights 세부 정보) 창이 열립니다.

- **Application Insights** - 정책 이름, 상관 관계 ID, Azure Application Insights 추적 ID, 추적 타임스탬프 등 추적 로그에 대한 일반 정보입니다.
- **기술 프로필** - 추적 로그에 표시되는 기술 프로필 목록입니다.
- **클레임** - 추적 로그에 표시되는 클레임 목록(사전순)과 해당 값입니다. 클레임이 서로 다른 값으로 추적 로그에 여러 번 표시되는 경우 `=>` 기호는 최신 값을 지정합니다. 이러한 클레임을 검토하여 예상 클레임 값이 올바르게 설정되었는지 확인할 수 있습니다. 예를 들어 클레임 값을 확인하는 사전 조건이 있는 경우 클레임 섹션을 통해 예상 흐름이 다르게 동작하는 이유를 확인할 수 있습니다.
- **클레임 변환** - 추적 로그에 표시되는 클레임 변환의 목록입니다. 각 클레임 변환에는 입력 클레임, 입력 매개 변수 및 출력 클레임이 포함됩니다. 클레임 변환 섹션을 통해 전송되는 데이터 및 클레임 변환의 결과를 파악할 수 있습니다.
- **토큰** - 추적 로그에 표시되는 토큰의 목록입니다. 토큰에는 기본 페더레이션 OAuth 및 OpenId Connect ID 공급자의 토큰이 포함됩니다. 페더레이션 ID 공급자의 토큰은 ID 공급자가 Azure AD B2C에 대한 클레임을 반환하는 방법에 대한 세부 정보를 제공하므로 ID 공급자 기술 프로필 출력 클레임을 매핑할 수 있습니다. 
- **예외** - 추적 로그에 표시되는 예외 또는 치명적 오류 목록입니다.
- **Application Insights JSON** - Application Insights에서 반환되는 원시 데이터입니다.

다음 스크린샷에서는 Application Insights 추적 로그 정보 창의 예를 보여 줍니다.  

![Azure AD B2C 확장 Azure AD B2C 추적 보고서 스크린샷](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>JWT 토큰 문제 해결

JWT 토큰 유효성 검사 및 디버깅 목적으로 [https://jwt.ms](https://jwt.ms) 같은 사이트를 사용하여 JWT를 디코딩할 수 있습니다. 토큰 검사를 위해 `https://jwt.ms`로 리디렉션할 수 있는 테스트 애플리케이션을 만듭니다. 아직 수행하지 않은 경우 [웹 애플리케이션을 등록](tutorial-register-applications.md)하고 [ID 토큰 암시적 부여를 사용하도록 설정](tutorial-register-applications.md#enable-id-token-implicit-grant)합니다. 

![JWT 토큰 미리 보기의 스크린샷](./media/troubleshoot-custom-policies/jwt-token-preview.png)

**지금 실행** 및 `https://jwt.ms`를 사용하여 웹 또는 모바일 애플리케이션과 별도로 정책을 테스트합니다. 이 웹 사이트는 신뢰 당사자 애플리케이션처럼 작동합니다. Azure AD B2C 정책에 의해 생성된 JWT(JSON Web Token)의 내용을 표시합니다.

## <a name="troubleshoot-saml-protocol"></a>SAML 프로토콜 문제 해결

서비스 공급자와의 통합을 구성하고 디버그하는 데 도움이 되도록 Chrome용 [SAML DevTools 확장](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio), FireFox용 [SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) 또는 [Edge 또는 IE 개발자 도구](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957) 같은 SAML 프로토콜에 대한 브라우저 확장을 사용할 수 있습니다.

다음 스크린샷에서는 Azure AD B2C가 ID 공급자로 보내는 SAML 요청 및 SAML 응답을 SAML DevTools 확장에서 표시하는 방법을 보여 줍니다.

![SAML 프로토콜 추적 로그의 스크린샷](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

이러한 도구를 사용하여 애플리케이션과 Azure AD B2C 간의 통합을 확인할 수 있습니다. 예를 들면 다음과 같습니다.

- SAML 요청에 서명이 포함되어 있는지 여부를 확인하고 권한 부여 요청에 서명하는 데 사용되는 알고리즘을 결정합니다.
- Azure AD B2C가 오류 메시지를 반환하는지 확인합니다.
- 어설션 섹션이 암호화되어 있는지 확인합니다.
- ID 공급자를 반환하는 클레임의 이름을 가져옵니다.

[Fiddler](https://www.telerik.com/fiddler)를 사용하여 클라이언트 브라우저와 Azure AD B2C 간의 메시지 교환을 추적할 수도 있습니다. 오케스트레이션 단계에서 사용자 환경이 실패한 위치를 확인하는 데 도움이 될 수 있습니다.

## <a name="troubleshoot-policy-validity"></a>정책 유효성 문제 해결

정책 개발을 완료한 후에는 정책을 Azure AD B2C에 업로드합니다. 정책에 몇 가지 문제가 있을 수 있습니다. 다음 방법을 사용하여 정책 무결성/유효성을 보장합니다.

사용자 지정 정책 설정에서 가장 일반적인 오류는 형식이 잘못 지정된 XML입니다. 좋은 XML 편집기가 거의 필수입니다. 좋은 XML 편집기는 XML을 기본적으로 표시하고, 콘텐츠를 색상으로 구분하며, 일반적인 용어를 미리 채우고, XML 요소를 인덱싱된 상태로 유지하며, XML 스키마의 유효성을 검사할 수 있습니다.

[Visual Studio Code](https://code.visualstudio.com/)를 사용하는 것이 좋습니다. 그런 다음, XML 확장(예: [XML Language Support by Red Hat](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml)(Red Hat의 XML 언어 지원))을 설치합니다. XML 확장을 사용하면 사용자 지정 정책 [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) 스키마 정의를 사용하여 XML 파일을 업로드하기 전에 XML 스키마의 유효성을 검사할 수 있습니다.

XML 파일 연결 전략을 사용하면 다음 설정을 VS Code `settings.json` 파일에 추가하여 XSD에 XML 파일을 바인딩할 수 있습니다. 그러려면 다음을 수행합니다.

1. VS Code에서 **설정** 을 클릭합니다. 자세한 내용은 [User and Workspace Settings](https://code.visualstudio.com/docs/getstarted/settings)(사용자 및 작업 영역 설정)를 참조하세요.
1. **fileAssociations** 를 검색한 다음, **확장** 에서 **XML** 을 선택합니다.
1. **settings.json에서 편집** 을 선택합니다.

    ![VS Code XML 스키마 유효성 검사의 스크린샷](./media/troubleshoot-custom-policies/xml-validation.png)
1. settings.json에서 다음 JSON 코드를 추가합니다.

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

다음 예제에서는 XML 유효성 검사 오류를 보여 줍니다. 요소 이름 위로 마우스를 이동하면 확장에 필요한 요소가 나열됩니다.

![VS Code XML 스키마 유효성 검사 오류 표시기의 스크린샷](./media/troubleshoot-custom-policies/xml-validation-error.png)

다음 경우에 `DisplayName` 요소가 올바릅니다. 그러나 순서가 잘못되었습니다. `DisplayName`은 `Protocol` 요소 앞에 있어야 합니다. 문제를 해결하려면 `DisplayName` 요소 위에 마우스를 두고 요소의 올바른 순서로 이동합니다.

![VS Code XML 스키마 유효성 검사 순서 오류 스크린샷](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>정책 업로드 및 정책 유효성 검사

XML 정책 파일의 유효성 검사는 업로드 시 자동으로 수행됩니다. 대부분의 오류로 인해 업로드가 실패합니다. 유효성 검사에는 업로드하는 정책 파일이 포함됩니다. 또한 업로드 파일에서 참조하는 파일 체인(신뢰 당사자 정책 파일, 확장 파일 및 기본 파일)이 포함됩니다.

> [!TIP]
> Azure AD B2C는 신뢰 당사자 정책에 대해 추가 유효성 검사를 실행합니다. 정책에 문제가 있으면 확장 정책만 편집해도 신뢰 당사자 정책을 함께 업로드하는 것이 좋습니다. 

이 섹션에서는 일반적인 유효성 검사 오류 및 가능한 해결 방법에 대해 설명합니다.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>스키마 유효성 검사 오류가 있습니다. ...잘못된 자식 요소 ‘{name}’이(가) 있습니다.

정책에 잘못된 XML 요소가 포함되어 있거나 XML 요소가 유효하지만 순서가 잘못된 것 같습니다. 이 유형의 오류를 해결하려면 [정책 유효성 문제 해결](#troubleshoot-policy-validity) 섹션을 확인하세요.

### <a name="there-is-a-duplicate-key-sequence-number"></a>중복 키 시퀀스 ‘{number}’이(가) 있습니다. 

사용자 [경험](userjourneys.md) 또는 [하위 경험](subjourneys.md)은 순서대로 실행되는 순서가 지정된 오케스트레이션 단계 목록으로 구성됩니다. 경험을 변경한 후 1부터 N까지 정수를 건너뛰지 말고 순차적으로 단계 번호를 다시 매깁니다.

> [!TIP]
> [VS Code](https://code.visualstudio.com/)용 [Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)`(Shift+Ctrl+r)` 명령을 사용하여 정책에서 모든 사용자 경험 및 하위 경험 오케스트레이션 단계의 번호를 다시 매길 수 있습니다.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>...순서가 “{number}”인 단계가 필요하지만 찾을 수 없습니다...

이전 오류를 확인하세요.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>사용자 경험 “{name}”의 오케스트레이션 단계 순서 “{number}” ...다음에는 클레임 공급자 선택 단계가 오고 클레임 교환이어야 하지만 형식이...

`ClaimsProviderSelection` 및 `CombinedSignInAndSignUp` 형식의 오케스트레이션 단계에는 사용자가 선택할 수 있는 옵션 목록이 포함되어 있습니다. 그 다음에는 하나 이상의 클레임 교환이 있는 `ClaimsExchange` 형식이 와야 합니다.

다음과 같은 오케스트레이션 단계로 인해 이 유형의 오류가 발생합니다. 두 번째 오케스트레이션 단계는 `ClaimsProviderSelection`이 아닌 `ClaimsExchange` 형식이어야 합니다.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>...2개의 클레임 교환이 있는 {number}단계입니다. 사용할 수 있는 클레임 교환을 확인하려면 먼저 클레임 공급자를 선택해야 합니다.

이전 단계가 `ClaimsProviderSelection` 또는 `CombinedSignInAndSignUp` 형식이 아닌 경우 `ClaimsExchange` 형식의 오케스트레이션 단계에는 단일 `ClaimsExchange`가 있어야 합니다. 다음과 같은 오케스트레이션 단계로 인해 이 유형의 오류가 발생합니다. 여섯 번째 단계에는 두 개의 클레임 교환이 포함되어 있습니다. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

이 유형의 오류를 해결하려면 두 개의 오케스트레이션 단계를 사용합니다. 각 오케스트레이션 단계에는 클레임 교환이 하나씩 있습니다.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>중복 키 시퀀스 ‘{name}’이(가) 있습니다.

경험에 `Id`가 동일한 여러 `ClaimsExchange`가 있습니다. 다음과 같은 단계로 인해 이 유형의 오류가 발생합니다. *AADUserWrite* ID가 사용자 경험에 두 번 나타납니다.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

이 유형의 오류를 해결하려면 8번째 오케스트레이션 단계의 클레임 교환을 고유한 이름(예: *Call-REST-API*)으로 변경합니다.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>...ID가 “{claim name}”인 ClaimType을 참조하지만 정책이나 기본 정책에 이러한 요소가 포함되어 있지 않습니다.

이 유형의 오류는 정책이 [클레임 스키마](claimsschema.md)에 정의되지 않은 클레임을 참조할 때 발생합니다. 클레임은 정책의 파일 중 하나 이상에 정의되어야 합니다. 

예를 들어 *schoolId* 출력 클레임이 있는 기술 프로필이 있습니다. 그러나 출력 클레임 *schoolId* 가 정책이나 상위 정책에 선언되어 있지 않습니다.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

이 유형의 오류를 해결하려면 `ClaimTypeReferenceId` 값의 철자가 잘못되었거나 스키마에 없는지를 확인합니다. 클레임이 확장 정책에 정의되어 있지만 기본 정책에서도 사용되고 있습니다. 클레임이 사용되는 정책이나 상위 수준 정책에 정의되어 있는지 확인합니다.

클레임을 클레임 스키마에 추가하면 이 유형의 오류가 해결됩니다.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>...ID가 있는 ClaimsTransformation을 참조합니다...

이 오류의 원인은 클레임 오류의 원인과 유사합니다. 이전 오류를 확인하세요.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>사용자가 현재 ‘yourtenant.onmicrosoft.com’ 테넌트의 사용자로 로그인되어 있습니다...

업로드하려는 정책과 다른 테넌트의 계정으로 로그인합니다. 예를 들어 admin@contoso.onmicrosoft.com으로 로그인하는데 정책 `TenantId`가 `fabrikam.onmicrosoft.com`으로 설정되어 있습니다.

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- `<TrustFrameworkPolicy\>` 및 `<BasePolicy\>` 요소의 `TenantId` 값이 대상 Azure AD B2C 테넌트와 일치하는지 확인합니다.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>클레임 유형 “{name}”이(가) 신뢰 당사자 기술 프로필의 출력 클레임이지만 사용자 경험의 모든 단계에 있는 출력 클레임이 아닙니다...

신뢰 당사자 정책에서 출력 클레임을 추가했지만 출력 클레임이 사용자 경험의 모든 단계에 있는 출력 클레임이 아닙니다. Azure AD B2C가 클레임 모음에서 클레임 값을 읽을 수 없습니다.

다음 예제에서 *schoolId* 클레임은 신뢰 당사자 기술 프로필의 출력 클레임이지만 *SignUpOrSignIn* 사용자 경험의 모든 단계에 있는 출력 클레임은 아닙니다.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

이 유형의 오류를 해결하려면 출력 클레임이 하나 이상의 오케스트레이션 단계 기술 프로필 출력 클레임 컬렉션에 나타나는지 확인합니다. 사용자 경험에서 클레임을 출력할 수 없는 경우 신뢰 당사자 기술 프로필에서 빈 문자열과 같은 기본값을 설정합니다.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>입력 문자열의 형식이 잘못되었습니다.

잘못된 값 형식을 다른 형식의 클레임으로 설정합니다. 예를 들어 정수 클레임을 정의합니다.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

그런 다음, 문자열 값을 설정하려고 합니다.

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

이 유형의 오류를 해결하려면 `DefaultValue="0"` 같은 올바른 값을 설정해야 합니다.


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>“{name}” 테넌트에 ID가 “{name}”인 정책이 이미 있습니다. ID가 같은 또 다른 정책을 저장할 수 없습니다.

테넌트에 정책을 업로드하려고 하지만 이름이 같은 정책이 테넌트에 이미 업로드되어 있습니다. 

이 유형의 오류를 해결하려면 정책을 업로드할 때 **사용자 지정 정책이 이미 있는 경우 덮어쓰기** 확인란을 선택합니다.

![사용자 지정 정책이 이미 있는 경우 덮어쓰는 방법을 보여 주는 스크린샷](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>다음 단계

- [Application Insights를 사용하여 Azure Active Directory B2C 로그 수집](troubleshoot-with-application-insights.md) 방법을 알아봅니다.

