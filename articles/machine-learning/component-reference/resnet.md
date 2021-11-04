---
title: ResNet
titleSuffix: Azure Machine Learning
description: Azure Machine Learning 디자이너에서 ResNet 알고리즘을 사용하는 이미지 분류 모델을 만드는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: c6be11cc4dafb5423d38bc869fc8b915b48814ce
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570001"
---
# <a name="resnet"></a>ResNet

이 문서에서는 Azure Machine Learning designer에서 **resnet** 구성 요소를 사용 하 여 resnet 알고리즘을 사용 하 여 이미지 분류 모델을 만드는 방법을 설명 합니다.  

이 분류 알고리즘은 관리되는 학습 방법이며 레이블이 지정된 데이터 세트가 필요합니다. 
> [!NOTE]
> 이 구성 요소는 스튜디오의 *데이터 레이블에* 의해 생성 된 레이블이 지정 된 데이터 집합을 지원 하지 않지만 [이미지 디렉터리](convert-to-image-directory.md) 구성 요소로 변환에서 생성 된 레이블이 지정 된 이미지 디렉터리만 지원 합니다. 

모델과 레이블이 지정된 이미지 디렉터리를 입력으로 [Pytorch 모델 학습](train-pytorch-model.md)에 제공하여 모델을 학습시킬 수 있습니다. 그러면 학습된 모델을 사용하여 [이미지 모델 채점](score-image-model.md)으로 새로운 입력 예제의 값을 예측할 수 있습니다.

### <a name="more-about-resnet"></a>ResNet에 대한 자세한 정보

ResNet에 대한 자세한 내용은 [이 문서](https://pytorch.org/vision/stable/models.html#torchvision.models.resnext101_32x8d)를 참조하세요.

## <a name="how-to-configure-resnet"></a>ResNet 구성 방법

1.  디자이너에서 **Resnet** 구성 요소를 파이프라인에 추가 합니다.  

2.  **모델 이름** 에 특정 ResNet 구조체 이름을 지정합니다. 지원되는 ResNet('resnet18', 'resnet34', 'resnet50', 'resnet101', 'resnet152', 'resnet152', 'resnext50\_32x4d', 'resnext101\_32x8d', 'wide_resnet50\_2', 'wide_resnet101\_2') 중에서 선택할 수 있습니다.

3.  **사전 학습** 에 ImageNet에서 사전 학습된 모델 사용 여부를 지정합니다. 선택하면 선택한 사전 학습된 모델을 기준으로 미세 조정할 수 있습니다. 선택 취소하면 처음부터 학습시킬 수 있습니다.

4.  **나머지 0 초기화** 에 각 나머지 분기에서 마지막 일괄 처리 일반 레이어를 0으로 초기화할지 여부를 지정합니다. 선택하면 나머지 분기가 0으로 시작하고 각 나머지 블록은 ID처럼 동작합니다. 이는 https://arxiv.org/abs/1706.02677 에 따라 대형 일괄 처리 크기를 수렴하는 데 도움이 될 수 있습니다.

5.  **Resnet** 구성 요소, 학습 및 유효성 검사 이미지 데이터 집합 구성 요소의 출력을 [학습 Pytorch 모델](train-pytorch-model.md)에 커넥트 합니다. 

6.  파이프라인을 제출합니다.

## <a name="results"></a>결과

파이프라인 실행이 완료된 후 점수 매기기에 모델을 사용하려면 [PyTorch 모델 학습](train-pytorch-model.md)을 [이미지 모델 채점](score-image-model.md)에 연결하여 새 입력 예제의 값을 예측합니다.

## <a name="technical-notes"></a>기술 정보  

###  <a name="component-parameters"></a>구성 요소 매개 변수  

| Name       | 범위 | Type    | 기본값           | Description                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| 모델 이름 | 모두   | 모드    | resnext101\_32x8d | 특정 ResNet 구조체 이름       |
| 사전 학습 | 모두   | 부울 | True              | ImageNet에서 사전 학습된 모델 사용 여부 |
| 나머지 0 초기화 | 모두 | 부울 | 아니요 | 각 나머지 분기에서 마지막 일괄 처리 일반 레이어를 0으로 초기화할지 여부 |
|            |       |         |                   |                                          |

###  <a name="output"></a>출력  

| Name            | Type                    | Description                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 학습되지 않은 모델 | UntrainedModelDirectory | Pytorch 모델 학습에 연결할 수 있는 학습되지 않은 ResNet 모델입니다. |

## <a name="next-steps"></a>다음 단계

Azure Machine Learning 하는 데 [사용할 수 있는 구성 요소 집합](component-reference.md) 을 참조 하세요. 