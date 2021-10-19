---
title: 고가용성, 재해 복구, 비즈니스 연속성
description: Azure VM에서 Always On 가용성 그룹, 장애 조치(failover) 클러스터 인스턴스, 데이터베이스 미러링, 로그 전달, Azure Storage로 백업 및 복원 등의 SQL Server에 사용할 수 있는 고가용성, 재해 복구(HADR), 비즈니스 연속성 옵션에 대해 알아봅니다.
services: virtual-machines-windows
documentationcenter: na
author: rajeshsetlem
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/27/2020
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: f5dd02d8abf70d3812fe0d07baa8ddf08bdc8a7a
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166076"
---
# <a name="business-continuity-and-hadr-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server를 위한 비즈니스 연속성 및 HADR
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

비즈니스 연속성은 재해 발생 시 비즈니스를 계속하고, 복구를 계획하고, 데이터의 고가용성을 보장합니다. Azure Virtual Machines에 SQL Server를 설치하여 사용하면 HADR(고가용성 및 재해 복구) 데이터베이스 솔루션의 비용을 절감하는 데 도움이 됩니다. 

대부분의 SQL Server HADR 솔루션은 VM(가상 머신)에서 지원되며, Azure 전용으로도, 하이브리드 솔루션으로도 사용 가능합니다. Azure 전용 솔루션에서는 HADR 시스템 전체가 Azure에서 실행됩니다. 하이브리드 구성에서는 솔루션의 일부가 Azure에서 실행되고 나머지 부분은 조직의 온-프레미스에서 실행됩니다. Azure 환경은 유연하게 제공되므로 사용 중인 SQL Server 데이터베이스 시스템의 예산과 HADR 요구 사항에 맞게 Azure로 부분적으로만 이동하거나 완전히 이동할 수도 있습니다.

이 문서에서는 Azure VM에서 SQL Server에 사용할 수 있는 비즈니스 연속성 솔루션을 비교 및 대조합니다. 

## <a name="overview"></a>개요

SLA(서비스 수준 약정)에 필요한 HADR 기능을 데이터베이스 시스템에 도입하는 것은 여러분의 선택입니다. Azure는 클라우드 서비스를 위한 서비스 복구 및 가상 머신의 오류 복구 감지와 같은 고가용성 메커니즘을 제공하기는 하지만, 이를 통해 SLA를 충족할 수 있다는 보장은 없습니다. 해당 메커니즘은 가상 머신의 고가용성을 보호하는 데 도움이 되지만 VM 내부에서 실행되는 SQL Server의 가용성을 보호하지 않습니다. 

VM이 온라인 상태이고 정상인 경우에도 SQL Server 인스턴스에 오류가 발생할 수 있습니다. Azure가 제공하는 고가용성 메커니즘에서도 소프트웨어 또는 하드웨어 오류 복구, 운영 체제 업그레이드의 경우에는 VM이 가동 중지될 수 있습니다.

Azure의 GRS(지역 중복 스토리지)는 지역에서 복제 기능을 사용하여 구현됩니다. GRS는 데이터베이스에 적합한 재해 복구 솔루션이 아닐 수 있습니다. 지역 복제는 데이터를 비동기 방식으로 전송하기 때문에, 재해 발생 시 최근 업데이트 내용이 손실될 수 있습니다. 지역 복제 제한 사항에 관한 자세한 내용은 [지역에서 복제 지원](#geo-replication-support) 섹션에서 다룹니다.

> [!NOTE]
> 이제 Azure Migrate를 사용하여 [장애 조치(failover) 클러스터 인스턴스](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) 및 [가용성 그룹](../../migration-guides/virtual-machines/sql-server-availability-group-to-sql-on-azure-vm.md) 솔루션을 모두 Azure VM의 SQL Server로 리프트 앤 시프트할 수 있습니다. 


## <a name="deployment-architectures"></a>배포 아키텍처
Azure는 비즈니스 연속성을 위해 다음과 같은 SQL Server 기술을 지원합니다.

* [Always On 가용성 그룹](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Always On FCI(장애 조치 클러스터 인스턴스)](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
* [로그 전달](/sql/database-engine/log-shipping/about-log-shipping-sql-server)
* [Azure Blob 스토리지를 사용하여 SQL Server 백업 및 복원](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)
* [데이터베이스 미러링](/sql/database-engine/database-mirroring/database-mirroring-sql-server) - SQL Server 2016에서 사용되지 않음
* [Azure Site Recovery](../../../site-recovery/site-recovery-sql.md)

기술을 조합하여 고가용성과 재해 복구 기능을 갖춘 SQL Server 솔루션을 구현하는 것도 가능합니다. 사용하는 기술에 따라, 하이브리드 배포에는 Azure 가상 네트워크와 함께 VPN 터널이 필요할 수도 있습니다. 다음 섹션에서 몇 가지 배포 아키텍처의 예를 소개합니다.

## <a name="azure-only-high-availability-solutions"></a>Azure 전용: 고가용성 솔루션

Always On 가용성 그룹을 사용하여 데이터베이스 수준에서 SQL Server에 대한 고가용성 솔루션을 유지할 수 있습니다. Always On 장애 조치 클러스터 인스턴스를 사용하여 인스턴스 수준에서 고가용성 솔루션을 만들 수도 있습니다. 추가 보호를 위해 장애 조치 클러스터 인스턴스에서 가용성 그룹을 만들어 두 수준 모두에서 중복을 만들 수 있습니다. 

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |동일한 지역에 있는 Azure VM에서 실행되는 모든 가용성 복제본은 고가용성을 제공합니다. Windows 장애 조치 클러스터링에 Active Directory 도메인이 필요하기 때문에 도메인 컨트롤러 VM을 구성해야 합니다.<br/><br/> 더 높은 중복성 및 가용성을 위해 [가용성 그룹 개요](availability-group-overview.md)에 설명된 대로 여러 [가용성 영역](../../../availability-zones/az-overview.md)에 Azure VM을 배포할 수 있습니다. 가용성 그룹의 SQL Server VM이 가용성 영역에 배포된 경우 [Azure SQL VM CLI](./availability-group-az-commandline-configure.md) 및 [Azure 빠른 시작 템플릿](availability-group-quickstart-template-configure.md) 문서에 설명된 대로 수신기에 대해 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-overview.md)를 사용합니다.<br/> ![“주 복제본", "보조 복제본", "파일 공유 감시"로 만든 "WSFC 클러스터" 위의 "도메인 컨트롤러"를 보여 주는 다이어그램입니다.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-ha-always-on.png)<br/>자세한 내용은 [Azure에서 가용성 그룹 구성(GUI)](./availability-group-quickstart-template-configure.md)을 참조하세요. |
| **장애 조치(Failover) 클러스터 인스턴스** |장애 조치 클러스터 인스턴스는 SQL Server VM에서 지원됩니다. FCI 기능에는 공유 스토리지가 필요하기 때문에 Azure VM에서 5개의 솔루션이 SQL Server와 함께 작동합니다. <br/><br/> - Windows Server 2019에 대한 [Azure 공유 디스크](failover-cluster-instance-azure-shared-disks-manually-configure.md) 사용. 공유 관리 디스크는 여러 가상 머신에 관리 디스크를 동시에 연결할 수 있게 하는 Azure 제품입니다. 클러스터의 VM은 SCSI PR(SCSI 영구 예약)을 통해 클러스터된 애플리케이션에서 선택한 예약에 따라 연결된 디스크에서 데이터를 읽거나 쓸 수 있습니다. SCSI PR은 SAN(저장 영역 네트워크) 온-프레미스 애플리케이션에서 사용되는 업계 표준 스토리지 솔루션입니다. 관리 디스크에서 SCSI PR을 사용하면 해당 애플리케이션을 있는 그대로 Azure로 마이그레이션할 수 있습니다. <br/><br/>- [스토리지 공간 다이렉트 \(S2D\)](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 사용하여 Windows Server 2016 이상에 대한 소프트웨어 기반 가상 SAN을 제공합니다.<br/><br/>- Windows Server 2012 이상에 [프리미엄 파일 공유](failover-cluster-instance-premium-file-share-manually-configure.md)를 사용합니다. 프리미엄 파일 공유는 SSD를 지원하고, 일관되게 대기 시간이 짧으며, FCI와 함께 사용 시 완벽하게 지원됩니다.<br/><br/>- 클러스터링용 파트너 솔루션에서 지원하는 스토리지 사용. SIOS DataKeeper를 사용하는 특정 예제는 블로그 항목 [장애 조치 클러스터링 및 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/)를 참조하세요.<br/><br/>- Azure ExpressRoute를 통해 원격 iSCSI 대상에 공유 블록 스토리지를 사용합니다. 예를 들어 NPS(NetApp 프라이빗 스토리지)는 Equinix와 함께 ExpressRoute를 사용하여 iSCSI 대상을 Azure VM에 공개합니다.<br/><br/>Microsoft 파트너의 공유 스토리지 및 데이터 복제 솔루션의 경우 장애 조치 시 데이터 액세스와 관련된 문제는 공급 업체에 문의하십시오.<br/><br/>|

## <a name="azure-only-disaster-recovery-solutions"></a>Azure 전용: 재해 복구 솔루션
가용성 그룹, 데이터베이스 미러링을 사용하여 Azure 내의 SQL Server 데이터베이스에 대한 재해 복구 솔루션을 구축하거나 스토리지 BLOB을 사용하여 백업 및 복원할 수 있습니다.

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |재해 복구를 위해 가용성 복제본을 Azure VM의 여러 데이터 센터에서 실행합니다. 이렇게 여러 지역에 나누어 실행되는 솔루션은 완전한 사이트 중단이 발생해도 데이터를 보호하는 데 도움이 됩니다. <br/> !["비동기 커밋"으로 연결된 "주 복제본"과 "보조 복제본"이 있는 두 지역을 보여 주는 다이어그램.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-alwayson.png)<br/>한 하위 지역 내의 모든 복제본은 동일한 클라우드 서비스 및 동일한 가상 네트워크 내에 있어야 합니다. 각 하위 지역에 별도의 가상 네트워크가 있으므로 해당 솔루션은 네트워크 간 연결이 필요합니다. 자세한 내용은 [Azure Portal을 사용하여 네트워크 간 연결 구성](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)을 참조하세요. 자세한 지침은 [여러 Azure 지역에서 SQL Server Always On 가용성 그룹 구성](availability-group-manually-configure-multiple-regions.md)을 참조하세요.|
| **데이터베이스 미러링** |재해 복구를 위해 주 서버와 미러 서버를 다른 데이터 센터에서 실행합니다. 서버 인증서를 사용하여 배포해야 합니다. SQL Server 데이터베이스 미러링은 Azure VM의 SQL Server 2008 또는 SQL Server 2008 R2에서 지원되지 않습니다. <br/>!["고성능"을 사용하여 “다른 지역의 미러”에 연결된 한 지역의 "보안 주체"를 표시하는 다이어그램.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-dbmirroring.png) |
| **Azure Blob 스토리지를 사용하여 백업 및 복원** |재해 복구를 위해 다른 데이터 센터에서 BLOB 스토리지로 직접 백업된 프로덕션 데이터베이스.<br/>![다른 하위 지역의 "Blob Storage"까지 백업하는 한 지역의 "데이터베이스"를 보여 주는 다이어그램.](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-backup-restore.png)<br/>자세한 내용은 [Azure VM에서 SQL Server의 백업 및 복원](../../../azure-sql/virtual-machines/windows/backup-restore.md)을 참조하세요. |
| **Azure Site Recovery를 사용하여 SQL Server를 Azure에 복제 및 장애 조치** |재해 복구를 위해 다른 Azure 데이터 센터의 Azure Storage에 직접 복제된 단일 Azure 데이터 센터의 프로덕션 SQL Server 인스턴스.<br/>![다른 데이터 센터의 재해 복구를 위해 "ASR 복제"를 사용하는 단일 Azure 데이터 센터에서 "데이터베이스"를 보여 주는 다이어그램. ](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/azure-only-dr-standalone-sqlserver-asr.png)<br/>자세한 내용은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](../../../site-recovery/site-recovery-sql.md)를 참조하세요. |


## <a name="hybrid-it-disaster-recovery-solutions"></a>하이브리드 IT: 재해 복구 솔루션
가용성 그룹, 데이터베이스 미러링, 로그 전달, Azure Blob Storage를 사용한 백업 및 복원을 사용하여 하이브리드 IT 환경 내에 SQL Server 데이터베이스에 대한 재해 복구 솔루션을 구축할 수 있습니다.

| 기술 | 아키텍처의 예 |
| --- | --- |
| **가용성 그룹** |사이트간 재해 복구를 위해 일부 가용성 복제본은 Azure VM에서 실행되고 다른 복제본은 온-프레미스에서 실행됩니다. 프로덕션 사이트는 온-프레미스와 Azure 데이터 센터 어디에도 있을 수 있습니다.<br/>![가용성 그룹](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-alwayson.png)<br/>모든 가용성 복제본이 동일한 장애 조치 클러스터에 있어야 하므로, 장애 조치 클러스터는 두 네트워크(다중 서브넷 장애 조치 클러스터)에 걸쳐 있어야 합니다. 이 구성은 Azure와 온-프레미스 네트워크 간의 VPN 연결이 필요합니다.<br/><br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다.|
| **데이터베이스 미러링** |서버 인증서를 사용한 사이트 간 재해 복구를 위해 한 파트너는 Azure VM에서 실행하고 다른 파트너는 온-프레미스에서 실행합니다. 파트너는 동일한 Active Directory 도메인에 속할 필요가 없으며 VPN 연결도 필요하지 않습니다.<br/>![데이터베이스 미러링](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-dbmirroring.png)<br/>또 다른 데이터베이스 미러링 시나리오로는 사이트 간 재해 복구를 위해 한 파트너가 Azure VM에서 실행되고 다른 파트너가 동일한 Active Directory 도메인의 온-프레미스에서 실행되는 경우를 들 수 있습니다. [Azure 가상 네트워크와 온-프레미스 네트워크 간의 VPN 연결](../../../vpn-gateway/tutorial-site-to-site-portal.md)이 필요합니다.<br/><br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다. SQL Server 데이터베이스 미러링은 Azure VM의 SQL Server 2008 또는 SQL Server 2008 R2에서 지원되지 않습니다. |
| **로그 전달** |사이트 간 재해 복구를 위해 한 서버는 Azure VM에서 실행하고 다른 서버는 온-프레미스에서 실행합니다. 로그 전달은 Windows 파일 공유를 사용하므로 Azure 가상 네트워크와 온-프레미스 네트워크 간의 VPN 연결이 필요합니다.<br/>![로그 전달](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-log-shipping.png)<br/>데이터베이스의 성공적인 재해 복구를 위해서는 재해 복구 사이트에 복제 도메인 컨트롤러도 설치해야 합니다. |
| **Azure Blob 스토리지를 사용하여 백업 및 복원** |온-프레미스 프로덕션 데이터베이스가 재해 복구를 위해 Azure Blob 스토리지에 직접 백업됩니다.<br/>![Backup 및 복원](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-backup-restore.png)<br/>자세한 내용은 [Azure Virtual Machines에서 SQL Server의 백업 및 복원](../../../azure-sql/virtual-machines/windows/backup-restore.md)을 참조하세요. |
| **Azure Site Recovery를 사용하여 SQL Server를 Azure에 복제 및 장애 조치** |재해 복구를 위해 온-프레미스 프로덕션 SQL Server 인스턴스가 Azure Storage에 직접 복제되었습니다.<br/>![Azure Site Recovery를 사용하여 복제](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-dr-standalone-sqlserver-asr.png)<br/>자세한 내용은 [SQL Server 재해 복구 및 Azure Site Recovery를 사용하여 SQL Server 보호](../../../site-recovery/site-recovery-sql.md)를 참조하세요. |


## <a name="free-dr-replica-in-azure"></a>Azure의 무료 DR 복제본

[Software Assurance](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)가 있는 경우 수동 DR 인스턴스에 대한 추가 라이센스 비용 없이 SQL Server로 하이브리드 DR(재해 복구) 계획을 구현할 수 있습니다.

예를 들어 세 개의 복제본이 모두 Azure에 호스트되는 경우 두 개의 무료 수동 보조를 사용할 수 있습니다. 

![Azure의 모든 항목이 있을 때 두 개의 무료 수동 복제본](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/failover-with-primary-in-azure.png)

또는 사용이 허가된 기본 온-프레미스, HA를 위한 하나의 무료 수동, 온-프레미스에서의 DR을 위한 하나의 무료 수동, Azure에서의 DR을 위한 하나의 무료 수동으로 하이브리드 장애 조치 환경을 구성할 수 있습니다.

![환경이 하나의 기본 온-프레미스 복제본을 포함하는 하이브리드인 경우 세 개의 무료 수동](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/hybrid-with-primary-on-prem.png)

자세한 내용은 [제품 라이선스 약관](https://www.microsoft.com/licensing/product-licensing/products)을 참조하세요. 

이 혜택을 사용하려면 [SQL Server 가상 머신 리소스](manage-sql-vm-portal.md#access-the-resource)로 이동하세요. **설정** 아래에서 **구성** 을 선택하고 **SQL Server 라이선스** 에서 **재해 복구** 옵션을 선택합니다. 확인란을 선택하여 이 SQL Server VM이 수동 복제본으로 사용되는지 확인한 다음, **적용** 을 선택하여 설정을 저장합니다. 

![Azure에서 재해 복구 복제본 구성](./media/business-continuity-high-availability-disaster-recovery-hadr-overview/dr-replica-in-portal.png)


## <a name="important-considerations-for-sql-server-hadr-in-azure"></a>Azure에서 SQL Server HADR에 대한 중요 고려 사항
Azure VM, 스토리지 및 네트워킹은 온-프레미스, 가상화되지 않은 IT 인프라에서는 작동 특성이 달라집니다. Azure에서 SQL Server HADR 솔루션을 성공적으로 구현하기 위해서는 차이점을 이해하고 그에 맞게 솔루션을 설계해야 합니다.

### <a name="high-availability-nodes-in-an-availability-set"></a>가용성 집합의 고가용성 노드
Azure의 가용성 집합을 사용하면 고가용성 노드를 별도의 오류 도메인과 업데이트 도메인에 배치할 수 있습니다. Azure 플랫폼은 가용성 집합의 각 가상 머신에 업데이트 도메인 및 장애 도메인을 할당합니다. 데이터 센터 내의 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 머신을 사용할 수 있고 99.95%의 Azure SLA가 충족되도록 합니다. 

고가용성 설정을 구성하려면 유지 관리 이벤트 중에 애플리케이션 또는 데이터 손실 방지를 위해 참여하는 모든 SQL Server 가상 머신을 동일한 가용성 집합에 넣습니다. 같은 클라우드 서비스에 있는 노드만 같은 가용성 집합에 참여할 수 있습니다. 자세한 내용은 [가상 머신의 가용성 관리](../../../virtual-machines/availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)를 참조하세요.

### <a name="high-availability-nodes-in-an-availability-zone"></a>가용성 영역의 고가용성 노드
가용성 영역은 Azure 지역 내의 고유한 물리적 위치입니다. 각 영역은 독립된 전원, 냉각 및 네트워킹을 갖춘 하나 이상의 데이터 센터로 구성됩니다. 하나 이상의 가상 머신을 사용할 수 있고 99.99%의 Azure SLA를 충족하는지 확인하여 지역 내에서 가용성 영역의 물리적 구분은 애플리케이션 및 데이터를 데이터 센터 오류로부터 보호하는 데 도움이 됩니다. 

고가용성을 구성하려면 참여하는 SQL Server 가상 머신을 해당 지역에서 사용 가능한 가용성 영역에 분산시킵니다. 가용성 영역 사이에서 네트워크 간 전송에 추가 요금이 부과됩니다. 자세한 내용은 [가용성 영역](../../../availability-zones/az-overview.md)을 참조하세요. 

### <a name="network-latency-in-hybrid-it"></a>하이브리드 IT 환경의 네트워크 대기 시간
온-프레미스 네트워크와 Azure 간에 긴 네트워크 대기 시간이 있을 수 있다는 가정을 하고 HADR 솔루션을 배포합니다. 복제본을 Azure에 배포할 때, 동기화 모드로 동기 커밋 대신 비동기 커밋을 사용하세요. 온-프레미스와 Azure 모두에 데이터베이스 미러링 서버를 배포할 때는 안전 우선 모드 대신 성능 우선 모드를 사용합니다.

클라우드 환경을 수용하는 데 도움이 될 수 있는 클러스터 및 HADR 설정은 [HADR 구성 모범 사례](hadr-cluster-best-practices.md)를 참조하세요. 

### <a name="geo-replication-support"></a>지역에서 복제 지원
Azure 디스크의 지역 복제는 동일한 데이터베이스의 로그 파일과 데이터 파일을 별도의 디스크로 저장하는 것을 지원하지 않습니다. GRS는 변경 내용을 독립적이고 비동기적으로 각 디스크에 복제합니다. 이 메커니즘은 지리적으로 복제된 복사본에서 쓰기 순서가 지켜지도록 보장하지만 여러 디스크에 지역 복제된 복사본에서는 보장하지 않습니다. 데이터 파일과 로그 파일을 별도의 디스크에 저장하도록 데이터베이스를 구성하는 경우, 재해 발생 후 복구된 디스크에는 로그 파일보다 최신인 데이터 파일 복사본이 포함되어 있을 수 있어 SQL Server의 미리 쓰기 로그와 트랜잭션의 ACID 속성(원자성, 일관성, 격리, 내구성)을 위반하게 됩니다. 

스토리지 계정에 지역 복제를 사용하지 않을 방법이 없으면 데이터베이스의 모든 데이터 및 로그 파일을 동일한 디스크에 저장해야 합니다. 데이터베이스의 크기 때문에 디스크를 두 개 이상 사용해야 하는 경우 데이터 중복성을 위해 앞서 나열한 재해 복구 솔루션 중 하나를 배포합니다.

## <a name="next-steps"></a>다음 단계

[가용성 그룹](availability-group-overview.md) 또는 [장애 조치 클러스터 인스턴스](failover-cluster-instance-overview.md)가 비즈니스에 가장 적합한 비즈니스 연속성 솔루션인지 결정합니다. 그런 다음 고가용성 및 재해 복구를 위한 환경을 구성하기 위한 [모범 사례](hadr-cluster-best-practices.md)를 검토합니다.
