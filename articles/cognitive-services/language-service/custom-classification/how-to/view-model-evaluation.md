---
title: 사용자 지정 분류 모델 평가 보기-Azure Cognitive Services
titleSuffix: Azure Cognitive Services
description: 사용자 지정 분류 모델에 대 한 평가 점수를 확인 하는 방법 알아보기
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 89cb476141544c136ac38abf996cf16d3ac4d002
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103061"
---
# <a name="view-the-model-evaluation"></a>모델 평가 보기

모델 평가를 검토 하는 것은 사용자 지정 분류 모델을 개발 하는 중요 한 단계입니다. 모델의 성능을 파악 하는 데 도움이 되며, 프로덕션 환경에서 사용 될 때 수행 되는 방법에 대 한 아이디어를 제공 합니다. 


## <a name="prerequisites"></a>사전 요구 사항

모델을 학습 하기 전에 다음이 필요 합니다.
* Azure blob storage 계정이 구성 된 [사용자 지정 분류 프로젝트](create-project.md) 
* 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md)
* 성공적으로 [학습 된 모델](train-model.md)

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.

## <a name="model-evaluation"></a>모델 평가

평가 프로세스에서는 학습 된 모델을 사용 하 여 테스트 집합의 파일에 대 한 사용자 정의 클래스를 예측 하 고 제공 된 데이터 태그와 비교 합니다. 테스트 집합은 학습 프로세스 중에 모델에 도입되지 않은 데이터로 구성됩니다. 

## <a name="view-the-model-details-using-language-studio"></a>언어 스튜디오를 사용 하 여 모델 세부 정보 보기

1. [언어 스튜디오](https://aka.ms/languageStudio)에서 프로젝트 페이지로 이동 합니다.
    1. 언어 스튜디오에서 **텍스트 분류** 라는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택 합니다. 

2. 왼쪽 메뉴에서 **모델 세부 정보 보기** 를 선택 합니다.

3. **상태** 열에서 모델 학습 상태를 확인 하 고 **f1 점수** 열에서 모델의 f1 점수를 확인 합니다.

    :::image type="content" source="../media/model-details-1.png" alt-text="모델 세부 정보 보기 단추" lightbox="../media/model-details-1.png":::

1. 자세한 내용은 모델 이름을 클릭하세요.

2. **개요** 섹션 아래의 **모델 수준** 평가 메트릭과 클래스 **성능 메트릭** 섹션 아래의 **클래스 수준** 평가 메트릭을 찾을 수 있습니다. 자세한 내용은 [평가 메트릭](../concepts/evaluation.md#model-level-and-class-level-evaluation-metrics) 을 참조 하세요.

    :::image type="content" source="../media/model-details-2.png" alt-text="모델 성능 메트릭" lightbox="../media/model-details-2.png":::

> [!NOTE]
> 여기에 표시 된 모든 클래스를 찾지 못한 경우에는 테스트 집합에이 클래스의 태그가 지정 된 파일이 없기 때문입니다.

**테스트 집합 혼동 행렬** 에서 모델에 대 한 혼동 행렬을 찾을 수 있습니다.

**단일 레이블 분류**

:::image type="content" source="../media/conf-matrix-single.png" alt-text="단일 클래스 분류를 위한 혼동 행렬" lightbox="../media/conf-matrix-single.png":::

**여러 레이블 분류**

:::image type="content" source="../media/conf-matrix-multi.png" alt-text="여러 클래스 분류에 대 한 혼동 행렬" lightbox="../media/conf-matrix-multi.png":::

## <a name="next-steps"></a>다음 단계

모델이 수행 하는 방법을 검토할 때 사용 되는 [평가 메트릭에](../concepts/evaluation.md) 대해 알아보세요. 모델 성능을 개선 해야 하는지 확인 한 후에 [는 모델 개선을](improve-model.md)시작할 수 있습니다.
