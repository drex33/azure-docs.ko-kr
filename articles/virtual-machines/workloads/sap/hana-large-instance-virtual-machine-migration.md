---
title: Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션| Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션하는 방법
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/28/2021
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e401ee0a8760033bfada5f054ad4c61904a62a6a
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136852"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure의 SAP HANA(대규모 인스턴스)를 Azure 가상 머신으로 마이그레이션
이 문서에서는 가능한 Azure 대규모 인스턴스 배포 시나리오를 설명하고 전환 가동 중지 시간을 최소화하는 계획 및 마이그레이션 방식을 제공합니다.

## <a name="overview"></a>개요
HLI(SAP HANA용 Azure 대규모 인스턴스)는 2016년 9월에 처음 발표되었습니다. 이후 많은 고객이 이 하드웨어를 메모리 내 컴퓨팅 플랫폼용 서비스로 채택했습니다. 최근 몇 년간 Azure VM(가상 머신) 크기 확장 및 HANA 스케일 아웃 배포 지원 기능이 엔터프라이즈 고객 대부분의 ERP 데이터베이스 용량 수요를 초과했습니다. 많은 고객이 물리적 서버의 SAP HANA 워크로드를 Azure VM으로 마이그레이션하는 데 관심을 보이고 있습니다.

이 문서는 단계별 구성 문서가 아닙니다. 일반적인 배포 모델을 설명하고 계획 및 마이그레이션 조언을 제공합니다. 전환 가동 중지 시간을 최소화하기 위해 준비하는 데 필요한 고려 사항을 설명하는 데 목적이 있습니다.

## <a name="assumptions"></a>가정
이 문서에서는 다음과 같이 가정합니다.
- 동종 HANA 데이터베이스 컴퓨팅 서비스를 HLI(Hana 대규모 인스턴스)에서 Azure VM으로 마이그레이션하는 것만이 유일한 고려 대상입니다. 중요한 소프트웨어 업그레이드나 패치 적용은 포함되지 않습니다. 사소한 업데이트에는 관련 SAP Note에 지원하는 것으로 명시되어 있는 최신 OS(운영 체제) 버전 또는 HANA 버전을 사용하는 것이 포함됩니다. 
- 모든 업데이트/업그레이드 작업은 마이그레이션 전후에 수행합니다.  예: SAP HANA MCOS를 MDC 배포로 변환. 
- 가동 중지 시간을 최소화해 주는 마이그레이션 방식은 SAP HANA 시스템 복제입니다. 다른 마이그레이션 방법은 이 문서의 범위에 포함되지 않습니다.
- 이 지침은 HLI의 Rev3 및 Rev4 SKU 모두에 적용됩니다.
- 마이그레이션 중에 HANA 배포 아키텍처는 주로 변경되지 않은 상태로 유지됩니다.  즉, 단일 인스턴스 DR(재해 복구)가 있는 시스템은 대상에서 동일하게 유지됩니다.
- 고객은 대상(예정) 아키텍처의 SLA(서비스 수준 약정)를 검토하고 이해했습니다. 
- HLI와 VM 간의 상업적 용어는 다릅니다. 비용을 관리하기 위해 VM의 사용량을 모니터링합니다.
- 고객은 HLI는 전용 컴퓨팅 플랫폼이며, VM은 아직 격리된 공유 인프라에서 실행된다는 점을 이해했습니다.
- 고객은 대상 VM이 원하는 아키텍처를 지원하는지 확인했습니다. SAP HANA 배포용으로 인증받은 지원되는 VM SKU 목록은 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)를 참조하세요.
- 고객은 디자인 및 마이그레이션 계획의 유효성을 검사했습니다.
- 재해 복구 VM을 주 사이트와 함께 계획합니다.  고객은 마이그레이션 후 VM에서 실행되는 주 사이트의 DR 노드로 HLI을 사용할 수 없습니다.
- 고객은 비즈니스 복구 가능성 및 규정 준수 요구 사항에 따라 필요한 백업 파일을 대상 VM에 복사했습니다. VM 액세스 가능 백업을 통해, 전환 기간 동안 특정 시점으로 복구를 수행할 수 있습니다.
- SAP HSR(HANA 시스템 복제)의 경우 고객이 [SLES](./high-availability-guide-suse-pacemaker.md) 및 [RHEL](./high-availability-guide-rhel-pacemaker.md)용 SAP HANA HA 가이드에 따라 STONITH 디바이스를 설정하고 구성해야 합니다.  HLI 사례와 같이 미리 구성되어 있지 않습니다.
- 이 마이그레이션 방식에 Optane 구성을 통한 HLI SKU는 포함되지 않습니다.

## <a name="deployment-scenarios"></a>배포 시나리오
모든 HLI 시나리오에서 Azure VM으로 마이그레이션할 수 있습니다. 일반적인 HLI 배포 모델이 다음 표에 요약되어 있습니다. 보조 Azure 서비스의 이점을 활용하려면 아키텍처를 약간 변경해야 할 수도 있습니다.

| 시나리오 ID | HLI 시나리오 | VM으로 마이그레이션 여부 | 설명 |
| --- | --- | --- | --- |
| 1 | [SID가 1개인 단일 노드](./hana-supported-scenario.md#single-node-with-one-sid) | 예 | - |
| 2 | [한 시스템에 여러 구성 요소가 있는 단일 노드(MCOS)](./hana-supported-scenario.md#single-node-mcos) | 예 | - |
| 3 | [스토리지 복제를 사용하는 DR이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-storage-replication) | 아니요 | Azure 가상 플랫폼에서는 스토리지 복제를 사용할 수 없습니다. 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경하세요. |
| 4 | [스토리지 복제를 사용하는 DR(다목적)이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-multipurpose-using-storage-replication) | 아니요 | Azure 가상 플랫폼에서는 스토리지 복제를 사용할 수 없습니다. 현재 DR 솔루션을 HSR 또는 백업/복원으로 변경하세요. |
| 5 | [고가용성을 위한 STONITH 포함 HSR](./hana-supported-scenario.md#hsr-with-stonith-for-high-availability) | 예 | 대상 VM에 대해 미리 구성된 SBD가 없습니다.  STONITH 솔루션을 선택하고 배포합니다.  가능한 옵션: Azure Fencing Agent([RHEL](./high-availability-guide-rhel-pacemaker.md), [SLES](./high-availability-guide-suse-pacemaker.md) 모두 지원) 및 STONITH 블록 디바이스(SBD). |
| 6 | [HSR을 통한 HA, 스토리지 복제를 통한 DR](./hana-supported-scenario.md#high-availability-with-hsr-and-dr-with-storage-replication) | 아니요 | DR용 스토리지 복제를 HSR 또는 백업/복원으로 바꿉니다. |
| 7 | [호스트 자동 장애 조치(Failover)(1+1)](./hana-supported-scenario.md#host-auto-failover-11) | 예 | Azure VM이 있는 공유 스토리지에서 ANF(Azure NetApp Files)를 사용합니다. |
| 8 | [대기 상태로 스케일 아웃](./hana-supported-scenario.md#scale-out-with-standby) | 예 | 스토리지 전용 ANF를 사용하는 M128s, M416s, M416ms VM이 있는 BW/4HANA입니다. |
| 9 | [대기 없이 스케일 아웃](./hana-supported-scenario.md#scale-out-without-standby) | 예 | M128s, M416s, M416ms VM이 있는 BW/4HANA(스토리지에서 ANF를 사용하거나 사용하지 않음)입니다. |
| 10 | [스토리지 복제를 사용하는 DR이 있는 스케일 아웃](./hana-supported-scenario.md#scale-out-with-dr-using-storage-replication) | 아니요 | DR용 스토리지 복제를 HSR 또는 백업/복원으로 바꿉니다. |
| 11 | [HSR을 사용하는 DR이 포함된 단일 노드](./hana-supported-scenario.md#single-node-with-dr-using-hsr) | 예 | - |
| 12 | [단일 노드 HSR에서 DR로(비용 최적화)](./hana-supported-scenario.md#single-node-hsr-to-dr-cost-optimized) | 예 | - |
| 13 | [HSR을 통한 HA 및 DR](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr) | 예 | - |
| 14 | [HSR을 통한 HA 및 DR(비용 최적화)](./hana-supported-scenario.md#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 예 | - |
| 15 | [HSR을 사용하는 DR이 있는 스케일 아웃](./hana-supported-scenario.md#scale-out-with-dr-using-hsr) | 예 | M128s, M416s, M416ms VM입니다(스토리지에서 ANF를 사용하거나 사용하지 않음). |


## <a name="source-hli-planning"></a>원본(HLI) 계획
HLI 서버를 온보딩할 때 Microsoft Service Management와 고객은 SAP HANA 데이터베이스 실행하기 위한 컴퓨팅, 네트워크, 스토리지 및 OS별 설정을 계획했습니다. Azure VM으로 마이그레이션에도 유사한 계획을 진행해야 합니다.

### <a name="sap-hana-housekeeping"></a>SAP HANA 하우스키핑 
원치 않는 오래된 데이터 또는 부실한 로그가 새 데이터베이스로 마이그레이션되지 않도록 데이터베이스 콘텐츠를 정리하는 것이 운영상 좋은 방법입니다.  하우스키핑에는 일반적으로 오래되거나, 만료되었거나, 비활성 상태인 데이터를 삭제 또는 보관하는 일이 포함됩니다.  이 '데이터 예방 조치'는 프로덕션 사용 전에 데이터 트리밍 유효성을 검사하기 위해 비프로덕션 시스템에서 테스트해야 합니다.

### <a name="allow-network-connectivity-for-new-vms-and-virtual-network"></a>새 VM 및 가상 네트워크에 대한 네트워크 연결 허용 
HLI 배포에서 네트워크는 [SAP HANA(대규모 인스턴스) 네트워크 아키텍처](./hana-network-architecture.md) 문서에 설명된 정보를 기반으로 설정되었습니다. 또한 네트워크 트래픽 라우팅은 [Azure의 라우팅](./hana-network-architecture.md#routing-in-azure) 섹션에 설명된 방식으로 수행됩니다.
- 새 VM 마이그레이션 대상이 이미 HLI에 연결할 수 있도록 허용된 IP 주소 범위를 사용하는 기존 가상 네트워크에 배치되어 있나요? 그러면 추가로 연결을 업데이트할 필요가 없습니다.
- 새 Azure VM이 새 Microsoft Azure Virtual Network에 배치(다른 지역에 배치될 수도 있음)되고 기존 가상 네트워크와 피어링되어 있나요? 그러면 원래 HLI 프로비저닝의 ExpressRoute 서비스 키와 리소스 ID를 사용하여 이 새 가상 네트워크 IP 범위에 대한 액세스를 허용할 수 있습니다. Microsoft Service Management와 협력하여 가상 네트워크와 HLI 연결이 가능하도록 설정합니다.  
    > [!NOTE]
    > 애플리케이션과 데이터베이스 계층 간의 네트워크 대기 시간을 최소화하려면 애플리케이션과 데이터베이스 계층이 모두 동일한 가상 네트워크에 있어야 합니다.  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>기존 앱 계층 가용성 집합, 가용성 영역 및 PPG(근접 배치 그룹)
현재 배포 모델은 특정 서비스 수준의 목표를 충족하도록 설계되었습니다. 이 경우, 대상 인프라가 설정된 목표를 충족하거나 초과해야 합니다.  
대부분 SAP 애플리케이션 서버는 가용성 집합에 배치됩니다. 현재 배포 서비스 수준이 만족스럽고 대상 VM이 HLI 논리적 이름의 호스트 이름을 가정한다면, VM의 IP를 가리키는 DNS(도메인 이름 서비스) 주소 확인을 업데이트하면 SAP 프로필을 업데이트하지 않고도 작동합니다.
- PPG를 사용하지 않는 경우에는 네트워크 지연 시간을 최소화하기 위해 모든 애플리케이션과 DB 서버를 동일한 영역에 배치해야 합니다.
- PPG를 사용하는 경우 이 문서의 뒷부분에 나오는 섹션인 [가용성 집합, 가용성 영역 및 근접 배치 그룹](#availability-sets-availability-zones-and-proximity-placement-groups)을 참조하세요.

### <a name="storage-replication-discontinuance-process-if-used"></a>스토리지 복제 중단 프로세스(사용되는 경우)
스토리지 복제를 DR 솔루션으로 사용한 경우 SAP 애플리케이션이 종료되면 종료합니다. 그전에 마지막 SAP HANA 카탈로그, 로그 파일 및 데이터 백업이 원격 DR HLI 스토리지 볼륨에 복제되어야 합니다. 이 복제는 물리적 서버에서 Azure VM으로 전환하는 동안 재해가 발생하는 경우에 중요합니다.

### <a name="data-backups-preservation-consideration"></a>데이터 백업 보존 고려 사항
Azure VM의 SAP HANA로 전환되면 HLI의 스냅샷 기반 데이터 및 로그 백업에 쉽게 액세스하거나 VM으로 복원할 수 없습니다. 잘라내기 몇 주 전부터 HLI에서 파일 수준 백업과 스냅숏을 만드는 것이 좋습니다. 이러한 백업은 새 SAP HANA VM에서 액세스할 수 있는 Azure Storage 계정에 복사해야 합니다. 초기 전환 기간에도 Azure 기반 백업에서 지정 시간 복구 요구 사항을 충족하기에 충분한 기록을 빌드하기 전에 파일 수준 백업을 수행합니다. 

HLI 콘텐츠를 백업하는 것은 매우 중요합니다. 또한 롤백이 필요한 경우 SAP 환경의 전체 백업에 쉽게 액세스할 수 있도록 하는 것이 좋습니다.

### <a name="adjusting-system-monitoring"></a>시스템 모니터링 조정 
다양한 도구를 사용하여 SAP 환경 내에서 모니터링하고 시스템에 대한 경고 알림을 보낼 수 있습니다. 모니터링 시 변경 사항을 통합하고 필요한 경우 경고 알림 수신자를 업데이트하려면 적절한 조치를 취해야 합니다.

### <a name="microsoft-operations-team-involvement"></a>Microsoft 운영 팀 참여 
기존 HLI 인스턴스를 기반으로 Azure Portal에서 티켓을 엽니다.  지원 티켓이 만들어지면 지원 엔지니어가 이메일을 통해 연락을 드립니다.  

### <a name="engage-microsoft-account-team"></a>Microsoft 계정 팀 참여
컴퓨팅 리소스에 불필요한 비용을 최소화하기 위해 HLI 계약의 갱신 기간이 다가올 때 마이그레이션을 계획합니다. HLI을 해제하려면 단위의 계약 해지 및 실제 종료 기간을 맞춰서 조정합니다.

## <a name="destination-planning"></a>대상 계획
신중하게 계획하는 것은 기존 인프라를 대신할 새 인프라를 배포하는 데 반드시 필요합니다. 새로운 추가 항목이 더 큰 체계에서 요구 사항을 충족하는지 확인합니다. 고려할 주요 사항은 다음과 같습니다.

### <a name="resource-availability-in-the-target-region"></a>대상 지역의 리소스 가용성 
현재 SAP 애플리케이션 서버의 배포 지역은 일반적으로 연결된 HLI와 가깝습니다. 그러나 HLI는 사용 가능한 Azure 지역보다 적은 위치에서 제공됩니다. 물리적 HLI를 Azure VM으로 마이그레이션할 때 성능을 최적화하려면 관련된 모든 서비스의 근접 거리도 '미세 조정'하는 것이 좋습니다.  이 작업을 수행하는 동안 선택한 지역에 필요한 리소스가 모두 있는지 확인합니다. 예를 들면, 특정 VM 제품군 또는 고가용성 설정 기능을 제공하는 Azure 영역에 대한 가용성을 확인하려고 할 수 있습니다.

### <a name="virtual-network"></a>가상 네트워크 
기존 가상 네트워크에서 새 HANA 데이터베이스를 실행하고 싶은가요, 아니면 새로 만들고 싶은가요? 주요 결정 요소는 SAP 환경에 대한 현재 네트워킹 레이아웃입니다. 또한 인프라가 1개 영역에서 2개 영역 배포로 변하고 PPG를 사용하면 아키텍처를 변경해야 합니다. 자세한 내용은 [SAP 애플리케이션에서 최적의 네트워크 대기 시간을 위한 Azure PPG](./sap-proximity-placement-scenarios.md) 문서를 참조하세요.   

### <a name="security"></a>보안
새 SAP HANA VM을 신규 또는 기존 vnet/서브넷 중 어디에서 사용하든, 이는 비즈니스에 중요한 새로운 서비스입니다. 보호할 가치가 있습니다. 액세스 제어가 회사의 보안 정책을 준수하는지 확인합니다.

### <a name="vm-sizing-recommendation"></a>VM 크기 조정 권장 사항
마이그레이션은 HANA 컴퓨팅 엔진의 크기를 적절하게 조정할 수 있는 기회이기도 합니다. HANA Studio에서 HANA [시스템 뷰](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)를 사용하여 시스템 리소스 사용량을 파악하고, 적절하게 크기를 조정함으로써 더욱 효율적으로 비용을 지출할 수 있습니다.

### <a name="storage"></a>Storage 
스토리지 성능은 SAP 애플리케이션 사용자 환경에 영향을 주는 요인 중 하나입니다. 지정된 VM SKU에 게시된 최소 스토리지 레이아웃이 있습니다. 자세한 내용은 [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)을 참조하세요. 새 HANA VM에 적절한 IO 용량과 성능을 보장하려면 이러한 사양을 검토하고 기존 HLI 시스템 통계와 비교해 보는 것이 좋습니다.

새 HANA VM과 연결된 서버에 대해 PPG를 구성할 예정인가요? 그러면 지원 티켓을 제출하여 스토리지와 VM의 공동 배치를 검사 및 확인합니다. 백업 솔루션을 변경해야 할 수도 있으므로, 운영 비용을 갑자기 지출하지 않도록 스토리지 비용도 다시 검토하는 것이 좋습니다.

### <a name="storage-replication-for-disaster-recovery"></a>재해 복구를 위한 스토리지 복제
HLI에서는 스토리지 복제가 기본 재해 복구 옵션으로 제공됩니다. 이 기능은 Azure VM의 SAP HANA에 대한 기본 옵션이 아닙니다. HSR, 백업/복원 또는 비즈니스 요구 사항을 충족하는 기타 지원 솔루션을 고려해 보세요.

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>가용성 집합, 가용성 영역 및 근접 배치 그룹 
애플리케이션 계층과 SAP HANA 간의 거리를 줄여 네트워크 대기 시간을 최소한으로 유지할 수 있습니다. 새 데이터베이스 VM과 현재 SAP 애플리케이션 서버를 PPG에 배치합니다. SAP 배포용 PPG에서 Azure 가용성 집합과 가용성 영역이 작동하는 방식을 알아보려면 [근접 배치 그룹](./sap-proximity-placement-scenarios.md)을 참조하세요.

HANA 시스템의 구성원이 둘 이상의 Azure 영역에 배포된 경우 선택한 영역의 대기 시간 프로필을 명확하게 볼 수 있어야 합니다. SAP 시스템 구성 요소를 배치하여 SAP 애플리케이션과 데이터베이스 간의 거리를 줄입니다. 공용 도메인 [가용성 영역 대기 시간 테스트 도구](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)를 사용하면 측정을 보다 쉽게 수행할 수 있습니다.  


### <a name="backup-strategy"></a>Backup 전략
많은 고객이 HLI에서 SAP HANA용 타사 백업 솔루션을 이미 사용하고 있습니다.  사용 중이라면 추가된 보호 VM과 HANA 데이터베이스만 구성해야 합니다. 마이그레이션 후 컴퓨터가 해제되면 진행 중인 HLI 백업 작업의 예약이 취소될 수 있습니다.

이제 VM에 SAP HANA용 Azure Backup이 일반 공급됩니다.  Azure VM의 SAP HANA 백업에 대한 자세한 내용은 [백업](../../../backup/backup-azure-sap-hana-database.md), [복원](../../../backup/sap-hana-db-restore.md) 및 [관리](../../../backup/sap-hana-db-manage.md)를 참조하세요.

### <a name="dr-strategy"></a>DR 전략
서비스 수준 목표가 더 긴 복구 시간을 수용할 경우 백업은 쉽게 수행할 수 있습니다. Blob Storage에 백업한 후 제자리에 복원하거나 새 VM으로 복원하는 것이 가장 간단하고 저렴한 DR 전략입니다.
 
대규모 인스턴스 플랫폼에서 HANA DR는 일반적으로 HSR를 사용하여 수행됩니다. Azure VM에서 HSR은 가장 자연스러운 네이티브 SAP HANA DR 솔루션이기도 합니다. 원본 배포가 단일 인스턴스인지, 클러스터링되었는지에 관계없이 DR 지역에는 원본 인프라의 복제본이 필요합니다.
이러한 DR 복제본은 주 HLI에서 VM으로 마이그레이션이 완료된 후에 구성됩니다.  DR HANA DB는 VM 인스턴스의 주 SAP HANA에 보조 복제 사이트로 등록됩니다.  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 애플리케이션 서버 연결 대상 변경
HSR 마이그레이션을 진행하면 새로운 HANA 데이트베이스 호스트와 애플리케이션 계층에 새로운 데이터베이스 호스트 이름이 생성됩니다. 새 호스트 이름을 반영하도록 SAP 프로필을 수정합니다. 호스트 이름을 유지하는 이름 확인으로 전환이 수행되는 경우 프로필을 변경할 필요가 없습니다.

### <a name="operating-system-os"></a>OS(운영 체제)
HLI/VM의 OS 이미지는 릴리스 수준이 동일한 경우(예: SLES 12 SP4)에도 같지 않습니다. HLI에 필요한 패키지, 핫 픽스, 패치, 커널 및 보안 수정의 유효성을 검사합니다. 그런 다음 대상에 동일한 패키지를 설치합니다.  HSR를 사용하여 이전 OS에서 최신 OS 버전을 사용하는 VM으로 복제할 수 있습니다.  [SAP Note 2763388](https://launchpad.support.sap.com/#/notes/2763388)을 검토하여 지원되는 버전을 확인하세요.

### <a name="new-sap-license-request"></a>새 SAP 라이선스 요청
VM으로 마이그레이션된 후 새 HANA 시스템에 대한 새 SAP 라이선스를 요청하는 간단한 호출입니다.

### <a name="service-level-agreement-sla-differences"></a>Service Level Agreement(서비스 수준 약정) 차이
HLI와 Azure VM 간의 가용성 SLA 차이에 대한 설명입니다.  예를 들어 클러스터형 HLI HA 쌍은 99.99%의 가용성을 제공합니다. 동일한 SLA를 얻으려면 가용성 영역에 VM을 배포해야 합니다. [Virtual Machines용 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)에서는 고객이 대상 인프라를 계획할 수 있도록 다양한 VM 구성의 가용성을 설명합니다.


## <a name="migration-strategy"></a>마이그레이션 전략
이 문서에서는 HLI에서 Azure VM으로 마이그레이션하기 위한 HANA 시스템 복제 방식만 다룹니다.  배포된 대상 스토리지 솔루션에 따라 프로세스는 약간 다릅니다. 대략적인 단계는 아래에 설명되어 있습니다.

### <a name="vm-with-premiumultra-disks-for-data"></a>데이터용 프리미엄 디스크/Ultra Disk가 있는 VM
프리미엄 디스크 또는 울트라 디스크로 배포된 VM의 경우 표준 SAP HANA 시스템 복제 구성이 HSR 설정에 적용됩니다. 시스템 복제 설정 단계에 대한 개요는 [SAP 도움말 문서](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)를 참조하세요. 이 문서에는 보조 시스템을 인수하고, 주 시스템으로 장애 복구(Failback)하며, 시스템 복제를 사용하지 않도록 설정하는 방법을 설명합니다. 마이그레이션에는 복제 단계를 설정, 인수하고 사용하지 않도록 설정하는 것만 필요합니다.  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>데이터 및 로그 볼륨에 대해 ANF를 사용하는 VM
상위 수준에서 전체 데이터 및 로그 볼륨의 최신 HLI 스토리지 스냅샷은 Azure Storage에 복사해야 합니다. 이 위치에서 대상 HANA VM을 통해 액세스할 수 있고 복구 가능합니다.  복사 프로세스는 네이티브 Linux 복사 도구를 사용하여 수행할 수 있습니다.  

> [!IMPORTANT]
> 복사 및 데이터 전송 시 HANA 데이터베이스 크기 및 네트워크 대역폭에 따라 시간이 오래 걸릴 수 있습니다. 대량 복사 프로세스는 주 HANA 데이터베이스 가동 중지 시간이 되기 전에 수행해야 합니다.

### <a name="mcos-to-mdc-conversion"></a>MCOS에서 MDC로 변환
MCOS(Multiple Components in One System) 배포 모델은 일부 HLI 고객이 사용했습니다. 이전 SAP HANA 버전의 MDC(Multiple Databases Container) 스토리지 스냅샷 제한을 피하기 위해 사용되었습니다.  MCOS 모델에서는 독립적인 여러 SAP HANA 인스턴스가 하나의 HANA 대용량 인스턴스에 누적됩니다. 마이그레이션에 HSR를 사용하면 잘 작동하지만, 테넌트 데이터베이스가 하나씩 있는 여러 HANA VM이 생성됩니다. 이 모델은 선호하는 것보다 더 분주한 환경을 만듭니다. SAP HANA 2.0의 기본 배포는 MDC입니다. 대안은 HSR 마이그레이션 후 [HANA 테넌트 전환](https://launchpad.support.sap.com/#/notes/2472478)을 수행하는 것입니다. HANA 테넌트 전환은 이러한 독립적인 HANA 데이터베이스를 단일 HANA 컨테이너의 코테넌트로 결합합니다.  

### <a name="application-layer-consideration"></a>애플리케이션 계층 고려 사항
데이터베이스 서버는 SAP 시스템의 중추로 간주됩니다.  모든 애플리케이션 서버는 SAP HANA 데이터베이스 근처에 있어야 합니다. 경우에 따라 새 PPG를 사용하려면 기존 애플리케이션 서버를 HANA VM이 있는 PPG로 전환해야 할 수도 있습니다.  이미 배포 템플릿이 준비되어 있으면 새로운 애플리케이션 서버를 빌드하는 것이 더 쉽다고 생각할 수 있습니다.

기존 애플리케이션 서버와 새로운 HANA VM을 최적으로 찾습니다. 그러면 더 큰 용량이 필요하지 않는 한 새로운 애플리케이션 서버를 빌드할 필요가 없습니다.

서비스 가용성을 향상시키기 위해 새 인프라를 빌드하는 경우 기존 애플리케이션 서버가 불필요해질 수 있습니다. 이 경우 종료하고 삭제할 수 있습니다.
대상 VM 호스트 이름이 변경되고 HLI 호스트 이름과 다른 경우, 새 호스트를 가리키도록 SAP 애플리케이션 서버 프로필을 조정해야 합니다. HANA 데이터베이스 IP 주소만 변경된 경우, 들어오는 연결을 새 HANA VM으로 유도하려면 DNS 레코드를 업데이트합니다.

### <a name="acceptance-test"></a>수용 테스트
HLI에서 VM으로 마이그레이션하는 경우 이기종 마이그레이션과 비교해 볼 때 데이터베이스 콘텐츠에 중요한 변경 사항이 없습니다. 그래도 새 설정의 성능을 확인하는 것이 좋습니다.  

### <a name="cutover-plan"></a>전환 계획
이 마이그레이션은 간단하지만 기존 데이터베이스를 해제해야 합니다.  대체(fallback)가 필요한 경우, 원본 시스템을 콘텐츠 및 백업 이미지와 함께 보존하려면 신중하게 계획을 세우는 것이 중요합니다. 계획을 잘 세우면 빠른 전환이 가능합니다.   

## <a name="post-migration"></a>마이그레이션 후
데이터 무결성을 보장하기 위해 HLI 종속 서비스와 연결을 안전하게 분리할 때까지 마이그레이션 작업이 완료되지 않습니다. 또한 불필요한 서비스는 종료하는 것이 좋습니다. 이 섹션에서는 몇 가지 더 중요한 항목을 설명합니다.

### <a name="decommissioning-the-hli"></a>HLI 서비스 해제
HANA 데이터베이스를 Azure VM으로 마이그레이션한 후 HLI 데이터베이스에서 비즈니스 트랜잭션이 실행되지 않는지 확인합니다.  그러나 HLI를 로컬 백업 보존 기간 동안 계속 실행하면 필요한 경우 복구 속도가 빨라집니다. 로컬 백업 보존 기간이 지난 경우에만 HANA 대용량 인스턴스를 해제해야 합니다. 그런 다음 Microsoft 담당자에게 연락하여 Microsoft와의 계약을 통해 HLI 약정을 체결합니다.

### <a name="remove-any-proxy-for-example-iptables-bigip-configured-for-hli"></a>HLI에 구성된 프록시(예: Iptables, BIGIP) 제거 
IPTables와 같은 프록시 서비스를 사용하여 HLI와 주고받는 온-프레미스 트래픽을 라우팅하는 경우 VM으로 마이그레이션하는 데 성공하면 해당 서비스가 필요하지 않습니다.  단, HLI가 대기 상태이면 이 연결 서비스를 유지해야 합니다.  HLI가 완전히 해제된 후에만 서비스를 종료합니다.

### <a name="remove-global-reach-for-hli"></a>HLI에 대한 Global Reach 제거 
Global Reach는 고객의 ExpressRoute 게이트웨이를 HLI ExpressRoute 게이트웨이와 연결하는 데 사용됩니다.  이를 통해 고객의 온-프레미스 트래픽이 프록시 서비스를 사용하지 않고 HLI 테넌트에 직접 도달할 수 있습니다. 이 연결은 마이그레이션 후 HLI 유닛이 없으면 더 이상 필요하지 않습니다. 그러나 IPTables 프록시 서비스와 마찬가지로, GlobalReach 역시 HLI가 완전히 해제될 때까지 유지되어야 합니다.

### <a name="operating-system-subscription--movereuse"></a>운영 체제 구독 - 이동/재사용
VM 서버가 배포되고 HLI가 해제되면 OS 구독을 바꾸거나 다시 사용할 수 있습니다. OS 라이선스에 대해 이중으로 요금을 지불할 필요가 없습니다.

## <a name="next-steps"></a>다음 단계

SAP 배포를 계획합니다.

> [!div class="nextstepaction"]
> [Azure의 SAP 워크로드: 계획 및 배포 검사 목록](sap-deployment-checklist.md)
