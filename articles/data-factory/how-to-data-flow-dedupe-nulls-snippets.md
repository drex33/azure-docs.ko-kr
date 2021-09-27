---
title: 데이터 흐름 코드 조각을 사용하여 행 중복 제거 및 Null 검색
description: 데이터 흐름에서 코드 조각을 사용하여 손쉽게 행 중복을 제거하고 Null을 찾는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: 7940d48edb94bfa89ccc3310172a09519ffc729a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124831362"
---
# <a name="dedupe-rows-and-find-nulls-by-using-data-flow-snippets"></a>데이터 흐름 코드 조각을 사용하여 행 중복 제거 및 Null 검색

[!INCLUDE [appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

매핑 데이터 흐름에서 코드 조각을 사용하여 데이터 중복 제거 및 Null 필터링과 같은 일반적인 작업을 손쉽게 수행할 수 있습니다. 이 문서에서는 데이터 흐름 스크립트 코드 조각을 사용하여 파이프라인에 이러한 함수를 쉽게 추가하는 방법을 설명합니다.
<br>
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4GnhH]

## <a name="create-a-pipeline"></a>파이프라인 만들기

1. **새 파이프라인** 을 선택합니다.

1. 데이터 흐름 작업을 추가합니다.

1. **원본 설정** 탭을 선택하고 원본 변환을 추가한 다음 데이터 세트 중 하나에 연결합니다.

    :::image type="content" source="media/data-flow/snippet-adf-2.png" alt-text="&quot;원본 유형을 추가하기 위한 원본 설정 &quot; 창의 스크린샷.":::

    중복 제거 및 Null 검사 코드 조각은 데이터 흐름 스키마 드리프트를 활용하는 일반 패턴을 사용합니다. 이 코드 조각은 데이터 세트의 스키마 또는 미리 정의된 스키마가 없는 데이터 세트에 사용할 수 있습니다.

1. [DFS(데이터 흐름 스크립트)](./data-flow-script.md#distinct-row-using-all-columns)의 '모든 열을 사용하는 고유 행' 섹션에서 DistinctRows용 코드 조각을 복사합니다.

1. [데이터 흐름 스크립트 설명서 페이지로 이동하여 Distinct Rows용 코드 조각을 복사합니다.](./data-flow-script.md#distinct-row-using-all-columns)

    :::image type="content" source="media/data-flow/snippet-adf-3.png" alt-text="소스 코드 조각의 스크린샷":::

1. 스크립트의 `source1` 정의 뒤에서 Enter 키를 누른 다음 코드 조각을 붙여넣습니다.

1. 다음 중 하나를 수행합니다.

   * 붙여넣은 코드 앞에 **source1** 를 입력하여 이 붙여넣은 코드 조각을 앞서 그래프에서 만든 원본 변환에 연결합니다.

   * 또는 그래프의 새 변환 노드에서 들어오는 스트림을 선택하여 디자이너에서 새 변환을 연결할 수 있습니다.

     :::image type="content" source="media/data-flow/snippet-adf-4.png" alt-text="&quot;조건부 분할 설정 &quot; 창의 스크린샷.":::

   이제 데이터 흐름은 모든 열 값에서 일반 해시를 사용하여 모든 행을 기준으로 그룹화하는 집계 변환을 사용하여 원본에서 중복 행을 제거합니다.
    
1. 데이터를 Null이 포함된 행의 스트림과 Null이 포함되지 않은 다른 스트림으로 분할하는 코드 조각을 추가합니다. 이를 수행하려면:

1. [코드 조각 라이브러리로 돌아가서 이번에는 NULL 검사용 코드를 복사합니다.](./data-flow-script.md#check-for-nulls-in-all-columns)

   b. 데이터 흐름 디자이너에서 **스크립트** 를 다시 선택하고 맨 아래에 이 새 변환 코드를 붙여넣습니다. 이 작업은 붙여넣은 코드 조각 앞에 해당 변환의 이름을 배치하여 스크립트를 이전 변환에 연결합니다.

   이제 데이터 흐름 그래프가 다음과 같이 표시됩니다.

    :::image type="content" source="media/data-flow/snippet-adf-1.png" alt-text="데이터 흐름 그래프의 스크린샷":::

  이제 데이터 흐름 스크립트 라이브러리에서 기존 코드 조각을 가져오고 기존 디자인에 추가하여 일반 중복 제거 및 Null 검사가 포함된 작동하는 데이터 흐름을 만들었습니다.

## <a name="next-steps"></a>다음 단계

* 매핑 데이터 흐름 [변환](concepts-data-flow-overview.md)을 사용하여 나머지 데이터 흐름 논리를 빌드합니다.