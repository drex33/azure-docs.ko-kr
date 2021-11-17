---
title: Microsoft Sentinel에서 분석 규칙에 대한 미세 조정 권장 사항 얻기
description: 자동으로 생성된 권장 사항을 사용하여 Microsoft Sentinel에서 위협 탐지 규칙을 미세 조정하여 위협 탐지 범위를 유지하면서 가양성을 줄이는 방법을 알아봅니다.
author: yelevin
ms.author: yelevin
ms.service: microsoft-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1b1d71f2c8a6a5f74d68d9480b918743a707cc83
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521513"
---
# <a name="get-fine-tuning-recommendations-for-your-analytics-rules-in-microsoft-sentinel"></a>Microsoft Sentinel에서 분석 규칙에 대한 미세 조정 권장 사항 얻기

> [!IMPORTANT]
>
> 검색 튜닝은 현재 **미리 보기** 에 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

SIEM에서 위협 탐지 규칙을 미세 조정하는 것은 위협 탐지 범위를 극대화하고 가양성 비율을 최소화하는 것 사이의 균형을 조정하는 어려운 연속 프로세스일 수 있습니다. Microsoft Sentinel은 기계 학습을 사용하여 시간별 인시던트 대응뿐만 아니라 데이터 원본에서 수십억 개의 신호를 분석하고, 패턴을 추론하고, 튜닝 오버헤드를 크게 낮출 수 있는 실행 가능한 권장 사항 및 인사이트를 제공하여 실제 위협을 감지하고 대응하는 데 집중할 수 있도록 함으로써 이 프로세스를 간소화하고 간소화합니다.

이제 튜닝 권장 사항 및 인사이트가 분석 규칙에 기본 제공되었습니다. 이 문서에서는 이러한 인사이트가 표시하는 내용과 권장 사항을 구현하는 방법을 설명합니다.

## <a name="view-rule-insights-and-tuning-recommendations"></a>규칙 인사이트 및 튜닝 권장 사항 보기

Microsoft Sentinel에 분석 규칙에 대한 튜닝 권장 사항이 있는지 확인하려면 Microsoft Sentinel 탐색 메뉴에서 **분석을** 선택합니다.

권장 사항이 있는 규칙은 다음과 같이 전구 아이콘을 표시합니다.

:::image type="content" source="media/detection-tuning/rule-list-with-insight.png" alt-text="권장 사항 표시기가 있는 분석 규칙 목록의 스크린샷." lightbox="media/detection-tuning/rule-list-with-insight.png":::

다른 인사이트를 통해 권장 사항을 보려면 규칙을 편집합니다. 분석 규칙 마법사의 **규칙 논리 설정** 탭에 결과 **시뮬레이션** 표시 아래에 함께 표시됩니다.

:::image type="content" source="media/detection-tuning/tuning-insights.png" alt-text="분석 규칙의 인사이트 조정 스크린샷." lightbox="media/detection-tuning/tuning-insights.png":::

### <a name="types-of-insights"></a>인사이트 유형

**튜닝 인사이트** 표시는 스크롤하거나 살짝 밀 수 있는 여러 창으로 구성되며 각각 다른 것을 보여 줍니다. 인사이트가 표시되는 시간 프레임(14일)은 프레임의 맨 위에 표시됩니다.

1. 첫 번째 인사이트 창에는 일부 통계 정보(인시던트당 평균 경고 수, 열린 인시던트 수 및 닫힌 인시던트 수)가 분류별로 그룹화되어 표시됩니다(참/거짓 긍정). 이 인사이트를 통해 이 규칙의 부하를 파악하고 튜닝이 필요한지(예: 그룹화 설정을 조정해야 하는 경우)를 파악할 수 있습니다.

    :::image type="content" source="media/detection-tuning/rule-efficiency.png" alt-text="규칙 효율성 인사이트의 스크린샷.":::

    이 인사이트는 Log Analytics 쿼리의 결과입니다. **인시던트당 평균 경고를** 선택하면 인사이트를 생성한 Log Analytics의 쿼리로 열립니다. **인시던트 열기를** 선택하면 **인시던트** 블레이드로 열립니다.

1. 두 번째 인사이트 창에서는 제외할 [엔터티 목록을 권장합니다.](entities-in-azure-sentinel.md) 이러한 엔터티는 **가양성** 으로 분류되고 닫은 인시던트와 고도로 상관 관계가 있습니다. 나열된 각 엔터티 옆에 있는 더하기 기호를 선택하여 나중에 이 규칙을 실행하여 쿼리에서 제외합니다. 

    :::image type="content" source="media/detection-tuning/entity-exclusion.png" alt-text="엔터티 제외 권장 사항의 스크린샷.":::

    이 권장 사항은 Microsoft의 고급 데이터 과학 및 기계 학습 모델에서 생성됩니다. **튜닝 인사이트** 표시에 이 창이 포함되는 것은 표시할 권장 사항이 있는지에 따라 달라집니다.

1. 세 번째 인사이트 창에는 이 규칙에 의해 생성된 모든 경고에서 가장 자주 나타나는 네 개의 매핑된 엔터티가 표시됩니다. 결과를 생성하려면 이 인사이트에 대한 규칙에 엔터티 매핑을 구성해야 합니다. 이 인사이트는 "스포트라이트를 받고" 다른 엔터티에서 주의를 끄는 엔터티를 인식하는 데 도움이 될 수 있습니다. 이러한 엔터티를 다른 규칙에서 개별적으로 처리하거나 가양성 또는 노이즈라고 판단하여 규칙에서 제외할 수 있습니다.

    :::image type="content" source="media/detection-tuning/top-entities.png" alt-text="상위 엔터티 인사이트의 스크린샷.":::

    이 인사이트는 Log Analytics 쿼리의 결과입니다. 엔터티를 선택하면 인사이트를 생성한 Log Analytics의 쿼리로 열립니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.
- [Microsoft Sentinel에서 가양성 처리](false-positives.md)
- [UEBA 데이터를 사용하여 가양성 분석](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
