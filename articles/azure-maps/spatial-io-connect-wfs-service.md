---
title: WFS(Web Feature Service) 서비스 연결 | Microsoft Azure Maps
description: WFS 서비스에 연결한 다음, Azure Maps 웹 SDK 및 공간 IO 모듈을 사용하여 WFS 서비스를 쿼리하는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: c870767c63c265c13e18f527d20954f8e72abae5
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437709"
---
# <a name="connect-to-a-wfs-service"></a>WFS 서비스에 연결

WFS(Web Feature Service)는 OGC(Open Geospatial Consortium)로 정의된 표준화된 API가 있는 공간 데이터를 쿼리하기 위한 웹 서비스입니다. 공간 IO 모듈의 `WfsClient` 클래스를 사용하면 개발자가 WFS 서비스에 연결하고 서비스에서 데이터를 쿼리할 수 있습니다.

다음은 `WfsClient` 클래스에서 지원되지 않는 기능입니다.

- 지원되는 버전: `1.0.0`, `1.1.0`, `2.0.0`
- 지원되는 필터 연산자: 이진 비교, 논리, 수학, 값, `bbox`.
- 요청은 `HTTP GET`만 사용하여 이루어집니다.
- 지원되는 작업:

    | 작업(Operation) | Description |
    | :-- | :-- |
    | GetCapabilities | 유효한 WFS 작업 및 매개 변수를 사용하여 메타데이터 문서 생성 |
    | GetFeature | 데이터 원본의 기능 선택 영역을 반환 |
    | DescribeFeatureType | 지원되는 기능 유형 반환 |

## <a name="using-the-wfs-client"></a>WFS 클라이언트 사용

공간 IO 모듈의 `atlas.io.ogc.WfsClient` 클래스를 사용하면 WFS 서비스를 쉽게 쿼리하고 응답을 GeoJSON 개체로 변환할 수 있습니다. 이 GeoJSON 개체는 기타 매핑 용도로 사용할 수 있습니다.

다음 코드는 WFS 서비스를 쿼리하고 반환된 기능을 맵에 렌더링합니다.

<br/>

<iframe height='700' scrolling='no' title='간단한 WFS 예제' src='//codepen.io/azuremaps/embed/MWwvVYY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/MWwvVYY/'>간단한 WFS 예제</a>를 참조합니다.
</iframe>

## <a name="supported-filters"></a>지원되는 필터

WFS 표준 사양은 OGC 필터를 사용합니다. 아래 필터는 호출되는 서비스가 이러한 필터도 지원한다는 가정 하에 WFS 클라이언트에서 지원됩니다. 사용자 지정 필터 문자열을 `CustomFilter` 클래스에 전달할 수 있습니다.

**논리 연산자**

- `And`
- `Or`
- `Not`

**값 연산자**

- `GmlObjectId`
- `ResourceId`

**수학 연산자**

- `Add`
- `Sub`
- `Mul`
- `Div`

**비교 연산자**

- `PropertyIsEqualTo`
- `PropertyIsNotEqualTo`
- `PropertyIsLessThan`
- `PropertyIsGreaterThan`
- `PropertyIsLessThanOrEqualTo`
- `PropertyIsGreaterThanOrEqualTo`
- `PropertyIsLike`
- `PropertyIsNull`
- `PropertyIsNil`
- `PropertyIsBetween`

다음 코드에서는 WFS 클라이언트에 다른 필터를 사용하는 방법을 보여줍니다.

<br/>

<iframe height='500' scrolling='no' title= 'WFS 필터 예제' src='//codepen.io/azuremaps/embed/NWqvYrV/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/NWqvYrV/'>WFS 필터 예제</a>를 참조합니다.
</iframe>

## <a name="wfs-service-explorer"></a>WFS 서비스 탐색기

다음 코드에서는 WFS 클라이언트를 사용하여 WFS 서비스를 탐색합니다. 서비스 내의 속성 유형 계층을 선택하고 연결된 범례를 확인합니다.

<br/>

<iframe height='700' scrolling='no' title= 'WFS 서비스 탐색기' src='//codepen.io/azuremaps/embed/bGdrvmG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/bGdrvmG/'>WFS 서비스 탐색기</a>를 참조합니다.
</iframe>

CORS를 사용하지 않는 엔드포인트에서 호스트되는 WFS 서비스에 액세스하기 위해 아래에 표시된 바와 같이 CORS 사용 프록시 서비스를 WFS 클라이언트의 `proxyService` 옵션에 전달할 수 있습니다. 

```JavaScript
//Create the WFS client to access the service and use the proxy service settings
client = new atlas.io.ogc.WfsClient({
    url: url,
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
});
```

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [WfsClient](/JavaScript/api/azure-maps-spatial-io/atlas.io.ogc.wfsclient)

> [!div class="nextstepaction"]
> [WfsServiceOptions](/JavaScript/api/azure-maps-spatial-io/atlas.wfsserviceoptions)

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)