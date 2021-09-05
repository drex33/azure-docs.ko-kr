---
title: Azure Media Services 레거시 구성 요소 | Microsoft Docs
description: 이 토픽에서는 Azure Media Services 레거시 구성 요소를 설명합니다.
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
ms.openlocfilehash: 588eb1f93fa953a9051b4e3228980217c63ea27b
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114719389"
---
# <a name="azure-media-services-legacy-components"></a>Azure Media Services 레거시 구성 요소

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

시간이 지남에 따라 Media Service 구성 요소를 개선하고 레거시 구성 요소를 사용 중지합니다. 이 문서는 레거시 구성 요소에서 현재 구성 요소로 애플리케이션을 마이그레이션하는 데 도움이 됩니다.
 
## <a name="retirement-plans-of-legacy-components-and-migration-guidance"></a>레거시 구성 요소의 사용 중지 플랜 및 마이그레이션 참고 자료

WAME(*Windows Azure Media Encoder*)과 AME(*Azure Media Encoder*) 미디어 프로세서는 사용되지 않습니다.

* [Windows Azure Media Encoder에서 Media Encoder Standard로 마이그레이션](migrate-windows-azure-media-encoder.md)
* [Azure Media Encoder에서 Media Encoder Standard로 마이그레이션](migrate-azure-media-encoder.md)

다음 미디어 분석 미디어 프로세서는 더는 사용되지 않거나, 곧 더는 사용되지 않을 예정입니다.

 
| **미디어 프로세서 이름** | **사용 중지 날짜** | **추가적인 참고 사항** |
| --- | --- | ---|
| Azure Media Indexer 2 | 2020년 1월 1일 | 이 미디어 프로세서는 [Media Services v3 AudioAnalyzerPreset Basic 모드](../latest/analyze-video-audio-files-concept.md)로 바뀝니다. 자세한 내용은 [Azure Media Indexer 2에서 Azure Video Analyzer for Media(이전의 Video Indexer)로 마이그레이션](migrate-indexer-v1-v2.md)을 참조하세요. |
| Azure Media Indexer | 2023년 3월 1일 | 이 미디어 프로세서는 [Media Services v3 AudioAnalyzerPreset Basic 모드](../latest/analyze-video-audio-files-concept.md)로 바뀝니다. 자세한 내용은 [Azure Media Indexer 2에서 Azure Video Analyzer for Media로 마이그레이션](migrate-indexer-v1-v2.md)을 참조하세요. |
| 동작 감지 | 2020년 6월 1일|지금은 교체 플랜이 없습니다. |
| 비디오 요약 |2020년 6월 1일|지금은 교체 플랜이 없습니다.|
| 동영상 OCR(Optical Character Recognition) | 2020년 6월 1일 |이 미디어 프로세서는 Azure Video Analyzer for Media로 대체되었습니다. 또한 [Azure Media Services v3 API](../latest/analyze-video-audio-files-concept.md)를 사용하는 것이 좋습니다. <br/>[Azure Media Services v3 미리 설정 및 Video Analyzer for Media 비교](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)를 참조하세요. |
| 얼굴 감지기 | 2020년 6월 1일 | 이 미디어 프로세서는 Azure Video Analyzer for Media로 대체되었습니다. 또한 [Azure Media Services v3 API](../latest/analyze-video-audio-files-concept.md)를 사용하는 것이 좋습니다. <br/>[Azure Media Services v3 미리 설정 및 Video Analyzer for Media 비교](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)를 참조하세요. |
| Content Moderator | 2020년 6월 1일 |이 미디어 프로세서는 Azure Video Analyzer for Media로 대체되었습니다. 또한 [Azure Media Services v3 API](../latest/analyze-video-audio-files-concept.md)를 사용하는 것이 좋습니다. <br/>[Azure Media Services v3 미리 설정 및 Video Analyzer for Media 비교](../../azure-video-analyzer/video-analyzer-for-media-docs/compare-video-indexer-with-media-services-presets.md)를 참조하세요. |

## <a name="next-steps"></a>다음 단계

[Media Services v2에서 v3로 이동하기 위한 마이그레이션 지침](../latest/migrate-v-2-v-3-migration-introduction.md)
