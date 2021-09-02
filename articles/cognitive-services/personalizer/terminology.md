---
title: 용어 - Personalizer
description: Personalizer는 보충 학습의 용어를 사용합니다. 이러한 용어는 Azure Portal 및 API에서 사용됩니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 04/23/2020
ms.openlocfilehash: bdd26d167a94826be4330f878df01cde43e853cb
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829922"
---
# <a name="personalizer-terminology"></a>Personalizer 용어

Personalizer는 보충 학습의 용어를 사용합니다. 이러한 용어는 Azure Portal 및 API에서 사용됩니다.

## <a name="conceptual-terminology"></a>개념 용어

* **학습 루프**: 개인 설정의 이점을 활용할 수 있는 애플리케이션의 모든 부분에 대해 _학습 루프_ 라는 Personalizer 리소스를 만듭니다. 개인에 맞게 설정하려는 환경이 둘 이상인 경우 각각에 대한 반복을 만듭니다.

* **모델**: Personalizer 모델은 사용자 동작에 대해 학습된 모든 데이터를 캡처하고, Rank 및 Reward 호출에 보내는 인수와 학습 정책에 따라 결정된 학습 동작을 조합하여 학습 데이터를 가져옵니다.

* **온라인 모드**: 학습 루프가 있는 Personalizer의 기본 [학습 동작](#learning-behavior)으로, 기계 학습을 사용하여 콘텐츠에 대한 **최상위 작업** 을 예측하는 모델을 빌드합니다.

* **실습생 모드**: 애플리케이션 결과와 작업에 영향을 주지 않으면서 Personalizer 모델이 웜 부팅으로 학습하도록 도와주는 [학습 동작](#learning-behavior)입니다.

## <a name="learning-behavior"></a>학습 동작:

* **온라인 모드**: 최상의 작업을 반환합니다. 모델은 최상의 작업으로 순위 호출에 응답하고 보상 호출을 사용하여 시간이 지남에 따라 선택 항목을 학습하고 개선합니다.
* **[실습생 모드](concept-apprentice-mode.md)** : 실습생으로 학습합니다. 사용자 모델은 기존 시스템의 동작을 관찰하여 학습합니다. 순위 호출은 항상 애플리케이션의 **기본 작업**(기준)을 반환합니다.

## <a name="personalizer-configuration"></a>Personalizer 구성

Personalizer는 [Azure Portal](https://portal.azure.com)에서 구성됩니다.

* **보상**: 보상 대기 시간, 기본 보상 및 보상 집계 정책에 대한 기본값을 구성합니다.

* **탐색**: 탐색에 사용할 순위 호출의 백분율을 구성합니다.

* **모델 업데이트 빈도**: 모델을 다시 학습하는 빈도입니다.

* **데이터 보존**: 데이터를 저장할 기간(일)입니다. 이는 학습 루프를 개선하는 데 사용되는 오프라인 평가에 영향을 미칠 수 있습니다.

## <a name="use-rank-and-reward-apis"></a>Rank 및 Reward API 사용

* **순위**: 기능 및 컨텍스트 기능을 사용한 작업에 대해 탐색 또는 익스플로잇을 사용하여 최상위 작업(콘텐츠 항목)을 반환합니다.

    * **작업**: 작업은 제품 또는 프로모션과 같이 선택할 수 있는 콘텐츠 항목입니다. Personalizer는 순위 API를 통해 사용자에게 표시할 상위 작업(반환된 보상 작업 ID)을 선택합니다.

    * **컨텍스트**: 더 정확한 순위를 제공하려면 컨텍스트에 대한 정보를 제공합니다. 예를 들어 다음과 같습니다.
        * 사용자
        * 사용하는 디바이스
        * 현재 시간입니다.
        * 현재 상황에 대한 다른 데이터
        * 사용자 또는 컨텍스트에 대한 기록 데이터

        특정 애플리케이션에 다른 컨텍스트 정보가 있을 수 있습니다.

    * **[기능](concepts-features.md)** : 콘텐츠 항목 또는 사용자 컨텍스트에 대한 정보 단위입니다. 집계된 기능만 사용해야 합니다. 특정 시간, 사용자 ID 또는 기타 집계할 수 없는 데이터를 기능으로 사용해서는 안 됩니다.

        * _작업 기능_ 은 콘텐츠에 대한 메타데이터입니다.
        * _컨텍스트 기능_ 은 내용이 표시되는 컨텍스트에 대한 메타데이터입니다.

* **탐색**: Personalizer 서비스는 최상의 작업을 반환하는 대신 사용자를 위해 다른 작업을 선택하는 시점을 검색합니다. Personalizer 서비스는 드리프트와 정체를 방지하고 검색을 통해 진행 중인 사용자 동작에 적응할 수 있습니다.

* **익스플로잇**: Personalizer 서비스는 현재 모델을 사용하여 과거 데이터를 기반으로 최적의 작업을 결정합니다.

* **실험 기간**: Personalizer 서비스에서 해당 이벤트에 대한 Rank 호출이 발생한 순간부터 보상을 기다리는 시간의 양입니다.

* **비활성 이벤트**: 비활성 이벤트는 Rank를 호출한 이벤트이지만, 클라이언트 애플리케이션 결정으로 인해 사용자가 결과를 볼 수 있는지가 확실하지 않습니다. 비활성 이벤트를 사용하면 맞춤 설정 결과를 만들고 저장한 다음, 나중에 기계 학습 모델에 영향을 주지 않고 삭제하도록 결정할 수 있습니다.


* **보상**: 순위 API에서 반환된 보상 작업 ID에 대해 사용자가 응답한 방식을 0~1 사이의 점수로 나타낸 측정값입니다. 0~1 값은 선택이 맞춤 설정의 비즈니스 목표를 달성하는 데 도움이 되는 정도에 기반한 비즈니스 논리에 따라 설정됩니다. 학습 루프는 이 보상을 개별 사용자 기록으로 저장하지 않습니다.

## <a name="evaluations"></a>평가

### <a name="offline-evaluations"></a>오프라인 평가

* **평가**: 오프라인 평가는 애플리케이션 데이터를 기반으로 루프에 대한 최상의 학습 정책을 결정합니다.

* **학습 정책**: Personalizer가 모든 이벤트에 대해 모델을 교육하는 방법은 기계 학습 알고리즘의 작동 방식에 영향을 미치는 일부 매개 변수에 의해 결정됩니다. 새 학습 루프는 기본 **학습 정책** 부터 시작하며 보통 수준의 성능을 제공할 수 있습니다. [평가](concepts-offline-evaluation.md)를 실행할 때 Personalizer는 루프의 사용 사례에 맞게 특별히 최적화된 새 학습 정책을 만듭니다. Personalizer는 평가 중에 생성된 각 특정 루프에 최적화된 정책을 통해 훨씬 더 나은 성능을 발휘합니다. 학습 정책의 이름은 Azure Portal의 Personalizer 리소스에 대한 **모델 및 학습 설정** 에서 _학습 설정_ 으로 지정됩니다.

### <a name="apprentice-mode-evaluations"></a>실습생 모드 평가

실습생 모드는 다음과 같은 **평가 메트릭** 을 제공합니다.
* **기준 – 평균 보상**: 애플리케이션 기본값(기준)의 평균 보상입니다.
* **Personalizer – 평균 보상**: Personalizer가 잠재적으로 도달할 수 있는 총 보상의 평균입니다.
* **평균 롤링 보상**: 기준 및 Personalizer 보상의 비율 – 최근 1000개 이벤트에 대해 표준화됩니다.

## <a name="next-steps"></a>다음 단계

* [윤리 및 책임 있는 사용](ethics-responsible-use.md)에 대해 자세히 알아보기