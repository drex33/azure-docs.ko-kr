---
title: 매핑 데이터 흐름의 행 변경 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인의 매핑 데이터 흐름에서 행 변경 변환을 사용하여 데이터베이스 대상을 업데이트하는 방법입니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 052b91600f77d50c6ae70c6742aa4fe41c64e552
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060331"
---
# <a name="alter-row-transformation-in-mapping-data-flow"></a>매핑 데이터 흐름의 행 변경 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

행에 대한 삽입, 삭제, 업데이트, upsert 정책을 설정하려면 행 변경 변환을 사용합니다. 일대다 조건을 식으로 추가할 수 있습니다. 일대다 조건은 각 행이 첫 번째로 일치하는 식에 해당하는 정책으로 표시되므로 우선 순위에 따라 지정해야 합니다. 각 조건으로 인해 하나의 행(또는 여러 행)이 삽입, 업데이트, 삭제 또는 upsert될 수 있습니다. 행 변경은 데이터베이스에 대해 DDL 작업과 DML 작업을 모두 생성할 수 있습니다.

:::image type="content" source="media/data-flow/alter-row1.png" alt-text="행 변경 설정":::

행 변경 변환은 데이터 흐름의 데이터베이스, REST 또는 CosmosDB 싱크에 대해서만 작동합니다. 행에 할당하는 작업(삽입, 업데이트, 삭제, upsert)은 디버그 세션 중에 발생하지 않습니다. 파이프라인에서 데이터 흐름 실행 작업을 실행하여 데이터베이스 테이블에 행 변경 정책을 시행합니다.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4vJYc]

## <a name="specify-a-default-row-policy"></a>기본 행 정책 지정

행 변경 변환을 만들고 조건이 `true()`인 행 정책을 지정합니다. 이전에 정의된 식과 일치하지 않는 각 행은 지정된 행 정책에 대해 표시됩니다. 기본적으로 조건 식과 일치하지 않는 각 행은 `Insert`으로 표시됩니다.

:::image type="content" source="media/data-flow/alter-row4.png" alt-text="행 변경 정책":::

> [!NOTE]
> 모든 행을 하나의 정책으로 표시하려면 해당 정책에 대한 조건을 만들고 조건을 `true()`로 지정할 수 있습니다.

## <a name="view-policies-in-data-preview"></a>데이터 미리 보기에서 정책 보기

[디버그 모드](concepts-data-flow-debug-mode.md)를 사용하여 데이터 미리 보기 창에서 행 변경 정책의 결과를 볼 수 있습니다. 행 변경 변환의 데이터 미리 보기는 대상에 대해 DDL 또는 DML 작업을 생성하지 않습니다.

:::image type="content" source="media/data-flow/alter-row3.png" alt-text="행 변경 정책":::

각 행 변경 정책은 삽입, 업데이트, upsert 또는 삭제된 동작이 발생하는지 여부를 나타내는 아이콘으로 표시됩니다. 위쪽 헤더는 미리 보기에서 각 정책의 영향을 받는 행의 수를 보여 줍니다.

## <a name="allow-alter-row-policies-in-sink"></a>싱크에서 행 변경 정책 허용

행 변경 정책을 준수하려면 데이터 스트림을 데이터베이스 또는 Cosmos 싱크에 작성해야 합니다. 싱크의 **설정** 탭에서 해당 싱크에 대해 허용되는 행 변경 정책을 사용하도록 설정합니다.

:::image type="content" source="media/data-flow/alter-row2.png" alt-text="행 변경 싱크":::

기본 동작은 삽입만 허용하는 것입니다. 업데이트, upsert 또는 삭제를 허용하려면 해당 조건에 대해 싱크에 있는 상자를 선택합니다. 업데이트, upsert 또는 삭제를 사용하도록 설정한 경우 싱크에 일치시킬 키 열을 지정해야 합니다.

> [!NOTE]
> 삽입, 업데이트 또는 upsert가 싱크에 있는 대상 테이블의 스키마를 수정하는 경우 데이터 흐름은 실패합니다. 데이터베이스의 대상 스키마를 수정하려면 **테이블 다시 만들기** 를 테이블 동작으로 선택합니다. 그러면 새 스키마 정의를 사용하여 테이블을 삭제하고 다시 만듭니다.

싱크 변환에는 대상 데이터베이스의 고유한 행 ID에 대한 단일 키 또는 일련의 키가 필요합니다. SQL 싱크의 경우 싱크 설정 탭에서 키를 설정합니다. CosmosDB의 경우 설정에서 파티션 키를 설정하고 싱크 매핑에서 CosmosDB 시스템 필드 “ID”도 설정합니다. CosmosDB의 경우 업데이트, upsert, 삭제에 대한 시스템 열 “ID”를 반드시 포함해야 합니다.

## <a name="merges-and-upserts-with-azure-sql-database-and-azure-synapse"></a>Azure SQL Database 및 Azure Synapse를 사용하여 병합 및 upsert

데이터 흐름은 upsert 옵션을 사용하여 Azure SQL Database 및 Azure Synapse 데이터베이스 풀(데이터 웨어하우스)에 대한 병합을 지원합니다.

그러나 대상 데이터베이스 스키마가 키 열의 ID 속성을 사용하는 시나리오를 경험할 수 있습니다. 이 서비스를 사용하려면 업데이트 및 upsert에 대한 행 값을 일치시키는 데 사용할 키를 식별해야 합니다. 그러나 대상 열에 ID 속성 집합이 있고 upsert 정책을 사용하고 있는 경우 대상 데이터베이스에서 열에 쓰기를 허용하지 않습니다. 분산 테이블의 배포 열에 대해 upsert를 시도하는 경우 오류가 발생할 수도 있습니다.

다음과 같은 방법으로 이를 해결할 수 있습니다.

1. 싱크 변환 설정으로 이동하고 “키 열 쓰기 건너뛰기”를 설정합니다. 그러면 서비스에서 매핑에 대한 키 값으로 선택한 열을 쓰지 않습니다.

2. 해당 키 열이 ID 열에 대한 문제를 일으키는 열이 아닌 경우 싱크 변환 전처리 SQL 옵션인 ```SET IDENTITY_INSERT tbl_content ON```를 사용할 수 있습니다. 그런 다음 사후 처리 SQL 속성인 ```SET IDENTITY_INSERT tbl_content OFF```를 사용하여 이를 해제합니다.

3. ID 사례와 배포 열 사례 모두 upsert에서 조건부 분할 변환을 통해 별도의 삽입 조건과 별도의 업데이트 조건을 사용하는 것으로 논리를 전환할 수 있습니다. 이러한 방식으로 키 열 매핑을 무시하도록 업데이트 경로에 대한 매핑을 설정할 수 있습니다.

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    alterRow(
           insertIf(<condition>?),
           updateIf(<condition>?),
           deleteIf(<condition>?),
           upsertIf(<condition>?),
        ) ~> <alterRowTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림 `CleanData`를 사용하여 3개의 행 변경 조건을 만드는 `SpecifyUpsertConditions`이라는 행 변경 변환입니다. 이전 변환에서 행이 데이터베이스에서 삽입, 업데이트 또는 삭제되는지 여부를 결정하는 `alterRowCondition`이라는 이름의 열이 계산됩니다. 열 값에 행 변경 규칙과 일치하는 문자열 값이 있으면 해당 정책이 할당됩니다.

UI에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/alter-row4.png" alt-text="행 변경 예제":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
SpecifyUpsertConditions alterRow(insertIf(alterRowCondition == 'insert'),
    updateIf(alterRowCondition == 'update'),
    deleteIf(alterRowCondition == 'delete')) ~> AlterRow
```

## <a name="next-steps"></a>다음 단계

행 변경 변환 후에는 [데이터를 대상 데이터 스토리지로 싱크](data-flow-sink.md)할 수 있습니다.
