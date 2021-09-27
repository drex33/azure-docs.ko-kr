---
title: 실패 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics의 실패 작업은 의도적으로 파이프라인에서 오류를 throw 합니다.
author: chez-charlie
ms.author: chez
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/22/2021
ms.openlocfilehash: 95c4a7245a39ad717ad47ac9e8a7c72b2b5365fd
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129059046"
---
# <a name="execute-fail-activity-in-azure-data-factory-and-synapse-analytics-preview"></a>Azure Data Factory 및 Synapse Analytics에서 실행 실패 작업 (미리 보기)

경우에 따라 파이프라인에서 오류를 의도적으로 throw 할 수 있습니다. 즉, [조회 작업](control-flow-lookup-activity.md) 에서 일치 하는 데이터를 반환 하지 않거나 200을 반환 하는 [사용자 지정 작업](transform-data-using-dotnet-custom-activity.md) 에 내부 오류가 발생 했습니다. 그 이유는 무엇이 든 파이프라인에서 실패 작업을 사용 하 고 오류 코드와 오류 메시지를 모두 사용자 지정할 수 있습니다.

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

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | `Fail` 작업의 이름입니다. | String | 예
type | **Fail** 으로 설정 해야 합니다. | String | 예
message | 실패 작업에 표시 되는 오류 메시지입니다. 런타임에 동적 콘텐츠를 평가할 수 있습니다. | String | 예
errorCode | 실패 활동의 오류 유형을 분류 하는 오류 코드입니다. 런타임에 동적 콘텐츠를 평가할 수 있습니다. | String | 예

## <a name="understand-fail-activity-error-code"></a>실패 작업 이해 오류 코드

일반적으로 실패 작업의 오류 코드 및 오류 메시지는 고객에 의해 설정 됩니다. 파이프라인 개발자에 게 문의 하 여 오류 코드의 특정 의미를 파악 합니다. 그러나 다음 edge 사례에서 ADF는 오류 코드 및/또는 오류 메시지를 설정 합니다.

상황 설명 | 오류 메시지 | 오류 코드
-------- | ----------- | --------------
(동적) 콘텐츠 `message` 및 `errorCode` 올바르게 해석 | 사용자가 설정한 오류 메시지 | 사용자가 설정 하는 오류 코드
및 둘 다의 동적 콘텐츠를 `message` `errorCode` 해석할 수 없습니다. | ' _<activity_name>_ 실패 메시지 또는 오류 코드를 해석 하지 못했습니다. | `ErrorCodeNotString`
의 동적 콘텐츠를 `message` 문자열로 해석할 수 없습니다. | '_<activity_name>_ 실패 메시지 매개 변수를 ' 문자열로 해석할 수 없습니다. ' | 사용자가 설정 하는 오류 코드
의 동적 콘텐츠가 `message` null, 빈 문자열 또는 공백으로 확인 됩니다. | ' _<activity_name>_ 실패 메시지 또는 오류 코드 '로 해석 하지 못했습니다. | 사용자가 설정 하는 오류 코드
의 동적 콘텐츠를 `errorCode` 문자열로 해석할 수 없습니다. | 사용자가 설정한 오류 메시지 | `ErrorCodeNotString`
의 동적 콘텐츠가 `errorCode` null, 빈 문자열 또는 공백으로 확인 됩니다. | 사용자가 설정한 오류 메시지 | `ErrorCodeNotString`
`message` `errorCode` 사용자가 제공한 값 또는 사용자가 제공한 값이 문자열을 사용할 수 없습니다. * | 다음을 사용 하 여 파이프라인 __실패__ : ' 속성 <`errorCode` / `message`> ' 값이 잘못 되었습니다.
누락 된 `message` 필드 * | ' 실패 메시지를 제공 하지 않았습니다. ' | 사용자가 설정 하는 오류 코드
누락 된 `errorCode` 필드 * | 사용자가 설정한 오류 메시지 | `ErrorCodeNotString`

Data Factory의 웹 UI (사용자 인터페이스)를 사용 하 여 파이프라인을 개발 하는 경우 *로 표시 된 상황은 발생 하지 않습니다.

## <a name="next-steps"></a>다음 단계

다음을 포함 하 여 지원 되는 다른 제어 흐름 활동을 참조 하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
