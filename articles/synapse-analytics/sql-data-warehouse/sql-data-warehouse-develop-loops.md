---
title: T-SQL 루프 사용
description: Azure Synapse Analytics의 전용 SQL 풀에 대해 T-SQL 루프를 사용하고 커서를 바꾸어 솔루션을 개발하는 방법을 설명합니다.
services: synapse-analytics
author: MSTehrani
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/17/2018
ms.author: emtehran
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: a6c4eb98d77ece6e6ae130fd57cc263ee7e5ca64
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "98683225"
---
# <a name="using-t-sql-loops-for-dedicated-sql-pools-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 전용 SQL 풀에 T-SQL 루프 사용

이 문서에는 T-SQL 루프를 사용하고 커서를 바꾸는 전용 SQL 풀 솔루션 개발에 대한 팁이 포함되어 있습니다.

## <a name="purpose-of-while-loops"></a>WHILE 루프의 목적

Azure Synapse의 전용 SQL 풀은 문 블록을 반복 실행하는 [WHILE](/sql/t-sql/language-elements/while-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) 루프를 지원합니다. 이 WHILE 루프는 지정한 조건이 true이거나 코드가 BREAK 키워드를 사용하여 루프를 명시적으로 종료할 때까지 계속됩니다.

루프는 SQL 코드에 정의된 커서를 대체하는 데 유용합니다. 다행히 SQL 코드로 작성된 거의 모든 커서는 빠른 정방향 읽기 전용 변형만 존재합니다. 따라서 WHILE 루프는 커서를 바꾸는 좋은 대안입니다.

## <a name="replacing-cursors-in-dedicated-sql-pool"></a>전용 SQL 풀에서 커서 바꾸기

그러나 계속 진행하기 전에 먼저 스스로 질문해 보아야 합니다. “집합 기반 작업을 사용하도록 이 커서를 다시 작성할 수 있을까?”

대부분의 경우 다시 작성할 수 있으며 자주 가장 좋은 방법이기도 합니다. 집합 기반 작업은 흔히 행별 반복 접근 방식보다 더 빠르게 수행됩니다.

빠른 정방향 읽기 전용 커서는 루핑 구성으로 쉽게 대체할 수 있습니다. 다음은 간단한 예제입니다. 이 코드 예제는 데이터베이스의 모든 테이블에 대한 통계를 업데이트합니다. 루프의 테이블을 반복하여 각 명령이 순차적으로 실행됩니다.

첫째, 개별 문을 식별하는 데 사용되는 고유한 행 번호를 포함하는 임시 테이블을 만듭니다.

```sql
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

둘째, 루프를 수행하는 데 필요한 변수를 초기화합니다.

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

이제 문을 반복하여 한 번에 하나씩 실행합니다.

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

마지막으로 첫 번째 단계에서 만든 임시 테이블을 삭제합니다.

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>다음 단계

더 많은 개발 팁은 [개발 개요](sql-data-warehouse-overview-develop.md)를 참조하세요.
