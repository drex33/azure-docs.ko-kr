---
title: 기본 제공 메트릭 액세스 - Azure IoT Edge
description: IoT Edge 런타임 구성 요소에서 기본 제공 메트릭에 대한 원격 액세스
author: v-tcassi
ms.author: v-tcassi
ms.date: 06/25/2021
ms.topic: conceptual
ms.reviewer: veyalla
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ee4c39b7dfc4097480588620465eedd40eba53f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536503"
---
# <a name="access-built-in-metrics"></a>기본 제공 메트릭 액세스

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge 런타임 구성 요소, IoT Edge 허브, IoT Edge 에이전트는 기본 제공 메트릭을 [Prometheus 표시 형식](https://prometheus.io/docs/instrumenting/exposition_formats/)으로 생성합니다. 해당 메트릭에 원격으로 액세스하여 IoT Edge 디바이스의 상태를 모니터링 및 파악합니다.

자체 솔루션을 사용하여 이러한 메트릭에 액세스할 수 있습니다. 또는 기본 제공 메트릭을 수집하여 Azure Monitor 또는 Azure IoT Hub로 보내는 작업을 처리하는 [메트릭 수집기 모듈](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft_iot_edge.metrics-collector)을 사용할 수 있습니다. 자세한 내용은 [메트릭 수집 및 전송](how-to-collect-and-transport-metrics.md)을 참조하세요.

릴리스 1.0.10부터, 메트릭은 기본적으로 **edgeHub** 및 **edgeAgent** 모듈(`http://edgeHub:9600/metrics` 및 `http://edgeAgent:9600/metrics`)의 **포트 9600** 에서 자동으로 노출됩니다. 기본적으로 호스트에 매핑된 포트는 아닙니다.

모듈의 `createOptions`에서 메트릭 포트를 노출 및 매핑하여 호스트에서 메트릭에 액세스합니다. 아래 예제에서는 기본 메트릭 포트를 호스트의 포트 9601에 매핑합니다.

```json
{
  "ExposedPorts": {
    "9600/tcp": {}
  },
  "HostConfig": {
    "PortBindings": {
      "9600/tcp": [
        {
          "HostPort": "9601"
        }
      ]
    }
  }
}
```

EdgeHub 및 edgeAgent의 메트릭 엔드포인트를 모두 매핑하는 경우에는 서로 다른 고유한 호스트 포트 번호를 선택합니다.

> [!NOTE]
> 컬렉션에 사용할 수 있는 기본 제공 메트릭에 환경 변수 `httpSettings__enabled`를 `false`로 설정하면 안 됩니다.
>
> 메트릭을 사용 중지하는 데 사용할 수 있는 환경 변수는 [azure/iotedge 리포지토리 문서](https://github.com/Azure/iotedge/blob/master/doc/EnvironmentVariables.md)를 참조하세요.

## <a name="available-metrics"></a>사용 가능한 메트릭

메트릭은 수집되는 메트릭의 특성을 식별하는 데 도움이 되는 태그를 포함합니다. 모든 메트릭에는 다음 태그가 포함됩니다.

| 태그 | 설명 |
|-|-|
| iothub | 디바이스가 통신하고 있는 허브 |
| edge_device | 현재 디바이스의 ID |
| instance_number | 현재 런타임을 나타내는 GUID. 다시 시작 시 모든 메트릭이 초기화됩니다. GUID를 사용하면 다시 시작을 보다 쉽게 조정할 수 있습니다. |

Prometheus 표시 형식에는 카운터, 계기, 히스토그램, 요약의 네 가지 핵심 메트릭이 있습니다. 여러 메트릭 유형에 대한 자세한 내용은 [Prometheus 메트릭 유형 문서](https://prometheus.io/docs/concepts/metric_types/)를 참조하세요.

기본 제공 히스토그램 및 요약 메트릭에 제공되는 분위수는 0.1, 0.5, 0.9, 0.99입니다.

**EdgeHub** 모듈은 다음 메트릭을 생성합니다.

| 이름 | 차원 | Description |
|-|-|-|
| `edgehub_gettwin_total` | `source`(작업 소스)<br> `id`(모듈 ID) | 유형: 카운터<br> GetTwin 호출의 총 횟수 |
| `edgehub_messages_received_total` | `route_output`(메시지를 전송한 출력)<br> `id` | 유형: 카운터<br> 고객으로부터 수신한 총 메시지 개수입니다. |
| `edgehub_messages_sent_total` | `from`(메시지 소스)<br> `to`(메시지 대상)<br>`from_route_output`<br> `to_route_input`(메시지 대상 입력)<br> `priority`(대상에 대한 메시지 우선 순위) | 유형: 카운터<br> 클라이언트 또는 업스트림에 전송된 총 메시지 개수입니다.<br> `to`가 $upstream이면 `to_route_input`은 비어 있습니다. |
| `edgehub_reported_properties_total` | `target`(업데이트 대상)<br> `id` | 유형: 카운터<br> 보고된 총 속성 업데이트 호출 |
| `edgehub_message_size_bytes` | `id`<br> | 유형: 요약<br> 클라이언트 메시지 크기<br> 특정 시간 동안(현재 10분) 새 측정이 보고되지 않는 경우 값은 `NaN`으로 보고될 수 있습니다. `summary` 형식의 경우, 해당 `_count` 및 `_sum` 카운터를 내보냅니다. |
| `edgehub_gettwin_duration_seconds` | `source` <br> `id` | 유형: 요약<br> 트윈 가져오기 작업에 소요된 시간 |
| `edgehub_message_send_duration_seconds` | `from`<br> `to`<br> `from_route_output`<br> `to_route_input` | 유형: 요약<br> 메시지를 전송하는 데 소요된 시간 |
| `edgehub_message_process_duration_seconds` | `from` <br> `to` <br> `priority` | 유형: 요약<br> 큐에서 메시지를 처리하는 데 소요된 시간 |
| `edgehub_reported_properties_update_duration_seconds` | `target`<br> `id` | 유형: 요약<br> 보고된 속성을 업데이트하는 데 소요된 시간 |
| `edgehub_direct_method_duration_seconds` | `from`(호출자)<br> `to`(수신자) | 유형: 요약<br> 다이렉트 메시지를 처리하는 데 소요된 시간 |
| `edgehub_direct_methods_total` | `from`<br> `to` | 유형: 카운터<br> 전송된 다이렉트 메시지의 총 개수 |
| `edgehub_queue_length` | `endpoint`(메시지 소스)<br> `priority`(큐 우선 순위) | 유형: 계기<br> 지정된 우선 순위의 edgeHub 큐의 현재 길이 |
| `edgehub_messages_dropped_total` | `reason`(no_route, ttl_expiry)<br> `from` <br> `from_route_output` | 유형: 카운터<br> 어떤 사유로 인해 삭제된 총 메시지 개수 |
| `edgehub_messages_unack_total` | `reason`(storage_failure)<br> `from`<br> `from_route_output` | 유형: 카운터<br> 스토리지 오류로 인해 수신되지 않은 총 메시지 개수 |
| `edgehub_offline_count_total` | `id` | 유형: 카운터<br> edgeHub 오프라인 상태가 된 총 횟수 |
| `edgehub_offline_duration_seconds`| `id` | 유형: 요약<br> 에지 허브가 오프라인 상태였던 시간 |
| `edgehub_operation_retry_total` | `id`<br> `operation`(작업 이름) | 유형: 카운터<br> edgeHub 작업을 다시 시도한 총 횟수 |
| `edgehub_client_connect_failed_total` | `id` <br> `reason`(인증되지 않음)<br> | 유형: 카운터<br> 클라이언트에서 edgeHub 연결에 실패한 총 횟수 |

**edgeAgent** 모듈은 다음 메트릭을 생성합니다.

| 이름 | 차원 | Description |
|-|-|-|
| `edgeAgent_total_time_running_correctly_seconds` | `module_name` | 유형: 계기<br> 모듈이 배포에 지정되고 실행 중 상태였던 시간 |
| `edgeAgent_total_time_expected_running_seconds` | `module_name` | 유형: 계기<br> 배포에서 모듈이 지정된 시간 |
| `edgeAgent_module_start_total` | `module_name`, `module_version` | 유형: 카운터<br> edgeAgent에서 Docker에 모듈 시작을 요청한 횟수 |
| `edgeAgent_module_stop_total` | `module_name`, `module_version` | 유형: 카운터<br> edgeAgent이 Docker에 모듈 중지를 요청한 횟수 |
| `edgeAgent_command_latency_seconds` | `command` | 유형: 계기<br> Docker에서 명령을 실행하는 데 소요된 시간. 가능한 명령은 만들기, 업데이트, 제거, 시작, 중지, 다시 시작입니다. |
| `edgeAgent_iothub_syncs_total` | | 유형: 카운터<br> edgeAgent가 iotHub와 트윈 동기화를 시도한 횟수(성공 및 실패 포함). 에이전트가 트윈 및 허브에 트윈 업데이트에 대한 알림을 요청한 횟수도 포함됩니다. |
| `edgeAgent_unsuccessful_iothub_syncs_total` | | 유형: 카운터<br> edgeAgent에서 iotHub와 트윈 동기화에 실패한 횟수 |
| `edgeAgent_deployment_time_seconds` | | 유형: 카운터<br> 변경 사항을 수신한 후 새 배포를 완료하는 데 소요된 시간 |
| `edgeagent_direct_method_invocations_count` | `method_name` | 유형: 카운터<br> Ping 또는 Restart와 같이 기본 제공 edgeAgent 직접 메서드가 호출되는 횟수 |
| `edgeAgent_host_uptime_seconds` | | 유형: 계기<br> 호스트가 작동된 시간 |
| `edgeAgent_iotedged_uptime_seconds` | | 유형: 계기<br> iotedged가 실행된 시간 |
| `edgeAgent_available_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype` | 유형: 계기<br> 디스크에 남아 있는 공간의 크기 |
| `edgeAgent_total_disk_space_bytes` | `disk_name`, `disk_filesystem`, `disk_filetype`| 유형: 계기<br> 디스크 크기 |
| `edgeAgent_used_memory_bytes` | `module_name` | 유형: 계기<br> 모든 프로세스에서 사용하는 RAM의 양 |
| `edgeAgent_total_memory_bytes` | `module_name` | 유형: 계기<br> 사용 가능한 RAM |
| `edgeAgent_used_cpu_percent` | `module_name` | 형식: 히스토그램<br> 프로세스에서 사용하는 CPU의 비율 |
| `edgeAgent_created_pids_total` | `module_name` | 유형: 계기<br> 컨테이너가 생성한 프로세스 또는 스레드 개수 |
| `edgeAgent_total_network_in_bytes` | `module_name` | 유형: 계기<br> 네트워크에서 수신한 바이트 수 |
| `edgeAgent_total_network_out_bytes` | `module_name` | 유형: 계기<br> 네트워크에 전송된 바이트 수 |
| `edgeAgent_total_disk_read_bytes` | `module_name` | 유형: 계기<br> 디스크에서 읽어온 바이트 수 |
| `edgeAgent_total_disk_write_bytes` | `module_name` | 유형: 계기<br> 디스크에 작성된 바이트 수 |
| `edgeAgent_metadata` | `edge_agent_version`, `experimental_features`, `host_information` | 유형: 계기<br> 디바이스에 대한 일반 메타데이터. 값은 항상 0이며, 정보는 태그에서 인코딩됩니다. 참고 `experimental_features` 및 `host_information`은 json 개체입니다. `host_information`은 ```{"OperatingSystemType": "linux", "Architecture": "x86_64", "Version": "1.0.10~dev20200803.4", "Provisioning": {"Type": "dps.tpm", "DynamicReprovisioning": false, "AlwaysReprovisionOnStartup": true}, "ServerVersion": "19.03.6", "KernelVersion": "5.0.0-25-generic", "OperatingSystem": "Ubuntu 18.04.4 LTS", "NumCpus": 6, "Virtualized": "yes"}``` 형식입니다. 참고 `ServerVersion`은 Docker 버전이며 `Version` IoT Edge 보안 디먼 버전입니다. |

## <a name="next-steps"></a>다음 단계

* [메트릭 수집 및 전송](how-to-collect-and-transport-metrics.md)
* [Azure IoT Edge 런타임 및 해당 아키텍처 이해](iot-edge-runtime.md)
* [IoT Edge 에이전트 및 IoT Edge 허브 모듈 쌍의 속성](module-edgeagent-edgehub.md)
