---
title: Azure API Management에서 API 분석 사용 | Microsoft Docs
description: Azure API Management 분석을 사용하여 API 사용량과 API 성능을 이해하고 분류할 수 있습니다.
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 11/24/2020
ms.author: apimpm
ms.openlocfilehash: da8b462eba7b5f68e345ffe5e73bd4096831935f
ms.sourcegitcommit: e6de87b42dc320a3a2939bf1249020e5508cba94
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114713057"
---
# <a name="get-api-analytics-in-azure-api-management"></a>Azure API Management API 분석 사용

Azure API Management는 API에 대한 기본 분석을 제공합니다. 다음을 포함한 여러 차원으로 API Management 인스턴스의 API 사용량과 성능을 분석합니다.

* 시간
* Geography
* API
* API 작업
* Products
* Subscriptions
* 사용자
* 요청

> [!NOTE]
> 지리 값은 IP 주소 매핑을 기반으로 하는 대략적인 값입니다.

:::image type="content" source="media/howto-use-analytics/analytics-report-portal.png" alt-text="포털의 타임라인 분석":::

분석을 사용하여 API에 대한 개괄적인 모니터링과 문제 해결을 수행할 수 있습니다. 진단과 감사에 대한 근 실시간 메트릭 및 리소스 로그를 비롯한 추가 모니터링 기능은 [자습서: 게시된 API 모니터링](api-management-howto-use-azure-monitor.md)을 참조하세요.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="analytics---portal"></a>분석 - 포털

Azure Portal을 사용하여 API Management 인스턴스에 대한 분석 데이터를 한눈에 검토합니다.

1. [Azure Portal](https://portal.azure.com)에서 API Management 인스턴스로 이동합니다. 
1. 왼쪽 메뉴의 **모니터링** 에서 **분석** 을 선택합니다.

    :::image type="content" source="media/howto-use-analytics/monitoring-menu-analytics.png" alt-text="포털에서 API Management 인스턴스에 대한 분석 선택":::  
1. 데이터의 시간 범위를 선택하거나 사용자 지정 시간 범위를 입력합니다.
1. 분석 데이터의 보고서 범주(예: **타임라인**, **지역**)를 선택합니다.
1. 필요에 따라 하나 이상의 추가 범주로 보고서를 필터링합니다.

## <a name="analytics---rest-api"></a>분석 - REST API

API Management REST API의 [보고서](/rest/api/apimanagement/2020-12-01/reports) 작업을 사용하여 API Management 인스턴스에 대한 분석 데이터를 검색하고 필터링합니다.

지원되는 작업은 API, 지역, API 작업, 제품, 요청, 구독, 시간 또는 사용자별로 보고서 레코드를 반환합니다.

## <a name="next-steps"></a>다음 단계

* API Management Azure Monitor 기능에 대한 소개는 [자습서: 게시된 API 모니터링](api-management-howto-use-azure-monitor.md)을 참조하세요.
* 자세한 HTTP 로깅 및 모니터링은 [Azure API Management, Event Hubs, Moesif를 통한 API 모니터링](api-management-log-to-eventhub-sample.md)을 참조하세요.
* [Azure API Management와 Azure Application Insights](api-management-howto-app-insights.md)의 통합에 대해 알아보세요.
