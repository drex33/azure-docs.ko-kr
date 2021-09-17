---
title: 공간 데이터 읽기 및 쓰기 | Microsoft Azure Maps
description: Azure Maps Web SDK에서 제공하는 공간 IO 모듈을 사용하여 데이터를 읽고 쓰는 방법을 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/01/2020
ms.topic: conceptual
ms.service: azure-maps
ms.custom: devx-track-js
ms.openlocfilehash: dfd5da016732d0424935e72e00b38cf597dd5a6e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430399"
---
# <a name="read-and-write-spatial-data"></a>공간 데이터 읽기 및 쓰기

아래 표에는 공간 IO 모듈을 사용하여 작업을 읽고 쓰는 데 지원되는 공간 파일 형식이 나와 있습니다.

| 데이터 형식       | 읽기 | 쓰기 |
|-------------------|------|-------|
| GeoJSON           | ✓  |  ✓  |
| GeoRSS            | ✓  |  ✓  |
| GML               | ✓  |  ✓  |
| GPX               | ✓  |  ✓  |
| KML               | ✓  |  ✓  |
| KMZ               | ✓  |  ✓  |
| 공간 CSV       | ✓  |  ✓  |
| 잘 알려진 텍스트   | ✓  |  ✓  |

다음 섹션에서는 공간 IO 모듈을 사용하여 공간 데이터를 읽고 쓰기 위한 다양한 도구에 대해 간략하게 설명합니다.

## <a name="read-spatial-data"></a>공간 데이터 읽기

`atlas.io.read` 함수는 공간 데이터가 포함된 KML, GPX, GeoRSS, GeoJSON 및 CSV 파일과 같은 일반적인 공간 데이터 형식을 읽는 데 사용되는 기본 함수입니다. 이 함수는 이러한 형식의 압축된 버전을 zip 파일 또는 KMZ 파일로 읽을 수도 있습니다. KMZ 파일 형식은 이미지와 같은 자산을 포함할 수도 있는 압축된 버전의 KML입니다. 또는 read 함수는 이러한 형식의 파일을 가리키는 URL을 사용할 수 있습니다. URL은 CORS 지원 엔드포인트에서 호스트되어야 하며, 그렇지 않은 경우 프록시 서비스를 읽기 옵션에 제공해야 합니다. 프록시 서비스는 CORS가 사용하도록 설정되지 않은 도메인에 리소스를 로드하는 데 사용됩니다. read 함수는 맵에 이미지 아이콘을 추가하는 프라미스를 반환하고 데이터를 비동기적으로 처리하여 UI 스레드에 미치는 영향을 최소화합니다.

압축된 파일을 zip 또는 KMZ로 읽을 때 압축을 풀고 첫 번째 유효한 파일을 검색합니다. 예를 들어 doc.kml 또는 다른 유효한 확장명(예: .kml, .xml, geojson, .json, .csv, .tsv 또는 .txt)이 있는 파일이 여기에 해당합니다. 그런 다음, KML 및 GeoRSS 파일에서 참조된 이미지는 액세스할 수 있도록 미리 로드됩니다. 액세스할 수 없는 이미지 데이터는 대체(fallback) 이미지를 로드하거나 스타일에서 제거될 수 있습니다. KMZ 파일에서 추출된 이미지는 데이터 URI로 변환됩니다.

read 함수의 결과는 `SpatialDataSet` 개체입니다. 이 개체는 GeoJSON FeatureCollection 클래스를 확장합니다. 맵에 해당 기능을 렌더링하기 위해 `DataSource`에 있는 그대로 쉽게 전달할 수 있습니다. `SpatialDataSet`는 기능 정보를 포함할 뿐만 아니라 다음 표에 설명된 대로 KML 그라운드 오버레이, 처리 메트릭 및 기타 세부 정보를 포함할 수도 있습니다.

| 속성 이름 | Type | 설명 | 
|---------------|------|-------------|
| `bbox` | `BoundingBox` | 데이터 세트에 있는 모든 데이터의 경계 상자입니다. |
| `features` | `Feature[]` | 데이터 세트 내의 GeoJSON 기능입니다. |
| `groundOverlays` | `(atlas.layer.ImageLayer | atlas.layers.OgcMapLayer)[]` | KML GroundOverlays의 배열입니다. |
| `icons` | 레코드&lt;문자열, 문자열&gt; | 아이콘 .URL 세트입니다. 키 = 아이콘 이름, 값 = URL |
| properties | any | 공간 데이터 세트의 문서 수준에서 제공되는 속성 정보입니다. |
| `stats` | `SpatialDataSetStats` | 공간 데이터 세트의 콘텐츠 및 처리 시간에 대한 통계입니다. |
| `type` | `'FeatureCollection'` | 읽기 전용 GeoJSON 형식 값입니다. |

## <a name="examples-of-reading-spatial-data"></a>공간 데이터 읽기 예제

다음 코드에서는 공간 데이터 세트를 읽고 `SimpleDataLayer` 클래스를 사용하여 지도에 렌더링하는 방법을 보여 줍니다. 이 코드는 URL에서 가리키는 GPX 파일을 사용합니다.

<br/>

<iframe height='500' scrolling='no' title='공간 데이터를 간단하게 로드' src='//codepen.io/azuremaps/embed/yLNXrZx/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/yLNXrZx/'>공간 데이터를 간단하게 로드</a>를 참조하세요.
</iframe>

다음 코드 데모에서는 KML 또는 KMZ를 읽고 지도에 로드하는 방법을 보여 줍니다. KML은 `ImageLyaer` 또는 `OgcMapLayer` 형식의 그라운드 오버레이를 포함할 수 있습니다. 이러한 오버레이는 기능과는 별도로 지도에 추가해야 합니다. 또한 데이터 세트에 사용자 지정 아이콘이 있는 경우 기능을 로드하기 전에 해당 아이콘을 지도 리소스에 로드해야 합니다.

<br/>

<iframe height='500' scrolling='no' title='지도에 KML 로드' src='//codepen.io/azuremaps/embed/XWbgwxX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/XWbgwxX/'>지도에 KML 로드</a>를 참조하세요.
</iframe>

필요에 따라 CORS를 사용하도록 설정하지 않았을 수 있는 도메인 간 자산에 액세스하기 위한 프록시 서비스를 제공할 수 있습니다. read 함수는 먼저 CORS를 사용하여 다른 도메인의 파일에 액세스하려고 합니다. 처음에 CORS를 사용하여 다른 도메인의 리소스에 처음 액세스하지 못하면 프록시 서비스가 제공된 경우에만 추가 파일을 요청합니다. read 함수는 제공된 프록시 URL의 끝에 파일 URL을 추가합니다. 이 코드 조각은 프록시 서비스를 read 함수에 전달하는 방법을 보여 줍니다.

```javascript
//Read a file from a URL or pass in a raw data as a string.
atlas.io.read('https://nonCorsDomain.example.com/mySuperCoolData.xml', {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(async r => {
    if (r) {
        // Some code goes here . . .
    }
});

```

아래 데모에서는 구분된 파일을 읽고 지도에 렌더링하는 방법을 보여 줍니다. 이 경우 코드는 공간 데이터 열이 있는 CSV 파일을 사용합니다.

<br/>

<iframe height='500' scrolling='no' title='구분된 파일 추가' src='//codepen.io/azuremaps/embed/ExjXBEb/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/ExjXBEb/'>구분된 파일 추가</a>를 참조하세요.
</iframe>

## <a name="write-spatial-data"></a>공간 데이터 쓰기

공간 IO 모듈에는 두 가지 기본 쓰기 함수가 있습니다. `atlas.io.write` 함수는 문자열을 생성하지만, `atlas.io.writeCompressed` 함수는 압축된 zip 파일을 생성합니다. 압축된 zip 파일에는 공간 데이터가 포함된 텍스트 기반 파일이 포함됩니다. 이러한 두 함수는 모두 파일에 데이터를 추가하기 위한 프라미스를 반환합니다. 또한 두 함수는 모두 `SpatialDataSet`, `DataSource`, `ImageLayer`, `OgcMapLayer`, 기능 컬렉션, 기능, 기하 도형 또는 이러한 데이터 형식의 조합 배열을 쓸 수 있습니다. 두 함수 중 하나를 사용하여 작성할 때 원하는 파일 형식을 지정할 수 있습니다. 파일 형식을 지정하지 않으면 데이터가 KML로 기록됩니다.

아래 도구는 `atlas.io.write` 함수와 함께 사용할 수 있는 대부분의 쓰기 옵션을 보여 줍니다.

<br/>

<iframe height='700' scrolling='no' title='공간 데이터 쓰기 옵션' src='//codepen.io/azuremaps/embed/YzXxXPG/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/YzXxXPG/'>공간 데이터 쓰기 옵션</a>을 참조하세요.
</iframe>

## <a name="example-of-writing-spatial-data"></a>공간 데이터 쓰기 예제

다음 샘플을 사용하면 공간 파일을 지도에 끌어서 놓은 다음, 로드할 수 있습니다. 지도에서 GeoJSON 데이터를 내보내고 지원되는 공간 데이터 형식 중 하나를 갖는 문자열 또는 압축 파일로 쓸 수 있습니다.

<br/>

<iframe height='700' scrolling='no' title='공간 파일을 지도에 끌어서 놓기' src='//codepen.io/azuremaps/embed/zYGdGoO/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/zYGdGoO/'>공간 파일을 지도에 끌어서 놓기</a>를 참조하세요.
</iframe>

필요에 따라 CORS를 사용하도록 설정하지 않았을 수 있는 도메인 간 자산에 액세스하기 위한 프록시 서비스를 제공할 수 있습니다. 이 코드 조각에서는 프록시 서비스를 통합할 수 있는 방법을 보여 줍니다.

```javascript
atlas.io.read(data, {
    //Provide a proxy service
    proxyService: window.location.origin + '/YourCorsEnabledProxyService.ashx?url='
}).then(
    //Success
    function(r) {
        //some code goes here ...
    }
);
```

## <a name="read-and-write-well-known-text-wkt"></a>WKT(잘 알려진 텍스트) 읽기 및 쓰기

WKT([잘 알려진 텍스트](https://en.wikipedia.org/wiki/Well-known_text_representation_of_geometry))는 공간 기하 도형을 텍스트로 나타내는 OGC(Open Geospatial Consortium) 표준입니다. PostGIS 플러그 인을 사용하는 Azure SQL 및 Azure PostgreSQL과 같은 많은 지리 공간적 시스템은 WKT를 지원합니다. 대부분의 OGC 표준과 마찬가지로 좌표는 "x y" 규칙에 맞게 "경도 위도"로 형식이 지정됩니다. 예를 들어, 경도 -110 및 위도 45의 지점은 WKT 형식을 사용하여 `POINT(-110 45)`로 쓸 수 있습니다.

`atlas.io.ogc.WKT.read` 함수를 사용하여 잘 알려진 텍스트를 읽고, `atlas.io.ogc.WKT.write` 함수를 사용하여 쓸 수 있습니다.

## <a name="examples-of-reading-and-writing-well-known-text-wkt"></a>WKT(잘 알려진 텍스트) 읽기 및 쓰기 예제

다음 코드는 잘 알려진 텍스트 문자열 `POINT(-122.34009 47.60995)`를 읽고 거품형 계층을 사용하여 지도에 렌더링하는 방법을 보여 줍니다.

<br/>

<iframe height='500' scrolling='no' title='잘 알려진 텍스트 읽기' src='//codepen.io/azuremaps/embed/XWbabLd/?height=500&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/XWbabLd/'>잘 알려진 텍스트 읽기</a>를 참조하세요.
</iframe>

다음 코드는 잘 알려진 텍스트를 돌아가며 읽고 쓰는 방법을 보여 줍니다.

<br/>

<iframe height='700' scrolling='no' title='잘 알려진 텍스트 읽기 및 쓰기' src='//codepen.io/azuremaps/embed/JjdyYav/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)에 의한 펜 <a href='https://codepen.io/azuremaps/pen/JjdyYav/'>잘 알려진 텍스트 읽기 및 쓰기</a>를 참조하세요.
</iframe>

## <a name="read-and-write-gml"></a>GML 읽기 및 쓰기

GML은 다른 XML 사양에 대한 확장으로 자주 사용되는 공간 XML 파일 사양입니다. `atlas.io.core.GmlWriter.write` 함수를 사용하여 GeoJSON 데이터를 GML 태그가 있는 XML로 작성할 수 있습니다. GML을 포함하는 XML은 `atlas.io.core.GmlReader.read` 함수를 사용하여 읽을 수 있습니다. read 함수에는 다음 두 가지 옵션이 있습니다.

- `isAxisOrderLonLat` 옵션 - 좌표 "위도, 경도" 또는 "경도, 위도"의 축 순서가 데이터 세트마다 다를 수 있으며 항상 잘 정의되어 있지는 않습니다. 기본적으로 GML 판독기는 좌표 데이터를 "위도, 경도"로 읽지만 이 옵션을 true로 설정하면 "경도, 위도"로 읽습니다.
- `propertyTypes` 옵션 - 이 옵션은 키 값 조회 테이블로, 키는 데이터 세트의 속성 이름에 해당합니다. 값은 구문 분석할 때 값을 캐스팅할 개체 형식입니다. 지원되는 형식 값은 `string`, `number`, `boolean` 및 `date`입니다. 속성이 조회 테이블에 없거나 형식이 정의되지 않은 경우 속성은 문자열로 구문 분석됩니다.

`atlas.io.read` 함수는 입력 데이터가 XML이지만 데이터가 다른 지원 공간 XML 형식 중 하나가 아니라는 사실을 감지할 때 기본적으로 `atlas.io.core.GmlReader.read` 함수가 됩니다.

`GmlReader`는 다음 SRID 중 하나가 있는 좌표를 구문 분석합니다.

- EPSG:4326(기본 설정)
- EPSG:4269, EPSG:4283, EPSG:4258, EPSG:4308, EPSG:4230, EPSG:4272, EPSG:4271, EPSG:4267, EPSG:4608, EPSG:4674 오차 범위가 작을 수 있음
- EPSG:3857, EPSG:102100, EPSG:3785, EPSG:900913, EPSG:102113, EPSG:41001, EPSG:54004

## <a name="more-resources"></a>추가 리소스

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

[atlas.io 정적 함수](/javascript/api/azure-maps-spatial-io/atlas.io)

[SpatialDataSet](/javascript/api/azure-maps-spatial-io/atlas.spatialdataset)

[SpatialDataSetStats](/javascript/api/azure-maps-spatial-io/atlas.spatialdatasetstats)

[GmlReader](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlreader)

[GmlWriter](/javascript/api/azure-maps-spatial-io/atlas.io.core.gmlwriter)

[atlas.io.ogc.WKT 함수](/javascript/api/azure-maps-spatial-io/atlas.io.ogc.wkt)

[WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

[핵심 작업 활용](spatial-io-core-operations.md)

[지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)


## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

[OGC 지도 계층 추가](spatial-io-add-ogc-map-layer.md)