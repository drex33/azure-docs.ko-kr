---
title: NER(사용자 지정 명명된 엔터티 인식) FAQ
titleSuffix: Azure Cognitive Services
description: 사용자 지정 명명된 엔터티 인식 사용할 때 자주 묻는 질문에 대해 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: a76320cb07f2829d314e13c50c0a7491b676dc2d
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132757099"
---
# <a name="frequently-asked-questions-for-custom-named-entity-recognition"></a>사용자 지정 명명된 엔터티 인식 대한 질문과 대답

개념에 대한 일반적인 질문과 대답 및 언어용 Azure Cognitive Service의 사용자 지정 NER와 관련된 시나리오를 찾습니다.

## <a name="how-do-i-get-started-with-the-service"></a>서비스를 시작할 어떻게 할까요? 있나요?

[빠른](./quickstart.md) 시작에서 첫 번째 프로젝트를 빠르게 만들거나 [프로젝트를 만드는 방법을 확인하여](how-to/create-project.md) 자세한 내용을 확인하세요.

## <a name="what-are-the-service-limits"></a>서비스의 제한 사항은 무엇입니까?

자세한 내용은 [서비스 제한 문서를](service-limits.md) 참조하세요.

## <a name="how-many-tagged-files-are-needed"></a>태그가 지정된 파일의 개수는 얼마인가요?

일반적으로 [태그 지정이](how-to/tag-data.md) 정확하고 일관적이며 완전히 수행된다는 점을 고려할 때 다양하고 대표적인 태그가 지정된 데이터는 더 나은 결과를 초래합니다. 모든 모델이 잘 수행되도록 하는 태그가 지정된 인스턴스 수는 설정되지 않습니다. 성능은 스키마 및 스키마의 모호성에 따라 크게 달라집니다. 모호한 엔터티 형식에는 더 많은 태그가 필요합니다. 성능은 태그 지정의 품질에 따라 달라집니다. 엔터티당 권장 태그가 지정된 인스턴스 수는 50개입니다.

## <a name="training-is-taking-a-long-time-is-this-expected"></a>학습 시간이 오래 걸리는데, 예상되는 건가요?

학습 프로세스는 시간이 오래 걸릴 수 있습니다. 대략적인 예측으로, 총 길이가 12,800,000자인 파일에 대한 예상 학습 시간은 6시간입니다.

## <a name="how-do-i-build-my-custom-model-programmatically"></a>프로그래밍 방식으로 사용자 지정 모델을 빌드할 어떻게 할까요? 있나요?

[!INCLUDE [SDK limitations](includes/sdk-limitations.md)]

[REST API를](https://aka.ms/ct-authoring-swagger) 사용하여 사용자 지정 모델을 빌드할 수 있습니다. 이 [빠른 시작에](quickstart.md?pivots=rest-api) 따라 Authoring API를 호출하는 방법에 대한 예제를 위해 API를 통해 프로젝트 만들기 및 모델 만들기를 시작합니다.

[모델을 사용하여 예측을](#how-do-i-use-my-trained-model-for-predictions)시작할 준비가 되면 REST API 또는 클라이언트 라이브러리를 사용할 수 있습니다.

## <a name="what-is-the-recommended-cicd-process"></a>권장되는 CI/CD 프로세스는 무엇인가요?

동일한 프로젝트 내의 동일한 데이터 세트에서 여러 모델을 학습할 수 있습니다. 모델을 성공적으로 학습시킨 후에는 [해당 평가를 볼](how-to/view-model-evaluation.md)수 있습니다. [Language Studio](https://aka.ms/languageStudio)내에서 모델을 [배포하고 테스트할](quickstart.md#deploy-your-model) 수 있습니다. 데이터에서 태그를 추가하거나 제거하고 **새** 모델을 학습시키고 테스트할 수도 있습니다. [서비스 제한을](service-limits.md)확인하여 동일한 프로젝트를 사용하여 학습된 모델의 최대 수에 대해 알아봅니다. 새 모델을 학습할 때 데이터 세트는 학습 및 테스트 집합으로 임의로 [분할되므로](how-to/train-model.md#data-split) 반영된 모델 평가가 동일한 테스트 집합에 대한 것이 아니고 결과를 비교할 수 없다는 보장이 없습니다. 사용자 고유의 테스트 집합을 개발하고 이를 사용하여 두 모델을 모두 평가하여 향상을 측정할 수 있도록 하는 것이 좋습니다.

## <a name="does-a-low-or-high-model-score-guarantee-bad-or-good-performance-in-production"></a>모델 점수가 낮거나 높음이 프로덕션에서 불량 또는 양호한 성능을 보장하나요?

모델 평가가 항상 포괄적인 것은 아닙니다. 이는 다음을 기반으로 합니다.
* 테스트 **세트가** 너무 작으면 양호/불량 점수가 모델의 실제 성능을 대표하지 않습니다. 또한 특정 엔터티 형식이 누락되거나 테스트 집합에 표시되지 않으면 모델 성능에 영향을 줍니다.
* 데이터가 프로덕션에서 예상하는 텍스트의 몇 가지 시나리오/예제만 포함하는 경우 **데이터 다양성은** 모델이 가능한 모든 시나리오에 노출되지 않으며 학습되지 않은 시나리오에서 제대로 수행되지 않을 수 있습니다.
* 모델을 학습시키는 데 사용되는 데이터 세트가 프로덕션에서 모델에 도입되는 데이터를 대표하지 않는 **경우** 모델 성능에 큰 영향을 하게 됩니다.

자세한 내용은 [데이터 선택 및 스키마 디자인](how-to/design-schema.md) 문서를 참조하세요.

## <a name="how-do-i-improve-model-performance"></a>모델 성능을 개선할 어떻게 할까요? 있나요?

* 모델 [혼동 행렬을](how-to/view-model-evaluation.md)봅니다. 특정 엔터티 형식이 올바르게 예측되지 않는 경우가 많으면 이 클래스에 태그가 지정된 인스턴스를 더 추가하는 것이 좋습니다. 두 엔터티 형식이 서로 예측되는 경우가 많으면 스키마가 모호하며 성능을 향상하기 위해 두 엔터티 형식을 하나의 엔터티 형식으로 병합하는 것이 좋습니다.

* [데이터 분포를 검사합니다.](how-to/improve-model.md#examine-data-distribution) 엔터티 형식 중 하나에 태그가 지정된 인스턴스가 다른 인스턴스보다 훨씬 더 많은 경우 모델은 이 형식에 편향될 수 있습니다. 다른 엔터티 형식에 더 많은 데이터를 추가하거나 주 형식에서 예제를 제거합니다.

* [데이터 선택 및 스키마 디자인에](how-to/design-schema.md)대해 자세히 알아봅니다.

* [테스트 집합을 검토하여](how-to/improve-model.md) 예측 및 태그가 지정된 엔터티를 나란히 확인하여 모델 성능을 더 잘 파악하고 스키마 또는 태그의 변경이 필요한지 결정할 수 있습니다.

## <a name="why-do-i-get-different-results-when-i-retrain-my-model"></a>모델을 다시 학습할 때 다른 결과를 얻는 이유는 무엇인가요?

* 새 모델을 학습할 때 데이터 세트는 학습 및 테스트 집합으로 임의로 [분할되므로](how-to/train-model.md#data-split) 반영된 모델 평가가 동일한 테스트 집합에 있다는 보장이 없으므로 결과를 비교할 수 없습니다.

* 동일한 모델을 다시 학습하는 경우 테스트 집합은 동일하지만 모델에서 예측이 약간 변경될 수 있습니다. 학습된 모델이 충분히 강력하지 않기 때문입니다. 이는 데이터의 대표성과 고유성 및 태그가 지정된 데이터의 품질에 영향을 줍니다.

## <a name="how-do-i-get-predictions-in-different-languages"></a>다른 언어로 예측을 얻으려면 어떻게 해야 하나요?

먼저 [프로젝트를 만들](how-to/create-project.md) 때 다국어 옵션을 사용하도록 설정하거나 나중에 프로젝트 설정 페이지에서 사용하도록 설정할 수 있습니다. 모델을 학습시키고 배포한 후 [여러 언어로](language-support.md#multiple-language-support)쿼리를 시작할 수 있습니다. 다른 언어에 대해 다양한 결과를 얻을 수 있습니다. 언어의 정확도를 높이려면 해당 언어로 태그가 지정된 인스턴스를 프로젝트에 추가하여 해당 언어의 더 많은 구문에 학습된 모델을 도입합니다.

## <a name="i-trained-my-model-but-i-cant-test-it"></a>모델을 학습했지만 테스트할 수 없습니다.

모델을 테스트하려면 [모델을 배포해야](quickstart.md#deploy-your-model) 합니다. 

## <a name="how-do-i-use-my-trained-model-for-predictions"></a>내 학습된 모델을 예측에 사용할 어떻게 할까요? 있나요?

모델을 배포한 후 [REST API](how-to/call-api.md?tabs=rest-api) 또는 클라이언트 라이브러리 를 사용하여 [예측 API](how-to/call-api.md)를 [호출합니다.](how-to/call-api.md?tabs=client)

## <a name="data-privacy-and-security"></a>데이터 개인 정보 보호 및 보안

사용자 지정 NER는 GDPR(일반 데이터 보호 규정)을 위한 데이터 프로세서입니다. GDPR 정책에 따라 사용자 지정 NER 사용자는 [Language Studio를](https://aka.ms/languageStudio) 통해 또는 REST API를 사용하여 프로그래밍 방식으로 사용자 콘텐츠를 보거나, 내보내거나, 삭제할 수 있는 모든 권한을 갖습니다. [](https://aka.ms/ct-authoring-swagger)

데이터는 Azure Storage 계정에만 저장됩니다. 사용자 지정 NER는 학습 중에 읽을 수 있는 액세스 권한만 있습니다.

## <a name="how-to-clone-my-project"></a>내 프로젝트를 복제하는 방법

프로젝트를 복제하려면 내보내기 API를 사용하여 프로젝트 자산을 내보낸 다음 새 프로젝트로 가져와야 합니다. 두 작업에 대한 [REST API](https://aka.ms/ct-authoring-swagger) 참조를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [사용자 지정 NER 개요](overview.md)
* [빠른 시작](quickstart.md)
