---
title: NCas T4 v3 시리즈
description: NCas T4 v3 시리즈 VM의 사양입니다.
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: e023eacd173ce23e3f88fa97f8c0127132c953ea
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108744536"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 시리즈 

NCasT4_v3 시리즈 가상 머신은 [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 및 AMD EPYC 7V12(Rome) CPU로 구동됩니다. VM에는 최대 4개의 NVIDIA T4 GPU(각각 16GB 메모리 포함), 최대 64개의 비다중 스레드 AMD EPYC 7V12(Rome) 프로세서 코어 및 440GiB의 시스템 메모리가 탑재되어 있습니다. 이러한 가상 머신은 AI 서비스(예: 사용자 생성 요청에 대한 실시간 추론)를 배포하는 데 적합하며 NVIDIA의 GRID 드라이버 및 가상 GPU 기술을 사용하는 대화형 그래픽 및 시각화 워크로드에도 적합합니다. NCasT4_v3 시리즈에서는 TensorRT, Caffe, ONNX 및 기타 프레임워크를 기반으로 하는 표준 GPU 컴퓨팅 워크로드 또는 OpenGL 및 DirectX 기반의 GPU 가속 그래픽 애플리케이션을 사용자와 근접한 위치에서 경제적으로 배포할 수 있습니다.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disk): 지원됨(가용성, 사용량 및 성능에 대한 [자세한 정보](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
[실시간 마이그레이션](maintenance-and-updates.md): 지원되지 않음<br>
[메모리 보존 업데이트](maintenance-and-updates.md): 지원되지 않음<br>
[VM 생성 지원](generation-2.md): 1세대 및 2세대<br>
[가속화된 네트워킹](../virtual-network/create-vm-accelerated-networking-cli.md): 지원됨<br>
[임시 OS 디스크](ephemeral-os-disks.md): 지원됨([미리 보기](ephemeral-os-disks.md#preview---ephemeral-os-disks-can-now-be-stored-on-temp-disks))<br>
NVIDIA NVLink Interconnect: 지원되지 않음<br>
<br>

| 크기 | vCPU | 메모리: GiB | 임시 스토리지(SSD) GiB | GPU | GPU 메모리: GiB | 최대 데이터 디스크 수 | 최대 NIC 수 / 예상 네트워크 대역폭(Mbps) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NC4as_T4_v3 |4 |28 |180 | 1 | 16 | 8 | 2 / 8000 |
| Standard_NC8as_T4_v3 |8 |56 |360 | 1 | 16 | 16 | 4 / 8000  |
| Standard_NC16as_T4_v3 |16 |110 |360 | 1 | 16 | 32 | 8 / 8000  |
| Standard_NC64as_T4_v3 |64 |440 |2880 | 4 | 64 | 32 | 8/32000  |


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Windows 또는 Linux를 실행하는 Azure NCasT4_v3 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA GPU 드라이버를 설치해야 합니다.

NVIDIA GPU 드라이버를 수동으로 설치하려는 경우 지원되는 운영 체제, 드라이버, 설치 및 확인 단계는 [Windows용 N 시리즈 GPU 드라이버 설치](./windows/n-series-driver-setup.md)를 참조하세요.

Azure NVIDIA GPU 드라이버 확장은 NCasT4_v3 시리즈 VM에 CUDA 드라이버를 배포합니다. 그래픽 및 시각화 워크로드를 위해서는 Azure에서 지원하는 GRID 드라이버를 수동으로 설치하세요.

## <a name="other-sizes"></a>기타 크기

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
