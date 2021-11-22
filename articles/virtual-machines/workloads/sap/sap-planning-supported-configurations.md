---
title: 'Azure의 SAP: Azure VM에서 지원되는 시나리오'
description: Azure Virtual Machines에서 지원하는 SAP 워크로드 시나리오
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3975f23ad184ec04849fca57ef37d5fe57c2b48f
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530543"
---
# <a name="sap-workload-on-azure-virtual-machine-supported-scenarios"></a>Azure 가상 머신의 SAP 워크로드 지원 시나리오
Azure에서 SAP NetWeaver, Business one `Hybris` 또는 S/4HANA 시스템 아키텍처를 디자인하면 다양한 아키텍처 및 도구를 사용하여 확장성 있고 효율적인 고가용성 배포를 수행할 수 있는 여러 기회가 열립니다. 사용하는 운영 체제 또는 DBMS에 따라 다르지만 제한이 있습니다. 또한 온-프레미스에서 지원되는 모든 시나리오가 Azure에서도 똑같은 방식으로 지원되는 것은 아닙니다. 이 문서에서는 Azure VM을 독점적으로 사용하는 지원되는 고가용성 구성 및 비-고가용성 구성과 아키텍처를 안내합니다. [HANA 대규모 인스턴스](./hana-overview-architecture.md)를 지원하는 시나리오는 [HANA 대규모 인스턴스의 지원되는 시나리오](./hana-supported-scenario.md) 문서를 참조하세요. 


## <a name="2-tier-configuration"></a>2계층 구성
SAP 2 계층 구성은 동일한 서버 또는 VM 단위에서 실행되는 SAP DBMS 및 애플리케이션 계층이 조합된 계층에서 빌드되는 것으로 간주됩니다. 두 번째 계층은 사용자 인터페이스 계층으로 간주됩니다. 2계층 구성의 경우 DBMS 및 SAP 애플리케이션 계층은 Azure VM 리소스를 공유합니다. 결과적으로 이러한 구성 요소가 리소스를 놓고 경합하지 않는 방식으로 다른 구성 요소를 구성해야 합니다. 또한 VM 리소스를 초과 구독하지 않도록 주의해야 합니다. 이러한 구성은 여러 관련 Azure 구성 요소의 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 초과하는 고가용성을 제공하지 않습니다.

이 구성을 그림으로 표시하면 다음과 같습니다.

![간단한 2계층 구성](./media/sap-planning-supported-configurations/two-tier-simple-configuration.png)

이러한 구성은 프로덕션 및 비프로덕션 사례를 위한 SQL Server, Oracle, Db2, maxDB 및 SAP ASE의 DBMS 시스템에 사용되는 Windows, Red Hat, SUSE 및 Oracle Linux에서 지원됩니다. DBMS로 사용되는 SAP HANA의 경우 이러한 유형의 구성은 비프로덕션 사례에만 지원됩니다. [Azure HANA 대규모 인스턴스](./hana-overview-architecture.md)의 배포 사례도 여기에 포함됩니다.
Azure에서 지원되는 모든 OS/DBMS 조합의 경우 이 유형의 구성이 지원됩니다. 그러나 DBMS 및 SAP 구성 요소가 메모리와 CPU 리소스를 두고 경합하여 실제 가용 리소스를 초과하는 일이 없도록 DBMS 및 SAP 구성 요소의 구성을 설정해야 합니다. 이 작업은 DBMS에 할당할 수 있는 메모리를 제한하는 방법으로 수행해야 합니다. 애플리케이션 인스턴스의 SAP 확장 메모리도 제한해야 합니다. 또한 VM의 전체적인 CPU 사용을 모니터링하여 구성 요소가 CPU 리소스를 최대화하지 않도록 해야 합니다. 

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 이 문서의 뒷부분에 설명된 것처럼 추가 고가용성 및 최종 재해 복구 구성을 사용하는 것이 좋습니다.


## <a name="3-tier-configuration"></a>3계층 구성
이 구성에서는 SAP 애플리케이션 계층과 DBMS 계층을 서로 다른 VM에 분리합니다. 일반적으로 규모가 크고 SAP 애플리케이션 계층의 리소스에서 더 많은 유연성을 확보할 이유가 없는 대규모 시스템에서 이렇게 구성합니다. 가장 간단한 설정에서는 여러 관련 Azure 구성 요소의 [Azure 서비스 수준 계약](https://azure.microsoft.com/support/legal/sla/)을 벗어나는 고가용성이 제공되지 않습니다. 

그림으로 표현하면 다음과 같습니다.

![간단한 3계층 구성을 보여주는 다이어그램](./media/sap-planning-supported-configurations/three-tier-simple-configuration.png)

이 유형의 구성은 프로덕션 및 비프로덕션 사례를 위한 SQL Server, Oracle, Db2, SAP HANA, maxDB 및 SAP ASE의 DBMS 시스템에 사용되는 Windows, Red Hat, SUSE 및 Oracle Linux에서 지원됩니다. 이 구성은 [Azure HANA 대규모 인스턴스](./hana-overview-architecture.md)의 기본 배포 구성입니다. 간단한 설명을 위해 SAP 애플리케이션 계층에서 SAP Central Services와 SAP 대화 상자 인스턴스를 구분하지 않았습니다. 이 간단한 3계층 구성에서는 SAP Central Services에 대한 고가용성 보호가 없습니다.

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 이 문서의 뒷부분에 설명된 것처럼 추가 고가용성 및 최종 재해 복구 구성을 사용하는 것이 좋습니다.


## <a name="multiple-dbms-instances-per-vm-or-hana-large-instance-unit"></a>VM 또는 HANA 대규모 인스턴스 단위당 여러 DBMS 인스턴스
이 유형의 구성에서는 Azure VM 또는 HANA 대규모 인스턴스 단위에 여러 DBMS 인스턴스를 호스트합니다. 유지 관리할 운영 체제가 적고 따라서 비용이 절감된다는 장점이 있습니다. 다른 장점으로는 큰 VM 또는 HANA 대규모 인스턴스 단위의 리소스를 여러 DBMS 인스턴스 간에 공유하여 유연성과 효율성을 높일 수 있습니다. 지금까지 이러한 구성은 대부분 비프로덕션 시스템에서만 관찰되었습니다.

이 구성은 다음과 같습니다.

![한 단위의 여러 DBMS 인스턴스](./media/sap-planning-supported-configurations/multiple-database-instances.png)

이 유형의 DBMS 배포는 다음을 지원합니다.

- Windows의 SQL Server
- IBM Db2. 자세한 내용은 [여러 인스턴스(Linux, UNIX)](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_10.5.0/com.ibm.db2.luw.admin.dbobj.doc/doc/c0004904.html) 문서를 참조하세요.
- Oracle. 자세한 내용은 [SAP 지원 노트 #1778431](https://launchpad.support.sap.com/#/notes/1778431) 및 관련 SAP 노트를 참조하세요.
- SAP HANA의 경우 VM 하나에 여러 인스턴스를 배포하는 것이 지원되며, SAP에서는 이 배포 방법을 MCOS라고 부릅니다. 자세한 내용은 SAP 문서 [하나의 호스트에 여러 SAP HANA 시스템 배포(MCOS)](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/2.0.02/
- /b2751fd43bec41a9a14e01913f1edf18.html)를 참조하세요.

하나의 호스트에서 여러 데이터베이스 인스턴스를 실행하는 경우 서로 다른 인스턴스가 리소스를 두고 경합하여 VM의 물리적 리소스 제한을 초과하는 일이 없도록 해야 합니다. VM을 공유하는 모든 인스턴스가 할당할 수 있는 메모리를 제한해야 하는 경우에 특히 그렇습니다. 여러 데이터베이스 인스턴스에서 사용할 수 있는 CPU 리소스에도 똑같이 적용될 수 있습니다. 지금까지 언급한 모든 DBMS는 인스턴스 수준에서 메모리 할당과 CPU 리소스를 제한할 수 있는 구성이 있습니다.
Azure VM에서 이러한 구성을 지원하기 위해, 다른 인스턴스에서 관리하는 데이터베이스의 데이터 및 로그/다시 실행 로그 파일에 사용되는 디스크 또는 볼륨이 분리될 것으로 기대합니다. 다시 말해서, 다른 DBMS 인스턴스에서 관리하는 데이터베이스의 데이터 또는 로그/다시 실행 로그 파일은 동일한 디스크 또는 볼륨을 공유할 수 없습니다. 

HANA 대규모 인스턴스의 디스크 구성은 구성된 상태로 전달되며 [HANA 대규모 인스턴스의 지원되는 시나리오](./hana-supported-scenario.md#single-node-mcos)에 자세히 설명되어 있습니다. 

> [!NOTE]
> 프로덕션 SAP 시스템의 경우 이 문서의 뒷부분에 설명된 것처럼 추가 고가용성 및 최종 재해 복구 구성을 사용하는 것이 좋습니다. 여러 DBMS 인스턴스가 있는 VM은 이 문서의 뒷부분에 설명된 고가용성 구성을 지원하지 않습니다.


## <a name="multiple-sap-dialog-instances-in-one-vm"></a>하나의 VM에 여러 SAP 대화 상자 인스턴스
여러 대화 상자 인스턴스가 운영 체제 미설치 서버 또는 프라이빗 클라우드에서 실행되는 VM에 배포되는 경우가 많았습니다. 이렇게 구성하는 이유는 특정 SAP 대화 상자 인스턴스를 특정 워크로드, 비즈니스 기능 또는 워크로드 유형에 맞게 조정하는 것이었습니다. 이러한 인스턴스를 별도의 VM에 격리하지 않는 이유는 운영 체제 유지 관리 및 운영에 많은 노력이 들기 때문입니다. 또는 VM의 호스터 또는 운영자가 운영 및 관리하는 VM마다 월별 요금을 요구하기 때문입니다. Azure에서 단일 VM에 여러 SAP 대화 상자 인스턴스를 호스팅하는 시나리오는 Windows, Red Hat, SUSE 및 Oracle Linux 운영 체제에서 프로덕션 및 비프로덕션 용도로 지원됩니다. 여러 SAP 애플리케이션 서버 인스턴스가 단일 VM에서 실행되는 경우 Windows 및 최신 Linux 커널에서 사용할 수 있는 SAP 커널 매개 변수 PHYS_MEMSIZE를 설정해야 합니다. 또한 Windows처럼 SAP 확장 메모리의 자동 증가가 구현된 운영 체제에서는 SAP 확장 메모리의 확장을 제한하는 것이 좋습니다. 이 작업은 SAP 프로필 매개 변수 `em/max_size_MB`를 사용하여 수행할 수 있습니다.

Azure VM 내에서 여러 SAP 대화 상자 인스턴스가 실행되는 3계층 구성은 다음과 같습니다.

![Azure VM 내에서 여러 SAP 대화 상자 인스턴스가 실행되는 3계층 구성을 보여주는 다이어그램](./media/sap-planning-supported-configurations/multiple-dialog-instances.png)

간단한 설명을 위해 SAP 애플리케이션 계층에서 SAP Central Services와 SAP 대화 상자 인스턴스를 구분하지 않았습니다. 이 간단한 3계층 구성에서는 SAP Central Services에 대한 고가용성 보호가 없습니다. 프로덕션 시스템의 경우 SAP Central Services를 보호되지 않은 상태로 내버려 두는 것은 좋지 않습니다. SAP Central Instances와 관련된 다중 SID 구성 및 이러한 다중 SID 구성의 고가용성에 대한 자세한 내용은 이 문서의 뒷부분에 나오는 섹션을 참조하세요.

## <a name="high-availability-protection-for-the-sap-dbms-layer"></a>SAP DBMS 계층의 고가용성 보호
SAP 프로덕션 시스템을 배포하려는 경우 고가용성 구성의 상시 대기 유형을 고려해야 합니다. 특히 전체 성능 및 확장성을 얻으려면 데이터를 메모리에 로드해야 하는 SAP HANA의 경우 Azure 서비스 복구는 고가용성에 적합한 방법이 아닙니다. 

일반적으로 Microsoft는 docs.microsoft.com의 SAP 워크로드 섹션에 설명된 고가용성 구성 및 소프트웨어 패키지만 지원합니다. SAP 노트 [#1928533](https://launchpad.support.sap.com/#/notes/1928533)에서도 동일한 설명을 읽을 수 있습니다. Microsoft는 SAP 워크로드와 함께 Microsoft에서 문서화하지 않은 다른 고가용성 타사 소프트웨어 프레임워크를 지원하지 않습니다. 이 경우 고가용성 프레임워크의 타사 공급업체는 고객이 지원 프로세스에 참여해야 하는 고가용성 구성의 지원 당사자입니다. 예외는 이 문서에서 언급할 예정입니다. 

일반적으로 Microsoft는 Azure VM 또는 HANA 대규모 인스턴스 단위에서 제한된 고가용성 구성 세트를 지원합니다. HANA 대규모 인스턴스에서 지원하는 시나리오는 [HANA 대규모 인스턴스의 지원되는 시나리오](./hana-supported-scenario.md) 문서를 참조하세요.

Azure VM의 경우 다음과 같은 고가용성 구성이 DBMS 수준에서 지원됩니다.

- SUSE 및 Red Hat의 Linux Pacemaker을 기반으로 하는 SAP HANA 시스템 복제. 자세한 내용은 아래 문서를 참조하세요.
    - [SUSE Linux Enterprise Server의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability.md)
    - [Red Hat Enterprise Linux의 Azure VM에 있는 SAP HANA의 고가용성](./sap-hana-high-availability-rhel.md)
- SUSE 및 Red Hat에서 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)를 사용하는 SAP HANA 스케일 아웃 n+m 구성. 자세한 설명은 아래 문서에 나와 있습니다.
    - [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-suse.md)
    - [Red Hat Enterprise Linux에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- Windows 스케일 아웃 파일 서비스를 기반으로 하는 SQL Server 장애 조치(failover) 클러스터. 하지만 프로덕션 시스템에 대한 권장 사항은 클러스터링 대신 SQL Server Always On를 사용하는 것입니다. SQL Server Always On은 별도의 스토리지를 사용하여 보다 좋은 가용성을 제공합니다. 자세한 내용은 아래 문서에 설명되어 있습니다. 
    - [Azure 가상 머신에 SQL Server 장애 조치(Failover) 클러스터 인스턴스 구성](../../../azure-sql/virtual-machines/windows/failover-cluster-instance-storage-spaces-direct-manually-configure.md)
- SQL Server Always On은 Azure의 SQL Server에 대한 Windows 운영 체제에서 지원됩니다. Azure의 프로덕션 SQL Server 인스턴스에 대한 기본 권장 사항입니다. 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [Azure Virtual Machines의 SQL Server Always On 가용성 그룹 소개](../../../azure-sql/virtual-machines/windows/availability-group-overview.md)
    - [다른 하위 지역의 Azure Virtual Machines에서 Always On 가용성 그룹 구성](../../../azure-sql/virtual-machines/windows/availability-group-manually-configure-multiple-regions.md)
    - [Azure에서 Always On 가용성 그룹에 대한 부하 분산 장치 구성](../../../azure-sql/virtual-machines/windows/availability-group-load-balancer-portal-configure.md)
- Windows 및 Oracle Linux에 대한 Oracle Data Guard. Oracle Linux에 대한 자세한 내용은 다음 문서에서 찾을 수 있습니다.
    - [Azure Linux 가상 머신에서 Oracle Data Guard 구현](../oracle/configure-oracle-dataguard.md)
- SUSE 및 RHEL의 IBM Db2 HADR. Pacemaker를 사용하는 SUSE 및 RHEL에 대한 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [Pacemaker를 사용하는 SUSE Linux Enterprise Server의 Azure VM에 배포된 IBM Db2 LUW의 고가용성](./dbms-guide-ha-ibm.md)
    - [Red Hat Enterprise Linux Server의 Azure VM에서 IBM DB2 LUW의 고가용성](./high-availability-guide-rhel-ibm-db2-luw.md)
- SAP ASE 및 SAP maxDB 구성. 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [SAP 워크로드에 대한 SAP ASE Azure Virtual Machines DBMS 배포](./dbms_guide_sapase.md)
    - [Azure VM에서 SAP MaxDB, liveCache 및 Content Server 배포](./dbms_guide_maxdb.md)
- HANA 대규모 인스턴스 고가용성 시나리오는 아래 문서에 자세히 설명되어 있습니다.
    - [HANA 대규모 인스턴스를 지원하는 시나리오 - 고가용성을 위해 STONITH를 사용하는 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability)
    - [HANA 대규모 인스턴스를 지원하는 시나리오 - 호스트 자동 장애 조치(failover)(1+1)](./hana-supported-scenario.md#host-auto-failover-11)

> [!IMPORTANT]
> 위에서 설명한 어떤 시나리오도 VM 하나에 여러 DBMS 인스턴스를 구성할 수 없습니다. 즉, 각 사례에서 VM당 하나의 데이터베이스 인스턴스만 배포하고 설명된 고가용성 방법으로 보호할 수 있습니다. 여러 DBMS 인스턴스를 동일한 Windows 또는 Pacemaker 장애 조치(failover) 클러스터에서 보호하는 기능은 현재 지원되지 **않습니다**. 또한 Oracle Data Guard는 VM당 단일 인스턴스 배포 사례만 지원합니다. 

하나의 DBMS 인스턴스에 여러 데이터베이스를 호스트할 수 있는 다양한 데이터베이스 시스템이 있습니다. SAP HANA의 경우처럼 여러 데이터베이스를 MDC(다중 데이터베이스 컨테이너)에 호스트할 수 있습니다. 다중 데이터베이스 구성이 단일 장애 조치(failover) 클러스터 리소스 내에서 작동하는 경우 이러한 구성이 지원됩니다. 여러 클러스터 리소스가 필요한 구성은 지원되지 않습니다. 다중 SQL Server 가용성 그룹을 정의하려는 구성의 경우에는 하나의 SQL Server 인스턴스를 사용합니다.


![DBMS HA 구성](./media/sap-planning-supported-configurations/database-high-availability-configuration.png)

DBMS 및/또는 운영 체제에 따라 Azure Load Balancer와 같은 구성 요소가 솔루션 아키텍처에 필요할 수도 있고 필요하지 않을 수도 있습니다. 

특히 maxDB의 경우 스토리지 구성이 달라야 합니다. maxDB에서 고가용성 구성을 구현하려면 데이터 및 로그 파일이 공유 스토리지에 있어야 합니다. maxDB의 경우에만 공유 스토리지가 고가용성을 지원합니다. 그 외의 DBMS에서는 노드당 스토리지 스택이 유일하게 지원되는 디스크 구성입니다.

다른 고가용성 프레임워크가 있으며 Microsoft Azure에서도 실행되는 것으로 알려져 있습니다. 그러나 Microsoft에서 해당 프레임워크를 테스트하지 않았습니다. 이러한 프레임워크를 사용하여 고가용성 구성을 빌드하려면 해당 소프트웨어 공급자와 함께 다음을 수행해야 합니다.

- 배포 아키텍처 개발
- 아키텍처 배포
- 아키텍처 지원

> [!IMPORTANT]
> Microsoft Azure Marketplace는 Azure 네이티브 스토리지 기반의 스토리지 솔루션을 제공하는 다양한 소프트 어플라이언스를 제공합니다. 이러한 소프트 어플라이언스를 사용하여 NFS 공유를 만들 수 있으며, 이론적으로는 대기 노드가 필요한 SAP HANA 스케일 아웃 배포에도 사용할 수 있습니다. 다양한 이유로 이러한 스토리지 소프트 어플라이언스 중 어떤 것도 Azure에 배포되는 Microsoft 및 SAP의 DBMS 솔루션을 지원하지 않습니다. SMB 공유에 DBMS를 배포하는 기능은 현재 지원되지 않습니다. NFS 공유에 DBMS를 배포하는 기능은 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)의 NFS 4.1 공유로 제한됩니다.


## <a name="high-availability-for-sap-central-service"></a>SAP Central Services의 고가용성
SAP Central Services는 SAP 구성의 두 번째 단일 실패 지점입니다. 따라서 이러한 Central Services 프로세스도 보호해야 합니다. SAP 워크로드를 지원하며 설명서를 제공하는 제품은 다음과 같습니다.

- sapmnt 및 전역 전송 디렉터리에 Windows 스케일 아웃 파일 서비스를 사용하는 windows 장애 조치(failover) 클러스터 서버. 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [Azure에서 파일 공유를 사용하여 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스 클러스터링](./sap-high-availability-guide-wsfc-file-share.md)
    - [SAP ASCS/SCS 인스턴스에 대해 Windows 장애 조치(Failover) 클러스터 및 파일 공유를 사용하여 SAP 고가용성을 위한 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-file-share.md)
- sapmnt 및 전역 전송 디렉터리에 대한 [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 기반 SMB 공유를 사용하는 Windows 장애 조치(failover) 클러스터 서버. 자세한 설명은 아래 문서에 나와 있습니다.
    - [SAP 애플리케이션용 Azure NetApp Files(SMB)를 사용하는 Windows의 Azure VM에서 SAP NetWeaver에 대한 고가용성](./high-availability-guide-windows-netapp-files-smb.md)
- SIOS `Datakeeper` 기반의 Windows 장애 조치(failover) 클러스터 서버. Microsoft에서 문서를 제공하지만, 이 솔루션을 사용할 때 SIOS 지원과 협력하려면 SIOS와의 지원 관계가 필요합니다. 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [Azure에서 클러스터 공유 디스크를 사용하여 SAP ASCS/SCS 인스턴스를 Windows 장애 조치(Failover) 클러스터에 클러스터링](./sap-high-availability-guide-wsfc-shared-disk.md)
    - [Windows 장애 조치(Failover) 클러스터 및 공유 디스크를 사용하여 SAP ASCS/SCS를 위한 SAP HA용 Azure 인프라 준비](./sap-high-availability-infrastructure-wsfc-shared-disk.md)
- 파일 복제를 위한 두 개의 SUSE VM과 `drdb`를 사용하여 고가용성 NFS 공유를 만드는 SUSE 운영 체제의 Pacemaker. 자세한 내용은 아래 문서에 나와 있습니다.
    - [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse.md)
    - [SUSE Linux Enterprise Server의 Azure VM에 있는 NFS의 고가용성](./high-availability-guide-suse-nfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)에서 제공하는 NFS 공유를 사용하는 Pacemaker SUSE 운영 체제. 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [SAP 애플리케이션용 Azure NetApp Files를 사용하여 SUSE Linux Enterprise Server에서 Azure VM의 SAP NetWeaver 고가용성 실현](./high-availability-guide-suse-netapp-files.md)
- `glusterfs` 클러스터에 호스트되는 NFS 공유를 사용하는 Red Hat 운영 체제의 Pacemaker. 자세한 내용은 아래 문서에서 찾을 수 있습니다.
    - [Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](./high-availability-guide-rhel.md)
    - [SAP NetWeaver용 Red Hat Enterprise Linux의 Azure VM에 있는 `GlusterFS`](./high-availability-guide-rhel-glusterfs.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)에 호스트되는 NFS 공유를 사용하는 Red Hat 운영 체제의 Pacemaker. 자세한 내용은 아래 문서에 설명되어 있습니다.
    - [SAP 애플리케이션용 Azure NetApp Files를 사용하는 Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](./high-availability-guide-rhel-netapp-files.md)

나열된 솔루션에서 `Datakeeper` 제품을 지원하고 문제 발생 시 SIOS와 직접 협력하려면 SIOS와의 지원 관계가 필요합니다. Windows, Red Hat 및/또는 SUSE OS 라이선스 취득 방식에 따라 나열된 고가용성 구성을 완전히 지원하려면 OS 공급자와 지원 계약을 체결해야 할 수도 있습니다.

구성을 다음과 같이 표시할 수도 있습니다.

![DBMS 및 ASCS HA 구성](./media/sap-planning-supported-configurations/high-available-3-tier-configuration.png)

그림의 오른쪽에는 고가용성 SAP Central Services가 표시됩니다. 문제 발생 시 장애 조치(failover)가 가능한 장애 조치(failover) 클러스터 프레임워크로 SAP Central Services를 보호해야 할 뿐 아니라, 단일 VM의 존재 여부와 관계 없이 sapmnt 및 전역 전송 디렉터리를 사용할 수 있도록 고가용성 NFS나 SMB 공유 또는 Windows 공유 디스크가 필요합니다. Windows 장애 조치(failover) 클러스터 서버나 Pacemaker 같은 일부 솔루션에는 트래픽을 정상 노드로 전달하거나 리디렉션하는 Azure 부하 분산 장치가 필요합니다.

표시된 목록에 Oracle Linux 운영 체제에 대한 언급은 없습니다. Oracle Linux는 Pacemaker를 클러스터 프레임워크로 지원하지 않습니다. Oracle Linux에 SAP 시스템을 배포하려 하고 Oracle Linux에 사용할 고가용성 프레임워크가 필요한 경우 타사 공급업체와 협력해야 합니다. 공급업체 중 하나는 Azure의 SAP에서 지원되는 Protection Suite for Linux를 제공하는 SIOS입니다. 자세한 내용은 SAP 노트 [#1662610 - SIOS Protection Suite for Linux 지원 세부 정보](https://launchpad.support.sap.com/#/notes/1662610)를 참조하세요.



### <a name="supported-storage-with-the-sap-central-services-scenarios-listed-above"></a>위에 나열된 SAP Central Services 시나리오를 지원하는 스토리지
Azure Storage 유형 중 일부만 고가용성 NFS 또는 SMB 공유를 제공하기 때문에 SAP Central Services 클러스터 시나리오에 사용할 수 있는 지원되는 스토리지 유형 목록은 다음과 같습니다.

- Windows 스케일 아웃 파일 서버를 사용하는 Windows 장애 조치(failover) 클러스터 서버는 Azure NetApp Files를 제외한 모든 네이티브 Azure 스토리지 유형에 배포할 수 있습니다. 그러나 처리량 및 IOPS의 서비스 수준 계약이 우수한 Premium Storage를 사용하는 것이 좋습니다.
- Azure NetApp Files에서 SMB를 사용하는 Windows 장애 조치(failover) 클러스터 서버는 Azure NetApp Files에서 지원됩니다. Azure 파일 서비스의 SMB 공유는 현재 지원되지 **않습니다**.
- SIOS `Datakeeper` 기반의 Windows 공유 디스크를 사용하는 Windows 장애 조치(failover) 클러스터 서버는 Azure NetApp Files를 제외한 모든 네이티브 Azure 스토리지 유형에 배포할 수 있습니다. 그러나 처리량 및 IOPS의 서비스 수준 계약이 우수한 Premium Storage를 사용하는 것이 좋습니다.
- Azure NetApp Files에서 NFS 공유를 사용하는 SUSE 또는 Red Hat Pacemaker는 Azure NetApp Files에서 지원됩니다. 
- 두 VM 간에 `drdb` 구성을 사용하는 SUSE Pacemake는 Azure NetApp Files를 제외한 네이티브 Azure 스토리지 유형을 사용하여 지원됩니다. 그러나 처리량 및 IOPS의 서비스 수준 계약이 우수한 Premium Storage를 사용하는 것이 좋습니다.
- `glusterfs`를 사용하여 NFS 공유를 제공하는 Red Hat Pacemaker는 Azure NetApp Files를 제외한 네이티브 Azure 스토리지 유형을 사용하여 지원됩니다. 그러나 처리량 및 IOPS의 서비스 수준 계약이 우수한 Premium Storage를 사용하는 것이 좋습니다.

> [!IMPORTANT]
> Microsoft Azure Marketplace는 Azure 네이티브 스토리지 기반의 스토리지 솔루션을 제공하는 다양한 소프트 어플라이언스를 제공합니다. 이러한 소프트 어플라이언스는 NFS 또는 SMB 공유를 만드는 데 사용할 수 있으며, 이론적으로는 장애 조치(failover) 클러스터 SAP Central Services에서도 사용할 수 있습니다. 이러한 솔루션은 Microsoft의 SAP 워크로드를 직접 지원하지 않습니다. 이러한 솔루션을 사용하여 NFS 또는 SMB 공유를 만들기로 결정하는 경우 스토리지 소프트 어플라이언스의 소프트웨어를 소유한 타사가 SAP Central Service 구성에 대한 지원을 제공해야 합니다.


## <a name="multi-sid-sap-central-services-failover-clusters"></a>다중 SID SAP Central Services 장애 조치(failover) 클러스터
대규모 SAP 환경에 필요한 VM 수를 줄일 수 있도록 SAP는 여러 SAP 시스템의 SAP 중앙 서비스 인스턴스를 장애 조치(failover) 클러스터 구성으로 실행하는 것을 허용합니다. NetWeaver 또는 S/4HANA 프로덕션 시스템이 30개 이상 있다고 가정해 봅시다. 다중 SID 클러스터링이 없으면 이 구성은 30개 이상의 Windows 또는 Pacemaker 장애 조치(failover) 클러스터에서 60개 이상의 VM이 필요합니다. 뿐만 아니라 DBMS 장애 조치(failover) 클러스터도 필요합니다. 장애 조치(failover) 클러스터 구성의 두 노드에 여러 SAP 중앙 서비스를 배포하면 VM 수를 크게 줄일 수 있습니다. 그러나 단일 2노드 클러스터 구성에 SAP 중앙 서비스 인스턴스를 여러 개 배포해도 몇 가지 단점이 있습니다. 클러스터 구성의 단일 VM과 관련된 문제가 여러 SAP 시스템에 적용됩니다. 여러 프로덕션 SAP 시스템이 영향을 받기 때문에 클러스터 구성에서 실행되는 게스트 OS의 유지 관리에 더 많은 조정이 필요합니다. SAP LaMa와 같은 도구는 시스템 복제 프로세스에서 다중 SID 클러스터링을 지원하지 않습니다.

Azure에서는 ENSA1 및 ENSA2를 사용하는 Windows 운영 체제에 대해 다중 SID 클러스터 구성이 지원됩니다. 이전 Enqueue Replication Service 아키텍처(ENSA1)를 단일 다중 SID 클러스터의 새 아키텍처(ENSA2)와 결합하지 않는 것이 좋습니다. 이러한 아키텍처에 대한 자세한 내용은 아래 문서에 설명되어 있습니다.

- [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md) 
- [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-file-share.md) 

SUSE의 경우 Pacemaker 기반의 다중 SID 클러스터도 지원됩니다. 현재는 다음에 대한 구성이 지원됩니다.

- 최대 5개의 SAP ASCS/SCS 인스턴스
- 이전 Enqueue Replication Service 아키텍처(ENSA1)
- 2노드 Pacemaker 클러스터 구성

구성은 [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 다중 SID 고가용성 가이드](./high-availability-guide-suse-multi-sid.md)에 설명되어 있습니다.

Enqueue Replication 서버를 구조적으로 사용하는 다중 SID 클러스터는 다음과 같습니다.

![Enqueue Replication 서버를 사용하는 다중 SID 클러스터를 보여주는 다이어그램](./media/sap-planning-supported-configurations/high-available-multi-system-configuration.png)


## <a name="sap-hana-scale-out-scenarios"></a>SAP HANA 스케일 아웃 시나리오
SAP HANA 스케일 아웃 시나리오는 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나열된 일부 HANA 인증 Azure VM에서 지원됩니다. '클러스터링' 열에 '예'로 표시된 모든 VM은 OLAP 또는 S/4HANA 스케일 아웃에 사용할 수 있습니다. 대기 노드가 없는 구성은 다음과 같은 Azure Storage 유형에서 지원됩니다. 

- Azure Premium Storage - /hana/log 볼륨에 대한 Azure 쓰기 가속기 포함
- [Ultra Disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

대기 노드가 있는 OLAP 또는 S/4HANA에 대한 SAP HANA 스케일 아웃 구성은 Azure NetApp Files에 호스트되는 NFS 공유에서만 지원됩니다.

대기 노드가 있는 스토리지 구성 또는 대기 노드가 없는 스토리지 구성에 대한 정확한 정보는 다음 문서를 참조하세요.

- [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 
- [SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-suse.md)
- [Red Hat Enterprise Linux에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포](./sap-hana-scale-out-standby-netapp-files-rhel.md)
- [SAP 지원 노트 #2080991](https://launchpad.support.sap.com/#/notes/2080991)

HANA 대규모 인스턴스 지원 HANA 스케일 아웃 구성에는 다음 설명서가 적용됩니다.

- [대기 노드가 있는 HANA 대규모 인스턴스 스케일 아웃을 지원하는 시나리오](./hana-supported-scenario.md#scale-out-with-standby)
- [대기 노드가 없는 HANA 대규모 인스턴스 스케일 아웃을 지원하는 시나리오](./hana-supported-scenario.md#scale-out-without-standby)


## <a name="disaster-recovery-scenario"></a>재해 복구 시나리오
여러 가지 재해 복구 시나리오가 지원됩니다. 재해 아키텍처란 전체 Azure 지역이 사용할 수 없게 되는 상황을 대비하는 아키텍처라고 정의할 수 있습니다. 즉, 재해 복구 대상은 SAP 환경을 실행하기 위한 대상으로써 다른 Azure 지역이어야 합니다. 방법 및 구성은 DBMS 계층과 비 DBMS 계층으로 분류됩니다. 

### <a name="dbms-layer"></a>DBMS 계층
DBMS 계층의 경우 Always On, Oracle Data Guard, Db2 HADR, SAP ASE Always On, HANA 시스템 복제 등의 DBMS 네이티브 복제 메커니즘을 사용하는 구성을 지원합니다. 이 경우 단일 Azure 지역에 배포되는 일반적인 고가용성 시나리오처럼 동기식이 아닌 비동기 복제 스트림이 필요합니다. 지원되는 DBMS 재해 복구 구성의 일반적인 예는 [Azure 지역 간 SAP HANA 가용성](./sap-hana-availability-across-regions.md#combine-availability-within-one-region-and-across-regions) 문서에 설명되어 있습니다. 이 섹션의 두 번째 그림에는 HANA를 예제로 사용하는 시나리오가 설명되어 있습니다. SAP 애플리케이션을 지원하는 모든 주 데이터베이스는 이러한 시나리오에 배포할 수 있습니다.

VM에서 전체 워크로드 트래픽을 발생시키지 않으므로 더 작은 VM을 재해 복구 지역의 대상 인스턴스로 사용할 수 있습니다. 이렇게 하려면 다음 사항을 염두에 두어야 합니다.

- 작은 VM 유형을 사용하면 작은 VM보다 많은 디스크를 연결할 수 없습니다.
- 작은 VM은 네트워크 및 스토리지 처리량이 적습니다.
- 여러 VM이 하나의 Azure 가용성 집합에 수집되거나 VM의 M 시리즈 제품군과 Mv2 제품군 사이에서 크기를 조정해야 하는 경우 VM 제품군의 크기를 조정하면 문제가 발생할 수 있습니다.
- 데이터베이스 인스턴스가 최소의 지연 시간으로 변경 스트림을 수신할 수 있는 CPU 및 메모리 사용량과 이러한 변경 내용을 최소의 지연 시간으로 데이터에 적용할 수 있는 충분한 CPU 및 메모리 리소스를 고려해야 합니다.  

다양한 VM 크기의 제한 사항에 대한 자세한 내용은 [VM 크기](../../sizes.md) 페이지에서 찾을 수 있습니다.

DR 대상을 배포하는 데 지원되는 또 다른 방법은 비프로덕션 SAP 인스턴스의 비프로덕션 DBMS 인스턴스를 실행하는 VM에 두 번째 DBMS 인스턴스를 설치하는 것입니다. DR 시나리오에서 주 인스턴스로 작동해야 하는 특정 대상 인스턴스에 필요한 메모리, CPU 리소스, 네트워크 대역폭 및 스토리지 대역폭을 알아내야 하기 때문에 약간 어려울 수 있습니다. 특히 HANA에서는 데이터가 DR 대상 인스턴스에 미리 로드되지 않도록 공유 호스트에서 DR 대상으로 작동하는 인스턴스를 구성하는 것이 좋습니다.

HANA 대규모 인스턴스 DR 시나리오의 경우 다음 문서를 확인하세요.

- [스토리지 복제를 사용하는 DR이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication)
- [스토리지 복제를 사용하는 DR(다목적)이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [스토리지 복제를 사용하는 DR(다목적)이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication)
- [스토리지 복제가 포함된 HSR 및 DR을 사용한 고가용성](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication)
- [스토리지 복제를 사용하는 DR이 있는 스케일 아웃](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication)
- [HSR를 사용하는 DR이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-hsr)
- [단일 노드 HSR에서 DR로(비용 최적화)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized)
- [HSR을 사용한 고가용성 및 재해 복구](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr)
- [HSR을 사용한 고가용성 및 재해 복구(비용 최적화)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized)
- [HSR를 사용하는 DR이 있는 스케일 아웃](./hana-supported-scenario.md#scale-out-with-dr-using-hsr)

> [!NOTE]
> SAP 워크로드에 배포된 DBMS에서는 [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) 사용 테스트가 진행되지 않았습니다. 따라서 현재는 SAP 시스템의 DBMS 계층을 지원하지 않습니다. 나열되지 않은 Microsoft 및 SAP의 다른 복제 방법은 지원되지 않습니다. 타사 소프트웨어를 사용하여 서로 다른 Azure 지역 간에 SAP 시스템의 DBMS 계층을 복제하는 방법은 해당 소프트웨어 공급업체에서 지원해야 하며 Microsoft 및 SAP 지원 채널에서는 지원되지 않습니다. 
 
## <a name="non-dbms-layer"></a>비 DBMS 계층
SAP 애플리케이션 계층과 필요한 최종 공유 또는 스토리지 위치에 대해 고객이 주로 활용하는 두 가지 주요 시나리오는 다음과 같습니다.

- 두 번째 Azure 지역의 재해 복구 대상은 프로덕션 또는 비프로덕션 용도로 사용되지 않습니다. 이 시나리오에서 재해 복구 대상으로 작동하는 VM은 배포되지 않으며 프로덕션 SAP 애플리케이션 계층의 이미지 및 변경 내용은 재해 복구 지역에 복제됩니다. 이러한 작업을 수행할 수 있는 기능을 [Azure Site Recovery](../../../site-recovery/azure-to-azure-move-overview.md)라고 합니다. Azure Site Recovery는 다음과 같은 Azure 간 복제 시나리오를 지원합니다. 
- 재해 복구 대상은 실제로 비프로덕션 시스템에서 사용 중인 VM입니다. 전체 SAP 환경은 서로 다른 두 Azure 지역에 분산되고, 프로덕션 시스템은 일반적으로 한 지역에 있고 비프로덕션 시스템은 또 다른 지역에 있습니다. 고객이 배포한 수많은 솔루션을 보면, 고객은 프로덕션 시스템에 해당하는 비프로덕션 시스템을 보유하고 있습니다. 고객의 프로덕션 애플리케이션 인스턴스가 애플리케이션 계층 비프로덕션 시스템에 미리 설치되어 있습니다. 장애 조치(failover)의 경우 비프로덕션 인스턴스가 종료되고, 프로덕션 VM의 가상 이름이 비프로덕션 VM으로 이동하고(DNS에 새 IP 주소를 할당한 후), 미리 설치된 프로덕션 인스턴스가 시작됩니다.

### <a name="sap-central-services-clusters"></a>SAP Central Services 클러스터
공유 디스크(Windows), SMB 공유(Windows) 또는 NFS 공유를 사용하는 SAP Central Services 클러스터는 복제하기가 약간 어렵습니다. Windows 쪽에서 사용 가능한 솔루션은 Windows Storage 복제입니다. Linux에서 구현 가능한 솔루션은 rsync입니다.



## <a name="non-supported-scenario"></a>지원되지 않는 시나리오
Azure 아키텍처에서 SAP 워크로드를 지원하지 않는 시나리오 목록이 있습니다. **지원되지 않음** 은 SAP와 Microsoft가 이러한 구성을 지원할 수 없으며, 이러한 아키텍처를 설정하려면 소프트웨어를 제공한 최종 관련 타사에 문의해야 합니다. 다음과 같은 두 가지 범주가 있습니다.

- 스토리지 소프트 어플라이언스: Azure Marketplace에 다양한 스토리지 소프트 어플라이언스가 제공됩니다. 일부 공급업체는 SAP 소프트웨어와 관련된 Azure에서 이러한 스토리지 소프트 어플라이언스를 사용하는 방법에 대한 자체 설명서를 제공합니다. 이러한 스토리지 소프트 어플라이언스와 관련된 구성 또는 배포 지원은 해당 스토리지 소프트 어플라이언스 공급업체가 제공해야 합니다. [SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553)에도 이 내용이 설명되어 있습니다.
- 고가용성 프레임워크: Azure에서 SAP 워크로드를 지원하는 고가용성 프레임워크는 Pacemaker 및 Windows Server 장애 조치(failover) 클러스터밖에 없습니다. 앞서 언급했듯이 SIOS의 `Datakeeper` 솔루션은 Microsoft에서 설명하고 문서화했습니다. 그러나 SIOS `Datakeeper`의 구성 요소는 해당 구성 요소를 제공하는 공급업체인 SIOS에서 지원해야 합니다. SAP 역시 다양한 SAP 노트에 다른 인증 고가용성 프레임워크를 나열해 두었습니다. 그 중 일부는 Azure용 제품을 제공하는 타사 공급업체의 인증을 받았습니다. 그러나 해당 제품을 사용하는 구성의 지원은 제품 공급업체가 제공해야 합니다. 공급업체마다 SAP 지원 프로세스와 통합하는 방법이 다릅니다. Azure에 배포된 SAP 구성에서 제품을 사용하기로 결정하기 전에 특정 공급업체에 가장 적합한 지원 프로세스를 명확하게 확인해야 합니다.
- 데이터베이스 파일이 공유 디스크에 상주하는 공유 디스크 클러스터는 maxDB를 제외하고 지원되지 않습니다. 그 외의 데이터베이스에 지원되는 솔루션은 SMB 또는 NFS 공유나 공유 디스크 대신 별도의 스토리지 위치를 사용하여 고가용성 시나리오를 구성하는 것입니다.

지원되지 않는 다른 시나리오는 다음과 같습니다.

- NetWeaver, S/4HANA 및 `Hybris`에서 예를 든 것처럼 SAP 애플리케이션 계층과 SAP 공통 아키텍처의 SAP DBMS 계층 간에 더 큰 네트워크 대기 시간이 발생하는 배포 시나리오. 다음 내용이 포함됩니다.
    - 계층 중 하나를 온-프레미스에 배포하고 다른 계층을 Azure에 배포
    - 시스템의 SAP 애플리케이션 계층을 DBMS 계층이 아닌 다른 Azure 지역에 배포
    - 한 계층을 Azure와 공동 배치되는 데이터 센터에 배포하고 다른 계층을 Azure에 배포(이러한 아키텍처 패턴을 Azure 기본 서비스에서 제공하는 경우는 제외)
    - SAP 애플리케이션 계층과 DBMS 계층 사이에 네트워크 가상 어플라이언스 배포
    - SAP DBMS 계층 또는 SAP 전역 전송 디렉터리에 대한 Azure 데이터 센터에 공동 배치되는 데이터 센터에 호스팅되는 스토리지 활용
    - 서로 다른 두 클라우드 공급업체를 사용하여 두 계층을 배포. 예: DBMS 계층은 Oracle 클라우드 인프라에 배포하고 애플리케이션 계층은 Azure에 배포
- 다중 인스턴스 HANA Pacemaker 클러스터 구성
- Windows에서 지원되는 SAP 데이터베이스용 SOFS 또는 ANF의 SMB를 통해 공유 디스크를 사용하는 Windows 클러스터 구성. 이 방법 대신 특정 데이터베이스의 네이티브 고가용성 복제를 사용하고 별도의 스토리지 스택을 사용하는 것이 좋습니다.
- SAP HANA, Oracle Linux의 Oracle, Suse 및 Red Hat의 Db2를 제외하고 ANF 상단의 NFS 공유에 있는 데이터베이스 파일을 사용하여 Linux에서 지원되는 SAP 데이터베이스 배포
- Windows 및 Oracle Linux가 아닌 다른 게스트 OS에 Oracle DBMS 배포. [SAP 지원 노트 #2039619](https://launchpad.support.sap.com/#/notes/2039619)도 참조하세요.

Microsoft에서 테스트하지 않았으므로 경험이 없는 시나리오는 다음과 같습니다.

- DBMS 계층 VM을 복제하는 Azure Site Recovery. 따라서 잠재적 재해 복구 구성에는 데이터베이스 네이티브 비동기 복제 기능을 활용하는 것이 좋습니다.
 

## <a name="next-steps"></a>다음 단계
[SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md)에서 다음 단계를 읽어 보세요.




  
