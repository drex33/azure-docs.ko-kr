---
title: Azure Marketplace의 관리되는 서비스 제안
description: Azure Marketplace에서 관리 서비스 제품을 통해 고객에게 Azure Lighthouse 관리 서비스를 제공합니다.
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 5d96a23f1dbdba74eefbf4f483a441c25e2dd47b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124732925"
---
# <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace의 관리되는 서비스 제안

이 문서에서는 [Azure Marketplace](https://azuremarketplace.microsoft.com)의 **관리형 서비스** 제품 유형에 대해 설명합니다. 관리형 서비스 제품으로 [Azure Lighthouse](../overview.md)를 통해 고객에게 리소스 관리 서비스를 제공할 수 있습니다. 해당 제품은 모든 잠재 고객이나 하나 이상의 특정 고객만 사용할 수 있습니다. 이러한 관리형 서비스와 관련된 비용은 고객에게 직접 청구하므로 Microsoft에서 부과하는 요금은 없습니다.

## <a name="understand-managed-service-offers"></a>관리형 서비스 제품 이해

관리형 서비스 제품으로 Azure Lighthouse에 고객을 온보딩하는 프로세스를 간소화할 수 있습니다. 고객이 Azure Marketplace에서 제품을 구매하는 경우 등록해야 하는 구독 및/또는 리소스 그룹을 지정할 수 있습니다.

각 제품에 대해 조직의 사용자가 고객 테 넌 트의 리소스에 대해 작업 해야 하는 액세스 권한을 정의 합니다. 이것은 고객 리소스에 액세스할 수 있는Azure Active Directory(Azure AD) 사용자, 그룹 및 서비스 주체를 지정하는 매니페스트와 액세스 수준을 정의하는 [역할](tenants-users-roles.md)을 통해 수행됩니다.

> [!NOTE]
> 관리형 서비스 제품은 Azure Government 및 기타 국가 클라우드에서 제공되지 않을 수 있습니다.

## <a name="public-and-private-offers"></a>퍼블릭 및 프라이빗 제품

각 관리형 서비스 제품에는 하나 이상의 플랜이 포함됩니다. 이 플랜은 프라이빗 또는 퍼블릭일 수 있습니다.

특정 고객으로 제품을 제한하려는 경우 프라이빗 플랜을 게시할 수 있습니다. 이렇게 하면 사용자가 제공하는 특정 구독 ID에 대해서만 플랜을 구매할 수 있습니다. 자세한 내용은 [프라이빗 제품](../../marketplace/private-offers.md)을 참조하세요.

> [!NOTE]
> CSP(클라우드 솔루션 공급자) 프로그램의 재판매인을 통해 설정된 구독은 프라이빗 제품을 지원하지 않습니다.

퍼블릭 플랜을 사용하면 서비스를 새 고객에게 프로모션할 수 있습니다. 이러한 정보는 일반적으로 고객 테넌트에 대한 액세스를 제한해야 할 때 좀 더 적합합니다. 고객과의 관계를 설정하고 난 후, 조직에 추가 액세스 권한을 부여하려는 경우 해당 고객을 위해 새 프라이빗 플랜을 게시하거나 [Azure Resource Manager 템플릿을 사용하여 추가 액세스를 위해 온보딩](../how-to/onboard-customer.md)할 수 있습니다.

적절한 경우 퍼블릭 및 프라이빗 플랜을 모두 동일한 제품에 포함할 수 있습니다.

> [!IMPORTANT]
> 플랜이 공개로 게시된 후에는 다시 비공개로 변경할 수 없습니다. 제품을 수락하고 리소스를 위임할 수 있는 고객을 제어하려면 비공개 플랜을 사용합니다. 공개 플랜에서는 특정 고객이나 특정 수의 고객으로 가용성을 제한할 수 없습니다(원하면 플랜 판매를 완전히 중단할 수는 있음). 제품을 게시할 때 **역할 정의** 가 [관리형 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)로 설정된 **권한 부여** 를 포함한 경우에만 고객이 제품을 수락한 후 [위임에 대한 액세스 권한을 제거](../how-to/remove-delegation.md)할 수 있습니다. 고객에게 연락하여 [액세스 권한을 제거](../how-to/view-manage-service-providers.md#remove-service-provider-offers)하도록 요청할 수도 있습니다.

## <a name="publish-managed-service-offers"></a>관리형 서비스 제품 게시

관리형 서비스 제품을 게시하는 방법을 알아보려면 [Azure Marketplace에 관리형 서비스 제품 게시](../how-to/publish-managed-services-offers.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- Azure Lighthouse [아키텍처](architecture.md) 및 [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Marketplace에 [관리형 서비스 제품 게시](../how-to/publish-managed-services-offers.md)
