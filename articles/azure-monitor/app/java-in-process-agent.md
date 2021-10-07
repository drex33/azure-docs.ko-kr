---
title: 'Azure Monitor: Application Insights Java'
description: 코드를 수정할 필요 없이 모든 환경에서 실행되는 Java 애플리케이션에 대한 애플리케이션 성능 모니터링입니다. 분산 추적 및 애플리케이션 맵.
ms.topic: conceptual
ms.date: 06/24/2021
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: 0aa8fdf150560058331c267d78967abf257bd122
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129617248"
---
# <a name="java-codeless-application-monitoring-with-azure-monitor-application-insights"></a>Azure Monitor Application Insights를 사용 하 여 Java 코드 없는 응용 프로그램 모니터링

> [!NOTE]
> 이전 2.x 문서를 찾고 있다면 [여기](./java-2x-get-started.md)로 이동하세요.

Java 코드리스 애플리케이션 모니터링은 단순성에 관한 것입니다. 코드 변경이 없으며 Java 에이전트는 몇 가지 구성 변경만으로 활성화할 수 있습니다.

Java 에이전트는 모든 환경에서 작동하며 모든 Java 애플리케이션을 모니터링할 수 있도록 합니다. 즉, VM, 온-프레미스, Windows, Linux에서 Java 앱을 실행하고 있는지 여부에 관계없이 Application Insights Java 에이전트가 앱을 모니터링합니다.

Application Insights Java 3.x 에이전트가 요청, 종속성 및 로그를 모두 자동으로 수집하기 때문에 Application Insights Java 2.x SDK를 애플리케이션에 추가하는 것은 더 이상 필요하지 않습니다.

애플리케이션에서 사용자 지정 원격 분석을 계속 보낼 수 있습니다. 3\.x 에이전트는 자동으로 수집된 모든 원격 분석과 함께 이를 추적하고 상관 관계를 지정합니다.

3\.x 에이전트는 Java 8 이상을 지원합니다.

## <a name="quickstart"></a>빠른 시작

Azure Monitor Application Insights에서 Java 코드 없는 응용 프로그램 모니터링을 사용 하는 방법에 대 한 빠른 시작을 보려면 다음 단계를 따르세요.

### <a name="step-1-download-the-agent"></a>1 단계: 에이전트 다운로드

Jar 파일을 다운로드 하기 전에 다음 구성 요소를 검토 합니다.

> [!WARNING]
> 
> - **3.0 Preview에서 업그레이드 하는 경우**
>
>    모두 소문자로 된 파일 이름 자체 외에도 json 구조가 완전히 변경되었으므로 아래 모든 [구성 옵션](./java-standalone-config.md)을 신중히 검토하세요.
> 
> - **3.0. x에서 업그레이드 하는 경우**
> 
>    이제 작업 이름과 요청 원격 분석 이름에 http 메서드(`GET`, `POST` 등)가 접두사로 붙습니다.
>    이전 값에 의존 하는 경우 사용자 지정 대시보드 또는 경고에 영향을 줄 수 있습니다.
>    자세한 내용은 [3.1.0 릴리스 정보](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0)를 참조하세요.
>
> - **3.1. x에서 업그레이드 하는 경우**
> 
>    이제 필드에 전체 (삭제 된) 쿼리가 있는 데이터베이스 종속성 이름이 더 간결 합니다 `data` . 이제 및 HTTP 종속성 이름이 더 설명적입니다.
>    이전 값에 의존 하는 경우 사용자 지정 대시보드 또는 경고에 영향을 줄 수 있습니다.
>    자세한 내용은 [3.2.0 릴리스 정보](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0) 를 참조 하세요.

[Applicationinsights-agent-3.2.0](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.0/applicationinsights-agent-3.2.0.jar) 파일을 다운로드 합니다.

### <a name="step-2-point-the-jvm-to-the-agent"></a>2 단계: 에이전트에 대 한 JVM 가리키기

애플리케이션의 JVM 인수에 `-javaagent:path/to/applicationinsights-agent-3.2.0.jar` 추가 

애플리케이션의 JVM 인수 구성에 관한 도움말은 [JVM 인수 업데이트를 위한 팁](./java-standalone-arguments.md)을 참조하세요.

### <a name="step-3-point-the-agent-to-your-application-insights-resource"></a>3 단계: 에이전트에 Application Insights 리소스 가리키기

Application Insights 리소스가 아직 없는 경우 [리소스 만들기 가이드](./create-new-resource.md)의 단계에 따라 새 리소스를 만들 수 있습니다.

환경 변수를 설정하여 에이전트가 Application Insights 리소스를 가리키도록 합니다.

```console
APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
```

또는 `applicationinsights.json`이라는 구성 파일을 작성하고 다음 내용으로 `applicationinsights-agent-3.2.0.jar`과 동일한 디렉터리에 배치합니다.

```json
{
  "connectionString": "InstrumentationKey=..."
}
```

Application Insights 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 연결 문자열":::

### <a name="step-4-thats-it"></a>4 단계: 이것이 끝났습니다.

이제 애플리케이션을 시작하고 Azure Portal의 Application Insights 리소스로 이동하여 모니터링 데이터를 확인합니다.

> [!NOTE]
> 모니터링 데이터가 포털에 표시 되는 데 몇 분 정도 걸릴 수 있습니다.


## <a name="configuration-options"></a>구성 옵션

`applicationinsights.json` 파일에서 다음을 추가로 구성할 수 있습니다.

* 클라우드 역할 이름
* 클라우드 역할 인스턴스
* 샘플링
* JMX 메트릭
* 사용자 지정 차원
* 원격 분석 프로세서(미리 보기)
* 자동 수집된 로깅
* 자동 수집된 마이크로미터 메트릭(Spring Boot Actuator 메트릭 포함)
* 하트비트
* HTTP 프록시
* 자체 진단

자세한 내용은 [구성 옵션](./java-standalone-config.md)을 참조하세요.

## <a name="auto-collected-requests"></a>자동으로 수집된 요청

* JMS 소비자
* Kafka 소비자
* Netty/WebFlux
* 서블릿
* Spring 일정

## <a name="auto-collected-dependencies"></a>자동으로 수집된 종속성

자동으로 수집된 종속성 및 다운스트림 분산 추적 전파:

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS-RS 클라이언트
* Jetty HttpClient
* JMS
* Kafka
* Netty 클라이언트
* OkHttp

자동으로 수집된 종속성(다운스트림 분산 추적 전파가 없음):

* Cassandra
* JDBC
* MongoDB(비동기 및 동기)
* Redis(Lettuce 및 Jedis)

## <a name="auto-collected-logs"></a>자동으로 수집된 로그

* java.util.logging
* Log4j(MDC 속성 포함)
* SLF4J/Logback(MDC 속성 포함)

## <a name="auto-collected-metrics"></a>자동으로 수집된 메트릭

* Micrometer(Spring Boot Actuator 메트릭 포함)
* JMX 메트릭

## <a name="azure-sdks"></a>Azure SDK

이러한 Azure Sdk에서 내보낸 원격 분석은 기본적으로 자동으로 수집 됩니다.

* [App Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10 이상
* [Cognitive Search](/java/api/overview/azure/search-documents-readme) 11.3.0 이상
* [Communication Chat](/java/api/overview/azure/communication-chat-readme) 1.0.0 이상
* [Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0 이상
* [Communication Identity](/java/api/overview/azure/communication-identity-readme) 1.0.0 이상
* [Communication Phone Numbers](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0 이상
* [Communication SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0 이상
* [Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0+
* [Digital Twins - Core](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0 이상
* [Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0+
* [Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0 이상
* [Event Hubs - Azure Blob Storage 검사점 저장소](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1+
* [Form Recognizer](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6 이상
* [ID](/java/api/overview/azure/identity-readme) 1.2.4+
* [Key Vault - Certificates](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6 이상
* [Key Vault - Keys](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6 이상
* [Key Vault - Secrets](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6 이상
* [Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0+
* [Storage - Blobs](/java/api/overview/azure/storage-blob-readme) 12.11.0 이상
* [Storage - Blobs Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0 이상
* [Storage - Blobs Cryptography](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0 이상
* [Storage - Common](/java/api/overview/azure/storage-common-readme) 12.11.0 이상
* [Storage - Files Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0 이상
* [Storage - Files Shares](/java/api/overview/azure/storage-file-share-readme) 12.9.0 이상
* [Storage - Queues](/java/api/overview/azure/storage-queue-readme) 12.9.0 이상
* [Text Analytics](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4 이상

[//]: # "https://azure.github.io/azure-sdk/releases/latest/java.html"에서 스크랩한 위의 이름 및 링크
[//]: # "및 버전은 azure-core 1.14.0에 구축된 Maven Central의 가장 오래된 버전에 대해 수동으로 동기화되었습니다."
[//]: # ""
[//]: # "var table = document.querySelector('#tg-sb-content > div > table')"
[//]: # "var str = ''"
[//]: # "for (var i = 1, row; row = table.rows[i]; i++) {"
[//]: # "  var name = row.cells[0].getElementsByTagName('div')[0].textContent.trim()"
[//]: # "  var stableRow = row.cells[1]"
[//]: # "  var versionBadge = stableRow.querySelector('.badge')"
[//]: # "  if (!versionBadge) {"
[//]: # "    계속"
[//]: # "  }"
[//]: # "  var version = versionBadge.textContent.trim()"
[//]: # "  var link = stableRow.querySelectorAll('a')[2].href"
[//]: # "  str += '* [' + name + '](' + link + ') ' + version + '\n'"
[//]: # "}"
[//]: # "console.log(str)"

## <a name="send-custom-telemetry-from-your-application"></a>애플리케이션에서 사용자 지정 원격 분석 보내기

Java 3.x Application Insights의 목표는 표준 API를 사용하여 사용자 지정 원격 분석을 보낼 수 있도록 하는 것입니다.

지금까지 널리 사용되는 로깅 프레임워크인 Micrometer 및 Application Insights Java 2.x SDK를 지원합니다.
Application Insights Java 3.x는 이러한 API를 통해 전송되는 원격 분석을 자동으로 캡처하고 자동으로 수집된 원격 분석과의 상관 관계를 지정합니다.

### <a name="supported-custom-telemetry"></a>지원되는 사용자 지정 원격 분석

아래 표에서는 Java 3.x 에이전트를 보완하기 위해 사용할 수 있는 현재 지원되는 사용자 지정 원격 분석 유형을 나타냅니다. 요약하면 사용자 지정 메트릭은 Micrometer를 통해 지원되고 사용자 지정 예외 및 추적은 로깅 프레임워크를 통해 사용하도록 설정될 수 있으며 모든 유형의 사용자 지정 원격 분석은 [Application Insights Java 2.x SDK](#send-custom-telemetry-using-the-2x-sdk)를 통해 지원됩니다.

|                     | 마이크로미터 | Log4j, logback, JUL | 2.x SDK |
|---------------------|------------|---------------------|---------|
| **사용자 지정 이벤트**   |            |                     |  예    |
| **사용자 지정 메트릭**.  |  예       |                     |  yes    |
| **종속성**    |            |                     |  예    |
| **예외**      |            |  예                |  예    |
| **페이지 보기**      |            |                     |  예    |
| **요청**        |            |                     |  예    |
| **Traces**          |            |  예                |  예    |

이제 Application Insights 3.x를 사용하여 SDK를 릴리스할 계획을 세울 예정입니다.

Application Insights Java 3.x는 Application Insights Java 2.x SDK로 전송되는 원격 분석을 이미 수신 대기하고 있습니다. 이 기능은 기존 2.x 사용자를 위한 업그레이드 스토리의 중요한 부분으로, OpenTelemetry API가 GA될 때까지 사용자 지정 원격 분석 지원의 중요한 격차를 메꿉니다.

### <a name="send-custom-metrics-using-micrometer"></a>Micrometer를 사용하여 사용자 지정 메트릭 보내기

애플리케이션에 Micrometer를 추가합니다.

```xml
<dependency>
  <groupId>io.micrometer</groupId>
  <artifactId>micrometer-core</artifactId>
  <version>1.6.1</version>
</dependency>
```

Micrometer [전역 레지스트리](https://micrometer.io/docs/concepts#_global_registry)를 사용하여 미터를 만듭니다.

```java
static final Counter counter = Metrics.counter("test_counter");
```

이를 사용하여 메트릭을 기록합니다.

```java
counter.increment();
```

### <a name="send-custom-traces-and-exceptions-using-your-favorite-logging-framework"></a>선호하는 로깅 프레임워크를 사용하여 사용자 지정 추적 및 예외 보내기

Log4j, Logback 및 java.util.logging은 자동으로 계측되며 이러한 로깅 프레임워크를 통해 수행된 로깅은 추적 및 예외 원격 분석으로 자동 수집됩니다.

기본적으로 로깅은 정보 수준 이상에서 수행되는 경우에만 수집됩니다.
이 수준을 변경하는 방법은 [구성 옵션](./java-standalone-config.md#auto-collected-logging)을 참조하세요.

로그에 사용자 지정 차원을 연결하려는 경우 [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html) 또는 [Logback MDC](http://logback.qos.ch/manual/mdc.html)를 사용할 수 있으며, java 3.x를 Application Insights 추적 및 예외 원격 분석에서 해당 MDC 속성을 사용자 지정 차원으로 자동 캡처합니다.

### <a name="send-custom-telemetry-using-the-2x-sdk"></a>2\.x SDK를 사용하여 사용자 지정 원격 분석 보내기

애플리케이션에 `applicationinsights-core-2.6.3.jar`을 추가합니다(모든 2.x 버전은 Application Insights Java 3.x에서 지원되지만 원하는 경우 최신 버전을 사용하는 것이 좋습니다).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-core</artifactId>
  <version>2.6.3</version>
</dependency>
```

TelemetryClient를 만듭니다.

  ```java
static final TelemetryClient telemetryClient = new TelemetryClient();
```

이를 사용하여 사용자 지정 원격 분석을 보냅니다.

##### <a name="events"></a>이벤트

```java
telemetryClient.trackEvent("WinGame");
```

##### <a name="metrics"></a>메트릭

```java
telemetryClient.trackMetric("queueLength", 42.0);
```

##### <a name="dependencies"></a>종속성

```java
boolean success = false;
long startTime = System.currentTimeMillis();
try {
    success = dependency.call();
} finally {
    long endTime = System.currentTimeMillis();
    RemoteDependencyTelemetry telemetry = new RemoteDependencyTelemetry();
    telemetry.setSuccess(success);
    telemetry.setTimestamp(new Date(startTime));
    telemetry.setDuration(new Duration(endTime - startTime));
    telemetryClient.trackDependency(telemetry);
}
```

##### <a name="logs"></a>로그

```java
telemetryClient.trackTrace(message, SeverityLevel.Warning, properties);
```

##### <a name="exceptions"></a>예외

```java
try {
    ...
} catch (Exception e) {
    telemetryClient.trackException(e);
}
```

### <a name="add-request-custom-dimensions-using-the-2x-sdk"></a>2\.x SDK를 사용하여 요청 사용자 지정 차원 추가

> [!NOTE]
> 이 기능은 3.0.2 이상에만 해당됩니다.

애플리케이션에 `applicationinsights-web-2.6.3.jar`을 추가합니다(모든 2.x 버전은 Application Insights Java 3.x에서 지원되지만 원하는 경우 최신 버전을 사용하는 것이 좋습니다).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

코드에 사용자 지정 차원을 추가합니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getProperties().put("mydimension", "myvalue");
```

### <a name="set-the-request-telemetry-user_id-using-the-2x-sdk"></a>2\.x SDK를 사용하여 요청 원격 분석 user_Id 설정

> [!NOTE]
> 이 기능은 3.0.2 이상에만 해당됩니다.

애플리케이션에 `applicationinsights-web-2.6.3.jar`을 추가합니다(모든 2.x 버전은 Application Insights Java 3.x에서 지원되지만 원하는 경우 최신 버전을 사용하는 것이 좋습니다).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

코드에서 `user_Id`를 설정합니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.getContext().getUser().setId("myuser");
```

### <a name="override-the-request-telemetry-name-using-the-2x-sdk"></a>2\.x SDK를 사용하여 요청 원격 분석 이름을 재정의합니다.

> [!NOTE]
> 이 기능은 3.0.2 이상에만 해당됩니다.

애플리케이션에 `applicationinsights-web-2.6.3.jar`을 추가합니다(모든 2.x 버전은 Application Insights Java 3.x에서 지원되지만 원하는 경우 최신 버전을 사용하는 것이 좋습니다).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

코드에 이름을 설정합니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
requestTelemetry.setName("myname");
```

### <a name="get-the-request-telemetry-id-and-the-operation-id-by-using-the-2x-sdk"></a>2.x SDK를 사용 하 여 요청 원격 분석 ID 및 작업 ID를 가져옵니다.

> [!NOTE]
> 이 기능은 3.0.3 이상에만 해당됩니다.

애플리케이션에 `applicationinsights-web-2.6.3.jar`을 추가합니다(모든 2.x 버전은 Application Insights Java 3.x에서 지원되지만 원하는 경우 최신 버전을 사용하는 것이 좋습니다).

```xml
<dependency>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>applicationinsights-web</artifactId>
  <version>2.6.3</version>
</dependency>
```

그리고 코드에서 요청 원격 분석 ID 및 작업 ID를 가져옵니다.

```java
import com.microsoft.applicationinsights.web.internal.ThreadContext;

RequestTelemetry requestTelemetry = ThreadContext.getRequestTelemetryContext().getHttpRequestTelemetry();
String requestId = requestTelemetry.getId();
String operationId = requestTelemetry.getContext().getOperation().getId();
```
