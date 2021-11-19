---
title: SLES의 SAP NetWeaver에 대한 다중 SID Azure VM 고가용성 가이드 | Microsoft Docs
description: SAP 애플리케이션용 SUSE Linux Enterprise Server의 SAP NetWeaver에 대한 다중 SID 고가용성 가이드
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/16/2020
ms.author: radeltch
ms.openlocfilehash: 8442418896f0ce07e601d89a9a1b42ca5b62404a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569298"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-for-sap-applications-multi-sid-guide"></a>SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 다중 SID 고가용성 가이드

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fsap%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 SAP 애플리케이션용 SUSE Linux Enterprise Server를 사용하는 Azure VM의 2노드 클러스터에 여러 SAP NetWeaver 또는 S4HANA 고가용성 시스템(즉, 다중 SID)을 배포하는 방법에 대해 설명합니다.  

예제 구성, 설치 명령 등에서 세 개의 SAP NetWeaver 7.50 시스템이 단일 2노드 고가용성 클러스터에 배포됩니다. SAP 시스템 SID는 다음과 같습니다.
* **NW1**: ASCS 인스턴스 번호 **00** 및 가상 호스트 이름 **msnw1ascs**, ERS 인스턴스 번호 **02** 및 가상 호스트 이름 **msnw1ers**  
* **NW2**: ASCS 인스턴스 번호 **10** 및 가상 호스트 이름 **msnw2ascs**, ERS 인스턴스 번호 **12** 및 가상 호스트 이름 **msnw2ers**  
* **NW3**: ASCS 인스턴스 번호 **20** 및 가상 호스트 이름 **msnw3ascs**, ERS 인스턴스 번호 **22** 및 가상 호스트 이름 **msnw3ers**  

이 문서에서는 데이터베이스 계층과 SAP NFS 공유 배포에 대해 설명하지 않습니다. 이 문서의 예제에서는 NFS 클러스터가 배포되었다고 가정하여 NW2 NFS 공유에 대해 nw2-nfs, NW3 NFS 공유 대해 nw3-nfs라는 가상 이름을 사용합니다.  

시작하기 전에 다음 SAP Note 및 백서를 참조하세요.

* SAP Note [1928533][1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917][2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [1944799][1944799]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 대한 SAP HANA 지침이 나와 있습니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498][2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692][2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787][1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드는 Netweaver HA 및 SAP HANA 시스템 복제 온-프레미스를 설정하는 데 필요한 모든 정보를 포함하고 있습니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE High Availability Extension 12 SP3 릴리스 정보][suse-ha-12sp3-relnotes]
* [SLES 12 및 SLES 15에 대한 SUSE 다중 SID 클러스터 가이드](https://documentation.suse.com/sbp/all/html/SBP-SAP-MULTI-SID/index.html)
* [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]
## <a name="overview"></a>개요

장애 조치(failover)가 발생하는 경우 클러스터에 참가하는 가상 머신은 모든 리소스를 실행할 수 있도록 크기를 조정해야 합니다. 각 SAP SID는 다중 SID 고가용성 클러스터에서 서로 독립적으로 장애 조치(failover)할 수 있습니다.  SBD 펜싱을 사용하는 경우 SBD 디바이스를 여러 클러스터 간에 공유할 수 있습니다.  

고가용성을 달성하려면 SAP NetWeaver에 고가용성 NFS 공유가 필요합니다. 다음 예에서는 SAP NFS 공유가 여러 SAP 시스템에서 사용할 수 있는 고가용성 [NFS 파일 서버](./high-availability-guide-suse-nfs.md)에서 호스팅된다고 가정합니다. 또는 공유가 [Azure NetApp Files NFS 볼륨](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)에 배포됩니다.  

![Pacemaker 클러스터는 두 개의 다중 SID 클러스터(msidcl1 및 msidcl2)에 대한 자세한 정보를 보여 줍니다.](./media/high-availability-guide-suse/ha-suse-multi-sid.png)

> [!IMPORTANT]
> Azure VM에서 SUSE Linux를 게스트 운영 체제로 사용하는 SAP ASCS/ERS의 다중 SID 클러스터링에 대한 지원은 동일한 클러스터에서 **5개** 의 SAP SID로 제한됩니다. 각각의 새 SID는 복잡성을 증가시킵니다. 동일한 클러스터에서 SAP 큐에 넣기 복제 서버 1과 큐에 넣기 복제 서버 2를 함께 사용하는 것은 **지원되지 않습니다**. 다중 SID 클러스터링은 서로 다른 SID가 있는 여러 SAP ASCS/ERS 인스턴스를 하나의 Pacemaker 클러스터에 설치하는 방법을 설명합니다. 현재 다중 SID 클러스터링은 ASCS/ERS에만 지원됩니다.  

> [!TIP]
> SAP ASCS/ERS의 다중 SID 클러스터링은 더 복잡한 솔루션입니다. 구현하는 것이 더 복잡합니다. 또한 유지 관리 작업(예: OS 패치 적용)을 실행할 때 더 많은 관리 작업이 필요합니다. 실제 구현을 시작하기 전에 배포 및 모든 관련 구성 요소(예: VM, NFS 탑재, VIP, 부하 분산 장치 구성 등)를 신중하게 계획하세요.  

NFS 서버, SAP NetWeaver ASCS, SAP NetWeaver SCS, SAP NetWeaver ERS 및 SAP HANA 데이터베이스는 가상 호스트 이름 및 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 Load Balancer가 필요합니다. [표준 부하 분산 장치](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)를 사용하는 것이 좋습니다.  

다음 목록에서는 세 개의 SAP 시스템을 사용하는 이 다중 SID 클러스터 예제에 대한 (A)SCS 및 ERS 부하 분산 장치의 구성을 보여 줍니다. 각 SID에 대한 각 ASCS 및 ERS 인스턴스에 대해 별도의 프런트 엔드 IP, 상태 프로브 및 부하 분산 규칙이 필요합니다. ASCS/ASCS 클러스터의 일부인 모든 VM을 하나의 백 엔드 풀에 할당합니다.  

### <a name="ascs"></a>(A)SCS

* 프런트 엔드 구성
  * NW1에 대한 IP 주소: 10.3.1.14
  * NW2에 대한 IP 주소: 10.3.1.16
  * NW3에 대한 IP 주소: 10.3.1.13
* 프로브 포트
  * 620 <strong>&lt;nr&gt;</strong> 포트, 따라서 NW1, NW2 및 NW3에 대한 프로브 포트는 각각 620 **00**, 620 **10** 및 620 **20** 임
* 부하 분산 규칙 - 
* 각 인스턴스(즉, NW1/ASCS, NW2/ASCS 및 NW3/ASCS)에 대해 하나씩 만듭니다.
  * 표준 Load Balancer를 사용하는 경우 **HA 포트** 를 선택합니다.
  * 기본 Load Balancer를 사용하는 경우 다음 포트에 대한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 36<strong>&lt;nr&gt;</strong> TCP
    * 39<strong>&lt;nr&gt;</strong> TCP
    * 81<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

### <a name="ers"></a>ERS

* 프런트 엔드 구성
  * NW1에 대한 IP 주소: 10.3.1.15
  * NW2에 대한 IP 주소: 10.3.1.17
  * NW3에 대한 IP 주소: 10.3.1.19
* 프로브 포트
  * 621 <strong>&lt;nr&gt;</strong> 포트, 따라서 NW1, NW2 및 N#에 대한 프로브 포트는 각각 621 **02**, 621 **12** and 621 **22** 임
* 부하 분산 규칙 - 각 인스턴스(즉, NW1/ERS, NW2/ERS 및 NW3/ERS)에 대해 하나씩 만듭니다.
  * 표준 Load Balancer를 사용하는 경우 **HA 포트** 를 선택합니다.
  * 기본 Load Balancer를 사용하는 경우 다음 포트에 대한 부하 분산 규칙을 만듭니다.
    * 32<strong>&lt;nr&gt;</strong> TCP
    * 33<strong>&lt;nr&gt;</strong> TCP
    * 5<strong>&lt;nr&gt;</strong>13 TCP
    * 5<strong>&lt;nr&gt;</strong>14 TCP
    * 5<strong>&lt;nr&gt;</strong>16 TCP

* 백 엔드 구성
  * (A)SCS/ERS 클러스터의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨

> [!IMPORTANT]
> 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.  

> [!Note]
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.  

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치되는 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하면 안 됩니다. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브에 오류가 발생합니다. 매개 변수 **net.ipv4.tcp_timestamps** 를 **0** 으로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브](../../../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.

## <a name="sap-nfs-shares"></a>SAP NFS 공유

SAP NetWeaver에는 전송, 프로필 디렉터리 등에 대한 공유 스토리지가 필요합니다. 고가용성 SAP 시스템의 경우 고가용성 NFS 공유를 사용해야 합니다. SAP NFS 공유의 아키텍처를 결정해야 합니다. 한 가지 옵션은 여러 SAP 시스템 간에 공유할 수 있는 [고가용성 NFS 클러스터를 SUSE Linux Enterprise Server의 Azure VM][nfs-ha]에 구축하는 것입니다. 

또 다른 옵션은 공유를 [Azure NetApp Files NFS 볼륨](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)에 배포하는 것입니다.  Azure NetApp Files를 사용하면 SAP NFS 공유에 대한 기본 제공 고가용성이 제공됩니다.

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>클러스터에 첫 번째 SAP 시스템 배포

이제 SAP NFS 공유에 대한 아키텍처를 결정했으므로 해당 설명서에 따라 첫 번째 SAP 시스템을 클러스터에 배포합니다.

* 고가용성 NFS 서버를 사용하는 경우 [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse.md)을 따릅니다.  
* Azure NetApp Files NFS 볼륨을 사용하는 경우 [SAP 애플리케이션용 Azure NetApp Files를 사용하는 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse-netapp-files.md)을 따릅니다.

위에 나열된 문서는 필요한 인프라를 준비하고, 클러스터를 구축하고, SAP 애플리케이션을 실행하기 위해 OS를 준비하는 단계를 안내합니다.  

> [!TIP]
> 첫 번째 시스템이 배포되면 추가 SAP SID를 클러스터에 추가하기 전에 항상 클러스터의 장애 조치(failover) 기능을 테스트하세요. 이렇게 하면 추가 SAP 시스템의 복잡성을 클러스터에 추가하기 전에 클러스터 기능이 작동하는지 알 수 있습니다.   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>클러스터에 추가 SAP 시스템 배포

이 예에서는 **NW1** 시스템이 이미 클러스터에 배포되었다고 가정합니다. 이제 **NW2** 및 **NW3** 클러스터 SAP 시스템에 배포하는 방법을 보여 줍니다. 

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

### <a name="prerequisites"></a>필수 구성 요소 

> [!IMPORTANT]
> 첫 번째 시스템 배포 중에만 필요한 단계가 있으므로 지침에 따라 추가 SAP 시스템을 클러스터에 배포하기 전에 지침에 따라 첫 번째 SAP 시스템을 클러스터에 배포합니다.  

이 설명서에서는 다음을 가정합니다.
* Pacemaker 클러스터가 이미 구성되어 실행되고 있습니다.  
* 하나 이상의 SAP 시스템(ASCS / ERS 인스턴스)이 이미 배포되어 클러스터에서 실행되고 있습니다.  
* 클러스터 장애 조치(failover) 기능이 테스트되었습니다.  
* 모든 SAP 시스템에 대한 NFS 공유가 배포됩니다.  

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

1. [Azure Portal을 통해 수동으로 Azure Load Balancer 배포](./high-availability-guide-suse-netapp-files.md#deploy-azure-load-balancer-manually-via-azure-portal) 지침에 따라 새로 배포된 시스템(즉, **NW2**, **NW3**)에 대한 구성을 기존 Azure Load Balancer에 추가합니다. 구성에 대한 IP 주소, 상태 프로브 포트, 부하 분산 규칙을 조정합니다.  

2. **[A]** 추가 SAP 시스템에 대한 이름 확인을 설정합니다. DNS 서버를 사용하거나 모든 노드에서 `/etc/hosts`를 수정할 수 있습니다. 다음 예제에서는 `/etc/hosts` 파일을 사용하는 방법을 보여 줍니다.  IP 주소 및 호스트 이름을 환경에 맞게 조정합니다. 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.16 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.13 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.17 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.19 msnw3ers
    # IP address for virtual host name for the NFS server for NW2
    10.3.1.31 nw2-nfs
    # IP address for virtual host name for the NFS server for NW3
    10.3.1.32 nw3-nfs
   ```

3. **[A]** 클러스터에 배포하는 추가 **NW2** 및 **NW3** SAP 시스템에 대한 공유 디렉터리를 만듭니다. 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** 클러스터에 배포하는 추가 SAP 시스템에 대해 /sapmnt/SID 및 /usr/sap/SID/SYS 파일 시스템을 탑재하도록 `autofs`를 구성합니다. 이 예제에서는 **NW2** 및 **NW3** 입니다.  

   `/etc/auto.direct` 파일을 클러스터에 배포하는 추가 SAP 시스템에 대한 파일 시스템으로 업데이트합니다.  

   * NFS 파일 서버를 사용하는 경우 [SLES의 SAP NetWeaver에 대한 Azure VM 고가용성](./high-availability-guide-suse.md#prepare-for-sap-netweaver-installation) 페이지의 지침을 따르세요.
   * Azure NetApp Files를 사용하는 경우 [Azure NetApp Files를 사용하는 SLES의 SAP NW에 대한 Azure VM 고가용성](./high-availability-guide-suse-netapp-files.md#prepare-for-sap-netweaver-installation) 페이지의 지침을 따르세요.

   새로 추가된 공유를 탑재하려면 `autofs` 서비스를 다시 시작해야 합니다.  

### <a name="install-ascs--ers"></a>ASCS/ERS 설치

1. 클러스터에 배포하는 추가 SAP 시스템의 ASCS 인스턴스에 대한 가상 IP 및 상태 프로브 클러스터 리소스를 만듭니다. 여기에 표시된 예제는 고가용성 NFS 서버를 사용하는 **NW2** 및 **NW3** ASCS에 대한 것입니다.  

   > [!IMPORTANT]
   > 최근 테스트 결과, 백로그와 하나의 연결만 처리하는 제한으로 인해 netcat이 요청에 응답하지 않는 것으로 확인되었습니다. netcat 리소스가 Azure Load Balancer 요청 수신을 중지하고 부동 IP를 사용할 수 없게 됩니다.  
   > 기존 Pacemaker 클러스터의 경우 netcat을 socat으로 교체할 것을 권장했습니다. 현재는 다음 패키지 버전 요구 사항과 함께 패키지 리소스 에이전트의 일부인 azure-lb 리소스 에이전트를 사용할 것을 권장합니다.
   > - SLES 12 SP4/SP5의 경우 버전은 resource-agents-4.3.018.a7fb5035-3.30.1 이상이어야 합니다.  
   > - SLES 15/15 SP1의 경우 버전은 resource-agents-4.3.0184.6ee15eb2-4.13.1 이상이어야 합니다.  
   >
   > 변경 내용 적용을 위해 잠시의 가동 중지 시간이 필요합니다.  
   > 기존 Pacemaker 클러스터의 경우 [Azure Load-Balancer 감지 강화](https://www.suse.com/support/kb/doc/?id=7024128)의 설명에 따라 socat을 사용하도록 구성을 이미 변경한 경우 즉시 azure-lb 리소스 에이전트로 전환할 필요가 없습니다.

    ```
      sudo crm configure primitive fs_NW2_ASCS Filesystem device='nw2-nfs:/NW2/ASCS' directory='/usr/sap/NW2/ASCS10' fstype='nfs4' \
       op start timeout=60s interval=0 \
       op stop timeout=60s interval=0 \
       op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW2_ASCS IPaddr2 \
        params ip=10.3.1.16 cidr_netmask=24 \
        op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW2_ASCS azure-lb port=62010
   
      sudo crm configure group g-NW2_ASCS fs_NW2_ASCS nc_NW2_ASCS vip_NW2_ASCS \
         meta resource-stickiness=3000

      sudo crm configure primitive fs_NW3_ASCS Filesystem device='nw3-nfs:/NW3/ASCS' directory='/usr/sap/NW3/ASCS20' fstype='nfs4' \
        op start timeout=60s interval=0 \
        op stop timeout=60s interval=0 \
        op monitor interval=20s timeout=40s
   
      sudo crm configure primitive vip_NW3_ASCS IPaddr2 \
       params ip=10.3.1.13 cidr_netmask=24 \
       op monitor interval=10 timeout=20
   
      sudo crm configure primitive nc_NW3_ASCS azure-lb port=62020
   
      sudo crm configure group g-NW3_ASCS fs_NW3_ASCS nc_NW3_ASCS vip_NW3_ASCS \
        meta resource-stickiness=3000
    ```

   리소스를 만들 때 리소스를 다른 클러스터 리소스에 할당할 수 있습니다. 이러한 리소스를 그룹화하면 클러스터 노드 중 하나로 마이그레이션됩니다. 클러스터 상태가 정상이고 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

2. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용하여 루트 권한으로 SAP NetWeaver ASCS를 설치합니다. 예를 들어 **NW2** 시스템의 경우 가상 호스트 이름은 <b>msnw2ascs</b>, <b>10.3.1.16</b> 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: <b>10</b>)입니다. **NW3** 시스템의 경우 가상 호스트 이름은 <b>msnw3ascs</b>, <b>10.3.1.13</b> 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: <b>20</b>)입니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. SAPINST_USE_HOSTNAME 매개 변수를 통해 가상 호스트 이름을 사용하여 SAP를 설치할 수 있습니다.  

     ```
      sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
     ```

   설치에서 하위 폴더를 /usr/sap/**SID**/ASCS **Instance#** 에 만들지 못하는 경우 소유자를 **sid** adm으로 설정하고, 그룹을 ASCS **Instance#** 의 sapsys로 설정하고, 다시 시도합니다.

3. **[1]** 클러스터에 배포하는 추가 SAP 시스템의 ERS 인스턴스에 대한 가상 IP 및 상태 프로브 클러스터 리소스를 만듭니다. 여기에 표시된 예제는 고가용성 NFS 서버를 사용하는 **NW2** 및 **NW3** ERS에 대한 것입니다. 

   ```
    sudo crm configure primitive fs_NW2_ERS Filesystem device='nw2-nfs:/NW2/ASCSERS' directory='/usr/sap/NW2/ERS12' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW2_ERS IPaddr2 \
      params ip=10.3.1.17 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW2_ERS azure-lb port=62112
   
    sudo crm configure group g-NW2_ERS fs_NW2_ERS nc_NW2_ERS vip_NW2_ERS

    sudo crm configure primitive fs_NW3_ERS Filesystem device='nw3-nfs:/NW3/ASCSERS' directory='/usr/sap/NW3/ERS22' fstype='nfs4' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW3_ERS IPaddr2 \
      params ip=10.3.1.19 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW3_ERS azure-lb port=62122
   
    sudo crm configure group g-NW3_ERS fs_NW3_ERS nc_NW3_ERS vip_NW3_ERS
   ```

   리소스를 만들 때 리소스를 다른 클러스터 노드에 할당할 수 있습니다. 이러한 리소스를 그룹화하면 클러스터 노드 중 하나로 마이그레이션됩니다. 클러스터 상태가 정상이고 모든 리소스가 시작되었는지 확인합니다.  

   다음으로, 새로 만든 ERS 그룹의 리소스가 동일한 SAP 시스템의 ASCS 인스턴스가 설치된 클러스터 노드의 반대쪽에 있는 클러스터 노드에서 실행되고 있는지 확인합니다.  예를 들어 NW2 ASCS가 `slesmsscl1`에 설치된 경우 NW2 ERS 그룹이 `slesmsscl2`에서 실행되고 있는지 확인합니다.  다음 명령을 실행하여 NW2 ERS 그룹을 `slesmsscl2`로 마이그레이션할 수 있습니다. 

    ```
      crm resource migrate g-NW2_ERS slesmsscl2 force
    ```

4. **[2]** SAP NetWeaver ERS 설치

   ERS에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용하여 루트 권한으로 SAP NetWeaver ERS를 다른 노드에 설치합니다. 예를 들어 **NW2** 시스템의 경우 가상 호스트 이름은 <b>msnw2ers</b>, <b>10.3.1.17</b> 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: <b>12</b>)입니다. **NW3** 시스템의 경우 가상 호스트 이름은 <b>msnw3ers</b>, <b>10.3.1.19</b> 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: <b>22</b>)입니다. 

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. SAPINST_USE_HOSTNAME 매개 변수를 통해 가상 호스트 이름을 사용하여 SAP를 설치할 수 있습니다.  

    ```
     sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   설치에서 하위 폴더를 /usr/sap/**NW2**/ERS **Instance#** 에 만들지 못하는 경우 소유자를 **sid** adm으로 설정하고, 그룹을 ERS **Instance#** 폴더의 sapsys로 설정하고, 다시 시도합니다.

   새로 배포된 SAP 시스템의 ERS 그룹을 다른 클러스터 노드로 마이그레이션해야 하는 경우 ERS 그룹에 대한 위치 제약 조건을 제거해야 합니다. 다음 명령을 실행하여 제약 조건을 제거할 수 있습니다(예제는 **NW2** and **NW3** SAP 시스템에 대해 제공됨).  

    ```
      crm resource unmigrate g-NW2_ERS
      crm resource unmigrate g-NW3_ERS
    ```

5. **[1]** ASCS/SCS 및 ERS 인스턴스 프로필을 새로 설치된 SAP 시스템에 맞게 조정합니다. 아래에 표시된 예제는 NW2에 대한 것입니다. ASCS/SCS 및 ERS 프로필을 클러스터에 추가된 모든 SAP 인스턴스에 맞게 조정해야 합니다.  
 
 * ASCS/SCS 프로필

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter, if using ENSA1
   enque/encni/set_so_keepalive = true
   ```

   ENSA1 및 ENSA2 모두에 대해 `keepalive` OS 매개 변수가 SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)에서 설명한 대로 설정되어 있는지 확인합니다.  

 * ERS 프로필

   ```
   sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```

6. **[A]** 새로 배포된 SAP 시스템(이 예제에서는 **NW2** and **NW3**)에 대한 SAP 사용자를 구성합니다. 

   ```
   # Add sidadm to the haclient group
   sudo usermod -aG haclient nw2adm
   sudo usermod -aG haclient nw3adm
   ```

7. 새로 설치된 SAP 시스템에 대한 ASCS 및 ERS SAP 서비스를 `sapservice` 파일에 추가합니다. 아래에 표시된 예제는 **NW2** 및 **NW3** SAP 시스템에 대한 것입니다.  

   ASCS 서비스 항목을 두 번째 노드에 추가하고 ERS 서비스 항목을 첫 번째 노드에 복사합니다. SAP 시스템에 대한 ASCS 인스턴스가 설치된 노드의 각 SAP 시스템에 대한 명령을 실행합니다.  

    ```
     # Execute the following commands on slesmsscl1,assuming the NW2 ASCS instance was installed on slesmsscl1
     cat /usr/sap/sapservices | grep ASCS10 | sudo ssh slesmsscl2 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl2 "cat /usr/sap/sapservices" | grep ERS12 | sudo tee -a /usr/sap/sapservices
     # Execute the following commands on slesmsscl2, assuming the NW3 ASCS instance was installed on slesmsscl2
     cat /usr/sap/sapservices | grep ASCS20 | sudo ssh slesmsscl1 "cat >>/usr/sap/sapservices"
     sudo ssh slesmsscl1 "cat /usr/sap/sapservices" | grep ERS22 | sudo tee -a /usr/sap/sapservices
    ```

8. **[1]** 새로 설치된 SAP 시스템에 대한 SAP 클러스터 리소스를 만듭니다. 

   큐에 넣기 서버 1 아키텍처(ENSA1)를 사용하는 경우 **NW2** 및 **NW3** SAP 시스템에 대한 리소스를 다음과 같이 정의합니다.

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure location loc_sap_NW2_failover_to_ers rsc_sap_NW2_ASCS10 rule 2000: runs_ers_NW2 eq 1
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true \
      meta priority=1000
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure location loc_sap_NW3_failover_to_ers rsc_sap_NW3_ASCS10 rule 2000: runs_ers_NW3 eq 1
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   SAP는 SAP NW 7.52부터 복제를 비롯하여 큐에 넣기 서버 2에 대한 지원을 도입했습니다. ABAP Platform 1809부터 큐에 넣기 서버 2가 기본적으로 설치됩니다. 큐에 넣기 서버 2 지원에 대해서는 SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416)을 참조하세요.
   큐에 넣기 서버 2 아키텍처([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html))를 사용하는 경우 **NW2** 및 **NW3** SAP 시스템에 대한 리소스를 다음과 같이 정의합니다.

    ```
     sudo crm configure property maintenance-mode="true"
    
     sudo crm configure primitive rsc_sap_NW2_ASCS10 SAPInstance \
      operations \$id=rsc_sap_NW2_ASCS10-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000 
    
     sudo crm configure primitive rsc_sap_NW2_ERS12 SAPInstance \
      operations \$id=rsc_sap_NW2_ERS12-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true 
    
     sudo crm configure modgroup g-NW2_ASCS add rsc_sap_NW2_ASCS10
     sudo crm configure modgroup g-NW2_ERS add rsc_sap_NW2_ERS12
    
     sudo crm configure colocation col_sap_NW2_no_both -5000: g-NW2_ERS g-NW2_ASCS
     sudo crm configure order ord_sap_NW2_first_start_ascs Optional: rsc_sap_NW2_ASCS10:start rsc_sap_NW2_ERS12:stop symmetrical=false
   
     sudo crm configure primitive rsc_sap_NW3_ASCS20 SAPInstance \
      operations \$id=rsc_sap_NW3_ASCS20-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ASCS10_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
      AUTOMATIC_RECOVER=false \
      meta resource-stickiness=5000
    
     sudo crm configure primitive rsc_sap_NW3_ERS22 SAPInstance \
      operations \$id=rsc_sap_NW3_ERS22-operations \
      op monitor interval=11 timeout=60 on-fail=restart \
      params InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW3_ERS22_msnw2ers" AUTOMATIC_RECOVER=false IS_ERS=true
    
     sudo crm configure modgroup g-NW3_ASCS add rsc_sap_NW3_ASCS20
     sudo crm configure modgroup g-NW3_ERS add rsc_sap_NW3_ERS22
    
     sudo crm configure colocation col_sap_NW3_no_both -5000: g-NW3_ERS g-NW3_ASCS
     sudo crm configure order ord_sap_NW3_first_start_ascs Optional: rsc_sap_NW3_ASCS20:start rsc_sap_NW3_ERS22:stop symmetrical=false
     sudo crm configure property maintenance-mode="false"
    ```

   이전 버전에서 업그레이드하고 큐에 넣기 서버 2로 전환하는 경우 SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)를 참조하세요. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.
   다음 예제에서는 **NW2** 및 **NW3** SAP 시스템이 클러스터에 추가된 후의 클러스터 리소스 상태를 보여 줍니다. 

    ```
     sudo crm_mon -r
    
    # Online: [ slesmsscl1 slesmsscl2 ]
    
    #Full list of resources:
    
    #stonith-sbd     (stonith:external/sbd): Started slesmsscl1
    # Resource Group: g-NW1_ASCS
    #     fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW1_ERS
    #     fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ASCS
    #     fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW2_ERS
    #     fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    # Resource Group: g-NW3_ASCS
    #     fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
    #     nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
    #     vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
    #     rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    # Resource Group: g-NW3_ERS
    #     fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
    #     nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
    #     vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
    #     rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
    ```

   다음 그림에서는 **NW2** SAP 시스템의 리소스를 확장한 상태에서 HA 웹 콘솔(Hawk)의 리소스 모양을 보여 줍니다.  

   [![SAP NetWeaver 고가용성 개요](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk.png)](./media/high-availability-guide-suse/ha-suse-multi-sid-hawk-detail.png#lightbox)

### <a name="proceed-with-the-sap-installation"></a>SAP 설치 진행 

다음을 수행하여 SAP 설치를 완료합니다.

* [SAP NetWeaver 애플리케이션 서버 준비](./high-availability-guide-suse.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [DBMS 인스턴스 설치](./high-availability-guide-suse.md#install-database)
* [기본 SAP 애플리케이션 서버 설치](./high-availability-guide-suse.md#sap-netweaver-application-server-installation)
* 하나 이상의 추가 SAP 애플리케이션 인스턴스 설치

## <a name="test-the-multi-sid-cluster-setup"></a>다중 SID 클러스터 설정 테스트

다음 테스트는 SUSE의 모범 사례 가이드에 있는 테스트 사례의 하위 세트입니다. 이는 편의를 위해 포함되었습니다. 클러스터 테스트의 전체 목록은 다음 설명서를 참조하세요.

* 고가용성 NFS 서버를 사용하는 경우 [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse.md)을 따릅니다.  
* Azure NetApp Files NFS 볼륨을 사용하는 경우 [SAP 애플리케이션용 Azure NetApp Files를 사용하는 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 고가용성](./high-availability-guide-suse-netapp-files.md)을 따릅니다.

항상 SUSE 모범 사례 가이드를 참조하고, 추가되었을 수 있는 모든 추가 테스트를 수행합니다.  
제공되는 테스트는 세 개의 SAP 시스템이 설치된 2노드 다중 SID 클러스터에 있습니다.  

1. HAGetFailoverConfig 및 HACheckFailoverConfig를 테스트합니다.

   ASCS 인스턴스를 현재 실행 중인 노드에서 \<sapsid\>adm으로 다음 명령을 실행합니다. 이러한 명령이 실패: 메모리 부족을 나타내며 실패할 경우 호스트 이름의 대시 때문일 수 있습니다. 이것은 알려진 문제로, SUSE에서는 sap-suse-cluster-connector 패키지에서 이 문제를 수정할 예정입니다.

   ```
    slesmsscl1:nw1adm 57> sapcontrol -nr 00 -function HAGetFailoverConfig

   # 10.12.2019 21:33:08
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw1adm 53> sapcontrol -nr 00 -function HACheckFailoverConfig

    # 19.12.2019 21:19:58
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl2:nw2adm 35> sapcontrol -nr 10 -function HAGetFailoverConfig

   # 10.12.2019 21:37:09
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl2
   # HANodes: slesmsscl2, slesmsscl1

    slesmsscl2:nw2adm 52> sapcontrol -nr 10 -function HACheckFailoverConfig

    # 19.12.2019 21:17:39
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch

    slesmsscl1:nw3adm 49> sapcontrol -nr 20 -function HAGetFailoverConfig

   # 10.12.2019 23:35:36
   # HAGetFailoverConfig
   # OK
   # HAActive: TRUE
   # HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4
   # HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP4 (sap_suse_cluster_connector 3.1.0)
   # HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   # HAActiveNode: slesmsscl1
   # HANodes: slesmsscl1, slesmsscl2

    slesmsscl1:nw3adm 52> sapcontrol -nr 20 -function HACheckFailoverConfig

    # 19.12.2019 21:10:42
    # HACheckFailoverConfig
    # OK
    # state, category, description, comment
    # SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   ```

2. 수동으로 ASCS 인스턴스를 마이그레이션합니다. 다음 예제에서는 SAP 시스템 NW2에 대한 ASCS 인스턴스를 마이그레이션하는 방법을 보여 줍니다.  
   테스트 시작 전 리소스 상태:
   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   다음 명령을 루트 권한으로 실행하여 NW2 ASCS 인스턴스를 마이그레이션합니다.

   ```
    crm resource migrate rsc_sap_NW2_ASCS10 force
    # INFO: Move constraint created for rsc_sap_NW2_ASCS10
    
    crm resource unmigrate rsc_sap_NW2_ASCS10
   # INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   
   # Remove failed actions for the ERS that occurred as part of the migration
    crm resource cleanup rsc_sap_NW2_ERS12
   ```

   테스트 후 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. HAFailoverToNode를 테스트합니다. 여기에 제공된 테스트는 SAP 시스템 NW2에 대한 ASCS 인스턴스 마이그레이션을 보여 줍니다.  

   테스트 시작 전 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   다음 명령을 **nw2** adm 권한으로 실행하여 NW2 ASCS 인스턴스를 마이그레이션합니다.

   ```
    slesmsscl2:nw2adm 53> sapcontrol -nr 10 -host msnw2ascs -user nw2adm password -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   crm resource cleanup rsc_sap_NW2_ERS12
   # Remove migration constraints
   crm resource clear rsc_sap_NW2_ASCS10
   #INFO: Removed migration constraints for rsc_sap_NW2_ASCS10
   ```

   테스트 후 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

1. 노드 작동 중단 시뮬레이트

   테스트 시작 전 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
   ```

   하나 이상의 ASCS 인스턴스가 실행되는 노드에서 루트 권한으로 다음 명령을 실행합니다. 다음 예제에서는 NW1 및 NW3에 대한 ASCS 인스턴스가 실행되는 `slesmsscl2`에서 명령을 실행했습니다.  

   ```
    slesmsscl2:~ # echo b > /proc/sysrq-trigger
   ```

   SBD를 사용하는 경우 종료된 노드에서 Pacemaker가 자동으로 시작되지 않습니다. 노드가 다시 시작된 후의 상태는 다음과 같습니다.

   ```
    Online: [ slesmsscl1 ]
    OFFLINE: [ slesmsscl2 ]
    Full list of resources:

    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl1
    
    Failed Resource Actions:
    * rsc_sap_NW1_ERS02_monitor_11000 on slesmsscl1 'not running' (7): call=125, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW2_ERS12_monitor_11000 on slesmsscl1 'not running' (7): call=126, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
    * rsc_sap_NW3_ERS22_monitor_11000 on slesmsscl1 'not running' (7): call=127, status=complete, exitreason='',
        last-rc-change='Fri Dec 13 19:32:10 2019', queued=0ms, exec=0ms
   ```

   다음 명령을 사용하여 종료된 노드에서 Pacemaker를 시작하고, SBD 메시지를 정리하고, 실패한 리소스를 정리합니다.

   ```bash
   # run as root
   # list the SBD device(s)
   cat /etc/sysconfig/sbd | grep SBD_DEVICE=

   # output is like:
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116;/dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1;/dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3"
   
   sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e116 -d /dev/disk/by-id/scsi-36001405034a84428af24ddd8c3a3e9e1 -d /dev/disk/by-id/scsi-36001405cdd5ac8d40e548449318510c3 message slesmsscl2 clear
   
   systemctl start pacemaker
   crm resource cleanup rsc_sap_NW1_ERS02
   crm resource cleanup rsc_sap_NW2_ERS12
   crm resource cleanup rsc_sap_NW3_ERS22
   ```

   테스트 후 리소스 상태:

   ```
    Full list of resources:
    stonith-sbd     (stonith:external/sbd): Started slesmsscl1
     Resource Group: g-NW1_ASCS
         fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW1_ERS
         fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW2_ASCS
         fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW2_ERS
         fs_NW2_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW2_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW2_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
     Resource Group: g-NW3_ASCS
         fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started slesmsscl1
         nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started slesmsscl1
         vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started slesmsscl1
         rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started slesmsscl1
     Resource Group: g-NW3_ERS
         fs_NW3_ERS (ocf::heartbeat:Filesystem):    Started slesmsscl2
         nc_NW3_ERS (ocf::heartbeat:azure-lb):      Started slesmsscl2
         vip_NW3_ERS        (ocf::heartbeat:IPaddr2):       Started slesmsscl2
         rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started slesmsscl2
   ```

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.
