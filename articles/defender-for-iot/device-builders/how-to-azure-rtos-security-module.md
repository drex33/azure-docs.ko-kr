---
title: Azure RTOS용 Defender-IoT-micro-agent 구성 및 사용자 지정
description: Azure RTOS용 Defender-IoT-micro-agent를 구성하고 사용자 지정하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 11/09/2021
ms.openlocfilehash: dc6e522e1d9453264d92100c6e7e830506d8284b
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132306286"
---
# <a name="configure-and-customize-defender-iot-micro-agent-for-azure-rtos"></a>Azure RTOS용 Defender-IoT-micro-agent 구성 및 사용자 지정

이 문서에서는 네트워크, 대역폭 및 메모리 요구 사항을 충족하기 위해 Azure RTOS 디바이스용 Defender-IoT-micro-agent를 구성하는 방법을 설명합니다.

`netxduo/addons/azure_iot/azure_iot_security_module/configs` 디렉터리에서 `*.dist` 확장명이 있는 대상 배포 파일을 선택해야 합니다.  

CMake 컴파일 환경을 사용하는 경우 선택한 값에 대한 명령줄 매개 변수를 `IOT_SECURITY_MODULE_DIST_TARGET`으로 설정해야 합니다. 예들 들어 `-DIOT_SECURITY_MODULE_DIST_TARGET=RTOS_BASE`입니다.

IAR 또는 다른 비 CMake 컴파일 환경에서는 알려진 포함 경로에 대한 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/` 경로를 추가해야 합니다. 예들 들어 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/RTOS_BASE`입니다.

다음 파일을 사용하여 디바이스 동작을 구성합니다.

**netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/\<target distribution>/asc_config.h**

CMake 컴파일 환경에서는 `netxduo/addons/azure_iot/azure_iot_security_module/configs/<target distribution>.dist` 파일을 편집하여 기본 구성을 변경해야 합니다. 다른 모든 환경에 대해 다음의CMake 형식 `set(ASC_XXX ON)` 또는 다음 파일 `netxduo/addons/azure_iot/azure_iot_security_module/inc/configs/<target distribution>/asc_config.h`를 사용합니다. 예들 들어 `#define ASC_XXX`입니다.

각 구성의 기본 동작은 다음 표에 나와 있습니다. 

## <a name="general"></a>일반

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_SECURITY_MODULE_ID | String | Defender-IoT-micro-agent | 디바이스의 고유 식별자입니다.  |
| SECURITY_MODULE_VERSION_(MAJOR)(MINOR)(PATCH)  | 숫자 | 3.2.1 | 버전입니다. |
| ASC_SECURITY_MODULE_SEND_MESSAGE_RETRY_TIME  | 숫자  | 3 | 실패 후에 Defender-IoT-micro-agent가 보안 메시지를 전송하는 데 걸리는 시간입니다. (초) |
| ASC_SECURITY_MODULE_PENDING_TIME  | 숫자 | 300 | Defender-IoT-micro-agent 보류 시간(초)입니다. 시간이 초과되면 상태가 일시 중단으로 변경됩니다. |

## <a name="collection"></a>컬렉션

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_FIRST_COLLECTION_INTERVAL | 숫자  | 30  | 수집기의 시작 컬렉션 간격 오프셋입니다. 시작하는 동안 여러 디바이스에서 동시에 메시지를 보내지 않도록 하기 위해 시스템의 컬렉션에 값이 추가됩니다.  |
| ASC_HIGH_PRIORITY_INTERVAL | 숫자 | 10 | 수집기 우선 순위가 높은 그룹 간격(초)입니다. |
| ASC_MEDIUM_PRIORITY_INTERVAL | 숫자 | 30 | 수집기 우선 순위가 보통인 그룹 간격(초)입니다. |
| ASC_LOW_PRIORITY_INTERVAL | 숫자 | 145,440  | 수집기 우선 순위가 낮은 그룹 간격(초)입니다. |

#### <a name="collector-network-activity"></a>수집기 네트워크 활동

수집기 네트워크 활동 구성을 사용자 지정하려면 다음을 사용합니다.

| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_COLLECTOR_NETWORK_ACTIVITY_TCP_DISABLED | 부울 | false | `TCP` 네트워크 활동을 필터링합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_UDP_DISABLED | 부울 | false | `UDP` 네트워크 활동 이벤트를 필터링합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ICMP_DISABLED | 부울 | false | `ICMP` 네트워크 활동 이벤트를 필터링합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_CAPTURE_UNICAST_ONLY | 부울 | true | 유니캐스트 수신 패킷만 캡처합니다. false로 설정하면 브로드캐스트와 멀티캐스트도 모두 캡처됩니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_SEND_EMPTY_EVENTS  | 부울  | false  | 수집기의 빈 이벤트를 보냅니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV4_OBJECTS_IN_CACHE | 숫자 | 64 | 메모리에 저장할 최대 IPv4 네트워크 이벤트 수입니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_MAX_IPV6_OBJECTS_IN_CACHE | 숫자 | 64  | 메모리에 저장할 최대 IPv6 네트워크 이벤트 수입니다. |

### <a name="collectors"></a>수집기
| 이름 | Type | 기본값 | 세부 정보 |
| - | - | - | - |
| ASC_COLLECTOR_HEARTBEAT_ENABLED | 부울 | 켜기 | 하트비트 수집기를 사용하도록 설정합니다. |
| ASC_COLLECTOR_NETWORK_ACTIVITY_ENABLED  | 부울 | 켜기 | 네트워크 활동 수집기를 사용하도록 설정합니다. |
| ASC_COLLECTOR_SYSTEM_INFORMATION_ENABLED | 부울 | 켜기 | 시스템 정보 수집기를 사용하도록 설정합니다.  |

다른 구성 플래그는 고급이며 지원되지 않는 기능을 포함합니다. 고객 지원팀에 문의하여 이를 변경하거나 자세한 내용을 문의하세요.
 
## <a name="supported-security-alerts-and-recommendations"></a>지원되는 보안 경고 및 권장 사항

Azure RTOS용 Defender-IoT-micro-agent는 특정 보안 경고 및 권장 사항을 지원합니다. 서비스에 대한 [관련 경고 및 권장 값을 검토하고 사용자 지정](concept-rtos-security-alerts-recommendations.md)해야 합니다.

## <a name="log-analytics-optional"></a>로그 분석(선택 사항)

로그 분석을 사용하도록 설정하고 구성하여 디바이스 이벤트와 활동을 조사할 수 있습니다. [Defender for IoT 서비스 로그 분석](how-to-security-data-access.md#log-analytics)을 설정하고 사용하는 방법에 대한 자세한 내용을 알아보세요. 

## <a name="next-steps"></a>다음 단계


- Azure RTOS용 Defender-IoT-micro-agent [보안 경고 및 권장 사항](concept-rtos-security-alerts-recommendations.md) 검토 및 사용자 지정
- 필요에 따라 [Azure RTOS API용 Defender-IoT-micro-agent](azure-rtos-security-module-api.md)를 참조하세요.
