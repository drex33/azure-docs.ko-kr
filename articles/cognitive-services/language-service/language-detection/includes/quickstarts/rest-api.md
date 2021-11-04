---
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: include
ms.date: 11/02/2021
ms.author: aahi
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4f39537abd292d7ef44ce0a49c37d087ba74ba12
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131100829"
---
[참조 설명서](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-1/)


## <a name="prerequisites"></a>사전 요구 사항

* 현재 버전의 [cURL](https://curl.haxx.se/).
* Azure 구독이 있으면 Azure Portal에서 <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics"  title="언어 리소스 만들기"  target="_blank">언어 리소스를 생성</a>하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.

> [!NOTE]
> * 다음 BASH 예제에서는 `\` 줄 연속 문자를 사용합니다. 콘솔 또는 터미널에서 다른 줄 연속 문자를 사용하는 경우 해당 문자를 사용하세요.
> * [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code)에서 언어별 샘플을 찾을 수 있습니다.
> * Azure Portal로 이동하여 필수 구성 요소에서 만든 언어 리소스의 키와 엔드포인트를 찾습니다. 리소스의 **키 및 엔드포인트** 페이지의 **리소스 관리** 아래에 있습니다. 그런 다음, 아래 코드의 문자열을 키와 엔드포인트로 바꿉니다.
API를 호출하려면 다음 정보가 필요합니다.


|매개 변수  |Description  |
|---------|---------|
|`-X POST <endpoint>`     | API에 액세스하기 위한 엔드포인트를 지정합니다.        |
|`-H Content-Type: application/json`     | JSON 데이터를 보내기 위한 콘텐츠 형식.          |
|`-H "Ocp-Apim-Subscription-Key:<key>`    | API에 액세스하기 위한 키를 지정합니다.        |
|`-d <documents>`     | 보내려는 문서가 포함된 JSON.         |

다음 cURL 명령은 BASH 셸에서 실행됩니다. 사용자 고유의 리소스 이름, 리소스 키 및 JSON 값을 사용하여 이 명령을 편집합니다.


## <a name="language-detection"></a>언어 검색

[!INCLUDE [REST API quickstart instructions](../../../includes/rest-api-instructions.md)]

```bash
curl -X POST https://<your-text-analytics-endpoint-here>/text/analytics/v3.1/languages/ \
-H "Content-Type: application/json" \
-H "Ocp-Apim-Subscription-Key: <your-text-analytics-key-here>" \
-d '{ documents: [{ id: "1", text: "This is a document written in English."}]}'
```

### <a name="json-response"></a>JSON 응답

```json
{
   "documents":[
      {
         "id":"1",
         "detectedLanguage":{
            "name":"English",
            "iso6391Name":"en",
            "confidenceScore":1.0
         },
         "warnings":[
            
         ]
      }
   ],
   "errors":[
      
   ],
   "modelVersion":"2021-01-05"
}
```
