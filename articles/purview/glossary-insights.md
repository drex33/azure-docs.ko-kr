---
title: 부서의 범위 Insights를 사용 하 여 데이터에 대 한 용어집 보고서
description: 이 방법 가이드에서는 데이터에 대 한 부서의 범위 Insights 용어집 보고를 보고 사용 하는 방법을 설명 합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 315cfdc60de77ab4d6345c5d579351049c5129a2
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218941"
---
# <a name="glossary-insights-on-your-data-in-azure-purview"></a>Azure 부서의 범위의 데이터에 대 한 용어집 정보

이 방법 가이드에서는 데이터에 대 한 부서의 범위 용어집 정보 보고서에 액세스 하 고, 보고, 필터링 하는 방법을 설명 합니다.

> [!IMPORTANT]
> Azure 부서의 범위 Insights는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> - 부서의 범위 계정에서 Insights로 이동 합니다.
> - 데이터의 눈 모양 보기

## <a name="prerequisites"></a>필수 조건

Purview 인사이트를 시작하기 전에 다음 단계를 완료했는지 확인합니다.

- Azure 리소스를 설정 하 고 데이터를 사용 하 여 계정 채우기

- 원본 유형에 대 한 검색 설정 및 완료

- 용어집을 설정 하 고 용어집에 자산을 첨부 합니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-glossary-insights"></a>부서의 범위 용어집 사용 Insights

Azure 부서의 범위에서 용어집 용어를 만들고 자산에 연결할 수 있습니다. 나중에 용어집 Insights에서 용어집 배포를 볼 수 있습니다. 이는 자산에 연결 된 용어를 사용 하 여 용어집의 상태를 알려 줍니다. 또한 사용자 수 별로 역할의 상태별 상태 및 배포를 알려 줍니다.

**용어집 Insights 보려면 다음을 수행 합니다.**

1. [Azure Portal](https://aka.ms/purviewportal)의 **Azure Purview** 인스턴스 화면으로 이동해 Purview 계정을 선택합니다.

1. **개요** 페이지의 **시작** 섹션에서 **Open 부서의 범위 Studio** account 타일을 선택 합니다.

   :::image type="content" source="./media/glossary-insights/portal-access.png" alt-text="Azure Portal에서 Purview 실행하기":::

1. 부서의 범위 **홈** 페이지의 왼쪽 메뉴에서 **Insights** 를 선택 합니다.

   :::image type="content" source="./media/glossary-insights/view-insights.png" alt-text="Azure Portal에서 인사이트 보기":::

1. **Insights** 영역에서 **용어집** 을 선택 하 여 부서의 범위 **용어집 Insights** 보고서를 표시 합니다.

**용어집 Insights** 은 조직에 대해 잘 정의 된 용어집을 유지 하기 위해 비즈니스 사용자, 중요 한 정보를 제공 합니다.

1. 이 보고서는 부서의 범위 계정에 총 용어 _를 표시 하는 **개략적인 kpi** 로 시작 하 고 자산을 포함 하는 **_자산_*_ 및 _*_만료 된 약관_ 은 제외 하 고 승인 된 용어** 입니다. 이러한 각 값은 용어집의 상태를 식별 하는 데 도움이 됩니다.

   :::image type="content" source="./media/glossary-insights/glossary-kpi.png" alt-text="용어집 insights KPI 보기"::: 


2. **용어** 에 대 한 스냅숏 섹션 (위에 표시 됨)은 자산이 없는 자산 및 약관이 있는 용어에 대해 **_초안_*_, _*_승인_*_, _*_경고_*_ 및 _*_만료_** 됨으로 용어 상태를 표시 합니다.

3. **자세히 보기** 를 선택 하 여 다양 한 상태의 용어 이름 및 **_관리자_*_ 및 _*_전문가_** 에 대 한 자세한 정보를 확인 합니다. 

   :::image type="content" source="./media/glossary-insights/glossary-view-more.png" alt-text="자산이 있거나 없는 용어에 대 한 스냅숏":::  

4. "자세히 보기"를 선택 하는 경우 (**자산이 있는 승인 된 용어** Insights _) _ *용어집** 약관 정보 페이지로 이동할 수 있습니다. 여기서는 첨부 된 용어가 포함 된 자산 목록으로 이동할 수 있습니다. 

   :::image type="content" source="./media/glossary-insights/navigate-to-glossary-detail.png" alt-text="용어집에 Insights"::: 

4. 용어집 정보 페이지에서 누락 된 정보 유형별로 **불완전 한 계약** 의 분포를 확인 합니다. 그래프는 정의가 누락 된 용어 수 ***_, _*_누락 된 전문가_*_, _*_누락 관리자_*_ 및 _* 누락 _여러_** 필드가 표시 됩니다.

1. 누락 된 정보가 있는 용어를 보려면 ***자세히 보기** _ * 완료 되지 않은 용어 * *를 선택 합니다. 용어집 용어 정보 페이지로 이동 하 여 누락 된 정보를 입력 하 고 용어집 용어가 완료 되었는지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[용어집](./how-to-create-import-export-glossary.md) 을 통해 용어집 용어를 만드는 방법에 대해 자세히 알아보세요.
