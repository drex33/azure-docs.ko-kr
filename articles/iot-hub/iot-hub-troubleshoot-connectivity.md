---
title: Azure IoT Hub와의 연결 끊김 모니터링 및 문제 해결
description: Azure IoT Hub의 디바이스 연결과 관련된 일반적인 오류를 모니터링하고 해결하는 방법을 알아봅니다.
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/06/2020
ms.author: jlian
ms.custom:
- mqtt
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: Technical Support'
- fasttrack-edit
- iot
ms.openlocfilehash: 22c1658740af7ef7eccbeca02c6f98485ec11222
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/19/2021
ms.locfileid: "112378307"
---
# <a name="monitor-diagnose-and-troubleshoot-azure-iot-hub-disconnects"></a>Azure IoT Hub와의 연결 끊김 모니터링, 진단, 문제 해결 

IoT 디바이스의 연결 문제는 가능한 실패 지점이 많기 때문에 문제 해결이 어려울 수 있습니다. 애플리케이션 로직, 물리적 네트워크, 프로토콜, 하드웨어, IoT Hub 및 기타 클라우드 서비스는 모두 문제를 일으킬 수 있습니다. 문제의 원인을 감지하고 정확하게 파악하는 기능은 매우 중요합니다. 그러나 대규모 IoT 솔루션에는 수천 개의 디바이스가 있을 수 있으므로 개별 디바이스를 수동으로 확인하는 것은 실용적이지 않습니다. IoT Hub는 다음과 같은 유용한 두 개의 Azure 서비스를 통합합니다.

* **Azure Monitor** Azure Monitor를 사용하면 IoT Hub에서 원격 분석을 수집, 분석, 작업할 수 있습니다. 대규모로 이러한 문제를 감지, 진단 및 해결하는 데 도움이 되도록 IoT Hub가 Azure Monitor를 통해 제공하는 모니터링 기능을 사용합니다. 여기에는 연결이 끊어질 때 알림 및 작업을 트리거하도록 경고를 설정하는 기능과 연결을 끊은 조건을 검색하는 데 사용할 수 있는 로그 구성 기능이 포함됩니다.

* **Azure Event Grid** 중요한 인프라 및 디바이스별 연결 끊기의 경우 Azure Event Grid를 사용하여 디바이스 연결 알림을 신청하고 IoT Hub에서 내보낸 이벤트의 연결을 끊습니다. Azure Event Grid를 사용하여 다음 이벤트 처리기 중 하나를 사용할 수 있습니다.

  - Azure 기능
  - Logic Apps
  - Azure Automation
  - 웹후크
  - Queue storage
  - 하이브리드 연결
  - Event Hubs

## <a name="event-grid-vs-azure-monitor"></a>Event Grid와 Azure Monitor 비교

Event Grid는 대기 시간이 짧은 디바이스별 모니터링 솔루션을 제공하며, 이 솔루션은 중요한 디바이스 및 인프라에 대한 디바이스 연결을 추적하는 데 사용할 수 있습니다. Azure Monitor는 IoT Hub에 연결된 디바이스 수를 모니터링하고 해당 수치가 임계값 아래로 떨어지면 경고를 트리거하는 데 사용할 수 있는 *연결된 디바이스* 메트릭을 제공합니다.

특정 시나리오에 Event Grid 또는 Azure Monitor를 사용할지 여부를 결정할 때 다음 사항을 고려하세요.

* 경고 지연: IoT Hub 연결 이벤트는 Event Grid를 통해 훨씬 더 신속하게 전달됩니다. 따라서 Event Grid는 빠른 알림이 필요한 시나리오에 더 적합합니다.

* 디바이스별 알림: Event Grid는 개별 디바이스에 대한 연결 및 연결 끊기를 추적하는 기능을 제공합니다. 따라서 Event Grid는 중요한 디바이스에 대한 연결을 모니터링해야 하는 시나리오에 더 적합합니다.

* 경량 설정: Azure Monitor 메트릭 경고는 이메일, 문자 메시지, Voice 및 기타 알림을 통해 알림을 전달하기 위하여 다른 서비스와 통합하지 않아도 되는 간단한 설정 환경을 제공합니다.  Event Grid를 사용하면 다른 Azure 서비스와 통합하여 알림을 제공해야 합니다. 두 서비스 모두 다른 서비스와 통합하여 더 복잡한 작업을 트리거할 수 있습니다.

## <a name="event-grid-monitor-connect-and-disconnect-events"></a>Event Grid: 디바이스 연결 및 연결 끊김 이벤트 모니터링

프로덕션 환경에서 디바이스 연결 및 연결 끊기 이벤트를 모니터링하려면 Event Grid에서 [**DeviceConnected** 및 **DeviceDisconnected** 이벤트](iot-hub-event-grid.md#event-types) 알림을 신청하여 경고를 트리거하고 디바이스 연결 상태를 모니터링하는 것이 좋습니다. Event Grid는 Azure Monitor보다 훨씬 짧은 이벤트 대기 시간을 제공하고, 각 디바이스별로 모니터링할 수 있습니다. 해당 요소들은 중요한 디바이스 및 인프라를 모니터링하는 메서드로 Event Grid를 선호하게 만듭니다.

Event Grid를 사용하여 디바이스 연결 끊기에 대한 경고를 모니터링하거나 트리거하는 경우 Azure IoT SDK를 사용하는 디바이스의 SAS 토큰 갱신으로 인한 정기적 연결 끊기를 필터링하는 방식으로 빌드를 수행해야 합니다. 자세한 내용은 [Azure IoT SDK를 사용한 MQTT 디바이스 연결 끊기 동작](#mqtt-device-disconnect-behavior-with-azure-iot-sdks)을 참조하세요.

Event Grid를 사용하여 디바이스 연결 이벤트를 모니터링하는 방법에 대한 자세한 내용은 다음 항목을 검토하세요.

* IoT Hub에서 Event Grid를 사용하는 방법에 대한 개요는 [Event Grid를 사용하여 IoT Hub 이벤트에 반응](iot-hub-event-grid.md)을 참조하세요. [디바이스 연결됨 및 디바이스 연결 끊김 이벤트의 제한 사항](iot-hub-event-grid.md#limitations-for-device-connected-and-device-disconnected-events) 섹션에 특히 주의하세요.

* 디바이스 연결 이벤트 순서를 지정하는 방법에 대한 자습서는 [Azure Cosmos DB를 사용하여 Azure IoT Hub에서 디바이스 연결 이벤트 순서 지정](iot-hub-how-to-order-connection-state-events.md)을 참조하세요.

* 이메일 알림을 보내는 방법에 대한 자습서는 Event Grid 설명서의 [Logic Apps Event Grid를 사용하여 Azure IoT Hub 이벤트에 대한 이메일 알림 보내기](../event-grid/publish-iot-hub-events-to-logic-apps.md)를 참조하세요.

## <a name="azure-monitor-route-connection-events-to-logs"></a>Azure Monitor: 연결 이벤트를 로그로 라우팅

IoT hub는 몇 가지 작업 범주에 대한 리소스 로그를 지속적으로 내보냅니다. 그러나 이 로그 데이터를 수집하려면 분석하거나 보관할 수 있는 대상으로 라우팅하는 진단 설정을 만들어야 합니다. 이러한 대상 중 하나는 Log Analytics 작업 영역을 통한 Azure Monitor 로그([가격 책정 참조](https://azure.microsoft.com/pricing/details/log-analytics/))이며, Kusto 쿼리를 사용하여 데이터를 분석할 수 있습니다.

IoT Hub [리소스 로그 연결 범주](monitor-iot-hub-reference.md#connections)는 디바이스 연결과 관련된 작업 및 오류를 내보냅니다. 다음 스크린샷은 이러한 로그를 Log Analytics 작업 영역으로 라우팅하는 진단 설정을 보여 줍니다.

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/create-diagnostic-setting.png" alt-text="Log Analytics 작업 영역에 연결 로그를 보내기 위한 권장 설정입니다.":::

IoT hub를 만든 후에는 가능한 한 빨리 진단 설정을 만드는 것이 좋습니다. IoT Hub는 항상 리소스 로그를 내보내지만 Azure Monitor는 이러한 로그를 대상으로 라우팅할 때까지 로그 데이터를 수집하지 않기 때문입니다.

로그를 대상으로 라우팅하는 방법에 대한 자세한 내용은 [컬렉션 및 라우팅](monitor-iot-hub.md#collection-and-routing)을 참조하세요. 진단 설정을 만드는 방법에 대한 자세한 내용은 [메트릭 및 로그 사용 자습서](tutorial-use-metrics-and-diags.md)를 참조하세요.

## <a name="azure-monitor-set-up-metric-alerts-for-device-disconnects"></a>Azure Monitor: 디바이스 연결 끊김에 대한 메트릭 경고 설정

IoT Hub에서 내보낸 플랫폼 메트릭에 따라 경고를 설정할 수 있습니다. 메트릭 경고를 사용하면 관심 있는 조건이 발생했음을 개인에게 알리고 해당 조건에 자동으로 응답할 수 있는 작업을 트리거할 수도 있습니다.

[*연결된 디바이스(미리 보기)*](monitor-iot-hub-reference.md#device-metrics) 메트릭은 IoT Hub에 연결된 디바이스 수를 알려줍니다. 이 메트릭이 임계값 아래로 떨어지면 트리거할 경고를 만들 수 있습니다.

:::image type="content" source="media/iot-hub-troubleshoot-connectivity/configure-alert-logic.png" alt-text="연결된 디바이스 메트릭에 대한 경고 논리 설정입니다.":::

메트릭 경고 규칙을 사용하여 대규모로 디바이스 연결이 끊기는 변칙 상황을 모니터링할 수 있습니다. 즉, 많은 수의 디바이스가 예기치 않게 연결이 끊기는 지를 결정하기 위해 경고를 사용합니다. 이러한 상황이 감지되면, 문제 해결에 도움이 되는 로그를 확인할 수 있습니다. 하지만 디바이스별 연결 끊김 및 중요한 디바이스에 대한 연결 끊김을 근실시간으로 모니터링하려면, Event Grid를 사용해야 합니다.

IoT Hub 경고에 대한 자세한 내용은 [모니터 IoT Hub의 경고](monitor-iot-hub.md#alerts)를 참조하세요. IoT Hub에서 경고를 만드는 연습에 대한 내용은 [메트릭 및 로그 사용 자습서](tutorial-use-metrics-and-diags.md)를 참조하세요. 경고에 대한 자세한 개요는 Azure Monitor 설명서의 [Microsoft Azure 경고 개요](../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="azure-monitor-use-logs-to-resolve-connectivity-errors"></a>Azure Monitor: 로그를 사용하여 연결 오류 해결

디바이스 연결 끊김을 감지하는 경우, Azure Monitor 메트릭 경고 또는 Event Grid 모두 로그를 사용하여 문제의 원인을 해결하는 데 도움이 될 수 있습니다. 이 섹션에서는 Azure Monitor 로그에서 일반적인 문제를 찾는 방법을 설명합니다. 아래 단계에서는 IoT Hub 연결 로그를 Log Analytics 작업 영역으로 보내는 [진단 설정](#azure-monitor-route-connection-events-to-logs)을 이미 만들었다고 가정합니다.

IoT Hub 리소스 로그를 Azure Monitor 로그로 라우팅하는 진단 설정을 만든 후에는 다음 단계에 따라 Azure Portal에서 로그를 확인합니다.

1. [Azure Portal](https://portal.azure.com)에서 IoT Hub로 이동합니다.

1. IoT 허브의 왼쪽 창에 있는 **모니터링** 에서 **경고** 를 선택합니다.

1. IoT Hub에 대한 연결 오류 로그를 격리하려면 쿼리 편집기에서 다음 쿼리를 입력한 다음, **실행** 을 선택합니다.

    ```kusto
    AzureDiagnostics
    | where ( ResourceType == "IOTHUBS" and Category == "Connections" and Level == "Error")
    ```

1. 결과가 있는 경우, 자세한 정보를 얻으려면 `OperationName`, `ResultType`(오류 코드), `ResultDescription`(오류 메시지)을 검색합니다.

   ![오류 로그의 예](./media/iot-hub-troubleshoot-connectivity/diag-logs.png)

가장 일반적인 오류에 대해서는 다음 문제 해결 가이드를 따르세요.

* [400027 ConnectionForcefullyClosedOnNewConnection](iot-hub-troubleshoot-error-400027-connectionforcefullyclosedonnewconnection.md)

* [404104 DeviceConnectionClosedRemotely](iot-hub-troubleshoot-error-404104-deviceconnectionclosedremotely.md)

* [401003 IoTHubUnauthorized](iot-hub-troubleshoot-error-401003-iothubunauthorized.md)

* [409002 LinkCreationConflict](iot-hub-troubleshoot-error-409002-linkcreationconflict.md)

* [500001 ServerError](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

* [500008 GenericTimeout](iot-hub-troubleshoot-error-500xxx-internal-errors.md)

## <a name="mqtt-device-disconnect-behavior-with-azure-iot-sdks"></a>Azure IoT SDK를 사용한 MQTT 디바이스 연결 끊기 동작

Azure IoT 디바이스 SDK는 IoT Hub에서 연결을 끊은 다음 MQTT(및 WebSocket을 통한 MQTT) 프로토콜을 통해 SAS 토큰을 갱신할 때 다시 연결합니다. 로그에서는 정보 디바이스 연결 끊기 및 연결 이벤트로 표시되며, 오류 이벤트가 수반되는 경우도 있습니다.

기본값으로 토큰 수명은 모든 SDK에 대해 60분입니다. 그러나 일부 SDK에서는 개발자가 변경할 수 있습니다. 다음 표에는 각 SDK의 토큰 수명, 토큰 갱신 및 토큰 갱신 동작이 요약되어 있습니다.

| SDK) | 토큰 수명 | 토큰 갱신 | 갱신 동작 |
|-----|----------|---------------------|---------|
| .NET | 60분, 구성 가능 | 85%의 수명, 구성 가능 | SDK는 토큰 수명에 더한 10분의 유예 기간에 연결을 끊고 다시 연결합니다. 로그에 생성된 정보 이벤트 및 오류입니다. |
| Java | 60분, 구성 가능 | 85%의 수명, 구성할 수 없음 | SDK는 토큰 수명에 더한 10분의 유예 기간에 연결을 끊고 다시 연결합니다. 로그에 생성된 정보 이벤트 및 오류입니다. |
| Node.js | 60분, 구성 가능 | 구성 가능 | SDK는 토큰 갱신 시 연결을 끊고 다시 연결합니다. 로그에는 정보 이벤트만 생성됩니다. |
| Python | 60분, 구성 가능 | 만료 전 120초 | SDK는 토큰 수명 동안 연결을 끊고 다시 연결합니다. |

다음 스크린샷은 다양한 SDK에 대한 Azure Monitor 로그의 토큰 갱신 동작을 보여 줍니다. 토큰 수명 및 갱신 임계값은 설명된 대로 기본값에서 변경되었습니다.

* 토큰 수명이 1200초(20분)이고 갱신이 수명의 90%에서 발생하도록 설정된 .NET 디바이스 SDK입니다. 연결 끊김이 30분마다 발생합니다.

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/net-mqtt.png" alt-text=".NET SDK를 사용하는 Azure Monitor 로그의 MQTT를 통한 토큰 갱신에 대한 오류 동작입니다.":::

* 토큰 수명이 300초(5분)이고 기본 수명의 85%에서 갱신되는 Java SDK입니다. 연결 끊김이 15분마다 발생합니다.

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/java-mqtt.png" alt-text="Java SDK를 사용하는 Azure Monitor 로그의 MQTT를 통한 토큰 갱신에 대한 오류 동작입니다.":::

* 토큰 수명이 300초(5분)이고 토큰 갱신이 3분에 발생하도록 설정된 Node SDK입니다. 토큰 갱신 시 연결이 끊어집니다. 또한 오류는 없으며 정보 연결/연결 끊기 이벤트만 내보내집니다.

    :::image type="content" source="media/iot-hub-troubleshoot-connectivity/node-mqtt.png" alt-text="Node SDK를 사용하는 Azure Monitor 로그의 MQTT를 통한 토큰 갱신에 대한 오류 동작입니다.":::

다음 쿼리는 결과를 수집하는 데 사용되었습니다. 이 쿼리는 속성 모음에서 SDK 이름 및 버전을 추출합니다. 자세한 사항은 [IoT Hub 로그의 SDK 버전](monitor-iot-hub.md#sdk-version-in-iot-hub-logs)을 참조하세요.

```kusto
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s)
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId) , Protocol =  tostring(parsed_json.protocol)
| distinct TimeGenerated, OperationName, Level, ResultType, ResultDescription, DeviceId, Protocol, SDKVersion

```

IoT 솔루션 개발자 또는 운영자는 로그에서 연결/연결 끊기 이벤트 및 관련 오류를 해석하기 위해 이 동작을 알고 있어야 합니다. 디바이스에 대한 토큰 수명 또는 갱신 동작을 변경하려는 경우 디바이스가 디바이스 쌍 설정을 구현하는지 또는 이를 가능하게 하는 디바이스 메서드를 구현하는지 확인하세요.

Event Hub를 사용하여 디바이스 연결을 모니터링하는 경우, SAS 토큰 갱신으로 인한 정기적 연결 끊김을 필터링하는 방식으로 빌드를 수행해야 합니다. 예를 들어, 연결 끊김 이벤트 다음에 특정 시간 범위 내의 연결 이벤트가 발생하는 한, 연결 끊김에 따른 작업을 트리거하지 않습니다.

> [!NOTE]
> IoT Hub는 디바이스 당 하나의 활성 MQTT 연결만을 지원합니다. 동일한 디바이스 ID를 대신하는 모든 새 MQTT 연결로 인해 IoT Hub가 기존 연결을 삭제하게 됩니다.
>
> 400027 ConnectionForcefullyClosedOnNewConnection이 IoT Hub 로그에 로그인됨

## <a name="i-tried-the-steps-but-they-didnt-work"></a>단계를 시도했지만 작동하지 않습니다.

이전 단계가 도움이 되지 않으면 다음을 시도해 보세요.

* 물리적으로 또는 원격으로(예: SSH) 문제가 있는 디바이스에 액세스할 수 있으면 [디바이스 쪽 문제 해결 가이드](https://github.com/Azure/azure-iot-sdk-node/wiki/Troubleshooting-Guide-Devices)에 따라 문제 해결을 계속합니다.

* Azure Portal > IoT Hub > IoT 디바이스에서 해당 디바이스가 **사용 가능** 상태인지 확인합니다.

* 디바이스가 MQTT 프로토콜을 사용하는 경우 포트 8883이 열려 있는지 확인합니다. 자세한 내용은 [IoT Hub(MQTT)에 연결](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조세요.

* [Azure IoT Hub에 대한 Microsoft Q&A 질문 페이지](/answers/topics/azure-iot-hub.html), [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-iot-hub) 또는 [Azure 지원](https://azure.microsoft.com/support/options/)에서 도움을 받으세요.

이 가이드가 도움이 되지 않는 경우 모두를 위해 설명서 개선에 도움이 되도록 피드백 섹션에 의견을 남겨 주세요.

## <a name="next-steps"></a>다음 단계

* 일시적인 문제 해결에 대한 자세한 내용은 [일시적인 오류 처리](/azure/architecture/best-practices/transient-faults)를 참조하세요.

* Azure IoT SDK 및 재시도 관리에 대한 자세한 내용은 [Azure IoT Hub 디바이스 SDK를 사용하여 연결 및 신뢰할 수 있는 메시징을 관리하는 방법](iot-hub-reliability-features-in-sdks.md#connection-and-retry)을 참조하세요.
