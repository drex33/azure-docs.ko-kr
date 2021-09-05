---
title: Azure Purview에서 데이터에 대한 검사 인사이트
description: 이 방법 가이드에서는 데이터에 대한 Purview Insights 보고를 확인하고 사용하는 방법을 설명합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 11/20/2020
ms.openlocfilehash: 3c5a3b9b312e093d7bee5c0c555e8e2eab0c531b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122535867"
---
# <a name="scan-insights-on-your-data-in-azure-purview"></a>Azure Purview에서 데이터에 대한 검사 인사이트

이 방법 가이드에서는 데이터에 대한 Azure Purview 검사 인사이트 보고서에 액세스하고, 보고, 필터링하는 방법을 설명합니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Purview 계정에서 인사이트 보기.
> * 검사에 대한 조감도 얻기.

## <a name="prerequisites"></a>필수 조건

Purview 인사이트를 시작하기 전에 다음 단계를 완료했는지 확인합니다.

* Azure 리소스를 설정하고 데이터를 사용하여 계정을 채웁니다.
* 데이터 원본에 대한 검사를 설정하고 완료합니다.

자세한 내용은 [Azure Purview에서 데이터 원본 관리(미리 보기)](manage-data-sources.md)를 참조하세요.

## <a name="use-purview-scan-insights"></a>Purview Scan Insights 사용하기

Azure Purview에서 원본 유형을 등록하고 검사할 수 있습니다. Scan Insights에서 시간별 검사 상태를 볼 수 있습니다. 해당 인사이트는 특정 기간 동안의 실패 또는 성공하거나 취소된 검사 수를 알려줍니다.

### <a name="view-scan-insights"></a>검사 인사이트 보기

1. Azure Portal의 **Azure Purview** 인스턴스 화면으로 이동해 Purview 계정을 선택합니다.

1. **개요** 페이지의 **시작하기** 섹션에서 **Purview Studio 열기** 타일을 선택합니다.

   :::image type="content" source="./media/scan-insights/portal-access.png" alt-text="Azure Portal에서 Purview 실행하기":::

1. Purview의 **홈** 페이지에서 **인사이트 보기** 타일을 선택하여 **인사이트** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false":::영역에 액세스합니다.

   :::image type="content" source="./media/scan-insights/view-insights.png" alt-text="Azure Portal에서 인사이트 보기":::

1. **인사이트** :::image type="icon" source="media/scan-insights/ico-insights.png" border="false"::: 영역에서 **검사** 를 선택하여 Purview **Scan 인사이트** 보고서를 표시합니다.

### <a name="view-high-level-kpis-to-show-count-of-scans-by-status-and-deep-dive-into-each-scan"></a>상태별 검사 수를 표시하는 상위 수준 KPI와 심층 검사 보기
 
1. 상위 수준 KPI는 일정 기간 내에 실행되는 총 검사를 표시합니다. 기간은 지난 30일이 기본값으로 설정됩니다. 하지만 최근 7일을 선택할 수도 있습니다. 시간 필터를 기반으로 KPI 값은 검사 수를 적절하게 반영합니다.


1. 선택한 시간 필터 값에 따라 그래프의 주 또는 일별로 성공, 실패, 취소된 검사의 분포를 볼 수 있습니다.

1. 그래프의 아래쪽에는 자세히 살펴볼 수 있는 **더 보기** 링크가 있습니다. 해당 링크를 통해 검사 인사이트 경험 내에서 **검사 상태** 페이지를 볼 수 있습니다. 여기에서 최근 30일 동안의 검사 이름 및 성공, 실패, 취소 횟수를 확인할 수 있습니다.

    :::image type="content" source="./media/scan-insights/main-graph.png" alt-text="시간별 검사 상태 보기":::

4. Azure Purview의 **데이터 맵** 환경 내에서 검사 기록과 연결되는 **검사 이름** 을 클릭하여 특정 검사를 추가로 검색할 수 있습니다. 실행 기록 페이지에서 추가적인 오류 조사에 도움이 되는 실행 ID를 가져올 수 있습니다.

    :::image type="content" source="./media/scan-insights/scan-status.png" alt-text="검사 세부 정보 보기":::

5. 마지막으로, 실행 기록 페이지의 왼쪽 위 모서리에 있는 이동 경로를 따라 Scan Insights **검사 상태** 페이지로 돌아갈 수 있습니다.

    :::image type="content" source="./media/scan-insights/scan-history.png" alt-text="검색 기록 보기"::: 

## <a name="next-steps"></a>다음 단계

* [데이터 인사이트](./concept-insights.md)를 통해 Azure Purview **인사이트** 에 대한 자세한 정보 알아보기.

* [데이터 원본 관리](./manage-data-sources.md)를 통해 Azure Purview의 **데이터 맵** 환경에 대해 자세히 알아보기