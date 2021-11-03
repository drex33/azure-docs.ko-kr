---
title: 대화형 Language Understanding에서 모델을 학습하고 평가하는 방법
titleSuffix: Azure Cognitive Services
description: 이 문서를 사용하여 모델을 학습하고 평가 세부 정보를 확인하여 개선합니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: a5f4ccb11b759f735618ef991e7dfd85fd071035
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053221"
---
# <a name="train-and-evaluate-models"></a>모델 학습 및 평가

[발화에 태그 지정](./tag-utterances.md)을 완료한 후 모델을 학습할 수 있습니다. 학습은 프로젝트의 학습 데이터의 현재 상태를 변환하여 예측에 사용할 수 있는 모델을 빌드하는 작업입니다. 학습할 때마다 학습 인스턴스의 이름을 지정해야 합니다. 

동일한 프로젝트 내에서 여러 모델을 만들고 학습할 수 있습니다. 그러나 특정 모델을 다시 학습하는 경우 마지막 상태를 덮어씁니다.

학습 시간은 오케스트레이션 워크플로 프로젝트를 처리할 때 몇 초에서 발화의 [최대 제한](../service-limits.md)에 도달할 때 몇 시간까지 소요될 수 있습니다. 학습하기 전에 평가를 사용하도록 설정하는 옵션을 통해 모델의 수행 방식을 확인할 수 있습니다. 

## <a name="train-model"></a>모델 학습

새 모델 이름을 입력하거나 **모델 이름** 드롭다운에서 기존 모델을 선택합니다. 모델 이름을 추가한 후 enter 키를 누릅니다. **학습으로 평가 실행** 토글을 변경하여 모델을 평가할지 여부를 선택합니다. 사용하도록 설정되면 태그가 지정된 발화가 3부분(학습에 80%, 유효성 검사에 10%, 테스트에 10%)으로 분할됩니다. 이후 모델의 평가 결과를 볼 수 있습니다.

:::image type="content" source="../media/train-model.png" alt-text="대화형 Language Understanding 프로젝트의 모델 학습 페이지를 보여주는 스크린샷." lightbox="../media/train-model.png":::

**학습** 단추를 클릭하고 모델 학습이 완료될 때까지 기다립니다. 모델 세부 정보 보기 페이지에서 모델의 학습 상태를 볼 수 있습니다.

## <a name="evaluate-model"></a>모델 평가

모델 학습을 완료한 후에 모델 세부 정보를 보고 학습 단계에서 평가를 사용하도록 설정한 경우 테스트 집합에 대해 모델이 얼마나 잘 수행되는지 확인할 수 있습니다. 모델이 얼마나 잘 수행되었는지 관찰하는 것을 평가라고 합니다. 테스트 집합은 발화의 20%로 구성되며, 이 분할은 학습 전에 임의로 수행됩니다. 테스트 집합은 학습 프로세스 중에 모델에 도입되지 않은 데이터로 구성됩니다. 평가 프로세스를 완료하려면 학습 집합에 10개 이상의 발화가 있어야 합니다.

**모델 세부 정보 보기** 페이지에서 현재 학습 상태 및 마지막으로 학습된 날짜와 함께 모든 모델을 볼 수 있습니다.

:::image type="content" source="../media/model-page-1.png" alt-text="대화형 Language Understanding 프로젝트의 모델 세부 정보 페이지를 보여주는 스크린샷." lightbox="../media/model-page-1.png":::

* 자세한 내용은 모델 이름을 클릭하세요. 이전에 평가를 사용하도록 설정한 경우에만 모델 이름을 클릭할 수 있습니다. 
* **개요** 섹션에서 선택한 옵션에 따라 집합적 의도 또는 엔터티에 대한 매크로 정밀도, 재현율 및 F1 점수를 찾을 수 있습니다. 
* **의도** 및 **엔터티** 탭에서 각 의도 또는 엔터티에 대한 마이크로 정밀도, 재현율 및 F1 점수를 별도로 찾을 수 있습니다.

> [!NOTE]
> 여기에 표시된 모델의 의도 또는 엔터티가 표시되지 않는 경우 이는 테스트 집합에 사용된 발화에 포함되지 않기 때문입니다.

화면 상단에 있는 [테스트 집합 혼동 행렬](../concepts/evaluation-metrics.md#confusion-matrix) 탭을 클릭하여 의도 및 엔터티에 대한 **혼동 행렬** 을 볼 수 있습니다. 

## <a name="next-steps"></a>다음 단계
* [모델 평가 메트릭](../concepts/evaluation-metrics.md)
* [모델 배포 및 쿼리](./deploy-query-model.md)
