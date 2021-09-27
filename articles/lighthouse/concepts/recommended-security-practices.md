---
title: 권장 보안 방법
description: Azure Lighthouse를 사용하는 경우 보안 및 액세스 제어를 고려하는 것이 중요합니다.
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 52bc5301633f8e9d92aeaeee22ecf34b6458fccb
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782369"
---
# <a name="recommended-security-practices"></a>권장 보안 방법

[Azure Lighthouse](../overview.md)를 사용하는 경우 보안 및 액세스 제어를 고려하는 것이 중요합니다. 테넌트의 사용자가 고객 구독 및 리소스 그룹에 직접 액세스할 수 있으므로 테넌트의 보안을 유지하는 단계를 수행하는 것이 좋습니다. 또한 고객의 리소스를 효과적으로 관리하는 데 필요한 액세스만 허용할 수도 있습니다. 이 항목에서는 이 작업을 수행하는 데 도움이 되는 권장 사항을 제공합니다.

> [!TIP]
> 해당 권장 사항은 Azure Lighthouse를 통해 [여러 테넌트를 관리하는 기업](enterprise.md)에도 적용됩니다.

## <a name="require-azure-ad-multi-factor-authentication"></a>Azure AD Multi-Factor Authentication 필요

[Azure Multi-Factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)(2단계 인증이라고도 함)에서는 여러 인증 단계를 요구하여 공격자가 계정에 대한 액세스 권한을 얻지 못하게 합니다. 위임된 고객 리소스에 대한 액세스 권한이 있는 사용자를 포함하여 관리 테넌트의 모든 사용자에 대해 Azure AD Multi-Factor Authentication을 요구해야 합니다.

또한 고객에게 테넌트에서도 Azure Multi-Factor Authentication을 구현하도록 요청하는 것이 좋습니다.

## <a name="assign-permissions-to-groups-using-the-principle-of-least-privilege"></a>최소 권한 원칙을 사용하여 그룹에 사용 권한 할당

더 간편하게 관리하기 위해서 고객 리소스 관리에 필요한 각 역할에 대해 Azure Active Directory(Azure AD) 그룹을 사용합니다. 이렇게 하면 해당 사용자에게 직접 사용 권한을 할당하는 대신, 필요에 따라 그룹에서 개별 사용자를 추가하거나 제거할 수 있습니다.

> [!IMPORTANT]
> Azure AD 그룹에 대한 사용 권한을 추가하려면 **그룹 유형** 이 **보안** 으로 설정되어 있어야 합니다. 이 옵션은 그룹을 만들 때 선택됩니다. 자세한 내용은 [Azure Active Directory를 사용하여 기본 그룹 만들기 및 멤버 추가](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md)를 참조하세요.

권한 구조를 만들 때 사용자가 작업을 완료하는 데 필요한 권한만 갖도록 하여 실수로 인한 오류 발생 가능성을 줄일 수 있게 최소 권한 원칙을 따라야 합니다.

예를 들어 다음과 같은 구조를 사용하려고 할 수 있습니다.

|그룹 이름  |유형  |principalId  |역할 정의  |역할 정의 ID  |
|---------|---------|---------|---------|---------|
|설계자     |사용자 그룹         |\<principalId\>         |Contributor         |b24988ac-6180-42a0-ab88-20f7382dd24c  |
|평가     |사용자 그룹         |\<principalId\>         |Reader         |acdd72a7-3385-48ef-bd42-f606fba81ae7  |
|VM 전문가     |사용자 그룹         |\<principalId\>         |VM 참가자         |9980e02c-c2be-4d73-94e8-173b1dc7cf3c  |
|Automation     |SPN(서비스 주체 이름)         |\<principalId\>         |참가자         |b24988ac-6180-42a0-ab88-20f7382dd24c  |

관련 그룹을 만든 후 필요에 따라 사용자를 할당할 수 있습니다. 실제로 액세스 권한이 필요한 사용자만 추가합니다. 그룹 멤버 자격을 정기적으로 검토하고 더 이상 적절하지 않거나 포함할 필요가 없는 모든 사용자를 제거해야 합니다.

[퍼블릭 관리형 서비스 제품을 통해 고객을 온보딩](../how-to/publish-managed-services-offers.md)할 경우 포함하는 모든 그룹(또는 사용자 또는 서비스 주체)은 해당 플랜을 구입하는 모든 고객에 대해 동일한 사용 권한을 갖습니다. 각 고객에 작동할 다른 그룹을 할당하려면 각 고객에게 단독으로 사용되는 별도의 프라이빗 플랜을 게시하거나 Azure Resource Manager 템플릿을 사용하여 고객을 개별적으로 온보딩해야 합니다. 예를 들어, 액세스 권한이 매우 제한된 퍼블릭 플랜을 게시한 다음, 고객이 사용자 지정된 Azure Resource Manager 템플릿을 사용하여 추가 액세스를 위해 해당 리소스를 직접 온보딩하도록 하여 필요에 따라 추가 액세스 권한을 부여할 수 있습니다.

> [!TIP]
> 관리 테넌트에서 사용자가 자신의 역할을 일시적으로 승격할 수 있는 *적격 권한 부여* 를 만들 수도 있습니다. 적격 권한 부여를 사용하면 권한 있는 역할에 대한 사용자의 영구 할당 수를 최소화하여 테넌트에서 사용자의 권한 있는 액세스와 관련된 보안 위험을 줄일 수 있습니다. 이 기능은 현재 퍼블릭 미리 보기로 제공되며 특정 라이선스 요구 사항이 있습니다. 자세한 내용은 [적격 권한 부여 만들기](../how-to/create-eligible-authorizations.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure 보안 벤치 마크의 지침이 Azure Lighthouse에 적용되는 방식을 이해하기 위해 [보안 기준 정보](../security-baseline.md)를 검토합니다.
- [Azure AD Multi-Factor Authentication 배포](../../active-directory/authentication/howto-mfa-getstarted.md)
- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
