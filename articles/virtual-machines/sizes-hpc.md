---
title: Azure VM 크기 - HPC | Microsoft Doc
description: Azure의 고성능 컴퓨팅 가상 머신에 사용할 수 있는 다양한 크기를 나열합니다. 이 시리즈의 크기에 대한 스토리지 처리량 및 네트워크 대역폭뿐만 아니라 vCPU, 데이터 디스크 및 NIC의 수에 대한 정보를 제공합니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 11c3fadd7f95f07bbcbf095e8d6a126022bbea0b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697924"
---
# <a name="high-performance-computing-vm-sizes"></a>고성능 컴퓨팅 VM 크기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

> [!TIP]
> 워크로드에 가장 적합한 다른 크기를 찾으려면 **[가상 머신 선택 도구](https://aka.ms/vm-selector)** 를 사용해 보세요.

Azure H 시리즈 VM(가상 머신)은 다양한 실제 HPC 워크로드에 대한 최고의 성능, 확장성 및 비용 효율성을 제공하도록 설계되었습니다.

[HBv3 시리즈](hbv3-series.md) VM은 유체 역학, 명시적 및 암시적 유한 요소 분석, 날씨 모델링, 지진 처리, 저수지 시뮬레이션 및 RTL 시뮬레이션 같은 HPC 애플리케이션에 최적화되어 있습니다. HBv3 VM은 최대 120 AMD EPYC™ 7003 시리즈(Milan) CPU 코어, 448GB RAM, 하이퍼 스레딩을 갖지 않습니다. HBv3 시리즈 VM은 메모리 대역폭의 350GB/초를 제공합니다. 코어 당 최대 32 MB의 L3 캐시, 블록 디바이스 SSD 성능의 최대 7GB/초, 최대 3.675GHz의 클록 주파수를 제공합니다. 

모든 HBv3 시리즈 VM은 NVIDIA 네트워킹에서 200GB/초 HDR InfiniBand를 사용하여 슈퍼 컴퓨터 규모의 MPI 작업을 지원합니다. 해당 VM은 최적화되고 일관된 RDMA 성능을 위해 차단되지 않는 패트 트리에 연결됩니다. 또한 HDR InfiniBand 패브릭은 적응형 라우팅과 표준 RC 및 UD 전송 외에 DCT(동적 연결 전송)을 지원합니다. 해당 기능은 애플리케이션 성능, 확장성, 일관성을 향상시키며 해당 사용이 적극 권장됩니다.

[HBv2시리즈](hbv2-series.md) VM은 유체 역학, 유한 요소 분석, 저수지 시뮬레이션 등 메모리 대역폭으로 구동되는 애플리케이션에 최적화되어 있습니다. HBv2 VM에는 120개의 AMD EPYC 7742프로세서 코어, CPU 코어당 4GB RAM이 있으며 동시 다중 스레딩이 없습니다. 각 HBv2 VM은 최대 340GB/초 메모리 대역폭, 최대 4테라플롭 FP64컴퓨팅을 제공합니다.

HBv2 VM은 200GB/초의 Mellanox HDR InfiniBand를 제공하며 HB와 HC 시리즈 VM은 모두 100GB/초 Mellanox EDR InfiniBand를 제공합니다. 모든 VM 형식은 최적화되고 일관된 RDMA 성능을 위해 차단되지 않는 패트 트리에 연결됩니다. HBv2 VM은 적응형 라우팅 및 동적으로 연결된 전송(표준 RC 및 UD 전송 외에 DCT)을 지원합니다. 해당 기능은 애플리케이션 성능, 확장성, 일관성을 향상시키며 해당 사용이 적극 권장됩니다.

[HB 시리즈](hb-series.md) VM은 유체 역학, 명시적 유한 요소 분석, 날씨 모델링 등 메모리 대역폭으로 구동되는 애플리케이션에 최적화되어 있습니다. HB VM은 60AMD EPYC 7551프로세서 코어, CPU 코어 당 4GB RAM을 탑재했으며, 하이퍼스레딩은 지원하지 않습니다. AMD EPYC 플랫폼은 260GB/초 이상의 메모리 대역폭을 제공합니다.

[HC 시리즈](hc-series.md) VM은 암시적 유한 요소 분석, 분자 역학, 계산화학 등 조밀한 컴퓨팅으로 구동되는 애플리케이션에 최적화되어 있습니다. HC VM은 44개의 Intel Xeon Platinum 8168 프로세서 코어, CPU 코어당 8GB RAM을 제공하며 하이퍼스레딩은 제공하지 않습니다. Intel Xeon Platinum 플랫폼은 Intel Math Kernel Library와 같은 소프트웨어 도구에 대해 다양한 소프트웨어 에코시스템을 지원합니다.

[H 시리즈](h-series.md) VM은 높은 CPU 주파수 또는 많은 코어당 메모리 요구 사항으로 구동되는 애플리케이션에 최적화되어 있습니다. H 시리즈 VM은 8개 또는 16개의 Intel Xeon E5 2667 v3 프로세서 코어, CPU 코어당 7GB 또는 14GB의 RAM을 제공하며 하이퍼스레딩은 지원하지 않습니다. H 시리즈는 일관된 RDMA 성능을 위해 비차단 FAT 트리 구성에서 56Gb/초 Mellanox FDR InfiniBand를 제공합니다. H 시리즈 VM은 Intel MPI 5.x 및 MS-MPI를 지원합니다.

> [!NOTE]
> 모든 HBv3, HBv2, HB 및 HC 시리즈 VM은 물리적 서버에 단독으로 액세스할 수 있습니다. 물리적 서버당 VM은 1개만 있으며 이러한 VM 크기에 대해 다른 VM과 공유되는 다중 테넌트는 없습니다.

> [!NOTE]
> [A8 – A11 VM](./sizes-previous-gen.md#a-series---compute-intensive-instances)은 2021년 3월부터 사용이 중지됩니다. 이제 이러한 크기의 새로운 VM은 배포할 수 없습니다. 기존 VM이 있는 경우 [HPC 마이그레이션 가이드](https://azure.microsoft.com/resources/hpc-migration-guide/)의 다른 VM 크기로 마이그레이션이 포함된 다음 단계에 대한 이메일 알림을 참조하세요.

## <a name="rdma-capable-instances"></a>RDMA 지원 인스턴스

대부분의 HPC VM 크기는 RDMA(원격 직접 메모리 액세스) 연결을 위한 네트워크 인터페이스를 제공합니다. 'r'로 지정된 일부 [N-시리즈](./nc-series.md) 크기도 RDMA를 지원합니다. 이 인터페이스는 다른 VM 크기에서 사용할 수 있는 표준 Azure 이더넷 네트워크 인터페이스 외에 추가로 사용됩니다.

이 보조 인터페이스를 사용하면 RDMA 지원 인스턴스에서 HBv3, HBv2에 대해 HDR 속도로, HB, HC, NDv2에 대해 EDR 속도로, H16r, H16mr에 대해 FDR 속도로 작동하는 IB(InfiniBand) 네트워크 및 기타 RDMA 지원 N-시리즈 가상 머신을 통해 통신할 수 있습니다. 이러한 RDMA 기능은 MPI(Message Passing Interface) 기반 애플리케이션의 확장성 및 성능을 높일 수 있습니다.

> [!NOTE]
> **SR-IOV 지원**: Azure HPC에는 현재 InfiniBand에 대해 SR-IOV를 사용하는지 여부에 따라 두 개의 VM 클래스가 있습니다. 현재 Azure에서 H16r, H16mr, NC24r을 제외하고 모든 최신 세대, RDMA 지원 또는 InfiniBand 사용이 가능한 VM은 SR-IOV를 사용하도록 설정되어 있습니다.
> RDMA는 InfiniBand(IB) 네트워크를 통해서만 사용하도록 설정되며 모든 RDMA 지원VM을 지원합니다.
> IP over IB는 SR-IOV 사용 VM 에서만 지원됩니다.
> RDMA는 이더넷 네트워크를 통해 사용하도록 설정되지 않습니다.

- **운영 체제** - CentOS, RHEL, Ubuntu, SUSE와 같은 Linux 배포판이 일반적으로 사용됩니다. Windows Server 2016 이상 버전은 모든 HPC 시리즈 VM에서 지원됩니다. Windows Server 2012 R2 및 Windows Server 2012도 SR-IOV를 사용하지 않는 VM에서 지원됩니다. [Windows Server 2012 R2는 VM 크기가 64(가상 또는 실제) 코어 이상인 HBv2 이상부터 지원되지 않습니다](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Marketplace의 지원되는 VM 이미지 목록과 이를 적절하게 구성하는 방법은 [VM 이미지](./workloads/hpc/configure.md)를 참조하세요. 각 VM 크기 페이지에도 소프트웨어 스택 지원이 나열되어 있습니다.

- **InfiniBand 및 드라이버** - InfiniBand 사용 VM에서 RDMA를 사용하도록 설정하려면 적절한 드라이버가 필요합니다. Marketplace의 지원되는 VM 이미지 목록과 이를 적절하게 구성하는 방법은 [VM 이미지](./workloads/hpc/configure.md)를 참조하세요. 또한 VM 확장이나 InfiniBand 드라이버의 수동 설치에 대해 알아보려면 [InfiniBand를 사용하도록 설정](./workloads/hpc/enable-infiniband.md)을 참조하세요.

- **MPI** - Azure에서 SR-IOV를 사용하는 VM 크기는 대부분의 MPI를 Mellanox OFED와 함께 사용할 수 있습니다. 비 SR-IOV를 사용하는 VM에서 지원되는 MPI 구현은 Microsoft ND(Network Direct) 인터페이스를 사용하여 VM 간에 통신합니다. 따라서 Intel MPI 5.x 및 MS MPI(Microsoft MPI) 2012R2이상 버전만 지원됩니다. Intel MPI 런타임 라이브러리의 이후 버전은 Azure RDMA 드라이버와 호환되거나 호환되지 않을 수 있습니다. Azure의 HPC VM에서 MPI를 설정하는 방법에 대한 자세한 내용은 [HPC에 대한 MPI 설정](./workloads/hpc/setup-mpi.md)을 참조하세요.

  > [!NOTE]
  > **RDMA 네트워크 주소 공간**: Azure의 RDMA 네트워크는 주소 공간 172.16.0.0/16을 예약합니다. Azure 가상 네트워크에 배포된 인스턴스에서 MPI 애플리케이션을 실행하려면 가상 네트워크 주소 공간이 RDMA 네트워크와 겹치지 않도록 해야 합니다.

## <a name="cluster-configuration-options"></a>클러스터 구성 옵션

Azure에서는 다음을 비롯한 RDMA 네트워크를 사용하여 통신할 수 있는 HPC VM의 클러스터를 만드는 몇 가지 옵션을 제공합니다. 

- **가상 머신** - Azure Resource Manager 배포 모델을 사용하는 경우 동일한 확장 집합이나 가용성 집합에서 RDMA 가능 HPC VM을 배포합니다. 클래식 배포 모델을 사용하는 경우 동일한 클라우드 서비스에서 VM을 배포합니다.

- **가상 머신 확장 집합** - 가상 머신 확장 집합에서 확장 집합 내 InfiniBand 통신 그룹의 단일 배치 그룹에 대한 배포를 제한하도록 합니다. 예를 들어, Resource Manager 템플릿에서 `singlePlacementGroup` 속성을 `true`로 설정합니다. `singlePlacementGroup=true`으로 스핀업할 수 있는 최대 확장 집합 크기는 기본적으로 100개의 VM으로 제한됩니다. HPC 작업 확장 요구가 단일 테넌트의 100VM 보다 높은 경우 [온라인 고객 지원 요청을 열어](../azure-portal/supportability/how-to-create-azure-support-request.md) 무료로 증량을 요청할 수 있습니다. 단일 확장 집합의 VM 수에 대한 제한은 300으로 늘릴 수 있습니다. 가용성 집합을 사용하여 VM을 배포하는 경우 최대 제한은 가용성 집합 당 VM 200입니다.

  > [!NOTE]
  > **가상 머신 간의 MPI**: VM(가상 머신) 간에 RDMA(예: MPI 통신 사용)가 필요한 경우 VM이 동일한 가상 머신 확장 집합이나 가용성 집합에 있는지 확인합니다.

- **Azure CycleCloud** - [Azure CycleCloud](/azure/cyclecloud/)에서 HPC 클러스터를 만들어서 MPI 작업을 실행합니다.

- **Azure Batch** - [Azure Batch](../batch/index.yml) 풀을 만들어 MPI 워크로드를 실행합니다. Azure Batch에서 MPI 애플리케이션 실행할 때 계산 집약적 인스턴스를 사용하려면 [다중 인스턴스 작업을 사용하여 Azure Batch에서 MPI(메시지 전달 인터페이스) 애플리케이션 실행](../batch/batch-mpi.md)을 참조하세요.

- **Microsoft HPC 팩** - [HPC 팩](/powershell/high-performance-computing/overview)에는 RDMA 지원 Linux VM에 배포할 경우 Azure RDMA 네트워크를 사용하는 MS-MPI에 대한 런타임 환경이 포함되어 있습니다. 예제 배포는 [MPI 애플리케이션을 실행하기 위해 HPC Pack을 사용하여 Linux RDMA 클러스터 설정](/powershell/high-performance-computing/hpcpack-linux-openfoam)을 참조하세요.

## <a name="deployment-considerations"></a>배포 고려 사항

- **Azure 구독** - 몇몇 계산 집약적 인스턴스를 배포하려면 종량제 구독 또는 기타 구매 옵션을 고려합니다. [Azure 무료 계정](https://azure.microsoft.com/free/)을 사용하는 경우, 제한된 수의 Azure 컴퓨팅 코어만 사용할 수 있습니다.

- **가격 책정 및 가용성** - Azure 지역에서 [VM 가격 책정](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) 및 [가용성](https://azure.microsoft.com/global-infrastructure/services/)을 확인합니다.

- **코어 할당량** – 기본값에서 Azure 구독의 코어 할당량을 늘려야 합니다. 구독에 따라서도 H 시리즈를 포함하여 특정 VM 크기 제품군에 배포할 수 있는 코어 수가 제한될 수 있습니다. 할당량 증가를 요청하려면 무료로 [온라인 고객 지원 요청을 개설](../azure-portal/supportability/how-to-create-azure-support-request.md) 합니다. (기본 제한은 구독 범주에 따라 달라질 수 있습니다.)

  > [!NOTE]
  > 대규모 용량이 필요한 경우 Azure 지원에 문의합니다. Azure 할당량은 신용 제한이며 용량 보증이 아닙니다. 할당량에 관계 없이 사용하는 코어에 대해서만 요금이 청구됩니다.
  
- **가상 네트워크** - Azure [가상 네트워크](https://azure.microsoft.com/documentation/services/virtual-network/) 는 계산 집약적 인스턴스를 사용할 필요가 없습니다. 하지만 많은 배포에서 온-프레미스 리소스에 액세스해야 하는 경우 적어도 클라우드 기반 Azure 가상 네트워크 또는 사이트 간 연결이 필요합니다. 필요한 경우 인스턴스를 배포할 새 가상 네트워크를 만듭니다. 선호도 그룹에서 가상 네트워크에 계산 집약적 VM을 추가하는 것은 지원되지 않습니다.

- **크기 조정** - 특수한 하드웨어로 인해 동일한 크기의 제품군(H 시리즈 또는 N 시리즈) 내에서만 계산 집약적 인스턴스의 크기를 조정할 수 있습니다. 예를 들어 H 시리즈 VM 크기는 한 H 시리즈에서 다른 H 시리즈로만 조정할 수 있습니다. InfiniBand 드라이버 지원 및 NVMe 디스크에 대한 추가 고려 사항은 특정 VM에 대해 고려해야 할 수 있습니다.


## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [컴퓨팅 최적화](sizes-compute.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

- [HPC 워크로드](./workloads/hpc/overview.md)에서 [VM을 구성하고](./workloads/hpc/configure.md), [InfiniBand를 사용하도록 설정하고](./workloads/hpc/enable-infiniband.md), [MPI를 설정하고](./workloads/hpc/setup-mpi.md), Azure 용 HPC 애플리케이션을 최적화하는 방법에 대해 자세히 알아보세요.
- [HBv3시리즈 개요](./workloads/hpc/hbv3-series-overview.md) 및 [HC 시리즈 개요](./workloads/hpc/hc-series-overview.md)를 검토합니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
