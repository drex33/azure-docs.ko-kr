---
title: 파트너 ID를 연결하여 위임된 리소스에 대한 영향 추적
description: Azure Lighthouse를 통해 관리하는 고객 리소스에서 파트너 ID를 연결하여 PEC(파트너 획득 크레딧)를 받습니다.
ms.date: 07/02/2021
ms.topic: how-to
ms.openlocfilehash: 72a31da9cf9927de00f005ce374488c0de9eba47
ms.sourcegitcommit: d90cb315dd90af66a247ac91d982ec50dde1c45f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/04/2021
ms.locfileid: "113288728"
---
# <a name="link-your-partner-id-to-track-your-impact-on-delegated-resources"></a>파트너 ID를 연결하여 위임된 리소스에 대한 영향 추적 

[Microsoft 파트너 네트워크](https://partner.microsoft.com/) 멤버인 경우 Microsoft가 Azure 고객 성공을 견인하는 파트너를 식별하고 인식할 수 있도록 파트너 ID를 위임된 고객 리소스를 관리하는 데 사용되는 자격 증명과 연결할 수 있습니다. 또한 이 링크를 통해 [CSP(클라우드 솔루션 공급자)](/partner-center/csp-overview) 파트너는 [관리 서비스에 대한 PEC(파트너 획득 크레딧)](/partner-center/partner-earned-credit)를 받을 수 있습니다. 대상 고객은 [MCA(Microsoft 고객 계약)에 서명한 고객](/partner-center/confirm-customer-agreement)이며 [Azure 플랜](/partner-center/azure-plan-get-started) 중에 있습니다.

Azure Lighthouse 활동에 대한 인식을 획득하려면 관리 테넌트의 사용자 계정 하나 이상과 [MPN ID를 연결](../../cost-management-billing/manage/link-partner-id.md)해야 하며, 연결된 계정은 각 등록된 구독에 대한 액세스 권한이 있어야 합니다.

## <a name="associate-your-partner-id-when-you-onboard-new-customers"></a>새 고객을 등록할 때 파트너 ID 연결

다음 프로세스를 사용하여 파트너 ID를 연결하고, 해당하는 경우 파트너 획득 크레딧을 사용하도록 설정합니다. 이러한 단계를 완료하려면 [MPN 파트너 ID](/partner-center/partner-center-account-setup#locate-your-mpn-id)를 알고 있어야 합니다. 파트너 프로필에 표시된 **관련 MPN ID** 를 사용해야 합니다.

간단히 하면, 테넌트에 서비스 주체 계정을 만들고, **연결된 MPN ID** 와 연결하고, [PEC에 적격한 Azure 기본 제공 역할](/partner-center/azure-roles-perms-pec)을 사용하여 등록한 모든 고객에게 액세스 권한을 부여하는 것이 좋습니다.

1. 관리 테넌트에서 [서비스 주체 사용자 계정을 만듭니다](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 이 예제에서는 이 서비스 주체 계정의 이름을 *Provider Automation Account* 로 지정합니다.
1. 관리 테넌트에서 이 서비스 주체 계정을 [연결된 MPN ID에 연결](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)합니다. 이 작업은 한 번만 필요합니다.
1. [ARM 템플릿](onboard-customer.md) 또는 [관리되는 서비스 제품](publish-managed-services-offers.md)을 사용하여 고객을 등록하는 경우, [PEC에 적격한 Azure 기본 제공 역할](/partner-center/azure-roles-perms-pec)이 있는 사용자로 Provider Automation 계정을 포함하는 권한 부여를 하나 이상 포함해야 합니다. PEC를 적용하려면 이 역할을 적시에 [적격한 권한 부여](create-eligible-authorizations.md)가 아닌 영구 할당으로 부여해야 합니다.

이러한 단계를 수행하면 관리하는 모든 고객 테넌트가 파트너 ID와 연결됩니다. Provider Automation Account는 고객 테넌트에서 인증하거나 작업을 수행할 필요가 없습니다.

:::image type="content" source="../media/lighthouse-pal.jpg" alt-text="Azure Lighthouse를 사용한 파트너 ID 연결 프로세스를 보여 주는 다이어그램.":::

## <a name="add-your-partner-id-to-previously-onboarded-customers"></a>이전에 등록한 고객에게 파트너 ID 추가

이미 고객을 등록한 경우 다른 배포를 수행하여 Provider Automation Account 서비스 주체를 추가하기 위해 다른 배포를 수행하지 않으려고 할 수 있습니다. 대신, **연결된 MPN ID** 를 해당 고객 테넌트의 작업에 대한 액세스 권한이 이미 있는 사용자 계정과 연결할 수 있습니다. 해당 계정에는 [PEC에 적격한 Azure 기본 제공 역할](/partner-center/azure-roles-perms-pec)이 영구 역할 할당으로 부여되어 있어야 합니다.

계정이 관리 테넌트의 [연결된 MPN ID에 연결](../../cost-management-billing/manage/link-partner-id.md#link-to-a-partner-id)되면 해당 고객에 대한 영향의 인식을 추적할 수 있습니다.

## <a name="confirm-partner-earned-credit"></a>파트너 획득 크레딧 확인

[Azure Portal에서 PEC 세부 정보를 보고](/partner-center/partner-earned-credit-explanation#azure-cost-management) PEC의 혜택을 받은 비용을 확인할 수 있습니다. PEC는 MCA에 서명하고 Azure 플랜에 속하는 CSP 고객에게만 적용됩니다.

위의 단계를 수행한 후 예상되는 연결이 표시되지 않으면 Azure Portal에서 지원 요청을 개설하세요.

[파트너 센터 SDK](/partner-center/develop/get-invoice-unbilled-consumption-lineitems)를 사용하고 `rateOfPartnerEarnedCredit`에 필터를 설정하여 구독에 대한 PEC 확인을 자동화할 수도 있습니다.

## <a name="next-steps"></a>다음 단계

- [Microsoft 파트너 네트워크](/partner-center/mpn-overview)에 대해 자세히 알아봅니다.
- [PEC 계산 및 지불 방법](/partner-center/partner-earned-credit-explanation)을 알아봅니다.
- Azure Lighthouse에 [고객을 등록](onboard-customer.md)합니다.