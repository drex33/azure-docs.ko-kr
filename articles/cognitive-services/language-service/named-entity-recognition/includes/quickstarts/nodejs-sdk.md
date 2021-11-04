---
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: devx-track-js, ignite-fall-2021
ms.openlocfilehash: 7287b294a66c2f7a08f0c5f09d7c58eddc03eb9c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131101173"
---
[참조 설명서](/javascript/api/overview/azure/ai-text-analytics-readme?preserve-view=true&view=azure-node-latest) | [라이브러리 소스 코드](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/textanalytics/ai-text-analytics) | [패키지(NPM)](https://www.npmjs.com/package/@azure/ai-text-analytics/v/5.1.0) | [샘플](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/textanalytics/ai-text-analytics/samples)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services)
* 현재 버전의 [Node.js](https://nodejs.org/)
* Azure 구독이 있으면 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="언어 리소스 만들기"  target="_blank">언어 리소스를 생성</a>하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
* 분석 기능을 사용하려면 표준(S) 가격 책정 계층을 사용하는 언어 리소스가 필요합니다.

## <a name="setting-up"></a>설치

### <a name="create-a-new-nodejs-application"></a>새 Node.js 애플리케이션 만들기

콘솔 창(예: cmd, PowerShell 또는 Bash)에서 앱에 대한 새 디렉터리를 만들고 이 디렉터리로 이동합니다. 

```console
mkdir myapp 

cd myapp
```

`package.json` 파일을 사용하여 노드 애플리케이션을 만들려면 `npm init` 명령을 실행합니다. 

```console
npm init
```

### <a name="install-the-client-library"></a>클라이언트 라이브러리 설치

NPM 패키지 설치:

```console
nnpm install @azure/ai-text-analytics@5.1.0
```

## <a name="code-example"></a>코드 예제

파일을 열고 아래 코드를 복사합니다. `key` 변수를 리소스의 키로 바꾸고 `endpoint` 변수를 리소스의 엔드포인트로 바꾸어야 합니다. 

[!INCLUDE [find the key and endpoint for a resource](../../../includes/find-azure-resource-info.md)]

```javascript
"use strict";

const { TextAnalyticsClient, AzureKeyCredential } = require("@azure/ai-text-analytics");
const key = '<paste-your-key-here>';
const endpoint = '<paste-your-endpoint-here>';
// Authenticate the client with your key and endpoint
const textAnalyticsClient = new TextAnalyticsClient(endpoint,  new AzureKeyCredential(key));

// Example method for recognizing entities in text
async function entityRecognition(client){

    const entityInputs = [
        "Microsoft was founded by Bill Gates and Paul Allen on April 4, 1975, to develop and sell BASIC interpreters for the Altair 8800",
        "La sede principal de Microsoft se encuentra en la ciudad de Redmond, a 21 kilómetros de Seattle."
    ];
    const entityResults = await client.recognizeEntities(entityInputs);

    entityResults.forEach(document => {
        console.log(`Document ID: ${document.id}`);
        document.entities.forEach(entity => {
            console.log(`\tName: ${entity.text} \tCategory: ${entity.category} \tSubcategory: ${entity.subCategory ? entity.subCategory : "N/A"}`);
            console.log(`\tScore: ${entity.confidenceScore}`);
        });
    });
}
entityRecognition(textAnalyticsClient);
```

## <a name="output"></a>출력

```console
Document ID: 0
        Name: Microsoft         Category: Organization  Subcategory: N/A
        Score: 0.29
        Name: Bill Gates        Category: Person        Subcategory: N/A
        Score: 0.78
        Name: Paul Allen        Category: Person        Subcategory: N/A
        Score: 0.82
        Name: April 4, 1975     Category: DateTime      Subcategory: Date
        Score: 0.8
        Name: 8800      Category: Quantity      Subcategory: Number
        Score: 0.8
Document ID: 1
        Name: 21        Category: Quantity      Subcategory: Number
        Score: 0.8
        Name: Seattle   Category: Location      Subcategory: GPE
        Score: 0.25
```
