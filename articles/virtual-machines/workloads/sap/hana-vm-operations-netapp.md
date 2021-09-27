---
title: SAP HANA Azure 가상 머신 ANF 구성 | Microsoft Docs
description: SAP HANA에 대한 Azure NetApp Files Storage 권장 사항.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure, ANF, HANA, Azure NetApp Files, 스냅샷
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/08/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4683b38ba6a59b0a50f7e0ea4165657407b59b69
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823182"
---
# <a name="nfs-v41-volumes-on-azure-netapp-files-for-sap-hana"></a>SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨

Azure NetApp Files는 **/hana/shared**, **/hana/data** 및 **/hana/log** 볼륨에 사용할 수 있는 네이티브 NFS 공유를 제공합니다. **/hana/data** 및 **/hana/log** 볼륨에 ANF 기반 NFS 공유를 사용하려면 v4.1 NFS 프로토콜을 사용해야 합니다. 공유가 ANF를 기반으로 하는 경우 NFS 프로토콜 v3은 **/hana/data** 및 **/hana/log** 볼륨에서 사용이 지원되지 않습니다. 


> [!IMPORTANT]
> Azure NetApp Files에서 구현된 NFS v3 프로토콜은 **/hana/data** 및 **/hana/log** 에서 사용이 지원되지 **않습니다**. **/hana/data** 및 **/hana/log** 볼륨에는 기능 관점에서 NFS 4.1 사용이 필수입니다. 반면 **/hana/shared** 볼륨에는 기능 관점에서 NFS v3 또는 NFS v4.1 프로토콜을 사용할 수 있습니다.

## <a name="important-considerations"></a>중요 고려 사항

SAP Netweaver 및 SAP HANA에 Azure NetApp Files를 고려하는 경우 다음과 같은 중요한 사항을 고려해야 합니다.

- 최소 용량 풀은 4TiB입니다.  
- 최소 볼륨 크기는 100GiB입니다.
- Azure NetApp Files와 Azure NetApp Files 볼륨이 탑재된 모든 가상 머신은 동일한 Azure Virtual Network나 동일한 지역의 [피어링된 가상 네트워크](../../../virtual-network/virtual-network-peering-overview.md)에 있어야 합니다.
- 짧은 대기 시간을 위해 Azure NetApp 스토리지와 근접하게 가상 머신을 배포하는 것이 중요합니다.  
- 선택한 가상 네트워크에 Azure NetApp Files로 위임된 서브넷이 있어야 합니다.
- 데이터베이스 서버에서 ANF 볼륨까지의 대기 시간이 1밀리초 미만으로 측정되는지 확인합니다.
- Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 할당량과 서비스 수준의 함수입니다. HANA Azure NetApp 볼륨을 크기 조정할 때 결과 처리량이 HANA 시스템 요구 사항을 충족해야 합니다. 또는 볼륨 용량 및 처리량을 독립적으로 구성하고 확장할 수 있는 [수동 QoS](../../../azure-netapp-files/manual-qos-capacity-pool-introduction.md) 용량 풀을 사용하는 것이 [좋습니다(이 문서의](../../../azure-netapp-files/manual-qos-capacity-pool-introduction.md) 특정 예제에 SAP HANA).
- 볼륨을 "통합"하여 더 큰 볼륨에서 더 많은 성능을 얻으세요. 예를 들어 /sapmnt, /usr/sap/trans, ...에 대해 하나의 볼륨을 사용합니다. 가능한 경우  
- Azure NetApp Files는 [내보내기 정책](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)을 제공합니다. 사용자는 허용되는 클라이언트, 액세스 유형(읽기 및 쓰기, 읽기 전용 등)을 제어할 수 있습니다. 
- Azure NetApp Files 기능은 아직 영역을 인식하지 않습니다. 현재 Azure NetApp Files 기능은 Azure 지역의 모든 가용성 영역에 배포되지 않습니다. 일부 Azure 지역에서 잠재적 대기 시간 영향을 염두에 두어야 합니다.   
- 가상 머신의 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID는 Azure NetApp Files의 구성과 일치해야 합니다. 

> [!IMPORTANT]
> SAP HANA 워크로드의 경우 짧은 대기 시간이 매우 중요합니다. Microsoft 담당자와 협력하여 가상 머신과 Azure NetApp Files 볼륨이 근접하게 배포되도록 해야 합니다.  

> [!IMPORTANT]
> 가상 머신과 Azure NetApp 구성 간에 <b>sid</b>adm 사용자 ID 및 `sapsys` 그룹 ID가 일치하지 않는 경우 VM에 탑재된 Azure NetApp 볼륨의 파일에 대한 권한은 `nobody`로 표시됩니다. Azure NetApp Files에 [새 시스템을 온보딩](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)할 때 <b>sidadm</b> 사용자 ID 및 `sapsys` 그룹 ID를 올바로 지정해야 합니다.


## <a name="sizing-for-hana-database-on-azure-netapp-files"></a>Azure NetApp Files에서 HANA 데이터베이스 크기 조정

Azure NetApp 볼륨의 처리량은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 설명된 대로 볼륨 크기와 서비스 수준의 함수입니다. 

크기 및 성능 관계와 서비스의 스토리지 엔드포인트에 대한 물리적 제한이 있다는 점을 이해하는 것이 중요합니다. 각 스토리지 엔드포인트는 볼륨을 만들 때 [위임된 서브넷 Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)에 동적으로 삽입되며 IP 주소를 받습니다. Azure NetApp Files 볼륨은 사용 가능한 용량 및 배포 논리에 따라 스토리지 엔드포인트를 공유합니다

아래 표는 단일 용량의 최대 물리적 대역폭 용량을 초과하기 때문에 백업을 저장하기 위해 큰 "Standard" 볼륨을 만드는 것이 합리적이며 12TB 이상의 "Ultra" 볼륨을 만드는 것은 의미가 없음을 보여줍니다. 

용량과 단일 Linux 세션의 최대 쓰기 처리량은 1.2~1.4GB/초입니다. /hana/data에 대해 더 많은 처리량이 필요한 경우 SAP HANA 데이터 볼륨 파티셔닝을 사용하여 데이터를 다시 로드하는 동안 I/O 작업을 스트라이핑하거나 여러 NFS 공유에 있는 여러 HANA 데이터 파일에 대해 HANA 저장점을 스트라이핑할 수 있습니다. 읽기 처리량을 개선하기 위해 NFS nconnect 탑재 옵션을 사용할 수 있습니다. Azure NetApp Files Linux 성능 및 nconnect에 대한 자세한 내용은 [Azure NetApp Files Linux NFS 탑재 옵션 모범 사례를 읽어보세요.](../../../azure-netapp-files/performance-linux-mount-options.md) HANA 데이터 볼륨 스트라이핑에 대한 자세한 내용은 다음 문서를 참조하세요.

- [HANA 관리자 가이드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [SAP HANA - 데이터 볼륨 분할에 대한 블로그](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP Note #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP Note #2700123](https://launchpad.support.sap.com/#/notes/2700123)


| 크기  | 처리량 Standard | 처리량 Premium | 처리량 Ultra |
| --- | --- | --- | --- |
| 1TB | 16MB/초 | 64MB/초 | 128MB/초 |
| 2TB | 32MB/초 | 128MB/초 | 256MB/초 |
| 4 TB | 64MB/초 | 256MB/초 | 512MB/초 |
| 10TB | 160MB/초 | 640MB/초 | 1,280MB/초 |
| 15TB | 240MB/초 | 960MB/초 | 1,400MB/초<sup>1</sup> |
| 20TB | 320MB/초 | 1,280MB/초 | 1,400MB/초<sup>1</sup> |
| 40TB | 640MB/초 | 1,400MB/초<sup>1</sup> | 1,400MB/초<sup>1</sup> |

<sup>1:</sup>쓰기 또는 단일 세션 읽기 처리량 제한(NFS 탑재 옵션 nconnect를 사용하지 않는 경우) 

데이터가 스토리지 백 엔드의 동일한 SSD에 기록된다는 점을 이해하는 것이 중요합니다. 환경을 관리할 수 있도록 용량 풀의 성능 할당량이 생성되었습니다.
스토리지 KPI는 모든 HANA 데이터베이스 크기에 대해 동일합니다. 거의 모든 경우에서 이러한 가정은 현실과 고객의 기대를 반영하지 않습니다. HANA 시스템의 크기는 반드시 작은 시스템의 스토리지 처리량은 낮아야 하고, 큰 시스템의 스토리지 처리량은 커야 한다는 의미가 아닙니다. 그러나 일반적으로 대규모 HANA 데이터베이스 인스턴스에 대한 처리량 요구 사항이 더 높을 것으로 예상할 수 있습니다. 기본 하드웨어에 대한 SAP의 크기 조정 규칙의 결과로 이러한 대규모 HANA 인스턴스는 인스턴스 재시작 후 데이터 로드와 같은 작업에서 더 많은 CPU 리소스를 제공하고 더 높은 병렬성을 제공합니다. 결과적으로 볼륨 크기는 고객의 기대와 요구 사항에 맞게 채택되어야 합니다. 그리고 순수 용량 요구 사항에 따라 달라지는 것도 아닙니다.

Azure에서 SAP용 인프라를 설계할 때 다음의 최소 처리량 특성으로 변환되는 SAP의 최소 스토리지 처리량 요구 사항(프로덕션 시스템)에 대해 알고 있어야 합니다.

| 볼륨 유형 및 I/O 유형 | SAP에서 요구하는 최소 KPI | Premium 서비스 계층 | Ultra 서비스 수준 |
| --- | --- | --- | --- |
| 로그 볼륨 쓰기 | 250MB/초 | 4 TB | 2TB |
| 데이터 볼륨 쓰기 | 250MB/초 | 4 TB | 2TB |
| 데이터 볼륨 읽기 | 400MB/초 | 6.3TB | 3.2TB |

세 가지 KPI가 모두 요구되므로 최소 읽기 요구 사항을 충족하려면 **/hana/data** 볼륨의 크기를 더 큰 용량으로 조정해야 합니다. 수동 QoS 용량 풀을 사용하는 경우 볼륨의 크기와 처리량을 독립적으로 정의할 수 있습니다. 용량과 처리량은 모두 동일한 용량 풀에서 가져와지므로 풀의 서비스 수준과 크기는 총 성능을 제공할 만큼 충분히 커야 [합니다(여기](../../../azure-netapp-files/manual-qos-capacity-pool-introduction.md)예제 참조).

높은 대역폭이 필요 없는 HANA 시스템의 경우 더 작은 볼륨 크기 또는 수동 QoS의 경우 처리량을 직접 조정하여 ANF 볼륨 처리량을 낮출 수 있습니다. HANA 시스템에 더 많은 처리량이 필요한 경우 온라인으로 용량 크기를 조정하여 볼륨을 조정할 수 있습니다. 백업 볼륨에 대해 정의된 KPI는 없습니다. 그러나 백업 볼륨 처리량은 성능이 좋은 환경에 필수적입니다. 로그 및 데이터 볼륨 성능은 고객의 기대에 맞게 설계되어야 합니다.

> [!IMPORTANT]
> 단일 NFS 볼륨에 배포하는 용량에 관계없이 처리량은 단일 세션의 소비자가 활용하는 1.2~1.4GB/초 대역폭의 범위에서 안정될 것으로 예상됩니다. 이는 ANF 제품의 기본 아키텍처와 NFS 관련 Linux 세션 제한과 관련이 있습니다. [Azure NetApp Files에 대한 성능 벤치 마크 테스트 결과](../../../azure-netapp-files/performance-benchmarks-linux.md) 문서에 설명된 성능 및 처리량 수치는 여러 클라이언트 VM이 있는 한 공유 NFS 볼륨에서 여러 세션에 걸쳐 테스트한 결과입니다. 이 시나리오는 SAP에서 측정하는 시나리오와 다릅니다. 즉, ANF에서 호스트되는 단일 NFS 볼륨에 대해 단일 VM에서 처리량을 측정합니다.

데이터 및 로그에 대한 SAP 최소 처리량 요구 사항을 충족하기 위해 또한 **/hana/shared** 에 대한 지침에 따라 권장 크기는 다음과 같습니다.

| 볼륨 | 크기<br /> Premium Storage 계층 | 크기<br /> Ultra Storage 계층 | 지원되는 NFS 프로토콜 |
| --- | --- | --- |
| /hana/log/ | 4TiB | 2TiB | v4.1 |
| /hana/data | 6.3TiB | 3.2TiB | v4.1 |
| /hana/shared scale-up | 최소(1TB, 1 x RAM)  | 최소(1TB, 1 x RAM) | v3 또는 v4.1 |
| /hana/shared scale-out | 작업자 노드의 1 x RAM<br /> 4개 작업자 노드 당  | 작업자 노드의 1 x RAM<br /> 4개 작업자 노드 당  | v3 또는 v4.1 |
| /hana/logbackup | 3 x RAM  | 3 x RAM | v3 또는 v4.1 |
| /hana/backup | 2 x RAM  | 2 x RAM | v3 또는 v4.1 |

모든 볼륨에 대해 NFS v4.1이 매우 권장됩니다.

백업 볼륨의 크기는 추정치입니다. 정확한 요구 사항은 워크로드 및 운영 프로세스에 따라 정의해야 합니다. 백업의 경우 다양한 SAP HANA 인스턴스에 대한 많은 볼륨을 하나(또는 두 개)의 더 큰 볼륨으로 통합할 수 있으며, ANF의 서비스 수준이 더 낮을 수 있습니다.

> [!NOTE]
> 이 설명서에 명시된 Azure NetApp Files 크기 조정 권장 사항은 SAP가 인프라 공급자에게 제시하는 최소 요구 사항을 대상으로 합니다. 실제 고객 배포 및 워크로드 시나리오에서는 충분하지 않을 수 있습니다. 이러한 권장 사항을 시작점으로 삼아 워크로드의 요구 사항에 따라 조정합니다.  

따라서 Ultra Disk 스토리지에 대해 나열된 것처럼 ANF 볼륨에 비슷한 처리량을 배포하는 것을 고려할 수 있습니다. 또한 이미 Ultra Disk 테이블에서 수행한 것처럼 다른 VM SKU의 볼륨에 대해 나열된 크기에 대해서도 크기를 고려합니다.

> [!TIP]
> 볼륨을 `unmount`하거나 가상 머신을 중지하거나 SAP HANA를 중지하지 않고도 Azure NetApp Files 볼륨의 크기를 동적으로 조정할 수 있습니다. 그러므로 애플리케이션이 예상된 처리량 수요와 예측하지 못한 처리량 수요를 모두 충족할 수 있습니다.

ANF에서 호스트되는 NFS v4.1 볼륨을 사용하는 대기 노드로 SAP HANA 스케일 아웃 구성을 배포하는 방법에 대한 설명서는 [SUSE Linux Enterprise Server의 Azure NetApp Files를 사용하여 Azure VM의 대기 노드로 SAP HANA 스케일 아웃](./sap-hana-scale-out-standby-netapp-files-suse.md)에 게시되어 있습니다.


## <a name="availability"></a>가용성
ANF 시스템 업데이트 및 업그레이드는 고객 환경에 영향을 주지 않고 적용됩니다. 정의된 [SLA는 99.99%](https://azure.microsoft.com/support/legal/sla/netapp/)입니다.


## <a name="volumes-and-ip-addresses-and-capacity-pools"></a>볼륨, IP 주소 및 용량 풀
ANF를 사용하는 경우, 기본 인프라가 구축되는 방식을 이해하는 것이 중요합니다. 용량 풀은 용량 풀 서비스 수준에 따라 용량과 성능 예산과 청구 단위를 제공하는 구문입니다. 용량 풀은 기본 인프라와 물리적 관계가 없습니다. 서비스에서 볼륨을 만들 때 스토리지 엔드포인트가 만들어집니다. 볼륨에 대한 데이터 액세스를 제공하기 위해 이 스토리지 엔드포인트에 단일 IP 주소가 할당됩니다. 여러 볼륨을 만드는 경우, 모든 볼륨이 이 스토리지 엔드포인트에 연결된 기본 운영 체제 미설치 장치로 분산됩니다. ANF에는 구성된 스토리지의 볼륨 또는/및 용량이 사전 정의된 내부 수준에 도달하면 고객 워크로드를 자동으로 배포하는 논리가 있습니다. 새 IP 주소를 사용하는 새 스토리지 엔드포인트는 볼륨에 액세스하기 위해 자동으로 생성되기 때문에 이러한 경우를 알 수 있습니다. ANF 서비스는 이 배포 논리에 대해 고객 제어를 제공하지 않습니다.

## <a name="log-volume-and-log-backup-volume"></a>로그 볼륨 및 로그 백업 볼륨
"로그 볼륨"( **/hana/log**)은 온라인 다시 실행 로그를 작성하는 데 사용됩니다. 따라서 이 볼륨에 열려 있는 파일이 있으므로 이 볼륨을 스냅샷하는 것은 의미가 없습니다. 온라인 다시 실행 로그 파일은 온라인 다시 실행 로그 파일이 가득 차거나 다시 실행 로그 백업이 실행되면 로그 백업 볼륨에 보관되거나 백업됩니다. 적절한 백업 성능을 제공하려면 로그 백업 볼륨에 양호한 처리량이 필요합니다. 스토리지 비용을 최적화하려면 여러 HANA 인스턴스의 로그 백업 볼륨을 통합하는 것이 좋습니다. 따라서, 여러 HANA 인스턴스는 동일한 볼륨을 활용하고 백업을 다른 디렉터리에 기록합니다. 이러한 통합을 사용하면 볼륨을 조금 더 크게 만들어야 하므로 더 많은 처리량을 얻을 수 있습니다. 

전체 HANA 데이터베이스 백업 쓰기를 사용하는 볼륨에도 동일하게 적용됩니다.  
 

## <a name="backup"></a>Backup
[Azure Virtual Machines의 SAP HANA 백업 가이드](../../../backup/sap-hana-db-about.md)에 설명된 대로 스트리밍 백업 및 Azure Backup 서비스의 SAP HANA 데이터베이스 백업 외에도 Azure NetApp Files는 스토리지 기반 스냅샷 백업을 수행할 수 있는 가능성을 열어줍니다. 

SAP HANA는 다음을 지원합니다.

- SAP HANA 1.0 SPS7 이상의 단일 컨테이너 시스템에 대한 Storage 기반 스냅샷 백업 지원 
- SAP HANA 2.0 SPS1 이상의 단일 테넌트에서 MDC(다중 데이터베이스 컨테이너) HANA 환경에 대한 Storage 기반 스냅샷 백업 지원
- SAP HANA 2.0 SPS4 이상의 다중 테넌트에서 MDC(다중 데이터베이스 컨테이너) HANA 환경에 대한 Storage 기반 스냅샷 백업 지원


스토리지 기반 스냅샷 백업 만들기는 간단한 4단계 절차입니다. 
1. HANA(내부) 데이터베이스 스냅샷 만들기 - 사용자 또는 도구에서 수행해야 하는 작업 
1. SAP HANA는 데이터 파일에 데이터를 기록하여 스토리지에서 일관된 상태로 만들기 - HANA가 HANA 스냅샷을 만든 결과로 이 단계를 수행
1. 스토리지의 **/hana/data** 볼륨에 스냅샷 만들기 - 사용자 또는 도구에서 수행해야 하는 단계. **/Hana/log** 볼륨에서 스냅샷을 수행할 필요는 없음
1. HANA(내부) 데이터베이스 스냅샷을 삭제하고 정상 작동 재개 - 사용자 또는 도구에서 수행해야 하는 단계

> [!WARNING]
> 마지막 단계가 누락되거나 마지막 단계를 수행하지 못하면 SAP HANA의 메모리 수요에 심각한 영향을 미치고 SAP HANA가 중단될 수 있습니다.

```
BACKUP DATA FOR FULL SYSTEM CREATE SNAPSHOT COMMENT 'SNAPSHOT-2019-03-18:11:00';
```

![SAP HANA에 대한 ANF 스냅샷 백업](media/hana-vm-operations-netapp/storage-snapshot-scenario.png)

```
az netappfiles snapshot create -g mygroup --account-name myaccname --pool-name mypoolname --volume-name myvolname --name mysnapname 
```

![SAP HANA에 대한 ANF 스냅샷 백업 파트2](media/hana-vm-operations-netapp/storage-snapshot.png)

```
BACKUP DATA FOR FULL SYSTEM CLOSE SNAPSHOT BACKUP_ID 47110815 SUCCESSFUL SNAPSHOT-2020-08-18:11:00';
```

이 스냅샷 백업 절차는 다양한 도구를 사용하여 다양한 방법으로 관리할 수 있습니다. 한 가지 예가 GitHub에서 사용할 수 있는 python 스크립트"ntaphana_azure.py"입니다. [https://github.com/netapp/ntaphana](https://github.com/netapp/ntaphana) 이것은 유지 관리 또는 지원 없이 "있는 그대로" 제공되는 샘플 코드입니다.



> [!CAUTION]
> 스냅샷 자체는 방금 스냅샷한 볼륨과 동일한 물리적 스토리지에 있으므로 보호된 백업이 아닙니다. 하루에 하나 이상의 스냅샷을 다른 위치로 "보호"해야 합니다. 이는 동일한 환경, 원격 Azure 지역 또는 Azure Blob 스토리지에서 수행할 수 있습니다.

저장소 스냅숏 기반 응용 프로그램 일치 백업에 사용할 수 있는 솔루션:

- Microsoft [Azure 애플리케이션 AzAcSnap (일관적인 스냅숏 도구)](../../../azure-netapp-files/azacsnap-introduction.md) 는 저장소 스냅숏을 만들기 전에 응용 프로그램 일치 상태로 전환 하는 데 필요한 모든 오케스트레이션을 처리 하 여 해당 데이터베이스를 작동 상태로 반환 하는 명령줄 도구입니다. AzAcSnap는 HANA Large Instance 및 Azure NetApp Files에 대 한 스냅숏 기반 백업을 지원 합니다. 자세한 내용은 Azure 애플리케이션 일치 하는 스냅숏 도구를 참조 하세요. 
- Commvault 백업 제품 사용자의 경우 다른 옵션은 Commvault IntelliSnap 11.21 이상입니다. 이 버전의 Commvault는 Azure NetApp Files 스냅숏 지원을 제공 합니다. [Commvault IntelliSnap 11.21](https://documentation.commvault.com/11.21/essential/116350_getting_started_with_backup_and_restore_operations_for_azure_netapp_file_services_smb_shares_and_nfs_shares.html) 문서에서 자세한 정보를 제공합니다.



### <a name="back-up-the-snapshot-using-azure-blob-storage"></a>Azure Blob 스토리지를 사용하여 스냅샷 백업
Azure Blob 스토리지에 백업은 ANF 기반 HANA 데이터베이스 스토리지 스냅샷 백업을 비용 효율적이고 빠르게 저장할 수 있는 방법입니다. 스냅샷을 Azure Blob 스토리지에 저장하려면 AzCopy 도구를 사용하는 것이 좋습니다. 이 도구의 최신 버전을 다운로드하여 GitHub의 python 스크립트가 설치된 bin 디렉터리에 설치합니다.
최신 AzCopy 도구를 다운로드합니다.

```
root # wget -O azcopy_v10.tar.gz https://aka.ms/downloadazcopy-v10-linux && tar -xf azcopy_v10.tar.gz --strip-components=1
Saving to: ‘azcopy_v10.tar.gz’
```

가장 고급 기능은 SYNC 옵션입니다. SYNC 옵션을 사용하는 경우 azcopy는 원본과 대상 디렉터리를 동기화합니다. **--delete-destination** 매개 변수를 사용하는 것이 중요합니다. 이 매개 변수가 없으면 azcopy가 대상 사이트에서 파일을 삭제하지 않고 대상 쪽의 공간 사용이 증가합니다. Azure Storage 계정에 컨테이너를 만듭니다. 그런 다음, Blob 컨테이너에 대한 SAS 키를 만들고 스냅샷 폴더를 Azure Blob 컨테이너에 동기화합니다.

예를 들어 일별 스냅샷을 Azure Blob 컨테이너와 동기화하여 데이터를 보호해야 하는 경우가 있습니다. 그리고 해당 스냅샷만 보관해야 하며 아래 명령을 사용할 수 있습니다.

```
root # > azcopy sync '/hana/data/SID/mnt00001/.snapshot' 'https://azacsnaptmytestblob01.blob.core.windows.net/abc?sv=2021-02-02&ss=bfqt&srt=sco&sp=rwdlacup&se=2021-02-04T08:25:26Z&st=2021-02-04T00:25:26Z&spr=https&sig=abcdefghijklmnopqrstuvwxyz' --recursive=true --delete-destination=true
```

## <a name="next-steps"></a>다음 단계
문서 읽기:

- [Azure Virtual Machines의 SAP HANA 고가용성](./sap-hana-availability-overview.md)
