---
title: SQL 풀 저장 프로시저 활동을 사용하여 데이터 변환
description: SQL 풀 저장 프로시저 활동을 사용하여 Azure Synapse Analytics에서 저장 프로시저를 호출하는 방법을 설명합니다.
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: pipeline
author: linda33wj
ms.author: jingwang
ms.reviewer: jrasnick
ms.date: 05/13/2021
ms.openlocfilehash: 4c1f12d4e61f254f753f3feb59ca46997f88b751
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123257076"
---
# <a name="transform-data-by-using-sql-pool-stored-procedure-activity-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 SQL 풀 저장 프로시저 활동을 사용하여 데이터 변환

[!INCLUDE[appliesto-xxx-asa-md](../../data-factory/includes/appliesto-xxx-asa-md.md)]

[파이프라인](../../data-factory/concepts-pipelines-activities.md)의 데이터 변환 작업을 통해 원시 데이터를 변환 및 처리하여 예측 가능하고 통찰력 있는 정보로 만듭니다. 이 문서는 데이터 변환 및 지원되는 변환 작업의 일반적인 개요를 제공하는 [데이터 변환](../../data-factory/transform-data.md) 문서를 기반으로 합니다.

Azure Synapse Analytics에서 SQL 풀 저장 프로시저 활동을 사용하여 전용 SQL 풀에서 저장 프로시저를 호출할 수 있습니다.

## <a name="syntax-details"></a>구문 세부 정보

SQL 풀 저장 프로시저 활동에서는 다음 설정이 지원됩니다.

| 속성                  | 설명                              | 필수 |
| ------------------------- | ---------------------------------------- | -------- |
| name                      | 작업의 이름                     | 예      |
| description               | 작업이 무엇에 사용되는지 설명하는 텍스트입니다. | 예       |
| type                      | SQL 풀 저장 프로시저 활동의 경우 활동 유형은 **SqlPoolStoredProcedure** 입니다. | 예      |
| sqlPool         | 현재 Azure Synapse 작업 영역의 [전용 SQL 풀](../sql/overview-architecture.md)에 대한 참조입니다. | 예      |
| storedProcedureName       | 호출할 저장 프로시저의 이름을 지정합니다. | 예      |
| storedProcedureParameters | 저장 프로시저 매개 변수의 값을 지정합니다. 매개 변수 값 및 데이터 원본에서 지원하는 해당 형식을 전달하기 위해 `"param1": { "value": "param1Value","type":"param1Type" }`를 사용합니다. 매개 변수에 null을 전달해야 하는 경우 `"param1": { "value": null }`(모두 소문자)을 사용합니다. | 예       |

예제:

```json
{
    "name": "SQLPoolStoredProcedureActivity",
    "description":"Description",
    "type": "SqlPoolStoredProcedure",
    "sqlPool": {
        "referenceName": "DedicatedSQLPool",
        "type": "SqlPoolReference"
    },
    "typeProperties": {
        "storedProcedureName": "usp_sample",
        "storedProcedureParameters": {
            "identifier": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" }

        }
    }
}
```

## <a name="next-steps"></a>다음 단계
 
- [파이프라인 및 활동](../../data-factory/concepts-pipelines-activities.md)
- [전용 SQL 풀](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md)
