---
title: 지속성 함수의 성능 및 크기 조정 - Azure
description: Azure Functions용 Durable Functions 확장의 고유한 스케일링 특징에 관해 알아봅니다.
author: cgillum
ms.topic: conceptual
ms.date: 05/13/2021
ms.author: azfuncdf
ms.openlocfilehash: 179eb4c5c380a65374143cf24b2f960458fe62b5
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/16/2021
ms.locfileid: "112240439"
---
# <a name="performance-and-scale-in-durable-functions-azure-functions"></a>지속성 함수의 성능 및 크기 조정(Azure Functions)

성능 및 확장성을 최적화하려면 [지속성 함수](durable-functions-overview.md)의 고유한 크기 조정 특징을 이해하는 것이 중요합니다.

## <a name="azure-storage-provider"></a>Azure Storage 공급자

Durable Functions의 기본 구성은 이 런타임 상태를 Azure Storage(클래식) 계정에 저장합니다. 모든 함수 실행은 Azure Storage 큐에 의해 이루어집니다. 오케스트레이션과 엔터티 상태 및 기록은 Azure 테이블에 저장됩니다. Azure Blob 및 blob 임대는 여러 앱 인스턴스(작업자 또는 단순히 VM이라고도 함)에 오케스트레이션 인스턴스 및 엔터티를 배포하는 데 사용됩니다. 이 섹션에서는 다양한 Azure Storage 아티팩트와 이것이 성능 및 확장성에 영향을 주는 방식에 대해 자세히 설명합니다.

> [!NOTE]
> 이 문서에서는 기본 Azure Storage 공급자를 사용하는 Durable Functions의 성능 및 확장성 특성을 주로 다룹니다. 그러나 다른 스토리지 공급자도 사용할 수 있습니다. Durable Functions에 지원되는 스토리지 공급자 및 비교 방법에 대한 자세한 내용은 [Durable Functions 스토리지 공급자](durable-functions-storage-providers.md) 설명서를 참조하세요.

### <a name="history-table"></a>기록 테이블

**기록** 테이블은 작업 허브 내의 모든 오케스트레이션 인스턴스에 대한 기록 이벤트가 포함된 Azure Storage 테이블입니다. 이 테이블의 이름은 *TaskHubName* History 형태를 갖습니다. 인스턴스가 실행되면 새 행이 이 테이블에 추가됩니다. 이 테이블의 파티션 키는 오케스트레이션의 인스턴스 ID에서 파생됩니다. 기본적으로 인스턴스 ID는 임의이며, Azure Storage 내부 파티션을 최적으로 배포할 수 있도록 합니다. 이 테이블의 행 키는 기록 이벤트의 순서를 지정하는 데 사용되는 시퀀스 번호입니다.

오케스트레이션 인스턴스를 실행해야 하는 경우 기록 테이블의 해당 행은 단일 테이블 파티션 내의 범위 쿼리를 사용하여 메모리에 로드됩니다. 그런 다음, 이러한 *기록 이벤트* 는 오케스트레이터 함수 코드로 재생되어 이전의 검사점 상태로 돌아갑니다. 이러한 방식으로 상태를 다시 작성하기 위해 실행 기록을 사용하게 되면 [이벤트 소싱 패턴](/azure/architecture/patterns/event-sourcing)의 영향을 받습니다.

> [!TIP]
> 기록 테이블에 저장된 오케스트레이션 데이터는 작업 및 하위 오케스트레이터 함수의 출력 페이로드를 포함합니다. 외부 이벤트의 페이로드는 기록 테이블에도 저장됩니다. 오케스트레이터를 실행해야 할 때마다 전체 기록이 메모리에 로드되기 때문에 기록이 충분히 많으면 지정된 VM에서 상당한 메모리 부담이 발생할 수 있습니다. 대규모 오케스트레이션을 여러 하위 오케스트레이션으로 분할하거나 호출하는 작업 및 하위 오케스트레이터 함수에 의해 반환되는 출력의 크기를 줄여 오케스트레이션 기록의 길이와 크기를 줄일 수 있습니다. 또는 VM당 [동시성 제한](#concurrency-throttles)을 낮추어 메모리에 동시에 로드되는 오케스트레이션 수를 제한하면 메모리 사용량을 줄일 수 있습니다.

### <a name="instances-table"></a>인스턴스 테이블

**인스턴스** 테이블은 작업 허브 내의 모든 오케스트레이션 및 엔터티 인스턴스의 상태를 포함합니다. 인스턴스가 생성되면 새 행이 이 테이블에 추가됩니다. 이 테이블의 파티션 키는 오케스트레이션 인스턴스 ID 또는 엔터티 키이고 행 키는 비어 있는 문자열입니다. 오케스트레이션 또는 엔터티 인스턴스당 하나의 행이 있습니다.

테이블은 [코드의 인스턴스 쿼리 요청](durable-functions-instance-management.md#query-instances)과 [상태 쿼리 HTTP API](durable-functions-http-api.md#get-instance-status) 호출을 충족하는 데 사용됩니다. 결과적으로 앞서 언급된 **기록** 테이블의 내용과 일관되게 유지됩니다. 이러한 방식으로 인스턴스 쿼리 작업을 효과적으로 충족하기 위해 별도 Azure Storage 테이블을 사용하면 [CQRS(명령 및 쿼리 책임 분리) 패턴](/azure/architecture/patterns/cqrs)의 영향을 받습니다.

> [!TIP]
> *Instances* 테이블을 분할하면 런타임 성능 또는 규모에는 눈에 띄는 영향 없이 수백만 개의 오케스트레이션 인스턴스를 저장할 수 있습니다. 그러나 인스턴스 수는 [다중 인스턴스 쿼리](durable-functions-instance-management.md#query-all-instances) 성능에 상당한 영향을 줄 수 있습니다. 이러한 테이블에 저장된 데이터의 양을 제어하려면 [이전 인스턴스 데이터를 주기적으로 제거](durable-functions-instance-management.md#purge-instance-history)하는 것이 좋습니다.

### <a name="internal-queue-triggers"></a>내부 큐 트리거

오케스트레이터, 엔터티, 활동 함수는 모두 함수 앱의 작업 허브에 있는 내부 큐에서 트리거됩니다. 이러한 방식으로 큐를 사용하면 신뢰할 수 있는 "최소 한 번" 메시지 배달이 보장됩니다. 지속성 함수에는 **제어 큐** 및 **작업 항목 큐** 의 두 가지 유형의 큐가 있습니다.

#### <a name="the-work-item-queue"></a>작업 항목 큐

지속성 함수에는 작업 허브마다 하나의 작업 항목 큐가 있습니다. 이것은 기본 큐이며 Azure Functions의 다른 모든 `queueTrigger` 큐와 비슷하게 동작합니다. 이 큐는 한 번에 하나의 메시지를 큐에서 제거하여 상태 비저장 *작업 함수* 를 트리거하는 데 사용됩니다. 이러한 각 메시지는 작업 함수 입력 및 추가 메타데이터(예: 실행할 함수)를 포함합니다. Durable Functions 애플리케이션이 여러 VM으로 확장하는 경우 이러한 VM은 모두 작업 항목 큐에서 작업을 가져오려고 경쟁합니다.

#### <a name="control-queues"></a>제어 큐

지속형 함수의 작업 허브당 여러 개의 *제어 큐* 가 있습니다. *제어 큐* 는 간단한 작업 항목 큐보다 더 복잡합니다. 제어 큐는 상태 저장 오케스트레이터 및 엔터티 함수를 트리거하는 데 사용됩니다. 오케스트레이터 및 엔터티 함수 인스턴스는 상태 저장 싱글톤이므로, 각 오케스트레이션 또는 엔터티는 한 번에 한 작업자만 처리하는 것이 중요합니다. 이 제약 조건을 만족하기 위해 각 오케스트레이션 인스턴스나 엔터티는 단일 제어 큐에 할당됩니다. 이 제어 큐는 작업자 간에 부하를 분산하여 한 번에 한 작업자만 각 큐를 처리하게 합니다. 이 동작에 대한 자세한 내용은 이후 섹션을 참조하세요.

제어 큐에는 다양한 오케스트레이션 수명 주기 메시지 유형이 포함됩니다. 예를 들어 [오케스트레이터 제어 메시지](durable-functions-instance-management.md), 작업 함수 *응답* 메시지 및 타이머 메시지가 있습니다. 단일 폴링의 제어 큐에서 최대 32개의 메시지가 제거됩니다. 이러한 메시지는 의도한 오케스트레이션 인스턴스를 포함하는 메타데이터 뿐만 아니라 페이로드 데이터도 포함됩니다. 큐에서 제거된 메시지가 여러 개의 동일한 오케스트레이션 인스턴스를 위한 것이면 일괄로 처리됩니다.

컨트롤 큐 메시지는 백그라운드 스레드를 사용하여 지속적으로 폴링됩니다. 각 큐 폴링의 일괄 처리 크기는 host.json의 `controlQueueBatchSize` 설정에 의해 제어되며 기본값은 32(Azure 큐에서 지원하는 최댓값)입니다. 메모리에 버퍼링되는 프리페치 컨트롤 큐 메시지의 최대 수는 host.json의 `controlQueueBufferThreshold` 설정에 의해 제어됩니다. `controlQueueBufferThreshold`의 기본값은 호스팅 계획의 유형을 비롯한 다양한 요인에 따라 달라집니다. 이러한 설정에 대한 자세한 내용은 [host.json 스키마](../functions-host-json.md#durabletask) 설명서를 참조하세요.

> [!TIP]
> `controlQueueBufferThreshold`의 값을 늘리면 단일 오케스트레이션 또는 엔터티가 이벤트를 더 빠르게 처리할 수 있습니다. 그러나 이 값을 늘리면 메모리 사용량이 증가할 수도 있습니다. 메모리 사용량이 높아지면 큐에서 메시지를 더 많이 끌어오고, 일부는 더 많은 오케스트레이션 기록을 메모리로 가져오기 때문입니다. 따라서 `controlQueueBufferThreshold` 값을 줄이는 것이 메모리 사용을 줄이는 효과적인 방법이 될 수 있습니다.

#### <a name="queue-polling"></a>큐 폴링

지속형 작업 확장은 무작위 지수 백오프 알고리즘을 구현하여 유휴 큐 폴링이 스토리지 트랜잭션 비용에 미치는 영향을 줄입니다. 메시지가 발견되면 런타임이 다른 메시지를 즉시 확인합니다. 메시지가 발견되지 않으면 다시 시도하기 전에 잠시 대기합니다. 후속 시도로 큐 메시지를 가져오지 못하면 최대 대기 시간(기본값 30초)에 도달할 때까지 대기 시간이 계속 증가합니다.

최대 폴링 연기는 [host.json 파일](../functions-host-json.md#durabletask)의 `maxQueuePollingInterval` 속성을 통해 구성할 수 있습니다. 이 속성을 더 큰 값으로 설정하면 메시지 처리 대기 시간이 길어질 수 있습니다. 더 긴 대기 시간은 비활성 기간 후에만 발생할 것으로 예상됩니다. 이 속성을 낮은 값으로 설정하면 스토리지 트랜잭션이 증가하기 때문에 [스토리지 비용이 증가](durable-functions-billing.md#azure-storage-transactions)할 수 있습니다.

> [!NOTE]
> Azure Functions Consumption 및 Premium 플랜에서 실행하는 경우 [Azure Functions Scale Controller](../event-driven-scaling.md)는 10초마다 한 번씩 각 제어 및 작업 항목 큐를 폴링합니다. 이 추가 폴링은 함수 앱 인스턴스를 활성화할 시기와 스케일링을 결정하는 데 필요합니다. 작성할 때 이 10초 간격은 일정하며 구성할 수 없습니다.

#### <a name="orchestration-start-delays"></a>오케스트레이션 시작 연기
오케스트레이션 인스턴스는 작업 허브의 제어 큐 중 하나에 `ExecutionStarted` 메시지를 두어 시작됩니다. 특정 조건에서는 오케스트레이션이 실행되도록 예약된 시간과 실제로 실행하기 시작하는 시간 사이에 몇 초간 연기될 수 있습니다. 이 시간 간격 동안 오케스트레이션 인스턴스는 `Pending` 상태로 유지됩니다. 이렇게 연기되는 이유는 다음과 같을 수 있습니다.

1. **백로그된 제어 큐**: 이 인스턴스의 제어 큐에 많은 수의 메시지가 포함되어 있으면 런타임에서 `ExecutionStarted` 메시지를 받고 처리하기까지 시간이 걸릴 수 있습니다. 오케스트레이션에서 동시에 많은 이벤트를 처리하는 경우 메시지 백로그가 발생할 수 있습니다. 제어 큐로 이동하는 이벤트에는 오케스트레이션 시작 이벤트, 작업 완료, 지속형 타이머, 종료 및 외부 이벤트가 포함됩니다. 정상적인 상황에서 이처럼 연기되는 경우 더 많은 수의 파티션이 있는 새 작업 허브를 만드는 것이 좋습니다. 추가 파티션을 구성하면 런타임에서 부하 배포를 위해 더 많은 제어 큐를 만듭니다. 각 파티션은 최대 16개의 파티션이 있는 제어 큐와 1:1로 대응합니다.

2. **백오프 폴링 연기**: 오케스트레이션 연기의 일반적인 원인 중 하나는 [이전에 설명한 제어 큐의 백오프 폴링 동작](#queue-polling)입니다. 그러나 이 연기는 앱이 둘 이상의 인스턴스로 스케일 아웃된 경우에만 예상됩니다. 앱 인스턴스가 하나뿐이거나 오케스트레이션을 시작하는 앱 인스턴스가 대상 제어 큐를 폴링하는 것과 같은 인스턴스인 경우 큐 폴링 연기가 발생하지 않습니다. 백오프 폴링 연기는 앞에서 설명한 대로 **host.json** 설정을 업데이트하여 줄일 수 있습니다.

### <a name="storage-account-selection"></a>스토리지 계정 선택

Durable Functions에서 사용하는 큐, 테이블 및 Blob은 구성된 Azure Storage 계정을 통해 만듭니다. 사용할 계정은 **host.json** 파일의 `durableTask/storageProvider/connectionStringName` 설정(또는 Durable Functions 1.x의 `durableTask/azureStorageConnectionStringName` 설정)을 사용하여 지정할 수 있습니다.

#### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "connectionStringName": "MyStorageAccountAppSetting"
      }
    }
  }
}
```

#### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "azureStorageConnectionStringName": "MyStorageAccountAppSetting"
    }
  }
}
```

계정을 지정하지 않으면 기본 `AzureWebJobsStorage` 스토리지 계정이 사용됩니다. 그렇지만 성능이 중요한 워크로드에서는 기본이 아닌 스토리지 계정을 구성하는 것이 좋습니다. 지속형 함수는 Azure Storage를 과도하게 사용하며, 전용 스토리지 계정을 사용하면 지속형 함수 스토리지 사용이 Azure Functions 호스트의 내부 사용과 분리됩니다.

> [!NOTE]
> Azure Storage 공급자를 사용하는 경우 표준 범용 Azure Storage 계정이 필요합니다. 그 외의 스토리지 계정 유형은 지원되지 않습니다. Durable Functions에 대해 레거시 v1 범용 스토리지 계정을 사용하는 것이 좋습니다. 최신 v2 스토리지 계정은 Durable Functions 워크로드에 대한 비용이 훨씬 높을 수 있습니다. Azure Storage 계정 유형에 대한 자세한 내용은 [Storage 계정 개요](../../storage/common/storage-account-overview.md) 설명서를 참조하세요.

### <a name="orchestrator-scale-out"></a>오케스트레이터 확장

VM을 탄력적으로 추가하여 작업 함수를 무한대로 스케일 아웃할 수 있지만, 개별 오케스트레이터 인스턴스 및 엔터티는 단일 파티션에 있도록 제한되며 최대 파티션 수는 `host.json`의 `partitionCount` 설정을 통해 제한됩니다. 

> [!NOTE]
> 일반적으로 말해서 오케스트레이터 함수는 간단하게 설계되므로 많은 컴퓨팅 성능이 필요하지 않습니다. 따라서 오케스트레이션을 위한 처리량을 높이기 위해 많은 수의 제어 큐 파티션을 만들 필요가 없습니다. 대부분의 과도한 작업은 무한정 확장될 수 있는 상태 비저장 작업 함수에서 수행하는 것이 좋습니다.

제어 큐의 수는 **host.json** 파일에서 정의됩니다. 다음 예제 host.json 코드 조각에서는 `durableTask/storageProvider/partitionCount` 속성(또는 `durableTask/partitionCount` in Durable Functions 1.x)을 `3`으로 설정합니다. 제어 큐는 파티션 수만큼 있습니다.

#### <a name="durable-functions-2x"></a>Durable Functions 2.x

```json
{
  "extensions": {
    "durableTask": {
      "storageProvider": {
        "partitionCount": 3
      }
    }
  }
}
```

#### <a name="durable-functions-1x"></a>Durable Functions 1.x

```json
{
  "extensions": {
    "durableTask": {
      "partitionCount": 3
    }
  }
}
```

작업 허브는 1~16개 파티션으로 구성할 수 있습니다. 파티션 수를 지정하지 않으면 기본 파티션 수는 **4** 개 입니다.

트래픽이 적은 시나리오에서는 애플리케이션이 스케일 인되므로 소수의 작업자가 파티션을 관리합니다. 예를 들어 아래 다이어그램을 살펴보세요.

![오케스트레이션 다이어그램 스케일 인](./media/durable-functions-perf-and-scale/scale-progression-1.png)

위의 다이어그램에서 오케스트레이터 1~6은 파티션 간에 부하가 분산됩니다. 마찬가지로 작업과 같은 파티션은 작업자 간에 부하가 분산됩니다. 파티션은 시작된 오케스트레이터 수와 관계없이 작업자 간에 부하가 분산됩니다.

Azure Functions Consumption 또는 Elastic Premium 플랜에서 실행 중이거나 부하 기반 자동 스케일링이 구성된 경우 트래픽이 증가함에 따라 더 많은 작업자가 할당되고 결국 모든 작업자에 걸쳐 부하가 분산됩니다. 계속 스케일 아웃하면 결국 한 작업자가 각 파티션을 관리합니다. 반면에 작업은 모든 작업자 간에 계속 부하가 분산됩니다. 이 내용은 아래 이미지에 표시되어 있습니다.

![첫 번째 스케일 아웃된 오케스트레이션 다이어그램](./media/durable-functions-perf-and-scale/scale-progression-2.png)

*지정된 시간* 의 최대 동시 _활성_ 오케스트레이션 수의 상한은 애플리케이션에 할당된 작업자 수와 `maxConcurrentOrchestratorFunctions`의 값을 _곱한_ 값과 같습니다. 파티션을 작업자 간에 완전히 스케일 아웃하면 이 상한을 더 정확하게 만들 수 있습니다. 완전히 스케일 아웃하면 각 작업자에 하나의 Functions 호스트 인스턴스만 있기 때문에, _활성_ 동시 오케스트레이터 인스턴스의 최대수는 파티션 수에 `maxConcurrentOrchestratorFunctions`의 값을 _곱한_ 값과 같습니다.

> [!NOTE]
> 이 컨텍스트에서 활성은 오케스트레이션 또는 엔터티가 메모리에 로드되고 새 이벤트를 처리하는 것을 의미합니다. 오케스트레이션 또는 엔터티가 작업 함수의 반환 값과 같은 추가 이벤트를 대기하는 경우 메모리에서 언로드되고 더 이상 활성으로 간주되지 않습니다. 오케스트레이션 및 엔터티는 처리할 새 이벤트가 있는 경우에만 메모리에 다시 로드됩니다. 모두 ‘실행 중’ 상태이더라도 단일 VM에서 실행될 수 있는 총 오케스트레이션 또는 엔터티에는 실제 최댓값이 없습니다. 동시 활성 오케스트레이션 또는 엔터티 인스턴스의 수에만 제한이 있습니다.

아래 이미지는 더 많은 오케스트레이터가 추가되었지만, 일부는 비활성 상태이므로 회색으로 표시되는 완전히 스케일 아웃된 시나리오를 보여 줍니다.

![두 번째 스케일 아웃된 오케스트레이션 다이어그램](./media/durable-functions-perf-and-scale/scale-progression-3.png)

파티션이 균등하게 배포되도록 스케일 아웃 중에 제어 큐 임대를 Functions 호스트 인스턴스 간에 재배포할 수 있습니다. 이 임대는 내부적으로 Azure Blob Storage 임대로 구현되며, 개별 오케스트레이션 인스턴스 또는 엔터티가 한 번에 단일 호스트 인스턴스에서만 실행되도록 합니다. 작업 허브가 3개의 파티션(따라서 3개의 제어 큐)으로 구성된 경우 오케스트레이션 인스턴스 및 엔터티는 3개의 임대 보유 호스트 인스턴스 모두에서 부하가 분산될 수 있습니다. 작업 함수 실행을 위한 용량을 늘리기 위해 추가 VM을 추가할 수 있습니다.

다음 다이어그램에서는 Azure Functions 호스트에서 확장된 환경의 스토리지 엔터티와 상호 작용하는 방법을 보여 줍니다.

![크기 조정 다이어그램](./media/durable-functions-perf-and-scale/scale-interactions-diagram.png)

이전 다이어그램에 나와 있는 것처럼 모든 VM은 작업 항목 큐에 메시지에 대해 경합합니다. 그러나 세 개의 VM만 제어 큐에서 메시지를 얻을 수 있으며 각 VM에서 단일 제어 큐를 잠급니다.

오케스트레이션 인스턴스 및 엔터티는 모든 제어 큐 인스턴스에 배포됩니다. 배포는 오케스트레이션의 인스턴스 ID 또는 엔터티 이름과 키 쌍을 해시하여 수행됩니다. 기본적으로 오케스트레이션 인스턴스 ID는 임의 GUID이며 인스턴스가 모든 제어 큐에서 똑같이 분산되도록 합니다.

일반적으로 말해서 오케스트레이터 함수는 간단하게 설계되므로 많은 컴퓨팅 성능이 필요하지 않습니다. 따라서 오케스트레이션을 위한 처리량을 높이기 위해 많은 수의 제어 큐 파티션을 만들 필요가 없습니다. 대부분의 과도한 작업은 무한정 확장될 수 있는 상태 비저장 작업 함수에서 수행하는 것이 좋습니다.

### <a name="auto-scale"></a>자동 크기 조정

Consumption 및 Elastic Premium 플랜에서 실행 중인 모든 Azure Functions와 마찬가지로 Durable Functions는 [Azure Functions 스케일 컨트롤러](../event-driven-scaling.md#runtime-scaling)를 통해 자동 스케일링을 지원합니다. 크기 조정 컨트롤러는 _peek_ 명령을 주기적으로 실행하여 모든 큐의 대기 시간을 모니터링합니다. 확인된 메시지의 대기 시간에 따라, 크기 조정 컨트롤러는 VM을 추가할지 또는 제거할지를 결정합니다.

크기 조정 컨트롤러는 제어 큐 메시지 대기 시간이 너무 높다고 판단되면 메시지 대기 시간이 적절한 수준까지 감소하거나, 제어 큐 파티션 수에 도달할 때까지 VM 인스턴스를 추가합니다. 마찬가지로, 파티션 수에 관계없이 작업 항목 큐 대기 시간이 높으면 크기 조정 컨트롤러는 VM 인스턴스를 계속 추가합니다.

> [!NOTE]
> Durable Functions 2.0부터 함수 앱은 Elastic Premium 플랜의 VNET 보호 서비스 엔드포인트 내에서 실행되도록 구성할 수 있습니다. 이 구성에서 Durable Functions 트리거는 스케일링 컨트롤러 대신 스케일링 요청을 시작합니다. 자세한 정보는 [런타임 스케일링 모니터링](../functions-networking-options.md#premium-plan-with-virtual-network-triggers)을 참조하세요.

## <a name="thread-usage"></a>스레드 사용

오케스트레이터 함수는 여러 번의 재생을 거치면서 실행이 결정적이 될 수 있도록 단일 스레드에서 실행됩니다. 이러한 단일 스레드 실행 때문에, 오케스트레이터 함수 스레드는 어떤 이유로든 CPU 집약적 작업을 수행하지 않고, I/O를 수행하지 않고, 차단하지 않습니다. I/O, 차단 또는 다중 스레드가 필요한 작업은 작업 함수로 이동해야 합니다.

작업 함수에는 일반 큐 트리거 함수와 동일한 모든 동작이 있습니다. I/O를 안전하게 수행하고, CPU 집약적 작업을 실행하며, 여러 스레드를 사용할 수 있습니다. 작업 트리거는 상태 비저장이므로 VM 수에 관계 없이 자유롭게 확장할 수 있습니다.

엔터티 함수도 단일 스레드에서 실행되고 작업은 한 번에 하나씩 처리됩니다. 그러나 엔터티 함수에서는 실행할 수 있는 코드의 형식이 제한되지 않습니다.

## <a name="concurrency-throttles"></a>동시성 제한

Azure Functions는 단일 응용 프로그램 인스턴스 내에서 여러 함수를 동시에 실행하도록 지원합니다. 이러한 동시 실행은 병렬 처리를 늘리며, 일반적인 앱이 시간이 지나면서 겪게 되는 "콜드 시작"의 수를 최소화합니다. 그러나 동시성이 높으면 네트워크 연결 또는 사용 가능한 메모리와 같은 VM당 시스템 리소스가 모두 소모될 수 있습니다. 함수 앱의 요구에 따라, 고부하 상황에서 메모리의 부족 문제를 방지하기 위해 인스턴스당 동시성을 제한해야 할 수도 있습니다.

작업, 오케스트레이터 및 엔터티 함수 동시성 제한은 둘 다 **host.json** 파일에서 구성할 수 있습니다. 관련 설정은 작업 함수의 경우 `durableTask/maxConcurrentActivityFunctions`이고 오케스트레이터와 엔터티 함수 둘 다의 경우 `durableTask/maxConcurrentOrchestratorFunctions`입니다. 이러한 설정은 동시에 메모리에 로드할 수 있는 orchestrator, entity 또는 activity 함수의 최대 수를 제어합니다.

### <a name="functions-20"></a>Functions 2.0

```json
{
  "extensions": {
    "durableTask": {
      "maxConcurrentActivityFunctions": 10,
      "maxConcurrentOrchestratorFunctions": 10
    }
  }
}
```

### <a name="functions-1x"></a>Functions 1.x

```json
{
  "durableTask": {
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10
  }
}
```

이전 예제에서는 최대 10개의 오케스트레이터 또는 엔터티 함수와 10개의 작업 함수가 단일 VM에서 동시에 실행될 수 있습니다. 동시 작업 수 및 오케스트레이터 또는 엔터티 함수 실행 수를 지정하지 않으면 VM에 있는 코어 수의 10배로 제한됩니다.

작업자 VM의 최대 작업 또는 오케스트레이션/엔터티 수에 도달한 경우에는 새 함수 실행을 시작하기 전에 실행 중인 함수가 완료되거나 언로드될 때까지 지속성 트리거가 대기합니다.

> [!NOTE]
> 이러한 설정은 단일 VM의 메모리 및 CPU 사용량을 관리하는 데 유용합니다. 그러나 여러 VM에 걸쳐 스케일 아웃할 경우 각 VM에는 여러 고유한 제한이 설정됩니다. 해당 설정을 전역 수준에서 동시성을 제어하는 데는 사용할 수 없습니다.

> [!NOTE]
> 오케스트레이션과 엔터티는 이벤트 또는 작업을 적극적으로 처리하는 경우에만 메모리에 로드됩니다. 해당 논리 및 대기는 실행 후(예: 오케스트레이터 함수 코드의 `await`(C#) 또는 `yield`(JavaScript, Python) 문을 실행) 메모리에서 언로드됩니다. 메모리에서 언로드된 오케스트레이션 및 엔터티는 `maxConcurrentOrchestratorFunctions` 스로틀을 계산하는 데 포함되지 않습니다. 수백만 개의 오케스트레이션 또는 엔터티가 ‘실행 중’ 상태인 경우에도 활성 메모리에 로드될 때만 스로틀 제한으로 계산됩니다. 마찬가지로 작업 함수를 예약하는 오케스트레이션은 오케스트레이션이 작업이 실행 완료를 기다리는 경우 스로틀에 포함되지 않습니다.

### <a name="language-runtime-considerations"></a>언어 런타임 고려 사항

선택한 언어 런타임은 엄격한 동시성 제한 또는 함수를 적용할 수 있습니다. 예를 들어 Python 또는 PowerShell로 작성된 Durable Function 앱은 단일 VM에서 한 번에 하나의 함수만 실행할 수 있습니다. 따라서 신중하게 고려하지 않으면 상당한 성능 문제가 발생할 수 있습니다. 예를 들어 오케스트레이터가 10개의 활동으로 팬아웃하지만 언어 런타임이 동시성을 하나의 함수로만 제한하는 경우 10개의 작업 함수 중 9개는 실행될 기회를 기다리는 동안 중단됩니다. 또한 이렇게 중단된 9개의 활동은 Durable Functions 런타임이 이미 메모리에 로드했으므로 다른 작업자에게 부하를 분산할 수 없습니다. 활동 함수가 오래 실행되는 경우 특히 문제가 됩니다.

사용 중인 언어 런타임이 동시성에 제한을 두는 경우 Durable Functions 동시성 설정을 언어 런타임의 동시성 설정과 일치하도록 업데이트해야 합니다. 이렇게 하면 Durable Functions 런타임이 언어 런타임에서 허용하는 것보다 더 많은 함수를 동시에 실행하려고 시도하지 않으므로 보류 중인 모든 활동의 부하가 다른 VM에 분산되도록 할 수 있습니다. 예를 들어 동시성을 4개의 함수로 제한하는 Python 앱(단일 언어 작업자 프로세스에서 4개의 스레드로만 구성되거나 4개 언어 작업자 프로세스에서 1개의 스레드로만 구성됨)이 있는 경우 `maxConcurrentOrchestratorFunctions` 및 `maxConcurrentActivityFunctions`를 모두 4로 구성해야 합니다.

Python에 대한 자세한 내용 및 성능 권장 사항은 [Azure Functions Python 앱의 처리량 성능 향상](../python-scale-performance-reference.md)을 참조하세요. 이 Python 개발자 참조 설명서에 언급된 기술은 Durable Functions 성능 및 확장성에 상당한 영향을 줄 수 있습니다.

## <a name="extended-sessions"></a>확장 세션

확장 세션은 메시지 처리를 완료한 후에도 메모리에 오케스트레이션 및 엔터티를 유지하는 설정입니다. 확장 세션을 사용하도록 설정하면 일반적으로 기본 지속성 저장소에 대한 I/O가 줄어들고 전반적인 처리량은 향상됩니다.

**host.json** 파일에서 `durableTask/extendedSessionsEnabled`를 `true`로 설정하여 확장 세션을 사용하도록 설정할 수 있습니다. `durableTask/extendedSessionIdleTimeoutInSeconds` 설정은 유휴 세션이 메모리에 유지되는 기간을 제어하는 데 사용할 수 있습니다.

**Functions 2.0**
```json
{
  "extensions": {
    "durableTask": {
      "extendedSessionsEnabled": true,
      "extendedSessionIdleTimeoutInSeconds": 30
    }
  }
}
```

**Functions 1.0**
```json
{
  "durableTask": {
    "extendedSessionsEnabled": true,
    "extendedSessionIdleTimeoutInSeconds": 30
  }
}
```

이 설정에는 다음과 같은 두 가지 단점이 있을 수 있습니다.

1. 유휴 인스턴스가 메모리에서 신속하게 언로드되지 않으므로 전체 함수 앱의 메모리 사용이 증가합니다.
2. 수명이 짧고 고유한 동시 오케스트레이터 또는 엔터티 함수 실행이 많으면 처리량이 전반적으로 감소할 수 있습니다.

예를 들어 `durableTask/extendedSessionIdleTimeoutInSeconds`를 30초로 설정하면, 1초 이내에 실행되는 수명이 짧은 오케스트레이터 또는 엔터티 함수 에피소드도 30초 동안 메모리를 계속 차지합니다. 또한 이전에 언급된 `durableTask/maxConcurrentOrchestratorFunctions` 할당량을 기준으로 계산하므로 다른 오케스트레이터 또는 엔터티 함수가 실행되지 못할 수 있습니다.

오케스트레이터 및 엔터티 함수에서 확장 세션의 구체적인 효과는 다음 섹션에 설명되어 있습니다.

> [!NOTE]
> 확장 세션은 현재 C# 또는 F#과 같은 .NET 언어에서만 지원됩니다. 다른 플랫폼용으로 `extendedSessionsEnabled`를 `true`로 설정하면 작업 및 오케스트레이션 트리거 함수 실행에 자동으로 실패하는 등 런타임 문제가 발생할 수 있습니다.

> [!NOTE]
> 확장된 세션에 대한 지원은 [사용 중인 Durable Functions 스토리지 공급자](durable-functions-storage-providers.md)에 따라 달라질 수 있습니다. 확장 세션이 지원되는지 여부는 스토리지 공급자 설명서를 참조하세요.

### <a name="orchestrator-function-replay"></a>오케스트레이터 함수 재생

앞서 언급된 것처럼 오케스트레이터 함수는 **기록** 테이블의 콘텐츠를 사용하여 재생됩니다. 기본적으로 오케스트레이터 함수 코드는 메시지의 일괄 처리가 제어 큐에서 제거될 때마다 재생됩니다. 팬아웃, 팬인 패턴을 사용 중이고 모든 작업이 완료될 때까지 기다릴 때에도(예: .NET에서 `Task.WhenAll()`, JavaScript에서 `context.df.Task.all()`, Python에서 `context.task_all()` 사용), 시간 경과에 따라 작업 응답 일괄 처리가 처리되므로 재생이 발생합니다. 확장 세션이 사용되도록 설정되면 오케스트레이터 함수 인스턴스는 더는 메모리에 유지되지 않으며, 새 메시지는 전체 기록 재생 없이 처리될 수 있습니다.

확장 세션의 성능 향상은 다음과 같은 상황에서 가장 자주 관찰됩니다.

* 동시에 실행되는 오케스트레이션 인스턴스 수가 제한된 경우.
* 오케스트레이션에 신속하게 완료되는 순차 작업의 수가 많은 경우(예: 수백 개의 작업 함수 호출).
* 오케스트레이션이 거의 동시에 완료되는 다수의 작업을 팬아웃 및 팬인하는 경우.
* 오케스트레이터 함수에서 대용량 메시지를 처리하거나 CPU 집약적인 데이터 처리를 수행해야 하는 경우.

기타 모든 상황에서는 일반적으로 오케스트레이터 함수의 성능이 눈에 띄게 향상되지 않습니다.

> [!NOTE]
> 이러한 설정은 오케스트레이터 함수가 완전하게 개발 및 테스트된 후에만 사용해야 합니다. 적극적인 기본 재생 동작은 개발 시 [오케스트레이터 함수 코드 제약 조건](durable-functions-code-constraints.md) 위반을 탐지하는 데 유용할 수 있기 때문에 기본적으로 사용하지 않게 설정됩니다.

### <a name="entity-function-unloading"></a>엔터티 함수 언로드

엔터티 함수는 단일 일괄 처리에서 최대 20개의 작업을 처리합니다. 엔터티가 작업 일괄 처리를 완료하는 즉시 상태를 유지하고 메모리에서 언로드합니다. 확장 세션 설정을 사용하여 메모리에서 엔터티 언로드를 연기할 수 있습니다. 엔터티는 이전과 같이 상태 변경을 계속 유지하지만 구성된 기간에 메모리에 남아 있기 때문에 스토리지의 로드 수를 줄입니다. 스토리지에서 부하가 감소하면 자주 액세스하는 엔터티의 전체 처리량이 증가할 수 있습니다.

## <a name="performance-targets"></a>성능 목표

프로덕션 애플리케이션에 대해 지속형 함수를 사용하려는 경우 계획 프로세스의 초기에 성능 요구 사항을 검토해야 합니다. 이 섹션에서는 몇 가지 기본 사용 시나리오와 예상되는 최대 처리량 수치를 설명합니다.

* **순차적 작업 실행**: 이 시나리오에서는 일련의 작업 함수를 순차적으로 실행하는 오케스트레이터 함수를 설명합니다. 이 시나리오는 [함수 체인](durable-functions-sequence.md) 샘플과 가장 유사합니다.
* **병렬 작업 실행**: 이 시나리오에서는 [팬아웃, 팬인](durable-functions-cloud-backup.md) 패턴을 사용하여 많은 작업 함수를 병렬로 실행하는 오케스트레이터 함수를 설명합니다.
* **병렬 응답 처리**:이 시나리오는 [팬아웃, 팬인](durable-functions-cloud-backup.md) 패턴의 두 번째 절반 부분입니다. 팬인 부분의 성능에 중점을 둡니다. 팬아웃과 달리, 팬인은 단일 오케스트레이터 함수 인스턴스에 의해 수행되므로 단일 VM에서만 실행될 수 있습니다.
* **외부 이벤트 처리**: 이 시나리오에서는 한 번에 하나씩 [외부 이벤트](durable-functions-external-events.md)를 처리하는 단일 오케스트레이터 함수 인스턴스를 나타냅니다.
* **엔터티 작업 처리**: 이 시나리오는 _단일_ [카운터 엔터티](durable-functions-entities.md)가 작업의 상수 스트림을 처리할 수 있는 속도를 테스트합니다.

> [!TIP]
> 팬아웃과 달리, 팬인 작업은 단일 VM으로 제한됩니다. 애플리케이션이 팬아웃, 팬인 패턴을 사용하며, 팬인 성능에 걱정될 경우 여러 [하위 오케스트레이션](durable-functions-sub-orchestrations.md)에 걸쳐 작업 함수 팬아웃을 세분화하는 것을 고려합니다.

### <a name="azure-storage-performance-targets"></a>Azure Storage 성능 목표

다음 표는 [Durable Functions에 기본 Azure Storage 공급자](durable-functions-storage-providers.md#azure-storage)를 사용하는 경우 앞에서 설명한 시나리오에 예상되는 최대 처리량 수를 보여 줍니다. "인스턴스"는 Azure App Service의 하나의 작은 ([A1](../../virtual-machines/sizes-previous-gen.md)) VM에서 실행되는 오케스트레이터 함수의 단일 인스턴스를 나타냅니다. 모든 경우에 [확장 세션](#orchestrator-function-replay)이 사용되도록 설정되어 있다고 가정합니다. 실제 결과는 함수 코드에서 수행하는 CPU 또는 I/O 작업에 따라 달라질 수 있습니다.

| 시나리오 | 최대 처리량 |
|-|-|
| 순차적 작업 실행 | 인스턴스별 초당 5개 작업 |
| 병렬 작업 실행(팬아웃) | 인스턴스별 초당 100개 작업 |
| 병렬 응답 처리(팬인) | 인스턴스별 초당 150개 응답 |
| 외부 이벤트 처리 | 인스턴스별 초당 50개 이벤트 |
| 엔터티 작업 처리 | 초당 64개의 작업 |

예상되는 처리량 수치가 표시되지 않는데 CPU 및 메모리 사용량이 정상으로 표시되면 해당 원인이 [스토리지 계정의 상태](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#troubleshooting-guidance)와 관련이 있는 것인지 확인합니다. 지속형 함수 확장은 Azure Storage 계정에 과도한 부하를 발생할 수 있으며 충분히 높은 부하는 스토리지 계정 제한을 유발할 수 있습니다.

> [!TIP]
> 경우에 따라 **host.json** 에서 `controlQueueBufferThreshold` 설정 값을 늘려서 외부 이벤트의 처리량, 활동 팬인, 엔터티 작업을 크게 증가시킬 수 있습니다. 이 값을 기본값보다 높게 설정하면 지속성 작업 프레임워크 스토리지 공급자가 더 많은 메모리를 사용하여 이러한 이벤트를 보다 적극적으로 프리페치하고, Azure Storage 제어 큐에서 큐 제거 메시지와 관련된 지연을 줄입니다. 자세한 내용은 [host.json](durable-functions-bindings.md#host-json) 참조 설명서에서 확인할 수 있습니다.

### <a name="high-throughput-processing"></a>높은 처리량 처리

Azure Storage 백 엔드의 아키텍처는 Durable Functions의 이론상 최대 성능 및 확장성에 대한 특정 제한을 설정합니다. 테스트에서 Azure Storage의 Durable Functions이 처리량 요구 사항을 충족하지 않는 것으로 나타나는 경우 [Durable Functions에 Netherite 스토리지 공급자](durable-functions-storage-providers.md#netherite)를 대신 사용하는 방안을 고려해야 합니다.

Netherite 스토리지 백 엔드는 [Microsoft Research](https://www.microsoft.com/research)에서 설계하고 개발했습니다. [Azure 페이지 Blob](../../storage/blobs/storage-blob-pageblob-overview.md)을 바탕으로 [Azure Event Hubs](../../event-hubs/event-hubs-about.md) 및 [더 빠른](https://www.microsoft.com/research/project/faster/) 데이터베이스 기술을 사용합니다. Netherite 디자인은 다른 공급자에 비해 오케스트레이션 및 엔터티 처리량이 훨씬 높습니다. 일부 벤치마크 시나리오에서는 기본 Azure Storage 공급자에 비해 처리량이 수십 배 증가하는 것으로 나타났습니다.

Durable Functions에 지원되는 스토리지 공급자 및 비교 방법에 대한 자세한 내용은 [Durable Functions 스토리지 공급자](durable-functions-storage-providers.md) 설명서를 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [재해 복구 및 지역 배포에 관해 알아보기](durable-functions-disaster-recovery-geo-distribution.md)
