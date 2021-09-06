---
title: Azure Data Explorer를 사용한 쿼리
titleSuffix: Azure Digital Twins
description: Azure Data Explorer용 Azure Digital Twins 쿼리 플러그 인 이해
author: baanders
ms.author: baanders
ms.date: 5/19/2021
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: 8b0c6558be0022d8cb72ede5b665023f2b3f138d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114438713"
---
# <a name="azure-digital-twins-query-plugin-for-azure-data-explorer"></a>Azure Data Explorer용 Azure Digital Twins 쿼리 플러그 인

[Azure Data Explorer](/azure/data-explorer/data-explorer-overview)용 Azure Digital Twins 플러그 인을 사용하면 Azure Digital Twins 그래프 및 Azure Data Explorer 시계열 데이터베이스를 통해 데이터에 액세스하고 결합하는 Azure Data Explorer 쿼리를 실행할 수 있습니다. 플러그 인을 사용하여 디지털 트윈 및 해당 관계를 통해 추론하여 모델링된 환경의 동작에 대한 인사이트를 얻을 수 있도록 다양한 시계열 데이터를 컨텍스트화합니다.

예를 들어 이 플러그 인을 사용하여 다음을 수행하는 Kusto 쿼리를 작성할 수 있습니다.
1. Azure Digital Twins 쿼리 플러그 인을 통해 관심 있는 디지털 트윈을 선택합니다.
2. Azure Data Explorer의 각 시계열에 대해 트윈을 조인하고 다음을 수행합니다. 
3. 해당 트윈에 대해 고급 시계열 분석을 수행합니다.  

Azure Digital Twins의 트윈 그래프에서 데이터를 Azure Data Explorer의 시계열 데이터와 결합하면 솔루션의 다양한 부분에 대한 운영 동작을 이해하는 데 도움이 될 수 있습니다. 

## <a name="using-the-plugin"></a>플러그 인 사용

다음 명령을 사용하여 Kusto 쿼리에서 플러그 인을 호출할 수 있습니다. 각각 Azure Digital Twins 인스턴스 엔드포인트 및 Azure Digital Twins 쿼리를 나타내는 문자열인 `<Azure-Digital-Twins-endpoint>` 및 `<Azure-Digital-Twins-query>`라는 두 개의 자리 표시자가 있습니다. 

```kusto
evaluate azure_digital_twins_query_request(<Azure-Digital-Twins-endpoint>, <Azure-Digital-Twins-query>) 
```

플러그 인은 [Azure Digital Twins 쿼리 API](/rest/api/digital-twins/dataplane/query)를 호출하여 작동하며, [쿼리 언어 구조](concepts-query-language.md)는 API를 사용할 때와 동일합니다. 단, 다음과 같은 두 가지 예외 사항이 있습니다. 
* `SELECT` 절의 `*` 와일드카드는 지원되지 않습니다. 대신 플러그 인을 사용하여 실행되는 Azure Digital Twin 쿼리는 `SELECT` 절에서 별칭을 사용해야 합니다.

    예를 들어 API를 사용하여 실행되는 아래 Azure Digital Twins 쿼리를 살펴보세요.
    
    ```SQL
    SELECT * FROM DIGITALTWINS
    ```
    
    플러그 인을 사용할 때 해당 쿼리를 실행하려면 다음과 같이 다시 작성해야 합니다.
    
    ```SQL
    SELECT T FROM DIGITALTWINS T
    ```
* 플러그 인에서 반환된 열 이름은 `$`로 시작할 수 없습니다. `SELECT` 절에서 별칭을 사용하면 이 시나리오를 방지하는 데도 도움이 됩니다.

    예를 들어 API를 사용하여 실행되는 아래 Azure Digital Twins 쿼리를 살펴보세요.
    
    ```SQL
    SELECT T.$dtId, T.Temperature FROM DIGITALTWINS T
    ```
    
    플러그 인을 사용할 때 해당 쿼리를 실행하려면 다음과 같이 다시 작성해야 합니다.
    
    ```SQL
    SELECT T.$dtId as tid, T.Temperature FROM DIGITALTWINS T
    ```


>[!IMPORTANT]
>사용자의 Azure AD 토큰은 인증하는 데 사용되므로 플러그 인의 사용자에게 **Azure Digital Twins 데이터 읽기 권한자** 역할 또는 **Azure Digital Twins 데이터 소유자** 역할을 부여해야 합니다. 이 역할을 할당하는 방법에 대한 정보는 [Azure Digital Twins 솔루션에 대한 보안](concepts-security.md#authorization-azure-roles-for-azure-digital-twins)을 참조하세요.

플러그 인을 사용하는 방법에 대한 자세한 내용은 [azure_digital_twins_query_request 플러그 인에 대한 Kusto 설명서](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)를 참조하세요.

예제 쿼리를 확인하고 샘플 데이터를 사용하여 연습을 완료하려면 GitHub에서 [Azure Data Explorer용 Azure Digital Twins 쿼리 플러그 인: 샘플 쿼리 및 연습](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries)을 참조하세요.

## <a name="using-azure-data-explorer-iot-data-with-azure-digital-twins"></a>Azure Digital Twins에서 Azure Data Explorer IoT 데이터 사용

IoT 데이터를 Azure Data Explorer에 수집하는 다양한 방법이 있습니다. Azure Digital Twins에서 Azure Data Explorer를 사용할 때 사용할 수 있는 두 가지는 다음과 같습니다.
* [Azure Time Series Insights 통합](how-to-integrate-time-series-insights.md)에 사용되는 프로세스와 유사하게 트윈 변경 이벤트를 처리하고 트윈 데이터를 Azure Data Explorer에 쓰는 Azure 함수를 사용하여 디지털 트윈 속성 값을 Azure Data Explorer에 기록합니다. 이 경로는 원격 분석 데이터를 사용하여 디지털 트윈을 수명으로 가져오는 고객에게 적합합니다.
* [IoT 데이터를 IoT Hub 또는 다른 원본에서 Azure Data Explorer 클러스터로 직접 수집](/azure/data-explorer/ingest-data-iot-hub)합니다. 그런 다음, 공동 Azure Digital Twins/Azure Data Explorer 쿼리를 사용하여 시계열 데이터를 컨텍스트화할 수 있도록 Azure Digital Twins 그래프를 사용합니다. 이 경로는 직접 수집 작업에 적합할 수 있습니다. 

### <a name="mapping-data-across-azure-data-explorer-and-azure-digital-twins"></a>Azure Data Explorer 및 Azure Digital Twins 간에 데이터 매핑

시계열 데이터를 Azure Data Explorer로 직접 수집하는 경우 이 원시 시계열 데이터를 공동 Azure Digital Twins/Azure Data Explorer 쿼리에 적합한 스키마로 변환해야 할 가능성이 높습니다.

Azure Data Explorer의 [업데이트 정책](/azure/data-explorer/kusto/management/updatepolicy) 을 사용하면 새 데이터가 원본 테이블에 삽입될 때마다 데이터를 자동으로 변환하고 대상 테이블에 추가할 수 있습니다. 

업데이트 정책을 사용하여 Azure Digital Twins의 해당 **트윈 ID** 로 원시 시계열 데이터를 보강하고, 대상 테이블에 유지할 수 있습니다. 그런 다음, 트윈 ID를 사용하여 대상 테이블을 Azure Digital Twins 플러그 인에서 선택한 디지털 트윈에 조인할 수 있습니다. 

예를 들어 Azure Data Explorer 인스턴스로 흐르는 원시 시계열 데이터를 저장하는 다음 테이블을 만들었다고 가정합니다. 

```kusto
.create-merge table rawData (Timestamp:datetime, someId:string, Value:string, ValueType:string)  
```

트윈 ID와 다른 선택적 필드를 사용하여 시계열 ID를 연결하는 매핑 테이블을 만들 수 있습니다. 

```kusto
.create-merge table mappingTable (someId:string, twinId:string, otherMetadata:string) 
```

그런 다음, 보강된 시계열 데이터를 저장할 대상 테이블을 만듭니다. 

```kusto
.create-merge table timeseriesSilver (twinId:string, Timestamp:datetime, someId:string, otherMetadata:string, ValueNumeric:real, ValueString:string)  
```

다음으로 매핑 테이블과 조인하여 원시 데이터를 보강하는 `Update_rawData` 함수를 만듭니다. 그러면 결과 대상 테이블에 트윈 ID가 추가됩니다. 

```kusto
.create-or-alter function with (folder = "Update", skipvalidation = "true") Update_rawData() { 
rawData 
| join kind=leftouter mappingTable on someId 
| project 
    Timestamp, ValueNumeric = toreal(Value), ValueString = Value, ... 
} 
```

마지막으로 함수를 호출하고 대상 테이블을 업데이트하는 업데이트 정책을 만듭니다. 

```kusto
.alter table timeseriesSilver policy update 
@'[{"IsEnabled": true, "Source": "rawData", "Query": "Update_rawData()", "IsTransactional": false, "PropagateIngestionProperties": false}]' 
```

대상 테이블을 만든 후에는 Azure Digital Twins 플러그 인을 사용하여 관심 있는 트윈을 선택한 다음, 대상 테이블의 시계열 데이터에 조인할 수 있습니다. 

### <a name="example-schema"></a>스키마 예제

다음은 공유 데이터를 나타내는 데 사용할 수 있는 스키마의 예제입니다.

| timestamp | twinId | modelId | name | 값 | relationshipTarget | relationshipID |
| --- | --- | --- | --- | --- | --- | --- |
| 2021-02-01 17:24 | ConfRoomTempSensor | dtmi:com:example:TemperatureSensor;1 | 온도 | 301.0 |  |  |

디지털 트윈 속성은 키-값 쌍(`name, value`)으로 저장됩니다. `name` 및 `value`는 동적 데이터 형식으로 저장됩니다. 

스키마는 `relationshipTarget` 및 `relationshipID` 필드별로 관계에 대한 속성 저장도 지원합니다. 키-값 스키마는 각 트윈 속성에 대해 열을 만들 필요가 없습니다.

### <a name="representing-properties-with-multiple-fields"></a>여러 필드가 포함된 속성 표시 

여러 필드를 사용하여 스키마에 속성을 저장하는 것이 좋습니다. 이러한 속성은 스키마에 `value`와 같이 JSON 개체를 저장하여 표시됩니다.

예를 들어 roll, pitch 및 yaw에 대한 세 개의 필드를 포함하는 속성을 나타내려는 경우 value 개체는 `{"roll": 20, "pitch": 15, "yaw": 45}`와 같습니다.

## <a name="next-steps"></a>다음 단계

* Azure Data Explorer: [azure_digital_twins_query_request plugin](/azure/data-explorer/kusto/query/azure-digital-twins-query-request-plugin)에서 Kusto 쿼리 언어에 대한 플러그 인 설명서를 확인합니다.

* 예제 시나리오: [Azure Data Explorer용 Azure Digital Twins 쿼리 플러그 인: 샘플 쿼리 및 연습](https://github.com/Azure-Samples/azure-digital-twins-getting-started/tree/main/adt-adx-queries)에서 쿼리를 실행하는 연습을 비롯하여 플러그 인을 사용하는 샘플 쿼리 확인합니다. 

* Azure Digital Twins: [Azure Time Series Insights와 통합](how-to-integrate-time-series-insights.md)에서 기록 데이터를 분석하기 위한 다른 전략에 대해 알아봅니다.
