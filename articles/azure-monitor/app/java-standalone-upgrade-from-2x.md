---
title: 2\.x에서 업그레이드 - Azure Monitor Application Insights Java
description: Azure Monitor Application Insights Java 2.x에서 업그레이드
ms.topic: conceptual
ms.date: 11/25/2020
author: MS-jgol
ms.custom: devx-track-java
ms.author: jgol
ms.openlocfilehash: cbfdc8c7e07a68335083c529e545143a513b1808
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129858275"
---
# <a name="upgrading-from-application-insights-java-2x-sdk"></a>Application Insights Java 2.x SDK에서 업그레이드

애플리케이션에서 Application Insights Java 2.x SDK를 이미 사용하고 있는 경우 이를 계속해서 사용할 수 있습니다.
Application Insights Java 3.x 에이전트는 이를 감지하고, 2.x SDK를 통해 보내는 모든 사용자 지정 원격 분석을 캡처하고 상관 관계를 지정하면서 중복 원격 분석을 방지하기 위해 2.x SDK에서 수행하는 자동 수집을 억제합니다.

Application Insights 2.x 에이전트를 사용 중인 경우 2.x 에이전트를 가리키는 `-javaagent:` JVM 인수를 제거해야 합니다.

이 문서의 나머지 부분에서는 2.x에서 3.x로 업그레이드할 때 발생할 수 있는 제한 사항 및 변경 내용과 도움이 될 수 있는 몇 가지 해결 방법을 설명합니다.

## <a name="telemetryinitializers-and-telemetryprocessors"></a>TelemetryInitializers 및 TelemetryProcessors

3\.x 에이전트를 사용하는 경우 2.x SDK TelemetryInitializers 및 TelemetryProcessors가 실행되지 않습니다.
이전에 이를 필요로 했던 많은 사용 사례는 [사용자 지정 차원](./java-standalone-config.md#custom-dimensions)을 구성하거나 [원격 분석 프로세서](./java-standalone-telemetry-processors.md)를 구성하여 Application Insights Java 3.x에서 해결할 수 있습니다.

## <a name="multiple-applications-in-a-single-jvm"></a>단일 JVM의 여러 애플리케이션

현재 Application Insights Java 3.x는 실행 중인 프로세스당 단일 [연결 문자열 및 역할 이름](./java-standalone-config.md#connection-string-and-role-name)만 지원합니다. 특히, 아직 동일한 tomcat 배포에 서로 다른 연결 문자열 또는 역할 이름을 사용하여 여러 tomcat 웹앱을 사용할 수 없습니다.

## <a name="operation-names"></a>작업 이름

Application Insights Java 2.x SDK에서 작업 이름에 전체 경로가 포함된 경우도 있습니다. 예:

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-with-full-path.png" alt-text="전체 경로가 포함된 작업 이름을 보여 주는 스크린샷":::

Application Insights Java 3.x의 작업 이름은 일반적으로 Application Insights Portal U/X에서 더 나은 집계 보기를 제공하도록 변경되었습니다. 예:

:::image type="content" source="media/java-ipa/upgrade-from-2x/operation-names-parameterized.png" alt-text="매개 변수화된 작업 이름을 보여 주는 스크린샷":::

그러나 일부 애플리케이션의 경우 이전 작업 이름에서 제공한 U/X의 집계된 뷰를 선호할 수 있습니다. 이 경우 3.x에서 [원격 분석 프로세서](./java-standalone-telemetry-processors.md)(미리 보기) 기능을 사용하여 이전 동작을 복제할 수 있습니다.

아래 코드 조각은 이전 동작을 복제하기 위해 결합된 3개의 원격 분석 프로세서를 구성합니다.
원격 분석 프로세서는 다음 작업을 순서대로 수행합니다.

1. 첫 번째 원격 분석 프로세서는 특성 프로세서(유형 `attribute`)입니다. 즉, 특성(현재 `requests` 및 `dependencies`, 곧 `traces`도)이 있는 모든 원격 분석에 적용됩니다.

   이름이 `http.method` 및 `http.url`인 특성이 있는 모든 원격 분석과 일치합니다.

   그런 다음 `http.url` 특성의 경로 부분을 `tempName`이라는 새 특성으로 추출합니다.

2. 두 번째 원격 분석 프로세서는 범위 프로세서(유형 `span`)이며, 이는 `requests` 및 `dependencies`에 적용됨을 의미합니다.

   이름이 `tempPath`인 특성이 있는 모든 범위와 일치합니다.

   그런 다음 `tempPath` 특성에서 범위 이름을 업데이트합니다.

3. 마지막 원격 분석 프로세서는 첫 번째 원격 분석 프로세서와 동일한 유형인 특성 프로세서입니다.

   `tempPath`라는 특성이 있는 모든 원격 분석과 일치합니다.

   그런 다음 `tempPath`라는 특성을 삭제하므로 사용자 지정 차원으로 보고되지 않습니다.

```
{
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "http.method" },
            { "key": "http.url" }
          ]
        },
        "actions": [
          {
            "key": "http.url",
            "pattern": "https?://[^/]+(?<tempPath>/[^?]*)",
            "action": "extract"
          }
        ]
      },
      {
        "type": "span",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "name": {
          "fromAttributes": [ "http.method", "tempPath" ],
          "separator": " "
        }
      },
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "attributes": [
            { "key": "tempPath" }
          ]
        },
        "actions": [
          { "key": "tempPath", "action": "delete" }
        ]
      }
    ]
  }
}
```

## <a name="2x-sdk-logging-appenders"></a>2.x SDK 로깅 어펜더

Application Insights Java 3.x는 로깅 어펜더를 구성할 필요 없이 [로깅을 자동으로 수집](./java-standalone-config.md#auto-collected-logging)합니다.
2\.x SDK 로깅 어펜더를 사용하는 경우 Application Insights Java 3.x에 의해 억제되므로 제거될 수 있습니다.

## <a name="2x-sdk-spring-boot-starter"></a>2.x SDK Spring Boot Starter

Java 3.x Spring Boot Starter에는 Application Insights가 없습니다.
3.x 설정 및 구성은 Spring Boot 사용 여부에 관계없이 동일한 [간단한 단계](./java-in-process-agent.md#get-started)를 따릅니다.

Application Insights Java 2.x SDK Spring Boot Starter에서 업그레이드할 때 클라우드 역할 이름이 더 이상 `spring.application.name`으로 기본 설정되지 않습니다.
json 구성 또는 환경 변수를 통해 3.x에서 클라우드 역할 이름을 설정하려면 [3.x 구성 문서](./java-standalone-config.md#cloud-role-name)를 참조하세요.
