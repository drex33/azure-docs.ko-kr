---
title: Azure Files의 NFS를 사용하는 SLES의 SAP NW에 대한 Azure VM 고가용성 | Microsoft Docs
description: SAP 애플리케이션용 Azure Files의 NFS를 사용하는 SUSE Linux Enterprise Server의 SAP NetWeaver 고가용성 가이드
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-sap
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 11/22/2021
ms.author: radeltch
ms.openlocfilehash: e305dc7a85cc7ae19ab0f1165c17955edbba91e3
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941361"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-nfs-on-azure-files"></a>Azure Files의 NFS를 사용하는 SUSE Linux Enterprise Server의 Azure VM에 있는 SAP NetWeaver에 대한 고가용성  

[dbms-guide]:dbms_guide_general.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[afs-azure-doc]:../../../storage/files/storage-files-introduction.md
[afs-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[2578899]:https://launchpad.support.sap.com/#/notes/2578899
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[2360818]:https://launchpad.support.sap.com/#/notes/2360818


[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-relnotes]:https://www.suse.com/releasenotes/index.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

이 문서에서는 [Azure Files의 NFS](../../../storage/files/files-nfs-protocol.md)를 사용하여 VM을 배포 및 구성하고, 클러스터 프레임워크를 설치하고, HA SAP NetWeaver 시스템을 설치하는 방법을 설명합니다. 예제 구성에서는 SLES(SUSE Linux Enterprise Server)에서 실행되는 VM을 사용합니다.      

## <a name="prerequisites"></a>사전 요구 사항  

* [Azure Files 설명서][afs-azure-doc] 
* SAP Note [1928533][1928533], 다음 항목을 포함합니다.  
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전
* SAP Note [2015553][2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2205917][2205917]에는 SAP 애플리케이션용 SUSE Linux Enterprise Server에 권장되는 OS 설정이 나와 있습니다.
* SAP Note [2178632][2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [2191498][2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692][2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1984787][1984787]은 SUSE LINUX Enterprise Server 12에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [2578899][2578899]는 SUSE Linux Enterprise Server 15에 대한 일반 정보를 포함하고 있습니다.
* SAP Note [1999351][1999351]은 SAP용 Azure 고급 모니터링 확장을 위한 추가 문제 해결 정보를 포함하고 있습니다.
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)는 Linux에 필요한 모든 SAP Note를 포함하고 있습니다.
* [Linux에서 SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [Linux에서 SAP용 Azure Virtual Machines 배포][deployment-guide]
* [Linux에서 SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* [SUSE SAP HA 모범 사례 가이드][suse-ha-guide] 이 가이드는 Netweaver HA 및 SAP HANA 시스템 복제 온-프레미스를 설정하는 데 필요한 모든 정보를 포함하고 있습니다. 이 가이드를 일반 기준으로 사용하세요. 여기서 훨씬 더 자세한 정보를 제공합니다.
* [SUSE High Availability Extension 릴리스 정보][suse-relnotes]


## <a name="overview"></a>개요

SAP NetWeaver 애플리케이션 계층을 배포하려면 환경에 `/sapmnt/SID` 및 `/usr/sap/trans`와 같은 공유 디렉터리가 필요합니다. 또한 HA SAP 시스템을 배포할 때 `/sapmnt/SID` 및 `/usr/sap/SID/ASCS`와 같은 고가용성 파일 시스템을 보호하고 만들어야 합니다.

이제 [Azure Files의 NFS](../../../storage/files/files-nfs-protocol.md)에 이러한 파일 시스템을 배치할 수 있습니다. Azure Files의 NFS는 HA 스토리지 솔루션입니다. 이 솔루션은 동기 ZRS(영역 중복 스토리지)를 제공하며 가용성 영역에 배포된 SAP ASCS/ERS 인스턴스에 적합합니다.  SAP Netweaver 중앙 서비스(ASCS/SCS)와 같은 단일 실패 지점 구성 요소를 보호하려면 Pacemaker 클러스터가 여전히 필요합니다.  

예제 구성 및 설치 명령은 다음 인스턴스 번호를 사용합니다.

| 인스턴스 이름 | 인스턴스 번호 |
| ---------------- | ------------------ |
| ASCS(ABAP SAP Central Services) | 00 |
| ERS | 01 |
| PAS(기본 애플리케이션 서버) | 02 |
| AAS(추가 애플리케이션 서버) | 03 |
| SAP 시스템 식별자 | NW1 |

:::image type="complex" source="./media/high-availability-guide-suse/high-availability-guide-suse-nfs-azure-files.png" alt-text="Azure Files의 NFS를 사용하는 SAP NetWeaver 고가용성":::
   이 다이어그램은 일반적인 SAP Netweaver HA 아키텍처를 보여줍니다. "sapmnt" 및 "saptrans" 파일 시스템은 Azure Files의 NFS 공유에 배포됩니다. SAP 중앙 서비스는 Pacemaker 클러스터로 보호됩니다. 클러스터형 VM은 Azure 부하 분산 장치 뒤에 있습니다. NFS 공유는 프라이빗 엔드포인트를 통해 탑재됩니다.
:::image-end:::

## <a name="prepare-infrastructure"></a>인프라 준비

이 문서에서는 [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md), 서브넷 및 리소스 그룹을 이미 배포했다고 가정합니다.

1. VM을 배포합니다. Azure 지역에서 이러한 옵션을 지원하는 경우 가용성 집합 또는 가용성 영역에 VM을 배포할 수 있습니다. VM에 대한 추가 IP 주소가 필요한 경우 두 번째 NIC를 배포하고 연결합니다. 기본 NIC에 보조 IP 주소를 추가하지 마세요. [Azure Load Balancer 부동 IP는 이 시나리오를 지원하지 않습니다.](../../../load-balancer/load-balancer-multivip-overview.md#limitations)  
 
2. 가상 IP의 경우 Azure [부하 분산 장치](../../../load-balancer/load-balancer-overview.md)를 배포 및 구성합니다. [표준 부하 분산 장치](../../../load-balancer/quickstart-load-balancer-standard-public-portal.md)를 사용하는 것이 좋습니다. 

    1. 두 개의 프런트 엔드 IP를 구성합니다. 하나는 ASCS(`10.90.90.10`)용이고 다른 하나는 ERS(`10.90.90.9`)용입니다.
    2. 백 엔드 풀을 만들고 클러스터의 일부가 될 두 VM을 추가합니다.
    3. ASCS용 상태 프로브를 만듭니다. 프로브 포트는 `62000`입니다. ERS용 프로브 포트를 만듭니다. ERS 프로브 포트는 `62101`입니다. 나중에 Pacemaker 리소스를 구성할 때 일치하는 프로브 포트를 사용해야 합니다.
    4. ASCS 및 ERS에 대한 부하 분산 규칙을 구성합니다. 해당 프런트 IP, 상태 프로브 및 백 엔드 풀을 선택합니다. HA 포트를 선택하고, 유휴 시간 제한을 30분으로 늘리고, 부동 IP를 사용하도록 설정합니다.

### <a name="deploy-azure-files-storage-account-and-nfs-shares"></a>Azure Files 스토리지 계정 및 NFS 공유 배포 

Azure Files의 NFS는 [Azure Files 프리미엄 스토리지][afs-azure-doc] 위에서 실행됩니다. Azure Files의 NFS를 설정하기 전에 [NFS 공유를 만드는 방법](../../../storage/files/storage-files-how-to-create-nfs-shares.md?tabs=azure-portal)을 참조하세요.    

Azure 지역 내에는 중복성에 대한 두 가지 옵션이 있습니다.

- [LRS(로컬 중복 스토리지)](../../../storage/common/storage-redundancy.md#locally-redundant-storage)는 로컬, 영역 내 동기 데이터 복제를 제공합니다.
- [ZRS(영역 중복 스토리지)](../../../storage/common/storage-redundancy.md#zone-redundant-storage)는 지역에 있는 세 개의 [가용성 영역](../../../availability-zones/az-overview.md)에서 데이터를 동기적으로 복제합니다.

선택한 Azure 지역이 적절한 중복성으로 Azure Files의 NFS 4.1을 제공하는지 확인합니다. **Premium Files Storage** 에서 [Azure 지역별 Azure Files의 가용성][afs-avail-matrix]을 검토합니다. 시나리오가 ZRS를 활용하는 경우 [ZRS를 사용하는 프리미엄 파일 공유가 Azure 지역에서 지원되는지 확인](../../../storage/common/storage-redundancy.md#zone-redundant-storage)합니다.

[Azure 프라이빗 엔드포인트](../../../storage/files/storage-files-networking-endpoints.md?tabs=azure-portal)를 통해 Azure Storage 계정에 액세스하는 것이 좋습니다. NFS 공유를 동일한 Azure VNet 또는 피어링된 Azure VNet에 탑재해야 하는 Azure Files 스토리지 계정 엔드포인트 및 VM을 배포해야 합니다.

1. `sapafsnfs`라는 File Storage 계정을 배포합니다. 이 예제에서는 ZRS를 사용합니다. 프로세스에 익숙하지 않은 경우 Azure Portal에 대한 [스토리지 계정 만들기](../../../storage/files/storage-how-to-create-file-share.md?tabs=azure-portal#create-a-storage-account)를 참조하세요.
1. **기본 사항** 탭에서 다음 설정을 사용합니다.

    1. **스토리지 계정 이름** 에 `sapafsnfs`를 입력합니다.
    
    1. **성능** 에 대해 **프리미엄** 을 선택합니다.
    
    1. **프리미엄 계정 유형** 에 대해 **FileStorage** 를 선택합니다.
    
    1. **복제** 에 대해 ZRS(영역 중복)를 선택합니다.

1. **새로 만들기** 를 선택합니다.

1. **고급** 탭에서 **REST API 작업을 위한 보안 전송 필요** 를 선택 취소합니다. 이 옵션을 선택 취소하지 않으면 VM에 NFS 공유를 탑재할 수 없습니다. 탑재 작업 시간이 초과됩니다.

1. **새로 만들기** 를 선택합니다.

1. **네트워킹** 섹션에서 다음 설정을 구성합니다. 

    1. **네트워킹 연결** 에서 **연결 방법** 으로 **프라이빗 엔드포인트** 를 선택합니다.  
    
    1. **프라이빗 엔드포인트** 에서 **프라이빗 엔드포인트 추가** 를 선택합니다.
    
1. **프라이빗 엔드포인트 만들기** 창에서 **구독**, **리소스 그룹** 및 **위치** 를 선택합니다. 
    
    **이름** 에 `sapafsnfs_pe`를 입력합니다.
        
    **스토리지 하위 리소스** 에 대해 **파일** 을 선택합니다.
        
    **네트워킹** 에서 **가상 네트워크** 에 대해 사용할 VNet 및 서브넷을 선택합니다. 다시 말해, SAP VM이 있는 VNet 또는 피어링된 VNet을 사용할 수 있습니다.
       
    **프라이빗 DNS 통합** 에서 **프라이빗 DNS 영역과 통합** 에 대해 기본 옵션인 **예** 를 적용합니다. **프라이빗 DNS 영역** 을 선택해야 합니다.
        
    **확인** 을 선택합니다.
        
1. **네트워킹** 탭에서 다시 **다음** 을 선택합니다.

1. **데이터 보호** 탭에서 모든 기본 설정을 유지합니다. 

1. **검토 + 만들기** 를 선택하여 구성의 유효성을 검사합니다.

1. 유효성 검사가 완료될 때까지 기다립니다. 계속하기 전에 문제를 해결합니다.

1. **검토 + 만들기** 탭에서 **만들기** 를 선택합니다.


다음으로, 사용자가 만든 스토리지 계정에 NFS 공유를 배포합니다. 이 예제에는 두 개의 NFS 공유인 `sapnw1` 및 `saptrans`가 있습니다.    
1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **스토리지 계정** 을 선택하거나 검색합니다. 

1. **스토리지 계정** 페이지에서 **sapafsnfs** 를 선택합니다.

1.  **sapafsnfs** 의 리소스 메뉴에 있는 **데이터 스토리지** 에서 **파일 공유** 를 선택합니다.

1. **파일 공유** 페이지에서 **파일 공유** 를 선택합니다.

   1. **이름** 에 `sapnw1`, `saptrans`를 입력합니다.
   1.  적절한 공유 크기를 선택합니다. 예를 들어 **128GB** 입니다.  공유, IOP 및 처리량 요구 사항에 저장된 데이터의 크기를 고려합니다.  자세한 내용은 [Azure 파일 공유 대상](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)을 참조하세요.
   1. 프로토콜로 **NFS** 를 선택합니다.
   1. **루트 Squash 없음** 을 선택합니다.  그렇지 않으면 VM에 공유를 탑재할 때 파일 소유자 또는 그룹을 볼 수 없습니다.

   > [!IMPORTANT]
   > 위의 공유 크기는 예제일 뿐입니다. 공유 크기를 적절하게 조정해야 합니다. 크기는 공유에 저장된 데이터의 크기뿐만 아니라 IOPS 및 처리량에 대한 요구 사항을 기반으로 합니다. 자세한 내용은 [Azure 파일 공유 대상](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)을 참조하세요.  

   NFS를 통해 탑재할 필요가 없는 SAP 파일 시스템은 [Azure 디스크 스토리지](../../disks-types.md#premium-ssds)에 배포할 수도 있습니다. 이 예제에서는 Azure 디스크 스토리지에 `/usr/sap/NW1/D02` 및 `/usr/sap/NW1/D03`을 배포할 수 있습니다. 

### <a name="important-considerations-for-nfs-on-azure-files-shares"></a>Azure Files 공유의 NFS에 대한 중요한 고려 사항

Azure Files의 NFS를 통해 배포를 계획할 때는 다음과 같은 중요한 사항을 고려하세요.  

- 최소 공유 크기는 100GiB입니다. [프로비전된 공유의 용량](../../../storage/files/understanding-billing.md#provisioned-model)에 대해서만 비용을 지불합니다. 
- 용량 요구 사항뿐만 아니라 IOPS 및 처리량 요구 사항에 따라 NFS 공유 크기를 정합니다. 자세한 내용은 [Azure 파일 공유 대상](../../../storage/files/storage-files-scale-targets.md#azure-file-share-scale-targets)을 참조하세요.
- 워크로드를 테스트하여 크기 조정의 유효성을 검사하고 성능 목표를 충족하는지 확인합니다. Azure Files의 NFS 관련 성능 문제를 해결하는 방법을 알아보려면 [Azure 파일 공유 성능 문제 해결](../../../storage/files/storage-troubleshooting-files-performance.md)을 참조하세요.
- SAP J2EE 시스템의 경우 Azure Files의 NFS에 `/usr/sap/<SID>/J<nr>`를 배치할 수 없습니다.
- SAP 시스템에 대량의 일괄 처리 작업 부하가 있는 경우 수백만 개의 작업 로그가 있을 수 있습니다. SAP 일괄 처리 작업 로그가 파일 시스템에 저장되어 있는 경우 `sapmnt` 공유의 크기 조정에 특히 주의해야 합니다. SAP_BASIS 7.52부터 일괄 처리 작업 로그의 기본 동작은 데이터베이스에 저장됩니다. 자세한 내용은 [데이터베이스의 작업 로그][2360818]를 참조하세요.     
- 각 SAP 시스템에 대해 별도의 `sapmnt` 공유 배포
- 인터페이스 또는 `saptrans`와 같은 다른 활동에 `sapmnt` 공유를 사용하지 마세요.
- 인터페이스 또는 `sapmnt`와 같은 다른 활동에 `saptrans` 공유를 사용하지 마세요.
- 단일 스토리지 계정에 너무 많은 SAP 시스템의 공유를 통합하지 않도록 합니다. [Storage 계정 성능 크기 조정 목표](../../../storage/files/storage-files-scale-targets.md#storage-account-scale-targets)도 있습니다. 스토리지 계정에 대한 제한을 초과하지 않도록 주의해야 합니다.
- 일반적으로 단일 스토리지 계정에 5개를 초과하는 SAP 시스템에 대한 공유를 통합하지 마세요. 이 지침은 스토리지 계정 제한을 초과하지 않도록 방지하고 성능 분석을 간소화하는 데 도움이 됩니다.   
- 일반적으로 동일한 스토리지 계정의 비프로덕션 및 프로덕션 SAP 시스템에 대해 `sapmnt`와 같은 공유를 혼합하지 않도록 합니다.
- [NFS 클라이언트 개선 사항](../../../storage/files/storage-troubleshooting-files-nfs.md#ls-hangs-for-large-directory-enumeration-on-some-kernels)을 활용하려면 SLES 15 SP2 이상에 배포하는 것이 좋습니다.     
- 프라이빗 엔드포인트를 사용합니다. 영역 오류가 발생할 가능성이 낮으면 NFS 세션이 자동으로 정상 영역으로 리디렉션됩니다. VM에서 NFS 공유를 다시 탑재할 필요가 없습니다.
- 가용성 영역에 VM을 배포하는 경우 ZRS를 지원하는 Azure 지역의 [ZRS에서 Storage 계정](../../../storage/common/storage-redundancy.md#zone-redundant-storage)을 사용합니다. 
- Azure Files는 현재 재해 복구 시나리오에 대해 지역 간 자동 복제를 지원하지 않습니다.  

## <a name="setting-up-ascs"></a>(A)SCS 설정

이 예제에서는 [Azure Portal](https://portal.azure.com/#home)을 통해 리소스를 수동으로 배포합니다.

### <a name="deploy-azure-load-balancer-via-azure-portal"></a>Azure Portal을 통해 Azure Load Balancer 배포

SAP 시스템용 VM을 배포한 후 부하 분산 장치를 만듭니다. 그런 다음, 백 엔드 풀의 VM을 사용합니다.

1. 내부 표준 부하 분산 장치를 만듭니다.
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS용 IP 주소 10.90.90.10
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름 입력(예: **frontend.NW1.ASCS**)
         1. 할당을 고정으로 설정하고 IP 주소(예: **10.90.90.10**)를 입력합니다.
         1. 확인 클릭
      1. ASCS ERS용 IP 주소 10.90.90.9
         * 위의 "a" 단계를 반복하여 ERS에 대한 IP 주소 만들기(예: **10.90.90.9** 및 **frontend.NW1.ERS**)
   1. 백 엔드 풀 만들기
      1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
      1. 새 백 엔드 풀의 이름 입력(예: **backend.NW1**)
      1. 가상 머신 추가 클릭
      1. 가상 머신 선택
      1. (A)SCS 클러스터의 가상 머신 및 해당 IP 주소 선택
      1. 추가를 클릭합니다.  
      
   1. 상태 프로브 만들기
      1. 포트: 620 **00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름 입력(예: **health.NW1.ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620 **00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS용 포트 621 **01**
            * 위의 "c" 단계를 반복하여 ERS에 대한 상태 프로브 만들기(예: 621 **01** 및 **health.NW1.ERS**)
   1. 부하 분산 규칙
      1. ASCS에 대한 백 엔드 풀 만들기
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정하고, 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름 입력(예: **lb.NW1.ASCS**)
         1. 이전에 만든 ASCS의 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 선택(예: **frontend.NW1.ASCS**, **backend.NW1** 및 **health.NW1.ASCS**)
         1. **HA 포트** 선택
         1. **부동 IP를 사용하도록 설정**
         1. 확인 클릭
         * 위의 단계를 반복하여 ERS에 대한 부하 분산 규칙 만들기(예: **lb.NW1.ERS**)
1. 또는 시나리오에 기본 부하 분산 장치(내부)가 필요한 경우 다음 단계를 수행합니다.  
   1. 프런트 엔드 IP 주소 만들기
      1. ASCS용 IP 주소 10.90.90.10
         1. 부하 분산 장치 열기, 프런트 엔드 IP 풀 선택 및 추가 클릭
         1. 새 프런트 엔드 IP 풀의 이름 입력(예: **frontend.NW1.ASCS**)
         1. 할당을 고정으로 설정하고 IP 주소(예: **10.90.90.10**)를 입력합니다.
         1. 확인을 클릭합니다.
      1. ASCS ERS용 IP 주소 10.90.90.9
         * 위의 "a" 단계를 반복하여 ERS에 대한 IP 주소 만들기(예: **10.90.90.9** 및 **frontend.NW1.ERS**)
   1. 백 엔드 풀 만들기
      1. 부하 분산 장치를 열고 백 엔드 풀을 선택한 다음 추가 클릭
      1. 새 백 엔드 풀의 이름 입력(예: **backend.NW1**)
      1. 가상 머신 추가 클릭
      1. ASCS에 대해 이전에 만든 가용성 집합 선택 
      1. (A)SCS 클러스터의 가상 머신 선택
      1. 확인 클릭
   1. 상태 프로브 만들기
      1. 포트: 620 **00**(ASCS용)
         1. 부하 분산 장치를 열고 상태 프로브를 선택한 다음 추가 클릭
         1. 새 상태 프로브의 이름 입력(예: **health.NW1.ASCS**)
         1. 프로토콜로 TCP를 선택하고, 620 **00** 포트를 선택한 다음, 간격은 5, 비정상 임계값은 2로 유지
         1. 확인 클릭
      1. ASCS ERS용 포트 621 **01**
            * 위의 "c" 단계를 반복하여 ERS에 대한 상태 프로브 만들기(예: 621 **01** 및 **health.NW1.ERS**)
   1. 부하 분산 규칙
      1. TCP: 32 **00**(ASCS용)
         1. 부하 분산 장치를 열고, 부하 분산 규칙을 설정하고, 추가 클릭
         1. 새 부하 분산 장치 규칙의 이름 입력(예: **lb.NW1.ASCS.3200**)
         1. 이전에 만든 ASCS의 프런트 엔드 IP 주소, 백 엔드 풀 및 상태 프로브 선택(예: **frontend.NW1.ASCS**)
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

[Azure의 SUSE Linux Enterprise Server에서 Pacemaker 설정](high-availability-guide-suse-pacemaker.md) 단계에 따라 SAP (A)SCS용 기본 Pacemaker 클러스터를 만듭니다.

### <a name="installation"></a>설치

다음 항목에는 접두사 **[A]** (모든 노드에 적용됨), **[1]** (노드 1에만 적용됨), **[2]** (노드 2에만 적용됨) 접두사가 표시되어 있습니다.

1. **[A]** 최신 버전의 SUSE Connector 설치

    ```bash
    sudo zypper install sap-suse-cluster-connector
    ```

   > [!NOTE]
   > 호스트 이름에 대시 사용과 관련된 알려진 문제는 패키지 **sap-suse-cluster-connector** 의 버전 **3.1.1** 에서 해결되었습니다. 호스트 이름에 대시가 있는 클러스터 노드를 사용하는 경우 패키지 sap-suse-cluster-connector의 버전 3.1.1 이상을 사용하고 있는지 확인하세요. 대시를 사용하면 클러스터가 작동하지 않습니다. 

   SAP SUSE 클러스터 커넥터의 새 버전을 설치했는지 확인합니다. 기존 버전은 sap_suse_cluster_connector라고 하며 새 버전은 **sap suse-cluster-connector** 라고 합니다.  

2. **[A]** SAP 리소스 에이전트 업데이트  
   
   이 문서에서 설명하는 새 구성을 사용하려면 리소스 에이전트 패키지용 패치가 필요합니다. 다음 명령을 사용하여 패치가 이미 설치되었는지를 확인할 수 있습니다.

    ```bash
    sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
    ```

   다음과 같은 출력이 표시되어야 합니다.

    ```bash
    <parameter name="IS_ERS" unique="0" required="0">;
    ```

   grep 명령을 실행하여 IS_ERS 매개 변수를 찾을 수 없는 경우에는 [SUSE 다운로드 페이지](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)에 나와 있는 패치를 설치해야 합니다.


3. **[A]** 호스트 이름 확인 설정

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

4. **[1]** NFS 공유에 SAP 디렉터리를 만듭니다.  
   VM 중 하나의 NFS 공유 **sapnw1** 을 임시로 탑재하고 중첩된 탑재 지점으로 사용할 SAP 디렉터리를 만듭니다.  

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

## <a name="prepare-for-sap-netweaver-installation"></a>SAP NetWeaver 설치 준비

1. **[A]** 공유 디렉터리 만들기

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

2. **[A]** Pacemaker 클러스터에 의해 제어되지 않는 파일 시스템을 탑재합니다.  

    ```bash
    vi /etc/fstab
    # Add the following lines to fstab, save and exit
    sapnfs.file.core.windows.net:/sapnfsafs/saptrans /usr/sap/trans  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/sapmntNW1 /sapmnt/NW1  nfs vers=4,minorversion=1,sec=sys  0  0
    sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1sys/ /usr/sap/NW1/SYS  nfs vers=4,minorversion=1,sec=sys  0  0
    
    # Mount the file systems
    mount -a 
    ```

3. **[A]** 스왑 파일 구성

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

### <a name="installing-sap-netweaver-ascsers"></a>SAP NetWeaver ASCS/ERS 설치

1. **[1]** ASCS 인스턴스에 대한 가상 IP 리소스 및 상태 프로브 만들기

   > [!IMPORTANT]
   > 다음 패키지 버전 요구 사항과 함께 패키지 리소스 에이전트의 일부인 azure-lb 리소스 에이전트를 사용하는 것이 좋습니다.
   > - SLES 12 SP4/SP5의 경우 버전은 resource-agents-4.3.018.a7fb5035-3.30.1 이상이어야 합니다.  
   > - SLES 15 이상의 경우 버전은 resource-agents-4.3.0184.6ee15eb2-4.13.1 이상이어야 합니다.  
   

    ```bash
    sudo crm node standby sap-cl2
    sudo crm configure primitive fs_NW1_ASCS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ascs' directory='/usr/sap/NW1/ASCS00' fstype='nfs' options='sec=sys,vers=4.1' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
    
    sudo crm configure primitive vip_NW1_ASCS IPaddr2 \
      params ip=10.90.90.10 cidr_netmask=24 \
      op monitor interval=10 timeout=20
    
    sudo crm configure primitive nc_NW1_ASCS azure-lb port=62000
    
    sudo crm configure group g-NW1_ASCS fs_NW1_ASCS nc_NW1_ASCS vip_NW1_ASCS \
       meta resource-stickiness=3000
    ```

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```bash 
    sudo crm_mon -r
    # Node sap-cl2: standby
    # Online: [ sap-cl1 ]
    #
    # Full list of resources:
    #
    # stonith-sbd     (stonith:external/sbd): Started sap-cl1
    # Resource Group: g-NW1_ASCS
    #  fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #  nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #  vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
   
    ```
  
2. **[1]** SAP NetWeaver ASCS 설치  

   ASCS에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름(예: ***sapascs** _, _*_10.90.90.10_*_) 및 부하 분산 장치의 프로브에 사용한 인스턴스 번호(예: _*_00_)를 사용하여 첫 번째 노드에 SAP NetWeaver ASCS를 루트로 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME을 사용하여 가상 호스트 이름으로 SAP를 설치할 수 있습니다.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
    ```

   설치에서 /usr/sap/**NW1**/ASCS **00** 에 하위 폴더를 만들지 못하면 ASCS **00** 폴더의 소유자와 그룹을 설정하고 다시 시도합니다. 

    ```bash
    chown nw1adm /usr/sap/NW1/ASCS00
    chgrp sapsys /usr/sap/NW1/ASCS00
    ```

3. **[1]** ERS 인스턴스에 대한 가상 IP 리소스 및 상태 검사 프로브 만들기

    ```bash
    sudo crm node online sap-cl2
    sudo crm node standby sap-cl1
    sudo crm configure primitive fs_NW1_ERS Filesystem device='sapnfs.file.core.windows.net:/sapnfsafs/sapnw1/usrsapNW1ers' directory='/usr/sap/NW1/ERS01' fstype='nfs' options='sec=sys,vers=4.1' \
      op start timeout=60s interval=0 \
      op stop timeout=60s interval=0 \
      op monitor interval=20s timeout=40s
   
    sudo crm configure primitive vip_NW1_ERS IPaddr2 \
      params ip=10.90.90.9 cidr_netmask=24 \
      op monitor interval=10 timeout=20
   
    sudo crm configure primitive nc_NW1_ERS azure-lb port=62101
    
    sudo crm configure group g-NW1_ERS fs_NW1_ERS nc_NW1_ERS vip_NW1_ERS
    ```

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```bash
    sudo crm_mon -r
   
    # Node sap-cl1: standby
    # Online: [ sap-cl2 ]
    # 
    # Full list of resources:
    #
    # stonith-sbd     (stonith:external/sbd): Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #  Resource Group: g-NW1_ERS
    #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started sap-cl2 
    #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ERS  (ocf::heartbeat:IPaddr2):     Started sap-cl2
    ```

4. **[2]** SAP NetWeaver ERS 설치

   ERS용 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름(예: **sapers**, **10.90.90.9**)과 부하 분산 장치의 프로브에 사용했던 인스턴스 번호(예: **01**)를 사용하여 두 번째 노드에 루트로 SAP NetWeaver ERS를 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다. 매개 변수 SAPINST_USE_HOSTNAME을 사용하여 가상 호스트 이름으로 SAP를 설치할 수 있습니다.

    ```bash
    <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > SWPM SP 20 PL 05 이상을 사용합니다. 그 이전 버전은 권한을 올바르게 설정하지 않으므로 설치가 실패합니다.

   설치에서 /usr/sap/**NW1**/ERS **01** 에 하위 폴더를 만들지 못하면 ERS **01** 폴더의 소유자와 그룹을 설정하고 다시 시도합니다.

    ```bash
    chown nw1adm /usr/sap/NW1/ERS01
    chgrp sapsys /usr/sap/NW1/ERS01
    ```

5. **[1]** ASCS/SCS 및 ERS 인스턴스 프로필 조정
 
   * ASCS/SCS 프로필

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ASCS00_sapascs
    
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

    ```bash
    sudo vi /sapmnt/NW1/profile/NW1_ERS01_sapers
     
    # Change the restart command to a start command
    #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
    
    # Add the following lines
    service/halib = $(DIR_CT_RUN)/saphascriptco.so
    service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
    
    # remove Autostart from ERS profile
    # Autostart = 1
    ```

6. **[A]** 연결 유지 구성

   SAP NetWeaver 애플리케이션 서버와 ASCS/SCS 간의 통신은 소프트웨어 부하 분산 장치를 통해 라우팅됩니다. 부하 분산 장치는 구성 가능한 시간 제한이 지나면 비활성 연결을 끊습니다. 이를 방지하려면 ENSA1을 사용하는 경우 SAP NetWeaver ASCS/SCS 프로필에서 매개 변수를 설정해야 합니다. ENSA1/ENSA2에 대해 모든 SAP 서버에서 Linux 시스템 `keepalive` 설정을 변경합니다. 자세한 내용은 [SAP Note 1410736][1410736]을 참조하세요.

    ```bash
    # Change the Linux system configuration
    sudo sysctl net.ipv4.tcp_keepalive_time=300
    ```

7. **[A]** 설치 후 SAP 사용자 구성

    ```bash
    # Add sidadm to the haclient group
    sudo usermod -aG haclient nw1adm
    ```

8. **[1]** `sapservice` 파일에 ASCS 및 ERS SAP 서비스 추가

   ASCS 서비스 항목을 두 번째 노드에 추가하고 ERS 서비스 항목을 첫 번째 노드에 복사합니다.

    ```bash
    cat /usr/sap/sapservices | grep ASCS00 | sudo ssh sap-cl2 "cat >>/usr/sap/sapservices"
    sudo ssh sap-cl2 "cat /usr/sap/sapservices" | grep ERS01 | sudo tee -a /usr/sap/sapservices
    ```

9. **[1]** SAP 클러스터 리소스 만들기

   큐에 넣기 서버 1 아키텍처(ENSA1)를 사용하는 경우 다음과 같이 리소스를 정의합니다.

    ```bash
    sudo crm configure property maintenance-mode="true"
   
    sudo crm configure primitive rsc_sap_NW1_ASCS00 SAPInstance \
     operations \$id=rsc_sap_NW1_ASCS00-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
    sudo crm configure primitive rsc_sap_NW1_ERS01 SAPInstance \
     operations \$id=rsc_sap_NW1_ERS01-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" AUTOMATIC_RECOVER=false IS_ERS=true \
     meta priority=1000
   
    sudo crm configure modgroup g-NW1_ASCS add rsc_sap_NW1_ASCS00
    sudo crm configure modgroup g-NW1_ERS add rsc_sap_NW1_ERS01
   
    sudo crm configure colocation col_sap_NW1_no_both -5000: g-NW1_ERS g-NW1_ASCS
    sudo crm configure location loc_sap_NW1_failover_to_ers rsc_sap_NW1_ASCS00 rule 2000: runs_ers_NW1 eq 1
    sudo crm configure order ord_sap_NW1_first_start_ascs Optional: rsc_sap_NW1_ASCS00:start rsc_sap_NW1_ERS01:stop symmetrical=false
   
    sudo crm node online sap-cl1
    sudo crm configure property maintenance-mode="false"
    ```

   SAP는 SAP NW 7.52부터 복제를 비롯하여 큐에 넣기 서버 2에 대한 지원을 도입했습니다. ABAP Platform 1809부터 큐에 넣기 서버 2가 기본적으로 설치됩니다. 큐에 넣기 서버 2 지원에 대해서는 SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416)을 참조하세요.  

   큐에 넣기 서버 2 아키텍처([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html))를 사용하는 경우 다음과 같이 리소스를 정의합니다.

    ```bash
    sudo crm configure property maintenance-mode="true"
   
    sudo crm configure primitive rsc_sap_NW1_ASCS00 SAPInstance \
     operations \$id=rsc_sap_NW1_ASCS00-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ASCS00_sapascs START_PROFILE="/sapmnt/NW1/profile/NW1_ASCS00_sapascs" \
     AUTOMATIC_RECOVER=false \
     meta resource-stickiness=5000
   
    sudo crm configure primitive rsc_sap_NW1_ERS01 SAPInstance \
     operations \$id=rsc_sap_NW1_ERS01-operations \
     op monitor interval=11 timeout=60 on-fail=restart \
     params InstanceName=NW1_ERS01_sapers START_PROFILE="/sapmnt/NW1/profile/NW1_ERS01_sapers" AUTOMATIC_RECOVER=false IS_ERS=true
   
    sudo crm configure modgroup g-NW1_ASCS add rsc_sap_NW1_ASCS00
    sudo crm configure modgroup g-NW1_ERS add rsc_sap_NW1_ERS01
    
    sudo crm configure colocation col_sap_NW1_no_both -5000: g-NW1_ERS g-NW1_ASCS
    sudo crm configure order ord_sap_NW1_first_start_ascs Optional: rsc_sap_NW1_ASCS00:start rsc_sap_NW1_ERS01:stop symmetrical=false
   
    sudo crm node online sap-cl1
    sudo crm configure property maintenance-mode="false"
    ```

   이전 버전에서 업그레이드하고 큐에 넣기 서버 2로 전환하는 경우 SAP note [2641019](https://launchpad.support.sap.com/#/notes/2641019)를 참조하세요. 

   클러스터 상태가 정상이며 모든 리소스가 시작되었는지 확인합니다. 리소스가 실행되는 노드는 중요하지 않습니다.

    ```bash
    sudo crm_mon -r
    # Full list of resources:
    # 
    # stonith-sbd     (stonith:external/sbd): Started sap-cl2
    #  Resource Group: g-NW1_ASCS
    #      fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started sap-cl1
    #      nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started sap-cl1
    #      vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started sap-cl1
    #      rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started sap-cl1
    #  Resource Group: g-NW1_ERS
    #      fs_NW1_ERS (ocf::heartbeat:Filesystem):    Started sap-cl2
    #      nc_NW1_ERS (ocf::heartbeat:azure-lb):      Started sap-cl2
    #      vip_NW1_ERS        (ocf::heartbeat:IPaddr2):       Started sap-cl2
    #      rsc_sap_NW1_ERS01  (ocf::heartbeat:SAPInstance):   Started sap-cl1
    ```

## <a name="sap-netweaver-application-server-preparation"></a><a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a> SAP NetWeaver 애플리케이션 서버 준비 

일부 데이터베이스에서는 데이터베이스 인스턴스 설치가 애플리케이션 서버에서 실행되어야 합니다. 이러한 경우 애플리케이션 서버 가상 머신을 사용할 수 있도록 준비합니다.

아래 단계에서는 ASCS/SCS 및 HANA 서버와 다른 서버에 애플리케이션 서버를 설치한다고 가정합니다. 그 외의 경우에는 호스트 이름 확인을 구성하는 단계 등 아래의 일부 단계를 수행할 필요가 없습니다.

다음 항목에는 **[A]** - PAS와 AAS에 모두 적용, **[P]** - PAS에만 적용 또는 **[S]** - AAS에만 적용이 접두사로 붙습니다.


1. **[A]** 운영 체제 구성

   더티 캐시의 크기를 줄입니다. 자세한 내용은 [큰 RAM이 있는 SLES 11/12 서버의 쓰기 성능 저하](https://www.suse.com/support/kb/doc/?id=7010287)를 참조하세요.

    ```bash
    sudo vi /etc/sysctl.conf
    # Change/set the following settings
    vm.dirty_bytes = 629145600
    vm.dirty_background_bytes = 314572800
    ```

1. **[A]** 호스트 이름 확인 설정

   DNS 서버를 사용하거나 모든 노드의 /etc/hosts를 수정할 수 있습니다. 이 예에서는 /etc/hosts 파일 사용 방법을 보여줍니다.
   다음 명령에서 IP 주소와 호스트 이름 바꾸기

    ```bash
    sudo vi /etc/hosts
    ```

   다음 줄을 /etc/hosts에 삽입합니다. 환경에 맞게 IP 주소와 호스트 이름 변경

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

1. **[A]** 파일 시스템 탑재

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

이 예제에서는 SAP NetWeaver가 SAP HANA에 설치됩니다. 이 설치에 지원되는 모든 데이터베이스를 사용할 수 있습니다. Azure에서 SAP HANA를 설치하는 방법에 대한 자세한 내용은 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요. 지원되는 데이터베이스 목록은 [SAP Note 1928533][1928533]을 참조하세요.

데이터베이스에 대한 부하 분산 장치 프런트 엔드 구성의 IP 주소에 매핑되는 가상 호스트 이름을 사용하여 SAP NetWeaver 데이터베이스 인스턴스를 루트로 설치합니다.  
sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

   ```bash
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 애플리케이션 서버 설치

다음 단계에 따라 SAP 애플리케이션 서버를 설치합니다.

1. **[A]** 애플리케이션 서버 준비. 위에 있는 [SAP NetWeaver 애플리케이션 서버 준비](high-availability-guide-suse-nfs-azure-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) 챕터의 단계에 따라 애플리케이션 서버를 준비합니다.

2. **[A]** SAP NetWeaver 애플리케이션 서버를 설치합니다.  
   기본 또는 추가 SAP NetWeaver 애플리케이션 서버를 설치합니다.

   sapinst 매개 변수 SAPINST_REMOTE_ACCESS_USER를 사용하면 루트 권한이 없는 사용자의 sapinst 연결을 허용할 수 있습니다.

    ```bash
    sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

3. **[A]** SAP HANA 보안 저장소 업데이트

   설치한 SAP HANA System Replication의 가상 이름을 가리키도록 SAP HANA 보안 저장소를 업데이트합니다.

   다음 명령을 실행하여 항목을 나열합니다.
    ```bash
    hdbuserstore List
    ```

   이 명령은 모든 항목을 나열해야 하며 다음과 유사해야 합니다.
    ```bash
    DATA FILE       : /home/nw1adm/.hdb/sapa01/SSFS_HDB.DAT
    KEY FILE        : /home/nw1adm/.hdb/sapa01/SSFS_HDB.KEY
   
    KEY DEFAULT 
      ENV : 10.90.90.5:30313
      USER: SAPABAP1
      DATABASE: NW1
    ```

   이 예제에서 기본 진입점의 IP 주소는 부하 분산 장치가 아니라 VM을 가리킵니다. 항목을 부하 분산 장치의 가상 호스트 이름을 가리키도록 변경합니다. 동일한 포트와 데이터베이스 이름을 사용해야 합니다. 예를 들어 샘플 출력의 `30313` 및 `NW1`이 있습니다. 

   ```bash
   su - nw1adm
   hdbuserstore SET DEFAULT nw1db:30313@NW1 SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-cluster-setup"></a>클러스터 설정 테스트

Pacemaker 클러스터를 철저히 테스트합니다. [일반적인 장애 조치(failover) 테스트를 실행](./high-availability-guide-suse.md#test-the-cluster-setup)합니다.

## <a name="next-steps"></a>다음 단계

* [SAP 애플리케이션 다중 SID에 대한 SLES의 Azure VM에서 SAP NW용 HA 가이드](./high-availability-guide-suse-multi-sid.md)
* [SAP용 Azure Virtual Machines 계획 및 구현][planning-guide]
* [SAP용 Azure Virtual Machines 배포][deployment-guide]
* [SAP용 Azure Virtual Machines DBMS 배포][dbms-guide]
* Azure VM에서 SAP HANA의 재해 복구를 계획하고 고가용성을 설정하는 방법을 알아보려면 [Azure VM(Virtual Machines)의 SAP HANA 고가용성][sap-hana-ha]을 참조하세요.