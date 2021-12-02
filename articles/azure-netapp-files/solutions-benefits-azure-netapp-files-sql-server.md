---
title: SQL Server 배포에 Azure NetApp Files를 사용할 경우의 이점 | Microsoft Docs
description: SQL Server 배포에 Azure NetApp Files를 사용하는 것에 대한 자세한 비용 분석 성능 이점을 보여 줍니다.
services: azure-netapp-files
documentationcenter: ''
author: b-hchen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/19/2021
ms.author: b-hchen
ms.openlocfilehash: 5e298a2cd9241bcb784606611431e86e94410df4
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133484774"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>SQL Server 배포에 Azure NetApp Files를 사용할 경우의 이점

Azure NetApp Files는 블록 스토리지 솔루션에 비해 SQL Server TCO(총 소유 비용)를 줄여줍니다.  블록 스토리지를 사용하여 가상 머신은 디스크 작업을 위한 I/O 및 대역폭에 제한을 부과했습니다. 네트워크 대역폭 제한만 Azure NetApp Files에 적용되며 이 경우에만 송신에 적용됩니다.  즉, Azure NetApp Files에는 VM 수준 I/O 제한이 적용되지 않습니다. 해당 I/O 제한이 없으면 Azure NetApp Files에 연결된 더 작은 가상 머신에서 실행되는 SQL Server는 훨씬 더 큰 가상 머신에서 실행되는 SQL Server만큼 성능을 발휘할 수 있습니다. 이와 같이 인스턴스 크기를 하향 조정하면 컴퓨팅 비용이 이전 가격 태그의 25%로 줄어듭니다.  ‘Azure NetApp Files를 사용하면 컴퓨팅 비용을 줄일 수 있습니다.’  

그러나 컴퓨팅 비용은 SQL Server 라이선스 비용에 비해 적습니다.  Microsoft SQL Server [라이선스](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf)는 실제 코어 수에 연결됩니다. 따라서 인스턴스 크기를 줄이면 소프트웨어 라이선스 비용을 훨씬 더 많이 줄일 수 있습니다. ‘Azure NetApp Files를 사용하면 소프트웨어 라이선스 비용을 줄일 수 있습니다.’

이 문서에서는 SQL Server 배포에 Azure NetApp Files를 사용할 경우의 자세한 비용 분석과 성능상의 이점을 보여 줍니다. 더 작은 인스턴스는 더 큰 인스턴스의 블록으로만 데이터베이스 작업을 수행하기에 충분한 CPU가 있을 뿐만 아니라, ‘대부분의 경우 더 작은 인스턴스는 Azure NetApp Files로 인해 더 큰 디스크 기반 인스턴스보다 훨씬 더 성능이 뛰어납니다.’ 

## <a name="detailed-cost-analysis"></a>자세한 비용 분석 

이 섹션의 두 가지 그래픽 집합에는 TCO 예가 나와 있습니다.  최적의 가격-용량-성능을 달성하기 위해 관리 디스크의 수와 유형, Azure NetApp Files 서비스 수준 및 각 시나리오의 용량을 선택했습니다.  각 그래픽은 그룹화된 머신(관리 디스크를 사용하는 D64와 비교하여 Azure NetApp Files를 사용하는 D16)으로 구성되며 가격은 각 머신 유형에 따라 구분됩니다.  

첫 번째 그래픽 집합은 D16s_v4를 D64와 비교하고 D8을 D32와 비교하고 D4를 D16과 비교하여 1TiB 데이터베이스 크기를 사용하는 솔루션의 전체 비용을 보여 줍니다. 각 구성에 대한 예상 IOPS는 녹색 또는 노란색 줄로 표시되고 오른쪽 Y축에 해당합니다.

[![1TiB 데이터베이스 크기를 사용하는 솔루션의 전체 비용을 보여 주는 그래픽입니다.](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


두 번째 그래픽 집합은 50TiB 데이터베이스를 사용하는 전체 비용을 보여 줍니다. 달리 비교해도 동일합니다. 즉, D16을 Azure NetApp Files와 비교하는 것과 블록을 사용하는 D64와 비교하는 경우에 동일합니다. 

[ ![50TiB 데이터베이스 크기를 사용하는 전체 비용을 보여 주는 그래픽입니다.](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>성능  

상당한 비용 절감 효과가 있는 어설션을 제공하려면 많은 성능이 필요합니다. 예를 들어 일반적인 Azure 인벤토리에서 가장 큰 인스턴스는 80,000 디스크 IOPS를 지원합니다. 단일 Azure NetApp Files 볼륨은 80,000 데이터베이스 IOPS를 달성할 수 있으며 D16과 같은 인스턴스는 동일하게 사용할 수 있습니다. 일반적으로 25,600 디스크 IOPS를 지원하는 D16은 D64 크기의 25%입니다.  D64s_v4는 80,000 디스크 IOPS를 지원할 수 있으므로 뛰어난 상위 수준 비교 항목을 제공합니다.

D16s_v4는 Azure NetApp Files 볼륨을 80,000 데이터베이스 IOPS로 설정할 수 있습니다. SSB(SQL Storage Benchmark) 벤치마킹 도구를 통해 입증된 바와 같이 D16 인스턴스는 D64 인스턴스의 디스크에서 달성할 수 있는 것보다 125% 더 큰 워크로드를 달성했습니다.  도구에 대한 자세한 내용은 [SSB 테스트 도구](#ssb-testing-tool) 섹션을 참조하세요.

1TiB의 작업 집합 크기와 80% 읽기, 20% 업데이트 SQL Server 워크로드를 사용하여 D 인스턴스 클래스의 대부분의 인스턴스에 대한 성능 기능을 측정했습니다. 하지만 D2 및 D64 인스턴스가 테스트에서 제외되었습니다. 전자는 가속 네트워킹을 지원하지 않으므로 제외되었고 후자는 비교 항목이므로 제외되었습니다. D4s_v4, D8s_v4, D16s_v4 및 D32s_v4의 제한을 각각 이해하려면 다음 그래프를 참조하세요.  관리 디스크 스토리지 테스트는 그래프에 표시되지 않습니다. 비교 값은 D 클래스 인스턴스 유형에 대한 [Azure 가상 머신 제한 표](../virtual-machines/dv3-dsv3-series.md)에서 직접 가져옵니다.

Azure NetApp Files를 사용하면 D 클래스의 각 인스턴스가 두 배 더 큰 인스턴스의 디스크 성능 기능을 충족하거나 초과할 수 있습니다.  ‘Azure NetApp Files를 사용하면 소프트웨어 라이선스 비용을 대폭 줄일 수 있습니다.’  

* 75% CPU 사용률의 D4는 D16의 디스크 기능과 일치합니다.  
    * D16은 25,600 디스크 IOPS로 속도가 제한됩니다.  
* 75% CPU 사용률의 D8은 D32의 디스크 기능과 일치합니다.  
    * D32는 51,200 디스크 IOPS로 속도가 제한됩니다.  
* 55% CPU 사용률의 D16은 D64의 디스크 기능과 일치합니다.  
    * D64는 80,000 디스크 IOPS로 속도가 제한됩니다.  
* 15% CPU 사용률의 D32는 D64의 디스크 기능과 일치합니다.  
    * 위에 명시된 D64는 80,000 디스크 IOPS로 속도가 제한됩니다.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>S3B CPU 제한 테스트 - 성능 및 처리 능력

다음 다이어그램에는 S3B CPU 제한 테스트가 요약되어 있습니다.

![Azure NetApp Files에 대한 단일 인스턴스 SQL Server의 평균 CPU 비율을 보여 주는 다이어그램입니다.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

확장성은 일부일 뿐입니다. 다른 부분은 대기 시간입니다.  더 작은 가상 머신에 훨씬 더 높은 I/O 속도를 유도할 수 있는 기능이 있는 것과 아래에 표시된 것처럼 낮은 한 자릿수 지연 시간으로 수행하는 것은 다른 문제입니다.  

* D4는 2.3밀리초 대기 시간의 Azure NetApp Files에 대해 26,000 IOPS를 유지했습니다.  
* D8은 2.0밀리초 대기 시간의 Azure NetApp Files에 대해 51,000 IOPS를 유지했습니다.  
* D16은 2.8밀리초 대기 시간의 Azure NetApp Files에 대해 88,000 IOPS를 유지했습니다.
* D32는 2.4밀리초 대기 시간의 Azure NetApp Files에 대해 80,000 IOPS를 유지했습니다.  

### <a name="s3b-per-instance-type-latency-results"></a>인스턴스 유형별 S3B 대기 시간 결과

다음 다이어그램은 Azure NetApp Files에 대한 단일 인스턴스 SQL Server의 대기 시간을 보여 줍니다.

![Azure NetApp Files에 대한 단일 인스턴스 SQL Server의 대기 시간을 보여 주는 다이어그램입니다.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>SSB 테스트 도구 
 
[TPC-E](http://www.tpc.org/tpce/) 벤치마킹 도구는 설계상 ‘스토리지’ 대신 ‘컴퓨팅’을 강조합니다.  이 섹션에 표시된 테스트 결과는 SSB(SQL Storage Benchmark)라는 스트레스 테스트 도구를 기반으로 합니다.  SQL Server Storage Benchmark는 SQL Server 데이터베이스에 대해 대규모 SQL을 실행하여 [SLOB2 Oracle 벤치마킹 도구](https://kevinclosson.net/slob/)와 유사하게 OLTP 워크로드를 시뮬레이션할 수 있습니다. 

SSB 도구는 Azure 가상 머신 내에서 실행되는 SQL Server 데이터베이스에 직접 해당 문을 실행하는 SELECT 및 UPDATE 기반 워크로드를 생성합니다.  이 프로젝트의 경우 SSB 워크로드에서 SQL Server 사용자 수가 1에서 100으로 증가했으며 사용자 수당 15분에 10~12개의 중간 지점이 있습니다.  해당 실행의 모든 성능 메트릭은 SSB가 시나리오당 세 번 실행된 반복성에 대한 perfmon 관점에서 가져왔습니다. 

테스트 자체가 80% SELECT 및 20% UPDATE 문으로 구성되었으므로 90% 무작위 읽기가 가능합니다.  SSB가 생성된 데이터베이스 자체의 크기는 1,000GB입니다. 15개의 사용자 테이블과 사용자 테이블당 9,000,000행 및 행당 8,192바이트로 구성됩니다. 

SSB 벤치마크는 오픈 소스 도구입니다.  [SQL Storage Benchmark GitHub 페이지](https://github.com/NetApp/SQL_Storage_Benchmark.git)에서 무료로 사용할 수 있습니다.  


## <a name="in-summary"></a>요약  

Azure NetApp Files를 사용하면 총 소유 비용을 대폭 줄이는 동시에 SQL Server 성능을 향상할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md) 
* [Azure NetApp Files를 사용하는 솔루션 아키텍처 – SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

