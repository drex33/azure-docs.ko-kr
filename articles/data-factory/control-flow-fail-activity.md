---
title: Azure Data Factory 및 Synapse Analytics에서 실패 작업 실행
titleSuffix: Azure Data Factory & Azure Synapse
description: 이 문서에서는 Azure Data Factory 및 Synapse Analytics의 실패 작업이 파이프라인에서 의도적으로 오류를 throw 하는 방법에 대해 설명 합니다.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 07891ea9f4f25827c80ac56a2731d4a5393f3b38
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438724"
---
# <a name="execute-a-fail-activity-in-azure-data-factory-and-synapse-analytics"></a>Azure Data Factory 및 Synapse Analytics에서 실패 작업 실행

때로는 파이프라인에서 의도적으로 오류를 throw 하는 것이 좋습니다. [조회 작업](control-flow-lookup-activity.md) 은 일치 하는 데이터를 반환 하지 않거나, 내부 오류로 인해 [사용자 지정 작업](transform-data-using-dotnet-custom-activity.md) 을 완료할 수 있습니다. 그 이유는 무엇이 든 파이프라인에서 실패 작업을 사용 하 고 오류 메시지와 오류 코드를 모두 사용자 지정할 수 있습니다.

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]


## <a name="syntax"></a>구문

```json
{
    "name": "MyFailActivity",
    "type": "Fail",
    "typeProperties": {
        "message": "500",
        "errorCode": "My Custom Error Message"
    }
}

```

## <a name="type-properties"></a>형식 속성

| 속성 | Description | 허용되는 값 | 필수 |
| --- | --- | --- | --- |
| name | 실패 활동의 이름입니다. | String | 예 |
| type | **Fail** 으로 설정 해야 합니다. | String | 예 |
| message | Fail 활동에 표시 되는 오류 메시지입니다. 런타임에 계산 되는 동적 콘텐츠가 될 수 있습니다. | String | 예 |
| errorCode | Fail 활동의 오류 유형을 분류 하는 오류 코드입니다. 런타임에 계산 되는 동적 콘텐츠가 될 수 있습니다. | String | 예 |
| | |

## <a name="understand-the-fail-activity-error-code"></a>실패 작업 오류 코드 이해

실패 작업의 오류 메시지 및 오류 코드는 일반적으로 사용자가 설정 합니다. 오류 코드의 특정 의미를 이해 하려면 파이프라인 개발자에 게 문의 하십시오. 그러나 다음에 지 사례에서 Azure Data Factory는 오류 메시지 및/또는 오류 코드를 설정 합니다.

| 상황 설명 | 오류 메시지 | 오류 코드 |
| --- | --- | --- |
및의 (동적) 콘텐츠가 `message` `errorCode` 올바르게 해석 됩니다. | 사용자가 설정한 오류 메시지입니다. | 사용자가 설정 하는 오류 코드입니다. |
및의 동적 콘텐츠를 `message` `errorCode` 해석할 수 없습니다. | " _<activity_name>_ 실패 메시지 또는 오류 코드를 해석 하지 못했습니다." | `ErrorCodeNotString` |
| 의 동적 콘텐츠는 `message` 문자열로 해석할 수 없습니다. | "_<activity_name>_ 실패 메시지 매개 변수를 문자열로 해석 하지 못했습니다." | 사용자가 설정 하는 오류 코드입니다. |
| 의 동적 콘텐츠는 `message` null, 빈 문자열 또는 공백으로 확인 됩니다. | " _<activity_name>_ 실패 메시지 또는 오류 코드를 해석 하지 못했습니다." | 사용자가 설정 하는 오류 코드입니다. |
| 의 동적 콘텐츠는 `errorCode` 문자열로 해석할 수 없습니다. | 사용자가 설정한 오류 메시지입니다. | `ErrorCodeNotString` |
| 의 동적 콘텐츠는 `errorCode` null, 빈 문자열 또는 공백으로 확인 됩니다. | 사용자가 설정한 오류 메시지입니다. | `ErrorCodeNotString` |
| `message` `errorCode` 사용자가 제공 하는 또는의 값이 문자열을 사용할 수 없습니다. * | 다음을 사용 하 여 파이프라인 _실패_ : "속성 <> 값이 잘못 되었습니다. `errorCode` / `message` " | |
| `message`필드가 없습니다. * | "실패 메시지를 제공 하지 않았습니다." | 사용자가 설정 하는 오류 코드입니다. |
| `errorCode`필드가 없습니다. * | 사용자가 설정한 오류 메시지입니다. | `ErrorCodeNotString` |
| | |

\* Data Factory의 웹 UI (사용자 인터페이스)를 사용 하 여 파이프라인을 개발 하는 경우에는이 상황이 발생 하지 않습니다.

## <a name="next-steps"></a>다음 단계

다음을 포함 하 여 지원 되는 다른 제어 흐름 활동을 참조 하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 실행 작업](control-flow-execute-pipeline-activity.md)
- [각 작업에 대해](control-flow-for-each-activity.md)
- [메타데이터 가져오기 작업](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 활동](control-flow-until-activity.md)
