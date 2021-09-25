---
title: 여러 인스턴스의 파티션 부하 분산 - Azure Event Hubs | Microsoft Docs
description: 이벤트 프로세서와 Azure Event Hubs SDK를 사용하여 애플리케이션의 여러 인스턴스의 파티션 부하를 분산하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 09/15/2021
ms.openlocfilehash: 9ddccf27ad12759cb502a969440cff3b81478389
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128676716"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>애플리케이션의 여러 인스턴스의 파티션 부하 분산
이벤트 처리 애플리케이션의 크기를 조정하기 위해 애플리케이션의 여러 인스턴스를 실행하고 부하를 분산하도록 할 수 있습니다. 이전 버전에서는 [EventProcessorHost](event-hubs-event-processor-host.md)를 사용하여 프로그램의 여러 인스턴스와 수신되는 검사점 이벤트 간의 부하를 분산할 수 있었습니다. 최신 버전(5.0 이상)에서는 **EventProcessorClient**(.NET 및 Java) 또는 **EventHubConsumerClient**(Python 및 JavaScript)를 사용하여 동일한 작업을 수행할 수 있습니다. 이벤트를 사용하여 개발 모델을 더 간단하게 할 수 있습니다. 이벤트 처리기를 등록하여 관심 있는 이벤트를 구독합니다. 이전 버전의 클라이언트 라이브러리를 사용하는 경우 [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md), [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/migration-guide.md), [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/servicebus/azure-servicebus/migration_guide.md) 및 [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/servicebus/service-bus/migrationguide.md) 마이그레이션 가이드를 참조하세요.

이 문서에서는 여러 인스턴스를 사용하여 이벤트 허브에서 이벤트를 읽은 다음 이벤트 프로세서 클라이언트의 기능에 대한 세부 정보를 제공하는 샘플 시나리오를 설명합니다. 이를 통해 여러 파티션에서 한 번에 이벤트를 수신하고 동일한 이벤트 허브 및 소비자 그룹을 사용하는 다른 소비자와 부하를 분산할 수 있습니다.

> [!NOTE]
> Event Hubs의 크기를 조정하는 핵심은 분할된 소비자라는 개념입니다. [경쟁하는 소비자](/previous-versions/msp-n-p/dn568101(v=pandp.10)) 패턴과 달리 분할된 소비자 패턴을 사용하면 경합 병목 상태를 제거하고 종단 간 병렬 처리를 용이하게 하여 대규모 크기 조정이 가능해집니다.

## <a name="example-scenario"></a>예제 시나리오

예제 시나리오로 100,000호의 집을 모니터링하는 보안 회사를 가정합니다. 1분마다 각 집에 설치된 다양한 센서(예: 동작 탐지기, 도어/창 열기 센서, 유리 파손 감지기 등)의 데이터를 가져옵니다. 회사는 거주자가 거의 실시간으로 해당 집의 활동을 모니터링하는 웹 사이트를 제공합니다.

각 센서는 데이터를 이벤트 허브로 푸시합니다. 이벤트 허브는 16개의 파티션으로 구성됩니다. 소비 측에서는 이러한 이벤트를 읽어 통합(필터링, 집계 등)하고 해당 집계를 스토리지 Blob에 덤프한 다음 사용자 친화적인 웹 페이지에 프로젝션할 수 있는 메커니즘이 필요합니다.

## <a name="write-the-consumer-application"></a>소비자 애플리케이션 작성

분산된 환경에서 소비자를 디자인할 때 시나리오는 다음 요구 사항을 처리해야 합니다.

1. **크기 조정:** 몇몇 Event Hubs 파티션에서 읽기 소유권을 가진 각 소비자를 사용하여 여러 소비자를 만듭니다.
2. **부하 분산:** 소비자를 동적으로 증가시키거나 감소시킵니다. 예를 들어, 새 센서 형식(예: 일산화 탐지기)이 각 집에 추가되면 이벤트 수가 증가합니다. 이 경우에 연산자(사람)는 소비자 인스턴스 수를 증가시킵니다. 그런 다음, 소비자 풀은 소유한 파티션의 수를 다시 조정하여 새로 추가된 소비자와 관련된 부하를 공유할 수 있습니다.
3. **오류에서 원활한 다시 시작:** 소비자(**소비자 A**)가 실패하면(예: 소비자를 호스트하는 가상 머신이 갑자기 충돌함) 다른 소비자는 **소비자 A** 가 소유한 파티션을 선택한 다음, 계속할 수 있습니다. 또한 *검사점* 또는 *오프셋* 이라는 연속 지점은 **소비자 A** 가 실패한 정확한 지점 또는 약간 앞에 위치해야 합니다.
4. **이벤트 소비:** 이전의 세 가지 포인트는 소비자 관리를 다루지만 이벤트를 소비하고 유용한 작업을 수행하는 코드가 있어야 합니다. 예를 들어 이벤트를 집계하고 Blob Storage에 업로드합니다.

## <a name="event-processor-or-consumer-client"></a>이벤트 프로세서 또는 소비자 클라이언트

이러한 요구 사항을 충족하기 위해 솔루션을 직접 빌드할 필요는 없습니다. Azure Event Hubs SDK는 이 기능을 제공합니다. .NET 또는 Java SDK에서는 이벤트 프로세서 클라이언트(EventProcessorClient)를 사용하고 Python 및 JavaScript SDK에서는 EventHubConsumerClient를 사용합니다. 이전 버전의 SDK에서는 이러한 기능을 지원하는 이벤트 프로세서 호스트(EventProcessorHost)였습니다.

대부분의 프로덕션 시나리오에서는 이벤트 프로세서 클라이언트를 사용하여 이벤트를 읽고 처리하는 것이 좋습니다. 프로세서 클라이언트는 진행 상태의 검사점을 확인하는 수단을 제공하면서 성능 및 내결함성 방식으로 이벤트 허브의 모든 파티션에서 이벤트를 처리하기 위한 강력한 환경을 제공하기 위한 것입니다. 이벤트 프로세서 클라이언트는 지정된 이벤트 허브에 대한 소비자 그룹의 컨텍스트 내에서 협조적으로 작동할 수 있습니다. 그룹에 대해 인스턴스를 사용할 수 있거나 사용할 수 없게 되면 클라이언트는 작업의 배포 및 부하 분산을 자동으로 관리합니다.

## <a name="partition-ownership-tracking"></a>파티션 소유권 추적

일반적으로 이벤트 프로세서 인스턴스는 하나 이상의 파티션에서 이벤트를 소유하고 처리합니다. 파티션 소유권은 이벤트 허브 및 소비자 그룹 조합과 관련된 모든 활성 이벤트 프로세서 인스턴스에 균등하게 배포됩니다. 

각 이벤트 프로세서에는 고유한 식별자가 부여되고 검사점 저장소에서 항목을 추가하거나 업데이트하여 파티션 소유권이 청구됩니다. 모든 이벤트 프로세서 인스턴스는 이 저장소와 주기적으로 통신하여 자체 처리 상태를 업데이트하고 다른 활성 인스턴스에 대해 학습합니다. 그런 다음 이 데이터를 사용하여 활성 프로세서 간에 부하를 분산합니다. 새 인스턴스는 처리 풀에 조인하여 확장할 수 있습니다. 오류가 발생하거나 규모를 축소하여 인스턴스가 중단되면 파티션 소유권이 다른 활성 프로세서로 정상적으로 전송됩니다.

검사점 저장소의 파티션 소유권 레코드는 Event Hubs 네임스페이스, 이벤트 허브 이름, 소비자 그룹, 이벤트 프로세서 식별자(소유자라고도 함), 파티션 ID 및 마지막 수정 시간을 추적합니다.



| Event Hubs 네임스페이스               | 이벤트 허브 이름 | **소비자 그룹** | 소유자                                | Partition ID | 마지막으로 수정한 시간  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

각 이벤트 프로세서 인스턴스는 파티션의 소유권을 획득하고 마지막으로 알려진 [검사점](# Checkpointing)에서 파티션 처리를 시작합니다. 프로세서가 실패(VM 종료)하는 경우 다른 인스턴스가 마지막으로 수정한 시간을 확인하여 검색합니다. 다른 인스턴스는 비활성 인스턴스가 이전에 소유한 파티션의 소유권을 얻으려고 시도하고 검사점 저장소는 인스턴스 중 하나만 파티션의 소유권을 주장하는 데 성공하도록 보장합니다. 따라서 특정 시점에 파티션에서 이벤트를 수신하는 프로세서는 최대 하나입니다.

## <a name="receive-messages"></a>메시지 받기

이벤트 프로세서를 만들 때 이벤트와 오류를 처리할 함수를 지정합니다. 이벤트를 처리하는 함수를 호출할 때마다 특정 파티션에서 단일 이벤트를 전달합니다. 이 이벤트를 처리하는 것은 사용자의 책임입니다. 소비자가 모든 메시지를 한 번 이상 처리하도록 하려면 다시 시도 논리를 사용하여 사용자 고유의 코드를 작성해야 합니다. 하지만 포이즌 메시지에 대해 주의해야 합니다.

작업을 상대적으로 빠르게 수행하는 것이 좋습니다. 즉, 가능한 대로 적게 처리합니다. 스토리지에 기록하고 일부 라우팅을 수행해야 하는 경우 두 개의 이벤트 프로세서를 가지고 두 개의 소비자 그룹을 사용하는 것이 좋습니다.

## <a name="checkpointing"></a>검사점 설정

*검사점* 은 이벤트 프로세서가 파티션 내에서 마지막으로 처리된 이벤트의 위치를 표시하거나 커밋하는 프로세스입니다. 검사점 표시는 일반적으로 소비자 그룹 내에서 이벤트를 처리하고 파티션마다 발생하는 함수에서 수행됩니다. 

이벤트 프로세서가 파티션에서 연결을 끊으면 다른 인스턴스는 해당 소비자 그룹에서 파티션의 마지막 프로세서에 의해 이전에 커밋된 검사점에 파티션 처리를 다시 시작할 수 있습니다. 프로세서가 연결하면, 오프셋을 이벤트 허브로 전달하여 읽기 시작할 위치를 지정합니다. 이러한 방식으로 검사점을 사용하여 다운스트림 애플리케이션에서 이벤트를 "완료"로 표시하고 이벤트 프로세서가 작동 중지될 때 탄력성을 제공할 수 있습니다. 이 검사점 프로세스에서 더 낮은 오프셋을 지정하면 이전 데이터로 돌아갈 수 있습니다. 

이벤트를 처리된 것으로 표시하기 위해 검사점을 수행하면 검사점 저장소의 항목이 이벤트의 오프셋 및 시퀀스 번호를 사용하여 추가되거나 업데이트됩니다. 사용자는 검사점의 업데이트 빈도를 결정해야 합니다. 성공적으로 처리된 각 이벤트 후 업데이트는 기본 검사점 저장소에 대한 쓰기 작업을 트리거하는 성능 및 비용 영향을 가질 수 있습니다. 또한 모든 단일 이벤트의 검사점을 확인하면 Service Bus 큐가 이벤트 허브보다 더 나은 옵션이 될 수 있는 큐에 대기 중인 메시징 패턴이 나타납니다. Event Hubs의 기본 개념은 대규모로 "최소한 한 번" 제공받는다는 것입니다. 다운스트림 시스템을 멱등원(idempotent)으로 만들면 동일한 이벤트가 여러 번 수신되는 결과를 초래하는 실패 또는 다시 시작에서 쉽게 복구할 수 있습니다.

> [!NOTE]
> Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원하는 환경에서 검사점 저장소로 Azure Blob Storage를 사용하는 경우 코드를 사용하여 Storage 서비스 API 버전을 해당 환경에서 지원하는 특정 버전으로 변경해야 합니다. 예를 들어 [Azure Stack Hub 버전 2002의 Event Hubs](/azure-stack/user/event-hubs-overview)에서 실행 중인 경우 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우 코드를 사용하여 Storage 서비스 API 버전의 대상을 2017-11-09로 해야 합니다. 특정 Storage API 버전을 대상으로 지정하는 방법에 대한 예제는 GitHub에 대한 다음 샘플을 참조하세요. 
> - [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
> - [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/)
> - [JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript) 또는 [TypeScript](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript)
> - [Python](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/)

## <a name="thread-safety-and-processor-instances"></a>스레드 안전성 및 프로세서 인스턴스

기본적으로 이벤트를 처리하는 함수는 지정된 파티션에 대해 순차적으로 호출됩니다. 이벤트 펌프가 다른 스레드의 백그라운드에서 계속 실행되므로 동일한 파티션에서 이 함수에 대한 후속 이벤트와 호출이 백그라운드로 큐에 대기합니다. 여러 파티션의 이벤트를 동시에 처리할 수 있으며 여러 파티션에서 액세스되는 모든 공유 상태가 동기화되어야 합니다.

## <a name="next-steps"></a>다음 단계
다음 빠른 시작을 참조하세요.

- [.NET Core](event-hubs-dotnet-standard-getstarted-send.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](event-hubs-python-get-started-send.md)
- [JavaScript](event-hubs-node-get-started-send.md)
