---
title: Azure Communication Services에 대한 채팅 SDK 개요
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 채팅 SDK에 대해 알아봅니다.
author: knvsl
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: chat
ms.openlocfilehash: 64d88922d849fc29ab5d33451d8d90bed6ed4028
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132308411"
---
# <a name="chat-sdk-overview"></a>채팅 SDK 개요

Azure Communication Services 채팅 SDK를 사용하여 다양한 실시간 채팅을 애플리케이션에 추가할 수 있습니다.

## <a name="chat-sdk-capabilities"></a>채팅 SDK 기능

다음 목록에서는 현재 Communication Services 채팅 SDK에서 사용할 수 있는 기능 세트를 보여줍니다.

| 기능 그룹 | 기능 | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| 핵심 기능 | 2명 이상의 사용자 간 채팅 스레드 만들기                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | 채팅 스레드의 토픽 업데이트                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | 채팅 스레드에서 참가자 추가 또는 제거                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 추가되는 참가자와 채팅 메시지 기록을 공유할지 여부를 선택합니다.                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 채팅 스레드의 참가자 목록 가져오기                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | 채팅 스레드 삭제                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 통신 사용자가 지정되면 사용자가 속한 채팅 스레드 목록을 가져옵니다.                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 특정 채팅 스레드에 대한 정보 가져오기                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | 채팅 스레드에서 메시지 보내기 및 받기                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 보낸 메시지의 콘텐츠 업데이트                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | 이전에 보낸 메시지 삭제                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |
|                   | 채팅의 다른 참가자가 읽은 메시지에 대한 수신 확인                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |
|                   | 참가자가 채팅 스레드에서 메시지를 적극적으로 입력할 때 알림 받기                                         | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |
|                   | 채팅 스레드에서 모든 메시지 가져오기                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 메시지 콘텐츠의 일부로 유니코드 이모지 보내기                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 채팅 메시지에 메타데이터 추가                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |
|                   | 표시 이름을 입력 표시기 알림에 추가                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ❌    | ✔️   |
|실시간 알림(독점적인 신호 패키지에서 사용하도록 설정**)|  채팅 클라이언트는 채팅 스레드에서 발생하는 들어오는 메시지 및 기타 작업에 대한 실시간 업데이트를 받기 위해 구독할 수 있습니다. 실시간 알림에 대해 지원되는 업데이트 목록을 보려면 [채팅 개념](concepts.md#real-time-notifications)을 참조하세요.                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |
| Azure Event Grid와 통합             | Azure Event Grid에서 사용할 수 있는 채팅 이벤트를 사용하여 사용자 지정 알림 서비스를 연결하거나 해당 이벤트를 웹후크에 게시하여 채팅이 완료된 후 CRM 레코드 업데이트와 같은 비즈니스 논리를 실행합니다.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
| 보고 </br>(이 정보는 Azure Portal에서 Communication Services 리소스의 모니터링 탭에서 사용할 수 있습니다.)      | Azure 메트릭 탐색기에서 게시된 메트릭을 모니터링하여 채팅 앱의 API 트래픽을 이해하고 이상을 감지하도록 경고를 설정합니다.     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |
|                   | 리소스에 대한 진단 로깅을 사용하도록 설정하여 Communication Services 솔루션을 모니터링하고 디버깅합니다.    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |


**독점적인 신호 패키지는 웹 소켓을 사용하여 구현됩니다. 웹 소켓이 지원되지 않으면 긴 폴링으로 대체됩니다.

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>OS 및 브라우저에서 JavaScript 채팅 SDK 지원

다음 표에서는 현재 사용할 수 있는 지원되는 브라우저 및 버전의 세트를 나타냅니다.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | iPad OS|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **채팅 SDK** | Firefox *, Chrome*, 새 Edge | Firefox *, Chrome*, Safari* | Chrome*  | Chrome* | Chrome* | Safari* | Safari* |

*이전 두 릴리스 외에도 최신 버전이 지원됩니다.<br/>

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [채팅 시작](../../quickstarts/chat/get-started.md)

다음 문서는 사용자에게 유용할 수 있습니다.
- [채팅 개념](../chat/concepts.md) 숙지
- 채팅에 대한 [가격 책정](../pricing.md#chat) 원리 이해
