---
title: 'Azure Monitor: Application Insights Java'
description: 코드를 수정할 필요 없이 모든 환경에서 실행되는 Java 애플리케이션에 대한 애플리케이션 성능 모니터링입니다. 분산 추적 및 애플리케이션 맵.
ms.topic: conceptual
ms.date: 06/24/2021
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: a1c40fde4c04e51ea3967747827fe7fc9831c915
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133265104"
---
# <a name="azure-monitor-opentelemetry-based-auto-instrumentation-for-java-applications"></a>Java 애플리케이션에 대한 OpenTelemetry 기반 자동 계측 Azure Monitor

이 문서에서는 OpenTelemetry 기반 Azure Monitor Java 제품을 사용하도록 설정하고 구성하는 방법을 설명합니다. 이 문서의 지침을 완료하면 Azure Monitor 애플리케이션 Insights 사용하여 애플리케이션을 모니터링할 수 있습니다.

## <a name="get-started"></a>시작

코드를 변경하지 않고 Java 자동 계측을 사용하도록 설정할 수 있습니다.

### <a name="prerequisites"></a>필수 구성 요소

- Java 8+를 사용하는 Java 애플리케이션
- Azure 구독: [무료로 Azure 구독 만들기](https://azure.microsoft.com/free/)
- 애플리케이션 Insights 리소스: [애플리케이션 Insights 리소스 만들기](create-workspace-resource.md#create-workspace-based-resource)

### <a name="enable-azure-monitor-application-insights"></a>Azure Monitor 애플리케이션 Insights 사용

이 섹션에서는 자동 계측 jar 파일을 다운로드하는 방법을 보여줍니다.

#### <a name="download-the-jar-file"></a>jar 파일 다운로드

[applicationinsights-agent-3.2.3.jar 파일을 다운로드합니다.](https://github.com/microsoft/ApplicationInsights-Java/releases/download/3.2.3/applicationinsights-agent-3.2.3.jar)

> [!WARNING]
> 
> 3.0 미리 보기에서 업그레이드하는 경우:
>
>    - 모든 [구성 옵션을](./java-standalone-config.md) 신중하게 검토합니다. JSON 구조가 완전히 변경되었습니다. 이제 파일 이름이 모두 소문자입니다.
> 
> 3.0.x에서 업그레이드하는 경우:
> 
>    - 작업 이름 및 요청 원격 분석 이름에는 이제 및 와 같은 HTTP 메서드가 접두사로 `GET` `POST` 추가됩니다.
>    이 변경 내용은 이전 값에 의존하는 경우 사용자 지정 대시보드 또는 경고에 영향을 줄 수 있습니다.
>    자세한 내용은 [3.1.0 릴리스 정보 를 참조하세요.](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.1.0)
>
> 3.1.x에서 업그레이드하는 경우:
> 
>    - 이제 데이터베이스 종속성 이름은 필드에 여전히 있는 전체(sanitized) 쿼리와 더 `data` 간결합니다. HTTP 종속성 이름은 이제 더 설명적입니다.
>    이 변경 내용은 이전 값에 의존하는 경우 사용자 지정 대시보드 또는 경고에 영향을 줄 수 있습니다.
>    자세한 내용은 [3.2.0 릴리스 정보 를 참조하세요.](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0)

#### <a name="point-the-jvm-to-the-jar-file"></a>JVM이 jar 파일을 가리키도록 합니다.

애플리케이션의 JVM 인수에 `-javaagent:path/to/applicationinsights-agent-3.2.3.jar` 추가

> [!TIP]
> 애플리케이션의 JVM 인수 구성에 관한 도움말은 [JVM 인수 업데이트를 위한 팁](./java-standalone-arguments.md)을 참조하세요.

#### <a name="set-the-application-insights-connection-string"></a>애플리케이션 Insights 연결 문자열 설정

1. 두 가지 방법으로 jar 파일을 Application Insights 리소스로 가리킬 수 있습니다.

   - 환경 변수를 설정할 수 있습니다.
    
        ```console
        APPLICATIONINSIGHTS_CONNECTION_STRING=InstrumentationKey=...
        ```

   - 또는 라는 구성 파일을 만들 수 `applicationinsights.json` 있습니다. 다음 콘텐츠와 동일한 디렉터리에 배치합니다. `applicationinsights-agent-3.2.3.jar`

        ```json
        {
          "connectionString": "InstrumentationKey=..."
        }
        ```

1. Application Insights 리소스에서 연결 문자열을 찾습니다.

    :::image type="content" source="media/java-ipa/connection-string.png" alt-text="애플리케이션 Insights 연결 문자열을 보여 주는 스크린샷.":::
    
#### <a name="confirm-data-is-flowing"></a>데이터가 흐르는지 확인

애플리케이션을 실행하고 **Azure Portal 애플리케이션 Insights 리소스** 탭을 엽니다. 포털에 데이터가 표시되면 몇 분 정도 걸릴 수 있습니다.

> [!NOTE]
> 애플리케이션을 실행할 수 없거나 예상대로 데이터를 얻지 못하는 경우 [문제 해결 섹션을](#troubleshooting) 참조하세요.

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="서버 요청 및 서버 응답 시간이 강조 표시된 애플리케이션 Insights 개요 탭을 보여 주는 스크린샷.":::

> [!IMPORTANT]
> 동일한 Application Insights 리소스에 원격 분석을 내보낸 서비스가 두 개 이상 있는 경우 애플리케이션 맵에 올바르게 표시되도록 [클라우드 역할 이름을 설정해야](java-standalone-config.md#cloud-role-name) 합니다.

Application Insights 계측을 사용하는 과정의 일환으로 진단 데이터를 수집하고 Microsoft에 보냅니다. 이 데이터는 애플리케이션 Insights 실행하고 개선하는 데 도움이 됩니다. 비일관적 데이터 수집을 사용하지 않도록 설정하는 옵션이 있습니다. 자세한 내용은 [Azure 애플리케이션 Insights Statsbeat를](./statsbeat.md)참조하세요.

## <a name="configuration-options"></a>구성 옵션

`applicationinsights.json`파일에서 다음 설정을 구성할 수도 있습니다.

* 클라우드 역할 이름
* 클라우드 역할 인스턴스
* 샘플링
* JMX 메트릭
* 사용자 지정 차원
* 원격 분석 프로세서(미리 보기)
* 자동 수집된 로깅
* Spring Boot Actuator 메트릭을 포함하는 자동 수집된 마이크로미터 메트릭
* 하트비트
* HTTP 프록시
* 자체 진단

자세한 내용은 구성 옵션 을 [참조하세요.](./java-standalone-config.md)

## <a name="instrumentation-libraries"></a>계측 라이브러리

Java 3.x에는 다음과 같은 계측 라이브러리가 포함되어 있습니다.

### <a name="autocollected-requests"></a>자동 수집된 요청

* JMS 소비자
* Kafka 소비자
* Netty/WebFlux
* 서블릿
* Spring 예약

### <a name="autocollected-dependencies"></a>자동 수집된 dependencies

자동 수집된 dependencies plus downstream distributed trace propagation:

* Apache HttpClient
* Apache HttpAsyncClient
* AsyncHttpClient
* Google HttpClient
* gRPC
* java.net.HttpURLConnection
* Java 11 HttpClient
* JAX-RS 클라이언트
* Jetty HttpClient
* JMS
* Kafka
* Netty 클라이언트
* OkHttp

다운스트림 분산 추적 전파 없이 자동 수집된 의존성:

* Cassandra
* JDBC
* MongoDB(비동기 및 동기)
* Redis(Lettuce 및 Jedis)

### <a name="autocollected-logs"></a>자동 수집된 로그

* java.util.logging
* MdC 속성을 포함하는 Log4j
* MDC 속성을 포함하는 SLF4J/Logback

### <a name="autocollected-metrics"></a>자동 수집된 메트릭

* Spring Boot Actuator 메트릭을 포함하는 마이크로미터
* JMX 메트릭

### <a name="azure-sdks"></a>Azure SDK

이러한 Azure SDK에서 내보낸 원격 분석은 기본적으로 자동으로 수집됩니다.

* [Azure 앱 Configuration](/java/api/overview/azure/data-appconfiguration-readme) 1.1.10 +
* [Azure Cognitive Search](/java/api/overview/azure/search-documents-readme) 11.3.0 +
* [Azure 통신 채팅](/java/api/overview/azure/communication-chat-readme) 1.0.0 +
* [Azure Communication Common](/java/api/overview/azure/communication-common-readme) 1.0.0 +
* [Azure Communication id](/java/api/overview/azure/communication-identity-readme) 1.0.0 +
* [Azure Communication 전화 숫자](/java/api/overview/azure/communication-phonenumbers-readme) 1.0.0 이상
* [Azure COMMUNICATION SMS](/java/api/overview/azure/communication-sms-readme) 1.0.0 +
* [Azure Cosmos DB](/java/api/overview/azure/cosmos-readme) 4.13.0 +
* [Azure Digital Twins-Core](/java/api/overview/azure/digitaltwins-core-readme) 1.1.0 +
* [Azure Event Grid](/java/api/overview/azure/messaging-eventgrid-readme) 4.0.0 +
* [Azure Event Hubs](/java/api/overview/azure/messaging-eventhubs-readme) 5.6.0 +
* [Azure Event Hubs-Azure Blob Storage Checkpoint Store](/java/api/overview/azure/messaging-eventhubs-checkpointstore-blob-readme) 1.5.1 +
* [Azure 양식 인식기](/java/api/overview/azure/ai-formrecognizer-readme) 3.0.6이 +
* [Azure id](/java/api/overview/azure/identity-readme) 1.2.4 +
* [Azure Key Vault-certificate](/java/api/overview/azure/security-keyvault-certificates-readme) 4.1.6 +
* [Azure Key Vault 키](/java/api/overview/azure/security-keyvault-keys-readme) 4.2.6 +
* [Azure Key Vault-비밀](/java/api/overview/azure/security-keyvault-secrets-readme) 4.2.6 +
* [Azure Service Bus](/java/api/overview/azure/messaging-servicebus-readme) 7.1.0 +
* [Azure Storage blob](/java/api/overview/azure/storage-blob-readme) 12.11.0 +
* [Azure Storage-blob Batch](/java/api/overview/azure/storage-blob-batch-readme) 12.9.0 +
* [Azure Storage-blob 암호화](/java/api/overview/azure/storage-blob-cryptography-readme) 12.11.0 +
* [Azure Storage-일반](/java/api/overview/azure/storage-common-readme) 12.11.0 +
* [Azure Storage 파일 Data Lake](/java/api/overview/azure/storage-file-datalake-readme) 12.5.0 +
* [Azure Storage-파일 공유](/java/api/overview/azure/storage-file-share-readme) 12.9.0 +
* [Azure Storage-큐](/java/api/overview/azure/storage-queue-readme) 12.9.0 +
* [Azure Text Analytics](/java/api/overview/azure/ai-textanalytics-readme) 5.0.4 +

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

## <a name="modify-telemetry"></a>원격 분석 수정

이 섹션에서는 원격 분석을 수정 하는 방법을 설명 합니다.

### <a name="add-span-attributes"></a>범위 특성 추가

를 사용 하 여 `opentelemetry-api` 범위에 특성을 추가할 수 있습니다. 이러한 특성에는 원격 분석에 사용자 지정 비즈니스 차원을 추가 하는 작업이 포함 될 수 있습니다. 특성을 사용 하 여 사용자 ID 또는 클라이언트 IP와 같은 Application Insights 스키마의 선택적 필드를 설정할 수도 있습니다.

#### <a name="add-a-custom-dimension"></a>사용자 지정 차원 추가

하나 이상의 사용자 지정 차원을 추가 하면 요청, 종속성 또는 예외 테이블의 _Customdimensions_ 필드가 채워집니다.

> [!NOTE]
> 이 기능은 3.2.0 이상 에서만 유효 합니다.

1. `opentelemetry-api-1.6.0.jar`응용 프로그램에를 추가 합니다.

    ```xml
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.6.0</version>
    </dependency>
    ```

1. 코드에 사용자 지정 차원을 추가 합니다.

    ```java
    import io.opentelemetry.api.trace.Span;
    
    Span.current().setAttribute("mycustomdimension", "myvalue1");
    ```

#### <a name="set-the-user-id"></a>사용자 ID 설정

요청, 종속성 또는 예외 테이블의 _사용자 ID_ 필드를 채웁니다.

> [!IMPORTANT]
> 인증 된 사용자 ID를 설정 하기 전에 적용 가능한 개인 정보 보호법을 참조 하십시오.

> [!NOTE]
> 이 기능은 3.2.0 이상 에서만 유효 합니다.

1. `opentelemetry-api-1.6.0.jar`응용 프로그램에를 추가 합니다.

    ```xml
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.6.0</version>
    </dependency>
    ```

1. `user_Id`코드에서 설정 합니다.

    ```java
    import io.opentelemetry.api.trace.Span;
    
    Span.current().setAttribute("enduser.id", "myuser");
    ```

### <a name="get-the-trace-id-or-span-id"></a>추적 ID 또는 범위 ID 가져오기

`opentelemetry-api`를 사용 하 여 추적 id 또는 범위 id를 가져올 수 있습니다. 이 작업을 수행 하 여 문제를 디버그 하 고 진단할 때 상관 관계를 향상 시키기 위해 기존 로깅 원격 분석에 이러한 식별자를 추가할 수 있습니다.

> [!NOTE]
> 이 기능은 3.2.0 이상 에서만 유효 합니다.

1. `opentelemetry-api-1.6.0.jar`응용 프로그램에를 추가 합니다.
    
    ```xml
    <dependency>
      <groupId>io.opentelemetry</groupId>
      <artifactId>opentelemetry-api</artifactId>
      <version>1.6.0</version>
    </dependency>
    ```

1. 코드에서 요청 추적 ID 및 범위 ID를 가져옵니다.

    ```java
    import io.opentelemetry.api.trace.Span;
    
    String traceId = Span.current().getSpanContext().getTraceId();
    String spanId = Span.current().getSpanContext().getSpanId();
    ```

## <a name="custom-telemetry"></a>사용자 지정 원격 분석

Java 3.x Application Insights의 목표는 표준 api를 사용 하 여 사용자 지정 원격 분석을 보낼 수 있도록 하는 것입니다.

현재 마이크로 측정기, 인기 있는 로깅 프레임 워크 및 Application Insights Java 2.x SDK를 지원 합니다. Application Insights Java 3.x는 이러한 api를 통해 전송 되는 원격 분석을 자동으로 캡처하고 자동 수집 된 원격 분석과 상관 관계를 합니다.

### <a name="supported-custom-telemetry"></a>지원되는 사용자 지정 원격 분석

다음 표에서는 Java 3.x 에이전트를 보완 하기 위해 사용할 수 있는 현재 지원 되는 사용자 지정 원격 분석 유형을 나타냅니다. 요약:

- 사용자 지정 메트릭은 마이크로 측정기를 통해 지원 됩니다.
- 로깅 프레임 워크를 통해 사용자 지정 예외 및 추적을 지원 합니다.
- 사용자 지정 요청, 종속성 및 예외는를 통해 지원 됩니다 `opentelemetry-api` .
- 모든 유형의 사용자 지정 원격 분석은 [Java 2.x SDK Application Insights](#send-custom-telemetry-by-using-the-2x-sdk)을 통해 지원 됩니다.

| 사용자 지정 원격 분석 유형            | 마이크로미터 | Log4j, logback, JUL | 2.x SDK | opentelemetry-api |
|---------------------|------------|---------------------|---------|-------------------|
| 사용자 지정 이벤트       |            |                     |  예    |                   |
| 사용자 지정 메트릭      |  예       |                     |  예    |                   |
| 종속성        |            |                     |  예    |  예              |
| 예외          |            |  예                |  예    |  예              |
| 페이지 보기          |            |                     |  예    |                   |
| 요청            |            |                     |  예    |  예              |
| Traces              |            |  예                |  예    |                   |

현재는 Application Insights 3.x를 사용 하 여 SDK를 릴리스할 계획이 아닙니다.

애플리케이션 Insights Java 3.x는 Application Insights Java 2.x SDK로 전송된 원격 분석을 이미 수신 대기하고 있습니다. 이 기능은 기존 2.x 사용자를 위한 업그레이드 스토리의 중요한 부분입니다. 또한 OpenTelemetry API가 일반화될 때까지 사용자 지정 원격 분석 지원에서 중요한 간격을 채웁니다.

### <a name="send-custom-metrics-by-using-micrometer"></a>마이크로미터를 사용하여 사용자 지정 메트릭 보내기

1. 애플리케이션에 Micrometer를 추가합니다.
    
    ```xml
    <dependency>
      <groupId>io.micrometer</groupId>
      <artifactId>micrometer-core</artifactId>
      <version>1.6.1</version>
    </dependency>
    ```

1. Micrometer [전역 레지스트리](https://micrometer.io/docs/concepts#_global_registry)를 사용하여 미터를 만듭니다.

    ```java
    static final Counter counter = Metrics.counter("test_counter");
    ```

1. 카운터를 사용하여 메트릭을 기록합니다.

    ```java
    counter.increment();
    ```

### <a name="send-custom-traces-and-exceptions-by-using-your-favorite-logging-framework"></a>즐겨찾는 로깅 프레임워크를 사용하여 사용자 지정 추적 및 예외 보내기

Log4j, Logback 및 java.util.logging은 자동으로 계측됩니다. 이러한 로깅 프레임워크를 통해 수행되는 로깅은 추적 및 예외 원격 분석으로 자동 수집됩니다.

기본적으로 로깅은 정보 수준 이상에서 수행되는 경우에만 수집됩니다.
이 수준을 변경하려면 구성 옵션 를 [참조하세요.](./java-standalone-config.md#auto-collected-logging)

로그에 사용자 지정 차원을 연결하려면 [Log4j 1.2 MDC,](https://logging.apache.org/log4j/1.2/apidocs/org/apache/log4j/MDC.html) [Log4j 2 MDC](https://logging.apache.org/log4j/2.x/manual/thread-context.html)또는 [Logback MDC](http://logback.qos.ch/manual/mdc.html)를 사용합니다. Application Insights Java 3.x는 추적 및 예외 원격 분석에서 해당 MDC 속성을 사용자 지정 차원으로 자동으로 캡처합니다.

### <a name="send-custom-telemetry-by-using-the-2x-sdk"></a>2.x SDK를 사용하여 사용자 지정 원격 분석 보내기

1. `applicationinsights-core-2.6.3.jar`애플리케이션에 를 추가합니다. 모든 2.x 버전은 Application Insights Java 3.x에서 지원됩니다. 선택할 수 있는 경우 최신 버전을 사용하는 것이 좋습니다.

    ```xml
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>applicationinsights-core</artifactId>
      <version>2.6.3</version>
    </dependency>
    ```

1. TelemetryClient를 만듭니다.
    
      ```java
    static final TelemetryClient telemetryClient = new TelemetryClient();
    ```

1. 클라이언트를 사용하여 사용자 지정 원격 분석을 보냅니다.

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

## <a name="troubleshooting"></a>문제 해결

문제 해결에 대한 도움말은 [문제 해결](java-standalone-troubleshoot.md)을 참조하십시오.

## <a name="support"></a>지원

지원을 받으려면 다음을 수행합니다.

- 문제 해결에 대한 도움말은 [문제 해결 단계를](java-standalone-troubleshoot.md)검토합니다.
- Azure 지원 문제의 경우 [Azure 지원 티켓을](https://azure.microsoft.com/support/create-ticket/)엽니다.
- OpenTelemetry 문제의 경우 [OpenTelemetry 커뮤니티에 직접 문의합니다.](https://opentelemetry.io/community/)

## <a name="opentelemetry-feedback"></a>OpenTelemetry 피드백

피드백을 제공하려면 다음을 수행합니다.

- OpenTelemetry 커뮤니티의 고객 [피드백 설문 조사](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform)를 작성합니다.
- [OpenTelemetry 얼리 어답터 Community](https://aka.ms/AzMonOTel/)조인하여 Microsoft에 자신을 알려주세요.
- [Microsoft Tech](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor)Community 다른 Azure Monitor 사용자와 소통합니다.
- [Azure 피드백 포럼](https://feedback.azure.com/d365community/forum/8849e04d-1325-ec11-b6e6-000d3a4f09d0)에서 기능 요청을 합니다.

## <a name="next-steps"></a>다음 단계

- 소스 코드를 검토하려면 [Azure Monitor Java 자동 계측 GitHub 리포지토리를 참조하세요.](https://github.com/Microsoft/ApplicationInsights-Java)
- OpenTelemetry 및 해당 커뮤니티에 대한 자세한 내용은 [OpenTelemetry Java GitHub 리포지토리를 참조하세요.](https://github.com/open-telemetry/opentelemetry-java-instrumentation)
- 사용 환경을 사용하도록 설정하려면 [웹 또는 브라우저 사용자 모니터링 사용을 참조하세요.](javascript.md)
