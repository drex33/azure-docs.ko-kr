---
title: Azure Cognitive Service for Language(미리 보기)의 사용자 지정 NER(명명된 엔터티 인식)이란?
titleSuffix: Azure Cognitive Services
description: 사용자 지정 NER(명명된 엔터티 인식)을 사용하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: bac6068c02ea4f253176a65061d11604104c2bd5
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131439047"
---
# <a name="what-is-custom-named-entity-recognition-ner-preview"></a>사용자 지정 NER(명명된 엔터티 인식)(미리 보기)이란?

사용자 지정 NER은 [Azure Cognitive Service for Language](../overview.md)에서 제공하는 기능 중 하나입니다. 텍스트 사용자 지정 NER 작업에 대한 사용자 지정 모델을 빌드할 수 있도록 기계 학습 인텔리전스를 적용하는 클라우드 기반 API 서비스입니다.

사용자 지정 NER은 [Azure Cognitive Service for Language](../overview.md) 내에서 사용자 지정 기능의 일부로 제공됩니다. 이 기능을 통해 사용자는 사용자 지정 AI 모델을 빌드하여 계약 또는 재무 문서와 같은 비정형 텍스트에서 도메인별 엔터티를 추출할 수 있습니다. 개발자는 사용자 지정 NER 프로젝트를 만들어 데이터에 반복적으로 태그를 지정하고, 학습하고, 평가하고, 모델 성능을 개선한 후 사용할 수 있습니다. 태그가 지정된 데이터의 품질은 모델 성능에 큰 영향을 미칩니다. 모델 빌드 및 사용자 지정을 간소화하기 위해 서비스는 [언어 스튜디오](https://aka.ms/languageStudio)를 통해 액세스할 수 있는 사용자 지정 웹 포털을 제공합니다. 이 [빠른 시작](quickstart.md)의 단계를 수행하여 서비스를 쉽게 시작할 수 있습니다. 
 
이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [빠른 시작](quickstart.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [개념](concepts/evaluation-metrics.md)은 서비스 기능에 대한 설명을 제공합니다.
* [방법 가이드](how-to/tag-data.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.

## <a name="example-usage-scenarios"></a>사용 시나리오 예제

### <a name="information-extraction"></a>정보 추출

많은 금융 및 법률 조직에서는 매일 은행 명세서, 법적 계약 또는 은행 양식과 같은 수천 개의 복잡한 비정형 텍스트에서 데이터를 추출하고 정규화합니다. 이러한 양식을 수동으로 처리하는 대신 사용자 지정 NER을 통해 이 프로세스를 자동화하고 비용, 시간 및 노력을 절감할 수 있습니다.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>의미 체계 검색을 향상/보강하기 위한 지식 마이닝

검색은 카탈로그 또는 문서 검색, 소매 제품 또는 데이터 과학을 위한 기술 자료 등을 비롯한 일반적인 시나리오를 통해 사용자에게 텍스트 콘텐츠를 표시하는 모든 앱에 기반이 됩니다.다양한 업계의 많은 기업은 구조화된 문서와 비구조적 문서를 모두 포함하는 다른 유형의 프라이빗 콘텐츠에 대한 풍부한 검색 환경을 구축하고 있습니다. 개발자는 파이프라인의 일부로 사용자 지정 NER을 사용하여 해당 산업과 관련된 텍스트에서 엔터티를 추출할 수 있습니다. 이러한 엔터티를 사용하여 보다 사용자 지정된 검색 환경을 위해 파일의 인덱싱을 보강할 수 있습니다. 

### <a name="audit-and-compliance"></a>감사 및 규정 준수

정책을 감사하고 적용하기 위해 상당히 긴 텍스트 파일을 수동으로 검토하는 대신 금융 또는 법률 기업의 IT 부서에서 사용자 지정 NER을 사용하여 자동화된 솔루션을 빌드할 수 있습니다. 이러한 솔루션은 규정 준수 정책을 적용하고 구조화 및 비구조적 콘텐츠를 처리하는 지식 마이닝 파이프라인을 기반으로 필요한 비즈니스 규칙을 설정하는 데 도움이 됩니다.

## <a name="application-development-lifecycle"></a>애플리케이션 개발 수명 주기

사용자 지정 NER을 사용하는 경우 일반적으로 여러 단계가 포함됩니다. 

:::image type="content" source="../custom-classification/media/development-lifecycle.png" alt-text="개발 수명 주기" lightbox="../custom-classification/media/development-lifecycle.png":::

1. **스키마 정의**: 데이터를 파악하고 추출하려는 엔터티를 식별합니다. 모호성을 방지합니다.

2. **데이터 태그 지정**: 데이터 태그 지정은 모델 성능을 결정하는 데 중요한 요소입니다. 정확하고, 일관되고, 완전하게 태그를 지정합니다.
    1. **정확한 태그 지정**: 각 엔터티를 항상 올바른 형식에 태그를 지정합니다. 추출하려는 항목만 포함하고 태그에 불필요한 데이터를 포함하지 마세요.
    2. **일관된 태그 지정**: 동일한 엔터티는 모든 파일에서 동일한 태그를 가져야 합니다.
    3. **완전한 태그 지정**: 모든 파일에 있는 엔터티의 모든 인스턴스에 태그를 지정합니다.

3. **모델 학습**: 모델은 태그가 지정된 데이터에서 학습을 시작합니다.

4. **모델 평가 세부 정보 보기**: 학습을 완료한 후에는 모델의 평가 세부 정보와 성능을 볼 수 있습니다.

5. **모델 개선**: 모델 평가 세부 정보를 검토한 후 모델을 개선할 수 있는 방법을 알아볼 수 있습니다.

6. **모델 배포**: 모델을 배포하는 것은 사용할 수 있도록 하기 위한 것입니다.

7. **엔터티 추출**: 엔터티 추출 작업에 사용자 지정 모델을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작 문서](quickstart.md)를 사용하여 사용자 지정 텍스트 분류 사용을 시작합니다.  

* 애플리케이션 개발 수명 주기를 진행하면서 [용어집](glossary.md)을 검토하여 이 기능에 대한 설명서 전체에서 사용되는 용어에 대해 자세히 알아보세요. 

* [지역별 가용성](service-limits.md#regional-availability)과 같은 정보에 대한 [서비스 제한](service-limits.md)을 확인해야 합니다.
