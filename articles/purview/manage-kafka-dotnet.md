---
title: .NET을 사용하여 Event Hubs를 통해 Azure Purview의 Atlas Kafka 토픽에 메시지를 게시하고 메시지 처리
description: 이 문서에서는 최신 Azure.Messaging.EventHubs 패키지를 사용하여 Purview의 Atlas Kafka 토픽과 이벤트를 주고 받는 .NET Core 애플리케이션을 만드는 과정을 연습할 수 있습니다.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 09/27/2021
ms.custom: mode-other
ms.openlocfilehash: b00b1d18ee6941af65a55f3eb82c2616a32f6f67
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133055476"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>.NET을 사용하여 Event Hubs를 통해 Azure Purview의 Atlas Kafka 토픽에 메시지를 게시하고 메시지 처리 
이 빠른 시작에서는 **Azure.Messaging.EventHubs** .NET 라이브러리를 사용하여 이벤트 허브와 Azure Purview의 Atlas Kafka 토픽의 이벤트를 주고 받는 방법을 보여 줍니다. 

> [!IMPORTANT]
> Purview 계정 생성 과정에서 관리되는 이벤트 허브가 생성됩니다. [Purview 계정 만들기](create-catalog-portal.md)를 참조하세요. 이벤트 허브 kafka 토픽 ATLAS_HOOK에 메시지를 게시할 수 있으며, 그러면 Purview에서 이를 사용하고 처리합니다. Purview는 이벤트 허브 kafka 토픽 ATLAS_ENTITIES에 엔터티 변경 내용을 알리며, 사용자는 이를 사용하고 처리할 수 있습니다. 이 빠른 시작에서는 새 **Azure.Messaging.EventHubs** 라이브러리를 사용합니다. 


## <a name="prerequisites"></a>사전 요구 사항
Azure Event Hubs를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Event Hubs 개요](../event-hubs/event-hubs-about.md)를 참조하세요. 

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.

- **Microsoft Azure 구독**. Azure Event Hubs를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다.  기존 Azure 계정이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입하거나 [계정을 만들 때](https://azure.microsoft.com) MSDN 구독자 혜택을 사용할 수 있습니다.
- **Microsoft Visual Studio 2019** Azure Event Hubs 클라이언트 라이브러리는 C# 8.0에 도입된 새 기능을 사용합니다.  이전 C# 언어 버전으로 라이브러리를 계속 사용할 수 있지만 새 구문은 사용할 수 없습니다. 전체 구문을 사용하려면 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 이상으로 컴파일하고 [언어 버전](/dotnet/csharp/language-reference/configure-language-version#override-a-default)을 `latest`로 설정하는 것이 좋습니다. Visual Studio를 사용하는 경우 Visual Studio 2019 이전 버전은 C# 8.0 프로젝트를 빌드하는 데 필요한 도구와 호환되지 않습니다. 체험 Community 버전을 비롯한 Visual Studio 2019는 [여기](https://visualstudio.microsoft.com/vs/)서 다운로드할 수 있습니다.

## <a name="publish-messages-to-purview"></a>Purview에 메시지 게시 
이 섹션에서는 이벤트 허브 kafka 토픽 **ATLAS_HOOK** 를 통해 Purview로 이벤트를 전송하는 .NET Core 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. 

## <a name="create-a-visual-studio-project"></a>Visual Studio 프로젝트 만들기

다음으로, Visual Studio에서 C# .NET 콘솔 애플리케이션을 만듭니다.

1. **Visual Studio** 를 시작합니다.
2. 시작 창에서 **새 프로젝트 만들기** > **콘솔 앱(.NET Framework)** 을 선택합니다. .NET 버전 4.5.2 이상이 필요합니다.
3. **프로젝트 이름** 에 **PurviewKafkaProducer** 를 입력합니다.
4. **만들기** 를 선택하여 프로젝트를 만듭니다.

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

1. Visual Studio 2019를 시작합니다. 
1. **새 프로젝트 만들기** 를 선택합니다. 
1. **새 프로젝트 만들기** 대화 상자에서 다음 단계를 수행합니다. 이 대화 상자가 표시되지 않으면 메뉴에서 **파일** 을 선택하고 **새로 만들기** 를 선택한 다음, **프로젝트** 를 선택합니다. 
    1. 프로그래밍 언어로 **C#** 을 선택합니다.
    1. 애플리케이션 유형으로 **콘솔** 을 선택합니다. 
    1. 결과 목록에서 **콘솔 앱(.NET Core)** 을 선택합니다. 
    1. 그다음에 **다음** 을 선택합니다. 


### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. 다음 명령을 실행하여 **Azure.Messaging.EventHubs** NuGet 패키지 및 **Azure.Messaging.EventHubs.Producer** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
    ```    

### <a name="write-code-to-send-messages-to-the-event-hub"></a>이벤트 허브에 메시지를 전송하는 코드 작성

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Event Hubs 연결 문자열 및 이벤트 허브 이름에 대한 `Program` 클래스에 상수를 추가합니다. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Purview 계정의 속성 탭에서 Atlas kafka 엔드포인트 기본/보조 연결 문자열을 살펴보면 Purview 계정과 연결된 이벤트 허브 네임스페이스를 가져올 수 있습니다.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="이벤트 허브 네임스페이스":::

    Purview에 메시지를 보내려면 이벤트 허브 이름이 **ATLAS_HOOK** 여야 합니다.

3. 메서드를 `Main` 다음 `async Main` 메서드로 바꾸고 Purview로 메시지를 밀어넣는 `async ProduceMessage`를 추가합니다. 자세한 내용은 코드 주석을 참조하세요. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. 프로그램을 빌드하고 오류가 없는지 확인합니다.
6. 프로그램을 실행하고 확인 메시지가 나타날 때까지 기다립니다. 

    > [!NOTE]
    > 정보 제공을 위한 주석을 비롯한 전체 소스 코드는 [GitHub의 이 파일](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)을 참조하세요.

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>엔터티 JSON 메시지 만들기 샘플을 사용하여 두 개의 열이 있는 sql 테이블을 만듭니다.

```json
    
    {
    "msgCreatedBy":"nayenama",
    "message":{
        "type":"ENTITY_CREATE_V2",
        "user":"admin",
        "entities":{
            "entities":[
                {
                    "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "temporary":false,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table added via Kafka"
                    },
                    "relationshipAttributes":{
                        "columns":[
                            {
                                "guid":"-1102395743156037",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid":"-1102395743156038",
                                "typeName":"azure_sql_column",
                                "uniqueAttributes":{
                                    "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                    },
                    "guid":"-1102395743156036",
                    "version":0
                }
            ],
            "referredEntities":{
                "-1102395743156037":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                        "precision":23,
                        "length":8,
                        "description":"Sales Order ID",
                        "scale":3,
                        "name":"OrderID",
                        "data_type":"int"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156037",
                    "version":2
                },
                "-1102395743156038":{
                    "typeName":"azure_sql_column",
                    "attributes":{
                        "owner":null,
                        "userTypeId":61,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                        "description":"Sales Order Date",
                        "scale":3,
                        "name":"OrderDate",
                        "data_type":"datetime"
                    },
                    "relationshipAttributes":{
                        "table":{
                            "guid":"-1102395743156036",
                            "typeName":"azure_sql_table",
                            "entityStatus":"ACTIVE",
                            "displayText":"SalesOrderTable",
                            "uniqueAttributes":{
                                "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                            }
                        }
                    },
                    "guid":"-1102395743156038",
                    "status":"ACTIVE",
                    "createdBy":"ServiceAdmin",
                    "version":0
                }
            }
        }
    },
    "version":{
        "version":"1.0.0"
    },
    "msgCompressionKind":"NONE",
    "msgSplitIdx":1,
    "msgSplitCount":1
}


``` 

## <a name="consume-messages-from-purview"></a>Purview의 메시지 사용
이 섹션에서는 이벤트 처리기를 사용하여 이벤트 허브에서 메시지를 수신하는 .NET Core 콘솔 애플리케이션을 작성하는 방법을 보여줍니다. ATLAS_ENTITIES 이벤트 허브를 사용하여 Purview에서 메시지를 수신해야 합니다. 이벤트 프로세서는 이러한 이벤트 허브에서 영구 검사점 및 병렬 수신을 관리하여 이벤트 허브의 이벤트 수신을 간소화합니다. 

> [!WARNING]
> Azure Stack Hub에서 이 코드를 실행하는 경우 특정 Storage API 버전을 대상으로 하지 않는 한 런타임 오류가 발생합니다. 이는 Event Hub SDK가 Azure에서 사용할 수 있는 최신 Azure Storage API를 사용하지만 Azure Stack Hub 플랫폼에서는 사용할 수 없기 때문입니다. Azure Stack Hub는 Azure에서 일반적으로 사용할 수 있는 것과 다른 버전의 Storage Blob SDK를 지원할 수도 있습니다. Azure Blob Storage를 검사점 저장소로 사용하는 경우 [Azure Stack Hub 빌드에 대해 지원되는 Azure Storage API 버전](/azure-stack/user/azure-stack-acs-differences?#api-version)을 확인하고 코드에서 해당 버전을 대상으로 지정합니다. 
>
> 예를 들어 Azure Stack Hub 버전 2005에서 실행 중인 경우 스토리지 서비스에 사용할 수 있는 가장 높은 버전은 2019-02-02입니다. 기본적으로 Event Hubs SDK 클라이언트 라이브러리는 Azure에서 사용 가능한 가장 높은 버전을 사용합니다(SDK 릴리스 당시 2019-07-07). 이 경우 이 섹션의 다음 단계 외에도 스토리지 서비스 API 버전 2019-02-02를 대상으로 하는 코드를 추가해야 합니다. 특정 Storage API 버전을 대상으로 지정하는 방법에 대한 예제는 [GitHub에 대한 이 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)을 참조하세요. 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Azure Storage 및 BLOB 컨테이너 만들기
이 빠른 시작에서는 Azure Storage를 검사점 저장소로 사용합니다. 다음 단계에 따라 Azure Storage 계정을 만듭니다. 

1. [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Blob 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [스토리지 계정에 대한 연결 문자열 가져오기](../storage/common/storage-configure-connection-string.md)

    연결 문자열과 컨테이너 이름을 적어 둡니다. 수신 코드에서 사용합니다. 


### <a name="create-a-project-for-the-receiver"></a>수신기에 대한 프로젝트 만들기

1. 솔루션 탐색기 창에서 **EventHubQuickStart** 솔루션을 길게 선택(또는 마우스 오른쪽 단추로 클릭)하고, **추가** 를 가리키고, **새 프로젝트** 를 선택합니다. 
1. **콘솔 앱(.NET Core)** 을 선택하고, **다음** 을 선택합니다. 
1. **프로젝트 이름** 에 **PurviewKafkaConsumer** 를 입력하고 **만들기** 를 선택합니다. 

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. 다음 명령을 실행하여 **Azure.Messaging.EventHubs** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. 다음 명령을 실행하여 **Azure.Messaging.EventHubs.Processor** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Main 메서드 업데이트 

1. **Program.cs** 파일 맨 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Event Hubs 연결 문자열 및 이벤트 허브 이름에 대한 `Program` 클래스에 상수를 추가합니다. 대괄호 안의 자리 표시자를 이벤트 허브를 만들 때 얻은 적절한 값으로 바꿉니다. 대괄호 안의 자리 표시자를 이벤트 허브 및 스토리지 계정을 만들 때 얻은 적절한 값으로 바꿉니다(액세스 키 - 기본 연결 문자열). `{Event Hubs namespace connection string}`이 네임스페이스 수준 연결 문자열이며 이벤트 허브 문자열이 아님을 확인합니다.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
    
    Purview 계정의 속성 탭에서 Atlas kafka 엔드포인트 기본/보조 연결 문자열을 살펴보면 Purview 계정과 연결된 이벤트 허브 네임스페이스를 가져올 수 있습니다.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="이벤트 허브 네임스페이스":::

    Purview에 메시지를 보내려면 이벤트 허브 이름이 **ATLAS_ENTITIES** 여야 합니다.

3. `Main` 메서드를 다음 `async Main` 메서드로 바꿉니다. 자세한 내용은 코드 주석을 참조하세요. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. 이제 다음 이벤트 및 오류 처리기 메서드를 클래스에 추가합니다. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. 프로그램을 빌드하고 오류가 없는지 확인합니다.

    > [!NOTE]
    > 정보 제공을 위한 주석을 비롯한 전체 소스 코드는 [GitHub의 이 파일](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)을 참조하세요.
6. 수신기 애플리케이션을 실행합니다. 

### <a name="sample-message-received-from-purview"></a>Purview에서 수신된 샘플 메시지

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas는 현재 **ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2** 같은 작업 유형을 지원합니다. Purview에 메시지를 밀어넣는 기능은 현재 기본적으로 사용하도록 설정되어 있습니다. Purview에서 데이터를 읽는 과정이 포함된 시나리오의 경우 허용 목록에 포함되어야 하므로 직접 문의하세요. (구독 ID와 Purview 계정의 이름을 제공합니다.)


## <a name="next-steps"></a>다음 단계
GitHub에서 다음 샘플을 확인합니다. 

- [GitHub에 대한 Event Hubs 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [GitHub의 이벤트 처리기 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas의 알림 소개](https://atlas.apache.org/2.0.0/Notifications.html)
