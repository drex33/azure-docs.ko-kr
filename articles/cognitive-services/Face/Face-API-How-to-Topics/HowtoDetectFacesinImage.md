---
title: 감지 API 호출 - Face
titleSuffix: Azure Cognitive Services
description: 이 가이드에서는 얼굴 감지를 사용하여 지정된 이미지에서 연령, 감정, 머리 포즈와 같은 특성을 추출하는 방법을 보여 줍니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/04/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 1fe1cddcbd194ec4b8ca25edfc4907631cb18df1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567082"
---
# <a name="call-the-detect-api"></a>감지 API 호출

이 가이드에서는 얼굴 감지 API를 사용하여 지정된 이미지에서 연령, 감정, 머리 포즈와 같은 특성을 추출하는 방법을 보여 줍니다. 사용자 요구에 맞게 이 API의 동작을 구성하는 다양한 방법을 알아봅니다.

이 가이드의 코드 조각은 Azure Cognitive Services Face 클라이언트 라이브러리를 사용하여 C#으로 작성되었습니다. 이 기능은 [REST API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)를 통해 사용할 수 있습니다.


## <a name="setup"></a>설정

이 가이드에서는 얼굴 구독 키와 엔드포인트 URL을 사용하여 `faceClient`라는 [FaceClient](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceclient) 개체를 이미 생성했다고 가정합니다. 이 기능을 설정하는 방법에 대한 지침은 빠른 시작 중 하나를 수행합니다.

## <a name="submit-data-to-the-service"></a>서비스에 데이터 제출

이미지에서 얼굴을 찾고 해당 위치를 가져오려면 [DetectWithUrlAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithurlasync) 또는 [DetectWithStreamAsync](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.faceoperationsextensions.detectwithstreamasync) 메서드를 호출합니다. **DetectWithUrlAsync** 는 URL 문자열을 입력으로 사용하고 **DetectWithStreamAsync** 는 이미지의 원시 바이트 스트림을 입력으로 사용합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic1":::

반환된 [DetectedFace](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.detectedface) 개체의 고유 ID와 얼굴의 픽셀 좌표를 제공하는 직사각형을 쿼리할 수 있습니다. 이렇게 하면 어떤 얼굴 ID가 원본 이미지의 어떤 얼굴에 매핑되는지 알 수 있습니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="basic2":::

얼굴의 위치 및 크기를 구문 분석하는 방법에 대한 자세한 내용은 [FaceRectangle](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.facerectangle)을 참조하세요. 일반적으로 이 직사각형에는 눈, 눈섭, 코 및 입이 포함됩니다. 정수리, 귀 및 턱은 반드시 포함되는 것은 아닙니다. 얼굴 사각형을 사용하여 전체 머리를 자르거나 중거리 인물 사진을 가져오려면 사각형을 각 방향으로 확장해야 합니다.

## <a name="determine-how-to-process-the-data"></a>데이터 처리 방법 결정

이 가이드는 전달할 수 있는 인수와 반환된 데이터로 수행할 수 있는 작업을 비롯하여 감지 호출의 세부 사항을 중점적으로 설명합니다. 필요한 기능만 쿼리하는 것이 좋습니다. 각 작업을 완료하려면 더 많은 시간이 걸립니다.

### <a name="get-face-landmarks"></a>얼굴 랜드마크 가져오기

[얼굴 랜드마크](../concepts/face-detection.md#face-landmarks)는 눈동자나 코끝과 같이 얼굴에서 찾기 쉬운 지점의 집합입니다. 얼굴 랜드마크 데이터를 가져오려면 _detectionModel_ 매개 변수를 `DetectionModel.Detection01`로 설정하고 _returnFaceLandmarks_ 매개 변수를 `true`로 설정합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks1":::

### <a name="get-face-attributes"></a>얼굴 특성 가져오기

얼굴 감지 API는 얼굴 직사각형과 랜드마크 외에도 얼굴의 여러 개념 특성을 분석할 수 있습니다. 전체 목록은 [얼굴 특성](../concepts/face-detection.md#attributes) 개념 섹션을 참조하세요.

얼굴 특성을 분석하려면 _detectionModel_ 매개 변수를 `DetectionModel.Detection01`로 설정하고 _returnFaceAttributes_ 매개 변수를 [FaceAttributeType 열거형](/dotnet/api/microsoft.azure.cognitiveservices.vision.face.models.faceattributetype) 값 목록으로 설정합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes1":::


## <a name="get-results-from-the-service"></a>서비스에서 결과 가져오기

### <a name="face-landmark-results"></a>얼굴 랜드마크 결과

다음 코드는 코와 눈동자의 위치를 검색하는 방법을 보여줍니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="landmarks2":::

얼굴 랜드마크 데이터를 사용하여 얼굴의 방향을 정확하게 계산할 수도 있습니다. 예를 들어 얼굴의 방향을 입의 가운데부터 눈의 가운데까지 벡터로 정의할 수 있습니다. 다음 코드는 이 벡터를 계산합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="direction":::

얼굴 방향을 알고 있는 경우 직사각형 얼굴을 회전하여 더 정확하게 정렬할 수 있습니다. 이미지에서 얼굴을 자르려면 얼굴이 항상 똑바로 나타나도록 이미지를 프로그래밍 방식으로 회전할 수 있습니다.


### <a name="face-attribute-results"></a>얼굴 특성 결과

다음 코드는 원래 호출에서 요청한 얼굴 특성 데이터를 검색하는 방법을 보여 줍니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/sdk/detect.cs" id="attributes2":::

각 특성에 대해 자세히 알아보려면 [얼굴 감지 및 특성](../concepts/face-detection.md) 개념 가이드를 참조하세요.

## <a name="next-steps"></a>다음 단계

이 가이드에서는 얼굴 감지 및 분석의 다양한 기능을 사용하는 방법을 알아보았습니다. 다음으로, 이러한 기능을 앱에 통합하여 사용자의 얼굴 데이터를 추가합니다.

- [자습서: Face 서비스에 사용자 추가](../enrollment-overview.md)

## <a name="related-articles"></a>관련 문서

- [참조 설명서(REST)](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
- [참조 설명서(.NET SDK)](/dotnet/api/overview/azure/cognitiveservices/face-readme)
