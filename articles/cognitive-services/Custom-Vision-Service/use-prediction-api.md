---
title: 예측 엔드포인트를 사용하여 Custom Vision 분류자를 통해 이미지를 프로그래밍 방식으로 테스트
titleSuffix: Azure Cognitive Services
description: API를 사용하여 Custom Vision Service 분류자를 통해 프로그래밍 방식으로 이미지를 테스트하는 방법을 알아봅니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/27/2021
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 99e2fbce479b575759a442f9dc278723adb2b25f
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062378"
---
# <a name="call-the-prediction-api"></a>예측 API 호출

모델을 학습 한 후에는 예측 API 끝점에 해당 이미지를 제출 하 여 프로그래밍 방식으로 이미지를 테스트할 수 있습니다. 이 가이드에서는 예측 API를 호출 하 여 이미지의 점수를 매기는 방법을 배웁니다. 사용자 요구에 맞게 이 API의 동작을 구성할 수 있는 다양한 방법을 알아봅니다.


> [!NOTE]
> 이 문서에서는 c #에 대 한 .NET 클라이언트 라이브러리를 사용 하 여 예측 API에 이미지를 전송 하는 방법을 보여 줍니다. 자세한 내용과 예는 [예측 API 참조](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)를 참조하세요.

## <a name="setup"></a>설정

### <a name="publish-your-trained-iteration"></a>학습된 반복 게시

[Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택하고 __성능__ 탭을 선택합니다.

예측 API에 이미지를 제출 하려면 먼저 예측을 위해 반복을 게시 해야 합니다 .이 작업은 __게시__ 를 선택 하 고 게시 된 반복의 이름을 지정 하 여 수행할 수 있습니다. 이렇게 하면 Custom Vision Azure 리소스의 예측 API에 모델이 액세스할 수 있습니다.

![게시 단추를 빨간색 사각형이 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/unpublished-iteration.png)

모델이 성공적으로 게시되고 나면 왼쪽 사이드바의 반복 옆에 "게시됨" 레이블이 표시되고 해당 이름이 반복의 설명 부분에 표시됩니다.

![게시된 레이블 및 게시된 반복의 이름이 빨간색 사각형으로 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/published-iteration.png)

### <a name="get-the-url-and-prediction-key"></a>URL 및 예측 키 가져오기

모델이 게시되고 나면 __예측 URL__ 을 선택하여 필요한 정보를 검색할 수 있습니다. 그러면 __예측 URL__ 및 __예측-키__ 를 포함하여 예측 API를 사용하기 위한 정보가 있는 대화 상자가 열립니다.

![예측 URL 단추를 빨간색 사각형이 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/published-iteration-prediction-url.png)

![이미지 파일 및 예측-키 값을 사용하기 위한 예측 URL 값을 빨간색 사각형이 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/prediction-api-info.png)

## <a name="submit-data-to-the-service"></a>서비스에 데이터 제출

이 가이드에서는 **[](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet-preview)** `predictionClient` Custom Vision 예측 키 및 끝점 URL을 사용 하 여 라는 CustomVisionPredictionClient 개체를 이미 생성 했다고 가정 합니다. 이 기능을 설정 [하는 방법](quickstarts/image-classification.md)에 대 한 지침은 빠른 시작 중 하나를 수행 합니다.

이 가이드에서는 로컬 이미지를 사용 하므로 학습 된 모델에 제출할 이미지를 다운로드 합니다. 다음 코드는 사용자에 게 로컬 경로를 지정 하 고 해당 경로에 있는 파일의 bytestream를 가져오는 메시지를 표시 합니다.

```csharp
Console.Write("Enter image file path: ");
string imageFilePath = Console.ReadLine();
byte[] byteData = GetImageAsByteArray(imageFilePath);
```

다음과 같은 도우미 메서드를 포함 합니다.

```csharp
private static byte[] GetImageAsByteArray(string imageFilePath)
{
    FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
    BinaryReader binaryReader = new BinaryReader(fileStream);
    return binaryReader.ReadBytes((int)fileStream.Length);
}
```

**[ClassifyImageAsync](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclientextensions.classifyimageasync?view=azure-dotnet#Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_CustomVisionPredictionClientExtensions_ClassifyImageAsync_Microsoft_Azure_CognitiveServices_Vision_CustomVision_Prediction_ICustomVisionPredictionClient_System_Guid_System_String_System_IO_Stream_System_String_System_Threading_CancellationToken_)** 메서드는 프로젝트 ID와 로컬에 저장 된 이미지를 사용 하 고 지정 된 모델에 대해 이미지의 점수를 지정 합니다.

```csharp
// Make a prediction against the new project
Console.WriteLine("Making a prediction:");
var result = predictionApi.ClassifyImageAsync(project.Id, publishedModelName, byteData);
```

## <a name="determine-how-to-process-the-data"></a>데이터 처리 방법 결정

필요에 따라 대체 방법 ( **[CustomVisionPredictionClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.customvisionpredictionclient?view=azure-dotnet)** 클래스의 메서드 참조)을 선택 하 여 서비스에서 점수 매기기 작업을 수행 하는 방법을 구성할 수 있습니다. 

위의 메서드를 간소화 하기 위해 비 비동기 버전의 메서드를 사용할 수 있지만, 프로그램에서 상당한 시간 동안 잠길 수 있습니다.

**-Withnostore** 메서드는 예측이 완료 된 후 서비스에서 예측 이미지를 유지 하지 않도록 요구 합니다. 일반적으로 서비스는 이러한 이미지를 유지 하므로 나중에 모델을 반복 하기 위한 학습 데이터로 추가할 수 있습니다.

**-Withhttpmessages** 메서드는 API 호출의 원시 HTTP 응답을 반환 합니다.

## <a name="get-results-from-the-service"></a>서비스에서 결과 가져오기

서비스는 **[Imageprediction](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.imageprediction?view=azure-dotnet)** 개체 형식으로 결과를 반환 합니다. **예측** 속성에는 각각 단일 개체 예측을 나타내는 **[PredictionModel](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.vision.customvision.prediction.models.predictionmodel?view=azure-dotnet)** 개체의 목록이 포함 되어 있습니다. 여기에는 레이블의 이름과 이미지에서 개체가 검색 된 경계 상자 좌표가 포함 됩니다. 그러면 앱에서이 데이터를 구문 분석할 수 있습니다. 예를 들어 화면에 레이블이 지정 된 개체 필드가 있는 이미지를 표시할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

이 가이드에서는 사용자 지정 이미지 분류자/감지기에 이미지를 제출하는 방법과 C# SDK로 응답을 프로그래밍 방식으로 수신하는 방법을 알아보았습니다. 다음으로 C#을 사용하여 엔드투엔드 시나리오를 완료하거나 다른 언어의 SDK로 시작하는 방법을 알아봅니다.

* [빠른 시작: Custom Vision SDK](quickstarts/image-classification.md)
