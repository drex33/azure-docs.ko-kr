---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 882355f0c4e6a44300182ffd27eb4cc422422f5f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100867"
---
## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [cURL](https://curl.haxx.se/).
* 언어 리소스입니다. 계정이 없으면 [Azure Portal](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)을 사용하여 만들 수 있습니다. 새 리소스를 만드는 경우 링크를 클릭하고 단계에 따라 배포할 때까지 기다립니다. 그런 다음 **리소스로 이동** 을 클릭합니다.

## <a name="get-your-resource-keys-and-endpoint"></a>리소스 키 및 엔드포인트 가져오기

1. [Azure Portal](https://ms.portal.azure.com/#home)에서 리소스 개요 페이지로 이동합니다.
2. 왼쪽 메뉴에서 **키 및 엔드포인트** 를 선택합니다. API 요청에 엔드포인트와 키를 사용합니다. 

    :::image type="content" source="../../../media/azure-portal-resource-credentials.png" alt-text="Azure Portal의 키 및 엔드포인트 페이지를 보여 주는 스크린샷" lightbox="../../../media/azure-portal-resource-credentials.png":::

## <a name="import-a-project"></a>프로젝트 가져오기

시작하려면 CLU JSON을 서비스로 가져올 수 있습니다. 빠른 시작은 "EmailProject"라는 이메일 애플리케이션에 대한 몇 가지 의도와 엔터티를 설정하는 샘플 JSON을 아래에 제공합니다. 

다음 URL, 헤더 및 JSON 본문을 사용하여 **POST** 요청을 만들어 프로젝트를 만듭니다.

### <a name="request-url"></a>요청 URL

API 요청을 만드는 경우 다음 URL을 사용합니다. 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversation/projects/EmailProject/:import?api-version=2021-11-01-preview
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

다음 샘플 JSON을 본문으로 사용합니다.

```json
{
    "api-version": "2021-11-01-preview",
    "metadata": {
        "name": "EmailProject",
        "description": "",
        "type": "Conversation",
        "multilingual": true,
        "language": "en-us"
    },
    "assets": {
        "intents": [
            {
                "name": "Read"
            },
            {
                "name": "Delete"
            },
            {
                "name": "Attach"
            }
        ],
        "entities": [
            {
                "name": "Sender"
            },
            {
                "name": "FileName"
            },
            {
                "name": "FileType"
            }
        ],
        "examples": [
            {
                "text": "add the pdf file with the name signed contract",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 31,
                        "length": 15
                    },
                    {
                        "entity": "FileType",
                        "offset": 8,
                        "length": 3
                    }
                ]
            },
            {
                "text": "attach the powerpoint file",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 10
                    }
                ]
            },
            {
                "text": "attach the excel file called reports q1",
                "language": "en-us",
                "intent": "Attach",
                "entities": [
                    {
                        "entity": "FileName",
                        "offset": 29,
                        "length": 10
                    },
                    {
                        "entity": "FileType",
                        "offset": 11,
                        "length": 5
                    }
                ]
            },
            {
                "text": "move this to the deleted folder",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "remove this one",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete this",
                "language": "en-us",
                "intent": "Delete",
                "entities": []
            },
            {
                "text": "delete my last email from martha",
                "language": "en-us",
                "intent": "Delete",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 26,
                        "length": 6
                    }
                ]
            },
            {
                "text": "what did the email from matt say",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 24,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read john's email for me",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 5,
                        "length": 4
                    }
                ]
            },
            {
                "text": "read the email from carol",
                "language": "en-us",
                "intent": "Read",
                "entities": [
                    {
                        "entity": "Sender",
                        "offset": 20,
                        "length": 5
                    }
                ]
            }
        ]
    }
}
```

## <a name="start-training-your-model"></a>모델 학습 시작

프로젝트를 가져온 후 모델 학습을 시작할 수 있습니다. 다음 URL, 헤더 및 JSON 본문을 사용하여 **POST** 요청을 만들어 학습을 시작합니다.

### <a name="request-url"></a>요청 URL

API 요청을 만드는 경우 다음 URL을 사용합니다. 아래의 자리 표시자 값을 사용자 고유의 값으로 바꿉니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/:train?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|

### <a name="request-body"></a>요청 본문

요청에 다음 개체를 사용합니다. 학습이 완료되면 모델 이름이 `MyModel`로 지정됩니다.  

```json
{
  "modelLabel":"MyModel",
  "RunVerification":false
}
```

API 요청이 보내지면 성공을 나타내는 `202` 응답을 받게 됩니다. 응답 헤더에서 `location` 값을 추출합니다. 다음과 같은 형식으로 지정됩니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

이 작업은 비동기이므로 `JOB-ID`가 요청을 식별하는 데 사용됩니다. 다음 단계에서 이 URL을 사용하여 학습 상태를 가져옵니다. 

## <a name="get-training-status"></a>학습 상태 가져오기

다음 **GET** 요청을 사용하여 모델의 학습 프로세스 상태를 쿼리합니다. 이전 단계에서 받은 URL을 사용하거나 아래 자리 표시자 값을 자신의 값으로 바꿀 수 있습니다. 

### <a name="request-url"></a>요청 URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/train/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | 모델의 학습 상태를 찾기 위한 ID입니다. 이는 이전 단계에서 받은 `location` 헤더 값에 있습니다.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|


### <a name="response-body"></a>응답 본문

요청을 보내면 다음과 같은 응답을 받게 됩니다. **status** 매개 변수가 "succeeded"로 변경될 때까지 이 엔드포인트를 계속 폴링합니다. 

```json
{
    "result":
    {
          "trainedModelLabel":"MyModel",
          "trainStatus":{"percentComplete":0,"elapsedTime":null},
          "evaluationStatus":{"percentComplete":0,"elapsedTime":null}
     },
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="deploy-your-model"></a>모델 배포

학습이 완료되면 예측을 위해 모델을 배포할 수 있습니다. 

다음 URL, 헤더 및 JSON 본문을 사용하여 **POST** 요청을 만들어 대화형 Language Understanding 모델 배포를 시작합니다.


### <a name="request-url"></a>요청 URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |


### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|


### <a name="request-body"></a>요청 본문

```json
{
  "trainedModelLabel":"MyModel",
  "deploymentName":"production"
}
```

API 요청을 보내면 성공을 나타내는 `202` 응답을 받게 됩니다. 응답 헤더에서 `location` 값을 추출합니다. 다음과 같은 형식으로 지정됩니다. 

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
``` 

이 작업은 비동기이므로 `JOB-ID`가 요청을 식별하는 데 사용됩니다. 다음 단계에서 이 URL을 사용하여 교육 상태를 가져옵니다.

## <a name="get-deployment-status"></a>배포 상태 가져오기

다음 **GET** 요청을 사용하여 모델 배포 프로세스의 상태를 쿼리합니다. 이전 단계에서 받은 URL을 사용하거나 아래 자리 표시자 값을 자신의 값으로 바꿀 수 있습니다. 

### <a name="request-url"></a>요청 URL

```rest
{YOUR-ENDPOINT}/language/analyze-conversations/projects/EmailProject/deployments/production/jobs/{JOB-ID}?api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
|`{JOB-ID}`     | 모델의 학습 상태를 찾기 위한 ID입니다. 이는 이전 단계에서 받은 `location` 헤더 값에 있습니다.  | `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxxx` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|


### <a name="response-body"></a>응답 본문

요청을 보내면 다음과 같은 응답을 받게 됩니다. **status** 매개 변수가 "succeeded"로 변경될 때까지 이 엔드포인트를 계속 폴링합니다. 

```json
{
    "jobId":"{JOB-ID}",
    "createdDateTime":"{CREATED-TIME}",
    "lastUpdatedDateTime":"{UPDATED-TIME}",
    "expirationDateTime":"{EXPIRATION-TIME}",
    "status":"running"
}
```

## <a name="query-model"></a>쿼리 모델

배포가 성공하면 예측을 위해 프로젝트 쿼리를 시작할 수 있습니다. 

다음 URL, 헤더 및 JSON 본문을 사용하여 **POST** 요청을 만들어 대화형 Language Understanding 모델 배포를 시작합니다.

### <a name="request-url"></a>요청 URL

```rest
{YOUR-ENDPOINT}/language/:analyze-conversations?projectName=EmailProject&deploymentName=production&api-version=2021-11-01-preview
```

|자리 표시자  |값  | 예제 |
|---------|---------|---------|
|`{YOUR-ENDPOINT}`     | API 요청을 인증하기 위한 엔드포인트입니다.   | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |

### <a name="headers"></a>헤더

다음 헤더를 사용하여 요청을 인증합니다. 

|키|값|
|--|--|
|`Ocp-Apim-Subscription-Key`| 리소스에 대한 키입니다. API 요청을 인증하는 데 사용됩니다.|

### <a name="request-body"></a>요청 본문

```json
{
  "query":"attach a docx file"
}
```

### <a name="response-body"></a>응답 본문

요청을 보내면 예측에 대해 다음과 같은 응답을 받게 됩니다!

```json
{
    "query":"attach a docx file",
    "prediction": {
        "topIntent":"Attach",
        "projectKind":"conversation",
        "intents":[{"category":"Attach","confidenceScore":0.9998592},{"category":"Read","confidenceScore":0.00010551753},{"category":"Delete","confidenceScore":3.5209276E-05}],
        "entities":[{"category":"FileType","text":"docx","offset":9,"length":4,"confidenceScore":1}]
     }
}
```
