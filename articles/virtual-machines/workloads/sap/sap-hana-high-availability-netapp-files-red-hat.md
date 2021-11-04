---
title: RHEL에서 ANF를 사용한 SAP HANA 스케일 업의 고가용성 | Microsoft Docs
description: Azure VM(Virtual Machine)에서 ANF를 사용하여 SAP HANA의 고가용성을 설정합니다.
services: virtual-machines-linux
documentationcenter: ''
author: rdeltcheva
manager: juergent
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/08/2021
ms.author: radeltch
ms.custom: ignite-fall-2021
ms.openlocfilehash: 917350cc99b19fc42dfb072307143e783050fcc7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131008430"
---
# <a name="high-availability-of-sap-hana-scale-up-with-azure-netapp-files-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux에서 Azure NetApp Files를 사용한 SAP HANA 스케일 업의 고가용성

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
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 ANF(Azure NetApp Files)를 사용하여 HANA 파일 시스템이 NFS를 통해 탑재된 경우 스케일 업 배포에서 SAP HANA 시스템 복제를 구성하는 방법을 설명합니다. 구성 및 설치 명령 예에서 인스턴스 번호 **03** 및 HANA 시스템 ID **HN1** 이 사용됩니다. SAP HANA 복제는 하나의 기본 노드와 하나 이상의 보조 노드로 구성됩니다.

이 문서의 단계가 다음 접두사로 표시되면 의미는 다음과 같습니다.

- **[A]** : 단계가 모든 노드에 적용됩니다.
- **[1]** : 단계가 노드 1에만 적용됩니다.
- **[2]** : 단계가 노드 2에만 적용됩니다.
 
다음 SAP Note 및 문서를 먼저 읽어 보세요.

- SAP Note [1928533](https://launchpad.support.sap.com/#/notes/1928533), 다음 항목을 포함합니다.
    - SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록.
    - Azure VM 크기에 대한 중요한 용량 정보.
    - 지원되는 SAP 소프트웨어 및 OS(운영 체제)와 데이터베이스 조합.
    - Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전.
- SAP Note [2015553](https://launchpad.support.sap.com/#/notes/2015553)는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
- SAP Note [405827](https://launchpad.support.sap.com/#/notes/405827)에는 HANA 환경의 권장되는 파일 시스템이 나열되어 있습니다.
- SAP Note [2002167](https://launchpad.support.sap.com/#/notes/2002167)에는 Red Hat Enterprise Linux에 권장되는 OS 설정이 있습니다.
- SAP Note [2009879](https://launchpad.support.sap.com/#/notes/2009879)에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
- SAP Note [2178632](https://launchpad.support.sap.com/#/notes/2178632)는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
- SAP Note [2191498](https://launchpad.support.sap.com/#/notes/2191498)는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
- SAP Note [2243692](https://launchpad.support.sap.com/#/notes/2243692)는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
- SAP Note [1999351](https://launchpad.support.sap.com/#/notes/1999351)은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
- [SAP Community Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)에는 Linux에 필요한 모든 SAP Note가 포함되어 있습니다.
- [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
- [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
- [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
- [SAP HANA system replication in pacemaker cluster](https://access.redhat.com/articles/3004101)(Pacemaker 클러스터의 SAP HANA 시스템 복제)
- 일반 RHEL 설명서
    - [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
    - [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
    - [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
    - [Configure SAP HANA System Replication in Scale-Up in a Pacemaker cluster when the HANA filesystems are on NFS shares](https://access.redhat.com/solutions/5156571)(HANA 파일 시스템이 NFS 공유에 있을 때 Pacemaker 클러스터의 스케일 업에서 SAP HANA 시스템 복제 구성)
- Azure 특정 RHEL 설명서:
    - [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
    - [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)
    - [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/solutions/3193782)(Microsoft Azure에서 사용할 용도로 Red Hat Enterprise Linux에 SAP HANA 설치)
    - [HANA 파일 시스템이 NFS 공유에 있을 때 Pacemaker 클러스터에서 SAP HANA 스케일 업 시스템 복제 구성](https://access.redhat.com/solutions/5156571)
- [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션](https://www.netapp.com/us/media/tr-4746.pdf)
- [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md)

## <a name="overview"></a>개요

일반적으로 스케일 업 환경에서는 SAP HANA의 모든 파일 시스템이 로컬 스토리지에서 탑재됩니다. Red Hat Enterprise Linux에서 SAP HANA 시스템 복제의 고가용성 설정은 [RHEL에서 SAP HANA 시스템 복제 설정](./sap-hana-high-availability-rhel.md) 가이드에 게시되어 있습니다.

[Azure NetApp Files](../../../azure-netapp-files/index.yml) NFS 공유에 대한 스케일 업 시스템의 SAP HANA 고가용성을 달성하기 위해 한 노드가 ANF의 NFS 공유에 대한 액세스 권한을 잃을 때 HANA 리소스를 복구하려면 클러스터에 몇 가지 추가 리소스 구성이 필요합니다.  클러스터는 NFS 탑재를 관리하여 리소스의 상태를 모니터링할 수 있도록 합니다. 파일 시스템 탑재와 SAP HANA 리소스 간의 종속성이 적용됩니다.  

![ANF의 SAP HANA HA 스케일 업](./media/sap-hana-high-availability-rhel/sap-hana-scale-up-netapp-files-red-hat.png)

SAP HANA 파일 시스템은 각 노드에서 Azure NetApp Files를 사용하여 NFS 공유에 탑재됩니다. 파일 시스템 /hana/data,/hana/log 및 /hana/shared는 각 노드에 고유합니다. 

Node1에 탑재됨(**hanadb1**)

- /hana/data에 10.32.2.4:/**hanadb1**-data-mnt00001
- /hana/log에 10.32.2.4:/**hanadb1**-log-mnt00001
- /hana/shared에 10.32.2.4:/**hanadb1**-shared-mnt00001

노드 2에 탑재됨(**hanadb2**)

- /hana/data에 10.32.2.4:/**hanadb2**-data-mnt00001
- /hana/log에 10.32.2.4:/**hanadb2**-log-mnt00001
- /hana/shared에 10.32.2.4:/**hanadb2**-shared-mnt00001

> [!NOTE]
> 파일 시스템 /hana/shared, /hana/data 및 /hana/log는 두 노드간에 공유되지 않습니다. 각 클러스터 노드에는 별도의 파일 시스템이 있습니다.   

SAP HANA 시스템 복제 구성은 전용 가상 호스트 이름과 가상 IP 주소를 사용합니다. Azure에서는 가상 IP 주소를 사용하려면 부하 분산 장치가 필요합니다. 다음 목록에는 부하 분산 장치의 구성이 나와 있습니다.

- 프런트 엔드 구성: hn1-db에 대한 IP 주소 10.32.0.10
- 백 엔드 구성: HANA 시스템 복제의 일부분이어야 하는 모든 가상 머신의 주 네트워크 인터페이스에 연결됨
- 프로브 포트: 포트 62503
- 부하 분산 규칙: 30313 TCP, 30315 TCP, 30317 TCP, 30340 TCP, 30341 TCP, 30342 TCP(기본 Azure Load Balancer를 사용하는 경우)  

## <a name="set-up-the-azure-netapp-file-infrastructure"></a>Azure NetApp Files 인프라 설정

Azure NetApp Files 인프라 설정을 진행하기 전에 [Azure NetApp Files 설명서](../../../azure-netapp-files/index.yml)를 숙지하세요.

Azure NetApp Files는 여러 [Azure 지역](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)에서 사용 가능합니다. 선택한 Azure 지역에서 Azure NetApp Files를 제공하는지 확인하세요.

Azure 지역별 Azure NetApp Files 가용성에 대한 자세한 내용은 [Azure 지역별 Azure NetApp Files 가용성](https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all)을 참조하세요.

### <a name="deploy-azure-netapp-files-resources"></a>Azure NetApp Files 리소스 배포

다음 지침에서는 [Azure 가상 네트워크](../../../virtual-network/virtual-networks-overview.md)를 이미 배포했다고 가정합니다. Azure NetApp Files 리소스가 탑재될 Azure NetApp Files 리소스와 VM을 동일하거나 피어링된 Azure Virtual Network에 배포해야 합니다.

1. [NetApp 계정 만들기](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)의 지침에 따라 선택한 Azure 지역에서 NetApp 계정을 만듭니다.

2.  [Azure NetApp Files 용량 풀 설정](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)의 지침에 따라 Azure NetApp Files 용량 풀을 설정합니다.

    이 문서에 제시된 HANA 아키텍처는 *Ultra* 서비스 수준에서 단일 Azure NetApp Files 용량 풀을 사용합니다. Azure의 HANA 워크로드의 경우 Azure NetApp Files *Ultra* 또는 *Premium* [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)을 사용하는 것이 좋습니다.

3.  [Azure NetApp Files에 서브넷 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)의 지침에 따라 Azure NetApp Files에 서브넷을 위임합니다.

4.  [Azure NetApp Files용 NFS 볼륨 만들기](../../../azure-netapp-files/azure-netapp-files-create-volumes.md) 지침에 따라 Azure NetApp Files 볼륨을 배포합니다.

    볼륨을 배포할 때 NFSv 4.1 버전을 선택해야 합니다. 지정된 Azure NetApp Files 서브넷에 볼륨을 배포합니다. Azure NetApp 볼륨의 IP 주소는 자동으로 할당됩니다.

    Azure NetApp Files 리소스와 Azure VM은 동일하거나 피어링된 Azure Virtual Network에 있어야 합니다. 예를 들어 hanadb1-data-mnt00001, hanadb1-log-mnt00001 등은 볼륨 이름이고 nfs://10.32.2.4/hanadb1-data-mnt00001, nfs://10.32.2.4/hanadb1-log-mnt00001 등은 Azure NetApp Files 볼륨의 파일 경로입니다.
    
    **hanadb1** 에서

    - 볼륨 hanadb1-data-mnt00001(nfs://10.32.2.4:/hanadb1-data-mnt00001) 
    - 볼륨 hanadb1-log-mnt00001(nfs://10.32.2.4:/hanadb1-log-mnt00001)
    - 볼륨 hanadb1-shared-mnt00001(nfs://10.32.2.4:/hanadb1-shared-mnt00001)
    
    **hanadb2** 에서

    - 볼륨 hanadb2-data-mnt00001(nfs://10.32.2.4:/hanadb2-data-mnt00001)
    - 볼륨 hanadb2-log-mnt00001(nfs://10.32.2.4:/hanadb2-log-mnt00001)
    - 볼륨 hanadb2-shared-mnt00001(nfs://10.32.2.4:/hanadb2-shared-mnt00001)

### <a name="important-considerations"></a>중요 고려 사항

SAP HANA 스케일 업 시스템에 대한 Azure NetApp Files를 만들 때 다음 사항을 고려해야 합니다.

- 최소 용량 풀은 4TiB(테비바이트)입니다.
- 최소 볼륨 크기는 100GiB(기비바이트)입니다.
- Azure NetApp Files 및 Azure NetApp Files 볼륨이 탑재될 모든 가상 머신은 동일한 Azure 가상 네트워크 또는 동일한 지역의 [피어링된 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md)에 있어야 합니다.
- 선택한 가상 네트워크에 Azure NetApp Files로 위임된 서브넷이 있어야 합니다.
- Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 할당량과 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨을 크기 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족해야 합니다.
- Azure NetApp Files [내보내기 정책](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)을 사용하여 허용되는 클라이언트, 액세스 유형(읽기-쓰기, 읽기 전용 등)을 제어할 수 있습니다.
- Azure NetApp Files 기능이 아직 영역을 인식하지 않습니다. 현재 이 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.

### <a name="sizing-of-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp Files 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 크기와 서비스 수준의 함수입니다.

Azure에서 SAP용 인프라를 설계할 때 최소 처리량 특성으로 변환되는 SAP에 대한 최소 스토리지의 일부 요구 사항에 유의해야 합니다.

- 1MB I/O 크기로 /hana/log에 대한 250MB/s(초당 MB)의 읽기-쓰기 작업입니다.
- 16MB 및 64MB I/O 크기로 /hana/data에 대한 최소 400MB/s의 읽기 작업입니다.
- 16MB 및 64MB I/O 크기로 /hana/data에 대한 최소 250MB/s의 쓰기 작업입니다.

볼륨 할당량 1TiB당 [Azure NetApp Files 처리량 한도](../../../azure-netapp-files/azure-netapp-files-service-levels.md)는 다음과 같습니다.

- Premium Storage 계층 - 64MiB/s
- Ultra Storage 계층 - 128MiB/s

/hana/data 및 /hana/log에 대한 SAP 최소 처리량 요구 사항과 /hana/shared에 대한 지침을 충족하기 위해 권장되는 크기는 다음과 같습니다.

|    볼륨    | Premium Storage 계층의 크기 | Ultra Storage 계층의 크기 | 지원되는 NFS 프로토콜 |
| :----------: | :--------------------------: | :------------------------: | :--------------------: |
|  /hana/log   |            4TiB             |           2TiB            |          v4.1          |
|  /hana/data  |           6.3TiB            |          3.2TiB           |          v4.1          |
| /hana/shared |           1 x RAM            |          1 x RAM           |          v3 또는 v4.1    |


> [!NOTE]
> 여기에 명시된 Azure NetApp Files 크기 조정 권장 사항은 SAP에서 인프라 공급자에 대해 권장하는 최소 요구 사항을 충족하는 것을 목표로 합니다. 실제 고객 배포 및 워크로드 시나리오에서는 이러한 크기가 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.

> [!TIP]
> 볼륨을 분리(*unmount*)하거나, 가상 머신을 중지하거나, SAP HANA를 중지하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 이 접근 방식을 사용하면 애플리케이션의 예상된 처리량 및 예기치 못한 처리량 요구 사항을 모두 유연하게 충족할 수 있습니다.

> [!NOTE]
> 이 문서에서 /hana/shared를 탑재하는 모든 명령은 NFSv4.1 /hana/shared 볼륨에 대해 제공됩니다.
> /hana/shared 볼륨을 NFSv3 볼륨으로 배포한 경우 NFSv3에 대한 /hana/shared의 탑재 명령을 조정하는 것을 잊지 마세요.


## <a name="deploy-linux-virtual-machine-via-azure-portal"></a>Azure Portal을 통해 Linux 가상 머신 배포 

먼저 Azure NetApp Files 볼륨을 만들어야 합니다. 그런 후 다음 단계를 수행합니다.

1.  리소스 그룹을 만듭니다.
2.  가상 네트워크를 만듭니다.
3.  가용성 집합을 만듭니다. 업데이트 도메인의 최대 수를 설정합니다.
4.  부하 분산 장치(내부)를 만듭니다. 표준 부하 분산 장치를 만드는 것이 좋습니다.
    2단계에서 만든 가상 네트워크를 선택합니다.
5.  가상 머신 1(**hanadb1**)을 만듭니다. 
6.  가상 머신 2(**hanadb2**)를 만듭니다.  
7.  가상 머신을 만드는 동안 모든 탑재 지점이 Azure NetApp Files의 NFS 공유에 있으므로 디스크를 추가하지 않습니다. 

> [!IMPORTANT]
> 부하 분산 시나리오의 NIC 보조 IP 구성에서는 부동 IP가 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.    

> [!NOTE] 
> 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.

8.  표준 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
    1.  먼저 프런트 엔드 IP 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
        1.  새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
        1.  **할당** 을 **정적** 으로 설정하고 IP 주소를 입력합니다(예: **10.32.0.10**).
        1.  **확인** 을 선택합니다.
        1.  새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.
    1.  다음으로, 백 엔드 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **백 엔드 풀** 을 선택한 다음, **추가** 를 클릭합니다.
        1.  새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
        1.  **가상 머신 추가** 를 선택합니다.
        1.  **가상 머신**을 선택합니다.
        1.  SAP HANA 클러스터의 가상 머신 및 해당 IP 주소를 선택합니다.
        1.  **추가** 를 선택합니다.
    1.  다음으로, 상태 프로브를 만듭니다.
        1.  부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
        1.  새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
        1.  프로토콜 및 포트 62503으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
        1.  **확인** 을 선택합니다.
    1.  다음으로 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: **hana-lb**).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**, **hana-backend** 및 **hana-hp**).
        1.  **HA 포트** 를 선택합니다.
        1.  **부동 IP를 사용하도록 설정** 했는지 확인합니다.
        1.  **확인** 을 선택합니다.


9. 또는 시나리오에서 기본 부하 분산 장치를 사용하는 경우 다음 구성 단계를 수행합니다.
    1.  부하 분산 장치를 구성합니다. 먼저 프런트 엔드 IP 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **프런트 엔드 IP 풀** 을 선택하고, **추가** 를 선택합니다.
        1.  새 프런트 엔드 IP 풀의 이름을 입력합니다(예: **hana-frontend**).
        1.  **할당** 을 **정적** 으로 설정하고 IP 주소를 입력합니다(예: **10.32.0.10**).
        1.  **확인** 을 선택합니다.
        1.  새 프런트 엔드 IP 풀을 만든 후, 풀 IP 주소를 적어 둡니다.
    1.  다음으로, 백 엔드 풀을 만듭니다.
        1.  부하 분산 장치를 열고, **백 엔드 풀** 을 선택한 다음, **추가** 를 클릭합니다.
        1.  새 백 엔드 풀의 이름 입력합니다(예: **hana-backend**).
        1.  **가상 머신 추가** 를 선택합니다.
        1.  3단계에서 만든 가용성 집합을 선택합니다.
        1.  SAP HANA 클러스터의 가상 머신을 선택합니다.
        1.  **확인** 을 선택합니다.
    1.  다음으로, 상태 프로브를 만듭니다.
        1.  부하 분산 장치를 열고, **상태 프로브** 를 선택한 다음, **추가** 를 선택합니다.
        1.  새 상태 프로브의 이름을 입력합니다(예: **hana-hp**).
        1.  프로토콜 및 포트 625 **03** 으로 **TCP** 를 선택합니다. 5로 설정된 **간격** 값, 2로 설정된 **비정상 임계값** 값을 유지합니다.
        1.  **확인** 을 선택합니다.
    1.  SAP HANA 1.0의 경우 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3 **03** 15).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
        1.  **TCP** 로 설정된 **프로토콜** 을 유지하고, 포트 3 **03** 15를 입력합니다.
        1.  **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정** 했는지 확인합니다.
        1.  **확인** 을 선택합니다.
        1.  포트 3 **03** 17에 대해 이러한 단계를 반복합니다.
    1.  SAP HANA 2.0의 경우 시스템 데이터베이스에 대한 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3 **03** 13).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
        1.  **TCP** 로 설정된 **프로토콜** 을 유지하고, 포트 3 **03** 13을 입력합니다.
        1.  **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정** 했는지 확인합니다.
        1.  **확인** 을 선택합니다.
        1.  포트 3 **03** 14에 대해 이러한 단계를 반복합니다.
    1.  SAP HANA 2.0의 경우 테넌트 데이터베이스에 대한 부하 분산 규칙을 만듭니다.
        1.  부하 분산 장치를 열고, **부하 분산 규칙** 을 선택한 다음, **추가** 를 선택합니다.
        1.  새 부하 분산 장치 규칙의 이름을 입력합니다(예: hana-lb-3 **03** 40).
        1.  이전에 만든 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브를 선택합니다(예: **hana-frontend**).
        1.  **TCP** 로 설정된 **프로토콜** 을 유지하고, 포트 3 **03** 40을 입력합니다.
        1.  **유휴 상태 시간 제한** 을 30분으로 증가시킵니다.
        1.  **부동 IP를 사용하도록 설정** 했는지 확인합니다.
        1.  **확인** 을 선택합니다.
        1.  포트 3 **03** 41 및 3 **03** 42에 대해 이러한 단계를 반복합니다.

SAP HANA에 필요한 포트에 대한 자세한 내용은 [SAP HANA 테넌트 데이터베이스](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6) 가이드의 [테넌트 데이터베이스에 연결](https://help.sap.com/viewer/78209c1d3a9b41cd8624338e42a12bf6/latest/en-US/7a9343c9f2a2436faa3cfdb5ca00c052.html) 챕터 또는 [SAP Note 2388694](https://launchpad.support.sap.com/#/notes/2388694)를 참조하세요.

> [!IMPORTANT]
> Azure Load Balancer 뒤에 배치되는 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하면 안 됩니다. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브에 오류가 발생합니다. 매개 변수 **net.ipv4.tcp_timestamps** 를 **0** 으로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브](../../../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요. 또한 SAP 참고 [2382421](https://launchpad.support.sap.com/#/notes/2382421)을 참조하세요.

## <a name="mount-the-azure-netapp-files-volume"></a>Azure NetApp Files 볼륨 탑재

1. **[A]** HANA 데이터베이스 볼륨에 대한 탑재 지점을 만듭니다. 

    ```
    mkdir -p /hana/data
    mkdir -p /hana/log
    mkdir -p /hana/shared
    ```

2. **[A]** NFS 도메인 설정 확인 도메인이 기본 Azure NetApp Files 도메인(예: **defaultv4iddomain.com**)으로 구성되어 있고 매핑이 **nobody** 로 설정되어 있는지 확인합니다.

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

    > [!IMPORTANT]    
    > VM의 /etc/idmapd.conf에서 NFS 도메인을 Azure NetApp Files의 기본 도메인 구성(**defaultv4iddomain.com**)과 일치하도록 설정합니다. NFS 클라이언트(예: VM)의 도메인 구성과 NFS 서버(예: Azure NetApp 구성)가 일치하지 않는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 사용 권한이 nobody로 표시됩니다.
    

3. **[1]** node1(**hanadb1**)에 노드별 볼륨 탑재 

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb1-data-mnt00001 /hana/data
    ```
    
4.  **[2]** node2(**hanadb2**)에 노드별 볼륨 탑재
    
    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-shared-mnt00001 /hana/shared
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-log-mnt00001 /hana/log
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.32.2.4:/hanadb2-data-mnt00001 /hana/data
    ```

5. **[A]** 모든 HANA 볼륨이 NFS 프로토콜 버전 NFSv4로 탑재되었는지 확인합니다.

    ```
    sudo nfsstat -m
    
    # Verify that flag vers is set to 4.1 
    # Example from hanadb1
    
    /hana/log from 10.32.2.4:/hanadb1-log-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/data from 10.32.2.4:/hanadb1-data-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    /hana/shared from 10.32.2.4:/hanadb1-shared-mnt00001
    Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.32.0.4,local_lock=none,addr=10.32.2.4
    ```

6. **[A]** **nfs4_disable_idmapping** 을 확인합니다. **Y** 로 설정되어야 합니다. **nfs4_disable_idmapping** 이 있는 디렉터리 구조를 만들려면 mount 명령을 실행합니다. 커널/드라이버용으로 액세스가 예약되어 있기 때문에 /sys/modules 아래에 디렉터리를 수동으로 만들 수 없습니다.

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # If you need to set nfs4_disable_idmapping to Y
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   **nfs_disable_idmapping** 매개 변수를 변경하는 방법에 대한 자세한 내용은 [https://access.redhat.com/solutions/1749883](https://access.redhat.com/solutions/1749883)을 참조하세요. 


## <a name="sap-hana-installation"></a>SAP HANA 설치

1. **[A]** 모든 호스트의 호스트 이름 확인을 설정합니다.

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts 파일을 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일을 사용하는 방법을 보여 줍니다. 다음 명령에서 IP 주소와 호스트 이름을 바꿉니다.

   ```
   sudo vi /etc/hosts
   # Insert the following lines in the /etc/hosts file. Change the IP address and hostname to match your environment  
   10.32.0.4   hanadb1
   10.32.0.5   hanadb2
   ```

3. **[A]** [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]에 설명된 대로 NFS를 사용하는 Azure NetApp에서 SAP HANA를 실행하기 위해 OS를 준비합니다. NetApp 구성 설정에 대한 구성 파일 */etc/sysctl.d/netapp-hana.conf* 를 만듭니다.  

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
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 추가 최적화 설정을 사용하여 */etc/sysctl.d/ms-az.conf* 구성 파일을 만듭니다.  

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

5. **[A]** [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]의 권장대로 sunrpc 설정을 조정합니다.  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

2. **[A]** HANA용 RHEL 구성

   RHEL 버전에 따라 아래 SAP Note에 설명된 대로 RHEL을 구성합니다.

   - [2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2292690)
   - [2777782 - SAP HANA DB: RHEL 8에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582 - Linux: GCC 6.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824 - Linux: GCC 7.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607 - Linux: GCC 9.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/2886607)

3. **[A]** SAP HANA 설치

   HANA 2.0 SPS 01로 시작하는 MDC는 기본 옵션입니다. HANA 시스템을 설치하는 경우 SYSTEMDB와 동일한 SID를 가진 테넌트를 함께 만듭니다. 경우에 따라 기본 테넌트가 필요하지 않습니다. 설치와 함께 초기 테넌트를 만들고 싶지 않은 경우 SAP Note [2629711](https://launchpad.support.sap.com/#/notes/2629711)을 따를 수 있습니다.

    HANA DVD에서 **hdblcm** 프로그램을 실행합니다. 프롬프트에서 다음 값을 입력합니다.  
    설치 선택: **1** 을 입력합니다.(설치용)  
    설치할 추가 구성 요소 선택: **1** 을 입력합니다.  
    설치 경로 입력 [/hana/shared]: 기본값을 적용하려면 Enter 키를 누릅니다.  
    로컬 호스트 이름 입력 [..]: Enter 키를 눌러 기본값을 허용합니다.  
    시스템에 호스트를 추가할까요? (y/n) [n]: **n**  
    SAP HANA 시스템 ID 입력: **HN1** 을 입력합니다.  
    인스턴스 번호 입력 [00]: **03** 을 입력합니다.  
    데이터베이스 모드 선택 / 인덱스 입력 [1]: Enter 키를 눌러 기본값을 허용합니다.  
    시스템 사용량 선택 / 인덱스 [4]: **4**(사용자 지정)를 입력합니다.  
    데이터 볼륨의 위치 입력 [/hana/data]: Enter 키를 눌러 기본값을 허용합니다.  
    로그 볼륨의 위치 입력 [/hana/log]: Enter 키를 눌러 기본값을 허용합니다.  
    최대 메모리 할당 제한? [n]: Enter 키를 눌러 기본값을 허용합니다.  
    호스트 '...'에 대한 인증서 호스트 이름 입력 [...]: Enter 키를 눌러 기본값을 허용합니다.  
    SAP 호스트 에이전트 사용자(sapadm) 암호 입력: 호스트 에이전트 사용자 암호를 입력합니다.  
    SAP 호스트 에이전트 사용자(sapadm) 암호 확인: 호스트 에이전트 사용자 암호를 다시 입력하여 확인합니다.  
    시스템 관리자(hn1adm) 암호 입력: 시스템 관리자 암호를 입력합니다.  
    시스템 관리자(hn1adm) 암호 확인: 시스템 관리자 암호를 다시 입력하여 확인합니다.  
    시스템 관리자 홈 디렉터리 입력 [/usr/sap/HN1/home]: Enter 키를 눌러 기본값을 허용합니다.  
    시스템 관리자 로그인 셸 입력 [/bin/sh]: Enter 키를 눌러 기본값을 허용합니다.  
    시스템 관리자 사용자 ID 입력 [1001]: Enter 키를 눌러 기본값을 허용합니다.  
    사용자 그룹(sapsys)의 ID 입력 [79]: Enter 키를 눌러 기본값을 허용합니다.  
    데이터베이스 사용자(SYSTEM) 암호 입력: 데이터베이스 사용자 암호를 입력합니다.  
    데이터베이스 사용자(SYSTEM) 암호 확인: 데이터베이스 사용자 암호를 다시 입력하여 확인합니다.  
    컴퓨터를 다시 부팅한 다음 시스템 다시 시작? [n]: Enter 키를 눌러 기본값을 허용합니다.  
    계속할까요? (y/n): 요약의 유효성을 검사합니다. 계속하려면 **y** 를 입력합니다.  

4. **[A]** SAP 호스트 에이전트 업그레이드

   [SAP 소프트웨어 센터](https://launchpad.support.sap.com/#/softwarecenter)에서 최신 SAP 호스트 에이전트 아카이브를 다운로드하고 다음 명령을 실행하여 에이전트를 업그레이드합니다. 다운로드한 파일을 가리키도록 아카이브의 경로를 바꿉니다.

   ```
   sudo /usr/sap/hostctrl/exe/saphostexec -upgrade -archive <path to SAP Host Agent SAR>
   ```

5. **[A]** 방화벽 구성

   Azure 부하 분산 장치 프로브 포트의 방화벽 규칙을 만듭니다.

   ```
   sudo firewall-cmd --zone=public --add-port=62503/tcp
   sudo firewall-cmd --zone=public --add-port=62503/tcp –permanent
   ```

## <a name="configure-sap-hana-system-replication"></a>SAP HANA 시스템 복제 구성

[SAP HANA 시스템 복제](./sap-hana-high-availability-rhel.md#configure-sap-hana-20-system-replication) 설정의 단계에 따라 SAP HANA 시스템 복제를 구성합니다. 

## <a name="cluster-configuration"></a>클러스터 구성

이 섹션에서는 Azure NetApp Files를 사용하여 NFS 공유에 SAP HANA를 설치할 때 클러스터가 원활하게 작동하는 데 필요한 단계를 설명합니다. 

### <a name="create-a-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정](./high-availability-guide-rhel-pacemaker.md) 단계에 따라 이 HANA 서버용 기본 Pacemaker 클러스터를 만듭니다.

### <a name="implement-the-python-system-replication-hook-saphanasr"></a>Python 시스템 복제 후크 SAPHanaSR 구현

이는 클러스터와의 통합을 최적화하고 클러스터 장애 조치(failover)가 필요할 때 검색 기능을 향상시키는 중요한 단계입니다. SAPHanaSR python 후크를 구성하는 것이 좋습니다.    

1. **[A]** HANA "시스템 복제 후크"를 설치합니다. 후크는 두 개의 HANA DB 노드에 모두 설치해야 합니다.           

   > [!TIP]
   > Python 후크는 HANA 2.0에서만 구현할 수 있습니다.        

   1. 후크를 `root`로 준비합니다.  

    ```bash
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR/srHook/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 두 노드에서 HANA를 중지합니다. <sid\>adm으로 실행합니다.  
   
    ```bash
    sapcontrol -nr 03 -function StopSystem
    ```

   3. 각 클러스터 노드에서 `global.ini`를 조정합니다.  
 
    ```bash
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

2. **[A]** 클러스터에서 <sid\>adm용 각 클러스터 노드에 sudoers 구성을 요구합니다. 이 예제에서는 새 파일을 만들어 수행합니다. 명령을 `root`로 실행합니다.    
    ```bash
    sudo visudo -f /etc/sudoers.d/20-saphana
    # Insert the following lines and then save
    Cmnd_Alias SITE1_SOK   = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE1 -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE1_SFAIL = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE1 -v SFAIL -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE2_SOK   = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE2 -v SOK -t crm_config -s SAPHanaSR
    Cmnd_Alias SITE2_SFAIL = /usr/sbin/crm_attribute -n hana_hn1_site_srHook_SITE2 -v SFAIL -t crm_config -s SAPHanaSR
    hn1adm ALL=(ALL) NOPASSWD: SITE1_SOK, SITE1_SFAIL, SITE2_SOK, SITE2_SFAIL
    Defaults!SITE1_SOK, SITE1_SFAIL, SITE2_SOK, SITE2_SFAIL !requiretty
    ```

3. **[A]** 두 노드에서 SAP HANA를 시작합니다. <sid\>adm으로 실행합니다.  

    ```bash
    sapcontrol -nr 03 -function StartSystem 
    ```

4. **[1]** 후크 설치를 확인합니다. 활성 HANA 시스템 복제 사이트에서 <sid\>adm으로 실행합니다.   

    ```bash
     cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*
     # Example output
     # 2021-04-12 21:36:16.911343 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:36:29.147808 ha_dr_SAPHanaSR SFAIL
     # 2021-04-12 21:37:04.898680 ha_dr_SAPHanaSR SOK

    ```

SAP HANA 시스템 복제 후크 구현에 대한 자세한 내용은 [SAP HA/DR 공급자 후크 사용](https://access.redhat.com/articles/3004101#enable-srhook)을 참조하세요.  

### <a name="configure-filesystem-resources"></a>파일 시스템 리소스 구성

이 예에서는 각 클러스터 노드에 고유한 HANA NFS 파일 시스템 /hana/shared, /hana/data 및 /hana/log가 있습니다.   

1. **[1]** 클러스터를 유지 관리 모드로 설정합니다.

   ```
   pcs property set maintenance-mode=true
   ```

2. **[1]** **hanadb1** 탑재를 위한 파일 시스템 리소스를 만듭니다.

    ```
    pcs resource create hana_data1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_log1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    pcs resource create hana_shared1 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb1-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb1_nfs
    ```

3. **[2]** **hanadb2** 탑재를 위한 파일 시스템 리소스를 만듭니다.

    ```
    pcs resource create hana_data2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-data-mnt00001 directory=/hana/data fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_log2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-log-mnt00001 directory=/hana/log fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    pcs resource create hana_shared2 ocf:heartbeat:Filesystem device=10.32.2.4:/hanadb2-shared-mnt00001 directory=/hana/shared fstype=nfs options=rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 --group hanadb2_nfs
    ```

    `OCF_CHECK_LEVEL=20` 특성이 모니터 작업에 추가되어 각 모니터가 파일 시스템에서 읽기/쓰기 테스트를 수행합니다. 이 특성이 없으면 모니터 작업은 파일 시스템이 탑재되어 있는지만 확인합니다. 연결이 끊어지면 액세스할 수 없더라도 파일 시스템이 탑재된 상태로 남아있을 수 있기 때문에 이것은 문제가 될 수 있습니다.

    `on-fail=fence` 특성도 모니터 작업에 추가됩니다. 이 옵션을 사용하면 노드에서 모니터 작업이 실패하는 경우 해당 노드가 즉시 펜싱됩니다. 이 옵션이 없으면 기본 동작으로 실패한 리소스에 종속된 모든 리소스를 중지하고, 실패한 리소스를 다시 시작한 다음, 실패한 리소스에 종속된 모든 리소스를 시작합니다. 이 동작은 SAPHana 리소스가 실패한 리소스에 의존하는 경우 시간이 오래 걸릴 뿐만 아니라 완전히 실패할 수도 있습니다. HANA 실행 파일이 있는 NFS 서버에 액세스할 수 없는 경우 SAPHana 리소스를 중지할 수 없습니다.

4. **[1]** 위치 제약 조건 구성

   Hanadb1 고유 탑재를 관리하는 리소스가 hanadb2에서 실행될 수 없도록 위치 제약 조건을 구성하고 그 반대의 경우도 마찬가지입니다.

    ```
    pcs constraint location hanadb1_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb2
    pcs constraint location hanadb2_nfs rule score=-INFINITY resource-discovery=never \#uname eq hanadb1
    ```

    `resource-discovery=never` 옵션은 각 노드의 고유 탑재가 동일한 탑재 지점을 공유하기 때문에 설정됩니다. 예를 들어, `hana_data1`은 탑재 지점 `/hana/data`를 사용하고 `hana_data2`도 탑재 지점 `/hana/data`를 사용합니다. 이로 인해 클러스터 시작 시 리소스 상태가 확인될 때 프로브 작업에 대해 가양성이 발생할 수 있으며, 이로 인해 불필요한 복구 동작이 발생할 수 있습니다. `resource-discovery=never`를 설정하면 이를 방지할 수 있습니다.

5. **[1]** 특성 리소스 구성

   특성 리소스를 구성합니다. 이러한 특성은 노드의 모든 NFS 탑재(/hana/data, /hana/log 및 /hana/data)가 탑재된 경우 true로 설정되고 그렇지 않은 경우 false로 설정됩니다.

   ```
   pcs resource create hana_nfs1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs1_active
   pcs resource create hana_nfs2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs2_active
   ```

6. **[1]** 위치 제약 조건 구성

   Hanadb1의 특성 리소스가 hanadb2에서 실행되지 않도록 하는 위치 제약 조건을 구성하고 그 반대의 경우도 마찬가지입니다.

   ```
   pcs constraint location hana_nfs1_active avoids hanadb2
   pcs constraint location hana_nfs2_active avoids hanadb1
   ```

7. **[1]** 주문 제약 조건 만들기

   노드의 모든 NFS 탑재를 탑재한 후에 노드의 특성 리소스가 시작되도록 정렬 제약 조건을 구성합니다.
   
    ```
    pcs constraint order hanadb1_nfs then hana_nfs1_active
    pcs constraint order hanadb2_nfs then hana_nfs2_active
    ```

   > [!TIP]
   > 구성에 `hanadb1_nfs` 또는 `hanadb2_nfs` 그룹 외부의 파일 시스템이 포함된 경우 파일 시스템 간에 순서 종속성이 없도록 `sequential=false` 옵션을 포함합니다. 모든 파일 시스템은 `hana_nfs1_active` 전에 시작해야 하지만 서로 상대적인 순서로 시작할 필요는 없습니다. 자세한 내용은 [HANA 파일 시스템이 NFS 공유에 있을 때 Pacemaker 클러스터의 스케일 업에서 SAP HANA 시스템 복제를 구성하는 방법](https://access.redhat.com/solutions/5156571)을 참조합니다.

### <a name="configure-sap-hana-cluster-resources"></a>SAP HANA 클러스터 리소스 구성

1. [SAP HANA 클러스터 리소스 만들기](./sap-hana-high-availability-rhel.md#create-sap-hana-cluster-resources)의 단계에 따라 클러스터에 SAP HANA 리소스를 만듭니다. SAP HANA 리소스가 만들어지면 SAP HANA 리소스와 파일 시스템(NFS 탑재) 간에 위치 규칙 제약 조건을 만들어야 합니다.

2. **[1]** SAP HANA 리소스와 NFS 탑재 간의 제약 조건 구성

   모든 노드의 NFS 탑재를 탑재한 경우에만 노드에서 SAP HANA 리소스를 실행할 수 있도록 위치 규칙 제약 조건이 설정됩니다.

    ```
    pcs constraint location SAPHanaTopology_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 7.x
    pcs constraint location SAPHana_HN1_03-master rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # On RHEL 8.x
    pcs constraint location SAPHana_HN1_03-clone rule score=-INFINITY hana_nfs1_active ne true and hana_nfs2_active ne true
    # Take the cluster out of maintenance mode
    sudo pcs property set maintenance-mode=false
    ```

   클러스터 및 모든 리소스의 상태를 확인합니다.
   > [!NOTE]
   > 이 문서에는 Microsoft에서 더 이상 사용하지 않는 용어인 *슬레이브* 용어에 대한 참조가 포함되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.
   
    ```
    sudo pcs status
    
    Online: [ hanadb1 hanadb2 ]
    
    Full list of resources:
    
    rsc_hdb_azr_agt(stonith:fence_azure_arm):  Started hanadb1
    
    Resource Group: hanadb1_nfs
    hana_data1 (ocf::heartbeat:Filesystem):Started hanadb1
    hana_log1  (ocf::heartbeat:Filesystem):Started hanadb1
    hana_shared1   (ocf::heartbeat:Filesystem):Started hanadb1
    
    Resource Group: hanadb2_nfs
    hana_data2 (ocf::heartbeat:Filesystem):Started hanadb2
    hana_log2  (ocf::heartbeat:Filesystem):Started hanadb2
    hana_shared2   (ocf::heartbeat:Filesystem):Started hanadb2
    
    hana_nfs1_active   (ocf::pacemaker:attribute): Started hanadb1
    hana_nfs2_active   (ocf::pacemaker:attribute): Started hanadb2
    
    Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
    Started: [ hanadb1 hanadb2 ]
    
    Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
    Masters: [ hanadb1 ]
    Slaves: [ hanadb2 ]
    
    Resource Group: g_ip_HN1_03
    nc_HN1_03  (ocf::heartbeat:azure-lb):  Started hanadb1
    vip_HN1_03 (ocf::heartbeat:IPaddr2):   Started hanadb1
    ```

## <a name="configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster"></a>Pacemaker 클러스터에서 HANA 활성/읽기 사용 시스템 복제 구성

SAP HANA 2.0 SPS 01부터 SAP는 SAP HANA 시스템 복제를 위한 활성/읽기 사용 설정을 허용합니다. 여기서 SAP HANA 시스템 복제의 보조 시스템은 읽기 집약적인 워크로드에 적극적으로 사용할 수 있습니다. 클러스터에서 이러한 설정을 지원하려면 클라이언트가 보조 읽기 사용 SAP HANA 데이터베이스에 액세스할 수 있도록 두 번째 가상 IP 주소가 필요합니다. 인수가 발생한 후에도 보조 복제 사이트에 액세스할 수 있도록 클러스터에서 가상 IP 주소를 SAPHana 리소스의 보조로 이동해야 합니다.

두 번째 가상 IP를 사용하는 Red Hat 고가용성 클러스터에서 HANA 활성/읽기 사용 시스템 복제를 관리하는 데 필요한 추가 구성은 [Pacemaker 클러스터에서 Hana 활성/읽기 사용 시스템 복제 구성](./sap-hana-high-availability-rhel.md#configure-hana-activeread-enabled-system-replication-in-pacemaker-cluster)에 설명되어 있습니다.  

계속 진행하기 전에 설명서의 상단 세그먼트에 설명된 대로 SAP HANA 데이터베이스를 관리하는 Red Hat 고가용성 클러스터를 완전히 구성했는지 확인합니다.    


## <a name="test-the-cluster-setup"></a>클러스터 설정 테스트

이 섹션에서는 설정을 테스트하는 방법을 설명합니다. 

1. 테스트를 시작하기 전에 Pacemaker에 실패한 작업이 없으며(pcs status를 통해), 예기치 않은 위치 제약 조건이 없고(예를 들어 마이그레이션 테스트의 결과), systemReplicationStatus의 경우처럼 해당 HANA 시스템 복제가 동기화 상태에 있는지 확인합니다.

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    ```

2. 노드가 NFS 공유(/hana/shared)에 대한 액세스 권한을 잃을 때 장애 시나리오에 대한 클러스터 구성을 확인합니다.

   SAP HANA 리소스 에이전트는 `/hana/shared`에 저장된 이진 파일에 의존하여 장애 조치(failover)동안 작업을 수행합니다. 제시된 시나리오에서는 `/hana/shared` 파일 시스템이 NFS를 통해 탑재됩니다.  
   서버 중 하나가 NFS 공유에 대한 액세스 권한을 상실하는 오류를 시뮬레이션하는 것은 어렵습니다. 실행할 수 있는 테스트는 파일 시스템을 읽기 전용으로 다시 탑재하는 것입니다.
   이 접근 방식은 활성 노드에서 `/hana/shared`에 대한 액세스가 손실된 경우 클러스터가 장애 조치(failover)를 수행할 수 있는지 확인합니다.     


   **예상 결과:** `/hana/shared`를 읽기 전용 파일 시스템으로 만들면 파일 시스템에서 읽기/쓰기 작업을 수행하는 `hana_shared1` 리소스의 `OCF_CHECK_LEVEL` 특성이 파일 시스템에 어떤 항목도 쓸 수 없어 실패하고 HANA 리소스 장애 조치(failover)를 수행합니다.  HANA 노드가 NFS 공유에 액세스할 수 없는 경우에도 동일한 결과가 예상됩니다. 

   테스트 시작 전 리소스 상태:

    ```
    sudo pcs status
    # Example output
    Full list of resources:
     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb1

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_log1  (ocf::heartbeat:Filesystem):    Started hanadb1
         hana_shared1       (ocf::heartbeat:Filesystem):    Started hanadb1

    Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Started hanadb1
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb1 hanadb2 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb1 ]
         Slaves: [ hanadb2 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb1
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb1
    ```

   다음 명령을 사용하여 활성 클러스터 노드에 /hana/shared를 읽기 전용 모드로 설정할 수 있습니다.

    ```
    sudo mount -o ro 10.32.2.4:/hanadb1-shared-mnt00001 /hana/shared
    ```

   hanadb1이 stonith(`pcs property show stonith-action`)에 설정된 작업에 따라 재부팅하거나 전원을 끕니다.  이 서버(hanadb1)가 다운되면 HANA 리소스가 hanadb2로 이동합니다. Hanadb2에서 클러스터의 상태를 확인할 수 있습니다.

    ```
    pcs status

    Full list of resources:

     rsc_hdb_azr_agt        (stonith:fence_azure_arm):      Started hanadb2

     Resource Group: hanadb1_nfs
         hana_data1 (ocf::heartbeat:Filesystem):    Stopped
         hana_log1  (ocf::heartbeat:Filesystem):    Stopped
         hana_shared1       (ocf::heartbeat:Filesystem):    Stopped

     Resource Group: hanadb2_nfs
         hana_data2 (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_log2  (ocf::heartbeat:Filesystem):    Started hanadb2
         hana_shared2       (ocf::heartbeat:Filesystem):    Started hanadb2

     hana_nfs1_active       (ocf::pacemaker:attribute):     Stopped
     hana_nfs2_active       (ocf::pacemaker:attribute):     Started hanadb2

     Clone Set: SAPHanaTopology_HN1_03-clone [SAPHanaTopology_HN1_03]
         Started: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Master/Slave Set: SAPHana_HN1_03-master [SAPHana_HN1_03]
         Masters: [ hanadb2 ]
         Stopped: [ hanadb1 ]

     Resource Group: g_ip_HN1_03
         nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hanadb2
         vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hanadb2
    ```

   또한 [RHEL의 SAP HANA 시스템 복제 설정](./sap-hana-high-availability-rhel.md#test-the-cluster-setup)에 설명된 테스트를 수행하여 SAP HANA 클러스터 구성을 철저히 테스트하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md)
