---
title: 자습서 - IoT Edge에 대한 Azure Monitor 통합 문서
description: IoT용 Azure Monitor 통합 문서를 사용하여 IoT Edge 모듈 및 디바이스를 모니터링하는 방법을 알아봅니다.
author: kgremban
manager: lizross
ms.author: kgremban
ms.date: 08/13/2021
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 6ba79aa63700a35e79d49febad8510e283b35441
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122207604"
---
# <a name="tutorial-monitor-iot-edge-devices"></a>자습서: IoT Edge 디바이스 모니터링

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Monitor 통합 문서를 사용하여 Azure IoT Edge 배포의 상태 및 성능을 모니터링합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
>
> * IoT Edge 디바이스가 공유하는 메트릭과 메트릭 수집기 모듈이 메트릭을 처리하는 방법의 이해
> * IoT Edge 디바이스에 메트릭 수집기 모듈 배포
> * 디바이스에서 수집된 메트릭의 큐레이팅된 시각화 보기

## <a name="prerequisites"></a>필수 구성 요소

시뮬레이션된 온도 센서 모듈이 배포된 IoT Edge 디바이스. 아직 디바이스가 없으면 [가상 Linux 디바이스에 첫 번째 IoT Edge 모듈 배포](quickstart-linux.md)의 단계에 따라 가상 머신을 사용하여 디바이스를 만듭니다.

## <a name="understand-iot-edge-metrics"></a>IoT Edge 메트릭 이해

모든 IoT Edge 디바이스는 2개의 *런타임 모듈* 을 사용하여 디바이스에 있는 다른 모든 모듈의 수명 주기와 통신을 관리합니다. 이러한 모듈을 **IoT Edge 에이전트** 및 **IoT Edge 허브** 라고 합니다. 이러한 모듈에 대한 자세한 내용은 [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)를 참조하세요.

두 런타임 모듈은 IoT Edge 디바이스 또는 디바이스의 개별 모듈 성능을 원격으로 모니터링할 수 있는 메트릭을 만듭니다. IoT Edge 에이전트는 개별 모듈 및 호스트 디바이스의 상태를 보고하므로, 모듈이 올바르게 실행된 기간 또는 디바이스에서 사용되는 RAM의 양 및 CPU 비율과 같은 메트릭을 만듭니다. IoT Edge 허브는 디바이스의 통신에 대해 보고하므로, 보내고 받은 총 메시지 수 또는 직접 메서드를 확인하는 데 걸리는 시간과 같은 메트릭을 만듭니다. 사용 가능한 메트릭의 전체 목록은 [기본 제공 메트릭 액세스](how-to-access-built-in-metrics.md)를 참조하세요.

이러한 메트릭은 두 모듈에서 자동으로 노출되므로, 이러한 메트릭에 액세스하고 보고하는 사용자 고유의 솔루션을 만들 수 있습니다. 이 프로세스를 더 쉽게 수행할 수 있도록 Microsoft에서는 사용자 지정 솔루션이 없거나 원하지 않는 사용자를 위해 이 프로세스를 처리하는 [azureiotedge-metrics-collector 모듈](https://hub.docker.com/_/microsoft-azureiotedge-metrics-collector)을 제공합니다. 메트릭 수집기 모듈은 두 런타임 모듈 및 사용자가 모니터링하려는 다른 모듈에서 메트릭을 수집하여 디바이스 외부로 전송합니다.

메트릭 수집기 모듈은 메트릭을 클라우드로 보내는 두 가지 방식 중 하나로 작동합니다. 이 자습서에서 사용할 첫 번째 옵션은 메트릭을 Log Analytics에 직접 보내는 것입니다. 네트워킹 정책에 필요한 경우에만 권장되는 두 번째 옵션은 IoT Hub를 통해 메트릭을 보낸 다음, Log Analytics에 메트릭 메시지를 전달하는 경로를 설정하는 것입니다. 어느 방법을 사용하든 메트릭이 Log Analytics 작업 영역에 있으면 Azure Monitor 통합 문서를 통해 볼 수 있습니다.

## <a name="create-a-log-analytics-workspace"></a>Log Analytics 작업 영역 만들기

Log Analytics 작업 영역은 메트릭 데이터를 수집하는 데 필요하며, 디바이스를 모니터링할 수 있도록 쿼리 언어 및 Azure Monitor와의 통합을 제공합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Log Analytics 작업 영역** 을 검색하여 선택합니다.

1. **만들기** 를 선택한 다음, 프롬프트의 지시에 따라 새 작업 영역을 만듭니다.

1. 작업 영역이 만들어지면 **리소스로 이동** 을 선택합니다.

1. **설정** 아래의 주 메뉴에서 **에이전트 관리** 를 선택합니다.

1. **작업 영역 ID** 및 **기본 키** 값을 복사합니다. 두 값은 자습서의 후반부에서 메트릭을 이 작업 영역으로 보내도록 메트릭 수집기 모듈을 구성할 때 사용됩니다.

## <a name="retrieve-your-iot-hub-resource-id"></a>IoT 허브 리소스 ID 검색

메트릭 수집기 모듈을 구성할 때 IoT 허브의 Azure Resource Manager 리소스 ID를 제공해야 합니다. 이 ID를 지금 검색합니다.

1. Azure Portal에서 IoT 허브로 이동합니다.

1. 왼쪽 메뉴의 **설정** 에서 **속성** 을 선택합니다.

1. **리소스 ID** 값을 복사합니다. `/subscriptions/<subscription_id>/resourceGroups/<resource_group_name>/providers/Microsoft.Devices/IoTHubs/<iot_hub_name>` 형식이어야 합니다.

## <a name="deploy-the-metrics-collector-module"></a>메트릭 수집기 모듈 배포

모니터링하려는 모든 디바이스에 메트릭 수집기 모듈을 배포합니다. 이 모듈은 다른 모듈과 마찬가지로 디바이스에서 실행되며, 메트릭 수집을 위해 할당된 엔드포인트를 모니터링하여 메트릭을 수집하고 클라우드로 전송합니다.

다음 단계에 따라 수집기 모듈을 배포하고 구성합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 IoT 허브로 이동합니다.

1. 왼쪽에 있는 메뉴의 **자동 디바이스 관리** 에서 **IoT Edge** 를 선택합니다.

1. IoT Edge 디바이스 목록에서 대상 디바이스의 디바이스 ID를 선택하여 디바이스 세부 정보 페이지를 엽니다.

1. 위쪽 메뉴 모음에서 **모듈 설정** 을 선택하여 3단계 모듈 배포 페이지를 엽니다.

1. 포털에서 모듈을 배포하는 첫 번째 단계는 디바이스에 있어야 하는 **모듈** 을 선언하는 것입니다. 빠른 시작에서 만든 것과 동일한 디바이스를 사용하는 경우 **SimulatedTemperatureSensor** 가 이미 나열되어 있을 것입니다. 나열되지 않았으면 다음과 같이 지금 추가합니다.

   1. **추가** 를 선택한 다음, 드롭다운 메뉴에서 **Marketplace 모듈** 을 선택합니다.

   1. **SimulatedTemperatureSensor** 를 검색하여 선택합니다.

1. 메트릭 수집기 모듈을 추가하고 구성합니다.

   1. **추가** 를 선택한 다음, 드롭다운 메뉴에서 **Marketplace 모듈** 을 선택합니다.
   1. **IoT Edge 메트릭 수집기** 를 검색하여 선택합니다.
   1. 모듈 목록에서 메트릭 수집기 모듈을 선택하여 구성 세부 정보 페이지를 엽니다.
   1. **환경 변수** 탭으로 이동합니다.
   1. 다음 값을 업데이트합니다.

      | Name | 값 |
      | ---- | ----- |
      | **ResourceId** | 이전 섹션에서 검색한 IoT 허브 리소스 ID입니다. |
      | **UploadTarget** | `AzureMonitor` |
      | **LogAnalyticsWorkspaceId** | 이전 섹션에서 검색한 Log Analytics 작업 영역 ID입니다. |
      | **LogAnalyticsSharedKey** | 이전 섹션에서 검색한 Log Analytics 키입니다. |

   1. 나중에 추가할 수 있는 추가 구성 옵션의 자리 표시자인 **OtherConfig** 환경 변수를 삭제합니다. 이 자습서에서는 필요 없습니다.
   1. **업데이트** 를 선택하여 변경 내용을 저장합니다.

1. **다음: 경로** 를 선택하여 모듈 배포의 두 번째 단계를 진행합니다.

1. 포털은 메트릭 수집기의 경로를 자동으로 추가합니다. IoT Hub를 통해 메트릭을 보내도록 수집기 모듈을 구성한 경우 이 경로를 사용하게 되지만, 이 자습서에서는 메트릭을 Log Analytics에 직접 보낼 것이므로 필요 없습니다. **FromMetricsCollectorToUpstream** 경로를 삭제합니다.

1. **검토 + 만들기** 를 선택하여 모듈 배포의 마지막 단계를 진행합니다.

1. **만들기** 를 선택하여 배포를 완료합니다.

모듈 배포를 완료한 후 디바이스 세부 정보 페이지로 돌아가면 4개 모듈이 **배포에 지정됨** 으로 나열되는 것을 볼 수 있습니다. 4개 모듈이 전부 **디바이스에서 보고됨**(모듈이 성공적으로 시작되어 상태를 IoT Hub 보고했음을 의미)으로 나열될 때까지 몇 분 정도 걸릴 수 있습니다. 페이지를 새로 고쳐 최신 상태를 확인합니다.

## <a name="monitor-device-health"></a>디바이스 상태 모니터링

디바이스 모니터링 통합 문서를 볼 수 있도록 준비될 때까지 최대 15분이 걸릴 수 있습니다. 메트릭 수집기 모듈을 배포하면 모듈이 메트릭 메시지를 Log Analytics에 전송하기 시작하고, 메트릭 메시지는 테이블 내에 구성됩니다. 사용자가 제공한 IoT Hub 리소스 ID는 수집된 메트릭을 해당 메트릭이 속한 허브에 연결합니다. 결과적으로 큐레이팅된 IoT Edge 통합 문서에서는 리소스 ID를 사용하여 메트릭 테이블을 쿼리함으로써 메트릭을 검색할 수 있습니다.

Azure Monitor는 다음과 같은 IoT용 세 가지 기본 통합 문서 템플릿을 제공합니다.

* **IoT Edge 플릿 보기** 통합 문서에는 비정상 디바이스를 식별하고 각 디바이스의 성능을 드릴다운할 수 있도록 활성 디바이스의 개요가 표시됩니다. 이 통합 문서에는 사용자가 만들 수 있는 모든 경고 규칙에서 생성된 경고도 표시됩니다.
* **IoT Edge 디바이스 세부 정보** 통합 문서는 메시지, 모듈 및 호스트의 세 가지 범주에 대한 시각화를 제공합니다. 메시지 보기는 디바이스에 대한 메시지 경로를 시각화하고 메시지 시스템의 전반적인 상태를 보고합니다. 모듈 보기는 디바이스의 개별 모듈 성능을 보여줍니다. 호스트 보기는 호스트 구성 요소 및 리소스 사용에 대한 버전 정보를 포함하여 호스트 디바이스에 대한 정보를 표시합니다.
* **IoT Edge 상태 스냅샷** 통합 문서는 구성 가능한 임계값에 대해 디바이스 신호를 측정하여 디바이스 상태가 정상인지 확인합니다. 이 통합 문서는 특정 디바이스의 상태 스냅샷을 초기화하는 데 필요한 매개 변수를 전달하는 플릿 보기 통합 문서 내에서만 액세스할 수 있습니다.

### <a name="explore-the-fleet-view-and-health-snapshot-workbooks"></a>플릿 보기 및 상태 스냅샷 통합 문서 살펴보기

플릿 보기 통합 문서에는 모든 디바이스가 표시되며, 특정 디바이스를 선택하여 해당 상태 스냅샷을 볼 수 있습니다. 다음 단계에 따라 통합 문서 시각화를 살펴볼 수 있습니다.

1. Azure Portal에서 IoT 허브 페이지로 돌아갑니다.

1. 주 메뉴에서 아래로 스크롤하여 **모니터링** 섹션을 찾은 다음, **통합 문서** 를 선택합니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/workbooks-gallery.png" alt-text="통합 문서를 선택하여 Azure Monitor 통합 문서 갤러리를 엽니다.":::

1. **IoT Edge 플릿 보기** 통합 문서를 선택합니다.

1. 메트릭 수집기 모듈을 실행하고 있는 디바이스가 표시됩니다. 디바이스가 **정상** 또는 **비정상** 으로 나열됩니다.

1. 디바이스 이름을 선택하여 **IoT Edge 상태 스냅샷** 을 열고 디바이스 상태에 대한 세부 정보를 봅니다.

1. 아무 시간 차트에서 X축 아래의 화살표 아이콘을 사용하거나 차트를 클릭하고 커서를 끌어 시간 범위를 변경합니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/health-snapshot-custom-time-range.png" alt-text="아무 차트를 클릭하고 끌거나 화살표 아이콘을 사용하여 시간 범위를 변경합니다.":::

1. 상태 스냅샷 통합 문서를 닫습니다. 플릿 보기 통합 문서에서 **통합 문서** 를 선택하여 통합 문서 갤러리로 돌아갑니다.

### <a name="explore-the-device-details-workbook"></a>디바이스 세부 정보 통합 문서 살펴보기

디바이스 세부 정보 통합 문서에는 개별 디바이스의 성능 세부 정보가 표시됩니다. 다음 단계에 따라 통합 문서 시각화를 살펴볼 수 있습니다.

1. 통합 문서 갤러리에서 **IoT Edge 디바이스 세부 정보** 통합 문서를 선택합니다.

1. 디바이스 세부 정보 통합 문서에 표시되는 첫 번째 페이지는 **라우팅** 탭이 선택된 **메시지** 보기입니다.

   왼쪽 표에는 엔드포인트별로 구성된 디바이스의 경로가 표시됩니다. 이 디바이스의 경우 IoT Hub 라우팅에 사용되는 특수 용어인 **업스트림** 엔드포인트가 시뮬레이션된 온도 센서 모듈의 **temperatureOutput** 출력에서 메시지를 수신하는 것을 볼 수 있습니다.

   오른쪽 그래프는 연결된 클라이언트 수를 시간별로 추적합니다. 그래프를 클릭하고 끌어 시간 범위를 변경할 수 있습니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-routing.png" alt-text="메시지 보기를 선택하여 디바이스의 통신 상태를 확인합니다.":::

1. **그래프** 탭을 선택하여 경로의 다른 시각화를 확인합니다. 그래프 페이지에서 다른 엔드포인트를 끌어서 놓아 그래프를 다시 정렬할 수 있습니다. 이 기능은 시각화할 경로가 많을 때 유용합니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-messaging-graph.png" alt-text="그래프 보기를 선택하여 디바이스 경로의 대화형 그래프를 확인합니다.":::

1. **상태** 탭은 삭제된 메시지 또는 연결이 끊긴 클라이언트와 같은 메시지 관련 문제를 보고합니다.

1. **모듈** 보기를 선택하여 디바이스에 배포된 모든 모듈의 상태를 확인합니다. 각 모듈을 선택하여 모듈에서 사용하는 CPU 및 메모리 양에 대한 세부 정보를 볼 수 있습니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-modules-availability.png" alt-text="모듈 보기를 선택하여 디바이스에 배포된 각 모듈의 상태를 확인합니다.":::

1. **호스트 보기** 를 선택하여 운영 체제, IoT Edge 디먼 버전 및 리소스 사용을 포함하여 호스트 디바이스에 대한 정보를 확인합니다.

## <a name="view-module-logs"></a>모듈 로그 보기

디바이스에 대한 메트릭을 살펴본 후, 개별 모듈을 더 자세히 검사하려는 경우가 있습니다. IoT Edge는 Azure Portal에서 라이브 모듈 로그 기능을 통해 문제 해결 지원을 제공합니다.

1. 디바이스 세부 정보 통합 문서에서 **실시간 문제 해결** 을 선택합니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/device-details-troubleshoot-live.png" alt-text="디바이스 세부 정보 통합 문서의 오른쪽 위에서 [실시간 문제 해결] 단추를 선택합니다.":::

1. 문제 해결 페이지가 IoT Edge 디바이스의 **edgeAgent** 로그로 열립니다. 디바이스 세부 정보 통합 문서에서 특정 시간 범위를 선택한 경우 해당 설정이 문제 해결 페이지로 전달됩니다.

1. 드롭다운 메뉴를 사용하여 디바이스에서 실행 중인 다른 모듈의 로그로 전환합니다. **다시 시작** 단추를 사용하여 모듈을 다시 시작합니다.

   :::image type="content" source="./media/tutorial-monitor-with-workbooks/troubleshoot-device.png" alt-text="드롭다운 메뉴를 사용하여 다른 모듈의 로그를 살펴보고 [다시 시작] 단추를 사용하여 모듈을 다시 시작합니다.":::

문제 해결 페이지는 IoT Edge 디바이스의 세부 정보 페이지에서도 액세스할 수 있습니다. 자세한 내용은 [Azure Portal에서 IoT Edge 디바이스 문제 해결](troubleshoot-in-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

나머지 자습서를 계속 진행할 것이므로 메트릭 수집기 모듈을 디바이스에 계속 유지하고, 위에서 설명한 통합 문서로 돌아가서 좀 더 복잡한 모듈 및 라우팅을 추가할 때 정보가 어떻게 변경되는지 확인합니다.

개발자 환경을 설정하여 디바이스에 사용자 지정 모듈 배포를 시작하는 다음 자습서를 계속 진행합니다.

> [!div class="nextstepaction"]
> [Linux 컨테이너로 IoT Edge 모듈 개발](tutorial-develop-for-linux.md)
