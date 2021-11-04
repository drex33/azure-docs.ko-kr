---
title: Azure Application Insights에서 Java 추적 로그 검색하기
description: Application Insights에서 검색 Log4J 또는 Logback 추적 검색
ms.topic: conceptual
ms.date: 05/18/2019
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 23443bf1063ac1653545bbd22a0b52f8bc72d008
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131067794"
---
# <a name="explore-java-trace-logs-in-application-insights"></a>Application Insights에서 Java 추적 로그 탐색

> [!CAUTION]
> 이 문서는 더 이상 권장되지 않는 Application Insights Java 2.x에 적용됩니다.
>
> 최신 버전에 대한 문서는 [Application Insights Java 3.x](./java-in-process-agent.md)에서 찾을 수 있습니다.

추적에 Logback 또는 Log4J(v1.2 또는 v2.0)를 사용하는 경우 추적 로그를 살펴보고 검색할 수 있는 Application Insights에 추적 로그를 자동으로 전송할 수 있습니다.

> [!TIP]
> 애플리케이션에 대해 Application Insights 계측 키를 한 번만 설정하면 됩니다. Java Spring 등의 프레임워크를 사용하는 경우 앱 구성의 다른 위치에 키를 이미 등록했을 수 있습니다.

## <a name="using-the-application-insights-java-agent"></a>Application Insights Java 에이전트 사용

기본적으로 Application Insights Java 에이전트는 `WARN` 수준 이상에서 수행된 로깅을 자동으로 캡처합니다.

`AI-Agent.xml` 파일을 사용하여 캡처한 로깅 임계값을 변경할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging threshold="info"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

`AI-Agent.xml` 파일을 사용하여 Java 에이전트의 로깅 캡처를 사용하지 않도록 설정할 수 있습니다.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsightsAgent>
   <Instrumentation>
      <BuiltIn>
         <Logging enabled="false"/>
      </BuiltIn>
   </Instrumentation>
</ApplicationInsightsAgent>
```

## <a name="alternatively-as-opposed-to-using-the-java-agent-you-can-follow-the-instructions-below"></a>또는 Java 에이전트를 사용하는 경우와 달리 아래 지침을 따를 수도 있습니다.

### <a name="install-the-java-sdk"></a>Java SDK 설치

아직 수행하지 않은 경우 지침을 다라 [Java용 Application Insights SDK][java]를 설치합니다.

### <a name="add-logging-libraries-to-your-project"></a>프로젝트에 로깅 라이브러리 추가
*프로젝트에 적합한 방법을 선택합니다.*

#### <a name="if-youre-using-maven"></a>Maven을 사용하는 경우...
빌드에 Maven을 사용하도록 프로젝트가 이미 설정된 경우 pom.xml 파일에 다음 코드 조각을 추가합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

*Logback*

```xml

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-logback</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v2.0*

```xml

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

*Log4J v1.2*

```xml

    <dependencies>
       <dependency>
          <groupId>com.microsoft.azure</groupId>
          <artifactId>applicationinsights-logging-log4j1_2</artifactId>
          <version>[2.0,)</version>
       </dependency>
    </dependencies>
```

#### <a name="if-youre-using-gradle"></a>Gradle을 사용하는 경우...
빌드에 Gradle을 사용하도록 프로젝트가 이미 설정된 경우 다음 줄 중 하나를 build.gradle 파일의 `dependencies` 그룹에 추가합니다.

그런 다음 프로젝트 종속성을 새로 고쳐 다운로드한 이진을 가져옵니다.

**Logback**

```

    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-logback', version: '2.0.+'
```

**Log4J v2.0**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j2', version: '2.0.+'
```

**Log4J v1.2**

```
    compile group: 'com.microsoft.azure', name: 'applicationinsights-logging-log4j1_2', version: '2.0.+'
```

#### <a name="otherwise-"></a>기타...
지침에 따라 Application Insights Java SDK를 수동으로 설치하고 적절한 어펜더에 해당하는 jar을 다운로드한 다음 프로젝트에 추가합니다. jar을 다운로드하려면 Maven Central 페이지로 이동하여 다운로드 섹션에서 'jar' 링크를 클릭합니다.

| 로거 | 다운로드 | 라이브러리 |
| --- | --- | --- |
| Logback |[Logback 어펜더 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-logback%22) |applicationinsights-logging-logback |
| Log4J v2.0 |[Log4J v2 어펜더 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j2%22) |applicationinsights-logging-log4j2 |
| Log4J v1.2 |[Log4J v1.2 어펜더 Jar](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22applicationinsights-logging-log4j1_2%22) |applicationinsights-logging-log4j1_2 |


### <a name="add-the-appender-to-your-logging-framework"></a>로깅 프레임워크에 어펜더 추가
추적 가져오기를 시작하려면 관련 코드 조각을 Log4J 및 Logback 구성 파일과 병합합니다. 

*Logback*

```xml

    <appender name="aiAppender" 
      class="com.microsoft.applicationinsights.logback.ApplicationInsightsAppender">
        <instrumentationKey>[APPLICATION_INSIGHTS_KEY]</instrumentationKey>
    </appender>
    <root level="trace">
      <appender-ref ref="aiAppender" />
    </root>
```

*Log4J v2.0*

```xml

    <Configuration packages="com.microsoft.applicationinsights.log4j.v2">
      <Appenders>
        <ApplicationInsightsAppender name="aiAppender" instrumentationKey="[APPLICATION_INSIGHTS_KEY]" />
      </Appenders>
      <Loggers>
        <Root level="trace">
          <AppenderRef ref="aiAppender"/>
        </Root>
      </Loggers>
    </Configuration>
```

*Log4J v1.2*

```xml

    <appender name="aiAppender" 
         class="com.microsoft.applicationinsights.log4j.v1_2.ApplicationInsightsAppender">
        <param name="instrumentationKey" value="[APPLICATION_INSIGHTS_KEY]" />
    </appender>
    <root>
      <priority value ="trace" />
      <appender-ref ref="aiAppender" />
    </root>
```

Application Insights 어펜더는 루트 로거만이 아니라 구성된 모든 로거에 의해 참조될 수 있습니다(위의 코드 샘플에 표시).

## <a name="explore-your-traces-in-the-application-insights-portal"></a>Application Insights 포털에서 추적 탐색
이제 Application Insights에 추적을 전송하도록 프로젝트를 구성했으며 [검색][diagnostic] 블레이드의 Application Insights 포털에서 이러한 추적을 보고 검색할 수 있습니다.

로거를 통해 제출된 예외는 포털에 예외 원격 분석으로 표시됩니다.

![Application Insights 포털에서 검색을 엽니다.](./media/java-trace-logs/01-diagnostics.png)

## <a name="next-steps"></a>다음 단계
[진단 검색][diagnostic]

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[java]: java-2x-get-started.md

