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
ms.openlocfilehash: 6b6444ef42e0ada42f374ed5d1e69aefd332a983
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131438876"
---
# <a name="join-a-teams-meeting"></a>Teams 회의 참가

> [!IMPORTANT]
> BYOI 상호 운용성은 공개 미리 보기이며 모든 Communication Services 애플리케이션 및 Teams 조직에서 사용할 수 있습니다.
>
> 미리 보기 API 및 SDK는 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 추천되지 않습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure 통신 서비스를 사용 하 여 사용자가 Teams 모임에 참여 하 고 참여할 수 있는 응용 프로그램을 빌드할 수 있습니다. [표준 ACS 가격은](https://azure.microsoft.com/pricing/details/communication-services/) 이러한 사용자에 게 적용 되지만 상호 운용성 기능 자체에 대 한 추가 요금은 없습니다. byoi (사용자 고유 id 가져오기) 모델을 사용 하면 사용자 인증을 제어할 수 있으며, 응용 프로그램의 사용자는 Teams 라이선스를 사용 하 여 Teams 회의에 참여할 필요가 없습니다. 이는 사용자 지정 응용 프로그램을 사용 하 여 가상 컨설팅 환경에 가입 하는 데 사용이 허가 된 Teams 사용자 (예: 의료 공급자 또는 금융 관리자)와 외부 사용자 (예: 환자 또는 클라이언트)가 사용할 수 있도록 하는 b2b 솔루션에 이상적입니다.

Azure Communication Services sdk를 사용 하 여 Microsoft 365 Teams id를 사용할 수도 있습니다. 자세한 내용은 [여기](./teams-interop.md)를 참조하세요.

현재는 Teams 사용자가 SDK를 호출 하는 Azure 통신 서비스를 사용 하 여 시작 된 호출에 가입할 수 없습니다.

## <a name="enabling-anonymous-meeting-join-in-your-teams-tenant"></a>Teams 테 넌 트에서 익명 모임 조인 사용

byoi 사용자가 Teams 회의에 조인 하는 경우 Teams 웹 응용 프로그램을 사용 하 여 익명으로 Teams 회의에 참여 하는 사용자와 마찬가지로 익명의 외부 사용자로 간주 됩니다. byoi 사용자가 익명 사용자로 Teams 모임에 조인 하는 기능은 기존 Teams 익명 모임 조인을 제어 하는 기존 "익명 모임 조인 허용" 구성에 의해 제어 됩니다. 이 설정은 [Teams 관리 센터](https://admin.teams.microsoft.com/meetings/settings) 또는 Teams PowerShell cmdlet [Set-CsTeamsMeetingConfiguration](/powershell/module/skype/set-csteamsmeetingconfiguration)에서 업데이트할 수 있습니다.  

Azure communication Teams Services를 사용 하 여 빌드된 사용자 지정 응용 프로그램은 최종 사용자 또는 봇에서 사용 될 수 있으며, 응용 프로그램 개발자가 통신의 일부로 명시적으로 표시 하지 않는 한 사용자를 Teams 하는 것 처럼 표시 되는 방법에는 차이가 없습니다. 사용자 지정 애플리케이션은 Teams 모임을 보호하기 위해 사용자 인증 및 기타 보안 조치를 고려해야 합니다. 익명 사용자가 모임에 참가하도록 설정하는 경우 보안에 미치는 영향을 고려하고, [Teams 보안 가이드](/microsoftteams/teams-security-guide#addressing-threats-to-teams-meetings)를 사용하여 익명 사용자가 사용할 수 있는 기능을 구성합니다.

## <a name="meeting-experience"></a>모임 환경

Teams 익명 회의 참여와 마찬가지로 애플리케이션에 참여할 회의 링크가 있어야 합니다. 이 링크는 Graph API 또는 Microsoft Teams의 일정에서 가져올 수 있습니다. Teams에 표시 되는 byoi 사용자의 이름은 SDK를 호출 하는 통신 서비스를 통해 구성할 수 있으며, 사용자가 Azure Active Directory Teams를 사용 하 여 인증 되지 않았음을 알리기 위해 "외부"로 레이블이 지정 됩니다.

모임 중에 통신 서비스 사용자는 Azure Communication Services Sdk를 통해 핵심 오디오, 비디오, 화면 공유 및 채팅 기능을 사용할 수 있습니다. 통신 서비스 사용자가 모임이 나 모임을 종료 한 후에는 더 이상 새 채팅 메시지를 보내거나 받을 수 없지만 모임 중에 보내고 받은 메시지에는 액세스할 수 있습니다. 익명 통신 서비스 사용자는 모임에서 참가자를 추가/제거할 수 없으며 모임에 대 한 녹음 또는 기록을 시작할 수 없습니다.

Teams 모임에 참가하는 데 필요한 데이터 흐름에 대한 추가 정보는 [클라이언트 및 서버 아키텍처 페이지](client-and-server-architecture.md)에서 사용할 수 있습니다. [그룹 통화 주인공 샘플](../samples/calling-hero-sample.md)은 웹 애플리케이션에서 Teams 모임에 참여하기 위한 예제 코드를 제공합니다.

## <a name="privacy"></a>개인 정보 취급 방침
Azure Communication Services와 Microsoft Teams 간의 상호 운용성을 통해 애플리케이션과 사용자가 Teams 통화, 회의 및 채팅에 참여할 수 있습니다. Teams 통화 또는 회의에서 기록 또는 전사를 사용하도록 설정할 때 애플리케이션 사용자에게 알림을 제공하는 것은 귀하의 책임입니다.

Microsoft는 기록 또는 전사가 시작되었음을 Azure Communication Services API를 통해 사용자에게 알리며, 애플리케이션의 사용자 인터페이스 내에서 사용자에게 실시간으로 이 사실을 전달해야 합니다. 귀하는 이 의무를 준수하지 않아 발생한 모든 비용 및 손해에 대해 Microsoft를 면책하는 데 동의합니다.

## <a name="limitations-and-known-issues"></a>제한 사항 및 알려진 문제

- byoi 사용자는 Teams 채널에 대해 예약 되 고 오디오 및 비디오를 사용 하도록 예약 된 Teams 모임에 참여할 수 있지만 채팅 메시지는 채널의 멤버가 아니기 때문에 보내거나 받을 수 없습니다.
- Microsoft Graph를 사용 하 여 [Teams 회의에 참가자를 나열할](/graph/api/call-list-participants)때 통신 서비스 사용자에 대 한 세부 정보는 현재 포함 되어 있지 않습니다.
- PowerPoint 프레젠테이션은 통신 서비스 사용자에 대해 렌더링 되지 않습니다.
- Teams 회의는 최대 1000 명의 참가자를 지원 하지만 SDK를 호출 하는 Azure 통신 서비스는 현재 350 참가자만 지원 합니다.
- [Microsoft Teams에 대 한 클라우드 비디오 Interop](/microsoftteams/cloud-video-interop)를 사용 하면 통신 서비스 사용자가 화면을 공유할 때 일부 장치에서 문제가 발생 했습니다.
- Teams 사용자에 게는 발생 한 직접, 함께 모드, 브레이크 실 등의 기능을 사용할 수 있습니다.
- 호출 SDK는 현재 Teams 모임의 폐쇄 캡션을 지원 하지 않습니다.
- 통신 서비스 사용자가 [Teams 라이브 이벤트](/microsoftteams/teams-live-events/what-are-teams-live-events) 에 연결할 수 없음
- bot에 대 한 [Teams activity handler 이벤트](/microsoftteams/platform/bots/bot-basics?tabs=csharp) 는 통신 서비스 사용자가 Teams 회의에 참여할 때 발생 하지 않습니다.

## <a name="next-steps"></a>다음 단계

- [방법: Teams 모임 참여](../how-tos/calling-sdk/teams-interoperability.md)
- [빠른 시작: byoi 호출 앱을 Teams 모임에 조인](../quickstarts/voice-video-calling/get-started-teams-interop.md)
- [빠른 시작: byoi 채팅 앱을 Teams 회의에 조인](../quickstarts/chat/meeting-interop.md)
