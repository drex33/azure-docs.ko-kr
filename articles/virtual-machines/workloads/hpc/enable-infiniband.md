---
title: HPC VM에서 InfiniBand 사용 - Azure Virtual Machines | Microsoft Docs
description: Azure HPC VM에서 InfiniBand를 사용하도록 설정하는 방법을 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 563cbf412fa8bb522b835fe41849f8358f5303fb
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689244"
---
# <a name="enable-infiniband"></a>InfiniBand 사용

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

[RDMA 지원](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM은 낮은 대기 시간과 높은 대역폭 InfiniBand 네트워크를 통해 통신합니다. 이러한 상호 연결에 대한 RDMA 기능은 분산 노드 HPC 및 AI 워크로드의 확장성과 성능을 향상하는 데 매우 중요합니다. InfiniBand 지원 H 시리즈 및 N 시리즈 VM은 최적화되고 일관적인 RDMA 성능을 제공하기 위해 지름이 작은 비중단 팻 트리에서 연결됩니다.

지원되는 VM 크기에서 InfiniBand를 사용하도록 설정하는 방법은 다양합니다.

## <a name="vm-images-with-infiniband-drivers"></a>InfiniBand 드라이버를 사용하는 VM 이미지
Marketplace에서 지원되는 VM 이미지 목록은 [VM 이미지](configure.md#vm-images)를 참조하세요. 이는 InfiniBand 드라이버(SR-IOV 또는 SR-IOV 이외 VM용)를 이용하여 미리 로드된 상태로 제공되거나, [RDMA 지원 VM](../../sizes-hpc.md#rdma-capable-instances)에 적절한 드라이버를 사용하여 구성할 수 있습니다.  Marketplace의 [CentOS-HPC](configure.md#centos-hpc-vm-images) 및 [Ubuntu-HPC](configure.md#ubuntu-hpc-vm-images) VM 이미지는 사용하기에 가장 쉬운 방법입니다.

## <a name="infiniband-driver-vm-extensions"></a>InfiniBand 드라이버 VM 확장
Linux에서는 [InfiniBandDriverLinux VM 확장](../../extensions/hpc-compute-infiniband-linux.md)을 사용하여 Mellanox OFED 드라이버를 설치하고, SR-IOV 사용 H 시리즈/N 시리즈 VM에서 InfiniBand를 사용하도록 설정할 수 있습니다.

Windows에서는 [InfiniBandDriverWindows VM 확장](../../extensions/hpc-compute-infiniband-windows.md)이 RDMA 연결용 Windows 네트워크 다이렉트 드라이버(SR-IOV 이외 VM) 또는 Mellanox OFED 드라이버(SR-IOV VM)를 설치합니다. 특정 A8/A9 인스턴스 배포 시 HpcVmDrivers 확장이 자동으로 추가됩니다. HpcVmDrivers VM 확장은 더 이상 사용되지 않고, 업데이트되지도 않습니다.

VM에 VM 확장을 추가해야 하는 경우 [Azure PowerShell](/powershell/azure/) cmdlet을 사용할 수 있습니다. 자세한 내용은 [가상 머신 확장 및 기능](../../extensions/overview.md)을 참조하세요. 또한 [클래식 배포 모델](/previous-versions/azure/virtual-machines/windows/classic/agents-and-extensions-classic)에서 배포된 VM의 확장으로 작업할 수 있습니다.

## <a name="manual-installation"></a>수동 설치
[OFED(Mellanox OpenFabrics 드라이버)](https://www.mellanox.com/products/InfiniBand-VPI-Software)는 [SR-IOV 사용](../../sizes-hpc.md#rdma-capable-instances) [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) VM에 수동으로 설치할 수 있습니다.

### <a name="linux"></a>Linux
아래의 예제를 사용하여 [Linux용 OFED 드라이버](https://www.mellanox.com/products/infiniband-drivers/linux/mlnx_ofed)를 설치할 수 있습니다. 여기에 나와 있는 예제는 RHEL/CentOS에서 사용되지만, 그 단계는 일반적으로 사용되며 Ubuntu(16.04, 18.04 19.04, 20.04) 및 SLES(12 SP4, 15)와 같이 호환되는 Linux 운영 체제에서 사용할 수 있습니다. 다른 배포판에 대한 추가 예제는 [azhpc-images 리포지토리](https://github.com/Azure/azhpc-images/blob/master/ubuntu/ubuntu-18.x/ubuntu-18.04-hpc/install_mellanoxofed.sh)에 있습니다. 또한 받은 편지함 드라이버도 작동하지만, Mellanox OFED 드라이버에서 더 많은 기능을 제공합니다.

```bash
MLNX_OFED_DOWNLOAD_URL=http://content.mellanox.com/ofed/MLNX_OFED-5.0-2.1.8.0/MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz
# Optionally verify checksum
wget --retry-connrefused --tries=3 --waitretry=5 $MLNX_OFED_DOWNLOAD_URL
tar zxvf MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64.tgz

KERNEL=( $(rpm -q kernel | sed 's/kernel\-//g') )
KERNEL=${KERNEL[-1]}
# Uncomment the lines below if you are running this on a VM
#RELEASE=( $(cat /etc/centos-release | awk '{print $4}') )
#yum -y install http://olcentgbl.trafficmanager.net/centos/${RELEASE}/updates/x86_64/kernel-devel-${KERNEL}.rpm
yum install -y kernel-devel-${KERNEL}
./MLNX_OFED_LINUX-5.0-2.1.8.0-rhel7.7-x86_64/mlnxofedinstall --kernel $KERNEL --kernel-sources /usr/src/kernels/${KERNEL} --add-kernel-support --skip-repo
```

### <a name="windows"></a>Windows
Windows의 경우 [Windows용 MELLANOX OFED 드라이버](https://www.mellanox.com/products/adapter-software/ethernet/windows/winof-2)를 다운로드하여 설치합니다.

## <a name="enable-ip-over-infiniband-ib"></a>IPoIB(IP over InfiniBand) 사용
MPI 작업을 실행하려는 경우 일반적으로 IPoIB가 필요하지 않습니다. MPI 라이브러리는 IB 통신용 동사 인터페이스를 사용합니다(MPI 라이브러리의 TCP/IP 채널을 명시적으로 사용하지 않는 경우). 그러나 통신에 TCP/IP를 사용하는 앱이 있는 경우 IB를 통해 실행하려면 IB 인터페이스를 통해 IPoIB를 사용할 수 있습니다. RHEL/CentOS의 경우 다음 명령을 이용해 IPoIB를 사용하도록 설정합니다.

```bash
sudo sed -i -e 's/# OS.EnableRDMA=n/OS.EnableRDMA=y/g' /etc/waagent.conf
sudo systemctl restart waagent
```

## <a name="next-steps"></a>다음 단계

- VM에서 [지원되는 다양한 MPI 라이브러리](setup-mpi.md)를 설치하고 실행하는 방법에 대해 자세히 알아봅니다.
- [HBv3 시리즈 개요](hbv3-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토합니다.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
