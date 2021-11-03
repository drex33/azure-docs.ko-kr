---
title: Purview 인사이트를 사용하여 Azure Purview의 데이터에 대한 분류 보고
description: 이 방법 가이드는 데이터에 대한 Purview 분류 보고를 보고 사용하는 방법을 설명합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.topic: how-to
ms.date: 09/27/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 80929cb469d4d60c649786c955e8f92906794185
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131011090"
---
# <a name="classification-insights-about-your-data-from-azure-purview"></a>Azure Purview의 데이터에 대한 분류 인사이트

이 방법 가이드는 데이터에 대한 Purview 분류 인사이트 보고서를 액세스하고, 보고, 필터링하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure 부서의 범위 Insights는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

지원 되는 데이터 원본에는 다음이 포함 됩니다. Azure Blob Storage, Azure Data Lake Storage (ADLS) gen 1, Azure Data Lake Storage (ADLS) gen 2, Azure Cosmos DB (SQL API), Azure Synapse Analytics (이전의 SQL DW), Azure SQL Database, azure SQL Managed Instance , SQL Server, amazon S3 버킷 및 amazon RDS 데이터베이스 (공개 미리 보기)를 제공 합니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> - Azure에서 Purview 계정 시작하기
> - 데이터에 대한 분류 인사이트 보기
> - 데이터에 대한 자세한 분류 세부 정보 드릴다운

## <a name="prerequisites"></a>사전 요구 사항

Purview 인사이트를 시작하기 전에 다음 단계를 완료했는지 확인합니다.

- Azure 리소스 설정 및 테스트 데이터를 통해 관련 계정 채우기

- 데이터 원본별로 테스트 데이터에 대한 스캔 설정 및 완료 자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리](manage-data-sources.md) 및 [스캔 규칙 집합 만들기](create-a-scan-rule-set.md)를 참조 하세요.

- [데이터 판독기 또는 데이터 큐레이터 역할](catalog-permissions.md#roles)이 있는 계정으로 Purview에 로그인했습니다.

자세한 내용은 [Azure 부서의 범위에서 데이터 원본 관리](manage-data-sources.md)를 참조 하세요.

## <a name="use-purview-classification-insights"></a>Purview 분류 인사이트 사용

Azure Purview에서 분류는 토픽 태그와 비슷하며 검사하는 동안 데이터 자산 내에 있는 특정 유형의 데이터를 표시하고 식별하는 데 사용됩니다.

Purview는 Microsoft 365와 동일한 중요한 정보 유형을 사용하므로 전체 데이터 자산에서 기존 보안 정책 및 보호를 확장할 수 있습니다.

> [!NOTE]
> 원본 유형을 검사한 후 새 자산을 반영하도록 **분류** 인사이트에 몇 시간을 할당합니다.

**분류 인사이트를 보려면 다음 단계를 따르세요.**

1. [Azure Portal](https://aka.ms/purviewportal)의 **Azure Purview** 인스턴스 화면으로 이동해 Purview 계정을 선택합니다.

1. **개요** 페이지의 **시작** 섹션에서 **부서의 범위 Studio** 타일을 선택 합니다.

1. Purview에서 왼쪽의 **인사이트** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 메뉴 항목을 선택하여 **인사이트** 영역에 액세스합니다.

1. **인사이트** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 영역에서 **분류** 를 선택하여 Purview **분류 인사이트** 보고서를 표시합니다.

   :::image type="content" source="./media/insights/select-classification-labeling.png" alt-text="분류 인사이트 보고서" lightbox="media/insights/select-classification-labeling.png":::

   기본 **분류 인사이트** 페이지에는 다음 영역이 표시됩니다.

   |영역  |설명  |
   |---------|---------|
   |**분류가 있는 원본 개요**     |다음을 제공하는 타일을 표시합니다. <br>- 데이터에서 찾은 구독 수 <br>- 데이터에서 찾은 고유한 분류의 수 <br>- 찾은 분류된 원본 수 <br>- 찾은 분류된 파일 수 <br>- 찾은 분류된 테이블 수         |
   |**분류된 데이터가 있는 상위 원본(지난 30일)**     |분류된 데이터에서 찾은 원본 수의 지난 30일 동안의 추세를 표시합니다.            |
   |**원본별 상위 분류 범주**     |분류 범주별로 찾은 원본 공급자(예: **재무** 또는 **정부**)의 수를 표시합니다.      |
   |**파일의 상위 분류**     |신용 카드 번호 또는 주민 등록 번호와 같이 데이터의 파일에 적용된 상위 분류를 표시합니다.         |
   |**테이블의 상위 분류**     | 개인 식별 정보와 같이 데이터의 테이블에 적용된 상위 분류를 표시합니다. |   
   |  **분류 작업** <br>(파일 및 테이블) |  선택한 기간 동안 분류된 파일 또는 테이블의 수를 각각 표시하는 파일 및 테이블에 대한 별도의 그래프를 표시합니다. <br>**기본값**: 30일<br>그래프 위에 있는 **시간** 필터를 선택하여 표시할 다른 시간 프레임을 선택합니다.    |
   |    |    |

## <a name="classification-insights-drilldown"></a>분류 인사이트 드릴다운

다음 **분류 인사이트** 그래프에서 **자세히 보기** 링크를 선택하여 드릴다운하고 자세한 내용을 확인합니다.

- **원본별 상위 분류 범주**
- **파일의 상위 분류**
- **테이블의 상위 분류**
- **분류 작업 > 분류 데이터**

예를 들어 다음과 같은 가치를 제공해야 합니다.

:::image type="content" source="media/insights/view-classifications-small.png" alt-text="모든 분류 보기":::

자세한 내용을 확인하려면 다음 중 한 가지를 수행합니다.

|옵션  |설명  |
|---------|---------|
|**데이터 필터링**     |  분류 이름, 구독 이름 또는 원본 유형을 포함하여 표시되는 데이터를 필터링하려면 그리드 위의 필터를 사용합니다. <br><br>정확한 분류 이름을 잘 모르는 경우 **키워드로 필터링** 상자에 이름의 일부 또는 전체를 입력할 수 있습니다.       |
|**그리드 정렬** |열 머리글을 클릭하여 해당 열을 기준으로 그리드를 정렬합니다. | 
|**열 편집**     |  그리드에 더 많거나 더 적은 열을 표시하려면 **열 편집** :::image type="icon" source="media/insights/ico-columns.png" border="false":::을 선택한 다음, 순서를 보거나 변경하려는 열을 선택합니다.   |
|**더 자세히 드릴다운**     | 특정 분류로 드릴다운하려면 **분류** 열에서 이름을 선택하여 **원본별 분류** 보고서를 봅니다. <br><br>이 보고서는 원본 이름, 원본 유형, 구독 ID 및 분류된 파일 및 테이블 수를 포함하여 선택한 분류에 대한 데이터를 표시합니다.      |
|**자산 찾아보기**     |  특정 분류 또는 원본으로 찾은 자산을 탐색하려면 보고 있는 보고서에 따라 분류 또는 원본을 선택한 다음 필터 위에서 **자산 찾아보기** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false":::를 선택합니다. <br><br>검색 결과에는 선택한 필터에 대해 찾은 분류된 자산이 모두 표시됩니다.  자세한 내용은 [Azure Purview Data Catalog 검색](how-to-search-catalog.md)을 참조하세요.       |
| | |

## <a name="next-steps"></a>다음 단계

Azure Purview 인사이트 보고서에 대해 자세히 알아보기
> [!div class="nextstepaction"]
> [용어집 인사이트](glossary-insights.md)

> [!div class="nextstepaction"]
> [인사이트 검사](scan-insights.md)

> [!div class="nextstepaction"]
> [민감도 레이블 지정 인사이트](./sensitivity-insights.md)
