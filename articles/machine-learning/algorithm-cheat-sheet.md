---
title: Machine Learning 알고리즘 치트 시트 - 디자이너
titleSuffix: Azure Machine Learning
description: 인쇄 가능한 Machine Learning 알고리즘 치트 시트를 사용하면, Azure Machine Learning 디자이너에서 예측 모델에 적합한 알고리즘을 선택할 수 있습니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
author: FrancescaLazzeri
ms.author: lazzeri
ms.date: 10/21/2021
adobe-target: true
ms.openlocfilehash: f14a468130dbadfc9ae6a5668a9e1c71b880e818
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564920"
---
# <a name="machine-learning-algorithm-cheat-sheet-for-azure-machine-learning-designer"></a>Azure Machine Learning 디자이너용 Machine Learning 알고리즘 치트 시트

**Azure Machine Learning 알고리즘 치트 시트** 를 사용하면, 디자이너로부터 예측 분석 모델에 적합한 알고리즘을 선택할 수 있습니다.

Azure Machine Learning은 ***분류** _, _*_추천 시스템_*_, _*_클러스터링_*_, _*_변칙 검색_*_, _*_회귀_*_ , _ *_문자 분석_* * 항목에서 다양한 알고리즘 라이브러리를 보유하고 있습니다. 각 항목은 다양한 유형의 기계 학습 문제를 해결하기 위해 고안되었습니다.

자세한 내용은 [알고리즘 선택 방법](how-to-select-algorithms.md)을 참조하세요.

## <a name="download-machine-learning-algorithm-cheat-sheet"></a>다운로드: Machine Learning 알고리즘 치트 시트

**여기서 치트 시트 다운로드: [Machine Learning 알고리즘 치트 시트(11x17인치)](https://download.microsoft.com/download/3/5/b/35bb997f-a8c7-485d-8c56-19444dafd757/azure-machine-learning-algorithm-cheat-sheet-nov2019.pdf?WT.mc_id=docs-article-lazzeri)**

![Machine Learning 알고리즘 치트 시트: Machine Learning 알고리즘을 선택하는 방법에 대해 알아보기.](./media/algorithm-cheat-sheet/machine-learning-algorithm-cheat-sheet.png)

간편하게 보관하면서 알고리즘을 선택할 수 있도록 tabloid 크기로 Machine Learning 알고리즘 치트 시트를 다운로드하여 인쇄합니다.

## <a name="how-to-use-the-machine-learning-algorithm-cheat-sheet"></a>Machine Learning 알고리즘 치트 시트를 사용하는 방법

알고리즘 치트 시트에서 제공된 제안 사항은 대략적인 위치 조정 규칙입니다. 제안을 변형하거나 명백하게 위반할 수 있습니다. 이 치트 시트는 시작점을 제안하기 위한 것입니다. 데이터에 대해 여러 알고리즘을 실행하고 비교해 보세요. 각 알고리즘의 원칙 및 데이터를 생성한 시스템에 대한 이해가 필요합니다.

모든 Machine Learning 알고리즘에는 자체 스타일이나 귀납적 바이어스가 있습니다. 특정 문제에 대해 적합한 알고리즘이 여러 개 존재할 수 있으며, 그 중 어떤 알고리즘이 다른 알고리즘보다 적합도가 높을 수도 있습니다. 하지만 어떤 것이 가장 적합한지 항상 미리 알 수 없습니다. 이러한 경우 여러 알고리즘이 참고 자료에 함께 나열됩니다. 적절한 전략은 한 알고리즘을 시도하고, 결과가 만족스럽지 않을 경우 다른 알고리즘을 시도하는 것입니다. 

Azure Machine Learning 디자이너의 알고리즘에 대해 자세히 알아보려면 [알고리즘 및 구성 요소 참조](component-reference/component-reference.md)로 이동하세요.

## <a name="kinds-of-machine-learning"></a>Machine Learning의 종류

Machine Learning의 세 가지 주요 범주는 *감독 학습*, *자율 학습* 및 *보충 학습* 입니다.

### <a name="supervised-learning"></a>감독 학습

감독 학습에서 각 데이터 요소는 관심 범주 또는 값과 연결되거나 해당 레이블이 지정됩니다. 범주 레이블의 예는 이미지를 'cat' 또는 'dog'로 할당하는 것입니다. 값 레이블의 예는 중고차와 연결된 판매 가격입니다. 감독 학습의 목표는 이와 같은 많은 레이블이 지정된 예제를 연구한 다음 향후 데이터 요소에 대한 예측을 수행할 수 있도록 하는 것입니다. 예를 들어 올바른 동물로 새 사진을 식별하거나 다른 중고 자동차에 정확한 판매 가격을 할당합니다. 이는 널리 사용되고 유용한 기계 학습 유형입니다.

### <a name="unsupervised-learning"></a>자율 학습

자율 학습에서는 데이터 요소에 연결된 레이블이 없습니다. 대신, 자율 학습 알고리즘의 목표는 어떤 방식으로든 데이터를 구성하거나 해당 구조를 설명하는 것입니다. 자율 학습은 K-Means처럼 클러스터로 그룹화하거나 더 간단하게 표시되도록 복잡한 데이터를 보는 다양한 방법을 찾는 것입니다.

### <a name="reinforcement-learning"></a>보충 학습

보충 학습에서는 알고리즘이 각 데이터 요소에 대한 응답으로 작업을 선택합니다. 이는 지정 시간 센서 판독값 집합이 한 데이터 요소이며 알고리즘으로 로봇의 다음 동작을 선택해야 하는 로봇 공학의 일반적인 접근 방식입니다. 사물 인터넷의 애플리케이션에 적합한 학습이기도 합니다. 또한 학습 알고리즘은 짧은 시간 후에 결정이 얼마나 효율적이었는지를 나타내는 보상 신호를 받습니다. 보상 신호에 따라 알고리즘은 가장 높은 보상을 달성하기 위해 해당 전략을 수정합니다. 

## <a name="next-steps"></a>다음 단계

* 자세한 내용은 [알고리즘 선택 방법](how-to-select-algorithms.md)을 참조하세요

* [Azure Machine Learning 및 Azure Portal에서 스튜디오에 대해 알아보기](overview-what-is-azure-machine-learning.md).

* [자습서: Azure Machine Learning 디자이너에서 예측 모델을 작성합니다](tutorial-designer-automobile-price-train-score.md).

* [심층 학습 및 Machine Learning에 대해 알아보기](concept-deep-learning-vs-machine-learning.md).
