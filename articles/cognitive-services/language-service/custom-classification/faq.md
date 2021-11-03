---
title: 사용자 지정 텍스트 분류 FAQ
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류 API를 사용할 때 자주 묻는 질문에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: a6a4473e6d7dfa55fc75934f3fbdfd44c89ab1c4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131053667"
---
# <a name="frequently-asked-questions"></a>질문과 대답

개념에 대한 일반적인 질문과 대답 및 언어용 Azure Cognitive Service의 사용자 지정 텍스트 분류와 관련된 시나리오를 찾습니다.

## <a name="how-many-tagged-files-are-needed"></a>태그가 지정된 파일의 개수는 얼마인가요?

일반적으로 [태그 지정이](how-to/tag-data.md) 정확하고 일관적이며 완전히 수행된다는 점을 고려할 때 다양하고 대표적인 태그가 지정된 데이터는 더 나은 결과를 초래합니다. 모든 모델이 잘 수행되도록 하는 태그가 지정된 클래스의 수는 설정되지 않습니다. 성능은 스키마 및 스키마의 모호성에 따라 크게 달라집니다. 모호한 클래스에 더 많은 태그가 필요합니다. 또한 성능은 태그 지정의 품질에 따라 달라집니다. 엔터티당 권장 태그가 지정된 인스턴스 수는 50개입니다. 

## <a name="what-are-the-service-limits"></a>서비스의 제한 사항은 무엇입니까?

자세한 내용은 [서비스 제한 문서를](service-limits.md) 참조하세요.

## <a name="what-to-do-if-my-model-scores-poorly"></a>모델 점수가 잘못되면 어떻게 해야 합니까?

모델 평가가 항상 포괄적인 것은 아닙니다. 특히 특정 클래스가 누락되거나 테스트 집합에 부족한 경우입니다. 태그가 지정된 데이터를 모델에 추가하여 성능을 향상시키고 보다 대표적인 테스트 집합을 갖추는 것이 좋습니다.

## <a name="how-do-i-improve-model-performance"></a>모델 성능을 개선할 어떻게 할까요? 있나요?

혼동 [행렬을](how-to/view-model-evaluation.md) 보고 스키마 모호성을 식별합니다. 그런 다음 [테스트 집합을 검토하여](how-to/improve-model.md) 예측 및 태그가 지정된 클래스를 나란히 확인하여 모델 성능을 더 잘 파악하고 스키마 또는 태그의 변경이 필요한지 결정할 수 있습니다.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>모델을 학습했지만 테스트할 수 없습니다.

모델을 테스트하려면 [모델을 배포해야](quickstart.md#deploy-your-model) 합니다. 

## <a name="how-do-i-use-the-analyze-api"></a>분석 API를 사용할 어떻게 할까요? 있나요?

모델을 배포한 후 [런타임 API 를 호출합니다.](how-to/call-api.md) 자세한 내용은 [분석 API 참조를 참조하세요.](https://aka.ms/ct-runtime-swagger)

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

데이터는 Azure Storage 계정에만 저장되며, 사용자 지정 분류는 학습 및 평가 중에 읽기에만 액세스할 수 있습니다. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>다음 단계

* [사용자 지정 텍스트 분류 개요](overview.md)
* [빠른 시작](quickstart.md)
