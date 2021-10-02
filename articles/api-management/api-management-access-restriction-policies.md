---
title: Azure API Management 액세스 제한 정책 | Microsoft Docs
description: Azure API Management에 사용할 수 있는 액세스 제한 정책에 대해 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 08/20/2021
ms.author: danlep
ms.openlocfilehash: c924b4bc9ff0819c4dcf16b4d27becae76bf6eca
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129349582"
---
# <a name="api-management-access-restriction-policies"></a>API Management 액세스 제한 정책

이 문서에서는 다음 API Management 정책에 대한 참조를 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](./api-management-policies.md)을 참조하세요.

## <a name="access-restriction-policies"></a><a name="AccessRestrictionPolicies"></a> 액세스 제한 정책

-   [HTTP 헤더 확인](#CheckHTTPHeader) - HTTP 헤더의 존재 및/또는 값을 적용합니다.
-   [구독으로 호출 속도 제한](#LimitCallRate) - 구독을 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
-   [키로 호출 속도 제한](#LimitCallRateByKey) - 키를 기준으로 호출 속도를 제한하여 API 사용량 급증을 방지합니다.
-   [호출자 IP 제한](#RestrictCallerIPs) - 특정 IP 주소 및/또는 주소 범위의 호출을 필터링(허용/거부)합니다.
-   [구독으로 사용 할당량 설정](#SetUsageQuota) - 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
-   [키로 사용 할당량 설정](#SetUsageQuotaByKey) - 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.
-   [JWT 유효성 검사](#ValidateJWT) - 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT의 존재 및 유효성을 적용합니다.
-  [클라이언트 인증서 유효성 검사](#validate-client-certificate) - 클라이언트가 API Management 인스턴스에 제공하는 인증서가 지정된 유효성 검사 규칙 및 클레임과 일치하도록 적용합니다.

> [!TIP]
> 다양한 용도로 서로 다른 범위에서 액세스 제한 정책을 사용할 수 있습니다. 예를 들어 API 레벨에서 `validate-jwt` 정책을 적용하여 AAD 인증으로 전체 API를 보호하거나 보다 세부적인 제어가 가능하도록 API 작업 레벨에서 적용하고 `claims`를 사용할 수 있습니다.

## <a name="check-http-header"></a><a name="CheckHTTPHeader"></a> HTTP 헤더 확인

`check-header` 정책을 사용하여 요청에 HTTP 헤더가 지정되도록 합니다. 필요에 따라 헤더에 특정 값이 있는지, 허용되는 범위의 값인지를 확인할 수 있습니다. 확인에 실패하면 정책에서 요청 처리를 종료하고 정책에 지정된 HTTP 상태 코드 및 오류 메시지를 반환합니다.

### <a name="policy-statement"></a>정책 문

```xml
<check-header name="header name" failed-check-httpcode="code" failed-check-error-message="message" ignore-case="true">
    <value>Value1</value>
    <value>Value2</value>
</check-header>
```

### <a name="example"></a>예제

```xml
<check-header name="Authorization" failed-check-httpcode="401" failed-check-error-message="Not authorized" ignore-case="false">
    <value>f6dc69a089844cf6b2019bae6d36fac8</value>
</check-header>
```

### <a name="elements"></a>요소

| 이름         | Description                                                                                                                                   | 필수 |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| check-header | 루트 요소입니다.                                                                                                                                 | 예      |
| value        | 허용된 HTTP 헤더 값입니다. 여러 값 요소가 지정된 경우 값 중 하나와 일치하면 확인에 성공한 것으로 간주됩니다. | 예       |

### <a name="attributes"></a>특성

| 이름                       | Description                                                                                                                                                            | 필수 | 기본값 |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| failed-check-error-message | 헤더가 없거나 잘못된 값이 있는 경우 HTTP 응답 본문에 반환할 오류 메시지입니다. 이 메시지는 적절히 이스케이프된 특수 문자를 포함해야 합니다. | 예      | 해당 없음     |
| failed-check-httpcode      | 헤더가 없거나 잘못된 값이 있는 경우 반환할 HTTP 상태 코드입니다.                                                                                        | 예      | 해당 없음     |
| header-name                | 확인할 HTTP 헤더의 이름입니다.                                                                                                                                  | 예      | 해당 없음     |
| ignore-case                | True 또는 False로 설정할 수 있습니다. True로 설정되면 헤더 값을 허용 가능한 값 집합과 비교할 때 대소문자가 무시됩니다.                                    | 예      | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound, outbound

-   **정책 범위:** 모든 범위

## <a name="limit-call-rate-by-subscription"></a><a name="LimitCallRate"></a> 구독으로 호출 속도 제한

`rate-limit` 정책은 호출 속도를 지정된 기간당 지정된 숫자로 제한하여 구독 하나당 최대 API 사용을 차단합니다. 호출 속도를 초과할 경우 호출자는 `429 Too Many Requests` 응답 상태 코드를 수신합니다.

> [!IMPORTANT]
> 이 정책은 정책 문서당 한 번만 사용할 수 있습니다.
>
> 이 정책의 정책 특성에는 [정책 식](api-management-policy-expressions.md)을 사용할 수 없습니다.

> [!CAUTION]
> 제한 아키텍처의 분산 특성으로 인해 속도 제한은 완전히 정확하지 않습니다. 구성된 요청 수와 허용되는 실제 요청 수의 차이는 요청 볼륨 및 속도, 백 엔드 대기 시간 및 기타 요인에 따라 달라집니다.

> [!NOTE]
> 속도 제한과 할당량 간의 차이를 이해하려면 [속도 제한 및 할당량을 참조](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)하세요.

### <a name="policy-statement"></a>정책 문

```xml
<rate-limit calls="number" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds">
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" 
        retry-after-header-name="header name" 
        retry-after-variable-name="policy expression variable name"
        remaining-calls-header-name="header name"  
        remaining-calls-variable-name="policy expression variable name"
        total-calls-header-name="header name"/>
    </api>
</rate-limit>
```

### <a name="example"></a>예제

다음 예제에서 구독당 속도 제한은 90초당 20개 호출입니다. 각 정책 실행 후 해당 기간에 허용되는 나머지 호출은 변수 `remainingCallsPerSubscription`에 저장됩니다.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit calls="20" renewal-period="90" remaining-calls-variable-name="remainingCallsPerSubscription"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름       | Description                                                                                                                                                                                                                                                                                              | 필수 |
| ---------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| rate-limit | 루트 요소입니다.                                                                                                                                                                                                                                                                                            | 예      |
| api        | 제품 내에서 API에 호출 속도 제한을 적용하려면 해당 요소 중 하나 이상을 추가합니다. 제품 및 API 호출 속도 제한은 독립적으로 적용됩니다. API는 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다.                    | 아니요       |
| operation  | API 내에서 작업에 호출 속도 제한을 적용하려면 해당 요소 중 하나 이상을 추가합니다. 제품, API 및 작업 호출 속도 제한은 독립적으로 적용됩니다. 작업은 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다. | 예       |

### <a name="attributes"></a>특성

| 이름           | Description                                                                                           | 필수 | 기본값 |
| -------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| name           | 속도 제한을 적용할 API의 이름입니다.                                                | 예      | 해당 없음     |
| calls          | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다. | 예      | 해당 없음     |
| renewal-period | 허용되는 요청 수가 `calls`에 지정된 값을 초과하지 않아야 하는 슬라이딩 윈도우의 길이(초 단위)입니다. 허용되는 최대값: 300초.                                            | 예      | 해당 없음     |
| retry-after-header-name    | 값이 지정된 호출 속도가 초과된 후 권장되는 재시도 간격(초)인 응답 헤더의 이름입니다. |  아니요 | 해당 없음  |
| retry-after-variable-name    | 지정된 호출 속도를 초과한 후 권장되는 재시도 간격(초)을 저장하는 정책 식 변수의 이름입니다. |  아니요 | 해당 없음  |
| remaining-calls-header-name    | 각 정책 실행 후 값이 `renewal-period`에 지정된 시간 간격에 허용되는 나머지 호출 수인 응답 헤더의 이름입니다. |  아니요 | 해당 없음  |
| remaining-calls-variable-name    | 각 정책 실행 후 `renewal-period`에 지정된 시간 간격에 허용되는 나머지 호출 수를 저장하는 정책 식 변수의 이름입니다. |  아니요 | 해당 없음  |
| total-calls-header-name    | `calls`에 값이 지정된 응답 헤더의 이름입니다. |  아니요 | 해당 없음  |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** product, api, operation

## <a name="limit-call-rate-by-key"></a><a name="LimitCallRateByKey"></a> 키로 호출 속도 제한

> [!IMPORTANT]
> 이 기능은 API Management의 **Consumption** 계층에서 사용할 수 없습니다.

`rate-limit-by-key` 정책은 호출 속도를 지정된 기간당 지정된 숫자로 제한하여 키 하나당 최대 API 사용을 차단합니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다. 제한에 포함될 요청을 지정하기 위해 선택적 증분 조건을 추가할 수 있습니다. 이 호출 속도를 초과할 경우 호출자는 `429 Too Many Requests` 응답 상태 코드를 수신합니다.

이 정책에 대한 자세한 내용과 예제는 [Azure API Management로 고급 요청 제한](./api-management-sample-flexible-throttling.md)을 참조하세요.

> [!CAUTION]
> 제한 아키텍처의 분산 특성으로 인해 속도 제한은 완전히 정확하지 않습니다. 구성된 요청 수와 허용되는 실제 요청 수의 차이는 요청 볼륨 및 속도, 백 엔드 대기 시간 및 기타 요인에 따라 달라집니다.

> [!NOTE]
> 속도 제한과 할당량 간의 차이를 이해하려면 [속도 제한 및 할당량을 참조](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)하세요.

### <a name="policy-statement"></a>정책 문

```xml
<rate-limit-by-key calls="number"
                   renewal-period="seconds"
                   increment-condition="condition"
                   counter-key="key value" 
                   retry-after-header-name="header name" retry-after-variable-name="policy expression variable name"
                   remaining-calls-header-name="header name"  remaining-calls-variable-name="policy expression variable name"
                   total-calls-header-name="header name"/> 

```

### <a name="example"></a>예제

다음 예제에서 60초당 10회 호출인 속도 제한은 호출자 IP 주소를 키로 사용합니다. 각 정책 실행 후 해당 기간에 허용되는 나머지 호출은 변수 `remainingCallsPerIP`에 저장됩니다.

```xml
<policies>
    <inbound>
        <base />
        <rate-limit-by-key  calls="10"
              renewal-period="60"
              increment-condition="@(context.Response.StatusCode == 200)"
              counter-key="@(context.Request.IpAddress)"
              remaining-calls-variable-name="remainingCallsPerIP"/>
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름              | Description   | 필수 |
| ----------------- | ------------- | -------- |
| rate-limit-by-key | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 이름                | Description                                                                                           | 필수 | 기본값 |
| ------------------- | ----------------------------------------------------------------------------------------------------- | -------- | ------- |
| calls               | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다. 정책 식이 허용됩니다. | 예      | 해당 없음     |
| counter-key         | 속도 제한 정책에 사용할 키입니다.                                                             | 예      | 해당 없음     |
| increment-condition | 요청을 속도에 포함할지를 지정하는 부울 식입니다(`true`).        | 아니요       | 해당 없음     |
| renewal-period      | 허용되는 요청 수가 `calls`에 지정된 값을 초과하지 않아야 하는 슬라이딩 윈도우의 길이(초 단위)입니다. 정책 식이 허용됩니다. 허용되는 최대값: 300초.                 | 예      | 해당 없음     |
| retry-after-header-name    | 값이 지정된 호출 속도가 초과된 후 권장되는 재시도 간격(초)인 응답 헤더의 이름입니다. |  아니요 | 해당 없음  |
| retry-after-variable-name    | 지정된 호출 속도를 초과한 후 권장되는 재시도 간격(초)을 저장하는 정책 식 변수의 이름입니다. |  아니요 | 해당 없음  |
| remaining-calls-header-name    | 각 정책 실행 후 값이 `renewal-period`에 지정된 시간 간격에 허용되는 나머지 호출 수인 응답 헤더의 이름입니다. |  아니요 | 해당 없음  |
| remaining-calls-variable-name    | 각 정책 실행 후 `renewal-period`에 지정된 시간 간격에 허용되는 나머지 호출 수를 저장하는 정책 식 변수의 이름입니다. |  아니요 | 해당 없음  |
| total-calls-header-name    | `calls`에 값이 지정된 응답 헤더의 이름입니다. |  아니요 | 해당 없음  |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound

-   **정책 범위:** 모든 범위

## <a name="restrict-caller-ips"></a><a name="RestrictCallerIPs"></a> 호출자 IP 제한

`ip-filter` 정책은 특정 IP 주소 및/또는 주소 범위에서 호출을 필터링(허용/거부)합니다.

### <a name="policy-statement"></a>정책 문

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address" />
</ip-filter>
```

### <a name="example"></a>예제

다음 예제에서 정책은 단일 IP 주소 또는 지정된 IP 주소 범위에서 들어오는 요청만 허용합니다.

```xml
<ip-filter action="allow">
    <address>13.66.201.169</address>
    <address-range from="13.66.140.128" to="13.66.140.143" />
</ip-filter>
```

### <a name="elements"></a>요소

| 이름                                      | Description                                         | 필수                                                       |
| ----------------------------------------- | --------------------------------------------------- | -------------------------------------------------------------- |
| ip-filter                                 | 루트 요소입니다.                                       | 예                                                            |
| address                                   | 필터링할 단일 IP 주소를 지정합니다.   | 하나 이상의 `address` 또는 `address-range` 요소가 필요합니다. |
| address-range from="address" to="address" | 필터링할 IP 주소 범위를 지정합니다. | 하나 이상의 `address` 또는 `address-range` 요소가 필요합니다. |

### <a name="attributes"></a>특성

| 이름                                      | Description                                                                                 | 필수                                           | 기본값 |
| ----------------------------------------- | ------------------------------------------------------------------------------------------- | -------------------------------------------------- | ------- |
| address-range from="address" to="address" | 액세스를 허용 또는 거부할 IP 주소 범위                                        | `address-range` 요소가 사용될 때 필요합니다. | 해당 없음     |
| ip-filter action="allow &#124; forbid"    | 지정된 IP 주소 및 범위에 대해 호출을 허용해야 할지 여부를 지정합니다. | 예                                                | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위

## <a name="set-usage-quota-by-subscription"></a><a name="SetUsageQuota"></a> 구독으로 사용 할당량 설정

`quota` 정책은 구독을 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다.

> [!IMPORTANT]
> 이 정책은 정책 문서당 한 번만 사용할 수 있습니다.
>
> 이 정책의 정책 특성에는 [정책 식](api-management-policy-expressions.md)을 사용할 수 없습니다.

> [!NOTE]
> 속도 제한과 할당량 간의 차이를 이해하려면 [속도 제한 및 할당량을 참조](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)하세요.

### <a name="policy-statement"></a>정책 문

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="API name" id="API id" calls="number" renewal-period="seconds" />
        <operation name="operation name" id="operation id" calls="number" renewal-period="seconds" />
    </api>
</quota>
```

### <a name="example"></a>예제

```xml
<policies>
    <inbound>
        <base />
        <quota calls="10000" bandwidth="40000" renewal-period="3600" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름      | Description                                                                                                                                                                                                                                                                                  | 필수 |
| --------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| quota     | 루트 요소입니다.                                                                                                                                                                                                                                                                                | 예      |
| api       | 제품 내에서 API에 대한 호출 할당량을 적용하려면 해당 요소 중 하나 이상을 추가합니다. 제품 및 API 호출 할당량은 독립적으로 적용됩니다. API는 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다.                    | 아니요       |
| operation | API 내에서 작업에 대한 호출 할당량을 적용하려면 해당 요소 중 하나 이상을 추가합니다. 제품, API 및 작업 호출 할당량은 독립적으로 적용됩니다. 작업은 `name` 또는 `id`를 통해 참조할 수 있습니다. 두 특성이 모두 제공되면 `id`는 사용되지만 `name`은 무시됩니다. | 예      |

### <a name="attributes"></a>특성

| 이름           | Description                                                                                               | 필수                                                         | 기본값 |
| -------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| name           | 할당량이 적용되는 API 또는 작업의 이름입니다.                                             | 예                                                              | 해당 없음     |
| bandwidth      | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 킬로바이트 수입니다. | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| calls          | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다.     | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| renewal-period | 할당량이 재설정되는 초 단위의 기간입니다. `0`으로 설정되면 기간이 무한으로 설정됩니다. | 예                                                              | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** product

## <a name="set-usage-quota-by-key"></a><a name="SetUsageQuotaByKey"></a> 키로 사용 할당량 설정

> [!IMPORTANT]
> 이 기능은 API Management의 **Consumption** 계층에서 사용할 수 없습니다.

`quota-by-key` 정책은 키를 기준으로 갱신 가능 또는 수명 호출 볼륨 및/또는 대역폭 할당량을 적용할 수 있습니다. 키는 임의의 문자열 값을 포함할 수 있으며 일반적으로 정책 식을 사용하여 제공됩니다. 할당량에 포함될 요청을 지정하기 위해 선택적 증분 조건을 추가할 수 있습니다. 여러 정책이 동일한 키 값을 증가시키는 경우 요청당 한 번만 증가됩니다. 호출 속도를 초과할 경우 호출자는 `403 Forbidden` 응답 상태 코드를 수신합니다.

이 정책에 대한 자세한 내용과 예제는 [Azure API Management로 고급 요청 제한](./api-management-sample-flexible-throttling.md)을 참조하세요.

> [!NOTE]
> 속도 제한과 할당량 간의 차이를 이해하려면 [속도 제한 및 할당량을 참조](./api-management-sample-flexible-throttling.md#rate-limits-and-quotas)하세요.

### <a name="policy-statement"></a>정책 문

```xml
<quota-by-key calls="number"
              bandwidth="kilobytes"
              renewal-period="seconds"
              increment-condition="condition"
              counter-key="key value" />

```

### <a name="example"></a>예제

다음 예제에서 할당량은 호출자 IP 주소를 키로 사용합니다.

```xml
<policies>
    <inbound>
        <base />
        <quota-by-key calls="10000" bandwidth="40000" renewal-period="3600"
                      increment-condition="@(context.Response.StatusCode >= 200 && context.Response.StatusCode < 400)"
                      counter-key="@(context.Request.IpAddress)" />
    </inbound>
    <outbound>
        <base />
    </outbound>
</policies>
```

### <a name="elements"></a>요소

| 이름  | Description   | 필수 |
| ----- | ------------- | -------- |
| quota | 루트 요소입니다. | 예      |

### <a name="attributes"></a>특성

| 이름                | Description                                                                                               | 필수                                                         | 기본값 |
| ------------------- | --------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------- | ------- |
| bandwidth           | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 킬로바이트 수입니다. | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| calls               | `renewal-period`에 지정된 시간 간격 동안 허용된 전체 최대 호출 수입니다.     | `calls`, `bandwidth` 또는 둘 다 함께 지정해야 합니다. | 해당 없음     |
| counter-key         | 할당량 정책에 사용할 키입니다.                                                                      | 예                                                              | 해당 없음     |
| increment-condition | 요청을 할당량에 포함할지를 지정하는 부울 식입니다(`true`).             | 아니요                                                               | 해당 없음     |
| renewal-period      | 할당량이 재설정되는 초 단위의 기간입니다. `0`으로 설정되면 기간이 무한으로 설정됩니다.                                                   | 예                                                              | 해당 없음     |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위

## <a name="validate-jwt"></a><a name="ValidateJWT"></a> JWT 유효성 검사

`validate-jwt` 정책은 지정된 HTTP 헤더 또는 지정된 쿼리 매개 변수에서 추출된 JWT(JSON 웹 토큰)의 존재 및 유효성을 적용합니다.

> [!IMPORTANT]
> `validate-jwt` 정책에서는 `require-expiration-time` 특정이 지정되지 않고 `false`로 설정된 경우가 아니면 `exp` 등록 클레임이 JWT 토큰에 포함되어야 합니다.
> `validate-jwt` 정책은 HS256 및 RS256 서명 알고리즘을 지원합니다. HS256의 경우 키를 base64 인코딩 양식으로 정책 내에 인라인으로 제공해야 합니다. RS256의 경우 키는 공개 ID 구성 엔드포인트를 통하거나 업로드되어 퍼블릭 키 또는 퍼블릭 키의 모듈러스-지수 쌍을 포함하는 인증서의 ID를 제공하는 방식으로 제공됩니다.
> `validate-jwt` 정책은 A128CBC-HS256, A192CBC-HS384, A256CBC-HS512 암호화 알고리즘을 사용하여 대칭 키로 암호화된 토큰을 지원합니다.

### <a name="policy-statement"></a>정책 문

```xml
<validate-jwt
    header-name="name of http header containing the token (use query-parameter-name attribute if the token is passed in the URL)"
    failed-validation-httpcode="http status code to return on failure"
    failed-validation-error-message="error message to return on failure"
    token-value="expression returning JWT token as a string"
    require-expiration-time="true|false"
    require-scheme="scheme"
    require-signed-tokens="true|false"
    clock-skew="allowed clock skew in seconds"
    output-token-variable-name="name of a variable to receive a JWT object representing successfully validated token">
  <openid-config url="full URL of the configuration endpoint, e.g. https://login.constoso.com/openid-configuration" />
  <issuer-signing-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </issuer-signing-keys>
  <decryption-keys>
    <key>base64 encoded signing key</key>
    <!-- if there are multiple keys, then add additional key elements -->
  </decryption-keys>
  <audiences>
    <audience>audience string</audience>
    <!-- if there are multiple possible audiences, then add additional audience elements -->
  </audiences>
  <issuers>
    <issuer>issuer string</issuer>
    <!-- if there are multiple possible issuers, then add additional issuer elements -->
  </issuers>
  <required-claims>
    <claim name="name of the claim as it appears in the token" match="all|any" separator="separator character in a multi-valued claim">
      <value>claim value as it is expected to appear in the token</value>
      <!-- if there is more than one allowed values, then add additional value elements -->
    </claim>
    <!-- if there are multiple possible allowed values, then add additional value elements -->
  </required-claims>
</validate-jwt>

```

### <a name="examples"></a>예

#### <a name="simple-token-validation"></a>단순 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key>  <!-- signing key specified as a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="token-validation-with-rsa-certificate"></a>RSA 인증서를 사용하여 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer">
    <issuer-signing-keys>
        <key certificate-id="my-rsa-cert" />  <!-- signing key specified as certificate ID, enclosed in double-quotes -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>  <!-- audience is set to API Management host name -->
    </audiences>
    <issuers>
        <issuer>http://contoso.com/</issuer>
    </issuers>
</validate-jwt>
```

#### <a name="azure-active-directory-token-validation"></a>Azure Active Directory 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration" />
    <audiences>
        <audience>25eef6e4-c905-4a07-8eb4-0d08d5df8b3f</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="azure-active-directory-b2c-token-validation"></a>Azure Active Directory B2C 토큰 유효성 검사

```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/tfp/contoso.onmicrosoft.com/b2c_1_signin/v2.0/.well-known/openid-configuration" />
    <audiences>
        <audience>d313c4e4-de5f-4197-9470-e509a2f0b806</audience>
    </audiences>
    <required-claims>
        <claim name="id" match="all">
            <value>insert claim here</value>
        </claim>
    </required-claims>
</validate-jwt>
```

#### <a name="authorize-access-to-operations-based-on-token-claims"></a>토큰 클레임 기반 작업에 대한 액세스 권한 부여

이 예제에서는 [JWT 유효성 검사](api-management-access-restriction-policies.md#ValidateJWT) 정책을 사용하여 토큰 클레임 값 기반 작업에 대해 권한을 부여하는 방법을 보여 줍니다.

```xml
<validate-jwt header-name="Authorization" require-scheme="Bearer" output-token-variable-name="jwt">
    <issuer-signing-keys>
        <key>{{jwt-signing-key}}</key> <!-- signing key is stored in a named value -->
    </issuer-signing-keys>
    <audiences>
        <audience>@(context.Request.OriginalUrl.Host)</audience>
    </audiences>
    <issuers>
        <issuer>contoso.com</issuer>
    </issuers>
    <required-claims>
        <claim name="group" match="any">
            <value>finance</value>
            <value>logistics</value>
        </claim>
    </required-claims>
</validate-jwt>
<choose>
    <when condition="@(context.Request.Method == "POST" && !((Jwt)context.Variables["jwt"]).Claims["group"].Contains("finance"))">
        <return-response>
            <set-status code="403" reason="Forbidden" />
        </return-response>
    </when>
</choose>
```

### <a name="elements"></a>요소

| 요소             | Description                                                                                                                                                                                                                                                                                                                                           | 필수 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-jwt        | 루트 요소입니다.                                                                                                                                                                                                                                                                                                                                         | 예      |
| audiences           | 토큰에 제공할 수 있는 허용 가능한 대상 그룹 클레임 목록을 포함합니다. 여러 대상 그룹 값이 있는 경우 각 값은 모든 값이 소진(이 경우 유효성 검사 실패)되거나 한 값이 성공할 때까지 시도됩니다. 한 명 이상의 대상 그룹을 지정해야 합니다.                                                                     | 아니요       |
| issuer-signing-keys | 서명된 토큰의 유효성을 검사하는 데 사용되는 Base64 인코딩 보안 키의 목록입니다. 보안 키가 여러 개 있는 경우 각 키는 모든 키가 소진(이 경우 유효성 검사 실패)되거나 한 개 키가 성공할 때까지 시도됩니다(토큰 롤오버에 유용함). 키 요소에는 `kid` 클레임에 대한 일치에 사용된 `id` 특성(선택적)이 포함됩니다. <br/><br/>아니면 다음을 사용하여 발급자 서명 키를 공급합니다.<br/><br/> - API Management에 [업로드](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add)된 인증서 엔터티의 식별자를 지정하는 `<key certificate-id="mycertificate" />` 형식의 `certificate-id`<br/>- base64url 인코딩된 형식으로 RSA 매개 변수를 지정하는 `<key n="<modulus>" e="<exponent>" />` 형식의 RSA 모듈러스 `n`와 지수 `e` 쌍               | 아니요       |
| decryption-keys     | 토큰의 암호를 해독하는 데 사용되는 Base64로 인코딩된 키의 목록입니다. 여러 개의 보안 키가 있는 경우 모든 키가 사용되거나(유효성 검사가 실패한 경우) 키가 성공할 때까지 각 키를 시도합니다. 키 요소에는 `kid` 클레임에 대한 일치에 사용된 `id` 특성(선택적)이 포함됩니다.<br/><br/>아니면 다음을 사용하여 암호 해독 키를 공급합니다.<br/><br/> - API Management에 [업로드](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-certificate-entity#Add)된 인증서 엔터티의 식별자를 지정하는 `<key certificate-id="mycertificate" />` 형식의 `certificate-id`                                                 | 아니요       |
| issuers             | 토큰을 발행한 허용 가능한 보안 주체의 목록입니다. 여러 발급자 값이 있는 경우 각 값은 모든 값이 소진(이 경우 유효성 검사 실패)되거나 한 값이 성공할 때까지 시도됩니다.                                                                                                                                         | 아니요       |
| openid-config       | 서명 키 및 발급자를 획득할 수 있는 준수 Open ID 구성 엔드포인트를 지정하는 데 사용됩니다.                                                                                                                                                                                                                        | 아니요       |
| required-claims     | 유효성을 고려할 토큰에 있을 것으로 예상되는 클레임 목록을 포함합니다. `match` 특성이 `all`로 설정되면 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 합니다. `match` 특성이 `any`로 설정되면 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 합니다. | 예       |

### <a name="attributes"></a>특성

| 이름                            | Description                                                                                                                                                                                                                                                                                                                                                                                                                                            | 필수                                                                         | 기본값                                                                           |
| ------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | -------------------------------------------------------------------------------- | --------------------------------------------------------------------------------- |
| clock-skew                      | Timespan입니다. 토큰 발급자와 API Management 인스턴스의 시스템 시계 간 최대 예상 시간 차이를 지정하는 데 사용합니다.                                                                                                                                                                                                                                                                                                               | 아니요                                                                               | 0초                                                                         |
| failed-validation-error-message | JWT가 유효성 검사를 통과하지 못한 경우 HTTP 응답 본문에 반환할 오류 메시지입니다. 이 메시지는 적절히 이스케이프된 특수 문자를 포함해야 합니다.                                                                                                                                                                                                                                                                                                 | 아니요                                                                               | 기본 오류 메시지는 유효성 검사 문제에 따라 달라집니다(예: "JWT not present(JWT 없음)"). |
| failed-validation-httpcode      | JWT가 유효성 검사를 통과하지 못한 경우 반환할 HTTP 상태 코드입니다.                                                                                                                                                                                                                                                                                                                                                                                         | 아니요                                                                               | 401                                                                               |
| header-name                     | 토큰을 보유하는 HTTP 헤더의 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                         | `header-name`, `query-parameter-name` 또는 `token-value` 중 하나를 지정해야 합니다. | 해당 없음                                                                               |
| query-parameter-name            | 토큰을 보유하는 쿼리 매개 변수의 이름입니다.                                                                                                                                                                                                                                                                                                                                                                                                     | `header-name`, `query-parameter-name` 또는 `token-value` 중 하나를 지정해야 합니다. | 해당 없음                                                                               |
| token-value                     | JWT 토큰이 포함된 문자열을 반환하는 식입니다. 토큰 값의 일부로 `Bearer `를 반환할 수 없습니다.                                                                                                                                                                                                                                                                                                                                           | `header-name`, `query-parameter-name` 또는 `token-value` 중 하나를 지정해야 합니다. | 해당 없음                                                                               |
| id                              | `key` 요소에 있는 `id` 특성을 통해 토큰(있는 경우)에 있는 `kid` 클레임과 일치시킬 문자열을 지정하여 서명 유효성 검사에 사용할 적절한 키를 확인할 수 있습니다.                                                                                                                                                                                                                                           | 아니요                                                                               | 해당 없음                                                                               |
| match                           | `claim` 요소에 있는 `match` 특성에 따라 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 하는지가 지정됩니다. 가능한 값은 다음과 같습니다.<br /><br /> - `all` - 유효성 검사 성공을 위해 정책에 있는 모든 클레임 값이 토큰에 표시되어야 합니다.<br /><br /> - `any` - 유효성 검사 성공을 위해 하나 이상의 클레임 값이 토큰에 표시되어야 합니다.                                                       | 아니요                                                                               | 모두                                                                               |
| require-expiration-time         | Boolean입니다. 토큰에 만료 클레임이 필요한지를 지정합니다.                                                                                                                                                                                                                                                                                                                                                                               | 아니요                                                                               | true                                                                              |
| require-scheme                  | 토큰 스키마 이름(예: ‘Bearer’)입니다. 이 특성이 설치되면 정책은 지정된 스키마가 권한 부여 헤더 값에 있는지를 확인합니다.                                                                                                                                                                                                                                                                                    | 아니요                                                                               | 해당 없음                                                                               |
| require-signed-tokens           | Boolean입니다. 토큰에 서명이 필요한지를 지정합니다.                                                                                                                                                                                                                                                                                                                                                                                           | 아니요                                                                               | true                                                                              |
| 구분 기호                       | 문자열입니다. 다중 값 클레임에서 값 집합을 추출하는 데 사용된 구분 기호(예: “,”)를 지정합니다.                                                                                                                                                                                                                                                                                                                                          | 아니요                                                                               | 해당 없음                                                                               |
| url                             | Open ID 구성 메타데이터를 가져올 수 있는 Open ID 구성 엔드포인트 URL입니다. 응답은 URL `https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderMetadata`에서 정의된 사양을 따라야 합니다. Azure Active Directory의 경우 다음 URL을 사용합니다. `https://login.microsoftonline.com/{tenant-name}/.well-known/openid-configuration` 여기서 사용자의 디렉터리 테넌트 이름을 대체합니다(예: `contoso.onmicrosoft.com`). | 예                                                                              | 해당 없음                                                                               |
| output-token-variable-name      | 문자열입니다. 토큰 유효성 검사에 성공 시 유형 [`Jwt`](api-management-policy-expressions.md)의 개체로 토큰 값을 수신하는 컨텍스트 변수의 이름입니다.                                                                                                                                                                                                                                                                                     | 아니요                                                                               | 해당 없음                                                                               |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위


## <a name="validate-client-certificate"></a>클라이언트 인증서 유효성 검사

`validate-client-certificate` 정책을 사용하여 클라이언트가 API Management 인스턴스에 제공한 인증서가 하나 이상의 인증서 ID에 대해 지정된 유효성 검사 규칙 및 클레임(예: 주체 또는 발급자)과 일치하도록 적용합니다.

유효한 것으로 간주하려면 클라이언트 인증서가 최상위 요소의 특성에 의해 정의된 모든 유효성 검사 규칙과 일치하고 정의된 ID 중 하나 이상에 대해 정의된 모든 클레임과 일치해야 합니다. 

이 정책을 사용하여 원하는 속성에 대해 들어오는 인증서 속성을 확인합니다. 또한 이 정책을 사용하여 다음과 같은 경우 클라이언트 인증서의 기본 유효성 검사를 재정의합니다.

* 관리형 게이트웨이에 대한 클라이언트 요청의 유효성을 검사하기 위해 사용자 지정 CA 인증서를 업로드한 경우
* 자체 관리형 게이트웨이에 대한 클라이언트 요청의 유효성을 검사하도록 사용자 지정 인증 기관을 구성한 경우

사용자 지정 CA 인증서 및 인증 기관에 대한 자세한 내용은 [Azure API Management 사용자 지정 CA 인증서를 추가하는 방법](api-management-howto-ca-certificates.md)을 참조하세요. 

### <a name="policy-statement"></a>정책 문

```xml
<validate-client-certificate 
    validate-revocation="true|false"
    validate-trust="true|false" 
    validate-not-before="true|false" 
    validate-not-after="true|false" 
    ignore-error="true|false">
    <identities>
        <identity 
            thumbprint="certificate thumbprint"
            serial-number="certificate serial number"
            common-name="certificate common name"
            subject="certificate subject string"
            dns-name="certificate DNS name"
            issuer-subject="certificate issuer"
            issuer-thumbprint="certificate issuer thumbprint"
            issuer-certificate-id="certificate identifier" />
    </identities>
</validate-client-certificate> 
```

### <a name="example"></a>예제

다음 예제에서는 정책의 기본 유효성 검사 규칙과 일치하도록 클라이언트 인증서의 유효성을 검사하고 주체 및 발급자 이름이 지정된 값과 일치하는지 확인합니다.

```xml
<validate-client-certificate 
    validate-revocation="true" 
    validate-trust="true" 
    validate-not-before="true" 
    validate-not-after="true" 
    ignore-error="false">
    <identities>
        <identity
            subject="C=US, ST=Illinois, L=Chicago, O=Contoso Corp., CN=*.contoso.com"
            issuer-subject="C=BE, O=FabrikamSign nv-sa, OU=Root CA, CN=FabrikamSign Root CA" />
    </identities>
</validate-client-certificate> 
```

### <a name="elements"></a>요소

| 요소             | Description                                  | 필수 |
| ------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-client-certificate        | 루트 요소입니다.      | 예      |
|   ID      |  클라이언트 인증서에 정의된 클레임이 있는 ID 목록을 포함합니다.       |    예        |

### <a name="attributes"></a>특성

| 이름                            | Description      | 필수 |  기본값    |
| ------------------------------- | -----------------| -------- | ----------- |
| validate-revocation  | Boolean입니다. 온라인 해지 목록에 대해 인증서의 유효성을 검사할지 여부를 지정합니다.  | no   | True  |
| validate-trust | Boolean입니다. 신뢰할 수 있는 CA에 대해 체인을 성공적으로 빌드할 수 없는 경우 유효성 검사가 실패해야 하는지를 지정합니다. | no | True |
| validate-not-before | Boolean입니다. 현재 시간에 대해 값의 유효성을 검사합니다. | no | True |
| validate-not-after  | Boolean입니다. 현재 시간에 대해 값의 유효성을 검사합니다. | no | True|
| ignore-error  | Boolean입니다. 정책이 다음 처리기로 진행하거나 유효성 검사 실패 시 오류 발생 시로 넘어갈지 여부를 지정합니다. | no | 거짓 |
| identity | 문자열입니다. 인증서를 유효하게 만드는 인증서 클레임 값의 조합입니다. | 예 | 해당 없음 |
| thumbprint | 인증서 지문입니다. | no | 해당 없음 |
| serial-number | 인증서 일련 번호입니다. | no | 해당 없음 |
| common-name | 인증서 일반 이름(주체 문자열의 일부)입니다. | no | 해당 없음 |
| subject | 주체 문자열입니다. 고유 이름 형식을 따라야 합니다. | no | 해당 없음 |
| dns-name | 주체 대체 이름 클레임 내 dnsName 항목의 값입니다. | no | 해당 없음 |
| issuer-subject | 발급자의 제목입니다. 고유 이름 형식을 따라야 합니다. | no | 해당 없음 |
| issuer-thumbprint | 발급자 지문입니다. | no | 해당 없음 |
| issuer-certificate-id | 발급자의 공개 키를 나타내는 기존 인증서 엔터티의 식별자입니다. 다른 발급자 특성과 함께 사용할 수 없습니다.  | no | 해당 없음 |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

-   **정책 섹션:** inbound
-   **정책 범위:** 모든 범위

## <a name="next-steps"></a>다음 단계

정책으로 작업하는 방법에 대한 자세한 내용은 다음을 참조하세요.

-   [API Management의 정책](api-management-howto-policies.md)
-   [API 변환](transform-api.md)
-   [정책 참조](./api-management-policies.md)(정책 문 및 해당 설정에 대한 전체 목록)
-   [정책 샘플](./policy-reference.md)
