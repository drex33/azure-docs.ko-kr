---
title: 서비스 공급자 보기 및 관리
description: 고객은 Azure Portal의 Azure Lighthouse 서비스 공급자, 서비스 공급자 제품 및 위임된 리소스에 관한 정보를 볼 수 있습니다.
ms.date: 07/16/2021
ms.topic: how-to
ms.openlocfilehash: fef9d23209e0551f105263c019297df8208e940a
ms.sourcegitcommit: e2fa73b682a30048907e2acb5c890495ad397bd3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114389172"
---
# <a name="view-and-manage-service-providers"></a>서비스 공급자 보기 및 관리

[Azure Portal](https://portal.azure.com)의 **서비스 공급자** 페이지를 통해 고객은 [Azure Lighthouse](../overview.md)를 사용하는 서비스 공급자를 제어하고 표시 유형을 확인할 수 있습니다. 고객은 특정 리소스를 위임하고, 신규 또는 업데이트된 제안을 검토하고, 서비스 공급자 액세스를 제거하는 등의 작업을 수행할 수 있습니다.

Azure Portal의 이 페이지를 보려면 **모든 서비스** 를 선택하고 **서비스 공급자** 를 검색하여 선택합니다. Azure Portal 맨 위에 있는 검색 상자에 "서비스 공급자" 또는 "Azure Lighthouse"를 입력하여 이 페이지를 찾을 수도 있습니다.

> [!NOTE]
> **서비스 공급자** 페이지를 보려면 고객 테넌트의 사용자에게 [리더 기본 제공 역할](../../role-based-access-control/built-in-roles.md#reader)(또는 읽기 권한을 포함하는 다른 기본 제공 역할)이 있어야 합니다.
>
> 제품을 추가하거나 업데이트하고, 리소스를 위임하고, 제품을 제거하려면 사용자에게 [소유자](../../role-based-access-control/built-in-roles.md#owner)와 같은 `Microsoft.Authorization/roleAssignments/write` 권한이 있는 역할이 있어야 합니다.

**서비스 공급자** 페이지에는 Azure Lighthouse를 통해 고객의 구독 또는 리소스 그룹에 액세스할 수 있는 서비스 공급자의 정보만 표시됩니다. Azure Lighthouse를 사용하지 않는 추가 서비스 공급자에 대한 정보는 표시되지 않습니다.

## <a name="view-service-provider-details"></a>서비스 공급자 세부 정보 보기

Azure Lighthouse를 사용하여 고객의 테넌트에서 작업하는 현재 서비스 공급자에 대한 세부 정보를 보려면 **서비스 공급자** 페이지의 왼쪽에서 **서비스 공급자 제품** 을 선택합니다.

각 제품에 대해 서비스 공급자의 이름과 이와 관련된 제품이 표시됩니다. 서비스 공급자에게 부여된 역할 할당을 포함하여 설명 및 기타 세부 정보를 볼 수 있는 제품을 선택할 수 있습니다.

**위임** 열에서 고객은 해당 제품의 서비스 공급자에게 위임된 구독 및/또는 리소스 그룹의 수를 확인할 수 있습니다. 서비스 공급자는 제품에 지정된 액세스 수준에 따라 이러한 구독 및/또는 리소스 그룹을 액세스하고 관리할 수 있습니다.

## <a name="add-service-provider-offers"></a>서비스 공급자 제품 추가

**서비스 공급자 제품** 페이지에서 새 서비스 공급자 제품을 추가하려면 **제품 추가** 를 선택합니다. **프라이빗 제품** 을 선택하여 서비스 공급자가 이 고객에 대해 게시한 제품을 봅니다. 그런 다음 제안을 선택한 다음 **설정 + 구독** 을 선택할 수 있습니다.

## <a name="update-service-provider-offers"></a>서비스 공급자 제품 업데이트

고객이 제품을 추가한 후 서비스 공급자는 새 역할 정의를 추가하는 것과 같이 동일한 제품의 업데이트된 버전을 Azure Marketplace에 게시할 수 있습니다. 제품의 새 버전이 게시된 경우 **서비스 공급자 제품** 페이지에는 해당 제품에 대한 행에 "업데이트" 아이콘이 표시됩니다. 이 아이콘을 선택하여 현재 버전 및 새 제품 버전 간의 차이점을 확인할 수 있습니다.

 ![제품 업데이트 아이콘](../media/update-offer.jpg)

고객은 변경 내용을 검토한 후 새 버전으로 업데이트하도록 선택할 수 있습니다. 그런 다음, 새 버전에 지정된 권한 부여 및 기타 설정이 해당 제품에 대해 위임된 구독 및/또는 리소스 그룹에 적용됩니다.

## <a name="remove-service-provider-offers"></a>서비스 공급자 제품 제거

해당 제품에 대한 행에서 휴지통 아이콘을 선택하여 언제든지 서비스 공급자 제품을 제거할 수 있습니다.

삭제를 확인한 후에는 해당 서비스 공급자가 해당 제품에 대해 이전에 위임된 리소스에 더 이상 액세스할 수 없습니다.

## <a name="delegate-resources"></a>리소스 위임

서비스 공급자가 고객의 리소스에 액세스하고 관리할 수 있으려면 먼저 하나 이상의 특정 구독 및/또는 리소스 그룹을 위임받아야 합니다. 고객이 제품을 추가했지만 아직 리소스를 위임하지 않은 경우 **서비스 공급자 제품** 섹션의 맨 위에 메모가 표시됩니다. 서비스 공급자는 위임이 완료될 때까지 고객 테넌트의 리소스에 대해 작업할 수 없습니다.

구독 또는 리소스 그룹을 제외하려면

1. 서비스 공급자, 제품 및 이름을 포함하는 행의 확인란을 선택합니다. 그런 다음, 화면 위쪽의 **리소스 위임** 을 선택합니다.
1. **리소스 위임** 페이지의 **제품 세부 정보** 섹션에서 서비스 공급자 및 제품에 대한 세부 정보를 검토합니다. 제품에 대한 역할 할당을 검토하려면 **선택한 제품에 대한 세부 정보를 보려면 여기를 클릭하세요.** 를 선택합니다.
1. **위임** 섹션에서 **구독 위임** 또는 **리소스 그룹 위임** 을 선택합니다.
1. 이 제품에 대해 위임하려는 구독 및/또는 리소스 그룹을 선택한 다음, **추가** 를 선택합니다.
1. 페이지 맨 아래에 있는 확인란을 선택하여 이 서비스 공급자에게 선택한 리소스에 대한 액세스 권한을 부여할 것인지 확인한 다음, **위임** 을 선택합니다.

## <a name="view-delegations"></a>위임 보기

위임은 특정 고객 리소스(구독 및/또는 리소스 그룹)와 해당 리소스에 대한 권한을 서비스 공급자에게 부여하는 역할 할당과의 연결을 나타냅니다. 위임 세부 정보를 보려면 **서비스 공급자** 페이지 왼쪽에서 **위임** 을 선택합니다.

페이지 상단의 필터를 사용하여 위임 정보를 정렬하고 그룹화할 수 있습니다. 특정 고객, 제품 또는 키워드별로 필터링할 수도 있습니다.

> [!NOTE]
> [Azure Portal에서 또는 API를 통해 위임된 범위에 대한 역할 할당을 볼 때](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) 이러한 역할이 부여된 서비스 공급자 테넌트의 사용자 또는 이러한 역할 할당은 고객에게 표시되지 않습니다.

## <a name="audit-and-restrict-delegations-in-your-environment"></a>사용자 환경에서 위임 감사 및 제한

고객은 Azure Lighthouse에 위임된 모든 구독 및/또는 리소스 그룹을 검토할 수 있습니다. 이는 구독의 수가 많거나 관리 작업을 수행하는 사용자가 많은 고객에게 특히 유용합니다.

Microsoft는 [관리 테넌트에 대한 범위 위임을 감사](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json)하는 [Azure Policy 기본 제공 정책 정의](../../governance/policy/samples/built-in-policies.md#lighthouse)를 제공합니다. 감사하려는 모든 구독이 포함된 관리 그룹에 이 정책을 할당할 수 있습니다. 이 정책에 대한 준수 여부를 확인하는 경우 정책이 할당된 관리 그룹 내에서 위임된 구독 및/또는 리소스 그룹이 비규격 상태로 표시됩니다. 그런 다음, 결과를 검토하고 예기치 않은 위임이 있는지 확인할 수 있습니다.

다른 [기본 제공 정책 정의](../../governance/policy/samples/built-in-policies.md#lighthouse)를 사용하면 [특정 관리 테넌트로 위임을 제한](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json)할 수 있습니다. 이 정책은 마찬가지로 위임을 제한하려는 모든 구독이 포함된 관리 그룹에 할당할 수 있습니다. 정책이 배포된 후에는 사용자가 지정하는 항목의 외부에 있는 테넌트에 구독을 위임하려는 시도는 거부됩니다.

정책을 할당하고 준수 상태 결과를 확인하는 방법에 대한 자세한 정보는 [빠른 시작: 정책 할당 만들기](../../governance/policy/assign-policy-portal.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure Lighthouse](../overview.md)에 대해 자세히 알아봅니다.
- [서비스 공급자 작업을 감사](view-service-provider-activity.md)하는 방법을 알아봅니다.
- Azure Portal에서 **내 고객** 페이지에서 서비스 공급자가 [고객을 보고 관리](view-manage-customers.md)하는 방법을 알아봅니다.
- [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)이 Azure Lighthouse를 사용하여 관리 환경을 통합하는 방법을 알아봅니다.
