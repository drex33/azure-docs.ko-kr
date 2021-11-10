---
title: Azure 프런트 도어 규칙 일치 조건 구성
description: 이 문서에서는 Azure Front 도어 규칙에서 사용할 수 있는 다양 한 일치 조건 목록을 제공 합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yuajia
ms.openlocfilehash: bb73ab8d36223a8bbac50fde2bb14d355ce26a10
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132161052"
---
# <a name="azure-front-door-rules-match-conditions"></a>Azure 전방 도어 규칙 일치 조건

azure front 도어 [규칙 엔진과](front-door-rules-engine.md) azure front 도어 Standard/Premium [규칙 집합](standard-premium/concept-rule-set.md)에서 규칙은 없음 또는 일부 일치 조건 및 동작으로 구성 됩니다. 이 문서에서는 Azure Front 도어 규칙 집합 또는 규칙 엔진에서 사용할 수 있는 일치 조건에 대 한 자세한 설명을 제공 합니다.

규칙의 첫 번째 파트는 일치 조건 또는 일치 조건 세트입니다. 최대 10개의 일치 조건으로 규칙을 구성할 수 있습니다. 일치 조건은 정의된 작업이 수행되는 특정 유형의 요청을 식별합니다. 여러 일치 조건을 사용하면 AND 논리를 사용하여 일치 조건이 그룹화됩니다. 여러 값을 지원하는 모든 일치 조건에는 OR 논리를 사용합니다.

다음 작업에 일치 조건을 사용할 수 있습니다.

* 특정 IP 주소, 국가 또는 지역을 기준으로 요청을 필터링합니다.
* 헤더 정보에 의한 필터 요청
* 모바일 디바이스 또는 데스크톱 디바이스에서 요청을 필터링합니다.
* 요청 파일 이름 및 파일 확장명에서 요청을 필터링합니다.
* 요청 URL, 프로토콜, 경로, 쿼리 문자열, POST 인수 등에서 요청을 필터링합니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="device-type"></a>디바이스 유형

**디바이스 유형** 일치 조건을 사용하여 모바일 디바이스나 데스크톱 디바이스에서 이루어진 요청을 식별합니다.  

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-------|------------------|
| 연산자 | <ul><li>Azure Portal: `Equal`, `Not Equal`</li><li>ARM 템플릿: `Equal`, `negateCondition` 속성을 사용하여 _같지 않음_ 을 지정합니다. |
| 값 | `Mobile`, `Desktop` |

### <a name="example"></a>예제

이 예제에서는 모바일 디바이스로부터 발생한 것으로 확인된 모든 요청을 일치시킵니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/device-type.png" alt-text="디바이스 유형 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "IsDevice",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "Mobile"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'IsDevice'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'Mobile'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleIsDeviceConditionParameters'
  }
}
```

---

## <a name="post-args"></a>POST 인수

**POST 인수** 일치 조건을 사용하여 POST 요청의 본문 안에서 제공된 인수에 따라 요청을 식별합니다. 단일 일치 조건은 POST 요청의 본문에서 단일 인수를 일치시킵니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

> [!NOTE]
> **POST 인수** 일치 조건은 `application/x-www-form-urlencoded` 콘텐츠 유형에서 작동합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| POST 인수 | POST 인수의 이름을 나타내는 문자열 값입니다. |
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 POST 인수의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 본문에서 `customerName` 인수를 제공하고 `customerName` 값이 문자 `J` 또는 `K`로 시작하는 모든 POST 요청을 일치시킵니다. `J`, `j`, `K` 및 `k`를 모두 일치시키도록 대/소문자 변환을 사용하여 입력 값을 대문자로 변환합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/post-args.png" alt-text="POST 인수 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "PostArgs",
  "parameters": {
    "selector": "customerName",
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
        "J",
        "K"
    ],
    "transforms": [
        "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters"
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'PostArgs'
  parameters: {
    selector: 'customerName'
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'J'
      'K'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRulePostArgsConditionParameters'
  }
}
```

---

## <a name="query-string"></a>쿼리 문자열

**쿼리 문자열** 일치 조건을 사용하여 특정 쿼리 문자열을 포함하는 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

> [!NOTE]
> 전체 쿼리 문자열은 선행 `?` 없이 단일 문자열으로 일치됩니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 쿼리 문자열 | 일치시킬 쿼리 문자열의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 쿼리 문자열의 시작 부분에 `?`를 포함하지 않습니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 쿼리 문자열이 `language=en-US` 문자열을 포함하는 모든 요청을 일치시킵니다. 일치 조건에서 대/소문자를 구분할 것이므로 대/소문자를 변환하지 않습니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/query-string.png" alt-text="쿼리 문자열 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "QueryString",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "language=en-US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'QueryString'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'language=en-US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleQueryStringConditionParameters'
  }
}
```

---

## <a name="remote-address"></a>원격 주소

**원격 주소** 일치 조건은 요청자의 위치 또는 IP 주소에 따라 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

* IP 주소 블록을 지정할 때 CIDR 표기법을 사용합니다. 즉, IP 주소 블록에 대한 구문은 뒤에 슬래시와 접두사 크기가 오는 기본 IP 주소입니다. 예를 들어 다음과 같은 가치를 제공해야 합니다.
    * **IPv4 예제**: `5.5.5.64/26`은 5.5.5.64~5.5.5.127 주소에서 도착하는 요청과 일치합니다.
    * **IPv6 예제**: `1:2:3:/48`은 1:2:3:0:0:0:0:0~1:2:3: ffff:ffff:ffff:ffff:ffff 주소에서 도착하는 요청과 일치합니다.
* 여러 IP 주소와 IP 주소 블록을 지정할 때는 ‘OR’ 논리를 적용합니다.
    * **IPv4 예**: 두 IP 주소 `1.2.3.4` 및 `10.20.30.40`을 추가할 경우 주소 1.2.3.4 또는 10.20.30.40에서 오는 모든 요청에 대해 조건을 일치시킵니다.
    * **IPv6 예**: 두 개의 IP 주소를 추가 하는 경우 `1:2:3:4:5:6:7:8` `10:20:30:40:50:60:70:80` 1:2:3:4:5:6:7:8 또는 10:20:30:40:50:60:70:80 주소에서 도착 하는 모든 요청에 대해 조건이 일치 됩니다.
* 원격 주소는 네트워크 연결의 원래 클라이언트 IP 또는 사용자가 프록시 뒤에 있는 경우 일반적으로 X로 전달 된 요청 헤더를 나타냅니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | <ul><li>Azure Portal에서: `Geo Match`, `Geo Not Match`, `IP Match` 또는 `IP Not Match`</li><li>ARM 템플릿에서: `GeoMatch`, `IPMatch` - `negateCondition` 속성을 사용하여 _지역 일치 안 함_ 또는 _IP 일치 안 함_ 을 지정합니다.</li></ul> |
| 값 | <ul><li>`IP Match` 또는 `IP Not Match` 연산자: 하나 이상의 IP 주소 범위를 지정합니다. 여러 IP 주소 범위를 지정할 경우 OR 논리를 사용하여 평가합니다.</li><li>`Geo Match` 또는 `Geo Not Match` 연산자: 국가 번호를 사용하여 하나 이상의 위치를 지정합니다.</li></ul> |

### <a name="example"></a>예제

이 예제에서는 요청이 미국에서 시작되지 않은 모든 요청과 일치시킵니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/remote-address.png" alt-text="원격 주소 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RemoteAddress",
  "parameters": {
    "operator": "GeoMatch",
    "negateCondition": true,
    "matchValues": [
      "US"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RemoteAddress'
  parameters: {
    operator: 'GeoMatch'
    negateCondition: true
    matchValues: [
      'US'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRemoteAddressConditionParameters'
  }
}
```

---

## <a name="request-body"></a>요청 본문

**요청 본문** 일치 조건은 요청 본문에 표시되는 특정 텍스트를 기준으로 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

> [!NOTE]
> 요청 본문의 크기가 64KB를 초과하는 경우 **요청 본문** 일치 조건에 대해 처음 64KB만 고려됩니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 요청 본문 텍스트의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 본문이 `ERROR` 문자열을 포함하는 모든 요청을 일치시킵니다. 일치를 평가하기 전에, `error` 및 대/소문자가 다른 항목도 이 일치 조건을 트리거하도록 요청 본문을 대문자로 변환합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-body.png" alt-text="요청 본문 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestBody",
  "parameters": {
    "operator": "Contains",
    "negateCondition": false,
    "matchValues": [
      "ERROR"
    ],
    "transforms": [
      "Uppercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestBody'
  parameters: {
    operator: 'Contains'
    negateCondition: false
    matchValues: [
      'ERROR'
    ]
    transforms: [
      'Uppercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestBodyConditionParameters'
  }
}
```

---

## <a name="request-file-name"></a>요청 파일 이름

**요청 파일 이름** 일치 조건은 요청 URL에 지정된 파일 이름을 포함하는 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 요청 파일 이름의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 파일 이름이 `media.mp4`인 모든 요청을 일치시킵니다. 일치를 평가하기 전에, `MEDIA.MP4` 및 대/소문자가 다른 항목도 이 일치 조건을 트리거하도록 파일 이름을 소문자로 변환합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-file-name.png" alt-text="요청 파일 이름 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileName",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "media.mp4"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileName'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'media.mp4'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFilenameConditionParameters'
  }
}
```

---

## <a name="request-file-extension"></a>요청 파일 확장명

**요청 파일 확장명** 일치 조건은 요청 URL에서 파일 이름에 지정된 파일 확장명을 포함하는 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

> [!NOTE]
> 맨 앞에 마침표를 포함하지 않습니다. 예를 들어 `.html` 대신 `html`을 사용합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 요청 파일 확장명의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 맨 앞에 마침표를 포함하지 않습니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 파일 확장명이 `pdf` 또는 `docx`인 모든 요청을 일치시킵니다. 일치를 평가하기 전에, `PDF`, `DocX` 및 대/소문자가 다른 항목도 이 일치 조건을 트리거하도록 요청 파일 확장명을 소문자로 변환합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-file-extension.png" alt-text="요청 파일 확장명 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlFileExtension",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "pdf",
      "docx"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters"
  }
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlFileExtension'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'pdf'
      'docx'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlFileExtensionMatchConditionParameters'
  }
}
```

---

## <a name="request-header"></a>요청 헤더

**요청 헤더** 일치 조건은 요청에 특정 헤더를 포함하는 요청을 식별합니다. 이 일치 조건을 사용하여 값에 관계없이 헤더의 존재 여부를 확인하거나, 헤더가 특정 값과 일치하는지 확인할 수 있습니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 헤더 이름 | POST 인수의 이름을 나타내는 문자열 값입니다. |
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 요청 헤더의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 그 값에 관계없이 요청에 이름이 `MyCustomHeader`인 헤더가 포함된 모든 요청을 일치시킵니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-header.png" alt-text="요청 헤더 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestHeader",
  "parameters": {
    "selector": "MyCustomHeader",
    "operator": "Any",
    "negateCondition": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestHeader'
  parameters: {
    selector: 'MyCustomHeader',
    operator: 'Any'
    negateCondition: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestHeaderConditionParameters'
  }
}
```

---

## <a name="request-method"></a>요청 메서드

**요청 메서드** 일치 조건은 지정된 HTTP 요청 메서드를 사용하는 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | <ul><li>Azure Portal: `Equal`, `Not Equal`</li><li>ARM 템플릿: `Equal`, `negateCondition` 속성을 사용하여 _같지 않음_ 을 지정합니다. |
| 요청 메서드 | `GET`, `POST`, `PUT`, `DELETE`, `HEAD`, `OPTIONS`, `TRACE` 중 하나 이상의 HTTP 메서드입니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |

### <a name="example"></a>예제

이 예제에서는 요청이 `DELETE` 메서드를 사용하는 모든 요청과 일치합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-method.png" alt-text="요청 메서드 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestMethod",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "DELETE"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestMethod'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'DELETE
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestMethodConditionParameters'
  }
}
```

---

## <a name="request-path"></a>요청 경로

**요청 경로** 일치 조건은 요청 URL에 지정된 경로를 포함하는 요청을 식별합니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

> [!NOTE]
> 경로는 호스트 이름과 슬래시 뒤에 있는 URL의 일부입니다. 예를 들어 URL `https://www.contoso.com/files/secure/file1.pdf`에서 경로는 `files/secure/file1.pdf`입니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 요청 경로의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 맨 앞 슬래시는 포함하지 않습니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 파일 경로가 `files/secure/`로 시작하는 모든 요청을 일치시킵니다. 일치를 평가하기 전에, `files/SECURE/`에 대한 요청과 대/소문자가 다른 항목도 이 일치 조건을 트리거하도록 요청 파일 확장명을 소문자로 변환합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-path.png" alt-text="요청 경로 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlPath",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "files/secure/"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlPath'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'files/secure/'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlPathMatchConditionParameters'
  }
}
```

---

## <a name="request-protocol"></a>요청 프로토콜

**요청 프로토콜** 일치 조건은 지정된 프로토콜(HTTP 또는 HTTPS)을 사용하는 요청을 식별합니다.

> [!NOTE]
> *프로토콜* 은 종종 *체계* 라고도 합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | <ul><li>Azure Portal: `Equal`, `Not Equal`</li><li>ARM 템플릿: `Equal`, `negateCondition` 속성을 사용하여 _같지 않음_ 을 지정합니다. |
| 요청 메서드 | `HTTP`, `HTTPS` |

### <a name="example"></a>예제

이 예제에서는 요청이 `HTTP` 프로토콜을 사용하는 모든 요청과 일치합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-protocol.png" alt-text="요청 프로토콜 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestScheme",
  "parameters": {
    "operator": "Equal",
    "negateCondition": false,
    "matchValues": [
      "HTTP"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestScheme'
  parameters: {
    operator: 'Equal'
    negateCondition: false
    matchValues: [
      'HTTP
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestSchemeConditionParameters'
  }
}
```

---

## <a name="request-url"></a>요청 URL

지정된 URL과 일치하는 요청을 식별합니다. 프로토콜 및 쿼리 문자열을 포함하여 조각이 아닌 전체 URL이 평가됩니다. OR 논리로 결합하여 일치시킬 여러 값을 지정할 수 있습니다.

> [!TIP]
> 이 규칙 조건을 사용하는 경우 프로토콜을 포함해야 합니다. 예를 들어, `www.contoso.com` 대신 `https://www.contoso.com`을 사용합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-|-|
| 연산자 | [표준 연산자 목록의](#operator-list) 모든 연산자입니다. |
| 값 | 일치시킬 요청 URL의 값을 나타내는 하나 이상의 문자열 또는 정수 값입니다. 여러 값을 지정할 경우 OR 논리를 사용하여 평가합니다. |
| 대/소문자 변환 | `Lowercase`, `Uppercase` |

### <a name="example"></a>예제

이 예제에서는 요청 URL이 `https://api.contoso.com/customers/123`으로 시작하는 모든 요청을 일치시킵니다. 일치를 평가하기 전에, `https://api.contoso.com/Customers/123`에 대한 요청과 대/소문자가 다른 항목도 이 일치 조건을 트리거하도록 요청 파일 확장명을 소문자로 변환합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/concept-rule-set-match-conditions/request-url.png" alt-text="요청 URL 일치 조건을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "RequestUri",
  "parameters": {
    "operator": "BeginsWith",
    "negateCondition": false,
    "matchValues": [
      "https://api.contoso.com/customers/123"
    ],
    "transforms": [
      "Lowercase"
    ],
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'RequestUri'
  parameters: {
    operator: 'BeginsWith'
    negateCondition: false
    matchValues: [
      'https://api.contoso.com/customers/123'
    ]
    transforms: [
      'Lowercase'
    ]
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleRequestUriConditionParameters'
  }
}
```

---

## <a name="operator-list"></a>운영자 목록

표준 연산자 목록의 값을 허용하는 규칙에는 다음 연산자가 유효합니다.

| 연산자                   | 설명                                                                                                                    | ARM 템플릿 지원                                            |
|----------------------------|--------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| 모두                        | 값에 관계없이 값이 있으면 일치합니다.                                                                     | `operator`: `Any`                                               |
| 같음                      | 값이 지정된 문자열과 정확하게 같으면 일치합니다.                                                                   | `operator`: `Equal`                                             |
| 포함                   | 값이 지정된 문자열을 포함하면 일치합니다.                                                                          | `operator`: `Contains`                                          |
| 미만                  | 값의 길이가 지정된 정수보다 작으면 일치합니다.                                                       | `operator`: `LessThan`                                          |
| 다음보다 큼               | 값의 길이가 지정된 정수보다 크면 일치합니다.                                                    | `operator`: `GreaterThan`                                       |
| 작거나 같음         | 값의 길이가 지정된 정수 이하이면 일치합니다.                                           | `operator`: `LessThanOrEqual`                                   |
| 크거나 같음      | 값의 길이가 지정된 정수 이상이면 일치합니다.                                        | `operator`: `GreaterThanOrEqual`                                |
| 시작 문자                | 값이 지정된 문자열로 시작하는 경우 일치합니다.                                                                       | `operator`: `BeginsWith`                                        |
| 끝 문자                  | 값이 지정된 문자열로 끝나는 경우 일치합니다.                                                                         | `operator`: `EndsWith`                                          |
| RegEx                      | 값이 지정된 정규식과 같으면 일치합니다. [자세한 내용은 아래를 참조하세요.](#regular-expressions)        | `operator`: `RegEx`                                             |
| 없음                    | 값이 없는 경우 일치합니다.                                                                                                | `operator`: `Any` 및 `negateCondition` : `true`                |
| 같지 않음                  | 값이 지정된 문자열과 일치하지 않는 경우 일치합니다.                                                                    | `operator`: `Equal` 및 `negateCondition` : `true`              |
| 포함하지 않음               | 값이 지정된 문자열을 포함하지 않는 경우 일치합니다.                                                                  | `operator`: `Contains` 및 `negateCondition` : `true`           |
| 작지 않음              | 값의 길이가 지정된 정수보다 작지 않으면 일치합니다.                                                   | `operator`: `LessThan` 및 `negateCondition` : `true`           |
| 크지 않음           | 값의 길이가 지정된 정수보다 크지 않으면 일치합니다.                                                | `operator`: `GreaterThan` 및 `negateCondition` : `true`        |
| 작거나 같지 않음     | 값의 길이가 지정된 정수보다 작거나 같지 않으면 일치합니다.                                       | `operator`: `LessThanOrEqual` 및 `negateCondition` : `true`    |
| 크거나 같지 않음 | 값의 길이가 지정된 정수보다 크거나 같지 않으면 일치합니다.                                    | `operator`: `GreaterThanOrEqual` 및 `negateCondition` : `true` |
| 다음으로 시작하지 않는 경우            | 값이 지정된 문자열로 시작하지 않는 경우 일치합니다.                                                               | `operator`: `BeginsWith` 및 `negateCondition` : `true`         |
| 다음으로 끝나지 않는 경우              | 값이 지정된 문자열로 끝나지 않는 경우 일치합니다.                                                                 | `operator`: `EndsWith` 및 `negateCondition` : `true`           |
| RegEx가 아님                  | 값이 지정된 정규식과 같지 않으면 일치합니다. [자세한 내용은 아래를 참조하세요.](#regular-expressions) | `operator`: `RegEx` 및 `negateCondition` : `true`              |

> [!TIP]
> *작거나 같음* 이나 *크거나 같음* 처럼 숫자 연산자의 경우 길이를 기준으로 비교합니다. 일치 조건의 값은 비교하려는 길이를 지정하는 정수여야 합니다.

### <a name="regular-expressions"></a>정규식

정규식은 다음 작업을 지원하지 않습니다.

* 하위 식 역참조 및 캡처
* 임의의 너비가 0인 어설션
* 서브루틴 참조 및 재귀 패턴
* 조건부 패턴
* 컨트롤 동사 역추적
* `\C` 단일 바이트 지시문
* `\R` 줄 바꿈 일치 지시문
* `\K` 일치 시작 초기화 지시문
* 설명선과 포함된 코드
* 원자성 그룹화 및 독점적 한정자

## <a name="next-steps"></a>다음 단계

Azure Front Door:

* Azure Front Door [규칙 엔진에](front-door-rules-engine.md) 대해 자세히 알아보기
* [첫 번째 규칙 엔진 을 구성하는](front-door-tutorial-rules-engine.md)방법을 알아봅니다. 
* [규칙 엔진 작업](front-door-rules-engine-actions.md)에 대해 자세히 알아봅니다.

Azure Front Door 표준/Premium:

* 표준/Premium [규칙 집합](standard-premium/concept-rule-set.md)Azure Front Door 대해 자세히 알아봅니다.
* [첫 번째 규칙 집합을 구성](standard-premium/how-to-configure-rule-set.md)하는 방법을 알아봅니다.
* [규칙 집합 작업](standard-premium/concept-rule-set-actions.md)에 대해 알아봅니다.
