---
title: HPC 및 GPU VM -Azure Virtual Machines의 알려진 문제 해결 | Microsoft Docs
description: Azure에서 HPC 및 GPU VM 크기와 관련하여 알려진 문제를 해결하는 방법에 대해 알아봅니다.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 04/28/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 9b0e33b80cf5bd8963efa036dc76ae9f634fee66
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114460541"
---
# <a name="known-issues-with-h-series-and-n-series-vms"></a>H 시리즈 및 N 시리즈 VM의 알려진 문제

이 문서에서는 [H 시리즈](../../sizes-hpc.md) 및 [N 시리즈](../../sizes-gpu.md) HPC 및 GPU VM을 사용할 때 최근 발생한 일반적인 문제 및 해당 솔루션을 제공하고자 합니다.

## <a name="cache-topology-on-standard_hb120rs_v3"></a>Standard_HB120rs_v3의 캐시 토폴로지
`lstopo`는 Standard_HB120rs_v3 VM 크기에 잘못된 캐시 토폴로지를 표시합니다. NUMA당 32MB L3만 표시 가능하기 때문일 수 있습니다. 그러나 실제로는 전체 VM에 동일한 480MB의 L3을 다른 제한된 코어 HBv3 VM 크기와 함께 사용할 수 있기 때문에 실제로 NUMA당 120MB L3이 예상대로 존재합니다. 올바른 값을 표시하지 못하는 외관상의 오류로, 워크로드에는 영향이 없습니다.

## <a name="qp0-access-restriction"></a>qp0 액세스 제한
보안 취약성을 유발할 수 있는 낮은 수준의 하드웨어 액세스를 방지하기 위해 큐 쌍 0은 게스트 VM에 액세스할 수 없습니다. 이는 일반적으로 ConnectX InfiniBand NIC의 관리와 관련된 작업에만 영향을 주며 ibdiagnet과 같은 일부 InfiniBand 진단을 실행하지만 최종 사용자 애플리케이션은 실행하지 않습니다.

## <a name="mofed-installation-on-ubuntu"></a>Ubuntu에 설치하는 MOFED
커널 버전 `5.4.0-1039-azure #42` 이상의 Ubuntu-18.04 기반 Marketplace VM 이미지에서 일부 이전 Mellanox OFED가 호환되지 않아 VM 부팅 시간이 최대 30분까지 증가하는 경우가 있습니다. 이 문제는 Mellanox OFED 버전 5.2-1.0.4.0과 5.2-2.2.0.0 모두 보고되었습니다. 이 문제는 Mellanox OFED 5.3-1.0.0.1에서 해결되었습니다.
호환되지 않는 OFED를 사용해야 하는 경우 솔루션은 **Canonical:UbuntuServer:18_04-lts-gen2:18.04.202101290** Marketplace VM 이미지 또는 이전 버전을 사용하고 커널을 업데이트하지 않는 것입니다.

## <a name="mpi-qp-creation-errors"></a>MPI QP 생성 오류
MPI 워크로드를 실행 중인 경우 아래와 같은 InfiniBand QP 생성 오류가 발생하면 VM을 다시 부팅하고 워크로드를 다시 시도하는 것이 좋습니다. 이 문제는 향후 수정될 예정입니다.

```bash
ib_mlx5_dv.c:150  UCX  ERROR mlx5dv_devx_obj_create(QP) failed, syndrome 0: Invalid argument
```

다음과 같이 문제가 관찰될 때 최대 큐-쌍 개수 값을 확인할 수 있습니다.
```bash
[user@azurehpc-vm ~]$ ibv_devinfo -vv | grep qp
max_qp: 4096
```

## <a name="accelerated-networking-on-hb-hc-hbv2-and-ndv2"></a>HB, HC, HBv2 및 NDv2의 가속화된 네트워킹

[Azure 가속화된 네트워킹](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)은 이제 RDMA와 InfiniBand 지원 및 SR-IOV 지원 VM 크기 [HB](../../hb-series.md), [HC](../../hc-series.md), [HBv2](../../hbv2-series.md), 및 [NDv2](../../ndv2-series.md)에서 사용할 수 있습니다. 이제 이 기능을 통해 최대 30Gbps까지 처리량이 개선되며 Azure 이더넷 네트워크에서 대기 시간이 짧아집니다. InfiniBand 네트워크를 통한 RDMA 기능과는 별개이지만, InfiniBand를 통해 작업을 실행할 때 이 기능에 대한 일부 플랫폼 변경이 특정 MPI 구현의 동작에 영향을 줄 수 있습니다. 특히 일부 VM의 InfiniBand 인터페이스에는 약간 다른 이름(이전 mlx5_0와 반대인 mlx5_1)이 포함될 수 있으며, 이로 인해 특히 UCX 인터페이스(일반적으로 OpenMPI 및 HPC-X 포함)를 사용하는 경우 MPI 명령줄을 조정해야 할 수 있습니다. 가장 간단한 솔루션은 현재 CentOS-HPC VM 이미지에서 최신 HPC-X를 사용하거나 필요하지 않은 경우 가속화된 네트워킹을 사용하지 않도록 설정하는 것입니다.
이에 대한 자세한 내용은 이 [TechCommunity 문서](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-and-hbv2/ba-p/2067965)에서 발견된 문제를 해결하는 방법에 대한 지침을 통해 확인할 수 있습니다.

## <a name="infiniband-driver-installation-on-non-sr-iov-vms"></a>비SR-IOV VM에 InfiniBand 드라이버 설치

현재 H16r, H16mr 및 NC24r은 SR-IOV를 사용하도록 설정되어 있지 않습니다. InfiniBand 스택 분기에 대한 자세한 내용은 [Azure VM 크기 - HPC](../../sizes-hpc.md#rdma-capable-instances)를 참조하세요.
SR-IOV를 사용하도록 설정된 VM 크기에서 OFED 드라이버를 사용하여 InfiniBand를 구성할 수 있지만 SR-IOV가 아닌 VM 크기에는 ND 드라이버가 필요합니다. 이 IB 지원은 [CentOS, RHEL 및 Ubuntu](configure.md)에서 문제 없이 사용할 수 있습니다.

## <a name="duplicate-mac-with-cloud-init-with-ubuntu-on-h-series-and-n-series-vms"></a>H 시리즈 및 N 시리즈 VM에서 Ubuntu로 클라우드 Cloud-Init을 사용하여 MAC 복제

Ubuntu VM 이미지에서 IB 인터페이스를 표시하려고 할 때 Cloud-Init에서 발생하는 알려진 문제가 있습니다. 이 문제는 VM을 다시 부팅하거나 일반화 후 VM 이미지를 만들려고 할 때 발생할 수 있습니다. VM 부팅 로그에 다음과 같은 오류가 표시될 수 있습니다.
```console
“Starting Network Service...RuntimeError: duplicate mac found! both 'eth1' and 'ib0' have mac”.
```

이 "Ubuntu에서 Cloud-Init을 사용하는 MAC 중복"은 알려진 문제입니다. 이는 최신 커널에서 해결될 예정입니다. 문제가 발생하는 경우 해결 방법은 다음과 같습니다.
1) (Ubuntu 18.04) 마켓플레이스 VM 이미지 배포
2) IB를 사용하도록 설정하는 데 필요한 소프트웨어 패키지 설치([지침은 여기에 있음](https://techcommunity.microsoft.com/t5/azure-compute/configuring-infiniband-for-ubuntu-hpc-and-gpu-vms/ba-p/1221351))
3) waagent.conf를 편집하여 EnableRDMA=y 변경
4) Cloud-Init에서 네트워킹 사용 안 함
    ```console
    echo network: {config: disabled} | sudo tee /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
    ```
5) Cloud-Init에서 생성한 netplan의 네트워킹 구성 파일을 편집하여 MAC을 제거합니다.
    ```console
    sudo bash -c "cat > /etc/netplan/50-cloud-init.yaml" <<'EOF'
    network:
      ethernets:
        eth0:
          dhcp4: true
      version: 2
    EOF
    ```

## <a name="dram-on-hb-series-vms"></a>HB 시리즈 VM의 DRAM

HB-시리즈 VM은 현재 게스트 VM에 228GB의 RAM만 노출할 수 있습니다. 마찬가지로 HBv2의 경우 458GB, HBv3 VM의 경우 448GB입니다. 이는 게스트 VM에 예약된 AMD CCX의 로컬 DRAM(NUMA 도메인)에 페이지가 할당되지 않도록 Azure 하이퍼바이저의 알려진 제한 사항이 있기 때문입니다.

## <a name="gss-proxy"></a>GSS 프록시

GSS 프록시의 CentOS/RHEL 7.5에는 NFS와 함께 사용할 때 심각한 성능 및 응답 성능 저하를 유발할 수 있는 알려진 버그가 있습니다. 다음을 사용하여 완화할 수 있습니다.

```console
sed -i 's/GSS_USE_PROXY="yes"/GSS_USE_PROXY="no"/g' /etc/sysconfig/nfs
```

## <a name="cache-cleaning"></a>캐시 정리

HPC 시스템에서는 작업이 완료된 후 다음 사용자에게 동일한 노드가 할당되기 전에 메모리를 정리하는 것이 유용한 경우가 많습니다. Linux에서 애플리케이션을 실행한 후에는 애플리케이션을 실행하지 않더라도 버퍼 메모리가 늘어나면 사용 가능한 메모리가 감소하는 것을 알 수 있습니다.

![정리 전 명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-1.png)

`numactl -H`를 사용하면 메모리가 버퍼링되는 NUMAnode가 표시됩니다. Linux에서 사용자는 세 가지 방법으로 캐시를 정리하여 버퍼링된 메모리 또는 캐시된 메모리를 '임의'로 되돌릴 수 있습니다. 루트이거나 sudo 권한이 있어야 합니다.

```console
echo 1 > /proc/sys/vm/drop_caches [frees page-cache]
echo 2 > /proc/sys/vm/drop_caches [frees slab objects e.g. dentries, inodes]
echo 3 > /proc/sys/vm/drop_caches [cleans page-cache and slab objects]
```

![정리 후 명령 프롬프트의 스크린샷](./media/known-issues/cache-cleaning-2.png)

## <a name="kernel-warnings"></a>커널 경고

Linux에서 HB 시리즈 VM을 부팅할 때 다음 커널 경고 메시지를 무시할 수 있습니다. 이는 시간이 지남에 따라 주소가 지정되는 Azure 하이퍼바이저의 알려진 제한 때문입니다.

```console
[  0.004000] WARNING: CPU: 4 PID: 0 at arch/x86/kernel/smpboot.c:376 topology_sane.isra.3+0x80/0x90
[  0.004000] sched: CPU #4's llc-sibling CPU #0 is not on the same node! [node: 1 != 0]. Ignoring dependency.
[  0.004000] Modules linked in:
[  0.004000] CPU: 4 PID: 0 Comm: swapper/4 Not tainted 3.10.0-957.el7.x86_64 #1
[  0.004000] Hardware name: Microsoft Corporation Virtual Machine/Virtual Machine, BIOS 090007 05/18/2018
[  0.004000] Call Trace:
[  0.004000] [<ffffffffb8361dc1>] dump_stack+0x19/0x1b
[  0.004000] [<ffffffffb7c97648>] __warn+0xd8/0x100
[  0.004000] [<ffffffffb7c976cf>] warn_slowpath_fmt+0x5f/0x80
[  0.004000] [<ffffffffb7c02b34>] ? calibrate_delay+0x3e4/0x8b0
[  0.004000] [<ffffffffb7c574c0>] topology_sane.isra.3+0x80/0x90
[  0.004000] [<ffffffffb7c57782>] set_cpu_sibling_map+0x172/0x5b0
[  0.004000] [<ffffffffb7c57ce1>] start_secondary+0x121/0x270
[  0.004000] [<ffffffffb7c000d5>] start_cpu+0x5/0x14
[  0.004000] ---[ end trace 73fc0e0825d4ca1f ]---
```


## <a name="next-steps"></a>다음 단계

- [HB 시리즈 개요](hb-series-overview.md) 및 [HC 시리즈 개요](hc-series-overview.md)를 검토하여 성능 및 확장성을 높일 수 있도록 워크로드를 최적으로 구성하는 방법을 알아보세요.
- [Azure Compute 기술 커뮤니티 블로그](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute)에서 최신 공지 사항, HPC 워크로드 예제 및 성능 결과에 대해 읽어보세요.
- HPC 워크로드를 실행하는 상위 수준의 아키텍처 보기는 [Azure의 HPC(고성능 컴퓨팅)](/azure/architecture/topics/high-performance-computing/)를 참조하세요.
