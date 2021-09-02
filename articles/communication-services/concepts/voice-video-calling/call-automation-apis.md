---
title: Azure Communication Services Call Automation API 개요
titleSuffix: An Azure Communication Services concept document
description: Call Automation 기능 및 API에 대한 개요를 제공합니다.
author: GrantMeStrength
manager: anvalent
services: azure-communication-services
ms.author: jken
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 0e4d9c737147685b20c5f83295ac5533efdab448
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123254078"
---
# <a name="call-automation-overview"></a>Call Automation 개요

[!INCLUDE [Public Preview](../../includes/public-preview-include-document.md)]

Call Automation API를 사용하면 **서비스** 에서 음성 및 비디오 통화 기능에 액세스할 수 있습니다. 이러한 API를 사용하여 약속에 대한 자동 아웃바운드 미리 알림 통화를 구동하거나 정전 또는 산불과 같은 이벤트에 대한 사전 예방 알림을 제공하는 서비스 애플리케이션을 만들 수 있습니다. 통화에 참가하는 서비스 애플리케이션은 참가자 참가 또는 종료와 같은 업데이트를 모니터링하여 풍부한 보고 및 로깅 기능을 구현할 수 있습니다.

![In-Call 및 Out-of-Call 앱](../media/call-automation-apps.png)

Call Automation API는 In-Call(애플리케이션 참가자 또는 앱 참가자) 작업 및 Out-of-Call 작업 모두에 대해 제공됩니다. 이러한 API 집합의 두 가지 주요 차이점은 다음과 같습니다.
- In-Call API에서는 애플리케이션이 참가자로 통화에 참가해야 합니다. 앱 참가자에는 [표준 PSTN 및 VoIP 요금](https://azure.microsoft.com/pricing/details/communication-services/)이 청구됩니다.
- In-Call API는 앱 참가자와 연결된 `callConnectionId`를 사용하고, Out-of-Call API는 통화 인스턴스와 연결된 `serverCallId`를 사용합니다. 

## <a name="use-cases"></a>사용 사례
| 사용 사례                                                       | In-Call(앱 참가자) | Out-of-Call   |
| ---------------------------------------------------------------| ------------------------- | ------------- |
| 봇과 인간 참가자 간에 1:1 통화 발신 또는 수신  | X                         |               |
| 오디오 프롬프트 재생 및 응답 청취                    | X                         |               |
| In-Call 이벤트 모니터링                                         | X                         |               |
| 여러 참가자와의 통화 만들기                        | X                         |               |
| 통화 참가자 및 참가자 세부 정보 가져오기                  | X                         |               |
| 통화 참가자 추가 또는 제거                                | X                         | X             |
| 피어 투 피어 통화의 서버 쪽 작업(예: 녹음)     |                           | X             |
| 모든 참가자에게 오디오 안내 재생                   |                           | X             |
| 통화 녹음 시작 및 관리                                |                           | X             |

## <a name="in-call-app-participant-apis"></a>In-Call(앱 참가자) API

In-Call API를 사용하면 애플리케이션이 통화에서 앱 참가자로서의 동작을 수행할 수 있습니다. 애플리케이션이 통화에 응답하거나 참가하면 다음과 같은 In-Call 작업에 사용되는 `callConnectionId`가 할당됩니다.
- 통화 참가자 추가 또는 제거
- 오디오 프롬프트 재생 및 DTMF 응답 청취
- 통화 명단 업데이트 및 이벤트 수신 대기
- 전화 끊기

![In-Call 애플리케이션](../media/call-automation-in-call.png)

### <a name="in-call-events"></a>In-Call 이벤트
이벤트 알림은 통화에 참가할 때 설정된 `callbackUri`를 통해 호출 애플리케이션에 JSON 페이로드로 전송됩니다. In-Call 앱 참가자에 전송되는 이벤트는 다음과 같습니다.
- CallState 이벤트(Establishing, Established, Terminating, Terminated)
- DTMF 수신됨
- 오디오 재생 결과
- 미디어 처리 취소
- 참가자 초대 결과
- 참가자 업데이트됨

## <a name="out-of-call-apis"></a>Out-of-Call API
Out-of-Call API를 사용하면 앱 참가자가 없어도 통화 또는 회의에 대한 작업을 수행할 수 있습니다. Out-of-Call API는 호출하는 클라이언트 SDK에서 제공하거나 Call Automation API를 통해 통화가 만들어질 때 생성된 `serverCallId`를 사용합니다. Out-of-Call API에는 앱 참가자가 필요하지 않으므로 피어 투 피어 통화에 서버 쪽 비즈니스 논리를 구현하는 데 유용합니다. 예를 들어 피어 투 피어 통화로 시작되는 지원 통화에서 에이전트(참가자 B)가 실무 전문가에게 도움을 요청하는 시나리오를 생각해 보세요. 참가자 B는 서버 애플리케이션의 클라이언트 인터페이스에서 이벤트를 트리거하여 사용 가능한 실무 전문가를 식별하고 통화에 초대합니다. 아래에 표시된 흐름의 최종 상태는 세 명의 인간 참기자를 포함하는 그룹 통화입니다.

![Out-of-Call 애플리케이션](../media/call-automation-out-of-call.png)

Out-of-Call API는 다음과 같은 작업에 사용할 수 있습니다.
- 통화 참가자 추가 또는 제거
- 통화 녹음 시작/중지/일시 중지/다시 시작
                                                       
### <a name="out-of-call-events"></a>Out-of-Call 이벤트
이벤트 알림은 원래 API 호출에서 제공하는 `callbackUri`를 통해 호출 애플리케이션에 JSON 페이로드로 전송됩니다. 해당하는 Out-of-Call 이벤트를 사용하는 작업은 다음과 같습니다.
- 통화 녹음(시작, 중지, 일시 중지, 다시 시작)
- 참가자 초대 결과

## <a name="next-steps"></a>다음 단계
자세한 내용은 [Call Automation 빠른 시작](../../quickstarts/voice-video-calling/call-automation-api-sample.md)을 확인하세요.
