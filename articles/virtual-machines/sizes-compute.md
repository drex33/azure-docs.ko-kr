---
title: Azure VM 크기 - 컴퓨팅 최적화 | Microsoft Docs
description: Azure의 가상 머신에 사용할 수 있는 다양한 컴퓨팅 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-compute
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: mimckitt
ms.openlocfilehash: edc1dbd3d0c2250bf2dd4db870a9813602477073
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688648"
---
# <a name="compute-optimized-virtual-machine-sizes"></a>컴퓨팅 최적화 가상 머신 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

컴퓨팅 최적화 VM 크기는 CPU 대 메모리 비율이 높습니다. 해당 크기는 트래픽이 중간 정도인 웹 서버, 네트워크 어플라이언스, 일괄 처리 프로세스, 애플리케이션 서버에 적합합니다. 이 문서에서는 vCPU, 데이터 디스크, NIC의 개수에 대한 정보를 제공합니다. 또한 이 그룹의 각 크기에 대한 스토리지 처리량 및 네트워크 대역폭에 대한 정보도 제공합니다.

- [Fsv2 시리즈](fsv2-series.md)는 2세대 Intel® Xeon® 플래티넘 8272CL(Cascade Lake) 프로세서 및 Intel® Xeon® 플래티넘 8168(Skylake) 프로세서에서 실행됩니다. 모든 코어 터보 클럭 속도가 3.4GHz이고 최대 싱글 코어 터보 주파수는 3.7GHz입니다. Intel® AVX-512 지침은 Intel Scalable Processor에 새로 있습니다. 지침은 단일 및 이중 정밀 부동 소수점 작업에서 워크로드를 벡터 처리할 때의 성능을 최대 2배로 끌어올립니다. 즉, 계산 워크로드를 빠르게 처리합니다. 시간당 가격이 더 낮은 Fsv2 시리즈는 vCPU당 ACU(Azure 컴퓨팅 단위)를 기준으로 하는 Azure 포트폴리오에서 가격 대비 성능이 가장 좋습니다.

- [FX 시리즈](fx-series.md)는 Intel® Xeon® Gold 6246R(Cascade Lake) 프로세서에서 실행됩니다. 4\.0GHz의 올코어 터보 주파수, vCPU당 21GB RAM, 최대 1TB의 총 RAM 및 로컬 임시 저장소를 제공합니다. FX 시리즈는 높은 CPU 클록 속도와 높은 메모리 대 CPU 비율이 필요한 워크로드, 코어당 라이선스 비용이 높은 워크로드 및 높은 단일 코어 성능이 필요한 애플리케이션에 유용합니다. FX 시리즈의 일반적인 사용 사례는 EDA(전자 디자인 자동화) 워크로드입니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.

Azure가 VM의 이름을 지정하는 방법에 대한 자세한 내용은 [Azure 가상 머신 크기 명명 규칙](./vm-naming-conventions.md)을 참조하세요.
