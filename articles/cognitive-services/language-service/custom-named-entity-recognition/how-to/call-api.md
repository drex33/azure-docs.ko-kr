---
title: NER(사용자 지정 명명된 엔터티 인식) 작업 제출
titleSuffix: Azure Cognitive Services
description: NER(사용자 지정 명명된 엔터티 인식)에 대한 요청을 보내는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: b655cf396053338ad475411ac0cd19bc41af3efd
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132758620"
---
# <a name="deploy-a-model-and-extract-entities-from-text-using-the-runtime-api"></a>런타임 API를 사용하여 모델을 배포하고 텍스트에서 엔터티를 추출합니다.

모델의 수행 방식에 만족하면 배포할 준비가 되었으며 텍스트에서 엔터티를 인식하는 데 사용됩니다. Language Studio가 아닌 API를 통해서만 엔터티 인식 작업을 보낼 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성된 Azure Blob Storage 계정으로 성공적으로 [만든 프로젝트](create-project.md)
    * 스토리지 계정에 [업로드된](create-project.md#prepare-training-data) 텍스트 데이터입니다.
* [태그가 지정된 데이터](tag-data.md)
* [성공적으로 학습된 모델](train-model.md)
* [모델 평가 세부 정보를](view-model-evaluation.md) 검토하여 모델의 성능을 확인했습니다.
    * (선택 사항) 성능이 만족스럽지 않은 경우 모델을 [개선했습니다.](improve-model.md)

자세한 내용은 [애플리케이션 개발 수명 주기를](../overview.md#application-development-lifecycle) 참조하세요.

## <a name="deploy-your-model"></a>모델 배포

1. [Language Studio에서](https://aka.ms/custom-extraction) 프로젝트로 이동

2. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

3. 배포할 모델을 선택한 다음 **모델 배포** 를 선택합니다.

> [!TIP]
> 분류할 텍스트 샘플을 전송하여 Language Studio에서 모델을 테스트할 수 있습니다. 
> 1. Language Studio의 프로젝트 왼쪽 메뉴에서 **모델 테스트를** 선택합니다.
> 2. 테스트할 모델을 선택합니다.
> 3. 텍스트 상자에 텍스트를 추가하고 `.txt` 파일을 업로드할 수도 있습니다. 
> 4. **테스트 실행** 을 클릭합니다.
> 5. **결과** 탭에서 텍스트에서 추출된 엔터티를 볼 수 있습니다. **JSON** 탭에서 JSON 응답을 볼 수도 있습니다.

## <a name="send-a-text-classification-request-to-your-model"></a>모델에 텍스트 분류 요청 보내기

# <a name="using-language-studio"></a>[Language Studio 사용](#tab/language-studio)

### <a name="using-language-studio"></a>Language Studio 사용

1. 배포가 완료되면 사용할 모델을 선택하고 위쪽 메뉴에서 **예측 URL 얻기를** 클릭하고 URL 및 본문을 복사합니다.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-1.png" alt-text="실행 유추" lightbox="../../custom-classification/media/get-prediction-url-1.png":::

2. 표시되는 창의 **제출** 피벗 아래에서 샘플 요청을 명령줄에 복사합니다.

3. `<YOUR_DOCUMENT_HERE>`를 분류하려는 실제 텍스트로 대체합니다.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-2.png" alt-text="run-inference-2" lightbox="../../custom-classification/media/get-prediction-url-2.png":::

4. 요청 제출

5. 응답 헤더에서 `jobId` 형식의 에서 추출을 받습니다. `operation-location``{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`

6. 검색 요청을 복사하고 요청을 바꾸고 `jobId` 제출합니다.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-3.png" alt-text="run-inference-3" lightbox="../../custom-classification/media/get-prediction-url-3.png":::
    
 ## <a name="retrieve-the-results-of-your-job"></a>작업 결과 검색

1. 이전에 받은 예제 요청을 받은 동일한 창에서 **검색을** 선택하고 샘플 요청을 텍스트 편집기로 복사합니다. 

    :::image type="content" source="../media/get-prediction-retrieval-url.png" alt-text="예측 검색 요청 및 URL을 보여주는 스크린샷" lightbox="../media/get-prediction-retrieval-url.png":::

2. `<OPERATION_ID>`를 `jobId` 이전 단계의 로 대체합니다. 

3. 터미널 `GET` 또는 명령 프롬프트에서 cURL 요청을 제출합니다. 요청이 성공한 경우 아래와 유사한 202 응답 및 JSON을 받게 됩니다.

[!INCLUDE [JSON result for entity recognition](../includes/recognition-result-json.md)]


# <a name="using-the-rest-api"></a>[REST API 사용](#tab/rest-api)

## <a name="use-the-rest-api"></a>REST API 사용

먼저 리소스 키 및 엔드포인트를 얻어야 합니다.

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스 개요 페이지로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Azure 엔드포인트를 얻습니다." lightbox="../../custom-classification/media/get-endpoint-azure.png":::

### <a name="submit-custom-ner-task"></a>사용자 지정 NER 작업 제출

1. 엔드포인트로 다음 URL을 업데이트하여 POST 요청 생성을 시작합니다.
    
    `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

2. 요청에 대한 헤더에서 헤더에 키를 `Ocp-Apim-Subscription-Key` 추가합니다.

3. 요청의 JSON 본문에서 분석을 위해 입력하는 문서와 사용자 지정 엔터티 인식 작업에 대한 매개 변수를 지정합니다. `project-name`은 대/소문자를 구분합니다.
 
    > [!tip]
    > JSON 구문에 대한 자세한 내용은 [빠른 시작 문서](../quickstart.md?pivots=rest-api#submit-custom-ner-task) 및 참조 [설명서를 참조하세요.](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-Preview-2/operations/Analyze)

    ```json
        {
        "displayName": "MyJobName",
        "analysisInput": {
            "documents": [
                {
                    "id": "doc1", 
                    "text": "This is a document."
                }
            ]
        },
        "tasks": {
            "customEntityRecognitionTasks": [      
                {
                    "parameters": {
                          "project-name": "MyProject",
                          "deployment-name": "MyDeploymentName"
                          "stringIndexType": "TextElements_v8"
                    }
                }
            ]
        }
    }
    ```

4. 성공을 나타내는 202 응답을 받게 됩니다. 응답 헤더에서 을 `operation-location` 추출합니다.
`operation-location`은 다음과 같은 형식으로 지정됩니다.

    `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

    다음 단계에서 이 엔드포인트를 사용하여 사용자 지정 인식 작업 결과를 가져옵니다.

5. 이전 단계의 URL을 사용하여 사용자 지정 인식 작업의 상태/결과를 쿼리하는 **GET** 요청을 만듭니다. 요청에 대한 헤더에 키를 `Ocp-Apim-Subscription-Key` 추가합니다.

[!INCLUDE [JSON result for entity recognition](../includes/recognition-result-json.md)]

# <a name="using-the-client-libraries"></a>[클라이언트 라이브러리 사용](#tab/client)

## <a name="use-the-client-libraries"></a>클라이언트 라이브러리 사용

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스 개요 페이지로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Azure 엔드포인트를 얻습니다." lightbox="../../custom-classification/media/get-endpoint-azure.png":::

3. 선택한 언어에 대한 클라이언트 라이브러리 패키지를 다운로드하여 설치합니다.
    
    |언어  |패키지 버전  |
    |---------|---------|
    |.NET     | [5.2.0-beta.2](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.2.0-beta.2)        |
    |Java     | [5.2.0-beta.2](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.2)        |
    |JavaScript     |  [5.2.0-beta.2](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.2.0-beta.2)       |
    |Python     | [5.2.0b2](https://pypi.org/project/azure-ai-textanalytics/5.2.0b2/)         |
    
4. 클라이언트 라이브러리를 설치한 후 GitHub 다음 샘플을 사용하여 API 호출을 시작합니다.
    
    * [C#](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/tests/samples/Sample9_RecognizeCustomEntitiesConvenience.cs)
    * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/RecognizeCustomEntities.java)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/customText.js)
    * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_recognize_custom_entities.py)
    
5. 클라이언트 및 반환 개체에 대한 자세한 내용은 다음 참조 설명서를 참조하세요.
    
    * [C#](/dotnet/api/azure.ai.textanalytics?view=azure-dotnet-preview&preserve-view=true)
    * [Java](/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-preview&preserve-view=true)
    * [JavaScript](/javascript/api/overview/azure/ai-text-analytics-readme?view=azure-node-preview&preserve-view=true)
    * [Python](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?view=azure-python-preview&preserve-view=true)
    
---

