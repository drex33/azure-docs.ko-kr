---
title: 개인 네트워크 액세스 개요 - Azure Database for MySQL 유연한 서버
description: Azure Database for MySQL의 유연한 서버 배포 옵션의 프라이빗 액세스 및 네트워킹 옵션에 대해 알아봅니다.
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: 961e1fbf4bc87355ce036fb9a5af7a1cd85c4e76
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131472716"
---
# <a name="private-network-access-for-azure-database-for-mysql---flexible-server"></a>Azure Database for MySQL 유연한 서버에 대 한 개인 네트워크 액세스

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

이 문서에서는 Azure MySQL 유연한 서버의 프라이빗 연결 옵션에 대해 설명합니다. Azure에서 서버를 안전하게 만들기 위해 Azure Database for MySQL 유연한 서버에 대한 가상 네트워크 개념을 상세히 살펴봅니다.


## <a name="private-access-vnet-integration"></a>프라이빗 액세스(VNet 통합)

[Azure Virtual Network(VNet)](../../virtual-network/virtual-networks-overview.md)는 Azure의 프라이빗 네트워크의 기본 구성 요소입니다. Azure Database for MySQL - 유연한 서버와 VNet(Virtual Network) 통합은 Azure의 네트워크 보안 및 격리 이점을 제공합니다.

Azure Database for MySQL - 유연한 서버에 대한 VNet(가상 네트워크) 통합을 사용하면 가상 네트워크 인프라로만 서버에 대한 액세스를 잠글 수 있습니다. VNet(가상 네트워크)은 단일 가상 네트워크에 모든 애플리케이션 및 데이터베이스 리소스를 포함하거나 동일하거나 다른 지역의 다른 VNet에 걸쳐 확장될 수 있습니다. Microsoft의 짧은 대기 시간, 고대역폭 프라이빗 백본 인프라 백본 인프라를 사용하는 [피어링](../../virtual-network/virtual-network-peering-overview.md)을 통해 다양한 가상 네트워크 간의 원활한 연결을 설정할 수 있습니다. 가상 네트워크는 연결하기 위해 하나로 표시됩니다.

Azure Database for MySQL - 유연한 서버는 다음에서 클라이언트 연결을 지원합니다.

* 동일한 Azure 지역 내의 가상 네트워크. (로컬 피어링된 VNet)
* Azure 지역의 가상 네트워크. (글로벌 피어링된 VNet)

서브넷을 사용하면 가상 네트워크를 하나 이상의 하위 네트워크로 분할하고 Azure 리소스를 배포할 수 있는 가상 네트워크 주소 공간의 일부를 각 서브넷에 할당합니다. Azure Database for MySQL - 유연한 서버에는 [위임된 서브넷](../../virtual-network/subnet-delegation-overview.md)이 필요합니다. 위임된 서브넷은 서브넷이 Azure Database for MySQL - 유연한 서버만 호스팅할 수 있도록 하는 명시적 식별자입니다. 서브넷을 위임하면 서비스는 Azure Database for MySQL - 유연한 서버를 원활하게 관리하기 위해 서브넷에 서비스별 리소스를 만들 수 있도록 하는 명시적 권한을 얻습니다.

Azure Database for MySQL - 유연한 서버는 Azure [프라이빗 DNS 영역](../../dns/private-dns-privatednszone.md)과 통합되어 사용자 지정 DNS 솔루션을 추가할 필요 없이 가상 네트워크에서 도메인 이름을 관리하고 확인할 수 있는 안정적이고 안전한 DNS 서비스를 제공합니다. [가상 네트워크 링크](../../dns/private-dns-virtual-network-links.md)를 만들어 프라이빗 DNS 영역을 하나 이상의 가상 네트워크에 연결할 수 있습니다.


:::image type="content" source="./media/concepts-networking/vnet-diagram.png" alt-text="유연한 서버 MySQL VNET":::

위의 다이어그램에서,

1. 유연한 서버는 VNET **VNet-1** 의 위임된 서브넷 - 10.0.1.0/24에 삽입됩니다.
2. 동일한 vnet 내의 다른 서브넷에 배포된 애플리케이션은 유연한 서버에 직접 액세스할 수 있습니다.
3. 다른 VNET **VNet-2** 에 배포된 애플리케이션은 유연한 서버에 직접 액세스할 수 없습니다. 유연한 서버에 액세스하려면 먼저 [프라이빗 DNS 영역 VNET 피어링](#private-dns-zone-and-vnet-peering)을 수행해야 합니다.

## <a name="virtual-network-concepts"></a>가상 네트워크 개념

MySQL 유연한 서버에서 가상 네트워크를 사용할 때 알아야 할 몇 가지 개념은 다음과 같습니다.

* **가상 네트워크** -

   Azure VNet(Virtual Network)은 사용하도록 구성된 개인 IP 주소 공간을 포함합니다. Azure 가상 네트워킹에 대한 자세한 내용은 [Azure Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)를 참조하세요.

    가상 네트워크는 유연한 서버와 같은 Azure 지역에 있어야 합니다.

* **위임된 서브넷** -

   가상 네트워크에는 서브넷(하위 네트워크)이 포함됩니다. 서브넷을 사용하면 가상 네트워크를 더 작은 주소 공간으로 분할할 수 있습니다. Azure 리소스는 가상 네트워크 내의 특정 서브넷에 배포됩니다.

   MySQL 유연한 서버는 MySQL 유연한 서버 전용으로 **위임된** 서브넷에 있어야 합니다. 이렇게 위임하면 Azure Database for MySQL 유동 서버에서만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다. 위임 속성을 Microsoft.DBforMySQL/flexibleServers로 할당하여 서브넷을 위임합니다.

* **NSG(네트워크 보안 그룹)**

   네트워크 보안 그룹의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 자세한 정보는 [네트워크 보안 그룹 개요](../../virtual-network/network-security-groups-overview.md)를 검토하세요.

* **프라이빗 DNS 영역 통합** -

   Azure 프라이빗 DNS 영역 통합을 통해 프라이빗 DNS 영역이 연결된 모든 지역 내 피어링된 VNET 또는 현재 VNET 내의 프라이빗 DNS를 확인할 수 있습니다.

* **가상 네트워크 피어링**

   가상 네트워크 피어링을 사용하면 Azure에서 두 개 이상의 가상 네트워크를 원활하게 연결할 수 있습니다. 피어링된 가상 네트워크는 연결하기 위해 하나로 표시됩니다. 피어링된 가상 네트워크에 있는 가상 머신 간의 트래픽은 Microsoft 백본 인프라를 사용합니다. 피어링된 VNet의 클라이언트 애플리케이션과 유연한 서버 간 트래픽은 Microsoft 개인 네트워크를 통해서만 라우팅되며 해당 네트워크에만 격리됩니다.

## <a name="using-private-dns-zone"></a>프라이빗 DNS 영역 사용

* Azure Portal 또는 Azure CLI를 사용하여 VNET으로 유연한 서버를 만드는 경우 제공된 서버 이름을 사용하여 구독의 서버별로 새 프라이빗 DNS 영역이 자동으로 프로비저닝됩니다. 또는 유연한 서버에서 사용할 사용자 고유의 프라이빗 DNS 영역을 설정하려는 경우에는 [프라이빗 DNS 개요](../../dns/private-dns-overview.md) 설명서를 참조하세요.
* Azure API, ARM 템플릿(Azure Resource Manager 템플릿) 또는 Terraform을 사용하는 경우 `mysql.database.azure.com`으로 끝나는 프라이빗 DNS 영역을 만들고 프라이빗 액세스로 유연한 서버를 구성하는 동안 이를 사용하세요. 자세한 내용은 [프라이빗 DNS 영역 개요](../../dns/private-dns-overview.md)를 참조하세요.

   > [!IMPORTANT]
   > 프라이빗 DNS 영역 이름은 `mysql.database.azure.com`으로 끝나야 합니다.

[Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)에서 프라이빗 액세스(VNet 통합)를 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.

## <a name="integration-with-custom-dns-server"></a>사용자 지정 DNS 서버와 통합

사용자 지정 DNS 서버를 사용하는 경우에는 DNS 전달자를 사용하여 Azure Database for MySQL - 유연한 서버의 FQDN을 확인해야 합니다. 전달자 IP 주소는 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)이어야 합니다. 사용자 지정 DNS 서버는 VNet 내부에 있거나 VNET의 DNS 서버 설정을 통해 연결할 수 있어야 합니다. 자세한 내용은 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

## <a name="private-dns-zone-and-vnet-peering"></a>프라이빗 DNS 영역 및 VNET 피어링

프라이빗 DNS 영역 설정과 VNET 피어링은 서로 독립적입니다. 프라이빗 DNS 영역 생성 및 사용에 대한 자세한 내용은 위의 [프라이빗 DNS 영역 사용](concepts-networking-vnet.md#using-private-dns-zone) 섹션을 참조하세요.

동일한 지역 또는 다른 지역의 다른 VNET에 프로비저닝된 클라이언트에서 유연한 서버에 연결하려는 경우에는 VNET과 프라이빗 DNS 영역을 연결해야 합니다. [가상 네트워크 연결 방법](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network) 설명서를 참조하세요.

> [!NOTE]
> `mysql.database.azure.com`으로 끝나는 프라이빗 DNS 영역 이름만 연결할 수 있습니다.

## <a name="connecting-from-on-premises-to-flexible-server-in-virtual-network-using-expressroute-or-vpn"></a>ExpressRoute 또는 VPN을 사용하여 온-프레미스에서 Virtual Network의 유연한 서버에 연결

온-프레미스에서 가상 네트워크의 유연한 서버에 액세스해야 하는 워크로드의 경우 [ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute/) 또는 [VPN](/azure/architecture/reference-architectures/hybrid-networking/vpn/)과, [온-프레미스에 연결된](/azure/architecture/reference-architectures/hybrid-networking/) 가상 네트워크가 필요합니다. 이 설정을 적용한 후, 온-프레미스 가상 네트워크에서 실행 중인 클라이언트 애플리케이션(예: MySQL Workbench)에서 연결하려는 경우 유연한 서버 이름 확인을 위해 DNS 전달자가 필요합니다. 이 DNS 전달자는 서버 수준 전달자를 통해 Azure 제공 DNS 서비스 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)에 대해 DNS 쿼리를 확인해야 합니다.

올바르게 구성하려면 다음 리소스가 필요합니다.

* 온-프레미스 네트워크
* 프라이빗 액세스로 프로비전된 MySQL 유연한 서버(VNet 통합)
* [온-프레미스에 연결된](/azure/architecture/reference-architectures/hybrid-networking/) 가상 네트워크
* Azure에 배포된 DNS 전달자 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md) 사용

그런 다음, 유연한 서버 이름(FQDN)을 사용하여 피어링된 가상 네트워크 또는 온-프레미스 네트워크의 클라이언트 애플리케이션에서 유연한 서버에 연결할 수 있습니다.

## <a name="unsupported-virtual-network-scenarios"></a>지원되지 않는 가상 네트워크 시나리오

* 퍼블릭 엔드포인트(또는 공용 IP 또는 DNS) - 가상 네트워크에 배포된 유연한 서버는 퍼블릭 엔드포인트를 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기(주소 공간)를 늘릴 수 없습니다.
* 유연한 서버는 Private Link를 지원하지 않습니다. 대신 VNet 주입을 사용하여 VNet 내에서 유연한 서버를 사용할 수 있도록 합니다.

> [!NOTE]
> 사용자 지정 DNS 서버를 사용하는 경우에는 DNS 전달자를 사용하여 Azure Database for MySQL - 유연한 서버의 FQDN을 확인해야 합니다. 자세한 정보는 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)를 사용하여 프라이빗 액세스(vnet 통합)를 사용하도록 설정하는 방법 알아보기
* [TLS 사용](how-to-connect-tls-ssl.md) 방법 알아보기
