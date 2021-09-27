---
title: ISV 시나리오의 Azure Lighthouse
description: ISV는 Azure Lighthouse의 기능을 사용하여 고객 제품의 유연성을 높일 수 있습니다.
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 9266b8d026446600cf25d2f141785c3a461ad0e8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768689"
---
# <a name="azure-lighthouse-in-isv-scenarios"></a>ISV 시나리오의 Azure Lighthouse

[Azure Lighthouse](../overview.md) 일반적인 시나리오는 서비스 공급자가 고객의 Azure AD(Azure Active Directory) 테넌트에서 리소스를 관리하는 경우입니다. Azure Lighthouse의 기능을 고객과 함께 SaaS 기반 제품을 사용하는 ISV(독립 소프트웨어 공급업체)에서도 사용할 수 있습니다. Azure Lighthouse는 구독 범위에 액세스해야 하는 관리되는 서비스 또는 지원을 제공하는 ISV에 특히 유용할 수 있습니다.

## <a name="managed-service-offers-in-azure-marketplace"></a>Azure Marketplace의 관리되는 서비스 제안

ISV는 이미 Azure Marketplace에 솔루션을 게시했을 수 있습니다. 관리되는 서비스 제품을 게시하여 관리 서비스를 고객에게 제공할 수 있습니다. 이러한 제품은 온보딩 프로세스를 간소화하고 필요한 만큼 많은 고객에게 서비스를 확장할 수 있도록 합니다. Azure Lighthouse는 고객에게 가치를 제공하는 데 사용할 수 있는 광범위한 [관리 작업 및 시나리오](cross-tenant-management-experience.md#enhanced-services-and-scenarios)를 지원합니다.

자세한 내용은 [Azure Marketplace에 관리되는 서비스 제품 게시](../how-to/publish-managed-services-offers.md)를 참조하세요.

## <a name="using-azure-lighthouse-with-azure-managed-applications"></a>Azure Managed Applications와 함께 Azure Lighthouse 사용

[Azure Managed Applications](../../azure-resource-manager/managed-applications/overview.md)는 ISV가 고객에게 서비스를 제공할 수 있는 또 다른 방법입니다. Azure Managed Applications와 함께 Azure Lighthouse를 사용하여 향상된 시나리오를 사용하도록 설정할 수 있습니다.

자세한 내용은 [Azure Lighthouse 및 Azure Managed Applications](managed-applications.md)를 참조하세요.

## <a name="saas-based-multi-tenant-offerings"></a>SaaS 기반 다중 테넌트 제공

추가 시나리오에서는 ISV가 자체 테넌트에서 구독의 리소스를 호스트한 다음, Azure Lighthouse 사용하여 고객이 이러한 리소스에 액세스할 수 있도록 합니다. 그러면 고객은 자신의 테넌트에 로그인하여 필요에 따라 이러한 리소스에 액세스할 수 있습니다. ISV는 자체 테넌트에서 IP를 유지 관리하며, 자체 지원 계획을 사용하여 고객의 계획이 아닌 테넌트에서 호스트되는 솔루션과 관련된 티켓을 발생할 수 있습니다. 리소스가 ISV의 테넌트에 있으므로 VM에 로그인, 앱 설치, 유지 관리 작업 수행과 같은 모든 작업을 ISV에서 직접 수행할 수 있습니다.

이 시나리오에서 고객 테넌트의 사용자는 고객이 ISV의 리소스를 관리하지 않더라도 기본적으로 "테넌트 관리"로 액세스 권한이 부여됩니다. ISV의 테넌트에 직접 액세스하기 때문에 고객이 실수로 솔루션이나 기타 ISV 리소스를 변경할 수 없도록 필요한 최소한의 권한만 부여하는 것이 중요합니다.

이 아키텍처를 사용하도록 설정하려면 ISV가 고객의 Azure AD 테넌트에서 사용자 그룹에 대한 개체 ID와 테넌트 ID를 얻어야 합니다. 그런 다음, ISV는 이 사용자 그룹에 적절한 권한을 부여하는 ARM 템플릿을 빌드하고 고객이 액세스할 리소스가 포함된 [ISV 구독에 배포](../how-to/onboard-customer.md)합니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure Lighthouse 아키텍처](architecture.md)에 대해 자세히 알아봅니다.
