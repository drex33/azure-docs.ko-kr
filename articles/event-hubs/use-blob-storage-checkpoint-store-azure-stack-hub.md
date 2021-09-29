---
title: Azure Stack Hub에서 Blob Storage를 검사점 저장소로 사용
description: 이 문서에서는 Azure Stack Hub의 Event Hubs에서 검사점 저장소로 Blob Storage를 사용하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 09/28/2021
ms.openlocfilehash: e3d75d4cd85bbf4e1bc8fc6fd581d510c35b0cb4
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213518"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub"></a>Blob Storage를 검사점 저장소로 사용 - Azure Stack Hub의 Event Hubs
Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원하는 환경에서 검사점 저장소로 Azure Blob Storage을 사용 하는 경우, 코드를 사용하여 스토리지 서비스 API 버전을 해당 환경에서 지원하는 특정 버전으로 변경해야 합니다. 예를 들어 [Azure Stack Hub 버전 2002에서 Event Hubs](/azure-stack/user/event-hubs-overview)를 실행 중인 경우, 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2017-11-09입니다. 이 경우 코드를 사용하여 스토리지 서비스 API 버전의 대상을 2017-11-09로 지정해야 합니다. 특정 스토리지 API 버전을 대상으로 지정하는 방법에 대한 예제는 GitHub의 다음 샘플을 참조하세요. 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/javascript/receiveEventsWithApiSpecificStorage.js) 또는 [TypeScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/eventhub/eventhubs-checkpointstore-blob/samples/v1/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python - [동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [비동기](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

Azure Stack Hub에서 지원하는 버전을 대상으로 지정하지 않고 검사점 저장소로 Blob Storage 사용하는 Event Hubs 수신기를 실행하는 경우, 다음과 같은 오류 메시지가 표시됩니다.

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Python의 샘플 오류 메시지
Python의 경우 `azure.core.exceptions.HttpResponseError`의 오류가 `EventHubConsumerClient.receive()`의 `on_error(partition_context, error)` 오류 처리기로 전달됩니다. 그러나 `receive()` 메서드는 예외를 발생하지 않습니다. `print(error)`에서 다음의 예외 정보를 출력합니다.

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

로거는 다음과 같은 두 가지 경고를 기록합니다.

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>다음 단계

분할 및 검사점에 대한 자세한 내용은 다음 문서, [애플리케이션의 여러 인스턴스에서 파티션 부하 분산](event-processor-balance-partition-load.md)을 참조하세요.
