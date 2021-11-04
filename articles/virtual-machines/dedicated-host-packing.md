---
title: 전용 호스트 SKU 구성 테이블
description: ADH Sku의 VM 압축에 대 한 사양입니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 67b17324b550196728da62cb0e5b306d6387fa25
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131483054"
---
# <a name="azure-dedicated-host-sku-configuration-tables"></a>Azure 전용 호스트 SKU 구성 테이블
Azure 전용 호스트 Sku는 VM 제품군 및 특정 하드웨어 사양의 조합입니다. 전용 호스트 SKU에서 지정 하는 VM 시리즈의 Vm만 배포할 수 있습니다. 예를 들어 Dsv3-3에서 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm만 프로 비전 할 수 있습니다. 

이 문서에서는 모든 전용 호스트 Sku에 대 한 하드웨어 사양 및 VM 패키지를 안내 합니다.

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

## <a name="dcsv2"></a>DCsv2
### <a name="dcsv2-type1"></a>DCsv2-Type1

DCsv2-Type1는 Intel® 커피 Lake (Xeon® E-2288G (SGX 기술 포함) 프로세서를 활용 하는 전용 호스트 SKU입니다. 8 개의 물리적 코어, 8 개 vCPUs 및 64 GiB RAM을 제공 합니다. DCsv2-Type1 [DCsv2 시리즈](dcv2-series.md) vm을 실행 합니다.

다음 압축 구성은 DCsv2-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 8              | 8               | 64GiB        | DC8s v2 | 1     |

## <a name="easv4"></a>Easv4
### <a name="easv4-type1"></a>Easv4-Type1

Easv4-Type1는 AMD의 2.35 GHz EPYC™ 7452 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 96 vCPUs 및 672 GiB RAM을 제공 합니다. Easv4-Type1 [Easv4 시리즈](eav4-easv4-series.md#easv4-series) vm을 실행 합니다.

다음 압축 구성은 Easv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672GiB       | E2as v4  | 32    |
|                |                 |               | E4as v4  | 21    |
|                |                 |               | E8as v4  | 10    |
|                |                 |               | E16as v4 | 5     |
|                |                 |               | E20as v4 | 4     |
|                |                 |               | E32as v4 | 2     |
|                |                 |               | E48as v4 | 1     |
|                |                 |               | E64as v4 | 1     |
|                |                 |               | E96as v4 | 1     |

## <a name="edsv4"></a>Edsv4
### <a name="edsv4-type1"></a>Edsv4-Type1

Edsv4-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 64 vCPUs 및 504 GiB RAM을 제공 합니다. Edsv4-Type1 [Edsv4 시리즈](edv4-edsv4-series.md#edsv4-series) vm을 실행 합니다.

다음 압축 구성은 Edsv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 52             | 64              | 504GiB       | E2ds v4  | 31    |
|                |                 |               | E4ds v4  | 15    |
|                |                 |               | E8ds v4  | 7     |
|                |                 |               | E16ds v4 | 3     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 1     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

## <a name="esv4"></a>Esv4
### <a name="esv4-type1"></a>Esv4-Type1

Esv4-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 80 vCPUs 및 504 GiB RAM을 제공 합니다. Esv4-Type1 [Esv4 시리즈](ev4-esv4-series.md#esv4-series) vm을 실행 합니다.

다음 압축 구성은 Esv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504GiB       | E2s v4  | 31    |
|                |                 |               | E4s v4  | 15    |
|                |                 |               | E8s v4  | 7     |
|                |                 |               | E16s v4 | 3     |
|                |                 |               | E20s v4 | 3     |
|                |                 |               | E32s v4 | 1     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

## <a name="esv3"></a>Esv3
### <a name="esv3-type1"></a>Esv3-Type1

Esv3-Type1는 Intel® Broadwell (2.3 GHz Xeon® E5-2673 v4) 프로세서를 활용 하는 전용 호스트 SKU입니다. 40 물리적 코어, 64 vCPUs 및 448 GiB RAM을 제공 합니다. Esv3-Type1 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) vm을 실행 합니다.

다음 압축 구성은 Esv3-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 448GiB       | E2s v3  | 28    |
|                |                 |               | E4s v3  | 14    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 2     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type2"></a>Esv3-Type2

Esv3-Type2는 Intel® Skylake (Xeon® 8171M) 프로세서를 활용 하는 전용 호스트 SKU입니다. 48 물리적 코어, 78 vCPUs 및 504 GiB RAM을 제공 합니다. Esv3-Type2 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) vm을 실행 합니다.

다음 압축 구성은 Esv3-Type2 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type3"></a>Esv3-Type3

Esv3-Type3는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8272CL) 프로세서를 활용 하는 전용 호스트 SKU입니다. 52 물리적 코어, 80 vCPUs 및 504 GiB RAM을 제공 합니다. Esv3-Type3 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) vm을 실행 합니다.

다음 압축 구성은 Esv3-Type3 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

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

## <a name="fxmds"></a>FXmds
### <a name="fxmds-type1"></a>FXmds-Type1

FXmds-Type1는 Intel® 케스케이드 Lake (Xeon® 골드 6246R) 프로세서를 활용 하는 전용 호스트 SKU입니다. 32 물리적 코어, 48 vCPUs 및 1152 GiB RAM을 제공 합니다. FXmds-Type1는 [FX 시리즈](fx-series.md) vm을 실행 합니다.

다음 압축 구성은 FXmds-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 32             | 48              | 1152 GiB     | FX4mds  | 12    |
|                |                 |               | FX12mds | 4     |
|                |                 |               | FX24mds | 2     |
|                |                 |               | FX36mds | 1     |
|                |                 |               | FX48mds | 1     |

## <a name="lsv2"></a>Lsv2
### <a name="lsv2-type1"></a>Lsv2-Type1

Lsv2-Type1는 Intel® 케스케이드 Lake (Xeon® 골드 6246R) 프로세서를 활용 하는 전용 호스트 SKU입니다. 64 물리적 코어, 80 vCPUs 및 640 GiB RAM을 제공 합니다. Lsv2-Type1 [Lsv2 시리즈](lsv2-series.md) vm을 실행 합니다.

다음 압축 구성은 Lsv2-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 80              | 640 GiB       | L8s v2  | 10    |
|                |                 |               | L16s v2 | 5     |
|                |                 |               | L32s v2 | 2     |
|                |                 |               | L48s v2 | 1     |
|                |                 |               | L64s v2 | 1     |
|                |                 |               | L80s v2 | 1     |

## <a name="m"></a>M
### <a name="ms-type1"></a>Ms-Type1

Ms-Type1 Intel® Cascade Lake(Xeon® 8280) 프로세서를 활용하는 Dedicated Host SKU입니다. 112개의 실제 코어, 128개의 vC CPU 및 2,048 GiB RAM을 제공합니다. Ms-Type1 [M,](m-series.md) Mls, Ms 및 Mts VM을 포함한 M 시리즈 VM을 실행합니다.

다음 Packing 구성에서는 Ms-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 112            | 128             | 2,048GiB     | M32ls   | 4     |
|                |                 |               | M32ts   | 4     |
|                |                 |               | M64     | 2     | 
|                |                 |               | M64s    | 2     |
|                |                 |               | M64ls   | 2     |
|                |                 |               | M128    | 1     | 
|                |                 |               | M128s   | 1     |

### <a name="msm-type1"></a>Msm-Type1

Msm-Type1 Intel® Cascade Lake(Xeon® 8280) 프로세서를 활용하는 Dedicated Host SKU입니다. 112개의 실제 코어, 128개의 vC CPU 및 3,892 GiB RAM을 제공합니다. Msm-Type1 [Ms,](m-series.md) Mms, Mts 및 Mls VM을 포함한 M 시리즈 VM을 실행합니다.

다음 Packing 구성에서는 Msm-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기   | VM 수 |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 128             | 3,892GiB     | M8ms      | 16    |
|                |                 |               | M8-2ms    | 16    |
|                |                 |               | M8-4ms    | 16    |
|                |                 |               | M16ms     | 8     |
|                |                 |               | M16-4ms   | 8     |
|                |                 |               | M16-8ms   | 8     |
|                |                 |               | M32ts     | 4     |
|                |                 |               | M32ls     | 4     |
|                |                 |               | M32ms     | 4     |
|                |                 |               | M32-8ms   | 4     |
|                |                 |               | M32-16ms  | 4     |
|                |                 |               | M64ms     | 2     |
|                |                 |               | M64       | 2     | 
|                |                 |               | M64s      | 2     |
|                |                 |               | M64m      | 2     |
|                |                 |               | M64ls     | 2     |
|                |                 |               | M64-16ms  | 2     |
|                |                 |               | M64-32ms  | 2     |
|                |                 |               | M128ms    | 1     |
|                |                 |               | M128s     | 1     |
|                |                 |               | M128m     | 1     |
|                |                 |               | M128      | 1     | 
|                |                 |               | M128-32ms | 1     |
|                |                 |               | M128-64ms | 1     |

## <a name="mv2"></a>Mv2
### <a name="msmv2-type1"></a>Msmv2-Type1

Msm-Type1는 Intel® Skylake (Xeon® 플래티넘 8180M) 프로세서를 활용 하는 전용 호스트 SKU입니다. 224 물리적 코어, 416 vCPUs 및 11400 GiB RAM을 제공 합니다. Msmv2-Type1는 Msv2 및 Mmsv2 Vm을 포함 하 여 [Mv2 시리즈](mv2-series.md) vm을 실행 합니다.

다음 압축 구성은 Msm-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기       | VM 수 |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 11,400GiB    | M208ms v2     | 2     |
|                |                 |               | M208s v2     | 2     |
|                |                 |               | M416-208ms v2 | 1     | 
|                |                 |               | M416-208s v2  | 1     | 
|                |                 |               | M416ms v2     | 1     |  
|                |                 |               | M416s v2      | 1     |  

### <a name="msv2-type1"></a>Msv2-Type1

Msv2-Type1는 Intel® Skylake (Xeon® 플래티넘 8180M) 프로세서를 활용 하는 전용 호스트 SKU입니다. 224 물리적 코어, 416 vCPUs 및 5700 GiB RAM을 제공 합니다. Msv2-Type1는 Msv2 및 Mmsv2 Vm을 포함 하 여 [Mv2 시리즈](mv2-series.md) vm을 실행 합니다.

다음 압축 구성은 Msv2-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기       | VM 수 |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 5,700GiB     | M208ms v2     | 2     |
|                |                 |               | M208s v2      | 1     |
|                |                 |               | M416-208s v2  | 1     |
|                |                 |               | M416s v2      | 1     |

## <a name="msv2"></a>Msv2
### <a name="mmsv2medmem-type1"></a>Mmsv2MedMem-Type1
Mmsv2MedMem-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8280) 프로세서를 활용 하는 전용 호스트 SKU입니다. 112 물리적 코어, 192 vCPUs 및 4096 GiB RAM을 제공 합니다. Mmsv2MedMem-Type1는 Msv2 및 Mmsv2 Vm을 포함 하 여 [Msv2 시리즈](msv2-mdsv2-series.md) vm을 실행 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기   | VM 수 |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 4,096GiB     | M32ms v2  | 4     |
|                |                 |               | M64s v2   | 3     |
|                |                 |               | M64ms v2  | 2     |
|                |                 |               | M128ms v2 | 1     |
|                |                 |               | M128s v2  | 1     |

### <a name="msv2medmem-type1"></a>Msv2MedMem-Type1
Msv2MedMem-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8280) 프로세서를 활용 하는 전용 호스트 SKU입니다. 112 물리적 코어, 192 vCPUs 및 2048 GiB RAM을 제공 합니다. Msv2MedMem-Type1는 Msv2 및 Mmsv2 Vm을 포함 하 여 [Msv2 시리즈](msv2-mdsv2-series.md) vm을 실행 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기    | VM 수 |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 2,048GiB     | M32ms v2   | 2     |
|                |                 |               | M64s v2    | 2     |
|                |                 |               | M64ms v2   | 1     |
|                |                 |               | M128s v2   | 1     |

## <a name="mdsv2"></a>Mdsv2
### <a name="mdmsv2medmem-type1"></a>Mdmsv2MedMem-Type1
Mdmsv2MedMem-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8280) 프로세서를 활용 하는 전용 호스트 SKU입니다. 112 물리적 코어, 192 vCPUs 및 4096 GiB RAM을 제공 합니다. Mdmsv2MedMem-Type1는 Mdsv2 및 Mdmsv2 Vm을 포함 하 여 [Msv2 시리즈](msv2-mdsv2-series.md) vm을 실행 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기    | VM 수 |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 4,096GiB     | M32dms v2  | 4     |
|                |                 |               | M64ds v2   | 2     |
|                |                 |               | M64dms v2  | 2     |
|                |                 |               | M128ds v2  | 1     |
|                |                 |               | M128dms v2 | 1     |

### <a name="mdsv2medmem-type1"></a>Mdsv2MedMem-Type1
Mdsv2MedMem-Type1는 Intel® 케스케이드 Lake (Xeon® 플래티넘 8280) 프로세서를 활용 하는 전용 호스트 SKU입니다. 112 물리적 코어, 192 vCPUs 및 2048 GiB RAM을 제공 합니다. Mdsv2MedMem-Type1는 Mdsv2 및 Mdmsv2 Vm을 포함 하 여 [Msv2 시리즈](msv2-mdsv2-series.md) vm을 실행 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기   | VM 수 |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 2,048GiB     | M32dms v2 | 2     |
|                |                 |               | M64ds v2  | 2     |
|                |                 |               | M64dms v2 | 1     |
|                |                 |               | M128ds v2 | 1     |

## <a name="nvasv4"></a>NVasv4
### <a name="nvasv4-type1"></a>NVasv4-Type1

NVasv4-Type1 amd Radeon 이러한 MI25 Gpu를 사용 하 여 AMD® 로마 (EPYC™ 7V12) 프로세서를 활용 하는 전용 호스트 SKU입니다. 128 물리적 코어, 128 vCPUs 및 448 GiB RAM을 제공 합니다. NVasv4-Type1 [NVsv4 시리즈](nvv4-series.md) vm을 실행 합니다.

다음 압축 구성은 NVasv4-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기   | VM 수 |
|----------------|-----------------|---------------|-----------|-------|
| 128            | 128             | 448GiB       | NV4as v4  | 30    |
|                |                 |               | NV8as v4  | 15    |
|                |                 |               | NV16as v4 | 7     |
|                |                 |               | NV32as v4 | 3     |

## <a name="nvsv3"></a>NVsv3
### <a name="nvsv3-type1"></a>NVsv3-Type1

NVsv3-Type1는 NVDIDIA Tesla M60 Gpu 및 NVIDIA GRID 기술을 사용 하는 Intel® Broadwell (E5-2690 v4) 프로세서를 활용 하는 전용 호스트 SKU입니다. 28 개의 물리적 코어, 48 vCPUs 및 448 GiB RAM을 제공 합니다. NVsv3-Type1 [NVv3 시리즈](nvv3-series.md) vm을 실행 합니다.

다음 압축 구성은 NVsv3-Type1 호스트에 배치할 수 있는 일관 된 Vm의 최대 압축을 간략하게 설명 합니다.

| 실제 코어 | 사용 가능한 vCPUs | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 28             | 48              | 448GiB       | NV12s v3 | 4     |
|                |                 |               | NV24s v3 | 2     |
|                |                 |               | NV48s v3 | 1     | 


## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 극대화하기 위해 영역과 오류 도메인을 모두 사용합니다.