---
title: Azure Load Balancer에 대한 인사이트
description: 부하 분산 장치 인사이트를 사용하여 신속한 오류 지역화 및 합리적인 설계 결정 사항 실현
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: kumud
ms.openlocfilehash: 7f87862cd922451113403b382930379f63e471e4
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357898"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>인사이트를 사용하여 Azure Load Balancer 모니터링 및 구성

네트워크에 대한 Azure Monitor를 통해 Load Balancer에 대한 기능 종속성 시각화 및 미리 구성된 메트릭 대시보드를 제공합니다. 이러한 시각적 개체는 적절한 설계를 결정하고 오류를 신속하게 지역화, 진단, 해결하는 데 도움이 됩니다.

>[!NOTE] 
>이 기능은 프리뷰 상태이며, 기능 종속성 보기 및 미리 구성된 대시보드가 이 환경을 개선하기 위해 변경될 수 있습니다.

>[!IMPORTANT]
>표준 Load Balancer는 미리 구성된 메트릭 대시보드의 Load Balancer 네임스페이스에서 메트릭을 확인하는 데 필요합니다. 여전히 VM, 가상 머신 확장 집합 및 연결 모니터 네임스페이스의 메트릭을 볼 수 있지만, 모든 프로덕션 워크로드에서 강력한 Load Balancer 메트릭 집합을 활용하도록 하려면 [Standard로 업그레이드](./upgrade-basic-standard.md)하는 것이 좋습니다.

## <a name="functional-dependency-view"></a>기능적 종속성 보기

기능적 종속성 보기를 사용하면 가장 복잡한 부하 분산 장치 설정도 파악할 수 있습니다. 최신 Load Balancer 구성에 대한 시각적 피드백을 통해 구성을 염두에 두면서 업데이트를 수행할 수 있습니다.

Azure에서 Load Balancer 리소스의 인사이트 블레이드를 방문하여 이 보기에 액세스할 수 있습니다.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="기능적 종속성 보기에 대한 예시입니다. Load Balancer의 프런트 엔드가 구성된 규칙을 통해 백 엔드 풀 멤버에 연결하는 것을 볼 수 있습니다. 표준 Load Balancer의 경우 부하 분산 규칙에서 백 엔드 풀 인스턴스로의 선은 상태 프로브 상태에 따라 색으로 구분됩니다." border="true":::

표준 Load Balancer의 경우 백 엔드 풀 리소스는 트래픽을 처리할 수 있는 백 엔드 풀의 현재 가용성을 나타내는 상태 프로브의 상태를 사용하여 색으로 구분됩니다. 위의 토폴로지와 함께 상태 그래프가 표시되고 애플리케이션의 상태에 대한 스냅샷 보기를 제공합니다.

## <a name="metrics-dashboard"></a>메트릭 대시보드

Load Balancer의 인사이트 블레이드에서 더 자세한 메트릭을 선택하여 Load Balancer의 특정 측면과 관련된 메트릭 시각적 개체를 포함하는 미리 구성된 [Azure Monitor 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 볼 수 있습니다. 이 대시보드는 Load Balancer 상태와 관련 문서에 대한 링크를 페이지 맨 위에 표시합니다.

처음에는 개요 탭이 표시됩니다. Load Balancer의 특정 측면과 관련된 시각적 개체가 포함된 사용 가능한 탭으로 이동할 수 있습니다. 각 탭의 아래쪽에 있는 대시보드에서 각각에 대한 명시적 지침을 사용할 수 있습니다.

현재 사용할 수 있는 대시보드 탭은 다음과 같습니다.
* 개요
* 프런트 엔드 및 백 엔드 가용성
* 데이터 처리량
* 흐름 분포
* 연결 모니터
* 메트릭 정의 

### <a name="overview-tab"></a>개요 탭
개요 탭에는 Load Balancer에 연결된 각 프런트 엔드 IP의 전체 데이터 경로 가용성 및 상태 프로브의 상태를 포함하는 검색 가능한 그리드가 있습니다. 이러한 메트릭은 프런트 엔드 IP가 응답하고 백 엔드 풀의 컴퓨팅 인스턴스가 인바운드 연결에 개별적으로 반응하는지 여부를 나타냅니다.

또한 이 페이지에서 각 프런트 엔드 IP의 전체 데이터 처리량을 확인하여 예상되는 트래픽 수준을 생성하고 받을지 여부를 파악할 수 있습니다. 페이지 맨 아래에 있는 지침은 불규칙한 값이 표시되는 적절한 탭으로 안내합니다.

### <a name="frontend-and-backend-availability-tab"></a>프런트 엔드 및 백 엔드 가용성 탭
프런트 엔드 및 백 엔드 가용성 탭은 몇 가지 유용한 보기에서 제공되는 데이터 경로 처리량 및 상태 프로브 상태 메트릭을 보여 줍니다. 첫 번째 그래프에는 문제가 있는지 여부를 확인할 수 있도록 집계 값이 표시됩니다. 그래프의 나머지 부분에서는 인바운드 가용성 문제의 원인을 파악하고 해결할 수 있도록 이러한 메트릭을 다양한 차원으로 분할하는 방법을 보여 줍니다.

이러한 그래프를 보는 워크플로는 다양한 증상의 일반적인 원인과 함께 페이지 아래쪽에 제공됩니다. 

### <a name="data-throughput-tab"></a>데이터 처리량 탭
데이터 처리량 탭에서는 인바운드 및 아웃바운드 처리량을 검토하여 트래픽 패턴이 예상대로 작동하는지 확인할 수 있습니다. 실행 중인 서비스가 어떻게 개별적으로 수행하고 있는지 확인할 수 있도록 프런트 엔드 IP 및 프런트 엔드 포트로 분할된 인바운드 및 아웃바운드 데이터 처리량을 보여 줍니다.

### <a name="flow-distribution"></a>흐름 분포
흐름 분포 탭은 백 엔드 인스턴스가 받고 생성하는 흐름 수를 시각화하고 관리하는 데 도움이 됩니다. 인바운드 및 아웃바운드 트래픽에 대한 흐름 생성 비율 및 흐름 수와 각 VM 및 가상 머신 확장 집합 인스턴스가 수신하는 네트워크 트래픽을 보여 줍니다. 

이러한 보기는 Load Balancer 구성 또는 트래픽 패턴이 불균형 트래픽으로 이어지는지 여부에 대한 피드백을 제공합니다. 예를 들어 세션 선호도가 구성되어 있고 단일 클라이언트가 불균형 요청 수를 만드는 경우입니다. 또한 컴퓨터 크기에 대한 [VM당 흐름 제한](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations)에 근접하고 있는지 여부도 알 수 있습니다.

### <a name="connection-monitors"></a>연결 모니터
연결 모니터 탭에는 구성한 모든 [연결 모니터](../network-watcher/connection-monitor.md)의 글로벌 맵에 대한 왕복 대기 시간이 표시됩니다. 이러한 시각적 개체는 엄격한 대기 시간 요구 사항이 있는 서비스에 대한 유용한 정보를 제공합니다. 요구 사항을 충족하기 위해 추가 지역 배포를 더하거나 [지역 간 부하 분산](./cross-region-overview.md) 모델로 이동해야 할 수 있습니다.

### <a name="metric-definitions"></a>메트릭 정의
메트릭 정의 탭에는 [다차원 메트릭 문서](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics)에 표시된 모든 정보가 포함되어 있습니다.

## <a name="next-steps"></a>다음 단계
* 대시보드를 검토하고 개선할 수 있는 항목이 있는 경우 아래 링크를 사용하여 피드백을 제공합니다.
* [메트릭 문서를 검토하여 각 메트릭이 계산되는 방식을 이해했는지 확인합니다](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).
* [Load Balancer에 대한 연결 모니터를 만듭니다](../network-watcher/connection-monitor.md).
* [사용자 고유의 통합 문서를 만듭니다](../azure-monitor/visualize/workbooks-overview.md). 자세한 메트릭 대시보드에서 편집 단추를 클릭하여 이를 수행할 수 있습니다.
