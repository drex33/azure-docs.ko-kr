---
title: Application Insights에 대한 Azure AD 인증(미리 보기)
description: Azure Active Directory(Azure AD) 인증을 사용하도록 설정하여 Application Insights 리소스에 인증된 원격 분석만 수집 확인하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: ab666ba0f7a57023d63e434de78a9b3859c238c8
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122607573"
---
# <a name="azure-ad-authentication-for-application-insights-preview"></a>Application Insights에 대한 Azure AD 인증(미리 보기)
이제 Application Insights는 Azure AD(Azure Active Directory) 기반 인증을 지원합니다. 이제 Azure AD를 사용하여 Application Insights 리소스에 인증된 원격 분석만 수집되도록 할 수 있습니다. 

일반적으로, 다양한 인증 시스템을 사용하면 복잡해질 수 있으며, 대규모로 자격 증명을 관리하기 어렵기 때문에 위험을 초래할 수 있습니다. 이제 로컬 인증을 옵트아웃하고 [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)를 사용하여 독점적으로 인증된 원격 분석과 [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md)를 Application Insights 리소스에 수집할 수 있습니다. 이 기능은 중요한 운영(경고/자동 크기 조정 등) 및 비즈니스 의사 결정을 모두 수행하는 데 사용되는 원격 분석의 보안 및 안정성을 향상시키는 단계입니다.

> [!IMPORTANT]
> Azure AD 인증은 현재 미리 보기로 제공되고 있습니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

공개 미리 보기에서 지원되지 않는 SDK/시나리오는 다음과 같습니다.
- [Application Insights Java 2.x SDK](java-2x-agent.md) – Azure AD 인증은 Application Insights Java Agent 3.2.0 이상에만 사용할 수 있습니다. 
- [ApplicationInsights JavaScript Web SDK](javascript.md). 
- [Application Insights OpenCensus Python SDK](opencensus-python.md) Python 버전 3.4 및 3.5.
- 프로덕션에는 [인증서/비밀 기반 AZURE AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md)를 사용하지 않는 것이 좋습니다. 대신 관리 ID를 사용합니다. 
- 기본적으로 App Service, VM/가상 머신 확장 집합, Azure Functions 등에 대해 코드리스 모니터링(언어의 경우)이 제공됩니다.
- [가용성 테스트](availability-overview.md).
- [프로파일러](profiler-overview.md).

## <a name="prerequisites-to-enable-azure-ad-authentication-ingestion"></a>Azure AD 인증 수집을 사용하도록 설정하기 위한 필수 구성 요소

- 다음을 사용한 경험:
    - [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md). 
    - [서비스 주체](../../active-directory/develop/howto-create-service-principal-portal.md).
    - [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md). 
- [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 사용하여 액세스 권한을 부여하는 리소스 그룹에 "Owner" 역할이 있습니다.

## <a name="configuring-and-enabling-azure-ad-based-authentication"></a>Azure AD 기반 인증 구성 및 사용 

1. ID가 없는 경우, 관리 ID 또는 서비스 주체를 사용하여 ID를 생성합니다.

    1. 관리 ID 사용(권장):

        [Azure 서비스에 대한 관리 ID를 설정합니다](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)(VM, App Service 등).

    1. 서비스 주체 사용(권장하지 않음):

        리소스에 액세스할 수 있는 Azure AD 애플리케이션 및 서비스 주체 생성 방법에 대한 자세한 정보는 [서비스 주체 생성](../../active-directory/develop/howto-create-service-principal-portal.md)을 참조하세요.

1. Azure Service에 역할을 할당합니다. 

    [Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md) 단계에 따라 대상 Application Insights 리소스의 "Monitoring Metrics Publisher" 역할을 원격 분석이 전송되는 Azure 리소스에 추가합니다. 

    > [!NOTE]
    > "Monitoring Metrics Publisher" 이라는 역할은 메트릭을 표시하지만 App Insights 리소스에 모든 원격 분석을 게시합니다.

1. 아래 언어별 구성 지침을 따릅니다.

### <a name="aspnet-and-net"></a>[ASP.NET 및 .NET](#tab/net)

> [!NOTE]
> Application Insights .NET SDK에서 Azure AD에 대한 지원은 [2.18-Beta3 버전](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.18.0-beta3)부터 포함됩니다.

Application Insights .NET SDK는 [Azure ID](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity#credential-classes)에서 제공하는 자격 증명 클래스를 지원합니다.

- 로컬 개발의 경우, `DefaultAzureCredential`이 권장됩니다.
- 시스템 할당 및 사용자 할당 관리 ID의 경우, `ManagedIdentityCredential`이 권장됩니다.
    - 시스템 할당의 경우, 매개 변수 없이 기본 생성자를 사용합니다.
    - 사용자 할당의 경우, 생성자에 clientId를 제공합니다.
- 서비스 주체의 경우, `ClientSecretCredential`이 권장됩니다. 
    - 생성자에 tenantId, clientId 및 clientSecret을 제공합니다.

다음은 .NET을 사용하여 `TelemetryConfiguration`을 수동으로 만들고 구성하는 예입니다.

```csharp
var config = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
}
var credential = new DefaultAzureCredential();
config.SetAzureTokenCredential(credential);

```

다음은 \ ASP.NET Core를 사용하여 `TelemetryConfiguration`을 구성하는 예입니다.
```csharp
services.Configure<TelemetryConfiguration>(config =>
{
       var credential = new DefaultAzureCredential();
       config.SetAzureTokenCredential(credential);
});
services.AddApplicationInsightsTelemetry(new ApplicationInsightsServiceOptions
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
});
```
### <a name="nodejs"></a>[Node.JS](#tab/nodejs)
 
> [!NOTE]
> Application Insights Node.JS에서 Azure AD에 대한 지원은 [2.1.0-beta.1 버전](https://www.npmjs.com/package/applicationinsights/v/2.1.0-beta.1)부터 포함됩니다.

Application Insights Node.JS는 [Azure ID](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/identity/identity#credential-classes)에서 제공하는 자격 증명 클래스를 지원합니다.

#### <a name="defaultazurecredential"></a>DefaultAzureCredential

```javascript
let appInsights = require("applicationinsights");
import { DefaultAzureCredential } from "@azure/identity"; 
 
const credential = new DefaultAzureCredential();
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

#### <a name="clientsecretcredential"></a>ClientSecretCredential

```javascript
let appInsights = require("applicationinsights");
import { ClientSecretCredential } from "@azure/identity"; 
 
const credential = new ClientSecretCredential(
    "<YOUR_TENANT_ID>",
    "<YOUR_CLIENT_ID>",
    "<YOUR_CLIENT_SECRET>"
  );
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

### <a name="java"></a>[Java](#tab/java)

> [!NOTE]
> Application Insights Java 에이전트의 Azure AD에 대한 지원은 [Java 3.2.0-BETA](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0-BETA)부터 포함됩니다. 

1. [Java 에이전트를 사용하여 애플리케이션을 구성합니다.](java-in-process-agent.md#quickstart)

    > [!IMPORTANT]
    > Java 에이전트를 사용하여 앱을 구성하는 동안 "IngestionEndpoint"를 포함하는 전체 연결 문자열을 사용합니다. 예: `InstrumentationKey=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX;IngestionEndpoint=https://XXXX.applicationinsights.azure.com/`.

    > [!NOTE]
    >  2\.X SDK에서 3.X Java 에이전트로 마이그레이션하는 방법에 대한 자세한 내용은 [Application Insights Java 2.X SDK에서 업그레이드](java-standalone-upgrade-from-2x.md)를 참조하세요.

1. 사용자가 사용하는 인증에 따라 구성 파일에 ApplicationInsights.jsjson 구성을 추가합니다. 관리 ID를 사용하는 것을 권장합니다.

#### <a name="system-assigned-managed-identity"></a>시스템이 할당한 관리 ID

다음은 Azure AD에서 인증에 시스템 할당 관리 ID를 사용하도록 Java 에이전트를 구성하는 방법에 대한 예입니다.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "SAMI" 
    } 
  } 
} 
```

#### <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

다음은 Azure AD에서 인증에 사용자 할당 관리 ID를 사용하도록 Java 에이전트를 구성하는 방법에 대한 예입니다.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "UAMI", 
      "clientId":"<USER-ASSIGNED MANAGED IDENTITY CLIENT ID>" 
    } 
  }  
} 
```
:::image type="content" source="media/azure-ad-authentication/user-assigned-managed-identity.png" alt-text="사용자 할당 관리 ID의 스크린샷." lightbox="media/azure-ad-authentication/user-assigned-managed-identity.png":::

#### <a name="client-secret"></a>클라이언트 암호

다음은 Azure AD에서 인증을 위해 서비스 주체를 사용하도록 Java 에이전트를 구성하는 방법에 대한 예입니다. 개발 중에 이 인증 유형을 사용하는 것이 좋습니다. 인증 기능을 추가하는 궁극적인 목표는 암호를 제거하는 것입니다.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint",
   "preview": { 
        "authentication": { 
        "enabled": true, 
        "type": "CLIENTSECRET", 
        "clientId":"<YOUR CLIENT ID>", 
        "clientSecret":"<YOUR CLIENT SECRET>", 
        "tenantId":"<YOUR TENANT ID>" 
    } 
  } 
} 
```
:::image type="content" source="media/azure-ad-authentication/client-secret-tenant-id.png" alt-text="TenantID 및 ClientID를 사용하는 클라이언트 암호의 스크린샷." lightbox="media/azure-ad-authentication/client-secret-tenant-id.png":::

:::image type="content" source="media/azure-ad-authentication/client-secret-cs.png" alt-text="클라이언트 암호를 사용하는 클라이언트 암호의 스크린샷." lightbox="media/azure-ad-authentication/client-secret-cs.png":::

### <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> Azure AD 인증은 Python v2.7, v3.6, v3.7에 대해서만 사용할 수 있습니다. Application Insights Opencensus Python SDK의 Azure AD에 대한 지원은 베타 버전 [opencensus-ext-azure 1.1b0](https://pypi.org/project/opencensus-ext-azure/1.1b0/)부터 제공됩니다.


적절한 [자격 증명](/python/api/overview/azure/identity-readme?view=azure-python#credentials)을 생성하고 Azure Monitor 내보내기 생성자에 전달합니다. 연결 문자열이 리소스의 계측 키 및 수집 엔드포인트로 설정되어 있는지 확인합니다.

다음은 Opencensus Azure Monitor 내보내기에서 지원하는 인증 유형입니다. 관리 ID는 프로덕션 환경에서 사용하는 것이 좋습니다.


#### <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential()
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential(client_id="<client-id>")
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="client-secret"></a>클라이언트 암호

```python
from azure.identity import ClientSecretCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tenant_id = "<tenant-id>"
client_id = "<client-id"
client_secret = "<client-secret>"

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...
```
---

## <a name="disable-local-authentication"></a>로컬 인증 사용 안 함

Azure AD 인증을 사용하는 경우, 로컬 인증을 사용하지 않도록 선택할 수 있습니다. 이렇게 하면 Azure AD에 의해 독점적으로 인증된 원격 분석을 수집하고 데이터 액세스에 영향을 줄 수 있습니다(예: API 키를 통해). 

Azure Portal, Azure Policy를 사용하거나 프로그래밍 방식으로 로컬 인증을 사용하지 않도록 설정할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1.  Application Insights 리소스에서 왼쪽 메뉴의 *구성* 제목 아래에 있는 **속성** 을 선택합니다. 그런 다음, 로컬 인증을 사용하도록 설정된 경우 **사용(변경하려면 클릭)** 을 선택합니다. 

    :::image type="content" source="./media/azure-ad-authentication/enabled.png" alt-text="선택 및 사용(변경하려면 클릭) 로컬 인증 단추 아래에 있는 *구성* 아래의 속성 스크린샷.":::

1. **사용 안 함** 을 선택하여 변경 사항을 적용합니다. 

    :::image type="content" source="./media/azure-ad-authentication/disable.png" alt-text="사용/사용 안 함 단추가 강조 표시된 로컬 인증의 스크린샷.":::

1. 리소스에서 로컬 인증을 사용하지 않도록 설정하면 **개요** 창에 해당 정보가 표시됩니다.

    :::image type="content" source="./media/azure-ad-authentication/overview.png" alt-text="사용 안 함(변경하려면 클릭)이 강조 표시된 개요 탭의 스크린샷.":::

### <a name="azure-policy"></a>Azure Policy 

'DisableLocalAuth'에 대한 Azure Policy는 사용자가 속성을 'true'로 설정하지 않고 새 Application Insights 리소스를 만드는 것을 허용하지 않습니다. 정책 이름은 'Application Insights 구성 요소가 AAD 인증을 수집하지 않도록 차단해야 합니다'입니다.

구독에 이 정책 정의를 적용하려면 [새 정책 할당을 만들고 정책을 할당](../../governance/policy/assign-policy-portal.md)합니다.

정책 템플릿 정의는 다음과 같습니다.
```JSON
{
    "properties": {
        "displayName": "Application Insights components should block non-AAD auth ingestion",
        "policyType": "BuiltIn",
        "mode": "Indexed",
        "description": "Improve Application Insights security by disabling log ingestion that are not AAD-based.",
        "metadata": {
            "version": "1.0.0",
            "category": "Monitoring"
        },
        "parameters": {
            "effect": {
                "type": "String",
                "metadata": {
                    "displayName": "Effect",
                    "description": "The effect determines what happens when the policy rule is evaluated to match"
                },
                "allowedValues": [
                    "audit",
                    "deny",
                    "disabled"
                ],
                "defaultValue": "audit"
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Insights/components"
                    },
                    {
                        "field": "Microsoft.Insights/components/DisableLocalAuth",
                        "notEquals": "true"                        
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        }
    }
}
```

### <a name="programmatic-enablement"></a>프로그래매틱 활성화 

`DisableLocalAuth` 속성은 Application Insights 리소스에서 로컬 인증을 사용하지 않도록 설정하는 데 사용됩니다. 이 속성은 `true`로 설정하면 모든 액세스에 Azure AD 인증을 사용해야 합니다.

다음은 로컬 인증을 사용하지 않도록 설정하여 작업 영역 기반 Application Insights 리소스를 만드는 데 사용할 수 있는 Azure Resource Manager 템플릿의 예입니다.

```JSON 
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        },
        "disableLocalAuth": {
            "type": "bool"
        }
     
    },
    "resources": [
        {
        "name": "[parameters('name')]",
        "type": "microsoft.insights/components",
        "location": "[parameters('regionId')]",
        "tags": "[parameters('tagsArray')]",
        "apiVersion": "2020-02-02-preview",
        "dependsOn": [],
        "properties": {
            "Application_Type": "[parameters('type')]",
            "Flow_Type": "Redfield",
            "Request_Source": "[parameters('requestSource')]",
            "WorkspaceResourceId": "[parameters('workspaceResourceId')]",
            "DisableLocalAuth": "[parameters('disableLocalAuth')]"
            }
    }
 ]
}

```

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 사용자가 지원 티켓을 제기하기 전에 문제를 해결하기 위해 취할 수 있는 고유한 문제 해결 시나리오 및 단계를 제공합니다. 

### <a name="ingestion-http-errors"></a>HTTP 오류 수집

수집 서비스에서 SDK 언어에 관계없이 특정 오류가 반환됩니다. Fiddler와 같은 도구를 사용하여 네트워크 트래픽을 수집할 수 있습니다. 연결 문자열에 설정된 IngestionEndpoint에 대한 트래픽을 필터링해야 합니다.

#### <a name="http11-400-authentication-not-support"></a>HTTP/1.1 400 인증이 지원되지 않음 

이는 Azure AD에 대해서만 Application Insights 리소스가 구성되어 있지만 SDK가 올바르게 구성되어 있지 않아 잘못된 API로 전송됨을 나타냅니다.

> [!NOTE]
>  "v2/track"는 Azure AD를 지원하지 않습니다. SDK가 올바르게 구성되면 원격 분석이 "v2.1/track"으로 전송됩니다.

다음 단계는 SDK 구성을 검토하는 것입니다.

#### <a name="http11-401-authorization-required"></a>HTTP/1.1 401 권한 필요

이는 SDK가 올바르게 구성되어 있지만 유효한 토큰을 가져올 수 없음을 나타냅니다. Azure Active Directory에 문제가 있을 수 있습니다.

다음 단계는 Azure ID에서 SDK 로그 또는 네트워크 오류에서 예외를 파악하는 것입니다.

#### <a name="http11-403-unauthorized"></a>HTTP/1.1 403 권한 없음 

이는 SDK가 Application Insights 리소스 또는 구독에 대한 권한이 없는 자격 증명으로 구성되어 있음을 나타냅니다.

다음 단계는 Application Insights 리소스의 액세스 제어를 검토하는 것입니다. "Monitoring Metrics Publisher" 역할이 부여된 자격 증명을 사용하여 SDK를 구성해야 합니다.

### <a name="language-specific-troubleshooting"></a>언어별 문제 해결

### <a name="aspnet-and-net"></a>[ASP.NET 및 .NET](#tab/net)

#### <a name="event-source"></a>이벤트 원본

Application Insights .NET SDK는 이벤트 원본을 사용하여 오류 로그를 내보냅니다. 이벤트 원본 로그 수집에 대한 자세한 내용은 [PerfView를 사용하여 데이터 수집 로그 없음 문제 해결](asp-net-troubleshoot-no-data.md#PerfView)을 참조합니다.

SDK가 토큰을 얻지 못하면 예외 메시지가 로깅됩니다. "AAD 토큰을 얻지 못했습니다. 오류 메시지: ”

### <a name="nodejs"></a>[Node.JS](#tab/nodejs)

다음 설정을 사용하여 내부 로그를 설정할 수 있습니다. 이 기능을 사용하도록 설정하면 Azure AD 통합과 관련된 오류를 포함하여 오류 로그가 콘솔에 표시됩니다. 예를 들어, 잘못된 자격 증명이 제공되거나 토큰을 생성하지 못하거나 제공된 자격 증명을 사용하여 엔드포인트를 인증하지 못하는 경우에 오류가 발생합니다.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").setInternalLogging(true, true);
```

### <a name="java"></a>[Java](#tab/java)

#### <a name="http-traffic"></a>HTTP 트래픽

Fiddler와 같은 도구를 사용하여 네트워크 트래픽을 검사할 수 있습니다. Fiddler를 통해 트래픽을 터널링을 사용하도록 설정하려면, 구성 파일에 다음 프록시 설정을 추가합니다.

```JSON
"proxy": {
"host": "localhost",
"port": 8888
}
```

또는 애플리케이션을 실행하는 동안 `-Djava.net.useSystemProxies=true -Dhttps.proxyHost=localhost -Dhttps.proxyPort=8888` jvm args를 추가합니다.

에이전트에서 Azure AD를 사용하도록 설정하면, 아웃바운드 트래픽에 HTTP 헤더 "권한 부여"가 포함됩니다.


#### <a name="401-unauthorized"></a>401 권한 없음 

로그 파일 `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 401, please check your credentials`에 다음 WARN 메시지가 표시되면, 에이전트가 원격 분석 전송에 성공하지 못했음을 의미합니다. 에이전트에서 Azure AD 인증을 사용하도록 설정하지 않았지만 Application Insights 리소스는 `DisableLocalAuth: true`로 구성됩니다. 유효한 자격 증명을 전달하고 Application Insights 리소스에 액세스할 수 있는 권한이 있는지 확인합니다.


fiddler를 사용하는 경우, `HTTP/1.1 401 Unauthorized - please provide the valid authorization token` 응답 헤더가 표시될 수 있습니다.


#### <a name="credentialunavailableexception"></a>CredentialUnavailableException

로그 파일 `com.azure.identity.CredentialUnavailableException: ManagedIdentityCredential authentication unavailable. Connection to IMDS endpoint cannot be established`에 다음 예외가 표시되면 에이전트가 액세스 토큰을 획득하는 데 성공하지 못했음을 의미하며 사용자 할당 관리 ID 구성에서 잘못된 `clientId`를 제공한 것일 수 있습니다


#### <a name="failed-to-send-telemetry"></a>원격 분석 전송 실패

로그 파일 `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 403, please check your credentials`에 다음 WARN 메시지가 표시되면, 에이전트가 원격 분석 전송에 성공하지 못했음을 의미합니다. 이는 제공된 자격 증명이 원격 분석을 구성 요소에 대한 액세스 권한을 부여하지 않기 때문일 수 있습니다

Fiddler를 사용하는 경우, `HTTP/1.1 403 Forbidden - provided credentials do not grant the access to ingest the telemetry into the component` 응답 헤더가 표시될 수 있습니다.

근본 원인은 다음 중 하나일 수 있습니다.
- 시스템 할당 관리 ID를 사용하도록 설정된 리소스를 만들었거나 사용자 할당 ID를 리소스와 연결했지만 리소스(SAMI를 사용하는 경우) 또는 사용자 할당 ID(UAMI를 사용하는 경우)에 `Monitoring Metrics Publisher` 역할을 추가하는 것을 잊어버렸을 수 있습니다.
- 액세스 토큰을 얻기 위한 올바른 자격 증명을 제공했지만 자격 증명이 올바른 Application Insights 리소스에 속하지 않습니다. Application Insights 리소스에서 `Monitoring Metrics Publisher` 역할이 있는 리소스(vm, App Service 등) 또는 사용자 할당 ID가 표시되는지 확인합니다.

#### <a name="invalid-tenantid"></a>잘못된 TenantId

로그 파일 `com.microsoft.aad.msal4j.MsalServiceException: Specified tenant identifier <TENANT-ID> is neither a valid DNS name, nor a valid external domain.`에 다음 예외가 표시되면 에이전트가 액세스 토큰을 획득하는 데 성공하지 못했음을 의미하며 클라이언트 암호 구성에서 잘못된 `tenantId`를 제공한 것일 수 있습니다.

#### <a name="invalid-client-secret"></a>잘못된 클라이언트 암호

로그 파일 `com.microsoft.aad.msal4j.MsalServiceException: Invalid client secret is provided`에 다음 예외가 표시되면 에이전트가 액세스 토큰을 획득하는 데 성공하지 못했음을 의미하며 클라이언트 암호 구성에서 잘못된 `clientSecret`를 제공한 것일 수 있습니다.


#### <a name="invalid-clientid"></a>잘못된 ClientId

로그 파일 `com.microsoft.aad.msal4j.MsalServiceException: Application with identifier <CLIENT_ID> was not found in the directory`에 다음 예외가 표시되면 에이전트가 액세스 토큰을 획득하는 데 성공하지 못했음을 의미하며 클라이언트 암호 구성에서 잘못된 “clientId”를 제공한 것일 수 있습니다

 이 오류는 테넌트의 관리자가 애플리케이션을 설치하지 않았거나 테넌트의 사용자가 동의하지 않은 경우에 발생할 수 있습니다. 잘못된 테넌트에 인증 요청을 보냈을 수도 있습니다.

### <a name="python"></a>[Python](#tab/python)

#### <a name="error-starts-with-credential-error-with-no-status-code"></a>오류는 "자격 증명 오류"로 시작합니다(상태 코드 없음)

사용 중인 자격 증명에 대해 잘못된 내용이 있으며, 클라이언트가 권한 부여를 위한 토큰을 가져올 수 없습니다. 일반적으로 상태에 필요한 데이터가 없기 때문입니다. 예를 들어, 시스템 ManagedIdentityCredential을 전달하지만 리소스가 시스템 관리 ID를 사용하도록 구성되지 않았습니다.

#### <a name="error-starts-with-authentication-error-with-no-status-code"></a>오류는 "인증 오류"로 시작합니다(상태 코드 없음)

클라이언트가 지정된 자격 증명을 사용하여 인증하지 못했습니다. 일반적으로 사용된 자격 증명에 올바른 역할 할당이 없는 경우에 발생합니다.

#### <a name="im-getting-a-status-code-400-in-my-error-logs"></a>오류 로그에 상태 코드 400이 표시됨

자격 증명이 없거나 자격 증명이 `None`로 설정되어 있지만 Application Insights 리소스가 `DisableLocalAuth: true`로 구성됩니다. 유효한 자격 증명을 전달하고 Application Insights 리소스에 액세스할 수 있는 권한이 있는지 확인합니다.

#### <a name="im-getting-a-status-code-403-in-my-error-logs"></a>오류 로그에 상태 코드 403이 표시됨

일반적으로 제공된 자격 증명이 Application Insights 리소스에 대한 원격 분석의 액세스 권한을 부여하지 않는 경우에 발생합니다. AI 리소스에 올바른 역할 할당이 있는지 확인합니다.

---
## <a name="next-steps"></a>다음 단계
* [포털에서 원격 분석 모니터링](overview-dashboard.md).
* [Live Metrics Stream을 통한 진단](live-stream.md).
