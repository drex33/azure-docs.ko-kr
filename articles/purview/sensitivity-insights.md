---
title: Purview Insights를 사용하는 Azure Purview의 보유 데이터 관련 민감도 레이블 보고
description: 이 방법 가이드에서는 데이터에 대한 Purview 민감도 레이블 보고를 확인하고 사용하는 방법을 설명합니다.
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-insights
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 06a48cd4f1a0aa0d95f1c4b5fa0faf4d7ada2ab3
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130238044"
---
# <a name="sensitivity-label-insights-about-your-data-in-azure-purview"></a>Azure Purview에서 데이터에 대한 민감도 레이블 인사이트

이 방법 가이드에서는 데이터에 적용된 민감도 레이블에서 제공한 보안 인사이트에 액세스하고, 확인하고, 필터링하는 방법을 설명합니다.

> [!IMPORTANT]
> Azure Purview 민감도 레이블 Insights 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

지원되는 데이터 원본에는 Azure Blob Storage, ADLS(Azure Data Lake Storage) GEN 1, ADLS(Azure Data Lake Storage) GEN 2, SQL Server, Azure SQL Database, Azure SQL Managed Instance, Amazon S3 버킷이 포함됩니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

> [!div class="checklist"]
> - Azure에서 Purview 계정을 시작합니다.
> - 데이터에 대한 민감도 레이블 지정 인사이트를 봅니다.
> - 데이터에 대한 더 많은 민감도 레이블 지정 세부 정보를 드릴다운합니다.

> [!NOTE]
> Purview에서 검사하는 [Power BI 자산](register-scan-power-bi-tenant.md)에 있는 민감도 레이블은 현재 민감도 레이블 지정 인사이트 보고서에 표시되지 않습니다. 
>
> Power BI 자산의 민감도 레이블을 보려면 [Purview Data Catalog](how-to-search-catalog.md)에서 자산을 확인하세요.
> 
## <a name="prerequisites"></a>필수 구성 요소

Purview 인사이트를 시작하기 전에 다음 단계를 완료했는지 확인합니다.

- Azure 리소스를 설정하고 테스트 데이터를 통해 관련 계정을 채웠습니다.

- [Azure Purview의 자산에 대한 Microsoft 365 민감도 레이블을 확장했고](create-sensitivity-label.md), 데이터에 적용하려는 레이블을 만들거나 선택했습니다.

- 데이터 원본별로 테스트 데이터에 대한 스캔을 설정하고 완료했습니다. 자세한 내용은 [Azure Purview에서 데이터 원본 관리](manage-data-sources.md) 및 검사 규칙 집합 [만들기를 참조하세요.](create-a-scan-rule-set.md)

- [데이터 판독기 또는 데이터 큐레이터 역할](catalog-permissions.md#roles)이 있는 계정으로 Purview에 로그인했습니다.

자세한 내용은 [Azure Purview에서 데이터 원본 관리](manage-data-sources.md) 및 Azure [Purview에서 자동으로 데이터 레이블 지정을 참조하세요.](create-sensitivity-label.md)

## <a name="use-purview-sensitivity-labeling-insights"></a>Purview 민감도 레이블 지정 인사이트 사용

Purview에서 분류는 주제 태그와 비슷하며 검사하는 동안 데이터 자산 내에 있는 특정 유형의 데이터를 표시하고 식별하는 데 사용됩니다.

민감도 레이블을 사용하면 조직에서 중요한 특정 데이터의 상태를 확인할 수 있습니다. 예를 들어 조직 내에서 특정 프로젝트 이름에 대한 기밀을 유지하는 반면 동일한 용어는 다른 조직에 대한 기밀은 아닙니다. 

분류는 **사회 보장 번호** 의 분류가 있는 사회 보장 번호와 같이 직접 일치합니다. 

이와 달리 민감도 레이블은 하나 이상의 분류 및 조건을 함께 발견할 경우 적용됩니다. 이 컨텍스트에서 [조건](/microsoft-365/compliance/apply-sensitivity-label-automatically)은 **다른 분류에 근접** 및 **% 신뢰도** 와 같이 비정형 데이터에 대해 정의할 수 있는 모든 매개 변수를 나타냅니다. 

Purview는 Microsoft 365와 같이 [중요한 정보 형식](/microsoft-365/compliance/sensitive-information-type-entity-definitions)이라고도 하는 동일한 분류를 사용합니다. 이렇게 하면 Azure Purview 자산에서 기존 민감도 레이블을 확장할 수 있습니다.

> [!NOTE]
> 원본 형식을 스캔한 뒤, **민감도 레이블 지정** 인사이트가 새로운 자산을 반영할 수 있도록 몇 시간 정도 기다립니다.

**민감도 레이블 지정 인사이트를 보려면:**

1. **Azure Purview** 홈 페이지로 이동합니다.

1. **개요** 페이지의 **시작** 섹션에서 **Purview 계정 시작** 타일을 선택합니다.

1. Purview에서 왼쪽의 **인사이트** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 메뉴 항목을 선택하여 **인사이트** 영역에 액세스합니다.

1. **인사이트** :::image type="icon" source="media/insights/ico-insights.png" border="false"::: 영역에서 **민감도 레이블** 을 선택하여 Purview **민감도 레이블 지정 인사이트** 보고서를 표시합니다.

    > [!NOTE]
    > 이 보고서가 비어 있는 경우 민감도 레이블을 Azure Purview로 확장하지 않았을 수 있습니다. 자세한 내용은 [Azure Purview에서 데이터에 자동으로 레이블 지정](create-sensitivity-label.md)을 참조하세요.

   :::image type="content" source="media/insights/sensitivity-labeling-insights-small.png" alt-text="민감도 레이블 지정 인사이트":::

   주요 **민감도 레이블 지정 인사이트** 페이지에는 다음과 같은 영역이 표시됩니다.

   |영역  |설명  |
   |---------|---------|
   |**민감도 레이블이 있는 원본 개요**     |다음을 제공하는 타일을 표시합니다. <br>- 데이터에서 발견된 구독 수 <br>- 데이터에 적용된 고유 민감도 레이블 수 <br>- 민감도 레이블이 적용된 원본 수 <br>- 민감도 레이블이 적용된 파일 및 테이블의 수|
   |**레이블이 있는 데이터가 있는 상위 원본(지난 30일)**     | 민감도 레이블이 적용된 원본 수의 지난 30일 동안의 추세를 보여 줍니다.       |
   |**원본 전체에 적용된 상위 레이블**     |모든 Purview 데이터 리소스에서 적용되는 상위 레이블을 표시합니다. |
   |**파일에 적용된 상위 레이블**     |데이터의 파일에 적용되는 상위 민감도 레이블을 표시합니다.          |
   |**테이블에 적용된 상위 레이블**     | 데이터의 데이터베이스 테이블에 적용되는 상위 민감도 레이블을 표시합니다. |   
   |  **레이블 지정 작업**  |  파일 및 테이블에 대한 별도의 그래프를 표시합니다. 각 그래프에는 선택한 시간 프레임 위에 레이블이 지정된 파일 또는 테이블의 수가 표시됩니다. <br>**기본값**: 30일<br>그래프 위에 있는 **시간** 필터를 선택하여 표시할 다른 시간 프레임을 선택합니다.    |
   |    |    |

## <a name="sensitivity-labeling-insights-drilldown"></a>민감도 레이블 지정 인사이트 드릴다운

다음 **민감도 레이블 지정 인사이트** 그래프에서 **자세히 보기** 링크를 선택하여 자세한 내용을 드릴다운합니다.

- **원본 전체에 적용된 상위 레이블**
- **파일에 적용된 상위 레이블**
- **테이블에 적용된 상위 레이블**
- **레이블 지정 작업 > 레이블이 지정된 데이터**

예:

:::image type="content" source="media/insights/sensitivity-label-drilldown-small.png" alt-text="민감도 레이블 드릴다운":::

자세한 내용을 확인하려면 다음 중 한 가지를 수행합니다.

|옵션  |설명  |
|---------|---------|
|**데이터 필터링**     |  레이블 이름, 구독 이름 또는 원본 유형을 포함하여 표시되는 데이터를 필터링하려면 그리드 위의 필터를 사용합니다. <br><br>정확한 레이블 이름을 잘 모르는 경우 **키워드로 필터링** 상자에 이름의 일부 또는 전체를 입력할 수 있습니다.       |
|**그리드 정렬** |열 머리글을 클릭하여 해당 열을 기준으로 그리드를 정렬합니다. | 
|**열 편집**     |  그리드에 더 많거나 더 적은 열을 표시하려면 **열 편집** :::image type="icon" source="media/insights/ico-columns.png" border="false":::을 선택한 다음, 순서를 보거나 변경하려는 열을 선택합니다.    <br><br>열 머리글을 클릭하여 해당 열을 기준으로 그리드를 정렬합니다.   |
|**자세히 드릴다운**     | 특정 레이블로 드릴다운하려면 **민감도 레이블** 열에서 이름을 선택하여 **원본별 레이블 지정** 보고서를 표시합니다. <br><br>이 보고서는 원본 이름, 원본 유형, 구독 ID 및 분류된 파일 및 테이블 수를 포함하여 선택한 레이블에 대한 데이터를 표시합니다.      |
|**자산 찾아보기**     |  특정 레이블이나 원본으로 찾은 자산을 찾아보려면 표시하는 보고서에 따라 하나 이상의 레이블이나 원본을 선택한 다음, 필터 위의 **자산 찾아보기** :::image type="icon" source="media/insights/ico-browse-assets.png" border="false":::를 선택합니다. <br><br>검색 결과는 선택한 필터에 대해 찾은 레이블이 지정된 모든 자산을 표시합니다.  자세한 내용은 [Azure Purview Data Catalog 검색](how-to-search-catalog.md)을 참조하세요.       |
| | |

## <a name="sensitivity-label-integration-with-microsoft-365-compliance"></a>Microsoft 365 준수와 민감도 레이블 통합

Microsoft 365에서 제공하는 [Microsoft Information Protection](/microsoft-365/compliance/information-protection)과의 통합은 Purview를 통해 직접 데이터 자산에 대한 가시성을 확장하고, 데이터를 분류하고 레이블 지정할 수 있음을 의미합니다.

Azure Purview에서 자산에 대한 Microsoft 365 민감도 레이블을 확장하려면 Microsoft 365 준수 센터에서 Azure Purview에 대한 Information Protection을 적극적으로 설정해야 합니다.

자세한 내용은 [Azure Purview에서 데이터에 자동으로 레이블 지정](create-sensitivity-label.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

이러한 Azure Purview 인사이트 보고서에 대해 자세히 알아봅니다.

- [용어집 인사이트](glossary-insights.md)
- [인사이트 검사](scan-insights.md)
- [분류 인사이트](./classification-insights.md)
