---
title: HBv3 시리즈 VM 개요, 아키텍처, 토폴로지 - Azure Virtual Machines | Microsoft Docs
description: Azure의 HBv3 시리즈 VM 크기에 대해 알아봅니다.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 1f5442c7ade736c4313812a4cb20e6535492acc3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693410"
---
# <a name="hbv3-series-virtual-machine-overview"></a>HBv3 시리즈 가상 머신 개요 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

[HBv3 시리즈](../../hbv3-series.md) 서버에는 총 128개의 물리적 "Zen3" 코어에 대해 64코어 EPYC 7V13 CPU가 2개 있습니다. HBv3에서는 SMT(동시 다중 스레딩)을 사용하도록 설정할 수 없습니다. 이 128개의 코어는 16개의 섹션(소켓당 8개)으로 나뉘며, 각 섹션에는 32MB L3 캐시에 균일하게 액세스할 수 있는 프로세서 코어가 8개 포함됩니다. Azure HBv3 서버는 다음 AMD BIOS 설정도 실행합니다.

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

따라서 서버는 32개의 코어 크기마다 NUMA 도메인 4개(소켓당 2개)를 사용하여 부팅됩니다. 각 NUMA는 3200MT/s로 작동하는 4개의 실제 DRAM 채널에 직접 액세스할 수 있습니다.

Azure 하이퍼바이저가 VM을 방해하지 않고 작동할 수 있는 공간을 제공하기 위해 서버당 8개의 물리적 코어를 예약합니다.

## <a name="vm-topology"></a>VM 토폴로지

다음 다이어그램에는 서버의 토폴로지가 나와 있습니다. 이 8개의 하이퍼바이저 호스트 코어(노란색)를 두 CPU 소켓에서 대칭으로 예약하여 각 NUMA 도메인에 있는 특정 CCD(Core Complex Dies)에서 처음 코어 2개를 가져오고 나머지 코어는 HBv3 시리즈 VM(녹색)에 사용합니다.

![HBv3 시리즈 서버의 토폴로지](./media/architecture/hbv3/hbv3-topology-server.png)

CCD 경계는 NUMA 경계와 동일하지 않습니다. HBv3에서 연속된 4개의 CCD 그룹이 호스트 서버 수준 및 게스트 VM 내에서 NUMA 도메인으로 구성됩니다. 따라서 모든 HBv3 VM 크기가 아래와 같이 OS 및 애플리케이션에 표시되는 4개의 NUMA 도메인, 각각 특정 [HBv3 VM 크기](../../hbv3-series.md)에 따라 코어 수가 다른 4개의 균일한 NUMA 도메인을 노출합니다.

![HBv3 시리즈 VM의 토폴로지](./media/architecture/hbv3/hbv3-topology-vm.png)

각 HBv3 VM 크기는 다음과 같이 AMD EPYC 7003 시리즈와 다른 CPU의 실제 레이아웃, 기능 및 성능이 유사합니다.

| HBv3 시리즈 VM 크기             | NUMA 도메인 | NUMA 도메인당 코어 수  | AMD EPYC와의 유사성         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | 이중 소켓 EPYC 7713            |
Standard_HB120r-96s_v3            | 4            | 24                     | 이중 소켓 EPYC 7643            |
Standard_HB120r-64s_v3            | 4            | 16                     | 이중 소켓 EPYC 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | 이중 소켓 EPYC 7313            |
Standard_HB120r-16s_v3            | 4            | 4                      | 이중 소켓 EPYC 72F3            |

> [!NOTE]
> 제한된 코어 VM 크기는 VM에 노출되는 물리적 코어의 수만 줄입니다. 모든 전역 공유 자산(RAM, 메모리 대역폭, L3 캐시, GMI 및 xGMI 연결, InfiniBand, Azure 이더넷 네트워크, 로컬 SSD)은 일정하게 유지됩니다. 이를 통해 고객은 지정된 워크로드 또는 소프트웨어 라이선스 요구 사항 집합에 가장 적합한 VM 크기를 선택할 수 있습니다.

각 HBv3 VM 크기의 가상 NUMA 매핑은 기본 실제 NUMA 토폴로지에 매핑됩니다. 잠재적으로 오해의 소지가 있는 하드웨어 토폴로지 추상화가 없습니다. 

다양한 [HBV3 VM 크기](../../hbv3-series.md)의 정확한 토폴로지는 [lstopo](https://linux.die.net/man/1/lstopo)의 출력을 사용하여 다음과 같이 표시됩니다.
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Standard_HB120rs_v3에 대한 lstopo 출력을 보려면 클릭</summary>

![HBv3-120 VM에 대한 lstopo 출력](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-96_v3에 대한 lstopo 출력을 보려면 클릭</summary>

![HBv3-96 VM에 대한 lstopo 출력](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-64_v3에 대한 lstopo 출력을 보려면 클릭</summary>

![HBv3-64 VM에 대한 lstopo 출력](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-32_v3에 대한 lstopo 출력을 보려면 클릭</summary>

![HBv3-32 VM에 대한 lstopo 출력](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Standard_HB120rs-16_v3에 대한 lstopo 출력을 보려면 클릭</summary>

![HBv3-16 VM에 대 한 lstopo 출력](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

## <a name="infiniband-networking"></a>InfiniBand 네트워킹
HBv3 VM에는 최대 200기가비트/초로 작동하는 Nvidia Mellanox HDR InfiniBand 네트워크 어댑터(Connectx-6)도 있습니다. NIC는 SRIOV를 통해 VM으로 전달되어 네트워크 트래픽이 하이퍼바이저를 우회할 수 있습니다. 따라서 고객은 운영 체제 미설치 환경처럼 HBv3 VM에 표준 Mellanox OFED 드라이버를 로드합니다.

HBv3 VM은 적응형 라우팅, 동적 연결 전송(DCT, 표준 RC/UD 전송에 추가) 및 Connectx-6 어댑터의 온보드 프로세서에 대한 MPI 집단의 하드웨어 기반 오프로드를 지원합니다. 이러한 기능은 애플리케이션 성능, 확장성 및 일관성을 향상시키므로 관련 기능을 사용하는 것이 좋습니다.

## <a name="temporary-storage"></a>임시 스토리지
HBv3 VM에는 물리적 로컬 SSD 디바이스가 세 개 있습니다. 한 디바이스는 페이지 파일 역할을 하도록 미리 정의되며, 일반 "SSD" 디바이스로 VM 내에 표시됩니다.

다른 두 개의 더 큰 SSD는 NVMeDirect를 통해 형식이 지정되지 않은 블록 NVMe 디바이스로 제공됩니다. 블록 NVMe 디바이스는 하이퍼바이저를 우회하므로 대역폭과 IOPS가 더 높고 IOP당 대기 시간이 더 적습니다.

스트라이프 배열에서 쌍으로 연결된 경우 NVMe SSD는 최대 7GB/s의 읽기, 3GB/s의 쓰기를 제공하고 전체 큐 깊이에 대해 최대 186,000의 IOPS(읽기) 및 201,000의 IOPS(쓰기)를 제공합니다.

## <a name="hardware-specifications"></a>하드웨어 사양 

| 하드웨어 사양          | HBv3 시리즈 VM              |
|----------------------------------|----------------------------------|
| 코어                            | 120, 96, 64, 32, 16(SMT 사용 안 함)               | 
| CPU                              | AMD EPYC 7V13                   | 
| CPU 빈도(비AVX)          | 3.1 GHz(전체 코어), 3.675GHz(최대 코어 10개)    | 
| 메모리                           | 448GB(코어당 RAM이 VM 크기에 따라 다름)         | 
| 로컬 디스크                       | 2 * 960GB NVMe(차단), 480GB SSD(페이지 파일) | 
| Infiniband                       | 200Gb/s Mellanox Connectx-3-6 HDR InfiniBand | 
| 네트워크                          | 50GB/s 이더넷(40GB/s 사용 가능) Azure 2세대 SmartNIC | 

## <a name="software-specifications"></a>소프트웨어 사양 

| 소프트웨어 사양        | HBv3 시리즈 VM                                            | 
|--------------------------------|-----------------------------------------------------------|
| 최대 MPI 작업 크기               | 코어 36,000개(singlePlacementGroup = true로 설정된 단일 가상 머신 확장 집합의 VM 300개) |
| MPI 지원                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| 추가 프레임워크          | UCX, libfabric, PGAS                  |
| Azure Storage 지원          | 표준 및 프리미엄 디스크(최대 디스크 32개)              |
| SRIOV RDMA용 OS 지원      | CentOS/RHEL 7.6+, Ubuntu 18.04+, SLES 12 SP4+, WinServer 2016+           |
| 성능에 권장되는 OS | CentOS 8.1, Windows Server 2019+
| Orchestrator 지원           | Azure CycleCloud, Azure Batch, AKS, [클러스터 구성 옵션](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2는 HBv3 및 코어가 64개(가상 또는 물리적) 넘게 있는 기타 VM에서는 지원되지 않습니다. 자세한 내용은 [Windows Server에서 Hyper-V용으로 지원되는 Windows 게스트 운영 체제](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
