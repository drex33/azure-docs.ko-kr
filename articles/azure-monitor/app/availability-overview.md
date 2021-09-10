---
title: Application Insights 가용성 테스트
description: 반복적인 웹 테스트를 설정하여 앱 또는 웹 사이트의 가용성 및 응답성을 모니터링합니다.
ms.topic: conceptual
ms.date: 07/13/2021
ms.openlocfilehash: 2e87d9eca75cfd507549213999882dfe6ff3ffa6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536155"
---
# <a name="application-insights-availability-tests"></a>Application Insights 가용성 테스트

웹앱이나 웹 사이트를 배포한 후 가용성과 응답성을 모니터링하도록 반복 테스트를 설정할 수 있습니다. [Application Insights](./app-insights-overview.md)는 전 세계 지점에서 정기적인 간격으로 애플리케이션에 웹 요청을 보냅니다. 애플리케이션이 응답하지 않거나 응답이 너무 느린 경우 사용자에게 경고할 수 있습니다.

퍼블릭 인터넷에서 액세스 가능한 모든 HTTP 또는 HTTPS 엔드포인트에 대해 가용성 테스트를 설정할 수 있습니다. 테스트하는 웹 사이트를 변경할 필요가 없습니다. 실제로 사용자가 소유한 사이트가 아니어도 됩니다. 서비스에서 사용하는 REST API의 가용성을 테스트할 수 있습니다.

## <a name="types-of-tests"></a>테스트 형식

가용성 테스트에는 다음 네 가지 형식이 있습니다.

* [URL ping 테스트(클래식)](monitor-web-app-availability.md): 포털을 통해 이 간단한 테스트를 만들어 엔드포인트가 응답하는지 확인하고 해당 응답과 관련된 성능을 측정할 수 있습니다. 종속 요청 구문 분석, 재시도 허용과 같은 고급 기능과 결합된 사용자 지정 성공 조건도 설정할 수 있습니다.
* [표준 테스트(미리 보기)](availability-standard-tests.md): 이 단일 요청 테스트는 URL ping 테스트와 비슷합니다. 여기에는 SSL 인증서 유효 기간, 사전 수명 확인, HTTP 요청 동사(예: `GET`, `HEAD` 또는 `POST`), 사용자 지정 헤더, HTTP 요청과 연결된 사용자 지정 데이터가 포함됩니다.
* [다단계 웹 테스트(클래식)](availability-multistep.md): 이 웹 요청 시퀀스 기록을 재생하여 더 복잡한 시나리오를 테스트할 수 있습니다. 다단계 웹 테스트는 Visual Studio Enterprise에서 만들고 포털에 업로드하여 실행할 수 있습니다.
* [사용자 지정 TrackAvailability 테스트](availability-azure-functions.md): 가용성 테스트를 실행할 사용자 지정 애플리케이션을 만들기로 결정한 경우 [TrackAvailability()](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) 메서드를 사용하여 결과를 Application Insights에 보낼 수 있습니다.

> [!IMPORTANT]
> [URL ping 테스트](monitor-web-app-availability.md)와 [다단계 웹 테스트](availability-multistep.md) 모두 퍼블릭 인터넷의 DNS 인프라를 활용하여 테스트된 엔드포인트의 도메인 이름을 확인합니다. 프라이빗 DNS를 사용하는 경우 퍼블릭 도메인 이름 서버가 테스트의 모든 도메인 이름을 제거할 수 있는지 확인해야 합니다. 제거할 수 없는 경우 [사용자 지정 TrackAvailability 테스트](/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability)를 대신 사용할 수 있습니다.

Application Insights 리소스당 최대 100개의 가용성 테스트를 만들 수 있습니다.

## <a name="troubleshooting"></a>문제 해결

전용 [문제 해결 문서](troubleshoot-availability.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [가용성 경고](availability-alerts.md)
* [다단계 웹 테스트](availability-multistep.md)
* [URL 테스트](monitor-web-app-availability.md)
* [Azure Functions를 사용하여 사용자 지정 가용성 테스트 만들기 및 실행](availability-azure-functions.md)
* [웹 테스트 Azure Resource Manager 템플릿](/azure/templates/microsoft.insights/webtests?tabs=json)