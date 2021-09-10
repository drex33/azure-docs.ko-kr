---
title: WebJobs SDK를 사용한 이벤트 기반 백그라운드 처리 자습서
description: 웹앱이 백그라운드 작업을 실행하도록 설정하는 방법을 알아봅니다. 이 자습서를 사용하여 WebJobs SDK를 시작합니다.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.date: 06/25/2021
ms.author: glenga
ms.topic: tutorial
ms.openlocfilehash: 6b4875f028fe8645125743d04770c2fd237b4f62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752463"
---
# <a name="tutorial-get-started-with-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>자습서: 이벤트 기반 백그라운드 처리를 위한 Azure WebJobs SDK 시작

웹앱이 백그라운드 작업, 예약된 작업을 실행하고 이벤트에 응답할 수 있도록 Azure App Service용 Azure WebJobs SDK를 시작합니다. 

Visual Studio 2019를 사용하여 WebJobs SDK를 통해 Azure Storage 큐 메시지에 응답하고, 로컬에서 프로젝트를 실행하고, 마지막으로 프로젝트를 Azure에 배포하는 .NET Core 콘솔 앱을 만듭니다.

이 자습서에서는 다음 작업 방법을 배웁니다.

> [!div class="checklist"]
> * 콘솔 앱 만들기
> * 함수 추가
> * 로컬에서 테스트
> * Azure에 배포
> * Application Insights 로깅 사용
> * 입출력 바인딩 추가

## <a name="prerequisites"></a>필수 조건

* **Azure 개발** 워크로드를 사용하는 Visual Studio 2019. [Visual Studio 2019를 설치합니다](/visualstudio/install/).

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/dotnet).

## <a name="create-a-console-app"></a>콘솔 앱 만들기
이 섹션에서는 먼저 Visual Studio 2019에서 프로젝트를 만듭니다. 다음으로, Azure 개발, 코드 게시를 위한 도구와 트리거를 수신 대기하고 함수를 호출하는 함수를 추가합니다. 마지막으로, 레거시 모니터링 도구를 사용하지 않고 기본 필터링과 함께 콘솔 공급자를 사용하도록 콘솔 로깅을 설정합니다. 

>[!NOTE]  
>이 문서의 절차는 .NET Core 3.1에서 실행되는 .NET Core 콘솔 앱 만들기에 대해 확인되었습니다.

### <a name="create-a-project"></a>프로젝트 만들기

1. Visual Studio에서 **파일** > **새로 만들기** > **프로젝트** 를 선택합니다.

1. **새 프로젝트 만들기** 에서 **콘솔 애플리케이션(C#)** 을 선택한 후 **다음** 을 선택합니다.

1. **새 프로젝트 구성** 에서 프로젝트 이름을 *WebJobsSDKSample* 로 지정한 후 **다음** 을 선택합니다.

1. **대상 프레임워크** 를 선택하고 **만들기** 를 선택합니다. 이 자습서는 .NET Core 3.1을 사용하여 확인되었습니다.

### <a name="install-webjobs-nuget-packages"></a>WebJobs NuGet 패키지 설치

최신 WebJobs NuGet 패키지를 설치합니다. 이 패키지에는 Azure App Service에서 WebJobs에 함수 코드를 게시할 수 있는 Microsoft.Azure.WebJobs(WebJobs SDK)가 포함됩니다.

1. [Microsoft.Azure.WebJobs.Extensions NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions/)의 안정적인 최신 3.x 버전을 가져옵니다.

2. Visual Studio에서 **도구** > **NuGet 패키지 관리자** 로 이동합니다.

3. **패키지 관리자 콘솔** 을 선택합니다. NuGet cmdlet 목록, 설명서 링크, `PM>` 진입점이 표시됩니다.

4. 다음 명령에서 `<3_X_VERSION>`을 1단계에서 찾은 현재 버전 번호로 바꿉니다. 

     ```powershell
     Install-Package Microsoft.Azure.WebJobs.Extensions -version <3_X_VERSION>
     ```
5. **패키지 관리자 콘솔** 에서 명령을 실행합니다. 확장 목록이 표시되고 자동으로 설치됩니다. 
  
### <a name="create-the-host"></a>호스트 만들기

호스트는 함수에 대한 런타임 컨테이너이며, 트리거를 수신 대기하고 함수를 호출합니다. 다음은 ASP.NET Core의 일반 호스트인 [`IHost`](/dotnet/api/microsoft.extensions.hosting.ihost)를 구현하는 호스트를 만드는 단계입니다.

1. **Program.cs** 탭을 선택하고 다음 `using` 문을 추가합니다.

    ```cs
    using System.Threading.Tasks;
    using Microsoft.Extensions.Hosting;
    ```

1. 또한 **Program.cs** 에서 `Main` 메서드를 다음 코드로 바꿉니다.

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

ASP.NET Core에서 호스트 구성은 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스의 메서드를 호출하여 설정됩니다. 자세한 내용은 [.NET 일반 호스트](/aspnet/core/fundamentals/host/generic-host)를 참조하세요. `ConfigureWebJobs` 확장 메서드는 WebJobs 호스트를 초기화합니다. `ConfigureWebJobs`에서 Storage 바인딩 확장과 같은 특정 바인딩 확장을 초기화하고 해당 확장의 속성을 설정합니다.  

### <a name="enable-console-logging"></a>콘솔 로깅 사용

[ASP.NET Core 로깅 프레임워크](/aspnet/core/fundamentals/logging)를 사용하는 콘솔 로깅을 설정합니다. 이 프레임워크 Microsoft.Extensions.Logging에는 다양한 기본 제공 및 타사 로깅 공급자에서 작동하는 API가 포함됩니다.

1. `Microsoft.Extensions.Logging`이 포함된 안정적인 최신 버전의 [`Microsoft.Extensions.Logging.Console` NuGet 패키지](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Console/)를 설치합니다.

2. 다음 명령에서 `<3_X_VERSION>`을 1단계에서 찾은 현재 버전 번호로 바꿉니다. NuGet 패키지의 각 유형에는 고유한 버전 번호가 있습니다.

   ```powershell
   Install-Package Microsoft.Extensions.Logging.Console -version <3_X_VERSION>
   ```
3. **패키지 관리자 콘솔** 에서 현재 버전 번호를 입력하고 명령을 실행합니다. 확장 목록이 표시되고 자동으로 설치됩니다. 

4. **Program.cs** 탭에서 다음 `using` 문을 추가합니다.

   ```cs
   using Microsoft.Extensions.Logging;
   ```
5. 계속해서 **Program.cs** 에서 [`ConfigureLogging`](/dotnet/api/microsoft.aspnetcore.hosting.webhostbuilderextensions.configurelogging) 메서드를 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder)의 `Build` 명령 앞에 추가합니다. [`AddConsole`](/dotnet/api/microsoft.extensions.logging.consoleloggerextensions.addconsole) 메서드는 구성에 콘솔 로깅을 추가합니다.

    ```cs
    builder.ConfigureLogging((context, b) =>
    {
        b.AddConsole();
    });
    ```

    `Main` 메서드는 이제 다음과 같습니다.

    ```cs
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.ConfigureWebJobs(b =>
                {
                    b.AddAzureStorageCoreServices();
                });
        builder.ConfigureLogging((context, b) =>
                {
                    b.AddConsole();
                });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

    이 추가를 통해 다음과 같이 변경됩니다.

    * [대시보드 로깅](https://github.com/Azure/azure-webjobs-sdk/wiki/Queues#logs)을 사용하지 않도록 설정합니다. 대시보드는 레거시 모니터링 도구이며, 처리량이 많은 프로덕션 시나리오에서는 대시보드 로깅을 사용하지 않는 것이 좋습니다.
    * 콘솔 공급자에 기본 [필터링](webjobs-sdk-how-to.md#log-filtering)을 추가합니다.

이제 Azure Storage 큐에 도착하는 메시지에 의해 트리거되는 함수를 추가할 수 있습니다.

## <a name="add-a-function"></a>함수 추가

함수는 일정에 따라 실행되거나, 이벤트에 따라 트리거되거나, 요청 시 실행되는 코드 단위입니다. 트리거는 서비스 이벤트를 수신 대기합니다. WebJobs SDK의 컨텍스트에서 트리거는 배포 모드를 참조하지 않습니다. SDK를 사용하여 만든 이벤트 기반 또는 예약된 WebJobs는 항상 “Always On”이 사용되는 연속 WebJobs로 배포되어야 합니다. 

이 섹션에서는 Azure Storage 큐에서 메시지에 의해 트리거되는 함수를 만듭니다. 먼저 Azure Storage에 연결하기 위해 바인딩 확장을 추가해야 합니다.

### <a name="install-the-storage-binding-extension"></a>Storage 바인딩 확장 설치

WebJobs SDK 버전 3부터 Azure Storage 서비스에 연결하려면 별도의 Storage 바인딩 확장 패키지를 설치해야 합니다. 

1. [Microsoft.Azure.WebJobs.Extensions.Storage](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.Storage) NuGet 패키지의 안정적인 최신 버전 3.x를 가져옵니다.

1. 다음 명령에서 `<3_X_VERSION>`을 1단계에서 찾은 현재 버전 번호로 바꿉니다. NuGet 패키지의 각 유형에는 고유한 버전 번호가 있습니다. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Extensions.Storage -Version <3_X_VERSION>
    ```
1. **패키지 관리자 콘솔** 에서 `PM>` 진입점에 현재 버전 번호를 사용하여 명령을 실행합니다.

1. 계속해서 **Program.cs** 의 `ConfigureWebJobs` 확장 메서드에서 `AddAzureStorage` 메서드를 [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) 인스턴스(`Build` 명령 앞)에 추가하여 Storage 확장을 초기화합니다. 이때 `ConfigureWebJobs` 메서드는 다음과 같이 표시됩니다.

    ```cs
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    ```
1. `builder`가 인스턴스화된 후 `Main` 메서드에서 다음 코드를 추가합니다.

    ```csharp
    builder.UseEnvironment(EnvironmentName.Development);
     ```

    [개발 모드](webjobs-sdk-how-to.md#host-development-settings)에서 실행하면 런타임이 메시지를 찾고 함수를 호출하는 데 걸리는 시간을 크게 지연시킬 수 있는 [큐 폴링 지수 백오프](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#polling-algorithm)가 감소합니다. 개발 및 테스트를 완료한 경우 이 코드 줄을 제거하거나 `Production`으로 전환해야 합니다. 

    이제 `Main` 메서드는 다음 예제와 같이 표시됩니다.

    ```csharp
    static async Task Main()
    {
        var builder = new HostBuilder();
        builder.UseEnvironment(EnvironmentName.Development);
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
        });
        builder.ConfigureLogging((context, b) =>
        {
            b.AddConsole();
        });
        builder.ConfigureWebJobs(b =>
        {
            b.AddAzureStorageCoreServices();
            b.AddAzureStorage();
        });
        var host = builder.Build();
        using (host)
        {
            await host.RunAsync();
        }
    }
    ```

### <a name="create-a-queue-triggered-function"></a>큐 트리거 함수 만들기

`QueueTrigger` 특성은 런타임에 새 메시지가 `queue`라는 Azure Storage 큐에 기록될 때 이 함수를 호출하도록 지시합니다. 큐 메시지의 내용은 `message` 매개 변수의 메서드 코드에 제공됩니다. 메서드 본문은 트리거 데이터를 처리하는 위치입니다. 이 예제에서 코드는 단지 메시지만 기록합니다.

1. 솔루션 탐색기에서 프로젝트를 마우스 오른쪽 단추로 클릭하고, **추가** > **새 항목** 을 선택한 다음, **클래스** 를 선택합니다. 

2. 새 C# 클래스 파일 이름을 *Functions.cs* 로 지정하고 **추가** 를 선택합니다.

3. *Functions.cs* 에서 생성된 템플릿을 다음 코드로 바꿉니다.
    
    ```cs
    using Microsoft.Azure.WebJobs;
    using Microsoft.Extensions.Logging;
    
    namespace WebJobsSDKSample
    {
        public class Functions
        {
            public static void ProcessQueueMessage([QueueTrigger("queue")] string message, ILogger logger)
            {
                logger.LogInformation(message);
            }
        }
    }
    ```

    *Functions* 클래스를 `public static`으로 표시해야 런타임이 메서드에 액세스하여 실행할 수 있습니다. 위의 코드 샘플에서는 메시지가 `queue`라는 큐에 추가되면 함수가 실행되고 `message` 문자열이 로그에 기록됩니다. 모니터링되는 큐는 다음에 만드는 기본 Azure Storage 계정에 있습니다.
   
`message` 매개 변수는 문자열일 필요는 없습니다. JSON 개체, 바이트 배열 또는 [CloudQueueMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueuemessage) 개체에 바인딩할 수도 있습니다. [큐 트리거 사용을 참조하세요](../azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp#usage). 각 바인딩 형식(예: 큐, Blob 또는 테이블)에는 바인딩할 수 있는 다른 매개 변수 형식 집합이 있습니다.

### <a name="create-an-azure-storage-account"></a>Azure Storage 계정 만들기

로컬로 실행되는 Azure Storage 에뮬레이터에는 WebJobs SDK에 필요한 일부 기능이 없습니다. Azure에서 스토리지 계정을 만들고 이를 사용하도록 프로젝트를 구성합니다. 

일반 공급 v2 스토리지 계정을 만드는 방법을 알아보려면 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요.

### <a name="locate-and-copy-your-connection-string"></a>연결 문자열 찾기 및 복사
스토리지를 구성하려면 연결 문자열이 필요합니다. 다음 단계에서 사용할 수 있도록 연결 문자열을 보관합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동하고 **설정** 을 선택합니다.
1. **설정** 에서 **액세스 키** 를 선택합니다.
1. **key1** 아래 **연결 문자열** 의 경우 **클립보드로 복사** 아이콘을 선택합니다.

     ![key](./media/webjobs-sdk-get-started/connection-key.png)

### <a name="configure-storage-to-run-locally"></a>로컬로 실행되도록 스토리지 구성

WebJobs SDK는 Azure의 애플리케이션 설정에서 스토리지 연결 문자열을 찾습니다. 로컬로 실행하는 경우 로컬 구성 파일 또는 환경 변수에서 이 값을 찾습니다.

1. 프로젝트를 마우스 오른쪽 단추로 클릭하고, **추가** > **새 항목** 을 선택하고, **JavaScript JSON 구성 파일** 을 선택하고, 새 파일 이름을 *appsettings.json* 파일로 지정하고, **추가** 를 선택합니다. 

1. 새 파일에서 다음 예제와 같이 `AzureWebJobsStorage` 필드를 추가합니다.

    ```json
    {
        "AzureWebJobsStorage": "{storage connection string}"
    }
    ```

1. *{storage connection string}* 을 이전에 복사한 연결 문자열로 바꿉니다.

1. 솔루션 탐색기에서 *appsettings.json* 파일을 선택하고 **속성** 창에서 **출력 디렉터리로 복사** 작업을 **변경된 내용만 복사** 로 설정합니다.

이 파일에는 연결 문자열 비밀이 포함되므로 원격 코드 리포지토리에 파일을 저장하면 안 됩니다. Azure에 프로젝트를 게시한 후 Azure App Service에서 앱에 동일한 연결 문자열 앱 설정을 추가할 수 있습니다.

## <a name="test-locally"></a>로컬에서 테스트

프로젝트를 로컬에서 빌드 및 실행하고 함수를 트리거하는 메시지 큐를 만듭니다.

1. Visual Studio의 **클라우드 탐색기** 에서 새 스토리지 계정의 노드를 확장한 다음, **큐** 를 마우스 오른쪽 단추로 클릭합니다.

1. **큐 만들기** 를 선택합니다.

1. 큐에 대한 이름으로 *queue* 를 입력한 다음, **확인** 을 선택합니다.

   ![큐를 만들고 이름을 “queue”로 지정하는 위치 스크린샷 ](./media/webjobs-sdk-get-started/create-queue.png)

1. 새 큐의 노드를 마우스 오른쪽 단추로 클릭한 다음, **열기** 를 선택합니다.

1. **메시지 추가** 아이콘을 선택합니다.

   ![메시지 추가 아이콘이 강조 표시된 스크린샷](./media/webjobs-sdk-get-started/create-queue-message.png)

1. **메시지 추가** 대화 상자에서 *Hello World!* 를 **메시지 텍스트** 로 입력한 다음, **확인** 을 선택합니다. 이제 큐에 메시지가 있습니다.

   ![큐 만들기](./media/webjobs-sdk-get-started/hello-world-text.png)

1. **Ctrl+F5** 를 눌러 프로젝트를 실행합니다.

   콘솔에는 런타임이 함수를 발견한 것이 표시됩니다. `ProcessQueueMessage` 함수에서 `QueueTrigger` 특성을 사용했으므로 WebJobs 런타임은 `queue`라는 큐에서 메시지를 수신 대기합니다. 이 큐에서 새 메시지를 찾으면 런타임은 함수를 호출하여 메시지 문자열 값을 전달합니다.

1. **큐** 창으로 돌아가고 창을 새로 고칩니다. 메시지가 로컬로 실행되는 함수에 의해 처리되었으므로 메시지가 사라집니다.

1. 콘솔 창을 닫습니다. 

이제 WebJobs SDK 프로젝트를 Azure에 게시하겠습니다.

## <a name="deploy-to-azure"></a><a name="deploy-as-a-webjob"></a>Azure에 배포

배포 중에 함수를 실행할 앱 서비스 인스턴스를 만듭니다. Azure에서 App Service에 .NET Core 콘솔 앱을 게시하면 자동으로 WebJob으로 실행됩니다. 게시에 대해 자세히 알아보려면 [Visual Studio를 사용하여 WebJobs 개발 및 배포](webjobs-dotnet-deploy-vs.md)를 참조하세요.

### <a name="create-azure-resources"></a>Azure 리소스 만들기

[!INCLUDE [webjobs-publish-net-core](../../includes/webjobs-publish-net-core.md)]

### <a name="enable-always-on"></a>Always On 사용

연속 WebJob의 경우 WebJobs가 올바르게 실행되도록 사이트에서 Always On 설정을 사용하도록 설정해야 합니다. Always On을 사용하도록 설정하지 않으면 몇 분 동안 활동이 없을 경우 런타임이 유휴 상태가 됩니다. 

1. **게시** 페이지에서 **호스팅** 위에 있는 점 세 개를 선택하여 **호스팅 프로필 섹션 작업** 을 표시하고 **Azure Portal에서 열기** 를 선택합니다.  

1. **설정** 에서 **구성** > **일반 설정** 을 선택하고, **Always On** 을 **켜기** 로 설정한 다음, **저장** 및 **계속** 을 선택하여 사이트를 다시 시작합니다.

### <a name="publish-the-project"></a>프로젝트 게시

Azure에서 만든 웹앱을 사용하여 WebJobs 프로젝트를 게시하겠습니다. 

1. **호스팅** 아래 **게시** 페이지에서 편집 단추를 선택하고, **WebJob 형식** 을 `Continuous`로 변경하고, **저장** 을 선택합니다. 이렇게 하면 메시지가 큐에 추가될 때 WebJob이 실행됩니다. 트리거된 WebJobs는 일반적으로 수동 웹후크에만 사용됩니다. 

1. **게시** 페이지의 오른쪽 위에서 **게시** 단추를 선택합니다. 작업이 완료되면 WebJob이 Azure에서 실행됩니다.

### <a name="create-a-storage-connection-app-setting"></a>스토리지 연결 앱 설정 만들기

appsettings.json 구성 파일에서 로컬로 사용한 것과 동일한 스토리지 연결 문자열 설정을 Azure에 만들어야 합니다. 이렇게 하면 연결 문자열을 보다 안전하게 저장할 수 있습니다.   

1. **게시** 프로필 페이지에서 **호스팅** 위에 있는 점 세 개를 선택하여 **호스팅 프로필 섹션 작업** 을 표시하고 **Azure App Service 설정 관리** 를 선택합니다.

1. **애플리케이션 설정** 에서 **+ 설정 추가** 를 선택합니다.

1. **새 앱 설정 이름** 에 `AzureWebJobsStorage`를 입력하고 **확인** 을 선택합니다. 
 
1. **원격** 에서 로컬 설정의 연결 문자열을 붙여넣고 **확인** 을 선택합니다. 

이제 Azure의 앱에서 연결 문자열이 설정됩니다.

### <a name="trigger-the-function-in-azure"></a>Azure에서 함수 트리거

1. 로컬로 실행하고 있지 않은지 확인합니다. 열려 있는 콘솔 창을 닫습니다. 그러지 않으면 사용자가 만드는 모든 큐 메시지를 인스턴스가 먼저 처리할 수 있습니다.

1. Visual Studio의 **큐** 페이지에서 이전처럼 큐에 메시지를 추가합니다.

1. **큐** 페이지를 새로 고치면 Azure에서 실행 중인 함수가 새 메시지를 처리했기 때문에 새 메시지가 사라집니다.

## <a name="enable-application-insights-logging"></a>Application Insights 로깅 사용

WebJob이 Azure에서 실행되면 콘솔 출력을 확인하여 함수 실행을 모니터링할 수 없습니다. WebJob을 모니터링할 수 있으려면 프로젝트를 게시할 때 연결된 [Application Insights](../azure-monitor/app/app-insights-overview.md) 인스턴스를 만들어야 합니다.

### <a name="create-an-application-insights-instance"></a>Application Insights 인스턴스 만들기

1. **게시** 프로필 페이지에서 **호스팅** 위에 있는 점 세 개를 선택하여 **호스팅 프로필 섹션 작업** 을 표시하고 **Azure Portal에서 열기** 를 선택합니다.

1. **설정** 아래 웹앱에서 **Application Insights** 를 선택하고 **Application Insights 켜기** 를 선택합니다.

1. 인스턴스의 생성된 **리소스 이름** 과 **위치** 를 확인하고 **적용** 을 선택합니다. 

1. **설정** 에서 **구성** 을 선택하고 새 `APPINSIGHTS_INSTRUMENTATIONKEY`가 만들어졌는지 확인합니다. 이 키는 Application Insights에 WebJob 인스턴스를 연결하는 데 사용됩니다.   

[Application Insights](../azure-monitor/app/app-insights-overview.md) 로깅을 활용하려면 로깅 코드도 업데이트해야 합니다.

### <a name="install-the-application-insights-extension"></a>Application Insights 확장 설치

1. [Microsoft.Azure.WebJobs.Logging.ApplicationInsights](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Logging.ApplicationInsights) NuGet 패키지의 안정적인 최신 버전 3.x를 가져옵니다.

2. 다음 명령에서 `<3_X_VERSION>`을 1단계에서 찾은 현재 버전 번호로 바꿉니다. NuGet 패키지의 각 유형에는 고유한 버전 번호가 있습니다. 

    ```powershell
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -Version <3_X_VERSION>
    ```
3. **패키지 관리자 콘솔** 에서 `PM>` 진입점에 현재 버전 번호를 사용하여 명령을 실행합니다.

### <a name="initialize-the-application-insights-logging-provider"></a>Application Insights 로깅 공급자 초기화

*Program.cs* 를 열고 `ConfigureLogging`에서 `AddConsole` 호출 뒤에 다음 이니셜라이저를 추가합니다.

```csharp
// If the key exists in settings, use it to enable Application Insights.
string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
if (!string.IsNullOrEmpty(instrumentationKey))
{
    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
}
```

이제 `Main` 메서드 코드는 다음 예제와 같이 표시됩니다. 

```csharp
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment(EnvironmentName.Development);
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    builder.ConfigureLogging((context, b) =>
            {
                b.AddConsole();

                // If the key exists in settings, use it to enable Application Insights.
                string instrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                if (!string.IsNullOrEmpty(instrumentationKey))
                {
                    b.AddApplicationInsightsWebJobs(o => o.InstrumentationKey = instrumentationKey);
                }
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

이는 기본 [필터링](webjobs-sdk-how-to.md#log-filtering)을 사용하여 Application Insights 로깅 공급자를 초기화합니다. 로컬로 실행하는 경우 모든 정보 및 상위 수준의 로그가 콘솔과 Application Insights 모두에 기록됩니다.

### <a name="republish-the-project-and-trigger-the-function-again"></a>프로젝트를 다시 게시하고 함수를 다시 트리거합니다.

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

1. 이전과 같이 *Hello App Insights!* 를 메시지 텍스트로 입력하는 것을 제외하고는 [이전](#test-locally)과 동일한 방식으로 Visual Studio의 **클라우드 탐색기** 를 사용하여 큐 메시지를 만듭니다.

1. **게시** 프로필 페이지에서 **호스팅** 위에 있는 점 세 개를 선택하여 **호스팅 프로필 섹션 작업** 을 표시하고 **Azure Portal에서 열기** 를 선택합니다.

1. **설정** 아래 웹앱에서 **Application Insights** 를 선택하고 **Application Insights 데이터 보기** 를 선택합니다.

1. **검색** 을 선택한 다음, **지난 24시간 동안의 모든 데이터 보기** 를 선택합니다.

   ![검색 선택](./media/webjobs-sdk-get-started/select-search.png)

1. *Hello App Insights!* 가 표시되지 않으면 몇 분 동안 정기적으로 **새로 고침** 을 선택합니다. Application Insights 클라이언트에서 처리하는 로그를 플러시하는 데 시간이 걸리기 때문에 로그가 즉시 표시되지 않습니다.

   ![Application Insights의 로그](./media/webjobs-sdk-get-started/logs-in-ai.png)

## <a name="add-inputoutput-bindings&quot;></a>입출력 바인딩 추가

바인딩은 데이터를 읽고 쓰는 코드를 간소화합니다. 입력 바인딩은 데이터를 읽는 코드를 간소화합니다. 출력 바인딩은 데이터를 쓰는 코드를 간소화합니다.  

### <a name=&quot;add-input-binding&quot;></a>입력 바인딩 추가

입력 바인딩은 데이터를 읽는 코드를 간소화합니다. 이 예제의 경우 큐 메시지는 Blob 이름으로, Azure Storage에서 Blob을 찾고 읽는 데 사용됩니다.

1. *Functions.cs* 에서 `ProcessQueueMessage` 메서드를 다음 코드로 바꿉니다.

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger(&quot;queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
   }
   ```

   이 코드에서 `queueTrigger`는 런타임에 다른 값으로 확인되는 [바인딩 식](../azure-functions/functions-bindings-expressions-patterns.md)입니다.  런타임에 큐 메시지의 내용이 포함됩니다.

1. `using`을 추가합니다.

   ```cs
   using System.IO;
   ```

1. 스토리지 계정에 Blob 컨테이너를 만듭니다.

   a. Visual Studio의 **클라우드 탐색기** 에서 스토리지 계정의 노드를 확장하고, **Blob** 을 마우스 오른쪽 단추로 클릭한 다음, **Blob 컨테이너 만들기** 를 선택합니다.

   b. **Blob 컨테이너 만들기** 대화 상자에서 *container* 를 컨테이너 이름으로 입력한 다음, **확인** 을 선택합니다.

1. *Program.cs* 파일을 Blob 컨테이너에 업로드합니다. (여기서는 이 파일이 예제로 사용되며, 모든 텍스트 파일을 업로드하고, 파일 이름이 포함된 큐 메시지를 만들 수 있습니다.)

   a. **클라우드 탐색기** 에서 직접 만든 컨테이너의 노드를 두 번 클릭합니다.

   b. **컨테이너** 창에서 **업로드** 단추를 선택합니다.

   ![Blob 업로드 단추](./media/webjobs-sdk-get-started/blob-upload-button.png)

   다. *Program.cs* 를 찾아 선택한 다음, **확인** 을 선택합니다.

1. 메시지 텍스트로 *Program.cs* 가 포함된 큐 메시지를 앞에서 만든 큐에 만듭니다.

   ![Program.cs 큐 메시지](./media/webjobs-sdk-get-started/queue-msg-program-cs.png)

1. 로컬로 프로젝트를 실행합니다.

   큐 메시지는 함수를 트리거하여 Blob을 읽고 해당 길이를 로깅합니다. 콘솔 출력은 다음과 같습니다.

   ```console
   Found the following functions:
   ConsoleApp1.Functions.ProcessQueueMessage
   Job host started
   Executing 'Functions.ProcessQueueMessage' (Reason='New queue message detected on 'queue'.', Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   Blob name:Program.cs
   Size: 532 bytes
   Executed 'Functions.ProcessQueueMessage' (Succeeded, Id=5a2ac479-de13-4f41-aae9-1361f291ff88)
   ```
### <a name="add-an-output-binding"></a>출력 바인딩 추가

출력 바인딩은 데이터를 쓰는 코드를 간소화합니다. 이 예제에서는 크기를 로깅하지 않고 Blob 복사본을 작성하여 이전 예제를 수정합니다. Blob Storage 바인딩은 이전에 설치한 Azure Storage 확장 패키지에 포함되어 있습니다.

1. `ProcessQueueMessage` 메서드를 다음 코드로 바꿉니다.

   ```cs
   public static void ProcessQueueMessage(
       [QueueTrigger("queue")] string message,
       [Blob("container/{queueTrigger}", FileAccess.Read)] Stream myBlob,
       [Blob("container/copy-{queueTrigger}", FileAccess.Write)] Stream outputBlob,
       ILogger logger)
   {
       logger.LogInformation($"Blob name:{message} \n Size: {myBlob.Length} bytes");
       myBlob.CopyTo(outputBlob);
   }
   ```

1. 메시지 텍스트로 *Program.cs* 가 포함된 또 다른 큐 메시지를 만듭니다.

1. 로컬로 프로젝트를 실행합니다.

   큐 메시지는 함수를 트리거하여 Blob을 읽고, 해당 길이를 로깅하고, 새 Blob을 만듭니다. 콘솔 출력은 동일하지만, Blob 컨테이너 창으로 이동하여 **새로 고침** 을 선택하면 *copy-Program.cs* 라는 새 Blob이 표시됩니다.

### <a name="republish-the-project"></a>프로젝트 다시 게시

1. **솔루션 탐색기** 에서 프로젝트를 마우스 오른쪽 단추로 클릭하고 **게시** 를 선택합니다.

1. **게시** 대화 상자에서 현재 프로필이 선택되어 있는지 확인한 다음, **게시** 를 선택합니다. 게시 결과는 **출력** 창에 자세히 표시됩니다.
 
1. Blob 컨테이너에 파일을 다시 업로드하고 큐에 업로드된 파일의 이름을 메시지로 추가하여 Azure에서 함수를 확인합니다. 큐에서 제거된 메시지와 Blob 컨테이너에 생성된 파일의 복사본이 표시됩니다. 

## <a name="next-steps"></a>다음 단계

이 자습서에서는 WebJobs SDK 3.x 프로젝트를 만들고 실행하고 배포하는 방법을 살펴보았습니다.

> [!div class="nextstepaction"]
> [WebJobs SDK에 대한 자세한 정보](webjobs-sdk-how-to.md)