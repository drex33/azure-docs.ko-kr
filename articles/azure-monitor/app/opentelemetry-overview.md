---
title: Azure Monitor OpenTelemetry 개요
description: Azure Monitor와 함께 OpenTelemetry를 사용 하는 방법에 대 한 개요를 제공 합니다.
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: a839437bd572946a6e6c2f62f2658ff4696db4e9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233491"
---
# <a name="opentelemetry-overview"></a>OpenTelemetry 개요

Microsoft는 원격 분석 계측의 미래에 [OpenTelemetry](https://opentelemetry.io/) 를 도입 하 고 있습니다. 고객은 공급 업체 중립적 계측을 요청 했으며, OpenTelemetry 커뮤니티와 협력 하 여 다양 한 언어에서 일관 된 Api/Sdk를 느낄 합니다.

Microsoft에서는 [OpenCensus](https://opencensus.io/) 및 [OpenTracing](https://opentracing.io/)의 두 가지 이전 인기 있는 오픈 소스 원격 분석 프로젝트에서 프로젝트 관련자와 협력 하 여 단일 프로젝트 (OpenTelemetry)를 만들 수 있습니다. OpenTelemetry에는 모든 주요 클라우드 및 APM (응용 프로그램 성능 관리) 공급 업체의 기여와 Microsoft가 플래티넘 구성원 인 [CNCF (Cloud Native 컴퓨팅 Foundation)](https://www.cncf.io/) 내에서의 기여를 포함 합니다.

## <a name="concepts"></a>개념

응용 프로그램을 관찰 하기 위해 수집 된 데이터를 세 가지 형식 또는 "핵심 요소"로 나눌 수 있습니다.
1.  분산 추적
2.  메트릭
3.  로그

처음에 OpenTelemetry 커뮤니티는 분산 추적에 걸렸습니다. 메트릭 및 로그는 아직 진행 중입니다. 전체 관찰성 스토리는 세 가지 핵심 요소를 모두 포함 하지만 현재 [.net, Python 및 JavaScript에 대 한 Azure Monitor OpenTelemetry 기반 내보내기 **미리 보기** 제품은](opentelemetry-enable.md) **분산 추적만 포함** 합니다.

[OpenTelemetry 커뮤니티 웹 사이트](https://opentelemetry.io/docs/concepts/data-sources/), [OpenTelemetry 사양](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/overview.md)및 [분산 시스템 관찰성](https://www.oreilly.com/library/view/distributed-systems-observability/9781492033431/ch04.html) Cindy Sridharan를 포함 하 여 세 가지 핵심 요소의 세부 정보를 설명 하는 여러 소스가 있습니다.

다음 섹션에서는 몇 가지 원격 분석 컬렉션 기본 사항을 다룹니다.

### <a name="instrumenting-your-application"></a>응용 프로그램 계측

기본 수준에서 "계측"은 단순히 응용 프로그램에서 원격 분석을 캡처할 수 있도록 하는 것입니다.

응용 프로그램을 계측 하는 방법에는 다음 두 가지가 있습니다.
1.  수동 계측
2.  자동 계측 (자동 계측)

수동 계측은 OpenTelemetry API를 코딩 하는 것입니다. 최종 사용자의 컨텍스트에서는 일반적으로 응용 프로그램에 언어별 SDK를 설치 하는 것을 의미 합니다. 수동 계측 패키지는 [.net, Python 및 JavaScript에 대 한 Azure Monitor OpenTelemetry 기반 내보내기 **미리 보기** 제품](opentelemetry-enable.md)으로 구성 됩니다.

> [!IMPORTANT]
> "수동"은 분산 추적의 범위를 정의 하기 위해 복잡 한 코드를 작성 해야 하는 것은 **아닙니다** (옵션 그대로 유지). OpenTelemetry 기여자들이 유지 관리하는 풍부하고 점점 확장되는 계측 라이브러리 세트를 사용하면 일반적인 프레임워크 및 라이브러리에서 원격 분석 신호를 손쉽게 캡처할 수 있습니다. OpenTelemetry 계측 라이브러리의 하위 집합은 Azure Monitor에서 지원 되며 고객 의견을 통해 알려 받습니다. 또한 [OpenTelemetry를 사용 하 여 가장 인기 있는 Azure 서비스 sdk를 계측](https://devblogs.microsoft.com/azure-sdk/introducing-experimental-opentelemetry-support-in-the-azure-sdk-for-net/)하도록 노력 하 고 있습니다.

반면, 자동 계측은 응용 프로그램 코드를 건드리지 않고도 구성을 통해 원격 분석 수집을 사용 하도록 설정 합니다. 더 편리 하 게 사용할 수 있는 것은 아니지만, 모든 언어에서 사용할 수 있는 것은 아닙니다. Azure Monitor의 OpenTelemetry 기반 자동 계측 제품은 [Java 3.X OpenTelemetry 기반 GA 제품](java-in-process-agent.md)으로 구성 되며 고객의 의견에 따라 계속 해 서 투자를 받습니다. OpenTelemetry 커뮤니티는 c # 및 Python 자동 계측을 통해 실험 하 고 있으며, Azure Monitor은 가까운 곳에서 간단 하 고 효율적인 수동 계측을 만드는 데 중점을 두었습니다.

### <a name="sending-your-telemetry"></a>원격 분석 보내기

또한 두 가지 방법으로 데이터를 Azure Monitor (또는 모든 공급 업체)에 보낼 수 있습니다.
1. 직접 내보내기
2. 에이전트를 통해

직접 내보내기는 응용 프로그램의 코드에서 직접 원격 분석을 Azure Monitor의 수집 끝점으로 전송 합니다. 이 방법의 주요 이점은 온 보 딩 단순성입니다.

**모든 Azure Monitor 현재 지원 되는 OpenTelemetry 기반 제품은 직접 내보내기를 사용** 합니다. 

또는 에이전트 [를 통해 원격](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/protocol/README.md)분석을 보내는 것은 OpenTelemetry에서 지원 되는 모든 언어에 대 한 경로를 제공 하 여를 통해 Azure Monitor으로 보냅니다. 이상 기능을 통해 고객은 [지원 되는 언어](platforms.md)이외의 언어로 작성 된 응용 프로그램을 관찰할 수 있습니다. 

> [!NOTE]
> Microsoft에서 아직 응용 프로그램 모니터링에 대 한 "에이전트 Via" 방식을 공식적으로 지원 하지 않더라도 일부 고객은 [OpenTelemetry 수집기](https://github.com/open-telemetry/opentelemetry-collector/blob/main/docs/design.md) 를 에이전트로 사용 하기 시작 했습니다. 그 동안 오픈 소스 커뮤니티에서는 일부 고객이 Azure Monitor Application Insights에 데이터를 전송하는 데 사용하는 OpenTelemetry 수집기 Azure Monitor 내보내기 도구에 기여해 왔습니다.

## <a name="terms"></a>항

OpenTelemetry 사양에서 [용어집](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/glossary.md) 을 참조 하세요.

Application Insights의 일부 레거시 용어는 OpenTelemetry의 업계 수렴에 따라 혼란 스 러 울 수 있습니다. 아래 표에서는 이러한 차이를 강조 표시 합니다. 결국 Application Insights 용어는 OpenTelemetry 용어로 바뀝니다.

Application Insights | OpenTelemetry
------ | ------
자동 수집기 | 계측 라이브러리
채널 | 내보내기  
코드 없는/에이전트 기반 |  자동 계측
추적 | 로그


## <a name="next-step"></a>다음 단계

다음 페이지는 Microsoft의 OpenTelemetry 기반 제품을 사용 하도록 설정 하 고 구성 하기 위한 언어별 지침으로 구성 됩니다. 중요 한 점은 OpenTelemetry가 프로젝트에 적합 한지 여부를 확인할 수 있도록 각 제품의 사용 가능한 기능 및 제한 사항을 공유 하는 것입니다.
- [.NET](opentelemetry-enable.md) 
- [Java](java-in-process-agent.md)
- [JavaScript](opentelemetry-enable.md)
- [Python](opentelemetry-enable.md)
