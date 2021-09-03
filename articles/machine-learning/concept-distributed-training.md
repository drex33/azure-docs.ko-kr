---
title: 분산 학습이란?
titleSuffix: Azure Machine Learning
description: Azure Machine Learning이 지원하는 분산 학습 유형 및 분산 학습에 사용할 수 있는 오픈 소스 프레임워크 통합에 대해 알아보세요.
services: machine-learning
ms.service: machine-learning
author: nibaccam
ms.author: nibaccam
ms.subservice: core
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 517eb0a0abfc00349b42e1948192ad53baca9c8b
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122603702"
---
# <a name="distributed-training-with-azure-machine-learning"></a>Azure Machine Learning을 사용한 분산 학습

이 문서에서는 분산 학습, 그리고 Azure Machine Learning이 이를 딥 러닝 모델에 맞게 지원하는 방법에 대해 알아봅니다. 

분산 학습에서 모델을 학습시키는 워크로드는 분할되어 작업자 노드라는 여러 미니 프로세서 간에 공유됩니다. 작업자 노드는 동시에 작동하여 모델 학습 속도를 향상합니다. 분산 학습을 기존 ML 모델에 사용할 수 있지만 심층 신경망을 학습하는 [딥 러닝](concept-deep-learning-vs-machine-learning.md)처럼 계산 및 시간 집약적인 작업에 더 적합합니다. 

## <a name="deep-learning-and-distributed-training"></a>딥 러닝 및 분산 학습 

분산 학습에는 [데이터 병렬 처리](#data-parallelism)와 [모델 병렬 처리](#model-parallelism)라는 두 가지 주요 유형이 있습니다. 딥 러닝 모델에 대한 분산 학습의 경우 [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/intro)에서 인기 있는 프레임워크, PyTorch 및 TensorFlow와의 통합을 지원합니다. 두 프레임워크는 분산 학습에 데이터 병렬 처리를 사용하고 계산 속도를 최적화하기 위해 [horovod](https://horovod.readthedocs.io/en/latest/summary_include.html)를 활용할 수 있습니다.


* [PyTorch를 사용하여 분산 학습](how-to-train-distributed-gpu.md#pytorch)

* [TensorFlow를 사용한 분산 학습](how-to-train-distributed-gpu.md#tensorflow)

분산 학습이 필요하지 않은 ML 모델의 경우 [Azure Machine Learning을 사용한 모델 학습](concept-train-machine-learning-model.md#python-sdk)을 참조하여 Python SDK를 사용하여 모델을 학습하는 다양한 방법을 확인하세요.

## <a name="data-parallelism"></a>데이터 병렬 처리

데이터 병렬 처리는 두 개의 분산 학습 방법을 구현하는 가장 쉬운 방법이며 대부분의 사용 사례에 충분합니다.

이 접근 방식에서 데이터는 파티션으로 나뉩니다. 여기서 파티션 수는 컴퓨팅 클러스터에서 사용 가능한 총 노드 수와 동일합니다. 모델은 각 작업자 노드에 복사되고 각 작업자는 해당 데이터의 하위 집합에 따라 작동합니다. 각 노드에는 학습 중인 모델을 지원할 수 있는 용량이 있어야 합니다. 즉, 모델은 각 노드에 전적으로 맞아야 합니다. 다음 다이어그램은 이 방법의 시각적 데모를 제공합니다.

![Data-parallelism-concept-diagram](./media/concept-distributed-training/distributed-training.svg)

각 노드는 학습 샘플과 레이블이 지정된 출력에 대한 예측 간의 오류를 독립적으로 계산합니다. 결국 각 노드가 오류를 기반으로 모델을 업데이트하고 모든 변경 내용을 다른 노드에 전달하여 해당 모델을 업데이트해야 합니다. 즉, 작업자 노드는 일괄 처리 계산의 마지막에 모델 매개 변수 또는 그라데이션을 동기화하여 일관된 모델을 학습해야 합니다. 

## <a name="model-parallelism"></a>모델 병렬 처리

네트워크 병렬 처리라고도 하는 모델 병렬 처리에서 모델은 서로 다른 노드에서 동시에 실행할 수 있는 여러 부분으로 분할되며 각 부분은 동일한 데이터를 기반으로 실행됩니다. 이 방법의 확장성은 알고리즘의 작업 병렬 처리 수준에 따라 달라지며 데이터 병렬 처리보다 구현하기가 더 복잡합니다. 

모델 병렬 처리에서 작업자 노드는 일반적으로 전달 또는 역 전파 단계마다 공유 매개 변수를 한 번만 동기화해야 합니다. 또한 각 노드가 동일한 학습 데이터를 기반으로 하는 모델의 하위 섹션에서 작동하므로 더 큰 모델은 문제가 되지 않습니다.

## <a name="next-steps"></a>다음 단계

* Python SDK를 사용하여 [모델 학습에 컴퓨팅 대상을 사용](how-to-set-up-training-targets.md)하는 방법을 알아봅니다.
* 기술 예제를 보려면 [참조 아키텍처 시나리오](/azure/architecture/reference-architectures/ai/training-deep-learning)를 참조하세요.
* [분산 GPU 교육 가이드](how-to-train-distributed-gpu.md)에서 MPI, TensorFlow 및 PyTorch에 대한 팁 찾기 