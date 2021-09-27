---
title: Azure Video Analyzer for Media(이전의 Video Indexer) 및 Azure Media Services v3 사전 설정 비교
description: 이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer) 기능과 Azure Media Services v3 사전 설정을 비교합니다.
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: juliako
ms.openlocfilehash: 66e62013c4719bf0b20808a240bdfa44483ab5b1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607618"
---
# <a name="compare-azure-media-services-v3-presets-and-video-analyzer-for-media"></a>Azure Media Services v3 미리 설정 및 Video Analyzer for Media 비교 

이 문서에서는  **API** 와 **Media Services v3 API** 의 기능을 비교합니다. 

현재 [Video Analyzer for Media API](https://api-portal.videoindexer.ai/)와 [Media Services v3 API](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json)에는 중복되는 기능이 있습니다. 다음 표에는 차이점과 유사점을 이해하기 위한 현재의 지침이 나와 있습니다. 

## <a name="compare"></a>비교

|기능|Video Analyzer for Media API |Media Services v3 API의<br/>비디오 분석기 및 오디오 분석기 미리 설정|
|---|---|---|
|미디어 인사이트|[향상된](video-indexer-output-json-v2.md) |[기본 사항](../../media-services/latest/analyze-video-audio-files-concept.md)|
|환경|지원되는 기능의 전체 목록 보기: <br/> [개요](video-indexer-overview.md)|비디오 인사이트만 반환합니다.|
|결제|[Media Services 가격](https://azure.microsoft.com/pricing/details/media-services/#analytics)|[Media Services 가격](https://azure.microsoft.com/pricing/details/media-services/#analytics)|
|규정 준수|최신 규정 준수 업데이트는 [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)를 방문하고 "Video Analyzer for Media"를 검색하여 관심 있는 인증서를 준수하는지 확인합니다.|최신 규정 준수 업데이트는 [Azure Compliance Offerings.pdf](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942/file/178110/23/Microsoft%20Azure%20Compliance%20Offerings.pdf)를 방문하고 "Media Services"를 검색하여 관심 있는 인증서를 준수하는지 확인합니다.|
|무료 평가판|미국 동부|사용할 수 없음|
|지역 가용성|[지역별 Cognitive Services 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=cognitive-services)을 참조하세요.|[지역별 Media Services 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=media-services)을 참조하세요.|

## <a name="next-steps"></a>다음 단계

[Video Analyzer for Media 개요](video-indexer-overview.md)

[Media Services v3 개요](../../media-services/latest/media-services-overview.md)
