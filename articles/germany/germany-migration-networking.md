---
title: Azure 독일에서 글로벌 Azure로 Azure 네트워크 리소스 마이그레이션
description: 이 문서에서는 Azure 독일에서 글로벌 Azure로 Azure 네트워크 리소스를 마이그레이션하는 방법에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 1e8cb8dd4d167b1671edb8367beb5514104938ac
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "122566189"
---
# <a name="migrate-network-resources-to-global-azure"></a>네트워크 리소스를 글로벌 Azure로 마이그레이션

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

대부분의 네트워킹 서비스는 Azure 독일에서 글로벌 Azure로의 마이그레이션을 지원하지 않습니다. 그러나 사이트 간 VPN을 사용하여 두 클라우드 환경 모두에서 네트워크를 연결할 수 있습니다. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

클라우드 간에 사이트 간 VPN을 설정하는 단계는 온-프레미스 네트워크와 Azure 간에 사이트 간 VPN을 배포하는 단계와 비슷합니다. 두 클라우드에서 게이트웨이를 정의한 다음, 서로 통신하는 방법을 VPN에 알려줍니다. [Azure Portal에서 사이트 간 연결 만들기](../vpn-gateway/tutorial-site-to-site-portal.md)에서는 사이트 간 VPN을 배포하기 위해 완료해야 하는 단계를 설명합니다. 단계 요약은 다음과 같습니다.

1. 가상 네트워크를 정의합니다.
1. 주소 공간을 정의합니다.
1. 서브넷을 정의합니다.
1. 게이트웨이 서브넷을 정의합니다.
1. 가상 네트워크에 대한 게이트웨이를 정의합니다.
1. 로컬 네트워크(로컬 VPN 디바이스)에 대한 게이트웨이를 정의합니다.
1. 로컬 VPN 디바이스를 구성합니다.
1. 연결을 구축합니다.

글로벌 Azure와 Azure 독일 간에 가상 네트워크를 연결하려면 다음을 수행합니다.

1. 글로벌 Azure에서 위 절차의 1~5단계를 완료합니다.
1. Azure 독일에서 1~5단계를 완료합니다.
1. 글로벌 Azure에서 6단계를 완료합니다.
   - Azure 독일에서 VPN 게이트웨이의 공용 IP 주소를 입력합니다.
1. Azure 독일에서 6단계를 완료합니다.
   - 글로벌 Azure에서 VPN 게이트웨이의 공용 IP 주소를 입력합니다.
1. 7단계를 건너뜁니다.
1. 8단계를 완료합니다.

## <a name="virtual-networks"></a>가상 네트워크

현재 가상 네트워크를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 대상 지역에 새 가상 네트워크를 만들고 해당 가상 네트워크로 리소스를 마이그레이션하는 것이 좋습니다.

추가 정보는 다음 항목을 참조하세요.

- [Azure Virtual Network 자습서](../virtual-network/index.yml)를 완료하여 지식을 새로 고칩니다.
- [가상 네트워크 개요](../virtual-network/virtual-networks-overview.md)를 검토합니다.
- [가상 네트워크를 계획](../virtual-network/virtual-network-vnet-plan-design-arm.md)하는 방법을 알아봅니다.

## <a name="network-security-groups"></a>네트워크 보안 그룹

현재 네트워크 보안 그룹을 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 대상 지역에 새 네트워크 보안 그룹을 만들고 새 애플리케이션 환경에 네트워크 보안 그룹 규칙을 적용하는 것이 좋습니다.

포털에서 또는 다음 PowerShell 명령을 실행하여 네트워크 보안 그룹의 현재 구성을 가져옵니다.

```powershell
$nsg=Get-AzNetworkSecurityGroup -ResourceName <nsg-name> -ResourceGroupName <resourcegroupname>
Get-AzNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg
```

추가 정보는 다음 항목을 참조하세요.

- [네트워크 보안 그룹에 대한 지식](../virtual-network/network-security-groups-overview.md#network-security-groups)을 새로 고칩니다.
- [네트워크 보안 개요](../virtual-network/network-security-groups-overview.md)를 검토합니다.
- [네트워크 보안 그룹을 관리](../virtual-network/manage-network-security-group.md)하는 방법을 알아봅니다.

## <a name="expressroute"></a>ExpressRoute

현재 Azure ExpressRoute 인스턴스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 글로벌 Azure에서 새 ExpressRoute 회로 및 새 ExpressRoute 게이트웨이를 만드는 것이 좋습니다.

추가 정보는 다음 항목을 참조하세요.

- [ExpressRoute 자습서](../expressroute/index.yml)를 완료하여 지식을 새로 고칩니다.
- [새 ExpressRoute 게이트웨이를 만드는](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md) 방법을 알아봅니다.
- [ExpressRoute 위치 및 서비스 공급자](../expressroute/expressroute-locations.md)에 대해 알아봅니다.
- [ExpressRoute에 대한 가상 네트워크 게이트웨이](../expressroute/expressroute-about-virtual-network-gateways.md)에 대해 읽어봅니다.

## <a name="vpn-gateway"></a>VPN Gateway

현재 Azure VPN Gateway 인스턴스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 글로벌 Azure에서 VPN Gateway의 새 인스턴스를 만들고 구성하는 것이 좋습니다.

포털 또는 PowerShell을 사용하여 현재 VPN Gateway 구성에 대한 정보를 수집할 수 있습니다. PowerShell에서 `Get-AzVirtualNetworkGateway*`로 시작하는 cmdlet 세트를 사용합니다.

온-프레미스 구성을 업데이트해야 합니다. 또한 Azure 네트워크 환경을 업데이트한 후 기존 IP 주소 범위에 대한 기존 규칙을 삭제합니다.

추가 정보는 다음 항목을 참조하세요.

- [VPN Gateway 자습서](../vpn-gateway/index.yml)를 완료하여 지식을 새로 고칩니다.
- [사이트 간 연결을 만드는](../vpn-gateway/tutorial-site-to-site-portal.md) 방법을 알아봅니다.
- [Get-AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) PowerShell cmdlet을 검토합니다.
- 블로그 게시물 [사이트 간 연결 만들기](/archive/blogs/ralfwi/connecting-clouds)를 읽어봅니다.
 
## <a name="application-gateway"></a>Application Gateway

현재 Azure Application Gateway 인스턴스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 글로벌 Azure에서 새 게이트웨이를 만들고 구성하는 것이 좋습니다.

포털 또는 PowerShell을 사용하여 현재 게이트웨이 구성에 대한 정보를 수집할 수 있습니다. PowerShell에서 `Get-AzApplicationGateway*`로 시작하는 cmdlet 세트를 사용합니다.

추가 정보는 다음 항목을 참조하세요.

- [Application Gateway 자습서](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md)를 완료하여 지식을 새로 고칩니다.
- [애플리케이션 게이트웨이를 만드는](../application-gateway/quick-create-portal.md) 방법을 알아봅니다.
- [Get-AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) PowerShell cmdlet을 검토합니다.

## <a name="dns"></a>DNS

Azure DNS 구성을 Azure 독일에서 글로벌 Azure로 마이그레이션하려면 DNS 영역 파일을 내보낸 다음, 새 구독으로 가져옵니다. 현재 영역 파일을 내보내는 유일한 방법은 Azure CLI를 사용하는 것입니다.

Azure 독일에서 원본 구독에 로그인한 후 Azure Resource Manager 모드를 사용하도록 Azure CLI를 구성합니다. 다음 명령을 실행하여 영역을 내보냅니다.

```azurecli
az network dns zone export -g <resource group> -n <zone name> -f <zone file name>
```

예:

```azurecli
az network dns zone export -g "myresourcegroup" -n "contoso.com" -f "contoso.com.txt"
```

이 명령은 Azure DNS 서비스를 호출하여 리소스 그룹 `myresourcegroup`에서 `contoso.com` 영역을 내보냅니다. 출력은 현재 폴더의 contoso.com.txt에 BIND 호환 영역 파일로 저장됩니다.

내보내기가 완료되면 영역 파일에서 NS 레코드를 삭제합니다. 새 지역 및 구독에 대한 새 NS 레코드가 만들어집니다.

이제 대상 환경에 로그인하고 새 리소스 그룹을 만들거나 기존 리소스 그룹을 선택한 다음, 영역 파일을 가져옵니다.

```azurecli
az network dns zone import -g <resource group> -n <zone name> -f <zone file name>
```

영역을 가져왔으면 다음 명령을 실행하여 영역의 유효성을 검사해야 합니다.

```azurecli
az network dns record-set list -g <resource group> -z <zone name>
```

유효성 검사가 완료되면 도메인 등록 기관에 문의하여 NS 레코드를 다시 위임합니다. NS 레코드 정보를 가져오려면 다음 명령을 실행합니다.

```azurecli
az network dns record-set ns list -g <resource group> -z --output json
```

추가 정보는 다음 항목을 참조하세요.

- [Azure DNS 자습서](../dns/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Azure DNS 개요](../dns/dns-overview.md)를 검토합니다.
- [Azure DNS 가져오기 및 내보내기](../dns/dns-import-export.md)에 대해 알아봅니다.

## <a name="network-watcher"></a>Network Watcher

현재 Azure Network Watcher 인스턴스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 글로벌 Azure에서 새 Network Watcher 인스턴스를 만들고 구성하는 것이 좋습니다. 그런 다음, 이전 환경과 새 환경 간의 결과를 비교합니다.

추가 정보는 다음 항목을 참조하세요.

- [Network Watcher 자습서](../network-watcher/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Network Watcher 개요](../network-watcher/network-watcher-monitoring-overview.md)를 검토합니다.
- [네트워크 보안 그룹 흐름 로그](../network-watcher/network-watcher-nsg-flow-logging-portal.md)에 대해 자세히 알아봅니다.
- [연결 모니터](../network-watcher/connection-monitor.md)에 대해 읽어봅니다.

## <a name="traffic-manager"></a>Traffic Manager

Azure Traffic Manager는 원활한 마이그레이션을 완료하는 데 도움이 될 수 있습니다. 그러나 Azure 독일에서 만든 Traffic Manager 프로필을 글로벌 Azure로 마이그레이션할 수 없습니다. 마이그레이션하는 동안 Traffic Manager 엔드포인트를 대상 환경으로 마이그레이션하므로 Traffic Manager 프로필을 업데이트해야 합니다.

원본 환경에서 실행 중인 동안 Traffic Manager를 사용하여 대상 환경에서 추가 엔드포인트를 정의할 수 있습니다. Traffic Manager가 새 환경에서 실행 중인 경우 원본 환경에서 아직 마이그레이션하지 않은 엔드포인트를 계속 정의할 수 있습니다. 이 시나리오를 [파란색-녹색 시나리오](https://azure.microsoft.com/blog/blue-green-deployments-using-azure-traffic-manager/)라고 합니다. 시나리오에는 다음 단계가 포함됩니다.

1. 글로벌 Azure에서 새 Traffic Manager 프로필을 만듭니다.
1. Azure 독일에서 엔드포인트를 정의합니다.
1. DNS CNAME 레코드를 새 Traffic Manager 프로필로 변경합니다.
1. 이전 Traffic Manager 프로필을 끕니다.
1. 엔드포인트를 마이그레이션하고 구성합니다. Azure 독일의 각 엔드포인트에 대해 다음을 수행합니다.
   1. 엔드포인트를 글로벌 Azure로 마이그레이션합니다.
   1. 새 엔드포인트를 사용하도록 Traffic Manager 프로필을 변경합니다.

추가 정보는 다음 항목을 참조하세요.

- [Traffic Manager 자습서](../traffic-manager/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Traffic Manager 개요](../traffic-manager/traffic-manager-overview.md)를 검토합니다.
- [Traffic Manager 프로필을 만드는 방법](../traffic-manager/quickstart-create-traffic-manager-profile.md)을 알아보세요.

## <a name="load-balancer"></a>Load Balancer

현재 Azure Load Balancer 인스턴스를 Azure 독일에서 글로벌 Azure로 마이그레이션하는 것은 지원되지 않습니다. 글로벌 Azure에서 새 부하 분산 장치를 만들고 구성하는 것이 좋습니다.

추가 정보는 다음 항목을 참조하세요.

- [Load Balancer 자습서](../load-balancer/index.yml)를 완료하여 지식을 새로 고칩니다.
- [Load Balancer 개요](../load-balancer/load-balancer-overview.md)를 검토합니다.
- [새 부하 분산 장치를 만드는](../load-balancer/quickstart-load-balancer-standard-public-portal.md) 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스를 마이그레이션하기 위한 도구, 기술, 권장 사항에 대해 알아보세요.

- [컴퓨팅](./germany-migration-compute.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)
