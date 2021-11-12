---
title: 포트 및 VLAN 이름 확인 향상
description: 센서에서 포트 및 VLAN 이름을 사용자 지정하여 디바이스 확인을 보강합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 5c4f29f43534a797c5e8ac7142fd9633830f7859
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132331493"
---
# <a name="enhance-port-vlan-and-os-resolution"></a>포트, VLAN 및 OS 확인 향상

센서에서 포트 및 VLAN 이름을 사용자 지정하여 디바이스 확인을 보강할 수 있습니다.

## <a name="customize-port-names"></a>포트 이름 사용자 지정

IoT 용 Microsoft Defender는 DHCP 또는 HTTP와 같은 가장 일반적으로 예약 된 포트에 이름을 자동으로 할당 합니다. Defender for IoT가 검색하는 다른 포트의 포트 이름을 사용자 지정할 수 있습니다. 예를 들어 비정상적으로 높은 활동을 보이는 예약되지 않은 포트에 이름을 할당합니다.

이러한 이름이 표시되는 경우는 다음과 같습니다.

  - 디바이스 맵에서 **디바이스 그룹** 을 선택할 때

  - 포트 정보를 제공하는 위젯을 만들 때

### <a name="view-custom-port-names-in-the-device-map"></a>디바이스 맵에서 사용자 지정 포트 이름 보기

사용자가 정의한 이름이 포함된 포트는 디바이스 맵에서 **알려진 애플리케이션** 그룹에 나타납니다.

:::image type="content" source="media/how-to-enrich-asset-information/applications-v2.png" alt-text="알려진 애플리케이션 그룹을 표시하는 디바이스 맵의 스크린샷.":::

### <a name="view-custom-port-names-in-widgets"></a>위젯에서 사용자 지정 포트 이름 보기

정의한 포트 이름은 포트별로 트래픽을 커버하는 위젯에 표시됩니다.

:::image type="content" source="media/how-to-enrich-asset-information/traffic-v2.png" alt-text="트래픽 커버.":::

사용자 지정 포트 이름을 정의하려면:

1. **시스템 설정** 을 선택한 다음 **표준 별칭** 을 선택합니다.

2. **포트 별칭 추가** 를 선택합니다.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-aliases.png" alt-text="포트 별칭 추가.":::

3. 포트 번호를 입력하고, **TCP/UDP** 를 선택하거나 **둘 다** 선택하고 이름을 추가합니다.

4. **저장** 을 선택합니다.

## <a name="configure-vlan-names"></a>VLAN 이름 구성

디바이스 VLAN 번호 및 태그를 사용하여 디바이스 인벤토리 데이터를 보강할 수 있습니다. 데이터 보강 외에 VLAN당 디바이스 수를 보고, VLAN 위젯별 대역폭을 볼 수 있습니다.

VLAN 지원은 802.1q(최대 VLAN ID 4094)를 기반으로 합니다.

VLAN 정보를 검색하는 데 사용할 수 있는 두 가지 방법은 다음과 같습니다.

- **자동으로 검색됨**: 기본적으로 센서가 VLAN을 자동으로 검색합니다. 트래픽을 통해 검색된 VLAN은 VLAN 구성 화면, 데이터 마이닝 보고서 및 VLAN 정보를 포함하는 기타 보고서에 표시됩니다. 사용되지 않는 VLAN은 표시되지 않습니다. 이러한 VLAN은 편집하거나 삭제할 수 없습니다. 

  각 VLAN에 고유한 이름을 추가해야 합니다. 이름을 추가하지 않으면 VLAN이 보고되는 모든 위치에 VLAN 번호가 표시됩니다.

- **수동으로 추가**: VLAN을 수동으로 추가할 수 있습니다. 수동으로 추가한 각 VLAN의 고유한 이름을 추가해야 하며, 이러한 VLAN은 편집하거나 삭제할 수 있습니다.

VLAN 이름은 최대 50자의 ASCII 문자를 포함할 수 있습니다.

> [!NOTE]
> VLAN 이름은 센서와 관리 콘솔 간에 동기화되지 않습니다. 관리 콘솔에서도 이름을 정의해야 합니다.  
Cisco 스위치의 경우 SPAN 구성에 다음 줄을 추가합니다. `monitor session 1 destination interface XX/XX encapsulation dot1q`. 이 명령에서 *XX/XX* 는 포트의 이름 및 번호입니다.

VLAN 이름을 구성하려면:

1. 측면 메뉴에서 **시스템 설정** 을 선택합니다.

2. **시스템 설정** 창에서 **VLAN** 을 선택합니다.

    :::image type="content" source="media/how-to-enrich-asset-information/edit-vlan.png" alt-text="시스템 설정을 사용하여 VLAN을 편집합니다.":::

3. 각 VLAN ID 옆에 고유한 이름을 추가합니다.

## <a name="improve-device-operating-system-classification-data-enhancement"></a>디바이스 운영 체제 분류 향상: 데이터 향상

센서는 지속적으로 새 디바이스를 자동으로 검색하고 운영 체제 유형을 비롯하여 이전에 검색된 디바이스의 변경도 자동으로 검색합니다.

특정 상황에서는 검색된 운영 체제에서 충돌이 검색될 수 있습니다. 예를 들어 데스크톱이나 서버 시스템을 참조하는 운영 체제 버전이 있는 경우 이 문제가 발생할 수 있습니다. 이 경우 선택적인 운영 체제 분류가 포함된 알림을 받게 됩니다.

:::image type="content" source="media/how-to-enrich-asset-information/enhance-data-screen.png" alt-text="데이터를 향상시킵니다.":::

운영 체제 분류를 보강하기 위해 권장 사항을 조사합니다. 이 분류는 디바이스 인벤토리, 데이터 마이닝 보고서 및 기타 표시에 표시됩니다. 이 정보를 최신 상태로 유지하면 경고, 위협 및 위험 분석 보고서의 정확도를 향상시킬 수 있습니다.

운영 체제 권장 사항에 액세스하려면:

1. **시스템 설정** 을 선택합니다.
1. **데이터 향상** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

다양한 보고서에서 보강된 디바이스 정보 보기:

- [디바이스 인벤토리에서 센서 감지 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)
- [센서 추세 및 통계 보고서](how-to-create-trends-and-statistics-reports.md)
- [센서 데이터 마이닝 쿼리](how-to-create-data-mining-queries.md)
