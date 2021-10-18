---
title: Application Insights 깔때기
description: Funnels를 사용하여 고객이 애플리케이션과 상호 작용하는 방법을 검색하는 방법을 알아봅니다.
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 9733cc11c346263ad0801f97bd876e5b8c7d4fd9
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131679"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Application Insights Funnels를 통해 고객이 애플리케이션을 사용하는 방법 검색

고객 환경을 이해하는 것이 비즈니스에 가장 중요합니다. 애플리케이션이 여러 단계와 관련된 경우, 고객 대부분이 전체 프로세스를 거치는지 아니면 특정 시점에서 프로세스를 종료하는지를 알아야 합니다. 웹 애플리케이션에서 일련의 단계를 거치는 것을 *깔때기* 라고 합니다. Application Insights Funnels를 사용하여 사용자에 대한 정보를 얻고 단계별 전환율을 모니터링할 수 있습니다. 

## <a name="create-your-funnel"></a>깔때기 만들기
깔때기를 만들기 전에 먼저 답변하려는 질문에 관해 결정합니다. 예를 들어, 고객 프로필을 보고 티켓을 만들어 몇 명의 사용자가 홈페이지를 보고 있는지 확인하려고 할 수 있습니다. 

유입 경로를 만들려면:

1. **유입 경로** 탭에서 **수정** 을 선택합니다.
1. *최상위 단계* 를 선택합니다.

     :::image type="content" source="./media/usage-funnels/funnel.png" alt-text="유입 경로 탭의 스크린샷 및 수정 탭에서 단계 선택" lightbox="./media/usage-funnels/funnel.png":::

1. 단계에 필터를 적용하려면 **필터 추가** 를 선택합니다. 그러면 상위 단계의 항목을 선택한 후에 표시됩니다.
1. 그런 다음 *두 번째 단계* 등을 선택합니다.
1. 유입 경로 결과를 보려면 **보기** 탭을 선택합니다.

      :::image type="content" source="./media/usage-funnels/funnel-2.png" alt-text="상단 및 두 번째 단계의 결과를 보여 주는 보기 탭의 유입 경로 탭 스크린샷" lightbox="./media/usage-funnels/funnel-2.png":::

1. 유입 경로를 저장하여 나중에 보려면 상단에서 **저장** 을 선택합니다. **열기** 를 사용하여 저장된 유입 경로를 열 수 있습니다.

### <a name="funnels-features"></a>Funnels 기능

- 앱이 샘플링된 경우 샘플링 배너가 표시됩니다. 배너를 선택하면 컨텍스트 창이 열려 샘플링을 끄는 방법을 설명합니다. 
- 단계를 선택하면 오른쪽에 더 상세한 내용이 표시됩니다. 
- 기록 전환 그래프에서 지난 90일 동안의 전환율을 확인할 수 있습니다. 
- 사용자 도구에 액세스하여 사용자에 대해 더 깊이 있게 파악할 수 있습니다. 각 단계에서 필터를 사용할 수 있습니다. 

## <a name="next-steps"></a>다음 단계
  * [사용 현황 개요](usage-overview.md)
  * [사용자, 세션 및 이벤트](usage-segmentation.md)
  * [보존](usage-retention.md)
  * [통합 문서](../visualize/workbooks-overview.md)
  * [사용자 컨텍스트 추가](./usage-overview.md)
  * [Power BI에 내보내기](./export-power-bi.md)