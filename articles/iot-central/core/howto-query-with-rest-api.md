---
title: REST API를 사용 하 여 Azure IoT Central에서 장치 쿼리
description: IoT Central REST API를 사용 하 여 응용 프로그램에서 장치를 쿼리 하는 방법
author: dominicbetts
ms.author: dobett
ms.date: 10/12/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.openlocfilehash: b287bb463519d4b6ef76997dfb778ac4827014c2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103200"
---
# <a name="how-to-use-the-iot-central-rest-api-to-query-devices"></a>IoT Central REST API를 사용 하 여 장치를 쿼리 하는 방법

IoT Central REST API를 사용하면 IoT Central 애플리케이션과 통합되는 클라이언트 애플리케이션을 개발할 수 있습니다. REST API를 사용 하 여 IoT Central 응용 프로그램에서 장치를 쿼리할 수 있습니다. 다음은 쿼리 REST API를 사용 하는 방법에 대 한 예입니다.

- 장치에서 보고 한 마지막 10 개 원격 분석 값을 가져옵니다.
- 동일한 방에 있는 장치에서 최근 24 시간 동안의 데이터를 가져옵니다. Room은 장치 또는 클라우드 속성입니다.
- 오류 상태에 있고 오래 된 펌웨어가 있는 모든 장치를 찾습니다.
- 10 분 분량의 평균 장치에서 원격 분석 추세.
- 모든 자동 온도 조절기 장치의 현재 펌웨어 버전을 가져옵니다.

이 문서에서는 API를 사용 하 여 장치를 쿼리 하는 방법을 설명 합니다 `/query` .

디바이스는 지원하는 속성, 원격 분석 및 명령을 _구성 요소_ 및 _모듈_ 로 그룹화할 수 있습니다.

모든 IoT Central REST API 호출에는 권한 부여 헤더가 필요합니다. 자세히 알아보려면 [IoT Central REST API 호출을 인증하고 권한을 부여하는 방법](howto-authorize-rest-api.md)을 참조하세요.

IoT Central REST API에 대한 참조 설명서는 [Azure IoT Central REST API 참조](/rest/api/iotcentral/)를 참조하세요.

## <a name="run-a-query"></a>쿼리 실행

다음 요청을 사용 하 여 쿼리를 실행 합니다.

```http
POST https://{your app subdomain}.azureiotcentral.com/api/query?api-version=1.1-preview
```

쿼리는 요청 본문에 있으며 다음 예제와 같습니다.

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

`urn:modelDefinition:fupmoiu28b:ymju9efv9`절의 값은 `FROM` *장치 템플릿 ID* 입니다. 장치 템플릿 ID를 찾으려면 IoT Central 응용 프로그램의 **장치** 페이지로 이동 하 고 템플릿을 사용 하는 장치를 마우스로 가리킵니다. 카드에는 장치 템플릿 ID가 포함 되어 있습니다.

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="페이지 URL에서 장치 템플릿 ID를 찾는 방법을 보여 주는 스크린샷":::

이 요청에 대한 응답은 다음 예제와 같습니다.

```json
{
  "results": [
    {
      "$id": "sample-003",
      "$ts": "2021-09-10T12:59:52.015Z",
      "temperature": 47.632160152311016,
      "humidity": 49.726422005390816
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:01:34.286Z",
      "temperature": 58.898120617808495,
      "humidity": 44.66125772328022
    },
    {
      "$id": "sample-001",
      "$ts": "2021-09-10T13:04:04.96Z",
      "temperature": 52.79601469228174,
      "humidity": 71.5067230188416
    },
    {
      "$id": "sample-002",
      "$ts": "2021-09-10T13:04:36.877Z",
      "temperature": 49.610062789623264,
      "humidity": 52.78538601804491
    }
  ]
}
```

## <a name="syntax"></a>Syntax

쿼리 구문은 SQL 구문과 비슷하며 다음 절로 구성 됩니다.

- `SELECT` 는 필수 이며 장치 원격 분석 값과 같은 검색 하려는 데이터를 정의 합니다.
- `FROM` 는 필수 이며 쿼리 하는 장치 유형을 식별 합니다. 이 절은 장치 템플릿 ID를 지정 합니다.
- `WHERE` 는 선택 사항이 며 결과를 필터링 할 수 있습니다.
- `ORDER BY` 는 선택 사항이 며 결과를 정렬할 수 있습니다.
- `GROUP BY` 는 선택 사항이 며 결과를 집계할 수 있습니다.

다음 섹션에서는 이러한 절에 대해 자세히 설명 합니다.

## <a name="select-clause"></a>SELECT 절

`SELECT`절은 쿼리 출력에 포함할 데이터 값을 나열 하며 다음 항목을 포함할 수 있습니다.

- 원격 분석입니다. 장치 템플릿에서 원격 분석 이름을 사용 합니다.
- reported 속성. 장치 템플릿에서 속성 이름을 사용 합니다.
- 클라우드 속성. 장치 템플릿에서 클라우드 속성 이름을 사용 합니다.
- `$id`. 디바이스 ID
- `$provisioned`. 장치가 아직 프로 비전 되었는지 여부를 표시 하는 부울 값입니다.
- `$simulated`. 장치가 시뮬레이션 된 장치 인지 여부를 표시 하는 부울 값입니다.
- `$ts`. 원격 분석 값과 연결 된 타임 스탬프입니다.

장치 템플릿에서 **장치 정보** 구성 요소와 같은 구성 요소를 사용 하는 경우 다음과 같이 구성 요소에 정의 된 원격 분석 또는 속성을 참조 합니다.

```json
{
  "query": "SELECT deviceInformation.model, deviceInformation.swVersion FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

장치 템플릿에서 구성 요소 이름을 찾을 수 있습니다.

:::image type="content" source="media/howto-query-with-rest-api/show-component-name.png" alt-text="구성 요소 이름을 찾는 방법을 보여 주는 스크린샷":::

절에는 다음 제한이 적용 됩니다 `SELECT` .

- 와일드 카드 연산자가 없습니다.
- Select 목록에는 15 개 이상의 항목을 사용할 수 없습니다.
- 단일 쿼리에서는 원격 분석 또는 속성 중 하나를 선택 합니다. 속성 쿼리에는 보고 된 속성 및 클라우드 속성이 모두 포함 될 수 있습니다.

### <a name="aliases"></a>별칭

키워드를 사용 `AS` 하 여 절의 항목에 대 한 별칭을 정의 `SELECT` 합니다. 별칭은 쿼리 출력에 사용 됩니다. 쿼리의 다른 곳에서 사용할 수도 있습니다. 예:

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature as t, pressure as p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50"
}
```

> [!TIP]
> Select 목록에 있는 다른 항목은 별칭으로 사용할 수 없습니다. 예를 들어 다음은 허용 되지 않습니다 `SELECT id, temp AS id...` .

결과는 다음과 같습니다.

```json
{
  "results": [
    {
      "ID": "sample-002",
      "timestamp": "2021-09-10T11:40:29.188Z",
      "t": 40.20355053736378,
      "p": 79.26806508746755
    },
    {
      "ID": "sample-001",
      "timestamp": "2021-09-10T11:43:42.61Z",
      "t": 68.03536237975348,
      "p": 58.33517075380311
    }
  ]
}
```

### <a name="top"></a>맨 위로 이동

를 사용 `TOP` 하 여 쿼리가 반환 하는 결과 수를 제한 합니다. 예를 들어 다음 쿼리는 처음 10 개 결과를 반환 합니다.

```json
{
    "query": "SELECT TOP 10 $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9"
}
```

을 사용 하지 않는 경우 `TOP` 쿼리는 최대 1만 결과를 반환 합니다.

결과 수를 제한 하기 전에 결과를 정렬 하려면 `TOP` [ORDER by](#order-by-clause)를 사용 합니다.

## <a name="from-clause"></a>FROM 절

`FROM`절은 장치 템플릿 ID를 포함 해야 합니다. 절은 쿼리 하는 `FROM` 장치의 유형을 지정 합니다.

장치 템플릿 ID를 찾으려면 IoT Central 응용 프로그램의 **장치** 페이지로 이동 하 고 템플릿을 사용 하는 장치를 마우스로 가리킵니다. 카드에는 장치 템플릿 ID가 포함 되어 있습니다.

:::image type="content" source="media/howto-query-with-rest-api/show-device-template-id.png" alt-text="페이지 URL에서 장치 템플릿 ID를 찾는 방법을 보여 주는 스크린샷":::

장치 [-get](/rest/api/iotcentral/1.1-previewdataplane/devices/get) REST API 호출을 사용 하 여 장치에 대 한 장치 템플릿 ID를 가져올 수도 있습니다.

## <a name="where-clause"></a>WHERE 절

`WHERE`절을 사용 하면 값 및 시간 창을 사용 하 여 결과를 필터링 할 수 있습니다.

### <a name="time-windows"></a>기간

지정 된 시간 내에 응용 프로그램에서 받은 원격 분석을 가져오려면 `WITHIN_WINDOW` 절의 일부로를 사용 `WHERE` 합니다. 예를 들어 마지막 날에 대 한 온도 및 습도 원격 분석을 검색 하려면 다음 쿼리를 사용 합니다.

```json
{
  "query": "SELECT $id, $ts, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D)"
}
```

기간 값에는 [ISO 8601 기간 형식이](https://en.wikipedia.org/wiki/ISO_8601#Durations)사용 됩니다. 다음 표에는 몇 가지 예가 나와 있습니다.

| 예제 | 설명                |
| ------- | -------------------------- |
| PT10M   | 지난 10 분           |
| P1D     | 지난 날                   |
| P2DT12H | 지난 2 일 ~ 12 시간 |
| P1W     | 지난 주                  |
| PT5H    | 지난 5 시간            |
| ' 2021-06-13T13:00:00Z/2021-06-13T13:30:00Z ' | 특정 시간 범위 |

> [!NOTE]
> 원격 분석을 쿼리 하는 경우에만 시간 창을 사용할 수 있습니다.

### <a name="value-comparisons"></a>값 비교

특정 값에 따라 원격 분석 또는 속성 값을 가져올 수 있습니다. 예를 들어 다음 쿼리는 온도가 0 보다 크고, 압력이 50 보다 크고, 장치 ID가 **002** 및 **샘플-003** 중 하나인 모든 메시지를 반환 합니다.

```json
{
  "query": "SELECT $id, $ts, temperature AS t, pressure AS p FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND t > 0 AND p > 50 AND $id IN ['sample-002', 'sample-003']"
}
```

다음과 같은 연산자가 지원됩니다.

- 논리 연산자 `AND` 및 `OR`
- 비교 연산자,,,,,, `=` `!=` `>` `<` `>=` `<=` `<>` 및 `IN`

> [!NOTE]
> `IN`연산자는 원격 분석 및 에서만 작동 `$id` 합니다.

절에는 다음 제한이 적용 됩니다 `WHERE` .

- 단일 쿼리에서 최대 10 개의 연산자를 사용할 수 있습니다.
- 원격 분석 쿼리에서 `WHERE` 절은 원격 분석 및 장치 메타 데이터 필터만 포함할 수 있습니다.
- 속성 쿼리에서 절은 보고 된 `WHERE` 속성, 클라우드 속성 및 장치 메타 데이터 필터만 포함할 수 있습니다.

## <a name="aggregations-and-group-by-clause"></a>집계 및 GROUP BY 절

집계 함수를 사용 하면 시간 창 내에서 원격 분석 데이터의 평균, 최대값 및 최소값 같은 값을 계산할 수 있습니다. 예를 들어 다음 쿼리는 `sample-001` 10 분 windows에서 장치의 평균 온도 및 습도를 계산 합니다.

```json
{
  "query": "SELECT AVG(temperature), AVG(pressure) FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 WHERE WITHIN_WINDOW(P1D) AND $id='{{DEVICE_ID}}' GROUP BY WINDOW(PT10M)"
}
```

결과는 다음과 같습니다.

```json
{
    "results": [
        {
            "$ts": "2021-09-14T11:40:00Z",
            "avg_temperature": 49.212146114456104,
            "avg_pressure": 48.590304135023764
        },
        {
            "$ts": "2021-09-14T11:30:00Z",
            "avg_temperature": 52.44844454703927,
            "avg_pressure": 52.25973211022142
        },
        {
            "$ts": "2021-09-14T11:20:00Z",
            "avg_temperature": 50.14626272506926,
            "avg_pressure": 48.98400386898757
        }
    ]
}
```

지원 되는 집계 함수는,,,,, `SUM` `MAX` `MIN` `COUNT` `AVG` `FIRST` 및 `LAST` 입니다.

`GROUP BY WINDOW`창 크기를 지정 하는 데 사용 합니다. 을 사용 하지 않는 경우 `GROUP BY WINDOW` 쿼리는 지난 30 일 동안 원격 분석을 집계 합니다.

> [!NOTE]
> 원격 분석 값만 집계할 수 있습니다.

## <a name="order-by-clause"></a>ORDER BY 절

`ORDER BY`절을 사용 하면 원격 분석 값, 타임 스탬프 또는 장치 ID를 기준으로 쿼리 결과를 정렬할 수 있습니다. 오름차순 이나 내림차순으로 정렬할 수 있습니다. 예를 들어 다음 쿼리는 가장 최근 결과를 먼저 반환 합니다.

```json
{
  "query": "SELECT $id as ID, $ts as timestamp, temperature, humidity FROM urn:modelDefinition:fupmoiu28b:ymju9efv9 ORDER BY timestamp DESC"
}
```

> [!TIP]
> `ORDER BY`와 결합 `TOP` 하 여 정렬 후 쿼리가 반환 하는 결과 수를 제한 합니다.

## <a name="limits"></a>제한

쿼리의 현재 제한은 다음과 같습니다.

- 절 목록에는 15 개 이하의 항목이 `SELECT` 있습니다.
- 절에 논리적 연산이 10 개 미만 `WHERE` 입니다.
- 쿼리는 최대 1만 레코드를 반환 합니다.
- 쿼리 문자열의 최대 길이는 350 자입니다.
- 절 목록에는 와일드 카드 ()를 사용할 수 없습니다 `*` `SELECT` .

## <a name="next-steps"></a>다음 단계

이제 REST API를 사용 하 여 장치를 쿼리 하는 방법을 배웠으므로 제안 된 다음 단계는 [IoT Central REST API를 사용 하 여 사용자 및 역할을 관리 하는 방법을](howto-manage-users-roles-with-rest-api.md)배우는 것입니다.
