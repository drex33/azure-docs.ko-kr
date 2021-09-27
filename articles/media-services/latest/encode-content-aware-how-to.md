---
title: 내용 인식 인코딩 사전 설정을 사용하는 방법
description: 이 문서에서는 Microsoft Azure Media Services v3에서 콘텐츠 인식 인코딩 사전 설정을 사용하는 방법을 설명합니다.
services: media-services
documentationcenter: ''
author: jiayali-ms
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 09/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 09d3f199cc17cc1457413f673afe3c6be0646f58
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128668624"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>내용 인식 인코딩 사전 설정을 사용하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>소개
[콘텐츠 인식 인코딩 사전 설정은](encode-content-aware-concept.md) 적응 비트 [전송률 스트리밍을](encode-autogen-bitrate-ladder.md)통해 배달할 콘텐츠를 더 잘 준비합니다. 여기서 비디오는 여러 비트 전송률로 인코딩해야 합니다. 여기에는 인코더가 광범위한 계산 분석을 요구하지 않고 지정된 해상도에 대한 최적의 비트 전송률 값을 렌더링할 수 있는 사용자 지정 논리가 포함됩니다. 사전 설정은 적응 스트리밍이 배달할 최적의 계층 수, 적절한 비트 전송률 및 해상도 설정을 결정합니다.

적응 스트리밍 사전 설정에 비해 콘텐츠 인식 인코딩은 낮음 및 중간 복잡성 비디오에 더 효과적입니다. 여기서 출력 파일은 낮은 비트 전송률이지만 여전히 좋은 보기 환경을 제공하는 품질입니다.

### <a name="using-the-content-aware-encoding-presets"></a>콘텐츠 인식 인코딩 사전 설정 사용

코드에서 콘텐츠 인식 사전 설정으로 인코딩하려면 [BuiltInStandardEncoderPreset](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset) 개체를 사용하고 [PresetName](/dotnet/api/microsoft.azure.management.media.models.builtinstandardencoderpreset.presetname) 속성을 다음 기본 제공 미리 설정 이름 중 하나로 설정합니다.

- **ContentAwareEncoding**  - 이 사전 설정은 H.264를 지원합니다.
- **H265ContentAwareEncoding** - 이 사전 설정은 HEVC(H.265)를 지원합니다.

> [!NOTE]
> ContentAwareEncodingExperimental이 아닌 **ContentAwareEncoding** 사전 설정을 사용해야 합니다. 또는 HEVC로 인코딩하려는 경우 **H265ContentAwareEncoding** 을 사용할 수 있습니다.

```csharp
TransformOutput[] output = new TransformOutput[]
{
   new TransformOutput
   {
      // The preset for the Transform is set to one of Media Services built-in sample presets.
      // You can customize the encoding settings by changing this to use "StandardEncoderPreset" class.
      Preset = new BuiltInStandardEncoderPreset()
      {
         // This sample uses the new preset for content-aware encoding
         PresetName = EncoderNamedPreset.ContentAwareEncoding
      }
   }
};
```

## <a name="configure-output-settings"></a>출력 설정 구성

또한 개발자는 적응 비트 전송률 스트리밍 구간을 인코딩하기 위한 최적의 설정을 결정할 때 콘텐츠 인식 인코딩 사전 설정에서 사용하는 출력 범위를 제어할 수도 있습니다.

**개발자는 PresetConfigurations** 클래스를 사용하여 일련의 제약 조건 및 옵션을 콘텐츠 인식 인코딩 사전 설정에 전달하여 인코더에서 생성된 결과 파일을 제어할 수 있습니다. 속성은 인코딩 작업의 환경 또는 비용을 제어하기 위해 모든 인코딩을 특정 최대 해상도로 제한하려는 상황에 특히 유용합니다.  또한 대상에서 모바일 네트워크 또는 대역폭 제약 조건이 있는 글로벌 지역에서 지원할 수 있는 최대 비트 전송률 및 최소 비트 전송률도 제어할 수 있습니다.

PresetConfigurations 클래스를 사용하면 콘텐츠 인식 인코딩 사전 설정에서 다음 설정을 조정할 수 있습니다. 


| 속성                   | Description                                                                                                                                                                         |
| ------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 복잡성              | 속도, 균형 조정 또는 품질. 속도와 품질 간의 균형을 제어하도록 인코더 설정을 구성할 수 있습니다. 예제: 인코딩 속도가 빠르지만 압축 효율성이 낮을 경우 복잡성을 속도로 설정합니다. |
| interleaveOutput          |    출력 MP4 파일의 형식을 지정하는 방법을 제어합니다.  오디오 및 비디오 둘 다와 인터리브하여 개별적으로 더 쉽게 공유할 수 있습니다. 또는 오디오 및 비디오 파일을 분리하여 추가 언어 오디오 트랙을 더 쉽게 지연 바인딩하거나 나중에 오디오를 수정할 수 있습니다.                                                                                                                                                                                 |
| keyFrameIntervalInSeconds |        파일을 인코딩할 때 GOP(각 사진 그룹)에 사용되는 키 프레임 거리를 설정합니다.  일반적인 최신 적응 스트리밍 프로토콜은 2초를 사용합니다.  제공하는 네트워크 조건에 따라 더 긴 간격을 사용하는 데 도움이 될 수 있습니다. CDN 공급자에게 문의하거나 자체 네트워크에서 몇 가지 실험을 수행합니다.                                                                                                                                                                             |
| maxBitrateBps             |               적응 스트리밍 사다리의 최상위 비트 전송률도 제한합니다.  이 제약 조건은 스토리지 비용 및 네트워크 배달 비용을 제어하는 데 유용합니다.  또한 클라이언트에서 지원하는 범위 내에서 배달된 비트 전송률도 유지할 수 있습니다.                                                                                                                                                                       |
| maxHeight                 |       적응형 사다리에서 인코딩할 수 있는 최상위 해상도입니다.  이는 비용을 제어하거나 대상 사용자의 환경을 제어하는 데에도 유용합니다. 모든 인코딩을 표준 정의로 제한하거나 원하는 경우 최대 720P로 제한할 수 있습니다.                                                                                                                                                                              |
| maxLayers                 |        이 속성은 적응 스트리밍 사다리의 계층 수를 제어합니다. 또한 정의에 따라 스토리지 컨테이너에 MP4로 출력되는 파일 수를 제어합니다.  이 값을 낮추면 비용이 감소합니다. 출력 변환을 더 적게 생성하기 때문에 인코딩 시간(분)의 총 수가 줄어듭니다. 이를 maxHeight 및 maxBitrateBps와 함께 사용하여 인코딩하는 동안 비용을 제어하고 청구를 보다 예측 가능하게 만듭니다.                                                                                                                                                                             |
| minBitrateBps             |                           인코더가 선택하는 가장 낮은 비트 전송률도 제어합니다.  이를 사용하여 저비트 전송률 및 저해상도 변환의 품질을 최적화합니다.                                                                                                                                                         |
| minHeight                 |     이 설정은 적응 비트 전송률 사다리가 생성하는 가장 작은 해상도를 제어합니다. 이렇게 하면 클라이언트가 인코딩하는 콘텐츠 형식에 대해 너무 흐리게 표시될 수 있는 매우 낮은 해상도 변환을 선택하는 것을 방지할 수 있습니다. 이렇게 하면 제공하는 환경의 품질에 많은 도움이 됩니다.                                                                                                                                                                                |
|

### <a name="example-code-for-configuring-content-aware-preset"></a>콘텐츠 인식 사전 설정을 구성하기 위한 예제 코드

다음 코드 조각은 [콘텐츠 인식, H.246 및 제약 조건이 있는 샘플 인코딩의 코드 조각입니다.](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained)

[!code-csharp[Main](../../../media-services-v3-dotnet/VideoEncoding/Encoding_H264_ContentAware_Constrained/Program.cs#PresetConfigurations)]

## <a name="samples"></a>샘플

다음 샘플에서는 사용할 수 있는 다양한 코덱 및 제약 조건, 사전 설정을 인스턴스화하는 방법 및 인코딩 작업을 제출하고 모니터링하는 방법을 보여줍니다.

### <a name="net"></a>.NET

| 샘플 | Description|
|---------|------------------|
| [콘텐츠 인식 및 H.246으로 인코딩](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware) | 제약 조건 없이 H.264 콘텐츠 인식 인코딩을 가장 기본적인 용도로 사용하는 것을 보여줍니다. |
| [콘텐츠 인식, H.246 및 제약 조건으로 인코딩](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_H264_ContentAware_Constrained) | PresetConfigurations 클래스를 사용하여 사전 설정의 출력 동작을 제한하는 방법을 보여줍니다.|
| [콘텐츠 인식 및 HEVC로 인코딩(H.265)](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/main/VideoEncoding/Encoding_HEVC_ContentAware) | 콘텐츠 인식 인코딩 및 제약 조건이 없는 HEVC 코덱의 기본 사용을 보여 줍니다.  PresetConfigurations 클래스도 HEVC에 대해 지원되며 이 샘플에 추가할 수 있습니다.|

> [!NOTE]
> `ContentAwareEncoding` 사전 설정을 사용하는 인코딩 작업은 출력 시간(분)만을 기준으로 요금이 청구됩니다. AMS는 2단계 인코딩을 사용하며 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#overview)에 나열된 것 이외의 사전 설정을 사용하는 것과 관련된 추가 요금은 없습니다.
  
## <a name="next-steps"></a>다음 단계

* [자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - CLI](stream-files-cli-quickstart.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - .NET](stream-files-dotnet-quickstart.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - Node.js](stream-files-nodejs-quickstart.md)
