---
title: Java에서 Queue Storage를 사용하는 방법 - Azure Storage
description: Queue Storage를 사용하여 큐를 생성 및 삭제하는 방법을 알아봅니다. Java용 Azure Storage 클라이언트 라이브러리를 사용하여 메시지를 삽입하고, 미리보고, 가져오고 삭제하는 방법을 알아봅니다.
author: normesta
ms.author: normesta
ms.reviewer: dineshm
ms.date: 08/19/2020
ms.topic: how-to
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java
ms.openlocfilehash: 25734b7f4073895da832b3d020679c6f7b36a080
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665856"
---
# <a name="how-to-use-queue-storage-from-java"></a>Java에서 Queue Storage를 사용하는 방법

[!INCLUDE [storage-selector-queue-include](../../../includes/storage-selector-queue-include.md)]

## <a name="overview"></a>개요

이 가이드에서는 Azure Queue Storage 서비스를 사용하여 일반 시나리오를 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure Storage SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)(영문)를 사용합니다. 시나리오에는 큐 메시지 **삽입하기**, **미리보기**, **가져오기**, 그리고 **삭제하기** 가 포함됩니다. 큐 **생성** 및 **삭제** 코드도 다룹니다. 큐에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하세요.

[!INCLUDE [storage-queue-concepts-include](../../../includes/storage-queue-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

먼저 개발 시스템이 [Java용 Azure Queue Storage 클라이언트 라이브러리 v12](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)에 나열된 필수 구성 요소를 충족하는지 확인합니다.

`queues-how-to-v12`이라는 Java 애플리케이션을 만들려면 다음을 수행합니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 Maven을 사용하여 `queues-how-to-v12`라는 새 콘솔 앱을 만듭니다. 다음 `mvn` 명령을 입력하여 "hello world" Java 프로젝트를 만듭니다.

   ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.howto \
        --define artifactId=queues-howto-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
   ```

   ```powershell
   mvn archetype:generate `
       --define interactiveMode=n `
       --define groupId=com.queues.howto `
       --define artifactId=queues-howto-v12 `
       --define archetypeArtifactId=maven-archetype-quickstart `
       --define archetypeVersion=1.4
   ```

1. 프로젝트를 생성하는 출력은 다음과 같습니다.

   ```console
   [INFO] Scanning for projects...
   [INFO]
   [INFO] ------------------< org.apache.maven:standalone-pom >-------------------
   [INFO] Building Maven Stub Project (No POM) 1
   [INFO] --------------------------------[ pom ]---------------------------------
   [INFO]
   [INFO] >>> maven-archetype-plugin:3.1.2:generate (default-cli) > generate-sources @ standalone-pom >>>
   [INFO]
   [INFO] <<< maven-archetype-plugin:3.1.2:generate (default-cli) < generate-sources @ standalone-pom <<<
   [INFO]
   [INFO]
   [INFO] --- maven-archetype-plugin:3.1.2:generate (default-cli) @ standalone-pom ---
   [INFO] Generating project in Batch mode
   [INFO] ----------------------------------------------------------------------------
   [INFO] Using following parameters for creating project from Archetype: maven-archetype-quickstart:1.4
   [INFO] ----------------------------------------------------------------------------
   [INFO] Parameter: groupId, Value: com.queues.howto
   [INFO] Parameter: artifactId, Value: queues-howto-v12
   [INFO] Parameter: version, Value: 1.0-SNAPSHOT
   [INFO] Parameter: package, Value: com.queues.howto
   [INFO] Parameter: packageInPathFormat, Value: com/queues/howto
   [INFO] Parameter: version, Value: 1.0-SNAPSHOT
   [INFO] Parameter: package, Value: com.queues.howto
   [INFO] Parameter: groupId, Value: com.queues.howto
   [INFO] Parameter: artifactId, Value: queues-howto-v12
   [INFO] Project created from Archetype in dir: C:\queues\queues-howto-v12
   [INFO] ------------------------------------------------------------------------
   [INFO] BUILD SUCCESS
   [INFO] ------------------------------------------------------------------------
   [INFO] Total time:  6.775 s
   [INFO] Finished at: 2020-08-17T15:27:31-07:00
   [INFO] ------------------------------------------------------------------------
   ```

1. 새로 만든 `queues-howto-v12` 디렉터리로 전환합니다.

   ```console
   cd queues-howto-v12
   ```

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 `pom.xml` 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.6.0</version>
</dependency>
```

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

먼저,개발 시스템이 [Java용 Azure Storage SDK v8](https://github.com/azure/azure-storage-java)에 나열된 필수 구성 요소를 충족하는지 확인합니다. Java용 Azure Storage 라이브러리를 다운로드하고 설치 하는 방법에 대한 지침을 따르세요. 작업을 완료하고 나면 이 문서의 예제를 사용하는 Java 애플리케이션을 만들 수 있습니다.

---

## <a name="configure-your-application-to-access-queue-storage"></a>Queue Storage에 액세스하도록 애플리케이션 구성

Azure Storage API를 사용하여 큐에 액세스하려는 Java 파일의 맨 위에 다음 가져오기 명령문을 추가합니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ImportStatements":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
// Include the following imports to use queue APIs.
import com.microsoft.azure.storage.*;
import com.microsoft.azure.storage.queue.*;
```

---

## <a name="set-up-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 설정

Azure Storage 클라이언트는 스토리지 연결 문자열을 사용하여 데이터 관리 서비스에 액세스합니다. [Azure Portal](https://portal.azure.com)에 나열된 스토리지 계정의 이름 및 기본 액세스 키를 가져옵니다. 이를 연결 문자열에서 `AccountName` 및 `AccountKey` 값으로 사용합니다. 이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ConnectionString":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
// Define the connection-string with your values.
final String storageConnectionString =
    "DefaultEndpointsProtocol=https;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key";
```

`ServiceConfiguration.cscfg`이라는 서비스 구성 파일에 이 문자열을 저장할 수 있습니다. Microsoft Azure 역할 내에서 실행되는 앱의 경우 `RoleEnvironment.getConfigurationSettings`를 호출하여 연결 문자열에 액세스합니다. 이 예제에서는 연결 문자열을 `Setting` 요소에서 가져오는데, 이름은 `StorageConnectionString`입니다.

```java
// Retrieve storage account from connection-string.
String storageConnectionString =
    RoleEnvironment.getConfigurationSettings().get("StorageConnectionString");
```

---

다음 샘플에서는 스토리지 연결 문자열을 포함하는 `String` 개체가 있다고 가정합니다.

## <a name="how-to-create-a-queue"></a>방법: 큐 만들기

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

이 `QueueClient` 개체는 큐와 상호 작용하기 위한 작업을 포함합니다. 다음 코드는 `QueueClient` 개체를 만듭니다. 이 `QueueClient` 개체를 사용하여 사용하려는 큐를 만들 수 있습니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_CreateQueue":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

`CloudQueueClient` 개체를 통해 큐에 대한 참조 개체를 가져올 수 있습니다. 다음 코드는 사용하려는 큐에 대한 참조를 제공하는 `CloudQueueClient` 개체를 만듭니다. 큐가 없는 경우 새로 만들 수 있습니다.

> [!NOTE]
> 다른 방법으로 `CloudStorageAccount` 개체를 만들 수도 있습니다. 자세한 내용은 [Azure Storage 클라이언트 SDK 참조](https://azure.github.io/azure-sdk-for-java/storage.html)에서`CloudStorageAccount` 를 참조하세요.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

   // Create the queue client.
   CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

   // Retrieve a reference to a queue.
   CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Create the queue if it doesn't already exist.
   queue.createIfNotExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-add-a-message-to-a-queue"></a>큐에 메시지 추가 방법

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

기존 큐에 메시지를 삽입하려면 `sendMessage` 메서드를 호출합니다. 메시지는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 다음은 문자열 메시지를 큐로 보내는 코드입니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_AddMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

기존 큐에 메시지를 삽입하려면 먼저 새 `CloudQueueMessage`를 만듭니다. 다음으로, `addMessage` 메서드를 호출합니다. `CloudQueueMessage`는 문자열(UTF-8 형식) 또는 바이트 배열에서 만들 수 있습니다. 다음은 큐가 없는 경우 큐를 새로 만들고 `Hello, World` 메시지를 삽입하는 코드입니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.createIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.addMessage(message);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-peek-at-the-next-message"></a>방법: 다음 메시지 보기

큐에서 메시지를 제거하지 않고도 `peekMessage`를 호출하여 큐의 앞에서 원하는 메시지를 미리 볼 수 있습니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_PeekMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Peek at the next message.
    CloudQueueMessage peekedMessage = queue.peekMessage();

    // Output the message value.
    if (peekedMessage != null)
    {
      System.out.println(peekedMessage.getMessageContentAsString());
   }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-change-the-contents-of-a-queued-message"></a>방법: 대기 중인 메시지의 콘텐츠 변경

큐에 있는 메시지의 콘텐츠를 변경할 수 있습니다. 메시지가 작업 태스크를 나타내는 경우 이 기능을 사용하여 상태를 업데이트할 수 있습니다. 다음 코드는 새로운 콘텐츠로 큐 메시지를 업데이트하고 표시 시간 제한이 30초 더 늘어나도록 설정합니다. 표시 시간 제한을 확장하면 클라이언트는 메시지 작업을 계속하는 데 추가로 30초 정도 걸립니다. 재시도 횟수도 유지할 수 있습니다. 메시지가 *n* 번 이상 다시 시도된 경우, 메시지를 지울 수도 있습니다. 이 시나리오는 처리될 때마다 애플리케이션 오류를 트리거하는 메시지를 차단하여 보호해 줍니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

다음 코드 샘플에서는 메시지 큐를 검색하여 내용에서 검색 문자열과 일치하는 최초의 메시지를 찾고 메시지 내용을 수정한 후 종료합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateSearchMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

다음 코드 샘플에서는 메시지 큐를 검색하여 내용에서 `Hello, world`와 일치하는 최초의 메시지를 찾고 메시지 내용을 수정한 후 종료합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // The maximum number of messages that can be retrieved is 32.
    final int MAX_NUMBER_OF_MESSAGES_TO_PEEK = 32;

    // Loop through the messages in the queue.
    for (CloudQueueMessage message : queue.retrieveMessages(MAX_NUMBER_OF_MESSAGES_TO_PEEK,1,null,null))
    {
        // Check for a specific string.
        if (message.getMessageContentAsString().equals("Hello, World"))
        {
            // Modify the content of the first matching message.
            message.setMessageContent("Updated contents.");
            // Set it to be visible in 30 seconds.
            EnumSet<MessageUpdateFields> updateFields =
                EnumSet.of(MessageUpdateFields.CONTENT,
                MessageUpdateFields.VISIBILITY);
            // Update the message.
            queue.updateMessage(message, 30, updateFields, null, null);
            break;
        }
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

다음 코드 샘플에서는 큐에 표시되는 첫 번째 메시지만 업데이트합니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_UpdateFirstMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage message = queue.retrieveMessage();

    if (message != null)
    {
        // Modify the message content.
        message.setMessageContent("Updated contents.");
        // Set it to be visible in 60 seconds.
        EnumSet<MessageUpdateFields> updateFields =
            EnumSet.of(MessageUpdateFields.CONTENT,
            MessageUpdateFields.VISIBILITY);
        // Update the message.
        queue.updateMessage(message, 60, updateFields, null, null);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-get-the-queue-length"></a>방법: 큐 길이 가져오기

큐에 있는 메시지의 추정된 개수를 가져올 수 있습니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

`getProperties` 메서드는 현재 큐에 있는 메시지 수를 포함하여 여러 값을 반환합니다. 요청 후에 메시지를 추가하거나 제거할 수 있으므로 개수는 근사치입니다. `getApproximateMessageCount` 메서드는 Queue Storage를 호출하지 않고, 호출에 의해 검색된 마지막 값을 `getProperties`로 반환합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_GetQueueLength":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

`downloadAttributes` 메서드는 현재 큐에 있는 메시지 수를 포함하여 여러 값을 검색합니다. 요청 후에 메시지를 추가하거나 제거할 수 있으므로 개수는 근사치입니다. `getApproximateMessageCount` 메서드는 Queue Storage를 호출하지 않고, 호출에 의해 검색된 마지막 값을 `downloadAttributes`로 반환합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
       CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

   // Download the approximate message count from the server.
    queue.downloadAttributes();

    // Retrieve the newly cached approximate message count.
    long cachedMessageCount = queue.getApproximateMessageCount();

    // Display the queue length.
    System.out.println(String.format("Queue length: %d", cachedMessageCount));
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-dequeue-the-next-message"></a>큐에서 다음 메시지를 제거하는 방법

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. `receiveMessage`를 호출하면 큐에서 다음 메시지를 가져옵니다. `receiveMessage`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 `deleteMessage`도 호출해야 합니다. 코드에서 메시지를 처리하지 못하는 경우 이 두 단계 프로세스를 통해 동일한 메시지를 받고 다시 시도할 수 있습니다. 코드는 메시지가 처리된 직후에 `deleteMessage`를 호출합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessage":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

다음 코드는 2단계를 거쳐 큐에서 메시지를 제거합니다. `retrieveMessage`를 호출하면 큐에서 다음 메시지를 가져옵니다. `retrieveMessage`에서 반환된 메시지는 이 큐의 메시지를 읽는 다른 코드에는 표시되지 않습니다. 기본적으로, 이 메시지는 30초간 표시되지 않습니다. 큐에서 메시지 제거를 완료하려면 `deleteMessage`도 호출해야 합니다. 코드에서 메시지를 처리하지 못하는 경우 이 두 단계 프로세스를 통해 동일한 메시지를 받고 다시 시도할 수 있습니다. 코드는 메시지가 처리된 직후에 `deleteMessage`를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve the first visible message in the queue.
    CloudQueueMessage retrievedMessage = queue.retrieveMessage();

    if (retrievedMessage != null)
    {
        // Process the message in less than 30 seconds, and then delete the message.
        queue.deleteMessage(retrievedMessage);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="additional-options-for-dequeuing-messages"></a>큐에서 메시지를 제거하는 추가 옵션

큐에서 메시지 검색을 사용자 지정할 수 있는 방법으로는 두 가지가 있습니다. 먼저, 메시지의 배치(최대 32개)를 가져옵니다. 다음으로, 표시하지 않는 시간 제한을 더 길거나 더 짧게 설정하여 코드에서 각 메시지를 완전히 처리하는 시간을 늘리거나 줄일 수 있습니다.

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

다음 코드 예제는 `receiveMessages` 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분(300초)으로 설정합니다. 시간 제한은 모든 메시지에 동시에 시작됩니다. `receiveMessages`에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DequeueMessages":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

다음 코드 예제는 `retrieveMessages` 메서드를 사용하여 한 번 호출에 20개의 메시지를 가져옵니다. 그런 다음에 `for` 루프를 사용하여 각 메시지를 처리합니다. 또한 각 메시지에 대해 표시하지 않는 제한 시간을 5분(300초)으로 설정합니다. 시간 제한은 모든 메시지에 동시에 시작됩니다. `retrieveMessages`에 대한 호출 이후로 5분이 지나고 나면 삭제되지 않은 모든 메시지가 다시 표시됩니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Retrieve 20 messages from the queue with a visibility timeout of 300 seconds.
    for (CloudQueueMessage message : queue.retrieveMessages(20, 300, null, null)) {
        // Do processing for all messages in less than 5 minutes,
        // deleting each message after processing.
        queue.deleteMessage(message);
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-list-the-queues"></a>큐 나열하는 방법

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

현재 큐의 목록을 가져오려면 `QueueServiceClient.listQueues()` 메서드를 호출해 `QueueItem` 개체 컬렉션을 반환합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_ListQueues":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

현재 큐의 목록을 가져오려면 `CloudQueueClient.listQueues()` 메서드를 호출해 `CloudQueue` 개체 컬렉션을 반환합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient =
        storageAccount.createCloudQueueClient();

    // Loop through the collection of queues.
    for (CloudQueue queue : queueClient.listQueues())
    {
        // Output each queue name.
        System.out.println(queue.getName());
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

## <a name="how-to-delete-a-queue"></a>방법: 큐 삭제

# <a name="java-v12-sdk"></a>[Java v12 SDK](#tab/java)

큐와 큐에 포함된 모든 메시지를 삭제하려면 `QueueClient` 개체의 `delete` 메서드를 호출합니다.

:::code language="java" source="~/azure-storage-snippets/queues/howto/java/java-v12/src/main/java/com/queues/howto/App.java" id="Snippet_DeleteMessageQueue":::

# <a name="java-v8-sdk"></a>[Java v8 SDK](#tab/java8)

큐와 큐에 포함된 모든 메시지를 삭제하려면 `CloudQueue` 개체의 `deleteIfExists` 메서드를 호출합니다.

```java
try
{
    // Retrieve storage account from connection-string.
    CloudStorageAccount storageAccount =
        CloudStorageAccount.parse(storageConnectionString);

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.createCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.getQueueReference("myqueue");

    // Delete the queue if it exists.
    queue.deleteIfExists();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

---

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>다음 단계

이제 Queue Storage의 기본 사항을 배웠으므로 다음 링크를 따라 좀 더 복잡한 스토리지 작업에 대해 알아보세요.

- [Java용 Azure Storage SDK](https://github.com/Azure/Azure-SDK-for-Java)
- [Azure Storage 클라이언트 SDK 참조](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage)
- [Azure Storage 서비스 REST API](/rest/api/storageservices/)
- [Azure Storage 팀 블로그](https://techcommunity.Microsoft.com/t5/Azure-storage/bg-p/azurestorageblog)
