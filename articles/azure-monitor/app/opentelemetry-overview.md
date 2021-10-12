---
title: Azure Monitor 개요가 있는 OpenTelemetry
description: Azure Monitor OpenTelemetry를 사용하는 방법에 대한 개요를 제공합니다.
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 268d7d8372fef1568a369f956e3caa91749f0e8e
ms.sourcegitcommit: d2875bdbcf1bbd7c06834f0e71d9b98cea7c6652
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129859539"
---
# <a name="opentelemetry-overview"></a>OpenTelemetry 개요

Microsoft는 원격 분석 계측의 미래로 [OpenTelemetry를](https://opentelemetry.io/) 수용하게 되어 기쁩니다. 고객들은 공급업체 중립 계측을 요청했으며, OpenTelemetry 커뮤니티와 협력하여 언어 간에 일관된 API/SDK를 만들게 되어 기쁘게 생각합니다.

Microsoft는 이전에 인기 있는 두 개의 오픈 소스 원격 분석 프로젝트인 [OpenCensus](https://opencensus.io/) 및 [OpenTracing의](https://opentracing.io/)프로젝트 관련자와 협력하여 단일 프로젝트인 OpenTelemetry를 만들었습니다. OpenTelemetry는 모든 주요 클라우드 및 APM(애플리케이션 성능 관리) 공급업체의 기여를 포함하며 Microsoft가 2019년 멤버로 있는 [CNCF(Cloud Native Computing Foundation)에](https://www.cncf.io/) 의해 보관됩니다.

## <a name="concepts"></a>개념

애플리케이션을 관찰하기 위해 수집된 데이터인 원격 분석은 세 가지 유형 또는 "핵심 요소"로 나눌 수 있습니다.
1.  분산 추적
2.  메트릭
3.  로그

처음에 OpenTelemetry 커뮤니티는 분산 추적을 시작했습니다. 메트릭 및 로그는 아직 진행 중입니다. 완전한 관찰 가능성 스토리는 세 가지 핵심 요소 모두를 포함하지만, 현재 [.NET, Python 및 JavaScript용 Azure Monitor OpenTelemetry 기반 내보내기 **미리 보기** 제품에는](opentelemetry-enable.md) **분산 추적만 포함됩니다.**

[OpenTelemetry 커뮤니티 웹 사이트, OpenTelemetry](https://opentelemetry.io/docs/concepts/data-sources/) [Specifications](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md)및 [Distributed Systems Observability](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html) by Sridharan을 포함하여 세 가지 핵심 요소에 대해 자세히 설명하는 여러 원본이 있습니다.

다음 섹션에서는 몇 가지 원격 분석 컬렉션 기본을 다룹니다.

### <a name="instrumenting-your-application"></a>애플리케이션 계측

기본 수준에서 "계측"은 단순히 애플리케이션이 원격 분석을 캡처할 수 있도록 합니다.

애플리케이션을 계측하는 방법에는 다음 두 가지가 있습니다.
1.  수동 계측
2.  자동 계측(자동 계측)

수동 계측은 OpenTelemetry API에 대해 코딩됩니다. 최종 사용자의 컨텍스트에서 일반적으로 애플리케이션에 언어별 SDK를 설치하는 것을 의미합니다. 수동 계측 패키지는 [.NET, Python 및 JavaScript용 Azure Monitor OpenTelemetry 기반 내보내기 **미리 보기** 제품으로](opentelemetry-enable.md)구성됩니다.

> [!IMPORTANT]
> "수동"은 분산 추적에 대한 범위를 정의하는 복잡한 코드를 작성해야 한다는 **것을** 의미하지는 않습니다(옵션으로 유지). OpenTelemetry 기여자들이 유지 관리하는 풍부하고 점점 확장되는 계측 라이브러리 세트를 사용하면 일반적인 프레임워크 및 라이브러리에서 원격 분석 신호를 손쉽게 캡처할 수 있습니다. OpenTelemetry 계측 라이브러리의 하위 집합은 고객 피드백을 통해 Azure Monitor 지원됩니다. 또한 가장 인기 있는 Azure 서비스 SDK를 계측하여 OTLP(OpenTelemetry Protocol)를 내보내고 Azure Monitor 최적화하기 위해 노력하고 있습니다.

반면에 자동 계측은 애플리케이션의 코드를 건드리지 않고 구성을 통해 원격 분석 수집을 사용하도록 설정됩니다. 더 편리하지만 구성이 덜 가능하고 모든 언어에서 사용할 수 있는 것은 아닙니다. Azure Monitor OpenTelemetry 기반 자동 계측 제품은 [Java 3.X OpenTelemetry 기반 GA 제품으로](java-in-process-agent.md)구성되며, 고객 피드백을 통해 지속적으로 투자하고 있습니다. OpenTelemetry 커뮤니티도 C# 및 Python 자동 계측을 실험하고 있지만 Azure Monitor 단기간에 간단하고 효과적인 수동 계측 스토리를 만드는 데 중점을 두고 있습니다.

### <a name="sending-your-telemetry"></a>원격 분석 보내기

Azure Monitor(또는 공급업체)에 데이터를 보내는 방법도 두 가지가 있습니다.
1. 직접 내보내기
2. 에이전트를 통해

직접 내보내기는 프로세스 내 원격 분석(애플리케이션의 코드에서)을 Azure Monitor의 Azure Monitor 엔드포인트로 직접 보냅니다. 이 방법의 주요 이점은 온보딩 단순성입니다.

**현재 지원되는 모든 Azure Monitor OpenTelemetry 기반 제품은 직접 내보내기** 를 사용합니다. 

또는 에이전트를 통해 원격 분석을 보내면 [OTLP](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md)를 통해 Azure Monitor 보낼 OpenTelemetry 지원 언어의 경로가 제공됩니다. 이렇게 하면 고객이 [지원되는 언어](platforms.md) 이외의 언어로 작성된 애플리케이션을 관찰할 수 있습니다. 

> [!NOTE]
> 일부 고객은 Microsoft가 아직 애플리케이션 모니터링을 위해 "에이전트를 통해" 접근 방식을 공식적으로 지원하지 않더라도 [OpenTelemetry-Collector를](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) 에이전트 대안으로 사용하기 시작했습니다. 그 동안 오픈 소스 커뮤니티에서는 일부 고객이 Azure Monitor Application Insights에 데이터를 전송하는 데 사용하는 OpenTelemetry 수집기 Azure Monitor 내보내기 도구에 기여해 왔습니다.

## <a name="terms"></a>용어

OpenTelemetry 사양의 [용어집을](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md) 참조하세요.

Application Insights 일부 레거시 용어는 OpenTelemetry의 산업 수렴을 고려할 때 혼동됩니다. 아래 표에는 이러한 차이점이 강조 표시됩니다. 결국 애플리케이션 Insights 용어는 OpenTelemetry 용어로 대체됩니다.

Application Insights | OpenTelemetry
------ | ------
추적   | 로그
채널   | 수출상  
코드리스/에이전트 기반   | 자동 계측

## <a name="next-step"></a>다음 단계

다음 페이지는 Microsoft의 OpenTelemetry 기반 제품을 사용하도록 설정하고 구성하는 언어별 지침으로 구성됩니다. 중요한 점은 OpenTelemetry가 프로젝트에 적합한지 확인할 수 있도록 각 제품의 사용 가능한 기능 및 제한 사항을 공유합니다.
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
