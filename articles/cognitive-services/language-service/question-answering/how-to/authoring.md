---
title: 작성 API - 질문 답변
titleSuffix: Azure Cognitive Services
description: 질문 답변 작성 API를 사용하여 새 질문 답변 쌍 추가, 기술 자료 만들기 및 게시와 같은 일반적인 작업을 자동화합니다.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/23/2021
ms.openlocfilehash: d5eaf1077b0cccc322953c0c1a07544b30559d0c
ms.sourcegitcommit: 7e5bfb91cc0a4f94fbe0399f900b0fc7cb59e009
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/28/2021
ms.locfileid: "133187481"
---
# <a name="authoring-api"></a>API 작성

Authoring API에 대한 질문 답변은 프로젝트/기술 자료 만들기, 게시 및 유지 관리뿐만 아니라 새 질문 답변 쌍 추가와 같은 일반적인 작업을 자동화하는 데 사용됩니다. 

> [!NOTE]
> 현재 제작 기능은 REST API 통해서만 사용할 수 있습니다. 이 문서에서는 cURL과 함께 REST API 사용하는 예제를 제공합니다. 사용 가능한 모든 매개 변수 및 기능에 대한 전체 설명서는 [REST API 참조 콘텐츠를 참조하세요.](https://docs.microsoft.com/rest/api/cognitiveservices/questionanswering/question-answering-projects)

## <a name="prerequisites"></a>사전 요구 사항

* 현재 버전의 [cURL](https://curl.haxx.se/). 이 문서에는 몇 가지 명령줄 스위치가 사용되며, 이러한 스위치는 [cURL 설명서](https://curl.haxx.se/docs/manpage.html)에 나와 있습니다.
* 이 문서의 명령은 Bash 셸에서 실행되도록 설계되었습니다. 이러한 명령은 수정 없이 Windows 명령 프롬프트 또는 PowerShell에서 항상 작동하지는 않습니다. Bash 셸을 로컬로 설치하지 않은 경우 [Azure Cloud Shell bash 환경을](../../../../cloud-shell/overview.md)사용할 수 있습니다.

## <a name="create-a-project"></a>프로젝트 만들기

프로그래밍 방식으로 프로젝트/기술 자료 만들기:

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 기술 자료 배포 예측 URL 얻기 에 대한 **Language Studio**  >  **질문의** 값을 찾을 수  >    >  있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 이전 예제가 아래 코드 샘플의 엔드포인트인 경우 엔드포인트 경로의 나머지 부분이 이미 존재하므로 의 지역 특정 부분만 추가하면 `southcentral` 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 기술 자료 배포 예측 URL 얻기 에 대한 **Language Studio**  >  **질문의** 값을 찾을 수  >    >  있습니다. 키 값은 샘플 요청의 일부입니다.|
| `NEW-PROJECT-NAME` | 프로젝트/기술 자료 질문에 대답하는 새 질문의 이름입니다.|

또한 프로젝트 언어와 같은 추가 값, 신뢰도 임계값을 충족하거나 초과하는 대답을 찾을 수 없는 경우 제공된 기본 답변, 이 언어 리소스가 여러 언어를 지원할지 여부를 조정할 수도 있습니다.

### <a name="example-query"></a>예제 쿼리

```bash
curl -X PATCH -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '{
      "description": "proj1 is a test project.",
      "language": "en",
      "settings": {
        "defaultAnswer": "No good match found for your question in the knowledge base."
      },
      "multilingualResource": true
    }
  }'  'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{NEW-PROJECT-NAME}?api-version=2021-10-01'
```

### <a name="example-response"></a>예제 응답

```json
{
 "200": {
      "headers": {},
      "body": {
        "projectName": "proj1",
        "description": "proj1 is a test project.",
        "language": "en",
        "settings": {
          "defaultAnswer": "No good match found for your question in the knowledge base."
        },
        "multilingualResource": true,
        "createdDateTime": "2021-05-01T15:13:22Z",
        "lastModifiedDateTime": "2021-05-01T15:13:22Z",
        "lastDeployedDateTime": "2021-05-01T15:13:22Z"
      }
 }
}
```

## <a name="delete-project"></a>프로젝트를 삭제합니다.

프로그래밍 방식으로 프로젝트/기술 자료 삭제:

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 이전 예제가 아래 코드 샘플의 엔드포인트인 경우 엔드포인트 경로의 나머지 부분이 이미 존재하므로 의 지역 특정 부분만 추가하면 `southcentral` 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 삭제하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X DELETE -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}?api-version=2021-10-01'
```

프로젝트를 성공적으로 삭제하면 `Operation-Location` 프로젝트 삭제 작업의 상태를 확인하는 데 사용할 수 있는 헤더가 반환됩니다. 대부분의 예제에서는 응답 헤더를 확인할 필요가 없으므로 표시하지 않았습니다. 응답 헤더를 검색하기 위해 curl 명령은 `-i` 를 사용합니다. 엔드포인트 주소 이전의 이 매개 변수가 없으면 이 명령에 대한 응답은 응답이 발생하지 않은 것처럼 비어 있는 것처럼 표시됩니다.

### <a name="example-response"></a>예제 응답

```bash
HTTP/2 202
content-length: 0
operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/deletion-jobs/{JOB-ID-GUID}
x-envoy-upstream-service-time: 324
apim-request-id:
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Tue, 23 Nov 2021 20:56:18 GMT
```

프로젝트가 이미 삭제되거나 찾을 수 없는 경우 다음과 같은 메시지가 표시됩니다.

```json
{
  "error": {
    "code": "ProjectNotFound",
    "message": "The specified project was not found.",
    "details": [
      {
        "code": "ProjectNotFound",
        "message": "{GUID}"
      }
    ]
  }
}
```

### <a name="get-project-deletion-status"></a>프로젝트 삭제 상태 얻기

프로젝트 삭제 요청의 상태를 확인하려면 다음을 수행합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 기술 자료 배포 예측 URL 얻기 에 대한 **Language Studio**  >  **질문의** 값을 찾을 수  >    >  있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 배포 상태를 확인하려는 프로젝트/기술 자료의 이름입니다.|
| `JOB-ID` | 프로그래밍 방식으로 프로젝트/기술 자료 삭제 하는 경우는 삭제 요청에 대 한 `JOB-ID` 응답 헤더의 일부로 생성 `operation-location` 됩니다. 는 `JOB-ID` 의 끝에 있는 guid입니다. `operation-location` 예: `operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/deletion-jobs/{THIS GUID IS YOUR JOB ID}` |

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/deletion-jobs/{JOB-ID}?api-version=2021-10-01'
```

### <a name="example-response"></a>예제 응답

```json
{
  "createdDateTime": "2021-11-23T20:56:18+00:00",
  "expirationDateTime": "2021-11-24T02:56:18+00:00",
  "jobId": "GUID",
  "lastUpdatedDateTime": "2021-11-23T20:56:18+00:00",
  "status": "succeeded"
}
```

## <a name="get-project-settings"></a>프로젝트 설정 얻기

지정된 프로젝트/기술 자료 정보를 검색하려면 아래 쿼리에서 다음 값을 업데이트합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 정보를 검색하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash

curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}?api-version=2021-10-01'
```

### <a name="example-response"></a>예제 응답

```json
 {
    "200": {
      "headers": {},
      "body": {
        "projectName": "proj1",
        "description": "proj1 is a test project.",
        "language": "en",
        "settings": {
          "defaultAnswer": "No good match found for your question in the knowledge base."
        },
        "createdDateTime": "2021-05-01T15:13:22Z",
        "lastModifiedDateTime": "2021-05-01T15:13:22Z",
        "lastDeployedDateTime": "2021-05-01T15:13:22Z"
      }
    }
  }
```

## <a name="get-question-answer-pairs"></a>질문 답변 쌍 얻기

지정된 프로젝트/기술 자료의 질문 답변 쌍 및 관련 정보를 검색하려면 아래 쿼리에서 다음 값을 업데이트합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 모든 질문 답변 쌍을 검색하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/qnas?api-version=2021-10-01'

```

### <a name="example-response"></a>예제 응답

```json
{
    "200": {
      "headers": {},
      "body": {
        "value": [
          {
            "id": 1,
            "answer": "ans1",
            "source": "source1",
            "questions": [
              "question 1.1",
              "question 1.2"
            ],
            "metadata": {
              "k1": "v1",
              "k2": "v2"
            },
            "dialog": {
              "isContextOnly": false,
              "prompts": [
                {
                  "displayOrder": 1,
                  "qnaId": 11,
                  "displayText": "prompt 1.1"
                },
                {
                  "displayOrder": 2,
                  "qnaId": 21,
                  "displayText": "prompt 1.2"
                }
              ]
            },
            "lastUpdatedDateTime": "2021-05-01T17:21:14Z"
          },
          {
            "id": 2,
            "answer": "ans2",
            "source": "source2",
            "questions": [
              "question 2.1",
              "question 2.2"
            ],
            "lastUpdatedDateTime": "2021-05-01T17:21:14Z"
          }
        ]
      }
    }
  }
```

## <a name="get-sources"></a>원본을 얻습니다.

지정된 프로젝트/기술 자료의 원본 및 관련 정보를 검색하려면 아래 쿼리에서 다음 값을 업데이트합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 모든 원본 정보를 검색하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT_NAME}/sources?api-version=2021-10-01'
```

### <a name="example-response"></a>예제 응답

```json
{
    "200": {
      "headers": {},
      "body": {
        "value": [
          {
            "displayName": "source1",
            "sourceUri": "https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/overview/overview",
            "sourceKind": "url",
            "lastUpdatedDateTime": "2021-05-01T15:13:22Z"
          },
          {
            "displayName": "source2",
            "sourceUri": "https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf",
            "sourceKind": "file",
            "contentStructureKind": "unstructured",
            "lastUpdatedDateTime": "2021-05-01T15:13:22Z"
          }
        ]
      }
    }
  }
```

## <a name="get-synonyms"></a>동의어 얻기

지정된 프로젝트/기술 자료의 동의어 및 관련 정보를 검색하려면 아래 쿼리에서 다음 값을 업데이트합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 동의어 정보를 검색하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash

curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/synonyms?api-version=2021-10-01'
```

### <a name="example-response"></a>예제 응답

```json
 {
    "200": {
      "headers": {},
      "body": {
        "value": [
          {
            "alterations": [
              "qnamaker",
              "qna maker"
            ]
          },
          {
            "alterations": [
              "botframework",
              "bot framework"
            ]
          }
        ]
      }
    }
  }
```

## <a name="deploy-project"></a>프로젝트 배포

프로젝트/기술 자료가 프로덕션에 배포하려면 아래 쿼리에서 다음 값을 업데이트합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 프로덕션에 배포하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X PUT -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/deployments/production?api-version=2021-10-01'  
```

프로젝트를 성공적으로 배포하면 `Operation-Location` 배포 작업의 상태를 확인하는 데 사용할 수 있는 헤더가 반환됩니다. 대부분의 예제에서는 응답 헤더를 확인할 필요가 없으므로 표시하지 않았습니다. 응답 헤더를 검색하기 위해 curl 명령은 `-i` 를 사용합니다. 엔드포인트 주소 이전의 이 매개 변수가 없으면 이 명령에 대한 응답은 응답이 발생하지 않은 것처럼 비어 있는 것처럼 표시됩니다.

### <a name="example-response"></a>예제 응답

```bash
0HTTP/2 202
content-length: 0
operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/deployments/production/jobs/{JOB-ID-GUID}
x-envoy-upstream-service-time: 31
apim-request-id:
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Tue, 23 Nov 2021 20:35:00 GMT
```

### <a name="get-project-deployment-status"></a>프로젝트 배포 상태 얻기

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 배포 상태를 확인하려는 프로젝트/기술 자료의 이름입니다.|
| `JOB-ID` | 프로그래밍 방식으로 프로젝트/기술 자료 배포 하는 경우는 배포 요청에 대 한 `JOB-ID` 응답 헤더의 일부로 생성 `operation-location` 됩니다. 는 `JOB-ID` 의 끝에 있는 guid입니다. `operation-location` 예: `operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/deployments/production/jobs/{THIS GUID IS YOUR JOB ID}` |

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/deployments/production/jobs/{JOB-ID}?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```json
    {
    "200": {
      "headers": {},
      "body": {
        "errors": [],
        "createdDateTime": "2021-05-01T17:21:14Z",
        "expirationDateTime": "2021-05-01T17:21:14Z",
        "jobId": "{JOB-ID-GUID}",
        "lastUpdatedDateTime": "2021-05-01T17:21:14Z",
        "status": "succeeded"
      }
    }
  }
```

## <a name="export-project-metadata-and-assets"></a>프로젝트 메타데이터 및 자산 내보내기

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 내보낼 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X POST -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '{exportAssetTypes": ["qnas","synonyms"]}' -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/:export?api-version=2021-10-01&format=tsv'
```

### <a name="example-response"></a>예제 응답

```bash
HTTP/2 202
content-length: 0
operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/Sample-project/export/jobs/{JOB-ID_GUID}
x-envoy-upstream-service-time: 214
apim-request-id:
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Tue, 23 Nov 2021 21:24:03 GMT
```

### <a name="check-export-status"></a>내보내기 상태 확인

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 내보내기 상태를 확인하려는 프로젝트/기술 자료의 이름입니다.|
| `JOB-ID` | 프로그래밍 방식으로 프로젝트/기술 자료를 내보내는 경우는 `JOB-ID` `operation-location` 내보내기 요청에 대 한 응답 헤더의 일부로 생성 됩니다. 는 `JOB-ID` 의 끝에 있는 guid입니다 `operation-location` . 예: `operation-location: https://southcentralus.api.cognitive.microsoft.com/language/query-knowledgebases/projects/sample-proj1/export/jobs/{THIS GUID IS YOUR JOB ID}` |

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/sample-proj1/export/jobs/{JOB-ID}?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```json
{
  "createdDateTime": "2021-11-23T21:24:03+00:00",
  "expirationDateTime": "2021-11-24T03:24:03+00:00",
  "jobId": "JOB-ID-GUID",
  "lastUpdatedDateTime": "2021-11-23T21:24:08+00:00",
  "status": "succeeded",
  "resultUrl": "https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/export/jobs/{JOB-ID_GUID}/result"
}
```

ResultUrl에 직접 액세스 하려고 하면 404 오류가 발생 합니다. `?api-version=2021-10-01`인증 된 요청에서 액세스할 수 있도록 하려면 경로에를 추가 해야 합니다.`https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/export/jobs/{JOB-ID_GUID}/result?api-version=2021-10-01`

## <a name="import-project"></a>프로젝트 가져오기

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 예제 끝점은입니다. `https://southcentralus.api.cognitive.microsoft.com/` 아래 코드 샘플에서 끝점이 끝점 인 경우 `southcentral` 끝점 경로의 나머지 부분이 이미 있기 때문에의 지역별 특정 부분만 추가 하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키로 인해 가동 중지 시간이 없는 보안 키 회전이 가능 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 가져올 대상으로 지정할 프로젝트/기술 자료의 이름입니다.|
| `FILE-URI-PATH` | 프로젝트/기술 자료를 프로그래밍 방식으로 내보낸 다음 상태를 확인 하는 경우 내보내기 a `resultUrl` 가 응답의 일부로 생성 됩니다. 예: `"resultUrl": "https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/export/jobs/{JOB-ID_GUID}/result"` 소스 파일로 추가 된 API 버전에서 resultUrl를 사용 하 여에서 프로젝트를 가져올 수 있습니다 `https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/export/jobs/{JOB-ID_GUID}/result?api-version=2021-10-01` .|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X POST -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '{
      "ImportJobOptions": {"fileUri": "FILE-URI-PATH"}
  }' -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/:import?api-version=2021-10-01&format=tsv'
```

프로젝트를 배포 하기 위한 성공적인 호출로 인해 `Operation-Location` 헤더가 반환 되 고 가져오기 작업의 상태를 확인 하는 데 사용할 수 있습니다. 대부분의 예제에서 응답 헤더를 확인할 필요가 없으므로 표시 되지 않았습니다. 이 명령에 사용 되는 응답 헤더를 검색 `-i` 합니다. 끝점 주소 앞에이 추가 매개 변수가 없으면 응답이 발생 하지 않은 것 처럼이 명령에 대 한 응답이 빈 상태로 표시 됩니다.

### <a name="example-response"></a>예제 응답

```bash
HTTP/2 202
content-length: 0
operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/sample-proj1/import/jobs/{JOB-ID-GUID}
x-envoy-upstream-service-time: 417
apim-request-id: 
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Wed, 24 Nov 2021 00:35:11 GMT
```

### <a name="check-import-status"></a>가져오기 상태 확인

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 예제 끝점은입니다. `https://southcentralus.api.cognitive.microsoft.com/` 아래 코드 샘플에서 끝점이 끝점 인 경우 `southcentral` 끝점 경로의 나머지 부분이 이미 있기 때문에의 지역별 특정 부분만 추가 하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키로 인해 가동 중지 시간이 없는 보안 키 회전이 가능 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 가져올 대상으로 지정할 프로젝트/기술 자료의 이름입니다.|
| `JOB-ID` | 프로그래밍 방식으로 프로젝트/기술 자료를 가져오는 경우는 `JOB-ID` `operation-location` 내보내기 요청에 대 한 응답 헤더의 일부로 생성 됩니다. 는 `JOB-ID` 의 끝에 있는 GUID입니다 `operation-location` . 예: `operation-location: https://southcentralus.api.cognitive.microsoft.com/language/query-knowledgebases/projects/sample-proj1/import/jobs/{THIS GUID IS YOUR JOB ID}` |

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://southcentralus.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME/import/jobs/{JOB-ID-GUID}?api-version=2021-10-01' 
```

### <a name="example-query-response"></a>쿼리 응답 예제

```bash
{
  "errors": [],
  "createdDateTime": "2021-05-01T17:21:14Z",
  "expirationDateTime": "2021-05-01T17:21:14Z",
  "jobId": "JOB-ID-GUID",
  "lastUpdatedDateTime": "2021-05-01T17:21:14Z",
  "status": "succeeded"
}
```

## <a name="list-deployments"></a>배포 나열

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 예제 끝점은입니다. `https://southcentralus.api.cognitive.microsoft.com/` 아래 코드 샘플에서 끝점이 끝점 인 경우 `southcentral` 끝점 경로의 나머지 부분이 이미 있기 때문에의 지역별 특정 부분만 추가 하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키로 인해 가동 중지 시간이 없는 보안 키 회전이 가능 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 배포 목록을 생성할 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/deployments?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```json
[
  {
    "deploymentName": "production",
    "lastDeployedDateTime": "2021-10-26T15:12:02Z"
  }
]
```

## <a name="list-projects"></a>프로젝트 나열

계정이 액세스할 수 있는 모든 질문에 대 한 응답 프로젝트 목록을 검색 합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 예제 끝점은입니다. `https://southcentralus.api.cognitive.microsoft.com/` 아래 코드 샘플에서 끝점이 끝점 인 경우 `southcentral` 끝점 경로의 나머지 부분이 이미 있기 때문에의 지역별 특정 부분만 추가 하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키로 인해 가동 중지 시간이 없는 보안 키 회전이 가능 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```json
{
  "value": [
    {
      "projectName": "Sample-project",
      "description": "My first question answering project",
      "language": "en",
      "multilingualResource": false,
      "createdDateTime": "2021-10-07T04:51:15Z",
      "lastModifiedDateTime": "2021-10-27T00:42:01Z",
      "lastDeployedDateTime": "2021-11-24T01:34:18Z",
      "settings": {
        "defaultAnswer": "No good match found in KB"
      }
    }
  ]
}
```

## <a name="update-sources"></a>업데이트 원본

이 예제에서는 기존 프로젝트에 새 소스를 추가 합니다. 또한 쿼리 본문의 일부로 전달 하는 작업의 종류에 따라이 명령을 사용 하 여 기존 원본을 바꾸고 삭제할 수 있습니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 예제 끝점은입니다. `https://southcentralus.api.cognitive.microsoft.com/` 아래 코드 샘플에서 끝점이 끝점 인 경우 `southcentral` 끝점 경로의 나머지 부분이 이미 있기 때문에의 지역별 특정 부분만 추가 하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키로 인해 가동 중지 시간이 없는 보안 키 회전이 가능 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 원본 업데이트를 원하는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X PATCH -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '[
  {
    "op": "add",
    "value": {
      "displayName": "source5",
      "sourceKind": "url",
      "sourceUri": "https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf",
      "sourceContentStructureKind": "semistructured"
    }
  }
]'  -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/sources?api-version=2021-10-01'
```

소스를 업데이트 하는 호출은 `Operation-Location` 가져오기 작업의 상태를 확인 하는 데 사용할 수 있는 헤더를 반환 합니다. 대부분의 예제에서 응답 헤더를 확인할 필요가 없으므로 항상 표시 되지 않았습니다. 이 명령에 사용 되는 응답 헤더를 검색 `-i` 합니다. 끝점 주소 앞에이 매개 변수가 없으면 응답이 발생 하지 않은 것 처럼이 명령에 대 한 응답이 빈 상태로 표시 됩니다.

### <a name="example-response"></a>예제 응답

```bash
HTTP/2 202
content-length: 0
operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/Sample-project/sources/jobs/{JOB_ID_GUID}
x-envoy-upstream-service-time: 412
apim-request-id: dda23d2b-f110-4645-8bce-1a6f8d504b33
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Wed, 24 Nov 2021 02:47:53 GMT
```

### <a name="get-update-source-status"></a>업데이트 원본 상태 가져오기

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 기술 자료 배포 예측 URL 얻기 에 대한 **Language Studio**  >  **질문의** 값을 찾을 수  >    >  있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 가져올 대상이 될 프로젝트/기술 자료의 이름입니다.|
| `JOB-ID` | 프로그래밍 방식으로 소스를 업데이트 하는 `JOB-ID` 경우는 업데이트 소스 요청에 대 한 응답 헤더의 일부로 생성 `operation-location` 됩니다. 는 `JOB-ID` 의 끝에 있는 GUID입니다. `operation-location` 예: `operation-location: https://southcentralus.api.cognitive.microsoft.com/language/query-knowledgebases/projects/sample-proj1/sources/jobs/{THIS GUID IS YOUR JOB ID}` |

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/sources/jobs/{JOB-ID}?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```bash
{
  "createdDateTime": "2021-11-24T02:47:53+00:00",
  "expirationDateTime": "2021-11-24T08:47:53+00:00",
  "jobId": "{JOB-ID-GUID}",
  "lastUpdatedDateTime": "2021-11-24T02:47:56+00:00",
  "status": "succeeded",
  "resultUrl": "/knowledgebases/Sample-project"
}

```

## <a name="update-question-and-answer-pairs"></a>질문 및 답변 쌍 업데이트

이 예제에서는 기존 원본에 질문 답변 쌍을 추가합니다. 쿼리 본문에 전달하는 작업에 따라 이 쿼리를 통해 기존 질문 답변 쌍을 수정하거나 삭제할 수도 있습니다. 라는 원본이 없으면 `source5` 이 예제 쿼리가 실패합니다. 쿼리 본문의 원본 값을 대상 프로젝트에 대해 존재하는 원본으로 조정할 수 있습니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 가져올 대상이 될 프로젝트/기술 자료의 이름입니다.|

```bash
curl -X PATCH -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '[
    {
        "op": "add",
        "value":{
            "id": 1,
            "answer": "The latest question answering docs are on https://docs.microsoft.com",
            "source": "source5",
            "questions": [
                "Where do I find docs for question answering?"
            ],
            "metadata": {},
            "dialog": {
                "isContextOnly": false,
                "prompts": []
            }
        }
    }
]'  -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/qnas?api-version=2021-10-01'
```

질문 응답 쌍을 업데이트하는 호출에 성공하면 `Operation-Location` 업데이트 작업의 상태를 확인하는 데 사용할 수 있는 헤더가 반환됩니다. 대부분의 예제에서는 응답 헤더를 확인할 필요가 없으므로 항상 표시하지는 않았습니다. 응답 헤더를 검색하기 위해 curl 명령은 `-i` 를 사용합니다. 엔드포인트 주소 이전의 이 매개 변수가 없으면 이 명령에 대한 응답은 응답이 발생하지 않은 것처럼 비어 있는 것처럼 표시됩니다.

### <a name="example-response"></a>예제 응답

```bash
HTTP/2 202
content-length: 0
operation-location: https://southcentralus.api.cognitive.microsoft.com:443/language/query-knowledgebases/projects/Sample-project/qnas/jobs/{JOB-ID-GUID}
x-envoy-upstream-service-time: 507
apim-request-id: 
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Wed, 24 Nov 2021 03:16:01 GMT
```

### <a name="get-update-question-answer-pairs-status"></a>질문 응답 쌍 상태 업데이트 받기

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 질문 응답 쌍 업데이트의 대상이 되려는 프로젝트/기술 자료의 이름입니다.|
| `JOB-ID` | 질문 답변 쌍을 프로그래밍 방식으로 업데이트하면 `JOB-ID` 업데이트 요청에 대한 응답 헤더의 일부로 이 생성됩니다. `operation-location` 는 `JOB-ID` 의 끝에 있는 GUID입니다. `operation-location` 예: `operation-location: https://southcentralus.api.cognitive.microsoft.com/language/query-knowledgebases/projects/sample-proj1/qnas/jobs/{THIS GUID IS YOUR JOB ID}` |

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '' 'https://southcentralus.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/qnas/jobs/{JOB-ID}?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```bash
  "createdDateTime": "2021-11-24T03:16:01+00:00",
  "expirationDateTime": "2021-11-24T09:16:01+00:00",
  "jobId": "{JOB-ID-GUID}",
  "lastUpdatedDateTime": "2021-11-24T03:16:06+00:00",
  "status": "succeeded",
  "resultUrl": "/knowledgebases/Sample-project"
```

## <a name="update-synonyms"></a>동의어 업데이트

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 동의어를 추가하려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X GET -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '{
"value": [
    {
      "alterations": [
        "qnamaker",
        "qna maker"
      ]
    },
    {
      "alterations": [
        "botframework",
        "bot framework"
      ]
    }
  ]
}' -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/synonyms?api-version=2021-10-01'
```

### <a name="example-response"></a>예제 응답

```bash
0HTTP/2 200
content-length: 17
content-type: application/json; charset=utf-8
x-envoy-upstream-service-time: 39
apim-request-id: 5deb2692-dac8-43a8-82fe-36476e407ef6
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Wed, 24 Nov 2021 03:59:09 GMT

{
  "value": []
}
```

## <a name="update-active-learning-feedback"></a>활성 학습 피드백 업데이트

|변수 이름 | 값 |
|--------------------------|-------------|
| `ENDPOINT`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트의 예는 `https://southcentralus.api.cognitive.microsoft.com/` 입니다. 아래 코드 샘플의 엔드포인트인 경우 `southcentral` 나머지 엔드포인트 경로가 이미 존재하므로 의 지역 특정 부분만 추가하면 됩니다.|
| `API-KEY` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 항상 두 개의 유효한 키를 사용하면 가동 중지 시간이 0인 보안 키 회전을 허용합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `PROJECT-NAME` | 활성 학습 피드백 업데이트의 대상이 되려는 프로젝트/기술 자료의 이름입니다.|

### <a name="example-query"></a>예제 쿼리

```bash
curl -X POST -H "Ocp-Apim-Subscription-Key: {API-KEY}" -H "Content-Type: application/json" -d '{
records": [
    {
      "userId": "user1",
      "userQuestion": "hi",
      "qnaId": 1
    },
    {
      "userId": "user1",
      "userQuestion": "hello",
      "qnaId": 2
    }
  ]
}' -i 'https://{ENDPOINT}.api.cognitive.microsoft.com/language/query-knowledgebases/projects/{PROJECT-NAME}/feedback?api-version=2021-10-01' 
```

### <a name="example-response"></a>예제 응답

```bash
HTTP/2 204
x-envoy-upstream-service-time: 37
apim-request-id: 92225e03-e83f-4c7f-b35a-223b1b0f29dd
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
date: Wed, 24 Nov 2021 04:02:56 GMT
```
