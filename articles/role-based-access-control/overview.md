---
title: Azure RBAC(Azure 역할 기반 액세스 제어)란?
description: Azure RBAC(Azure 역할 기반 액세스 제어) 개요를 가져옵니다. 역할 할당을 사용하여 Azure 리소스에 대한 액세스를 제어합니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: overview
ms.workload: identity
ms.date: 05/17/2021
ms.author: rolyon
ms.custom: contperf-fy21q1, azuread-video-2020
ms.openlocfilehash: 3b731b83264802884bba01ed3c32db6152d0a4f9
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356123"
---
# <a name="what-is-azure-role-based-access-control-azure-rbac"></a>Azure RBAC(Azure 역할 기반 액세스 제어)란?

클라우드 리소스에 대한 액세스 관리는 클라우드를 사용하는 모든 조직에서 중요한 기능입니다. Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure 리소스에 액세스할 수 있는 사용자, 해당 리소스로 수행할 수 있는 작업 및 액세스 권한이 있는 영역을 관리하는 데 도움을 줍니다.

Azure RBAC는 Azure 리소스에 대한 액세스를 세밀하게 관리할 수 있는 [Azure Resource Manager](../azure-resource-manager/management/overview.md) 기반의 권한 부여 시스템입니다.

이 비디오에서는 Azure RBAC에 대한 개요를 빠르게 제공합니다.

>[!VIDEO https://www.youtube.com/embed/Dzhm-garKBM]

## <a name="what-can-i-do-with-azure-rbac"></a>Azure RBAC로 어떤 작업을 할 수 있나요?

Azure RBAC로 다음과 같은 일을 할 수 있습니다.

- 한 사용자는 구독의 가상 머신을 관리하고 다른 사용자는 가상 네트워크를 관리하도록 허용
- DBA 그룹이 구독의 SQL 데이터베이스를 관리하도록 허용
- 사용자가 가상 머신, 웹 사이트, 서브넷 등 리소스 그룹의 모든 리소스를 관리하도록 허용
- 애플리케이션이 리소스 그룹의 모든 리소스에 액세스하도록 허용

## <a name="how-azure-rbac-works"></a>Azure RBAC 작동 방법

Azure RBAC를 사용하여 리소스에 대한 액세스를 제어하는 방법은 Azure 역할을 할당하는 것입니다. 이 개념을 반드시 이해해야 하며, 이 원리에 따라 권한이 적용됩니다. 역할 할당은 보안 주체, 역할 정의, 범위의 세 가지 요소로 구성됩니다.

### <a name="security-principal"></a>보안 주체

*보안 주체* 는 사용자, 그룹, 서비스 주체 또는 Azure 리소스에 대한 액세스를 요청하는 관리 ID를 나타내는 개체입니다. 이러한 보안 주체에 역할을 할당할 수 있습니다.

![역할 할당의 보안 주체 유형을 보여 주는 다이어그램.](./media/shared/rbac-security-principal.png)

### <a name="role-definition"></a>역할 정의

*역할 정의* 는 권한 컬렉션입니다. 일반적으로 단지 *역할* 이라고 합니다. 역할 정의에는 읽기, 쓰기 및 삭제와 같이 수행할 수 있는 작업이 나열됩니다. 역할은 소유자처럼 대략적일 수도 있고 가상 머신 판독기처럼 구체적일 수도 있습니다.

![역할 할당을 위한 역할 정의 예를 보여 주는 다이어그램](./media/shared/rbac-role-definition.png)

Azure에는 사용 가능한 [기본 제공 역할](built-in-roles.md)이 여러 개 있습니다. 예를 들어 [Virtual Machine 기여자](built-in-roles.md#virtual-machine-contributor) 역할을 사용하면 사용자가 가상 머신을 만들고 관리할 수 있습니다. 기본 제공 역할이 조직의 특정 요구 사항을 충족하지 않는 경우 [Azure 사용자 지정 역할](custom-roles.md)을 만들면 됩니다.

이 비디오에서는 기본 제공 역할 및 사용자 지정 역할에 대한 간략한 개요를 제공합니다.

>[!VIDEO https://www.youtube.com/embed/I1mefHptRgo]

Azure에는 개체 내 데이터에 대한 액세스 권한을 부여할 수 있는 데이터 작업이 있습니다. 예를 들어 사용자가 스토리지 계정에 대한 데이터 읽기 액세스 권한을 갖고 있는 경우 해당 스토리지 계정 내의 BLOB 또는 메시지를 읽을 수 있습니다.

자세한 내용은 [Azure 역할 정의 이해](role-definitions.md)를 참조하세요.

### <a name="scope"></a>범위

*범위* 는 액세스가 적용되는 리소스의 세트입니다. 역할에 할당할 때 범위를 정의하여 허용되는 작업을 추가로 제한할 수 있습니다. 이러한 기능은 누군가를 단일 리소스 그룹에 대해서만 [웹 사이트 기여자](built-in-roles.md#website-contributor)로 지정하려는 경우에 유용합니다.

Azure는 4개 수준([관리 그룹](../governance/management-groups/overview.md), 구독, [리소스 그룹](../azure-resource-manager/management/overview.md#resource-groups) 또는 리소스)에서 범위를 지정할 수 있습니다. 범위는 부모-자식 관계로 구조화되어 있습니다. 이러한 범위 수준에서 역할을 할당할 수 있습니다.

![역할 할당의 범위 수준을 보여 주는 다이어그램.](./media/shared/rbac-scope.png)

범위에 대한 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

### <a name="role-assignments"></a>역할 할당

*역할 할당* 은 액세스 권한을 부여하기 위해 특정 범위에서 역할 정의를 사용자, 그룹, 서비스 주체 또는 관리 ID에 연결하는 프로세스입니다. 역할 할당을 만들어서 액세스 권한을 부여하고, 역할 할당을 제거하여 액세스 권한을 취소합니다.

다음 다이어그램은 역할 할당의 예를 보여줍니다. 이 예제에서 마케팅 그룹에는 pharma-sales 리소스 그룹에 대한 [기여자](built-in-roles.md#contributor) 역할이 할당되었습니다. 즉, 마케팅 그룹의 해당 사용자가 pharma-sales 리소스 그룹에 Azure 리소스를 만들거나 관리할 수 있습니다. 마케팅 사용자는 다른 역할 할당에 포함되지 않는 한, pharma-sales 리소스 그룹 외부에 있는 리소스에 액세스할 수 없습니다.

![보안 주체, 역할 정의 및 범위가 역할 할당을 만드는 방법을 보여 주는 다이어그램.](./media/overview/rbac-overview.png)

Azure Portal, Azure CLI, Azure PowerShell, Azure SDK 또는 REST API를 사용하여 역할을 할당할 수 있습니다.

자세한 내용은 [Azure 역할을 할당하는 단계](role-assignments-steps.md)를 참조하세요.

## <a name="groups"></a>그룹

역할 할당은 그룹에 대해 전이적입니다. 즉, 사용자가 그룹의 멤버이고 해당 그룹이 역할 할당이 있는 다른 그룹의 멤버인 경우 해당 사용자는 역할 할당에 대한 권한을 갖게 됩니다.

![역할 할당이 그룹에 전이되는 방식을 보여주는 다이어그램.](./media/overview/rbac-groups-transitive.png)

## <a name="multiple-role-assignments"></a>여러 역할 할당

따라서 여러 겹치는 역할 할당이 있는 경우 어떻게 되나요? Azure RBAC는 추가 모델이므로 유효 권한은 역할 할당의 합계입니다. 사용자가 구독 범위에서 기여자 역할에 부여되고 리소스 그룹에서 읽기 권한자 역할에 부여되는 다음 예제를 고려해 봅니다. 기여자 권한과 리더 권한의 합계는 사실상 구독에 대한 기여자 역할입니다. 따라서 이 경우 판독기 역할 할당은 영향을 주지 않습니다.

![여러 역할 할당이 겹치는 방식을 보여주는 다이어그램.](./media/overview/rbac-multiple-roles.png)

## <a name="deny-assignments"></a>거부 할당

이전에는 Azure RBAC가 거부가 없는 허용 전용 모델이었지만 이제 Azure RBAC는 제한된 방식으로 거부 할당을 지원합니다. *거부 할당* 은 역할 할당과 마찬가지로 액세스를 거부하기 위해 특정 범위에서 사용자, 그룹, 서비스 주체 또는 관리 ID에게 거부 작업 세트를 연결합니다. 역할 할당은 *허용된* 일련의 작업을 정의하고 거부 할당은 *허용되지 않는* 일련의 작업을 정의합니다. 즉, 거부 할당은 역할 할당이 사용자에게 액세스 권한을 부여하더라도 지정된 작업을 사용자가 수행할 수 없도록 차단합니다. 거부 할당은 역할 할당보다 우선합니다.

자세한 내용은 [Azure 거부 할당 이해](deny-assignments.md)를 참조하세요.

## <a name="how-azure-rbac-determines-if-a-user-has-access-to-a-resource"></a>Azure RBAC에서 사용자가 리소스에 액세스 권한이 있는지 확인하는 방법

다음은 리소스에 액세스할 수 있는지 확인하기 위해 Azure RBAC가 사용하는 고급 단계입니다. 이러한 단계는 Azure RBAC와 통합된 Azure Resource Manager 또는 데이터 평면 서비스에 적용됩니다. 이 단계는 액세스 문제를 해결하려는 경우 이해하는 데 도움이 됩니다.

1. 사용자(또는 서비스 주체)가 Azure Resource Manager에 대한 토큰을 획득합니다.

    토큰에는 사용자의 그룹 멤버 자격(전이적 그룹 멤버 자격)이 포함됩니다.

1. 사용자는 연결된 토큰을 사용하여 Azure Resource Manager에 REST API를 호출합니다.

1. Azure Resource Manager는 모든 역할 할당을 검색하고 작업이 수행될 때 리소스에 적용되는 할당을 거부합니다.

1. 거부 할당이 적용되면 액세스가 차단됩니다. 그렇지 않으면 평가가 계속됩니다.

1. Azure Resource Manager는 이 사용자 또는 그룹에 적용되는 역할 할당을 범위를 좁히고 사용자가 이 리소스에 대해 갖는 역할을 결정합니다.

1. Azure Resource Manager는 사용자가 이 리소스에 대해 갖는 역할에 API 호출의 동작이 포함되는지 여부를 결정합니다. 역할에 와일드카드(`*`)가 있는 `Actions`가 포함된 경우, 허용된 `Actions`에서 `NotActions`를 빼서 유효 권한을 계산합니다. 마찬가지로 모든 데이터 작업에 대해 동일한 빼기가 수행됩니다.

    `Actions - NotActions = Effective management permissions`

    `DataActions - NotDataActions = Effective data permissions`

1. 사용자에게 요청된 범위에서 작업에 대한 역할이 없으면 액세스 권한이 허용되지 않습니다. 그렇지 않으면 모든 조건이 평가됩니다.

1. 역할 할당에 조건이 포함되어 있으면 평가됩니다. 그렇지 않으면 액세스가 허용됩니다.

1. 조건이 충족되면 액세스가 허용됩니다. 그렇지 않으면 액세스가 허용되지 않습니다.

다음 다이어그램은 평가 논리의 요약입니다.

![리소스에 대한 액세스를 결정하기 위한 평가 논리 순서도입니다.](./media/overview/evaluation-logic.png)

## <a name="license-requirements"></a>라이선스 요구 사항

[!INCLUDE [Azure AD free license](../../includes/active-directory-free-license.md)]

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)
- [여러 역할의 이해](rbac-and-directory-admin-roles.md)
- [클라우드 채택 프레임워크: Azure에서 리소스 액세스 관리](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management)
