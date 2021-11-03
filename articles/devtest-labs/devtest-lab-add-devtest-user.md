---
title: 소유자 및 사용자 추가
description: Azure Portal 또는 PowerShell을 사용하여 Azure DevTest Labs에 소유자 및 사용자 추가
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6edf548658d02ae3427fe5ac448dcc3e38a6b4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068763"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Azure DevTest Labs에 소유자 및 사용자 추가
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

Azure DevTest Labs의 액세스는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/overview.md)을 통해 제어됩니다. Azure RBAC를 사용하면 팀 내에서 업무를 수행하기 위해 사용자에게 필요한 만큼의 액세스 권한을 부여하는 *역할* 로 업무를 분리할 수 있습니다. 이러한 세 가지 Azure 역할은 *소유자*, *DevTest Labs 사용자* 및 *참가자* 입니다. 이 문서에서는 세 가지 주요 Azure 역할에서 각각 수행할 수 있는 작업을 학습합니다. 여기서 포털 및 PowerShell 스크립트를 통해 랩에 사용자를 추가하는 방법과 구독 수준에서 사용자를 추가하는 방법을 설명합니다.

## <a name="actions-that-can-be-performed-in-each-role"></a>각 역할에서 수행할 수 있는 작업
사용자를 할당할 수 있는 세 가지 주요 역할이 있습니다.

* 소유자
* DevTest Lab 사용자
* 참가자

다음 표에는 이러한 각 역할의 사용자가 수행할 수 있는 작업에 대해 설명합니다.

| **이 역할의 사용자가 수행할 수 있는 작업** | **DevTest Lab 사용자** | **소유자** | **기여자** |
| --- | --- | --- | --- |
| **랩 작업** | | | |
| 랩에 사용자 추가 |예 |예 |예 |
| 비용 설정 업데이트 |예 |예 |예 |
| **VM 기본 작업** | | | |
| 사용자 지정 이미지 추가 및 제거 |예 |예 |예 |
| 수식 추가, 업데이트 및 삭제 |예 |예 |예 |
| 마켓플레이스 이미지를 사용하도록 설정 |예 |예 |예 |
| **VM 작업** | | | |
| VM 만들기 |예 |예 |예 |
| VM 시작, 중지 및 삭제 |사용자가 만든 VM만 |예 |예 |
| VM 정책 업데이트 |예 |예 |예 |
| VM에 데이터 디스크 추가/VM에서 데이터 디스크 제거 |사용자가 만든 VM만 |예 |예 |
| **아티팩트 작업** | | | |
| 아티팩트 리포지토리 추가 및 제거 |예 |예 |예 |
| 아티팩트 적용 |예 |예 |예 |

> [!NOTE]
> 사용자가 VM을 만들면 해당 사용자는 만든 VM의 **소유자** 역할에 자동으로 할당됩니다.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>랩 수준에서 소유자 또는 사용자 추가
Azure Portal을 통해 랩 수준에서 소유자 및 사용자를 추가할 수 있습니다. 사용자는 유효한 [MSA(Microsoft 계정)](./devtest-lab-faq.yml)를 가진 외부 사용자일 수 있습니다.
다음 단계는 Azure DevTest Labs에서 랩에 소유자 또는 사용자를 추가하는 과정을 안내합니다.

1. [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../role-based-access-control/built-in-roles.md#owner)권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. 원하는 리소스 그룹을 열고 **DevTest Labs** 를 선택 합니다.

1. 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택합니다.

    ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **역할** 탭에서 **소유자** 또는 **사용자** 역할을 선택 합니다.

    ![역할 탭이 선택된 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **구성원** 탭에서 원하는 역할을 지정할 사용자를 선택 합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.


## <a name="add-an-external-user-to-a-lab-using-powershell"></a>PowerShell을 사용하여 랩에 외부 사용자 추가

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure Portal에 사용자를 추가하는 것 외에도 PowerShell 스크립트를 사용하여 랩에 외부 사용자를 추가할 수 있습니다. 다음 예제에서 **변경할 값** 설명 아래 매개 변수 값을 수정하세요.
Azure Portal의 랩 블레이드에서 `subscriptionId`, `labResourceGroup` 및 `labName` 값을 검색할 수 있습니다.

> [!NOTE]
> 샘플 스크립트는 지정된 사용자가 Active Directory에 게스트로 추가된 것으로 가정하고 사례가 없는 경우 실패합니다. Active Directory에 없는 사용자를 랩에 추가하려면 [랩 수준에서 소유자 또는 사용자 추가](#add-an-owner-or-user-at-the-lab-level)섹션에 설명된 대로 Azure Portal을 사용하여 사용자를 역할에 할당합니다.   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>구독 수준에서 소유자 또는 사용자 추가
Azure 권한은 Azure의 부모 범위에서 자식 범위로 전파됩니다. 따라서 랩을 포함하는 Azure 구독 소유자는 자동으로 해당 랩의 소유자입니다. 랩의 사용자가 만든 다른 리소스와 VM 및 Azure DevTest Labs 서비스의 소유자이기도 합니다. 

[Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040)에서 랩의 블레이드를 통해 랩에 소유자를 더 추가할 수 있습니다. 그러나 추가된 소유자의 관리 범위는 구독 소유자 범위보다 더 좁습니다. 예를 들어 추가된 소유자는 DevTest Labs 서비스에서 구독에 만든 일부 리소스에 대해서는 모든 권한이 없습니다. 

Azure 구독에 소유자를 추가하려면 다음 단계를 수행합니다.

1. [사용자 액세스 관리자](../role-based-access-control/built-in-roles.md#user-access-administrator) 또는 [소유자](../role-based-access-control/built-in-roles.md#owner)권한으로 [Azure Portal](https://portal.azure.com) 에 로그인 합니다.

1. 원하는 구독 그룹을 엽니다.

1. 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가** 를 선택합니다.

    ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **역할** 탭에서 **소유자** 역할을 선택 합니다.

    ![역할 탭이 선택된 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **구성원** 탭에서 소유자 역할에 지정할 사용자를 선택 합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
