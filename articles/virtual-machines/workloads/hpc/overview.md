---
title: InfiniBand 지원 H 시리즈 및 N 시리즈 VM의 고성능 컴퓨팅 - Azure Virtual Machines
description: HPC에 최적화된 InfiniBand 지원 H 시리즈 및 N 시리즈 VM의 특징과 기능에 대해 알아봅니다.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 2b9f89126f49b75f5e232521f807f6db3ba9d4e9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693392"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>InfiniBand 지원 H 시리즈 및 N 시리즈 VM의 고성능 컴퓨팅

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

Azure의 InfiniBand 지원 H 시리즈 및 N 시리즈 VM은 다양한 실제 HPC 및 AI 워크로드에 최상의 성능, MPI(메시지 전달 인터페이스) 확장성 및 비용 효율성을 제공하도록 설계되었습니다. 이러한 HPC(고성능 컴퓨팅) 최적화 VM은 유체역학, 지구 모델링, 날씨 시뮬레이션 등과 같이 과학 및 엔지니어링 분야의 가장 계산 집약적인 문제를 해결하는 데 사용됩니다.

이 문서에서는 Azure에서 InfiniBand 지원 H 시리즈 및 N 시리즈 VM을 시작하는 방법과 확장이 가능하도록 VM에서 HPC 및 AI 워크로드 구성을 최적화하는 방법을 설명합니다.

## <a name="features-and-capabilities"></a>특징과 기능

InfiniBand 지원 H 시리즈 및 N 시리즈 VM은 최고의 HPC 성능, MPI 확장성 및 HPC 워크로드에 대한 비용 효율성을 제공하도록 설계되었습니다. VM의 특징 및 기능에 대한 자세한 내용은 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM을 참조하세요.

### <a name="rdma-and-infiniband"></a>RDMA 및 InfiniBand

[RDMA 지원](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM은 낮은 대기 시간과 높은 대역폭 InfiniBand 네트워크를 통해 통신합니다. 이러한 상호 연결에 대한 RDMA 기능은 분산 노드 HPC 및 AI 워크로드의 확장성과 성능을 향상하는 데 매우 중요합니다. InfiniBand 지원 H 시리즈 및 N 시리즈 VM은 최적화되고 일관적인 RDMA 성능을 제공하기 위해 지름이 작은 비중단 팻 트리에서 연결됩니다.
InfiniBand 지원 VM에서 InfiniBand를 설정하는 방법에 대한 자세한 내용은 [InfiniBand 사용](enable-infiniband.md)을 참조하세요.

### <a name="message-passing-interface"></a>메시지 전달 인터페이스

SR-IOV 지원 H 시리즈 및 N 시리즈는 거의 모든 MPI 라이브러리 및 버전을 지원합니다. 가장 일반적으로 사용되는 MPI 라이브러리는 Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Platform MPI입니다. 모든 RDMA(원격 직접 메모리 액세스) 동사가 지원됩니다.
지원되는 다양한 MPI 라이브러리 설치 및 최적의 구성에 대한 자세한 내용은 [MPI 설정](setup-mpi.md)을 참조하세요.

## <a name="get-started"></a>시작하기

첫 번째 단계는 VM 사양 및 [RDMA 기능](../../sizes-hpc.md#rdma-capable-instances)에 따라 워크로드에 가장 적합한 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM 유형을 선택하는 것입니다.
두 번째로, InfiniBand를 사용하도록 설정하여 VM을 구성합니다. 최적화된 VM 이미지와 드라이버 굽기를 사용하는 방법을 포함하여 다양한 방법으로 이 작업을 수행할 수 있습니다. 자세한 내용은 [Linux 최적화](configure.md) 및 [InfiniBand 사용](enable-infiniband.md)를 참조하세요.
세 번째로, 분산 노드 워크로드의 경우 MPI를 적절하게 선택하고 구성하는 것이 중요합니다. 자세한 내용은 [MPI 설정](setup-mpi.md)을 참조하세요.
네 번째로, 성능과 확장성을 높일 수 있도록 [HBv3 시리즈 개요](hbv3-series-overview.md), [HC 시리즈 개요](hc-series-overview.md) 등의 VM 제품군과 관련된 지침에 따라 워크로드를 최적으로 구성합니다.

## <a name="next-steps"></a>다음 단계

- InfiniBand 지원 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM의 [구성 및 최적화](configure.md)에 대해 자세히 알아보세요.
- [HBv3 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토하여 성능 및 확장성을 높일 수 있도록 워크로드를 최적으로 구성하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- [Azure에서 HPC 애플리케이션 최적화에 대한 학습 모듈](/learn/modules/optimize-tightly-coupled-hpc-apps/)을 사용하여 지식을 테스트합니다.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.