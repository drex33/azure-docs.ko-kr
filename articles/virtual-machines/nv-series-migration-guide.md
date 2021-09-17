---
title: NV 시리즈 마이그레이션 가이드
description: NV 시리즈 마이그레이션 가이드
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 3fc9607a6882ddae42c2606abb60903dbcb05108
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123436467"
---
# <a name="nv-series-migration-guide"></a>NV 시리즈 마이그레이션 가이드

더 강력한 GPU VM 크기를 Azure 데이터 센터에서 사용할 수 있게 되 면 작업을 평가 하 고 NV 및 NV_Promo 시리즈에서 Vm (가상 머신)을 마이그레이션합니다. 이러한 레거시 Vm을 새 VM 시리즈 (예: NVsv3 및 NVasv4)로 마이그레이션하여 비용이 절감 되는 성능을 향상 시킬 수 있습니다. NVsv3 VM 시리즈는 Nvidia M60 Gpu에 의해 구동 됩니다. NVasv4 시리즈는 AMD Radeon 이러한 MI25 Gpu에 의해 구동 됩니다.

NV와 NV_Promo 계열과 최신 NVsv3 및 NVasv4 시리즈의 주요 차이점은 다음과 같습니다.
- 성능 개선.
- Premium 저장소에 대 한 지원.
- 분수 GPU 크기에서 다중 GPU 구성으로 선택할 수 있는 옵션입니다.

NVsv3 및 NVasv4 시리즈는 모두 최신 코어와 용량이 더 높습니다.

다음 섹션에서는 레거시 NV 계열과 NVsv3 및 NVv4 시리즈 간의 차이점을 요약 합니다.
 
 ## <a name="nvsv3-series"></a>NVsv3 시리즈 

NVv3 시리즈 Vm은 Intel E5-2690 v4 (Broadwell) Cpu 및 Intel Hyper-Threading 기술을 사용 하 여 NVIDIA Tesla M60 Gpu 및 NVIDIA GRID 기술로 구동 됩니다. 이러한 Vm은 고객이 원하는 GPU 가속 그래픽 응용 프로그램 및 가상 데스크톱을 대상으로 합니다.
- 데이터를 시각화 합니다.
- 표시할 결과를 시뮬레이션 합니다.
- CAD에서 작업 합니다.
- 콘텐츠를 렌더링 및 스트림 합니다. 

이러한 Vm은 인코딩 및 렌더링과 같은 단일 전체 작업을 실행할 수도 있습니다.

NVv3 vm은 Premium 저장소를 지원 하 고 NV 계열과 비교할 때 시스템 메모리 (RAM)의 두 배를 제공 합니다. 최신 사양은 [GPU 가속 계산 VM 크기: NVsv3 시리즈](nvv3-series.md)를 참조 하세요.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU: 12(+6) <br> 메모리: GiB 112(+56) <br> 임시 저장소 (SSD) GiB: 320 (-20) <br> 최대 데이터 디스크: 12(-12) <br> 가속 네트워킹: 예 <br> Premium 저장소: 예  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24(+12) <br>메모리: GiB 224(+112) <br>임시 저장소 (SSD) GiB: 640 (-40)<br>최대 데이터 디스크: 24(-24)<br>가속 네트워킹: 예 <br>Premium 저장소: 예   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48(+24) <br>메모리: GiB 448(+224) <br>임시 저장소 (SSD) GiB: 1280 (-160) <br>최대 데이터 디스크: 32(-32) <br>가속 네트워킹: 예 <br>Premium 저장소: 예   |

## <a name="nvv4-series"></a>NVv4 시리즈 

NVv4 시리즈 Vm은 AMD Radeon 이러한 MI25 Gpu 및 AMD EPYC 7V12 (로마) Cpu로 구동 됩니다. NVv4 시리즈를 사용 하면 Azure에서 부분 Gpu를 사용 하는 Vm을 도입 합니다. GPU 가속 그래픽 응용 프로그램 및 가상 데스크톱에 대 한 적절 한 크기의 VM을 선택 합니다 .이는 GiB 프레임 버퍼로 2-GiB 프레임 버퍼를 사용 하 여 전체 GPU로 시작 합니다.

NVv4 vm은 현재 Windows 게스트 운영 체제만 지원 합니다. 최신 사양은 [GPU 가속 계산 VM 크기: NVv4 시리즈](nvv4-series.md)를 참조 하세요.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU: 16(+10) <br>메모리: GiB 56  <br>임시 저장소 (SSD) GiB: 352 (+ 12) <br>최대 데이터 디스크: 16(-8) <br>가속 네트워킹: 예 <br>Premium 저장소: 예   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32(+20) <br>메모리: GiB 112 <br>임시 저장소 (SSD) GiB: 704 (+ 24) <br>최대 데이터 디스크: 32(+16)<br>가속 네트워킹: 예 <br>Premium 저장소: 예   |
|Standard_NV24 <br> Standard_NV24_Promo |해당 없음  | 해당 없음  |

## <a name="migration-steps-for-general-changes"></a>일반적인 변경 내용에 대 한 마이그레이션 단계

일반적인 변경 내용 처리:

1. 마이그레이션할 시리즈와 크기를 선택합니다. 

1. 대상 VM 시리즈에 대 한 할당량을 가져옵니다.

1. 현재 NV 시리즈 VM 크기를 대상 크기로 조정 합니다.

  대상 크기가 NVv4 인 경우 Nvidia GPU 드라이버를 제거 하 고 AMD GPU 드라이버를 설치 해야 합니다.

## <a name="migration-steps-for-breaking-changes"></a>주요 변경 내용에 대 한 마이그레이션 단계

주요 변경 사항을 처리 하려면 다음 섹션의 단계를 따르세요.

### <a name="select-a-target-size-for-migration"></a>마이그레이션할 대상 크기 선택

현재 사용량을 평가한 후에는 필요한 GPU VM 유형을 결정 합니다. 작업 요구 사항에 따라 몇 가지 옵션을 선택할 수 있습니다. 선택 하는 방법은 다음과 같습니다.

- 워크 로드가 그래픽 또는 시각화이 고 Nvidia GPU를 사용 하는 데 하드 종속성이 있는 경우 NVsv3 시리즈로 마이그레이션합니다.
- 워크 로드가 그래픽 또는 시각화이 고 특정 유형의 GPU에 하드 종속성이 없는 경우 NVsv3 또는 NVVasv4 시리즈로 마이그레이션합니다.
 
> [!Note]
>모범 사례는 비용과 성능에 따라 VM 크기를 선택하는 것입니다. 이 문서의 권장 사항은 NV와 NV_Promo 크기 및 다른 VM 시리즈에서 가장 일치 하는 항목에 대 한 성능 메트릭의 일대일 비교를 기반으로 합니다.
>올바른 크기를 결정 하기 전에 Azure 가격 책정 계산기를 사용 하 여 비용을 비교 합니다.

### <a name="get-a-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대 한 할당량을 가져옵니다. 

가이드에 따라 [VM 제품군에 의한 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다. 마이그레이션을 위해 선택한 대상 VM 크기에 따라 VM 제품군 이름으로 NVSv3 series 또는 NVv4 시리즈를 선택 합니다.

### <a name="resize-the-current-vm"></a>현재 VM 크기 조정

[Azure Portal 또는 PowerShell을 통해 VM의 크기를 조정할](./windows/resize-vm.md)수 있습니다. [Azure CLI를 사용 하 여 VM의 크기를 조정할](./linux/change-vm-size.md)수도 있습니다. 

## <a name="faq"></a>FAQ
**Q:** 대상 VM 크기에 어떤 GPU 드라이버를 사용해야 하나요? 

**A:** NVsv3 시리즈의 경우 [NVIDIA GRID 드라이버](./windows/n-series-driver-setup.md)를 사용 합니다. NVv4의 경우 [AMD GPU 드라이버](./windows/n-series-amd-driver-setup.md)를 사용합니다. 

**Q:** 지금 Nvidia GPU 드라이버 확장을 사용 합니다. 대상 VM 크기에서 작동할까요? 

**A:** 현재 [Nvidia 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 NVsv3에서 작동합니다. 대상 VM 크기가 is NVv4인 경우 [AMD GPU 드라이버 확장](./extensions/hpccompute-amd-gpu-windows.md)을 사용합니다. 
 
**Q:** CUDA에 대한 종속성이 있는 경우 어떤 대상 VM 시리즈를 사용해야 하나요? 

 **A:** NVv3은 CUDA를 지원합니다. AMD Gpu를 사용 하는 NVv4 VM 시리즈는 CDA를 지원 하지 않습니다.
