---
title: 최적화된 Azure Dedicated Host S SKU Storage
description: Storage 최적화된 ADH S SKU의 VM 패킹에 대한 사양입니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: ad313c0d11a2d8b8948cbf439fa6a175a0cf3d5a
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440604"
---
# <a name="storage-optimized-azure-dedicated-host-skus"></a>최적화된 Azure Dedicated Host S SKU Storage
Azure Dedicated Host S SKU는 VM 제품군과 특정 하드웨어 사양의 조합입니다. Dedicated Host SKU에서 지정하는 VM 시리즈의 VM만 배포할 수 있습니다. 예를 들어 Dsv3-Type3에서는 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) VM만 프로비전할 수 있습니다. 

이 문서에서는 모든 스토리지 최적화 Dedicated Host S SKU에 대한 하드웨어 사양 및 VM Packing을 설명합니다.

## <a name="limitations"></a>제한 사항

전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

## <a name="lsv2"></a>Lsv2
### <a name="lsv2-type1"></a>Lsv2-Type1

Lsv2-Type1 Intel® Cascade Lake(Xeon® Gold 6246R) 프로세서를 활용하는 Dedicated Host SKU입니다. 64개의 실제 코어, 80개의 vC CPU 및 640 GiB RAM을 제공합니다. Lsv2-Type1 [Lsv2 시리즈](lsv2-series.md) VM을 실행합니다.

다음 Packing 구성에서는 Lsv2-Type1 호스트에 배치할 수 있는 균일한 VM의 최대 패킹을 간략하게 설명합니다.

| 물리적 코어 | 사용 가능한 vC CPU | 사용 가능한 RAM | VM 크기 | VM 수 |
|----------------|-----------------|---------------|---------|-------|
| 64             | 80              | 640 GiB       | L8s v2  | 10    |
|                |                 |               | L16s v2 | 5     |
|                |                 |               | L32s v2 | 2     |
|                |                 |               | L48s v2 | 1     |
|                |                 |               | L64s v2 | 1     |
|                |                 |               | L80s v2 | 1     |

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [전용 호스트](dedicated-hosts.md) 개요를 참조하세요.

- [Azure 빠른 시작 템플릿](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md)에서 사용할 수 있는 샘플 템플릿이 있습니다. 이 템플릿은 지역의 복원력을 극대화하기 위해 영역과 오류 도메인을 모두 사용합니다.
