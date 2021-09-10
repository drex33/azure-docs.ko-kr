---
title: Face REST API 빠른 시작
description: cURL과 함께 Face REST API를 사용하여 얼굴을 감지하고, 유사 얼굴을 찾고(이미지별 얼굴 검색), 얼굴을 식별하고(얼굴 인식 검색), 얼굴 데이터를 마이그레이션합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 6520c9514ee8fbf950437026c1d2712683e58151
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/19/2021
ms.locfileid: "122442508"
---
Face REST API를 사용하여 얼굴 인식을 시작합니다. Face 서비스는 이미지에서 사람의 얼굴을 감지하고 인식하기 위한 고급 알고리즘에 대한 액세스를 제공합니다.

Face REST API를 사용하여 다음을 수행합니다.

* [얼굴 감지 및 분석](#detect-and-analyze-faces)
* [유사 얼굴 찾기](#find-similar-faces)

> [!NOTE]
> 이 빠른 시작에서는 cURL 명령을 사용하여 REST API를 호출합니다. 프로그래밍 언어를 사용하여 REST API를 호출할 수도 있습니다. [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest), [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest)의 예는 GitHub 샘플을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [!INCLUDE [contributor-requirement](../../../includes/quickstarts/contributor-requirement.md)]
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 </a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 생성한 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* [PowerShell 버전 6.0 이상](/powershell/scripting/install/installing-powershell-core-on-windows) 또는 유사한 명령줄 애플리케이션.


## <a name="detect-and-analyze-faces"></a>얼굴 감지 및 분석

다음과 같은 명령을 사용하여 Face API를 호출하고 이미지에서 얼굴 특성 데이터를 가져옵니다. 먼저, 텍스트 편집기에 코드를 복사합니다. 코드를 명령의 특정 부분으로 변경해야 실행할 수 있습니다.&mdash;

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_3":::

다음과 같이 변경합니다.
1. `Ocp-Apim-Subscription-Key`를 유효한 Face 구독 키에 할당합니다.
1. 쿼리 URL의 첫 번째 부분을 구독 키에 해당하는 엔드포인트에 맞게 변경합니다.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 필요에 따라 다른 이미지를 가리키도록 요청의 본문에서 URL을 변경합니다.

변경한 후에는 명령 프롬프트를 열고 새 명령을 입력합니다. 

### <a name="examine-the-results"></a>결과 검사

콘솔 창에 얼굴 정보가 JSON 데이터로 표시됩니다. 예를 들면 다음과 같습니다.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>얼굴 특성 가져오기
 
얼굴 특성을 추출하려면 Detect API를 다시 호출하되, `detectionModel`을 `detection_01`로 설정합니다. `returnFaceAttributes` 쿼리 매개 변수도 추가합니다. 이제 명령은 다음과 같이 표시됩니다. 이전과 같이 Face 구독 키와 엔드포인트를 삽입합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>결과 검사

이제 반환된 얼굴 정보에 얼굴 특성이 포함됩니다. 예를 들면 다음과 같습니다.

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>유사 얼굴 찾기

이 작업에서는 감지된 하나의 얼굴(원본)을 가져오고, 다른 얼굴(대상) 세트를 검색하여 일치 항목을 찾습니다(이미지별 얼굴 검색). 일치 항목을 찾으면 일치하는 얼굴의 ID를 콘솔에 출력합니다.

### <a name="detect-faces-for-comparison"></a>비교할 얼굴 감지

먼저 이미지에서 얼굴을 감지해야 이를 비교할 수 있습니다. [감지 및 분석](#detect-and-analyze-faces) 섹션에서와 같이 이 명령을 실행합니다. 이 검색 방법은 비교 작업에 최적화되어 있습니다. 위의 섹션과 같이 자세한 얼굴 특성을 추출하지 않으며 다른 검색 모델을 사용합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

JSON 응답에서 `"faceId"` 값을 찾아 임시 위치에 저장합니다. 그런 다음, 이러한 다른 이미지 URL에 대해 위의 명령을 다시 호출하고 얼굴 ID도 저장합니다. 이러한 ID를 대상 얼굴 그룹으로 사용하여 비슷한 얼굴을 찾습니다.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

마지막으로 매칭에 사용할 단일 원본 얼굴을 검색하고 ID를 저장합니다. 이 ID는 다른 ID와 구분하여 보관합니다.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>일치 항목 찾기

다음 명령을 텍스트 편집기에 복사합니다.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

그런 다음, 다음과 같이 변경합니다.
1. `Ocp-Apim-Subscription-Key`를 유효한 Face 구독 키에 할당합니다.
1. 쿼리 URL의 첫 번째 부분을 구독 키에 해당하는 엔드포인트에 맞게 변경합니다.

`body` 값에 다음 JSON 콘텐츠를 사용합니다.

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. `"faceId"`에 원본 얼굴 ID를 사용합니다.
1. 다른 얼굴 ID를 `"faceIds"` 배열의 용어로 붙여 넣습니다.

### <a name="examine-the-results"></a>결과 검사

쿼리 얼굴과 일치하는 얼굴의 ID를 나열하는 JSON 응답을 받게 됩니다.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>리소스 정리

Cognitive Services 구독을 정리하고 제거하려면 리소스나 리소스 그룹을 삭제하면 됩니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Face REST API를 사용하여 기본 얼굴 인식 작업을 수행하는 방법을 알아보았습니다. 다음으로, 다양한 얼굴 감지 모델과 사용 사례에 적합한 모델을 지정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [얼굴 감지 모델 버전 지정](../../Face-API-How-to-Topics/specify-detection-model.md)

* [Face 서비스란?](../../overview.md)