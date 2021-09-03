---
title: 경고를 사용하여 문제에 대한 알림 받기 - Azure IoT Edge
description: Azure Monitor 경고 규칙을 사용하여 대규모로 모니터링
author: veyalla
ms.author: veyalla
ms.date: 06/08/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 1de507f113f5bf457a4394be9e5fb86c37291e95
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537466"
---
# <a name="get-notified-about-issues-using-alerts-preview"></a>경고를 사용하여 문제에 대한 알림 받기(미리 보기)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

[Azure Monitor 로그 경고](../azure-monitor/alerts/alerts-unified-log.md)를 사용하여 IoT Edge 디바이스를 대규모로 모니터링할 수 있습니다. [솔루션 아키텍처](how-to-collect-and-transport-metrics.md#architecture)에 강조 표시된 대로 Azure Monitor Log Analytics는 메트릭 데이터베이스로 사용됩니다. 이 통합은 리소스 중심 로그 경고를 사용하여 강력하고 유연한 경고 기능을 제공합니다.

> [!IMPORTANT]
> 이 기능은 현재 IoT Hub에서만 사용할 수 있으며 IoT Central에서는 사용할 수 없습니다.

## <a name="create-an-alert-rule"></a>경고 규칙 만들기

디바이스 플릿에서 다양한 범위를 모니터링하는 [로그 경고 규칙을 만들](../azure-monitor/alerts/alerts-log.md) 수 있습니다.

샘플 [KQL](/azure/data-explorer/kusto/query/) 경고 쿼리는 IoT Hub 리소스 아래에 제공됩니다. 에지 디바이스의 메트릭 데이터에 대해 작동하는 쿼리에는 제목에 *IoT Edge:* 접두사가 붙습니다. 이러한 예제를 그대로 사용하거나 필요에 따라 수정하여 정확한 요구에 맞는 쿼리를 만들 수 있습니다.

예제 경고 쿼리에 액세스하려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴의 **모니터링** 섹션에서 **로그** 를 선택합니다.
1. **쿼리** 를 선택하여 예제 쿼리 브라우저를 엽니다.

:::image type="content" source="./media/how-to-create-alerts/example-alerts.png" alt-text="예제 경고 쿼리에 액세스합니다." lightbox="./media/how-to-create-alerts/example-alerts.png":::

[메트릭 수집기 모듈](how-to-collect-and-transport-metrics.md#metrics-collector-module)은 모든 데이터를 표준 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 테이블로 수집합니다. 동일한 테이블을 쿼리하여 사용자 지정 모듈의 메트릭 데이터를 기반으로 경고 규칙을 만들 수 있습니다.

### <a name="split-by-device-dimension"></a>디바이스 차원으로 분할

모든 예제 경고 규칙 쿼리는 디바이스 ID를 기준으로 값을 집계합니다. 이 그룹화는 경고를 발생시킨 디바이스를 확인하는 데 필요합니다. 특정 디바이스를 선택하여 경고 규칙을 활성화하거나 모든 디바이스에서 활성화할 수 있습니다. 경고 논리를 설정하기 전에 미리 보기 그래프를 사용하여 디바이스별 추세를 탐색합니다.

### <a name="choose-notification-preferences"></a>알림 기본 설정 선택

[작업 그룹](../azure-monitor/alerts/action-groups.md)에서 알림 기본 설정을 구성하고 경고 규칙을 만들 때 경고 규칙에 연결합니다.

## <a name="select-alert-rule-scope"></a>경고 규칙 범위 선택

이전 섹션의 지침을 사용하여 단일 IoT 허브로 범위가 지정된 경고 규칙을 만듭니다. 그러나 여러 IoT 허브에 대해 동일한 규칙을 만들 수 있습니다. 범위를 리소스 그룹 또는 전체 구독으로 변경하여 해당 범위 내의 모든 허브에서 경고 규칙을 사용하도록 설정합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT Hub로 이동합니다.
1. 메뉴의 **모니터링** 섹션에서 **로그** 를 선택합니다.
1. **범위 선택** 을 선택하여 경고 규칙의 범위를 변경합니다.

:::image type="content" source="./media/how-to-create-alerts/change-scope.png" alt-text="경고 범위 변경" lightbox="./media/how-to-create-alerts/change-scope.png":::

`_ResourceId` 필드를 기준으로 값을 집계하고 경고 규칙을 만들 때 *리소스 ID 열* 로 선택합니다. 이 접근 방식은 편의상 경고를 올바른 리소스와 연결합니다.

## <a name="viewing-alerts"></a>경고 보기

[IoT Edge 플릿 보기 통합 문서](how-to-explore-curated-visualizations.md#iot-edge-fleet-view-workbook)의 **경고** 탭에서 여러 IoT Hubs에 걸쳐 디바이스에 대해 생성된 경고를 참조하세요.

경고 규칙 이름을 클릭하여 경고에 대한 자세한 컨텍스트를 확인합니다. 디바이스 이름 링크를 클릭하면 경고가 발생한 시간을 기준으로 디바이스에 대한 자세한 메트릭이 표시됩니다.

## <a name="next-steps"></a>다음 단계

[사용자 지정 모듈의 메트릭](how-to-add-custom-metrics.md)을 통해 모니터링 솔루션을 향상시킵니다.