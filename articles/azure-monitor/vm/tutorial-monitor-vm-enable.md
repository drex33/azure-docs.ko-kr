---
title: 자습서 - Azure 가상 머신에 대한 모니터링 사용
description: Azure Monitor VM 인사이트를 사용하여 모니터링을 사용하도록 설정하여 Azure 가상 머신을 모니터링합니다.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: 4156685d707a6e4fd6319a7130750838434f1efa
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352732"
---
# <a name="tutorial-enable-monitoring-for-azure-virtual-machine"></a>자습서: Azure 가상 머신에 대한 모니터링 사용
Azure 가상 머신의 상태 및 성능을 모니터링하려면 에이전트를 설치하여 게스트 운영 체제에서 데이터를 수집해야 합니다. VM 인사이트는 Azure 가상 머신에서 실행되는 게스트 운영 체제 및 워크로드를 모니터링하기 위한 Azure Monitor 기능입니다. Azure 가상 머신에 대한 모니터링을 사용하도록 설정하면 필요한 에이전트가 설치되고 게스트 운영 체제에서 성능, 프로세스 및 종속성 정보 수집이 시작됩니다. 

> [!NOTE]
> Azure Monitor 완전히 새로운 경우 자습서: Azure Monitor 사용하여 [Azure 리소스 모니터링으로](../essentials/monitor-azure-resource.md)시작해야 합니다. Azure 가상 머신은 플랫폼 메트릭 및 활동 로그와 같은 다른 Azure 리소스와 유사한 모니터링 데이터를 생성합니다. 이 자습서에서는 가상 머신에 고유한 추가 모니터링을 사용하도록 설정하는 방법을 설명합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 머신에서 성능 및 로그 데이터를 수집하는 Log Analytics 작업 영역을 만듭니다.
> * 필요한 에이전트를 설치하고 데이터 수집을 시작하는 가상 머신에 대해 VM 인사이트를 사용하도록 설정합니다. 
> * 가상 머신에서 수집된 성능 데이터를 분석하는 그래프를 검사합니다. 
> * 가상 머신에서 실행 중인 프로세스 및 다른 시스템과의 의존도를 보여주는 맵을 검사합니다.


> [!NOTE]
> VM 인사이트는 가상 머신의 게스트 운영 체제에서 성능 데이터를 수집하는 Log Analytics 에이전트를 설치합니다. 게스트 운영 체제에서 로그를 수집하지 않고 성능 데이터를 Azure Monitor 메트릭으로 보내지 않습니다. 이 기능은 [자습서: Azure 가상 머신에서 게스트 로그 및 메트릭 수집을 참조하세요.](tutorial-monitor-vm-guest.md)

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 완료하려면 다음이 필요합니다. 

- 모니터링할 Azure 가상 머신입니다.



## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기
[!INCLUDE [Create workspace](../../../includes/azure-monitor-tutorial-workspace.md)]


## <a name="enable-monitoring"></a>모니터링 사용
**Azure Portal** 가상 머신의 메뉴에서 Insights 선택합니다. VM 인사이트가 아직 사용하도록 설정되지 않은 경우 모니터링을 사용하도록 설정할 수 있는 다음과 유사한 화면이 표시됩니다. **사용** 을 클릭합니다.

> [!NOTE]
> 가상 머신을 만들 때 **자세한 모니터링 사용** 옵션을 선택한 경우 VM 인사이트가 이미 활성화되어 있을 수 있습니다. 작업 영역을 선택하고 **사용을** 다시 클릭합니다. VM 인사이트를 통해 수집된 데이터가 전송되는 작업 영역입니다.

:::image type="content" source="media/tutorial-monitor-vm/enable-vminsights-02.png" lightbox="media/tutorial-monitor-vm/enable-vminsights-02.png" alt-text="작업 영역에서 VM 인사이트 사용":::

모니터링을 사용하도록 설정 중이라는 메시지가 표시됩니다. 에이전트를 설치하고 데이터 수집을 시작하는 데 몇 분 정도 걸릴 수 있습니다. 

> [!NOTE]
> VM 인사이트에 사용할 수 있는 업그레이드에 대한 메시지가 나타날 수 있습니다. 그렇다면 계속하기 전에 업그레이드를 수행하는 옵션을 선택합니다.

## <a name="view-performance"></a>성능 보기
배포가 완료되면 컴퓨터에 대한 **성능** 데이터가 있는 VM 인사이트의 성능 탭에 보기가 표시됩니다. 시간이 지남에 따라 주요 게스트 메트릭의 값이 표시됩니다. 

:::image type="content" source="media/tutorial-monitor-vm/performance.png" lightbox="media/tutorial-monitor-vm/performance.png" alt-text="VM 인사이트 성능 보기":::

## <a name="view-processes-and-dependencies"></a>프로세스 및 의존성 보기
**지도** 탭을 선택하여 가상 머신에 대한 프로세스 및 의존도를 확인합니다. 현재 컴퓨터가 보기의 가운데에 있습니다. 프로세스 를 확장하여 실행 중인 **프로세스를 봅니다.**

:::image type="content" source="media/tutorial-monitor-vm/map-processes.png" lightbox="media/tutorial-monitor-vm/map-processes.png" alt-text="프로세스를 통해 VM 인사이트 맵 보기":::


## <a name="view-machine-details"></a>머신 세부 정보 보기
**지도** 보기는 가상 머신에 대해 수집된 정보가 있는 다양한 탭을 제공합니다. 탭을 클릭하여 사용 가능한 항목을 확인합니다.

:::image type="content" source="media/tutorial-monitor-vm/map-details.png" lightbox="media/tutorial-monitor-vm/map-details.png" alt-text="머신 세부 정보가 있는 VM 인사이트 맵 보기":::

## <a name="next-steps"></a>다음 단계
이제 가상 머신에서 데이터를 수집하기 때문에 해당 데이터를 사용하여 문제가 검색될 때 사전에 알리는 경고를 만들 수 있습니다.

> [!div class="nextstepaction"]
> [Azure 가상 머신을 사용할 수 없는 경우 경고 만들기](tutorial-monitor-vm-alert.md)

