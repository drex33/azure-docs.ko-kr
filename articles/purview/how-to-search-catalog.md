---
title: '방법: Data Catalog 검색'
description: 이 문서에서는 Azure 부서의 범위 data catalog를 검색 하는 방법에 대 한 개요를 제공 합니다.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 0473c75678631fee158cfabd33406b863971bf97
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129455772"
---
# <a name="search-the-azure-purview-data-catalog"></a>Azure Purview Data Catalog 검색

데이터를 검색 하 고 Azure 부서의 범위 데이터 맵으로 수집 한 후 데이터 소비자는 분석 또는 거 버 넌 스 작업에 필요한 데이터를 쉽게 찾을 수 있어야 합니다. 원하는 데이터의 위치를 알 수 없기 때문에 데이터 검색에는 시간이 많이 걸릴 수 있습니다. 데이터를 찾은 후에도 데이터를 신뢰 하 고이에 대 한 종속성을 가져올 수 있는지 여부를 얻으려 하거나 수 있습니다.

Azure Purview에서 검색의 목표는 데이터 검색 프로세스를 가속화하여 중요한 데이터를 신속하게 찾는 것입니다. 이 문서에서는 Azure Purview 데이터 카탈로그를 검색하여 찾고 있는 정보를 빠르게 발견하는 방법에 대해 설명합니다.

## <a name="search-the-catalog-for-assets"></a>카탈로그에서 자산 검색

검색 표시줄은 부서의 범위 Studio UX의 위쪽 막대에서 빠르게 액세스할 수 있습니다. 데이터 카탈로그 홈 페이지에서 검색 표시줄이 화면 가운데에 표시 됩니다.

:::image type="content" source="./media/how-to-search-catalog/purview-search-bar.png" alt-text="Azure Purview 검색 창의 위치를 보여 주는 스크린샷" border="true":::

검색 창을 클릭 하면 검색 기록 및 최근에 데이터 카탈로그에서 액세스 한 자산이 표시 됩니다. 이를 통해 이미 수행 된 이전 데이터 탐색을 빠르게 선택할 수 있습니다.

:::image type="content" source="./media/how-to-search-catalog/search-no-keywords.png" alt-text="아무 키워드도 입력하지 않은 검색 창을 보여 주는 스크린샷" border="true":::

이름, 데이터 형식, 분류, 용어집 용어 등의 자산을 식별하는 데 도움이 되는 키워드를 입력합니다. 검색 키워드에를 입력 하면 부서의 범위은 요구 사항에 맞는 자산 및 검색을 동적으로 제안 합니다. 검색을 완료하려면 "검색 결과 보기"를 클릭하거나 "Enter" 키를 누릅니다.

:::image type="content" source="./media/how-to-search-catalog/search-keywords.png" alt-text="사용자가 키워드를 입력하는 동안의 검색 창을 보여 주는 스크린샷" border="true":::

검색에를 입력 하면 부서의 범위는 사용자가에 입력 한 키워드와 일치 하는에 대 한 데이터 판독기 인 데이터 자산 목록을 반환 합니다.

부서의 범위 관련성 엔진은 모든 일치 항목을 정렬 하 고 사용자에 게 유용 하다 고 생각 하는 내용에 따라 순위를 결정 합니다. 예를 들어 데이터 관리자에서 용어를 할당 하 고 설명이 지정 된 여러 키워드에서 일치 하는 테이블은 주석이 추가 되지 않은 폴더 보다 데이터 소비자에 게 더 관심을 가질 수 있습니다. 많은 요소 집합은 자산의 관련성 점수를 결정 하 고, 부서의 범위 검색 팀은 상위 검색 결과에 값이 포함 되도록 관련성 엔진을 지속적으로 조정 하 고 있습니다.

검색 하려는 자산이 상위 결과에 포함 되지 않은 경우 왼쪽의 패싯을 사용 하 여 용어집 용어, 분류, 포함 하는 컬렉션 등의 비즈니스 메타 데이터를 기준으로 필터링 할 수 있습니다. Azure Data Lake Storage Gen2 또는 Azure SQL Database 같은 특정 데이터 원본 유형에 관심이 있으면 원본 유형 약 필터를 사용 하 여 검색 범위를 좁힐 수 있습니다.

> [!NOTE]
> 검색은 데이터 판독기 또는 큐레이터에 대 한 컬렉션의 자산만 반환 합니다. 자세한 내용은 [컬렉션 만들기 및 관리](how-to-create-and-manage-collections.md)를 참조 하세요.

:::image type="content" source="./media/how-to-search-catalog/search-results.png" alt-text="검색 결과를 보여 주는 스크린샷" border="true":::

특정 주석의 경우 줄임표를 클릭 하 여 AND 조건 또는 OR 조건 중에서 선택할 수 있습니다. 

:::image type="content" source="./media/how-to-search-catalog/search-and-or-choice.png" alt-text="And 및 or OR condition 중에서 선택 하는 방법을 보여 주는 스크린샷" border="true":::

원하는 자산을 찾은 후에는이를 선택 하 여 스키마, 계보 및 자세한 분류 목록과 같은 세부 정보를 볼 수 있습니다. 자산 정보 페이지에 대해 자세히 알아보려면 [카탈로그 자산 관리](catalog-asset-details.md)를 참조 하세요.

:::image type="content" source="./media/how-to-search-catalog/search-view-asset.png" alt-text="자산 정보 페이지를 보여 주는 스크린샷" border="true":::

## <a name="bulk-edit-search-results"></a>대량 편집 검색 결과

검색에서 반환 된 여러 자산을 변경 하려는 경우 Azure 부서의 범위를 사용 하 여 용어집 용어, 분류 및 연락처를 대량으로 수정할 수 있습니다. 자세히 알아보려면 [자산 대량 편집](how-to-bulk-edit-assets.md) 가이드를 참조 하세요.

## <a name="browse-the-data-catalog"></a>데이터 카탈로그 찾아보기

검색 하는 내용을 알고 있는 경우에는 데이터 소비자가 사용 가능한 데이터를 탐색 하려는 경우가 있습니다. Azure 부서의 범위 data catalog는 사용자가 컬렉션 또는 카탈로그에서 각 데이터 원본의 계층 구조를 탐색 하 여 사용할 수 있는 데이터를 탐색 하는 데 사용할 수 있는 찾아보기 환경을 제공 합니다. 자세한 내용은 [데이터 카탈로그 찾아보기](how-to-browse-catalog.md)를 참조 하세요.

## <a name="search-query-syntax"></a>검색 쿼리 구문

모든 검색 쿼리는 키워드와 연산자로 구성됩니다. 키워드는 자산의 속성에 포함되는 항목입니다. 잠재적 키워드는 분류, 용어집 용어, 자산 설명 또는 자산 이름일 수 있습니다. 키워드는 일치 결과를 찾는 속성의 일부일 수 있습니다. 아래에 나열된 키워드 및 연산자를 사용하여 Azure Purview의 범위에서 찾고 있는 자산을 반환하는지 확인합니다. 

검색 쿼리를 작성하는 데 사용할 수 있는 연산자는 다음과 같습니다. 연산자는 단일 쿼리에 필요한 만큼 여러 번 조합할 수 있습니다.

| 연산자 | 정의 | 예제 |
| -------- | ---------- | ------- |
| 또는 | 자산이 두 키워드 중 하나 이상을 포함하도록 지정합니다. 모두 대문자여야 합니다. 공백은 OR 연산자이기도 합니다.  | 쿼리 `hive OR database`는 'hive'나 'database' 또는 둘 모두가 포함된 자산을 반환합니다. |
| AND | 자산이 두 키워드를 모두 포함해야 하도록 지정합니다. 모두 대문자여야 함 | 쿼리 `hive AND database`는 'hive'와 'database'가 모두 포함된 자산을 반환합니다. |
| NOT | 자산이 NOT 절 오른쪽의 키워드를 포함할 수 없도록 지정합니다. | 쿼리 `hive NOT database`는 'hive'를 포함하지만 'database'는 포함되지 않은 자산을 반환합니다. |
| () | 키워드 및 연산자 집합을 그룹화합니다. 여러 연산자를 결합할 때 괄호는 연산 순서를 지정 합니다. | 쿼리 `hive AND (database OR warehouse)`는 'hive'와 'database' 또는 'warehouse' 중 하나 또는 둘 모두를 포함한 자산을 반환합니다. |
| "" | 쿼리에서 일치해야 하는 구의 정확한 내용을 지정합니다. | 쿼리 `"hive database"`는 속성에 "hive database" 구가 포함된 자산을 반환합니다. |
| * | 한 문자에서 여러 문자에 일치하는 와일드카드입니다. 키워드에서 첫 번째 문자로 사용할 수 없습니다. | 이 쿼리는 ' `dat*` 데이터 ' 또는 ' 데이터베이스 '와 같은 ' dat '로 시작 하는 속성을 포함 하는 자산을 반환 합니다. |
| ? | 단일 문자와 일치하는 와일드카드입니다. 키워드에서 첫 번째 문자로 사용할 수 없음 | 쿼리 `dat?`는 'date' 또는'data'와 같이 'dat'로 시작하고 문자가 네 개인 속성이 있는 자산을 반환합니다. |

> [!Note]
> 항상 부울 연산자(**AND**, **OR**, **NOT**)는 모두 대문자로 지정합니다. 그러지 않으면 대/소문자와 추가 공백이 무시됩니다.

## <a name="next-steps"></a>다음 단계

- [용어집 용어를 만들고, 가져오며, 내보내는 방법](how-to-create-import-export-glossary.md)
- [비즈니스 용어집에 대한 용어 템플릿을 관리하는 방법](how-to-manage-term-templates.md)
