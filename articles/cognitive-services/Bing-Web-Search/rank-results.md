---
title: 순위를 사용하여 검색 결과를 표시하는 방법 - Bing Web Search API
titleSuffix: Azure Cognitive Services
description: 순위를 사용하여 Bing Web Search API의 검색 결과를 표시하는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.assetid: BBF87972-B6C3-4910-BB52-DE90893F6C71
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 03/17/2019
ms.openlocfilehash: 4c7ffac31f5b5d6fecacb78487f8b7400d109e6d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128620544"
---
# <a name="how-to-use-ranking-to-display-bing-web-search-api-results"></a>순위를 사용하여 Bing Web Search API 결과를 표시하는 방법  

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

각 검색 응답에는 검색 결과를 표시하는 방법을 지정하는 [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse) 답변이 포함됩니다. 순위 응답은 일반적인 검색 결과 페이지에 대한 기본 줄 콘텐츠와 사이드바 콘텐츠를 기준으로 결과를 그룹화합니다. 일반적인 기본 줄과 사이드바 형식으로 결과를 표시하지 않는 경우 기본 줄 콘텐츠에 사이드바 콘텐츠보다 높은 가시성을 제공해야 합니다.  

각 그룹(기본 줄 또는 사이드바) 내에서 [항목](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankinggroup-items) 배열은 콘텐츠가 나타나는 순서를 식별합니다. 각 항목은 답변 내에서 결과를 식별하는 다음 두 가지 방법을 제공합니다.  

-   `answerType` 및 `resultIndex` - `answerType` 필드는 답변(예: 웹 페이지 또는 뉴스)을 식별하고, `resultIndex`는 답변 내의 결과(예: 뉴스 기사)를 식별합니다. 인덱스는 0부터 시작합니다.  

-   `value` — `value` 필드는 답변 또는 답변 내 결과의 ID와 일치하는 ID를 포함합니다. 답변 또는 결과 중 하나만 ID를 포함합니다.  

순위 ID를 답변 또는 결과 중 하나의 ID와 일치하기만 하면 되므로 ID를 사용하는 것이 더 간단합니다. 답변 개체에 `id` 필드가 포함된 경우 모든 답변의 결과를 함께 표시합니다. 예를 들어 `News` 개체에 `id` 필드가 포함된 경우 모든 뉴스 기사를 함께 표시합니다. `News` 개체에 `id` 필드가 포함되지 않은 경우에는 각 뉴스 기사에 `id` 필드가 포함되며, 순위 응답이 뉴스 기사와 다른 답변의 결과를 함께 사용합니다.  

`answerType` 및 `resultIndex` 사용은 좀 더 복잡합니다. `answerType`을 사용하여 표시할 결과가 포함된 답변을 식별합니다. 그런 다음, `resultIndex`로 답변 결과를 인덱싱하여 표시할 결과를 가져옵니다. (`answerType` 값은 [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) 개체의 필드 이름입니다.) 모든 대답의 결과를 함께 표시해야 하는 경우 순위 응답 항목에 `resultIndex` 필드가 포함되지 않습니다.  

## <a name="ranking-response-example"></a>순위 응답 예제

다음은 예제 [RankingResponse](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#rankingresponse)를 보여 줍니다. 웹 답변에는 `id` 필드가 포함되어 있지 않으므로 순위에 따라 모든 웹 페이지를 개별적으로 표시합니다(각 웹 페이지에 `id` 필드가 포함됨). 또한 이미지, 비디오 및 관련 검색 답변에는 `id` 필드가 포함되어 있으므로 순위에 따라 각 답변의 결과를 함께 표시합니다.

```json
{  
    "_type" : "SearchResponse",
    "webPages" : {
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
        "totalEstimatedMatches" : 835000,
        "value" : [
            {
                "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
                "name" : "Motor Sports - Live at the race track ...",
                "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                "displayUrl" : "www.contoso.com\/usa\/eventsandracing\/motorsport",
                "snippet" : "Here you will find detailed information about racing...",
                "deepLinks" : [{
                    "name" : "Customer Racing",
                    "url" : "http:\/\/www.bing.com\/cr?IG=96C4CF214A0A43...",
                    "snippet" : "Customer racing news; General news..."
            },
            . . .  
        ]  
    }],  
    "images" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/images...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "2016 Supercar Wallpapers",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2017-03-25T11:14:00",
                "contentUrl" : "http:\/\/www.contoso.com\/wall...",
                "hostPageUrl" : "http:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "contentSize" : "373283 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "http:\/\/www.contoso.com\/lmp-...",
                "width" : 1920,
                "height" : 1080,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "insightsSourcesSummary" : {
                    "shoppingSourcesCount" : 0,
                    "recipeSourcesCount" : 0
                }
            },
            . . .  
        ]  
    },  
    "relatedSearches" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches",
        "value" : [
            {
                "text" : "vintage racing teams",
                "displayText" : "vintage racing teams",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2..."
            },
            . . .  
        ]  
    },  
    "videos" : {
        "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos",
        "readLink" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/videos...",
        "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214A...",
        "isFamilyFriendly" : true,
        "value" : [
            {
                "name" : "Why We Race",
                "description" : "A new era begins in motorsports this weekend...",
                "webSearchUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF2...",
                "thumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OVP.Vo1...",
                "datePublished" : "2014-01-25T16:31:48",
                "publisher" : [
                    {
                        "name" : "Fabrikam"
                    }
                ],
                "contentUrl" : "https:\/\/www.fabrikam.com\/watch?v=oL...",
                "hostPageUrl" : "https:\/\/www.bing.com\/cr?IG=96C4CF214...",
                "encodingFormat" : "mp4",
                "hostPageDisplayUrl" : "https:\/\/www.fabrikam.com\/watch?v=oLAZgD...",
                "width" : 480,
                "height" : 360,
                "duration" : "PT2M42S",
                "motionThumbnailUrl" : "https:\/\/tse4.mm.bing.net\/th?id=OM...",
                "embedHtml" : "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www.you...<\/iframe>",
                "allowHttpsEmbed" : true,
                "viewCount" : 47325,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 168
                },
                "allowMobileEmbed" : true,
                "isSuperfresh" : false
            },
            . . .  
        ]  
    },  
    "rankingResponse" : {
        "mainline" : {
            "items" : [{
                "answerType" : "WebPages",
                "resultIndex" : 0,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0"
                }
            },
            {
                "answerType" : "Images",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Images"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 1,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.1"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 2,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.2"
                }
            },
            {
                "answerType" : "Videos",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#Videos"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 3,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.3"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 4,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.4"
                }
            },
            {
                "answerType" : "WebPages",
                "resultIndex" : 5,
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.5"
                }
            }]
        },
        "sidebar" : {
            "items" : [{
                "answerType" : "RelatedSearches",
                "value" : {
                    "id" : "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#RelatedSearches"
                }
            }]
        }
    }
}  
```  

이 순위 응답에 따라 기본 줄에는 다음 검색 결과가 표시됩니다.  

-   첫 번째 웹 페이지 결과
-   모든 이미지  
-   두 번째 및 세 번째 웹 페이지 결과  
-   모든 비디오  
-   네 번째, 다섯 번째, 여섯 번째 웹 페이지 결과  

또한 사이드바에는 다음 검색 결과가 표시됩니다.  

-   모든 관련 검색  


## <a name="next-steps"></a>다음 단계

순위가 지정되지 않은 결과의 수준을 올리는 방법에 대한 자세한 내용은 [순위가 지정되지 않은 답변의 수준 올리기](./filter-answers.md#promoting-answers-that-are-not-ranked)을 참조하세요.

응답에서 순위가 지정된 답변 수를 제한하는 방법에 대한 자세한 내용은 [응답의 답변 수 제한](./filter-answers.md#limiting-the-number-of-answers-in-the-response)을 참조하세요.

순위를 사용하여 결과를 표시하는 C# 예제는 [C# 순위 자습서](./csharp-ranking-tutorial.md)를 참조하세요.