---
title: 진단 로그 사용
titleSuffix: Azure Digital Twins
description: 진단 설정으로 로깅을 사용하도록 설정하고 즉시 볼 수 있도록 로그를 쿼리하는 방법을 참조합니다. 또한 로그 범주 및 해당 스키마에 대해 알아봅니다.
author: baanders
ms.author: baanders
ms.date: 9/15/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q1
ms.openlocfilehash: c868f0c8418f6b265e3de5b4d8ea0c6b7312a33e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128593652"
---
# <a name="troubleshooting-azure-digital-twins-diagnostics-logging"></a>Azure Digital Twins 문제 해결: 진단 로깅

Azure Digital Twins는 서비스 인스턴스에 대 한 **로그** 를 수집 하 여 성능, 액세스 및 기타 데이터를 모니터링할 수 있습니다. 이러한 로그를 사용하여 Azure Digital Twins 인스턴스에서 발생하는 상황을 파악하고, Azure 지원에 문의하지 않고도 문제에 대한 근본 원인을 분석할 수 있습니다.

이 문서에서는 수집할 로그 유형과 해당 로그를 저장할 위치 (예: Log Analytics 또는 선택한 저장소 계정)를 포함 하 여 [Azure Portal](https://portal.azure.com)에서 진단 설정을 구성 하는 방법을 보여 줍니다. 그런 다음 로그를 쿼리하여 사용자 지정 정보를 신속 하 게 수집할 수 있습니다.

또한이 문서에는 Azure 디지털 쌍이 수집할 수 있는 모든 **로그 범주** 와 해당 **스키마** 에 대 한 정보도 포함 되어 있습니다.

## <a name="turn-on-diagnostic-settings"></a>진단 설정 켜기 

진단 설정을 켜서 Azure Digital Twins 인스턴스에서 로그 수집을 시작합니다. 내보낸 로그를 저장할 대상을 선택할 수도 있습니다. Azure Digital Twins 인스턴스에 대해 진단 설정을 사용하도록 설정하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Digital Twins 인스턴스로 이동합니다. 포털 검색 창에 이름을 입력하여 찾을 수 있습니다. 

2. 메뉴에서 **진단 설정** 을 선택한 다음, **진단 설정 추가** 를 선택합니다.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Azure Portal의 진단 설정 페이지 및 추가할 단추를 보여주는 스크린샷." lightbox="media/troubleshoot-diagnostics/diagnostic-settings.png":::

3. 이어지는 페이지에서 다음 값을 입력합니다.
     * **진단 설정 이름**: 진단 설정에 이름을 지정합니다.
     * **범주 세부 정보**: 모니터링할 작업을 선택하고 확인란을 선택하여 해당 작업에 대해 진단을 사용하도록 설정합니다. 진단 설정이 보고할 수 있는 작업은 같습니다.
        - DigitalTwinsOperation
        - EventRoutesOperation
        - ModelsOperation
        - QueryOperation
        - AllMetrics
        
        이러한 범주 및 해당 범주에 포함된 정보에 대한 자세한 내용은 아래의 [로그 범주](#log-categories) 섹션을 참조하세요.
     * **대상 세부 정보**: 로그를 보낼 위치를 선택합니다. 다음 세 가지 옵션을 조합해서 선택할 수 있습니다.
        - Log Analytics에 보내기
        - 스토리지 계정에 보관
        - 이벤트 허브로 스트림

        대상 선택에 필요한 경우 추가적인 세부 정보를 입력하라는 메시지가 표시될 수 있습니다.  
    
4. 새 설정을 저장합니다. 

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings-details.png" alt-text="사용자가 진단 설정 정보를 입력한 Azure Portal의 진단 설정 페이지를 보여주는 스크린샷." lightbox="media/troubleshoot-diagnostics/diagnostic-settings-details.png":::

새 설정은 약 10분 후에 적용됩니다. 그런 다음, 해당 인스턴스에 대한 **진단 설정** 페이지에서 구성된 대상에 로그가 다시 표시됩니다. 

진단 설정 및 설정 옵션에 대한 자세한 정보는 [플랫폼 로그 및 메트릭을 다른 대상으로 전송하는 진단 설정 만들기](../azure-monitor/essentials/diagnostic-settings.md)에서 확인할 수 있습니다.

## <a name="view-and-query-logs"></a>로그 보기 및 쿼리

Azure Digital Twins 로그의 저장소 세부 정보를 구성한 후 정보를 생성 하 고 문제를 해결 하기 위해 **사용자 지정 쿼리** 를 작성할 수 있습니다. 또한 서비스는 고객이 인스턴스에 대해 가질 수 있는 일반적인 질문의 주소를 지정 하 여 시작 하는 데 도움이 될 수 있는 몇 가지 예제 쿼리를 제공 합니다.

인스턴스에 대한 로그를 쿼리하는 방법은 다음과 같습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Digital Twins 인스턴스로 이동합니다. 포털 검색 창에 이름을 입력하여 찾을 수 있습니다. 

2. 메뉴에서 **로그** 를 선택하여 로그 쿼리 페이지를 엽니다. 페이지가 *쿼리* 라는 창으로 열립니다.

    :::image type="content" source="media/troubleshoot-diagnostics/logs.png" alt-text="미리 빌드된 쿼리를 보여주는 쿼리 창이 중첩된 Azure Portal의 Azure Digital Twins 인스턴스에 대한 로그 페이지를 보여주는 스크린샷." lightbox="media/troubleshoot-diagnostics/logs.png":::

    이러한 쿼리는 다양한 로그에 대해 작성된 미리 빌드된 쿼리 예입니다. 쿼리 중 하나를 선택하고 쿼리 편집기에 로드하고 실행하여 인스턴스에 대한 로그를 확인할 수 있습니다.

    아무 것도 실행하지 않고 *쿼리* 창을 닫고, 사용자 지정 쿼리 코드를 작성하거나 편집할 수 있는 쿼리 편집기 페이지로 바로 이동할 수도 있습니다.

3. *쿼리* 창을 종료한 후에는 쿼리 편집기 기본 페이지가 표시됩니다. 여기에서 예제 쿼리의 텍스트를 보고 편집하거나 처음부터 직접 쿼리를 작성할 수 있습니다.
    :::image type="content" source="media/troubleshoot-diagnostics/logs-query.png" alt-text="Azure Portal의 Azure Digital Twins 인스턴스에 대한 로그 페이지를 보여주는 스크린샷. 여기에는 로그 목록, 쿼리 코드 및 쿼리 기록이 포함됩니다." lightbox="media/troubleshoot-diagnostics/logs-query.png":::

    왼쪽 창에서 
    - *테이블* 탭에는 쿼리에서 사용할 수 있는 다양한 Azure Digital Twins [로그 범주](#log-categories)가 표시됩니다. 
    - *쿼리* 탭에는 편집기에 로드할 수 있는 예제 쿼리가 포함되어 있습니다.
    - *필터* 탭에서는 쿼리가 반환하는 데이터의 필터링된 보기를 사용자 지정할 수 있습니다.

로그 쿼리와 이러한 쿼리를 작성하는 방법에 대한 자세한 정보는 [Azure Monitor의 로그 쿼리 개요](../azure-monitor/logs/log-query-overview.md)를 참조하세요.

## <a name="log-categories"></a>로그 범주

Azure Digital Twins가 수집하는 로그의 범주에 대한 자세한 정보는 다음과 같습니다.

| 로그 범주 | Description |
| --- | --- |
| ADTModelsOperation | 모델과 관련된 모든 API 호출 로그 |
| ADTQueryOperation | 쿼리와 관련된 모든 API 호출 로그 |
| ADTEventRoutesOperation | 이벤트 경로와 관련된 모든 API 호출과 Azure Digital Twins에서 Event Grid, Event Hubs 및 Service Bus와 같은 엔드포인트 서비스로의 이벤트 송신을 로그합니다. |
| ADTDigitalTwinsOperation | 개별 트윈과 관련된 모든 API 호출 로그 |

각 로그 범주는 쓰기, 읽기, 삭제 및 동작의 작업으로 구성됩니다. 이러한 범주는 다음과 같이 REST API 호출에 매핑됩니다.

| 이벤트 유형 | REST API 작업 |
| --- | --- |
| 쓰기 | PUT 및 PATCH |
| 읽기 | GET |
| DELETE | DELETE |
| 작업 | POST |

다음은 각 범주에 로그된 작업 및 해당 [Azure Digital Twins REST API 호출](/rest/api/azure-digitaltwins/)의 포괄적인 목록입니다. 

>[!NOTE]
> 각 로그 범주에는 여러 작업/REST API 호출이 포함됩니다. 아래 표에서 각 로그 범주는 다음 로그 범주가 나열될 때까지 그 아래에 있는 모든 작업/REST API 호출에 매핑됩니다. 

| 로그 범주 | 작업 | REST API 호출 및 기타 이벤트 |
| --- | --- | --- |
| ADTModelsOperation | Microsoft.DigitalTwins/models/write | 디지털 트윈 모델 업데이트 API |
|  | Microsoft.DigitalTwins/models/read | ID 및 목록 API를 통해 가져오는 디지털 트윈 모델 |
|  | Microsoft.DigitalTwins/models/delete | 디지털 트윈 모델 삭제 API |
|  | Microsoft.DigitalTwins/models/action | 디지털 트윈 모델 추가 API |
| ADTQueryOperation | Microsoft.DigitalTwins/query/action | Twins API 쿼리 |
| ADTEventRoutesOperation | Microsoft.DigitalTwins/eventroutes/write | 이벤트 경로 추가 API |
|  | Microsoft.DigitalTwins/eventroutes/read | ID 및 목록 API를 통해 가져오는 이벤트 경로 |
|  | Microsoft.DigitalTwins/eventroutes/delete | 이벤트 경로 삭제 API |
|  | Microsoft.DigitalTwins/eventroutes/action | API 호출이 아닌 엔드포인트 서비스에 이벤트를 게시하는 동안 오류 발생 |
| ADTDigitalTwinsOperation | Microsoft.DigitalTwins/digitaltwins/write | Digital Twins 추가, 관계 추가, 업데이트, 구성 요소 업데이트 |
|  | Microsoft.DigitalTwins/digitaltwins/read | ID를 통해 가져오는 Digital Twins, 구성 요소 가져오기, ID로 관계 가져오기, 들어오는 관계 나열, 관계 나열 |
|  | Microsoft.DigitalTwins/digitaltwins/delete | Digital Twins 삭제, 관계 삭제 |
|  | Microsoft.DigitalTwins/digitaltwins/action | Digital Twins 구성 요소 원격 분석 보내기, 원격 분석 보내기 |

## <a name="log-schemas"></a>로그 스키마 

각 로그 범주에는 해당 범주의 이벤트를 보고하는 방식을 정의하는 스키마가 있습니다. 각 개별 로그 항목은 텍스트로 저장되고 JSON Blob으로 서식이 지정됩니다. 아래의 각 로그 유형에 대해 로그 및 예제 JSON 본문의 필드가 제공됩니다. 

`ADTDigitalTwinsOperation`, `ADTModelsOperation` 및 `ADTQueryOperation`은 일관된 API 로그 스키마를 사용합니다. `ADTEventRoutesOperation`은 속성에서 `endpointName` 필드를 포함하도록 스키마를 확장합니다.

### <a name="api-log-schemas"></a>API 로그 스키마

이 로그 스키마는 `ADTDigitalTwinsOperation`, `ADTModelsOperation`, `ADTQueryOperation`에 대해 일관됩니다. `Microsoft.DigitalTwins/eventroutes/action` 작업 이름을 **제외** 하고 동일한 스키마가 `ADTEventRoutesOperation`에도 사용됩니다(이 스키마에 대한 자세한 정보는 다음 섹션인 [송신 로그 스키마](#egress-log-schemas) 참조).

이 스키마에는 Azure Digital Twins 인스턴스에 대한 API 호출과 관련된 정보가 포함됩니다.

다음은 API 로그에 대한 필드 및 속성 설명입니다.

| 필드 이름 | 데이터 형식 | Description |
|-----|------|-------------|
| `Time` | DateTime | 이 이벤트가 발생한 날짜와 시간(UTC) |
| `ResourceId` | String | 이벤트가 발생한 리소스의 Azure Resource Manager 리소스 ID |
| `OperationName` | String  | 이벤트 중 수행되는 작업의 유형 |
| `OperationVersion` | String | 이벤트 중 사용된 API 버전 |
| `Category` | String | 내보내는 리소스의 유형 |
| `ResultType` | String | 이벤트의 결과 |
| `ResultSignature` | String | 이벤트에 대한 http 상태 코드 |
| `ResultDescription` | String | 이벤트에 대한 추가 정보 |
| `DurationMs` | String | 이벤트를 수행하는 데 걸린 시간(밀리초) |
| `CallerIpAddress` | String | 이벤트에 대한 마스킹된 원본 IP 주소 |
| `CorrelationId` | Guid | 고객이 제공한 이벤트의 고유 식별자 |
| `ApplicationId` | Guid | 전달자 권한 부여에 사용되는 애플리케이션 ID |
| `Level` | Int | 이벤트의 로깅 심각도 |
| `Location` | String | 이벤트가 발생한 지역 |
| `RequestUri` | URI | 이벤트 중 사용된 엔드포인트 |
| `TraceId` | String | `TraceId` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 시스템 간에 분산된 추적을 고유하게 식별하는 데 사용되는 전체 추적의 ID입니다. |
| `SpanId` | String | `SpanId` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 추적에서 이 요청의 ID입니다. |
| `ParentId` | String | `ParentId` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 부모 ID가 없는 요청이 추적의 루트입니다. |
| `TraceFlags` | String | `TraceFlags` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 샘플링, 추적 수준 등의 추적 플래그를 제어합니다. |
| `TraceState` | String | `TraceState` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 다양한 분산 추적 시스템에 걸쳐 있는 추가적인 공급업체별 추적 식별 정보입니다. |

다음은 이러한 로그 유형에 대한 예제 JSON 본문입니다.

#### <a name="adtdigitaltwinsoperation"></a>ADTDigitalTwinsOperation

```json
{
  "time": "2020-03-14T21:11:14.9918922Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/digitaltwins/write",
  "operationVersion": "2020-10-31",
  "category": "DigitalTwinOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": 8,
  "callerIpAddress": "13.68.244.*",
  "correlationId": "2f6a8e64-94aa-492a-bc31-16b9f0b16ab3",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/digitaltwins/factory-58d81613-2e54-4faa-a930-d980e6e2a884?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtmodelsoperation"></a>ADTModelsOperation

```json
{
  "time": "2020-10-29T21:12:24.2337302Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/models/write",
  "operationVersion": "2020-10-31",
  "category": "ModelsOperation",
  "resultType": "Success",
  "resultSignature": "201",
  "resultDescription": "",
  "durationMs": "80",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "9dcb71ea-bb6f-46f2-ab70-78b80db76882",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/Models?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adtqueryoperation"></a>ADTQueryOperation

```json
{
  "time": "2020-12-04T21:11:44.1690031Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/query/action",
  "operationVersion": "2020-10-31",
  "category": "QueryOperation",
  "resultType": "Success",
  "resultSignature": "200",
  "resultDescription": "",
  "durationMs": "314",
  "callerIpAddress": "13.68.244.*",
  "correlationId": "1ee2b6e9-3af4-4873-8c7c-1a698b9ac334",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/query?api-version=2020-10-31",
  "properties": {},
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
}
```

#### <a name="adteventroutesoperation"></a>ADTEventRoutesOperation

다음은 `Microsoft.DigitalTwins/eventroutes/action` 유형이 **아닌** `ADTEventRoutesOperation`에 대한 JSON 본문 예제입니다(이 스키마에 대한 자세한 정보는 다음 섹션인 [송신 로그 스키마](#egress-log-schemas) 참조).

```json
  {
    "time": "2020-10-30T22:18:38.0708705Z",
    "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
    "operationName": "Microsoft.DigitalTwins/eventroutes/write",
    "operationVersion": "2020-10-31",
    "category": "EventRoutesOperation",
    "resultType": "Success",
    "resultSignature": "204",
    "resultDescription": "",
    "durationMs": 42,
    "callerIpAddress": "212.100.32.*",
    "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
    "identity": {
      "claims": {
        "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
      }
    },
    "level": "4",
    "location": "southcentralus",
    "uri": "https://myinstancename.api.scus.digitaltwins.azure.net/EventRoutes/egressRouteForEventHub?api-version=2020-10-31",
    "properties": {},
    "traceContext": {
      "traceId": "95ff77cfb300b04f80d83e64d13831e7",
      "spanId": "b630da57026dd046",
      "parentId": "9f0de6dadae85945",
      "traceFlags": "01",
      "tracestate": "k1=v1,k2=v2"
    }
  },
```

### <a name="egress-log-schemas"></a>송신 로그 스키마

다음 예는 `Microsoft.DigitalTwins/eventroutes/action` 작업 이름과 관련된 `ADTEventRoutesOperation` 로그의 스키마입니다. 여기에는 Azure Digital Twins 인스턴스에 연결된 송신 엔드포인트에 대한 예외 및 API 작업과 관련된 세부 정보가 포함됩니다.

|필드 이름 | 데이터 형식 | Description |
|-----|------|-------------|
| `Time` | DateTime | 이 이벤트가 발생한 날짜와 시간(UTC) |
| `ResourceId` | String | 이벤트가 발생한 리소스의 Azure Resource Manager 리소스 ID |
| `OperationName` | String  | 이벤트 중 수행되는 작업의 유형 |
| `Category` | String | 내보내는 리소스의 유형 |
| `ResultDescription` | String | 이벤트에 대한 추가 정보 |
| `CorrelationId` | Guid | 고객이 제공한 이벤트의 고유 식별자 |
| `ApplicationId` | Guid | 전달자 권한 부여에 사용되는 애플리케이션 ID |
| `Level` | Int | 이벤트의 로깅 심각도 |
| `Location` | String | 이벤트가 발생한 지역 |
| `TraceId` | String | `TraceId` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 시스템 간에 분산된 추적을 고유하게 식별하는 데 사용되는 전체 추적의 ID입니다. |
| `SpanId` | String | `SpanId` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 추적에서 이 요청의 ID입니다. |
| `ParentId` | String | `ParentId` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 부모 ID가 없는 요청이 추적의 루트입니다. |
| `TraceFlags` | String | `TraceFlags` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 샘플링, 추적 수준 등의 추적 플래그를 제어합니다. |
| `TraceState` | String | `TraceState` - [W3C의 추적 컨텍스트](https://www.w3.org/TR/trace-context/)에 포함. 다양한 분산 추적 시스템에 걸쳐 있는 추가적인 공급업체별 추적 식별 정보입니다. |
| `EndpointName` | String | Azure Digital Twins에서 만든 송신 엔드포인트의 이름 |

다음은 이러한 로그 유형에 대한 예제 JSON 본문입니다.

#### <a name="adteventroutesoperation-for-microsoftdigitaltwinseventroutesaction"></a>Microsoft.DigitalTwins/eventroutes/action에 대한 ADTEventRoutesOperation

다음은 `Microsoft.DigitalTwins/eventroutes/action` 유형의 `ADTEventRoutesOperation`에 대한 예제 JSON 본문입니다.

```json
{
  "time": "2020-11-05T22:18:38.0708705Z",
  "resourceId": "/SUBSCRIPTIONS/BBED119E-28B8-454D-B25E-C990C9430C8F/RESOURCEGROUPS/MYRESOURCEGROUP/PROVIDERS/MICROSOFT.DIGITALTWINS/DIGITALTWINSINSTANCES/MYINSTANCENAME",
  "operationName": "Microsoft.DigitalTwins/eventroutes/action",
  "operationVersion": "",
  "category": "EventRoutesOperation",
  "resultType": "",
  "resultSignature": "",
  "resultDescription": "Unable to send EventHub message to [myPath] for event Id [f6f45831-55d0-408b-8366-058e81ca6089].",
  "durationMs": -1,
  "callerIpAddress": "",
  "correlationId": "7f73ab45-14c0-491f-a834-0827dbbf7f8e",
  "identity": {
    "claims": {
      "appId": "872cd9fa-d31f-45e0-9eab-6e460a02d1f1"
    }
  },
  "level": "4",
  "location": "southcentralus",
  "uri": "",
  "properties": {
    "endpointName": "myEventHub"
  },
  "traceContext": {
    "traceId": "95ff77cfb300b04f80d83e64d13831e7",
    "spanId": "b630da57026dd046",
    "parentId": "9f0de6dadae85945",
    "traceFlags": "01",
    "tracestate": "k1=v1,k2=v2"
  }
},
```

## <a name="next-steps"></a>다음 단계

* 진단을 구성하는 방법에 대한 자세한 내용은 [Azure 리소스에서 로그 데이터 수집 및 사용](../azure-monitor/essentials/platform-logs-overview.md)을 참조하세요.
* Azure Digital Twins 메트릭에 대한 정보는 [문제 해결: Azure Monitor로 메트릭 보기](troubleshoot-metrics.md)를 참조하세요.
* 메트릭에 대한 경고를 사용하도록 설정하는 방법은 [문제 해결: 경고 설정](troubleshoot-alerts.md)을 참조하세요.