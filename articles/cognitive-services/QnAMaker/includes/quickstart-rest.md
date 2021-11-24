---
title: '빠른 시작: cURL 및 REST를 사용하여 기술 자료 관리 - QnA Maker'
description: 이 빠른 시작에서는 REST API를 사용하여 기술 자료를 만들고, 게시하고, 쿼리하는 방법을 보여 줍니다.
ms.date: 1/22/2021
ms.topic: quickstart
ms.custom: ignite-fall-2021, mode-other
ms.openlocfilehash: dabdfc693e0590bfcdaed0a3ce858c69f2ac0600
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133039722"
---
[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>사전 요구 사항

* 현재 버전의 [cURL](https://curl.haxx.se/). 빠른 시작에는 몇 가지 명령줄 스위치가 사용되며, 이러한 스위치는 [cURL 설명서](https://curl.haxx.se/docs/manpage.html)에 나와 있습니다.
* 키와 리소스 이름을 사용하려면 [QnA Maker 리소스](../how-to/set-up-qnamaker-service-azure.md?tabs=v1#create-a-new-qna-maker-service)가 있어야 합니다. 리소스를 만드는 동안 리소스 **이름** 을 입력한 다음, 키가 생성되었습니다. 리소스 이름은 엔드포인트의 하위 도메인으로 사용됩니다. 키와 리소스 이름을 검색하려면 Azure Portal에서 이 리소스에 대한 **빠른 시작** 을 선택합니다. 리소스 이름은 엔드포인트 URL의 첫 번째 하위 도메인입니다.

    `https://YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> 다음 BASH 예제에서는 `\` 줄 연속 문자를 사용합니다. 콘솔 또는 터미널에서 다른 줄 연속 문자를 사용하는 경우 이 문자를 사용하세요.

## <a name="create-a-knowledge-base"></a>기술 자료 만들기

REST API 및 cURL을 사용하여 기술 자료를 만드는 데 필요한 정보는 다음과 같습니다.

|정보|cURL 구성|목적|
|--|--|--|
|QnA Maker 리소스 이름|URL|URL을 생성하는 데 사용됨|
|QnA Maker 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|QnA Maker 서비스에 인증|
|기술 자료를 설명하는 JSON|`-d` 매개 변수|JSON [예제](/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples)|
|JSON 크기(바이트 단위)|`Content-Size` 헤더의 `-h` 매개 변수||

cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 JSON 값과 JSON 크기를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

QnA Maker의 cURL 응답에는 [작업 상태를 가져오는](#get-status-of-operation) 데 필요한 `operationId`가 포함됩니다.

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
|QnA Maker 리소스 이름|URL|URL을 생성하는 데 사용됨|
|작업 ID|URL 경로|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|QnA Maker 서비스에 인증|

cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 작업 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
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

기술 자료를 쿼리하기 전에 다음을 수행해야 합니다.
* 기술 자료 게시
* 런타임 엔드포인트 키 가져오기

이 작업은 기술 자료를 게시합니다. 런타임 엔드포인트 키를 가져오는 작업은 [별도의 작업](#get-published-knowledge-bases-runtime-endpoint-key)입니다.

|정보|cURL 구성|목적|
|--|--|--|
|QnA Maker 리소스 이름|URL|URL을 생성하는 데 사용됨|
|QnA Maker 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|QnA Maker 서비스에 인증|
|기술 자료 ID|URL 경로|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL 명령은 BASH 셸에서 실행됩니다. 리소스 이름, 리소스 키 및 기술 자료 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

응답 상태는 204이며 결과가 없습니다. cURL 명령에 대한 자세한 정보 출력을 확인하려면 `-v` 명령줄 매개 변수를 사용합니다. 여기에는 HTTP 상태가 포함됩니다.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>게시된 기술 자료의 런타임 엔드포인트 키 가져오기

기술 자료를 쿼리하기 전에 다음을 수행해야 합니다.
* 기술 자료 게시
* 런타임 엔드포인트 키 가져오기

이 작업은 런타임 엔드포인트 키를 가져옵니다. 기술 자료를 게시하는 작업은 [별도의 작업](#publish-knowledge-base)입니다.

런타임 엔드포인트 키는 QnA Maker 리소스를 사용하는 모든 기술 자료의 키와 동일합니다.

|정보|cURL 구성|목적|
|--|--|--|
|QnA Maker 리소스 이름|URL|URL을 생성하는 데 사용됨|
|QnA Maker 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|QnA Maker 서비스에 인증|

cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름과 리소스 키를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

cURL 응답에는 런타임 엔드포인트 키가 포함됩니다. 기술 자료에서 답변을 얻으려면 쿼리할 때 키 중 하나만 사용합니다.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>게시된 기술 자료의 답변에 대한 쿼리

기술 자료의 답변을 얻는 것은 기술 자료를 관리하는 것과 다른 별도의 런타임에서 수행됩니다. 별도의 런타임이므로 런타임 키를 사용하여 인증해야 합니다.

|정보|cURL 구성|목적|
|--|--|--|
|QnA Maker 리소스 이름|URL|URL을 생성하는 데 사용됨|
|QnA Maker 런타임 키|`Authorization` 헤더의 `-h` 매개 변수|키는 `Endpointkey ` 단어가 포함된 문자열의 일부입니다. QnA Maker 서비스에 인증|
|기술 자료 ID|URL 경로|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|쿼리를 설명하는 JSON|`-d` 매개 변수|JSON의 [요청 본문 매개 변수](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) 및 [예제](/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|JSON 크기(바이트 단위)|`Content-Size` 헤더의 `-h` 매개 변수||

cURL 명령은 BASH 셸에서 실행됩니다. 리소스 이름, 리소스 키 및 기술 자료 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

성공적인 응답에는 클라이언트 애플리케이션(예: 채팅 봇)에서 사용자에게 답변을 표시하는 데 필요한 다른 정보와 함께 상위 답변이 포함됩니다.

## <a name="delete-knowledge-base"></a>기술 자료 삭제

기술 자료를 사용하는 작업이 완료되면 해당 기술 자료를 삭제합니다.

|정보|cURL 구성|목적|
|--|--|--|
|QnA Maker 리소스 이름|URL|URL을 생성하는 데 사용됨|
|QnA Maker 리소스 키|`Ocp-Apim-Subscription-Key` 헤더의 `-h` 매개 변수|QnA Maker 서비스에 인증|
|기술 자료 ID|URL 경로|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

cURL 명령은 BASH 셸에서 실행됩니다. 리소스 이름, 리소스 키 및 기술 자료 ID를 사용하여 이 명령을 편집합니다.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

응답 상태는 204이며 결과가 없습니다. cURL 명령에 대한 자세한 정보 출력을 확인하려면 `-v` 명령줄 매개 변수를 사용합니다. 여기에는 HTTP 상태가 포함됩니다.

## <a name="additional-resources"></a>추가 리소스

* [작성](/rest/api/cognitiveservices/qnamaker4.0/knowledgebase) 참조 설명서
* [런타임](/rest/api/cognitiveservices/qnamaker4.0/runtime) 참조 설명서
* [cURL을 사용하는 BASH 스크립트 샘플](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)
