---
title: '방법: Data Catalog 검색'
description: 이 문서에서는 데이터 카탈로그를 검색하는 방법에 대한 개요를 제공합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 09/27/2021
ms.openlocfilehash: 5581a19f001b4caee8ef0e0d93908bd6d98471a0
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129213603"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog 검색

데이터 검색은 데이터 소비자를 위한 데이터 분석 또는 데이터 거버넌스 작업에 대한 첫 번째 단계입니다. 원하는 데이터의 위치를 알 수 없기 때문에 데이터 검색에는 시간이 많이 걸릴 수 있습니다. 데이터를 찾은 후에도 데이터를 신뢰하고 이에 대한 종속성을 적용해야 하는지 여부가 의심스러울 수 있습니다.

Azure Purview에서 검색의 목표는 데이터 검색 프로세스를 가속화하여 중요한 데이터를 신속하게 찾는 것입니다. 이 문서에서는 Azure Purview 데이터 카탈로그를 검색하여 찾고 있는 정보를 빠르게 발견하는 방법에 대해 설명합니다.

## <a name="search-the-catalog-for-assets"></a>카탈로그에서 자산 검색

Azure Purview에서 검색 창은 Purview studio UX 상단에 위치합니다.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure Purview 검색 창의 위치를 보여 주는 스크린샷" border="true":::

검색 창을 선택하면 최근 검색 기록과 최근에 액세스한 자산을 확인할 수 있습니다. 최근에 본 모든 자산을 확인하려면 **모두 보기** 를 선택합니다.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="아무 키워드도 입력하지 않은 검색 창을 보여 주는 스크린샷" border="true":::

이름, 데이터 형식, 분류, 용어집 용어 등의 자산을 식별하는 데 도움이 되는 키워드를 입력합니다. 원하는 자산과 관련된 키워드를 입력하면 Azure Purview는 검색 및 잠재적인 자산 일치 항목에 대한 제안을 표시합니다. 검색을 완료 하려면 **검색 결과 보기** 를 선택 하거나 "enter"를 누릅니다.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="사용자가 키워드를 입력하는 동안의 검색 창을 보여 주는 스크린샷" border="true":::

검색 결과 페이지에는 관련성 순서로 제공된 키워드와 일치하는 자산 목록이 표시됩니다. 다양한 요인이 자산의 관련성 점수에 영향을 줄 수 있습니다. 찾고 있는 자산에 적용되는 특정 데이터 저장소, 분류, 연락처, 레이블 및 용어집 용어를 선택하여 목록을 더 자세히 필터링할 수 있습니다.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="검색 결과를 보여 주는 스크린샷" border="true":::

 스키마, 계보, 자산 소유자를 비롯한 속성을 볼 수 있는 자산 세부 정보 페이지를 확인하려면 원하는 자산을 선택합니다.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="자산 정보 페이지를 보여 주는 스크린샷" border="true":::

## <a name="search-query-syntax"></a>검색 쿼리 구문

모든 검색 쿼리는 키워드와 연산자로 구성됩니다. 키워드는 자산의 속성에 포함되는 항목입니다. 잠재적 키워드는 분류, 용어집 용어, 자산 설명 또는 자산 이름일 수 있습니다. 키워드는 일치 결과를 찾는 속성의 일부일 수 있습니다. 아래에 나열된 키워드 및 연산자를 사용하여 Azure Purview의 범위에서 찾고 있는 자산을 반환하는지 확인합니다. 

검색 쿼리를 작성하는 데 사용할 수 있는 연산자는 다음과 같습니다. 연산자는 단일 쿼리에 필요한 만큼 여러 번 조합할 수 있습니다.

| 연산자 | 정의 | 예제 |
| -------- | ---------- | ------- |
| 또는 | 자산이 두 키워드 중 하나 이상을 포함하도록 지정합니다. 모두 대문자여야 합니다. 공백은 OR 연산자이기도 합니다.  | 쿼리 `hive OR database`는 'hive'나 'database' 또는 둘 모두가 포함된 자산을 반환합니다. |
| AND | 자산이 두 키워드를 모두 포함해야 하도록 지정합니다. 모두 대문자여야 함 | 쿼리 `hive AND database`는 'hive'와 'database'가 모두 포함된 자산을 반환합니다. |
| NOT | 자산이 NOT 절 오른쪽의 키워드를 포함할 수 없도록 지정합니다. | 쿼리 `hive NOT database`는 'hive'를 포함하지만 'database'는 포함되지 않은 자산을 반환합니다. |
| () | 키워드 및 연산자 집합을 그룹화합니다. 여러 연산자를 결합할 때 괄호는 연산 순서를 지정합니다. | 쿼리 `hive AND (database OR warehouse)`는 'hive'와 'database' 또는 'warehouse' 중 하나 또는 둘 모두를 포함한 자산을 반환합니다. |
| "" | 쿼리에서 일치해야 하는 구의 정확한 내용을 지정합니다. | 쿼리 `"hive database"`는 속성에 "hive database" 구가 포함된 자산을 반환합니다. |
| * | 한 문자에서 여러 문자에 일치하는 와일드카드입니다. 키워드에서 첫 번째 문자로 사용할 수 없습니다. | 쿼리 `dat*`는 'data' 또는 'database'와 같이 'dat'로 시작되는 속성이 있는 자산을 반환합니다. |
| ? | 단일 문자와 일치하는 와일드카드입니다. 키워드에서 첫 번째 문자로 사용할 수 없음 | 쿼리 `dat?`는 'date' 또는'data'와 같이 'dat'로 시작하고 문자가 네 개인 속성이 있는 자산을 반환합니다. |

> [!Note]
> 항상 부울 연산자(**AND**, **OR**, **NOT**)는 모두 대문자로 지정합니다. 그러지 않으면 대/소문자와 추가 공백이 무시됩니다.

## <a name="next-steps"></a>다음 단계

- [용어집 용어를 만들고, 가져오며, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대한 용어 템플릿을 관리하는 방법](how-to-manage-term-templates.md)
