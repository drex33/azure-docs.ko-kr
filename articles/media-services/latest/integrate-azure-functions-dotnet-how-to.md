---
title: Media Services v3를 통해 Azure Functions 개발
description: 이 문서에서는 Visual Studio Code 사용하여 Media Services v3에서 Azure Functions 개발을 시작하는 방법을 보여줍니다.
services: media-services
author: xpouyat
ms.service: media-services
ms.workload: media
ms.devlang: dotnet
ms.topic: article
ms.date: 06/09/2021
ms.author: xpouyat
ms.custom: devx-track-csharp
ms.openlocfilehash: 523a5dbfb503f47f15e44e7be1b61bf6cf05c471
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661719"
---
# <a name="develop-azure-functions-with-media-services-v3"></a>Media Services v3를 통해 Azure Functions 개발

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에서는 Media Services를 사용하여 Azure Functions 만들기를 시작하는 방법을 보여 줍니다. 이 문서에 정의된 Azure 함수는 Media Encoder Standard 비디오 파일을 인코딩합니다. 인코딩 작업이 만들어지는 즉시 함수는 작업 이름과 출력 자산 이름을 반환합니다. Azure Functions 검토하려면 Azure Functions **섹션의** [개요](../../azure-functions/functions-overview.md) 및 기타 항목을 참조하세요.

Azure Media Services를 사용하는 기존 Azure Functions를 탐색하고 배포하려는 경우 [Media Services Azure Functions](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration)를 확인하세요. 이 리포지토리에는 Media Services 사용하여 Blob Storage, 인코딩 및 라이브 스트리밍 작업에서 직접 콘텐츠를 포함하는 워크플로를 표시하는 예제가 포함되어 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 첫 번째 함수를 만들기 전에 활성 Azure 계정이 있어야 합니다. Azure 계정이 아직 없는 경우 [체험 계정을 사용](https://azure.microsoft.com/free/)할 수 있습니다.
- AMS(Azure Media Services) 계정에서 작업을 수행하거나 Media Services에서 보낸 이벤트를 수신 대기하는 Azure Functions를 만들려는 경우 [여기](account-create-how-to.md)에 설명한 대로 AMS 계정을 만들어야 합니다.
- [지원되는 플랫폼](https://code.visualstudio.com/docs/supporting/requirements#_platforms) 중 하나에 [Visual Studio Code](https://code.visualstudio.com/)를 설치합니다.

이 문서에서는 Azure Media Services 통신하는 C# .NET 5 함수를 만드는 방법을 설명합니다. 다른 언어로 함수를 만들려면 이 [문서를](../../azure-functions/functions-develop-vs-code.md)찾습니다.

### <a name="run-local-requirements"></a>로컬 요구 사항 실행

이러한 필수 조건은 로컬에서 함수를 실행 및 디버그하는 경우에만 필요합니다. Azure Functions에 대한 프로젝트를 만들거나 게시하지 않아도 됩니다.

- [.NET Core 3.1 및 .NET 5 SDK.](https://dotnet.microsoft.com/download/dotnet)

- [Azure Functions Core Tools](../../azure-functions/functions-run-local.md#install-the-azure-functions-core-tools) 버전 3.x 이상입니다. 프로젝트를 로컬로 시작하면 Core Tools 패키지가 자동으로 다운로드되어 설치됩니다. Core Tools는 전체 Azure Functions 런타임을 포함하므로 다운로드 및 설치에 시간이 걸릴 수 있습니다.

- Visual Studio Code용 [C# 확장](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).

## <a name="install-the-azure-functions-extension"></a>Azure Functions 확장 설치

Azure Functions 확장을 사용하여 함수를 생성 및 테스트하고 Azure에 배포할 수 있습니다.

1. Visual Studio Code **확장을** 열고 **Azure Functions를** 검색하거나 Visual Studio Code 링크를 [`vscode:extension/ms-azuretools.vscode-azurefunctions`](vscode:extension/ms-azuretools.vscode-azurefunctions) 선택합니다.

1. **설치** 를 선택하여 Visual Studio Code에 대한 확장을 설치합니다.

    ![Azure Functions에 대한 확장 설치](./Media/integrate-azure-functions-dotnet-how-to/vscode-install-extension.png)

1. 설치 후 작업 표시줄에서 Azure 아이콘을 선택합니다. 사이드바에 Azure Functions 영역이 표시됩니다.

    ![사이드바에 있는 Azure Functions 영역](./Media/integrate-azure-functions-dotnet-how-to/azure-functions-window-vscode.png)

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

Functions 확장을 사용하면 첫 번째 함수와 함께 함수 앱 프로젝트를 만들 수 있습니다. 다음 단계에서는 새 Functions 프로젝트에서 HTTP 트리거 함수를 만드는 방법을 보여 줍니다. HTTP 트리거는 시연할 가장 간단한 함수 트리거 템플릿입니다.

1. **Azure: Functions** 에서 **함수 만들기** 아이콘을 선택합니다.

    ![함수 만들기](./Media/integrate-azure-functions-dotnet-how-to/create-function.png)

1. 함수 앱 프로젝트의 폴더를 선택한 다음, **함수 프로젝트에 C#을 선택하고 런타임에** **.NET 5 격리를** 선택합니다.

1. HTTP **트리거** 함수 템플릿을 선택합니다.

    ![HTTP 트리거 템플릿 선택](./Media/integrate-azure-functions-dotnet-how-to/create-function-choose-template.png)

1. 함수 이름에 **HttpTriggerEncode를** 입력하고 Enter 키를 선택하고 네임스페이스에 **대해 Company.Function을** 수락한 다음, 액세스 권한으로 **함수를** 선택합니다. 이 권한 부여 수준에서는 함수 엔드포인트를 호출할 때 [함수 키](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)를 제공해야 합니다.

    ![함수 권한 부여 선택](./Media/integrate-azure-functions-dotnet-how-to/create-function-auth.png)

    HTTP 트리거 함수에 대한 템플릿을 사용하여 선택한 언어로 함수가 만들어집니다.

    ![Visual Studio Code에서 HTTP 트리거 함수 템플릿](./Media/integrate-azure-functions-dotnet-how-to/new-function-full.png)

## <a name="install-media-services-and-other-extensions"></a>Media Services 및 기타 확장 설치

터미널 창에서 dotnet add package 명령을 실행하여 프로젝트에 필요한 확장 패키지를 설치합니다. 다음 명령은 샘플에 필요한 Media Services 패키지 및 기타 확장을 설치합니다.

```bash
dotnet add package Azure.Storage.Blobs
dotnet add package Microsoft.Azure.Management.Media
dotnet add package Azure.Identity
```

## <a name="generated-project-files"></a>생성된 프로젝트 파일

프로젝트 템플릿은 선택한 언어로 프로젝트를 만들고 필요한 종속성을 설치합니다. 새 프로젝트에는 다음과 같은 파일이 있습니다.

* **host.json**: 함수 호스트를 구성할 수 있습니다. 이러한 설정은 로컬에서 함수를 실행하는 경우와 Azure에서 함수를 실행하는 경우에 적용됩니다. 자세한 내용은 [host.json 참조](./../../azure-functions/functions-host-json.md)를 참조하세요.

* **local.settings.json**: 함수를 로컬로 실행할 때 사용되는 설정을 유지합니다. 이러한 설정은 함수를 로컬로 실행하는 경우에만 사용됩니다.

    >[!IMPORTANT]
    >local.settings.json 파일에 암호가 있을 수 있으므로 프로젝트 원본 제어에서 해당 파일을 제외해야 합니다.

* 함수를 구현하는 **HttpTriggerEncode.cs** 클래스 파일입니다.

### <a name="httptriggerencodecs"></a>HttpTriggerEncode.cs

함수에 대한 C# 코드입니다. 해당 역할은 Media Services 자산 또는 원본 URL을 사용하여 Media Services 인코딩 작업을 시작하는 것입니다. 존재하지 않는 경우 생성되는 변환을 사용합니다. 만들 때 입력 본문에 제공된 사전 설정을 사용했습니다. 

>[!IMPORTANT]
>HttpTriggerEncode.cs 파일의 전체 콘텐츠를 [ `HttpTriggerEncode.cs` 이 리포지토리 의 로](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/blob/main/Tutorial/HttpTriggerEncode.cs)바꿉니다.

함수 정의가 완료되면 **저장 후 실행을** 선택합니다.

함수의 **Run** 메서드에 대한 소스 코드는 다음과 같습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet-core-functions-integration/Tutorial/HttpTriggerEncode.cs#Run)]

### <a name="localsettingsjson"></a>local.settings.json

파일을 다음 콘텐츠로 업데이트하고 값을 대체합니다.

```json
{
  "IsEncrypted": false,
  "Values": {
    "AzureWebJobsStorage": "",
    "FUNCTIONS_WORKER_RUNTIME": "dotnet-isolated",
    "AadClientId": "00000000-0000-0000-0000-000000000000",
    "AadEndpoint": "https://login.microsoftonline.com",
    "AadSecret": "00000000-0000-0000-0000-000000000000",
    "AadTenantId": "00000000-0000-0000-0000-000000000000",
    "AccountName": "amsaccount",
    "ArmAadAudience": "https://management.core.windows.net/",
    "ArmEndpoint": "https://management.azure.com/",
    "ResourceGroup": "amsResourceGroup",
    "SubscriptionId": "00000000-0000-0000-0000-000000000000"
  }
}
```

## <a name="test-your-function"></a>함수 테스트

VS Code 로컬로 함수를 실행하는 경우 함수는 다음과 같이 노출되어야 합니다. 

```url
http://localhost:7071/api/HttpTriggerEncode
```

테스트하려면 Postman을 사용하여 JSON 입력 본문을 사용하여 이 URL에서 POST를 수행할 수 있습니다.

JSON 입력 본문 예제:

```json
{
    "inputUrl":"https://nimbuscdn-nimbuspm.streaming.mediaservices.windows.net/2b533311-b215-4409-80af-529c3e853622/Ignite-short.mp4",
    "transformName" : "TransformAS",
    "builtInPreset" :"AdaptiveStreaming"
 }
```

함수는 작업 및 출력 자산 이름을 포함하는 출력 본문과 함께 200 OK를 반환해야 합니다.

![Postman을 통해 함수 테스트](./Media/integrate-azure-functions-dotnet-how-to/postman.png)

## <a name="next-steps"></a>다음 단계

이 시점에서 Media Services API를 호출하는 함수 개발을 시작할 준비가 된 것입니다.

자세한 내용과 Azure Media Services v3에서 Azure Functions 사용하는 전체 샘플은 Media Services [v3 Azure Functions 샘플을 참조하세요.](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)
