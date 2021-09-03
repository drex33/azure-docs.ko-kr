---
title: Azure IoT Hub에 대한 공용 네트워크 액세스 관리
description: IoT hub에 대한 공용 네트워크 액세스를 사용하거나 사용하지 않도록 설정하는 방법에 대한 설명서
author: jlian
ms.author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.openlocfilehash: a729cd14c2f65b7ff4ab478f9efd25e13a1170b4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515602"
---
# <a name="managing-public-network-access-for-your-iot-hub"></a>IoT 허브의 공용 네트워크 액세스 관리

[VNet에서 IoT 허브의 프라이빗 엔드포인트](virtual-network-support.md)로만 액세스를 제한하려면 공용 네트워크 액세스를 사용하지 않도록 설정합니다. 이렇게 하려면 Azure Portal 또는 `publicNetworkAccess` API를 사용합니다. 포털 또는 `publicNetworkAccess` API를 사용하여 공개 액세스를 허용할 수도 있습니다.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Azure Portal을 사용하여 공용 네트워크 액세스 끄기

1. [Azure Portal](https://portal.azure.com)로 이동
2. IoT Hub로 이동 **리소스 그룹** 으로 이동하고 적절한 그룹을 선택한 다음 IoT Hub를 선택합니다.
3. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
4. 공용 네트워크 액세스 허용에서 **사용 안 함** 을 선택합니다.
5. **저장** 을 선택합니다.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png" alt-text="공용 네트워크 액세스를 해제할 수 있는 Azure Portal을 보여 주는 이미지" lightbox="media/iot-hub-publicnetworkaccess/turn-off-public-network-access.png":::

공용 네트워크 액세스를 사용하려면 **모든 네트워크** 를 선택한 다음 **저장** 을 선택합니다.

### <a name="accessing-the-iot-hub-after-disabling-the-public-network-access"></a>공용 네트워크 액세스를 사용하지 않도록 설정한 후 IoT Hub 액세스

공용 네트워크 액세스가 사용하지 않도록 설정되면 IoT Hub는 [Azure 프라이빗 링크를 사용하는 VNet 프라이빗 엔드포인트](virtual-network-support.md)를 통해서만 액세스할 수 있습니다. 이 제한에는 IoT Hub 서비스에 대한 API 호출이 사용자의 자격 증명으로 브라우저를 사용하여 직접 수행되기 때문에 Azure Portal을 통한 액세스가 포함됩니다.

### <a name="iot-hub-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>공용 네트워크 액세스를 사용하지 않도록 설정한 후 IoT Hub 엔드포인트, IP 주소 및 포트

IoT Hub는 다중 테넌트 PaaS(Platform as a Service)이므로 여러 고객이 동일한 컴퓨팅, 네트워킹 및 스토리지 하드웨어 리소스 풀을 공유합니다. IoT Hub의 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 갖는 퍼블릭 엔드포인트에 매핑됩니다. 이 IoT Hub 퍼블릭 엔드포인트는 여러 고객이 공유하며, 광역 네트워크와 온-프레미스 네트워크에 있는 IoT 디바이스가 모두 여기에 액세스할 수 있습니다. 

공용 네트워크 액세스를 사용하지 않도록 설정하면 특정 IoT Hub 리소스에 적용되므로 격리가 보장됩니다. 공용 경로를 사용하여 다른 고객 리소스에 대해 서비스를 계속 활성화하기 위해 공용 엔드포인트는 확인 가능한 상태로 유지되고, IP 주소는 검색 가능하며, 포트는 열린 상태로 유지됩니다. Microsoft는 여러 계층의 보안을 통합하여 테넌트 간의 완전한 격리를 보장하므로 이는 문제가 되지 않습니다. 자세한 내용은 [Azure 퍼블릭 클라우드에서 격리](../security/fundamentals/isolation-choices.md#tenant-level-isolation)를 참조하세요.

### <a name="ip-filter"></a>IP 필터

공용 네트워크 액세스를 사용하지 않도록 설정하면 모든 [IP 필터](iot-hub-ip-filtering.md) 규칙이 무시됩니다. 이는 공용 인터넷의 모든 IP가 차단되기 때문입니다. IP 필터를 사용하려면 **선택한 IP 범위** 옵션을 사용합니다.

### <a name="bug-fix-with-built-in-event-hub-compatible-endpoint"></a>기본 제공 Event Hub 호환 엔드포인트 관련 버그 수정

IoT Hub에 대한 공용 네트워크 액세스가 사용하지 않도록 설정된 경우 공용 인터넷을 통해 [기본 제공 Event Hub 호환 엔드포인트](iot-hub-devguide-messages-read-builtin.md)에 계속 액세스할 수 있는 IoT Hub 관련 버그가 있습니다. 이 버그에 대해 자세히 알아보고 문의하려면 [IoT Hub에 대한 공용 네트워크 액세스를 사용하지 않으면 기본 제공 Event Hub 엔드포인트에 대한 액세스가 사용하지 않도록 설정됨](https://azure.microsoft.com/updates/iot-hub-public-network-access-bug-fix)을 참조하세요.

## <a name="turn-on-network-access-using-azure-portal"></a>Azure Portal을 사용하여 네트워크 액세스 설정

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. IoT Hub로 이동 **리소스 그룹** 으로 이동하여 적절한 그룹을 선택한 다음 허브를 선택합니다.
3. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
4. 공용 네트워크 액세스 허용에서 **선택한 IP 범위** 를 선택합니다.
5. **IP 필터** 대화 상자를 열어 **클라이언트 IP 주소 추가** 를 선택하고 이름과 주소 범위를 입력합니다.
6. **저장** 을 선택합니다. 단추가 회색으로 표시된 경우 클라이언트 IP 주소가 이미 IP 필터로 추가되었는지 확인합니다.

:::image type="content" source="media/iot-hub-publicnetworkaccess/turn-on-public-network-access.png" alt-text="공용 네트워크 액세스를 켜는 Azure Portal을 보여 주는 이미지":::

### <a name="turn-on-all-network-ranges"></a>모든 네트워크 범위 설정

1. IoT Hub로 이동 **리소스 그룹** 으로 이동하여 적절한 그룹을 선택한 다음 허브를 선택합니다.
1. 왼쪽 메뉴에서 **네트워킹** 을 선택합니다.
1. 공용 네트워크 액세스 허용에서 **모든 네트워크** 를 선택합니다.
1. **저장** 을 선택합니다.

### <a name="check-iot-hub-access-using-cloud-shell"></a>Cloud Shell을 사용하여 IoT Hub 액세스 확인

Azure Cloud Shell을 사용하여 IoT Hub 액세스를 확인할 수 있습니다. 모든 네트워크 범위를 설정했는지 확인한 후 다음 명령을 실행합니다. "SubscriptionName"을 구독 이름으로 바꾸고 "MyIoTHub"를 허브 이름으로 바꿉니다.

```azurecli
  az account set -s "SubscriptionName"
  az iot hub device-identity list --hub-name "MyIoTHub"
```

```azurepowershell
  Set-AzContext -Name "SubscriptionName"
  Get-AzIoTHubDevice -IotHubName "MyIoTHub"
```
### <a name="troubleshooting"></a>문제 해결

IoT Hub에 액세스하는 데 문제가 있는 경우 네트워크 구성이 문제일 수 있습니다. 예를 들어 IoT 디바이스 페이지에 액세스하려고 할 때 다음 오류 메시지가 표시되는 경우 **네트워킹** 페이지를 확인하여 공용 네트워크 액세스가 사용하지 않도록 설정되었는지 또는 선택한 IP 범위로 제한되었는지 확인합니다.

```
  Unable to retrieve devices. Please ensure that your network connection is online and network settings allow connections from your IP address.
```

IoT Hub에 액세스하려면 IP 주소 범위에 IP 주소를 추가하거나 모든 네트워크에 대한 공용 네트워크 액세스를 사용하도록 설정할 수 있는 권한을 IT 관리자에게 요청합니다. 문제가 해결되지 않으면 로컬 네트워크 설정을 확인하거나 로컬 네트워크 관리자에게 문의하여 IoT Hub에 대한 연결을 수정합니다. 예를 들어 로컬 네트워크의 프록시가 IoT Hub에 대한 액세스를 방해할 수 있는 경우가 있습니다.

위의 명령이 작동하지 않거나 모든 네트워크 범위를 설정할 수 없는 경우 Microsoft 지원에 문의합니다.
