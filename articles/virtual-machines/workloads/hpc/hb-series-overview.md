---
title: HB 시리즈 VM 개요 - Azure Virtual Machines | Microsoft Docs
description: Azure에서 HB 시리즈 VM 크기에 대한 미리 보기 지원에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 7bcd49de1b92dd91ee1c643d1f48629d8b0a8c02
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694645"
---
# <a name="hb-series-virtual-machines-overview"></a>HB 시리즈 가상 머신 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 단일 확장 집합

AMD EPYC에서 HPC(고성능 컴퓨팅) 애플리케이션 성능을 최대화하려면 신중한 접근 방식의 메모리 지역성과 프로세스 배치를 신중하게 설정해야 합니다. 아래에서는 Azure에서 HPC 애플리케이션에 대한 AMD EPYC 아키텍처 및 그 구현에 대해 간략하게 설명합니다. “pNUMA”라는 용어는 물리적인 NUMA 도메인을, “vNUMA”는 가상화된 NUMA 도메인을 의미합니다.

물리적으로, [HB 시리즈](../../hb-series.md) 서버 1개는 총 64개의 물리적 코어를 위한 32코어 EPYC 7551 CPU 2개로 구성됩니다. 64개의 코어는 16개의 pNUMA 도메인(소켓당 8개)로 나뉘고, 각 도메인은 4개의 코어로 이루어져 있으며 “CPU 복합체”(“CCX”)로 알려져 있습니다. 각 CCX에는 자체 L3 캐시가 있으며, 이를 통해 OS가 pNUMA/vNUMA 경계를 파악합니다. 인접한 한 쌍의 CCX는 두 개의 물리적 DRAM 채널(HB 시리즈 서버 내 32GB DRAM)에 대한 액세스를 공유합니다.

VM을 방해하지 않고 Azure 하이퍼바이저가 작동할 수 있는 공간을 제공하기 위해 물리적 pNUMA 도메인 0(첫 번째 CCX)을 예약합니다. 그런 다음 VM에 대한 pNUMA 도메인 1-15(나머지 CCX 단위)를 할당합니다. VM에 다음이 표시됩니다.

VM당 `(15 vNUMA domains) * (4 cores/vNUMA) = 60` 코어

VM 자체는 pNUMA 0이 지정되지 않았음을 인식하지 못합니다. VM은 pNUMA 1~15를 vNUMA 0~14로 인식하고, vSocket 0에 vNUMA 7, vSocket 1에 8 vNUMA가 있는 것으로 인식합니다. 비대칭이기는 하지만 OS는 정상적으로 부팅되고 작동됩니다. 이 가이드의 뒷부분에서는 이 비대칭 NUMA 레이아웃에서 MPI 애플리케이션을 효과적으로 실행하는 방법을 설명합니다.

게스트 VM에 기본 실리콘을 그대로 노출하기 때문에 HB 시리즈 VM에서는 프로세스 고정이 효과적입니다. 최적의 성능과 일관성을 위해 프로세스를 고정하는 것이 좋습니다.

다음 다이어그램에서는 Azure 하이퍼바이저 및 HB 시리즈 VM용으로 예약된 코어의 분리를 보여 줍니다.

![Azure 하이퍼바이저 및 HB 시리즈 VM용으로 예약된 코어의 분리](./media/architecture/hb-segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양                | HB 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어                            | 60(SMT 사용 안 함)                |
| CPU                              | AMD EPYC 7551                    |
| CPU 빈도(비 AVX)          | ~2.55GHz(단일 + 모든 코어)   |
| 메모리                           | 4GB/코어(총 240GB)         |
| 로컬 디스크                       | 700 GB SSD                       |
| Infiniband                       | 100GB EDR Mellanox ConnectX-5 |
| 네트워크                          | 50GB 이더넷(40GB 사용 가능) Azure 2세대 SmartNIC |

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양           |HB 시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 18000 코어(단일 가상 머신 확장 집합에 300개 VM, singlePlacementGroup=true)  |
| MPI 지원                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, 플랫폼 MPI  |
| 추가 프레임워크       | UCX, libfabric, PGAS |
| Azureob Storage 지원       | 표준 및 프리미엄 디스크(최대 4개의 디스크) |
| SRIOV RDMA에 대한 OS 지원   | CentOS/RHEL 7.6+, Ubuntu 16.04+, SLES 12 SP4 +, WinServer 2016+  |
| 오케스트레이터 지원        | CycleCloud, 배치, AKS; [클러스터 구성 옵션](../../sizes-hpc.md#cluster-configuration-options) |

## <a name="next-steps"></a>다음 단계

- [AMD EPYC 아키텍처](https://bit.ly/2Epv3kC) 및 [다중 칩 아키텍처](https://bit.ly/2GpQIMb)에 대해 자세히 알아봅니다. 자세한 내용은 [AMD EPYC 프로세서 HPC 튜닝 가이드](https://bit.ly/2T3AWZ9)를 참조하세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
