---
title: 이미지 분석 API 호출
titleSuffix: Azure Cognitive Services
description: 이미지 분석 API를 호출하고 해당 동작을 구성하는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 46334015d932f667e5bbb7a10153e9b70aaa0764
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132058602"
---
# <a name="call-the-image-analysis-api"></a>이미지 분석 API 호출

이 문서에서는 이미지 분석 API를 호출하여 이미지의 시각적 기능에 대한 정보를 반환하는 방법을 보여 줍니다.

이 가이드에서는 사용자가 이미 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스를 생성"  target="_blank">Computer Vision 리소스를 생성했으며 </a> 구독 키와 엔드포인트 URL을 획득했다고 가정합니다. 아직 시작하지 않았다면 [빠른 시작](../quickstarts-sdk/image-analysis-client-library.md)을 따라 시작하세요.
  
## <a name="submit-data-to-the-service"></a>서비스에 데이터 제출

분석 API에 로컬 이미지 또는 원격 이미지를 제출합니다. 로컬의 경우 HTTP 요청 본문에 이진 이미지 데이터를 저장합니다. 원격의 경우 `{"url":"http://example.com/images/test.jpg"}` 같이 요청 본문의 형식을 지정하여 이미지의 URL을 지정합니다.

## <a name="determine-how-to-process-the-data"></a>데이터 처리 방법 결정

###  <a name="select-visual-features"></a>시각적 기능 선택

[분석 API](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b)를 통해 모든 서비스의 이미지 분석 기능에 액세스할 수 있습니다. URL 쿼리 매개 변수를 설정하여 사용하려는 기능을 지정해야 합니다. 매개 변수는 쉼표로 구분된 여러 값을 가질 수 있습니다. 지정하는 기능마다 추가 계산 시간이 필요하므로 필요한 항목만 지정하세요.

|URL 매개 변수 | 값 | Description|
|---|---|--|
|`visualFeatures`|`Adult` | 이미지가 본질적으로 음란물인지(노출 또는 성적 행위 묘사), 잔혹한지(지나친 폭력 또는 유혈 묘사) 감지합니다. 성적으로 노골적인 콘텐츠(즉, 외설 콘텐츠)도 감지됩니다.|
||`Brands` | 이미지 내에서 대략적인 위치를 포함한 다양한 브랜드를 감지합니다. Brands 인수는 영어로만 사용할 수 있습니다.|
||`Categories` | 설명서에 정의된 분류에 따라 이미지 콘텐츠를 분류합니다. 이것이 `visualFeatures`의 기본값입니다.|
||`Color` | 강조 색, 기준 색, 이미지가 흑백인지 여부를 결정합니다.|
||`Description` | 지원되는 언어를 사용하여 완전한 문장으로 이미지 콘텐츠를 설명합니다.|
||`Faces` | 얼굴이 있는지 여부를 감지합니다. 얼굴이 있으면 좌표, 성별 및 나이를 생성합니다.|
||`ImageType` | 이미지가 클립 아트인지 또는 선 그리기인지 감지합니다.|
||`Objects` | 이미지 내에서 대략적인 위치를 포함한 다양한 개체를 감지합니다. Objects 인수는 영어로만 사용할 수 있습니다.|
||`Tags` | 이미지 콘텐츠와 관련된 단어의 자세한 목록으로 이미지에 태그를 지정합니다.|
|`details`| `Celebrities` | 이미지에서 감지할 수 있는 유명인을 식별합니다.|
||`Landmarks` |이미지에서 감지할 수 있는 랜드마크를 식별합니다.|

채워진 URL은 다음과 같습니다.

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>언어 지정

반환되는 데이터의 언어를 지정할 수도 있습니다. 다음 URL 쿼리 매개 변수는 언어를 지정합니다. 기본값은 `en`입니다.

|URL 매개 변수 | 값 | Description|
|---|---|--|
|`language`|`en` | 영어|
||`es` | 스페인어|
||`ja` | 일본어|
||`pt` | 포르투갈어|
||`zh` | 중국어(간체)|

채워진 URL은 다음과 같습니다.

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **범위가 지정된 API 호출**
>
> 이미지 분석 기능 중 일부는 직접 호출하거나 분석 API 호출을 통해 호출할 수 있습니다. 예를 들어, `https://{endpoint}/vision/v3.2/tag`에 요청을 보내 이미지 태그로 범위가 지정된 분석을 수행할 수 있습니다. 별도로 호출할 수 있는 다른 기능은 [참조 설명서](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b)를 확인하세요.

## <a name="get-results-from-the-service"></a>서비스에서 결과 가져오기

이 서비스는 `200` HTTP 응답을 반환하며 본문에는 반환된 데이터가 JSON 문자열 형식으로 포함됩니다. 성공적인 JSON 응답의 예제는 다음과 같습니다.

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

이 예제의 필드에 대한 설명은 다음 표를 참조하세요.

필드 | Type | 콘텐츠
------|------|------|
태그들  | `object` | 태그 배열의 최상위 개체입니다.
tags[].Name | `string`    | 태그 분류자의 키워드입니다.
tags[].Score    | `number`    | 0에서 1 사이의 신뢰도 점수입니다.
description     | `object`    | 이미지 설명의 최상위 개체입니다.
description.tags[] |    `string`    | 태그 목록입니다. 캡션을 생성하는 기능에 대한 신뢰도가 부족한 경우 태그는 호출자에게 제공되는 유일한 정보일 수 있습니다.
description.captions[].text    | `string`    | 이미지를 설명하는 구.
description.captions[].confidence    | `number`    | 구의 신뢰도 점수입니다.

### <a name="error-codes"></a>오류 코드

가능한 오류 및 오류의 원인에 대한 다음 목록을 참조하세요.

* 400
    * InvalidImageUrl - 이미지 URL의 형식이 잘못되었거나 액세스할 수 없습니다.
    * InvalidImageFormat - 입력 데이터가 유효한 이미지가 아닙니다.
    * InvalidImageSize - 입력 이미지가 너무 큽니다.
    * NotSupportedVisualFeature - 지정된 기능 유형이 유효하지 않습니다.
    * NotSupportedImage - 지원되지 않는 이미지입니다(예: 아동 포르노).
    * InvalidDetails - 지원되지 않는 `detail` 매개 변수 값입니다.
    * NotSupportedLanguage - 요청된 작업이 지정된 언어로 지원되지 않습니다.
    * BadArgument - 오류 메시지에 추가 세부 정보가 제공됩니다.
* 415 - 지원되지 않는 미디어 형식 오류입니다. Content-Type이 허용되는 형식이 아닙니다.
    * 이미지 URL의 경우: Content-Type이 application/json이어야 합니다.
    * 이진 이미지 데이터의 경우: Content-Type이 application/octet-stream 또는 multipart/form-data여야 합니다.
* 500
    * FailedToProcess
    * Timeout - 이미지 처리 시간이 초과되었습니다.
    * InternalServerError

> [!TIP]
> Computer Vision을 사용하는 동안 서비스에 적용된 [요금 제한](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/)에 의한 일시적인 오류나 네트워크 중단과 같은 다른 일시적인 문제가 발생할 수 있습니다. 이러한 유형의 오류를 처리하는 방법에 대한 내용은 클라우드 디자인 패턴 가이드의 [다시 시도 패턴](/azure/architecture/patterns/retry) 및 관련 [회로 차단기 패턴](/azure/architecture/patterns/circuit-breaker)을 참조하세요.


## <a name="next-steps"></a>다음 단계

REST API를 사용해 보려면 [이미지 분석 API 참조](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/56f91f2e778daf14a499f21b)로 이동합니다.