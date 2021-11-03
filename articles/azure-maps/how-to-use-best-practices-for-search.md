---
title: Azure Maps 검색 서비스에 대한 모범 사례 | Microsoft Azure Maps
description: Microsoft Azure Maps의 Search Service를 사용할 때 모범 사례를 적용하는 방법에 대해 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: f9d276c9663b294f3c9a863ab0eb54b2ba2b19cc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131435227"
---
# <a name="best-practices-for-azure-maps-search-service"></a>Azure Maps Search Service에 대한 모범 사례

Azure Maps [Search Service](/rest/api/maps/search)에는 개발자가 주소, 위치, 비즈니스 목록을 이름 또는 범주별로 검색하는 데 도움이 되는 다양한 기능을 제공하는 API와 기타 지리적 정보가 포함되어 있습니다. 예를 들어 [유사 항목 검색 API](/rest/api/maps/search/getsearchfuzzy)를 사용하면 사용자가 주소 또는 POI(관심 지점)를 검색할 수 있습니다.

이 문서에서는 Azure Maps Search Service에서 데이터를 호출할 때 올바른 사례를 적용하는 방법을 설명합니다. 이 문서에서 배울 내용은 다음과 같습니다.
> [!div class="checklist"]
>
> * 관련 일치 항목을 반환하는 쿼리 작성
> * 검색 결과 제한
> * 결과 형식 간 차이점 파악
> * 주소 검색-응답 구조 읽기

## <a name="prerequisites"></a>사전 요구 사항

1. [Azure Maps 계정을 만듭니다](quick-demo-map-app.md#create-an-azure-maps-account).
2. 기본 키 또는 구독 키라고도 하는 [기본 구독 키를 가져옵니다](quick-demo-map-app.md#get-the-primary-key-for-your-account).

이 문서에서는 [Postman 앱](https://www.postman.com/downloads/)을 사용하여 REST 호출을 빌드하지만 모든 API 개발 환경을 선택할 수 있습니다.

## <a name="best-practices-to-geocode-addresses"></a>지역 코드 주소에 대한 모범 사례

Azure Maps Search Service를 사용하여 전체 또는 부분 주소를 검색하면 API가 검색 쿼리에서 키워드를 읽습니다. 그런 다음, 주소의 경도 및 위도 좌표를 반환합니다. 이 프로세스를 *지오코딩* 이라고 합니다.

국가/지역의 지역 코드 기능은 도로 데이터의 가용성과 지오코딩 서비스의 정밀도에 따라 달라집니다. 국가 또는 지역별 Azure Maps 지오코딩 기능에 대한 자세한 내용은 [지오코딩 검사](./geocoding-coverage.md)를 참조하세요.

### <a name="limit-search-results"></a>검색 결과 제한

 Azure Maps Search API를 사용하면 검색 결과를 적절하게 제한할 수 있습니다. 사용자에게 관련된 데이터를 표시할 수 있도록 결과를 제한합니다.

> [!NOTE]
> Search API는 이 문서에서 설명하는 것보다 많은 매개 변수를 지원합니다.

#### <a name="geobiased-search-results"></a>지오바이어스 검색 결과

사용자에 대한 관련 영역에 지오바이어싱하려면 항상 가능한 한 많은 위치 정보를 추가합니다. 몇 가지 입력 유형을 지정하여 검색 결과를 제한할 수 있습니다.

* `countrySet` 매개 변수를 설정합니다. 예를 들어 `US,FR`로 설정할 수 있습니다. 기본적으로 API는 전 세계를 검색하므로 불필요한 결과를 반환할 수 있습니다. 쿼리에 `countrySet` 매개 변수가 없으면 부정확한 검색 결과가 반환될 수 있습니다. 예를 들어 *Bellevue* 라는 도시를 검색하면 미국과 프랑스에 *Bellevue* 라는 도시가 있으므로 두 국가/지역에서 결과가 반환됩니다.

* `btmRight` 및 `topleft` 매개 변수를 사용하여 경계 상자를 설정할 수 있습니다. 이들 매개 변수는 맵의 특정 영역으로 검색을 제한합니다.

* 결과의 관련 영역에 영향을 주려면 `lat` 및 `lon` 좌표 매개 변수를 정의합니다. `radius` 매개 변수를 사용하여 검색 영역의 반경을 설정합니다.

#### <a name="fuzzy-search-parameters"></a>유사 항목 검색 매개 변수

검색 쿼리의 사용자 입력을 모르는 경우 Azure Maps [Search Fuzzy API](/rest/api/maps/search/getsearchfuzzy)를 사용하는 것이 좋습니다. 예를 들어 사용자의 입력은 주소 또는 *쇼핑몰* 과 같은 POI(관심 지점)의 형식일 수 있습니다. 이 API는 POI 검색과 지오코딩을 정식 *한 줄 검색* 으로 결합합니다. 

* `minFuzzyLevel` 및 `maxFuzzyLevel` 매개 변수는 쿼리 매개 변수가 사용자가 원하는 정보와 정확히 일치하지 않는 경우에도 관련 일치 항목을 반환하는 데 도움이 됩니다. 성능을 최대화하고 비정상적인 결과를 줄이려면 검색 쿼리를 기본값 `minFuzzyLevel=1` 및 `maxFuzzyLevel=2`로 설정합니다.

    예를 들어 `maxFuzzyLevel` 매개 변수를 2로 설정하면 검색어 *restrant* 가 *restaurant* 와 일치합니다. 필요할 때 기본 유사 항목 수준을 재정의할 수 있습니다.

* `idxSet` 매개 변수를 사용하여 정확한 결과 형식 집합의 우선 순위를 지정합니다. 정확한 결과 집합의 우선 순위를 지정하려면 쉼표로 구분된 인덱스 목록을 제출하면 됩니다. 목록에서 항목 순서는 중요하지 않습니다. Azure Maps는 다음과 같은 인덱스를 지원합니다.

* `Addr` - **주소 범위**: 거리의 시작과 끝에서 보간되는 주소 지점입니다. 이 지점은 주소 범위로 표시됩니다.
* `Geo` - **지역**: 토지의 행정 구역입니다. 예를 들어 지리는 국가/지역, 시/도 또는 구/군/시일 수 있습니다.
* `PAD` - **지점 주소**: 거리 이름과 번지를 포함한 주소입니다. 인덱스에서 지점 주소를 찾을 수 있습니다. 예는 *Soquel Dr 2501* 입니다. 지점 주소는 주소에 사용 가능한 가장 높은 수준의 정확도를 제공합니다.  
* `POI` - **관심 지점**: 지도에서 주목할 만하거나 관심이 있을 것으로 간주되는 지점입니다. [Search Address API](/rest/api/maps/search/getsearchaddress)가 POI를 반환하지 않습니다.  
* `Str` - **거리**: 지도상의 거리입니다.
* `XStr` - **교차로**: 두 거리가 교차하는 지점이나 장소입니다.

#### <a name="usage-examples"></a>사용 예

* `idxSet=POI` - POI만 검색합니다.

* `idxSet=PAD,Addr` - 주소만 검색합니다. `PAD`는 지점 주소를 나타내고, `Addr`는 주소 범위를 나타냅니다.

### <a name="reverse-geocode-and-filter-for-a-geography-entity-type"></a>지리 엔터티 형식에 대한 역방향 지역 코드 및 필터

[Search Address Reverse API](/rest/api/maps/search/getsearchaddressreverse)에서 역방향 지역 코드 검색을 수행하면 서비스에서 관리 영역에 대한 다각형을 반환할 수 있습니다. 예를 들어 도시에 대한 영역 다각형을 가져올 수 있습니다.  특정 지리 엔터티 형식으로 검색 범위를 좁히려면 요청에 `entityType` 매개 변수를 포함합니다.

결과 응답에는 지리 ID와 일치하는 엔터티 형식이 포함됩니다. 둘 이상의 엔티티를 제공하면 엔드포인트는 *사용 가능한 가장 작은 엔티티* 를 반환합니다. 반환된 기하 도형 ID를 사용하여 [Search Polygon 서비스](/rest/api/maps/search/getsearchpolygon)를 통해 지리의 기하 도형을 얻을 수 있습니다.

#### <a name="sample-request"></a>샘플 요청

```HTTP
https://atlas.microsoft.com/search/address/reverse/json?api-version=1.0&subscription-key={Your-Azure-Maps-Primary-Subscription-key}&query=47.6394532,-122.1304551&language=en-US&entityType=Municipality
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "queryTime": 14,
        "numResults": 1
    },
    "addresses": [
        {
            "address": {
                "routeNumbers": [],
                "countryCode": "US",
                "countrySubdivision": "WA",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "municipality": "Redmond",
                "country": "United States",
                "countryCodeISO3": "USA",
                "countrySubdivisionName": "Washington"
            },
            "position": "47.639454,-122.130455",
            "dataSources": {
                "geometry": {
                    "id": "00005557-4100-3c00-0000-0000596ae571"
                }
            },
            "entityType": "Municipality"
        }
    ]
}
```

### <a name="set-the-results-language"></a>결과 언어 설정

`language` 매개 변수를 사용하여 반환된 검색 결과의 언어를 설정합니다. 요청에서 언어를 설정하지 않으면 Search Service는 기본적으로 국가 또는 지역에서 가장 많이 사용되는 언어를 사용합니다. 지정된 언어로 사용할 수 있는 데이터가 없으면 기본 언어가 사용됩니다.

자세한 내용은 [Azure Maps 지원되는 언어](./supported-languages.md)를 참조하세요.


### <a name="use-predictive-mode-automatic-suggestions"></a>예측 모드 사용(자동 제안)

부분 쿼리와 일치하는 항목을 더 찾으려면 `typeahead` 매개 변수를 `true`로 설정합니다. 이 쿼리는 부분 입력으로 해석되며 검색은 예측 모드로 전환됩니다. `typeahead` 매개 변수를 `true`로 설정하지 않으면 서비스에서는 모든 관련 정보가 전달된 것으로 간주합니다.

다음 샘플 쿼리에서는 *Microso* 에 대해 Search Address 서비스를 쿼리합니다. 여기에서 `typeahead` 매개 변수는 `true`로 설정됩니다. 응답은 검색 서비스가 쿼리를 부분 쿼리로 해석했음을 보여줍니다. 응답에는 자동으로 제안된 쿼리에 대한 결과가 포함됩니다.

#### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&typeahead=true&countrySet=US&lat=47.6370891183&lon=-122.123736172&query=Microsoft
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "query": "microsoft",
        "queryType": "NON_NEAR",
        "queryTime": 18,
        "numResults": 7,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6370891183,
            "lon": -122.123736172
        }
    },
    "results": [
        {
            "type": "Street",
            "id": "US/STR/p0/9438784",
            "score": 2.594099998474121,
            "dist": 314.0590106663596,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
            },
            "position": {
                "lat": 47.63988,
                "lon": -122.12438
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64223,
                    "lon": -122.1256,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63748,
                    "lon": -122.12309,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1756074",
            "score": 2.592679977416992,
            "dist": 876.0272035824189,
            "address": {
                "streetName": "Microsoft Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64032,
                "lon": -122.1344
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64253,
                    "lon": -122.13535,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63816,
                    "lon": -122.13305,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/1470668",
            "score": 2.5290400981903076,
            "dist": 2735.4883918101486,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Bellevue, WA 98007"
            },
            "position": {
                "lat": 47.65784,
                "lon": -122.14335
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.65785,
                    "lon": -122.14335,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65784,
                    "lon": -122.14325,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/12812615",
            "score": 2.527509927749634,
            "dist": 2870.9579016916873,
            "address": {
                "streetName": "Microsoft West Campus Road",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft West Campus Road, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.66034,
                "lon": -122.1404
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.66039,
                    "lon": -122.14325,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.65778,
                    "lon": -122.13749,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/197588",
            "score": 2.4630401134490967,
            "dist": 878.1404663812472,
            "address": {
                "streetName": "157th Avenue Northeast",
                "municipalitySubdivision": "Redmond",
                "municipality": "Redmond",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98052",
                "extendedPostalCode": "980525344, 980525398, 980525399",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "157th Avenue Northeast, Redmond, WA 98052"
            },
            "position": {
                "lat": 47.64351,
                "lon": -122.13056
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.64473,
                    "lon": -122.13058,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.6425,
                    "lon": -122.13016,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/3033991",
            "score": 2.0754499435424805,
            "dist": 3655467.8844475765,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Yorkmount, Charlotte",
            },
            "position": {
                "lat": 35.14267,
                "lon": -80.91824
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14287,
                    "lon": -80.91839,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14267,
                    "lon": -80.91814,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/8395877",
            "score": 2.0754499435424805,
            "dist": 3655437.0037482483,
            "address": {
                "streetName": "Microsoft Way",
                "municipalitySubdivision": "Charlotte",
                "municipality": "Charlotte",
                "countrySecondarySubdivision": "Mecklenburg",
                "countryTertiarySubdivision": "Township 1 Charlotte",
                "countrySubdivision": "NC",
                "countrySubdivisionName": "North Carolina",
                "postalCode": "28273",
                "extendedPostalCode": "282738105, 282738106, 282738108, 2827382, 282738200",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Microsoft Way, Charlotte, NC 28273"
            },
            "position": {
                "lat": 35.14134,
                "lon": -80.9198
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 35.14274,
                    "lon": -80.92159,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 35.14002,
                    "lon": -80.91824,
                    "valid": true
                }
            }
        }
    ]
}
```


### <a name="encode-a-uri-to-handle-special-characters"></a>특수 문자 처리를 위한 URI 인코딩 

교차로 주소를 찾으려면 URI를 인코딩하여 주소에서 특수 문자를 처리해야 합니다. 주소 예로 *1st Avenue & Union Street, Seattle* 을 살펴보겠습니다. 여기에서 요청을 보내기 전에 앰퍼샌드 문자(`&`)를 인코딩합니다.

문자 데이터를 URI로 인코딩하는 것이 좋습니다. URI에서는 백분율 기호(`%`)와 문자의 UTF-8 코드에 해당하는 2자리 16진수 값을 사용하여 모든 문자를 인코딩합니다.

#### <a name="usage-examples"></a>사용 예

다음 주소로 시작합니다.

```
query=1st Avenue & E 111th St, New York
```

주소를 인코딩합니다.

```
query=1st%20Avenue%20%26%20E%20111th%20St%2C%20New%20York
```

다음 방법을 사용할 수 있습니다.

JavaScript 또는 TypeScript:

```javascript
encodeURIComponent(query)
```

C# 또는 Visual Basic:

```csharp
Uri.EscapeDataString(query)
```

Java:

```Java
URLEncoder.encode(query, "UTF-8") 
```

Python:

```Python
import urllib.parse 
urllib.parse.quote(query)
```

C++:

```C++
#include <curl/curl.h>
curl_easy_escape(query)
```

PHP:

```PHP
urlencode(query)
```

Ruby:

```Ruby
CGI::escape(query) 
```

Swift:

```Swift
query.stringByAddingPercentEncodingWithAllowedCharacters(.URLHostAllowedCharacterSet()) 
```

Go:

```Go
import ("net/url") 
url.QueryEscape(query)
```

## <a name="best-practices-for-poi-searching"></a>POI 검색 관련 모범 사례

POI 검색에서 이름으로 POI 결과를 요청할 수 있습니다. 예를 들어 이름을 기준으로 비즈니스를 검색할 수 있습니다.

애플리케이션에 적용 범위가 필요한 국가/지역을 지정하려면 `countrySet` 매개 변수를 사용하는 것이 좋습니다. 기본 동작은 전 세계를 검색하는 것입니다. 이 광범위한 검색은 불필요한 결과를 반환할 수 있으며 검색에 오랜 시간이 걸릴 수 있습니다.

### <a name="brand-search"></a>브랜드 검색

POI 검색 응답에는 응답의 결과와 정보의 관련성을 높이기 위해 브랜드 정보가 포함됩니다. 이 정보를 사용하여 응답을 추가로 구문 분석할 수 있습니다.

요청에서 쉼표로 구분된 브랜드 이름 목록을 제출할 수 있습니다. `brandSet` 매개 변수를 설정하여 결과를 특정 브랜드로 제한하려면 목록을 사용합니다. 목록에서 항목 순서는 중요하지 않습니다. 여러 브랜드 목록을 제공하는 경우 반환되는 결과는 하나 이상의 목록에 속해야 합니다.

브랜드 검색을 살펴보기 위해 [POI 범주 검색](/rest/api/maps/search/getsearchpoicategory) 요청을 만들어 보겠습니다. 다음 예에서는 Washington의 Redmond에 있는 Microsoft 캠퍼스 근처의 주유소를 찾습니다. 응답은 반환된 각 POI의 브랜드 정보를 보여줍니다.

#### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&query=gas%20station&limit=3&lat=47.6413362&lon=-122.1327968
```

#### <a name="response"></a>응답

```JSON
{
    "summary": {
        "query": "gas station",
        "queryType": "NON_NEAR",
        "queryTime": 276,
        "numResults": 3,
        "offset": 0,
        "totalResults": 762680,
        "fuzzyLevel": 1,
        "geoBias": {
            "lat": 47.6413362,
            "lon": -122.1327968
        }
    },
    "results": [
        {
            "type": "POI",
            "id": "US/POI/p0/8831765",
            "score": 5.6631999015808105,
            "dist": 1037.0280221303253,
            "info": "search:ta:840531000004190-US",
            "poi": {
                "name": "Chevron",
                "phone": "+(1)-(425)-6532200",
                "brands": [
                    {
                        "name": "Chevron"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.chevron.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2444",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Northeast Bellevue, Bellevue",
            },
            "position": {
                "lat": 47.63201,
                "lon": -122.13281
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63291,
                    "lon": -122.13414,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63111,
                    "lon": -122.13148,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63222,
                        "lon": -122.13312,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831752",
            "score": 5.662710189819336,
            "dist": 1330.1278248163273,
            "info": "search:ta:840539001100326-US",
            "poi": {
                "name": "76",
                "phone": "+(1)-(425)-7472126",
                "brands": [
                    {
                        "name": "76"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.76.com",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "2421",
                "streetName": "148Th Ave Ne",
                "municipalitySubdivision": "Redmond, Bridle Trails, Bellevue",
                "municipality": "Redmond, Bellevue",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle East",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98007",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "2421 148Th Ave Ne, Bellevue, WA 98007",
                "localName": "Bellevue"
            },
            "position": {
                "lat": 47.63187,
                "lon": -122.14365
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.63277,
                    "lon": -122.14498,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.63097,
                    "lon": -122.14232,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "minor",
                    "position": {
                        "lat": 47.63187,
                        "lon": -122.14374,
                        "valid": true
                    }
                },
                {
                    "type": "main",
                    "position": {
                        "lat": 47.63186,
                        "lon": -122.14313,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "POI",
            "id": "US/POI/p0/8831764",
            "score": 5.662449836730957,
            "dist": 1458.645407416307,
            "info": "search:ta:840539000488527-US",
            "poi": {
                "name": "BROWN BEAR CAR WASH",
                "phone": "+(1)-(425)-6442868",
                "brands": [
                    {
                        "name": "Texaco"
                    }
                ],
                "categorySet": [
                    {
                        "id": 7311
                    }
                ],
                "url": "www.texaco.com/",
                "categories": [
                    "petrol station"
                ],
                "classifications": [
                    {
                        "code": "PETROL_STATION",
                        "names": [
                            {
                                "nameLocale": "en-US",
                                "name": "petrol station"
                            }
                        ]
                    }
                ]
            },
            "address": {
                "streetNumber": "15248",
                "streetName": "Bel Red Rd",
                "municipalitySubdivision": "Redmond",
            },
            "position": {
                "lat": 47.62843,
                "lon": -122.13628
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62933,
                    "lon": -122.13761,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62753,
                    "lon": -122.13495,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.62827,
                        "lon": -122.13628,
                        "valid": true
                    }
                }
            ]
        }
    ]
}
```


### <a name="airport-search"></a>공항 검색

검색 POI API를 사용하여 공식 코드로 공항을 찾을 수 있습니다. 예를 들어 *SEA* 를 사용 하 여 Seattle-Tacoma International Airport를 찾을 수 있습니다.

```HTTP
https://atlas.microsoft.com/search/poi/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1.0&query=SEA 
```

### <a name="nearby-search"></a>주변 검색

특정 위치 주변의 POI 결과를 검색하려면 [Search Nearby API](/rest/api/maps/search/getsearchnearby)를 사용해봅니다. 엔드포인트는 POI 결과만 반환합니다. 검색 쿼리 매개 변수를 사용하지 않습니다.

결과를 제한하려면 반경을 설정하는 것이 좋습니다.

## <a name="understanding-the-responses"></a>응답 이해

Azure Maps Search Service에 주소 검색을 요청하여 Seattle에서 주소를 찾아보겠습니다. 다음 요청 URL에서 `countrySet` 매개 변수를 `US`으로 설정하여 USA에서 주소를 검색합니다.

### <a name="sample-query"></a>샘플 쿼리

```HTTP
https://atlas.microsoft.com/search/address/json?subscription-key={Your-Azure-Maps-Primary-Subscription-key}&api-version=1&query=400%20Broad%20Street%2C%20Seattle%2C%20WA&countrySet=US
```

### <a name="supported-types-of-results"></a>지원되는 결과 형식

* **지점 주소**: 지도에서 거리 이름과 번지를 포함한 특정 주소의 지점입니다. 지점 주소는 주소에 대한 가장 높은 수준의 정확도를 제공합니다.

* **주소 범위**: 거리의 시작과 끝에서 보간되는 주소 지점의 범위입니다.  

* **Geography**: 지도에서 국가/지역, 시/도, 구/군/시와 같은 토지의 행정 구역을 나타내는 영역입니다.

* **POI**: 지도에서 주목할 만하고 관심이 있을 수 있는 지점입니다.

* **거리**: 지도상의 거리입니다. 주소는 주소를 포함하는 거리의 위도 및 경도 좌표로 확인됩니다. 집 호수는 처리되지 않을 수 있습니다.

* **교차로**: 교차점입니다. 교차로는 두 거리가 교차하는 지점을 나타냅니다.

### <a name="response"></a>응답

응답 구조를 살펴보겠습니다. 다음 응답에서 결과 개체의 형식이 서로 다릅니다. 자세히 살펴보면 다음과 같은 세 가지 형식의 결과 개체가 있습니다.

* 지점 주소
* Street
* 교차로

주소 검색에서 POI를 반환하지 않습니다.  

각 응답 개체의 `Score` 매개 변수는 일치 점수가 동일한 응답의 다른 개체 점수와 어떤 관련이 있는지를 나타냅니다. 응답 개체 매개 변수에 대한 자세한 내용은 [Get Search Address](/rest/api/maps/search/getsearchaddress)를 참조하세요.

```JSON
{
    "summary": {
        "query": "400 broad street seattle wa",
        "queryType": "NON_NEAR",
        "queryTime": 146,
        "numResults": 6,
        "offset": 0,
        "totalResults": 7,
        "fuzzyLevel": 1
    },
    "results": [
        {
            "type": "Point Address",
            "id": "US/PAD/p0/28725082",
            "score": 9.893799781799316,
            "address": {
                "streetNumber": "400",
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.62039,
                "lon": -122.34928
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62129,
                    "lon": -122.35061,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61949,
                    "lon": -122.34795,
                    "valid": true
                }
            },
            "entryPoints": [
                {
                    "type": "main",
                    "position": {
                        "lat": 47.61982,
                        "lon": -122.34886,
                        "valid": true
                    }
                }
            ]
        },
        {
            "type": "Street",
            "id": "US/STR/p0/6700384",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61724,
                "lon": -122.35207
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61825,
                    "lon": -122.35336,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61626,
                    "lon": -122.35078,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/9701953",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
            },
            "position": {
                "lat": 47.61965,
                "lon": -122.349
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62066,
                    "lon": -122.35041,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61857,
                    "lon": -122.34761,
                    "valid": true
                }
            }
        },
        {
            "type": "Street",
            "id": "US/STR/p0/11721297",
            "score": 8.129190444946289,
            "address": {
                "streetName": "Broad Street",
                "municipalitySubdivision": "Seattle, Downtown Seattle, Denny Regrade, Belltown",
                "municipality": "Seattle",
                "countrySecondarySubdivision": "King",
                "countryTertiarySubdivision": "Seattle",
                "countrySubdivision": "WA",
                "countrySubdivisionName": "Washington",
                "postalCode": "98121",
                "extendedPostalCode": "981211237",
                "countryCode": "US",
                "country": "United States",
                "countryCodeISO3": "USA",
                "freeformAddress": "Broad Street, Seattle, WA 98121"
            },
            "position": {
                "lat": 47.61825,
                "lon": -122.35078
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.61857,
                    "lon": -122.35078,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.61825,
                    "lon": -122.35041,
                    "valid": true
                }
            }
        },
        {
            "type": "Cross Street",
            "id": "US/XSTR/p1/232144",
            "score": 6.754479885101318,
            "address": {
                "streetName": "Broad Street & Valley Street",
                "municipalitySubdivision": "South Lake Union, Seattle",
            },
            "position": {
                "lat": 47.62545,
                "lon": -122.33974
            },
            "viewport": {
                "topLeftPoint": {
                    "lat": 47.62635,
                    "lon": -122.34107,
                    "valid": true
                },
                "btmRightPoint": {
                    "lat": 47.62455,
                    "lon": -122.33841,
                    "valid": true
                }
            }
        }
    ]
}
```

### <a name="geometry"></a>geometry

*Geometry* 의 응답 형식에 `geometry` 및 `id` 아래의 `dataSources` 개체에서 반환되는 기하 도형 ID가 포함될 수 있습니다. 예를 들어 [Search Polygon 서비스](/rest/api/maps/search/getsearchpolygon)를 사용하여 GeoJSON 형식의 기하 도형 데이터를 요청할 수 있습니다. 이 형식을 사용하여 엔터티 세트에 대한 도시나 공항 개요를 얻을 수 있습니다. 그런 다음, 이 경계 데이터를 사용하여 [지오펜스를 설정](./tutorial-geofence.md)하거나 [기하 도형 내에서 POI를 검색](/rest/api/maps/search/postsearchinsidegeometry)할 수 있습니다.

[Search Address](/rest/api/maps/search/getsearchaddress) API나 [Search Fuzzy](/rest/api/maps/search/getsearchfuzzy) API에 대한 응답에는 `geometry` 및 `id` 아래의 `dataSources` 개체에서 반환되는 기하 도형 ID가 포함될 수 있습니다.

```JSON
"dataSources": { 
        "geometry": { 
            "id": "00005557-4100-3c00-0000-000059690938" // The geometry ID is returned in the dataSources object under "geometry" and "id".
        }
} 
```

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요:

> [!div class="nextstepaction"]
> [Azure Maps Search Service 요청을 작성하는 방법](./how-to-search-for-address.md)

> [!div class="nextstepaction"]
> [Search Service API 설명서](/rest/api/maps/search)
