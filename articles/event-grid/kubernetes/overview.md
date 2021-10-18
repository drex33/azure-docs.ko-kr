---
title: Kubernetes의 Azure Event Grid - 개요
description: 이 문서에서는 Azure Arc를 사용하는 Kubernetes의 Event Grid에 대한 개요를 제공합니다.
author: jfggdl
ms.author: jafernan
ms.subservice: kubernetes
ms.date: 05/25/2021
ms.topic: overview
ms.openlocfilehash: 109c9248bf087cb998fd8debdc644de8cfb1488b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714902"
---
# <a name="event-grid-on-kubernetes-with-azure-arc-preview---overview"></a>Azure Arc를 사용하는 Kubernetes의 Event Grid(미리 보기) - 개요
이 문서에서는 Kubernetes의 Event Grid, 사용 사례, 제공하는 기능 및 Azure Event Grid와의 차이점에 대한 개요를 제공합니다.

[!INCLUDE [event-grid-preview-feature-note.md](../includes/event-grid-preview-feature-note.md)]

## <a name="what-is-event-grid"></a>Event Grid란?
Event Grid는 이벤트 중심 아키텍처를 사용하는 워크로드를 통합하는 데 사용되는 이벤트 브로커입니다. 이벤트 기반 아키텍처는 이벤트를 사용하여 시스템 상태 변경의 발생을 전달하며 마이크로 서비스를 사용하는 아키텍처와 같은 분리된 아키텍처의 일반적인 통합 접근 방식입니다. Event Grid는 구독자가 이벤트를 전송(푸시)하고 해당 구독자가 이벤트를 보내는 게시자를 반드시 인식하는 것이 아닌 푸시-푸시 통신 모델이라고도 설명하는 게시-구독을 제공합니다. 이 모델은 클라이언트가 메시지 브로커에서 메시지를 끌어와 결과적으로 메시지 브로커와 사용 클라이언트 간에 더 강력한 결합이 있는 Azure Service Bus 또는 Azure Event Hubs에서 사용되는 것과 같은 클래식 푸시-풀 모델과 대조됩니다.

Event Grid는 Azure의 완전 관리형 PaaS 서비스인 **Azure Event Grid** 와 배포되는 위치(온 프레미스 또는 클라우드)와 상관없이 Kubernetes 클러스터에서 Event Grid를 사용할 수 있도록 하는 Azure Arc를 사용하는 Kubernetes의 Event Grid의 두 가지 버전으로 제공됩니다. 

이 문서에서는 명확성을 위해 사용된 버전에 관계없이 일반 서비스 기능을 언급할 때 **Event Grid** 라는 용어를 사용합니다. Azure에서 호스트되는 관리 서비스를 나타내기 위해 **Azure Event Grid** 라고 합니다. 간결함을 위해 **Azure Arc를 사용하는 Kubernetes의 Event Grid** 를 **Kubernetes의 Event Grid** 라고도 합니다.

사용하는 Event Grid 버전에 관계없이 Event Grid에 이벤트를 보내는 **이벤트 게시자** 와 Event Grid에서 전달한 이벤트를 수신하는 엔드포인트를 공개하는 하나 이상의 **이벤트 구독자** 가 있습니다. Event Grid에 게시된 모든 이벤트를 모든 이벤트 구독자에게 전달할 필요는 없습니다. Event Grid를 사용하면 **이벤트 구독** 에 정의된 구성 설정 집합을 통해 특정 대상으로 라우팅되어야 하는 이벤트를 선택할 수 있습니다. 이벤트 구독에서 필터를 사용하여 특정 이벤트를 하나의 엔드포인트로 라우팅하거나 멀티캐스트를 여러 엔드포인트로 라우팅할 수 있습니다. Event Grid는 다시 시도 논리도 포함된 안정적인 전달 메커니즘을 제공합니다. Event Grid는 또한 개방형 표준을 기반으로 하며 [Cloud Events 1.0 스키마 사양](https://github.com/cloudevents/spec/blob/master/spec.md)을 지원합니다.


## <a name="event-grid-on-kubernetes-with-azure-arc"></a>Azure Arc를 사용하는 Kubernetes의 Event Grid
Azure Arc를 사용하는 Kubernetes의 Event Grid는 자체 Kubernetes 클러스터에서 Event Grid를 실행할 수 있는 제품입니다. 이 기능은 [Azure Arc 지원 Kubernetes](../../azure-arc/kubernetes/overview.md)를 사용하여 활성화됩니다. Azure Arc 지원 Kubernetes를 통해 [지원되는 Kubernetes 클러스터](install-k8s-extension.md#supported-kubernetes-distributions)가 Azure에 연결됩니다. 연결되면 여기에 [Event Grid를 설치](install-k8s-extension.md)할 수 있습니다. 

### <a name="use-case"></a>사용 사례
Kubernetes의 Event Grid는 다양한 이벤트 중심 통합 시나리오를 지원합니다. 그러나 사용자 스토리로 지원되고 표현되는 주요 포괄적인 시나리오는 다음과 같습니다.

"Kubernetes 클러스터에 배포된 시스템의 소유자로서 이벤트를 게시하고 해당 이벤트의 라우팅을 구성하여 시스템의 상태 변경 사항을 전달하려고 합니다. 이를 통해 내 제어와 상관 없이 이벤트 처리기가 적절하다고 생각되는 방식으로 내 시스템의 이벤트를 처리할 수 있습니다."

위의 요구 사항을 실현하는 데 도움이 되는 **기능**: [Event Grid 토픽](/rest/api/eventgrid/version2021-06-01-preview/topics).

### <a name="event-grid-on-kubernetes-at-a-glance"></a>Kubernetes의 Event Grid 살펴보기
사용자 관점에서 Kubernetes의 Event Grid는 파란색의 다음 리소스로 구성됩니다.

:::image type="content" source="./media/overview/event-grid-topics.png" alt-text="리소스" lightbox="./media/overview/event-grid-topics.png":::

* **토픽** 은 게시자가 Event Grid에 이벤트를 보내는 엔드포인트를 공개하는 일종의 입력 채널입니다.
* **이벤트 구독** 은 이벤트가 전달되는 대상으로 이벤트를 필터링하고 라우팅하기 위한 구성 설정이 포함된 리소스입니다.
* **이벤트** 는 상태 변경 알림입니다.
* **이벤트 처리기** 는 이벤트를 수신하고 어떤 방식으로든 이벤트에 반응하거나 처리하는 애플리케이션 또는 서비스입니다. 때로는 이벤트 처리기를 **이벤트 구독자** 라고도 합니다. 위의 다이어그램에서 이벤트 처리기는 Kubernetes 클러스터(K8s) 및 Azure Service Bus 서비스에 배포된 API입니다.

이러한 개념에 대한 자세한 내용은 [Azure Event Grid의 개념](concepts.md)을 참조하세요.

### <a name="sample-workload-integration-scenarios-and-destinations"></a>샘플 워크로드 통합 시나리오 및 대상

클러스터에서 실행되는 워크로드를 통합할 수 있습니다. 게시자는 Kubernetes 클러스터에서 실행되는 모든 서비스 또는 게시자가 이벤트를 보내는 토픽 엔드포인트(Event Grid 브로커에서 호스트됨)에 대한 액세스 권한이 있는 워크로드일 수 있습니다.

:::image type="content" source="./media/overview/event-grid-intra-cluster-integration.png" alt-text="클러스터 내 통합" lightbox="./media/overview/event-grid-intra-cluster-integration.png":::


Kubernetes 클러스터 중 하나에 배포된 Event Grid로 이벤트를 보내는 네트워크의 다른 곳에 배포된 게시자를 사용할 수도 있습니다.

:::image type="content" source="./media/overview/event-grid-in-network-integration.png" alt-text="네트워크 내 통합" lightbox="./media/overview/event-grid-in-network-integration.png":::

Kubernetes의 Event Grid를 사용하면 추가 처리, 스토리지 또는 시각화를 위해 이벤트를 Azure로 전달할 수 있습니다.

:::image type="content" source="./media/overview/event-grid-forward-events.png" alt-text="Azure로 이벤트 전달":::

#### <a name="destinations"></a>대상
이벤트 처리기 대상은 Event Grid가 네트워크(퍼블릭 또는 프라이빗)를 통해 도달할 수 있는 HTTPS 또는 HTTP 엔드포인트일 수 있으며, 여기에는 액세스 권한(일부 인증 메커니즘으로 보호되지 않음)이 있습니다. 이벤트 구독을 만들 때 이벤트 전달 대상을 정의합니다. 자세한 내용은 [이벤트 처리기](event-handlers.md)를 참조하세요. 

## <a name="features"></a>기능
Kubernetes의 Event Grid는 [Azure Event Grid](../custom-topics.md)에서도 제공하는 기능인 [Event Grid 토픽](/rest/api/eventgrid/version2021-06-01-preview/topics)을 지원합니다. Event Grid 토픽은 사용자가 소유하거나 시스템에 액세스할 수 있는 다른 워크로드와 시스템을 통합해야 하는 요구 사항이 있는 [기본 통합 사용 사례](#use-case)를 이해하는 데 도움이 됩니다.

Kubernetes의 Azure Event Grid를 통해 얻을 수 있는 몇 가지 기능은 다음과 같습니다.

* **[이벤트 필터링](filter-events.md)** : 이벤트 유형, 이벤트 제목 또는 이벤트 데이터를 필터링하여 이벤트 처리기가 관련 이벤트만 수신하도록 합니다.
* **팬 아웃**: 많은 위치로 이벤트 복사본을 보낼 수 있도록 동일한 이벤트에 대한 여러 엔드포인트를 구독합니다.
* **개방형 표준 기반**: CNCF의 [Cloud Events 1.0 스키마 사양](https://github.com/cloudevents/spec/blob/master/spec.md)을 사용하여 이벤트를 정의합니다.
* **신뢰성**: Event Grid에는 이벤트가 대상에 도달하도록 하는 다시 시도 이벤트 전달 논리가 있습니다.

자세한 내용은 [Kubernetes의 Event Grid가 지원하는 기능](features.md)을 참조하세요.

## <a name="pricing"></a>가격 책정 
Azure Arc를 사용하는 Kubernetes의 Event Grid는 미리 보기 버전에서 무료로 제공됩니다.

## <a name="next-steps"></a>다음 단계
Kubernetes의 Event Grid를 사용하여 이벤트 라우팅을 시작하려면 다음 단계를 따르세요.

1. [클러스터를 Azure Arc에 연결합니다](../../azure-arc/kubernetes/quickstart-connect-cluster.md).
1. Kubernetes 클러스터에 Event Grid를 배포하는 실제 리소스인 [Event Grid 확장을 설치](install-k8s-extension.md)합니다. 확장에 대한 자세한 내용은 [Event Grid 확장](install-k8s-extension.md#event-grid-extension) 섹션을 참조하세요. 
1. [사용자 지정 위치를 만듭니다](../../azure-arc/kubernetes/custom-locations.md). 사용자 지정 위치는 클러스터의 네임스페이스를 나타내며 토픽 및 이벤트 구독이 배포되는 위치입니다.
1. [토픽 및 하나 이상의 이벤트 구독을 만듭니다](create-topic-subscription.md).
1. [이벤트를 게시합니다](create-topic-subscription.md).

다음은 사용할 수 있는 추가 리소스입니다.

* [데이터 평면 SDK](../sdk-overview.md#data-plane-sdks).
* [데이터 평면 SDK를 사용하여 이벤트 예를 게시합니다](https://devblogs.microsoft.com/azure-sdk/event-grid-ga/).
* [Event Grid CLI](/cli/azure/eventgrid).
* [관리 SDK](../sdk-overview.md#management-sdks).
