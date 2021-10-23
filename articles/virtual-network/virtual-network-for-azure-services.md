---
title: Azure 서비스에 대한 가상 네트워크
titlesuffix: Azure Virtual Network
description: 전용 Azure 서비스를 가상 네트워크에 배포하는 방법과 해당 배포에서 제공하는 기능을 알아봅니다.
services: virtual-network
documentationcenter: na
author: mohnader
ms.service: virtual-network
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/06/2020
ms.author: kumud
ms.openlocfilehash: 811d014f394e4efa59295adea5a688d68a292301
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130257616"
---
# <a name="deploy-dedicated-azure-services-into-virtual-networks"></a>가상 네트워크에 전용 Azure 서비스 배포

[가상 네트워크](virtual-networks-overview.md)에 전용 Azure 서비스를 배포하는 경우 개인 IP 주소를 통해 개인적으로 서비스 리소스와 통신할 수 있습니다.

![가상 네트워크에 배포된 서비스](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

가상 네트워크 내에 서비스를 배포하면 다음과 같은 기능이 제공됩니다.

- 가상 네트워크 내의 리소스는 개인 IP 주소를 통해 개인적으로 서로 통신할 수 있습니다. 가상 네트워크의 가상 머신에서 실행 중인 SQL Server와 HDInsight 간에 데이터를 전송하는 예제입니다.
- 온-프레미스 리소스는 [사이트 간 VPN(VPN Gateway)](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) 또는 [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)를 통해 개인 IP 주소를 사용하여 가상 네트워크의 리소스에 액세스할 수 있습니다.
- 가상 네트워크를 [피어링](virtual-network-peering-overview.md)하여 가상 네트워크의 리소스가 개인 IP 주소로 서로 간에 통신하도록 할 수 있습니다.
- 가상 네트워크의 서비스 인스턴스는 일반적으로 Azure 서비스를 통해 완전히 관리됩니다. 여기에는 리소스의 상태 모니터링 및 로드에 따른 크기 조정이 포함됩니다.
- 서비스 인스턴스는 가상 네트워크의 서브넷에 배포됩니다. 서브넷에 대한 인바운드 및 아웃바운드 네트워크 액세스는 서비스에서 제공하는 지침에 따라 [네트워크 보안 그룹](./network-security-groups-overview.md#network-security-groups)을 통해 열어야 합니다.
- 또한 특정 서비스는 배포되는 서브넷에 제한을 적용하여 정책 애플리케이션을 제한하고 동일한 서브넷 내에서 VM 및 서비스 리소스를 라우팅 또는 결합합니다. 시간이 지남에 따라 변경될 수 있으므로 특정 제한 사항에 대해서는 각 서비스에 문의하세요. 이러한 서비스의 예로는 Azure NetApp Files, 전용 HSM, Azure Container Instances, App Service가 있습니다. 
- 경우에 따라 서비스에서는 서브넷이 특정 서비스를 호스트할 수 있다는 명시적 식별자로써 [위임된 서브넷](virtual-network-manage-subnet.md#add-a-subnet)을 요구할 수 있습니다. 위임하면 서비스는 위임된 서브넷에서 서비스별 리소스를 만들 수 있는 명시적 권한을 부여합니다.
- [위임된 서브넷이 있는 가상 네트워크](/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet)에서 REST API 응답의 예를 참조하세요. 위임된 서브넷 모델을 사용하는 포괄적인 서비스 목록은 [사용 가능한 위임](/rest/api/virtualnetwork/availabledelegations/list) API를 통해 얻을 수 있습니다.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>가상 네트워크에 배포할 수 있는 서비스

|범주|서비스| 전용<sup>1</sup> 서브넷
|-|-|-|
| 컴퓨팅 | 가상 머신: [Linux](/previous-versions/azure/virtual-machines/linux/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) 또는 [Windows](/previous-versions/azure/virtual-machines/windows/infrastructure-example?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[가상 머신 확장 집합](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[클라우드 서비스](/previous-versions/azure/reference/jj156091(v=azure.100)): Virtual Network(클래식)만 해당<br/> [Azure Batch](../batch/nodes-and-pools.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| 예 <br/> 아니요 <br/> 아니요 <br/> 아니요<sup>2</sup>
| 네트워크 | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)  <br/> [Azure Bastion](../bastion/bastion-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[네트워크 가상 어플라이언스](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn)| 예 <br/> 예 <br/> 예 <br/> 예 <br/> 예
|데이터|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Managed Instance](../azure-sql/managed-instance/connectivity-architecture-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| 예 <br/> 예 <br/> 
|분석 | [Azure HDInsight](../hdinsight/hdinsight-plan-virtual-network-deployment.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks?toc=%2fazure%2fvirtual-network%2ftoc.json) |아니요<sup>2</sup> <br/> 아니요<sup>2</sup> <br/> 
| ID | [Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-create-instance.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |예 <br/>
| 컨테이너 | [AKS(Azure Kubernetes Service)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[ACI(Azure Container Instance)](https://www.aka.ms/acivnet)<br/>Azure Virtual Network CNI [플러그 인](https://github.com/Azure/acs-engine/tree/master/examples/vnet)을 사용하는 [Azure Container Service 엔진](https://github.com/Azure/acs-engine)<br/>[Azure Functions](../azure-functions/functions-networking-options.md#virtual-network-integration) |아니요<sup>2</sup><br/> 예 <br/> 아니요 <br/> 예
| 웹 | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Web Apps](../app-service/overview-vnet-integration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service Environment](../app-service/overview-vnet-integration.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|예 <br/> 예 <br/> 예 <br/> 예
| 호스트형 | [Azure 전용 HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|예 <br/> 예 <br/>
| Azure Spring Cloud | [Azure 가상 네트워크에 배포(VNet 삽입)](../spring-cloud/how-to-deploy-in-azure-virtual-network.md)<br/>| 예 <br/>
| | |

<sup>1</sup> '전용'은 서비스 관련 리소스만 이 서브넷에 배포할 수 있으며 고객 VM/VMSS와 함께 결합할 수 없음을 의미합니다. <br/> 
<sup>2</sup> 이러한 서비스를 전용 서브넷에 두는 것이 모범 사례로 권장되지만 서비스에 적용되는 필수 요구 사항은 아닙니다.