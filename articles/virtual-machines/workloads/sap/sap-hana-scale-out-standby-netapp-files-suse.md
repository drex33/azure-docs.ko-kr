---
title: SLES에서 Azure NetApp Files를 사용하여 대기 상태로 SAP HANA 스케일 아웃 | Microsoft Docs
description: SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포하는 방법을 알아봅니다.
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 04/06/2021
ms.author: radeltch
ms.openlocfilehash: a4e3d2b2c6b7c56771ebfddd60bb5ec13ab78d28
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643634"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>SUSE Linux Enterprise Server에서 Azure NetApp Files를 사용하여 Azure VM에 대기 노드가 있는 SAP HANA 스케일 아웃 시스템 배포 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]:https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


이 문서에서는 공유 스토리지 볼륨에 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 사용하여 Azure VM(가상 머신)에서 대기 상태의 스케일 아웃 구성에 고가용성 SAP HANA 시스템을 배포하는 방법을 설명합니다.  

예제 구성, 설치 명령 등에서 HANA 인스턴스는 **03** 이고 HANA 시스템 ID는 **HN1** 입니다. 이 예제는 SAP 12 SP4용 SUSE Linux Enterprise Server 및 HANA 2.0 SP4를 기반으로 합니다. 

시작하기 전에 다음 SAP Note와 문서를 참조하세요.

* [Azure NetApp Files 설명서][anf-azure-doc] 
* SAP Note [1928533] 내용:  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]: Azure에서 SAP를 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917]: SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 있습니다.
* SAP Note [1944799]: SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP 지침이 있습니다.
* SAP Note [2178632]: Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보가 있습니다.
* SAP 노트 [2191498]: Azure에서 Linux에 필요한 SAP Host Agent 버전이 있습니다.
* SAP Note [2243692]: Azure에서 Linux의 SAP 라이선스에 대한 정보가 있습니다.
* SAP Note [1984787]: SUSE Linux Enterprise Server 12에 대한 일반 정보가 있습니다.
* SAP Note [1999351]: SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보가 있습니다.
* SAP Note [1900823]: SAP HANA 스토리지 요구 사항에 대한 정보가 있습니다.
* [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes): Linux에 필요한 모든 SAP Note가 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide]: NetWeaver 고가용성 및 SAP HANA 시스템 복제를 온-프레미스에 설치하는 데 필요한 모든 정보가 있습니다(일반 기준으로 사용. 훨씬 더 자세한 정보가 있음).
* [SUSE High Availability Extension 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]
* [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]
* [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md)

## <a name="overview"></a>개요

HANA 고가용성을 달성하는 한 가지 방법은 호스트 자동 장애 조치(failover)를 구성하는 것입니다. 호스트 자동 장애 조치를 구성하려면 하나 이상의 가상 머신을 HANA 시스템에 추가하여 대기 노드로 구성합니다. 활성 노드에 장애가 발생하면 대기 노드가 자동으로 인수합니다. Azure 가상 머신으로 제시된 구성에서는 [Azure NetApp Files에서 NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md)를 사용하여 자동 장애 조치를 수행합니다.  

> [!NOTE]
> 대기 노드는 모든 데이터베이스 볼륨에 액세스할 수 있어야 합니다. HANA 볼륨은 NFSv4 볼륨으로 탑재되어야 합니다. NFSv4 프로토콜의 향상된 파일 임대 기반 잠금 메커니즘은 `I/O` 펜싱에 사용됩니다. 

> [!IMPORTANT]
> 지원되는 구성을 빌드하려면 HANA 데이터 및 로그 볼륨을 NFSv4.1 볼륨으로 배포하고 NFSv4.1 프로토콜을 사용하여 탑재해야 합니다. 대기 노드가 있는 HANA 호스트 자동 장애 조치 구성은 NFSv3에서 지원되지 않습니다.

![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

SAP HANA 네트워크 권장 사항을 따르는 위의 다이어그램에는 3개의 서브넷이 하나의 Azure 가상 네트워크 내에 표시됩니다. 
* 클라이언트 통신용
* 스토리지 시스템과의 통신용
* 내부 HANA 노드 간 통신용

Azure NetApp 볼륨은 [Azure NetApp Files에 위임된](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md) 별도의 서브넷에 있습니다.  

이 예제 구성에서 서브넷은 다음과 같습니다.  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>Azure NetApp Files 인프라 설정 

Azure NetApp Files 인프라 설정을 진행하기 전에 [Azure NetApp Files 설명서][anf-azure-doc]를 숙지하세요. 

Azure NetApp Files는 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용할 수 있습니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공하는지 확인하세요.  

Azure 지역별 Azure NetApp Files 가용성에 대한 자세한 내용은 [Azure 지역별 Azure NetApp Files 가용성][anf-avail-matrix]을 참조하세요.  

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포  

다음 지침에서는 [Azure 가상 네트워크](../../../virtual-network/virtual-networks-overview.md)를 이미 배포했다고 가정합니다. Azure NetApp Files 리소스가 탑재될 Azure NetApp Files 리소스와 VM을 동일하거나 피어링된 Azure Virtual Network에 배포해야 합니다.  

1. [NetApp 계정 만들기](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)의 지침에 따라 선택한 Azure 지역에서 NetApp 계정을 만듭니다.  

2. [Azure NetApp Files 용량 풀 설정](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)의 지침에 따라 Azure NetApp Files 용량 풀을 설정합니다.  

   이 문서에 제시된 HANA 아키텍처는 *Ultra* 서비스 수준에서 단일 Azure NetApp Files 용량 풀을 사용합니다. Azure의 HANA 워크로드의 경우 Azure NetApp Files *Ultra* 또는 *Premium* [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)을 사용하는 것이 좋습니다.  

3. [Azure NetApp Files에 서브넷 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)의 지침에 따라 Azure NetApp Files에 서브넷을 위임합니다.  

4. [Azure NetApp Files에 대한 NFS 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)의 지침에 따라 Azure NetApp Files 볼륨을 배포합니다.  

   볼륨을 배포할 때 **NFSv4.1** 버전을 선택해야 합니다. 현재 NFSv 4.1에 대 한 액세스를 allowlist에 추가 해야 합니다. 지정된 Azure NetApp Files [서브넷](/rest/api/virtualnetwork/subnets)에 볼륨을 배포합니다. Azure NetApp 볼륨의 IP 주소는 자동으로 할당됩니다. 
   
   Azure NetApp Files 리소스와 Azure VM은 동일하거나 피어링된 Azure 가상 네트워크에 있어야 합니다. 예를 들어 **HN1**-data-mnt00001, **HN1**-log-mnt00001 등은 볼륨 이름이고 nfs://10.23.1.5/**HN1**-data-mnt00001, nfs://10.23.1.4/**HN1**-log-mnt00001 등은 Azure NetApp Files 볼륨의 파일 경로입니다.  

   * volume **HN1**-data-mnt00001(nfs://10.23.1.5/**HN1**-data-mnt00001)
   * volume **HN1**-data-mnt00002(nfs://10.23.1.6/**HN1**-data-mnt00002)
   * volume **HN1**-log-mnt00001(nfs://10.23.1.4/**HN1**-log-mnt00001)
   * volume **HN1**-log-mnt00002(nfs://10.23.1.6/**HN1**-log-mnt00002)
   * volume **HN1**-shared(nfs://10.23.1.4/**HN1**-shared)
   
   이 예제에서는 각 HANA 데이터 및 로그 볼륨에 대해 별도의 Azure NetApp Files 볼륨을 사용했습니다. 소규모 시스템이나 비프로덕션 시스템에 대한 구성 비용을 최적화하려면, 모든 데이터 탑재 및 모든 로그 탑재를 단일 볼륨에 배치할 수 있습니다.  

### <a name="important-considerations"></a>중요 고려 사항

SUSE 고가용성 아키텍처에서 SAP NetWeaver용 Azure NetApp Files를 만드는 경우 다음과 같은 중요 고려 사항에 유의해야 합니다.

- 최소 용량 풀은 4TiB(테비바이트)입니다.  
- 최소 볼륨 크기는 100GiB(기비바이트)입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재될 모든 가상 머신은 동일한 Azure 가상 네트워크에 있거나 동일한 지역의 [피어링된 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md)에 있어야 합니다.  
- 선택한 가상 네트워크에 Azure NetApp Files로 위임된 서브넷이 있어야 합니다.
- Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 할당량과 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨의 크기를 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족하는지 확인해야 합니다.  
- Azure NetApp Files [내보내기 정책](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)을 사용하면 허용된 클라이언트, 액세스 유형(읽기-쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능은 아직 영역을 인식하지 않습니다. 현재 이 기능은 Azure 지역의 일부 가용성 영역에만 배포되어 있습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.  
-  

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 크기와 서비스 수준의 함수입니다. 

Azure에서 SAP용 인프라를 설계할 때는 최소 처리량 특성으로 변환되는 SAP의 몇 가지 최소 스토리지 요구 사항을 알고 있어야 합니다.

- 1MB I/O 크기로 /hana/log에 대해 250MB/s(초당 MB) 읽기-쓰기가 가능하도록 설정합니다.  
- 16MB 및 64MB I/O 크기의 /hana/data에 대해 400MB/초 이상의 읽기 작업이 가능하도록 설정합니다.  
- 16MB 및 64MB I/O 크기의 /hana/data에 대해 최소 250MB/초 이상의 쓰기 작업이 가능하도록 설정합니다. 

볼륨 할당량 1TiB당 [Azure NetApp Files 처리량 한도](../../../azure-netapp-files/azure-netapp-files-service-levels.md)는 다음과 같습니다.
- Premium Storage 계층 - 64MiB/초  
- Ultra Storage 계층 - 128MiB/초  

데이터 및 로그에 대한 SAP 최소 처리량 요구 사항과 /hana/shared에 대한 지침을 충족하기 위해 권장되는 크기는 다음과 같습니다.

| 볼륨 | 크기 -<br>Premium Storage 계층 | 크기 -<br>Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- | --- |
| /hana/log/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3TiB | 3.2TiB | v4.1 |
| /hana/shared | 작업자 노드 4개당 최대(512GB, 1xRAM) | 작업자 노드 4개당 최대(512GB, 1xRAM) | v3 또는 v4.1 |

이 문서에 제시된 레이아웃에 대한 SAP HANA 구성에 Azure NetApp Files Ultra Storage 계층을 사용하면, 다음과 같습니다.

| 볼륨 | 크기 -<br>Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/mnt00001 | 2TiB | v4.1 |
| /hana/log/mnt00002 | 2TiB | v4.1 |
| /hana/data/mnt00001 | 3.2TiB | v4.1 |
| /hana/data/mnt00002 | 3.2TiB | v4.1 |
| /hana/shared | 2TiB | v3 또는 v4.1 |

> [!NOTE]
> 여기에 명시된 Azure NetApp Files 크기 조정 권장 사항은 SAP에서 인프라 공급자에 대해 권장하는 최소 요구 사항을 충족하는 것을 목표로 합니다. 실제 고객 배포 및 워크로드 시나리오에서는 이러한 크기가 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.  

> [!TIP]
> 볼륨을 분리(*unmount*)하거나, 가상 머신을 중지하거나, SAP HANA를 중지하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 이런 방식을 통해 애플리케이션의 예상 처리량 및 예기치 못한 처리량 요구 사항을 모두 유연하게 충족할 수 있습니다.

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>Azure Portal을 통한 Linux 가상 머신 배포

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. 그런 후 다음 단계를 수행합니다.
1. [Azure 가상 네트워크](../../../virtual-network/virtual-networks-overview.md)에 [Azure 가상 네트워크 서브넷](../../../virtual-network/virtual-network-manage-subnet.md)을 만듭니다. 
1. VM을 배포합니다. 
1. 네트워크 인터페이스를 추가로 만들고 네트워크 인터페이스를 해당 VM에 연결합니다.  

   각 가상 머신에는 세 개의 Azure 가상 네트워크 서브넷(`client`, `storage`, `hana`)에 해당하는 세 개의 네트워크 인터페이스가 있습니다. 

   자세한 내용은 [여러 네트워크 인터페이스 카드를 사용하여 Azure에서 Linux 가상 머신을 만들기](../../linux/multiple-nics.md)를 참조하세요.  

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. 짧은 대기 시간을 달성하려면 Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다. SAP HANA Azure NetApp Files를 사용하는 [새 SAP HANA 시스템을 온보딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)하는 경우에는 필요한 정보를 제출하세요. 
 
다음 지침에서는 리소스 그룹, Azure 가상 네트워크, 3개의 Azure 네트워크 서브넷(`client`, `storage`, `hana`)을 이미 만들었다고 가정합니다. VM을 배포할 때 클라이언트 서브넷을 선택합니다. 그래야 클라이언트 네트워크 인터페이스가 VM의 기본 인터페이스가 됩니다. 또한 스토리지 서브넷 게이트웨이를 통해 Azure NetApp Files 위임 서브넷에 대한 명시적 경로를 구성해야 합니다. 

> [!IMPORTANT]
> 선택하는 OS가 사용 중인 특정 VM 유형에서 SAP HANA용으로 인증된 SAP인지 반드시 확인하세요. SAP HANA 인증 VM 유형과 이러한 유형의 OS 릴리스 목록은 [SAP HANA 인증 IaaS 플랫폼](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 사이트에서 확인할 수 있습니다. 특정 유형의 SAP HANA 지원 OS 릴리스 전체 목록을 보려면 목록에 있는 VM 유형의 세부 정보를 클릭하세요.  

1. SAP HANA에 대한 가용성 집합을 만듭니다. 최대 업데이트 도메인을 설정해야 합니다.  

2. 다음 단계를 수행하여 세 개의 가상 머신(**hanadb1**, **hanadb2**, **hanadb3**)을 만듭니다.  

   a. Azure 갤러리에서 SLES4SAP 이미지를 사용합니다(SAP HANA에서 지원됨). 이 예제에서는 SLES4SAP 12 SP4 이미지를 사용했습니다.  

   b. SAP HANA용으로 이전에 만든 가용성 집합을 선택합니다.  

   c. 클라이언트 Azure 가상 네트워크 서브넷을 선택합니다. [가속화된 네트워크](../../../virtual-network/create-vm-accelerated-networking-cli.md)를 선택합니다.  

   가상 머신을 배포하면 네트워크 인터페이스 이름이 자동으로 생성됩니다. 편의를 위해 이 지침에서는 자동으로 생성된 네트워크 인터페이스를 참조합니다. 이것은 클라이언트 Azure 가상 네트워크 서브넷에 **hanadb1-client**, **hanadb2-client**, **hanadb3-client** 으로 연결됩니다. 

3. 세 개의 네트워크 인터페이스를 각 가상 머신당 하나씩 `storage` 가상 네트워크 서브넷용으로 만듭니다(이 예제의 경우 **hanadb1-storage**, **hanadb2-storage**, **hanadb3-storage**).  

4. 세 개의 네트워크 인터페이스를 각 가상 머신당 하나씩 `hana` 가상 네트워크 서브넷용으로 만듭니다(이 예제의 경우 **hanadb1-hana**, **hanadb2-hana**, **hanadb3-hana**).  

5. 다음 단계를 수행하여 새로 만든 가상 네트워크 인터페이스를 해당 가상 머신에 연결합니다.  

    a. [Azure Portal](https://portal.azure.com/#home)에서 가상 머신으로 이동합니다.  

    b. 왼쪽 창에서 **Virtual Machines** 를 선택합니다. 가상 머신 이름(예: **hanadb1**)을 필터링한 다음, 가상 머신을 선택합니다.  

    c. **개요** 창에서 **중지** 를 선택하여 가상 머신의 할당을 취소합니다.  

    d. **네트워킹** 을 선택하고 네트워크 인터페이스를 연결합니다. **네트워크 인터페이스 연결** 드롭다운 목록에서 `storage` 및 `hana` 서브넷용으로 이미 생성된 네트워크 인터페이스를 선택합니다.  
    
    e. **저장** 을 선택합니다. 
 
    f. 나머지 가상 머신에 대해 b~e 단계를 반복합니다(이 예제의 경우 **hanadb2** 및 **hanadb3**).
 
    g. 지금은 가상 머신을 중지 상태로 둡니다. 이제 새로 연결된 모든 네트워크 인터페이스에 대해 [가속화된 네트워킹](../../../virtual-network/create-vm-accelerated-networking-cli.md)을 사용하도록 설정합니다.  

6. 다음 단계를 수행하여 `storage` 및 `hana` 서브넷의 추가 네트워크 인터페이스에 가속화된 네트워킹을 사용하도록 설정합니다.  

    a. [Azure Portal](https://portal.azure.com/#home)에서 [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/)을 엽니다.  

    b. 다음 명령을 실행하여 `storage` 및 `hana` 서브넷에 연결된 추가 네트워크 인터페이스에 가속화된 네트워킹을 사용하도록 설정합니다.  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 다음 단계를 수행하여 가상 머신을 시작합니다.  

    a. 왼쪽 창에서 **Virtual Machines** 를 선택합니다. 가상 머신 이름(예: **hanadb1**)을 필터링한 다음, 선택합니다.  

    b. **개요** 창에서 **시작** 을 선택합니다.  

## <a name="operating-system-configuration-and-preparation"></a>운영 체제 구성 및 준비

다음 섹션의 지침에는 다음 중 하나가 접두사로 추가됩니다.
* **[A]** : 모든 노드에 적용 가능
* **[1]** : 노드 1에만 적용 가능
* **[2]** : 노드 2에만 적용 가능
* **[3]** : 노드 3에만 적용 가능

다음 단계를 수행하여 OS를 구성하고 준비합니다.

1. **[A]** 가상 머신에서 호스트 파일을 유지 관리합니다. 모든 서브넷에 대한 항목을 포함합니다. 이 예제의 `/etc/hosts`에 추가된 항목은 다음과 같습니다.  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** 의도치 않은 호스트 이름 변경을 방지하기 위해 스토리지 용 네트워크 인터페이스에 대한 DHCP 및 클라우드 구성 설정을 변경합니다.  

    다음 지침에서는 스토리지 네트워크 인터페이스가 `eth1`인 것으로 가정합니다. 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** Azure NetApp Files에 대한 통신이 스토리지 네트워크 인터페이스를 통해 이동하도록 네트워크 경로를 추가합니다.  

    다음 지침에서는 스토리지 네트워크 인터페이스가 `eth1`인 것으로 가정합니다.  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    VM을 다시 부팅하여 변경 내용을 활성화합니다.  

3. **[A]** [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]에 설명된 대로 NFS를 사용하는 NetApp 시스템에서 SAP HANA를 실행하기 위해 OS를 준비합니다. NetApp 구성 설정에 대한 구성 파일 */etc/sysctl.d/netapp-hana.conf* 를 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** Microsoft for Azure 구성 설정을 사용하여 구성 파일 */etc/sysctl.d/ms-az.conf* 를 만듭니다.  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    net.ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

> [!TIP]
> SAP 호스트 에이전트가 포트 범위를 관리할 수 있도록 sysctl 구성 파일에서 net.ipv4.ip_local_port_range 및 net.ipv4.ip_local_reserved_ports를 명시적으로 설정하지 마십시오. 자세한 내용은 SAP Note [2382421](https://launchpad.support.sap.com/#/notes/2382421)을 참조하세요.  

4. **[A]** [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]의 권장대로 sunrpc 설정을 조정합니다.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** HANA 데이터베이스 볼륨에 대한 탑재 지점을 만듭니다.  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** **HN1**-shared에서 /usr/sap에 대한 노드 관련 디렉터리를 만듭니다.  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** NFS 도메인 설정을 확인합니다. 도메인이 기본 Azure NetApp Files 도메인(예: **`defaultv4iddomain.com`** )으로 구성되어 있고 매핑이 **nobody** 로 설정되어 있는지 확인합니다.  

    > [!IMPORTANT]
    > VM의 `/etc/idmapd.conf`에서 NFS 도메인을 Azure NetApp Files의 기본 도메인 구성( **`defaultv4iddomain.com`** )과 일치하도록 설정해야 합니다. NFS 클라이언트(예: VM)의 도메인 구성과 NFS 서버(예: Azure NetApp 구성)가 일치하지 않는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 사용 권한이 `nobody`로 표시됩니다.  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** `nfs4_disable_idmapping`을 확인합니다. **Y** 로 설정되어야 합니다. `nfs4_disable_idmapping`이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** SAP HANA 그룹 및 사용자를 수동으로 만듭니다. 그룹 sapsys 및 사용자 **hn1** adm의 ID는 온보딩 중에 제공된 것과 동일한 ID로 설정되어야 합니다. (이 예제에서 ID는 **1001** 로 설정되어 있습니다.) ID가 올바르게 설정되지 않으면 볼륨에 액세스할 수 없습니다. 그룹 sapsys 및 사용자 계정 **hn1** adm 및 sapadm의 ID는 모든 가상 머신에서 동일해야 합니다.  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** 공유 Azure NetApp Files 볼륨을 탑재합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** **hanadb1** 에 노드별 볼륨을 탑재합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** **hanadb2** 에 노드별 볼륨을 탑재합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** **hanadb3** 에 노드별 볼륨을 탑재합니다.  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 모든 HANA 볼륨이 NFS 프로토콜 버전 **NFSv4** 로 탑재되었는지 확인합니다.  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>설치  

이 예제에서는 Azure에서 대기 노드를 사용하여 스케일 아웃 구성에 SAP HANA를 배포하기 위해 HANA 2.0 SP4를 사용했습니다.  

### <a name="prepare-for-hana-installation"></a>HANA 설치 준비

1. **[A]** HANA 설치 전에 루트 암호를 설정합니다. 설치가 완료된 후 루트 암호를 사용하지 않도록 설정할 수 있습니다. `root` 명령 `passwd`로 실행합니다.  

2. **[1]** 암호를 입력하지 않고 SSH를 통해 **hanadb2** 및 **hanadb3** 에 로그인할 수 있는지 확인합니다.  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** HANA 2.0 SP4에 필요한 추가 패키지를 설치합니다. 자세한 내용은 SAP Note [2593824](https://launchpad.support.sap.com/#/notes/2593824)를 참조하세요. 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2], [3]** SAP HANA `data` 및 `log` 디렉터리 소유권을 **hn1** adm으로 변경합니다.   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA 설치

1. **[1]** [SAP HANA 2.0 설치 및 업데이트 가이드](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)의 지침에 따라 SAP HANA를 설치합니다. 이 예제에서는 마스터, 작업자 1개, 대기 노드 1개로 SAP HANA 스케일 아웃을 설치합니다.  

   a. HANA 설치 소프트웨어 디렉터리에서 **hdblcm** 프로그램을 시작합니다. `internal_network` 매개변수를 사용하고, 내부 HANA 노드 간 통신에 사용되는 서브넷의 주소 공간을 전달합니다.  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. 프롬프트에서 다음 값을 입력합니다.

     * **작업 선택**: **1** 입력(설치의 경우)
     * **설치할 추가 구성요소**: **2, 3** 입력
     * 설치 경로: Enter 키 누르기(기본값은 /hana/shared).
     * **로컬 호스트 이름**: Enter 키를 눌러 기본값 적용
     * **시스템에 호스트를 추가하시겠습니까?** : **y** 입력
     * **추가할 쉼표로 구분된 호스트 이름**: **hanadb2, hanadb3** 입력
     * **루트 사용자 이름** [root]: Enter 키를 눌러 기본값 적용
     * **루트 사용자 암호**: 루트 사용자의 암호 입력
     * 호스트 hanadb2의 역할: **1** 입력(작업자의 경우)
     * 호스트 hanadb2에 대한 **호스트 장애 조치 그룹** [기본값]: Enter 키를 눌러 기본값 적용
     * 호스트 hanadb2 []에 대 한 **Storage 파티션 번호** 의 경우 \<\<assign automatically\> \> enter 키를 눌러 기본값을 적용 합니다.
     * 호스트 hanadb2에 대한 **작업자 그룹** [기본값]: Enter 키를 눌러 기본값 적용
     * 호스트 hanadb3의 **역할 선택**: **2** 입력(대기)
     * 호스트 hanadb3에 대한 **호스트 장애 조치 그룹** [기본값]: Enter 키를 눌러 기본값 적용
     * 호스트 hanadb3에 대한 **작업자 그룹** [기본값]: Enter 키를 눌러 기본값 적용
     * **SAP HANA 시스템 ID**: **HN1** 입력
     * **인스턴스 번호** [00]: **03** 입력
     * **로컬 호스트 작업자 그룹** [기본값]: Enter 키를 눌러 기본값 적용
     * **시스템 사용량 선택/인덱스 입력 [4]** : **4** 입력(사용자 지정)
     * **데이터 볼륨의 위치** [/hana/data/HN1]: Enter 키를 눌러 기본값 적용
     * **로그 볼륨의 위치** [/hana/log/HN1]: Enter 키를 눌러 기본값 적용
     * **최대 메모리 할당 제한?** [n]: **n** 입력
     * **호스트 hanadb1의 인증서 호스트 이름** [hanadb1]: Enter를 눌러 기본값 적용
     * **호스트 hanadb2의 인증서 호스트 이름** [hanadb2]: Enter를 눌러 기본값 적용
     * **호스트 hanadb3의 인증서 호스트 이름** [hanadb3]: Enter를 눌러 기본값 적용
     * **시스템 관리자(hn1adm) 암호**: 암호 입력
     * **시스템 데이터베이스 사용자(system) 암호**: 시스템 암호 입력
     * **시스템 데이터베이스 사용자(system) 암호 확인**: 시스템 암호 입력
     * **머신을 다시 부팅한 후 시스템 다시 시작?** [n]: **n** 입력 
     * **계속하시겠습니까(y/n)** : 요약의 유효성을 검사하고 모든 항목이 양호하면 **y** 입력


2. **[1]** global.ini 확인  

   global.ini를 표시하고 내부 SAP HANA 노드 간 통신에 대한 구성이 준비되었는지 확인합니다. **통신** 섹션을 확인합니다. `hana` 서브넷의 주소 공간이 있어야 하고 `listeninterface`가 `.internal`로 설정되어야 합니다. **internal_hostname_resolution** 섹션을 확인합니다. `hana` 서브넷에 속하는 HANA 가상 머신의 IP 주소가 있어야 합니다.  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 클라이언트 IP 주소가 클라이언트 통신에 사용되도록 호스트 매핑을 추가합니다. `public_host_resolution` 섹션을 추가하고 클라이언트 서브넷에서 해당 IP 주소를 추가합니다.  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** SAP HANA를 다시 시작하여 변경 내용을 활성화합니다.  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 클라이언트 인터페이스에서 통신용 `client` 서브넷의 IP 주소를 사용하는지 확인합니다.  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   구성을 확인하는 방법은 SAP Note [2183363 - SAP HANA 내부 네트워크 구성](https://launchpad.support.sap.com/#/notes/2183363)을 참조하세요.  

6. 기본 Azure NetApp Files 스토리지에 대해 SAP HANA를 최적화하려면 다음 SAP HANA 매개 변수를 설정합니다.

   - `max_parallel_io_requests` **128**
   - `async_read_submit` **on**
   - `async_write_submit_active` **on**
   - `async_write_submit_blocks` **all**

   자세한 내용은 [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]을 참조하세요. 

   SAP HANA 2.0 시스템부터는 `global.ini`에서 매개 변수를 설정할 수 있습니다. 자세한 내용은 SAP Note [1999930](https://launchpad.support.sap.com/#/notes/1999930)을 참조하세요.  
   
   SAP HANA 1.0 시스템 버전 SPS12 이하의 경우 SAP Note [2267798](https://launchpad.support.sap.com/#/notes/2267798)의 설명대로 이러한 매개 변수는 설치 중에 설정할 수 있습니다.  

7. Azure NetApp Files에서 사용하는 스토리지의 파일 크기 제한은 16TB(테라바이트)입니다. SAP HANA는 스토리지 제한을 암시적으로 인식하지 않으며 파일 크기 제한인 16TB에 도달해도 자동으로 새 데이터 파일을 만들지 않습니다. SAP HANA가 16TB를 초과하여 파일을 확장하려고 시도하면 오류가 발생하고 결국 인덱스 서버 작동이 중단됩니다. 

   > [!IMPORTANT]
   > SAP HANA가 스토리지 하위 시스템의 [16TB 제한](../../../azure-netapp-files/azure-netapp-files-resource-limits.md)을 초과하여 데이터 파일을 늘리는 것을 방지하려면 `global.ini`에서 다음 매개 변수를 설정합니다.  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 자세한 내용은 SAP Note [2400005](https://launchpad.support.sap.com/#/notes/2400005)를 참조하세요.
   > SAP Note [2631285](https://launchpad.support.sap.com/#/notes/2631285)에 유의하세요. 

## <a name="test-sap-hana-failover"></a>SAP HANA 장애 조치(failover) 테스트 

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 마스터 및 슬레이브에 대한 참조가 포함되어 있습니다.  이러한 용어가 소프트웨어에서 제거되면 이 문서에서도 제거할 것입니다.

1. SAP HANA 작업자 노드에서 노드 충돌을 시뮬레이트합니다. 다음을 수행합니다. 

   a. 노드 충돌을 시뮬레이트하기 전에 **hn1** adm으로 다음 명령을 실행하여 환경의 상태를 캡처합니다.  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. 노드 충돌을 시뮬레이트하려면 작업자 노드(이 경우 **hanadb2**)에서 루트로 다음 명령을 실행합니다.  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 시스템에서 장애 조치 완료 여부를 모니터링합니다. 장애 조치가 완료되면 상태를 캡처합니다. 예를 들면 다음과 같습니다.  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 노드에 커널 패닉이 발생하면 ‘모든’ HANA 가상 머신에서 `kernel.panic`을 20초로 설정하여 SAP HANA 장애 조치로 인한 지연을 방지합니다. 이 구성은 `/etc/sysctl`에서 수행합니다. 가상 머신을 다시 부팅하여 변경 내용을 활성화합니다. 이 변경을 수행하지 않으면 노드에 커널 패닉이 발생할 때 장애 조치가 10분 이상 걸릴 수 있습니다.  

2. 다음을 수행하여 이름 서버를 종료합니다.

   a. 테스트 전에 **hn1** adm으로 다음 명령을 실행하여 환경의 상태를 확인합니다.  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. 활성 마스터 노드(이 경우 **hanadb1**)에서 **hn1** adm으로 다음 명령을 실행합니다.  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    대기 노드 **hanadb3** 이 마스터 노드가 됩니다. 장애 조치 테스트가 완료된 후 리소스 상태는 다음과 같습니다.  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. **hanadb1**(즉, 이름 서버가 종료된 동일한 가상 머신)에서 HANA 인스턴스를 다시 시작합니다. **hanadb1** 노드는 환경에 다시 참여하고 대기 역할을 유지합니다.  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **hanadb1** 에서 SAP HANA가 시작된 후 다음 상태가 예상됩니다.  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. 다시, 현재 활성 마스터 노드(즉, 노드 **hanadb3**)에서 이름 서버를 종료합니다.  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   노드 **hanadb1** 이 마스터 노드의 역할을 재개합니다. 장애 조치 테스트가 완료된 후 상태는 다음과 같습니다.

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. **hanadb3** 에서 SAP HANA를 시작하면 대기 노드로 사용할 준비가 됩니다.  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **hanadb3** 에서 SAP HANA가 시작된 후 상태는 다음과 같습니다.  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md)
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(가상 머신)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
