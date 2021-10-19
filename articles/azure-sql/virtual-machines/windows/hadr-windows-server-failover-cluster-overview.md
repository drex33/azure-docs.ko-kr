---
title: Windows Server 장애 조치(failover) 클러스터 서비스
description: '가용성 그룹 및 장애 조치(failover) 클러스터 인스턴스와 같은 Azure VM에서 SQL Server와 함께 사용하는 경우 Windows Server 장애 조치(failover) 클러스터 기술과의 차이점을 알아봅니다. '
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
ms.openlocfilehash: dc007e4aeb68d3cecd156a650bd3c04de1fd26e0
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130162201"
---
# <a name="windows-server-failover-cluster-with-sql-server-on-azure-vms"></a>Azure VM에서 SQL Server를 사용하는 Windows Server 장애 조치(failover) 클러스터
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

이 문서에서는 AG(Always On 가용성 그룹) 또는 FCI(장애 조치(failover) 클러스터 인스턴스)와 같은 HADR(고가용성 및 재해 복구)을 위해 Azure VM에서 SQL Server와 함께 Windows Server 장애 조치(failover) 클러스터 기능을 사용하는 경우의 차이점을 설명합니다. 

Windows 기능에 대한 자세한 내용은 [Windows Server 장애 조치(Failover) 클러스터 문서](/windows-server/failover-clustering/failover-clustering-overview)를 참조하세요.

## <a name="overview"></a>개요

AG(Always On 가용성 그룹) 또는 FCI(장애 조치(failover) 클러스터 인스턴스)와 같은 Windows의 SQL Server 고가용성 솔루션은 기본 WSFC(Windows Server 장애 조치(failover) 클러스터링) 서비스를 사용합니다.

클러스터 서비스는 네트워크 연결과 클러스터의 노드 상태를 모니터링합니다. 이 모니터링은 SQL Server에서 가용성 그룹이나 장애 조치(failover) 클러스터 인스턴스 기능의 일부로 수행하는 상태 검사에 추가됩니다. 클러스터 서비스가 노드에 연결할 수 없거나 클러스터의 AG 또는 FCI 역할이 비정상인 경우 클러스터 서비스는 클러스터의 동일하거나 다른 노드에서 애플리케이션과 서비스를 복구하여 온라인 상태로 가져오도록 적절한 복구 작업을 시작합니다.

## <a name="cluster-health-monitoring"></a>클러스터 상태 모니터링

고가용성을 제공하기 위해 클러스터는 클러스터형 솔루션을 구성하는 다양한 구성 요소의 상태를 확인해야 합니다. 클러스터 서비스는 실패를 검색하고 응답하기 위해 여러 시스템과 네트워크 매개 변수에 따라 클러스터 상태를 모니터링합니다. 

실패에 신속하게 응답 및 거짓 실패 방지 간의 균형을 맞추기 위해 실패를 선언하는 임계값을 설정하는 것이 중요합니다.

모니터링 전략에는 두 가지가 있습니다.

| 모니터링  | Description |
|-|-|
| Aggressive | 하드 실패의 신속한 실패 감지와 복구를 제공하므로 높은 수준의 가용성을 제공합니다. 클러스터 서비스와 SQL Server는 일시적 실패를 제공하는 것이 아니라 일시적인 중단이 발생한 경우 일부 상황에서 리소스를 조기에 장애 조치(failover)할 수 있습니다. 실패가 감지되면 후속 정정 작업에 시간이 더 걸릴 수 있습니다. |
| 완화됨 | 짧은 일시적인 네트워크 이슈에 더 높은 허용 오차를 사용하여 다양한 실패 감지 기능을 제공합니다. 일시적 실패를 방지하지만 진짜 실패 감지가 지연될 위험이 있습니다. |

클라우드의 클러스터 환경에서 적극적으로 설정하면 이로 인해 조기에 실패하고 중단 시간이 길어질 수 있으므로 Azure VM의 장애 조치(failover) 클러스터에 완화된 모니터링 전략을 사용하는 것이 좋습니다. 임계값 설정을 조정하려면 [클러스터 모범 사례](hadr-cluster-best-practices.md#relaxed-monitoring)를 참조하세요. 

## <a name="cluster-heartbeat"></a>클러스터 하트비트

클러스터 하트비트와 노드 간의 상태 검색에 영향을 미치는 기본 설정은 다음과 같습니다.

| 설정 | Description |
|-|-|
| Delay | 클러스터 하트비트가 노드 간에 전송되는 빈도를 정의합니다. 지연은 다음 하트비트가 전송되기 전의 시간(초)입니다. 동일한 클러스터 내에서 동일한 서브넷의 노드 간에 그리고 다른 서브넷에 있는 노드 간에 서로 다른 지연 설정이 구성될 수 있습니다. |
| 임계값 | 임계값은 클러스터에서 복구 작업을 수행하기 전에 누락될 수 있는 하트비트 수입니다. 동일한 클러스터 내에서 동일한 서브넷의 노드 간에 그리고 다른 서브넷에 있는 노드 간에 서로 다른 임계값 설정이 구성될 수 있습니다. |

이러한 설정의 기본값은 클라우드 환경에 너무 낮을 수 있으며 일시적인 네트워크 이슈로 인해 불필요한 실패가 발생할 수 있습니다. 내결함성을 강화하도록 Azure VM에서 장애 조치(failover) 클러스터에 완화된 임계값 설정을 사용합니다. 자세한 내용은 [클러스터 모범 사례](hadr-cluster-best-practices.md#heartbeat-and-threshold)를 참조하세요. 

## <a name="quorum"></a>Quorum

2노드 클러스터는 [쿼럼 리소스](/windows-server/storage/storage-spaces/understand-quorum) 없이 작동하지 않지만 고객은 프로덕션 지원을 위해 쿼럼 리소스를 사용해야 합니다. 쿼럼 리소스가 없는 클러스터는 클러스터 유효성 검사를 통과할 수 없습니다. 

기술적으로 3노드 클러스터는 쿼럼 리소스가 없는 단일 노드 손실(2노드로 감소)에서 지속될 수 있습니다. 하지만 클러스터가 노드 2개로 감소된 이후 노드 손실이나 노드 간에 통신이 실패하는 경우 클러스터형 리소스가 스플릿 브레인 시나리오를 방지하기 위해 오프라인 상태로 전환되는 위험이 있습니다. 쿼럼 리소스를 구성하면 클러스터 리소스는 노드 하나만 온라인 상태가 되어 온라인 상태를 유지할 수 있습니다.

디스크 감시는 가장 복원력이 높은 쿼럼 옵션이지만 Azure VM의 SQL Server에서 디스크 감시를 사용하려면 고가용성 솔루션에 몇 가지 제한 사항을 적용하는 Azure 공유 디스크를 사용해야 합니다. 따라서 Azure 공유 디스크를 사용하여 장애 조치(failover) 클러스터 인스턴스를 구성하는 경우 디스크 감시를 사용하고, 그 외에는 가능한 경우 클라우드 감시를 사용합니다. 

다음 표에서는 Azure VM에서 SQL Server에 사용할 수 있는 쿼럼 옵션을 보여줍니다. 

|  |[클라우드 감시](/windows-server/failover-clustering/deploy-cloud-witness) |[디스크 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum) |[파일 공유 감시](/windows-server/failover-clustering/manage-cluster-quorum#configure-the-cluster-quorum)  |
|---------|---------|---------|---------|
|**지원되는 OS**| Windows Server 2016+ |모두 | 모두|
| **설명** | 클라우드 감시는 Microsoft Azure를 사용하여 클러스터 쿼럼에 대한 투표를 제공하는 장애 조치(failover) 클러스터 쿼럼 감시의 한 유형입니다. 기본 크기는 약 1MB이며 타임 스탬프도 포함합니다. 클라우드 감시는 여러 사이트, 여러 영역 및 여러 지역의 배포에 적합합니다. 공유 스토리지를 사용하는 장애 조치(failover) 클러스터 솔루션이 없는 경우 가능하면 클라우드 감시를 사용합니다. | 디스크 감시는 클러스터에서 사용 가능한 스토리지 그룹의 작은 클러스터형 디스크입니다. 이 디스크는 항상 사용 가능하며 노드 간에 장애 조치(failover)가 가능합니다. 여기에는 클러스터 데이터베이스 복사본이 포함되며 기본 크기는 1GB 미만입니다. 디스크 감시는 Azure 공유 디스크(또는 공유 SCSI, iSCSI 또는 파이버 채널 SAN과 같은 공유 디스크 솔루션)를 사용하는 모든 클러스터에 선호되는 쿼럼 옵션입니다.  클러스터 공유 볼륨을 디스크 감시로 사용할 수 없습니다. Azure 공유 디스크를 디스크 감시로 구성합니다.  | 파일 공유 감시는 일반적으로 Windows Server를 실행하는 파일 서버에 구성되는 SMB 파일 공유입니다. 이는 witness.log 파일에 클러스터링 정보를 유지하지만 클러스터 데이터베이스의 복사본은 저장하지 않습니다. Azure에서 동일한 가상 네트워크 내의 별도 가상 머신에서 파일 공유를 구성할 수 있습니다. 사용자 환경에서 디스크 감시나 클라우드 감시를 사용할 수 없는 경우 파일 공유 감시를 사용합니다. | 

시작하려면 [클러스터 쿼럼 구성](hadr-cluster-quorum-configure-how-to.md)을 참조하세요. 


## <a name="virtual-network-name-vnn"></a>VNN(가상 네트워크 이름)

기존 온-프레미스 환경에서 장애 조치(failover) 클러스터 인스턴스 또는 Always On 가용성 그룹과 같은 클러스터형 리소스는 Virtual Network 이름을 사용하여 트래픽을 적절한 대상인 장애 조치(failover) 클러스터 인스턴스 또는 Always On 가용성 그룹의 수신기 중 하나로 라우팅합니다. 가상 이름은 DNS의 IP 주소를 바인딩하고 클라이언트는 현재 리소스를 소유하고 있는 노드에 관계없이 가상 이름이나 IP 주소를 사용하여 고가용성 대상에 연결할 수 있습니다. VNN은 클러스터에서 관리하는 네트워크 이름과 주소이며 클러스터 서비스는 장애 조치(failover) 이벤트 중에 노드 간에 네트워크 주소를 이동합니다. 실패가 발생하면 원래 주 복제본에서 주소가 오프라인 상태로 전환되고 새 주 복제본에서 온라인 상태가 됩니다.

Azure Virtual Machines에서는 트래픽을 클라이언트에서 클러스터형 리소스의 Virtual Network 이름(장애 조치(failover) 클러스터 인스턴스 또는 가용성 그룹의 수신기)으로 라우팅하려면 추가 구성 요소가 필요합니다. Azure에서 부하 분산 장치는 클러스터형 SQL Server 리소스가 사용하는 VNN의 IP 주소를 보유하며 트래픽을 적절한 고가용성 대상으로 라우팅하는 데 필요합니다. 또한 부하 분산 장치는 네트워킹 구성 요소를 사용하여 실패를 감지하고 주소를 새 호스트로 이동합니다. 

부하 분산 장치는 프런트 엔드에 도착하는 인바운드 흐름을 배포한 다음, 해당 트래픽을 백 엔드 풀로 정의된 인스턴스로 라우팅합니다. 부하 분산 규칙과 상태 프로브를 사용하여 트래픽 흐름을 구성합니다. SQL Server FCI를 사용하면 백 엔드 풀 인스턴스는 SQL Server를 실행하는 Azure 가상 머신이 되며 가용성 그룹을 사용하면 백 엔드 풀이 수신기가 됩니다. 부하 분산 장치를 사용하는 경우 경미한 장애 조치(failover) 지연이 있습니다. 기본적으로 상태 프로브가 10초마다 연결 검사를 수행하기 때문입니다. 

시작하려면 [장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-vnn-azure-load-balancer-configure.md)나 [가용성 그룹](availability-group-vnn-azure-load-balancer-configure.md)에 Azure Load Balancer를 구성하는 방법을 알아봅니다. 

**지원되는 OS**: 모두   
**지원되는 SQL 버전**: 모두   
**지원되는 HADR 솔루션**: 장애 조치(failover) 클러스터 인스턴스 및 가용성 그룹   

VNN 구성이 복잡할 수 있습니다. 이는 실패의 추가 원인이며 이로 인해 실패 검색에서 지연이 발생할 수 있으며 추가 리소스 관리와 관련된 오버헤드와 비용이 발생합니다. 이러한 제한 사항 중 일부를 해결하기 위해 SQL Server 2019에서는 분산 네트워크 이름 기능을 지원합니다. 

## <a name="distributed-network-name-dnn"></a>DNN(분산 네트워크 이름)

SQL Server 2019부터 분산 네트워크 이름 기능은 SQL Server 클라이언트가 부하 부산 장치를 사용하지 않고도 SQL Server 장애 조치(failover) 클러스터 인스턴스나 가용성 그룹 수신기에 연결할 수 있는 대체 방법을 제공합니다. 

DNN 리소스가 생성되면 클러스터는 클러스터에 있는 모든 노드의 IP 주소를 DNS 이름에 바인딩합니다. 클라이언트는 이 목록에 있는 각 IP 주소에 연결을 시도하여 연결할 리소스를 찾습니다. 연결 문자열에 `MultiSubnetFailover=True`를 지정하면 이 프로세스를 가속화할 수 있습니다. 이 설정은 공급자가 모든 IP 주소를 병렬로 시도 하도록 지시하므로 클라이언트는 FCI 또는 수신기에 즉시 연결할 수 있습니다. 

다음과 같은 이유로 가능하면 부하 분산 장치에 분산 네트워크 이름을 사용하는 것이 좋습니다. 
- 더 이상 부하 분산 장치 리소스를 유지 관리하지 않아도 되므로 엔드투엔드 솔루션이 더욱 강력해집니다. 
- 부하 분산 장치 프로브를 제거하면 장애 조치(failover) 기간이 최소화됩니다. 
- DNN은 Azure VM의 SQL Server를 사용하여 장애 조치(failover) 클러스터 인스턴스 또는 가용성 그룹 수신기의 프로비저닝 및 관리를 간소화합니다. 

DNN을 사용하는 경우 대부분의 SQL Server 기능이 FCI 및 가용성 그룹에서 투명하게 작동하지만 특별 고려 사항이 필요할 수 있는 특정 기능이 있습니다. 

**지원되는 OS**: Windows Server 2016 이상   
**지원되는 SQL 버전**: SQL Server 2019 CU2(FCI) 및 SQL Server 2019 CU8(AG)   
**지원되는 HADR 솔루션**: 장애 조치(failover) 클러스터 인스턴스 및 가용성 그룹   

시작하려면 [장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-distributed-network-name-dnn-configure.md)나 [가용성 그룹](availability-group-distributed-network-name-dnn-listener-configure.md)에 분산 네트워크 이름 리소스를 구성하는 방법을 알아봅니다.

다른 SQL Server 기능과 DNN을 사용하는 경우 추가로 고려할 사항이 있습니다. 자세한 내용은 [FCI 및 DNN 상호 운용성](failover-cluster-instance-dnn-interoperability.md)과 [AG 및 DNN 상호 운용성](availability-group-dnn-interoperability.md)을 참조하세요. 

## <a name="recovery-actions"></a>복구 작업

클러스터 서비스는 실패가 검색되면 정정 작업을 수행합니다. 이렇게 하면 기존 노드의 리소스를 다시 시작하거나 리소스를 다른 노드로 장애 조치(failover)할 수 있습니다. 정정 측정이 시작하면 완료하는 데 다소 시간이 걸립니다. 

예를 들어 다시 시작된 가용성 그룹은 다음 시퀀스에 따라 온라인 상태가 됩니다. 

1. 수신기 IP가 온라인 상태가 됩니다.
1. 수신기 네트워크 이름이 온라인 상태가 됩니다.
1. 가용성 그룹이 온라인 상태가 됩니다.
1. 개별 데이터베이스는 복구를 통해 이동하며 다시 실행 로그 길이와 같은 여러 가지 요인에 따라 시간이 걸릴 수 있습니다. 연결은 데이터베이스가 완전히 복구된 후에만 수신기에서 라우팅됩니다. 자세한 내용은 [장애 조치(failover) 시간 예측(RTO)](/sql/database-engine/availability-groups/windows/monitor-performance-for-always-on-availability-groups)를 참조하세요. 

복구에 시간이 걸릴 수 있으므로 적극적인 모니터링에서 20초 이내에 실패를 검색하도록 설정하면 일시적인 이벤트가 발생하는 경우(예: 메모리를 보존하는 [Azure VM 유지 관리](#azure-platform-maintenance))에 중단 시간이 발생할 수 있습니다. 모니터링을 더욱 완화된 값인 40초로 설정하면 서비스가 더 이상 중단되지 않는데 도움이 될 수 있습니다. 

임계값 설정을 조정하려면 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조하세요. 


## <a name="node-location"></a>노드 위치

Azure의 가상 머신에 있는 Windows 클러스터의 노드는 동일한 Azure 지역 내에서 물리적으로 분리되거나 다른 지역에 있을 수 있습니다. 이러한 거리는 클러스터 노드가 고유한 시설 내 위치 간에 넓게 있음을 나타내는 네트워크 대기 시간을 발생시킬 수 있습니다. 클라우드 환경에서 차이점은 지역 내에서 노드 간의 거리를 인식하지 못할 수 있다는 점입니다.  또한 물리적 및 가상 구성 요소, 홉 수와 같은 일부 다른 요소는 대기 시간 증가에 영향을 미칠 수 있습니다. 노드 간의 대기 시간이 중요한 경우 클러스터 노드를 [근접 배치 그룹](../../../virtual-machines/co-location.md) 내에 배치하여 네트워크 근접성을 보장하는 것이 좋습니다.

## <a name="resource-limits"></a>리소스 한계

Azure VM을 구성하는 경우 CPU, 메모리 및 IO의 컴퓨팅 리소스 제한을 결정합니다. 구매한 Azure VM 또는 디스크 제한 보다 더 많은 리소스가 필요한 워크로드로 인해 VM 성능 이슈가 발생할 수 있습니다. 성능 저하로 인해 클러스터 서비스 또는 SQL Server 고가용성 기능의 상태 검사가 실패할 수 있습니다. 리소스 병목 상태로 인해 노드나 리소스가 클러스터 또는 SQL Server에 표시될 수 있습니다. 

많은 SQL IO 작업 또는 백업, 인덱스 또는 통계 유지 관리와 같은 유지 관리 작업으로 인해 VM 또는 디스크가 *IOPS* 또는 *MBPS* 처리량 제한에 도달하여 SQL Server에서 *IsAlive/LooksAlive* 검사에 응답하지 않을 수 있습니다. 

SQL Server에 예기치 않은 장애 조치(failover)가 발생한 경우 모든 [성능 모범 사례](performance-guidelines-best-practices-checklist.md)를 준수하고 디스크나 VM 수준을 최대로 사용하는 서버를 모니터링하는지 확인합니다. 

## <a name="azure-platform-maintenance"></a>Azure 플랫폼 유지 관리

다른 클라우드 서비스와 마찬가지로 Azure에서는 가상 머신에 대한 호스트 인프라의 안정성, 성능 및 보안을 개선하기 위해 주기적으로 플랫폼을 업데이트합니다. 이러한 업데이트의 용도는 호스팅 환경의 소프트웨어 구성 요소 패치에서 네트워킹 구성 요소 업그레이드 또는 하드웨어 서비스 해제에 이르기까지 다양합니다.

대부분의 플랫폼 업데이트는 고객 VM에 영향을 주지 않습니다. 영향을 주지 않는 업데이트가 불가능한 경우 Azure는 고객 VM에 미치는 영향이 가장 적은 업데이트 메커니즘을 선택합니다. 영향이 거의 없는 유지 관리는 VM을 10초 미만으로 일시 중지합니다. 경우에 따라 Azure는 메모리 보존 유지 관리 메커니즘을 사용합니다. 이러한 메커니즘은 VM을 최대 30초 동안 일시 중지하고 메모리를 RAM에 보존합니다. 그런 다음, VM이 다시 시작되고 클록이 자동으로 동기화됩니다.

메모리 보존 유지 관리는 90%를 초과하는 Azure VM에서 작동합니다. G, M, N 및 H 시리즈에서는 작동하지 않습니다. Azure는 실시간 마이그레이션 기술을 점점 더 많이 사용하며 메모리 보존 유지 관리 메커니즘을 개선하여 일시 중지 기간을 줄이고 있습니다. VM을 다른 호스트로 실시간 마이그레이션하는 경우 일부 중요한 워크로드(예: SQL Server)는 VM 일시 중지까지 몇 분간 성능이 약간 저하될 수 있습니다.

플랫폼 유지 관리 중 리소스 병목 상태가 발생하면 AG 또는 FCI가 클러스터 서비스에 표시될 수 있습니다. 자세한 내용은 이 문서의 [리소스 제한](#resource-limits) 섹션을 참조하세요. 

적극적인 클러스터 모니터링을 사용하는 경우 확장된 VM이 일시 중지되면 장애 조치(failover)가 트리거될 수 있습니다. 장애 조치(failover)를 수행하면 가동 중지 시간이 유지 관리 일시 중지 시보다 더 늘어납니다. 따라서 유지 관리를 위해 VM이 일시 중지된 상태에서 장애 조치(failover)가 트리거되지 않도록 완화된 모니터링을 사용하는 것이 좋습니다. Azure VM에서 클러스터 임계값을 설정하는 방법에 대한 자세한 내용은 [클러스터 모범 사례](hadr-cluster-best-practices.md)를 참조하세요.

## <a name="limitations"></a>제한 사항

FCI나 가용성 그룹을 사용하고 Azure Virtual Machines에서 SQL Server를 사용하여 작업하는 경우 다음 제한 사항을 고려합니다. 

### <a name="msdtc"></a>MSDTC 

Azure Virtual Machines는 CSV(클러스터형 공유 볼륨)의 스토리지와 [Azure 표준 Load Balancer](../../../load-balancer/load-balancer-overview.md)가 있는 Windows Server 2019에서 또는 Azure 공유 디스크를 사용하는 SQL Server VM에서 MSDTC(Microsoft Distributed Transaction Coordinator)를 지원합니다. 

Azure Virtual Machines는 클러스터링된 공유 볼륨이 있는 Windows Server 2016 또는 이전 버전에서 MSDTC를 지원하지 않으며, 이유는 다음과 같습니다.

- 클러스터형 MSDTC 리소스는 공유 스토리지를 사용하도록 구성할 수 없습니다. Windows Server 2016에서 MSDTC 리소스를 만드는 경우 스토리지가 사용 가능하더라도 사용 가능한 공유 스토리지가 표시되지 않습니다. 이 문제는 Windows Server 2019에서 수정되었습니다.
- 기본 부하 분산 장치는 RPC 포트를 처리하지 않습니다.



## <a name="next-steps"></a>다음 단계

Azure VM에서 SQL Server와 함께 Windows 장애 초지(failover) 클러스터를 사용하는 경우의 차이점을 학습했습니다. 이제는 고가용성 기능 [고가용성 그룹](availability-group-overview.md)이나 [장애 조치(failover) 클러스터 인스턴스](failover-cluster-instance-overview.md)에 대해 알아봅니다. 시작할 준비가 되면 구성 권장 사항의 [모범 사례](hadr-cluster-best-practices.md)를 검토하세요. 
