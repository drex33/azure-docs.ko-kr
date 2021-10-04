---
title: Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인
description: Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 07/16/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 023b6898b5ba7d7d5b457f1e0f9c9051d74a1126
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129399777"
---
# <a name="join-an-azure-ssis-integration-runtime-to-a-virtual-network"></a>Azure-SSIS 통합 런타임을 Azure 가상 네트워크에 조인

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory에서 SSIS(SQL Server Integration Services)를 사용하는 경우 다음과 같은 시나리오에서는 Azure SSIS IR(통합 런타임)을 Azure 가상 네트워크에 조인해야 합니다.

- 자체 호스팅 IR을 프록시로 구성하거나 관리하지 않고 Azure-SSIS IR에서 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소에 연결하려 합니다. 

- IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트를 사용하여 Azure SQL Database에서 또는 프라이빗 엔드포인트를 사용하여 SQL Managed Instance에서 SSISDB(SSIS 카탈로그 데이터베이스)를 호스트하려 합니다. 

- Azure-SSIS IR에서 실행되는 SSIS 패키지의 가상 네트워크 서비스 엔드포인트를 사용하여 구성된 Azure 리소스에 연결하려 합니다.

- Azure-SSIS IR에서 실행되는 SSIS 패키지에서 IP 방화벽 규칙을 사용하여 구성된 데이터 저장소/리소스에 연결하려 합니다.

Data Factory를 사용하면 클래식 배포 모델 또는 Azure Resource Manager 배포 모델을 통해 만들어진 가상 네트워크에 Azure SSIS IR을 조인할 수 있습니다.

> [!IMPORTANT]
> 클래식 가상 네트워크는 더 이상 사용되지 않으므로 대신 Azure Resource Manager 가상 네트워크를 사용하세요.  이미 클래식 가상 네트워크를 사용하고 있다면 가능한 한 빨리 Azure Resource Manager 가상 네트워크로 전환하세요.

[가상 네트워크에 조인하도록 Azure SSIS(SQL Server Integration Services) IR(통합 런타임) 구성](tutorial-deploy-ssis-virtual-network.md) 자습서에서는 Azure Portal을 통한 최소 단계를 보여 줍니다. 이 문서는 자습서에서 다루는 것에 더해 다음과 같은 모든 선택적 작업도 설명합니다.

- 가상 네트워크(클래식)를 사용하는 경우.
- Azure-SSIS IR에 자체 공용 IP 주소가 필요한 경우.
- 자체 DNS(Domain Name System) 서버를 사용하는 경우.
- 서브넷에서 NSG(네트워크 보안 그룹)를 사용하는 경우.
- Azure ExpressRoute 또는 UDR(사용자 정의 경로)을 사용하는 경우.
- 사용자 지정 Azure-SSIS IR을 사용하는 경우.
- Azure PowerShell 프로비저닝을 사용하는 경우

## <a name="access-to-on-premises-data-stores"></a>온-프레미스 데이터 저장소 액세스

SSIS 패키지가 온-프레미스 데이터 저장소에 액세스하는 경우 Azure-SSIS IR을 온-프레미스 네트워크에 연결된 가상 네트워크에 조인할 수 있습니다. 또는 자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성하고 관리할 수 있습니다. 자세한 내용은 [자체 호스팅 IR을 Azure-SSIS IR의 프록시로 구성](./self-hosted-integration-runtime-proxy-ssis.md)을 참조하세요. 

Azure-SSIS IR을 가상 네트워크에 조인하는 경우 다음과 같은 중요한 사항을 명심해야 합니다. 

- 온-프레미스 네트워크에 연결된 가상 네트워크가 없는 경우 먼저 Azure-SSIS IR이 조인할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만듭니다. 그런 다음 해당 가상 네트워크에서 온-프레미스 네트워크로 사이트 간 [VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) 또는 [ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 연결을 구성합니다. 

- Azure-SSIS IR과 같은 위치의 온-프레미스 네트워크에 Azure Resource Manager 가상 네트워크가 이미 연결되어 있는 경우 IR을 해당 가상 네트워크에 조인할 수 있습니다. 

- Azure-SSIS IR과 다른 위치의 온-프레미스 네트워크에 클래식 가상 네트워크가 이미 연결되어 있는 경우 Azure-SSIS IR을 조인할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만들 수 있습니다. 그런 다음, [클래식-Azure Resource Manager 가상 네트워크](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 연결을 구성합니다. 
 
- Azure-SSIS IR과 다른 위치의 온-프레미스 네트워크에 Azure Resource Manager 가상 네트워크가 이미 연결되어 있는 경우 먼저 Azure-SSIS IR이 조인할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만들 수 있습니다. 그런 다음 [Azure Resource Manager-Azure Resource Manager 가상 네트워크](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 연결을 구성합니다. 

## <a name="hosting-the-ssis-catalog-in-sql-database"></a>SQL Database에서 SSIS 카탈로그 호스트

가상 네트워크 서비스 엔드포인트가 있는 Azure SQL Database에서 SSIS 카탈로그를 호스트하는 경우 Azure-SSIS IR을 동일한 가상 네트워크 및 서브넷에 연결해야 합니다.

프라이빗 엔드포인트가 있는 SQL Managed Instance에서 SSIS 카탈로그를 호스트하는 경우 관리되는 인스턴스와 다른 서브넷에 있는 동일한 가상 네트워크에 Azure-SSIS IR을 조인해야 합니다. Azure-SSIS IR을 SQL Managed Instance와 다른 가상 네트워크에 조인하는 경우에는 가상 네트워크 피어링(동일한 지역으로 제한됨) 또는 가상 네트워크 간 연결을 사용하는 것이 좋습니다. 자세한 내용은 [애플리케이션을 Azure SQL Managed Instance에 연결](../azure-sql/managed-instance/connect-application-instance.md)을 참조하세요.

## <a name="access-to-azure-services"></a>Azure 서비스에 대한 액세스

[가상 네트워크 서비스 엔드포인트](../virtual-network/virtual-network-service-endpoints-overview.md)를 지원하는 Azure 리소스에 SSIS 패키지가 액세스하고 Azure-SSIS IR에서 해당 리소스에 대한 액세스를 보호하려는 경우 가상 네트워크 서비스 엔드포인트에 대해 구성된 가상 네트워크 서브넷에 Azure-SSIS IR을 조인한 다음 동일한 서브넷에서의 액세스를 허용하는 가상 네트워크 규칙을 관련 Azure 리소스에 추가할 수 있습니다.

## <a name="access-to-data-sources-protected-by-ip-firewall-rule"></a>IP 방화벽 규칙으로 보호되는 데이터 원본에 대한 액세스

SSIS 패키지가 특정 고정 공용 IP 주소만 허용하는 데이터 저장소/리소스에 액세스하고 Azure-SSIS IR에서 해당 리소스에 대한 액세스를 보호하려는 경우, Azure-SSIS IR을 가상 네트워크에 조인할 때 [공용 IP 주소](../virtual-network/virtual-network-public-ip-address.md)를 가상 네트워크에 연결한 다음 해당 IP 주소에서의 액세스를 허용하는 IP 방화벽 규칙을 관련 리소스에 추가할 수 있습니다. 이렇게 하는 두 가지 방법이 있습니다. 

- Azure-SSIS IR 만들 때 고유한 공용 IP 주소를 [가져와서 Azure Data Factory Studio UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) 또는 Azure PowerShell [SDK를](join-azure-ssis-integration-runtime-virtual-network-powershell.md)통해 지정할 수 있습니다. Azure-SSIS IR의 아웃바운드 인터넷 연결만 제공된 공용 IP 주소를 사용하고, 서브넷의 다른 디바이스는 이를 사용하지 않습니다.
- Azure-SSIS IR이 조인할 서브넷에 [Virtual Network NAT](../virtual-network/nat-gateway/nat-overview.md)를 설정할 수도 있습니다. 그러면 이 서브넷의 모든 아웃바운드 연결이 지정된 공용 IP 주소를 사용합니다.

어느 경우든 가상 네트워크는 Azure Resource Manager 배포 모델을 통해서만 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS 통합 런타임 가상 네트워크 구성 세부 정보](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Azure Data Factory Studio UI를 사용하여 가상 네트워크에 Azure-SSIS 통합 런타임 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell 사용하여 가상 네트워크에 Azure-SSIS 통합 런타임 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 
- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크에서 가상 네트워크 서비스 엔드포인트 또는 SQL Managed Instance와 함께 Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트하는 방법에 대한 지침을 제공합니다. 이 문서는 Azure-SSIS IR을 가상 네트워크에 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보를 가져오는 방법을 보여 줍니다. 반환된 정보의 상태 설명도 제공합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR을 규모 확장하는 방법을 보여줍니다.
