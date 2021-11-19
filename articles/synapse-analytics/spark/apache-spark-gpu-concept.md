---
title: GPU 가속 풀
description: Synapse Analytics 내의 GPU를 소개합니다.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: spark
ms.date: 11/10/2021
ms.author: midesa
ms.openlocfilehash: d4a4c38b61dcce01ec7fb73dc70605ce795b6412
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495051"
---
# <a name="gpu-accelerated-apache-spark-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics의 GPU 가속 Apache Spark 풀
Azure Synapse Analytics는 이제 GPU(그래픽 처리 장치)로 가속화된 Apache Spark 풀을 지원합니다. 

NVIDIA GPU를 사용하면 데이터 과학자와 엔지니어가 데이터 통합 ​​파이프라인을 실행하고 기계 학습 모델에 점수를 매기는 등의 작업에 필요한 시간을 줄일 수 있습니다. 이 문서에서는 GPU 가속 풀을 만들고 Azure Synapse Analytics에서 사용하는 방법에 대해 설명합니다. 또한 이 문서에서는 GPU 가속 런타임의 일부로 미리 설치된 GPU 드라이버 및 라이브러리에 대해서도 자세히 설명합니다.

> [!NOTE]
> Azure Synapse GPU 사용 풀은 현재 공개 미리 보기로 제공됩니다.

## <a name="create-a-gpu-accelerated-pool"></a>GPU 가속 풀 만들기
풀을 만들고 관리하는 프로세스를 간소화하기 위해 Azure Synapse는 하위 수준 라이브러리를 미리 설치하고 컴퓨팅 노드 간에 복잡한 네트워킹 요구 사항을 모두 설정합니다. 이 통합을 통해 사용자는 GPU 가속 풀을 몇 분 이내에 시작할 수 있습니다. GPU 가속 풀을 만드는 방법에 대해 자세히 알아보려면 [GPU 가속 풀을 만드는 방법](../quickstart-create-apache-gpu-pool-portal.md)에 대한 빠른 시작을 참조하세요.

> [!NOTE]
>  - GPU 가속 풀은 미국 동부, 오스트레일리아 동부 및 북유럽에 있는 작업 영역에서 만들 수 있습니다.
>  - GPU 가속 풀은 Apache Spark 3 런타임에서만 사용할 수 있습니다.
>  - GPU 사용 클러스터를 만들려면 [제한 증가](./apache-spark-rapids-gpu.md#quotas-and-resource-constraints-in-azure-synapse-gpu-enabled-pools)를 요청해야 할 수 있습니다. 
 
## <a name="gpu-accelerated-runtime"></a>GPU 가속 런타임

### <a name="nvidia-gpu-driver-cuda-and-cudnn"></a>NVIDIA GPU 드라이버, CUDA 및 cuDNN
Azure Synapse Analytics는 이제 다양한 NVIDIA 라이브러리 및 구성이 포함된 GPU 가속 Apache Spark 풀을 제공합니다. Azure Synapse Analytics는 기본적으로 Spark 드라이버 및 작업자 인스턴스에서 GPU를 사용하는 데 필요한 NVIDIA 드라이버 및 라이브러리를 설치합니다.
 - CUDA 11.2
 - libnccl2=2.8.4
 - libnccl-dev=2.8.4
 - libcudnn8=8.1.1 
 - libcudnn8-dev=8.1.1

> [!NOTE]
> 이 소프트웨어에는 NVIDIA Corporation에서 제공한 소스 코드가 포함되어 있습니다. 특히 GPU 가속 풀을 지원하기 위해 Azure Synapse Apache Spark 풀에는 [CUDA 샘플](https://docs.nvidia.com/cuda/eula/#nvidia-cuda-samples-preface)의 코드가 포함됩니다.

### <a name="nvidia-end-user-license-agreement-eula"></a>NVIDIA EULA(최종 사용자 사용권 계약)
Synapse Spark에서 GPU 가속 하드웨어 옵션을 선택하면 다음과 관련하여 NVIDIA EULA에 설명된 사용 약관에 암시적으로 동의하게 됩니다.
  - CUDA 11.2: [EULA :: CUDA Toolkit 설명서(nvidia.com)](https://docs.nvidia.com/cuda/eula/index.html)
  - libnccl2=2.8.4: [NVIDIA/nccl(github.com) 마스터에 있는 nccl/LICENSE.txt](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libnccl-dev=2.8.4: [NVIDIA/nccl(github.com) 마스터에 있는 nccl/LICENSE.txt](https://github.com/NVIDIA/nccl/blob/master/LICENSE.txt)
  - libcudnn8=8.1.1: [소프트웨어 사용권 계약 :: NVIDIA Deep Learning cuDNN 설명서](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - libcudnn8-dev=8.1.1: [소프트웨어 사용권 계약 :: NVIDIA Deep Learning cuDNN 설명서](https://docs.nvidia.com/deeplearning/cudnn/sla/index.html)
  - CUDA, NCCL 및 cuDNN 라이브러리와 NCCL 라이브러리에 대한 [NVIDIA 최종 사용자 사용권 계약(NCCL 추가 조항 포함)](https://docs.nvidia.com/deeplearning/nccl/sla/index.html#overview)

## <a name="accelerate-etl-workloads"></a>ETL 워크로드 가속화
NVIDIA의 [RAPIDS Accelerator for Apache Spark](https://nvidia.github.io/spark-rapids/)에 대한 기본 제공 지원을 사용하면 Azure Synapse의 GPU 가속 Spark 풀에서 코드를 변경하지 않고도 표준 분석 벤치마크에 비해 상당히 향상된 성능을 제공할 수 있습니다. NVIDIA CUDA 및 UCX를 기반으로 구축된 NVIDIA RAPIDS는 GPU 가속 SQL, DataFrame 작업 및 Spark 순서 섞기를 지원합니다. 이러한 가속화를 활용하는 데 필요한 코드 변경이 없으므로 사용자는 Linux Foundation의 Delta Lake 또는 Microsoft의 하이퍼스페이스 인덱싱을 사용하는 데이터 파이프라인을 가속화할 수도 있습니다. 

Azure Synapse Analytics의 GPU 가속 풀에서 NVIDIA RAPIDS Accelerator를 사용하는 방법에 대해 자세히 알아보려면 [RAPIDS를 사용하여 성능을 향상시키는 방법](apache-spark-rapids-gpu.md)에 대한 해당 가이드를 참조하세요.

## <a name="improve-machine-learning-scoring-workloads"></a>기계 학습 채점 워크로드 향상
많은 조직에서는 짧은 기간 동안 대량 일괄 처리 채점 작업을 자주 실행합니다. 향상된 일괄 처리 채점 작업을 달성하기 위해 Microsoft의 [Hummingbird 라이브러리](https://github.com/Microsoft/hummingbird)와 함께 GPU 가속 Spark 풀을 사용할 수도 있습니다. Hummingbird를 사용하면 사용자가 기존의 트리 기반 ML 모델을 가져와서 텐서 계산으로 컴파일할 수 있습니다. Hummingbird를 통해 사용자는 네이티브 하드웨어 가속 및 신경망 프레임워크를 원활하게 활용하여 모델을 다시 작성할 필요 없이 ML 모델 채점을 가속화할 수 있습니다.  

## <a name="next-steps"></a>다음 단계
- [Azure Synapse Analytics](../overview-what-is.md)
