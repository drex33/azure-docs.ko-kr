---
title: 유효성 검사 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics의 유효성 검사 작업은 사용자 정의 조건으로 데이터 집합의 유효성을 검사할 때까지 파이프라인의 실행을 지연 시킵니다.
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: fa98ef27b5dbcc7949f37bf548c414d015224e4c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750620"
---
# <a name="validation-activity-in-azure-data-factory-and-synapse-analytics-pipelines"></a>Azure Data Factory 및 Synapse Analytics 파이프라인의 유효성 검사 작업
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

파이프라인에서 유효성 검사 활동을 사용하면 연결된 데이터 세트의 참조가 있는지, 파이프라인이 지정된 조건을 충족하는지 또는 시간 제한에 도달했는지 유효성을 검사한 후에만 파이프라인이 실행을 계속하는지 확인할 수 있습니다.


## <a name="syntax"></a>구문

```json

{
    "name": "Validation_Activity",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_File",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "minimumSize": 20
    }
},
{
    "name": "Validation_Activity_Folder",
    "type": "Validation",
    "typeProperties": {
        "dataset": {
            "referenceName": "Storage_Folder",
            "type": "DatasetReference"
        },
        "timeout": "7.00:00:00",
        "sleep": 10,
        "childItems": true
    }
}

```


## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
name | ‘Validation’ 작업의 이름 | String | 예 |
type | **Validation** 으로 설정해야 합니다. | String | 예 |
데이터 세트 | 이 데이터 세트 참조가 존재하는지 유효성을 검사했거나, 지정된 조건을 충족했거나, 시간 제한에 도달했을 때까지 작업이 실행을 차단합니다. 제공된 데이터 세트는 “MinimumSize” 또는 “ChildItems” 속성을 지원해야 합니다. | 데이터 세트 참조 | 예 |
시간 제한 | 작업 실행에 대한 시간 제한을 지정합니다. 값을 지정하지 않으면 기본값은 7일(“7.00:00:00”)입니다. 형식은 d.hh:mm:ss입니다. | String | 예 |
sleep | validation 시도 간의 지연 시간(초)입니다. 값을 지정하지 않으면 기본값은 10초입니다. | 정수 | 예 |
childItems | 폴더에 자식 항목이 있는지 여부를 확인합니다. -true로 설정할 수 있습니다. 이렇게 하면 폴더가 존재하고 항목이 있는지 유효성을 검사합니다. 폴더에 적어도 하나의 항목이 존재하거나 시간 제한 값에 도달할 때까지 차단합니다. -false의 경우 폴더가 존재하고 비어 있는지 유효성을 검사합니다. 폴더가 비어 있거나 시간 제한 값에 도달할 때까지 차단합니다. 값을 지정하지 않으면 폴더가 존재하거나 시간 제한에 도달할 때까지 작업이 차단됩니다. | 부울 | 예 |
minimumSize | 파일의 최소 크기(바이트)입니다. 값을 지정하지 않으면 기본값은 0바이트입니다. | 정수 | 예 |


## <a name="next-steps"></a>다음 단계
지원되는 다른 제어 흐름 작업을 참조하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
