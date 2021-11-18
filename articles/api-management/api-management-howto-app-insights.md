---
title: Azure Application Insights와 Azure API Management 통합
titleSuffix: Azure API Management
description: Azure Application Insights에서 Azure API Management의 이벤트를 기록하고 보는 방법을 알아봅니다.
services: api-management
author: dlepow
ms.service: api-management
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/27/2021
ms.author: danlep
ms.openlocfilehash: 0833e80c17294bc50489f37b21a0b605a0b0ac9d
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132717509"
---
# <a name="how-to-integrate-azure-api-management-with-azure-application-insights"></a>Azure Application Insights와 Azure API Management를 통합하는 방법

Azure 애플리케이션 Insights를 Azure API Management와 쉽게 통합할 수 있습니다. Azure 애플리케이션 Insights는 여러 플랫폼에서 앱을 빌드하고 관리 하는 웹 개발자를 위한 확장 가능한 서비스입니다. 이 가이드에서는 다음을 수행 합니다.
* API Management에 대 한 Application Insights 통합의 모든 단계를 안내 합니다.
* API Management 서비스 인스턴스에 대 한 성능 영향을 줄이기 위한 전략에 대해 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure API Management 인스턴스가 필요 합니다. [하나](get-started-create-service-instance.md) 를 먼저 만듭니다.

## <a name="create-an-application-insights-instance"></a>Application Insights 인스턴스 만들기

Application Insights를 사용 하려면 [Application Insights 서비스의 인스턴스를 만듭니다](../azure-monitor/app/create-new-resource.md). Azure Portal를 사용 하 여 인스턴스를 만들려면 [작업 영역 기반 Application Insights 리소스](../azure-monitor/app/create-workspace-resource.md)를 참조 하세요.

## <a name="create-a-connection-between-application-insights-and-api-management"></a>Application Insights와 API Management 간의 연결 만들기

1. **Azure Portal** 에서 **Azure API Management 서비스 인스턴스** 로 이동합니다.
1. 왼쪽 메뉴에서 **Application Insights** 를 선택합니다.
1. **+추가** 를 선택합니다.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-1.png" alt-text="새 연결을 추가할 위치를 보여 주는 스크린샷":::
1. 앞에서 만든 **Application Insights** 인스턴스를 선택 하 고 간단한 설명을 제공 합니다.
1. Application Insights에서 API Management 인스턴스의 [가용성 모니터링](../azure-monitor/app/monitor-web-app-availability.md)을 사용하도록 설정하려면, **가용성 모니터 추가** 확인란을 선택합니다.
    * 이 설정은 API Management 서비스 엔드포인트가 응답하는지 여부를 주기적으로 확인합니다. 
    * 결과는 Application Insights 인스턴스의 **가용성** 창에 표시됩니다.
1. **만들기** 를 선택합니다.
1. 이제 계측 키를 사용 하 여 새 Application Insights 로거가 목록에 표시 되는지 확인 합니다.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-logger-2.png" alt-text="계측 키를 사용하여 새로 만든 Application Insights 로거를 볼 수 있는 위치를 보여 주는 스크린샷":::

> [!NOTE]
> 내부적으로, Application Insights 인스턴스의 계측 키를 포함하는 API Management 인스턴스에 [로거](/rest/api/apimanagement/2020-12-01/logger/create-or-update) 엔터티가 생성됩니다.

## <a name="enable-application-insights-logging-for-your-api"></a>API에 대한 Application Insights 로깅 사용

1. **Azure Portal** 에서 **Azure API Management 서비스 인스턴스** 로 이동합니다.
1. 왼쪽 메뉴에서 **API** 를 선택합니다.
1. API를 클릭합니다(여기서는 **데모 회의 API**). 구성된 경우 버전을 선택합니다.
1. 위쪽 표시줄의 **설정** 탭으로 이동합니다.
1. **진단 로그** 섹션까지 아래로 스크롤합니다.  
    :::image type="content" source="media/api-management-howto-app-insights/apim-app-insights-api-1.png" alt-text="Application Insights 로거":::
1. **사용** 확인란을 선택합니다.
1. **대상** 드롭다운에서 연결된 로거를 선택합니다.
1. **샘플링(%)** 으로 **100** 을 입력하고, **항상 오류 로깅** 확인란을 선택합니다.
1. 나머지 설정은 그대로 둡니다.

    > [!WARNING]
    > 기본 **페이로드 바이트 수를 로그** 값 **0** 으로 재정의 하면 api의 성능이 상당히 저하 될 수 있습니다.

1. **저장** 을 선택합니다.
1. 내부적으로 이라는 [진단](/rest/api/apimanagement/2020-12-01/diagnostic/create-or-update) 엔터티는 `applicationinsights` API 수준에서 만들어집니다.

> [!NOTE]
> 요청이 성공적으로 완료 되 면 클라이언트에 전체 응답 API Management 전송 됩니다.


| 설정 이름                        | 값 형식                        | Description                                                                                                                                                                                                                                                                                                                                      |
|-------------------------------------|-----------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 사용                              | boolean                           | 이 API의 로깅이 사용되는지 여부를 지정합니다.                                                                                                                                                                                                                                                                                                |
| 대상                         | Azure Application Insights 로거 | 사용할 Azure 애플리케이션 Insights로 거를 지정 합니다.                                                                                                                                                                                                                                                                                           |
| 샘플링(%)                        | decimal                           | 0-100의 값(퍼센트)입니다. <br/> Application Insights에 기록할 요청의 백분율을 지정합니다. 0% 샘플링은 기록되는 요청이 없음을 의미하고, 100%는 모든 요청이 기록됨을 의미합니다. <br/> Application Insights에 요청을 로깅할 때 성능에 미치는 영향을 줄이려면 이 설정을 사용합니다. [성능에 미치는 영향 및 로그 샘플링](#performance-implications-and-log-sampling)을 참조하세요. |
| 항상 오류 로깅                   | boolean                           | 이 설정을 선택하면 **샘플링** 설정에 관계없이 모든 오류가 Application Insights에 로깅됩니다.   
| 클라이언트 IP 주소 기록 | |  이 설정을 선택하면 API 요청에 대한 클라이언트 IP 주소가 Application Insights에 기록됩니다.                                         |
| 자세한 정도         |                                   | 세부 정보 표시 수준을 지정합니다. 심각도 수준이 더 높은 사용자 지정 추적만 기록됩니다. 기본값은 정보입니다.      | 
| 상관 관계 프로토콜 |  |  여러 구성 요소에서 보낸 원격 분석을 상호 연결하는 데 사용할 프로토콜을 선택합니다. 기본값은 레거시입니다. <br/>자세한 내용은 [Application Insights에서 원격 분석 상관 관계](../azure-monitor/app/correlation.md)를 참조하세요.  |
| 기본 옵션: 기록할 헤더              | list                              | 요청 및 응답에 대해 Application Insights에 기록할 헤더를 지정합니다.  기본값: 헤더가 기록되지 않음                                                                                                                                                                                                             |
| 기본 옵션: 기록할 페이로드 바이트 수 | 정수                           | 요청 및 응답에 대해 Application Insights에 기록할 본문의 첫 번째 바이트 수를 지정합니다.  기본값: 0.                                                                                                                                                                                                    |
| 고급 옵션: 프런트 엔드 요청  |                                   | *프런트 엔드 요청* 이 Application Insights에 기록되는지 여부와 방법을 지정합니다. *프런트 엔드 요청* 은 Azure API Management 서비스로 들어오는 요청입니다.                                                                                                                                                                        |
| 고급 옵션: 프런트 엔드 응답 |                                   | *프런트 엔드 응답* 이 Application Insights에 기록되는지 여부와 방법을 지정합니다. *프런트 엔드 응답* 은 Azure API Management 서비스에서 나가는 응답입니다.                                                                                                                                                                   |
| 고급 옵션: 백 엔드 요청   |                                   | *백 엔드 요청* 이 Application Insights에 기록되는지 여부와 방법을 지정합니다. *백 엔드 요청* 은 Azure API Management 서비스에서 나가는 요청입니다.                                                                                                                                                                        |
| 고급 옵션: 백 엔드 응답  |                                   | *백 엔드 응답* 이 Application Insights에 기록되는지 여부와 방법을 지정합니다. *백 엔드 응답* 은 Azure API Management 서비스로 들어오는 응답입니다.                                                                                                                                                                       |

> [!NOTE]
> 다른 수준에서로 거를 지정할 수 있습니다. 
> + 단일 API로 거.
> + 모든 Api에 대 한 로거가 있습니다.
>  
> *둘 다* 지정:
> + 서로 다른로 거 인 경우 두 가지 모두 사용 됩니다 (멀티플렉싱 로그).
> + 서로 다른 설정을 사용 하는 동일한로 거를 사용 하는 경우 단일 API로 거 (보다 세분화 된 수준)가 모든 Api에 대해 하나를 재정의 합니다.

## <a name="what-data-is-added-to-application-insights"></a>Application Insights에 추가되는 데이터

Application Insights에서 받는 항목은 다음과 같습니다.

| 원격 분석 항목 | 설명 |
| -------------- | ----------- |
| *요청* | 들어오는 모든 요청에 대해 다음을 수행합니다. <ul><li>*프런트 엔드 요청*</li><li>*프런트 엔드 응답*</li></ul> |
| *종속성* | 백 엔드 서비스로 전달되는 모든 요청에 대해 다음을 수행합니다. <ul><li>*백 엔드 요청*</li><li>*백 엔드 응답*</li></ul> |
| *Exception* | 실패한 모든 요청에 대해 다음을 수행합니다. <ul><li>닫힌 클라이언트 연결로 인해 실패</li><li>API 정책의 *오류* 발생 시 섹션 트리거</li><li>4xx 또는 5xx와 일치하는 응답 HTTP 상태 코드가 있습니다.</li></ul> |
| *추적* | [추적](api-management-advanced-policies.md#Trace) 정책을 구성하는 경우 <br /> `trace` 정책의 `severity` 설정은 Application Insights 로깅의 `verbosity` 설정보다 크거나 같아야 합니다. |

### <a name="emit-custom-metrics"></a>사용자 지정 메트릭을 내다
정책을 구성하여 사용자 지정 메트릭을 내화할 수 [`emit-metric`](api-management-advanced-policies.md#emit-metrics) 있습니다. 

API Management 애플리케이션 Insights 사전 집계 메트릭을 사용할 수 있도록 하려면 서비스에서 사용자 지정 메트릭을 수동으로 사용하도록 설정해야 합니다.
1. [`emit-metric`](api-management-advanced-policies.md#emit-metrics) [API 만들기 또는 업데이트와](/rest/api/apimanagement/2021-04-01-preview/api-diagnostic/create-or-update)함께 정책을 사용합니다.
1. `"metrics":true`다른 속성과 함께 를 페이로드에 추가합니다.

> [!NOTE]
> Application Insights 인스턴스당 메트릭 및 이벤트의 최대 크기와 수에 대한 자세한 내용은 [Application Insights 제한](../azure-monitor/service-limits.md#application-insights)을 참조하세요.

## <a name="performance-implications-and-log-sampling"></a>성능에 미치는 영향 및 로그 샘플링

> [!WARNING]
> 모든 이벤트를 기록하면 들어오는 요청 속도에 따라 성능에 심각한 영향을 미칠 수 있습니다.

내부 부하 테스트에 따라 로깅 기능을 사용하도록 설정하면 요청 속도가 초당 1,000개 요청을 초과할 때 처리량이 40%-50% 감소했습니다. 애플리케이션 Insights 통계 분석을 사용하여 애플리케이션 성능을 평가하도록 설계되었습니다. 그것은 아니야:
* 감사 시스템입니다.
* 대용량 API에 대한 각 개별 요청을 로깅하는 데 적합합니다.

[ **샘플링** 설정을 조정하여](#enable-application-insights-logging-for-your-api)기록된 요청 수를 조작할 수 있습니다. 값이 100%이면 모든 요청이 기록됨을 의미하고, 0%는 기록되지 않음을 의미합니다. 

**샘플링은** 원격 분석 볼륨을 줄여 로깅의 이점을 계속 유지하면서 상당한 성능 저하를 효과적으로 방지하는 데 도움이 됩니다.

성능 문제를 개선하려면 다음을 건너뜁니다.
* 요청 및 응답 헤더.
* 본문 로깅.

## <a name="video"></a>비디오

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2pkXv]
>
>

## <a name="next-steps"></a>다음 단계

+ [Azure Application Insights](/azure/application-insights/)에 대해 자세히 알아봅니다.
+ [Azure Event Hubs에 로깅](api-management-howto-log-event-hubs.md)을 살펴봅니다.