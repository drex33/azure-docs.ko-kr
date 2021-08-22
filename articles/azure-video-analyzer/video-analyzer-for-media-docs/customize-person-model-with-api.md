---
title: Azure Video Analyzer for Media(이전의 Video Indexer) API를 사용하여 사람 모델 사용자 지정
titleSuffix: Azure Video Analyzer for Media
description: Azure Video Analyzer for Media(이전의 Video Indexer) API를 사용하여 사람 모델을 사용자 지정하는 방법에 대해 알아봅니다.
services: azure-video-analyzer
author: anikaz
manager: johndeu
ms.topic: article
ms.subservice: azure-video-analyzer-media
ms.date: 01/14/2020
ms.author: kumud
ms.openlocfilehash: 01205b70b3f4195be290075727bece30d57c515e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2021
ms.locfileid: "112121394"
---
# <a name="customize-a-person-model-with-the-video-analyzer-for-media-api"></a>Video Analyzer for Media API를 사용하여 사람 모델 사용자 지정

Azure Video Analyzer for Media(이전의 Video Indexer)는 비디오 콘텐츠에 대한 얼굴 감지 및 유명인 인식을 지원합니다. 유명인 인식 기능은 IMDB, Wikipedia, 상위 LinkedIn 영향력 행사자 등 일반적으로 요청되는 데이터 원본을 기준으로 하는 약 백만 개의 얼굴을 처리합니다. 유명인 인식 기능으로 인식되지 않는 얼굴은 감지되지만 이름 없이 유지됩니다. Video Analyzer for Media에 비디오를 업로드하고 결과를 얻은 후 돌아가서 인식되지 않은 얼굴에 이름을 지정할 수 있습니다. 얼굴에 이름 레이블을 지정하면 얼굴과 이름이 계정의 개인 모델에 추가됩니다. 그러면 Video Analyzer for Media는 향후 비디오와 과거 비디오에서 이 얼굴을 인식합니다.

Video Analyzer for Media API를 사용하여 이 항목에 설명된 대로 비디오에서 감지된 얼굴을 편집할 수 있습니다. [Video Analyzer for Media 웹 사이트에서 사람 모델 사용자 지정](customize-person-model-with-api.md)에 설명된 대로 Video Analyzer for Media 웹 사이트를 사용할 수도 있습니다.

## <a name="managing-multiple-person-models"></a>여러 개의 개인 모델 관리

Video Analyzer for Media는 계정당 여러 사람 모델을 지원합니다. 이 기능은 현재 Video Analyzer for Media API를 통해서만 사용할 수 있습니다.

계정이 다른 사용 사례 시나리오를 지원하는 경우 계정당 여러 개의 개인 모델을 만드는 것이 좋습니다. 예를 들어 콘텐츠가 스포츠와 관련된 경우 각 스포츠(미식 축구, 농구, 축구 등)에 대해 별도의 개인 모델을 만들 수 있습니다.

모델이 생성되면, 비디오를 업로드/인덱싱하거나 다시 인덱싱할 때 특정 개인 모델의 모델 ID를 제공하여 모델을 사용할 수 있습니다. 비디오에 대해 새로운 얼굴을 학습하면 비디오와 연결된 특정 사용자 지정 모델이 업데이트됩니다.

각 계정이 50개의 개인 모델로 제한됩니다. 다중 개인 모델 지원이 필요하지 않은 경우, 업로드/인덱싱하거나 다시 인덱싱할 때 비디오에 개인 모델 ID를 할당하지 마세요. 이 경우 Video Analyzer for Media는 계정의 기본 사용자 지정 개인 모델을 사용합니다.

## <a name="create-a-new-person-model"></a>새 개인 모델 만들기

지정된 계정에 새 개인 모델을 만들려면 [개인 모델 만들기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Person-Model) API를 사용합니다.

응답은 방금 만든 개인 모델의 이름 및 생성된 모델 ID를 아래 예제 형식으로 제공합니다.

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

그런 다음, [인덱싱할 비디오를 업로드](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)하거나 [비디오를 다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)할 때 **personModelId** 매개 변수의 **ID** 값을 사용합니다.

## <a name="delete-a-person-model"></a>개인 모델 삭제

지정된 계정에서 사용자 지정 개인 모델을 삭제하려면 [개인 모델 삭제](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Delete-Person-Model) API를 사용합니다.

개인 모델을 성공적으로 삭제한 후에도 삭제된 모델을 사용 중인 현재 비디오의 인덱스는 다시 인덱싱할 때까지 변경되지 않습니다. 다시 인덱싱하면 Video Analyzer for Media는 해당 모델을 사용하여 인덱싱된 현재 비디오에서 삭제된 모델에 이름이 지정된 얼굴을 인식하지 못하지만 이러한 얼굴은 계속 감지됩니다. 삭제된 모델을 사용하여 인덱싱된 현재 비디오는 이제 계정의 기본 개인 모델을 사용합니다. 삭제된 모델의 얼굴이 계정의 기본 모델에서도 이름이 지정된 경우에는 해당 얼굴이 비디오에서 계속 인식됩니다.

개인 모델을 성공적으로 삭제할 경우 반환되는 콘텐츠가 없습니다.

## <a name="get-all-person-models"></a>모든 개인 모델 가져오기

지정된 계정의 모든 개인 모델을 얻으려면 [개인 모델 가져오기](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Person-Models) API를 사용합니다.

응답은 사용자 계정에 있는 모든 개인 모델(지정된 계정의 기본 개인 모델 포함)의 목록과 각 이름 및 ID를 아래 예제의 형식으로 제공합니다.

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

[인덱싱할 비디오를 업로드](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Upload-Video)하거나 [비디오를 다시 인덱싱](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video)할 때 `personModelId` 매개 변수에 대한 개인 모델의 `id` 값을 사용하여 비디오에 사용할 모델을 선택할 수 있습니다.

## <a name="update-a-face"></a>얼굴 업데이트

이 명령을 사용하면 비디오 ID와 얼굴 ID를 사용하는 이름으로 비디오의 얼굴을 업데이트할 수 있습니다. 그러면 작업이 업로드/인덱싱하거나 다시 인덱싱할 때 비디오와 연결된 개인 모델이 업데이트됩니다. 개인 모델이 할당되지 않은 경우 계정의 기본 개인 모델이 업데이트됩니다.

그런 다음 시스템은 동일한 인물 모델을 공유하는 다른 현재 비디오에서 동일한 얼굴의 발생을 인식합니다. 다른 현재 비디오의 얼굴 인식은 배치 프로세스이므로 적용되는 데 다소 시간이 걸릴 수 있습니다.

Video Analyzer for Media가 유명인으로 인식한 얼굴을 새 이름으로 업데이트할 수 있습니다. 사용자가 지정한 새 이름이 기본 제공 유명인 인식보다 우선 적용됩니다.

얼굴을 업데이트하려면 [비디오 얼굴 업데이트](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Face) API를 사용합니다.

이름은 개인 모델에서 고유하므로 동일한 개인 모델에 있는 두 개의 다른 얼굴에 동일한 `name` 매개 변수 값을 제공하는 경우 Video Analyzer for Media는 얼굴을 동일한 개인으로 보고, 비디오가 다시 인덱싱될 때 통합합니다.

## <a name="next-steps"></a>다음 단계

[Video Analyzer for Media 웹 사이트에서 사람 모델 사용자 지정](customize-person-model-with-website.md)
