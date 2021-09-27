---
title: Azure App Services 성능 모니터링 | Microsoft Docs
description: Azure App Services에 대한 애플리케이션 성능 모니터링입니다. 차트 로드 및 응답 시간, 종속성 정보, 성능에 관해 설정된 경고입니다.
ms.topic: conceptual
ms.date: 08/05/2021
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 03ee74851f82fd8ff7833fc5d27c901cf0b2317a
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788746"
---
# <a name="application-monitoring-for-azure-app-service-overview"></a>Azure App Service 개요에 대 한 응용 프로그램 모니터링

이제 [Azure App Services](../../app-service/index.yml)에서 실행되는 ASP.NET, ASP.NET Core, Java, Node.js 기반 웹 애플리케이션에 대한 모니터링을 사용하도록 설정하는 것이 훨씬 쉬워졌습니다. 이전에는 수동으로 앱을 계측해야 했지만 이제 최신 확장/에이전트가 기본적으로 App Service 이미지에 기본 제공됩니다. 

## <a name="enable-application-insights"></a>Application Insights 사용

Azure App Services 호스트된 애플리케이션에 대해 애플리케이션 모니터링을 사용하도록 설정하는 방법에는 두 가지가 있습니다.

- **에이전트 기반 애플리케이션 모니터링**(ApplicationInsightsAgent). 
 
    - 이 방법으로 가장 쉽게 사용하도록 설정할 수 있고 코드 변경이나 고급 구성이 필요하지 않습니다. 이 방법을 “런타임” 모니터링이라고도 합니다. Azure App Services의 경우 이 모니터링 수준을 최소한으로 사용하는 것이 좋으며, 특정 시나리오에 따라 수동 계측을 통한 고급 모니터링이 필요한지 여부를 평가할 수 있습니다.

    - 다음은 에이전트 기반 모니터링에 대 한 지원입니다.
        - [.NET Core](./azure-web-apps-net-core.md)
        - [.NET](./azure-web-apps-net.md)
        - [Java](./azure-web-apps-java.md)
        - [Nodejs](./azure-web-apps-nodejs.md)
    
* Application Insights SDK를 설치하여 **코드를 통해 애플리케이션을 수동으로 계측** 합니다.

    * 이 방법은 훨씬 더 사용자 지정이 가능 하지만 [.Net Core](./asp-net-core.md), [.net](./asp-net.md), [Node.js](./nodejs.md), [Python](./opencensus-python.md)및 [Java](./java-in-process-agent.md)용 독립 실행형 에이전트에 대 한 SDK와 같은 접근 방식이 필요 합니다. 또한 이 방법은 최신 버전의 패키지에 대한 업데이트를 직접 관리해야 함을 의미합니다.

    * 에이전트 기반 모니터링을 사용하여 기본적으로 캡처되지 않는 이벤트/종속성을 추적하기 위해 사용자 지정 API 호출을 수행해야 하는 경우 이 방법을 사용해야 합니다. 자세한 내용은 [사용자 지정 이벤트 및 메트릭용 API 문서](./api-custom-events-metrics.md)를 확인하세요. 

> [!NOTE]
> 에이전트 기반 모니터링과 수동 SDK 기반 계측이 모두 감지되면 .NET에서는 수동 계측 설정만 적용되는 반면 Java에서는 에이전트 기반 계측만 원격 분석을 내보냅니다. 이를 통해 중복 데이터가 전송되는 것을 방지합니다.

> [!NOTE]
> 스냅숏 디버거 및 프로파일러는 .NET 및 .NET Core 에서만 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
- App Service에서 실행 되는 [.Net Core](./azure-web-apps-net-core.md), [.net](./azure-web-apps-net.md), [Java](./azure-web-apps-java.md) , [nodejs](./azure-web-apps-nodejs.md) 응용 프로그램에 대해 에이전트 기반 응용 프로그램 모니터링을 사용 하도록 설정 하는 방법에 대해 알아봅니다. 
