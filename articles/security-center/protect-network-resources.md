---
title: Microsoft Defender for Cloud에서 네트워크 리소스 보호
description: 이 문서에서는 Azure 네트워크 리소스를 보호 하 고 보안 정책을 준수 하는 데 도움이 되는 Microsoft Defender for Cloud의 권장 사항을 다룹니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: memildin
ms.openlocfilehash: be9597fd1ee8b9231ec2c41b214e233dd92e1aa0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422295"
---
# <a name="protect-your-network-resources"></a>네트워크 리소스 보호

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud는 네트워크 보안 모범 사례에 대 한 Azure 리소스의 보안 상태를 지속적으로 분석 합니다. Defender for Cloud가 잠재적인 보안 취약점을 식별 하는 경우 리소스를 강화 하 고 보호 하는 데 필요한 컨트롤을 구성 하는 과정을 안내 하는 권장 사항을 만듭니다.

네트워킹에 대한 권장 사항의 전체 목록은 [네트워킹 권장 사항](recommendations-reference.md#recs-networking)을 참조하세요.

이 문서에서는 네트워크 보안 관점에서 Azure 리소스에 적용되는 권장 사항을 설명합니다. 네트워킹 권장 사항은 차세대 방화벽, 네트워크 보안 그룹, JIT VM 액세스, 허용 범위가 과도하게 큰 인바운드 트래픽 규칙 등에 초점을 맞춥니다. 네트워킹 권장 사항 및 수정 작업 목록은 [클라우드의 Microsoft Defender에서 보안 권장 사항 관리](review-security-recommendations.md)를 참조 하세요.

Defender for Cloud의 **네트워킹** 기능에는 다음이 포함 됩니다. 

- 네트워크 맵 (서버에 Microsoft Defender 필요)
- [적응 네트워크 강화](adaptive-network-hardening.md) (서버용 Microsoft Defender 필요)
- 네트워킹 보안 권장 사항
 
## <a name="view-your-networking-resources-and-their-recommendations"></a>네트워킹 리소스 및 권장 사항 보기

[자산 인벤토리 페이지](asset-inventory.md)에서 리소스 종류 필터를 사용하여 조사할 네트워킹 리소스를 선택합니다.

:::image type="content" source="./media/protect-network-resources/network-filters-inventory.png" alt-text="자산 인벤토리 네트워크 리소스 종류" lightbox="./media/protect-network-resources/network-filters-inventory.png":::


## <a name="network-map"></a>네트워크 맵

대화형 네트워크 맵은 네트워크 리소스를 강화하기 위한 권장 사항 및 정보를 제공하는 보안 오버레이를 통해 그래픽 뷰를 제공합니다. 맵을 사용하면 Azure 워크로드의 네트워크 토폴로지, 가상 머신과 서브넷 간 연결, 맵에서 특정 리소스 및 해당 리소스에 대한 권장 사항으로 드릴다운하는 기능을 확인할 수 있습니다.

네트워크 맵을 열려면:

1. 클라우드 용 Defender의 메뉴에서 **워크 로드 보호** 대시보드를 엽니다.

1. **네트워크 맵** 을 선택 합니다.

    :::image type="content" source="./media/protect-network-resources/opening-network-map.png" alt-text="워크 로드 보호에서 네트워크 맵을 엽니다." lightbox="./media/protect-network-resources/opening-network-map.png":::

1. **레이어** 메뉴를 선택하고 **토폴로지** 를 선택합니다.
 
토폴로지 맵의 기본 보기에 다음이 표시됩니다.

- 현재 선택한 구독-맵은 포털에서 선택한 구독에 대해 최적화 됩니다. 선택 항목을 수정 하면 새 선택 항목으로 맵이 다시 생성 됩니다.
- 리소스 관리자 리소스 종류 ("클래식" Azure 리소스는 지원 되지 않음)의 Vm, 서브넷 및 Vnet
- 피어링된 VNet
- [네트워크 권장 사항](review-security-recommendations.md)의 심각도가 높거나 보통인 리소스만
- 인터넷 연결 리소스

:::image type="content" source="./media/protect-network-resources/network-map-info.png" alt-text="클라우드 네트워킹 토폴로지 맵의 Defender 스크린샷" lightbox="./media/protect-network-resources/network-map-info.png":::

## <a name="understanding-the-network-map"></a>네트워크 맵 이해

네트워크 맵은 Azure 리소스를 **토폴로지** 보기 및 **트래픽** 보기로 표시할 수 있습니다. 

### <a name="the-topology-view"></a>토폴로지 보기

네트워킹 맵의 **토폴로지** 보기에서 네트워킹 리소스에 대한 다음과 같은 정보를 볼 수 있습니다.

- 내부 원에는 선택한 구독 내에 있는 모든 Vnet을 볼 수 있으며, 다음 원은 모든 서브넷이고, 외부 원은 모든 가상 머신입니다.
- 맵에서 리소스를 연결하는 선은 서로 연결된 리소스가 무엇인지, Azure 네트워크가 어떻게 구조화되었는지를 나타냅니다. 
- 심각도 지표를 사용 하 여 Defender for Cloud에서 오픈 권장 사항이 있는 리소스에 대 한 개요를 신속 하 게 파악할 수 있습니다.
- 리소스 중 하나를 클릭하여 드릴다운하고 해당 리소스 및 해당 권장 사항에 대한 세부 사항을 네트워크 맵의 컨텍스트에서 직접 볼 수 있습니다.  
- 지도에 너무 많은 리소스가 표시 되는 경우 Microsoft Defender for Cloud는 소유 알고리즘을 사용 하 여 리소스를 ' 스마트 클러스터 ' 하 고 가장 중요 한 상태에 있는 항목을 강조 표시 하 고 심각도가 가장 높은 권장 사항을 제공 합니다.

맵은 대화형이며 동적이므로 모든 노드를 클릭할 수 있고 필터에 따라 보기를 변경할 수 있습니다.

1. 맨 위에 있는 필터를 사용하면 네트워크 맵에서 표시되는 항목을 수정할 수 있습니다. 다음을 기준으로 맵에 집중할 수 있습니다.

   -  **보안 상태**: Azure 리소스의 심각도(높음, 중간, 낮음)에 따라 맵을 필터링할 수 있습니다.
   - **권장 사항**: 해당 리소스에서 활성화된 권장 사항을 기준으로 표시되는 리소스를 선택할 수 있습니다. 예를 들어 클라우드에 대해 Defender에서 네트워크 보안 그룹을 사용 하도록 권장 하는 리소스만 볼 수 있습니다.
   - **네트워크 영역**: 기본적으로 맵은 인터넷 연결 리소스만 표시하므로 내부 VM도 선택할 수 있습니다.
 
2. 언제든지 왼쪽 위 모서리에 있는 **재설정** 을 클릭하면 맵을 기본 상태로 돌릴 수 있습니다.

리소스로 드릴다운하려면:

1. 맵에서 특정 리소스를 선택하면 오른쪽 창이 열리고 리소스에 대한 일반 정보, 연결된 보안 솔루션(있는 경우) 및 리소스와 관련된 권장 사항을 제공합니다. 선택한 리소스의 각 형식에 대해 동일한 동작 형식입니다. 
2. 맵에서 노드 위로 마우스를 가져가면 구독, 리소스 형식 및 리소스 그룹을 포함한 리소스에 대한 일반 정보를 볼 수 있습니다.
3. 링크를 사용하여 도구 팁을 확대하고, 해당 특정 노드에서 맵에 다시 초점을 맞춥니다. 
4. 특정 노드에서 떨어져서 맵에 초점을 다시 맞추려면 축소합니다.

### <a name="the-traffic-view"></a>트래픽 보기

**트래픽** 보기는 리소스 간의 가능한 모든 트래픽의 맵을 제공합니다. 이렇게 하면 사용자와 통신할 수 있는 리소스를 정의하는 모든 사용자가 구성한 규칙의 시각적 맵이 제공됩니다. 그러면 신속하게 워크로드 내에서 위험이 있는 구성을 식별할 수 있을 뿐 아니라 네트워크 보안 그룹의 기존 구성도 볼 수 있습니다.

### <a name="uncover-unwanted-connections"></a>원치 않는 연결 파악

이 보기의 장점은 이러한 허용된 연결과 존재하는 취약성을 표시하는 기능입니다. 따라서 이 데이터의 교집합 영역을 사용하여 사용자 리소스에 필요한 강화 작업을 수행할 수 있습니다. 

예를 들어, 인식하지 못한 두 개의 머신이 통신할 수 있음을 감지하여 워크로드와 서브넷을 더 잘 격리할 수 있습니다.

### <a name="investigate-resources"></a>리소스 조사

리소스로 드릴다운하려면:

1. 맵에서 특정 리소스를 선택하면 오른쪽 창이 열리고 리소스에 대한 일반 정보, 연결된 보안 솔루션(있는 경우) 및 리소스와 관련된 권장 사항을 제공합니다. 선택한 리소스의 각 형식에 대해 동일한 동작 형식입니다. 
2. 리소스에서 가능한 아웃바운드 및 인바운드 트래픽의 목록을 보려면 **트래픽** 을 클릭합니다. 이 목록은 리소스와 통신할 수 있는 사용자 및 프로토콜과 포트를 통해 리소스가 통신할 수 있는 대상의 포괄적인 목록입니다. 예를 들어 VM을 선택하면 통신할 수 있는 모든 VM이 표시되고, 서브넷을 선택하면 통신할 수 있는 모든 서브넷이 표시됩니다.

**이 데이터는 크로스오버 및 상호 작용을 이해하기 위해 여러 규칙을 분석하는 고급 Machine Learning 알고리즘과 함께 네트워크 보안 그룹의 분석을 기반으로 합니다.** 

[![네트워킹 트래픽 맵.](./media/protect-network-resources/network-map-traffic.png)](./media/protect-network-resources/network-map-traffic.png#lightbox)


## <a name="next-steps"></a>다음 단계

다른 Azure 리소스 유형에 적용되는 권장 사항에 대해 자세히 알아보려면 다음을 참조하세요.

- [Microsoft Defender for Cloud에서 컴퓨터 및 응용 프로그램 보호](./asset-inventory.md)
