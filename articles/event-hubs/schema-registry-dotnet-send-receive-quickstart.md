---
title: 이벤트를 보내고 받을 때 스키마 유효성 검사 - AMQP 및 .NET
description: 이 문서에서는 스키마 레지스트리를 사용하는 스키마 유효성 검사를 통해 Azure Event Hubs에서 이벤트를 보내거나 받는 .NET Core 애플리케이션을 만드는 연습을 제공 합니다.
ms.topic: quickstart
ms.date: 10/20/2021
ms.custom: devx-track-csharp, ignite-fall-2021
ms.openlocfilehash: a0c14102f4db010b641685d89f2ac1bb09022ee9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029693"
---
# <a name="validate-schema-when-sending-and-receiving-events---amqp-and-net"></a>이벤트를 보내고 받을 때 스키마 유효성 검사 - AMQP 및 .NET 
이 빠른 시작에서는 **Azure.Messaging.EventHubs** .NET 라이브러리를 사용하는 스키마 유효성 검사로 이벤트 허브와 이벤트를 주고 받는 방법을 보여줍니다. 

## <a name="prerequisites"></a>사전 요구 사항
Azure Event Hubs를 처음 사용하는 경우 이 빠른 시작을 수행하기 전에 [Event Hubs 개요](event-hubs-about.md)를 참조하세요. 

이 빠른 시작을 완료하려면 다음 필수 구성 요소가 필요합니다.
- [Event Hubs 네임스페이스 및 이벤트 허브 만들기](event-hubs-create.md)의 빠른 시작 지침을 따릅니다.
- Event Hubs 네임스페이스에 대한 연결 문자열을 가져오려면 [연결 문자열 가져오기](event-hubs-get-connection-string.md)의 지침을 따릅니다. 현재 빠른 시작에서 사용할 다음 설정을 적어둡니다.
    - Event Hubs 네임스페이스에 대한 연결 문자열
    - 이벤트 허브의 이름입니다.
- **[.NET 빠른 시작](event-hubs-dotnet-standard-getstarted-send.md)을 완료하고** .NET를 사용하여 이벤트 허브에서 이벤트를 보내고 받는 방법에 익숙해질 수 있습니다. 이전에 .NET 빠른 시작을 이미 수행한 경우 이 단계를 건너뛸 수 있습니다. 
- **[Schema Registry를 사용하여 스키마 만들기](create-schema-registry.md)의 지침에 따라** 스키마 그룹 및 스키마를 만듭니다. 스키마를 만들 때 현재 빠른 시작의 [스키마 만들기](#create-a-schema) 문서에 있는 지침을 따르세요. 
- **Microsoft Visual Studio 2019** Azure Event Hubs 클라이언트 라이브러리는 C# 8.0에 도입된 새 기능을 사용합니다.  이전 C# 언어 버전으로 라이브러리를 계속 사용할 수 있지만 새 구문은 사용할 수 없습니다. 전체 구문을 사용하려면 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 이상 및 `latest`(으)로 설정된 [언어 버전](/dotnet/csharp/language-reference/configure-language-version#override-a-default)으로 컴파일하는 것이 좋습니다. Visual Studio를 사용하는 경우 Visual Studio 2019 이전 버전은 C# 8.0 프로젝트를 빌드하는 데 필요한 도구와 호환되지 않습니다. 체험 Community 버전을 비롯한 Visual Studio 2019는 [여기](https://visualstudio.microsoft.com/vs/)서 다운로드할 수 있습니다.


## <a name="create-a-schema"></a>스키마 만들기 
1. 스키마 레지스트리 포털을 사용하여 **contoso-sg** 라는 스키마 그룹을 만듭니다. Avro는 직렬화 형식으로 사용하고 호환성 모드에서는 사용하지 **않습니다**. 
1. 해당 스키마 그룹에서 다음 스키마 콘텐츠를 사용하여 스키마 이름:``Microsoft.Azure.Data.SchemaRegistry.example.Order``으로 새 Avro 스키마를 만듭니다. 

    ```json 
    {
      "namespace": "Microsoft.Azure.Data.SchemaRegistry.example",
      "type": "record",
      "name": "Order",
      "fields": [
        {
          "name": "id",
          "type": "string"
        },
        {
          "name": "amount",
          "type": "double"
        },
        {
          "name": "description",
          "type": "string"
        }
      ]
    } 
    ```

## <a name="produce-events-to-event-hubs-with-schema-validation"></a>스키마 유효성 검사를 사용하여 이벤트 허브에 이벤트 생성


### <a name="create-console-application-for-event-producer"></a>이벤트 생산자용 콘솔 애플리케이션 만들기 

1. Visual Studio 2019를 시작합니다. 
1. **새 프로젝트 만들기** 를 선택합니다. 
1. **새 프로젝트 만들기** 대화 상자에서 다음 단계를 수행합니다. 이 대화 상자가 표시되지 않으면 메뉴에서 **파일** 을 선택하고 **새로 만들기** 를 선택한 다음, **프로젝트** 를 선택합니다. 
    1. 프로그래밍 언어로 **C#** 을 선택합니다.
    1. 애플리케이션 유형으로 **콘솔** 을 선택합니다. 
    1. 결과 목록에서 **콘솔 애플리케이션** 을 선택합니다. 
    1. 그다음에 **다음** 을 선택합니다. 

        :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/new-send-project.png" alt-text="새 프로젝트 대화 상자를 보여 주는 이미지.":::
1. 프로젝트 이름으로 **OrderProducer**, 솔루션 이름으로 **SRQuickStart** 를 입력한 다음, **확인** 을 선택하여 프로젝트를 만듭니다. 


### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. 다음 명령을 실행하여 **Azure.Messaging.EventHubs** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. 생산자 애플리케이션을 인증하여 [여기](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio)에 표시된 대로 Visual Studio를 통해 Azure에 연결합니다.  

### <a name="code-generation-using-the-avro-schema"></a>Avro 스키마를 사용하여 코드 생성  
1. 동일한 스키마 콘텐츠를 사용하고 OrderProducer 프로젝트 내에 Avro 스키마 파일 ``Order.avsc`` 파일을 만들 수 있습니다. 
1. 그런 다음 이 스키마 파일을 사용하여 .NET에 대한 코드를 생성할 수 있습니다. 코드 생성을 위해 [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/)과 같은 외부 코드 생성 도구를 사용할 수 있습니다. (예를 들어 `` avrogen -s .\Order.avsc ``를 실행하여 코드를 생성할 수 있습니다). 
1. 코드를 생성한 후에는 프로젝트 내에서 해당 C# 형식을 사용할 수 있어야 합니다. 위의 Avro 스키마의 경우 ``Microsoft.Azure.Data.SchemaRegistry.example`` 네임스페이스에 C# 형식을 생성 합니다. 


### <a name="write-code-to-serialize-and-send-events-to-the-event-hub"></a>이벤트를 직렬화하고 이벤트 허브로 전송하는 코드를 작성합니다. 

1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System;
    using System.IO;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    using System.Threading.Tasks;
    ```
1. 또한 아래와 같이 ``Order`` 스키마와 관련하여 생성된 형식을 가져올 수 있습니다. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Event Hubs 연결 문자열 및 이벤트 허브 이름에 대한 `Program` 클래스에 상수를 추가합니다.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

    > [!NOTE]
    > 자리 표시자 값을 네임스페이스에 대한 연결 문자열, 이벤트 허브의 이름 및 스키마 그룹으로 바꿉니다.
3. `Program` 클래스에 다음 정적 속성을 추가합니다. 코드 주석을 참조하세요. 

    ```csharp
        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.
        static EventHubProducerClient producerClient;    
    ```
1. `Main` 메서드를 다음 `async Main` 메서드로 바꿉니다. 자세한 내용은 코드 주석을 참조하세요. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            producerClient = new EventHubProducerClient(connectionString, eventHubName); 
    
            // Create a schema registry client that you can use to serialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();
            
            // Create a new order object using the generated type/class 'Order'. 
            var sampleOrder = new Order { id = "12345", amount = 55.99, description = "This is a sample order." };
    
            using var memoryStream = new MemoryStream();
            // Create an Avro object serializer using the Schema Registry client object. 
            var producerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = true });
    
            // Serialize events data to the memory stream object. 
            producerSerializer.Serialize(memoryStream, sampleOrder, typeof(Order), CancellationToken.None);
    
            byte[] _memoryStreamBytes;
            _memoryStreamBytes = memoryStream.ToArray();
    
            // Create event data with serialized data and add it to an event batch. 
            eventBatch.TryAdd(new EventData(_memoryStreamBytes));
    
            // Send serilized event data to event hub. 
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 1 order has been published.");
        }
    ```
5. 프로그램을 빌드하고 오류가 없는지 확인합니다.
6. 프로그램을 실행하고 확인 메시지가 나타날 때까지 기다립니다. 

    ```csharp
    A batch of 1 order has been published.
    ```
1. Azure Portal에서 이벤트 허브가 이벤트를 받았는지 확인할 수 있습니다. **메트릭** 섹션에서 **메시지** 보기로 전환합니다. 페이지를 새로 고쳐 차트를 업데이트합니다. 메시지가 수신되었다는 내용이 표시될 때까지 몇 초 정도 걸릴 수 있습니다. 

    :::image type="content" source="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png" alt-text="이벤트 허브가 이벤트를 받았는지 확인하는 Azure Portal 페이지의 이미지." lightbox="./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png":::




## <a name="consume-events-from-event-hubs-with-schema-validation"></a>스키마 유효성 검사를 사용하여 이벤트 허브의 이벤트 사용
이 섹션에서는 이벤트 허브에서 이벤트를 수신하고 스키마 레지스트리를 사용하여 이벤트 데이터를 역직렬화하는 .NET Core 콘솔 애플리케이션을 작성하는 방법을 보여줍니다. 


### <a name="create-consumer-application"></a>소비자 애플리케이션 만들기

1. 솔루션 탐색기 창에서 **SRQuickStart** 솔루션을 마우스 오른쪽 단추로 클릭하고, **추가** 를 가리킨 다음, **새 프로젝트** 를 선택합니다. 
1. **콘솔 애플리케이션** 을 선택하고 **다음** 을 선택합니다. 
1. **프로젝트 이름** 에 **OrderConsumer** 를 입력하고 **만들기** 를 선택합니다. 
1. **솔루션 탐색기** 창에서 **OrderConsumer** 를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다. 

### <a name="add-the-event-hubs-nuget-package"></a>Event Hubs NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. **패키지 관리자 콘솔** 창에서 **기본 프로젝트** 로 **OrderConsumer** 가 선택되어 있는지 확인합니다. 그렇지 않은 경우 드롭다운 목록을 사용하여 **OrderConsumer** 를 선택합니다.


1. 다음 명령을 실행하여 필요한 NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    Install-Package Azure.Messaging.EventHubs.Processor
    Install-Package Azure.Identity
    Install-Package Microsoft.Azure.Data.SchemaRegistry.ApacheAvro -Version 1.0.0-beta.2
    Install-Package Azure.ResourceManager.Compute -Version 1.0.0-beta.1

    ```
1. 생산자 애플리케이션을 인증하여 [여기](/dotnet/api/overview/azure/identity-readme#authenticating-via-visual-studio)에 표시된 대로 Visual Studio를 통해 Azure에 연결합니다. 



### <a name="code-generation-using-the-avro-schema"></a>Avro 스키마를 사용하여 코드 생성  
1. 동일한 스키마 콘텐츠를 사용하고 ``OrderProducer`` 프로젝트 내에 Avro 스키마 파일 ``Order.avsc`` 파일을 만들 수 있습니다. 
1. 그런 다음 이 스키마 파일을 사용하여 .NET에 대한 코드를 생성할 수 있습니다. 이를 위해 [avrogen](https://www.nuget.org/packages/Apache.Avro.Tools/)과 같은 외부 코드 생성 도구를 사용할 수 있습니다. (예를 들어 `` avrogen -s .\Order.avsc ``를 실행하여 코드를 생성할 수 있습니다). 
1. 코드를 생성한 후에는 프로젝트 내에서 해당 C# 형식을 사용할 수 있어야 합니다. 위의 Avro 스키마의 경우 ``Microsoft.Azure.Data.SchemaRegistry.example`` 네임스페이스에 C# 형식을 생성 합니다. 


### <a name="write-code-to-receive-events-and-deserialize-them-using-schema-registry"></a>스키마 레지스트리를 사용하여 이벤트를 수신하고 역직렬화하는 코드 작성


1. **Program.cs** 파일 위에 다음 `using` 문을 추가합니다.

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using System.Threading.Tasks;
    using System.Threading;
    using Azure.Data.SchemaRegistry;
    using Azure.Identity;
    using Microsoft.Azure.Data.SchemaRegistry.ApacheAvro;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. 또한 아래와 같이 ``Order`` 스키마와 관련하여 생성된 형식을 가져올 수 있습니다. 
    ```csharp
    using Microsoft.Azure.Data.SchemaRegistry.example;   
    ```

2. Event Hubs 연결 문자열 및 이벤트 허브 이름에 대한 `Program` 클래스에 상수를 추가합니다.  

    ```csharp
        // connection string to the Event Hubs namespace
        private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";

        // name of the event hub
        private const string eventHubName = "<EVENT HUB NAME>";

        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
        
        // Schema Registry endpoint 
        private const string schemaRegistryEndpoint = "<EVENT HUBS NAMESPACE>.servicebus.windows.net>";
            
        // name of the consumer group   
        private const string schemaGroup = "<SCHEMA GROUP>";

    ```

3. `Program` 클래스에 다음 정적 속성을 추가합니다. 

    ```csharp
        static BlobContainerClient storageClient;

        // The Event Hubs client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when events are being published or read regularly.        
        static EventProcessorClient processor;    
    ```
1. `Main` 메서드를 다음 `async Main` 메서드로 바꿉니다. 자세한 내용은 코드 주석을 참조하세요. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }
    ```
1. 이제 스키마 레지스트리를 사용하여 이벤트 역직렬화 논리를 포함하는 다음과 같은 이벤트 처리기 메서드를 추가합니다. 
    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Create a schema registry client that you can use to deserialize and validate data.  
            var schemaRegistryClient = new SchemaRegistryClient(endpoint: schemaRegistryEndpoint, credential: new DefaultAzureCredential());
    
            // Retrieve event data and convert it to a byte array. 
            byte[] _memoryStreamBytes = eventArgs.Data.Body.ToArray();
            using var consumerMemoryStream = new MemoryStream(_memoryStreamBytes);
    
            var consumerSerializer = new SchemaRegistryAvroObjectSerializer(schemaRegistryClient, schemaGroup, new SchemaRegistryAvroObjectSerializerOptions { AutoRegisterSchemas = false });
            consumerMemoryStream.Position = 0;
    
            // Deserialize event data and create order object using schema registry. 
            Order sampleOrder = (Order)consumerSerializer.Deserialize(consumerMemoryStream, typeof(Order), CancellationToken.None);
            Console.WriteLine("Received - Order ID: " + sampleOrder.id);                 
    
            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);   
        }

    ```

 
1. 이제 클래스에 다음과 같은 오류 처리기 메서드를 추가합니다. 

    ```csharp
        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }
  
    ```
1. 프로그램을 빌드하고 오류가 없는지 확인합니다.


6. 수신기 애플리케이션을 실행합니다. 
1. 이벤트가 수신되었다는 메시지가 표시됩니다. 

    ```bash
    Received - Order ID: 12345
    ```
    이러한 이벤트는 앞에서 송신기 프로그램을 실행하여 이벤트 허브로 보낸 세 개 이벤트입니다. 


## <a name="next-steps"></a>다음 단계
추가 정보는 [.NET용 Azure 스키마 레지스트리 클라이언트 라이브러리](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/schemaregistry/Azure.Data.SchemaRegistry)를 참조하세요. 
