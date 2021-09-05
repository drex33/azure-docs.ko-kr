---
title: Microsoft Azure Maps의 Mobility Services(미리 보기) 데이터 구조
description: Azure Maps Mobility Services(미리 보기)에서 데이터를 지하철 영역으로 구성하는 방법을 이해합니다. 대중 교통 정류장 및 노선에 대한 정보를 저장하는 필드를 확인합니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: a64049ab544b2ec790d3625dad9137ae539f3020
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566620"
---
# <a name="data-structures-in-azure-maps-mobility-services-preview"></a>Azure Maps Mobility Services(미리 보기)의 데이터 구조 

> [!IMPORTANT]
> Azure Maps Mobility Services 미리 보기는 사용 중지되었으며 2021년 10월 5일 이후에는 더 이상 사용할 수 없으며 지원되지 않습니다. 다른 모든 Azure Maps API 및 서비스는 이 만료 공지의 영향을 받지 않습니다.
> 자세한 내용은 [Azure Maps Mobility 미리 보기 사용 중지](https://azure.microsoft.com/updates/azure-maps-mobility-services-preview-retirement/)를 참조하세요.

이 문서에서는 [Azure Maps Mobility Services](/rest/api/maps/mobility)의 지하철 영역에 대한 개념을 소개합니다. 이 서비스가 대중 교통 정류장 및 노선에 대해 쿼리될 때 반환되는 일반적인 필드 중 일부에 대해 설명합니다. Mobility Services API를 사용하여 개발하기 전에 이 문서를 읽어 보는 것이 좋습니다.

## <a name="metro-area"></a>지하철 영역

Mobility Services(미리 보기) 데이터는 지원되는 지하철 영역을 기준으로 그룹화됩니다. 지하철 영역은 도시 경계를 따르지 않습니다. 지하철 영역에는 여러 도시, 인구 밀도가 높은 도시 및 주변 도시가 포함될 수 있습니다. 실제로 한 국가/지역 전체가 하나의 지하철 영역일 수 있습니다. 

`metroID`는 [지하철 영역 정보 가져오기 API](/rest/api/maps/mobility/getmetroareainfopreview)를 호출하는 데 사용할 수 있는 지하철 영역 ID입니다. Azure Maps의 "지하철 가져오기" API를 사용하여 교통 수단 유형, 교통 기관, 활성 경고 및 선택한 지하철에 대한 추가 세부 정보를 요청합니다. 지원되는 지하철 영역 및 metroID를 요청할 수도 있습니다. Metro 영역 ID는 변경될 수 있습니다.

**metroID:** 522   **이름:** 시애틀-타코마-벨뷰

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>정류장 ID

대중 교통 정류장은 두 가지 유형의 ID인 [GFTS(General Transit Feed Specification)](http://gtfs.org/) ID 및 Azure Maps 정류장 ID로 참조될 수 있습니다. GFTS ID는 stopKey라고 하며 Azure Maps 정류장 ID는 stopID라고 합니다. 대중 교통 정류장을 자주 참조하는 경우 Azure Maps 정류장 ID를 사용하는 것이 좋습니다. stopID는 더 안정적이며 실제 정류장이 있는 한 동일하게 유지될 가능성이 높습니다. GTFS 정류장 ID는 더 자주 업데이트됩니다. 예를 들어 GTFS 정류장 ID는 GTFS 공급자 요청에 따라 업데이트되거나 새 GTFS 버전이 릴리스될 때 업데이트될 수 있습니다. 실제 정류장에는 변경 사항이 없어도 GTFS 정류장 ID는 변경될 수 있습니다.

시작하려면 [주변 교통 가져오기 API](/rest/api/maps/mobility/getnearbytransitpreview)를 사용하여 가까운 대중 교통 정류장을 요청할 수 있습니다.

## <a name="line-groups-and-lines"></a>노선 그룹 및 노선

Mobility Services(미리 보기)는 노선 및 노선 그룹에 병렬 데이터 모델을 사용합니다. 이 모델은 [GTFS](http://gtfs.org/) 경로 및 트립 데이터에서 상속된 변경 사항을 보다 효율적으로 처리하는 데 사용됩니다.


### <a name="line-groups"></a>노선 그룹

노선 그룹은 동일한 그룹의 논리적 부분인 모든 노선을 그룹화하는 엔터티입니다. 일반적으로 노선 그룹에는 두 노선이 포함됩니다. 한 노선은 지점 A에서 B로, 다른 하나는 지점 B에서 A로 이동합니다. 두 노선은 동일한 대중 교통 기관에 속하며 동일한 노선 번호를 갖습니다. 그러나 노선 그룹에는 노선이 세 개 이상 있거나 한 노선만 있는 경우가 있을 수 있습니다.


### <a name="lines"></a>선

위에서 설명한 것처럼 각 노선 그룹은 일련의 노선으로 구성됩니다. 각 노선 그룹은 두 노선으로 구성되고 각 노선은 방향을 설명합니다.  그러나 노선 그룹을 구성하는 노선이 더 많은 경우가 있습니다. 예를 들어 때때로 특정 환경에서 우회하는 노선이 있습니다. 두 경우 모두 동일한 노선 번호를 가집니다. 또한 노선 그룹은 한 노선으로 구성될 수 있습니다. 단일 방향이 있는 원형 노선은 한 노선을 가진 노선 그룹입니다.

시작하려면 [교통 노선 가져오기 API](/rest/api/maps/mobility/gettransitlineinfopreview)를 사용하여 노선 그룹을 요청할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Mobility Services(미리 보기)를 사용하여 교통 데이터를 요청하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [교통 데이터를 요청하는 방법](how-to-request-transit-data.md)

Mobility Services(미리 보기)를 사용하여 실시간 데이터를 요청하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [실시간 데이터를 요청하는 방법](how-to-request-real-time-data.md)

Azure Maps Mobility Services(미리 보기) API 설명서 살펴보기

> [!div class="nextstepaction"]
> [Mobility Services API 설명서](/rest/api/maps/mobility)