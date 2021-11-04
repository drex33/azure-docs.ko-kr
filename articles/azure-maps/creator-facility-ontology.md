---
title: Microsoft Azure Maps Creator의 시설 온톨로지
description: Azure Maps Creator의 기능 클래스 정의를 설명하는 시설 온톨로지
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/14/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
zone_pivot_groups: facility-ontology-schema
ms.openlocfilehash: 3ef3d9dd9d992e17c66516de8d13978e6c7c4821
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450802"
---
# <a name="facility-ontology"></a>Facility Ontology

시설 온톨로지는 Azure Maps Creator가 내부적으로 Creator 데이터 세트에 시설 데이터를 저장하는 방법을 정의합니다.  내부 시설 데이터 구조를 정의하는 것 외에도 시설 온톨로지는 WFS API를 통해 외부에 공개됩니다. WFS API를 사용하여 데이터 세트에서 시설 데이터를 쿼리하는 경우 응답 형식은 해당 데이터 세트에 제공된 온톨로지에 의해 정의됩니다.

개략적으로 시설 온톨로지는 데이터 세트를 기능 클래스로 나눕니다. 모든 기능 클래스는 `ID` 및 `Geometry`와 같은 공통 속성 세트를 공유합니다.  공통 속성 세트 외에도 각 기능 클래스는 속성 세트를 정의합니다. 각 속성은 해당 데이터 형식 및 제약 조건에 의해 정의됩니다. 일부 기능 클래스에는 다른 기능 클래스에 종속된 속성이 있습니다. 종속 속성은 또 다른 기능 클래스의 `ID`로 평가됩니다.  

## <a name="changes-and-revisions"></a>변경 내용 및 수정 버전

:::zone pivot="facility-ontology-v1"

Facility 1.0에는 [Azure Maps Services](https://aka.ms/AzureMaps)에 대한 Facility 기능 클래스 정의의 수정 버전이 포함됩니다.

:::zone-end

:::zone pivot="facility-ontology-v2"

Facility 2.0에는 [Azure Maps Services](https://aka.ms/AzureMaps)에 대한 Facility 기능 클래스 정의의 수정 버전이 포함됩니다.

:::zone-end

### <a name="major-changes"></a>주요 변경 내용

:::zone pivot="facility-ontology-v1"

다음 제약 조건 유효성 검사를 수정했습니다.

* `isObstruction = true`의 배타성 ‘또는’ `lineElement` 및 `areaElement` 기능 클래스에 대한 `obstructionArea`의 존재를 확인하기 위한 제약 조건 유효성 검사입니다.

* `isRoutable = true`의 배타성 ‘또는’ `category` 기능 클래스에 대한 `routeThroughBehavior`의 존재를 확인하기 위한 제약 조건 유효성 검사입니다.
:::zone-end

:::zone pivot="facility-ontology-v2"

* 벽, 기둥 등을 포함할 구조 기능 클래스를 추가했습니다.
* 라우팅 시나리오를 보강하도록 디자인된 특성을 정리했습니다. 현재 라우팅 엔진은 해당 특성을 지원하지 않습니다.

:::zone-end

## <a name="unit"></a>단위

`unit` 기능 클래스는 탐색 에이전트가 점유하고 트래버스할 수 있는 겹치지 않는 물리적 영역을 정의합니다. `unit`은 현관, 방, 안마당 등일 수 있습니다.

**기하 도형 형식**: Polygon

:::zone pivot="facility-ontology-v1"

| 속성           | 형식                        | 필수 | Description                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)     |true      | [`category`](#category) 기능의 ID입니다.|
|`isOpenArea`        | 부울(기본값은 `null`)                    |false     | 단위가 열린 영역인지 여부를 나타냅니다. `true`로 설정하면 [구조](#structure)가 단위 경계를 둘러싸지 않고 탐색 에이전트가 [`opening`](#opening) 없이도 `unit`에 들어갈 수 있습니다. 기본적으로 단위는 물리적 장벽으로 둘러싸고 통로 기능이 단위 경계에 배치되는 경우에만 열립니다. 열린 영역 단위에 벽이 필요한 경우 벽은 `true`와 같은 `isObstruction` 속성을 사용하여 [`lineElement`](#lineelement) 또는 [`areaElement`](#areaelement)로 나타낼 수 있습니다.|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |단위를 트래버스할 수 있는 탐색 에이전트의 유형을 나타냅니다. 지정되지 않은 경우 단위는 탐색 에이전트에 의해 트래버스될 수 있다고 가정합니다. |
|`isRoutable`        | 부울(기본값은 `null`)                      | false    |  단위가 라우팅 그래프의 일부인지 확인합니다. `true`로 설정하면 단위는 라우팅 환경의 원본/대상 또는 중간 노드로 사용될 수 있습니다. |
|`routeThroughBehavior`        | enum ["disallowed", "allowed", "preferred"] |  false     | 단위 탐색이 허용되는지 확인합니다. 지정되지 않은 경우 `categoryId` 속성에서 참조되는 범주 기능에서 해당 값을 상속합니다. 지정된 경우 범주 기능에 지정된 값을 재정의합니다. |
|`nonPublic`        |  boolean| false       | `true`이면 단위는 권한 있는 사용자만 탐색할 수 있습니다.  기본값은 `false`여야 합니다. |
| `levelId`          | [level.Id](#level)        | true     | 수준 기능의 ID입니다. |
|`occupants`         |  [directoryInfo.Id](#directoryinfo)의 배열 |    false |    [directoryInfo](#directoryinfo) 기능의 ID입니다. 기능에서 하나 이상의 점유자를 나타내는 데 사용됩니다. |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | [directoryInfo](#directoryinfo) 기능의 ID입니다. 기능의 주소를 나타내는 데 사용됩니다.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | 단위의 방/단위/콘도/스위트룸 번호입니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| 속성           | 형식                        | 필수 | Description                                                  |
|--------------------|-----------------------------|----------|--------------------------------------------------------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)     |true      | [`category`](#category) 기능의 ID입니다.|
|`isOpenArea`        | 부울(기본값은 `null`)                    |false     | 단위가 열린 영역인지 여부를 나타냅니다. `true`로 설정하면 [구조](#structure)가 단위 경계를 둘러싸지 않고 탐색 에이전트가 [`opening`](#opening) 없이도 `unit`에 들어갈 수 있습니다. 기본적으로 단위는 물리적 장벽으로 둘러싸고 통로 기능이 단위 경계에 배치되는 경우에만 열립니다. 열린 영역 단위에 벽이 필요한 경우 벽은 `true`와 같은 `isObstruction` 속성을 사용하여 [`lineElement`](#lineelement) 또는 [`areaElement`](#areaelement)로 나타낼 수 있습니다.|
|`isRoutable`        | 부울(기본값은 `null`)                     | false    |  단위가 라우팅 그래프의 일부인지 확인합니다. `true`로 설정하면 단위는 라우팅 환경의 원본/대상 또는 중간 노드로 사용될 수 있습니다. |
| `levelId`          | [level.Id](#level)        | true     | 수준 기능의 ID입니다. |
|`occupants`         |  [directoryInfo.Id](#directoryinfo)의 배열 |    false |    [directoryInfo](#directoryinfo) 기능의 ID입니다. 기능에서 하나 이상의 점유자를 나타내는 데 사용됩니다. |
|`addressId`         | [directoryInfo.Id](#directoryinfo) | true     | [directoryInfo](#directoryinfo) 기능의 ID입니다. 기능의 주소를 나타내는 데 사용됩니다.|
|`addressRoomNumber`         |  [directoryInfo.Id](#directoryinfo) | true     | 단위의 방/단위/콘도/스위트룸 번호입니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

:::zone pivot="facility-ontology-v2"

## <a name="structure"></a>structure

`structure` 기능 클래스는 탐색할 수 없는 겹치지 않는 물리적 영역을 정의합니다. 벽, 기둥 등일 수 있습니다.

**기하 도형 형식**: Polygon

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `levelId`          |  [level.Id](#level)            | true     | [`level`](#level) 기능의 ID입니다. |
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다. 허용되는 최대 길이는 1000입니다. |
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

## <a name="zone"></a>영역

`zone` 기능 클래스는 WiFi 영역 또는 응급 어셈블리 영역과 같은 가상 영역을 정의합니다. 영역은 대상으로 사용할 수 있지만 통과 트래픽에 사용되지 않습니다.

**기하 도형 형식**: Polygon

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `setId`          | 문자열         | true     |다중 수준 영역을 나타내는 영역 기능에 필요합니다. `setId`는 여러 수준에 걸쳐 있는 영역의 고유 ID입니다. `setId`를 사용하면 여러 층의 다양한 적용 범위가 있는 영역을 다양한 수준의 다른 기하 도형으로 나타낼 수 있습니다. `setId`는 임의 문자열일 수 있으며 대/소문자를 구분합니다. `setId`는 GUID인 것이 좋습니다.  허용되는 최대 길이는 1000입니다.|
| `levelId`          |  [level.Id](#level)             | true     | [`level`](#level) 기능의 ID입니다. |
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

## <a name="level"></a>수준

`level` 클래스 기능은 설정된 입면도에서 건물 영역을 정의합니다. 예를 들어, [`units`](#unit)와 같은 기능 세트가 포함된 건물의 층입니다.  

**기하 도형 형식**: MultiPolygon

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)    |true      | [`category`](#category) 기능의 ID입니다.|
| `ordinal`          | integer        | true     | 수준 번호입니다. [`verticalPenetration`](#verticalpenetration) 기능에서 이동 방향에 도움이 되는 층의 상대적 순서를 결정하는 데 사용됩니다. 일반적인 사례는 1층을 0으로 시작하는 것입니다. 위쪽으로 모든 층에 대해 +1을 추가하고 아래쪽으로 모든 층에 대해 -1을 추가합니다. 더 높은 물리적 층이 더 높은 서수 값으로 표시되는 경우 숫자로 모델링할 수 있습니다. |
| `abbreviatedName`          | 문자열        | false     | 엘리베이터 단추에 있는 것과 같은 4자 약식 수준 이름입니다.  허용되는 최대 길이는 1000입니다.|
| `heightAboveFacilityAnchor`          | double         | false     | [`facility.anchorHeightAboveSeaLevel`](#facility) 위에 있는 수준 층의 세로 거리(미터)입니다. |
| `verticalExtent`          | double         | false     | 수준의 세로 범위(미터)입니다. 지정하지 않으면 기본값은 [`facility.defaultLevelVerticalExtent`](#facility)입니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`anchorPoint` |   [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

## <a name="facility"></a>facility

`facility` 기능 클래스는 사이트, 건물 공간 등의 영역을 정의합니다.

**기하 도형 형식**: MultiPolygon

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
|`occupants`         | [directoryInfo.Id](#directoryinfo)의 배열 |    false |    [directoryInfo](#directoryinfo) 기능의 ID입니다. 기능에서 하나 이상의 점유자를 나타내는 데 사용됩니다. |
|`addressId`         | [directoryInfo.Id](#directoryinfo)  | true     | [directoryInfo](#directoryinfo) 기능의 ID입니다. 기능의 주소를 나타내는 데 사용됩니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다. 허용되는 최대 길이는 1000입니다. |
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|
|`anchorHeightAboveSeaLevel` |  double | false | 해수면 위 앵커 지점의 높이(미터)입니다. 해수면은 EGM 2008에 의해 정의됩니다.|
|`defaultLevelVerticalExtent` |  double| false | 수준 세로 범위의 기본값(미터)입니다.|

## <a name="verticalpenetration"></a>verticalPenetration

`verticalPenetration` 클래스 기능은 세트에서 사용되는 경우 수준 사이에서 세로로 탐색하는 방법을 나타내는 영역을 정의합니다. 이를 사용하여 계단, 엘리베이터 등을 모델링할 수 있습니다. 기하 도형은 단위 및 기타 세로 침투 기능을 겹칠 수 있습니다.

**기하 도형 형식**: Polygon

:::zone pivot="facility-ontology-v1"

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `setId`          | 문자열       | true     | 여러 수준을 연결하려면 세트에서 세로 침투 기능을 사용해야 합니다. 동일한 세트의 세로 침투 기능은 동일한 것으로 간주합니다. `setId`는 임의 문자열일 수 있으며 대/소문자를 구분합니다. GUID를 `setId`로 사용하는 것이 좋습니다.  허용되는 최대 길이는 1000입니다.|
| `levelId`          | [level.Id](#level)         | true     | 수준 기능의 ID입니다. |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | 이 기능에서 허용되는 이동 방향입니다. [`level`](#level) 기능의 서수 특성은 낮은 순서 및 높은 순서를 결정하는 데 사용됩니다.|
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |단위를 트래버스할 수 있는 탐색 에이전트의 유형을 나타냅니다. 지정되지 않은 경우 단위는 탐색 에이전트에 의해 트래버스될 수 있습니다. |
|`nonPublic`        |  boolean| false       | `true`이면 단위는 권한 있는 사용자만 탐색할 수 있습니다.  기본값은 `false`여야 합니다. |
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        | [category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `setId`          | 문자열       | true     | 여러 수준을 연결하려면 세트에서 세로 침투 기능을 사용해야 합니다. 동일한 세트의 세로 침투 기능이 연결됩니다. `setId`는 임의 문자열일 수 있으며 대/소문자를 구분합니다. GUID를 `setId`로 사용하는 것이 좋습니다. 허용되는 최대 길이는 1000입니다. |
| `levelId`          | [level.Id](#level)         | true     | 수준 기능의 ID입니다. |
|`direction`         |  string enum [ "both", "lowToHigh", "highToLow", "closed" ]| false     | 이 기능에서 허용되는 이동 방향입니다. [`level`](#level) 기능의 서수 특성은 낮은 순서 및 높은 순서를 결정하는 데 사용됩니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

## <a name="opening"></a>열려 있음

`opening` 클래스 기능은 두 단위 사이 또는 `unit`과 `verticalPenetration` 사이에 트래버스 가능한 경계를 정의합니다.

**기하 도형 형식**: LineString

:::zone pivot="facility-ontology-v1"

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        |[category.Id](#category)     |true      | 범주 기능의 ID입니다.|
| `levelId`          | [level.Id](#level)        | true     | 수준 기능의 ID입니다. |
| `isConnectedToVerticalPenetration` | boolean | false | 이 기능이 옆면 중 하나에 있는 `verticalPenetration` 기능에 연결되는지 여부입니다. 기본값은 `false`여야 합니다. |
|`navigableBy`        | enum ["pedestrian", "wheelchair", "machine", "bicycle", "automobile", "hiredAuto", "bus", "railcar", "emergency", "ferry", "boat"]  | false      |단위를 트래버스할 수 있는 탐색 에이전트의 유형을 나타냅니다. 지정되지 않은 경우 단위는 탐색 에이전트에 의해 트래버스될 수 있습니다. |
| `accessRightToLeft`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | 오른쪽에서 왼쪽으로 통로를 통과하는 경우 액세스 방법입니다. 왼쪽 및 오른쪽은 첫 번째 꼭짓점에 고정되어 두 번째 꼭짓점과 마주 보는 기능 기하 도형의 꼭짓점에 의해 결정됩니다. 이 속성을 생략하는 것은 액세스 제한 사항이 없음을 의미합니다.|
| `accessLeftToRight`| enum [ "prohibited", "digitalKey", "physicalKey", "keyPad", "guard", "ticket", "fingerprint", "retina", "voice", "face", "palm", "iris", "signature", "handGeometry", "time", "ticketChecker", "other"] | false | 왼쪽에서 오른쪽으로 통로를 통과하는 경우 액세스 방법입니다. 왼쪽 및 오른쪽은 첫 번째 꼭짓점에 고정되어 두 번째 꼭짓점과 마주 보는 기능 기하 도형의 꼭짓점에 의해 결정됩니다. 이 속성을 생략하는 것은 액세스 제한 사항이 없음을 의미합니다.|
| `isEmergency` | boolean | false | `true`이면 통로는 응급 상황 중에만 탐색할 수 있습니다. 기본값은 `false` |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson) y입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

:::zone pivot="facility-ontology-v2"

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        |[category.Id](#category)     |true      | 범주 기능의 ID입니다.|
| `levelId`          | [level.Id](#level)        | true     | 수준 기능의 ID입니다. |
|`anchorPoint` | [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson) y입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

:::zone-end

## <a name="directoryinfo"></a>directoryInfo

`directoryInfo` 개체 클래스 기능은 단위, 시설이나 단위 또는 시설 점유자의 이름, 주소, 전화번호, 웹 사이트 및 작업 시간을 정의합니다.

**기하 도형 형식**: 없음

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`streetAddress`        |문자열    |false    |주소의 주소 부분입니다.  허용되는 최대 길이는 1000입니다. |
|`unit`        |문자열    |false    |주소의 단위 번호 부분입니다.  허용되는 최대 길이는 1000입니다. |
|`locality`|    문자열|    false    |주소의 구/군/시입니다. 예: 도시, 지방 자치 단체, 마을. 허용되는 최대 길이는 1000입니다.|
|`adminDivisions`|    문자열|    false    |주소의 행정 구역 부분으로, 가장 작은 규모에서 가장 큰 규모 순서(군, 주, 국가)입니다. 예: ["King", "Washington", "USA" ] 또는 ["West Godavari", "Andhra Pradesh", "IND" ]. 허용되는 최대 길이는 1000입니다.|
|`postalCode`|    문자열 |    false    |주소의 우편 번호 부분입니다. 허용되는 최대 길이는 1000입니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다. 허용되는 최대 길이는 1000입니다. |
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`phoneNumber` |    문자열 |    false |    전화 번호 허용되는 최대 길이는 1000입니다. |
|`website` |    문자열 |    false |  웹 사이트 URL입니다. 허용되는 최대 길이는 1000입니다. |
|`hoursOfOperation` |    문자열 |    false |   [Open Street Map 사양](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification)에 따른 작업 시간(텍스트)입니다. 허용되는 최대 길이는 1000입니다. |

## <a name="pointelement"></a>pointElement

`pointElement`는 첫 번째 보조 키트 또는 스프링클러 헤드와 같은 단위에서 점 기능을 정의하는 클래스 기능입니다.

**기하 도형 형식**: MultiPoint

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        |[category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `unitId`          | 문자열     | true     | 이 기능을 포함하는 [`unit`](#unit) 기능의 ID입니다.  허용되는 최대 길이는 1000입니다.|
| `isObstruction`          | 부울(기본값은 `null`) | false     | `true`인 경우 이 기능은 포함하는 단위 기능을 통해 라우팅되는 동안 피해야 할 장애물을 나타냅니다. |
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다. 허용되는 최대 길이는 1000입니다. |
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다.  허용되는 최대 길이는 1000입니다.|

## <a name="lineelement"></a>lineElement

`lineElement`는 분리 벽 또는 창과 같은 단위의 선 기능을 정의하는 클래스 기능입니다.

**기하 도형 형식**: LinearMultiString

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        |[category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `unitId`          | 문자열     | true     | 이 기능을 포함하는 [`unit`](#unit) 기능의 ID입니다. 허용되는 최대 길이는 1000입니다. |
| `isObstruction`          | 부울(기본값은 `null`)| false     | `true`인 경우 이 기능은 포함하는 단위 기능을 통해 라우팅되는 동안 피해야 할 장애물을 나타냅니다. |
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다. 허용되는 최대 길이는 1000입니다. |
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|
|`obstructionArea` |   [Polygon](/rest/api/maps/wfs/get-feature#featuregeojson)| false | 라우팅 중에 피해야 하는 기능의 간소화된 기하 도형입니다(선 기하 도형이 복잡한 경우). `isObstruction`을 true로 설정해야 합니다.|

## <a name="areaelement"></a>areaElement

`areaElement`는 단위에서 다각형 기능을 정의하는 클래스 기능입니다(예: 아래로 열린 영역, 단위의 아일랜드 같은 장애물).

**기하 도형 형식**: MultiPolygon

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 기능과 기능을 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`categoryId`        |[category.Id](#category)      |true      | [`category`](#category) 기능의 ID입니다.|
| `unitId`          | 문자열     | true     | 이 기능을 포함하는 [`unit`](#unit) 기능의 ID입니다. 허용되는 최대 길이는 1000입니다. |
| `isObstruction`          | boolean | false     | `true`인 경우 이 기능은 포함하는 단위 기능을 통해 라우팅되는 동안 피해야 할 장애물을 나타냅니다. |
|`obstructionArea` |  geometry: ["Polygon","MultiPolygon" ]| false | 라우팅 중에 피해야 하는 기능의 간소화된 기하 도형입니다(선 기하 도형이 복잡한 경우). `isObstruction`을 true로 설정해야 합니다.|
|`name` |    문자열 |    false |    현지 언어의 기능 이름입니다. 허용되는 최대 길이는 1000입니다. |
|`nameSubtitle` |    문자열 |    false |   기능의 `name` 아래에 표시되는 부제목입니다. 이름을 다른 언어로 표시하는 데 사용할 수 있습니다.  허용되는 최대 길이는 1000입니다.|
|`nameAlt` |    문자열 |    false |   기능에 사용되는 대체 이름입니다.  허용되는 최대 길이는 1000입니다.|
|`anchorPoint` |  [Point](/rest/api/maps/wfs/get-feature#featuregeojson) | false | 기능을 점으로 나타내는 [GeoJSON 점 기하 도형](/rest/api/maps/wfs/get-feature#featuregeojson)입니다. 기능의 레이블을 배치하는 데 사용할 수 있습니다.|

## <a name="category"></a>category

`category` 클래스 기능은 범주 이름을 정의합니다. 예: “room.conference”.

**기하 도형 형식**: 없음

:::zone pivot="facility-ontology-v1"

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 범주의 원래 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 범주와 범주를 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`name` |    문자열 |    true |   범주 이름. “.”를 사용하여 범주의 계층 구조를 나타내는 것이 좋습니다. 예: “room.conference”, “room.privateoffice”. 허용되는 최대 길이는 1000입니다. |
| `routeThroughBehavior` | boolean | false | 통과 트래픽에 기능을 사용할 수 있는지 여부를 결정합니다.|
|`isRoutable`        | 부울(기본값은 `null`)                  | false    |  기능이 라우팅 그래프에 포함되어야 하는지 여부를 결정합니다. `true`로 설정하면 단위는 라우팅 환경의 원본/대상 또는 중간 노드로 사용될 수 있습니다. |

:::zone-end

:::zone pivot="facility-ontology-v2"

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`originalId`        | 문자열     |true      | 클라이언트 데이터에서 파생된 범주의 원래 ID입니다.  허용되는 최대 길이는 1000입니다.|
|`externalId`        | 문자열     |true      | 클라이언트에서 내부 데이터베이스와 같이 다른 데이터 세트의 다른 범주와 범주를 연결하는 데 사용되는 ID입니다. 허용되는 최대 길이는 1000입니다.|
|`name` |    문자열 |    true |   범주 이름. “.”를 사용하여 범주의 계층 구조를 나타내는 것이 좋습니다. 예: “room.conference”, “room.privateoffice”. 허용되는 최대 길이는 1000입니다. |

:::zone-end
