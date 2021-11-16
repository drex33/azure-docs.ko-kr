---
title: Azure Files에서 NFS를 사용 하는 RHEL on SAP NW의 Azure Vm 고가용성 Microsoft Docs
description: Azure Files에서 NFS를 사용 하는 Azure Vm (가상 머신) RHEL의 SAP NW에 대해 고가용성을 설정 합니다.
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
tags: azure-resource-manager
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/16/2021
ms.author: radeltch
ms.openlocfilehash: badc6696936b3cdf99b91a5abf0d95217ec9b2e7
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132556960"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-with-nfs-on-azure-files"></a>Azure Files에서 NFS를 사용 하는 Red Hat Enterprise Linux에서 Azure vm의 SAP NetWeaver에 대 한 고가용성

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2772999]:https://launchpad.support.sap.com/#/notes/2772999
[2009879]:https://launchpad.support.sap.com/#/notes/2009879
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html


[sap-hana-ha]:sap-hana-high-availability-rhel.md


이 문서에서는 [Azure Files에서 NFS](../../../storage/files/files-nfs-protocol.md)를 사용 하 여 vm을 배포 및 구성 하 고, 클러스터 프레임 워크를 설치 하 고, HA SAP NetWeaver 시스템을 설치 하는 방법을 설명 합니다. 예제 구성은 Red Hat Enterprise Linux (RHEL)에서 실행 되는 vm을 사용 합니다.  

## <a name="prerequisites"></a>필수 조건

* [Azure Files 설명서][afs-azure-doc] 
* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2002167] 에는 Red Hat Enterprise Linux 8.x에 대해 권장 되는 OS 설정이 있습니다.
* SAP Note [2772999] 에 Red Hat Enterprise Linux .x에 대 한 권장 OS 설정이 있습니다.
* SAP Note [2009879]에는 Red Hat Enterprise Linux용 SAP HANA 지침이 있습니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [pacemaker 클러스터의 SAP Netweaver](https://access.redhat.com/articles/3150081)
* 일반 RHEL 설명서
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)(고가용성 추가 기능 개요)
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)(고가용성 추가 기능 관리)
  * [High Availability Add-On Reference](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)(고가용성 추가 기능 참조)
  * [RHEL 7.5에서 독립 실행형 리소스를 사용하여 SAP Netweaver용 ASCS/ERS 구성](https://access.redhat.com/articles/3569681)
  * [RHEL의 Pacemaker에서 독립 실행형 큐에 넣기 서버 2(ENSA2)를 사용하여 SAP S/4HANA ASCS/ERS 구성](https://access.redhat.com/articles/3974941)
* Azure 특정 RHEL 설명서:
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)(RHEL 고가용성 클러스터용 지원 정책 - Microsoft Azure Virtual Machines(클러스터 멤버))
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)(Microsoft Azure에서 Red Hat Enterprise Linux 7.4 이상 고가용성 클러스터 설치 및 구성)

## <a name="overview"></a>개요

SAP NetWeaver 응용 프로그램 계층을 배포 하려면 및와 같은 공유 디렉터리가 `/sapmnt/SID` `/usr/sap/trans` 환경에 있어야 합니다. 또한 HA SAP 시스템을 배포할 때 및와 같은 항상 사용 가능한 파일 시스템을 보호 하 고 사용 하도록 설정 해야 `/sapmnt/SID` `/usr/sap/SID/ASCS` 합니다.

이제 [Azure Files에서 NFS](../../../storage/files/files-nfs-protocol.md)에 이러한 파일 시스템을 저장할 수 있습니다. Azure Files의 NFS는 HA 저장소 솔루션입니다. 이 솔루션은 ZRS (동기 영역 중복 저장소)를 제공 하며 가용성 영역에 배포 된 SAP ASCS/ERS 인스턴스에 적합 합니다.  Netweaver 중앙 서비스 (ASCS/SCS)와 같은 단일 실패 지점 구성 요소를 보호 하려면 여전히 Pacemaker 클러스터가 필요 합니다.  

구성 및 설치 명령 예제에는 다음 인스턴스 번호가 사용 됩니다.

| 인스턴스 이름 | 인스턴스 번호 |
| ---------------- | ------------------ |
| ABAP SAP Central Services (ascs) | 00 |
| ERS | 01 |
| PAS (기본 응용 프로그램 서버) | 02 |
| 추가 응용 프로그램 서버 (.AAS) | 03 |
| SAP 시스템 식별자 | N W 1 |

:::image type="complex" source="./media/high-availability-guide-rhel/high-availability-guide-rhel-nfs-azure-files.png" alt-text="Azure Files에서 NFS를 사용 하는 SAP NetWeaver 고가용성":::
   이 다이어그램은 일반적인 SAP Netweaver HA 아키텍처를 보여 줍니다. "Sapmnt" 및 "saptrans" 파일 시스템은 Azure Files에서 NFS 공유에 배포 됩니다. SAP central services는 Pacemaker 클러스터에 의해 보호 됩니다. 클러스터 된 Vm은 Azure 부하 분산 장치 뒤에 있습니다. NFS 공유는 전용 끝점을 통해 탑재 됩니다.
:::image-end:::

## <a name="prepare-infrastructure"></a>인프라 준비

이 문서에서는 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), 서브넷 및 리소스 그룹을 이미 배포 했다고 가정 합니다.

1. Vm을 배포 합니다. Azure 지역에서 이러한 옵션을 지 원하는 경우 가용성 집합 또는 가용성 영역에서 Vm을 배포할 수 있습니다. Vm에 대 한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포 및 연결 합니다. 주 NIC에 보조 IP 주소를 추가 하지 마세요. [Azure Load Balancer 부동 IP는이 시나리오를 지원 하지 않습니다](../../../load-balancer/load-balancer-multivip-overview.md#limitations).  
 
2. 가상 Ip의 경우 Azure [부하 분산 장치](../../../load-balancer/load-balancer-overview.md)를 배포 하 고 구성 합니다. [표준 부하 분산 장치](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)를 사용 하는 것이 좋습니다. 

    1. 두 개의 프런트 엔드 Ip를 구성 합니다. 하나는 ASCS ( `10.90.90.10` )이 고 다른 하나는 ERS ()입니다. `10.90.90.9`
    2. 백 엔드 풀을 만들고 클러스터에 포함 될 두 Vm을 추가 합니다.
    3. ASCS에 대 한 상태 프로브를 만듭니다. 프로브 포트는 `62000` 입니다. ERS에 대 한 프로브 포트를 만듭니다. ERS 프로브 포트는 `62101` 입니다. 나중에 Pacemaker 리소스를 구성 하는 경우 일치 하는 프로브 포트를 사용 해야 합니다.
    4. ASCS 및 ERS에 대 한 부하 분산 규칙을 구성 합니다. 해당 하는 프런트 Ip, 상태 프로브 및 백 엔드 풀을 선택 합니다. HA 포트를 선택 하 고 유휴 시간 제한을 30 분으로 늘리고 부동 IP를 사용 하도록 설정 합니다.

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>저장소 계정 및 NFS 공유 Azure Files 배포 

Azure Files의 NFS는 [Azure Files Premium 저장소][afs-azure-doc]를 기반으로 실행 됩니다. Azure Files에서 NFS를 설정 하기 전에 [nfs 공유를 만드는 방법](../../../storage/files/storage-files-how-to-create-nfs-shares.md?tabs=azure-portal)을 참조 하세요.    

Azure 지역 내에서 중복성을 위한 두 가지 옵션이 있습니다.

- 로컬 [중복 저장소 (LRS)](../../../storage/common/storage-redundancy.md#locally-redundant-storage)-로컬 영역 동기 데이터 복제를 제공 합니다.
- 영역 [중복 저장소 (ZRS)](../../../storage/common/storage-redundancy.md#zone-redundant-storage)-지역의 3 개 [가용성 영역](../../../availability-zones/az-overview.md) 에서 데이터를 동기적으로 복제 합니다.

선택한 Azure 지역에서 적절 한 중복성을 사용 하 여 Azure Files에 NFS 4.1을 제공 하는지 확인 합니다. **Premium 파일 Storage** 에서 [Azure 지역별 Azure Files 가용성][afs-avail-matrix] 을 검토 합니다. 시나리오에서 ZRS을 활용 하 [Premium는 경우 ZRS와 파일 공유가 Azure 지역에서 지원 되는지 확인](../../../storage/common/storage-redundancy.md#zone-redundant-storage)합니다.

[Azure 개인 끝점](../../../storage/files/storage-files-networking-endpoints.md?tabs=azure-portal)을 통해 Azure Storage 계정에 액세스 하는 것이 좋습니다. 동일한 Azure VNet 또는 피어 링 Azure Vnet에서 NFS 공유를 탑재 해야 하는 Azure Files storage 계정 끝점 및 Vm을 배포 해야 합니다.

1. 이라는 File Storage 계정을 배포 `sapafsnfs` 합니다. 이 예제에서는 ZRS를 사용 합니다. 프로세스에 익숙하지 않은 경우 Azure Portal [저장소 계정 만들기](../../../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#create-a-storage-account) 를 참조 하세요.
1. **기본 사항** 탭에서 다음 설정을 사용 합니다.

    1. **Storage 계정 이름** 에을 입력 `sapafsnfs` 합니다.
    
    1. **성능을** 위해 **Premium** 를 선택 합니다.
    
    1. **계정 유형 Premium** **FileStorage** 를 선택 합니다.
    
    1. **복제** 에 대해 영역 중복 (ZRS)을 선택 합니다.

1. **다음** 을 선택합니다.

1. **고급** 탭에서 **REST API 작업에 대 한 보안 전송 필요** 를 선택 취소 합니다. 이 옵션의 선택을 취소 하지 않으면 NFS 공유를 VM에 탑재할 수 없습니다. 탑재 작업은 시간 초과 됩니다.

1. **다음** 을 선택합니다.

1. **네트워킹** 섹션에서 다음 설정을 구성 합니다. 

    1. **네트워킹 연결** 아래에서 **연결 방법** 으로 **개인 끝점** 을 선택 합니다.  
    
    1. **개인 끝점** 에서 **추가 개인 끝점** 을 선택 합니다.
    
1. **개인 끝점 만들기** 창에서 **구독**, **리소스 그룹** 및 **위치** 를 선택 합니다. 
    
    **이름** 에 `sapafsnfs_pe`를 입력합니다.
        
    **Storage 하위 리소스** 에 대해 **파일** 을 선택 합니다.
        
    **네트워킹** 에서 **가상 네트워크** 에 대해 사용할 VNet 및 서브넷을 선택 합니다. 다시, SAP Vm이 있는 VNet 또는 피어 링 VNet을 사용할 수 있습니다.
       
    **사설 DNS 통합** 에서 **개인 DNS 영역과 통합** 에 대해 기본 옵션인 **예** 를 그대로 사용 합니다. **사설 DNS 영역** 을 선택 해야 합니다.
        
    **확인** 을 선택합니다.
        
1. **네트워킹** 탭에서 **다음** 을 선택 합니다.

1. **데이터 보호** 탭에서 모든 기본 설정을 그대로 유지 합니다. 

1. **검토 + 만들기** 를 선택 하 여 구성의 유효성을 검사 합니다.

1. 유효성 검사가 완료 될 때까지 기다립니다. 계속 하기 전에 문제를 해결 하십시오.

1. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.


그런 다음 만든 저장소 계정에 NFS 공유를 배포 합니다. 이 예제에는 및 라는 두 개의 NFS 공유가 `sapnw1` 있습니다 `saptrans` .    
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **Storage 계정을** 선택 하거나 검색 합니다. 

1. **Storage 계정** 페이지에서 **sapafsnfs** 를 선택 합니다.

1.  **Sapafsnfs** 의 리소스 메뉴에서 **데이터 저장소** 아래에 있는 **파일 공유** 를 선택 합니다.

1. **파일 공유** 페이지에서 **파일 공유** 를 선택 합니다.

   1. **이름** 에 `sapnw1` ,를 입력 `saptrans` 합니다.
   1.  적절 한 공유 크기를 선택 합니다. 예를 들면 **128 GB** 입니다.  공유, IOPs 및 처리량 요구 사항에 저장 된 데이터의 크기를 고려 합니다.  자세한 내용은 [Azure 파일 공유 대상](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)을 참조 하세요.
   1. 프로토콜로 **NFS** 를 선택 합니다.
   1. **Root Squash 안 함** 을 선택 합니다.  그렇지 않으면 Vm에 공유를 탑재할 때 파일 소유자 또는 그룹을 볼 수 없습니다.

   > [!IMPORTANT]
   > 위의 공유 크기는 한 가지 예입니다. 공유의 크기를 적절 하 게 조정 해야 합니다. 크기는 공유에 저장 된 데이터의 크기 뿐만 아니라 IOPS 및 처리량에 대 한 요구 사항에 따라 달라 집니다. 자세한 내용은 [Azure 파일 공유 대상](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)을 참조 하세요.  

   NFS를 통해 탑재 하지 않아도 되는 SAP 파일 시스템을 [Azure disk storage](../../disks-types.md#premium-ssds)에 배포할 수도 있습니다. 이 예제에서는 `/usr/sap/NW1/D02` `/usr/sap/NW1/D03` Azure 디스크 저장소에 및을 배포할 수 있습니다. 

### <a name="important-considerations-for-nfs-on-azure-files-shares"></a>Azure Files 공유의 NFS에 대 한 중요 고려 사항

Azure Files에서 NFS를 사용 하 여 배포를 계획 하는 경우 다음과 같은 중요 한 사항을 고려해 야 합니다.  

- 최소 공유 크기는 100 GiB입니다. [프로 비전 된 공유의 용량](../../../storage/files/understanding-billing.md#provisioned-model) 에 대해서만 비용을 지불 합니다. 
- 용량 요구 사항 뿐만 아니라 IOPS 및 처리량 요구 사항에 따라 NFS 공유의 크기를 조정 합니다. 자세한 내용은 [Azure 파일 공유 대상](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets) 을 참조 하세요.
- 워크 로드를 테스트 하 여 크기 조정의 유효성을 검사 하 고 성능 목표를 충족 하는지 확인 합니다. Azure Files에서 NFS를 사용 하 여 성능 문제를 해결 하는 방법을 알아보려면 [Azure 파일 공유 성능 문제 해결](../../../storage/files/storage-troubleshooting-files-performance.md) 을 참조 하세요.
- SAP J2EE 시스템의 경우 Azure Files에 NFS를 사용할 수 없습니다 `/usr/sap/<SID>` .
- SAP 시스템에 많은 일괄 작업 부하가 있는 경우 수백만 개의 작업 로그가 있을 수 있습니다. SAP batch 작업 로그가 파일 시스템에 저장 된 경우 공유 크기 조정에 특히 주의 해야 합니다 `sapmnt` . SAP_BASIS 7.52에서 일괄 처리 작업 로그의 기본 동작은 데이터베이스에 저장 됩니다. 자세한 내용은 [데이터베이스의 작업 로그][2360818]를 참조 하세요.     
- `sapmnt`각 SAP 시스템에 대해 별도의 공유 배포
- `sapmnt`인터페이스와 같은 다른 작업에는 공유를 사용 하지 마세요.`saptrans`
- `saptrans`인터페이스와 같은 다른 작업에는 공유를 사용 하지 마세요.`sapmnt`
- 단일 저장소 계정에 너무 많은 SAP 시스템에 대 한 공유를 통합 하지 마십시오. [계정 성능 확장 대상도 Storage](../../../storage/files/storage-files-scale-targets.md#storage-account-scale-targets)있습니다. 저장소 계정에 대 한 한도를 초과 하지 않도록 주의 해야 합니다.
- 일반적으로는 단일 저장소 계정에 5 개 이상의 SAP 시스템에 대 한 공유를 통합 하지 마세요. 이 지침은 저장소 계정 제한을 초과 하는 것을 방지 하 고 성능 분석을 간소화 합니다.   
- 일반적으로 동일한 저장소 계정에서 비프로덕션 및 프로덕션 SAP 시스템에 대 한 공유를 혼합 하지 마세요.
- [NFS 클라이언트 향상 기능](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels)을 활용 하기 위해 RHEL 8.4 이상에를 배포 하는 것이 좋습니다.   
- 개인 끝점을 사용 합니다. 영역 오류가 발생할 경우에는 NFS 세션이 자동으로 정상 영역으로 리디렉션됩니다. Vm에서 NFS 공유를 다시 탑재할 필요가 없습니다.
- 가용성 영역에서 vm을 배포 하는 경우 ZRS를 지 원하는 Azure 지역에서 [ZRS와 함께 Storage 계정을](../../../storage/common/storage-redundancy.md#zone-redundant-storage) 사용 합니다. 
- Azure Files는 현재 재해 복구 시나리오에 대 한 자동 지역 간 복제를 지원 하지 않습니다.

## <a name="setting-up-ascs"></a>(A)SCS 설정

이 예제에서는 [Azure Portal](https://portal.azure.com/#home)를 통해 리소스를 수동으로 배포 합니다.

### <a name="deploy-azure-load-balancer-via-azure-portal"></a>Azure Portal를 통해 Azure Load Balancer 배포

SAP 시스템용 Vm을 배포한 후에 부하 분산 장치를 만듭니다. 그런 다음 백 엔드 풀의 Vm을 사용 합니다.

1. 내부 표준 부하 분산 장치를 만듭니다.
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS에 대 한 IP 주소 10.90.90.10
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예: **프런트 엔드). N w 1. ASCS**)
         1. 할당을 정적으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.90.90.10**).
         1. 확인을 클릭합니다.
      1. ASCS ERS에 대 한 IP 주소 10.90.90.9
         * "A"에서 위의 단계를 반복 하 여 **10.90.90.9** 및 프런트 엔드와 같은 사람에 대 한 IP 주소를 만듭니다 **. N w 1. ERS**)
   1. 백 엔드 풀 만들기
      1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
      1. 새 백 엔드 풀의 이름을 입력 합니다 (예: **백 엔드). N W 1**)
      1. 가상 머신 추가 클릭
      1. 가상 머신 선택
      1. (A)SCS 클러스터의 가상 머신 및 해당 IP 주소 선택
      1. 추가를 클릭합니다.  
      
   1. 상태 프로브 만들기
      1. 포트: 620 **00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브 (예: 상태)의 이름을 입력 합니다 **. N w 1. ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620 **00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS용 포트 621 **01**
            * "C"에서 위의 단계를 반복 하 여 해당 ERS에 대 한 상태 프로브를 만듭니다 (예: 621 **01** 및 **health). N w 1. ERS**)
   1. 부하 분산 규칙
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정하고, 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름 (예: lb)을 입력 합니다 **. N w 1. ASCS**)
         1. 이전에 만든 ASCS, 백 엔드 풀 및 상태 프로브에 대 한 프런트 엔드 IP 주소를 선택 합니다 (예: **프런트 엔드). N w 1. ASCS**, **백 엔드. N W 1** 및 **상태. N w 1. ASCS**)
         1. **HA 포트** 선택
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
         * 위의 단계를 반복 하 여 ERS에 대 한 부하 분산 규칙을 만듭니다 (예: **lb. N w 1. ERS**)
1. 또는 시나리오에 기본 부하 분산 장치(내부)가 필요한 경우 다음 단계를 수행합니다.  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS에 대 한 IP 주소 10.90.90.10
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름을 입력 합니다 (예: **프런트 엔드). N w 1. ASCS**)
         1. 할당을 정적으로 설정 하 고 IP 주소를 입력 합니다 (예: **10.90.90.10**).
         1. 확인을 클릭합니다.
      1. ASCS ERS에 대 한 IP 주소 10.90.90.9
         * "A"에서 위의 단계를 반복 하 여 **10.90.90.9** 및 프런트 엔드와 같은 사람에 대 한 IP 주소를 만듭니다 **. N w 1. ERS**)
   1. 백 엔드 풀 만들기
      1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
      1. 새 백 엔드 풀의 이름을 입력 합니다 (예: **백 엔드). N W 1**)
      1. 가상 머신 추가 클릭
      1. ASCS에 대해 이전에 만든 가용성 집합 선택 
      1. (A)SCS 클러스터의 가상 머신 선택
      1. 확인 클릭
   1. 상태 프로브 만들기
      1. 포트: 620 **00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브 (예: 상태)의 이름을 입력 합니다 **. N w 1. ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620 **00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS용 포트 621 **01**
            * "C"에서 위의 단계를 반복 하 여 해당 ERS에 대 한 상태 프로브를 만듭니다 (예: 621 **01** 및 **health). N w 1. ERS**)
   1. 부하 분산 규칙
      1. TCP: 32 **00**(ASCS용)
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정하고, 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름 (예: lb)을 입력 합니다 **. N w 1. ASCS. 3200**)
         1. 이전에 만든 ASCS, 백 엔드 풀 및 상태 프로브에 대 한 프런트 엔드 IP 주소를 선택 합니다 (예: **프런트 엔드). N w 1. ASCS**)
         1. 프로토콜로 **TCP** 를 유지하고. 포트로 **3200** 입력
         1. 유휴 상태 시간 제한을 30분으로 증가
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
      1. ASCS에 대한 추가 포트
         * 포트 36 **00**, 39 **00**, 81 **00**, 5 **00** 13, 5 **00** 14, 5 **00** 16 및 ASCS용 TCP에 대해 위의 "d" 단계를 반복
      1. ASCS ERS에 대한 추가 포트
         * 포트 32 **01**, 33 **01**, 5 **01** 13, 5 **01** 14, 5 **01** 16 및 ASCS ERS용 TCP에 대해 위의 "d" 단계를 반복

      
      > [!IMPORTANT]
      > 부동 IP는 부하 분산 시나리오의 NIC 보조 IP 구성에서 지원되지 않습니다. 자세한 내용은 [Azure Load Balancer 제한 사항](../../../load-balancer/load-balancer-multivip-overview.md#limitations)을 참조하세요. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포합니다.  

      > [!Note]
      > 공용 IP 주소가 없는 VM이 내부(공용 IP 주소 없음) 표준 Azure 부하 분산 장치의 백 엔드 풀에 배치되는 경우 퍼블릭 엔드포인트로 라우팅을 허용하기 위해 추가 구성을 수행하지 않는 한 아웃바운드 인터넷 연결이 없습니다. 아웃바운드 연결을 설정하는 방법에 대한 자세한 내용은 [SAP 고가용성 시나리오에서 Azure 표준 Load Balancer를 사용하는 Virtual Machines에 대한 퍼블릭 엔드포인트 연결](./high-availability-guide-standard-load-balancer-outbound-connections.md)을 참조하세요.  

      > [!IMPORTANT]
      > Azure Load Balancer 뒤에 배치되는 Azure VM에서 TCP 타임스탬프를 사용하도록 설정하면 안 됩니다. TCP 타임스탬프를 사용하도록 설정하면 상태 프로브에 오류가 발생합니다. 매개 변수 **net.ipv4.tcp_timestamps** 를 **0** 으로 설정합니다. 자세한 내용은 [Load Balancer 상태 프로브](../../../load-balancer/load-balancer-custom-probe-overview.md)를 참조하세요.


### <a name="create-pacemaker-cluster"></a>Pacemaker 클러스터 만들기

[Azure의 Red Hat Enterprise Linux에서 Pacemaker 설정](high-availability-guide-rhel-pacemaker.md) 단계에 따라 이 (A)SCS 서버용 기본 Pacemaker 클러스터를 만듭니다.

### <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

    ```bash
    sudo vi /etc/hosts
    ```

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

    ```bash
     # IP address of cluster node 1
     10.90.90.7    sap-cl1
     # IP address of cluster node 2
     10.90.90.8     sap-cl2
     # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
     10.90.90.10   sapascs
     # IP address of the load balancer frontend configuration for SAP Netweaver ERS
     10.90.90.9    sapers
    ```

2. **[A]** NFS 클라이언트 및 기타 요구 사항 설치

    ```bash
    sudo yum -y install nfs-utils resource-agents resource-agents-sap
    ```

3. **[1]** NFS 공유에 SAP 디렉터리를 만듭니다.  
   일시적으로 NFS 공유를 탑재 하 여 Vm 중 하나를 **sapnw1** 중첩 된 탑재 점으로 사용할 SAP 디렉터리를 만듭니다.  

    ```bash
    # mount temporarily the volume
    sudo mkdir -p /saptmp
    sudo mount -t nfs sapnfs.file.core.windows.net:/sapnfsafs/sapnw1 /saptmp -o vers=4,minorversion=1,sec=sys
    # create the SAP directories
    sudo cd /saptmp
    sudo mkdir -p sapmntNW1
    sudo mkdir -p usrsapNW1ascs
    sudo mkdir -p usrsapNW1ers
    sudo mkdir -p usrsapNW1sys
    # unmount the volume and delete the temporary directory
    cd ..
    sudo umount /saptmp
    sudo rmdir /saptmp
    ``` 

4. **[A]** 공유 디렉터리 만들기

    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans
    sudo mkdir -p /usr/sap/NW1/SYS
    sudo mkdir -p /usr/sap/NW1/ASCS00
    sudo mkdir -p /usr/sap/NW1/ERS01
    
    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
    sudo chattr +i /usr/sap/NW1/SYS
    sudo chattr +i /usr/sap/NW1/ASCS00
    sudo chattr +i /usr/sap/NW1/ERS01
    ```

5. **[A]** 리소스-에이전트-sap 버전 확인

   설치된 리소스-에이전트-sap 패키지 버전이 3.9.5-124.el7 이상인지 확인
    ```
    sudo yum info resource-agents-sap
   ```


6. **[A]** 탑재 항목 추가

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

7. **[A]** 스왑 파일 구성

    ```bash
    sudo vi /etc/waagent.conf
    
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
    
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

    ```bash
    sudo service waagent restart
    ```

8. **[A]** RHEL 구성

   RHEL 2002167에 대 한 SAP Note [] 또는 RHEL 8.X 용 sap note [2772999] 에 설명 된 대로 RHEL를 구성 합니다.  

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS 설치

1. **[1]** 클러스터 기본 속성 구성

    ```bash
    # If using RHEL 7.X
    pcs resource defaults resource-stickiness=1
    pcs resource defaults migration-threshold=3
    # If using RHEL 8.X
    pcs resource defaults update resource-stickiness=1
    pcs resource defaults update migration-threshold=3
    ```

1. **[1]** ASCS 인스턴스에 대한 가상 IP 리소스 및 상태 프로브 만들기

    ```bash
    sudo pcs node standby sap-cl2
   
    sudo pcs resource create fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' \
      directory='/usr/sap/NW1/ASCS00' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
      --group g-NW1_ASCS
   
    sudo pcs resource create vip_NW1_ASCS IPaddr2 \
      ip=10.90.90.10 cidr_netmask=24 \
      --group g-NW1_ASCS
   
    sudo pcs resource create nc_NW1_ASCS azure-lb port=62000 \
      --group g-NW1_ASCS
    ```

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```bash
    sudo pcs status
    
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl1
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1 
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    ```

1. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소 (예: **sapascs**, **10.90.90.10** 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호 (예: **00**)에 매핑되는 가상 호스트 이름을 사용 하 여 첫 번째 노드에 SAP NetWeaver ascs를 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

    설치에서 /usr/sap/**NW1**/ASCS **00** 에 하위 폴더를 만들지 못하면 ASCS **00** 폴더의 소유자와 그룹을 설정하고 다시 시도합니다.

    ```bash
    sudo chown nw1adm /usr/sap/NW1/ASCS00
    sudo chgrp sapsys /usr/sap/NW1/ASCS00
    ```

1. **[1]** ERS 인스턴스에 대한 가상 IP 리소스 및 상태 검사 프로브 만들기

    ```bash
    sudo pcs node unstandby sap-cl2
    sudo pcs node standby sap-cl1
    
    sudo pcs resource create fs_NW1_AERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' \
      directory='/usr/sap/NW1/ERS01' fstype='nfs' force_unmount=safe options='sec=sys,vers=4.1' \
      op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW1_AERS
   
    sudo pcs resource create vip_NW1_AERS IPaddr2 \
      ip=10.90.90.9 cidr_netmask=24 \
     --group g-NW1_AERS
   
    sudo pcs resource create nc_NW1_AERS azure-lb port=62101 \
     --group g-NW1_AERS
    ```
 
   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```bash
    sudo pcs status
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    ```

1. **[2]** SAP NetWeaver ERS 설치  

   사용자에 대 한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름 (예: **sapers**, **10.90.90.9** 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호 (예: **01**)를 사용 하 여 두 번째 노드에 SAP NetWeaver ERS를 루트로 설치 합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

    ```bash
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
    sudo firewall-cmd --zone=public  --add-port=4237/tcp

    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   /Usr/sap/**n w 1**/ers **01** 에 하위 폴더를 만들지 못한 경우에는 ers **01** 폴더의 소유자와 그룹을 설정 하 고 다시 시도 하세요.

    ```
    sudo chown qaadm /usr/sap/NW1/ERS01
    sudo chgrp sapsys /usr/sap/NW1/ERS01
    ```

1. **[1]** ASCS/SCS 및 ERS 인스턴스 프로필 조정

   * ASCS/SCS 프로필

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
   
    # Change the restart command to a start command
    #Restart_Program_01 = local $(_EN) pf=$(_PF)
    Start_Program_01 = local $(_EN) pf=$(_PF)
   
    # Add the keep alive parameter, if using ENSA1
    enque/encni/set_so_keepalive = true
    ```

   ENSA1 및 ENSA2 모두에 대해 `keepalive` OS 매개 변수가 SAP note [1410736](https://launchpad.support.sap.com/#/notes/1410736)에서 설명한 대로 설정되어 있는지 확인합니다.  

   * ERS 프로필

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ERS01_sapers
   
    # Change the restart command to a start command
    #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

1. **[A]** 연결 유지 구성

   SAP NetWeaver 애플리케이션 서버와 ASCS/SCS 간의 통신은 소프트웨어 부하 분산 장치를 통해 라우팅됩니다. 부하 분산 장치는 구성 가능한 시간 제한이 지나면 비활성 연결을 끊습니다. 이를 방지하려면 ENSA1을 사용하는 경우 SAP NetWeaver ASCS/SCS 프로필에서 매개 변수를 설정해야 합니다. `keepalive`ENSA1/ENSA2 모두에 대한 모든 SAP 서버에서 Linux 시스템 설정을 변경합니다. 자세한 내용은 [SAP Note 1410736][1410736]을 참조하세요.

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

1. **[A]** /usr/sap/sapservices 파일 업데이트

   sapinit 시작 스크립트에 의해 인스턴스가 시작되지 않도록 하려면 /usr/sap/sapservices 파일에서 Pacemaker가 관리하는 모든 인스턴스를 주석 처리해야 합니다. HANA SR에 사용할 SAP HANA 인스턴스는 주석 처리하지 마세요.

    ```bash
    sudo vi /usr/sap/sapservices
   
    # On the node where you installed the ASCS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ASCS00/exe/sapstartsrv pf=/usr/sap/NW1/SYS/profile/NW1_ASCS00_sapascs -D -u nw1adm
   
    # On the node where you installed the ERS, comment out the following line
    # LD_LIBRARY_PATH=/usr/sap/NW1/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW1/ERS01/exe/sapstartsrv pf=/usr/sap/NW1/ERS01/profile/NW1_ERS01_sapers -D -u nw1adm
    ```

1. **[1]** SAP 클러스터 리소스 만들기

   큐에 넣기 서버 1 아키텍처(ENSA1)를 사용하는 경우 다음과 같이 리소스를 정의합니다.

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
     InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
     op monitor interval=20 on-fail=restart timeout=60 \
     op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
     InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
     AUTOMATIC_RECOVER=false IS_ERS=true \
     op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW1_AERS
     
    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint location rsc_sap_NW1_ASCS00 rule score=2000 runs_ers_NW1 eq 1
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP는 SAP NW 7.52부터 복제를 비롯하여 큐에 넣기 서버 2에 대한 지원을 도입했습니다. ABAP Platform 1809부터 큐에 넣기 서버 2가 기본적으로 설치됩니다. 큐에 넣기 서버 2 지원에 대해서는 SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416)을 참조하세요.
   [ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)(큐에 넣기 서버 2 아키텍처)를 사용하는 경우 리소스 에이전트 resource-agents-sap-4.1.1-12.el7.x86_64 이상을 설치하고 리소스를 다음과 같이 정의합니다.

    ```bash
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_NW1_ASCS00 SAPInstance \
    InstanceName=NW1_ASCS00_anftstsapvh START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_ASCS
   
    sudo pcs resource meta g-NW1_ASCS resource-stickiness=3000

    sudo pcs resource create rsc_sap_NW1_ERS01 SAPInstance \
    InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
    --group g-NW1_AERS
      
    sudo pcs resource meta rsc_sap_NW1_ERS01  resource-stickiness=3000

    sudo pcs constraint colocation add g-NW1_AERS with g-NW1_ASCS -5000
    sudo pcs constraint order start g-NW1_ASCS then start g-NW1_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW1_ASCS then stop g-NW1_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby sap-cl1
    sudo pcs property set maintenance-mode=false
    ```

   이전 버전에서 업그레이드하고 큐에 넣기 서버 2로 전환하는 경우 SAP Note [2641322](https://launchpad.support.sap.com/#/notes/2641322)를 참조하세요. 

   > [!NOTE]
   > 위의 구성에서 시간 제한은 단지 예제이며 특정 SAP 설정에 맞게 조정해야 할 수 있습니다. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```bash
    sudo pcs status
    
    # Online: [ sap-cl1 sap-cl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl2
    #  Resource Group: g-NW1_AERS
    #      fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
   ```

1. **[A]** 두 노드의 ASCS 및 ERS에 대한 방화벽 규칙을 추가합니다.

    ```bash
    # Probe Port of ASCS
    sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62000/tcp
    sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3200/tcp
    sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3600/tcp
    sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3900/tcp
    sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8100/tcp
    sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50013/tcp
    sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50014/tcp
    sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50016/tcp
    # Probe Port of ERS
    sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62101/tcp
    sudo firewall-cmd --zone=public --add-port=3201/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3201/tcp
    sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3301/tcp
    sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50113/tcp
    sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50114/tcp
    sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=50116/tcp
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a> SAP NetWeaver 애플리케이션 서버 준비

   일부 데이터베이스에서는 데이터베이스 인스턴스 설치가 애플리케이션 서버에서 실행되어야 합니다. 이러한 경우 애플리케이션 서버 가상 머신을 사용할 수 있도록 준비합니다.  

   아래 단계에서는 ASCS/SCS 및 HANA 서버와 다른 서버에 애플리케이션 서버를 설치한다고 가정합니다. 그 외의 경우에는 호스트 이름 확인을 구성하는 단계 등 아래의 일부 단계를 수행할 필요가 없습니다.  

   다음 항목에는 **[A]** - PAS와 AAS에 모두 적용, **[P]** - PAS에만 적용 또는 **[S]** - AAS에만 적용이 접두사로 붙습니다.  

1. **[A]** 호스트 이름 확인 설정 DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름을 바꿉니다.  

    ```bash
    sudo vi /etc/hosts
    ```

   다음 줄을 /etc/hosts에 삽입합니다. 사용자 환경에 맞게 IP 주소와 호스트 이름을 변경합니다.

    ```bash
    10.90.90.7    sap-cl1
    10.90.90.8    sap-cl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    10.90.90.10   sapascs
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    10.90.90.9    sapers
    10.90.90.12   sapa01
    10.90.90.13   sapa02
    ```

1. **[A]** sapmnt 디렉터리 만들기
   
    ```bash
    sudo mkdir -p /sapmnt/NW1
    sudo mkdir -p /usr/sap/trans

    sudo chattr +i /sapmnt/NW1
    sudo chattr +i /usr/sap/trans
   ```

1. **[A]** NFS 클라이언트 및 기타 요구 사항 설치  

    ```bash
    sudo yum -y install nfs-utils uuidd
    ```

1. **[A]** 탑재 항목 추가  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```   

1. **[A]** 스왑 파일 구성
 
    ```bash
    sudo vi /etc/waagent.conf
   
    # Set the property ResourceDisk.EnableSwap to y
    # Create and use swapfile on resource disk.
    ResourceDisk.EnableSwap=y
   
    # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
    # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
    # Size of the swapfile.
    ResourceDisk.SwapSizeMB=2000
    ```

   에이전트를 다시 시작하여 변경 내용을 활성화합니다.

    ```bash
    sudo service waagent restart
    ```

## <a name="install-database"></a>데이터베이스 설치

이 예제에서는 SAP NetWeaver가 SAP HANA에 설치됩니다. 이 설치에 지원되는 모든 데이터베이스를 사용할 수 있습니다. Azure에 SAP HANA를 설치하는 방법에 대한 자세한 내용은 [Red Hat Enterprise Linux에 설치된 Azure VM의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.. For a list of supported databases, see [SAP Note 1928533][1928533]

데이터베이스에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용하여 SAP NetWeaver 데이터베이스 인스턴스를 루트로 설치합니다.

sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   ```bash
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 애플리케이션 서버 설치

다음 단계에 따라 SAP 애플리케이션 서버를 설치합니다.

1. **[A]** 애플리케이션 서버 준비

   위에 있는 [SAP NetWeaver 애플리케이션 서버 준비](high-availability-guide-rhel-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) 챕터의 단계에 따라 애플리케이션 서버를 준비합니다.

1. **[A]** SAP NetWeaver 애플리케이션 서버를 설치합니다.  

   기본 또는 추가 SAP NetWeaver 애플리케이션 서버를 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
    ```

1. **[A]** SAP HANA 보안 저장소 업데이트

   설치한 SAP HANA System Replication의 가상 이름을 가리키도록 SAP HANA 보안 저장소를 업데이트합니다.

   다음 명령을 실행하여 항목을 로 나열합니다. `<sapsid>adm`

    ```bash
    hdbuserstore List
    ```

   이 경우 모든 항목을 나열해야 하며 다음과 비슷합니다.
    ```bash
    DATA FILE       : /home/nw1adm/.hdb/sapa01/SSFS_HDB.DAT
    KEY FILE        : /home/nw1adm/.hdb/sapa01/SSFS_HDB.KEY
   
    KEY DEFAULT
      ENV : 10.90.90.5:30313
      USER: SAPABAP1
      DATABASE: NW1
    ```

   이 예제에서 기본 진입점의 IP 주소는 부하 분산제가 아닌 VM을 가리킵니다. 부하 분산 프로그램의 가상 호스트 이름을 가리키도록 항목을 변경합니다. 동일한 포트 및 데이터베이스 이름을 사용해야 합니다. 예를 들어 `30313` `NW1` 샘플 출력의 및 입니다.


    ```bash
    su - nw1adm
    hdbuserstore SET DEFAULT nw1db:30313@NW1 SAPABAP1 <password of ABAP schema>
    ```

## <a name="test-cluster-setup"></a>클러스터 설정 테스트

Pacemaker 클러스터를 철저히 테스트합니다. [일반적인 장애 조치(failover) 테스트를 실행합니다.](./high-availability-guide-rhel.md#test-the-cluster-setup)

## <a name="next-steps"></a>다음 단계

* [SAP 애플리케이션 다중 SID에 대한 RHEL의 Azure VM에서 HA 가이드](./high-availability-guide-rhel-multi-sid.md)
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [Azure의 SAP HANA(큰 인스턴스) 고가용성 및 재해 복구](hana-overview-high-availability-disaster-recovery.md) - Azure의 SAP HANA(큰 인스턴스)에 대한 고가용성 및 재해 복구 계획을 설정하는 방법을 알아봅니다.
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.