---
title: 매핑 데이터 흐름에 파생된 열 변환
titleSuffix: Azure Data Factory & Azure Synapse
description: 매핑 데이터 흐름 파생 열 변환을 사용하여 Azure Data Factory 및 Azure Synapse Analytics에서 대규모로 데이터를 변환하는 방법을 알아봅니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 51656330a0d849ed61d7f1cae88f7bbfc0610c49
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058788"
---
# <a name="derived-column-transformation-in-mapping-data-flow"></a>데이터 흐름 매핑의 파생된 열 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[!INCLUDE[data-flow-preamble](includes/data-flow-preamble.md)]

파생 열 변환을 사용하여 데이터 흐름에 새 열을 생성하거나 기존 필드를 수정합니다.

## <a name="create-and-update-columns"></a>열 만들기 및 업데이트

파생 열을 만드는 경우 새 열을 생성하거나 기존 열을 업데이트할 수 있습니다. **열** 텍스트 상자에 만들려는 열을 입력합니다. 스키마의 기존 열을 재정의하려면 열 드롭다운을 사용할 수 있습니다. 파생 열의 식을 작성하려면 **식 입력** 텍스트 상자를 클릭합니다. 식 입력을 시작하거나 식 작성기를 열어 논리를 생성할 수 있습니다.

:::image type="content" source="media/data-flow/create-derive-column.png" alt-text="파생 열 설정":::

파생 열을 더 추가하려면 열 목록 위의 **추가** 를 클릭하거나 기존 파생 열 옆에 있는 더하기 아이콘을 클릭합니다. **열 추가** 또는 **열 패턴 추가** 를 선택합니다.

:::image type="content" source="media/data-flow/add-derived-column.png" alt-text="새 파생 열 선택":::

### <a name="column-patterns"></a>열 패턴

스키마가 명시적으로 정의되지 않았거나 열 집합을 대량으로 업데이트하려는 경우 열 패턴을 만드는 것이 좋습니다. 열 패턴을 사용하면 열 메타데이터에 따라 규칙을 통해 열을 일치시키고 일치하는 각 열의 파생 열을 만들 수 있습니다. 자세한 내용은 파생 열 변환에서 [열 패턴을 작성하는 방법](concepts-data-flow-column-pattern.md#column-patterns-in-derived-column-and-aggregate)을 참조하세요.

:::image type="content" source="media/data-flow/column-pattern-derive.png" alt-text="열 패턴":::

## <a name="building-schemas-using-the-expression-builder"></a>식 작성기를 사용하여 스키마 작성

매핑 데이터 흐름 [식 작성기](concepts-data-flow-expression-builder.md)를 사용하는 경우 **파생 열** 섹션에서 파생 열을 만들고 편집하고 관리할 수 있습니다. 변환에서 생성 또는 변경된 모든 열이 나열됩니다. 열 이름을 클릭하여 편집할 열이나 패턴을 대화형으로 선택합니다. 열을 더 추가하려면 **새로 만들기** 를 선택한 다음, 단일 열 또는 패턴을 추가할지를 선택합니다.

:::image type="content" source="media/data-flow/derive-add-column.png" alt-text="새 열 만들기":::

복합 열로 작업하는 경우 하위 열을 만들 수 있습니다. 이렇게 하려면 아무 열이나 옆에 있는 더하기 아이콘을 클릭하고 **하위 열 추가** 를 선택합니다. 데이터 흐름의 복합 형식을 처리하는 방법에 대한 자세한 내용은 [매핑 데이터 흐름의 JSON 처리](format-json.md#mapping-data-flow-properties)를 참조하세요.

:::image type="content" source="media/data-flow/derive-add-subcolumn.png" alt-text="하위 열 추가":::

데이터 흐름의 복합 형식을 처리하는 방법에 대한 자세한 내용은 [매핑 데이터 흐름의 JSON 처리](format-json.md#mapping-data-flow-properties)를 참조하세요.

:::image type="content" source="media/data-flow/derive-complex-column.png" alt-text="복합 열 추가":::

### <a name="locals"></a>로컬

여러 열에서 논리를 공유하고 있거나 논리를 구분하려는 경우 파생 열 변환 내에서 로컬을 만들 수 있습니다. 로컬은 다음 변환으로 다운스트림 전파되지 않는 논리 집합입니다. 식 작성기 내에서 **식 요소** 로 이동한 다음 **로컬** 을 선택하면 로컬을 만들 수 있습니다. **새로 만들기** 를 선택하여 새 로컬을 만듭니다.

:::image type="content" source="media/data-flow/create-local.png" alt-text="로컬 만들기":::

로컬은 함수, 입력 스키마, 매개 변수, 기타 로컬을 포함하여 파생 열의 모든 식 요소를 참조할 수 있습니다. 다른 로컬을 참조하는 경우 참조된 로컬이 현재 로컬 “위”에 있어야 하므로 순서가 중요합니다.

:::image type="content" source="media/data-flow/create-local-2.png" alt-text="로컬 만들기 2":::

파생 열에서 로컬을 참조하려면 **식 요소** 뷰에서 로컬을 클릭하거나 이름 앞에 콜론을 사용하여 로컬을 참조합니다. 예를 들어 local1이라는 로컬은 `:local1`로 참조됩니다. 로컬 정의를 편집하려면 식 요소 뷰에서 해당 정의를 마우스로 가리키고 연필 아이콘을 클릭합니다.

:::image type="content" source="media/data-flow/using-locals.png" alt-text="로컬 사용":::

## <a name="data-flow-script"></a>데이터 흐름 스크립트

### <a name="syntax"></a>구문

```
<incomingStream>
    derive(
           <columnName1> = <expression1>,
           <columnName2> = <expression2>,
           each(
                match(matchExpression),
                <metadataColumn1> = <metadataExpression1>,
                <metadataColumn2> = <metadataExpression2>
               )
          ) ~> <deriveTransformationName>
```

### <a name="example"></a>예제

아래 예제는 들어오는 스트림 `MoviesYear`를 사용하고 두 개의 파생 열을 만드는 `CleanData`라는 파생 열입니다. 첫 번째 파생 열은 `Rating` 열을 정수 형식의 등급 값으로 바꿉니다. 두 번째 파생 열은 이름이 'movies'로 시작하는 각 열과 일치하는 패턴입니다. 일치하는 각 열에 대해 일치하는 열의 값에 'movie_' 접두사를 붙인 값과 같은 `movie` 열을 만듭니다. 

UI에서 이 변환은 아래 이미지와 같습니다.

:::image type="content" source="media/data-flow/derive-script.png" alt-text="파생 예제":::

이 변환에 대한 데이터 흐름 스크립트는 아래 코드 조각에 있습니다.

```
MoviesYear derive(
                Rating = toInteger(Rating),
                each(
                    match(startsWith(name,'movies')),
                    'movie' = 'movie_' + toString($$)
                )
            ) ~> CleanData
```

## <a name="next-steps"></a>다음 단계

- [매핑 데이터 흐름 식 언어](data-flow-expression-functions.md)에 대해 자세히 알아봅니다.
