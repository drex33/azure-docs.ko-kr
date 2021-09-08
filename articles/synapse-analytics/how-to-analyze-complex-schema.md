---
title: 배열 및 중첩된 구조로 스키마 분석
description: Apache Spark 및 SQL을 사용하여 배열 및 중첩 구조를 분석하는 방법
services: synapse-analytics
author: Rodrigossz
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: spark
ms.date: 06/15/2020
ms.author: rosouz
ms.reviewer: jrasnick
ms.openlocfilehash: 0130379b5c9225329bde1d49c119eaec446d17de
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112626"
---
# <a name="analyze-complex-data-types-in-azure-synapse-analytics"></a>Azure Synapse Analytics에서 복잡한 데이터 형식 분석

이 문서는 [Azure Cosmos DB용 Azure Synapse Link](.\synapse-link\how-to-connect-synapse-link-cosmos-db.md)의 Parquet 파일 및 컨테이너와 관련이 있습니다. Spark 또는 SQL을 사용하여 배열 또는 중첩된 구조와 같은 복잡한 스키마로 데이터를 읽거나 변환할 수 있습니다. 다음 예제는 단일 문서를 사용하여 완료되지만, Spark 또는 SQL을 사용하여 수십억 개의 문서로 규모를 쉽게 조정할 수 있습니다. 이 문서에 포함된 코드는 PySpark(Python)를 사용합니다.

## <a name="use-case"></a>사용 사례

복합 데이터 형식이 점점 보편화되어 데이터 엔지니어에게 문제가 되고 있습니다. 중첩된 스키마와 배열을 분석하는 일은 시간이 많이 걸리고 복잡한 SQL 쿼리를 포함할 수 있습니다. 또한 중첩된 열 데이터 형식에 대한 이름을 바꾸거나 캐스트기가 어려울 수 있습니다. 또한 깊이 중첩된 개체를 사용하는 경우 성능 문제가 발생할 수 있습니다.

데이터 엔지니어는 복합 데이터 형식을 효율적으로 처리하고 누구나 손쉽게 액세스할 수 있도록 하는 방법을 이해해야 합니다. 다음 예제에서는 Azure Synapse Analytics에서 Spark를 사용하여 데이터 프레임을 통해 개체를 플랫 구조로 읽고 변환합니다. Azure Synapse Analytics에서 서버리스 SQL 모델을 사용하여 이러한 개체를 직접 쿼리하고 해당 결과를 일반 테이블로 반환합니다.

## <a name="what-are-arrays-and-nested-structures"></a>배열 및 중첩된 구조란?

다음 개체는 [Application Insights](../azure-monitor/app/app-insights-overview.md)에서 가져옵니다. 이 개체에는 중첩된 구조와 중첩된 구조를 포함하는 배열이 있습니다.

```json
{
    "id": "66532691-ab20-11ea-8b1d-936b3ec64e54",
    "context": {
        "data": {
            "eventTime": "2020-06-10T13:43:34.553Z",
            "samplingRate": "100.0",
            "isSynthetic": "false"
        },
        "session": {
            "isFirst": "false",
            "id": "38619c14-7a23-4687-8268-95862c5326b1"
        },
        "custom": {
            "dimensions": [
                {
                    "customerInfo": {
                        "ProfileType": "ExpertUser",
                        "RoomName": "",
                        "CustomerName": "diamond",
                        "UserName": "XXXX@yahoo.com"
                    }
                },
                {
                    "customerInfo": {
                        "ProfileType": "Novice",
                        "RoomName": "",
                        "CustomerName": "topaz",
                        "UserName": "XXXX@outlook.com"
                    }
                }
            ]
        }
    }
}
```

### <a name="schema-example-of-arrays-and-nested-structures"></a>배열 및 중첩 구조의 스키마 예제
`df.printschema` 명령을 사용하여 개체의 데이터 프레임(**df** 라고 함)의 스키마를 인쇄하는 경우 다음 표현이 표시됩니다.

* 노란색은 중첩된 구조를 나타냅니다.
* 녹색은 두 개의 요소가 포함된 배열을 나타냅니다.

[![스키마 원본을 보여 주는 노란색/녹색 강조 표시가 있는 코드](./media/how-to-complex-schema/schema-origin.png)](./media/how-to-complex-schema/schema-origin.png#lightbox)

문서가 Azure Cosmos DB 트랜잭션 저장소로 수집될 때 `_rid`, `_ts`, `_etag`이(가) 시스템에 추가되었습니다.

이전 데이터 프레임에서는 열 5개와 행 1개만 계산합니다. 변환 후 큐레이팅된 데이터 프레임에는 열 13개와 행 2개가 표 형식으로 포함됩니다.

## <a name="flatten-nested-structures-and-explode-arrays"></a>중첩된 구조 평면화 및 배열 분해

Azure Synapse Analytics에서 Spark를 사용하여 중첩 구조를 열로, 배열 요소를 여러 행으로 쉽게 변환할 수 있습니다. 다음 단계를 사용하여 구현합니다.

[![Spark 변환 단계를 보여 주는 순서도](./media/how-to-complex-schema/spark-transform-steps.png)](./media/how-to-complex-schema/spark-transform-steps.png#lightbox)

### <a name="define-a-function-to-flatten-the-nested-schema"></a>중첩된 스키마를 평면화하는 함수 정의

이 함수는 변경하지 않고 사용할 수 있습니다. [PySpark Notebook](quickstart-apache-spark-notebook.md)에서 다음 함수를 사용하여 셀을 만듭니다.

```python
from pyspark.sql.functions import col

def flatten_df(nested_df):
    stack = [((), nested_df)]
    columns = []

    while len(stack) > 0:
        parents, df = stack.pop()

        flat_cols = [
            col(".".join(parents + (c[0],))).alias("_".join(parents + (c[0],)))
            for c in df.dtypes
            if c[1][:6] != "struct"
        ]

        nested_cols = [
            c[0]
            for c in df.dtypes
            if c[1][:6] == "struct"
        ]

        columns.extend(flat_cols)

        for nested_col in nested_cols:
            projected_df = df.select(nested_col + ".*")
            stack.append((parents + (nested_col,), projected_df))

    return nested_df.select(columns)
```

### <a name="use-the-function-to-flatten-the-nested-schema"></a>함수를 사용하여 중첩된 스키마를 평면화합니다.

이 단계에서는 데이터 프레임의 중첩된 스키마(**df**)를 새 데이터 프레임(`df_flat`)으로 평면화합니다.

```python
from pyspark.sql.types import StringType, StructField, StructType
df_flat = flatten_df(df)
display(df_flat.limit(10))
```

표시 함수는 열 10개와 행 1개를 반환해야 합니다. 배열 및 중첩된 요소도 여전히 있습니다.

### <a name="transform-the-array"></a>배열 변환

여기에서는 데이터 프레임(`df_flat`)의 배열(`context_custom_dimensions`)을 새 데이터 프레임(`df_flat_explode`)으로 변환합니다. 다음 코드에서는 선택할 열도 정의합니다.

```python
from pyspark.sql.functions import explode
from pyspark.sql.functions import flatten
from pyspark.sql.functions import arrays_zip
df_flat_explode = df_flat.select("_rid","_ts","id","_etag",explode(df_flat.context_custom_dimensions),"context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")\
.select("_rid","_ts","id","_etag","col.*","context_session_isFirst","context_session_id","context_data_eventTime","context_data_samplingRate","context_data_isSynthetic")
display(df_flat_explode.limit(10))

```

표시 함수는 열 10개와 행 2개를 반환해야 합니다. 다음 단계는 1단계에서 정의한 함수로 중첩된 스키마를 평면화하는 것입니다.

### <a name="use-the-function-to-flatten-the-nested-schema"></a>함수를 사용하여 중첩된 스키마를 평면화합니다.

마지막으로, 함수를 사용하여 데이터 프레임(`df_flat_explode`)의 중첩된 스키마를 새 데이터 프레임(`df_flat_explode_flat`)으로 평면화합니다.
```python
df_flat_explode_flat = flatten_df(df_flat_explode)
display(df_flat_explode_flat.limit(10))
```

표시 함수는 열 13개와 행 2개를 표시해야 합니다.

데이터 프레임(`df_flat_explode_flat`)의 함수(`printSchema`)는 다음 결과를 반환합니다.

[![최종 스키마를 보여 주는 코드](./media/how-to-complex-schema/schema-final.png)](./media/how-to-complex-schema/schema-final.png#lightbox)

## <a name="read-arrays-and-nested-structures-directly"></a>배열과 중첩된 구조 직접 읽기

서버리스 SQL 모델을 사용하면 해당 개체에 대한 보기와 테이블을 쿼리하고 만들 수 있습니다.

먼저, 데이터가 저장된 방식에 따라 사용자는 다음 분류를 사용해야 합니다. 대문자로 표시되는 모든 항목은 사용 사례에 따라 다릅니다.

| 대량 | 형식 |
| ------ | ------ |
| 'https://ACCOUNTNAME.dfs.core.windows.net/FILESYSTEM/PATH/FINENAME.parquet' |'Parquet'(ADLSg2)|
| N'endpoint=https://ACCOUNTNAME.documents-staging.windows-ppe.net:443/;account=ACCOUNTNAME;database=DATABASENAME;collection=COLLECTIONNAME;region=REGIONTOQUERY', SECRET='YOURSECRET' |'CosmosDB'(Azure Synapse Link)|


각 필드를 다음과 같이 바꿉니다.
* 'YOUR BULK ABOVE'는 연결하는 데이터 원본의 연결 문자열입니다.
* 'YOUR TYPE ABOVE'는 원본에 연결하는 데 사용하는 형식입니다.

```sql
select *
FROM
openrowset(
    BULK 'YOUR BULK ABOVE',
    FORMAT='YOUR TYPE ABOVE'
)
with (id varchar(50),
        contextdataeventTime varchar(50) '$.context.data.eventTime',
        contextdatasamplingRate varchar(50) '$.context.data.samplingRate',
        contextdataisSynthetic varchar(50) '$.context.data.isSynthetic',
        contextsessionisFirst varchar(50) '$.context.session.isFirst',
        contextsessionid varchar(50) '$.context.session.id',
        contextcustomdimensions varchar(max) '$.context.custom.dimensions'
) as q 
cross apply openjson (contextcustomdimensions) 
with ( ProfileType varchar(50) '$.customerInfo.ProfileType',
            RoomName varchar(50) '$.customerInfo.RoomName',
            CustomerName varchar(50) '$.customerInfo.CustomerName',
            UserName varchar(50) '$.customerInfo.UserName'
    )
```

작업에는 다음과 같은 두 가지 유형이 있습니다.

- 첫 번째 작업 유형은 다음 코드 줄에 표시되며, 중첩된 요소(`Context.Data.eventTime`)를 참조하는 `contextdataeventTime`(이)라는 열을 정의합니다. 
  ```sql
  contextdataeventTime varchar(50) '$.context.data.eventTime'
  ```

  이 줄은 중첩된 요소(`Context>Data>eventTime`)를 참조하는 `contextdataeventTime`(이)라는 열을 정의합니다.

- 두 번째 작업 유형은 `cross apply`을(를) 사용하여 배열의 각 요소에 대해 새 행을 만듭니다. 그런 다음 각각의 중첩된 개체를 정의합니다. 
  ```sql
  cross apply openjson (contextcustomdimensions) 
  with ( ProfileType varchar(50) '$.customerInfo.ProfileType', 
  ```

  배열에 중첩된 구조가 4개인 요소가 5개 있는 경우 SQL 서버리스 모델은 행 5개와 열 4개를 반환합니다. SQL 서버리스 모델은 현재 위치에서 쿼리를 수행하고, 배열을 행 2개로 매핑하며, 중첩된 구조를 모두 열에 표시할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Spark 3을 사용하여 Azure Cosmos DB용 Synapse Link를 쿼리하는 방법 알아보기](./synapse-link/how-to-query-analytical-store-spark-3.md)
* [Spark 2를 사용하여 Azure Cosmos DB용 Synapse Link를 쿼리하는 방법 알아보기](./synapse-link/how-to-query-analytical-store-spark.md)
* [Parquet 중첩 형식 쿼리](./sql/query-parquet-nested-types.md)
