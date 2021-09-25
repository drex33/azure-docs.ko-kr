---
title: Azure Video Analyzer for Media(이전의 Video Indexer)를 사용하여 음성 언어 자동 식별 - Azure
description: 이 문서에서는 Video Analyzer for Media(이전의 Video Indexer) 언어 식별 모델을 사용하여 비디오에서 음성 언어를 자동으로 식별하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 04/12/2020
ms.author: ellbe
ms.openlocfilehash: b1cb45b206fab5a7aaa887f66c908b524fa9ba93
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128561570"
---
# <a name="automatically-identify-the-spoken-language-with-language-identification-model"></a>언어 식별 모델을 사용하여 음성 언어 자동 식별

Video Analyzer for Media(이전의 Video Indexer)는 오디오에서 음성 언어 콘텐츠를 자동으로 식별하고 주요 식별 언어로 전사할 미디어 파일을 보내는 프로세스인 LID(자동 언어 식별)을 지원합니다. 

현재 LID는 영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(간체), 일본어, 러시아어 및 포르투갈어(브라질)를 지원합니다. 

아래 [지침 및 제한 사항](#guidelines-and-limitations) 섹션을 검토해야 합니다.

## <a name="choosing-auto-language-identification-on-indexing"></a>인덱싱할 때 자동 언어 식별 선택

API를 사용하여 비디오를 인덱싱하거나 [다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)할 때 `sourceLanguage` 매개 변수에서 `auto detect` 옵션을 선택합니다.

포털을 사용하는 경우 [Video Analyzer for Media](https://www.videoindexer.ai/) 홈페이지의 **계정 비디오** 로 이동하여 다시 인덱싱하려는 비디오의 이름을 마우스로 가리킵니다. 오른쪽 아래 모서리에서 다시 인덱싱 단추를 클릭합니다. **비디오 다시 인덱싱** 대화 상자의 **비디오 원본 언어** 드롭다운 상자에서 *자동 검색* 을 선택합니다.

![자동 검색](./media/language-identification-model/auto-detect.png)

## <a name="model-output"></a>모델 출력

Video Analyzer for Media는 해당 언어에 대한 신뢰도가 `> 0.6`인 경우 가장 가능성이 높은 언어에 따라 비디오를 전사합니다. 언어를 확실하게 식별할 수 없는 경우 Video Indexer는 음성 언어를 영어로 가정합니다. 

모델 주요 언어는 인사이트 JSON에서 `sourceLanguage` 특성(루트/비디오/인사이트 아래)으로 사용할 수 있습니다. 해당 신뢰도 점수는 `sourceLanguageConfidence` 특성에서 사용할 수도 있습니다.

```json
"insights": {
        "version": "1.0.0.0",
        "duration": "0:05:30.902",
        "sourceLanguage": "fr-FR",
        "language": "fr-FR",
        "transcript": [...],
        . . .
        "sourceLanguageConfidence": 0.8563
      },
```

## <a name="guidelines-and-limitations"></a>지침 및 제한 사항

* LID(자동 언어 식별)는 다음 언어를 지원합니다. 

    영어, 스페인어, 프랑스어, 독일어, 이탈리아어, 중국어(간체), 일본어, 러시아어 및 포르투갈어(브라질)
* Video Analyzer for Media는 아랍어(최신 표준 및 레반틴), 힌디어 및 한국어를 지원하지만 이러한 언어는 LID에서 지원되지 않습니다.
* 오디오에 위의 지원되는 목록 이외의 언어가 포함된 경우 예기치 못한 결과가 발생합니다.
* Video Analyzer for Media가 충분히 높은 신뢰도(`>0.6`)로 언어를 식별할 수 없는 경우 대체 언어는 영어입니다.
* 혼합 언어 오디오가 포함된 파일은 현재 지원되지 않습니다. 오디오에 혼합된 언어가 포함되어 있으면 예기치 않은 결과가 발생합니다. 
* 저품질 오디오는 모델 결과에 영향을 미칠 수 있습니다.
* 모델에는 오디오에서 1분 이상의 음성이 필요합니다.
* 이 모델은 자연스러운 대화 음성(음성 명령, 노래 등이 아님)을 인식하도록 설계되었습니다.

## <a name="next-steps"></a>다음 단계

* [개요](video-indexer-overview.md)
* [자동으로 다국어 콘텐츠 식별 및 전사](multi-language-identification-transcription.md)
