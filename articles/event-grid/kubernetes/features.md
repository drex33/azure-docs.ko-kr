---
title: Kubernetes의 Azure Event Grid - 기능
description: 이 문서에서는 Kubernetes의 Event Grid 기능을 Azure의 Event Grid와 비교합니다.
author: jfggdl
ms.subservice: kubernetes
ms.author: jafernan
ms.date: 05/25/2021
ms.topic: conceptual
ms.openlocfilehash: c24a6a8278aaea9ff78122ac632d08e8bf5ef8a0
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132935012"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-features"></a>Azure Arc 기능을 사용하는 Kubernetes의 Event Grid
Kubernetes의 Event Grid는 워크로드를 통합하고 하이브리드 아키텍처를 실현하는 데 도움이 되는 다양한 기능 집합을 제공합니다. 동일한 서비스의 다른 버전과 같은 [rest API](/rest/api/eventgrid/version2021-06-01-preview/topics)(버전 2020-10-15-preview부터 적용), [Event Grid CLI](/cli/azure/eventgrid), Azure Portal 환경, [관리 SDK](../sdk-overview.md#management-sdks), Azure Event Grid를 사용하는 [데이터 평면 SDK](../sdk-overview.md#data-plane-sdks)를 공유합니다. 이벤트를 게시할 준비가 되면 Event Grid 두 버전 모두에서 작동하는 [다른 언어로 제공된 데이터 평면 SDK 예제](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/)를 사용할 수 있습니다.

Kubernetes의 Event Grid와 Azure Event Grid는 다양한 기능을 공유하며 동일한 사용자 환경 제공을 목표로 하지만, 충족해야 하는 요구 사항과 소프트웨어 수명 주기에 있는 단계가 서로 다릅니다. 예를 들어 Kubernetes의 Event Grid에서는 사용자 지정 항목이라고도 하는 Event Grid 항목 유형만 사용할 수 있습니다. 다른 유형의 항목(아래 참조)은 사용할 수 없거나 아직 지원되지 않습니다. Event Grid 두 버전의 주요 차이점은 아래 표에 나와 있습니다.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]


## <a name="event-grid-on-kubernetes-vs-event-grid-on-azure"></a>Kubernetes의 Event Grid와 Azure의 Event Grid 비교

| 기능 | Kubernetes의 Event Grid | Azure Event Grid |
|:--|:-:|:-:|
| [Event Grid 토픽](/rest/api/eventgrid/version2021-06-01-preview/topics) | ✔ | ✔ |
| [CNCF 클라우드 이벤트 스키마](https://github.com/cloudevents/spec/blob/main/cloudevents/spec.md) | ✔ | ✔ |
| Event Grid 및 사용자 지정 스키마 | ✘* | ✔ |
| 안정적인 배달 | ✔ | ✔ |
| 메트릭  | ✔** | ✔ |
| Azure Monitor  | ✘ | ✔ |
| [배달 못한 편지 위치](../manage-event-delivery.md#set-dead-letter-location) | ✘ | ✔ |
| [다른 Event Grid 항목으로 이벤트 전달](event-handlers.md#azure-event-grid) | ✔ | ✘ |
| [시스템 항목](../system-topics.md) | ✘ | ✔ |
| [도메인 항목](../event-domains.md) | ✘ | ✔ |
| [파트너 이벤트](../partner-events-overview.md) | ✘ | ✔ |
| [대상 엔드포인트 유효성 검사](../webhook-event-delivery.md#endpoint-validation-with-event-grid-events) | ✘ | ✔ |
| [Azure Functions의 Azure Event Grid 트리거](../../azure-functions/functions-bindings-event-grid-trigger.md) | ✘ | ✔ |
| Azure Relay의 하이브리드 연결을 대상으로 지정 | ✘ | ✔ |
| [고급 필터링](filter-events.md) | ✔*** | ✔ |
| [AAD를 사용하는 Webhook AuthN/AuthZ](../secure-webhook-delivery.md) | ✘ | ✔ |
| [리소스 ID를 사용하여 이벤트 전달](/rest/api/eventgrid/version2021-06-01-preview/event-subscriptions/create-or-update) | ✘ | ✔ |
| 동일한 데이터 평면 SDK 집합 | ✔ | ✔ |
| 동일한 관리 SDK 집합 | ✔ | ✔ |
| 동일한 Event Grid CLI | ✔ | ✔ |

\* Cloud Events 1.0 스키마는 확장성 메커니즘을 제공하며 개방형 표준입니다. 이러한 품질 또는 기능은 Event Grid나 사용자 지정 스키마에서는 제공되지 않습니다. Cloud Events 1.0 스키마는 Event Grid 스키마의 개선 버전입니다.

\** 항목 및 이벤트 구독에 대한 메트릭은 [Prometheus 표시 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)을 사용하여 제공됩니다. Azure Portal의 메트릭 또는 기타 모니터링 기능은 현재 미리 보기 버전에서는 사용할 수 없습니다.

\*** Kubernetes의 Event Grid는 Azure의 Event Grid처럼 이벤트 데이터의 값을 기반으로 하는 이벤트 고급 필터링을 지원하지만, Kubernetes의 Event Grid에서는 지원하지 않는 몇 가지 기능과 연산자가 있습니다. 자세한 내용은 [고급 필터링](filter-events.md#filter-by-values-in-event-data)을 참조하세요.

## <a name="next-steps"></a>다음 단계
Kubernetes의 Event Grid에 대한 자세한 내용은 [Azure Arc를 사용하는 Kubernetes의 Event Grid(미리 보기) - 개요](overview.md)를 참조하세요.
