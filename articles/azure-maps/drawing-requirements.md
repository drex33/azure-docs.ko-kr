---
title: Microsoft Azure Maps Creator의 그리기 패키지 요구 사항
description: 시설 디자인 파일을 맵 데이터로 변환하기 위한 그리기 패키지 요구 사항에 대해 알아보기
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/02/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.openlocfilehash: 2e6b380b040697a56179f5fec7f7d10796f035fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536703"
---
# <a name="drawing-package-requirements"></a>그리기 패키지 요구 사항

[Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)를 사용하여 업로드된 그리기 패키지를 맵 데이터로 변환할 수 있습니다. 이 문서에서는 Conversion API의 그리기 패키지 요구 사항에 대해 설명합니다. 패키지 샘플을 보려면 [그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples) 샘플을 다운로드하면 됩니다.

그리기 패키지를 준비하는 방법에 대한 지침은 [그리기 패키지 변환 가이드](drawing-package-guide.md)를 참조하세요.


## <a name="prerequisites"></a>사전 요구 사항

그리기 패키지에는 DWG 형식으로 저장된 그리기가 포함되어 있으며, 이는 Autodesk AutoCAD® 소프트웨어의 원시 파일 형식입니다.

그리기 패키지에서 그리기를 생성할 CAD 소프트웨어를 선택할 수 있습니다.  

[Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)는 그리기 패키지를 맵 데이터로 변환합니다. 변환 서비스는 AutoCAD DWG 파일 형식 `AC1032`로 작동합니다.


## <a name="glossary-of-terms"></a>용어집

쉽게 참조할 수 있도록 해당 문서를 읽는 데 중요한 몇 가지 용어와 정의를 소개합니다.

| 용어  | 정의 |
|:-------|:------------|
| 계층 | 그리기 파일의 AutoCAD DWG 레이어.|
| 엔터티 | 그리기 파일의 AutoCAD DWG 엔터티. |
| Xref  | 주 그리기에 외부 참조로 첨부된 AutoCAD DWG 파일 형식의 파일입니다.  |
| Level | 설정된 고도에 있는 건물의 영역입니다. 예를 들어 건물의 층입니다. |
| 기능 | 기하 도형을 메타데이터 정보와 결합하는 변환 서비스에서 생성된 개체의 인스턴스입니다. |
| 기능 클래스 | 기능에 대한 일반적인 청사진입니다. 예를 들어 ‘단위’는 기능 클래스이고, ‘사무실’은 기능입니다.  |

## <a name="drawing-package-structure"></a>그리기 패키지 구조

그리기 패키지는 다음 파일이 포함된 .zip 보관 파일입니다.

- AutoCAD DWG 파일 형식의 DWG 파일
- 그리기 패키지의 DWG 파일을 설명하는 _manifest.json_ 파일입니다.

그리기 패키지는 .zip 확장자를 사용하여 단일 보관 파일로 압축해야 합니다. DWG 파일은 패키지 내에서 어떤 방식으로든 구성할 수 있지만, 매니페스트 파일은 압축된 패키지의 루트 디렉터리에 있어야 합니다. 다음 섹션에서는 DWG 파일, 매니페스트 파일 및 이러한 파일의 내용에 대한 요구 사항을 자세히 설명합니다. 샘플 패키지를 보려면 [샘플 그리기 패키지](https://github.com/Azure-Samples/am-creator-indoor-data-examples)를 다운로드하면 됩니다.

## <a name="dwg-file-conversion-process"></a>DWG 파일 변환 프로세스

[Azure Maps 변환 서비스](/rest/api/maps/v2/conversion)는 각 DWG 파일에 대해 다음을 수행합니다.

- 기능 클래스를 추출합니다.
    - Levels
    - Units
    - 영역
    - 통로
    - 벽
    - 수직 침투
- *시설* 기능을 생성합니다.  
- 다른 기능에서 참조할 기본 범주 기능의 최소 세트를 생성합니다.
    - 객실
    - structure
    - wall
    - opening.door
    - 영역
    - facility
    
## <a name="dwg-file-requirements"></a>DWG 파일 요구 사항

각 시설 수준에는 단일 DWG 파일이 필요합니다. 단일 수준의 모든 데이터는 단일 DWG 파일에 포함되어야 합니다.  외부 참조(_xrefs_)는 부모 그리기에 바인딩되어야 합니다. 예를 들어 세 수준의 시설에는 그리기 패키지에 세 개의 DWG 파일이 있습니다.

각 DWG 파일은 다음 요구 사항을 준수해야 합니다.

- DWG 파일은 _실외_ 및 _단위_ 레이어를 정의해야 합니다. 필요에 따라 정의할 수 있는 레이어는 _Wall_, _Door_, _UnitLabel_, _Zone_ 및 _ZoneLabel_ 입니다.
- DWG 파일은 여러 수준의 기능을 포함할 수 없습니다.
- DWG 파일은 여러 시설의 기능을 포함할 수 없습니다.
- DWG는 그리기 패키지의 다른 DWG 파일과 동일한 측정 시스템 및 측정 단위를 참조해야 합니다.

## <a name="dwg-layer-requirements"></a>DWG 계층 요구 사항

각 DWG 계층은 다음 규칙을 따라야 합니다.

- 계층에는 단일 클래스의 기능이 단독으로 포함되어야 합니다. 예를 들어 단위와 벽은 동일한 계층에 있을 수 없습니다.
- 단일 기능 클래스를 여러 계층으로 나타낼 수 있습니다.
- 자체 교차 다각형은 허용되지만 자동으로 복구됩니다. 복구하는 경우 [Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)에서 경고가 발생합니다. 복구된 결과가 예상 결과와 일치하지 않을 수 있으므로 수동으로 검사하는 것이 좋습니다.
- 각 계층에는 지원되는 엔터티 형식 목록이 있습니다. 계층의 다른 모든 엔터티 형식은 무시됩니다. 예를 들어 텍스트 엔터티는 벽 계층에서 지원되지 않습니다.

아래 표에는 각 레이어에 지원되는 엔터티 형식과 변환된 맵 기능이 간략히 나와 있습니다. 계층에 지원되지 않는 엔터티 형식이 포함되면 [Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)에서 해당 엔터티를 무시합니다.  

| 계층 | 엔터티 형식 | 변환된 기능 |
| :----- | :-------------------| :-------
| [실외](#exterior-layer) | 다각형, 폴리라인(닫힌), 원, 타원(닫힌) | Levels
| [단위](#unit-layer) |  다각형, 폴리라인(닫힌), 원, 타원(닫힌) |  단위 및 수직 침투
| [벽](#wall-layer)  | 다각형, 폴리라인(닫힌), 원, 타원(닫힌), 구조 |
| [문](#door-layer) | 다각형, 폴리라인(닫힌), 선, 원호(CircularArc), 원 | 통로
| [영역](#zone-layer) | 다각형, 폴리라인(닫힌), 원, 타원(닫힌) | 영역
| [UnitLabel(단위 레이블)](#unitlabel-layer) | 텍스트(단일선) | 해당 사항 없음 이 레이어는 속성을 단위 레이어의 단위 기능에만 추가할 수 있습니다. 자세한 내용은 [UnitLabel 레이어](#unitlabel-layer)를 참조하세요.
| [ZoneLabel(영역 레이블)](#zonelabel-layer) | 텍스트(단일선) | 해당 사항 없음 이 레이어는 속성을 ZonesLayer의 영역 기능에만 추가할 수 있습니다. 자세한 내용은 [ZoneLabel 레이어](#zonelabel-layer)를 참조하세요.

아래 섹션에서는 각 계층의 요구 사항에 대해 자세히 설명합니다.

### <a name="exterior-layer"></a>실외 레이어

각 수준에 대한 DWG 파일에는 해당 수준의 경계를 정의하는 레이어가 포함되어야 합니다. 이 레이어를 ‘실외’ 레이어라고 합니다. 예를 들어 두 개의 수준이 시설에 포함된 경우 각 파일에 대한 실외 레이어가 포함된 두 개의 DWG 파일이 있어야 합니다.

실외 레이어에 있는 엔터티 그리기의 수와 관계없이 [결과 시설 데이터 세트](tutorial-creator-indoor-maps.md#create-a-feature-stateset)에는 각 DWG 파일에 대해 하나의 수준 기능만 포함됩니다. 추가 필수 구성 요소:

- 실외는 다각형, 폴리라인(닫힌), 원 또는 타원(닫힌)으로 그려야 합니다.
- 실외는 겹칠 수 있지만 하나의 기하 도형으로 변합니다.
- 결과 수준 기능은 4평방미터 이상이어야 합니다.
- 결과 수준 기능은 400,000제곱미터를 초과하면 안 됩니다.

여러 개의 겹치는 폴리라인이 레이어에 포함되면 폴리라인이 단일 수준 기능으로 변합니다. 또는 여러 개의 겹치지 않는 폴리라인이 계층에 포함되면 결과 수준 기능에 다중 다각형 표현이 포함됩니다.

실외 레이어의 예제는 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 윤곽선 레이어로 확인할 수 있습니다.

### <a name="unit-layer"></a>단위 레이어

각 수준에 대한 DWG 파일은 단위가 포함된 레이어를 정의합니다. 단위는 사무실, 복도, 계단 및 엘리베이터와 같은 건물 내에서 탐색 가능한 공간입니다. `VerticalPenetrationCategory` 속성이 정의된 경우, 여러 수준(예: 엘리베이터 및 계단)에 걸쳐 있는 탐색 가능한 단위는 수직 침투 기능으로 변환됩니다. 서로 겹치는 수직 침투 기능에는 하나의 `setid`가 할당됩니다.

단위 레이어는 다음 요구 사항을 따라야 합니다.

- 단위는 다각형, 폴리라인(닫힌), 원 또는 타원(닫힌)으로 그려야 합니다.
- 단위는 시설 외부 경계의 범위 내에 있어야 합니다.
- 단위는 부분적으로 겹칠 수 없습니다.
- 단위는 자체 교차 기하 도형을 포함할 수 없습니다.

UnitLabel 레이어에서 텍스트 개체를 만들어 단위의 이름을 지정한 다음, 해당 개체를 단위의 범위 내에 배치합니다. 자세한 내용은 [UnitLabel 레이어](#unitlabel-layer)를 참조하세요.

단위 계층의 예제는 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 확인할 수 있습니다.

### <a name="wall-layer"></a>벽 레이어

각 수준에 대한 DWG 파일에는 벽, 기둥, 기타 건물 구조의 물리적 범위를 정의하는 레이어가 포함될 수 있습니다.

- 벽은 다각형, 폴리라인(닫힌), 원 또는 타원(닫힌)으로 그려야 합니다.
- 벽 레이어에는 건물 구조로 해석되는 기하 도형만 포함되어야 합니다.

벽 계층의 예제는 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 확인할 수 있습니다.

### <a name="door-layer"></a>문 레이어

문이 포함된 DWG 계층을 포함할 수 있습니다. 각 문은 단위 레이어에서 단위의 가장자리와 겹쳐야 합니다.

Azure Maps 데이터 세트의 문 통로는 여러 단위 경계와 겹치는 단일 직선 세그먼트로 표시됩니다. 다음 이미지는 문 레이어의 기하 도형을 데이터 세트의 통로 기능으로 변환하는 방법을 보여 줍니다.

![통로를 생성하는 단계를 보여 주는 4개의 그래픽](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>영역 레이어

각 수준에 대한 DWG 파일에는 영역의 물리적 범위를 정의하는 영역 레이어가 포함될 수 있습니다. 영역은 이름이 지정되고 렌더링될 수 있는 탐색 불가능한 공간입니다. 영역은 여러 수준으로 확장될 수 있으며 zoneSetId 속성을 사용하여 함께 그룹화됩니다.

- 영역은 다각형, 폴리라인(닫힌), 원 또는 타원(닫힌)으로 그려야 합니다.
- 영역은 겹칠 수 있습니다.
- 영역은 시설 외부 경계의 내부 또는 외부에 있을 수 있습니다.

ZoneLabel 레이어에서 텍스트 개체를 만들어 영역의 이름을 지정하고, 해당 텍스트 개체를 영역의 범위 내에 배치합니다. 자세한 내용은 [ZoneLabel 레이어](#zonelabel-layer)를 참조하세요.

영역 계층의 예제는 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 확인할 수 있습니다.

### <a name="unitlabel-layer"></a>UnitLabel 레이어

각 수준에 대한 DWG 파일에는 UnitLabel 레이어가 포함될 수 있습니다. UnitLabel 레이어는 단위 레이어에서 추출한 단위에 이름 속성을 추가합니다. 이름 속성이 있는 단위에는 매니페스트 파일에 지정된 추가 세부 정보가 있을 수 있습니다.

- 단위 레이블은 한 줄 텍스트 엔터티여야 합니다.
- 단위 레이블은 해당 단위의 범위 내에 있어야 합니다.
- 단위는 UnitLabel 레이어에서 여러 텍스트 엔터티를 포함할 수 없습니다.

UnitLabel 레이어의 예제는 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 확인할 수 있습니다.

### <a name="zonelabel-layer"></a>ZoneLabel 레이어

각 수준에 대한 DWG 파일에는 ZoneLabel 레이어가 포함될 수 있습니다. 이 레이어는 이름 속성을 영역 레이어에서 추출된 영역에 추가합니다. 이름 속성이 있는 영역에는 매니페스트 파일에 지정된 추가 세부 정보가 있을 수 있습니다.

- 영역 레이블은 한 줄 텍스트 엔터티여야 합니다.
- 영역 레이블은 해당 영역의 범위 내에 있어야 합니다.
- 영역은 ZoneLabel 레이어에서 여러 텍스트 엔터티를 포함할 수 없습니다.

ZoneLabel 레이어의 예제는 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)에서 확인할 수 있습니다.

## <a name="manifest-file-requirements"></a>매니페스트 파일 요구 사항

zip 폴더는 디렉터리의 루트 수준에 있는 매니페스트 파일을 포함해야 하며, 파일 이름은 **manifest.json** 이어야 합니다. 이는 [Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)에서 콘텐츠를 구문 분석할 수 있도록 하는 DWG 파일에 대해 설명합니다. 매니페스트에서 식별된 파일만 수집됩니다. zip 폴더에 있지만 매니페스트에 제대로 나열되지 않은 파일은 무시됩니다.

매니페스트 파일의 `buildingLevels` 개체에 있는 파일 경로는 zip 폴더의 루트에 대한 상대 경로여야 합니다. DWG 파일 이름은 시설 수준의 이름과 정확히 일치해야 합니다. 예를 들어 “Basement(지하실)” 수준에 대한 DWG 파일은 “Basement.dwg”입니다. 수준 2에 대한 DWG 파일은 “level_2.dwg”로 명명됩니다. 수준 이름에 공백이 있는 경우 밑줄을 사용합니다.

매니페스트 개체를 사용하는 경우 요구 사항이 있지만, 모든 개체가 필요한 것은 아닙니다. 아래 표에는 [Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion) 버전 1.1의 필수 개체 및 선택적 개체가 나와 있습니다.

>[!NOTE]
> 달리 지정하지 않는 한, 문자열 속성 형식이 있는 모든 속성은 1,000자를 허용합니다.


| Object | 필수 | 설명 |
| :----- | :------- | :------- |
| `version` | true |매니페스트 스키마 버전입니다. 현재 버전 1.1만 지원됩니다.|
| `directoryInfo` | true | 시설 지리적 정보 및 연락처 정보를 간략히 설명합니다. 또한 거주자 지리적 정보 및 연락처 정보를 설명하는 데에도 사용할 수 있습니다. |
| `buildingLevels` | true | 건물 수준 및 수준 디자인이 포함된 파일 수준을 지정합니다. |
| `georeference` | true | 시설 그리기에 대한 숫자 지리적 정보를 포함합니다. |
| `dwgLayers` | true | 레이어의 이름을 나열하고, 각 레이어에는 자체 기능의 이름이 나열됩니다. |
| `unitProperties` | false | 단위 기능에 대한 추가 메타데이터를 삽입하는 데 사용할 수 있습니다. |
| `zoneProperties` | false | 영역 기능에 대한 추가 메타데이터를 삽입하는 데 사용할 수 있습니다. |

다음 섹션에서는 각 개체의 요구 사항에 대해 자세히 설명합니다.

### `directoryInfo`

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
| `name`      | 문자열 | true   |  건물의 이름입니다. |
| `streetAddress`|    문자열 |    false    | 건물의 주소입니다. |
|`unit`     | 문자열    |  false    |  건물의 단위입니다. |
| `locality` |    문자열 |    false |    도시, 타운, 지역, 환경 또는 지역의 이름입니다.|
| `adminDivisions` |    문자열의 JSON 배열 |    false     | 주소 명칭을 포함하는 배열입니다. 예: (국가, 주) ISO 3166 국가 번호 및 ISO 3166-2 주/지역 코드를 사용합니다. |
| `postalCode` |    문자열    | false    | 메일 분류 코드입니다. |
| `hoursOfOperation` |    문자열 |     false | [OSM Opening Hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) 형식을 따릅니다. |
| `phone`    | 문자열 |    false |    건물과 연결된 전화 번호입니다. |
| `website`    | 문자열 |    false    | 건물과 연결된 웹 사이트입니다. |
| `nonPublic` |    bool    | false | 건물이 대중에게 공개되는지 여부를 지정하는 플래그입니다. |
| `anchorLatitude` | numeric |    false | 시설 앵커(압정)의 위도입니다. |
| `anchorLongitude` | numeric |    false | 시설 앵커(압정)의 경도입니다. |
| `anchorHeightAboveSeaLevel`  | numeric | false | 시설의 1층 높이입니다(해수면 기준, 미터 단위). |
| `defaultLevelVerticalExtent` | numeric | false | 수준의 `verticalExtent`가 정의되지 않은 경우 사용할 이 시설 수준의 기본 높이(두께)입니다. |

### `buildingLevels`

`buildingLevels` 개체에는 건물 수준의 JSON 배열이 포함됩니다.

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`levelName`    |문자열    |true |    설명이 포함된 수준 이름입니다. 예를 들어 1층, 로비, 장애인 주차 공간, 지하실입니다.|
|`ordinal` | integer |    true | 수준의 수직 순서를 결정합니다. 모든 시설에는 서수가 0인 수준이 있어야 합니다. |
|`heightAboveFacilityAnchor` | numeric | false |    앵커 위의 수준 높이입니다(미터 단위). |
| `verticalExtent` | numeric | false | 바닥에서 천장까지의 수준 높이(두께)입니다(미터 단위). |
|`filename` |    문자열 |    true |    건물 수준에 대한 CAD 그리기의 파일 시스템 경로입니다. 건물의 zip 파일의 루트에 대한 상대 경로여야 합니다. |

### `georeference`

| 속성  | 형식 | 필수 | 설명 |
|-----------|------|----------|-------------|
|`lat`    | numeric |    true |    시설 그리기의 원점에 대한 위도의 10진수 표현입니다. 원점 좌표는 WGS84 Web Mercator(`EPSG:3857`)에 있어야 합니다.|
|`lon`    |numeric|    true|    시설 그리기의 원점에 대한 경도의 10진수 표현입니다. 원점 좌표는 WGS84 Web Mercator(`EPSG:3857`)에 있어야 합니다. |
|`angle`|    numeric|    true|   진북 및 그리기의 세로(Y) 축 사이의 시계 방향 각도(도)입니다.   |

### `dwgLayers`

| 속성  | 형식 | 필수 | 설명 |
|-----------|------|----------|-------------|
|`exterior`    |문자열 배열|    true|    외부 건물 프로필을 정의하는 레이어의 이름입니다.|
|`unit`|    문자열 배열|    true|    단위를 정의하는 레이어의 이름입니다.|
|`wall`|    문자열 배열    |false|    벽을 정의하는 레이어의 이름입니다.|
|`door`    |문자열 배열|    false   | 문을 정의하는 레이어의 이름입니다.|
|`unitLabel`    |문자열 배열|    false    |단위 이름을 정의하는 레이어의 이름입니다.|
|`zone` | 문자열 배열    | false    | 영역을 정의하는 레이어의 이름입니다.|
|`zoneLabel` | 문자열 배열 |     false |    영역 이름을 정의하는 레이어의 이름입니다.|

### `unitProperties`

`unitProperties` 개체에는 단위 속성의 JSON 배열이 포함됩니다.

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|`unitName`    |문자열    |true    |이 `unitProperty` 레코드와 연결할 단위의 이름입니다. `unitName`과 일치하는 레이블이 `unitLabel` 레이어에 있는 경우에만 이 레코드가 유효합니다. |
|`categoryName`|    문자열|    false    |단위의 목적입니다. 제공된 렌더링 스타일에서 사용할 수 있는 값 목록은 [여기](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json)에서 사용할 수 있습니다. |
|`occupants`    |directoryInfo 개체의 배열 |false    |단위의 거주자 목록입니다. |
|`nameAlt`|    문자열|    false|    단위의 대체 이름입니다. |
|`nameSubtitle`|    문자열    |false|    단위의 부제목입니다. |
|`addressRoomNumber`|    문자열|    false|    단위의 방, 단위, 아파트, 도구 모음 번호입니다.|
|`verticalPenetrationCategory`|    문자열|    false| 이 속성을 정의하면 결과 기능이 단위가 아닌 VRT(수직 침투)가 됩니다. 수직 침투를 사용하여 위 또는 아래 수준의 다른 수직 침투 기능으로 이동할 수 있습니다. 수직 침투는 [범주](https://aka.ms/pa-indoor-spacecategories) 이름입니다. 이 속성이 정의되면, `categoryName` 속성은 `verticalPenetrationCategory`로 재정의됩니다. |
|`verticalPenetrationDirection`|    문자열|    false    |`verticalPenetrationCategory`가 정의되면 필요에 따라 유효한 이동 방향을 정의합니다. 허용되는 값은 `lowToHigh`, `highToLow`, `both`, `closed`입니다. 기본값은 `both`입니다. 이 값은 대/소문자를 구분합니다.|
| `nonPublic` | bool | false | 단위가 대중에게 공개되는지 여부를 나타냅니다. |
| `isRoutable` | bool | false | 이 속성이 `false`로 설정된 경우, 단위로 가거나 단위를 통과할 수 없습니다. 기본값은 `true`입니다. |
| `isOpenArea` | bool | false | 단위에 연결된 통로 없이 탐색 에이전트가 단위에 들어갈 수 있도록 합니다. 기본적으로 이 값은 통로가 없는 단위에 대해 `true`로, 통로가 있는 단위에 대해 `false`로 설정되어 있습니다. 통로가 없는 단위에 대해 `isOpenArea`를 `false`로 수동 설정하면, 탐색 에이전트가 도달할 수 없기 때문에 경고가 발생합니다.|

### `zoneProperties`

`zoneProperties` 개체에는 영역 속성의 JSON 배열이 포함됩니다.

| 속성  | 형식 | 필수 | Description |
|-----------|------|----------|-------------|
|zoneName        |문자열    |true    |`zoneProperty` 레코드와 연결할 영역의 이름입니다. `zoneName`과 일치하는 레이블이 영역의 `zoneLabel` 레이어에 있는 경우에만 이 레코드가 유효합니다.  |
|categoryName|    문자열|    false    |영역의 용도. 제공된 렌더링 스타일에서 사용할 수 있는 값 목록은 [여기](https://atlas.microsoft.com/sdk/javascript/indoor/0.1/categories.json)에서 사용할 수 있습니다.|
|zoneNameAlt|    문자열|    false    |영역의 대체 이름입니다.  |
|zoneNameSubtitle|    문자열 |    false    |영역의 부제목입니다. |
|zoneSetId|    문자열 |    false    | 그룹으로 쿼리하거나 선택할 수 있도록 여러 영역 간에 관계를 수립하려면 ID를 설정합니다. 예를 들어 여러 수준에 걸쳐 있는 영역이 있습니다. |

### <a name="sample-drawing-package-manifest"></a>그리기 패키지 매니페스트 샘플

다음은 그리기 패키지 샘플에 대한 매니페스트 파일입니다. 전체 패키지를 다운로드하려면 [그리기 패키지 샘플](https://github.com/Azure-Samples/am-creator-indoor-data-examples)을 확인하세요.

#### <a name="manifest-file"></a>매니페스트 파일

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

그리기 패키지에서 요구 사항이 충족되면 [Azure Maps Conversion 서비스](/rest/api/maps/v2/conversion)를 사용하여 패키지를 맵 데이터 세트로 변환할 수 있습니다. 그런 다음, 데이터 세트를 사용하여 실내 맵 모듈을 통해 실내 맵을 생성할 수 있습니다.

> [!div class="nextstepaction"]
> [Creator Facility Ontology](creator-facility-ontology.md)

> [!div class="nextstepaction"]
> [실내 지도용 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [그리기 패키지 가이드](drawing-package-guide.md)

> [!div class="nextstepaction"]
>[실내 지도용 Creator](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [자습서: Creator 실내 맵 만들기](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [실내 맵 동적 스타일 지정](indoor-map-dynamic-styling.md)