---
title: Azure Web Application Firewall에 대한 로그 모니터링
description: Azure Web Application Firewall을 사용 설정하고 해당 로그를 관리하는 방법 알아보기
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 20ea450789c30556a6a92091affb6631658d5c97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124817962"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Azure Web Application Firewall에 대한 리소스 로그

로그를 사용하여 Web Application Firewall 리소스를 모니터링할 수 있습니다. 성능, 액세스, 기타 데이터를 저장하거나 모니터링 목적으로 리소스에서 사용할 수 있습니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>진단 로그

Azure에서 다양한 유형의 로그를 사용하여 Application Gateway를 관리하고 문제를 해결할 수 있습니다. 이러한 로그 중 일부는 포털을 통해 액세스할 수 있습니다. Azure Blob Storage에서 모든 로그를 추출하여 다양한 도구(예: [Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md),Excel 및 Power BI)에서 볼 수 있습니다. 다음 목록에서 다른 종류의 로그에 대해 자세히 알아볼 수 있습니다.

* **활동 로그**: [Azure 활동 로그](../../azure-monitor/essentials/activity-log.md)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 확인할 수 있습니다. 활동 로그 항목은 기본적으로 수집되고 Azure Portal에서 볼 수 있습니다.
* **액세스 리소스 로그**: 이 로그를 사용하여 Application Gateway 액세스 패턴을 확인하고 중요한 정보를 분석할 수 있습니다. 여기에는 호출자의 IP, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력이 포함됩니다. 액세스 로그는 300초마다 수집됩니다. 이 로그에는 Application Gateway 인스턴스당 하나의 레코드가 포함됩니다. Application Gateway 인스턴스는 instanceId 속성으로 식별됩니다.
* **성능 리소스 로그**: 이 로그를 사용하여 Application Gateway 인스턴스의 성능을 확인할 수 있습니다. 이 로그는 인스턴스 단위로 처리된 총 요청 수, 처리량(바이트), 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다. 성능 로그는 60초마다 수집됩니다. 성능 로그는 v1 SKU에 대해서만 사용할 수 있습니다. v2 SKU의 경우 성능 데이터에 대한 [메트릭](../../application-gateway/application-gateway-metrics.md)을 사용합니다.
* **방화벽 리소스 로그**: 이 로그를 사용하여 Azure Web Application Firewall로 구성된 Application Gateway의 검색 모드 또는 방지 모드를 통해 로깅된 요청을 확인할 수 있습니다.

> [!NOTE]
> 로그는 Azure Resource Manager 배포 모델에 배포된 리소스에만 사용할 수 있습니다. 클래식 배포 모델에서 리소스에 대한 로그를 사용할 수 없습니다. 두 모델에 대해 더 잘 이해하려면 [리소스 관리자 배포 및 클래식 배포 이해](../../azure-resource-manager/management/deployment-models.md) 문서를 참조하세요.

로그 저장에는 세 가지 옵션이 있습니다.

* **Storage 계정** - 로그를 장기간 저장하고 필요할 때 검토하는 경우에 가장 적합합니다.
* **이벤트 허브**: 이벤트 허브는 리소스에 대한 경고를 받기 위해 다른 보안 정보 및 이벤트 관리(SIEM) 도구와 통합하는 데 유용한 옵션입니다.
* **Azure Monitor 로그**: Azure Monitor 로그는 일반적으로 애플리케이션을 실시간으로 모니터링하거나 추세를 파악하는 데 가장 적합합니다.

### <a name="enable-logging-through-powershell"></a>PowerShell을 통한 로깅 사용

활동 로깅은 모든 Resource Manager 리소스에 대해 사용하도록 설정됩니다. 이러한 로그를 통해 사용 가능한 데이터 수집을 시작하려면 액세스 및 성능 로깅을 사용하도록 설정해야 합니다. 로깅을 사용하려면 다음 단계를 사용합니다.

1. 로그 데이터를 저장할 스토리지 계정의 리소스 ID를 적어 둡니다. 이 값의 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\>입니다. 구독의 모든 스토리지 계정을 사용할 수 있습니다. Azure Portal을 사용하여 이 정보를 찾을 수 있습니다.

    ![포털: 스토리지 계정의 리소스 ID](../media/web-application-firewall-logs/diagnostics1.png)

2. 로깅을 사용할 Application Gateway의 리소스 ID를 적어 둡니다. 이 값의 형식은 /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\>입니다. 포털을 사용하여 이 정보를 찾을 수 있습니다.

    ![포털: 애플리케이션 게이트웨이의 리소스 ID](../media/web-application-firewall-logs/diagnostics2.png)

3. 다음 PowerShell cmdlet을 사용하여 리소스 로깅을 사용하도록 설정합니다.

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>활동 로그에는 별도의 스토리지 계정이 필요하지 않습니다. 액세스 및 성능 로깅에 스토리지를 사용할 경우 서비스 요금이 부과됩니다.

### <a name="enable-logging-through-the-azure-portal"></a>Azure Portal을 통한 로깅 사용

1. Azure Portal에서 리소스를 찾고 **진단 설정** 을 선택합니다.

   Application Gateway의 경우 다음 세 가지 로그를 사용할 수 있습니다.

   * 액세스 로그
   * 성능 로그
   * 방화벽 로그

2. 데이터 수집을 시작하려면 **진단 켜기** 를 선택합니다.

   ![진단 켜기][1]

3. **진단 설정** 페이지에서는 리소스 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. 또한 이벤트 허브 및 스토리지 계정을 사용하여 리소스 로그를 저장할 수도 있습니다.

   ![구성 프로세스 시작][2]

5. 설정의 이름을 입력하고 설정을 확인하고 **저장** 을 선택합니다.

### <a name="activity-log"></a>활동 로그

Azure에서는 기본적으로 활동 로그를 생성합니다. 이러한 로그는 Azure 이벤트 로그 저장소에 90일 동안 유지됩니다. [이벤트 및 활동 로그 보기](../../azure-monitor/essentials/activity-log.md) 문서를 참조하여 이러한 로그에 대해 자세히 알아보세요.

### <a name="access-log"></a>액세스 로그

이전 단계에서 설명한 대로 액세스 로그는 각 Application Gateway 인스턴스에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. Application Gateway의 액세스는 각각 v1에 대한 다음 예와 같이 JSON 형식으로 로깅됩니다.

|값  |Description  |
|---------|---------|
|instanceId     | 요청을 처리한 Application Gateway 인스턴스        |
|clientIP     | 요청에 대한 원래 IP        |
|clientPort     | 요청에 대한 원래 포트       |
|httpMethod     | 요청에서 사용된 HTTP 메서드       |
|requestUri     | 받은 요청의 URI        |
|RequestQuery     | **Server-Routed**: 요청을 보낸 백 엔드 풀 인스턴스입니다.</br>**X-AzureApplicationGateway-LOG-ID**: 요청에 사용된 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. </br>**SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드       |
|UserAgent     | HTTP 요청 헤더의 사용자 에이전트        |
|httpStatus     | Application Gateway에서 클라이언트로 반환한 HTTP 상태 코드       |
|httpVersion     | 요청의 HTTP 버전        |
|receivedBytes     | 받은 패킷의 크기(바이트)        |
|sentBytes| 보낸 패킷의 크기(바이트)|
|timeTaken| 요청을 처리하고 응답을 보내는 데 걸리는 시간(밀리초)입니다. 이 값은 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 받은 시점부터 응답 보내기 작업을 완료하는 시점까지의 간격으로 계산됩니다. 걸린 시간(Time-Taken) 필드에는 대개 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간이 포함됩니다. |
|sslEnabled| 백 엔드 풀에 대한 통신에서 TLS/SSL을 사용했는지 여부입니다. 유효한 값은 on과 off입니다.|
|host| 요청이 백 엔드 서버로 전송된 호스트 이름입니다. 백 엔드 호스트 이름이 재정의되는 경우 이 이름에 반영됩니다.|
|originalHost| 클라이언트로부터 Application Gateway가 요청을 받은 호스트 이름입니다.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Application Gateway 및 WAF v2의 경우 로그에 약간의 추가 정보가 표시됩니다.

|값  |Description  |
|---------|---------|
|instanceId     | 요청을 처리한 Application Gateway 인스턴스        |
|clientIP     | 요청에 대한 원래 IP        |
|clientPort     | 요청에 대한 원래 포트       |
|httpMethod     | 요청에서 사용된 HTTP 메서드       |
|requestUri     | 받은 요청의 URI        |
|UserAgent     | HTTP 요청 헤더의 사용자 에이전트        |
|httpStatus     | Application Gateway에서 클라이언트로 반환한 HTTP 상태 코드       |
|httpVersion     | 요청의 HTTP 버전        |
|receivedBytes     | 받은 패킷의 크기(바이트)        |
|sentBytes| 보낸 패킷의 크기(바이트)|
|timeTaken| 요청을 처리하고 응답을 보내는 데 걸리는 시간(밀리초)입니다. 이 값은 Application Gateway에서 HTTP 요청의 첫 번째 바이트를 받은 시점부터 응답 보내기 작업을 완료하는 시점까지의 간격으로 계산됩니다. 걸린 시간(Time-Taken) 필드에는 대개 요청 및 응답 패킷이 네트워크를 통해 이동하는 시간이 포함됩니다. |
|sslEnabled| 백 엔드 풀에 대한 통신에서 TLS를 사용했는지 여부입니다. 유효한 값은 on과 off입니다.|
|sslCipher| TLS 통신에 사용되는 암호화 도구 모음입니다(TLS가 사용 설정된 경우).|
|sslProtocol| 사용되는 TLS 프로토콜입니다(TLS가 사용 설정된 경우).|
|serverRouted| Application Gateway에서 요청을 라우팅하는 백 엔드 서버입니다.|
|serverStatus| 백 엔드 서버의 HTTP 상태 코드입니다.|
|serverResponseLatency| 백 엔드 서버의 응답 대기 시간입니다.|
|host| 요청의 호스트 헤더에 나열된 주소입니다.|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>성능 로그

이전 단계에서 설명한 대로 성능 로그는 각 Application Gateway 인스턴스에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. 성능 로그 데이터는 1분 간격으로 생성됩니다. v1 SKU에 대해서만 사용할 수 있습니다. v2 SKU의 경우 성능 데이터에 대한 [메트릭](../../application-gateway/application-gateway-metrics.md)을 사용합니다. 다음 데이터가 로깅됩니다.


|값  |Description  |
|---------|---------|
|instanceId     |  성능 데이터가 생성되는 Application Gateway 인스턴스입니다. 다중 인스턴스 애플리케이션 게이트웨이의 경우 인스턴스마다 하나의 행이 있습니다.        |
|healthyHostCount     | 백 엔드 풀의 정상 호스트 수        |
|unHealthyHostCount     | 백 엔드 풀의 비정상 호스트 수        |
|requestCount     | 처리된 요청 수        |
|대기 시간 | 인스턴스와 요청을 처리하는 백 엔드 사이의 평균 요청 대기 시간(밀리초)입니다. |
|failedRequestCount| 실패한 요청 수|
|throughput| 마지막 로그 이후의 평균 처리량(초당 바이트 수로 측정됨)|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 대기 시간은 HTTP 요청의 첫 번째 바이트를 받은 시간부터 HTTP 응답의 마지막 바이트를 보낸 시간까지 계산됩니다. 즉 Application Gateway 처리 시간, 백 엔드로의 네트워크 사용 시간 및 백 엔드에서 요청을 처리하는 데 걸린 시간의 합계입니다.

### <a name="firewall-log"></a>방화벽 로그

이전 단계에서 설명한 대로 방화벽 로그는 각 애플리케이션 게이트웨이에서 이러한 로그를 사용하도록 설정한 경우에만 생성됩니다. 또한 이 로그를 사용하려면 애플리케이션 게이트웨이에서 웹 애플리케이션 방화벽을 구성해야 합니다. 데이터는 로깅을 사용하도록 설정할 때 지정한 스토리지 계정에 저장됩니다. 다음 데이터가 로깅됩니다.


|값  |Description  |
|---------|---------|
|instanceId     | 방화벽 데이터가 생성되는 Application Gateway 인스턴스입니다. 다중 인스턴스 애플리케이션 게이트웨이의 경우 인스턴스마다 하나의 행이 있습니다.         |
|clientIp     |   요청에 대한 원래 IP      |
|clientPort     |  요청에 대한 원래 포트       |
|requestUri     | 받은 요청의 URL       |
|ruleSetType     | 규칙 집합 유형이며, 사용 가능한 값은 OWASP입니다.        |
|ruleSetVersion     | 사용된 규칙 집합 버전이며, 사용 가능한 값은 2.2.9 및 3.0입니다.     |
|ruleId     | 트리거 이벤트의 규칙 ID        |
|message     | 사용자에게 친숙한 트리거 이벤트에 대한 메시지이며, 자세한 내용은 세부 정보 섹션에서 제공됩니다.        |
|action     |  요청에서 수행되는 동작이며, 사용 가능한 값은 차단 및 허용(사용자 지정 규칙의 경우), 일치(규칙이 요청의 일부와 일치하는 경우), 및 검색되고 차단(WAF가 검색 또는 방지 모드에 있는지에 따라 둘 다 필수 규칙임)됩니다.      |
|site     | 로그를 생성한 사이트이며, 현재 규칙이 전역이므로 Global만 나열됩니다.|
|자세히     | 트리거 이벤트의 세부 정보        |
|details.message     | 규칙에 대한 설명        |
|details.data     | 규칙과 일치하는 요청 내 특정 데이터         |
|details.file     | 규칙이 포함된 구성 파일        |
|details.line     | 이벤트를 트리거한 구성 파일의 줄 번호       |
|hostname   | Application Gateway의 호스트 이름 또는 IP 주소입니다.    |
|transactionId  | 동일한 요청 내에서 발생한 여러 규칙 위반을 그룹화하는 데 도움이 되는 지정된 트랜잭션의 고유 ID입니다.   |
|policyId   | Application Gateway, 수신기 또는 경로와 연결된 방화벽 정책의 고유 ID입니다.   |
|policyScope    | 정책의 위치로, 값은 “전역”, “수신기” 또는 “위치”가 될 수 있습니다.   |
|policyScopeName   | 정책이 적용되는 개체의 이름입니다.    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>활동 로그 보기 및 분석

다음 방법 중 하나를 사용하여 활동 로그 데이터를 확인하고 분석할 수 있습니다.

* **Azure 도구** - Azure PowerShell, Azure CLI, Azure REST API 또는 Azure Portal을 통해 활동 로그에서 정보를 검색합니다. 각 방법에 대한 단계별 지침은 [Resource Manager의 활동 작업](../../azure-monitor/essentials/activity-log.md) 문서에 자세히 나와 있습니다.
* **Power BI** - [Power BI](https://powerbi.microsoft.com/pricing) 계정이 아직 없는 경우 무료로 사용해볼 수 있습니다. [Power BI 템플릿 앱](/power-bi/service-template-apps-overview)을 사용하여 데이터를 분석할 수 있습니다.

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>액세스, 성능 및 방화벽 로그 보기 및 분석

[Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md)는 Blob Storage 계정에서 카운터 및 이벤트 로그 파일을 수집할 수 있습니다. 여기에는 로그를 분석하는 시각화 및 강력한 검색 기능이 포함되어 있습니다.

스토리지 계정에 연결하고 액세스 및 성능 로그에 대한 JSON 로그 항목을 검색할 수도 있습니다. JSON 파일을 다운로드한 후 CSV로 변환하여 Excel, Power BI 또는 기타 데이터 시각화 도구에서 볼 수 있습니다.

> [!TIP]
> Visual Studio를 익숙하게 사용할 수 있고 C#에서 상수 및 변수에 대한 값 변경에 대한 기본 개념이 있는 경우 GitHub에서 제공하는 [로그 변환기 도구](https://github.com/Azure-Samples/networking-dotnet-log-converter)를 사용할 수 있습니다.
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>GoAccess를 통해 액세스 로그 분석

Application Gateway 액세스 로그에 대해 널리 사용되는 [GoAccess](https://goaccess.io/) 로그 분석기를 설치하고 실행하는 Resource Manager 템플릿을 게시했습니다. GoAccess는 고유 방문자, 요청한 파일, 호스트, 운영 체제, 브라우저, HTTP 상태 코드 및 기타 유용한 HTTP 트래픽 통계를 제공 합니다. 자세한 내용은 [GitHub의 Resource Manager 템플릿 폴더에 대한 추가 정보 파일](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/application-gateway-logviewer-goaccess)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Monitor 로그](../../azure-monitor/insights/azure-networking-analytics.md)를 사용하여 카운터 및 이벤트 로그를 시각화합니다.
* [Power BI를 사용하여 Azure 활동 로그 시각화](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/) 블로그 게시물
* [Power BI 등에서 Azure 활동 로그 보기 및 분석](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) 블로그 게시물

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
