---
title: 이미지 정보 가져오기 - Bing Image Search API
titleSuffix: Azure Cognitive Services
description: Bing Image Search API를 사용하여 이미지에 대한 자세한 정보를 가져오는 방법을 알아봅니다.
services: cognitive-services
manager: nitinme
ms.assetid: 0BCD936E-D4C0-472D-AE40-F4B2AB6912D5
ms.service: cognitive-services
ms.subservice: bing-image-search
ms.topic: conceptual
ms.date: 03/04/2019
ms.openlocfilehash: f4f9ce34f35b32c580a5cbf0f579765e55a4c2d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128672517"
---
# <a name="get-image-insights-with-the-bing-image-search-api"></a>Bing Image Search API를 사용하여 이미지 정보 가져오기

> [!WARNING]
> Bing Search API는 Cognitive Services에서 Bing Search Services로 이동합니다. **2020년 10월 30일** 부터 Bing Search의 모든 새 인스턴스는 [여기](/bing/search-apis/bing-web-search/create-bing-search-service-resource)에 설명된 프로세스에 따라 프로비저닝되어야 합니다.
> Cognitive Services를 사용하여 프로비저닝된 Bing Search API는 향후 3년 동안 또는 기업계약이 종료될 때까지(둘 중 먼저 도래할 때까지) 지원됩니다.
> 마이그레이션 지침은 [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource)를 참조하세요.

> [!IMPORTANT]
> 보다 포괄적인 인사이트를 제공하므로 이미지 인사이트를 가져오기 위해 /images/details 엔드포인트를 사용하는 대신 [Visual Search](../bing-visual-search/overview.md)를 사용해야 합니다.


각 이미지에는 이미지에 대한 정보를 가져오는 데 사용할 수 있는 인사이트 토큰이 포함됩니다. 예를 들어 관련된 이미지의 컬렉션, 이미지를 포함한 웹 페이지 또는 이미지에 표시된 제품을 구입할 수 있는 소매상 목록을 가져올 수 있습니다.  

이미지에 대한 인사이트를 얻으려면 응답에서 이미지의 [imageInsightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image-imageinsightstoken) 토큰을 캡쳐합니다.

```json
"value" : [{
        . . .
        "name":"sailing dinghy.jpg",
        "imageInsightsToken" : "mid_D6426898706EC7..."
        "insightsSourcesSummary" : {
            "shoppingSourcesCount" : 9,
            "recipeSourcesCount" : 0
        },
        . . .
}],
```

다음으로 이미지 세부 정보 엔드포인트를 호출하고 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) 쿼리 매개 변수를 `imageInsightsToken`의 토큰으로 설정합니다.  

가져올 인사이트를 지정하려면 `modules` 쿼리 매개 변수를 설정합니다. 모든 인사이트를 가져오려면 `modules`를 `All`로 설정합니다. 캡션 및 컬렉션 인사이트를 얻으려면 `modules`를 `Caption%2CCollection`으로 설정합니다. 가능한 인사이트의 전체 목록은 [모듈](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested)을 참조하세요. 일부 인사이트는 모든 이미지에 사용할 수 있습니다. 응답에는 사용 가능한 경우 요청한 모든 인사이트가 포함됩니다.

다음 예제에서는 이전 이미지에 사용 가능한 모든 인사이트를 요청합니다.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_D6426898706EC7...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-insights-of-a-known-image"></a>알려진 이미지의 인사이트 가져오기

인사이트를 얻으려는 이미지에 대한 URL이 있는 경우 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) 매개 변수 대신 [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) 쿼리 매개 변수를 사용하여 이미지를 지정합니다. 또는 이미지 파일이 있는 경우 POST 요청의 본문에 이미지의 이진 파일을 보낼 수 있습니다. POST 요청을 사용하는 경우 `Content-Type` 헤더를 `multipart/data-form`으로 설정해야 합니다. 옵션에서 이미지의 크기가 1MB를 초과할 수 없습니다.  

이미지에 대한 URL이 있는 경우 다음 예제에서는 이미지의 인사이트를 요청하는 방법을 보여줍니다.

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&imgUrl=https%3A%2F%2Fwww.mydomain.com%2Fimages%2Fsunflower.png&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

## <a name="getting-all-image-insights"></a>모든 이미지 인사이트 가져오기  

이미지의 모든 인사이트를 요청하려면 [모듈](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 쿼리 매개 변수를 `All`로 설정합니다. 관련된 검색을 가져오려면 요청에는 사용자의 쿼리 문자열이 포함되어야 합니다. 이 예제는 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken)사용하여 이미지를 지정하는 방법을 보여줍니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=sailing+dinghy&insightsToken=mid_68364D764J...&modules=All&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

최상위 개체는 [Images](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) 개체 대신 [ImageInsightsResponse](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imageinsightsresponse) 개체입니다.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "bcid_3297E6A54E4787A5F51C49D9BA342B9A*ccid_Fe2Hx...",
    "bestRepresentativeQuery" : {
        "text" : "Sailing Dinghy",
        "displayText" : "Sailing Dinghy",
        "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing+Dinghy...",
    },
    "pagesIncluding" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Powerboating Dublin, Dinghy Sailing Courses...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
                "datePublished" : "2017-01-20T00:41:00.0000000Z",
                "contentUrl" : "http:\/\/www.contoso.ie\/content...",
                "hostPageUrl" : "http:\/\/www.contoso.ie\/powerboating...",
                "contentSize" : "59063 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "www.contoso.ie\/powerboating...",
                "width" : 800,
                "height" : 600,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 225
                },
                "imageInsightsToken" : "ccid_pHjQIA0x*mid_17F61B1316A39C92214...",
                "imageId" : "17F61B1316A39C922143FFDE9DFB5B0FB41171",
                "accentColor" : "0997C2"
            },
            . . .
        ]
    },
    "relatedSearches" : {
        "value" : [
            {
                "text" : "Sailing Fun",
                "displayText" : "Sailing Fun",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?q=Sailing...",
                "thumbnail" : {
                    "url" : "https:\/\/tse1.mm.bing.net\/th?q=Sailing+Fun..."
                }
            },
            . . .
        ]
    },
    "visuallySimilarImages" : {
        "value" : [
            {
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=...",
                "name" : "Weekend On the Water",
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP...",
                "datePublished" : "2010-09-05T12:00:00.0000000Z",
                "contentUrl" : "http:\/\/1.bp.contoso.com\/_dc_6...",
                "hostPageUrl" : "http:\/\/contoso.com\/2010...",
                "contentSize" : "203806 B",
                "encodingFormat" : "jpeg",
                "hostPageDisplayUrl" : "contoso.com\/2010...",
                "width" : 1600,
                "height" : 1249,
                "thumbnail" : {
                    "width" : 300,
                    "height" : 234
                },
                "imageInsightsToken" : "ccid_Jg1Kwuc4*mid_5B7DA43976D3A422...",
                "imageId" : "5B7DA43976D3A422BA679A3AB019BB52C08DBC",
                "accentColor" : "0B2543"
            },
            . . .
        ]
    },
    "imageTags" : {
        "value" : [
            {
                "name" : "sail boat"
            },
            . . .
        ]
    }
}
```

## <a name="recognizing-entities-in-an-image"></a>이미지에서 엔터티 인식  

엔터티 인식 기능은 현재 사용자만인 이미지에서 엔터티를 식별합니다. 이미지에서 엔터티를 식별하려면 [모듈](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 쿼리 매개 변수를 `RecognizedEntities`로 설정합니다.  

> [!NOTE]
> 다른 모듈과 함께 이 모듈을 지정할 수 없습니다. 다른 모듈과 함께 이 모듈을 지정하면 응답에는 인식된 엔터티가 포함되지 않습니다.  

다음은 [imgUrl](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#imgurl) 매개 변수를 사용하여 이미지를 지정하는 방법을 보여줍니다. 쿼리 매개 변수는 URL로 인코드해야 합니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=faith+hill&insightsToken=mid_68364D764J...&modules=RecognizedEntities&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

다음은 이전 요청에 대한 응답을 보여줍니다. 이미지에 두 사람이 포함되기 때문에 응답은 각 사용자에 대한 지역을 식별합니다. 이 경우에 사람은 CelebrityAnnotations 및 CelebRecognitionAnnotations 그룹에서 인식될 수 없습니다. Bing은 원본 이미지의 사용자와 일치할 가능성에 따라 각 그룹의 사람을 나열합니다. 목록은 신뢰성의 내림차순입니다. CelebRecognitionAnnotations 그룹은 일치 항목이 정확한지에 대해 가장 높은 수준의 신뢰도를 제공합니다.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "recognizedEntityGroups" : {
        "value" : [
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebRecognitionAnnotations"
            },
            {
                "recognizedEntityRegions" : [...],
                "name" : "CelebrityAnnotations"
            }
        ]
    }
}
```

`region` 필드는 Bing이 엔터티를 인식할 수 있는 이미지의 영역을 식별합니다. 사용자에게 지역은 사람의 얼굴을 나타냅니다.  

사각형의 값은 원래 이미지의 높이와 너비를 기준으로 하고 범위는 0.0~1.0입니다. 예를 들어 이미지가 300x200이고 지역의 왼쪽 위 모서리가 (10, 20)이며 오른쪽 아래 모서리가 (290, 150)인 경우 정규화된 사각형은 다음과 같습니다.  

-   왼쪽: 10 / 300 = 0.03333...  
-   위쪽:  20 / 200 = 0.1  
-   오른쪽: 290 / 300 = 0.9667...  
-   아래쪽: 150 / 200 = 0.75  

Bing이 후속 인사이트 호출에서 반환하는 지역을 사용할 수 있습니다. 예를 들어 인식되는 엔터티의 이미지와 시각적 유사 항목을 가져옵니다. 자세한 내용은 시각적 유사 항목 및 엔터티 인식 모듈을 사용하기 위한 이미지 자르기를 참조하세요. 다음은 이미지를 자르는 데 사용하려는 지역 필드 및 쿼리 매개 변수 간의 매핑을 보여줍니다.  

-   왼쪽은 [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal)에 매핑됩니다.  
-   위쪽은 [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat)에 매핑됩니다.  
-   오른쪽은 [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car)에 매핑됩니다.  
-   아래쪽은 [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab)에 매핑됩니다.  

## <a name="finding-visually-similar-images"></a>시각적으로 유사한 이미지 찾기  

원본 이미지와 시각적으로 유사한 이미지를 찾으려면 [모듈](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 쿼리 매개 변수를 SimilarImages로 설정합니다.  

다음 요청은 시각적으로 유사한 이미지를 가져오는 방법을 보여줍니다. 요청은 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) 쿼리 매개 변수를 사용하여 원본 이미지를 식별합니다. 관련성을 향상시키려면 사용자의 쿼리 문자열을 포함해야 합니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?insightsToken=mid_68364D764J...&modules=SimilarImages&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```


다음은 이전 요청에 대한 응답을 보여줍니다.  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarImages" : {
        "value" : [
            {
                "name" : "typical Hawaiian Sunset! :) | Scenes of Hawaii",
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=detailv2...",
                "thumbnailUrl" : "https:\/\/tse1.mm.bing.net\/th?id=OIP.Mda2a86...",
                 . . .
            }
        ]
    }
```

## <a name="cropping-images-to-use-with-visually-similar-and-entity-recognition-modules"></a>시각적 유사 항목 및 엔터티 인식 모듈을 사용하기 위해 이미지 자르기  

Bing에서 사용하는 이미지의 지역을 지정하여 이미지가 시각적으로 유사한지 여부를 확인하거나 엔터티 인식을 수행하려면 [cal](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cal), [cat](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cat), [cab](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#cab) 및 [car](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#car) 쿼리 매개 변수를 사용합니다. 기본적으로 Bing은 전체 이미지를 사용합니다.  

매개 변수는 Bing이 비교에 사용하는 위쪽, 왼쪽 및 아래쪽, 오른쪽 지역을 지정합니다. 원본 이미지의 너비 및 높이의 분수로 값을 지정합니다. 소수 값은 왼쪽 위 모서리에서 (0.0, 0.0)으로 시작하고 오른쪽 아래 모서리에서 (1.0, 1.0)으로 끝납니다. 예를 들어 왼쪽 위 모퉁이가 위에서 아래쪽 1/4 및 왼쪽 1/4에서 시작되도록 지정하려면 `cal`을 0.25로 설정하고 `cat`을 0.25로 설정합니다.  

다음과 같은 시퀀스의 호출은 자르기 지역을 지정하는 영향을 보여줍니다. 첫 번째 호출에는 자르기가 포함되고 Bing은 가운데 이미지에서 두 사람이 나란히 서 있는 것을 인식합니다.  

```  
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1  
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```  

응답에서는 인식된 두 개의 엔터티를 보여줍니다.  

```json
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value": [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                },  
                {  
                    "region" : {  
                        "left" : 0.25,  
                        "top" : 0.2488889,  
                        "right" : 0.4466667,  
                        "bottom" : 0.5111111  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Marcus Appel",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
            "name" : "CelebRecognitionAnnotations"
        }]
    }  
}  
```  

두 번째 호출은 중간에서 수직으로 이미지를 잘라내고 Bing은 이미지의 오른쪽에서 한 사람을 인식합니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?cal=0.5&cat=0.0&car=1.0&cab=1.0&modules=RecognizedEntities&imgurl=https%3A%2F%2Ftse1.mm.bing.net%2Fth%3Fid%3DOIP.M0cbee6fadb43f35b2344e53da7a23ec1o0%26pid%3DApi&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

응답에서는 인식된 하나의 엔터티를 보여줍니다.  

```json  
{  
    "_type" : "ImageInsights",  
    "recognizedEntityGroups" : {
        "value" : [  
            . . .  
            {  
                "recognizedEntityRegions" : [{  
                    "region" : {  
                        "left" : 0.5066667,  
                        "top" : 0.1955556,  
                        "right" : 0.75,  
                        "bottom" : 0.52  
                    },  
                    "matchingEntities" : [{  
                        "entity" : {  
                            "_type" : "Person",  
                            "name" : "Charlene Whitney",  
                            . . .  
                        },  
                        "matchConfidence" : 0.9961388  
                    }]  
                }],  
                "name" : "CelebRecognitionAnnotations"  
            }
        ]
    }
}  
```  

## <a name="finding-visually-similar-products"></a>시각적으로 유사한 제품 찾기  

원본 이미지에서 찾은 제품과 시각적으로 유사한 제품을 포함하는 이미지 찾으려면 [모듈](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#modulesrequested) 쿼리 매개 변수를 SimilarProducts로 설정합니다.  

다음 요청은 시각적으로 유사한 제품의 이미지를 가져오는 방법을 보여줍니다. 요청은 [insightsToken](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#insightstoken) 쿼리 매개 변수를 사용하여 이전 요청에서 반환된 원본 이미지를 식별합니다. 관련성을 향상시키려면 사용자의 쿼리 문자열을 포함해야 합니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?q=anne+klein+dresses&modules=SimilarProducts&insightsToken=ccid_WOeyfoSp*mid_4B0A357&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

다음은 이전 요청에 대한 응답을 보여줍니다. 제품 등급 및 최저 가격을 찾을 수 있는지 여부와 상관 없이 응답에는 유사한 제품의 이미지가 포함되고 온라인으로 제품을 제공하는 소매상 수를 나타냅니다(`aggregateOffer` 필드 참조).  

```json
{
    "_type" : "ImageInsights",
    "imageInsightsToken" : "ccid_ldi5nX38*mid_29470780DE0E6F969...",
    "visuallySimilarProducts" : {
        "value" : [
            {
                "name" : "Sequin One-Shoulder Twist-Drape Dress",  
                "webSearchUrl" : "https:\/\/www.bing.com\/images\/search?view=de...",  
                "thumbnailUrl" : "https:\/\/tse2.mm.bing.net\/th?id=OIP.M85bdee...",  
                . . .
            },
            . . .
        ]
    }
}
```

온라인으로 제품을 제공하는 소매상 목록을 가져오려면([offerCount](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference) 필드 참조) API를 다시 호출하고 `modules`를 ShoppingSources로 설정합니다. 그런 다음, `insightsToken` 쿼리 매개 변수를 제품 요약 이미지에서 찾은 토큰으로 설정합니다.  

```
GET https://api.cognitive.microsoft.com/bing/v7.0/images/details?modules=ShoppingSources&insightsToken=ccid_hb3uRvUk*mid_BF5C252A47F2C765...&mkt=en-us HTTP/1.1    
Ocp-Apim-Subscription-Key: 123456789ABCDE  
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows Phone 8.0; Trident/6.0; IEMobile/10.0; ARM; Touch; NOKIA; Lumia 822)  
X-MSEdge-ClientIP: 999.999.999.999  
X-Search-Location: lat:47.60357;long:-122.3295;re:100  
X-MSEdge-ClientID: <blobFromPriorResponseGoesHere>  
Host: api.cognitive.microsoft.com
```

다음은 이전 요청에 대한 응답입니다.  

```json  
{  
    "_type" : "ImageInsights",  
    "shoppingSources" : {  
        "offers" : [{  
            "url" : "http:\/\/www.contoso.com\/dp\/B00O...",  
            "seller" : {  
                "name" : "Contoso",  
                "image" : {  
                    "url" : "https:\/\/tse3.mm.bing.net\/th?id=A10d50fe..."  
                }  
            },  
            "price" : 126.87,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        },  
        {  
            "url" : "http:\/\/www.adatum.com\/product\/heritage...\/",  
            "seller" : {  
                "name" : "fabrikam.com"  
            },  
            "price" : 495,  
            "priceCurrency" : "USD",  
            "availability" : "InStock"  
        }]  
    }  
}  
```