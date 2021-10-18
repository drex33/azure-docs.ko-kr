---
title: 구성 옵션 - Java용 Azure Monitor Application Insights
description: Java용 Azure Monitor Application Insights를 구성하는 방법
ms.topic: conceptual
ms.date: 11/04/2020
ms.custom: devx-track-java
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: c5de4ed71de7f6cb6534620cbfd1bc62226123e5
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132800"
---
# <a name="configuration-options---azure-monitor-application-insights-for-java"></a>구성 옵션 - Java용 Azure Monitor Application Insights

> [!WARNING]
> **3.0 미리 보기에서 업그레이드하는 경우**
>
> 모두 소문자로 된 파일 이름 자체 외에도 json 구조가 완전히 변경되었으므로 아래 모든 구성 옵션을 신중히 검토하세요.

## <a name="connection-string-and-role-name"></a>연결 문자열 및 역할 이름

시작하는 데 필요한 가장 일반적인 설정은 연결 문자열 및 역할 이름입니다.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  }
}
```

연결 문자열이 필요하며, 다른 애플리케이션의 데이터를 동일한 Application Insights 리소스로 보낼 때마다 역할 이름이 중요합니다.

아래에서 자세한 내용 및 추가 구성 옵션을 확인할 수 있습니다.

## <a name="configuration-file-path"></a>구성 파일 경로

기본적으로 Application Insights Java 3.x는 구성 파일의 이름을 `applicationinsights.json`으로 지정하고 `applicationinsights-agent-3.2.0.jar`와 동일한 디렉터리에 위치하도록 합니다.

다음 중 하나를 사용하여 사용자 고유의 구성 파일 경로를 지정할 수 있습니다.

* `APPLICATIONINSIGHTS_CONFIGURATION_FILE` 환경 변수 또는
* `applicationinsights.configuration.file` Java 시스템 속성

상대 경로를 지정하면 `applicationinsights-agent-3.2.0.jar`이 있는 디렉터리를 기준으로 확인됩니다.

## <a name="connection-string"></a>연결 문자열

연결 문자열이 필요합니다. Application Insights 리소스에서 연결 문자열을 찾을 수 있습니다.

:::image type="content" source="media/java-ipa/connection-string.png" alt-text="Application Insights 연결 문자열":::


```json
{
  "connectionString": "InstrumentationKey=..."
}
```

환경 변수 `APPLICATIONINSIGHTS_CONNECTION_STRING`을 사용하여 연결 문자열을 설정할 수도 있습니다. 이 경우 json 구성에 지정된 연결 문자열보다 우선 적용됩니다.

연결 문자열을 설정하지 않으면 Java 에이전트가 사용되지 않습니다.

## <a name="cloud-role-name"></a>클라우드 역할 이름

클라우드 역할 이름은 애플리케이션 맵의 구성 요소에 레이블을 적용하는 데 사용됩니다.

클라우드 역할 이름을 설정하려면 다음을 수행합니다.

```json
{
  "role": {   
    "name": "my cloud role name"
  }
}
```

클라우드 역할 이름이 설정되지 않은 경우 Application Insights 리소스의 이름을 사용하여 애플리케이션 맵의 구성 요소에 레이블을 지정할 수 있습니다.

환경 변수 `APPLICATIONINSIGHTS_ROLE_NAME`을 사용하여 클라우드 역할 이름을 설정할 수도 있습니다. 이 경우 json 구성에 지정된 클라우드 역할 이름보다 우선 적용됩니다.

## <a name="cloud-role-instance"></a>클라우드 역할 인스턴스

클라우드 역할 인스턴스는 기본적으로 머신 이름으로 설정됩니다.

클라우드 역할 인스턴스를 머신 이름이 아닌 다른 항목으로 설정하려면 다음을 수행합니다.

```json
{
  "role": {
    "name": "my cloud role name",
    "instance": "my cloud role instance"
  }
}
```

환경 변수 `APPLICATIONINSIGHTS_ROLE_INSTANCE`를 사용하여 클라우드 역할 인스턴스를 설정할 수도 있습니다. 이는 json 구성에 지정된 클라우드 역할 인스턴스보다 우선 적용됩니다.

## <a name="sampling"></a>샘플링

샘플링은 비용을 줄여야 할 경우에 유용합니다.
샘플링은 작업 ID(추적 ID라고도 함)에서 함수로 수행되므로 동일한 작업 ID가 항상 동일한 샘플링 결정을 발생하도록 합니다. 이렇게 하면 다른 부분이 샘플링되는 동안 분산 트랜잭션의 일부가 샘플링되지 않습니다.

예를 들어 샘플링을 10%로 설정하면 트랜잭션의 10%만 표시되지만 이러한 10% 중 각각에는 완전한 엔드투엔드 트랜잭션 세부 정보가 포함됩니다.

다음은 **모든 트랜잭션의 약 1/3** 을 캡처하기 위해 샘플링을 설정하는 방법의 예제입니다. 사용 사례에 맞는 샘플링 주기를 설정했는지 확인합니다.

```json
{
  "sampling": {
    "percentage": 33.333
  }
}
```

환경 변수 `APPLICATIONINSIGHTS_SAMPLING_PERCENTAGE`를 사용하여 샘플링 백분율을 설정할 수도 있습니다. 이는 json 구성에 지정된 샘플링 백분율보다 우선 적용됩니다.

> [!NOTE]
> 샘플링 비율의 경우 100/N(여기서 N은 정수)에 가까운 백분율을 선택합니다. 현재 샘플링은 다른 값을 지원하지 않습니다.

## <a name="sampling-overrides-preview"></a>샘플링 재정의(미리 보기)

이 기능은 3.0.3부터 미리 보기 상태입니다.

샘플링 재정의를 사용하면 [기본 샘플링 백분율](#sampling)을 재정의할 수 있습니다. 예를 들면 다음과 같습니다.
* 잡음 상태 검사에 대한 샘플링 백분율을 0이나 작은 값으로 설정합니다.
* 잡음 종속성 호출에 대한 샘플링 백분율을 0이나 작은 값으로 설정합니다.
* 기본 샘플링이 더 낮은 것으로 구성된 경우에도 중요한 요청 유형(예: `/login`)에 대해 샘플링 백분율을 100으로 설정합니다.

자세한 내용은 [샘플링 재정의](./java-standalone-sampling-overrides.md) 설명서를 참조하세요.

## <a name="jmx-metrics"></a>JMX 메트릭

몇 가지 추가 JMX 메트릭을 수집하려면 다음을 수행합니다.

```json
{
  "jmxMetrics": [
    {
      "name": "JVM uptime (millis)",
      "objectName": "java.lang:type=Runtime",
      "attribute": "Uptime"
    },
    {
      "name": "MetaSpace Used",
      "objectName": "java.lang:type=MemoryPool,name=Metaspace",
      "attribute": "Usage.used"
    }
  ]
}
```

`name`은 이 JMX 메트릭에 할당되는 메트릭 이름입니다(모두 가능).

`objectName`은 수집하려는 JMX MBean의 [개체 이름](https://docs.oracle.com/javase/8/docs/api/javax/management/ObjectName.html)입니다.

`attribute`는 수집하려는 JMX MBean의 특성 이름입니다.

숫자 및 부울 JMX 메트릭 값이 지원됩니다. 부울 JMX 메트릭은 false의 경우 `0`에, true의 경우 `1`에 매핑됩니다.

## <a name="custom-dimensions"></a>사용자 지정 차원

모든 원격 분석에 사용자 지정 차원을 추가하려면 다음을 수행합니다.

```json
{
  "customDimensions": {
    "mytag": "my value",
    "anothertag": "${ANOTHER_VALUE}"
  }
}
```

`${...}`를 사용하여 시작 시 지정된 환경 변수에서 값을 읽을 수 있습니다.

> [!NOTE]
> 버전 3.0.2부터 `service.version`이라는 사용자 지정 차원을 추가하는 경우 값은 사용자 지정 차원이 아닌 Application Insights Logs 테이블의 `application_Version` 열에 저장됩니다.

## <a name="inherited-attribute-preview"></a>상속된 특성(미리 보기)

버전 3.2.0부터 요청 원격 분석에서 프로그래밍 방식으로 사용자 지정 차원을 설정하고 다음에 오는 종속성 원격 분석에서 상속하려는 경우:

```json
{
  "inheritedAttributes": [
    {
      "key": "mycustomer",
      "type": "string"
    }
  ]
}
```


## <a name="telemetry-processors-preview"></a>원격 분석 프로세서(미리 보기)

이를 통해 요청, 종속성 및 추적 원격 분석에 적용되는 규칙을 구성할 수 있습니다. 예를 들면 다음과 같습니다.
 * 중요한 데이터 마스크
 * 조건부로 사용자 지정 차원 추가
 * Azure Portal에서 유사한 원격 분석을 집계하는 데 사용되는 범위 이름을 업데이트합니다.
 * 수집 비용을 제어하기 위해 특정 범위 특성을 삭제합니다.

자세한 내용은 [원격 분석 프로세서](./java-standalone-telemetry-processors.md) 설명서를 확인하세요.

> [!NOTE]
> 수집 비용을 제어하기 위해 특정(전체) 범위를 삭제하려는 경우 [샘플링 재정의](./java-standalone-sampling-overrides.md)를 참조하세요.

## <a name="auto-collected-logging"></a>자동 수집된 로깅

Log4j, Logback 및 java.util.logging은 자동으로 계측되며 이러한 로깅 프레임워크를 통해 수행된 로깅은 자동으로 수집됩니다.

로깅은 첫 번째로 로깅 프레임워크에 대해 구성된 수준을 충족하고, 두 번째로 Application Insights에 대해 구성된 수준을 충족하는 경우에만 캡처됩니다.

예를 들어 로깅 프레임워크가 `com.example` 패키지에서 `WARN`(및 이상)을 로그하도록 구성되고 Application Insights가 `INFO`(및 이상)를 캡처하도록 구성된 경우 Application Insights는 `com.example` 패키지에서 `WARN`(및 이상)만 캡처합니다.

Application Insights에 대해 구성된 기본 수준은 `INFO`입니다. 이 수준을 변경하려면 다음을 수행합니다.

```json
{
  "instrumentation": {
    "logging": {
      "level": "WARN"
    }
  }
}
```

환경 변수 `APPLICATIONINSIGHTS_INSTRUMENTATION_LOGGING_LEVEL`을 사용하여 수준을 설정할 수도 있습니다. 이 경우 json 구성에 지정된 수준보다 우선 적용됩니다.

이러한 값은 `applicationinsights.json` 파일에 지정할 수 있는 유효한 `level` 값이며, 서로 다른 로깅 프레임워크의 로깅 수준에 대응하는 방식입니다.

| 수준             | Log4j  | Logback | 7월     |
|-------------------|--------|---------|---------|
| OFF               | OFF    | OFF     | OFF     |
| FATAL             | FATAL  | 오류   | SEVERE  |
| ERROR(또는 SEVERE) | 오류  | 오류   | SEVERE  |
| WARN(또는 WARNING) | WARN   | WARN    | 경고 |
| 정보              | INFO   | INFO    | INFO    |
| CONFIG            | DEBUG  | DEBUG   | CONFIG  |
| DEBUG(또는 FINE)   | DEBUG  | DEBUG   | FINE    |
| FINER             | DEBUG  | DEBUG   | FINER   |
| TRACE(또는 FINEST) | TRACE  | TRACE   | FINEST  |
| ALL               | ALL    | ALL     | ALL     |

> [!NOTE]
> 예외 개체가 로거에 전달되면 로그 메시지(및 예외 개체 세부 정보)는 `traces` 테이블 대신 `exceptions` 테이블의 Azure Portal에 표시됩니다.

## <a name="auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics"></a>자동 수집된 마이크로미터 메트릭(Spring Boot Actuator 메트릭 포함)

애플리케이션에서 [마이크로미터](https://micrometer.io)를 사용하는 경우 마이크로미터 글로벌 레지스트리로 전송되는 메트릭은 자동으로 수집됩니다.

또한 애플리케이션에서 [Spring Boot Actuator](https://docs.spring.io/spring-boot/docs/current/reference/html/production-ready-features.html)를 사용하는 경우 Spring Boot Actuator로 구성된 메트릭도 자동으로 수집됩니다.

자동 수집된 마이크로미터 메트릭(Spring Boot Actuator 메트릭 포함)를 사용하지 않도록 설정하려면 다음을 수행합니다.

> [!NOTE]
> 사용자 지정 메트릭은 별도로 청구되며 추가 비용이 발생할 수 있습니다. 자세한 [가격 정보](https://azure.microsoft.com/pricing/details/monitor/)를 확인하시기 바랍니다. 마이크로미터 및 Spring Actuator 메트릭을 사용하지 않도록 설정하려면 구성 파일에 아래 구성을 추가합니다.

```json
{
  "instrumentation": {
    "micrometer": {
      "enabled": false
    }
  }
}
```

## <a name="suppressing-specific-auto-collected-telemetry"></a>자동 수집된 특정 원격 분석 표시 안 함

3\.0.3 버전부터 다음 구성 옵션을 사용하여 자동 수집된 특정 원격 분석을 표시하지 않을 수 있습니다.

```json
{
  "instrumentation": {
    "azureSdk": {
      "enabled": false
    },
    "cassandra": {
      "enabled": false
    },
    "jdbc": {
      "enabled": false
    },
    "jms": {
      "enabled": false
    },
    "kafka": {
      "enabled": false
    },
    "micrometer": {
      "enabled": false
    },
    "mongo": {
      "enabled": false
    },
    "rabbitmq": {
      "enabled": false
    },
    "redis": {
      "enabled": false
    },
    "springScheduling": {
      "enabled": false
    }
  }
}
```

이러한 환경 변수를 `false`로 설정하여 이러한 계측을 표시하지 않을 수도 있습니다.

* `APPLICATIONINSIGHTS_INSTRUMENTATION_AZURE_SDK_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_CASSANDRA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JDBC_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_JMS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_KAFKA_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MICROMETER_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_MONGO_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_RABBITMQ_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_REDIS_ENABLED`
* `APPLICATIONINSIGHTS_INSTRUMENTATION_SPRING_SCHEDULING_ENABLED`

(json 구성에서 지정된 사용 설정보다 우선 적용됩니다.)

> [!NOTE]
> 더 세분화된 컨트롤을 원하는 경우(예: 모든 redis 호출이 아니라 일부 redis 호출을 표시하지 않는 경우)에는 [샘플링 재정의](./java-standalone-sampling-overrides.md)를 참조하세요.

## <a name="preview-instrumentations"></a>Instrumentations 미리 보기

3.2.0 버전부터 다음 미리 보기 instrumentations를 사용 하도록 설정할 수 있습니다.

```
{
  "preview": {
    "instrumentation": {
      "apacheCamel": {
        "enabled": true
      },
      "grizzly": {
        "enabled": true
      },
      "quartz": {
        "enabled": true
      },
      "springIntegration": {
        "enabled": true
      }
    }
  }
}
```

## <a name="heartbeat"></a>하트비트

기본적으로 Application Insights Java 3.x는 15분마다 한 번씩 하트비트 메트릭을 보냅니다.
하트비트 메트릭을 사용하여 경고를 트리거하는 경우 이 하트비트의 빈도를 늘릴 수 있습니다.

```json
{
  "heartbeat": {
    "intervalSeconds": 60
  }
}
```

> [!NOTE]
> 하트비트 데이터가 Application Insights 사용을 추적하는 데에도 사용되므로 간격을 15분보다 길게 늘릴 수 없습니다.

## <a name="http-proxy"></a>HTTP 프록시

애플리케이션이 방화벽 뒤에 있고 Application Insights에 직접 연결할 수 없는 경우([Application Insights에서 사용하는 IP 주소](./ip-addresses.md) 참조), HTTP 프록시를 사용하도록 Application Insights Java 3.x를 구성할 수 있습니다.

```json
{
  "proxy": {
    "host": "myproxy",
    "port": 8080
  }
}
```

Application Insights Java 3.x는 설정된 경우 전역 `https.proxyHost` 및 `https.proxyPort` 시스템 속성(과 필요한 경우 `http.nonProxyHosts`)도 적용합니다.

## <a name="metric-interval"></a>메트릭 간격

이 기능은 미리 보기 상태입니다.

기본적으로 메트릭은 60초마다 캡처됩니다.

버전 3.0.3부터 이 간격을 변경할 수 있습니다.

```json
{
  "preview": {
    "metricIntervalSeconds": 300
  }
}
```

설정은 다음과 같은 모든 메트릭에 적용됩니다.

* 기본 성능 카운터(예: CPU 및 메모리)
* 기본 사용자 지정 메트릭(예: 가비지 수집 타이밍)
* 구성된 JMX 메트릭([위 참조](#jmx-metrics))
* 마이크로미터 메트릭([위 참조](#auto-collected-micrometer-metrics-including-spring-boot-actuator-metrics))


[//]: # "참고 OpenTelemetry 지원은 OpenTelemetry API가 1.0에 도달할 때까지 프라이빗 미리 보기 상태입니다."

[//]: # "## OpenTelemetry API 이전 1.0 릴리스 지원"

[//]: # "OpenTelemetry API가 아직 안정적이지 않으므로 OpenTelemetry API의 이전 1.0 버전에 대한 지원이 옵트인됩니다."
[//]: # "그리고 각 버전의 에이전트는 OpenTelemetry API의 특정 이전 1.0 버전만 지원합니다."
[//]: # "(OpenTelemetry API 1.0이 릴리스된 후에는 이 제한이 적용되지 않습니다.)"

[//]: # "```json"
[//]: # "{"
[//]: # "  \"미리 보기\": {"
[//]: # "    \"openTelemetryApiSupport\": true"
[//]: # "  }"
[//]: # "}"
[//]: # "```"

## <a name="authentication-preview"></a>인증(미리 보기)
> [!NOTE]
> 인증 기능은 버전 3.2.0-BETA부터 사용 가능합니다.

이를 통해 Azure Active Directory 인증에 필요한 [토큰 자격 증명](/java/api/overview/azure/identity-readme#credentials)을 생성하도록 에이전트를 구성할 수 있습니다.
자세한 내용은 [인증](./azure-ad-authentication.md) 문서를 확인하세요.

## <a name="self-diagnostics"></a>자체 진단

“자체 진단”은 Application Insights Java 3.x의 내부 로깅을 참조합니다.

이 기능은 Application Insights 자체의 문제를 발견하고 진단하는 데 유용할 수 있습니다.

기본적으로 Application Insights Java 3.x는 다음 구성에 해당하는 `applicationinsights.log` 파일과 콘솔 모두에 대해 `INFO` 수준으로 로그를 기록합니다.

```json
{
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```

`destination`은 `file`, `console` 또는 `file+console` 중 하나일 수 있습니다.

`level`은 `OFF`, `ERROR`, `WARN`, `INFO`, `DEBUG` 또는 `TRACE` 중 하나일 수 있습니다.

`path`는 절대 또는 상대 경로일 수 있습니다. 상대 경로는 `applicationinsights-agent-3.2.0.jar`이 있는 디렉터리를 기준으로 확인됩니다.

`maxSizeMb`는 롤오버되기 전에 로그 파일의 최대 크기입니다.

`maxHistory`는 현재 로그 파일 외에도 유지되는 로그 파일에 대해 롤업되는 수입니다.

버전 3.0.2부터 환경 변수 `APPLICATIONINSIGHTS_SELF_DIAGNOSTICS_LEVEL`을 사용하여 자체 진단을 `level`로 설정할 수도 있습니다. 이는 json 구성에 지정된 자체 진단 수준보다 우선 적용됩니다.

## <a name="an-example"></a>예제

이는 구성 파일이 여러 구성 요소와 같이 표시되는 것을 보여 주는 예제입니다.
요구 사항에 따라 특정 옵션을 구성하세요.

```json
{
  "connectionString": "InstrumentationKey=...",
  "role": {
    "name": "my cloud role name"
  },
  "sampling": {
    "percentage": 100
  },
  "jmxMetrics": [
  ],
  "customDimensions": {
  },
  "instrumentation": {
    "logging": {
      "level": "INFO"
    },
    "micrometer": {
      "enabled": true
    }
  },
  "proxy": {
  },
  "preview": {
    "processors": [
    ]
  },
  "selfDiagnostics": {
    "destination": "file+console",
    "level": "INFO",
    "file": {
      "path": "applicationinsights.log",
      "maxSizeMb": 5,
      "maxHistory": 1
    }
  }
}
```