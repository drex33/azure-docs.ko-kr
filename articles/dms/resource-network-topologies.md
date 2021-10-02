---
title: SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지
titleSuffix: Azure Database Migration Service
description: Azure Database Migration Service를 사용한 Azure SQL Managed Instance 마이그레이션을 위한 원본 및 대상의 구성 알아보기.
author: pochiraju
ms.author: rajpo
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: reference
ms.date: 01/08/2020
ms.openlocfilehash: 4a4cdce5ae20147bbf25a82401c9993a620320e5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129363030"
---
# <a name="network-topologies-for-azure-sql-managed-instance-migrations-using-azure-database-migration-service"></a>Azure Database Migration Service를 사용한 Azure SQL Managed Instance 마이그레이션에 대한 네트워크 토폴로지를 알아봅니다.

이 문서에서는 SQL Server에서 Azure SQL Managed Instance Instance로 포괄적인 마이그레이션 환경을 제공하기 위해 Azure Database Migration Service에서 사용할 수 있는 다양한 네트워크 토폴로지에 대해 설명합니다.

## <a name="azure-sql-managed-instance-configured-for-hybrid-workloads"></a>하이브리드 워크로드에 대해 구성된 Azure SQL Managed Instance 

Azure SQL Managed Instance가 온-프레미스 네트워크에 연결된 경우 이 토폴로지를 사용합니다. 이 방법은 가장 간소화된 네트워크 라우팅을 제공하고 마이그레이션 동안 최대 데이터 처리량을 제공합니다.

![하이브리드 워크로드에 대한 네트워크 토폴로지](media/resource-network-topologies/hybrid-workloads.png)

**Requirements**

- 이 시나리오에서는 Azure SQL Managed Instance와 Azure Database Migration Service의 인스턴스가 같은 Microsoft Azure Virtual Network에서 생성되지만 다른 서브넷을 사용합니다.  
- 이 시나리오에 사용된 가상 네트워크는 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 네트워크에도 연결됩니다.

## <a name="sql-managed-instance-isolated-from-the-on-premises-network"></a>온-프레미스 네트워크에서 격리된 Azure SQL Managed Instance

작업 환경에 다음과 같은 시나리오 중 하나 이상이 필요한 경우 이 네트워크 토폴로지를 사용합니다.

- Azure SQL Managed Instance는 온-프레미스 연결에서 분리되는 반면, Azure Database Migration Service 인스턴스는 온-프레미스 네트워크에 연결됩니다.
- Azure RBAC(Azure 역할 기반 액세스 제어) 정책이 설정되어 있는 경우, 사용자가 SQL Managed Instance를 호스트하는 동일한 구독에 액세스하도록 제한해야 합니다.
- SQL Managed Instance와 Azure Database Migration Service에 사용되는 가상 네트워크는 서로 다른 구독에 위치합니다.

![온-프레미스 네트워크에서 격리된 Managed Instance의 네트워크 토폴로지](media/resource-network-topologies/mi-isolated-workload.png)

**Requirements**

- Azure Database Migration Service 이 시나리오에 사용하는 가상 네트워크도 [ExpressRoute](../expressroute/expressroute-introduction.md) 또는 [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)을 사용하여 온-프레미스 네트워크에 연결해야 합니다.
- SQL Managed Instance와 Azure Database Migration Service에 사용되는 가상 네트워크 간에 [VNet 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 설정합니다.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>클라우드 간 마이그레이션: 공유 가상 네트워크

원본 SQL Server가 Azure VM에서 호스트되고 SQL Managed Instance와 Azure Database Migration Service가 동일한 가상 네트워크를 공유하는 경우 이 토폴로지를 사용합니다.

![공유 VNet을 포함한 클라우드 간 마이그레이션에 대한 네트워크 토폴로지](media/resource-network-topologies/cloud-to-cloud.png)

**Requirements**

- 추가 요구 사항 없음

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>클라우드 간 마이그레이션: 격리된 가상 네트워크

작업 환경에 다음과 같은 시나리오 중 하나 이상이 필요한 경우 이 네트워크 토폴로지를 사용합니다.

- SQL Managed Instance는 격리된 가상 네트워크에서 프로비저닝됩니다.
- Azure RBAC(Azure 역할 기반 액세스 제어) 정책이 설정되어 있는 경우, 사용자가 SQL Managed Instance를 호스트하는 동일한 구독에 액세스하도록 제한해야 합니다.
- SQL Managed Instance와 Azure Database Migration Service에 사용되는 가상 네트워크는 서로 다른 구독에 위치합니다.

![격리된 VNet을 포함한 클라우드 간 마이그레이션에 대한 네트워크 토폴로지](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Requirements**

- SQL Managed Instance와 Azure Database Migration Service에 사용되는 가상 네트워크 간에 [VNet 네트워크 피어링](../virtual-network/virtual-network-peering-overview.md)을 설정합니다.

## <a name="inbound-security-rules"></a>인바운드 보안 규칙

| **이름**   | **포트** | **프로토콜** | **원본** | **대상** | **작업** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | 모두      | 모두          | DMS SUBNET | 모두             | Allow      |

## <a name="outbound-security-rules"></a>아웃바운드 보안 규칙

| **이름**                  | **포트**                                              | **프로토콜** | **원본** | **대상**           | **작업** | **규칙이 필요한 이유**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ServiceBus                | 443, ServiceTag: ServiceBus                           | TCP          | 모두        | 모두                       | Allow      | Service Bus를 통한 관리 평면 통신 <br/>(Microsoft 피어링을 사용하도록 설정한 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                             |
| 스토리지                   | 443, ServiceTag: Storage                              | TCP          | 모두        | 모두                       | Allow      | Azure Blob Storage를 사용하는 관리 평면 <br/>(Microsoft 피어링을 사용하도록 설정한 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                             |
| 진단               | 443, ServiceTag: AzureMonitor                         | TCP          | 모두        | 모두                       | Allow      | DMS는 이 규칙을 사용하여 문제 해결을 위한 진단 정보를 수집합니다. <br/>(Microsoft 피어링을 사용하도록 설정한 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                  |
| SQL 원본 서버         | 1433(또는 SQL Server가 수신 대기 중인 TCP IP 포트) | TCP          | 모두        | 온-프레미스 주소 공간 | 허용      | DMS의 SQL Server 원본 연결 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                                                       |
| SQL Server 명명된 인스턴스 | 1434                                                  | UDP          | 모두        | 온-프레미스 주소 공간 | 허용      | DMS의 SQL Server 명명된 인스턴스 원본 연결 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.)                                                                        |
| SMB 공유                 | 445(시나리오에서 필요한 경우)                             | TCP          | 모두        | 온-프레미스 주소 공간 | 허용      | Azure SQL Database MI 및 Azure VM의 SQL Server로 마이그레이션할 데이터베이스 백업 파일을 저장하는 DMS용 SMB 네트워크 공유 <br/>(사이트 간 연결이 있는 경우 이 규칙이 필요하지 않을 수 있습니다.) |
| DMS_subnet                | 모두                                                   | 모두          | 모두        | DMS_Subnet                | 허용      |                                                                                                                                                                                                  |

## <a name="see-also"></a>참고 항목

- [SQL Server를 SQL Managed Instance로 마이그레이션](./tutorial-sql-server-to-managed-instance.md)
- [Azure Database Migration Service 사용을 위한 선행 조건 개요](./pre-reqs.md).
- [Azure Portal을 사용하여 가상 네트워크 만들기](../virtual-network/quick-create-portal.md)

## <a name="next-steps"></a>다음 단계

- Azure Database Migration Service의 개요는 [Azure Database Migration Service](dms-overview.md) 문서를 참조하세요.
- Azure Database Migration Service의 지역별 가용성에 대한 최신 정보는 [하위 지역별 사용 가능 제품](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) 페이지를 참조하세요.