---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6e020a861294747ce381f4b6ae8d11e0f082d857
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132876288"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)

## <a name="create-a-new-azure-resource-and-azure-blob-storage-account"></a>새 Azure 리소스 및 Azure Blob Storage 계정 만들기

사용자 지정 NER을 사용하려면 먼저 프로젝트를 만들고 모델 학습을 시작하는 데 필요한 자격 증명을 제공하는 Azure 언어 리소스를 만들어야 합니다. 또한 모델을 빌드하는 데 사용할 데이터 세트를 업로드할 수 있는 Azure 스토리지 계정이 필요합니다.

> [!IMPORTANT]
> 빠르게 시작하려면 아래 제공된 단계를 사용하여 새 Azure 언어 리소스를 만드는 것이 좋습니다. 그러면 리소스를 만들고 동시에 스토리지 계정을 구성할 수 있습니다. 이는 나중에 수행하는 것보다 쉽습니다.
>
> 사용하려는 기존 리소스가 있는 경우 해당 리소스와 스토리지 계정을 별도로 구성해야 합니다. 자세한 내용은 [프로젝트 만들기 문서](../../how-to/create-project.md#using-a-pre-existing-azure-resource)를 참조하세요.

1. [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)로 이동하여 새 Azure 언어 리소스를 만듭니다. 추가 기능을 선택하라는 메시지가 표시되면 **사용자 지정 텍스트 분류 및 사용자 지정 NER** 를 선택합니다. 리소스를 만들 때 다음 매개 변수가 있는지 확인합니다.

    |Azure 리소스 요구 사항  |필수 값  |
    |---------|---------|
    |위치 | "West US 2" 또는 "West Europe"         |
    |가격 책정 계층     | 표준(**S**) 가격 책정 계층        |

2. **Custom NER(명명된 엔터티 인식) 및 사용자 지정 분류 (미리 보기)** 섹션에서 기존 스토리지 계정을 선택하거나 **새 스토리지 계정 만들기** 를 선택합니다. 이러한 값은 이 빠른 시작을 위한 것이며 프로덕션 환경에서 사용하려는 [스토리지 계정 값](../../../../../storage/common/storage-account-overview.md)이 반드시 필요한 것은 아닙니다.

    |스토리지 계정 값  |권장되는 값  |
    |---------|---------|
    | Name | 임의의 이름 |
    | 성능 | 표준 |
    | 계정 종류| 스토리지(범용 v1) |
    | 복제 | LRS(로컬 중복 스토리지)
    |위치 | 최고의 대기 시간을 위해 가장 가까운 모든 위치.        |


## <a name="upload-sample-data-to-blob-container"></a>Blob 컨테이너에 샘플 데이터 업로드

Azure 스토리지 계정을 만들고 언어 리소스에 연결한 후에는 이 빠른 시작을 위한 예제 파일을 업로드해야 합니다. 이러한 파일은 나중에 모델을 학습시키는 데 사용됩니다.

1. GitHub에서 이 빠른 시작을 위한 [예제 데이터를 다운로드](https://go.microsoft.com/fwlink/?linkid=2175226)합니다.

2. [Azure Portal](https://ms.portal.azure.com)에서 Azure 스토리지 계정으로 이동합니다. 계정으로 이동하여 샘플 데이터를 업로드합니다.

제공된 샘플 데이터 세트에는 20개의 대출 계약이 포함되어 있고, 각 계약에는 대출 기관과 대출자라는 두 당사자가 있습니다. 제공된 샘플 태그 파일을 사용하여 두 당사자, 계약 날짜, 대출 금액 및 이자율에 대한 관련 정보를 추출할 수 있습니다.

### <a name="get-your-resource-keys-endpoint"></a>리소스 키 엔드포인트 가져오기

* [Azure Portal](https://ms.portal.azure.com/#home)에서 스토리지 계정 개요 페이지로 이동합니다.

* 화면 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 대한 엔드포인트를 사용합니다. 이 경우 `Ocp-Apim-Subscription-Key` 헤더에 대한 키가 필요합니다.
:::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Azure 리소스에 대한 키 및 엔드포인트 화면을 보여 주는 스크린샷" lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="create-a-custom-ner-project"></a>사용자 지정 NER 프로젝트 만들기

리소스 및 스토리지 컨테이너가 구성되면 새 사용자 지정 NER 프로젝트를 만듭니다. 프로젝트는 데이터를 기반으로 하는 사용자 지정 AI 모델을 빌드하기 위한 작업 영역입니다. 사용자 및 사용 중인 Azure 리소스에 대한 기여자 액세스 권한이 있는 다른 사용자만 프로젝트에 액세스할 수 있습니다.

> [!NOTE]
> 프로젝트 이름은 모든 작업에 대해 대/소문자를 구분합니다.

다음 URL, 헤더 및 JSON 본문을 사용하는 **POST** 요청을 만들어 프로젝트를 만들고 태그 파일을 가져옵니다.

다음 URL을 사용하여 프로젝트를 만들고 태그 파일을 가져옵니다. 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{projectName}/:import?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|

### <a name="body"></a>본문

요청에서 다음 JSON을 사용합니다. 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. [샘플 데이터](https://github.com/Azure-Samples/cognitive-services-sample-data-files) 탭에서 사용할 수 있는 태그 파일 사용

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "MyProject",
        "multiLingual": true,
        "description": "Tryong out custom text classification",
        "modelType": "Extraction",
        "language": "string",
        "storageInputContainerName": "YOUR-CONTAINER-NAME",
        "settings": {}
    },
    "assets": {
        "extractors": [
        {
            "name": "Entity1"
        },
        {
            "name": "Entity2"
        }
    ],
    "documents": [
        {
            "location": "doc1.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 500,
                    "labels": [
                        {
                            "extractorName": "Entity1",
                            "offset": 25,
                            "length": 10
                        },                    
                        {
                            "extractorName": "Entity2",
                            "offset": 120,
                            "length": 8
                        }
                    ]
                }
            ]
        },
        {
            "location": "doc2.txt",
            "language": "en-us",
            "extractors": [
                {
                    "regionOffset": 0,
                    "regionLength": 100,
                    "labels": [
                        {
                            "extractorName": "Entity2",
                            "offset": 20,
                            "length": 5
                        }
                    ]
                }
            ]
        }
    ]
    }
}
```
메타데이터 키의 경우: 

|키  |값  | 예제 |
|---------|---------|---------|
| `modelType  `    | 모델 유형입니다. | 추출 |
|`storageInputContainerName`   | Azure Blob 스토리지 컨테이너의 이름입니다.   | `myContainer` |

이 요청은 다음과 같은 경우 오류를 반환합니다.

* 스토리지 계정에 대한 적절한 권한이 선택한 리소스에 없습니다. 

## <a name="start-training-your-model"></a>모델 학습 시작

프로젝트가 만들어지면 사용자 지정 NER 모델 학습을 시작할 수 있습니다. 다음 URL, 헤더 및 JSON 본문을 사용하는 **POST** 요청을 만들어 NER 모델 학습을 시작합니다.
다음 URL, 헤더 및 JSON 본문을 사용하는 **POST** 요청을 만들어 텍스트 분류 모델 학습을 시작합니다.

### <a name="request-url"></a>요청 URL

API 요청을 만드는 경우 다음 URL을 사용합니다. 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/:train?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | 프로젝트에 대한 이름입니다. 이 값은 대/소문자를 구분합니다.  | `myProject` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|

### <a name="request-body"></a>요청 본문

요청에서 다음 JSON을 사용합니다. 학습이 완료되면 모델 이름이 `MyModel`로 지정됩니다.  

```json
{
  "modelLabel": "MyModel",
  "runValidation": true
}
```

|키  |값  | 예제 |
|---------|---------|---------|
|`modelLabel  `    | 모델 이름입니다.   | MyModel |
|`runValidation`     | 테스트 세트에서 유효성 검사를 실행할 부울 값입니다.   | True |

API 요청이 보내지면 성공을 나타내는 `202` 응답을 받게 됩니다. 응답 헤더에서 `location` 값을 추출합니다. 다음과 같은 형식으로 지정됩니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

이 작업은 비동기이므로 `JOB-ID`가 요청을 식별하는 데 사용됩니다. 다음 단계에서 이 URL을 사용하여 학습 상태를 가져옵니다. 

## <a name="get-training-status"></a>학습 상태 가져오기

다음 **GET** 요청을 사용하여 모델의 학습 프로세스 상태를 쿼리합니다. 이전 단계에서 받은 URL을 사용하거나, 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿀 수 있습니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | 프로젝트에 대한 이름입니다. 이 값은 대/소문자를 구분합니다.  | `myProject` |
|`{JOB-ID}`     | 모델의 학습 상태를 찾기 위한 ID입니다. 이는 이전 단계에서 받은 `location` 헤더 값에 있습니다.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|

### <a name="response-body"></a>응답 본문

요청이 보내지면 다음과 같은 응답을 받게 됩니다. 

```json
{
  "jobs": [
    {
      "result": {
        "trainedModelLabel": "MyModel",
        "trainStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        },
        "evaluationStatus": {
          "percentComplete": 0,
          "elapsedTime": "string"
        }
      },
      "jobId": "string",
      "createdDateTime": "2021-10-19T23:24:41.572Z",
      "lastUpdatedDateTime": "2021-10-19T23:24:41.572Z",
      "expirationDateTime": "2021-10-19T23:24:41.572Z",
      "status": "unknown",
      "errors": [
        {
          "code": "unknown",
          "message": "string"
        }
      ]
    }
  ],
  "nextLink": "string"
}
```
## <a name="deploy-your-model"></a>모델 배포

다음 URL, 헤더 및 JSON 본문을 사용하는 **PUT** 요청을 만들어 사용자 지정 NER 모델 배포를 시작합니다.

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | 프로젝트에 대한 이름입니다. 이 값은 대/소문자를 구분합니다.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | 배포의 이름입니다. 이 값은 대/소문자를 구분합니다.  | `prod` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|

### <a name="request-body"></a>요청 본문

요청에서 다음 JSON을 사용합니다. 학습이 완료되면 모델 이름이 `MyModel`로 지정됩니다.  

```json
{
{
  "trainedModelLabel": "MyModel"
}
```

API 요청이 보내지면 성공을 나타내는 `202` 응답을 받게 됩니다. 응답 헤더에서 `location` 값을 추출합니다. 다음과 같은 형식으로 지정됩니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

이 작업은 비동기이므로 `JOB-ID`가 요청을 식별하는 데 사용됩니다. 다음 단계에서 이 URL을 사용하여 게시 상태를 가져옵니다.

## <a name="get-the-deployment-status"></a>배포 상태 가져오기

다음 **GET** 요청을 사용하여 모델의 게시 프로세스 상태를 쿼리합니다. 이전 단계에서 받은 URL을 사용하거나, 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿀 수 있습니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-text/projects/{YOUR-PROJECT-NAME}/deployments/{DEPLOYMENT-NAME}/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | 프로젝트에 대한 이름입니다. 이 값은 대/소문자를 구분합니다.  | `myProject` |
|`{DEPLOYMENT-NAME}`     | 배포의 이름입니다. 이 값은 대/소문자를 구분합니다.  | `prod` |
|`{JOB-ID}`     | 모델의 학습 상태를 찾기 위한 ID입니다. 이는 이전 단계에서 받은 `location` 헤더 값에 있습니다.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|


### <a name="submit-custom-ner-task"></a>사용자 지정 NER 작업 제출

이제 모델이 배포되었으므로 엔터티 인식 작업을 해당 모델에 보낼 수 있습니다. 

> [!NOTE]
> 프로젝트 이름은 대/소문자를 구분합니다.

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

### <a name="response-body"></a>응답 본문

응답은 다음 매개 변수가 포함된 JSON 문서입니다.

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
                    "validDocumentsCount":0,
                    "erroneousDocumentsCount":0,
                    "transactionsCount":0
                }
                    }
                }
            ]
        }
    }
```

## <a name="clean-up-resources"></a>리소스 정리

프로젝트가 더 이상 필요하지 않은 경우 다음과 같은 **DELETE** 요청을 사용하여 삭제할 수 있습니다. 자리 표시자 값을 사용자 고유의 값으로 바꿉니다.   

```rest
{YOUR-ENDPOINT}/language/text/authoring/v1.0-preview.2/projects/{PROJECT-NAME}
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{PROJECT-NAME}`     | 프로젝트에 대한 이름입니다. 이 값은 대/소문자를 구분합니다.  | `myProject` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|Ocp-Apim-Subscription-Key| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|
