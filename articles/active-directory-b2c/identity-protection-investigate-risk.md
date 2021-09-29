---
title: Azure Active Directory B2C ID 보호로 위험 조사
description: Azure AD B2C ID 보호에서 위험한 사용자 및 검색을 조사하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 09/16/2021
ms.custom: project-no-code
ms.author: mimart
author: msmimart
manager: celested
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 91b154604e8d2ec898b45aef43f453d769934464
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128572370"
---
# <a name="investigate-risk-with-identity-protection-in-azure-ad-b2c"></a>Azure AD B2C에서 ID 보호로 위험 조사

ID 보호는 Azure AD B2C 테넌트에 대한 지속적인 위험 검색을 제공합니다. 이를 통해 조직은 ID 기반 위험을 검색, 조사 및 수정할 수 있습니다. ID 보호는 Azure AD B2C 테넌트의 ID 위험을 조사하는 데 사용할 수 있는 위험 보고서와 함께 제공됩니다. 이 문서에서는 위험을 조사하고 완화하는 방법을 알아봅니다.

## <a name="overview"></a>개요

Azure AD B2C ID 보호는 두 개의 보고서를 제공합니다. *위험한 사용자* 보고서는 관리자가 위험에 노출되는 사용자와 검색에 대한 세부 정보를 찾을 수 있는 위치입니다. *위험 검색* 보고서는 유형, 동시에 트리거되는 기타 위험, 로그인 시도 위치 등을 포함하여 각 위험 검색에 대한 정보를 제공합니다.

각 보고서는 보고서 맨 위에 표시된 기간에 해당하는 모든 검색 항목 목록으로 시작됩니다. 보고서의 맨 위에 있는 필터를 사용하여 보고서를 필터링할 수 있습니다. 관리자는 데이터를 다운로드하거나 [MS Graph API 및 Microsoft Graph PowerShell SDK](../active-directory/identity-protection/howto-identity-protection-graph-api.md)를 사용하여 데이터를 지속적으로 내보낼 수 있습니다.

## <a name="service-limitations-and-considerations"></a>서비스 제한 사항 및 고려 사항

ID 보호를 사용하는 경우 다음 사항을 고려하세요.

- ID 보호는 기본적으로 설정되어 있습니다.
- ID 보호는 Google 또는 Facebook과 같은 로컬 및 소셜 ID에 모두 사용할 수 있습니다. 소셜 ID의 경우 조건부 액세스를 활성화해야 합니다. 소셜 계정 자격 증명은 외부 ID 공급자에 의해 관리되므로 검색이 제한됩니다.
- Azure AD B2C에서는 [Azure AD ID 보호 위험 검색](../active-directory/identity-protection/overview-identity-protection.md)의 하위 집합만 사용할 수 있습니다. 다음 위험 검색은 Azure AD B2C에서 지원됩니다.  

|위험 탐지 유형  |Description  |
|---------|---------|
| 비정상적 이동     | 사용자의 최근 로그인을 기준으로 비정상적인 위치에서 로그인합니다.        |
|익명 IP 주소     | 익명 IP 주소에서 로그인합니다(예: Tor 브라우저, 익명성 도구 VPN).        |
|맬웨어 연결 IP 주소     | 맬웨어 연결 IP 주소에서 로그인합니다.         |
|일반적이지 않은 로그인 속성     | 지정된 사용자에게 최근 확인되지 않은 속성으로 로그인합니다.        |
|관리자가 확인한 사용자 손상    | 관리자가 사용자가 손상된 것으로 표시했습니다.             |
|암호 스프레이     | 암호 스프레이 공격을 통해 로그인합니다.      |
|Azure AD 위협 인텔리전스     | Microsoft의 내부 및 외부 위협 인텔리전스 소스가 알려진 공격 패턴을 식별했습니다.        |

## <a name="pricing-tier"></a>가격 책정 계층

일부 ID 보호 기능에는 Azure AD B2C Premium P2가 필요합니다. 필요한 경우 [Azure AD B2C 가격 책정 계층을 Premium P2로 변경](./billing.md)하세요. 다음 표에는 ID 보호 기능과 필요한 가격 책정 계층이 요약되어 있습니다.  

|기능   |P1   |P2|
|----------|:-----------:|:------------:|
|위험한 사용자 보고서     |&#x2713; |&#x2713; |
|위험한 사용자 보고서 세부 정보  | |&#x2713; |
|위험한 사용자 보고서 수정    | &#x2713; |&#x2713; |
|위험 검색 보고서   |&#x2713;|&#x2713;|
|위험 검색 보고서 세부 정보  ||&#x2713;|
|보고서 다운로드 |  &#x2713;| &#x2713;|
|MS Graph API 액세스 |  &#x2713;| &#x2713;|

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="investigate-risky-users"></a>위험한 사용자 조사

관리자는 위험한 사용자 보고서에 제공되는 정보를 통해 다음을 확인할 수 있습니다.

- **위험 상태**, **위험에 노출된** 사용자, 위험을 **해결한** 사용자 또는 위험을 **해제한** 사용자를 보여 줍니다.
- 탐지 항목에 대한 세부 정보
- 모든 위험한 로그인 기록
- 위험 기록
 
그러면 관리자는 이러한 이벤트에 대한 작업을 수행하도록 선택할 수 있습니다. 관리자는 다음을 선택할 수 있습니다.

- 사용자 암호 재설정
- 사용자 도용 확인
- 사용자 위험 해제
- 사용자가 로그인하지 못하도록 차단
- Azure ATP를 사용하여 자세히 조사

관리자는 Azure Portal에서 또는 프로그래밍 방식으로 Microsoft Graph API [사용자 위험 해제](/graph/api/riskyusers-dismiss?preserve-view=true&view=graph-rest-beta)를 통해 사용자의 위험을 해제하도록 선택할 수 있습니다. 사용자의 위험을 해제하려면 관리자 권한이 필요합니다. 위험 수정은 위험한 사용자 또는 사용자 대신 관리자가 수행할 수 있습니다(예: 암호 재설정을 통해).

### <a name="navigating-the-risky-users-report"></a>위험한 사용자 보고서 탐색

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. **Azure 서비스** 에서 **Azure AD B2C** 를 선택합니다. 또는 검색 상자를 사용하여 **Azure AD B2C** 를 찾고 선택합니다.
1. **보안** 에서 **위험한 사용자** 를 선택합니다.

   ![위험한 사용자](media/identity-protection-investigate-risk/risky-users.png)

    개별 항목을 선택하면 검색 항목 아래의 세부 정보 창이 확장됩니다. 관리자는 세부 정보 보기를 사용하여 각 검색 항목을 살펴보고 관련 작업을 수행할 수 있습니다.

    ![위험한 사용자 작업](media/identity-protection-investigate-risk/risky-users-report-actions.png)


## <a name="risk-detections-report"></a>위험 검색 보고서

위험 검색 보고서에는 최대 지난 90일(3개월) 동안의 필터링 가능한 데이터가 포함되어 있습니다.

관리자는 위험 검색 보고서에 제공되는 정보를 통해 다음을 확인할 수 있습니다.

- 각 위험 검색에 대한 정보(예: 유형)
- 동시에 트리거되는 기타 위험
- 로그인 시도 위치

그러면 관리자는 사용자의 위험 또는 로그인 보고서로 돌아가서 수집된 정보에 따라 작업을 수행하도록 선택할 수 있습니다.

### <a name="navigating-the-risk-detections-report"></a>위험 검색 보고서 탐색

1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **보안** 에서 **위험 검색** 을 선택합니다.

   ![위험 탐지](media/identity-protection-investigate-risk/risk-detections.png)


## <a name="next-steps"></a>다음 단계

- [조건부 액세스를 사용자 흐름에 추가](conditional-access-user-flow.md)합니다.