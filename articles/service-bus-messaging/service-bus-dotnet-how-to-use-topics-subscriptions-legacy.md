---
title: .NET(이전 버전)에서 Azure Service Bus 토픽 및 구독 사용
description: Service Bus 메시징 항목 및 구독을 사용하는 C# .NET Core 콘솔 애플리케이션을 작성합니다.
ms.topic: how-to
ms.tgt_pltfrm: dotnet
ms.date: 07/27/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 4644cc6ed4c3f668c7a53ad963e7779740f0b771
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121736272"
---
# <a name="use-service-bus-topics-and-subscriptions-with-net-old-package"></a>.NET(이전 패키지)에서 Service Bus 토픽 및 구독 사용
이 문서에서는 다음 단계를 다룹니다.

1. 항목에 일련의 메시지를 보내도록 .NET Core 콘솔 애플리케이션을 작성합니다.
2. 구독에서 해당 메시지를 수신하도록 .NET Core 콘솔 애플리케이션을 작성합니다.

> [!WARNING]
> 이 문서에서는 이전 Microsoft.Azure.ServiceBus 패키지를 사용합니다. 최신 Azure.Messaging.ServiceBus 패키지를 사용하는 문서는 [Azure.Messaging.ServiceBus 패키지를 사용하여 메시지 보내기 및 받기](service-bus-dotnet-how-to-use-topics-subscriptions.md)를 참조하세요. 이전 라이브러리를 사용하는 애플리케이션을 새 라이브러리로 이동하려면 [Microsoft.Azure.ServiceBus에서 Azure.Messaging.ServiceBus로 마이그레이션하기 위한 가이드](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/servicebus/Azure.Messaging.ServiceBus/MigrationGuide.md)를 참조하세요. 

## <a name="prerequisites"></a>필수 구성 요소

1. Azure 구독 이 문서의 단계를 완료하려면 Azure 계정이 필요합니다. [Visual Studio 또는 MSDN 구독자 혜택](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF)을 활성화해도 되고, 또는 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)에 가입해도 됩니다.
2. [빠른 시작: Azure Portal을 사용하여 Service Bus 항목 및 해당 항목에 대한 하나 이상의 구독 만들기](service-bus-quickstart-topics-subscriptions-portal.md): 다음 작업을 수행합니다.
    1. Service Bus **네임스페이스** 를 만듭니다.
    2. **연결 문자열** 을 가져옵니다.
    3. 네임스페이스에서 **토픽** 을 만듭니다.
    4. 네임스페이스에서 토픽에 대한 **하나의 구독** 을 만듭니다.
3. [Visual Studio 2017 업데이트 3(버전 15.3, 26730.01)](https://www.visualstudio.com/vs) 이상
4. [NET Core SDK](https://dotnet.microsoft.com/download) 버전 2.0 이상
 
## <a name="send-messages-to-the-topic"></a>토픽에 메시지 보내기

항목에 메시지를 보내려면 Visual Studio를 사용하여 C# 콘솔 애플리케이션을 작성합니다.

### <a name="create-a-console-application"></a>콘솔 애플리케이션 만들기

Visual Studio를 시작하고 새로운 **콘솔 앱(.NET Core)** 프로젝트를 만듭니다.

### <a name="add-the-service-bus-nuget-package"></a>Service Bus NuGet 패키지 추가

1. 마우스 오른쪽 단추로 새롭게 만든 프로젝트를 클릭하고 **NuGet 패키지 관리** 를 선택합니다.
2. **찾아보기** 탭을 클릭하고 **[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)** 를 검색한 다음 **Microsoft.Azure.ServiceBus** 항목을 선택합니다. **설치** 를 클릭하여 설치를 완료한 후 이 대화 상자를 닫습니다.
   
    ![NuGet 패키지 선택][nuget-pkg]

### <a name="write-code-to-send-messages-to-the-topic"></a>코드를 작성하여 토픽에 메시지 보내기

1. 클래스 선언 전에 Program.cs의 네임스페이스 정의 위쪽에서 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` 클래스 내에서 다음 변수를 선언합니다. `ServiceBusConnectionString` 변수를 네임스페이스를 만들 때 가져온 연결 문자열로 설정하고, `TopicName`을 항목을 만들 때 사용된 이름으로 설정합니다.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    static ITopicClient topicClient;
    ``` 

3. `Main()` 메서드를 다음 단계에서 추가할 SendMessagesAsync 메서드를 사용하여 비동기적으로 메시지를 보내는 다음 **비동기** `Main` 메서드로 바꿉니다. 

    ```csharp
    public static async Task Main(string[] args)
    {
        const int numberOfMessages = 10;
        topicClient = new TopicClient(ServiceBusConnectionString, TopicName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after sending all the messages.");
        Console.WriteLine("======================================================");

        // Send messages.
        await SendMessagesAsync(numberOfMessages);

        Console.ReadKey();

        await topicClient.CloseAsync();
    }
    ```
5. `Main` 메서드의 바로 뒤에 `numberOfMessagesToSend`(현재 10으로 설정됨)으로 지정된 메시지 수를 전송하는 작업을 수행하는 다음 `SendMessagesAsync()` 메서드를 추가합니다.

    ```csharp
    static async Task SendMessagesAsync(int numberOfMessagesToSend)
    {
        try
        {
            for (var i = 0; i < numberOfMessagesToSend; i++)
            {
                // Create a new message to send to the topic.
                string messageBody = $"Message {i}";
                var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                // Write the body of the message to the console.
                Console.WriteLine($"Sending message: {messageBody}");

                // Send the message to the topic.
                await topicClient.SendAsync(message);
            }
        }
        catch (Exception exception)
        {
            Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
        }
    }
    ```
  
6. 보낸 사람 Program.cs 파일은 다음과 같아야 합니다.
   
    ```csharp
    namespace CoreSenderApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            static ITopicClient topicClient;

            public static async Task Main(string[] args)
            {
                const int numberOfMessages = 10;
                topicClient = new TopicClient(ServiceBusConnectionString, TopicName);
    
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after sending all the messages.");
                Console.WriteLine("======================================================");
    
                // Send messages.
                await SendMessagesAsync(numberOfMessages);
    
                Console.ReadKey();
    
                await topicClient.CloseAsync();
            }

            static async Task SendMessagesAsync(int numberOfMessagesToSend)
            {
                try
                {
                    for (var i = 0; i < numberOfMessagesToSend; i++)
                    {
                        // Create a new message to send to the topic
                        string messageBody = $"Message {i}";
                        var message = new Message(Encoding.UTF8.GetBytes(messageBody));

                        // Write the body of the message to the console
                        Console.WriteLine($"Sending message: {messageBody}");

                        // Send the message to the topic
                        await topicClient.SendAsync(message);
                    }
                }
                catch (Exception exception)
                {
                    Console.WriteLine($"{DateTime.Now} :: Exception: {exception.Message}");
                }
            }
        }
    }
    ```

3. 프로그램을 실행하고 Azure Portal을 확인합니다. 네임스페이스 **개요** 창에서 항목 이름을 클릭합니다. 항목 **Essentials** 화면이 표시됩니다. 이제 창의 맨 아래 가까이에 나열된 구독에서 각 구독에 대한 **메시지 수** 값은 **10** 입니다. 메시지를 검색하지 않고 보낸 사람 애플리케이션을 실행할 때마다(다음 섹션에서 설명) 이 값이 10만큼 증가합니다. 또한 앱이 항목에 메시지를 추가할 때마다 항목의 현재 크기는 **Essentials** 창에 있는 **현재** 값을 증가시킵니다.
   
      ![메시지 크기][topic-message]

## <a name="receive-messages-from-the-subscription"></a>구독에서 메시지 받기

사용자가 보낸 메시지를 받으려면 다른 .NET Core 콘솔 애플리케이션을 만들고 이전의 보낸 사람 애플리케이션과 유사한 **Microsoft.Azure.ServiceBus** NuGet 패키지를 설치합니다.

### <a name="write-code-to-receive-messages-from-the-subscription"></a>코드를 작성하여 구독에서 메시지 받기

1. 클래스 선언 전에 Program.cs의 네임스페이스 정의 위쪽에서 다음 `using` 문을 추가합니다.
   
    ```csharp
    using System.Text;
    using System.Threading;
    using System.Threading.Tasks;
    using Microsoft.Azure.ServiceBus;
    ```

2. `Program` 클래스 내에서 다음 변수를 선언합니다. `ServiceBusConnectionString` 변수는 네임스페이스를 만들 때 가져온 연결 문자열로 설정하고, `TopicName`을 항목을 만들 때 사용된 이름으로 설정하고, `SubscriptionName`을 항목에 구독을 만들 때 사용된 이름으로 설정합니다.
   
    ```csharp
    const string ServiceBusConnectionString = "<your_connection_string>";
    const string TopicName = "<your_topic_name>";
    const string SubscriptionName = "<your_subscription_name>";
    static ISubscriptionClient subscriptionClient;
    ```

3. `Main()` 메서드를 다음 **async** `Main` 메서드로 바꿉니다. 다음 단계에서 추가할 `RegisterOnMessageHandlerAndReceiveMessages()` 메서드를 호출합니다. 

    ```csharp
    public static async Task Main(string[] args)
    {    
        subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);

        Console.WriteLine("======================================================");
        Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
        Console.WriteLine("======================================================");

        // Register subscription message handler and receive messages in a loop
        RegisterOnMessageHandlerAndReceiveMessages();

        Console.ReadKey();

        await subscriptionClient.CloseAsync();    
    }
   ```
5. `Main()` 메서드의 바로 뒤에 메시지 처리기를 등록하고 보낸 사람 애플리케이션에서 보낸 메시지를 수신하는 다음 메서드를 추가합니다.

    ```csharp
    static void RegisterOnMessageHandlerAndReceiveMessages()
    {
        // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
        var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
        {
            // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
            // Set it according to how many messages the application wants to process in parallel.
            MaxConcurrentCalls = 1,

            // Indicates whether the message pump should automatically complete the messages after returning from user callback.
            // False below indicates the complete operation is handled by the user callback as in ProcessMessagesAsync().
            AutoComplete = false
        };

        // Register the function that processes messages.
        subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
    }
    ```    

6. 이전 메서드의 바로 뒤에 수신된 메시지를 처리하는 다음 `ProcessMessagesAsync()` 메서드를 추가합니다.
 
    ```csharp
    static async Task ProcessMessagesAsync(Message message, CancellationToken token)
    {
        // Process the message.
        Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

        // Complete the message so that it is not received again.
        // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
        await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

        // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
        // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
        // to avoid unnecessary exceptions.
    }
    ```

7. 마지막으로 발생할 수 있는 모든 예외를 처리하는 다음 메서드를 추가합니다.
 
    ```csharp
    // Use this handler to examine the exceptions received on the message pump.
    static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
    {
        Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
        var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
        Console.WriteLine("Exception context for troubleshooting:");
        Console.WriteLine($"- Endpoint: {context.Endpoint}");
        Console.WriteLine($"- Entity Path: {context.EntityPath}");
        Console.WriteLine($"- Executing Action: {context.Action}");
        return Task.CompletedTask;
    }    
    ```    

8. 받는 사람 Program.cs 파일은 다음과 같아야 합니다.
   
    ```csharp
    namespace CoreReceiverApp
    {
        using System;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using Microsoft.Azure.ServiceBus;

        class Program
        {
            const string ServiceBusConnectionString = "<your_connection_string>";
            const string TopicName = "<your_topic_name>";
            const string SubscriptionName = "<your_subscription_name>";
            static ISubscriptionClient subscriptionClient;

            public static async Task Main(string[] args)
            {    
                subscriptionClient = new SubscriptionClient(ServiceBusConnectionString, TopicName, SubscriptionName);
        
                Console.WriteLine("======================================================");
                Console.WriteLine("Press ENTER key to exit after receiving all the messages.");
                Console.WriteLine("======================================================");
        
                // Register subscription message handler and receive messages in a loop
                RegisterOnMessageHandlerAndReceiveMessages();
        
                Console.ReadKey();
        
                await subscriptionClient.CloseAsync();    
            }

            static void RegisterOnMessageHandlerAndReceiveMessages()
            {
                // Configure the message handler options in terms of exception handling, number of concurrent messages to deliver, etc.
                var messageHandlerOptions = new MessageHandlerOptions(ExceptionReceivedHandler)
                {
                    // Maximum number of concurrent calls to the callback ProcessMessagesAsync(), set to 1 for simplicity.
                    // Set it according to how many messages the application wants to process in parallel.
                    MaxConcurrentCalls = 1,

                    // Indicates whether MessagePump should automatically complete the messages after returning from User Callback.
                    // False below indicates the Complete will be handled by the User Callback as in `ProcessMessagesAsync` below.
                    AutoComplete = false
                };

                // Register the function that processes messages.
                subscriptionClient.RegisterMessageHandler(ProcessMessagesAsync, messageHandlerOptions);
            }

            static async Task ProcessMessagesAsync(Message message, CancellationToken token)
            {
                // Process the message.
                Console.WriteLine($"Received message: SequenceNumber:{message.SystemProperties.SequenceNumber} Body:{Encoding.UTF8.GetString(message.Body)}");

                // Complete the message so that it is not received again.
                // This can be done only if the subscriptionClient is created in ReceiveMode.PeekLock mode (which is the default).
                await subscriptionClient.CompleteAsync(message.SystemProperties.LockToken);

                // Note: Use the cancellationToken passed as necessary to determine if the subscriptionClient has already been closed.
                // If subscriptionClient has already been closed, you can choose to not call CompleteAsync() or AbandonAsync() etc.
                // to avoid unnecessary exceptions.
            }

            static Task ExceptionReceivedHandler(ExceptionReceivedEventArgs exceptionReceivedEventArgs)
            {
                Console.WriteLine($"Message handler encountered an exception {exceptionReceivedEventArgs.Exception}.");
                var context = exceptionReceivedEventArgs.ExceptionReceivedContext;
                Console.WriteLine("Exception context for troubleshooting:");
                Console.WriteLine($"- Endpoint: {context.Endpoint}");
                Console.WriteLine($"- Entity Path: {context.EntityPath}");
                Console.WriteLine($"- Executing Action: {context.Action}");
                return Task.CompletedTask;
            }
        }
    }
    ```
9. 프로그램을 실행하고 포털을 다시 확인합니다. 이제 **메시지 수** 와 **현재** 값이 **0** 이 됩니다.
   
    ![항목 길이][topic-message-receive]

축하합니다! 이제 .NET Standard 라이브러리를 사용하여 토픽 및 구독을 만들고, 10개의 메시지를 보내고, 해당 메시지를 수신했습니다.

> [!NOTE]
> [Service Bus Explorer](https://github.com/paolosalvatori/ServiceBusExplorer/)로 Service Bus 리소스를 관리할 수 있습니다. Service Bus Explorer를 사용하면 Service Bus 네임스페이스에 연결하고 쉬운 방식으로 메시징 엔터티를 관리할 수 있습니다. 이 도구는 가져오기/내보내기 기능 또는 항목, 큐, 구독, 릴레이 서비스, Notification Hubs 및 이벤트 허브를 테스트하는 기능과 같은 고급 기능을 제공합니다. 

## <a name="next-steps"></a>다음 단계

Service Bus 메시지의 고급 기능 중 일부를 보여주는 Service Bus [샘플이 있는 GitHub 리포지토리](https://github.com/Azure/azure-service-bus/tree/master/samples)를 확인합니다.

<!--Image references-->

[nuget-pkg]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/nuget-package.png
[topic-message]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message.png
[topic-message-receive]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/topic-message-receive.png
[createtopic1]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic1.png
[createtopic2]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic2.png
[createtopic3]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic3.png
[createtopic4]: ./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-topic4.png
[github-samples]: https://github.com/Azure-Samples/azure-servicebus-messaging-samples
[azure-portal]: https://portal.azure.com
