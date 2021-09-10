---
title: Azure 관리 디스크의 성능 계층
description: 관리 디스크의 성능 계층에 대하여 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: d2f617d2f5d05a6310d5442fa8d4a9e49369912e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696421"
---
# <a name="performance-tiers-for-managed-disks"></a>관리 디스크의 성능 계층

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

Azure 관리 디스크의 성능은 디스크를 만들 때 해당 디스크의 성능 계층 형태로 설정됩니다. 성능 계층은 관리 디스크의 IOPS 및 처리량을 결정합니다. 디스크의 프로비전 크기를 설정할 때 성능 계층이 자동으로 선택됩니다. 성능 계층은 디스크 크기를 변경하지 않고서도 배포할 때나 그 이후에 변경될 수 있습니다.

성능 계층을 변경하면 디스크의 버스팅 기능을 사용하지 않고서도 더 높은 수요에 맞게 준비하고 그에 충족할 수 있습니다. 추가 성능이 얼마나 오래 필요한지에 따라 버스팅에 의존하는 것 보다 성능 계층을 변경하는 것이 비용적인 측면에서 더 효율적일 수 있습니다. 휴일 쇼핑이나 성능 테스트 또는 학습 환경을 실행하는 경우처럼 일관되게 높은 수준의 성능이 일시적으로 필요한 상황에 이상적입니다. 이러한 상황을 처리하기 위하여 필요한 기간만큼 성능 계층을 높여 사용할 수 있습니다. 그런 다음 추가 성능이 더 이상 필요하지 않게 되었을 때 원래 계층으로 복귀할 수 있습니다.

## <a name="how-it-works"></a>작동 방법

디스크를 처음 배포하거나 프로비전할 때 디스크의 기준 성능 계층은 프로비전된 디스크 크기에 맞춰 설정됩니다. 높아진 수요를 충족하기 위하여 원래 기준보다 성능 계층을 높여 사용할 수 있습니다. 해당 성능 수준이 더 이상 필요 없어지면, 초기 기준 성능 계층으로 돌아갈 수 있습니다.

성능 계층이 달라지면 청구 내용도 달라집니다. 예를 들어, P10 디스크(128GiB)를 프로비전하는 경우, 기준 성능 계층은 P10(500 IOPS 및 100MBps)으로 설정됩니다. 요금은 P10 요율에 맞춰 청구됩니다. 디스크 크기를 늘리지 않고서도 P50(7,500 IOPS 및 250MBps) 성능에 맞추고자 해당 계층을 업그레이드할 수 있습니다. 업그레이드되어 있는 도중에는 P50 요율에 맞춰 요금이 청구됩니다. 높아진 성능이 더 이상 필요 없어지면 다시 P10 계층으로 돌아갈 수 있습니다. 디스크는 다시 P10 요율대로 요금이 청구됩니다.

| 디스크 크기 | 기준 성능 계층 | 다음으로 업그레이드 가능: |
|----------------|-----|-------------------------------------|
| 4GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128GiB | P10 | P15, P20, P30, P40, P50 |
| 256GiB | P15 | P20, P30, P40, P50 |
| 512GiB | P20 | P30, P40, P50 |
| 1TiB | P30 | P40, P50 |
| 2TiB | P40 | P50 |
| 4TiB | P50 | 없음 |
| 8TiB | P60 |  P70, P80 |
| 16TiB | P70 | P80 |
| 32TiB | P80 | 없음 |

청구 정보는 [관리 디스크 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)을 참조하세요.

## <a name="restrictions"></a>제한

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>다음 단계

성능 계층 변경 방법에 대하여 확인하려면 [포털](disks-performance-tiers-portal.md)이나 [PowerShell/CLI](disks-performance-tiers.md) 문서를 참조하세요.

