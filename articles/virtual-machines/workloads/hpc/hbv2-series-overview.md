---
title: HBv2 시리즈 VM 개요 - Azure Virtual Machines | Microsoft Docs
description: Azure의 HBv2 시리즈 VM 크기에 대해 알아봅니다.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 436a4ab3de748b19bd2cad5ae171a754bf9dc955
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132312004"
---
# <a name="hbv2-series-virtual-machine-overview"></a>HBv2 시리즈 가상 머신 개요 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

AMD EPYC에서 HPC(고성능 컴퓨팅) 애플리케이션 성능을 최대화하려면 신중한 접근 방식의 메모리 지역성과 프로세스 배치를 신중하게 설정해야 합니다. 아래에서는 AMD EPYC 아키텍처와 Azure에서 HPC 애플리케이션을 위해 이를 구현하는 방법에 대해 간략히 설명합니다. **pNUMA** 라는 용어는 물리적 NUMA 도메인을 의미하고 **vNUMA** 는 가상화된 NUMA 도메인을 의미합니다. 

물리적으로, [HBv2 시리즈](../../hbv2-series.md) 서버 1개는 총 128개의 물리적 코어를 위한 64코어 EPYC 7742 CPU 2개로 구성됩니다. 128개의 코어는 32개의 pNUMA 도메인(소켓당 16개)으로 나뉘고, 각 도메인은 4개의 코어이며 AMD에서는 **코어 복합체**(또는 **CCX**)로 알려져 있습니다. 각 CCX에는 자체 L3 캐시가 있으며, 이를 통해 OS가 pNUMA/vNUMA 경계를 파악합니다. 4개의 인접 CCX는 물리적 DRAM의 2개 채널에 대한 액세스를 공유합니다. 

Azure 하이퍼바이저가 VM을 방해하지 않고 작동할 수 있는 공간을 제공하기 위해 물리적 pNUMA 도메인 0 및 16(즉, 각 CPU 소켓의 첫 번째 CCX)을 예약합니다. 나머지 30개의 pNUMA 도메인은 모두 vNUMA가 되는 시점에 VM에 할당됩니다. 따라서 VM에 다음이 표시됩니다.

VM당 `(30 vNUMA domains) * (4 cores/vNUMA) = 120`개 코어 

VM 자체는 pNUMA 0 및 16이 예약되어 있음을 인식하지 못합니다. 대칭적으로 소켓당 15개의 vNUMA로 0-29를 표시되는 vNUMA를 열거합니다(vSocket 0의 vNUMA 0-14, vSocket 1의 vNUMA 15-29). 다음 섹션에는 이 비대칭 NUMA 레이아웃에서 MPI 애플리케이션을 실행하는 최상의 방법에 대한 지침이 있습니다. 

프로세스 고정은 기본 실리콘을 있는 그대로 게스트 VM에 노출하므로 HBv2 시리즈 VM에서 작동합니다. 최적의 성능과 일관성을 유지하기 위해 이를 고정하는 것이 좋습니다. 


## <a name="hardware-specifications"></a>하드웨어 사양 

| 하드웨어 사양          | HBv2 시리즈 VM                   | 
|----------------------------------|----------------------------------|
| 코어                            | 120(SMT 사용 안 함)               | 
| CPU                              | AMD EPYC 7742                    | 
| CPU 주파수(비 AVX)          | ~3.1GHz(단일 + 모든 코어)    | 
| 메모리                           | 4GB/코어(총 480GB)         | 
| 로컬 디스크                       | 960GB NVMe(블록), 480GB SSD(페이지 파일) | 
| Infiniband                       | 200GB/s EDR Mellanox ConnectX-6 | 
| 네트워크                          | 50GB/s 이더넷(40GB/s 사용 가능) Azure 2세대 SmartNIC | 


## <a name="software-specifications"></a>소프트웨어 사양 

| 소프트웨어 사양     | HBv2 시리즈 VM                                            | 
|-----------------------------|-----------------------------------------------------------|
| 최대 MPI 작업 크기            | 36000개 코어(singlePlacementGroup = true로 설정된 단일 가상 머신 확장 집합의 300개 VM) |
| MPI 지원                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, 플랫폼 MPI  |
| 추가 프레임워크       | UCX, libfabric, PGAS |
| Azureob Storage 지원       | 표준 및 프리미엄 디스크(최대 8개의 디스크) |
| SRIOV RDMA에 대한 OS 지원   | CentOS/RHEL 7.6+, Ubuntu 16.04+, SLES 12 SP4 +, WinServer 2016+  |
| 오케스트레이터 지원        | CycleCloud, 배치, AKS; [클러스터 구성 옵션](../../sizes-hpc.md#cluster-configuration-options)  |

> [!NOTE] 
> Windows Server 2012 R2는 HBv2 및 64개 가상 또는 물리적 코어를 초과하는 기타 VM에서 지원되지 않습니다. 자세한 내용은 [Windows Server의 Hyper-V에서 지원되는 Windows 게스트 운영 체제](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 및 [다중 칩 아키텍처](https://bit.ly/2GpQIMb)에 대해 자세히 알아봅니다. 자세한 내용은 [AMD EPYC 프로세서 HPC 튜닝 가이드](https://bit.ly/2T3AWZ9)를 참조하세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
