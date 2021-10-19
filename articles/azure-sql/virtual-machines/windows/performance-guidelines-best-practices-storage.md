---
title: '스토리지: 성능 모범 사례 및 지침'
description: Azure VM(Virtual Machine)에서 SQL Server의 성능을 최적화하기 위한 스토리지 모범 사례와 지침을 제공합니다.
services: virtual-machines-windows
documentationcenter: na
author: bluefooted
editor: ''
tags: azure-service-management
ms.assetid: a0c85092-2113-4982-b73a-4e80160bac36
ms.service: virtual-machines-sql
ms.subservice: performance
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/25/2021
ms.author: pamela
ms.reviewer: mathoma
ms.openlocfilehash: 83d47a3b1d42233df6f90690e88a898feaccb70b
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130161522"
---
# <a name="storage-performance-best-practices-for-sql-server-on-azure-vms"></a>스토리지: Azure VM의 SQL Server에 대한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VM(Virtual Machine)에서 SQL Server의 성능을 최적화하기 위한 스토리지 모범 사례와 지침을 제공합니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크로드가 적은 경우 모든 권장 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

자세한 내용은 이 시리즈의 다른 문서([검사 목록](performance-guidelines-best-practices-checklist.md), [VM 크기](performance-guidelines-best-practices-vm-size.md), [보안](security-considerations-best-practices.md), [HADR 구성](hadr-cluster-best-practices.md) 및 [기준 수집](performance-guidelines-best-practices-collect-baseline.md))를 참조하세요. 

## <a name="checklist"></a>검사 목록

이 문서의 나머지 부분에서 자세히 설명하는 스토리지 모범 사례에 대한 간략한 개요를 보려면 다음 검사 목록을 검토하세요. 

- 디스크 유형을 선택하기 전에 애플리케이션을 모니터링하고 SQL Server 데이터, 로그, tempdb 파일에 대한 [스토리지 대역폭 및 대기 시간 요구 사항을 결정](../../../virtual-machines/premium-storage-performance.md#counters-to-measure-application-performance-requirements)합니다. 
- 스토리지 성능을 최적화하기 위해 사용 가능한 캐시되지 않은 최고 IOPS를 계획하고, [가상 머신 및 디스크 상한 설정](../../../virtual-machines/premium-storage-performance.md#throttling)을 방지하면서 데이터 읽기에 대한 성능 기능으로 데이터 캐싱을 사용합니다.
- 데이터, 로그, tempdb 파일을 별도의 드라이브에 저장합니다.
    - 데이터 드라이브의 경우 [프리미엄 P30 및 P40 디스크](../../../virtual-machines/disks-types.md#premium-ssds)만 사용하여 캐시 지원의 가용성을 보장합니다.
    - 로그 드라이브의 경우 [프리미엄 P30 - P80 디스크](../../../virtual-machines/disks-types.md#premium-ssds)를 평가하는 동안 용량 및 테스트 성능과 비용에 대해 계획합니다.
      - 밀리초보다 적은 단위의 스토리지 대기 시간이 필요한 경우 트랜잭션 로그에 [Azure 울트라 디스크](../../../virtual-machines/disks-types.md#ultra-disks)를 사용합니다.
      - M 시리즈 가상 머신 배포의 경우 Azure 울트라 디스크를 사용하는 것보다 [쓰기 가속기](../../../virtual-machines/how-to-enable-write-accelerator.md)를 사용하는 것이 좋습니다.
    - 최적의 VM 크기를 선택한 후 대부분의 SQL Server 워크로드를 위해 로컬 임시 SSD `D:\` 드라이브에 [tempdb](/sql/relational-databases/databases/tempdb-database)를 배치합니다. 
      - tempdb를 배치하기에 로컬 드라이브의 용량이 부족한 경우 VM의 크기를 조정하는 것이 좋습니다. 자세한 내용은 [데이터 파일 캐싱 정책](#data-file-caching-policies)을 참조하세요.
- 대상 가상 머신의 최대 IOPS 및 처리량 한계까지 I/O 대역폭을 늘리려면 [스토리지 공간](/windows-server/storage/storage-spaces/overview)을 사용하여 여러 Azure 데이터 디스크를 스트라이프합니다.
- 데이터 파일 디스크에 대해 [호스트 캐싱](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)을 읽기 전용으로 설정합니다.
- 로그 파일 디스크에 대해 [호스트 캐싱](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)을 없음으로 설정합니다.
    - SQL Server 파일이 들어 있는 디스크에서 읽기/쓰기 캐싱을 사용하도록 설정하지 마세요. 
    - 디스크의 캐시 설정을 변경하기 전에 항상 SQL Server 서비스를 중지합니다.
- 개발 및 테스트 워크로드와 장기 백업 보관에는 표준 스토리지를 사용하는 것이 좋습니다. 프로덕션 워크로드에는 표준 HDD/SDD를 사용하지 않는 것이 좋습니다.
- [크레딧 기반 디스크 버스팅](../../../virtual-machines/disk-bursting.md#credit-based-bursting)(P1-P20)은 소규모 개발/테스트 워크로드 및 부서 시스템인 경우에만 고려해야 합니다.
- 임시 `D:\` 드라이브(기본값은 4KB)가 아닌 드라이브에 배치된 모든 데이터 파일에 대해 64KB 블록 크기(할당 단위 크기)를 사용하도록 데이터 디스크의 형식을 지정합니다. Azure Marketplace를 통해 배포된 SQL Server VM은 블록 크기로 형식이 지정된 데이터 디스크와 함께 제공되고 64KB로 설정된 스토리지 풀에 인터리빙됩니다. 

스토리지 검사 목록을 다른 목록과 비교하려면 포괄적인 [성능 모범 사례 검사 목록](performance-guidelines-best-practices-checklist.md)을 참조하세요. 

## <a name="overview"></a>개요

Azure VM에서 SQL Server 워크로드에 대한 가장 효율적인 구성을 찾으려면 먼저 [비즈니스 애플리케이션의 스토리지 성능을 측정](performance-guidelines-best-practices-collect-baseline.md#storage)하여 시작합니다. 스토리지 요구 사항을 파악한 후에는 적절한 메모리 대 vCore 비율을 사용하여 필요한 IOPS 및 처리량을 지원하는 가상 머신을 선택합니다. 

워크로드에 대한 스토리지 확장성이 충분하고 비즈니스의 용량 및 성능 요구 사항을 충족하는 디스크를 혼합(일반적으로 스토리지 풀)하여 VM 크기를 선택합니다. 

디스크 유형은 디스크에서 호스트 되는 파일 유형과 최고 성능 요구 사항에 따라 달라집니다.

> [!TIP]
> Azure Portal을 통해 SQL Server VM을 프로비전하면 스토리지 구성 프로세스를 안내하고, 데이터 및 로그 파일에 대 한 별도의 스토리지 풀 만들기, `D:\` 드라이브에 tempdb 대상 지정, 최적의 캐싱 정책 사용과 같은 대부분의 스토리지 모범 사례를 구현할 수 있습니다. 스토리지를 프로비전하고 구성하는 방법에 대한 자세한 내용은 [SQL VM 스토리지 구성](storage-configuration.md)을 참조하세요. 

## <a name="vm-disk-types"></a>VM 디스크 유형

디스크의 성능 수준에서 선택할 수 있습니다. 기본 스토리지로 사용할 수 있는 관리 디스크 유형(성능 향상 기능으로 표시됨)은 표준 HDD(하드 디스크 드라이브), 표준 SSD, 프리미엄 SSD 및 울트라 디스크입니다. 

디스크의 성능은 용량이 4GiB 인 P1 및 120 IOPS와 같은 [프리미엄 디스크 레이블](../../../virtual-machines/disks-types.md#premium-ssds)을 사용하여 그룹화되며 스토리지가 32TiB인 P80 및 2만 IOPS로 디스크를 늘립니다. 프리미엄 스토리지는 일부 워크로드에 대한 읽기 및 쓰기 성능을 개선하는 데 도움이 되는 스토리지 캐시를 지원합니다. 자세한 내용은 [관리 디스크 개요](../../../virtual-machines/managed-disks-overview.md)를 참조하세요. 

Azure VM에서 SQL Server(OS 디스크, 임시 디스크 및 데이터 디스크)에 대해 고려해야 할 세 가지 주요 [디스크 유형](../../../virtual-machines/managed-disks-overview.md#disk-roles)도 있습니다. 운영 체제 드라이브 `(C:\)` 및 임시 드라이브 `(D:\)`에 저장된 항목을 신중하게 선택합니다. 

### <a name="operating-system-disk"></a>운영 체제 디스크

운영 체제 디스크는 운영 체제의 실행 버전으로 부팅하고 탑재할 수 있는 VHD이며 `C:\` 드라이브로 레이블이 지정됩니다. Azure Virtual Machine을 만들 때 플랫폼에서는 운영 체제 디스크에 대한 VM에 하나 이상의 디스크를 연결합니다. `C:\` 드라이브는 애플리케이션 설치 및 파일 구성에 대한 기본 위치입니다. 

프로덕션 SQL Server 환경의 경우 데이터 파일, 로그 파일, 오류 로그에 운영 체제 디스크를 사용하지 마세요. 

### <a name="temporary-disk"></a>임시 디스크

Azure 가상 머신은 임시 디스크(`D:\` 드라이브로 레이블이 지정됨)라는 다른 디스크를 포함합니다. 가상 머신 시리즈 및 크기에 따라 이 디스크의 용량은 달라질 수 있습니다. 임시 디스크는 사용 후 삭제됩니다. 즉, 가상 머신을 다시 시작하거나(예: 할당 해제 후 다시 할당) 다른 호스트로 이동할 때(예: [서비스 복구](/troubleshoot/azure/virtual-machines/understand-vm-reboot)) 디스크 스토리지를 다시 만듭니다. 

임시 스토리지 드라이브는 원격 스토리지에 유지되지 않으므로 사용자 데이터베이스 파일, 트랜잭션 로그 파일 또는 보존되어야 하는 항목을 저장해서는 안 됩니다. 

로컬 캐시를 사용하는 데 문제가 있는 경우를 제외하고 SQL Server 워크로드에 대한 로컬 임시 SSD `D:\` 드라이브에 tempdb를 넣습니다. [임시 디스크가 없는](../../../virtual-machines/azure-vms-no-temp-disk.yml) 가상 머신을 사용하는 경우, 캐싱을 읽기 전용으로 설정하여 자체 격리된 디스크 또는 스토리지 풀에 tempdb를 저장하는 것이 좋습니다. 자세한 내용은 [tempdb 데이터 캐싱 정책](performance-guidelines-best-practices-storage.md#data-file-caching-policies)을 참조하세요.

### <a name="data-disks"></a>데이터 디스크

데이터 디스크는 단일 디스크에서 가상 머신에 제공할 수 있는 용량과 성능을 초과하기 위해 [스토리지 풀](/windows-server/storage/storage-spaces/overview)에서 자주 만들어지는 원격 스토리지 디스크입니다.

워크로드의 IOPS, 처리량 및 용량 요구 사항을 충족하는 최소 디스크 수를 연결합니다. 크기를 조정하려는 가장 작은 가상 머신의 최대 데이터 디스크 수를 초과하지 마세요.

성능 요구 사항에 가장 적합하도록 프로비전된 데이터 디스크에 데이터 및 로그 파일을 저장합니다. 

임시 `D:\` 드라이브(기본값은 4KB)가 아닌 드라이브에 배치된 모든 데이터 파일에 대해 64KB 할당 단위 크기를 사용하도록 데이터 디스크의 형식을 지정합니다. Azure Marketplace를 통해 배포된 SQL Server VMs는 할당 단위 크기로 형식이 지정된 데이터 디스크와 함께 제공되고 64KB로 설정된 스토리지 풀에 인터리빙됩니다. 

> [!NOTE]
> azure [Blob storage](/sql/relational-databases/databases/sql-server-data-files-in-microsoft-azure) 또는 [azure premium 파일 공유](../../../storage/files/storage-how-to-create-file-share.md)와 같은 [SMB 저장소](/sql/database-engine/install-windows/install-sql-server-with-smb-fileshare-as-a-storage-option) 에서 직접 SQL Server 데이터베이스 파일을 호스팅할 수도 있지만 최상의 성능, 안정성 및 기능 가용성을 위해 [azure managed disks](../../../virtual-machines/managed-disks-overview.md) 를 사용 하는 것이 좋습니다.

## <a name="premium-disks"></a>프리미엄 디스크:

프로덕션 SQL Server 워크로드에 대한 데이터 및 로그 파일에 프리미엄 SSD 디스크를 사용합니다. 프리미엄 SSD IOPS 및 대역폭은 [디스크 크기와 유형에](../../../virtual-machines/disks-types.md)따라 달라집니다. 

프로덕션 워크로드의 경우 SQL Server 데이터 파일에 대한 P30 및/또는 P40 디스크를 사용하여 캐싱을 지원하고 SQL Server 트랜잭션 로그 파일에 대해 P80까지 P30을 사용합니다.  최적의 총 소유 비용을 위해, 데이터 및 로그 파일에 대해 P30(5000 IOPS200MBPS)부터 시작하고 가상 머신 디스크 수를 제어해야 할 때 더 높은 용량을 선택합니다.

OLTP 워크로드의 경우 최대 사용 시간 및 `Disk Reads/sec` + `Disk Writes/sec` 성능 카운터를 사용하여 워크로드를 사용하는 성능 요구 사항과 디스크(또는 스토리지 풀)당 대상 IOPS를 일치시킵니다. 데이터 웨어하우스 및 보고 워크로드의 경우, 최대 사용 시간의 워크로드 및 `Disk Read Bytes/sec` + `Disk Write Bytes/sec`를 사용하여 대상 처리량을 일치시킵니다. 

스토리지 공간을 사용하여 최적의 성능을 획득하고 두 개의 풀(로그 파일에 하나 데이터 파일에 다른 하나)을 구성합니다. 디스크 스트라이프를 사용하지 않는 경우, 별도의 드라이브에 매핑된 프리미엄 SSD 디스크 2개(로그 파일용 1개 및 데이터용 1개)를 사용합니다.

스토리지 풀의 일부로 사용되는 디스크당 [프로비전된 IOPS 및 처리량](../../../virtual-machines/disks-types.md#premium-ssds)입니다. 디스크의 결합된 IOPS 및 처리량 기능은 가상 머신의 처리량 제한까지 이르는 최대 용량입니다.

가장 좋은 방법은 IOPS(및 처리량) 및 용량에 대한 최소 요구 사항을 충족하면서 최대한 적은 수의 디스크를 사용하는 것입니다. 그러나, 소형 디스크가 많은 것이 대형 디스크가 적은 것보다 가격과 성능의 균형을 유지하는 데 좋습니다.

### <a name="scaling-premium-disks"></a>프리미엄 디스크 스케일링

Azure 관리 디스크를 처음 배포하는 경우, 해당 디스크의 성능 계층은 프로비전된 디스크 크기를 기준으로 합니다. 배포 시 성능 계층을 지정하거나 디스크 크기를 변경하지 않고 나중에 변경합니다. 요구가 증가하면 비즈니스 요구에 맞게 성능 수준을 높일 수 있습니다. 

성능 계층을 변경하면 관리자가 [디스크 버스팅](../../../virtual-machines/disk-bursting.md#credit-based-bursting)에 의존하지 않고 높은 수요를 준비하고 충족할 수 있습니다. 

스토리지 성능 계층에 맞게 요금이 청구되는 경우, 필요에 따라 더 높은 성능을 사용할 수 있습니다. 용량을 늘리지 않고 성능 요구 사항에 맞게 계층을 업그레이드합니다. 추가 성능이 더 이상 필요하지 않은 경우 원래 계층으로 돌아갑니다.

이 비용 효율적이고 일시적인 성능 확장은 쇼핑, 성능 테스트, 학습 이벤트 및 단기간 성능 향상이 필요한 기타 짧은 기간과 같은 대상 이벤트에 강력한 사용 사례입니다. 

자세한 내용은 [관리 디스크의 성능 계층](../../../virtual-machines/disks-change-performance.md)을 참조하세요. 

## <a name="azure-ultra-disk"></a>Azure 울트라 디스크

대기 시간이 줄어든 밀리초 미만 응답 시간이 필요한 경우, SQL Server 로그 드라이브 또는 I/O 대기 시간에 매우 민감한 애플리케이션의 데이터 드라이브에도 [Azure 울트라 디스크](../../../virtual-machines/disks-types.md#ultra-disks)를 사용하는 것이 좋습니다.

용량 및 IOPS를 독립적으로 확장할 수 있는 울트라 디스크를 구성할 수 있습니다. 울트라 디스크 관리자는 애플리케이션 요구 사항에 따라 용량, IOPS 및 처리량 요구 사항으로 디스크를 프로비전할 수 있습니다. 

울트라 디스크는 모든 VM 시리즈에서 지원되지 않으며 지역 가용성, 중복성 및 Azure Backup 지원과 같은 기타 제한 사항이 있습니다. 자세한 내용은, 전체 제한 사항 목록에 대한 [Azure 울트라 디스크 사용](../../../virtual-machines/disks-enable-ultra-ssd.md)의 내용을 참조하세요.

## <a name="standard-hdds-and-ssds"></a>표준 HDD 및 SSD

[표준 HDD](../../../virtual-machines/disks-types.md#standard-hdds) 및 SSD는 대기 시간 및 대역폭이 다양하므로 개발/테스트 워크로드에만 권장됩니다. 프로덕션 워크로드에는 프리미엄 SSD를 사용해야 합니다. 표준 SSD(개발/테스트 시나리오)를 사용하는 경우 [VM 크기](../../../virtual-machines/sizes.md?toc=/azure/virtual-machines/windows/toc.json)에서 지원하는 최대 데이터 디스크 수를 추가하고 최상의 성능을 위해 스토리지 공간을 사용하여 디스크 스트라이프를 사용하는 것이 좋습니다.

## <a name="caching"></a>캐싱

프리미엄 스토리지 캐싱을 지원하는 가상 머신은 Azure BlobCache 또는 호스트 캐싱이라는 추가 기능을 활용하여 가상 머신의 IOPS 및 처리량 기능을 확장할 수 있습니다. 프리미엄 스토리지 및 프리미엄 캐싱에 사용하도록 설정된 가상 머신에는 스토리지 성능을 향상시키기 위해 함께 사용할 수 있는 두 가지 스토리지 대역폭 제한이 있습니다.

캐싱을 사용하지 않는 IOPS 및 MBps 처리량은 가상 머신의 캐시되지 않은 디스크 처리량 제한에 포함됩니다. 캐시된 최대 제한은 사용량 증가 및 예기치 않은 최대 사용량을 해결하는 데 도움이 되는 읽기 버퍼를 추가로 제공합니다.

추가 비용 없이 데이터 드라이브에 대한 읽기 성능을 확실히 향상시키기 위해 옵션이 지원될 때마다 프리미엄 캐싱을 사용하도록 설정합니다. 

Azure BlobCache(캐시된 IOPS 및 처리량)에 대한 읽기 및 쓰기는 가상 머신의 캐시되지 않은 IOPS 및 처리량 한도에 대해 계산되지 않습니다.

> [!NOTE]
> 4TiB 이상(P50 이상)의 디스크에서 디스크 캐싱은 지원되지 않습니다. 여러 디스크가 VM에 연결되어 있는 경우 4TiB보다 작은 디스크는 캐싱을 지원합니다. 자세한 내용은 [디스크 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 참조하세요. 

### <a name="uncached-throughput"></a>캐시되지 않은 처리량

최대 캐시되지 않은 디스크 IOPS 및 처리량은 가상 머신에서 처리할 수 있는 최대 원격 스토리지 한도입니다. 이 제한은 가상 머신에서 정의되며 기본 디스크 스토리지의 제한이 아닙니다. 이 제한은 임시 드라이브( `D:\` 드라이브) 또는 OS 드라이브에 대한 로컬 I/O가 아닌 VM에 원격으로 연결된 데이터 드라이브에 대한 I/O에만 적용됩니다.

VM에 사용할 수 있는 캐시되지 않은 IOPS 및 처리량의 양은 가상 머신에 대한 설명서에서 확인할 수 있습니다.

예를 들어, [M 시리즈](../../../virtual-machines/m-series.md) 설명서는 Standard_M8ms VM에 대한 최대 캐시되지 않은 처리량이 5000 IOPS 및 125MBps의 캐시되지 않은 디스크 처리량임을 보여 줍니다. 

![M 시리즈의 캐시되지 않은 디스크 처리량 설명서를 보여주는 스크린샷](./media/performance-guidelines-best-practices/m-series-table.png)

마찬가지로 Standard_M32ts에서 2만의 캐시되지 않은 디스크 IOPS 및 500MBps의 캐시되지 않은 디스크 처리량을 지원하는지 확인할 수 있습니다. 이 제한은 기본 프리미엄 디스크 스토리지에 관계 없이 가상 머신 수준에서 관리됩니다.

자세한 내용은, [캐시되지 않은 한도 및 캐시된 한도](../../../virtual-machines/disks-performance.md#virtual-machine-uncached-vs-cached-limits)를 참조하세요.


### <a name="cached-and-temp-storage-throughput"></a>캐시된 스토리지 및 임시 스토리지 처리량

최대 캐시 및 임시 저장소 처리량 제한은 가상 머신에 대한 캐시되지 않은 처리량 제한과는 별개의 제한입니다. Azure BlobCache는 가상 머신 호스트의 임의 액세스 메모리와 로컬로 연결된 SSD의 조합으로 구성됩니다. 가상 머신 내에 임시 드라이브(`D:\` 드라이브)도 이 로컬 SSD에 호스트됩니다.

캐시된 최대 및 임시 스토리지 처리량 제한은 호스트 캐싱이 **사용되는 경우에만** 로컬 임시 드라이브(`D:\` 드라이브)와 Azure BlobCache에 대한 I/O를 제어합니다. 

프리미엄 스토리지에서 캐싱이 설정된 경우, 가상 머신은 원격 스토리지의 캐시되지 않은 VM IOPS 및 처리량 제한을 초과하여 확장할 수 있습니다.  

특정 가상 머신만 프리미엄 스토리지 및 프리미엄 스토리지 캐싱을 모두 지원합니다(가상 머신 설명서에서 확인해야 함). 예를 들어, [M 시리즈](../../../virtual-machines/m-series.md) 설명서는 프리미엄 스토리지 및 프리미엄 스토리지 캐싱이 모두 지원됨을 나타냅니다. 

![M 시리즈 프리미엄 스토리지 지원을 보여주는 스크린샷](./media/performance-guidelines-best-practices/m-series-table-premium-support.png)

캐시 제한은 가상 머신 크기에 따라 달라집니다. 예를 들어, Standard_M8ms VM은 10000의 캐시된 디스크 IOPS 및 1000MBps의 캐시된 디스크 처리량을 793GiB의 총 캐시 크기와 함께 지원합니다. 마찬가지로, Standard_M32ts VM은 40000의 캐시된 디스크 IOPS 및 400MBps의 캐시된 디스크 처리량을 3174GiB의 총 캐시 크기로 지원합니다. 

![M 시리즈의 캐시된 디스크 처리량 설명서를 보여주는 스크린샷](./media/performance-guidelines-best-practices/m-series-table-cached-temp.png)

기존 VM에서 호스트 캐싱을 수동으로 사용하도록 설정할 수 있습니다. 가상 머신의 캐싱 정책을 변경하기 전에 모든 애플리케이션 워크로드 및 SQL Server 서비스를 중지합니다. 가상 머신 캐시 설정을 변경하면 설정이 적용된 후 대상 디스크가 분리되어 다시 첨부됩니다.

### <a name="data-file-caching-policies"></a>데이터 파일 캐싱 정책

스토리지 캐싱 정책은 드라이브에서 호스트되는 SQL Server 데이터 파일의 유형에 따라 달라집니다. 

다음 테이블에서는 SQL Server 데이터 유형에 따라 권장되는 캐싱 정책을 요약하여 보여줍니다. 

|SQL Server 디스크 |권장 |
|---------|---------|
| **데이터 디스크** | SQL Server 데이터 파일을 호스트하는 디스크에 `Read-only` 캐싱을 사용하도록 설정합니다. <br/> 캐시에서 읽기는 데이터 디스크에서 캐시되지 않은 읽기를 수행하는 것보다 빠릅니다. <br/> 캐시되지 않은 IOPS 및 처리량 외에도 캐시된 IOPS 및 처리량은 VM 한도 내의 가상 머신에서 사용할 수 있는 총 성능을 제공하지만 실제 성능은 캐시를 사용하는 워크로드의 기능 (캐시 적중률)에 따라 달라집니다. <br/>|
|**트랜잭션 로그 디스크**|트랜잭션 로그를 호스팅하는 디스크에 대한 캐싱 정책을 `None`으로 설정합니다.  트랜잭션 로그 디스크에 캐싱을 사용하도록 설정하는 경우, 성능의 이점을 얻을 수 없으며 실제로, 로그 드라이브에서 `Read-only` 또는 `Read/Write` 캐싱을 사용하도록 설정하면 드라이브에 대한 쓰기 성능이 저하되고 데이터 드라이브에서 읽기에 사용할 수 있는 캐시의 양이 줄어듭니다.  |
|**운영 OS 디스크** | 기본 캐싱 정책은 OS 드라이브에 대해 `Read/write`입니다. <br/> OS 드라이브의 캐싱 수준을 변경하지 않는 것이 좋습니다.  |
| **tempdb**| 용량이 부족하여 임시 드라이브 `D:\`에 tempdb를 배치할 수 없는 경우, 더 큰 임시 드라이브를 가져오도록 가상 머신의 크기를 조정하거나 `Read-only` 캐싱이 구성된 별도의 데이터 드라이브에 tempdb를 배치하세요. <br/> 가상 머신 캐시와 임시 드라이브 모두 로컬 SSD를 사용하므로 tempdb I/O로 크기를 조정하면 임시 드라이브에 호스팅될 때 캐시된 IOPS 및 처리량 가상 머신 한도에 대해 이를 염두에 두어야 합니다.| 
| | | 

> [!IMPORTANT]
> Azure 디스크의 캐시 설정이 변경되면 대상 디스크를 분리하고 다시 연결합니다. SQL Server 데이터, 로그 또는 애플리케이션 파일을 호스트하는 디스크에 대한 캐시 설정을 변경하는 경우 데이터 손상 방지를 위해 다른 관련 서비스와 함께 SQL Server 서비스를 중지해야 합니다.

자세히 알아보려면 [디스크 캐싱](../../../virtual-machines/premium-storage-performance.md#disk-caching)을 참조하세요. 


## <a name="disk-striping"></a>디스크 스트라이프

로그 파일 및 tempdb를 포함하여 SQL 데이터 파일에 필요한 처리량 및 대역폭을 분석합니다. 처리량 및 대역폭 제한은 VM 크기에 따라 달라집니다. 자세한 내용은 [VM 크기](../../../virtual-machines/sizes.md)를 참조하세요.

추가 데이터 디스크를 추가하고 추가 처리량에 디스크 스트라이프를 사용할 수 있습니다. 예를 들어, 12000IOPS 및 180MB/초 처리량을 요구하는 애플리케이션은 3개의 스트라이프 P30 디스크를 사용하여 15000IOPS 및 600MB/s 처리량을 제공할 수 있습니다. 

디스크 스트라이프를 구성하려면 [디스크 스트라이프](storage-configuration.md#disk-striping)를 참조하세요. 

## <a name="disk-capping"></a>디스크 상한 설정 

디스크 및 가상 머신 수준 모두에서 처리량 제한이 있습니다. VM당 및 디스크당 최대 IOPS 제한은 서로 다르고 독립적입니다.

이러한 한도를 초과하는 리소스를 사용하는 애플리케이션은 제한됩니다(더 이상 사용되지 않음). 애플리케이션 요구 사항을 충족하는 디스크 스트라이프에서 가상 머신 및 디스크 크기를 선택하면 상한 설정 제한이 발생하지 않습니다. 상한 설정을 해결하려면 캐싱을 사용하거나 더 적은 처리량이 필요하도록 애플리케이션을 조정합니다.

예를 들어, 12000IOPS 및 180MB/초를 필요로 하는 애플리케이션은 다음을 수행할 수 있습니다. 
- 최대 캐시되지 않은 디스크 처리량이 20,000IOPS 및 500MBps 인 [Standard_M32ms](../../../virtual-machines/m-series.md)를 사용합니다.
- 3개의 P30 디스크를 스트라이프하여 15000IOPS 및 600mb/s 처리량을 제공합니다.
- [Standard_M16ms](../../../virtual-machines/m-series.md) 가상 머신을 사용하고 호스트 캐싱을 사용하여 처리량을 소비하는 동안 로컬 캐시를 활용합니다. 

사용률이 높은 시간 동안 확장하도록 구성된 가상 머신은 최대 VM 크기를 지원할 수 있는 충분한 IOPS 및 처리량이 있는 저장소를 프로비전하고, 사용 대상으로 지정된 가장 작은 VM SKU로 지원되는 최대 개수 보다 작거나 같은 전체 디스크 수를 유지해야 합니다.

디스크 상한 설정 제한 사항에 대한 자세한 내용 및 상한 설정 사용을 방지하기 위해 캐싱을 사용하는 것에 대한 자세한 내용은 [디스크 IO 상한 설정](../../../virtual-machines/disks-performance.md)을 참조하세요.

> [!NOTE] 
> 일부 디스크 상한 설정은 사용자에게 만족할 만한 성능을 제공할 수 있습니다. 대규모 VM으로 크기를 조정하는 대신 워크로드를 조정하고 관리하여 비즈니스에 대한 비용 및 성능 관리를 분산합니다. 


## <a name="write-acceleration"></a>쓰기 가속화

쓰기 가속화는 [M 시리즈](../../../virtual-machines/m-series.md) VM(가상 머신)에서만 사용할 수 있는 디스크 기능입니다. 쓰기 가속화의 목적은 대용량 중요 업무용 OLTP 워크로드 또는 데이터 웨어하우스 환경으로 인해 한 자릿수 I/O 대기 시간이 필요할 때 Azure Premium Storage에 대한 쓰기의 I/O 대기 시간을 개선하는 것입니다. 

쓰기 가속화를 사용하여 로그 파일을 호스트하는 드라이브에 대한 쓰기 대기 시간을 향상시킵니다. SQL Server 데이터 파일에 쓰기 가속화를 사용하지 마세요. 

쓰기 가속화 디스크는 가상 머신과 동일한 IOPS 제한을 공유합니다. 연결된 디스크는 VM에 대한 쓰기 가속화 IOPS 제한을 초과할 수 없습니다.  

다음 표에서는 가상 머신당 지원되는 데이터 디스크 및 IOPS의 수를 간략하게 설명합니다. 

| VM SKU  | 쓰기 가속기 디스크 수  | VM당 쓰기 가속기 디스크 IOPS  |
|---|---|---|
| M416ms_v2, M416s_v2  | 16  | 20000  |
| M128ms, M128s  | 16  | 20000  |
| M208ms_v2, M208s_v2  | 8  | 10000  |
| M64ms, M64ls, M64s  |  8 | 10000 |
| M32ms, M32ls, M32ts, M32s  | 4  | 5,000  |
| M16ms, M16s  | 2 | 2500 |
| M8ms, M8s  | 1 | 1250 |

쓰기 가속화 사용에 대한 몇 가지 제한 사항이 있습니다. 자세히 알아보려면 [쓰기 가속기 사용 시 제한 사항](../../../virtual-machines/how-to-enable-write-accelerator.md#restrictions-when-using-write-accelerator)을 참조하세요.


### <a name="comparing-to-azure-ultra-disk"></a>Azure 울트라 디스크와 비교

쓰기 가속화와 Azure 울트라 디스크의 가장 큰 차이점은 쓰기 가속화는 M 시리즈에만 사용할 수 있는 가상 머신 기능이지만 Azure 울트라 디스크는 저장소 옵션인 것입니다. 쓰기 가속화는 가상 머신 크기에 따라 고유한 제한이 있는 쓰기에 최적화된 캐시입니다. Azure 울트라 디스크는 Azure Virtual Machines에 대한 짧은 대기 시간 디스크 저장소 옵션입니다. 

가능하면 트랜잭션 로그 디스크의 울트라 디스크를 통해 쓰기 가속화를 사용합니다. 쓰기 가속화를 지원하지 않지만 트랜잭션 로그에 짧은 대기 시간이 필요한 가상 머신의 경우, Azure 울트라 디스크를 사용합니다. 

## <a name="monitor-storage-performance"></a>스토리지 성능 모니터

스토리지 요구를 평가하고 스토리지 기능을 얼마나 잘 수행하는지 확인하려면 측정할 내용과 해당 지표의 의미를 이해해야 합니다. 

[IOPS(초당 입력/출력 수)](../../../virtual-machines/premium-storage-performance.md#iops)는 애플리케이션이 초당 스토리지로 만드는 요청 수입니다. 성능 모니터 카운터 `Disk Reads/sec` 및 `Disk Writes/sec`을 사용하여 IOPS를 측정합니다. [OLTP (온라인 트랜잭션 처리)](/azure/architecture/data-guide/relational-data/online-transaction-processing) 애플리케이션은 최적의 성능을 얻기 위해 높은 IOPS를 구동해야 합니다. 결제 처리 시스템, 온라인 쇼핑 및 소매 POS 시스템과 같은 애플리케이션은 모두 OLTP 애플리케이션의 예입니다.

[처리량](../../../virtual-machines/premium-storage-performance.md#throughput) 은 기본 스토리지로 전송되는 데이터의 볼륨이며 종종 초당 메가바이트 단위로 측정됩니다. 성능 모니터 카운터 `Disk Read Bytes/sec` 및 `Disk Write Bytes/sec`을 사용하여 처리량을 측정합니다. [데이터 웨어하우징](/azure/architecture/data-guide/relational-data/data-warehousing)은 IOPS를 통한 처리량을 최대화하는 데 최적화되었습니다. 분석, 보고, ETL 작업 흐름 및 기타 비즈니스 인텔리전스 대상에 대한 데이터 저장소와 같은 애플리케이션은 모두 데이터 웨어하우징 애플리케이션의 예입니다.

I/O 단위 크기는 IOPS 및 처리량 기능에 영향을 주므로 I/O 크기가 작으면 더 높은 IOPS를 생성하고 I/O 크기가 크면 더 높은 처리량을 생성합니다. SQL Server는 최적의 I/O 크기를 자동으로 선택합니다. 자세한 내용은 [애플리케이션의 IOPS, 처리량 및 대기 시간 최적화](../../../virtual-machines/premium-storage-performance.md#optimize-iops-throughput-and-latency-at-a-glance)를 참조하세요. 

가상 머신 및 디스크 수준에서 상한 설정을 검색하는 데 필요한 중요한 특정 Azure Monitor 메트릭과 AzureBlob 캐시의 사용량과 상태가 있습니다. 모니터링 솔루션 및 Azure Portal 대시보드에 추가할 주요 카운터를 식별하려면 [스토리지 사용률 메트릭](../../../virtual-machines/disks-metrics.md#storage-io-utilization-metrics)을 참조하세요. 

> [!NOTE]
> Azure Monitor는 현재 임시 드라이브 `(D:\)`에 대한 디스크 수준 메트릭을 제공하지 않습니다. 사용된 VM 캐시된 IOPS 비율 및 사용된 VM 캐시된 대역폭 백분율에는 모두 임시 드라이브 `(D:\)` 및 호스트 캐싱의 IOPS 및 처리량이 반영됩니다.


## <a name="next-steps"></a>다음 단계

성능 모범 사례에 대해 자세히 알아보려면 다음 시리즈의 다른 문서를 참조하세요.
- [빠른 검사 목록](performance-guidelines-best-practices-checklist.md)
- [VM 크기](performance-guidelines-best-practices-vm-size.md)
- [보안](security-considerations-best-practices.md)
- [HADR 설정](hadr-cluster-best-practices.md)
- [기준 수집](performance-guidelines-best-practices-collect-baseline.md)

보안 모범 사례는 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](security-considerations-best-practices.md)을 참조하세요.

TPC-E 및 TPC_C 벤치마크를 사용하여 Azure VM에서 SQL Server 성능을 자세히 테스트하려면 블로그 [OLTP 성능 최적화](https://techcommunity.microsoft.com/t5/sql-server/optimize-oltp-performance-with-sql-server-on-azure-vm/ba-p/916794)를 참조하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
