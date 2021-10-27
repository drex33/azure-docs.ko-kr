---
title: 사용자 지정 Teams 엔드포인트 빌드
titleSuffix: An Azure Communication Services concept document
description: 이 문서에서는 사용자 지정 Teams 엔드포인트를 빌드하는 방법을 설명합니다.
author: tomaschladek
manager: nmurav
services: azure-communication-services
ms.author: tchladek
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: teams-interop
ms.openlocfilehash: fe3dcf7c56c72f259a456875f3ab2716ad609353
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130250163"
---
# <a name="build-a-custom-teams-endpoint"></a>사용자 지정 Teams 엔드포인트 빌드

> [!IMPORTANT]
> 사용자 지정 Teams 엔드포인트 환경을 사용하거나 사용하지 않도록 설정하려면 [이 양식](https://forms.office.com/r/B8p5KqCH19)을 작성하여 제출합니다.

Azure Communication Services를 사용하여 Microsoft Teams 클라이언트 또는 다른 사용자 지정 Teams 엔드포인트와 통신하는 사용자 지정 Teams 엔드포인트를 빌드할 수 있습니다. 사용자 지정 Teams 엔드포인트를 사용하면 Teams 사용자에 대한 음성, 비디오, 채팅 및 화면 공유 환경을 사용자 지정할 수 있습니다.

Azure Communication Services ID SDK를 사용하여 Teams 사용자의 Azure AD(Azure Active Directory) 액세스 토큰을 통신 ID 액세스 토큰으로 교환할 수 있습니다. 다음 섹션의 다이어그램에서는 가상 회사 Fabrikam이 가상 회사 Contoso의 고객인 다중테넌트 사용 사례를 보여줍니다.

## <a name="calling"></a>호출 

음성, 비디오 및 화면 공유 기능은 Azure Communication Services Calling SDK를 통해 제공됩니다. 다음 다이어그램은 통화 환경을 사용자 지정 Teams 엔드포인트와 통합할 때 따라야 하는 프로세스의 개요를 보여줍니다.

![사용자 지정 Teams 엔드포인트 환경에 대해 호출 기능을 사용하도록 설정하는 프로세스의 다이어그램입니다.](./media/teams-identities/teams-identity-calling-overview.png)

## <a name="chat"></a>채팅

필요에 따라 사용자 지정 Teams 엔드포인트를 사용하여 Graph API를 통해 채팅 기능을 통합할 수도 있습니다. Graph API에 대한 자세한 내용은 [채팅 리소스 유형](/graph/api/channel-post-messages) 설명서를 참조하세요. 

![사용자 지정 Teams 엔드포인트 환경에 대해 채팅 기능을 사용하도록 설정하는 프로세스의 다이어그램입니다.](./media/teams-identities/teams-identity-chat-overview.png)

## <a name="azure-communication-services-permissions"></a>Azure Communication Services 권한

### <a name="delegated-permissions"></a>위임된 권한

|   사용 권한    |  표시 문자열   |  Description | 관리자 동의가 필요함 | 지원되는 Microsoft 계정 |
|:--- |:--- |:--- |:--- |:--- |
| _`https://auth.msft.communication.azure.com/VoIP`_ | Teams 내 호출 관리 | Teams 호출을 시작, 조인, 전달, 전송 또는 종료하고 호출 속성을 업데이트합니다. | 아니요 | 아니요 |

### <a name="application-permissions"></a>애플리케이션 사용 권한

없음

### <a name="roles-for-granting-consent-on-behalf-of-a-company"></a>회사를 대신하여 동의를 부여하는 역할

- 글로벌 관리자
- 애플리케이션 관리자(프라이빗 미리 보기에만 해당)
- 클라우드 애플리케이션 관리자(프라이빗 미리 보기에만 해당)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Teams 액세스 토큰 발급](../quickstarts/manage-teams-identity.md)

[Teams 상호 운용성](./teams-interop.md)에 대해 알아봅니다.
