---
title: 샘플링 재정의(미리 보기) - Java용 Azure Monitor Application Insights
description: Java용 Azure Monitor Application Insights에서 샘플링 재정의를 구성하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 03/22/2021
author: trask
ms.custom: devx-track-java
ms.author: trstalna
ms.openlocfilehash: c0f6c1b0fce97bc835cb63a47d8827d7fab8ed56
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599832"
---
# <a name="sampling-overrides-preview---azure-monitor-application-insights-for-java"></a>샘플링 재정의(미리 보기) - Java용 Azure Monitor Application Insights

> [!NOTE]
> 샘플링 재정의 기능은 3.0.3부터 미리 보기 상태입니다.

샘플링 재정의를 사용하면 [기본 샘플링 백분율](./java-standalone-config.md#sampling)을 재정의할 수 있습니다. 예를 들면 다음과 같습니다.
 * 잡음 상태 검사에 대한 샘플링 백분율을 0이나 작은 값으로 설정합니다.
 * 잡음 종속성 호출에 대한 샘플링 백분율을 0이나 작은 값으로 설정합니다.
 * 기본 샘플링이 더 낮은 것으로 구성된 경우에도 중요한 요청 유형(예: `/login`)에 대해 샘플링 백분율을 100으로 설정합니다.

## <a name="terminology"></a>용어

샘플링 재정의에 관해 알아보기 전에 ‘범위’라는 용어를 이해해야 합니다. 범위는 다음에 대한 일반적인 용어입니다.

* 들어오는 요청.
* 나가는 종속성(예: 다른 서비스에 대한 원격 호출).
* In-process 종속성(예: 서비스의 하위 구성 요소에 의해 수행되는 작업).

샘플링 재정의의 경우 다음 범위 구성 요소가 중요합니다.

* 특성

범위 특성은 지정된 요청 또는 종속성의 표준 및 사용자 지정 속성을 둘 다 나타냅니다.

## <a name="getting-started"></a>시작

시작하려면 *applicationinsights.json* 이라는 구성 파일을 만듭니다. 이를 동일한 디렉터리에 *applicationinsights-agent-\*.jar* 로 저장합니다. 다음 템플릿을 사용합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            ...
          ],
          "percentage": 0
        },
        {
          "attributes": [
            ...
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="how-it-works"></a>작동 방식

범위가 시작될 때 해당 시간에 범위에 있는 특성을 사용하여 샘플링 재정의가 일치하는지 확인합니다.

샘플링 재정의 중 하나가 일치하는 경우 해당 샘플링 백분율을 사용하여 범위를 샘플링할지 여부를 결정합니다.

일치하는 첫 번째 샘플링 재정의만 사용됩니다.

샘플링 재정의가 일치하지 않는 경우:

* 추적의 첫 번째 범위인 경우 [기본 샘플링 백분율](./java-standalone-config.md#sampling)이 사용됩니다.
* 추적의 첫 번째 범위가 아닌 경우 부모 샘플링 결정이 사용됩니다.

> [!WARNING]
> 범위를 수집하지 않기로 결정된 경우 다운스트림 범위와 일치하는 샘플링 재정의가 있더라도 모든 다운스트림 범위도 수집되지 않습니다.
> 그렇지 않으면 다운스트림 범위가 수집되지만 수집되지 않은 범위에 부모가 되어 추적이 끊어지기 때문에 이 동작이 필요합니다.

> [!NOTE]
> 샘플링 결정은 traceId(operationId라고도 함)를 0~100 사이의 숫자로 해시하는 작업을 기반으로 하며 이후 해당 해시는 샘플링 백분율에 비교됩니다.
> 지정된 추적의 모든 범위는 동일한 traceId를 가지므로 동일한 해시를 가집니다. 따라서 샘플링 결정은 전체 추적에서 일관됩니다.

## <a name="example-suppress-collecting-telemetry-for-health-checks"></a>예: 상태 검사에 대한 원격 분석 수집 안 함

이 기능은 `/health-checks`에 대한 모든 요청의 원격 분석을 수집하지 않습니다.

또한 일반적으로 `/health-checks`에서 수집되는 다운스트림 범위(종속성)를 수집하지 않습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/health-check",
              "matchType": "regexp"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-suppress-collecting-telemetry-for-a-noisy-dependency-call"></a>예: 노이즈 종속성 호출에 대한 원격 분석 수집 안 함

이 기능은 모든 `GET my-noisy-key` redis 호출에 대한 원격 분석을 수집하지 않습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "db.system",
              "value": "redis",
              "matchType": "strict"
            },
            {
              "key": "db.statement",
              "value": "GET my-noisy-key",
              "matchType": "strict"
            }
          ],
          "percentage": 0
        }
      ]
    }
  }
}
```

## <a name="example-collect-100-of-telemetry-for-an-important-request-type"></a>예: 중요한 요청 유형에 대한 원격 분석의 100% 수집

이 기능은 `/login`에 대한 원격 분석의 100%를 수집합니다.

다운스트림 범위(종속성)는 부모의 샘플링 결정(해당 다운스트림 범위에 대한 샘플링 재정의 제외)을 준수하므로 모든 ‘/login’ 요청에 대해서도 수집됩니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "sampling": {
    "percentage": 10
  },
  "preview": {
    "sampling": {
      "overrides": [
        {
          "attributes": [
            {
              "key": "http.url",
              "value": "https?://[^/]+/login",
              "matchType": "regexp"
            }
          ],
          "percentage": 100
        }
      ]
    }
  }
}
```

## <a name="common-span-attributes"></a>공통 범위 특성

이 섹션에는 샘플링 재정의가 사용할 수 있는 몇 가지 일반적인 범위 특성이 나열됩니다.

### <a name="http-spans"></a>HTTP 범위

| attribute  | Type | Description | 
|---|---|---|
| `http.method` | 문자열 | HTTP 요청 메서드입니다.|
| `http.url` | 문자열 | `scheme://host[:port]/path?query[#fragment]` 형식의 전체 HTTP 요청 URL. 조각은 일반적으로 HTTP를 통해 전송되지 않습니다. 하지만 조각이 알려져 있는 경우 이를 포함해야 합니다.|
| `http.flavor` | 문자열 | HTTP 프로토콜의 유형입니다. |
| `http.user_agent` | 문자열 | 클라이언트에서 보낸 [HTTP 사용자 에이전트](https://tools.ietf.org/html/rfc7231#section-5.5.3) 헤더의 값입니다. |

`http.status_code`은 범위의 시작 부분에 사용할 수 없으므로 샘플링 결정에 사용할 수 없습니다.

### <a name="jdbc-spans"></a>JDBC 범위

| attribute  | Type | Description  |
|---|---|---|
| `db.system` | 문자열 | 사용 중인 DBMS(데이터베이스 관리 시스템) 제품의 식별자입니다. |
| `db.connection_string` | 문자열 | 데이터베이스에 연결하는 데 사용되는 연결 문자열입니다. 포함된 자격 증명을 제거하는 것이 좋습니다.|
| `db.user` | 문자열 | 데이터베이스에 액세스하기 위한 사용자 이름입니다. |
| `db.name` | 문자열 | 액세스 중인 데이터베이스의 이름을 보고하는 데 사용되는 문자열입니다. 데이터베이스를 전환하는 명령의 경우 명령이 실패하더라도 이 문자열을 대상 데이터베이스로 설정해야 합니다.|
| `db.statement` | 문자열 | 실행 중인 데이터베이스 문입니다.|
