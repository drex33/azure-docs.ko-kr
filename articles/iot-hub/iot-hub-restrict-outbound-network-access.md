---
title: IoT Hub 아웃바운드 네트워크 액세스 제한 및 데이터 손실 방지
description: 개발자 가이드 - 신뢰할 수 있는 위치로만 송신하도록 IoT Hub를 구성하는 방법입니다.
author: jlian
manager: briz
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/24/2021
ms.custom:
- 'Role: Cloud Development'
ms.openlocfilehash: f4f8044237c82212723a54b677d77bc5aecb95a5
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867806"
---
# <a name="restrict-outbound-network-access-for-azure-iot-hub"></a>Azure IoT Hub에 대한 아웃바운드 네트워크 액세스 제한

IoT Hub는 [사용자 지정 엔드포인트로의 라우팅](iot-hub-devguide-messages-d2c.md), [파일 업로드](iot-hub-devguide-file-upload.md) 및 [디바이스 ID 내보내기](iot-hub-bulk-identity-mgmt.md)를 통해 다른 서비스로의 데이터 송신을 지원합니다. 엔터프라이즈 환경의 추가 보안을 위해 `restrictOutboundNetworkAccess` API를 사용하여 IoT Hub 송신을 명시적으로 승인된 대상으로만 제한합니다. 현재 이 기능은 Azure Portal에서 사용할 수 없습니다.

## <a name="enabling-the-restriction"></a>제한 사용

이 기능을 사용하도록 설정하려면 가능한 방법을 사용하여 IoT Hub 리소스 속성(`PUT`)을 업데이트하여 `restrictOutboundNetworkAccess`를 `true`로 설정하고 FQDN(정규화된 도메인 이름)이 포함된 `allowedFqdnList`를 배열로 포함합니다. 

[Create 또는 Update 메서드](/rest/api/iothub/iothubresource/createorupdate)와 함께 사용할 JSON 표현을 보여 주는 예:

```json
{
...
            "properties": {
              ...
                "restrictOutboundNetworkAccess": true,
                "allowedFqdnList": [
                    "my-eventhub.servicebus.windows.net",
                    "iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net"
                ]
              ...
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            }
        }
    }
}
```
Azure CLI를 사용하여 동일한 업데이트를 수행하려면 다음을 실행합니다.

```azurecli-interactive
az resource update -n <iothubName> -g <resourceGroupName> --resource-type Microsoft.Devices/IotHubs --set properties.restrictOutboundNetworkAccess=true properties.allowedFqdnList="['my-eventhub.servicebus.windows.net','iothub-ns-built-in-endpoint-2917414-51ea2487eb.servicebus.windows.net']"
```

## <a name="restricting-outbound-network-access-with-existing-routes"></a>기존 경로로 아웃바운드 네트워크 액세스 제한

`restrictOutboundNetworkAccess`가 `true`로 설정되면 허용된 FQDN 외부의 대상으로 데이터를 내보내려는 시도가 실패합니다. 사용자 지정 엔드포인트가 허용된 FQDN 목록에 포함되어 있지 않으면 기존에 구성된 경로도 작동을 멈춥니다.

## <a name="built-in-endpoint"></a>기본 제공 엔드포인트

`restrictOutboundNetworkAccess`가 `true`로 설정된 경우 기본 제공 이벤트 허브 호환 엔드포인트는 제한에서 제외되지 않습니다. 즉, 계속 작동하려면 허용된 FQDN 목록에 기본 제공 엔드포인트 FQDN을 포함해야 합니다.

## <a name="next-steps"></a>다음 단계

- 데이터 송신에 관리 ID를 사용하려면 [관리 ID에 대한 IoT Hub 지원](iot-hub-managed-identity.md)을 참조하세요.
- 인바운드 네트워크 액세스를 제한하려면 [IoT Hub에 대한 공개 네트워크 액세스 관리](iot-hub-public-network-access.md) 및 [Private Link로 가상 네트워크에 대한 IoT Hub 지원](virtual-network-support.md)을 참조하세요.