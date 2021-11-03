---
title: Azure Virtual WAN의 프라이빗 엔드포인트로 향하는 트래픽 보호
description: 네트워크 규칙 및 애플리케이션 규칙을 사용하여 Azure Virtual WAN의 프라이빗 엔드포인트로 향하는 트래픽을 보호하는 방법에 대해 알아봅니다.
services: firewall-manager
author: KumudD
ms.service: firewall-manager
ms.topic: how-to
ms.date: 04/02/2021
ms.author: kumud
ms.openlocfilehash: 58dd59bc47c15dd5f6b04588b441297b9fbb7968
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068687"
---
# <a name="secure-traffic-destined-to-private-endpoints-in-azure-virtual-wan"></a>Azure Virtual WAN의 프라이빗 엔드포인트로 향하는 트래픽 보호

> [!NOTE]
> 이 문서는 보안 가상 허브에만 적용됩니다. 허브 가상 네트워크에서 Azure Firewall을 사용하여 프라이빗 엔드포인트로 향하는 트래픽을 검사하려면 [Azure Firewall을 사용하여 프라이빗 엔드포인트로 향하는 트래픽 검사](../private-link/inspect-traffic-with-azure-firewall.md)를 참조하세요.

[Azure 프라이빗 엔드포인트](../private-link/private-endpoint-overview.md)는 [Azure Private Link](../private-link/private-link-overview.md)를 만드는 데 사용되는 기본 구성 요소입니다. 프라이빗 엔드포인트를 사용하면 가상 네트워크에 배포된 Azure 리소스가 프라이빗 링크 리소스와 비공개로 통신할 수 있습니다.

프라이빗 엔드포인트를 사용하면 리소스가 가상 네트워크에 배포된 프라이빗 링크 서비스에 액세스할 수 있습니다. 가상 네트워크 피어링 및 온-프레미스 네트워크 연결을 통해 프라이빗 엔드포인트에 액세스하면 연결이 확장됩니다.

Virtual WAN에 연결된 가상 네트워크의 프라이빗 엔드포인트를 통해 노출되는 서비스로 향하는 온-프레미스 또는 Azure 클라이언트의 트래픽을 필터링해야 할 수 있습니다. 이 문서에서는 [Azure Firewall](../firewall/overview.md)을 보안 공급자로 사용하는 [보안 가상 허브](../firewall-manager/secured-virtual-hub.md)를 통해 이 작업을 안내합니다.

Azure 방화벽은 다음 방법 중 하나를 사용하여 트래픽을 필터링합니다.

* TCP 및 UDP 프로토콜에 관한 [네트워크 규칙의 FQDN](../firewall/fqdn-filtering-network-rules.md)
* HTTP, HTTPS 및 MSSQL에 관한 [애플리케이션 규칙의 FQDN](../firewall/features.md#application-fqdn-filtering-rules).
* [네트워크 규칙](../firewall/features.md#network-traffic-filtering-rules)을 사용하는 원본 및 대상 IP 주소, 포트, 프로토콜

네트워크 규칙에 대한 애플리케이션 규칙을 사용하여 프라이빗 엔드포인트로 향하는 트래픽을 검사합니다.
보안 가상 허브는 Microsoft에서 관리하며 [프라이빗 DNS 영역](../dns/private-dns-privatednszone.md)에 연결할 수 없습니다. 이는 [프라이빗 링크 리소스](../private-link/private-endpoint-overview.md#private-link-resource) FQDN의 해당 프라이빗 엔드포인트 IP 주소를 확인하는 데 필요합니다.

SQL FQDN 필터링은 [프록시 모드](../azure-sql/database/connectivity-architecture.md#connection-policy)에서만 지원됩니다(포트 1433). *프록시* 모드는 *리디렉션* 에 비해 더 많은 대기 시간이 발생할 수 있습니다. Azure 내에서 연결하는 클라이언트의 기본값인 리디렉션 모드를 계속 사용하려면 방화벽 네트워크 규칙의 FQDN을 사용하여 액세스를 필터링할 수 있습니다.

## <a name="filter-traffic-using-fqdn-in-network-and-application-rules"></a>네트워크 및 애플리케이션 규칙에서 FQDN을 사용하여 트래픽 필터링

다음 단계에서는 네트워크 및 애플리케이션 규칙에서 Azure Firewall이 FQDN을 사용하여 트래픽을 필터링하도록 허용합니다.

1. 보안 가상 허브에 연결되어 있고 프라이빗 엔드포인트의 A 레코드 유형을 호스팅하는 프라이빗 DNS 영역에 연결된 가상 네트워크에 [DNS 전달자](../private-link/private-endpoint-dns.md#virtual-network-and-on-premises-workloads-using-a-dns-forwarder) 가상 머신을 배포합니다.

2. DNS 전달자 가상 머신의 IP 주소를 가리키도록 [사용자 지정 DNS 설정](../firewall/dns-settings.md#configure-custom-dns-servers---azure-portal)을 구성하고 보안 가상 허브에 배포된 Azure Firewall과 연결된 방화벽 정책에 DNS 프록시를 사용하도록 설정합니다.

3. 보안 가상 허브에 연결된 가상 네트워크의 [사용자 지정 DNS 서버](../virtual-network/manage-virtual-network.md#change-dns-servers)가 보안 가상 허브에 배포된 Azure Firewall과 연결된 개인 IP 주소를 가리키도록 구성합니다.

4. 프라이빗 엔드포인트 공용 DNS 영역에 대한 DNS 쿼리를 보안 가상 허브에 배포된 Azure Firewall과 연결된 개인 IP 주소에 전달하도록 온-프레미스 DNS 서버를 구성합니다.

5. 보안 가상 허브에 배포된 Azure Firewall과 연결된 방화벽 정책에서 FQDN *대상 유형* 과 프라이빗 링크 리소스 공용 FQDN *대상* 을 사용하여 필요에 따라 [애플리케이션 규칙](../firewall/tutorial-firewall-deploy-portal.md#configure-an-application-rule) 또는 [네트워크 규칙](../firewall/tutorial-firewall-deploy-portal.md#configure-a-network-rule)을 구성합니다.

6. 보안 가상 허브에 배포된 Azure Firewall과 연결된 방화벽 정책에서 *보안 가상* 허브로 이동하고, 프라이빗 엔드포인트로 향하는 트래픽 필터링을 구성할 보안 가상 허브를 선택합니다.

7. **보안 구성** 으로 이동하고 **프라이빗 트래픽** 에서 **Azure Firewall을 통해 보내기** 를 선택합니다.

8. **프라이빗 트래픽 접두사** 를 선택하여 보안 가상 허브에서 Azure Firewall을 통해 검사되는 CIDR 접두사를 편집하고 다음과 같이 각 프라이빗 엔드포인트에 /32 접두사 하나를 추가합니다.

   > [!IMPORTANT]
   > 이러한 /32 접두사가 구성되지 않은 경우 프라이빗 엔드포인트로 향하는 트래픽은 Azure Firewall을 무시합니다.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Firewall Manager 보안 구성" border="true":::

이러한 단계는 클라이언트 및 프라이빗 엔드포인트가 동일한 보안 가상 허브에 연결된 서로 다른 가상 네트워크에 배포된 경우와 온-프레미스 클라이언트에만 효과가 있습니다. 클라이언트와 프라이빗 엔드포인트가 동일한 가상 네트워크에 배포된 경우, 프라이빗 엔드포인트에 대해 /32 경로를 사용하는 UDR을 만들어야 합니다. **다음 홉 유형** 을 **가상 어플라이언스** 로 설정하고 **다음 홉 주소** 를 보안 가상 허브에 배포된 Azure Firewall의 개인 IP 주소로 설정하여 이러한 경로를 구성합니다. **게이트웨이 경로 전파** 를 **예** 로 설정해야 합니다.

다음 다이어그램에서는 여러 클라이언트가 Azure Virtual WAN에 배포된 프라이빗 엔드포인트에 연결하는 DNS 및 데이터 트래픽 흐름을 보여 줍니다.

:::image type="content" source="./media/private-link-inspection-secure-virtual-hub/private-link-inspection-virtual-wan-architecture.png" alt-text="트래픽 흐름" border="true":::

## <a name="troubleshooting"></a>문제 해결

보안 가상 허브를 통해 프라이빗 엔드포인트로 향하는 트래픽을 필터링하려고 할 때 발생할 수 있는 주요 문제는 다음과 같습니다.

- 클라이언트에서 프라이빗 엔드포인트에 연결할 수 없습니다.

- Azure Firewall이 우회됩니다. 이 증상은 Azure Firewall에 네트워크 또는 애플리케이션 규칙 로그 항목이 없는지 확인하면 알 수 있습니다.

대부분의 경우 이러한 문제는 다음과 같은 이슈 중 하나로 인해 발생합니다.

- 잘못된 DNS 이름 확인

- 잘못된 라우팅 구성

### <a name="incorrect-dns-name-resolution"></a>잘못된 DNS 이름 확인

1. 가상 네트워크 DNS 서버가 *사용자 지정* 으로 설정되어 있고 IP 주소가 보안 가상 허브에 있는 Azure Firewall의 개인 IP 주소인지 확인합니다.

   Azure CLI:

   ```azurecli-interactive
   az network vnet show --name <VNET Name> --resource-group <Resource Group Name> --query "dhcpOptions.dnsServers"
   ```
2. DNS 전달자 가상 머신과 동일한 가상 네트워크에 있는 클라이언트가 DNS 전달자로 구성된 가상 머신을 직접 쿼리하여 프라이빗 엔드포인트 공용 FQDN의 해당 개인 IP 주소를 확인할 수 있는지 확인합니다.

   Linux:

   ```bash
   dig @<DNS forwarder VM IP address> <Private endpoint public FQDN>
   ```
3. *AzureFirewallDNSProxy* Azure Firewall 로그 항목을 검사하고 클라이언트에서 DNS 쿼리를 수신하고 해결할 수 있는지 확인합니다.

   ```kusto
   AzureDiagnostics
   | where Category contains "DNS"
   | where msg_s contains "database.windows.net"
   ```
4. *DNS 프록시* 가 사용하도록 설정되어 있고 ,보안 가상 허브의 Azure Firewall과 연결된 방화벽 정책에 DNS 전달자 가상 머신 IP 주소의 IP 주소를 가리키는 *사용자 지정* DNS 서버가 구성되어 있는지 확인합니다.

   Azure CLI:

   ```azurecli-interactive
   az network firewall policy show --name <Firewall Policy> --resource-group <Resource Group Name> --query dnsSettings
   ```

### <a name="incorrect-routing-configuration"></a>잘못된 라우팅 구성

1. 보안 가상 허브에 배포된 Azure Firewall과 연결된 방화벽 정책에서 *보안 구성* 을 확인합니다. **프라이빗 트래픽** 열 아래에서 트래픽을 필터링하려는 모든 가상 네트워크 및 분기 연결에 대해 **Azure Firewall로 보호됨** 이 표시되는지 확인합니다.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-policy-private-traffic-configuration.png" alt-text="Azure Firewall로 보호되는 프라이빗 트래픽" border="true":::

2. 보안 가상 허브에 배포된 Azure Firewall과 연결된 방화벽 정책에서 **보안 구성** 을 확인합니다. **프라이빗 트래픽 접두사** 에서 트래픽을 필터링하려는 각 프라이빗 엔드포인트의 개인 IP 주소에 대한 /32 항목이 있는지 확인합니다.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/firewall-manager-security-configuration.png" alt-text="Firewall Manager 보안 구성 - 프라이빗 트래픽 접두사" border="true":::

3. 가상 WAN의 보안 가상 허브에서 트래픽을 필터링하려는 가상 네트워크 및 분기 연결과 관련된 경로 테이블에 대한 유효 경로를 검사합니다. 트래픽을 필터링하려는 각 프라이빗 엔드포인트의 개인 IP 주소에 대한 /32 항목이 있는지 확인합니다.

   :::image type="content" source="./media/private-link-inspection-secure-virtual-hub/secured-virtual-hub-effective-routes.png" alt-text="보안 가상 허브 유효 경로" border="true":::

4. 트래픽을 필터링하려는 가상 네트워크에 배포된 가상 머신과 연결된 NIC의 유효 경로를 검사합니다. 트래픽을 필터링하려는 각 프라이빗 엔드포인트의 개인 IP 주소에 대한 /32 항목이 있는지 확인합니다.
 
   Azure CLI:

   ```azurecli-interactive
   az network nic show-effective-route-table --name <Network Interface Name> --resource-group <Resource Group Name> -o table
   ```
5. 온-프레미스 라우팅 디바이스의 라우팅 테이블을 검사합니다. 프라이빗 엔드포인트가 배포된 가상 네트워크의 주소 공간을 알아보고 있는지 확인합니다.

   Azure Virtual WAN은 방화벽 정책 **보안 구성** 의 **프라이빗 트래픽 접두사** 아래에 구성된 접두사를 온-프레미스에 보급하지 않습니다. /32 항목은 온-프레미스 라우팅 디바이스의 라우팅 테이블에 표시되지 않을 것으로 예상됩니다.

6. **AzureFirewallApplicationRule** 및 **AzureFirewallNetworkRule** Azure Firewall 로그를 검사합니다. 프라이빗 엔드포인트로 향하는 트래픽이 로그되고 있는지 확인합니다.

   **AzureFirewallNetworkRule** 로그 항목에는 FQDN 정보가 포함되지 않습니다. 네트워크 규칙을 검사할 때 IP 주소 및 포트를 기준으로 필터링합니다.

   [Azure Files](../storage/files/storage-files-introduction.md) 프라이빗 엔드포인트로 향하는 트래픽을 필터링할 때 **AzureFirewallNetworkRule** 로그 항목은 클라이언트가 처음으로 파일 공유에 탑재하거나 연결하는 경우에만 생성됩니다. Azure Firewall은 파일 공유의 파일에 대한 [CRUD](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) 작업 로그를 생성하지 않습니다. 클라이언트가 파일 공유에 처음 연결하거나 탑재할 때 열린 영구 TCP 채널을 통해 CRUD 작업이 수행되기 때문입니다.

   애플리케이션 규칙 로그 쿼리 예제:

   ```kusto
   AzureDiagnostics
   | where msg_s contains "database.windows.net"
   | where Category contains "ApplicationRule"
   ```
## <a name="next-steps"></a>다음 단계

- [Azure Firewall을 사용하여 프라이빗 엔드포인트로 향하는 트래픽 검사](../private-link/inspect-traffic-with-azure-firewall.md)
