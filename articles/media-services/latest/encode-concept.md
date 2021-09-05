---
title: Media Services를 사용하여 비디오 및 오디오 인코딩
description: 이 문서에서는 Azure Media Services를 사용한 비디오 및 오디오 인코딩에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 7b4b3806ed00955096939c4b996c9a9d3daec004
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530678"
---
# <a name="encoding-video-and-audio-with-media-services"></a>Media Services를 사용하여 비디오 및 오디오 인코딩

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Media Services에서 인코딩은 (a) 파일 크기를 줄이기 위해 및/또는 (b) 다양한 디바이스 및 앱과 호환되는 형식으로 만들기 위해 디지털 비디오 및/또는 오디오가 포함된 파일을 한 가지 표준 형식에서 다른 표준 형식으로 변환하는 과정을 의미합니다. 이 프로세스를 비디오 압축 또는 코드 변환이라고도 부릅니다. 이 개념에 대한 자세한 내용은 [데이터 압축](https://en.wikipedia.org/wiki/Data_compression) 및 [인코딩 및 코드 변환이란?](https://www.streamingmedia.com/Articles/Editorial/What-Is-/What-Is-Encoding-and-Transcoding-75025.aspx)을 참조하세요.

비디오는 일반적으로 [점진적 다운로드](https://en.wikipedia.org/wiki/Progressive_download) 또는 [적응 비트 전송률 스트리밍](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)을 통해 디바이스 및 앱에 전달됩니다.

> [!IMPORTANT]
> Media Services는 취소되었거나 오류가 발생한 작업에 대해 요금을 청구하지 않습니다. 예를 들어 진행률 50%에 도달한 후 취소된 작업은 작업 시간 50%에 해당하는 요금이 청구되지 않습니다. 완료된 작업에 대해서만 요금이 청구됩니다.

* 점진적 다운로드로 전달하기 위해서는 Azure Media Services를 사용하여 [H.264](https://en.wikipedia.org/wiki/H.264/MPEG-4_AVC) 코덱으로 인코딩된 비디오와 [AAC](https://en.wikipedia.org/wiki/Advanced_Audio_Coding) 코덱으로 인코딩된 오디오가 포함된 [MP4](https://en.wikipedia.org/wiki/MPEG-4_Part_14) 파일로 디지털 미디어 파일(mezzanine)을 변환할 수 있습니다. 이 MP4 파일은 스토리지 계정에 있는 자산에 기록됩니다. Azure Storage API 또는 SDK(예를 들어 [Storage REST API](../../storage/common/storage-rest-api-auth.md) 또는 [.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md))를 사용하여 파일을 직접 다운로드할 수 있습니다. 스토리지에서 특정 컨테이너 이름을 사용하여 출력 자산을 만든 경우 이 위치를 사용합니다. 그렇지 않으면 Media Services를 사용하여 [자산 컨테이너 URL을 나열](/rest/api/media/assets/listcontainersas)할 수 있습니다. 
* 적응 비트 전송률 스트리밍으로 콘텐츠 전달을 준비하려면 mezzanine 파일을 다중 비트 전송률(높은 수준에서 낮은 수준)로 인코딩해야 합니다. 정상적인 품질 전환을 위해 비트 전송률이 낮아지면 비디오 해상도가 낮아집니다. 그 결과 인코딩 표라고 부르는 해상도 및 비트 전송률 사다리가 생성됩니다([자동 생성된 적응 비트 전송률 사다리](encode-autogen-bitrate-ladder.md) 참조 또는 권장 콘텐츠 인식 인코딩 사전 설정 사용). Media Services를 사용하여 mezzanine 파일을 여러 비트 전송률로 인코딩할 수 있습니다. 이렇게 하면 MP4 파일과 스토리지 계정의 자산에 기록되는 연관된 스트리밍 구성 파일 집합을 얻을 수 있습니다. 그런 후 Media Services의 [동적 패키징](encode-dynamic-packaging-concept.md) 기능을 사용하여 [MPEG-DASH](https://en.wikipedia.org/wiki/Dynamic_Adaptive_Streaming_over_HTTP) 및 [HLS](https://en.wikipedia.org/wiki/HTTP_Live_Streaming)와 같은 스트리밍 프로토콜을 통해 비디오를 전달할 수 있습니다. 이렇게 하려면 [스트리밍 로케이터](stream-streaming-locators-concept.md)를 만들고 지원되는 프로토콜에 따라 스트리밍 URL을 빌드할 수 있습니다. 그런 후 해당 기능에 따라 디바이스/앱으로 이를 전달할 수 있습니다.

다음 다이어그램은 동적 패키징을 사용하는 주문형 인코딩의 워크플로를 보여줍니다.

![동적 패키징을 사용하는 주문형 인코딩 워크플로](./media/encode-dynamic-packaging-concept/media-services-dynamic-packaging.svg)

이 항목에서는 Media Services v3을 사용하여 콘텐츠를 인코딩하는 방법에 대한 지침을 제공합니다.

## <a name="transforms-and-jobs"></a>변환 및 작업

Media Services v3을 사용하여 인코딩하려면 [변환](/rest/api/media/transforms) 및 [작업](/rest/api/media/jobs)을 만들어야 합니다. 변환은 인코딩 설정 및 출력에 대한 방법을 정의합니다. 작업은 해당 방법의 인스턴스입니다. 자세한 내용은 [Transform 및 Jobs](transform-jobs-concept.md)를 참조하세요.

Media Services를 사용하여 인코딩하는 경우 미리 설정을 사용하여 입력 미디어 파일을 처리하는 방법을 인코더에 알려줍니다. Media Services v3에서는 표준 인코더를 사용하여 파일을 인코딩합니다. 예를 들어 인코딩된 콘텐츠에서 원하는 비디오 해상도 및/또는 오디오 채널 수를 지정할 수 있습니다.

업계 모범 사례에 따라 권장되는 기본 제공 미리 설정 중 하나를 사용하여 빠르게 시작하거나 특정 시나리오 또는 디바이스 요구 사항을 대상으로 지정하는 사용자 지정 미리 설정을 작성할 수 있습니다. 자세한 내용은 [사용자 지정 변환을 사용하여 인코딩](transform-custom-presets-how-to.md)을 참조하세요.

2019년 1월부터 시작하여, 표준 인코더로 인코딩하여 MP4 파일을 만들면 새로운 .mpi 파일이 생성되고 출력 자산에 추가됩니다. 이 MPI 파일은 [동적 패키징](encode-dynamic-packaging-concept.md) 및 스트리밍 시나리오의 성능을 향상하기 위해 사용합니다.

> [!NOTE]
> MPI 파일을 수정 또는 제거하거나 해당 파일의 존재 여부에 따른 종속성을 서비스에서 사용하지 않아야 합니다.

### <a name="creating-job-input-from-an-https-url"></a>HTTPS URL에서 작업 입력 만들기

비디오 처리 작업을 제출할 때는 Media Services에 입력 비디오를 찾을 위치를 알려주어야 합니다. 한 가지 옵션은 HTTPS URL을 작업 입력으로 지정하는 것입니다. 현재까지 Media Services v3는 HTTPS URL을 통해 청크 분할 전송 인코딩을 지원하지 않습니다.

#### <a name="examples"></a>예제

* [.NET을 사용하여 HTTPS URL에서 인코딩](stream-files-dotnet-quickstart.md)
* [REST를 사용하여 HTTPS URL에서 인코딩](stream-files-tutorial-with-rest.md)
* [CLI를 사용하여 HTTPS URL에서 인코딩](stream-files-cli-quickstart.md)
* [Node.js를 사용하여 HTTPS URL에서 인코딩](stream-files-nodejs-quickstart.md)

### <a name="creating-job-input-from-a-local-file"></a>로컬 파일에서 작업 입력 만들기

입력 비디오는 Media Service 자산으로 저장할 수 있으며 이 경우 파일(로컬 또는 Azure Blob Storage에 저장됨)을 기반으로 입력 자산을 만듭니다.

#### <a name="examples"></a>예제

[기본 제공 사전 설정을 사용하여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)

### <a name="creating-job-input-with-subclipping"></a>서브클리핑을 사용하여 작업 입력 만들기

또한 비디오를 인코딩할 때 소스 파일을 자르거나 클리핑하도록 지정하고 입력 비디오 중 원하는 부분만 포함된 출력을 만들 수 있습니다. 이 기능은 [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) 사전 설정 또는 [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) 사전 설정을 사용하여 빌드된 모든 [변환](/rest/api/media/transforms)에서 작동합니다.

주문형 또는 라이브 아카이브(기록된 이벤트) 비디오의 단일 클립으로 [작업](/rest/api/media/jobs/create)을 만들도록 지정할 수 있습니다. 작업 입력은 자산 또는 HTTPS URL일 수 있습니다.

> [!TIP]
> 비디오를 다시 인코딩하지 않고 비디오 서브클립을 스트리밍하려면 [동적 패키지 작성 도구를 사용하여 매니페스트 사전 필터링](filters-dynamic-manifest-concept.md)을 사용하는 것이 좋습니다.

#### <a name="examples"></a>예제

예제 참조:

* [.NET을 사용한 비디오 서브클리핑](transform-subclip-video-dotnet-how-to.md)
* [REST를 사용한 비디오 서브클리핑](transform-subclip-video-rest-how-to.md)

## <a name="built-in-presets"></a>기본 제공 미리 설정

Media Services는 다음과 같은 기본 제공 인코딩 사전 설정을 지원합니다.  

### <a name="builtinstandardencoderpreset"></a>BuiltInStandardEncoderPreset

[BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩하는 기본 제공 미리 설정을 지정하는 데 사용됩니다.

현재 지원되는 기본 제공 사전 설정은 다음과 같습니다.

- **EncoderNamedPreset.AACGoodQualityAudio**: 192kbps로 인코딩된 스테레오 오디오만 포함된 단일 MP4 파일을 만듭니다.
- **EncoderNamedPreset.AdaptiveStreaming**(권장): H.264 적응 비트 전송률 인코딩을 지원합니다. 자세한 내용은 [비트 전송률 사다리 자동 생성](encode-autogen-bitrate-ladder.md)을 참조하세요.
- **EncoderNamerPreset.H265AdaptiveStreaming**: AdaptiveStreaming 사전 설정과 비슷하지만 HEVC(H.265) 코덱이 사용됩니다. H.265 비디오 및 스테레오 AAC 오디오가 포함된 GOP 정렬 MP4 파일 집합을 만듭니다. 입력, 해상도, 비트 전송률 및 프레임 속도를 기준으로 비트 전송률 사다리를 자동 생성합니다. 자동 생성된 사전 설정은 입력 해상도를 초과하지 않습니다. 예를 들어 입력이 720p일 때 출력은 아무리 높아도 720p로 유지됩니다.
- **EncoderNamedPreset.ContentAwareEncoding**: H.264 콘텐츠 인식 인코딩을 위한 사전 설정을 제공합니다. 콘텐츠 인식 인코딩을 사용하여 GOP 맞춤 MP4 세트를 생성합니다. 입력 콘텐츠에 따라 서비스가 입력 콘텐츠의 초기 경량 분석을 수행하고 그 결과를 사용하여 적응 스트리밍으로 전송을 위해 최적 레이어 수, 적합한 비트 전송률 및 해상도 설정을 결정합니다. 이 사전 설정은 출력 파일이 낮은 비트 전송률로 제공되지만 시청자에게 여전히 좋은 경험을 제공하는 품질로 유지되는 낮은 품질 및 중간 품질의 복합 비디오에 특히 효과적입니다. 출력에는 비디오 및 오디오가 인터리브된 MP4 파일이 포함됩니다. 이 사전 설정은 최대 1080P HD의 출력만 생성합니다. 4K 출력이 필요한 경우 "maxBitrateBps" 속성을 사용하여 [PresetConfigurations](https://github.com/Azure/azure-rest-api-specs/blob/7026463801584950d4ccbaa6b05b15d29555dd3a/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L2397)로 사전 설정을 구성할 수 있습니다. 자세한 내용은 [콘텐츠 인식 인코딩](encode-content-aware-concept.md)을 참조하세요.
- **EncoderNamedPreset.H265ContentAwareEncoding**: HEVC(H.265) 콘텐츠 인식 인코딩을 위한 사전 설정을 제공합니다. 콘텐츠 인식 인코딩을 사용하여 GOP 맞춤 MP4 세트를 생성합니다. 입력 콘텐츠에 따라 서비스가 입력 콘텐츠의 초기 경량 분석을 수행하고 그 결과를 사용하여 적응 스트리밍으로 전송을 위해 최적 레이어 수, 적합한 비트 전송률 및 해상도 설정을 결정합니다. 이 사전 설정은 출력 파일이 낮은 비트 전송률로 제공되지만 시청자에게 여전히 좋은 경험을 제공하는 품질로 유지되는 낮은 품질 및 중간 품질의 복합 비디오에 특히 효과적입니다. 출력에는 비디오 및 오디오가 인터리브된 MP4 파일이 포함됩니다. 이 사전 설정은 최대 4K HD의 출력을 생성합니다. 8K 출력이 필요한 경우 "maxBitrateBps" 속성을 사용하여 [PresetConfigurations](https://github.com/Azure/azure-rest-api-specs/blob/7026463801584950d4ccbaa6b05b15d29555dd3a/specification/mediaservices/resource-manager/Microsoft.Media/stable/2021-06-01/Encoding.json#L2397)로 사전 설정을 구성할 수 있습니다.
  > [!NOTE]
  > 이제 사용되지 않는 ContentAwareEncodingExperimental 대신 **ContentAwareEncoding** 을 사용해야 합니다.

- **EncoderNamedPreset.H264MultipleBitrate1080p**: 6000kbps~400kbps 범위의 8개 GOP 정렬 MP4 파일과 스테레오 AAC 오디오 집합을 생성합니다. 해상도는 1,080p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264MultipleBitrate720p**: 3400kbps~400kbps 범위의 6개 GOP 정렬 MP4 파일과 스테레오 AAC 오디오 집합을 생성합니다. 해상도는 720p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264MultipleBitrateSD**: 1600kbps~400kbps 범위의 5개 GOP 정렬 MP4 파일과 스테레오 AAC 오디오 집합을 생성합니다. 해상도는 480p에서 시작하여 360p까지 낮아집니다.
- **EncoderNamedPreset.H264SingleBitrate1080p**: H.264 코덱을 사용하여 6750kbps로 인코딩되고 영상 높이가 1080 픽셀인 비디오와 AAC-LC 코덱을 사용하여 64kbps로 인코딩된 스테레오 오디오를 포함하는 MP4 파일을 생성합니다.
- **EncoderNamedPreset.H264SingleBitrate720p**: H.264 코덱을 사용하여 4500kbps로 인코딩되고 영상 높이가 720 픽셀인 비디오와 AAC-LC 코덱을 사용하여 64kbps로 인코딩된 스테레오 오디오를 포함하는 MP4 파일을 생성합니다.
- **EncoderNamedPreset.H264SingleBitrateSD**: H.264 코덱을 사용하여 2200kbps로 인코딩되고 영상 높이가 480 픽셀인 비디오와 AAC-LC 코덱을 사용하여 64kbps로 인코딩된 스테레오 오디오를 포함하는 MP4 파일을 생성합니다.
- **EncoderNamedPreset.H265SingleBitrate720P**: HEVC(H.265) 코덱을 사용하여 1800kbps로 인코딩되고 영상 높이가 720 픽셀인 비디오와 AAC-LC 코덱을 사용하여 128kbps로 인코딩된 스테레오 오디오를 포함하는 MP4 파일을 생성합니다.
- **EncoderNamedPreset.H265SingleBitrate1080p**: HEVC(H.265) 코덱을 사용하여 3500kbps로 인코딩되고 영상 높이가 1080 픽셀인 비디오와 AAC-LC 코덱을 사용하여 128kbps로 인코딩된 스테레오 오디오를 포함하는 MP4 파일을 생성합니다.
- **EncoderNamedPreset.H265SingleBitrate4K**: HEVC(H.265) 코덱을 사용하여 9500kbps로 인코딩되고 영상 높이가 2160 픽셀인 비디오와 AAC-LC 코덱을 사용하여 128kbps로 인코딩된 스테레오 오디오를 포함하는 MP4 파일을 생성합니다.

최신 사전 설정 목록을 보려면 [비디오 인코딩에 사용할 기본 제공 사전 설정](/rest/api/media/transforms/createorupdate#encodernamedpreset)을 참조하세요.

사전 설정 사용 방법을 보려면 [파일 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)을 참조하세요.

### <a name="standardencoderpreset"></a>StandardEncoderPreset

[StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset)은 표준 인코더로 입력 비디오를 인코딩할 때 사용할 설정을 설명합니다. Transform 미리 설정을 사용자 지정할 때 이 미리 설정을 사용합니다.

#### <a name="considerations"></a>고려 사항

사용자 지정 사전 설정을 만들 때 다음 고려 사항이 적용됩니다.

- AVC 콘텐츠의 높이 및 너비에 대한 모든 값은 4의 배수여야 합니다.
- Azure Media Services v3에서 모든 인코딩 비트 전송률은 비트/초입니다. 이는 킬로비트/초를 단위로 사용한 v2 API의 사전 설정과는 다릅니다. 예를 들어 v2의 비트 전송률이 128(킬로비트/초)로 지정된 경우 v3에서는 128000(비트/초)으로 설정됩니다.

### <a name="customizing-presets"></a>사전 설정 사용자 지정

Media Services는 특정 인코딩 필요 및 요구 사항을 충족하기 위해 미리 설정에 포함된 모든 값을 완전히 사용자 지정할 수 있도록 지원합니다. 인코더 사전 설정을 사용자 지정하는 방법의 예를 보려면 아래 목록을 참조하세요.

#### <a name="examples"></a>예제

- [.NET을 사용하여 사전 설정 사용자 지정](transform-custom-presets-how-to.md)
- [CLI를 사용하여 사전 설정 사용자 지정](transform-custom-preset-cli-how-to.md)
- [REST를 사용하여 사전 설정 사용자 지정](transform-custom-preset-rest-how-to.md)


## <a name="preset-schema"></a>사전 설정 스키마

Media Services v3에서 사전 설정은 API 자체에 있는 강력한 형식의 엔터티입니다. [Open API 사양(또는 Swagger)](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01)에서 이러한 개체에 대한 “스키마” 정의를 찾아볼 수 있습니다 또한 [REST API](/rest/api/media/transforms/createorupdate#standardencoderpreset), [.NET SDK](/dotnet/api/microsoft.azure.management.media.models.standardencoderpreset)(또는 다른 Media Services v3 SDK 참조 설명서)에서 사전 설정 정의(예: **StandardEncoderPreset**)를 확인할 수 있습니다.

## <a name="scaling-encoding-in-v3"></a>v3에서 인코딩 크기 조정

미디어 처리를 크기 조정하려면 [CLI를 사용하여 크기 조정](media-reserved-units-cli-how-to.md)을 참조하세요.
API의 **2020-05-01** 버전 또는 Azure Portal을 통해 생성된 계정의 경우 크기 조정 및 미디어 예약 단위가 더 이상 필요하지 않습니다. 크기 조정은 서비스 내부에서 자동으로 처리됩니다.

## <a name="billing"></a>결제

Media Services는 취소되었거나 오류가 발생한 작업에 대해 요금을 청구하지 않습니다. 예를 들어 진행률 50%에 도달한 후 취소된 작업은 작업 시간 50%에 해당하는 요금이 청구되지 않습니다. 완료된 작업에 대해서만 요금이 청구됩니다.

자세한 내용은 [가격 책정](https://azure.microsoft.com/pricing/details/media-services/)을 참조하세요.

## <a name="ask-questions-give-feedback-get-updates"></a>질문, 피드백 제공, 업데이트 받기

[Azure Media Services 커뮤니티](media-services-community.md) 문서를 체크 아웃하여 다양한 방법으로 질문을 하고, 피드백을 제공하고, Media Services에 대한 업데이트를 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Media Services를 사용하여 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [기본 제공 사전 설정을 사용하여 HTTPS URL에서 인코딩](job-input-from-http-how-to.md)
* [기본 제공 사전 설정을 사용하여 로컬 파일 인코딩](job-input-from-local-file-how-to.md)
* [특정 시나리오 또는 디바이스 요구 사항을 대상으로 사용자 지정 사전 설정 빌드](transform-custom-presets-how-to.md)
