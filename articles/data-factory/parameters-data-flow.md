---
title: 매핑 데이터 흐름 매개 변수화
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 매핑 데이터 흐름을 매개 변수화하는 방법 알아보기
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: d01e1ca8d3eb0ca2e345d42118ad5f1463c3139f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750472"
---
# <a name="parameterizing-mapping-data-flows"></a>매핑 데이터 흐름 매개 변수화

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)] 

Azure Data Factory 및 Synapse 파이프라인의 매핑 데이터 흐름은 매개 변수 사용을 지원합니다. 데이터 흐름 정의 내에서 매개 변수를 정의하고 식 전체에서 사용합니다. 매개 변수 값은 데이터 흐름 실행 작업을 통해 호출 파이프라인에 의해 설정됩니다. 데이터 흐름 활동 식의 값을 설정하는 세 가지 옵션이 있습니다.

* 파이프라인 제어 흐름 식 언어를 사용하여 동적 값 설정
* 데이터 흐름 식 언어를 사용하여 동적 값 설정
* 식 언어를 사용하여 정적 리터럴 값 설정

이 기능을 사용하여 데이터 흐름을 범용적이고 유연하며 재사용 가능하게 만듭니다. 이러한 매개 변수를 사용하여 데이터 흐름 설정 및 식을 매개 변수화할 수 있습니다.

## <a name="create-parameters-in-a-mapping-data-flow"></a>매핑 데이터 흐름에서 매개 변수 만들기

데이터 흐름에 매개 변수를 추가하려면 데이터 흐름 캔버스의 빈 부분을 클릭하여 일반 속성을 표시합니다. 설정 창에 **매개 변수** 라는 탭이 표시됩니다. **새로 만들기** 를 선택하여 새 매개 변수를 생성합니다. 각 매개 변수에 대해 이름을 지정하고, 형식을 선택하며, 선택적으로 기본값을 설정해야 합니다.

:::image type="content" source="media/data-flow/create-params.png" alt-text="데이터 흐름 매개 변수 만들기":::

## <a name="use-parameters-in-a-mapping-data-flow"></a>매핑 데이터 흐름에서 매개 변수 사용 

모든 데이터 흐름 식에서 매개 변수를 참조할 수 있습니다. 매개 변수는 $로 시작되며 변경할 수 없습니다. 식 작성기 내에서 사용할 수 있는 매개 변수 목록은 **매개 변수** 탭에서 찾을 수 있습니다.

:::image type="content" source="media/data-flow/parameter-expression.png" alt-text="스크린샷은 매개 변수 탭에서 사용 가능한 매개 변수를 보여 줍니다.":::

**새 매개 변수** 를 선택하고 이름 및 유형을 지정하여 추가 매개 변수를 빠르게 추가할 수 있습니다.

:::image type="content" source="media/data-flow/new-parameter-expression.png" alt-text="스크린샷은 새 매개 변수가 추가된 매개 변수 탭의 매개 변수를 보여 줍니다.":::

## <a name="assign-parameter-values-from-a-pipeline"></a>파이프라인에서 매개 변수 값 할당

매개 변수가 있는 데이터 흐름을 만든 후에는 데이터 흐름 실행 작업을 사용하여 파이프라인에서 실행할 수 있습니다. 파이프라인 캔버스에 활동을 추가하면 활동의 **매개 변수** 탭에서 사용 가능한 데이터 흐름 매개 변수가 표시됩니다.

매개 변수 값을 할당하는 경우 spark 유형을 기반으로 하는 [파이프라인 식 언어](control-flow-expression-language-functions.md) 또는 [데이터 흐름 식 언어](data-flow-expression-functions.md)를 사용할 수 있습니다. 각 매핑 데이터 흐름에는 파이프라인 및 데이터 흐름 식 매개 변수의 조합이 있을 수 있습니다.

:::image type="content" source="media/data-flow/parameter-assign.png" alt-text="스크린샷은 myparam 값에 대해 데이터 흐름 식이 선택된 매개 변수 탭을 보여 줍니다.":::

### <a name="pipeline-expression-parameters"></a>파이프라인 식 매개 변수

파이프라인 식 매개 변수를 사용하면 다른 파이프라인 작업과 유사한 시스템 변수, 함수, 파이프라인 매개 변수 및 변수를 참조할 수 있습니다. **파이프라인 식** 을 클릭하면 식 작성기를 사용하여 식을 입력할 수 있는 측면 탐색이 열립니다.

:::image type="content" source="media/data-flow/parameter-pipeline.png" alt-text="스크린샷은 식 작성기 창을 보여 줍니다.":::

참조되면 파이프라인 매개 변수가 평가되고 해당 값이 데이터 흐름 식 언어로 사용됩니다. 파이프라인 식 유형이 데이터 흐름 매개 변수 유형과 일치하지 않아도 됩니다. 

#### <a name="string-literals-vs-expressions"></a>문자열 리터럴 및 식

문자열 유형의 파이프라인 표현식 매개 변수를 할당할 때 기본적으로 따옴표가 추가되고 값은 리터럴로 평가됩니다. 매개 변수 값을 데이터 흐름 식으로 읽으려면 매개 변수 옆의 식 상자를 선택합니다.

:::image type="content" source="media/data-flow/string-parameter.png" alt-text="스크린샷은 매개 변수에 대해 선택된 데이터 흐름 매개 변수 창 식을 보여 줍니다.":::

데이터 흐름 매개 변수 `stringParam`이 값이 `upper(column1)`인 파이프라인 매개 변수를 참조하는 경우. 

- 식이 선택되면 `$stringParam`은 column1의 값을 모두 대문자로 평가합니다.
- 식이 선택되지 않으면(기본 동작) `$stringParam`은 `'upper(column1)'`로 평가됩니다.

#### <a name="passing-in-timestamps"></a>타임스탬프 전달

파이프라인 식 언어에서 `pipeline().TriggerTime`과 같은 시스템 변수 및 `utcNow()`와 같은 함수는 'yyyy-MM-dd\'T\'HH:mm:ss.SSSSSSZ' 형식의 문자열로 타임스탬프를 반환합니다. 이를 타임스탬프 유형의 데이터 흐름 매개 변수로 변환하려면 문자열 보간을 사용하여 `toTimestamp()` 함수에 원하는 타임스탬프를 포함합니다. 예를 들어 파이프라인 트리거 시간을 데이터 흐름 매개 변수로 변환하려면 `toTimestamp(left('@{pipeline().TriggerTime}', 23), 'yyyy-MM-dd\'T\'HH:mm:ss.SSS')`를 사용할 수 있습니다. 

:::image type="content" source="media/data-flow/parameter-timestamp.png" alt-text="스크린샷은 트리거 시간을 입력할 수 있는 매개 변수 탭을 보여 줍니다.":::

> [!NOTE]
> 데이터 흐름은 최대 3밀리초까지만 지원할 수 있습니다. `left()` 함수는 추가 자릿수를 잘라내는 데 사용됩니다.

#### <a name="pipeline-parameter-example"></a>파이프라인 매개 변수 예

문자열 유형 `@pipeline.parameters.pipelineParam`의 파이프라인 매개 변수를 참조하는 정수 매개 변수 `intParam`이 있다고 가정합니다. 

:::image type="content" source="media/data-flow/parameter-pipeline-2.png" alt-text="스크린샷은 stringParam 및 intParam이라는 매개 변수가 있는 매개 변수 탭을 보여 줍니다.":::

`@pipeline.parameters.pipelineParam`에는 런타임에 `abs(1)` 값이 지정됩니다.

:::image type="content" source="media/data-flow/parameter-pipeline-4.png" alt-text="스크린샷은 a b s (1) 값이 선택된 매개 변수 탭을 보여 줍니다.":::

`$intParam`이 파생된 열과 같은 식에서 참조되면 `abs(1)`이 `1`을 반환합니다. 

:::image type="content" source="media/data-flow/parameter-pipeline-3.png" alt-text="스크린샷은 열 값을 보여 줍니다.":::

### <a name="data-flow-expression-parameters"></a>데이터 흐름 식 매개 변수

**데이터 흐름 식** 을 선택하면 데이터 흐름 식 작성기가 열립니다. 데이터 흐름 전체에서 함수, 기타 매개 변수 및 정의된 스키마 열을 참조할 수 있습니다. 이 식은 참조될 때 그대로 평가됩니다.

> [!NOTE]
> 잘못된 식을 전달하거나 해당 변환에 존재하지 않는 스키마 열을 참조하는 경우 매개 변수는 null로 평가됩니다.


### <a name="passing-in-a-column-name-as-a-parameter"></a>열 이름을 매개 변수로 전달

일반적인 패턴은 열 이름을 매개 변수 값으로 전달하는 것입니다. 열이 데이터 흐름 스키마에 정의되어 있는 경우 이를 문자열 식으로 직접 참조할 수 있습니다. 열이 스키마에 정의되어 있지 않은 경우 `byName()` 함수를 사용합니다. `toString()`과 같은 캐스트 함수를 사용하여 열을 적절한 유형으로 캐스트해야 합니다.

예를 들어, 매개 변수 `columnName`을 기반으로 문자열 열을 매핑하려는 경우 `toString(byName($columnName))`과 동일한 파생 열 변환을 추가할 수 있습니다.

:::image type="content" source="media/data-flow/parameterize-column-name.png" alt-text="열 이름을 매개 변수로 전달":::

## <a name="next-steps"></a>다음 단계
* [데이터 흐름 실행 작업](control-flow-execute-data-flow-activity.md)
* [제어 흐름 식](control-flow-expression-language-functions.md)
