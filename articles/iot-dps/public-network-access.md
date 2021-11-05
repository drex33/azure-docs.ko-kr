---
title: Azure IoT Device Provisioning Service(DPS)에 대한 공용 네트워크 액세스 관리
description: Azure IoT Device Provisioning Service(DPS)에 대한 공용 네트워크 액세스를 사용하지 않도록 설정하고 사용하도록 설정하는 방법에 대한 설명서
ms.author: v-stharr
author: anastasia-ms
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 74c22d802c022d51a1ef8b4aa231cc92661d582b
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131859278"
---
# <a name="manage-public-network-access-for-your-iot-device-provisioning-service"></a>IoT Device Provisioning Service에 대한 공용 네트워크 액세스 관리

[VNet에서 DPS에 대한 프라이빗 엔드포인트에 대한](virtual-network-support.md)액세스를 제한하려면 공용 네트워크 액세스를 사용하지 않도록 설정합니다. 이렇게 하려면 Azure Portal 또는 `publicNetworkAccess` API를 사용합니다. 포털 또는 `publicNetworkAccess` API를 사용하여 공개 액세스를 허용할 수도 있습니다.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Azure Portal을 사용하여 공용 네트워크 액세스 끄기

공용 네트워크 액세스를 해제하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.
3. Device Provisioning Service를 선택합니다.
4. 왼쪽의 **설정** 메뉴에서 *네트워킹을* 선택합니다.
5. **공용 네트워크 액세스** 아래에서 사용 안 *함을 선택합니다.*
6. **저장** 을 선택합니다.

    :::image type="content" source="media/iot-dps-public-network-access/disable-public-access.png" alt-text="공용 네트워크 액세스를 해제할 수 있는 Azure Portal을 보여 주는 이미지" :::

공용 네트워크 액세스를 설정하려면 다음을 수행합니다.

1. **모든 네트워크** 를 선택합니다.
2. **저장** 을 선택합니다.

## <a name="access-the-dps-after-disabling-the-public-network-access"></a>공용 네트워크 액세스를 비활성화한 후 DPS에 액세스

공용 네트워크 액세스를 사용하지 않도록 설정하면 DPS 인스턴스는 [Azure 프라이빗 링크 를 사용하여 해당 VNet 프라이빗 엔드포인트를](virtual-network-support.md)통해서만 액세스할 수 있습니다. 이 제한에는 Azure Portal 통한 액세스가 포함됩니다.

## <a name="dps-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>공용 네트워크 액세스를 비활성화한 후 DPS 엔드포인트, IP 주소 및 포트

DPS는 여러 고객이 동일한 컴퓨팅, 네트워킹 및 스토리지 하드웨어 리소스 풀을 공유하는 다중 테넌트 PaaS(Platform-as-a-Service)입니다. DPS의 호스트 이름은 인터넷을 통해 공개적으로 라우팅 가능한 IP 주소를 사용하여 공용 엔드포인트에 매핑합니다. 다양한 고객이 이 DPS 공용 엔드포인트를 공유하고, 광역 네트워크 및 온-프레미스 네트워크의 IoT 디바이스가 모두 액세스할 수 있습니다. 

공용 네트워크 액세스를 사용하지 않도록 하는 것은 특정 DPS 리소스에 적용되어 격리를 보장합니다. 공용 경로를 사용하여 다른 고객 리소스에 대해 서비스를 계속 활성화하기 위해 공용 엔드포인트는 확인 가능한 상태로 유지되고, IP 주소는 검색 가능하며, 포트는 열린 상태로 유지됩니다. Microsoft는 여러 계층의 보안을 통합하여 테넌트 간의 완전한 격리를 보장하므로 이는 문제가 되지 않습니다. 자세한 내용은 [Azure 퍼블릭 클라우드에서 격리](../security/fundamentals/isolation-choices.md#tenant-level-isolation)를 참조하세요.

## <a name="ip-filter"></a>IP 필터

공용 네트워크 액세스를 사용하지 않도록 설정하면 모든 [IP 필터](../iot-dps/iot-dps-ip-filtering.md) 규칙이 무시됩니다. 이는 공용 인터넷의 모든 IP가 차단되기 때문입니다. IP 필터를 사용하려면 **선택한 IP 범위** 옵션을 사용합니다.

### <a name="turn-on-all-network-ranges"></a>모든 네트워크 범위 설정

모든 네트워크 범위를 켜려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.
3. Device Provisioning Service를 선택합니다.
4. 왼쪽의 **설정** 메뉴에서 *네트워킹을* 선택합니다.
5. **공용 네트워크 액세스** 아래에서 모든 *네트워크를* 선택합니다.
6. **저장** 을 선택합니다.
