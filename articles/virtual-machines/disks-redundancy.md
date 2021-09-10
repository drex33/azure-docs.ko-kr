---
title: Azure 관리 디스크에 대한 중복 옵션
description: Azure 관리 디스크에 대한 영역 중복 스토리지 및 로컬 중복 스토리지에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 07/12/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: b24f24547e45b8ef580715828839ec1b1e5b4618
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692695"
---
# <a name="redundancy-options-for-managed-disks"></a>관리 디스크에 대한 중복성 옵션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Azure 관리 디스크는 ZRS(영역 중복 스토리지)를 미리 보기 및 로컬 중복 스토리지로 두 개의 스토리지 중복성 옵션을 제공합니다. ZRS는 LRS(로컬 중복 스토리지)보다 관리 디스크에 대한 고가용성을 제공합니다. 그러나 LRS 디스크는 단일 데이터 센터의 3개 복사본에 데이터를 동기식으로 쓰기 때문에 LRS 디스크의 쓰기 대기 시간이 ZRS 디스크보다 좋습니다.

## <a name="locally-redundant-storage-for-managed-disks"></a>관리 디스크에 대한 로컬 중복 스토리지

LRS(로컬 중복 스토리지)는 선택된 지역의 단일 데이터 센터 내에서 데이터를 세 번 복제합니다. LRS는 서버 랙 및 드라이브 오류로부터 데이터를 보호합니다. 자연재해나 기타 문제와 같은 영역 오류로부터 LRS 디스크를 보호하려면 다음 단계를 수행합니다.

- 두 영역에 데이터를 동기적으로 쓰고 재해 발생 시 다른 영역으로 자동으로 장애 조치(failover)할 수 있는 애플리케이션을 사용합니다.
    - 예를 들어, SQL Server AlwaysOn이 있습니다.
- ZRS 스냅샷을 사용하여 LRS 디스크를 자주 백업합니다.
- [Azure Site Recovery](../site-recovery/azure-to-azure-how-to-enable-zone-to-zone-disaster-recovery.md)를 통해 LRS 디스크에 대한 영역 간 재해 복구를 사용합니다. 그러나 영역 간 재해 복구는 0개의 RPO(복구 지점 목표)를 제공하지 않습니다.

워크플로가 영역 전체에서 애플리케이션 수준의 동기 쓰기를 지원하지 않거나 애플리케이션이 0 RPO를 충족해야 하는 경우 ZRS 디스크가 이상적입니다.

## <a name="zone-redundant-storage-for-managed-disks-preview"></a>관리 디스크에 대한 영역 중복 스토리지(미리 보기)

ZRS(영역 중복 스토리지)는 선택하는 지역에 있는 세 개의 Azure 가용성 영역에서 Azure 관리 디스크를 동기적으로 복제합니다. 각 가용성 영역은 독립적인 전원, 냉각 및 네트워킹을 갖춘 별도의 물리적 위치입니다. 

ZRS디스크(미리 보기)를 사용하면 가용성 영역의 오류에서 복구할 수 있습니다. 영역의 작동이 중단되면 다른 영역의 VM(가상 머신)에 ZRS 디스크를 연결할 수 있습니다. SQL FCI, SAP ASCS/SCS 또는 GFS2와 같은 클러스터형 또는 분산 애플리케이션에서 가용성을 개선하기 위해 VM 간에 ZRS 디스크를 공유할 수도 있습니다. 공유 ZRS 디스크를 서로 다른 영역의 기본 및 보조 VM에 연결하여 ZRS와 [가용성 영역](../availability-zones/az-overview.md)을 둘 다 활용할 수 있습니다. 주 영역에 오류가 발생하면 [SCSI 영구 예약](disks-shared-enable.md#supported-scsi-pr-commands)을 사용하여 보조 VM으로 신속하게 장애 조치(failover)할 수 있습니다.

### <a name="billing-implications"></a>요금 청구 영향

자세한 내용은 [Azure 가격 페이지](https://azure.microsoft.com/pricing/details/managed-disks/)를 참조하세요.

### <a name="comparison-with-other-disk-types"></a>다른 디스크 유형과 비교

더 많은 쓰기 대기 시간을 제외하고 ZRS를 사용하는 디스크는 LRS를 사용하는 디스크와 동일합니다. 해당 디스크는 동일한 스케일링 대상을 포함합니다. [디스크를 벤치마크](disks-benchmarks.md)하여 애플리케이션의 워크로드를 시뮬레이션하고 LRS 디스크와 ZRS 디스크 간에 대기 시간을 비교합니다. 

### <a name="limitations"></a>제한 사항

[!INCLUDE [disk-storage-zrs-limitations](../../includes/disk-storage-zrs-limitations.md)]

## <a name="next-steps"></a>다음 단계

- ZRS 디스크를 만드는 방법을 알아보려면 [ZRS 관리 디스크 배포](disks-deploy-zrs.md)를 참조하세요.
