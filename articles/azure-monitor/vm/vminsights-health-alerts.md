---
title: VM 인사이트 게스트 상태 경고(미리 보기)
description: VM 인사이트 게스트 상태에 의해 생성된 경고와 이를 사용하도록 설정하고 알림을 구성하는 방법에 대해 설명합니다.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/10/2020
ms.openlocfilehash: 28bc9f5ed218302df38b342628e9792b1facb191
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132812494"
---
# <a name="vm-insights-guest-health-alerts-preview"></a>VM 인사이트 게스트 상태 경고(미리 보기)
VM 인사이트 게스트 상태를 사용하면 일정한 간격으로 샘플링되는 성능 측정 집합에 정의된 대로 가상 머신의 상태를 볼 수 있습니다. 가상 머신 또는 모니터가 비정상 상태로 변경되면 경고가 생성될 수 있습니다. [Azure Monitor의 경고 규칙에 의해 생성](../alerts/alerts-overview.md)된 경고를 확인하고 관리할 수 있으며 새 경고가 생성될 때 사전에 알림을 받도록 선택할 수 있습니다.

## <a name="configure-alerts"></a>경고 구성
이 기능이 미리 보기 상태인 동안에는 VM 인사이트 게스트 상태에 대한 명시적 경고 규칙을 만들 수 없습니다. 기본적으로 각 가상 머신에 대해 경고가 생성되지만 각 모니터에 대해서는 경고가 생성되지 않습니다.  즉, 모니터가 가상 머신의 현재 상태에 영향을 주지 않는 상태로 변경되는 경우 가상 머신 상태가 변경되지 않으므로 경고가 생성되지 않습니다. 

Azure Portal에서 가상 머신에 대한 구성에 있는 **경고 상태** 설정에서 특정 가상 머신 또는 가상 머신의 특정 모니터에 대한 경고를 사용하지 않도록 설정할 수 있습니다. Azure Portal에서 모니터를 구성하는 방법에 대한 자세한 내용은 [VM 인사이트 게스트 상태(미리 보기)에서 모니터링 구성](vminsights-health-configure.md)을 참조하세요. 가상 머신 집합에서 모니터를 구성하는 방법에 대한 자세한 내용은 [데이터 수집 규칙을 사용하여 VM 인사이트 게스트 상태에서 모니터링 구성(미리 보기)](vminsights-health-configure-dcr.md)을 참조하세요.

## <a name="alert-severity"></a>경고 심각도
게스트 상태에 의해 생성되는 경고의 심각도는 가상 머신의 심각도 또는 경고를 트리거하는 모니터에 직접 매핑됩니다.

| 모니터 상태 | 경고 심각도 |
|:---|:---|
| 위험 | Sev1 |
| 경고  | Sev2 |
| 정상  | Sev4 |

## <a name="alert-lifecycle"></a>경고 수명 주기
**경고** 또는 **위험** 상태로 변경될 때마다 각 가상 머신에 대한 [Azure 경고](../alerts/alerts-overview.md)가 생성됩니다. **Azure Monitor** 메뉴의 **경고** 또는 Azure Portal의 가상 머신 메뉴에서 경고를 확인합니다.

가상 머신 상태가 변경될 때 경고가 이미 **발생** 한 경우 두 번째 경고가 생성되지 않지만 동일한 경고의 심각도는 가상 머신의 상태와 일치하도록 변경됩니다. 예를 들어 **경고** 가 이미 **발생** 한 상태에서 가상 머신이 **위험** 상태로 변경되면 해당 경고의 심각도가 **Sev1** 로 변경됩니다. **Sev1** 이 이미 **발생** 한 상태에서 가상 머신이 **경고** 상태로 변경되면 해당 경고의 심각도가 **Sev2** 로 변경됩니다. 가상 머신이 다시 **정상** 상태로 전환되면 심각도가 **Sev4** 로 변경되고 경고가 해결됩니다.

## <a name="viewing-alerts"></a>경고 보기
[Azure Portal의 다른 경고](../alerts/alerts-overview.md#alerts-experience)와 함께 VM 인사이트 게스트 상태에 의해 생성된 경고를 봅니다. **Azure Monitor** 메뉴에서 **경고** 를 선택하여 모니터링되는 모든 리소스에 대한 경고를 확인하거나 가상 머신의 메뉴에서 **경고** 를 선택하여 해당 가상 머신에 대한 경고를 볼 수 있습니다.

## <a name="alert-properties"></a>경고 속성

### <a name="properties-in-the-azure-portal"></a>Azure Portal의 속성
다음 표에서는 Azure Portal에서의 경고 속성에 대해 설명합니다.

| 속성 | Description |
|:---|:---|
| 경고가 만들어지기 전의 모니터 상태 | 이 경고가 처음 발생하기 전의 모니터 또는 가상 머신의 상태입니다. |
| 경고가 만들어진 때의 모니터 상태 | 처음으로 경고가 발생했을 때의 모니터 또는 가상 머신의 상태입니다. 경고를 발생시킨 상태입니다. |
| 경고가 생성되었을 때의 상태 전환에 대해 자세히 알아보려면 | 정확한 상태 전환을 볼 수 있는 VM 상태 페이지에 연결합니다. 이 상태 전환은 모니터가 **정상** 상태에서 비정상 상태로 전환될 때의 인스턴스를 나타냅니다. |

예를 들어, 모니터가 t0 시간에 **정상** 에서 **위험** 으로 전환되고 새 경고가 **Sev1** 과 발생합니다. 그런 다음 t1 시간에 **위험** 에서 **경고** 로 전환되며 경고 심각도가 **Sev2** 로 업데이트됩니다. t2 시간에는 **정상** 상태가 되고 경고가 해결됩니다.

이 전체 시퀀스 동안 경고 속성에는 다음 값이 포함됩니다.

- 경고를 만들기 전의 모니터 상태: 정상
- 경고가 만들어졌을 때의 모니터 상태: 위험
- 경고가 생성되었을 때의 상태 전환에 대해 자세히 알아보려면 t0 시간에 발생한 상태 전환에 대한 탐색 링크를 확인하세요.


### <a name="properties-in-notifications"></a>알림의 속성
알림에 포함된 경고의 속성은 다음 표에 설명되어 있습니다.

| 속성 | Description |
|:---|:---|
| 이전 모니터 상태 | 상태를 변경하기 전의 모니터 또는 가상 머신 상태입니다. 경고 심각도 업데이트가 이 알림을 트리거하는 경우 이 속성은 심각도 업데이트 직전의 상태를 나타냅니다. |
| 현재 모니터 상태 | 상태가 변경되었을 때의 모니터 또는 가상 머신의 상태입니다. 경고 심각도 업데이트가 이 알림을 트리거하는 경우 이 속성은 새 상태를 나타냅니다. |
| 이 상태 전환에 대해 자세히 알아보려면 | 정확한 상태 전환을 볼 수 있는 VM 상태 페이지에 연결합니다. 이 상태 전환은 이 알림을 트리거한 변경된 상태를 모니터링할 때의 인스턴스를 나타냅니다. |

위의 예를 사용하여 알림은 매번 다음과 같은 속성을 갖습니다.

t0 시간에 수신되는 알림
- 이전 모니터 상태: 정상
- 현재 모니터 상태: 위험
- 이 상태 전환에 대해 자세히 알아보려면 t0 시간에 발생한 상태 전환에 대한 탐색 링크를 확인하세요.

t1 시간에 수신되는 알림
- 이전 모니터 상태: 위험
- 현재 모니터 상태: 경고
- 이 상태 전환에 대해 자세히 알아보려면 t1 시간에 발생한 상태 전환에 대한 탐색 링크를 확인하세요.

t2 시간에 수신되는 알림
- 이전 모니터 상태: 경고
- 현재 모니터 상태: 정상
- 이 상태 전환에 대해 자세히 알아보려면 t2 시간에 발생한 상태 전환에 대한 탐색 링크를 확인하세요.

## <a name="configure-notifications"></a>알림 구성
게스트 상태에 의해 트리거되는 경고를 사전에 알림을 받으려면 [작업 그룹](../alerts/action-groups.md)을 만들어 SMS 메시지 보내기 또는 논리 앱 시작과 같이 수행할 다른 작업을 정의합니다. 그런 다음 모니터와 가상 컴퓨터의 범위를 지정 하 고 해당 작업 그룹을 사용 하는 [경고 처리 규칙](../alerts/alerts-action-rules.md) 을 만듭니다.

Azure Portal **모니터** 메뉴에서 **경고** 를 선택한 다음 **경고 처리 규칙 (미리 보기)** 을 선택 합니다. 
:::image type="content" source="media/vminsights-health-alerts/alerts-view.png" alt-text="경고 보기" lightbox="media/vminsights-health-alerts/alerts-view.png":::

**만들기** 를 클릭 하 여 새 경고 처리 규칙을 만듭니다. 

:::image type="content" source="media/vminsights-health-alerts/alert-processing-rule-new.png" alt-text="새 경고 처리 규칙" lightbox="media/vminsights-health-alerts/alert-processing-rule-new.png":::

**범위 탭** 에서 **범위 선택** 을 클릭 하 고 구독, 리소스 그룹 또는 하나 이상의 특정 가상 컴퓨터를 선택 합니다. 이 알림은 범위 내에 있는 가상 머신에 대해서만 발생합니다. **모니터 서비스가 VM Insights와 동일한** 필터를 추가 합니다. 다른 필터를 추가하여 알림을 트리거할 특정 경고를 지정합니다. 예를 들어 **심각도** 를 사용하여 특정 심각도와 일치하는 모든 모니터의 경고를 일치시킬 수 있습니다.

:::image type="content" source="media/vminsights-health-alerts/alert-processing-rule-scope.png" alt-text="경고 처리 규칙 범위" lightbox="media/vminsights-health-alerts/alert-processing-rule-scope.png":::

**규칙 설정** 탭을 선택 하 고 **규칙 유형에** 대해 **작업 그룹 적용** 을 선택 합니다. **작업 그룹 추가** 를 클릭 하 고 모니터와 연결할 작업 그룹을 선택 합니다. 규칙 이름을 지정하고 저장해야 하는 리소스 그룹을 선택합니다. **만들기** 를 클릭하여 규칙을 만듭니다.

:::image type="content" source="media/vminsights-health-alerts/alert-processing-rule-settings.png" alt-text="경고 처리 규칙 설정" lightbox="media/vminsights-health-alerts/alert-processing-rule-settings.png":::

**세부 정보** 탭을 선택 합니다. 경고 처리 규칙을 저장할 **구독** 및 **리소스 그룹** 을 선택 하 고 설명이 포함 된 **규칙 이름을** 제공 합니다. **제거 + 만들기** 를 클릭 하 여 경고 처리 규칙을 만들고 사용 하도록 설정 합니다.

:::image type="content" source="media/vminsights-health-alerts/alert-processing-rule-details.png" alt-text="경고 처리 규칙 세부 정보" lightbox="media/vminsights-health-alerts/alert-processing-rule-details.png":::

## <a name="next-steps"></a>다음 단계

- [VM 인사이트 및 온보드 에이전트에서 게스트 상태를 사용하도록 설정합니다.](vminsights-health-enable.md)
- [Azure Portal을 사용하여 모니터를 구성합니다.](vminsights-health-configure.md)
- [데이터 수집 규칙을 사용하여 모니터를 구성합니다.](vminsights-health-configure-dcr.md)