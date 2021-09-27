---
title: Azure Lighthouse 아키텍처
description: Azure Lighthouse의 테넌트 간 관계와 고객의 테넌트에서 만들어 이러한 관계를 사용하도록 설정하는 리소스에 대해 알아봅니다.
ms.date: 09/13/2021
ms.topic: conceptual
ms.openlocfilehash: 92890199b11da69fac58ec4ff2bccc1ab347d076
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128629126"
---
# <a name="azure-lighthouse-architecture"></a>Azure Lighthouse 아키텍처

Azure Lighthouse를 사용하면 서비스 공급자가 고객 참여 및 온보딩 환경을 간소화하는 동시에 위임된 리소스를 민첩하고 정확하게 규모에 맞게 관리할 수 있습니다. 권한 있는 사용자, 그룹 및 서비스 주체는 고객의 Azure AD(Azure Active Directory) 테넌트에 계정이 없거나 해당 고객의 테넌트에 대한 공동 소유자가 아니더라도 고객 구독의 컨텍스트에서 직접 작업할 수 있습니다. 이 액세스를 지원하는 데 사용되는 메커니즘을 Azure 위임 리소스 관리라고 합니다.

:::image type="content" source="../media/delegation.jpg" alt-text="Azure 위임 리소스 관리를 보여 주는 다이어그램":::

> [!TIP]
> Azure Lighthouse는 테넌트 간 관리를 간소화하기 위해 [여러 개의 자체 Azure AD 테넌트가 있는 기업 내에서](enterprise.md) 사용할 수도 있습니다.

이 항목에서는 Azure Lighthouse의 테넌트 간 관계와 고객의 테넌트에서 만들어 이러한 관계를 사용하도록 설정하는 리소스에 대해 설명합니다.

## <a name="delegation-resources-created-in-the-customer-tenant"></a>고객 테넌트에서 만든 위임 리소스

고객의 구독 또는 리소스 그룹을 Azure Lighthouse에 온보딩하면 **등록 정의** 와 **등록 할당** 이라는 두 개의 리소스가 만들어집니다. [API 및 관리 도구](cross-tenant-management-experience.md#apis-and-management-tool-support)를 사용하여 이러한 리소스에 액세스하거나 [Azure Portal](../how-to/view-manage-customers.md)에서 사용할 수 있습니다.

### <a name="registration-definition"></a>등록 정의

등록 정의에는 Azure Lighthouse 제안의 세부 정보(관리 테넌트 ID 및 기본 역할을 관리 테넌트의 특정 사용자, 그룹 및/또는 서비스 주체에 할당하는 권한 부여)가 포함됩니다.

등록 정의는 위임된 각 구독의 구독 수준 또는 위임된 리소스 그룹이 포함된 각 구독에서 만들어집니다. API를 사용하여 등록 정의를 만드는 경우 구독 수준에서 작업해야 합니다. 예를 들어 Azure PowerShell을 사용하면 New-AzureRmResourceGroupDeployment를 사용하는 대신, 새 등록 정의([New-AzManagedServicesDefinition](/powershell/module/az.managedservices/new-azmanagedservicesdefinition))를 만들기 전에 New-AzureRmDeployment를 사용해야 합니다.

### <a name="registration-assignment"></a>등록 할당

등록 할당은 등록 정의를 특정 범위, 즉 온보딩된 구독 및/또는 리소스 그룹에 할당합니다.

등록 할당은 위임된 각 범위에서 만들어지므로 온보딩된 항목에 따라 구독 그룹 수준 또는 리소스 그룹 수준에 있게 됩니다.

각 등록 할당은 구독 수준에서 유효한 등록 정의를 참조해야 하며, 해당 서비스 공급자에 대한 권한 부여를 위임된 범위에 연결하여 액세스 권한을 부여해야 합니다.

## <a name="logical-projection"></a>논리적 프로젝션

Azure Lighthouse는 리소스에 대한 한 테넌트에서 다른 테넌트로의 논리적 프로젝션을 만듭니다. 이렇게 하면 권한 있는 서비스 공급자 사용자가 위임된 고객 구독 및 리소스 그룹에서 작업할 수 있는 권한 부여를 통해 해당 사용자 고유의 테넌트에 로그인할 수 있습니다. 그러면 서비스 공급자의 테넌트에 있는 사용자가 각 개별 고객 테넌트에 로그인하지 않고도 고객을 대신하여 관리 작업을 수행할 수 있습니다.

서비스 공급자 테넌트의 사용자, 그룹 또는 서비스 주체가 고객의 테넌트에 있는 리소스에 액세스할 때마다 Azure Resource Manager에서 요청을 받습니다. Resource Manager는 고객 자신의 테넌트 내에서 사용자의 요청에 대해 수행하는 것처럼 이러한 요청을 인증합니다. Azure Lighthouse의 경우 등록 정의와 등록 할당이라는 두 개의 리소스가 고객의 테넌트에 있는지 확인하여 이를 수행합니다. 그렇다면 Resource Manager는 해당 리소스에서 정의한 정보에 따라 액세스 권한을 부여합니다.

:::image type="content" source="../media/logical-projection.jpg" alt-text="Azure Lighthouse의 논리적 프로젝션을 보여 주는 다이어그램":::

서비스 공급자의 테넌트에 있는 사용자의 작업은 고객의 테넌트에 저장된 활동 로그에서 추적됩니다. 이를 통해 고객은 [수행된 변경 내용과 이를 변경한 사용자를 확인](../how-to/view-service-provider-activity.md)할 수 있습니다.

## <a name="how-azure-lighthouse-works"></a>Azure Lighthouse의 작동 방식

높은 수준에서 관리 테 넌 트에 대해 Azure Lighthouse가 작동 하는 방식은 다음과 같습니다.

1. 그룹, 서비스 주체 또는 사용자가 고객의 Azure 리소스를 관리하는 데 필요한 [역할](tenants-users-roles.md#role-support-for-azure-lighthouse)을 식별합니다.
2. [관리되는 서비스 제안을 Azure Marketplace에 게시](../how-to/publish-managed-services-offers.md)하거나 [Azure Resource Manager 템플릿을 배포](../how-to/onboard-customer.md)하여 이 액세스 권한을 지정하고 고객을 Azure Lighthouse에 온보딩합니다. 이 온보딩 프로세스는 위에서 설명한 두 개의 리소스(등록 정의 및 등록 할당)를 고객의 테넌트에 만듭니다.
3. 고객이 온보딩되면 권한 있는 사용자가 관리 테넌트에 로그인하고, 정의한 액세스 권한에 따라 지정된 고객 범위(구독 또는 리소스 그룹)에서 작업을 수행합니다. 고객은 수행된 모든 작업을 검토할 수 있으며, 언제든지 액세스 권한을 제거할 수 있습니다.

대부분의 경우 한 서비스 공급자만 고객의 특정 리소스를 관리하지만, 고객은 동일한 구독 또는 리소스 그룹에 대해 여러 위임을 만들어 여러 서비스 공급자가 액세스할 수 있도록 할 수 있습니다. 이 시나리오에서는 [서비스 공급자의 테넌트에서 여러 고객에게 리소스를 프로젝션](isv-scenarios.md#saas-based-multi-tenant-offerings)하는 ISV 시나리오도 지원합니다.

## <a name="next-steps"></a>다음 단계

- 등록 정의 및 등록 할당 작업에 대한 [Azure CLI](/cli/azure/managedservices) 및 [Azure Powershell](/powershell/module/az.managedservices) 명령을 검토합니다.
- Azure Lighthouse에 대한 [향상된 서비스 및 시나리오에](cross-tenant-management-experience.md#enhanced-services-and-scenarios)에 대해 알아봅니다.
- [테넌트, 사용자 및 역할](tenants-users-roles.md)이 Azure Lighthouse에서 작동하는 방법에 대해 자세히 알아봅니다.
