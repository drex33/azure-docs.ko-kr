---
title: NV 시리즈 마이그레이션 가이드
description: NV 시리즈 마이그레이션 가이드
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 3f37a9c14a66156b908eb1d046f98301add07c20
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128562386"
---
# <a name="nv-series-migration-guide"></a>NV 시리즈 마이그레이션 가이드

Azure 데이터 센터에서 더 강력한 GPU VM 크기를 사용할 수 있게 되면 NV 및 NV_Promo 시리즈에서 워크로드를 평가하고 VM(가상 머신)을 마이그레이션합니다. 비용 절감을 통해 성능을 향상하기 위해 이러한 레거시 VM을 NVsv3 및 NVasv4와 같은 새 VM 시리즈로 마이그레이션할 수 있습니다. NVsv3 VM 시리즈는 Nvidia M60 GPU로 구동됩니다. NVasv4 시리즈는 AMD Radeon 직관적 MI25 GPU를 통해 구동됩니다.

NV와 NV_Promo 시리즈와 최신 NVsv3 및 NVasv4 시리즈 간의 주요 차이점은 다음과 같습니다.
- 성능 개선.
- Premium 스토리지에 대한 지원.
- 소수 GPU 크기에서 다중 GPU 구성으로 선택할 수 있는 옵션입니다.

NVsv3 및 NVasv4 시리즈는 모두 최신 코어와 더 큰 용량을 갖습니다.

다음 섹션에서는 레거시 NV 시리즈와 NVsv3 및 NVv4 시리즈 간의 차이점을 요약합니다.
 
 ## <a name="nvsv3-series"></a>NVsv3 시리즈 

NVv3 시리즈 VM은 Intel E5-2690 v4(Broadwell) CPU 및 Intel Hyper-Threading Technology를 사용하는 NVIDIA Tesla M60 GPU 및 NVIDIA GRID 기술로 구동됩니다. 이러한 VM은 고객이 다음을 원하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱을 대상으로 합니다.
- 해당 데이터를 시각화합니다.
- 볼 결과를 시뮬레이션합니다.
- CAD에서 작업합니다.
- 콘텐츠를 렌더링하고 스트리밍합니다. 

이러한 VM은 인코딩 및 렌더링과 같은 단정밀도 워크로드를 실행할 수도 있습니다.

NVv3 VM은 Premium 스토리지를 지원하며 NV 시리즈와 비교할 때 RAM(시스템 메모리)의 두 배와 함께 제공됩니다. 최신 사양은 [GPU 가속 컴퓨팅 VM 크기: NVsv3 시리즈 를 참조하세요.](nvv3-series.md)

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이점  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV12s_v3  | vCPU: 12(+6) <br> 메모리: GiB 112(+56) <br> SSD(임시 스토리지) GiB: 320(-20) <br> 최대 데이터 디스크: 12(-12) <br> 가속화된 네트워킹: 예 <br> Premium 스토리지: 예  |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV24s_v3  | vCPU: 24(+12) <br>메모리: GiB 224(+112) <br>SSD(임시 스토리지) GiB: 640(-40)<br>최대 데이터 디스크: 24(-24)<br>가속화된 네트워킹: 예 <br>Premium 스토리지: 예   |
|Standard_NV24 <br> Standard_NV24_Promo |Standard_NV48s_v3  | vCPU: 48(+24) <br>메모리: GiB 448(+224) <br>SSD(임시 스토리지) GiB: 1280(-160) <br>최대 데이터 디스크: 32(-32) <br>가속화된 네트워킹: 예 <br>Premium 스토리지: 예   |

## <a name="nvv4-series"></a>NVv4 시리즈 

NVv4 시리즈 VM은 AMD Radeon 직관적 MI25 GPU 및 AMD EPYC 7V12(로마) CPU를 통해 구동됩니다. NVv4 시리즈를 통해 Azure는 부분 GPU가 있는 VM을 도입합니다. 16 GiB 프레임 버퍼가 있는 전체 GPU에 2-GiB 프레임 버퍼가 있는 GPU의 1/8에서 시작하는 GPU 가속 그래픽 애플리케이션 및 가상 데스크톱에 적합한 크기의 VM을 선택합니다.

NVv4 VM은 현재 Windows 게스트 운영 체제만 지원합니다. 최신 사양은 [GPU 가속 컴퓨팅 VM 크기: NVv4 시리즈 를 참조하세요.](nvv4-series.md)

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이점  |
|---|---|---|
|Standard_NV6 <br> Standard_NV6_Promo |Standard_NV16as_v4  | vCPU: 16(+10) <br>메모리: GiB 56  <br>SSD(임시 스토리지) GiB: 352(+12) <br>최대 데이터 디스크: 16(-8) <br>가속화된 네트워킹: 예 <br>Premium 스토리지: 예   |
|Standard_NV12 <br> Standard_NV12_Promo |Standard_NV32as_v4  | vCPU: 32(+20) <br>메모리: GiB 112 <br>SSD(임시 스토리지) GiB: 704(+24) <br>최대 데이터 디스크: 32(+16)<br>가속화된 네트워킹: 예 <br>Premium 스토리지: 예   |
|Standard_NV24 <br> Standard_NV24_Promo |해당 없음  | 해당 없음  |

## <a name="migration-steps-for-general-changes"></a>일반 변경에 대한 마이그레이션 단계

일반적인 변경 내용을 처리하려면 다음을 수행합니다.

1. 마이그레이션할 시리즈와 크기를 선택합니다. 

1. 대상 VM 시리즈에 대한 할당량을 얻습니다.

1. 현재 NV 시리즈 VM 크기를 대상 크기로 조정합니다.

  대상 크기가 NVv4인 경우 Nvidia GPU 드라이버를 제거하고 AMD GPU 드라이버를 설치해야 합니다.

## <a name="migration-steps-for-breaking-changes"></a>주요 변경 내용에 대한 마이그레이션 단계

주요 변경 내용을 처리하려면 다음 섹션의 단계를 수행합니다.

### <a name="select-a-target-size-for-migration"></a>마이그레이션할 대상 크기 선택

현재 사용량을 평가한 후 필요한 GPU VM 유형을 결정합니다. 워크로드 요구 사항에 따라 몇 가지 다른 선택 사항이 있습니다. 선택하는 방법은 다음과 같습니다.

- 워크로드가 그래픽 또는 시각화이고 Nvidia GPU 사용에 대한 종속성이 있는 경우 NVsv3 시리즈로 마이그레이션합니다.
- 워크로드가 그래픽 또는 시각화이고 특정 유형의 GPU에 대한 종속성이 없는 경우 NVsv3 또는 NVVasv4 시리즈로 마이그레이션합니다.
 
> [!Note]
>모범 사례는 비용과 성능에 따라 VM 크기를 선택하는 것입니다. 이 문서의 권장 사항은 NV 및 NV_Promo 크기에 대한 성능 메트릭의 일대일 비교와 다른 VM 시리즈에서 가장 가까운 일치를 기반으로 합니다.
>올바른 크기를 결정 하기 전에 Azure 가격 책정 계산기를 사용 하 여 비용을 비교 합니다.

### <a name="get-a-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대 한 할당량을 가져옵니다. 

가이드에 따라 [VM 제품군에 의한 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다. 마이그레이션을 위해 선택한 대상 VM 크기에 따라 VM 제품군 이름으로 NVSv3 series 또는 NVv4 시리즈를 선택 합니다.

### <a name="resize-the-current-vm"></a>현재 VM 크기 조정

[VM의 크기를 조정할](resize-vm.md)수 있습니다.

## <a name="faq"></a>FAQ
**Q:** 대상 VM 크기에 어떤 GPU 드라이버를 사용해야 하나요? 

**A:** NVsv3 시리즈의 경우 [NVIDIA GRID 드라이버](./windows/n-series-driver-setup.md)를 사용 합니다. NVv4의 경우 [AMD GPU 드라이버](./windows/n-series-amd-driver-setup.md)를 사용합니다. 

**Q:** 지금 Nvidia GPU 드라이버 확장을 사용 합니다. 대상 VM 크기에서 작동할까요? 

**A:** 현재 [Nvidia 드라이버 확장](./extensions/hpccompute-gpu-windows.md)은 NVsv3에서 작동합니다. 대상 VM 크기가 is NVv4인 경우 [AMD GPU 드라이버 확장](./extensions/hpccompute-amd-gpu-windows.md)을 사용합니다. 
 
**Q:** CUDA에 대한 종속성이 있는 경우 어떤 대상 VM 시리즈를 사용해야 하나요? 

 **A:** NVv3은 CUDA를 지원합니다. AMD Gpu를 사용 하는 NVv4 VM 시리즈는 CDA를 지원 하지 않습니다.
