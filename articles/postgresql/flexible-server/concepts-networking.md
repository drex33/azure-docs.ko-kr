---
title: 네트워킹 개요 - Azure Database for PostgreSQL - 유연한 서버
description: Azure Database for PostgreSQL의 유연한 서버 배포 옵션에 대한 연결 및 네트워킹 옵션에 대해 알아봅니다.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/08/2021
ms.openlocfilehash: 3a0ce42cf32e218f3debaf6f3e84bb8f27a81c82
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129279035"
---
# <a name="networking-overview-for-azure-database-for-postgresql---flexible-server-preview"></a>네트워킹 개요 - Azure Database for PostgreSQL - 유연한 서버(미리 보기)

이 문서에서는 Azure Database for PostgreSQL - 유연한 서버에 대한 연결 및 네트워킹 개념을 설명합니다. 유연한 서버 배포 옵션은 미리 보기 상태입니다.

Azure Database for PostgreSQL - 유연한 서버 인스턴스(*유연한 서버*)를 만들 때 다음 네트워킹 옵션 중 하나를 선택해야 합니다. **프라이빗 액세스(VNet 통합)** 또는 **퍼블릭 액세스(허용된 IP 주소)** . 

> [!NOTE]
> 서버가 만들어진 후에는 네트워킹 옵션을 변경할 수 없습니다. 

다음 특징은 프라이빗 액세스 또는 퍼블릭 액세스 옵션을 사용하도록 선택하는 경우에 적용됩니다.

* 허용되는 IP 주소에서의 연결은 유효한 자격 증명을 사용하여 PostgreSQL 서버에 인증해야 합니다.
* 네트워크 트래픽에 [연결 암호화](#tls-and-ssl)를 적용할 수 있습니다.
* 서버에 FQDN(정규화된 도메인 이름)이 있습니다. 연결 문자열의 `hostname` 속성에는 IP 주소 대신 FQDN을 사용하는 것이 좋습니다.
* 두 옵션 모두 데이터베이스 수준 또는 테이블 수준이 아닌 서버 수준에서 액세스를 제어합니다. PostgreSQL의 역할 속성을 사용하여 데이터베이스, 테이블 및 기타 개체 액세스를 제어합니다.

> [!NOTE]
> Azure Database for PostgreSQL은 관리형 데이터베이스 서비스이므로 사용자에게 `pg_hba.conf`와 같은 구성 파일을 보거나 수정할 수 있는 호스트 또는 OS 액세스 권한이 제공되지 않습니다. 파일 내용은 네트워크 설정에 따라 자동으로 업데이트됩니다.

## <a name="private-access-vnet-integration"></a>프라이빗 액세스(VNet 통합)

유연한 서버를 [Azure VNet(Virtual Network)](../../virtual-network/virtual-networks-overview.md)에 배포할 수 있습니다. Azure 가상 네트워크는 프라이빗하고 안전한 네트워크 통신을 제공합니다. 가상 네트워크의 리소스는 이 네트워크에 할당된 개인 IP 주소를 통해 통신할 수 있습니다.

다음 기능을 원하는 경우 이 네트워킹 옵션을 선택합니다.

* 개인 IP 주소를 사용하여 동일한 가상 네트워크의 Azure 리소스에서 유연한 서버에 연결
* VPN 또는 Azure ExpressRoute를 사용하여 비 Azure 리소스에서 유연한 서버에 연결
* 유연한 서버에 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트가 없는지 확인합니다.

:::image type="content" source="./media/how-to-manage-virtual-network-portal/flexible-pg-vnet-diagram.png" alt-text="가상 네트워크 간에 피어링이 작동하는 방식을 보여 주는 다이어그램(그중 하나에는 유연한 서버가 포함됨).":::

위의 다이어그램에서:
- 유연한 서버는 VNet-1 가상 네트워크의 서브넷 10.0.1.0/24에 삽입됩니다.
- 동일한 가상 네트워크 내의 다른 서브넷에 배포된 애플리케이션은 유연한 서버에 직접 액세스할 수 있습니다.
- 다른 가상 네트워크(VNet-2)에 배포된 애플리케이션은 유연한 서버에 직접 액세스할 수 없습니다. 유연한 서버에 액세스하려면 먼저 [프라이빗 DNS 영역에 대한 가상 네트워크 피어링](#private-dns-zone-and-virtual-network-peering)을 수행해야 합니다.
   
### <a name="virtual-network-concepts"></a>가상 네트워크 개념

Azure Virtual Network에는 사용자가 사용하도록 구성된 개인 IP 주소 공간이 포함되어 있습니다. 가상 네트워크는 유연한 서버와 같은 Azure 지역에 있어야 합니다. 가상 네트워크에 대한 자세한 내용은 [Azure Virtual Network 개요](../../virtual-network/virtual-networks-overview.md)를 참조하세요.

다음은 PostgreSQL 유연한 서버에서 가상 네트워크를 사용할 때 알아야 할 몇 가지 개념입니다.

* **위임된 서브넷**. 가상 네트워크에는 서브넷(하위 네트워크)이 포함됩니다. 서브넷을 사용하면 가상 네트워크를 더 작은 주소 공간으로 분할할 수 있습니다. Azure 리소스는 가상 네트워크 내의 특정 서브넷에 배포됩니다. 

  유연한 서버는 *위임* 된 서브넷에 있어야 합니다. 즉, Azure Database for PostgreSQL - 유연한 서버 인스턴스만 해당 서브넷을 사용할 수 있습니다. 다른 Azure 리소스 유형은 위임된 서브넷에 있을 수 없습니다. 서브넷의 위임 속성을 `Microsoft.DBforPostgreSQL/flexibleServers`로 할당하여 서브넷을 위임합니다.

  > [!IMPORTANT]
  > `AzureFirewallSubnet`, `AzureFirewallManagementSubnet`, `AzureBastionSubnet` 및 `GatewaySubnet` 이름은 Azure 내에 예약되어 있습니다. 이들 중 어느 것도 서브넷 이름으로 사용하지 마세요.

* **NSG(네트워크 보안 그룹)** . NSG의 보안 규칙을 사용하면 가상 네트워크 서브넷 및 네트워크 인터페이스 내외부로 이동할 수 있는 네트워크 트래픽 유형을 필터링할 수 있습니다. 자세한 내용은 [NSG 개요](../../virtual-network/network-security-groups-overview.md)를 참조하세요.

  ASG(애플리케이션 보안 그룹)를 사용하면 플랫 네트워크에 NSG를 사용하여 Layer-4 보안을 쉽게 제어할 수 있습니다. 신속하게 다음을 수행할 수 있습니다.
  
  - 가상 머신을 ASG에 조인시키거나 ASG에서 가상 머신을 제거합니다.
  - 해당 가상 머신에 규칙을 동적으로 적용하거나 해당 가상 머신에서 규칙을 제거합니다. 
  
  자세한 내용은 [ASG 개요](../../virtual-network/application-security-groups.md)를 참조하세요. 
  
  현재 ASG가 Azure Database for PostgreSQL - 유연한 서버를 사용한 규칙의 일부인 NSG는 지원하지 않습니다. 현재 NSG에서 [IP 기반 원본 또는 대상 필터링](../../virtual-network/network-security-groups-overview.md#security-rules)을 사용하는 것이 좋습니다. 

  > [!IMPORTANT]
  > Azure Database for PostgreSQL 고가용성 기능 - 유연한 서버에는 로그 보관을 위해 Azure Database for PostgreSQL - 유연한 서버가 배포된 Azure 가상 네트워크 서브넷 내의 대상 포트 5432, 6432로 트래픽을 보내고 받는 기능이 필요합니다. [NSG(네트워크 보안 그룹)를](../../virtual-network/network-security-groups-overview.md) 만들어 Azure Database for PostgreSQL - 유연한 서버가 배포된 서브넷 내에서 트래픽 흐름을 거부하는 경우 서브넷 내의 대상 포트 5432 및 6432 및 Azure Storage에 대한 트래픽을 대상으로 Azure Storage [서비스 태그를](../../virtual-network/service-tags-overview.md) 사용하여 Azure Storage로의 트래픽을 허용해야 합니다.

* **프라이빗 DNS 영역 통합**. Azure 프라이빗 DNS 영역 통합을 통해 프라이빗 DNS 영역이 연결된 모든 지역 내 피어링된 가상 네트워크 또는 현재 가상 네트워크 내의 프라이빗 DNS를 확인할 수 있습니다. 

### <a name="using-a-private-dns-zone"></a>프라이빗 DNS 영역 사용

Azure Portal 또는 Azure CLI를 사용하여 가상 네트워크를 통해 유연한 서버를 만드는 경우 제공한 서버 이름을 사용하여 구독의 각 서버에 대해 새 프라이빗 DNS 영역이 자동으로 프로비저닝됩니다. 

Azure API, ARM 템플릿(Azure Resource Manager 템플릿) 또는 Terraform을 사용하는 경우 `postgres.database.azure.com`으로 끝나는 프라이빗 DNS 영역을 만듭니다. 프라이빗 액세스로 유연한 서버를 구성하는 동안 이러한 영역을 사용합니다. 자세한 내용은 [프라이빗 DNS 영역 개요](../../dns/private-dns-overview.md)를 참조하세요.


 Azure 가상 네트워크에서 프라이빗 네트워크 액세스를 사용하는 경우 API, ARM 및 Terraform을 비롯한 다양한 인터페이스에서 프라이빗 DNS 영역 정보를 제공해야 합니다.  따라서 API, ARM 또는 Terraform을 사용하여 프라이빗 네트워크 액세스를 사용하는 새로운 Azure Database for PostgreSQL 유연한 서버를 만들려면 프라이빗 DNS 영역을 만들고 프라이빗 액세스로 유연한 서버를 구성하는 동안 사용합니다. Microsoft Azure REST API [사양에 대한](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/postgresql/resource-manager/Microsoft.DBforPostgreSQL/preview/2021-06-01-preview/postgresql.json)자세한 내용을 참조하세요. 유연한 서버를 만들기 위해 [Azure Portal](./how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](./how-to-manage-virtual-network-cli.md) 사용하는 경우 이전에 동일하거나 다른 구독에서 만든 프라이빗 DNS 영역 이름을 제공하거나 기본 프라이빗 DNS 영역이 구독에 자동으로 만들어집니다.



### <a name="integration-with-a-custom-dns-server"></a>사용자 지정 DNS 서버와 통합

사용자 지정 DNS 서버를 사용하는 경우에는 DNS 전달자를 사용하여 Azure Database for PostgreSQL - 유연한 서버의 FQDN을 확인해야 합니다. 전달자 IP 주소는 [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)이어야 합니다. 

사용자 지정 DNS 서버는 가상 네트워크 내부에 있거나 가상 네트워크의 DNS 서버 설정을 통해 연결할 수 있어야 합니다. 자세한 내용은 [자체 DNS 서버를 사용하는 이름 확인](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)을 참조하세요.

### <a name="private-dns-zone-and-virtual-network-peering"></a>프라이빗 DNS 영역 및 가상 네트워크 피어링

프라이빗 DNS 영역 설정과 가상 네트워크 피어링은 서로 독립적입니다. 동일한 지역 또는 다른 지역의 다른 가상 네트워크에 프로비저닝된 클라이언트에서 유연한 서버에 연결하려면 프라이빗 DNS 영역을 가상 네트워크와 연결해야 합니다. 자세한 내용은 [가상 네트워크 연결](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network)을 참조하세요.

> [!NOTE]
> `postgres.database.azure.com`으로 끝나는 프라이빗 DNS 영역 이름만 연결할 수 있습니다.

### <a name="unsupported-virtual-network-scenarios"></a>지원되지 않는 가상 네트워크 시나리오

다음은 가상 네트워크 작업에 대한 몇 가지 제한 사항입니다.

* 가상 네트워크에 배포된 유연한 서버는 퍼블릭 엔드포인트(또는 공용 IP 또는 DNS)를 포함할 수 없습니다.
* 유연한 서버가 가상 네트워크 및 서브넷에 배포된 후에는 다른 가상 네트워크 또는 서브넷으로 이동할 수 없습니다. 가상 네트워크를 다른 리소스 그룹 또는 구독으로 이동할 수 없습니다.
* 서브넷에 리소스가 있으면 서브넷 크기(주소 공간)를 늘릴 수 없습니다.
* 유연한 서버는 Azure Private Link를 지원하지 않습니다. 대신 가상 네트워크 삽입을 사용하여 가상 네트워크 내에서 유연한 서버를 사용할 수 있도록 합니다. 


## <a name="public-access-allowed-ip-addresses"></a>퍼블릭 액세스(허용된 IP 주소)

퍼블릭 액세스 방법을 선택하면 인터넷에서 퍼블릭 엔드포인트를 통해 유연한 서버에 액세스합니다. 퍼블릭 엔드포인트는 공개적으로 확인할 수 있는 DNS 주소입니다. *허용되는 IP 주소* 라는 말은 선택하는 IP 주소 범위에 서버 액세스 권한을 부여한다는 뜻입니다. 이러한 권한을 *방화벽 규칙* 이라고 합니다. 

다음 기능을 원하는 경우 이 네트워킹 옵션을 선택합니다.

* 가상 네트워크를 지원하지 않는 Azure 리소스에서 연결
* VPN 또는 ExpressRoute로 연결되지 않은 Azure 외부의 리소스에서 연결
* 유연한 서버에 인터넷을 통해 액세스할 수 있는 퍼블릭 엔드포인트가 있는지 확인합니다.

퍼블릭 액세스 메서드의 특성은 다음과 같습니다.

* 허용하는 IP 주소만 PostgreSQL 유연한 서버에 액세스할 권한이 있습니다. 기본적으로 IP 주소는 허용되지 않습니다. 서버를 만드는 동안 또는 나중에 IP 주소를 추가할 수 있습니다.
* PostgreSQL 서버는 공개적으로 확인할 수 있는 DNS 이름이 있습니다.
* 유연한 서버는 Azure 가상 네트워크 중 하나에 있지 않습니다.
* 서버에서 들어오고 나가는 네트워크 트래픽은 개인 네트워크를 통해 이동하지 않습니다. 트래픽은 일반적인 인터넷 경로를 사용합니다.

### <a name="firewall-rules"></a>방화벽 규칙

방화벽 규칙을 통해 허용하지 않은 IP 주소에서 연결 시도가 발생하면 원래 클라이언트에 오류가 발생합니다.

### <a name="allowing-all-azure-ip-addresses"></a>모든 Azure IP 주소 허용

고정된 발신 IP 주소를 Azure 서비스에 사용할 수 없는 경우 모든 Azure 데이터 센터의 모든 IP 주소에서 연결을 사용하도록 설정하는 것이 좋습니다.

> [!IMPORTANT]
> **Azure 서비스 및 Azure 내의 리소스에서 퍼블릭 액세스 허용** 옵션은 다른 고객 구독의 연결을 포함하여 Azure에서의 모든 연결을 허용하도록 방화벽을 구성합니다. 이 옵션을 선택할 때 로그인 및 사용자 권한이 부여된 사용자만으로 액세스를 제한하는지 확인합니다.

### <a name="troubleshooting-public-access-issues"></a>퍼블릭 액세스 문제 해결
Azure Database for PostgreSQL 서비스로의 액세스가 예상대로 작동되지 않는 경우 다음 사항을 고려하세요.

* **허용 목록의 변경 내용이 아직 적용되지 않았습니다.** Azure Database for PostgreSQL 서버 방화벽 구성에 변경 내용이 적용되려면 최대 5분 정도 걸릴 수 있습니다.

* **인증 실패**. 사용자에 Azure Database for PostgreSQL 서버에 대한 권한이 없거나 암호가 틀렸을 경우 Azure Database for PostgreSQL 서버에 대한 연결이 거부됩니다. 방화벽 설정은 클라이언트에게 서버에 연결을 시도할 수 있는 기회를 제공합니다. 각 클라이언트는 꼭 필요한 보안 자격 증명을 제공해야 합니다.

* **동적 클라이언트 IP 주소가 액세스를 차단하고 있습니다**. 동적 IP 주소 지정을 통해 인터넷에 연결되어 있고 방화벽을 통과하는 데 문제가 있는 경우 다음 솔루션 중 하나를 시도합니다.

   * Azure Database for PostgreSQL 서버에 액세스하는 클라이언트 컴퓨터에 할당된 IP 주소 범위는 ISP(인터넷 서비스 공급자)에 문의하세요. 그런 다음 IP 주소 범위를 방화벽 규칙으로 추가합니다.
   * 클라이언트 컴퓨터 대신 고정 IP 지정을 가져온 다음 고정 IP 주소를 방화벽 규칙으로 추가합니다.

* **IPv6 형식에는 방화벽 규칙을 사용할 수 없습니다.** 방화벽 규칙은 IPv4 형식이어야 합니다. IPv6 형식으로 방화벽 규칙을 지정하면 유효성 검사 오류가 발생합니다.

## <a name="host-name"></a>호스트 이름

선택한 네트워킹 옵션에 관계없이 유연한 서버에 연결할 때 항상 FQDN을 호스트 이름으로 사용하는 것이 좋습니다. 서버의 IP 주소는 고정적으로 유지된다는 보장이 없습니다. FQDN을 사용하면 연결 문자열을 변경하지 않아도 됩니다. 

FQDN을 호스트 이름으로 사용하는 예는 `hostname = servername.postgres.database.azure.com`입니다. 가능하면 `hostname = 10.0.0.4`(프라이빗 주소) 또는 `hostname = 40.2.45.67`(퍼블릭 주소)은 사용하지 않습니다.


## <a name="tls-and-ssl"></a>TLS 및 SSL

Azure Database for PostgreSQL - 유연한 서버는 TLS(전송 계층 보안)를 사용한 PostgreSQL 서비스에 대한 클라이언트 애플리케이션 연결을 적용합니다. TLS는 데이터베이스 서버와 클라이언트 애플리케이션 간에 암호화된 네트워크 연결을 보장하는 업계 표준 프로토콜입니다. TLS는 SSL(Secure Sockets Layer)의 업데이트된 프로토콜입니다. 

Azure Database for PostgreSQL은 TLS 1.2 이상을 지원합니다. [RFC 8996](https://datatracker.ietf.org/doc/rfc8996/)에서 IETF(Internet Engineering Task Force)는 TLS 1.0 및 TLS 1.1을 사용해서는 안 된다고 명시하고 있습니다. 두 프로토콜 모두 2019년 말부터 더 이상 사용되지 않습니다.

TLS 1.0 및 TLS 1.1와 같은 이전 버전의 TLS 프로토콜을 사용 하는 들어오는 모든 연결은 기본적으로 거부 됩니다. 

> [!NOTE]
> SSL 및 TLS 인증서는 최신 암호화 프로토콜을 사용 하 여 연결을 보호 하는 것을 인증 합니다. 네트워크에서 연결을 암호화 하 여 전송 중에 데이터에 대 한 무단 액세스를 방지 합니다. 따라서 최신 버전의 TLS를 사용 하 여 Azure Database for PostgreSQL 유연한 서버에 대 한 연결을 암호화 하는 것이 좋습니다. 권장 하지는 않지만 필요한 경우 **require_secure_transport** server 매개 변수를 OFF로 업데이트 하 여 Azure Database for PostgreSQL 유연한 서버에 대 한 연결에 대해 TLS\SSL를 사용 하지 않도록 설정 하는 옵션이 있습니다. **Ssl_min_protocol_version** 및 **ssl_max_protocol_version** server 매개 변수를 설정 하 여 TLS 버전을 설정할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Portal](how-to-manage-virtual-network-portal.md) 또는 [Azure CLI](how-to-manage-virtual-network-cli.md)에서 **프라이빗 액세스(VNet 통합)** 를 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.
* [Azure Portal](how-to-manage-firewall-portal.md) 또는 [Azure CLI](how-to-manage-firewall-cli.md)에서 **퍼블릭 액세스(허용된 IP 주소)** 옵션을 사용하여 유연한 서버를 만드는 방법에 대해 알아봅니다.