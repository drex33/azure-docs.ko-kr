---
title: '빠른 시작: Python 및 Bing News Search REST API를 사용하여 뉴스 검색 수행'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 Python을 통해 Bing News Search REST API로 요청을 보내고 JSON 응답을 받습니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-news-search
ms.topic: quickstart
ms.date: 06/16/2020
ms.author: aahi
ms.custom: seodec2018, devx-track-python, mode-other
ms.openlocfilehash: bcbc5e7fa45214f3322d39631e06340f05de7ff8
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133051093"
---
# <a name="quickstart-perform-a-news-search-using-python-and-the-bing-news-search-rest-api"></a>빠른 시작: Python 및 Bing News Search REST API를 사용하여 뉴스 검색 수행

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

이 빠른 시작을 사용하여 Bing News Search API에 대한 첫 번째 호출을 수행할 수 있습니다. 이 간단한 Python 애플리케이션은 검색 쿼리를 API에 보내고 JSON 결과를 처리합니다. 

이 애플리케이션은 Python으로 작성되었지만, API는 대부분의 프로그래밍 언어와 호환되는 RESTful 웹 서비스입니다.

[MyBinder](https://mybinder.org)에서 이 코드 샘플을 Jupyter Notebook으로 실행하려면 **바인더 시작** 배지를 선택합니다. 

[![바인더 시작](https://mybinder.org/badge.svg)](https://mybinder.org/v2/gh/Microsoft/cognitive-services-notebooks/master?filepath=BingNewsSearchAPI.ipynb)

이 샘플의 소스 코드는 [GitHub](https://github.com/Azure-Samples/cognitive-services-REST-api-samples/blob/master/python/Search/BingNewsSearchv7.py)에서도 제공됩니다.

[!INCLUDE [cognitive-services-bing-news-search-signup-requirements](../../../includes/cognitive-services-bing-news-search-signup-requirements.md)]

## <a name="create-and-initialize-the-application"></a>애플리케이션 만들기 및 초기화

선호하는 IDE 또는 편집기에서 새 Python 파일을 만들고, 요청 모듈을 가져옵니다. 구독 키, 엔드포인트 및 검색어에 대한 변수를 만듭니다. 다음 코드에서 글로벌 엔드포인트를 사용하거나 리소스의 Azure Portal에 표시되는 [사용자 지정 하위 도메인](../../cognitive-services/cognitive-services-custom-subdomains.md) 엔드포인트를 사용할 수 있습니다.

```python
import requests

subscription_key = "your subscription key"
search_term = "Microsoft"
search_url = "https://api.cognitive.microsoft.com/bing/v7.0/news/search"
```

## <a name="create-parameters-for-the-request"></a>요청에 대한 매개 변수 만들기

키로 `Ocp-Apim-Subscription-Key`를 사용하여 구독 키를 새 사전에 추가합니다. 검색 매개 변수에 대해 동일한 작업을 수행합니다.

```python
headers = {"Ocp-Apim-Subscription-Key" : subscription_key}
params  = {"q": search_term, "textDecorations": True, "textFormat": "HTML"}
```

## <a name="send-a-request-and-get-a-response"></a>요청 보내기 및 응답 받기

1. 요청 라이브러리를 사용하여 사용자의 구독 키 및 이전 단계에서 만든 사전 개체를 통해 Bing Visual Search API를 호출합니다.

    ```python
    response = requests.get(search_url, headers=headers, params=params)
    response.raise_for_status()
    search_results = json.dumps(response.json())
    ```

2. `search_results`에 JSON 개체로 저장된 API의 응답에 포함된 문서에 대한 설명에 액세스합니다. 
    
    ```python
    descriptions = [article["description"] for article in search_results["value"]]
    ```

## <a name="display-the-results"></a>결과 표시

이러한 설명은 굵게 강조 표시된 검색 키워드를 사용하여 테이블로 렌더링될 수 있습니다.

```python
from IPython.display import HTML
rows = "\n".join(["<tr><td>{0}</td></tr>".format(desc)
                  for desc in descriptions])
HTML("<table>"+rows+"</table>")
```

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [단일 페이지 웹앱 만들기](tutorial-bing-news-search-single-page-app.md)
