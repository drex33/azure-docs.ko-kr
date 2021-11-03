---
title: 사용자 지정 NER (명명 된 엔터티 인식) FAQ
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명 된 엔터티 인식을 사용할 때 질문과 대답을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: c413e03e9106a3063e9cfdf26a1ab4b7fd7d5f25
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103157"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>사용자 지정 명명 된 엔터티 인식에 대 한 질문과 대답

개념에 대 한 일반적인 질문과 대답 및 언어별 Azure 인식 서비스의 사용자 지정 NER 관련 된 시나리오에 대 한 답변을 찾습니다.

## <a name="how-many-tagged-files-are-needed"></a>태그가 지정 된 파일의 수는 몇 개입니까?

일반적으로 태그 지정이 정확 하 고 정확 하 게 완벽 하 게 수행 되는 경우 다양 하 고 기본적인 태그가 지정 된 [데이터](how-to/tag-data.md) 는 더 나은 결과를 초래 합니다. 모든 모델이 제대로 작동 하도록 하는 태그가 지정 된 엔터티의 집합 수는 없습니다. 성능은 스키마 및 엔터티의 모호성에 따라 좌우 됩니다. 모호한 엔터티 형식에 더 많은 태그가 필요 합니다. 또한 성능은 태그 지정의 품질에 따라 달라 집니다. 엔터티 당 권장 태그가 지정 된 인스턴스 수는 200입니다. 

## <a name="what-are-the-service-limits"></a>서비스의 제한 사항은 무엇입니까?

자세한 내용은 [서비스 제한 문서](service-limits.md) 를 참조 하세요.

## <a name="what-to-do-if-my-model-scores-poorly"></a>모델 점수가 잘못 되 면 어떻게 해야 하나요?

모델 평가는 항상 포괄적이 지 않을 수 있습니다. 특히 특정 클래스가 누락 되거나 테스트 집합에서 표시 되는 경우입니다. 모델에 태그가 지정 된 데이터를 추가 하 여 성능을 향상 시키고 보다 대표적인 테스트 집합을 포함 하는 것이 좋습니다.

## <a name="how-do-i-improve-model-performance"></a>모델 성능을 향상 어떻게 할까요? 있나요?

[혼동 행렬](how-to/view-model-evaluation.md) 에서 스키마 모호성을 확인 합니다. 그런 다음 [테스트 집합을 검토](how-to/improve-model.md) 하 여 예측 및 태그가 지정 된 엔터티를 나란히 표시 하 여 모델 성능을 보다 잘 파악 하 고 스키마 또는 태그의 변경 내용이 필요한 지 결정할 수 있습니다.  

## <a name="i-trained-my-model-but-i-cant-test-it"></a>모델을 학습 했지만 테스트할 수 없습니다.

모델을 테스트 하려면 먼저 [배포](quickstart.md#deploy-your-model) 해야 합니다. 

## <a name="how-do-i-use-the-analyze-api"></a>분석 API를 사용 어떻게 할까요??

모델을 배포한 후에 [는 런타임 API를 호출](how-to/call-api.md)합니다. 자세한 내용은 [API 참조 분석](https://aka.ms/ct-runtime-swagger) 을 참조 하세요.

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

데이터는 Azure storage 계정에만 저장 되며, 사용자 지정 NER 학습 및 평가 중에 읽을 수 있는 액세스 권한만 있습니다. 

<!-- ## How to clone my project?

To clone your project you need to [export]() project assests and then [import]() them into a new project. -->

## <a name="next-steps"></a>다음 단계

[사용자 지정 NER 개요](overview.md)
