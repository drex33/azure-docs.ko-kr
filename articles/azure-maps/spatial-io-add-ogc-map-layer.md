---
title: OGC(Open Geospatial Consortium) 지도 계층 추가 | Microsoft Azure Maps
description: OGC 지도 계층을 지도에 오버레이하는 방법 및 OgcMapLayer 클래스에서 다른 옵션을 사용하는 방법에 대해 알아봅니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: azure-maps
ms.openlocfilehash: 43fec3aacb1bc22566f4e918166f5e004110a563
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123437691"
---
# <a name="add-a-map-layer-from-the-open-geospatial-consortium-ogc"></a>OGC(Open Geospatial Consortium)에서 지도 계층 추가

`atlas.layer.OgcMapLayer` 클래스는 지도에서 WMS(웹 맵 서비스) 이미지와 WMTS(웹 지도 타일 서비스) 이미지를 오버레이로 사용할 수 있습니다. WMS는 인터넷을 통해 좌표가 참조된 지도 이미지를 제공하기 위해 OGC에서 개발한 표준 프로토콜입니다. 이미지 좌표 참조는 지리적 위치에 이미지를 연결하는 프로세스입니다. WMTS는 OGC에서 개발한 표준 프로토콜이기도 합니다. 미리 렌더링된 좌표 참조 지도 타일을 제공하기 위해 디자인되었습니다.

다음 섹션에서는 `OgcMapLayer` 클래스에서 지원하는 웹 지도 서비스 기능을 간략하게 설명합니다.

**WMS(웹 지도 서비스)**

- 지원되는 버전: `1.0.0`, `1.1.0`, `1.1.1`, `1.3.0`
- 서비스에서 `EPSG:3857` 프로젝션 시스템을 지원하거나 재프로젝션을 처리해야 합니다.
- GetFeatureInfo를 사용하려면 서비스가 `EPSG:4326`을 지원하거나 재프로젝션을 처리해야 합니다. 
- 지원되는 작업:

    | 작업(Operation) | Description |
    | :-- | :-- |
    | GetCapabilities | 지원되는 기능을 통해 서비스에 대한 메타데이터를 검색합니다 |
    | GetMap | 지정된 영역에 대한 지도 이미지를 검색합니다 |
    | GetFeatureInfo | 기능에 대한 기본 데이터를 포함하는 `feature_info`를 검색합니다 |

**WMTS(웹 지도 타일 서비스)**

- 지원되는 버전: `1.0.0`
- 타일은 `TileWidth == TileHeight`처럼 사각형이어야 합니다.
- 지원되는 CRS: `EPSG:3857` 또는 `GoogleMapsCompatible` 
- TileMatrix 식별자는 지도의 확대/축소 수준에 해당하는 정수 값이어야 합니다. Azure 지도에서 확대/축소 수준은 `"0"`과 `"22"` 사이의 값입니다. 따라서 `"0"`은 지원되지만 `"00"`은 지원되지 않습니다.
- 지원되는 작업:

    | 작업(Operation) | Description |
    | :-- | :-- |
    | GetCapabilities | 지원되는 작업 및 기능을 검색합니다 |
    | GetTile | 특정 타일에 대한 이미지를 검색합니다 |

## <a name="overlay-an-ogc-map-layer"></a>OGC 지도 계층 오버레이

`url`은 서비스의 기준 URL이거나 서비스의 기능을 가져오기 위한 쿼리가 있는 전체 URL일 수 있습니다. 제공된 세부 정보에 따라 WFS 클라이언트는 몇 가지 표준 URL 형식을 시도하여 서비스에 처음 액세스하는 방법을 결정할 수 있습니다.

다음 코드에서는 맵에 OGC 지도 계층을 오버레이하는 방법을 보여줍니다.

<br/>

<iframe height='700' scrolling='no' title='OGC 지도 계층 예제' src='//codepen.io/azuremaps/embed/xxGLZWB/?height=700&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/xxGLZWB/'>OGC 지도 계층 예제</a>를 참조합니다.
</iframe>

## <a name="ogc-map-layer-options"></a>OGC 지도 계층 옵션

아래 샘플은 다양한 OGC 지도 계층 옵션을 보여줍니다. 오른쪽 위 모서리에 있는 코드 펜 단추를 클릭하여 코드 펜을 편집할 수 있습니다.

<br/>

<iframe height='700' scrolling='no' title='OGC 지도 계층 옵션' src='//codepen.io/azuremaps/embed/abOyEVQ/?height=700&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/abOyEVQ/'>OGC 지도 계층 옵션</a>을 참조합니다.
</iframe>

## <a name="ogc-web-map-service-explorer"></a>OGC 웹 맵 서비스 탐색기

다음 도구는 WMS(웹 맵 서비스) 및 WMTS(웹 지도 타일 서비스)의 이미지를 계층으로 오버레이합니다. 맵에 렌더링되는 서비스의 계층을 선택할 수 있습니다. 이러한 계층에 대한 연결된 범례를 볼 수도 있습니다.

<br/>

<iframe height='750' scrolling='no' title='OGC 웹 맵 서비스 탐색기' src='//codepen.io/azuremaps/embed/YzXxYdX/?height=750&theme-id=0&default-tab=result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true'><a href='https://codepen.io'>CodePen</a>에서 Azure Maps(<a href='https://codepen.io/azuremaps'>@azuremaps</a>)의 펜 <a href='https://codepen.io/azuremaps/pen/YzXxYdX/'>OGC 웹 지도 서비스 탐색기</a>를 참조합니다.
</iframe>

프록시 서비스를 사용하도록 맵 설정을 지정할 수도 있습니다. 프록시 서비스를 사용하면 CORS를 사용하지 않는 도메인에서 호스트되는 리소스를 로드할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 사용된 클래스 및 메서드에 대해 자세히 알아봅니다.

> [!div class="nextstepaction"]
> [OgcMapLayer](/javascript/api/azure-maps-spatial-io/atlas.layer.ogcmaplayer)

> [!div class="nextstepaction"]
> [OgcMapLayerOptions](/javascript/api/azure-maps-spatial-io/atlas.ogcmaplayeroptions)

맵에 추가할 수 있는 코드 샘플을 포함하는 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [WFS 서비스에 연결](spatial-io-connect-wfs-service.md)

> [!div class="nextstepaction"]
> [핵심 작업 활용](spatial-io-core-operations.md)

> [!div class="nextstepaction"]
> [지원되는 데이터 형식 세부 정보](spatial-io-supported-data-format-details.md)