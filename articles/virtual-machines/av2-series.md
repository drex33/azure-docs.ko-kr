---
title: Av2 시리즈
description: Av2 시리즈 VM의 사양입니다.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: dbb10405408250be55bf10f49d3c557a0205df92
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692871"
---
# <a name="av2-series"></a>Av2 시리즈

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

다양한 하드웨어 유형 및 프로세서에 Av2 시리즈 VM을 배포할 수 있습니다. Av2 시리즈 VM에는 개발 및 테스트와 같은 엔트리 레벨 워크로드에 가장 적합한 CPU 성능 및 메모리 구성이 있습니다. 배포된 하드웨어와 관계없이 인스턴스 실행 시 일관된 프로세서 성능을 제공하기 위해 크기가 제한됩니다. 이 크기가 배포되는 실제 하드웨어를 확인하려면 Virtual Machine 내에서 가상 하드웨어를 쿼리합니다. 사용 사례로는 개발 및 테스트 서버, 트래픽이 적은 웹 서버, 중소 규모의 데이터베이스, 개념 증명, 코드 리포지토리 등이 있습니다.

[ACU](acu.md): 100<br>
[Premium Storage](premium-storage-performance.md): 지원되지 않음 <br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원되지 않음 <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원됨 <br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원됨 <br>
[VM 생성 지원](generation-2.md): 1세대 <br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원되지 않음<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원되지 않음 <br>
<br>

| 크기 | vCore | 메모리: GiB | 임시 스토리지(SSD) GiB | 최대 임시 스토리지 처리량: IOPS/읽기 MBps/쓰기 MBps | 최대 데이터 디스크/처리량: IOPS | 최대 NIC 수 | 예상 네트워크 대역폭(Mbps)
|---|---|---|---|---|---|---|---|
| Standard_A1_v2  | 1 | 2  | 10 | 1000/20/10  | 2/2x500   | 2 | 250  |
| Standard_A2_v2  | 2 | 4  | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4_v2  | 4 | 8  | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8_v2  | 8 | 16 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |
| Standard_A2m_v2 | 2 | 16 | 20 | 2000/40/20  | 4/4x500   | 2 | 500  |
| Standard_A4m_v2 | 4 | 32 | 40 | 4000/80/40  | 8/8x500   | 4 | 1000 |
| Standard_A8m_v2 | 8 | 64 | 80 | 8000/160/80 | 16/16x500 | 8 | 2000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 정보: [디스크 유형](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
