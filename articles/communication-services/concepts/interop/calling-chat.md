---
title: 통화 및 채팅 상호 운용성 Teams
titleSuffix: An Azure Communication Services concept document
description: 통화 및 채팅 상호 운용성 Teams
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 328bfff2366efb416cbd41452a821256f675d0e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131078696"
---
# <a name="teams-interoperability-calling-and-chat"></a>Teams 상호 운용성: 통화 및 채팅

> [!IMPORTANT]
> 통화 및 채팅 상호 운용성은 프라이빗 미리 보기로 있으며 제한된 수의 Azure Communication Services 얼리 어답터로 제한됩니다. 이 [양식을 제출하여 미리 보기 참여를 요청할](https://forms.office.com/r/F3WLqPjw0D) 수 있으며, 시나리오를 검토하고 미리 보기 참여를 평가합니다.
>
> 프라이빗 미리 보기 API 및 SDK는 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에 적합하지 않으며 테스트 사용자 및 테스트 데이터에만 사용해야 합니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.
> 
> 지원, 질문 또는 피드백 또는 보고서 문제를 제공하려면 [Teams Interop 임시 통화 및 채팅 채널을](https://teams.microsoft.com/l/channel/19%3abfc7d5e0b883455e80c9509e60f908fb%40thread.tacv2/Teams%2520Interop%2520ad%2520hoc%2520calling%2520and%2520chat?groupId=d78f76f3-4229-4262-abfb-172587b7a6bb&tenantId=72f988bf-86f1-41af-91ab-2d7cd011db47)사용하세요. Azure Communication Service TAP 팀의 구성원이어야 합니다.

이 미리 보기의 일부로 Azure Communication Services SDK를 사용하여 BYOI(Bring Your Own Identity) 사용자가 Teams 사용자와 1:1 통화 또는 1:n 채팅을 시작할 수 있도록 하는 애플리케이션을 빌드할 수 있습니다. [표준 ACS 가격 책정은](https://azure.microsoft.com/pricing/details/communication-services/) 이러한 사용자에게 적용되지만 상호 운용성 기능 자체에는 추가 요금이 부과되지 않습니다.



## <a name="enabling-calling-and-chat-interoperability-in-your-teams-tenant"></a>Teams 테넌트에서 통화 및 채팅 상호 운용성 사용
Communication Services 사용자와 Teams 테넌트 간에 통화 및 채팅을 사용하도록 설정하려면 새 Teams PowerShell cmdlet [Set-CsTeamsAcsFederationConfiguration](/powershell/module/teams/set-csteamsacsfederationconfiguration)을 사용합니다. 이 cmdlet은 프라이빗 미리 보기의 참가자만 사용할 수 있습니다. 

Teams 사용자와 연결하고 통신하기 위해 Azure Communication Services 빌드된 사용자 지정 애플리케이션은 최종 사용자 또는 봇에서 사용할 수 있으며, 애플리케이션 개발자가 명시적으로 표시하지 않는 한 Teams 사용자에게 표시되는 방식이 구별되지 않습니다.

Teams 사용자와 통화 또는 채팅을 시작하려면 사용자의 Azure Active Directory(AAD) 개체 ID가 필요합니다. [Azure AD](../../../active-directory/hybrid/how-to-connect-sync-whatis.md) 커넥트(또는 다른 메커니즘)을 사용하여 온-프레미스 디렉터리와 AAD 간에 동기화하는 경우 Microsoft Graph [API를](/graph/api/resources/users) 사용하거나 온-프레미스 디렉터리에서 가져올 수 있습니다.

## <a name="calling"></a>호출
Calling SDK를 사용하면 Communication Services 사용자 또는 엔드포인트가 Teams 사용자와 함께 1:1 호출을 시작할 수 있으며 AAD(Azure Active Directory) 개체 ID로 식별됩니다. 다른 Communication Services 사용자를 호출하는 기존 애플리케이션을 Teams 사용자를 대신 호출하도록 쉽게 수정할 수 있습니다.
 
[호출 관리 - Azure Communication Services 방법 가이드 | Microsoft Docs](../../how-tos/calling-sdk/manage-calls.md?pivots=platform-web)

다른 ACS 사용자 호출:
```js
const acsCallee = { communicationUserId: <'ACS_USER_ID>' }
const call = callAgent.startCall([acsCallee]);
```

Teams 사용자 호출:
```js
const teamsCallee = { microsoftTeamsUserId: '<Teams User AAD Object ID>' }
const call = callAgent.startCall([teamsCallee]);
```
 
**제한 사항 및 알려진 문제**
- Teams 사용자는 "TeamsOnly" 모드에 있어야 합니다. 비즈니스용 Skype 사용자는 Communication Services 사용자로부터 1:1 호출을 받을 수 없습니다.
- 그룹 호출에 대한 에스컬레이션은 지원되지 않습니다.
- Communication Services 사용자가 통화 기록에 올바르게 표시되지 않음
- Communication Services 통화 녹음은 1:1 호출에 사용할 수 없습니다.
- 통화 전달, 그룹 통화 픽업, 간소화 및 음성 메일과 같은 고급 통화 라우팅 기능은 지원되지 않습니다.
- Teams 사용자는 Communication Services 사용자를 전달/전송 대상으로 설정할 수 없습니다.
- LyncIpPhone 포크는 지원되지 않습니다.

## <a name="chat"></a>채팅
채팅 SDK를 사용하면 Communication Services 사용자 또는 엔드포인트가 Azure Active Directory(AAD) 개체 ID로 식별되는 Teams 사용자와 1:n 채팅을 시작할 수 있습니다. 다른 Communication Services 사용자와 채팅을 만드는 기존 애플리케이션을 쉽게 수정하여 Teams 사용자와 채팅을 만들 수 있습니다.
                                            
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
-   메시지 보내기/받기(형식: 텍스트, 서식 있는 텍스트, 감정) 
-   Communication Services 사용자가 보낸 메시지를 편집할 수 있습니다.
-   보낸 메시지 삭제
-   실시간 알림 수신(현재 ACS에서 지원하는 스레드 및 메시지 관련 이벤트)
-   입력 표시기 보내기 & 받기
-   송신 & 읽기 영수증 받기
-   참가자 추가 및 메시지 기록 공유: Teams 사용자는 Teams 사용자만 추가할 수 있습니다. Communication Services 사용자는 Teams 추가하고 사용자를 Communication Services 수 있습니다.
-   채팅에서 기존 참가자 제거
-   채팅 나가기
-   채팅 토픽 업데이트
-   Communication Services 사용자가 채팅을 삭제할 수 있습니다.


**제한 사항 및 알려진 문제**
- Teams 사용자의 메시지 편집이 실패합니다.
- Communication Services 사용자가 스레드를 삭제하는 경우 Teams 사용자의 메시지 기록이 제거되고 스레드에서 Teams 사용자가 제거됩니다.
- 외부 사용자에 대한 Teams 클라이언트 UI가 일치하지 않습니다.
- Teams 클라이언트를 사용하여 채팅 참가자와 통화를 시작할 수 없습니다.


## <a name="privacy"></a>개인 정보 취급 방침
Azure Communication Services와 Microsoft Teams 간의 상호 운용성을 통해 애플리케이션과 사용자가 Teams 통화, 회의 및 채팅에 참여할 수 있습니다. Teams 통화 또는 회의에서 기록 또는 전사를 사용하도록 설정할 때 애플리케이션 사용자에게 알림을 제공하는 것은 귀하의 책임입니다.

Microsoft는 기록 또는 전사가 시작되었음을 Azure Communication Services API를 통해 사용자에게 알리며, 애플리케이션의 사용자 인터페이스 내에서 사용자에게 실시간으로 이 사실을 전달해야 합니다. 귀하는 이 의무를 준수하지 않아 발생한 모든 비용 및 손해에 대해 Microsoft를 면책하는 데 동의합니다.
