---
title: PyTorch 모델 학습
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너의 학습 PyTorch 모델 구성 요소를 사용 하 여 모델을 처음부터 학습 하거나 기존 모델을 미세 조정할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 03/19/2021
ms.openlocfilehash: 0d2bf3b403aab29f0e47fd8aac1aa935acb31dd5
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570160"
---
# <a name="train-pytorch-model"></a>PyTorch 모델 학습

이 문서에서는 Azure Machine Learning designer에서 **학습 PyTorch 모델** 구성 요소를 사용 하 여 DenseNet와 같은 PyTorch 모델을 학습 하는 방법을 설명 합니다. 모델을 정의하고 매개 변수를 설정한 후에 학습이 수행되며, 학습을 위해서는 레이블이 지정된 데이터가 필요합니다. 

현재 **학습 PyTorch 모델** 구성 요소는 단일 노드와 분산 학습을 모두 지원 합니다.

## <a name="how-to-use-train-pytorch-model"></a>PyTorch 모델 학습 사용 방법 

1. 디자이너에서 파이프라인 초안에 [DenseNet](densenet.md) 구성 요소 또는 [resnet](resnet.md) 을 추가 합니다.

2. 파이프라인에 **학습 PyTorch 모델** 구성 요소를 추가 합니다. **모델 학습** 범주에서이 구성 요소를 찾을 수 있습니다. **학습** 을 확장 하 고 **PyTorch 모델** 구성 요소를 파이프라인으로 끌어 놓습니다.

   > [!NOTE]
   > **학습 PyTorch 모델** 구성 요소는 대량 데이터 집합에 대 한 **GPU** 유형 계산에서 더 효율적으로 실행 됩니다. 그렇지 않으면 파이프라인이 실패 합니다. **다른 계산 대상 사용** 을 설정 하 여 구성 요소의 오른쪽 창에서 특정 구성 요소에 대 한 compute를 선택할 수 있습니다.

3.  왼쪽 입력에서 학습되지 않은 모델을 연결합니다. 학습 데이터 세트 및 유효성 검사 데이터 세트를 **PyTorch 모델 학습** 의 가운데 및 오른쪽 입력에 연결합니다.

    학습되지 않은 모델의 경우 DenseNet과 같은 PyTorch 모델이어야 합니다. 그렇지 않으면 'InvalidModelDirectoryError'가 발생합니다.

    학습 데이터 세트는 레이블이 지정된 이미지 디렉터리여야 합니다. 레이블이 지정된 이미지 디렉터리를 가져오는 방법은 **이미지 디렉터리로 변환** 을 참조하세요. 레이블이 지정되지 않은 경우 'NotLabeledDatasetError'가 발생합니다.

    학습 데이터 세트와 유효성 검사 데이터 세트의 레이블 범주가 동일해야 합니다. 그렇지 않으면 InvalidDatasetError가 발생합니다.

4.  **Epoch** 에 학습시킬 Epoch 수를 지정합니다. 각 Epoch에서 전체 데이터 세트가 반복되며, 기본값은 5입니다.

5.  **배치 크기** 에 배치로 학습시킬 인스턴스 수를 지정합니다(기본값 16).

6.  **준비 단계 수** 에 초기 학습 속도가 수렴을 시작하기에 다소 큰 경우 학습을 준비하려는 Epoch 수를 지정합니다(기본값 0).

7.  **학습 속도** 에 *학습 속도* 값을 지정합니다(기본값: 0.001). 학습 속도는 모델을 테스트하고 수정할 때마다 sgd와 같은 최적화 프로그램에서 사용되는 단계의 크기를 제어합니다.

    속도를 더 낮게 설정하면 모델을 더 자주 테스트할 수 있지만 로컬 정체 상태에 고착될 위험이 있습니다. 속도를 더 높게 설정하면 더 빨리 수렴할 수 있지만 실제 최솟값이 과도해질 위험이 있습니다.

    > [!NOTE]
    > 학습 중에 학습 속도가 너무 높아서 학습 손실이 nan이 될 경우 학습 속도를 낮추는 것이 도움이 될 수 있습니다.
    > 분산 학습에서 그라데이션 하강을 안정적으로 유지하기 위해 실제 학습 속도는 `lr * torch.distributed.get_world_size()`로 계산됩니다. 프로세스 그룹의 일괄 처리 크기는 월드 크기에 단일 프로세스의 크기를 곱한 값이기 때문입니다.
    > 다항식 학습 속도 감소가 적용되며 더 나은 성능의 모델을 만드는 데 도움이 될 수 있습니다.

8.  필요한 경우 **임의 시드** 에 시드로 사용할 정수 값을 입력합니다. 각 실행 간에 실험 재현성을 보장하려면 시드를 사용하는 것이 좋습니다.

9.  유효성 검사 손실이 연속적으로 감소하지 않는다면 **페이션스** 에 학습을 조기 중지할 Epoch 수를 지정합니다. 기본값은 3입니다.

10. **인쇄 빈도** 에 각 Epoch에서 반복할 때의 학습 로그 인쇄 빈도를 지정합니다(기본값 10).

11. 파이프라인을 제출합니다. 데이터 세트의 크기가 더 큰 경우 시간이 걸리므로 GPU 컴퓨팅을 사용하는 것이 좋습니다.

## <a name="distributed-training"></a>분산 학습

분산 학습에서 모델 학습을 위한 워크로드는 작업자 노드라고 하는 여러 미니 프로세서 사이에 분할되고 공유됩니다. 작업자 노드는 동시에 작동하여 모델 학습 속도를 향상합니다. 현재 디자이너는 **학습 PyTorch 모델** 구성 요소에 대 한 분산 학습을 지원 합니다.

### <a name="training-time"></a>학습 시간

**PyTorch 모델 학습** 에서 분산 학습을 수행하면 몇 시간 만에 ImageNet(1,000개 클래스, 120만개 이미지) 등 대규모 데이터 세트를 학습시킬 수 있습니다. 다음 표는 다양한 디바이스에서 ImageNet에 대해 Resnet50을 처음부터 50 Epoch 학습시킬 때의 학습 시간과 성능을 보여줍니다.

| 디바이스       | 학습 시간  | 학습 처리량  | 상위 1개 유효성 검사 정확도 | 상위 5개 유효성 검사 정확도 |
| ------------- | -------------- | -------------------- | ------------------------- | ------------------------- |
| V100 GPU 16개  | 6시간 22분        | ~3,200개 이미지/초     | 68.83%                    | 88.84%                    | 
| V100 GPU 8개   | 12시간 21분       | ~1,670개 이미지/초     | 68.84%                    | 88.74%                    |

이 구성 요소 ' 메트릭 ' 탭을 클릭 하 고 ' 초당 이미지 학습 ' 및 ' 상위 1 정확도 '와 같은 학습 메트릭 그래프를 확인 합니다.

[![학습 메트릭을 보여주는 스크린샷](./media/module/train-pytorch-model-train-metrics.png)](././media/module/train-pytorch-model-train-metrics.png#lightbox)

### <a name="how-to-enable-distributed-training"></a>분산 학습을 사용하도록 설정하는 방법

**학습 PyTorch 모델** 구성 요소에 대 한 분산 학습을 사용 하도록 설정 하려면 구성 요소의 오른쪽 창에서 **실행 설정** 을 설정 합니다. 분산 학습에는 **[AML 컴퓨팅 클러스터](../how-to-create-attach-compute-cluster.md?tabs=python)** 만 지원됩니다.

> [!NOTE]
> NCCL 백엔드 학습 PyTorch 모델 구성 요소가 필요한 요청을 사용 하기 때문에 분산 교육을 활성화 하려면 **여러 gpu** 가 필요 합니다.

1. 구성 요소를 선택 하 고 오른쪽 패널을 엽니다. **실행 설정** 섹션을 확장합니다.

    [![runsetting에서 분산 학습을 설정하는 방법을 보여주는 스크린샷](./media/module/distributed-training-run-setting.png)](./media/module/distributed-training-run-setting.png#lightbox)

2. 컴퓨팅 대상으로 AML 컴퓨팅을 선택했는지 확인합니다.

3. **리소스 레이아웃** 섹션에서 다음 값을 설정해야 합니다.

    - **노드 수:** 학습에 사용할 컴퓨팅 대상의 노드 수입니다. 컴퓨팅 클러스터의 **최대 노드 수** 보다 **적거나 같아야** 합니다. 기본값은 1개로, 단일 노드 작업을 의미합니다.

    - **노드당 프로세스 수**: 노드당 트리거되는 프로세스의 수입니다. 컴퓨팅의 **처리 단위** **보다 적거나 같아야** 합니다. 기본값은 1개로, 이는 단일 프로세스 작업을 의미합니다.

    컴퓨팅 세부 정보 페이지에서 컴퓨팅 이름을 클릭하여 컴퓨팅의 **최대 노드 수** 와 **처리 단위** 를 확인할 수 있습니다.

    [![컴퓨팅 클러스터를 확인하는 방법을 보여주는 스크린샷](./media/module/compute-cluster-node.png)](./media/module/compute-cluster-node.png#lightbox)

Azure Machine Learning에서의 분산 학습에 대한 자세한 내용은 [여기서](../concept-distributed-training.md) 알아볼 수 있습니다.

### <a name="troubleshooting-for-distributed-training"></a>분산 학습 문제 해결

이 구성 요소에 대 한 분산 교육을 사용 하는 경우 각 프로세스에 대 한 드라이버 로그가 있습니다. `70_driver_log_0`은 마스터 프로세스용입니다. 오른쪽 창의 **출력+로그** 탭에서 각 프로세스의 오류 세부 정보에 대한 드라이버 로그를 확인할 수 있습니다.

[![드라이버 로그를 보여주는 스크린샷](./media/module/distributed-training-error-driver-log.png)](./media/module/distributed-training-error-driver-log.png#lightbox) 

구성 요소에서 사용 하도록 설정 된 분산 교육이 `70_driver` 로그 없이 실패 한 경우 `70_mpi_log` 오류 세부 정보를 확인할 수 있습니다.

다음 예제에서는 **노드당 프로세스 수** 가 컴퓨팅의 **처리 단위** 보다 큰 일반적인 오류를 보여줍니다.

[![mpi 로그를 보여주는 스크린샷](./media/module/distributed-training-error-mpi-log.png)](./media/module/distributed-training-error-mpi-log.png#lightbox)

구성 요소 문제 해결에 대 한 자세한 내용은 [이 문서](designer-error-codes.md) 를 참조할 수 있습니다.

## <a name="results"></a>결과

파이프라인 실행이 완료된 후 점수 매기기에 모델을 사용하려면 [PyTorch 모델 학습](train-PyTorch-model.md)을 [이미지 모델 채점](score-image-model.md)에 연결하여 새 입력 예제의 값을 예측합니다.

## <a name="technical-notes"></a>기술 정보
###  <a name="expected-inputs"></a>예상 입력  

| Name               | Type                    | Description                              |
| ------------------ | ----------------------- | ---------------------------------------- |
| 학습되지 않은 모델    | UntrainedModelDirectory | 학습되지 않은 모델, PyTorch 필요         |
| 학습 데이터 세트   | ImageDirectory          | 학습 데이터 세트                         |
| 유효성 검사 데이터 세트 | ImageDirectory          | 각 Epoch의 평가를 위한 유효성 검사 데이터 세트 |

###  <a name="component-parameters"></a>구성 요소 매개 변수  

| Name          | 범위            | Type    | 기본값 | Description                              |
| ------------- | ---------------- | ------- | ------- | ---------------------------------------- |
| Epoch        | >0               | 정수 | 5       | 레이블 또는 결과 열이 포함된 열을 선택합니다. |
| Batch 크기    | >0               | 정수 | 16      | 일괄 학습할 인스턴스 수   |
| 준비 단계 수 | >=0         | 정수 | 0       | 학습을 준비할 Epoch 수 |
| 학습 속도 | >=double.Epsilon | Float   | 0.1   | 추측 기울기 하강 최적화 프로그램의 초기 학습 속도입니다. |
| 무작위 초기값   | 모두              | 정수 | 1       | 모델에서 사용하는 난수 생성기의 초기값입니다. |
| 페이션스      | >0               | 정수 | 3       | 학습을 조기에 중지할 Epoch 수   |
| 인쇄 빈도 |             >0 | 정수 | 10      | 각 Epoch에서 반복할 때의 학습 로그 인쇄 빈도 |

###  <a name="outputs"></a>출력  

| Name          | Type           | Description   |
| ------------- | -------------- | ------------- |
| 학습된 모델 | ModelDirectory | 학습된 모델 |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요.