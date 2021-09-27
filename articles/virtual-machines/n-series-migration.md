---
title: Azure의 GPU 컴퓨팅 워크로드 마이그레이션 가이드
description: NC, ND, NCv2 시리즈 마이그레이션 가이드입니다.
author: iafinder
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 08/15/2020
ms.author: iafinder
ms.openlocfilehash: 574118d9bd8c400eccb48ed551d6059c0dfc7bf2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551684"
---
# <a name="migration-guide-for-gpu-compute-workloads-in-azure"></a>Azure의 GPU 컴퓨팅 워크로드 마이그레이션 가이드

마켓플레이스 및 Microsoft Azure 데이터 센터에서 더 강력한 GPU를 사용할 수 있게 됨에 따라 워크로드의 성능을 다시 평가하고 최신 GPU로 마이그레이션하는 것이 좋습니다.

같은 이유로, Azure는 신뢰할 수 있는 고품질 서비스 제품을 유지하는 것 외에도 이전 VM 크기를 지원하는 하드웨어를 주기적으로 사용 중지합니다. Azure에서 사용 중지되는 첫 번째 GPU 제품 그룹은 각각 NVIDIA Tesla K80, P100 및 P40 데이터 센터 GPU 가속기에서 구동되는 원래의 NC, NC v2 및 ND 시리즈 VM입니다. 이러한 제품은 2022년 8월 31일에 사용 중지되며, 이 시리즈에서 가장 오래된 VM은 2016년에 출시되었습니다.

그 이후 GPU는 전체 딥 러닝 및 HPC 업계와 함께 놀라운 발전을 이루었으며, 일반적으로 세대 간 성능이 두 배 이상 향상되었습니다. NVIDIA K80, P40 및 P100 GPU가 출시된 이후 Azure는 NVIDIA의 T4, V100 및 A100 GPU를 기반으로 하여 GPU 가속 컴퓨팅 및 AI에 맞춘 여러 최신 세대 및 범주의 VM 제품을 출시했으며 InfiniBand 기반 상호 연결 패브릭과 같은 선택적 기능으로 차별화했습니다. 이러한 옵션은 모두 고객이 마이그레이션 경로로 탐색하도록 권장하는 옵션입니다.

대부분의 경우 최신 세대의 GPU에서 제공하는 성능이 크게 향상되면 GPU 시간당 비용이 다를 수 있지만, 버스트 가능한 작업의 경우 작업 기간을 줄이거나 컴퓨팅 리소스에 대한 고정 크기 수요를 처리하는 데 필요한 전체 GPU 사용 VM의 수량을 줄여 전체 TCO를 낮춥니다. 이러한 이점 외에도, 고객은 고성능 VM을 통해 솔루션 시간을 향상시키고, 최신 소프트웨어, CUDA 런타임 및 드라이버 버전을 채택하여 솔루션의 상태와 지원 가능성을 향상시킬 수 있습니다.

## <a name="migration-vs-optimization"></a>마이그레이션 및 최적화

Azure는 고객이 GPU 아키텍처 고려 사항, 상호 연결, TCO, 솔루션 시간 및 규정 준수 지역 또는 대기 시간 요구 사항을 기반으로 하는 지역 가용성을 포함하여 특정 GPU VM 제품을 선택하도록 지시할 수 있는 다양한 요구 사항이 있음을 인식하고 있습니다. 이러한 요구 사항 중 일부는 시간이 지남에 따라 변경됩니다.

동시에 GPU 가속은 새롭고 빠르게 진화하는 영역입니다.

따라서이 제품 영역에 대 한 모든 지침은 진정한 1 크기에 적합 하지 않습니다. 마이그레이션은 클러스터 된 배포 모델에서 단일 용량의 8 GPU VM으로 이동 하는 것과 같은 작업을 수행 하는 것과 같은 작업을 수행 하 고, 축소 된 전체 자릿수 데이터 형식을 활용 하 고, 다중 인스턴스 GPU와 같은 기능을 채택 하는 등의 작업을 수행 하는 것과 같은 작업

TensorCore 추가와 같은 기능이 중요도의 순서에 따라 성능을 높일 수 있는 이미 극적인 세대별 GPU 성능 향상의 컨텍스트에서 볼 때 이러한 종류의 고려 사항은 워크로드에 따라 크게 다릅니다.

마이그레이션을 애플리케이션 재 아키텍처와 결합하면 엄청난 가치를 창출하고 비용과 솔루션 시간을 개선할 수 있습니다.

그러나 이러한 종류의 개선 사항은 이 문서의 범위를 벗어납니다. 이 문서의 목표는 현재 고객이 실행할 수 있는 일반화된 워크로드에 대한 직접 동등성 클래스에 집중하여 GPU당 가격 및 성능 *모두* 에서 사용 중지할 기존 VM 제품군과 가장 비슷한 VM 옵션을 식별하는 것입니다.

따라서 이 문서에서는 필요한 VM 인스턴스 수, GPU, 상호 연결 등과 같은 워크로드 관련 속성에 대한 인사이트 또는 제어 권한이 사용자에게 없을 수 있다고 가정합니다.

## <a name="recommended-upgrade-paths"></a>권장 업그레이드 경로

### <a name="nc-series-vms-featuring-nvidia-k80-gpus"></a>NVIDIA K80 GPU를 갖춘 NC 시리즈 VM

[NC(v1) 시리즈](./nc-series.md) VM은 Intel Xeon E5-2690 v3(Haswell) 프로세서와 페어링되는 1~4개의 NVIDIA Tesla K80 데이터 센터 GPU 가속기에서 구동되는 Azure에서 가장 오래된 GPU 가속 컴퓨팅 VM 유형입니다. 까다로운 AI, ML 및 HPC 애플리케이션에 대한 주력 VM 유형이 된 후에 GPU 시간당 절대 비용이 달러당 처리량이 더 높은 GPU보다 매우 낮은 것을 중요하게 생각하는 사용자를 위해 이러한 제품은 제품 수명 주기 후반(특히 NC 시리즈 프로모션 격 책정을 통해)에서 인기 있는 선택 항목으로 남아 있었습니다.

현재 노후화된 NVIDIA K80 GPU 플랫폼의 컴퓨팅 성능이 최신 GPU를 갖춘 VM 시리즈에 비해 상대적으로 낮다는 점을 고려할 때 NC 시리즈의 인기 있는 사용 사례는 실시간 유추 및 분석 워크로드입니다. 여기서는 애플리케이션의 요청을 도착하는 즉시 처리할 수 있도록 가속화된 VM을 안정적인 상태에서 사용할 수 있어야 합니다. 이러한 경우 요청의 볼륨 또는 일괄 처리 크기가 성능이 더 뛰어난 GPU의 이점을 활용하는 데 충분하지 않을 수 있습니다. 또한 NC VM은 GPU 가속을 학습, 개발 또는 실험하는 개발자와 학생에게 인기가 있습니다. 이러한 사용자에게는 프로덕션 수준까지 반복할 필요가 없는 저렴한 클라우드 기반 CUDA 배포 대상이 필요합니다.

일반적으로 NC 시리즈 고객은 NC 크기에서 [NC T4 v3](./nct4-v3-series.md) 크기(NVIDIA Tesla T4 GPU에서 구동되는 경량 워크로드를 위한 Azure의 새 GPU 가속 플랫폼)로 직접 이동하는 것을 고려해야 하지만, InfiniBand 사용 NC 시리즈 크기에서 실행되는 워크로드에는 다른 VM SKU를 고려해야 합니다.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이 | 
|---|---|---|
Standard_NC6 <br> Standard_NC6_Promo | Standard_NC4as_T4_v3 <br>또는<br>Standard_NC8as_T4 | CPU: Intel Haswell 및 AMD Rome<br>GPU 수: 1(동일)< br>GPU 세대: NVIDIA Keppler 및 Turing(2세대 이상, 2x FP32 FLOPs 이하)<br>GPU 메모리(GPU당 GiB): 16(4 이상)<br>vCPU: 4(2 이하) 또는 8(2 이상)<br>메모리 GiB: 16(40 이하) 또는 56(동일)<br>임시 스토리지(SSD) GiB: 180(160 이하) 또는 360(20 이상)<br>최대 데이터 디스크 수: 8(4 이하) 또는 16(4 이상)<br>가속화된 네트워킹: 예(+)<br>Premium Storage: 예(+)| 
| Standard_NC24<br>Standard_NC24_Promo | Standard_NC64as_T4_v3* | CPU: Intel Haswell 및 AMD Rome<br>GPU 수: 4(동일)<br>GPU 세대: NVIDIA Keppler 및. Turing(2세대 이상, 2x FP32 FLOPs 이하)<br>GPU 메모리(GPU당 GiB): 16(4 이상)<br>vCPU: 64(40 이상)<br>메모리 GiB: 440(216 이상)<br>임시 스토리지(SSD) GiB: 2880(1440 이상)<br>최대 데이터 디스크 수: 32(32 이하)<br>가속화된 네트워킹: 예(+)<br>Premium Storage: 예(+) | 
|Standard_NC24r<br>Standard_NC24r_Promo<br><br>(InfiniBand 클러스터링 사용 크기) | Standard_NC24rs_v3* | CPU: Intel Haswell 및 Intel Broadwell<br>GPU 수: 4(동일)<br>GPU 세대: NVIDIA Keppler 및 Volta(2세대 이상)<br>GPU 메모리(GPU당 GiB): 16(4 이상)<br>vCPU: 24(0 이상)<br>메모리 GiB: 448(224 이상)<br>임시 스토리지(SSD) GiB: 2948(1440 이상)<br>최대 데이터 디스크 수: 32(동일)<br>가속화된 네트워킹: 아니요(동일)<br>Premium Storage: 예(+)<br>InfiniBand 상호 연결: 예 | 


### <a name="nd-series-vms-featuring-nvidia-tesla-p40-gpus"></a>NVIDIA Tesla P40 GPU를 갖춘 ND 시리즈 VM

ND 시리즈 가상 머신은 원래 AI 및 Deep Learning 워크로드용으로 설계된 미드레인지 플랫폼입니다. 이전 버전보다 향상된 단정밀도 부동 소수점 연산을 통해 뛰어난 성능을 일괄 처리 유추에 제공했으며, NVIDIA Tesla P40 GPU 및 Intel Xeon E5-2690 v4(Broadwell) CPU에서 구동됩니다. NC 및 NC v2 시리즈와 마찬가지로 ND 시리즈는 RDMA 및 InfiniBand 연결을 통한 대기 시간이 짧고 처리량이 높은 보조 네트워크 구성을 제공하므로 많은 GPU에서 대규모 학습 작업을 실행할 수 있습니다.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이 | 
|---|---|---|
|Standard_ND6 | Standard_NC4as_T4_v3<br>또는<br>Standard_NC8as_T4 | CPU: Intel Broadwell 및 AMD Rome<br>GPU 수: 1(동일)<br>GPU 세대: NVIDIA Pascal 및 Turing(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(8 이하)<br>vCPU: 4(2 이하) 또는 8(2 이상)<br>메모리 GiB: 16(40 이하) 또는 56(56 이하)<br>임시 스토리지(SSD) GiB: 180(552 이하) 또는 360(372 이하)<br>최대 데이터 디스크 수: 8(4 이하) 또는 16(4 이상)<br>가속화된 네트워킹: 예(+)<br>Premium Storage: 예(+) | 
| Standard_ND12 | Standard_NC16as_T4_v3 | CPU: Intel Broadwell 및 AMD Rome<br>GPU 수: 1(1 이하)<br>GPU 세대: NVIDIA Pascal 및 Turing(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(8 이하)<br>vCPU: 16(4 이상)<br>메모리 GiB: 110(114 이하)<br>임시 스토리지(SSD) GiB: 360(1114 이하)<br>최대 데이터 디스크 수: 48(16 이상)<br>가속화된 네트워킹: 예(+)<br>Premium Storage: 예(+) | 
| Standard_ND24 | Standard_NC64as_T4_v3* | CPU: Intel Broadwell 및 AMD Rome<br>GPU 수: 4(동일)<br>GPU 세대: NVIDIA Pascal 및 Turing(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(8 이하)<br>vCPU: 64(40 이상)<br>메모리 GiB: 440(동일)<br>임시 스토리지(SSD) GiB: 2880(동일)<br>최대 데이터 디스크 수: 32(동일)<br>가속화된 네트워킹: 예(+)<br>Premium Storage: 예(+) | 
| Standard_ND24r | Standard_NC24rs_v3* | CPU: Intel Broadwell(동일)<br>GPU 수: 4(동일)<br>GPU 세대: NVIDIA Pascal 및 Volta(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(8 이하)<br>vCPU: 24(0 이상)<br>메모리 GiB: 448(동일)<br>임시 스토리지(SSD) GiB: 2948(동일)<br>최대 데이터 디스크 수: 32(동일)<br>가속화된 네트워킹: 아니요(동일)<br>Premium Storage: 예(+)<br>InfiniBand 상호 연결: 예(동일) | 

### <a name="nc-v2-series-vms-featuring-nvidia-tesla-p100-gpus"></a>NVIDIA Tesla P100 GPU를 갖춘 NC v2 시리즈 VM

NC v2 시리즈 가상 머신은 원래 AI 및 Deep Learning 워크로드용으로 설계된 주력 플랫폼입니다. GPU당 성능이 원래 NC 시리즈의 약 2배이고, NVIDIA Tesla P100 GPU 및 Intel Xeon E5-2690 v4(Broadwell) CPU에서 구동되는 뛰어난 성능을 딥Deep Learning 학습에 제공했습니다. NC 및 ND 시리즈와 마찬가지로 NC v2 시리즈는 RDMA 및 InfiniBand 연결을 통한 대기 시간이 짧고 처리량이 높은 보조 네트워크 구성을 제공하므로 많은 GPU에서 대규모 학습 작업을 실행할 수 있습니다.

| 현재 VM 크기 | 대상 VM 크기 | 사양의 차이 | 
|---|---|---|
| Standard_NC6s_v2 | Standard_NC6s_v3 | CPU: Intel Broadwell(동일)<br>GPU 수: 1(동일)<br>GPU 세대: NVIDIA Pascal 및 Volta(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(동일)<br>vCPU: 6(동일)<br>메모리 GiB: 112(동일)<br>임시 스토리지(SSD) GiB: 736(동일)<br>최대 데이터 디스크 수: 12(동일)<br>가속화된 네트워킹: 아니요(동일)<br>Premium Storage: 예(+) | 
| Standard_NC12s_v2 | Standard_NC12s_v3 | CPU: Intel Broadwell(동일)<br>GPU 수: 2(동일)<br>GPU 세대: NVIDIA Pascal 및 Volta(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(동일)<br>vCPU: 12(동일)<br>메모리 GiB: 112(동일)<br>임시 스토리지(SSD) GiB: 1474(동일)<br>최대 데이터 디스크 수: 24(동일)<br>가속화된 네트워킹: 아니요(동일)<br>Premium Storage: 예(+) | 
| Standard_NC24s_v2 | Standard_NC24s_v3 | CPU: Intel Broadwell(동일)<br>GPU 수: 4(동일)<br>GPU 세대: NVIDIA Pascal 및 Volta(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(동일)<br>vCPU: 24(동일)<br>메모리 GiB: 448(동일)<br>임시 스토리지(SSD) GiB: 2948(동일)<br>최대 데이터 디스크 수: 32(동일)<br>가속화된 네트워킹: 아니요(동일)<br>Premium Storage: 예(+) | 
| Standard_NC24rs_v2 | Standard_NC24rs_v3* | CPU: Intel Broadwell(동일)<br>GPU 수: 4(동일)<br>GPU 세대: NVIDIA Pascal 및 Volta(1세대 이상)<br>GPU 메모리(GPU당 GiB): 16(동일)<br>vCPU: 24(동일)<br>메모리 GiB: 448(동일)<br>임시 스토리지(SSD) GiB: 2948(동일)<br>최대 데이터 디스크 수: 32(동일)<br>가속화된 네트워킹: 아니요(동일)<br>Premium Storage: 예(+)<br>InfiniBand 상호 연결: 예(동일)| 


## <a name="migration-steps"></a>마이그레이션 단계

### <a name="general-changes"></a>일반 변경 내용

1.  마이그레이션을 위한 시리즈 및 크기 선택. 추가 인사이트를 얻으려면 [가격 계산기](https://azure.microsoft.com/pricing/calculator/)를 활용하세요.

2.  대상 VM 시리즈에 대한 할당량 얻기

3.  현재 N\* 시리즈 VM 크기를 대상 크기로 조정합니다. 이 경우 Virtual Machine 이미지에서 사용하는 운영 체제를 업데이트하거나 드라이버가 미리 설치된 HPC 이미지 중 하나를 시작점으로 도입하는 것이 좋을 수도 있습니다.

    > [!IMPORTANT]
    > VM 이미지는 새 GPU VM 시리즈에 필요한 것보다 이전 버전의 CUDA 런타임, NVIDIA 드라이버 및 Mellanox OFED 드라이버(해당되는 경우 RDMA 사용 크기에만)를 사용하여 생성되었을 수 있으며, [Azure 설명서의 지침](./sizes-gpu.md)에 따라 업데이트할 수 있습니다.

### <a name="breaking-changes"></a>주요 변경 내용

#### <a name="select-target-size-for-migration"></a>마이그레이션할 대상 크기 선택

현재 사용량을 평가한 후 필요한 GPU VM 유형을 결정합니다. 워크로드 요구 사항에 따라 선택할 수 있는 몇 가지 항목이 있습니다.

> [!NOTE]
> 비용과 성능 모두를 기준으로 VM 크기를 선택하는 것이 가장 좋습니다. 이 가이드의 권장 사항은 성능 메트릭과 다른 VM 시리즈에서 가장 유사한 일치 항목의 범용 일대일 비교를 기반으로 합니다. 올바른 크기를 결정하기 전에 Azure 가격 계산기를 사용하여 비용을 비교하세요.

> [!IMPORTANT]
> 모든 레거시 NC, NC v2 및 ND 시리즈 크기는 여러 GPU 크기로 사용할 수 있습니다. 여기에는 단일 4-GPU VM 또는 개별적으로 제공할 수 있는 단일 K80, P40 또는 P100 GPU보다 더 많은 컴퓨팅 성능을 요구하는 긴밀하게 결합된 스케일 아웃 워크로드에 대한 InfiniBand 상호 연결이 있거나 없는 4-GPU 크기가 포함됩니다. 위의 권장 사항은 간단한 경로를 제공하지만, 이러한 크기의 사용자는 [NC v3 시리즈](./ncv3-series.md) 및 [ND v2 시리즈](./ndv2-series.md)와 같은 더 강력한 NVIDIA V100 GPU 기반 VM 시리즈를 사용하여 성능 목표를 달성하는 것을 고려해야 합니다. 이러한 VM 시리즈는 일반적으로 다중 GPU 및 다중 노드 구성을 각각 요구하기 전에 훨씬 더 높은 GPU당 및 VM당 성능을 제공함으로써 더 낮은 비용과 향상된 관리 효율성을 통해 동일한 수준의 워크로드 성능을 가능하게 합니다.
<br>

#### <a name="get-quota-for-the-target-vm-family"></a>대상 VM 제품군에 대한 할당량 얻기

가이드에 따라 [VM 제품군별로 vCPU 할당량 증가를 요청](../azure-portal/supportability/per-vm-quota-requests.md)합니다. 마이그레이션하도록 선택한 대상 VM 크기를 선택합니다.

#### <a name="resize-the-current-virtual-machine"></a>현재 가상 머신 크기 조정

[가상 컴퓨터의 크기를 조정할](resize-vm.md)수 있습니다. 

## <a name="next-steps"></a>다음 단계

GPU 사용 가상 머신 크기의 전체 목록은 [GPU - 가속 컴퓨팅 개요](sizes-gpu.md)를 참조하세요.