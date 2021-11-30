---
title: iOS 맵에 타일 계층 추가
titleSuffix: Microsoft Azure Maps
description: 맵에 타일 계층을 추가하는 방법을 알아봅니다. Azure 지도 iOS SDK를 사용하여 날씨 방사형 오버레이를 지도에 추가하는 예제를 참조하세요.
author: stevemunk
ms.author: v-munksteve
ms.date: 11/23/2021
ms.topic: how-to
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 6f8876658f6c6336a30add249365766756477215
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218964"
---
# <a name="add-a-tile-layer-to-a-map-in-the-ios-sdk-preview"></a>iOS SDK의 지도에 타일 계층 추가(미리 보기)

이 문서에서는 Azure 지도 iOS SDK를 사용하여 맵에 타일 계층을 렌더링하는 방법을 보여 줍니다. 타일 계층을 사용하여 Azure Maps 기본 맵 타일의 위에 이미지를 겹칠 수 있습니다. Azure Maps의 타일 배치 체계에 대한 자세한 내용은 [확대/축소 수준 및 타일 그리드](zoom-levels-and-tile-grid.md) 설명서에서 볼 수 있습니다.

타일 계층이 서버에서 타일에 로드됩니다. 이러한 이미지는 사전 렌더링되며, 타일 계층에서 인식하는 명명 규칙을 사용하여 서버의 다른 이미지와 같이 저장됩니다. 또는 이러한 이미지를 거의 실시간으로 생성하는 동적 서비스로 렌더링할 수 있습니다. Azure Maps TileLayer 클래스로 지원되는 세 가지 다른 타일 서비스 명명 규칙이 있습니다.

* X, Y, 확대/축소 표기법-확대/축소 수준에 따라 타일 그리드에서 x는 열 위치이고 y는 행 위치입니다.
* Quadkey 표기법-x, y 조합은 정보를 타일의 고유 식별자인 단일 문자열 값으로 확대/축소합니다.
* 경계 상자-경계 상자 좌표를 사용하여 일반적으로 [WMS(웹 매핑 서비스)](https://www.opengeospatial.org/standards/wms)에 사용하는 `{west},{south},{east},{north}` 형식으로 이미지를 지정할 수 있습니다.

> [!TIP]
> TileLayer는 맵에서 큰 데이터 세트를 시각화하는 좋은 방법입니다. 이미지에서 타일 계층을 생성할 있을 뿐 아니라 타일 계층으로 벡터 데이터도 렌더링할 수 있습니다. 맵 컨트롤은 타일 계층으로 벡터 데이터를 렌더링함으로써, 맵 컨트롤은 표현하는 벡터 데이터보다 파일 크기가 훨씬 더 작은 타일만 로드해야 합니다. 맵에서 데이터 행 수백만 개를 렌더링해야 하는 대부분의 사용자가 이 기술을 사용합니다.

타일 계층으로 전달된 타일 URL은 다음 매개 변수를 사용하는 TileJSON 리소스에 대한 http/https URL 또는 타일 URL 템플릿이어야 합니다.

* `{x}` - 타일의 X 위치입니다. 또한 `{y}` 및 `{z}`가 필요합니다.
* `{y}` - 타일의 Y 위치입니다. 또한 `{x}` 및 `{z}`가 필요합니다.
* `{z}` - 타일의 확대/축소 수준입니다. 또한 `{x}` 및 `{y}`가 필요합니다.
* `{quadkey}` - Bing Maps 타일 시스템 명명 규칙에 따라 타일 Quadkey 식별자입니다.
* `{bbox-epsg-3857}` - EPSG 3857 공간 참조 시스템에서 `{west},{south},{east},{north}` 형식을 사용하는 경계 상자 문자열입니다.
* `{subdomain}` - 하위 도메인 값이 지정된 경우 하위 도메인 값에 대한 자리 표시자입니다.
* `map.domainPlaceholder` - 맵에 사용되는 것과 동일한 값을 사용하여 도메인 및 타일 요청의 인증을 정렬하는 자리 표시자입니다. Azure Maps에서 호스트하는 타일 서비스를 호출할 때 사용합니다.

## <a name="prerequisites"></a>필수 구성 요소

이 문서의 프로세스를 완료하려면 [Azure 지도 iOS SDK를](how-to-use-ios-map-control-library.md) 설치하여 맵을 로드해야 합니다.

## <a name="add-a-tile-layer-to-the-map"></a>맵에 타일 계층 추가

이 샘플은 타일 세트를 가리키는 타일 계층을 만드는 방법을 보여 줍니다. 이 샘플에서는 x, y, 확대/축소 바둑판식 배열 시스템을 사용합니다. 이 타일 계층의 원본은 [OpenSeaMap 프로젝트](https://openseamap.org/index.php)로, 여기에는 크라우드소싱 항해 차트가 포함되어 있습니다. 타일 계층을 볼 때 종종 맵에서 도시 레이블을 명확하게 볼 수 있는 것이 좋습니다. 이 동작은 맵 레이블 계층 아래에 타일 계층을 삽입하여 수행할 수 있습니다.

```swift
map.layers.insertLayer(
    TileLayer(options: [
        .tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
        .opacity(0.8),
        .tileSize(512),
        .minSourceZoom(7),
        .maxSourceZoom(17)
    ]),
    below: "labels"
)
```

다음 스크린샷은 진한 회색조 스타일이 있는 맵에서 해리 정보의 타일 계층을 표시하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/Add-tile-layer-to-map-ios/tiles.png" alt-text="이 이미지는 어두운 회색조 스타일이 있는 맵에 Nautical 정보의 타일 계층을 표시하는 위의 코드를 보여 줍니다.":::

## <a name="add-an-ogc-web-mapping-service-wms"></a>OGC WMS(웹 매핑 서비스) 추가

WMS(웹 매핑 서비스)는 지도 데이터의 이미지를 제공하기 위한 OGC(Open Geospatial Consortium) 표준입니다. Azure Maps에서 사용 가능한 이 형식으로 사용할 수 있는 개방형 데이터 세트가 많습니다. 서비스가 `EPSG:3857` CRS(좌표 참조 시스템)를 지원하는 경우 이 유형의 서비스를 타일 계층과 함께 사용할 수 있습니다. WMS 서비스를 사용할 때 너비/높이 매개 변수를 서비스에서 지원하는 것과 동일한 값으로 설정하고, 이 동일한 값을 `tileSize` 옵션에 설정해야 합니다. 형식이 지정된 URL에서 `BBOX` 서비스 매개 변수를 `{bbox-epsg-3857}` 자리 표시자로 설정합니다.

```swift
map.layers.insertLayer(
    TileLayer(options: [
        .tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
        .tileSize(1024)
    ]),
    below: "labels"
)
```

다음 스크린샷은 지도 위, 레이블 아래에 있는 [USGS(미국 지질 조사)](https://mrdata.usgs.gov/)의 지질 데이터 웹 매핑 서비스를 오버레이하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/Add-tile-layer-to-map-ios/wms.png" alt-text="이 이미지는 미국 맵 맨 위에 있는 미국 맵 설문 조사의 웹 매핑 서비스 레이블 아래에 있는 웹 매핑 서비스를 오버레이하는 위의 코드를 보여줍니다.":::

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>OGC WMTS(웹 매핑 타일 서비스) 추가

WMTS(웹 매핑 타일 서비스)는 지도에 타일 기반 오버레이를 제공하기 위한 OGC(Open Geospatial Consortium) 표준입니다. Azure Maps에서 사용 가능한 이 형식으로 사용할 수 있는 개방형 데이터 세트가 많습니다. 서비스가 `EPSG:3857` 또는 `GoogleMapsCompatible` CRS(좌표 참조 시스템)를 지원하는 경우 이 유형의 서비스를 타일 계층과 함께 사용할 수 있습니다. WMTS 서비스를 사용할 때 너비/높이 매개 변수를 서비스에서 지원하는 것과 동일한 값으로 설정하고, 이 동일한 값을 `tileSize` 옵션에 설정해야 합니다. 형식이 지정된 URL에서 다음 자리 표시자를 적절하게 바꿉니다.

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

```swift
map.layers.insertLayer(
    TileLayer(options: [
        .tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
        .tileSize(256),
        .bounds(
            left: -173.25000107492872,
            bottom: 0.0005794121990209753, 
            right: 146.12527718104752,
            top: 71.506811402077
        ),
        .maxSourceZoom(18)
    ]),
    below: "transit"
)
```

다음 스크린샷은 지도 위, 도로와 레이블 아래에 있는 [미국 지질 조사(USGS) 국가 지도](https://viewer.nationalmap.gov/services/)의 이미지 웹 매핑 타일 서비스를 오버레이하는 위의 코드를 보여 줍니다.

:::image type="content" source="./media/ios-sdk/Add-tile-layer-to-map-ios/wmts.png" alt-text="이 이미지는 미국 USGS(National Map) National Map의 웹 매핑 타일 서비스를 지도의 도로 및 레이블 아래에 오버레이하는 위의 코드를 보여줍니다.":::

## <a name="additional-information"></a>추가 정보

맵에서 이미지를 오버레이하는 방법에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [맵에 이미지 계층 추가](add-image-layer-map-ios.md)
