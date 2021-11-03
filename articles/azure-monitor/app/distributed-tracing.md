---
title: Azure Application Insights의 분산 추적 | Microsoft Docs
description: OpenCensus 프로젝트에서 파트너 관계를 통해 분산 추적에 대한 Microsoft의 지원 정보를 제공합니다.
ms.topic: conceptual
ms.custom: devx-track-dotnet
author: nikmd23
ms.author: nimolnar
ms.date: 09/17/2018
ms.reviewer: mbullwin
ms.openlocfilehash: c4f32c0803bb5815599c81ca6c89335fa6e1ffac
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131455172"
---
# <a name="what-is-distributed-tracing"></a>분산 추적이란?

최신 클라우드 및 [마이크로 서비스](https://azure.com/microservices) 아키텍처가 등장하면서 비용을 절감하면서 가용성과 처리량을 높일 수 있는 독립적으로 배포할 수 있는 간단한 서비스가 생겨났습니다. 해당 움직임으로 인해 개별 서비스를 전반적으로 이해하기는 쉬워졌지만 전체 시스템을 추론하고 디버그하기가 더 어려워졌습니다.

모놀리식 아키텍처에서는 호출 스택을 사용하여 디버깅하는 데 익숙해졌습니다. 호출 스택은 각 호출에 대한 세부 정보 및 매개 변수와 함께 실행 흐름(메서드 A가 메서드 B를 호출하고 이로 인해 메서드 C가 호출됨)을 표시하기에 훌륭한 도구입니다. 이런 방식은 단일 프로세스에서 실행되는 단일 조직이나 서비스에는 적합하지만 호출이 로컬 스택의 참조가 아닌 프로세스 경계를 넘어서는 경우에는 어떻게 디버그해야 할까요? 

바로 이런 경우 분산 추적이 사용됩니다.  

분산 추적은 최신 클라우드 및 마이크로 서비스 아키텍처의 호출 스택과 동일하며 단순한 성능 프로파일러가 추가되었습니다. Azure Monitor에는 분산 추적 데이터를 사용하는 두 가지 환경이 제공됩니다. 첫째는 [트랜잭션 진단](./transaction-diagnostics.md) 보기입니다. 이것은 호출 스택에 시간 차원이 추가된 것과 동일합니다. 트랜잭션 진단 보기는 단일 트랜잭션/요청에 대한 가시성을 제공하며 요청 단위를 기준으로 안정성 문제와 성능 병목 현상의 근본 원인을 찾는 데 유용합니다.

Azure Monitor에는 [애플리케이션 맵](./app-map.md) 보기도 제공됩니다. 여기에는 다수의 트랜잭션을 집계하여 시스템이 어떻게 상호 작용하는지에 대한 토폴로지 보기가 표시되고 평균 성능 및 오류율이 얼마인지를 보여줍니다. 

## <a name="how-to-enable-distributed-tracing"></a>분산 추적을 사용하는 방법

애플리케이션의 서비스 전체에서 분산 추적을 사용하도록 설정하는 것은 서비스가 구현된 언어에 따라 적절한 에이전트, SDK 또는 라이브러리를 각 서비스에 추가하는 것만큼 간단합니다.

## <a name="enabling-via-application-insights-through-auto-instrumentation-or-sdks"></a>자동 계측 또는 SDK를 통해 Application Insights로 사용 설정

.NET, .NET Core, Java, Node.js 및 JavaScript용 Application Insights 에이전트 및/또는 SDK는 모두 기본적으로 분산 추적을 지원합니다. 각 Application Insights SDK를 설치 및 구성하는 지침이 다음과 같이 제공됩니다.

* [.NET](asp-net.md)
* [.NET Core](asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.JS](../app/nodejs-quick-start.md)
* [JavaScript](./javascript.md)
* [Python](opencensus-python.md)

적절한 Application Insight SDK가 설치되고 구성되면, 많이 사용되는 프레임워크, 라이브러리 및 기술에 대한 추적 정보가 SDK 종속성 자동 수집기를 통해 자동으로 수집됩니다. 지원되는 기술의 전체 목록은 [종속성 자동 수집 설명서](./auto-collect-dependencies.md)에 제공됩니다.

 또한 [TelemetryClient](./api-custom-events-metrics.md)의 [TrackDependency](./api-custom-events-metrics.md)를 호출하여 원하는 기술을 수동으로 추적할 수 있습니다.

## <a name="enable-via-opentelemetry"></a>OpenTelemetry를 통해 사용

애플리케이션 Insights [이제 OpenTelemetry](https://opentelemetry.io/)를 통한 분산 추적을 지원합니다. OpenTelemetry는 추적, 메트릭 및 로그를 Application Insights 보내기 위한 공급업체 중립적 계측을 제공합니다. 처음에 OpenTelemetry 커뮤니티는 분산 추적을 시작했습니다. 메트릭 및 로그는 아직 진행 중입니다. 완전한 관찰 가능성 스토리는 세 가지 핵심 요소 모두를 포함하지만, 현재 [.NET, Python 및 JavaScript용 Azure Monitor OpenTelemetry 기반 내보내기 미리 보기 제품에는](opentelemetry-enable.md) 분산 추적만 포함됩니다. 그러나 Java OpenTelemetry 기반 Azure Monitor 제품은 GA이며 완벽하게 지원됩니다. 

다음 페이지는 Microsoft의 OpenTelemetry 기반 제품을 사용하도록 설정하고 구성하는 언어별 지침으로 구성됩니다. 중요한 점은 OpenTelemetry가 프로젝트에 적합한지 확인할 수 있도록 각 제품의 사용 가능한 기능 및 제한 사항을 공유합니다.

* [.NET](opentelemetry-enable.md?tabs=net)
* [Java](java-in-process-agent.md)
* [Node.JS](opentelemetry-enable.md?tabs=nodejs)
* [Python](opentelemetry-enable.md?tabs=python)

## <a name="enable-via-opencensus"></a>OpenCensus를 통해 사용하도록 설정

Application Insights SDK 외에, Application Insights는 [OpenCensus](https://opencensus.io/)를 통해서도 분산 추적을 지원합니다. OpenCensus는 오픈 소스, 벤더에 구애받지 않는, 라이브러리의 단일 배포판이며 서비스에 대한 메트릭 컬렉션 및 분산 추적 기능을 제공합니다. 또한 오픈 소스 커뮤니티에서 Redis, Memcached 또는 MongoDB와 같이 널리 사용되는 기술을 통해 분산 추적이 가능합니다. [Microsoft는 여러 다른 모니터링 및 클라우드 파트너와 OpenCensus에 대해 협력하고 있습니다](https://open.microsoft.com/2018/06/13/microsoft-joins-the-opencensus-project/).

[Python](opencensus-python.md) 

OpenCensus 웹 사이트에는 [Python](https://opencensus.io/api/python/trace/usage.html)과 [Go](https://godoc.org/go.opencensus.io)에 대한 API 참조 설명서는 물론 OpenCensus 사용을 위한 다양한 가이드가 있습니다. 

## <a name="next-steps"></a>다음 단계

* [OpenCensus Python 사용 가이드](https://opencensus.io/api/python/trace/usage.html)
* [애플리케이션 맵](./app-map.md)
* [엔드투엔드 성능 모니터링](../app/tutorial-performance.md)

