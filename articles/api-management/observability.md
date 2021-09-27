---
title: Azure API Management의 가시성 | Microsoft Docs
description: Azure API Management의 모든 가시성 옵션에 대한 개요입니다.
services: api-management
documentationcenter: ''
author: begim
manager: alberts
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/01/2020
ms.author: danlep
ms.openlocfilehash: 02a33de6ecb83f87627b84e8606e4dcc88108679
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128664545"
---
# <a name="observability-in-azure-api-management"></a>Azure API Management의 가시성

가시성은 시스템이 생성하는 데이터에서 시스템의 내부 상태를 파악하는 기능이며 해당 데이터를 살펴보고 어떤 일이 왜 발생했는지 묻는 질문에 대답하는 기능입니다. 

Azure API Management를 사용하면 조직이 모든 API 관리를 중앙 집중화하는 데 도움이 됩니다. 모든 API 트래픽의 단일 진입점으로 사용되기 때문에 API를 관찰하는 데 이상적인 위치입니다. 

## <a name="observability-tools"></a>가시성 도구

아래 표에는 API 관찰을 위해 API Management에서 지원하는 모든 도구가 요약되어 있습니다. 각 도구는 하나 이상의 시나리오에 유용합니다.

| 도구        | 유용한 분야    | 데이터 지연 | 보존 | 샘플링 | 데이터 종류 | 사용|
|:------------- |:-------------|:---- |:----|:---- |:--- |:---- 
| **[API 검사기](api-management-howto-api-inspector.md)** | 테스트 및 디버깅 | 인스턴트 | 마지막 100개 추적 | 요청에 따라 튜닝됨 | 요청 추적 | 항상
| **기본 제공 분석** | 보고 및 모니터링 | 분 | 수명 | 100% | 보고서 및 로그 | 항상 |
| **[Azure Monitor 메트릭](api-management-howto-use-azure-monitor.md)** | 보고 및 모니터링 | 분 | 93일(연장하려면 업그레이드) | 100% | 메트릭 | 항상 |
| **[Azure Monitor 로그](api-management-howto-use-azure-monitor.md)** | 보고, 모니터링, 디버그 | 분 | 31일/5GB(연장하려면 업그레이드) | 100%(조정 가능) | 로그 | Optional |
| **[Azure Application Insights](api-management-howto-app-insights.md)** | 보고, 모니터링, 디버그 | 초 | 90일/5GB(연장하려면 업그레이드) | 사용자 지정 | 로그, 메트릭 | Optional |
| **[Azure 이벤트 허브를 통해 로깅](api-management-howto-log-event-hubs.md)** | 사용자 지정 시나리오 | 초 | 관리되는 사용자 | 사용자 지정 | 사용자 지정 | Optional |

### <a name="self-hosted-gateway"></a>자체 호스팅 게이트웨이

위에서 언급한 모든 도구는 클라우드의 관리형 게이트웨이에서 지원됩니다. [자체 호스팅 게이트웨이](self-hosted-gateway-overview.md)에서는 현재 Azure Monitor로 진단 로그를 보내지 않습니다. 하지만 자체 호스팅 게이트웨이가 배포되는 위치에서 로컬로 로그를 구성하고 보존할 수 있습니다. 자세한 내용은 [자체 호스팅 게이트웨이의 클라우드 메트릭 및 로그 구성](how-to-configure-cloud-metrics-logs.md)과 [자체 호스팅 게이트웨이의 로컬 메트릭 및 로그 구성](how-to-configure-local-metrics-logs.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

* [자습서에 따라 API Management에 대한 자세한 내용](import-and-publish.md)을 알아봅니다.
