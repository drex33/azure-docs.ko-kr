---
title: Azure Spring Cloud에서 로그 및 메트릭 분석 | Microsoft Docs
description: Azure Spring Cloud에서 진단 데이터를 분석하는 방법 알아보기
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 8a462809ca7be524e0e5149808bdcbe28f49dd77
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128649041"
---
# <a name="analyze-logs-and-metrics-with-diagnostics-settings"></a>진단 설정을 사용하여 로그 및 메트릭 분석

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure Spring Cloud의 진단 기능을 사용하여 다음 서비스 중 하나를 통해 로그 및 메트릭을 분석할 수 있습니다.

* 데이터가 Azure Storage에 기록되는 Azure Log Analytics를 사용합니다. Log Analytics로 로그를 내보낼 때 지연이 발생합니다.
* 감사 또는 수동 검사를 위해 저장소 계정에 로그를 저장 합니다. 보존 시간을 일 단위로 지정할 수 있습니다.
* 타사 서비스 또는 사용자 지정 분석 솔루션에서 수집할 수 있도록 로그를 이벤트 허브로 스트림합니다.

모니터링할 로그 범주 및 메트릭 범주를 선택합니다.

> [!TIP]
> 로그를 스트림하시겠습니까? 이 [Azure CLI 명령](/cli/azure/spring-cloud/app#az_spring_cloud_app_logs)을 확인하세요!

## <a name="logs"></a>로그

|로그 | Description |
|----|----|
| **ApplicationConsole** | 모든 고객 애플리케이션의 콘솔 로그입니다. |
| **SystemLogs** | 현재 이 범주에는 [Spring Cloud Config Server](https://cloud.spring.io/spring-cloud-config/reference/html/#_spring_cloud_config_server) 로그만 있습니다. |
| **IngressLogs** | 모든 고객의 응용 프로그램에 대 한 [수신 로그](#show-ingress-log-entries-containing-a-specific-host) 및 액세스 로그만 |

## <a name="metrics"></a>메트릭

메트릭의 전체 목록은 [Spring Cloud 메트릭](./concept-metrics.md#user-metrics-options)을 참조하세요.

시작하려면 이러한 서비스 중 하나를 사용하도록 설정하여 데이터를 수신합니다. Log Analytics 구성에 대해 자세히 알아보려면 [Azure Monitor에서 Log Analytics로 시작](../azure-monitor/logs/log-analytics-tutorial.md)을 참조하세요.

## <a name="configure-diagnostics-settings"></a>진단 설정 구성

1. Azure Portal에서 Azure Spring Cloud 인스턴스로 이동합니다.
1. **진단 설정** 옵션을 선택한 다음, **진단 설정 추가** 를 선택합니다.
1. 설정에 사용할 이름을 입력한 다음, 로그를 보낼 위치를 선택합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.
    * **스토리지 계정에 보관**
    * **이벤트 허브로 스트림**
    * **Log Analytics에 보내기**

1. 모니터링할 로깅 범주 및 메트릭 범주를 선택한 다음, 보존 시간(일)을 지정합니다. 보존 시간은 스토리지 계정에만 적용됩니다.
1. **저장** 을 선택합니다.

> [!NOTE]
> 1. 로그 또는 메트릭이 내보내지는 시점과 스토리지 계정, 이벤트 허브 또는 Log Analytics에 표시되는 시점 사이에는 최대 15분 간격이 있을 수 있습니다.
> 1. Azure Spring Cloud 인스턴스가 삭제되거나 이동되면 이 작업은 **진단 설정** 리소스에 종속되지 않습니다. **진단 설정** 리소스는 부모(예: Azure Spring Cloud 인스턴스)에 대해 작업을 수행하기 전에 수동으로 삭제해야 합니다. 그렇지 않으면 새 Azure Spring Cloud 인스턴스가 삭제된 것과 동일한 리소스 ID로 프로비전되거나 Azure Spring Cloud 인스턴스가 다시 이동되는 경우 이전 **진단 설정** 리소스는 계속 확장합니다.

## <a name="view-the-logs-and-metrics"></a>로그 및 메트릭 보기

다음 제목에 설명된 것처럼 다양한 방법으로 로그 및 메트릭을 볼 수 있습니다.

### <a name="use-the-logs-blade"></a>로그 블레이드 사용

1. Azure Portal에서 Azure Spring Cloud 인스턴스로 이동합니다.
1. **로그 검색** 창을 열려면 **로그** 를 선택합니다.
1. **테이블** 검색 상자에서
   * 로그를 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

   * 메트릭을 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 검색 결과를 보려면 **실행** 을 선택합니다.

### <a name="use-log-analytics"></a>Log Analytics 사용

1. Azure Portal의 왼쪽 창에서 **Log Analytics** 를 선택합니다.
1. 진단 설정을 추가할 때 선택한 Log Analytics 작업 영역을 선택합니다.
1. **로그 검색** 창을 열려면 **로그** 를 선택합니다.
1. **테이블** 검색 상자에서
   * 로그를 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AppPlatformLogsforSpring
    | limit 50
    ```

    * 메트릭을 보려면 다음과 같은 간단한 쿼리를 입력합니다.

    ```sql
    AzureMetrics
    | limit 50
    ```

1. 검색 결과를 보려면 **실행** 을 선택합니다.
1. 필터 조건을 설정하여 특정 애플리케이션 또는 인스턴스의 로그를 검색할 수 있습니다.

    ```sql
    AppPlatformLogsforSpring
    | where ServiceName == "YourServiceName" and AppName == "YourAppName" and InstanceName == "YourInstanceName"
    | limit 50
    ```

    > [!NOTE]
    > `==`는 대/소문자를 구분하지만 `=~`는 그렇지 않습니다.

Log Analytics에서 사용되는 쿼리 언어에 대한 자세한 내용은 [Azure Monitor 로그 쿼리](/azure/data-explorer/kusto/query/)를 참조하세요. 중앙 집중식 클라이언트에서 모든 Log Analytics 로그를 쿼리하려면 [Azure Data Explorer](/azure/data-explorer/query-monitor-data)를 확인하세요.

### <a name="use-your-storage-account"></a>스토리지 계정 사용

1. Azure Portal의 왼쪽 탐색 패널 또는 검색 상자에서 **스토리지 계정** 을 찾습니다.
1. 진단 설정을 추가할 때 선택한 스토리지 계정을 선택합니다.
1. **Blob 컨테이너** 창을 열려면 **Blob** 을 선택합니다.
1. 애플리케이션 로그를 검토하려면 **insights-logs-applicationconsole** 이라는 컨테이너를 검색합니다.
1. 애플리케이션 메트릭을 검토하려면 **insights-metrics-pt1m** 이라는 컨테이너를 검색합니다.

스토리지 계정에 진단 정보를 보내는 방법에 대한 자세한 내용은 [Azure Storage에서 진단 데이터 저장 및 보기](../storage/common/storage-introduction.md)를 참조하세요.

### <a name="use-your-event-hub"></a>이벤트 허브 사용

1. Azure Portal의 왼쪽 탐색 패널 또는 검색 상자에서 **Event Hubs** 를 찾습니다.

1. 진단 설정을 추가할 때 선택한 이벤트 허브를 검색하고 선택합니다.
1. **이벤트 허브 목록** 창을 열려면 **Event Hubs** 를 선택합니다.
1. 애플리케이션 로그를 검토하려면 **insights-logs-applicationconsole** 이라는 이벤트 허브를 검색합니다.
1. 애플리케이션 메트릭을 검토하려면 **insights-metrics-pt1m** 이라는 이벤트 허브를 검색합니다.

이벤트 허브에 진단 정보를 보내는 방법에 대한 자세한 내용은 [Event Hubs를 사용하여 실행 부하 과다 경로에서 Azure Diagnostics 데이터 스트리밍](../azure-monitor/agents/diagnostics-extension-stream-event-hubs.md)을 참조하세요.

## <a name="analyze-the-logs"></a>로그 분석

Azure Log Analytics는 Kusto 엔진과 함께 실행되므로 로그에서 분석을 쿼리할 수 있습니다. Kusto를 사용하여 로그를 쿼리하는 방법에 대한 간략한 소개는 [Log Analytics 자습서](../azure-monitor/logs/log-analytics-tutorial.md)를 검토하세요.

애플리케이션 로그는 애플리케이션의 상태, 성능 등에 대해 중요한 정보와 자세한 로그를 제공합니다. 다음 섹션에서는 애플리케이션의 현재 및 과거 상태를 파악하는 데 도움이 되는 몇 가지 간단한 쿼리를 소개합니다.

### <a name="show-application-logs-from-azure-spring-cloud"></a>Azure Spring Cloud의 애플리케이션 로그 표시

Azure Spring Cloud의 애플리케이션 로그 목록을 검토하려면 먼저 표시된 최신 로그를 시간별로 정렬한 후 다음 쿼리를 실행합니다.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| sort by TimeGenerated desc
```

### <a name="show-logs-entries-containing-errors-or-exceptions"></a>오류 또는 예외를 포함하는 로그 항목 표시

오류 또는 예외를 언급하는 정렬되지 않은 로그 항목을 검토하려면 다음 쿼리를 실행합니다.

```sql
AppPlatformLogsforSpring
| project TimeGenerated , ServiceName , AppName , InstanceName , Log
| where Log contains "error" or Log contains "exception"
```

이 쿼리를 사용하여 오류를 찾거나, 쿼리 용어를 수정하여 특정 오류 코드 또는 예외를 찾습니다.

### <a name="show-the-number-of-errors-and-exceptions-reported-by-your-application-over-the-last-hour"></a>지난 1시간 동안 애플리케이션에서 보고된 오류 및 예외 수 표시

지난 1시간 동안 애플리케이션에서 기록한 오류 및 예외 수를 표시하는 원형 차트를 만들려면 다음 쿼리를 실행합니다.

```sql
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where Log contains "error" or Log contains "exception"
| summarize count_per_app = count() by AppName
| sort by count_per_app desc
| render piechart
```

### <a name="show-ingress-log-entries-containing-a-specific-host"></a>특정 호스트를 포함 하는 수신 로그 항목 표시

특정 호스트에 의해 생성 된 로그 항목을 검토 하려면 다음 쿼리를 실행 합니다.

```sql
AppPlatformIngressLogs
| where TimeGenerated > ago(1h) and Host == "ingress-asc.test.azuremicroservices.io" 
| project TimeGenerated, RemoteIP, Host, Request, Status, BodyBytesSent, RequestTime, ReqId, RequestHeaders
| sort by TimeGenerated
```

이 쿼리를 사용 하 여 `Status` `RequestTime` 이 특정 호스트의 수신 로그에 대 한 응답, 및 기타 속성을 찾습니다. 

### <a name="show-ingress-log-entries-for-a-specific-requestid"></a>특정 requestId의 수신 로그 항목 표시

특정 값에 대 한 로그 항목을 검토 하려면 `requestId` *\<request_ID>* 다음 쿼리를 실행 합니다.

```sql
AppPlatformIngressLogs
| where TimeGenerated > ago(1h) and ReqId == "<request_ID>" 
| project TimeGenerated, RemoteIP, Host, Request, Status, BodyBytesSent, RequestTime, ReqId, RequestHeaders
| sort by TimeGenerated
```

### <a name="learn-more-about-querying-application-logs"></a>애플리케이션 로그 쿼리에 대한 자세한 정보

Azure Monitor는 Log Analytics를 사용하여 애플리케이션 로그를 쿼리하기 위한 광범위한 지원을 제공합니다. 이 서비스에 대한 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조하세요. 애플리케이션 로그를 분석하는 쿼리를 빌드하는 방법에 대한 자세한 내용은 [Azure Monitor의 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)를 참조하세요.

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

### <a name="how-do-i-convert-multi-line-java-stack-traces-into-a-single-line"></a>여러 줄 Java 스택 추적을 한 줄로 변환 어떻게 할까요?

여러 줄 스택 추적을 한 줄로 변환하는 방법에 대한 해결 방법이 있습니다. Java 로그 출력을 수정하여 스택 추적 메시지의 서식을 다시 지정하고 줄 바꿈 문자를 토큰으로 바꿀 수 있습니다. Java Logback 라이브러리를 사용하는 경우 다음과 같이 `%replace(%ex){'[\r\n]+', '\\n'}%nopex`를 추가하여 스택 추적 메시지의 서식을 다시 지정할 수 있습니다.

```xml
<configuration>
    <appender name="CONSOLE" class="ch.qos.logback.core.ConsoleAppender">
        <encoder>
            <pattern>
                level: %level, message: "%logger{36}: %msg", exceptions: "%replace(%ex){'[\r\n]+', '\\n'}%nopex"%n
            </pattern>
        </encoder>
    </appender>
    <root level="INFO">
        <appender-ref ref="CONSOLE"/>
    </root>
</configuration>
```

그런 다음 아래와 같이 Log Analytics의 줄 바꿈 문자로 토큰을 바꿀 수 있습니다.

```sql
AppPlatformLogsforSpring
| extend Log = array_strcat(split(Log, '\\n'), '\n')
```

다른 Java 로그 라이브러리에 대해 동일한 전략을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작: 첫 번째 Azure Spring Cloud 애플리케이션 배포](./quickstart.md)
