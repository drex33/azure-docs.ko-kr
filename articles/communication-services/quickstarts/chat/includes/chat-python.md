---
title: 포함 파일
description: 포함 파일
services: azure-communication-services
author: probableprime
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 06/30/2021
ms.topic: include
ms.custom: include file
ms.author: rifox
ms.openlocfilehash: b22cee560284a5a185e6b3ba560531d43d37ca67
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122967929"
---
## <a name="sample-code"></a>샘플 코드
[GitHub](https://github.com/Azure-Samples/communication-services-python-quickstarts/tree/main/add-chat)에서 이 빠른 시작에 대한 최종 코드를 찾습니다.

## <a name="prerequisites"></a>사전 요구 사항
시작하기 전에 다음을 확인해야 합니다.

- 활성 구독이 있는 Azure 계정을 만듭니다. 자세한 내용은 [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)를 참조하세요.
- [Python](https://www.python.org/downloads/) 설치
- Azure Communication Services 리소스를 만듭니다. 자세한 내용은 [빠른 시작: Communication Services 리소스 만들기 및 관리](../../create-communication-resource.md)를 참조하세요. 이 빠른 시작에서는 리소스 엔드포인트를 기록해야 합니다.
- [사용자 액세스 토큰](../../access-tokens.md). 범위를 `chat`으로 설정하고 `token` 문자열과 `userId` 문자열을 기록해 둡니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-python-application"></a>새 Python 애플리케이션 만들기

터미널 또는 명령 창을 열어 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다.

```console
mkdir chat-quickstart && cd chat-quickstart
```

텍스트 편집기를 사용하여 프로젝트 루트 디렉터리에 *start-chat.py* 라는 파일을 만듭니다. 기본 예외 처리를 비롯해 프로그램의 구조를 추가합니다. 다음 섹션에서는 이 빠른 시작의 모든 소스 코드를 이 파일에 추가합니다.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>SDK 설치

다음 명령을 사용하여 SDK를 설치합니다.

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>개체 모델

다음 클래스 및 인터페이스는 Python용 Azure Communication Services 채팅 SDK의 주요 기능 중 일부를 처리합니다.

| Name                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| `ChatClient` | 이 클래스는 채팅 기능에 필요합니다. 구독 정보를 사용하여 인스턴스화하고 스레드를 만들고 가져오고 삭제하는 데 사용합니다. |
| `ChatThreadClient` | 이 클래스는 채팅 대화목록 기능에 필요합니다. `ChatClient`를 통해 인스턴스를 가져오고 이를 사용하여 메시지를 전송, 수신, 업데이트 및 삭제할 수 있습니다. 또한 이를 통해 사용자를 추가, 제거 및 가져오고, 입력 알림과 읽음 확인을 보낼 수 있습니다. |

## <a name="create-a-chat-client"></a>채팅 클라이언트 만들기

채팅 클라이언트를 만들려면 Communications Service 엔드포인트와 필수 조건 단계의 일부로 생성된 액세스 토큰을 사용합니다.

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```
이 빠른 시작에서는 권장되는 사항이기는 하지만 채팅 애플리케이션에 대한 토큰을 관리하는 서비스 계층을 만드는 방법을 다루지 않습니다. 자세한 내용은 [채팅 개념](../../../concepts/chat/concepts.md)의 "채팅 아키텍처" 섹션을 참조하세요.

## <a name="start-a-chat-thread"></a>채팅 스레드 시작

`create_chat_thread` 메서드를 사용하여 채팅 스레드를 만듭니다.

- `topic`을 사용하여 스레드에 항목을 제공합니다. 채팅 스레드가 생성되면 `update_thread` 함수를 사용하여 항목을 업데이트할 수 있습니다.
- `thread_participants`를 사용하여 채팅 스레드에 추가할 `ChatParticipant`를 나열합니다. `ChatParticipant`는 `CommunicationUserIdentifier` 유형을 `user`로 받아들입니다.

`CreateChatThreadResult`는 스레드를 만들 때 반환된 결과입니다. 생성된 채팅 스레드 `id`를 가져오는 데 사용할 수 있습니다. 그런 다음, `get_chat_thread_client` 메서드를 사용하여 이 `id`를 `ChatThreadClient` 개체를 가져오는 데 사용할 수 있습니다. `ChatThreadClient`를 사용하면 이 채팅 스레드에 다른 채팅 작업을 수행할 수 있습니다.

```python
topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>채팅 스레드 클라이언트 가져오기

`get_chat_thread_client` 메서드는 이미 존재하는 스레드에 대한 스레드 클라이언트를 반환합니다. 생성된 스레드에서 작업을 수행하는 데 사용할 수 있습니다. 예를 들어 참가자를 추가하고 메시지를 보낼 수 있습니다. `thread_id`는 기존 채팅 스레드의 고유 ID입니다.

`ChatThreadClient`를 사용하면 이 채팅 스레드에 다른 채팅 작업을 수행할 수 있습니다.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>모든 채팅 스레드 나열

`list_chat_threads` 메서드는 `ChatThreadItem` 유형의 반복기를 반환합니다.

- `start_time`을 사용하여 채팅 스레드를 가져올 가장 이른 시간을 지정합니다.
- `results_per_page`를 사용하여 페이지당 반환되는 최대 채팅 스레드 수를 지정합니다.

`[ChatThreadItem]` 반복기는 스레드 나열 시 반환된 응답입니다.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>채팅 스레드에 메시지 보내기

`send_message` 메서드를 사용하여 방금 만든 채팅 스레드에 `thread_id`로 식별되는 메시지를 보냅니다.

- `content`를 사용하여 채팅 메시지 콘텐츠를 제공합니다.
- `chat_message_type`을 사용하여 메시지 콘텐츠 형식을 지정합니다. 가능한 값은 `text` 및 `html`입니다. 값을 지정하지 않으면 기본값 `text`로 설정됩니다.
- `sender_display_name`을 사용하여 보낸 사람의 표시 이름을 지정합니다.
- 선택적으로 `metadata`를 사용하여 메시지와 함께 보내려는 추가 데이터를 포함합니다. 이 필드는 개발자가 채팅 메시지 기능을 확장하고 사용 사례에 대한 사용자 지정 정보를 추가할 수 있는 메커니즘을 제공합니다. 예를 들어 메시지에서 파일 링크를 공유할 때 메타데이터에 'hasAttachment:true'를 추가하여 수신자의 애플리케이션이 이를 구문 분석하고 그에 따라 표시할 수 있습니다.

`SendChatMessageResult`는 메시지 전송 시 반환된 응답입니다. 메시지의 고유 ID를 포함합니다.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='Please take a look at the attachment'
sender_display_name='sender name'
metadata={
    'hasAttachment': 'true',
    'attachmentUrl': 'https://contoso.com/files/attachment.docx'
}

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT, metadata=metadata)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>채팅 스레드에서 채팅 메시지 받기

지정된 간격으로 `list_messages` 메서드를 폴링하여 채팅 메시지를 가져올 수 있습니다.

- `results_per_page`를 사용하여 페이지당 반환되는 최대 메시지 수를 지정합니다.
- `start_time`을 사용하여 메시지를 가져올 가장 이른 시간을 지정합니다.

`[ChatMessage]` 반복기는 메시지 나열 시 반환된 응답입니다.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages`는 `update_message` 및 `delete_message`를 사용하여 메시지에 수행된 편집 또는 삭제를 포함한 최신 버전의 메시지를 반환합니다. 삭제된 메시지의 경우 `ChatMessage.deleted_on`은 메시지가 삭제된 시간을 나타내는 `datetime` 값을 반환합니다. 편집된 메시지의 경우 `ChatMessage.edited_on`은 메시지가 편집된 시간을 나타내는 `datetime`을 반환합니다. 원래 메시지 생성 시간은 메시지를 정렬하는 데 사용되는 `ChatMessage.created_on`을 사용하여 확인할 수 있습니다

`list_messages`는 `ChatMessage.type`으로 식별되는 다양한 유형의 메시지를 반환합니다. 

자세한 내용은 [메시지 유형](../../../concepts/chat/concepts.md#message-types)을 참조하세요.

## <a name="send-read-receipt"></a>읽음 확인 보내기

`send_read_receipt` 메서드를 사용하여 사용자 대신 읽음 확인 이벤트를 스레드에 게시합니다.

- `message_id`를 사용하여 현재 사용자가 읽은 최신 메시지의 ID를 지정합니다.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>채팅 스레드에 사용자를 참가자로 추가

채팅 스레드가 생성되면 스레드에 사용자를 추가하고 제거할 수 있습니다. 사용자를 추가하면 채팅 스레드에 메시지를 보내고 다른 참가자를 추가/제거할 수 있는 액세스 권한이 사용자에게 부여됩니다. `add_participants` 메서드를 호출하기 전에 해당 사용자에 대한 새 액세스 토큰 및 ID를 획득했는지 확인하세요. 사용자는 채팅 클라이언트를 초기화하기 위해 해당 액세스 토큰이 필요합니다.

새 액세스 토큰과 ID가 모든 사용자에게 제공되는 경우 `add_participants` 메서드를 사용하여 한 명 이상의 사용자를 채팅 스레드에 추가할 수 있습니다.

`list(tuple(ChatParticipant, CommunicationError))`가 반환됩니다. 참가자가 성공적으로 추가되면 빈 목록이 필요합니다. 참가자를 추가하는 동안 오류가 발생한 경우 발생한 오류와 함께 실패한 참가자로 목록이 채워집니다.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatParticipant(
#     identifier=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatParticipant(
    identifier=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>채팅 스레드의 스레드 참가자 나열

참가자를 추가하는 방법과 유사하게 스레드에서 참가자를 나열할 수도 있습니다.

`list_participants`를 사용하여 스레드의 참가자를 검색합니다. 다음 명령은 모두 선택 사항입니다.

- `results_per_page`를 사용하여 페이지당 반환할 최대 참가자 수를 지정합니다.
- `skip`을 사용하여 응답에서 지정된 위치까지 참가자를 건너뜁니다.

`[ChatParticipant]` 반복기는 참가자 나열 시 반환된 응답입니다.

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>코드 실행

`python` 명령을 사용하여 애플리케이션 디렉터리에서 애플리케이션을 실행합니다.

```console
python start-chat.py
```
