---
title: Azure Communication Service의 메트릭 정의
titleSuffix: An Azure Communication Services concept document
description: 이 문서에서는 Azure Portal에서 사용할 수 있는 메트릭 정의를 다룹니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: b97c7ead1c10573dccaf260e7ab1225872d2e898
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679885"
---
# <a name="metrics-overview"></a>메트릭 개요

Azure Communication Services는 현재 채팅 및 SMS에 대한 메트릭을 제공합니다. [Azure 메트릭 탐색기](../../azure-monitor/essentials/metrics-getting-started.md)를 사용하면 사용자 고유의 차트를 만들고, 비정상적인 메트릭 값을 조사하고, 채팅 및 SMS 요청에서 내보내는 메트릭 데이터를 사용하여 API 트래픽을 이해할 수 있습니다.

## <a name="where-to-find-metrics"></a>메트릭을 찾을 수 있는 위치

Azure Communication Services의 채팅 및 SMS 서비스는 API 요청에 대한 메트릭을 내보냅니다. 이러한 메트릭은 Communication Services 리소스 아래의 [메트릭] 블레이드에서 찾을 수 있습니다. Communication Services 리소스 아래의 통합 문서 블레이드를 사용하여 영구 대시보드를 만들 수도 있습니다.

## <a name="metric-definitions"></a>메트릭 정의

Communication Services 메트릭 내에는 **채팅 API 요청** 및 **SMS API 요청** 의 두 가지 유형의 요청이 표시됩니다.

채팅 및 SMS API 요청 메트릭에는 메트릭 데이터를 필터링하는 데 사용할 수 있는 3개의 차원이 포함되어 있습니다. 이러한 차원은 `Count` 집계 유형을 사용하여 함께 집계할 수 있으며 `Sum`, `Average`, `Min` 및 `Max`를 비롯한 모든 표준 Azure 집계 시계열을 지원합니다.

지원되는 집계 유형 및 시계열 집계에 대한 자세한 내용은 [Azure 메트릭 탐색기의 고급 기능](../../azure-monitor/essentials/metrics-charts.md#aggregation)에서 찾을 수 있습니다.

- **작업** - ACS 채팅 게이트웨이에서 호출할 수 있는 모든 작업 또는 경로입니다.
- **상태 코드** - 요청 후에 전송된 상태 코드 응답입니다.
- **StatusSubClass** - 응답 후에 전송된 상태 코드 시리즈입니다. 


### <a name="chat-api-request-metric-operations"></a>채팅 API 요청 메트릭 작업

채팅 API 요청 메트릭에는 다음과 같은 작업을 사용할 수 있습니다.

| 작업/경로    | 설명                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| GetChatMessage       | 메시지 ID별로 메시지를 가져옵니다. |
| ListChatMessages     | 스레드의 채팅 메시지 목록을 가져옵니다. |
| SendChatMessage      | 스레드에 채팅 메시지를 보냅니다. |
| UpdateChatMessage    | 채팅 메시지를 업데이트합니다. |
| DeleteChatMessage    | 채팅 메시지를 삭제합니다. |
| GetChatThread        | 채팅 스레드를 가져옵니다. |
| ListChatThreads      | 사용자의 채팅 스레드 목록을 가져옵니다. |
| UpdateChatThread     | 채팅 스레드의 속성을 업데이트합니다. |
| CreateChatThread     | 채팅 스레드를 만듭니다. |
| DeleteChatThread     | 스레드를 삭제합니다. |
| GetReadReceipts      | 스레드의 읽음 확인을 가져옵니다. |
| SendReadReceipt      | 사용자를 대신하여 읽음 확인 이벤트를 스레드로 보냅니다. |
| SendTypingIndicator           | 사용자를 대신하여 입력 이벤트를 스레드에 게시합니다. |
| ListChatThreadParticipants    | 스레드의 멤버를 가져옵니다. |
| AddChatThreadParticipants     | 스레드에 스레드 멤버를 추가합니다. 멤버가 이미 있는 경우 변경되지 않습니다. |
| RemoveChatThreadParticipant   | 스레드에서 멤버를 제거합니다. |

:::image type="content" source="./media/chat-metric.png" alt-text="채팅 API 요청 메트릭":::

인식되지 않는 작업에 대한 요청이 발생하면 "잘못된 경로" 값 응답을 받게 됩니다.

### <a name="sms-api-requests"></a>SMS API 요청

SMS API 요청 메트릭에는 다음과 같은 작업을 사용할 수 있습니다.

| 작업/경로    | 설명                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| SMSMessageSent       | SMS 메시지를 보냅니다. |
| SMSDeliveryReportsReceived     | SMS 배달 보고서를 가져옵니다. |
| SMSMessagesReceived      | SMS 메시지를 가져옵니다. |


:::image type="content" source="./media/sms-metric.png" alt-text="SMS API 요청 메트릭":::

### <a name="authentication-api-requests"></a>인증 API 요청

인증 API 요청 메트릭에는 다음과 같은 작업을 사용할 수 있습니다.

| 작업/경로    | 설명                                                                                    |
| -------------------- | ---------------------------------------------------------------------------------------------- |
| CreateIdentity       | 단일 사용자를 나타내는 ID를 만듭니다. |
| DeleteIdentity       | ID를 삭제합니다. |
| CreateToken          | 액세스 토큰을 만듭니다. |
| RevokeToken          | 지정된 시간 전에 ID에 대해 만들어진 모든 액세스 토큰을 취소합니다. |

:::image type="content" source="./media/acs-auth-metrics.png" alt-text="인증 요청 메트릭.":::

## <a name="next-steps"></a>다음 단계

- [데이터 플랫폼 메트릭](../../azure-monitor/essentials/data-platform-metrics.md)에 대한 자세한 정보
