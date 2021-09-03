---
title: Azure의 공유 디스크를 사용하는 WSFC의 SAP ASCS/SCS 인스턴스 클러스터링 | Microsoft Docs
description: 클러스터 공유 디스크를 사용하여 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스를 클러스터링하는 방법을 알아봅니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: f6fb85f8-c77a-4af1-bde8-1de7e4425d2e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/29/2021
ms.author: radeltch
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8fca86017b69d20ae71af94b4cabe76c5236815f
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/18/2021
ms.locfileid: "122530862"
---
# <a name="cluster-an-sap-ascsscs-instance-on-a-windows-failover-cluster-by-using-a-cluster-shared-disk-in-azure"></a>Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링

> ![Windows OS][Logo_Windows] Windows
>

Windows Server 장애 조치(Failover) 클러스터링은 Windows에서 고가용성 SAP ASCS/SCS를 설치하고 DBMS를 사용하기 위한 기반이 됩니다.

장애 조치(failover) 클러스터는 함께 작동하여 애플리케이션 및 서비스의 가용성을 높이는 1+n개 독립 서버(노드) 그룹입니다. 노드에 장애가 발생하는 경우 Windows Server 장애 조치(Failover) 클러스터링은 애플리케이션 및 서비스를 제공하기 위해 발생할 수 있으며 정상 클러스터를 유지 관리하는 장애 횟수를 계산합니다. 장애 조치 클러스터링을 달성하기 위해 여러 다른 쿼럼 모드 중에서 선택할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 문서의 작업을 시작하기 전에 다음 문서를 검토하세요.

* [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오][sap-high-availability-architecture-scenarios]


## <a name="windows-server-failover-clustering-in-azure"></a>Azure에서 Windows Server 장애 조치(Failover) 클러스터링

Azure Virtual Machines를 사용하는 Windows Server 장애 조치(failover) 클러스터링을 사용하려면 추가 구성 단계가 필요합니다. 클러스터를 빌드할 때 SAP ASCS/SCS 인스턴스에 대해 여러 개의 IP 주소 및 가상 호스트 이름을 설정해야 합니다.

### <a name="name-resolution-in-azure-and-the-cluster-virtual-host-name"></a>Azure에서 이름 확인 및 클러스터 가상 호스트 이름

Azure 클라우드 플랫폼은 부동 IP 주소와 같은 가상 IP 주소를 구성하는 옵션을 제공하지 않습니다. 클라우드의 클러스터 리소스에 연결하도록 가상 IP 주소를 설정하기 위한 대체 솔루션이 필요합니다. 

Azure Load Balancer 서비스는 Azure에서 ‘내부 부하 분산 장치’를 제공합니다. 내부 부하 분산 장치를 사용하면 클라이언트는 클러스터 가상 IP 주소를 통해 클러스터에 도달합니다. 

클러스터 노드를 포함하는 리소스 그룹에 부하 분산 장치를 배포합니다. 그런 후 내부 부하 분산 장치의 프로브 포트를 사용하여 필요한 모든 포트 전달 규칙을 구성합니다. 클라이언트는 가상 호스트 이름을 통해 연결할 수 있습니다. DNS 서버는 클러스터 IP 주소를 확인하고 내부 부하 분산 장치는 클러스터의 활성 노드에 대한 포트 전달을 처리합니다.

> [!IMPORTANT]
> 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. 해당 VM에 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.  

![그림 1: 공유 디스크를 사용하지 않는 Azure의 Windows 장애 조치(Failover) 클러스터링 구성][sap-ha-guide-figure-1001]

_공유 디스크를 사용하지 않는 Azure의 Windows Server 장애 조치(Failover) 클러스터링 구성_

### <a name="sap-ascsscs-ha-with-cluster-shared-disks"></a>클러스터 공유 디스크를 사용하는 SAP ASCS/SCS HA
Windows에서 SAP ASCS/SCS 인스턴스에는 SAP 중앙 서비스, SAP 메시지 서버, 인큐 서버 프로세스 및 SAP 글로벌 호스트 파일이 포함됩니다. SAP 글로벌 호스트 파일은 전체 SAP 시스템에 대한 중앙 파일을 저장합니다.

SAP ASCS/SCS 인스턴스에는 다음과 같은 구성 요소가 있습니다.

* SAP 중앙 서비스에는 다음이 포함됩니다.
    * 두 개의 프로세스인 메시지 및 큐에 넣기 서버와 이러한 두 프로세스에 액세스하는 데 사용되는 \<ASCS/SCS virtual host name>
    * 파일 구조: S:\usr\sap\\&lt;SID&gt;\ASCS/SCS\<instance number\>


* SAP 글로벌 호스트 이름:
  * 파일 구조: S:\usr\sap\\&lt;SID&gt;\SYS\..
  * 다음 UNC 경로를 사용하여 이러한 글로벌 S:\usr\sap\\&lt;SID&gt;\SYS\. 파일에 액세스할 수 있도록 하는 sapmnt 파일 공유

    \\\\<ASCS/SCS 가상 호스트 이름\>\sapmnt\\&lt;SID&gt;\SYS\...


![그림 2: SAP ASCS/SCS 인스턴스의 프로세스, 파일 구조 및 글로벌 호스트 sapmnt 파일 공유][sap-ha-guide-figure-8001]

_SAP ASCS/SCS 인스턴스의 프로세스, 파일 구조, 글로벌 호스트 sapmnt 파일 공유_

높은 가용성 설정에서 SAP ASCS/SCS 인스턴스를 클러스터링합니다. *클러스터형 공유 디스크*(이 예제에서 S 드라이브)를 사용하여 SAP ASCS/SCS 및 SAP 글로벌 호스트 파일을 배치합니다.

![그림 3: 공유 디스크를 사용하는 SAP ASCS/SCS HA 아키텍처][sap-ha-guide-figure-8002]

_공유 디스크를 통한 SAP ASCS/SCS HA 아키텍처_


큐에 넣기 서버 복제 1 아키텍처 사용:
* SAP 메시지 및 큐에 넣기 서버 프로세스에 액세스하고 sapmnt 파일 공유를 통해 SAP 글로벌 호스트 파일에 액세스하는 데 동일한 \<ASCS/SCS virtual host name>이 사용됩니다.
* 동일한 클러스터 공유 디스크 S가 공유됩니다.  

큐에 넣기 서버 복제 2 아키텍처 사용: 
* SAP 메시지 서버 프로세스에 액세스하고 sapmnt 파일 공유를 통해 SAP 글로벌 호스트 파일에 액세스하는 데 동일한 \<ASCS/SCS virtual host name>이 사용됩니다.
* 동일한 클러스터 공유 디스크 S가 공유됩니다.
* 큐에 넣기 서버 프로세스에 액세스하기 위한 개별 \<ERS virtual host name>이 있습니다.  

![그림 4: 공유 디스크를 사용하는 SAP ASCS/SCS HA 아키텍처][sap-ha-guide-figure-8003]

_공유 디스크를 통한 SAP ASCS/SCS HA 아키텍처_

#### <a name="shared-disk-and-enqueue-replication-server"></a>공유 디스크 및 큐에 넣기 복제 서버 

1. 공유 디스크는 ERS(큐에 넣기 복제 서버) 인스턴스가 다음과 같은 조건을 이루는 큐에 넣기 서버 복제 1 아키텍처를 통해 지원됩니다.   

   - 클러스터되지 않음
   - `localhost` 이름을 사용
   - 클러스터 노드 각각의 로컬 디스크에 배포됨

2. 마찬가지로 공유 디스크는 ERS2(큐에 넣기 복제 서버 2) 인스턴스가 다음과 같은 조건을 이루는 큐에 넣기 서버 복제 2 아키텍처를 통해 지원됩니다.  

   - 클러스터됨
   - 전용 가상/네트워크 호스트 이름을 사용
   - (A)SCS IP 주소 이외에 Azure Internal Load Balancer 상에 구성될 ERS 가상 호스트 이름의 IP 주소가 필요
   - 클러스터 노드 각각의 **로컬 디스크** 에 배포되므로 공유 디스크 불필요

   > [!TIP]
   > ERS1 및 ERS2(큐에 넣기 복제 서버 1 및 2)에 대해서는 다음을 통해 자세히 확인할 수 있습니다.  
   > [Microsoft 장애 조치(failover) 클러스터 내의 큐에 넣기 복제 서버](https://help.sap.com/viewer/3741bfe0345f4892ae190ee7cfc53d4c/CURRENT_VERSION_SWPM20/en-US/8abd4b52902d4b17a105c2fabdf5c0cf.html)  
   > [장애 조치(failover) 클러스터 환경의 새로운 큐에 넣기 복제기](https://blogs.sap.com/2019/03/19/new-enqueue-replicator-in-failover-cluster-environments/)  

#### <a name="options-for-shared-disk-in-azure-for-sap-workloads"></a>SAP 워크로드에 대한 Azure 내 공유 디스크 옵션

Azure 내 Windows 장애 조치(failover) 클러스터에는 공유 디스크용 옵션이 두 개 있습니다.

- [Azure 공유 디스크](../../disks-shared.md) - 기능은 Azure 관리형 디스크를 동시에 다수의 VM에 연결할 수 있도록 합니다. 
- 타사 소프트웨어[SIOS DataKeeper Cluster Edition](https://us.sios.com/products/datakeeper-cluster)를 사용해 클러스터 공유 스토리지를 시뮬레이션하는 미러된 스토리지를 만듭니다. 

공유 디스크에 대한 기술을 선택할 때는 다음 사항을 고려해야 합니다.

**SAP 워크로드를 위한 Azure 공유 디스크**
- 별도의 소프트웨어를 유지하고 작동할 필요 없이 Azure 관리형 디스크를 동시에 다수의 VM에 연결할 수 있게 합니다. 
- 하나의 스토리지 클러스터에 대해 하나의 Azure 공유 디스크로 작업합니다. 이는 SAP 솔루션의 안정성에 영향을 줍니다.
- 현재는 가용성 집합에서 Azure 공유 프리미엄 디스크를 사용하는 배포만을 지원합니다. Azure 공유 디스크는 영역 배포에서는 지원되지 않습니다.     
- [프리미엄 SSD 범위](../../disks-shared.md#disk-sizes)에서 지정된 최소 디스크 크기를 사용해 Azure 프리미엄 디스크를 프로비저닝하여 필요한 수량의 VM에 동시에 연결할 수 있는지 확인합니다(기본적으로는 SAP ASCS Windows 장애 조치(failover) 클러스터당 2대). 
- Azure 공유 Ultra disk는 가용성 집합에서의 배포나 영역 배포를 지원하지 않으므로 SAP 워크로드에 지원되지 않습니다.  
 
**SIOS**
- SIOS 솔루션은 디스크 두 개 사이의 실시간 동기 데이터 복제를 제공합니다.
- SIOS 솔루션을 통해 관리 디스크 두 개를 운영하며, 가용성 집합이나 가용성 영역을 사용하는 경우 해당 관리 디스크가 서로 다른 스토리지 클러스터에 배치됩니다. 
- 가용성 영역에 배포할 수 있습니다.
- 추가로 구매해야 하는 타사 소프트웨어를 설치하여 운영해야 합니다.

### <a name="shared-disk-using-azure-shared-disk"></a>Azure 공유 디스크를 사용하는 공유 디스크

Microsoft는 공유 디스크 옵션에서 SAP ASCS/SCS 고가용성을 구현하는 데 사용할 수 있는 [Azure 공유 디스크](../../disks-shared.md)를 제공합니다.

#### <a name="prerequisites-and-limitations"></a>필수 구성 요소 및 제한 사항

현재, Azure 프리미엄 SSD 디스크를 SAP ASCS/SCS 인스턴스용 Azure 공유 디스크로 사용할 수 있습니다. 현재 적용 중인 제한 사항은 다음과 같습니다.

-  [Azure Ultra disk](../../disks-types.md#ultra-disk)는 SAP 워크로드용 Azure 공유 디스크로 지원되지 않습니다. 현재 가용성 집합의 Azure Ultra Disk를 사용하여 Azure VM을 배치할 수 없습니다.
-  프리미엄 SSD 디스크가 있는 [Azure 공유 디스크](../../disks-shared.md)는 가용성 집합의 VM에서만 지원됩니다. 가용성 영역 배포에서는 지원되지 않습니다. 
-  Azure 공유 디스크 값인 [maxShares](../../disks-shared-enable.md?tabs=azure-cli#disk-sizes)는 공유 디스크로 사용할 수 있는 클러스터 노드의 개수를 결정합니다. 일반적으로 SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터에서는 두 개의 노드를 구성하므로 `maxShares`에 해당하는 값은 2로 설정되어야 합니다.
-  모든 SAP ASCS/SCS 클러스터 VM은 동일한 [Azure 근접 배치 그룹](../../windows/proximity-placement-groups.md)에 배포되어야 합니다.   
   PPG가 없는 Azure 공유 디스크를 사용해 가용성 집합에 Windows 클러스터 VM을 배포할 수 있지만, PPG가 있으면 Azure 공유 디스크와 클러스터 VM의 물리적 근접성을 확보할 수 있으므로 해당 VM과 스토리지 계층 간의 대기 시간을 줄일 수 있습니다.    

Azure 공유 디스크의 제한 사항에 대한 자세한 내용은 Azure 공유 디스크 설명서의 [제한 사항](../../disks-shared.md#limitations) 섹션을 꼼꼼히 검토하여 참조하세요.

> [!IMPORTANT]
> SAP ASCS/SCS Windows 장애 조치(Failover) 클러스터를 Azure 공유 디스크로 배포하는 경우, 해당 배포가 하나의 스토리지 클러스터에서 단일 공유 디스크로 작동한다는 점에 유의합니다. Azure 공유 디스크가 배포된 스토리지 클러스터에 문제가 생기면 SAP ASCS/SCS 인스턴스가 영향을 받습니다.    

> [!TIP]
> [Azure 계획 가이드 상의 SAP Netweaver](./planning-guide.md) 및 [SAP 워크로드에 대한 Azure Storage 가이드](./planning-guide-storage.md)를 검토하여 SAP 배포를 계획할 때 중요하게 고려해야 할 사항을 확인하세요.

### <a name="supported-os-versions"></a>지원된 OS 버전

Windows Server 2016과 2019가 모두 지원됩니다(최신 데이터 센터 이미지 사용).

다음과 같은 이유로 인해 **Windows Server 2019 Datacenter** 를 사용하는 것이 훨씬 좋습니다.
- Windows 2019 장애 조치(Failover) 클러스터 서비스가 Azure 인식
- Azure 호스트 유지 관리에 대한 통합 및 인식이 추가되었으며, Azure 일정 이벤트를 모니터링하여 향상된 경험을 제공합니다.
- 분산 네트워크 이름(기본 옵션)을 사용할 수 있습니다. 따라서, 클러스터 네트워크 이름을 위해 전용 IP 주소가 있을 필요가 없습니다. 또한, 해당 IP 주소를 Azure Internal Load Balancer에 구성할 필요도 없습니다. 

### <a name="shared-disks-in-azure-with-sios-datakeeper"></a>SIOS DataKeeper를 사용한 Azure의 공유 디스크

공유 디스크를 위한 또다른 옵션은 타사 소프트웨어 SIOS DataKeeper Cluster Edition을 사용해 클러스터 공유 스토리지를 시뮬레이션하는 미러된 스토리지를 만드는 것입니다. SIOS 솔루션은 실시간 동기 데이터 복제 기능을 제공합니다.

클러스터에 대한 공유 디스크 리소스를 만들려면

1. Windows 클러스터 구성에 있는 각 가상 머신에 추가 디스크 하나를 연결합니다.
2. 두 가상 머신 노드에서 SIOS DataKeeper Cluster Edition을 실행합니다.
3. 원본 가상 머신의 추가 디스크 연결 볼륨의 콘텐츠를 대상 가상 머신의 추가 디스크 연결 볼륨에 미러링하는 방식으로 SIOS DataKeeper Cluster Edition을 구성합니다. SIOS DataKeeper는 원본 및 대상 로컬 볼륨을 추상화한 다음 Windows Server 장애 조치(Failover) 클러스터링에 단일 공유 디스크로 제공합니다.

[SIOS DataKeeper](https://us.sios.com/products/datakeeper-cluster/)에 대한 자세한 정보를 참조하세요.

![그림 5: SIOS DataKeeper를 사용하는 Azure의 Windows Server 장애 조치(Failover) 클러스터링 구성][sap-ha-guide-figure-1002]

_SIOS DataKeeper를 사용한 Azure의 Windows 장애 조치(Failover) 클러스터링 구성_

> [!NOTE]
> SQL Server와 같은 일부 DBMS 제품에서는 가용성을 높이기 위해 공유 디스크를 사용할 필요가 없습니다. SQL Server AlwaysOn은 한 클러스터 노드의 로컬 디스크에서 다른 클러스터 노드의 로컬 디스크로 DBMS 데이터 및 로그 파일을 복제합니다. 이 경우 Windows 클러스터 구성에는 공유 디스크가 필요하지 않습니다.
>
## <a name="optional-configurations"></a>선택적 구성

다음 다이어그램에서는 총 VM 수를 줄이기 위해 Microsoft Windows 장애 조치(failover) 클러스터를 실행하는 Azure VM의 여러 SAP 인스턴스를 보여 줍니다.

SAP ASCS/SCS 클러스터의 로컬 SAP Application Server 또는 Microsoft SQL Server Always On 노드의 SAP ASCS/SCS 클러스터 역할일 수 있습니다.

> [!IMPORTANT]
> SQL Server Always On 노드에 로컬 SAP Application Server를 설치하는 것은 지원되지 않습니다.
>

SAP ASCS/SCS와 Microsoft SQL Server 데이터베이스는 모두 SPOF(단일 실패 지점)입니다. Windows 환경에서 이러한 SPOF를 보호하기 위해 WSFC가 사용됩니다.

SAP ASCS/SCS의 리소스 사용량은 매우 작지만 SQL Server 또는 SAP Application Server에 대한 메모리 구성을 2GB씩 줄이는 것이 좋습니다.

### <a name="sap-application-servers-on-wsfc-nodes-using-sios-datakeeper"></a>SIOS DataKeeper를 사용하는 WSFC 노드의 SAP Application Server

![그림 6: SIOS DataKeeper 및 로컬로 설치된 SAP Application Server를 사용하여 Azure에서 Windows Server 장애 조치(failover) 클러스터링 구성][sap-ha-guide-figure-1003]

> [!NOTE]
> SAP Application Server는 로컬로 설치되므로 그림과 같이 동기화를 설정하지 않아도 됩니다.
>
### <a name="sap-ascsscs-on-sql-server-always-on-nodes-using-sios-datakeeper"></a>SIOS DataKeeper를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS

![그림 7: SIOS DataKeeper를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS][sap-ha-guide-figure-1005]

[Windows SOFS를 사용하는 WSFC 노드의 SAP Application Server에 대한 선택적 구성][optional-fileshare]

[NetApp Files SMB를 사용하는 WSFC 노드의 SAP Application Server에 대한 선택적 구성][optional-smb]

[Windows SOFS를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS에 대한 선택적 구성][optional-fileshare-sql]

[NetApp Files SMB를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS에 대한 선택적 구성][optional-smb-sql]

## <a name="next-steps"></a>다음 단계

* [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스에 대한 SAP HA용 Azure 인프라 준비][sap-high-availability-infrastructure-wsfc-shared-disk]

* [SAP ASCS/SCS 인스턴스에 대한 Windows 장애 조치(Failover) 클러스터 및 공유 디스크에 SAP NetWeaver HA 설치][sap-high-availability-installation-wsfc-shared-disk]


[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2243692]:https://launchpad.support.sap.com/#/notes/2243692

[sap-installation-guides]:http://service.sap.com/instguides

[azure-resource-manager/management/azure-subscription-service-limits]:../../../azure-resource-manager/management/azure-subscription-service-limits.md
[azure-resource-manager/management/azure-subscription-service-limits-subscription]:../../../azure-resource-manager/management/azure-subscription-service-limits.md

[dbms-guide]:../../virtual-machines-windows-sap-dbms-guide.md

[deployment-guide]:deployment-guide.md

[dr-guide-classic]:https://go.microsoft.com/fwlink/?LinkID=521971

[getting-started]:get-started.md
[ha-guide]:sap-high-availability-guide.md
[sap-high-availability-architecture-scenarios]:sap-high-availability-architecture-scenarios.md
[sap-high-availability-infrastructure-wsfc-shared-disk]:sap-high-availability-infrastructure-wsfc-shared-disk.md
[sap-high-availability-installation-wsfc-shared-disk]:sap-high-availability-installation-wsfc-shared-disk.md

[planning-guide]:planning-guide.md
[planning-guide-11]:planning-guide.md
[planning-guide-2.1]:planning-guide.md#1625df66-4cc6-4d60-9202-de8a0b77f803
[planning-guide-2.2]:planning-guide.md#f5b3b18c-302c-4bd8-9ab2-c388f1ab3d10

[planning-guide-microsoft-azure-networking]:planning-guide.md#61678387-8868-435d-9f8c-450b2424f5bd
[planning-guide-storage-microsoft-azure-storage-and-data-disks]:planning-guide.md#a72afa26-4bf4-4a25-8cf7-855d6032157f

[sap-ha-guide]:sap-high-availability-guide.md
[sap-ha-guide-2]:#42b8f600-7ba3-4606-b8a5-53c4f026da08
[sap-ha-guide-4]:#8ecf3ba0-67c0-4495-9c14-feec1a2255b7
[sap-ha-guide-8]:#78092dbe-165b-454c-92f5-4972bdbef9bf
[sap-ha-guide-8.1]:#c87a8d3f-b1dc-4d2f-b23c-da4b72977489
[sap-ha-guide-8.9]:#fe0bd8b5-2b43-45e3-8295-80bee5415716
[sap-ha-guide-8.11]:#661035b2-4d0f-4d31-86f8-dc0a50d78158
[sap-ha-guide-8.12]:#0d67f090-7928-43e0-8772-5ccbf8f59aab
[sap-ha-guide-8.12.1]:#5eecb071-c703-4ccc-ba6d-fe9c6ded9d79
[sap-ha-guide-8.12.3]:#5c8e5482-841e-45e1-a89d-a05c0907c868
[sap-ha-guide-8.12.3.1]:#1c2788c3-3648-4e82-9e0d-e058e475e2a3
[sap-ha-guide-8.12.3.2]:#dd41d5a2-8083-415b-9878-839652812102
[sap-ha-guide-8.12.3.3]:#d9c1fc8e-8710-4dff-bec2-1f535db7b006
[sap-ha-guide-9]:#a06f0b49-8a7a-42bf-8b0d-c12026c5746b
[sap-ha-guide-9.1]:#31c6bd4f-51df-4057-9fdf-3fcbc619c170
[sap-ha-guide-9.1.1]:#a97ad604-9094-44fe-a364-f89cb39bf097

[sap-ha-multi-sid-guide]:sap-high-availability-multi-sid.md (SAP 다중 SID 고가용성 구성)

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png

[sap-ha-guide-figure-1000]:./media/virtual-machines-shared-sap-high-availability-guide/1000-wsfc-for-sap-ascs-on-azure.png
[sap-ha-guide-figure-1001]:./media/virtual-machines-shared-sap-high-availability-guide/1001-wsfc-on-azure-ilb.png
[sap-ha-guide-figure-1003]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sios-as.png
[sap-ha-guide-figure-1005]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sql-ascs-sios.png
[sap-ha-guide-figure-1002]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sios.png
[sap-ha-guide-figure-2000]:./media/virtual-machines-shared-sap-high-availability-guide/2000-wsfc-sap-as-ha-on-azure.png
[sap-ha-guide-figure-2001]:./media/virtual-machines-shared-sap-high-availability-guide/2001-wsfc-sap-ascs-ha-on-azure.png
[sap-ha-guide-figure-2003]:./media/virtual-machines-shared-sap-high-availability-guide/2003-wsfc-sap-dbms-ha-on-azure.png
[sap-ha-guide-figure-2004]:./media/virtual-machines-shared-sap-high-availability-guide/2004-wsfc-sap-ha-e2e-archit-template1-on-azure.png
[sap-ha-guide-figure-2005]:./media/virtual-machines-shared-sap-high-availability-guide/2005-wsfc-sap-ha-e2e-arch-template2-on-azure.png

[sap-ha-guide-figure-3000]:./media/virtual-machines-shared-sap-high-availability-guide/3000-template-parameters-sap-ha-arm-on-azure.png
[sap-ha-guide-figure-3001]:./media/virtual-machines-shared-sap-high-availability-guide/3001-configuring-dns-servers-for-Azure-vnet.png
[sap-ha-guide-figure-3002]:./media/virtual-machines-shared-sap-high-availability-guide/3002-configuring-static-IP-address-for-network-card-of-each-vm.png
[sap-ha-guide-figure-3003]:./media/virtual-machines-shared-sap-high-availability-guide/3003-setup-static-ip-address-ilb-for-ascs-instance.png
[sap-ha-guide-figure-3004]:./media/virtual-machines-shared-sap-high-availability-guide/3004-default-ascs-scs-ilb-balancing-rules-for-azure-ilb.png
[sap-ha-guide-figure-3005]:./media/virtual-machines-shared-sap-high-availability-guide/3005-changing-ascs-scs-default-ilb-rules-for-azure-ilb.png
[sap-ha-guide-figure-3006]:./media/virtual-machines-shared-sap-high-availability-guide/3006-adding-vm-to-domain.png
[sap-ha-guide-figure-3007]:./media/virtual-machines-shared-sap-high-availability-guide/3007-config-wsfc-1.png
[sap-ha-guide-figure-3008]:./media/virtual-machines-shared-sap-high-availability-guide/3008-config-wsfc-2.png
[sap-ha-guide-figure-3009]:./media/virtual-machines-shared-sap-high-availability-guide/3009-config-wsfc-3.png
[sap-ha-guide-figure-3010]:./media/virtual-machines-shared-sap-high-availability-guide/3010-config-wsfc-4.png
[sap-ha-guide-figure-3011]:./media/virtual-machines-shared-sap-high-availability-guide/3011-config-wsfc-5.png
[sap-ha-guide-figure-3012]:./media/virtual-machines-shared-sap-high-availability-guide/3012-config-wsfc-6.png
[sap-ha-guide-figure-3013]:./media/virtual-machines-shared-sap-high-availability-guide/3013-config-wsfc-7.png
[sap-ha-guide-figure-3014]:./media/virtual-machines-shared-sap-high-availability-guide/3014-config-wsfc-8.png
[sap-ha-guide-figure-3015]:./media/virtual-machines-shared-sap-high-availability-guide/3015-config-wsfc-9.png
[sap-ha-guide-figure-3016]:./media/virtual-machines-shared-sap-high-availability-guide/3016-config-wsfc-10.png
[sap-ha-guide-figure-3017]:./media/virtual-machines-shared-sap-high-availability-guide/3017-config-wsfc-11.png
[sap-ha-guide-figure-3018]:./media/virtual-machines-shared-sap-high-availability-guide/3018-config-wsfc-12.png
[sap-ha-guide-figure-3019]:./media/virtual-machines-shared-sap-high-availability-guide/3019-assign-permissions-on-share-for-cluster-name-object.png
[sap-ha-guide-figure-3020]:./media/virtual-machines-shared-sap-high-availability-guide/3020-change-object-type-include-computer-objects.png
[sap-ha-guide-figure-3021]:./media/virtual-machines-shared-sap-high-availability-guide/3021-check-box-for-computer-objects.png
[sap-ha-guide-figure-3022]:./media/virtual-machines-shared-sap-high-availability-guide/3022-set-security-attributes-for-cluster-name-object-on-file-share-quorum.png
[sap-ha-guide-figure-3023]:./media/virtual-machines-shared-sap-high-availability-guide/3023-call-configure-cluster-quorum-setting-wizard.png
[sap-ha-guide-figure-3024]:./media/virtual-machines-shared-sap-high-availability-guide/3024-selection-screen-different-quorum-configurations.png
[sap-ha-guide-figure-3025]:./media/virtual-machines-shared-sap-high-availability-guide/3025-selection-screen-file-share-witness.png
[sap-ha-guide-figure-3026]:./media/virtual-machines-shared-sap-high-availability-guide/3026-define-file-share-location-for-witness-share.png
[sap-ha-guide-figure-3027]:./media/virtual-machines-shared-sap-high-availability-guide/3027-successful-reconfiguration-cluster-file-share-witness.png
[sap-ha-guide-figure-3028]:./media/virtual-machines-shared-sap-high-availability-guide/3028-install-dot-net-framework-35.png
[sap-ha-guide-figure-3029]:./media/virtual-machines-shared-sap-high-availability-guide/3029-install-dot-net-framework-35-progress.png
[sap-ha-guide-figure-3030]:./media/virtual-machines-shared-sap-high-availability-guide/3030-sios-installer.png
[sap-ha-guide-figure-3031]:./media/virtual-machines-shared-sap-high-availability-guide/3031-first-screen-sios-data-keeper-installation.png
[sap-ha-guide-figure-3032]:./media/virtual-machines-shared-sap-high-availability-guide/3032-data-keeper-informs-service-be-disabled.png
[sap-ha-guide-figure-3033]:./media/virtual-machines-shared-sap-high-availability-guide/3033-user-selection-sios-data-keeper.png
[sap-ha-guide-figure-3034]:./media/virtual-machines-shared-sap-high-availability-guide/3034-domain-user-sios-data-keeper.png
[sap-ha-guide-figure-3035]:./media/virtual-machines-shared-sap-high-availability-guide/3035-provide-sios-data-keeper-license.png
[sap-ha-guide-figure-3036]:./media/virtual-machines-shared-sap-high-availability-guide/3036-data-keeper-management-config-tool.png
[sap-ha-guide-figure-3037]:./media/virtual-machines-shared-sap-high-availability-guide/3037-tcp-ip-address-first-node-data-keeper.png
[sap-ha-guide-figure-3038]:./media/virtual-machines-shared-sap-high-availability-guide/3038-create-replication-sios-job.png
[sap-ha-guide-figure-3039]:./media/virtual-machines-shared-sap-high-availability-guide/3039-define-sios-replication-job-name.png
[sap-ha-guide-figure-3040]:./media/virtual-machines-shared-sap-high-availability-guide/3040-define-sios-source-node.png
[sap-ha-guide-figure-3041]:./media/virtual-machines-shared-sap-high-availability-guide/3041-define-sios-target-node.png
[sap-ha-guide-figure-3042]:./media/virtual-machines-shared-sap-high-availability-guide/3042-define-sios-synchronous-replication.png
[sap-ha-guide-figure-3043]:./media/virtual-machines-shared-sap-high-availability-guide/3043-enable-sios-replicated-volume-as-cluster-volume.png
[sap-ha-guide-figure-3044]:./media/virtual-machines-shared-sap-high-availability-guide/3044-data-keeper-synchronous-mirroring-for-SAP-gui.png
[sap-ha-guide-figure-3045]:./media/virtual-machines-shared-sap-high-availability-guide/3045-replicated-disk-by-data-keeper-in-wsfc.png
[sap-ha-guide-figure-3046]:./media/virtual-machines-shared-sap-high-availability-guide/3046-dns-entry-sap-ascs-virtual-name-ip.png
[sap-ha-guide-figure-3047]:./media/virtual-machines-shared-sap-high-availability-guide/3047-dns-manager.png
[sap-ha-guide-figure-3048]:./media/virtual-machines-shared-sap-high-availability-guide/3048-default-cluster-probe-port.png
[sap-ha-guide-figure-3049]:./media/virtual-machines-shared-sap-high-availability-guide/3049-cluster-probe-port-after.png
[sap-ha-guide-figure-3050]:./media/virtual-machines-shared-sap-high-availability-guide/3050-service-type-ers-delayed-automatic.png
[sap-ha-guide-figure-5000]:./media/virtual-machines-shared-sap-high-availability-guide/5000-wsfc-sap-sid-node-a.png
[sap-ha-guide-figure-5001]:./media/virtual-machines-shared-sap-high-availability-guide/5001-sios-replicating-local-volume.png
[sap-ha-guide-figure-5002]:./media/virtual-machines-shared-sap-high-availability-guide/5002-wsfc-sap-sid-node-b.png
[sap-ha-guide-figure-5003]:./media/virtual-machines-shared-sap-high-availability-guide/5003-sios-replicating-local-volume-b-to-a.png

[sap-ha-guide-figure-6003]:./media/virtual-machines-shared-sap-high-availability-guide/6003-sap-multi-sid-full-landscape.png


[sap-ha-guide-figure-8001]:./media/virtual-machines-shared-sap-high-availability-guide/8001.png
[sap-ha-guide-figure-8002]:./media/virtual-machines-shared-sap-high-availability-guide/8002.png
[sap-ha-guide-figure-8003]:./media/virtual-machines-shared-sap-high-availability-guide/8003.png
[sap-ha-guide-figure-8004]:./media/virtual-machines-shared-sap-high-availability-guide/8004.png
[sap-ha-guide-figure-8005]:./media/virtual-machines-shared-sap-high-availability-guide/8005.png
[sap-ha-guide-figure-8006]:./media/virtual-machines-shared-sap-high-availability-guide/8006.png
[sap-ha-guide-figure-8007]:./media/virtual-machines-shared-sap-high-availability-guide/8007.png
[sap-ha-guide-figure-8008]:./media/virtual-machines-shared-sap-high-availability-guide/8008.png
[sap-ha-guide-figure-8009]:./media/virtual-machines-shared-sap-high-availability-guide/8009.png
[sap-ha-guide-figure-8010]:./media/virtual-machines-shared-sap-high-availability-guide/8010.png
[sap-ha-guide-figure-8011]:./media/virtual-machines-shared-sap-high-availability-guide/8011.png
[sap-ha-guide-figure-8012]:./media/virtual-machines-shared-sap-high-availability-guide/8012.png
[sap-ha-guide-figure-8013]:./media/virtual-machines-shared-sap-high-availability-guide/8013.png
[sap-ha-guide-figure-8014]:./media/virtual-machines-shared-sap-high-availability-guide/8014.png
[sap-ha-guide-figure-8015]:./media/virtual-machines-shared-sap-high-availability-guide/8015.png
[sap-ha-guide-figure-8016]:./media/virtual-machines-shared-sap-high-availability-guide/8016.png
[sap-ha-guide-figure-8017]:./media/virtual-machines-shared-sap-high-availability-guide/8017.png
[sap-ha-guide-figure-8018]:./media/virtual-machines-shared-sap-high-availability-guide/8018.png
[sap-ha-guide-figure-8019]:./media/virtual-machines-shared-sap-high-availability-guide/8019.png
[sap-ha-guide-figure-8020]:./media/virtual-machines-shared-sap-high-availability-guide/8020.png
[sap-ha-guide-figure-8021]:./media/virtual-machines-shared-sap-high-availability-guide/8021.png
[sap-ha-guide-figure-8022]:./media/virtual-machines-shared-sap-high-availability-guide/8022.png
[sap-ha-guide-figure-8023]:./media/virtual-machines-shared-sap-high-availability-guide/8023.png
[sap-ha-guide-figure-8024]:./media/virtual-machines-shared-sap-high-availability-guide/8024.png
[sap-ha-guide-figure-8025]:./media/virtual-machines-shared-sap-high-availability-guide/8025.png


[sap-templates-3-tier-multisid-xscs-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs%2Fazuredeploy.json
[sap-templates-3-tier-multisid-xscs-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-db%2Fazuredeploy.json
[sap-templates-3-tier-multisid-db-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-db-md%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-apps%2Fazuredeploy.json
[sap-templates-3-tier-multisid-apps-marketplace-image-md]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-apps-md%2Fazuredeploy.json

[virtual-machines-azure-resource-manager-architecture-benefits-arm]:../../../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager

[virtual-machines-manage-availability]:../../virtual-machines-windows-manage-availability.md
[optional-smb]:high-availability-guide-windows-netapp-files-smb.md#5121771a-7618-4f36-ae14-ccf9ee5f2031 (NetApp Files SMB를 사용하는 WSFC 노드의 SAP Application Server에 대한 선택적 구성)
[optional-fileshare]:sap-high-availability-guide-wsfc-file-share.md#86cb3ee0-2091-4b74-be77-64c2e6424f50 (Windows SOFS를 사용하는 WSFC 노드의 SAP Application Server에 대한 선택적 구성)
[optional-smb-sql]:high-availability-guide-windows-netapp-files-smb.md#01541cf2-0a03-48e3-971e-e03575fa7b4f (NetApp Files SMB를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS에 대한 선택적 구성)
[optional-fileshare-sql]:sap-high-availability-guide-wsfc-file-share.md#db335e0d-09b4-416b-b240-afa18505f503 (Windows SOFS를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS에 대한 선택적 구성)