---
title: 파일 포함
description: 포함 파일
services: functions
author: nzthiago
ms.service: azure-functions
ms.topic: include
ms.date: 02/21/2018
ms.author: nzthiago
ms.custom: include file
ms.openlocfilehash: ba6929739504dfdf0b699e89393018f68751ce07
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128637625"
---
## <a name="function-app-timeout-duration"></a><a name="timeout"></a>함수 앱 시간 제한 기간 

함수 앱의 시간 제한 기간은 [host.json](../articles/azure-functions/functions-host-json.md#functiontimeout) 프로젝트 파일의 `functionTimeout` 속성에 의해 정의됩니다. 다음 표는 두 계획 및 다른 런타임 버전에 대한 기본 및 최댓값을 분 단위로 보여줍니다.

| 계획 | 런타임 버전 | 기본값 | 최대 |
|------|---------|---------|---------|
| Consumption | 1.x | 5 | 10 |
| Consumption | 2.x | 5 | 10 |
| Consumption | 3.x | 5 | 10 |
| Premium | 1.x | 제한 없음 | 제한 없음 |
| Premium | 2.x | 30 | 제한 없음 |
| Premium | 3.x | 30 | 제한 없음 |
| App Service | 1.x | 제한 없음 | 제한 없음 |
| App Service | 2.x | 30 | 제한 없음 |
| App Service | 3.x | 30 | 제한 없음 |

> [!NOTE] 
> 함수 앱 시간 제한 설정에 관계없이 230초는 HTTP 트리거 함수가 요청에 응답하는 데 걸리는 최대 시간입니다. 이는 [Azure Load Balancer의 기본 유휴 시간 제한](../articles/app-service/faq-availability-performance-application-issues.yml#why-does-my-request-time-out-after-230-seconds-) 때문입니다. 처리 시간을 늘리려면 [Durable Functions 비동기 패턴](../articles/azure-functions/durable/durable-functions-overview.md#async-http) 사용을 고려하거나 [실제 작업을 연기하고 즉각적인 응답을 반환합니다](../articles/azure-functions/performance-reliability.md#avoid-long-running-functions).
