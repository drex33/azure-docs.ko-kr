---
title: 자습서 - Azure 리소스에 대한 메트릭 경고 만들기
description: Azure 메트릭 탐색기를 사용하여 메트릭 차트를 만드는 방법을 알아봅니다.
author: bwren
ms.author: bwren
ms.topic: tutorial
ms.date: 11/08/2021
ms.openlocfilehash: 431ef4418741a0406eeb4d350879dbc1be7ae737
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132350849"
---
# <a name="tutorial-create-a-metric-alert-for-an-azure-resource"></a>자습서: Azure 리소스에 대한 메트릭 경고 만들기
Azure Monitor 경고는 모니터링 데이터에서 중요한 조건이 발견될 때 사용자에게 사전에 알립니다. Azure 리소스의 메트릭 값이 임계값을 초과하는 경우 메트릭 경고 규칙에서 경고를 만듭니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 메트릭 탐색기에서 메트릭 경고 규칙 만들기
> * 경고 임계값 구성
> * 경고 세부 정보를 정의하는 작업 그룹 만들기

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 완료하려면 다음이 필요합니다. 

- 모니터링할 Azure 리소스입니다. 메트릭을 지원하는 Azure 구독의 모든 리소스를 사용할 수 있습니다. 리소스가 메트릭을 지원하는지 확인하려면 Azure Portal에서 리소스 메뉴로 이동한 후 해당 메뉴의 **모니터링** 섹션에 **메트릭** 옵션이 있는지 확인합니다.
- 경고하려는 하나 이상의 메트릭이 있는 메트릭 탐색기의 차트. [자습서: Azure 리소스에 대한 메트릭 분석](../essentials/tutorial-metrics.md)을 완료합니다.

## <a name="create-new-alert-rule"></a>새 경고 규칙 만들기
메트릭 탐색기에서 **새 경고 규칙** 을 클릭합니다. 규칙은 메트릭 탐색기에서 선택한 대상 개체 및 메트릭을 사용하여 미리 구성됩니다.

:::image type="content" source="media/tutorial-metric-alert/new-alert-rule.png" alt-text="새 경고 규칙" lightbox="media/tutorial-metric-alert/new-alert-rule.png":::

## <a name="configure-alert-logic"></a>경고 논리 구성
리소스가 이미 선택되어 있습니다. 경고 규칙에 대한 임계값 및 기타 세부 정보를 지정하도록 신호 논리를 수정해야 합니다. 

**조건 이름** 을 클릭하여 이러한 설정을 확인합니다. 

:::image type="content" source="./media/tutorial-metric-alert/configuration.png" lightbox="./media/tutorial-metric-alert/configuration.png" alt-text="경고 규칙 구성":::

차트에는 경고가 발생한 시기를 확인할 수 있도록 지남에 따라 선택된 신호의 값이 표시됩니다. 신호 논리를 지정하면 이 차트가 업데이트됩니다.

:::image type="content" source="./media/tutorial-metric-alert/signal-logic.png" lightbox="./media/tutorial-metric-alert/signal-logic.png" alt-text="경고 규칙 신호 논리":::

**경고 논리** 는 조건 및 평가 시간으로 정의됩니다. 이 조건이 true이면 경고가 발생합니다. 경고 규칙에 대한 **임계값** 을 제공하고, **연산자** 및 **집계 형식** 을 수정하여 필요한 논리를 정의합니다.

:::image type="content" source="./media/tutorial-metric-alert/alert-logic.png" lightbox="./media/tutorial-metric-alert/alert-logic.png" alt-text="경고 규칙 경고 논리":::

기본 시간 세분성을 그대로 적용하거나 요구 사항에 맞게 수정할 수 있습니다. **평가 빈도** 는 경고 논리가 평가되는 빈도를 정의합니다. **집계 세분성** 은 수집된 값이 집계되는 시간 간격을 정의합니다.

신호 논리 구성이 완료되면 **완료** 를 클릭합니다.

## <a name="configure-actions"></a>작업 구성
[!INCLUDE [Action groups](../../../includes/azure-monitor-tutorial-action-group.md)]

## <a name="configure-details"></a>세부 정보 구성
[!INCLUDE [Alert details](../../../includes/azure-monitor-tutorial-alert-details.md)]

:::image type="content" source="./media/tutorial-metric-alert/alert-details.png" lightbox="./media/tutorial-metric-alert/alert-details.png" alt-text="경고 규칙 세부 정보":::


**경고 규칙 만들기** 를 클릭하여 경고 규칙을 만듭니다.


## <a name="view-the-alert"></a>경고 보기
[!INCLUDE [View alert](../../../includes/azure-monitor-tutorial-view-alert.md)]


## <a name="next-steps"></a>다음 단계
이제 Azure 리소스에 대한 메트릭 경고를 만드는 방법을 알아보았으므로 다음 자습서 중 하나를 사용하여 로그 데이터를 수집합니다.

> [!div class="nextstepaction"]
> [Azure 리소스에서 리소스 로그 수집](../essentials/tutorial-resource-logs.md)
> [!div class="nextstepaction"]
> [Azure 가상 머신에서 게스트 로그 및 메트릭 수집](../vm/tutorial-monitor-vm-guest.md)