---
title: Azure 예약을 보고 관리할 수 있는 권한
description: Azure Portal에서 Azure 예약을 보고 관리하는 방법을 알아봅니다.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 08/11/2021
ms.author: banders
ms.openlocfilehash: 7f0ac2f8813a38a017a901ad4fe793ed628a06d3
ms.sourcegitcommit: 0ede6bcb140fe805daa75d4b5bdd2c0ee040ef4d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122608279"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Azure 예약을 보고 관리할 수 있는 권한

이 문서에서는 예약 권한이 작동하는 방식과 사용자가 Azure Portal 및 Azure PowerShell에서 Azure 예약을 보고 관리할 수 있는 방법을 설명합니다.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="who-can-manage-a-reservation-by-default"></a>기본적으로 예약을 관리할 수 있는 사용자

기본적으로 다음 사용자는 예약을 보고 관리할 수 있습니다.

- 예약을 구매하는 사용자와 예약 구매에 사용한 청구 구독의 계정 관리자가 예약 주문에 추가됩니다.
- 기업계약 및 Microsoft 고객 계약 청구 관리자.
- 모든 Azure 구독과 관리 그룹을 관리할 수 있는 높은 액세스 권한이 있는 사용자
- Azure AD(Azure Active Directory) 테넌트(디렉터리)의 예약을 위한 예약 관리자
- 예약 읽기 권한자는 Azure Active Directory 테넌트(디렉터리)의 예약에 대한 읽기 전용 액세스 권한을 가집니다.

예약 수명 주기는 Azure 구독과 독립적이므로 예약은 Azure 구독에 있는 리소스가 아닙니다. 대신 구독과 분리된 자체 Azure RBAC 권한이 있는 테넌트 수준 리소스입니다. 예약은 구매 후 구독에서 권한을 상속하지 않습니다.

## <a name="view-and-manage-reservations"></a>예약 보기 및 관리

청구 관리자는 다음 단계를 수행하여 Azure Portal에서 모든 예약과 예약 트랜잭션을 보고 관리합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인하고 **Cost Management + Billing** 으로 이동합니다.
    - EA 관리자인 경우 왼쪽 메뉴에서 **청구 범위** 를 선택한 다음, 청구 범위 목록에서 하나를 선택합니다.
    - Microsoft 고객 계약 청구 프로필 소유자인 경우 왼쪽 메뉴에서 **청구 프로필** 을 선택합니다. 청구 프로필 목록에서 하나를 선택합니다.
1. 왼쪽 메뉴에서 **제품 + 서비스** > **예약** 을 선택합니다.
1. EA 등록 또는 청구 프로필에 대한 전체 예약 목록이 표시됩니다.
1. 청구 관리자는 예약을 선택한 다음, 표시되는 창에서 **액세스 권한 부여** 를 선택하여 예약 소유권을 가져올 수 있습니다.

### <a name="add-billing-administrators"></a>청구 관리자 추가

Azure Portal에서 기업계약 또는 Microsoft 고객 계약에 사용자를 청구 관리자로 추가합니다.

- 기업계약의 경우 _엔터프라이즈 관리자_ 역할이 있는 사용자를 추가하여 기업계약에 적용되는 모든 예약 주문을 보고 관리합니다. Enterprise 관리자는 **Cost Management + Billing** 에서 예약을 보고 관리할 수 있습니다.
    - _Enterprise 관리자(읽기 전용)_ 역할이 있는 사용자는 **Cost Management + Billing** 에서 예약을 볼 수만 있습니다. 
    - 부서 관리자 및 계정 소유자는 액세스 제어(IAM)를 사용하여 명시적으로 추가하지 _않는 한_ 예약을 볼 수 없습니다. 자세한 내용은 [Azure Enterprise 역할 관리](../manage/understand-ea-roles.md)를 참조하세요.
- Microsoft 고객 계약의 경우 청구 프로필 소유자 역할 또는 청구 프로필 기여자 역할을 가진 사용자는 청구 프로필을 사용하여 이루어진 모든 예약 구매를 관리할 수 있습니다. 청구 프로필 리더와 청구서 관리자는 청구 프로필로 지불되는 모든 예약을 볼 수 있습니다. 그러나 예약을 변경할 수는 없습니다.
    자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

## <a name="view-reservations-with-azure-rbac-access"></a>Azure RBAC 액세스를 사용하여 예약 보기

Azure Portal에서 예약을 구매했거나 예약에 추가되는 경우 다음 단계를 수행하여 예약을 보고 관리합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **모든 서비스** > **예약** 을 선택하여 액세스할 수 있는 예약을 나열합니다.

## <a name="manage-subscriptions-and-management-groups-with-elevated-access"></a>높은 액세스 권한으로 구독 및 관리 그룹 관리

[모든 Azure 구독과 관리 그룹을 관리하도록 사용자의 액세스 권한](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json)을 상승할 수 있습니다.

액세스 권한이 상승된 후:

1. **모든 서비스** > **예약** 으로 이동하여 테넌트에 있는 모든 예약을 확인합니다.
1. 예약을 수정하려면 IAM(액세스 제어)을 사용하여 자신을 예약 순서 소유자로 추가합니다.

## <a name="grant-access-to-individual-reservations"></a>개별 예약에 대한 액세스 권한 부여

Azure Portal에서 예약에 대한 소유자 액세스 권한이 있는 사용자와 청구 관리자는 개별 예약 주문에 대한 액세스 관리를 위임할 수 있습니다.

다른 사용자가 예약을 관리할 수 있게 하려면 다음 두 가지 옵션을 사용하면 됩니다.

- 예약 주문의 리소스 범위에 있는 사용자에게 소유자 역할을 할당하여 개별 예약 주문에 대한 액세스 관리를 위임합니다. 제한된 액세스 권한을 부여하려면 다른 역할을 선택합니다.  
     세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

- 기업계약 또는 Microsoft 고객 계약에 사용자를 청구 관리자로 추가합니다.
    - 기업계약의 경우 _엔터프라이즈 관리자_ 역할이 있는 사용자를 추가하여 기업계약에 적용되는 모든 예약 주문을 보고 관리합니다. _엔터프라이즈 관리자(읽기 전용)_ 역할이 있는 사용자는 예약만 볼 수 있습니다. 부서 관리자 및 계정 소유자는 액세스 제어(IAM)를 사용하여 명시적으로 추가하지 _않는 한_ 예약을 볼 수 없습니다. 자세한 내용은 [Azure Enterprise 역할 관리](../manage/understand-ea-roles.md)를 참조하세요.

        _엔터프라이즈 관리자는 예약 주문의 소유권을 가질 수 있으며, 액세스 제어(IAM)를 사용하여 예약에 다른 사용자를 추가할 수 있습니다._
    - Microsoft 고객 계약의 경우 청구 프로필 소유자 역할 또는 청구 프로필 기여자 역할을 가진 사용자는 청구 프로필을 사용하여 이루어진 모든 예약 구매를 관리할 수 있습니다. 청구 프로필 리더와 청구서 관리자는 청구 프로필로 지불되는 모든 예약을 볼 수 있습니다. 그러나 예약을 변경할 수는 없습니다.
    자세한 내용은 [청구 프로필 역할 및 작업](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.

## <a name="grant-access-with-powershell"></a>PowerShell로 액세스 권한 부여

예약 주문에 대한 소유자 액세스 권한이 있는 사용자, 높은 액세스 권한이 있는 사용자 및 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator)는 액세스 권한이 있는 모든 예약 주문에 대한 액세스 관리를 위임할 수 있습니다.

## <a name="assign-the-owner-role-for-all-reservations"></a>모든 예약에 대해 소유자 역할 할당

다음 Azure PowerShell 스크립트를 사용하여 사용자에게 Azure AD 테넌트(디렉터리)의 모든 예약 주문에 대한 Azure RBAC 액세스 권한을 부여합니다.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources
 
Connect-AzAccount -Tenant <TenantId>
 
$response = Invoke-AzRestMethod -Path /providers/Microsoft.Capacity/reservations?api-version=2020-06-01 -Method GET
 
$responseJSON = $response.Content | ConvertFrom-JSON
 
$reservationObjects = $responseJSON.value
 
foreach ($reservation in $reservationObjects)
{
  $reservationOrderId = $reservation.id.substring(0, 84)
  Write-Host "Assiging Owner role assignment to "$reservationOrderId
  New-AzRoleAssignment -Scope $reservationOrderId -ObjectId <ObjectId> -RoleDefinitionName Owner
}
```

### <a name="parameters"></a>매개 변수

**-ObjectId** 사용자, 그룹 또는 서비스 주체의 Azure AD ObjectId입니다.
- 유형: String
- 별칭: Id, PrincipalId
- 위치: 명명됨
- 기본값: 없음
- 파이프라인 입력 허용: True
- 와일드카드 문자 허용: False

**-TenantId** 테넌트 고유 식별자입니다.
- 유형: String
- 위치: 5
- 기본값: 없음
- 파이프라인 입력 허용: False
- 와일드카드 문자 허용: False

[사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#user-access-administrator)는 사용자를 예약 관리자 및 예약 읽기 권한자 역할에 추가할 수 있습니다.

## <a name="add-a-reservation-administrator-role-at-the-tenant-level"></a>테넌트 수준에서 예약 관리자 역할 추가

다음 Azure PowerShell 스크립트를 사용하여 PowerShell로 테넌트 수준에서 예약 관리자 역할을 추가합니다.

```azurepowershell
Import-Module Az.Accounts
Import-Module Az.Resources
Connect-AzAccount -Tenant <TenantId>
New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Administrator"
```

### <a name="parameters"></a>매개 변수

**-ObjectId** 사용자, 그룹 또는 서비스 주체의 Azure AD ObjectId입니다.
- 유형: String
- 별칭: Id, PrincipalId
- 위치: 명명됨
- 기본값: 없음
- 파이프라인 입력 허용: True
- 와일드카드 문자 허용: False

**-TenantId** 테넌트 고유 식별자입니다.
- 유형: String
- 위치: 5
- 기본값: 없음
- 파이프라인 입력 허용: False
- 와일드카드 문자 허용: False

## <a name="assign-a-reservation-reader-role-at-the-tenant-level"></a>테넌트 수준에서 예약 읽기 권한자 역할 할당

다음 Azure PowerShell 스크립트를 사용하여 PowerShell로 테넌트 수준에서 예약 읽기 권한자 역할을 할당합니다.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources

Connect-AzAccount -Tenant <TenantId>

New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Reader"
```

### <a name="parameters"></a>매개 변수

**-ObjectId** 사용자, 그룹 또는 서비스 주체의 Azure AD ObjectId입니다.
- 유형: String
- 별칭: Id, PrincipalId
- 위치: 명명됨
- 기본값: 없음
- 파이프라인 입력 허용: True
- 와일드카드 문자 허용: False

**-TenantId** 테넌트 고유 식별자입니다.
- 유형: String
- 위치: 5
- 기본값: 없음
- 파이프라인 입력 허용: False
- 와일드카드 문자 허용: False


## <a name="next-steps"></a>다음 단계

- [Azure 예약 관리](manage-reserved-vm-instance.md)
