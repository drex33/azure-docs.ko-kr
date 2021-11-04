---
title: 이미지 조정 - Content Moderator
titleSuffix: Azure Cognitive Services
description: Content Moderator의 머신 지원 이미지 조정 및 휴먼 참여형 검토 도구를 사용하여 성인 및 외설 콘텐츠에 대한 이미지를 조정합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.openlocfilehash: 63273fc1d4da85d34bdad22f2c83890dbd137f4e
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458440"
---
# <a name="learn-image-moderation-concepts"></a>이미지 조정 개념 알아보기

Content Moderator의 컴퓨터 지원 이미지 조정 및 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md)를 사용하여 성인 및 외설 콘텐츠에 대한 이미지를 조정합니다. 텍스트 콘텐츠에 대한 이미지를 검색하고 해당 텍스트를 추출한 후 얼굴을 감지합니다. 사용자 지정 목록에서 이미지와 일치하는 사용자를 검색한 후 후속 조치를 취할 수 있습니다.

## <a name="evaluating-for-adult-and-racy-content"></a>성인 및 외설 콘텐츠 평가

**평가** 작업은 0과 1 사이의 신뢰도 점수를 반환합니다. true 또는 false에 해당하는 부울 데이터도 반환합니다. 이러한 값은 이미지에 잠재적인 성인 또는 외설 콘텐츠가 포함될지 여부를 예측합니다. 이 API로 이미지(파일 또는 URL)를 호출하면 반환된 응답에 다음 정보가 포함됩니다.

```json
"ImageModeration": {
    .............
    "adultClassificationScore": 0.019196987152099609,
    "isImageAdultClassified": false,
    "racyClassificationScore": 0.032390203326940536,
    "isImageRacyClassified": false,
    ............
    ],
```

> [!NOTE]
> 
> - `isImageAdultClassified`는 잠재적으로 특정 상황에서 성적으로 노골적이거나 성인용으로 간주될 수 있는 이미지가 있음을 나타냅니다.
> - `isImageRacyClassified`는 잠재적으로 특정 상황에서 성적으로 외설적이거나 도발적인 것으로 간주될 수 있는 이미지가 있음을 나타냅니다.
> - 점수는 0과 1 사이입니다. 점수가 높을수록 모델이 예측하는 범주에 해당할 가능성이 높아집니다. 이 미리 보기는 수동으로 코딩된 결과가 아닌 통계 모델을 사용합니다. 고유한 콘텐츠로 테스트하여 각 범주가 요구 사항과 얼마나 일치하는지 확인하는 것이 좋습니다.
> - 부울 값은 내부 점수 임계값에 따라 true 또는 false입니다. 고객은 이 값을 사용할지 또는 콘텐츠 정책에 따라 사용자 지정 임계값을 결정할지를 평가해야 합니다.

## <a name="detecting-text-with-optical-character-recognition-ocr"></a>OCR(광학 문자 인식)을 사용하여 텍스트 감지

**OCR(광학 문자 인식)** 작업은 이미지에서 텍스트 콘텐츠의 존재 여부를 예측하고 텍스트 조정을 위해 추출합니다. 언어를 지정할 수 있습니다. 언어를 지정하지 않으면 기본적으로 영어가 검색됩니다.

응답에는 다음과 같은 정보가 포함됩니다.
- 원본 텍스트
- 검색된 텍스트 요소와 해당 신뢰도 점수

예제 추출:

```json
"TextDetection": {
    "status": {
        "code": 3000.0,
        "description": "OK",
        "exception": null
    },
    .........
    "language": "eng",
    "text": "IF WE DID \r\nALL \r\nTHE THINGS \r\nWE ARE \r\nCAPABLE \r\nOF DOING, \r\nWE WOULD \r\nLITERALLY \r\nASTOUND \r\nOURSELVE \r\n",
    "candidates": []
},
```

## <a name="detecting-faces"></a>얼굴 감지

얼굴을 검색하면 이미지에 있는 얼굴 같은 개인 정보를 검색할 수 있습니다. 각 이미지에서 잠재적인 얼굴 및 얼굴 수를 감지합니다.

응답에는 다음 정보가 포함됩니다.

- 얼굴 수
- 감지된 얼굴의 위치 목록

예제 추출:

```json
"FaceDetection": {
    ......
    "result": true,
    "count": 2,
    "advancedInfo": [
        .....
    ],
    "faces": [
        {
            "bottom": 598,
            "left": 44,
            "right": 268,
            "top": 374
        },
        {
            "bottom": 620,
            "left": 308,
            "right": 532,
            "top": 396
        }
    ]
}
```

## <a name="creating-and-managing-custom-lists"></a>사용자 지정 목록 만들기 및 관리

많은 온라인 커뮤니티에서 사용자가 이미지 또는 다른 형식의 콘텐츠를 업로드한 후에, 다음 며칠, 몇 주 및 몇 개월 동안 불쾌감을 주는 항목이 여러 번 공유될 수 있습니다. 같은 이미지 또는 이미지가 약간 수정된 버전을 여러 위치에서 반복적으로 검색하고 걸러내는 데 드는 비용은 매우 크며 오류가 발생하기 쉬울 수 있습니다.

동일한 이미지를 여러 번 조정하는 대신, 차단된 콘텐츠의 사용자 지정 목록에 불쾌감을 주는 이미지를 추가합니다. 이러한 방식으로 콘텐츠 조정 시스템은 들어오는 이미지를 사용자 지정 목록과 비교하고 추가 처리를 중지합니다.

> [!NOTE]
> 최대 **5개 이미지 목록** 으로 제한되고, 각 목록은 **10,000개 이미지를 초과하지 않아야** 합니다.
>

Content Moderator는 사용자 지정 이미지 목록을 관리하기 위한 작업을 포함하는 전체 [이미지 목록 관리 API](try-image-list-api.md)를 제공합니다. [이미지 목록 API 콘솔](try-image-list-api.md)에서 시작하고 REST API 코드 샘플을 사용합니다. Visual Studio 및 C#에 익숙한 경우 [이미지 목록 .NET 빠른 시작](image-lists-quickstart-dotnet.md)도 확인합니다.

## <a name="matching-against-your-custom-lists"></a>사용자 지정 목록에 대해 일치하는지 확인

일치 작업을 사용하면 목록 작업을 사용하여 만들어지고 관리되는 사용자 지정 목록을 기준으로 들어오는 이미지의 유사 일치를 수행할 수 있습니다.

일치하는 항목이 있으면 일치하는 이미지의 식별자와 조정 태그가 반환됩니다. 응답에는 다음 정보가 포함됩니다.

- 일치 점수(0과 1 사이)
- 일치하는 이미지
- 이미지 태그(이전 조정 동안 할당됨)
- 이미지 레이블

예제 추출:

```json
{
    ..............,
    "IsMatch": true,
    "Matches": [
        {
            "Score": 1.0,
            "MatchId": 169490,
            "Source": "169642",
            "Tags": [],
            "Label": "Sports"
        }
    ],
    ....
}
```

## <a name="review-tool"></a>검토 도구

좀 더 미묘한 차이가 있는 경우 Content Moderator의 [검토 도구](Review-Tool-User-Guide/human-in-the-loop.md) 및 해당 API를 사용하여 중재자가 검토할 수 있게 조정 결과 및 콘텐츠를 제공합니다. 컴퓨터 할당 태그를 검토하고 최종 결정을 확인합니다.

![사용자 중재자를 위한 이미지 검토](images/moderation-reviews-quickstart-dotnet.PNG)

## <a name="next-steps"></a>다음 단계

[이미지 조정 API 콘솔](try-image-api.md)을 시험 사용하고 REST API 코드 샘플을 사용합니다. 또한 사용자 검토를 설정하는 방법을 알아보려면 [검토, 워크플로 및 작업](./review-api.md)을 참조하세요.
