---
title: 검색 모델을 지정하는 방법 - Face
titleSuffix: Azure Cognitive Services
description: 이 문서에서는 Azure Face 애플리케이션에서 사용할 얼굴 감지 모델을 선택하는 방법을 보여 줍니다.
services: cognitive-services
author: yluiu
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: yluiu
ms.custom: devx-track-csharp
ms.openlocfilehash: b933829ec9cfdb322cf0498c10966b9c244ac98e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535720"
---
# <a name="specify-a-face-detection-model"></a>얼굴 감지 모델 지정

이 가이드에서는 Azure Face 서비스의 얼굴 감지 모델을 지정하는 방법을 보여 줍니다.

Face 서비스는 기계 학습 모델을 사용하여 이미지에서 사람 얼굴에 대한 작업을 수행합니다. 사용자 피드백에 따라 모델의 정확도를 지속적으로 향상시키고 연구를 진행하면서 이러한 향상된 기능을 모델 업데이트로 제공합니다. 개발자는 사용할 얼굴 감지 모델의 버전을 지정하는 옵션을 사용할 수 있습니다. 사용 사례에 가장 잘 맞는 모델을 선택할 수 있습니다.

특정 얼굴 작업에서 얼굴 감지 모델을 지정하는 방법에 대해 자세히 알아보세요. Face 서비스는 얼굴 이미지를 다른 형태의 데이터로 변환할 때마다 얼굴 감지 기능을 사용합니다.

최신 모델을 사용해야 하는지 확실하지 않은 경우 [다른 모델 평가](#evaluate-different-models) 섹션으로 바로 이동하여 새 모델을 평가하고 현재 데이터 세트를 사용하여 결과를 비교합니다.

## <a name="prerequisites"></a>사전 요구 사항

AI 얼굴 감지의 개념에 대해 잘 알고 있어야 합니다. 그렇지 않은 경우 얼굴 감지 개념 가이드 또는 방법 가이드를 참조하세요.

* [얼굴 감지 개념](../concepts/face-detection.md)
* [검색 API 호출](HowtoDetectFacesinImage.md)

## <a name="detect-faces-with-specified-model"></a>지정된 모델을 사용하여 얼굴 감지

얼굴 감지는 사람 얼굴의 경계 상자 위치를 찾고 관련 시각적 랜드마크를 식별합니다. 얼굴의 특징을 추출하고 나중에 [인식](../concepts/face-recognition.md) 작업에 사용하기 위해 저장합니다.

[Face - Detect] API를 사용할 때는 `detectionModel` 매개 변수를 사용하여 모델 버전을 할당할 수 있습니다. 사용 가능한 값은

* `detection_01`
* `detection_02`
* `detection_03`

[Face - Detect] REST API에 대한 요청 URL은 다음과 같습니다.

`https://westus.api.cognitive.microsoft.com/face/v1.0/detect[?returnFaceId][&returnFaceLandmarks][&returnFaceAttributes][&recognitionModel][&returnRecognitionModel][&detectionModel]&subscription-key=<Subscription key>`

클라이언트 라이브러리를 사용하는 경우 적절한 문자열을 전달하여 `detectionModel`에 대한 값을 할당할 수 있습니다. 할당되지 않은 상태로 두면 API는 기본 모델 버전(`detection_01`)을 사용합니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
var faces = await faceClient.Face.DetectWithUrlAsync(imageUrl, false, false, recognitionModel: "recognition_04", detectionModel: "detection_03");
```

## <a name="add-face-to-person-with-specified-model"></a>지정된 모델을 사용하여 Person에 얼굴 추가

Face 서비스는 이미지에서 얼굴 데이터를 추출하고 [PersonGroup Person - Add Face](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b) API를 통해 **Person** 개체와 연결할 수 있습니다. 이 API 호출에서는 [Face - Detect]와 동일한 방법으로 감지 모델을 지정할 수 있습니다.

.NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
// Create a PersonGroup and add a person with face detected by "detection_03" model
string personGroupId = "mypersongroupid";
await faceClient.PersonGroup.CreateAsync(personGroupId, "My Person Group Name", recognitionModel: "recognition_04");

string personId = (await faceClient.PersonGroupPerson.CreateAsync(personGroupId, "My Person Name")).PersonId;

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.PersonGroupPerson.AddFaceFromUrlAsync(personGroupId, personId, imageUrl, detectionModel: "detection_03");
```

이 코드는 ID `mypersongroupid`를 사용하여 **PersonGroup** 을 만들고 여기에 **Person** 을 추가합니다. 그런 다음, `detection_03` 모델을 사용하여 이 **Person** 에 Face를 추가합니다. *detectionModel* 매개 변수를 지정하지 않으면 API는 기본 모델인 `detection_01`을 사용합니다.

> [!NOTE]
> **Person** 개체의 모든 얼굴에 대해 동일한 감지 모델을 사용할 필요는 없으며, **Person** 개체와 비교할 새 얼굴을 감지할 때 동일한 감지 모델을 사용할 필요가 없습니다(예: [Face - Detection] API).

## <a name="add-face-to-facelist-with-specified-model"></a>지정된 모델을 사용하여 FaceList에 얼굴 추가

기존 **FaceList** 개체에 얼굴을 추가할 때 감지 모델을 지정할 수도 있습니다. .NET 클라이언트 라이브러리에 대한 다음 코드 예제를 참조하세요.

```csharp
await faceClient.FaceList.CreateAsync(faceListId, "My face collection", recognitionModel: "recognition_04");

string imageUrl = "https://news.microsoft.com/ceo/assets/photos/06_web.jpg";
await client.FaceList.AddFaceFromUrlAsync(faceListId, imageUrl, detectionModel: "detection_03");
```

이 코드는 `My face collection`이라는 **FaceList** 를 만들고 `detection_03` 모델을 사용하여 여기에 Face를 추가합니다. *detectionModel* 매개 변수를 지정하지 않으면 API는 기본 모델인 `detection_01`을 사용합니다.

> [!NOTE]
> **FaceList** 개체의 모든 얼굴에 대해 동일한 감지 모델을 사용할 필요는 없으며, **FaceList** 개체와 비교할 새 얼굴을 감지할 때 동일한 감지 모델을 사용할 필요가 없습니다.

## <a name="evaluate-different-models"></a>다른 모델 평가

다양한 얼굴 감지 모델은 다양한 작업에 최적화되어 있습니다. 차이점에 대한 개요는 다음 표를 참조하세요.

|**detection_01**  |**detection_02**  |**detection_03** 
|---------|---------|---|
|모든 얼굴 감지 작업에 대한 기본값입니다. | 2019년 5월에 출시되었으며 필요에 따라 모든 얼굴 감지 작업에 사용할 수 있습니다. |  2021년 2월에 출시되었으며 필요에 따라 모든 얼굴 감지 작업에 사용할 수 있습니다.
|작은 얼굴, 측면 얼굴 또는 흐린 얼굴에는 최적화되지 않았습니다.  | 작은 얼굴, 측면 얼굴, 흐린 얼굴에 대한 정확도가 향상되었습니다. | 더 작은 얼굴(64x64픽셀) 및 회전된 얼굴 방향을 포함하여 정확도가 더욱 향상되었습니다.
|감지 호출에서 지정된 경우 주요 얼굴 특성(머리 자세, 나이, 감정 등)을 반환합니다. |  얼굴 특성을 반환하지 않습니다.     | 감지 호출에서 지정된 경우 마스크 및 머리 포즈 특성을 반환합니다.
|감지 호출에서 지정된 경우 얼굴 랜드마크를 반환합니다.   | 얼굴 랜드마크를 반환하지 않습니다.  | 감지 호출에서 지정된 경우 얼굴 랜드마크를 반환합니다.

감지 모델의 성능을 비교하는 가장 좋은 방법은 샘플 데이터 세트에서 사용하는 것입니다. 각 감지 모델을 사용하여 다양한 이미지, 특히 여러 얼굴 또는 표시하기 어려운 얼굴의 이미지에 대해 [Face - Detect] API를 호출하는 것이 좋습니다. 각 모델에서 반환하는 얼굴 수에 주의해야 합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 다양한 Face API와 함께 사용할 감지 모델을 지정하는 방법을 배웠습니다. 다음으로, 빠른 시작에 따라 얼굴 감지 및 분석을 시작하세요.

* [Face .NET SDK](../quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)
* [Face Python SDK](../quickstarts/client-libraries.md?pivots=programming-language-python%253fpivots%253dprogramming-language-python)
* [Face Go SDK](../quickstarts/client-libraries.md?pivots=programming-language-go%253fpivots%253dprogramming-language-go)

[얼굴 - 감지]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d
[Face - Find Similar]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237
[얼굴 - 식별]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239
[Face - Verify]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a
[PersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244
[PersonGroup - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395246
[PersonGroup Person - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523b
[PersonGroup - Train]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249
[LargePersonGroup - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d
[FaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b
[FaceList - Get]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c
[FaceList - Add Face]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395250
[LargeFaceList - Create]: https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc