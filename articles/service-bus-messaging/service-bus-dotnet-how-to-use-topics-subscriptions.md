---
title: Azure Service Bus 토픽 시작(.NET)
description: 이 자습서에서는 .NET 프로그래밍 언어를 사용하여 Azure Service Bus 토픽에 메시지를 보내고 토픽 구독에서 메시지를 받는 방법을 보여 드립니다.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 10/11/2021
ms.custom: contperf-fy22q2, mode-other
ms.openlocfilehash: db80e9a6ed7a62cbd47a32f01d54c8d2af1c3a5e
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359262"
---
# <a name="get-started-with-azure-service-bus-topics-and-subscriptions-net"></a>Azure Service Bus 토픽 및 구독 시작(.NET)

> [!div class="op_single_selector" title1="프로그래밍 언어를 선택합니다."]
> * [C#](service-bus-dotnet-how-to-use-topics-subscriptions.md)
> * [Java](service-bus-java-how-to-use-topics-subscriptions.md)
> * [JavaScript](service-bus-nodejs-how-to-use-topics-subscriptions.md)
> * [Python](service-bus-python-how-to-use-topics-subscriptions.md)


이 빠른 시작에서는 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) .NET 라이브러리를 사용하여 Service 버스 토픽에 메시지를 보내고 해당 토픽에 대한 구독에서 메시지를 수신하는 방법을 보여 줍니다.

이 빠른 시작에서는 다음 단계를 수행합니다.

1. Azure Portal을 사용하여 Service Bus 네임스페이스를 만듭니다.
2. Azure Portal을 사용하여 Service Bus 항목을 만듭니다.
3. Azure Portal을 사용하여 해당 항목에 Service Bus 구독을 만듭니다.
4. 항목에 일련의 메시지를 보내도록 .NET Core 콘솔 애플리케이션을 작성합니다.
5. 구독에서 해당 메시지를 수신하도록 .NET Core 콘솔 애플리케이션을 작성합니다.

> [!NOTE]
> 이 빠른 시작에서는 메시지 일괄 처리를 Service Bus 토픽으로 보내고 해당 토픽의 구독에서 해당 메시지를 받는 간단한 시나리오를 구현하는 단계별 지침을 제공합니다.  기타 고급 시나리오에 대한 자세한 샘플은 [GitHub .NET 샘플 Service Bus](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)참조하세요. 

## <a name="prerequisites"></a>사전 요구 사항
이 서비스를 처음 사용하는 경우 이 빠른 시작 전에 [Service Bus 개요](service-bus-messaging-overview.md)를 참조하세요. 

- **Azure 구독**. Azure Service Bus를 비롯한 Azure 서비스를 사용하려면 구독이 필요합니다.  기존 Azure 계정이 없는 경우 [평가판](https://azure.microsoft.com/free/)에 가입하거나 [계정을 만들 때](https://azure.microsoft.com) MSDN 구독자 혜택을 사용할 수 있습니다.
- **Microsoft Visual Studio 2019** Azure Service Bus 클라이언트 라이브러리는 C# 8.0에 도입된 새 기능을 사용합니다.  이전 C# 언어 버전으로 라이브러리를 계속 사용할 수 있지만 새 구문은 사용할 수 없습니다. 전체 구문을 사용하려면 [.NET Core SDK](https://dotnet.microsoft.com/download) 3.0 이상 및 `latest`로 설정된 [언어 버전](/dotnet/csharp/language-reference/configure-language-version#override-a-default)으로 컴파일하는 것이 좋습니다. Visual Studio를 사용하는 경우 Visual Studio 2019 이전 버전은 C# 8.0 프로젝트를 빌드하는 데 필요한 도구와 호환되지 않습니다. 체험 Community 버전을 비롯한 Visual Studio 2019는 [여기](https://visualstudio.microsoft.com/vs/)서 다운로드할 수 있습니다.

[!INCLUDE [service-bus-create-namespace-portal](./includes/service-bus-create-namespace-portal.md)]

[!INCLUDE [service-bus-create-topic-subscription-portal](./includes/service-bus-create-topic-subscription-portal.md)]

> [!IMPORTANT]
> 네임스페이스, 토픽 이름, 구독 이름에 대한 연결 문자열을 기록해 둡니다. 이 자습서의 뒷부분에서 사용됩니다.

## <a name="send-messages-to-the-topic"></a>토픽에 메시지 보내기
이 섹션에서는 Service Bus 토픽으로 메시지를 보내는 .NET Core 콘솔 애플리케이션을 만드는 방법을 보여 줍니다. 

> [!NOTE]
> 이 빠른 시작에서는 메시지 일괄 처리를 Service Bus 토픽으로 보내고 해당 토픽의 구독에서 해당 메시지를 받는 간단한 시나리오를 구현하는 단계별 지침을 제공합니다.  기타 고급 시나리오에 대한 자세한 샘플은 [GitHub .NET 샘플 Service Bus](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)참조하세요.

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

1. Visual Studio 2019를 시작합니다. 
1. **새 프로젝트 만들기** 를 선택합니다. 
1. **새 프로젝트 만들기** 대화 상자에서 다음 단계를 수행합니다. 이 대화 상자가 표시되지 않으면 메뉴에서 **파일** 을 선택하고 **새로 만들기** 를 선택한 다음, **프로젝트** 를 선택합니다. 
    1. 프로그래밍 언어로 **C#** 을 선택합니다.
    1. 애플리케이션 유형으로 **콘솔** 을 선택합니다. 
    1. 결과 목록에서 **콘솔 애플리케이션** 을 선택합니다. 
    1. 그다음에 **다음** 을 선택합니다. 

        :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/new-send-project.png" alt-text="C# 및 콘솔이 선택된 새 프로젝트 만들기 대화 상자를 보여 주는 이미지":::
1. 프로젝트 이름에 **TopicSender** 를 입력하고 솔루션 이름에 **ServiceBusTopicQuickStart** 를 입력한 후 **다음** 을 선택합니다. 
1. **추가 정보** 페이지에서 **만들기** 를 선택하여 솔루션 및 프로젝트를 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. 다음 명령을 실행하여 [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/) NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-send-messages-to-the-topic"></a>코드를 추가하여 토픽에 메시지 보내기 

1. **Program.cs** 의 네임스페이스 정의 위쪽에서 다음 `using` 문을 클래스 선언 앞에 추가합니다.

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. `Program` 클래스 내에서 `Main` 메서드 바로 앞에 다음 속성을 선언합니다. `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다. 그런 다음 `<TOPIC NAME>`을 Service Bus 토픽 이름으로 바꿉니다.

    ```csharp
    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of your Service Bus topic
    static string topicName = "<TOPIC NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the sender used to publish messages to the topic
    static ServiceBusSender sender;

    // number of messages to be sent to the topic
    private const int numOfMessages = 3;
    ```    
1. **Program.cs** 의 코드를 다음 코드로 바꿉니다. 다음은 코드의 중요한 단계입니다.  
    1. 네임스페이스에 대한 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
    1. `ServiceBusClient` 개체에서 [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) 메서드를 호출하여 특정 Service Bus 토픽에 대한 [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) 개체를 만듭니다.     
    1. [ServiceBusSender.CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync)를 사용하여 [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) 개체를 만듭니다.
    1. [ServiceBusMessageBatch.TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage)를 사용하여 일괄 처리에 메시지를 추가합니다. 
    1. [ServiceBusSender.SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) 메서드를 사용하여 메시지 일괄 처리를 Service Bus 토픽에 보냅니다.
    
    ```csharp
    static async Task Main()
    {
        // The Service Bus client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when messages are being published or read
        // regularly.
        //
        // Create the clients that we'll use for sending and processing messages.
        client = new ServiceBusClient(connectionString);
        sender = client.CreateSender(topicName);

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
            // Use the producer client to send the batch of messages to the Service Bus topic
            await sender.SendMessagesAsync(messageBatch);
            Console.WriteLine($"A batch of {numOfMessages} messages has been published to the topic.");
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
    ```
1. Program.cs 파일은 다음과 같아야 합니다. 
    
    자세한 내용은 코드 주석을 참조하세요.

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace TopicSender
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
            // name of your Service Bus topic
            static string topicName = "<TOPIC NAME>";
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the sender used to publish messages to the topic
            static ServiceBusSender sender;
    
            // number of messages to be sent to the topic
            private const int numOfMessages = 3;
    
            static async Task Main()
            {
                // The Service Bus client types are safe to cache and use as a singleton for the lifetime
                // of the application, which is best practice when messages are being published or read
                // regularly.
                //
                // Create the clients that we'll use for sending and processing messages.
                client = new ServiceBusClient(connectionString);
                sender = client.CreateSender(topicName);
    
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
                    // Use the producer client to send the batch of messages to the Service Bus topic
                    await sender.SendMessagesAsync(messageBatch);
                    Console.WriteLine($"A batch of {numOfMessages} messages has been published to the topic.");
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
1. `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다. 그런 다음 `<TOPIC NAME>`을 Service Bus 토픽 이름으로 바꿉니다. 
1. 프로그램을 빌드하고 오류가 없는지 확인합니다. 
1. 프로그램을 실행하고 확인 메시지가 나타날 때까지 기다립니다.
    
    ```bash
    A batch of 3 messages has been published to the topic
    ```
1. Azure Portal에서 다음 단계를 수행합니다.
    1. Service Bus 네임스페이스로 이동합니다. 
    1. **개요** 페이지의 아래쪽 가운데 창에서 **토픽** 탭으로 전환하고, Service Bus 토픽을 선택합니다. 다음 예에서는 `mytopic`입니다.
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="항목 선택":::
    1. **Service Bus 토픽** 페이지 아래쪽의 **메트릭** 섹션에 있는 **메시지** 차트에서 해당 토픽에 대해 들어오는 메시지가 세 개 있음을 확인할 수 있습니다. 값이 표시되지 않으면 몇 분 동안 기다렸다가 페이지를 새로 고쳐 업데이트된 차트를 확인합니다. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="토픽으로 보낸 메시지" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. 아래쪽 창에서 구독을 선택합니다. 다음 예에서는 **S1** 입니다. **Service Bus 구독** 페이지에 **활성 메시지 수** 가 **3** 으로 표시됩니다. 토픽으로 보낸 메시지 3개를 구독에서 받았지만 받는 사람이 아직 선택하지 않았습니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="구독에서 받은 메시지" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    
## <a name="receive-messages-from-a-subscription"></a>구독에서 메시지 받기
이 섹션에서는 Service Bus 토픽에 대한 구독에서 메시지를 받는 .NET Core 콘솔 애플리케이션을 만듭니다. 

> [!NOTE]
> 이 빠른 시작에서는 메시지 일괄 처리를 Service Bus 토픽으로 보내고 해당 토픽의 구독에서 해당 메시지를 받는 간단한 시나리오를 구현하는 단계별 지침을 제공합니다.  기타 고급 시나리오에 대한 자세한 샘플은 [GitHub .NET 샘플 Service Bus](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)참조하세요.

### <a name="create-a-project-for-the-receiver"></a>수신기에 대한 프로젝트 만들기

1. 솔루션 탐색기 창에서 **ServiceBusTopicQuickStart** 솔루션을 마우스 오른쪽 단추로 클릭하고 **추가** 를 가리킨 다음, **새 프로젝트** 를 선택합니다. 
1. **콘솔 애플리케이션** 을 선택하고 **다음** 을 선택합니다. 
1. **프로젝트 이름** 에 **SubscriptionReceiver** 를 입력하고 **다음** 을 선택합니다. 
1. **추가 정보** 페이지에서 **만들기** 를 선택합니다. 
1. **솔루션 탐색기** 창에서 **SubscriptionReceiver** 를 마우스 오른쪽 단추로 클릭하고 **시작 프로젝트로 설정** 을 선택합니다. 

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 메뉴에서 **도구** > **NuGet 패키지 관리자** > **패키지 관리자 콘솔** 을 선택합니다. 
1. **패키지 관리자 콘솔** 창에서 **SubscriptionReceiver** 가 **기본 프로젝트** 로 선택되어 있는지 확인합니다. 그렇지 않은 경우 드롭다운 목록을 사용하여 **SubscriptionReceiver** 를 선택합니다.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-subscription-receiver-project.png" alt-text="패키지 관리자 콘솔 창에서 SubscriptionReceiver 프로젝트 선택을 보여주는 이미지." lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-subscription-receiver-project.png":::    
1. 다음 명령을 실행하여 **Azure.Messaging.ServiceBus** NuGet 패키지를 설치합니다.

    ```cmd
    Install-Package Azure.Messaging.ServiceBus
    ```

### <a name="add-code-to-receive-messages-from-the-subscription"></a>코드를 추가하여 구독에서 메시지 받기
1. **Program.cs** 의 네임스페이스 정의 위쪽에서 다음 `using` 문을 클래스 선언 앞에 추가합니다.

    ```csharp
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
2. `Program` 클래스 내에서 `Main` 메서드 바로 앞에 다음 속성을 선언합니다. 자리 표시자를 올바른 값으로 바꿉니다.
    - `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다.
    - `<TOPIC NAME>`을 Service Bus 토픽의 이름으로 바꿉니다.
    - `<SERVICE BUS - TOPIC SUBSCRIPTION NAME>`을 토픽에 대한 구독의 이름으로 바꿉니다. 

    ```csharp
    // connection string to your Service Bus namespace
    static string connectionString = "<NAMESPACE CONNECTION STRING>";

    // name of the Service Bus topic
    static string topicName = "<SERVICE BUS TOPIC NAME>";

    // name of the subscription to the topic
    static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";

    // the client that owns the connection and can be used to create senders and receivers
    static ServiceBusClient client;

    // the processor that reads and processes messages from the subscription
    static ServiceBusProcessor processor;    
    ```
3. 수신된 메시지 및 오류를 처리하려면 다음 메서드를 `Program` 클래스에 추가합니다. 

    ```csharp
    // handle received messages
    static async Task MessageHandler(ProcessMessageEventArgs args)
    {
        string body = args.Message.Body.ToString();
        Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

        // complete the message. messages is deleted from the subscription. 
        await args.CompleteMessageAsync(args.Message);
    }

    // handle any errors when receiving messages
    static Task ErrorHandler(ProcessErrorEventArgs args)
    {
        Console.WriteLine(args.Exception.ToString());
        return Task.CompletedTask;
    }
    ```
1. **Program.cs** 의 코드를 다음 코드로 바꿉니다. 다음은 코드의 중요한 단계입니다.
    1. 네임스페이스에 대한 연결 문자열을 사용하여 [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) 개체를 만듭니다. 
    1. `ServiceBusClient` 개체에서 [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) 메서드를 호출하여 지정된 Service Bus 큐에 대한 [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) 개체를 생성합니다. 
    1. `ServiceBusProcessor` 개체의 [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) 및 [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) 이벤트에 대한 처리기를 지정합니다. 
    1. `ServiceBusProcessor` 개체에서 [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync)를 호출하여 메시지 처리를 시작합니다. 
    1. 사용자가 키를 눌러 처리를 종료하면 `ServiceBusProcessor` 객체에서 [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync)를 호출합니다. 
    

    자세한 내용은 코드 주석을 참조하세요.

    ```csharp
    static async Task Main()
    {
        // The Service Bus client types are safe to cache and use as a singleton for the lifetime
        // of the application, which is best practice when messages are being published or read
        // regularly.
        //
        // Create the clients that we'll use for sending and processing messages.
        client = new ServiceBusClient(connectionString);

        // create a processor that we can use to process the messages
        processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

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
    ```
1. `Program.cs`는 다음과 같아야 합니다.  

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    
    namespace SubscriptionReceiver
    {
        class Program
        {
            // connection string to your Service Bus namespace
            static string connectionString = "<NAMESPACE CONNECTION STRING>";
    
            // name of the Service Bus topic
            static string topicName = "<SERVICE BUS TOPIC NAME>";
        
            // name of the subscription to the topic
            static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    
            // the client that owns the connection and can be used to create senders and receivers
            static ServiceBusClient client;
    
            // the processor that reads and processes messages from the subscription
            static ServiceBusProcessor processor;
    
            // handle received messages
            static async Task MessageHandler(ProcessMessageEventArgs args)
            {
                string body = args.Message.Body.ToString();
                Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");
    
                // complete the message. messages is deleted from the subscription. 
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
                // Create the clients that we'll use for sending and processing messages.
                client = new ServiceBusClient(connectionString);
    
                // create a processor that we can use to process the messages
                processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());
    
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
1. 자리 표시자를 올바른 값으로 바꿉니다.
    - `<NAMESPACE CONNECTION STRING>`을 Service Bus 네임스페이스에 대한 연결 문자열로 바꿉니다.
    - `<TOPIC NAME>`을 Service Bus 토픽의 이름으로 바꿉니다.
    - `<SERVICE BUS - TOPIC SUBSCRIPTION NAME>`을 토픽에 대한 구독의 이름으로 바꿉니다. 
1. 프로그램을 빌드하고 오류가 없는지 확인합니다.
1. 수신기 애플리케이션을 실행합니다. 받은 메시지가 표시됩니다. 아무 키나 눌러 수신기와 애플리케이션을 중지합니다. 

    ```console
    Wait for a minute and then press any key to end the processing
    Received: Message 1 from subscription: S1
    Received: Message 2 from subscription: S1
    Received: Message 3 from subscription: S1
    
    Stopping the receiver...
    Stopped receiving messages
    ```
1. 포털을 다시 확인합니다. 
    - **Service Bus 토픽** 페이지의 **메시지** 차트에 3개의 들어오는 메시지와 3개의 나가는 메시지가 표시됩니다. 이러한 숫자가 표시되지 않으면 몇 분 동안 기다렸다가 페이지를 새로 고쳐 업데이트된 차트를 확인합니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="보낸 메시지 및 받은 메시지" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
    - **Service Bus 구독** 페이지에 **활성 메시지 수** 가 0으로 표시됩니다. 받는 사람이 이 구독에서 메시지를 받고 메시지를 완료했기 때문입니다. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="종료된 구독의 활성 메시지 수" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
        


## <a name="next-steps"></a>다음 단계
다음 설명서와 샘플을 참조하세요.

- [.NET용 Azure Service Bus 클라이언트 라이브러리 - 추가 정보](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [GitHub의 Azure Service Bus에 대한 .NET 샘플](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API 참조](/dotnet/api/azure.messaging.servicebus?preserve-view=true)
