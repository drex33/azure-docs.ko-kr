---
title: '빠른 시작: cURL 및 REST를 사용하여 기술 자료 관리 - 사용자 지정 질문 답변'
description: 이 빠른 시작에서는 REST API를 사용하여 기술 자료를 만들고, 게시하고, 쿼리하는 방법을 보여 줍니다.
ms.date: 11/16/2021
ms.topic: include
author: mrbullwinkle
ms.author: mbullwin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6de414a090145a91db92ad73b7bdc5e6e8eec16a
ms.sourcegitcommit: 024fcf9a76cf238e4bfbccfd699e97cc34fa1f42
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133190448"
---
## <a name="prerequisites"></a>사전 요구 사항

* 현재 버전의 [cURL](https://curl.haxx.se/). 빠른 시작에는 몇 가지 명령줄 스위치가 사용되며, 이러한 스위치는 [cURL 설명서](https://curl.haxx.se/docs/manpage.html)에 나와 있습니다.
* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 질문 답변 - API 키와 엔드포인트를 생성하기 위해 사용자 지정 질문 답변 기능이 사용하도록 설정된 [언어 리소스](https://ms.portal.azure.com/?quickstart=true#create/Microsoft.CognitiveServicesTextAnalytics)가 필요합니다.
    * 언어 리소스가 배포된 후 **리소스로 이동** 을 선택합니다. API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
* 쿼리할 기존 기술 자료. 기술 자료가 설치되지 않은 경우 [**Language Studio 빠른 시작**](../quickstart/sdk.md)의 지침을 따를 수 있습니다. 또는 이 [Surface 사용자 가이드 URL](https://download.microsoft.com/download/7/B/1/7B10C82E-F520-4080-8516-5CF0D803EEE0/surface-book-user-guide-EN.pdf)을 데이터 원본으로 사용하는 기술 자료를 추가합니다.

## <a name="query-a-knowledge-base"></a>기술 자료 쿼리

### <a name="generate-an-answer-from-a-knowledge-base"></a>기술 자료에서 답변 생성

REST API 및 cURL을 사용하여 질문 답변 프로젝트/기술 자료를 쿼리하려면 다음 정보를 확인해야 합니다.

|변수 이름 | 값 |
|--------------------------|-------------|
| `Endpoint`               | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 엔드포인트 예제는 `https://southcentralus.api.cognitive.microsoft.com/`입니다.|
| `API-Key` | 이 값은 Azure Portal에서 리소스를 검사할 때 **키 및 엔드포인트** 섹션에서 찾을 수 있습니다. Key1 또는 Key2를 사용할 수 있습니다. 가동 중지 시간이 0인 안전한 키 회전을 위해 항상 두 개의 유효한 키가 있어야 합니다. 또는 **Language Studio** > **질문 답변** > **기술 자료 배포** > **예측 URL 가져오기** 에서 값을 찾을 수 있습니다. 키 값은 샘플 요청의 일부입니다.|
| `Project` | 질문 답변 프로젝트의 이름입니다.|
| `Deployment`             | 두 개의 가능한 값은 `test` 및 `production`입니다. `production`은 **Language Studio** > **질문 답변** > **기술 자료 배포** 에서 기술 자료를 배포했는지 여부에 따라 달라집니다.|

cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 JSON 값과 JSON 크기를 사용하여 이 명령을 편집합니다.

```bash
curl -X POST -H "Ocp-Apim-Subscription-Key: {YOUR_API_KEY}" -H "Content-Type: application/json" -d '{
  "question": "How much battery life do I have left?"
  }'  'https://{YOUR_ENDPOINT}.api.cognitive.microsoft.com/language/:query-knowledgebases?projectName={YOUR_PROJECT_NAME}&api-version=2021-10-01&deploymentName={DEPLOYMENT_NAME}'
```

위의 코드를 실행할 때 필수 구성 요소의 데이터 원본을 사용하는 경우 다음과 같은 답변이 표시됩니다.

```json
{
"answers": [
    {
      "questions": [
        "Check battery level"
      ],
      "answer": "If you want to see how much battery you have left, go to **Start  **> **Settings  **> **Devices  **> **Bluetooth & other devices  **, then find your pen. The current battery level will appear under the battery icon.",
      "confidenceScore": 0.9185,
      "id": 101,
      "source": "https://support.microsoft.com/en-us/surface/how-to-use-your-surface-pen-8a403519-cd1f-15b2-c9df-faa5aa924e98",
      "metadata": {},
      "dialog": {
        "isContextOnly": false,
        "prompts": []
      }
    }
  ]
}
```

`confidenceScore`는 0과 1 사이의 값을 반환합니다. 이를 백분율로 생각하여 100을 곱합니다. 이 경우 0.9185의 신뢰도 점수는 질문 답변에서 기술 자료를 기반으로 한 질문에 대한 올바른 답변이라고 91.85% 확신한다는 것을 의미합니다.

신뢰도 점수가 특정 임계값 미만인 답변을 제외하려면 `confidenceScoreThreshold` 매개 변수를 추가하면 됩니다.

```bash
curl -X POST -H "Ocp-Apim-Subscription-Key: {YOUR_API_KEY}" -H "Content-Type: application/json" -d '{
  "question": "How much battery life do I have left?",
  "confidenceScoreThreshold": "0.95",
  }'  'https://{YOUR_ENDPOINT}.api.cognitive.microsoft.com//language/:query-knowledgebases?projectName=Sample-project&api-version=2021-10-01&deploymentName={DEPLOYMENT_NAME}'
```

이전 코드 실행에서 신뢰도 점수가 `.9185`임을 알고 있으므로 임계값을 `.95`로 설정하면 [기본 답변](../how-to/change-default-answer.md)이 반환됩니다.

```json
{
  "answers": [
    {
      "questions": [],
      "answer": "No good match found in KB",
      "confidenceScore": 0.0,
      "id": -1,
      "metadata": {}
    }
  ]
}
```

## <a name="query-text-without-a-knowledge-base"></a>기술 자료 없이 텍스트 쿼리

`query-text`를 통해 호출되는 미리 빌드된 질문 답변 REST API를 사용하여 기술 자료 없이 질문 답변을 사용할 수도 있습니다. 이 경우 요청이 보내지면 답변을 검색하려는 질문 및 관련 텍스트 레코드 모두를 질문 답변에 제공합니다.

다음 예제에서는 `API KEY` 및 `ENDPOINT`에 대한 변수만 수정하면 됩니다.

```bash
curl -X POST -H "Ocp-Apim-Subscription-Key: {YOUR_API_KEY}" -H "Content-Type: application/json" -d '{
"question":"How long does it takes to charge a surface?",
"records":[
{"id":"doc1","text":"Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you\u0027re using your Surface for power-intensive activities like gaming or video streaming while you\u0027re charging it"},
{"id":"doc2","text":"You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface."}],
"language":"en",
"stringIndexType":"Utf16CodeUnit"
}'  'https://{YOUR_ENDPOINT}.api.cognitive.microsoft.com/language/:query-text?&api-version=2021-10-01'
```

이 예제에서 반환하는 결과는 다음과 같습니다.

```json
{  
"answers": [
    {
      "answer": "Power and charging.It takes two to four hours to charge the Surface Pro 4 battery fully from an empty state. It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it",
      "confidenceScore": 0.9118788838386536,
      "id": "doc1",
      "answerSpan": {
        "text": "two to four hours",
        "confidenceScore": 0.9850527,
        "offset": 27,
        "length": 18
      },
      "offset": 0,
      "length": 243
    },
    {
      "answer": "It can take longer if you're using your Surface for power-intensive activities like gaming or video streaming while you're charging it",
      "confidenceScore": 0.052793052047491074,
      "id": "doc1",
      "answerSpan": {
        "text": "longer",
        "confidenceScore": 0.6694634,
        "offset": 11,
        "length": 7
      },
      "offset": 109,
      "length": 134
    },
    {
      "answer": "You can use the USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging, not for data transfer. If you want to use a USB device, plug it into the USB port on your Surface.",
      "confidenceScore": 0.017600709572434425,
      "id": "doc2",
      "answerSpan": {
        "text": "USB port on your Surface Pro 4 power supply to charge other devices, like a phone, while your Surface charges. The USB port on the power supply is only for charging",
        "confidenceScore": 0.1544854,
        "offset": 15,
        "length": 165
      },
      "offset": 0,
      "length": 280
    }
  ]
}
```