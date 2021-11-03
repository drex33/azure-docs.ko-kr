---
title: Azure 리소스에 대한 복제 작업 만들기
description: Azure Logic Apps 워크플로를 기반으로 복제 작업 템플릿을 사용하여 Azure 리소스를 복제합니다.
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: bd4eaa70b456841ae47c6efa9bc3f2b323d0bcf5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103164"
---
# <a name="create-replication-tasks-for-azure-resources-using-azure-logic-apps-preview"></a>Azure Logic Apps 사용하여 Azure 리소스에 대한 복제 작업 만들기(미리 보기)

> [!IMPORTANT]
> 이 기능은 미리 보기로 제공되고 [Microsoft Azure 미리 보기의 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)이 적용됩니다.

최대 가용성 및 안정성은 Azure 서비스의 운영 우선 순위가 가장 높지만 네트워킹 또는 이름 확인 문제, 오류 또는 일시적인 무응답으로 인해 통신이 중지되는 여러 가지 방법이 있습니다. 이러한 조건은 재해 복구 상황에서처럼 지역 배포를 완전히 중단하려고 할 수 있도록 "비정상 상황"이 아닙니다. 그러나 일부 앱의 비즈니스 시나리오는 몇 분 또는 몇 초 이내에 지속되는 가용성 이벤트의 영향을 받습니다.

예측할 수 없는 이벤트가 Azure 지역의 Azure 리소스에 미칠 수 있는 영향을 줄이기 위해 비즈니스 연속성을 유지할 수 있도록 이러한 리소스의 콘텐츠를 한 지역에서 다른 지역으로 복제할 수 있습니다. Azure에서는 데이터, 이벤트 또는 메시지를 한 지역의 원본에서 다른 지역의 대상으로 이동하는 [*복제 작업을*](#replication-task) 만들 수 있습니다. 이렇게 하면 원본이 오프라인으로 전환되고 대상을 인수해야 하는 경우 대상을 쉽게 사용할 수 있습니다.

> [!NOTE]
> 복제 작업을 사용하여 동일한 지역의 엔터티 간에 콘텐츠를 이동할 수도 있지만 전체 지역을 사용할 수 없게 되거나 중단이 발생하는 경우 원본과 대상 모두에 영향을 받습니다.

이 문서에서는 Azure Logic Apps 제공하는 복제 태스크에 대한 개요를 제공하고 Azure Service Bus 큐에 대한 복제 작업 예제를 만드는 방법을 보여줍니다. 논리 앱 및 워크플로를 처음으로 접하는 경우 Azure Logic Apps 위해 Azure Logic Apps 및 [단일 테넌트 및 다중 테넌트 및 통합 서비스 환경이란?을](single-tenant-overview-compare.md)검토하세요. [](logic-apps-overview.md)

<a name="replication-task"></a>

## <a name="what-is-a-replication-task"></a>복제 작업이란 무엇인가요?

일반적으로 복제 작업은 원본에서 데이터, 이벤트 또는 메시지를 수신하고, 해당 콘텐츠를 대상으로 이동한 다음, 원본이 Event Hubs 엔터티인 경우를 제외하고 원본에서 해당 콘텐츠를 삭제합니다. 복제 태스크는 일반적으로 콘텐츠를 변경하지 않고 이동하지만 Azure Logic Apps 의해 구동되는 복제 태스크도 [복제 속성을 추가합니다.](#replication-properties) 원본 프로토콜과 대상 프로토콜이 다른 경우 이러한 작업은 메타데이터 구조 간의 매핑도 수행합니다. 복제 작업은 일반적으로 상태 비저장입니다. 즉, 작업의 병렬 또는 순차적 실행에서 상태 또는 기타 부작용을 공유하지 않습니다.

사용 가능한 복제 작업 템플릿을 사용하는 경우 만드는 각 복제 작업에는 **논리 앱(표준)** 리소스에 기본 [상태 비저장 워크플로가](single-tenant-overview-compare.md#stateful-stateless) 있으며, 여기에는 복제 작업에 대한 여러 워크플로가 포함될 수 있습니다. 이 리소스는 복제 및 페더레이션 작업을 포함하여 서버리스 애플리케이션을 구성하고 실행하기 위한 확장 가능하고 안정적인 실행 환경인 단일 테넌트 Azure Logic Apps 호스트됩니다. 또한 단일 테넌트 Azure Logic Apps 런타임은 [Azure Functions 확장성 모델을](../azure-functions/functions-bindings-register.md) 사용하며 Azure Functions 런타임에서 확장으로 호스트됩니다. 이 디자인은 논리 앱 워크플로에 대한 이식성, 유연성 및 추가 성능과 Azure Functions 플랫폼 및 Azure App Service 에코시스템에서 상속된 기타 기능 및 이점을 제공합니다.

복제 및 페더레이션에 대한 자세한 내용은 다음 설명서를 검토하세요.

- [다중 사이트 및 다중 지역 페더레이션 Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [이벤트 복제 작업 패턴](../event-hubs/event-hubs-federation-patterns.md)
- [Service Bus 메시지 복제 및 지역 간 페더레이션](../service-bus-messaging/service-bus-federation-overview.md)
- [메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-task-templates"></a>

## <a name="replication-task-templates"></a>복제 태스크 템플릿

현재 복제 작업 템플릿은 [Azure Event Hubs](../event-hubs/event-hubs-about.md) 및 Azure [Service Bus](../service-bus-messaging/service-bus-messaging-overview.md)사용할 수 있습니다. 다음 표에서는 현재 이 미리 보기에서 사용할 수 있는 복제 작업 템플릿을 나열합니다.

| 리소스 유형 | 복제 원본 및 대상 |
|---------------|-------------------------------|
| Azure Event Hubs 네임스페이스 | - 인스턴스를 Event Hubs 인스턴스에 Event Hubs <br>- Service Bus 큐에 인스턴스 Event Hubs <br>- 인스턴스를 Service Bus 토픽에 Event Hubs |
| Azure Service Bus 네임스페이스 | - Service Bus 큐에 큐 Service Bus <br>- Service Bus 큐에 Service Bus 항목 <br>- 이벤트 허브 인스턴스에 큐 Service Bus <br>- Service Bus 큐에 토픽 구독 Service Bus <br>- Event Hubs 인스턴스에 토픽 구독 Service Bus |
|||

### <a name="replication-topology-and-workflow"></a>복제 토폴로지 및 워크플로

Azure Logic Apps(표준)에서 구동되는 복제 태스크의 작동 방식을 시각화하기 위해 다음 다이어그램에서는 Event Hubs 인스턴스 및 Service Bus 큐에 대한 복제 태스크 구조와 워크플로를 보여 주었습니다.

#### <a name="replication-topology-for-event-hubs"></a>Event Hubs 대한 복제 토폴로지

다음 다이어그램은 Event Hubs 인스턴스 간의 토폴로지 및 복제 작업 워크플로를 보여줍니다.

![Event Hubs 인스턴스 간의 "논리 앱(표준)" 워크플로를 통해 구동되는 복제 태스크에 대한 토폴로지의 개념 다이어그램](media/create-replication-tasks-azure-resources/replication-topology-event-hubs.png)

Azure Event Hubs 복제 및 페더레이션에 대한 자세한 내용은 다음 설명서를 검토하세요.

- [다중 사이트 및 다중 지역 페더레이션 Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [이벤트 복제 작업 패턴](../event-hubs/event-hubs-federation-patterns.md)

#### <a name="replication-topology-for-service-bus"></a>Service Bus 대한 복제 토폴로지

다음 다이어그램은 Service Bus 큐 간의 토폴로지 및 복제 작업 워크플로를 보여줍니다.

![Service Bus 큐 간의 "논리 앱(표준)" 워크플로를 통해 구동되는 복제 태스크에 대한 토폴로지의 개념 다이어그램](media/create-replication-tasks-azure-resources/replication-topology-service-bus-queues.png)

Azure Service Bus 복제 및 페더레이션에 대한 자세한 내용은 다음 설명서를 검토하세요.

- [Service Bus 메시지 복제 및 지역 간 페더레이션](../service-bus-messaging/service-bus-federation-overview.md)
- [메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="replication-properties"></a>

## <a name="metadata-and-property-mappings"></a>메타데이터 및 속성 매핑

Event Hubs 경우 원본 Event Hubs 네임스페이스에서 가져온 다음 항목은 대상 Event Hub 네임스페이스의 새 서비스 할당 값으로 대체됩니다. 이벤트의 서비스 할당 메타데이터, 원래 지연 시간, 시퀀스 번호 및 오프셋입니다. 그러나 Azure 제공 샘플의 [도우미 함수](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) 및 복제 작업의 경우 원래 값은 `repl-enqueue-time` (ISO8601 문자열), 및 사용자 속성에 `repl-sequence` 유지됩니다. `repl-offset` 이러한 속성에는 `string` 형식이 있으며 각 원래 속성의 문자열화된 값이 포함됩니다. 이벤트가 여러 번 전달되는 경우에는 직접적인 원본의 서비스 할당된 메타데이터가 이미 존재하는 속성에 추가되고 값은 세미콜론으로 구분됩니다. 자세한 내용은 [서비스 할당 메타데이터 - 이벤트 복제 작업 패턴 을 검토하세요.](../event-hubs/event-hubs-federation-patterns.md#service-assigned-metadata)

Service Bus 경우 원본 Service Bus 큐 또는 토픽에서 가져온 다음 항목은 대상 Service Bus 큐 또는 토픽의 새 서비스 할당 값으로 대체됩니다. 메시지의 서비스 할당 메타데이터, 원래 큐에 대기 시간 및 시퀀스 번호입니다. 그러나 Azure에서 제공하는 샘플의 기본 복제 작업의 경우 원래 값은 사용자 `repl-enqueue-time` 속성(ISO8601 문자열) 및 에 `repl-sequence` 유지됩니다. 이러한 속성에는 `string` 형식이 있으며 각 원래 속성의 문자열화된 값이 포함됩니다. 메시지가 여러 번 전달되는 경우에는 직접적인 원본의 서비스 할당된 메타데이터가 이미 존재하는 속성에 추가되고 값은 세미콜론으로 구분됩니다. 자세한 내용은 [서비스 할당 메타데이터 - 메시지 복제 작업 패턴 을 검토하세요.](../service-bus-messaging/service-bus-federation-patterns.md#service-assigned-metadata)

작업이 Service Bus Event Hubs 복제하는 경우 작업은 `User Properties` 속성만 `Properties` 속성에 매핑합니다. 그러나 작업이 Event Hubs Service Bus 복제될 때 태스크는 다음 속성을 매핑합니다.

| From Event Hubs | Service Bus |
|-----------------|----------------|
| ContentType | ContentType |
| CorrelationId | CorrelationId |
| MessageId | MessageId |
| PartitionKey | PartitionKey SessionId |
| 속성 | 사용자 속성 |
| ReplyTo | ReplyTo |
| ReplyToGroupName | ReplyToSessionId |
| 제목 | 레이블 |
| 대상 | 대상 |
|||

<a name="order-preservation"></a>

## <a name="order-preservation"></a>순서 유지

Event Hubs 경우 동일한 수의 파티션 간 복제는 이벤트를 변경하지 않고 1:1 클론을 만들지만 중복을 포함할 수도 있습니다. 그러나 서로 다른 파티션 수 간의 복제는 파티션 키에 따라 이벤트의 상대 순서만 유지되지만 중복을 포함할 수도 있습니다. 자세한 내용은 [스트림 및 순서 유지를 검토하세요.](../event-hubs/event-hubs-federation-patterns.md#streams-and-order-preservation)

Service Bus 경우 원본에서 검색된 세션 ID가 동일한 메시지 시퀀스가 원래 시퀀스의 일괄 처리로 대상 큐 또는 토픽에 제출되고 세션 ID가 같도록 세션을 사용하도록 설정해야 합니다. 자세한 내용은 [시퀀스 및 순서 유지를](../service-bus-messaging/service-bus-federation-patterns.md#sequences-and-order-preservation)검토하세요.

복제 작업을 만들 수 있는 Azure 서비스의 다중 사이트 및 다중 지역 페더레이션에 대해 자세히 알아보려면 다음 설명서를 검토하세요.

- [다중 사이트 및 다중 지역 페더레이션 Event Hubs](../event-hubs/event-hubs-federation-overview.md)
- [이벤트 복제 작업 패턴](../event-hubs/event-hubs-federation-patterns.md)
- [Service Bus 메시지 복제 및 지역 간 페더레이션](../service-bus-messaging/service-bus-federation-overview.md)
- [메시지 복제 작업 패턴](../service-bus-messaging/service-bus-federation-patterns.md)

<a name="pricing"></a>

## <a name="pricing"></a>가격 책정

아래에서 복제 작업은 단일 테 넌 트 Azure Logic Apps에서 호스트 되는 **논리 앱 (표준)** 리소스의 상태 비저장 워크플로에 의해 구동 됩니다. 이 복제 작업을 만들 때 요금은 즉시 부과 되기 시작 합니다. 사용량, 계량, 요금 청구 및 가격 책정 모델은 [표준 호스팅 계획](logic-apps-pricing.md#standard-pricing) 및 [표준 계획 가격 책정 계층](logic-apps-pricing.md#standard-pricing-tiers)을 따릅니다.

Event Hubs에서 수신 하는 이벤트 수 또는 Service Bus 처리 하는 메시지를 기준으로 표준 계획을 확장 하거나 축소 하 여 활성 복제 중에 최소 CPU 사용량과 낮은 대기 시간을 유지할 수 있습니다. 이 동작을 수행 하려면 적절 한 표준 계획 가격 책정 계층을 선택 해야 Azure Logic Apps에서 CPU 사용량을 제한 하거나 시작 하지 않고 빠른 복제 속도를 보장할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 계정 및 구독 구독이 없는 경우 [Azure 체험 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

- 원본 및 대상 리소스 또는 엔터티-지리적 재해 복구 장애 조치 (failover) 시나리오를 테스트할 수 있도록 서로 다른 Azure 지역에 있어야 합니다. 이러한 엔터티는 사용 하려는 작업 템플릿에 따라 달라질 수 있습니다. 이 문서의 예제에서는 서로 다른 네임 스페이스와 Azure 지역에 있는 두 개의 Service Bus 큐를 사용 합니다.

- 복제 태스크를 만들 때 다시 사용할 수 있는 **논리 앱 (표준)** 리소스입니다. 이렇게 하면 용량, 처리량 및 크기 조정과 같이 복제 시나리오의 요구 사항에 따라 [호스팅 계획 및 가격 책정 계층](#pricing) 을 선택 하는 등 복제 작업을 위해이 리소스를 구체적으로 사용자 지정할 수 있습니다. 복제 작업을 만들 때이 리소스를 만들 수 있지만 지역, 호스팅 계획 및 가격 책정 계층을 변경할 수 없습니다. 다음 목록에서는 이전에 만든 논리 앱 리소스에 대 한 다른 이유 및 모범 사례를 제공 합니다.

  - 복제 작업의 원본 및 대상 엔터티와 다른 지역에서이 논리 앱 리소스를 만들 수 있습니다.

    현재이 지침은 Azure 리소스 내에서 복제 작업의 기본 통합으로 인해 제공 됩니다. 엔터티 간에 복제 작업을 만들고 기존 리소스를 사용 하는 대신 새 논리 앱 리소스를 만들도록 선택 하는 경우 *새 논리 앱은 원본 엔터티와 동일한 지역에 생성 됩니다*. 원본 지역을 사용할 수 없게 되 면 복제 작업도 작동할 수 없습니다. 장애 조치 (failover) 시나리오에서 작업은 새 원본 (이전에는 [활성-수동 복제 패턴이](../service-bus-messaging/service-bus-federation-overview.md#active-passive-replication) 시도 하는 대상 엔터티)의 데이터 읽기를 시작할 수 없습니다.

  - 기본 특성을 사용 하는 대신 호스팅 계획 및 가격 책정 계층을 선택 하 여이 논리 앱 리소스를 미리 사용자 지정할 수 있습니다. 이렇게 하면 복제 작업에서 더 빠른 복제를 위해 더 많은 이벤트 또는 초당 메시지를 처리할 수 있습니다. 복제 태스크를 만들 때이 리소스를 만들 경우 이러한 기본 특성은 고정 됩니다.

  - 특히 활성-수동 복제 패턴을 따라야 하는 경우이 논리 앱 리소스에 *복제 태스크 워크플로만* 포함 되도록 할 수 있습니다. 기존 논리 앱을 사용 하 여 복제 작업을 만드는 경우이 옵션은 해당 논리 앱 리소스에 작업 (상태 비저장 워크플로)을 추가 합니다.

  자세한 내용은 [Azure Portal에서 단일 테 넌 트 Azure Logic Apps (표준)를 사용 하 여 통합 워크플로 만들기](create-single-tenant-workflows-azure-portal.md)를 검토 하세요.

- 선택 사항: 대상 네임 스페이스에 대 한 연결 문자열입니다. 이 옵션을 사용 하면 다른 구독에 대상을 지정할 수 있으므로 구독 간 복제를 설정할 수 있습니다.

   대상 엔터티에 대 한 연결 문자열을 찾으려면 다음 단계를 수행 합니다.

   1. [Azure Portal](https://portal.azure.com)에서 대상 네임 스페이스로 이동 합니다.

   1. 네임 스페이스 탐색 메뉴의 **설정** 아래에서 **공유 액세스 정책** 을 선택 합니다.

   1. 열리는 **공유 액세스 정책** 창의 **정책** 아래에서 **RootManageSharedAccessKey** 을 선택 합니다.

   1. **SAS 정책: RootManageSharedAccessKey** 창에서 **주 연결 문자열** 값을 복사 합니다.

   1. 나중에 문자열을 사용 하 여 대상 네임 스페이스에 연결할 수 있도록 연결 문자열을 어딘가에 저장 합니다.

<a name="naming"></a>

## <a name="naming-conventions"></a>명명 규칙

복제 작업 또는 엔터티에 사용 하는 명명 전략을 아직 만들지 않은 경우 신중 하 게 고려해 야 합니다. 이름을 쉽게 식별 하 고 구분할 수 있는지 확인 합니다. 예를 들어 Event Hubs 네임 스페이스를 사용 하는 경우 복제 작업은 원본 네임 스페이스의 모든 이벤트 허브에서 복제 됩니다. Service Bus 큐를 사용 하 여 작업 하는 경우 다음 표에서는 엔터티 및 복제 작업의 이름을 지정 하는 예를 제공 합니다.

| 원본 이름 | 예제 | 복제 앱 | 예제 | 대상 이름 | 예제 |
|-------------|---------|-----------------|---------|-------------|---------|
| 네임스페이스: `<name>-sb-<region>` | `fabrikam-sb-weu` | 논리 앱: `<name-source-region-target-region>` | `fabrikam-rep-weu-wus` | 네임스페이스: `<name>-sb-<region>` | `fabrikam-sb-wus` |
| 큐 `<name>` | `jobs-transfer` | 워크플로 `<name>` | `jobs-transfer-workflow` | 큐 `<name>` | `jobs` |
|||||||

<a name="create-replication-task"></a>

## <a name="create-a-replication-task"></a>복제 작업 만들기

이 예에서는 Service Bus 큐에 대 한 복제 작업을 만드는 방법을 보여 줍니다.

1. [Azure Portal](https://portal.azure.com)에서 원본으로 사용 하려는 Service Bus 네임 스페이스를 찾습니다.

1. 네임 스페이스 탐색 메뉴의 **자동화** 섹션에서 **작업 (미리 보기)** 을 선택 합니다.

   !["작업 (미리 보기)"이 선택 된 Azure Portal 및 Azure Service Bus 네임 스페이스 메뉴를 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/service-bus-automation-menu.png)

1. **작업 창에서** 작업 템플릿을 선택할 수 있도록 **작업 추가** 를 선택 합니다.

   !["작업 추가"가 선택 된 "작업 (미리 보기)" 창을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/add-replication-task.png)

1. **작업 추가** 창의 **템플릿 선택** 에서 만들려는 복제 작업에 대 한 템플릿에서 **선택** 을 선택 합니다. 다음 페이지가 표시 되지 않으면 **다음: 인증** 을 선택 합니다.

   이 예는 Service Bus 큐 간에 콘텐츠를 복제 하는 **Service Bus 큐에 복제** 태스크 템플릿을 선택 하 여 계속 합니다.

   !["Service Bus에 복제" 템플릿이 선택 된 "작업 추가" 창을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/select-replicate-service-bus-template.png)

1. 모든 연결에 대 한 인증 자격 증명을 제공할 수 있도록 **인증** 탭의 **연결** 섹션에서 작업에 표시 되는 모든 연결에 대해 **만들기** 를 선택 합니다. 각 작업의 연결 유형은 작업에 따라 다릅니다.

   이 예에서는 대상 큐가 있는 대상 Service Bus 네임 스페이스에 대 한 연결을 만드는 프롬프트를 보여 줍니다. 원본 Service Bus 네임 스페이스에 대 한 연결이 이미 존재 합니다.

   ![대상 Service Bus 네임 스페이스에 대 한 연결에 대해 선택 된 "만들기" 옵션을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/create-authenticate-connections.png)

1. 대상에 대 한 필요한 정보를 제공 하 고 **만들기** 를 선택 합니다.

   이 예에서는 연결에 대 한 표시 이름을 제공한 다음 대상 큐가 있는 Service Bus 네임 스페이스를 선택 합니다.

   ![지정 된 연결 표시 이름 및 Service Bus 네임 스페이스가 선택 된 "커넥트" 창을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/connect-target-service-bus-namespace.png)

   > [!TIP]
   > 대신 연결 문자열을 사용 하 여 연결을 만들 수도 있습니다. 이 옵션을 사용 하면 다른 구독에 대상을 지정할 수 있으므로 구독 간 복제를 설정할 수 있습니다. 복제 작업을 만든 위치를 기반으로 하는 대상 또는 원본은 동적으로 구성 되므로 대상만 연결 하면 됩니다. 연결 문자열을 사용 하려면 다음 단계를 사용 합니다.
   >
   > 1. **커넥트** 창에서 **연결 문자열을 통해 커넥트** 를 선택 합니다.
   >
   > 2. **연결 문자열** 상자에 대상 네임 스페이스에 대 한 연결 문자열을 입력 합니다.

   다음 예에서는 성공적으로 생성 된 연결을 보여 줍니다.

   ![Service Bus 네임 스페이스에 대 한 연결이 완료 된 "태스크 추가" 창을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/connected-service-bus-namespaces.png)

1. 모든 연결을 완료 한 후 **다음: 구성** 을 선택 합니다.

1. **구성** 탭에서 작업에 필요한 작업 이름과 기타 정보를 제공 합니다.

   > [!NOTE]
   > 작업 이름은 만든 후에 변경할 수 없으므로 [기본 워크플로를 편집](#edit-task-workflow)하는 경우에도 계속 적용되는 이름을 고려해야 합니다. 기본 워크플로에 대한 변경 내용은 작업 템플릿이 아니라 사용자가 만든 작업에만 적용됩니다.
   >
   > 예를 들어 작업 이름을 지정할 때 `fabrikam-rep-weu-wus` 나중에 다른 용도로 기본 워크플로를 편집 하는 경우 작업 이름을 일치 하도록 변경할 수 없습니다.

   1. 기존 논리 **앱 (표준)** 리소스에 작업 워크플로를 추가 하려면 **논리 앱** 목록에서 기존 논리 앱을 선택 합니다. 대신 새 **논리 앱 (표준)** 리소스를 만들려면 **논리 앱** 목록에서 **새로 만들기** 를 선택 하 고 새 논리 앱에 사용할 이름을 제공 합니다.

      > [!NOTE]
      > 복제 태스크를 만드는 동안 새 논리 앱 리소스를 만드는 경우 논리 앱은 *원본 엔터티와 동일한 지역* 에 생성 됩니다 .이는 원본 지역을 사용할 수 없게 되 고 장애 조치 (failover) 시나리오에서 작동 하지 않는 경우 문제가 됩니다. 가장 좋은 방법은 원본과 다른 지역에 **논리 앱 (표준)** 리소스를 만드는 것입니다. 복제 작업을 만들 때 기존 논리 앱을 대신 선택 하 고 기본 상태 비저장 워크플로를 기존 논리 앱에 추가 합니다. 자세한 내용은 [필수 구성 요소](#prerequisites)를 참조 하세요.

   1. 완료되면 **검토 + 만들기** 를 선택합니다.

   ![작업 이름, 원본 및 대상 큐 이름, 논리 앱 리소스에 사용할 이름 등 복제 태스크 정보를 포함 하는 "작업 추가" 창을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/configure-replication-task.png)

1. **검토 + 만들기** 탭에서 작업에 대 한 복제 작업에 필요한 Azure 리소스를 확인 합니다.

   - 복제 작업에 대 한 새 논리 앱 리소스를 만들도록 선택한 경우에는 복제 태스크가 작동 하기 위해 만드는 데 필요한 Azure 리소스가 창에 표시 됩니다. 예를 들어 이러한 리소스에는 논리 앱 리소스, 워크플로 및 기타 런타임 작업에 대 한 구성 정보를 포함 하는 Azure Storage 계정이 포함 되어 있습니다. 예를 들어 Event Hubs 사용 하는 경우이 저장소 계정에는 원본 영역이 중단 되거나 사용할 수 없게 될 경우 원본 엔터티가 중지 되는 스트림의 위치 또는 *오프셋* 및 검사점 정보가 포함 됩니다.

     다음 예에서는 새 논리 앱을 만들도록 선택한 경우 **검토 + 만들기** 탭을 보여 줍니다.

     ![새 논리 앱을 만들 때 리소스 정보를 포함 하는 "검토 + 만들기" 창을 보여 주는 스크린샷](./media/create-replication-tasks-azure-resources/validate-replication-task-new-logic-app.png)

   - 복제 작업에 대 한 기존 논리 앱 리소스를 다시 사용 하기로 선택한 경우이 창에는 복제를 사용 하 여 작동 하는 데 사용 되는 Azure 리소스가 표시 됩니다.

     다음 예에서는 기존 논리 앱을 다시 사용 하도록 선택한 경우 **검토 + 만들기** 탭을 보여 줍니다.

     ![기존 논리 앱을 다시 사용 시 리소스 정보가 있는 "검토 + 만들기" 창을 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/validate-replication-task-existing-logic-app.png)

   > [!NOTE]
   > 원본, 대상 또는 둘 다 가상 네트워크 뒤에 있는 경우 작업을 만든 후 권한 및 액세스를 설정해야 합니다. 이 시나리오에서는 논리 앱 워크플로가 복제 작업을 수행할 수 있도록 권한 및 액세스 권한이 필요합니다.

1. 준비가 되면 **만들기** 를 선택합니다.

   자동으로 라이브로 실행 중인 만든 작업이 이제 **작업** 목록에 표시됩니다.

   > [!TIP]
   > 작업이 즉시 표시되지 않으면 작업 목록을 새로 고치거나 잠시 기다렸다가 새로 고칩니다. 도구 모음에서 **새로 고침** 을 선택합니다.

   ![만든 복제 작업이 있는 "작업" 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/created-replication-task.png)

1. 리소스가 가상 네트워크 뒤에 있는 경우 논리 앱 리소스 및 워크플로에서 해당 리소스에 액세스할 수 있는 권한을 설정해야 합니다.

## <a name="set-up-retry-policy"></a>재시도 정책 설정

복제 관계의 어느 쪽에서든 가용성 이벤트 중 데이터 손실을 방지하려면 견고성을 위해 재시도 정책을 구성해야 합니다. 복제 태스크에 대한 재시도 정책을 구성하려면 [Azure Logic Apps 재시도 정책에 대한 설명서와](logic-apps-exception-handling.md#retry-policies) [기본 워크플로를 편집하는](#edit-task-workflow)단계를 검토합니다.

<a name="review-task-history"></a>

## <a name="review-task-history"></a>작업 기록 검토

이 예제에서는 해당 상태, 입력, 출력 및 기타 정보와 함께 워크플로 실행 태스크의 기록을 보고 Service Bus 큐 복제 태스크에 대한 예제를 계속 사용하는 방법을 보여줍니다.

1. [Azure Portal](https://portal.azure.com)검토하려는 작업 기록이 있는 Azure 리소스 또는 엔터티를 찾습니다.

   이 예제에서 이 리소스는 Service Bus 네임스페이스입니다.

1. 리소스 탐색 메뉴의 **설정** 아래에서 **Automation** 섹션에서 **작업(미리 보기)** 을 선택합니다.

1. **작업** 창에서 검토하려는 작업을 찾습니다. 해당 작업의 **실행** 열에서 **보기** 를 선택합니다.

   !["작업" 창, 복제 작업 및 선택한 "보기" 옵션을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-runs-for-task.png)

   이 단계에서는 표준 논리 앱 리소스에 포함된 기본 상태 비저장 워크플로에 대한 **개요** 창을 엽니다.

1. 상태 비활성 워크플로에 대한 실행 기록을 보려면 **개요** 창 도구 모음에서 **디버그 모드 사용을** 선택합니다.

   **실행 기록** 탭에는 해당 식별자, 상태, 시작 시간 및 실행 기간과 함께 태스크에 대한 모든 이전 진행 중 및 대기 중인 실행이 표시됩니다.

   ![작업의 실행, 해당 상태 및 기타 정보를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/run-history-list.png)

   다음 표에서는 실행의 가능한 상태를 설명합니다.

   | 상태 | Description |
   |--------|-------------|
   | **취소** | 실행 중 작업이 취소되었습니다. |
   | **실패** | 작업에 실패한 하나 이상의 동작이 있지만 실패를 처리하기 위한 후속 동작이 없었습니다. |
   | **실행 중** | 작업이 현재 실행되고 있습니다. |
   | **성공함** | 모든 작업에 성공했습니다. 동작이 실패한 경우에도 작업이 성공적으로 완료될 수 있지만 실패를 처리하기 위한 후속 동작이 있었습니다. |
   | **대기 중** | 작업의 이전 인스턴스가 아직 실행 중이므로 실행이 아직 시작되지 않았으며 일시 중지되었습니다. |
   |||

1. 실행의 각 단계에 대한 상태 및 기타 정보를 보려면 해당 실행을 선택합니다.

   실행 세부 정보 창이 열리고 실행된 기본 워크플로가 표시됩니다.

   - 워크플로는 항상 [*트리거*](../connectors/apis-list.md#triggers)로 시작합니다. 이 작업의 경우 워크플로는 메시지가 원본 Service Bus 큐에 도착할 때까지 기다리는 Service Bus 트리거로 시작합니다.

   - 각 단계에는 상태 및 실행 기간이 표시됩니다. 기간이 0초인 단계를 실행하는 데 1초 미만이 걸렸습니다.

   ![워크플로의 실행, 상태 및 실행 기간의 각 단계를 보여주는 스크린샷](./media/create-replication-tasks-azure-resources/run-history-details.png)

1. 각 단계에 대한 입력 및 출력을 검토하려면 단계를 선택합니다. 이 단계를 선택하면 해당 단계에 대한 입력, 출력 및 속성 세부 정보를 표시하는 창이 열립니다.

   이 예제에서는 Service Bus 트리거에 대한 입력을 보여줍니다.

   ![트리거 입력, 출력 및 속성을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-trigger-inputs-outputs-properties.png)

Azure 리소스에 대한 복제 작업의 컨텍스트와 별도로 앱, 데이터, 서비스 및 시스템을 통합할 수 있도록 자체 자동화된 워크플로를 빌드하는 방법을 알아보려면 [Azure Portal 단일 테넌트 Azure Logic Apps(표준)를](create-single-tenant-workflows-azure-portal.md)사용하여 통합 워크플로 만들기를 검토하세요.

<a name="monitor"></a>

## <a name="monitor-replication-tasks"></a>복제 작업 모니터링

복제 태스크 또는 기본 논리 앱 워크플로의 성능 및 상태를 확인하려면 Azure Monitor 기능인 [Application Insights](../azure-monitor/app/app-insights-overview.md)를 사용할 수 있습니다. [애플리케이션 Insights 애플리케이션 맵은](../azure-monitor/app/app-map.md) 복제 작업을 모니터링하는 데 사용할 수 있는 유용한 시각적 도구입니다. 이 맵은 캡처된 모니터링 정보에서 자동으로 생성되므로 복제 작업 원본 및 대상 전송의 성능과 안정성을 살펴볼 수 있습니다. 즉각적인 진단 인사이트 및 로그 세부 정보의 짧은 대기 시간 시각화를 위해 [라이브 메트릭](../azure-monitor/app/live-stream.md) 포털 도구와 Azure Monitor 기능을 사용할 수 있습니다.

<a name="edit-task"></a>

## <a name="edit-the-task"></a>작업 편집

작업을 변경하려면 다음 옵션을 사용할 수 있습니다.

- 연결 정보 또는 구성 정보와 같은 태스크의 속성을 변경할 수 있도록 태스크 ["인라인"을 편집합니다.](#edit-task-inline)

- [디자이너에서 작업의 기본 워크플로를 편집합니다.](#edit-task-workflow)

<a name="edit-task-inline"></a>

### <a name="edit-the-task-inline"></a>"인라인"으로 작업 편집

1. [Azure Portal](https://portal.azure.com)에서 업데이트하려는 작업이 있는 리소스를 찾습니다.

1. 리소스 탐색 메뉴의 **Automation** 섹션에서 **작업(미리 보기)** 을 선택합니다.

1. 작업 목록에서 업데이트하려는 작업을 찾습니다. 작업의 줄임표( **...** ) 메뉴를 열고, **인라인 편집** 을 선택합니다.

   ![열린 줄임표 메뉴와 선택한 옵션인 "인라인 편집"을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-task-in-line.png)

   기본적으로 **인증** 탭이 나타나고 기존 연결이 표시됩니다.

1. 새 인증 자격 증명을 추가하거나 연결에 대해 다른 기존 인증 자격 증명을 선택하려면 연결의 줄임표( **...** ) 메뉴를 열고, **새 연결 추가** 를 선택하거나 사용 가능한 경우 다른 인증 자격 증명을 선택합니다.

   > [!NOTE]
   > 원본 연결이 아닌 대상 연결만 편집할 수 있습니다.

   !["인증" 탭, 기존 연결 및 선택한 타원 메뉴를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-connections.png)

1. 다른 작업 속성을 업데이트하려면 **다음: 구성을** 선택합니다.

   이 예제의 작업에 대해 다른 원본 및 대상 큐를 지정할 수 있습니다. 그러나 작업 이름과 기본 논리 앱 및 워크플로는 동일하게 유지됩니다.

   ![편집할 수 있는 "구성" 탭 및 속성을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-task-configuration.png)

1. 완료되면 **저장** 을 선택합니다.

<a name="edit-task-workflow"></a>

### <a name="edit-the-tasks-underlying-workflow"></a>작업의 기본 워크플로 편집

복제 작업 뒤에 있는 기본 워크플로를 편집할 수 있습니다. 이렇게 하면 만든 태스크에 대한 원래 구성이 변경되지만 작업 템플릿 자체는 변경할 수 없습니다. 변경 내용을 저장한 후에는 편집된 작업이 더 이상 원래 작업과 동일한 기능을 수행하지 않습니다. 원래 기능을 수행하는 작업을 원하는 경우 동일한 템플릿을 사용하여 새 작업을 만들어야 할 수 있습니다. 원래 작업을 다시 만들지 않으려면 디자이너를 사용하여 작업 뒤에 있는 워크플로를 변경하지 마세요. 대신 통합 요구 사항에 맞게 **논리 앱(표준)** 상태 비지정 워크플로를 만듭니다. 자세한 내용은 [Azure Portal 단일 테넌트 Azure Logic Apps(표준)를 사용하여 통합 워크플로 만들기를](create-single-tenant-workflows-azure-portal.md)참조하세요.

1. [Azure Portal](https://portal.azure.com)에서 업데이트하려는 작업이 있는 리소스를 찾습니다.

1. 리소스 탐색 메뉴의 **Automation** 섹션에서 **작업을** 선택합니다.

1. 작업 목록에서 업데이트하려는 작업을 찾습니다. 작업의 줄임표( **...** ) 메뉴를 열고, **Logic Apps에서 열기** 를 선택합니다.

   ![열린 타원 메뉴와 선택한 옵션인 "Logic Apps 열기"를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/open-task-in-designer.png)

   Azure Portal 이제 워크플로를 편집할 수 있는 디자이너로 컨텍스트를 변경합니다.

   ![디자이너 및 기본 워크플로를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-task-workflow-designer.png)

   이제 트리거 및 작업에 대한 속성뿐만 아니라 워크플로의 트리거 및 작업을 편집할 수 있습니다.

1. 트리거 또는 작업에 대한 속성을 보려면 해당 트리거 또는 작업을 선택합니다.

   ![Service Bus 트리거 속성 창을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/edit-service-bus-trigger.png)

   이 예제에서는 트리거의 **IsSessionsEnabled** 속성이 **예** 로 변경됩니다.

1. 변경 내용을 저장하려면 디자이너 도구 모음에서 **저장** 을 선택합니다.

   ![디자이너 도구 모음 및 선택한 "저장" 명령을 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/save-updated-workflow.png)

1. 업데이트된 워크플로를 테스트하고 실행하려면 업데이트된 워크플로가 포함된 논리 앱 리소스를 엽니다. 워크플로 탐색 메뉴에서 **개요**  >  **실행 트리거**  >  **실행을** 선택합니다.

   실행이 완료되면 디자이너에서 워크플로의 실행 세부 정보를 표시합니다. 각 단계에 대한 입력 및 출력을 검토하려면 단계를 선택합니다. 이 단계를 선택하면 해당 단계에 대한 입력, 출력 및 속성 세부 정보를 표시하는 창이 열립니다.

   이 예제에서는 업데이트된 트리거 속성 값과 함께 선택한 Service Bus 트리거의 입력, 출력 및 속성을 보여줍니다.

   ![트리거의 입력, 출력 및 속성이 있는 워크플로의 실행 세부 정보를 보여주는 스크린샷.](./media/create-replication-tasks-azure-resources/view-updated-run-details-trigger-inputs.png)

1. 작업이 계속 실행되지 않도록 워크플로를 사용하지 않도록 설정하려면 **개요** 도구 모음에서 **사용 안 함을** 선택합니다. 자세한 내용은 [단일 테 넌 트 워크플로 사용 또는 사용 안 함](create-single-tenant-workflows-azure-portal.md#disable-or-enable-workflows)을 참조 하세요.

<a name="failover"></a>

## <a name="set-up-failover-for-azure-event-hubs"></a>Azure Event Hubs에 대 한 장애 조치 설정

동일한 엔터티 형식 간의 Azure Event Hubs 복제의 경우 지역 재해 복구에서는 원본 엔터티에서 대상 엔터티로 장애 조치 (failover)를 수행 하 고, 영향을 받는 이벤트 소비자 및 생산자에 게 대상 엔터티에 대 한 끝점을 사용 하도록 지시 합니다 .이는 새 원본이 됩니다. 따라서 재해가 발생 하 고 원본 엔터티가 장애 조치 (failover) 되 면 복제 작업을 비롯 한 소비자 및 생산자가 새 원본으로 리디렉션됩니다. 복제 작업에 의해 만들어진 저장소 계정 계정에는 원본 지역이 중단 되거나 사용할 수 없게 될 경우 원본 엔터티가 중지 되는 스트림의 위치 또는 오프셋 및 검사점 정보가 포함 되어 있습니다.

저장소 계정이 원래 원본에서 레거시 정보를 포함 하지 않도록 하 고 복제 태스크가 새 원본 스트림의 시작 부분에서 이벤트를 읽고 복제 하기 시작 하 게 하려면 복제 작업을 수동으로 다시 구성 해야 합니다.

1. [Azure Portal](https://portal.azure.com)에서 논리 앱 리소스 또는 복제 작업 뒤의 기본 워크플로를 엽니다.

   > [!NOTE]
   > 논리 앱 리소스는 복제 작업 워크플로만 포함 해야 합니다.

1. 리소스 또는 워크플로의 탐색 메뉴에서 **개요** 를 선택 합니다. **개요** 도구 모음에서 워크플로에 대해 **사용 안 함** 을 선택 하거나 논리 앱 리소스에 대해 **중지** 를 선택 합니다.

1. 복제 태스크 리소스를 포함 하는 Azure 리소스 그룹으로 이동 합니다.

   이 리소스 그룹에는 원본 엔터티에서 검사점 및 스트림 오프셋 정보를 저장 하는 논리 앱 리소스 및 저장소 계정이 포함 되어 있습니다.

1. 논리 앱 리소스와 연결 된 저장소 계정으로 이동 합니다. 이 저장소 계정을 찾으려면 논리 앱 리소스를 포함 하는 리소스 그룹을 엽니다. 다음 단계를 수행 하 여 저장소 계정을 삭제 합니다.

   1. 저장소 계정 탐색 메뉴의 **데이터 저장소** 에서 **컨테이너** 를 선택 합니다.

   1. 열리는 **컨테이너** 창에서 Event Hubs 원본에 대해 **azure-webjobs-eventhub** 를 선택 합니다.

      > [!NOTE]
      > **Webjobs-eventhub** 항목이 존재 하지 않는 경우 작업을 한 번 이상 실행 해야 합니다.

   1. **Webjobs-eventhub** 창에서 다음 형식의 이름을 가진 네임 스페이스 폴더를 선택 `<source-event-hub-name>.servicebus.windows.net` 합니다.

   1. 네임 스페이스 폴더에서 이전 원본 엔터티에 대 한 폴더를 삭제 합니다. 이 폴더는 이전 소스에 대 한 검사점 및 오프셋 정보를 포함 하며 일반적으로 해당 원본의 이름을 포함 합니다.

1. 복제 작업 뒤에 있는 논리 앱 리소스 또는 워크플로로 돌아갑니다. 논리 앱을 다시 시작 하거나 워크플로를 다시 사용 하도록 설정 하세요.

생산자와 소비자가 새 소스 끝점을 사용 하도록 하려면 새 원본 엔터티에 대 한 정보를 사용 하 여 쉽게 연결 하 고 업데이트 하는 위치에서 찾을 수 있어야 합니다. 생산자 나 소비자가 자주 발생 하거나 지속적으로 오류가 발생 하는 경우 해당 위치를 확인 하 고 구성을 조정 해야 합니다. 이 구성을 공유 하는 방법에는 여러 가지가 있지만 DNS 및 파일 공유는 예입니다.

지역 재해 복구에 대 한 자세한 내용은 다음 설명서를 검토 하세요.

- [Azure Event Hubs - 지리적 재해 복구](../event-hubs/event-hubs-geo-dr.md)
- [Azure Service Bus-지역 재해 복구](../service-bus-messaging/service-bus-geo-dr.md)

<a name="problems-failures"></a>

## <a name="replication-problems-and-failures"></a>복제 문제 및 오류

이 섹션에서는 복제가 실패 하거나 작동을 중지할 수 있는 가능한 방법을 설명 합니다.

- 메시지 크기 제한

  복제 태스크가 [복제 속성](#replication-properties)을 추가 하므로 1mb 보다 작은 메시지를 전송 해야 합니다. 그렇지 않고 메시지 크기가 태스크에서 [복제 속성](#replication-properties)을 추가한 후에 Event Hubs 엔터티로 전송할 수 있는 이벤트 크기 보다 크면 복제 프로세스가 실패 합니다.

  예를 들어 이벤트 크기는 1mb입니다. 태스크가 복제 속성을 추가 하면 메시지 크기는 1mb 보다 큽니다. 메시지를 보내려고 시도 하는 아웃 바운드 호출이 실패 합니다.

- 파티션 키

  이벤트에 파티션 키가 있는 경우 해당 인스턴스에 동일한 수의 파티션이 있으면 Event Hubs 인스턴스 간 복제가 실패 합니다.

## <a name="next-steps"></a>다음 단계

- [단일 테넌트 Azure Logic Apps의 워크플로 디자이너 정보](designer-overview.md)
- [단일 테넌트 Azure Logic Apps에서 논리 앱에 대한 호스트 및 앱 설정 편집](edit-app-settings-host-settings.md)
- [단일 테넌트 Azure Logic Apps의 환경에 있는 워크플로에서 사용할 매개 변수 만들기](parameterize-workflow-app.md)
