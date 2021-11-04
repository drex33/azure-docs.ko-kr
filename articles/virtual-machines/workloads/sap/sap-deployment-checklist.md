---
title: SAP 워크로드 계획 및 배포 검사 목록 | Microsoft Docs
description: Azure에 SAP 워크로드 배포를 계획하고 워크로드를 배포하기 위한 검사 목록
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/10/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 652347a2b63581a613088497ef8b3cb7df4ee8ec
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448750"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>Azure의 SAP 워크로드: 계획 및 배포 검사 목록

이 검사 목록은 SAP NetWeaver, S/4HANA 및 Hybris 애플리케이션을 Azure Infrastructure as a Service로 이동하는 고객을 위해 작성되었습니다. 프로젝트를 진행하는 동안 고객 및/또는 SAP 파트너는 검사 목록을 검토해야 합니다. 많은 검사가 프로젝트를 시작할 때 그리고 계획 단계에서 완료된다는 것을 기억해야 합니다. 배포가 완료된 후에는 배포된 Azure 인프라 또는 SAP 소프트웨어 릴리스에서 간단한 변경조차 복잡해질 수 있습니다.

프로젝트를 진행하는 동안 주요 마일스톤에서 검사 목록을 검토합니다. 이렇게 하면 작은 문제가 큰 문제로 확대되기 전에 미리 발견할 수 있습니다. 또한 필요한 변경 내용을 다시 엔지니어링하고 테스트하는 데 충분한 시간을 확보할 수 있습니다. 이 검사 목록을 전체 목록으로 간주해서는 안 됩니다. 상황에 따라 더 많은 검사를 수행해야 할 수도 있습니다.

Azure와 무관한 작업은 이 검사 목록에 포함되어 있지 않습니다. 예를 들어 Azure 플랫폼이나 호스팅 공급자로 전환하는 동안 SAP 애플리케이션 인터페이스가 변경될 수도 있습니다.

이 검사 목록은 이미 배포된 시스템에도 사용할 수 있습니다. 배포한 이후에 쓰기 가속기, 가용성 영역 등의 새로운 기능과 새 VM 유형이 추가되었을 수 있습니다. 따라서 검사 목록을 주기적으로 검토하여 Azure 플랫폼의 새로운 기능을 알아 두는 것이 유용합니다.

## <a name="project-preparation-and-planning-phase"></a>프로젝트 준비 및 계획 단계
이 단계에서는 SAP 워크로드를 Azure 플랫폼으로 마이그레이션할 계획을 세웁니다. 이 단계에서는 적어도 다음 문서를 작성하고 아래와 같은 마이그레이션 요소를 정의 및 설명해야 합니다.

1. 개략적인 디자인 문서. 이 문서에는 다음 내용이 포함되어야 합니다.
    - SAP 구성 요소 및 애플리케이션의 현재 인벤토리와 Azure의 대상 애플리케이션 인벤토리
    - 관련 당사자의 책임과 할당을 정의하는 RACI(책임 할당 매트릭스). 상위 수준에서 시작한 후, 계획 전체와 첫 번째 배포에서 점점 세부적으로 작업합니다.
    - 개괄적인 솔루션 아키텍처
    - 배포할 Azure 지역의 결정. [Azure 지역 목록](https://azure.microsoft.com/global-infrastructure/regions/)을 참조하세요. 각 지역에서 사용할 수 있는 서비스에 대한 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/)을 참조하세요.
    - 온-프레미스에서 Azure에 연결하기 위한 네트워킹 아키텍처. [Azure의 가상 데이터 센터 청사진](/azure/architecture/vdc/)부터 숙지하세요.
    - 많은 영향을 미치는 비즈니스 데이터를 Azure에서 실행하기 위한 보안 원칙. 데이터 보안에 대한 자세한 내용은 [Azure 보안 설명서](../../../security/index.yml)를 참조하세요.
2.  기술 디자인 문서. 이 문서에는 다음 내용이 포함되어야 합니다.
    - 솔루션의 블록 다이어그램
    - Azure의 컴퓨팅, 스토리지 및 네트워킹 구성 요소 크기 조정. Azure VM의 SAP 크기 조정은 [SAP 
    -  노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533) 을 참조하세요.
    - 비즈니스 연속성 및 재해 복구 아키텍처
    - OS, DB, 커널 및 SAP 지원 팩 버전에 대한 자세한 정보. SAP NetWeaver 또는 S/4HANA에서 지원되는 모든 OS 릴리스가 Azure VM에서도 반드시 지원되는 것은 아닙니다. DBMS 릴리스도 마찬가지입니다. 다음 소스를 확인하여 SAP 및 Azure를 지원하도록 SAP 릴리스, DBMS 릴리스 및 OS 릴리스를 정렬하고 필요한 경우 업그레이드하세요. SAP 및 Microsoft의 완벽한 지원을 받으려면 SAP 및 Azure에서 지원하는 릴리스 조합이 있어야 합니다. 필요한 경우 소프트웨어 구성 요소 중 일부를 업그레이드할 계획을 세워야 합니다. 지원되는 SAP, OS 및 DBMS 소프트웨어에 대한 자세한 내용은 다음 문서에 설명되어 있습니다.
        - [SAP 지원 노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533). 이 노트에는 Azure VM에서 지원되는 최소 OS 릴리스가 정의되어 있습니다. 대부분의 비 HANA 데이터베이스에 필요한 최소 데이터베이스 릴리스도 정의되어 있습니다. 마지막으로 SAP에서 지원하는 Azure VM 유형에 대한 SAP 크기 조정 기능을 제공합니다.
        - [SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553). 이 노트에는 Azure 스토리지에 대한 지원 정책과 Microsoft에 필요한 지원 관계가 정의되어 있습니다.
        - [SAP 지원 노트 #2039619](https://launchpad.support.sap.com/#/notes/2039619). 이 노트에는 Azure의 Oracle 지원 매트릭스가 정의되어 있습니다. Oracle은 Azure에서 SAP 워크로드에 대한 게스트 운영 체제로 Windows 및 Oracle Linux만 지원합니다. 이러한 지원 설명은 SAP 인스턴스를 실행하는 SAP 애플리케이션 계층에도 적용됩니다. 그러나 Oracle은 Pacemaker를 통해 Oracle Linux의 SAP Central Services에 대한 고가용성을 지원하지 않습니다. Oracle Linux에서 ASCS에 대한 고가용성이 필요한 경우 Linux용 SIOS Protection Suite를 사용해야 합니다. SAP 인증 데이터에 대한 자세한 내용은 SAP 지원 노트 [#1662610 - Linux용 SIOS Protection Suite 지원 세부 정보](https://launchpad.support.sap.com/#/notes/1662610)를 확인하세요. Windows에서는 SAP Central Services용 SAP 지원 Windows Server 장애 조치(Failover) 클러스터 솔루션이 Oracle DBMS 계층과 함께 지원됩니다.
        - [SAP 지원 노트 #2235581](https://launchpad.support.sap.com/#/notes/2235581). 이 노트는 다양한 OS 릴리스의 SAP HANA 지원 매트릭스를 제공합니다.
        - SAP HANA에서 지원하는 Azure VM 및 [HANA 대규모 인스턴스](./hana-overview-architecture.md)는 [SAP 웹 사이트](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나와 있습니다.
        - [SAP 제품 가용성 매트릭스](https://support.sap.com/en/)
        - [SAP 지원 노트 #2555629 - SAP HANA 2.0 동적 계층화 – 하이퍼바이저 및 클라우드 지원](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP 지원 노트 #1662610 - Linux용 SIOS Protection Suite에 대한 지원 세부 정보](https://launchpad.support.sap.com/#/notes/1662610)
        - 다른 SAP 관련 제품에 대한 SAP 노트     
    - SAP Central Services에 다중 SID 클러스터 구성을 사용하는 것은 Azure의 Windows, SLES 및 RHEL 게스트 운영 체제에서 지원됩니다. 이러한 다중 SID 클러스터를 사용하면 ASCS/SCS가 더 많아질 수 있다는 점에 유의하세요. 각 게스트 OS 시나리오에 대한 설명서는 다음 문서에서 찾을 수 있습니다.
        - [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 공유 디스크를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-shared-disk.md)
        - [Azure에서 Windows Server 장애 조치(Failover) 클러스터링 및 파일 공유를 사용하는 SAP ASCS/SCS 인스턴스 다중 SID 고가용성](./sap-ascs-ha-multi-sid-wsfc-file-share.md)
        - [SAP 애플리케이션용 SUSE Linux Enterprise Server의 Azure VM 기반 SAP NetWeaver에 대한 다중 SID 고가용성 가이드](./high-availability-guide-suse-multi-sid.md)
        - [SAP 애플리케이션용 Red Hat Enterprise Linux의 Azure VM 기반 SAP NetWeaver에 대한 다중 SID 고가용성 가이드](./high-availability-guide-rhel-multi-sid.md)
    - 고가용성 및 재해 복구 아키텍처
        - RTO 및 RPO에 따라 필요한 고가용성 및 재해 복구 아키텍처 구성을 정의합니다.
        - 한 영역 내에서 고가용성을 제공해야 하는 경우 Azure에서 DBMS가 제공해야 하는 기능을 확인합니다. 대부분의 DBMS 패키지는 프로덕션 시스템에 권장하는 동기화 방법인 동기 상시 대기를 제공합니다. [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md) 및 관련 문서부터 시작해서 다양한 데이터베이스에 대한 SAP 관련 설명서도 확인하세요.
           [SQL Server에서 설명했듯이](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server) DBMS 계층의 공유 디스크 구성에서 Windows Server 장애 조치(Failover) 클러스터를 사용하는 것은 지원되지 않습니다. 대신 다음과 같은 솔루션을 사용합니다.
           - [SQL Server Always On](/previous-versions/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle 데이터 가드](../oracle/configure-oracle-dataguard.md)
           - [HANA 시스템 복제](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Azure 지역의 재해 복구는 다른 DBMS 공급업체에서 제공하는 솔루션을 검토하세요. 대부분은 비동기 복제 또는 로그 전달을 지원합니다.
        - SAP 애플리케이션 계층의 경우 비즈니스 재발 테스트 시스템(프로덕션 배포의 복제본인 것이 가장 좋음)을 동일한 Azure 지역에서 실행할 것인지 아니면 DR 지역에서 실행할 것인지 결정합니다. 후자인 경우 해당 비즈니스 재발 시스템을 프로덕션 배포의 DR 대상으로 지정할 수 있습니다.
        - DR 사이트에 비프로덕션 시스템을 배치하지 않으려면 SAP 애플리케이션 계층을 Azure DR 지역에 복제하는 방법으로 Azure Site Recovery를 검토합니다. 자세한 내용은 [다중 계층 SAP NetWeaver 앱 배포를 위한 재해 복구 설정](../../../site-recovery/site-recovery-sap.md)을 참조하세요.
        - [Azure 가용성 영역](../../../availability-zones/az-overview.md)을 사용하여 결합된 HADR 구성을 사용하기로 결정하는 경우 가용성 영역을 사용할 수 있는 Azure 지역을 숙지해야 합니다. 두 가용성 영역 간의 네트워크 대기 시간이 증가하여 발생할 수 있는 제한 사항도 고려해야 합니다.  
3.  모든 SAP 인터페이스(SAP 및 비 SAP)의 인벤토리
4.  기초 서비스 디자인. 이 디자인에는 다음 항목이 포함되어야 합니다.
    - Active Directory 및 DNS 디자인
    - Azure 내 네트워크 토폴로지 및 다른 SAP 시스템의 할당
    - Azure의 인프라 및 SAP 애플리케이션을 관리하는 팀을 위한 [Azure RBAC(Azure 역할 기반 액세스 제어)](../../../role-based-access-control/overview.md) 구조
    - 리소스 그룹 토폴로지
    - [태그 지정 전략](../../../azure-resource-manager/management/tag-resources.md#tags-and-billing)
    - VM 및 기타 인프라 구성 요소 및/또는 논리적 이름에 대한 명명 규칙
5.  Microsoft Professional 또는 프리미어 지원 계약. Microsoft와 프리미어 지원 계약을 맺은 경우 담당 Microsoft TAM(기술 계정 관리자)을 확인하세요. SAP 지원 요구 사항은 [SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553)을 참조하세요.
6.  Azure 구독 수 및 구독의 코어 할당량. 필요에 따라 [지원 요청을 열어 Azure 구독 할당량을 늘립니다](../../../azure-portal/supportability/regional-quota-requests.md).
7.  SAP 데이터를 Azure로 마이그레이션하기 위한 데이터 감소 및 데이터 마이그레이션 계획. SAP NetWeaver 시스템의 경우 SAP는 대량의 데이터 볼륨을 제한하는 방법에 대한 지침을 제공합니다. SAP ERP 시스템의 데이터 관리 방법은 [이 SAP 가이드](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2)를 참조하세요. 일부 콘텐츠는 NetWeaver 및 S/4HANA 시스템에도 일반적으로 적용됩니다.
8.  자동화된 배포 방법. Azure에서 인프라 배포를 자동화하는 목표는 결정적 방식으로 배포하여 결정적 결과를 얻는 것입니다. 많은 고객이 PowerShell 또는 CLI 기반 스크립트를 사용합니다. 하지만 SAP용 Azure 인프라를 배포하고 SAP 소프트웨어를 설치하는 데 사용할 수 있는 다양한 오픈 소스 기술이 있습니다. GitHub에서 예제를 찾을 수 있습니다.
    - [Azure Cloud의 자동화된 SAP 배포](https://github.com/Azure/sap-hana)
    - [SAP HANA 설치](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  고객, 시스템 통합업체, Microsoft 및 기타 관련 당사자 간의 정기적인 디자인 및 배포 검토 케이던스를 정의합니다.


## <a name="pilot-phase-strongly-recommended"></a>파일럿 단계(강력 권장)
 
프로젝트를 계획하고 준비하는 동안 또는 그 전에 파일럿을 실행할 수 있습니다. 파일럿 단계를 사용하여 계획 및 준비 단계에서 작성된 방법과 디자인을 테스트할 수도 있습니다. 그리고 파일럿 단계를 확장하여 개념 증명으로 만들 수 있습니다.

파일럿 배포 중에 전체 HADR 솔루션 및 보안 디자인을 설정하고 유효성을 검사하는 것이 좋습니다. 어떤 고객은 이 단계에서 확장성 테스트를 수행합니다. 또 어떤 고객은 SAP 샌드박스 시스템 배포를 파일럿 단계로 사용합니다. 여기서는 파일럿을 실행하기 위해 Azure로 마이그레이션하려는 시스템을 이미 식별했다고 가정합니다.

1. Azure로 데이터 전송을 최적화합니다. 가장 좋은 선택은 시나리오에 따라 달라집니다. ExpressRoute 회로의 대역폭이 충분한 경우 온-프레미스에서 [Azure ExpressRoute ](https://azure.microsoft.com/services/expressroute/)를 통해 전송하는 것이 가장 빠릅니다. 다른 시나리오에서는 인터넷을 통해 전송하는 것이 더 빠릅니다.
2. 데이터 내보내기 및 가져오기가 수반되는 이기종 SAP 플랫폼 마이그레이션의 경우 내보내기 및 가져오기 단계를 테스트하고 최적화합니다. SQL Server가 대상 플랫폼인 대량 마이그레이션의 경우 [권장 사항](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)을 찾을 수 있습니다. 결합된 릴리스 업그레이드가 필요 없는 경우 마이그레이션 모니터/SWPM을 사용할 수 있습니다. 마이그레이션을 SAP 릴리스 업그레이드와 결합할 때 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용할 수 있습니다. 이렇게 하려면 원본 및 대상 DBMS 플랫폼 조합에 대한 특정 요구 사항을 충족해야 합니다. 이 프로세스는 [SUM 2.0 SP03의 DMO(데이터베이스 마이그레이션 옵션)](https://launchpad.support.sap.com/#/notes/2631152)에 설명되어 있습니다.
   1.  원본으로 내보내고, Azure로 파일 업로드를 내보내고, 성능을 가져옵니다. 내보내기와 가져오기 간 겹침을 최대화합니다.
   2.  인프라 크기를 조정하기 위해 대상 플랫폼의 데이터베이스 볼륨을 평가합니다.
   3.  유효성을 검사하고 타이밍을 최적화합니다.
1. 기술 유효성 검사
   1. VM 유형
        - SAP 지원 노트, SAP HANA 하드웨어 디렉터리 및 SAP PAM의 리소스를 다시 검토합니다. Azure를 지원하는 VM, 이러한 VM 유형을 지원하는 OS 릴리스, 지원되는 SAP 및 DBMS 릴리스가 변경되지 않았는지 확인합니다.
        - Azure에 배포하는 애플리케이션 및 인프라의 크기가 유효한지 다시 확인합니다. 기존 애플리케이션을 이동하는 경우 사용하는 인프라 및 [SAP 벤치마크 웹 페이지](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd)에서 필요한 SAPS를 파생하고 SAPS 지원 노트 [#1928533](https://launchpad.support.sap.com/#/notes/1928533)에 나열된 SAPS 번호와 비교할 수 있습니다. 또한 [SAPS 등급에 대한 이 문서](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208)를 기억해 두세요.
        - 계획 단계에서 선택한 VM 유형의 최대 스토리지 처리량 및 네트워크 처리량과 관련된 Azure VM의 크기 조정을 평가하고 테스트합니다. 데이터는 아래 문서에서 찾을 수 있습니다.
           -  [Azure에서 Windows 가상 머신 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 크기 조정을 위해서는 *캐시되지 않은 최대 디스크 처리량* 을 고려하는 것이 중요합니다.
           -  [Azure에서 Linux 가상 머신의 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json). 크기 조정을 위해서는 *캐시되지 않은 최대 디스크 처리량* 을 고려하는 것이 중요합니다.
   2. 스토리지.
        - [SAP 워크로드에 대한 Azure Storage 유형](./planning-guide-storage.md) 문서를 확인하세요.
        - SAP 애플리케이션 계층을 나타내는 VM과 성능이 중요하지 않은 DBMS 배포에는 적어도 [Azure 표준 SSD 스토리지](../../disks-types.md#standard-ssds)를 사용합니다.
        - 일반적으로 [Azure 표준 HDD 디스크](../../disks-types.md#standard-hdds)는 권장하지 않습니다.
        - 원격 성능이 중요한 DBMS에는 [Azure Premium Storage](../../disks-types.md#premium-ssds)를 사용합니다.
        - [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)를 사용합니다.
        - M-Series가 있는 DBMS 로그 드라이브에 대해 Azure Write Accelerator를 사용합니다. [쓰기 가속기](../../how-to-enable-write-accelerator.md)에 설명된 쓰기 가속기 제한 및 사용법을 숙지합니다.
        - 다른 DBMS 형식의 경우 [일반 SAP 관련 DBMS 설명서](./dbms_guide_general.md) 및 일반 문서에 언급되는 DBMS 관련 설명서를 확인합니다.
        - SAP HANA에 대한 자세한 내용은 [Azure에서 SAP HANA 인프라 구성 및 작업](./hana-vm-operations.md)을 참조하세요.
        - 디바이스 ID를 사용하여 Azure 데이터 디스크를 Azure Linux VM에 탑재해서는 안됩니다. 대신 UUID(Universally Unique Identifier)를 사용합니다. 예를 들어 Azure 데이터 디스크를 탑재하기 위해 그래픽 도구를 사용하는 경우 주의해야 합니다. /etc/fstab의 항목을 두 번 확인하여 UUID가 디스크를 탑재하는 데 사용되는지 확인합니다. 자세한 내용은 [이 문서](../../linux/attach-disk-portal.md#connect-to-the-linux-vm-to-mount-the-new-disk)에서 확인할 수 있습니다.
   3. 네트워킹
        - 여러 Azure 가상 네트워크에서 가상 네트워크 인프라 및 SAP 애플리케이션 배포를 테스트하고 평가합니다.
        -  단일 Azure 가상 네트워크 내에서 허브 및 스포크 가상 네트워크 아키텍처 접근 방식 또는 마이크로 구분 접근 방식을 평가합니다. 이 평가의 기준은 다음과 같습니다.
               1. [피어링된 Azure 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md) 간의 데이터 교환 비용. 비용에 대한 자세한 내용은 [Virtual Network 가격 책정](https://azure.microsoft.com/pricing/details/virtual-network/)을 참조하세요.
               2. 네트워크 보안 그룹을 변경하여 가상 네트워크 내에서 서브넷을 분리하는 것과는 반대로 Azure 가상 네트워크 간의 피어링을 빠르게 해제하는 장점. 이 평가는 가상 네트워크의 서브넷에 호스트되는 애플리케이션 또는 VM이 보안상 위험할 수 있는 경우에 수행합니다.
                3. 온-프레미스, 외부 세계 및 Azure에서 구축한 가상 데이터 센터 간의 네트워크 트래픽에 대한 중앙 로깅 및 감사.
        - SAP 애플리케이션 계층과 SAP DBMS 계층 간의 데이터 경로를 평가하고 테스트합니다.
            -  SAP 애플리케이션과 SAP NetWeaver, Hybris 또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 간 통신 경로에 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/)를 배치하는 기능은 지원되지 않습니다.
            -  피어링되지 않은 다른 Azure 가상 네트워크에 SAP 애플리케이션 계층과 SAP DBMS를 배치하는 것은 지원되지 않습니다.
            -  [애플리케이션 보안 그룹 및 네트워크 보안 그룹 규칙](../../../virtual-network/network-security-groups-overview.md)을 사용하여 SAP 애플리케이션 계층과 SAP DBMS 계층 간의 경로를 정의할 수 있습니다.
        - SAP 애플리케이션 계층 및 SAP DBMS 계층에서 사용되는 VM에 [Azure 가속화된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 사용하도록 설정합니다. Azure에서 가속화된 네트워킹을 지원하려면 다음과 같은 여러 OS 수준이 필요합니다.
            - Windows Server 2012 R2 이상
            - SUSE Linux 12 SP3 이상
            - RHEL 7.4 이상
            - Oracle Linux 7.5. RHCKL 커널을 사용하는 경우 릴리스 3.10.0-862.13.1.el7이 필요합니다. Oracle UEK 커널을 사용하는 경우 릴리스 5가 필요합니다.
        - SAP 지원 노트 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 SAP 애플리케이션 계층 VM과 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. [SAP 지원 노트 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가합니다. 네트워크 대기 시간이 보통이거나 정상 범위에 있어야 합니다. [이 문서](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에 설명된 대로 VM과 HANA 대규모 인스턴스 단위 간의 트래픽에 예외가 적용됩니다.
        - ILB 배포가 Direct Server Return을 사용하도록 설정되어 있는지 확인합니다. 이 설정은 DBMS 계층의 고가용성 구성에 Azure ILB가 사용되는 경우 대기 시간을 줄입니다.
        - Linux 게스트 운영 체제와 함께 Azure Load Balancer를 사용하는 경우 Linux 네트워크 매개 변수 **net.ipv4.tcp_timestamps** 가 **0** 으로 설정되었는지 확인합니다. 이 권장 사항은 이전 버전의 [SAP 노트 #2382421](https://launchpad.support.sap.com/#/notes/2382421) 권장 사항과 충돌합니다. 이제 SAP 노트는 Azure 부하 분산 장치에 사용하려면 이 매개 변수를 **0** 으로 설정해야 한다고 업데이트되었습니다.
        - 최적의 네트워크 대기 시간을 얻으려면 [Azure 근접 배치 그룹](../../co-location.md)을 사용하는 것이 좋습니다. 자세한 내용은 [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)을 참조하세요.
   4. 고가용성 및 재해 복구 배포
        - 특정 Azure 가용성 영역을 정의하지 않고 SAP 애플리케이션 계층을 배포하는 경우 단일 SAP 시스템의 SAP 대화 인스턴스 또는 미들웨어 인스턴스를 실행하는 모든 VM이 [가용성 집합](../../availability-set-overview.md)에 배포되어 있는지 확인합니다.
        - SAP Central Services 및 DBMS에 고가용성이 필요하지 않은 경우 이러한 VM을 SAP 애플리케이션 계층과 동일한 가용성 집합에 배포할 수 있습니다.
        - 고가용성을 위해 수동 복제를 사용하여 SAP Central Services 및 DBMS 계층을 보호하는 경우 SAP Central Services용 노드 2개와 DBMS 노드 2개를 각각 별도의 가용성 집합에 둡니다.
        - Azure 가용성 영역에 배포하는 경우 가용성 집합을 사용할 수 없습니다. 하지만 능동 및 수동 Central Services 노드를 서로 다른 두 가용성 영역에 배포해야 합니다. 대기 시간이 가장 짧은 가용성 영역을 사용합니다.
          가용성 영역의 DBMS 및 SAP Central Services 계층에 대한 Windows 또는 Pacemaker 장애 조치(Failover) 클러스터를 설정하는 사용 사례에서는 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-standard-availability-zones.md)를 사용해야 합니다. [기본 Load Balancer](../../../load-balancer/load-balancer-overview.md)는 영역 배포에 사용할 수 없습니다.
   5. 시간 제한 설정
        - SAP 인스턴스의 SAP NetWeaver 개발자 추적을 검사하고 큐에 넣기 서버와 SAP 작업 프로세스 간 연결이 끊어지지 않았는지 확인합니다. 이러한 연결 끊김은 다음 두 레지스트리 매개 변수를 설정하여 방지할 수 있습니다.
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. 자세한 내용은 [KeepAliveTime](/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))을 참조하세요.
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. 자세한 내용은 [KeepAliveInterval](/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10))을 참조하세요.
        - 온-프레미스 SAP GUI 인터페이스와 Azure에 배포된 SAP 애플리케이션 계층 간 GUI 시간 초과를 방지하려면 default.pfl 또는 인스턴스 프로필에서 다음 매개 변수가 설정되었는지 확인합니다.
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - SAP 큐에 넣기 프로세스와 SAP 작업 프로세스 간에 설정된 연결이 해제되지 않도록 하려면 **enque/encni/set_so_keepalive** 매개 변수를 **true** 로 설정해야 합니다. [SAP 노트 #2743751](https://launchpad.support.sap.com/#/notes/2743751)도 참조하세요.  
        - Windows 장애 조치(failover) 클러스터 구성을 사용하는 경우 응답하지 않는 노드에 반응하는 시간이 Azure에 대해 올바르게 설정되어 있는지 확인합니다. [장애 조치(Failover) 클러스터 네트워크 임계값 조정](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) 문서에는 매개 변수 및 매개 변수가 장애 조치(Failover) 민감도에 미치는 영향이 나와 있습니다. 클러스터 노드가 동일한 서브넷에 있다고 가정할 경우 다음 매개 변수를 변경해야 합니다.
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. OS 설정 또는 패치
        - SAP에서 HANA를 실행하려면 다음 노트와 설명서를 읽어 보세요.
            -   [SAP 지원 노트 #2814271 - Azure에서 SAP HANA 백업이 체크섬 오류로 실패](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP 지원 노트 #2753418 - 타이머 폴백으로 인한 잠재적 성능 저하](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP 지원 노트 #2791572 - Azure의 Hyper-V에 대한 VDSO 지원 누락에 따른 성능 저하](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP 지원 노트 #2382421 - HANA 및 OS 수준에서 네트워크 구성 최적화](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP 지원 노트 #2694118 - Azure의 Red Hat Enterprise Linux HA 추가 항목](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP 지원 노트 #1984787 – SUSE LINUX Enterprise Server 12: 설치 노트](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP 지원 노트 #2002167 - Red Hat Enterprise Linux 7.x: 설치 및 업그레이드](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP Support Note #2292690 - SAP HANA DB: RHEL 7에 대한 권장 OS 설정](https://launchpad.support.sap.com/#/notes/0002292690)(영문)
            -   [SAP 지원 노트 #2772999 - Red Hat Enterprise Linux 8.x: 설치 및 구성](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP Support Note #2777782 - SAP HANA DB: RHEL 8의 권장 OS 설정](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP 지원 노트 #2578899 – SUSE Linux Enterprise Server 15: 설치 노트](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP 지원 노트 #2455582 - Linux: GCC 6.x로 컴파일된 SAP 애플리케이션 실행](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP 지원 노트 #2729475 - SAP HANA에 대해 인증된 Azure VM에서 "하이퍼바이저가 지원되지 않습니다" 오류와 함께 HWCCT가 실패](https://launchpad.support.sap.com/#/notes/2729475)
1. 고가용성 및 재해 복구 절차 테스트
   1. VM(Windows 게스트 운영 체제)을 종료하거나 운영 체제를 비상 모드(Linux 게스트 운영 체제)로 전환하여 장애 조치(failover) 상황을 시뮬레이션합니다. 이 단계는 장애 조치(failover) 구성이 설계대로 작동하는지 파악하는 데 도움이 됩니다.
   1. 장애 조치(failover)를 실행하는 데 걸리는 시간을 측정합니다. 시간이 너무 오래 걸리면 다음을 고려합니다.
        - SUSE Linux의 경우 Azure Fence 에이전트 대신 SBD 디바이스를 사용하여 장애 조치(failover) 속도를 높입니다.
        - SAP HANA의 경우 데이터 다시 로드 시간이 너무 오래 걸리면 더 많은 스토리지 대역폭을 프로비전하는 것이 좋습니다.
   3. 백업/복원 순서와 타이밍을 테스트하고 필요한 경우 수정합니다. 백업 시간이 충분한지 확인합니다. 복원 및 시간 복원 작업도 테스트해야 합니다. RTO가 데이터베이스 또는 VM 복원 프로세스에 의존하는 경우 복원 시간이 RTO SLA 내에 있는지 확인합니다.
   4. 지역 간 DR 기능과 아키텍처를 테스트합니다.
1. 보안 검사.
   1. Azure RBAC(역할 기반 액세스 제어) 아키텍처의 유효성을 테스트합니다. 목표는 다른 팀의 액세스 및 권한을 분리하고 제한하는 것입니다. 예를 들어 SAP 기본 팀 구성원은 VM을 배포하고 Azure Storage의 디스크를 지정된 Azure 가상 네트워크에 할당할 수 있어야 합니다. 그러나 SAP 기본 팀은 고유의 가상 네트워크를 만들거나 기존 가상 네트워크의 설정을 변경할 수 없어야 합니다. 네트워크 팀의 구성원은 VM을 SAP 애플리케이션 및 DBMS VM이 실행되는 가상 네트워크에 배포할 수 없어야 합니다. 또한 네트워크 팀의 구성원은 VM 특성을 변경하거나 VM 또는 디스크를 삭제할 수 없어야 합니다.  
   1.  [네트워크 보안 그룹 및 ASC](../../../virtual-network/network-security-groups-overview.md) 규칙이 예상대로 작동하는지 확인하고 보호된 리소스를 보호합니다.
   1.  암호화해야 하는 모든 리소스가 암호화되는지 확인합니다. 인증서를 백업하고, 해당 인증서를 저장 및 액세스하고, 암호화된 엔터티를 복원하는 프로세스를 정의하고 구현합니다.
   1.  OS 지원 관점에서 가능한 경우 OS 디스크에 [Azure Disk Encryption](../../../security/fundamentals/azure-disk-encryption-vms-vmss.md)을 사용합니다.
   1.  너무 많은 암호화 계층을 사용하지 않도록 주의합니다. 경우에 따라 DBMS 투명한 데이터 암호화 방법 중 하나와 함께 Azure Disk Encryption을 사용하여 동일한 서버에 있는 여러 디스크 또는 구성 요소를 보호하는 것이 좋습니다.  예를 들어 SAP DBMS 서버의 경우 운영 체제 부팅 디스크(OS가 ADE를 지원하는 경우) 및 DBMS 데이터 지속성 파일이 사용하지 않는 데이터 디스크에서 ADE(Azure Disk Encryption)를 사용하도록 설정할 수 있습니다.  예를 들어 DBMS TDE 암호화 키를 보관하는 디스크에서 ADE를 사용합니다.
1. 성능 테스트. SAP에서 SAP 추적 및 측정을 기반으로 다음과 같은 비교를 수행합니다.
   - 해당하는 경우 상위 10개 온라인 보고서를 현재 구현과 비교합니다.
   - 해당하는 경우 상위 10개 일괄 작업을 현재 구현과 비교합니다.
   - 인터페이스를 통해 SAP 시스템으로 전송되는 데이터를 비교합니다. 온-프레미스에서 Azure로 전송되는 경우처럼 서로 다른 위치 간에 데이터가 전송되는 것을 알 고 있는 인터페이스에 집중합니다.


## <a name="non-production-phase"></a>비프로덕션 단계 
이 단계에서는 성공적인 파일럿 또는 POC(개념 증명) 후에 비프로덕션 SAP 시스템을 Azure에 배포하기 시작한다고 가정합니다. POC 과정에서 배우고 경험한 모든 것을 이 배포에 통합합니다. POC에 대해 나열된 모든 조건과 단계는 이 배포에도 적용됩니다.

이 단계에서는 일반적으로 개발 시스템, 단위 테스트 시스템 및 비즈니스 재발 테스트 시스템을 Azure에 배포합니다. 향후 프로덕션 시스템에 필요하므로 단일 SAP 애플리케이션 라인에 포함된 하나 이상의 비프로덕션 시스템에 완전한 고가용성 구성을 적용하는 것이 좋습니다. 다음은 이 단계에서 완료해야 하는 몇 가지 추가 단계입니다.  

1.  이전 플랫폼의 시스템을 Azure로 이동하기 전에 CPU 사용량, 스토리지 처리량 및 IOPS 데이터와 같은 리소스 소비량 데이터를 수집합니다. 특히 DBMS 계층 단위에서 이 데이터를 수집하지만 애플리케이션 계층 단위에서도 수집합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
2.  시스템의 가용성 사용 시간 패턴을 기록합니다. 목표는 비프로덕션 시스템을 연중무휴로 사용할 수 있어야 하는지 여부 또는 주 또는 월의 특정 단계 동안 종료해도 되는 비프로덕션 시스템이 있는지 파악하는 것입니다.
3.  Azure에서 VM에 대한 사용자 고유의 OS 이미지를 만들 것인지 또는 Azure Azure Compute 갤러리(이전의 Shared Image Gallery)에서 이미지를 사용할지 여부를 테스트하고 결정합니다. Azure Compute 갤러리의 이미지를 사용하는 경우 OS 공급업체와의 지원 계약을 반영하는 이미지를 사용해야 합니다. 일부 OS 공급업체의 경우 Azure Compute 갤러리를 사용하면 사용자 고유의 라이선스 이미지를 가져올 수 있습니다. 다른 OS 이미지의 경우 Azure의 견적 가격에 지원이 포함됩니다. 사용자 고유의 OS 이미지를 만들려는 경우 다음 문서에서 설명서를 찾을 수 있습니다.
    -   [Azure에 배포된 Windows VM의 일반화된 이미지 작성](../../windows/capture-image-resource.md)
    -   [Azure에 배포된 Linux VM의 일반화된 이미지 작성](../../linux/capture-image.md)
3.  Azure Compute 갤러리에서 SUSE 및 Red Hat Linux 이미지를 사용하는 경우 Azure Compute 갤러리의 Linux 공급업체에서 제공하는 SAP용 이미지를 사용해야 합니다.
4.  Microsoft 지원 계약에 대한 SAP 지원 요구 사항을 이행해야 합니다. [SAP 지원 노트 #2015553](https://launchpad.support.sap.com/#/notes/2015553)을 참조하세요. HANA 대규모 인스턴스의 경우 [온보딩 요구 사항](./hana-onboarding-requirements.md)을 참조하세요.
4.  최적의 가동 중지 시간을 선택할 수 있도록 적절한 관련자가 [계획된 유지 관리 알림](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/)을 받을 수 있게 합니다.
5.  [Channel 9](https://channel9.msdn.com/) 같은 채널의 Azure 프레젠테이션을 자주 확인하여 배포에 적용할 수 있는 새 기능이 있는지 알아봅니다.
6.  [지원 노트 #1928533](https://launchpad.support.sap.com/#/notes/1928533)과 같은 Azure 관련 SAP 노트에서 새 VM SKU 또는 새로 지원되는 OS 및 DBMS 릴리스가 있는지 확인합니다. 최고의 가격 대비 성능을 제공하는 VM을 배포할 수 있도록 새 VM 유형의 가격을 이전 VM 유형의 가격과 비교합니다.
7.  SAP 지원 노트, SAP HANA 하드웨어 디렉터리 및 SAP PAM을 다시 확인합니다. Azure를 지원하는 VM, 이러한 VM을 지원하는 OS 릴리스, 지원되는 SAP 및 DBMS 릴리스가 변경되지 않았는지 확인합니다.
8.  [SAP 웹 사이트](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에서 Azure의 새로운 HANA 인증 SKU를 확인합니다. 새 SKU의 가격 책정을 사용하려는 가격 책정과 비교합니다. 최종적으로 가장 좋은 가격 대비 성능을 제공하는 제품을 사용하도록 변경해야 합니다.
9.  새 VM 유형을 사용하고 원하는 새 Azure 기능을 통합하도록 배포 스크립트를 조정합니다.
10. 인프라를 배포한 후 SAP 지원 노트 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 따라 SAP 애플리케이션 계층 VM 및 DBMS VM 간의 네트워크 대기 시간을 테스트하고 평가합니다. [SAP 지원 노트 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가합니다. 네트워크 대기 시간이 보통이거나 정상 범위에 있어야 합니다. [이 문서](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에 설명된 대로 VM과 HANA 대규모 인스턴스 단위 간의 트래픽에 예외가 적용됩니다. [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md#azure-network-considerations) 및 [Azure의 SAP HANA 인프라 구성 및 운영](./hana-vm-operations.md)에 언급된 어떤 제한도 배포에 적용되지 않는지 확인합니다.
11. [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)에 설명된 대로 VM을 올바른 [Azure 근접 배치 그룹](../../co-location.md)에 배포합니다.
11. 개념 증명 단계에 나열된 다른 모든 검사를 수행한 후 워크로드를 적용합니다.
12. 워크로드가 적용되면 Azure 시스템의 리소스 사용량을 기록합니다. 이 사용량을 이전 플랫폼의 레코드와 비교합니다. 더 큰 차이가 확인되면 향후 배포에서 VM 크기를 조정합니다. 스토리지를 줄이면 VM의 네트워크 대역폭도 감소합니다.
    - [Azure에서 Windows 가상 머신에 대한 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [Azure에서 Linux 가상 머신의 크기](../../sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. 시스템 복사 기능 및 프로세스를 시험합니다. 목표는 개발 시스템 또는 테스트 시스템을 쉽게 복사하도록 하여 프로젝트 팀이 새 시스템을 빠르게 얻을 수 있게 만드는 것입니다. 
14. 팀의 Azure 역할 기반 액세스, 권한 및 프로세스를 최적화하여 의무를 분리합니다. 그와 동시에 모든 팀이 Azure 인프라에서 맡은 작업을 수행할 수 있는지 확인합니다.
15. 고가용성 및 재해 복구 절차를 연습, 테스트 및 문서화하여 직원이 이러한 작업을 실행할 수 있도록 합니다. 단점을 파악하고 배포에 통합하려는 새 Azure 기능을 조정합니다.


## <a name="production-preparation-phase"></a>프로덕션 준비 단계 
이 단계에서는 비프로덕션 배포 중에 경험하고 배운 내용을 수집하고 향후 프로덕션 배포에 적용합니다. 현재 호스팅 위치와 Azure 간의 데이터 전송 작업도 준비해야 합니다.

1.  Azure로 이동하기 전에 프로덕션 시스템의 필수 SAP 릴리스 업그레이드를 완료합니다.
1.  프로덕션 시스템의 마이그레이션 이후에 수행해야 하는 기능 및 비즈니스 테스트를 비즈니스 소유자와 협의합니다.
1.  이러한 테스트를 현재 호스팅 위치의 원본 시스템으로 완료해야 합니다. 시스템이 Azure로 이동된 후 첫 번째 테스트를 수행하지 않도록 합니다.
1.  프로덕션 시스템을 Azure로 마이그레이션하는 프로세스를 테스트합니다. 같은 기간에 모든 프로덕션 시스템을 Azure로 이동하지 않는 경우 동일한 호스팅 위치에 있어야 하는 프로덕션 시스템 그룹을 작성합니다. 데이터 마이그레이션을 테스트합니다. 다음은 몇 가지 일반적인 방법입니다.
    - Azure에서 데이터베이스 콘텐츠를 시드하고 동기화하려면 백업/복원 등의 DBMS 방법과 SQL Server Always On, HANA 시스템 복제 또는 로그 전달을 함께 사용합니다.
    - 백업/복원은 좀 더 작은 데이터베이스에 사용합니다.
    - SAP SWPM에 통합되는 SAP 마이그레이션 모니터를 사용하여 이기종 마이그레이션을 수행합니다.
    - 마이그레이션을 SAP 릴리스 업그레이드와 조합해야 하는 경우 [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) 프로세스를 사용합니다. 원본 DBMS와 대상 DBMS 간의 모든 조합이 지원되는 것은 아닙니다. 자세한 내용은 다양한 DMO 릴리스의 관련 SAP 지원 노트를 참조하세요. 예: [SUM 2.0 SP04의 DMO(데이터베이스 마이그레이션 옵션)](https://launchpad.support.sap.com/#/notes/2644872)
    - 백업 또는 SAP 내보내기 파일을 이동해야 하는 경우 인터넷을 통한 데이터 전송과 ExpressRoute를 통한 데이터 전송 중 어떤 방식의 처리량이 더 높은지 테스트합니다. 인터넷을 통해 데이터를 이동하는 경우 향후 프로덕션 시스템을 위해 필요한 네트워크 보안 그룹/애플리케이션 보안 그룹 규칙 중 일부를 변경해야 할 수 있습니다.
1.  이전 플랫폼에서 Azure로 시스템을 이동하기 전에 리소스 사용 데이터를 수집합니다. 유용한 데이터로는 CPU 사용량, 스토리지 처리량 및 IOPS 데이터가 있습니다. 특히 DBMS 계층 단위에서 이 데이터를 수집하지만 애플리케이션 계층 단위에서도 수집합니다. 또한 네트워크 및 스토리지 대기 시간을 측정합니다.
1.  SAP 지원 노트와 필요한 OS 설정, SAP HANA 하드웨어 디렉터리 및 SAP PAM을 다시 확인합니다. Azure를 지원하는 VM, 이러한 VM을 지원하는 OS 릴리스, 지원되는 SAP 및 DBMS 릴리스가 변경되지 않았는지 확인합니다.
1.  마지막으로 결정하는 VM 유형 및 Azure 기능을 반영하도록 배포 스크립트를 업데이트합니다.
1.  인프라 및 애플리케이션을 배포한 후 다음을 확인합니다.
    - 올바른 VM 유형을 올바른 특성 및 스토리지 크기로 배포했습니다.
    - VM이 올바른 OS 릴리스 및 패치에 있으며 균일합니다.
    - VM이 필요에 따라 일관적인 방식으로 확정됩니다.
    - 올바른 애플리케이션 릴리스 및 패치를 설치하고 배포했습니다.
    - VM을 계획대로 Azure 가용성 집합에 배포했습니다.
    - Azure Premium Storage는 대기 시간이 중요한 디스크 또는 [SLA가 99.9%인 단일 VM](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)이 필요한 곳에 사용됩니다.
    - Azure 쓰기 가속기가 올바르게 배포되었습니다.
        - VM 내의 스토리지 공간 또는 스트라이프 세트가, 쓰기 가속기가 필요한 디스크에 올바르게 작성되었는지 확인합니다.
        - [Linux의 소프트웨어 RAID 구성](/previous-versions/azure/virtual-machines/linux/configure-raid)을 확인합니다.
        - [Azure의 Linux VM에서 LVM 구성](/previous-versions/azure/virtual-machines/linux/configure-lvm)을 확인합니다.
    - [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)가 배타적으로 사용됩니다.
    - VM이 올바른 가용성 집합 및 가용성 영역에 배포되었습니다.
    - SAP 애플리케이션 계층 및 SAP DBMS 계층에 사용되는 VM에서 [Azure 가속화된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)을 사용하도록 설정되었습니다.
    - SAP 애플리케이션과 SAP NetWeaver, Hybris 또는 S/4HANA 기반 SAP 시스템의 DBMS 계층 간 통신 경로에 [Azure 네트워크 가상 어플라이언스](https://azure.microsoft.com/solutions/network-appliances/)가 없습니다.
    - 애플리케이션 보안 그룹 및 네트워크 보안 그룹 규칙을 사용하면 필요에 따라 통신을 계획하고 차단할 수 있습니다.
    - 앞에서 설명된 시간 제한이 올바르게 설정되었습니다.
    - [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure 근접 배치 그룹](sap-proximity-placement-scenarios.md)에 설명된 대로 VM이 올바른 [Azure 근접 배치 그룹](../../co-location.md)에 배포되었습니다.
    - SAP 지원 노트 [#500235](https://launchpad.support.sap.com/#/notes/500235) 및 [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)에 설명된 대로 SAP 애플리케이션 계층 VM과 DBMS VM 간의 네트워크 대기 시간이 테스트되고 유효성이 검사됩니다. [SAP 지원 노트 #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)의 네트워크 대기 시간 지침에 따라 결과를 평가합니다. 네트워크 대기 시간이 보통이거나 정상 범위에 있어야 합니다. [이 문서](./hana-network-architecture.md#networking-architecture-for-hana-large-instance)에 설명된 대로 VM과 HANA 대규모 인스턴스 단위 간의 트래픽에 예외가 적용됩니다.
    - 필요할 때 적절한 암호화 방법으로 암호화가 구현되었습니다.
    - 인터페이스 및 기타 애플리케이션은 새로 배포된 인프라에 연결할 수 있습니다.
1.  계획된 Azure 유지 관리에 대응하기 위한 플레이북을 만듭니다. 계획된 유지 관리를 위해 시스템 및 VM을 다시 부팅해야 하는 순서를 결정합니다.
    

## <a name="go-live-phase"></a>라이브로 전환 단계
라이브 단계 중에는 이전 단계에서 개발한 플레이북을 따라야 합니다. 테스트하고 연습한 단계를 실행합니다. 구성 및 프로세스에서 갑작스러운 변경을 피합니다. 또한 다음 단계를 완료합니다.

1. Azure Portal 모니터링 및 기타 모니터링 도구가 작동하는지 확인합니다. Windows용 Windows 성능 모니터(perfmon) 및 Linux용 SAR을 추천합니다.
    - CPU 카운터
        - 평균 CPU 시간, 합계(모든 CPU)
        - 평균 CPU 시간, 개별 프로세서(M128 VM에서 128개 프로세서)
        - CPU 커널 시간, 개별 프로세서
        - CPU 사용자 시간, 개별 프로세서
    - 메모리.
        - 사용 가능한 메모리
        - 메모리 페이지 입력/초
        - 메모리 페이지 출력/초
    - 디스크를 선택해야 합니다.
        - 개별 디스크당 디스크 읽기(KBps)
        - 개별 디스크당 디스크 읽기/초
        - 개별 디스크당 디스크 읽기(마이크로초/읽기)
        - 개별 디스크당 디스크 쓰기(KBps)
        - 개별 디스크당 디스크 쓰기/초
        - 개별 디스크당 디스크 쓰기(마이크로초/읽기)
    - Network.
        - 네트워크 패킷 입력/초
        - 네트워크 패킷 출력/초
        - 네트워크 KB 입력/초
        - 네트워크 KB 출력/초
1.  데이터 마이그레이션 후 비즈니스 소유자와 합의한 모든 유효성 검사 테스트를 수행합니다. 원래 원본 시스템에 대한 결과가 있는 경우에만 유효성 검사 테스트 결과를 수락합니다.
1.  인터페이스가 작동하는지 여부 및 다른 애플리케이션이 새로 배포한 프로덕션 시스템과 통신할 수 있는지 여부를 확인합니다.
1.  SAP 트랜잭션 STMS를 통해 전송 및 수정 시스템을 확인합니다.
1.  시스템이 프로덕션용으로 출시되면 데이터베이스 백업을 수행합니다.
1.  시스템이 프로덕션용으로 출시되면 SAP 애플리케이션 계층 VM의 VM 백업을 수행합니다.
1.  현재는 라이브 단계가 아니지만 이 라이브 단계 동안 Azure로 이동한 SAP 시스템과 통신하는 SAP 시스템의 경우 SM51에서 호스트 이름 버퍼를 다시 설정해야 합니다. 그러면 Azure로 이동한 애플리케이션 인스턴스의 이름과 연결된 이전에 캐시한 IP 주소가 제거됩니다.  


## <a name="post-production"></a>프로덕션 후
이 단계에서는 시스템을 모니터링, 운영 및 관리합니다. SAP의 관점에서 보면, 이전 호스팅 위치에서 완료했어야 했던 일반적인 작업이 적용됩니다. 또한 다음과 같은 Azure 관련 작업을 완료합니다.

1. 높은 요금이 부과된 시스템의 Azure 청구서를 검토합니다.
2. VM 및 스토리지 쪽에서 가격/성능 효율을 최적화합니다.
3. 시스템을 종료할 수 있는 시간을 최적화합니다.  


## <a name="next-steps"></a>다음 단계
아래 문서를 참조하세요.

- [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현](./planning-guide.md)
- [SAP NetWeaver에 대한 Azure Virtual Machines 배포](./deployment-guide.md)
- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md)