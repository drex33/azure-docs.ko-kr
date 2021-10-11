---
title: Node.js 및 TypeScript를 사용하여 Media Services로 라이브 스트리밍
titleSuffix: Azure Media Services
description: Node.js, TypeScript 및 OBS Studio를 사용하여 라이브 이벤트를 스트리밍하는 방법을 알아봅니다.
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
ms.custom: mvc, devx-track-nodejs
ms.date: 04/15/2021
ms.author: inhenkel
ms.openlocfilehash: 2b553548111c732ca778c439232e949b0abb4707
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/02/2021
ms.locfileid: "129388606"
---
# <a name="tutorial-stream-live-with-media-services-by-using-nodejs-and-typescript"></a>자습서: Node.js 및 TypeScript를 사용하여 Media Services로 라이브 스트리밍

Azure Media Services에서 [라이브 이벤트](/rest/api/media/liveevents)는 라이브 스트리밍 콘텐츠 처리를 담당합니다. 라이브 이벤트는 라이브 인코더에 제공할 입력 엔드포인트(수집 URL)를 제공합니다. 라이브 이벤트는 라이브 인코더로부터 입력 스트림을 수신하여 하나 이상의 [스트리밍 엔드포인트](/rest/api/media/streamingendpoints)를 통해 스트리밍할 수 있도록 합니다. 또한 라이브 이벤트는 미리 보기 엔드포인트(미리 보기 URL)도 제공하며, 사용자는 이를 통해 추가적인 처리 및 전달 전에 스트림을 미리 보고 유효성을 검사할 수 있습니다. 

이 자습서에서는 Node.js와 TypeScript를 사용하여 *통과* 유형의 라이브 이벤트를 만들고 [OBS Studio](https://obsproject.com/download)를 사용하여 라이브 스트림을 브로드캐스트하는 방법을 보여줍니다.

이 자습서에서는 다음을 수행합니다.

> [!div class="checklist"]
> * 샘플 코드 다운로드
> * 라이브 스트리밍을 구성하고 수행하는 코드 검사
> * [Media Player 데모 사이트](https://ampdemo.azureedge.net)에서 [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)로 이벤트 시청.
> * 리소스를 정리합니다.


[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

> [!NOTE]
> 이 자습서에서 Node.js 예제를 사용하지만, 일반적인 단계는 [REST API](/rest/api/media/liveevents), [CLI](/cli/azure/ams/live-event) 또는 지원되는 기타 [SDK](media-services-apis-overview.md#sdks)와 동일합니다. 

## <a name="prerequisites"></a>사전 요구 사항

이 자습서를 완료하려면 다음 항목이 필요합니다.

- [Node.js](https://nodejs.org/en/download/)를 설치합니다.
- [TypeScript](https://www.typescriptlang.org/)를 설치합니다.
- [Media Services 계정 만들기](./create-account-howto.md) 리소스 그룹 이름 및 Media Services 계정 이름에 사용한 값을 기억해둡니다.
- [Azure CLI를 사용하여 Azure Media Services API에 액세스](./access-api-howto.md)의 단계를 따르고 자격 증명을 저장합니다. 이 자격 증명은 API에 액세스하고 환경 변수 파일을 구성하는 데 필요합니다.
- [Node.js 구성 및 연결](./configure-connect-nodejs-howto.md) 문서의 단계별 안내를 통해 Node.js 클라이언트 SDK 사용 방법을 숙지합니다.
- Visual Studio Code 또는 Visual Studio 설치
- TypeScript 언어를 지원하도록 [Visual Studio Code 환경을 설정](https://code.visualstudio.com/Docs/languages/typescript)합니다.

라이브 스트리밍 소프트웨어에는 다음 항목이 추가로 필요합니다.

- 브로드캐스트 또는 이벤트에 사용되는 카메라 또는 디바이스(예: 랩톱).
- 카메라 스트림을 인코딩하고 RTMP(실시간 메시징 프로토콜)를 통해 Media Services 라이브 스트리밍 서비스로 보내는 온-프레미스 소프트웨어 인코더. 자세한 내용은 [권장 온-프레미스 라이브 인코더](encode-recommended-on-premises-live-encoders.md)를 참조하세요. 스트림은 RTMP 또는 부드러운 스트리밍 형식이어야 합니다.

  이 샘플에서는 OBS(Open Broadcaster Software) Studio를 사용하여 RTMP를 수집 엔드포인트로 브로드캐스트한다고 가정합니다. [OBS Studio를 설치](https://obsproject.com/download)합니다. 

  OBS Studio에서 다음 인코딩 설정을 사용합니다.

  - 인코더: NVIDIA NVENC(사용 가능한 경우) 또는 x264
  - 전송률 제어: CBR
  - 비트 전송률: 2,500Kbps(또는 컴퓨터에 적합한 값)
  - 키 프레임 간격: 2초 또는 짧은 대기 시간의 경우 1초  
  - 사전 설정: 낮은 지연 시간 또는 성능(NVENC) 또는 x264를 사용하는 "veryfast"
  - 프로필: 높음
  - GPU: 0(자동)
  - 최대 B 프레임: 2

> [!TIP]
> 계속 진행하기 전에 [Media Services v3를 통한 라이브 스트리밍](stream-live-streaming-concept.md)을 검토하세요.

## <a name="download-and-configure-the-sample"></a>샘플 다운로드 및 구성

다음 명령을 사용하여 라이브 스트리밍 Node.js 샘플이 포함된 GitHub 리포지토리를 컴퓨터에 복제합니다.  

```bash
git clone https://github.com/Azure-Samples/media-services-v3-node-tutorials.git
```

라이브 스트리밍 샘플은 [Live](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live) 폴더에 있습니다.

[AMSv3Samples](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples) 폴더에서 *sample.env* 라는 파일을 *.env* 라는 새 파일에 복사하고 [Azure CLI를 사용하여 Azure Media Services API 액세스](./access-api-howto.md) 문서에서 수집한 환경 변수 설정에 저장합니다.
코드 샘플에서 올바르게 작동할 수 있도록 파일 이름에서 "env" 앞에 점(.)이 포함되어 있는지 확인합니다.

[.env 파일](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/sample.env)에는 Azure AD(Azure Active Directory) 애플리케이션 키 및 비밀이 포함됩니다. 또한 Media Services 계정에 대한 SDK 액세스 권한을 인증하는 데 필요한 계정 이름과 구독 정보도 포함됩니다. *.gitignore* 파일은 포크 처리된 리포지토리에 이 파일이 게시되지 않도록 이미 구성되어 있습니다. 이러한 자격 증명은 계정의 중요한 비밀이므로 유출되지 않도록 주의하십시오.

> [!IMPORTANT]
> 이 샘플에서는 각 리소스에 고유한 접미사를 사용합니다. 디버깅을 취소하거나 앱을 실행하지 않고 종료하면 계정에서 여러 라이브 이벤트가 발생합니다. 
>
> 실행 중인 라이브 이벤트를 중지해야 합니다. 그렇지 않으면 *비용이 청구* 됩니다. 프로그램을 끝까지 실행하여 리소스를 자동으로 정리합니다. 프로그램이 중지되거나 실수로 디버거를 중지하고 프로그램 실행을 중단한 경우 포털을 다시 확인하여 원하지 않는 청구 비용이 발생할 수 있는 실행 중 또는 대기 상태의 라이브 이벤트가 남아있지 않은지 확인해야 합니다.

## <a name="examine-the-typescript-code-for-live-streaming"></a>라이브 스트리밍을 위한 TypeScript 코드 조사

이 섹션에서는 *Live* 프로젝트의 [index.ts](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/Live/index.ts) 파일에 정의된 함수를 살펴봅니다.

샘플을 정리하지 않고 여러 번 실행하더라도 이름이 충돌하지 않도록, 이 샘플에서는 각 리소스의 고유 접미사를 만듭니다.

### <a name="start-using-the-media-services-sdk-for-nodejs-with-typescript"></a>TypeScript로 Node.js용 Media Services SDK 사용 시작

Node.js로 Media Services API를 사용하려면 먼저 npm 패키지 관리자를 사용하여 [@azure/arm-mediaservices](https://www.npmjs.com/package/@azure/arm-mediaservices) SDK 모듈을 추가해야 합니다.

```bash
npm install @azure/arm-mediaservices
```

*package.json* 파일은 이미 구성되어 있습니다. 모듈 및 종속성을 로드하려면 `npm install`을 실행하기만 하면 됩니다.

1. 명령 프롬프트를 열고 샘플의 디렉터리를 찾습니다.
1. *AMSv3Samples* 폴더로 디렉터리를 변경합니다.

    ```bash
    cd AMSv3Samples
    ```

1. *packages.json* 파일에서 사용되는 패키지를 설치합니다.

    ```bash
    npm install 
    ```

1. *AMSv3Samples* 폴더에서 Visual Studio Code를 엽니다. 이 작업은 *.vscode* 폴더와 *tsconfig.json* 파일이 위치한 곳에서 시작하는 데 필요합니다.

    ```bash
    cd ..
    code .
    ```

*Live* 폴더를 열고, Visual Studio Code 편집기에서 *index.ts* 파일을 엽니다.

*index.ts* 파일에 있는 동안 F5 키를 선택하여 디버거를 엽니다.

### <a name="create-the-media-services-client"></a>Media Services 클라이언트 만들기

다음 코드 조각은 Node.js에서 미디어 서비스 클라이언트를 만드는 방법을 보여줍니다.

이 코드에서는 `AzureMediaServicesOptions`의 `longRunningOperationRetryTimeout` 속성을 기본값인 30초에서 2초로 변경합니다. 이렇게 변경하면 Azure Resource Manager 엔드포인트에서 장기 실행 작업의 상태를 폴링하는 데 걸리는 시간이 줄어듭니다. 모두 비동기 호출인 라이브 이벤트 만들기, 시작 및 중지와 같은 주요 작업을 완료하는 데 걸리는 시간이 단축됩니다. 대부분의 시나리오에서 2초 값을 권장합니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateMediaServicesClient)]

### <a name="create-a-live-event"></a>라이브 이벤트 만들기

이 섹션에서는 라이브 이벤트의 기본 *통과* 형식을 만드는 방법을 보여줍니다(`LiveEventEncodingType`을 `PassthroughBasic`으로 설정). 사용할 수 있는 유형에 대한 정보는 [라이브 이벤트 유형](live-event-outputs-concept.md#live-event-types)을 참조하세요. 기본 또는 표준 통과 외에도 720p 또는 1080p 적응형 비트 전송률 클라우드 인코딩용 라이브 인코딩 이벤트를 사용할 수 있습니다.
 
라이브 이벤트를 만들 때 다음 항목을 지정할 수 있습니다.

* **라이브 이벤트에 대한 수집 프로토콜**. 현재 RTMP, RTMPS 및 부드러운 스트리밍 프로토콜이 지원됩니다. 라이브 이벤트 또는 연결된 라이브 출력이 실행 중인 동안에는 프로토콜 옵션을 변경할 수 없습니다. 다른 프로토콜이 필요한 경우 각 스트리밍 프로토콜에 대해 별도의 라이브 이벤트를 만듭니다.  
* **수집 및 미리 보기에 대한 IP 제한**. 이 라이브 이벤트에 비디오를 수집하도록 허용된 IP 주소를 정의할 수 있습니다. 허용된 IP 주소는 다음 옵션 중 하나로 지정할 수 있습니다.

  * 단일 IP 주소(예: `10.0.0.1`)
  * IP 주소 및 CIDR(Classless Inter-Domain Routing) 서브넷 마스크를 사용하는 IP 범위(예: `10.0.0.1/22`)
  * IP 주소와 점으로 구분된 10진수 서브넷 마스크를 사용하는 IP 범위(예: `10.0.0.1(255.255.252.0)`)

  지정된 IP 주소가 없고 정의된 규칙이 없는 경우, IP 주소가 허용되지 않습니다. 모든 IP 주소를 허용하려면 규칙을 만들고 `0.0.0.0/0`으로 설정합니다. IP 주소는 4개의 숫자를 사용하는 IPv4 주소 또는 CIDR 주소 범위 형식 중 하나여야 합니다.
* **이벤트를 만들 때 자동으로 시작됩니다**. 자동 시작을 `true`로 설정하면 라이브 이벤트가 생성된 후 시작됩니다. 즉, 라이브 이벤트를 실행하는 즉시 청구가 시작됩니다. 추가 청구를 중지하려면 라이브 이벤트 리소스에 대해 명시적으로 `Stop`을 호출해야 합니다. 자세한 내용은 [라이브 이벤트 상태 및 청구](live-event-states-billing-concept.md)를 참조하세요.

  대기 모드는 실행 상태로 더 빠르게 전환할 수 있게 만들어주는 저렴한 '할당됨' 상태에서 라이브 이벤트를 시작하는 데 사용됩니다. 이는 스트리머에 채널을 빠르게 전달해야 하는 핫 풀과 같은 상황에서 유용합니다.
* **정적 호스트 이름 및 고유 GUID**. 수집 URL을 하드웨어 기반 라이브 인코더에서 예측할 수 있고 쉽게 유지 관리할 수 있도록 `useStaticHostname` 속성을 `true`로 설정합니다. `accessToken`에는 고유한 사용자 지정 GUID를 사용합니다. 자세한 내용은 [라이브 이벤트 수집 URL](live-event-outputs-concept.md#live-event-ingest-urls)을 참조하세요.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveEvent)]

### <a name="create-an-asset-to-record-and-archive-the-live-event"></a>라이브 이벤트를 기록하고 보관할 자산 만들기

이 코드 블록에서 라이브 이벤트 보관을 기록할 "테이프"로 사용할 빈 자산을 만듭니다.

이러한 개념을 학습할 때는 자산 개체를 과거 비디오 테이프 레코더에 넣는 테이프로 생각하면 도움이 됩니다. 라이브 출력은 테이프 레코더 머신에 해당합니다. 라이브 이벤트는 머신 뒤로 들어오는 비디오 신호에 불과합니다.

자산 또는 "테이프"는 언제든지 만들 수 있습니다. 이 비유에서 "테이프 레코더"인 라이브 출력 개체에 빈 자산을 전달합니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateAsset)]

### <a name="create-the-live-output"></a>라이브 출력 만들기

이 섹션에서는 자산 이름을 입력으로 사용하여 라이브 이벤트를 기록할 위치를 알려주는 라이브 출력을 만듭니다. 또한 레코딩에 사용할 DVR(시간 이동) 시간 범위를 설정했습니다.

샘플 코드는 시간 이동 시간 범위를 1시간으로 설정하는 방법을 보여줍니다. 이렇게 하면 클라이언트가 이벤트의 마지막 1시간 동안 어디서나 재생할 수 있습니다. 또한 라이브 이벤트의 마지막 1시간만 아카이브에 남게 됩니다. 필요한 경우 이 시간 범위를 최대 25시간까지 연장할 수 있습니다.  또한 HLS(HTTP 라이브 스트리밍) 및 DASH(Dynamic Adaptive Streaming over HTTP) 매니페스트가 게시될 때 URL 경로에서 사용하는 출력 매니페스트 이름을 제어할 수 있습니다.

실시간 출력 또는 이 비유의 "테이프 레코더"도 언제든지 만들 수 있습니다. 신호 흐름을 시작하기 전이나 후에 라이브 출력을 만들 수 있습니다. 속도를 높여야 하는 경우 신호 흐름을 시작하기 전에 출력을 만드는 것이 도움이 되는 경우가 있습니다.

라이브 출력은 생성되면 시작되고 삭제되면 중지됩니다.  라이브 출력을 삭제해도 기본 자산 또는 자산의 콘텐츠는 삭제되지 않습니다. "테이프"를 꺼내는 것이라 생각해 보세요. 녹화가 포함된 자산은 원하는 만큼 지속됩니다. 이를 꺼낼 때(즉, 라이브 출력이 삭제될 때) 요청 시 즉시 볼 수 있게 됩니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateLiveOutput)]


### <a name="get-ingest-urls"></a>수집 URL 가져오기

라이브 이벤트가 생성되면 라이브 인코더에 제공할 수집 URL을 가져올 수 있습니다. 인코더는 이러한 URL로 라이브 RTMP 프로토콜을 사용하여 스트림을 입력합니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetIngestURL)]

### <a name="get-the-preview-url"></a>미리 보기 URL 가져오기

`previewEndpoint`를 사용하여 인코더의 입력이 수신되고 있는지 미리 보고 확인합니다.

> [!IMPORTANT]
> 계속하기 전에 비디오가 미리 보기 URL로 전달되고 있는지 확인합니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#GetPreviewURL)]

### <a name="create-and-manage-live-events-and-live-outputs"></a>라이브 이벤트 및 라이브 출력 만들기 및 관리

스트림이 라이브 이벤트로 유입되면 클라이언트 플레이어가 사용할 스트리밍 로케이터를 게시하여 스트리밍 이벤트를 시작할 수 있습니다. 이렇게 하면 스트리밍 엔드포인트를 통해 시청자에게 스트림이 제공됩니다.

먼저 라이브 이벤트를 만들어 신호를 만듭니다. 해당 라이브 이벤트를 시작하고 인코더를 입력에 연결할 때까지 신호는 이동하지 않습니다.

"테이프 레코더"를 중지하려면 `LiveOutput`에서 `delete`를 호출합니다. 이 작업은 "테이프"(자산)에 있는 보관의 *콘텐츠* 를 삭제하지 않습니다. "테이프 레코더"만 삭제하고 보관을 중지합니다. 자산은 자산 자체에서 명시적으로 `delete`를 호출할 때까지 보관된 비디오 콘텐츠로 항상 유지됩니다. `LiveOutput`을 삭제하자마자 게시된 스트리밍 로케이터 URL을 통해 "자산"의 레코딩된 콘텐츠를 재생할 수 있습니다. 

보관된 콘텐츠를 재생하는 클라이언트 기능을 제거하려면 먼저 자산에서 모든 로케이터를 제거해야 합니다. 또한 전송을 위해 CDN(콘텐츠 배달 네트워크)을 사용하는 경우 URL 경로에서 CDN 캐시를 플러시합니다. 그렇지 않으면 콘텐츠가 CDN의 표준 TTL(Time-to-Live) 설정(최대 72시간) 동안 CDN의 캐시에 저장됩니다.

#### <a name="create-a-streaming-locator-to-publish-hls-and-dash-manifests"></a>HLS 및 DASH 매니페스트를 게시하기 위해 스트리밍 로케이터 만들기

> [!NOTE]
> Media Services 계정이 생성되면 기본 스트리밍 엔드포인트가 중지됨 상태로 계정에 추가됩니다. 콘텐츠 스트리밍을 시작하고 [동적 패키징](encode-dynamic-packaging-concept.md) 및 동적 암호화를 활용하려면 콘텐츠를 스트리밍하려는 스트리밍 엔드포인트가 실행 상태에 있어야 합니다.

스트리밍 로케이터를 사용해 자산을 게시한 경우, 스트리밍 로케이터가 만료 또는 삭제되는 시점 중 먼저 도래하는 시점까지 라이브 이벤트(최대 DVR 기간 길이)를 계속 볼 수 있습니다. 이는 보기 대상 사용자가 라이브로 또는 요청 시 가상 ‘테이프’ 레코딩을 볼 수 있는 방법입니다. 레코딩이 완료(라이브 출력이 삭제될 때)되면 동일한 URL을 사용하여 라이브 이벤트, DVR 창 또는 주문형 자산을 볼 수 있습니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#CreateStreamingLocator)]

#### <a name="build-the-paths-to-the-hls-and-dash-manifests"></a>HLS 및 DASH 매니페스트에 대한 경로 빌드

샘플의 `BuildManifestPaths` 메서드는 다양한 클라이언트 및 플레이어 프레임워크에 HLS 또는 DASH 전송에 사용할 스트리밍 경로를 결정론적으로 만드는 방법을 보여줍니다.

[!code-typescript[Main](../../../media-services-v3-node-tutorials/AMSv3Samples/Live/index.ts#BuildManifestPaths)]

## <a name="watch-the-event"></a>이벤트 보기

이벤트를 보려면 스트리밍 로케이터를 만들기 위해 코드를 실행할 때 가져온 스트리밍 URL을 복사합니다. 원하는 미디어 플레이어를 사용할 수 있습니다. [Azure Media Player](https://amp.azure.net/libs/amp/latest/docs/index.html)는 [Media Player 데모 사이트](https://ampdemo.azureedge.net)에서 스트림을 테스트하는 데 사용할 수 있습니다.

라이브 이벤트가 중지되면 이벤트가 자동으로 주문형 콘텐츠로 변환됩니다. 이벤트를 중지하고 삭제한 후에도 자산을 삭제하지 않는 한 사용자는 주문형 비디오로 보관된 콘텐츠를 스트리밍할 수 있습니다. 이벤트에서 사용 중인 자산은 삭제할 수 없습니다. 이벤트를 먼저 삭제해야 합니다.

## <a name="clean-up-resources-in-your-media-services-account"></a>Media Services 계정의 리소스 정리

애플리케이션을 끝까지 실행하면 `cleanUpResources` 함수에 사용된 모든 리소스가 자동으로 정리됩니다. 애플리케이션 또는 디버거가 완료될 때까지 실행되는지 확인합니다. 그렇지 않으면 리소스가 유출되어 계정에서 라이브 이벤트가 실행될 수 있습니다. Azure Portal에서 다시 확인하여 Media Services 계정에서 모든 리소스가 정리되었는지 확인합니다. 

샘플 코드에서 자세한 내용은 `cleanUpResources` 메서드를 참조하세요.

> [!IMPORTANT]
> 라이브 이벤트가 계속 실행되도록 두면 비용이 청구됩니다. 프로젝트 또는 프로그램이 응답을 중지하거나 어떤 이유로든 종료되면 라이브 이벤트가 청구 상태로 계속 실행될 수 있습니다.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="more-developer-documentation-for-nodejs-on-azure"></a>Azure의 Node.js에 대한 추가 개발자 설명서

- [JavaScript 및 Node.js 개발자용 Azure](/azure/developer/javascript/)
- [@azure/azure-sdk-for-js GitHub 리포지토리의 Media Services 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/mediaservices/arm-mediaservices)
- [Node.js 개발자를 위한 Azure 패키지 설명서](/javascript/api/overview/azure/)


## <a name="next-steps"></a>다음 단계

[파일 스트리밍](stream-files-tutorial-with-api.md)