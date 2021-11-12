---
title: 특정 영역의 디바이스에 대해 알아봅니다
description: 온-프레미스 관리 콘솔을 사용하여 특정 영역별 포괄적인 보기 정보 가져오기
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/09/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: 511c3b5f66b84490380507c5106775cd6cc6b255
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132318886"
---
# <a name="view-information-per-zone"></a>영역별 정보 보기


## <a name="view-a-device-map-for-a-zone"></a>영역에 대한 디바이스 맵 보기

센서에서 선택한 영역에 대한 디바이스 맵을 봅니다. 이 보기에는 선택한 영역과 관련된 모든 네트워크 요소(센서, 연결된 디바이스 및 기타 정보 포함)가 표시됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/zone-map-screenshot.png" alt-text="영역 맵의 스크린샷.":::


- **사이트 관리** 창의 영역 이름을 포함하는 표시줄에서 **영역 맵 보기** 를 선택합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-region-to-default-business-unit-v2.png" alt-text="기본 사업부의 기본 지역.":::

**디바이스 맵** 창이 표시됩니다. 맵에서 디바이스 및 디바이스 정보를 보는 데 사용할 수 있는 도구는 다음과 같습니다. 이러한 각 기능에 대한 자세한 내용은 *Defender for IoT 플랫폼 사용자 가이드* 를 참조하세요.

- **맵 확대/축소 보기**: 간소화된 보기, 연결 보기 및 자세히 보기입니다. 표시된 맵 보기는 맵의 확대/축소 수준에 따라 달라집니다. 확대/축소 수준을 조정하여 맵 보기 간에 전환합니다.

  :::image type="icon" source="media/how-to-work-with-asset-inventory-information/zoom-icon.png" border="false":::

- **맵 검색 및 레이아웃 도구**: 다양한 네트워크 세그먼트, 디바이스, 디바이스 그룹 또는 레이어를 표시하는 데 사용되는 도구입니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/search-and-layout-tools.png" alt-text="검색 및 레이아웃 도구 보기의 스크린샷.":::

- **디바이스에 대한 레이블 및 표시기:** 예를 들면 IT 네트워크의 서브넷에 그룹화된 디바이스의 수가 있습니다. 이 예시에서는 8개입니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/labels-and-indicators.png" alt-text="레이블 및 표시기의 스크린샷.":::

- **디바이스 속성 보기**: 예를 들어 디바이스를 모니터링하는 센서 및 기본 디바이스 속성입니다. 디바이스를 마우스 오른쪽 단추로 클릭하여 디바이스 속성을 봅니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-properties-v2.png" alt-text="디바이스 속성 보기의 스크린샷.":::

- **디바이스와 연결된 경고:** 디바이스를 마우스 오른쪽 단추로 클릭하여 관련 경고를 확인합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/show-alerts.png" alt-text="경고 보기 표시의 스크린샷.":::

## <a name="view-alerts-associated-with-a-zone"></a>영역과 연결된 경고 보기

특정 영역과 연결된 경고를 보려면 다음을 수행합니다.

- 경고 아이콘을 **영역** 창에서 선택합니다. 

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/business-unit-view-v2.png" alt-text="예제가 포함된 기본 사업부 보기입니다.":::

자세한 내용은 [개요: 경고 사용](how-to-work-with-alerts-on-premises-management-console.md)을 참조하세요.

### <a name="view-the-device-inventory-of-a-zone"></a>영역의 디바이스 인벤토리 보기

특정 영역과 연결된 디바이스 인벤토리를 보려면 다음을 수행합니다.

- **디바이스 인벤토리 보기** 를 **영역** 창에서 선택합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/default-business-unit.png" alt-text="디바이스 인벤토리 화면이 표시됩니다.":::

자세한 정보는 [디바이스 인벤토리에서 모든 엔터프라이즈 센서 검색 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)를 참조하세요.

## <a name="view-additional-zone-information"></a>추가 영역 정보 보기

다음과 같은 추가 영역 정보를 사용할 수 있습니다.

- **영역 세부 정보**: 영역에 연결된 디바이스, 경고 및 센서의 수를 확인합니다.

- **센서 세부 정보**: 영역에 할당된 각 센서의 이름, IP 주소 및 버전을 표시합니다.

- **연결 상태**: 센서의 연결이 끊어지면 센서에서 연결합니다. [온-프레미스 관리 콘솔에 센서 연결](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console)을 참조하세요. 

- **업데이트 진행률**: 연결된 센서를 업그레이드하는 경우 업그레이드 상태가 표시됩니다. 업그레이드하는 동안 온-프레미스 관리 콘솔은 센서에서 디바이스 정보를 받지 않습니다.

## <a name="next-steps"></a>다음 단계

[전역, 지역 및 로컬 위협에 대한 인사이트 확보](how-to-gain-insight-into-global-regional-and-local-threats.md)
