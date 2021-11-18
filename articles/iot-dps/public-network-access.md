---
title: DPS (Azure IoT 장치 프로 비전 서비스)에 대 한 공용 네트워크 액세스 관리
description: Azure IoT 장치 프로 비전 서비스 (DPS)에 대해 공용 네트워크 액세스를 사용 하지 않도록 설정 하 고 사용 하도록 설정 하는 방법
ms.author: wesmc
author: wesmc7777
ms.service: iot-dps
services: iot-dps
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 558be33fb932521e3083231b1d529a076ea6c752
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132761590"
---
# <a name="manage-public-network-access-for-your-iot-device-provisioning-service"></a>IoT 장치 프로 비전 서비스에 대 한 공용 네트워크 액세스 관리

[VNet에서 DPS의 개인 끝점에 대 한](virtual-network-support.md)액세스를 제한 하려면 공용 네트워크 액세스를 사용 하지 않도록 설정 합니다. 이렇게 하려면 Azure Portal 또는 `publicNetworkAccess` API를 사용합니다. 포털 또는 `publicNetworkAccess` API를 사용하여 공개 액세스를 허용할 수도 있습니다.

## <a name="turn-off-public-network-access-using-the-azure-portal"></a>Azure Portal을 사용하여 공용 네트워크 액세스 끄기

공용 네트워크 액세스를 해제 하려면:

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.
3. Device Provisioning Service를 선택합니다.
4. 왼쪽의 **설정** 메뉴에서 *네트워킹* 을 선택 합니다.
5. **공용 네트워크 액세스** 에서 *사용 안 함* 을 선택 합니다.
6. **저장** 을 선택합니다.

    :::image type="content" source="media/iot-dps-public-network-access/disable-public-access.png" alt-text="공용 네트워크 액세스를 해제할 수 있는 Azure Portal을 보여 주는 이미지" :::

공용 네트워크 액세스를 켜려면:

1. **모든 네트워크** 를 선택 합니다.
2. **저장** 을 선택합니다.

## <a name="access-the-dps-after-disabling-the-public-network-access"></a>공용 네트워크 액세스를 사용 하지 않도록 설정한 후 DPS 액세스

공용 네트워크 액세스를 사용 하지 않도록 설정 하면 DPS 인스턴스는 [Azure 개인 링크를 사용 하 여 VNet 개인 끝점](virtual-network-support.md)을 통해서만 액세스할 수 있습니다. 이 제한에는 Azure Portal를 통한 액세스가 포함 됩니다.

## <a name="dps-endpoint-ip-address-and-ports-after-disabling-public-network-access"></a>공용 네트워크 액세스를 사용 하지 않도록 설정한 후 DPS 끝점, IP 주소 및 포트

DPS는 여러 고객이 동일한 계산, 네트워킹 및 저장소 하드웨어 리소스 풀을 공유 하는 다중 테 넌 트 PaaS (Platform as a Service)입니다. DPS의 호스트 이름은 인터넷을 통해 공개적으로 라우팅할 수 있는 IP 주소를 사용 하는 공용 끝점에 매핑됩니다. 여러 고객이이 DPS 공용 끝점을 공유 하 고 광역 네트워크와 온-프레미스 네트워크의 IoT 장치가 모두 액세스할 수 있습니다. 

공용 네트워크 액세스를 사용 하지 않도록 설정 하면 특정 DPS 리소스에 적용 되므로 격리가 보장 됩니다. 공용 경로를 사용하여 다른 고객 리소스에 대해 서비스를 계속 활성화하기 위해 공용 엔드포인트는 확인 가능한 상태로 유지되고, IP 주소는 검색 가능하며, 포트는 열린 상태로 유지됩니다. Microsoft는 여러 계층의 보안을 통합하여 테넌트 간의 완전한 격리를 보장하므로 이는 문제가 되지 않습니다. 자세한 내용은 [Azure 퍼블릭 클라우드에서 격리](../security/fundamentals/isolation-choices.md#tenant-level-isolation)를 참조하세요.

## <a name="ip-filter"></a>IP 필터

공용 네트워크 액세스를 사용하지 않도록 설정하면 모든 [IP 필터](../iot-dps/iot-dps-ip-filtering.md) 규칙이 무시됩니다. 이는 공용 인터넷의 모든 IP가 차단되기 때문입니다. IP 필터를 사용하려면 **선택한 IP 범위** 옵션을 사용합니다.

### <a name="turn-on-all-network-ranges"></a>모든 네트워크 범위 설정

모든 네트워크 범위를 켜려면:

1. [Azure Portal](https://portal.azure.com)로 이동합니다.
2. 왼쪽 메뉴 또는 포털 페이지에서 **모든 리소스** 를 선택합니다.
3. Device Provisioning Service를 선택합니다.
4. 왼쪽의 **설정** 메뉴에서 *네트워킹* 을 선택 합니다.
5. **공용 네트워크 액세스** 에서 *모든 네트워크* 를 선택 합니다.
6. **저장** 을 선택합니다.
