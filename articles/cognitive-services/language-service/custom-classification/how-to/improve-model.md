---
title: 사용자 지정 텍스트 분류 모델 성능을 향상시키는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류에 대한 모델 개선에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 73513e90f37a3d003351604d119612306ba66277
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132062741"
---
# <a name="improve-model-performance"></a>모델 성능 향상

모델을 학습시킨 후 평가 세부 정보를 검토한 후 모델의 성능을 향상시켜야 하는지 여부를 결정할 수 있습니다. 이 문서에서는 모델에서 태그가 지정된 예측 클래스와 클래스 간의 불일치를 검토하고 데이터 분포를 검사합니다.

## <a name="prerequisites"></a>전제 조건

필요에 따라 모델을 개선하려면 다음이 필요합니다.

* 구성된 Azure Blob Storage 계정이 있는 [사용자 지정 분류 프로젝트](create-project.md) 
* 스토리지 계정에 [업로드된](create-project.md#prepare-training-data) 텍스트 데이터입니다.
* [태그가](train-model.md) [지정된 데이터를](tag-data.md) 사용하여 모델 학습 성공
* [모델 평가 세부 정보를 검토하여 모델의](view-model-evaluation.md) 성능을 확인했습니다.
* 평가에 사용되는 [평가 메트릭을 숙지했습니다.](../concepts/evaluation.md)

자세한 내용은 [애플리케이션 개발 수명 주기를](../overview.md#project-development-lifecycle) 참조하세요.

## <a name="review-test-set-predictions"></a>테스트 집합 예측 검토

Language Studio를 사용하여 모델의 수행 방식과 예상한 수행 방식을 검토할 수 있습니다. 학습한 각 모델에 대해 예측 및 태그가 지정된 클래스를 나란히 검토할 수 있습니다.

1. [Language Studio에서](https://aka.ms/languageStudio)프로젝트 페이지로 이동합니다.
    1. Language Studio에서 **텍스트 분류** 레이블이 있는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택합니다. 

2. 왼쪽 메뉴에서 **모델 향상을** 선택합니다.

3. **테스트 집합 검토를** 선택합니다.

4. 모델 드롭다운 메뉴에서 학습된 **모델을** 선택합니다.

5. 더 쉬운 분석을 위해 **잘못된 예측만 표시를** 전환하여 실수만 볼 수 있습니다.

    :::image type="content" source="../media/review-validation-set.png" alt-text="유효성 검사 집합 검토" lightbox="../media/review-validation-set.png":::

6. 클래스에 속해야 하는 파일이 지속적으로 클래스 로 분류되는 경우  `X` 이러한 클래스 간에 `Y` 모호성이 있으며 스키마를 다시 고려해야 합니다.

## <a name="examine-data-distribution-from-language-studio"></a>Language Studio에서 데이터 배포 검사

파일의 데이터 분포를 검사하여 어떤 클래스가 더 이상 표현되지 않는지 결정할 수 있습니다. 학습에 사용되는 파일이 클래스 간에 동일하게 분산되지 않고 모델 성능에 위험을 초래하는 경우 데이터 불균형이 발생합니다. 예를 들어 *클래스 1에* 태그가 지정된 파일이 50개 있고 *클래스 2에* 태그가 지정된 파일만 10개 있는 경우 이는 *클래스 1이* 과도하게 표시되고 *클래스 2가* 미달 표현되는 데이터 불균형입니다. 

이 경우 모델은 파일을 *클래스 1로* 분류하는 데 편향되어 있으며 *클래스 2를* 간과할 수 있습니다. 스키마가 모호한 경우 데이터 불균형으로 인해 더 복잡한 문제가 발생할 수 있습니다. 두 클래스가 명확하게 구분되지 않고 클래스 *2가* 부족한 경우 모델은 텍스트를 *클래스 1로* 분류할 가능성이 높습니다.

평가 [메트릭에서](../concepts/evaluation.md)클래스가 과도하게 표시되면 다른 클래스보다 회수율이 높은 경향이 있지만, 표현된 클래스의 회수 율이 낮습니다.

데이터 세트의 데이터 분포를 검사하려면 다음을 수행합니다.

1. [Language Studio의](https://aka.ms/languageStudio)프로젝트 페이지로 이동합니다.
    1. Language Studio에서 **텍스트 분류** 레이블이 있는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택합니다. 

2. 왼쪽 메뉴에서 **모델 향상을** 선택합니다.

3. **데이터 배포 검사를** 선택합니다.

    :::image type="content" source="../media/examine-data-distribution.png" alt-text="데이터 분포 검사" lightbox="../media/examine-data-distribution.png":::

4. **데이터 태그** 페이지로 돌아가기 데이터를 다르게 태그하는 방법에 대한 아이디어가 형성되면 조정합니다.

## <a name="next-steps"></a>다음 단계

* 모델의 수행 방식에 만족하면 런타임 API를 사용하여 [텍스트 분류 요청 보내기를](call-api.md) 시작할 수 있습니다.
