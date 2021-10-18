---
title: Teams 모임 상호 운용성
titleSuffix: An Azure Communication Services concept document
description: Teams 모임 참가
author: tomkau
ms.author: tomkau
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: 88025243a379b18b5b24cb3c47caee4713b47585
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130131546"
---
# <a name="join-a-teams-meeting"></a>Teams 회의 참가

> [!IMPORTANT]
> BYOI 상호 운용성은 공개 미리 보기이며 모든 Communication Services 애플리케이션 및 Teams 조직에서 사용할 수 있습니다.
>
> 미리 보기 API 및 SDK는 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 추천되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Communication Services 사용하여 사용자가 Teams 모임에 참가하고 참여할 수 있는 애플리케이션을 빌드할 수 있습니다. [표준 ACS 가격 책정은](https://azure.microsoft.com/pricing/details/communication-services/) 이러한 사용자에게 적용되지만 상호 운용성 기능 자체에는 추가 요금이 부과되지 않습니다. BYOI(Bring Your Own Identity) 모델을 사용하면 사용자 인증을 제어할 수 있으며, 애플리케이션 사용자는 Teams 모임에 참가하는 데 Teams 라이선스가 필요하지 않습니다. 이는 사용자 지정 애플리케이션을 사용하여 사용이 허가된 Teams 사용자(예: 의료 서비스 공급자 또는 재무 관리자) 및 외부 사용자(예: 환자 또는 클라이언트)가 가상 상담 경험에 참여할 수 있도록 하는 비즈니스-소비자 솔루션에 적합합니다.

Azure Communication Services SDK에서 Microsoft 365 Teams ID를 사용할 수도 있습니다. 자세한 내용은 [여기](./teams-interop.md)를 참조하세요.

현재 Teams 사용자가 Azure Communication Services Calling SDK를 사용하여 시작된 호출에 조인할 수 없습니다.

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>Teams 테넌트에서 익명 모임 조인 사용

BYOI 사용자가 Teams 모임에 참가하면 Teams 웹 애플리케이션을 사용하여 익명으로 Teams 모임에 참가하는 사용자와 비슷한 익명의 외부 사용자로 처리됩니다. BYOI 사용자가 익명 사용자로 Teams 모임에 참가하는 기능은 기존 "익명 모임 조인 허용" 구성에 의해 제어되며, 기존 Teams 익명 모임 조인도 제어합니다. 이 설정은 [Teams 관리 센터](https://admin.teams.microsoft.com/meetings/settings) 또는 Teams PowerShell cmdlet [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration)에서 업데이트할 수 있습니다.  

Teams 사용자와 연결하고 통신하기 위해 Azure Communication Services 빌드된 사용자 지정 애플리케이션은 최종 사용자 또는 봇에서 사용할 수 있으며, 애플리케이션 개발자가 이를 통신의 일부로 명시적으로 나타내지 않는 한 Teams 사용자에게 표시되는 방식이 구별되지 않습니다. 사용자 지정 애플리케이션은 Teams 모임을 보호하기 위해 사용자 인증 및 기타 보안 조치를 고려해야 합니다. 익명 사용자가 모임에 참가하도록 설정하는 경우 보안에 미치는 영향을 고려하고, [Teams 보안 가이드](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)를 사용하여 익명 사용자가 사용할 수 있는 기능을 구성합니다.

## <a name="meeting-experience"></a>모임 환경

Teams 익명 회의 참여와 마찬가지로 애플리케이션에 참여할 회의 링크가 있어야 합니다. 이 링크는 Graph API 또는 Microsoft Teams의 일정에서 가져올 수 있습니다. Teams 표시되는 BYOI 사용자의 이름은 Communication Services Calling SDK를 통해 구성할 수 있으며 Teams 사용자가 Azure Active Directory 사용하여 인증되지 않았음을 알 수 있도록 "외부"로 레이블이 지정됩니다.

회의 중에 Communication Services 사용자는 Azure Communication Services SDK를 통해 핵심 오디오, 비디오, 화면 공유 및 채팅 기능을 사용할 수 있습니다. Communication Services 사용자가 모임을 떠나거나 모임이 끝나면 더 이상 새 채팅 메시지를 보내거나 받을 수 없지만 모임 중에 보내고 받은 메시지에 액세스할 수 있습니다. 익명 Communication Services 사용자는 모임에 참가자를 더 추가할 수 없으며 모임에 대한 기록 또는 기록을 시작할 수 없습니다.

Teams 모임에 참가하는 데 필요한 데이터 흐름에 대한 추가 정보는 [클라이언트 및 서버 아키텍처 페이지](client-and-server-architecture.md)에서 사용할 수 있습니다. [그룹 통화 주인공 샘플](../samples/calling-hero-sample.md)은 웹 애플리케이션에서 Teams 모임에 참여하기 위한 예제 코드를 제공합니다.

## <a name="privacy"></a>개인 정보 취급 방침
Azure Communication Services와 Microsoft Teams 간의 상호 운용성을 통해 애플리케이션과 사용자가 Teams 통화, 회의 및 채팅에 참여할 수 있습니다. Teams 통화 또는 회의에서 기록 또는 전사를 사용하도록 설정할 때 애플리케이션 사용자에게 알림을 제공하는 것은 귀하의 책임입니다.

Microsoft는 기록 또는 전사가 시작되었음을 Azure Communication Services API를 통해 사용자에게 알리며, 애플리케이션의 사용자 인터페이스 내에서 사용자에게 실시간으로 이 사실을 전달해야 합니다. 귀하는 이 의무를 준수하지 않아 발생한 모든 비용 및 손해에 대해 Microsoft를 면책하는 데 동의합니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- BYOI 사용자는 Teams 채널에 대해 예약된 Teams 모임에 참가하고 오디오 및 비디오를 사용할 수 있지만 채널의 멤버가 아니므로 채팅 메시지를 보내거나 받을 수 없습니다.
- Microsoft Graph 사용하여 Teams [모임의 참가자를 나열하는](/graph/api/call-list-participants)경우 Communication Services 사용자에 대한 세부 정보는 현재 포함되지 않습니다.
- Teams 회의는 최대 1,000명의 참가자를 지원하지만 Azure Communication Services Calling SDK는 현재 350명의 참가자만 지원합니다.
- [Microsoft Teams Cloud Video Interop를](/microsoftteams/cloud-video-interop)통해 일부 디바이스는 Communication Services 사용자가 화면을 공유하는 경우 문제를 발견했습니다.
- 손, 함께 모드 및 중단실과 같은 기능은 Teams 사용자에게만 사용할 수 있습니다.
- 통화 SDK는 현재 Teams 모임에 대한 자막을 지원하지 않습니다.
- Communication Services 사용자가 Teams 라이브 [이벤트에](/microsoftteams/teams-live-events/what-are-teams-live-events) 참가할 수 없음
- Communication Services 사용자가 Teams 모임에 참가할 때 봇에 대한 Teams [활동 처리기 이벤트가](/microsoftteams/platform/bots/bot-basics?tabs=csharp) 발생하지 않습니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [BYOI 통화 앱을 Teams 모임에](../quickstarts/voice-video-calling/get-started-teams-interop.md) 
>  조인 [Teams 모임에 BYOI 채팅 앱 조인](../quickstarts/chat/meeting-interop.md)
