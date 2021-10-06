---
title: Azure Service Bus 큐 시작(Azure.Messaging.ServiceBus)
description: 이 자습서에서는 Service Bus 큐에서 메시지를 보내고 받는 .NET Core C# 애플리케이션을 만듭니다.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 08/16/2021
ms.custom: contperf-fy21q4
ms.openlocfilehash: b84ff908404c2d18f86ddd63fa14a9854fdf72d6
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079336"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Azure Service Bus 큐에서 메시지 보내기 및 받기(.NET)
이 빠른 시작에서는 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) .NET 라이브러리를 사용하여 Service Bus 큐에 메시지를 보내고 받는 방법을 보여 줍니다.

> [!NOTE]
> [GitHub의 .NET용 Azure SDK 리포지토리](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)에서 Azure Service Bus에 대한 추가 .NET 샘플을 찾을 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure 구독**. Azure Service Bus를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다. 기존 Azure 계정이 없으면 [평가판](https://azure.microsoft.com/free/)에 등록할 수 있습니다.
- **Microsoft Visual Studio 2019** Azure Service Bus 클라이언트 라이브러리는 C# 8.0에 도입된 새 기능을 사용합니다.  이전 C# 언어 버전으로 라이브러리를 계속 사용할 수 있지만 새 구문은 사용할 수 없습니다. 전체 구문을 사용하려면 .NET Core SDK 3.0 이상 및 `latest`로 설정된 언어 버전으로 컴파일하는 것이 좋습니다. Visual Studio를 사용하는 경우 Visual Studio 2019 이전 버전은 C# 8.0 프로젝트를 빌드하는 데 필요한 도구와 호환되지 않습니다.
- **Service Bus 네임스페이스 및 큐 만들기**. [Azure Portal을 사용하여 Service Bus 큐 만들기](service-bus-quickstart-portal.md) 문서의 단계에 따라 Service Bus 네임스페이스 및 큐를 만듭니다. 

    > [!IMPORTANT]
    > Service Bus 네임스페이스에 대한 [**기본 연결 문자열**](./service-bus-quickstart-topics-subscriptions-portal.md#get-the-connection-string) 및 만든 **큐** 의 이름을 적어 둡니다. 이 자습서의 뒷부분에서 사용됩니다. 


## <a name="send-messages"></a>메시지 보내기
이 섹션에서는 Service Bus 큐로 메시지를 전송하는 .NET Core 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. 

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

1. Visual Studio 2019를 시작합니다. 
1. **새 프로젝트 만들기** 를 선택합니다. 
1. **새 프로젝트 만들기** 대화 상자에서 다음 단계를 수행합니다. 이 대화 상자가 표시되지 않으면 메뉴에서 **파일** 을 선택하고 **새로 만들기** 를 선택한 다음, **프로젝트** 를 선택합니다. 
    1. 프로그래밍 언어로 **C#** 을 선택합니다.
    1. 애플리케이션 유형으로 **콘솔** 을 선택합니다. 
    1. 결과 목록에서 **콘솔 애플리케이션** 을 선택합니다. 
    1. 그다음에 **다음** 을 선택합니다. 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="C# 및 콘솔이 선택된 새 프로젝트 만들기 대화 상자를 보여 주는 이미지":::
1. 프로젝트 이름으로 **QueueSender** 를 입력하고 솔루션 이름으로 **ServiceBusQueueQuickStart** 를 입력한 후 **다음** 을 선택합니다. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/project-solution-names.png" alt-text="새 프로젝트 구성 대화 상자의 솔루션 및 프로젝트 이름을 보여 주는 이미지":::
1. **추가 정보** 페이지에서 **만들기** 를 선택하여 솔루션 및 프로젝트를 만듭니다. 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. 다음 명령을 실행하여 **Azure.Messaging.ServiceBus** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-queue"></a>메시지를 큐에 보내는 코드 추가

1. **Program.cs** 의 코드를 다음 코드로 바꿉니다. 다음은 코드의 중요한 단계입니다.  
    1. 네임스페이스에 대한 기본 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
    1. [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체에서 [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 메서드를 호출하여 특정 Service Bus 큐에 대한 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 개체를 만듭니다.     
    1. [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync) 메서드를 사용하여 [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) 개체를 만듭니다.
    1. [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage)를 사용하여 일괄 처리에 메시지를 추가합니다. 
    1. [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) 메서드를 사용하여 메시지 일괄 처리를 Service Bus 큐에 보냅니다.
    
        자세한 내용은 코드 주석을 참조하세요.
    
        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace QueueSender
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
                // name of your Service Bus queue
                static string queueName = "<QUEUE NAME>";
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the sender used to publish messages to the queue
                static ServiceBusSender sender;
        
                // number of messages to be sent to the queue
                private const int numOfMessages = 3;
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
                    // Create the clients that we'll use for sending and processing messages.
                    client = new ServiceBusClient(connectionString);
                    sender = client.CreateSender(queueName);
        
                    // create a batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();
        
                    for (int i = 1; i <= numOfMessages; i++)
                    {
                        // try adding a message to the batch
                        if (!messageBatch.TryAddMessage(new ServiceBusMessage($"Message {i}")))
                        {
                            // if it is too large for the batch
                            throw new Exception($"The message {i} is too large to fit in the batch.");
                        }
                    }
        
                    try
                    {
                        // Use the producer client to send the batch of messages to the Service Bus queue
                        await sender.SendMessagesAsync(messageBatch);
                        Console.WriteLine($"A batch of {numOfMessages} messages has been published to the queue.");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await sender.DisposeAsync();
                        await client.DisposeAsync();
                    }
        
                    Console.WriteLine("Press any key to end the application");
                    Console.ReadKey();
                }
            }
        }   
        ``` 
1. `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 기본 연결 문자열로 바꿉니다. 그런 후 `<QUEUE NAME>`을 큐의 이름으로 바꿉니다.
1. 프로그램을 빌드하고 오류가 없는지 확인합니다. 
1. 프로그램을 실행하고 확인 메시지가 나타날 때까지 기다립니다.
    
    ```bash
    A batch of 3 messages has been published to the queue
    ```
1. Azure Portal에서 다음 단계를 수행합니다.
    1. Service Bus 네임스페이스로 이동합니다. 
    1. **개요** 페이지의 아래쪽 가운데 창에서 큐를 선택합니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/select-queue.png" alt-text="Azure Portal에서 큐가 선택된 Service Bus 네임스페이스 페이지를 보여 주는 이미지" lightbox="./media/service-bus-dotnet-get-started-with-queues/select-queue.png":::
    1. **기본 정보** 섹션의 값을 확인합니다.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="받은 메시지 수와 큐의 크기를 보여 주는 이미지" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    다음 값을 확인합니다.
    - 큐에 대한 **활성** 메시지 수 값은 이제 **3** 입니다. 메시지를 검색하지 않고 이 보낸 사람 앱을 실행할 때마다 이 값이 3씩 증가합니다.
    - 큐의 **현재 크기** 는 앱이 큐에 메시지를 추가할 때마다 증가합니다.
    - 아래쪽 **메트릭** 섹션의 **메시지** 차트에서 해당 큐에 대해 3개의 들어오는 메시지가 있음을 확인할 수 있습니다. 

## <a name="receive-messages"></a>메시지 받기
이 섹션에서는 큐에서 메시지를 수신하는 .NET Core 콘솔 애플리케이션을 만듭니다. 

### <a name="create-a-project-for-the-receiver"></a>수신기에 대한 프로젝트 만들기

1. 솔루션 탐색기 창에서 **ServiceBusQueueQuickStart** 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가를** 가리킨 다음, **새 프로젝트** 를 선택합니다. 
1. **콘솔 애플리케이션** 을 선택하고 **다음** 을 선택합니다. 
1. **프로젝트 이름** 으로 **QueueReceiver** 를 입력하고 **만들기** 를 선택합니다. 
1. **솔루션 탐색기** 창에서 **QueueReceiver** 를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다. 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. **패키지 관리자 콘솔** 창에서 **기본 프로젝트** 로 **QueueReceiver** 가 선택되어 있는지 확인합니다. 그렇지 않은 경우 드롭다운 목록을 사용하여 **QueueReceiver** 를 선택합니다.

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/package-manager-console.png" alt-text="패키지 관리자 콘솔에서 선택한 QueueReceiver 프로젝트를 보여주는 스크린샷":::
1. 다음 명령을 실행하여 **Azure.Messaging.ServiceBus** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-the-code-to-receive-messages-from-the-queue"></a>큐에서 메시지를 받는 코드 추가
이 섹션에서는 큐에서 메시지를 검색하는 코드를 추가합니다.

1. **Program.cs** 의 코드를 다음 코드로 바꿉니다. 다음은 코드의 중요한 단계입니다.
    다음은 코드의 중요한 단계입니다.
    1. 네임스페이스에 대한 기본 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
    1. [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체에서 [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) 메서드를 호출하여 지정된 Service Bus 큐에 대한 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 개체를 만듭니다. 
    1. [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 개체의 [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) 및 [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) 이벤트에 대한 처리기를 지정합니다. 
    1. [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 개체에서 [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync)를 호출하여 메시지 처리를 시작합니다. 
    1. 사용자가 키를 눌러 처리를 종료하면 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 개체에서 [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync)를 호출합니다. 

        자세한 내용은 코드 주석을 참조하세요.

        ```csharp
        using System;
        using System.Threading.Tasks;
        using Azure.Messaging.ServiceBus;
        
        namespace QueueReceiver
        {
            class Program
            {
                // connection string to your Service Bus namespace
                static string connectionString = "<NAMESPACE CONNECTION STRING>";
        
                // name of your Service Bus queue
                static string queueName = "<QUEUE NAME>";
        
        
                // the client that owns the connection and can be used to create senders and receivers
                static ServiceBusClient client;
        
                // the processor that reads and processes messages from the queue
                static ServiceBusProcessor processor;
        
                // handle received messages
                static async Task MessageHandler(ProcessMessageEventArgs args)
                {
                    string body = args.Message.Body.ToString();
                    Console.WriteLine($"Received: {body}");
        
                    // complete the message. messages is deleted from the queue. 
                    await args.CompleteMessageAsync(args.Message);
                }
        
                // handle any errors when receiving messages
                static Task ErrorHandler(ProcessErrorEventArgs args)
                {
                    Console.WriteLine(args.Exception.ToString());
                    return Task.CompletedTask;
                }
        
                static async Task Main()
                {
                    // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                    // of the application, which is best practice when messages are being published or read
                    // regularly.
                    //
        
                    // Create the client object that will be used to create sender and receiver objects
                    client = new ServiceBusClient(connectionString);
        
                    // create a processor that we can use to process the messages
                    processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());
        
                    try
                    {
                        // add handler to process messages
                        processor.ProcessMessageAsync += MessageHandler;
        
                        // add handler to process any errors
                        processor.ProcessErrorAsync += ErrorHandler;
        
                        // start processing 
                        await processor.StartProcessingAsync();
        
                        Console.WriteLine("Wait for a minute and then press any key to end the processing");
                        Console.ReadKey();
        
                        // stop processing 
                        Console.WriteLine("\nStopping the receiver...");
                        await processor.StopProcessingAsync();
                        Console.WriteLine("Stopped receiving messages");
                    }
                    finally
                    {
                        // Calling DisposeAsync on client types is required to ensure that network
                        // resources and other unmanaged objects are properly cleaned up.
                        await processor.DisposeAsync();
                        await client.DisposeAsync();
                    }
                }
            }
        }
        ```
1. `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 기본 연결 문자열로 바꿉니다. 그런 후 `<QUEUE NAME>`을 큐의 이름으로 바꿉니다. 
1. 프로그램을 빌드하고 오류가 없는지 확인합니다.
1. 수신기 애플리케이션을 실행합니다. 받은 메시지가 표시됩니다. 아무 키나 눌러 수신기와 애플리케이션을 중지합니다. 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1
    Received: Message 2
    Received: Message 3
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. 포털을 다시 확인합니다. **활성** 메시지로 `0`이 표시되지 않으면 몇 분 정도 기다렸다가 페이지를 새로 고칩니다. 

    - **활성** 메시지 수 및 **현재 크기** 값은 이제 **0** 입니다.
    - 아래쪽 **메트릭** 섹션의 **메시지** 차트에서 해당 큐에 대해 8개의 들어오는 메시지와 8개의 나가는 메시지가 있음을 확인할 수 있습니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="받은 후의 활성 메시지 수 및 크기" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::


## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요.

- [.NET용 Azure Service Bus 클라이언트 라이브러리 - 추가 정보](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 참조](/dotnet/api/azure.messaging.servicebus)
