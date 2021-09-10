---
title: Azure Application Insights IP 주소 수집 | Microsoft Docs
description: Application Insights에서 IP 주소 및 지리적 위치를 처리 하는 방법을 이해합니다.
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 28b3fb6242d496be7962961d36db3a85c6187d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536651"
---
# <a name="geolocation-and-ip-address-handling"></a>지리적 위치 및 IP 주소 처리

이 문서에서는 기본 동작을 수정하는 방법과 함께 Application Insights에서 지리적 위치 조회 및 IP 주소 처리가 작동하는 방식을 설명합니다.

## <a name="default-behavior"></a>기본 동작

기본적으로 IP 주소는 일시적으로 수집되지만 Application Insights에는 저장되지 않습니다. 기본 프로세스는 다음과 같습니다.

원격 분석이 Azure로 전송될 때 Application Insightssms IP 주소를 [MaxMind의 GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/)로 지리적 위치 조회를 수행하는 데 사용됩니다. Application Insights는 이 조회 결과를 사용하여 `client_City`, `client_StateOrProvince` 및 `client_CountryOrRegion` 필드를 채웁니다. 그런 다음 주소가 삭제되고 `0.0.0.0`이 `client_IP` 필드에 기록됩니다. 

원격 분석 형식 필터링

* 브라우저 원격 분석: Application Insights 보낸 사람의 IP 주소를 수집 합니다. 수집 엔드포인트는 IP 주소를 계산 합니다.
* 서버 원격 분석: Application Insights 원격 분석 모듈이 클라이언트 IP 주소를 임시로 수집합니다. `X-Forwarded-For` 헤더가 설정되면 IP 주소가 로컬로 수집되지 않습니다. 들어오는 IP 주소 목록에 항목이 두 개 이상 있는 경우 마지막 IP 주소가 위치 정보 필드를 채우는 데 사용됩니다.

이 동작은 불필요한 개인 데이터 수집을 방지하기 위한 것입니다. 가능하면 항상 개인 데이터 수집을 피하는 것이 좋습니다. 

> [!NOTE]
> 기본값은 IP 주소를 수집하지 않는 것이지만 이 동작을 재정의할 수 있습니다. 컬렉션이 규정 준수 요구 사항이나 현지 규정을 위반하지 않는지 확인하는 것이 좋습니다. 
>
> Application Insights에서의 개인 데이터 처리에 대한 자세한 내용은 [개인 데이터 지침](../logs/personal-data-mgmt.md)을 참조하세요.


## <a name="storage-of-ip-address-data"></a>IP 주소 데이터의 Storage

IP 수집과 스토리지를 사용하도록 설정하려면 Application Insights 구성 요소의 `DisableIpMasking` 속성을 `true`로 설정해야 합니다. Azure Resource Manager 템플릿을 통해 또는 REST API를 호출하여 이 속성을 설정할 수 있습니다. 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager 템플릿

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>포털

단일 Application Insights 리소스의 동작만 수정해야 하는 경우 Azure Portal을 사용하세요. 

1. Application Insights 리소스로 이동한 다음 **자동화** > **템플릿 내보내기** 를 선택합니다. 

2. **배포** 를 선택합니다.

    ![빨간색으로 강조 표시된 배포 버튼을 보여주는 스크린샷.](media/ip-collection/deploy.png)

3. **템플릿 편집** 을 선택합니다.

    ![리소스 그룹에 대한 경고와 함께 빨간색으로 강조 표시된 편집 버튼을 보여주는 스크린샷.](media/ip-collection/edit-template.png)

    > [!NOTE]
    > 스크린샷에 표시된 대로 다음 오류가 발생하면 해결할 수 있습니다. ‘리소스 그룹이 템플릿의 하나 이상의 리소스에서 지원하지 않는 위치에 있습니다. 다른 리소스 그룹을 선택 하세요.’ 드롭다운에서 다른 리소스 그룹을 임시로 선택한 다음 원래 리소스 그룹을 다시 선택하여 오류를 해결합니다.

4. JSON 템플릿에서 `resources` 안에 있는 `properties`을 찾고 마지막 JSON 필드에 쉼표를 추가한 다음 새 줄 `"DisableIpMasking": true`을 추가합니다. 그런 다음 **저장** 을 선택합니다.

    ![요청 소스에 대 한 속성 뒤에 쉼표와 새 줄을 추가 하는 것을 보여 주는 스크린샷](media/ip-collection/save.png)

5. **검토 + 만들기** > **만들기** 를 차례로 선택합니다.

    > [!NOTE]
    > ‘배포 실패’가 표시되면 유형이 `microsoft.insights/components`인 배포 세부정보를 살펴보고 상태를 확인합니다. 이 작업이 성공하면 `DisableIpMasking`에 대한 변경 사항이 배포됩니다.

6. 배포가 완료되면 새 원격 분석 데이터가 기록됩니다.

    템플릿을 다시 선택하여 편집하면 새로 추가된 속성 없이 기본 템플릿만 보입니다. IP 주소 데이터가 표시되지 않고 `"DisableIpMasking": true`가 설정되어 있는지 확인하려면 다음 PowerShell을 실행합니다.
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using Azure Cloud Shell, you need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    속성 목록이 결과로 반환됩니다. 속성 중 하나에 `DisableIpMasking: true`가 표시되어야 합니다. Azure Resource Manager를 사용하여 새 속성을 배포하기 전에 PowerShell을 실행하는 경우 속성이 존재하지 않습니다.

### <a name="rest-api"></a>Rest API

동일한 수정 작업을 수행하는 [REST API](/rest/api/azure/) 페이로드는 다음과 같습니다.

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>원격 분석 이니셜라이저

`DisableIpMasking`보다 더 유연한 대안이 필요한 경우 [원격 분석 이니셜라이저](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer)를 사용하여 IP 주소의 전체 또는 일부를 맞춤 필드에 복사할 수 있습니다. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet-or-aspnet-core"></a>ASP.NET 또는 ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> `ISupportProperties`에 액세스할 수 없는 경우 안정적인 최신 Application Insights SDK를 실행하고 있는지 확인합니다. `ISupportProperties`은 높은 카디널리티 값을 위한 것입니다. `GlobalProperties`은 지역 이름 및 환경 이름과 같은 낮은 카디널리티 값에 더 적합 합니다.

### <a name="enable-the-telemetry-initializer-for-aspnet"></a>ASP.NET에 원격 분석 이니셜라이저 사용

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-the-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core에 원격 분석 이니셜라이저 사용

ASP.NET Core에 대해 ASP.NET과 동일한 방식으로 원격 분석 이니셜라이저를 만들 수 있습니다. 이니셜라이저를 사용하도록 설정 하려면 다음 예제를 참조하세요.

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.JS](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[클라이언트 쪽 JavaScript](#tab/javascript)

### <a name="client-side-javascript"></a>클라이언트 쪽 JavaScript

서버 쪽 SDK와 달리 클라이언트 쪽 JavaScript SDK는 IP 주소를 계산하지 않습니다. 기본적으로 클라이언트 쪽 원격 분석에 대한 IP 주소 계산은 Azure의 수집 엔드포인트에서 발생합니다. 

클라이언트 쪽에서 직접 IP 주소를 계산하려면 자체 사용자 지정 논리를 추가하고 결과를 사용하여 `ai.location.ip` 태그를 설정해야 합니다. `ai.location.ip`가 설정되면 수집 엔드포인트에서 IP 주소 계산이 수행되지 않고, 제공된 IP 주소가 지리적 위치 조회에 사용됩니다. 이 시나리오에서 IP 주소는 기본적으로 여전히 0으로 채워집니다. 

사용자 정의 논리에서 계산된 전체 IP 주소를 유지하려면 `ai.location.ip`에서 제공한 IP 주소 데이터를 별도의 사용자 정의 필드에 복사하는 원격 분석 이니셜라이저를 사용할 수 있습니다. 그러나 서버 측 SDK와 달리 클라이언트 측 SDK는 타사 라이브러리나 사용자 정의 논리에 의존할 수 없는 경우 주소를 계산하지 않습니다.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

클라이언트 측 데이터가 수집 엔드포인트로 전달되기 전에 프록시를 통과하는 경우 IP 주소 계산은 클라이언트가 아닌 프록시의 IP 주소를 표시할 수 있습니다. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>원격 분석 이니셜라이저 결과 보기

사이트에 새 트래픽을 보내는 경우 몇 분 정도 기다린 다음 쿼리를 실행하여 컬렉션이 작동 하는지 확인할 수 있습니다.

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

새로 수집된 IP 주소가 `customDimensions_client-ip` 열에 표시됩니다. 기본 `client-ip` 열은 여전히 4개의 8진수가 모두 0으로 채워집니다. 

localhost에서 테스트하고 `customDimensions_client-ip` 값이 `::1`이면 이 값은 예상된 동작입니다. `::1`은 IPv6의 루프백 주소를 나타냅니다. IPv4의 `127.0.0.1`에 해당합니다.

## <a name="next-steps"></a>다음 단계

* Application Insights의 [개인 데이터 수집](../logs/personal-data-mgmt.md)에 대해 자세히 알아봅니다.

* Application Insights의 [IP 주소 수집](https://apmtips.com/posts/2016-07-05-client-ip-address/) 작동 방식에 대해 자세히 알아봅니다. 이 문서는 엔지니어 중 한 명이 작성한 이전 외부 블로그 게시물입니다. IP 주소가 `0.0.0.0`로 기록되는 현재 기본 동작보다 앞서지만 내장된 원격 측정 이니셜라이저의 메커니즘에 대해 더 자세히 설명합니다.
