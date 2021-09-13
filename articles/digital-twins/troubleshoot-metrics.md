---
title: Azure Monitor의 메트릭 보기
titleSuffix: Azure Digital Twins
description: Azure Monitor에서 Azure Digital Twins 메트릭을 보는 방법을 참조하세요.
author: baanders
ms.author: baanders
ms.date: 8/4/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 8dc18662431e750301db7e3d2c4e56d5fbaea674
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770935"
---
# <a name="troubleshooting-azure-digital-twins-metrics"></a>Azure Digital Twins 문제 해결: 메트릭

이 문서에서 설명하는 메트릭은 Azure 구독에서 Azure Digital Twins 리소스의 상태에 대한 정보를 제공합니다. Azure Digital Twins 메트릭을 사용하여 Azure Digital Twins 서비스의 전반적인 상태와 연결된 리소스를 평가할 수 있습니다. 이러한 사용자 측 통계는 Azure Digital Twins의 기능을 확인하고 Azure 지원에 문의하지 않고도 문제의 근본 원인을 분석하는 데 도움이 됩니다.

메트릭은 기본적으로 사용하도록 설정됩니다. [Azure Portal](https://portal.azure.com)에서 Azure Digital Twins 메트릭을 볼 수 있습니다.

## <a name="how-to-view-azure-digital-twins-metrics"></a>Azure Digital Twins 메트릭을 보는 방법

1. Azure Digital Twins 인스턴스를 만듭니다. [인스턴스 및 인증 설정](how-to-set-up-instance-portal.md)에서 Azure Digital Twins 인스턴스를 설정하는 방법에 대한 지침을 찾을 수 있습니다.

2. [Azure Portal](https://portal.azure.com)에서 Azure Digital Twins 인스턴스를 찾습니다. 포털 검색 창에 이름을 입력하여 페이지를 열 수 있습니다. 

    인스턴스의 메뉴에서 **메트릭** 을 선택합니다.
   
    :::image type="content" source="media/troubleshoot-metrics/azure-digital-twins-metrics.png" alt-text="Azure Portal에 있는 Azure Digital Twins의 메트릭 페이지를 보여주는 스크린샷.":::

    이 페이지에는 Azure Digital Twins 인스턴스의 메트릭이 표시됩니다. 목록에서 보려는 메트릭을 선택하여 메트릭의 사용자 지정 보기를 만들 수도 있습니다.
    
3. 메뉴에서 **진단 설정** 및 **진단 설정 추가** 를 차례로 선택하여 Event Hubs 엔드포인트 또는 Azure Storage 계정으로 메트릭 데이터를 보낼 수 있습니다.

    :::image type="content" source="media/troubleshoot-diagnostics/diagnostic-settings.png" alt-text="Azure Portal에 추가할 진단 설정 페이지 및 단추를 보여주는 스크린샷.":::

    이 프로세스에 대한 자세한 내용은 [문제 해결: 진단 설정](troubleshoot-diagnostics.md)을 참조하세요.

4. 메뉴에서 **경고** 및 **+ 새 경고 규칙** 을 차례로 선택하여 메트릭 데이터에 대한 경고를 설정하도록 선택할 수 있습니다.
    :::image type="content" source="media/troubleshoot-alerts/alerts-pre.png" alt-text="Azure Portal에 추가할 경고 페이지 및 단추를 보여주는 스크린샷.":::

    이 프로세스에 대한 자세한 내용은 [문제 해결: 경고 설정](troubleshoot-alerts.md)을 참조하세요.

## <a name="azure-digital-twins-metrics-and-how-to-use-them"></a>Azure Digital Twins 메트릭 및 사용 방법

Azure Digital Twins는 인스턴스와 연결된 리소스의 상태에 대한 개요를 제공하는 몇 가지 메트릭을 제공합니다. 인스턴스 상태의 큰 그림을 그리기 위해 여러 가지 메트릭의 정보를 결합할 수도 있습니다. 

다음 표에서는 각 Azure Digital Twins 인스턴스가 추적하는 메트릭과 각 메트릭이 인스턴스의 전체 상태와 어떻게 관련되는지를 설명합니다.

#### <a name="metrics-for-tracking-service-limits"></a>서비스 제한 추적에 대한 메트릭

이러한 메트릭을 솔루션의 일부 측면에 대해 [게시된 서비스 제한](reference-service-limits.md#functional-limits)에 도달 하는 시기를 추적하도록 구성할 수 있습니다. 

추적을 설정하려면 Azure Monitor의 [경고](troubleshoot-alerts.md) 기능을 사용합니다. 메트릭이 게시된 한도의 특정 비율에 도달하면 경고를 받도록 이러한 메트릭에 대한 임계값을 정의할 수 있습니다.

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| TwinCount | 트윈 개수(미리 보기) | 개수 | 합계 | Azure Digital Twins 인스턴스의 총 트윈 수. 이 메트릭을 사용하여 인스턴스당 허용되는 최대 트윈 수에 대한 [서비스 한도](reference-service-limits.md#functional-limits)에 근접하고 있는지 확인합니다. |  없음 |
| ModelCount | 모델 개수(미리 보기) | 개수 | 합계 | Azure Digital Twins 인스턴스의 총 모델 수. 이 메트릭을 사용하여 인스턴스당 허용되는 최대 모델 수에 대한 [서비스 한도](reference-service-limits.md#functional-limits)에 근접하고 있는지 확인합니다. | 없음 |

#### <a name="api-request-metrics"></a>API 요청 메트릭

API 요청으로 수행해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| ApiRequests | API 요청 | 개수 | 합계 | Digital Twins 읽기, 쓰기, 삭제 및 쿼리 작업에 대해 수행된 API 요청의 수입니다. |  인증, <br>작업, <br>프로토콜, <br>상태 코드, <br>상태 코드 클래스, <br>상태 텍스트 |
| ApiRequestsFailureRate | API 요청 실패율 | 백분율 | 평균 | 서비스가 인스턴스에 대해 수신하는 API 요청 중 Digital Twins 읽기, 쓰기, 삭제 및 쿼리 작업에 대한 내부 오류(500) 응답 코드를 제공하는 요청의 백분율입니다. | 인증, <br>작업, <br>프로토콜, <br>상태 코드, <br>상태 코드 클래스, <br>상태 텍스트
| ApiRequestsLatency | API 요청 대기 시간 | 밀리초 | 평균 | API 요청에 대한 응답 시간입니다. 이 값은 Azure Digital Twins가 요청을 받은 시간부터 서비스가 Digital Twins 읽기, 쓰기, 삭제 및 쿼리 작업에 대한 성공/실패 결과를 보낼 때까지 시간을 나타냅니다. | 인증, <br>작업, <br>프로토콜 |

#### <a name="billing-metrics"></a>청구 메트릭

청구로 수행해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| BillingApiOperations | 청구 API 작업 | 개수 | 합계 | Azure Digital Twins 서비스를 대상으로 수행된 모든 API 요청 수에 대한 청구 메트릭입니다. | Meter ID |
| BillingMessagesProcessed | 처리된 청구 메시지 | 개수 | 합계 | Azure Digital Twins에서 외부 엔드포인트로 전송된 메시지 수에 대한 청구 메트릭입니다.<br><br>요금 청구를 위해 단일 메시지로 간주되려면 페이로드가 1KB보다 크지 않아야 합니다. 이 한도보다 큰 페이로드는 1KB씩 추가 메시지로 계산됩니다. 즉, 1~2KB 사이의 메시지는 2개 메시지로 계산되고, 2~3KB 사이의 메시지는 3개의 메시지로 계산됩니다.<br>이 제한은 응답에도 적용됩니다. 예를 들어 응답 본문에서 1.5KB를 반환하는 호출은 2개 작업으로 요금이 청구됩니다. | Meter ID |
| BillingQueryUnits | 청구 쿼리 단위 | 개수 | 합계 | 쿼리 실행에 사용된 서비스 리소스 사용량을 내부적으로 계산하여 측정한 쿼리 단위 수 쿼리 단위를 측정하는 데 사용할 수 있는 도우미 API인 [QueryChargeHelper 클래스](/dotnet/api/azure.digitaltwins.core.querychargehelper?view=azure-dotnet&preserve-view=true)도 있습니다. | Meter ID |

Azure Digital Twins 요금이 청구되는 방법에 관한 자세한 내용은 [Azure Digital Twins 가격 책정](https://azure.microsoft.com/pricing/details/digital-twins/)을 참조하세요.

#### <a name="ingress-metrics"></a>수신 메트릭

데이터 수신으로 수행해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| IngressEvents | 수신 이벤트 | 개수 | 합계 | Azure Digital Twins로 들어오는 원격 분석 이벤트의 수 | 결과 |
| IngressEventsFailureRate | 수신 이벤트 실패율 | 백분율 | 평균 | 서비스에서 내부 오류(500) 응답 코드를 반환하는 수신 원격 분석 이벤트의 백분율입니다. | 결과 |
| IngressEventsLatency | 수신 이벤트 대기 시간 | 밀리초 | 평균 | 이벤트가 도착하는 시점부터 Azure Digital Twins가 이벤트를 보낼 준비가 완료되는 시점까지 걸리는 시간입니다. 이 시점에서 서비스는 성공/실패 결과를 전송합니다. | 결과 |

#### <a name="routing-metrics"></a>라우팅 메트릭

라우팅으로 수행해야 하는 메트릭:

| 메트릭 | 메트릭 표시 이름 | 단위 | 집계 유형| Description | 차원 |
| --- | --- | --- | --- | --- | --- |
| MessagesRouted | 라우팅된 메시지 | 개수 | 합계 | Event Hub, Service Bus 또는 Event Grid와 같은 엔드포인트 Azure 서비스로 라우팅된 메시지 수입니다. | 엔드포인트 유형, <br>결과 |
| RoutingFailureRate | 라우팅 실패율 | 백분율 | 평균 | Azure Digital Twins에서 Event Hub, Service Bus 또는 Event Grid와 같은 엔드포인트 Azure 서비스로 라우팅될 때 오류가 발생하는 이벤트의 백분율입니다. | 엔드포인트 유형, <br>결과 |
| RoutingLatency | 라우팅 대기 시간 | 밀리초 | 평균 | 이벤트가 Azure Digital Twins에서 라우팅되는 시점부터 Event Hub, Service Bus 또는 Event Grid와 같은 엔드포인트 Azure 서비스에 게시되는 시점까지 경과한 시간입니다. | 엔드포인트 유형, <br>결과 |

## <a name="dimensions"></a>차원

차원은 메트릭에 대한 자세한 정보를 식별하는 데 도움이 됩니다. 일부 라우팅 메트릭은 엔드포인트당 정보를 제공합니다. 다음 표에서는 이러한 차원에 사용할 수 있는 값을 나열합니다.

| 차원 | 값 |
| --- | --- |
| 인증 | OAuth |
| 작업(API 요청) | Microsoft.DigitalTwins/digitaltwins/delete, <br>Microsoft.DigitalTwins/digitaltwins/write, <br>Microsoft.DigitalTwins/digitaltwins/read, <br>Microsoft.DigitalTwins/eventroutes/read, <br>Microsoft.DigitalTwins/eventroutes/write, <br>Microsoft.DigitalTwins/eventroutes/delete, <br>Microsoft.DigitalTwins/models/read, <br>Microsoft.DigitalTwins/models/write, <br>Microsoft.DigitalTwins/models/delete, <br>Microsoft.DigitalTwins/query/action |
| 엔드포인트 유형 | Event Grid, <br>Event Hub, <br>Service Bus |
| 프로토콜 | HTTPS |
| 결과 | 성공, <br>실패 |
| 상태 코드 | 200, 404, 500 등 |
| 상태 코드 클래스 | 2xx, 4xx, 5xx 등 |
| 상태 텍스트 | 내부 서버 오류, 찾을 수 없음 등 |

## <a name="next-steps"></a>다음 단계

Azure Digital Twins에 대해 기록된 메트릭을 관리하는 방법에 대한 자세한 내용은 [문제 해결: 진단 설정](troubleshoot-diagnostics.md)을 참조하세요.
