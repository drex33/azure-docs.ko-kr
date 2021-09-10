---
title: .NET 5.0을 사용하여 Media Services로 라이브 스트리밍
titleSuffix: Azure Media Services
description: .NET 5.0을 사용하여 라이브 이벤트를 스트리밍하는 방법을 알아봅니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc, devx-track-csharp
ms.date: 06/13/2019
ms.author: inhenkel
ms.openlocfilehash: f10ef55a44aa917fd8f0fb3783dcd29284512d7e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732669"
---
# <a name="tutorial-stream-live-with-media-services-by-using-net-50"></a>자습서: .NET 5.0을 사용하여 Media Services로 라이브 스트리밍

Azure Media Services에서 [라이브 이벤트](/rest/api/media/liveevents)는 라이브 스트리밍 콘텐츠 처리를 담당합니다. 라이브 이벤트는 라이브 인코더에 제공할 입력 엔드포인트(수집 URL)를 제공합니다. 라이브 이벤트는 라이브 인코더로부터 입력 스트림을 수신하여 하나 이상의 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)를 통해 스트리밍할 수 있도록 합니다. 또한 라이브 이벤트는 미리 보기 엔드포인트(미리 보기 URL)도 제공하며, 사용자는 이를 통해 추가적인 처리 및 전달 전에 스트림을 미리 보고 유효성을 검사할 수 있습니다. 

이 자습서에는 .NET 5.0을 사용하여 ‘통과’ 형식의 라이브 이벤트를 만드는 방법을 보여 줍니다. 이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 샘플 앱을 다운로드합니다.
> * 라이브 스트리밍을 수행하는 코드 검사
> * [Media Player 데모 사이트](https://ampdemo.azureedge.net)에서 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)로 이벤트 시청.
> * 리소스를 정리합니다.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 이 자습서에서 [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.liveevent) 예제를 사용하지만, 일반적인 단계는 [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event) 또는 지원되는 기타 [SDK](media-services-apis-overview.md#sdks)와 동일합니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

- [Windows/macOS/Linux용 Visual Studio Code](https://code.visualstudio.com/)를 설치하거나 [Windows 또는 Mac용 Visual Studio 2019](https://visualstudio.microsoft.com/)를 설치합니다.
- [.NET 5.0 SDK](https://dotnet.microsoft.com/download)를 설치합니다.
- [Media Services 계정 만들기](./account-create-how-to.md) **API 액세스** 세부 정보를 JSON 형식으로 복사하거나 Media Services 계정에 연결하는 데 필요한 값을 이 샘플에 사용된 *.env* 파일 형식으로 저장해야 합니다.
- [Azure CLI를 사용하여 Azure Media Services API에 액세스](./access-api-howto.md)의 단계를 따르고 자격 증명을 저장합니다. 이 샘플에서 API에 액세스하려면 이를 사용하거나 *.env* 파일 형식으로 입력해야 합니다. 

라이브 스트리밍 소프트웨어에는 다음 항목이 추가로 필요합니다.

- 브로드캐스트 또는 이벤트에 사용되는 카메라 또는 디바이스(예: 랩톱).
- 카메라 스트림을 인코딩하고 RTMP(실시간 메시징 프로토콜)를 통해 Media Services 라이브 스트리밍 서비스로 보내는 온-프레미스 소프트웨어 인코더. 자세한 내용은 [권장 온-프레미스 라이브 인코더](encode-recommended-on-premises-live-encoders.md)를 참조하세요. 스트림은 RTMP 또는 부드러운 스트리밍 형식이어야 합니다.  

  이 샘플에서는 OBS(Open Broadcaster Software) Studio를 사용하여 RTMP를 수집 엔드포인트로 브로드캐스트한다고 가정합니다. [OBS Studio를 설치](https://obsproject.com/download)합니다. 

> [!TIP]
> 계속 진행하기 전에 [Media Services v3를 통한 라이브 스트리밍](stream-live-streaming-concept.md)을 검토하세요. 

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 라이브 스트리밍 .NET 샘플이 포함된 GitHub 리포지토리를 머신에 복제합니다.  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-dotnet.git
```

라이브 스트리밍 샘플은 [Live](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live) 폴더에 있습니다.

[!INCLUDE [appsettings or .env file](./includes/note-appsettings-or-env-file.md)]

> [!IMPORTANT]
> 이 샘플에서는 각 리소스에 고유한 접미사를 사용합니다. 디버깅을 취소하거나 앱을 실행하지 않고 종료하면 계정에서 여러 라이브 이벤트가 발생합니다.
>
> 실행 중인 라이브 이벤트를 중지해야 합니다. 그렇지 않으면 *비용이 청구* 됩니다.

## <a name="examine-the-code-that-performs-live-streaming"></a>라이브 스트리밍을 수행하는 코드 검사

이 섹션에서는 *LiveEventWithDVR* 프로젝트의 [Authentication.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Common_Utils/Authentication.cs) 파일(Common_Utils 폴더에 있는) 및 [Program.cs](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/Live/LiveEventWithDVR/Program.cs) 파일에 정의된 함수를 살펴봅니다.

샘플을 정리하지 않고 여러 번 실행하더라도 이름이 충돌하지 않도록, 이 샘플에서는 각 리소스의 고유 접미사를 만듭니다.


### <a name="start-using-media-services-apis-with-the-net-sdk"></a>.NET SDK로 Media Services API 사용 시작

Authentication.cs는 로컬 구성 파일(appsettings.json 또는 .env)에 제공된 자격 증명을 사용하여 `AzureMediaServicesClient` 개체를 만듭니다.

`AzureMediaServicesClient` 개체를 사용하면 .NET에서 Media Services API 사용을 시작할 수 있습니다. 개체를 만들려면 `GetCredentailsAsync`에서 구현된 Azure Active Directory를 사용하여 클라이언트가 Azure에 연결할 수 있는 자격 증명을 제공해야 합니다. 또 다른 옵션은 `GetCredentialsInteractiveAuthAsync`에서 구현된 대화형 인증을 사용하는 것입니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#CreateMediaServicesClientAsync)]

문서 시작 부분에서 복제한 코드에 있는 `GetCredentialsAsync` 함수는 로컬 구성 파일(*appsettings.json)* 에 제공된 자격 증명을 기반으로 또는 리포지토리의 루트에 있는 *.env* 환경 변수 파일을 통해 `ServiceClientCredentials` 개체를 만듭니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsAsync)]

대화형 인증의 경우 `GetCredentialsInteractiveAuthAsync` 함수는 대화형 인증 및 로컬 구성 파일(*appsettings.json*)에 제공된 연결 매개 변수를 기반으로 또는 리포지토리의 루트에 있는 *.env* 환경 변수 파일을 통해 `ServiceClientCredentials` 개체를 만듭니다. 이 경우 구성 또는 환경 변수 파일에 AADCLIENTID 및 AADSECRET가 필요하지 않습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Common_Utils/Authentication.cs#GetCredentialsInteractiveAuthAsync)]


### <a name="create-a-live-event"></a>라이브 이벤트 만들기

이 섹션에서는 라이브 이벤트의 *통과* 형식을 만드는 방법을 보여줍니다(`LiveEventEncodingType`을 `None`으로 설정). 사용할 수 있는 유형에 대한 정보는 [라이브 이벤트 유형](live-event-outputs-concept.md#live-event-types)을 참조하세요. 통과 외에도 720p 또는 1080p 적응형 비트 전송률 클라우드 인코딩용 라이브 코드 변환 이벤트를 사용할 수 있습니다.
 
라이브 이벤트를 만들 때 다음 항목을 지정할 수 있습니다.

* **라이브 이벤트에 대한 수집 프로토콜**. 현재 RTMP, RTMPS 및 부드러운 스트리밍 프로토콜이 지원됩니다. 라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜이 필요한 경우 각 스트리밍 프로토콜에 대해 별도의 라이브 이벤트를 만듭니다. 
* **수집 및 미리 보기에 대한 IP 제한**. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 다음 옵션 중 하나로 지정할 수 있습니다.

  * 단일 IP 주소(예: `10.0.0.1`)
  * IP 주소 및 CIDR(Classless Inter-Domain Routing) 서브넷 마스크를 사용하는 IP 범위(예: `10.0.0.1/22`)
  * IP 주소와 점으로 구분된 10진수 서브넷 마스크를 사용하는 IP 범위(예: `10.0.0.1(255.255.252.0)`)

  지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 `0.0.0.0/0`으로 설정합니다. IP 주소는 4개의 숫자를 사용하는 IPv4 주소 또는 CIDR 주소 범위 형식 중 하나여야 합니다.  
* **이벤트를 만들 때 자동으로 시작됩니다**. 자동 시작을 `true`로 설정하면 라이브 이벤트가 생성된 후 시작됩니다. 즉, 라이브 이벤트를 실행하는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 `Stop`을 호출해야 합니다. 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing-concept.md)를 참조하세요.

  대기 모드는 실행 상태로 더 빠르게 전환할 수 있게 만들어주는 저렴한 '할당됨' 상태에서 라이브 이벤트를 시작하는 데 사용됩니다. 이는 스트리머에 채널을 빠르게 전달해야 하는 핫 풀과 같은 상황에서 유용합니다.
* **정적 호스트 이름 및 고유 GUID**. 수집 URL을 하드웨어 기반 라이브 인코더에서 예측할 수 있고 쉽게 유지 관리할 수 있도록 `useStaticHostname` 속성을 `true`로 설정합니다. 자세한 내용은 [라이브 이벤트 수집 URL](live-event-outputs-concept.md#live-event-ingest-urls)을 참조하세요.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveEvent)]

### <a name="get-ingest-urls"></a>수집 URL 가져오기

라이브 이벤트가 생성되면 라이브 인코더에 제공할 수집 URL을 가져올 수 있습니다. 인코더는 이러한 URL을 사용하여 라이브 스트림을 입력합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetIngestURL)]

### <a name="get-the-preview-url"></a>미리 보기 URL 가져오기

`previewEndpoint`를 사용하여 인코더의 입력이 수신되고 있는지 미리 보고 확인합니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#GetPreviewURLs)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>라이브 이벤트 및 라이브 출력 만들기 및 관리

라이브 이벤트로 들어오는 스트림이 있으면 자산, 라이브 출력 및 스트리밍 로케이터를 만들어 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트림이 보관되고 스트리밍 엔드포인트를 통해 시청자가 스트림을 사용할 수 있게 됩니다.

이러한 개념을 학습할 때는 자산 개체를 과거 비디오 테이프 레코더에 넣는 테이프로 생각하면 도움이 됩니다. 라이브 출력은 테이프 레코더 머신에 해당합니다. 라이브 이벤트는 머신 뒤로 들어오는 비디오 신호에 불과합니다.

먼저 라이브 이벤트를 만들어 신호를 만듭니다. 해당 라이브 이벤트를 시작하고 인코더를 입력에 연결할 때까지 신호는 이동하지 않습니다.

"테이프"는 언제든지 만들 수 있습니다. 이 비유에서 "테이프 레코더"인 라이브 출력 개체로 전달하는 것은 빈 ‘자산’에 불과합니다.

"테이프 레코더"는 언제든지 만들 수도 있습니다. 신호 흐름을 시작하기 전이나 후에 라이브 출력을 만들 수 있습니다. 속도를 높여야 하는 경우 신호 흐름을 시작하기 전에 출력을 만드는 것이 도움이 되는 경우가 있습니다.

"테이프 레코더"를 중지하려면 `LiveOutput`에서 `delete`를 호출합니다. 이 작업은 "테이프"(자산)의 *콘텐츠* 를 삭제하지 않습니다. 자산은 자산 자체에서 명시적으로 `delete`를 호출할 때까지 보관된 비디오 콘텐츠로 항상 유지됩니다. 

다음 섹션에서는 자산 및 라이브 출력을 만드는 과정을 설명합니다.

#### <a name="create-an-asset"></a>자산 만들기

라이브 출력에서 사용할 자산을 만듭니다. 이러한 비유에서 자산은 라이브 비디오 신호를 기록하는 "테이프"에 해당합니다. 뷰어는 이 가상 테이프에서 라이브 또는 요청 시 콘텐츠를 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateAsset)]

#### <a name="create-a-live-output"></a>라이브 출력 만들기

라이브 출력은 생성되면 시작되고 삭제되면 중지됩니다.  라이브 출력을 삭제해도 기본 자산 또는 자산의 콘텐츠는 삭제되지 않습니다. "테이프"를 꺼내는 것이라 생각해 보세요. 녹화가 포함된 자산은 원하는 만큼 지속됩니다. 이를 꺼낼 때(즉, 라이브 출력이 삭제될 때) 요청 시 즉시 볼 수 있게 됩니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateLiveOutput)]

#### <a name="create-a-streaming-locator"></a>스트리밍 로케이터 만들기

> [!NOTE]
> Media Services 계정이 생성되면 기본 스트리밍 엔드포인트가 중지됨 상태로 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 [동적 패키징](encode-dynamic-packaging-concept.md) 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 실행 상태에 있어야 합니다.

스트리밍 로케이터를 사용해 자산을 게시한 경우, 스트리밍 로케이터가 만료 또는 삭제되는 시점 중 먼저 도래하는 시점까지 라이브 이벤트(최대 DVR 기간 길이)를 계속 볼 수 있습니다. 이는 보기 대상 사용자가 라이브로 또는 요청 시 가상 ‘테이프’ 레코딩을 볼 수 있는 방법입니다. 레코딩이 완료(라이브 출력이 삭제될 때)되면 동일한 URL을 사용하여 라이브 이벤트, DVR 창 또는 주문형 자산을 볼 수 있습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CreateStreamingLocator)]

```csharp

// Get the URL to stream the output
ListPathsResponse paths = await client.StreamingLocators.ListPathsAsync(resourceGroupName, accountName, locatorName);

foreach (StreamingPath path in paths.StreamingPaths)
{
    UriBuilder uriBuilder = new UriBuilder();
    uriBuilder.Scheme = "https";
    uriBuilder.Host = streamingEndpoint.HostName;

    uriBuilder.Path = path.Paths[0];
    // Get the URL from the uriBuilder: uriBuilder.ToString()
}
```

### <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

스트리밍 이벤트를 완료하고 이전에 프로비전된 리소스를 정리하려면 다음 절차를 따릅니다.

1. 인코더에서 스트림의 푸시를 중지합니다.
1. 라이브 이벤트를 중지합니다. 라이브 이벤트가 중지된 후에는 요금이 발생하지 않습니다. 채널을 다시 시작해야 하는 경우 채널의 수집 URL은 동일하므로 인코더를 다시 구성하지 않아도 됩니다.
1. 라이브 이벤트의 보관 파일을 주문형 스트림으로 계속 제공하지 않으려면 스트리밍 엔드포인트를 중지합니다. 라이브 이벤트가 중지됨 상태이면 요금이 발생하지 않습니다.

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLiveEventAndOutput)]

[!code-csharp[Main](../../../media-services-v3-dotnet/Live/LiveEventWithDVR/Program.cs#CleanupLocatorAssetAndStreamingEndpoint)]

## <a name="watch-the-event"></a>이벤트 보기

**Ctrl+F5** 를 눌러 코드를 실행합니다. 그러면 라이브 이벤트를 시청하는 데 사용할 수 있는 스트리밍 URL이 출력됩니다. 스트리밍 로케이터를 만들기 위해 가져온 스트리밍 URL을 복사합니다. 원하는 미디어 플레이어를 사용할 수 있습니다. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)는 [Media Player 데모 사이트](https://ampdemo.azureedge.net)에서 스트림을 테스트하는 데 사용할 수 있습니다.

라이브 이벤트가 중지되면 이벤트가 자동으로 주문형 콘텐츠로 변환됩니다. 이벤트를 중지하고 삭제한 후에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트리밍할 수 있습니다. 이벤트에서 사용 중인 자산은 삭제할 수 없습니다. 이벤트를 먼저 삭제해야 합니다.

## <a name="clean-up-remaining-resources"></a>나머지 리소스 정리

이 자습서에서 만든 Media Services 및 스토리지 계정을 포함하여 리소스 그룹의 리소스가 더 이상 필요하지 않으면 이전에 만든 리소스 그룹을 삭제합니다.

다음 CLI 명령을 실행합니다.

```azurecli-interactive
az group delete --name amsResourceGroup
```

> [!IMPORTANT]
> 라이브 이벤트가 계속 실행되도록 두면 비용이 청구됩니다. 프로젝트 또는 프로그램이 응답을 중지하거나 어떤 이유로든 종료되면 라이브 이벤트가 청구 상태로 계속 실행될 수 있습니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md)
 
