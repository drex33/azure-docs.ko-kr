---
title: 변수 작업 추가
titleSuffix: Azure Data Factory & Azure Synapse
description: 변수 추가 작업을 설정 하 여 Data Factory 또는 Synapse Analytics 파이프라인에 정의 된 기존 배열 변수에 값을 추가 하는 방법에 대해 알아봅니다.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: f3dfff9505ab40e7bd751e7ade8fa697241e6f85
ms.sourcegitcommit: b69ce103ff31805cf2002b727670db9452ef8518
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/05/2021
ms.locfileid: "133570477"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics의 추가 변수 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
변수 추가 작업을 사용 하 여 Data Factory 또는 Synapse Analytics 파이프라인에 정의 된 기존 배열 변수에 값을 추가 합니다.

## <a name="create-an-append-variable-activity-with-ui"></a>UI를 사용 하 여 추가 변수 작업 만들기

파이프라인에서 추가 변수 작업을 사용 하려면 다음 단계를 완료 합니다.

1. 파이프라인 캔버스의 배경을 선택 하 고 변수 탭을 사용 하 여 배열 유형 변수를 추가 합니다.

   :::image type="content" source="media/control-flow-append-variable-activity/add-pipeline-array-variable.png" alt-text="TestVariable 이라는 배열 유형 변수를 사용 하 여 변수 탭이 선택 된 빈 파이프라인 캔버스를 표시 합니다.":::

2. 파이프라인 활동 창에서 _추가 변수_ 를 검색 하 고 변수 추가 활동을 파이프라인 캔버스로 끌어 옵니다.
1. Canvas에서 변수 추가 활동을 선택 하지 않은 경우 해당 활동을 선택 하 고 해당  **변수** 탭을 선택 하 여 세부 정보를 편집 합니다.
1. Name 속성에 대 한 변수를 선택 합니다.
1. 변수의 배열에 추가 될 값에 대 한 식을 입력 합니다.  리터럴 문자열 식 이거나 동적 [식, 함수](control-flow-expression-language-functions.md), [시스템 변수](control-flow-system-variables.md)또는 [다른 작업의 출력](how-to-expression-language-functions.md#examples-of-using-parameters-in-expressions)을 조합 하 여 사용할 수 있습니다.

   :::image type="content" source="media/control-flow-append-variable-activity/append-variable.png" alt-text="각 단계를 구성 하는 번호가 매겨진 전환 작업에 대 한 UI를 표시 합니다.":::

## <a name="type-properties"></a>형식 속성

속성 | 설명 | 필수
-------- | ----------- | --------
이름 | 파이프라인의 작업 이름 | 예
Description | 작업이 어떤 일을 수행하는지 설명하는 텍스트 | 아니요
형식 | 활동 유형은 AppendVariable입니다. | 예
값 | 지정된 변수에 추가하는 데 사용되는 문자열 리터럴 또는 표현식 개체 값 | 예
VariableName | 활동별로 수정될 변수의 이름, 변수는 ‘Array’ 유형이어야 합니다. | 예

## <a name="next-steps"></a>다음 단계
관련 제어 흐름 작업에 대해 알아봅니다. 

- [변수 설정 작업](control-flow-set-variable-activity.md)
