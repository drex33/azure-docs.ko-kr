---
title: 애플리케이션을 SQL Managed Instance에 연결
titleSuffix: Azure SQL Managed Instance
description: 이 문서에서는 Azure SQL Managed Instance에 애플리케이션을 연결하는 방법을 설명합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: connect
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma, bonova, vanto
ms.date: 08/20/2021
ms.openlocfilehash: d4c86b555502d662e681fcb5904426a7a891883d
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893465"
---
# <a name="connect-your-application-to-azure-sql-managed-instance"></a>애플리케이션을 Azure SQL Managed Instance에 연결
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

현재 애플리케이션을 호스트하는 방법과 위치를 결정하는 여러 가지 옵션이 있습니다.

Azure App Service 또는 Azure 가상 네트워크 통합 옵션 중 일부(예: Azure App Service Environment, Azure Virtual Machines, 가상 머신 확장 집합)를 사용하여 클라우드에서 애플리케이션을 호스트할 수 있습니다. 하이브리드 클라우드 접근 방법을 사용하고 애플리케이션을 온-프레미스에 유지할 수도 있습니다.

어떤 방법을 선택하든지 Azure SQL Managed Instance에 연결할 수 있습니다. 

이 문서는 다양한 애플리케이션 시나리오의 가상 네트워크에서 애플리케이션을 Azure SQL Managed Instance에 연결하는 방법을 설명합니다. 

> [!IMPORTANT]
> 가상 네트워크 외부에서 관리되는 인스턴스에 대한 데이터 액세스를 사용하도록 설정할 수도 있습니다. 관리되는 인스턴스의 퍼블릭 엔드포인트를 사용하여 VPN에 연결되지 않은 Power BI, Azure App Service 또는 온-프레미스 네트워크와 같은 다중 테넌트 Azure 서비스에서 관리되는 인스턴스에 액세스할 수 있습니다. 관리되는 인스턴스에서 퍼블릭 엔드포인트를 사용하도록 설정하고 관리되는 인스턴스 서브넷과 연결된 네트워크 보안 그룹에서 퍼블릭 엔드포인트 트래픽을 허용해야 합니다. [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](./public-endpoint-configure.md)에 대한 좀 더 중요한 세부 정보를 참조하세요. 

![고가용성](./media/connect-application-instance/application-deployment-topologies.png)


## <a name="connect-inside-the-same-vnet"></a>동일한 VNet 내에서 연결

SQL Managed Instance와 동일한 가상 네트워크 내에 애플리케이션을 연결하는 것이 가장 간단한 시나리오입니다. 가상 네트워크 내의 가상 머신은 다른 서브넷 내에 있더라도 서로 직접 연결할 수 있습니다. 즉, App Service Environment 또는 가상 머신 내 애플리케이션을 연결하려면 연결 문자열을 적절하게 설정하기만 하면 됩니다.  

## <a name="connect-inside-a-different-vnet"></a>다른 VNet 내에서 연결

SQL Managed Instance에서 다른 가상 네트워크 내에 있는 경우 애플리케이션을 연결하는 것은 SQL Managed Instance가 자체 가상 네트워크에 개인 IP 주소를 포함하기 때문에 좀 더 복잡합니다. 연결하려면 SQL Managed Instance를 배포할 때 애플리케이션이 가상 네트워크에 액세스해야 합니다. 따라서 애플리케이션과 SQL Managed Instance 가상 네트워크가 연결되어야 합니다. 이 시나리오가 작동하기 위해 가상 네트워크가 동일한 구독에 있을 필요는 없습니다.

가상 네트워크를 연결하는 두 가지 옵션이 있습니다.

- [Azure VNet 피어링](../../virtual-network/virtual-network-peering-overview.md)
- VNet 간 VPN 게이트웨이([Azure Portal](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-vnet-vnet-cli.md))

피어링이 Microsoft 백본 네트워크를 사용하기 때문에 피어링은 적합한 기능입니다. 따라서 연결성 관점에서 피어링된 가상 네트워크 및 동일한 가상 네트워크에 있는 가상 머신 간에 대기 시간이 눈에 띄는 차이점은 없습니다. 가상 네트워크 피어링은 동일한 지역의 네트워크 간에 지원됩니다. 또한 글로벌 가상 네트워크 피어링은 아래 참고에 설명된 제한 사항으로 지원됩니다.  

> [!IMPORTANT]
> [2020년 9월 22일에 새로 만든 가상 클러스터의 글로벌 가상 네트워크 피어링에 대한 지원을 발표했습니다](https://azure.microsoft.com/updates/global-virtual-network-peering-support-for-azure-sql-managed-instance-now-available/). 즉, 발표일 이후부터는 빈 서브넷에서 생성된 SQL 관리형 인스턴스와 해당 서브넷에서 생성된 모든 후속 관리형 인스턴스에 대해 글로벌 가상 네트워크 피어링이 지원됩니다. 다른 모든 SQL 관리형 인스턴스의 경우 피어링 지원은 [글로벌 가상 네트워크 피어링의 제약 조건](../../virtual-network/virtual-network-manage-peering.md#requirements-and-constraints)으로 인해 같은 지역의 네트워크로 제한됩니다. 자세한 내용은 [Azure 가상 네트워크에 대해 자주 묻는 질문](../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers) 문서의 관련 섹션을 참조하세요. 발표 날짜 이전에 생성된 가상 클러스터에서 SQL 관리형 인스턴스에 대해 글로벌 가상 네트워크 피어링을 사용하려면 해당 인스턴스에서 [유지 관리 기간](../database/maintenance-window.md)을 구성하는 것이 좋습니다. 그러면 인스턴스가 글로벌 가상 네트워크 피어링을 지원하는 새 가상 클러스터로 이동합니다.

## <a name="connect-from-on-premises"></a>온-프레미스에서 연결 

가상 네트워크(개인 IP 주소)를 통해 온-프레미스 애플리케이션을 SQL Managed Instance에 연결할 수도 있습니다. 온-프레미스에서 액세스하기 위해 애플리케이션과 SQL Managed Instance 가상 네트워크 간에 사이트 간 연결을 만들어야 합니다. 가상 네트워크 외부에서 관리되는 인스턴스에 대한 데이터 액세스는 [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](./public-endpoint-configure.md)을 참조하세요.

온-프레미스를 Azure 가상 네트워크에 연결하는 방법에는 두 개의 옵션이 있습니다.

- 사이트 간 VPN 연결([Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md), [PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md), [Azure CLI](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli.md))
- [Azure ExpressRoute](../../expressroute/expressroute-introduction.md) 연결  

온-프레미스와 Azure 연결을 성공적으로 설정하고 SQL Managed Instance에 연결을 설정할 수 없는 경우 리디렉션을 위해 방화벽에서 11000~11999의 포트 범위뿐만 아니라 SQL 포트 1433의 아웃바운드 연결이 열려 있는지 확인합니다.

## <a name="connect-the-developer-box"></a>개발자 상자 연결

또한 개발자 상자를 SQL Managed Instance에 연결할 수 있습니다. 따라서 가상 네트워크를 통해 개발자 상자에서 액세스하려면 먼저 개발자 상자와 SQL Managed Instance 가상 네트워크 간에 연결을 만들어야 합니다. 이렇게 하려면 네이티브 Azure 인증서 인증을 사용하여 가상 네트워크t에 지점 및 사이트 간 연결을 구성합니다. 자세한 내용은 [온-프레미스 컴퓨터에서 Azure SQL Managed Instance로 연결 지점-사이트 간 연결 구성](point-to-site-p2s-configure.md)을 참조하세요.

가상 네트워크 외부에서 관리되는 인스턴스에 대한 데이터 액세스는 [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](./public-endpoint-configure.md)을 참조하세요.

## <a name="connect-with-vnet-peering"></a>VNet 피어링을 사용하여 연결

고객에 의해 구현되는 또 다른 시나리오는 VPN Gateway가 한 호스팅 SQL Managed Instance의 구독 및 별도 가상 네트워크에 설치된 경우입니다. 그러면 이 두 개의 가상 네트워크가 피어링됩니다. 그러한 구현 방식이 다음 샘플 아키텍처 다이어그램에 나와 있습니다.

![가상 네트워크 피어링](./media/connect-application-instance/vnet-peering.png)

기본 인프라를 설정하고 나면 VPN 게이트웨이가 SQL Managed Instance를 호스트하는 가상 네트워크에서 IP 주소를 볼 수 있도록 일부 설정을 수정해야 합니다. 이렇게 하려면 **피어링 설정** 에서 다음과 같이 매우 구체적으로 변경합니다.

1. VPN 게이트웨이를 호스트하는 가상 네트워크에서 **피어링**, SQL Managed Instance에 대한 피어링된 가상 네트워크 연결로 차례로 이동한 다음, **게이트웨이 전송 허용** 을 클릭합니다.
2. SQL Managed Instance를 호스트하는 가상 네트워크에서 **피어링**, VPN 게이트웨이에 대한 피어링된 가상 네트워크 연결로 차례로 이동한 다음, **원격 게이트웨이 사용** 을 클릭합니다.

## <a name="connect-azure-app-service"></a>Azure App Service 연결 

Azure App Service에서 호스트되는 애플리케이션을 연결할 수도 있습니다. 가상 네트워크를 통해 Azure App Service에서 액세스하려면 먼저 애플리케이션과 SQL Managed Instance 가상 네트워크 간에 연결을 만들어야 합니다. [Azure 가상 네트워크와 앱 통합](../../app-service/overview-vnet-integration.md)을 참조하세요. 가상 네트워크 외부에서 관리되는 인스턴스에 대한 데이터 액세스는 [Azure SQL Managed Instance에서 퍼블릭 엔드포인트 구성](./public-endpoint-configure.md)을 참조하세요. 

가상 네트워크를 통한 Azure App Service 액세스 문제 해결은 [가상 네트워크 및 애플리케이션 문제 해결](../../app-service/overview-vnet-integration.md#troubleshooting)을 참조하세요. 연결을 설정할 수 없는 경우 [네트워킹 구성 동기화](azure-app-sync-network-configuration.md)를 시도하세요.

Azure App Service를 SQL Managed Instance에 연결하는 특수한 경우는 SQL Managed Instance 가상 네트워크에 피어링된 네트워크에 Azure App Service를 통합하는 경우입니다. 해당 경우에는 다음 구성을 설정해야 합니다.

- SQL Managed Instance 가상 네트워크에는 게이트웨이가 없어야 합니다.  
- SQL Managed Instance 가상 네트워크에는 `Use remote gateways` 옵션이 설정되어 있어야 합니다.
- 피어링된 가상 네트워크에는 `Allow gateway transit` 옵션이 설정되어 있어야 합니다.

이 시나리오는 다음 다이어그램에서 설명합니다.

![통합 앱 피어링](./media/connect-application-instance/integrated-app-peering.png)

>[!NOTE]
>가상 네트워크 통합 기능은 ExpressRoute 게이트웨이가 있는 가상 네트워크와 앱을 통합하지 않습니다. ExpressRoute 게이트웨이가 공존 모드로 구성되어 있어도 가상 네트워크 통합은 작동하지 않습니다. ExpressRoute 연결을 통해 리소스에 액세스해야 하는 경우에는 가상 네트워크에서 실행되는 App Service Environment를 사용할 수 있습니다.

## <a name="troubleshooting-connectivity-issues"></a>연결 문제 해결

연결 문제를 해결하려면 다음을 검토합니다.

- 동일한 가상 네트워크이지만 다른 서브넷 내의 Azure 가상 머신에서 SQL Managed Instance에 연결할 수 없는 경우 VM 서브넷에 액세스를 차단할 수도 있는 네트워크 보안 그룹이 설정되어 있는지 확인합니다. 또한 Azure 경계 내에서 리디렉션을 통해 연결하는 데 필요하므로 11000-11999 범위의 포트뿐만 아니라 SQL 포트 1433에 대한 아웃바운드 연결을 엽니다.
- 가상 네트워크와 연결된 경로 테이블에 대해 BGP 전파를 **사용** 으로 설정해야 합니다.
- P2S VPN을 사용할 경우 Azure Portal에서 구성을 확인하여 **수신/송신** 숫자가 보이는지 확인합니다. 0이 아닌 숫자는 Azure가 온-프레미스에서 트래픽을 라우팅하는 것을 나타냅니다.

   ![수신/송신 숫자](./media/connect-application-instance/ingress-egress-numbers.png)

- 클라이언트 머신(VPN 클라이언트를 사용 중인)에 사용자가 액세스해야 하는 모든 가상 네트워크에 대한 경로 항목이 있는지 확인합니다. 경로는 `%AppData%\Roaming\Microsoft\Network\Connections\Cm\<GUID>\routes.txt`에 저장됩니다.

   ![route.txt](./media/connect-application-instance/route-txt.png)

   이 이미지와 같이 관련된 각 가상 네트워크에 대한 항목이 두 개, 포털에서 구성된 VPN 엔드포인트에 대한 항목이 세 개 있습니다.

   경로를 확인하는 또 다른 방법은 다음 명령을 사용하는 것입니다. 출력에는 다양한 서브넷에 대한 경로가 표시됩니다.

   ```cmd
   C:\ >route print -4
   ===========================================================================
   Interface List
   14...54 ee 75 67 6b 39 ......Intel(R) Ethernet Connection (3) I218-LM
   57...........................rndatavnet
   18...94 65 9c 7d e5 ce ......Intel(R) Dual Band Wireless-AC 7265
   1...........................Software Loopback Interface 1
   Adapter===========================================================================

   IPv4 Route Table
   ===========================================================================
   Active Routes:
   Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0       10.83.72.1     10.83.74.112     35
         10.0.0.0    255.255.255.0         On-link       172.26.34.2     43
         10.4.0.0    255.255.255.0         On-link       172.26.34.2     43
   ===========================================================================
   Persistent Routes:
   None
   ```

- 가상 네트워크 피어링을 사용하는 경우 [게이트웨이 전송 허용 및 원격 게이트웨이 사용](#connect-from-on-premises) 설정을 위한 지침을 따랐는지 확인합니다.

- 가상 네트워크 피어링을 사용하여 Azure App Service에서 호스트된 애플리케이션을 연결하고 SQL Managed Instance 가상 네트워크에 공용 IP 주소 범위가 있는 경우 호스트된 애플리케이션 설정에서 아웃바운드 트래픽을 공용 IP 네트워크로 라우팅할 수 있도록 해야 합니다. [지역 가상 네트워크 통합](../../app-service/overview-vnet-integration.md#regional-virtual-network-integration)의 지침을 따릅니다.

## <a name="required-versions-of-drivers-and-tools"></a>드라이버 및 도구의 필요한 버전

SQL Managed Instance에 연결하려면 다음과 같은 버전 이상의 도구와 드라이버를 사용하는 것이 좋습니다.

| 드라이버/도구 | 버전 |
| --- | --- |
|.NET Framework | 4.6.1(또는 .NET Core) |
|ODBC 드라이버| v17 |
|PHP 드라이버| 5.2.0 |
|JDBC 드라이버| 6.4.0 |
|Node.js 드라이버| 2.1.1 |
|OLEDB 드라이버| 18.0.2.0 |
|SSMS| 18.0 [이상](/sql/ssms/download-sql-server-management-studio-ssms) |
|[SMO](/sql/relational-databases/server-management-objects-smo/sql-server-management-objects-smo-programming-guide) | [150](https://www.nuget.org/packages/Microsoft.SqlServer.SqlManagementObjects) 이상 |

## <a name="next-steps"></a>다음 단계

- SQL Managed Instance에 대한 자세한 내용은 [SQL Managed Instance란?](sql-managed-instance-paas-overview.md)을 참조하세요.
- 관리되는 인스턴스를 새로 만드는 방법을 보여 주는 자습서에 대해서는 [관리되는 인스턴스 만들기](instance-create-quickstart.md)를 참조하세요.