---
title: NCas T4 v3 시리즈
description: NCas T4 v3 시리즈 VM의 사양입니다.
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
author: vikancha-MSFT
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: vikancha
ms.openlocfilehash: 62108e46b748552fc22a8dc1e86ea78b3cc5e4e5
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124774161"
---
# <a name="ncast4_v3-series"></a>NCasT4_v3 시리즈 

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

NCasT4_v3 시리즈 가상 머신은 [NVIDIA Tesla T4](https://www.nvidia.com/en-us/data-center/tesla-t4/) GPU 및 AMD EPYC 7V12(Rome) CPU로 구동됩니다. VM은 최대 4개의 NVIDIA T4 GPU와 각각 16GB의 메모리, 최대 64개의 비 멀티스레드 AMD EPYC 7V12(Rome) 프로세서 코어(기본 주파수 2.45GHz, 전체 코어 최대 주파수 3.1GHz 및 단일 코어 피크를 갖추고 있습니다. 3.3GHz의 주파수) 및 440GiB의 시스템 메모리. 이러한 가상 머신은 사용자 생성 요청의 실시간 추론 또는 NVIDIA의 GRID 드라이버 및 가상 GPU 기술을 사용하는 대화형 그래픽 및 시각화 워크로드와 같은 AI 서비스 배포에 이상적입니다. CUDA, TensorRT, Caffe, ONNX 및 기타 프레임워크를 기반으로 하는 표준 GPU 컴퓨팅 워크로드 또는 OpenGL 및 DirectX를 기반으로 하는 GPU 가속 그래픽 애플리케이션을 NCasT4_v3 시리즈에서 사용자와 가까운 위치에서 경제적으로 배포할 수 있습니다.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): 지원됨<br>
[Premium Storage 캐싱](premium-storage-performance.md): 지원됨<br>
[Ultra Disks](disks-types.md#ultra-disk): 지원됨(가용성, 사용량 및 성능에 대해 [자세히 알아보기](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312)) <br>
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




## <a name="supported-operating-systems-and-drivers"></a>지원되는 운영 체제 및 드라이버

Windows 또는 Linux를 실행하는 Azure NCasT4_v3 시리즈 VM의 GPU 기능을 최대한 활용하려면 NVIDIA GPU 드라이버를 설치해야 합니다.

NVIDIA GPU 드라이버를 수동으로 설치하려는 경우 지원되는 운영 체제, 드라이버, 설치 및 확인 단계는 [Windows용 N 시리즈 GPU 드라이버 설치](./windows/n-series-driver-setup.md)를 참조하세요.

Azure NVIDIA GPU 드라이버 확장은 NCasT4_v3 시리즈 VM에 CUDA 드라이버를 배포합니다. 그래픽 및 시각화 워크로드를 위해서는 Azure에서 지원하는 GRID 드라이버를 수동으로 설치하세요.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>기타 크기 및 정보

- [범용](sizes-general.md)
- [메모리에 최적화](sizes-memory.md)
- [Storage에 최적화](sizes-storage.md)
- [GPU에 최적화](sizes-gpu.md)
- [고성능 컴퓨팅](sizes-hpc.md)
- [이전 세대](sizes-previous-gen.md)

가격 계산기: [가격 계산기](https://azure.microsoft.com/pricing/calculator/)

디스크 유형에 대한 자세한 내용은 [Azure에서 사용할 수 있는 디스크 유형](disks-types.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

[ACU(Azure 컴퓨팅 단위)](acu.md)가 Azure SKU 간의 Compute 성능을 비교하는 데 어떻게 도움을 줄 수 있는지 알아봅니다.
