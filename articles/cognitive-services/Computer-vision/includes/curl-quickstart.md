---
title: '빠른 시작: 광학 인식 REST API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 광학 인식 REST API로 시작합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceeb1804c9332d9e0d3e11336ff92e8aacc8516c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585304"
---
광학 인식 REST API를 사용하여 인쇄 및 필기 텍스트를 읽을 수 있습니다.

> [!NOTE]
> 이 빠른 시작에서는 cURL 명령을 사용하여 REST API를 호출합니다. 프로그래밍 언어를 사용하여 REST API를 호출할 수도 있습니다. [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST), [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST)의 예는 GitHub 샘플을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/) 
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="Computer Vision 리소스 만들기"  target="_blank">Computer Vision 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
  * 애플리케이션을 Computer Vision 서비스에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
  * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* [cURL](https://curl.haxx.se/) 설치


## <a name="extract-printed-and-handwritten-text"></a>인쇄 및 필기 텍스트 추출

OCR 서비스는 이미지나 문서에서 보이는 텍스트를 추출하여 문자 스트림으로 변환할 수 있습니다. 텍스트 추출에 대한 자세한 내용은 [OCR(광학 인식) 개요](../overview-ocr.md)를 참조하세요.


### <a name="call-the-read-api"></a>읽기 API 호출

샘플을 만들고 실행하려면 다음 단계를 수행합니다.

1. 다음 명령을 텍스트 편집기에 복사합니다.
1. 필요한 경우 명령에서 다음 내용을 변경합니다.
    1. `<subscriptionKey>`의 값을 구독 키로 바꿉니다.
    1. 요청 URL(`westcentralus`)의 첫 번째 부분을 고유한 엔드포인트 URL의 텍스트로 바꿉니다.
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. 필요한 경우 요청 본문의 이미지 URL(`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`)을 분석할 다른 이미지의 URL로 변경합니다.
1. 명령 프롬프트 창을 엽니다.
1. 텍스트 편집기에서 명령 프롬프트 창으로 명령을 붙여넣은 후 명령을 실행합니다.

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

응답에는 값이 고유한 URL인 `Operation-Location` 헤더가 포함됩니다. 이 URL을 사용하여 읽기 작업의 결과를 쿼리할 수 있습니다. URL은 48시간 후에 만료됩니다.

### <a name="how-to-use-preview-features"></a>미리 보기 기능을 사용하는 방법
미리 보기 언어 및 기능의 경우, 최신 미리 보기를 사용하려면 [모델 버전을 지정하는 방법](../Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional)을 참조하세요. 미리 보기 모델에는 현재 GA 언어 및 기능에 대한 향상된 기능이 포함되어 있습니다.

### <a name="get-read-results"></a>읽기 결과 가져오기

1. 다음 명령을 텍스트 편집기에 복사합니다.
1. URL을 이전 단계에서 복사한 `Operation-Location` 값으로 바꿉니다.
1. 필요한 경우 명령에서 다음 내용을 변경합니다.
    1. `<subscriptionKey>`의 값을 구독 키로 바꿉니다.
1. 명령 프롬프트 창을 엽니다.
1. 텍스트 편집기에서 명령 프롬프트 창으로 명령을 붙여넣은 후 명령을 실행합니다.

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>응답 검사

성공적인 응답이 JSON을 통해 반환됩니다. 애플리케이션 예제는 다음 예제와 유사하게 명령 프롬프트 창에서 성공한 응답을 구문 분석하고 표시합니다.

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Read REST API를 호출하는 방법을 알아보았습니다. 다음으로, Read API 기능에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
>[읽기 API 호출](../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 개요](../overview-ocr.md)
