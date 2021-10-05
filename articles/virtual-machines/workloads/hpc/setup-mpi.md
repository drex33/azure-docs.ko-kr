---
title: HPC에 대한 MPI(메시지 전달 인터페이스) 설정 - Azure Virtual Machines | Microsoft Docs
description: Azure에서 HPC에 대한 MPI를 설정하는 방법에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/16/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: ca3559d262420ee6c7ba935a0986340d2d475d8d
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129533193"
---
# <a name="set-up-message-passing-interface-for-hpc"></a>HPC에 대한 메시지 전달 인터페이스 설정

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

[MPI(메시지 전달 인터페이스)](https://en.wikipedia.org/wiki/Message_Passing_Interface)는 분산 메모리 병렬화를 위한 개방형 라이브러리 및 사실상의 표준입니다. 일반적으로 많은 HPC 워크로드에서 사용됩니다. [RDMA 지원](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM에서 HPC 워크로드는 MPI를 사용하여 낮은 대기 시간과 높은 대역폭 InfiniBand 네트워크를 통해 통신할 수 있습니다.
- Azure에서 SR-IOV를 사용하는 VM 크기는 대부분의 MPI를 Mellanox OFED와 함께 사용할 수 있습니다.
- 비 SR-IOV를 사용하는 VM에서 지원되는 MPI 구현은 Microsoft ND(Network Direct) 인터페이스를 사용하여 VM 간에 통신합니다. 따라서 MS MPI(Microsoft MPI) 2012 R2 이상 및 Intel MPI 5.x 버전만 지원됩니다. Intel MPI 런타임 라이브러리의 이후 버전(2017, 2018)은 Azure RDMA 드라이버와 호환되거나 호환되지 않을 수 있습니다.

SR-IOV를 사용하는 [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances)의 경우, [CentOS-HPC VM 이미지](configure.md#centos-hpc-vm-images) 버전 7.6 이상이 적합합니다. 이러한 VM 이미지는 RDMA용 OFED 드라이버와 일반적으로 사용되는 다양한 MPI 라이브러리 및 과학적 컴퓨팅 패키지를 통해 최적화되고 미리 로드된 상태로 제공되며, 시작하기에 가장 쉬운 방법입니다.

여기에 나와 있는 예제는 RHEL/CentOS에서 사용되지만, 그 단계는 일반적으로 사용되며 Ubuntu(16.04, 18.04 19.04, 20.04) 및 SLES(12 SP4, 15)와 같이 호환되는 Linux 운영 체제에서 사용할 수 있습니다. 다른 배포판에 대한 다른 MPI 구현을 설정하기 위한 더 많은 예제는 [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)에 있습니다.

> [!NOTE]
> 특정 MPI 라이브러리(예: Platform MPI)를 사용하여 SR-IOV를 사용하는 VM에서 MPI 작업을 실행하려면 격리 및 보안을 위해 테넌트 전체에 파티션 키(p-키)를 설정해야 할 수 있습니다. P-키 값을 확인하고 MPI 라이브러리를 사용하는 MPI 작업에 대해 올바르게 설정하는 방법에 대한 자세한 내용은 [파티션 키 검색](#discover-partition-keys) 섹션의 단계를 따르세요.

> [!NOTE]
> 아래 코드 조각은 예제입니다. 안정적인 최신 버전의 패키지를 사용하거나 [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mpis.sh)를 참조하는 것이 좋습니다.

## <a name="choosing-mpi-library"></a>MPI 라이브러리 선택
HPC 애플리케이션이 특정 MPI 라이브러리를 권장하는 경우 먼저 해당 버전을 사용해 보세요. MPI를 유연하게 선택할 수 있고 최상의 성능을 원할 경우 HPC-X를 사용해 보세요. 전반적으로 HPC-X MPI는 InfiniBand 인터페이스에 UCX 프레임워크를 사용하여 최고의 성능을 제공하고 모든 Mellanox InfiniBand 하드웨어 및 소프트웨어 기능을 활용합니다. 또한 HPC-X 및 OpenMPI는 ABI 호환되므로 OpenMPI로 빌드된 HPC-X를 사용하여 HPC 애플리케이션을 동적으로 실행할 수 있습니다. 마찬가지로 Intel MPI, MVAPICH, MPICH는 ABI 호환됩니다.

다음 그림은 인기 있는 MPI 라이브러리의 아키텍처를 보여 줍니다.

![인기 있는 MPI 라이브러리의 아키텍처](./media/mpi-architecture.png)

## <a name="ucx"></a>UCX

[UCX(통합 통신 X)](https://github.com/openucx/ucx)는 HPC용 통신 API의 프레임워크입니다. InfiniBand를 통한 MPI 통신에 최적화되며 OpenMPI 및 MPICH와 같은 여러 MPI 구현에서 작동합니다.

```bash
wget https://github.com/openucx/ucx/releases/download/v1.4.0/ucx-1.4.0.tar.gz
tar -xvf ucx-1.4.0.tar.gz
cd ucx-1.4.0
./configure --prefix=<ucx-install-path>
make -j 8 && make install
```

> [!NOTE]
> 최근 UCX의 빌드는 여러 NIC 인터페이스가 있는 경우 올바른 InfiniBand 인터페이스를 선택하는 [문제](https://github.com/openucx/ucx/pull/5965)를 해결했습니다. 자세한 내용은 VM에서 가속화된 네트워킹을 사용하도록 설정한 경우 InfiniBand를 통해 MPI를 실행하는 방법에 대한 [HPC 및 GPU VM의 알려진 문제 해결](hb-hc-known-issues.md#accelerated-networking-on-hb-hc-hbv2-and-ndv2)을 참조하세요.

## <a name="hpc-x"></a>HPC-X

[HPC-X 소프트웨어 도구 키트](https://www.mellanox.com/products/hpc-x-toolkit)는 UCX 및 HCOLL을 포함하며 UCX에 기반하여 빌드할 수 있습니다.

```bash
HPCX_VERSION="v2.6.0"
HPCX_DOWNLOAD_URL=https://azhpcstor.blob.core.windows.net/azhpc-images-store/hpcx-v2.6.0-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
wget --retry-connrefused --tries=3 --waitretry=5 $HPCX_DOWNLOAD_URL
tar -xvf hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64.tbz
mv hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64 ${INSTALL_PREFIX}
HPCX_PATH=${INSTALL_PREFIX}/hpcx-${HPCX_VERSION}-gcc-MLNX_OFED_LINUX-5.0-1.0.0.0-redhat7.7-x86_64
```

다음 명령은 HPC-X 및 OpenMPI에 대한 몇 가지 권장 mpirun 인수를 보여 줍니다.
```bash
mpirun -n $NPROCS --hostfile $HOSTFILE --map-by ppr:$NUMBER_PROCESSES_PER_NUMA:numa:pe=$NUMBER_THREADS_PER_PROCESS -report-bindings $MPI_EXECUTABLE
```
여기서

|매개 변수|Description                                        |
|---------|---------------------------------------------------|
|`NPROCS`   |MPI 프로세스의 수를 지정합니다. 예: `-n 16`|
|`$HOSTFILE`|호스트 이름 또는 IP 주소가 포함된 파일을 지정하여 MPI 프로세스를 실행할 위치를 나타냅니다. 예: `--hostfile hosts`|
|`$NUMBER_PROCESSES_PER_NUMA`   |각 NUMA 도메인에서 실행할 MPI 프로세스의 수를 지정합니다. 예를 들어 NUMA당 4개의 MPI 프로세스를 지정하려면 `--map-by ppr:4:numa:pe=1`을 사용합니다.|
|`$NUMBER_THREADS_PER_PROCESS`  |MPI 프로세스당 스레드 수를 지정합니다. 예를 들어 NUMA당 1개의 MPI 프로세스와 4개의 스레드를 지정하려면 `--map-by ppr:1:numa:pe=4`를 사용합니다.|
|`-report-bindings` |MPI 프로세스와 코어 간 매핑을 인쇄합니다. 이는 MPI 프로세스 고정이 올바른지 확인하는 데 유용합니다.|
|`$MPI_EXECUTABLE`  |MPI 라이브러리의 MPI 실행 파일 연결을 지정합니다. MPI 컴파일러 래퍼는 이를 자동으로 수행합니다. 예를 들어 `mpicc` 또는 `mpif90`입니다.|

OSU 대기 시간 마이크로 벤치마크를 실행하는 예제는 다음과 같습니다.
```bash
${HPCX_PATH}mpirun -np 2 --map-by ppr:2:node -x UCX_TLS=rc ${HPCX_PATH}/ompi/tests/osu-micro-benchmarks-5.3.2/osu_latency
```

### <a name="optimizing-mpi-collectives"></a>MPI Collective 최적화

MPI Collective 통신 기본 형식은 유연하고 이식 가능한 그룹 통신 작업을 구현하는 방법을 제공합니다. 이는 다양한 과학적 병렬 애플리케이션에서 널리 사용되며 전반적인 애플리케이션 성능에 상당한 영향을 미칩니다. 집단 통신에 대해 HPC-X 및 HCOLL 라이브러리를 사용하여 집단 통신 성능을 최적화하기 위한 구성 매개 변수에 대한 자세한 내용은 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/optimizing-mpi-collective-communication-using-hpc-x-on-azurehpc/ba-p/1356740)를 참조하세요.

예를 들어, 밀결합된 MPI 애플리케이션이 과도하게 집단 통신을 하는 것으로 의심되는 경우 계층적 집단(HCOLL)을 사용하도록 설정할 수 있습니다. 이러한 기능을 사용하려면 다음 매개 변수를 사용합니다.
```bash
-mca coll_hcoll_enable 1 -x HCOLL_MAIN_IB=<MLX device>:<Port>
```

> [!NOTE] 
> HPC-X 2.7.4 이상을 사용하는 경우 MOFED 버전의 UCX가 HPC-X의 UCX가 서로 다른 경우 명시적으로 LD_LIBRARY_PATH를 전달해야 할 수 있습니다.

## <a name="openmpi"></a>OpenMPI

위에 설명된 대로 UCX를 설치합니다. HCOLL은 [HPC-X 소프트웨어 도구 키트](https://www.mellanox.com/products/hpc-x-toolkit)의 일부이며 특별한 설치를 요구하지 않습니다.

OpenMPI는 리포지토리에서 사용할 수 있는 패키지에서 설치할 수 있습니다.

```bash
sudo yum install –y openmpi
```

UCX를 사용하여 최신의 안정적인 OpenMPI 릴리스를 빌드하는 것이 좋습니다.

```bash
OMPI_VERSION="4.0.3"
OMPI_DOWNLOAD_URL=https://download.open-mpi.org/release/open-mpi/v4.0/openmpi-${OMPI_VERSION}.tar.gz
wget --retry-connrefused --tries=3 --waitretry=5 $OMPI_DOWNLOAD_URL
tar -xvf openmpi-${OMPI_VERSION}.tar.gz
cd openmpi-${OMPI_VERSION}
./configure --prefix=${INSTALL_PREFIX}/openmpi-${OMPI_VERSION} --with-ucx=${UCX_PATH} --with-hcoll=${HCOLL_PATH} --enable-mpirun-prefix-by-default --with-platform=contrib/platform/mellanox/optimized && make -j$(nproc) && make install
```

성능을 최적화하려면 `ucx` 및 `hcoll`을 사용하여 OpenMPI를 실행합니다. [HPC-X](#hpc-x) 예제도 참조하세요.

```bash
${INSTALL_PREFIX}/bin/mpirun -np 2 --map-by node --hostfile ~/hostfile -mca pml ucx --mca btl ^vader,tcp,openib -x UCX_NET_DEVICES=mlx5_0:1  -x UCX_IB_PKEY=0x0003  ./osu_latency
```

위에서 언급한 대로 파티션 키를 확인합니다.

## <a name="intel-mpi"></a>Intel MPI

선택한 버전의 [Intel MPI](https://software.intel.com/mpi-library/choose-download)를 다운로드합니다. Intel MPI 2019 릴리스는 OFA(Open Fabrics Alliance) 프레임워크에서 OFI(Open Fabrics Interfaces) 프레임워크로 전환되었으며 현재는 이를 지원합니다. InfiniBand 지원 공급자로는 mlx 및 verbs 두 가지가 있습니다.
버전에 따라 I_MPI_FABRICS 환경 변수를 변경합니다.
- Intel MPI 2019 및 2021에서 `I_MPI_FABRICS=shm:ofi`, `I_MPI_OFI_PROVIDER=mlx`를 사용합니다. `mlx` 공급자는 UCX를 사용합니다. 동사를 사용하는 것은 불안정하고 성능이 떨어집니다. 자세한 내용은 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/intelmpi-2019-on-azure-hpc-clusters/ba-p/1403149)를 참조하세요.
- Intel MPI 2018: `I_MPI_FABRICS=shm:ofa` 사용
- Intel MPI 2016: `I_MPI_DAPL_PROVIDER=ofa-v2-ib0` 사용

다음은 Intel MPI 2019 업데이트 5+에 제안된 몇 가지 mpirun 인수입니다.
```bash
export FI_PROVIDER=mlx
export I_MPI_DEBUG=5
export I_MPI_PIN_DOMAIN=numa

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
여기서

|매개 변수|Description                                        |
|---------|---------------------------------------------------|
|`FI_PROVIDER`  |사용되는 API, 프로토콜, 네트워크에 영향을 줄 Llibfabric 공급자로 무엇을 사용할지 지정합니다. verbs도 한 옵션이지만 일반적으로 더 나은 성능을 제공하는 것은 mlx입니다.|
|`I_MPI_DEBUG`|프로세스를 고정하는 위치 및 사용되는 프로토콜 및 네트워크에 대한 세부 정보를 제공할 수 있는 추가 디버그 출력의 수준을 지정합니다.|
|`I_MPI_PIN_DOMAIN` |프로세스 고정 방식을 지정합니다. 예를 들어 코어나 소켓 또는 NUMA 도메인에 고정할 수 있습니다. 이 예제에서는 이 환경 변수를 numa로 설정합니다. 즉, 프로세스가 NUMA 노드 도메인에 고정됩니다.|

### <a name="optimizing-mpi-collectives"></a>MPI Collective 최적화

특히 집합적 작업이 상당한 시간을 소비하는 경우 시도할 수 있는 몇 가지 다른 옵션이 있습니다. Intel MPI 2019 업데이트 5 이상은 mlx 공급자를 지원하고 InfiniBand와 통신하는 데 UCX 프레임워크를 사용합니다. HCOLL도 지원합니다.
```bash
export FI_PROVIDER=mlx
export I_MPI_COLL_EXTERNAL=1
```

### <a name="non-sr-iov-vms"></a>비 SR-IOV VM

비 SR-IOV VM의 경우 5.x 런타임 [평가판 버전](https://registrationcenter.intel.com/en/forms/?productid=1740) 다운로드의 예제는 다음과 같습니다.
```bash
wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/9278/l_mpi_p_5.1.3.223.tgz
```
설치 단계에 대해서는 [Intel MPI Library 설치 가이드](https://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html)를 참조하세요.
필요에 따라 루트가 아닌 비 디버거 프로세스에 대해 ptrace를 사용하도록 설정할 수 있습니다(가장 최신 버전의 Intel MPI에 필요).
```bash
echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
```

### <a name="suse-linux"></a>SUSE Linux
SUSE Linux Enterprise Server VM 이미지 버전 - HPC용 SLES 12 SP3, HPC용 SLES 12 SP3(프리미엄), HPC용 SLES 12 SP1, HPC용 SLES 12 SP1(프리미엄), SLES 12 SP4 및 SLES 15, RDMA 드라이버가 설치되고 Intel MPI 패키지가 VM에 배포됩니다. 다음 명령을 실행하여 Intel MPI를 설치합니다.
```bash
sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
```

## <a name="mvapich"></a>MVAPICH

다음은 MVAPICH2 빌드의 예제입니다. 참고. 아래에서 사용하는 것보다 최신 버전이 제공될 수 있습니다.
```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/mv2/mvapich2-2.3.tar.gz
tar -xv mvapich2-2.3.tar.gz
cd mvapich2-2.3
./configure --prefix=${INSTALL_PREFIX}
make -j 8 && make install
```

OSU 대기 시간 마이크로 벤치마크를 실행하는 예제는 다음과 같습니다.
```bash
${INSTALL_PREFIX}/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=48 ./osu_latency
```

다음 목록에는 몇 가지 권장 `mpirun` 인수가 포함되어 있습니다.
```bash
export MV2_CPU_BINDING_POLICY=scatter
export MV2_CPU_BINDING_LEVEL=numanode
export MV2_SHOW_CPU_BINDING=1
export MV2_SHOW_HCA_BINDING=1

mpirun -n $NPROCS -f $HOSTFILE $MPI_EXECUTABLE
```
여기서

|매개 변수|Description                                        |
|---------|---------------------------------------------------|
|`MV2_CPU_BINDING_POLICY`   |사용할 바인딩 정책을 지정합니다. 이 정책은 프로세스를 코어 ID에 고정하는 방법에 영향을 줍니다. 이 경우 scatter를 지정하므로 프로세스는 NUMA 도메인 간에 균등하게 분산됩니다.|
|`MV2_CPU_BINDING_LEVEL`|프로세스를 고정할 위치를 지정합니다. 이 경우 numanode로 설정하므로 프로세스는 NUMA 도메인 단위에 고정됩니다.|
|`MV2_SHOW_CPU_BINDING` |프로세스를 고정하는 위치에 대한 디버그 정보를 가져올지 여부를 지정합니다.|
|`MV2_SHOW_HCA_BINDING` |각 프로세스에서 사용하는 호스트 채널 어댑터에 대한 디버그 정보를 가져올지 여부를 지정합니다.|

## <a name="platform-mpi"></a>Platform MPI

Platform MPI Community Edition에 필요한 패키지를 설치합니다.

```bash
sudo yum install libstdc++.i686
sudo yum install glibc.i686
Download platform MPI at https://www.ibm.com/developerworks/downloads/im/mpi/index.html 
sudo ./platform_mpi-09.01.04.03r-ce.bin
```

설치 프로세스를 따릅니다.

다음 명령은 MPI pingpong을 실행하는 예제이며 CentOS-HPC 7.6, 7.8 및 8.1 VM 이미지를 사용하여 HBv3 VM에서 Platform MPI를 사용하는 allreduce입니다.

```bash
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:1,10.0.0.9:1 -np 2 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 pingpong
/opt/ibm/platform_mpi/bin/mpirun -hostlist 10.0.0.8:120,10.0.0.9:120 -np 240 -e MPI_IB_PKEY=0x800a  -ibv  /home/jijos/mpi-benchmarks/IMB-MPI1 allreduce -npmin 240
```


## <a name="mpich"></a>MPICH

위에 설명된 대로 UCX를 설치합니다. MPICH를 빌드합니다.

```bash
wget https://www.mpich.org/static/downloads/3.3/mpich-3.3.tar.gz
tar -xvf mpich-3.3.tar.gz
cd mpich-3.3
./configure --with-ucx=${UCX_PATH} --prefix=${INSTALL_PREFIX} --with-device=ch4:ucx
make -j 8 && make install
```

MPICH를 실행합니다.

```bash
${INSTALL_PREFIX}/bin/mpiexec -n 2 -hostfile ~/hostfile -env UCX_IB_PKEY=0x0003 -bind-to hwthread ./osu_latency
```

위에서 언급한 대로 파티션 키를 확인합니다.

## <a name="osu-mpi-benchmarks"></a>OSU MPI 벤치마크

[OSU MPI 벤치마크](http://mvapich.cse.ohio-state.edu/benchmarks/) 및 untar를 다운로드합니다.

```bash
wget http://mvapich.cse.ohio-state.edu/download/mvapich/osu-micro-benchmarks-5.5.tar.gz
tar –xvf osu-micro-benchmarks-5.5.tar.gz
cd osu-micro-benchmarks-5.5
```

특정 MPI 라이브러리를 사용하여 벤치마크를 빌드합니다.

```bash
CC=<mpi-install-path/bin/mpicc>CXX=<mpi-install-path/bin/mpicxx> ./configure 
make
```

MPI 벤치마크는 `mpi/` 폴더 아래에 있습니다.


## <a name="discover-partition-keys"></a>파티션 키 검색

동일한 테넌트 내의 다른 VM(가용성 집합 또는 Virtual Machine Scale Set)과 통신하기 위한 파티션 키(p-키)를 검색합니다.

```bash
/sys/class/infiniband/mlx5_0/ports/1/pkeys/0
/sys/class/infiniband/mlx5_0/ports/1/pkeys/1
```

둘 중 더 큰 값은 MPI에서 사용해야 하는 테넌트 키입니다. 예: 다음이 p-키인 경우 0x800b는 MPI와 함께 사용해야 합니다.

```bash
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/0
0x800b
cat /sys/class/infiniband/mlx5_0/ports/1/pkeys/1
0x7fff
```

기본(0x7fff) 파티션 키가 아닌 다른 파티션을 사용합니다. UCX를 사용하려면 p-키의 MSB를 지워야 합니다. 예를 들어 0x800b에 대해 UCX_IB_PKEY를 0x000b로 설정합니다.

또한 테넌트(가용성 집합 또는 Virtual Machine Scale Set)가 있는 한 PKEY는 동일하게 유지됩니다. 노드가 추가되거나 삭제되는 경우에도 마찬가지입니다. 새 테넌트가 서로 다른 PKEY를 가져옵니다.


## <a name="set-up-user-limits-for-mpi"></a>MPI에 대한 사용자 제한 설정

MPI에 대한 사용자 제한을 설정합니다.

```bash
cat << EOF | sudo tee -a /etc/security/limits.conf
*               hard    memlock         unlimited
*               soft    memlock         unlimited
*               hard    nofile          65535
*               soft    nofile          65535
EOF
```

## <a name="set-up-ssh-keys-for-mpi"></a>MPI에 대한 SSH 키 설정

필요한 MPI 유형에 대한 SSH 키를 설정합니다.

```bash
ssh-keygen -f /home/$USER/.ssh/id_rsa -t rsa -N ''
cat << EOF > /home/$USER/.ssh/config
Host *
    StrictHostKeyChecking no
EOF
cat /home/$USER/.ssh/id_rsa.pub >> /home/$USER/.ssh/authorized_keys
chmod 600 /home/$USER/.ssh/authorized_keys
chmod 644 /home/$USER/.ssh/config
```

위의 구문은 공유 홈 디렉터리를 가정합니다. 그렇지 않으면 .ssh 디렉터리를 각 노드에 복사해야 합니다.

## <a name="next-steps"></a>다음 단계

- [InfiniBand 지원](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM에 대해 알아보세요.
- [HBv3 시리즈 개요](hbv3-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토합니다.
- [HB 시리즈 VM에 최적화된 MPI 프로세스 배치](https://techcommunity.microsoft.com/t5/azure-global/optimal-mpi-process-placement-for-azure-hb-series-vms/ba-p/2450663)를 읽어보세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
