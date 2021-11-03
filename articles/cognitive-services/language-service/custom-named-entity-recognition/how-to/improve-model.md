---
title: 사용자 지정 NER (명명 된 엔터티 인식) 모델 성능을 개선 하는 방법
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식 (NER)에 대 한 모델 향상에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 5ae65097fbd4e7bdc9f4e602a64bc392e45ef75e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053538"
---
# <a name="improve-the-performance-of-custom-named-entity-recognition-ner-models"></a>사용자 지정 NER (명명 된 엔터티 인식) 모델의 성능 향상 

모델을 학습 한 후 평가 세부 정보를 검토 하 고 나면 모델 성능을 향상 시킬 수 있습니다. 이 문서에서는 모델에 의해 태그가 지정 된 예측 클래스와 클래스 간의 불일치를 검토 하 고 데이터 배포를 검사 합니다.

## <a name="prerequisites"></a>사전 요구 사항

* 구성 된 Azure blob storage 계정을 사용 하 여 성공적으로 [프로젝트를 만들었습니다](create-project.md) .
    * 저장소 계정에 [업로드](create-project.md#prepare-training-data) 된 텍스트 데이터입니다.
* [태그가 지정 된 데이터](tag-data.md)
* [성공적으로 학습 된 모델](train-model.md)
* 모델 [평가 세부 정보](view-model-evaluation.md) 를 검토 하 여 모델의 수행 방식을 확인 합니다.
    * 평가에 사용 되는 [평가 메트릭을](../concepts/evaluation-metrics.md) 알아보기.

자세한 내용은 [응용 프로그램 개발 수명 주기](../overview.md#application-development-lifecycle) 를 참조 하세요.


## <a name="improve-model"></a>모델 개선

[모델 평가](view-model-evaluation.md)를 검토 한 후에는 모델의 예측에 무엇이 잘못 되었다는 것을 알 수 있습니다. 

> [!NOTE]
> 이 가이드에서는 학습 중에 생성 된 [유효성 검사 집합](train-model.md#data-split) 의 데이터를 중심으로 설명 합니다.

### <a name="review-validation-set"></a>유효성 검사 집합 검토

Language Studio를 사용 하 여 예상 되는 방법에 대해 모델이 수행 하는 방법을 검토할 수 있습니다. 학습 한 각 모델에 대해 예측 및 태그가 지정 된 클래스를 검토할 수 있습니다.

1. [언어 스튜디오](https://aka.ms/languageStudio)에서 프로젝트 페이지로 이동 합니다.
    1. 언어 스튜디오에서 **텍스트 분류** 라는 섹션을 찾습니다.
    2. **사용자 지정 텍스트 분류** 를 선택 합니다. 

2. 왼쪽 메뉴에서 **모델 개선** 을 선택 합니다.

3. **유효성 검사 집합 검토** 를 선택 합니다.

4. **모델** 드롭다운 메뉴에서 학습 된 모델을 선택 합니다.

5. 보다 쉽게 분석 하기 위해 **잘못 된 예측 표시** 를 설정/해제 하 여 실수가 표시 되도록 설정할 수 있습니다.

다음 정보를 사용 하 여 모델을 향상 시킬 수 있습니다. 

* 엔터티가 `X` 엔터티로 지속적으로 식별 되는 경우 `Y` 이러한 엔터티 형식 간에 모호성이 있음을 의미 하므로 스키마를 다시 고려해 야 합니다.

* 복잡 한 엔터티가 반복 해 서 추출 되지 않는 경우 더 쉽게 추출할 수 있도록 간단한 엔터티로 분리 하는 것이 좋습니다.

* 데이터에 태그가 지정 되지 않은 엔터티를 예측 하는 경우에는 태그를 검토 해야 합니다. 모든 파일에서 엔터티의 모든 인스턴스가 제대로 태그를 지정 해야 합니다.

### <a name="examine-data-distribution"></a>데이터 배포 검사

파일의 데이터 배포를 검사 하 여 엔터티가 가장 먼저 표시 되는지 여부를 결정할 수 있습니다. 태그를 엔터티 간에 균등 하 게 배포 하지 않으면 데이터 불균형이 발생 하며 모델의 성능에 대 한 위험이 있습니다. 예를 들어 엔터티 *2* 에 10 개의 태그만 있는 반면 *엔터티 1* 에는 50 태그가 있는 경우이는 *엔터티 1* 이 표시 되 고 *엔터티 2* 가 표시 되는 데이터 불균형의 예입니다. 모델은 *엔터티 1* 을 추출 하는 것을 중심으로 하 고, *엔터티 2* 를 간과 할 수 있습니다. 스키마가 모호할 경우 더 복잡 한 문제가 데이터 불균형에서 발생할 수 있습니다. 두 엔터티가 비슷한 것이 고 *엔터티 2* 가 어떻게 표시 되는 것이 가장 중요 한 것은 모델을 *엔터티 1* 로 추출할 가능성이 가장 큰 것입니다.

[모델 평가](view-model-evaluation.md)에서 위에 있는 엔터티는 다른 엔터티 보다 더 높은 회수를 갖게 되 고, 표시 된 엔터티 아래에는 더 낮은 회수가 있습니다.

데이터 집합의 데이터 배포를 검사 하려면:

1. [언어 스튜디오](https://aka.ms/languageStudio)에서 프로젝트 페이지로 이동 합니다.
    1. 언어 스튜디오에서 **정보 추출** 섹션을 찾습니다.
    2. **명명 된 사용자 지정 엔터티 추출** 을 선택 합니다. 
2. 왼쪽 메뉴에서 **모델 개선** 을 선택 합니다.

3. **데이터 배포 검사** 를 선택 합니다.

## <a name="next-steps"></a>다음 단계

모델이 수행 되는 방식에 만족 했으면 런타임 API를 사용 하 여 [엔터티 추출 요청 보내기를](call-api.md) 시작할 수 있습니다.
