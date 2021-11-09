---
title: 사용자 지정 분류 모델 평가 보기 - Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: 사용자 지정 분류 모델에 대한 평가 점수를 보는 방법 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 6b7d56ee6eb7280b285442a2c82001b100d1cf06
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132054375"
---
# <a name="view-the-model-evaluation"></a>모델 평가 보기

모델 평가를 검토하는 것은 사용자 지정 분류 모델을 개발할 때 중요한 단계입니다. 모델이 얼마나 잘 수행되는지 알아보고 프로덕션에서 사용할 때 모델의 성능을 파악하는 데 도움이 됩니다. 


## <a name="prerequisites"></a>전제 조건

모델을 학습하기 전에 다음이 필요합니다.
* 구성된 Azure Blob Storage 계정이 있는 [사용자 지정 분류 프로젝트](create-project.md) 
* 스토리지 계정에 [업로드된](create-project.md#prepare-training-data) 텍스트 데이터입니다.
* [태그가 지정된 데이터](tag-data.md)
* 성공적으로 [학습된 모델](train-model.md)

자세한 내용은 [애플리케이션 개발 수명 주기를](../overview.md#project-development-lifecycle) 참조하세요.

## <a name="model-evaluation"></a>모델 평가

평가 프로세스는 학습된 모델을 사용하여 테스트 집합의 파일에 대한 사용자 정의 클래스를 예측하고 제공된 데이터 태그와 비교합니다. 테스트 집합은 학습 프로세스 중에 모델에 도입되지 않은 데이터로 구성됩니다. 

## <a name="view-the-model-details-using-language-studio"></a>Language Studio를 사용하여 모델 세부 정보 보기

1. [Language Studio의](https://aka.ms/languageStudio)프로젝트 페이지로 이동합니다.
    1. Language Studio에서 **텍스트 분류** 레이블이 있는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택합니다. 

2. 왼쪽 메뉴에서 **모델 세부 정보 보기를** 선택합니다.

3. **상태** 열에서 모델 학습 상태를 확인하고 F1 점수 열에서 모델에 대한 **F1 점수를** 봅니다.

    :::image type="content" source="../media/model-details-1.png" alt-text="모델 세부 정보 보기 단추" lightbox="../media/model-details-1.png":::

1. 자세한 내용은 모델 이름을 클릭하세요.

2. **모델 수준** 평가 메트릭은 **개요** 섹션에서 찾을 수 있고 클래스 수준 평가 메트릭은 **클래스** **성능 메트릭** 섹션에서 찾을 수 있습니다. 자세한 내용은 [평가 메트릭을](../concepts/evaluation.md#model-level-and-class-level-evaluation-metrics) 참조하세요.

    :::image type="content" source="../media/model-details-2.png" alt-text="모델 성능 메트릭" lightbox="../media/model-details-2.png":::

> [!NOTE]
> 여기에 표시된 모든 클래스를 찾을 수 없는 경우 테스트 집합에 이 클래스의 태그가 지정된 파일이 없기 때문입니다.

테스트 **집합 혼동 행렬** 에서 모델에 대한 혼동 행렬을 찾을 수 있습니다.

> [!NOTE]
> 혼동 행렬은 현재 여러 레이블 분류 프로젝트에 대해 지원되지 않습니다.

**단일 레이블 분류**

:::image type="content" source="../media/conf-matrix-single.png" alt-text="단일 클래스 분류에 대한 혼동 행렬" lightbox="../media/conf-matrix-single.png":::

<!-- **Multiple Label Classification**

:::image type="content" source="../media/conf-matrix-multi.png" alt-text="Confusion matrix for multiple class classification" lightbox="../media/conf-matrix-multi.png"::: -->

## <a name="next-steps"></a>다음 단계

모델의 수행 방식을 검토할 때 사용되는 [평가 메트릭에](../concepts/evaluation.md) 대해 알아봅니다. 모델 성능을 개선해야 하는지 여부를 알고 나면 [모델 향상을](improve-model.md)시작할 수 있습니다.
