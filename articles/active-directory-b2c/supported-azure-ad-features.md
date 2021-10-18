---
title: 지원되는 Azure AD 기능
description: Azure AD B2C에서 계속 지원되는 Azure AD 기능에 대해 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 10/08/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: f43f6afb5e102dc33bc8b5b1e1a146a58f394f60
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129621485"
---
# <a name="supported-azure-ad-features"></a>지원되는 Azure AD 기능

Azure AD B2C 테넌트는 이미 있을 수 있지만 이를 사용하는 Azure Active Directory 테넌트와 다릅니다. Azure AD B2C 테넌트에서 사용할 수 있는 Azure AD 기능은 다음과 같습니다.

|기능  |Azure AD  | Azure AD B2C |
|---------|---------|---------|
| [그룹](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md) | 그룹은 관리 및 사용자 계정을 관리하는 데 사용할 수 있습니다.| 그룹은 관리 계정을 관리하는 데 사용할 수 있습니다. [소비자 계정](user-overview.md#consumer-user)은 어떤 그룹에도 속할 수 없습니다. |
| [외부 ID 게스트 초대](../active-directory//external-identities/add-users-administrator.md)| 게스트 사용자를 초대하고, Facebook 및 Google 계정을 사용하여 페더레이션 및 로그인과 같은 외부 ID 기능을 구성할 수 있습니다. | 애플리케이션에 액세스하거나 테넌트를 관리하기 위해 Microsoft 계정 또는 Azure AD 사용자만 게스트로 Azure AD 테넌트에 초대할 수 있습니다. [소비자 계정](user-overview.md#consumer-user)의 경우 Azure AD B2C 사용자 흐름과 사용자 지정 정책을 사용하여 사용자를 관리하고, Google 또는 Facebook과 같은 외부 ID 공급자에 가입하거나 로그인합니다. |
| [역할 및 관리자](../active-directory/fundamentals/active-directory-users-assign-role-azure-portal.md)| 관리 및 사용자 계정에 대해 완벽하게 지원됩니다. | 역할은 [소비자 계정](user-overview.md#consumer-user)에서 지원되지 않습니다. 소비자 계정에는 Azure 리소스에 대한 액세스 권한이 없습니다.|
| [사용자 지정 도메인 이름](../active-directory/fundamentals/add-custom-domain.md) |  Azure AD 사용자 지정 도메인은 관리 계정에만 사용할 수 있습니다. | [소비자 계정](user-overview.md#consumer-user)은 사용자 이름, 전화 번호 또는 모든 이메일 주소를 사용하여 로그인할 수 있습니다. [사용자 지정 도메인](custom-domain.md)은 리디렉션 URL에서 사용할 수 있습니다.|
| [조건부 액세스](../active-directory/conditional-access/overview.md) | 관리 및 사용자 계정에 대해 완벽하게 지원됩니다. | Azure AD 조건부 액세스 기능의 하위 집합은 [소비자 계정](user-overview.md#consumer-user)에서 지원됩니다. Azure AD B2C [조건부 액세스](conditional-access-user-flow.md)를 구성하는 방법을 알아봅니다.|
| [Premium P1](https://azure.microsoft.com/pricing/details/active-directory) | Azure AD Premium P1 기능에 대해 완벽하게 지원됩니다. 예: [암호 보호](../active-directory/authentication/concept-password-ban-bad.md), [하이브리드 ID](../active-directory/hybrid/whatis-hybrid-identity.md), [조건부 액세스](../active-directory/roles/permissions-reference.md#), [동적 그룹](../active-directory/enterprise-users/groups-create-rule.md) 등. | Azure AD 조건부 액세스 기능의 하위 집합은 [소비자 계정](user-overview.md#consumer-user)에서 지원됩니다. Azure AD B2C [조건부 액세스](conditional-access-user-flow.md)를 구성하는 방법을 알아봅니다.|
| [Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) | Azure AD Premium P2 기능에 대해 완벽하게 지원됩니다. 예: [ID 보호](../active-directory/identity-protection/overview-identity-protection.md) 및 [ID 거버넌스](../active-directory/governance/identity-governance-overview.md).  | Azure AD ID 보호 기능의 하위 집합은 [소비자 계정](user-overview.md#consumer-user)에서 지원됩니다. [ID 보호로 위험을 조사](identity-protection-investigate-risk.md)하고 Azure AD B2C [조건부 액세스](conditional-access-user-flow.md)를 구성하는 방법을 알아봅니다. |

> [!NOTE]
> **테넌트의 다른 Azure 리소스:** <br>Azure AD B2C 테넌트에서는 가상 머신, Azure 웹앱 또는 Azure 함수와 같은 다른 Azure 리소스를 프로비전할 수 없습니다. 이러한 리소스는 Azure AD 테넌트에서 만들어야 합니다.