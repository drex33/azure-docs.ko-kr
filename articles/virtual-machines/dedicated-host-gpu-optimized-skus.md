---
title: GPU에 최적화 된 Azure 전용 호스트 Sku
description: GPU에 최적화 된 ADH Sku의 VM 압축 사양입니다.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0caa5d9e0410048fe767f0b94e2f0fabc270696a
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440601"
---
# <a name="gpu-optimized-azure-dedicated-host-skus"></a>GPU에 최적화 된 Azure 전용 호스트 Sku
Azure 전용 호스트 Sku는 VM 제품군 및 특정 하드웨어 사양의 조합입니다. 전용 호스트 SKU에서 지정 하는 VM 시리즈의 Vm만 배포할 수 있습니다. 예를 들어 Dsv3-3에서 [Dsv3 시리즈](dv3-dsv3-series.md#dsv3-series) vm만 프로 비전 할 수 있습니다. 

이 문서에서는 모든 GPU에 최적화 된 전용 호스트 Sku에 대 한 하드웨어 사양 및 VM 패키지를 안내 합니다.

## <a name="limitations"></a>제한 사항

전용 호스트에 사용할 수 있는 크기 및 하드웨어 유형은 지역에 따라 다릅니다. 자세히 알아보려면 호스트 [가격 책정 페이지](https://aka.ms/ADHPricing)를 참조하세요.

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
