---
title: HADR 구성 모범 사례
description: 지원되는 쿼럼 또는 연결 라우팅 옵션과 같이 Azure Virtual Machines에서 SQL Server에 대한 HADR(고가용성 및 재해 복구)을 구성할 때 지원되는 클러스터 구성에 대해 알아봅니다.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/01/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 40c68a77a3e432c5ff03da2a99e93255719e8898
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163419"
---
# <a name="hadr-configuration-best-practices-sql-server-on-azure-vms"></a>HADR 구성 모범 사례(Azure VM의 SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

[Windows Server 장애 조치(Failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)는 Azure VM(Virtual Machines)의 SQL Server를 사용한 HADR(고가용성 및 재해 복구)에 사용됩니다. 

이 문서에서는 Azure VM에서 SQL Server와 함께 사용할 때 [장애 조치(failover) 클러스터 인스턴스(FCI)](failover-cluster-instance-overview.md) 및 [가용성 그룹](availability-group-overview.md) 모두에 대한 클러스터 구성 모범 사례를 제공합니다. 

자세한 내용은 이 시리즈의 다른 문서([검사 목록](performance-guidelines-best-practices-checklist.md), [VM 크기](performance-guidelines-best-practices-vm-size.md), [스토리지](performance-guidelines-best-practices-storage.md), [보안](security-considerations-best-practices.md), [HADR 구성](hadr-cluster-best-practices.md), [기준선 수집](performance-guidelines-best-practices-collect-baseline.md))를 참조하세요. 

## <a name="checklist"></a>검사 목록

이 문서의 나머지 부분에서 자세히 설명하는 HADR 모범 사례에 대한 간략한 개요를 보려면 다음 검사 목록을 검토합니다. 

Windows 클러스터의 경우 모범 사례를 고려합니다. 

* 일시적인 네트워크 실패 또는 Azure 플랫폼 유지 관리로 인한 예기치 않은 중단을 방지하기 위해 덜 적극적인 매개 변수로 클러스터를 변경합니다. 자세히 알아보려면 [하트비트 및 임계값 설정](#heartbeat-and-threshold)을 참조하세요. Windows Server 2012 이상에서는 다음 권장 값을 사용합니다. 
   - **SameSubnetDelay**: 1초
   - **SameSubnetThreshold**: 하트비트 40개
   - **CrossSubnetDelay**: 1초
   - **CrossSubnetThreshold**: 하트비트 40개
* VM을 가용성 집합 또는 다른 가용성 영역에 배치합니다.  자세한 내용은 [VM 가용성 설정](#vm-availability-settings)을 참조하세요. 
* 클러스터 노드당 하나의 NIC 및 단일 서브넷을 사용합니다. 
* 클러스터 [쿼럼 투표](#quorum-voting)를 구성하여 3개 이상의 홀수 투표를 사용합니다. DR 지역에 투표를 할당하지 마세요. 
* 리소스 제약으로 인한 예기치 않은 재시작 또는 장애 조치(failover)를 방지하기 위해 [리소스 제한](#resource-limits)을 신중하게 모니터링합니다.
   - OS, 드라이버 및 SQL Server가 최신 빌드인지 확인합니다. 
   - Azure VM에서 SQL Server에 대한 성능을 최적화합니다. 자세히 알아보려면 이 문서의 다른 섹션을 검토합니다. 
   - 리소스 제한을 방지하기 위해 워크로드를 줄이거나 분산합니다. 
   - 제약을 방지하기 위해 제한이 더 높은 VM이나 디스크로 이동합니다. 

SQL Server 가용성 그룹 또는 장애 조치(failover) 클러스터 인스턴스의 경우 다음 모범 사례를 고려합니다. 

* 예기치 않은 오류가 자주 발생하는 경우 이 문서의 나머지 부분에서 설명하는 성능 모범 사례를 따릅니다. 
* SQL Server VM 성능 최적화로 예기치 않은 장애 조치(failover)가 해결되지 않는 경우 가용성 그룹 또는 장애 조치(failover) 클러스터 인스턴스에 대한 [모니터링 완화](#relaxed-monitoring)를 고려합니다. 하지만 문제의 근본 원인이 해결되지 않을 수 있고 오류 발생 가능성을 줄여 증상을 일시적으로 덮는 데 그칠 수 있습니다. 계속해서 근본 원인을 조사하고 해결해야 할 수 있습니다. Windows Server 2012 이상에서는 다음 권장 값을 사용합니다. 
   - **임대 시간 제한**: 이 등식을 사용하여 최대 임대 시간 제한 값을 계산합니다.   
   `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
   40초부터 시작합니다. 이전에 권장되었던 완화된 `SameSubnetThreshold` 및 `SameSubnetDelay` 값을 사용하는 경우 임대 시간 제한 값이 80초를 초과해서는 안 됩니다.   
   - **Max failures in a specified period(지정된 기간의 최대 실패 횟수)** : 이 값을 6으로 설정합니다. 
* VNN(가상 네트워크 이름)을 사용하여 HADR 솔루션에 연결하는 경우 클러스터의 범위가 하나의 서브넷에 불과하더라도 연결 문자열에서 `MultiSubnetFailover = true`를 지정합니다. 
   - 클라이언트에서 `MultiSubnetFailover = True`를 지원하지 않는 경우 `RegisterAllProvidersIP = 0` 및 `HostRecordTTL = 300`을 설정하여 더 짧은 기간 동안 클라이언트 자격 증명을 캐시해야 할 수 있습니다. 하지만 이렇게 하면 DNS 서버에 추가 쿼리가 발생할 수 있습니다. 
- DNN(분산형 네트워크 이름)을 사용하여 HADR 솔루션에 연결하려면 다음 사항을 고려합니다.
   - `MultiSubnetFailover = True`를 지원하는 클라이언트 드라이버를 사용해야 하고, 이 매개 변수가 연결 문자열에 있어야 합니다. 
   - 가용성 그룹의 DNN 수신기에 연결할 때 연결 문자열에서 고유한 DNN 포트를 사용합니다. 
- 기본 가용성 그룹에 대한 데이터베이스 미러링 연결 문자열을 사용하여 부하 분산 장치 또는 DNN에 대한 필요성을 무시합니다. 
- 불일치 I/O가 발생하지 않도록 고가용성 솔루션 배포 전에 VHD의 섹터 크기를 확인합니다. 자세히 알아보려면 [KB3009974](https://support.microsoft.com/topic/kb3009974-fix-slow-synchronization-when-disks-have-different-sector-sizes-for-primary-and-secondary-replica-log-files-in-sql-server-ag-and-logshipping-environments-ed181bf3-ce80-b6d0-f268-34135711043c)를 참조하세요. 


## <a name="vm-availability-settings"></a>VM 가용성 설정

가동 중지 시간의 영향을 줄이려면 다음 VM 최대 가용성 설정을 고려합니다. 

* 가장 짧은 대기 시간을 위해 근접 배치 그룹을 가속화된 네트워킹과 함께 사용합니다.
* 데이터 센터 수준 오류를 방지하기 위해 별도의 가용 영역에, 또는 동일한 데이터 센터 내에서 낮은 대기 시간 중복성을 위해 단일 가용성 집합에 가상 머신 클러스터 노드를 배치합니다.
* 가용성 집합의 VM에 대해 프리미엄 관리형 OS 및 데이터 디스크를 사용합니다.
* 각 애플리케이션 계층을 별도의 가용성 집합으로 구성합니다.

## <a name="quorum"></a>Quorum

2노드 클러스터는 [쿼럼 리소스](/windows-server/storage/storage-spaces/understand-quorum) 없이 작동하지 않지만 고객은 프로덕션 지원을 위해 쿼럼 리소스를 사용해야 합니다. 쿼럼 리소스가 없는 클러스터는 클러스터 유효성 검사를 통과할 수 없습니다. 

기술적으로 3노드 클러스터는 쿼럼 리소스가 없는 단일 노드 손실(2노드로 감소)에서 지속될 수 있습니다. 하지만 클러스터가 2개 노드로 감소된 이후 노드 손실이나 통신에 실패하는 경우 클러스터된 리소스가 스플릿 브레인 시나리오를 방지하기 위해 오프라인 상태로 전환되는 위험이 있습니다. 쿼럼 리소스를 구성하면 클러스터가 단 하나의 노드만 온라인 상태가 되어 온라인 상태를 지속할 수 있습니다.

디스크 감시는 가장 복원력이 높은 쿼럼 옵션이지만 Azure VM의 SQL Server에서 디스크 감시를 사용하려면 고가용성 솔루션에 몇 가지 제한 사항을 적용하는 Azure 공유 디스크를 사용해야 합니다. 따라서 Azure 공유 디스크를 사용하여 장애 조치(failover) 클러스터 인스턴스를 구성하는 경우 디스크 감시를 사용하고, 그 외에는 가능한 경우 클라우드 감시를 사용합니다. 

다음 표에서는 Azure VM에서 SQL Server에 사용할 수 있는 쿼럼 옵션을 보여줍니다. 

|  |[클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness) |[디스크 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[파일 공유 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**지원되는 OS**| Windows Server 2016+ |모두 | 모두|

- **클라우드 감시** 는 여러 사이트, 여러 영역 및 여러 지역의 배포에 이상적입니다. 공유 스토리지 클러스터 솔루션을 사용하지 않는 경우 가능한 항상 클라우드 감시를 사용하세요.
- **디스크 감시** 는 가장 탄력적으로 사용되는 쿼럼 옵션으로, Azure 공유 디스크(또는 공유 SCSI, iSCSI 또는 파이버 채널 SAN과 같은 공유 디스크 솔루션)를 사용하는 모든 클러스터에서 선호됩니다.  클러스터 공유 볼륨을 디스크 감시로 사용할 수 없습니다. 
- **파일 공유 감시** 는 디스크 감시 및 클라우드 감시를 사용할 수 없는 경우에 적합합니다. 

시작하려면 [클러스터 쿼럼 구성](hadr-cluster-quorum-configure-how-to.md)을 참조하세요. 

## <a name="quorum-voting"></a>쿼럼 투표

Windows Server 장애 조치(Failover) 클러스터에 참가하는 노드의 쿼럼 투표를 변경할 수 있습니다. 

노드 투표 설정을 수정하는 경우 다음 지침을 따르세요. 

| 쿼럼 투표 지침 |
|-|
| 기본적으로 투표가 없는 각 노드에서 시작합니다. 각 노드에는 명시적 타당성을 갖춘 투표만 있어야 합니다.|
| 가용성 그룹의 주 복제본 또는 장애 조치(failover) 클러스터 인스턴스의 기본 설정 소유자를 호스트하는 클러스터 노드에 대한 투표를 사용하도록 설정합니다. |
| 자동 장애 조치(failover) 소유자에 투표를 사용하도록 설정합니다. 자동 장애 조치(failover)로 인해 주 복제본 또는 FCI를 호스팅할 수 있는 각 노드에는 투표가 있어야 합니다. | 
| 가용성 그룹에 보조 복제본이 두 개 이상 있는 경우 자동 장애 조치(failover)가 있는 복제본에 대해서만 투표를 사용하도록 설정합니다. | 
| 보조 재해 복구 사이트에 있는 노드에 대한 투표를 사용하지 않도록 설정합니다. 기본 사이트에 문제가 없는 경우 보조 사이트의 노드가 클러스터를 오프라인으로 전환하는 의사 결정에 영향을 주지 않아야 합니다. | 
| 최소 3개의 쿼럼 투표를 포함하여 투표 수를 홀수로 합니다. 2노드 클러스터에서 필요한 경우 추가 투표를 위해 [쿼럼 감시](hadr-cluster-quorum-configure-how-to.md)를 추가합니다. | 
| 투표 할당 사후 장애 조치(failover)를 다시 평가합니다. 정상 상태의 쿼럼을 지원하지 않는 클러스터 구성에 대해서는 장애 조치(failover)를 수행할 필요가 없습니다. |


## <a name="connectivity"></a>연결


VNN(가상 네트워크 이름), 또는 SQL Server 2019부터 장애 조치(failover) 클러스터 인스턴스와 가용성 그룹 수신기 모두에 대해 DNN(분산 네트워크 이름)을 구성할 수 있습니다. 

분산 네트워크 이름은 사용 가능한 경우 권장되는 연결 옵션입니다. 
- 더 이상 부하 분산 장치 리소스를 유지 관리하지 않아도 되므로 엔드투엔드 솔루션이 더욱 강력해집니다. 
- 부하 분산 장치 프로브를 제거하면 장애 조치(failover) 기간이 최소화됩니다. 
- DNN은 Azure VM의 SQL Server를 사용하여 장애 조치(failover) 클러스터 인스턴스 또는 가용성 그룹 수신기의 프로비저닝 및 관리를 간소화합니다. 

DNN을 사용하거나 여러 서브넷에 걸쳐 있는 AG 또는 FCI를 사용하는 경우 MultiSubnetFailover 매개 변수를 지원하는 클라이언트 드라이버를 사용하고, 연결 문자열에 MultiSubnetFailover=True를 지정해야 합니다. 가용성 그룹의 경우 연결 문자열에는 DNN 포트 번호가 포함 되어야 합니다(FCI에는 필요하지 않음). 

자세히 알아보려면 [Windows Server 장애 조치(Failover) 클러스터 개요](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn)를 참조하세요. 

연결을 구성하려면 다음 문서를 참조하세요.
- 가용성 그룹:  [DNN 구성](availability-group-distributed-network-name-dnn-listener-configure.md), [VNN 구성](availability-group-vnn-azure-load-balancer-configure.md)
- 장애 조치(failover) 클러스터 인스턴스: [DNN 구성](failover-cluster-instance-distributed-network-name-dnn-configure.md), [VNN 구성](failover-cluster-instance-vnn-azure-load-balancer-configure.md). 

DNN을 사용하는 경우 대부분의 SQL Server 기능이 FCI 및 가용성 그룹에서 투명하게 작동하지만 특별 고려 사항이 필요할 수 있는 특정 기능이 있습니다. 자세한 내용은 [FCI 및 DNN 상호 운용성](failover-cluster-instance-dnn-interoperability.md)과 [AG 및 DNN 상호 운용성](availability-group-dnn-interoperability.md)을 참조하세요. 

>[!TIP]
> 단일 서브넷에 걸친 HADR 솔루션에 대해서도 연결 문자열에서 MultiSubnetFailover parameter = true를 설정하여 연결 문자열 업데이트에 대한 필요성 없이 서브넷의 향후 확장을 지원합니다.  

## <a name="heartbeat-and-threshold"></a>하트비트와 임계값 

클러스터 하트비트와 임계값 설정을 완화합니다. 기본 하트비트 및 임계값 클러스터 설정은 고도로 조정된 온-프레미스 네트워크에 맞춰 설계되며 클라우드 환경의 대기 시간 증가 가능성을 고려하지 않습니다. 하트비트 네트워크는 UDP 3343으로 유지 관리되며, 이는 TCP보다 안정성이 떨어지고 불완전한 대화가 발생하기 쉽습니다.
 
따라서 Azure VM 고가용성 솔루션에서 SQL Server에 대한 클러스터 노드를 실행할 때 클러스터 설정을 더 완화된 모니터링 상태로 변경하여 네트워크 대기 또는 장애, Azure 유지 관리 또는 리소스 병목 현상 발생으로 인한 일시적 장애를 방지합니다. 

지연 및 임계값 설정은 총 상태 검색에 누적된 영향을 미칩니다. 예를 들어 2초마다 하트비트를 전송하도록 *CrossSubnetDelay* 를 설정하고 *CrossSubnetThreshold* 에서 복구 전 누락된 하트비트를 10개로 설정하면 복구 작업을 수행하기 전에 클러스터의 총 네트워크 허용 오차는 20초가 될 수 있습니다. 일반적으로 자주 하트비트를 계속해서 전송하되 임계값을 더 크게 하는 것이 좋습니다. 

일시적인 문제에 대해 더 큰 허용 오차를 제공하면서 합당한 중단 도중 복구를 보장하려면 지연 및 임계값 설정을 아래 표에 자세히 설명된 권장값으로 완화합니다. 

| 설정 | Windows Server 2012 이상 | Windows Server 2008R2 |
|:---------------------|:----------------------------|:-----------------------|
| SameSubnetDelay      | 1초                    | 2초               |
| SameSubnetThreshold  | 하트비트 40개               | 하트비트 10개(최대)         |
| CrossSubnetDelay     | 1초                    | 2초               |  
| CrossSubnetThreshold | 하트비트 40개               | 하트비트 20개(최대)         |


PowerShell을 사용하여 클러스터 매개 변수 변경: 

# <a name="windows-server-2012-2019"></a>[Windows Server 2012-2019](#tab/windows2012)


```powershell
(get-cluster).SameSubnetThreshold = 40
(get-cluster).CrossSubnetThreshold = 40
```

# <a name="windows-server-2008r2"></a>[Windows Server 2008/R2](#tab/windows2008)


```powershell
(get-cluster).SameSubnetThreshold = 10
(get-cluster).CrossSubnetThreshold = 20 
(get-cluster).SameSubnetDelay = 2000
(get-cluster).CrossSubnetDelay = 2000
```

---

PowerShell을 사용하여 변경 확인: 

```powershell
get-cluster | fl *subnet*
```

다음을 살펴보세요. 

* 이 변경은 즉시 적용되며 클러스터를 다시 시작하거나 리소스가 필요하지 않습니다. 
* 동일한 서브넷 값은 서브넷 간 값보다 크지 않아야 합니다. 
* SameSubnetThreshold <= CrossSubnetThreshold
* SameSubnetDelay <= CrossSubnetDelay

허용 가능한 가동 중지 시간과 애플리케이션, 비즈니스 요구 사항 및 환경에 따라 정정 작업이 발생할 때까지의 시간을 기준으로 완화된 값을 선택합니다. 기본 Windows Server 2019 값을 초과할 수 없는 경우 가능하면 최소한 이러한 값을 일치시켜야 합니다. 

참조를 위해 다음 표에서는 기본값을 자세히 설명합니다. 


| 설정 | Windows Server 2019 |  Windows Server 2016 |    Windows Server 2008 - 2012 R2 |
|:---------------------|:----------------|   ------------|:----------------------------|
| SameSubnetDelay      | 1초        | 1초       | 1초                    |
| SameSubnetThreshold  | 하트비트 20개   | 하트비트 10개  | 하트비트 5개               |
| CrossSubnetDelay     | 1초        | 1초     | 1초                    |
| CrossSubnetThreshold | 하트비트 20개   | 하트비트 10개   | 하트비트 5개               |


자세한 내용은 [장애 조치(failover) 클러스터 네트워크 임계값 튜닝](/windows-server/troubleshoot/iaas-sql-failover-cluster)을 참조하세요.

## <a name="relaxed-monitoring"></a>완화된 모니터링

클러스터 하트비트 및 임계값 설정을 권장 수준으로 튜닝하는 것이 허용 오차로는 부족하고 실제 중단이 아닌 일시적인 문제로 인해 오류가 계속 표시되는 경우 AG 또는 FCI 모니터링을 더욱 완화적으로 구성할 수 있습니다. 일부 시나리오에서는 작업 수준이 지정된 경우 일정 기간 동안 모니터링을 일시적으로 완화하는 것이 유용할 수 있습니다. 예를 들어 데이터베이스 백업, 인덱스 유지 관리, DBCC CHECKDB 등과 같은 IO 집약적 워크로드를 수행하는 경우 모니터링을 완화할 수 있습니다. 작업이 완료되면 모니터링을 덜 완화된 값으로 설정합니다. 

> [!WARNING]
> 이러한 설정을 변경하면 기본 문제를 덮을 수 있지만 실패 가능성을 제거하는 대신 임시 솔루션으로 사용해야 합니다. 계속해서 기본 문제를 조사 및 해결해야 합니다. 

완화된 모니터링을 위해 기본값에서 다음 매개 변수를 늘리고 필요에 따라 조정합니다. 


|매개 변수 |기본값  |완화된 값  |Description  |
|---------|---------|---------|---------|
|**Healthcheck 제한 시간**|30000 |60000 |주 복제본 또는 노드의 상태를 확인합니다. 클러스터 리소스 DLL sp_server_diagnostics는 상태 확인 제한 시간 임계값의 1/3에 해당하는 간격으로 결과를 반환합니다. sp_server_diagnostics가 느리거나 정보를 반환하지 않는 경우 리소스 DLL은 리소스가 응답하지 않고 자동 장애 조치(failover)를 시작하기로 결정하기 전에 상태 확인 제한 시간 임계값의 전체 간격 동안 대기합니다. |
|**오류 상태 수준** |  3  |   2  |자동 장애 조치(failover)를 트리거하는 조건입니다. 가장 낮은 제한 수준 1에서 가장 높은 제한 수준 5까지의 다섯 가지 오류 상태 수준이 있습니다.  |

Transact-SQL(T-SQL)을 사용하여 AG 및 FCI 모두에 대한 상태 검사 및 오류 조건을 수정합니다. 

가용성 그룹: 

```sql
ALTER AVAILABILITY GROUP AG1 SET (HEALTH_CHECK_TIMEOUT =60000);
ALTER AVAILABILITY GROUP AG1 SET (FAILURE_CONDITION_LEVEL = 2);
```

장애 조치(Failover) 클러스터 인스턴스: 

```sql
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY HealthCheckTimeout = 60000;
ALTER SERVER CONFIGURATION SET FAILOVER CLUSTER PROPERTY FailureConditionLevel = 2; 
```

**가용성 그룹** 에 따라 다음 권장 매개 변수로 시작하고 필요에 따라 조정합니다. 

|매개 변수 |기본값  |완화된 값  |Description  |
|---------|---------|---------|---------|
|**임대 시간 제한**|20000|40,000|스플릿 브레인을 방지합니다. |
|**세션 제한 시간**|10000 |20000|복제본 간의 통신 문제를 검사합니다. 세션 제한 시간은 가용성 복제본이 연결이 실패한 것으로 간주되기 전에 연결된 복제본에서 ping 응답을 받기 위해 기다리는 최대 시간(초)을 제어하는 복제본 속성입니다. 기본적으로 복제본은 ping 응답을 받기 위해 10초 동안 기다립니다. 이 복제본 속성은 지정된 보조 복제본과 가용성 그룹의 주 복제본 사이의 연결에만 적용됩니다. |
| **Max failures in specified period** | 2 | 6 |여러 노드 오류 내에서 클러스터된 리소스의 무한한 이동을 방지하는 데 사용됩니다. 값이 너무 낮으면 가용성 그룹이 오류 상태가 될 수 있습니다. 값이 너무 낮으면 AG가 오류 상태가 될 수 있으므로 값을 높여 성능 문제로 인한 짧은 중단을 방지해야 합니다. | 

변경 전에 다음 사항을 고려합니다. 
- 해당 기본값 아래로 제한 시간 값을 줄이지 않습니다. 
- 이 등식을 사용하여 최대 임대 시간 초과값을 계산합니다.   
 `Lease timeout < (2 * SameSubnetThreshold * SameSubnetDelay)`.   
  40초부터 시작합니다. 이전에 권장되었던 완화된 `SameSubnetThreshold` 및 `SameSubnetDelay` 값을 사용하는 경우 임대 시간 제한 값이 80초를 초과해서는 안 됩니다.    
- 동기-커밋 복제본의 경우 session-timeout을 높은 값으로 변경하면 HADR_sync_commit 대기가 증가할 수 있습니다.

**임대 시간 제한** 

**장애 조치(Failover) 클러스터 관리자** 를 사용하여 가용성 그룹에 대한 **임대 시간 제한** 설정을 수정합니다. 자세한 단계는 SQL Server [가용성 그룹 임대 상태 검사](/sql/database-engine/availability-groups/windows/availability-group-lease-healthcheck-timeout#lease-timeout) 설명서를 참조하세요.

**세션 제한 시간** 

Transact-SQL(T-SQL)을 사용하여 가용성 그룹에 대한 **세션 시간 초과** 를 수정합니다. 

```sql
ALTER AVAILABILITY GROUP AG1
MODIFY REPLICA ON 'INSTANCE01' WITH (SESSION_TIMEOUT = 15);
```

**Max failures in specified period**

장애 조치(failover) 클러스터 관리자를 사용하여 **Max failures in specified period** 값을 수정합니다. 
1. 탐색 창에서 **역할** 을 선택합니다.
1. **역할** 에서 클러스터된 리소스를 마우스 오른쪽 단추로 클릭하고 **속성** 을 선택합니다. 
1. **장애 조치(Failover)** 탭을 선택하고 **Max failures in specified period** 값을 원하는 대로 늘립니다. 

## <a name="resource-limits"></a>리소스 한계

VM 또는 디스크 한계로 인해 리소스 병목 현상이 발생하고 클러스터의 상태에 영향을 미치며 상태 검사를 방해할 수 있습니다. 리소스 한계 문제가 발생하는 경우 다음을 고려합니다. 

* OS, 드라이버 및 SQL Server가 최신 빌드인지 확인합니다.
* Azure Virtual Machines의 SQL Server [성능 가이드라인](performance-guidelines-best-practices-checklist.md)에서 설명하는 것과 같이 Azure VM 환경에서 SQL Server 최적화
* 리소스 한계를 초과하지 않고 사용률을 줄이기 위해 워크로드를 줄이거나 분산
* 다음과 같은 경우 SQL Server 워크로드 조정
    * 인덱스 추가/최적화
    * 필요한 경우 및 가능한 경우 전체 검사를 통해 통계 업데이트  
    * 리소스 관리자(SQL Server 2014부터, 엔터프라이즈 전용)와 같은 기능을 사용하여 백업 또는 인덱스 유지 관리와 같은 특정 워크로드 동안 리소스 사용률을 제한합니다. 
* 워크로드의 수요를 충족하거나 초과하도록 제한이 더 높은 VM 또는 디스크로 이동합니다. 

## <a name="networking"></a>네트워킹

서버당 단일 NIC (클러스터 노드)를 사용 합니다. Azure 네트워킹에는 Azure 가상 컴퓨터 게스트 클러스터에서 추가 Nic를 불필요 하 게 만드는 물리적 중복성이 있습니다. 클러스터 유효성 검사 보고서는 노드가 단일 네트워크에서만 연결할 수 있다는 경고를 표시합니다. Azure 가상 머신 게스트 장애 조치(failover) 클러스터에서는 이 경고를 무시할 수 있습니다. 

특정 VM에 대 한 대역폭 제한은 nic에서 공유 되 고 추가 nic를 추가 하는 것은 Azure vm의 SQL Server에 대 한 가용성 그룹 성능을 향상 시 키 지 않습니다. 따라서 두 번째 NIC를 추가할 필요가 없습니다. 

Azure에서 RFC를 준수하지 않는 DHCP 서비스를 사용할 경우 특정 장애 조치 클러스터 구성을 만들 수 없게 됩니다. 이 오류는 클러스터 네트워크 이름에 중복 IP 주소(예: 클러스터 노드 중 하나와 IP 주소가 같음)가 할당되기 때문에 발생합니다. Windows 장애 조치 클러스터 기능에 따라 달라지는 가용성 그룹을 사용하는 경우 해당 문제가 발생합니다.

노드가 2개인 클러스터를 만들고 온라인 상태로 만드는 상황을 고려해 보십시오.

1. 클러스터가 온라인 상태가 되면 다음 NODE1은 클러스터 네트워크 이름으로 동적 할당된 IP 주소를 요청합니다.
2. DHCP 서비스는 요청이 NODE1 자체에서 나온다는 것을 인식하기 때문에 DHCP 서비스는 NODE1의 자체 IP 주소 이외에는 IP 주소를 제공하지 않습니다.
3. Windows는 NODE1과 장애 조치 클러스터 네트워크 이름에 중복된 주소가 할당된 것으로 감지하므로, 기본 클러스터 그룹이 온라인 상태가 될 수 없습니다.
4. 기본 클러스터 그룹은 NODE2로 이동합니다. NODE2는 NODE1의 IP 주소를 클러스터 IP 주소로 취급하여 기본 클러스터 그룹이 온라인 상태가 됩니다.
5. NODE2가 NODE1에 연결을 시도하면 NODE1로 보내지는 패킷이 NODE1의 IP 주소 자체로 향하게 되므로 NODE2에서 출발 자체를 할 수 없습니다. 따라서 NODE2는 NODE1에 연결할 수 없고 쿼럼을 잃고 클러스터를 닫게 됩니다.
6. NODE1은 NODE2로 패킷을 보낼 수 있지만, NODE2는 응답할 수 없습니다. NODE1도 쿼럼을 잃고 클러스터를 닫습니다.

사용되지 않는 고정 IP 주소를 클러스터 네트워크 이름으로 할당하여 클러스터 네트워크 이름을 온라인 상태로 만들면 이런 상황을 방지할 수 있습니다. 예를 들어 169.254.1.1과 같은 링크-로컬 IP 주소를 사용할 수 있습니다. 이 절차를 간단히 수행하려면 [Azure에서 가용성 그룹에 Windows 장애 조치 클러스터 구성](https://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx)을 참조하세요.

자세한 내용은 [Azure에서 가용성 그룹 구성(GUI)](./availability-group-quickstart-template-configure.md)을 참조하세요.


## <a name="known-issues"></a>알려진 문제

일반적으로 알려진 문제와 오류에 대한 해결 방법을 검토합니다. 

**멤버 자격에서 제거된 클러스터 노드**


[Windows 클러스터 하트비트 및 임계값 설정](#heartbeat-and-threshold)이 환경에 비해 너무 적극적인 경우 시스템 이벤트 로그에 다음 메시지가 자주 표시될 수 있습니다. 

```
Error 1135
Cluster node 'Node1' was removed from the active failover cluster membership. 
The Cluster service on this node may have stopped. This could also be due to the node having 
lost communication with other active nodes in the failover cluster. Run the Validate a 
Configuration Wizard to check your network configuration. If the condition persists, check 
for hardware or software errors related to the network adapters on this node. Also check for 
failures in any other network components to which the node is connected such as hubs, switches, or bridges.
```


자세한 내용은 [이벤트 ID 1135 클러스터 문제 해결](/windows-server/troubleshoot/troubleshooting-cluster-event-id-1135)을 참조하세요.


**임대 만료** / **임대가 더 이상 유효하지 않음**


[모니터링](#relaxed-monitoring)이 환경에 비해 너무 적극적인 경우 AG 또는 FCI 재시작, 오류 또는 장애 조치(failover)가 자주 발생할 수 있습니다. 추가로 가용성 그룹의 경우 SQL Server 오류 로그에 다음 메시지가 표시될 수 있습니다. 

```
Error 19407: The lease between availability group 'PRODAG' and the Windows Server Failover Cluster has expired. 
A connectivity issue occurred between the instance of SQL Server and the Windows Server Failover Cluster. 
To determine whether the availability group is failing over correctly, check the corresponding availability group 
resource in the Windows Server Failover Cluster
```

```
Error 19419: The renewal of the lease between availability group '%.*ls' and the Windows Server Failover Cluster 
failed because the existing lease is no longer valid. 
``` 

**연결 제한 시간**

가용성 그룹 환경에 **세션 시간 초과** 가 너무 적극적인 경우 다음과 같은 메시지가 자주 표시될 수 있습니다.

```
Error 35201: A connection timeout has occurred while attempting to establish a connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or firewall issue exists, 
or the endpoint address provided for the replica is not the database mirroring endpoint of the host server instance.
```

```
Error 35206
A connection timeout has occurred on a previously established connection to availability 
replica 'replicaname' with ID [availability_group_id]. Either a networking or a firewall issue 
exists, or the availability replica has transitioned to the resolving role. 
```

**그룹을 장애 조치(failover)하지 않음**



**Maximum Failures in the Specified Period** 값이 너무 낮고 일시적인 문제로 인해 간헐적으로 오류가 발생하는 경우 가용성 그룹이 오류 상태가 될 수 있습니다. 더 많은 일시적 오류를 허용하려면 이 값을 늘려야 합니다. 

```
Not failing over group <Resource name>, failoverCount 3, failoverThresholdSetting <Number>, computedFailoverThreshold 2. 
```


## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure VM의 SQL Server에 대한 HADR 설정](hadr-cluster-best-practices.md)
- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Always On 가용성 그룹](availability-group-overview.md)
- [Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터](hadr-windows-server-failover-cluster-overview.md)
- [Azure VM에서 SQL Server를 사용하는 장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)
- [장애 조치(failover) 클러스터 인스턴스 개요](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
