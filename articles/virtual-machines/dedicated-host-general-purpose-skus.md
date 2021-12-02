---
title: 범용 Azure 전용 호스트 Sku
description: 범용 ADH Sku의 VM 압축 사양입니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: 8213083546e7b6067b3de165f4aa7700d182dd04
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440610"
---
# <a name="general-purpose-azure-dedicated-host-skus"></a>범용 Azure 전용 호스트 Sku
Azure 전용 호스트 Sku는 VM 제품군 및 특정 하드웨어 사양의 조합입니다. 전용 호스트 SKU에서 지정 하는 VM 시리즈의 Vm만 배포할 수 있습니다. 예를 들어 Dsv3-3에서 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm만 프로 비전 할 수 있습니다. 

이 문서에서는 모든 범용 전용 호스트 Sku에 대 한 하드웨어 사양 및 VM 패키지를 안내 합니다.

## <a name="limitations"></a>제한 사항

전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="dasv4"></a>Dasv4
### <a name="dasv4-type1"></a>Dasv4-Type1
Dasv4-Type1는 AMD의 2.35 GHz EPYC™ 7452 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 96 vCPUs 및 672 GiB RAM을 제공 합니다. Dasv4-Type1 [Dasv4 시리즈](dav4-dasv4-series.md#dasv4-series) vm을 실행 합니다. 특정 VM 성능 정보를 더 잘 이해 하려면 VM 크기 설명서를 참조 하세요.

다음 압축 구성은 Dasv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672GiB       | D2as v4  | 32    |
|                |                 |               | D4as v4  | 24    |
|                |                 |               | D8as v4  | 12    |
|                |                 |               | D16as v4 | 6     |
|                |                 |               | D32as v4 | 3     |
|                |                 |               | D48as v4 | 2     |
|                |                 |               | D64as v4 | 1     |
|                |                 |               | D96as v4 | 1     |

Dasv4-Type1에서 여러 VM 크기를 혼합할 수도 있습니다. 다음은 Dasv4-Type1에서 VM 패키지의 샘플 조합입니다.
- 1 D48asv4 + 3 D16asv4
- 1 D32asv4 + 2 D16asv4 + 8 D4asv4
- 20 D4asv4 + 8 D2asv4

### <a name="dasv4-type2"></a>Dasv4-Type2
Dasv4-Type2은 AMD의 EPYC™ 7763v 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 112 vCPUs 및 768 GiB RAM을 제공 합니다. Dasv4-Type2 [Dasv4 시리즈](dav4-dasv4-series.md#dasv4-series) vm을 실행 합니다. 특정 VM 성능 정보를 더 잘 이해 하려면 VM 크기 설명서를 참조 하세요.

다음 압축 구성은 Dasv4-Type2 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 112             | 768 GiB       | D2as v4  | 32    |
|                |                 |               | D4as v4  | 25    |
|                |                 |               | D8as v4  | 12    |
|                |                 |               | D16as v4 | 6     |
|                |                 |               | D32as v4 | 3     |
|                |                 |               | D48as v4 | 2     |
|                |                 |               | D64as v4 | 1     |
|                |                 |               | D96as v4 | 1     |

## <a name="ddsv4"></a>Ddsv4
### <a name="ddsv4-type1"></a>Ddsv4-Type1
Ddsv4-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 68 vCPUs 및 504 GiB RAM을 제공 합니다. Ddsv4-Type1 [Ddsv4 시리즈](ddv4-ddsv4-series.md#ddsv4-series) vm을 실행 합니다.

다음 압축 구성은 Ddsv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 52             | 68              | 504GiB       | D2ds v4  | 32    |
|                |                 |               | D4ds v4  | 17    |
|                |                 |               | D8ds v4  | 8     |
|                |                 |               | D16ds v4 | 4     |
|                |                 |               | D32ds v4 | 1     |
|                |                 |               | D48ds v4 | 1     |
|                |                 |               | D64ds v4 | 1     |

Ddsv4-Type1에서 여러 VM 크기를 혼합할 수도 있습니다. 다음은 Ddsv4-Type1에서 VM 패키지의 샘플 조합입니다.
- 1 D48dsv4 + 4 D4dsv4 + 2 D2dsv4
- 1 D32dsv4 + 2 D16dsv4 + 1 D4dsv4
- 10 D4dsv4 + 14 D2dsv4

### <a name="ddsv4-type2"></a>Ddsv4-Type2
Ddsv4-Type2는 Intel® Ice Lake (Xeon® 플래티넘 8370C) 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 119 vCPUs 및 768 GiB RAM을 제공 합니다. Ddsv4-Type2 [Ddsv4 시리즈](ddv4-ddsv4-series.md#ddsv4-series) vm을 실행 합니다.

다음 압축 구성은 Ddsv4-Type2 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 119             | 768 GiB       | D2ds v4  | 32    |
|                |                 |               | D4ds v4  | 19    |
|                |                 |               | D8ds v4  | 9     |
|                |                 |               | D16ds v4 | 4     |
|                |                 |               | D32ds v4 | 2     |
|                |                 |               | D48ds v4 | 1     |
|                |                 |               | D64ds v4 | 1     |

## <a name="dsv4"></a>Dsv4
### <a name="dsv4-type1"></a>Dsv4-Type1

Dsv4-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 80 vCPUs 및 504 GiB RAM을 제공 합니다. Dsv4-Type1 [Dsv4 시리즈](dv4-dsv4-series.md#dsv4-series) vm을 실행 합니다.

다음 압축 구성은 Dsv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504GiB       | D2s v4  | 32    |
|                |                 |               | D4s v4  | 20    |
|                |                 |               | D8s v4  | 10    |
|                |                 |               | D16s v4 | 5     |
|                |                 |               | D32s v4 | 2     |
|                |                 |               | D48s v4 | 1     |
|                |                 |               | D64s v4 | 1     |

Dsv4-Type1에서 여러 VM 크기를 혼합할 수도 있습니다. 다음은 Dsv4-Type1에서 VM 패키지의 샘플 조합입니다.
- 1 D64sv4 + 1 D16sv4
- 1 D32sv4 + 2 D16dsv4 + 2 D8sv4
- 10 D4sv4 + 20 D2sv4

### <a name="dsv4-type2"></a>Dsv4-Type2

Dsv4-Type2는 Intel® Ice Lake (Xeon® 플래티넘 8370C) 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 119 vCPUs 및 768 GiB RAM을 제공 합니다. Dsv4-Type2 [Dsv4 시리즈](dv4-dsv4-series.md#dsv4-series) vm을 실행 합니다.

다음 압축 구성은 Dsv4-Type2 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 119             | 768 GiB       | D2s v4  | 32    |
|                |                 |               | D4s v4  | 25    |
|                |                 |               | D8s v4  | 12    |
|                |                 |               | D16s v4 | 6     |
|                |                 |               | D32s v4 | 3     |
|                |                 |               | D48s v4 | 2     |
|                |                 |               | D64s v4 | 1     |

## <a name="dsv3"></a>Dsv3
### <a name="dsv3-type1"></a>Dsv3-Type1

Dsv3-Type1는 Intel® Broadwell (2.3 GHz Xeon® E5-2673 v4) 프로세서를 활용 하는 전용 호스트 SKU입니다. 40 물리적 코어, 64 vCPUs 및 256 GiB RAM을 제공 합니다. Dsv3-Type1 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm을 실행 합니다.

다음 압축 구성은 Dsv3-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 256GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 16    |
|                |                 |               | D8s v3  | 8     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Dsv3-Type1에서 여러 VM 크기를 혼합할 수도 있습니다. 다음은 Dsv3-Type1에서 VM 패키지의 샘플 조합입니다.
- 1 D32sv3 + 1 D16sv3 + 1 D8sv3
- 1 D48sv3 + 3 D4sv3 + 2 D2sv3
- 10 D4sv3 + 12 D2sv3

### <a name="dsv3-type2"></a>Dsv3-Type2

Dsv3-Type2는 Intel® Skylake (2.1 GHz Xeon® 플래티넘 8171M) 프로세서를 활용 하는 전용 호스트 SKU입니다. 48 물리적 코어, 76 vCPUs 및 504 GiB RAM을 제공 합니다. Dsv3-Type2 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm을 실행 합니다.

다음 압축 구성은 Dsv3-Type2 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 18    |
|                |                 |               | D8s v3  | 9     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Dsv3-Type2에서 여러 VM 크기를 혼합할 수도 있습니다. 다음은 Dsv3-Type2에서 VM 패키지의 샘플 조합입니다.
- 1 D64sv3 + 2 D4vs3 + 2 D2sv3
- 1 D48sv3 + 4 D4sv3 + 6 D2sv3
- 12 D4sv3 + 14 D2sv3

### <a name="dsv3-type3"></a>Dsv3-Type3

Dsv3-Type3는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 80 vCPUs 및 504 GiB RAM을 제공 합니다. Dsv3-Type3 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm을 실행 합니다.

다음 압축 구성은 Dsv3-Type3 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 20    |
|                |                 |               | D8s v3  | 10    |
|                |                 |               | D16s v3 | 5     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

Dsv3-3에서 여러 VM 크기를 혼합할 수도 있습니다. 다음은 Dsv3-3에 대 한 VM 패키지의 샘플 조합입니다.
- 1 D64sv3 + 1 D8sv3 + 2 D4sv3
- 1 D48sv3 + 1 D16sv3 + 4 D4sv3
- 15 D4sv3 + 10 D2sv3

### <a name="dsv3-type4"></a>Dsv3-Type4

Dsv3-Type4는 Intel® Ice Lake (Xeon® 플래티넘 8370C) 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 119 vCPUs 및 768 GiB RAM을 제공 합니다. Dsv3-Type4 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm을 실행 합니다.

다음 압축 구성은 Dsv3-Type4 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 119             | 768 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 25    |
|                |                 |               | D8s v3  | 12    |
|                |                 |               | D16s v3 | 6     |
|                |                 |               | D32s v3 | 3     |
|                |                 |               | D48s v3 | 2     |
|                |                 |               | D64s v3 | 1     |

## <a name="dcsv2"></a>DCsv2
### <a name="dcsv2-type1"></a>DCsv2-Type1

DCsv2-Type1는 Intel® 커피 Lake (Xeon® E-2288G (SGX 기술 포함) 프로세서를 활용 하는 전용 호스트 SKU입니다. 8 개의 물리적 코어, 8 개 vCPUs 및 64 GiB RAM을 제공 합니다. DCsv2-Type1 [DCsv2 시리즈](dcv2-series.md) vm을 실행 합니다.

다음 압축 구성은 DCsv2-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 8              | 8               | 64GiB        | DC8 v2 | 1     |

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 극대화하기 위해 영역과 오류 도메인을 모두 사용합니다.
