---
title: 지원되는 데이터 형식 세부 정보 | Microsoft Azure 맵
description: 공간 IO 모듈에서 구분된 공간 데이터를 구문 분석 하는 방법에 대해 알아봅니다.
author: stevemunk
ms.author: v-munksteve
ms.date: 10/28/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 0d8c4d1a3521598e8b42b26af8af917cd180954b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131473873"
---
# <a name="supported-data-format-details"></a>지원되는 데이터 형식 세부 정보

이 문서에서는 모든 XML 태그 및 Well-Known Text 기하 도형 형식에 대한 읽기 및 쓰기 지원을 자세히 설명합니다. 또한 공간 IO 모듈에서 구분된 공간 데이터를 구문 분석하는 방법도 자세히 설명합니다.

## <a name="supported-xml-namespaces"></a>지원되는 XML 네임스페이스

공간 IO 모듈은 다음 네임스페이스의 XML 태그를 지원합니다.

| 네임스페이스 접두사 | 네임스페이스 URI   | 참고                                                                    |
|:-----------------|:----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | GeoRSS 파일에서 읽기 전용 지원.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | GeoRSS 파일에서 읽기 전용 지원.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | GPX 파일에서 읽기 전용 지원. DisplayColor를 구문 분석하고 사용합니다. 도형 메타데이터에 추가된 다른 모든 속성입니다. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | GPX 파일에서 지원됩니다. 선 색을 사용합니다. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | 읽기 전용입니다. GeoRSS는 Atom 형식을 사용하여 작성합니다.              |

## <a name="supported-xml-elements"></a>지원되는 XML 요소

공간 IO 모듈은 다음과 같은 XML 요소를 지원합니다. 지원되지 않는 모든 XML 태그는 JSON 개체로 변환됩니다. 그런 다음, 각 태그가 부모 도형 또는 계층의 `properties` 필드에 속성으로 추가됩니다.

### <a name="kml-elements"></a>KML 요소

공간 IO 모듈은 다음과 같은 KML 요소를 지원합니다.

| 요소 이름         | 읽기    | 쓰기   | 참고                                                                   |
|----------------------|---------|---------|-------------------------------------------------------------------------|
| `address`            | 부분 | yes     | 개체가 구문 분석되지만 도형 위치를 지정하는 데 사용되지 않습니다.                                                                    |
| `AddressDetails`     | 부분 | 아니요      | 개체가 구문 분석되지만 도형 위치를 지정하는 데 사용되지 않습니다.                                                                    |
| `atom:author`        | 예     | 예     |                                                                                                                            |
| `atom:link`          | 예     | 예     |                                                                                                                            |
| `atom:name`          | 예     | 예     |                                                                                                                            |
| `BalloonStyle`       | 부분 | 부분 | `displayMode`는 지원되지 않습니다. `PopupTemplate`으로 변환됩니다. 쓰려면 `popupTemplate` 속성을 쓰려는 기능의 속성으로 추가합니다. |
| `begin`              | 예     | 예     |                                                                                                                            |
| `color`              | 예     | 예     | `#AABBGGRR` 및 `#BBGGRR`을 포함합니다. CSS 색 문자열로 구문 분석됨                                                           |
| `colorMode`          | 예     | no      |                                                                                                                            |
| `coordinates`        | 예     | 예     |                                                                                                                            |
| `Data`               | 예     | 예     |                                                                                                                            |
| `description`        | 예     | 예     |                                                                                                                            |
| `displayName`        | 예     | 예     |                                                                                                                            |
| `Document`           | 예     | 예     |                                                                                                                            |
| `drawOrder`          | 부분 | 아니요      | 접지 오버레이를 읽고 이를 정렬하는 데 사용합니다. 
| `east`               | 예     | 예     |                                                                                                                            |
| `end`                | 예     | 예     |                                                                                                                            |
| `ExtendedData`       | 예     | 예     | 형식화되지 않은 `Data` , `SimpleData` 또는 `Schema` 및 `$[dataName]` 형식의 엔터티 대체를 지원합니다.                      |
| `extrude`            | 부분 | 부분 | 다각형에만 지원됩니다. 서로 다른 높이의 다각형이 있는 MultiGeometry는 개별 기능으로 구분됩니다. 선 스타일은 지원되지 않습니다. 고도가 0인 다각형은 플랫 다각형으로 렌더링됩니다. 읽을 때 외부 링의 첫 번째 좌표 고도는 다각형의 높이 속성으로 추가됩니다. 그런 다음, 첫 번째 좌표의 고도가 맵에서 다각형을 렌더링하는 데 사용됩니다. |
| `fill`               | 예     | 예     |                                                                                                                            |
| `Folder`             | 예     | 예     |                                                                                                                            |
| `GroundOverlay`      | 예     | 예     | `color`는 지원되지 않습니다.                                                                                                   |
| `heading`            | 부분 | 아니요      | 구문 분석되지만 `SimpleDataLayer`에서 렌더링하지 않습니다. 도형의 속성에 데이터가 저장되는 경우에만 씁니다.                 |
| `hotSpot`            | yes     | 부분 | 도형의 속성에 데이터가 저장되는 경우에만 씁니다. 단위는 "픽셀"로만 출력 됩니다.                         |
| `href`               | 예     | 예     |                                                                                                                            |
| `Icon`               | 부분 | 부분 | 구문 분석되지만 `SimpleDataLayer`에서 렌더링하지 않습니다. URI 데이터를 포함하는 경우에만 도형의 아이콘 속성을 씁니다. `href`만 지원됩니다. |
| `IconStyle`          | 부분 | 부분 | `icon`, `heading`, `colorMode` 및 `hotspots` 값은 구문 분석되지만 `SimpleDataLayer`에서 렌더링하지 않습니다.         |
| `innerBoundaryIs`    | 예     | 예     |                                                                                                                            |
| `kml`                | 예     | 예     |                                                                                                                            |
| `LabelStyle`         | no      | no      |                                                                                                                            |
| `LatLonBox`          | 예     | 예     |                                                                                                                            |
| `gx:LatLonQuad`      | 예     | 예     |                                                                                                                            |
| `LinearRing`         | 예     | 예     |                                                                                                                            |
| `LineString`         | 예     | 예     |                                                                                                                            |
| `LineStyle`          | 예     | 예     | `colorMode`는 지원되지 않습니다.                                                                                         |
| `Link`               | 예     | 아니요      | `href` 속성만 네트워크 링크에 지원됩니다.                                                                   |
| `MultiGeometry`      | 부분 | 부분 | 읽을 때 개별 기능으로 구분될 수 있습니다.                                                                     |
| `name`               | 예     | 예     |                                                                                                                            |
| `NetworkLink`        | 예     | 아니요      | 링크는 문서와 동일한 도메인에 있어야 합니다.                                                                  |
| `NetworkLinkControl` | 아니요      | no      |                                                                                                                            |
| `north`              | 예     | 예     |                                                                                                                            |
| `open`               | 예     | 예     |                                                                                                                            |
| `outerBoundaryIs`    | 예     | 예     |                                                                                                                            |
| `outline`            | 예     | 예     |                                                                                                                            |
| `overlayXY`          | no      | 아니요      |                                                                                                                            |
| `Pair`               | 부분 | 아니요      | `StyleMap`의 `normal` 스타일만 지원됩니다. `highlight`는 지원되지 않습니다.                                   |
| `phoneNumber`        | 예     | 예     |                                                                                                                            |
| `PhotoOverlay`       | no      | no      |                                                                                                                            |
| `Placemark`          | 예     | 예     |                                                                                                                            |
| `Point`              | 예     | 예     |                                                                                                                            |
| `Polygon`            | 예     | 예     |                                                                                                                            |
| `PolyStyle`          | 예     | 예     |                                                                                                                            |
| `Region`             | 부분 | 부분 | `LatLongBox`는 문서 수준에서 지원됩니다.                                                                      |
| `rotation`           | 아니요      | no      |                                                                                                                            |
| `rotationXY`         | no      | no      |                                                                                                                            |
| `scale`              | no      | no      |                                                                                                                            |
| `Schema`             | 예     | 예     |                                                                                                                            |
| `SchemaData`         | 예     | 예     |                                                                                                                            |
| `schemaUrl`          | 부분 | yes     | KMZ에 포함되지 않은 외부 문서에서 스타일을 로드하는 것을 지원하지 않습니다.                             |
| `ScreenOverlay`      | 아니요      | no      |                                                                                                                            |
| `screenXY`           | no      | no      |                                                                                                                            |
| `SimpleData`         | 예     | 예     |                                                                                                                            |
| `SimpleField`        | 예     | 예     |                                                                                                                            |
| `size`               | no      | 아니요      |                                                                                                                            |
| `Snippet`            | 부분 | 부분 | `maxLines` 특성은 무시됩니다.                                                                                  |
| `south`              | 예     | 예     |                                                                                                                            |
| `Style`              | 예     | 예     |                                                                                                                            |
| `StyleMap`           | 부분 | 아니요      | `StyleMap`의 일반 스타일만 지원됩니다.                                                                        |
| `styleUrl`           | 부분 | yes     | 외부 스타일 URL은 지원되지 않습니다.                                                                         |
| `text`               | 예     | 예     | `$[geDirections]`의 대체가 지원되지 않습니다.                                                                          |
| `textColor`          | 예     | 예     |                                                                                                                            |
| `TimeSpan`           | 예     | 예     |                                                                                                                            |
| `TimeStamp`          | 예     | 예     |                                                                                                                            |
| `value`              | 예     | 예     |                                                                                                                            |
| `viewRefreshMode`    | 부분 | 아니요      |  WMS 서비스를 가리키는 경우 `onStop`만 접지 오버레이에 지원됩니다. URL에 `BBOX={bboxWest},{bboxSouth},{bboxEast},{bboxNorth}`를 추가하고 맵이 이동할 때 업데이트됩니다.  |
| `visibility`         | 예     | 예     |                                                                                                                            |
| `west`               | 예     | 예     |                                                                                                                            |
| `when`               | 예     | 예     |                                                                                                                            |
| `width`              | 예     | 예     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS 요소

공간 IO 모듈은 다음과 같은 GeoRSS 요소를 지원합니다.

| 요소 이름             | 읽기    | 쓰기 | 참고                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | 예     | 예   |                                                                                                |
| `atom:category`          | 예     | 예   |                                                                                                |
| `atom:content`           | 예     | 예   |                                                                                                |
| `atom:contributor`       | 예     | 예   |                                                                                                |
| `atom:email`             | 예     | 예   |                                                                                                |
| `atom:entry`             | 예     | 예   |                                                                                                |
| `atom:feed`              | 예     | 예   |                                                                                                |
| `atom:icon`              | 예     | 예   |                                                                                                |
| `atom:id`                | 예     | 예   |                                                                                                |
| `atom:link`              | 예     | 예   |                                                                                                |
| `atom:logo`              | 예     | 예   |                                                                                                |
| `atom:name`              | 예     | 예   |                                                                                                |
| `atom:published`         | 예     | 예   |                                                                                                |
| `atom:rights`            | 예     | 예   |                                                                                                |
| `atom:source`            | 예     | 예   |                                                                                                |
| `atom:subtitle`          | 예     | 예   |                                                                                                |
| `atom:summary`           | 예     | 예   |                                                                                                |
| `atom:title`             | 예     | 예   |                                                                                                |
| `atom:updated`           | 예     | 예   |                                                                                                |
| `atom:uri`               | 예     | 예   |                                                                                                |
| `geo:lat`                | 예     | 아니요    | `georss:point`로 기록됩니다.                                                                   |
| `geo:lon`                | 예     | 아니요    | `georss:point`로 기록됩니다.                                                                   |
| `geo:long`               | 예     | 아니요    | `georss:point`로 기록됩니다.                                                                   |
| `georss:box`             | 예     | 아니요    | 다각형으로 읽고 "Rectangle"의 `subType` 속성을 지정합니다.                                |
| `georss:circle`          | 예     | 예   |                                                                                                |
| `georss:elev`            | 예     | 예   |                                                                                                |
| `georss:featurename`     | 예     | 예   |                                                                                                |
| `georss:featuretypetag`  | 예     | 예   |                                                                                                |
| `georss:floor`           | 예     | 예   |                                                                                                |
| `georss:line`            | 예     | 예   |                                                                                                |
| `georss:point`           | 예     | 예   |                                                                                                |
| `georss:polygon`         | 예     | 예   |                                                                                                |
| `georss:radius`          | 예     | 예   |                                                                                                |
| `georss:relationshiptag` | 예     | 예   |                                                                                                |
| `georss:where`           | 예     | 예   |                                                                                                |
| `geourl:latitude`        | 예     | 아니요    | `georss:point`로 기록됩니다.                                                                   |
| `geourl:longitude`       | 예     | 아니요    | `georss:point`로 기록됩니다.                                                                   |
| `position`               | 예     | 아니요    | 일부 XML 피드는 `georss:where` 태그를 사용하여 래핑하는 대신 위치 태그를 사용하여 GML을 래핑합니다. 이 태그를 읽지만 `georss:where` 태그를 사용하여 기록합니다. |
| `rss`                    | 예     | 아니요    | ATOM 형식으로 기록되는 GeoRSS입니다.                                                                 |
| `rss:author`             | yes     | 부분 | `atom:author`로 기록됩니다.                                                                 |
| `rss:category`           | 예     | 부분 | `atom:category`로 기록됩니다.                                                               |
| `rss:channel`            | 예     | no    |                                                                                                |
| `rss:cloud`              | 예     | no    |                                                                                                |
| `rss:comments`           | 예     | no    |                                                                                                |
| `rss:copyright`          | 예     | 부분 | 도형에 `rights` `properties` 속성이 아직 없는 경우 `atom:rights`로 기록됩니다.       |
| `rss:description`        | 예     | 부분 | 도형에 `content` `properties` 속성이 아직 없는 경우 `atom:content`로 기록됩니다.      |
| `rss:docs`               | 예     | no    |                                                                                                |
| `rss:enclosure`          | 예     | no    |                                                                                                |
| `rss:generator`          | 예     | no    |                                                                                                |
| `rss:guid`               | 예     | 부분 | 도형에 `id` `properties` 속성이 아직 없는 경우 `atom:id`로 기록됩니다.         |
| `rss:image`              | yes     | 부분 | 도형에 `logo` `properties` 속성이 아직 없는 경우 `atom:logo`로 기록됩니다.      |
| `rss:item`               | 예     | 부분 | `atom:entry`로 기록됩니다.                                                                  |
| `rss:language`           | 예     | no    |                                                                                                |
| `rss:lastBuildDate`      | 예     | 부분 | 도형에 `updated` `properties` 속성이 아직 없는 경우 `atom:updated`로 기록됩니다.     |
| `rss:link`               | yes     | 부분 | `atom:link`로 기록됩니다.                                                                   |
| `rss:managingEditor`     | 예     | 부분 | `atom:contributor`로 기록됩니다.                                                            |
| `rss:pubDate`            | 예     | 부분 | 도형에 `published` `properties` 속성이 아직 없는 경우 `atom:published`로 기록됩니다.  |
| `rss:rating`             | 예     | no    |                                                                                                |
| `rss:skipDays`           | 예     | no    |                                                                                                |
| `rss:skipHours`          | 예     | no    |                                                                                                |
| `rss:source`             | 예     | 부분 | `atom:link`를 포함하는 `atom:source`로 기록됩니다.                                       |
| `rss:textInput`          | 예     | no    |                                                                                                |
| `rss:title`              | 예     | 부분 | `atom:title`로 기록됩니다.                                                                  |
| `rss:ttl`                | 예     | no    |                                                                                                |
| `rss:webMaster`          | 예     | 아니요    |                                                                                                |

### <a name="gml-elements"></a>GML 요소

공간 IO 모듈은 다음과 같은 GML 요소를 지원합니다.

| 요소 이름            | 읽기 | 쓰기 | 참고                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | 예  | 아니요    | `gml:posList`로 기록됩니다.                                                              |
| `gml:curveMember`       | 예  | no    |                                                                                        |
| `gml:curveMembers`      | 예  | no    |                                                                                        |
| `gml:Box`               | 예  | 아니요    | `gml:Envelope`로 기록됩니다.                                                             |
| `gml:description`       | 예  | 예   |                                                                                        |
| `gml:Envelope`          | 예  | 예   |                                                                                        |
| `gml:exterior`          | 예  | 예   |                                                                                        |
| `gml:Feature`           | 예  | 아니요    | 도형으로 기록됩니다.                                                                    |
| `gml:FeatureCollection` | 예  | 아니요    | 기하 도형 컬렉션으로 기록됩니다.                                                      |
| `gml:featureMember`     | 예  | 아니요    | 기하 도형 컬렉션으로 기록됩니다.                                                      |
| `gml:geometry`          | 예  | 아니요    | 도형으로 기록됩니다.                                                                    |
| `gml:geometryMember`    | 예  | 예   |                                                                                        |
| `gml:geometryMembers`   | 예  | 예   |                                                                                        |
| `gml:identifier`        | 예  | 예   |                                                                                        |
| `gml:innerBoundaryIs`   | 예  | 아니요    | `gml.interior`를 사용하여 기록됩니다.                                                          |
| `gml:interior`          | 예  | 예   |                                                                                        |
| `gml:LinearRing`        | 예  | 예   |                                                                                        |
| `gml:LineString`        | 예  | 예   |                                                                                        |
| `gml:lineStringMember`  | 예  | 예   |                                                                                        |
| `gml:lineStringMembers` | 예  | no    |                                                                                        |
| `gml:MultiCurve`        | 예  | 아니요    | `gml:LineString` 구성원만 읽습니다. `gml.MultiLineString`으로 기록됩니다.                  |
| `gml:MultiGeometry`     | 부분  | 부분   | FeatureCollection으로만 읽힙니다.                                              |
| `gml:MultiLineString`   | 예  | 예   |                                                                                        |
| `gml:MultiPoint`        | 예  | 예   |                                                                                        |
| `gml:MultiPolygon`      | 예  | 예   |                                                                                        |
| `gml:MultiSurface`      | 예  | 아니요    | `gml:Polygon` 구성원만 읽습니다. `gml.MultiPolygon`으로 기록됩니다.                        |
| `gml:name`              | 예  | 예   |                                                                                        |
| `gml:outerBoundaryIs`   | 예  | 아니요    | `gml.exterior`를 사용하여 기록됩니다.                                                          |
| `gml:Point`             | 예  | 예   |                                                                                        |
| `gml:pointMember`       | 예  | 예   |                                                                                        |
| `gml:pointMembers`      | 예  | no    |                                                                                        |
| `gml:Polygon`           | 예  | 예   |                                                                                        |
| `gml:polygonMember`     | 예  | 예   |                                                                                        |
| `gml:polygonMembers`    | 예  | no    |                                                                                        |
| `gml:pos`               | 예  | 예   |                                                                                        |
| `gml:posList`           | 예  | 예   |                                                                                        |
| `gml:surfaceMember`     | 예  | 예   |                                                                                        |

#### <a name="additional-notes"></a>추가 참고 사항

- 구성원 요소에서 자식 요소 내에 포함될 수 있는 기하 도형이 검색됩니다. 이 검색 작업은 GML에서 확장되는 많은 XML 형식이 구성원 요소의 직계 자식으로 기하 도형을 배치하지 않을 수 있기 때문에 필요합니다.
- `srsName`은 WGS84 좌표 및 다음 코드([EPSG:4326](https://epsg.io/4326)) 및 웹 Mercator([EPSG:3857](https://epsg.io/3857)) 또는 해당 대체 코드 중 하나에 부분적으로 지원됩니다. 다른 모든 좌표계는 WGS84로 있는 그대로 구문 분석됩니다.
- 축 순서는 XML 피드를 읽을 때 지정하지 않을 경우, XML 피드의 힌트에 따라 결정됩니다. "위도, 경도" 축 순서에 대한 기본 설정이 지정됩니다.
- GML 파일에 쓸 때 속성에 사용자 지정 GML 네임스페이스를 지정하지 않으면 추가 속성 정보가 추가되지 않습니다.

### <a name="gpx-elements"></a>GPX 요소

공간 IO 모듈은 다음과 같은 GPX 요소를 지원합니다.

| 요소 이름             | 읽기    | 쓰기   | 참고                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | 예     | 예     |                                                                                             |
| `gpx:author`             | 예     | 예     |                                                                                             |
| `gpx:bounds`             | 예     | 예     | 읽을 때 LocationRect로 변환됩니다.                                                    |
| `gpx:cmt`                | 예     | 예     |                                                                                             |
| `gpx:copyright`          | 예     | 예     |                                                                                             |
| `gpx:desc`               | 예     | 예     | 다른 XML 형식과 맞추기 위해 읽을 때 description 속성에 복사됩니다.               |
| `gpx:dgpsid`             | 예     | 예     |                                                                                             |
| `gpx:ele`                | 예     | 예     |                                                                                             |
| `gpx:extensions`         | 부분 | 부분 | 읽을 때 스타일 정보가 추출됩니다. 다른 모든 확장은 단순 JSON 개체로 평면화됩니다. 도형 스타일 정보만 기록됩니다. |
| `gpx:geoidheight`        | 예     | 예     |                                                                                             |
| `gpx:gpx`                | 예     | 예     |                                                                                             |
| `gpx:hdop`               | 예     | 예     |                                                                                             |
| `gpx:link`               | 예     | 예     |                                                                                             |
| `gpx:magvar`             | 예     | 예     |                                                                                             |
| `gpx:metadata`           | 예     | 예     |                                                                                             |
| `gpx:name`               | 예     | 예     |                                                                                             |
| `gpx:pdop`               | 예     | 예     |                                                                                             |
| `gpx:rte`                | 예     | 예     |                                                                                             |
| `gpx:rtept`              | 예     | 예     |                                                                                             |
| `gpx:sat`                | 예     | 예     |                                                                                             |
| `gpx:src`                | 예     | 예     |                                                                                             |
| `gpx:sym`                | 예     | 예     | 값이 캡처되지만 압정 아이콘을 변경하는 데 사용되지 않습니다.                             |
| `gpx:text`               | 예     | 예     |                                                                                             |
| `gpx:time`               | 예     | 예     |                                                                                             |
| `gpx:trk`                | 예     | 예     |                                                                                             |
| `gpx:trkpt`              | 예     | 예     |                                                                                             |
| `gpx:trkseg`             | 예     | 예     |                                                                                             |
| `gpx:type`               | 예     | 예     |                                                                                             |
| `gpx:vdop`               | 예     | 예     |                                                                                             |
| `gpx:wpt`                | 예     | 예     |                                                                                             |
| `gpx_style:color`        | 예     | 예     |                                                                                             |
| `gpx_style:line`         | 부분 | 부분 | `color`, `opacity`, `width`, `lineCap`이 지원됩니다.                                       |
| `gpx_style:opacity`      | 예     | 예     |                                                                                             |
| `gpx_style:width`        | 예     | 예     |                                                                                             |
| `gpxx:DisplayColor`      | 예     | 아니요      | 도형의 색을 지정하는 데 사용됩니다. 쓸 때 `gpx_style:line` 색이 대신 사용됩니다.|
| `gpxx:RouteExtension`    | 부분 | 아니요      | 모든 속성이 `properties`로 읽힙니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpxx:TrackExtension`    | 부분 | 아니요      | 모든 속성이 `properties`로 읽힙니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpxx:WaypointExtension` | 부분 | 아니요      | 모든 속성이 `properties`로 읽힙니다. 이 경우에는 `DisplayColor`만 사용됩니다.                     |
| `gpx:keywords`           | 예     | 예     |                                                                                             |
| `gpx:fix`                | 예     | 예     |                                                                                             |

#### <a name="additional-notes"></a>추가 참고 사항

기록 시:

- MultiPoint가 개별 이동 지점으로 분할됩니다.
- Polygon 및 MultiPolygon이 트랙으로 기록됩니다.
  
## <a name="supported-well-known-text-geometry-types"></a>지원되는 Well-Known Text 기하 도형 유형

| 기하 도형 유형 | 읽기 | 쓰기 |
|--------------|:----:|:-----:|
| POINT | x | x |
| POINT Z | x | x |
| POINT M | x | x<sup>[2]</sup> |
| POINT ZM | x<sup>[1]</sup><sup>[2]</sup> | |
| LINESTRING | x | x |
| LINESTRING Z | x | x | 
| LINESTRING M | x | x<sup>[2]</sup> |
| LINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | |
| POLYGON | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | |
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x |
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | |
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x |
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | |
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | |
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> |
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x |

\[1\] Z 매개 변수만 캡처되고 Position 값에 세 번째 값으로 추가됩니다.

\[2\] M 매개 변수가 캡처되지 않습니다.

## <a name="delimited-spatial-data-support"></a>구분된 공간 데이터 지원

CSV(쉼표로 구분된 값 파일) 같은 구분된 공간 데이터에는 공간 데이터를 포함하는 열이 있는 경우가 많습니다. 예를 들어 위도 및 경도 정보를 포함하는 열이 있을 수 있습니다. Well-Known Text 형식에는 공간 기하 도형 데이터를 포함하는 열이 있을 수 있습니다.

### <a name="spatial-data-column-detection"></a>공간 데이터 열 검색

공간 데이터를 포함하는 구분된 파일을 읽을 때 위치 필드를 포함하는 열을 확인하기 위해 헤더가 분석됩니다. 헤더에 유형 정보가 포함되어 있으면 셀 값을 적절한 유형으로 캐스팅하는 데 사용됩니다. 헤더를 지정하지 않으면 첫 번째 행이 분석되고 헤더를 생성하는 데 사용됩니다. 첫 번째 행을 분석하는 경우, 대/소문자 구분 없이 열 이름을 다음 이름과 매칭하는 검사가 실행됩니다. 파일에 둘 이상의 이름이 있는 경우 이름 순서가 우선 순위입니다.

#### <a name="latitude"></a>위도

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>경도

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>상승

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geography

데이터의 첫 행에서 Well-Known Text 형식으로 된 문자열이 검색됩니다. 

### <a name="delimited-data-column-types"></a>구분된 데이터 열 형식

헤더 행을 검색할 때 열 이름에 있는 모든 형식 정보가 추출되고 해당 열의 셀을 캐스팅하는 데 사용됩니다. 다음은 형식 값이 "ColumnName (typeName)"인 열 이름의 예입니다. 다음과 같은 대/소문자를 구분하지 않는 형식 이름이 지원됩니다.

#### <a name="numbers"></a>숫자

- edm.int64
- int
- long
- edm.double
- float
- double
- number

#### <a name="booleans"></a>부울

- edm.boolean
- bool
- boolean

#### <a name="dates"></a>날짜

- edm.datetime
- date
- Datetime

#### <a name="geography"></a>Geography

- edm.geography
- geography

#### <a name="strings"></a>문자열

- edm.string
- varchar
- text
- 문자열

헤더에서 형식 정보를 추출할 수 없고 읽을 때 동적 형식 지정 옵션을 사용할 수 있는 경우, 캐스팅하기에 가장 적합한 데이터 형식을 확인하기 위해 각 셀이 개별적으로 분석됩니다.

## <a name="next-steps"></a>다음 단계

맵에 추가할 더 많은 코드 예제를 보려면 다음 문서를 참조하세요.

[공간 데이터 읽기 및 쓰기](spatial-io-read-write-spatial-data.md)
