---
title: 엔터프라이즈 시나리오의 Azure Lighthouse
description: Azure Lighthouse의 기능은 여러 Azure AD 테넌트를 사용하는 엔터프라이즈 내에서 테넌트 간 관리를 간소화하는 데 사용할 수 있습니다.
ms.date: 10/21/2021
ms.topic: conceptual
ms.openlocfilehash: 7d0accd765049c671d8d61a76d7c6449260b0140
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130249650"
---
# <a name="azure-lighthouse-in-enterprise-scenarios"></a>엔터프라이즈 시나리오의 Azure Lighthouse

[Azure Lighthouse](../overview.md)에 대한 일반적인 시나리오는 서비스 공급자가 고객에게 속한 Azure Active Directory(Azure AD) 테넌트 리소스를 관리하는 경우입니다. 그러나 Azure Lighthouse의 기능은 여러 Azure AD 테넌트를 사용하는 엔터프라이즈 내에서 테넌트 간 관리를 간소화하는 데 사용할 수도 있습니다.

## <a name="single-vs-multiple-tenants"></a>단일 및 다중 테넌트 비교

대부분의 조직에서는 단일 Azure AD 테넌트를 사용하여 더 쉽게 관리할 수 있습니다. 모든 리소스가 한 테넌트 내에 있으면 해당 테넌트 내의 지정된 사용자, 사용자 그룹 또는 서비스 주체가 관리 작업을 중앙 집중식으로 수행할 수 있습니다. 가능한 경우에는 언제든지 하나의 테넌트만 조직에 사용하는 것이 좋습니다.

일부 조직에서는 여러 Azure AD 테넌트를 사용해야 할 수도 있습니다. 이는 구입이 진행된 후 장기 테넌트 통합 전략이 아직 정의되지 않은 경우와 같이 일시적인 상황일 수 있습니다. 그 외의 경우, 조직에서 완전히 독립적인 자회사, 지리적 또는 법적 요구 사항 또는 기타 고려 사항으로 인해 지속적으로 여러 테넌트를 유지해야 할 수도 있습니다.

다중 테넌트 아키텍처가 필요한 경우, Azure Lighthouse를 사용하여 관리 작업을 중앙 집중화하고 간소화할 수 있습니다. Azure Lighthouse를 사용 하 여 하나의 테 넌 트에 있는 사용자는 중앙 집중화 되 고 확장 가능한 방식으로 [교차 테 넌 트 관리 기능](cross-tenant-management-experience.md) 을 수행할 수 있습니다.

## <a name="tenant-management-architecture"></a>테넌트 관리 아키텍처

엔터프라이즈에서 Azure Lighthouse를 사용하려면 다른 테넌트에서 관리 작업을 수행하는 사용자를 포함하는 테넌트를 결정해야 합니다. 즉, 하나의 테넌트를 다른 테넌트에 대한 관리 테넌트로 지정해야 합니다.

예를 들어 조직에 *Tenant A* 라는 단일 테넌트가 있다고 가정해 봅시다. 그다음 조직에서 *Tenant B* 와 *Tenant C* 를 구입한다면, 이를 별도 테넌트로 유지 관리해야 하는 비즈니스 이유가 생기게 됩니다. 그러나 동일한 사용자 집합에서 수행되는 관리 작업을 사용하여 모든 작업에 대해 동일한 정책 정의, 백업 사례 및 보안 프로세스를 사용하는 것이 좋습니다.

Tenant A에 해당 작업을 수행하는 조직 사용자가 이미 포함되어 있기 때문에 Tenant B 및 Tenant C 내에서 구독을 등록할 수 있습니다. 그러면 Tenant A의 동일한 사용자가 모든 테넌트 전반에서 해당 작업을 수행할 수 있습니다.

![Tenant B와 Tenant C의 리소스를 관리하는 Tenant A의 사용자를 보여 주는 다이어그램](../media/enterprise-azure-lighthouse.jpg)

## <a name="security-and-access-considerations"></a>보안 및 액세스 고려 사항

대부분의 엔터프라이즈 시나리오에서는 Azure Lighthouse에 전체 구독을 위임하는 것이 바람직합니다. 구독 내에서 특정 리소스 그룹만 위임하도록 선택할 수도 있습니다.

어떤 방식이든 [리소스에 액세스할 수 있는 사용자를 정의할 때는 최소 권한의 원칙을 따라야 합니다](recommended-security-practices.md#assign-permissions-to-groups-using-the-principle-of-least-privilege). 이렇게 하면 필요한 작업을 수행하는 데 필요한 권한만 사용자에게 부여하고 실수로 인한 오류가 발생할 가능성을 줄일 수 있습니다.

Azure Lighthouse는 물리적으로 데이터 또는 리소스를 이동하는 것이 아니라 관리 테넌트와 관리형 테넌트 간의 논리적 연결만 제공합니다. 또한 액세스는 항상 관리 테넌트에서 관리형 테넌트로의 한 방향으로만 진행됩니다. 관리 테넌트의 사용자와 그룹은 관리형 테넌트 리소스에서 관리 작업을 수행할 때 다단계 인증을 계속 사용해야 합니다.

내부 또는 외부 거버넌스 및 규정 준수 가드 레일을 사용하는 엔터프라이즈는 투명성 요구 사항을 충족하기 위해 [Azure 활동 로그](../../azure-monitor/essentials/platform-logs-overview.md)를 사용할 수 있습니다. 엔터프라이즈 테넌트에서 관리 및 관리형 테넌트 관계를 설정한 경우, 각 테넌트의 사용자는 기록된 활동을 확인하여 다른 테넌트의 사용자가 수행한 작업을 모니터링하고 볼 수 있습니다.

## <a name="onboarding-considerations"></a>온보딩 고려 사항

구독(또는 구독 내의 리소스 그룹)은 Azure Resource Manager 템플릿을 배포하거나 Azure Marketplace에 게시된 관리형 서비스 제안을 통해 Azure Lighthouse에 온보딩할 수 있습니다.

엔터프라이즈 사용자는 일반적으로 엔터프라이즈 테넌트에 직접 액세스할 수 있으며 관리 제안을 마케팅하거나 홍보할 필요가 없으므로, 일반적으로 Azure Resource Manager 템플릿을 배포하는 것이 더 빠르고 간단합니다. [온보딩 지침](../how-to/onboard-customer.md)은 서비스 공급자 및 고객을 의미하는 반면, 기업에서는 동일한 프로세스를 사용하여 테넌트를 온보딩할 수 있습니다.

원하는 경우 [Azure Marketplace에 관리 서비스 제안을 게시](../how-to/publish-managed-services-offers.md)하여 엔터프라이즈 내의 테넌트를 온보딩할 수 있습니다. 제안을 적절한 테넌트에서만 사용할 수 있도록 하려면 계획이 비공개로 표시되어야 합니다. 프라이빗 플랜을 사용하면 온보딩하려는 각 테넌트에 구독 ID를 제공할 수 있으며, 다른 사용자는 해당 제안을 받을 수 없습니다.

## <a name="azure-ad-b2c"></a>Azure AD B2C

[Azure AD B2C (Azure Active Directory B2C)](../../active-directory-b2c/overview.md) 는 기업 간 id를 서비스로 제공 합니다. Azure Lighthouse를 통해 리소스 그룹을 위임 하는 경우 Azure Monitor를 사용 하 여 Azure AD B2C (Azure Active Directory B2C) 로그인 및 감사 로그를 다른 모니터링 솔루션으로 라우팅할 수 있습니다. 장기 사용을 위해 로그를 유지 하거나 타사 SIEM (보안 정보 및 이벤트 관리) 도구와 통합 하 여 사용자 환경에 대 한 통찰력을 얻을 수 있습니다.

자세한 내용은 [Azure Monitor를 사용 하 여 Azure AD B2C 모니터링](../../active-directory-b2c/azure-monitor.md)을 참조 하세요.

## <a name="terminology-notes"></a>용어 정보

엔터프라이즈 내 테넌트 간 관리의 경우 Azure Lighthouse 설명서의 서비스 공급자에 대한 참조는 엔터프라이즈 내 관리 테넌트, 즉 Azure Lighthouse를 통해 다른 테넌트의 리소스를 관리할 사용자가 포함된 테넌트에 적용되는 것으로 이해할 수 있습니다. 마찬가지로 고객에 대한 참조는 관리 테넌트의 사용자를 통해 관리할 리소스를 위임하는 테넌트에 적용되는 것으로 이해할 수 있습니다.

예를 들어 위에서 설명한 예에서 테넌트 A는 서비스 공급자 테넌트(관리 테넌트)로 간주하고, 테넌트 B와 테넌트 C는 고객 테넌트로 간주할 수 있습니다.

이 예에서 적절한 권한이 있는 테넌트 A 사용자는 Azure Portal의 **내 고객** 페이지에서 [위임된 리소스를 보고 관리](../how-to/view-manage-customers.md)할 수 있습니다. 마찬가지로 적절한 권한이 있는 테넌트 B 및 테넌트 C 사용자는 Azure Portal의 **서비스 공급자** 페이지에서 테넌트 A에 [위임된 리소스를 보고 관리](../how-to/view-manage-service-providers.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](cross-tenant-management-experience.md)에 대해 알아봅니다.
- [Azure Lighthouse 작동 방법](architecture.md)에 대해 자세히 알아봅니다.
