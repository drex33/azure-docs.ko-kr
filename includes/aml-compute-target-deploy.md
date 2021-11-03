---
title: 포함 파일
description: 포함 파일
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 10/21/2021
ms.openlocfilehash: 75b0a6d6f1cf00cab533c1e3d147634dbdb3b55c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131076619"
---
모델을 호스팅하는 데 사용하는 컴퓨팅 대상은 배포된 엔드포인트의 비용 및 가용성에 영향을 줍니다. 이 표를 사용하여 적절한 컴퓨팅 대상을 선택합니다.

| 컴퓨팅 대상 | 사용 대상 | GPU 지원 | FPGA 지원 | Description |
| ----- | ----- | ----- | ----- | ----- |
| [로컬&nbsp;웹&nbsp;서비스](../articles/machine-learning/how-to-deploy-local-container-notebook-vm.md) | 테스트/디버깅 | &nbsp; | &nbsp; | 제한된 테스트 및 문제 해결에 사용합니다. 하드웨어 가속은 로컬 시스템에서 라이브러리를 사용하는지에 따라 달라집니다.
| [AKS(Azure Kubernetes Service)](../articles/machine-learning/how-to-deploy-azure-kubernetes-service.md) | 실시간 유추 <br/><br/> 프로덕션 워크로드에는 권장되지 않습니다. |  [예](../articles/machine-learning/how-to-deploy-with-triton.md)(웹 서비스 배포) | [예](../articles/machine-learning/how-to-deploy-fpga-web-service.md)   |대규모 프로덕션 배포에 사용합니다. 배포된 서비스의 빠른 응답 시간 및 자동 크기 조정을 제공합니다. 클러스터 자동 크기 조정은 Azure Machine Learning SDK를 통해 지원되지 않습니다. AKS 클러스터의 노드를 변경하려면 Azure Portal에서 AKS 클러스터의 UI를 사용합니다. <br/><br/> 디자이너에서 지원됩니다. |
| [Azure Container Instances](../articles/machine-learning/how-to-deploy-azure-container-instance.md) | 실시간 유추 <br/><br/> 개발/테스트 목적으로만 권장됩니다.| &nbsp;  | &nbsp; | 48GB 미만의 RAM이 필요한 소규모 CPU 기반 워크로드에 사용합니다. 클러스터를 관리하지 않아도 됩니다. <br/><br/> 디자이너에서 지원됩니다. |
| [Azure Machine Learning 컴퓨팅 클러스터](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md) | 일괄 처리&nbsp;유추 | [예](../articles/machine-learning/tutorial-pipeline-batch-scoring-classification.md)(기계 학습 파이프라인) | &nbsp;  | 서버리스 컴퓨팅에서 일괄 처리 채점을 실행합니다. 우선 순위가 보통이거나 낮은 VM을 지원합니다. 실시간 유추를 지원하지 않습니다.|
| [Azure Arc 지원 Kubernetes](/azure/machine-learning/how-to-attach-compute-targets) | 실시간 유추 <br/><br/>  일괄 처리 유추 | 예 | 해당 없음 | Azure Arc에서 관리 되는 온-프레미스, 클라우드 및에 지 Kubernetes 클러스터에서 추론 워크 로드 실행 |  

> [!NOTE]
> 로컬 클러스터와 Azure Machine Learning 컴퓨팅 클러스터와 같은 컴퓨팅 대상은 학습 및 실험에 GPU를 지원하지만 _웹 서비스로 배포하면_ AKS에서만 유추에 GPU를 사용할 수 있습니다.
>
> _기계 학습 파이프라인을 통해 채점할 때_ GPU를 유추에 사용하는 것은 Azure Machine Learning 컴퓨팅에서만 지원됩니다.
> 
> 클러스터 SKU를 선택할 때는 먼저 스케일 업한 다음, 스케일 아웃합니다. 모델에 필요한 RAM이 150%인 머신에서 시작하여 결과를 프로파일링하고 필요한 성능을 갖춘 머신을 찾습니다. 이를 파악한 후에는 동시 추론 요구에 맞게 머신 수를 늘립니다.

> [!NOTE]
> * 컨테이너 인스턴스는 크기가 1GB 미만인 작은 모델에만 적합합니다.
> * 큰 모델의 개발/테스트에는 단일 노드 AKS 클러스터를 사용합니다.