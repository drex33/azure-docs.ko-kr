---
title: Read API를 호출하는 방법
titleSuffix: Azure Cognitive Services
description: Read API를 호출하고 해당 동작을 자세히 구성하는 방법에 대해 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 03/31/2021
ms.author: pafarley
ms.openlocfilehash: d5a1da6bbe251e6200cd3a64117748e9ebbfae2a
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112298118"
---
# <a name="call-the-read-api"></a>읽기 API 호출

이 가이드에서는 Read API를 호출하여 이미지에서 텍스트를 추출하는 방법을 알아봅니다. 사용자 요구에 맞게 이 API의 동작을 구성할 수 있는 다양한 방법을 알아봅니다.

이 가이드에서는 사용자가 이미 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스를 생성"  target="_blank">Computer Vision 리소스를 생성했으며 </a> 구독 키와 엔드포인트 URL을 획득했다고 가정합니다. 아직 시작하지 않았다면 [빠른 시작](../quickstarts-sdk/client-library.md)을 따라 시작하세요.

## <a name="submit-data-to-the-service"></a>서비스에 데이터 제출

읽기 API에 로컬 이미지 또는 원격 이미지를 제출합니다. 로컬의 경우 HTTP 요청 본문에 이진 이미지 데이터를 저장합니다. 원격의 경우 `{"url":"http://example.com/images/test.jpg"}` 같이 요청 본문의 형식을 지정하여 이미지의 URL을 지정합니다.

Read API의 [Read 호출](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)은 이미지 또는 PDF 문서를 입력으로 사용하고 텍스트를 비동기적으로 추출합니다.

`https://{endpoint}/vision/v3.2/read/analyze[?language][&pages][&readingOrder]`

호출은 `Operation-Location`이라는 응답 헤더 필드와 함께 반환됩니다. `Operation-Location` 값은 다음 단계에서 사용할 작업 ID가 포함된 URL입니다.

|응답 헤더| 예제 값 |
|:-----|:----|
|Operation-Location | `https://cognitiveservice/vision/v3.2/read/analyzeResults/49a36324-fc4b-4387-aa06-090cfbf0064f` |

> [!NOTE]
> **Billing** 
>
> [Computer Vision 가격 책정](https://azure.microsoft.com/pricing/details/cognitive-services/computer-vision/) 페이지에는 읽기에 대한 가격 책정 계층이 포함되어 있습니다. 분석된 각 이미지 또는 페이지는 하나의 트랜잭션입니다. 100페이지가 포함된 PDF 또는 TIFF 문서를 사용하여 작업을 호출하면 읽기 작업은 100개의 트랜잭션으로 계산되며 100개의 트랜잭션에 대해 요금이 청구됩니다. 작업을 50번 호출하고 각 호출에서 100페이지의 문서를 제출한 경우 50 X 100 = 5000 트랜잭션에 대한 요금이 청구됩니다.

## <a name="determine-how-to-process-the-data"></a>데이터 처리 방법 결정

### <a name="language-specification"></a>언어 사양

[읽기](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005) 호출에는 언어에 대한 선택적 요청 매개 변수가 있습니다. 읽기는 자동 언어 식별 및 다국어 문서를 지원하므로 문서를 특정 언어로 강제 처리하려는 경우에만 언어 코드를 제공합니다.

### <a name="natural-reading-order-output-latin-languages-only"></a>자연스러운 읽기 순서 출력(라틴어만 해당)

`readingOrder` 쿼리 매개 변수를 사용하여 텍스트 줄이 출력되는 순서를 지정합니다. 다음 예제와 같이 인간 친화적인 읽기 순서 출력을 위해 `natural`을 사용합니다. 이 기능은 라틴어에 대해서만 지원됩니다.

:::image type="content" source="../Images/ocr-reading-order-example.png" alt-text="OCR 읽기 순서 예" border="true" :::

### <a name="select-pages-or-page-ranges-for-text-extraction"></a>텍스트 추출을 위한 페이지 또는 페이지 범위 선택

큰 다중 페이지 문서의 경우 `pages` 쿼리 매개 변수를 통해 페이지 번호 또는 페이지 범위를 지정하여 해당 페이지에서만 텍스트를 추출합니다. 다음 예제에서는 모든 페이지(1-10) 및 선택한 페이지(3-6) 모두에 대한 텍스트를 포함하는 10개의 페이지가 있는 문서를 보여줍니다.

:::image type="content" source="../Images/ocr-select-pages.png" alt-text="선택한 페이지 출력" border="true" :::

## <a name="get-results-from-the-service"></a>서비스에서 결과 가져오기

두 번째 단계는 [읽기 결과 가져오기](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d9869604be85dee480c8750) 작업을 호출하는 것입니다. 이 작업은 읽기 작업에서 만든 작업 ID를 입력으로 사용합니다.

`https://{endpoint}/vision/v3.2/read/analyzeResults/{operationId}`

다음과 같은 가능한 값을 가진 **상태** 필드가 포함된 JSON 응답을 반환합니다.

|값 | 의미 |
|:-----|:----|
| `notStarted`| 작업이 시작되지 않았습니다. |
| `running`| 작업이 처리 중입니다. |
| `failed`| 작업이 실패했습니다. |
| `succeeded`| 작업이 성공했습니다. |

**성공한** 값이 반환될 때까지 이 작업을 반복적으로 호출합니다. 1~2초의 간격을 사용하여 RPS(초당 요청) 속도를 초과하지 않도록 합니다.

> [!NOTE]
> 체험 계층은 요청 속도를 분당 20개의 호출로 제한합니다. 유료 계층은 요청에 따라 증가할 수 있는 10개의 RPS(초당 요청)를 허용합니다. Azure 리소스 식별자 및 지역을 확인하고 Azure 지원 티켓을 열거나 계정 팀에 문의하여 더 높은 RPS(초당 요청 수) 요금을 요청합니다.

**상태** 필드에 `succeeded` 값이 있는 경우 JSON 응답에는 이미지 또는 문서에서 추출된 텍스트 콘텐츠가 포함됩니다. JSON 응답은 인식된 단어의 원래 줄 그룹을 유지 관리합니다. 추출된 텍스트 줄과 해당 경계 상자 좌표가 포함됩니다. 각 텍스트 줄에는 해당 좌표와 신뢰도 점수가 있는 추출된 모든 단어가 포함됩니다.

> [!NOTE]
> `Read` 작업에 제출된 데이터는 일시적으로 암호화되어 짧은 기간 동안 저장된 후 삭제됩니다. 이를 통해 애플리케이션은 서비스 응답의 일부로 추출된 텍스트를 검색할 수 있습니다.

### <a name="sample-json-output"></a>샘플 JSON 출력

성공적인 JSON 응답의 다음 예제를 참조하세요.

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-02-04T06:32:08.2752706+00:00",
  "lastUpdatedDateTime": "2021-02-04T06:32:08.7706172+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 2.1243,
        "width": 502,
        "height": 252,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              58,
              42,
              314,
              59,
              311,
              123,
              56,
              121
            ],
            "text": "Tabs vs",
            "appearance": {
              "style": {
                "name": "handwriting",
                "confidence": 0.96
              }
            },
            "words": [
              {
                "boundingBox": [
                  68,
                  44,
                  225,
                  59,
                  224,
                  122,
                  66,
                  123
                ],
                "text": "Tabs",
                "confidence": 0.933
              },
              {
                "boundingBox": [
                  241,
                  61,
                  314,
                  72,
                  314,
                  123,
                  239,
                  122
                ],
                "text": "vs",
                "confidence": 0.977
              }
            ]
          }
        ]
      }
    ]
  }
}
```

### <a name="handwritten-classification-for-text-lines-latin-languages-only"></a>텍스트 줄에 대한 필기 분류(라틴어에만 해당)

응답에는 신뢰도 점수와 함께 각 텍스트 줄이 필기 스타일인지 여부를 분류하는 것이 포함됩니다. 이 기능은 라틴어에 대해서만 지원됩니다. 다음 예제에서는 이미지의 텍스트에 대한 필기 분류를 보여줍니다.

:::image type="content" source="../Images/ocr-handwriting-classification.png" alt-text="OCR 필기 분류 예" border="true" :::

## <a name="next-steps"></a>다음 단계

REST API를 사용해 보려면 [Read API 참조](https://centraluseuap.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2/operations/5d986960601faab4bf452005)로 이동하세요.
