---
title: 구독을 관리 그룹으로 구성하고 Microsoft Defender for Cloud에 대한 사용자에게 역할 할당
description: Azure 구독을 Microsoft Defender for Cloud의 관리 그룹으로 구성하고 조직의 사용자에게 역할을 할당하는 방법을 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 06/11/2021
ms.custom: subject-rbac-steps
ms.author: memildin
ms.openlocfilehash: ffb6e78ffd0b4214f0751686dba695e8d35e3160
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131425124"
---
# <a name="organize-subscriptions-into-management-groups-and-assign-roles-to-users"></a>구독을 관리 그룹으로 구성하고 사용자에게 역할 할당

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 페이지에서는 Azure Active Directory 테넌트에 연결된 모든 Azure 구독에 보안 정책을 적용하여 조직의 보안 상태를 대규모로 관리하는 방법을 설명합니다.

Azure AD 테넌트에 연결된 모든 구독의 보안 상태로 가시성을 달성하려면 루트 관리 그룹에 할당된 읽기 권한이 충분히 있는 Azure 역할이 필요합니다.

## <a name="organize-your-subscriptions-into-management-groups"></a>관리 그룹으로 구독 구성

### <a name="introduction-to-management-groups"></a>관리 그룹 소개

관리 그룹은 액세스, 정책 및 **구독 그룹** 에 대한 보고를 효율적으로 관리하는 기능을 제공하고, 루트 관리 그룹에서 작업을 수행하여 전체 Azure 공간을 효율적으로 관리합니다. 관리 그룹에 구독을 구성하고 거버넌스 정책을 관리 그룹에 적용합니다. 관리 그룹에 속하는 모든 구독은 관리 그룹에 적용되는 정책을 자동으로 상속합니다. 

각 Azure AD 테넌트에는 **루트 관리 그룹** 이라고 하는 하나의 최상위 관리 그룹이 부여됩니다. 이 루트 관리 그룹은 모든 관리 그룹과 구독이 루트 관리 그룹까지 접히도록 만들어집니다. 이 그룹은 전역 정책 및 Azure 역할 할당을 디렉터리 수준에서 적용할 수 있습니다. 

루트 관리 그룹은 다음 작업 중 하나를 실행할 때 자동으로 만들어집니다. 
- [Azure Portal](https://portal.azure.com)에서 **관리 그룹** 을 엽니다.
- API 호출을 사용하여 관리 그룹을 만듭니다.
- PowerShell을 사용하여 관리 그룹을 만듭니다. PowerShell 지침은 [리소스 및 조직 관리를 위한 관리 그룹 만들기](../governance/management-groups/create-management-group-portal.md)를 참조하세요.

관리 그룹은 Defender for Cloud를 온보딩할 필요가 없지만 루트 관리 그룹이 생성되도록 하나 이상을 만드는 것이 좋습니다. 그룹이 만들어진 후 Azure AD 테넌트 아래의 모든 하위 구독이 연결됩니다. 

관리 그룹의 자세한 개요는 [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md) 문서를 참조하세요.

### <a name="view-and-create-management-groups-in-the-azure-portal"></a>Azure Portal에서 관리 그룹 보기 및 만들기

1. [Azure Portal](https://portal.azure.com)에서 위쪽 표시줄의 검색 상자를 사용하여 **관리 그룹** 을 찾아 엽니다.

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="관리 그룹에 액세스.":::

    관리 그룹의 목록이 표시됩니다.

1. 관리 그룹을 만들려면 **관리 그룹 추가** 를 선택하고 관련 세부 정보를 입력한 다음, **저장** 을 선택합니다.

    :::image type="content" source="media/management-groups-roles/add-management-group.png" alt-text="Azure에 관리 그룹 추가.":::

    - **관리 그룹 ID** 는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에 편집할 수 없습니다. 
    - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도 표시 이름은 관리 그룹을 만들 때 사용되는 선택적 필드로, 언제든지 변경할 수 있습니다.  


### <a name="add-subscriptions-to-a-management-group"></a>관리 그룹에 구독 추가
만든 관리 그룹에 구독을 추가할 수 있습니다.

1. Azure Portal에서 **관리 그룹** 을 열고 구독에 대한 관리 그룹을 선택합니다.

    :::image type="content" source="./media/management-groups-roles/management-group-subscriptions.png" alt-text="구독에 대한 관리 그룹을 선택.":::

1. 그룹 페이지가 열리면 **구독** 을 선택합니다.

1. 구독 페이지에서 **추가** 를 선택한 다음, 구독과 **저장** 을 차례로 선택합니다. 범위에 모든 구독을 추가할 때까지 반복합니다.

    :::image type="content" source="./media/management-groups-roles/management-group-add-subscriptions.png" alt-text="관리 그룹에 구독 추가":::

   > [!IMPORTANT]
   > 관리 그룹은 구독 및 자식 관리 그룹 모두를 포함할 수 있습니다. 사용자에게 부모 관리 그룹에 대한 Azure 역할을 할당하면 자식 관리 그룹의 구독에서 액세스 권한을 상속합니다. 부모 관리 그룹에서 설정된 정책도 자식에서 상속됩니다. 



## <a name="assign-azure-roles-to-other-users"></a>다른 사용자에게 Azure 역할 할당

### <a name="assign-azure-roles-to-users-through-the-azure-portal"></a>Azure Portal을 통해 사용자에게 Azure 역할을 할당합니다. 

1. [Azure Portal](https://portal.azure.com)에서 위쪽 표시줄의 검색 상자를 사용하여 **관리 그룹** 을 찾아 엽니다.

    :::image type="content" source="./media/management-groups-roles/open-management-groups-service.png" alt-text="관리 그룹에 액세스.":::

    관리 그룹의 목록이 표시됩니다.

1.  관련된 관리 그룹을 선택합니다.

1. **액세스 제어(IAM)** 를 선택하고 **역할 할당** 탭을 연 다음, **추가** > **역할 할당 추가** 를 선택합니다.

    :::image type="content" source="./media/management-groups-roles/add-user.png" alt-text="관리 그룹에 사용자 추가.":::

1. **역할 할당 추가** 페이지에서 관련 역할을 선택합니다.

    :::image type="content" source="./media/management-groups-roles/add-role-assignment-page.png" alt-text="역할 할당 추가 페이지.":::

1. **구성원** 탭에서 **+ 구성원 선택** 을 선택하고 관련 구성원에게 역할을 할당합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.


### <a name="assign-azure-roles-to-users-with-powershell"></a>PowerShell로 사용자에게 Azure 역할 할당: 

1. [Azure PowerShell](/powershell/azure/install-az-ps)을 설치합니다.
2. 다음 명령을 실행합니다. 

    ```azurepowershell
    # Login to Azure as a Global Administrator user
    Connect-AzAccount
    ```

3. 메시지가 표시되면 전역 관리자 자격 증명으로 로그인합니다. 

    ![로그인 프롬프트 스크린샷.](./media/management-groups-roles/azurerm-sign-in.PNG)

4. 다음 명령을 실행하여 reader 역할 권한을 부여합니다.

    ```azurepowershell
    # Add Reader role to the required user on the Root Management Group
    # Replace "user@domian.com” with the user to grant access to
    New-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/"
    ```
5. 역할을 제거하려면 다음 명령을 사용합니다. 

    ```azurepowershell
    Remove-AzRoleAssignment -SignInName "user@domain.com" -RoleDefinitionName "Reader" -Scope "/" 
    ```

## <a name="remove-elevated-access"></a>상승된 액세스 제거 

Azure 역할이 사용자에게 할당되면 테넌트 관리자는 사용자 액세스 관리자 역할에서 스스로를 제거해야 합니다.

1. [Azure Portal](https://portal.azure.com) 또는 [Azure Active Directory 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

2. 탐색 목록에서 **Azure Active Directory** 를 선택한 다음, **속성** 을 선택합니다.

3. **Azure 리소스에 대한 액세스 관리** 에서 스위치를 **아니요** 로 설정합니다.

4. 설정을 저장 하려면 **저장** 을 선택합니다.



## <a name="next-steps"></a>다음 단계

이 페이지에서는 구독을 관리 그룹으로 구성하고 사용자에게 역할을 할당하는 방법을 알아보았습니다. 관련 정보는 다음을 참조하세요.

- [클라우드용 Microsoft Defender의 권한](permissions.md)