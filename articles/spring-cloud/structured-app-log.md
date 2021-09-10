---
title: Azure Spring Cloud의 구조적 애플리케이션 로그 | Microsoft Docs
description: 이 문서에서는 Azure Spring Cloud에서 구조적 애플리케이션 로그 데이터를 생성하고 수집하는 방법을 설명합니다.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 02/05/2021
ms.author: karler
ms.custom: devx-track-java
ms.openlocfilehash: 8d84462d38c00e3788e424bd7cac6742d8b0e408
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537487"
---
# <a name="structured-application-log-for-azure-spring-cloud"></a>Azure Spring Cloud의 구조적 애플리케이션 로그

이 문서에서는 Azure Spring Cloud에서 구조적 애플리케이션 로그 데이터를 생성하고 수집하는 방법을 설명합니다. 적절한 구성을 통해 Azure Spring Cloud는 Log Analytics를 사용하여 유용한 애플리케이션 로그 쿼리 및 분석 기능을 제공합니다.

## <a name="log-schema-requirements"></a>로그 스키마 요구 사항

로그 쿼리 환경을 개선하려면 애플리케이션 로그가 JSON 형식이고 스키마를 준수해야 합니다. Azure Spring Cloud는 이 스키마를 사용하여 애플리케이션을 구문 분석하고 Log Analytics로 스트리밍합니다.

> [!NOTE]
> JSON 로그 형식을 사용하면 콘솔에서 로그 스트리밍 출력을 읽기 어렵습니다. 사용자가 읽을 수 있는 출력을 가져오려면 `az spring-cloud app logs` CLI 명령에 `--format-json` 인수를 추가합니다. [JSON 구조화 로그 형식](./how-to-log-streaming.md#format-json-structured-logs)을 참조하세요.

**JSON 스키마 요구 사항:**

| Json 키      | Json 값 형식|  필수 | Log Analytics의 열| Description |
| --------------| ------------|-----------|-----------------|--------------------------|
| timestamp     | 문자열      |     예   | AppTimestamp    | 타임스탬프(UTC 형식)  |
| logger        | 문자열      |     No    | 로거          | logger                   |
| 수준         | 문자열      |     No    | CustomLevel     | 로그 수준                |
| 스레드        | 문자열      |     No    | 스레드          | 스레드                   |
| message       | 문자열      |     No    | 메시지         | 로그 메시지              |
| stackTrace    | 문자열      |     No    | StackTrace      | 예외 스택 추적    |
| exceptionClass| 문자열      |     No    | ExceptionClass  | 예외 클래스 이름     |
| mdc           | 중첩된 JSON |     예    |                 | 매핑된 진단 컨텍스트|
| mdc.traceId   | 문자열      |     No    | TraceId         |분산 추적의 추적 ID|
| mdc.spanId    | 문자열      |     No    | SpanId          |분산 추적의 범위 ID |
|               |             |           |                 |                          |

* "timestamp" 필드는 필수이며 UTC 형식이어야 하고 다른 모든 필드는 선택 사항입니다.
* "mdc" 필드의 "traceId" 및 "spanId"는 추적 용도로 사용됩니다.
* 각 JSON 레코드를 한 줄에 로그합니다.

**레코드 로그 샘플**

```log
{"timestamp":"2021-01-08T09:23:51.280Z","logger":"com.example.demo.HelloController","level":"ERROR","thread":"http-nio-1456-exec-4","mdc":{"traceId":"c84f8a897041f634","spanId":"c84f8a897041f634"},"stackTrace":"java.lang.RuntimeException: get an exception\r\n\tat com.example.demo.HelloController.throwEx(HelloController.java:54)\r\n\","message":"Got an exception","exceptionClass":"RuntimeException"}
```

## <a name="limitations"></a>제한 사항

JSON 로그의 각 줄은 최대 **16k 바이트** 를 포함할 수 있습니다. 단일 로그 레코드의 JSON 출력이 이 제한을 초과하면 여러 줄로 강제 분할되고, 각 원시 줄은 구조적으로 구문 분석되지 않고 `Log` 열에 수집됩니다.

일반적으로 이는 [Appinsights In-Process 에이전트](./how-to-application-insights.md)가 사용하도록 설정된 경우 심층 스택 추적을 사용하는 예외 로깅에서 발생합니다.  최종 출력이 올바르게 구문 분석되도록 스택 추적 출력에 제한 설정(아래 구성 샘플 참조)을 적용합니다.

## <a name="generate-schema-compliant-json-log"></a>스키마 규격 JSON 로그 생성

Spring 애플리케이션의 경우 [logback](http://logback.qos.ch/) 및 [log4j2](https://logging.apache.org/log4j/2.x/)와 같은 일반적인 [로깅 프레임워크](https://docs.spring.io/spring-boot/docs/2.1.13.RELEASE/reference/html/boot-features-logging.html#boot-features-custom-log-configuration)를 사용하여 예상되는 JSON 로그 형식을 생성할 수 있습니다.

### <a name="log-with-logback"></a>Logback을 사용하여 로그

Spring Boot 스타터를 사용하는 경우 기본적으로 Logback이 사용됩니다. Logback 앱의 경우 [logstash-encoder](https://github.com/logstash/logstash-logback-encoder)를 사용하여 JSON 형식의 로그를 생성합니다. 이 메서드는 Spring Boot 버전 2.1 이상에서 지원됩니다.

프로시저에서는 다음을 수행합니다.

1. `pom.xml` 파일에 logstash 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>net.logstash.logback</groupId>
        <artifactId>logstash-logback-encoder</artifactId>
        <version>6.5</version>
    </dependency>
    ```

1. `logback-spring.xml` 구성 파일을 업데이트하여 JSON 형식을 설정합니다.

    ```xml
    <configuration>
        <appender name="stdout" class="ch.qos.logback.core.ConsoleAppender">
            <encoder class="net.logstash.logback.encoder.LoggingEventCompositeJsonEncoder">
                <providers>
                    <timestamp>
                        <fieldName>timestamp</fieldName>
                        <timeZone>UTC</timeZone>
                    </timestamp>
                    <loggerName>
                        <fieldName>logger</fieldName>
                    </loggerName>
                    <logLevel>
                        <fieldName>level</fieldName>
                    </logLevel>
                    <threadName>
                        <fieldName>thread</fieldName>
                    </threadName>
                    <nestedField>
                        <fieldName>mdc</fieldName>
                        <providers>
                            <mdc />
                        </providers>
                    </nestedField>
                    <stackTrace>
                        <fieldName>stackTrace</fieldName>
                        <!-- maxLength - limit the length of the stack trace -->
                        <throwableConverter class="net.logstash.logback.stacktrace.ShortenedThrowableConverter">
                            <maxDepthPerThrowable>200</maxDepthPerThrowable>
                            <maxLength>14000</maxLength>
                            <rootCauseFirst>true</rootCauseFirst>
                        </throwableConverter>
                    </stackTrace>
                    <message />
                    <throwableClassName>
                        <fieldName>exceptionClass</fieldName>
                    </throwableClassName>
                </providers>
            </encoder>
        </appender>
        <root level="info">
            <appender-ref ref="stdout" />
        </root>
    </configuration>
    ```

1. `logback-spring.xml`와 같이 `-spring` 접미사가 있는 로깅 구성 파일을 사용하는 경우 Spring 활성 프로필을 기반으로 로깅 구성을 설정할 수 있습니다.

    ```xml
    <configuration>
        <springProfile name="dev">
            <!-- JSON appender definitions for local development, in human readable format -->
            <include resource="org/springframework/boot/logging/logback/defaults.xml" />
            <include resource="org/springframework/boot/logging/logback/console-appender.xml" />
            <root level="info">
                <appender-ref ref="CONSOLE" />
            </root>
        </springProfile>

        <springProfile name="!dev">
            <!-- JSON appender configuration from previous step, used for staging / production -->
            ...
        </springProfile>
    </configuration>
    ```

    로컬 개발의 경우 JVM 인수 `-Dspring.profiles.active=dev`로 Spring Cloud 애플리케이션을 실행하면 JSON 형식 줄이 아닌 사용자가 읽을 수 있는 로그를 볼 수 있습니다.

### <a name="log-with-log4j2"></a>log4j2로 로그

log4j2 앱의 경우 [json-template-layout](https://logging.apache.org/log4j/2.x/manual/json-template-layout.html)을 사용하여 JSON 형식의 로그를 생성합니다. 이 메서드는 Spring Boot 버전 2.1 이상에서 지원됩니다.

프로시저에서는 다음을 수행합니다.

1. `spring-boot-starter`에서 `spring-boot-starter-logging`을 제외하고 `pom.xml` 파일에 `spring-boot-starter-log4j2`, `log4j-layout-template-json` 종속성을 추가합니다.

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
        <exclusions>
            <exclusion>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-logging</artifactId>
            </exclusion>
        </exclusions>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-log4j2</artifactId>
    </dependency>
    <dependency>
        <groupId>org.apache.logging.log4j</groupId>
        <artifactId>log4j-layout-template-json</artifactId>
        <version>2.14.0</version>
    </dependency>
    ```

2. 클래스 경로에서 JSON 레이아웃 템플릿 파일 `jsonTemplate.json`을 준비합니다.

    ```json
    {
        "mdc": {
            "$resolver": "mdc"
        },
        "exceptionClass": {
            "$resolver": "exception",
            "field": "className"
        },
        "stackTrace": {
            "$resolver": "exception",
            "field": "stackTrace",
            "stringified": true
        },
        "message": {
            "$resolver": "message",
            "stringified": true
        },
        "thread": {
            "$resolver": "thread",
            "field": "name"
        },
        "timestamp": {
            "$resolver": "timestamp",
            "pattern": {
                "format": "yyyy-MM-dd'T'HH:mm:ss.SSS'Z'",
                "timeZone": "UTC"
            }
        },
        "level": {
            "$resolver": "level",
            "field": "name"
        },
        "logger": {
            "$resolver": "logger",
            "field": "name"
        }
    }
    ```

3. `log4j2-spring.xml` 구성 파일에서 이 JSON 레이아웃 템플릿을 사용합니다.

    ```xml
    <configuration>
        <appenders>
            <console name="Console" target="SYSTEM_OUT">
                <!-- maxStringLength - limit the length of the stack trace -->
                <JsonTemplateLayout eventTemplateUri="classpath:jsonTemplate.json" maxStringLength="14000" />
            </console>
        </appenders>
        <loggers>
            <root level="info">
                <appender-ref ref="Console" />
            </root>
        </loggers>
    </configuration>
    ```

## <a name="analyze-the-logs-in-log-analytics"></a>Log Analytics의 로그 분석

애플리케이션을 올바르게 설정한 후에는 애플리케이션 콘솔 로그가 Log Analytics로 스트리밍됩니다. 이 구조를 통해 Log Analytics에서 효율적인 쿼리를 실행할 수 있습니다.

### <a name="check-log-structure-in-log-analytics"></a>Log Analytics에서 로그 구조 확인

이렇게 하려면 다음 절차를 수행합니다.

1. 서비스 인스턴스의 서비스 개요 페이지로 이동합니다.
2. **모니터링** 섹션에서 **로그** 항목을 선택합니다.
3. 이 쿼리를 실행합니다.

   ```query
   AppPlatformLogsforSpring
   | where TimeGenerated > ago(1h)
   | project AppTimestamp, Logger, CustomLevel, Thread, Message, ExceptionClass, StackTrace, TraceId, SpanId
   ```

4. 애플리케이션 로그는 다음 이미지에 표시된 대로 반환됩니다.

   ![Json 로그 표시](media/spring-cloud-structured-app-log/json-log-query.png)

### <a name="show-log-entries-containing-errors"></a>오류가 포함된 로그 항목 표시

오류가 있는 로그 항목을 검토하려면 다음 쿼리를 실행합니다.

```query
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h) and CustomLevel == "ERROR"
| project AppTimestamp, Logger, ExceptionClass, StackTrace, Message, AppName
| sort by AppTimestamp
```

이 쿼리를 사용하여 오류를 찾거나, 쿼리 용어를 수정하여 특정 예외 클래스 또는 오류 코드를 찾습니다.

### <a name="show-log-entries-for-a-specific-traceid"></a>특정 traceId에 대한 로그 항목 표시

특정 추적 ID "trace_id"에 대한 로그 항목을 검토하려면 다음 쿼리를 실행합니다.

```query
AppPlatformLogsforSpring
| where TimeGenerated > ago(1h)
| where TraceId == "trace_id"
| project AppTimestamp, Logger, TraceId, SpanId, StackTrace, Message, AppName
| sort by AppTimestamp
```

## <a name="next-steps"></a>다음 단계

* 로그 쿼리에 대한 자세한 내용은 [Azure Monitor에서 로그 쿼리 시작](../azure-monitor/logs/get-started-queries.md)을 참조하세요.
