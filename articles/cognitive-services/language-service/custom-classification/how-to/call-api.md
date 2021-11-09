---
title: 사용자 지정 분류 작업을 제출하는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류에 대한 요청을 보내는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 9ba640d5d0177c812ad25a76353bd89f2e696d81
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054717"
---
# <a name="deploy-a-model-and-classify-text-using-the-runtime-api"></a>런타임 API를 사용하여 모델 배포 및 텍스트 분류

모델에 만족하고 필요한 사항을 개선한 후에는 모델을 배포하고 텍스트 분류를 시작할 수 있습니다. 모델을 배포하면 런타임 API를 통해 사용할 수 있습니다.

## <a name="prerequisites"></a>전제 조건

* 구성된 Azure Blob Storage 계정이 있는 [사용자 지정 분류 프로젝트](create-project.md) 
* 스토리지 계정에 [업로드된](create-project.md#prepare-training-data) 텍스트 데이터입니다.
* [태그가 지정된 데이터](tag-data.md) 및 성공적으로 [학습된 모델](train-model.md)
* [모델 평가 세부 정보를 검토하여 모델의](view-model-evaluation.md) 성능을 확인했습니다.
* (선택 사항) 성능이 만족스럽지 않은 경우 모델을 [개선했습니다.](improve-model.md) 

자세한 내용은 [애플리케이션 개발 수명 주기를](../overview.md#project-development-lifecycle) 참조하세요.

## <a name="deploy-your-model"></a>모델 배포

1. [Language Studio에서](https://aka.ms/custom-classification) 프로젝트로 이동

2. 왼쪽 메뉴에서 **모델 배포** 를 선택합니다.

3. 배포할 모델을 선택한 다음 **모델 배포** 를 선택합니다.

> [!TIP]
> 분류할 텍스트 샘플을 전송하여 Language Studio에서 모델을 테스트할 수 있습니다. 
> 1. Language Studio의 프로젝트 왼쪽 메뉴에서 **모델 테스트를** 선택합니다.
> 2. 테스트할 모델을 선택합니다.
> 3. 텍스트 상자에 텍스트를 추가하고 `.txt` 파일을 업로드할 수도 있습니다. 
> 4. **테스트 실행** 을 클릭합니다.
> 5. **결과** 탭에서 텍스트의 예측 클래스를 볼 수 있습니다. **JSON** 탭에서 JSON 응답을 볼 수도 있습니다.

## <a name="send-a-text-classification-request-to-your-model"></a>모델에 텍스트 분류 요청 보내기

# <a name="using-language-studio"></a>[Language Studio 사용](#tab/language-studio)

### <a name="using-language-studio"></a>Language Studio 사용

1. 배포가 완료되면 사용하려는 모델을 선택하고 위쪽 메뉴에서 **예측 URL 얻기를** 클릭하고 URL 및 본문을 복사합니다.

    :::image type="content" source="../media/get-prediction-url-1.png" alt-text="실행 유추" lightbox="../media/get-prediction-url-1.png":::

2. 표시되는 창의 **제출** 피벗 아래에서 샘플 요청을 명령줄에 복사합니다.

3. `<YOUR_DOCUMENT_HERE>`를 분류하려는 실제 텍스트로 대체합니다.

    :::image type="content" source="../media/get-prediction-url-2.png" alt-text="run-inference-2" lightbox="../media/get-prediction-url-2.png":::

4. 요청 제출

5. 응답 헤더에서 `jobId` 형식의 에서 `operation-location` 추출을 받습니다. `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId}>`

6. 검색 요청을 복사하고 `<OPERATION-ID>` 을 `jobId` 받은 양식 마지막 단계로 바꾸고 요청을 제출합니다.

    :::image type="content" source="../media/get-prediction-url-3.png" alt-text="run-inference-3" lightbox="../media/get-prediction-url-3.png":::

 결과에 대한 자세한 내용은 다음 섹션에서 확인할 수 있습니다.

# <a name="using-the-api"></a>[API 사용](#tab/api)

### <a name="using-the-api"></a>API 사용

먼저 리소스 키와 엔드포인트를 얻어야 합니다.

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스 개요 페이지로 이동합니다.

2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.

    :::image type="content" source="../media/get-endpoint-azure.png" alt-text="Azure 엔드포인트를 얻습니다." lightbox="../media/get-endpoint-azure.png":::

### <a name="submit-text-classification-task"></a>텍스트 분류 작업 제출

> [!NOTE]
> 프로젝트 이름은 대/소문자를 구분합니다.

이 **POST** 요청을 사용하여 엔터티 추출 작업을 시작합니다.

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

|키|샘플 값|설명|
|--|--|--|
|displayName|"MyJobName"|사용자의 작업 이름|
|문서|[{},{}]|작업을 실행할 문서 목록|
|ID|"doc1"|문자열 문서 식별자|
|text|"Lorem ipsum dolor sit amet"| 문자열 형식으로 문서화|
|"tasks"|[]| 수행하려는 작업 목록입니다.|
|--|customMultiClassificationTasks|수행하려는 작업의 작업 식별자입니다. 단일 분류 작업에는 `customClassificationTasks`을 사용하고 다중 분류 작업에는 `customMultiClassificationTasks`를 사용합니다. |
|매개 변수|[]|작업에 전달할 매개 변수 목록|
|project-name| "MyProject"| 프로젝트 이름입니다. 프로젝트 이름은 대/소문자를 구분합니다.|
|deployment-name| "MyDeploymentName"| 배포 이름|

#### <a name="response"></a>응답

성공을 나타내는 202 응답을 받게 됩니다. 응답 **헤더** 에서 `operation-location`을 추출합니다.
`operation-location` 형식은 다음과 같습니다.

 `{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`

다음 단계에서 이 엔드포인트를 사용하여 사용자 지정 분류 작업 결과를 얻습니다.

### <a name="get-the-classification-task-status-and-results"></a>분류 작업 상태 및 결과 가져오기

다음 **GET** 요청을 사용하여 사용자 지정 분류 작업의 상태/결과를 쿼리합니다. 이전 단계에서 받은 엔드포인트를 사용할 수 있습니다.

`{YOUR-ENDPOINT}/text/analytics/v3.2-preview.2/analyze/jobs/<jobId>`.

#### <a name="headers"></a>헤더

|키|값|
|--|--|
|Ocp-Apim-Subscription-Key| 이 API에 대한 액세스를 제공하는 구독 키입니다.|

결과에 대한 자세한 내용은 다음 섹션에서 확인할 수 있습니다.

---


#### <a name="text-classification-task-results"></a>텍스트 분류 태스크 결과

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
    "customMultiClassificationTasks": [
        {
            "lastUpdateDateTime": "2021-05-19T14:32:25.579Z",
            "name": "MyJobName",
            "status": "completed",
            "results": {
                "documents": [
                    {
                        "id": "doc1",
                        "classes": [
                            {
                                "category": "Class_1",
                                "confidenceScore": 0.0551877357
                            }
                        ],
                        "warnings": []
                    },
                    {
                        "id": "doc2",
                        "classes": [
                            {
                                "category": "Class_1",
                                "confidenceScore": 0.0551877357
                            },
                                                        {
                                "category": "Class_2",
                                "confidenceScore": 0.0551877357
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
