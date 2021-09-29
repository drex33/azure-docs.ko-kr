---
title: Azure Purview의 데이터에 대한 자산 인사이트
description: 이 방법 가이드에서는 데이터에 대한 Purview Insights 자산 보고를 보고 사용하는 방법을 설명합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8879edc7d1858cff5871c5339da4857d81f5c2d8
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129217024"
---
# <a name="asset-insights-on-your-data-in-azure-purview"></a>Azure Purview의 데이터에 대한 자산 인사이트

이 방법 가이드에서는 데이터에 대한 Purview Asset Insight 보고서에 액세스, 보기 및 필터링하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure Purview Insights 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> * Purview 계정에서 인사이트 보기.
> * 데이터의 조감도를 확인합니다.
> * 자산 수 세부 정보를 자세히 알아보기 위해 드릴다운합니다.

## <a name="prerequisites"></a>필수 조건

Purview 인사이트를 시작하기 전에 다음 단계를 완료했는지 확인합니다.

* Azure 리소스를 설정하고 데이터를 사용하여 계정을 채웁니다.

* 원본 유형에 대한 검사를 설정하고 완료합니다.

자세한 내용은 [Azure Purview에서 데이터 원본 관리를 참조하세요.](manage-data-sources.md)

## <a name="use-purview-asset-insights"></a>Purview 자산 Insights 사용

Azure Purview에서 원본 유형을 등록하고 검사할 수 있습니다. 검사가 완료되면 Asset Insights 자산 배포를 볼 수 있습니다. 이 자산은 분류 및 리소스 집합별로 데이터 자산의 상태를 알려줍니다. 또한 데이터 크기가 변경되었는지도 알려줍니다.

> [!NOTE]
> 원본 유형을 검색한 후 Asset Insights 3-8시간 동안 새 자산을 반영합니다. 지연은 배포 지역의 트래픽이 많거나 워크로드 크기가 높기 때문일 수 있습니다. 자세한 내용은 현장 지원 팀에 문의하세요.

1. Azure Portal Azure Purview 리소스로 이동합니다.

1. **개요** 페이지의 **시작하기** 섹션에서 **Purview Studio 열기** 타일을 선택합니다.

   :::image type="content" source="./media/asset-insights/portal-access.png" alt-text="Azure Portal에서 Purview 실행하기":::

1. Purview **홈** 페이지의 왼쪽 메뉴에서 **Insights** 선택합니다.

   :::image type="content" source="./media/asset-insights/view-insights.png" alt-text="Azure Portal에서 인사이트 보기":::

1. **Insights** 영역에서 **자산을** 선택하여 Purview Asset Insights 보고서를 **표시합니다.**

### <a name="view-asset-insights"></a>자산 Insights 보기

1. 기본 **자산 Insights** 페이지에는 다음 영역이 표시됩니다.

2. 원본 유형, 분류된 자산 및 검색된 자산을 표시하는 상위 수준 KPI
 
3. 첫 번째 그래프는 **원본 유형당 자산을 표시합니다.**

4. 원본 유형별로 자산 배포를 봅니다. 원본 유형별 자산 배포를 보려면 분류 또는 전체 분류 범주를 선택합니다. 
 
5. 자세히 보려면 자세히 **보기를** 선택합니다. 이 보기는 원본 유형 및 자산 수의 테이블 형식을 표시합니다. 분류 필터는 이 페이지로 진행됩니다.

   :::image type="content" source="./media/asset-insights/highlight-kpis.png" alt-text="Asset Insights KPI 및 그래프 보기":::
 
6. 자산 수가 있는 상위 폴더를 보려는 특정 원본을 선택합니다. 

   :::image type="content" source="./media/asset-insights/select-data-source.png" alt-text="원본 유형 선택":::
 
7. 위에서 선택한 원본 유형 내의 최상위 폴더에 대해 총 자산을 선택합니다.

   :::image type="content" source="./media/asset-insights/file-path.png" alt-text="파일 경로 보기":::

8. 폴더 내의 파일 목록을 봅니다. 이동 경로 탐색을 사용하여 Insights 다시 이동합니다.

   :::image type="content" source="./media/asset-insights/list-page.png" alt-text="자산 목록 보기":::  

### <a name="file-based-source-types"></a>파일 기반 원본 유형
Asset Insights 다음 몇 가지 그래프는 파일 기반 원본 형식의 분포를 보여 제공합니다. 원본 형식 내에서 **파일 형식의 크기 추세(GB)라고** 하는 첫 번째 그래프는 지난 30일 동안의 상위 파일 형식 크기 추세를 보여줍니다. 
 
1. 원본 형식을 선택해 소스 내의 파일 형식을 봅니다. 
 
1. **자세히 보기를** 선택하여 현재 데이터 크기, 크기 변경, 현재 자산 수 및 자산 수 변경을 확인합니다.
 
   > [!NOTE]
   > 검색이 지난 30일 동안 한 번만 실행되었거나 분류 추가/제거와 같은 카탈로그 변경이 30일 동안 한 번만 발생한 경우 위의 변경 정보는 비어 있는 것으로 나타납니다.

1. 원본 유형을 선택할 때 상위 자산 수가 변경된 상위 폴더를 참조하세요.

1. 경로를 선택하여 자산 목록을 확인합니다.

파일 기반 원본 형식의 두 번째 그래프는 ***리소스 집합 와 연결되지 않은 파일입니다.*** 모든 파일이 리소스 집합으로 롤업되어야 하는 경우 이 그래프를 통해 롤업되지 않은 자산을 이해하는 데 도움이 될 수 있습니다. 누락된 자산은 폴더의 잘못된 파일 패턴을 나타낼 수 있습니다. 파일에 대한 자세한 내용을 보려면 다른 그래프와 동일한 단계를 수행합니다.

   :::image type="content" source="./media/asset-insights/file-based-assets.png" alt-text="파일 기반 자산 보기":::  

## <a name="next-steps"></a>다음 단계

[스캔 Insights](./scan-insights.md) Azure Purview 인사이트 보고서에 대해 자세히 알아봅니다.
