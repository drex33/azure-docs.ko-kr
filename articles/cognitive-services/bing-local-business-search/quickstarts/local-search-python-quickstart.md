---
title: 빠른 시작 - Python의 API에 쿼리 보내기 - Bing Local Business Search
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python에서 Bing Local Business Search API 사용을 시작합니다.
services: cognitive-services
author: aahill
ms.author: aahi
manager: nitinme
ms.date: 05/12/2020
ms.topic: quickstart
ms.service: cognitive-services
ms.subservice: bing-local-business
ms.custom: devx-track-python, mode-api
ms.openlocfilehash: 1aea74fb93c21bf3612f4807d9b305cad43ccbb4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131051252"
---
# <a name="quickstart-send-a-query-to-the-bing-local-business-search-api-in-python"></a>빠른 시작: Python에서 Bing Local Business Search API에 쿼리 보내기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Azure Cognitive Service인 Bing Local Business Search API에 요청을 보내는 방법을 알아봅니다. 이 간단한 애플리케이션은 Python에서 작성되었지만 API는 RESTful 웹 서비스로, HTTP를 요청하고 JSON을 구문 분석할 수 있는 모든 프로그래밍 언어와 호환됩니다.

이 예제 애플리케이션은 검색 쿼리에 대한 API에서 로컬 응답 데이터를 가져옵니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* [Python](https://www.python.org/) 2.x 또는 3.x
* Azure 구독을 만든 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7"  title="Bing Search 리소스 만들기"  target="_blank">Bing Search 리소스 만들기</a>를 수행하여 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.

## <a name="run-the-complete-application"></a>전체 애플리케이션 실행

다음 예제에서는 다음 단계에서 구현되는 지역화된 결과를 가져옵니다.
1. 변수를 선언하여 호스트 및 경로별로 엔드포인트를 지정합니다.
2. 쿼리 매개 변수를 지정합니다. 
3. 요청을 만들고 `Ocp-Apim-Subscription-Key` 헤더를 추가하는 검색 기능을 정의합니다.
4. `Ocp-Apim-Subscription-Key` 헤더를 설정합니다. 
5. 연결하고 요청을 보냅니다.
6. JSON 결과를 인쇄합니다.

이 데모의 전체 코드는 다음과 같습니다.

```python
import http.client, urllib.parse
import json

# Replace the subscriptionKey string value with your valid subscription key.

> [!WARNING]
> Bing Search APIs are moving from Cognitive Services to Bing Search Services. Starting **October 30, 2020**, any new instances of Bing Search need to be provisioned following the process documented [here](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> Bing Search APIs provisioned using Cognitive Services will be supported for the next three years or until the end of your Enterprise Agreement, whichever happens first.
> For migration instructions, see [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
subscriptionKey = 'YOUR-SUBSCRIPTION-KEY'

host = 'api.cognitive.microsoft.com'
path = '/bing/v7.0/localbusinesses/search'

query = 'restaurant in Bellevue'

params = '?q=' + urllib.parse.quote (query) + '&mkt=en-us'

def get_local():
    headers = {'Ocp-Apim-Subscription-Key': subscriptionKey}
    conn = http.client.HTTPSConnection (host)
    conn.request ("GET", path + params, None, headers)
    response = conn.getresponse ()
    return response.read ()

result = get_local()
print (json.dumps(json.loads(result), indent=4))

```

## <a name="next-steps"></a>다음 단계
- [Local Business Search Java 빠른 시작](local-search-java-quickstart.md)
- [Local Business Search C# 빠른 시작](local-quickstart.md)
- [Local Business Search Node.js 빠른 시작](local-search-node-quickstart.md)
