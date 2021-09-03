---
title: 인식 모델을 지정하는 방법 - 얼굴
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face 애플리케이션에서 사용할 인식 모델을 선택하는 방법을 보여 줍니다.
services: cognitive-services
author: longli0
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: longl
ms.custom: devx-track-csharp
ms.openlocfilehash: 92e9c22712fdbfae5ab13a23cf72e282a225288a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535990"
---
# <a name="specify-a-face-recognition-model"></a>얼굴 인식 모델 지정

이 가이드에서는 Azure Face 서비스를 사용하여 얼굴 감지, 식별 및 유사성 검색을 위한 얼굴 인식 모델을 지정하는 방법을 보여 줍니다.

Face 서비스는 기계 학습 모델을 사용하여 이미지 속 사람 얼굴에 대한 작업을 수행합니다. 사용자 피드백에 따라 모델의 정확도를 지속적으로 향상시키고 연구를 진행하면서 이러한 향상된 기능을 모델 업데이트로 제공합니다. 개발자는 사용할 얼굴 인식 모델의 버전을 지정할 수 있습니다. 사용 사례에 가장 잘 맞는 모델을 선택할 수 있습니다.

Azure Face 서비스는 4가지 인식 모델을 제공합니다. 이러한 모델을 사용하여 만든 FaceList 또는 **PersonGroup** 을 사용하는 고객에게 이전 버전과의 호환성을 보장하기 위해 _recognition_01_(2017년에 게시됨), _recognition_02_(2019년에 게시됨) 및 _recognition_03_(2020년에 게시됨) 모델이 계속 지원됩니다. **FaceList** 또는 **Persongroup** 은 항상 생성 시 사용된 인식 모델을 사용하며, 새 얼굴이 추가되면 이 모델과 연결됩니다. 이는 생성 후 변경할 수 없으며, 고객은 **FaceList** 또는 **PersonGroup** 에 해당하는 인식 모델을 사용해야 합니다.

나중에 편리한 시간에 인식 모델로 이동할 수 있지만 선택한 인식 모델을 사용하여 새 FaceList 및 PersonGroup을 만들어야 합니다.

_recognition_04_ 모델(2021년에 게시됨)은 현재 사용할 수 있는 가장 정확한 모델입니다. 신규 고객인 경우 이 모델을 사용하는 것이 좋습니다. _Recognition_04_ 는 유사성 비교와 사람 일치 비교 모두에 대해 향상된 정확도를 제공합니다. _Recognition_04_ 는 얼굴 가리개(수술용 마스크, N95 마스크, 천 마스크)를 착용하고 있는 등록된 사용자에 대한 인식을 개선합니다. 이제 최신 _detection_03_ 모델을 사용하여 등록된 사용자가 얼굴 가리개를 착용하고 있는지 여부를 감지한 후 최신 _recognition_04_ 모델을 사용하여 사용자가 누구인지 인식하는 안전하고 원활한 사용자 환경을 빌드할 수 있습니다. 각 모델은 서로 독립적으로 작동하며, 한 모델에 설정된 신뢰도 임계값을 다른 인식 모델과 비교할 수 없습니다.

모델 충돌을 방지하면서 여러 Face 작업에서 선택한 모델을 지정하는 방법을 알아보려면 계속 읽어 보세요. 고급 사용자가 최신 모델로 전환해야 하는지 여부를 결정하려는 경우 [여러 모델 평가](#evaluate-different-models) 섹션으로 건너뛰어 새 모델을 평가하고 현재 데이터 세트를 사용하여 결과를 비교합니다.


## <a name="prerequisites"></a>필수 구성 요소

AI 얼굴 감지 및 식별의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 먼저 다음 가이드를 참조하세요.

* [얼굴 감지 개념](../concepts/face-detection.md)
* [얼굴 인식 개념](../concepts/face-recognition.md)
* [검색 API 호출](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 감지

얼굴 감지는 사람 얼굴의 시각적 랜드마크를 식별하고 경계 상자 위치를 찾습니다. 또한 얼굴의 요소를 추출하여 식별에 사용할 수 있도록 저장합니다. 이 모든 정보가 한 얼굴의 표현을 형성합니다.

인식 모델은 얼굴 요소가 추출될 때 사용되므로 Detect 작업을 수행할 때 모델 버전을 지정할 수 있습니다.

[Face - Detect] API를 사용할 때 `recognitionModel` 매개 변수를 사용하여 모델 버전을 할당합니다. 사용 가능한 값은
* recognition_01
* recognition_02
* recognition_03
* recognition_04


필요에 따라 _returnRecognitionModel_ 매개 변수(기본값 **false**)를 지정하여 응답에서 _recognitionModel_ 을 반환할지 여부를 나타낼 수 있습니다. 따라서 [Face - Detect] REST API에 대한 요청 URL은 다음과 같습니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용하는 경우 `recognitionModel` 버전을 나타내는 문자열을 전달하여 값을 할당할 수 있습니다. 할당되지 않은 상태로 두면 `recognition_01`의 기본 모델 버전이 사용됩니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, true, true, recognitionModel: "recognition_01", returnRecognitionModel: true);
```

## <a name="identify-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 식별

Face 서비스는 이미지에서 얼굴 데이터를 추출하고 [얼굴 추가](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API 호출 등을 통해 이를 **Person** 개체와 연결할 수 있으며, 여러 **Person** 개체를 **PersonGroup** 에 함께 저장할 수 있습니다. 그런 다음, [Face - Identify] 호출을 사용하여 **PersonGroup** 과 새 얼굴을 비교할 수 있으며, 해당 그룹 내에서 일치하는 사용자를 식별할 수 있습니다.

**PersonGroup** 에는 모든 **Person** 별로 고유한 인식 모델이 하나씩 있어야 하며, 그룹을 만들 때 `recognitionModel` 매개 변수를 사용하여 이를 지정할 수 있습니다([PersonGroup - Create] 또는 [LargePersonGroup - Create]). 이 매개 변수를 지정하지 않으면 원래 `recognition_01` 모델이 사용되지 않습니다. 그룹은 항상 생성 시 사용된 인식 모델을 사용하고, 새 얼굴은 추가될 때 이 모델에 연결됩니다. 그룹을 만든 후에는 이를 변경할 수 없습니다. **PersonGroup** 구성 시 사용된 모델을 확인하려면 _returnRecognitionModel_ 매개 변수가 **true** 로 설정된 [PersonGroup - Get] API를 사용합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
// Create an empty PersonGroup with "recognition_04" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");
```

이 코드에서는 ID가 `mypersongroupid`인 **PersonGroup** 이 생성되고, _recognition_04_ 모델을 사용하여 얼굴 요소를 추출하도록 설정됩니다.

이에 따라, [Face - Detect] API를 통해 이 **PersonGroup** 과 비교할 얼굴을 감지할 때 사용할 모델을 지정해야 합니다. 사용하는 모델은 항상 **PersonGroup** 의 구성과 일치해야 합니다. 그렇지 않으면 호환되지 않는 모델로 인해 작업이 실패합니다.

[Face - Identify] API에는 변경 내용이 없습니다. 검색 시 모델 버전을 지정하기만 하면 됩니다.

## <a name="find-similar-faces-with-specified-model"></a>지정된 모델을 사용하여 유사한 얼굴 찾기

유사성 검색을 위한 인식 모델을 지정할 수도 있습니다. [FaceList - Create] API 또는 [LargeFaceList - Create]를 사용하여 **FaceList** 를 만들 때 `recognitionModel`을 사용하여 모델 버전을 할당할 수 있습니다. 이 매개 변수를 지정하지 않으면 `recognition_01` 모델이 기본적으로 사용됩니다. **FaceList** 는 항상 생성 시 사용된 인식 모델을 사용하고, 새 얼굴은 목록에 추가될 때 이 모델에 연결됩니다. 생성 후에는 이를 변경할 수 없습니다. **FaceList** 구성 시 사용된 모델을 확인하려면 _returnRecognitionModel_ 매개 변수가 **true** 로 설정된 [FaceList - Get] API를 사용합니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");
```

이 코드는 요소 추출 시 _recognition_04_ 모델을 사용하여 `My face collection`이라는 **FaceList** 를 만듭니다. 이 **FaceList** 에서 새로 검색된 얼굴과 유사한 얼굴을 검색하면 해당 얼굴이 _recognition_04_ 모델을 사용하여 검색되어야 합니다([Face - Detect]). 이전 섹션과 마찬가지로 모델은 일관적이어야 합니다.

[Face - Identify] API에는 변경 내용이 없습니다. 검색 시 모델 버전을 지정하기만 하면 됩니다.

## <a name="verify-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 확인

[Face - Verify] API는 두 얼굴이 동일한 사람인지 여부를 확인합니다. Verify API에서 인식 모델과 관련된 변경 내용은 없지만 동일한 모델을 사용하여 검색된 얼굴만 비교할 수 있습니다.

## <a name="evaluate-different-models"></a>여러 모델 평가

사용자 고유의 데이터에 대한 여러 인식 모델의 성능을 비교하려는 경우 다음을 수행해야 합니다.
1. _recognition_01_, _recognition_02_, _recognition_03_ 및 _recognition_04_ 를 각각 사용하여 4개의 PersonGroup을 만듭니다.
1. 이미지 데이터를 사용하여 얼굴을 감지하고 이러한 4개의 **PersonGroup** 내에 있는 **Person** 에 등록합니다. 
1. PersonGroup – Train API를 사용하여 PersonGroup을 학습시킵니다.
1. **PersonGroup** 4개에서 Face - Identify로 테스트하고 결과를 비교합니다.


일반적으로 신뢰도 임계값(얼굴을 식별하는 모델의 신뢰도를 결정하는 0과 1 사이의 값)을 지정하는 경우 모델마다 다른 임계값을 사용해야 할 수 있습니다. 한 모델의 임계값은 다른 모델과 공유되는 것이 아니며, 반드시 동일한 결과를 생성하는 것은 아닙니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양한 Face 서비스 API와 함께 사용할 인식 모델을 지정하는 방법을 배웠습니다. 다음에는 얼굴 감지로 시작하는 빠른 시작을 진행하세요.

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[얼굴 - 감지]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Identify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 - 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - 만들기]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc
