---
title: 원격 분석 프로세서의 예 - Java용 Azure Monitor Application Insights
description: Java용 Azure Monitor Application Insights의 원격 분석 프로세서를 보여주는 예를 살펴봅니다.
ms.topic: conceptual
ms.date: 12/29/2020
author: kryalama
ms.custom: devx-track-java
ms.author: kryalama
ms.openlocfilehash: 5d704ed2213a77873780a005823f25541e6563d0
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/10/2021
ms.locfileid: "111895349"
---
# <a name="telemetry-processor-examples---azure-monitor-application-insights-for-java"></a>원격 분석 프로세서의 예 - Java용 Azure Monitor Application Insights

이 문서에서는 Java용 Application Insights의 원격 분석 프로세서 예를 제공합니다. include 및 exclude 구성에 대한 샘플이 있습니다. 또한 특성 프로세서와 범위 프로세서에 대한 샘플도 있습니다.
## <a name="include-and-exclude-span-samples"></a>범위 포함 및 제외 샘플

이 섹션에서는 범위를 포함 및 제외하는 방법을 설명합니다. 또한 여러 범위를 제외하고 선택적 처리를 적용하는 방법도 설명합니다.
### <a name="include-spans"></a>범위 포함

이 섹션에서는 특성 프로세서의 범위를 포함하는 방법을 보여줍니다. 속성과 일치하지 않는 범위는 프로세서에서 처리되지 않습니다.

일치하는 범위를 찾으려면 범위 이름이 `spanA` 또는 `spanB`와 동일해야 합니다. 

이러한 범위는 include 속성과 일치하며 프로세서 동작이 적용됩니다.
* Span1 이름: 'spanA' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 이름: 'spanB' 특성: {env: dev, test_request: false}
* Span3 이름: 'spanA' 특성: {env: 1, test_request: dev, credit_card: 1234}

이 범위는 include 속성과 일치하지 않으며 프로세서 작업이 적용되지 않습니다.
* Span4 이름: 'spanC' 특성: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans"></a>범위 제외

이 섹션에서는 특성 프로세서의 범위를 제외하는 방법을 보여줍니다. 속성과 일치하는 범위는 프로세서에서 처리되지 않습니다.

일치하는 범위를 찾으려면 범위 이름이 `spanA` 또는 `spanB`와 동일해야 합니다.

다음 범위는 exclude 속성과 일치하며 프로세서 작업이 적용되지 않습니다.
* Span1 이름: 'spanA' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 이름: 'spanB' 특성: {env: dev, test_request: false}
* Span3 이름: 'spanA' 특성: {env: 1, test_request: dev, credit_card: 1234}

이 범위는 exclude 속성과 일치하지 않으며 프로세서 작업이 적용됩니다.
* Span4 이름: 'spanC' 특성: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="exclude-spans-by-using-multiple-criteria"></a>여러 조건을 사용하여 범위 제외

이 섹션에서는 특성 프로세서의 범위를 제외하는 방법을 보여줍니다. 속성과 일치하는 범위는 프로세서에서 처리되지 않습니다.

일치하는 범위를 찾으려면 다음 조건이 충족되어야 합니다.
* 특성(예: `dev` 값이 있는 `env`)이 범위에 있어야 합니다.
* 범위에는 `test_request` 키가 포함된 특성이 있어야 합니다.

다음 범위는 exclude 속성과 일치하며 프로세서 작업이 적용되지 않습니다.
* Span1 이름: 'spanB' 특성: {env: dev, test_request: 123, credit_card: 1234}
* Span2 이름: 'spanA' 특성: {env: dev, test_request: false}

다음 범위는 exclude 속성과 일치하지 않으며 프로세서 작업이 적용됩니다.
* Span3 이름: 'spanB' 특성: {env: 1, test_request: dev, credit_card: 1234}
* Span4 이름: 'spanC' 특성: {env: dev, dev_request: false}


```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ],
          "attributes": [
            {
              "key": "env",
              "value": "dev"
            },
            {
              "key": "test_request"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="selective-processing"></a>선택 처리

이 섹션에서는 이 프로세서가 적용되어야 하는 범위를 나타내는 범위 속성 집합을 지정하는 방법을 보여줍니다. include 속성은 처리해야 하는 범위를 나타냅니다. exclude 속성은 처리되지 않아야 하는 범위를 필터링합니다.

다음 구성에서 이러한 범위는 속성과 일치하며 프로세서 작업이 적용됩니다.

* Span1 이름: 'spanB' 특성: {env: production, test_request: 123, credit_card: 1234, redact_trace: "false"}
* Span2 이름: 'spanA' 특성: {env: staging, test_request: false, redact_trace: true}

이러한 범위는 include 속성과 일치하지 않으며 프로세서 작업이 적용되지 않습니다.
* Span3 이름: 'spanB' 특성: {env: production, test_request: true, credit_card: 1234, redact_trace: false}
* Span4 이름: 'spanC' 특성: {env: dev, test_request: false}

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "strict",
          "spanNames": [
            "spanA",
            "spanB"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "attributes": [
            {
              "key": "redact_trace",
              "value": "false"
            }
          ]
        },
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          },
          {
            "key": "duplicate_key",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

## <a name="attribute-processor-samples"></a>특성 프로세서 샘플

### <a name="insert"></a>삽입

다음 샘플은 새 `{"attribute1": "attributeValue1"}` 특성을 `attribute1` 키가 없는 범위와 로그에 삽입합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "attribute1",
            "value": "attributeValue1",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="insert-from-another-key"></a>다른 키에서 삽입

다음 샘플은 `anotherkey` 특성의 값을 사용하여 새 `{"newKey": "<value from attribute anotherkey>"}` 특성을 `newKey` 키가 없는 범위와 로그에 삽입합니다. `anotherkey` 특성이 없으면 범위와 로그에 새 특성이 삽입되지 않습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "newKey",
            "fromAttribute": "anotherKey",
            "action": "insert"
          }
        ]
      }
    ]
  }
}
```

### <a name="update"></a>업데이트

다음 샘플에서는 특성을 `{"db.secret": "redacted"}`로 업데이트합니다. 특성 `foo`의 값을 사용하여 `boo` 특성을 업데이트합니다. `boo` 특성이 없는 범위와 로그는 변경되지 않습니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "db.secret",
            "value": "redacted",
            "action": "update"
          },
          {
            "key": "boo",
            "fromAttribute": "foo",
            "action": "update" 
          }
        ]
      }
    ]
  }
}
```

### <a name="delete"></a>삭제

다음 샘플에서는 `credit_card` 키가 있는 특성을 삭제하는 방법을 보여줍니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "credit_card",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```

### <a name="hash"></a>해시

다음 샘플에서는 기존 특성 값을 해시하는 방법을 보여줍니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "user.email",
            "action": "hash"
          }
        ]
      }
    ]
  }
}
```

### <a name="extract"></a>추출

다음 샘플에서는 정규식(regex)을 사용하여 다른 특성 값을 기준으로 새 특성을 만드는 방법을 보여줍니다.
예를 들어 `http.url = http://example.com/path?queryParam1=value1,queryParam2=value2`가 지정되면 다음 특성이 삽입됩니다.
* httpProtocol: `http`
* httpDomain: `example.com`
* httpPath: `path`
* httpQueryParams: `queryParam1=value1,queryParam2=value2`
* http.url: 변경 *없음*

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "actions": [
          {
            "key": "http.url",
            "pattern": "^(?<httpProtocol>.*):\\/\\/(?<httpDomain>.*)\\/(?<httpPath>.*)(\\?|\\&)(?<httpQueryParams>.*)",
            "action": "extract"
          }
        ]
      }
    ]
  }
}
```

다음 샘플에서는 regex 패턴과 일치하는 범위 이름이 있는 범위를 처리하는 방법을 보여줍니다.
이 프로세서는 `token` 특성을 제거합니다. 범위 이름이 `auth.*`와 일치는 범위와 범위 이름이 `login.*`과 일치하지 않는 범위의 `password` 특성을 난독 처리합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "attribute",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "auth.*"
          ]
        },
        "exclude": {
          "matchType": "regexp",
          "spanNames": [
            "login.*"
          ]
        },
        "actions": [
          {
            "key": "password",
            "value": "obfuscated",
            "action": "update"
          },
          {
            "key": "token",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```


## <a name="span-processor-samples"></a>범위 프로세서 샘플

### <a name="name-a-span"></a>범위 이름 지정

다음 샘플에서는 `db.svc`, `operation` 및 `id` 특성 값을 지정합니다. `::` 값으로 구분된 해당 특성을 순서대로 사용하여 범위의 새 이름을 지정합니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "fromAttributes": [
            "db.svc",
            "operation",
            "id"
          ],
          "separator": "::"
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name"></a>범위 이름에서 특성 추출

입력 범위 이름이 `/api/v1/document/12345678/update`라고 가정해 보겠습니다. 다음 샘플은 출력 범위 이름 `/api/v1/document/{documentId}/update`으로 표시됩니다. 새 `documentId=12345678` 특성을 범위에 추가합니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "name": {
          "toAttributes": {
            "rules": [
              "^/api/v1/document/(?<documentId>.*)/update$"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="extract-attributes-from-a-span-name-by-using-include-and-exclude"></a>include 및 exclude를 사용하여 범위 이름에서 특성을 추출합니다.

다음 샘플에서는 범위 이름을 `{operation_website}`로 변경하는 방법을 보여줍니다. 범위에 다음 속성이 있으면 `operation_website` 키와 `{oldSpanName}` 값이 있는 특성을 추가합니다.
- 범위 이름의 문자열 아무 곳에 `/`가 포함됩니다.
- 범위 이름은 `donot/change`가 아닙니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "span",
        "include": {
          "matchType": "regexp",
          "spanNames": [
            "^(.*?)/(.*?)$"
          ]
        },
        "exclude": {
          "matchType": "strict",
          "spanNames": [
            "donot/change"
          ]
        },
        "name": {
          "toAttributes": {
            "rules": [
              "(?<operation_website>.*?)$"
            ]
          }
        }
      }
    ]
  }
}
```


## <a name="log-processor-samples"></a>로그 프로세서 샘플

### <a name="extract-attributes-from-a-log-message-body"></a>로그 메시지 본문에서 특성 추출

입력 로그 메시지 본문이 `Starting PetClinicApplication on WorkLaptop with PID 27984 (C:\randompath\target\classes started by userx in C:\randompath)`라고 가정해 보겠습니다. 다음 샘플은 출력 메시지 본문 `Starting PetClinicApplication on WorkLaptop with PID {PIDVALUE} (C:\randompath\target\classes started by userx in C:\randompath)`로 표시됩니다. 새 `PIDVALUE=27984` 특성을 로그에 추가합니다.

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "log",
        "body": {
          "toAttributes": {
            "rules": [
              "^Starting PetClinicApplication on WorkLaptop with PID (?<PIDVALUE>\\d+) .*"
            ]
          }
        }
      }
    ]
  }
}
```

### <a name="masking-sensitive-data-in-log-message"></a>로그 메시지의 중요한 데이터 마스킹

다음 샘플에서는 로그 프로세서와 특성 프로세서를 모두 사용하여 로그 메시지 본문에서 중요한 데이터를 마스킹하는 방법을 보여줍니다.
입력 로그 메시지 본문이 `User account with userId 123456xx failed to login`이라고 가정해 보겠습니다. 로그 프로세서는 출력 메시지 본문을 `User account with userId {redactedUserId} failed to login`으로 업데이트하며 특성 프로세서는 이전 단계에서 추가한 새 `redactedUserId` 특성을 삭제합니다.
```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000",
  "preview": {
    "processors": [
      {
        "type": "log",
        "body": {
          "toAttributes": {
            "rules": [
              "^User account with userId (?<redactedUserId>\\d+) .*"
            ]
          }
        }
      },
      {
        "type": "attribute",
        "actions": [
          {
            "key": "redactedUserId",
            "action": "delete"
          }
        ]
      }
    ]
  }
}
```