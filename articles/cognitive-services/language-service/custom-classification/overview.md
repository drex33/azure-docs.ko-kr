---
title: Azure Cognitive Services for Language의 사용자 지정 분류(미리 보기)란 무엇인가요?
titleSuffix: Azure Cognitive Services
description: 사용자 지정 텍스트 분류를 사용하는 방법을 알아봅니다.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: c31a348376f41bf61a4632d991fc02f08b360be7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450251"
---
# <a name="what-is-custom-text-classification-preview"></a>사용자 지정 텍스트 분류(미리 보기)란?

사용자 지정 텍스트 분류는 [Azure Cognitive Service for Language](../overview.md)에서 제공하는 기능 중 하나입니다. 텍스트 분류 작업에 대한 사용자 지정 모델을 빌드할 수 있도록 기계 학습 인텔리전스를 적용하는 클라우드 기반 API 서비스입니다. 

사용자 지정 텍스트 분류는 Azure Cognitive for Language 내에서 사용자 지정 기능의 일부로 제공됩니다. 이 기능을 통해 사용자는 사용자 지정 AI 모델을 빌드하여 사용자가 미리 정의한 사용자 지정 범주로 텍스트를 분류할 수 있습니다. 개발자는 사용자 지정 분류 프로젝트를 만들어 데이터에 반복적으로 태그를 지정하고, 학습하고, 평가하고, 모델 성능을 개선한 후 사용할 수 있습니다. 태그가 지정된 데이터의 품질은 모델 성능에 큰 영향을 미칩니다. 모델 빌드 및 사용자 지정을 간소화하기 위해 서비스는 [언어 스튜디오](https://aka.ms/languageStudio)를 통해 액세스할 수 있는 사용자 지정 웹 포털을 제공합니다. 이 [빠른 시작](quickstart.md)의 단계를 수행하여 서비스를 쉽게 시작할 수 있습니다. 

사용자 지정 텍스트 분류는 두 가지 유형의 프로젝트를 지원합니다. 

* **단일 레이블 분류** - 데이터 세트의 각 파일에 대해 단일 클래스를 할당할 수 있습니다. 예를 들어 영화 스크립트는 "Action" 또는 "Thriller"로만 분류할 수 있습니다. 
* **다중 레이블 분류** - 데이터 세트의 각 파일에 대해 다중 클래스를 할당할 수 있습니다. 예를 들어 영화 스크립트는 "Action" 또는 "Action 및 Thriller"로 분류할 수 있습니다.

이 설명서에는 다음과 같은 문서 유형이 포함되어 있습니다.

* [빠른 시작](quickstart.md)은 서비스에 대한 요청을 수행하는 과정을 안내하는 시작 지침입니다.
* [개념](concepts/evaluation.md)은 서비스 기능에 대한 설명을 제공합니다.
* [방법 가이드](how-to/tag-data.md)에는 보다 구체적이거나 사용자 지정된 방식으로 서비스를 사용하기 위한 지침이 포함되어 있습니다.

## <a name="example-usage-scenarios"></a>사용 시나리오 예제

### <a name="automatic-emailsticket-triage"></a>자동 이메일/티켓 심사

모든 유형의 지원 센터는 구조화되지 않은 자유 형식 텍스트 및 첨부 파일을 포함하는 수천~수십만 개의 이메일/티켓을 받습니다. 내부 팀 내의 주제 전문가에게 시기적절한 검토, 승인 및 라우팅하는 것이 중요합니다. 그러나 적절한 부서를 검토하고 라우팅하는 사람들이 참여하는 이 규모의 이메일 심사에는 시간과 귀중한 리소스가 필요합니다. 사용자 지정 분류를 통해 들어오는 텍스트 심사를 분석하고 관련 부서로 자동으로 라우팅될 콘텐츠를 분류하여 필요한 조치를 취할 수 있습니다.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>의미 체계 검색을 향상/보강하기 위한 지식 마이닝

검색은 카탈로그 또는 문서 검색, 소매 제품 또는 데이터 과학을 위한 기술 자료 등을 비롯한 일반적인 시나리오를 통해 사용자에게 텍스트 콘텐츠를 표시하는 모든 앱에 기반이 됩니다.다양한 업계의 많은 기업은 구조화된 문서와 비구조적 문서를 모두 포함하는 다른 유형의 프라이빗 콘텐츠에 대한 풍부한 검색 환경을 구축하고 있습니다. 개발자는 파이프라인의 일부로 사용자 지정 분류를 사용하여 텍스트를 해당 업계와 관련된 클래스로 분류할 수 있습니다. 예측된 클래스를 사용하여 보다 사용자 지정된 검색 환경을 위해 파일의 인덱싱을 보강할 수 있습니다. 

## <a name="application-development-lifecycle"></a>애플리케이션 개발 수명 주기

사용자 지정 분류 애플리케이션을 만드는 데는 일반적으로 여러 단계가 포함됩니다. 

:::image type="content" source="media/development-lifecycle.png" alt-text="개발 수명 주기" lightbox="media/development-lifecycle.png":::

모델을 최대한 활용하려면 다음 단계를 수행합니다.

1. **스키마 정의**: 데이터를 파악하고 구분하려는 클래스를 식별하여 모호성을 방지합니다.

2. **데이터 태그 지정**: 데이터 태그 지정의 품질은 모델 성능을 결정하는 데 중요한 요소입니다. 학습에 포함하려는 모든 파일에 태그를 지정합니다. 두 클래스에 속할 수 있는 파일이 **여러 클래스 분류** 프로젝트를 사용하는 경우 동일한 클래스에 속하는 파일은 항상 동일한 클래스를 가져야 합니다. 클래스 모호성을 방지하고, 특히 단일 클래스 분류 프로젝트에서 클래스를 서로 명확하게 분리할 수 있는지 확인합니다.

3. **모델 학습**: 모델은 태그가 지정된 데이터에서 학습을 시작합니다.

4. **모델 평가 세부 정보 보기**: 모델에 대한 평가 세부 정보를 확인하여 새 데이터에 도입 시 모델의 성능을 확인합니다.

5. **모델 개선**: 잘못된 모델 예측을 검사하고 데이터 분포를 검사하여 모델 성능을 개선합니다.

6. **모델 배포**: 모델을 배포하면 [분석 API](https://aka.ms/ct-runtime-swagger)를 통해 사용할 수 있습니다.

7. **텍스트 분류**: 텍스트 분류 작업에 사용자 지정 모델링을 사용합니다.

## <a name="next-steps"></a>다음 단계

* [빠른 시작 문서](quickstart.md)를 사용하여 사용자 지정 텍스트 분류 사용을 시작합니다.  

* 애플리케이션 개발 수명 주기를 진행하면서 [용어집](glossary.md)을 검토하여 이 기능에 대한 설명서 전체에서 사용되는 용어에 대해 자세히 알아보세요. 

* [지역별 가용성](service-limits.md#regional-availability)과 같은 정보에 대한 [서비스 제한](service-limits.md)을 확인해야 합니다.
