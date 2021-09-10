---
title: Azure VM 크기 - GPU | Microsoft Docs
description: Azure의 가상 머신에 사용할 수 있는 다양한 GPU 최적화 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 1114d288a506d1b5123f16ab7a24ab14b585638d
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538427"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>GPU 최적화 가상 머신 크기

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

GPU 최적화 VM 크기는 단일, 여러 또는 일부 GPU에서 사용 가능한 특수한 가상 머신입니다. 이러한 크기는 계산 집약적이며 그래픽 집약적인 시각화 워크로드용으로 설계되었습니다. 이 문서에서는 GPU, vCPU, 데이터 디스크 및 NIC의 개수와 종류에 대한 정보를 제공합니다. 이 그룹화의 각 크기에 대해 스토리지 처리량 및 네트워크 대역폭도 포함되어 있습니다.

- [NCv3 시리즈](ncv3-series.md) 및 [NC T4_v3 시리즈](nct4-v3-series.md) 크기는 컴퓨팅 집약적인 GPU 가속 애플리케이션에 최적화되어 있습니다. CUDA 기반 및 OpenCL 기반 애플리케이션, 시뮬레이션, AI, 딥 러닝 등을 예로 들 수 있습니다. NC T4 v3 시리즈는 NVIDIA의 Tesla T4 GPU 및 AMD EPYC2 Rome 프로세서를 갖춘 유추 작업에 중점을 둡니다. NCv3 시리즈는 NVIDIA의 Tesla V100 GPU를 지원하는 고성능 컴퓨팅과 AI 워크로드에 중점을 둡니다.

- [ND A100 v4 시리즈](nda100-v4-series.md) 크기는 딥 러닝 학습 및 가속화된 HPC 애플리케이션을 강화하고 스케일 아웃하는 데 중점을 두고 있습니다. ND A100 v4 시리즈는 각각 200기가비트 Mellanox InfiniBand HDR 연결 및 40GB GPU 메모리와 함께 사용할 수 있는 8개의 NVIDIA A100 TensorCore GPU를 사용합니다.

- [NV 시리즈](nv-series.md) 및 [NVv3 시리즈](nvv3-series.md) 크기는 OpenGL 및 DirectX와 같은 프레임워크를 사용하는 원격 시각화, 스트리밍, 게임, 인코딩 및 VDI 시나리오에 맞게 최적화되고 설계되었습니다. 이러한 VM은 NVIDIA Tesla M60 GPU의 지원을 받습니다.

- [NVv4 시리즈](nvv4-series.md) VDI 및 원격 가상화를 위해 최적화되고 설계된 VM 크기입니다. 파티셔닝된 GPU를 사용하는 NVv4는 작은 GPU 리소스가 필요한 워크로드에 적합한 크기를 제공합니다. 이러한 VM은 AMD Radeon Instinct MI25 GPU에서 지원됩니다. NVv4 VM은 현재 Windows 게스트 운영 체제만 지원합니다.

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA 또는 AMD GPU 드라이버를 설치해야 합니다.

- NVIDIA GPU가 지원하는 VM의 경우 [NVIDIA GPU 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 적절한 NVIDIA CUDA 또는 GRID 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. 지원되는 운영 체제 및 배포 단계는 [NVIDIA GPU 드라이버 확장 설명서](./extensions/hpccompute-gpu-windows.md)를 참조하세요. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

   또는 NVIDIA GPU 드라이버를 수동으로 설치할 수 있습니다. 지원되는 운영 체제, 드라이버, 설치 및 확인 단계는 [Windows를 실행하는 N 시리즈 VM에 NVIDIA GPU 드라이버 설치](./windows/n-series-driver-setup.md) 또는 [Linux를 실행하는 N 시리즈 VM에 NVIDIA GPU 드라이버 설치](./linux/n-series-driver-setup.md)를 참조하세요.

- AMD GPU에서 지원하는 VM의 경우 [AMD GPU 드라이버 확장](./extensions/hpccompute-amd-gpu-windows.md)이 적절한 AMD 드라이버를 설치합니다. Azure PowerShell 또는 Azure Resource Manager 템플릿과 같은 도구나 Azure Portal을 사용하여 확장을 설치 또는 관리합니다. VM 확장에 대한 일반적인 내용은 [Azure 가상 머신 확장 및 기능](./extensions/overview.md)을 참조하세요.

   또는 AMD GPU 드라이버를 수동으로 설치할 수 있습니다. 지원되는 운영 체제, 드라이버, 설치 및 확인 단계는 [Windows를 실행하는 N 시리즈 VM에 AMD GPU 드라이버 설치](./windows/n-series-amd-driver-setup.md)를 참조하세요.

## <a name="deployment-considerations"></a>배포 고려 사항

- N 시리즈 VM의 가용성에 대해서는 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

- N 시리즈 VM은 Resource Manager 배포 모델에만 배포할 수 있습니다.

- N 시리즈 VM은 디스크에 지원되는 Azure Storage의 유형이 다양합니다. NC 및 NV VM은 표준 디스크 스토리지(HDD)로 백업되는 VM 디스크만 지원합니다. 다른 모든 GPU VM은 표준 디스크 스토리지 및 SSD(프리미엄 디스크 스토리지)에서 지원하는 VM 디스크를 지원합니다.

- 몇 개의 N 시리즈 VM을 배포하려는 경우 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- Azure 구독에서 코어 할당량(지역당)을 늘리고 NC, NCv2, NCv3, ND, NDv2, NV 또는 NVv2 코어에 대한 별도의 할당량을 늘려야 할 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-portal/supportability/how-to-create-azure-support-request.md) 합니다. 기본 제한은 구독 범주에 따라 달라질 수 있습니다.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
