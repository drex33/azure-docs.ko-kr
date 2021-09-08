---
title: Application Insights를 사용하여 사용자 지정 정책 문제 해결
titleSuffix: Azure AD B2C
description: Application Insights를 설정하여 사용자 지정 정책의 실행을 추적하는 방법
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 08/26/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b8ceea26ed7a5e58e890c4e313b00f1f4f37f4e7
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967801"
---
# <a name="collect-azure-active-directory-b2c-logs-with-application-insights"></a>Application Insights를 사용하여 Azure Active Directory B2C 로그 수집

이 문서에서는 사용자 지정 정책을 사용하여 문제를 진단할 수 있도록 Azure AD B2C(Active Directory B2C)에서 로그를 수집하는 단계를 설명합니다. Application Insights는 예외를 진단하고 애플리케이션 성능 문제를 시각화하는 방법을 제공합니다. Azure AD B2C에는 Application Insights로 데이터를 보내는 기능을 있습니다.

여기서 설명하는 자세한 활동 로그는 사용자 지정 정책을 개발하는 **동안에만** 사용하도록 설정해야 합니다.

> [!WARNING]
> 프로덕션 환경에서 `DeploymentMode`를 `Development`로 설정하지 않습니다. 로그는 ID 공급자와 주고 받는 모든 클레임을 수집합니다. 개발자가 Application Insights 로그에 수집된 개인 데이터를 담당하게 됩니다. 이러한 자세한 로그는 정책이 **개발 모드** 인 경우에만 수집됩니다.

## <a name="set-up-application-insights"></a>Application Insights 설정

아직 없는 경우 구독에 Application Insights의 인스턴스를 만듭니다.

> [!TIP]
> Application Insights의 단일 인스턴스를 여러 Azure AD B2C 테넌트에 사용할 수 있습니다. 그런 다음 쿼리에서 테넌트 또는 정책 이름을 기준으로 필터링할 수 있습니다. 자세한 내용은 [Application Insights 로그 보기](#see-the-logs-in-application-insights) 샘플을 참조하세요.

구독에서 Application Insights의 기존 인스턴스를 사용하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure 구독이 포함된 디렉터리(Azure AD B2C 디렉터리 제외)를 선택합니다.
1. 이전에 만든 Application Insights 리소스를 엽니다.
1. **개요** 페이지에서 **계측 키** 를 기록합니다.

구독에서 Application Insights의 인스턴스를 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. 상단 메뉴에서 **디렉터리 + 구독** 필터를 선택한 다음, Azure 구독이 포함된 디렉터리(Azure AD B2C 디렉터리 제외)를 선택합니다.
1. 왼쪽 탐색 메뉴에서 **리소스 만들기** 를 선택합니다.
1. **Application Insights** 를 검색하고 선택한 다음, **만들기** 를 선택합니다.
1. 양식을 작성하고 **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다.
1. 배포가 완료되면 **리소스로 이동** 을 선택합니다.
1. Application Insights 메뉴의 **구성** 에서 **속성** 을 선택합니다.
1. 이후 단계에서 사용할 **계측 키** 를 기록합니다.

## <a name="configure-the-custom-policy"></a>사용자 지정 정책 구성

1. RP(신뢰 당사자) 파일(예: *SignUpOrSignin.xml*)을 엽니다.
1. 다음 속성을 `<TrustFrameworkPolicy>` 요소에 추가합니다.

   ```xml
   DeploymentMode="Development"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
   ```

1. 아직 존재하지 않는 경우 `<UserJourneyBehaviors>` 자식 노드를 `<RelyingParty>` 노드에 추가합니다. `<DefaultUserJourney ReferenceId="UserJourney Id" from your extensions policy, or equivalent (for example:SignUpOrSigninWithAAD" />` 이후에 위치해야 합니다.
1. 다음 노드를 `<UserJourneyBehaviors>` 요소의 자식으로 추가합니다. `{Your Application Insights Key}`를 이전에 기록한 Application Insights **계측 키** 로 바꿔야 합니다.

    ```xml
    <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
    ```

    * `DeveloperMode="true"`로 인해 Application Insights는 처리 파이프라인을 통해 원격 분석을 빠르게 수행합니다. 개발에는 좋지만 고용량일 경우에는 제한됩니다. 프로덕션에서는 `DeveloperMode`를 `false`로 설정합니다.
    * `ClientEnabled="true"`는 페이지 보기 및 클라이언트 쪽 오류를 추적하는 ApplicationInsights 클라이언트 쪽 스크립트를 보냅니다. Application Insights 포털의 **browserTimings** 테이블에서 볼 수 있습니다. `ClientEnabled= "true"`를 설정하여 페이지 스크립트에 Application Insights를 추가하면 페이지 로드 및 AJAX 호출의 타이밍, 개수, 브라우저 예외 및 AJAX 실패의 세부 정보와 사용자 및 세션 개수를 얻을 수 있습니다. 이 필드는 **선택 사항** 이며 기본적으로 `false`로 설정됩니다.
    * `ServerEnabled="true"`는 Application Insights에 기존 UserJourneyRecorder JSON을 사용자 지정 이벤트로 보냅니다.

    예를 들면 다음과 같습니다.

    ```xml
    <TrustFrameworkPolicy
      ...
      TenantId="fabrikamb2c.onmicrosoft.com"
      PolicyId="SignUpOrSignInWithAAD"
      DeploymentMode="Development"
      UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights"
    >
    ...
    <RelyingParty>
      <DefaultUserJourney ReferenceId="UserJourney ID from your extensions policy, or equivalent (for example: SignUpOrSigninWithAzureAD)" />
      <UserJourneyBehaviors>
        <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="true" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
      </UserJourneyBehaviors>
      ...
    </TrustFrameworkPolicy>
    ```

1. 정책을 업로드합니다.

## <a name="see-the-logs-in-application-insights"></a>Application Insights에서 로그를 참조하세요.

Application Insights에서 새 로그가 표시되기까지 짧은 지연 시간이 발생합니다(일반적으로 5분 미만).

1. [Azure Portal](https://portal.azure.com)에서 만든 Application Insights 리소스를 엽니다.
1. **개요** 페이지에서 **로그** 를 선택합니다.
1. Application Insights에서 새 탭을 엽니다.

로그를 참조하는 데 사용할 수는 쿼리 목록은 다음과 같습니다.

| 쿼리 | Description |
|---------------------|--------------------|
| `traces` | Azure AD B2C에서 생성된 모든 로그를 가져옵니다. |
| `traces | where timestamp > ago(1d)` | Azure AD B2C에서 최근까지 생성된 모든 로그를 가져옵니다.|
| `traces | where message contains "exception" | where timestamp > ago(2h)`|  지난 2시간 동안 오류가 발생한 모든 로그를 가져옵니다.|
| `traces | where customDimensions.Tenant == "contoso.onmicrosoft.com" and customDimensions.UserJourney  == "b2c_1a_signinandup"` | Azure AD B2C *contoso.onmicrosoft.com* 테넌트에 의해 생성된 모든 로그를 가져오고 사용자 경험은 *b2c_1a_signinandup* 입니다. |
| `traces | where customDimensions.CorrelationId == "00000000-0000-0000-0000-000000000000"`| 상관 관계 ID에 대해 Azure AD B2C에 의해 생성된 모든 로그를 가져옵니다. 상관 관계 ID를 사용자의 상관 관계 ID로 바꿉니다. | 

항목이 길어질 수 있습니다. 자세히 보기 위해 CSV로 내보냅니다.

쿼리에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)를 참조하세요.

## <a name="see-the-logs-in-vs-code-extension"></a>VS Code 확장의 로그를 참조하세요.

[VS Code](https://code.visualstudio.com/)용 [Azure AD B2C 확장](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)을 설치하는 것이 좋습니다. Azure AD B2C 확장을 사용하여 로그는 정책 이름, 상관 관계 ID(Application Insights는 상관 관계 ID의 첫 번째 숫자를 제공함) 및 로그 타임스탬프를 기반으로 구성됩니다. 이 기능을 사용하면 로컬 타임스탬프를 기반으로 관련 로그를 찾고 Azure AD B2C에 의해 실행되는 사용자 경험을 확인할 수 있습니다.

> [!NOTE]
> 커뮤니티는 ID 개발자에게 도움이 되는 Azure AD B2C에 대한 VS Code 확장을 개발했습니다. 이 확장은 Microsoft에서 지원되지 않으며 엄격하게 있는 그대로 제공됩니다.

### <a name="set-application-insights-api-access"></a>Application Insights API 액세스 설정

Application Insights를 설정하고 사용자 지정 정책을 구성한 후 Application Insights **API ID** 를 가져오고 **API 키** 를 만들어야 합니다. API ID와 API 키는 Azure AD B2C 확장에서 Application Insights 이벤트(원격 분석)를 읽는 데 사용됩니다. API 키는 암호처럼 관리해야 합니다. 공개되지 않도록 주의해야 합니다.

> [!NOTE]
> 이전에 만든 Application Insights 계측 키는 Azure AD B2C에서 Application Insights를 원격 분석을 보내는 데 사용됩니다. 계측 키는 VS Code 확장이 아닌 Azure AD B2C 정책에서만 사용합니다.

Application Insights ID 및 키를 가져오려면:

1. Azure Portal에서 애플리케이션에 대한 Application Insights 리소스를 엽니다.
1. **설정** 을 선택한 다음 **API 액세스** 를 선택합니다.
1. **애플리케이션 ID** 를 복사합니다.
1. **API 키 만들기** 를 선택합니다.
1. **원격 분석 읽기** 상자를 선택합니다.
1. API 키 만들기 블레이드를 닫기 전에 **키** 를 복사하여 안전한 곳에 저장합니다. 키를 분실한 경우 다른 키를 만들어야 합니다.

    ![API 액세스 키를 만드는 방법을 보여 주는 스크린샷](./media/troubleshoot-with-application-insights/application-insights-api-access.png)

### <a name="set-up-azure-ad-b2c-vs-code-extension"></a>Azure AD B2C VS Code 확장 설정

이제 Azure Application Insights API ID와 키가 있으므로 로그를 읽도록 VS Code 확장을 구성할 수 있습니다. Azure AD B2C VS Code 확장은 설정에 대해 두 가지 범위를 제공합니다.

- **사용자 전역 설정** - 사용자가 여는 모든 VS Code 인스턴스에 전역적으로 적용되는 설정입니다.
- **작업 영역 설정** - 작업 영역 내에 저장된 설정이며 작업 영역을 열 때만 적용됩니다(VS Code **폴더 열기** 사용).

1. **Azure AD B2C 추적** 탐색기에서 **설정** 아이콘을 클릭합니다.

    ![Application Insights 설정을 선택하는 방법을 보여 주는 스크린샷](./media/troubleshoot-with-application-insights/app-insights-settings.png)

1. Azure Application Insights **ID** 와 **키** 를 제공합니다.
1. 페이지 맨 아래에 있는 **저장**

설정을 저장하면 Application Insights 로그가 **Azure AD B2C 추적(App insights)** 창에 표시됩니다.

![Azure Application Insights 추적을 제공하는 vscode용 Azure AD B2C 확장의 스크린샷](./media/troubleshoot-with-application-insights/vscode-extension-application-insights-trace.png)


## <a name="configure-application-insights-in-production"></a>프로덕션에서 Application Insights 구성

프로덕션 환경 성능과 사용자 환경을 개선하려면 중요하지 않은 메시지를 무시하도록 정책을 구성하는 것이 중요합니다. 프로덕션 환경에서 다음 구성을 사용합니다. 

1. [TrustFrameworkPolicy](trustframeworkpolicy.md)의 `DeploymentMode` 특성을 `Production`으로 설정합니다. 

   ```xml
   <TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0"
   TenantId="yourtenant.onmicrosoft.com"
   PolicyId="B2C_1A_signup_signin"
   PublicPolicyUri="http://yourtenant.onmicrosoft.com/B2C_1A_signup_signin"
   DeploymentMode="Production"
   UserJourneyRecorderEndpoint="urn:journeyrecorder:applicationinsights">
   ```

1. [JourneyInsights](relyingparty.md#journeyinsights)의 `DeveloperMode`를 `false`로 설정합니다.

   ```xml
   <UserJourneyBehaviors>
     <JourneyInsights TelemetryEngine="ApplicationInsights" InstrumentationKey="{Your Application Insights Key}" DeveloperMode="false" ClientEnabled="false" ServerEnabled="true" TelemetryVersion="1.0.0" />
   </UserJourneyBehaviors>
   ```
   
1. 정책을 업로드하고 테스트합니다.



## <a name="next-steps"></a>다음 단계

- [Azure AD B2C 사용자 지정 정책 문제 해결](troubleshoot-custom-policies.md)에 대해 알아봅니다.
