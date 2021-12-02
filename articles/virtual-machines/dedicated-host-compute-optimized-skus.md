---
title: 계산에 최적화 된 Azure 전용 호스트 Sku
description: 계산에 최적화 된 ADH Sku의 VM 압축 사양입니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: 9675e42830a1ffa3366b2b7a9c5473f147346518
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440618"
---
# <a name="compute-optimized-azure-dedicated-host-skus"></a>계산에 최적화 된 Azure 전용 호스트 Sku
Azure 전용 호스트 Sku는 VM 제품군 및 특정 하드웨어 사양의 조합입니다. 전용 호스트 SKU에서 지정 하는 VM 시리즈의 Vm만 배포할 수 있습니다. 예를 들어 Dsv3-3에서 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm만 프로 비전 할 수 있습니다. 

이 문서에서는 모든 계산에 최적화 된 전용 호스트 Sku에 대 한 하드웨어 사양 및 VM 패키지를 안내 합니다.

## <a name="limitations"></a>제한 사항

전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="fsv2"></a>Fsv2
### <a name="fsv2-type2"></a>Fsv2-Type2

Fsv2-Type2는 Intel® Skylake (Xeon® Platinum 8168) 프로세서를 활용 하는 전용 호스트 SKU입니다. 48 물리적 코어, 72 vCPUs 및 144 GiB RAM을 제공 합니다. Fsv2-Type2 [Fsv2 시리즈](fsv2-series.md) vm을 실행 합니다.

다음 압축 구성은 Fsv2-Type2 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 48             | 72              | 144 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 18    |
|                |                 |               | F8s v2  | 9     |
|                |                 |               | F16s v2 | 4     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

### <a name="fsv2-type3"></a>Fsv2-Type3

Fsv2-Type3는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 86 vCPUs 및 504 GiB RAM을 제공 합니다. Fsv2-Type3 [Fsv2 시리즈](fsv2-series.md) vm을 실행 합니다.

다음 압축 구성은 Fsv2-Type3 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 86              | 504GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 21    |
|                |                 |               | F8s v2  | 10    |
|                |                 |               | F16s v2 | 5     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

### <a name="fsv2-type4"></a>Fsv2-Type4

Fsv2-Type4는 Intel® Ice Lake (Xeon® 플래티넘 8370C) 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 119 vCPUs 및 768 GiB RAM을 제공 합니다. Fsv2-Type4 [Fsv2 시리즈](fsv2-series.md) vm을 실행 합니다.

다음 압축 구성은 Fsv2-Type4 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 119             | 768 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 25    |
|                |                 |               | F8s v2  | 12    |
|                |                 |               | F16s v2 | 6     |
|                |                 |               | F32s v2 | 3     |
|                |                 |               | F48s v2 | 2     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

## <a name="fxmds"></a>FXmds
### <a name="fxmds-type1"></a>FXmds-Type1

FXmds-Type1 Intel® Cascade Lake(Xeon® Gold 6246R) 프로세서를 활용하는 Dedicated Host SKU입니다. 32개의 실제 코어, 48개의 vC CPU 및 1,152 GiB RAM을 제공합니다. FXmds-Type1 [FX 시리즈](fx-series.md) VM을 실행합니다.

다음 Packing 구성에서는 FXmds-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 32             | 48              | 1,152 GiB     | FX4mds  | 12    |
|                |                 |               | FX12mds | 4     |
|                |                 |               | FX24mds | 2     |
|                |                 |               | FX36mds | 1     |
|                |                 |               | FX48mds | 1     |

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 극대화하기 위해 영역과 오류 도메인을 모두 사용합니다.
