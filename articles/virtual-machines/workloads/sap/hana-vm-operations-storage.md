---
title: SAP HANA Azure 가상 머신 스토리지 구성 | Microsoft Docs
description: SAP HANA가 배포된 VM에 대한 스토리지 권장 사항입니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: SAP, Azure HANA, 스토리지 Ultra Disk, 프리미엄 스토리지
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/09/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0017344d745b29162aa36a492c31c1c48e6dd4d6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131466526"
---
# <a name="sap-hana-azure-virtual-machine-storage-configurations"></a>SAP HANA Azure 가상 머신 스토리지 구성

Azure에서는 SAP HANA를 실행하는 Azure VM에 적합한 다양한 스토리지 유형을 제공합니다. SAP HANA 배포에 고려할 수 있는 **SAP HANA 인증 Azure 스토리지 유형** 은 다음과 같습니다. 

- Azure 프리미엄 SSD 또는 프리미엄 스토리지 
- [Ultra Disk](../../disks-enable-ultra-ssd.md)
- [Azure NetApp Files](https://azure.microsoft.com/services/netapp/) 

이러한 디스크 유형에 대한 자세한 내용은 [SAP 워크로드에 대한 Azure Storage 유형](./planning-guide-storage.md) 및 [디스크 유형 선택](../../disks-types.md) 문서를 참조하세요.

Azure는 Azure 표준 및 프리미엄 스토리지에서 VHD를 배포하는 두 가지 방법을 제공합니다. [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)를 Azure 블록 스토리지 배포에 활용하는 것이 좋습니다. 

IOPS 및 스토리지 처리량에서 스토리지 유형 및 해당 SLA의 목록을 보려면 [관리 디스크에 대한 Azure 설명서](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

> [!IMPORTANT]
> 선택한 Azure 스토리지 유형과는 독립적으로 해당 스토리지에 사용되는 파일 시스템을 SAP에서 특정 운영 체제 및 DBMS에 대해 지원해야 합니다. [SAP Support Note #2972496](https://launchpad.support.sap.com/#/notes/2972496)에는 SAP HANA를 포함하여 다양한 운영 체제 및 데이터베이스에 지원되는 파일 시스템이 나와 있습니다. 이는 SAP HANA가 어떤 작업이든 읽기 및 쓰기를 위해 액세스할 수 모든 볼륨에 적용됩니다. 특히 SAP HANA용 Azure에서 NFS를 사용하는 경우 이 문서의 뒷부분에서 설명한 대로 NFS 버전의 추가 제한 사항이 적용됩니다. 


다양한 스토리지 유형에 대한 최소 SAP HANA 인증 조건은 다음과 같습니다. 

- Azure 프리미엄 스토리지 - Azure [쓰기 가속기](../../how-to-enable-write-accelerator.md)에서 **/hana/log** 를 지원해야 합니다. **/hana/data** 볼륨은 Azure 쓰기 가속기를 사용하지 않는 프리미엄 스토리지에 배치하거나 Ultra Disk에 배치할 수 있습니다.
- Azure Ultra Disk(최소한 **/hana/log** 볼륨용) - **/hana/data** 볼륨은 Azure 쓰기 가속기를 사용하지 않는 프리미엄 스토리지에 배치하거나 다시 시작 시간을 단축하기 위해 Ultra Disk에 배치할 수 있습니다.
- Azure NetApp Files 기반 **NFS v4.1** 볼륨( **/hana/log 및 /hana/data** 용) - /hana/shared의 볼륨은 NFS v3 또는 NFS v4.1 프로토콜을 사용할 수 있습니다.

일부 스토리지 유형은 결합할 수 있습니다. 예를 들어 필요한 짧은 대기 시간을 확보하기 위해 **/hana/data** 를 프리미엄 스토리지에 배치하고 **/hana/log** 를 Ultra Disk 스토리지에 배치할 수 있습니다. ANF 기반 볼륨을 **/hana/data** 에 사용하는 경우 **/hana/log** 볼륨도 ANF 기반의 NFS를 기반으로 해야 합니다. ANF 기반 NFS를 볼륨 중 하나(예: /hana/data)에 사용하고 Azure 프리미엄 스토리지 또는 Ultra Disk를 다른 볼륨(예: **/hana/log**)에 사용하는 것은 **지원되지 않습니다**.

온-프레미스 환경의 경우 I/O 하위 시스템 및 해당 성능은 신경 쓸 필요가 거의 없었습니다. SAP HANA에 대한 최소 스토리지 요구 사항을 충족하는 것은 어플라이언스 공급업체의 몫이었기 때문입니다. 자체적으로 Azure 인프라를 빌드하는 동안에는 그러한 SAP 발행 요구 사항 몇 가지에 대해 알아야 합니다. SAP에서 권장하는 최소 처리량 특성 중 일부는 다음과 같습니다.

- 250MB/초의 **/hana/log** 에서 1MB I/O 크기로 읽기/쓰기
- 16MB 및 64MB I/O 크기의 **/hana/data** 에 대해 400MB/초 이상의 읽기 작업
- 16MB 및 64MB I/O 크기의 **/hana/data** 에 대해 250MB/초 이상의 쓰기 작업

낮은 스토리지 대기 시간이 DBMS 시스템에 중요하므로 SAP HANA와 같이 DBMS의 경우에도 데이터를 메모리 내에 유지합니다. 스토리지에서 중요한 경로는 일반적으로 DBMS 시스템의 트랜잭션 로그 쓰기입니다. 그러나 쓰기 저장점 또는 충돌 복구 후 메모리 내 데이터 로드와 같은 작업도 중요할 수 있습니다. 따라서 Azure 프리미엄 스토리지, Ultra 디스크 또는 ANF는 **반드시** 반드시 **/hana/data** 및 **/hana/log** 볼륨에 활용해야 합니다. 


HANA에 대한 스토리지 구성을 선택하는 몇 가지 지침은 다음과 같이 나열할 수 있습니다.

- [SAP 워크로드에 대한 Azure Storage 유형](./planning-guide-storage.md) 및 [디스크 유형 선택](../../disks-types.md)을 기반으로 하여 스토리지 유형을 결정합니다.
- VM 크기를 조정하거나 VM을 결정하는 경우 전체 VM I/O 처리량 및 IOPS 제한을 고려합니다. 전체 VM 스토리지 처리량은 [메모리 최적화 가상 머신 크기](../../sizes-memory.md) 문서에 설명되어 있습니다.
- 스토리지 구성을 결정하는 경우 **/hana/data** 볼륨 구성을 사용하여 VM의 전체 처리량 미만으로 유지합니다. 저장점을 작성하면 SAP HANA에서 I/O를 적극적으로 실행할 수 있습니다. 저장점을 작성할 때 **/hana/data** 볼륨의 처리량 제한까지 쉽게 높일 수 있습니다. **/hana/data** 볼륨을 작성하는 디스크의 처리량이 VM에서 허용하는 것보다 더 높은 경우 저장점 쓰기에 사용되는 처리량이 다시 실행 로그 쓰기의 처리량 요구를 방해하는 상황이 발생할 수 있습니다. 이는 애플리케이션 처리량에 영향을 줄 수 있는 상황입니다.
- HANA 시스템 복제를 사용하려는 경우 HANA 시스템 복제 구성에 참여하는 모든 VM의 **/hana/data** 및 **/hana/log** 에 대해 정확히 동일한 유형의 Azure Storage를 사용해야 합니다. 예를 들어 한 VM의 **/hana/data** 에 대해 Azure Premium Storage를 사용하고 동일한 HANA 시스템 복제 구성 내의 다른 VM의 **/hana/log** 에 대해 Azure Ultra Disk를 사용하는 것은 지원되지 않습니다.


> [!IMPORTANT]
> 스토리지 구성에 대한 제안 사항은 시작하기 위한 지침을 의미합니다. 워크로드를 실행하고 스토리지 사용률 패턴을 분석하면 제공된 모든 스토리지 대역폭 또는 IOPS를 활용하지 않는다는 것을 알 수 있습니다. 그러면 스토리지 크기를 줄이는 것을 고려할 수 있습니다. 또는 반대로 워크로드에는 이러한 구성에서 제안된 것보다 더 많은 스토리지 처리량이 필요할 수 있습니다. 따라서 더 많은 용량, IOPS 또는 처리량을 배포해야 할 수 있습니다. 필요한 스토리지 용량, 필요한 스토리지 대기 시간, 필요한 스토리지 처리량/IOPS 및 가장 저렴한 구성 사이의 긴장 상황에서 Azure는 사용자와 HANA 워크로드에 적합한 절충점을 찾고 조정할 수 있는 다양한 기능과 다양한 가격을 갖춘 다양한 스토리지 유형을 제공합니다.


## <a name="stripe-sets-versus-sap-hana-data-volume-partitioning"></a>스트라이프 세트 및 SAP HANA 데이터 볼륨 분할
Azure 프리미엄 스토리지를 사용하면 여러 Azure 디스크에서 **/hana/data** 및/또는 **/hana/log** 볼륨을 스트라이프할 때 최상의 가격/성능 비율에 도달할 수 있습니다. 더 많은 IOPS 또는 처리량을 제공하는 더 큰 디스크 볼륨을 배포하는 대신, 지금까지 이 작업은 Linux의 일부인 LVM 및 MDADM 볼륨 관리자를 사용하여 수행되었습니다. 디스크를 스트라이프하는 방법은 수십 년 전부터 사용되었으며 잘 알려져 있습니다. 필요한 IOPS 또는 처리량 기능을 달성하는 데 이러한 스트라이프 볼륨이 유익한 만큼 복잡성도 스트라이프 볼륨 관리에 추가됩니다. 특히 볼륨 용량을 확장해야 하는 경우 SAP는 적어도 **/hana/data** 에 대해 여러 Azure 디스크에서 스트라이프하는 것과 동일한 목표를 달성하는 대체 방법을 도입했습니다. SAP HANA 2.0 SPS03부터 HANA 인덱스 서버는 여러 Azure 디스크에 있는 여러 HANA 데이터 파일에서 I/O 작업을 스트라이프할 수 있습니다. 이점은 여러 Azure 디스크에서 스트라이프 볼륨을 만들고 관리할 필요가 없다는 것입니다. 데이터 볼륨 분할의 SAP HANA 기능은 다음에 자세히 설명되어 있습니다.

- [HANA 관리자 가이드](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.05/en-US/40b2b2a880ec4df7bac16eae3daef756.html?q=hana%20data%20volume%20partitioning)
- [SAP HANA - 데이터 볼륨 분할에 대한 블로그](https://blogs.sap.com/2020/10/07/sap-hana-partitioning-data-volumes/)
- [SAP Note #2400005](https://launchpad.support.sap.com/#/notes/2400005)
- [SAP Note #2700123](https://launchpad.support.sap.com/#/notes/2700123)

자세히 살펴보면 이 기능을 활용하는 경우 볼륨 관리자 기반 스트라이프 세트의 복잡성이 제거된다는 것이 분명합니다. 또한 HANA 데이터 볼륨 분할이 Azure 프리미엄 스토리지와 같은 Azure 블록 스토리지에서만 작동하지 않는다는 것도 알 수 있습니다. IOPS 또는 처리량 제한이 NFS 공유에 있는 경우 이 기능을 사용하여 이러한 공유를 스트라이프할 수도 있습니다.  


## <a name="linux-io-scheduler-mode"></a>Linux I/O 스케줄러 모드
Linux에는 몇 가지 다른 I/O 일정 예약 모드가 있습니다. Linux 공급 업체 및 SAP를 통한 일반적인 권장 사항은 디스크 볼륨에 대한 I/O 스케줄러 모드를 **mq-deadline** 또는 **kyber** 모드에서 **noop**(다중 큐가 아님)로 다시 구성하거나, SLES saptune 프로필에서 아직 수행하지 않은 경우 (다중 큐) 모드에 대해 **none** 으로 다시 구성하는 것입니다. 자세한 내용은 다음에서 참조했습니다. 

- [SAP Note #1984787](https://launchpad.support.sap.com/#/notes/1984787)
- [SAP Note #2578899](https://launchpad.support.sap.com/#/notes/2578899) 
- [SLES 12 SP4의 noop 설정 문제](https://www.suse.com/support/kb/doc/?id=000019547)

Red Hat에서는 다른 SAP 애플리케이션에 대한 특정 튜닝 프로필에 설정된 설정을 그대로 유지합니다.


## <a name="solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines"></a>Azure M 시리즈 가상 머신에 프리미엄 스토리지 및 Azure 쓰기 가속기를 사용하는 솔루션
Azure 쓰기 가속기는 Azure M 시리즈 VM 전용 기능입니다. 이름에서 알 수 있듯이 이 기능은 Azure 프리미엄 스토리지에 대한 쓰기의 I/O 대기 시간을 향상시키기 위한 것입니다. SAP HANA의 경우 Write Accelerator는 **/hana/log** 볼륨에 대해서만 사용해야 합니다. 따라서 **/hana/data** 와 **/hana/log** 는 별개의 볼륨입니다(Azure 쓰기 가속기가 **/hana/log** 볼륨만 지원하기 때문). 

> [!IMPORTANT]
> Azure 프리미엄 스토리지를 사용하는 경우 Azure [쓰기 가속기](../../how-to-enable-write-accelerator.md)를 반드시 **/hana/log** 볼륨에 사용해야 합니다. 쓰기 가속기는 프리미엄 스토리지 및 M 시리즈/Mv2 시리즈 VM에만 사용할 수 있습니다. 쓰기 가속기는 Esv3 또는 Edsv4와 같은 다른 Azure VM 제품군과 함께 작동하지 않습니다.

아래의 Azure 프리미엄 디스크에 대한 캐싱 권장 사항은 다음과 같이 나열된 SAP HANA에 대한 I/O 특성을 가정하고 있습니다.

- HANA 데이터 파일에 대한 읽기 워크로드가 거의 없습니다. 예외는 HANA 인스턴스를 다시 시작한 후 또는 HANA에 데이터를 로드할 때 발생하는 대규모 I/O입니다. 데이터 파일에 대한 대규모 I/O의 또 다른 사례는 HANA 데이터베이스 백업일 수 있습니다. 대부분의 경우 모든 데이터 파일 볼륨을 완전히 읽어야 하므로 결과적으로 읽기 캐싱은 의미가 없습니다. 
- HANA 저장점 및 HANA 크래시 복구를 기반으로 데이터 파일에 대한 쓰기가 갑작스럽게 발생합니다. 저장점 쓰기는 비동기이며 사용자 트랜잭션을 유지하지 않습니다. 크래시 복구 중에 데이터 쓰기는 시스템이 다시 빠르게 응답하기 위해 성능이 중요합니다. 그러나 크래시 복구는 예외적인 상황이어야 합니다.
- HANA 다시 실행 파일에는 읽기가 거의 없습니다. 트랜잭션 로그 백업, 크래시 복구를 수행하는 경우 또는 HANA 인스턴스를 다시 시작하는 단계의 대규모 I/O는 예외입니다.  
- SAP HANA 다시 실행 로그 파일에 대한 기본 부하는 쓰기입니다. 워크로드의 특성에 따라 4KB 정도의 작은 I/O가 있거나, 다른 경우에 1MB 이상의 I/O 크기가 있을 수 있습니다. SAP HANA 다시 실행 로그에 대한 쓰기 대기 시간은 성능이 중요합니다.
- 모든 쓰기는 신뢰할 수 있는 방식으로 디스크에서 유지되어야 합니다.

**권장 사항: SAP HANA에서 관찰된 이러한 I/O 패턴의 결과로 Azure 프리미엄 스토리지를 사용하는 다양한 볼륨에 대한 캐싱은 다음과 같이 설정해야 합니다.**

- **/hana/data** - 캐싱 또는 읽기 캐싱 없음
- **/hana/log** - 캐싱 없음 - Azure 쓰기 가속기를 사용하도록 설정해야 하는 M 및 Mv2 시리즈 VM은 예외 
- **/hana/shared** - 읽기 캐싱
- **OS 디스크** - VM을 만들 때 Azure에서 설정한 기본 캐싱을 변경하지 않음


LVM 또는 mdadm을 사용하여 스트라이프 세트를 여러 Azure 프리미엄 디스크에 작성하는 경우 스트라이프 크기를 정의해야 합니다. 이러한 크기는 **/hana/data** 와 **/hana/log** 간에 차이가 있습니다. **권장 사항: 스트라이프 크기에 따라 다음을 사용하는 것이 좋습니다.**

- **/hana/data** 에 대해 256KB
- **/hana/log** 의 경우 64KB

> [!NOTE]
> **/hana/data** 에 대한 스트라이프 크기는 최신 Linux 버전을 사용하는 고객 환경에 따라 64KB 또는 128KB를 요구하는 이전 권장 사항에서 256KB로 변경되었습니다. 256KB의 크기는 약간 더 나은 성능을 제공합니다. 또한 더 큰 I/O 크기를 사용하여 충분한 처리량을 얻기 위해 **/hana/log** 의 스트라이프 크기에 대한 권장 사항이 32KB에서 64KB로 변경되었습니다.

> [!NOTE]
> Azure 블록 스토리지는 세 개의 VHD 이미지를 유지하므로 RAID 볼륨을 사용하여 중복 수준을 구성할 필요가 없습니다. 스트라이프 세트를 Azure 프리미엄 디스크에 사용하는 것은 전적으로 충분한 IOPS 및/또는 I/O 처리량을 제공하는 볼륨을 구성하는 것입니다.

여러 Azure VHD를 스트라이프 세트 아래에 누적하면 IOPS 및 스토리지 처리량 측면에서 누적되는 것입니다. 따라서 스트라이프 세트를 3 x P30 Azure 프리미엄 스토리지 디스크에 배치하면 단일 Azure 프리미엄 스토리지 P30 디스크의 3배의 IOPS 및 3배의 스토리지 처리량을 제공해야 합니다.

> [!IMPORTANT]
> LVM 또는 mdadm을 볼륨 관리자로 사용하여 스트라이프 세트를 여러 Azure 프리미엄 디스크에 만드는 경우 3개의 SAP HANA 파일 시스템, 즉 /data, /log 및 /shared를 기본 또는 루트 볼륨 그룹에 배치하면 안 됩니다. 일반적으로 /data, /log 및 /shared에 개별 볼륨 그룹을 만드는 Linux 공급 업체 지침을 따르는 것이 좋습니다.


### <a name="azure-burst-functionality-for-premium-storage"></a>Premium Storage용 Azure 버스트 기능
Azure Premium Storage 디스크 용량이 512GiB 이하인 경우 버스트 기능이 제공됩니다. 디스크 버스팅이 작동하는 정확한 방식은 [디스크 버스팅](../../disk-bursting.md) 문서에 설명되어 있습니다. 이 문서를 참조하면 I/O 워크로드가 디스크의 공칭 IOPS 및 처리량보다 낮을 때 IOPS와 처리량이 누적되는 개념을 이해할 수 있습니다(공칭 처리량에 대한 자세한 내용은 [Managed Disks 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) 참조). 현재 사용량과 디스크의 공칭 값 사이의 IOPS 및 처리량 델타가 누적됩니다. 버스트는 최대 30분으로 제한됩니다.

버스트 기능을 계획할 수 있는 이상적인 사례는 서로 다른 DBMS에 대한 데이터 파일을 포함하는 볼륨 또는 디스크일 것입니다. 특히 중소 규모 시스템에서 이러한 볼륨에 필요한 I/O 워크로드는 다음과 같습니다.

- 낮음~중간 수준의 읽기 워크로드(데이터가 메모리에 캐시되거나 HANA의 경우처럼 완전히 메모리에 있는 것이 가장 좋기 때문)
- 정기적으로 실행되는 데이터베이스 검사점 또는 저장점에 의해 유발되는 쓰기 버스트
- 스토리지 스냅샷을 통해 백업이 실행되지 않는 경우의 연속 스트림에서 읽는 백업 워크로드
- SAP HANA의 경우 인스턴스를 다시 시작한 후 메모리에 데이터를 로드

특히 워크로드에서 초당 몇백 개의 트랜잭션만을 처리하는 소규모 DBMS 시스템의 경우, 트랜잭션 또는 다시 실행 로그를 저장하는 디스크 또는 볼륨에 대해 버스트 기능을 사용할 수 있습니다. 이러한 디스크 또는 볼륨에 필요한 워크로드는 다음과 같습니다.

- 워크로드 및 워크로드의 특성에 종속되는 디스크에 대한 기본 쓰기(애플리케이션에서 발생하는 모든 커밋은 I/O 작업을 유발할 가능성이 있기 때문)
- 인덱스를 만들거나 다시 빌드하는 것과 같은 운영 작업 사례의 경우 처리량 워크로드 증가
- 트랜잭션 로그 또는 다시 실행 로그 백업을 수행하는 경우 읽기 버스트


### <a name="production-recommended-storage-solution-based-on-azure-premium-storage"></a>Azure 프리미엄 스토리지를 기반으로 하는 프로덕션 권장 스토리지 솔루션

> [!IMPORTANT]
> Azure M 시리즈 가상 머신에 대한 SAP HANA 인증은 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 독점적으로 사용하는 것입니다. 결과적으로, Azure M 시리즈 가상 머신에 프로덕션 시나리오 SAP HANA 배포는 **/hana/log** 볼륨에 대해 Azure Write Accelerator를 사용하여 구성되어야 합니다.  

> [!NOTE]
> Azure 프리미엄 스토리지와 관련된 시나리오에서는 버스트 기능을 구성에 구현합니다. 모양이나 형태에 관계없이 스토리지 테스트 도구를 사용하는 경우 [Azure 프리미엄 디스크 버스팅이 작동하는 방식](../../disk-bursting.md)을 고려합니다. SAP HWCCT 또는 HCMT 도구를 통해 제공되는 스토리지 테스트를 실행하면 일부 테스트에서 누적 가능한 버스팅 크레딧을 초과하므로 모든 테스트가 조건을 통과한다고 기대하지 않습니다. 특히 모든 테스트가 중단 없이 순차적으로 실행되는 경우입니다.

> [!NOTE]
> M32ts 및 M32ls VM을 사용하면 HCMT/HWCCT 디스크 테스트를 사용할 때 디스크 처리량이 예상보다 더 낮을 수 있습니다. 디스크 버스팅을 사용하거나 기본 디스크의 충분히 프로비저닝된 I/O 처리량이 있는 경우에도 마찬가지입니다. 확인된 동작의 근본 원인은 HCMT/HWCCT 스토리지 테스트 파일이 Premium Storage 데이터 디스크의 읽기 캐시에 완전히 캐시되었기 때문입니다. 이 캐시는 가상 머신을 호스트하는 컴퓨팅 호스트에 있으며 HCMT/HWCCT의 테스트 파일을 완전히 캐시할 수 있습니다. 이러한 경우 [M 시리즈](../../m-series.md) 문서의 **최대 캐시 및 임시 스토리지 처리량: IOPS/MBps(GiB 단위의 캐시 크기)** 열에 나열된 할당량이 관련이 있습니다. 특히 M32ts 및 M32ls의 경우 읽기 캐시에 대한 처리량 할당량은 400MB/초에 불과합니다. 테스트 파일이 완전히 캐시되면 디스크 버스팅 이상의 프로비저닝된 I/O 처리량에도 불구하고 테스트를 수행하면 400MB/초의 최대 처리량보다 약간 부족한 결과가 나타날 수 있습니다. 또는 Azure Premium Storage 데이터 디스크에서 읽기 캐시를 사용하도록 설정하지 않고 테스트할 수도 있습니다.


> [!NOTE]
> 프로덕션 시나리오의 경우 특정 VM 유형이 [IAAS에 대한 SAP 설명서](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)에서 SAP별 SAP HANA에 대해 지원되는지 여부를 확인합니다.

**권장 사항: 프로덕션 시나리오에 권장되는 Azure 프리미엄 스토리지 관련 구성은 다음과 같습니다.**

SAP **/hana/data** 볼륨 구성:

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | 프로비전된 처리량 | 최대 버스트 처리량 | IOPS | 버스트 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192GiB | 500MBps | 4 x P6 | 200MBps | 680MBps | 960 | 14,000 |
| M32ls | 256GiB | 500MBps | 4 x P6 | 200MBps | 680MBps | 960 | 14,000 |
| M64ls | 512GiB | 1,000MBps | 4 x P10 | 400MBps | 680MBps | 2,000 | 14,000 |
| M32dms_v2, M32ms_v2 | 875GiB  | 500MBps | 4 x P15 | 500MBps | 680MBps | 4,400 | 14,000 |
| M64s, M64ds_v2, M64s_v2 | 1,024GiB | 1,000MBps | 4 x P15 | 500MBps | 680MBps | 4,400 | 14,000 |
| M64ms, M64dms_v2, M64ms_v2 | 1,792GiB | 1,000MBps | 4 x P20 | 600MBps | 680MBps | 9,200 | 14,000 |  
| M128s, M128ds_v2, M128s_v2 | 2,048GiB | 2,000MBps | 4 x P20 | 600MBps | 680MBps | 9,200| 14,000 | 
| M192ids_v2, M192is_v2 | 2,048GiB | 2,000MBps | 4 x P20 | 600MBps | 680MBps | 9,200| 14,000 | 
| M128ms, M128dms_v2, M128ms_v2 | 3,892GiB | 2,000MBps | 4 x P30 | 800MBps | 버스팅 없음 | 20,000 | 버스팅 없음 | 
| M192ims, M192idms_v2 | 4,096GiB | 2,000MBps | 4 x P30 | 800MBps | 버스팅 없음 | 20,000 | 버스팅 없음 | 
| M208s_v2 | 2,850GiB | 1,000MBps | 4 x P30 | 800MBps | 버스팅 없음 | 20,000| 버스팅 없음 | 
| M208ms_v2 | 5,700GiB | 1,000MBps | 4 x P40 | 1,000MBps | 버스팅 없음 | 30,000 | 버스팅 없음 |
| M416s_v2 | 5,700GiB | 2,000MBps | 4 x P40 | 1,000MBps | 버스팅 없음 | 30,000 | 버스팅 없음 |
| M416ms_v2 | 11,400GiB | 2,000MBps | 4 x P50 | 1,000MBps | 버스팅 없음 | 30,000 | 버스팅 없음 |


**/hana/log** 볼륨의 경우 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | **/hana/log** 볼륨 | 프로비전된 처리량 | 최대 버스트 처리량 | IOPS | 버스트 IOPS |
| --- | --- | --- | --- | --- | --- | --- | 
| M32ts | 192GiB | 500MBps | 3 x P10 | 300MBps | 510MBps | 1,500 | 10,500 | 
| M32ls | 256GiB | 500MBps | 3 x P10 | 300MBps | 510MBps | 1,500 | 10,500 | 
| M64ls | 512GiB | 1,000MBps | 3 x P10 | 300MBps | 510MBps | 1,500 | 10,500 | 
| M32dms_v2, M32ms_v2 | 875GiB | 500MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 | 
| M64s, M64ds_v2, M64s_v2 | 1,024GiB | 1,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 | 
| M64ms, M64dms_v2, M64ms_v2 | 1,792GiB | 1,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 |  
| M128s, M128ds_v2, M128s_v2 | 2,048GiB | 2,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500| 
| M192ids_v2, M192is_v2 | 2,048GiB | 2,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500| 
| M128ms, M128dms_v2, M128ms_v2 | 3,892GiB | 2,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 |
| M192idms_v2, M192ims_v2 | 4,096GiB | 2,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 | 
| M208s_v2 | 2,850GiB | 1,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 |  
| M208ms_v2 | 5,700GiB | 1,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 |  
| M416s_v2 | 5,700GiB | 2,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 |  
| M416ms_v2 | 11,400GiB | 2,000MBps | 3 x P15 | 375MBps | 510MBps | 3,300 | 10,500 | 


다른 볼륨의 경우 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/shared | /root 볼륨 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| M32ts | 192GiB | 500MBps | 1 x P15 | 1 x P6 | 1 x P6 |
| M32ls | 256GiB | 500MBps |  1 x P15 | 1 x P6 | 1 x P6 |
| M64ls | 512GiB | 1,000MBps | 1 x P20 | 1 x P6 | 1 x P6 |
| M32dms_v2, M32ms_v2 | 875GiB | 500MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64s, M64ds_v2, M64s_v2 | 1,024GiB | 1,000MBps | 1 x P30 | 1 x P6 | 1 x P6 |
| M64ms, M64dms_v2, M64ms_v2 | 1,792GiB | 1,000MBps | 1 x P30 | 1 x P6 | 1 x P6 | 
| M128s, M128ds_v2, M128s_v2 | 2,048GiB | 2,000MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M192ids_v2, M192is_v2  | 2,048GiB | 2,000MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M128ms, M128dms_v2, M128ms_v2 | 3,892GiB | 2,000MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M192idms_v2, M192ims_v2  | 4,096GiB | 2,000MBps | 1 x P30 | 1 x P10 | 1 x P6 |
| M208s_v2 | 2,850GiB | 1,000MBps |  1 x P30 | 1 x P10 | 1 x P6 |
| M208ms_v2 | 5,700GiB | 1,000MBps | 1 x P30 | 1 x P10 | 1 x P6 | 
| M416s_v2 | 5,700GiB | 2,000MBps |  1 x P30 | 1 x P10 | 1 x P6 | 
| M416ms_v2 | 11,400GiB | 2,000MBps | 1 x P30 | 1 x P10 | 1 x P6 | 


제안된 다른 볼륨에 대한 스토리지 처리량이 실행하려는 워크로드를 충족하는지 여부를 확인합니다. **/hana/data** 및 **/hana/log** 에 대한 더 높은 볼륨이 워크로드에 필요한 경우 Azure 프리미엄 스토리지 VHD의 수를 늘려야 합니다. 나열된 것보다 더 많은 VHD로 볼륨을 크기 조정하면 Azure 가상 머신 유형의 한도 내 IOPS 및 I/O 처리량이 증가합니다.

Azure Write Accelerator는 [Azure 관리 디스크](https://azure.microsoft.com/services/managed-disks/)에만 작동합니다. 따라서 **/hana/log** 볼륨을 형성하는 Azure 프리미엄 스토리지 디스크는 관리 디스크로 배포해야 합니다. Azure 쓰기 가속기에 대한 자세한 지침 및 제한 사항은 [쓰기 가속기](../../how-to-enable-write-accelerator.md) 문서에서 확인할 수 있습니다.

Azure [Esv3](../../ev3-esv3-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#esv3-series) 제품군 및 [Edsv4](../../edv4-edsv4-series.md?toc=/azure/virtual-machines/linux/toc.json&bc=/azure/virtual-machines/linux/breadcrumb/toc.json#edsv4-series)의 HANA 인증 VM의 경우 **/hana/data** 및 **/hana/log** 볼륨에 대해 ANF가 필요합니다. 또는 **/hana/log** 볼륨에 대해서만 Azure 프리미엄 스토리지 대신 Azure Ultra Disk 스토리지를 활용해야 합니다. 결과적으로 Azure 프리미엄 스토리지의 **/hana/data** 볼륨 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data | 프로비전된 처리량 | 최대 버스트 처리량 | IOPS | 버스트 IOPS |
| --- | --- | --- | --- | --- | --- | --- |
| E20ds_v4 | 160GiB | 480MBps | 3 x P10 | 300MBps | 510MBps | 1,500 | 10,500 |
| E32ds_v4 | 256GiB | 768MBps | 3 x P10 |  300MBps | 510MBps | 1,500 | 10,500|
| E48ds_v4 | 384GiB | 1,152MBps | 3 x P15 |  375MBps |510MBps | 3,300  | 10,500 | 
| E64ds_v4 | 504GiB | 1,200MBps | 3 x P15 |  375MBps | 510MBps | 3,300 | 10,500 | 
| E64s_v3 | 432GiB | 1,200MB/s | 3 x P15 |  375MBps | 510MBps | 3,300 | 10,500 | 

Ultra Disk의 **/hana/log** 를 포함한 다른 볼륨의 경우 구성은 다음과 같습니다.

| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/log 볼륨 | /hana/log I/O 처리량 | /hana/log IOPS | /hana/shared | /root 볼륨 | /usr/sap |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160GiB | 480MBps | 80GB | 250MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E32ds_v4 | 256GiB | 768MBps | 128GB | 250MBps | 1,800 | 1 x P15 | 1 x P6 | 1 x P6 |
| E48ds_v4 | 384GiB | 1,152MBps | 192GB | 250MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64ds_v4 | 504GiB | 1,200MBps | 256GB | 250MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |
| E64s_v3 | 432GiB | 1,200MBps | 220GB | 250MBps | 1,800 | 1 x P20 | 1 x P6 | 1 x P6 |


## <a name="azure-ultra-disk-storage-configuration-for-sap-hana"></a>SAP HANA용 Azure Ultra Disk 스토리지 구성
또 다른 Azure 스토리지 유형으로 [Azure Ultra Disk](../../disks-types.md#ultra-disks)가 있습니다. 이제까지 제공된 Azure 스토리지와 Ultra Disk의 중요한 차이점은 디스크 기능이 더 이상 디스크 크기에 구속되지 않는다는 것입니다. 고객은 Ultra Disk에 대해 다음과 같은 기능을 정의할 수 있습니다.

- 4GiB에서 65,536GiB까지의 디스크 크기
- 100IOPS에서 160,000IOPS까지의 IOPS 범위(최대값은 VM 유형에 따라 다름)
- 300MB/초에서 2,000MB/초까지의 스토리지 처리량

Ultra Disk를 사용하면 원하는 크기, IOPS 및 디스크 처리량 범위를 충족하는 단일 디스크를 정의할 수 있습니다. Azure 프리미엄 스토리지 기반의 논리 볼륨 관리자(예: LVM 또는 MDADM)를 사용하여 IOPS 및 스토리지 처리량 요구 사항을 충족하는 볼륨을 구성하는 대신, Ultra Disk와 프리미엄 스토리지 간에 구성 조합을 실행할 수 있습니다. 결과적으로 Ultra Disk의 사용을 성능이 중요한 **/hana/data** 및 **/hana/log** 볼륨으로 제한하고, Azure 프리미엄 스토리지를 사용하여 다른 볼륨을 처리할 수 있습니다.

Ultra Disk의 다른 이점은 프리미엄 스토리지에 비해 더 향상된 읽기 대기 시간입니다. 더 빠른 읽기 대기 시간은 HANA 시작 시간과 이후 데이터를 메모리로 로드하는 시간을 단축하려는 경우에 이점이 될 수 있습니다. HANA가 저장점을 작성하는 경우에도 Ultra Disk 스토리지의 이점을 느낄 수 있습니다. 

> [!NOTE]
> Ultra Disk는 아직 모든 Azure 지역에 출시되지 않았고 아직 아래 나열된 모든 VM 유형을 지원하지 않습니다. Ultra Disk를 사용할 수 있는 지역 및 지원되는 VM 제품군에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](../../disks-types.md#ultra-disks) 문서를 참조하세요.

### <a name="production-recommended-storage-solution-with-pure-ultra-disk-configuration"></a>순수 Ultra Disk 구성을 사용하는 프로덕션 권장 스토리지 솔루션
이 구성에서는 **/hana/data** 및 **/hana/log** 볼륨을 별도로 유지합니다. 제안된 값은 SAP가 [SAP TDI 스토리지 백서](https://archive.sap.com/kmuuid2/70c8e423-c8aa-3210-3fae-e043f5c1ca92/SAP%20HANA%20TDI%20-%20Storage%20Requirements.pdf)에서 권장하는 대로 SAP HANA 및 스토리지 구성에 대한 VM 유형을 인증하는 KPI에서 도출된 것입니다.

권장 사항은 이 문서의 앞부분에서 설명한 대로 SAP 최소 요구 사항을 초과하는 경우가 많습니다. 나열된 권장 사항은 SAP에서 권장하는 크기와 다양한 VM 유형이 제공하는 최대 스토리지 처리량 간의 절충입니다.

> [!NOTE]
> Azure Ultra Disk는 디스크의 기가바이트 용량당 2IOPS 이상을 적용합니다.


| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 볼륨 | /hana/data I/O 처리량 | /hana/data IOPS | /hana/log 볼륨 | /hana/log I/O 처리량 | /hana/log IOPS |
| --- | --- | --- | --- | --- | --- | --- | --- | -- |
| E20ds_v4 | 160GiB | 480MB/s | 200GB | 400MBps | 2,500 | 80GB | 250MB | 1,800 |
| E32ds_v4 | 256GiB | 768MB/s | 300GB | 400MBps | 2,500 | 128GB | 250MBps | 1,800 |
| E48ds_v4 | 384GiB | 1,152MB/s | 460GB | 400MBps | 3,000 | 192GB | 250MBps | 1,800 |
| E64ds_v4 | 504GiB | 1200MB/s | 610GB | 400MBps | 3,500 |  256GB | 250MBps | 1,800 |
| E64s_v3 | 432GiB | 1,200MB/s | 610GB | 400MBps | 3,500 | 220GB | 250MB | 1,800 |
| M32ts | 192GiB | 500MB/s | 250GB | 400MBps | 2,500 | 96GB | 250MBps  | 1,800 |
| M32ls | 256GiB | 500MB/s | 300GB | 400MBps | 2,500 | 256GB | 250MBps  | 1,800 |
| M64ls | 512GiB | 1,000MB/s | 620GB | 400MBps | 3,500 | 256GB | 250MBps  | 1,800 |
| M32dms_v2, M32ms_v2 | 875GiB | 500MB/s |  1,200GB | 600MBps | 5,000 | 512 GB | 250MBps  | 2,500 |
| M64s, M64ds_v2, M64s_v2 | 1,024GiB | 1,000MB/s |  1,200GB | 600MBps | 5,000 | 512 GB | 250MBps  | 2,500 |
| M64ms, M64dms_v2, M64ms_v2 | 1,792GiB | 1,000MB/s | 2,100GB | 600MBps | 5,000 | 512 GB | 250MBps  | 2,500 |
| M128s, M128ds_v2, M128s_v2 | 2,048GiB | 2,000MB/s |2,400GB | 750MBps | 7,000 | 512 GB | 250MBps  | 2,500 |
| M192ids_v2, M192is_v2 | 2,048GiB | 2,000MB/s |2,400GB | 750MBps | 7,000 | 512 GB | 250MBps  | 2,500 | 
| M128ms, M128dms_v2, M128ms_v2 | 3,892GiB | 2,000MB/s | 4,800GB | 750MBps |9,600 | 512 GB | 250MBps  | 2,500 | 
| M192idms_v2, M192ims_v2 | 4,096GiB | 2,000MB/s | 4,800GB | 750MBps |9,600 | 512 GB | 250MBps  | 2,500 | 
| M208s_v2 | 2,850GiB | 1,000MB/s | 3,500GB | 750MBps | 7,000 | 512 GB | 250MBps  | 2,500 | 
| M208ms_v2 | 5,700GiB | 1,000MB/s | 7,200GB | 750MBps | 14,400 | 512 GB | 250MBps  | 2,500 | 
| M416s_v2 | 5,700GiB | 2,000MB/s | 7,200GB | 1,000MBps | 14,400 | 512 GB | 400MBps  | 4,000 | 
| M416ms_v2 | 11,400GiB | 2,000MB/s | 14,400GB | 1,500MBps | 28,800 | 512 GB | 400MBps  | 4,000 |   

**나열된 값은 시작점으로 사용되며 실제 수요를 기준으로 평가해야 합니다.** Azure Ultra Disk의 장점은 VM을 종료하거나 시스템에 적용되는 워크로드를 중지하지 않고도 IOPS 및 처리량 값을 조정할 수 있다는 것입니다.   

> [!NOTE]
> 이제까지 Ultra Disk 스토리지를 사용하는 스토리지 스냅샷은 사용할 수 없습니다. 따라서 Azure Backup 서비스에서 VM 스냅샷을 사용할 수 없습니다.


## <a name="nfs-v41-volumes-on-azure-netapp-files"></a>Azure NetApp Files 기반 NFS v 4.1 볼륨
HANA용 ANF에 대한 자세한 내용은 [SAP HANA용 Azure NetApp Files 기반 NFS v4.1 볼륨](./hana-vm-operations-netapp.md) 문서를 참조하세요.




## <a name="cost-conscious-solution-with-azure-premium-storage"></a>Azure 프리미엄 스토리지를 사용하는 비용에 민감한 솔루션
지금까지 이 문서의 [Azure M 시리즈 가상 머신에 프리미엄 스토리지 및 Azure 쓰기 가속기를 사용하는 솔루션](#solutions-with-premium-storage-and-azure-write-accelerator-for-azure-m-series-virtual-machines) 섹션에서 설명한 Azure 프리미엄 스토리지 솔루션은 SAP HANA 프로덕션 지원 시나리오를 위한 것이었습니다. 프로덕션 지원 구성의 특징 중 하나는 SAP HANA 데이터용 볼륨과 다시 실행 로그를 두 개의 다른 볼륨으로 분리하는 것입니다. 이렇게 분리하는 이유는 볼륨의 워크로드 특성이 다르기 때문입니다. 그리고 제안된 프로덕션 구성을 사용하면 다양한 유형의 캐싱 또는 다양한 유형의 Azure 블록 스토리지가 필요할 수 있습니다. 비 프로덕션 시나리오의 경우 프로덕션 시스템에 대해 고려하는 몇 가지 사항이 더 낮은 수준의 비 프로덕션 시스템에는 적용되지 않을 수 있습니다. 결과적으로 HANA 데이터와 로그 볼륨을 결합할 수 있습니다. 그러나 결국에는 몇 가지 원인으로 인해 운영 체제에 필요한 특정 처리량 또는 대기 시간 KPI를 결국 충족하지 못하는 것과 같은 상황이 발생합니다. 이러한 환경에서 비용을 절감하는 또 다른 측면은 [Azure 표준 SSD 스토리지의 사용](./planning-guide-storage.md#azure-standard-ssd-storage)입니다. 표준 SSD 또는 표준 HDD Azure 스토리지를 선택하면 [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) 문서에서 설명한 대로 단일 VM SLA에 영향을 줍니다.

이러한 구성에 대해 비용이 저렴한 대안은 다음과 같습니다.


| VM SKU | RAM | 최대 VM I/O<br /> 처리량 | /hana/data 및 /hana/log<br /> (LVM 또는 MDADM으로 스트라이프됨) | /hana/shared | /root 볼륨 | /usr/sap | comments |
| --- | --- | --- | --- | --- | --- | --- | -- |
| DS14v2 | 112GiB | 768MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| E16v3 | 128GiB | 384MB/s | 4 x P6 | 1 x E10 | 1 x E6 | 1 x E6 | HANA에서 인증되지 않은 VM 유형입니다. <br /> 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| M32ts | 192GiB | 500MB/s | 3 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 5,000으로 제한됩니다.<sup>2</sup> |
| E20ds_v4 | 160GiB | 480MB/s | 4 x P6 | 1 x E15 | 1 x E6 | 1 x E6 | 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| E32v3 | 256GiB | 768MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | HANA에서 인증되지 않은 VM 유형입니다. <br /> 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| E32ds_v4 | 256GiB | 768MBps | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| M32ls | 256GiB | 500MB/s | 4 x P10 | 1 x E15 | 1 x E6 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 5,000으로 제한됩니다.<sup>2</sup> |
| E48ds_v4 | 384GiB | 1,152MBps | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| E64v3 | 432GiB | 1,200MB/s | 6 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| E64ds_v4 | 504GiB | 1200MB/s |  7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 1ms 미만의 스토리지 대기 시간을 달성할 수 없습니다.<sup>1</sup> |
| M64ls | 512GiB | 1,000MB/s | 7 x P10 | 1 x E20 | 1 x E6 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 10,000으로 제한됩니다.<sup>2</sup> |
| M32dms_v2, M32ms_v2 | 875GiB | 500MB/s | 6 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 5,000으로 제한됩니다.<sup>2</sup> |
| M64s, M64ds_v2, M64s_v2 | 1,024GiB | 1,000MB/s | 7 x P15 | 1 x E30 | 1 x E6 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 10,000으로 제한됩니다.<sup>2</sup> |
| M64ms, M64dms_v2, M64ms_v2| 1,792GiB | 1,000MB/s | 6 x P20 | 1 x E30 | 1 x E6 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 10,000으로 제한됩니다.<sup>2</sup> |
| M128s, M128ds_v2, M128s_v2 | 2,048GiB | 2,000MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 20,000으로 제한됩니다.<sup>2</sup> |
| M192ids_v2, M192is_v2 | 2,048GiB | 2,000MB/s |6 x P20 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 20,000으로 제한됩니다.<sup>2</sup> |
| M128ms, M128dms_v2, M128ms_v2  | 3,800GiB | 2,000MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 20,000으로 제한됩니다.<sup>2</sup> |
| M192idms_v2, M192ims_v2  | 4,096GiB | 2,000MB/s | 5 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 20,000으로 제한됩니다.<sup>2</sup> |
| M208s_v2 | 2,850GiB | 1,000MB/s | 4 x P30 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 10,000으로 제한됩니다.<sup>2</sup> |
| M208ms_v2 | 5,700GiB | 1,000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 10,000으로 제한됩니다.<sup>2</sup> |
| M416s_v2 | 5,700GiB | 2,000MB/s | 4 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 20,000으로 제한됩니다.<sup>2</sup> |
| M416ms_v2 | 11400GiB | 2,000MB/s | 7 x P40 | 1 x E30 | 1 x E10 | 1 x E6 | 쓰기 가속기를 결합된 데이터 및 로그 볼륨에 사용하면 IOPS 속도가 20,000으로 제한됩니다.<sup>2</sup> |


<sup>1</sup> [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md)는 Ev4 및 Ev4 VM 제품군에서 사용할 수 없습니다. Azure 프리미엄 스토리지를 사용하면 I/O 대기 시간이 1ms 미만이 됩니다.

<sup>2</sup> VM 제품군은 [Azure 쓰기 가속기](../../how-to-enable-write-accelerator.md)를 지원하지만, 쓰기 가속기의 IOPS 제한이 디스크 구성 IOPS 기능을 제한할 수도 있습니다.

SAP HANA에 대한 데이터 및 로그 볼륨을 결합하는 경우 스트라이프 볼륨을 작성하는 디스크에 읽기 캐시 또는 읽기/쓰기 캐시를 사용하도록 설정하지 않아야 합니다.

SAP에서 인증되지 않은, 예를 들어 [SAP HANA 하드웨어 디렉터리](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)에 나열되지 않은 VM 유형이 나열되어 있습니다. 고객의 피드백은 나열되지 않은 이러한 VM 유형이 일부 비 프로덕션 작업에 성공적으로 사용되었다는 것입니다.


## <a name="next-steps"></a>다음 단계
자세한 내용은 다음을 참조하세요.

- [Azure Virtual Machines의 SAP HANA 고가용성 가이드](./sap-hana-availability-overview.md)
