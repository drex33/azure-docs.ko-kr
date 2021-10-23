---
title: Azure AD B2C의 ID 보호 및 조건부 액세스
description: ID 보호에서 위험한 로그인 및 위험 검색에 대한 가시성을 제공하는 방법을 알아봅니다. 조건부 액세스를 통해 Azure AD B2C 테넌트에서 위험 이벤트를 기반으로 하는 조직 정책을 적용하는 방법을 알아봅니다.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: overview
ms.date: 05/28/2021
ms.author: kengaderdus
author: kengaderdus
manager: CelesteDG
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8b53e5397c9e4e04a7049d18f9e9efdcfc62a921
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130037798"
---
# <a name="identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C에 대한 ID 보호 및 조건부 액세스

Azure AD ID 보호 및 조건부 액세스를 사용하여 Azure AD B2C(Azure Active Directory B2C)의 보안을 강화합니다. 위험한 사용자 및 위험한 로그인을 포함한 ID 보호 위험 검색 기능은 Azure AD B2C 테넌트에서 자동으로 검색되어 표시됩니다. 이러한 위험 검색을 사용하여 작업을 결정하고 조직 정책을 적용하는 조건부 액세스 정책을 만들 수 있습니다. 이러한 기능을 함께 사용하면 Azure AD B2C 애플리케이션 소유자가 위험한 인증 및 액세스 정책을 더 효율적으로 제어할 수 있습니다.
  
Azure AD의 [ID 보호](../active-directory/identity-protection/overview-identity-protection.md) 및 [조건부 액세스](../active-directory/conditional-access/overview.md)에 이미 익숙한 경우 이 문서에서 설명하는 사소한 차이점을 포함하여 Azure AD B2C에서 이러한 기능을 사용하는 것이 익숙할 것입니다.

![B2C 테넌트의 조건부 액세스](media/conditional-access-identity-protection-overview/conditional-access-b2c.png)

> [!NOTE]
> 위험한 로그인 정책을 만들려면 Azure AD B2C **Premium P2** 가 필요합니다. **Premium P1** 테넌트는 위치, 애플리케이션, 사용자 기반 또는 그룹 기반 정책을 기반으로 하는 정책을 만들 수 있습니다. 자세한 내용은 [Azure AD B2C 가격 책정 계층 변경](billing.md#change-your-azure-ad-pricing-tier)을 참조하세요.

## <a name="benefits-of-identity-protection-and-conditional-access-for-azure-ad-b2c"></a>Azure AD B2C에 대한 ID 보호 및 조건부 액세스의 이점  

조건부 액세스 정책을 ID 보호 위험 검색과 쌍으로 연결하면 적절한 정책 작업을 통해 위험한 인증에 응답할 수 있습니다.

- **애플리케이션 및 고객 기반의 인증 위험에 대한 새로운 수준의 가시성을 얻습니다**. Azure AD 및 Microsoft 계정 전체에서 매월 수십억 개의 인증 신호를 받는 경우 이제 위험 검색 알고리즘에서 해당 지역 소비자 또는 시민 인증에 대한 인증을 낮음, 중간 또는 높음 위험 플래그로 지정합니다.
- **사용자 고유의 적응 인증을 구성하여 위험을 자동으로 해결합니다**. 지정된 애플리케이션의 경우 MFA(다단계 인증)에서와 같이 두 번째 인증 단계를 제공하기 위해 특정 사용자 세트를 요구할 수 있습니다. 또는 검색된 위험 수준에 따라 액세스를 차단할 수 있습니다. 다른 Azure AD B2C 환경과 마찬가지로 조직의 음성, 스타일 및 브랜드를 사용하여 결과 최종 사용자 환경을 사용자 지정할 수 있습니다. 또한 사용자가 액세스 권한을 얻을 수 없는 경우 완화 대안을 표시할 수도 있습니다.
- **위치, 그룹 및 앱에 따라 액세스를 제어합니다**.  조건부 액세스를 사용하여 위험 기반이 상황을 제어할 수도 있습니다. 예를 들어 특정 앱에 액세스하는 고객에게 MFA를 요구하거나 지정된 지리적 위치의 액세스를 차단할 수 있습니다.
- **Azure AD B2C 사용자 흐름 및 Identity Experience Framework 사용자 지정 정책과 통합됩니다**. 기존 사용자 지정 환경을 사용하고, 조건부 액세스와 상호 작용하는 데 필요한 제어를 추가합니다. 또한 지식 기반 액세스 또는 선호하는 사용자 고유의 MFA 공급자와 같은 액세스 권한을 부여하기 위한 고급 시나리오를 구현할 수도 있습니다.

## <a name="feature-differences-and-limitations"></a>기능의 차이점 및 제한 사항

Azure AD B2C의 ID 보호 및 조건부 액세스는 일반적으로 Azure AD에서와 동일한 방식으로 작동하지만 다음과 같은 예외가 있습니다.

- Security Center는 Azure AD B2C에서 사용할 수 없습니다.

- ID 보호 및 조건부 액세스는 Azure AD B2C 테넌트의 ROPC 서버 간 흐름에 지원되지 않습니다.

- ID 보호 위험 검색은 Azure AD B2C 테넌트에서 로컬 B2C 계정에만 사용할 수 있으며, Google 또는 Facebook과 같은 소셜 ID에는 사용할 수 없습니다.

- ID 보호 위험 검색의 하위 집합은 Azure AD B2C 테넌트에서 사용할 수 있습니다. [ID 보호로 위험 조사](identity-protection-investigate-risk.md) 및 [사용자 흐름에 조건부 액세스 추가](conditional-access-user-flow.md)를 참조하세요.

- 조건부 액세스 디바이스 규정 준수 기능은 Azure AD B2C 테넌트에서 사용할 수 없습니다.


## <a name="integrate-conditional-access-with-user-flows-and-custom-policies"></a>조건부 액세스를 사용자 흐름 및 사용자 지정 정책과 통합

Azure AD B2C에서는 기본 제공 사용자 흐름에서 조건부 액세스 조건을 트리거할 수 있습니다. 또한 조건부 액세스를 사용자 지정 정책에 통합할 수 있습니다. B2C 사용자 흐름의 다른 측면과 마찬가지로 최종 사용자 환경 메시징은 조직의 음성, 브랜드 및 완화 대안에 따라 사용자 지정할 수 있습니다. [조건부 액세스를 사용자 흐름에 추가](conditional-access-user-flow.md)를 참조하세요.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Microsoft Graph API를 사용하여 Azure AD B2C에서 조건부 액세스 정책을 관리할 수도 있습니다. 자세한 내용은 [조건부 액세스 설명서](../active-directory/conditional-access/overview.md) 및 [Microsoft Graph 작업](microsoft-graph-operations.md#conditional-access)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [조건부 액세스를 사용자 흐름에 추가](conditional-access-user-flow.md)
- [Azure AD의 ID 보호에 대해 알아보기](../active-directory/identity-protection/overview-identity-protection.md)
- [조건부 액세스에 대해 알아보기](../active-directory/conditional-access/overview.md)