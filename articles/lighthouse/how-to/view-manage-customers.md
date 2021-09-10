---
title: Azure Portal에서 고객과 위임된 리소스 보기 및 관리
description: Azure Lighthouse를 사용하는 서비스 공급자는 Azure Portal의 내 고객으로 이동하여 위임된 모든 고객 리소스 및 구독을 볼 수 있습니다.
ms.date: 08/12/2021
ms.topic: how-to
ms.openlocfilehash: a29f34983bc42d74efd65a45605bb99944897345
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537334"
---
# <a name="view-and-manage-customers-and-delegated-resources-in-the-azure-portal"></a>Azure Portal에서 고객과 위임된 리소스 보기 및 관리

[Azure Lighthouse](../overview.md)를 사용하는 서비스 공급자는 [Azure Portal](https://portal.azure.com)의 **내 고객** 페이지를 사용하여 위임된 고객 리소스 및 구독을 볼 수 있습니다.

> [!TIP]
> 여기서는 서비스 공급자 및 고객 관련 내용을 다루지만, [여러 테넌트를 관리하는 기업](../concepts/enterprise.md)은 동일한 프로세스를 사용하여 관리 환경을 통합할 수 있습니다.

Azure Portal의 **내 고객** 페이지에 액세스하려면 **모든 서비스** 를 선택한 다음, **내 고객** 을 검색하여 선택합니다. Azure Portal 맨 위에 있는 검색 상자에 "내 고객"을 입력하여 찾을 수도 있습니다.

**내 고객** 페이지의 상위 **고객** 섹션에는 Azure Lighthouse를 통해 구독 또는 리소스 그룹을 Azure Active Directory(Azure AD) 테넌트에 위임받은 고객에 대한 정보만 표시된다는 점을 기억합니다. 다른 고객과 작업 하는 경우( [Cloud Solution Provider(CSP) 프로그램](/partner-center/csp-overview) 등을 통해) 해당 [리소스를 Azure Lighthouse로 온보딩](onboard-customer.md)하지 않는 한(비록 페이지 하단의 [Cloud Solution Provider(미리 보기) 섹션](#cloud-solution-provider-preview)에서 특정 CSP 고객에 대한 세부 정보를 볼 수 있지만) **고객** 섹션의 해당 고객 정보를 볼 수 없습니다.

> [!NOTE]
> 고객은 Azure Portal의 **서비스 공급자** 로 이동하여 서비스 공급자에 대한 정보를 볼 수 있습니다. 자세한 내용은 [서비스 공급자 보기 및 관리](view-manage-service-providers.md)를 참조하세요.

## <a name="view-and-manage-customer-details"></a>고객 세부 정보 보기 및 관리

고객 세부 정보를 보려면 **내 고객** 페이지의 왼쪽에서 **고객** 을 선택합니다.

> [!IMPORTANT]
> 이 정보를 보려면 사용자는 온보딩 프로세스에서 [Reader](../../role-based-access-control/built-in-roles.md#reader) 역할(또는 읽기 권한을 포함하는 다른 기본 제공 역할)을 받아야 합니다.

각 고객에 대해 사용자 참여와 연관된 고객 이름, 고객 ID(테넌트 ID), **제품 ID** 및 **제품 버전** 을 볼 수 있습니다. **위임** 열에는 위임된 구독의 수 및/또는 위임된 리소스 그룹의 수가 표시됩니다.

페이지 맨 위에 있는 옵션을 통해 특정 고객, 제품 또는 키워드를 기준으로 고객 정보를 정렬, 필터링 및 그룹화할 수 있습니다.

이 페이지에서 다음 정보를 볼 수 있습니다.

- 고객과 연결된 모든 구독, 제품 및 위임을 보려면 고객의 이름을 선택합니다.
- 제품 및 해당 위임에 대한 자세한 내용을 보려면 제품 이름을 선택합니다.
- 위임된 구독 또는 리소스 그룹의 역할 할당에 대한 자세한 내용을 보려면 **위임** 열의 항목을 선택합니다.

> [!NOTE]
> 고객이 위임된 후 구독의 이름을 바꾸면 업데이트된 구독 이름이 표시됩니다. 테넌트의 이름을 바꾸는 경우 Azure Portal의 일부 위치에서는 이전 테넌트 이름이 계속 표시될 수 있습니다.

## <a name="view-and-manage-delegations"></a>위임 보기 및 관리

위임에는 위임된 구독 또는 리소스 그룹과 해당 액세스 권한이 있는 사용자 및 권한이 함께 표시됩니다. 이 정보를 보려면 **내 고객** 페이지의 왼쪽에서 **위임** 을 선택합니다.

페이지 맨 위에 있는 옵션을 통해 이 정보를 특정 고객, 제품 또는 키워드를 기준으로 정렬, 필터링 및 그룹화할 수 있습니다.

### <a name="view-role-assignments"></a>역할 할당 보기

각 위임에 연결된 사용자 및 사용 권한은 **역할 할당** 열에 표시됩니다. 각 항목을 선택하여 구독 또는 리소스 그룹에 대한 액세스 권한이 부여된 사용자, 그룹 및 서비스 주체의 전체 목록을 볼 수 있습니다. 여기서 특정 사용자, 그룹 또는 서비스 주체 이름을 선택하여 자세한 정보를 얻을 수 있습니다.

### <a name="remove-delegations"></a>위임 제거

Azure Lighthouse에 고객을 온보딩할 때 [관리형 서비스 등록 할당 삭제 역할](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role)을 맡은 사용자를 포함한 경우, 해당 사용자는 해당 위임 관련 행에 표시되는 휴지통 아이콘을 선택하여 위임을 제거할 수 있습니다. 이렇게 하면 서비스 공급자 테넌트에 있는 사용자가 이전에 위임된 리소스에 액세스할 수 없게 됩니다.

자세한 내용은 [위임에 대한 액세스 권한 제거](remove-delegation.md)를 참조하세요.

## <a name="view-delegation-change-activity"></a>위임 변경 작업 보기

**내 고객** 페이지의 **활동 로그** 섹션은 고객이 구독 또는 리소스 그룹을 테넌트에 위임할 때, 그리고 이전에 위임된 리소스가 제거될 때 매번 추적합니다. 이 정보는 [루트 범위에서 모니터링 Reader 역할이 할당된](monitor-delegation-changes.md) 사용자만 볼 수 있습니다.

자세한 내용은 [Azure Portal에서 위임 변경 내용 보기](monitor-delegation-changes.md#view-delegation-changes-in-the-azure-portal)를 참조하세요.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>위임된 구독의 컨텍스트에서 작업

로그인한 디렉터리에서 전환하지 않고도 Azure Portal 내의 위임된 구독 컨텍스트에서 직접 작업할 수 있습니다. 이를 수행하려면:

1. Azure Portal 위쪽에서 **디렉터리 + 구독** 또는 **설정** 아이콘을 선택합니다.
1. [디렉터리 + 구독 설정 페이지](../../azure-portal/set-preferences.md#directories--subscriptions)에서 **고급 필터** 토글이 [꺼져 있는지](../../azure-portal/set-preferences.md#subscription-filters) 확인합니다.
1. **기본 구독 필터** 섹션에서 적절한 디렉터리와 구독을 선택합니다.

:::image type="content" source="../media/subscription-filter-delegated.png" alt-text="하나의 위임된 구독을 표시하는 필터 스크린샷":::

그런 다음, [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)을 지원하는 서비스에 액세스하면 서비스는 기본적으로 필터에 포함한 위임된 구독의 컨텍스트로 설정됩니다. 위의 단계를 수행하고 **모두 선택** 확인란을 선택하여 변경하거나 대신 사용할 하나 이상의 구독을 선택할 수 있습니다.

> [!NOTE]
> 전체 구독에 액세스하는 대신, 하나 이상의 리소스 그룹에 대한 액세스 권한이 부여된 경우, 해당 리소스 그룹이 속한 구독을 선택합니다. 그러면 해당 구독의 컨텍스트에서 작업하게 되지만 지정된 리소스 그룹에만 액세스할 수 있습니다.

개별 서비스 내에서 구독이나 리소스 그룹을 선택하여 테넌트 간 관리 환경을 지원하는 서비스 내에서 위임된 구독이나 리소스 그룹과 관련된 기능에 액세스할 수도 있습니다.

## <a name="cloud-solution-provider-preview"></a>클라우드 솔루션 공급자(미리 보기)

**내 고객** 페이지의 별도 **클라우드 솔루션 공급자(미리 보기)** 섹션은 [MCA(Microsoft Customer Agreement)에 서명하고](/partner-center/confirm-customer-agreement) 또한 [Azure 플랜에 속하는](/partner-center/azure-plan-get-started) CSP 고객의 청구 정보 및 리소스를 표시합니다. 자세한 내용은 [Microsoft 파트너 계약의 청구 계정 시작](../../cost-management-billing/understand/mpa-overview.md)을 참조하세요.

해당 CSP 고객은 Azure Lighthouse 등록 여부와 관계없이 현재 섹션에 표시됩니다. 마찬가지로 CSP 고객은 Azure Lighthouse 온보딩을 위해 **내 고객** 의 **클라우드 솔루션 공급자(미리 보기)** 섹션에 표시될 필요는 없습니다.

## <a name="next-steps"></a>다음 단계

- [테넌트 간 관리 환경](../concepts/cross-tenant-management-experience.md)에 대해 알아봅니다.
- Azure Portal에서 **서비스 공급자** 로 이동하여 고객이 [서비스 공급자를 보고 관리](view-manage-service-providers.md)하는 방법을 알아봅니다.
