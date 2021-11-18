---
title: Azure Application Gateway 데이터 참조 모니터링
description: Application Gateway를 모니터링할 때 필요한 중요 참조 자료
author: vhorne
ms.author: victorh
ms.topic: conceptual
ms.service: application-gateway
ms.custom: subject-monitoring
ms.date: 11/17/2021
ms.openlocfilehash: 648ee994b2f82b52e29bb764c9923dc5de9dece6
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757600"
---
<!-- VERSION 2.2
Template for monitoring data reference article for Azure services. This article is support for the main "Monitoring [servicename]" article for the service. -->

# <a name="monitoring-azure-application-gateway-data-reference"></a>Azure Application Gateway 데이터 참조 모니터링

Azure Application Gateway의 모니터링 데이터 수집 및 분석에 대한 자세한 내용은 [Azure Application Gateway 모니터링](monitor-application-gateway.md)을 참조하세요.

## <a name="metrics"></a>메트릭

<!-- REQUIRED if you support Metrics. If you don't, keep the section but call that out. Some services are only onboarded to logs.
<!-- Please keep headings in this order -->

<!--  OPTION 2 -  Link to the metrics as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the metrics-supported link. For highly customized example, see [CosmosDB](../cosmos-db/monitor-cosmos-db-reference.md#metrics). They even regroup the metrics into usage type vs. resource provider and type.
-->

<!-- Example format. Mimic the setup of metrics supported, but add extra information -->

### <a name="application-gateway-v2-metrics"></a>Application Gateway v2 메트릭

리소스 공급자 및 유형: [Microsoft.Network/applicationGateways](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkapplicationgateways)

#### <a name="timing-metrics"></a>타이밍 메트릭
Application Gateway는 모두 밀리초 단위로 측정되는 요청 및 응답과 관련된 몇 가지 기본 제공 타이밍 메트릭을 제공합니다.

> [!NOTE]
>
> Application Gateway에 수신기가 둘 이상인 경우에는 항상 *수신기* 차원을 기준으로 필터링하여 보다 의미 있는 유추를 얻기 위해 다른 대기 시간 메트릭을 비교합니다.


| 메트릭 | 단위 | 설명|
|:-------|:-----|:------------|
|**백 엔드 연결 시간**|밀리초|백 엔드 애플리케이션과의 연결을 설정하는 소요된 시간입니다.<br><br>여기에는 네트워크 대기 시간과 백 엔드 서버의 TCP 스택에서 새 연결을 설정 하는 데 소요 되는 시간이 포함 됩니다. TLS의 경우 핸드셰이크에 소요 된 시간도 포함 됩니다.|
|**백 엔드 첫 번째 바이트 응답 시간**|밀리초|백 엔드 서버에 대한 연결을 설정하기 시작하는 시간과 응답 헤더의 첫 번째 바이트를 수신하기 시작하는 시간의 간격입니다.<br><br>이는 백 엔드 연결 시간, Application Gateway에서 백 엔드에 도달하는 요청에서 소요된 시간, 백 엔드 애플리케이션이 응답하는 데 소요된 시간(서버에서 콘텐츠를 생성하여 데이터베이스 쿼리를 가져오는 데 소요된 시간), 그리고 백 엔드에서 Application Gateway에 도달하는 응답의 첫 번째 바이트에서 소요된 시간의 대략적인 합입니다.|
|**백 엔드 마지막 바이트 응답 시간**|밀리초|백 엔드 서버에 대한 연결을 설정하기 시작한 시간과 응답 본문의 마지막 바이트를 수신하기 시작하는 시간의 간격입니다.<br><br>이것은 백 엔드 첫 번째 바이트 응답 시간과 데이터 전송 시간의 합에 가깝습니다. 이 숫자는 요청된 개체의 크기와 서버 네트워크의 대기 시간에 따라 크게 달라질 수 있습니다.|
|**Application Gateway 총 시간**|밀리초|수신할 요청을 처리하고 응답을 보내는 데 걸리는 평균 시간입니다.<br><br>Application Gateway가 HTTP 요청의 첫 번째 바이트를 받은 시점부터 마지막 응답 바이트가 클라이언트로 전송된 시간까지의 간격입니다. 여기에는 모든 응답과 클라이언트 RTT를 전송하기 위해 Application Gateway에서 수행하는 Application Gateway, 백 엔드 마지막 바이트 응답 시간 등의 처리 시간이 포함됩니다.|
|**클라이언트 RTT**|밀리초|클라이언트와 Application Gateway 사이의 평균 왕복 시간입니다.|

이러한 메트릭을 사용하여 관찰된 속도가 클라이언트 네트워크, Application Gateway 성능, 백 엔드 네트워크 및 백엔드 서버 TCP 스택 포화, 백 엔드 애플리케이션 성능 또는 큰 파일 크기 때문인지 여부를 확인할 수 있습니다.

예를 들어 *백 엔드 첫 번째 바이트 응답 시간* 추세가 급증 하지만 *백 엔드 연결 시간* 추세가 안정적 이면 응용 프로그램 게이트웨이가 백 엔드 대기 시간으로, 연결을 설정 하는 데 걸리는 시간이 안정적이 고, 백 엔드 응용 프로그램의 응답 시간이 증가 하 여 스파이크가 발생 하는 것을 유추할 수 있습니다. 반면에 *백 엔드 첫 번째 바이트 응답 시간* 에 스파이크가 *백 엔드 연결 시간* 의 해당 스파이크와 연결되어 있는 경우 Application Gateway와 백 엔드 서버 사이의 네트워크 또는 백 엔드 서버 TCP 스택이 포화 상태임을 추론할 수 있습니다. 

*백 엔드 마지막 바이트 응답 시간* 에 스파이크가 있지만 *백 엔드 첫 번째 바이트 응답 시간* 은 안정적인 경우 스파이크가 요청되는 파일이 더 크기 때문이라고 추론할 수 있습니다.

마찬가지로 *Application Gateway 총 시간* 에 스파이크가 있지만 *백 엔드 마지막 바이트 응답 시간* 이 안정적인 경우 이는 Application Gateway의 성능 병목 현상 또는 클라이언트와 Application Gateway 사이에 있는 네트워크의 병목 현상의 징후일 수 있습니다. 또한 *클라이언트 RTT* 에 해당 하는 스파이크가 있는 경우 클라이언트와 Application Gateway 간의 네트워크로 인해 성능이 저하 되는 것을 나타냅니다.

#### <a name="application-gateway-metrics"></a>Application Gateway 메트릭

| 메트릭 | 단위 | 설명|
|:-------|:-----|:------------|
|**받은 바이트 수**|바이트|Application Gateway가 클라이언트에서 받은 바이트 수입니다.|
|**보낸 바이트 수**|바이트|Application Gateway가 클라이언트에 보낸 바이트 수입니다.|
|**클라이언트 TLS 프로토콜**|개수|Application Gateway와 연결을 설정한 클라이언트에서 시작한 TLS 및 비 TLS 요청 수입니다. TLS 프로토콜 배포를 보려면 TLS 프로토콜 차원으로 필터링합니다.|
|**현재 용량 단위**|개수|트래픽 부하를 분산하는 데 사용되는 용량 단위 수입니다. 용량 단위에는 컴퓨팅 단위, 영구 연결 및 처리량의 세 가지 결정 요인이 있습니다. 각 용량 단위는 최대 1개의 컴퓨팅 단위 또는 2,500개의 영구 연결 또는 2.22Mbps의 처리량으로 구성됩니다.|
|**현재 컴퓨팅 단위**|개수|사용된 프로세서 용량 수입니다. 컴퓨팅 단위에 영향을 주는 요소는 TLS 연결/초, URL 다시 쓰기 계산 및 WAF 규칙 처리입니다.|
|**현재 연결**|개수|클라이언트에서 Application Gateway로 활성 상태인 총 동시 연결 수입니다.|
|**예상 청구 용량 단위**|개수|v2 SKU를 사용하는 경우 가격 책정 모델은 사용량에 따라 결정됩니다. 용량 단위는 고정 비용 외에 청구되는 소비 기반 비용을 측정합니다. * 예상 청구 된 용량 단위는 청구를 예상 하는 데 사용 되는 용량 단위 수를 표시 합니다. 이 값은 *현재 용량 단위*(트래픽 부하를 분산하는 데 필요한 용량 단위)와 *고정 청구 가능 용량 단위*(프로비저닝된 최소 용량 단위) 중에서 더 큰 값으로 계산됩니다.|
|**실패한 요청**|개수|Application Gateway에서 5xx 서버 오류 코드와 함께 제공된 요청 수입니다. 여기에는 Application Gateway에서 생성 된 5xx 코드와 백 엔드에서 생성 된 5xx 코드가 포함 됩니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시하도록 요청 수를 추가로 필터링할 수 있습니다.|
|**고정 청구 가능 용량 단위**|개수|Application Gateway 구성에서 *최소 배율 단위* 설정(한 인스턴스는 10 개의 용량 단위로 변환됨)에 따라 프로비저닝된 최소 용량 단위 수입니다.|
|**초당 새 연결 수**|개수|클라이언트에서 Application Gateway로 설정되고 Application Gateway에서 백 엔드 멤버로 설정된 초당 평균 새 TCP 연결 수입니다.|
|**응답 상태**|상태 코드|Application Gateway에서 반환된 HTTP 응답 상태입니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.|
|**처리량**|바이트/초|Application Gateway에서 제공하는 초당 바이트 수입니다.|
|**총 요청 수**|개수|Application Gateway가 제공하는 성공한 요청 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시하도록 요청 수를 추가로 필터링할 수 있습니다.|

#### <a name="backend-metrics"></a>백 엔드 메트릭

| 메트릭 | 단위 | 설명|
|:-------|:-----|:------------|
|**백 엔드 응답 상태**|개수|백 엔드에서 반환된 HTTP 응답 상태 코드의 수입니다. Application Gateway 생성된 응답 코드는 포함되지 않습니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.|
|**정상 호스트 수**|개수|상태 프로브에서 정상으로 확인된 백 엔드 수입니다. 특정 백 엔드 풀의 정상 호스트 수를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.|
|**비정상 호스트 수**|개수|상태 프로브에서 비정상으로 확인된 백 엔드 수입니다. 특정 백 엔드 풀의 비정상 호스트 수를 표시하도록 백 엔드 풀 기준으로 필터링할 수 있습니다.|
|**분당 요청 수/정상 호스트**|개수|백 엔드 풀의 각 정상 멤버에서 받은 평균 요청 수(1분)입니다. *BackendPool HttpSettings* 차원을 사용하여 백 엔드 풀을 지정합니다.|


### <a name="application-gateway-v1-metrics"></a>Application Gateway v1 메트릭

#### <a name="application-gateway-metrics"></a>Application Gateway 메트릭

| 메트릭 | 단위 | 설명|
|:-------|:-----|:------------|
|**CPU 사용률**|백분율|Application Gateway 할당된 CPU 사용량을 표시합니다. 정상적인 조건에서 CPU 사용량은 Application Gateway 뒤에 호스트되는 웹 사이트에서 대기 시간이 발생하고 클라이언트 경험에 방해가 될 수 있으므로 일반적으로 90%를 초과해서는 안 됩니다. 인스턴스 수를 늘리거나 더 큰 SKU 크기로 이동하거나 둘 다 수행하여 Application Gateway 구성을 수정하여 CPU 사용량을 간접적으로 제어하거나 개선할 수 있습니다.|
|**현재 연결**|개수|Application Gateway와 설정된 현재 연결 수입니다.|
|**실패한 요청**|개수|연결 문제로 인해 실패한 요청 수입니다. 이 수에는 *요청 시간 초과* HTTP 설정 및 Application Gateway와 백 엔드 간의 연결 문제로 인해 실패한 요청이 초과되어 실패한 요청이 포함됩니다. 이 개수에는 사용할 수 있는 정상 백 엔드가 없기 때문에 오류가 포함되지 않습니다. 백 엔드의 4xx 및 5xx 응답도 이 메트릭의 일부로 간주되지 않습니다.|
|**응답 상태**|상태 코드|Application Gateway에서 반환된 HTTP 응답 상태입니다. 2xx, 3xx, 4xx 및 5xx 범주로 응답을 표시하도록 응답 상태 코드 분산을 더욱 세분화할 수 있습니다.|
|**처리량**|Bytes/sec|Application Gateway에서 제공하는 초당 바이트 수입니다.|
|**총 요청 수**|개수|Application Gateway가 제공하는 성공한 요청 수입니다. 각/특정 백 엔드 풀-http 설정 조합의 수를 표시하도록 요청 수를 추가로 필터링할 수 있습니다.|
|**웹 애플리케이션 방화벽 차단 요청 수**|개수|WAF에 의해 차단된 요청 수입니다.|
|**웹 애플리케이션 방화벽 차단 요청 배포**|개수|각 WAF 규칙 그룹/특정 WAF 규칙 그룹 또는 WAF 규칙 ID 조합당 개수를 표시하도록 필터링된 WAF에 의해 차단된 요청 수입니다.|
|**웹 애플리케이션 방화벽 총 규칙 배포**|개수|각 특정 WAF 규칙 그룹 또는 WAF 규칙 ID 조합당 수신된 요청 수입니다.|


<!-- Keep this text as-is -->
보다 자세한 정보는 [Azure Monitor에서 지원되는 모든 플랫폼 메트릭](../azure-monitor/essentials/metrics-supported.md)을 참조하세요.



## <a name="metric-dimensions"></a>메트릭 차원

<!-- REQUIRED. Please  keep headings in this order -->
<!-- If you have metrics with dimensions, outline it here. If you have no dimensions, say so.  Questions email azmondocs@microsoft.com -->

메트릭 차원에 대한 자세한 내용은 [다차원 메트릭](../azure-monitor/essentials/data-platform-metrics.md#multi-dimensional-metrics)을 참조하세요.


<!-- See https://docs.microsoft.com/azure/storage/common/monitor-storage-reference#metrics-dimensions for an example. Part is copied below. -->

Azure Application Gateway는 Azure Monitor의 일부 메트릭에 대한 차원을 지원합니다. 각 메트릭에는 해당 메트릭에 대해 사용 가능한 측정 기준을 설명하는 설명이 포함되어 있습니다.


## <a name="resource-logs"></a>리소스 로그
<!-- REQUIRED. Please  keep headings in this order -->

이 섹션에는 Azure Application Gateway에 대해 수집할 수 있는 리소스 로그 유형이 나열되어 있습니다. 

<!-- List all the resource log types you can have and what they are for -->  

[Azure Monitor에서 지원되는 모든 리소스 로그 범주 유형](../azure-monitor/essentials/resource-logs-schema.md) 목록을 참조하세요.

> [!NOTE]
> 성능 로그는 v1 SKU에 대해서만 사용할 수 있습니다. v2 SKU의 경우 성능 데이터에 대한 [메트릭](#metrics)을 사용합니다.

자세한 내용은 [Application Gateway에 대한 백 엔드 상태 및 진단 로그](application-gateway-diagnostics.md#access-log)를 참조하세요.

<!--  OPTION 2 -  Link to the resource logs as above, but work in extra information not found in the automated metric-supported reference article.  NOTE: YOU WILL NOW HAVE TO MANUALLY MAINTAIN THIS SECTION to make sure it stays in sync with the resource-log-categories link. You can group these sections however you want provided you include the proper links back to resource-log-categories article. 
-->

<!-- Example format. Add extra information -->

### <a name="application-gateway"></a>Application Gateway

리소스 공급자 및 유형: [Microsoft.Network/applicationGateways](../azure-monitor/essentials/resource-logs-categories.md#microsoftnetworkapplicationgateways)

| 범주 | 표시 이름 | 정보|
|:---------|:-------------|------------------|
| **Activitylog**   | 활동 로그 | 활동 로그 항목은 기본적으로 수집됩니다. [Azure 활동 로그](../azure-monitor/essentials/activity-log.md)(이전의 작업 로그 및 감사 로그)를 사용하여 Azure 구독에 제출된 모든 작업과 상태를 확인할 수 있습니다. |
|**ApplicationGatewayAccessLog**|액세스 로그| 이 로그를 사용하여 Application Gateway 액세스 패턴을 확인하고 중요한 정보를 분석할 수 있습니다. 여기에는 호출자의 IP 주소, 요청된 URL, 응답 대기 시간, 반환 코드, 바이트 입출력이 포함됩니다. 액세스 로그는 60초마다 수집됩니다. 이 로그에는 Application Gateway 인스턴스당 하나의 레코드가 포함됩니다. Application Gateway 인스턴스는 instanceId 속성으로 식별됩니다.|
| **ApplicationGatewayPerformanceLog**|성능 로그|이 로그를 사용하여 Application Gateway 인스턴스를 수행하는 방법을 확인할 수 있습니다. 이 로그는 인스턴스 단위로 처리된 총 요청 수, 처리량(바이트), 실패한 요청 수, 정상 및 비정상 백 엔드 인스턴스 수 등의 성능 정보를 캡처합니다. 성능 로그는 60초마다 수집됩니다. 성능 로그는 v1 SKU에 대해서만 사용할 수 있습니다. v2 SKU의 경우 성능 데이터에 대한 [메트릭](#metrics)을 사용합니다.|
|**ApplicationGatewayFirewallLog**|방화벽 로그|이 로그를 사용하면 웹 애플리케이션 방화벽으로 구성된 애플리케이션 게이트웨이의 검색 모드 또는 방지 모드를 통해 로깅된 요청을 확인할 수 있습니다. 방화벽 로그는 60초마다 수집됩니다.|


## <a name="azure-monitor-logs-tables"></a>Azure Monitor Logs 테이블
<!-- REQUIRED. Please keep heading in this order -->

이 섹션에서는 Azure Application Gateway와 관련된 모든 Azure Monitor 로그 Kusto 테이블을 참조하고 로그 분석 쿼리를 사용할 수 있습니다. 


<!-- OPTION 1 - Minimum -  Link to relevant bookmarks in https://docs.microsoft.com/azure/azure-monitor/reference/tables/tables-resourcetype where your service tables are listed. These files are auto generated from the REST API.   If this article is missing tables that you and the PM know are available, both of you contact azmondocs@microsoft.com.  
-->

<!-- Example format. There should be AT LEAST one Resource Provider/Resource Type here. -->

|리소스 종류 | 참고 |
|-------|-----|
| [Application Gateway](/azure/azure-monitor/reference/tables/tables-resourcetype#application-gateways) |AzureActivity, AzureDiagnostics 및 AzureMetrics 포함 |


모든 Azure Monitor Logs/Log Analytics 테이블에 대한 참조는 [Azure Monitor 로그 테이블 참조](/azure/azure-monitor/reference/tables/tables-resourcetype)를 확인하세요.

### <a name="diagnostics-tables"></a>진단 테이블
<!-- REQUIRED. Please keep heading in this order -->
<!-- If your service uses the AzureDiagnostics table in Azure Monitor Logs / Log Analytics, list what fields you use and what they are for. Azure Diagnostics is over 500 columns wide with all services using the fields that are consistent across Azure Monitor and then adding extra ones just for themselves.  If it uses service specific diagnostic table, refers to that table. If it uses both, put both types of information in. Most services in the future will have their own specific table. If you have questions, contact azmondocs@microsoft.com -->

Azure Application Gateway는 [Azure Diagnostics](/azure/azure-monitor/reference/tables/azurediagnostics) 테이블을 사용하여 리소스 로그 정보를 저장합니다. 다음 열이 관련됩니다.

**Azure Diagnostics**

| 속성 | 설명 |
|:--- |:---|
requestUri_s | 클라이언트 요청의 URI입니다.|
메시지 | "SQL 삽입 공격"과 같은 정보 메시지|
userAgent_s | 클라이언트 요청의 사용자 에이전트 세부 정보|
ruleName_s | 이 요청을 제공하는 데 사용되는 요청 라우팅 규칙|
httpMethod_s | 클라이언트 요청의 HTTP 메서드|
instanceId_s | 평가를 위해 클라이언트 요청이 라우팅되는 Appgw 인스턴스|
httpVersion_s | 클라이언트 요청의 HTTP 버전|
clientIP_s | 요청이 이루어진 IP|
host_s | 클라이언트 요청의 호스트 헤더|
requestQuery_s | 클라이언트 요청의 일부로 쿼리 문자열|
sslEnabled_s | 클라이언트 요청에 SSL이 활성화되어 있나요?|


## <a name="see-also"></a>참고 항목

<!-- replace below with the proper link to your main monitoring service article -->
- 모니터링 Azure Application Gateway 대한 설명은 [Azure Azure Application Gateway](monitor-application-gateway.md) 모니터링을 참조하세요.
- Azure 리소스 모니터링에 대한 자세한 내용은 [Azure Monitor를 사용하여 Azure 리소스 모니터링](../azure-monitor/essentials/monitor-azure-resource.md)을 참조하세요.