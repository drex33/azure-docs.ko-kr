---
title: SAP 워크로드에 대한 Azure Storage 유형
description: SAP 워크로드에 대한 Azure Storage 유형 계획
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: d7c59cc1-b2d0-4d90-9126-628f9c7a5538
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/17/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0af6df954dda4e5af6335776b1f93f929da5834e
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530670"
---
# <a name="azure-storage-types-for-sap-workload"></a>SAP 워크로드에 대한 Azure Storage 형식
Azure는 용량, 처리량, 대기 시간, 가격이 다양한 수많은 스토리지 유형을 제공합니다. 일부 스토리지 형식은 또는 SAP 시나리오에 사용할 수 없거나 제한적으로 사용할 수 있습니다. 반면, 몇 가지 Azure Storage 유형은 특정 SAP 워크로드 시나리오에 적합하거나 최적화되어 있습니다. 특히 SAP HANA의 경우 일부 Azure Storage 유형은 SAP HANA 사용에 대해 인증을 받았습니다. 이 문서에서는 다양한 유형의 스토리지를 살펴보고 SAP 워크로드 및 SAP 구성 요소를 통해 기능 및 유용성에 대해 설명합니다.

이 문서 전체에 사용된 단위에 대한 설명입니다. 퍼블릭 클라우드 공급업체는 기가바이트 또는 테라바이트 대신 GiB([기비바이트](https://en.wikipedia.org/wiki/Gibibyte)) 또는 TiB([테비바이트](https://en.wikipedia.org/wiki/Tebibyte))를 크기 단위로 사용합니다. 따라서 모든 Azure 설명서 및 가격 책정에는 이러한 단위를 사용합니다.  문서 전체에서 MiB, GiB, TiB 단위의 크기만 참조합니다. MB, GB, TB 단위를 사용하여 계획해야 할 수도 있습니다. 따라서 250MiB/초 처리량 대신 400MiB/초의 처리량 크기를 조정해야 하는 경우 계산에 약간의 차이가 있을 수 있음을 염두에 두어야 합니다.

## <a name="microsoft-azure-storage-resiliency"></a>Microsoft Azure Storage 복원력

표준 HDD, 표준 SSD, Azure 프리미엄 스토리지, Ultra Disk의 Microsoft Azure 스토리지는 기본 VHD(OS 포함) 및 VM이 연결된 데이터 디스크 또는 VHD를 세 개의 서로 다른 스토리지 노드에 세 개의 복사본으로 저장합니다. 명백한 스토리지 노드 오류가 발생하는 경우 다른 복제본으로 장애 조치(failover)하고 새 복제본을 시드합니다. 이러한 중복성으로 인해 다수의 Azure 디스크 전반에 걸쳐 아무런 스토리지 중복 레이어도 사용할 필요가 **없습니다**. 이 사실을 LRS(로컬 중복 스토리지)라고 합니다. LRS는 Azure에서 이러한 유형의 스토리지에 대한 기본값입니다. [Azure NetApp Files](https://azure.microsoft.com/services/netapp/)는 다른 원시 Azure 스토리지와 동일한 Service Level Agreement(서비스 수준 약정)를 얻을 수 있는 충분한 중복도를 제공합니다.

중복 메서드에는 몇 가지가 더 있으며, 이 내용은 Azure가 제공하는 다양한 스토리지 유형 중 일부에 적용되는 [Azure Storage 복제](../../../storage/common/storage-redundancy.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) 문서에 설명되어 있습니다. 

또한 다양한 Azure Storage 유형이 [Virtual Machines에 대한 SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines)에서 릴리스된 단일 VM 가용성 SLA에 영향을 줍니다.


### <a name="azure-managed-disks"></a>Azure Managed Disks

Managed Disks는 Azure Storage 계정에 저장된 VHD 대신 사용할 수 있는 Azure Resource Manager 리소스 종류입니다. Managed Disks는 연결되어 있는 가상 머신의 [가용성 집합][virtual-machines-manage-availability]에 맞게 자동으로 조정되므로, 가상 머신에서 실행되는 서비스와 가상 머신의 가용성을 높여 줍니다. 자세한 내용은 [개요 문서](../../managed-disks-overview.md)를 참조하세요.

이 예제에서는 복원력과 관련하여 관리 디스크가 가지는 이점을 보여줍니다.

- Azure 가용성 집합에서 SAP 시스템을 위한 2개의 DBMS VM을 배포하려고 합니다. 
- Azure가 VM을 배포하면 OS 이미지를 포함하는 디스크가 다른 스토리지 클러스터에 배치됩니다. 이렇게 하면 단일 Azure 스토리지 클러스터에서 발생하는 오류가 두 VM 모두에 영향을 미치는 것을 방지할 수 있습니다.
- 데이터베이스의 데이터 및 로그 파일을 저장하기 위해 해당 VM에 할당하는 새 관리 디스크를 만들면, 두 VM을 위한 해당 새 디스크가 별도의 스토리지 클러스터에도 배포되므로 첫 번째 VM의 디스크는 두 번째 VM의 디스크와 스토리지 클러스터를 공유하지 않게 됩니다.

관리 디스크를 사용하지 않고 고객 정의 스토리지 계정에 배포하는 경우 디스크 할당은 임의로 이루어지며, 복원을 목적으로 VM이 AvSet 내에 배포된다는 사실을 인식하지 못합니다.

> [!NOTE]
> 이러한 이유 및 관리 디스크를 통해 독점적으로 사용할 수 있는 몇 가지 다른 개선 사항 때문에 디스크에 Azure 블록 스토리지를 사용하는 VM(Azure NetApp Files를 제외한 모든 Azure 스토리지)의 새 배포에는 베이스 VHD/OS 디스크(SAP 데이터베이스 파일이 포함된 데이터 디스크)에 Azure Managed Disks를 사용해야 합니다. 가용성 집합을 통해 VM을 배포하는지, 가용성 영역 전체에 배포하는지 또는 집합과 영역에 관계없이 배포하는지 여부와 무관합니다. 백업 저장용으로 사용되는 디스크가 반드시 관리 디스크일 필요는 없습니다.

> [!NOTE]
> Azure Managed Disks는 로컬 중복 스토리지(LRS)만을 제공합니다. 


## <a name="storage-scenarios-with-sap-workloads"></a>SAP 워크로드를 사용하는 스토리지 시나리오
영구 스토리지는 Azure에 배포하는 스택의 다양한 구성 요소에 있는 SAP 워크로드에 필요합니다. 최소한의 시나리오 목록은 다음과 같습니다.

- 디스크에 설치한 운영 체제 및 기타 소프트웨어가 포함된 VM의 베이스 VHD를 유지합니다. 이 디스크/VHD는 VM의 루트입니다. 변경 내용은 유지되어야 합니다. 따라서 다음에 VM을 중지했다가 다시 시작해도 이전에 변경한 모든 내용이 그대로 유지되어야 합니다. 특히 VM이 Azure에 의해 원래 실행되던 호스트가 아닌 다른 호스트에 배포되는 경우에 그렇습니다.
- 영구 데이터 디스크. 애플리케이션 데이터를 저장하기 위해 연결하는 VHD입니다. 해당 애플리케이션 데이터에는 데이터베이스, 백업 파일 또는 소프트웨어 설치의 데이터 및 로그/다시 실행 파일이 포함됩니다. 운영 체제가 저장된 베이스 VHD 이외 모든 디스크를 의미합니다.
- NetWeaver 또는 S/4HANA에 대한 글로벌 전송 디렉터리를 포함하는 파일 공유 또는 공유 디스크. 이러한 공유의 콘텐츠는 여러 VM에서 실행되는 소프트웨어에 사용되거나 고가용성 장애 조치(failover) 클러스터 시나리오를 빌드하는 데 사용됩니다.
- EDI 프로세스 또는 이와 유사한 프로세스를 위한 /sapmnt 디렉터리 또는 공통 파일 공유. 이러한 공유의 콘텐츠는 여러 VM에서 실행되는 소프트웨어에 사용되거나 고가용성 장애 조치(failover) 클러스터 시나리오를 빌드하는 데 사용됩니다.

다음 섹션에서는 위의 네 가지 시나리오에 적용되는 다양한 Azure Storage 유형 및 SAP 워크로드에 대한 유용성에 대해 알아봅니다. 다양한 Azure Storage 유형을 사용하는 방법에 대한 일반적인 분류는 [Azure에서 사용할 수 있는 디스크 유형](../../disks-types.md) 문서에 설명되어 있습니다. SAP 워크로드에 대해 서로 다른 Azure Storage 유형을 사용하기 위한 권장 사항은 많이 다르지 않습니다.

S/4HANA의 SAP NetWeaver/애플리케이션 계층용 Azure Storage 유형에 대한 지원 제한 사항은 [SAP support note 2015553](https://launchpad.support.sap.com/#/notes/2015553)을 참조하세요. SAP HANA 인증 및 지원되는 Azure Storage 유형은 [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서를 참조하세요.

다양한 Azure Storage 유형을 설명하는 섹션에는 SAP 지원 스토리지를 사용하는 가능성 및 제한 사항에 대해 자세한 배경 정보가 제공됩니다. 

### <a name="storage-choices-when-using-dbms-replication"></a>DBMS 복제를 사용하는 경우의 스토리지 선택
참조 아키텍처에서는 SQL Server Always On, HANA System Replication, Db2 HADR 또는 Oracle Data Guard와 같은 DBMS 기능을 사용한다고 예상합니다. 둘 또는 여러 Azure 가상 머신 간에 이 기술을 사용하는 경우 각 VM에 대해 선택한 스토리지 유형이 동일해야 합니다. 즉, DBMS 시스템의 다시 실행 로그 볼륨에 대한 스토리지 선택이 한 VM에서 Azure Premium Storage라면, 해당 볼륨은 같은 고가용성 동기화 구성에 있는 다른 모든 VM에서 Azure Premium Storage를 기반으로 해야 합니다. 데이터베이스 파일에 사용되는 데이터 볼륨의 경우에도 마찬가지입니다.
  

## <a name="storage-recommendations-for-sap-storage-scenarios"></a>SAP 스토리지 시나리오에 대한 스토리지 권장 사항
세부 정보를 살펴보기 전에 이미 문서의 시작 부분에 요약 및 권장 사항이 제공되어 있습니다. 특정 유형의 Azure Storage에 대한 세부 정보는 문서의 이 섹션 다음에 나와 있습니다. SAP 스토리지 시나리오에 대한 스토리지 권장 사항을 표에 요약하면 다음과 같습니다.

| 사용 시나리오 | 표준 HDD | 표준 SSD | Premium Storage | Ultra disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| OS 디스크 | 부적합 |  제한적 적합(비프로덕션) | 권장 | 불가능 | 불가능 |
| 전역 전송 디렉터리 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장 | 권장 |
| /sapmnt | 부적합 | 제한적 적합(비프로덕션) | 권장 | 권장 | 권장 |
| DBMS 데이터 볼륨 SAP HANA M/Mv2 VM 제품군 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장 | 권장<sup>2</sup> |
| DBMS 로그 볼륨 SAP HANA M/Mv2 VM 제품군 | 지원되지 않음 | 지원되지 않음 | 권장<sup>1</sup> | 권장 | 권장<sup>2</sup> | 
| DBMS 데이터 볼륨 SAP HANA Esv3/Edsv4 VM 제품군 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장 | 권장<sup>2</sup> |
| DBMS 로그 볼륨 SAP HANA Esv3/Edsv4 VM 제품군 | 지원되지 않음 | 지원되지 않음 | 지원되지 않음 | 권장 | 권장<sup>2</sup> | 
| DBMS 데이터 볼륨 비 HANA | 지원되지 않음 | 제한적 적합(비프로덕션) | 권장 | 권장 | Oracle Linux의 특정 Oracle 릴리스 및 Linux의 Db2에만 해당 |
| DBMS 로그 볼륨 비 HANA M/Mv2 VM 제품군 | 지원되지 않음 | 제한적 적합(비프로덕션) | 권장<sup>1</sup> | 권장 | Oracle Linux의 특정 Oracle 릴리스 및 Linux의 Db2에만 해당 |
| DBMS 로그 볼륨 비 HANA 비 M/Mv2 VM 제품군 | 지원되지 않음 | 제한적 적합(비프로덕션) | 중간 규모 워크로드까지 적합 | 권장 | Oracle Linux의 특정 Oracle 릴리스 및 Linux의 Db2에만 해당 |


<sup>1</sup> 로그/다시 실행 로그 볼륨에 대해 M/Mv2 VM 제품군에 [Azure Write Accelerator](../../how-to-enable-write-accelerator.md) 사용 <sup>2</sup> ANF를 사용하려면 /hana/data는 물론 /hana/log가 ANF에 있어야 함 

다양한 스토리지 유형에서 기대할 수 있는 특징은 다음과 같습니다.

| 사용 시나리오 | 표준 HDD | 표준 SSD | Premium Storage | Ultra disk | Azure NetApp Files |
| --- | --- | --- | --- | --- | --- |
| 처리량/ IOPS SLA | 아니요 | 아니요 | 예 | 예 | 예 |
| 대기 시간 읽기 | high | 중간~높음 | low | 밀리초 미만 | 밀리초 미만 |
| 대기 시간 쓰기 | high | 중간~높음  | 낮음(밀리초 미만<sup>1</sup>) | 밀리초 미만 | 밀리초 미만 |
| HANA 지원 | 아니요 | 아니요 | 예<sup>1</sup> | 예 | 예 |
| 디스크 스냅샷 가능 | 예 | 예 | 예 | 아니요 | 예 |
| 가용성 집합을 사용하는 경우 서로 다른 스토리지 클러스터에 디스크 할당 | 관리 디스크를 통해 | 관리 디스크를 통해 | 관리 디스크를 통해 | 가용성 집합을 통해 배포된 VM에서 지원되지 않는 디스크 유형 | 아니요<sup>3</sup> |
| 가용성 영역에 맞춰 조정 | 예 | 예 | 예 | 예 | Microsoft 참여 필요 |
| 영역 중복 | 관리 디스크용 아님 | 관리 디스크용 아님 | 관리 디스크용 아님 | 아니요 | 아니요 |
| 지리적 중복 | 관리 디스크용 아님 | 관리 디스크용 아님 | 아니요 | 아니요 | 아니요 |


<sup>1</sup> 로그/다시 실행 로그 볼륨에 대해 M/Mv2 VM 제품군에 [Azure Write Accelerator](../../how-to-enable-write-accelerator.md) 사용

<sup>2</sup> 프로비저닝된 IOPS 및 처리량에 따라 비용이 다름

<sup>3</sup> 서로 다른 ANF 용량 풀을 생성해도 서로 다른 스토리지 단위에 용량 풀이 배포된다는 보장은 없음


> [!IMPORTANT]
> ANF(Azure NetApp Files)를 사용하여 1밀리초 미만의 I/O 대기 시간을 달성하려면 Microsoft와 협력하여 ANF를 기반으로 VM과 NFS 공유 간에 올바른 배치를 조정해야 합니다. 아직까지 배포된 VM과 ANF에서 호스트되는 NFS 볼륨 사이에 자동 근접성을 제공하는 메커니즘은 없습니다. 서로 다른 Azure 지역의 설정이 다른 것을 감안할 때 VM과 NFS 공유가 근접하게 할당되지 않으면 추가된 네트워크 대기 시간으로 인해 I/O 대기 시간이 1밀리초를 초과하여 늘어날 수 있습니다.


> [!IMPORTANT]
> 현재 제공되는 Azure 블록 스토리지 기반 관리 디스크 또는 Azure NetApp Files 중 어느 것도 영역 또는 지리적 중복을 제공하지 않습니다. 결과적으로, 고가용성 및 재해 복구 아키텍처가 이러한 관리 디스크, NFS 또는 SMB 공유에 대해 어떤 유형의 Azure 네이티브 스토리지 복제에도 의존하지 않는지 확인해야 합니다.


## <a name="azure-premium-storage"></a>Azure Premium Storage
Azure 프리미엄 SSD 스토리지는 다음을 제공하는 것을 목표로 도입되었습니다.

* 낮은 I/O 대기 시간
* IOPS 및 처리량에 대한 Service Level Agreement(서비스 수준 약정)
* 변동성이 적은 I/O 대기 시간

이런 유형의 스토리지는 DBMS 워크로드, 짧은 한 자릿수 밀리초 대기 시간이 필요한 스토리지 트래픽, IOPS 및 처리량에 대한 SLA를 대상으로 합니다. Azure Premium Storage의 경우 비용 기반은 이러한 디스크에 저장된 실제 데이터 볼륨이 아니라 해당 디스크의 크기 범주이며, 디스크 내에 저장된 데이터의 양과 무관합니다. [프리미엄 SSD](../../disks-types.md#premium-ssd) 문서에 표시된 크기 범주에 직접 매핑되지 않는 Premium Storage에 디스크를 만들 수도 있습니다. 이 문서의 결론은 다음과 같습니다.

- 스토리지는 범위로 구성됩니다. 예를 들어 513GiB ~ 1024GiB 용량 범위의 디스크는 동일한 기능과 동일한 월별 비용을 공유합니다.
- GiB당 IOPS는 크기 범주 전체에서 선형으로 추적되지 않습니다. 32GiB 미만의 작은 디스크는 GiB당 IOPS 속도가 더 높습니다. 32GiB ~ 1024GiB를 초과하는 디스크의 경우 GiB당 IOPS 속도는 GiB당 4-5 IOPS입니다. 최대 32,767GiB의 큰 디스크는 GiB당 IOPS 속도가 1 미만입니다.
- 이 스토리지의 I/O 처리량은 디스크 범주의 크기와 선형 관계가 아닙니다. 더 작은 디스크(65GiB에서 128GiB 사이의 용량 범주)의 경우 처리량은 약 780KB/GiB입니다. 초대형 디스크(예: 32,767GiB 디스크)의 경우 처리량은 약 28KB/GiB입니다.
- IOPS 및 처리량 SLA는 디스크 용량을 변경하지 않고는 변경할 수 없습니다.


SAP 워크로드에 대한 기능 매트릭스는 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 베이스 VHD | 적합 | 모든 시스템 |
| 데이터 디스크 | 적합 | 모든 시스템 - [특히 SAP HANA의 경우](../../how-to-enable-write-accelerator.md) |
| SAP 전역 전송 디렉터리 | YES | [지원됨](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 적합 | 모든 시스템 |
| 백업 스토리지 | 적합 | 백업 단기 스토리지용 |
| 공유/공유 디스크 | 사용할 수 없음 | Azure Premium Files 또는 타사 필요 |
| 복원력 | LRS | GRS 또는 ZRS를 디스크에 사용할 수 없음 |
| 대기 시간 | 낮음~보통 | - |
| IOPS SLA | YES | - |
| 용량에 비례하는 IOPS | 괄호 안 반선형  | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 디스크당 최대 IOPS | [디스크 크기에 따라](https://azure.microsoft.com/pricing/details/managed-disks/) 20,000 | [VM 한도](../../sizes.md)도 고려 |
| 처리량 SLA | YES | - |
| 용량에 비례하는 처리량 | 괄호 안 반선형 | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 인증됨 | YES | [특히 SAP HANA의 경우](../../how-to-enable-write-accelerator.md) |
| 디스크 스냅샷 가능 | YES | - |
| Azure Backup VM 스냅샷 가능 | YES | [Write Accelerator](../../how-to-enable-write-accelerator.md) 캐시된 디스크 제외  |
| 비용 | MEDIUM | - |

Azure Premium Storage는 Azure Premium Storage와 함께 제공되는 일반적인 캐싱 유형으로 SAP HANA 스토리지 대기 시간 KPI를 충족하지 않습니다. SAP HANA 로그 쓰기에 대한 스토리지 대기 시간 KPI를 충족하려면 [Write Accelerator 사용](../../how-to-enable-write-accelerator.md) 문서에 설명된 대로 Azure Write Accelerator 캐싱을 사용해야 합니다. Azure Write Accelerator는 트랜잭션 로그 쓰기 및 다시 실행 로그 쓰기에 대해 다른 모든 DBMS 시스템에 이점을 제공합니다. 따라서 모든 SAP DBMS 배포에서 사용하는 것이 좋습니다. SAP HANA의 경우 Azure Premium Storage와 함께 Azure Write Accelerator를 사용하는 것은 필수입니다.



**요약:** Azure Premium Storage는 SAP 워크로드에 권장되는 Azure Storage 유형 중 하나입니다. 이 권장 사항은 프로덕션 시스템뿐만 아니라 비프로덕션 시스템에도 적용됩니다. Azure Premium Storage는 데이터베이스 워크로드를 처리하는 데 적합합니다. Azure 쓰기 가속기를 사용하여 Azure Premium 디스크에 대한 쓰기 대기 시간을 크게 개선할 수 있습니다. 그러나 IOPS가 높고 처리량 속도가 빠른 DBMS 시스템의 경우 스토리지 용량을 과도하게 프로비저닝해야 하거나, Windows 스토리지 공간 또는 Linux의 논리 볼륨 관리자와 같은 기능을 사용하여 원하는 용량을 제공하는 동시에 필요로 하는 IOPS 또는 처리량이 가장 비용 효율적인 Stripe 세트를 빌드해야 합니다.


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


## <a name="azure-ultra-disk"></a>Azure Ultra Disk
Azure ultra disks는Azure IaaS VM에 대해 높은 처리량, 높은 IOPS 및 일관성 있는 짧은 대기 시간 디스크 스토리지를 제공합니다. Ultra Disk의 몇 가지 추가 이점에는 VM(가상 머신)을 다시 시작하지 않고도 워크로드와 함께 디스크의 IOPS 및 처리량을 동적으로 변경할 수 있다는 것이 포함됩니다. Ultra Disk는 SAP DBMS 워크로드와 같은 데이터를 많이 사용하는 워크로드에 적합합니다. Ultra Disk는 데이터 디스크로만 사용할 수 있으며 운영 체제를 저장하는 베이스 VHD 디스크로 사용할 수 없습니다. Azure Premium Storage를 베이스 VHD 디스크로 사용하는 것이 좋습니다.

Ultra Disk를 만들 때 3가지 차원을 정의할 수 있습니다.

- 디스크의 용량. 범위는 4GiB ~ 65,536GiB입니다.
- 디스크에 대해 프로비저닝된 IOPS. 다른 최댓값이 디스크 용량에 적용됩니다. 자세한 내용은 [Ultra Disk](../../disks-types.md#ultra-disk) 문서를 참조하세요.
- 프로비저닝된 스토리지 대역폭. 디스크 용량에 따라 최대 대역폭이 다르게 적용됩니다. 자세한 내용은 [Ultra Disk](../../disks-types.md#ultra-disk) 문서를 참조하세요.

단일 디스크의 비용은 특정 디스크에 대해 개별적으로 정의할 수 있는 세 가지 차원에 따라 결정됩니다. 


SAP 워크로드에 대한 기능 매트릭스는 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 베이스 VHD | 작동하지 않음 | - |
| 데이터 디스크 | 적합 | 모든 시스템  |
| SAP 전역 전송 디렉터리 | YES | [지원됨](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 적합 | 모든 시스템 |
| 백업 스토리지 | 적합 | 백업 단기 스토리지용 |
| 공유/공유 디스크 | 사용할 수 없음 | 타사 필요 |
| 복원력 | LRS | GRS 또는 ZRS를 디스크에 사용할 수 없음 |
| 대기 시간 | 매우 낮음 | - |
| IOPS SLA | YES | - |
| 용량에 비례하는 IOPS | 괄호 안 반선형  | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| 디스크당 최대 IOPS | 1,200 ~ 160,000 | 디스크 용량에 따라 다름 |
| 처리량 SLA | YES | - |
| 용량에 비례하는 처리량 | 괄호 안 반선형 | [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/) |
| HANA 인증됨 | YES | - |
| 디스크 스냅샷 가능 | 아니요 | - |
| Azure Backup VM 스냅샷 가능 | 아니요 | - |
| 비용 | Premium Storage보다 높음 | - |



**요약:** Azure Ultra Disk는 모든 종류의 SAP 워크로드용으로 적합한, 대기 시간이 짧은 스토리지입니다. 지금까지 Ultra Disk는 가용성 영역(영역 배포)을 통해 배포된 VM과 조합하는 방법으로만 사용할 수 있었습니다. 현재 Ultra Disk는 스토리지 스냅샷을 지원하지 않습니다. 다른 모든 스토리지와 달리, Ultra Disk는 베이스 VHD 디스크로 사용할 수 없습니다. Ultra Disk는 I/O 워크로드의 변동이 크고, 배포된 스토리지 처리량 또는 IOPS의 크기를 대역폭 및 IOPS의 최대 사용량에 맞게 조정하는 대신 스토리지 워크로드 패턴에 맞게 조정하려는 경우에 적합합니다.


## <a name="azure-netapp-files-anf"></a>ANF(Azure NetApp Files)
[Azure NetApp Files](https://azure.microsoft.com/services/netapp/)는 고성능 Azure 네이티브 NFS 및 SMB 공유를 제공하는 것을 목표로 한 Microsoft와 NetApp 간 협력의 결과입니다. 강조점은 DBMS 배포 시나리오를 가능하게 하는 고대역폭 및 대기 시간이 짧은 스토리지를 제공하고 시간이 지남에 따라 Azure를 통해 NetApp 스토리지의 일반적인 운영 기능을 사용하도록 설정하는 것입니다. NFS/SMB 공유는 스토리지 처리량과 가격 면에서 차별화되는 세 가지 다른 서비스 수준으로 제공됩니다. 서비스 수준은 [Azure NetApp Files에 대한 서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md) 문서에 설명되어 있습니다. 다양한 유형의 SAP 워크로드에 대해 다음과 같은 서비스 수준을 적극 권장합니다.

- SAP DBMS 워크로드:    성능, 이상적으로는 Ultra
- SAPMNT 공유:         성능, 이상적으로는 Ultra
- 전역 전송 디렉터리: 성능, 이상적으로는 Ultra

> [!NOTE]
> 최소 프로비저닝 크기는 용량 풀이라고 하는 4TiB 단위입니다. 그런 다음, 이 용량 풀에서 볼륨을 만듭니다. 반면, 빌드할 수 있는 가장 작은 볼륨은 100GiB입니다. 용량 풀은 TiB 단계로 확장할 수 있습니다. 가격 책정에 대한 자세한 내용은 [Azure NetApp Files 가격 책정](https://azure.microsoft.com/pricing/details/netapp/) 문서를 참조하세요.

ANF 스토리지는 현재 다음과 같은 몇 가지 SAP 워크로드 시나리오에서 지원됩니다.

- SAP의 글로벌 전송 디렉터리에 SMB 또는 NFS 공유 제공
- 고가용성 시나리오에서 sapmnt 공유(아래 문서 참조)
    - [SAP 애플리케이션용 Azure NetApp Files(SMB)를 사용하는 Windows의 Azure VM에서 SAP NetWeaver에 대한 고가용성](./high-availability-guide-windows-netapp-files-smb.md)
    - [SAP 애플리케이션용 Azure NetApp Files를 사용하여 SUSE Linux Enterprise Server에서 Azure VM의 SAP NetWeaver 고가용성 실현](./high-availability-guide-suse-netapp-files.md)
    - [SAP 애플리케이션용 Azure NetApp Files를 사용하는 Red Hat Enterprise Linux의 SAP NetWeaver에 대한 Azure Virtual Machines 고가용성](./high-availability-guide-rhel-netapp-files.md)
- /hana/shared 볼륨에 NFS v4.1 또는 NFS v3 및/또는 /hana/data 및 /hana/log 볼륨에 NFS v4.1 공유를 사용하는 SAP HANA 배포(자세한 내용은 [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md) 문서 참조)
- Suse 또는 Red Hat Linux 게스트 OS의 IBM Db2
- Oracle 데이터 및 다시 실행 로그 볼륨에 대해 [dNFS](https://docs.oracle.com/en/database/oracle/oracle-database/19/ntdbi/creating-an-oracle-database-on-direct-nfs.html#GUID-2A0CCBAB-9335-45A8-B8E3-7E8C4B889DEA)를 사용하는 Oracle Linux 게스트 OS에서의 Oracle 배포 자세한 내용은 [SAP 워크로드에 대한 Azure Virtual Machines ORACLE DBMS 배포](./dbms_guide_oracle.md) 문서에서 확인할 수 있습니다.

> [!NOTE]
> Azure NetApp Files 기반 NFS 또는 SMB 공유에는 다른 DBMS 워크로드가 지원되지 않습니다. 이것이 변경될 경우 업데이트 및 변경 내용이 제공됩니다.

Azure Premium Storage와 마찬가지로 처리량의 최소 수치를 준수해야 하는 경우 GB당 고정 또는 선형 처리량 크기는 문제가 될 수 있습니다. 이것은 SAP HANA의 경우와 같습니다. ANF를 사용하면 이 문제가 Azure 프리미엄 디스크를 사용하는 것보다 더 두드러질 수 있습니다. Azure 프리미엄 디스크의 경우 GiB당 처리량이 상대적으로 높은 여러 개의 작은 디스크를 사용하고 여러 디스크에 걸쳐 스트라이프하면 낮은 용량으로 처리량 및 비용 효율 높일 수 있습니다. 이러한 종류의 스트라이프는 ANF에 호스트되는 NFS 또는 SMB 공유에 대해 작동하지 않습니다. 이러한 제한으로 인해 다음과 같이 과도한 용량이 배포됩니다.

- 예를 들어 ANF에서 호스트되는 NFS 볼륨에서 250MiB/초의 처리량을 달성하려면 Ultra 서비스 수준의 1.95TiB 용량을 배포해야 합니다. 
- 400MiB/초를 달성하려면 3.125TiB 용량을 배포해야 합니다. 하지만, 볼륨에 필요한 처리량을 달성하려면 용량을 과도하게 프로비저닝해야 할 수 있습니다. 용량을 과도하게 프로비저닝하면 작은 HANA 인스턴스의 가격 책정에 영향을 줍니다. 
- SAP /sapmnt 디렉터리에 대해 ANF를 기반으로 NFS를 사용하는 공간에서는 일반적으로 Azure NetApp Files에 의해 적용되는 최소 용량인 100GiB ~ 150GiB를 사용합니다. 하지만 고객 경험에 따르면 관련 처리량 12.8MiB/초(Ultra 서비스 수준 사용)가 충분하지 않을 수 있으며 SAP 시스템의 안정성에 부정적인 영향을 미칠 수 있습니다. 이런 경우 고객은 / sapmnt 볼륨의 크기를 늘려서 해당 볼륨에 더 많은 처리량이 제공되도록 하여 문제를 피할 수 있습니다.

SAP 워크로드에 대한 기능 매트릭스는 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 베이스 VHD | 작동하지 않음 | - |
| 데이터 디스크 | 적합 | SAP HANA만  |
| SAP 전역 전송 디렉터리 | YES | SMB 및 NFS |
| SAP sapmnt | 적합 | 모든 시스템 SMB(Windows만 해당) 또는 NFS(Linux만 해당) |
| 백업 스토리지 | 적합 | - |
| 공유/공유 디스크 | YES | SMB 3.0, NFS v3 및 NFS v4.1 |
| 복원력 | LRS | GRS 또는 ZRS를 디스크에 사용할 수 없음 |
| 대기 시간 | 매우 낮음 | - |
| IOPS SLA | YES | - |
| 용량에 비례하는 IOPS | 완전한 선형  | [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 따라 다름 |
| 처리량 SLA | YES | - |
| 용량에 비례하는 처리량 | 괄호 안 반선형 | [서비스 수준](../../../azure-netapp-files/azure-netapp-files-service-levels.md)에 따라 다름 |
| HANA 인증됨 | YES | - |
| 디스크 스냅샷 가능 | YES | - |
| Azure Backup VM 스냅샷 가능 | 아니요 | - |
| 비용 | Premium Storage보다 높음 | - |


ANF 스토리지의 추가로 기본 제공되는 기능:

- 볼륨의 스냅샷을 수행하는 기능
- 스냅샷에서 ANF 볼륨 복제
- 스냅샷에서 볼륨 복원(snap-revert)

**요약**: Azure NetApp Files는 NFS 및 SMB 볼륨이나 공유를 배포하는 데 사용할 수 있는 HANA 인증을 받은 대기 시간이 짧은 스토리지입니다. 스토리지에는 볼륨의 GiB 용량에 따라 선형 방식으로 다른 처리량과 IOPS를 제공하는 세 가지 서비스 수준이 제공됩니다. ANF 스토리지는 대기 노드를 사용하여 SAP HANA 스케일 아웃 시나리오를 배포할 수 있도록 합니다. ANF 스토리지는 /sapmnt 또는 SAP 글로벌 전송 디렉터리에 필요한 파일 공유를 제공하는 데 적합합니다. ANF 스토리지는 원시 NetApp 기능으로 사용할 수 있는 기능 가용성과 함께 제공됩니다.  



## <a name="azure-standard-ssd-storage"></a>Azure 표준 SSD 스토리지
Azure 표준 HDD 스토리지에 비해 Azure 표준 SSD 스토리지는 더 나은 가용성, 일관성, 안정성 및 대기 시간을 제공합니다. 낮은 IOPS 수준에서 일관된 성능이 필요한 워크로드에 최적화되어 있습니다. 이 스토리지는 IOPS 및 처리량 요구가 낮은 비프로덕션 SAP 시스템에 사용되는 최소 스토리지입니다. SAP 워크로드에 대한 기능 매트릭스는 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 베이스 VHD | 제한적 적합 | 비프로덕션 시스템 |
| 데이터 디스크 | 제한적 적합 | IOPS 및 대기 시간 요구가 낮은 일부 비프로덕션 시스템 |
| SAP 전역 전송 디렉터리 | 아니요 | [지원되지 않음](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 제한적 적합 | 비프로덕션 시스템 |
| 백업 스토리지 | 적합 | - |
| 공유/공유 디스크 | 사용할 수 없음 | 타사 필요 |
| 복원력 | LRS, GRS | 디스크에 사용 가능한 ZRS 없음 |
| 대기 시간 | high | SAP 전역 전송 디렉터리 또는 프로덕션 시스템에 대해 너무 높음 |
| IOPS SLA | 아니요 | - |
| 디스크당 최대 IOPS | 500 | 디스크 크기와 무관 |
| 처리량 SLA | 아니요 | - |
| HANA 인증됨 | 아니요 | - |
| 디스크 스냅샷 가능 | YES | - |
| Azure Backup VM 스냅샷 가능 | YES | - |
| 비용 | LOW | - |



**요약:** Azure 표준 SSD 스토리지는 베이스 VHD용 비프로덕션 VM, 상대적으로 대기 시간에 민감하지 않거나 IOPS 및 처리량 속도가 느린 경우의 최종 DBMS 배포에 대한 최소 권장 사항입니다. 이 Azure Storage 유형은 더 이상 SAP Global 전송 디렉터리 호스팅을 지원하지 않습니다. 



## <a name="azure-standard-hdd-storage"></a>Azure 표준 HDD 스토리지
Azure 표준 HDD 스토리지는 Azure 인프라가 2014년에 SAP NetWeaver 워크로드에 대해 인증을 받을 당시 유일한 스토리지 유형이었습니다. 2014년에 Azure 가상 머신은 작고 스토리지 처리량이 적었습니다. 따라서 이 스토리지 유형은 요구를 따라가기만 했습니다. 이 스토리지는 대기 시간에 민감하지 않은 워크로드(SAP 공간에서는 거의 경험할 수 없음)에 이상적입니다. Azure VM의 처리량이 증가하고 이러한 VM이 생성하는 워크로드가 증가함에 따라 이 스토리지 유형은 더 이상 SAP 시나리오에서 사용할 수 없습니다. SAP 워크로드에 대한 기능 매트릭스는 다음과 같습니다.

| 기능| 의견| 노트/링크 | 
| --- | --- | --- | 
| OS 베이스 VHD | 부적합 | - |
| 데이터 디스크 | 부적합 | - |
| SAP 전역 전송 디렉터리 | 아니요 | [지원되지 않음](https://launchpad.support.sap.com/#/notes/2015553) |
| SAP sapmnt | 아니요 | 지원되지 않음 |
| 백업 스토리지 | 적합 | - |
| 공유/공유 디스크 | 사용할 수 없음 | Azure Files 또는 타사 필요 |
| 복원력 | LRS, GRS | 디스크에 사용 가능한 ZRS 없음 |
| 대기 시간 | high | DBMS 사용, SAP 전역 전송 디렉터리 또는 sapmnt/saploc에 대해 너무 높음 |
| IOPS SLA | 아니요 | - |
| 디스크당 최대 IOPS | 500 | 디스크 크기와 무관 |
| 처리량 SLA | 아니요 | - |
| HANA 인증됨 | 아니요 | - |
| 디스크 스냅샷 가능 | YES | - |
| Azure Backup VM 스냅샷 가능 | YES | - |
| 비용 | LOW | - |



**요약:** 표준 HDD는 SAP 백업을 저장하는 데만 사용해야 하는 Azure 스토리지 유형입니다. 데이터 조회에 사용되는 사용 중지된 시스템과 같은 비활성 시스템의 기본 VHD로만 사용해야 합니다. 활성 개발, QA 또는 프로덕션 VM은 해당 스토리지를 기반으로 해서는 안 되며 데이터베이스 파일 또한 해당 스토리지에서 호스트되어서는 안 됩니다.


## <a name="azure-vm-limits-in-storage-traffic"></a>스토리지 트래픽에서의 Azure VM 한도
온-프레미스 시나리오와는 반대로, 직접 선택하는 개별 VM 유형은 달성할 수 있는 스토리지 대역폭에서 중요한 역할을 합니다. 다양한 스토리지 유형에 대해 다음을 고려해야 합니다.

| 스토리지 유형| Linux | Windows | 의견 |
| --- | --- | --- | --- |
| 표준 HDD | [Azure의 Linux VM 크기](../../sizes.md) | [Azure의 Windows VM 크기](../../sizes.md) | 중간 규모 또는 큰 VM의 스토리지 한도를 조정하기 어려울 수 있음 |
| 표준 SSD | [Azure의 Linux VM 크기](../../sizes.md) | [Azure의 Windows VM 크기](../../sizes.md) | 중간 규모 또는 큰 VM의 스토리지 한도를 조정하기 어려울 수 있음 |
| Premium Storage | [Azure의 Linux VM 크기](../../sizes.md) | [Azure의 Windows VM 크기](../../sizes.md) | 스토리지 구성으로 IOPS 또는 스토리지 처리량 VM 한도에 쉽게 도달 |
| Ultra 디스크 스토리지 | [Azure의 Linux VM 크기](../../sizes.md) | [Azure의 Windows VM 크기](../../sizes.md) | 스토리지 구성으로 IOPS 또는 스토리지 처리량 VM 한도에 쉽게 도달 |
| Azure NetApp Files | [Azure의 Linux VM 크기](../../sizes.md) | [Azure의 Windows VM 크기](../../sizes.md) | 스토리지 트래픽은 스토리지 대역폭이 아닌 네트워크 처리량 대역폭을 사용합니다! |

제한 사항으로는 다음을 고려할 수 있습니다.

- VM이 작을수록 연결할 수 있는 디스크의 수가 적습니다. ANF에는 적용되지 않습니다. NFS 또는 SMB 공유를 탑재하기 때문에 연결할 수 있는 공유 볼륨의 수에 제한이 없습니다.
- VM의 I/O 처리량 및 IOPS 한도는 Premium Storage 디스크 및 Ultra Disk를 사용하여 쉽게 초과할 수 있습니다.
- ANF를 사용하는 경우 공유 볼륨에 대한 트래픽은 VM의 네트워크 대역폭을 사용하고 스토리지 대역폭은 사용하지 않습니다.
- 이중 숫자 TiB 용량 공간에 있는 NFS 볼륨이 큰 경우, 단일 VM에서 해당 볼륨에 액세스하는 처리량은 공유 볼륨과 상호 작용하는 단일 세션에 대한 Linux의 한도에 따라 정체되게 됩니다. 

SAP 시스템의 수명 주기에서 Azure VM의 크기를 조정하는 경우 더 크고 새로운 유형의 IOPS 및 저장소 처리량 한도를 평가해야 합니다. 일부 경우에는 Azure VM의 새 기능에 맞춰 스토리지 구성을 조정하는 것도 적합할 수 있습니다. 


## <a name="striping-or-not-striping"></a>스트라이프 여부 결정
여러 Azure 디스크에서 하나의 큰 볼륨으로 스트라이프 세트를 만들면 개별 디스크의 IOPS 및 처리량을 단일 볼륨에 누적시킬 수 있습니다. Azure Standard Storage 및 Azure Premium Storage에만 사용됩니다. Azure Ultra Disk는 디스크 용량과 관계없이 처리량 및 IOPS를 구성할 수 있으므로 스트라이프 세트를 사용하지 않아도 됩니다. NFS 또는 SMB 기반 공유 볼륨은 스트라이프할 수 없습니다. Azure Premium Storage 처리량 및 IOPS의 비선형적인 특성으로 인해 대용량 단일 Azure Premium Storage 디스크와 동일한 IOPS 및 처리량을 사용하여 더 작은 용량을 프로비저닝할 수 있습니다. 이는 Azure Premium Storage를 사용하여 저렴한 비용으로 높은 처리량 또는 IOPS를 달성할 수 있는 방법입니다. 예를 들어 2개의 P15 Premium Storage 디스크에 스트라이프 처리를 하면: 

- 250MiB/초의 처리량을 얻을 수 있습니다. 해당 볼륨은 512GiB의 용량을 갖게 됩니다. 초당 250MiB의 처리량을 제공하는 단일 디스크를 사용하려면 2개의 TiB 용량이 있는 P40 디스크를 선택해야 합니다. 
- 스트라이프 처리를 통해 전체 용량이 512GiB인 P10 Premium Storage 디스크 4개를 스트라이프하여 400MiB/초. 초당 최소 500MiB의 처리량을 가지는 단일 디스크를 사용하려면 용량이 8TiB인 P60 Premium Storage 디스크를 선택해야 합니다. Premium Storage 비용은 용량과 선형 관계에 가까우므로 스트라이프를 사용하여 비용을 절감할 수 있습니다.

스트라이프 시 다음과 같은 일부 규칙을 따라야 합니다.

- Azure Storage는 데이터 중복을 유지하므로 VM에서 구성된 스토리지를 사용해야 합니다.
- 스트라이프 세트가 적용되는 디스크는 서로 동일한 크기여야 합니다.

Azure Premium Storage를 사용하여 최적의 가격과 성능 비율을 얻을 수 있는 가장 좋은 방법은 여러 개의 소규모 디스크를 스트라이프하는 것입니다. 스트라이프 처리를 하는 경우 추가로 배포 및 관리가 필요할 수 있습니다.

특정 스트라이프 크기 권장 사항은 다른 DBMS에 대한 설명서(예: [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md))를 참조하세요.




## <a name="next-steps"></a>다음 단계
문서를 읽어보세요.

- [SAP 워크로드용 Azure Virtual Machines DBMS 배포 시 고려 사항](./dbms_guide_general.md)
- [SAP HANA Azure 가상 머신 스토리지 구성](./hana-vm-operations-storage.md)
