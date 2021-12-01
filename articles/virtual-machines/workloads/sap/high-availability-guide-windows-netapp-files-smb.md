---
title: Azure NetApp Files(SMB)를 사용하는 Windows의 SAP NW용 Azure VM HA | Microsoft Docs
description: SAP 애플리케이션용 Azure NetApp Files(SMB)를 사용하는 Windows의 Azure VM에서 SAP NetWeaver에 대한 고가용성
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 07/29/2021
ms.author: radeltch
ms.openlocfilehash: efebeb328b61485edd28e138198d3f203427395c
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133367034"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>SAP 애플리케이션용 Azure NetApp Files(SMB)를 사용하는 Windows의 Azure VM에서 SAP NetWeaver에 대한 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md
[high-availability-guide]:high-availability-guide.md
[dfs-n-reference]:high-availability-guide-windows-dfs.md

[anf-azure-doc]:../../../azure-netapp-files/azure-netapp-files-introduction.md
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
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

이 문서에서는 [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md)에서 [SMB](/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)를 사용하여 가상 머신을 배포 및 구성하고, Windows VM에서 클러스터 프레임워크와 고가용성 SAP NetWeaver 7.50 시스템을 설치하는 방법을 설명합니다.  

데이터베이스 레이어는 이 문서에서 자세히 다루지 않습니다. Azure [가상 네트워크](../../../virtual-network/virtual-networks-overview.md)가 이미 생성되었다고 가정합니다.  

다음 SAP Note 및 문서를 먼저 읽어 보세요.

* [Azure NetApp Files 설명서][anf-azure-doc] 
* 다음을 포함하는 SAP Note [1928533][1928533]:  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows에 필요한 SAP 커널 버전
* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* SAP Note [2287140](https://launchpad.support.sap.com/#/notes/2287140)은 SMB 3.x 프로토콜의 SAP 지원 CA 기능에 대한 필수 조건을 나열합니다.
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770)에는 Windows 2012 및 2016에서 느리게 실행되는 SAP 트랜잭션 AL11에 대한 문제 해결 정보가 있습니다.
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507)에는 SMB 3.0 프로토콜을 사용하는 Windows Server의 파일 공유에 대한 투명한 장애 조치(failover) 기능에 대한 정보가 있습니다.
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452)에는 데이터 액세스 중 열악한 파일 시스템 성능/오류를 해결하기 위한 권장 사항(8.3 이름 생성 비활성화)이 있습니다.
* [Azure에서 SAP ASCS/SCS 인스턴스의 Windows 장애 조치(Failover) 클러스터 및 파일 공유에 SAP NetWeaver 고가용성 설치](./sap-high-availability-installation-wsfc-file-share.md) 
* [SAP NetWeaver에 대한 Azure Virtual Machines 고가용성 아키텍처 및 시나리오](./sap-high-availability-architecture-scenarios.md)
* [ASCS 클러스터 구성에서 프로브 포트 추가](sap-high-availability-installation-wsfc-file-share.md)
* [장애 조치(failover) 클러스터에 (A)SCS 인스턴스 설치](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [Azure NetApp Files에 대한 SMB 볼륨 만들기](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)
* [Azure NetApp Files를 사용하는 Microsoft Azure의 NetApp SAP 애플리케이션][anf-sap-applications-azure]

> [!IMPORTANT]
> 주의: [Azure NetApp Files][anf-azure-doc] SMB 볼륨에 호스트된 SMB 공유에 SWPM이 있는 SAP 시스템을 설치하려고 하면 "warningPerm이 정의되지 않았습니다."와 같은 권한 부족으로 인해 설치 오류가 발생할 수 있습니다. 이 오류를 방지하려면 SWPM이 실행되는 컨텍스트의 사용자에게 SAP 시스템을 설치하는 동안 상승된 권한의 "도메인 관리자"가 필요합니다.  

## <a name="overview"></a>개요

SAP는 Windows 장애 조치(Failover) 클러스터에 SAP ASCS/SCS 인스턴스를 클러스터링하기 위한 새로운 접근 방법을 개발했습니다. 이것은 클러스터 공유 디스크의 대안이 됩니다. 클러스터 공유 디스크를 사용하는 대신 SMB 파일 공유를 사용하여 SAP 글로벌 호스트 파일을 배포할 수 있습니다. Azure NetApp Files는 Active Directory를 사용하여 NTFS ACL이 있는 SMBv3(NFS와 함께)를 지원합니다. Azure NetApp Files는 PaaS 서비스이므로 자동으로 항상 사용 가능합니다. 이러한 기능을 통해 Azure NetApp Files는 SAP 글로벌용 SMB 파일 공유를 호스트하는 데 적합한 옵션이 되었습니다.  
[Azure AD(Active Directory) Domain Services](../../../active-directory-domain-services/overview.md) 및 [AD DS(Active Directory Domain Services)](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)가 모두 지원됩니다. 기존 Active Directory 도메인 컨트롤러는 Azure NetApp Files에서 사용할 수 있습니다. 도메인 컨트롤러는 Azure에서 가상 머신으로 사용되거나 ExpressRoute 또는 S2S VPN을 통해 온-프레미스에서 사용될 수 있습니다. 이 문서에서는 Azure VM에서 도메인 컨트롤러를 사용합니다.  
SAP Netweaver 중앙 서비스의 HA(고가용성)를 위해서는 공유 스토리지가 필요합니다. Windows에서 이러한 기능을 사용하기 위해 지금까지 SOFS 클러스터를 빌드하거나 SIOS와 같은 클러스터 공유 디스크 S/W를 사용해야 했습니다. 이제 Azure NetApp Files에 배포된 공유 스토리지를 사용하여 SAP Netweaver HA를 실현할 수 있습니다. 공유 스토리지에 Azure NetApp Files를 사용하면 SOFS 또는 SIOS가 필요 없습니다.  

> [!NOTE]
> 파일 공유를 사용한 SAP ASCS/SCS 인스턴스의 클러스터링은 SAP 커널 7.49 이상이 적용된 SAP NetWeaver 7.40 이상에서 지원됩니다.  

![SMB 공유를 사용하는 SAP ASCS/SCS HA 아키텍처](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 파일 공유에 대한 필수 구성 요소는 다음과 같습니다.
* SMB 3.0 이상의 프로토콜
* AD 사용자 그룹 및 computer$ 컴퓨터 개체에 대해 AD(Active Directory) ACL(액세스 제어 목록)을 설정할 수 있어야 합니다.
* 파일 공유에서 HA를 사용하도록 설정되어 있어야 합니다.

이 참조 아키텍처에서 SAP Central 서비스에 대한 공유는 Azure NetApp Files에서 제공됩니다.

![SMB 공유 세부 정보가 있는 SAP ASCS/SCS HA 아키텍처](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files용 SMB 볼륨 만들기 및 탑재

Azure NetApp Files를 사용하기 위한 준비 과정으로 다음 단계를 수행합니다.  

1. [NetApp 계정 만들기](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)에 설명된 단계에 따라 Azure NetApp 계정을 만듭니다.  
2. [용량 풀 설정](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)의 지침에 따라 용량 풀을 설정합니다.
3. Azure NetApp Files 리소스는 위임된 서브넷에 있어야 합니다. 위임된 서브넷을 만들려면 [Azure NetApp Files에 서브넷 위임](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)의 지침을 따릅니다.  

   > [!IMPORTANT]
   > SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. [Active Directory 연결에 대한 요구 사항](../../../azure-netapp-files/create-active-directory-connections.md#requirements-for-active-directory-connections)을 검토합니다.  
   >   
   > Active Directory 연결을 만들 때 SAP 애플리케이션에 대해 13자 호스트 이름 제한을 방지하기 위해 SMB 서버(컴퓨터 계정) 접두사를 8자 미만으로 입력해야 합니다. 접미사는 SMB 컴퓨터 계정 이름에 자동으로 추가됩니다.     
   > SAP 애플리케이션에 대한 호스트 이름 제한은 [2718300 - 실제 및 가상 호스트 이름 길이 제한](https://launchpad.support.sap.com/#/notes/2718300) 및 [611361 - SAP ABAP 플랫폼 서버의 호스트 이름](https://launchpad.support.sap.com/#/notes/611361)에 설명되어 있습니다.  

4. [Active Directory 연결 만들기](../../../azure-netapp-files/create-active-directory-connections.md#create-an-active-directory-connection)에 설명된 대로 Active Directory 연결을 만듭니다.  
5. [SMB 볼륨 추가](../../../azure-netapp-files/azure-netapp-files-create-volumes-smb.md#add-an-smb-volume)의 지침에 따라 SMB Azure NetApp Files SMB 볼륨을 만듭니다.  
6. Windows 가상 머신에 SMB 볼륨을 탑재합니다.

> [!TIP]
> [Azure Portal](https://portal.azure.com/#home)에서 Azure NetApp Files 개체로 이동하는 경우 **볼륨** 블레이드를 클릭한 다음, **탑재 지침** 을 클릭하여 Azure NetApp Files 볼륨을 탑재하는 방법에 대한 지침을 찾을 수 있습니다.  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>Windows 장애 조치(Failover) 클러스터를 사용하여 SAP HA용 인프라 준비 

1. [Azure 내부 Load Balancer에 대한 기본 ASCS/SCS 부하 분산 규칙을 설정합니다](./sap-high-availability-infrastructure-wsfc-shared-disk.md#fe0bd8b5-2b43-45e3-8295-80bee5415716).
2. [도메인에 Windows 가상 머신을 추가합니다](./sap-high-availability-infrastructure-wsfc-shared-disk.md#e69e9a34-4601-47a3-a41c-d2e11c626c0c).
3. [SAP ASCS/SCS 인스턴스의 두 클러스터 노드 모두에 레지스트리 항목 추가](./sap-high-availability-infrastructure-wsfc-shared-disk.md#661035b2-4d0f-4d31-86f8-dc0a50d78158).
4. [SAP ASCS/SCS 인스턴스에 대한 Windows Server 장애 조치 클러스터 설정](./sap-high-availability-infrastructure-wsfc-shared-disk.md#0d67f090-7928-43e0-8772-5ccbf8f59aab)
5. Windows Server 2016을 사용하는 경우 [Azure Cloud Witness](/windows-server/failover-clustering/deploy-cloud-witness)를 구성하는 것이 좋습니다.


## <a name="install-sap-ascs-instance-on-both-nodes"></a>두 노드에 SAP ASCS 인스턴스 설치

SAP의 다음 소프트웨어가 필요합니다.
   * SAP SWPM(Software Provisioning Manager) 설치 도구 버전 SPS25 이상.
   * SAP 커널 7.49 이상
   * [클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름 만들기](./sap-high-availability-installation-wsfc-shared-disk.md#a97ad604-9094-44fe-a364-f89cb39bf097)에 설명된 대로 클러스터형 SAP ASCS/SCS 인스턴스의 가상 호스트 이름(클러스터 네트워크 이름)을 만듭니다.

> [!NOTE]
> 파일 공유를 사용한 SAP ASCS/SCS 인스턴스의 클러스터링은 SAP 커널 7.49 이상이 적용된 SAP NetWeaver 7.40 이상에서 지원됩니다.  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>첫 번째 ASCS/SCS 클러스터 노드에 ASCS/SCS 인스턴스 설치

1. 첫 번째 클러스터 노드에 SAP ASCS/SCS 인스턴스를 설치합니다. SAP SWPM 설치 도구를 시작한 다음, **제품** > **DBMS** > 설치 > 애플리케이션 서버 ABAP(또는 Java) > 고가용성 시스템 > ASCS/SCS 인스턴스 > 첫 번째 클러스터 노드로 이동합니다.  

2. SWPM에서 클러스터 공유 구성으로 **파일 공유 클러스터** 를 선택합니다.  
3. **SAP 시스템 클러스터 매개 변수** 단계에서 메시지가 표시되면 **파일 공유 호스트 이름** 으로 이미 만든 Azure NetApp Files SMB 공유의 호스트 이름을 입력합니다.  이 예제에서 SMB 공유 호스트 이름은 **anfsmb-9562** 입니다. 

   > [!IMPORTANT]
   > SWPM의 필수 조건 검사 결과에서 지속적인 가용성 기능 조건이 충족되지 않음을 표시하는 경우 [Windows에 더 이상 존재하지 않는 공유 폴더에 액세스하려고 할 때 발생하는 지연 오류 메시지](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)의 지침에 따라 해결할 수 있습니다.  

   > [!TIP]
   > SWPM에서 필수 조건 검사 결과에서 스왑 크기 조건이 충족되지 않음을 표시하는 경우 내 컴퓨터>시스템 속성>성능 설정>고급>가상 메모리>변경으로 이동하여 스왑 크기를 조정할 수 있습니다.  

4. PowerShell을 사용하여 SAP 클러스터 리소스인 `SAP-SID-IP` 프로브 포트를 구성합니다. [프로브 포트 구성](./sap-high-availability-installation-wsfc-shared-disk.md#10822f4f-32e7-4871-b63a-9b86c76ce761)에 설명된 대로 SAP ASCS/SCS 클러스터 노드 중 하나에서 이 구성을 실행합니다.

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>두 번째 ASCS/SCS 클러스터 노드에 ASCS/SCS 인스턴스 설치

1. 두 번째 클러스터 노드에 SAP ASCS/SCS 인스턴스를 설치합니다. SAP SWPM 설치 도구를 시작한 다음, **제품** > **DBMS** > 설치 > 애플리케이션 서버 ABAP(또는 Java) > 고가용성 시스템 > ASCS/SCS 인스턴스 > 추가 클러스터 노드로 이동합니다.  

### <a name="update-the-sap-ascsscs-instance-profile"></a>SAP ASCS/SCS 인스턴스 프로필 업데이트

SAP ASCS/SCS 인스턴스 프로필 \<SID>_ASCS/SCS\<Nr>_ \<Host>에서 매개 변수를 업데이트합니다.


| 매개 변수 이름 | 매개 변수 값 |
| --- | --- |
| gw/netstat_once | **0** |
| enque/encni/set_so_keepalive  | **true** |
| service/ha_check_node | **1** |

매개 변수 `enque/encni/set_so_keepalive`는 ENSA1을 사용하는 경우에만 필요합니다.  
SAP ASCS/SCS 인스턴스를 다시 시작합니다. [SAP ASCS/SCS 인스턴스의 클러스터 노드에 대한 레지스트리 항목 설정][high-availability-guide] 지침을 따라 두 SAP ASCS/SCS 클러스터 노드에서 `KeepAlive` 매개 변수를 설정합니다. 

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>DBMS 인스턴스 및 SAP 애플리케이션 서버 설치

다음을 설치하여 SAP 설치를 완료합니다.

   * DBMS 인스턴스  
   * 기본 SAP 애플리케이션 서버  
   * 추가 SAP 애플리케이션 서버  

## <a name="test-the-sap-ascsscs-instance-failover"></a>SAP ASCS/SCS 인스턴스 장애 조치(failover) 테스트 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>클러스터 노드 A에서 클러스터 노드 B로 장애 조치 및 반대로 장애 조치
이 테스트 시나리오에서는 클러스터 노드 sapascs1을 노드 A로, 클러스터 노드 sapascs2를 노드 B로 나타냅니다.

1. 클러스터 리소스가 노드 A에서 실행되고 있는지 확인합니다. ![그림 1: 장애 조치 테스트 전에 노드 A에서 실행되는 Windows Server 장애 조치 클러스터 리소스](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 클러스터 노드 A를 다시 시작합니다. SAP 클러스터 리소스는 클러스터 노드 B로 이동합니다. ![그림 2: 장애 조치 테스트 후 노드 B에서 실행되는 Windows Server 장애 조치 클러스터 리소스](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>잠금 항목 테스트

1. SAP ERS(Enqueue Replication Server)가 활성 상태인지 확인  
2. SAP 시스템에 로그온하고, 트랜잭션 SU01을 실행하고, 변경 모드에서 사용자 ID를 엽니다. 이렇게 하면 SAP 잠금 항목이 생성됩니다.  
3. SAP 시스템에 로그인하면 트랜잭션 ST12로 이동하여 잠금 항목을 표시합니다.  
4. 클러스터 노드 A에서 클러스터 노드 B로 ASCS 리소스를 장애 조치합니다.  
5. SAP ASCS/SCS 클러스터 리소스 장애 조치가 유지되기 전에 생성된 잠금 항목이 유지되는지 확인합니다.  

![그림 3: 장애 조치 테스트 후 잠금 항목 유지](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

자세한 내용은 [ERS를 통해 ASCS에서 장애 조치 큐에 넣기 문제 해결](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)을 참조하세요.

## <a name="optional-configurations"></a>선택적 구성

다음 다이어그램은 총 VM 수를 줄이기 위해 Microsoft Windows 장애 조치(failover) 클러스터를 실행하는 Azure VM의 여러 SAP 인스턴스를 보여 줍니다.

SAP ASCS/SCS 클러스터의 로컬 SAP 애플리케이션 서버 또는 Microsoft SQL Server Always On 노드의 SAP ASCS/SCS 클러스터 역할일 수 있습니다.

> [!IMPORTANT]
> SQL Server Always On 노드에 로컬 SAP 애플리케이션 서버를 설치할 수는 없습니다.
>

SAP ASCS/SCS와 Microsoft SQL Server 데이터베이스는 모두 SPOF(단일 실패 지점)입니다. Windows 환경에서 이러한 SPOF를 보호하기 위해 Azure NetApp Files SMB가 사용됩니다.

SAP ASCS/SCS의 리소스 사용량은 매우 작지만 SQL Server 또는 SAP Application Server에 대한 메모리 구성을 2GB씩 줄이는 것이 좋습니다.

### <a name="sap-application-servers-on-wsfc-nodes-using-netapp-files-smb"></a><a name="5121771a-7618-4f36-ae14-ccf9ee5f2031"></a>NetApp Files SMB를 사용하는 WSFC 노드의 SAP 애플리케이션 서버

![그림 4: Windows NetApp Files SMB 및 로컬로 설치된 SAP Application Server를 사용하여 Azure에서 Windows Server 장애 조치 클러스터링 구성][sap-ha-guide-figure-8007A]

> [!NOTE]
> 이 그림은 추가 로컬 디스크의 사용을 보여 줍니다. 이것은 OS 드라이브에 애플리케이션 소프트웨어를 설치하지 않는 고객의 경우 선택 사항입니다(C:\)
>
### <a name="sap-ascsscs-on-sql-server-always-on-nodes-using-azure-netapp-files-smb"></a><a name="01541cf2-0a03-48e3-971e-e03575fa7b4f"></a> Azure NetApp Files SMB를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS

> [!IMPORTANT]
> SQL Server 볼륨에 Azure NetApp Files SMB를 사용하는 것은 지원되지 않습니다.
> 

![그림: Azure NetApp Files SMB를 사용하는 SQL Server Always On 노드의 SAP ASCS/SCS][sap-ha-guide-figure-8007B]

> [!NOTE]
> 이 그림은 추가 로컬 디스크의 사용을 보여 줍니다. OS 드라이브(C:\)에 애플리케이션 소프트웨어를 설치하지 않을 고객의 경우 선택 사항입니다.
>
### <a name="using-windows-dfs-n-to-support-flexible-sapmnt-share-creation-for-smb-based-file-share"></a>Windows DFS-N을 사용 하 여 SMB 기반 파일 공유에 대 한 유연한 SAPMNT 공유 만들기 지원
DFS-N을 사용 하면 동일한 Azure 지역 및 구독 내에 배포 된 SAP 시스템에 대해 개별 sapmnt 볼륨을 활용할 수 있습니다. 이 설치에 대 한 설명서는 [여기][dfs-n-reference] 에서 찾을 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure(대규모 인스턴스)에서 SAP HANA의 재해 복구 계획 및 고가용성 계획을 수립하려면 
* [Azure의 SAP HANA(대규모 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md)를 참조하세요.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.

[sap-ha-guide-figure-8007A]:./media/virtual-machines-shared-sap-high-availability-guide/ha-smb-as.png
[sap-ha-guide-figure-8007B]:./media/virtual-machines-shared-sap-high-availability-guide/ha-sql-ascs-smb.png