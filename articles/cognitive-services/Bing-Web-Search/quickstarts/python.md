---
title: '빠른 시작: Python을 사용하여 검색 수행 - Bing Web Search API'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing Web Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: quickstart
ms.date: 05/22/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python, mode-other
ms.openlocfilehash: 25af331f62c00af5c37e71176fedea98333d1071
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133053557"
---
# <a name="quickstart-use-python-to-call-the-bing-web-search-api"></a>빠른 시작: Python을 사용하여 Bing Web Search API 호출  

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing Web Search API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 Python 애플리케이션은 검색 요청을 API에 보내고 JSON 응답을 표시합니다. 이 애플리케이션은 Python으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

이 예제는 [MyBinder](https://mybinder.org)에서 Jupyter 노트북으로 실행됩니다. 실행하려면 바인더 배지 시작을 선택합니다.

[![바인더](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingWebSearchAPI.ipynb)

## <a name="prerequisites"></a>사전 요구 사항

* [Python 2.x 또는 3.x](https://www.python.org/)

[!INCLUDE [bing-web-search-quickstart-signup](../../../../includes/bing-web-search-quickstart-signup.md)]

## <a name="define-variables"></a>변수 정의

1. `subscription_key` 값을 Azure 계정의 유효한 구독 키로 바꿉니다.

   ```python
   subscription_key = "YOUR_ACCESS_KEY"
   assert subscription_key
   ```

2. Bing Web Search API 엔드포인트를 선언합니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

   ```python
   search_url = "https://api.bing.microsoft.com/v7.0/search"
   ```

3. 필요에 따라 `search_term`의 값을 바꿔 검색 쿼리를 사용자 지정합니다.

   ```python
   search_term = "Azure Cognitive Services"
   ```

## <a name="make-a-request"></a>요청하기

이 코드에서는 `requests` 라이브러리를 사용하여 Bing Web Search API를 호출하고 결과를 JSON 개체로 반환합니다. API 키는 `headers` 사전에 전달되고, 검색 용어와 쿼리 매개 변수는 `params` 사전에 전달됩니다. 

옵션 및 매개 변수의 전체 목록은 [Bing Web Search API v7](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)을 참조하세요.

```python
import requests

headers = {"Ocp-Apim-Subscription-Key": subscription_key}
params = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
response = requests.get(search_url, headers=headers, params=params)
response.raise_for_status()
search_results = response.json()
```

## <a name="format-and-display-the-response"></a>응답 형식 지정 및 표시

`search_results` 개체에는 검색 결과와 관련 쿼리 및 페이지와 같은 메타데이터가 포함됩니다. 이 코드는 `IPython.display` 라이브러리를 사용하여 응답 형식을 지정하고 브라우저에 해당 응답을 표시합니다.

```python
from IPython.display import HTML

rows = "\n".join(["""<tr>
                       <td><a href=\"{0}\">{1}</a></td>
                       <td>{2}</td>
                     </tr>""".format(v["url"], v["name"], v["snippet"])
                  for v in search_results["webPages"]["value"]])
HTML("<table>{0}</table>".format(rows))
```

## <a name="sample-code-on-github"></a>GitHub의 샘플 코드

이 코드를 로컬로 실행하려면 [GitHub에서 사용 가능한 전체 샘플](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingWebSearchv7.py)을 참조하세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Bing Web Search API 단일 페이지 앱 자습서](../tutorial-bing-web-search-single-page-app.md)

[!INCLUDE [bing-web-search-quickstart-see-also](../../../../includes/bing-web-search-quickstart-see-also.md)]
