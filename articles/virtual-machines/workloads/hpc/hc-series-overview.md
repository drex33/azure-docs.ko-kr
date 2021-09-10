---
title: HC 시리즈 VM 개요 - Azure Virtual Machines | Microsoft Docs
description: Azure에서 HC 시리즈 VM 크기의 미리 보기 지원에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 0db18bf162ee91ddccc070d7250a2ee35b05f5a9
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689208"
---
# <a name="hc-series-virtual-machine-overview"></a>HC 시리즈 가상 머신 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

Intel Xeon 확장 가능 프로세서에서 HPC 애플리케이션 성능을 최대화하려면 이 새로운 아키텍처에 프로세스를 배치하기 위한 신중한 접근 방식이 필요합니다. 여기에서는 HPC 애플리케이션을 위해 Azure HC 시리즈 VM에서 이를 구현하는 방법을 간략하게 설명합니다. “pNUMA”라는 용어는 물리적인 NUMA 도메인을, “vNUMA”는 가상화된 NUMA 도메인을 의미합니다. 마찬가지로 "pCore"라는 용어는 물리적인 CPU 코어를, "vCore"는 가상화된 CPU 코어를 의미합니다.

물리적으로, [HC 시리즈](../../hc-series.md) 서버는 총 48개의 물리적 코어를 위한 24코어 Xeon Platinum 8168 CPU 2개로 구성됩니다. 각 CPU는 단일 pNUMA 도메인이며 6개 채널의 DRAM에 통합적으로 액세스할 수 있습니다. Intel Xeon Platinum CPU는 이전 세대(256KB/코어 -> 1MB/코어)보다 4배 더 큰 L2 캐시를 제공하지만, 이전 Intel CPU(2.5MB/코어 -> 1.375MB/코어)에 비해서는 L3 캐시를 줄였습니다.

위의 토폴로지는 HC 시리즈 하이퍼바이저 구성에도 적용됩니다. Azure 하이퍼바이저가 VM을 방해하지 않고 작동할 수 있는 공간을 제공하기 위해 pCore 0-1 및 24-25(즉, 각 소켓의 처음 2개 pCore)가 예약되어 있습니다. 그런 다음, pNUMA 도메인의 나머지 코어를 모두 VM에 할당합니다. 따라서 VM에 다음이 표시됩니다.

VM당 `(2 vNUMA domains) * (22 cores/vNUMA) = 44`개 코어

VM은 pCore 0-1 및 24-25가 제공되지 않았다는 정보를 알 수 없습니다. 따라서 기본적으로 22개의 코어가 있는 것처럼 각 vNUMA를 노출합니다.

또한 Intel Xeon Platinum, Gold 및 Silver CPU에는 CPU 소켓 내부 및 외부 통신을 위해 온다이 2D 메시 네트워크가 도입되어 있습니다. 최적의 성능과 일관성을 유지하기 위해 이를 고정하는 것이 좋습니다. 게스트 VM에 기본 실리콘을 그대로 노출하기 때문에 HC 시리즈 VM에서는 프로세스 고정이 효과적입니다.

다음 다이어그램에서는 Azure 하이퍼바이저 및 HC 시리즈 VM용으로 예약된 코어의 분리를 보여 줍니다.

![Azure 하이퍼바이저 및 HC 시리즈 VM용으로 예약된 코어의 분리](./media/architecture/hc-segregation-cores.png)

## <a name="hardware-specifications"></a>하드웨어 사양

| 하드웨어 사양          | HC 시리즈 VM                     |
|----------------------------------|----------------------------------|
| 코어                            | 44(HT 사용 안 함)                 |
| CPU                              | Intel Xeon 플래티넘 8168         |
| CPU 주파수(비 AVX)          | 3.7GHz(단일 코어), 2.7~3.4GHz(모든 코어) |
| 메모리                           | 8GB/코어(총 352 개)            |
| 로컬 디스크                       | 700 GB SSD                       |
| Infiniband                       | 100GB EDR Mellanox ConnectX-5   |
| 네트워크                          | 50GB 이더넷(40GB 사용 가능) Azure 2세대 SmartNIC    |

## <a name="software-specifications"></a>소프트웨어 사양

| 소프트웨어 사양     |HC 시리즈 VM           |
|-----------------------------|-----------------------|
| 최대 MPI 작업 크기            | 13,200개의 코어(singlePlacementGroup = true로 설정된 단일 가상 머신 확장 집합의 300개 VM)  |
| MPI 지원                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, 플랫폼 MPI  |
| 추가 프레임워크       | UCX, libfabric, PGAS |
| Azureob Storage 지원       | 표준 및 프리미엄 디스크(최대 4개의 디스크) |
| SRIOV RDMA에 대한 OS 지원   | CentOS/RHEL 7.6+, Ubuntu 16.04+, SLES 12 SP4 +, WinServer 2016+  |
| 오케스트레이터 지원        | CycleCloud, 배치, AKS; [클러스터 구성 옵션](../../sizes-hpc.md#cluster-configuration-options)  |

## <a name="next-steps"></a>다음 단계

- [Intel XEON SP 아키텍처](https://software.intel.com/content/www/us/en/develop/articles/intel-xeon-processor-scalable-family-technical-overview.html)에 대해 자세히 알아봅니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
