---
title: Event Grid 문제 해결
description: 이 문서에서는 Azure Event Grid 문제를 해결하는 다양한 방법을 제공합니다.
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 94370af8a3325d1798c3e2bcb65c2ccb3e54a43b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546351"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Azure Event Grid 문제 해결
이 문서에서는 Azure Event Grid 문제를 해결하는 데 도움이 되는 정보를 제공합니다. 

## <a name="diagnostic-logs"></a>진단 로그
게시 및 배달 오류 로그를 캡처하고 볼 수 있도록 Event Grid 토픽 또는 도메인에 대한 진단 설정을 사용하도록 설정합니다. 자세한 내용은 [진단 로그](enable-diagnostic-logs-topic.md)를 참조하세요.

## <a name="metrics"></a>메트릭
Event Grid 토픽 및 구독에 대한 메트릭을 확인하고 이에 대한 경고를 만들 수 있습니다. 자세한 내용은 [Event Grid 메트릭](monitor-event-delivery.md)을 참조하세요.

## <a name="alerts"></a>경고
Azure Event Grid 메트릭 및 활동 로그 작업에 대한 경고를 만듭니다. 자세한 내용은 [Event Grid 메트릭 및 활동 로그에 대한 경고](set-alerts.md)를 참조하세요.

## <a name="subscription-validation-issues"></a>구독 유효성 검사 문제
이벤트 구독을 만드는 동안 제공된 엔드포인트의 유효성 검사가 실패했다는 오류 메시지가 표시될 수 있습니다. 구독 유효성 검사 문제를 해결하려면 [Event Grid 구독 유효성 검사 문제 해결](troubleshoot-subscription-validation.md)을 참조하세요. 

## <a name="network-connectivity-issues"></a>네트워크 연결 문제
클라이언트 애플리케이션에서 Event Grid 토픽/도메인에 연결할 수 없는 다양한 이유가 있습니다. 발생하는 연결 문제는 영구적이거나 일시적일 수 있습니다. 이러한 문제를 해결하는 방법에 대한 자세한 내용은 [연결 문제 해결](troubleshoot-network-connectivity.md)을 참조하세요.

## <a name="error-codes"></a>오류 코드
400, 409 및 403과 같은 오류 코드를 함 하는 오류 메시지가 표시되면 [Event Grid 오류 문제 해결](troubleshoot-errors.md)을 참조하세요. 

## <a name="distributed-tracing"></a>분산된 추적
.NET, Java, Python 및 JavaScript의 Event Grid 라이브러리는 추적 배포를 지원 합니다. 추적 배포에 대 한 [CloudEvents 사양의 지침](https://github.com/cloudevents/spec/blob/v1.0.1/extensions/distributed-tracing.md) 을 준수 하기 위해 배포 된 추적을 사용 하는 경우 라이브러리는 `traceparent` `tracestate` 확장의 및 특성을 설정 `CloudEvent` 합니다.

응용 프로그램에서 분산 추적을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 Azure SDK 분산 추적 설명서를 참조 하세요.

- [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)
- [Java](/azure/developer/java/sdk/tracing)
- [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/core/azure-core-tracing-opentelemetry)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/core/README.md#tracing)

[azure Event Hubs](handler-event-hubs.md) 또는 [azure Service Bus](handler-service-bus.md) Event Grid 구독에 대 한 종단 간 추적을 사용 하도록 설정 하려면 CloudEvent extension 특성을 amqp 응용 프로그램 속성에 전달 하도록 [사용자 지정 배달 속성](delivery-properties.md) 을 구성 `traceparent` `Diagnostic-Id` 합니다. 

Event Hubs에 대해 구성 된 추적 배달 속성이 있는 구독의 예는 다음과 같습니다.

```azurecli
az eventgrid event-subscription create --name <event-grid-subscription-name> \
    --source-resource-id <event-grid-resource-id>
    --endpoint-type eventhub \
    --endpoint <event-hubs-endpoint> \
    --delivery-attribute-mapping Diagnostic-Id dynamic traceparent
```

### <a name="sample"></a>샘플
[줄 카운터 샘플](/samples/azure/azure-sdk-for-net/line-counter/)을 참조하세요. 이 샘플 앱에서는 스토리지, Event Hubs 및 Event Grid 클라이언트를 ASP.NET Core 통합, 분산 추적 및 호스트된 서비스와 함께 사용하는 방법을 보여 줍니다. 이를 통해 사용자는 Blob에 파일을 업로드할 수 있으며 이 경우 파일 이름을 포함하는 Event Hubs 이벤트가 트리거됩니다. Event Hubs 프로세서가 이벤트를 받으면 앱은 Blob을 다운로드하고 파일의 줄 수를 계산합니다. 앱은 줄 수를 포함하는 페이지에 대한 링크를 표시합니다. 이 링크를 클릭하면 Event Grid를 사용하여 파일 이름을 포함하는 CloudEvent가 게시됩니다.

## <a name="next-steps"></a>다음 단계
도움이 더 필요한 경우 [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-eventgrid)에 문제를 게시하거나 [지원 티켓](https://azure.microsoft.com/support/options/)을 여세요. 
