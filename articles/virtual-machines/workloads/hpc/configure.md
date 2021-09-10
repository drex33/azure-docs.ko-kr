---
title: InfiniBand 사용 H 시리즈 및 N 시리즈 Azure 가상 머신의 구성/최적화
description: HPC용 InfiniBand 사용 H 시리즈 및 N 시리즈 VM을 구성하고 최적화하는 방법에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 06/02/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: cd040ef394163113b46f9af46aef0aead9cb37ca
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689280"
---
# <a name="configure-and-optimize-vms"></a>VM 구성 및 최적화

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

이 문서는 HPC용 InfiniBand 사용 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM을 구성하 고 최적화하는 방법에 대한 몇 가지 지침을 공유합니다.

## <a name="vm-images"></a>VM 이미지
IB(InfiniBand) 사용 VM에서 RDMA를 사용하도록 설정하려면 적절한 드라이버가 필요합니다.
- Marketplace의 [CentOS-HPC VM 이미지](#centos-hpc-vm-images)는 적절한 IB 드라이버로 사전 구성되어 제공됩니다.
  - CentOS-HPC 버전 7.9 VM 이미지는 Nvidia GPU 드라이버도 사전 구성되어 제공됩니다. 
- Marketplace의 [Ubuntu-HPC VM 이미지](#ubuntu-hpc-vm-images)는 적절한 IB 드라이버 및 GPU 드라이버로 사전 구성되어 제공됩니다.

이러한 VM 이미지(VMI)는 기본 CentOS 및 Ubuntu marketplace VM 이미지를 기반으로합니다. 기본 CentOS Marketplace 이미지에서 이러한 VM 이미지를 만드는 데 사용되는 스크립트는 [azhpc-images 저장소](https://github.com/Azure/azhpc-images/tree/master/centos)에 있습니다.

GPU를 지원하는 [N 시리즈](../../sizes-gpu.md) VM에서는 적절한 GPU 드라이버가 추가로 필요합니다. 다음 방법으로 사용할 수 있습니다.
- Nvidia GPU 드라이버와 GPU 컴퓨팅 소프트웨어 스택(CUDA, NCCL)으로 사전 구성되어 제공되는 [Ubuntu-HPC VM 이미지](#ubuntu-hpc-vm-images)와 [CentOS-HPC VM 이미지](#centos-hpc-vm-images) 버전 7.9를 사용합니다.
- [VM 확장](../../extensions/hpccompute-gpu-linux.md)을 통해 GPU 드라이버를 추가합니다.
- GPU 드라이버를 [수동으로](../../linux/n-series-driver-setup.md) 설치합니다.
- Marketplace의 일부 기타 VM 이미지는 Nvidia의 VM 이미지 몇 개를 포함하여 Nvidia GPU 드라이버와 함께 미리 설치된 상태로 제공됩니다.

워크로드의 Linux 배포판 및 버전 요구 사항에 따라 Marketplace의 [CentOS-HPC VM 이미지](#centos-hpc-vm-images) 및 [Ubuntu-HPC VM 이미지](#ubuntu-hpc-vm-images)는 HPC 및 AI를 시작하는 가장 쉬운 방법입니다. Azure의 워크로드.
또한 워크로드 별 사용자 정의 및 구성을 사용하여 [커스텀 VM 이미지](../../linux/tutorial-custom-images.md)를 만들고이를 반복적으로 재사용하는 것이 좋습니다.

### <a name="vm-sizes-supported-by-the-hpc-vm-images"></a>HPC VM 이미지에서 지 원하는 VM 크기

#### <a name="infiniband-ofed-support"></a>InfiniBand OFED 지원
최신 Azure HPC Marketplace 이미지는 ConnectX3-Pro InfiniBand 카드를 지원하지 않는 Mellanox OFED 5.1 이상과 함께 제공됩니다. 이러한 VM 이미지는 지 수 x-5 및 최신 InfiniBand 카드만 지원합니다. 이는 이러한 HPC VM 이미지에서 InfiniBand OFED에 대한 다음 VM 크기 지원 매트릭스를 의미합니다.
- [H 시리즈](../../sizes-hpc.md): HB, HC, HBv2, HBv3
- [N 시리즈](../../sizes-gpu.md): NDv2, NDv4

#### <a name="gpu-driver-support"></a>GPU 드라이버 지원
현재 [Ubuntu-HPC VM 이미지](#ubuntu-hpc-vm-images)와 [CentOS-HPC VM 이미지](#centos-hpc-vm-images) 버전 7.9만 Nvidia GPU 드라이버와 GPU 컴퓨팅 소프트웨어 스택(CUDA, NCCL)으로 사전 구성되어 제공됩니다.

지원되는 HPC VM 이미지의 GPU 드라이버에 대한 VM 크기 지원 매트릭스는 다음과 같습니다.
- [N 시리즈](../../sizes-gpu.md): NDv2, NDv4 VM 크기는 Nvidia GPU 드라이버 및 GPU 컴퓨팅 소프트웨어 스택(CUDA, NCCL)에서 지원됩니다.
- [N 시리즈](../../sizes-gpu.md)의 다른 'NC'및 'ND'VM 크기는 Nvidia GPU 드라이버에서 지원됩니다.

또한 위의 모든 VM 크기는 "Gen 2"VM을 지원하지만 일부 이전 VM은 "Gen 1"VM도 지원합니다. "Gen 2"지원은 VMI URN 또는 버전 끝에 "01"로 표시됩니다.

### <a name="centos-hpc-vm-images"></a>CentOS-HPC VM 이미지

#### <a name="sr-iov-enabled-vms"></a>SR-IOV 사용 VM
SR-IOV를 사용하는 [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances)의 경우, CentOS-HPC VM 이미지 버전 7.6 이상이 적합합니다. 이러한 VM 이미지는 RDMA용 Mellanox OFED 드라이버와 일반적으로 사용되는 다양한 MPI 라이브러리 및 과학적 컴퓨팅 패키지로 최적화되고 미리 로드됩니다. 위의 [VM 크기 지원 매트릭스](#vm-sizes-supported-by-the-hpc-vm-images)를 참조하세요.
- 사용 가능한 최신 버전의 VM 이미지는 [CLI](/cli/azure/vm/image#az_vm_image_list) 또는 [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/openlogic.centos-hpc?tab=Overview)를 사용하여 다음 정보와 함께 나열될 수 있습니다.
   ```bash
   "publisher": "OpenLogic",
   "offer": "CentOS-HPC",
   ```
- 기본 CentOS Marketplace 이미지에서 CentOS-HPC 버전 7.6 이상 VM 이미지를 만드는 데 사용되는 스크립트는 [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/tree/master/centos)에 있습니다.
- 또한 CentOS-HPC 버전 7.6 이상 VM 이미지에 포함된 내용과 배포 방법에 대한 자세한 내용은 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094)에 있습니다.

> [!NOTE] 
> CentOS-HPC VM 이미지 중에서 현재 버전 7.9 VM 이미지만 Nvidia GPU 드라이버와 GPU 컴퓨팅 소프트웨어 스택(CUDA, NCCL)도 사전 구성되어 제공됩니다.

> [!NOTE] 
> FDR InfiniBand(예: NCv3 이하)가 포함된 SR-IOV 사용 N 시리즈 VM의 크기는 Marketplace에서 다음과 같은 CentOS-HPC VM 이미지 또는 이전 버전을 사용할 수 있습니다.
>- OpenLogic:CentOS-HPC:7.6:7.6.2020062900
>- OpenLogic:CentOS-HPC:7_6gen2:7.6.2020062901
>- OpenLogic:CentOS-HPC:7.7:7.7.2020062600
>- OpenLogic:CentOS-HPC:7_7-gen2:7.7.2020062601
>- OpenLogic:CentOS-HPC:8_1:8.1.2020062400
>- OpenLogic:CentOS-HPC:8_1-gen2:8.1.2020062401

#### <a name="non-sr-iov-enabled-vms"></a>SR-IOV 비사용 VM
SR-IOV 비사용 [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances), CentOS-HPC 버전 6.5 이상의 경우 Marketplace에서 최대 7.4까지 적합합니다. 예를 들어 [H16 시리즈 VM](../../h-series.md)의 경우 버전 7.1 ~ 7.4를 사용하는 것이 좋습니다. 이러한 VM 이미지는 RDMA 및 Intel MPI 버전 5.1용 네트워크 다이렉트 드라이버를 사용하여 미리 로드된 상태로 제공됩니다.

> [!NOTE]
> SR-IOV 비사용 VM의 경우 이러한 CentOS 기반 HPC 이미지에 대한 커널 업데이트는 **yum** 구성 파일에서 사용하도록 설정할 수 없습니다. 이는 NetworkDirect Linux RDMA 드라이버가 RPM 패키지로 배포되기 때문입니다. 커널이 업데이트되면 드라이버 업데이트가 작동하지 않을 수 있습니다.

### <a name="ubuntu-hpc-vm-images"></a>Ubuntu-HPC VM 이미지
SR-IOV 지원 [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances)의 경우 Ubuntu-HPC VM 이미지 버전 18.04 및 20.04가 적합합니다. 이러한 VM 이미지는 RDMA용 Mellanox OFED 드라이버, Nvidia GPU 드라이버, NCCL(GPU 계산 소프트웨어 스택) 및 널리 사용되는 다양한 MPI 라이브러리 및 과학적 컴퓨팅 패키지를 사용하여 최적화되고 미리 로드됩니다. 위의 [VM 크기 지원 매트릭스](#vm-sizes-supported-by-the-hpc-vm-images)를 참조하세요.
- 사용 가능한 최신 버전의 VM 이미지는 [CLI](/cli/azure/vm/image#az_vm_image_list) 또는 [Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-hpc?tab=overview)를 사용하여 다음 정보와 함께 나열될 수 있습니다.
   ```bash
   "publisher": "Microsoft-DSVM",
   "offer": "Ubuntu-HPC",
   ```
- 기본 Ubuntu Marketplace 이미지에서 Ubuntu-HPC VM 이미지를 만드는 데 사용되는 스크립트는 [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/tree/master/ubuntu)에 있습니다.
- 또한 Ubuntu-HPC VM 이미지에 포함된 내용과 배포 방법에 대한 자세한 내용은 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/azure-hpc-vm-images/ba-p/977094)에 있습니다.

### <a name="rhelcentos-vm-images"></a>RHEL/CentOS VM 이미지
Marketplace에서 기본 RHEL 또는 CentOS 기반의 HPC 이외 VM 이미지는 SR-IOV 사용 [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances)에서 사용하도록 구성할 수 있습니다. VM에서 [InfiniBand를 사용](enable-infiniband.md)하고 [MPI를 설정](setup-mpi.md)하는 방법에 대해 자세히 알아보세요.
- [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/tree/master/centos)를 통해 기본 CentOS Marketplace 이미지에서 CentOS-HPC 버전 7.6 이상의 VM 이미지를 만드는 데 사용되는 스크립트도 사용할 수 있습니다.
 
### <a name="ubuntu-vm-images"></a>Ubuntu VM 이미지
Marketplace의 기본 Ubuntu Server 16.04 LTS, 18.04 LTS 및 20.04 LTS VM 이미지는 SR-IOV/SR-IOV 이외 [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances) 모두에 대해 지원됩니다. VM에서 [InfiniBand를 사용](enable-infiniband.md)하고 [MPI를 설정](setup-mpi.md)하는 방법에 대해 자세히 알아보세요.
- Ubuntu VM 이미지에서 InfiniBand을 사용하도록 설정하는 지침은 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351)에 나와 있습니다.
- 기본 Ubuntu Marketplace 이미지에서 Ubuntu 18.04 및 20.04 LTS 기반 HPC VM 이미지를 만드는 데 사용되는 스크립트는 [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/tree/master/ubuntu)에 있습니다.

> [!NOTE]
> Mellanox OFED 5.1 이상에서는 FDR InfiniBand를 이용하는 SR-IOV 사용 N 시리즈 VM 크기(예: NCv3)에 ConnectX3-Pro InfiniBand 카드를 지원하지 않습니다. ConnectX3-Pro 카드를 이용하는 N 시리즈 VM의 LTS Mellanox OFED 버전 4.9-0.1.7.0 이하 버전을 사용하세요. 자세한 내용은 [Linux InfiniBand 드라이버](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)를 참조하세요.

### <a name="suse-linux-enterprise-server-vm-images"></a>SUSE Linux Enterprise Server VM 이미지
Marketplace의 HPC용 SLES 12 SP3, HPC용 SLES 12 SP3(프리미엄), HPC용 SLES 12 SP1, HPC용 SLES 12 SP1(프리미엄), SLES 12 SP4 및 SLES 15 VM 이미지가 지원됩니다. 이러한 VM 이미지에는 RDMA용 Network Direct 드라이버(SR-IOV가 아닌 VM 크기) 및 Intel MPI 버전 5.1이 사전로드되어 있습니다. VM에서 [MPI를 설정](setup-mpi.md)하는 방법에 대해 자세히 알아보세요.

## <a name="optimize-vms"></a>VM 최적화

다음은 VM의 성능을 개선하기 위한 몇 가지 최적화 설정(선택 사항)입니다.

### <a name="update-lis"></a>LIS 업데이트

기능이나 성능에 필요한 경우 지원되는 OS 배포판에서 [LIS(Linux Integration Services) 드라이버](../../linux/endorsed-distros.md)를 설치하거나 업데이트할 수 있습니다. 특히 사용자 지정 이미지 또는 이전 OS 버전(예: CentOS/RHEL 6.x 또는 7.x 이전 버전)을 사용하여 배포합니다.

```bash
wget https://aka.ms/lis
tar xzf lis
pushd LISISO
./upgrade.sh
```

### <a name="reclaim-memory"></a>메모리 회수

원격 메모리 액세스를 방지하기 위해 메모리를 자동으로 회수하여 성능을 개선합니다.

```bash
echo 1 >/proc/sys/vm/zone_reclaim_mode
```

VM을 다시 부팅한 후 이 작업을 유지하려면 다음을 수행합니다.

```bash
echo "vm.zone_reclaim_mode = 1" >> /etc/sysctl.conf sysctl -p
```

### <a name="disable-firewall-and-selinux"></a>방화벽 및 SELinux 사용 안 함

```bash
systemctl stop iptables.service
systemctl disable iptables.service
systemctl mask firewalld
systemctl stop firewalld.service
systemctl disable firewalld.service
iptables -nL
sed -i -e's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
```

### <a name="disable-cpupower"></a>Cpupower 사용 안 함

```bash
service cpupower status
if enabled, disable it:
service cpupower stop
sudo systemctl disable cpupower
```

### <a name="configure-walinuxagent"></a>WALinuxAgent 구성

```bash
sed -i -e 's/# OS.EnableRDMA=y/OS.EnableRDMA=y/g' /etc/waagent.conf
```
필요에 따라 WALinuxAgent를 사전 작업 단계로 사용하지 않도록 설정하고, HPC 워크로드에 대한 VM 리소스 가용성을 극대화하기 위해 사후 작업을 다시 사용하도록 설정할 수 있습니다.


## <a name="next-steps"></a>다음 단계

- InfiniBand 사용 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM에서 [InfiniBand을 사용하도록 설정](enable-infiniband.md)하는 방법에 대해 자세히 알아봅니다.
- VM에서 [지원되는 다양한 MPI 라이브러리](setup-mpi.md)를 설치하고 실행하는 방법에 대해 자세히 알아봅니다.
- [HBv3 시리즈 개요](hbv3-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토합니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
