---
title: 컨테이너 인사이트를 사용하여 GPU 모니터링 구성 | Microsoft Docs
description: 이 문서에서는 컨테이너 인사이트를 사용하여 NVIDIA 및 AMD GPU 지원 노드로 Kubernetes 클러스터 모니터링을 구성하는 방법을 설명합니다.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 9f18628b2814305d8629d3216bb0a0d1bab092fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528624"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>컨테이너 인사이트를 사용하여 GPU 모니터링 구성

에이전트 버전 *ciprod03022019* 부터 컨테이너 인사이트 통합 에이전트는 이제 GPU 인식 Kubernetes 클러스터 노드의 GPU(그래픽 처리 장치) 사용량에 대한 모니터링을 지원하며, GPU 리소스를 요청하고 사용하는 Pod/컨테이너를 모니터링합니다.

## <a name="supported-gpu-vendors"></a>지원되는 GPU 공급업체

컨테이너 인사이트는 다음 GPU 공급업체의 GPU 클러스터에 대한 모니터링을 지원합니다.

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [AMD](https://github.com/RadeonOpenCompute/k8s-device-plugin)

컨테이너 인사이트는 60초 간격으로 다음 메트릭을 수집하고 **InsightMetrics** 테이블에 저장하여 노드와 GPU 요청 Pod 및 워크로드의 GPU 지원량에 대한 모니터링을 자동으로 시작합니다.

>[!NOTE]
>GPU 노드를 사용하여 클러스터를 프로비저닝한 후 AKS 요구 사항에 따라 GPU 워크로드를 실행하는 데 필요한 [GPU 드라이버](../../aks/gpu-cluster.md)가 설치되어 있는지 확인합니다. 컨테이너 인사이트는 노드에서 실행되는 GPU 드라이버 Pod를 통해 GPU 메트릭을 수집합니다. 

|메트릭 이름 |메트릭 차원(태그) |Description |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|과거 샘플 기간(60초) 대비 GPU가 컨테이너를 위해 사용 중/적극적으로 처리 중인 시간의 백분율입니다. 업무 주기는 1에서 100 사이의 숫자입니다. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |각 컨테이너가 하나 이상의 GPU로 한도를 지정할 수 있습니다. GPU의 일부를 요청하거나 제한할 수는 없습니다. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |각 컨테이너가 하나 이상의 GPU를 요청할 수 있습니다. GPU의 일부를 요청하거나 제한할 수는 없습니다.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |특정 컨테이너에 사용할 수 있는 GPU 메모리 크기(바이트)입니다. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |특정 컨테이너에 사용 중인 GPU 메모리 크기(바이트)입니다. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Kubernetes에서 사용할 수 있는 한 노드의 GPU 수입니다. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |한 노드에 있는 총 GPU 수입니다. |

## <a name="gpu-performance-charts"></a>GPU 성능 차트 

컨테이너 인사이트에는 앞서 표에 나열된 메트릭에 대해 미리 구성된 차트가 각 클러스터의 GPU 통합 문서로 포함되어 있습니다. 컨테이너 인사이트에 사용할 수 있는 통합 문서에 대한 설명은 [컨테이너 인사이트의 통합 문서](container-insights-reports.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- GPU 지원 노드가 포함된 AKS 클러스터를 배포하는 방법에 대한 자세한 내용은 [AKS(Azure Kubernetes Service)에서 컴퓨팅 집약적인 워크로드에 GPU 사용](../../aks/gpu-cluster.md)을 참조하세요.

- [Microsoft Azure의 GPU 최적화된 VM SKU](../../virtual-machines/sizes-gpu.md)에 관해 자세히 알아보세요.

- 클러스터에 있는 하나 이상의 노드에서 GPU를 관리하기 위한 Kubernetes 실험적 지원에 대한 자세한 내용은 [Kubernetes의 GPU 지원](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/)을 참조하세요.
