---
title: 메트릭 수집 및 전송 - Azure IoT Edge
description: Azure Monitor를 사용하여 IoT Edge 기본 제공 메트릭을 원격으로 모니터링
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 60a01c9e3a3e8643ad7d993db34d299fdc5ef145
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537422"
---
# <a name="collect-and-transport-metrics-preview"></a>메트릭 수집 및 전송(미리 보기)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Azure Monitor 및 기본 제공 메트릭 통합을 사용하여 IoT Edge 집합을 원격으로 모니터링할 수 있습니다. 디바이스에서 이 기능을 사용하도록 설정하려면 배포에 메트릭 수집기 모듈을 추가하고 모듈 메트릭을 수집하고 Azure Monitor에 전송하도록 구성합니다.

## <a name="architecture"></a>Architecture

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

[![IoT Hub를 사용하는 메트릭 모니터링 아키텍처](./media/how-to-collect-and-transport-metrics/arch.png)](./media/how-to-collect-and-transport-metrics/arch.png#lightbox)

| 참고 | Description |
|-|-|
|  1 | 모든 모듈은 [Prometheus 데이터 모델](https://prometheus.io/docs/concepts/data_model/)을 사용하여 메트릭을 내보내야 합니다. [기본 제공 메트릭](how-to-access-built-in-metrics.md)은 기본적으로 광범위한 워크로드 가시성을 지원하지만 사용자 지정 모듈을 통해 시나리오별 메트릭을 내보내 모니터링 솔루션을 향상시킬 수도 있습니다. [사용자 지정 메트릭 추가](how-to-add-custom-metrics.md) 문서에서 오픈 소스 라이브러리를 사용하여 사용자 지정 모듈을 계측하는 방법을 알아봅니다. |
|  2️ | [메트릭 수집기 모듈](https://aka.ms/edgemon-metrics-collector)은 워크로드 모듈 메트릭을 수집한 후 오프디바이스로 전송하는 Microsoft 제공 IoT Edge 모듈입니다. 메트릭 컬렉션은 *끌어오기* 모델을 사용합니다. 모듈에서 송신된 데이터를 제어하도록 컬렉션 빈도, 엔드포인트 및 필터를 구성할 수 있습니다. 자세한 내용은 이 문서 뒷부분에 나오는 [메트릭 수집기 구성 섹션](#metrics-collector-configuration)을 참조하세요. |
|  3️ | 메트릭 수집기 모듈에서 클라우드로 메트릭을 전송하는 두 가지 옵션이 있습니다. *옵션 1* 은 메트릭을 Log Analytics로 보냅니다.<sup>1</sup> 수집된 메트릭은 `InsightsMetrics`라는 고정된 네이티브 테이블을 사용하여 지정된 Log Analytics 작업 영역으로 수집됩니다. 이 테이블의 스키마는 Prometheus 메트릭 데이터 모델과 호환됩니다.<br><br> 이 옵션을 사용하려면 아웃바운드 포트 443의 작업 영역에 액세스해야 합니다. Log Analytics 작업 영역 ID 및 키를 모듈 구성의 일부로 지정해야 합니다. 제한된 네트워크에서 사용하도록 설정하려면 이 문서의 뒷부분에 나오는 [제한 된 네트워크 액세스 시나리오에서 사용](#enable-in-restricted-network-access-scenarios)을 참조하세요.
|  4️ | 각 메트릭 항목에는 [모듈 구성](#metrics-collector-configuration)의 일부로 지정된 `ResourceId`가 포함됩니다. 이 연결은 메트릭을 지정된 리소스(예: IoT Hub)와 자동으로 연결합니다. 결과적으로 [큐레이팅된 IoT Edge 통합 문서 템플릿](how-to-explore-curated-visualizations.md)은 리소스에 대해 쿼리를 실행하여 메트릭을 검색할 수 있습니다. <br><br> 또한 이 방법을 사용하면 여러 IoT Hub가 단일 Log Analytics 작업 영역을 메트릭 데이터베이스로 안전하게 공유할 수 있습니다. |
|  5️ | *옵션 2* 는 메트릭을 IoT Hub로 보냅니다.<sup>1</sup> 수집기 모듈은 수집된 메트릭을 `edgeHub` 모듈을 통해 UTF-8로 인코딩된 JSON [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md)로 전송하도록 구성할 수 있습니다. 이 옵션은 IoT Hub 엔드포인트에 대한 외부 액세스만 허용하는 잠긴 IoT Edge 디바이스의 모니터링을 잠금 해제합니다. 또한 자식 디바이스가 부모 디바이스에만 액세스할 수 있는 중첩된 구성에서 자식 IoT Edge 디바이스를 모니터링할 수 있습니다. |
|  6️ | 메트릭이 IoT Hub를 통해 라우팅되는 경우 (일회성) 클라우드 워크플로를 설정해야 합니다. 워크플로는 메트릭 수집기 모듈에서 도착하는 메시지를 처리하고 Log Analytics 작업 영역으로 보냅니다. 이 워크플로에서는 이 선택적 경로를 통해 도착하는 메트릭에 대해서도 [큐레이팅된 시각화](how-to-explore-curated-visualizations.md) 및 [경고](how-to-create-alerts.md) 기능을 사용할 수 있도록 합니다. 이 클라우드 워크플로를 설정하는 방법에 대한 자세한 내용은 [IoT Hub 통해 메트릭 라우팅](#route-metrics) 섹션을 참조하세요. |

<sup>1</sup> 현재 *옵션 1* 을 사용하여 IoT Edge 디바이스에서 Log Analytics로 메트릭을 직접 전송하는 것은 최소한의 설정이 필요한 좀 더 간단한 경로입니다. 특정 시나리오에서 IoT Edge 디바이스가 IoT Hub 통신할 수 있도록 *옵션 2* 접근 방식을 요구하지 않는 한, 첫 번째 옵션이 선호됩니다.

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

[![IoT Central을 사용하는 메트릭 모니터링 아키텍처](./media/how-to-collect-and-transport-metrics/arch-iot-central.png)](./media/how-to-collect-and-transport-metrics/arch-iot-central.png#lightbox)

| 참고 | Description |
|-|-|
|  1 | 모든 모듈은 [Prometheus 데이터 모델](https://prometheus.io/docs/concepts/data_model/)을 사용하여 메트릭을 내보내야 합니다. [기본 제공 메트릭](how-to-access-built-in-metrics.md)은 기본적으로 광범위한 워크로드 가시성을 지원하지만 사용자 지정 모듈을 통해 시나리오별 메트릭을 내보내 모니터링 솔루션을 향상시킬 수도 있습니다. [사용자 지정 메트릭 추가](how-to-add-custom-metrics.md) 문서에서 오픈 소스 라이브러리를 사용하여 사용자 지정 모듈을 계측하는 방법을 알아봅니다. |
|  2️ | [메트릭 수집기 모듈](https://aka.ms/edgemon-metrics-collector)은 워크로드 모듈 메트릭을 수집한 후 오프디바이스로 전송하는 Microsoft 제공 IoT Edge 모듈입니다. 메트릭 컬렉션은 *끌어오기* 모델을 사용합니다. 모듈에서 송신된 데이터를 제어하도록 컬렉션 빈도, 엔드포인트 및 필터를 구성할 수 있습니다. 자세한 내용은 이 문서 뒷부분에 나오는 [메트릭 수집기 구성 섹션](#metrics-collector-configuration)을 참조하세요. |
|  3️ | 메트릭 수집기 모듈에서 클라우드로 메트릭을 전송하는 두 가지 옵션이 있습니다. ‘옵션 1’은 Log Analytics에 메트릭을 전송합니다. 수집한 메트릭은 `InsightsMetrics`라는 고정된 네이티브 테이블을 사용하여 지정된 Log Analytics 작업 영역으로 수집됩니다. 이 테이블의 스키마는 Prometheus 메트릭 데이터 모델과 호환됩니다.<br><br> 이 옵션을 사용하려면 아웃바운드 포트 443의 작업 영역에 액세스해야 합니다. Log Analytics 작업 영역 ID 및 키를 모듈 구성의 일부로 지정해야 합니다. 제한된 네트워크에서 사용하도록 설정하려면 이 문서의 뒷부분에 나오는 [제한 된 네트워크 액세스 시나리오에서 사용](#enable-in-restricted-network-access-scenarios)을 참조하세요.
|  4️ | 각 메트릭 항목에는 [모듈 구성](#metrics-collector-configuration)의 일부로 지정된 `ResourceId`가 포함됩니다. 이 연결은 지정한 리소스(예: IoT Central)에 메트릭을 자동으로 연결합니다. 결과적으로 [큐레이팅된 IoT Edge 통합 문서 템플릿](how-to-explore-curated-visualizations.md)은 리소스에 대해 쿼리를 실행하여 메트릭을 검색할 수 있습니다. <br><br> 또한 이 방법을 사용하면 여러 IoT Central 애플리케이션이 단일 Log Analytics 작업 영역을 메트릭 데이터베이스로 안전하게 공유할 수 있습니다. |
|  5️ | ‘옵션 2’는 IoT Central에 메트릭을 전송합니다. 이 옵션을 사용하면 운영자가 단일 위치에서 메트릭과 디바이스 원격 분석을 볼 수 있습니다. `edgeHub` 모듈을 통해 수집한 메트릭을 UTF-8로 인코딩된 JSON [디바이스-클라우드 메시지](../iot-hub/iot-hub-devguide-messages-d2c.md)로 전송하도록 수집기 모듈을 구성할 수 있습니다. 이 옵션은 IoT Central 엔드포인트에 대한 외부 액세스만 허용되는 잠긴 IoT Edge 디바이스의 모니터링을 잠금 해제합니다. 또한 자식 디바이스가 부모 디바이스에만 액세스할 수 있는 중첩된 구성에서 자식 IoT Edge 디바이스를 모니터링할 수 있습니다. |

---

## <a name="metrics-collector-module"></a>메트릭 수집기 모듈

Microsoft에서 제공하는 메트릭 수집기 모듈을 IoT Edge 배포에 추가하여 모듈 메트릭을 수집한 후 Azure Monitor로 전송할 수 있습니다. 모듈 코드는 오픈 소스이며 [IoT Edge GitHub 리포지토리](https://github.com/Azure/iotedge/tree/release/1.1/edge-modules/azure-monitor)에서 사용할 수 있습니다.

메트릭 수집기 모듈은 Linux X64, ARM32, ARM64 및 Windows X64(버전 1809)를 지원하는 다중 아키텍처 Docker 컨테이너 이미지로 제공됩니다. **[`mcr.microsoft.com/azureiotedge-metrics-collector`](https://aka.ms/edgemon-metrics-collector)** 에서 공개적으로 사용할 수 있습니다.

[IoT Edge 모듈 Marketplace](https://aka.ms/edgemon-module-marketplace)에서도 사용할 수 있습니다.

## <a name="metrics-collector-configuration"></a>메트릭 수집기 구성

메트릭 수집기를 위한 모든 구성은 환경 변수를 사용하여 수행됩니다. 최소한 아래 표에서 **필수** 로 표시된 변수를 지정해야 합니다.

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

| 환경 변수 이름 | Description |
|-|-|
| `ResourceId` | 디바이스가 통신하는 IoT Hub의 리소스 ID입니다. 자세한 내용은 [리소스 ID](#resource-id) 섹션을 참조하세요.  <br><br>  **필수** <br><br> 기본값: *none* |
| `UploadTarget` |  메트릭이 HTTPS를 통해 Azure Monitor로 직접 전송되는지 또는 D2C 메시지로서 IoT Hub에 전송되는지를 제어합니다. 자세한 내용은 [업로드 대상](#upload-target)을 참조하세요. <br><br>**AzureMonitor** 또는 **IoTMessage** 일 수 있습니다.  <br><br>  **필요 없음** <br><br> 기본값: *AzureMonitor* |
| `LogAnalyticsWorkspaceId` | [Log Analytics 작업 영역 ID](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key). <br><br>*UploadTarget* 이 *AzureMonitor* 인 경우에만 **필수** 입니다. <br><br>기본값: *none* |
| `LogAnalyticsSharedKey` | [Log Analytics 작업 영역 키](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key). <br><br>*UploadTarget* 이 *AzureMonitor* 인 경우에만 **필수** 입니다.   <br><br> 기본값: *none* |
| `ScrapeFrequencyInSecs` | 메트릭을 수집하고 전송할 정기 시간 간격(초)입니다.<br><br>  예: *600* <br><br>  **필요 없음** <br><br> 기본값: *300* |
| `MetricsEndpointsCSV` | Prometheus 메트릭을 수집할 쉼표로 구분된 엔드포인트 목록입니다. 메트릭을 수집할 모든 모듈 엔드포인트가 이 목록에 나타나야 합니다.<br><br>  예: *http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics* <br><br>  **필요 없음** <br><br> 기본값: *http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics* |
| `AllowedMetrics` | 수집할 메트릭 목록입니다. 다른 모든 메트릭은 무시됩니다. 사용하지 않도록 설정하려면 빈 문자열로 설정합니다. 자세한 내용은 [허용 및 허용 안 함 목록](#allow-and-disallow-lists)을 참조하세요. <br><br>예: *metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]*<br><br>  **필요 없음** <br><br> 기본값: *비어 있음* |
| `BlockedMetrics` | 무시할 메트릭 목록입니다. *AllowedMetrics* 를 재정의하므로 두 목록에 모두 포함된 메트릭은 보고되지 않습니다. 자세한 내용은 [허용 및 허용 안 함 목록](#allow-and-disallow-lists)을 참조하세요. <br><br>   예: *metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes*<br><br>  **필요 없음**  <br><br>기본값: *비어 있음* |
| `CompressForUpload` | 메트릭을 업로드할 때 압축을 사용할지 여부를 제어합니다. 모든 업로드 대상에 적용됩니다.<br><br>  예: *true* <br><br>    **필요 없음** <br><br>  기본값: *true* |
| `AzureDomain` | Log Analytics로 직접 메트릭을 수집할 때 사용할 최상위 Azure 도메인을 지정합니다. <br><br>  예: *azure.us* <br><br>    **필요 없음** <br><br>  기본값: *azure.com* |

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

| 환경 변수 이름 | Description |
|-|-|
| `ResourceId` | 디바이스가 통신하는 IoT Central 애플리케이션의 리소스 ID입니다. 자세한 내용은 [리소스 ID](#resource-id) 섹션을 참조하세요.  <br><br>  **필수** <br><br> 기본값: *none* |
| `UploadTarget` |  메트릭이 HTTPS를 통해 Azure Monitor에 직접 전송되는지 또는 D2C 메시지로 IoT Central에 전송되는지를 제어합니다. 자세한 내용은 [업로드 대상](#upload-target)을 참조하세요. <br><br>**AzureMonitor** 또는 **IoTMessage** 일 수 있습니다.  <br><br>  **필요 없음** <br><br> 기본값: *AzureMonitor* |
| `LogAnalyticsWorkspaceId` | [Log Analytics 작업 영역 ID](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key). <br><br>*UploadTarget* 이 *AzureMonitor* 인 경우에만 **필수** 입니다. <br><br>기본값: *none* |
| `LogAnalyticsSharedKey` | [Log Analytics 작업 영역 키](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key). <br><br>*UploadTarget* 이 *AzureMonitor* 인 경우에만 **필수** 입니다.   <br><br> 기본값: *none* |
| `ScrapeFrequencyInSecs` | 메트릭을 수집하고 전송할 정기 시간 간격(초)입니다.<br><br>  예: *600* <br><br>  **필요 없음** <br><br> 기본값: *300* |
| `MetricsEndpointsCSV` | Prometheus 메트릭을 수집할 쉼표로 구분된 엔드포인트 목록입니다. 메트릭을 수집할 모든 모듈 엔드포인트가 이 목록에 나타나야 합니다.<br><br>  예: *http://edgeAgent:9600/metrics , http://edgeHub:9600/metrics, http://MetricsSpewer:9417/metrics* <br><br>  **필요 없음** <br><br> 기본값: *http://edgeHub:9600/metrics , http://edgeAgent:9600/metrics* |
| `AllowedMetrics` | 수집할 메트릭 목록입니다. 다른 모든 메트릭은 무시됩니다. 사용하지 않도록 설정하려면 빈 문자열로 설정합니다. 자세한 내용은 [허용 및 허용 안 함 목록](#allow-and-disallow-lists)을 참조하세요. <br><br>예: *metricToScrape{quantile=0.99}[endpoint=http://MetricsSpewer:9417/metrics ]*<br><br>  **필요 없음** <br><br> 기본값: *비어 있음* |
| `BlockedMetrics` | 무시할 메트릭 목록입니다. *AllowedMetrics* 를 재정의하므로 두 목록에 모두 포함된 메트릭은 보고되지 않습니다. 자세한 내용은 [허용 및 허용 안 함 목록](#allow-and-disallow-lists)을 참조하세요. <br><br>   예: *metricToIgnore{quantile=0.5}[endpoint=http://VeryNoisyModule:9001/metrics ], docker_container_disk_write_bytes*<br><br>  **필요 없음**  <br><br>기본값: *비어 있음* |
| `CompressForUpload` | 메트릭을 업로드할 때 압축을 사용할지 여부를 제어합니다. 모든 업로드 대상에 적용됩니다.<br><br>  예: *true* <br><br>    **필요 없음** <br><br>  기본값: *true* |
| `AzureDomain` | Log Analytics로 직접 메트릭을 수집할 때 사용할 최상위 Azure 도메인을 지정합니다. <br><br>  예: *azure.us* <br><br>    **필요 없음** <br><br>  기본값: *azure.com* |

IoT Edge와 IoT Central에 대한 자세한 내용은 [Azure IoT Central 애플리케이션에 Azure IoT Edge 디바이스 연결](../iot-central/core/concepts-iot-edge.md)을 참조하세요.

---

### <a name="resource-id"></a>리소스 ID

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

메트릭 수집기 모듈에는 IoT Edge 디바이스가 속하는 IoT Hub의 Azure Resource Manager ID가 필요합니다. 이 ID를 **ResourceID** 환경 변수의 값으로 제공합니다.

리소스 ID의 형식은 다음과 같습니다.

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Devices/IoTHubs/<iot hub name>
```

리소스 ID는 Azure Portal에 있는 IoT Hub의 **속성** 페이지에서 찾을 수 있습니다.

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id.png" alt-text="IoT Hub 속성에서 리소스 ID 검색":::

또는 [az resource show](/cli/azure/resource#az_resource_show) 명령을 사용하여 ID를 검색합니다.

```azurecli-interactive
az resource show -g <resource group> -n <hub name> --resource-type "Microsoft.Devices/IoTHubs"
```

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

메트릭 수집기 모듈에는 IoT Edge 디바이스가 속하는 IoT Central 애플리케이션의 Azure Resource Manager ID가 필요합니다. 이 ID를 **ResourceID** 환경 변수의 값으로 제공합니다.

리소스 ID의 형식은 다음과 같습니다.

```input
/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.IoTCentral/IoTApps/<iot central app name>
```

리소스 ID는 Azure Portal에 있는 IoT Central 애플리케이션의 **속성** 페이지에서 찾을 수 있습니다.

:::image type="content" source="./media/how-to-collect-and-transport-metrics/resource-id-iot-central.png" alt-text="IoT Central 속성에서 리소스 ID 검색":::

또는 [az resource show](/cli/azure/resource#az_resource_show) 명령을 사용하여 ID를 검색합니다.

```azurecli-interactive
az resource show -g <resource group> -n <application name> --resource-type "Microsoft.IoTCentral/IoTApps"
```

---

### <a name="upload-target"></a>업로드 대상

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

**UploadTarget** 구성 옵션은 메트릭을 Azure Monitor로 직접 보낼지 또는 IoT Hub로 보낼지를 제어합니다.

**UploadTarget** 을 **IoTMessage** 로 설정하면 모듈 메트릭이 IoT 메시지로 게시됩니다. 이러한 메시지는 엔드포인트 `/messages/modules/<module name>/outputs/metricOutput`에서 UTF8로 인코딩된 json으로 내보내집니다. 형식은 다음과 같습니다.

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

**UploadTarget** 구성 옵션은 메트릭이 Azure Monitor에 직접 전송되는지 또는 IoT Central에 전송되는지를 제어합니다.

**UploadTarget** 을 **IoTMessage** 로 설정하면 모듈 메트릭이 IoT 메시지로 게시됩니다. 이러한 메시지는 엔드포인트 `/messages/modules/<module name>/outputs/metricOutput`에서 UTF8로 인코딩된 json으로 내보내집니다. 형식은 다음과 같습니다.

```json
[{
    "TimeGeneratedUtc": "<time generated>",
    "Name": "<prometheus metric name>",
    "Value": <decimal value>,
    "Label": {
        "<label name>": "<label value>"
    }
}, {
    "TimeGeneratedUtc": "2020-07-28T20:00:43.2770247Z",
    "Name": "docker_container_disk_write_bytes",
    "Value": 0.0,
    "Label": {
        "name": "AzureMonitorForIotEdgeModule"
    }
}]
```

---

### <a name="allow-and-disallow-lists"></a>허용 및 허용 안 함 목록

`AllowedMetrics` 및 `BlockedMetrics` 구성 옵션은 공백 또는 쉼표로 구분된 메트릭 선택기 목록을 사용합니다. 메트릭은 목록과 일치하고, 두 목록에서 하나 이상의 메트릭과 일치하는 경우 포함되거나 제외됩니다.

메트릭 선택기는 [PromQL](https://prometheus.io/docs/prometheus/latest/querying/basics/) 쿼리 언어의 하위 집합과 비슷한 형식을 사용합니다.

```query
metricToSelect{quantile=0.5,otherLabel=~Re[ge]*|x}[http://VeryNoisyModule:9001/metrics]
```

메트릭 선택기는 다음 세 부분으로 구성 됩니다.

메트릭 이름(`metricToSelect`).

* 와일드카드 `*`(모든 문자) 및 `?`(단일 문자)를 메트릭 이름에 사용할 수 있습니다. 예를 들어, `*CPU`는 `maxCPU` 및 `minCPU`와 일치하지만 `CPUMaximum`과는 일치하지 않습니다. `???CPU`는 `maxCPU` 및 `minCPU`와 일치하지만 `maximumCPU`와는 일치하지 않습니다.
* 이 구성 요소는 메트릭 선택기에 필요합니다.

레이블 기반 선택기(`{quantile=0.5,otherLabel=~Re[ge]*|x}`)

* 여러 메트릭 값은 중괄호로 묶어 포함할 수 있습니다. 값은 쉼표로 구분해야 합니다.
* 선택기에 있는 모든 레이블이 존재하고 일치하는 경우에 메트릭이 일치됩니다.
* PromQL과 마찬가지로 다음과 같은 일치 연산자가 허용됩니다.
  * `=` 제공된 문자열과 정확히 일치하는(대/소문자 구분) 레이블을 검색합니다.
  * `!=` 제공된 문자열과 정확히 일치하지 않는 레이블을 검색합니다.
  * `=~` 제공된 정규식과 일치하는 레이블을 검색합니다. 예: `label=~CPU|Mem|[0-9]*`
  * `!=` 제공된 정규식과 맞지 않는 레이블을 검색합니다.
  * 정규식은 완전히 고정됩니다(^ 및 $는 각 정규식의 시작과 끝에 자동으로 추가됨).
  * 이 구성 요소는 메트릭 선택기에서 선택 사항입니다.

엔드포인트 선택기(`[http://VeryNoisyModule:9001/metrics]`)

* URL은 `MetricsEndpointsCSV`에 나열된 URL과 정확히 일치해야 합니다.
* 이 구성 요소는 메트릭 선택기에서 선택 사항입니다.

메트릭은 지정된 선택기의 모든 부분과 일치해야 선택할 수 있습니다. 이름과 일치하고, , 모든 동일한 태그가 일치하는 값을 가지며, 지정된 엔드포인트에서 가져온 것이어야 합니다. 예를 들어 `mem{quantile=0.5,otherLabel=foobar}[http://VeryNoisyModule:9001/metrics]`는 `mem{quantile=0.5,otherLabel=~foo|bar}[http://VeryNoisyModule:9001/metrics]` 선택기와 일치하지 않습니다. 여러 선택기를 사용하여 and 유사 동작 대신 or 유사 동작을 만들어야 합니다.

예를 들어, 모듈 `module1`(태그)의 메트릭 `mem`을 허용할 뿐만 아니라 태그 `agg=p99`가 지정된 `module2`의 동일한 메트릭을 허용하려면 `AllowedMetrics`에 다음 선택기를 추가할 수 있습니다.

```query
mem{}[http://module1:9001/metrics] mem{agg="p99"}[http://module2:9001/metrics]
```

또는 메트릭 `mem` 및 `cpu`를 허용하려면(태그 또는 엔드포인트) 다음을 `AllowedMetrics`에 추가합니다.

```query
mem cpu
```

## <a name="enable-in-restricted-network-access-scenarios"></a>제한된 네트워크 액세스 시나리오에서 사용

Log Analytics 작업 영역으로 메트릭을 직접 전송하는 경우 다음 URL에 대한 아웃바운드 액세스를 허용합니다.

* `https://<LOG_ANALYTICS_WORKSPACE_ID>.ods.opinsights.azure.com/*`
* `https://<LOG_ANALYTICS_WORKSPACE_ID>.oms.opinsights.azure.com/*`

### <a name="proxy-considerations"></a>프록시 고려 사항

메트릭-수집기 모듈은 .NET Core로 작성됩니다. 따라서 시스템 모듈에 대해 동일한 지침을 사용하여 [프록시 서버를 통한 통신을 허용](how-to-configure-proxy-support.md#configure-deployment-manifests)합니다.

로컬 모듈의 메트릭 컬렉션은 http 프로토콜을 사용합니다. `NO_PROXY` 환경 변수를 설정하여 로컬 통신이 프록시 서버를 통과하지 않도록 합니다.

`NO_PROXY` 값을 제외해야 하는 쉼표로 구분된 호스트 이름 목록으로 설정합니다. 호스트 이름에 모듈 이름을 사용합니다. 예: *edgeHub,edgeAgent,myCustomModule*.

## <a name="route-metrics"></a>경로 메트릭

# <a name="iot-hub"></a>[IoT Hub](#tab/iothub)

Log Analytics에 직접 전송하는 대신 IoT Hub를 통해 메트릭을 수집해야 하는 경우도 있습니다. 자식 디바이스에서 부모 디바이스의 IoT Edge 허브에만 액세스할 수 있는 [중첩된 구성의 IoT Edge 디바이스](tutorial-nested-iot-edge.md)를 모니터링하는 경우를 예로 들 수 있습니다. 또 다른 예로는 아웃바운드 네트워크 액세스 권한이 있는 IoT Edge 디바이스를 IoT Hub에 배포하는 경우가 있습니다.

이 시나리오에서 모니터링을 사용하도록 설정하려면 edgeHub 모듈을 통해 메트릭을 D2C(디바이스-클라우드) 메시지로 보내도록 메트릭 수집기 모듈을 구성할 수 있습니다. 수집기 [구성](#metrics-collector-configuration)에서 `UploadTarget` 환경 변수를 `IoTMessage`로 설정하여 이 기능을 켤 수 있습니다.

>[!TIP]
>수집기 모듈에서 IoT Hub로 메트릭 메시지를 전달하려면 edgeHub 경로를 추가해야 합니다. `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream` 형식입니다.

이 옵션을 사용하려면 IoT Hub에 도착하는 메트릭 메시지를 Log Analytics 작업 영역에 전달하는 [추가 설정](how-to-collect-and-transport-metrics.md#sample-cloud-workflow)이 필요합니다. 설정이 없으면 [큐레이팅된 시각화](how-to-explore-curated-visualizations.md), [경고](how-to-create-alerts.md) 등의 다른 통합 부분이 작동하지 않습니다.

>[!NOTE]
>이 옵션을 사용하면 추가 비용을 파악할 수 있습니다. 메트릭 메시지는 IoT Hub 메시지 할당량에 따라 계산됩니다. 또한 Log Analytics 수집 및 클라우드 워크플로 리소스에 대해 요금이 청구됩니다.

### <a name="sample-cloud-workflow"></a>샘플 클라우드 워크플로

IoT Hub에서 Log Analytics로 메트릭 메시지를 배달하는 클라우드 워크플로는 [IoT Edge 로깅 및 모니터링 샘플](https://github.com/Azure-Samples/iotedge-logging-and-monitoring-solution#monitoring-architecture-reference)의 일부로 제공됩니다. 이 샘플을 기존 클라우드 리소스에 배포하거나 프로덕션 배포 참조로 사용할 수 있습니다.

# <a name="iot-central"></a>[IoT Central](#tab/iotcentral)

Log Analytics에 직접 전송하는 대신 IoT Central을 통해 메트릭을 수집해야 하는 경우도 있습니다. 자식 디바이스에서 부모 디바이스의 IoT Edge 허브에만 액세스할 수 있는 [중첩된 구성의 IoT Edge 디바이스](tutorial-nested-iot-edge.md)를 모니터링하는 경우를 예로 들 수 있습니다. 또 다른 예로 아웃바운드 네트워크 액세스 권한이 있는 IoT Edge 디바이스를 IoT Central에 배포하는 경우가 있습니다.

이 시나리오에서 모니터링을 사용하도록 설정하려면 edgeHub 모듈을 통해 메트릭을 D2C(디바이스-클라우드) 메시지로 보내도록 메트릭 수집기 모듈을 구성할 수 있습니다. 수집기 [구성](#metrics-collector-configuration)에서 `UploadTarget` 환경 변수를 `IoTMessage`로 설정하여 이 기능을 켤 수 있습니다.

다음 예제 배포 매니페스트는 구성을 보여 줍니다.

```json
{
  "modulesContent": {
    "$edgeAgent": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "runtime": {
          "type": "docker",
          "settings": {
            "minDockerVersion": "v1.25",
            "loggingOptions": "",
            "registryCredentials": {}
          }
        },
        "systemModules": {
          "edgeAgent": {
            // ...
          },
          "edgeHub": {
            // ...
          }
        },
        "modules": {
          "SimulatedTemperatureSensor": {
            // ...
          },
          "AzureMonitorForIotEdgeModule": {
            "settings": {
                "image": "mcr.microsoft.com/azureiotedge-metrics-collector:1.0",
                "createOptions": "{\"HostConfig\":{\"LogConfig\":{\"Type\":\"json-file\",\"Config\":{\"max-size\":\"4m\",\"max-file\":\"7\"}}}}"
            },
            "type": "docker",
            "env": {
              "UploadTarget": {
                "value": "IotMessage"
              },
              "ResourceId": {
                "value": "/subscriptions/{your subscription id}/IOTC/providers/Microsoft.IoTCentral/IoTApps/{your app name}"
              },
              "MetricsEndpointsCSV": {
                "value": "http://edgeHub:9600/metrics,http://edgeAgent:9600/metrics"
              },
              "ScrapeFrequencyInSecs": {
                "value": "30"
              },
              "AllowedMetrics": {
                "value": ""
              },
              "BlockedMetrics": {
                "value": ""
              },
              "CompressForUpload": {
                "value": "false"
              },
              "TransformForIoTCentral": {
                "value": "true"
              }
            },
            "status": "running",
            "restartPolicy": "always",
            "version": "1.0"
          }
        }
      }
    },
    "$edgeHub": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "routes": {
          "temperatureupload": "FROM /messages/modules/SimulatedTemperatureSensor/outputs/temperatureOutput INTO $upstream",
          "metricupload": "FROM /messages/modules/AzureMonitorForIotEdgeModule/outputs/metricOutput INTO $upstream"
        },
        "storeAndForwardConfiguration": {
          "timeToLiveSecs": 7200
        }
      }
    },
    "SimulatedTemperatureSensor": {
      // ...
    },
    "AzureMonitorForIotEdgeModule": {
      "properties.desired": {
        "schemaVersion": "1.0",
        "scrapeFrequencySecs": 30,
        "metricsFormat": "Json",
        "syncTarget": "IoTHub"
      }
    }
  }
}
```

>[!TIP]
>수집기 모듈에서 IoT Central로 메트릭 메시지를 전달하려면 edgeHub 경로를 추가해야 합니다. `FROM /messages/modules/replace-with-collector-module-name/* INTO $upstream` 형식입니다.

IoT Central 애플리케이션에서 IoT Edge 디바이스의 메트릭을 보려면 다음을 수행합니다.

* **IoT Edge 메트릭 표준 인터페이스** 를 [디바이스 템플릿](../iot-central/core/concepts-device-templates.md)에 상속된 인터페이스로 추가합니다.

    :::image type="content" source="media/how-to-collect-and-transport-metrics/add-metrics-interface.png" alt-text="IoT Edge 메트릭 표준 인터페이스 추가":::

* 인터페이스에서 정의된 원격 분석 값을 사용하여 IoT Edge 디바이스를 모니터링하는 데 필요한 [대시보드](../iot-central/core/howto-manage-dashboards.md)를 빌드합니다.

    :::image type="content" source="media/how-to-collect-and-transport-metrics/iot-edge-metrics-telemetry.png" alt-text="원격 분석으로 제공된 IoT Edge 메트릭":::

>[!NOTE]
>이 옵션을 사용하면 추가 비용을 파악할 수 있습니다. 메트릭 메시지는 IoT Central 메시지 할당량에 계산됩니다.

---

## <a name="next-steps"></a>다음 단계

Azure Monitor에서 사용하도록 설정하는 [큐레이팅된 시각화](how-to-explore-curated-visualizations.md)의 유형에 대해 알아봅니다.
