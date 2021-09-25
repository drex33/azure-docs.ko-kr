---
title: 클라우드 솔루션 공급자 프로그램 고려 사항
description: CSP 파트너의 경우 Azure 위임 리소스 관리를 통해 세분화된 권한을 사용하여 보안 및 제어 기능을 향상시킵니다.
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 6a53ec7cfc2e9f82664e57eecf5bf3f587444134
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629107"
---
# <a name="azure-lighthouse-and-the-cloud-solution-provider-program"></a>Azure Lighthouse 및 클라우드 솔루션 공급자 프로그램

[CSP(클라우드 솔루션 공급 기업)](/partner-center/csp-overview) 파트너인 경우 [AOBO(관리 위임자)](https://channel9.msdn.com/Series/cspdev/Module-11-Admin-On-Behalf-Of-AOBO) 기능을 사용하여 CSP 프로그램을 통해 고객용으로 생성된 Azure 구독에 액세스할 수 있습니다. 이 액세스를 통해 고객의 구독을 직접 지원, 구성 및 관리할 수 있습니다.

[Azure Lighthouse](../overview.md)를 사용하면 AOBO와 함께 Azure 위임 리소스 관리를 사용할 수 있습니다. 이렇게 하면 사용자에게 더 세분화된 권한을 부여하여 보안을 개선하고 불필요한 액세스를 줄일 수 있습니다. 또한 사용자가 테넌트에서 단일 로그인을 사용하여 여러 고객 구독에서 작업할 수 있으므로 효율성 및 확장성이 향상됩니다.

> [!TIP]
> 고객 리소스를 보호하려면 [파트너 보안 요구 사항 보안 요구 사항](/partner-center/partner-security-requirements)과 함께 [권장 보안 사례](recommended-security-practices.md)를 검토하고 준수해야 합니다.

## <a name="administer-on-behalf-of-aobo"></a>AOBO(관리 위임자)

AOBO를 사용하는 경우 테넌트에서 [관리 에이전트](/partner-center/permissions-overview#manage-commercial-transactions-in-partner-center-azure-ad-and-csp-roles) 역할이 있는 사용자는 CSP 프로그램을 통해 만든 Azure 구독에 대해 AOBO 액세스 권한이 있습니다. 고객의 구독에 액세스해야 하는 모든 사용자는 이 그룹의 멤버여야 합니다. AOBO는 다른 고객에게 작용하는 고유한 그룹을 만들거나 그룹이나 사용자에 대해 다른 역할을 사용하도록 설정하는 유연성을 허용하지 않습니다.

![AOBO를 사용한 테넌트 관리를 보여 주는 다이어그램](../media/csp-1.jpg)

## <a name="azure-lighthouse"></a>Azure Lighthouse

Azure Lighthouse를 사용하면 다음 다이어그램이 제시하는 대로 다양한 그룹을 다양한 고객 또는 역할에 할당할 수 있습니다. 사용자는 [Azure 위임 리소스 관리](architecture.md)를 통해 적절한 수준의 액세스 권한을 얻게 되므로 관리 에이전트 역할(즉, 전체 AOBO 액세스 권한)이 있는 사용자의 수를 줄일 수 있습니다. 이렇게 하면 고객 리소스에 대한 불필요한 액세스를 제한하여 보안을 향상할 수 있습니다. 또한 사용자에게 필요한 것보다 더 많은 액세스 권한을 부여하지 않고 각 사용자의 [업무에](tenants-users-roles.md#role-support-for-azure-lighthouse) 가장 적합한 Azure 기본 제공 역할을 사용하여 대규모로 여러 고객을 보다 유연하게 관리할 수 있습니다.

![AOBO 및 Azure Lighthouse를 사용한 테넌트 관리를 보여주는 다이어그램.](../media/csp-2.jpg)

CSP 프로그램을 통해 만든 구독을 온보딩할 때는 [Azure Lighthouse에 구독 온보딩](../how-to/onboard-customer.md)에 설명된 단계를 수행하게 됩니다. 테넌트에서 관리 에이전트 역할이 있는 사용자는 이 온보딩을 수행할 수 있습니다.

> [!TIP]
> CSP(클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정된 구독은 프라이빗 요금제를 사용하는 [관리형 서비스 제공](managed-services-offers.md)을 지원하지 않습니다. [Azure Resource Manager 템플릿을 사용](../how-to/onboard-customer.md)하여 Azure Lighthouse에 이러한 구독을 온보딩할 수 있습니다.

> [!NOTE]
> 이제 Azure Portal의 [**내 고객** 페이지에는](../how-to/view-manage-customers.md) [MCA(Microsoft 고객 계약)에 서명](/partner-center/confirm-customer-agreement)했으며 [Azure 플랜](/partner-center/azure-plan-get-started)에 속하는 CSP 고객의 청구 정보 및 리소스를 표시하는 **클라우드 솔루션 공급자(미리 보기)** 섹션이 포함되어 있습니다. 자세한 내용은 [Microsoft 파트너 계약 청구 계정 시작](../../cost-management-billing/understand/mpa-overview.md)을 참조하세요.
>
> CSP 고객은 Azure Lighthouse에 대한 온보딩 여부에 관계없이 해당 섹션에 표시될 수 있습니다. 온보딩된 경우 [고객과 위임된 리소스 보기 및 관리](../how-to/view-manage-customers.md)에 설명된 대로 **고객** 섹션에도 표시됩니다. 마찬가지로 CSP 고객은 Azure Lighthouse 온보딩을 위해 **내 고객** 의 **클라우드 솔루션 공급자(미리 보기)** 섹션에 표시될 필요는 없습니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure Lighthouse에 구독을 온보딩](../how-to/onboard-customer.md)하는 방법에 대해 알아봅니다.
- [클라우드 솔루션 공급자 프로그램](/partner-center/csp-overview)에 대해 알아봅니다.
