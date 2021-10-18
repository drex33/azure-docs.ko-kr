---
title: Application Insights를 사용하여 AKS(Azure Kubernetes Service)에서 애플리케이션 모니터링 - Azure Monitor | Microsoft Docs
description: Azure Monitor는 Kubernetes에서 실행되는 애플리케이션과 원활하게 통합되며, 앱 문제를 즉시 찾을 수 있습니다.
ms.topic: conceptual
ms.date: 05/13/2020
ms.openlocfilehash: 63584a3d753f0b20e91697d0d49e4290348fe22b
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130129798"
---
# <a name="zero-instrumentation-application-monitoring-for-kubernetes---azure-monitor-application-insights"></a>Kubernetes에 대한 제로 계측 애플리케이션 모니터링 - Azure Monitor Application Insights

> [!IMPORTANT]
>  현재 코드를 계측하지 않고 Kubernetes에서 실행되는 Java 앱에 대한 모니터링을 사용하도록 설정할 수 있습니다. [Java 독립 실행형 에이전트](./java-in-process-agent.md)를 사용합니다. 애플리케이션 모니터링을 원활하게 활성화하는 솔루션은 다른 언어에서도 작동하지만 SDK를 사용하여 AKS에서 실행되는 앱을 모니터링합니다. [ASP.NET Core](./asp-net-core.md), [ASP.NET](./asp-net.md), [Node.js](./nodejs.md), [JavaScript](./javascript.md) 및 [Python](./opencensus-python.md).

## <a name="application-monitoring-without-instrumenting-the-code"></a>코드를 계측하지 않고 애플리케이션 모니터링
현재 Java만 사용하면 코드를 계측하지 않고도 애플리케이션 모니터링을 사용하도록 설정할 수 있습니다. 다른 언어로 애플리케이션을 모니터링하려면 SDK를 사용합니다. 

## <a name="java"></a>Java
활성화되면 Java 에이전트는 가장 널리 사용되는 라이브러리 및 프레임워크에서 다양한 요청, 종속성, 로그 및 메트릭을 자동으로 수집합니다.

[자세한 지침](./java-in-process-agent.md)에 따라 Kubernetes 앱 및 기타 환경에서 실행되는 Java 앱을 모니터링합니다. 

## <a name="other-languages"></a>기타 언어

다른 언어로 된 애플리케이션의 경우 현재 SKD를 사용하는 것이 좋습니다.
* [ASP.NET Core](./asp-net-core.md)
* [ASP.NET](./asp-net.md)
* [Node.JS](./nodejs.md) 
* [JavaScript](./javascript.md)
* [Python](./opencensus-python.md)

## <a name="next-steps"></a>다음 단계

* [Azure Monitor](../overview.md) 및 [Azure Application Insights](./app-insights-overview.md)에 대해 자세히 알아보기
* [분산 추적](./distributed-tracing.md)의 개요를 가져오고 비즈니스에 대해 [애플리케이션 맵](./app-map.md?tabs=net)이 수행할 수 있는 작업을 확인합니다.
