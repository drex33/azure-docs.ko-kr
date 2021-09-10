---
title: NV 시리즈 마이그레이션 가이드
description: NV 시리즈 마이그레이션 가이드
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: e91da8b052ba9ecce4a345c610b2299de22de1b3
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122538295"
---
#  <a name="nv-series-migration-guide"></a>NV 시리즈 마이그레이션 가이드 

Microsoft Azure 데이터 센터에서 더 강력한 GPU VM 크기를 사용할 수 있게 될 때 워크로드를 평가하고 NV 및 NV_Promo 시리즈에서 VM(가상 머신)을 마이그레이션하는 것이 좋습니다. 이 레거시 VM은 저렴한 비용으로 더 나은 성능을 제공하기 위해 NVsv3 및 NVasv4 시리즈와 같은 새 VM 시리즈로 마이그레이션할 수 있습니다. NVsv3 VM 시리즈는 AMD Radeon Instinct MI25 GPU를 기반으로 하고 NVasv4 시리즈는 Nvidia M60 GPU를 기반으로 합니다.  NV, NV_Promo 시리즈와 최신 NVsv3, NVasv4 간 주요 차이점은 향상된 성능, 프리미엄 스토리지 지원, 다중 GPU 구성에 대한 적은 수의 GPU 크기 중에서 선택하는 옵션 등입니다. NVsv3 및 NVasv4 시리즈는 둘 다 더 최신 코어와 더 큰 용량을 포함합니다.  

다음 섹션에서는 레거시 NV 시리즈와 NVsv3 및 NVv4 시리즈 간 차이점을 요약합니다.
 
 ## <a name="nvsv3-series"></a>NVsv3 시리즈 

NVv3 시리즈 가상 머신은 Intel E5-2690 v4(Broadwell) CPU와 Intel 하이퍼 스레딩 기술이 적용된 NVIDIA Tesla M60 GPU와 NVIDIA GRID 기술을 기반으로 합니다. 이러한 가상 머신은 고객이 데이터를 시각화하고 결과를 시뮬레이트하여 보고 CAD에서 작업하거나 콘텐츠를 렌더링 및 스트림하려고 하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다. 또한 이러한 가상 머신은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수 있습니다. NVv3 가상 머신은 Premium Storage를 지원하고 NV 시리즈에 비해 두 배의 시스템 메모리(RAM)를 제공합니다. 최신 사양은 [GPU 가속 컴퓨팅 VM 크기: NVsv3 시리즈](nvv3-series.md)를 참조하세요.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU: 12(+6) <br> 메모리: GiB 112(+56) <br> 임시 스토리지(SSD) GiB: 320(-20) <br> 최대 데이터 디스크: 12(-12) <br> 가속화된 네트워킹: 예 <br> Premium Storage: 예  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24(+12) <br>메모리: GiB 224(+112) <br>임시 스토리지(SSD) GiB: 640(-40)<br>최대 데이터 디스크: 24(-24)<br>가속화된 네트워킹: 예 <br>Premium Storage: 예   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48(+24) <br>메모리: GiB 448(+224) <br>임시 스토리지(SSD) GiB: 1280(-160) <br>최대 데이터 디스크: 32(-32) <br>가속화된 네트워킹: 예 <br>Premium Storage: 예   |

## <a name="nvsv4-series"></a>NVsv4 시리즈 

NVv4 시리즈 가상 머신은 AMD Radeon Instinct MI25 GPU 및 AMD Epyc 7V12(Rome) CPU를 기반으로 합니다. NVv4 시리즈 Azure는 부분 GPU를 사용하는 가상 머신을 소개합니다. GPU 가속화된 그래픽 애플리케이션 및 가상 데스크톱에 적합한 크기의 가상 머신을 2GiB 프레임 버퍼가 있는 GPU의 1/8에서 시작하여 16GiB 프레임 버퍼가 있는 전체 GPU까지 선택하세요. NVv4 가상 머신은 현재 Windows 게스트 운영 체제만 지원합니다. 최신 사양은 [GPU 가속 컴퓨팅 VM 크기: NVsv4 시리즈](nvv4-series.md)를 참조하세요.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU: 16(+10) <br>메모리: GiB 56  <br>임시 스토리지(SSD) GiB: 352(+12) <br>최대 데이터 디스크: 16(-8) <br>가속화된 네트워킹: 예 <br>Premium Storage: 예   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32(+20) <br>메모리: GiB 112 <br>임시 스토리지(SSD) GiB: 704(+24) <br>최대 데이터 디스크: 32(+16)<br>가속화된 네트워킹: 예 <br>Premium Storage: 예   |
|Standard_NV24 <br> Standard_NV24_Promo |해당 없음  | 해당 없음  |

## <a name="migration-steps"></a>마이그레이션 단계 
 

일반 변경 내용 

1. 마이그레이션할 시리즈와 크기를 선택합니다. 

2. 대상 VM 시리즈에 대한 할당량을 가져옵니다. 

3. 현재 NV 시리즈 VM 크기를 대상 크기로 조정합니다. 

  대상 크기가 NVv4인 경우 Nvidia GPU 드라이버를 제거하고 AMD GPU 드라이버를 설치해야 합니다. 
  
## <a name="breaking-changes"></a>주요 변경 내용 

## <a name="select-target-size-for-migration"></a>마이그레이션할 대상 크기 선택 
현재 사용량을 평가한 후 필요한 GPU VM 유형을 결정합니다. 워크로드 요구 사항에 따라 선택할 수 있는 몇 가지 항목이 있습니다. 선택하는 방법은 다음과 같습니다.  

워크로드가 그래픽/시각화이고 Nvidia GPU 사용에 대한 강한 종속성이 있는 경우 NVsv3 시리즈로 마이그레이션하는 것이 좋습니다.  

워크로드가 그래픽/시각화이고 특정 유형의 GPU에 대한 강한 종속성이 없는 경우 NVsv3 또는 NVVasv4 시리즈를 권장합니다. 
> [!Note]
>모범 사례는 비용과 성능에 따라 VM 크기를 선택하는 것입니다. 이 가이드의 권장 사항은 NV 및 NV_Promo 크기와 다른 VM 시리즈의 가장 가까운 일치 항목에 대한 성능 메트릭의 일대일 비교를 기반으로 합니다.
>적합한 크기를 결정하기 전에 Azure 가격 계산기를 사용하여 비용을 비교합니다.

## <a name="get-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대한 할당량 가져오기 

가이드에 따라 [VM 제품군에 의한 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다. 마이그레이션을 위해 선택한 대상 VM 크기에 따라 VM 제품군 이름으로 NVsv3 시리즈나 NVv4 시리즈를 선택합니다.
## <a name="resize-the-current-virtual-machine"></a>현재 가상 머신 크기 조정
[가상 머신의 크기는 Azure Portal 또는 PowerShell을 통해 조정](./windows/resize-vm.md)할 수 있습니다. [가상 머신의 크기는 Azure CLI를 사용하여 조정](./linux/change-vm-size.md)할 수도 있습니다. 

## <a name="faq"></a>FAQ
**Q:** 대상 VM 크기에 어떤 GPU 드라이버를 사용해야 하나요? 

**A:** NVsv3 시리즈의 경우 [Nvidia GRID 드라이버](./windows/n-series-driver-setup.md)를 사용합니다. NVv4의 경우 [AMD GPU 드라이버](./windows/n-series-amd-driver-setup.md)를 사용합니다. 

**Q:** 현재 NVIDIA GPU 드라이버 확장을 사용합니다. 대상 VM 크기에서 작동할까요? 

**A:** 현재 [Nvidia 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 NVsv3에서 작동합니다. 대상 VM 크기가 is NVv4인 경우 [AMD GPU 드라이버 확장](./extensions/hpccompute-amd-gpu-windows.md)을 사용합니다. 
       
**Q:** CUDA에 대한 종속성이 있는 경우 어떤 대상 VM 시리즈를 사용해야 하나요? 

 **A:** NVv3은 CUDA를 지원합니다. AMD GPU가 있는 NVv4 VM 시리즈는 CUDA를 지원하지 않습니다.  
