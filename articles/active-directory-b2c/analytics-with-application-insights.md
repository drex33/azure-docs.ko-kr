---
title: Application Insights를 사용하여 사용자 동작 추적
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 경험에서 Application Insights의 이벤트 로그를 사용하도록 설정하는 방법에 대해 알아봅니다.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.topic: how-to
ms.workload: identity
ms.date: 01/29/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a296babaafdee7939d462a356b9a3087e5e8990f
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122777777"
---
# <a name="track-user-behavior-in-azure-ad-b2c-by-using-application-insights"></a>Application Insights를 사용하여 Azure AD B2C에서 사용자 동작 추적

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-user-flow"

[!INCLUDE [active-directory-b2c-limited-to-custom-policy](../../includes/active-directory-b2c-limited-to-custom-policy.md)]

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure Active Directory B2C(Azure AD B2C)에서 Azure AD B2C에 제공된 계측 키를 사용하여 [Application Insights](../azure-monitor/app/app-insights-overview.md)에 직접 이벤트 데이터를 보낼 수 있습니다. Application Insights 기술 프로필을 사용하면 사용자 경험에 대한 상세하고 사용자 지정된 이벤트 로그를 얻을 수 있습니다.

- 사용자 동작에 대한 정보를 얻습니다.
- 개발에서 또는 프로덕션 환경에서 사용자 고유의 정책 문제를 해결합니다.
- 성능을 측정합니다.
- Application Insights에서 알림을 만듭니다.

## <a name="overview"></a>개요

사용자 지정 이벤트 로그를 사용하도록 설정하려면 Application Insights 기술 프로필을 추가합니다. 기술 프로필에서 Application Insights 계측 키, 이벤트 이름 및 기록할 클레임을 정의합니다. 이벤트를 게시하려면 [사용자 경험](userjourneys.md)에서 기술 프로필을 오케스트레이션 단계로 추가합니다.

Application Insights를 사용하는 경우 다음 사항을 고려하세요.

- Application Insights에서 새 로그를 사용하기 전까지 일반적으로 5분 이내인 짧은 지연이 발생합니다.
- Azure AD B2C를 사용하여 기록할 클레임을 선택할 수 있습니다. 개인 데이터가 포함된 클레임은 포함하지 마세요.
- 사용자 세션을 기록하려면 상관 관계 ID를 사용하여 이벤트를 통합할 수 있습니다.
- [사용자 경험](userjourneys.md) 또는 [하위 경험](subjourneys.md)에서 직접 Application Insights 기술 프로필을 호출합니다. Application Insights 기술 프로필을 [유효성 검사 기술 프로필](validation-technical-profile.md)로 사용하지 마세요.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites-custom-policy](../../includes/active-directory-b2c-customization-prerequisites-custom-policy.md)]

## <a name="create-an-application-insights-resource"></a>Application Insights 리소스 만들기

Application Insights를 Azure AD B2C와 함께 사용하는 경우 리소스를 만들고 계측 키를 가져오기만 하면 됩니다. 자세한 내용은 [Application Insights 리소스 만들기](../azure-monitor/app/create-new-resource.md)를 참조하세요.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 디렉터리가 아니라 Azure AD 구독이 있는 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 구독이 있는 Azure AD 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal의 왼쪽 상단 모서리에서 **리소스 만들기** 를 선택하고 **Application Insights** 를 검색하여 선택합니다.
1. **만들기** 를 선택합니다.
1. **이름** 에 리소스 이름을 입력합니다.
1. **애플리케이션 유형** 에서 **ASP.NET 웹 애플리케이션** 을 선택합니다.
1. **리소스 그룹**: 기존 그룹을 선택하거나 새 리소스 그룹의 이름을 입력합니다.
1. **만들기** 를 선택합니다.
1. 새 Application Insights 리소스를 열고 **Essentials** 를 확장한 다음 계측 키를 복사합니다.

![Application Insights 개요 탭의 계측 키를 보여 주는 스크린샷](./media/analytics-with-application-insights/app-insights.png)

## <a name="define-claims"></a>클레임 정의

클레임은 Azure AD B2C 정책 실행 중에 데이터의 임시 스토리지를 제공합니다. [ClaimsSchema 요소](claimsschema.md)에서 클레임을 선언합니다.

1. 정책의 확장 파일을 엽니다. 파일은 `SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 과 유사할 수 있습니다.
1. [BuildingBlocks](buildingblocks.md) 요소를 검색합니다. 요소가 보이지 않으면 추가하세요.
1. **ClaimsSchema** 요소를 찾습니다. 요소가 보이지 않으면 추가하세요.
1. **ClaimsSchema** 요소에 다음 클레임을 추가합니다.

   ```xml
   <ClaimType Id="EventType">
     <DisplayName>Event type</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="EventTimestamp">
     <DisplayName>Event timestamp</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="PolicyId">
     <DisplayName>Policy Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="Culture">
     <DisplayName>Culture ID</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="CorrelationId">
     <DisplayName>Correlation Id</DisplayName>
     <DataType>string</DataType>
   </ClaimType>
   <ClaimType Id="federatedUser">
     <DisplayName>Federated user</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   <ClaimType Id="parsedDomain">
     <DisplayName>Domain name</DisplayName>
     <DataType>string</DataType>
     <UserHelpText>The domain portion of the email address.</UserHelpText>
   </ClaimType>
   <ClaimType Id="userInLocalDirectory">
     <DisplayName>userInLocalDirectory</DisplayName>
     <DataType>boolean</DataType>
   </ClaimType>
   ```

## <a name="add-new-technical-profiles"></a>새 기술 프로필 추가

기술 프로필은 사용자 지정 정책에서 함수로 간주될 수 있습니다. 해당 함수는 [기술 프로필 포함](technicalprofiles.md#include-technical-profile) 방법을 사용합니다. 여기서 기술 프로필은 다른 기술 프로필을 포함하고 설정을 변경하거나 새 기능을 추가합니다. 다음 테이블은 세션을 열고 이벤트를 게시하는 데 사용되는 기술 프로필을 정의합니다.

| 기술 프로필 | Task |
| ----------------- | -----|
| AppInsights-Common | 일반적인 구성이 포함된 일반 기술 프로필입니다. 여기에는 Application Insights 계측 키, 기록할 클레임 컬렉션, 개발자 모드가 포함됩니다. 다른 기술 프로필은 일반 기술 프로필을 포함하고 이벤트 이름과 같은 클레임을 추가합니다. |
| AppInsights-SignInRequest | 로그인 요청이 수신되었을 때 클레임 집합을 사용하여 **SignInRequest** 이벤트를 기록합니다. |
| AppInsights-UserSignUp | 사용자가 등록 또는 로그인 경험에서 등록 옵션을 트리거할 때 **UserSignUp** 이벤트를 기록합니다. |
| AppInsights-SignInComplete | 토큰이 신뢰 당사자 애플리케이션에 전송되었을 때 인증이 성공하면 **SignInComplete** 이벤트를 기록합니다. |

스타터 팩에서 *TrustFrameworkExtensions.xml* 파일을 엽니다. **ClaimsProvider** 요소에 기술 프로필을 추가합니다.

```xml
<ClaimsProvider>
  <DisplayName>Application Insights</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AppInsights-Common">
      <DisplayName>Application Insights</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.Insights.AzureApplicationInsightsProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <!-- The ApplicationInsights instrumentation key, which you use for logging the events -->
        <Item Key="InstrumentationKey">xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx</Item>
        <Item Key="DeveloperMode">false</Item>
        <Item Key="DisableTelemetry ">false</Item>
      </Metadata>
      <InputClaims>
        <!-- Properties of an event are added through the syntax {property:NAME}, where NAME is the property being added to the event. DefaultValue can be either a static value or a value that's resolved by one of the supported DefaultClaimResolvers. -->
        <InputClaim ClaimTypeReferenceId="EventTimestamp" PartnerClaimType="{property:EventTimestamp}" DefaultValue="{Context:DateTimeInUtc}" />
        <InputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="{property:TenantId}" DefaultValue="{Policy:TrustFrameworkTenantId}" />
        <InputClaim ClaimTypeReferenceId="PolicyId" PartnerClaimType="{property:Policy}" DefaultValue="{Policy:PolicyId}" />
        <InputClaim ClaimTypeReferenceId="CorrelationId" PartnerClaimType="{property:CorrelationId}" DefaultValue="{Context:CorrelationId}" />
        <InputClaim ClaimTypeReferenceId="Culture" PartnerClaimType="{property:Culture}" DefaultValue="{Culture:RFC5646}" />
      </InputClaims>
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-SignInRequest">
      <InputClaims>
        <!-- An input claim with a PartnerClaimType="eventName" is required. This is used by the AzureApplicationInsightsProvider to create an event with the specified value. -->
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInRequest" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>

    <TechnicalProfile Id="AppInsights-UserSignUp">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="UserSignUp" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
    
    <TechnicalProfile Id="AppInsights-SignInComplete">
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="SignInComplete" />
        <InputClaim ClaimTypeReferenceId="federatedUser" PartnerClaimType="{property:FederatedUser}" DefaultValue="false" />
        <InputClaim ClaimTypeReferenceId="parsedDomain" PartnerClaimType="{property:FederationPartner}" DefaultValue="Not Applicable" />
        <InputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="{property:IDP}" DefaultValue="Local" />
      </InputClaims>
      <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

> [!IMPORTANT]
> `AppInsights-Common` 기술 프로필의 계측 키를 Application Insights 리소스에서 제공하는 GUID로 변경합니다.

## <a name="add-the-technical-profiles-as-orchestration-steps"></a>오케스트레이션 단계로 기술 프로필 추가

기술 프로필을 참조하는 새 오케스트레이션 단계를 추가합니다.

> [!IMPORTANT]
> 새 오케스트레이션 단계를 추가한 후, 1부터 N까지 건너뛰지 말고 순차적으로 단계 번호를 다시 매깁니다.

1. 두 번째 오케스트레이션 단계로 `AppInsights-SignInRequest`를 호출합니다. 이 단계에서는 등록 또는 로그인 요청을 받았음을 추적합니다.

   ```xml
   <!-- Track that we have received a sign in request -->
   <OrchestrationStep Order="2" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInRequest" TechnicalProfileReferenceId="AppInsights-SignInRequest" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. `SendClaims` 오케스트레이션 단계 전에 `AppInsights-UserSignup`을 호출하는 새 단계를 추가합니다. 사용자가 등록 혹은 로그인 경험에서 등록 단추를 선택할 때 트리거됩니다.

   ```xml
   <!-- Handles the user selecting the sign-up link in the local account sign-in page -->
   <OrchestrationStep Order="8" Type="ClaimsExchange">
     <Preconditions>
       <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
         <Value>newUser</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
       <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
         <Value>newUser</Value>
         <Value>false</Value>
         <Action>SkipThisOrchestrationStep</Action>
       </Precondition>
     </Preconditions>
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackUserSignUp" TechnicalProfileReferenceId="AppInsights-UserSignup" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

1. `SendClaims` 오케스트레이션 단계 후에 `AppInsights-SignInComplete`를 호출합니다. 이 단계는 성공적으로 완료된 경험을 나타냅니다.

   ```xml
   <!-- Track that we have successfully sent a token -->
   <OrchestrationStep Order="10" Type="ClaimsExchange">
     <ClaimsExchanges>
       <ClaimsExchange Id="TrackSignInComplete" TechnicalProfileReferenceId="AppInsights-SignInComplete" />
     </ClaimsExchanges>
   </OrchestrationStep>
   ```

## <a name="upload-your-file-run-the-policy-and-view-events"></a>파일을 업로드, 정책 실행 및 이벤트 보기

*TrustFrameworkExtensions.xml* 파일을 저장하고 업로드합니다. 그런 다음, 애플리케이션에서 신뢰 당사자 정책을 호출하거나 Azure Portal에서 **지금 실행** 을 사용합니다. Application Insights에서 이벤트를 사용할 수 있을 때까지 기다립니다.

1. Azure Active Directory 테넌트에서 **Application Insights** 리소스를 엽니다.
1. **사용량** 를 선택한 다음 **이벤트** 를 선택합니다.
1. **During** 을 **지난 1시간** 으로 설정하고 **By** 를 **3분** 으로 설정합니다. 결과를 보려면 창을 새로 고침해야 할 수도 있습니다.

![Application Insights 이벤트 통계를 보여 주는 스크린샷](./media/analytics-with-application-insights/app-ins-graphic.png)

## <a name="collect-more-data"></a>더 많은 데이터 수집

비즈니스 요구에 맞게 더 많은 클레임을 기록하는 것이 좋습니다. 클레임을 추가하려면 먼저 [클레임을 정의](#define-claims)한 다음, 클레임을 입력 클레임 컬렉션에 추가합니다. **AppInsights-Common** 기술 프로필에 추가하는 클레임은 모든 이벤트에 표시됩니다. 특정 기술 프로필에 추가하는 클레임은 해당 이벤트에만 표시됩니다. 입력 클레임 요소에는 다음 특성이 포함됩니다.

- **ClaimTypeReferenceId** 는 클레임 유형에 대한 참조입니다.
- **PartnerClaimType** 은 Azure Insights에 나타나는 속성의 이름입니다. `{property:NAME}` 구문을 사용합니다. 단, `NAME`은 이벤트에 추가하는 속성입니다.
- **DefaultValue** 는 이벤트 이름과 같이, 기록될 미리 정의된 값입니다. 사용자 경험에 사용되는 클레임이 비어 있으면 기본값이 사용됩니다. 예를 들어 `identityProvider` 클레임은 Facebook과 같은 페더레이션 기술 프로필에 의해 설정됩니다. 클레임이 비어 있으면 로컬 계정으로 로그인한 사용자를 나타냅니다. 따라서 기본값은 **Local** 로 설정됩니다. 애플리케이션 ID 또는 사용자 IP 주소와 같은 컨텍스트 값을 사용하여 [클레임 확인자](claim-resolver-overview.md)를 기록할 수도 있습니다.

### <a name="manipulate-claims"></a>클레임 조작

[입력 클레임 변환](custom-policy-overview.md#manipulating-your-claims)을 사용하여 입력 클레임을 수정하거나 Application Insights로 보내기 전에 새 항목을 생성할 수 있습니다. 다음 예제에서 기술 프로필에는 `CheckIsAdmin` 입력 클레임 변환이 포함됩니다.

```xml
<TechnicalProfile Id="AppInsights-SignInComplete">
  <InputClaimsTransformations>  
    <InputClaimsTransformation ReferenceId="CheckIsAdmin" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="isAdmin" PartnerClaimType="{property:IsAdmin}"  />
    ...
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

### <a name="add-events"></a>이벤트 추가

이벤트를 추가하려면 `AppInsights-Common` 기술 프로필을 포함하는 새 기술 프로필을 만듭니다. 그다음 새 기술 프로필을 [사용자 경험](custom-policy-overview.md#orchestration-steps)에 오케스트레이션 단계로 추가합니다. 준비가 되면 [Precondition](userjourneys.md#preconditions) 요소를 사용하여 이벤트를 트리거합니다. 예를 들어 사용자가 다단계 인증을 통해 실행하는 경우에만 이벤트를 보고합니다.

```xml
<TechnicalProfile Id="AppInsights-MFA-Completed">
  <InputClaims>
     <InputClaim ClaimTypeReferenceId="EventType" PartnerClaimType="eventName" DefaultValue="MFA-Completed" />
  </InputClaims>
  <IncludeTechnicalProfile ReferenceId="AppInsights-Common" />
</TechnicalProfile>
```

>[!Important]
>사용자 경험에 이벤트를 추가하는 경우 오케스트레이션 단계의 번호를 순차적으로 다시 매깁니다.

```xml
<OrchestrationStep Order="8" Type="ClaimsExchange">
  <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>isActiveMFASession</Value>
    <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="TrackUserMfaCompleted" TechnicalProfileReferenceId="AppInsights-MFA-Completed" />
  </ClaimsExchanges>
</OrchestrationStep>
```

## <a name="enable-developer-mode"></a>개발자 모드를 사용하도록 설정

Application Insights를 사용하여 이벤트를 정의하는 경우 개발자 모드를 사용하도록 설정할지 여부를 지정할 수 있습니다. 개발자 모드는 이벤트가 버퍼링되는 방식을 제어합니다. 최소 이벤트 볼륨이 있는 개발 환경에서 개발자 모드를 사용하도록 설정하면 이벤트가 즉시 Application Insights로 전송됩니다. 기본값은 `false`입니다. 프로덕션 환경에서 개발자 모드를 사용하도록 설정하지 마세요.

개발자 모드를 사용하도록 설정하려면 `AppInsights-Common` 기술 프로필에서 `DeveloperMode` 메타데이터를 `true`로 변경합니다.

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DeveloperMode">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="disable-telemetry"></a>원격 분석 사용 안 함

Application Insights 로그를 사용하지 않도록 설정하려면 `AppInsights-Common` 기술 프로필에서 `DisableTelemetry` 메타데이터를 `true`로 변경합니다.

```xml
<TechnicalProfile Id="AppInsights-Common">
  <Metadata>
    ...
    <Item Key="DisableTelemetry">true</Item>
  </Metadata>
</TechnicalProfile>
```

## <a name="next-steps"></a>다음 단계

[Azure Application Insights를 사용하여 사용자 지정 KPI 대시보드를 만드는](../azure-monitor/app/tutorial-app-dashboards.md)방법을 알아봅니다.

::: zone-end