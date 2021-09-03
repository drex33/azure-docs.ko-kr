---
title: 인덱서 v1 및 v2에서 Azure Media Services Video Indexer로 마이그레이션 | Microsoft Docs
description: 이 토픽에서는 Azure Media Indexer v1 및 v2에서 Azure Media Services Video Indexer로 마이그레이션하는 방법에 대해 설명합니다.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2021
ms.author: inhenkel
ms.openlocfilehash: 3a2871bd42e6b4da9ec20dc918c5c0ab79ed1a64
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114721558"
---
# <a name="migrate-from-media-indexer-and-media-indexer-2-to-video-analyzer-for-media"></a>Media Indexer 및 Media Indexer 2에서 Video Analyzer for Media로 마이그레이션 

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!IMPORTANT]
> 고객은 인덱서 v1 및 인덱서 v2에서 [Media Services v3 AudioAnalyzerPreset 기본 모드](../latest/analyze-video-audio-files-concept.md)를 사용하도록 마이그레이션하는 것이 좋습니다. [Azure Media Indexer](media-services-index-content.md) 미디어 프로세서 및 [Azure Media Indexer 2 Preview](./legacy-components.md) 미디어 프로세서를 사용 중지하고 있습니다. 사용 중지 날짜는 이 [레거시 구성 요소](legacy-components.md) 항목을 참조하세요.

Azure Video Analyzer for Media는 Azure 미디어 분석, Azure Cognitive Search, Cognitive Services(Face API, Microsoft 번역기, Computer Vision API, Custom Speech 서비스 등)를 기반으로 구축된 클라우드 애플리케이션입니다. 이 애플리케이션에서 Video Analyzer for Media 비디오 및 오디오 모델을 사용하여 비디오의 인사이트를 추출할 수 있습니다. Video Analyzer for Media를 사용할 수 있는 시나리오, 제공하는 기능, 시작하는 방법을 보려면 [Video Analyzer for Media 동영상 및 오디오 모델](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md)을 참조하세요. 

[Azure Media Services v3 분석기 사전 설정](../latest/analyze-video-audio-files-concept.md)을 사용하거나 [Video Analyzer for Media API](https://api-portal.videoindexer.ai/)를 직접 사용하여 비디오 및 오디오 파일에서 인사이트를 추출할 수 있습니다. 현재 Video Analyzer for Media API와 Media Services v3 API에는 중복되는 기능이 있습니다.

> [!NOTE]
> Video Analyzer for Media와 Media Services 분석기 기본 설정 간의 차이점을 이해하려면 [비교 문서](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)를 확인 하세요.

이 문서에서는 Azure Media Indexer와 Azure Media Indexer 2에서 Video Analyzer for Media로 마이그레이션하는 단계를 설명합니다.  

## <a name="migration-options"></a>마이그레이션 옵션

|필요한 경우  |다음으로 |
|---|---|
|VTT, SRT 또는 TTML의 선택 자막 파일 형식으로 모든 미디어 파일 형식에 대한 음성을 텍스트로 변환 기록을 제공하는 솔루션입니다.<br/>키워드, 토픽 유추, 음향 이벤트, 화자 분할, 엔터티 추출 및 번역과 같은 추가 오디오 인사이트도 있습니다.| Video Analyzer for Media v2 REST API 또는 Azure Media Services v3 Audio Analyzer 사전 설정을 통해 Video Analyzer for Media 기능을 사용하도록 애플리케이션을 업데이트합니다.|
|음성을 텍스트로 변환 기능| Cognitive Services Speech API를 직접 사용합니다.|  

## <a name="getting-started-with-video-analyzer-for-media"></a>Video Analyzer for Media 시작

다음 섹션에서는 관련 링크를 제공 합니다. [Video Analyzer for Media로 시작하려면 어떻게 해야 하나요?](../../azure-video-analyzer/video-analyzer-for-media-docs/video-indexer-overview.md#how-can-i-get-started-with-video-analyzer-for-media) 

## <a name="getting-started-with-media-services-v3-apis"></a>Media Services v3 API 시작하기

Azure Media Services v3 API를 사용하면 [Azure Media Services v3 분석기 사전 설정](../latest/analyze-video-audio-files-concept.md)을 통해 Video Indexer로 비디오 및 오디오 파일에서 인사이트를 추출할 수 있습니다.

**AudioAnalyzerPreset** 를 사용하면 오디오 또는 비디오 파일에서 여러 오디오 인사이트를 추출할 수 있습니다. 출력에는 오디오 음성에 대한 VTT 또는 TTML 파일과 JSON 파일(추가 오디오 인사이트 포함)이 있습니다. 오디오 인사이트에는 키워드, 화자 인덱싱 및 음성 감정 분석이 포함 됩니다. AudioAnalyzerPreset은 특정 언어에 대한 언어 감지도 지원 합니다. 자세한 내용은 [변환](/rest/api/media/transforms/createorupdate#audioanalyzerpreset)을 참조하세요.

### <a name="get-started"></a>시작

시작하려면

* [자습서](../latest/analyze-videos-tutorial.md)
* AudioAnalyzerPreset 샘플: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/AudioAnalytics/AudioAnalyzer) 또는 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/AudioAnalytics/AudioAnalyzer)
* VideoAnalyzerPreset 샘플: [Java SDK](https://github.com/Azure-Samples/media-services-v3-java/tree/master/VideoAnalytics/VideoAnalyzer) 또는 [.NET SDK](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/VideoAnalytics/VideoAnalyzer)

## <a name="getting-started-with-cognitive-services-speech-services"></a>Cognitive Services 음성 서비스 시작

[Azure Cognitive Services](../../cognitive-services/index.yml)는 오디오 스트림을 애플리케이션, 도구 또는 디바이스가 사용하거나 표시할 수 있는 텍스트로 실시간으로 기록하는 음성을 텍스트로 변환을 제공합니다. 음성을 텍스트로 변환을 사용하여 [고유한 음향 모델, 언어 모델 또는 발음 모델을 사용자 지정](../../cognitive-services/speech-service/how-to-custom-speech-train-model.md)할 수 있습니다. 자세한 내용은 [Cognitive Services 음성을 텍스트로 변환](../../cognitive-services/speech-service/speech-to-text.md)을 참조하세요. 

> [!NOTE] 
> 음성을 텍스트로 변환 서비스는 비디오 파일 형식을 사용하지 않으며 [특정 오디오 형식](../../cognitive-services/speech-service/rest-speech-to-text.md#audio-formats)만 사용합니다. 

텍스트 음성 변환 서비스 및 시작 하는 방법에 대한 자세한 내용은 [음성-텍스트 변환이란?](../../cognitive-services/speech-service/speech-to-text.md)을 참조 하세요.

## <a name="known-differences-from-deprecated-services"></a>사용되지 않는 서비스의 알려진 차이점

Video Analyzer for Media, Azure Media Services v3 AudioAnalyzerPreset 및 Cognitive Services Speech Services 서비스를 사용하는 것이 더 안정적이며 사용 중지된 Azure Media Indexer 1 및 Azure Media Indexer 2 프로세서보다 우수한 품질의 출력을 생성합니다.  

몇 가지 알려진 차이점은 다음과 같습니다.

* Cognitive Services Speech Services는 키워드 추출을 지원 하지 않습니다. 그러나 Video Analyzer for Media 및 Media Services v3 AudioAnalyzerPreset는 JSON 파일 형식에서 보다 강력한 키워드 집합을 제공합니다.

## <a name="support"></a>지원

[새 지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)으로 이동하여 지원 티켓을 열 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [레거시 구성 요소](legacy-components.md)
* [가격 책정 페이지](https://azure.microsoft.com/pricing/details/media-services/#encoding)
