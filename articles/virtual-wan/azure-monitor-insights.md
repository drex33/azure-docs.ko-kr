---
title: Azure Monitor Insights를 사용하여 Virtual WAN 모니터링
description: Azure Monitor Insights를 사용하여 Azure Virtual WAN을 모니터링하는 방법에 대해 알아봅니다.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 06/22/2021
ms.author: cherylmc
ms.openlocfilehash: 73209f93e2b7fd96dc6ebb540d62624249db5be8
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112539235"
---
# <a name="azure-monitor-insights-for-virtual-wan"></a>Virtual WAN용 Azure Monitor Insights

Azure Virtual WAN에 대한 [Azure Monitor 인사이트](../azure-monitor/insights/network-insights-overview.md)를 통해 사용자 및 운영자는 자동 검색된 토폴로지 맵을 통해 표시되는 가상 WAN의 상태를 볼 수 있습니다. 맵의 리소스 상태 및 상태 오버레이를 통해 가상 WAN의 전체적인 상태에 대한 스냅샷 보기가 제공됩니다. Virtual WAN 포털의 리소스 구성 페이지에 대한 원클릭 액세스를 통해 맵에서 리소스를 탐색할 수 있습니다.

Virtual WAN 리소스 수준 메트릭은 미리 패키징된 Virtual WAN 메트릭 통합 문서를 통해 수집 및 표시됩니다. 통합 문서에는 가상 WAN, 허브, 게이트웨이 및 연결 수준의 메트릭이 표시됩니다. 이 문서에서는 Virtual WAN에 대한 Azure Monitor 인사이트를 사용하여 Virtual WAN 토폴로지와 메트릭을 단일 위치에서 보는 단계를 안내합니다.

> [!NOTE]
> **인사이트** 메뉴 옵션은 **모니터링** 내 Virtual WAN 포털에 있습니다. 네트워크용 Azure Monitor를 사용하여 Virtual WAN 토폴로지 및 메트릭 통합 문서에 액세스할 수도 있습니다. 자세한 내용은 [네트워크용 Azure Monitor](../azure-monitor/insights/network-insights-overview.md)를 참조하세요. 
>

## <a name="before-you-begin"></a>시작하기 전에

이 문서의 단계를 완료하려면 하나 이상의 허브를 사용하는 가상 WAN이 있어야 합니다. 새 가상 WAN과 허브를 만들려면 이 문서의 다음 단계를 따르세요.

* [Virtual WAN 만들기](virtual-wan-site-to-site-portal.md#openvwan)
* [허브 만들기](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>VWAN 토폴로지 보기

**Azure Portal** > **Virtual WAN** 으로 이동합니다. 왼쪽 창의 **모니터** 메뉴에서 **인사이트(미리 보기)** 를 선택합니다. **인사이트** 보기가 표시됩니다. Virtual WAN 종속성 맵과 대략적인 **메트릭** 미니 통합 문서가 표시됩니다.

**그림 1: 모니터 > 인사이트 메뉴**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="인사이트(미리 보기) 보기를 보여주는 스크린샷." lightbox="./media/azure-monitor-insights/monitor-menu.png":::

**인사이트** 보기에서 자동 검색된 Virtual WAN 리소스를 볼 수 있습니다. 이러한 리소스로는 허브, 게이트웨이, 방화벽, 연결 및 스포크 가상 네트워크, 타사 NVA 및 엔드투엔드 Virtual WAN의 분기가 있습니다. 예제는 **그림 2** 를 참조하세요.

리소스 상태가 맵에서 색으로 구분되고 리소스 아이콘에 중첩됩니다. 허브 용량 및 게이트웨이 사용량과 같은 대략적인 Virtual WAN 메트릭은 미니 통합 문서의 창 오른쪽에 표시됩니다.

**그림 2: 인사이트 보기**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="인사이트 보기를 보여주는 스크린샷." lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>종속성 보기

Virtual WAN에 대한 **종속성** 보기는 허브 및 스포크 아키텍처로 광범위하게 구성된 모든 Virtual WAN 리소스의 모든 상호 연결된 보기를 시각화하는 데 도움이 됩니다.

**그림 3: VWAN 종속성 보기**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="종속성 보기를 보여주는 스크린샷." lightbox="./media/azure-monitor-insights/dependency-map.png":::

**종속성** 보기 맵은 연결된 그래프로 다음 리소스를 표시합니다.

* 다양한 Azure 지역에서의 Virtual WAN 허브.
* 허브에 직접 연결된 스포크 가상 네트워크.
* VPN 및 Azure ExpressRoute 분기 사이트와 ExpressRoute, S2S, P2S 연결 및 가상 네트워크 게이트웨이를 통해 각 허브에 연결된 P2S 사용자.
* 허브(보안 허브)에 배포된 Azure 방화벽(타사 방화벽 프록시 포함).
* 스포크 가상 네트워크에 배포된 타사 NVA(네트워크 가상 어플라이언스).

또한 종속성 맵은 간접적으로 연결된 가상 네트워크(Virtual WAN 스포크 가상 네트워크와 피어링되는 가상 네트워크)를 표시합니다.

종속성 맵을 통해 각 리소스의 구성 설정을 손쉽게 탐색할 수 있습니다. 예를 들어 허브 리소스를 마우스로 가리키면 허브 지역 및 허브 접두사와 같은 기본 리소스 구성을 볼 수 있습니다. 마우스 오른쪽 단추를 클릭하여 허브 리소스의 Azure Portal 페이지에 액세스합니다.

**그림 4: 리소스 관련 정보 탐색**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="리소스 관련 정보를 탐색하는 방법을 보여주는 스크린샷.":::

**종속성** 보기의 검색 및 필터 표시줄은 그래프를 통해 검색하는 쉽게 검색할 수 있는 방법을 제공합니다. 다양한 필터를 통해 특정 경로 및 상태로 검색 범위를 좁힐 수 있습니다.

**그림 5: 검색 및 필터링**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="검색 및 필터 표시줄을 보여주는 스크린샷." lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>상세 메트릭

**상세 메트릭 보기** 를 선택하여 상세 **메트릭** 페이지에 액세스할 수 있습니다. **메트릭** 페이지는 별도의 탭을 사용하여 미리 구성된 대시보드입니다. 이러한 탭은 가상 WAN 수준과 허브 수준, 개별 연결 수준에서 가상 WAN 리소스 용량, 성능 및 사용률에 대한 인사이트를 제공합니다.

**그림 6: 상세 메트릭 대시보드**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="상세 메트릭 대시보드를 보여주는 스크린샷." lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [Azure Monitor의 메트릭](../azure-monitor/essentials/data-platform-metrics.md)을 참조하세요.
* 모든 Virtual WAN 메트릭에 대한 전체 설명은 [Virtual WAN 모니터링](monitor-virtual-wan.md)을 참조하세요.
