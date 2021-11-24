---
title: '빠른 시작: 첫 번째 Bing Custom Search 인스턴스 만들기'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작을 사용하여 사용자가 정의하는 도메인 및 웹 페이지를 검색할 수 있는 사용자 지정 Bing 인스턴스를 만듭니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: quickstart
ms.date: 03/24/2020
ms.author: aahi
ms.custom: mode-other
ms.openlocfilehash: c023e6226b17d51014921920dedf36cb3b85b520
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133064658"
---
# <a name="quickstart-create-your-first-bing-custom-search-instance"></a>빠른 시작: 첫 번째 Bing Custom Search 인스턴스 만들기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

Bing Custom Search를 사용하려면 웹 보기 또는 조각을 정의하는 사용자 지정 검색 인스턴스를 만들어야 합니다. 이 인스턴스에는 사용자가 원할 수 있는 모든 순위 조정과 함께 검색하려는 공용 도메인, 웹 사이트 및 웹 페이지가 포함됩니다. 

인스턴스를 만들려면 [Bing Custom Search 포털](https://customsearch.ai)을 사용합니다. 

![Bing Custom Search 포털의 이미지](media/blockedCustomSrch.png)

## <a name="prerequisites"></a>전제 조건

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](../../../includes/cognitive-services-bing-custom-search-signup-requirements.md)]

## <a name="create-a-custom-search-instance"></a>사용자 지정 검색 인스턴스 만들기

Bing Custom Search 인스턴스를 만들려면

1. [Bing Custom Search 포털](https://customsearch.ai) 웹 페이지에서 **시작** 을 클릭하고 Microsoft 계정으로 로그인합니다.

2. **새 인스턴스** 를 클릭하고 설명이 포함된 이름을 입력합니다. 언제든지 인스턴스의 이름을 변경할 수 있습니다.
 
3. **검색 환경** 의 **활성** 탭에서 검색에 포함할 웹 사이트 하나 이상의 URL을 입력합니다. 

    > [!NOTE]
    > Bing Custom Search 인스턴스는 공개되고 Bing에 의해 인덱싱된 도메인 및 웹 페이지에 대한 결과를 반환합니다.

4. Bing Custom Search 포털의 오른쪽을 사용하여 쿼리를 입력하고 검색 인스턴스에서 반환된 검색 결과를 검사할 수 있습니다. 결과가 반환되지 않는 경우 다른 URL을 입력합니다.  

5. **게시** 를 클릭하여 프로덕션 환경에 대한 변경 내용을 게시하고 인스턴스의 엔드포인트를 업데이트합니다.

6.  **엔드포인트** 에서 **프로덕션** 탭을 클릭하고 **사용자 지정 구성 ID** 를 복사합니다. 이 ID는 호출에서 `customconfig=` 쿼리 매개 변수에 추가하여 Custom Search API를 호출하는 데 필요합니다.


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [빠른 시작: Bing Custom Search 엔드포인트 호출](./call-endpoint-csharp.md)
