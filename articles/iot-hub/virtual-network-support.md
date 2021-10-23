---
title: 가상 네트워크에 대한 Azure IoT Hub 지원
description: IoT Hub에서 가상 네트워크 연결 패턴을 사용하는 방법
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: jlian
ms.openlocfilehash: bd5a88bb8fc47e5f4815aea57544d6093281999b
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130258870"
---
# <a name="iot-hub-support-for-virtual-networks-with-private-link-and-managed-identity"></a>Private Link 및 관리 ID를 사용하는 가상 네트워크에 대한 IoT Hub 지원

기본적으로 IoT Hub의 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 갖는 퍼블릭 엔드포인트에 매핑됩니다. 이 IoT Hub 퍼블릭 엔드포인트는 여러 고객이 공유하며, 광역 네트워크와 온-프레미스 네트워크에 있는 IoT 디바이스가 여기에 액세스할 수 있습니다.

![IoT Hub 퍼블릭 엔드포인트](./media/virtual-network-support/public-endpoint.png)

[메시지 라우팅](./iot-hub-devguide-messages-d2c.md), [파일 업로드](./iot-hub-devguide-file-upload.md), [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)를 비롯한 IoT Hub 기능을 사용할 때도 IoT Hub에서 고객 소유 Azure 리소스에 대한 퍼블릭 엔드포인트를 통한 연결이 필요합니다. 이러한 연결 경로는 종합적으로 IoT Hub에서 고객 리소스까지의 송신 트래픽을 구성합니다.

사용자가 소유하고 운영하는 VNet을 통해 Azure 리소스(IoT Hub 포함)의 연결을 제한하는 것이 좋습니다. 그 이유는 다음과 같습니다.

* 공용 인터넷에 대한 연결 노출을 방지하여 IoT 허브의 네트워크 격리를 도입

* 온-프레미스 네트워크 자산에서 프라이빗 연결 환경을 사용하도록 설정하여 데이터와 트래픽이 Azure 백본 네트워크로 직접 전송되도록 합니다.

* 중요한 온-프레미스 네트워크에서의 반출 공격을 방지합니다. 

* [프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)를 사용하여 Azure 전역의 연결 패턴을 설정합니다.

이 문서에서는 IoT Hub에서의 수신 연결을 위해 [Azure Private Link](../private-link/private-link-overview.md)를 사용하고 IoT Hub에서 다른 Azure 리소스까지로의 송신 연결을 위해 신뢰할 수 있는 Microsoft 서비스 예외를 사용하여 이 목표를 달성하는 방법을 설명합니다.

## <a name="ingress-connectivity-to-iot-hub-using-azure-private-link"></a>Azure Private Link를 사용한 IoT Hub로의 수신 연결

프라이빗 엔드포인트는 Azure 리소스에 연결할 수 있는 고객 소유의 VNet 내에 할당된 프라이빗 IP 주소입니다. Azure Private Link를 통해 IoT 허브의 프라이빗 엔드포인트를 설정하여 VNet 내부 서비스가 IoT Hub의 퍼블릭 엔드포인트로 트래픽을 보내지 않아도 IoT Hub에 도달하도록 허용할 수 있습니다. 마찬가지로, 온-프레미스 디바이스는 [VPN(가상 사설망)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 피어링을 사용하여 VNet 및 IoT Hub(프라이빗 엔드포인트를 통해)에 연결할 수 있습니다. 결과적으로 [IoT Hub IP 필터](./iot-hub-ip-filtering.md) 또는 [공용 네트워크 액세스 토글](iot-hub-public-network-access.md)을 사용하여 IoT Hub의 공개 엔드포인트에 대한 연결을 제한하거나 완전히 차단할 수 있습니다. 이 방법은 디바이스의 프라이빗 엔드포인트를 사용하여 허브에 대한 연결을 유지합니다. 이 설정은 주요 온-프레미스 네트워크에 있는 디바이스에 적용되며, 광역 네트워크에 배포된 디바이스에는 권장되지 않습니다.

![IoT Hub 가상 네트워크 송신](./media/virtual-network-support/virtual-network-ingress.png)

계속하기 전에 다음과 같은 전제 조건을 충족하는지 확인하세요.

* 프라이빗 엔드포인트를 만들 [Azure VNet](../virtual-network/quick-create-portal.md)과 서브넷을 만들었습니다.

* 온-프레미스 네트워크에서 작동하는 디바이스의 경우, VNet에 [VPN(가상 사설망)](../vpn-gateway/vpn-gateway-about-vpngateways.md) 또는 Azure [ExpressRoute](https://azure.microsoft.com/services/expressroute/) 프라이빗 피어링을 설정합니다.

### <a name="set-up-a-private-endpoint-for-iot-hub-ingress"></a>IoT Hub 수신을 위한 프라이빗 엔드포인트 설정

프라이빗 엔드포인트는 디바이스-클라우드 메시지와 같은 IoT Hub 디바이스 API 및 서비스 API(예: 디바이스 만들기 및 업데이트)에 대해 작동합니다.

1. Azure Portal에서 **네트워킹**, **개인 액세스** 를 선택 하 고 **+ 개인 끝점 만들기** 옵션을 클릭 합니다.

    :::image type="content" source="media/virtual-network-support/private-link.png" alt-text="IoT Hub의 프라이빗 엔드포인트를 추가할 곳을 보여 주는 스크린샷" border="true":::

1. 새 프라이빗 엔드포인트를 만들 구독, 리소스 그룹, 이름, 영역을 지정합니다. 프라이빗 엔드포인트는 허브와 같은 영역에 만드는 것이 가장 좋습니다.

1. **다음: 리소스** 를 클릭하고 IoT Hub 리소스의 구독을 지정한 다음 리소스 유형으로 **“Microsoft.Devices/IotHubs”** 를 선택하고, **리소스** 로 IoT Hub 이름을 선택하고, 대상 하위 리소스로 **iotHub** 를 선택합니다.

1. **다음: 구성** 을 클릭하고 프라이빗 엔드포인트를 만들 가상 네트워크와 서브넷을 지정합니다. 원하는 경우 Azure 프라이빗 DNS 영역과의 통합 옵션을 선택합니다.

1. **다음: 태그** 를 클릭하고 선택적으로 리소스의 태그를 지정합니다.

1. **검토 + 만들기** 를 클릭하여 프라이빗 링크 리소스를 만듭니다.

### <a name="built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 엔드포인트 

[기본 제공 Event 허브 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)는 프라이빗 엔드포인트를 통해 액세스할 수도 있습니다. 프라이빗 링크가 구성되면 기본 제공 엔드포인트에 대한 추가 프라이빗 엔드포인트 연결이 표시되어야 합니다. FQDN에 `servicebus.windows.net`이 있습니다.

:::image type="content" source="media/virtual-network-support/private-built-in-endpoint.png" alt-text="각 IoT Hub 프라이빗 링크가 제공된 2개의 프라이빗 엔드포인트를 보여 주는 이미지":::

IoT Hub의 [IP 필터](iot-hub-ip-filtering.md)는 기본 제공 엔드포인트에 대한 공용 액세스를 선택적으로 제어할 수 있습니다. 

IoT 허브에 대한 공용 네트워크 액세스를 완전히 차단하려면 [공용 네트워크 액세스를 해제](iot-hub-public-network-access.md)하거나 IP 필터를 사용하여 모든 IP를 차단하고 기본 제공 엔드포인트에 규칙을 적용하는 옵션을 선택합니다.

### <a name="pricing-for-private-link"></a>Private Link의 가격

가격 책정에 대한 자세한 내용은 [Azure Private Link 가격 책정](https://azure.microsoft.com/pricing/details/private-link)을 참조하세요.

## <a name="egress-connectivity-from-iot-hub-to-other-azure-resources"></a>IoT Hub에서 다른 Azure 리소스로의 송신 연결

IoT Hub는 Azure Blob Storage, 이벤트 허브, 서비스 버스 리소스에 연결하여 해당 리소스의 퍼블릭 엔드포인트를 통해 [메시지 라우팅](./iot-hub-devguide-messages-d2c.md), [파일 업로드](./iot-hub-devguide-file-upload.md) 및 [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)를 수행할 수 있습니다. 리소스를 VNet 블록에 바인딩하면 기본적으로 리소스에 대한 연결이 차단됩니다. 그 결과 이 구성은 IoT Hub가 리소스로 데이터를 보내는 작업을 방지합니다. 이 문제를 해결하려면 IoT Hub에서 **신뢰할 수 있는 Microsoft 서비스** 옵션을 통해 스토리지 계정, 이벤트 허브 또는 서비스 버스 리소스에 대한 연결을 설정하세요. 

다른 서비스에서 신뢰할 수 있는 Microsoft 서비스로 IoT 허브를 발견할 수 있도록 하려면 IoT 허브에서 관리 ID를 사용해야 합니다. 관리 ID가 프로비전되면 허브의 관리 ID에 Azure RBAC 권한을 부여하여 사용자 지정 엔드포인트에 액세스해야 합니다. [IoT Hub의 관리 ID 지원](./iot-hub-managed-identity.md) 문서에 따라 Azure RBAC 권한으로 관리 ID를 프로비전하고 사용자 지정 엔드포인트를 IoT Hub에 추가합니다. 방화벽 구성이 있는 경우 IoT Hub에서 사용자 지정 엔드포인트에 액세스할 수 있도록 신뢰할 수 있는 Microsoft 자사 예외 기능을 설정해야 합니다.

### <a name="pricing-for-trusted-microsoft-service-option"></a>신뢰할 수 있는 Microsoft 서비스 옵션에 대한 가격 책정
신뢰할 수 있는 Microsoft 자사 서비스 예외 기능은 무료입니다. 프로비저닝된 스토리지 계정, 이벤트 허브 또는 서비스 버스 리소스의 요금은 개별적으로 적용됩니다.
## <a name="next-steps"></a>다음 단계

IoT Hub 기능에 대해 자세히 알아보려면 다음 링크를 참조하세요.

* [메시지 라우팅](./iot-hub-devguide-messages-d2c.md)
* [파일 업로드](./iot-hub-devguide-file-upload.md)
* [대량 디바이스 가져오기/내보내기](./iot-hub-bulk-identity-mgmt.md)
