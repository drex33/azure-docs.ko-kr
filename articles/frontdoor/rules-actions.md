---
title: Azure Front Door 규칙 작업 구성
description: 이 문서에서는 Azure Front Door 규칙으로 수행할 수 있는 다양한 작업 목록을 제공합니다.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.date: 11/10/2021
ms.author: yuajia
ms.openlocfilehash: c072cf341049e5e2fc8631fdb628f04bdf719b97
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352739"
---
# <a name="azure-front-door-rules-actions"></a>Azure Front Door 규칙 작업

Azure Front Door 규칙 [엔진](front-door-rules-engine.md) 및 Azure Front Door 표준/Premium [규칙 집합은](standard-premium/concept-rule-set.md) 일치 조건과 작업의 조합이 있는 규칙으로 구성됩니다. 이 문서에서는 Azure Front Door 규칙 엔진 또는 규칙 집합에서 사용할 수 있는 형식 작업에 대한 자세한 설명을 제공합니다. 해당 작업은 일치 조건이 식별하는 요청 형식에 적용되는 동작을 정의합니다. 규칙은 최대 5개의 작업을 포함할 수 있습니다.

> [!IMPORTANT]
> Azure Front Door 표준/프리미엄(미리 보기)은 현재 공개 미리 보기로 제공됩니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

다음은 Azure Front Door 규칙 집합에서 사용할 수 있는 작업입니다.  

## <a name="cache-expiration"></a>캐시 만료

**캐시 만료** 작업을 수행하여 규칙 일치 조건에서 지정하는 요청에 대한 엔드포인트의 TTL(time to live) 값을 덮어쓸 수 있습니다.

> [!NOTE]
> 원본에서 `no-cache`, `private` 또는 `no-store` 값이 있는 `Cache-Control` 헤더를 사용하여 특정 응답을 캐시하지 않도록 지정할 수 있습니다. 이 상황에서는 Front Door가 콘텐츠를 캐시하지 않으므로 이 작업은 어떤 영향도 주지 않습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-------|------------------|
| 캐시 동작 | <ul><li>**캐시 바이패스:** 콘텐츠가 캐시되지 않습니다. ARM 템플릿에서 `cacheBehavior` 속성을 `BypassCache`로 설정합니다.</li><li>**재정의:** 원본에서 반환된 TTL 값을 작업에 지정된 값으로 덮어씁니다. 이 동작은 응답이 캐시 가능한 경우에만 적용됩니다. ARM 템플릿에서 `cacheBehavior` 속성을 `Override`로 설정합니다.</li><li>**누락된 경우 설정:** 원본에서 TTL 값이 반환되지 않으면 규칙 집합은 TTL을 작업에 지정된 값으로 설정합니다. 이 동작은 응답이 캐시 가능한 경우에만 적용됩니다. ARM 템플릿에서 `cacheBehavior` 속성을 `SetIfMissing`으로 설정합니다.</li></ul> |
| 캐시 기간 | _캐시 동작_ 이 `Override` 또는 `Set if missing`로 설정된 경우 이러한 필드는 사용할 캐시 기간을 지정해야 합니다. 최대 기간은 366일입니다.<ul><li>Azure Portal: 일, 시, 분 및 초를 지정합니다.</li><li>ARM 템플릿: `d.hh:mm:ss` 형식으로 기간을 지정합니다. |

### <a name="example"></a>예제

이 예제에서는 캐시 기간을 아직 지정하지 않은 일치 요청에 대해 캐시 만료를 6시간으로 재정의합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/cache-expiration.png" alt-text="캐시 만료 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheExpiration",
  "parameters": {
    "cacheBehavior": "SetIfMissing",
    "cacheType": "All",
    "cacheDuration": "0.06:00:00",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheExpiration'
  parameters: {
    cacheBehavior: 'SetIfMissing'
    cacheType: All
    cacheDuration: '0.06:00:00'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheExpirationActionParameters'
  }
}
```

---

## <a name="cache-key-query-string"></a>캐시 키 쿼리 문자열

**캐시 키 쿼리 문자열** 작업을 수행하여 쿼리 문자열에 따라 캐시 키를 수정할 수 있습니다. 캐시 키는 Front Door가 캐시에 대해 고유한 요청을 식별하는 방법입니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-------|------------------|
| 동작 | <ul><li>**포함:** 매개 변수에 지정된 쿼리 문자열은 캐시 키가 생성되면 포함됩니다. ARM 템플릿에서 `queryStringBehavior` 속성을 `Include`로 설정합니다.</li><li>**모든 고유 URL 캐시:** 각 고유 URL에는 자체 캐시 키가 있습니다. ARM 템플릿에서 `IncludeAll`의 `queryStringBehavior`를 사용합니다.</li><li>**제외:** 캐시 키가 생성되면 매개 변수에 지정된 쿼리 문자열이 제외됩니다. ARM 템플릿에서 `queryStringBehavior` 속성을 `Exclude`로 설정합니다.</li><li>**쿼리 문자열 무시:** 캐시 키가 생성되면 쿼리 문자열이 고려되지 않습니다. ARM 템플릿에서 `queryStringBehavior` 속성을 `ExcludeAll`로 설정합니다.</li></ul>  |
| 매개 변수 | 쉼표로 구분된 쿼리 문자열 매개 변수 이름 목록입니다. |

### <a name="example"></a>예제

이 예제에서는 `customerId`라는 쿼리 문자열 매개 변수를 포함하도록 캐시 키를 수정합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/cache-key-query-string.png" alt-text="캐시 키 쿼리 문자열 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "CacheKeyQueryString",
  "parameters": {
    "queryStringBehavior": "Include",
    "queryParameters": "customerId",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'CacheKeyQueryString'
  parameters: {
    queryStringBehavior: 'Include'
    queryParameters: 'customerId'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleCacheKeyQueryStringBehaviorActionParameters'
  }
}
```

---

## <a name="modify-request-header"></a>요청 헤더 수정

**요청 헤더 수정** 작업을 수행하여 요청의 헤더를 원본으로 보낼 때 이를 수정할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-------|------------------|
| 연산자 | <ul><li>**추가:** 지정된 헤더는 지정된 값을 사용하여 요청에 추가됩니다. 헤더가 이미 있는 경우 문자열 연결을 사용하여 기존 헤더 값에 값이 추가됩니다. 구분 기호는 추가되지 않습니다. ARM 템플릿에서 `Append`의 `headerAction`을 사용합니다.</li><li>**덮어쓰기:** 지정된 헤더는 지정된 값을 사용하여 요청에 추가됩니다. 헤더가 이미 있으면 지정한 값이 기존 값을 덮어씁니다. ARM 템플릿에서 `Overwrite`의 `headerAction`을 사용합니다.</li><li>**삭제:** 규칙에 지정된 헤더가 있으면 요청에서 헤더가 삭제됩니다. ARM 템플릿에서 `Delete`의 `headerAction`을 사용합니다.</li></ul> |
| 헤더 이름 | 수정할 헤더의 이름입니다. |
| 헤더 값 | 추가하거나 덮어쓸 값입니다. |

### <a name="example"></a>예제

이 예제에서는 요청 헤더에 `AdditionalValue` 값을 `MyRequestHeader` 추가합니다. 원본 집합에서 응답 헤더를 `ValueSetByClient` 값으로 설정한 경우에는 이 작업이 적용된 후 요청 헤더에 `ValueSetByClientAdditionalValue` 값이 포함됩니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/modify-request-header.png" alt-text="요청 헤더 수정 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyRequestHeader",
  "parameters": {
    "headerAction": "Append",
    "headerName": "MyRequestHeader",
    "value": "AdditionalValue",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyRequestHeader'
  parameters: {
    headerAction: 'Append'
    headerName: 'MyRequestHeader'
    value: 'AdditionalValue'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="modify-response-header"></a>응답 헤더 수정

**응답 헤더 수정** 작업을 수행하여 응답에 있는 헤더를 클라이언트에 반환하기 전에 수정할 수 있습니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|-------|------------------|
| 연산자 | <ul><li>**추가:** 지정된 헤더는 지정된 값을 사용하여 응답에 추가됩니다. 헤더가 이미 있는 경우 문자열 연결을 사용하여 기존 헤더 값에 값이 추가됩니다. 구분 기호는 추가되지 않습니다. ARM 템플릿에서 `Append`의 `headerAction`을 사용합니다.</li><li>**덮어쓰기:** 지정된 헤더는 지정된 값을 사용하여 응답에 추가됩니다. 헤더가 이미 있으면 지정한 값이 기존 값을 덮어씁니다. ARM 템플릿에서 `Overwrite`의 `headerAction`을 사용합니다.</li><li>**삭제:** 규칙에 지정된 헤더가 있으면 응답에서 헤더가 삭제됩니다.  ARM 템플릿에서 `Delete`의 `headerAction`을 사용합니다.</li></ul> |
| 헤더 이름 | 수정할 헤더의 이름입니다. |
| 헤더 값 | 추가하거나 덮어쓸 값입니다. |

### <a name="example"></a>예제

이 예제에서는 응답이 클라이언트에 반환되기 전에 응답에서 이름이 `X-Powered-By`인 헤더를 삭제합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/modify-response-header.png" alt-text="응답 헤더 수정 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "ModifyResponseHeader",
  "parameters": {
    "headerAction": "Delete",
    "headerName": "X-Powered-By",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'ModifyResponseHeader'
  parameters: {
    headerAction: 'Delete'
    headerName: 'X-Powered-By'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleHeaderActionParameters'
  }
}
```

---

## <a name="url-redirect"></a>URL 리디렉션

클라이언트를 새 URL로 리디렉션하려면 **URL 리디렉션** 작업을 수행합니다. 그러면 클라이언트가 Front Door의 리디렉션 응답으로 전송됩니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|----------|------------------|
| 리디렉션 유형 | 요청자에게 반환할 응답 유형입니다. <ul><li>Azure Portal: 찾음(302), 이동됨(301), 임시 리디렉션(307), 영구 리디렉션(308)</li><li>ARM 템플릿: `Found`, `Moved`, `TemporaryRedirect`, `PermanentRedirect`</li></ul> |
| 리디렉션 프로토콜 | <ul><li>Azure Portal: `Match Request`, `HTTP`, `HTTPS`</li><li>ARM 템플릿: `MatchRequest`, `Http`, `Https`</li></ul> |
| 대상 호스트 | 요청을 리디렉션할 호스트 이름입니다. 들어오는 호스트를 유지하려면 비워 둡니다. |
| 대상 경로 | 리디렉션에 사용할 경로입니다. 앞쪽 `/`를 포함합니다. 들어오는 경로를 유지하려면 비워 둡니다. |
| 쿼리 문자열 | 리디렉션에 사용되는 쿼리 문자열입니다. 앞쪽 `?`를 포함하지 마세요. 들어오는 쿼리 문자열을 유지하려면 비워 둡니다. |
| 대상 조각 | 리디렉션에 사용할 조각입니다. 들어오는 조각을 유지하려면 비워 둡니다. |

### <a name="example"></a>예제

이 예제에서는 조각을 유지하면서 요청을 `https://contoso.com/exampleredirection?clientIp={client_ip}`로 리디렉션합니다. HTTP 임시 리디렉션(307)이 사용됩니다. 클라이언트의 IP 주소는 `client_ip` [서버 변수](#server-variables)를 사용하여 URL 내에서 `{client_ip}` 토큰 대신 사용됩니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/url-redirect.png" alt-text="URL 리디렉션 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRedirect",
  "parameters": {
    "redirectType": "TemporaryRedirect",
    "destinationProtocol": "Https",
    "customHostname": "contoso.com",
    "customPath": "/exampleredirection",
    "customQueryString": "clientIp={client_ip}",
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRedirect'
  parameters: {
    redirectType: 'TemporaryRedirect'
    destinationProtocol: 'Https'
    customHostname: 'contoso.com'
    customPath: '/exampleredirection'
    customQueryString: 'clientIp={client_ip}'
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRedirectActionParameters'
  }
}
```

---

## <a name="url-rewrite"></a>URL 다시 쓰기

원본으로 라우팅하는 요청의 경로를 다시 쓰려면 **URL 다시 쓰기** 작업을 수행합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|----------|------------------|
| 원본 패턴 | 바꿀 URL 경로에 원본 패턴을 정의합니다. 현재 원본 패턴은 접두사 기반 일치를 사용합니다. 모든 URL 경로를 일치시키려면 원본 패턴 값으로 슬래시(`/`)를 사용합니다. |
| 대상 | 다시 쓰기에 사용할 대상 경로를 정의합니다. 대상 경로는 원본 패턴을 덮어씁니다. |
| 불일치한 경로 유지 | _예_ 로 설정된 경우 원본 패턴 뒤의 나머지 경로가 새 대상 경로에 추가됩니다. |

### <a name="example"></a>예제

이 예제에서는 모든 요청을 `/redirection` 경로에 다시 쓰고 나머지 경로는 유지하지 않습니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/url-rewrite.png" alt-text="URL 다시 쓰기 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "UrlRewrite",
  "parameters": {
    "sourcePattern": "/",
    "destination": "/redirection",
    "preserveUnmatchedPath": false,
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'UrlRewrite'
  parameters: {
    sourcePattern: '/'
    destination: '/redirection'
    preserveUnmatchedPath: false
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleUrlRewriteActionParameters'
  }
}
```

---

## <a name="origin-group-override"></a> 원본 그룹 재정의

**원본 그룹 재정의** 작업을 사용하여 요청이 라우팅되어야 하는 원본 그룹을 변경합니다.

### <a name="properties"></a>속성

| 속성 | 지원되는 값 |
|----------|------------------|
| 원본 그룹 | 요청을 라우팅해야 하는 원본 그룹입니다. 그러면 Front Door 엔드포인트 경로에 지정된 구성이 재정의됩니다. |

### <a name="example"></a>예제

이 예에서는 Front Door 엔드포인트 경로의 구성에 관계없이 일치하는 모든 요청을 `SecondOriginGroup`이라는 원본 그룹으로 라우팅합니다.

# <a name="portal"></a>[포털](#tab/portal)

:::image type="content" source="./media/rules-actions/origin-group-override.png" alt-text="원본 그룹 재정의 작업을 보여 주는 포털 스크린샷":::

# <a name="json"></a>[JSON](#tab/json)

```json
{
  "name": "OriginGroupOverride",
  "parameters": {
    "originGroup": {
      "id": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup"
    },
    "@odata.type": "#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters"
  }
}
```

# <a name="bicep"></a>[Bicep](#tab/bicep)

```bicep
{
  name: 'OriginGroupOverride'
  parameters: {
    originGroup: {
      id: '/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.Cdn/profiles/<profile-name>/originGroups/SecondOriginGroup'
    }
    '@odata.type': '#Microsoft.Azure.Cdn.Models.DeliveryRuleOriginGroupOverrideActionParameters'
  }
}
```

---

## <a name="server-variables"></a>서버 변수

규칙 집합 서버 변수는 요청에 관한 구조화된 정보에 대한 액세스 권한을 제공합니다. 서버 변수를 사용하여 요청/응답 헤더 또는 URL 다시 쓰기 경로/쿼리 문자열을 동적으로 변경할 수 있습니다(예: 새 페이지를 로드하거나 양식을 게시하는 경우).

### <a name="supported-variables"></a>지원되는 변수

| 변수 이름    | Description                                                                                                                                                                                                                                                                               |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `socket_ip`      | Azure Front Door 에지에 대한 직접 연결의 IP 주소입니다. 클라이언트에서 HTTP 프록시 또는 부하 분산 장치를 사용하여 요청을 보낸 경우 `socket_ip`의 값은 프록시 또는 부하 분산 장치의 IP 주소입니다.                                                                      |
| `client_ip`      | 원본에서 요청한 클라이언트의 IP 주소입니다. 요청에 `X-Forwarded-For` 헤더가 있으면 헤더에서 클라이언트 IP 주소가 선택됩니다.                                                                                                               |
| `client_port`    | 요청한 클라이언트의 IP 포트입니다.                                                                                                                                                                                                                                          |
| `hostname`       | 클라이언트의 요청에 있는 호스트 이름입니다.                                                                                                                                                                                                                                             |
| `geo_country`    | 해당 국가/지역 코드를 통해 요청자의 원산 국가/지역을 나타냅니다.                                                                                                                                                                                                       |
| `http_method`    | URL을 요청하는 데 사용되는 메서드(예: `GET` 또는 `POST`)입니다.                                                                                                                                                                                                                         |
| `http_version`   | 요청 프로토콜입니다. 일반적으로, `HTTP/1.0` `HTTP/1.1` 또는 `HTTP/2.0`입니다.                                                                                                                                                                                                                      |
| `query_string`   | 요청된 URL에서 "?" 뒤에 오는 변수/값 쌍 목록입니다.<br />예를 들어 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 요청에서 `query_string` 값은 `id=123&title=fabrikam`이 됩니다.                                                      |
| `request_scheme` | 요청 체계는 `http` 또는 `https`입니다.                                                                                                                                                                                                                                                    |
| `request_uri`    | 전체 원본 요청 URI(인수 포함)입니다.<br />예를 들어 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 요청에서 `request_uri` 값은 `/article.aspx?id=123&title=fabrikam`이 됩니다.                                                                     |
| `ssl_protocol`   | 설정된 TLS 연결의 프로토콜입니다.                                                                                                                                                                                                                                            |
| `server_port`    | 요청을 수락한 서버의 포트입니다.                                                                                                                                                                                                                                           |
| `url_path`       | 웹 클라이언트에서 액세스할 호스트의 특정 리소스를 식별합니다. 이는 인수가 없는 요청 URI의 일부입니다.<br />예를 들어 `http://contoso.com:8080/article.aspx?id=123&title=fabrikam` 요청에서 `uri_path` 값은 `/article.aspx`가 됩니다. |

### <a name="server-variable-format"></a>서버 변수 형식    

서버 변수는 다음 형식을 사용하여 지정할 수 있습니다.

* `{variable}`: 전체 서버 변수를 포함합니다. 예를 들어 클라이언트 IP 주소가 `111.222.333.444`이면 `{client_ip}` 토큰이 `111.222.333.444`로 평가됩니다.
* `{variable:offset}`: 변수 끝 부분까지 특정 오프셋 뒤에 서버 변수를 포함합니다. 오프셋은 0부터 시작합니다. 예를 들어 클라이언트 IP 주소가 `111.222.333.444`이면 `{client_ip:3}` 토큰이 `.222.333.444`로 평가됩니다.
* `{variable:offset:length}`: 지정된 길이까지 특정 오프셋 뒤에 서버 변수를 포함합니다. 오프셋은 0부터 시작합니다. 예를 들어 클라이언트 IP 주소가 `111.222.333.444`이면 `{client_ip:4:3}` 토큰이 `222`로 평가됩니다.

### <a name="supported-actions"></a>지원되는 작업

서버 변수는 다음 작업에서 지원됩니다.

* 캐시 키 쿼리 문자열
* 요청 헤더 수정
* 응답 헤더 수정
* URL 리디렉션
* URL 다시 쓰기

## <a name="next-steps"></a>다음 단계

* [Azure Front Door 표준/프리미엄 규칙 집합](standard-premium/concept-rule-set.md)에 대해 자세히 알아보세요.
* [Azure Front Door 규칙 엔진에](front-door-rules-engine.md)대해 자세히 알아봅니다.
* [규칙 집합 일치 조건](rules-match-conditions.md)에 대해 자세히 알아보세요.
