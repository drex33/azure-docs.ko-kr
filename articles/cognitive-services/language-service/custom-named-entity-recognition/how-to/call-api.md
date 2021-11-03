---
title: 사용자 지정 NER (명명 된 엔터티 인식) 작업 제출
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)에 대 한 요청을 보내는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 874d7c93a4e884c5bd449c6d49d676147e673978
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053559"
---
# <a name="deploy-a-model-and-extract-entities-from-text-using-the-runtime-api"></a>런타임 API를 사용 하 여 모델을 배포 하 고 텍스트에서 엔터티를 추출 합니다.

모델의 작동 방식에 만족 하면 배포할 준비가 되 고 텍스트에서 엔터티를 인식 하는 데 사용할 수 있습니다. API를 통해서만 엔터티 인식 작업을 보낼 수 있으며, 언어 스튜디오는 보낼 수 없습니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성 된 Azure blob storage 계정을 사용 하 여 성공적으로 [프로젝트를 만들었습니다](create-project.md) .
    * 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md)
* [성공적으로 학습 된 모델](train-model.md)
* 모델 [평가 세부 정보](view-model-evaluation.md) 를 검토 하 여 모델의 수행 방식을 확인 합니다.
    * 필드 성능이 만족 스 럽 면 모델을 [개선 했습니다](improve-model.md) .

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="deploy-your-model"></a>모델 배포

1. [언어 스튜디오](https://aka.ms/custom-extraction) 에서 프로젝트로 이동

2. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

3. 배포할 모델을 선택한 다음 **모델 배포** 를 선택합니다.

> [!TIP]
> 분류할 텍스트의 샘플을 전송 하 여 언어 스튜디오에서 모델을 테스트할 수 있습니다. 
> 1. 언어 스튜디오에서 프로젝트의 왼쪽에 있는 메뉴에서 **테스트 모델** 을 선택 합니다.
> 2. 테스트할 모델을 선택합니다.
> 3. 텍스트 상자에 텍스트를 추가하고 `.txt` 파일을 업로드할 수도 있습니다. 
> 4. **테스트 실행** 을 클릭합니다.
> 5. **결과** 탭에서 추출 된 엔터티를 텍스트에서 볼 수 있습니다. **JSON** 탭에서 JSON 응답을 볼 수도 있습니다.

## <a name="send-a-text-classification-request-to-your-model"></a>모델에 텍스트 분류 요청 보내기

# <a name="using-language-studio"></a>[언어 스튜디오 사용](#tab/language-studio)

### <a name="using-language-studio"></a>언어 스튜디오 사용

1. 배포가 완료 되 면 사용 하려는 모델을 선택 하 고 맨 위 메뉴에서 **예측 Url 가져오기** 를 클릭 하 고 URL 및 본문을 복사 합니다.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-1.png" alt-text="실행-유추" lightbox="../../custom-classification/media/get-prediction-url-1.png":::

2. 표시 되는 창의 **제출** 피벗 아래에서 샘플 요청을 명령줄에 복사 합니다.

3. `<YOUR_DOCUMENT_HERE>`분류 하려는 실제 텍스트로 대체 합니다.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-2.png" alt-text="실행-유추-2" lightbox="../../custom-classification/media/get-prediction-url-2.png":::

4. 요청 제출

5. 에서 추출 하는 응답 헤더의 `jobId` `operation-location` 형식은 다음과 같습니다. `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`

6. 검색 요청을 복사 하 고 `jobId` 요청을 바꾸고 제출 합니다.

    :::image type="content" source="../../custom-classification/media/get-prediction-url-3.png" alt-text="실행-유추-3" lightbox="../../custom-classification/media/get-prediction-url-3.png":::
    
 ## <a name="retrieve-the-results-of-your-job"></a>작업의 결과를 검색 합니다.

1. 이전에 받은 예제 요청을 가져온 동일한 창에서 **검색** 을 선택 하 고 샘플 요청을 텍스트 편집기에 복사 합니다. 

    :::image type="content" source="../media/get-prediction-retrieval-url.png" alt-text="예측 검색 요청 및 URL을 보여 주는 스크린샷" lightbox="../media/get-prediction-retrieval-url.png":::

2. `<OPERATION_ID>` `jobId` 이전 단계의로 대체 합니다. 

3. `GET`터미널 또는 명령 프롬프트에서 말아 넘기기 요청을 제출 합니다. 요청이 성공 하는 경우 아래와 유사한 202 응답과 JSON을 받게 됩니다.

결과에 대 한 자세한 내용은 다음 섹션에서 찾을 수 있습니다.

# <a name="using-the-api"></a>[API 사용](#tab/api)

### <a name="using-the-api"></a>API 사용

### <a name="get-your-resource-keys-endpoint"></a>리소스 키 엔드포인트 가져오기

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 스토리지 계정 개요 페이지로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.

    :::image type="content" source="../../custom-classification/media/get-endpoint-azure.png" alt-text="Azure 끝점 가져오기" lightbox="../../custom-classification/media/get-endpoint-azure.png":::

### <a name="submit-custom-ner-task"></a>사용자 지정 NER 작업 제출

> [!NOTE]
> 프로젝트 이름은 대/소문자를 구분 합니다.

이 **POST** 요청을 사용하여 엔터티 추출 작업을 시작합니다. `{projectName}`을 사용하려는 모델이 있는 프로젝트 이름으로 바꿉니다.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze`

#### <a name="headers"></a>헤더

|키|값|
|--|--|
|Ocp-Apim-Subscription-Key| 이 API에 대한 액세스를 제공하는 구독 키입니다.|

#### <a name="body"></a>본문

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

|키|샘플 값|설명|
|--|--|--|
|displayName|"MyJobName"|사용자의 작업 이름|
|문서|[{},{}]|작업을 실행할 문서 목록|
|ID|"doc1"|문자열 문서 식별자|
|text|"Lorem ipsum dolor sit amet"| 문자열 형식으로 문서화|
|"tasks"|[]| 수행하려는 작업 목록입니다.|
|--|customEntityRecognitionTasks|수행하려는 작업에 대한 작업 식별자입니다. |
|매개 변수|[]|작업에 전달할 매개 변수 목록|
|project-name| "MyProject"| 프로젝트 이름입니다. 프로젝트 이름은 대/소문자를 구분합니다.|
|deployment-name| "MyDeploymentName"| 배포 이름|

#### <a name="response"></a>응답

성공을 나타내는 202 응답을 받게 됩니다. 응답 **헤더** 에서 `operation-location`을 추출합니다.
`operation-location`은 다음과 같은 형식으로 지정됩니다.

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

다음 단계에서 이 엔드포인트를 사용하여 사용자 지정 인식 작업 결과를 가져옵니다.

### <a name="get-task-status-and-results"></a>작업 상태 및 결과 가져오기

다음 **GET** 요청을 사용하여 사용자 지정 인식 작업의 상태/결과를 쿼리합니다. 이전 단계에서 받은 엔드포인트를 사용할 수 있습니다.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>헤더

|키|값|
|--|--|
|Ocp-Apim-Subscription-Key| 이 API에 대한 액세스를 제공하는 구독 키입니다.|

결과에 대한 자세한 내용은 다음 섹션에서 확인할 수 있습니다.

---

#### <a name="custom-extraction-task-results"></a>사용자 지정 추출 작업 결과

Get 결과 호출에서 반환된 응답은 다음 매개 변수가 있는 JSON 문서입니다.

```json
{
    "createdDateTime": "2021-05-19T14:32:25.578Z",
    "displayName": "MyJobName",
    "expirationDateTime": "2021-05-19T14:32:25.578Z",
    "jobId": "3fa85f64-5717-4562-b3fc-2c963f66afa6",
    "lastUpdateDateTime": "2021-05-19T14:32:25.578Z",
    "status": "completed",
    "errors": [],
    "tasks": {
        "details": {
            "name": "MyJobName",
            "lastUpdateDateTime": "2021-03-29T19:50:23Z",
            "status": "completed"
        },
        "completed": 1,
        "failed": 0,
        "inProgress": 0,
        "total": 1,
        "tasks": {
    "customEntityRecognitionTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
               "documents": [
                        {
                            "id": "doc1",
                            "entities": [
                                {
                                    "text": "Government",
                                    "category": "restaurant_name",
                                    "offset": 23,
                                    "length": 10,
                                    "confidenceScore": 0.0551877357
                                }
                            ],
                            "warnings": []
                        },
                        {
                            "id": "doc2",
                            "entities": [
                                {
                                    "text": "David Schmidt",
                                    "category": "artist",
                                    "offset": 0,
                                    "length": 13,
                                    "confidenceScore": 0.8022353
                                }
                            ],
                            "warnings": []
                        }
                    ],
                "errors": [],
                "statistics": {
                    "documentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```
