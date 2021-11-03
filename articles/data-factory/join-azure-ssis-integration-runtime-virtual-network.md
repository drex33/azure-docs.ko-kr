---
title: 가상 네트워크에 Azure-SSIS 통합 런타임 조인
description: Azure-SSIS 통합 런타임을 가상 네트워크에 조인하는 방법을 알아봅니다.
ms.service: data-factory
ms.subservice: integration-services
ms.topic: conceptual
ms.date: 10/27/2021
author: swinarko
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9766ed3ed870e41c44a2dd84bbaad578ec54509c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131087878"
---
# <a name="join-azure-ssis-integration-runtime-to-a-virtual-network"></a>가상 네트워크에 Azure-SSIS 통합 런타임 조인

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ADF(Azure Data Factory)에서 SSIS(SQL Server Integration Services)를 사용하는 경우 다음과 같은 시나리오에서 Azure-SSIS IR(통합 런타임)을 가상 네트워크에 조인해야 합니다.

- 자체 호스팅 IR을 프록시로 구성하고 관리하지 않고 Azure-SSIS IR 실행되는 SSIS 패키지에서 온-프레미스 데이터 저장소/리소스에 액세스하려고 합니다.

- 프라이빗 엔드포인트/IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트 또는 가상 네트워크를 조인하여 SSISDB(SSIS 카탈로그 데이터베이스)를 호스트하는 Azure SQL Managed Instance 구성된 Azure SQL Database 서버를 사용하려고 합니다.

- Azure-SSIS IR 실행되는 SSIS 패키지에서 프라이빗 엔드포인트/IP 방화벽 규칙/가상 네트워크 서비스 엔드포인트로 구성된 Azure 데이터 저장소/리소스에 액세스하려고 합니다.

- Azure-SSIS IR 실행되는 SSIS 패키지에서 IP 방화벽 규칙으로 구성된 다른 클라우드 데이터 저장소/리소스에 액세스하려고 합니다.

ADF를 사용하면 Azure-SSIS IR Azure Resource Manager 또는 클래식 배포 모델을 통해 만든 가상 네트워크에 조인할 수 있습니다.

> [!IMPORTANT]
> 클래식 가상 네트워크는 더 이상 사용되지 않으므로 대신 Azure Resource Manager 가상 네트워크를 사용하세요. 이미 클래식 가상 네트워크를 사용하고 있다면 가능한 한 빨리 Azure Resource Manager 가상 네트워크로 전환하세요.

[가상 네트워크에 조인하도록 Azure-SSIS IR 구성](tutorial-deploy-ssis-virtual-network.md) 자습서에서는 Azure Portal/ADF UI를 통한 Express 가상 네트워크 삽입 방법을 사용하는 최소 단계를 보여줍니다. 이 문서 및 [Azure-SSIS IR 삽입하도록 가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md) 문서와 같은 다른 문서는 자습서를 확장하고 모든 선택적 단계를 설명합니다.

- 표준 가상 네트워크 삽입 방법을 사용하는 경우
- 클래식 가상 네트워크를 사용하는 경우
- Azure-SSIS IR 대한 BYOIP(고정 공용 IP) 주소를 가져오는 경우
- 사용자 고유의 DNS(도메인 이름 시스템) 서버를 사용하는 경우
- NSG(네트워크 보안 그룹)를 사용하는 경우
- UDR(사용자 정의 경로)을 사용하는 경우
- 사용자 지정 Azure-SSIS IR을 사용하는 경우.
- Azure PowerShell 사용하여 Azure-SSIS IR 프로비전하는 경우

## <a name="access-to-on-premises-data-stores"></a>온-프레미스 데이터 저장소 액세스

SSIS 패키지가 온-프레미스 데이터 저장소에 액세스하는 경우 Azure-SSIS IR을 온-프레미스 네트워크에 연결된 가상 네트워크에 조인할 수 있습니다. 또는 자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성하고 관리할 수 있습니다. 자세한 내용은 [자체 호스팅 IR을 Azure-SSIS IR 프록시로 구성을 참조하세요.](self-hosted-integration-runtime-proxy-ssis.md) 

Azure-SSIS IR을 가상 네트워크에 조인하는 경우 다음과 같은 중요한 사항을 명심해야 합니다. 

- 온-프레미스 네트워크에 연결된 가상 네트워크가 없는 경우 먼저 Azure-SSIS IR이 조인할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만듭니다. 그런 다음, 해당 가상 네트워크에서 온-프레미스 네트워크로의 사이트-사이트 [VPN 게이트웨이 연결](../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) 또는 [Azure ExpressRoute](../expressroute/expressroute-howto-linkvnet-classic.md) 연결을 구성합니다. 

- Azure Resource Manager 가상 네트워크가 Azure-SSIS IR 동일한 위치에 있는 온-프레미스 네트워크에 이미 연결되어 있는 경우 해당 가상 네트워크에 IR을 조인할 수 있습니다. 

- Azure-SSIS IR과 다른 위치의 온-프레미스 네트워크에 클래식 가상 네트워크가 이미 연결되어 있는 경우 Azure-SSIS IR을 조인할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만들 수 있습니다. 그런 다음, [클래식-Azure Resource Manager 가상 네트워크](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md) 연결을 구성합니다. 
 
- Azure-SSIS IR과 다른 위치의 온-프레미스 네트워크에 Azure Resource Manager 가상 네트워크가 이미 연결되어 있는 경우 먼저 Azure-SSIS IR이 조인할 [Azure Resource Manager 가상 네트워크](../virtual-network/quick-create-portal.md#create-a-virtual-network)를 만들 수 있습니다. 그런 다음 [Azure Resource Manager-Azure Resource Manager 가상 네트워크](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md) 연결을 구성합니다. 

## <a name="hosting-ssisdb-in-azure-sql-database-server-or-managed-instance"></a>Azure SQL Database 서버 또는 Managed Instance SSISDB 호스팅

가상 네트워크 서비스 엔드포인트로 구성된 Azure SQL Database 서버에서 SSISDB를 호스트하는 경우 Azure-SSIS IR 동일한 가상 네트워크 및 서브넷에 조인해야 합니다.

가상 네트워크를 조인하는 Azure SQL Managed Instance SSISDB를 호스트하는 경우 Azure-SSIS IR 관리되는 인스턴스가 아닌 다른 서브넷에 있는 동일한 가상 네트워크에 조인해야 합니다. Azure-SSIS IR 관리되는 인스턴스가 아닌 다른 가상 네트워크에 조인하려면 가상 네트워크 피어링(동일한 지역으로 제한) 또는 가상 네트워크-가상 네트워크 연결을 권장합니다. 자세한 내용은 [애플리케이션을 Azure SQL Managed Instance에 연결](../azure-sql/managed-instance/connect-application-instance.md)을 참조하세요.

## <a name="access-to-azure-data-stores"></a>Azure 데이터 저장소에 대한 액세스

SSIS 패키지가 [가상 네트워크 서비스 엔드포인트를](../virtual-network/virtual-network-service-endpoints-overview.md) 지원하는 Azure 데이터 저장소/리소스에 액세스하고 Azure-SSIS IR 해당 리소스에 대한 액세스를 보호하려는 경우 Azure-SSIS IR 가상 네트워크 서비스 엔드포인트용으로 구성된 가상 네트워크 서브넷에 조인한 다음, 동일한 서브넷에서 액세스할 수 있도록 관련 리소스의 방화벽에 가상 네트워크 규칙을 추가할 수 있습니다.

## <a name="access-to-other-cloud-data-stores"></a>다른 클라우드 데이터 저장소에 대한 액세스

SSIS 패키지가 특정 고정 공용 IP 주소만 허용하는 다른 클라우드 데이터 저장소/리소스에 액세스하고 Azure-SSIS IR 해당 리소스에 대한 액세스를 보호하려는 경우 가상 네트워크에 조인하는 동안 [공용 IP 주소를](../virtual-network/virtual-network-public-ip-address.md) Azure-SSIS IR 연결한 다음 관련 리소스의 방화벽에 IP 방화벽 규칙을 추가하여 해당 IP 주소로부터의 액세스를 허용할 수 있습니다. 이 작업을 수행하는 두 가지 옵션이 있습니다. 

- Azure-SSIS IR 만들 때 사용자 고유의 고정 공용 IP 주소를 [가져와서 ADF UI](join-azure-ssis-integration-runtime-virtual-network-ui.md) 또는 Azure PowerShell 통해 [Azure-SSIS IR](join-azure-ssis-integration-runtime-virtual-network-powershell.md)연결할 수 있습니다. Azure-SSIS IR 아웃바운드 인터넷 연결만 이러한 공용 IP 주소를 사용하고 서브넷의 다른 리소스는 사용하지 않습니다.

- 또한 Azure-SSIS IR 조인하는 서브넷에서 [가상 네트워크 NAT(네트워크 주소 변환)를](../virtual-network/nat-gateway/nat-overview.md) 구성할 수 있으며 이 서브넷의 모든 아웃바운드 인터넷 연결은 지정된 공용 IP 주소를 사용합니다.

모든 경우에 가상 네트워크는 Azure Resource Manager 배포 모델을 통해서만 배포할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure-SSIS IR 삽입하도록 가상 네트워크 구성](azure-ssis-integration-runtime-virtual-network-configuration.md)
- [Express 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-express-virtual-network-injection.md)
- [표준 가상 네트워크 삽입 방법](azure-ssis-integration-runtime-standard-virtual-network-injection.md)
- [ADF UI를 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-ui.md)
- [Azure PowerShell 통해 가상 네트워크에 Azure-SSIS IR 조인](join-azure-ssis-integration-runtime-virtual-network-powershell.md)

Azure-SSIS IR에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [Azure SSIS IR](concepts-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR 등 IR에 대한 일반적인 개념적 정보를 제공합니다. 
- [자습서: Azure에 SSIS 패키지 배포](tutorial-deploy-ssis-packages-azure.md). 이 자습서에서는 Azure-SSIS IR을 만드는 단계별 지침을 제공합니다. Azure SQL Database 서버를 사용하여 SSISDB를 호스트합니다. 
- [Azure-SSIS IR 만들기](create-azure-ssis-integration-runtime.md). 이 문서는 자습서를 더 자세히 설명합니다. 가상 네트워크 서비스 엔드포인트/IP 방화벽 규칙/프라이빗 엔드포인트로 구성된 Azure SQL Database 서버 또는 가상 네트워크를 조인하여 SSISDB를 호스트하는 Azure SQL Managed Instance 사용하는 방법에 대한 지침을 제공합니다. 가상 네트워크에 Azure-SSIS IR 조인하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure SSIS IR에 대한 정보를 가져와 파악하는 방법을 설명합니다.
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서에서는 Azure-SSIS IR을 중지하거나, 시작하거나, 삭제하는 방법을 설명합니다. 또한 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법도 설명합니다.
