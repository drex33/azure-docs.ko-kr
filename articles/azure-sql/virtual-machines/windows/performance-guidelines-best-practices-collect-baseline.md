---
title: '기준 수집: 성능 모범 사례 및 지침'
description: Azure VM(Virtual Machine)에서 SQL Server의 성능을 최적화하기 위한 지침으로 성능 기준선을 수집하는 단계를 제공합니다.
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
ms.openlocfilehash: 7c223dfd6629f315d3d25b47ffe78f709aa4b864
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130165209"
---
# <a name="collect-baseline-performance-best-practices-for-sql-server-on-azure-vm"></a>기준 수집: Azure VM의 SQL Server에 대한 성능 모범 사례
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 Azure VMs(Virtual Machines)에서 SQL Server의 성능을 최적화하기 위한 일련의 모범 사례 및 지침으로 성능 기준선을 수집할 정보를 제공합니다.

일반적으로 비용에 대한 최적화와 성능에 대한 최적화 간의 절충이 있습니다. 이 성능 모범 사례 시리즈는 Azure Virtual Machines에서 SQL Server에 대한 *최상의* 성능을 얻는 데 중점을 두었습니다. 워크로드가 적은 경우 모든 권장 최적화 사항이 필요하지 않을 수 있습니다. 이러한 권장 사항을 평가할 때 성능 요구 사항, 비용 및 작업 패턴을 고려하세요.

## <a name="overview"></a>개요

규범적인 접근 방식의 경우 소스 환경의 일반적인 압력 및 잠재적 병목 상태를 더 잘 이해할 수 있도록 PerfMon/LogMan을 사용하여 성능 카운터를 수집하고 SQL Server 대기 통계를 캡처합니다. 

[애플리케이션 성능 검사 목록](../../../virtual-machines/premium-storage-performance.md#application-performance-requirements-checklist)에 따라 최대 사용 시간에 소스 워크로드의 CPU, 메모리, [IOPS](../../../virtual-machines/premium-storage-performance.md#iops), [처리량](../../../virtual-machines/premium-storage-performance.md#throughput), [대기 시간](../../../virtual-machines/premium-storage-performance.md#latency)을 수집하여 시작합니다. 

일반적인 업무 시간 중 워크로드와 같이 사용량이 많은 시간 중에 그리고 날짜별 마지막 처리 및 주말 ETL 워크로드와 같이 부하가 높은 다른 프로세스 중에 데이터를 수집합니다. 분기의 마지막 날짜에 처리와 같이 작업이 많은 워크로드에 대한 리소스를 스케일링 업한 다음, 워크로드가 완료되면 스케일링을 완료하는 것이 좋습니다. 

성능 분석을 사용하여 워크로드의 성능 요구 사항에 맞게 스케일링할 수 있는 [VM 크기](../../../virtual-machines/sizes-memory.md)를 선택합니다.


## <a name="storage"></a>스토리지

SQL Server 성능은 주로 I/O 하위 시스템에 따라 크게 좌우되며, 스토리지 성능은 IOPS 및 처리량으로 측정됩니다. 데이터베이스가 실제 메모리에 적합하지 않을 경우 SQL Server는 데이터베이스 페이지를 버퍼 풀 안팎으로 계속해서 가져옵니다. SQL Server에 대한 데이터 파일은 다르게 처리해야 합니다. tempdb를 비롯한 데이터 파일에 임의로 액세스하는 트랜잭션을 롤백해야 하는 경우를 제외하고 로그 파일에 대한 액세스는 순차적으로 실행됩니다. 저속 I/O 하위 시스템이 있는 경우 속도가 느린 응답 시간과 같은 성능 문제 및 시간 초과로 인해 완료되지 않는 작업이 발생할 수 있습니다. 

Azure Marketplace Virtual Machines는 기본적으로 데이터 파일과 별도의 실제 디스크에 로그 파일이 있습니다. tempdb 데이터 파일 수 및 크기는 모범 사례를 충족하며 임시 `D:\` 드라이브를 대상으로 합니다. 

다음 PerfMon 카운터는 SQL Server에 필요한 IO 처리량의 유효성을 검사하는 데 도움이 될 수 있습니다. 
* **\LogicalDisk\Disk Reads/Sec**(읽기 IOPS)
* **\LogicalDisk\Disk Writes/Sec**(쓰기 IOPS) 
* **\LogicalDisk\Disk Read Bytes/Sec**(데이터, 로그, tempdb 파일에 대한 읽기 처리량 요구 사항)
* **\LogicalDisk\Disk Write Bytes/Sec**(데이터, 로그, tempdb 파일에 대한 쓰기 처리량 요구 사항)

최고 수준에서 IOPS 및 처리량 요구 사항을 사용하여 측정한 용량과 일치하는 VM 크기를 평가합니다. 

20K 읽기 IOPS 및 10K 쓰기 IOPS가 워크로드에 필요한 경우 스토리지 공간을 사용하여 스트라이프된 2개의 P30 디스크와 함께 E16s_v3(최대 32K의 캐시된 IOPS 및 25600의 캐시되지 않은 IOPS) 또는 M16_s(최대 20K의 캐시된 IOPS 및 10K의 캐시되지 않은 IOPS)를 선택할 수 있습니다. 

VMs마다 IOPS 및 처리량에 대한 스케일링 제한이 다르므로 워크로드의 처리량 및 IOPS 요구 사항을 모두 이해해야 합니다.

## <a name="memory"></a>메모리

SQL Server에서 내부적으로 사용하는 메모리뿐만 아니라 OS에서 사용하는 외부 메모리를 모두 추적합니다. 두 구성 요소에 대한 압력을 식별하면 Virtual Machines의 크기를 조정하고 튜닝할 기회를 파악하는 데 도움이 됩니다. 

다음 PerfMon 카운터는 SQL Server 가상 머신의 메모리 상태에 대한 유효성을 검사하는 데 도움이 될 수 있습니다. 
* \Memory\사용 가능한 MB
* [\SQLServer:Memory Manager\Target Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Memory Manager\Total Server Memory (KB)](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Lazy writes/sec](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)
* [\SQLServer:Buffer Manager\Page life expectancy](/sql/relational-databases/performance-monitor/sql-server-buffer-manager-object)

## <a name="compute"></a>컴퓨팅

Azure에서 컴퓨팅은 온-프레미스와 다르게 관리됩니다. 온-프레미스 서버는 관리 오버 헤드 및 새 하드웨어 획득 비용으로 인해 업그레이드하지 않고 지난 몇 년간 빌드되었습니다. 가상화는 이와 같은 문제 중 일부를 완화하지만 애플리케이션은 기본 하드웨어의 장점을 최대한 활용하도록 최적화되어 있습니다. 즉, 리소스 사용량을 크게 변경하려면 전체 물리적 환경의 균형을 다시 맞춰야 합니다. 

이는 다른 하드웨어 시리즈의 새 가상 머신 및 다른 지역의 새 가상 머신에서 쉽게 달성할 수 있는 Azure의 과제가 아닙니다. 

Azure에서는 Virtual Machines 리소스를 최대한 활용하려고 하므로 워크로드에 영향을 주지 않고 평균 CPU를 최대한 높게 유지하도록 Azure Virtual Machines를 구성해야 합니다. 

다음 PerfMon 카운터는 SQL Server 가상 머신의 컴퓨팅 상태에 대한 유효성을 검사하는 데 도움이 될 수 있습니다.
* **\Processor Information(_Total)\% Processor Time**
* **\Process(sqlservr)\% Processor Time**

> [!NOTE] 
> 컴퓨팅의 80%를 사용하는 것이 가장 좋지만, 오랜 기간 동안 최대 90% 이상 사용하고 100%에 도달하지 않는 것이 좋습니다. 기본적으로 애플리케이션에 필요한 컴퓨팅을 프로비저닝하고 비즈니스 요구에 따라 스케일 업하거나 스케일 다운할 계획을 세워야 합니다. 


## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음 시리즈의 다른 문서를 참조하세요.
- [빠른 검사 목록](performance-guidelines-best-practices-checklist.md)
- [VM 크기](performance-guidelines-best-practices-vm-size.md)
- [스토리지](performance-guidelines-best-practices-storage.md)
- [보안](security-considerations-best-practices.md)
- [HADR 설정](hadr-cluster-best-practices.md)


보안 모범 사례는 [Azure Virtual Machines의 SQL Server에 대한 보안 고려 사항](security-considerations-best-practices.md)을 참조하세요.

[Azure Virtual Machines의 SQL Server 개요](sql-server-on-azure-vm-iaas-what-is-overview.md)에서 다른 SQL Server 가상 머신 문서를 검토하세요. SQL Server 가상 머신에 대한 질문이 있으면 [질문과 대답](frequently-asked-questions-faq.yml)을 참조하세요.
