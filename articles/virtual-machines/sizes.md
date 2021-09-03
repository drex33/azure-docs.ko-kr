---
title: VM 크기
description: Azure의 가상 머신에 사용할 수 있는 다양한 크기를 나열합니다.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 07/21/2020
ms.author: jushiman
ms.openlocfilehash: 1da0acf71d8f3f806ec42dcb61c4d74127892c27
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567867"
---
# <a name="sizes-for-virtual-machines-in-azure"></a>Azure에서 가상 머신에 대한 크기

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

이 문서에서는 앱 및 워크로드를 실행하는 데 사용할 수 있는 Azure 가상 머신에 대한 크기 및 옵션을 설명합니다. 또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다.또한 이러한 리소스의 사용 계획을 세울 때 알아야 할 배포 고려 사항도 제공합니다. 

:::image type="content" source="media/sizes/azurevmsthumb.jpg" alt-text="VM의 올바른 크기 선택을 위한 YouTube 비디오" link="https://youtu.be/zOSvnJFd3ZM":::

| Type | 크기 | 설명 |
|------|-------|-------------|
| [범용](sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, Dv4, Dsv4, Ddv4, Ddsv4  | CPU 대 메모리 비율이 적당합니다. 테스트 및 개발, 중소 규모 데이터베이스 및 트래픽이 적거나 중간 정도인 웹 서버에 적합합니다. |
| [컴퓨팅 최적화](sizes-compute.md) | F, Fs, Fsv2, FX | CPU 대 메모리 비율이 높습니다. 트래픽이 중간 정도인 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스 및 애플리케이션 서버에 적합합니다. |
| [메모리에 최적화](sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | 메모리 대 CPU 비율이 높습니다. 관계형 데이터베이스 서버, 중대형 캐시 및 메모리 내 분석에 적합합니다.                 |
| [Storage에 최적화](sizes-storage.md) | Lsv2 | 빅 데이터, SQL, NoSQL 데이터베이스, 데이터 웨어하우징, 대형 트랜잭션 데이터베이스에 이상적인 높은 디스크 처리량과 IO.  |
| [GPU](sizes-gpu.md) | NC, NCv2, NCv3, NCasT4_v3, ND, NDv2, NV, NVv3, NVv4 | 고급 그래픽 렌더링 및 비디오 편집뿐만 아니라 딥 러닝을 통한 모델 학습 및 추론(ND)도 목표로 하는 특수화된 가상 머신입니다. 한 개 이상의 GPU를 사용할 수 있습니다. |
| [고성능 컴퓨팅](sizes-hpc.md) | HB, HBv2, HBv3, HC, H | Microsoft의 가장 빠르고 강력한 CPU 가상 머신으로, 필요한 경우 처리량이 높은 네트워크 인터페이스(RDMA)도 제공합니다. |

- 다양한 크기의 가격 책정에 대한 자세한 내용은 [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux) 또는 [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/Windows/#Windows)의 가격 책정 페이지를 참조하세요.
- Azure 지역의 VM 크기 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.
- Azure VM에 대한 일반적인 제한은 [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md)을 참조하세요.
- Azure가 VM의 이름을 지정하는 방법에 대한 자세한 내용은 [Azure 가상 머신 크기 명명 규칙](./vm-naming-conventions.md)을 참조하세요.

## <a name="rest-api"></a>REST API

REST API를 사용하여 VM 크기에 대해 쿼리하는 방법은 다음을 참조하세요.

- [크기 조정에 사용 가능한 가상 머신 크기 나열](/rest/api/compute/virtualmachines/listavailablesizes)
- [구독에 사용 가능한 가상 머신 크기 나열](/rest/api/compute/resourceskus/list)
- [가용성 집합에서 사용 가능한 가상 머신 크기 나열](/rest/api/compute/availabilitysets/listavailablesizes)

## <a name="acu"></a>ACU

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

## <a name="benchmark-scores"></a>벤치마크 점수

[CoreMark 벤치마크 점수](./linux/compute-benchmark-scores.md)를 사용하여 Linux VM의 컴퓨팅 성능에 대해 자세히 알아봅니다.

[SPECInt 벤치마크 점수](./windows/compute-benchmark-scores.md)를 사용하여 Windows VM의 컴퓨팅 성능에 대해 자세히 알아봅니다.

## <a name="manage-costs"></a>비용 관리

[!INCLUDE [cost-management-horizontal](../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>다음 단계

사용할 수 있는 다른 VM 크기에 대한 자세한 정보:

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- 표준, Dv1(D1-4 및 D11-14 v1) 및 A8-A11 시리즈에 대한 [이전 세대](sizes-previous-gen.md) 페이지 참조
