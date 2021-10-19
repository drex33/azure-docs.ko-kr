---
title: '검사 목록: 모범 사례 및 지침'
description: Azure VM(Virtual Machine)에서 SQL Server의 성능을 최적화하기 위한 지침 및 모범 사례를 검토하는 빠른 검사 목록을 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: pamela
ms.custom: contperf-fy21q3
ms.reviewer: mathoma
ms.openlocfilehash: 1dd05395d921e2a75a56db353e0b0c740b094e49
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164510"
---
# <a name="checklist-best-practices-for-sql-server-on-azure-vms"></a>검사 목록: Azure VM의 SQL Server에 대한 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VMs(Virtual Machines)에서 SQL Server의 성능을 최적화하기 위한 일련의 지침 및 모범 사례로 빠른 검사 목록을 제공합니다. 

자세한 내용은 이 시리즈의 다른 문서([검사 목록](performance-guidelines-best-practices-checklist.md), [VM 크기](performance-guidelines-best-practices-vm-size.md), [스토리지](performance-guidelines-best-practices-storage.md), [보안](security-considerations-best-practices.md), [HADR 구성](hadr-cluster-best-practices.md), [기준 수집](performance-guidelines-best-practices-collect-baseline.md))를 참조하세요. 


## <a name="overview"></a>개요

Azure Virtual Machines에서 SQL Server를 실행하는 동안 온-프레미스 서버 환경의 SQL Server에 적용할 수 있는 동일한 데이터베이스 성능 튜닝 옵션을 계속 사용합니다. 그러나 퍼블릭 클라우드의 관계형 데이터베이스 성능은 가상 머신의 크기 및 데이터 디스크의 구성과 같은 많은 요인에 따라 달라집니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크로드가 적은 경우 모든 권장 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

## <a name="vm-size"></a>VM 크기

다음은 Azure VM에서 SQL Server 실행에 대한 VM 크기 모범 사례의 빠른 검사 목록입니다. 

- [Standard_M8-4ms](../../../virtual-machines/m-series.md), [E4ds_v4](../../../virtual-machines/edv4-edsv4-series.md#edv4-series) 또는 [DS12_v2](../../../virtual-machines/dv2-dsv2-series-memory.md#dsv2-series-11-15) 이상과 같이 vCPU가 4개 이상인 VM 크기를 사용합니다. 
- SQL Server 워크로드의 최고 성능을 위해 [메모리가 최적화된](../../../virtual-machines/sizes-memory.md) 가상 머신 크기를 사용합니다. 
- [DSv2 11-15](../../../virtual-machines/dv2-dsv2-series-memory.md), [Edsv4](../../../virtual-machines/edv4-edsv4-series.md) 시리즈, [M](../../../virtual-machines/m-series.md), [Mv2](../../../virtual-machines/mv2-series.md) 시리즈는 OLTP 워크로드에 필요한 최적의 메모리 대 vCore 비율을 제공합니다. 두 M 시리즈 VMs는 중요 업무용 워크로드에 필요한 가장 높은 메모리 대 vCore 비율을 제공하며, 데이터 웨어하우스 워크로드에도 적합합니다. 
- 중요 업무용 및 데이터 웨어하우스 워크로드에 대한 메모리 대 vCore 비율을 높이는 것이 좋습니다. 
- 최적의 SQL Server 성능을 위해 SQL Server 설정 및 스토리지 옵션이 구성되었으므로 Azure Virtual Machine Marketplace 이미지를 사용합니다. 
- 대상 워크로드의 성능 특성을 수집하고 수집한 정보를 사용하여 비즈니스에 적합한 VM 크기를 결정합니다.
- [Data Migration Assistant](https://www.microsoft.com/download/details.aspx?id=53595) [SKU 권장 사항](/sql/dma/dma-sku-recommend-sql-db) 도구를 사용하여 기존 SQL Server 워크로드에 적합한 VM 크기를 찾습니다.

자세히 알아보려면 포괄적인 [VM 크기 모범 사례](performance-guidelines-best-practices-vm-size.md)를 참조하세요. 

## <a name="storage"></a>스토리지

다음은 Azure VM에서 SQL Server를 실행하기 위한 저장소 구성 모범 사례의 빠른 검사 목록입니다. 

- 디스크 유형을 선택하기 전에 애플리케이션을 모니터링하고 SQL Server 데이터, 로그, tempdb 파일에 대한 [스토리지 대역폭 및 대기 시간 요구 사항을 결정](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements)합니다. 
- 스토리지 성능을 최적화하려면 사용 가능한 캐시되지 않은 최고 IOPS를 계획하고, [가상 머신 및 디스크 최대 가용량 사용/제한](../../../virtual-machines/premium-storage-performance.md#throttling)을 방지하면서 데이터 읽기에 대한 성능 기능으로 데이터 캐싱을 사용합니다.
- 데이터, 로그, tempdb 파일을 별도의 드라이브에 저장합니다.
    - 데이터 드라이브의 경우 [프리미엄 P30 및 P40 디스크](../../../virtual-machines/disks-types.md#premium-ssds)만 사용하여 캐시 지원의 가용성을 보장합니다.
    - 로그 드라이브의 경우 [프리미엄 P30 - P80 디스크](../../../virtual-machines/disks-types.md#premium-ssds)를 평가하는 동안 용량 및 테스트 성능과 비용에 대해 계획합니다.
      - 밀리초보다 적은 단위의 스토리지 대기 시간이 필요한 경우 트랜잭션 로그에 [Azure 울트라 디스크](../../../virtual-machines/disks-types.md#ultra-disks)를 사용합니다. 
      - M 시리즈 가상 머신 배포의 경우 Azure 울트라 디스크를 사용하는 것보다 [쓰기 가속기](../../../virtual-machines/how-to-enable-write-accelerator.md)를 사용하는 것이 좋습니다.
    - [](/sql/relational-databases/databases/tempdb-database) `D:\` 최적의 VM 크기를 선택한 후에 대부분의 SQL Server 작업에 대해 로컬 임시 SSD (기본) 드라이브에 tempdb를 넣습니다. 
      - tempdb를 배치하기에 로컬 드라이브의 용량이 부족한 경우 VM의 크기를 조정하는 것이 좋습니다. 자세한 내용은 [데이터 파일 캐싱 정책](performance-guidelines-best-practices-storage.md#data-file-caching-policies)을 참조하세요.
- 대상 가상 머신의 최대 IOPS 및 처리량 한계까지 I/O 대역폭을 늘리려면 [스토리지 공간](/windows-server/storage/storage-spaces/overview)을 사용하여 여러 Azure 데이터 디스크를 스트라이프합니다.
- 데이터 파일 디스크에 대해 [호스트 캐싱](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)을 읽기 전용으로 설정합니다.
- 로그 파일 디스크에 대해 [호스트 캐싱](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)을 없음으로 설정합니다.
    - SQL Server 파일이 들어 있는 디스크에서 읽기/쓰기 캐싱을 사용하도록 설정하지 마세요. 
    - 디스크의 캐시 설정을 변경하기 전에 항상 SQL Server 서비스를 중지합니다.
- 개발 및 테스트 워크로드의 경우 표준 스토리지를 사용하는 것이 좋습니다. 프로덕션 워크로드에는 표준 HDD/SDD를 사용하지 않는 것이 좋습니다.
- [크레딧 기반 디스크 버스팅](../../../virtual-machines/disk-bursting.md#credit-based-bursting)(P1-P20)은 소규모 개발/테스트 워크로드 및 부서 시스템인 경우에만 고려해야 합니다.
- SQL Server VM과 동일한 지역에 스토리지 계정을 프로비저닝합니다. 
- Azure 지역 중복 스토리지(지역에서 복제)를 사용하지 않도록 설정하고 스토리지 계정에서 LRS(로컬 중복 스토리지)를 사용합니다.
- 임시 `D:\` 드라이브(기본값은 4KB)가 아닌 드라이브에 배치된 모든 데이터 파일에 대해 64KB 할당 단위 크기를 사용하도록 데이터 디스크의 형식을 지정합니다. Azure Marketplace를 통해 배포된 SQL Server VMs는 할당 단위 크기로 형식이 지정된 데이터 디스크와 함께 제공되고 64KB로 설정된 스토리지 풀에 인터리빙됩니다. 


자세히 알아보려면 포괄적인 [스토리지 모범 사례](performance-guidelines-best-practices-storage.md)를 참조하세요. 

## <a name="sql-server-features"></a>SQL Server 기능

다음은 프로덕션 환경의 Azure 가상 머신에서 SQL Server 인스턴스를 실행할 때 SQL Server 구성 설정의 모범 사례에 대한 간략한 검사 목록입니다. 

- 해당하는 경우 [데이터베이스 페이지 압축](/sql/relational-databases/data-compression/data-compression)을 사용하도록 설정합니다.
- [백업 압축](/sql/relational-databases/backup-restore/backup-compression-sql-server)을 사용하도록 설정합니다.
- 데이터 파일에 [인스턴트 파일 초기화](/sql/relational-databases/databases/database-instant-file-initialization)를 사용하도록 설정합니다.
- 데이터베이스의 [자동 증가](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-autogrow)를 제한합니다.
- 데이터베이스의 [자동 축소](/troubleshoot/sql/admin/considerations-autogrow-autoshrink#considerations-for-auto_shrink)를 사용하지 않도록 설정합니다.
- 데이터베이스 자동 닫기를 사용하지 않도록 설정합니다.
- [시스템 데이터베이스](/sql/relational-databases/databases/move-system-databases)를 포함하여 모든 데이터베이스를 데이터 디스크로 이동합니다.
- SQL Server 오류 로그 및 추적 파일 디렉터리를 데이터 디스크로 이동합니다.
- 기본 백업 및 데이터베이스 파일 위치를 구성합니다.
- 최대 [SQL Server 메모리 제한](/sql/database-engine/configure-windows/server-memory-server-configuration-options#use-)을 설정하여 운영 체제에 충분한 메모리를 남겨 둡니다. ([Memory\Available Bytes를 활용](/sql/relational-databases/performance-monitor/monitor-memory-usage)하여 운영 체제 메모리 상태를 모니터링합니다.)
- [메모리의 페이지 잠금](/sql/database-engine/configure-windows/enable-the-lock-pages-in-memory-option-windows)을 사용하도록 설정합니다.
- OLTP를 많이 사용하는 환경에서 [임시 작업을 위해 최적화](/sql/database-engine/configure-windows/optimize-for-ad-hoc-workloads-server-configuration-option)를 사용하도록 설정합니다.
- 설치된 SQL Server 버전에 대한 [최신 누적 업데이트](/sql/database-engine/install-windows/latest-updates-for-microsoft-sql-server)를 평가하고 적용합니다.
- [모범 사례에 따라](/sql/relational-databases/performance/best-practice-with-the-query-store) 모든 프로덕션 SQL Server 데이터베이스에서 [쿼리 저장소](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)를 사용하도록 설정합니다.
- 중요 업무용 애플리케이션 데이터베이스에서 [자동 조정](/sql/relational-databases/automatic-tuning/automatic-tuning)을 사용하도록 설정합니다.
- 모든 [tempdb 모범 사례](/sql/relational-databases/databases/tempdb-database#optimizing-tempdb-performance-in-sql-server)를 준수하는지 확인합니다.
- 임시 D:/ 드라이브에 tempdb를 저장합니다.
- 코어당 하나의 파일로 시작하는 여러 tempdb 데이터 파일을 사용하여 [권장되는 파일 수를 사용](/troubleshoot/sql/performance/recommendations-reduce-allocation-contention#resolution)합니다(최대 8개 파일).
- SQL Server 에이전트 작업을 예약하여 [DBCC CHECKDB](/sql/t-sql/database-console-commands/dbcc-checkdb-transact-sql#a-checking-both-the-current-and-another-database), [인덱스 재구성](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#reorganize-an-index), [인덱스 다시 빌드](/sql/relational-databases/indexes/reorganize-and-rebuild-indexes#rebuild-an-index) 및 [통계 업데이트](/sql/t-sql/statements/update-statistics-transact-sql#examples) 작업을 실행합니다.
- SQL Server [트랜잭션 로그 파일](/sql/relational-databases/logs/manage-the-size-of-the-transaction-log-file#Recommendations)의 상태와 크기를 모니터링하고 관리합니다.
- 사용 중인 버전에 대해 사용할 수 있는 새로운 [SQL Server 기능](/sql/sql-server/what-s-new-in-sql-server-ver15)을 활용합니다.
- 배포를 고려하는 버전 간에 [지원되는 기능](/sql/sql-server/editions-and-components-of-sql-server-version-15)의 차이점에 주의합니다.

## <a name="azure-features"></a>사용하여 매핑

다음은 Azure VM에서 SQL Server를 실행할 때 Azure 관련 지침에 대한 모범 사례의 빠른 검사 목록입니다.

- [SQL IaaS 에이전트 확장](sql-agent-extension-manually-register-single-vm.md)에 등록하여 다양한 [기능 이점](sql-server-iaas-agent-extension-automate-management.md#feature-benefits)의 잠금을 해제합니다.
- SQL Server 작업에 가장 적합한 [백업 및 복원 전략](backup-restore.md#decision-matrix)을 활용합니다.
- 가상 머신에서 [가속화된 네트워킹을 사용할 수](../../../virtual-network/create-vm-accelerated-networking-cli.md#portal-creation) 있는지 확인합니다.
- [Azure Security Center](../../../security-center/index.yml)를 활용하여 가상 머신 배포의 전반적인 보안 상태를 향상시킵니다.
- 취약성 평가 및 Just-In-Time 액세스를 비롯한 특정 [SQL Server VM 검사](../../../security-center/defender-for-sql-introduction.md)에 대해 [Azure Security Center](https://azure.microsoft.com/services/security-center/)와 통합된 [Azure Defender](../../../security-center/azure-defender.md)를 활용하여 필요한 경우 합법적인 사용자가 가상 머신에 액세스할 수 있도록 허용하는 동시에 공격 서비스를 줄일 수 있습니다. 자세한 내용은 [취약성 평가](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md), [SQL Server VM에 대한 취약성 평가 사용](../../../security-center/defender-for-sql-on-machines-vulnerability-assessment.md) 및 [Just-In-Time 액세스](../../../security-center/just-in-time-explained.md)를 참조하세요. 
- [Azure Advisor](../../../advisor/advisor-overview.md)를 활용하여 [성능](../../../advisor/advisor-performance-recommendations.md), [비용](../../../advisor/advisor-cost-recommendations.md), [안정성](../../../advisor/advisor-high-availability-recommendations.md), [운영 효율성](../../../advisor/advisor-operational-excellence-recommendations.md) 및 [보안 권장 사항](../../../advisor/advisor-security-recommendations.md)을 해결합니다.
- [Azure Monitor](../../../azure-monitor/vm/monitor-virtual-machine.md)를 활용하여 SQL Server 환경에서 원격 분석 데이터를 수집, 분석 및 조치합니다. 여기에는 [VM 인사이트](../../../azure-monitor/vm/vminsights-overview.md)를 사용하여 인프라 문제를 식별하고 심층 진단을 위해 [Log Analytics](../../../azure-monitor/logs/log-query-overview.md)로 데이터를 모니터링하는 작업이 포함됩니다.
- 개발 및 테스트 환경에 대해 [자동 종료](../../../automation/automation-solution-vm-management.md)를 사용하도록 설정합니다. 
- 비즈니스 연속성 SLA를 충족하는 HADR(고가용성 및 재해 복구) 솔루션을 구현합니다. Azure VM에서 SQL Server에 사용할 수 있는 [HADR 옵션](business-continuity-high-availability-disaster-recovery-hadr-overview.md#deployment-architectures)을 참조하세요. 
- Azure Portal(지원 + 문제 해결)을 사용하여 [리소스 상태](../../../service-health/resource-health-overview.md) 및 기록을 평가합니다. 필요한 경우 새 지원 요청을 제출합니다.

## <a name="hadr-configuration"></a>HADR 구성

[Always On 가용성 그룹](availability-group-overview.md) 및 [장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)와 같은 HADR(고가용성 및 재해 복구) 기능은 기본 [Windows Server 장애 조치(Failover) 클러스터](hadr-windows-server-failover-cluster-overview.md) 기술을 사용합니다. 클라우드 환경을 더 잘 지원하기 위해 HADR 설정을 수정하기 위한 모범 사례를 검토합니다. 

Windows 클러스터의 경우 모범 사례를 고려합니다. 

* 일시적인 네트워크 실패 또는 Azure 플랫폼 유지 관리로 인한 예기치 않은 중단을 방지하기 위해 덜 적극적인 매개 변수로 클러스터를 변경합니다. 자세히 알아보려면 [하트비트 및 임계값 설정](hadr-cluster-best-practices.md#heartbeat-and-threshold)을 참조하세요. Windows Server 2012 이상에서는 다음 권장 값을 사용합니다. 
   - **SameSubnetDelay**: 1초
   - **SameSubnetThreshold**: 하트비트 40개
   - **CrossSubnetDelay**: 1초
   - **CrossSubnetThreshold**: 하트비트 40개
* VM을 가용성 집합 또는 다른 가용성 영역에 배치합니다.  자세한 내용은 [VM 가용성 설정](hadr-cluster-best-practices.md#vm-availability-settings)을 참조하세요. 
* 클러스터 노드당 하나의 NIC 및 단일 서브넷을 사용합니다. 
* 클러스터 [쿼럼 투표](hadr-cluster-best-practices.md#quorum-voting)를 구성하여 3개 이상의 홀수 투표를 사용합니다. DR 지역에 투표를 할당하지 마세요. 
* 리소스 제약으로 인한 예기치 않은 재시작 또는 장애 조치(failover)를 방지하기 위해 [리소스 제한](hadr-cluster-best-practices.md#resource-limits)을 신중하게 모니터링합니다.
   - OS, 드라이버 및 SQL Server가 최신 빌드인지 확인합니다. 
   - Azure VM에서 SQL Server에 대한 성능을 최적화합니다. 자세히 알아보려면 이 문서의 다른 섹션을 검토합니다. 
   - 리소스 제한을 방지하기 위해 워크로드를 줄이거나 분산합니다. 
   - 제약을 방지하기 위해 제한이 더 높은 VM이나 디스크로 이동합니다. 

SQL Server 가용성 그룹 또는 장애 조치(failover) 클러스터 인스턴스의 경우 다음 모범 사례를 고려합니다. 

* 예기치 않은 오류가 자주 발생하는 경우 이 문서의 나머지 부분에서 설명하는 성능 모범 사례를 따릅니다. 
* SQL Server VM 성능 최적화로 예기치 않은 장애 조치(failover)가 해결되지 않는 경우 가용성 그룹 또는 장애 조치(failover) 클러스터 인스턴스에 대한 [모니터링 완화](hadr-cluster-best-practices.md#relaxed-monitoring)를 고려합니다. 하지만 문제의 근본 원인이 해결되지 않을 수 있고 오류 발생 가능성을 줄여 증상을 일시적으로 덮는 데 그칠 수 있습니다. 계속해서 근본 원인을 조사하고 해결해야 할 수 있습니다. Windows Server 2012 이상에서는 다음 권장 값을 사용합니다. 
   - **임대 시간 제한**: 이 등식을 사용하여 최대 임대 시간 제한 값을 계산합니다.   
    `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.    
    40초부터 시작합니다. 이전에 권장되었던 완화된 `SameSubnetThreshold` 및 `SameSubnetDelay` 값을 사용하는 경우 임대 시간 제한 값이 80초를 초과해서는 안 됩니다. 
   - **지정 된 기간 동안 최대 실패**:이 값을 6으로 설정할 수 있습니다.
   - **Healthcheck timeout**: 처음에는이 값을 6만로 설정 하 고 필요에 따라 조정할 수 있습니다. 
* VNN(가상 네트워크 이름)을 사용하여 HADR 솔루션에 연결하는 경우 클러스터의 범위가 하나의 서브넷에 불과하더라도 연결 문자열에서 `MultiSubnetFailover = true`를 지정합니다. 
   - 클라이언트에서 `MultiSubnetFailover = True`를 지원하지 않는 경우 `RegisterAllProvidersIP = 0` 및 `HostRecordTTL = 300`을 설정하여 더 짧은 기간 동안 클라이언트 자격 증명을 캐시해야 할 수 있습니다. 하지만 이렇게 하면 DNS 서버에 추가 쿼리가 발생할 수 있습니다. 
- DNN(분산형 네트워크 이름)을 사용하여 HADR 솔루션에 연결하려면 다음 사항을 고려합니다.
   - `MultiSubnetFailover = True`를 지원하는 클라이언트 드라이버를 사용해야 하고, 이 매개 변수가 연결 문자열에 있어야 합니다. 
   - 가용성 그룹의 DNN 수신기에 연결할 때 연결 문자열에서 고유한 DNN 포트를 사용합니다. 
- 기본 가용성 그룹에 대한 데이터베이스 미러링 연결 문자열을 사용하여 부하 분산 장치 또는 DNN에 대한 필요성을 무시합니다. 
- 불일치 I/O가 발생하지 않도록 고가용성 솔루션 배포 전에 VHD의 섹터 크기를 확인합니다. 자세히 알아보려면 [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c)를 참조하세요. 


자세히 알아보려면 포괄적인 [HADR 모범 사례](hadr-cluster-best-practices.md)를 참조하세요. 


## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 시리즈의 다른 문서를 참조하세요.

- [VM 크기](performance-guidelines-best-practices-vm-size.md)
- [스토리지](performance-guidelines-best-practices-storage.md)
- [보안](security-considerations-best-practices.md)
- [HADR 설정](hadr-cluster-best-practices.md)
- [기준 수집](performance-guidelines-best-practices-collect-baseline.md)

보안 모범 사례는 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](security-considerations-best-practices.md)을 참조하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
