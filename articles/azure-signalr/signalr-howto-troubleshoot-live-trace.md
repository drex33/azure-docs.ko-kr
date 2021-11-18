---
title: Azure SignalR Service에 대한 라이브 추적 도구를 사용하는 방법
description: Azure SignalR Service에 대한 라이브 추적 도구를 사용하는 방법 알아보기
author: wanlwanl
ms.author: wanl
ms.service: signalr
ms.topic: conceptual
ms.date: 06/30/2021
ms.openlocfilehash: 1f361b8a785f108f985dbae23290150c46170daa
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705304"
---
# <a name="how-to-use-live-trace-tool-for-azure-signalr-service"></a>Azure SignalR Service에 대한 라이브 추적 도구를 사용하는 방법

라이브 추적 도구는 Azure SignalR Service에서 라이브 추적을 캡처하고 표시하기 위한 단일 웹 애플리케이션입니다. 라이브 추적은 다른 서비스에 의존하지 않고 실시간으로 수집할 수 있습니다.
한 번의 클릭으로 라이브 추적 기능을 사용하거나 사용하지 않도록 설정할 수 있습니다. 원하는 로그 범주를 선택할 수도 있습니다.

> [!NOTE]
> 라이브 추적은 아웃바운드 메시지로 계산됩니다.

## <a name="launch-the-live-trace-tool"></a>라이브 추적 도구 시작

1. Azure Portal로 이동합니다.
2. **라이브 추적 사용** 을 선택합니다.
3. 도구 모음에서 **저장** 단추를 클릭하고 변경 사항이 적용될 때까지 기다립니다.
4. Azure Web PubSub 서비스 인스턴스의 **진단 설정** 페이지에서 **라이브 추적 도구 열기** 를 선택합니다. 

    :::image type="content" source="media/signalr-howto-troubleshoot-live-trace/live-traces-with-live-trace-tool.png" alt-text="라이브 추적 도구 실행 스크린샷.":::

## <a name="capture-live-traces"></a>라이브 추적 캡처

라이브 추적 도구는 문제 해결을 위해 라이브 추적을 캡처하는 데 도움이 되는 몇 가지 기본 기능을 제공합니다.

* **캡처**: 라이브 추적 도구를 사용하여 Azure Web PubSub 인스턴스에서 실시간 라이브 추적 캡처를 시작합니다.
* **지우기**: 캡처한 실시간 라이브 추적을 지웁니다.
* **내보내기**: 라이브 추적을 파일로 내보냅니다. 현재 지원되는 파일 형식은 CSV 파일입니다.
* **로그 필터**: 라이브 추적 도구를 사용하면 캡처된 실시간 라이브 추적을 하나의 특정 키워드로 필터링할 수 있습니다. 일반적인 구분 기호(예: 공백, 쉼표, 세미콜론 등)는 키 단어의 일부로 처리됩니다. 
* **상태**: 상태는 라이브 추적 도구가 특정 인스턴스와 연결되어 있는지 또는 연결이 끊겼는지를 보여 줍니다.

:::image type="content" source="./media/signalr-howto-troubleshoot-live-trace/live-trace-tool-capture.png" alt-text="라이브 추적 도구로 라이브 추적 캡처 스크린샷.":::

라이브 추적 도구에서 캡처한 실시간 라이브 추적에는 문제 해결을 위한 자세한 정보가 포함되어 있습니다. 

| Name | Description |
| ------------ |  ------------------------ | 
| Time | 로그 이벤트 시간 |
| 로그 수준 | 로그 이벤트 수준(추적/디버그/정보/경고/오류) |
| 이벤트 이름 | 이벤트의 작업 이름 |
| 메시지 | 로그 이벤트의 세부 메시지 |
| 예외 | Azure Web PubSub 서비스의 런타임 예외 |
| 허브 | 사용자 정의 허브 이름 |
| 연결 ID | 연결의 ID |
| 연결 유형 | 연결 유형입니다. 허용되는 값은 `Server`(서버와 서비스 간의 연결) 및 `Client`(클라이언트와 서비스 간의 연결)입니다.|
| 사용자 ID | 사용자의 ID |
| IP | 클라이언트의 IP 주소 |
| 서버 고정 | 클라이언트의 라우팅 모드. 허용되는 값은 `Disabled`, `Preferred` 및 `Required`입니다. 자세한 내용은 [ServerStickyMode](https://github.com/Azure/azure-signalr/blob/master/docs/run-asp-net-core.md#serverstickymode)를 참조하세요. |
| 전송 | 클라이언트가 HTTP 요청을 보내는 데 사용할 수 있는 전송입니다. 허용되는 값은 `WebSockets`, `ServerSentEvents` 및 `LongPolling`입니다. 자세한 내용은 [HttpTransportType](/dotnet/api/microsoft.aspnetcore.http.connections.httptransporttype)을 참조하세요. |
| 메시지 추적 ID | 메시지의 고유 식별자입니다. |
| 경로 템플릿 | API의 경로 템플릿 |
| Http 메서드 | Http 메서드 (POST/GET/PUT/DELETE) |
| URL | Uniform resource locator |
| 추적 ID | 요청을 나타내는 고유 식별자입니다. |
| 상태 코드 | Http 응답 코드 |
| Duration | 요청 간의 기간이 수신 및 처리 됩니다. |
| 헤더 | HTTP 요청 또는 응답을 사용 하 여 클라이언트와 서버에서 전달 하는 추가 정보 |
| 호출 ID | 호출을 나타내는 고유 식별자입니다 (ASP.NET SignalR에만 사용 가능). |
| 메시지 유형 | 메시지 유형 (BroadcastDataMessage/JoinGroupMessage/LeaveGroupMessage/...) |

## <a name="next-steps"></a>다음 단계

이 가이드에서는 라이브 추적 도구를 사용하는 방법에 대해 배웠습니다. 일반적인 문제를 처리하는 방법도 알아볼 수 있습니다.
* 문제 해결 가이드: 라이브 추적을 기반으로 일반적인 문제를 해결하는 방법은 [문제 해결 가이드](./signalr-howto-troubleshoot-guide.md)를 참조하세요.
* 문제 해결 방법: 자가 진단을 통해 근본 원인을 직접 찾거나 문제 범위를 좁히려면 [문제 해결 방법 소개](./signalr-howto-troubleshoot-method.md)를 참조하세요.