---
title: Azure Monitor 인사이트를 통해 Azure Service Bus 모니터링
description: 이 문서에서는 Service Bus 소비자에게 성능 문제를 빠르게 이해할 수 있는 Azure Monitor Service Bus 인사이트 기능을 설명합니다.
ms.topic: conceptual
ms.date: 11/16/2021
ms.openlocfilehash: 9d9702ac84fb145db14d155160e847a2a96b586e
ms.sourcegitcommit: b00a2d931b0d6f1d4ea5d4127f74fc831fb0bca9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2021
ms.locfileid: "132871777"
---
# <a name="azure-monitor---service-bus-insights"></a>Azure Monitor - Service Bus 인사이트
Azure Service Bus 인사이트는 통합 대화형 환경으로 모든 Service Bus 리소스의 전반적인 성능, 실패, 용량 및 운영 상태를 보여 줍니다. 이 문서는 새로운 모니터링 환경의 이점과 조직의 고유한 요구 사항에 맞게 환경을 수정하고 조정 하는 방법을 이해하는 데 도움이 됩니다.

## <a name="introduction"></a>소개

환경을 살펴보기 전에, 환경에서 어떤 방식으로 정보가 제공되고 시각화되는지 이해해야 합니다. 

다음을 제공합니다.

* 단일 위치의 모든 구독에서 리소스를 Service Bus 대규모 **관점에서** 평가하려는 구독 및 리소스만 선택적으로 범위로 할 수 있습니다.

* 특정 Service Bus 리소스의 **드릴다운 분석을** 통해 문제를 진단하거나 범주별로 자세한 분석(사용률, 실패, 용량 및 작업)을 수행할 수 있습니다. 이러한 옵션 중 하나를 선택하면 관련 정보에 대한 심층 보기를 제공합니다.

* **사용자 지정** - 이 환경은 Azure Monitor 통합 문서 템플릿을 기반으로 하므로 표시할 메트릭을 변경하고, 제한과 일치하는 임계값을 수정하거나 설정한 다음, 사용자 지정 통합 문서에 저장할 수 있습니다. 그런 다음, 통합 문서의 차트를 Azure 대시보드에 고정할 수 있습니다.  

이 기능을 사용하거나 구성할 필요는 없습니다. 이러한 Service Bus 메트릭은 기본적으로 수집됩니다.

>[!NOTE]
>이 기능은 무료로 사용할 수 있으며 [Azure Monitor 가격 책정 세부 정보](https://azure.microsoft.com/pricing/details/monitor/) 페이지에 설명된 것처럼 Azure Monitor 필수 기능을 구성하거나 설정하는 경우에만 요금이 부과됩니다.

## <a name="view-utilization-and-performance-metrics-for-service-bus"></a>Service Bus 대한 사용률 및 성능 메트릭 보기

모든 구독에서 Service Bus 리소스의 사용률 및 성능을 보려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. **모니터** 를 검색하고 **모니터** 를 선택합니다.

    !["모니터"라는 단어가 포함된 검색 상자와 속도계 스타일 이미지를 사용하는 서비스 "모니터"라고 적힌 드롭다운](./media/service-bus-insights/search-monitor.png)

3. 왼쪽 메뉴에서 **Service Bus(미리 보기)** 를 선택합니다.

    :::image type="content" source="./media/service-bus-insights/service-bus.png" lightbox="./media/service-bus-insights/service-bus.png" alt-text="Service Bus Insights 페이지의 스크린샷.":::

### <a name="overview"></a>개요

**개요에서** 테이블에는 대화형 Service Bus 메트릭이 표시됩니다. 다음 드롭다운 목록에서 선택하는 옵션을 기준으로 결과를 필터링할 수 있습니다.

* **구독** - Service Bus 리소스가 있는 구독만 나열됩니다.  

* **Service Bus** - 모든 리소스, 하위 집합 또는 단일 Service Bus 리소스를 선택할 수 있습니다.

* **시간 범위** - 기본적으로는 선택한 항목을 기준으로 지난 4시간의 정보를 표시합니다.

드롭다운 목록 아래의 카운터 타일은 선택한 구독에 있는 Service Bus 리소스의 총 수를 롤업합니다. 트랜잭션 메트릭을 보고하는 통합 문서의 열에 대한 조건부 색 구분 또는 열 맵이 있습니다. 가장 진한 색이 가장 높은 값을 가지며 밝은 색은 가장 작은 값을 기준으로 합니다. 

**개요** 페이지에는 활성 연결에 대한 다음 값과 타임라인도 표시됩니다.

| 메트릭 이름 |  Description |
| ---------- | ---------- | 
|활성 연결 수| 네임스페이스와 엔터티의 활성 연결 수입니다. 해당 메트릭의 값은 지정 시간 값입니다. 해당 시점 직후에 활성화된 연결은 메트릭에 반영되지 않을 수 있습니다. |
|열린 연결 | 열린 연결 수입니다. |
|닫힌 연결 | 닫힌 연결 수입니다. |
|성공한 요청| 지정된 기간 동안 Service Bus 서비스에 대한 성공한 요청 수입니다. |
|제한된 요청| 사용량 초과로 인해 제한된 요청 수입니다. |  

이러한 메트릭에 대한 자세한 내용은 [Service Bus 메트릭을 참조하세요.](../../service-bus-messaging/monitor-service-bus-reference.md)

파란색으로 강조 표시된 Service Bus 리소스 이름을 선택하면 해당 리소스에 대한 기본 **개요로** 표시됩니다. 

### <a name="requests"></a>요청

페이지 맨 위에서 **요청을** 선택하면 통합 문서 템플릿의 **요청** 부분이 열립니다. 

:::image type="content" source="./media/service-bus-insights/service-bus-requests.png" lightbox="./media/service-bus-insights/service-bus-requests.png" alt-text="Service Bus Insights 페이지의 요청 탭 스크린샷.":::

이 페이지에는 다음 값과 들어오는 요청에 대한 타임라인도 표시됩니다. 

| 메트릭 이름 |  Description |
| ---------- | ---------- |
| 들어오는 요청| 지정된 기간 동안 Service Bus 서비스에 대한 요청 수입니다. | 
|성공한 요청| 지정된 기간 동안 Service Bus 서비스에 대한 성공한 요청 수입니다. |
|제한된 요청| 사용량 초과로 인해 제한된 요청 수입니다. |  
|서버 오류| 지정된 기간 동안 Service Bus 서비스에서 오류로 인해 처리되지 않은 요청 수입니다. |
|사용자 오류 | 지정된 기간 동안 사용자 오류로 인해 처리되지 않은 요청 수입니다. |

이러한 메트릭에 대한 자세한 내용은 [Service Bus - 요청 메트릭을 참조하세요.](../../service-bus-messaging/monitor-service-bus-reference.md#request-metrics)


### <a name="messages"></a>메시지

페이지 맨 위에서 **메시지를** 선택하면 통합 문서 템플릿의 **메시지** 부분이 열립니다. 

:::image type="content" source="./media/service-bus-insights/service-bus-messages.png" lightbox="./media/service-bus-insights/service-bus-messages.png" alt-text="Service Bus Insights 페이지의 메시지 탭 스크린샷.":::

이 페이지에는 다음 값과 들어오는 메시지의 타임라인도 표시됩니다. 

| 메트릭 이름 |  Description | 
| ---------- | ---------- | 
|들어오는 메시지| 지정된 기간 동안 Service Bus 보낸 메시지 수입니다. 해당 메트릭은 자동 전송되는 메시지를 포함하지 않습니다. | 
|보내는 메시지| 지정된 기간 동안 Service Bus 받은 메시지 수입니다. |
| 메시지| 큐/토픽에 있는 메시지 수 | 
| 예약된 메시지| 큐/토픽에서 예약된 메시지 수입니다. |

이러한 메트릭에 대한 자세한 내용은 [Service Bus - 메시지 메트릭을 참조하세요.](../../service-bus-messaging/monitor-service-bus-reference.md#message-metrics)


## <a name="view-from-a-service-bus-resource"></a>Service Bus 리소스에서 보기

1. Azure Portal **Service Bus** 검색하고 기존 Service Bus 네임스페이스를 선택합니다.
2. Service Bus **네임스페이스** 페이지의 왼쪽 메뉴에 있는 **모니터링** 섹션에서 **Insights(미리 보기)** 또는 **통합 문서를** 선택하여 추가 분석을 수행합니다. **Insights(미리 보기)** 를 선택하면 다음 페이지가 표시됩니다. 

    :::image type="content" source="./media/service-bus-insights/service-bus-overview.png" lightbox="./media/service-bus-insights/service-bus-overview.png" alt-text="Service Bus Insights 개요의 스크린샷.":::

이 페이지에는 Azure Monitor **- Service Bus(미리 보기)** 페이지에 표시되는 것과 비슷한 **탭(개요,** **요청** 및 **메시지)이** 표시됩니다. 그러나 Azure Monitor 페이지의 탭과 비교하여 이러한 탭에 대한 자세한 정보를 볼 수 있습니다. 

> [!NOTE]
> 이 페이지에 표시되는 메트릭에 대한 자세한 내용은 [메트릭 Service Bus 참조하세요.](../../service-bus-messaging/monitor-service-bus-reference.md)

### <a name="overview-tab"></a>개요 탭
선택한 **시간 범위의** Service Bus **네임스페이스에** 대해 활성 연결 수, 열린 연결 수, 성공한 요청, 제한된 요청 및 들어오는 메시지 정보가 표시됩니다.

또한 선택한 시간 범위에서 시간별 네임스페이스에 대한 활성 연결, 열린 연결 및 닫힌 연결, 성공한 요청, 제한된 요청 및 들어오는 & 나가는 메시지와 같은 정보가 있는 차트가 표시됩니다. 예를 들어 **활성 연결** 차트에는 지난 4시간 동안의 활성 연결 수(선택한 시간 범위)가 표시됩니다.

:::image type="content" source="./media/service-bus-insights/service-bus-overview.png" lightbox="./media/service-bus-insights/service-bus-overview.png" alt-text="Service Bus Insights 개요의 스크린샷.":::

### <a name="requests-tab"></a>요청 탭
선택한 **시간 범위의** Service Bus **네임스페이스에** 대한 다음 정보가 표시됩니다. 들어오는 요청 수, 성공한 요청, 서버 오류, 사용자 오류 및 제한된 요청입니다.

이 섹션의 다음 표에서는 Service Bus 네임스페이스의 각 리소스(큐 또는 토픽)에 대한 이러한 값을 구분합니다.

또한 이 페이지에는 선택한 시간 범위에서 시간별 네임스페이스에 대한 다음 정보를 보여주는 차트가 있습니다.

- 네임스페이스의 모든 큐 및 토픽과 네임스페이스의 각 큐 또는 토픽(세그먼트)에 대해 들어오고 성공하며 제한된 요청
- 네임스페이스의 모든 큐 및 토픽과 네임스페이스의 각 큐 또는 토픽(세그먼트)에 대한 서버 및 사용자 오류입니다. 
 

### <a name="messages-tab"></a>메시지 탭
선택한 **시간 범위** 에 대 한 Service Bus **네임 스페이스** 에 대 한 다음 정보가 표시 됩니다. 

- 네임 스페이스의 모든 큐 및 항목에 대 한 들어오는 메시지, 보내는 메시지 및 배달 못 한 메시지 수입니다.
- 네임 스페이스의 모든 큐 및 항목에 대 한 평균 메시지 수, 활성 메시지 수, 예약 된 메시지 수입니다.

이 섹션 뒤에 나오는 표에서는 Service Bus 네임 스페이스의 각 리소스 (큐 또는 항목)에 대 한 이러한 값의 중단을 보여 줍니다.

이 페이지에는 선택한 시간 범위에서 시간 경과에 따른 네임 스페이스에 대 한 다음 정보를 보여 주는 차트도 있습니다. 

- 네임 스페이스의 모든 엔터티 (큐 및 토픽)에 대 한 들어오고 나가는, 모든 활성, 예약 된 및 배달 못 한 메시지 메시지입니다. 
- 세그먼트 또는 엔터티 (네임 스페이스의 큐 또는 토픽) 별로 위의 메트릭을 분석 합니다.


### <a name="time-range"></a>시간 범위

기본적으로 **시간 범위** 필드는 **지난 4 시간 동안의** 데이터를 표시 합니다. 시간 범위를 수정하여 최근 5분에서 최근 7일까지 중 원하는 데이터를 표시할 수 있습니다. 시간 범위 선택기에는 시작/종료 날짜를 입력하여 선택한 계정에서 사용 가능한 데이터를 기반으로 사용자 지정 시간 프레임을 볼 수 있는 **사용자 지정** 모드도 포함되어 있습니다.

:::image type="content" source="./media/service-bus-insights/service-bus-time-range.png" alt-text="시간 범위 선택을 보여 주는 스크린샷":::

## <a name="pin-and-export"></a>고정 및 내보내기

섹션의 오른쪽 위에 있는 압정 아이콘을 선택하여 메트릭 섹션 중 하나를 [Azure 대시보드](../../azure-portal/azure-portal-dashboards.md)에 고정할 수 있습니다.

:::image type="content" source="./media/service-bus-insights/pin.png" alt-text="섹션 위쪽의 고정 단추를 보여 주는 스크린샷":::

데이터를 Excel 형식으로 내보내려면 압정 아이콘 왼쪽에 있는 아래쪽 화살표 아이콘을 선택합니다.

:::image type="content" source="./media/service-bus-insights/export-excel.png" alt-text="도구 모음에서 통합 문서 내보내기 단추의 선택을 보여 주는 스크린샷":::


## <a name="customize-service-bus-insights"></a>Service Bus insights 사용자 지정

이 환경은 Azure Monitor 통합 문서 템플릿 위에 빌드되어 있으므로 도구 모음에서 **사용자 지정** 을 선택 하 여 사용자 지정할 수 있습니다. 

:::image type="content" source="./media/service-bus-insights/customize.png" alt-text="도구 모음에서 사용자 지정 단추의 선택을 보여 주는 스크린샷":::

**Service Bus 개요-Azure Monitor** 페이지에서 **편집** 을 선택 하 여 수정 하 고 수정 된 버전의 복사본을 사용자 지정 통합 문서에 **저장** 합니다. 도구 모음에서 **통합 문서** 를 선택 하 여 저장 한 통합 문서의 갤러리를 볼 수 있습니다.

:::image type="content" source="./media/service-bus-insights/workbooks.png" alt-text="통합 문서 선택 도구 모음 단추를 보여 주는 스크린샷":::

## <a name="troubleshooting"></a>문제 해결

문제 해결 지침은 전용 통합 문서 기반 인사이트 [문제 해결 문서](troubleshoot-workbooks.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [메트릭 경고](../alerts/alerts-metric.md) 및 [서비스 상태 알림](../../service-health/alerts-activity-log-service-notifications-portal.md)을 구성하여 문제 발견에 도움이 되는 자동 경고를 설정합니다.

* [Azure Monitor 통합 문서를 사용하여 대화형 보고서 만들기](../visualize/workbooks-overview.md)를 검토하여 통합 문서에서 지원하도록 디자인된 시나리오, 새 보고서를 작성하고 기존 보고서를 사용자 지정하는 방법 등을 알아보세요.
