---
title: Azure Maps 장기 실행 작업 API
description: Azure Maps에서 장기 실행 비동기 백그라운드 처리에 대해 알아보기
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
ms.custom: mvc
ms.openlocfilehash: c8f2fe6253efd6ee2bce4ff34f71434764188eb2
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132310460"
---
# <a name="creator-long-running-operation-api"></a>작성자 장기 실행 작업 API

Azure Maps의 일부 API는 [비동기 요청-회신 패턴](/azure/architecture/patterns/async-request-reply)을 사용합니다. 이 패턴을 통해 Azure Maps는 고가용성의 응답성이 뛰어난 서비스를 제공할 수 있습니다. 이 문서에서는 장기 실행 비동기 백그라운드 처리의 Azure Map 특정 구현을 설명합니다.

## <a name="submitting-a-request"></a>요청 제출

클라이언트 애플리케이션은 HTTP API에 대한 동기 호출을 통해 장기 실행 작업을 시작합니다. 일반적으로 이 호출은 HTTP POST 요청 형식입니다. 비동기 워크로드가 성공적으로 생성되면 API가 HTTP `202` 상태 코드를 반환하여 요청이 수락되었음을 나타냅니다. 이 응답에는 장기 실행 작업의 상태를 확인하기 위해 클라이언트에서 폴링할 수 있는 엔드포인트를 가리키는 `Location` 헤더가 포함되어 있습니다.

### <a name="example-of-a-success-response"></a>성공 응답의 예제

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

호출에서 유효성 검사를 통과하지 못하면 API는 잘못된 요청에 대한 HTTP `400` 응답을 대신 반환합니다. 응답 본문은 요청이 잘못된 이유에 대한 자세한 정보를 클라이언트에 제공합니다.

### <a name="monitoring-the-operation-status"></a>작업 상태 모니터링

허용되는 응답 헤더에 제공된 위치 엔드포인트를 폴링하여 장기 실행 작업의 상태를 확인할 수 있습니다. 작업 상태 요청의 응답 본문에는 항상 `status` 및 `createdDateTime` 속성이 포함됩니다. `status` 속성은 장기 실행 작업의 현재 상태를 나타냅니다. 가능한 상태에는 `"NotStarted"`, `"Running"`, `"Succeeded"` 및 `"Failed"`가 포함됩니다. `createdDateTime` 속성은 장기 실행 작업을 시작하기 위해 초기 요청이 수행된 시간을 표시합니다. 상태가 `"NotStarted"` 또는 `"Running"`이면 응답과 함께 `Retry-After` 헤더도 제공됩니다. 초 단위로 측정된 `Retry-After` 헤더를 사용하여 작업 상태 API에 대한 다음 폴링 호출을 수행할 시기를 결정할 수 있습니다.

### <a name="example-of-running-a-status-response"></a>상태 응답을 실행하는 예제

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>작업 완료 처리

장기 실행 작업이 완료되면 응답의 상태가 `"Succeeded"` 또는 `"Failed"`됩니다. 장기 실행 작업에서 새 리소스를 만든 경우 성공 응답은 HTTP `201 Created` 상태 코드를 반환합니다. 응답에는 리소스에 대한 메타데이터를 가리키는 `Location` 헤더도 포함됩니다. 새 리소스를 만들지 않은 경우 성공 응답은 HTTP `200 OK` 상태 코드를 반환합니다. 오류가 발생하면 응답 상태 코드도 `200 OK` 코드가 됩니다. 단, 응답에는 본문에 `error` 속성이 있습니다. 오류 데이터는 OData 오류 사양을 따릅니다.

### <a name="example-of-success-response"></a>성공 응답의 예제

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>실패 응답의 예제

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```
