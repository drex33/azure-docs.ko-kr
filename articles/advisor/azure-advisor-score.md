---
title: Advisor 점수를 사용하여 Azure 워크로드 최적화
description: Azure Advisor 점수를 사용하여 Azure를 최대한 활용합니다.
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: fc9bcee7d19d9e8ed7b550e7298d615d397c6f8b
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484710"
---
# <a name="optimize-azure-workloads-by-using-advisor-score"></a>Advisor 점수를 사용하여 Azure 워크로드 최적화

## <a name="introduction-to-advisor-score"></a>Azure 점수 소개

Azure Advisor는 워크로드에 대한 모범 사례 권장 사항을 제공합니다. 이러한 권장 사항은 다음과 같이 지원할 수 있도록 사용자에게 맞추어지고 실행 가능합니다.

* 워크로드의 상태를 향상시키고 Azure 배포를 최적화합니다.
* 모범 사례에 따라 주요 문제를 사전에 방지합니다.
* [Microsoft Azure Well-Architected Framework](/azure/architecture/framework/)의 5가지 핵심 요소와 비교하여 Azure 워크로드를 평가합니다.

Advisor의 핵심 기능인 Advisor 점수는 이러한 목표를 효과적이고 효율적으로 달성하는 데 도움이 될 수 있습니다.

Azure를 최대한 활용하려면 워크로드 최적화 여정에서 현재 위치를 이해하는 것이 중요합니다. 효율적으로 사용되거나 그렇지 않은 서비스 또는 리소스를 알아야 합니다. 또한 권장 사항에 따라 작업의 우선 순위를 지정하여 결과를 최대화하는 방법을 알아야 합니다.

이 최적화 여정에서 진행 상황을 추적하고 보고하는 것도 중요합니다. Advisor 점수를 사용하면 새로운 게이미피케이션 환경에서 이러한 모든 작업을 쉽게 수행할 수 있습니다.

맞춤형 클라우드 컨설턴트인 Azure Advisor는 사용량 원격 분석 및 리소스 구성을 지속적으로 평가하여 업계 모범 사례를 확인합니다. 그런 다음, Advisor는 결과를 단일 점수로 집계합니다. 이 점수를 통해 신뢰할 수 있고 안전하며 비용 효율적인 솔루션을 구축하는 데 필요한 단계를 수행하고 있는지 한눈에 파악할 수 있습니다.

Advisor 점수는 전체 점수로 구성되며, 5가지 범주 점수로 자세히 분석할 수 있습니다. Advisor의 각 범주에 대한 하나의 점수는 Well-Architected Framework의 5가지 핵심 요소를 나타냅니다.

일별, 주별 및 월별 추세를 사용하여 전체 점수와 범주 점수를 확인하면 시간이 지남에 따라 진행 상황을 추적할 수 있습니다. 목표를 달성하는 데 도움이 되는 벤치마크를 설정할 수도 있습니다.

 ![Advisor 점수 페이지를 보여 주는 스크린샷](./media/advisor-score-1.png)

## <a name="interpret-an-advisor-score"></a>Advisor 점수 해석

Advisor는 전체 Advisor 점수와 Advisor 범주에 대한 분석을 백분율로 표시합니다. 범주에서 100%의 점수는 Advisor에서 평가한 모든 리소스가 Advisor에서 권장하는 모범 사례를 따른다는 것을 의미합니다. 스펙트럼의 다른 쪽 끝에 있는 0%의 점수는 Advisor에서 평가한 리소스에서 Advisor의 권장 사항을 따르지 않는다는 것을 의미합니다. 이러한 점수 조직을 사용하면 다음과 같은 흐름을 쉽게 달성할 수 있습니다.

* **Advisor 점수** 는 Advisor 점수에 따라 워크로드 또는 구독에서 수행하는 방식에 대한 기준을 설정하는 데 도움이 됩니다. 또한 기록 추세를 확인하여 사용자의 추세를 이해할 수 있습니다.
* 각 권장 사항에 대한 **범주별 점수** 는 사용자의 점수를 최대한 향상시킬 수 있는 뛰어난 권장 사항을 알려줍니다. 이러한 값은 권장 사항에 대한 가중치와 예측된 구현 용이성 모두를 반영합니다. 이러한 요소는 시간을 활용하여 최고의 가치를 얻는 데 도움이 됩니다. 또한 우선 순위를 지정하는 데도 도움이 됩니다.
* 각 권장 사항에 대한 **범주 점수 영향** 은 각 범주에 대한 수정 작업의 우선 순위를 지정하는 데 도움이 됩니다.

범주 점수에 대한 각 권장 사항의 기여도는 Azure Portal의 **Advisor 점수** 페이지에 명확하게 표시됩니다. 각 범주 점수는 **점수 증가 가능성** 열에 나열된 백분율 득점만큼 높일 수 있습니다. 이 값은 범주 내 권장 사항의 가중치와 잠재적으로 가장 쉬운 작업 처리하는 데 예측된 구현 용이성을 모두 반영합니다. 점수에 가장 큰 영향을 주는 권장 사항에 집중하면 시간을 활용하여 최대한 진행하는 데 도움이 됩니다.

![Advisor 점수 영향을 보여 주는 스크린샷](./media/advisor-score-2.png)

Advisor 권장 사항이 개별 리소스와 관련이 없는 경우 해당 권장 사항을 연기하거나 해제할 수 있습니다. 이러한 권장 사항은 다음 새로 고침을 통해 점수 계산에서 제외됩니다. 또한 Advisor는 이 입력을 모델을 향상시키기 위한 추가 피드백으로 사용합니다.

## <a name="how-is-an-advisor-score-calculated"></a>Advisor 점수를 계산하는 방법

Advisor는 범주 점수와 전체 Advisor 점수를 백분율로 표시합니다. 범주에서 100%의 점수는 *Advisor에서 평가한* 모든 리소스가 Advisor에서 권장하는 모범 사례를 따른다는 것을 의미합니다. 스펙트럼의 다른 쪽 끝에 있는 0%의 점수는 Advisor에서 평가한 리소스에서 Advisor의 권장 사항을 따르지 않는다는 것을 의미합니다.

**각 5개 범주에서 최고 가능한 점수는 100%입니다.** 전체 Advisor 점수는 각 해당 범주 점수의 합계를 모든 해당 범주에서 가능한 최고 점수의 합계로 나눈 값으로 계산됩니다. 대부분 구독에서 이는 Advisor가 각 범주의 점수를 더하고 500으로 나누는 것을 의미합니다. 그러나 *각 범주 점수는 Advisor에서 평가한 리소스를 사용하는 경우에만 계산됩니다*.

### <a name="advisor-score-calculation-example"></a>Advisor 점수 계산 예

* **단일 구독 점수:** 이 예는 구독에 대한 모든 Advisor 범주 점수의 단순 평균입니다. Advisor 범주 점수에서 **비용** = 73, **안정성** = 85, **운영 효율성** = 77 및 **성능** = 100인 경우 Advisor 점수는 (73 + 85 + 77 + 100)/(4x100) = 0.84이거나 84%입니다.
* **여러 구독 점수:** 여러 구독이 선택되면 생성된 전체 Advisor 점수는 가중치 집계 범주 점수입니다. 여기서 각 Advisor 범주 점수는 구독에서 사용하는 리소스를 기반으로 하여 집계됩니다. 가중치 집계 범주 점수가 획득되면 Advisor에서 단순 평균 계산을 수행하여 구독에 대한 전체 점수를 제공합니다.

### <a name="scoring-methodology"></a>채점 방법론

Advisor 점수 계산은 다음 4가지 단계로 요약할 수 있습니다.

1. Advisor에서 *영향을 받는 리소스의 소매 비용* 을 계산합니다. 해당 리소스는 Advisor에 하나 이상의 권장 사항이 있는 구독의 리소스입니다.
1. Advisor에서 *평가된 리소스의 소매 비용* 을 계산합니다. 해당 리소스는 권장 사항이 있는지 여부와 관계없이 Advisor에서 모니터링되는 리소스입니다.
1. 각 권장 사항 유형에 대해 Advisor에서 *정상 리소스 비율* 을 계산합니다. 해당 비율은 영향을 받는 리소스의 소매 비용을 평가된 리소스의 소매 비용으로 나눈 값입니다.
1. Advisor는 각 범주의 정상 리소스 비율에 세 가지 추가 가중치를 적용합니다.

   * 영향이 큰 권장 사항은 영향이 적은 권장 사항보다 가중치가 높습니다.
   * 오래된 권장 사항이 있는 리소스에는 더 낮은 점수가 계산됩니다.
   * Advisor에서 연기하거나 해제하는 리소스는 점수 계산에서 완전히 제거됩니다.

Advisor는 이 모델을 Advisor 범주 수준에서 적용하여 각 범주에 대한 Advisor 점수를 제공합니다. **보안** 은 [보안 점수](../defender-for-cloud/secure-score-security-controls.md#introduction-to-secure-score) 모델을 사용합니다. 단순 평균에서 최종 Advisor 점수를 산출합니다.

## <a name="advisor-score-faqs"></a>Advisor 점수 FAQ

### <a name="how-often-is-my-score-refreshed"></a>내 점수를 새로 고치는 빈도는 어떻게 되나요?

점수는 하루에 한 번 이상 새로 고쳐집니다.

### <a name="why-do-some-recommendations-have-the-empty---value-in-the-category-score-impact-column"></a>일부 권장 사항의 [범주 점수 영향] 열에 빈 값("-")이 있는 이유는 무엇인가요?

Advisor는 채점 모델의 최근 변경 내용과 함께 새 권장 사항을 즉시 포함하지 않습니다. 짧은 평가 기간(일반적으로 몇 주)이 끝나면 점수에 포함됩니다.

### <a name="why-is-the-cost-score-impact-greater-for-some-recommendations-even-if-they-have-lower-potential-savings"></a>일부 권장 사항의 잠재적 절감 효과가 더 낮은 경우에도 이러한 권장 사항의 비용 점수 영향이 더 큰 이유는 무엇인가요?

**비용** 점수는 사용률이 낮은 리소스의 잠재적 절감 효과와 이러한 권장 사항에 대해 예측된 구현 용이성을 모두 반영합니다. 예를 들어 잠재적 절감 효과가 더 낮더라도 더 오랫동안 유휴 상태였던 영향을 받는 리소스에는 추가 가중치가 적용됩니다.

### <a name="why-dont-i-have-a-score-for-one-or-more-categories-or-subscriptions"></a>하나 이상의 범주 또는 구독에 대한 점수가 없는 이유는 무엇인가요?

Advisor는 Advisor에서 평가하는 리소스가 있는 범주 및 구독에 대해서만 점수를 생성합니다.

### <a name="what-if-a-recommendation-isnt-relevant"></a>권장 사항과 관련이 없으면 어떻게 되나요?

Advisor에서 권장 사항을 해제하면 해당 권장 사항이 점수 계산에서 제외됩니다. 또한 권장 사항을 해제하면 Advisor에서 권장 사항의 품질을 향상시키는 데 도움이 됩니다.

### <a name="why-did-my-score-change"></a>내 점수가 변경된 이유는 무엇인가요?

Advisor에서 권장하는 모범 사례를 채택하여 영향을 받는 리소스를 수정하면 점수가 변경될 수 있습니다. 사용자 또는 구독에 대한 권한이 있는 사용자가 새 리소스를 수정하거나 만든 경우 점수 변동도 표시될 수 있습니다. 점수는 모든 리소스의 총 비용에 대한 비용 영향을 받는 리소스의 비율을 기준으로 합니다.

### <a name="how-does-advisor-calculate-the-retail-cost-of-resources-on-a-subscription"></a>Advisor에서 구독의 리소스에 대한 소매 비용을 어떻게 계산하나요?

Advisor는 [Azure 가격](https://azure.microsoft.com/pricing/)에 게시된 종량제 요금을 사용합니다. 이러한 요금에는 적용 가능한 할인이 반영되지 않습니다. 따라서 요금과 리소스가 할당된 마지막 날의 사용량을 곱합니다. 리소스 비용 계산에서 할인을 생략하면 할인이 다를 수 있는 구독, 테넌트 및 등록 간에 Advisor 점수를 비교할 수 있습니다.

### <a name="do-i-need-to-view-the-recommendations-in-advisor-to-get-points-for-my-score"></a>내 점수에 대한 득점을 얻으려면 Advisor에서 권장 사항을 확인해야 하나요?

아니요. 이러한 모범 사례를 사전에 채택하고 Advisor에서 권장 사항을 확인하지 않은 경우에도 점수는 Advisor에서 권장하는 모범 사례를 채택하는지 여부를 반영합니다.

### <a name="does-the-scoring-methodology-differentiate-between-production-and-dev-test-workloads"></a>채점 방법론에서 프로덕션 워크로드와 개발-테스트 워크로드를 구분하나요?

아니요, 지금은 그렇지 않습니다. 그러나 이러한 리소스가 개발 및 테스트에 사용되고 권장 사항이 적용되지 않는 경우 개별 리소스에 대한 권장 사항을 해제할 수 있습니다.

### <a name="can-i-compare-scores-between-a-subscription-with-100-resources-and-a-subscription-with-100000-resources"></a>리소스가 100개인 구독과 리소스가 100,000개인 구독 간의 점수를 비교할 수 있나요?

채점 방법론은 구독 및 서비스 조합의 리소스 수를 제어하도록 설계되었습니다. 리소스가 적은 구독은 리소스가 많은 구독보다 점수가 더 높거나 낮을 수 있습니다.

### <a name="what-does-it-mean-when-i-see-coming-soon-in-the-score-impact-column"></a>점수 영향 열에 "출시 예정"이 표시되면 무엇을 의미하나요?

이 메시지는 새로운 권장 사항이라는 것이며, 이를 Advisor 점수 모델로 가져오기 위해 노력하고 있음을 의미합니다. 이 새로운 권장 사항이 점수 계산에서 고려되면 권장 사항에 대한 점수 영향 값이 표시됩니다.

### <a name="does-my-score-depend-on-how-much-i-spend-on-azure"></a>내 점수가 Azure에서 지출한 금액에 따라 달라지나요?

아니요. 점수가 반드시 지출한 금액을 반영하는 것은 아닙니다. 불필요한 지출은 **비용** 점수를 낮춥니다.

## <a name="access-advisor-score"></a>Advisor 점수 액세스

Advisor 점수는 Azure Portal에서 공개 미리 보기에 있습니다. **Advisor 점수** 는 왼쪽 창의 **Advisor** 섹션 아래에서 확인합니다.

![Advisor 점수 진입점을 보여 주는 스크린샷](./media/advisor-score-3.png)

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.

* [Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-cost-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)
* [Advisor 뛰어난 운영 권장 사항](advisor-operational-excellence-recommendations.md)
