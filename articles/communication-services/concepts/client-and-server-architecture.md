---
title: 클라이언트 및 서버 아키텍처
titleSuffix: An Azure Communication Services concept document
description: Communication Services 아키텍처에 관해 알아봅니다.
author: probableprime
manager: mikben
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 75ab8032d197b00516578f1e0a92f29122126249
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122966625"
---
# <a name="client-and-server-architecture"></a>클라이언트 및 서버 아키텍처

이 페이지에서는 여러 Azure Communication Service 시나리오에 포함된 일반적인 아키텍처 구성 요소 및 데이터 흐름을 보여줍니다. 관련 구성 요소는 다음과 같습니다.

1. **클라이언트 애플리케이션.** 이 웹 사이트 또는 네이티브 애플리케이션은 최종 사용자의 커뮤니케이션을 위해 활용됩니다. Azure Communication Services는 여러 브라우저 및 애플리케이션 플랫폼을 위한 [SDK 클라이언트 라이브러리](sdk-options.md)를 제공합니다. 핵심 SDK 외에도 [UI 도구 키트](https://aka.ms/acsstorybook)를 사용하여 브라우저 앱 개발을 가속화할 수 있습니다.
1. **ID 관리 서비스.**  비즈니스 논리에서 사용자 및 기타 개념을 Azure Communication Services에 매핑하고 필요할 때 사용자에 대해 토큰을 만들기 위해 빌드하는 서비스 기능입니다.
1. **호출 관리 서비스.**  음성 및 음성 호출을 관리하고 모니터링하기 위해 빌드하는 서비스 기능입니다.  이 서비스는 호출 만들기, 사용자 초대, 전화 번호 호출, 오디오 재생, DMTF 톤 수신을 수행하고, 호출 자동화 SDK 및 REST API를 통해 다른 많은 호출 기능을 활용할 수 있습니다.


## <a name="user-access-management"></a>사용자 액세스 관리

Azure Communication Services 클라이언트는 Communication Services 리소스에 안전하게 액세스하기 위해 `user access tokens`를 제공해야 합니다. 토큰 및 연결 문자열의 중요성 또는 이를 생성하는 데 필요한 관리 ID로 인해 `User access tokens`는 신뢰할 수 있는 서비스에서 생성되고 관리되어야 합니다. 액세스 토큰을 제대로 관리하지 못하면 리소스를 잘못 사용하여 추가 요금이 부과될 수 있습니다.

:::image type="content" source="../media/scenarios/architecture_v2_identity.svg" alt-text="사용자 액세스 토큰 아키텍처를 보여 주는 다이어그램":::

### <a name="dataflows"></a>데이터 흐름
1. 사용자가 클라이언트 애플리케이션을 시작합니다. 이 애플리케이션 및 사용자 인증 체계의 디자인은 사용자가 관리합니다.
2. 클라이언트 애플리케이션이 ID 관리 서비스에 연결합니다. ID 관리 서비스는 Azure Communication Service ID에 대해 사용자와 기타 주소 지정 가능한 개체(예: 서비스 또는 봇) 사이의 매핑을 유지 관리합니다.
3. ID 관리 서비스는 적용 가능한 ID에 대해 사용자 액세스 토큰을 만듭니다. 이전에 Azure Communication Services ID가 할당되지 않았으면 새 ID가 생성됩니다.  

### <a name="resources"></a>리소스
- **개념:** [사용자 ID](identity-model.md)
- **빠른 시작:** [액세스 토큰 만들기 및 관리](../quickstarts/access-tokens.md)
- **자습서:** [Azure Functions를 사용하여 ID 관리 서비스 빌드](../tutorials/trusted-service-tutorial.md)

> [!IMPORTANT]
> 간단한 설명을 위해 여기에서는 후속 아키텍처 흐름의 사용자 액세스 관리 및 토큰 배포를 표시하지 않습니다.


## <a name="calling-a-user-without-push-notifications"></a>푸시 알림 없이 사용자 호출
가장 간단한 음성 및 음성 호출 시나리오에는 푸시 알림 없이 포그라운드에서 사용자가 다른 사용자를 호출하는 과정이 포함됩니다.

:::image type="content" source="../media/scenarios/architecture_v2_calling_without_notifications.svg" alt-text="푸시 알림 없이 호출하는 Communication Services 아키텍처를 보여주는 다이어그램입니다.":::

### <a name="dataflows"></a>데이터 흐름

1. 수락 사용자가 수신되는 전화 호출을 받을 수 있도록 호출 클라이언트를 초기화합니다.
2. 시작 사용자는 호출할 대상 사용자의 Azure Communication Services ID가 필요합니다. 일반적인 환경에는 사용자의 친구들 및 연결된 Azure Communication Service ID를 수집하는 ID 관리 서비스로 유지 관리되는 *친구 목록* 이 포함될 수 있습니다.
3. 시작 사용자가 호출 클라이언트를 초기화하고 원격 사용자를 호출합니다.
4. 수락 사용자에게는 호출 SDK를 통해 수신 중인 호출에 대한 알림이 표시됩니다.
5. 이러한 사용자는 호출 중 음성과 동영상을 사용하여 서로 통신합니다.

### <a name="resources"></a>리소스
- **개념:** [호출 개요](voice-video-calling/calling-sdk-features.md)
- **빠른 시작:** [앱에 음성 호출 추가](../quickstarts/voice-video-calling/getting-started-with-calling.md)
- **빠른 시작:** [앱에 동영상 호출 추가](../quickstarts/voice-video-calling/get-started-with-video-calling.md)
- **주인공 샘플:** [Web, iOS 및 Android를 위한 그룹 호출](../samples/calling-hero-sample.md)


## <a name="joining-a-user-created-group-call"></a>사용자가 만든 그룹 호출에 참가
명시적인 초대 없이 사용자가 참가하도록 허용해야 할 수 있습니다. 예를 들어 연결된 호출이 포함된 *소셜 공간* 이 있고, 사용자가 한가할 때 호출에 참가할 수 있습니다. 이 첫 번째 데이터 흐름에서는 클라이언트가 처음에 만드는 호출을 보여줍니다.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_client_driven.svg" alt-text="Communication Services 아키텍처 호출의 대역 외 신호 전송을 보여주는 다이어그램입니다.":::

### <a name="dataflows"></a>데이터 흐름
1. 시작 사용자가 호출 클라이언트를 초기화하고 그룹 호출을 수행합니다.
2. 시작 사용자가 그룹 호출 ID를 호출 관리 서비스와 공유합니다.
3. 호출 관리 서비스는 이 호출 ID를 다른 사용자와 공유합니다. 예를 들어 애플리케이션이 예약된 이벤트로부터 발생할 경우 그룹 호출 ID는 예약된 이벤트의 데이터 모델의 특성일 수 있습니다.
4. 다른 사용자는 그룹 호출 ID를 사용하여 호출에 참가합니다.
5. 이러한 사용자는 호출 중 음성과 동영상을 사용하여 서로 통신합니다.


## <a name="joining-a-scheduled-teams-call"></a>예약된 Teams 호출 참가
Azure Communication Service 애플리케이션은 Teams 호출에 참가할 수 있습니다. 이것은 소비자가 사용자 지정 애플리케이션 및 사용자 지정 ID를 사용하고 비즈니스는 Teams를 사용하는 많은 비즈니스-소비자 시나리오에 이상적입니다.

:::image type="content" source="../media/scenarios/architecture_v2_calling_join_teams_driven.svg" alt-text="Teams 회의에 참가하기 위한 Communication Services 아키텍처를 보여주는 다이어그램입니다.":::


### <a name="dataflows"></a>데이터 흐름
1. 호출 관리 서비스는 [Graph API](/graph/api/resources/onlinemeeting?view=graph-rest-1.0)를 사용하여 그룹 호출을 만듭니다. 또 다른 패턴에는 [Bookings](https://www.microsoft.com/microsoft-365/business/scheduling-and-booking-app), Outlook, Teams 또는 Microsoft 365 에코시스템의 다른 예약 경험을 사용하여 그룹 호출을 만드는 최종 사용자가 포함됩니다.
2. 호출 관리 서비스는 Teams 호출 세부정보를 Azure Communication Service 클라이언트와 공유합니다.
3. 일반적으로 Teams 사용자는 호출에 참가하고 외부 사용자가 로비를 통해 참가하도록 허용해야 합니다. 하지만 이러한 경험은 Teams 테넌트 구성 및 특정 회의 설정에 다라 달라집니다.
4. Azure Communication Service 사용자는 2단계에 수신된 세부정보를 사용해서 호출 클라이언트를 초기화하고 Teams 회의에 참가합니다.
5. 이러한 사용자는 호출 중 음성과 동영상을 사용하여 서로 통신합니다.

### <a name="resources"></a>리소스
- **개념:** [Teams 상호 운용성](teams-interop.md)
- **빠른 시작:** [Teams 회의 참가](../quickstarts/voice-video-calling/get-started-teams-interop.md)