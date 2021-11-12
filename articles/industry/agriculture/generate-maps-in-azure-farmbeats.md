---
title: 맵 생성
description: 이 문서에서는 Azure FarmBeats에서 맵을 생성하는 방법을 설명합니다.
author: RiyazPishori
ms.topic: article
ms.date: 11/04/2019
ms.author: riyazp
ms.openlocfilehash: 0183e56d9bdf0c995dbbcf273914790eb0909e7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132297548"
---
# <a name="generate-maps"></a>맵 생성

Azure FarmBeats를 사용하면 위성 이미지 및 센서 데이터 입력을 사용하여 다음과 같은 맵을 생성할 수 있습니다. 맵은 팜의 지리적 위치를 확인하고 디바이스에 적합한 위치를 식별하는 데 도움이 됩니다.

  - **센서 배치 맵**: 팜에서 사용할 센서의 수와 배치할 위치에 대한 권장 사항을 제공합니다.
  - **위성 인덱스 맵**: 팜에 대한 식생 인덱스 및 수분 인덱스를 표시합니다.
  - **토양 수분 열 지도**: 위성 데이터와 센서 데이터를 융합하여 토양 수분 분포를 표시합니다.

## <a name="sensor-placement-map"></a>센서 배치 맵

FarmBeats 센서 배치 맵은 토양 수분 센서를 배치할 수 있도록 지원합니다. 맵 출력은 센서 배포에 대한 좌표 목록으로 구성됩니다. 이러한 센서의 입력은 위성 이미지와 함께 토양 수분 열 지도를 생성하는 데 사용됩니다.

이 맵은 연중 여러 날짜에 걸쳐 표시된 엽층부(canopy)를 분할하여 파생됩니다. 심지어 토양과 건물도 엽층부의 일부입니다. 위치에 필요하지 않은 센서는 제거할 수 있습니다. 이 맵은 지침을 위한 것이며, 사용자 지정 지식에 따라 위치와 숫자를 약간 변경할 수 있습니다. 센서를 추가해도 토양 수분 열 지도 결과는 회귀하지 않지만, 센서 수가 줄어들면 열 지도 정확도가 떨어질 수 있습니다.

## <a name="before-you-begin"></a>시작하기 전에

센서 배치 맵을 생성하기 전에 다음 필수 구성 요소를 충족해야 합니다.

- 팜 크기는 1에이커를 초과해야 합니다.
- 구름이 없는 Sentinel 장면의 수는 선택한 날짜 범위에 대해 6개를 초과해야 합니다.
- 구름이 없는 6개 이상의 Sentinel 장면에서 NDVI(정규화된 차이 식생 인덱스)는 0.3 이상이어야 합니다.

    > [!NOTE]
    > Sentinel은 사용자당 두 개의 동시 스레드만 허용합니다. 따라서 작업이 큐에서 대기하고 완료하는 데 더 오래 걸릴 수 있습니다.

### <a name="dependencies-on-sentinel"></a>Sentinel에 대한 종속성

Sentinel과 관련된 종속성은 다음과 같습니다.

- 위성 이미지를 다운로드하기 위해 Sentinel 성능을 사용합니다. Sentinel 성능 상태 및 유지 관리 [작업](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)을 확인합니다.
- Sentinel은 사용자당 두 개의 동시 [다운로드 스레드](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services)만 허용합니다.
- 정밀도 맵 생성은 [Sentinel 적용 범위 및 다시 방문 빈도](https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)의 영향을 받습니다.

## <a name="create-a-sensor-placement-map"></a>센서 배치 맵 만들기
이 섹션에서는 센서 배치 맵을 만드는 절차에 대해 자세히 설명합니다.

> [!NOTE]
> 센서 배치 맵은 **맵** 페이지 또는 **팜 세부 정보** 페이지의 **정밀도 맵 생성** 드롭다운 메뉴에서 시작할 수 있습니다.

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동합니다.
2. **맵 만들기** 를 선택하고, 드롭다운 메뉴에서 **센서 배치를** 선택합니다.

    ![센서 배치 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. **센서 배치** 를 선택하면 **센서 배치** 창이 표시됩니다.

    ![센서 배치 창](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. **팜** 드롭다운 메뉴에서 팜을 선택합니다.
   팜을 검색하여 선택하려면 드롭다운 목록에서 스크롤하거나 텍스트 상자에서 팜 이름을 입력합니다.
5. 작년에 대한 맵을 생성하려면 **권장** 을 선택합니다.
6. 사용자 지정 날짜 범위에 대한 맵을 생성하려면 **날짜 범위 선택** 옵션을 선택합니다. 센서 배치 맵을 생성하려는 시작 날짜 및 종료 날짜를 입력합니다.
7. **맵 생성** 을 선택합니다.
 작업 세부 정보가 포함된 확인 메시지가 표시됩니다.

  작업 상태에 대한 자세한 내용은 **작업 보기** 섹션을 참조하세요. 작업 상태가 *실패* 로 표시되면 *실패* 상태에 대한 도구 설명에 자세한 오류 메시지가 표시됩니다. 이 경우 이전 단계를 반복하고 다시 시도합니다.

  문제가 지속되면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조하거나, 관련 로그에 대한 지원을 받으려면 [Azure FarmBeats 포럼](https://aka.ms/FarmBeatsMSDN)에 문의하세요.

### <a name="view-and-download-a-sensor-placement-map"></a>센서 배치 맵 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동합니다.

    ![왼쪽 탐색 메뉴에서 맵 선택](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색 영역에서 **필터** 를 선택합니다.
  **필터** 창에 검색 조건이 표시됩니다.

    ![필터 창](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 드롭다운 메뉴에서 **형식**, **날짜** 및 **이름** 값을 선택합니다. 그런 다음, **적용** 을 선택하여 보려는 맵을 검색합니다.
  작업이 만들어진 날짜는 type_farmname_YYYY-MM-DD 형식으로 표시됩니다.
4. 페이지 끝에 있는 탐색 모음을 사용하여 사용 가능한 맵 목록을 스크롤합니다.
5. 보려는 맵을 선택합니다. 팝업 창에 선택한 맵에 대한 미리 보기가 표시됩니다.
6. **다운로드** 를 선택하고, 센서 좌표의 GeoJSON 파일을 다운로드합니다.

    ![센서 배치 맵 미리 보기](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>위성 인덱스 맵

다음 섹션에서는 위성 인덱스 맵을 만들고 보는 데 관련된 절차에 대해 설명합니다.

> [!NOTE]
> 위성 인덱스 맵은 **맵** 페이지 또는 **팜 세부 정보** 페이지의 **정밀도 맵 생성** 드롭다운 메뉴에서 시작할 수 있습니다.

FarmBeats는 팜에 대한 NDVI, EVI(향상된 식생 인덱스) 및 NDWI(정규화된 차이 수분 인덱스) 맵을 생성하는 기능을 제공합니다. 이러한 인덱스는 농작물의 현재 성장 상태 또는 과거의 성장 상태 및 지표면의 대표적인 수위를 결정하는 데 도움이 됩니다.


> [!NOTE]
> 맵이 생성되는 날짜에는 Sentinel 이미지가 필요합니다.


## <a name="create-a-satellite-indices-map"></a>위성 인덱스 맵 만들기

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동합니다.
2. **맵 만들기** 를 선택하고, 드롭다운 메뉴에서 **위성 인덱스** 를 선택합니다.

    ![드롭다운 메뉴에서 위성 인덱스 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. **위성 인덱스** 를 선택하면 **위성 인덱스** 창이 표시됩니다.

    ![위성 인덱스 창](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. 드롭다운 메뉴에서 팜을 선택합니다.
   팜을 검색하여 선택하려면 드롭다운 목록에서 스크롤하거나 팜 이름을 입력합니다.   
5. 지난 주에 대한 맵을 생성하려면 **이번 주** 를 선택합니다.
6. 사용자 지정 날짜 범위에 대한 맵을 생성하려면 **날짜 범위 선택** 옵션을 선택합니다. 위성 인덱스 맵을 생성하려는 시작 날짜 및 종료 날짜를 입력합니다.
7. **맵 생성** 을 선택합니다.
    작업 세부 정보가 포함된 확인 메시지가 표시됩니다.

    ![위성 인덱스 맵 확인 메시지](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    작업 상태에 대한 자세한 내용은 **작업 보기** 섹션을 참조하세요. 작업 상태가 *실패* 로 표시되면 *실패* 상태에 대한 도구 설명에 자세한 오류 메시지가 표시됩니다. 이 경우 이전 단계를 반복하고 다시 시도합니다.

    문제가 지속되면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조하거나, 관련 로그에 대한 지원을 받으려면 [Azure FarmBeats 포럼](https://aka.ms/FarmBeatsMSDN)에 문의하세요.

### <a name="view-and-download-a-map"></a>맵 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동합니다.

    ![맵 선택](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색 영역에서 **필터** 를 선택합니다. **필터** 창에 검색 조건이 표시됩니다.

    ![필터 창에 검색 조건이 표시됩니다.](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. 드롭다운 메뉴에서 **형식**, **날짜** 및 **이름** 값을 선택합니다. 그런 다음, **적용** 을 선택하여 보려는 맵을 검색합니다.
  작업이 만들어진 날짜는 type_farmname_YYYY-MM-DD 형식으로 표시됩니다.

4. 페이지 끝에 있는 탐색 모음을 사용하여 사용 가능한 맵 목록을 스크롤합니다.
5. **팜 이름** 과 **날짜** 의 각 조합에 대해 다음 세 가지 맵을 사용할 수 있습니다.
    - NDVI
    - EVI
    - NDWI
6. 보려는 맵을 선택합니다. 팝업 창에 선택한 맵에 대한 미리 보기가 표시됩니다.
7. 드롭다운 메뉴에서 **다운로드** 를 선택하여 다운로드 형식을 선택합니다. 맵이 다운로드되어 컴퓨터의 로컬 폴더에 저장됩니다.

    ![선택한 위성 인덱스 맵 미리 보기](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>토양 수분 열 지도

토양 수분은 토양 입자 사이의 공간에 있는 물입니다.토양 수분 열 지도는 팜 내에서 임의의 깊이에 있는 토양 수분 데이터를 고해상도로 이해하는 데 도움이 됩니다. 정확하고 사용 가능한 토양 수분 열 지도를 생성하려면 센서를 균일하게 배포해야 합니다. 모든 센서는 동일한 공급자의 것이어야 합니다. 공급자마다 보정 차이와 함께 토양 수분을 측정하는 방식이 다릅니다. 열 지도는 해당 깊이에 배포된 센서를 사용하여 특정 깊이에 대해 생성됩니다.

### <a name="before-you-begin"></a>시작하기 전에

토양 수분 열 지도를 생성하기 전에 다음 필수 구성 요소를 충족해야 합니다.

- 세 개 이상의 토양 수분 센서를 배포해야 합니다. 센서가 배포되고 팜에 연결되기 전에 토양 수분 열 지도를 만들려고 시도하지 마세요.
- 토양 수분 열 지도를 생성하는 것은 Sentinel의 경로 범위, 구름 적용 범위 및 구름 그림자의 영향을 받습니다. 토양 수분 열 지도는 요청된 날부터 지난 120일 동안 구름이 없는 하나 이상의 Sentinel 장면을 사용할 수 있어야 합니다.
- 팜에 배포된 센서의 절반 이상은 온라인 상태여야 하며, 데이터를 데이터 허브로 스트림해야 합니다.
- 열 지도는 동일한 공급자의 센서 측정값을 사용하여 생성해야 합니다.


## <a name="create-a-soil-moisture-heatmap"></a>토양 수분 열 지도 만들기

다음 단계를 수행합니다.

1. 맵 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동하여 **지도** 페이지를 표시합니다.
2. **맵 만들기** 를 선택하고, 드롭다운 메뉴에서 **토양 수분** 을 선택합니다.

    ![드롭다운 메뉴에서 토양 수분 선택](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. **토양 수분** 을 선택하면 **토양 수분** 창이 표시됩니다.

    ![토양 수분 창](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. **팜** 드롭다운 메뉴에서 팜을 선택합니다.
   팜을 검색하여 선택하려면 드롭다운 목록에서 스크롤하거나 **팜 선택** 드롭다운 메뉴에서 팜 이름을 입력합니다.
5. **수분 센서 측정값 선택** 드롭다운 메뉴에서 맵을 생성하려는 토양 수분 센서 측정값(깊이)을 선택합니다.
센서 측정값을 찾으려면 **센서** 로 이동하여 토양 수분 센서를 선택합니다. 그런 다음, **센서 속성** 섹션 아래에서 **측정값 이름** 의 값을 사용합니다.
6. **오늘** 또는 **이번 주** 에 대한 지도를 생성하려면 옵션 중 하나를 선택합니다.
7. 사용자 지정 날짜 범위에 대한 맵을 생성하려면 **날짜 범위 선택** 옵션을 선택합니다. 토양 수분 열 지도를 생성하려는 시작 날짜 및 종료 날짜를 입력합니다.
8. **맵 생성** 을 선택합니다.
 작업 세부 정보가 포함된 확인 메시지가 표시됩니다.

   ![토양 수분 맵 확인 메시지](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    작업 상태에 대한 자세한 내용은 **작업 보기** 섹션을 참조하세요. 작업 상태가 *실패* 로 표시되면 *실패* 상태에 대한 도구 설명에 자세한 오류 메시지가 표시됩니다. 이 경우 이전 단계를 반복하고 다시 시도합니다.

    문제가 지속되면 [문제 해결](troubleshoot-azure-farmbeats.md) 섹션을 참조하거나, 관련 로그에 대한 지원을 받으려면 [Azure FarmBeats 포럼](https://aka.ms/FarmBeatsMSDN)에 문의하세요.

### <a name="view-and-download-a-map"></a>맵 보기 및 다운로드

다음 단계를 수행합니다.

1. 홈 페이지의 왼쪽 탐색 메뉴에서 **맵** 으로 이동합니다.

    ![맵으로 이동](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. 창의 왼쪽 탐색 영역에서 **필터** 를 선택합니다. 맵을 검색할 수 있는 **필터** 창이 표시됩니다.

    ![왼쪽 탐색 영역에서 필터 선택](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  드롭다운 메뉴에서 **형식**, **날짜** 및 **이름** 값을 선택합니다. 그런 다음, **적용** 을 선택하여 보려는 맵을 검색합니다. 작업이 만들어진 날짜는 type_farmname_YYYY-MM-DD 형식으로 표시됩니다.
4. 테이블 머리글 옆에 있는 **정렬** 아이콘을 선택하여 **팜**, **날짜**, **만든 날짜**, **작업 ID** 및 **작업 유형** 을 기준으로 정렬합니다.
5. 페이지 끝에 있는 탐색 단추를 사용하여 사용 가능한 맵 목록을 스크롤합니다.
6. 보려는 맵을 선택합니다. 팝업 창에 선택한 맵에 대한 미리 보기가 표시됩니다.
7. 드롭다운 메뉴에서 **다운로드** 를 선택하여 다운로드 형식을 선택합니다. 맵이 다운로드되어 지정된 폴더에 저장됩니다.

    ![토양 수분 열 지도 미리 보기](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
