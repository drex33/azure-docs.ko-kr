---
title: 필드에서 프로그래밍 가능한 게이트 배열(FPGA)에 대한 Azure 가상 머신 크기
description: Azure의 가상 머신에 사용할 수 있는 다양한 FPGA 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-fpga
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: vikancha
ms.openlocfilehash: c421c30e9a8f117806e9e93b2df5154bd881e7c2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697933"
---
# <a name="fpga-optimized-virtual-machine-sizes"></a>FPGA 최적화 가상 머신 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

FPGA 최적화 VM 크기는 단일 또는 여러 FPGA에서 사용 가능한 특수한 가상 머신입니다. 이러한 크기는 계산 집약적인 작업용으로 설계되었습니다. 이 문서에서는 FPGA, vCPU, 데이터 디스크 및 NIC의 개수와 종류에 대한 정보를 제공합니다. 이 그룹화의 각 크기에 대해 스토리지 처리량 및 네트워크 대역폭도 포함되어 있습니다.

- [NP 시리즈](np-series.md) 크기는 기계 학습 유추, 비디오 트랜스 코딩, 그리고 데이터베이스 검색 및 분석을 비롯한 워크로드에 최적화되어 있습니다. NP 시리즈는 Xilinx U250 가속기에 의해 구동됩니다.


## <a name="deployment-considerations"></a>배포 고려 사항

- N 시리즈 VM의 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

- N 시리즈 VM은 Resource Manager 배포 모델에만 배포할 수 있습니다.

- 몇 개의 N 시리즈 VM을 배포하려는 경우 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- Azure 구독에서 코어 할당량(지역당)을 늘리고 NP 코어에 대한 별도의 할당량을 늘려야 할 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-portal/supportability/how-to-create-azure-support-request.md) 합니다. 기본 제한은 구독 범주에 따라 달라질 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [GPU - 컴퓨팅 가속화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
