---
title: 자습서 - Azure 가상이 다운된 경우 경고
description: 가상 머신을 사용할 수 없는 경우 사전에 알리기 위해 Azure Monitor 경고 규칙을 만듭니다.
ms.service: azure-monitor
ms.topic: article
ms.custom: subject-monitoring
ms.date: 11/04/2021
ms.openlocfilehash: af60844e6aced51989cbee07a787deba01a379f9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352790"
---
# <a name="tutorial-create-alert-when-azure-virtual-machine-is-unavailable"></a>자습서: Azure 가상 머신을 사용할 수 없는 경우 경고 만들기
가상 머신에 대한 가장 일반적인 경고 조건 중 하나는 가상 머신이 실행 중인지 여부입니다. 가상 머신에 대한 Azure Monitor VM 인사이트를 사용하여 모니터링을 사용하도록 설정하면 하트비트는 1분마다 Azure Monitor 전송됩니다. 하트비트 검색되지 않은 경우 경고를 보내는 로그 쿼리 경고 규칙을 만들 수 있습니다. 이 메서드는 가상 머신이 실행되고 있지 않은 경우 경고할 뿐만 아니라 응답하지 않는 경우에도 경고합니다.


이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * 가상 머신에 대한 Azure Monitor VM 인사이트에서 수집한 로그 데이터를 봅니다.
> * 가상 머신을 사용할 수 없는 경우 사전에 알리는 경고 규칙을 로그 데이터에서 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소
이 자습서를 완료하려면 다음이 필요합니다. 

- 모니터링할 Azure 가상 머신입니다.
- 가상 머신에 대해 사용하도록 설정된 VM 인사이트를 사용하여 모니터링합니다. [자습서: Azure 가상 머신에 대한 모니터링 사용을](tutorial-monitor-vm-enable.md)참조하세요.



## <a name="create-a-heartbeat-query"></a>하트비트 쿼리 만들기
로그 쿼리 경고 규칙을 만드는 방법에는 여러 가지가 있습니다. 이 자습서에서는 **맵** 보기의 로그 이벤트 탭에서 **시작합니다.** 그러면 가상 머신에 대해 수집된 로그 데이터의 요약이 제공됩니다. 

:::image type="content" source="media/tutorial-monitor-vm/map-logs.png" lightbox="media/tutorial-monitor-vm/map-logs.png" alt-text="로그 이벤트 탭이 있는 지도 보기":::

**하트비트** 를 클릭합니다. 그러면 하트비트 이벤트에 대한 간단한 쿼리를 사용하여 가상 머신에서 수집된 로그 데이터를 분석하는 기본 도구인 Log Analytics가 열립니다. **TimeGenerated를** 클릭하여 해당 열을 기준으로 정렬하면 1분마다 하트비트 생성을 확인할 수 있습니다.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat.png" alt-text="하트비트 이벤트를 통해 쿼리 경고를 기록합니다.":::


경고의 경우 지난 5분 동안의 하트비트 레코드만 반환하려고 합니다. 레코드가 반환되지 않으면 가상 머신이 다운된 것으로 가정할 수 있습니다.

쿼리에 줄을 추가하여 지난 5분 동안 생성된 레코드로만 결과를 필터링합니다. 현재 시간에서 특정 시간 범위를 빼는 [ago 함수를](/azure/data-explorer/kusto/query/agofunction) 사용합니다.

```
Heartbeat
| where Computer == 'computer-name'
| where TimeGenerated > ago(5m)
```

**실행을** 클릭하여 이 쿼리의 결과를 확인합니다. 이제 지난 5분 동안의 하트비트만 포함됩니다.

:::image type="content" source="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" lightbox="media/tutorial-monitor-vm/log-query-heartbeat-time-filter.png" alt-text="시간 필터를 사용하여 하트비트 이벤트를 사용하여 쿼리 경고를 기록합니다.":::

## <a name="create-alert-rule"></a>경고 규칙 만들기
이제 로그 쿼리가 생겼으므로 해당 쿼리가 레코드를 반환하지 않을 때 경고를 보내는 경고 규칙을 만들 수 있습니다. 지난 5분 동안 하트비트 레코드가 반환되지 않으면 해당 시간에 머신이 응답하지 않았다고 가정할 수 있습니다. 

**새 경고 규칙을** 클릭하여 현재 쿼리에서 규칙을 만듭니다.

:::image type="content" source="media/tutorial-monitor-vm/new-alert-rule.png" lightbox="media/tutorial-monitor-vm/new-alert-rule.png" alt-text="새 경고 규칙.":::


경고 규칙에는 이미 **로그 쿼리가** 채워져 있습니다. 쿼리에서 반환된 테이블 행 수를 계산하려고 하기 때문에 **측정값도** 이미 올바름입니다. 행 수가 0이면 경고를 만들려고 합니다.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-01.png" lightbox="media/tutorial-monitor-vm/alert-rule-01.png" alt-text="경고 규칙 조건 1.":::

**경고 논리로** Scroll down **연산자를** **같음으로** 변경하고 **임계값 0을** 제공합니다.  즉, 레코드가 반환되지 않거나 쿼리의 레코드 수가 0인 경우 경고를 만들려고 합니다.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-02.png" lightbox="media/tutorial-monitor-vm/alert-rule-02.png" alt-text="경고 규칙 조건 2.":::

## <a name="configure-action-group"></a>작업 그룹 구성
**작업** 페이지에서 경고 규칙에 하나 이상의 [작업 그룹을](../alerts/action-groups.md) 추가할 수 있습니다. 작업 그룹은 이메일 또는 SMS 메시지 보내기와 같이 경고가 발생하면 수행할 작업 집합을 정의합니다.

작업 그룹이 이미 있는 경우 **작업 그룹 추가를** 클릭하여 경고 규칙에 기존 그룹을 추가합니다.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-actions.png" lightbox="media/tutorial-monitor-vm/alert-rule-actions.png" alt-text="경고 규칙 작업 그룹을 추가합니다.":::

구독에 선택할 작업 그룹이 없는 경우 **작업 그룹 만들기를** 클릭하여 새 작업 그룹을 만듭니다. 작업 그룹에 대한 **구독** 및 **리소스 그룹을** 선택하고 포털에 표시할 작업 **그룹 이름과** 메일 및 SMS 알림에 표시 **이름을** 지정합니다.

:::image type="content" source="media/tutorial-monitor-vm/action-group-basics.png" lightbox="./media/tutorial-monitor-vm/action-group-basics.png" alt-text="작업 그룹 기본 사항":::

**알림을** 선택하고 하나 이상의 방법을 추가하여 경고가 발생하면 적절한 사람에게 알립니다.

:::image type="content" source="media/tutorial-monitor-vm/action-group-notifications.png" lightbox="./media/tutorial-monitor-vm/action-group-notifications.png" alt-text="작업 그룹 알림":::



## <a name="configure-details"></a>세부 정보 구성
**세부 정보** 페이지에서 경고 규칙에 대한 다른 설정을 구성할 수 있습니다.

- 경고 규칙을 저장할 **구독** 및 **리소스 그룹입니다.** 모니터링하는 리소스와 동일한 리소스 그룹에 있을 필요는 없습니다.
- **경고의 심각도입니다.** 심각도를 사용하면 상대적 중요도가 비슷한 경고를 그룹화할 수 있습니다. **오류의** 심각도는 응답하지 않는 가상 머신에 적합합니다.
- 확인란을 선택한 상태로 유지하여 **생성 시 경고 사용 을** 선택합니다.
- **경고를 자동으로 해결하려면 확인란을 선택합니다.** 그러면 가상 머신이 다시 온라인이 되면 경고가 자동으로 해결되고 하트비트 레코드가 다시 확인됩니다.

:::image type="content" source="media/tutorial-monitor-vm/alert-rule-details.png" lightbox="media/tutorial-monitor-vm/alert-rule-details.png" alt-text="경고 규칙 세부 정보입니다.":::

**검토 + 만들기를** 클릭하여 경고 규칙을 만듭니다.

## <a name="view-the-alert"></a>경고 보기
경고 규칙을 테스트하려면 가상 머신을 중지합니다. 작업 그룹에서 알림을 구성한 경우 몇 분 내에 해당 알림을 받게 됩니다. 가상 머신에 대한 경고 페이지에 표시된 요약에 표시된 **경고도** 표시됩니다.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="경고 요약":::

**심각도를** 클릭하여 해당 경고 목록을 확인합니다. 경고 자체를 클릭하여 세부 정보를 봅니다.

:::image type="content" source="media/tutorial-monitor-vm/alerts-summary.png" lightbox="media/tutorial-monitor-vm/alerts-summary.png" alt-text="경고 목록":::

## <a name="next-steps"></a>다음 단계
이제 로그 데이터에서 경고를 만드는 방법을 알아보셨으므로 데이터 수집 규칙을 사용하여 가상 머신에서 추가 로그 및 성능 데이터를 수집합니다.

> [!div class="nextstepaction"]
> [Azure 가상 머신에서 게스트 로그 및 메트릭 수집](tutorial-monitor-vm-guest.md)

