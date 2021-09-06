---
title: 큐레이팅된 시각화 살펴보기 - Azure IoT Edge
description: Azure 통합 문서를 사용하여 IoT Edge 기본 제공 메트릭 시각화 및 탐색
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7be7260e993f9cc95b542406767d6794f18836f8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537499"
---
# <a name="explore-curated-visualizations-preview"></a>큐레이팅된 시각화 살펴보기(미리 보기)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Monitor 통합 문서를 사용하여 IoT Edge 디바이스로부터 수집된 메트릭을 Azure Portal에서 바로 시각화 및 탐색할 수 있습니다. IoT Edge 디바이스에 대한 큐레이팅된 모니터링 통합 문서는 공용 템플릿 형식으로 제공됩니다.

* IoT Hub에 연결된 디바이스의 경우 Azure Portal의 **IoT Hub** 블레이드에서 **모니터링** 섹션의 **통합 문서** 페이지로 이동합니다.
* IoT Central에 연결된 디바이스의 경우 Azure Portal의 **IoT Central** 블레이드에서 **모니터링** 섹션의 **통합 문서** 페이지로 이동합니다.

큐레이팅된 통합 문서는 IoT Edge 런타임의 [기본 제공 메트릭](how-to-access-built-in-metrics.md)을 사용합니다. 이러한 보기에는 워크로드 모듈의 메트릭 계측이 필요하지 않습니다.

## <a name="access-curated-workbooks"></a>큐레이팅된 통합 문서에 액세스

IoT용 Azure Monitor 통합 문서는 메트릭 시각화를 즉시 시작하는 데 사용할 수 있는, 또는 솔루션에 맞게 사용자 지정할 수 있는 템플릿 세트입니다.

큐레이팅된 통합 문서에 액세스하려면 다음 단계를 따릅니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT 허브 또는 IoT Central 애플리케이션으로 이동합니다.

1. 메뉴의 **모니터링** 섹션에서 **통합 문서** 를 선택합니다.

1. 퍼블릭 템플릿 목록에서 살펴보려는 통합 문서를 선택합니다.

  * **IoT Edge 플릿 보기**: 디바이스 플릿을 모니터링하고 상태 스냅샷을 위한 특정 디바이스로 드릴다운합니다.
  * **IoT Edge 디바이스 세부 정보**: IoT Edge 디바이스에서 메시징, 모듈, 호스트 구성 요소에 대한 디바이스 세부 정보를 시각화합니다.
  * **IoT Edge 상태 스냅샷**: 6개의 일반적인 성능 메트릭을 기반으로 디바이스의 상태를 봅니다. 상태 스냅샷 통합 문서에 액세스하려면 플릿 보기 통합 문서에서 시작한 다음, 보려는 특정 디바이스를 선택합니다. 플릿 보기 통합 문서는 몇 가지 필수 매개 변수를 상태 스냅샷 보기에 전달합니다.

통합 문서를 직접 살펴볼 수도 있고, 다음 섹션을 사용하여 각 통합 문서가 제공하는 데이터 및 시각화의 종류를 미리 볼 수도 있습니다.

## <a name="iot-edge-fleet-view-workbook"></a>IoT Edge 플릿 보기 통합 문서

플릿 보기 통합 문서에는 사용할 수 있는 두 가지 보기가 있습니다.

* **디바이스** 보기에는 활성 디바이스의 개요가 표시됩니다.
* **경고** 보기에는 [미리 구성된 경고 규칙](how-to-create-alerts.md)에서 생성된 경고가 표시됩니다.

통합 문서 맨 위에 있는 탭을 사용하여 보기 간에 전환할 수 있습니다.

# <a name="devices"></a>[디바이스](#tab/devices)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif" alt-text="플릿 보기 통합 문서의 디바이스 섹션입니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-fleet-view.gif":::

**디바이스** 보기에서 메트릭을 보내는 활성 디바이스의 개요를 참조하세요. 이 보기는 현재 IoT 허브 또는 IoT Central 애플리케이션과 연결된 디바이스를 표시합니다.

오른쪽에는 전송된 로컬 및 업스트림 메시지를 보여 주는 복합 막대가 있는 디바이스 목록이 있습니다. 디바이스 이름으로 목록을 필터링하고 디바이스 이름 링크를 클릭하여 자세한 메트릭을 볼 수 있습니다.

왼쪽의 하이브 셀 시각화는 어떤 디바이스가 정상이고 어떤 디바이스가 비정상인지를 보여 줍니다. 디바이스에서 메트릭을 마지막으로 보낸 시점도 보여 줍니다. 30분 넘게 메트릭을 보내지 않은 디바이스는 파란색으로 표시됩니다. 하이브 셀에서 디바이스 이름을 클릭하여 해당 상태 스냅샷을 확인합니다. 상태를 결정할 때 디바이스의 마지막 3개 측정값만 고려됩니다. 보고된 메트릭에서 일시적인 급증에 대해 최근 데이터 계정만 사용합니다.

# <a name="alerts"></a>[경고](#tab/alerts)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif" alt-text="플릿 보기 통합 문서의 경고 섹션입니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-alerts.gif":::

**경고** 보기의 [pre-created alert rules](how-to-create-alerts.md)(미리 만든 경고 규칙)에서 생성된 알림을 확인하세요. 이 보기를 사용하면 여러 IoT 허브 또는 IoT Central 애플리케이션의 경고를 볼 수 있습니다.

왼쪽에는 경고 심각도 목록과 함께 개수가 있습니다. 오른쪽에는 지역당 경고 총계가 표시된 지도가 있습니다.

경고 세부 정보를 보려면 심각도 행을 클릭합니다. **경고 규칙** 링크를 클릭하면 경고 컨텍스트로 이동합니다. **디바이스** 링크를 클릭하면 자세한 메트릭 통합 문서가 열립니다. 이 보기에서 열면, 디바이스 세부 정보 통합 문서가 경고가 발생했을 때의 시간 범위로 자동으로 조정됩니다.

---

## <a name="iot-edge-device-details-workbook"></a>IoT Edge 디바이스 세부 정보 통합 문서

디바이스 세부 정보 통합 문서에는 사용할 수 있는 세 가지 보기가 있습니다.

* **메시징** 보기는 디바이스에 대한 메시지 경로를 시각화하고 메시징 시스템의 전반적인 상태를 보고합니다.
* **모듈** 보기는 디바이스의 개별 모듈이 수행되는 방식을 보여 줍니다.
* **호스트** 보기는 호스트 구성 요소 및 리소스 사용에 대한 버전 정보를 포함하여 호스트 디바이스에 대한 정보를 표시합니다.

통합 문서 맨 위에 있는 탭을 사용하여 보기 간에 전환할 수 있습니다.

디바이스 세부 정보 통합 문서는 또한 IoT Edge 포털 기반 문제 해결 환경과 통합되므로 디바이스에서 오는 **라이브 로그** 를 볼 수 있습니다. 통합 문서 위의 **\<device name> 라이브 문제 해결** 단추를 선택하여 이 환경에 액세스할 수 있습니다.

# <a name="messaging"></a>[메시징](#tab/messaging)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif" alt-text="디바이스 세부 정보 통합 문서의 메시징 섹션입니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-messaging-details.gif":::

**메시징** 보기에는 라우팅 세부 정보, 라우팅 그래프, 메시징 상태의 세 가지 하위 섹션이 포함됩니다. 전체 시간 범위를 선택한 범위로 조정하려면 시간 차트를 끌어서 놓습니다.

**라우팅** 섹션에는 발신 모듈과 수신 모듈 간의 메시지 흐름이 표시됩니다. 메시지 수, 속도, 연결된 클라이언트 수와 같은 정보를 제공합니다. 추가로 드릴인하려면 발신자 또는 수신자를 클릭합니다. 발신자를 클릭하면 발신자가 경험하는 대기 시간 추세 차트와 보낸 메시지 수가 표시됩니다. 수신자를 클릭하면 수신자에 대한 큐 길이 추세와 수신된 메시지 수가 표시됩니다.

**그래프** 섹션에는 모듈 간의 메시지 흐름에 대한 시각적 표현이 표시됩니다. 끌어서 확대/축소하여 그래프를 조정할 수 있습니다.

**상태** 섹션은 메시징 하위 시스템의 전체 상태와 관련된 다양한 메트릭을 제공합니다. 오류가 표시된 경우 세부 정보로 점진적으로 드릴인할 수 있습니다.

# <a name="modules"></a>[모듈](#tab/modules)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif" alt-text="디바이스 세부 정보 통합 문서의 모듈 섹션입니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-module-details.gif":::

**모듈** 보기는 디바이스에서 실행 중인 모든 모듈의 상태를 보고하는 edgeAgent 모듈에서 수집된 메트릭을 표시합니다. 다음과 같은 정보가 포함됩니다.

* 모듈 가용성
* 모듈별 CPU 및 메모리 사용
* 모든 모듈에서의 CPU 및 메모리 사용
* 모듈 다시 시작 횟수 및 다시 시작 타임라인

# <a name="host"></a>[Host](#tab/host)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif" alt-text="디바이스 세부 정보 통합 문서의 호스트 섹션입니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-host-details.gif":::

**호스트** 보기는 edgeAgent 모듈의 메트릭을 표시합니다. 다음과 같은 정보가 포함됩니다.

* 호스트 구성 요소 버전 정보
* 작동 시간
* 호스트 수준에서 CPU, 메모리, 디스크 공간 사용

# <a name="live-logs"></a>[라이브 로그](#tab/livelogs)

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif" alt-text="디바이스 세부 정보 통합 문서를 통해 라이브 로그에 액세스합니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-troubleshoot-live.gif":::

이 통합 문서는 포털 기반 문제 해결 환경과 직접 통합됩니다. **Troubleshoot live**(문제 해결 라이브) 단추를 클릭하여 문제 해결 화면으로 이동합니다. 여기서는 디바이스에서 주문형으로 끌어온 모듈 로그를 쉽게 볼 수 있습니다. 시간 범위는 통합 문서의 시간 범위로 자동으로 설정되므로, 즉시 temporal 컨텍스트에 있게 됩니다. 이 환경에서 모듈을 다시 시작할 수도 있습니다.

---

## <a name="iot-edge-health-snapshot-workbook"></a>IoT Edge 상태 스냅샷 통합 문서

플릿 보기 통합 문서 내에서 상태 스냅샷 통합 문서에 액세스할 수 있습니다. 플릿 보기 통합 문서는 상태 스냅샷 뷰를 초기화하는 데 필요한 일부 매개 변수를 전달합니다. 하이브 셀에서 디바이스 이름을 선택하여 해당 디바이스의 상태 스냅샷을 확인합니다.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png" alt-text="플릿 보기 통합 문서에서 디바이스를 선택하여 상태 스냅샷 통합 문서에 액세스합니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-access-health-snapshot.png":::

기본적으로 상태 스냅샷은 6개의 신호로 구성됩니다.

* 업스트림 메시지
* 로컬 메시지
* 큐 길이
* 디스크 사용량
* 호스트 수준 CPU 사용률
* 호스트 수준 메모리 사용률

위 신호는 구성 가능한 임계값을 기준으로 측정되어 디바이스가 정상인지를 확인합니다. 통합 문서를 편집하여 임계값을 조정하거나 새 신호를 추가할 수 있습니다. 통합 문서 사용자 지정에 대해 알아보려면 다음 섹션을 참조하세요.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif" alt-text="상태 스냅샷 통합 문서를 봅니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-explore-health-snapshot.gif":::

## <a name="customize-workbooks"></a>통합 문서 사용자 지정

[Azure Monitor 통합 문서](../azure-monitor/visualize/workbooks-overview.md)를 얼마든지 사용자 지정할 수 있습니다. 요구 사항에 맞게 퍼블릭 템플릿을 편집할 수 있습니다. 모든 시각화는 [InsightsMetrics](/azure/azure-monitor/reference/tables/insightsmetrics) 테이블의 리소스 중심 [KQL](/azure/data-explorer/kusto/query/) 쿼리에 의해 구현됩니다. 상태 임계값을 편집하는 아래 예제를 참조하세요.

통합 문서의 사용자 지정을 시작하려면 먼저 편집 모드로 들어갑니다. 통합 문서의 메뉴 모음에서 **편집** 단추를 선택합니다.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png" alt-text="통합 문서의 편집 모드로 들어갑니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-access-edit-mode.png":::

큐레이팅된 통합 문서는 통합 문서 그룹을 광범위하게 사용합니다. 여러 중첩된 그룹에서 **편집** 을 클릭해야 시각화 쿼리를 볼 수 있습니다.

변경 내용을 새 통합 문서로 저장합니다. 저장된 통합 문서를 팀과 [공유](../azure-monitor/visualize/workbooks-access-control.md)하거나 조직의 리소스 배포 과정에서 [프로그래밍 방식으로 배포](../azure-monitor/visualize/workbooks-automate.md)할 수 있습니다.

예를 들어 디바이스가 정상 또는 비정상으로 간주되는 시기의 임계값을 변경하는 것이 좋습니다. 그렇게 하려면 이 통합 문서가 디바이스를 비교하는 모든 메트릭 임계값을 포함하는 **device-health-graph** 쿼리 항목에 도달할 때까지 플릿 보기 통합 문서 템플릿으로 드릴인하면 됩니다.

:::image type="content" source="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif" alt-text="쿼리 항목에 도달할 때까지 중첩된 구성 요소의 편집 모드를 계속 열어 둡니다." lightbox="./media/how-to-explore-curated-visualizations/how-to-edit-thresholds.gif":::

## <a name="next-steps"></a>다음 단계

[경고 규칙](how-to-create-alerts.md) 및 [사용자 지정 모듈의 메트릭](how-to-add-custom-metrics.md)을 사용하여 모니터링 솔루션을 사용자 지정합니다.