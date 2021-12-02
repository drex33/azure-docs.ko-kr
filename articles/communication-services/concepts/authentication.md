---
title: Azure Communication Services에 인증
titleSuffix: An Azure Communication Services concept document
description: 애플리케이션 또는 서비스가 Communication Services에 인증할 수 있는 다양한 방법에 대해 알아봅니다.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: identity
ms.openlocfilehash: 75a225b184f8759e88846361aec53f7133a4ff44
ms.sourcegitcommit: 93c7420c00141af83ed3294923b4826dd4dc6ff2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133440394"
---
# <a name="authenticate-to-azure-communication-services"></a>Azure Communication Services에 인증

Azure Communication Services와의 모든 클라이언트 상호 작용에는 인증이 필요합니다. 일반적인 아키텍처의 경우 [클라이언트 및 서버 아키텍처](./client-and-server-architecture.md), ‘액세스 키’ 또는 ‘관리 ID’가 인증에 사용됩니다. 

다른 유형의 인증은 ‘사용자 액세스 토큰’을 사용하여 사용자 참여가 필요한 서비스에 대해 인증을 진행합니다. 예를 들어 채팅 또는 호출 서비스는 ‘사용자 액세스 토큰’을 활용하여 사용자가 스레드에 추가되고 서로 대화할 수 있도록 지원합니다.

## <a name="authentication-options"></a>인증 옵션

다음 표는 Azure Communication Services SDK 및 해당 인증 옵션을 보여 줍니다.

| SDK)    | 인증 옵션                               |
| ----------------- | ----------------------------------------------------|
| ID          | 액세스 키 또는 관리 ID                      |
| sms               | 액세스 키 또는 관리 ID                      |
| 전화 번호     | 액세스 키 또는 관리 ID                      |
| 호출           | 사용자 액세스 토큰                                   |
| 채팅              | 사용자 액세스 토큰                                   |

각 권한 부여 옵션에 대한 간략한 설명은 다음과 같습니다.

### <a name="access-key"></a>선택키

액세스 키 인증은 신뢰할 수 있는 서비스 환경에서 실행되는 서비스 애플리케이션에 적합합니다. 액세스 키는 Azure Communication Services 포털에서 찾을 수 있습니다. 서비스 애플리케이션이 이를 자격 증명으로 사용하여 해당 SDK를 초기화합니다. [ID SDK](../quickstarts/access-tokens.md)에서 사용되는 방법의 예를 참조하세요. 

액세스 키는 리소스의 연결 문자열의 일부이기 때문에 연결 문자열을 사용하는 인증은 액세스 키를 사용하는 인증과 동등합니다.

액세스 키를 사용하여 ACS의 API를 수동으로 호출하려는 경우 요청에 서명해야 합니다. 요청에 서명하는 방법은 [자습서](../tutorials/hmac-header-tutorial.md)에 자세히 설명되어 있습니다.

### <a name="managed-identity"></a>관리 ID

관리 ID는 다른 권한 부여 옵션에 비해 우수한 보안과 사용 편의성을 제공합니다. 예를 들어 Azure AD를 사용하면 액세스 키 권한 부여와 마찬가지로 코드 내에 계정 액세스 키를 저장하지 않아도 됩니다. 통신 서비스 애플리케이션에서 액세스 키 권한 부여를 계속 사용할 수도 있지만, Microsoft에서는 가능하면 Azure AD로 옮기는 것을 권장합니다. 

관리 ID를 설정하려면 [Azure CLI에서 등록된 애플리케이션을 만듭니다](../quickstarts/identity/service-principal-from-cli.md). 그런 다음 엔드포인트와 자격 증명을 사용하여 SDK를 인증할 수 있습니다. [관리 ID](../quickstarts/identity/service-principal.md)를 사용하는 방법의 예를 참조하세요.

### <a name="user-access-tokens"></a>사용자 액세스 토큰

사용자 액세스 토큰은 ID SDK를 사용하여 생성되며 ID SDK에서 생성된 사용자들과 연결됩니다. [사용자를 만들고 토큰을 생성](../quickstarts/access-tokens.md)하는 방법의 예를 참조하세요. 그런 다음 채팅 또는 호출 SDK에서 대화에 추가된 참가자를 인증하는 데 사용자 액세스 토큰이 사용됩니다. 자세한 내용은 [애플리케이션에 채팅 추가](../quickstarts/chat/get-started.md)를 참조하세요. 사용자 액세스 토큰 인증은 보안이 유지되는 Azure 리소스가 아닌 사용자를 인증하는 데 사용된다는 점에서 액세스 키 및 관리 ID 인증과 다릅니다.

## <a name="using-identity-for-monitoring-and-metrics"></a>모니터링 및 메트릭에 ID 사용

사용자 ID는 Azure Monitor 통해 수집된 로그 및 메트릭의 기본 키 역할을 하기 위한 것입니다. 예를 들어 특정 사용자의 모든 호출을 보려면 특정 Azure Communication Services ID(또는 ID)를 단일 사용자에게 매핑하는 방식으로 인증을 설정해야 합니다. [로그 분석](../concepts/analytics/log-analytics.md)및 사용 가능한 [메트릭에](../concepts/metrics.md) 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Communication Services 리소스 만들기 및 관리](../quickstarts/create-communication-resource.md)
> [Azure CLI에서 Azure Active Directory 및 관리 ID 애플리케이션 만들기](../quickstarts/identity/service-principal-from-cli.md)
> [사용자 액세스 토큰 만들기](../quickstarts/access-tokens.md)

자세한 내용은 다음 문서를 참조하세요.
- [클라이언트 및 서버 아키텍처에 대한 자세한 정보](../concepts/client-and-server-architecture.md)
