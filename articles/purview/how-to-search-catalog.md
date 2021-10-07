---
title: '방법: Data Catalog 검색'
description: 이 문서에서는 Azure Purview 데이터 카탈로그를 검색하는 방법에 대한 개요를 제공합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 329213fc37edae93d3871c1a52b6d5b73f8f76ed
ms.sourcegitcommit: e82ce0be68dabf98aa33052afb12f205a203d12d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/07/2021
ms.locfileid: "129659567"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog 검색

데이터를 검사하고 Azure Purview 데이터 맵에 수집한 후 데이터 소비자는 분석 또는 거버넌스 워크로드에 필요한 데이터를 쉽게 찾아야 합니다. 원하는 데이터의 위치를 알 수 없기 때문에 데이터 검색에는 시간이 많이 걸릴 수 있습니다. 데이터를 찾은 후에도 데이터를 신뢰하고 데이터에 대한 종속성을 사용할 수 있는지 여부가 의심스러울 수 있습니다.

Azure Purview에서 검색의 목표는 데이터 검색 프로세스를 가속화하여 중요한 데이터를 신속하게 찾는 것입니다. 이 문서에서는 Azure Purview 데이터 카탈로그를 검색하여 찾고 있는 정보를 빠르게 발견하는 방법에 대해 설명합니다.

## <a name="search-the-catalog-for-assets"></a>카탈로그에서 자산 검색

Purview Studio UX의 위쪽 표시줄에서 검색 표시줄에 빠르게 액세스할 수 있습니다. 데이터 카탈로그 홈페이지에서 검색 표시줄은 화면 중앙에 있습니다.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure Purview 검색 창의 위치를 보여 주는 스크린샷" border="true":::

검색 표시줄을 클릭하면 검색 기록 및 데이터 카탈로그에서 최근에 액세스한 자산이 표시됩니다. 이렇게 하면 이미 수행된 이전 데이터 탐색에서 신속하게 선택할 수 있습니다.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="아무 키워드도 입력하지 않은 검색 창을 보여 주는 스크린샷" border="true":::

이름, 데이터 형식, 분류, 용어집 용어 등의 자산을 식별하는 데 도움이 되는 키워드를 입력합니다. 검색 키워드에 입력하면 Purview는 필요에 맞는 자산 및 검색을 동적으로 제안합니다. 검색을 완료하려면 "검색 결과 보기"를 클릭하거나 "Enter" 키를 누릅니다.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="사용자가 키워드를 입력하는 동안의 검색 창을 보여 주는 스크린샷" border="true":::

검색에 입력하면 Purview는 사용자가 입력한 키워드와 일치하는 에 대한 데이터 판독기인 데이터 자산 목록을 반환합니다.

Purview 관련성 엔진은 모든 일치 항목을 정렬하고 사용자에게 유용하다고 생각하는 항목에 따라 순위를 매겼습니다. 예를 들어 데이터 관리자가 용어집 용어를 할당하고 설명을 지정한 여러 키워드와 일치하는 테이블은 주석이 추가되지 않은 폴더보다 데이터 소비자에게 더 흥미로울 수 있습니다. 많은 요소 집합이 자산의 관련성 점수를 결정하며 Purview 검색 팀은 관련성 엔진을 지속적으로 조정하여 상위 검색 결과에 가치가 있는지 확인합니다.

상위 결과에 찾고 있는 자산이 포함되지 않은 경우 왼쪽의 facet를 사용하여 용어집 용어, 분류 및 포함하는 컬렉션과 같은 비즈니스 메타데이터를 기준으로 필터링할 수 있습니다. Azure Data Lake Storage Gen2 또는 Azure SQL Database 같은 특정 데이터 원본 형식에 관심이 있는 경우 원본 유형 필터를 사용하여 검색 범위를 좁힐 수 있습니다.

> [!NOTE]
> 검색은 데이터 판독기 또는 모음인 컬렉션의 자산만 반환합니다. 자세한 내용은 [컬렉션 만들기 및 관리를 참조하세요.](how-to-create-and-manage-collections.md)

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="검색 결과를 보여 주는 스크린샷" border="true":::

특정 주석의 경우 줄임표를 클릭하여 AND 조건 또는 OR 조건 중에서 선택할 수 있습니다. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="및 AND 또는 OR 조건 중에서 선택하는 방법을 보여주는 스크린샷" border="true":::

원하는 자산을 찾으면 이를 선택하여 스키마, 계보 및 자세한 분류 목록과 같은 세부 정보를 볼 수 있습니다. 자산 세부 정보 페이지에 대한 자세한 내용은 [카탈로그 자산 관리를 참조하세요.](catalog-asset-details.md)

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="자산 정보 페이지를 보여 주는 스크린샷" border="true":::

## <a name="bulk-edit-search-results"></a>검색 결과 대량 편집

검색에서 반환된 여러 자산을 변경하려는 경우 Azure Purview를 사용하여 용어집 용어, 분류 및 연락처를 대량으로 수정할 수 있습니다. 자세한 내용은 자산 [대량 편집 가이드를 참조하세요.](how-to-bulk-edit-assets.md)

## <a name="browse-the-data-catalog"></a>데이터 카탈로그 찾아보기

원하는 내용을 알고 있는 경우 검색이 좋지만 데이터 소비자가 사용 가능한 데이터를 탐색하려는 경우가 있습니다. Azure Purview 데이터 카탈로그는 사용자가 카탈로그에 있는 각 데이터 원본의 계층 구조를 수집하거나 트래버스하여 사용할 수 있는 데이터를 탐색할 수 있는 찾아보기 환경을 제공합니다. 자세한 내용은 [데이터 카탈로그 찾아보기를 참조하세요.](how-to-browse-catalog.md)

## <a name="search-query-syntax"></a>검색 쿼리 구문

모든 검색 쿼리는 키워드와 연산자로 구성됩니다. 키워드는 자산의 속성에 포함되는 항목입니다. 잠재적 키워드는 분류, 용어집 용어, 자산 설명 또는 자산 이름일 수 있습니다. 키워드는 일치 결과를 찾는 속성의 일부일 수 있습니다. 아래에 나열된 키워드 및 연산자를 사용하여 Azure Purview의 범위에서 찾고 있는 자산을 반환하는지 확인합니다.

공백, 대시 및 쉼표 등의 특정 문자는 구분 기호로 해석됩니다. 와 같은 문자열을 `hive-database` 검색하는 것은 두 개의 키워드를 검색하는 것과 `hive database` 같습니다. 

검색 쿼리를 작성하는 데 사용할 수 있는 연산자는 다음과 같습니다. 연산자는 단일 쿼리에 필요한 만큼 여러 번 조합할 수 있습니다.

| 연산자 | 정의 | 예제 |
| -------- | ---------- | ------- |
| 또는 | 자산이 두 키워드 중 하나 이상을 포함하도록 지정합니다. 모두 대문자여야 합니다. 공백은 OR 연산자이기도 합니다.  | 쿼리 `hive OR database`는 'hive'나 'database' 또는 둘 모두가 포함된 자산을 반환합니다. |
| AND | 자산이 두 키워드를 모두 포함해야 하도록 지정합니다. 모두 대문자여야 함 | 쿼리 `hive AND database`는 'hive'와 'database'가 모두 포함된 자산을 반환합니다. |
| NOT | 자산이 NOT 절 오른쪽의 키워드를 포함할 수 없도록 지정합니다. | 쿼리 `hive NOT database`는 'hive'를 포함하지만 'database'는 포함되지 않은 자산을 반환합니다. |
| () | 키워드 및 연산자 집합을 그룹화합니다. 여러 연산자를 결합할 때 괄호는 연산 순서를 지정합니다. | 쿼리 `hive AND (database OR warehouse)`는 'hive'와 'database' 또는 'warehouse' 중 하나 또는 둘 모두를 포함한 자산을 반환합니다. |
| "" | 쿼리에서 일치해야 하는 구의 정확한 내용을 지정합니다. | 쿼리 `"hive database"`는 속성에 "hive database" 구가 포함된 자산을 반환합니다. |
| * | 한 문자에서 여러 문자에 일치하는 와일드카드입니다. 키워드에서 첫 번째 문자로 사용할 수 없습니다. | 쿼리는 `dat*` 'data' 또는 'database'와 같이 'dat'로 시작하는 속성이 있는 자산을 반환합니다. |
| ? | 단일 문자와 일치하는 와일드카드입니다. 키워드에서 첫 번째 문자로 사용할 수 없음 | 쿼리 `dat?`는 'date' 또는'data'와 같이 'dat'로 시작하고 문자가 네 개인 속성이 있는 자산을 반환합니다. |

> [!Note]
> 항상 부울 연산자(**AND**, **OR**, **NOT**)는 모두 대문자로 지정합니다. 그러지 않으면 대/소문자와 추가 공백이 무시됩니다.

## <a name="next-steps"></a>다음 단계

- [용어집 용어를 만들고, 가져오며, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대한 용어 템플릿을 관리하는 방법](how-to-manage-term-templates.md)
