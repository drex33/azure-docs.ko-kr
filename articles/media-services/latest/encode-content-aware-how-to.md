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
ms.date: 08/17/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: b6537547db2bb3371b6d7b4cd36ebf9ffc48530c
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122530987"
---
# <a name="how-to-use-the-content-aware-encoding-preset"></a>콘텐츠 인식 인코딩 사전 설정을 사용하는 방법

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="introduction"></a>소개
[콘텐츠 인식 인코딩 사전 설정](encode-content-aware-how-to.md)은 [적응 비트 전송률 스트리밍](encode-autogen-bitrate-ladder.md)을 통해 배달할 콘텐츠를 더 잘 준비합니다. 여기서 비디오는 여러 비트 전송률로 인코딩해야 합니다. 여기에는 광범위한 계산 분석을 요구하지 않고 인코더가 지정된 해상도에 대한 최적의 비트 전송률 값을 검색할 수 있는 사용자 지정 논리가 포함됩니다. 사전 설정은 적응 스트리밍이 배달할 최적의 계층 수, 적절한 비트 전송률 및 해상도 설정을 결정합니다. 

적응 스트리밍 사전 설정에 비해 콘텐츠 인식 인코딩은 낮음 및 중간 복잡성 비디오에 더 효과적입니다. 여기서 출력 파일은 낮은 비트 전송률이지만 여전히 좋은 보기 환경을 제공하는 품질입니다.

## <a name="use-the-content-aware-encoding-preset"></a>콘텐츠 인식 인코딩 사전 설정 사용
다음과 같이 이 사전 설정을 사용하는 변환을 만들 수 있습니다.

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

변환 출력을 사용하는 자습서는 [다음 단계](#next-steps) 섹션을 참조하세요. 출력 자산은 자습서에 표시된 것처럼 MPEG-DASH 및 HLS와 같은 프로토콜로 Media Services 스트리밍 엔드포인트에서 배달할 수 있습니다.

> [!NOTE]
> `ContentAwareEncoding` 사전 설정을 사용하는 인코딩 작업은 출력 시간(분)만을 기준으로 요금이 청구됩니다. AMS는 2단계 인코딩을 사용하며 [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#overview)에 나열된 것 이외의 사전 설정을 사용하는 것과 관련된 추가 요금은 없습니다.
  
## <a name="next-steps"></a>다음 단계

* [자습서: Media Services v3를 사용하여 비디오 업로드, 인코딩 및 스트리밍](stream-files-tutorial-with-api.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - REST](stream-files-tutorial-with-rest.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - CLI](stream-files-cli-quickstart.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - .NET](stream-files-dotnet-quickstart.md)
* [자습서: URL에 따라 원격 파일 인코딩 및 비디오 스트림 - Node.js](stream-files-nodejs-quickstart.md)
