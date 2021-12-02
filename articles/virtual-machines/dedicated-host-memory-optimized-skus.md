---
title: 메모리 최적화 Azure Dedicated Host S SKU
description: 메모리 최적화 ADH S SKU의 VM 패킹에 대한 사양입니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: c85e5698e98414ecc44511bb7f132e202340920e
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440605"
---
# <a name="memory-optimized-azure-dedicated-host-skus"></a>메모리 최적화 Azure Dedicated Host S SKU
Azure Dedicated Host S SKU는 VM 제품군과 특정 하드웨어 사양의 조합입니다. Dedicated Host SKU에서 지정하는 VM 시리즈의 VM만 배포할 수 있습니다. 예를 들어 Dsv3-Type3에서는 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) VM만 프로비전할 수 있습니다. 

이 문서에서는 모든 메모리 최적화 Dedicated Host S CPU에 대한 하드웨어 사양 및 VM Packing을 설명합니다.

## <a name="limitations"></a>제한 사항

전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="easv4"></a>Easv4
### <a name="easv4-type1"></a>Easv4-Type1

Easv4-Type1 AMD의 2.35GHz EPYC™ 7452 프로세서를 활용하는 Dedicated Host SKU입니다. 64개의 실제 코어, 96개의 vC CPU 및 672 GiB RAM을 제공합니다. Easv4-Type1 [Easv4 시리즈](eav4-easv4-series.md#easv4-series) VM을 실행합니다.

다음 Packing 구성에서는 Easv4-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기  | VM 수 |
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

### <a name="easv4-type2"></a>Easv4-Type2

Easv4-Type2 AMD의 2.35GHz EPYC™ 7452 프로세서를 활용하는 Dedicated Host SKU입니다. 64개의 실제 코어, 96개의 vC CPU 및 768 GiB RAM을 제공합니다. Easv4-Type2 [Easv4 시리즈](eav4-easv4-series.md#easv4-series) VM을 실행합니다.

다음 Packing 구성에서는 Easv4-Type2 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 768 GiB       | E2as v4  | 32    |
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

Edsv4-Type1 Intel® Cascade Lake(Xeon® 8272CL) 프로세서를 활용하는 Dedicated Host SKU입니다. 52개의 실제 코어, 64개의 vC CPU 및 504 GiB RAM을 제공합니다. Edsv4-Type1 [Edsv4 시리즈](edv4-edsv4-series.md#edsv4-series) VM을 실행합니다.

다음 Packing 구성에서는 Edsv4-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 52             | 64              | 504GiB       | E2ds v4  | 31    |
|                |                 |               | E4ds v4  | 15    |
|                |                 |               | E8ds v4  | 7     |
|                |                 |               | E16ds v4 | 3     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 1     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

### <a name="edsv4-type2"></a>Edsv4-Type2

Edsv4-Type2 Intel® Ice Lake(Xeon® 8370C) 프로세서를 활용하는 Dedicated Host SKU입니다. 64개의 실제 코어, 119개의 vC CPU 및 768 GiB RAM을 제공합니다. Edsv4-Type2 [Edsv4 시리즈](edv4-edsv4-series.md#edsv4-series) VM을 실행합니다.

다음 Packing 구성에서는 Edsv4-Type2 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기  | VM 수 |
|----------------|-----------------|---------------|----------|-------|
| 64             | 119             | 768 GiB       | E2ds v4  | 32    |
|                |                 |               | E4ds v4  | 19    |
|                |                 |               | E8ds v4  | 9     |
|                |                 |               | E16ds v4 | 4     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 2     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

## <a name="esv4"></a>Esv4
### <a name="esv4-type1"></a>Esv4-Type1

Esv4-Type1 Intel® Cascade Lake(Xeon® 8272CL) 프로세서를 활용하는 Dedicated Host SKU입니다. 52개의 실제 코어, 80개의 vC CPU 및 504 GiB RAM을 제공합니다. Esv4-Type1 [Esv4 시리즈](ev4-esv4-series.md#esv4-series) VM을 실행합니다.

다음 Packing 구성에서는 Esv4-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504GiB       | E2s v4  | 31    |
|                |                 |               | E4s v4  | 15    |
|                |                 |               | E8s v4  | 7     |
|                |                 |               | E16s v4 | 3     |
|                |                 |               | E20s v4 | 3     |
|                |                 |               | E32s v4 | 1     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

### <a name="esv4-type2"></a>Esv4-Type2

Esv4-Type2 Intel® Ice Lake(Xeon® 8370C) 프로세서를 활용하는 Dedicated Host SKU입니다. 64개의 실제 코어, 119개의 vC CPU 및 768 GiB RAM을 제공합니다. Esv4-Type2 [Esv4 시리즈](ev4-esv4-series.md#esv4-series) VM을 실행합니다.

다음 Packing 구성에서는 Esv4-Type2 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 119             | 768 GiB       | E2s v4  | 32    |
|                |                 |               | E4s v4  | 21    |
|                |                 |               | E8s v4  | 10    |
|                |                 |               | E16s v4 | 5     |
|                |                 |               | E20s v4 | 4     |
|                |                 |               | E32s v4 | 2     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

## <a name="esv3"></a>Esv3
### <a name="esv3-type1"></a>Esv3-Type1

Esv3-Type1 Intel® Broadwell(2.3GHz Xeon® E5-2673 v4) 프로세서를 활용하는 Dedicated Host SKU입니다. 40개의 실제 코어, 64개의 vC CPU 및 448 GiB RAM을 제공합니다. Esv3-Type1 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) VM을 실행합니다.

다음 Packing 구성에서는 Esv3-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
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

Esv3-Type2 Intel® Skylake(Xeon® 8171M) 프로세서를 활용하는 Dedicated Host SKU입니다. 48개의 실제 코어, 78개의 vC CPU 및 504 GiB RAM을 제공합니다. Esv3-Type2 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) VM을 실행합니다.

다음 Packing 구성에서는 Esv3-Type2 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
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

Esv3-Type3 Intel® Cascade Lake(Xeon® 8272CL) 프로세서를 활용하는 Dedicated Host SKU입니다. 52개의 실제 코어, 80개의 vC CPU 및 504 GiB RAM을 제공합니다. Esv3-Type3 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) VM을 실행합니다.

다음 Packing 구성에서는 Esv3-Type3 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type4"></a>Esv3-Type4

Esv3-Type4 Intel® Ice Lake(Xeon® 8370C) 프로세서를 활용하는 Dedicated Host SKU입니다. 64개의 실제 코어, 119개의 vC CPU 및 768 GiB RAM을 제공합니다. Esv3-Type4 [Esv3 시리즈](ev3-esv3-series.md#ev3-series) VM을 실행합니다.

다음 Packing 구성에서는 Esv3-Type4 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 119             | 768 GiB       | E2s v3  | 32    |
|                |                 |               | E4s v3  | 21    |
|                |                 |               | E8s v3  | 10    |
|                |                 |               | E16s v3 | 5     |
|                |                 |               | E20s v3 | 4     |
|                |                 |               | E32s v3 | 2     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

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

Msm-Type1 Intel® Cascade Lake(Xeon® 8280) 프로세서를 활용하는 Dedicated Host SKU입니다. 112개의 실제 코어, 128개의 vC CPU 및 3,892 GiB RAM을 제공합니다. Msm-Type1 Ms, Mms, [Mts](m-series.md) 및 Mls VM을 포함한 M 시리즈 VM을 실행합니다.

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

Msm-Type1 Intel® Skylake(Xeon® 8180M) 프로세서를 활용하는 Dedicated Host SKU입니다. 224 물리적 코어, 416 vCPUs 및 11400 GiB RAM을 제공 합니다. Msmv2-Type1는 Msv2 및 Mmsv2 Vm을 포함 하 여 [Mv2 시리즈](mv2-series.md) vm을 실행 합니다.

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

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 극대화하기 위해 영역과 오류 도메인을 모두 사용합니다.
