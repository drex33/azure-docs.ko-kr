---
title: 호출 및 채팅 상호 운용성 Teams
titleSuffix: An Azure Communication Services concept document
description: 호출 및 채팅 상호 운용성 Teams
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 330dbb37317542088f2c62fc0ffb843f3297294c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131464987"
---
# <a name="teams-interoperability-calling-and-chat"></a>Teams 상호 운용성: 호출 및 채팅

> [!IMPORTANT]
> 호출 및 채팅 상호 운용성은 비공개 미리 보기 이며 제한 된 수의 Azure Communication Services 초기로 제한 됩니다. [이 양식을 제출 하 여 미리 보기 참여를 요청할](https://forms.office.com/r/F3WLqPjw0D) 수 있습니다. 그러면 시나리오를 검토 하 고 미리 보기 참여를 평가 합니다.
>
> 비공개 미리 보기 Api 및 Sdk는 서비스 수준 계약 없이 제공 되며 프로덕션 워크 로드에는 적합 하지 않으며 테스트 사용자 및 테스트 데이터에만 사용 해야 합니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 
> 지원, 질문 또는 피드백을 제공 하거나 문제를 보고 하려면 [Teams Interop 임시 호출 및 채팅 채널](https://teams.microsoft.com/l/channel/19%3abfc7d5e0b883455e80c9509e60f908fb%40thread.tacv2/Teams%2520Interop%2520ad%2520hoc%2520calling%2520and%2520chat?groupId=d78f76f3-4229-4262-abfb-172587b7a6bb&tenantId=72f988bf-86f1-41af-91ab-2d7cd011db47)을 사용 하세요. Azure Communication Service 탭 팀의 구성원 이어야 합니다.

이 미리 보기의 일부로 Azure Communication Services sdk를 사용 하 여 사용자의 id (byoi) 사용자가 1:1 호출을 시작 하거나 1: n 채팅을 Teams 사용자로 전환할 수 있는 응용 프로그램을 빌드할 수 있습니다. [표준 ACS 가격은](https://azure.microsoft.com/pricing/details/communication-services/) 이러한 사용자에 게 적용 되지만 상호 운용성 기능 자체에 대 한 추가 요금은 없습니다.



## <a name="enabling-calling-and-chat-interoperability-in-your-teams-tenant"></a>Teams 테 넌 트에서 호출 및 채팅 상호 운용성 사용
통신 서비스 사용자와 Teams 테 넌 트 간에 호출 및 채팅을 사용 하도록 설정 하려면 Teams 새로운 PowerShell cmdlet [CsTeamsAcsFederationConfiguration](/powershell/module/teams/set-csteamsacsfederationconfiguration)을 사용 합니다. 이 cmdlet은 비공개 미리 보기의 참가자만 사용할 수 있습니다. 

Azure Communication Services를 사용 하 여 빌드한 사용자 지정 응용 프로그램을 Teams 사용자와 연결 하 고 통신 하기 위해 최종 사용자 또는 봇에서 사용할 수 있으며, 응용 프로그램 개발자가 명시적으로 표시 하지 않는 한 사용자 Teams 표시 되는 방법에는 차이가 없습니다.

Teams 사용자가 호출 하거나 채팅을 시작 하려면 사용자의 AAD (Azure Active Directory) 개체 ID가 필요 합니다. [Azure AD Connect](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) (또는 다른 메커니즘)를 사용 하 여 온-프레미스 디렉터리와 AAD 간에 동기화 하는 경우 [Microsoft Graph API](/graph/api/resources/users) 또는 온-프레미스 디렉터리를 사용 하 여이를 가져올 수 있습니다.

## <a name="calling"></a>호출
호출 SDK를 사용 하면 통신 서비스 사용자 또는 끝점이 Azure Active Directory (AAD) 개체 ID로 식별 되는 Teams 사용자로 1:1 호출을 시작할 수 있습니다. 다른 통신 서비스 사용자를 호출 하는 기존 응용 프로그램을 쉽게 수정 하 여 Teams 사용자를 호출할 수 있습니다.
 
[호출 관리-Azure 통신 서비스 방법 가이드 | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

다른 ACS 사용자 호출:
```js
const acsCallee = { communicationUserId: '<ACS_USER_ID>' }
const call = callAgent.startCall([acsCallee]);
```

Teams 사용자 호출:
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**제한 사항 및 알려진 문제**
- Teams 사용자는 "teamsonly" 모드에 있어야 합니다. 비즈니스용 Skype 사용자는 통신 서비스 사용자 로부터 1:1 호출을 받을 수 없습니다.
- 그룹 호출로의 에스컬레이션이 지원 되지 않습니다.
- 통신 서비스 통화 기록은 1:1 호출에 사용할 수 없습니다.
- 호출 전달, 그룹 호출 픽업, simulring 및 음성 메일과 같은 고급 호출 라우팅 기능은 지원 되지 않습니다.
- 사용자 Teams 통신 서비스 사용자를 전달/전송 대상으로 설정할 수 없습니다.
- Teams 클라이언트에는 통신 서비스 사용자와 1:1를 호출 하는 동안 예상 대로 작동 하지 않는 여러 기능이 있습니다.
- Teams 및 [Skype IP 휴대폰용](/skypeforbusiness/certification/devices-ip-phones) 타사 [장치](/MicrosoftTeams/devices/teams-ip-phones) 는 지원 되지 않습니다.

## <a name="chat"></a>채팅
채팅 SDK를 사용 하면 통신 서비스 사용자 또는 끝점이 Azure Active Directory (AAD) 개체 ID로 식별 되는 Teams 사용자와 채팅을 1: n 채팅을 시작할 수 있습니다. 다른 통신 서비스 사용자와 채팅을 만드는 기존 응용 프로그램을 쉽게 수정 하 여 Teams 사용자와 채팅을 만들 수 있습니다.
                                            
[빠른 시작: 앱에 채팅 추가](../../quickstarts/chat/get-started.md?pivots=programming-language-javascript)

Teams 사용자와 채팅 만들기:
```js
async function createChatThread() { 
const createChatThreadRequest = {  topic: "Hello, World!"  }; 
const createChatThreadOptions = {
    participants: [ { 
        id: { microsoftTeamsUserId: '<TEAMS_USER_ID>' }, 
        displayName: '<USER_DISPLAY_NAME>' }
    ] }; 
const createChatThreadResult = await chatClient.createChatThread( 
createChatThreadRequest, createChatThreadOptions ); 
const threadId = createChatThreadResult.chatThread.id; return threadId; }
```                                         

**지원되는 기능**
-   메시지 보내기/받기 (형식: 텍스트, 서식 있는 텍스트, 이모티콘) 
-   통신 서비스 사용자가 보낸 메시지를 편집할 수 있음
-   보낸 메시지 삭제
-   실시간 알림 (현재 ACS에서 지원 되는 스레드 및 메시지 관련 이벤트) 받기
-   전송 & 수신 입력 표시기
-   읽음 확인 수신 & 수신
-   참가자 추가 및 메시지 기록 공유: Teams 사용자는 Teams 사용자만 추가할 수 있습니다. 통신 서비스 사용자는 Teams 및 통신 서비스 사용자를 추가할 수 있습니다.
-   채팅에서 기존 참가자 제거
-   채팅 나가기
-   채팅 항목 업데이트
-   통신 서비스 사용자가 채팅을 삭제할 수 있습니다.


**제한 사항 및 알려진 문제**
- Teams 사용자가 메시지를 편집 하지 못했습니다.
- 통신 서비스 사용자가 스레드를 삭제 하면 Teams 사용자에 대 한 메시지 기록이 제거 되 고 스레드에서 Teams 사용자가 제거 됩니다.
- 외부 사용자에 대 한 Teams 클라이언트 UI가 일치 하지 않습니다.
- Teams 클라이언트를 사용 하 여 채팅 참가자와 통화를 시작할 수 없습니다.


## <a name="privacy"></a>개인 정보 취급 방침
Azure Communication Services와 Microsoft Teams 간의 상호 운용성을 통해 애플리케이션과 사용자가 Teams 통화, 회의 및 채팅에 참여할 수 있습니다. Teams 통화 또는 회의에서 기록 또는 전사를 사용하도록 설정할 때 애플리케이션 사용자에게 알림을 제공하는 것은 귀하의 책임입니다.

Microsoft는 Azure Communication Services API를 통해 기록 또는 기록이이 시작 될 사용자에 게 응용 프로그램의 사용자 인터페이스 내에서 사용자에 게 실시간으로 이러한 사실을 전달 해야 함을 알려줍니다. 귀하는 이 의무를 준수하지 않아 발생한 모든 비용 및 손해에 대해 Microsoft를 면책하는 데 동의합니다.
