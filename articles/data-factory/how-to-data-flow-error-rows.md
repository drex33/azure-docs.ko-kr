---
title: Azure Data Factory의 매핑 데이터 흐름에서 오류 행 처리
description: 매핑 데이터 흐름을 사용하여 Azure Data Factory에서 SQL 잘림 오류를 처리하는 방법에 대해 알아봅니다.
author: kromerm
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 11/22/2020
ms.author: makromer
ms.openlocfilehash: 9a7fb311fce0c557276f0ce8feb814d791df959d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831272"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Data Factory 매핑 데이터 흐름에서 SQL 잘림 오류 행 처리

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름을 사용할 때 Data Factory의 일반적인 시나리오는 변환된 데이터를 Azure SQL Database의 데이터베이스에 쓰는 것입니다. 이 시나리오에서는 방지해야 하는 일반적인 오류 조건은 열 잘림입니다.

ADF 데이터 흐름의 데이터베이스 싱크에 데이터를 쓸 때 오류를 정상적으로 처리하는 주요 두 가지 방법이 있습니다.

* 데이터베이스 데이터를 처리할 때 [오류 행 처리](./connector-azure-sql-database.md#error-row-handling) 싱크를 ‘오류 발생 시 계속’으로 설정합니다. 이는 데이터 흐름에 사용자 지정 논리가 필요하지 않은 자동 캐치올(Catch-All) 메서드입니다.
* 또는 다음 단계를 수행하여 대상 문자열 열에 맞지 않는 열에 대한 로깅을 제공하여 데이터 흐름이 계속되게 합니다.

> [!NOTE]
> 자동 오류 행 처리를 사용하도록 설정하는 경우, 자체 오류 처리 논리를 작성하는 아래 메서드와 달리 약간의 성능 저하가 발생하며, ADF가 추가 단계를 수행하여 오류를 트래핑하는 2단계 작업을 수행하게 됩니다.

## <a name="scenario"></a>시나리오

1. ‘이름’이라는 ```nvarchar(5)```열이 있는 대상 데이터베이스 테이블이 있습니다.

2. 데이터 흐름 내에서 싱크의 동영상 제목을 해당 대상 ‘이름’ 열에 매핑해야 합니다.

    :::image type="content" source="media/data-flow/error4.png" alt-text="동영상 데이터 흐름 1":::
    
3. 5자로 제한된 싱크 열에 동영상 제목이 모두 들어가지 않는다는 문제가 있습니다. 이 데이터 흐름을 실행하면 다음과 같은 오류가 표시됩니다.```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

이 동영상은 데이터 흐름에서 오류 행 처리 논리를 설정하는 예제를 소개합니다.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4uOHj]

## <a name="how-to-design-around-this-condition"></a>이 조건을 중심으로 디자인하는 방법

1. 이 시나리오에서 ‘이름’ 열의 최대 길이는 5자입니다. 따라서 5자보다 긴 ‘제목’을 사용하여 행을 로그할 수 있도록 하는 조건부 분할 변환을 추가하고 해당 공간에 맞출 수 있는 나머지 행도 데이터베이스에 쓸 수 있도록 합니다.

    :::image type="content" source="media/data-flow/error1.png" alt-text="조건부 분할":::

2. 이 조건부 분할 변환은 ‘제목’의 최대 길이를 5자로 정의합니다. 5자보다 짧거나 같은 행은 ```GoodRows``` 스트림으로 이동합니다. 5자보다 긴 행은 ```BadRows``` 스트림으로 이동합니다.

3. 이제 실패한 행을 기록해야 합니다. ```BadRows```스트림에 대한 싱크 변환을 스트림에 추가합니다. 여기에서는 전체 트랜잭션 기록의 로깅을 사용할 수 있도록 모든 필드를 ‘자동 매핑’합니다. Blob Storage의 단일 파일에 대한 문자로 구분된 CSV 파일 출력입니다. 로그 파일 ‘badrows.csv’를 호출합니다.

    :::image type="content" source="media/data-flow/error3.png" alt-text="잘못된 행":::
    
4. 완료된 데이터 흐름은 다음과 같습니다. 이제 SQL 잘림 오류를 방지하고 해당 항목을 로그 파일에 저장하기 위해 오류 행을 분할할 수 있습니다. 한편 성공한 행은 대상 데이터베이스에 계속 쓸 수 있습니다.

    :::image type="content" source="media/data-flow/error2.png" alt-text="완료 데이터 흐름":::

5. 싱크 변환에서 오류 행 처리 옵션을 선택하고 ‘출력 오류 행’을 설정하는 경우 ADF는 드라이버에서 보고한 오류 메시지와 함께 행 데이터의 CSV 파일 출력을 자동으로 생성합니다. 해당 대체 옵션을 사용하여 데이터 흐름에 해당 논리를 수동으로 추가할 필요는 없습니다. 이 옵션을 사용할 경우 약간의 성능 저하가 발생하므로 ADF는 오류를 트래핑하고 로그하는 2단계 방법을 구현할 수 있습니다.

    :::image type="content" source="media/data-flow/error-row-3.png" alt-text="오류 행이 있는 완료 데이터 흐름":::

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름 [변환](concepts-data-flow-overview.md)을 사용하여 나머지 데이터 흐름 논리를 빌드합니다.