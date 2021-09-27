---
title: Azure Lighthouse 및 Azure 관리형 애플리케이션
description: Azure Lighthouse 및 Azure 관리형 애플리케이션을 함께 사용할 수 있는 방법에 대해 알아봅니다.
ms.date: 09/08/2021
ms.topic: conceptual
ms.openlocfilehash: 629f9f1f6b6db69816d26cc7eed1e305dd55923b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124768575"
---
# <a name="azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 및 Azure 관리형 애플리케이션

Azure 관리형 애플리케이션 및 Azure Lighthouse는 서비스 공급자가 고객의 테넌트에 있는 리소스에 액세스할 수 있도록 하여 작동합니다. 이 둘을 사용하도록 설정하는 데 도움이 되는 작동 방식 및 시나리오에서의 차이점, 그리고 둘 다를 동시에 사용하는 방법에 대해 이해하는 것이 도움이 될 수 있습니다.

> [!TIP]
> 이 항목에서는 서비스 공급자 및 고객만 언급하지만, [여러 테넌트를 관리하는 기업](enterprise.md)에서도 동일한 프로세스와 도구를 사용할 수 있습니다.

## <a name="comparing-azure-lighthouse-and-azure-managed-applications"></a>Azure Lighthouse 및 Azure 관리형 애플리케이션 비교

다음 표에서는 Azure Lighthouse 또는 Azure 관리형 애플리케이션을 사용하도록 선택할 수 있는지 여부에 영향을 줄 수 있는 몇 가지 개략적인 차이점을 보여 줍니다. 아래에서 설명하고 있듯이, 이 둘을 함께 사용하는 솔루션을 디자인할 수도 있습니다.

|고려 사항  |Azure Lighthouse  |Azure 관리되는 애플리케이션  |
|---------|---------|---------|
|일반적인 사용자     |여러 테넌트를 관리하는 서비스 공급자 또는 기업         |개별 소프트웨어 공급업체(ISV)         |
|테넌트 간 액세스의 범위     |구독(들) 또는 리소스 그룹(들)         |리소스 그룹(단일 애플리케이션으로 범위 지정됨)         |
|Azure Marketplace에서 구입 가능     |아니요(제품은 Azure Marketplace에 게시할 수 있지만, 고객은 별도로 청구서를 받게 됨)        |예         |
|IP 보호     |예(IP는 서비스 공급자의 테넌트에 남아 있을 수 있음)        |예(설계상 리소스 그룹은 고객에게 잠겨 있음)         |
|거부 할당     |예         |예        |

### <a name="azure-lighthouse"></a>Azure Lighthouse

[Azure Lighthouse](../overview.md)를 사용하여 서비스 공급자는 고객의 구독(또는 리소스 그룹)에서 직접 폭넓은 관리 작업을 수행할 수 있습니다. 이 액세스는 논리 프로젝션을 통해 수행되어, 서비스 공급자가 자신의 테넌트에 로그인하여 고객의 테넌트에 속한 리소스에 액세스할 수 있습니다. 고객은 서비스 공급자에 게 위임할 구독 또는 리소스 그룹을 결정할 수 있으며, 고객은 해당 리소스에 대한 모든 액세스 권한을 보유합니다. 또한 언제든지 서비스 공급자의 액세스 권한을 제거할 수 있습니다.

Azure Lighthouse를 사용하기 위해 고객은 [ARM 템플릿을 배포](../how-to/onboard-customer.md)하거나 [Azure Marketplace의 관리형 서비스 제품](managed-services-offers.md)을 통해 온보딩됩니다. [파트너 ID를 링크](../how-to/partner-earned-credit.md)하여 고객 참여에 미치는 영향을 추적할 수 있습니다.

Azure Lighthouse는 서비스 공급자가 지속적으로 고객에 대한 관리 작업을 수행하는 경우에 일반적으로 사용됩니다. 기술 수준에서 Azure Lighthouse가 작동하는 방식에 대한 자세한 내용은 [Azure Lighthouse 아키텍처](architecture.md)를 참조하세요.

### <a name="azure-managed-applications"></a>Azure 관리되는 애플리케이션

[Azure 관리형 애플리케이션](../../azure-resource-manager/managed-applications/overview.md)을 통해 서비스 공급자 또는 ISV는 고객이 자신의 구독에서 쉽게 배포하고 사용할 수 있는 클라우드 솔루션을 제공할 수 있습니다.

관리형 애플리케이션에서 리소스는 앱 게시자가 관리하는 리소스 그룹에 배포됩니다. 리소스 그룹은 고객의 구독에 있지만, 게시자 테넌트의 ID는 해당 리소스 그룹에 액세스할 수 있습니다. ISV는 관리형 애플리케이션을 계속 관리 및 유지 관리하는 반면, 고객은 해당 리소스 그룹에서의 작업에 대한 직접 액세스 권한이 없거나 해당 리소스에 대한 액세스 권한이 전혀 없습니다.

관리형 애플리케이션은 [사용자 지정 Azure Portal 환경](../../azure-resource-manager/managed-applications/concepts-view-definition.md) 및 [사용자 지정 공급자와의 통합](../../azure-resource-manager/managed-applications/tutorial-create-managed-app-with-custom-provider.md)을 지원합니다. 이러한 옵션을 사용하면 더 많은 사용자 지정 및 통합된 환경을 제공하여, 고객이 일부 관리 작업을 직접 쉽게 수행할 수 있습니다.

관리형 애플리케이션은 특정 고객 사용을 위한 개인 제품 또는 여러 고객이 구매할 수 있는 공개 제품으로 [Azure Marketplace에 게시](../../marketplace/azure-app-offer-setup.md)할 수 있습니다. [관리형 애플리케이션을 서비스 카탈로그에 게시](../../azure-resource-manager/managed-applications/publish-service-catalog-app.md)하여, 이것들을 조직 내 사용자에게 제공할 수도 있습니다. ARM 템플릿을 사용하여 서비스 카탈로그와 Marketplace 인스턴스를 모두 배포할 수 있습니다. 여기에는 상업용 마켓플레이스 파트너의 고유한 식별자를 포함하여 [고객 사용량 특성](../../marketplace/azure-partner-customer-usage-attribution.md)을 추적할 수 있습니다.

Azure 관리형 애플리케이션은 일반적으로 서비스 공급자에 의해 완전히 관리되는 턴키 솔루션을 통해 달성할 수 있는 특정 고객 요구에 사용됩니다.

## <a name="using-azure-lighthouse-and-azure-managed-applications-together"></a>Azure Lighthouse 및 Azure 관리형 애플리케이션 동시 사용

Azure Lighthouse 및 Azure 관리형 애플리케이션은 여러 액세스 메커니즘을 사용하여 서로 다른 목표를 달성하는 반면, 서비스 공급자가 동일한 고객에게 둘 다를 사용하는 것이 적합 한 시나리오가 있을 수 있습니다.

예를 들어, 고객은 Azure Lighthouse를 통해 서비스 공급자가 제공하는 관리되는 서비스를 원할 수 있습니다. 이를 사용하면 고객이 위임된 구독을 지속적으로 제어하는 것과 함께 파트너의 작업을 볼 수 있습니다. 그러나 서비스 공급자는 고객이 고객의 테넌트에 저장되는 특정 리소스에 액세스하거나 해당 리소스에 대해 사용자 지정된 작업을 고객에게 허용하지 않도록 할 수도 있습니다. 이러한 목표를 달성하기 위해 서비스 공급자는 개인 제품을 관리형 애플리케이션으로 게시할 수 있습니다. 관리형 애플리케이션에는 고객의 테넌트에 배포된 리소스 그룹이 포함 될 수 있지만, 고객이 직접 이것에 액세스할 수는 없습니다.

또한 고객은 여러 서비스 공급자의 관리형 애플리케이션에 관심이 있을 수 있습니다.여기에서 고객이 이러한 서비스 공급자의 Azure Lighthouse를 통해 관리되는 서비스를 사용하는지 여부는 관련성이 없습니다. 또한 CSP(클라우드 솔루션 공급자) 프로그램의 파트너는 다른 ISV가 게시한 특정 관리형 애플리케이션을 Azure Lighthouse를 통해 지원 대상 고객에게 재판매할 수 있습니다. 폭넓은 옵션을 사용하는 경우, 서비스 공급자는 해당하는 경우에 리소스에 대한 액세스를 제한하면서 고객 요구에 맞게 적절한 균형을 선택할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 관리형 애플리케이션](../../azure-resource-manager/managed-applications/overview.md)에 대해 자세히 알아보기
- [Azure Lighthouse에 구독을 온보딩](../how-to/onboard-customer.md)하는 방법에 대해 알아봅니다.
- [Azure Lighthouse를 사용한 ISV 시나리오](isv-scenarios.md)에 대해 알아봅니다.