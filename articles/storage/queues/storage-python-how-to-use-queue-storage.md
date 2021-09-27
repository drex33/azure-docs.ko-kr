---
title: Python에서 Azure Queue Storage를 사용하는 방법
description: Azure Queue Storage를 사용하여 Python에서 큐를 작성 및 삭제하고 메시지를 삽입, 가져오기 및 삭제하는 방법을 알아봅니다.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 02/16/2021
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: seo-javascript-october2019, devx-track-python
ms.openlocfilehash: c7210ea6c0930f77620254a09b46ea37055aa8ef
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579556"
---
# <a name="how-to-use-azure-queue-storage-from-python"></a>Python에서 Azure Queue Storage를 사용하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>개요

이 문서에서는 Azure Queue Storage 서비스를 사용하는 일반적인 시나리오를 보여 줍니다. 여기서 다루는 시나리오에는 큐 메시지의 삽입, 피킹(peeking), 가져오기 및 삭제가 포함됩니다. 큐를 만들고 삭제하는 코드도 다룹니다.

이 문서의 예제는 Python으로 작성되었으며 [Python용 Azure Queue Storage 클라이언트 라이브러리](https://github.com/azure/azure-sdk-for-python/tree/master/sdk/storage/azure-storage-queue)를 사용합니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="download-and-install-azure-storage-sdk-for-python"></a>Python용 Azure Storage SDK 다운로드 및 설치

[Python용 Azure Storage SDK](https://github.com/azure/azure-storage-python)에는 Python v2.7, v3.3 이상이 필요합니다.

### <a name="install-via-pypi"></a>PyPI를 통해 설치

PyPi(Python Package Index)를 통해 설치하려면 다음을 입력합니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

```console
pip install azure-storage-queue
```

# <a name="python-v2"></a>[Python v2](#tab/python2)

```console
pip install azure-storage-queue==2.1.0
```

---

> [!NOTE]
> Python용 Azure Storage SDK v0.36 또는 이전 버전에서 업그레이드하는 경우 최신 패키지를 설치하기 전에 `pip uninstall azure-storage`를 사용하여 이전 SDK를 제거합니다.

대체 설치 방법은 [Python용 Azure SDK](https://github.com/Azure/Azure-SDK-for-Python)를 참조하세요.

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 애플리케이션 구성

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

[`QueueClient`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient) 개체를 사용하면 큐로 작업할 수 있습니다. 프로그래밍 방식으로 Azure 큐에 액세스하려는 Python 파일의 맨 위쪽에 다음 코드를 추가합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ImportStatements":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`QueueService`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true) 개체를 사용하면 큐로 작업할 수 있습니다. 다음 코드는 `QueueService` 개체를 만듭니다. 프로그래밍 방식으로 Azure Storage에 액세스하려는 Python 파일의 맨 위쪽에 다음 코드를 추가합니다.

```python
from azure.storage.queue import (
        QueueService,
        QueueMessageFormat
)

import os, uuid
```

---

`os` 패키지는 환경 변수 검색을 지원합니다. `uuid` 패키지는 큐 이름에 대한 고유 식별자 생성을 지원합니다.

## <a name="create-a-queue"></a>큐 만들기

연결 문자열은 이전에 설정된 `AZURE_STORAGE_CONNECTION_STRING` 환경 변수에서 검색됩니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

다음 코드는 스토리지 연결 문자열을 사용하는 `QueueClient` 개체를 만듭니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_CreateQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

다음 코드는 스토리지 연결 문자열을 사용하는 `QueueService` 개체를 만듭니다.

```python
# Retrieve the connection string from an environment
# variable named AZURE_STORAGE_CONNECTION_STRING
connect_str = os.getenv("AZURE_STORAGE_CONNECTION_STRING")

# Create a unique name for the queue
queue_name = "queue-" + str(uuid.uuid4())

# Create a QueueService object which will
# be used to create and manipulate the queue
print("Creating queue: " + queue_name)
queue_service = QueueService(connection_string=connect_str)

# Create the queue
queue_service.create_queue(queue_name)
```

---

Azure 큐 메시지는 텍스트로 저장됩니다. 이진 데이터를 저장하려면 메시지를 큐에 넣기 전에 Base64 인코딩 및 디코딩 기능을 설정합니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

클라이언트 개체를 만들 때 Base64 인코딩 및 디코딩 함수를 구성합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_EncodeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

Queue Storage 개체에서 Base64 인코딩 및 디코딩 함수를 구성합니다.

```python
# Setup Base64 encoding and decoding functions
queue_service.encode_function = QueueMessageFormat.binary_base64encode
queue_service.decode_function = QueueMessageFormat.binary_base64decode
```

---

## <a name="insert-a-message-into-a-queue"></a>큐에 메시지 삽입

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

큐에 메시지를 삽입하려면 [`send_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#send-message-content----kwargs-) 메서드를 사용합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_AddMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

큐에 메시지를 삽입하려면 [`put_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#put-message-queue-name--content--visibility-timeout-none--time-to-live-none--timeout-none-) 메서드를 사용하여 새 메시지를 만들고 큐에 추가합니다.

```python
message = u"Hello, World"
print("Adding message: " + message)
queue_service.put_message(queue_name, message)
```

---

## <a name="peek-at-messages"></a>메시지 보기

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

[`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#peek-messages-max-messages-none----kwargs-) 메서드를 호출하여 큐에서 제거하지 않고 메시지를 피킹할 수 있습니다. 기본적으로 이 메서드는 단일 메시지를 피킹합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_PeekMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`peek_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#peek-messages-queue-name--num-messages-none--timeout-none-) 메서드를 호출하여 큐에서 제거하지 않고 메시지를 피킹할 수 있습니다. 기본적으로 이 메서드는 단일 메시지를 피킹합니다.

```python
messages = queue_service.peek_messages(queue_name)

for peeked_message in messages:
    print("Peeked message: " + peeked_message.content)
```

---

## <a name="change-the-contents-of-a-queued-message"></a>대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업을 나타내는 경우 이 기능을 사용하여 작업의 상태를 업데이트할 수 있습니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

다음 코드에서는 [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#update-message-message--pop-receipt-none--content-none----kwargs-) 메서드를 사용하여 메시지를 업데이트합니다. 표시 제한 시간은 0으로 설정되어 있습니다.이는 메시지가 즉시 표시되고 콘텐츠가 업데이트됨을 의미합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_ChangeMessage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

다음 코드에서는 [`update_message`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#update-message-queue-name--message-id--pop-receipt--visibility-timeout--content-none--timeout-none-) 메서드를 사용하여 메시지를 업데이트합니다. 표시 제한 시간은 0으로 설정되어 있습니다.이는 메시지가 즉시 표시되고 콘텐츠가 업데이트됨을 의미합니다.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    queue_service.update_message(
        queue_name, message.id, message.pop_receipt, 0, u"Hello, World Again")
```

---

## <a name="get-the-queue-length"></a>큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

[get_queue_properties](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#get-queue-properties---kwargs-) 메서드는 `approximate_message_count`를 포함한 큐 속성을 반환합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_GetQueueLength":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[`get_queue_metadata`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-queue-metadata-queue-name--timeout-none-) 메서드는 `approximate_message_count`를 포함한 큐 속성을 반환합니다.

```python
metadata = queue_service.get_queue_metadata(queue_name)
count = metadata.approximate_message_count
print("Message count: " + str(count))
```

---

서비스가 요청에 응답한 후 메시지가 추가되거나 제거될 수 있으므로 이 결과는 근사치일 뿐입니다.

## <a name="dequeue-messages"></a>큐에서 메시지 제거

2단계를 거쳐 큐에서 메시지를 제거합니다. 코드에서 메시지를 처리하지 못하는 경우 이 두 단계 프로세스를 통해 동일한 메시지를 받고 다시 시도할 수 있습니다. 메시지가 성공적으로 처리된 후에 `delete_message`를 호출합니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

[receive_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-)를 호출하면 기본적으로 큐에서 다음 메시지가 나타납니다. `receive_messages`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-message-message--pop-receipt-none----kwargs-)도 호출해야 합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueMessages":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

[get_messages](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-)를 호출하면 기본적으로 큐에서 다음 메시지가 나타납니다. `get_messages`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 [delete_message](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-message-queue-name--message-id--pop-receipt--timeout-none-)도 호출해야 합니다.

```python
messages = queue_service.get_messages(queue_name)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져올 수 있습니다. 두 번째로, 표시하지 않는 제한 시간을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

다음 코드 예제는 [`receive_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#receive-messages---kwargs-) 메서드를 사용하여 메시지를 일괄적으로 가져옵니다. 그런 다음, 중첩된 `for` 루프를 사용하여 각 일괄 처리 내 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DequeueByPage":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

다음 코드 예제는 [`get_messages`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#get-messages-queue-name--num-messages-none--visibility-timeout-none--timeout-none-) 메서드를 사용하여 한 번 호출에 16개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분으로 설정합니다.

```python
messages = queue_service.get_messages(queue_name, num_messages=16, visibility_timeout=5*60)

for message in messages:
    print("Deleting message: " + message.content)
    queue_service.delete_message(queue_name, message.id, message.pop_receipt)
```

---

## <a name="delete-a-queue"></a>큐 삭제

# <a name="python-v12-sdk"></a>[Python v12 SDK](#tab/python)

큐와 큐에 표함된 모든 메시지를 삭제하려면 [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueclient#delete-queue---kwargs-) 메서드를 호출합니다.

:::code language="python" source="~/azure-storage-snippets/queues/howto/python/python-v12/python-howto-v12.py" id="Snippet_DeleteQueue":::

# <a name="python-v2"></a>[Python v2](#tab/python2)

큐와 큐에 표함된 모든 메시지를 삭제하려면 [`delete_queue`](/azure/developer/python/sdk/storage/azure-storage-queue/azure.storage.queue.queueservice.queueservice?view=storage-py-v2&preserve-view=true#delete-queue-queue-name--fail-not-exist-false--timeout-none-) 메서드를 호출합니다.

```python
print("Deleting queue: " + queue_name)
queue_service.delete_queue(queue_name)
```

---

[!INCLUDE [storage-try-azure-tools-queues](../../../includes/storage-try-azure-tools-queues.md)]

## <a name="next-steps"></a>다음 단계

이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 자세히 알아보세요.

- [Azure Queue Storage Python API 참조](/python/api/azure-storage-queue)
- [Python 개발자 센터](https://azure.microsoft.com/develop/python/)
- [Azure Storage REST API 참조](/rest/api/storageservices/)
