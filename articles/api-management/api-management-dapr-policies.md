---
title: Azure API Management Dapr 통합 정책 | Microsoft Docs
description: Dapr 마이크로 서비스 확장과 상호 작용하기 위한 Azure API Management 정책에 대해 알아봅니다.
author: dlepow
ms.author: danlep
ms.date: 02/18/2021
ms.topic: article
ms.service: api-management
ms.openlocfilehash: 4251d87727fafbb136b29eb2479ac0a70f2d027d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647559"
---
# <a name="api-management-dapr-integration-policies"></a>API Management Dapr 통합 정책

이 항목에서는 Dapr 통합 API Management 정책에 대한 참조를 제공합니다. Dapr는 모든 언어나 프레임워크를 사용하여 상태 비저장 및 상태 저장 마이크로 서비스 기반 애플리케이션을 빌드하기 위한 이식 가능 런타임입니다. 이는 서비스 검색 및 내장 재시도 논리를 사용한 호출, 배달 의미 체계를 최소 한 번 사용한 게시 및 구독 혹은 외부 서비스를 사용하여 쉽게 컴퍼지션하기 위한 플러그형 바인딩 리소스와 같은 일반적인 마이크로 서비스 패턴을 체계화합니다. [dapr.io](https://dapr.io)로 이동하여 자세한 정보를 확인하고 Dapr를 시작하는 방법에 대한 지침을 제공합니다. 정책의 추가 및 구성에 대한 자세한 내용은 [API Management 정책](api-management-howto-policies.md)을 참조하세요.

> [!IMPORTANT]
> 이 항목에서 참조하는 정책은 Dapr를 지원하는 [API Management 게이트웨이의 자체 호스팅 버전](self-hosted-gateway-overview.md)에서만 사용할 수 있습니다.

## <a name="enable-dapr-support-in-the-self-hosted-gateway"></a>자체 호스팅 게이트웨이에서 Dapr 지원 사용

자체 호스팅 게이트웨이에서 Dapr 지원 기능을 켜려면 “앱 이름”을 원하는 이름으로 대체하는 [Kubernetes 배포 템플릿](how-to-deploy-self-hosted-gateway-kubernetes.md) 아래 [Dapr 주석](https://github.com/dapr/docs/blob/master/README.md)을 추가합니다. Dapr로 API Management를 설정하고 사용하는 방법에 대한 전체 설명은 [여기](https://aka.ms/apim/dapr/walkthru)에서 볼 수 있습니다.
```yml
template:
    metadata:
      labels:
        app: app-name
      annotations:
        dapr.io/enabled: "true"
        dapr.io/app-id: "app-name"
```


## <a name="distributed-application-runtime-dapr-integration-policies"></a>Dapr(Distributed Application Runtime) 통합 정책

-  [서비스에 요청 보내기](api-management-dapr-policies.md#invoke): Dapr 런타임을 사용하여 Dapr 마이크로 서비스를 찾고 안정적으로 통신합니다. Dapr에서 서비스 호출에 대한 자세한 정보는 [추가 정보](https://github.com/dapr/docs/blob/master/README.md#service-invocation) 파일의 설명을 참조하세요.
-  [Pub/Sub 토픽으로 메시지 보내기](api-management-dapr-policies.md#pubsub): Dapr 런타임을 사용하여 게시/구독 토픽에 메시지를 게시합니다. Dapr에서 게시/구독 메시지 보내기에 대한 자세한 정보는 [추가 정보](https://github.com/dapr/docs/blob/master/README.md) 파일의 설명을 참조하세요.
-  [출력 바인딩 트리거](api-management-dapr-policies.md#bind): Dapr 런타임을 사용하여 출력 바인딩을 통해 외부 시스템을 호출합니다. Dapr의 바인딩에 대한 자세한 정보는 [추가 정보](https://github.com/dapr/docs/blob/master/README.md) 파일의 설명을 참조하세요.

## <a name="send-request-to-a-service"></a><a name="invoke"></a> 서비스에 요청 보내기

이 정책은 `http://localhost:3500/v1.0/invoke/{app-id}[.{ns-name}]/method/{method-name}` 템플릿 매개 변수를 정책문에 지정된 값으로 바꾸기 위해 현재 요청에 대한 대상 URL을 설정합니다.

이 정책은 Dapr가 게이트웨이와 동일한 Pod의 사이드카 컨테이너에서 실행되는 것으로 가정합니다. 요청을 받을 때, Dapr 런타임은 HTTP 및 gRPC 간의 가능한 프로토콜 변환, 다시 시도, 분산 추적 및 오류 처리를 포함하여 서비스 검색 및 실제 호출을 수행합니다.

### <a name="policy-statement"></a>정책 문

```xml
<set-backend-service backend-id="dapr" dapr-app-id="app-id" dapr-method="method-name" dapr-namespace="ns-name" />
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 “echo”라는 마이크로 서비스에서 “back” 이라는 메서드를 호출하는 방법을 보여 줍니다. `set-backend-service`정책은 대상 URL을 `http://localhost:3500/v1.0/invoke/echo.echo-app/method/back`로 설정합니다. `forward-request`정책은 요청을 마이크로 서비스로 전달하는 Dapr 런타임으로 디스패치합니다.

이 `forward-request`정책은 이해를 돕기 위해 여기에 표시됩니다. 이 정책은 일반적으로 `base` 키워드를 통해 전체 범위에서 “상속”됩니다.

```xml
<policies>
    <inbound>
        <base />
        <set-backend-service backend-id="dapr" dapr-app-id="echo" dapr-method="back" dapr-namespace="echo-app" />
    </inbound>
    <backend>
        <forward-request />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
    </on-error>
</policies>
```

### <a name="elements"></a>요소

| 요소             | Description  | 필수 |
|---------------------|--------------|----------|
| set-backend-service | 루트 요소 | 예      |

### <a name="attributes"></a>특성

| attribute        | Description                     | 필수 | 기본값 |
|------------------|---------------------------------|----------|---------|
| backend-id       | “dapr”로 설정해야 합니다.           | 예      | 해당 없음     |
| dapr-app-id      | 대상 마이크로 서비스 이름입니다. Dapr에서 [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) 매개 변수를 구성하는 데 사용됩니다.| 예 | 해당 없음 |
| dapr-method      | 대상 마이크로 서비스에서 호출할 메서드 또는 URL의 이름입니다. Dapr의 [메서드 이름](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) 매개 변수에 매핑됩니다.| 예 | 해당 없음 |
| dapr-namespace   | 대상 마이크로 서비스가 상주하는 네임스페이스의 이름입니다. Dapr에서 [appId](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/service_invocation_api.md) 매개 변수를 구성하는 데 사용됩니다.| 예 | 해당 없음 |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound
- **정책 범위:** 모든 범위

## <a name="send-message-to-pubsub-topic"></a><a name="pubsub"></a> Pub/Sub 토픽으로 메시지 보내기

이 정책은 API Management 게이트웨이가 Dapr 게시/구독 토픽에 메시지를 보내도록 지시합니다. 정책에서는 템플릿 매개 변수를 바꾸는 `http://localhost:3500/v1.0/publish/{{pubsub-name}}/{{topic}}`에 HTTP POST가 요청하게 하고 정책문에 지정된 콘텐츠를 추가함으로써 이 작업을 수행합니다.

이 정책에서는 Dapr 런타임이 게이트웨이와 동일한 pod의 사이드카 컨테이너에서 실행되고 있다고 가정합니다. Dapr 런타임에서는 Pub/Sub 의미 체계를 구현합니다.

### <a name="policy-statement"></a>정책 문

```xml
<publish-to-dapr pubsub-name="pubsub-name" topic="topic-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <!-- message content -->
</publish-to-dapr>
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 현재 요청의 본문을 “orders” Pub/Sub [구성 요소](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)의 “new” [토픽](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md#url-parameters)으로 보내는 방법을 보여 줍니다. Dapr 런타임에서 받은 응답은 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 개체의 Variables 컬렉션의 “Dapr 응답” 항목에 저장됩니다.

예를 들어, Dapr 런타임이 대상 토픽을 찾을 수 없는 경우 및 오류로 응답하는 경우에는 “오류 발생” 섹션이 트리거됩니다. Dapr 런타임에서 받은 응답은 호출자 그대로 반환됩니다. 대상 토픽을 찾은 경우 기본 `200 OK` 응답이 반환됩니다.

“백 엔드” 섹션이 비어 있고 요청이 백 엔드로 전달되지 않습니다.

```xml
<policies>
     <inbound>
        <base />
        <publish-to-dapr
           pubsub-name="orders"
               topic="new"
               response-variable-name="dapr-response">
            @(context.Request.Body.As<string>())
        </publish-to-dapr>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="pubsub-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>요소

| 요소             | Description  | 필수 |
|---------------------|--------------|----------|
| publish-to-dapr     | 루트 요소 | 예      |

### <a name="attributes"></a>특성

| attribute        | Description                     | 필수 | 기본값 |
|------------------|---------------------------------|----------|---------|
| pubsub-name      | 대상 PubSub 구성 요소의 이름입니다. Dapr에서 [pubsubname](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) 매개 변수에 매핑됩니다. 표시되지 않는 경우 __토픽__ 특성 값은 `pubsub-name/topic-name` 형식이어야 합니다.    | 예       | None    |
| 토픽            | 항목의 이름입니다. Dapr에서 [토픽](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/pubsub_api.md) 매개 변수에 매핑됩니다.               | 예      | 해당 없음     |
| ignore-error     | `true`로 설정하는 경우에는 Dapr 런타임에서 오류를 수신하는 동안 [“오류 발생”](api-management-error-handling-policies.md) 섹션을 트리거하지 않도록 정책에 지시합니다. | 예 | `false` |
| response-variable-name | Dapr 런타임의 응답을 저장하는 데 사용할 [변수](api-management-policy-expressions.md#ContextVariables) 컬렉션 항목의 이름입니다. | 예 | None |
| 시간 제한 | Dapr 런타임이 응답할 때까지 대기하는 시간(초)입니다. 범위는 1 ~ 240초입니다. | 예 | 5 |
| template | 메시지 콘텐츠를 변환하는 데 사용할 템플릿 엔진입니다. “액체”는 유일하게 지원되는 값입니다. | 예 | None |
| content-type | 메시지 콘텐츠의 유형입니다. “application/json”은 유일하게 지원되는 값입니다. | 예 | None |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, on-error
- **정책 범위:** 모든 범위

## <a name="trigger-output-binding"></a><a name="bind"></a> 트리거 출력 바인딩

이 정책은 API Management 게이트웨이에 아웃바운드 Dapr [바인딩을](https://github.com/dapr/docs/blob/master/README.md)트리거하도록 지시합니다. 정책에서는 템플릿 매개 변수를 바꾸는 `http://localhost:3500/v1.0/bindings/{{bind-name}}`에 HTTP POST가 요청하게 하고 정책문에 지정된 콘텐츠를 추가함으로써 이 작업을 수행합니다.

이 정책에서는 Dapr 런타임이 게이트웨이와 동일한 pod의 사이드카 컨테이너에서 실행되고 있다고 가정합니다. Dapr 런타임에서는 바인딩으로 표시되는 외부 리소스를 호출합니다.

### <a name="policy-statement"></a>정책 문

```xml
<invoke-dapr-binding name="bind-name" operation="op-name" ignore-error="false|true" response-variable-name="resp-var-name" timeout="in seconds" template="Liquid" content-type="application/json">
    <metadata>
        <item key="item-name"><!-- item-value --></item>
    </metadata>
    <data>
        <!-- message content -->
    </data>
</invoke-dapr-binding>
```

### <a name="examples"></a>예

#### <a name="example"></a>예제

다음 예제에서는 작업 이름이 “creat”인 “external-systems”라는 아웃바운드 바인딩, “source” 및 “client-ip”라는 두 개의 키/값 항목으로 구성된 메타데이터 및 원래 요청에서 오는 본문의 트리거를 보여 줍니다. Dapr 런타임에서 받은 응답은 [컨텍스트](api-management-policy-expressions.md#ContextVariables) 개체의 변수 컬렉션의 “바인딩-응답” 항목에 캡처됩니다.

몇 가지 이유로 인해 Dapr 런타임이 실패하고 오류로 응답하는 경우에는 “오류 발생” 섹션이 트리거되고 Dapr 런타임에서 수신한 응답이 호출자 그대로 반환됩니다. 대상 토픽을 찾은 경우 기본 `200 OK` 응답이 반환됩니다.

“백 엔드” 섹션이 비어 있고 요청이 백 엔드로 전달되지 않습니다.

```xml
<policies>
     <inbound>
        <base />
        <invoke-dapr-binding
                      name="external-system"
                      operation="create"
                      response-variable-name="bind-response">
            <metadata>
                <item key="source">api-management</item>
                <item key="client-ip">@( context.Request.IpAddress )</item>
            </metadata>
            <data>
                @( context.Request.Body.As<string>() )
            </data>
        </invoke-dapr-binding>
    </inbound>
    <backend>
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <base />
        <return-response response-variable-name="bind-response" />
    </on-error>
</policies>
```

### <a name="elements"></a>요소

| 요소             | Description  | 필수 |
|---------------------|--------------|----------|
| invoke-dapr-binding | 루트 요소 | 예      |
| metadata            | 특정 메타데이터를 키/값 쌍의 형식으로 바인딩합니다. Dapr의 [메타데이터](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) 속성에 매핑됩니다. | 예 |
| 데이터            | 메시지 내용입니다. Dapr의 [데이터](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) 속성에 매핑됩니다. | 예 |


### <a name="attributes"></a>특성

| attribute        | Description                     | 필수 | 기본값 |
|------------------|---------------------------------|----------|---------|
| name            | 대상 바인딩 이름입니다. Dapr에서 [정의된](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#bindings-structure) 바인딩의 이름과 일치해야 합니다.           | 예      | 해당 없음     |
| operation       | 대상 작업 이름(바인딩별)입니다. Dapr의 [작업](https://github.com/dapr/docs/blob/master/daprdocs/content/en/reference/api/bindings_api.md#invoking-output-bindings) 속성에 매핑됩니다. | 예 | None |
| ignore-error     | `true`로 설정하는 경우에는 Dapr 런타임에서 오류를 수신하는 동안 [“오류 발생”](api-management-error-handling-policies.md) 섹션을 트리거하지 않도록 정책에 지시합니다. | 예 | `false` |
| response-variable-name | Dapr 런타임의 응답을 저장하는 데 사용할 [변수](api-management-policy-expressions.md#ContextVariables) 컬렉션 항목의 이름입니다. | 예 | None |
| 시간 제한 | Dapr 런타임이 응답할 때까지 대기하는 시간(초)입니다. 범위는 1 ~ 240초입니다. | 예 | 5 |
| template | 메시지 콘텐츠를 변환하는 데 사용할 템플릿 엔진입니다. “액체”는 유일하게 지원되는 값입니다. | 예 | None |
| content-type | 메시지 콘텐츠의 유형입니다. “application/json”은 유일하게 지원되는 값입니다. | 예 | None |

### <a name="usage"></a>사용량

이 정책은 다음과 같은 정책 [섹션](./api-management-howto-policies.md#sections) 및 [범위](./api-management-howto-policies.md#scopes)에서 사용할 수 있습니다.

- **정책 섹션:** inbound, outbound, on-error
- **정책 범위:** 모든 범위
