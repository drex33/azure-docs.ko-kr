---
title: 사용자 지정 분류 모델을 학습 하는 방법-Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류를 위해 모델을 학습 하는 방법에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 2671796484f062440734d710acf95718a9a31998
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053218"
---
# <a name="how-to-train-a-text-classification-model"></a>텍스트 분류 모델을 학습 하는 방법


학습은 모델이 [태그가 지정 된 데이터](tag-data.md)에서 학습 하는 프로세스입니다. 교육이 완료 된 후 모델 [평가 메트릭을 사용](../how-to/view-model-evaluation.md) 하 여 [모델을 개선](../how-to/improve-model.md)해야 하는지 여부를 결정할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

모델을 학습 하기 전에 다음이 필요 합니다.

* 구성 된 Azure blob storage 계정을 사용 하 여 [성공적으로 프로젝트를 만들었습니다](create-project.md) . 
* 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md)

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="data-splits"></a>데이터 분할

학습 프로세스를 시작 하기 전에 데이터 집합의 파일을 임의로 세 그룹으로 나눕니다.

* **학습 집합** 에는 데이터 집합에 있는 파일의 80%가 포함 됩니다. 모델을 학습 하는 데 사용 되는 기본 집합입니다.

* **테스트 집합** 에는 데이터 집합에서 사용할 수 있는 파일의 20%가 포함 됩니다. 이 집합은 모델의 비편향 [평가](../how-to/view-model-evaluation.md) 를 제공 하는 데 사용 됩니다. 이 집합은 학습 중에 모델에는 도입 되지 않습니다. 학습 데이터를 다시 조정 하 고 결과를 변경 하지 않도록이 집합에 대 한 올바른 예측 및 잘못 된 예측의 세부 정보가 표시 되지 않습니다.

## <a name="train-model-in-language-studio"></a>언어 스튜디오에서 모델 학습

1. [언어 스튜디오](https://aka.ms/LanguageStudio)에서 프로젝트 페이지로 이동 합니다.

2. 왼쪽 메뉴에서 **학습** 을 선택합니다.

3. 새 모델을 학습시키려면 **새 모델 학습** 을 선택하고, 아래 텍스트 상자에서 모델 이름을 입력합니다. 이 옵션을 선택 하 고 아래 드롭다운에서 원하는 모델을 선택 하 여 **기존 모델을 덮어쓸** 수 있습니다.

    :::image type="content" source="../media/train-model.png" alt-text="새 모델 만들기" lightbox="../media/train-model.png":::

4. 페이지 아래쪽에서 **학습** 단추를 선택합니다.

모델을 학습 하는 데 걸리는 시간은 데이터 집합에 따라 다르며, 몇 시간이 걸릴 수 있습니다. 한 번에 하나의 모델만 학습 하 고, 동일한 프로젝트에서 이미 학습 하 고 있는 경우 다른 모델을 만들거나 학습할 수 없습니다. 


교육이 성공적으로 완료 된 후에는 다음 사항에 유의 하세요.

* [모델의 평가 세부 정보 보기](../how-to/view-model-evaluation.md) 모델 학습 후에는 학습 중에 모델에 도입 되지 않은 [테스트 집합](../how-to/train-model.md#data-splits)에 대해 모델 평가가 수행 됩니다. 평가를 확인 하 여 실제 시나리오에서 모델이 수행 되는 방식을 파악할 수 있습니다.

* [데이터 배포 검사](../how-to/improve-model.md#examine-data-distribution-from-language-studio) 모든 클래스가 제대로 표시 되 고 모든 클래스를 적절 하 게 표시 하도록 균형 있는 데이터 분포가 있는지 확인 합니다. 특정 클래스를 다른 클래스 보다 훨씬 더 자주 태그를 지정 하는 경우이 클래스는 아래에 표시 될 수 있으며, 대부분의 경우 런타임에 모델에서 제대로 인식 되지 않을 수 있습니다. 이 경우이 클래스에 속하는 파일을 데이터 집합에 추가 하는 것이 좋습니다.

* [성능 향상 (선택 사항)](../how-to/improve-model.md) 오류 분석에 따라 [태그가 지정 된 데이터](tag-data.md) 를 수정 하는 것 외에도, 수행 중인 엔터티 형식에 대 한 태그의 수를 늘리거나 태그가 지정 된 데이터의 다양성을 향상 시킬 수 있습니다. 이렇게 하면 오류가 발생 하는 잠재적인 언어적 현상에 대해 모델에서 올바른 예측을 제공 하는 데 도움이 됩니다.

<!-- * Define your own test set: If you are using a random split option and the resulting test set was not comprehensive enough, consider defining your own test to include a variety of data layouts and balanced tagged classes.
 -->


## <a name="next-steps"></a>다음 단계

교육이 완료 된 후 모델 [평가 메트릭을 사용](../how-to/view-model-evaluation.md) 하 여 필요에 따라 [모델을 향상](../how-to/improve-model.md)시킬 수 있습니다. 모델에 만족 하면 모델을 배포 하 여 [텍스트를 분류](call-api.md)하는 데 사용할 수 있게 할 수 있습니다.
