---
title: 장애 조치(Failover) 클러스터 인스턴스
description: Azure Virtual Machines에서 SQL Server를 사용하는 FCI(장애 조치(failover) 클러스터 인스턴스)에 대해 알아봅니다.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: overview
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.openlocfilehash: 2bcf10cf3d5e2036a14372d5dd0bacef7e396857
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132157110"
---
# <a name="failover-cluster-instances-with-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VM(가상 머신)에서 SQL Server에 FCI(장애 조치(failover) 클러스터 인스턴스)를 사용하는 경우 기능의 차이를 소개합니다. 

## <a name="overview"></a>개요

Azure VM의 SQL Server는 [WSFC(Windows Server 장애 조치(failover) 클러스터링)](hadr-windows-server-failover-cluster-overview.md) 기능을 사용하여 서버 인스턴스 수준(장애 조치(failover) 클러스터 인스턴스)의 중복성을 통해 로컬 고가용성을 제공합니다. FCI는 WSFC(또는 단순히 클러스터) 노드 및 가능한 경우, 여러 서브넷에 설치되는 SQL Server의 단일 인스턴스입니다. 네트워크에서 FCI는 단일 컴퓨터에서 실행되는 SQL Server의 단일 인스턴스로 나타납니다. 하지만 현재 노드를 사용할 수 없게 되면 FCI는 한 WSFC 노드에서 다른 노드로 장애 조치(failover)를 제공합니다.

문서의 나머지 부분에서는 Azure VM의 SQL Server에서 사용하는 경우 장애 조치(failover) 클러스터 인스턴스의 차이에 중점을 둡니다. 장애 조치(failover) 클러스터링 기술에 대해 자세히 알아보려면 다음을 참조하세요. 

- [Windows 클러스터 기술](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server 장애 조치(failover) 클러스터 인스턴스](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

> [!NOTE]
> Azure Migrate를 사용하여 Azure VM의 SQL Server에 대한 장애 조치(failover) 클러스터 인스턴스 솔루션을 리프트 앤 시프트할 수 있습니다. 자세한 내용은 [장애 조치(failover) 클러스터 인스턴스 마이그레이션](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md)을 참조하세요. 

## <a name="quorum"></a>Quorum

Azure Virtual Machines의 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스는 클러스터 쿼럼에 대한 디스크 감시, 클라우드 감시 또는 파일 공유 감시를 사용하도록 지원합니다.

자세히 알아보려면 [Azure에서 SQL Server VM을 사용한 Quorum 모범 사례](hadr-cluster-best-practices.md#quorum)를 참조하세요. 


## <a name="storage"></a>스토리지

기존의 온-프레미스 클러스터 환경에서 Windows 장애 조치(failover) 클러스터는 두 노드에서 모두 액세스할 수 있는 SAN(스토리지 영역 네트워크)을 공유 스토리지로 사용합니다. SQL Server 파일은 공유 스토리지에서 호스트되며 활성 노드만 한 번에 파일에 액세스할 수 있습니다. 

Azure VM의 SQL Server는 SQL Server 장애 조치(failover) 클러스터 인스턴스 배포를 위한 공유 스토리지 솔루션으로 다양한 옵션을 제공합니다. 

||[Azure 공유 디스크](../../../virtual-machines/disks-shared.md)|[프리미엄 파일 공유](../../../storage/files/storage-how-to-create-file-share.md) |[S2D(스토리지 공간 다이렉트)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)|
|---------|---------|---------|---------|
|**최소 OS 버전**| 모두 |Windows Server 2012|Windows Server 2016|
|**최소 SQL Server 버전**|모두|SQL Server 2012|SQL Server 2016|
|**지원되는 VM 가용성** |[프리미엄 SSD LRS](../../../virtual-machines/disks-redundancy.md#locally-redundant-storage-for-managed-disks): [근접 배치 그룹](../../../virtual-machines/windows/proximity-placement-groups-portal.md)이 있거나 없는 가용성 집합 </br> [프리미엄 SSD ZRS](../../../virtual-machines/disks-redundancy.md#zone-redundant-storage-for-managed-disks): 가용성 영역</br> [Ultra 디스크](../../../virtual-machines/disks-enable-ultra-ssd.md): 동일한 가용성 영역|가용성 집합 및 가용성 영역|가용성 집합 |
|**FileStream 지원**|예|아니요|예 |
|**Azure Blob 캐시**|예|아니요|예|

이 섹션의 나머지 부분에서는 Azure VM의 SQL Server에 사용할 수 있는 각 스토리지 옵션의 이점과 제한 사항을 나열합니다. 

### <a name="azure-shared-disks"></a>Azure 공유 디스크

[Azure 공유 디스크](../../../virtual-machines/disks-shared.md)는 [Azure 관리 디스크](../../../virtual-machines/managed-disks-overview.md)의 기능입니다. Windows Server 장애 조치(failover) 클러스터링은 장애 조치(failover) 클러스터 인스턴스에 Azure 공유 디스크를 사용하도록 지원합니다. 

**지원되는 OS**: 모두   
**지원되는 SQL 버전**: 모두     

**이점**: 
- HADR(고가용성 및 재해 복구) 아키텍처를 그대로 유지하면서 Azure에 마이그레이션하려는 애플리케이션에 유용합니다. 
- SCSI PR(SCSI 영구 예약) 지원으로 인해, 클러스터링된 애플리케이션을 있는 그대로 Azure에 마이그레이션할 수 있습니다. 
- 공유 Azure 프리미엄 SSD 및 Azure Ultra Disk Storage를 지원합니다.
- 단일 공유 디스크를 사용하거나 여러 공유 디스크를 스트라이프하여 공유 스토리지 풀을 생성할 수 있습니다. 
- Filestream을 지원합니다.
- 프리미엄 SSD는 가용성 집합을 지원합니다. 
- 프리미엄 SSD ZRS(영역 중복 스토리지)는 가용성 영역을 지원합니다. FCI의 VM 부분은 다른 가용성 영역에 배치할 수 있습니다. 

> [!NOTE]
> Azure 공유 디스크는 [표준 SSD 크기](../../../virtual-machines/disks-shared.md#disk-sizes)도 지원하지만 성능 제한으로 인해 SQL Server 워크로드에 표준 SSD를 사용하지 않는 것이 좋습니다.

**단점**: 

- 프리미엄 SSD 디스크 캐싱이 지원되지 않습니다.
- Ultra 디스크는 가용성 집합을 지원하지 않습니다. 
- Ultra 디스크에 가용성 영역이 지원되지만 VM이 동일한 가용성 영역에 있어야 하므로 가상 머신의 가용성이 99.9%로 줄어듭니다.
- Ultra 디스크는 ZRS(영역 중복 스토리지)를 지원하지 않습니다.

 
시작하려면 [Azure 공유 디스크를 사용하는 SQL Server 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-azure-shared-disks-manually-configure.md)를 참조하세요. 

### <a name="storage-spaces-direct"></a>직접 스토리지 공간

[스토리지 공간 다이렉트](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)는 Azure Virtual Machines에서 장애 조치(failover) 클러스터링과 함께 지원되는 Windows Server 기능입니다. 소프트웨어 기반 가상 SAN을 제공합니다.

**지원되는 OS**: Windows Server 2016 이상   
**지원되는 SQL 버전**: SQL Server 2016 이상   


**이점:** 

- 네트워크 대역폭이 충분하여 강력하고 성능이 뛰어난 공유 스토리지 솔루션을 제공할 수 있습니다. 
- Azure Blob 캐시를 지원하므로 읽기를 캐시에서 로컬로 처리할 수 있습니다. 업데이트는 두 노드에 동시에 복제됩니다. 
- FileStream을 지원합니다. 

**제한 사항:**

- Windows Server 2016 이상에서만 사용할 수 있습니다. 
- 가용성 영역이 지원되지 않습니다.
- 두 가상 머신에 연결된 디스크 용량이 동일해야 합니다. 
- 지속적인 디스크 복제로 인해 고성능을 구현하려면 네트워크 대역폭이 높아야 합니다. 
- 스토리지가 각 VM에 연결되어 있으므로 VM이 더 커야 하고 스토리지 비용을 두 배로 지불해야 합니다. 

시작하려면 [스토리지 공간 다이렉트를 사용하는 SQL Server 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-storage-spaces-direct-manually-configure.md)를 참조하세요. 

### <a name="premium-file-share"></a>프리미엄 파일 공유

[프리미엄 파일 공유](../../../storage/files/storage-how-to-create-file-share.md)는 [Azure Files](../../../storage/files/index.yml)의 기능입니다. 프리미엄 파일 공유는 SSD를 지원하며 지연 시간이 지속적으로 짧습니다. Windows Server 2012 이상에서 SQL Server 2012 이상에 대한 장애 조치(failover) 클러스터 인스턴스에 사용할 수 있도록 완벽하게 지원됩니다. 프리미엄 파일 공유를 사용하면 가동 중지 시간 없이 파일 공유 크기를 조정하고 확장할 수 있기 때문에 뛰어난 유연성을 제공합니다.

**지원되는 OS**: Windows Server 2012 이상   
**지원되는 SQL 버전**: SQL Server 2012 이상   

**이점:** 
- 가상 머신을 위한 공유 스토리지 솔루션은 여러 가용성 영역에 분산됩니다. 
- 한 자릿수 지연 시간과 버스트 가능 I/O 성능을 갖춘 완전 관리형 파일 시스템입니다. 

**제한 사항:**
- Windows Server 2012 이상에서만 사용할 수 있습니다. 
- FileStream이 지원되지 않습니다. 


시작하려면 [프리미엄 파일 공유를 사용하는 SQL Server 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-premium-file-share-manually-configure.md)를 참조하세요. 

### <a name="partner"></a>파트너

지원되는 스토리지가 있는 파트너 클러스터링 솔루션이 있습니다. 

**지원되는 OS**: 모두   
**지원되는 SQL 버전**: 모두   

한 가지 예는 SIOS DataKeeper를 스토리지로 사용합니다. 자세한 내용은 [장애 조치(failover) 클러스터링 및 SIOS DataKeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/) 블로그 항목을 참조하세요.

### <a name="iscsi-and-expressroute"></a>iSCSI 및 ExpressRoute

Azure ExpressRoute를 통해 iSCSI 대상 공유 블록 스토리지를 노출할 수도 있습니다. 

**지원되는 OS**: 모두   
**지원되는 SQL 버전**: 모두   

예를 들어 NPS(NetApp 프라이빗 스토리지)는 Equinix와 함께 ExpressRoute를 사용하여 iSCSI 대상을 Azure VM에 공개합니다.

Microsoft 파트너의 공유 스토리지 및 데이터 복제 솔루션의 경우 장애 조치 시 데이터 액세스와 관련된 문제는 공급 업체에 문의하십시오.

## <a name="connectivity"></a>연결

장애 조치(failover) 클러스터 인스턴스에 연결하기 위한 온-프레미스 환경과 일치시키려면 SQL Server VM을 동일한 가상 네트워크 내의 [여러 서브넷](failover-cluster-instance-prepare-vm.md#subnets)에 배포합니다. 여러 서브넷이 있으면 트래픽을 FCI로 라우팅하기 위해 Azure Load Balancer 또는 DNN(분산 네트워크 이름)에 대한 추가 종속성이 필요하지 않습니다. 

SQL Server VM을 단일 서브넷에 배포하는 경우 장애 조치(failover) 클러스터 인스턴스로 트래픽을 라우팅하도록 VNN(가상 네트워크 이름) 및 Azure Load Balancer 또는 DNN(분산 네트워크 이름)을 구성할 수 있습니다. [두 이상의 차이점을 검토](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)한 다음, 장애 조치(failover) 클러스터 인스턴스에 대해 [분산 네트워크 이름](failover-cluster-instance-distributed-network-name-dnn-configure.md) 또는 [가상 네트워크 이름](failover-cluster-instance-vnn-azure-load-balancer-configure.md)을 배포합니다.

장애 조치(failover)가 더 빠르며 부하 분산 장치를 관리하는 오버헤드와 비용이 제거되므로 분산 네트워크 이름이 권장됩니다(가능한 경우). 

DNN를 사용하는 경우 대부분의 SQL Server 기능이 FCI에서 투명하게 작동하지만 특별한 고려 사항이 필요할 수 있는 특정 기능이 있습니다. 자세히 알아보려면 [FCI 및 DNN 상호 운용성](failover-cluster-instance-dnn-interoperability.md)을 참조하세요. 

## <a name="limitations"></a>제한 사항

Azure Virtual Machines에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스에 대한 다음 제한 사항을 고려하세요. 

### <a name="lightweight-extension-support"></a>경량 확장 지원

현재 Azure 가상 머신의 SQL Server 장애 조치(failover) 클러스터 인스턴스는 SQL Server IaaS 에이전트 확장의 [경량 관리 모드](sql-server-iaas-agent-extension-automate-management.md#management-modes)에서만 지원됩니다. 전체 확장 모드에서 경량 모드로 변경하려면 해당 VM에 대한 **SQL 가상 머신** 리소스를 삭제한 다음, 경량 모드에서 SQL IaaS 에이전트 확장에 등록합니다. Azure Portal을 사용하여 **SQL 가상 머신** 리소스를 삭제할 때 가상 머신이 삭제되지 않도록 올바른 가상 머신 옆에 있는 확인란을 선택 취소합니다. 

전체 확장은 자동화된 백업, 패치, 고급 포털 관리 등의 기능을 지원합니다. 이러한 기능은 경량 관리 모드로 등록된 SQL Server VM에 대해서는 작동하지 않습니다.

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines는 CSV(클러스터형 공유 볼륨)의 스토리지와 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-overview.md)가 있는 Windows Server 2019에서 또는 Azure 공유 디스크를 사용하는 SQL Server VM에서 MSDTC(Microsoft Distributed Transaction Coordinator)를 지원합니다. 

Azure Virtual Machines는 클러스터링된 공유 볼륨이 있는 Windows Server 2016 또는 이전 버전에서 MSDTC를 지원하지 않으며, 이유는 다음과 같습니다.

- 클러스터형 MSDTC 리소스는 공유 스토리지를 사용하도록 구성할 수 없습니다. Windows Server 2016에서 MSDTC 리소스를 만드는 경우 스토리지가 사용 가능하더라도 사용 가능한 공유 스토리지가 표시되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 부하 분산 장치는 RPC 포트를 처리하지 않습니다.


## <a name="next-steps"></a>다음 단계

[클러스터 구성 모범 사례](hadr-cluster-best-practices.md)를 검토한 후 [FCI용 SQL Server VM을 준비](failover-cluster-instance-prepare-vm.md)할 수 있습니다. 


자세한 내용은 다음을 참조하세요.

- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [장애 조치(Failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

