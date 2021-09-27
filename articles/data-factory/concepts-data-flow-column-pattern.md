---
title: 매핑 데이터 흐름의 열 패턴
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 또는 Synapse Analytics를 사용 하 여 데이터 흐름을 매핑하는 열 패턴을 사용 하 여 일반화 된 데이터 변환 패턴을 만듭니다.
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 32d39c956121881da0073b53fe5b4196dbc179de
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828487"
---
# <a name="using-column-patterns-in-mapping-data-flow"></a>매핑 데이터 흐름의 열 패턴 사용

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

여러 매핑 데이터 흐름 변환을 사용하면 하드 코드된 열 이름 대신 패턴을 기반으로 하는 템플릿 열을 참조할 수 있습니다. 이러한 일치를 *열 패턴* 이라고 합니다. 정확한 필드 이름을 요구하는 대신 이름, 데이터 형식, 스트림, 원점 또는 위치에 따라 열을 일치 시키는 패턴을 정의할 수 있습니다. 열 패턴이 유용한 두 가지 시나리오는 다음과 같습니다.

* 들어오는 원본 필드가 텍스트 파일 또는 NoSQL 데이터베이스에서 열을 변경하는 경우와 같이 자주 변경되는 경우. 이 시나리오를 [스키마 드리프트](concepts-data-flow-schema-drift.md)라고 합니다.
* 큰 열 그룹에서 일반적인 작업을 수행 하려는 경우. 예를 들어, 열 이름에 ' total '이 포함된 모든 열을 double로 캐스팅하려고 합니다.

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4Iui1]

## <a name="column-patterns-in-derived-column-and-aggregate"></a>파생 열 및 집계의 열 패턴

파생 열, 집계 또는 창 변환에서 열 패턴을 추가하려면, 열 목록 위에 있는 **추가** 를 클릭하거나 기존 파생 열 옆에 있는 더하기 아이콘을 클릭합니다. **열 패턴 추가** 를 선택합니다.

:::image type="content" source="media/data-flow/add-column-pattern.png" alt-text="스크린샷은 열 패턴을 추가하기 위한 더하기 아이콘을 보여줍니다.":::

[식 작성기](concepts-data-flow-expression-builder.md)를 사용하여 일치 조건을 입력합니다. 해당 열의 `name`, `type`, `stream`, `origin`, 및 `position`을 기준으로 하는 열들과 일치하는 부울 식을 만듭니다. 이 패턴은 조건이 true를 반환하는 경우 드리프트되거나 정의되는 열에 영향을 줍니다.


:::image type="content" source="media/data-flow/edit-column-pattern.png" alt-text="스크린샷은 파생 열의 설정 탭을 보여 줍니다.":::

위의 열 패턴은 double 형식의 모든 열을 일치 시키고, 일치하는 항목 마다 하나의 파생 열을 만듭니다. `$$`을 열 이름 필드로 사용함으로써 일치하는 각 열이 같은 이름으로 업데이트됩니다. 각 열의 값은 소수점 이하 두 자리까지 반올림된 기존 값입니다.

일치 조건이 올바른지 확인하기 위해, **검사** 탭에서 정의된 열의 출력 스키마의 유효성을 검사하거나 **데이터 미리 보기** 탭에서 데이터의 스냅샷을 가져올 수 있습니다. 

:::image type="content" source="media/data-flow/columnpattern3.png" alt-text="스크린샷은 출력 스키마 탭을 표시합니다.":::

### <a name="hierarchical-pattern-matching"></a>계층 패턴 일치

복합 계층 구조 내에서도 패턴 일치를 빌드할 수 있습니다. 데이터 스트림의 각 계층에 대한 메시지가 표시되는 섹션 `Each MoviesStruct that matches`을 확장합니다. 그런 다음 선택한 계층 내에서 속성에 대해 일치하는 패턴을 빌드할 수 있습니다.

:::image type="content" source="media/data-flow/patterns-hierarchy.png" alt-text="스크린샷은 계층적 열 패턴을 보여 줍니다.":::

## <a name="rule-based-mapping-in-select-and-sink"></a>선택 및 싱크의 규칙 기반 매핑

원본에서 열을 매핑하고 변환을 선택하는 경우, 고정 매핑 또는 규칙 기반 매핑을 추가할 수 있습니다. 열들의 `name`, `type`, `stream`, `origin`, 및 `position`을 기준으로 일치 시킵니다. 고정 및 규칙 기반 매핑의 모든 조합을 사용할 수 있습니다. 기본값으로 50개 이상의 열을 포함하는 모든 프로젝션은 모든 열에서 일치하는 규칙 기반 매핑으로 기본설정되며, 입력 이름을 출력합니다. 

규칙 기반 매핑을 추가하려면, **매핑 추가** 를 클릭하고 **규칙 기반 매핑** 을 선택합니다.

:::image type="content" source="media/data-flow/rule2.png" alt-text="스크린샷은 매핑 추가에서 선택한 규칙 기반 매핑을 보여 줍니다.":::

각 규칙 기반 매핑에는 일치 조건과 각 매핑된 열의 이름을 입력해야 합니다. 두 값은 모두 [식 작성기](concepts-data-flow-expression-builder.md)를 통해 입력됩니다. 왼쪽 식 상자에 부울 일치 조건을 입력합니다. 오른쪽 식 상자에서 일치하는 열을 매핑할 대상을 지정합니다.

:::image type="content" source="media/data-flow/rule-based-mapping.png" alt-text="스크린샷은 매핑을 보여 줍니다.":::

`$$` 구문을 사용하여 일치하는 열의 입력 이름을 참조합니다. 위의 이미지를 예로 들 수 있습니다. 예를 들어, 사용자는 이름이 6개 문자보다 짧은 모든 문자열 열에 대해 일치시키려 합니다. 하나의 들어오는 열의 이름이 `test`로 지정된 경우, 식 `$$ + '_short'`에서 열 `test_short`의 이름을 바꿉니다. 이 매핑이 사용할 수 있는 유일한 매핑이면, 해당 조건을 충족하지 않는 모든 열이 출력된 데이터에서 삭제됩니다.

패턴은 드리프트된 열과 정의된 열 모두와 일치합니다. 규칙에 따라 매핑되는 정의된 열을 확인하려면, 규칙 옆에 있는 안경 아이콘을 클릭합니다. 데이터 미리 보기를 사용하여 출력을 확인합니다.

### <a name="regex-mapping"></a>Regex 매핑

아래쪽 펼침 단추 아이콘을 클릭하면, regex 매핑 조건을 지정할 수 있습니다. Regex 매핑 조건은 지정된 regex 조건과 일치하는 모든 열 이름과 일치합니다. 이것을 표준 규칙 기반 매핑과 함께 사용할 수 있습니다.

:::image type="content" source="media/data-flow/regex-matching.png" alt-text="스크린샷은 계층 수준 및 이름이 일치하는 regex 매핑 조건을 보여 줍니다.":::

위의 예제는 regex 패턴 `(r)`이나 소문자 r을 포함하는 모든 열 이름에서 일치합니다. 표준 규칙 기반 매핑과 유사하게, 일치하는 모든 열은 `$$` 구문을 사용하여 오른쪽의 조건에 따라 변경됩니다.

### <a name="rule-based-hierarchies"></a>규칙 기반 계층

정의된 프로젝션에 계층이 있는 경우, 규칙 기반 매핑을 사용하여 계층 하위 열을 매핑할 수 있습니다. 일치 조건 및 하위 열을 매핑하려는 복합 열을 지정합니다. 오른쪽에 지정된 규칙으로서 ‘이름’을 사용하여 일치하는 모든 하위 열이 출력됩니다.

:::image type="content" source="media/data-flow/rule-based-hierarchy.png" alt-text="스크린샷은 계층 구조에 대해를 사용하는 규칙 기반 매핑을 보여 줍니다.":::

위의 예는 복합 열의 모든 하위 열 `a`에서 일치합니다. `a`은 하위 열 `b`와 `c`을 포함합니다. 조건으로서 '이름'이 `$$`이되므로, 출력 스키마는 두 개의 열 `b`과 `c`를 포함합니다.

## <a name="pattern-matching-expression-values"></a>패턴 일치 식 값.

* `$$`은 런타임에 각 일치 항목의 이름이나 값으로 변환합니다. `this`와 동등한 것으로 `$$`을 간주합니다.
* `name`은 들어오는 각 열의 이름을 나타냅니다.
* `type`은 수신되는 각 열의 데이터 형식을 나타냅니다. 데이터 흐름 형식 시스템의 데이터 형식 목록은 [여기](concepts-data-flow-overview.md#data-flow-data-types)에서 찾을 수 있습니다.
* `stream`은 각 스트림 또는 흐름의 변환과 관련된 이름을 나타냅니다.
* `position`은 데이터 흐름에서 열의 서수 위치입니다.
* `origin`은 열이 시작 되거나 마지막으로 업데이트된 변환입니다.

## <a name="next-steps"></a>다음 단계
* 데이터 변환에 대한 매핑 데이터 흐름 [식 언어](data-flow-expression-functions.md)에 대해 자세히 알아보기
* [싱크 변환](data-flow-sink.md)에서 열 패턴을 사용하고, 규칙 기반 매핑으로 [변환 선택](data-flow-select.md)을 사용합니다.
