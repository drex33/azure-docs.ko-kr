---
title: Visual Studio를 사용하여 Azure Functions 개발
description: Visual Studio 2019용 Azure Functions 도구를 사용하여 Azure Functions를 개발하고 테스트하는 방법을 알아봅니다.
ms.custom: vs-azure, devx-track-csharp
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: a8be86708f2f3a8394b1e6e17d70a9d9038dc6e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039281"
---
# <a name="develop-azure-functions-using-visual-studio"></a>Visual Studio를 사용하여 Azure Functions 개발  

Visual Studio를 사용하면 C# 클래스 라이브러리 함수를 개발, 테스트하여 Azure에 배포할 수 있습니다. Azure Functions를 처음 사용하는 경우, [Azure Functions 소개](functions-overview.md)를 참조하세요.

Visual Studio로 함수를 개발하면 다음과 같은 이점이 있습니다. 

* 로컬 개발 컴퓨터에서 함수를 편집, 빌드 및 실행합니다. 
* Azure Functions 프로젝트를 Azure에 직접 게시하고 필요에 따라 Azure 리소스를 만듭니다. 
* C# 특성을 사용하여 C# 코드에서 직접 함수 바인딩을 선언합니다.
* 미리 컴파일된 C# 함수를 개발하고 배포합니다. 미리 컴파일된 함수는 C# 스크립트 기반 함수보다 더 뛰어난 콜드 부팅 성능을 제공합니다. 
* Visual Studio 개발의 모든 이점을 누리면서 C#에서 함수를 코딩합니다. 

이 문서에서는 Visual Studio를 사용하여 C# 클래스 라이브러리 함수를 개발하고 Azure에 게시하는 방법에 대해 자세히 설명합니다. 이 문서를 읽기 전에 [Visual Studio용 Functions 빠른 시작](functions-create-your-first-function-visual-studio.md)을 읽는 것이 좋습니다. 

별도로 언급하지 않는 한, 표시된 프로시저와 예제는 Visual Studio 2019에 대한 것입니다. 

## <a name="prerequisites"></a>사전 요구 사항

- Azure Functions 도구. Azure Functions 도구를 추가하려면 Visual Studio 설치 시 **Azure 개발** 워크로드를 포함합니다. Azure Functions 도구는 Visual Studio 2017부터 Azure 개발 워크로드에서 사용할 수 있습니다.

- Azure Storage 계정과 같이 필요한 다른 리소스는 게시 프로세스 중에 구독에서 만들어 집니다.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> Visual Studio 2017에서 Azure 개발 워크로드는 Azure Functions 도구를 별도의 확장으로 설치합니다. Visual Studio 2017 설치를 업데이트할 경우 Azure Functions 도구의 [최신 버전](#check-your-tools-version)을 사용하고 있는지 확인합니다. 다음 섹션에서는 Visual Studio 2017에서 Azure Functions 도구 확장을 확인하고 필요한 경우 업데이트하는 방법을 보여 줍니다. 
>
> Visual Studio 2019을 사용하는 경우에는 이 섹션을 건너뜁니다.

### <a name="check-your-tools-version-in-visual-studio-2017"></a><a name="check-your-tools-version"></a>Visual Studio 2017에서 도구 버전 확인

1. **도구** 메뉴에서 **확장 및 업데이트** 를 선택합니다. **설치됨** > **도구** 를 펼치고 **Azure Functions 및 웹 작업 도구** 를 선택합니다.

    ![함수 도구 버전 확인](./media/functions-develop-vs/functions-vstools-check-functions-tools.png)

1. 설치된 **버전** 을 확인하고 [릴리스 정보](https://github.com/Azure/Azure-Functions/blob/master/VS-AzureTools-ReleaseNotes.md)에 나열된 최신 버전과 지금 설치된 버전을 비교합니다. 

1. 최신 버전이 아닌 경우 다음 섹션에 표시된 대로 Visual Studio에서 도구를 업데이트합니다.

### <a name="update-your-tools-in-visual-studio-2017"></a>Visual Studio 2017에서 도구 업데이트

1. **확장명 및 업데이트** 대화 상자에서 **업데이트** > **Visual Studio Marketplace** 를 확장하고, **Azure Functions 및 웹 작업 도구** 를 선택하고 **업데이트** 를 선택합니다.

    ![함수 도구 버전 업데이트](./media/functions-develop-vs/functions-vstools-update-functions-tools.png)   

1. 도구 업데이트를 다운로드한 후에 **닫기** 를 선택한 다음 Visual Studio를 닫고 VSIX 설치 프로그램을 사용하여 도구 업데이트를 트리거합니다.

1. VSIX 설치 프로그램에서 **수정** 을 선택하여 도구를 업데이트합니다. 

1. 업데이트가 완료되면 **닫기** 를 선택하고 Visual Studio를 다시 시작합니다.

> [!NOTE]  
> Visual Studio 2019 이상에서 Azure Functions 도구 확장은 Visual Studio의 일부로 업데이트됩니다.  

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

[!INCLUDE [Create a project using the Azure Functions](../../includes/functions-vstools-create.md)]

Azure Functions 프로젝트를 만든 후 프로젝트 템플릿은 C# 프로젝트를 만들고, `Microsoft.NET.Sdk.Functions` NuGet 패키지를 설치하고, 대상 프레임워크를 설정합니다. 새 프로젝트에는 다음 파일이 포함됩니다.

* **host.json**: 함수 호스트를 구성할 수 있습니다. 이러한 설정은 로컬 및 Azure에서 실행할 때 모두 적용됩니다. 자세한 내용은 [host.json 참조](functions-host-json.md)를 참조하세요.

* **local.settings.json**: 함수를 로컬로 실행할 때 사용되는 설정을 유지합니다. 이러한 설정은 Azure에서 실행하는 경우에는 사용되지 않습니다. 자세한 내용은 [로컬 설정 파일](#local-settings)을 참조하세요.

    >[!IMPORTANT]
    >local.settings.json 파일에 암호가 있을 수 있으므로 프로젝트 원본 제어에서 해당 파일을 제외해야 합니다. 이 파일에 대한 **출력 디렉터리로 복사** 설정은 **새 내용이면 복사** 로 설정해야 합니다. 

자세한 내용은 [Functions 클래스 라이브러리 프로젝트](functions-dotnet-class-library.md#functions-class-library-project)를 참조하세요.

[!INCLUDE [functions-local-settings-file](../../includes/functions-local-settings-file.md)]

Visual Studio는 프로젝트를 게시할 때 local.settings.json의 설정을 자동으로 업로드하지 않습니다. 이러한 설정이 Azure의 함수 앱에도 있는지 확인하려면 프로젝트를 게시한 후에 이를 업로드합니다. 자세한 내용은 [함수 앱 설정](#function-app-settings)을 참조하세요. `ConnectionStrings` 컬렉션의 값은 전혀 게시되지 않습니다.

코드는 함수 앱 설정 값을 환경 변수로 읽을 수도 있습니다. 자세한 내용은 [환경 변수](functions-dotnet-class-library.md#environment-variables)를 참조하세요.

## <a name="configure-your-build-output-settings"></a>빌드 출력 설정 구성

Azure Functions 프로젝트를 빌드할 때 빌드 도구는 함수 런타임과 공유되는 어셈블리의 복사본 하나만 유지되도록 출력을 최적화합니다. 이를 통해 최대한 많은 공간을 절약하는 최적화된 빌드를 제공합니다. 그러나 프로젝트 어셈블리의 최신 버전으로 이동하면 빌드 도구에서 이러한 어셈블리를 유지해야 한다는 사실을 모를 수 있습니다. 최적화 프로세스 중에 이러한 어셈블리가 유지되는지 확인하려면 프로젝트 (.csproj) 파일의 `FunctionsPreservedDependencies` 요소를 사용하여 해당 어셈블리를 지정할 수 있습니다.

```xml
  <ItemGroup>
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Extensions.dll" />
    <FunctionsPreservedDependencies Include="Microsoft.AspNetCore.Http.Features.dll" />
  </ItemGroup>
```

## <a name="configure-the-project-for-local-development"></a>로컬 개발에 대한 프로젝트 구성

함수 런타임에서 Azure Storage 계정을 내부적으로 사용합니다. HTTP 및 웹후크 이외의 모든 트리거 형식에 대해 `Values.AzureWebJobsStorage` 키를 유효한 Azure Storage 계정 연결 문자열에 설정해야 합니다. 함수 앱은 프로젝트에 필요한 `AzureWebJobsStorage` 연결 설정에 [Azure Storage 에뮬레이터](../storage/common/storage-use-emulator.md)를 사용할 수도 있습니다. 에뮬레이터를 사용하려면 이러한 `AzureWebJobsStorage` 값을 `UseDevelopmentStorage=true`로 설정합니다. 배포하기 전에 이 설정을 실제 스토리지 계정 연결로 변경해야 합니다.

스토리지 계정 연결 문자열을 설정하려면 다음을 수행합니다.

1. Visual Studio에서 **뷰** > **클라우드 탐색기** 를 선택합니다.

2. **클라우드 탐색기** 에서 **스토리지 계정** 을 펼치고 사용 중인 스토리지 계정을 선택합니다. **속성** 탭에서 **기본 연결 문자열** 값을 복사합니다.

2. 프로젝트에서 local.settings.json 파일을 열고 `AzureWebJobsStorage` 키의 값을 복사한 연결 문자열로 설정합니다.

3. 이전 단계를 반복하여 함수에 필요한 다른 연결에 대한 `Values` 배열에 고유 키를 추가합니다. 

## <a name="add-a-function-to-your-project"></a>프로젝트에 함수 추가

C# 클래스 라이브러리 함수에서, 함수에 사용된 바인딩은 코드에 특성을 적용하여 정의됩니다. 제공된 템플릿에서 함수 트리거를 만들면 트리거 특성이 적용됩니다. 

1. **솔루션 탐색기** 에서 프로젝트 노드를 마우스 오른쪽 단추로 클릭하고 **추가** > **새 항목** 을 차례로 선택합니다. 

2. **Azure Function** 을 선택하고 클래스의 **이름** 을 입력한 다음 **추가** 를 선택합니다.

3. 사용자의 트리거를 선택하고, 바인딩 속성을 설정한 후, **확인** 을 선택합니다. 다음 예제에서는 Queue Storage 트리거 함수를 만들기 위한 설정을 보여 줍니다. 

    ![Queue Storage 트리거 함수 만들기](./media/functions-develop-vs/functions-vstools-create-queuetrigger.png)

    이 트리거 예제에서는 키 이름이 `QueueStorage`인 연결 문자열을 사용합니다. 이 연결 문자열 설정은 [local.settings.json 파일](functions-develop-local.md#local-settings-file)에서 정의합니다.

4. 새로 추가된 클래스를 검사합니다. `FunctionName` 특성을 사용하는 정적 `Run()` 메소드가 표시됩니다. 이 특성은 메서드가 함수에 대한 진입점임을 나타냅니다.

    예를 들어 다음 C# 클래스는 기본 Queue Storage 트리거 함수를 나타냅니다.

    ```csharp
    using System;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.WebJobs.Host;
    using Microsoft.Extensions.Logging;

    namespace FunctionApp1
    {
        public static class Function1
        {
            [FunctionName("QueueTriggerCSharp")]
            public static void Run([QueueTrigger("myqueue-items", 
                Connection = "QueueStorage")]string myQueueItem, ILogger log)
            {
                log.LogInformation($"C# Queue trigger function processed: {myQueueItem}");
            }
        }
    }
    ```

바인딩 고유 특성은 진입점 메서드에 적용되는 각 바인딩 매개 변수에 적용됩니다. 특성은 매개 변수로 바인딩 정보를 사용합니다. 이전 예제에서 첫 매개 변수는 Queue Storage 트리거 함수를 나타내는 `QueueTrigger` 특성이 적용되었습니다. 큐 이름 및 연결 문자열 설정 이름은 `QueueTrigger` 특성에 매개 변수로 전달됩니다. 자세한 내용은 [Azure Functions의 Azure Queue Storage 바인딩](functions-bindings-storage-queue-trigger.md)을 참조하세요.

위의 프로시저를 사용하여 함수 앱 프로젝트에 더 많은 함수를 추가할 수 있습니다. 프로젝트의 각 함수는 다른 트리거를 가질 수 있지만 함수에는 정확히 하나의 트리거가 있어야 합니다. 자세한 내용은 [Azure Functions 트리거 및 바인딩 개념](functions-triggers-bindings.md)을 참조하세요.

## <a name="add-bindings"></a>바인딩 추가

트리거와 마찬가지로 입력 및 출력 바인딩이 바인딩 특성으로 함수에 추가됩니다. 다음과 같이 함수에 바인딩을 추가합니다.

1. [로컬 개발에 대한 프로젝트를 구성](#configure-the-project-for-local-development)했는지 확인합니다.

2. 특정 바인딩에 대한 적절한 NuGet 확장 패키지를 추가합니다. 

   자세한 내용은 [Visual Studio를 사용하는 C# 클래스 라이브러리](./functions-bindings-register.md#local-csharp)를 참조하세요. 바인딩에 대한 참조 문서에서 바인딩 관련 NuGet 패키지 요구 사항을 찾습니다. 예를 들어 [Event Hubs 바인딩 참조 문서](functions-bindings-event-hubs.md)에서 Event Hubs 트리거에 대한 패키지 요구 사항을 찾습니다.

3. 바인딩에 필요한 앱 설정이 있는 경우 [로컬 설정 파일](functions-develop-local.md#local-settings-file)의 `Values` 컬렉션에 추가합니다. 

   함수는 로컬로 실행될 때 이러한 값을 사용합니다. 함수가 Azure의 함수 앱에서 실행될 때 [함수 앱 설정](#function-app-settings)이 사용됩니다.

4. 메서드 서명에 적절한 바인딩 특성을 추가합니다. 다음 예제에서 큐 메시지는 함수를 트리거하고, 출력 바인딩은 다른 큐와 동일한 텍스트가 있는 새 큐 메시지를 만듭니다.

    ```csharp
    public static class SimpleExampleWithOutput
    {
        [FunctionName("CopyQueueMessage")]
        public static void Run(
            [QueueTrigger("myqueue-items-source", Connection = "AzureWebJobsStorage")] string myQueueItem, 
            [Queue("myqueue-items-destination", Connection = "AzureWebJobsStorage")] out string myQueueItemCopy,
            ILogger log)
        {
            log.LogInformation($"CopyQueueMessage function processed: {myQueueItem}");
            myQueueItemCopy = myQueueItem;
        }
    }
    ```

   Queue Storage에 대한 연결은 `AzureWebJobsStorage` 설정에서 가져옵니다. 자세한 내용은 특정 바인딩에 대한 참조 문서를 참조하세요. 

[!INCLUDE [Supported triggers and bindings](../../includes/functions-bindings.md)]

## <a name="testing-functions"></a>테스팅 함수

Azure Functions Core 도구를 사용하면 로컬 개발 컴퓨터에서 Azure Functions 프로젝트를 실행할 수 있습니다. 자세한 내용은 [Azure Functions Core 도구로 작업](functions-run-local.md)을 참조하세요. Visual Studio에서 처음으로 함수를 시작할 때 이러한 도구를 설치하라는 메시지가 표시됩니다. 

다음과 같이 Visual Studio에서 함수를 테스트합니다.

1. F5 키를 누릅니다. 메시지가 표시되면 Visual Studio에서 Azure Functions Core(CLI) 도구를 다운로드하여 설치하도록 요구하는 요청을 수락합니다. 또한 도구에서 HTTP 요청을 처리할 수 있도록 방화벽 예외를 사용하도록 설정해야 할 수도 있습니다.

2. 프로젝트가 실행 중인 상태에서 배포된 기능을 테스트할 때처럼 코드를 테스트합니다. 

   자세한 내용은 [Azure Functions에서 코드를 테스트하기 위한 전략](functions-test-a-function.md)을 참조하세요. 디버그 모드에서 Visual Studio를 실행하면 중단점이 예상대로 적중됩니다.

<!---
For an example of how to test a queue triggered function, see the [queue triggered function quickstart tutorial](functions-create-storage-queue-triggered-function.md#test-the-function).  
-->


## <a name="publish-to-azure"></a>Azure에 게시

Visual Studio에서 게시하는 경우 다음 두 가지 배포 방법 중 하나를 사용합니다.

* [웹 배포](functions-deployment-technologies.md#web-deploy-msdeploy): Windows 앱을 패키징하고 모든 IIS 서버에 배포합니다.
* [패키지 실행을 사용하는 Zip 배포](functions-deployment-technologies.md#zip-deploy): Azure Functions 배포에 권장됩니다.

다음 단계를 사용하여 Azure의 함수 앱에 프로젝트를 게시합니다.

[!INCLUDE [Publish the project to Azure](../../includes/functions-vstools-publish.md)]

## <a name="function-app-settings"></a>함수 앱 설정

Visual Studio는 프로젝트를 게시할 때 이러한 설정을 자동으로 업로드하지 않으므로 local.settings.json에 추가하는 모든 설정은 Azure의 함수 앱에도 추가해야 합니다.

Azure의 함수 앱에 필요한 설정을 업로드하는 가장 쉬운 방법은 프로젝트를 성공적으로 게시한 후에 표시되는 **Azure App Service 설정 관리...** 링크를 선택하는 것입니다.

:::image type="content" source="./media/functions-develop-vs/functions-vstools-app-settings.png" alt-text="게시 창에서 설정":::

이 링크를 선택하면 새로운 애플리케이션 설정을 추가하거나 기존 애플리케이션 설정을 수정할 수 있는 함수 앱에 대한 **애플리케이션 설정** 대화 상자가 표시됩니다.

![애플리케이션 설정](./media/functions-develop-vs/functions-vstools-app-settings2.png)

**로컬** 은 local.settings.json 파일의 설정 값을 표시하며 **원격** 은 Azure의 함수 앱에 대한 현재 설정 값입니다. **설정 추가** 를 선택하여 새로운 앱 설정을 만듭니다. **로컬 값 삽입** 링크를 사용하여 **원격** 필드에 설정 값을 복사합니다. **확인** 을 선택하면 보류 중인 변경 내용이 로컬 설정 파일 및 함수 앱에 기록됩니다.

> [!NOTE]
> 기본적으로 local.settings.json 파일은 소스 제어에 체크인되지 않습니다. 즉, 소스 제어에서 로컬 Functions 프로젝트를 복제하는 경우 프로젝트에는 local.settings.json 파일이 없습니다. 이 경우 **애플리케이션 설정** 대화 상자가 예상대로 작동하도록 프로젝트 루트에서 local.settings.json 파일을 수동으로 만들어야 합니다. 

또한 다음 다른 방법 중 하나로 애플리케이션 설정을 관리할 수도 있습니다.

* [Azure 포털을 사용합니다](functions-how-to-use-azure-function-app-settings.md#settings).
* [Azure Functions Core 도구에서 `--publish-local-settings` 게시 옵션을 사용합니다](functions-run-local.md#publish).
* [Azure CLI를 사용합니다](/cli/azure/functionapp/config/appsettings#az_functionapp_config_appsettings_set).

## <a name="monitoring-functions"></a>함수 모니터링

함수 실행을 모니터링할 때는 Azure Application Insights와 함수 앱을 통합하는 방식을 사용하는 것이 좋습니다. Azure Portal에서 함수 앱을 만들 때는 이 통합이 기본적으로 자동 수행됩니다. 그러나 Visual Studio 게시 중에 함수 앱을 만들 때는 Azure에서 함수 앱 통합이 수행되지 않습니다. Application Insights를 함수 앱에 연결하는 방법은 [Application Insights 통합 사용](configure-monitoring.md#enable-application-insights-integration)을 참조하세요.

Application Insights를 사용하여 모니터링하는 방법에 대한 자세한 내용은 [Azure Functions 모니터링](functions-monitoring.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Functions Core 도구에 대한 자세한 내용은 [Azure Functions Core 도구로 작업](functions-run-local.md)을 참조하세요.

.NET 클래스 라이브러리와 같은 함수를 개발하는 방법에 대한 자세한 내용은 [Azure Functions C# 개발자 참조](functions-dotnet-class-library.md)를 참조하세요. 이 문서에서는 특성을 사용하여 Azure Functions에서 지원하는 다양한 유형의 바인딩을 선언하는 방법의 예제로 연결합니다.    
