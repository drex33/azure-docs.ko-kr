---
title: Media Services v3 샘플
description: 이 문서에는 메서드 및 SDK별로 구성된 Media Services v3에 사용할 수 있는 모든 샘플의 목록이 포함되어 있습니다.  샘플에는 .NET, Node.JS, Python, Java 외에도 Postman을 사용하는 REST가 포함됩니다.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: overview
ms.date: 03/24/2021
ms.author: inhenkel
ms.openlocfilehash: 1f7397423eef2969e3a3e61b2201770a1e54a2ed
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438357"
---
# <a name="media-services-v3-samples"></a>Media Services v3 샘플

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

이 문서에는 메서드 및 SDK별로 구성된 Media Services에 사용할 수 있는 모든 샘플의 목록이 포함되어 있습니다.  샘플에는 .NET, Node.js(TypeScript), Python 및 Java 외에도 Postman을 사용하는 REST가 포함됩니다.

## <a name="samples-by-sdk"></a>SDK별 샘플

각 탭에서 찾고 있는 샘플에 대한 설명과 링크를 확인할 수 있습니다.

## <a name="net"></a>[.NET](#tab/net/)

| 샘플 | Description |
|-------------|-------------|
| [Account/CreateAccount](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Account/CreateAccount)|샘플은 Media Services 계정을 만들고 기본 스토리지 계정을 설정하는 방법, 키 전달 IP 허용 목록, 관리 ID, 스토리지 인증을 비롯한 고급 구성 설정과 사용자 고유의 암호화 키를 가져오는 방법을 보여 줍니다.|
| [VideoEncoding/Encoding_PredefinedPreset](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_PredefinedPreset)|샘플은 기본 제공 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법을 보여 줍니다.|
| [VideoEncoding/Encoding_H264_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | 제약 조건이 없는 H.264 콘텐츠 인식 인코딩을 가장 기본적으로 사용하는 방법을 보여 줍니다. |
| [VideoEncoding/Encoding_H264_ContentAware_Constrained](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | PresetConfigurations 클래스를 사용하여 사전 설정의 출력 동작을 제한하는 방법을 보여줍니다.|
| [VideoEncoding/Encoding_H264](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264)|샘플은 사용자 지정 H.264 인코딩 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법을 보여 줍니다.|
| [VideoEncoding/Encoding_HEVC_ContentAware](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | 콘텐츠 인식 인코딩을 사용하고 제약 조건이 없는 HEVC 코덱을 기본적으로 사용하는 방법을 보여 줍니다.  PresetConfigurations 클래스는 HEVC에도 지원되며 이 샘플에 추가할 수 있습니다.|
| [VideoEncoding/Encoding_HEVC](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC)|샘플은 사용자 지정 HEVC 인코딩 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법을 보여 줍니다.|
| [VideoEncoding/Encoding_StitchTwoAssets](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_StitchTwoAssets)|샘플은 JobInputSequence를 사용하여 작업을 제출하여 시작 또는 종료 시간에 잘릴 수 있는 둘 이상의 자산을 연결하는 방법을 보여 줍니다. 인코딩된 결과 파일은 모든 자산이 함께 연결된 단일 비디오입니다.  이 샘플은 확인을 위해 스트리밍 및 다운로드 결과에 대한 출력 자산도 게시합니다.|
| [VideoEncoding/Encoding_SpriteThumbnail](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_SpriteThumbnail)|샘플은 썸네일 스프라이트를 사용한 사용자 지정 사전 설정과 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법을 보여 줍니다.|
| [Live/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Live/LiveEventWithDVR)|이 샘플은 먼저 최대 25시간까지 전체 보관을 사용한 LiveEvent와 5분 DVR 창을 사용한 자산에 대한 필터를 만드는 방법을 보여 준 후, 필터를 사용하여 스트리밍을 위해 로케이터를 만드는 방법을 보여 줍니다.|
| [VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoAnalytics/VideoAnalyzer)|이 샘플은 비디오 분석기 변환을 만들고, 비디오 파일을 입력 자산에 업로드하고, 변환이 있는 작업을 제출하고, 확인을 위해 결과를 다운로드하는 방법을 설명합니다.|
| [AudioAnalytics/AudioAnalyzer](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/AudioAnalytics/AudioAnalyzer)|이 샘플은 오디오 분석기 변환을 만들고, 미디어 파일을 입력 자산에 업로드하고, 변환이 있는 작업을 제출하고, 확인을 위해 결과를 다운로드하는 방법을 설명합니다.|
| [ContentProtection/BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicAESClearKey)|이 샘플은 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 비밀 키를 사용하여 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 토큰을 가져오고, Azure Media Player에서 재생할 URL을 출력하는 방법을 설명합니다. 플레이어에서 스트림을 요청하면 Media Services는 지정된 키를 사용하여 AES-128을 사용하여 콘텐츠를 동적으로 암호화하고, Azure Media Player는 토큰을 사용하여 암호 해독합니다.|
| [ContentProtection/BasicWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicWidevine)|이 샘플은 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 비밀 키를 사용하여 Widevine 구성이 포함된 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 토큰을 가져오고, Widevine Player에서 재생할 URL을 출력하는 방법을 설명합니다. 사용자가 Widevine으로 보호되는 콘텐츠를 요청하면 플레이어 애플리케이션에서 Media Services 라이선스 서비스로부터 라이선스를 요청합니다. 플레이어 애플리케이션에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. Widevine 라이선스에는 클라이언트 플레이어에서 콘텐츠를 해독하고 스트림하는 데 사용할 수 있는 암호 해독 키가 포함되어 있습니다.|
| [ContentProtection/BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/BasicPlayReady)|이 샘플은 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 비밀 키를 사용하여 PlayReady 구성이 포함된 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 토큰을 가져오고, Azure Media Player에서 재생할 URL을 출력하는 방법을 설명합니다. 사용자가 PlayReady로 보호되는 콘텐츠를 요청하면 플레이어 애플리케이션에서 Media Services 라이선스 서비스로부터 라이선스를 요청합니다. 플레이어 애플리케이션에 권한이 있으면 Media Services 라이선스 서비스에서 플레이어에 라이선스를 발급합니다. PlayReady 라이선스에는 클라이언트 플레이어가 콘텐츠를 암호 해독하고 스트리밍하는 데 사용할 수 있는 암호 해독 키가 포함되어 있습니다.|
| [ContentProtection/OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/ContentProtection/OfflinePlayReadyAndWidevine)|이 샘플은 PlayReady 및 Widevine DRM을 사용하여 콘텐츠를 동적으로 암호화하고, 라이선스 서비스에서 라이선스를 요청하지 않고 콘텐츠를 재생하는 방법을 설명합니다. 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 개방 제한 및 PlayReady/Widevine 영구 구성을 사용하여 ContentKeyPolicy를 만들고, ContentKeyPolicy를 StreamingLocator와 연결하고, 재생할 URL을 출력하는 방법을 보여 줍니다.|
| [Streaming/AssetFilters](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/AssetFilters)|이 샘플은 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, 자산 필터 및 계정 필터를 만들고, 필터를 스트리밍 로케이터와 연결하고, 재생할 URL을 출력하는 방법을 설명합니다.|
| [Streaming/StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/Streaming/StreamHLSAndDASH)|이 샘플은 기본 제공 AdaptiveStreaming 사전 설정을 사용하여 변환을 만들고, 작업을 제출하고, HLS 및 DASH 스트리밍에 대한 출력 자산을 게시하는 방법을 설명합니다.|
| [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/HighAvailabilityEncodingStreaming/) | 이 샘플은 주문형 인코딩 또는 분석을 사용하는 프로덕션 시스템에 대한 지침 및 모범 사례를 제공합니다. 읽기 권한자는 [Media Services 및 VOD를 통한 고가용성](architecture-high-availability-encoding-concept.md) 관련 문서를 시작해야 합니다. [HighAvailabilityEncodingStreaming](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/main/HighAvailabilityEncodingStreaming/README.md) 샘플에 제공되는 별도의 솔루션 파일이 있습니다. |
| [Media Services에 대한 Azure Functions](https://github.com/xpouyat/media-services-v3-dotnet-core-functions-integration/tree/main/Functions)|이 프로젝트에는 비디오를 처리하기 위해 Azure Media Services v3에 연결하는 Azure Functions의 예제가 포함되어 있습니다. Visual Studio 2019 또는 Visual Studio Code를 사용하여 함수를 개발하고 실행할 수 있습니다. ARM(Azure Resource Manager) 템플릿 및 GitHub Actions 워크플로는 Function 리소스를 배포하고 지속적인 배포를 사용하도록 설정하기 위해 제공됩니다.|

## <a name="nodejs"></a>[Node.JS](#tab/node/)

|샘플|Description|
|---|---|
|[Hello World - 자산 나열](https://github.com/Azure-Samples/media-services-v3-node-tutorials/blob/main/AMSv3Samples/HelloWorld-ListAssets/list-assets.ts)|자산을 연결하고 나열하는 방법에 대한 기본 예제입니다. |
|[라이브 스트리밍](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/Live/index.ts)| 기본 라이브 스트리밍 예제입니다. **경고** 라이브를 사용하는 경우 모든 리소스가 정리되고 포털에서 더는 청구되지 않는지 확인해야 합니다.|
|[HLS 및 DASH 업로드 및 스트림](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesSample/index.ts)| 로컬 파일을 업로드하거나 원본 URL에서 인코딩하는 기본 예제입니다. 샘플에서는 스토리지 SDK를 사용하여 콘텐츠를 다운로드하는 방법을 보여 주고 플레이어에 스트리밍하는 방법을 보여 줍니다. |
|[PlayReady 및 Widevine DRM을 사용하여 HLS 및 DASH 업로드 및 스트림](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/StreamFilesWithDRMSample/index.ts)| Widevine 및 PlayReady DRM을 사용하여 인코딩하고 스트림하는 방법을 설명합니다. |
|[비디오 및 오디오 인덱싱을 위한 AI 업로드 및 사용](https://github.com/Azure-Samples/media-services-v3-node-tutorials/tree/main/AMSv3Samples/VideoIndexerSample/index.ts)| 비디오 및 오디오 분석기 사전 설정을 사용하여 비디오 또는 오디오 파일에서 메타데이터와 인사이트를 생성하는 예제입니다. |

## <a name="python"></a>[Python](#tab/python)

|샘플|Description|
|---|---|
|[Python을 통한 기본 인코딩](https://github.com/Azure-Samples/media-services-v3-python)| 로컬 파일을 업로드하거나 원본 URL에서 인코딩하는 기본 예제입니다. 샘플에서는 스토리지 SDK를 사용하여 콘텐츠를 다운로드하는 방법을 보여 주고 플레이어에 스트리밍하는 방법을 보여 줍니다. |
|[이벤트 및 함수를 사용한 얼굴 편집](https://github.com/Azure-Samples/media-services-v3-python/tree/main/VideoAnalytics/FaceRedactorEventBased)| 이는 Azure Storage 계정에 있는 즉시 비디오에서 Azure Media Services Face Redactor 작업을 트리거하는 이벤트 기반 접근 방법의 예제입니다. 솔루션에 Azure Media Services, Azure Function, Event Grid 및 Azure Storage를 활용합니다. 솔루션에 대한 전체 설명은 [README.md](https://github.com/Azure-Samples/media-services-v3-python/blob/main/VideoAnalytics/FaceRedactorEventBased/README.md)를 참조하세요.|


## <a name="java"></a>[Java](#tab/java)

|샘플|설명|
|---|---|
|[AudioAnalytics/AudioAnalyzer/](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer)|미디어 파일에서 오디오를 분석하는 방법입니다. |
|Content Protection|
|ContentProtection||
|[BasicAESClearKey](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicAESClearKey)|AES-128을 사용하여 콘텐츠를 동적으로 암호화하는 방법입니다.|
|[BasicPlayReady](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicPlayReady)|PlayReady DRM을 사용하여 콘텐츠를 동적으로 암호화하는 방법입니다.|
|[BasicWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/BasicWidevine)|Widevine DRM을 사용하여 콘텐츠를 동적으로 암호화하는 방법입니다.|
|[OfflineFairPlay](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflineFairPlay)|FairPlay DRM을 사용하여 콘텐츠를 동적으로 암호화하고, 라이선스 서비스에서 라이선스를 요청하지 않고 콘텐츠를 재생하는 방법입니다.|
|[OfflinePlayReadyAndWidevine](https://github.com/Azure-Samples/media-services-v3-java/tree/master/ContentProtection/OfflinePlayReadyAndWidevine)|PlayReady 및 Widevine DRM을 사용하여 콘텐츠를 동적으로 암호화하고, 라이선스 서비스에서 라이선스를 요청하지 않고 콘텐츠를 재생하는 방법입니다.|
|DynamicPackagingVODContent||
|[AssetFilters](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/AssetFilters)|자산 및 계정 필터를 사용하여 콘텐츠를 필터링하는 방법입니다.|
|[StreamHLSAndDASH](https://github.com/Azure-Samples/media-services-v3-java/tree/master/DynamicPackagingVODContent/StreamHLSAndDASH)|스트리밍을 위해 VOD 콘텐츠를 HLS/DASH로 동적으로 패키지하는 방법입니다.|
|[LiveIngest/LiveEventWithDVR](https://github.com/Azure-Samples/media-services-v3-java/tree/master/LiveIngest/LiveEventWithDVR)|최대 25시간의 전체 보관을 사용하여 LiveEvent를 만들고 5분 DVR 기간을 사용하여 자산에 대한 필터를 만드는 방법 및 스트리밍을 위한 로케이터를 만들고 필터를 사용하는 방법입니다.|
|[VideoAnalytics/VideoAnalyzer](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer)|최대 25시간의 전체 보관을 사용하여 LiveEvent를 만들고 5분 DVR 기간을 사용하여 자산에 대한 필터를 만드는 방법 및 스트리밍을 위한 로케이터를 만들고 필터를 사용하는 방법입니다.|
|VideoEncoding||
|[EncodingWithMESCustomPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESCustomPreset)|StandardEncoderPreset 설정을 사용하여 사용자 지정 인코딩 변환을 만드는 방법입니다.|
|[EncodingWithMESPredefinedPreset](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoEncoding/EncodingWithMESPredefinedPreset)|기본 제공 사전 설정 및 HTTP URL 입력을 사용하여 작업을 제출하고, 스트리밍을 위해 출력 자산을 게시하고, 확인을 위해 결과를 다운로드하는 방법입니다.|

---

## <a name="rest-postman-collection"></a>REST Postman Collection

[REST Postman](https://github.com/Azure-Samples/media-services-v3-rest-postman) 샘플에는 Postman 클라이언트로 가져올 수 있는 Postman 환경 및 컬렉션이 포함되어 있습니다. Postman 컬렉션 샘플은 API 구조, ARM(Azure Resource Management)과의 작동 방식 및 클라이언트 SDK의 호출 구조를 숙지하는 데 권장됩니다. 

[!INCLUDE [warning-rest-api-retry-policy.md](./includes/warning-rest-api-retry-policy.md)]
