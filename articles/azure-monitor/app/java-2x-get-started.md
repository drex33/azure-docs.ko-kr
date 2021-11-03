---
title: '빠른 시작: Azure Application Insights로 Java 웹앱 분석'
description: 'Application Insights를 사용하여 Java 웹앱에 대한 애플리케이션 성능 모니터링. '
ms.topic: conceptual
ms.date: 11/22/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: a73bfa9ab247cafff4c99ed481134974fd1715ac
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131079019"
---
# <a name="get-started-with-application-insights-in-a-java-web-project"></a>Java 웹 프로젝트에서 Application Insights 시작하기

> [!CAUTION]
> 이 문서는 더 이상 권장되지 않는 Application Insights Java 2.x에 적용됩니다.
>
> 최신 버전에 대한 문서는 [Application Insights Java 3.x](./java-in-process-agent.md)에서 찾을 수 있습니다.

이 자습서에서는 Application Insights SDK를 사용하여 요청을 계측하고, 의존성을 추적하고, 성능 카운터를 수집하고, 성능 문제 및 예외를 진단하고, 사용자가 앱에서 수행하는 작업을 추적하는 코드를 작성합니다.

Application Insights는 라이브 애플리케이션의 성능 및 사용을 이해하는 데 도움이 되는 확장 가능한 분석 서비스입니다. Application Insights는 Linux, Unix 또는 Windows에서 실행되는 Java 앱을 지원합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* 작동하는 Java 애플리케이션.

## <a name="get-an-application-insights-instrumentation-key"></a>Application Insights 계측 키 가져오기

> [!IMPORTANT]
> 계측 키보다 [연결 문자열](./sdk-connection-string.md?tabs=java)이 권장됩니다. 새 Azure 지역에서는 계측 키 대신 연결 문자열을 **사용해야 합니다**. 연결 문자열은 원격 분석 데이터를 연결할 리소스를 식별합니다. 또한 리소스가 원격 분석의 대상으로 사용할 엔드포인트를 수정할 수 있습니다. 연결 문자열을 복사하여 애플리케이션의 코드 또는 환경 변수에 추가해야 합니다.
1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
2. Azure Portal에서 Application Insights 리소스를 만듭니다. Java 웹 애플리케이션에 대한 애플리케이션 종류를 설정합니다.

3. 새 리소스의 계측 키를 찾습니다. 코드 프로젝트에 이 키를 곧바로 붙여넣어야 합니다.

    ![새 리소스 개요에서 속성을 클릭하고 계측 키 복사](./media/java-get-started/instrumentation-key-001.png)

## <a name="add-the-application-insights-sdk-for-java-to-your-project"></a>프로젝트에 Java용 Aplication Insights SDK 추가

*프로젝트 형식 선택*

# <a name="maven"></a>[Maven](#tab/maven)

빌드에 Maven을 사용하도록 프로젝트가 이미 설정된 경우 *pom.xml* 파일에 다음 코드를 병합합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

```xml
    <dependencies>
      <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>applicationinsights-web-auto</artifactId>
        <!-- or applicationinsights-web for manual web filter registration -->
        <!-- or applicationinsights-core for bare API -->
        <version>2.6.2</version>
      </dependency>
    </dependencies>
```

# <a name="gradle"></a>[Gradle](#tab/gradle)

빌드에 Gradle을 사용하도록 프로젝트가 이미 설정된 경우 다음 코드를 *build.gradle* 파일에 병합합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

```gradle
    dependencies {
      compile group: 'com.microsoft.azure', name: 'applicationinsights-web-auto', version: '2.6.2'
      // or applicationinsights-web for manual web filter registration
      // or applicationinsights-core for bare API
    }
```

---

### <a name="questions"></a>질문
* *`-web-auto`, `-web` 및 `-core` 구성 요소 사이에는 어떤 관계가 있나요?*
  * `applicationinsights-web-auto`는 런타임에 Application Insights 서블릿 필터를 자동으로 등록하여 HTTP 서블릿 요청 수와 응답 시간을 추적하는 메트릭을 제공합니다.
  * `applicationinsights-web` 또한 HTTP 서블릿 요청 수와 응답 시간을 추적하는 메트릭을 제공하지만 애플리케이션에서 Application Insights 서블릿 필터를 수동으로 등록해야 합니다.
  * 예를 들어 애플리케이션이 서블릿 기반이 아닌 경우 `applicationinsights-core`는 기본 API만 제공합니다.
  
* *SDK를 최신 버전으로 업데이트하려면 어떻게 해야 하나요?*
  * 2020년 11월부터 Java 애플리케이션을 모니터링하려면 Application Insights Java 3.x를 사용하는 것이 좋습니다. 시작하는 방법에 대한 자세한 내용은 [Application Insights Java 3.x](./java-in-process-agent.md)를 참조하세요.

## <a name="add-an-applicationinsightsxml-file"></a>*ApplicationInsights.xml* 파일 추가
*ApplicationInsights.xml* 을 프로젝트의 리소스 폴더에 추가하거나 프로젝트의 배포 클래스 경로에 추가되었는지 확인합니다. 다음 XML을 복사합니다.

해당 계측 키를 Azure Portal에서 가져온 계측 키와 바꿉니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings" schemaVersion="2014-05-30">

   <!-- The key from the portal: -->
   <InstrumentationKey>** Your instrumentation key **</InstrumentationKey>

   <!-- HTTP request component (not required for bare API) -->
   <TelemetryModules>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebRequestTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebSessionTrackingTelemetryModule"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.modules.WebUserTrackingTelemetryModule"/>
   </TelemetryModules>

   <!-- Events correlation (not required for bare API) -->
   <!-- These initializers add context data to each event -->
   <TelemetryInitializers>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationIdTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebOperationNameTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebSessionTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserTelemetryInitializer"/>
      <Add type="com.microsoft.applicationinsights.web.extensibility.initializers.WebUserAgentTelemetryInitializer"/>
   </TelemetryInitializers>

</ApplicationInsights>
```

필요에 따라 애플리케이션에 액세스할 수 있는 위치에 구성 파일이 있을 수 있습니다.  `-Dapplicationinsights.configurationDirectory` 시스템 속성은 *ApplicationInsights.xml* 을 포함하는 디렉터리를 지정합니다. 예를 들어 `E:\myconfigs\appinsights\ApplicationInsights.xml`에 있는 구성 파일은 `-Dapplicationinsights.configurationDirectory="E:\myconfigs\appinsights"` 속성을 사용하여 구성됩니다.

* 계측 키는 원격 분석의 모든 항목과 함께 전송되며 리소스에서 표시하도록 Application Insights에 알려줍니다.
* HTTP 요청 구성 요소는 선택 사항입니다. 자동으로 포털에 요청 및 응답 시간에 대한 원격 분석을 보냅니다.
* 이벤트 상관 관계는 HTTP 요청 구성 요소에 추가됩니다. 서버에서 받은 각 요청에 식별자를 할당합니다. 그런 다음 ‘Operation.Id’ 속성으로 원격 분석의 모든 항목에 해당 식별자를 속성으로 추가합니다. [진단 검색][diagnostic]에서 필터를 설정하여 각 요청과 연결된 원격 분석의 상관 관계를 지정할 수 있습니다.

### <a name="alternative-ways-to-set-the-instrumentation-key"></a>계측 키를 설정하는 다른 방법
Application Insights SDK는 다음 순서로 키를 찾습니다.

1. 시스템 속성: -DAPPINSIGHTS_INSTRUMENTATIONKEY=your_ikey
2. 환경 변수: APPINSIGHTS_INSTRUMENTATIONKEY
3. 구성 파일: *ApplicationInsights.xml*

또한 [코드로 설정](./api-custom-events-metrics.md#ikey)할 수 있습니다.

```java
    String instrumentationKey = "00000000-0000-0000-0000-000000000000";

    if (instrumentationKey != null)
    {
        TelemetryConfiguration.getActive().setInstrumentationKey(instrumentationKey);
    }
```

## <a name="add-agent"></a>에이전트 추가

나가는 HTTP 호출, JDBC 쿼리, 애플리케이션 로깅 및 더 나은 작업 이름을 캡처하기 위해 [Java 에이전트를 설치](java-2x-agent.md)합니다.

## <a name="run-your-application"></a>애플리케이션 실행
응용 프로그램을 디버그 모드로 개발 컴퓨터에서 실행하거나 서버에 게시합니다.

## <a name="view-your-telemetry-in-application-insights"></a>Application Insights에서 원격 분석 보기
[Microsoft Azure 포털](https://portal.azure.com)의 Application Insights 리소스로 돌아갑니다.

HTTP 요청 데이터가 개요 블레이드에 표시됩니다. (없는 경우 몇 초 정도 기다린 다음 새로고침을 클릭합니다.)

![개요 샘플 데이터 스크린샷](./media/java-get-started/overview-graphs.png)

[메트릭에 대해 자세히 알아봅니다.][metrics]

차트를 클릭하면 더 자세한 집계된 메트릭을 볼 수 있습니다.

![차트가 포함된 Application Insights 실패 창](./media/java-get-started/006-barcharts.png)

### <a name="instance-data"></a>인스턴스 데이터
특정 요청 유형을 클릭하여 개별 인스턴스를 확인합니다.

![특정 샘플 보기로 드릴](./media/java-get-started/007-instance.png)

### <a name="analytics-powerful-query-language"></a>분석: 강력한 쿼리 언어
더 많은 데이터가 누적되면 쿼리를 실행하여 데이터를 집계하고 개별 인스턴스를 찾을 수 있습니다.  [분석](../logs/log-query-overview.md) 은 성능 및 사용 이해 및 진단 목적 모두에 강력한 도구입니다.

![분석 예제](./media/java-get-started/0025.png)

## <a name="install-your-app-on-the-server"></a>서버에 앱 설치
이제 서버에 앱을 게시하고, 사람들이 사용하게 한 다음 포털에 표시되는 원격 분석을 확인합니다.

* 방화벽에서 애플리케이션이 다음 포트에 원격 분석을 보내도록 허용하는지 확인합니다.

  * dc.services.visualstudio.com:443
  * f5.services.visualstudio.com:443

* 나가는 트래픽이 방화벽을 통해 라우팅되어야 하는 경우 시스템 속성 `http.proxyHost` 및 `http.proxyPort`를 정의합니다.

* Windows 서버에 다음을 설치합니다.

  * [Microsoft Visual C++ 재배포 가능 패키지](https://www.microsoft.com/download/details.aspx?id=40784)

    (이 구성 요소를 통해 성능 카운터를 사용할 수 있게 됩니다.)

## <a name="azure-app-service-aks-vms-config"></a>Azure App Service, AKS, VM 구성

Azure 리소스 공급자에서 실행되는 애플리케이션을 모니터링하는 가장 쉬운 방법은 [Application Insights Java 3.x](./java-in-process-agent.md)를 사용하는 것입니다.


## <a name="exceptions-and-request-failures"></a>예외 및 요청 실패
처리되지 않은 예외 및 요청 오류는 Application Insights 웹 필터에 의해 자동으로 수집됩니다.

다른 예외에 대한 데이터를 수집하기 위해 [코드에서 trackException()에 대한 호출을 삽입][apiexceptions]할 수 있습니다.

## <a name="monitor-method-calls-and-external-dependencies"></a>메서드 호출 및 외부 종속성 모니터링
[Java 에이전트를 설치](java-2x-agent.md) 하여 지정된 내부 메서드 및 JDBC를 통해 수행한 호출을 타이밍 데이터와 함께 기록합니다.

또한 자동 작업 명명에도 사용됩니다.

## <a name="w3c-distributed-tracing"></a>W3C 분산 추적

Application Insights Java SDK는 이제 [W3C 분산 추적](https://w3c.github.io/trace-context/)을 지원합니다.

수신 SDK 구성은 [상관 관계](correlation.md) 관련 문서에 자세히 설명되어 있습니다.

발신 SDK 구성은 [Ai-agent.xml](java-2x-agent.md) 파일에서 정의됩니다.

## <a name="performance-counters"></a>성능 카운터
**조사**, **메트릭** 을 열고 다양한 범위의 성능 카운터를 확인합니다.

![선택한 프로세스 프라이빗 바이트가 포함된 메트릭 창의 스크린샷](./media/java-get-started/011-perf-counters.png)

### <a name="customize-performance-counter-collection"></a>성능 카운터 수집 사용자 지정
성능 카운터의 표준 집합 컬렉션을 사용하지 않으려면 *ApplicationInsights.xml* 파일의 루트 노드 아래에 다음 코드를 추가합니다.

```xml
    <PerformanceCounters>
       <UseBuiltIn>False</UseBuiltIn>
    </PerformanceCounters>
```

### <a name="collect-additional-performance-counters"></a>추가 성능 카운터 수집
추가 성능 카운터가 수집되도록 지정할 수 있습니다.

#### <a name="jmx-counters-exposed-by-the-java-virtual-machine"></a>JMX 카운터(Java Virtual Machine을 통해 노출됨)

```xml
    <PerformanceCounters>
      <Jmx>
        <Add objectName="java.lang:type=ClassLoading" attribute="TotalLoadedClassCount" displayName="Loaded Class Count"/>
        <Add objectName="java.lang:type=Memory" attribute="HeapMemoryUsage.used" displayName="Heap Memory Usage-used" type="composite"/>
      </Jmx>
    </PerformanceCounters>
```

* `displayName` - Application Insights 포털에서 표시되는 이름입니다.
* `objectName` – JMX 개체 이름입니다.
* `attribute` - 가져올 JMX 개체 이름의 특성입니다.
* `type` (선택 사항) - JMX 개체 특성의 유형:
  * 기본값: int 또는 long과 같은 단순 유형입니다.
  * `composite`: 성능 카운터 데이터는 'Attribute.Data' 형식입니다.
  * `tabular`: 성능 카운터 데이터는 표 행 형식입니다.

#### <a name="windows-performance-counters"></a>Windows 성능 카운터
각 [Windows 성능 카운터](/windows/win32/perfctrs/performance-counters-portal) 는 한 범주의 구성원입니다(필드가 클래스의 구성원인 것과 동일한 방식). 범주는 전역일 수 있으며, 번호 또는 이름이 지정된 인스턴스를 가질 수도 있습니다.

```xml
    <PerformanceCounters>
      <Windows>
        <Add displayName="Process User Time" categoryName="Process" counterName="%User Time" instanceName="__SELF__" />
        <Add displayName="Bytes Printed per Second" categoryName="Print Queue" counterName="Bytes Printed/sec" instanceName="Fax" />
      </Windows>
    </PerformanceCounters>
```

* displayName - Application Insights 포털에서 표시되는 이름입니다.
* categoryName – 이 성능 카운터와 관련된 성능 카운터 범주(성능 개체)입니다.
* counterName – 성능 카운터의 이름입니다.
* instanceName – 성능 카운터 범주 인스턴스입니다. 또는 범주가 단일 인스턴스를 포함하는 경우 빈 문자열("")의 이름입니다. categoryName이 프로세스이며 수입하려는 성능 카운터는 앱이 실행 중인 현재 JVM 프로세스에서 오는 경우, `"__SELF__"`을(를) 지정합니다.

### <a name="unix-performance-counters"></a>Unix 성능 카운터
* [Application Insights 플러그 인과 함께 collectd를 설치](java-2x-collectd.md) 하여 광범위한 시스템 및 네트워크 데이터를 얻을 수 있습니다.

## <a name="get-user-and-session-data"></a>사용자 및 세션 데이터 가져오기
이제 웹 서버에서 원격 분석을 보내려 합니다. 애플리케이션을 전체적으로 파악하기 위해 모니터링을 추가할 수 있습니다.

* [웹 페이지에 원격 분석을 추가][usage] 하여 페이지 보기 및 사용자 메트릭을 모니터링합니다.
* [웹 테스트를 설정][availability] 하여 애플리케이션이 라이브 상태로 유지되며 응답하는지 확인할 수 있습니다.

## <a name="send-your-own-telemetry"></a>사용자 고유의 원격 분석 전송
이제 SDK를 설치한 했으므로 API를 사용하여 사용자 고유의 원격 분석을 전송할 수 있습니다.

* [사용자 지정 이벤트 및 메트릭을 추적][api]하여 사용자가 애플리케이션으로 수행하는 것을 알아볼 수 있습니다.
* [이벤트 및 로그를 검색][diagnostic]하여 문제를 진단할 수 있습니다.

## <a name="availability-web-tests"></a>가용성 웹 테스트
Application Insights는 일정한 간격으로 웹 사이트를 테스트하여 잘 실행되며 제대로 응답하는지 확인할 수 있습니다.

[가용성 웹 테스트 설정 방법에 대한 자세한 정보.][availability]

## <a name="questions-problems"></a>궁금한 점이 더 있나요? 문제가 있습니까?
[Java 문제 해결](java-2x-troubleshoot.md)

## <a name="next-steps"></a>다음 단계
* [종속성 호출 모니터링](java-2x-agent.md)
* [Unix 성능 카운터 모니터링](java-2x-collectd.md)
* [웹 페이지에 모니터링](javascript.md)을 추가하여 페이지 로드 시간, AJAX 호출, 브라우저 예외를 모니터링합니다.
* [사용자 지정 원격 분석](./api-custom-events-metrics.md)을 작성하여 브라우저 또는 서버에서 사용량을 추적합니다.
* 앱의 원격 분석을 통해 강력한 쿼리를 수행하려면 [분석](../logs/log-query-overview.md)을 사용합니다.
* 자세한 내용은 [Java 개발자용 Azure](/java/azure)를 방문하세요.

<!--Link references-->

[api]: ./api-custom-events-metrics.md
[apiexceptions]: ./api-custom-events-metrics.md#trackexception
[availability]: ./monitor-web-app-availability.md
[diagnostic]: ./diagnostic-search.md
[javalogs]: java-2x-trace-logs.md
[metrics]: ../essentials/metrics-charts.md
[usage]: javascript.md