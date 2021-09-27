---
title: Azure Video Analyzer for Media(이전의 Video Indexer) 개념 - Azure
titleSuffix: Azure Video Analyzer for Media (formerly Video Indexer)
description: 이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer) 용어 및 개념에 대한 간략한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 01/19/2021
ms.author: juliako
ms.openlocfilehash: 6b68ee08d6569bd22709e5b9ee25cbbf745c0470
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630170"
---
# <a name="video-analyzer-for-media-concepts"></a>Video Analyzer for Media 개념

이 문서에서는 Azure Video Analyzer for Media(이전의 Video Indexer) 용어 및 개념에 대한 간략한 개요를 제공합니다.

## <a name="audiovideocombined-insights"></a>오디오/비디오/결합된 인사이트

Video Analyzer for Media에 비디오를 업로드하면 Video Analyzer for Media에서 다양한 AI 모델을 실행하여 시각적 개체와 오디오를 모두 분석합니다. Video Analyzer for Media가 비디오를 분석할 때 AI 모델이 인사이트를 추출합니다. 자세한 내용은 [개요](video-indexer-overview.md)를 참조하세요.

## <a name="confidence-scores"></a>신뢰도 점수 

신뢰도 점수는 인사이트의 신뢰도를 나타냅니다. 0\.0에서 1.0 사이의 숫자입니다. 이 점수가 높을수록 응답의 신뢰도가 높습니다. 예제: 

```json
"transcript":[
{
  "id":1,
  "text":"Well, good morning everyone and welcome to",
  "confidence":0.8839,
  "speakerId":1,
  "language":"en-US",
  "instances":[
     {
    "adjustedStart":"0:00:10.21",
    "adjustedEnd":"0:00:12.81",
    "start":"0:00:10.21",
    "end":"0:00:12.81"
     }
  ]
},
```

## <a name="content-moderation"></a>콘텐츠 조정

텍스트 및 시각적 콘텐츠 조정 모델을 사용하여 부적절한 콘텐츠로부터 사용자를 보호하고 게시하는 콘텐츠가 조직의 가치에 부합하는지 유효성을 검사합니다. 자동으로 특정 비디오를 차단하거나 사용자에게 콘텐츠에 관해 경고할 수 있습니다. 자세한 내용은 [인사이트: 시각 및 텍스트 콘텐츠 조정](video-indexer-output-json-v2.md#visualcontentmoderation)을 참조하세요. 

## <a name="blocks"></a>블록   

블록을 사용하면 데이터를 더 쉽게 살펴볼 수 있습니다. 예를 들어 블록은 화자가 변경되거나 오랫동안 일시 중지되는 경우를 기준으로 나눌 수 있습니다.  

## <a name="project-and-editor"></a>프로젝트 및 편집기

[Video Analyzer for Media](https://www.videoindexer.ai/) 웹 사이트에서는 비디오의 심층 인사이트를 사용하여 올바른 미디어 콘텐츠를 찾고, 관심 있는 부분을 찾은 다음 결과를 사용하여 완전히 새로운 프로젝트를 만들 수 있습니다. 프로젝트를 만든 후에는 Video Analyzer for Media에서 렌더링하고 다운로드한 다음, 자체 편집 애플리케이션 또는 다운스트림 워크플로에서 사용할 수 있습니다.

이 기능을 유용하게 사용할 수 있는 몇 가지 시나리오는 다음과 같습니다. 

* 예고편을 위한 영화 하이라이트 만들기
* 뉴스 캐스트에서 이전 비디오 클립 사용
* 소셜 미디어에 대한 짧은 콘텐츠 만들기

자세한 내용은 [편집기를 사용하여 프로젝트 만들기](use-editor-create-project.md)를 참조하세요.

## <a name="keyframes"></a>키 프레임

Video Analyzer for Media는 각 샷을 가장 잘 나타내는 프레임을 선택합니다. 키 프레임은 미적 속성(예: 대비 및 안정성)을 기준으로 전체 비디오에서 선택된 대표 프레임입니다. 자세한 내용은 [장면, 샷 및 키 프레임](scenes-shots-keyframes.md)을 참조하세요.

## <a name="time-range-vs-adjusted-time-range"></a>시간 범위 및 조정된 시간 범위   

TimeRange는 원본 비디오의 시간 범위입니다. AdjustedTimeRange는 현재 재생 목록을 기준으로 한 시간 범위입니다. 다른 비디오의 다른 줄에서 재생 목록을 만들 수 있으므로 1시간 분량의 비디오를 가져오고 이 비디오에서 한 줄만 사용할 수 있습니다(예: 10:00-10:15). 이 경우 TimeRange는 10:00-10:15이지만 adjustedTimeRange는 00:00-00:15인 한 줄의 재생 목록을 갖게 됩니다. 

## <a name="widgets"></a>위젯

Video Analyzer for Media는 앱에 위젯 포함을 지원합니다. 자세한 내용은 [앱에 Video Analyzer for Media 위젯 포함](video-indexer-embed-widgets.md)을 참조하세요.

## <a name="summarized-insights"></a>요약된 인사이트  

요약된 인사이트에는 얼굴, 주제, 감정 등의 데이터에 대한 집계된 보기가 포함됩니다. 예를 들어 수천 개의 시간 범위 각각을 검토하고 포함된 얼굴을 확인하는 대신, 요약된 인사이트에는 모든 얼굴과 각 얼굴에 대해 표시된 시간 범위 및 시간의 비율(%)을 나타냅니다.  

## <a name="next-steps"></a>다음 단계

- [개요](video-indexer-overview.md)
- [Insights](video-indexer-output-json-v2.md)
