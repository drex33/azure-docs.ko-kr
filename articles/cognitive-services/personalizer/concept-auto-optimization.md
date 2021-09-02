---
title: 자동 최적화 - Personalizer
description: 이 문서에서는 Azure Personalizer 서비스에서 자동 최적화 기능의 개념을 간략히 설명합니다.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 03/08/2021
ms.openlocfilehash: 603dc55b96d12610bc023487e731c7599841c985
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829418"
---
# <a name="personalizer-auto-optimize-preview"></a>Personalizer 자동 최적화(미리 보기)


## <a name="introduction"></a>소개
Personalizer 자동 최적화를 사용하면 모델 학습에 사용할 향상된 학습 설정을 자동으로 검색하고 적용하여 Personalizer 루프를 최상의 기계 학습 성능으로 유지하므로 사용자의 수작업이 줄어듭니다. 개선으로 인해 보상 손실이 발생할 가능성을 낮추기 위해 Personalizer에서 새로운 학습 설정을 적용할 때는 엄격한 기준이 적용됩니다.

Personalizer 자동 최적화는 공개 미리 보기로 제공되고 있으며 사용자 의견에 따라 기능, 접근 방식 및 프로세스가 변경됩니다.

## <a name="when-to-use-auto-optimize"></a>자동 최적화가 필요한 경우
대부분의 경우 자동 최적화를 켜는 것이 가장 좋습니다. 자동 최적화는 새 Personalizer 루프에 대해 기본적으로 *설정* 됩니다.

자동 최적화는 다음과 같은 상황에서 도움이 될 수 있습니다.
* 많은 테넌트에서 사용되는 애플리케이션을 빌드하고 각각 고유한 Personalizer 루프를 갖는 경우가 여기에 해당합니다(예를 들어 여러 전자상거래 사이트를 호스트하는 경우). 자동 최적화를 사용하면 많은 수의 Personalizer 루프에 대한 학습 설정을 조정하는 데 필요한 수작업을 없앨 수 있습니다.
* Personalizer를 배포한 후 제대로 작동하고 좋은 보상을 받고 있는지 검증했다면 기능에 버그나 문제가 없는 것을 확인한 것입니다.

> [!NOTE]
> 자동 최적화는 Personalizer 학습 설정을 주기적으로 덮어씁니다. 사용 사례 또는 업계에서 모델과 설정의 감사 및 보관이 필요하거나 이전 설정의 백업이 필요한 경우 Azure Portal을 통해 Personalizer API를 사용하면 학습 설정을 검색하거나 다운로드할 수 있습니다.

## <a name="how-to-enable-and-disable-auto-optimize"></a>자동 최적화를 사용하거나 사용하지 않도록 설정하는 방법
자동 최적화를 사용하도록 설정하려면 Azure Portal의 "모델 및 학습 설정" 블레이드에 있는 토글 스위치를 사용합니다. 

또는 Personalizer `/configurations/service` API를 사용하여 자동 최적화를 활성화할 수도 있습니다.

자동 최적화를 사용하지 않도록 설정하려면 토글을 끕니다.

## <a name="auto-optimize-reports"></a>보고서 자동 최적화

모델 및 학습 설정 블레이드에서 자동 최적화 실행 기록과 각각에 대해 수행된 작업을 볼 수 있습니다. 

이 테이블은 다음의 경우에 표시됩니다.
* 자동 최적화 실행이 수행된 경우
* 데이터 창이 포함된 경우
* 온라인, 기준선 및 가장 잘 찾은 학습 설정의 보상 성능인 경우
* 수행된 작업: 학습 설정이 업데이트되거나 되지 않은 경우

각 자동 최적화 기록 행에 있는 서로 다른 학습 설정의 보상 성능은 절대 숫자와 기준선 성능 대비 백분율로 표시됩니다. 

**예**: 기준선 평균 보상이 0.20으로 예상되고 온라인 Personalizer 동작이 0.30을 달성하는 경우 각각 100% 및 150%로 표시됩니다. 자동 최적화에서 0.40 평균 보상을 달성할 수 있는 학습 설정을 찾았으면 200%로 표시됩니다(0.40은 0.20의 200%). 신뢰 구간에서 허용하는 것으로 가정하면 새 설정이 적용되며, 다음 번에 실행될 때까지 Personalizer가 온라인 설정으로 구동됩니다.

최대 24개의 이전 자동 최적화 실행에 대한 기록이 분석을 위해 유지됩니다. 각각에 대한 이러한 오프라인 평가 및 보고서의 자세한 내용을 검색할 수 있습니다. 또한 보고서에는 이 기록에 있는 모든 학습 설정이 포함되며 이를 찾아서 다운로드하거나 적용할 수 있습니다.

## <a name="how-it-works"></a>작동 방식
Personalizer는 보상을 기반으로 사용하는 AI 모델을 지속적으로 학습시킵니다. 이러한 학습은 학습 프로세스에 사용되는 하이퍼 매개 변수 및 기타 값을 포함하는 몇 가지 *학습 설정* 에 따라 수행됩니다. 이러한 학습 설정은 특정 Personalizer 인스턴스에 맞게 "조정"될 수 있습니다. 

Personalizer에도 *오프라인 평가* 를 수행할 수 있는 기능이 있습니다. 오프라인 평가는 과거 데이터를 확인하고 다양한 알고리즘 및 모델에서 획득했을 수 있는 Personalizer 평균 보상의 통계 예측을 생성할 수 있습니다. 이 프로세스 중에는 Personalizer에서 더 우수한 학습 설정을 검색하여 지난 기간 동안 성능(획득한 보상)을 예측합니다.

#### <a name="auto-optimize-frequency"></a>자동 최적화 빈도
자동 최적화는 주기적으로 실행되며 과거 데이터를 기반으로 자동 최적화를 수행합니다.
* 애플리케이션에서 지난 2주 동안 약 20Mb가 넘는 데이터를 Personalizer에 보냈다면 지난 2주 동안의 데이터를 사용합니다.
* 애플리케이션에서 이 양보다 적게 보냈다면 Personalizer는 최적화할 데이터가 충분해질 때까지 또는 저장된 가장 빠른 데이터(최대 데이터 보존 기간(일))에 도달할 때까지 과거 기간(일)의 데이터를 추가합니다.

자동 최적화가 실행되는 정확한 시간과 일은 Personalizer 서비스에 의해 결정되며 시간에 따라 변동됩니다.

#### <a name="criteria-for-updating-learning-settings"></a>학습 설정을 업데이트하기 위한 조건

Personalizer는 이러한 보상 예측을 통해 다른 사용자의 현재 학습 설정을 변경할지 여부를 결정합니다. 각 예측은 95% 신뢰 구간 상한 및 하한이 있는 분포 곡선입니다. Personalizer는 다음과 같은 경우에만 새 학습 설정을 적용합니다.
  * 평가 기간 동안 더 높은 평균 보상을 나타내는 경우 그리고
  * 95% 신뢰 구간 하한이 온라인 학습 설정의 95% 신뢰 구간 하한보다 *높은* 경우
향후 보상 손실 가능성을 없애는 동시에 보상 향상을 극대화하기 위해 이 기준은 Personalizer에서 관리되며 [Seldonian 알고리즘](https://aisafety.cs.umass.edu/overview.html) 및 AI 안전의 연구 결과를 활용합니다.

#### <a name="limitations-of-auto-optimize"></a>자동 최적화의 제한 사항

Personalizer 자동 최적화는 과거 기간에 대한 평가를 바탕으로 향후 성능을 예측합니다. 전 세계의 외부 요인, 애플리케이션 및 사용자로 인해 지난 기간 동안 수행된 Personalizer 모델에 대한 이러한 예측과 추정이 미래를 대표하지 않을 수 있습니다.

다중 슬롯 개인 설정 API 미리 보기 기능을 사용하도록 설정한 Personalizer 루프에는 자동 최적화 미리 보기를 사용할 수 없습니다. 

## <a name="next-steps"></a>다음 단계

* [오프라인 평가](concepts-offline-evaluation.md)
* [학습 정책 및 설정](concept-active-learning.md)
* [오프라인 평가를 통해 Personalizer를 분석하는 방법](how-to-offline-evaluation.md) 
* [AI 안전 연구](https://aisafety.cs.umass.edu/overview.html) 
