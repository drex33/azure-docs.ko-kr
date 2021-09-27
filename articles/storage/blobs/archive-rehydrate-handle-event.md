---
title: Blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행
titleSuffix: Azure Storage
description: .NET을 사용하여 Azure Function을 개발한 다음, 보관 계층에서 Blob을 리하이드레이션할 때 발생하는 이벤트에 대응하여 함수를 실행하도록 Azure Event Grid를 구성하는 방법을 알아봅니다.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/11/2021
ms.author: tamram
ms.reviewer: fryu
ms.subservice: blobs
ms.openlocfilehash: 3ce2f92f88e24eb634222db5ffab45acaf1a5820
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577995"
---
# <a name="run-an-azure-function-in-response-to-a-blob-rehydration-event"></a>Blob 리하이드레이션 이벤트에 대한 응답으로 Azure 함수 실행

보관 계층에 있는 blob을 읽으려면 먼저 blob을 핫 또는 쿨 계층으로 리하이드레이션합니다. 리하이드레이션 프로세스를 완료하는 데 몇 시간이 걸릴 수 있습니다. 리하이드레이션 작업의 상태를 반복적으로 폴링하는 대신, Blob 리하이드레이션 작업이 완료되면 이벤트를 실행하고 애플리케이션에서 이 이벤트를 처리하도록 [Azure Event Grid](../../event-grid/overview.md)를 구성할 수 있습니다.

이벤트가 발생하면 Event Grid에서 엔드포인트를 통해 이벤트 처리기에 이벤트를 보냅니다. [Azure Functions](../../azure-functions/functions-overview.md)를 비롯한 여러 Azure 서비스가 이벤트 처리기 역할을 할 수 있습니다. Azure Function은 이벤트에 대한 응답으로 실행할 수 있는 코드 블록입니다. 이 방법에서는 Azure Function을 개발한 다음, Blob이 리하이드레이션될 때 발생하는 이벤트에 대한 응답으로 함수를 실행하도록 Event Grid를 구성하는 프로세스를 안내합니다.

이 문서에서는 Visual Studio에서 .NET을 사용하여 Azure Function을 만들고 테스트하는 방법을 보여 줍니다. 다양한 로컬 개발 환경에서 다양한 프로그래밍 언어를 사용하여 Azure Functions를 빌드할 수 있습니다. Azure Functions에 지원되는 언어에 대한 자세한 내용은 [Azure Functions에서 지원되는 언어](../../azure-functions/supported-languages.md)를 참조하세요. Azure Functions의 개발 옵션에 대한 자세한 내용은 [로컬에서 Azure Functions 코딩 및 테스트](../../azure-functions/functions-develop-local.md)를 참조하세요.

보관 계층에서 Blob 리하이드레이션하는 데 관한 자세한 내용은 [보관 계층에서 Blob 리하이드레이션 개요](archive-rehydrate-overview.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

이 문서에서는 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/)를 사용하여 .NET으로 Azure Function을 개발하는 방법을 보여 줍니다. Visual Studio Community를 무료로 설치할 수 있습니다. [.NET으로 Azure 개발용 Visual Studio를 구성](/dotnet/azure/configure-visual-studio)해야 합니다.

Azure Function을 로컬로 디버그하려면 Postman과 같은 HTTP 요청을 보낼 수 있는 도구를 사용해야 합니다.

[Azure 구독](../../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing)이 필요합니다. 아직 계정이 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/dotnet/)을 만듭니다.

## <a name="create-an-azure-function-app"></a>Azure 함수 앱 만들기

함수 앱은 Azure Functions의 컨테이너 역할을 하는 Azure 리소스입니다. 새 함수 앱 또는 기존 함수 앱을 사용하여 이 문서에 설명된 단계를 완료할 수 있습니다.

Azure Portal에서 새 함수 앱을 만들려면 다음 단계를 따르세요.

1. Azure Portal에서 ‘함수 앱’을 검색합니다. **함수 앱** 아이콘을 선택하여 구독에 있는 함수 앱 목록으로 이동합니다.
1. **만들기** 단추를 선택하여 새 함수 앱을 만듭니다.
1. **기본** 탭에서 리소스 그룹을 지정하고 새 함수 앱의 고유한 이름을 제공합니다.
1. **게시** 옵션이 *코드* 로 설정되어 있는지 확인합니다.
1. **런타임 스택** 드롭다운에서 *.NET* 을 선택합니다. **버전** 필드는 최신 버전의 .NET Core를 사용하도록 자동으로 입력됩니다.
1. 새 함수 앱의 지역을 선택합니다.

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-basics-tab.png" alt-text="Azure에서 새 함수 앱을 만드는 방법을 보여 주는 스크린샷 - 기본 탭":::

1. **기본** 탭을 완료한 후 **호스팅** 탭으로 이동합니다.
1. **호스팅** 탭에서 Azure Function이 저장될 스토리지 계정을 선택합니다. 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만들 수 있습니다.
1. **운영 체제** 필드가 *Windows* 로 설정되어 있는지 확인합니다.
1. **계획 형식** 필드에서 ‘사용(서버리스)’을 선택합니다. 이 계획에 대한 자세한 내용은 [Azure Functions 사용 계획 호스팅](../../azure-functions/consumption-plan.md)을 참조하세요.

    :::image type="content" source="media/archive-rehydrate-handle-event/create-function-app-hosting-tab.png" alt-text="Azure에서 새 함수 앱을 만드는 방법을 보여 주는 스크린샷 - 호스팅 탭":::

1. **검토 + 만들기** 를 선택하여 새 함수 앱을 만듭니다.

함수 앱 구성에 관한 자세한 내용은 Azure Functions 문서에서 [함수 앱 관리](../../azure-functions/functions-how-to-use-azure-function-app-settings.md)를 참조하세요.

## <a name="create-an-azure-function-as-an-event-grid-trigger"></a>Event Grid 트리거를 사용하여 Azure Function 만들기

다음으로, 특정 스토리지 계정에서 blob을 리하이드레이션할 때 실행되는 Azure Function을 만듭니다. C# 및 .NET Core를 사용하여 Visual Studio에서 Azure Function을 만들려면 다음 단계를 따르세요.

1. Visual Studio 2019를 시작하고 새 Azure Functions 프로젝트를 만듭니다. 자세한 내용은 [함수 앱 프로젝트 만들기](../../azure-functions/functions-create-your-first-function-visual-studio.md#create-a-function-app-project)에 설명된 지침을 따르세요.
1. **새 Azure Functions 애플리케이션 만들기** 단계에서 다음 값을 선택합니다.
    - 기본적으로 Azure Functions 런타임은 **Azure Functions v3(.NET Core)** 으로 설정됩니다. Microsoft는 이 버전의 Azure Functions 런타임을 사용하는 것이 좋습니다.
    - 가능한 트리거 목록에서 **Event Grid 트리거** 를 선택합니다. Azure Function으로 Blob Storage 이벤트를 처리하는 데 권장되는 트리거 형식이 Event Grid 트리거인 이유에 관한 자세한 내용은 [Event Grid 이벤트에 대한 이벤트 처리기로 함수 사용](../../event-grid/handler-functions.md)을 참조하세요.
    - **스토리지 계정** 설정에서 Azure Function이 저장될 위치를 표시합니다. 기존 스토리지 계정을 선택하거나 새 스토리지 계정을 만듭니다.
1. **만들기** 를 선택하여 Visual Studio에서 새 프로젝트를 만듭니다.
1. 그런 다음 [함수 이름 바꾸기](../../azure-functions/functions-create-your-first-function-visual-studio.md#rename-the-function)에 설명된 대로 클래스와 Azure Function의 이름을 바꿉니다. 시나리오에 적합한 이름을 선택합니다.
1. Visual Studio에서 **도구** | **NuGet 패키지 관리자** | **패키지 관리자 콘솔** 을 선택하고, 콘솔에서 다음 패키지를 설치합니다.

    ```powershell
    Install-Package Azure.Storage.Blobs
    Install-Package Microsoft.ApplicationInsights.WorkerService
    Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights
    ```

1. Azure Function의 클래스 파일에서 다음 using 문을 붙여넣습니다.

    ```csharp
    using System;
    using System.IO;
    using System.Text;
    using Microsoft.Azure.WebJobs;
    using Microsoft.Azure.EventGrid.Models;
    using Microsoft.Azure.WebJobs.Extensions.EventGrid;
    using Microsoft.Extensions.Logging;
    using Azure;
    using Azure.Storage.Blobs;
    using Azure.Storage.Blobs.Models;
    ```

1. 클래스 파일에서 **Run** 메서드를 찾습니다. 이벤트가 발생할 때 실행되는 메서드입니다. **Run** 메서드의 본문에 다음 코드를 붙여넣습니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿔야 합니다.

    ```csharp
    // When either Microsoft.Storage.BlobCreated or Microsoft.Storage.BlobTierChanged
    // event occurs, write the event details to a log blob in the same container
    // as the event subject (the blob for which the event occurred).

    // Create a unique name for the log blob.
    string logBlobName = string.Format("function-log-{0}.txt", DateTime.UtcNow.Ticks);

    // Populate connection string with your Shared Key credentials.
    const string ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account-name>;AccountKey=<account-key>;EndpointSuffix=core.windows.net";

    // Get data from the event.
    dynamic data = eventGridEvent.Data;
    string eventBlobUrl = Convert.ToString(data.url);
    string eventApi = Convert.ToString(data.api);

    // Build string containing log information.
    StringBuilder eventInfo = new StringBuilder();
    eventInfo.AppendLine(string.Format("{0} operation occurred.", eventApi));
    eventInfo.AppendLine(string.Format("Blob URL: {0}", eventBlobUrl));
    eventInfo.AppendLine($@"Additional event details:
        Id=[{eventGridEvent.Id}]
        EventType=[{eventGridEvent.EventType}]
        EventTime=[{eventGridEvent.EventTime}]
        Subject=[{eventGridEvent.Subject}]
        Topic=[{eventGridEvent.Topic}]");

    // If event was BlobCreated and API call was CopyBlob, respond to the event.
    bool copyBlobEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobCreated") &&
                                 (eventApi == "CopyBlob");

    // If event was BlobTierChanged and API call was SetBlobTier, respond to the event.
    bool setTierEventOccurred = (eventGridEvent.EventType == "Microsoft.Storage.BlobTierChanged") &&
                                (eventApi == "SetBlobTier");

    // If one of these two events occurred, write event info to a log blob.
    if (copyBlobEventOccurred | setTierEventOccurred)
    {
        // Create log blob in same account and container.
        BlobUriBuilder logBlobUriBuilder = new BlobUriBuilder(new Uri(eventBlobUrl))
        {
            BlobName = logBlobName
        };

        BlobClient logBlobClient = new BlobClient(ConnectionString,
                                                  logBlobUriBuilder.BlobContainerName,
                                                  logBlobName);

        byte[] byteArray = Encoding.ASCII.GetBytes(eventInfo.ToString());

        try
        {
            // Write the log info to the blob.
            // Overwrite if the blob already exists.
            using (MemoryStream memoryStream = new MemoryStream(byteArray))
            {
                BlobContentInfo blobContentInfo =
                    logBlobClient.Upload(memoryStream, overwrite: true);
            }
        }
        catch (RequestFailedException e)
        {
            Console.WriteLine(e.Message);
            throw;
        }
    }
    ```

Azure Functions 개발에 대한 자세한 내용은 [Azure Functions 개발 참고 자료](../../azure-functions/functions-reference.md)를 참조하세요.

Blob Storage 이벤트가 이벤트 처리기에 게시될 때 포함되는 정보에 관한 자세한 내용은 [Event Grid 소스로서의 Azure Blob Storage](../../event-grid/event-schema-blob-storage.md)를 참조하세요.

## <a name="run-the-azure-function-locally-in-the-debugger"></a>디버거에서 로컬로 Azure Function을 실행합니다.

Azure Function 코드를 로컬로 테스트하려면 이벤트를 트리거하는 HTTP 요청을 수동으로 전송해야 합니다. Postman과 같은 도구를 사용하여 요청을 게시할 수 있습니다.

Azure Function의 클래스 파일 맨 위에는 로컬 환경에서 테스트하는 데 사용할 수 있는 URL 엔드포인트가 있습니다. 이 URL로 요청을 게시하면 코드를 디버그할 수 있도록 로컬 환경에서 이벤트가 트리거됩니다. URL의 형식은 다음과 같습니다.

```http
http://localhost:7071/runtime/webhooks/EventGrid?functionName={functionname}
```

이 엔드포인트에 보내는 요청은 시뮬레이션된 요청입니다. Azure Storage 계정에서 데이터를 보내거나 받지 않습니다.

이 엔드포인트에 요청을 생성하고 보내려면 다음 단계를 따릅니다. 이 예제에서는 Postman을 사용하여 요청을 보내는 방법을 보여 줍니다.

1. Postman에서 새 요청을 만듭니다.
1. 위에 표시된 URL을 요청 URL 필드에 붙여넣고 `{functionname}`을 함수 이름으로 바꾸고 중괄호를 제거합니다. 요청 동사가 GET으로 설정되어 있는지 확인합니다.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-url.png" alt-text="Postman에서 이벤트 트리거의 로컬 URL을 지정하는 방법을 보여 주는 스크린샷":::

1. **Content-Type** 헤더를 추가하고 *application/json* 으로 설정합니다.
1. **aeg-event-type** 헤더를 추가하고 *Notification* 으로 설정합니다.

    :::image type="content" source="media/archive-rehydrate-handle-event/trigger-azure-function-postman-headers.png" alt-text="이벤트를 트리거하는 로컬 요청에 대한 헤더 구성을 보여 주는 스크린샷":::

1. Postman에서 본문 형식을 *JSON* 으로 설정하고 형식을 *raw* 로 설정하여 요청 본문을 지정합니다. 다음 예에서는 **Blob 복사** 요청을 시뮬레이션합니다. 꺾쇠 괄호로 묶인 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 이는 시뮬레이션된 요청이기 때문에 날짜/시간 또는 식별자 값을 변경할 필요가 없습니다.

    ```json
    [{
      "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>",
      "subject": "/blobServices/default/containers/<container-name>/blobs/<blob-name>",
      "eventType": "Microsoft.Storage.BlobCreated",
      "id": "2bfb587b-501e-0094-2746-8b2884065d32",
      "data": {
        "api": "CopyBlob",
        "clientRequestId": "3d4dedc7-6c27-4816-9405-fdbfa806b00c",
        "requestId": "2bfb587b-501e-0094-2746-8b2884000000",
        "eTag": "0x8D9595DCA505BDF",
        "contentType": "text/plain",
        "contentLength": 48,
        "blobType": "BlockBlob",
        "url": "https://<storage-account>.blob.core.windows.net/<container-name>/<blob-name>",
        "sequencer": "0000000000000000000000000000201B00000000004092a5",
        "storageDiagnostics": {
          "batchId": "8a92736a-6006-0026-0046-8bd7f5000000"
        }
      },
      "dataVersion": "",
      "metadataVersion": "1",
      "eventTime": "2021-08-07T04:42:41.0730463Z"
    }]
    ```

1. Visual Studio에서 원하는 중단점을 코드에 배치하고 **F5** 를 눌러 디버거를 실행합니다.
1. Postman에서 **보내기** 단추를 선택하여 요청을 엔드포인트로 보냅니다.

요청을 보내면 Event Grid에서 Azure Function을 호출하므로 정상적으로 디버그할 수 있습니다. 추가 정보 및 예제는 Azure Functions 문서에서 [수동으로 요청 게시](../../azure-functions/functions-bindings-event-grid-trigger.md#manually-post-the-request)를 참조하세요.

이벤트를 트리거하는 요청은 시뮬레이션되지만 이벤트가 실행될 때 실행되는 Azure Function은 스토리지 계정의 새 Blob에 로그 정보를 씁니다. 다음 이미지에 표시된 대로 Azure Portal에서 Blob의 콘텐츠를 확인하고 마지막으로 수정된 시간을 볼 수 있습니다.

:::image type="content" source="media/archive-rehydrate-handle-event/log-blob-contents-portal.png" alt-text="Azure Portal에서 로그 Blob의 콘텐츠를 보여 주는 스크린샷":::

## <a name="publish-the-azure-function"></a>Azure 함수 게시

Azure Function을 로컬에서 테스트하고 나면 다음 단계로 이전에 만든 Azure Function 앱에 Azure Function을 게시합니다. 스토리지 계정에서 발생하는 이벤트를 함수 엔드포인트로 보내도록 Event Grid를 구성할 수 있도록 함수를 게시해야 합니다.

함수를 게시하려면 다음 단계를 수행합니다.

1. 솔루션 탐색기에서 Azure Functions 프로젝트를 선택 하 고 저장 하거나 마우스 오른쪽 단추로 클릭 하 고 **게시** 를 선택 합니다.
1. **게시** 창에서 대상으로 **Azure** 를 선택한 후 **다음** 을 선택합니다.
1. **Azure Function 앱(Windows)** 을 특정 대상으로 선택한 후 **다음** 을 선택합니다.
1. **Functions 인스턴스** 탭의 드롭다운 메뉴에서 구독을 선택한 다음 사용 가능한 함수 앱 목록에서 Azure Function 앱을 찾습니다.
1. **패키지 파일에서 실행** 확인란이 선택되었는지 확인합니다.
1. **마침** 을 선택하여 함수 게시를 준비합니다.
1. **게시** 페이지에서 구성이 올바른지 확인합니다. Application Insights에 대한 서비스 종속성이 구성되지 않았다는 경고가 표시되면 이 페이지에서 구성할 수 있습니다.
1. **게시** 단추를 선택하여 이전에 만든 Azure Function 앱에 Azure Function 게시를 시작합니다.

    :::image type="content" source="media/archive-rehydrate-handle-event/visual-studio-publish-azure-function.png" alt-text="Visual Studio에서 Azure Function을 게시하는 페이지를 보여 주는 스크린샷":::

Azure Function에서 코드를 변경할 때마다 업데이트된 함수를 Azure에 게시해야 합니다.

## <a name="subscribe-to-blob-rehydration-events-from-a-storage-account"></a>스토리지 계정에서 blob 리하이드레이션 이벤트 구독

이제 이벤트에 대한 응답으로 실행할 수 있는 Azure Function이 포함된 함수 앱이 있습니다. 다음 단계에서는 스토리지 계정에서 이벤트 구독을 만듭니다. 이벤트 구독에서는 스토리지 계정의 Blob에 대한 작업에 응답하여 Event Grid를 통해 이벤트를 게시하도록 스토리지 계정을 구성합니다. 그런 다음 Event Grid는 사용자가 지정한 이벤트 처리기 엔드포인트로 이벤트를 보냅니다. 이 경우 이벤트 처리기는 이전 섹션에서 만든 Azure Function입니다.

이벤트 구독을 만들 때 이벤트 처리기로 전송되는 이벤트를 필터링할 수 있습니다. 보관 계층에서 blob을 리하이드레이션할 때 캡처할 이벤트는 [Blob 계층 설정](/rest/api/storageservices/set-blob-tier) 작업에 해당하는 **Microsoft.Storage.BlobTierChanged** 및 [Blob 복사](/rest/api/storageservices/copy-blob) 또는 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업에 해당하는 **Microsoft.Storage.BlobCreated** 이벤트입니다. 시나리오에 따라 해당 이벤트 중 하나만 처리하는 것이 좋습니다.

이벤트 구독을 만들 때 다음 단계를 수행합니다.

1. Azure Portal의 보관 계층에서 리하이드레이션할 Blob이 포함된 스토리지 계정으로 이동합니다.
1. 왼쪽 탐색 창에서 **이벤트** 설정을 선택합니다.
1. **이벤트** 페이지에서 **추가 옵션** 을 선택합니다.
1. **이벤트 구독 만들기** 를 선택합니다.
1. **이벤트 구독 만들기** 페이지의 **이벤트 구독 세부 정보** 섹션에서 이벤트 구독의 이름을 입력합니다.
1. **토픽 세부 정보** 섹션에서 시스템 토픽의 이름을 제공합니다. 시스템 토픽은 Azure Storage에서 게시한 하나 이상의 이벤트를 나타냅니다. 시스템 토픽에 관한 자세한 내용은 [Azure Event Grid의 시스템 토픽](../../event-grid/system-topics.md)을 참조하세요.
1. **이벤트 유형** 섹션에서 **Blob 작성** 및 **Blob 계층 변경** 이벤트를 선택합니다. 보관 계층에서 Blob을 리하이드레이션하도록 선택하는 방법에 따라 이 두 이벤트 중 하나가 실행됩니다.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-event-types-portal.png" alt-text="Azure Portal에서 Blob 리하이드레이션 이벤트에 대한 이벤트 유형을 선택하는 방법을 보여 주는 스크린샷":::

1. **엔드포인트 세부 정보** 섹션의 드롭다운 메뉴에서 *Azure Function* 을 선택합니다.
1. **엔드포인트 선택** 을 선택하여 이전 섹션에서 만든 함수를 지정합니다. **Azure Function 선택** 대화 상자에서 Azure Function에 대한 구독, 리소스 그룹 및 함수 앱을 선택합니다. 마지막으로 드롭다운에서 함수 이름을 선택하고 **선택 확인** 을 선택합니다.

    :::image type="content" source="media/archive-rehydrate-handle-event/select-azure-function-endpoint-portal.png" alt-text="Azure Function을 Event Grid 구독의 엔드포인트로 선택하는 방법을 보여 주는 스크린샷":::

1. **만들기** 단추를 선택하여 이벤트 구독을 만들고 Azure Function 이벤트 처리기로 이벤트 보내기를 시작합니다.

이벤트 구독에 대한 자세한 내용은 [Azure Event Grid 개념](../../event-grid/concepts.md#event-subscriptions)을 참조하세요.

## <a name="test-the-azure-function-event-handler"></a>Azure Function 이벤트 처리기 테스트

Azure Function을 테스트하기 위해 이벤트 구독이 포함된 스토리지 계정에서 이벤트를 트리거할 수 있습니다. 이전에 만든 이벤트 구독은 **Microsoft.Storage.BlobCreated** 및 **Microsoft.Storage.BlobTierChanged** 라는 두 가지 이벤트를 필터링합니다. 해당 이벤트 중 하나가 발생하면 Azure Function을 트리거합니다.

이 문서에 표시된 Azure Function은 다음 두 가지 시나리오에서 로그 blob에 씁니다.

- 이벤트가 **Microsoft.Storage.BlobCreated** 이고 API 작업이 **Blob 복사** 인 경우.
- 이벤트가 **Microsoft.Storage.BlobTierChanged** 이고 API 작업이 **Blob 계층 설정** 인 경우.

Blob을 리하이드레이션하여 함수를 테스트하는 방법을 알아보려면 다음 두 프로시저 중 하나를 참조하세요.

- [복사 작업을 사용하여 blob 리하이드레이션](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-with-a-copy-operation)
- [계층을 변경하여 blob 리하이드레이션](archive-rehydrate-to-online-tier.md#rehydrate-a-blob-by-changing-its-tier)

리하이드레이션이 완료되면 로그 Blob이 리하이드레이션된 Blob과 동일한 컨테이너에 기록됩니다. 예를 들어 복사 작업으로 Blob을 리하이드레이션하면 Azure Portal에서 원본 Blob이 보관 계층에 남아 있고, 완전히 리하이드레이션된 대상 Blob이 대상 온라인 계층에 표시되며, Azure Function에서 만든 로그 Blob도 목록에 표시되는 것을 알 수 있습니다.

:::image type="content" source="media/archive-rehydrate-handle-event/copy-blob-archive-tier-rehydrated-with-log-blob.png" alt-text="보관 계층의 원래 blob, 핫 계층의 리하이드레이션된 blob, 이벤트 처리기에서 쓴 로그 blob을 보여 주는 스크린샷":::

Blob을 리하이드레이션하는 작업은 리하이드레이션 우선 순위 설정에 따라 최대 15시간이 걸릴 수 있습니다. 리하이드레이션 우선 순위를 **높음** 으로 설정하면 크기가 10GB 미만인 Blob의 리하이드레이션이 1시간 이내에 완료될 수 있습니다. 그러나 우선 순위가 높은 리하이드레이션은 리소스가 더 많이 듭니다. 자세한 내용은 [보관 계층의 Blob 리하이드레이션 개요](archive-rehydrate-overview.md)를 참조하세요.

> [!TIP]
> 이 방법을 사용하는 이유는 Blob 리하이드레이션의 맥락에서 해당 이벤트를 처리하기 위해서이지만, 테스트용으로는 blob 업로드 또는 온라인 blob의 계층(‘예:’ 핫에서 쿨로)을 변경할 때 해당 이벤트를 관찰하는 것이 도움이 됩니다. 이 이벤트를 즉시 실행되기 때문입니다.

Event Grid에서 이벤트를 필터링하는 방법에 관한 자세한 내용은 [Azure Event Grid에 대한 이벤트를 필터링하는 방법](../../event-grid/how-to-filter-events.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

- [Azure Blob Storage의 액세스 계층 - 핫, 쿨 및 보관](storage-blob-storage-tiers.md)
- [보관 계층의 Blob 리하이드레이션 개요](archive-rehydrate-overview.md)
- [보관된 Blob을 온라인 계층으로 리하이드레이션](archive-rehydrate-to-online-tier.md)
- [Blob Storage 이벤트에 응답](storage-blob-event-overview.md)
