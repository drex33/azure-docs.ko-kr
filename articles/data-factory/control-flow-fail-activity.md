---
title: Azure Data Factory 및 Synapse Analytics 실패 작업 실행
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 문서에서는 Azure Data Factory 및 Synapse Analytics 실패한 활동이 파이프라인에서 의도적으로 오류를 throw하는 방법을 설명합니다.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 4e64c6153d116968b7e2bdcb6a43daa8f5a35f3d
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132808340"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics 실패 작업 실행

파이프라인에서 의도적으로 오류를 throw하려는 경우도 있습니다. [조회 작업은](control-flow-lookup-activity.md) 일치하는 데이터를 반환하지 않거나 [사용자 지정 활동이](transform-data-using-dotnet-custom-activity.md) 내부 오류로 완료될 수 있습니다. 이유가 무엇이든, 이제 파이프라인에서 실패 활동을 사용하고 오류 메시지와 오류 코드를 모두 사용자 지정할 수 있습니다.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>구문

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "errorCode": "500",
        "message": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>형식 속성

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| name | 실패 작업의 이름입니다. | String | 예 |
| type | **실패** 로 설정해야 합니다. | String | 예 |
| message | 실패 활동에 표시된 오류 메시지입니다. 런타임에 평가되는 동적 콘텐츠일 수 있습니다. | String | 예 |
| errorCode | 실패 활동의 오류 유형을 분류하는 오류 코드입니다. 런타임에 평가되는 동적 콘텐츠일 수 있습니다. | String | 예 |
| | |

## <a name="understand-the-fail-activity-error-code"></a>실패 활동 오류 코드 이해

실패 작업의 오류 메시지 및 오류 코드는 일반적으로 사용자가 설정합니다. 오류 코드의 특정 의미를 이해하려면 파이프라인 개발자에게 문의하세요. 그러나 다음 에지 사례에서 Azure Data Factory 오류 메시지 및/또는 오류 코드를 설정합니다.

| 상황 설명 | 오류 메시지 | 오류 코드 |
| --- | --- | --- |
및 의 (동적) `message` `errorCode` 콘텐츠가 올바르게 해석됩니다. | 사용자가 설정한 오류 메시지 | 사용자가 설정한 오류 코드 |
및 의 동적 콘텐츠는 `message` `errorCode` 해석할 수 없습니다. | "오류 메시지 또는 오류 코드를 _해석하지<activity_name>"_ | `ErrorCodeNotString` |
| 의 동적 콘텐츠는 `message` 문자열로 해석할 수 없습니다. | _"<activity_name>_ 오류 메시지 매개 변수를 문자열로 해석할 수 없습니다." | 사용자가 설정한 오류 코드 |
| 의 동적 콘텐츠는 `message` null, 빈 문자열 또는 공백으로 확인됩니다. | "오류 메시지 또는 오류 코드를 _해석하지<activity_name>"_ | 사용자가 설정한 오류 코드 |
| 의 동적 콘텐츠는 `errorCode` 문자열로 해석할 수 없습니다. | 사용자가 설정한 오류 메시지 | `ErrorCodeNotString` |
| 의 동적 콘텐츠는 `errorCode` null, 빈 문자열 또는 공백으로 확인됩니다. | 사용자가 설정한 오류 메시지 | `ErrorCodeNotString` |
| 사용자가 제공한 또는 의 값은 `message` `errorCode` 문자열을 사용할 수 없습니다.* | "속성 <> 잘못된 값"으로 _파이프라인이_ `errorCode` / `message` 실패합니다. | |
| `message`필드가 없습니다.* | "실패 메시지가 제공되지 않았습니다." | 사용자가 설정한 오류 코드 |
| `errorCode`필드가 없습니다.* | 사용자가 설정한 오류 메시지 | `ErrorCodeNotString` |
| | |

\* 파이프라인이 Data Factory 웹 UI(사용자 인터페이스)를 통해 개발된 경우에는 이 상황이 발생하지 않아야 합니다.

## <a name="next-steps"></a>다음 단계

다음을 포함하여 지원되는 다른 제어 흐름 활동을 참조하세요.

- [If Condition 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [For Each 작업](control-flow-for-each-activity.md)
- [메타데이터 가져오기 작업](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
- [파이프라인 오류 이해](tutorial-pipeline-failure-error-handling.md)
