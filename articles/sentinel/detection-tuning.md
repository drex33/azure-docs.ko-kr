---
title: Microsoft 센티널의 분석 규칙에 대 한 미세 조정 권장 사항 가져오기
description: 자동으로 생성 된 권장 사항을 사용 하 여 Microsoft 센티널에서 위협 검색 규칙을 미세 조정 하는 방법에 대해 알아보고 위협 검색 범위를 유지 하면서 거짓 긍정을 줄입니다.
author: yelevin
ms.author: yelevin
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 11/09/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 854e8fa7d129140fca1a16dc02971bd2cd99ceb7
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132323048"
---
# <a name="get-fine-tuning-recommendations-for-your-analytics-rules-in-microsoft-sentinel"></a>Microsoft 센티널의 분석 규칙에 대 한 미세 조정 권장 사항 가져오기

> [!IMPORTANT]
>
> 검색 튜닝은 현재 **미리 보기로** 제공 됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

SIEM의 위협 검색 규칙을 미세 조정 하는 것은 위협 검색 범위를 극대화 하 고 가양성을 최소화 하는 것이 어려울 수 있습니다. Microsoft 센티널은 기계 학습을 사용 하 여 데이터 원본에서 수십억 개의 신호를 분석 하 고 시간에 따른 인시던트에 대 한 응답을 분석 하 고, 튜닝 오버 헤드를 크게 낮출 수 있는 조치 가능한 권장 사항 및 통찰력을 제공 하 고, 실제 위협에 대 한 검색 및 대응에 집중할 수 있도록 기계 학습을 통해이 프로세스를 간소화 하 고 간소화 합니다.

이제 튜닝 권장 사항 및 통찰력이 분석 규칙에 기본 제공 됩니다. 이 문서에서는 이러한 통찰력을 보여 주고 권장 사항을 구현 하는 방법을 설명 합니다.

## <a name="view-rule-insights-and-tuning-recommendations"></a>규칙 정보 및 튜닝 권장 사항 보기

Microsoft 센티널에 분석 규칙에 대 한 튜닝 권장 사항이 있는지 확인 하려면 Microsoft 센티널 탐색 메뉴에서 **분석** 을 선택 합니다.

권장 사항이 있는 모든 규칙은 다음과 같이 전구 아이콘을 표시 합니다.

:::image type="content" source="media/detection-tuning/rule-list-with-insight.png" alt-text="권장 사항 표시기가 있는 분석 규칙 목록 스크린샷" lightbox="media/detection-tuning/rule-list-with-insight.png":::

규칙을 편집 하 여 다른 정보를 비롯 한 권장 사항을 확인 합니다. 분석 규칙 마법사의 **규칙 논리 설정** 탭에서 **결과 시뮬레이션** 표시 아래에 함께 표시 됩니다.

:::image type="content" source="media/detection-tuning/tuning-insights.png" alt-text="분석 규칙의 튜닝 정보에 대 한 스크린샷" lightbox="media/detection-tuning/tuning-insights.png":::

### <a name="types-of-insights"></a>정보 유형

**튜닝 정보** 표시는 스크롤 하거나 살짝 밀기 할 수 있는 여러 개의 창으로 구성 되어 있으며, 각 창에는 서로 다른 항목이 표시 됩니다. 정보를 표시 하는 시간 프레임-14 일은 프레임 맨 위에 표시 됩니다.

1. 첫 번째 정보 창에는 몇 가지 통계 정보가 표시 됩니다. 여기에는 인시던트에 대 한 평균 경고 수, 열린 인시던트 수 및 종결 된 인시던트 수가 분류 별로 그룹화 되어 표시 됩니다 (true/false 긍정). 이러한 정보를 활용 하면이 규칙에 대 한 부하를 파악 하 고 튜닝이 필요한 지 여부를 파악할 수 있습니다. 예를 들어 그룹화 설정을 조정 해야 하는 경우를 들 수 있습니다.

    :::image type="content" source="media/detection-tuning/rule-efficiency.png" alt-text="규칙 효율성 통찰력의 스크린샷":::

    이 정보는 Log Analytics 쿼리의 결과입니다. **인시던트 당 평균 경고** 를 선택 하면 정보를 생성 한 Log Analytics의 쿼리로 이동 합니다. **열린 인시던트** 를 선택 하면 **인시던트** 블레이드로 이동 합니다.

1. 두 번째 정보 창에서는 제외할 [엔터티의](entities-in-azure-sentinel.md) 목록을 권장 합니다. 이러한 엔터티는 사용자가 닫고 **가양성** 으로 분류 한 인시던트에 매우 밀접 하 게 연관 되어 있습니다. 나열 된 각 엔터티 옆에 있는 더하기 기호를 선택 하 여이 규칙의 이후 실행에서 쿼리에서 제외 합니다. 

    :::image type="content" source="media/detection-tuning/entity-exclusion.png" alt-text="엔터티 제외 권장 사항의 스크린샷.":::

    이 권장 사항은 Microsoft의 고급 데이터 과학 및 기계 학습 모델에 의해 생성 됩니다. 이 창에 포함 된 **튜닝 정보** 표시는 표시할 권장 사항에 따라 달라 집니다.

1. 세 번째 정보 창에는이 규칙에 의해 생성 된 모든 경고에서 가장 자주 표시 되는 네 개의 엔터티가 표시 됩니다. 이 정보를 얻기 위해 규칙에서 엔터티 매핑을 구성 해야 결과가 생성 됩니다. 이러한 통찰력을 통해 "스포트라이트 많이 사용" 되는 모든 엔터티를 파악 하 고 다른 엔터티를 통해 주의를 기울일 수 있습니다. 이러한 엔터티를 다른 규칙에서 개별적으로 처리 하거나 거짓 긍정 또는 소음 이라고 판단 하 고 규칙에서 제외할 수 있습니다.

    :::image type="content" source="media/detection-tuning/top-entities.png" alt-text="최상위 엔터티 정보에 대 한 스크린샷":::

    이 정보는 Log Analytics 쿼리의 결과입니다. 엔터티를 선택 하면 정보를 생성 한 Log Analytics의 쿼리로 이동 합니다.

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.
- [Microsoft 센티널의 가양성 처리](false-positives.md)
- [UEBA 데이터를 사용하여 가양성 분석](investigate-with-ueba.md#use-ueba-data-to-analyze-false-positives)
- [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)
