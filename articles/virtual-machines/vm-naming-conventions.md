---
title: Azure VM 크기 명명 규칙
description: Azure VM 크기에 사용되는 명명 규칙을 설명합니다.
ms.service: virtual-machines
subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.date: 7/22/2020
ms.author: mimckitt
ms.custom: sttsinar
ms.openlocfilehash: 931a80078c4e808ac394bdd226fb972807ed4563
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697243"
---
# <a name="azure-virtual-machine-sizes-naming-conventions"></a>Azure Virtual Machine 크기 명명 규칙

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

이 페이지에서는 Azure VM에 사용되는 명명 규칙을 설명합니다. VM은 이러한 명명 규칙을 사용하여 다양한 기능과 사양을 나타냅니다.

## <a name="naming-convention-explanation"></a>명명 규칙 설명

**[제품군]**  +  **[하위 제품군*]**  +  **[vCPU 수]**  +  **[제한된 vCPU*]**  +  **[추가 기능]**  +  **[액셀러레이터 유형*]**  +  **[버전]**

|값 | 설명|
|---|---|
| 패밀리 | VM 제품군 시리즈를 나타냅니다.| 
| \* 하위 제품군 | 특수한 VM 차별화에만 사용됩니다.|
| vCPU 수| VM의 vCPU 수를 나타냅니다. |
| *제한된 vCPU| 특정 VM 크기에만 사용됩니다. [제한된 vCPU 지원 크기](./constrained-vcpu.md)에 대한 vCPU 수를 나타냅니다. |
| 추가 기능 | 하나 이상의 소문자가 다음과 같은 추가 기능을 나타냅니다. <br> a = AMD 기반 프로세서 <br> d = 최신 Azure VM에 대한 diskfull(로컬 임시 디스크가 있음). [Ddv4 및 Ddsv4 시리즈](./ddv4-ddsv4-series.md)를 참조하세요. <br> i = 격리 크기 <br> l = 메모리 부족. 메모리 집약적 크기보다 적은 양의 메모리 <br> m = 메모리 집약적. 특정 크기에서 가장 많은 양의 메모리 <br> t = 적은 메모리. 특정 크기의 가장 적은 양의 메모리 <br> s = [Ultra SSD](./disks-types.md#ultra-disk) 사용을 비롯한 Premium Storage 지원(참고: s 특성이 없는 최신 크기의 일부는 여전히 Premium Storage를 지원할 수 있음(예: M128, M64 등))<br> |
| *가속기 유형 | 특수/GPU SKU의 하드웨어 가속기 유형을 나타냅니다. 2020년 3분기부터 출시된 새로운 특수/GPU SKU에만 이름에 하드웨어 가속기가 있습니다. |
| Version | VM 제품군 시리즈의 버전을 나타냅니다. |

## <a name="example-breakdown"></a>예시 분석

**[제품군]**  +  **[하위 제품군*]**  +  **[vCPU 수]**  +  **[추가 기능]**  +  **[엑셀러레이터 유형*]**  +  **[버전]**

### <a name="example-1-m416ms_v2"></a>예 1: M416ms_v2

|값 | 설명|
|---|---|
| 패밀리 | M | 
| vCPU 수 | 416 |
| 추가 기능 | m = 메모리 집약적 <br> s = Premium Storage 가능 |
| Version | v2 |

### <a name="example-2-nv16as_v4"></a>예 2: NV16as_v4

|값 | 설명|
|---|---|
| 패밀리 | N | 
| 하위 제품군 | V |
| vCPU 수 | 16 |
| 추가 기능 | a = AMD 기반 프로세서 <br> s = Premium Storage 가능 |
| Version | v4 |

### <a name="example-3-nc4as_t4_v3"></a>예 3: NC4as_T4_v3

|값 | 설명|
|---|---|
| 패밀리 | N | 
| 하위 제품군 | C |
| vCPU 수 | 4 |
| 추가 기능 | a = AMD 기반 프로세서 <br> s = Premium Storage 가능 |
| 가속기 유형 | T4 |
| Version | v3 |

### <a name="example-4-m8-2ms_v2-constrained-vcpu"></a>예 4: M8-2ms_v2(제약이 있는 vCPU)

|값 | 설명|
|---|---|
| 패밀리 | M | 
| vCPU 수 | 8 |
| 제한이 있는 실제 vCPU 수 | 2 |
| 추가 기능 | m = 메모리 집약적 <br> s = Premium Storage 가능 |
| Version | v2 |

## <a name="next-steps"></a>다음 단계

Azure에서 사용 가능한 [VM 크기](./sizes.md)에 대해 자세히 알아봅니다.
