---
title: 사용자 지정 텍스트 분류 모델 성능을 향상 하는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류를 위한 모델 향상에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: eae333dfd399e33063b2b18777c45ea2c1b89a54
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053663"
---
# <a name="improve-model-performance"></a>모델 성능 향상

모델을 학습 한 후 평가 세부 정보를 검토 한 후 모델의 성능을 향상 시켜야 할지 결정할 수 있습니다. 이 문서에서는 모델에 의해 태그가 지정 된 예측 클래스와 클래스 간의 불일치를 검토 하 고 데이터 배포를 검사 합니다.

## <a name="prerequisites"></a>사전 요구 사항

필요에 따라 모델을 향상 시키려면 다음이 필요 합니다.

* Azure blob storage 계정이 구성 된 [사용자 지정 분류 프로젝트](create-project.md) 
* 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [모델을](train-model.md) 성공적으로 학습 하기 위한 [태그가 지정 된 데이터](tag-data.md)
* 모델 [평가 세부 정보](view-model-evaluation.md) 를 검토 하 여 모델의 수행 방식을 확인 합니다.
* 평가에 사용 되는 [평가 메트릭에](../concepts/evaluation.md) 대 한 알아보기

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="review-test-set-predictions"></a>테스트 집합 예측 검토

Language Studio를 사용 하 여 모델에서 수행 하는 방법 및 예상 되는 방법을 검토할 수 있습니다. 학습 한 각 모델에 대해 예측 및 태그가 지정 된 클래스를 나란히 검토할 수 있습니다.

1. [언어 스튜디오](https://aka.ms/languageStudio)에서 프로젝트 페이지로 이동 합니다.
    1. 언어 스튜디오에서 **텍스트 분류** 라는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택 합니다. 

2. 왼쪽 메뉴에서 **모델 개선** 을 선택 합니다.

3. **테스트 집합 검토** 를 선택 합니다.

4. **모델** 드롭다운 메뉴에서 학습 된 모델을 선택 합니다.

5. 보다 쉽게 분석 하기 위해 **잘못 된 예측 표시** 를 설정/해제 하 여 실수가 표시 되도록 설정할 수 있습니다.

    :::image type="content" source="../media/review-validation-set.png" alt-text="유효성 검사 집합 검토" lightbox="../media/review-validation-set.png":::

6. 클래스에 속해야 하는 파일이  `X` 지속적으로 클래스로 분류 되는 경우 `Y` 이러한 클래스 간에 모호성이 있음을 의미 하므로 스키마를 다시 고려해 야 합니다.

## <a name="examine-data-distribution-from-language-studio"></a>언어 스튜디오에서 데이터 배포 검사

파일의 데이터 배포를 검사 하 여 클래스의 표시 여부를 결정할 수 있습니다. 학습에 사용 되는 파일이 클래스 간에 균등 하 게 분산 되지 않고 성능 모델에 대 한 위험을 야기 하는 경우 데이터 불균형이 발생 합니다. 예를 들어 클래스 *1* 에 50 태그가 지정 된 파일이 있지만 클래스 *2* 에는 태그가 지정 된 파일 10 개가 있는 경우이는 *클래스 1* 이 보다 위에 있고 *클래스 2* 가 표시 되는 데이터 불균형입니다. 

이 경우 모델은 파일을 *클래스 1* 로 분류 하 고 *클래스 2* 를 간과 하지 않을 수 있습니다. 스키마가 모호한 경우 데이터 불균형에서 더 복잡 한 문제가 발생할 수 있습니다. 두 클래스를 명확 하 게 구분 하지 않고 *클래스 2* 를 가장 하는 것은 모델에서 텍스트를 *클래스 1* 로 분류할 가능성이 가장 큽니다.

[계산 메트릭에](../concepts/evaluation.md)따라 클래스가 표시 되는 경우에는 다른 클래스 보다 더 높은 회수를 갖게 되며, 표시 된 클래스에는 더 낮은 회수가 있습니다.

데이터 집합의 데이터 배포를 검사 하려면:

1. [언어 스튜디오](https://aka.ms/languageStudio)에서 프로젝트 페이지로 이동 합니다.
    1. 언어 스튜디오에서 **텍스트 분류** 라는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택 합니다. 

2. 왼쪽 메뉴에서 **모델 개선** 을 선택 합니다.

3. **데이터 배포 검사** 를 선택 합니다.

    :::image type="content" source="../media/examine-data-distribution.png" alt-text="데이터 배포 검사" lightbox="../media/examine-data-distribution.png":::

4. **태그 데이터** 페이지로 돌아가 데이터에 다르게 태그를 지정 하는 방법에 대 한 아이디어를 구성 하 고 나면 조정을 수행 합니다.

## <a name="next-steps"></a>다음 단계

* 모델이 수행 되는 방식에 만족 했으면 런타임 API를 사용 하 여 [텍스트 분류 요청을 보낼](call-api.md) 수 있습니다.
