---
ms.topic: include
ms.date: 09/03/2021
ms.author: lagayhar
author: lgayhardt
services: azure-monitor
ms.subservice: application-insights
ms.openlocfilehash: 327ab19750615c7912eeffdd520efa896314f1d4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634244"
---
### <a name="connection-string-and-instrumentation-key"></a>연결 문자열 및 계측 키

코드리스 모니터링을 사용하는 경우 연결 문자열만 필요합니다. 그러나 수동 계측을 수행할 때 이전 버전의 SDK에 대한 이전 버전과의 호환성을 유지하기 위해 계측 키를 설정하는 것이 좋습니다.

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Application Insights의 표준 메트릭과 Azure App Service 메트릭 간에 차이가 있나요?

Application Insights는 애플리케이션에 대해 수행한 관련 요청에 대한 원격 분석을 수집합니다. WebApps/WebServer에서 오류가 발생하고 요청이 사용자 애플리케이션에 도달하지 않은 경우 Application Insights 이에 대한 원격 분석이 없습니다.

Application Insights에서 계산되는 `serverresponsetime`의 기간이 Web Apps에서 관찰되는 서버 응답 시간과 반드시 일치하는 것은 아닙니다. 이는 Application Insights는 실제 요청이 사용자 애플리케이션에 도달하는 기간만 계산되기 때문입니다. 요청이 WebServer에서 고정/큐에 대기 중인 경우 해당 대기 시간은 웹앱 메트릭에 포함되지만 애플리케이션 Insights 메트릭에는 포함되지 않습니다.


