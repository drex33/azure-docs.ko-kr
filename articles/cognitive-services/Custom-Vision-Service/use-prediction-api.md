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
ms.openlocfilehash: c3b0090c1c75c8d341ff67b15e7e0391be45c157
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131446698"
---
# <a name="use-your-model-with-the-prediction-api"></a>예측 API와 함께 모델 사용

모델을 학습한 후에는 이미지를 예측 API 엔드포인트에 제출하여 프로그래밍 방식으로 테스트할 수 있습니다.

> [!NOTE]
> 이 문서에서는 C#을 사용하여 예측 API에 이미지를 제출하는 방법을 보여 줍니다. 자세한 내용과 예는 [예측 API 참조](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)를 참조하세요.

## <a name="publish-your-trained-iteration"></a>학습된 반복 게시

[Custom Vision 웹 페이지](https://customvision.ai)에서 프로젝트를 선택하고 __성능__ 탭을 선택합니다.

예측 API에 이미지를 제출하려면 먼저 예측을 위해 반복을 게시해야 하는데 이 작업은 __게시__ 를 선택하고 게시된 반복의 이름을 지정하여 수행할 수 있습니다. 이렇게 하면 Custom Vision Azure 리소스의 예측 API에 모델이 액세스할 수 있습니다.

![게시 단추를 빨간색 사각형이 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/unpublished-iteration.png)

모델이 성공적으로 게시되고 나면 왼쪽 사이드바의 반복 옆에 "게시됨" 레이블이 표시되고 해당 이름이 반복의 설명 부분에 표시됩니다.

![게시된 레이블 및 게시된 반복의 이름이 빨간색 사각형으로 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>URL 및 예측 키 가져오기

모델이 게시되고 나면 __예측 URL__ 을 선택하여 필요한 정보를 검색할 수 있습니다. 그러면 __예측 URL__ 및 __예측-키__ 를 포함하여 예측 API를 사용하기 위한 정보가 있는 대화 상자가 열립니다.

![예측 URL 단추를 빨간색 사각형이 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/published-iteration-prediction-url.png)

![이미지 파일 및 예측-키 값을 사용하기 위한 예측 URL 값을 빨간색 사각형이 둘러싸고 있는 성능 탭이 표시됩니다.](./media/use-prediction-api/prediction-api-info.png)


이 가이드에서는 로컬 이미지를 사용하므로 **이미지 파일이 있는 경우** 에서 임시 위치로 URL을 복사합니다. 해당하는 __예측-키__ 값도 복사합니다.

## <a name="create-the-application"></a>애플리케이션 만들기

1. Visual Studio에서 새 C# 콘솔 애플리케이션을 만듭니다.

1. 다음 코드를 __Program.cs__ 파일의 본문으로 사용합니다.

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. 다음 정보를 변경합니다.
   * `namespace` 필드를 프로젝트 이름으로 설정합니다.
   * 자리 표시자 `<Your prediction key>`를 이전에 검색한 키 값으로 바꿉니다.
   * 자리 표시자 `<Your prediction URL>`을 이전에 검색한 URL로 바꿉니다.

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션을 실행할 때 콘솔에 이미지 파일의 경로를 입력하라는 메시지가 표시됩니다. 그런 다음 이미지가 Prediction API에 제출되고 예측 결과가 JSON 형식 문자열로 반환됩니다. 다음은 응답 예입니다.

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>다음 단계

이 가이드에서는 사용자 지정 이미지 분류자/감지기에 이미지를 제출하는 방법과 C# SDK로 응답을 프로그래밍 방식으로 수신하는 방법을 알아보았습니다. 다음으로 C#을 사용하여 엔드투엔드 시나리오를 완료하거나 다른 언어의 SDK로 시작하는 방법을 알아봅니다.

* [빠른 시작: Custom Vision SDK](quickstarts/image-classification.md)
