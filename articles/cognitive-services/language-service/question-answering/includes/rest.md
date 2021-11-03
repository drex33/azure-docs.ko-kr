---
title: '빠른 시작: cURL 및 REST를 사용하여 기술 자료 관리 - 사용자 지정 질문 답변'
description: 이 빠른 시작에서는 REST API를 사용하여 기술 자료를 만들고, 게시하고, 쿼리하는 방법을 보여 줍니다.
ms.date: 11/02/2021
ms.topic: include
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2768081da14be2c481093d54c833f1cde45e36a9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131029792"
---
## <a name="prerequisites"></a>사전 요구 사항

* 현재 버전의 [cURL](https://curl.haxx.se/). 빠른 시작에는 몇 가지 명령줄 스위치가 사용되며, 이러한 스위치는 [cURL 설명서](https://curl.haxx.se/docs/manpage.html)에 나와 있습니다.
* 사용자 지정 질문 및 답변에는 API 키 및 엔드포인트를 생성하기 위해 사용 설정된 사용자 지정 질문 답변 기능이 있는 [언어 리소스](../../../qnamaker/how-to/set-up-qnamaker-service-azure.md?tabs=v2#create-a-new-qna-maker-service)가 필요합니다. 리소스를 만들 때 선택한 **이름** 은 엔드포인트의 하위 도메인으로 사용됩니다. 키와 리소스 이름을 검색하려면 Azure Portal에서 리소스에 대한 **빠른 시작** 을 선택합니다. 리소스 이름은 엔드포인트 URL의 첫 번째 하위 도메인입니다. <!--TODO: Change link-->

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2`

> [!CAUTION]
> 다음 BASH 예제에서는 `\` 줄 연속 문자를 사용합니다. 콘솔 또는 터미널에서 다른 줄 연속 문자를 사용하는 경우 이 문자를 사용하세요.

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

REST API 및 cURL을 사용하여 기술 자료를 만드는 데 필요한 정보는 다음과 같습니다.

|정보|cURL 구성|목적|
|--|--|--|
|언어 리소스 이름(사용자 지정 질문 답변 기능 사용)|URL|URL을 생성하는 데 사용됨|
|언어 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|언어 서비스에 인증|
|기술 자료를 설명하는 JSON|`-d` 매개 변수|JSON [예제](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples)|
|JSON 크기(바이트 단위)|`Content-Size` 헤더의 `-h` 매개 변수||

cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 JSON 값과 JSON 크기를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

사용자 지정 질문 답변의 cURL 응답에는 [작업 상태를 가져오는 데](#get-status-of-operation) 필요한 `operationId`이 포함됩니다.

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>작업 상태 가져오기

기술 자료를 만드는 경우 비동기 작업이므로 응답에는 상태를 확인하는 정보가 포함됩니다.

|정보|cURL 구성|목적|
|--|--|--|
|언어 리소스 이름(사용자 지정 질문 답변 기능 사용)|URL|URL을 생성하는 데 사용됨|
|작업 ID|URL 경로|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|언어 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|언어 서비스에 인증|

cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 작업 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

cURL 응답에는 상태가 포함됩니다. 작업 상태가 성공인 경우 `resourceLocation`에 기술 자료 ID가 포함됩니다.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>기술 자료 게시

기술 자료를 쿼리하기 전에 기술 자료를 게시해야 합니다.

|정보|cURL 구성|목적|
|--|--|--|
|언어 리소스 이름(사용자 지정 질문 답변 기능 사용)|URL|URL을 생성하는 데 사용됨|
|언어 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|언어 서비스에 인증|
|기술 자료 ID|URL 경로|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL 명령은 BASH 셸에서 실행됩니다. 리소스 이름, 리소스 키 및 기술 자료 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

응답 상태는 204이며 결과가 없습니다. cURL 명령에 대한 자세한 정보 출력을 확인하려면 `-v` 명령줄 매개 변수를 사용합니다. 여기에는 HTTP 상태가 포함됩니다.

## <a name="query-for-answer-from-published-knowledge-base"></a>게시된 기술 자료의 답변에 대한 쿼리

|정보|cURL 구성|목적|
|--|--|--|
|언어 리소스 이름(사용자 지정 질문 답변 기능 사용)|URL|URL을 생성하는 데 사용됨|
|언어 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|언어 서비스에 인증|
|기술 자료 ID|URL 경로|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|쿼리를 설명하는 JSON|`-d` 매개 변수|JSON의 [요청 본문 매개 변수](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) 및 [예제](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|JSON 크기(바이트 단위)|`Content-Size` 헤더의 `-h` 매개 변수||

cURL 명령은 BASH 셸에서 실행됩니다. 리소스 이름, 리소스 키 및 기술 자료 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

성공적인 응답에는 클라이언트 애플리케이션(예: 채팅 봇)에서 사용자에게 답변을 표시하는 데 필요한 다른 정보와 함께 상위 답변이 포함됩니다.

## <a name="delete-knowledge-base"></a>기술 자료 삭제

기술 자료를 사용하는 작업이 완료되면 해당 기술 자료를 삭제합니다.

|정보|cURL 구성|목적|
|--|--|--|
|언어 리소스 이름(사용자 지정 질문 답변 기능 사용)|URL|URL을 생성하는 데 사용됨|
|언어 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|언어 서비스에 인증|
|기술 자료 ID|URL 경로|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL 명령은 BASH 셸에서 실행됩니다. 리소스 이름, 리소스 키 및 기술 자료 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v5.0-preview.2/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

응답 상태는 204이며 결과가 없습니다. cURL 명령에 대한 자세한 정보 출력을 확인하려면 `-v` 명령줄 매개 변수를 사용합니다. 여기에는 HTTP 상태가 포함됩니다.

## <a name="additional-resources"></a>추가 리소스

* [작성](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) 참조 설명서
* [런타임](/rest/api/cognitiveservices/qnamaker4.0/runtime) 참조 설명서
* [cURL을 사용하는 BASH 스크립트 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)
