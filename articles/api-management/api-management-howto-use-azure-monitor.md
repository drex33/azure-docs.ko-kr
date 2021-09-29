---
title: 자습서 - Azure API Management에서 게시된 API 모니터링 | Microsoft Docs
description: 이 자습서의 단계에 따라 Azure API Management에서 메트릭, 경고, 활동 로그 및 리소스 로그를 사용하여 API를 모니터링하는 방법을 알아봅니다.
services: api-management
author: dlepow
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 10/14/2020
ms.author: danlep
ms.openlocfilehash: 378fa83925808b1c9551522cfe4c47665095cd17
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642076"
---
# <a name="tutorial-monitor-published-apis"></a>자습서: 게시된 API 모니터링

Azure Monitor를 통해 Azure API Management 서비스의 메트릭 또는 로그를 시각화, 쿼리, 라우팅, 보관하고 필요한 조치를 수행할 수 있습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * API의 메트릭 보기 
> * 경고 규칙 설정 
> * 활동 로그 보기
> * 리소스 로그 사용 및 보기

API Management의 기본 제공 [분석](howto-use-analytics.md)을 사용하여 API의 사용 및 성능을 모니터링할 수도 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

+ [Azure API Management 용어](api-management-terminology.md)를 익힙니다.
+ 다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)
+ 또한 [첫 번째 API 가져오기 및 게시](import-and-publish.md) 자습서를 완료합니다.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="view-metrics-of-your-apis"></a>API의 메트릭 보기

API Management는 1분 간격으로 [메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 내보내므로 거의 실시간으로 API 상태를 확인할 수 있습니다. 다음은 가장 많이 사용되는 두 가지 메트릭입니다. 사용 가능한 모든 메트릭 목록은 [지원되는 메트릭](../azure-monitor/essentials/metrics-supported.md#microsoftapimanagementservice)을 참조하세요.

* **용량** - APIM 서비스를 업그레이드/다운그레이드할지 결정하는 데 도움이 됩니다. 메트릭은 1분 간격으로 내보내지며 보고 시점의 게이트웨이 용량을 반영합니다. 메트릭의 범위는 0-100이고, CPU 및 메모리 사용률 등의 게이트웨이 리소스를 기반으로 계산됩니다.
* **요청** - API Management 서비스를 통과하는 API 트래픽을 분석하는 데 도움이 됩니다. 메트릭은 분당 내보내고 응답 코드, 위치, 호스트 이름 및 오류를 포함한 차원으로 게이트웨이 요청 수를 보고합니다. 

> [!IMPORTANT]
> 다음 메트릭은 2019년 5월부터 사용되지 않으며 2023년 8월에 사용 중지됩니다. 총 게이트웨이 요청, 성공적인 게이트웨이 요청, 권한이 없는 게이트웨이 요청, 실패한 게이트웨이 요청, 기타 게이트웨이 요청. 동일한 기능을 제공하는 요청 메트릭으로 마이그레이션하세요.

:::image type="content" source="media/api-management-howto-use-azure-monitor/apim-monitor-metrics.png" alt-text="API Management 개요의 메트릭 스크린샷":::

메트릭에 액세스하려면

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다. **개요** 페이지에서 API의 주요 메트릭을 검토합니다.
1. 메트릭을 자세히 조사하려면 페이지 아래쪽에 있는 메뉴에서 **메트릭** 을 선택합니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-metrics-blade.png" alt-text="[모니터링] 메뉴의 [메트릭] 항목 스크린샷":::

1. 드롭다운 목록에서 관심 있는 메트릭을 선택합니다. 예: **Requests** 
1. 차트에는 총 API 호출 수가 표시됩니다.
1. **요청** 메트릭의 차원을 사용하여 차트를 필터링할 수 있습니다. 예를 들어 **필터 추가** 를 선택하고, **백 엔드 응답 코드 범주** 를 선택하고, 값으로 500을 입력합니다. 이제 차트에는 API 백 엔드에서 실패한 요청의 수가 표시됩니다.   

## <a name="set-up-an-alert-rule"></a>경고 규칙 설정 

메트릭 및 활동 로그를 기반으로 [경고](../azure-monitor/alerts/alerts-metric-overview.md)를 수신할 수 있습니다. Azure Monitor를 사용하여 트리거되면 다음을 수행하도록 [경고를 구성](../azure-monitor/alerts/alerts-metric.md)할 수 있습니다.

* 전자 메일 알림 보내기
* 웹후크 호출
* Azure 논리 앱 호출

요청 메트릭을 기반으로 경고 규칙 예제를 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. 페이지 맨 아래의 메뉴 모음에서 **경고** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/alert-menu-item.png" alt-text="[모니터링] 메뉴의 [경고] 옵션 스크린샷":::

1. **+ 새 경고 규칙** 을 선택합니다.
1. **경고 규칙 만들기** 창에서 **조건 선택** 을 클릭합니다.
1. **신호 논리 구성** 창에서 다음을 수행합니다.
    1. **신호 형식** 에서 **메트릭** 을 선택합니다.
    1. **신호 이름** 에서 **요청** 을 선택합니다.
    1. **차원으로 분할** 의 **차원 이름** 에서 **게이트웨이 응답 코드 범주** 를 선택합니다.
    1. **차원 값** 에서 권한이 없거나 잘못된 요청과 같은 클라이언트 오류에 대해 **4xx** 를 선택합니다.
    1. **경고 논리** 에서, 경고가 트리거되어야 하는 임계값을 지정하고 **완료** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/threshold.png" alt-text="[신호 논리 구성] 창의 스크린샷":::

1. 기존 작업 그룹을 선택하거나 새 작업 그룹을 만듭니다. 다음 예제에서는 새 작업 그룹을 만듭니다. 알림 이메일은 admin@contoso.com으로 전송됩니다. 

    :::image type="content" source="media/api-management-howto-use-azure-monitor/action-details.png" alt-text="새 작업 그룹에 대한 알림의 스크린샷":::

1. 경고 규칙의 이름과 설명을 입력하고, 심각도를 선택합니다. 
1. **경고 규칙 만들기** 를 선택합니다.
1. 이제 API 키 없이 Conference API를 호출하여 경고 규칙을 테스트합니다. 다음은 그 예입니다. 

    ```bash
    curl GET https://apim-hello-world.azure-api.net/conference/speakers HTTP/1.1 
    ```

    평가 기간에 따라 경고가 트리거되고, 이메일이 admin@contoso.com으로 전송됩니다. 

    API Management 인스턴스의 **경고** 페이지에도 경고가 표시됩니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/portal-alerts.png" alt-text="포털의 경고 스크린샷":::

## <a name="activity-logs"></a>활동 로그

활동 로그는 API Management 서비스에서 수행된 작업에 대한 정보를 제공합니다. 활동 로그를 통해 API Management 서비스에 대한 모든 쓰기 작업(PUT, POST, DELETE)에서 "무엇을, 누가, 언제"를 판단할 수 있습니다.

> [!NOTE]
> 활동 로그에는 읽기(GET) 작업 또는 Azure Portal에서 수행되었거나 원본 Management API를 사용하는 작업이 포함되지 않습니다.

API Management 서비스에서 활동 로그에 액세스하거나 Azure Monitor에서 모든 Azure 리소스의 로그에 액세스할 수 있습니다. 

:::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs.png" alt-text="포털의 활동 로그 스크린샷":::

활동 로그를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.

1. **활동 로그** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-activity-logs-blade.png" alt-text="[모니터링] 메뉴의 [활동 로그] 항목 스크린샷":::
1. 원하는 필터링 범위를 선택하고 **적용** 을 선택합니다.

## <a name="resource-logs"></a>리소스 로그

리소스 로그는 문제 해결뿐만 아니라 감사에 중요한 작업 및 오류에 대한 풍부한 정보를 제공합니다. 리소스 로그는 활동 로그와 다릅니다. 활동 로그는 Azure 리소스에서 수행된 작업에 대한 인사이트를 제공합니다. 리소스 로그는 리소스에서 수행하는 작업에 대한 인사이트를 제공합니다.

리소스 로그를 구성하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
2. **진단 설정** 을 선택합니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/api-management-diagnostic-logs-blade.png" alt-text="[모니터링] 메뉴의 [진단 설정] 항목 스크린샷":::

1. **+ 진단 설정 추가** 를 선택합니다.
1. 수집하려는 로그 또는 메트릭을 선택합니다.

   리소스 로그를 메트릭과 함께 스토리지 계정에 보관하고, Event Hub로 스트림하고, Log Analytics 작업 영역으로 보낼 수 있습니다. 

자세한 내용은 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)를 참조하세요.

## <a name="view-diagnostic-data-in-azure-monitor"></a>Azure Monitor에서 진단 데이터 보기

Log Analytics 작업 영역에서 GatewayLogs 또는 메트릭을 수집하도록 설정하는 경우 Azure Monitor에 데이터가 표시될 때까지 몇 분 정도 걸릴 수 있습니다. 데이터를 보려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다.
1. 페이지 아래쪽의 메뉴에서 **로그** 를 선택합니다.

    :::image type="content" source="media/api-management-howto-use-azure-monitor/logs-menu-item.png" alt-text="[모니터링] 메뉴의 [로그] 항목 스크린샷":::

쿼리를 실행하여 데이터를 봅니다. 여러 [샘플 쿼리](../azure-monitor/logs/queries.md)가 제공되며, 자체 쿼리를 실행해도 됩니다. 예를 들어 다음 쿼리는 GatewayLogs 테이블에서 최근 24시간 동안의 데이터를 검색합니다.

```kusto
ApiManagementGatewayLogs
| where TimeGenerated > ago(1d) 
```

API Management에 대한 리소스 로그 사용에 대한 자세한 내용은 다음을 참조하세요.

* [Azure Monitor Log Analytics 시작](../azure-monitor/logs/log-analytics-tutorial.md) 또는 [Log Analytics 데모 환경](https://portal.loganalytics.io/demo) 사용

* [Azure Monitor의 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)

다음 JSON은 GatewayLogs에서 성공한 API 요청의 샘플 항목을 나타냅니다. 자세한 내용은 [스키마 참조](gateway-log-schema-reference.md)에서 확인할 수 있습니다. 

```json
{
    "Level": 4,
    "isRequestSuccess": true,
    "time": "2020-10-14T17:xx:xx.xx",
    "operationName": "Microsoft.ApiManagement/GatewayLogs",
    "category": "GatewayLogs",
    "durationMs": 152,
    "callerIpAddress": "xx.xx.xxx.xx",
    "correlationId": "3f06647e-xxxx-xxxx-xxxx-530eb9f15261",
    "location": "East US",
    "properties": {
        "method": "GET",
        "url": "https://apim-hello-world.azure-api.net/conference/speakers",
        "backendResponseCode": 200,
        "responseCode": 200,
        "responseSize": 41583,
        "cache": "none",
        "backendTime": 87,
        "requestSize": 526,
        "apiId": "demo-conference-api",
        "operationId": "GetSpeakers",
        "apimSubscriptionId": "master",
        "clientTime": 65,
        "clientProtocol": "HTTP/1.1",
        "backendProtocol": "HTTP/1.1",
        "apiRevision": "1",
        "clientTlsVersion": "1.2",
        "backendMethod": "GET",
        "backendUrl": "https://conferenceapi.azurewebsites.net/speakers"
    },
    "resourceId": "/SUBSCRIPTIONS/<subscription ID>/RESOURCEGROUPS/<resource group>/PROVIDERS/MICROSOFT.APIMANAGEMENT/SERVICE/APIM-HELLO-WORLD"
}
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * API의 메트릭 보기
> * 경고 규칙 설정 
> * 활동 로그 보기
> * 리소스 로그 사용 및 보기


다음 자습서를 진행합니다.

> [!div class="nextstepaction"]
> [호출 추적](api-management-howto-api-inspector.md)