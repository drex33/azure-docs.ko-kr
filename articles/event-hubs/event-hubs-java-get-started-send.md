---
title: Java(최신)를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기
description: 이 문서에서는 최신 azure-messaging-eventhubs 패키지를 사용하여 Azure Event Hubs와 이벤트를 주고 받는 Java 애플리케이션을 만드는 과정을 연습할 수 있습니다.
ms.topic: quickstart
ms.date: 04/30/2021
ms.custom: devx-track-java, mode-other
ms.openlocfilehash: e34cc9706dd17f4522db884ed1892afc92ab0e90
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133039121"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Java를 사용하여 Azure Event Hubs에서 이벤트 보내기 또는 받기(azure-messaging-eventhubs)

이 빠른 시작에서는 **azure-messaging-eventhubs** Java 패키지를 사용하여 이벤트 허브와 이벤트를 주고 받는 방법을 보여줍니다.

> [!IMPORTANT]
> 이 빠른 시작은 새로운 **azure-messaging-eventhubs** 패키지를 사용합니다. 이전 **azure-eventhubs** 및 **azure-eventhubs-eph** 패키지를 사용하는 빠른 시작은 [azure-eventhubs 및 azure-eventhubs-eph를 사용하여 이벤트 보내기 및 받기](event-hubs-java-get-started-send-legacy.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

Azure Event Hubs를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Event Hubs 개요](event-hubs-about.md)를 참조하세요.

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- **Microsoft Azure 구독**. Azure Event Hubs를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다.  기존 Azure 계정이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입하거나 [계정을 만들 때](https://azure.microsoft.com) MSDN 구독자 혜택을 사용할 수 있습니다.
- Java 개발 환경. 이 빠른 시작에서는 [Eclipse](https://www.eclipse.org/)를 사용합니다. JDK(Java Development Kit) 버전 8 이상이 필요합니다.
- **Event Hubs 네임스페이스 및 이벤트 허브 만들기** 첫 번째 단계에서는 [Azure Portal](https://portal.azure.com)을 사용하여 Event Hubs 형식의 네임스페이스를 만들고 애플리케이션에서 Event Hub와 통신하는 데 필요한 관리 자격 증명을 얻습니다. 네임스페이스 및 이벤트 허브를 만들려면 [이 문서](event-hubs-create.md)의 절차를 따릅니다. 그리고 다음 문서의 지침에 따라 **Event Hubs 네임스페이스에 대한 연결 문자열** 을 가져옵니다. [연결 문자열 가져오기](event-hubs-get-connection-string.md#get-connection-string-from-the-portal) 이 빠른 시작의 뒷부분에서 연결 문자열을 사용합니다.

## <a name="send-events"></a>이벤트 보내기

이 섹션에서는 이벤트 허브로 이벤트를 전송하는 Java 애플리케이션을 만드는 방법을 보여줍니다.

### <a name="add-reference-to-azure-event-hubs-library"></a>Azure Event Hubs 라이브러리에 대한 참조 추가

Event Hubs용 Java 클라이언트 라이브러리는 [Maven Central 리포지토리](https://search.maven.org/search?q=a:azure-messaging-eventhubs)에서 사용할 수 있습니다. Maven 프로젝트 파일 안에 다음 종속성 선언을 사용하여 이 라이브러리를 참조할 수 있습니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.7.0</version>
</dependency>
```

> [!NOTE]
> 버전을 Maven 리포지토리에 게시된 최신 버전으로 업데이트합니다.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

다음 샘플에서는 먼저 즐겨 찾는 Java 개발 환경에서 콘솔/셸 애플리케이션에 대한 새 Maven 프로젝트를 만듭니다. `Sender` 클래스를 추가하고 해당 클래스에 다음 코드를 추가합니다.

> [!IMPORTANT]
> `<Event Hubs namespace connection string>`을 Event Hubs 네임스페이스에 대한 연결 문자열로 업데이트합니다. `<Event hub name>`을 네임스페이스의 이벤트 허브의 이름으로 업데이트합니다.

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```

### <a name="add-code-to-publish-events-to-the-event-hub"></a>이벤트 허브에 이벤트를 게시하는 코드 추가

`publishEvents`라는 메서드를 `Sender` 클래스에 추가합니다.

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the EventData is bigger than the max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));

        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

프로그램을 빌드하고 오류가 없는지 확인합니다. 이 프로그램은 수신자 프로그램을 실행한 후에 실행됩니다.

## <a name="receive-events"></a>이벤트 수신

이 자습서의 코드는 전체 작동 중인 애플리케이션을 검사할 수 있는 [GitHub의 EventProcessorClient 샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java)을 기반으로 합니다.

> [!WARNING]
> Azure Stack Hub에서 이 코드를 실행하는 경우 특정 Storage API 버전을 대상으로 하지 않는 한 런타임 오류가 발생합니다. 이는 Event Hub SDK가 Azure에서 사용할 수 있는 최신 Azure Storage API를 사용하지만 Azure Stack Hub 플랫폼에서는 사용할 수 없기 때문입니다. Azure Stack Hub는 Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Azure Blob Storage SDK를 지원할 수도 있습니다. Azure Blob Storage를 검사점 저장소로 사용하는 경우 [Azure Stack Hub 빌드에 대해 지원되는 Azure Storage API 버전](/azure-stack/user/azure-stack-acs-differences?#api-version)을 확인하고 코드에서 해당 버전을 대상으로 지정합니다.
>
> 예를 들어 Azure Stack Hub 버전 2005에서 실행 중인 경우 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2019-02-02입니다. 기본적으로 Event Hubs SDK 클라이언트 라이브러리는 Azure에서 사용 가능한 가장 높은 버전을 사용합니다(SDK 릴리스 당시 2019-07-07). 이 경우 이 섹션의 다음 단계 외에도 스토리지 서비스 API 버전 2019-02-02를 대상으로 하는 코드를 추가해야 합니다. 특정 Storage API 버전을 대상으로 지정하는 방법에 대한 예제는 [GitHub에 대한 이 샘플](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java)을 참조하세요.


### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage 및 BLOB 컨테이너 만들기

이 빠른 시작에서는 Azure Storage(특히 Blob Storage)를 검사점 저장소로 사용합니다. 검사점은 이벤트 프로세서가 파티션 내에서 마지막으로 처리된 이벤트의 위치를 표시하거나 커밋하는 프로세스입니다. 검사점 표시는 일반적으로 이벤트를 처리하는 함수 내에서 수행됩니다. 검사점에 대한 자세한 내용은 [이벤트 프로세서](event-processor-balance-partition-load.md)를 참조하세요.

다음 단계에 따라 Azure Storage 계정을 만듭니다.

1. [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [스토리지 계정에 대한 연결 문자열 가져오기](../storage/common/storage-configure-connection-string.md)

    **연결 문자열** 과 **컨테이너 이름** 을 적어 둡니다. 수신 코드에서 사용합니다.

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Java 프로젝트에 Event Hubs 라이브러리 추가

pom.xml 파일에 다음 종속성을 추가합니다.

- [azure-messaging-eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.7.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.6.0</version>
    </dependency>
</dependencies>
```

1. Java 파일 맨 위에 다음 **import** 문을 추가합니다.

    ```java
    import com.azure.messaging.eventhubs.*;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.*;
    import com.azure.storage.blob.*;
    import java.util.function.Consumer;
    ```
2. `Receiver`라는 클래스를 만들고 다음 문자열 변수를 클래스에 추가합니다. 자리 표시자를 올바른 값으로 바꿉니다.

    > [!IMPORTANT]
    > 자리 표시자를 올바른 값으로 바꿉니다.
    > - `<Event Hubs namespace connection string>`을 Event Hubs 네임스페이스에 대한 연결 문자열로 바꿉니다. 업데이트
    > - `<Event hub name>`을 네임스페이스의 이벤트 허브의 이름으로 업데이트합니다.
    > - `<Storage connection string>`을 Azure 스토리지 계정에 대한 연결 문자열로 업데이트합니다.
    > - `<Storage container name>`을 Azure Blob Storage의 컨테이너의 이름으로 업데이트합니다.

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
3. 다음 `main` 메서드를 클래스에 추가합니다.

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString)
            .containerName(storageContainerName)
            .buildAsyncClient();

        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName)
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR)
            .processError(ERROR_HANDLER)
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));

        // Use the builder object to create an event processor client
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }
    ```
4. 이벤트 및 오류를 처리하는 두 개의 도우미 메서드(`PARTITION_PROCESSOR` 및 `ERROR_HANDLER`)를 `Receiver` 클래스에 추가합니다.

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        PartitionContext partitionContext = eventContext.getPartitionContext();
        EventData eventData = eventContext.getEventData();

        System.out.printf("Processing event from partition %s with sequence number %d with body: %s%n",
            partitionContext.getPartitionId(), eventData.getSequenceNumber(), eventData.getBodyAsString());

        // Every 10 events received, it will update the checkpoint stored in Azure Blob Storage.
        if (eventData.getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };

    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };
    ```

5. 프로그램을 빌드하고 오류가 없는지 확인합니다.

## <a name="run-the-applications"></a>애플리케이션 실행

1. **받는 사람** 애플리케이션 목록을 실행합니다.
1. 그런 다음, **보낸 사람** 애플리케이션을 실행합니다.
1. **받는 사람** 애플리케이션 창에서 보낸 사람 애플리케이션에 의해 게시된 이벤트를 확인합니다.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo
    Processing event from partition 0 with sequence number 332 with body: Bar
    ```
    
1. 받는 사람 애플리케이션 창에서 **ENTER** 키를 눌러 애플리케이션을 중지합니다.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo
    Processing event from partition 0 with sequence number 332 with body: Bar

    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>다음 단계

GitHub에서 다음 샘플을 참조하세요.

- [azure-messaging-eventhubs samples](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [azure-messaging-eventhubs-checkpointstore-blob 샘플](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).
