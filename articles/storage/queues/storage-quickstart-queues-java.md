---
title: '빠른 시작: Azure Queue Storage 클라이언트 라이브러리 v12 - Java'
description: Java용 Azure Queue Storage 클라이언트 라이브러리 v12를 사용하여 큐를 만들고 메시지를 큐에 추가하는 방법을 알아봅니다. 그런 다음, 큐에서 메시지를 읽고 삭제하는 방법을 알아봅니다. 큐를 삭제하는 방법도 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 12/01/2020
ms.topic: quickstart
ms.service: storage
ms.subservice: queues
ms.custom: devx-track-java, mode-other
ms.openlocfilehash: 4d78442492edc3c2c92f92d1c7bf29aed99bc7c9
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133063570"
---
# <a name="quickstart-azure-queue-storage-client-library-v12-for-java"></a>빠른 시작: Java용 Azure Queue Storage 클라이언트 라이브러리 v12

Java용 Azure Queue Storage 클라이언트 라이브러리 v12를 시작합니다. Azure Queue Storage는 나중에 검색하고 처리할 수 있도록 대량의 메시지를 저장하는 서비스입니다. 다음 단계에 따라 패키지를 설치하고 기본 작업에 대한 예제 코드를 사용해보십시오.

Java용 Azure Queue Storage 클라이언트 라이브러리 v12를 사용하여 다음을 수행합니다.

- 큐 만들기
- 큐에 메시지 추가
- 큐의 메시지 피킹(Peeking)
- 큐의 메시지 업데이트
- 큐에서 메시지 수신 및 삭제
- 큐 삭제

추가 리소스:

- [API 참조 설명서](/java/api/overview/azure/storage-queue-readme)
- [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue)
- [패키지(Maven)](https://mvnrepository.com/artifact/com.azure/azure-storage-queue)
- [샘플](../common/storage-samples-java.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json#queue-samples)

## <a name="prerequisites"></a>필수 구성 요소

- [JDK(Java Development Kit)](/java/azure/jdk/), 버전 8 이상
- [Apache Maven](https://maven.apache.org/download.cgi)
- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- Azure Storage 계정 - [스토리지 계정 만들기](../common/storage-account-create.md)

## <a name="setting-up"></a>설치

이 섹션에서는 Java용 Azure Queue Storage 클라이언트 라이브러리 v12를 사용하는 프로젝트 준비 과정을 안내합니다.

### <a name="create-the-project"></a>프로젝트 만들기

`queues-quickstart-v12`라는 Java 애플리케이션을 만듭니다.

1. 콘솔 창(예: cmd, PowerShell 또는 Bash)에서 Maven을 사용하여 `queues-quickstart-v12`라는 새 콘솔 앱을 만듭니다. 다음 `mvn` 명령을 입력하여 "hello world" Java 프로젝트를 만듭니다.

    # <a name="powershell"></a>[PowerShell](#tab/powershell)

    ```powershell
    mvn archetype:generate `
        --define interactiveMode=n `
        --define groupId=com.queues.quickstart `
        --define artifactId=queues-quickstart-v12 `
        --define archetypeArtifactId=maven-archetype-quickstart `
        --define archetypeVersion=1.4
    ```

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    mvn archetype:generate \
        --define interactiveMode=n \
        --define groupId=com.queues.quickstart \
        --define artifactId=queues-quickstart-v12 \
        --define archetypeArtifactId=maven-archetype-quickstart \
        --define archetypeVersion=1.4
    ```

    ---

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
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: packageInPathFormat, Value: com/queues/quickstart
    [INFO] Parameter: version, Value: 1.0-SNAPSHOT
    [INFO] Parameter: package, Value: com.queues.quickstart
    [INFO] Parameter: groupId, Value: com.queues.quickstart
    [INFO] Parameter: artifactId, Value: queues-quickstart-v12
    [INFO] Project created from Archetype in dir: C:\quickstarts\queues\queues-quickstart-v12
    [INFO] ------------------------------------------------------------------------
    [INFO] BUILD SUCCESS
    [INFO] ------------------------------------------------------------------------
    [INFO] Total time:  6.394 s
    [INFO] Finished at: 2019-12-03T09:58:35-08:00
    [INFO] ------------------------------------------------------------------------
    ```

1. 새로 만든 `queues-quickstart-v12` 디렉터리로 전환합니다.

   ```console
   cd queues-quickstart-v12
   ```

### <a name="install-the-package"></a>패키지 설치

텍스트 편집기에서 `pom.xml` 파일을 엽니다. 종속성 그룹에 다음 종속성 요소를 추가합니다.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-storage-queue</artifactId>
  <version>12.0.1</version>
</dependency>
```

### <a name="set-up-the-app-framework"></a>앱 프레임워크 설정

프로젝트 디렉터리에서 다음을 수행합니다.

1. `/src/main/java/com/queues/quickstart` 디렉터리로 이동합니다.
1. 편집기에서 `App.java` 파일을 엽니다.
1. `System.out.println("Hello, world");` 문을 삭제합니다.
1. `import` 지시문 추가

코드는 다음과 같습니다.

```java
package com.queues.quickstart;

/**
 * Azure Queue Storage client library v12 quickstart
 */
import com.azure.storage.queue.*;
import com.azure.storage.queue.models.*;
import java.io.*;
import java.time.*;

public class App
{
    public static void main( String[] args ) throws IOException
    {
    }
}
```

[!INCLUDE [storage-quickstart-credentials-include](../../../includes/storage-quickstart-credentials-include.md)]

## <a name="object-model"></a>개체 모델

Azure Queue Storage는 대량의 메시지를 저장하기 위한 서비스입니다. 큐 메시지의 크기는 최대 64KB입니다. 큐는 스토리지 계정의 용량 제한에 도달할 때까지 수백만 개의 메시지를 포함할 수 있습니다. 큐는 비동기적으로 처리할 작업의 백로그를 만드는 데 일반적으로 사용됩니다. Queue Storage는 다음 세 가지 유형의 리소스를 제공합니다.

- 스토리지 계정
- 스토리지 계정의 큐
- 큐 내의 메시지

다음 다이어그램에서는 이러한 리소스 간의 관계를 보여줍니다.

![Queue storage 아키텍처 다이어그램](./media/storage-queues-introduction/queue1.png)

다음 Java 클래스를 사용하여 이러한 리소스와 상호 작용합니다.

- [`QueueClientBuilder`](/java/api/com.azure.storage.queue.queueclientbuilder): `QueueClientBuilder` 클래스는 `QueueClient` 개체를 구성하고 인스턴스화합니다.
- [`QueueServiceClient`](/java/api/com.azure.storage.queue.queueserviceclient): `QueueServiceClient`를 사용하면 스토리지 계정의 모든 큐를 관리할 수 있습니다.
- [`QueueClient`](/java/api/com.azure.storage.queue.queueclient): `QueueClient` 클래스를 사용하면 개별 큐와 해당 메시지를 관리하고 조작할 수 있습니다.
- [`QueueMessageItem`](/java/api/com.azure.storage.queue.models.queuemessageitem): `QueueMessageItem` 클래스는 큐에서 [`ReceiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages)를 호출할 때 반환되는 개별 개체를 나타냅니다.

## <a name="code-examples"></a>코드 예제

다음 예제 코드 조각은 Java용 Azure Queue Storage 클라이언트 라이브러리를 사용하여 다음 작업을 수행하는 방법을 보여줍니다.

- [연결 문자열 가져오기](#get-the-connection-string)
- [큐 만들기](#create-a-queue)
- [큐에 메시지 추가](#add-messages-to-a-queue)
- [큐의 메시지 피킹(Peeking)](#peek-at-messages-in-a-queue)
- [큐의 메시지 업데이트](#update-a-message-in-a-queue)
- [큐에서 메시지 수신 및 삭제](#receive-and-delete-messages-from-a-queue)
- [큐 삭제](#delete-a-queue)

### <a name="get-the-connection-string"></a>연결 문자열 가져오기

다음 코드는 스토리지 계정에 대한 연결 문자열을 검색합니다. 연결 문자열은 [스토리지 연결 문자열 구성](#configure-your-storage-connection-string) 섹션에서 생성된 환경 변수에 저장됩니다.

다음 코드를 `main` 메서드 내에 추가합니다.

```java
System.out.println("Azure Queue Storage client library v12 - Java quickstart sample\n");

// Retrieve the connection string for use with the application. The storage
// connection string is stored in an environment variable on the machine
// running the application called AZURE_STORAGE_CONNECTION_STRING. If the environment variable
// is created after the application is launched in a console or with
// Visual Studio, the shell or application needs to be closed and reloaded
// to take the environment variable into account.
String connectStr = System.getenv("AZURE_STORAGE_CONNECTION_STRING");
```

### <a name="create-a-queue"></a>큐 만들기

새 큐의 이름을 결정합니다. 다음 코드는 큐 이름의 고유성을 보장하기 위해 큐 이름에 GUID 값을 추가합니다.

> [!IMPORTANT]
> 큐 이름은 소문자, 숫자 및 하이픈만 포함할 수 있으며 문자나 숫자로 시작해야 합니다. 각 하이픈의 앞과 뒤에는 하이픈이 아닌 문자가 있어야 합니다. 이름의 길이는 3~63자 사이여야 합니다. 큐 이름 지정에 대한 자세한 내용은 [큐 및 메타데이터 이름 지정](/rest/api/storageservices/naming-queues-and-metadata)을 참조하세요.

[`QueueClient`](/java/api/com.azure.storage.queue.queueclient) 클래스의 인스턴스를 만듭니다. 그런 다음, [`Create`](/java/api/com.azure.storage.queue.queueclient.create) 메서드를 호출하여 스토리지 계정에 큐를 만듭니다.

이 코드를 `main` 메서드의 끝에 추가합니다.

```java
// Create a unique name for the queue
String queueName = "quickstartqueues-" + java.util.UUID.randomUUID();

System.out.println("Creating queue: " + queueName);

// Instantiate a QueueClient which will be
// used to create and manipulate the queue
QueueClient queueClient = new QueueClientBuilder()
                                .connectionString(connectStr)
                                .queueName(queueName)
                                .buildClient();

// Create the queue
queueClient.create();
```

### <a name="add-messages-to-a-queue"></a>큐에 메시지 추가

다음 코드 조각은 [`sendMessage`](/java/api/com.azure.storage.queue.queueclient.sendmessage) 메서드를 호출하여 큐에 메시지를 추가합니다. `sendMessage` 호출에서 반환된 [`SendMessageResult`](/java/api/com.azure.storage.queue.models.sendmessageresult)도 저장합니다. 결과는 나중에 프로그램에서 메시지를 업데이트하는 데 사용됩니다.

이 코드를 `main` 메서드의 끝에 추가합니다.

```java
System.out.println("\nAdding messages to the queue...");

// Send several messages to the queue
queueClient.sendMessage("First message");
queueClient.sendMessage("Second message");

// Save the result so we can update this message later
SendMessageResult result = queueClient.sendMessage("Third message");
```

### <a name="peek-at-messages-in-a-queue"></a>큐의 메시지 피킹(Peeking)

[`peekMessages`](/java/api/com.azure.storage.queue.queueclient.peekmessages) 메서드를 호출하여 큐의 메시지를 피킹합니다. 이 메서드는 큐 앞에서 하나 이상의 메시지를 검색하지만 메시지의 표시 유형을 변경하지는 않습니다.

이 코드를 `main` 메서드의 끝에 추가합니다.

```java
System.out.println("\nPeek at the messages in the queue...");

// Peek at messages in the queue
queueClient.peekMessages(10, null, null).forEach(
    peekedMessage -> System.out.println("Message: " + peekedMessage.getMessageText()));
```

### <a name="update-a-message-in-a-queue"></a>큐의 메시지 업데이트

[`updateMessage`](/java/api/com.azure.storage.queue.queueclient.updatemessage) 메서드를 호출하여 메시지 콘텐츠를 업데이트합니다. 이 메서드는 메시지의 표시 시간 제한 및 콘텐츠를 변경할 수 있습니다. 메시지 콘텐츠는 크기가 최대 64KB인 UTF-8 인코딩 문자열이어야 합니다. 메시지의 새 콘텐츠와 함께 이전에 코드에 저장된 `SendMessageResult`를 사용하여 메시지 ID 및 수신 확인 팝을 전달합니다. 메시지 ID 및 수신 확인 팝은 업데이트할 메시지를 식별합니다.

```java
System.out.println("\nUpdating the third message in the queue...");

// Update a message using the result that
// was saved when sending the message
queueClient.updateMessage(result.getMessageId(),
                          result.getPopReceipt(),
                          "Third message has been updated",
                          Duration.ofSeconds(1));
```

### <a name="receive-and-delete-messages-from-a-queue"></a>큐에서 메시지 수신 및 삭제

[`receiveMessages`](/java/api/com.azure.storage.queue.queueclient.receivemessages) 메서드를 호출하여 이전에 추가한 메시지를 다운로드합니다. 또한 예제 코드는 메시지를 받아서 처리한 후 큐에서 메시지를 삭제합니다. 이 경우 처리는 콘솔에 메시지를 표시하는 것뿐입니다.

앱은 메시지를 수신하고 삭제하기 전에 `System.console().readLine();`을 호출하여 사용자 입력을 위해 일시 중지합니다. 리소스가 삭제되기 전에 [Azure Portal](https://portal.azure.com)에서 리소스가 제대로 생성되었는지 확인합니다. 명시적으로 삭제되지 않은 메시지는 다시 처리할 수 있도록 큐에 다시 표시됩니다.

이 코드를 `main` 메서드의 끝에 추가합니다.

```java
System.out.println("\nPress Enter key to receive messages and delete them from the queue...");
System.console().readLine();

// Get messages from the queue
queueClient.receiveMessages(10).forEach(
    // "Process" the message
    receivedMessage -> {
        System.out.println("Message: " + receivedMessage.getMessageText());

        // Let the service know we're finished with
        // the message and it can be safely deleted.
        queueClient.deleteMessage(receivedMessage.getMessageId(), receivedMessage.getPopReceipt());
    }
);
```

### <a name="delete-a-queue"></a>큐 삭제

다음 코드는 [`Delete`](/java/api/com.azure.storage.queue.queueclient.delete) 메서드를 사용하여 큐를 삭제하여 앱이 만든 리소스를 정리합니다.

이 코드를 `main` 메서드의 끝에 추가합니다.

```java
System.out.println("\nPress Enter key to delete the queue...");
System.console().readLine();

// Clean up
System.out.println("Deleting queue: " + queueClient.getQueueName());
queueClient.delete();

System.out.println("Done");
```

## <a name="run-the-code"></a>코드 실행

이 앱은 메시지 세 개를 만들어서 Azure 큐에 추가합니다. 이 코드는 큐의 메시지를 나열하고, 검색하여 삭제한 후 최종적으로 큐를 삭제합니다.

콘솔 창에서 애플리케이션 디렉터리로 이동한 다음, 애플리케이션을 빌드하고 실행합니다.

```console
mvn compile
```

그런 다음, 패키지를 빌드합니다.

```console
mvn package
```

다음 `mvn` 명령을 사용하여 앱을 실행합니다.

```console
mvn exec:java -Dexec.mainClass="com.queues.quickstart.App" -Dexec.cleanupDaemonThreads=false
```

앱의 출력은 다음 예제 출력과 유사합니다.

```output
Azure Queue Storage client library v12 - Java quickstart sample

Adding messages to the queue...

Peek at the messages in the queue...
Message: First message
Message: Second message
Message: Third message

Updating the third message in the queue...

Press Enter key to receive messages and delete them from the queue...

Message: First message
Message: Second message
Message: Third message has been updated

Press Enter key to delete the queue...

Deleting queue: quickstartqueues-fbf58f33-4d5a-41ac-ac0e-1a05d01c7003
Done
```

메시지를 받기 전에 앱이 일시 중지되면 [Azure Portal](https://portal.azure.com)에서 스토리지 계정을 확인합니다. 메시지가 큐에 있는지 확인합니다.

`Enter` 키를 눌러서 메시지를 받고 삭제합니다. 메시지가 표시되면 `Enter` 키를 다시 눌러서 큐를 삭제하고 데모를 마칩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Java 코드를 사용하여 큐를 만들고 큐에 메시지를 추가하는 방법을 알아보았습니다. 그런 다음, 메시지를 피킹하고 검색하고 삭제하는 방법을 알아보았습니다. 마지막으로 메시지 큐를 삭제하는 방법을 알아보았습니다.

자습서, 샘플, 빠른 시작 및 기타 설명서는 다음을 참조하세요.

> [!div class="nextstepaction"]
> [Java 클라우드 개발자용 Azure](/azure/developer/java/)

- 더 많은 Azure Queue Storage 샘플 앱은 [Java용 Azure Queue Storage 클라이언트 라이브러리 v12 - 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/storage/azure-storage-queue/src/samples/java/com/azure/storage/queue)을 참조하세요.
