---
title: 클라우드 장치 인벤토리를 사용 하 여 IoT 장치 관리
description: 장치 인벤토리를 사용 하 여 IoT 장치를 관리 하는 방법을 알아봅니다.
ms.date: 11/10/2021
ms.topic: how-to
ms.openlocfilehash: 89628a2d5d03be0f96b51470450be817bb26c719
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386886"
---
# <a name="manage-your-iot-devices-with-the-device-inventory"></a>장치 인벤토리를 사용 하 여 IoT 장치 관리

장치 인벤토리를 사용 하 여 장치 시스템 및 네트워크 정보를 볼 수 있습니다. 검색, 필터, 열 편집 및 내보내기 도구를 사용 하 여이 정보를 관리할 수 있습니다.

:::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-inventory-screenshot.png" alt-text="IoT의 장치 인벤토리 화면에 대 한 총 Defender 개요입니다.":::

장치 인벤토리의 이점에는 다음이 포함 됩니다.

- 모든 IOT 및 다른 입력의 장치를 식별 합니다. 예를 들어 사용자 환경에서 통신 하지 않는 장치를 이해 하 고 문제를 해결 해야 할 수 있습니다.

- 사이트, 유형 또는 공급 업체 별로 장치를 그룹화 하 고 필터링 합니다.

- 각 장치에 대 한 가시성을 확보 하 고 각각에 대해 서로 다른 위협 및 경고를 조사 합니다.

- 보고서에 대 한 전체 장치 인벤토리를 CSV 파일로 내보냅니다.

## <a name="device-inventory-overview"></a>장치 인벤토리 개요

장치 인벤토리는 환경 내의 모든 장치에 대 한 개요를 제공 합니다. 여기에서 각 장치 및 필터의 개별 세부 정보를 확인 하 고 다양 한 옵션으로 검색 순서를 지정할 수 있습니다.

다음 표에서는 장치 인벤토리에 있는 여러 장치 속성을 설명 합니다.

| 매개 변수 | Description | 기본값 |
|--|--|--|
| **데이터 원본** | 마이크로 에이전트, 이상 센서 및 Mde와 같은 데이터 원본입니다. | 마이크로 에이전트 |
| **디바이스 클래스** | 장치의 클래스입니다. | IoT |
| **디바이스 모델** | 장치의 모델입니다. | - |
| **디바이스 이름** | 센서가 검색한 디바이스 이름이거나 사용자가 입력한 디바이스 이름입니다. | - |
| **장치 하위 유형** | 스피커 및 스마트 tv와 같은 장치의 하위 형식입니다. | 관리 디바이스 |
| **디바이스 유형** | 통신, 산업 등의 장치 유형입니다. | 기타 |
| **처음 표시 됨** | 장치가 처음으로 표시 된 날짜 및 시간입니다. MM/DD/YYYY HH: MM: SS AM/PM 형식으로 표시 됩니다. | - |
| **IP 주소** | 디바이스의 IP 주소입니다. | - |
| **마지막 작업** | 장치에서 클라우드로 마지막으로 이벤트를 보낸 날짜 및 시간입니다. MM/DD/YYYY HH: MM: SS AM/PM 형식으로 표시 됩니다. | - |
| **마지막 업데이트 시간** | 장치가 시스템 정보 이벤트를 클라우드로 마지막으로 전송한 날짜 및 시간입니다. MM/DD/YYYY HH: MM: SS AM/PM 형식으로 표시 됩니다. | - |
| **MAC 주소** | 디바이스의 Mac 주소입니다. | - |
| **OS 아키텍처** | 운영 체제의 아키텍처입니다. | - |
| **OS 배포** | Android, Linux 및 Haiku와 같은 운영 체제의 배포입니다. | - |
| **OS 플랫폼** | 디바이스의 OS입니다(감지된 경우). | - |
| **OS 버전** | Windows 10 및 Ubuntu 20.04.1와 같은 운영 체제의 버전입니다. | - |
| **사이트** | 이 디바이스가 포함된 사이트입니다. | - |
| **공급업체** | MAC 주소에 정의된 디바이스 공급업체의 이름입니다. | - |

**장치 인벤토리를 보려면 다음을 수행 합니다**.

1. [Azure Portal](https://ms.portal.azure.com)을 엽니다.

1. IoT 장치 **인벤토리에 대 한 Defender** 로 이동  >  합니다.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-inventory.png" alt-text="IoT 용 Defender의 왼쪽 메뉴에서 장치 인벤토리를 선택 합니다." lightbox="media/how-to-manage-device-inventory-on-the-cloud/device-inventory.png":::

## <a name="customize-the-device-inventory-table"></a>장치 인벤토리 테이블 사용자 지정

장치 인벤토리 테이블에서 열을 추가 하거나 제거할 수 있습니다. 필드를 끌어서 놓아 열 순서를 변경할 수도 있습니다.

**장치 인벤토리 테이블을 사용자 지정 하려면**:

1. :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/edit-columns-icon.png" border="false"::: 단추를 선택합니다.

1. 열 편집 탭에서 드롭다운 메뉴를 선택 하 여 열 값을 변경 합니다.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-drop-down-menu.png" alt-text="드롭다운 메뉴를 선택 하 여 지정 된 열의 값을 변경 합니다.":::

1. 단추를 선택 하 여 열을 추가 :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/add-column-icon.png" border="false"::: 합니다.

1. 열 매개 변수를 새 위치로 끌어 열을 다시 정렬 합니다.

1. 단추를 선택 하 여 열을 삭제 :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/trashcan-icon.png" border="false"::: 합니다.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/delete-a-column.png" alt-text="휴지통 아이콘을 선택 하 여 열을 삭제 합니다.":::

1. **저장** 을 선택 하 여 변경한 내용을 저장 합니다.

장치 인벤토리를 기본 설정으로 다시 설정 하려면 열 편집 창에서 단추를 선택 :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/reset-icon.png" border="false"::: 합니다.

## <a name="filter-the-device-inventory"></a>디바이스 인벤토리 필터링

장치 인벤토리를 검색 하 고 필터링 하 여 테이블에 표시 되는 정보를 정의할 수 있습니다.

장치 인벤토리 테이블에 적용할 수 있는 필터 목록은 [장치 인벤토리 개요](#device-inventory-overview)를 참조 하세요.

**장치 인벤토리를 필터링 하려면**:

1. **필터 추가** 선택

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/add-filter.png" alt-text="필터 추가 단추를 선택 하 여 장치 인벤토리에 표시할 항목을 지정 합니다.":::

1. 필터 추가 창에서 열 드롭다운 메뉴를 선택 하 여 필터링 할 열을 선택 합니다.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/add-filter-window.png" alt-text="장치 인벤토리에 필터링 할 열을 선택 합니다.":::

1. 필터링 기준으로 필터 필드에 값을 입력 합니다.

1. **적용 단추** 를 선택 합니다.

한 번에 여러 필터를 적용할 수 있습니다. 장치 인벤토리 페이지를 벗어나면 필터가 저장 되지 않습니다.

## <a name="view-device-information"></a>디바이스 정보 보기

특정 장치 정보를 보려면 장치를 선택 하 고 장치 정보 창을 표시 합니다.

:::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/device-information-window.png" alt-text="장치를 선택 하 여 해당 장치의 모든 정보를 확인 합니다." lightbox="media/how-to-manage-device-inventory-on-the-cloud/device-information-window.png":::

## <a name="export-the-device-inventory-to-csv"></a>장치 인벤토리를 CSV로 내보내기

장치 인벤토리를 CSV 파일로 내보낼 수 있습니다. 테이블을 내보낼 때 장치 인벤토리 테이블에 적용 하는 모든 필터를 내보냅니다.

단추를 선택 :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/export-button.png" border="false"::: 하 여 현재 장치 인벤토리를 CSV 파일로 내보냅니다.

## <a name="how-to-identify-devices-that-have-not-recently-communicated-with-the-azure-cloud"></a>최근 Azure 클라우드와 통신 하지 않은 장치를 확인 하는 방법

특정 장치에서 적극적으로 통신 하지 않는 경우를 확인 하 고 지정 된 기간 동안 전달 되지 않은 장치를 확인할 수 있습니다.

**최근 통신 하지 않은 모든 장치를 확인 하려면**:

1. [Azure Portal](https://ms.portal.azure.com)을 엽니다.

1. IoT 장치 **인벤토리에 대 한 Defender** 로 이동  >  합니다.

1. :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/edit-columns-icon.png" border="false"::: 단추를 선택합니다.

1. 단추를 선택 하 여 열을 추가 :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/add-column-icon.png" border="false"::: 합니다.

1. **마지막 작업** 을 선택 합니다.

1. **저장** 을 선택합니다.

1. 주 장치 인벤토리 페이지에서 **마지막 작업** 을 선택 하 여 마지막 작업 별로 페이지를 정렬 합니다.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/last-activity.png" alt-text="마지막 활동으로 구성 된 장치 인벤토리의 스크린샷."  lightbox="media/how-to-manage-device-inventory-on-the-cloud/last-activity.png":::

1. :::image type="icon" source="media/how-to-manage-device-inventory-on-the-cloud/add-filter-icon.png" border="false":::마지막 활동 열에 필터를 추가 하려면를 선택 합니다.

    :::image type="content" source="media/how-to-manage-device-inventory-on-the-cloud/last-activity-filter.png" alt-text="마지막 활동을 표시 하는 기간을 선택할 수 있는 필터 추가 화면의 스크린샷":::

1. 기간 또는 사용자 지정 날짜 범위를 입력 하 고 **적용** 을 선택 합니다.

## <a name="see-next"></a>다음 참조

- [조직을 위한 Microsoft Defender for IoT 시작](overview.md)

