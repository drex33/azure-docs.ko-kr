---
title: 사용자 지정 분류 작업을 제출 하는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류 요청을 보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 7a6902f79c3dcd20b57ca36732d8a5596f9b1fca
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757232"
---
# <a name="deploy-a-model-and-classify-text-using-the-runtime-api"></a>런타임 API를 사용 하 여 모델 배포 및 텍스트 분류

모델에 만족 하 고 필요한 기능을 개선 한 후에는 배포 하 고 텍스트 분류를 시작할 수 있습니다. 모델을 배포 하면 런타임 API를 통해 사용할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure blob storage 계정이 구성 된 [사용자 지정 분류 프로젝트](create-project.md) 
* 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md) 및 성공적으로 [학습 된 모델](train-model.md)
* 모델 [평가 세부 정보](view-model-evaluation.md) 를 검토 하 여 모델의 수행 방식을 확인 합니다.
* 필드 성능이 만족 스 럽 면 모델을 [개선 했습니다](improve-model.md) . 

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#project-development-lifecycle) 를 참조 하세요.

## <a name="deploy-your-model"></a>모델 배포

1. [언어 스튜디오](https://aka.ms/custom-classification) 에서 프로젝트로 이동

2. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

3. 배포할 모델을 선택한 다음 **모델 배포** 를 선택합니다.

> [!TIP]
> 분류할 텍스트의 샘플을 전송 하 여 언어 스튜디오에서 모델을 테스트할 수 있습니다. 
> 1. 언어 스튜디오에서 프로젝트의 왼쪽에 있는 메뉴에서 **테스트 모델** 을 선택 합니다.
> 2. 테스트할 모델을 선택합니다.
> 3. 텍스트 상자에 텍스트를 추가하고 `.txt` 파일을 업로드할 수도 있습니다. 
> 4. **테스트 실행** 을 클릭합니다.
> 5. **결과** 탭에서 텍스트의 예측 클래스를 볼 수 있습니다. **JSON** 탭에서 JSON 응답을 볼 수도 있습니다.

## <a name="send-a-text-classification-request-to-your-model"></a>모델에 텍스트 분류 요청 보내기

# <a name="using-language-studio"></a>[언어 스튜디오 사용](#tab/language-studio)

### <a name="using-language-studio"></a>언어 스튜디오 사용

1. 배포가 완료 되 면 사용 하려는 모델을 선택 하 고 맨 위 메뉴에서 **예측 Url 가져오기** 를 클릭 하 고 URL 및 본문을 복사 합니다.

    :::image type="content" source="../media/get-prediction-url-1.png" alt-text="실행-유추" lightbox="../media/get-prediction-url-1.png":::

2. 표시 되는 창의 **제출** 피벗 아래에서 샘플 요청을 명령줄에 복사 합니다.

3. `<YOUR_DOCUMENT_HERE>`분류 하려는 실제 텍스트로 대체 합니다.

    :::image type="content" source="../media/get-prediction-url-2.png" alt-text="실행-유추-2" lightbox="../media/get-prediction-url-2.png":::

4. 요청 제출

5. 에서 추출 하는 응답 헤더의 `jobId` `operation-location` 형식은 다음과 같습니다. `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`

6. 검색 요청을 복사 하 고 `<OPERATION-ID>` `jobId` 받은 양식 마지막 단계로 바꾸고 요청을 제출 합니다.

    :::image type="content" source="../media/get-prediction-url-3.png" alt-text="실행-유추-3" lightbox="../media/get-prediction-url-3.png":::

[!INCLUDE [JSON result for classification](../includes/classification-result-json.md)]


# <a name="using-the-api"></a>[API 사용](#tab/rest-api)

### <a name="using-the-rest-api"></a>REST API 사용

먼저 리소스 키와 끝점을 가져와야 합니다.

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스 개요 페이지로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.

    :::image type="content" source="../media/get-endpoint-azure.png" alt-text="Azure 끝점 가져오기" lightbox="../media/get-endpoint-azure.png":::

### <a name="submit-text-classification-task"></a>텍스트 분류 작업 제출

1. 다음 URL을 끝점으로 업데이트 하 여 POST 요청을 생성 하기 시작 합니다.
    
    `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

2. 요청에 대 한 헤더에서 키를 헤더에 추가 합니다 `Ocp-Apim-Subscription-Key` .

3. 요청의 JSON 본문에서 분석을 위해 입력 하는 문서와 사용자 지정 엔터티 인식 작업에 대 한 매개 변수를 지정 합니다. `project-name`은 대/소문자를 구분합니다.
 
    > [!tip]
    > JSON 구문에 대 한 자세한 내용은 [빠른 시작 문서](../quickstart.md?pivots=rest-api#submit-text-classification-task) 및 [참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-2-Preview-2/operations/Analyze) 를 참조 하세요.
    
    ```json
    {
        "displayName": "MyJobName",
        "analysisInput": {
            "documents": [
                {
                    "id": "doc1", 
                    "text": "Lorem ipsum dolor sit amet, consectetur adipiscing elit. Nunc tempus, felis sed vehicula lobortis, lectus ligula facilisis quam, quis aliquet lectus diam id erat. Vivamus eu semper tellus. Integer placerat sem vel eros iaculis dictum. Sed vel congue urna."
                },
                {
                    "id": "doc2",
                    "text": "Mauris dui dui, ultricies vel ligula ultricies, elementum viverra odio. Donec tempor odio nunc, quis fermentum lorem egestas commodo. Class aptent taciti sociosqu ad litora torquent per conubia nostra, per inceptos himenaeos."
                }
            ]
        },
        "tasks": {
            "customMultiClassificationTasks": [      
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

4. 성공을 나타내는 202 응답을 받게 됩니다. 응답 헤더에서을 추출 `operation-location` 합니다.
`operation-location`은 다음과 같은 형식으로 지정됩니다.

    `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

    다음 단계에서 이 엔드포인트를 사용하여 사용자 지정 인식 작업 결과를 가져옵니다.

5. 이전 단계의 URL을 사용 하 여 사용자 지정 인식 태스크의 상태/결과를 쿼리 하는 **GET** 요청을 만듭니다. `Ocp-Apim-Subscription-Key`요청에 대 한 헤더에 키를 추가 합니다.

[!INCLUDE [JSON result for classification](../includes/classification-result-json.md)]

# <a name="using-the-client-libraries"></a>[클라이언트 라이브러리 사용](#tab/client)

## <a name="use-the-client-libraries"></a>클라이언트 라이브러리 사용

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스 개요 페이지로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Azure 끝점 가져오기" lightbox="../../custom-classification/media/get-endpoint-azure.png":::

3. 원하는 언어의 클라이언트 라이브러리 패키지를 다운로드 하 여 설치 합니다.
    
    |언어  |패키지 버전  |
    |---------|---------|
    |.NET     | [5.2.0-beta. 2](https://www.nuget.org/packages/Azure.AI.TextAnalytics/5.2.0-beta.2)        |
    |Java     | [5.2.0-beta. 2](https://mvnrepository.com/artifact/com.azure/azure-ai-textanalytics/5.2.0-beta.2)        |
    |JavaScript     |  [5.2.0-beta. 2](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.2.0-beta.2)       |
    |Python     | [5.2.0 b2](https://pypi.org/project/azure-ai-textanalytics/5.2.0b2/)         |
    
4. 클라이언트 라이브러리를 설치한 후 GitHub에서 다음 샘플을 사용 하 여 API 호출을 시작 합니다.
    
    단일 범주:
    * [C#](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/tests/samples/Sample10_SingleCategoryClassify.cs)
    * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/ClassifyDocumentSingleCategory.java)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/customText.js)
    * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_single_category_classify.py)
    
    여러 범주:
    * [C#](https://github.com/Azure/azure-sdk-for-net/blob/main/sdk/textanalytics/Azure.AI.TextAnalytics/tests/samples/Sample11_MultiCategoryClassify.cs)
    * [Java](https://github.com/Azure/azure-sdk-for-java/blob/main/sdk/textanalytics/azure-ai-textanalytics/src/samples/java/com/azure/ai/textanalytics/lro/ClassifyDocumentMultiCategory.java)
    * [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/textanalytics/ai-text-analytics/samples/v5/javascript/customText.js)
    * [Python](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/textanalytics/azure-ai-textanalytics/samples/sample_multi_category_classify.py)

5. 클라이언트에 대 한 자세한 내용과 반환 개체에 대 한 자세한 내용은 다음 참조 설명서를 참조 하세요.
    
    * [C#](/dotnet/api/azure.ai.textanalytics?view=azure-dotnet-preview&preserve-view=true)
    * [Java](/java/api/overview/azure/ai-textanalytics-readme?view=azure-java-preview&preserve-view=true)
    * [JavaScript](/javascript/api/overview/azure/ai-text-analytics-readme?view=azure-node-preview&preserve-view=true)
    * [Python](/python/api/azure-ai-textanalytics/azure.ai.textanalytics?view=azure-python-preview&preserve-view=true)
---

