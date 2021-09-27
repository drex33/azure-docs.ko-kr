---
title: 위임 업데이트
description: 이전에 Azure Lighthouse에 등록한 고객에 대한 위임을 업데이트하는 방법에 대해 알아봅니다.
ms.date: 09/08/2021
ms.topic: how-to
ms.openlocfilehash: 41ba14433f1af7abb60034e4034f0585ed9968f4
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124736662"
---
# <a name="update-a-delegation"></a>위임 업데이트

구독(또는 리소스 그룹)을 Azure Lighthouse에 온보딩한 후에 변경해야 하는 경우가 있습니다. 예를 들어, 고객이 여러 Azure 기본 제공 역할을 요구하는 추가 관리 작업을 수행하거나 고객 구독이 위임되는 테넌트를 변경해야 할 수 있습니다.

> [!TIP]
> 이 토픽에서는 서비스 공급자 및 고객 관련 내용을 다루지만 [여러 테넌트를 관리하는 엔터프라이즈](../concepts/enterprise.md)는 동일한 프로세스를 사용하여 Azure Lighthouse를 설정하고 관리 환경을 통합할 수 있습니다.

[ARM 템플릿(Azure Resource Manager 템플릿)을 통해 고객을 온보딩](onboard-customer.md)하는 경우 해당 고객에 대한 새 배포를 수행해야 합니다. 변경하는 내용에 따라 원래 제안을 업데이트하거나 원래 제안을 제거하고 새 제안을 만들 수 있습니다.

- **권한 부여를 변경하는 경우**: ARM 템플릿의 **권한 부여** 섹션만 변경하여 위임을 업데이트할 수 있습니다.
- **관리 테넌트를 변경하는 경우**: 이전 제안과 다른 **mspOfferName** 을 사용하여 새 ARM 템플릿을 만들어야 합니다.

## <a name="update-your-arm-template"></a>ARM 템플릿 업데이트

위임을 업데이트하려면 원하는 변경 내용을 포함하는 ARM 템플릿을 배포해야 합니다.

이전에 포함한 역할을 사용하여 새 사용자 그룹을 추가하거나 기존 사용자에 대한 역할을 변경하는 등 권한 부여만 업데이트하는 경우에는 이전 위임에 사용한 [ARM 템플릿](onboard-customer.md#create-an-azure-resource-manager-template)에서와 동일한 **mspOfferName** 을 사용할 수 있습니다. 이전 템플릿을 시작 지점으로 사용할 수 있습니다. 그런 다음, Azure 기본 제공 역할을 다른 역할로 바꾸거나 템플릿에 완전히 새로운 권한 부여를 추가하는 등 필요한 내용을 변경합니다.

**mspOfferName** 을 변경하는 경우 이는 별도의 새로운 제안으로 간주됩니다. 이 작업은 관리 테넌트를 변경하는 경우에 필요합니다.

관리 테넌트가 동일하게 유지되는 경우에는 **mspOfferName** 을 변경할 필요가 없습니다. 대부분의 경우 동일한 고객에게 하나의 **mspOfferName** 만 사용하고 테넌트를 관리하는 것이 좋습니다. 이를 변경하도록 선택하는 경우에는 새 제안을 배포하기 전에 고객의 이전 위임이 제거되었는지를 먼저 확인해야 합니다.

## <a name="remove-the-previous-delegation"></a>이전 위임 제거

새 배포를 수행하기 전에 [이전 위임에 대한 액세스 권한을 제거](remove-delegation.md)하는 것이 좋습니다. 이렇게 하면 모든 이전 권한이 제거되므로 앞으로 적용해야 하는 정확한 사용자/그룹 및 역할로 정리를 시작할 수 있습니다.

> [!IMPORTANT]
> 새 **mspOfferName** 을 사용하고 동일한 **principalId** 값을 유지하는 경우 새 제안을 배포하기 전에 이전 위임에 대한 액세스 권한을 제거해야 합니다. 제안을 먼저 제거하지 않으면 이전에 사용 권한을 부여받은 사용자가 충돌하는 할당으로 인해 완전히 액세스할 수 없게 됩니다.

관리 테넌트를 변경하는 경우 두 테넌트 모두에 계속 액세스하려면 이전 제안을 그대로 두면 됩니다. 새 관리 테넌트에만 액세스할 수 있도록 하려면 이전 제안을 제거해야 합니다. 새 제안을 등록하기 전이나 후에 이 작업을 수행할 수 있습니다.

제안을 업데이트하여 권한 부여만 조정하고 동일한 **mspOfferName** 을 유지하는 경우에는 이전 위임을 제거할 필요가 없습니다. 새 배포가 이전 위임을 대체하고 최신 템플릿의 권한 부여만 적용됩니다.

:::image type="content" source="../media/update-delegation.jpg" alt-text="mspOfferName을 변경하고 이전 위임을 제거하는 경우를 보여 주는 다이어그램":::

위임에 대한 액세스를 제거하는 작업은 원래 위임에서 [관리 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)이 부여된 관리 테넌트의 모든 사용자가 수행할 수 있습니다. 관리 테넌트에 이 역할이 있는 사용자가 없는 경우 고객에게 [Azure Portal의 제안에 대한 액세스 권한을 제거](view-manage-service-providers.md#remove-service-provider-offers)하도록 요청할 수 있습니다.

> [!TIP]
> 위의 단계를 수행하여 이전 위임을 제거하고 여전히 새 ARM 템플릿을 배포할 수 없는 경우 [등록 정의를 완전히 제거](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition)해야 할 수 있습니다. 이 작업은 고객 테넌트에서 [Owner](../../role-based-access-control/built-in-roles.md#owner)와 같이, `Microsoft.Authorization/roleAssignments/write` 권한이 있는 역할을 가진 모든 사용자가 수행할 수 있습니다.  

## <a name="deploy-the-arm-template"></a>ARM 템플릿 배포

고객은 이전에 수행한 것과 동일한 방식으로 [업데이트된 템플릿을 배포](onboard-customer.md#deploy-the-azure-resource-manager-template)할 수 있습니다. 즉, Azure Portal에서 PowerShell을 사용하거나 Azure CLI를 사용하여 배포할 수 있습니다.

배포가 완료된 후에 [성공했는지 확인](onboard-customer.md#confirm-successful-onboarding)합니다. 그런 다음, 업데이트된 권한 부여가 고객이 위임한 구독 또는 리소스 그룹에 적용됩니다.

## <a name="updating-managed-service-offers"></a>관리 서비스 제안 업데이트

Azure Marketplace 게시된 관리 서비스 제안을 통해 고객을 온보딩하고 권한 부여를 업데이트하려는 경우 해당 고객에 대한 플랜에서 업데이트된 사용하려는 [권한 부여를](../../marketplace/create-managed-service-offer-plans.md#authorizations) 사용하여 [새 버전의 제안을 게시하면](../../marketplace/update-existing-offer.md) 됩니다. 그러면 고객은 [Azure Portal 변경 내용을 검토하고 새 버전 을 수락할](view-manage-service-providers.md#update-service-provider-offers)수 있습니다.

관리 테넌트를 변경하려는 경우 고객이 수락할 [새 관리 서비스 제안을 만들고 게시](publish-managed-services-offers.md)해야 합니다.

> [!IMPORTANT]
> 앞서 언급했듯이 동일한 고객에 대해 여러 제안을 사용하고 테넌트를 관리하는 것을 방지하는 것이 좋습니다. 동일한 관리 테넌트를 사용하는 동일한 고객에 대한 새 제안을 게시하는 경우 고객이 최신 제안을 수락하기 전에 이전 제안이 제거되었는지 확인해야 합니다.

## <a name="next-steps"></a>다음 단계

- Azure Portal의 **내 고객** 으로 이동하여 [고객을 보고 관리](view-manage-customers.md)합니다.
- 이전에 등록된 [위임에 대한 액세스 권한 제거](remove-delegation.md) 방법을 알아봅니다.
- [Azure Lighthouse 아키텍처](../concepts/architecture.md)에 대해 자세히 알아봅니다.
