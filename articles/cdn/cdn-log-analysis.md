---
title: Azure CDN 사용 패턴 분석
description: 이 문서에서는 Azure CDN 제품에 사용할 수 있는 다양한 유형의 분석 보고서를 설명합니다.
services: cdn
author: duongau
manager: KumudD
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2020
ms.author: duau
ms.openlocfilehash: 09ad5d0ed882b5c6f8eece565d3977acb39b3a19
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426566"
---
# <a name="analyze-azure-cdn-usage-patterns"></a>Azure CDN 사용 패턴 분석

애플리케이션에 대해 CDN을 사용하도록 설정한 후에는 CDN 사용을 모니터링하고, 전송 상태를 확인하고, 잠재적인 문제를 해결할 수 있습니다. Azure CDN은 다음과 같은 방식으로 이러한 기능을 제공합니다. 

## <a name="raw-logs-for-azure-cdn-from-microsoft"></a>Microsoft의 Azure CDN에 대한 원시 로그
표준 Microsoft 프로필을 사용하면 원시 로그를 사용하도록 설정하고 로그를 다음으로 스트림하도록 선택할 수 있습니다.

* Azure Storage
* 이벤트 허브(영문)
* Azure Log Analytics

Azure Log Analytics를 사용하면 모니터링 메트릭을 보고 경고를 설정할 수 있습니다. 

자세한 내용은 [Azure CDN HTTP 원시 로그](monitoring-and-access-log.md)를 참조하세요.


## <a name="core-analytics-via-azure-diagnostic-logs"></a>Azure 진단 로그를 통한 코어 분석

핵심 분석은 모든 가격 책정 계층의 CDN 엔드포인트에 사용할 수 있습니다. Azure 진단 로그를 사용하면 핵심 분석을 Azure Storage, 이벤트 허브 또는 Azure Monitor 로그로 내보낼 수 있습니다. Azure Monitor 로그는 사용자 구성 및 사용자 지정이 가능한 그래프가 포함된 솔루션을 제공합니다. Azure 진단 로그에 대한 자세한 내용은 [Azure 진단 로그](cdn-azure-diagnostic-logs.md)를 참조하세요.

## <a name="verizon-core-reports"></a>Verizon 코어 보고서

**Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필은 코어 보고서를 제공합니다. Verizon 보조 포털에서 코어 보고서를 볼 수 있습니다. Verizon 코어 보고서는 Azure Portal에서 **관리** 옵션을 통해 액세스할 수 있고 다양한 그래프와 보기를 제공합니다. 자세한 내용은 [Verizon의 코어 보고서](cdn-analyze-usage-patterns.md)를 참조하세요.

## <a name="verizon-custom-reports"></a>Verizon 사용자 지정 보고서

**Verizon의 Azure CDN 표준** 또는 **Verizon의 Azure CDN 프리미엄** 프로필은 사용자 지정 보고서를 제공합니다. Verizon 보조 포털에서 사용자 지정 보고서를 볼 수 있습니다. Verizon 사용자 지정 보고서는 Azure Portal에서 **관리** 옵션을 통해 액세스할 수 있습니다. 

사용자 지정 보고서는 각 에지 CNAME에 대해 전송된 적중 횟수나 데이터 수를 표시합니다. 데이터는 시간 지남에 따라 HTTP 응답 코드 또는 캐시 상태별로 그룹화됩니다. 자세한 내용은 [Verizon의 사용자 지정 보고서](cdn-verizon-custom-reports.md)를 참조하세요.

## <a name="azure-cdn-premium-from-verizon-reports"></a>Verizon의 Azure CDN Premium 보고서

**Verizon의 Azure CDN Premium** 을 사용하면 다음 보고서에도 액세스할 수 있습니다.
   * [고급 HTTP 보고서](cdn-advanced-http-reports.md)
   * [실시간 통계](cdn-real-time-stats.md)
   * [Azure CDN 에지 노드 성능](cdn-edge-performance.md)

## <a name="next-steps"></a>다음 단계
이 문서에서는 Azure CDN의 분석 보고서에 대한 다양한 옵션을 알아보았습니다.

Azure CDN 및 이 문서에 언급된 다른 Azure 서비스에 대한 자세한 내용은 다음을 참조하세요.

* [Azure CDN이란?](cdn-overview.md)
* [Azure CDN HTTP 원시 로그](monitoring-and-access-log.md)
