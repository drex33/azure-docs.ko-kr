---
title: Azure Portal을 사용하여 외부 게스트 사용자에게 Azure 역할 할당 - Azure RBAC
description: Azure Portal 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 조직 외부의 사용자에게 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: how-to
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 06/28/2021
ms.author: rolyon
ms.custom: it-pro,subject-rbac-steps
ms.openlocfilehash: 175beacc486c4b59919bf20300bbd06f7b9aa1a7
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988501"
---
# <a name="assign-azure-roles-to-external-guest-users-using-the-azure-portal"></a>Azure Portal을 사용하여 외부 게스트 사용자에게 Azure 역할 할당

[Azure RBAC(Azure 역할 기반 액세스 제어)](overview.md)를 사용하면 환경의 특정 리소스에 액세스해야 하지만 전체 인프라 또는 청구 관련 범위에 액세스하지 않아도 되는 외부 공동 작업자, 공급업체 또는 프리랜서와 협력하는 대기업 및 중소기업의 보안을 더 효율적으로 관리할 수 있습니다. [Azure Active Directory B2B](../active-directory/external-identities/what-is-b2b.md)의 기능을 사용하여 외부 게스트 사용자와 협업할 수 있으며, Azure RBAC를 사용하여 사용자 환경에서 게스트 사용자에게 필요한 권한만 부여할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

Azure 역할을 할당하거나 역할 할당을 제거하려면 다음이 필요합니다.

- `Microsoft.Authorization/roleAssignments/write` 및 `Microsoft.Authorization/roleAssignments/delete` 사용 권한(예: [사용자 액세스 관리자](built-in-roles.md#user-access-administrator) 또는 [소유자](built-in-roles.md#owner))

## <a name="when-would-you-invite-guest-users"></a>언제 게스트 사용자를 초대하나요?

다음은 게스트 사용자를 조직에 초대하고 권한을 부여하는 몇 가지 예제 시나리오입니다.

- 이메일 계정만 있는 외부 자영업자가 프로젝트에 대한 Azure 리소스에 액세스할 수 있도록 허용합니다.
- 외부 파트너가 특정 리소스 또는 전체 구독을 관리할 수 있도록 허용합니다.
- 고객 조직에 속하지 않은 지원 엔지니어(예: Microsoft 지원)가 문제 해결을 위해 일시적으로 고객의 Azure 리소스에 액세스할 수 있도록 허용합니다.

## <a name="permission-differences-between-member-users-and-guest-users"></a>멤버 사용자와 게스트 사용자의 권한 차이

디렉터리의 네이티브 멤버(멤버 사용자)는 다른 디렉터리에서 B2B 협업 게스트(게스트 사용자)로 초대된 사용자와 다른 권한을 갖습니다. 예를 들어 멤버 사용자는 거의 모든 디렉터리 정보를 읽을 수 있지만 게스트 사용자는 제한된 디렉터리 권한을 가집니다. 멤버 사용자와 게스트 사용자에 대한 자세한 내용은 [Azure Active Directory의 기본 사용자 권한이란?](../active-directory/fundamentals/users-default-permissions.md)을 참조하세요.

## <a name="add-a-guest-user-to-your-directory"></a>디렉터리에 게스트 사용자 추가

Azure Active Directory 페이지를 사용하여 디렉터리에 게스트 사용자를 추가하려면 다음 단계를 따르세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 게스트를 초대할 수 있도록 조직의 외부 협업 설정이 구성되어 있는지 확인합니다. 자세한 내용은 [B2B 외부 협업을 사용하도록 설정 및 게스트를 초대할 수 있는 사용자 관리](../active-directory/external-identities/delegate-invitations.md)를 참조하세요.

1. **Azure Active Directory** > **사용자** > **새 게스트 사용자** 를 클릭합니다.

    ![Azure Portal의 새 게스트 사용자 기능 스크린샷](./media/role-assignments-external-users/invite-guest-user.png)

1. 세 게스트 사용자를 추가하려면 다음 단계를 따르세요. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../active-directory/external-identities/add-users-administrator.md#add-guest-users-to-the-directory)를 참조하세요.

게스트 사용자를 디렉터리에 추가한 후에 게스트 사용자에게 공유 앱에 대한 직접 링크를 보낼 수 있습니다. 또는 게스트 사용자는 초대 메일에서 초대 수락 링크를 클릭할 수 있습니다.

![게스트 사용자 초대 메일의 스크린샷](./media/role-assignments-external-users/invite-email.png)

게스트 사용자가 디렉터리에 액세스하려면 초대 프로세스를 완료해야 합니다.

![게스트 사용자 초대 검토 권한의 스크린샷](./media/role-assignments-external-users/invite-review-permissions.png)

초대 프로세스에 대한 자세한 내용은 [Azure Active Directory B2B 협업 초대 사용](../active-directory/external-identities/redemption-experience.md)을 참조하세요.

## <a name="assign-a-role-to-a-guest-user"></a>게스트 사용자에게 역할 할당

Azure RBAC에서 액세스 권한을 부여하려면 역할을 할당합니다. 게스트 사용자에게 역할을 할당하려면 멤버 사용자, 그룹, 서비스 주체 또는 관리 ID와 [동일한 단계](role-assignments-portal.md)를 수행합니다. 다음 단계에 따라 여러 범위의 게스트 사용자에게 역할을 할당합니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽의 검색 상자에서 액세스 권한을 부여할 범위를 검색합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 특정 리소스를 검색합니다.

1. 해당 범위에 대한 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

    ![미리 보기 환경의 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 스크린샷](./media/shared/rg-access-control.png)

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 클릭합니다.

    역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

    ![미리 보기 환경에 대한 추가 > 역할 할당 추가 메뉴의 스크린샷](./media/shared/add-role-assignment-menu-preview.png)

    역할 할당 추가 페이지가 열립니다.

1. **역할** 탭에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

   ![미리 보기 환경을 위한 역할 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/roles.png)

1. **멤버** 탭에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.

   ![미리 보기 환경을 위한 멤버 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/members.png)

1. **멤버 선택** 을 클릭합니다.

1. 게스트 사용자를 찾고 선택합니다. 목록에 사용자가 표시되지 않으면 **선택** 상자에 입력하여 디렉터리에서 표시 이름이나 메일 주소를 검색할 수 있습니다.

    **선택** 상자에서 입력하여 디렉터리에서 표시 이름이나 메일 주소를 검색할 수 있습니다.

    ![미리 보기 환경의 멤버 선택 창 스크린샷](./media/role-assignments-external-users/select-members.png)

1. **선택** 을 클릭하여 게스트 사용자를 멤버 목록에 추가합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 클릭합니다.

    잠시 후에 게스트 사용자에게 선택한 범위의 역할이 할당됩니다.

    ![Virtual Machine 기여자의 역할 할당 스크린샷](./media/role-assignments-external-users/access-control-role-assignments.png)

## <a name="assign-a-role-to-a-guest-user-not-yet-in-your-directory"></a>디렉터리에 아직 없는 게스트 사용자에게 역할 할당

게스트 사용자에게 역할을 할당하려면 멤버 사용자, 그룹, 서비스 주체 또는 관리 ID와 [동일한 단계](role-assignments-portal.md)를 수행합니다.

게스트 사용자가 아직 디렉터리에 없는 경우 멤버 선택 창에서 직접 사용자를 초대할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽의 검색 상자에서 액세스 권한을 부여할 범위를 검색합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 특정 리소스를 검색합니다.

1. 해당 범위에 대한 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 클릭합니다.

    역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

    ![미리 보기 환경에 대한 추가 > 역할 할당 추가 메뉴의 스크린샷](./media/shared/add-role-assignment-menu-preview.png)

    역할 할당 추가 페이지가 열립니다.

1. **역할** 탭에서 **Virtual Machine 기여자** 같은 역할을 선택합니다.

1. **멤버** 탭에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.

   ![미리 보기 환경을 위한 멤버 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/members.png)

1. **멤버 선택** 을 클릭합니다.

1. **선택** 상자에 초대하려는 사용자의 메일 주소를 입력하고 해당 사용자를 선택합니다.

    ![멤버 선택 창의 게스트 사용자 초대 스크린샷](./media/role-assignments-external-users/select-members-new-guest.png)

1. **선택** 을 클릭하여 게스트 사용자를 멤버 목록에 추가합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 클릭하여 게스트 사용자를 디렉터리에 추가하고, 역할을 할당하고, 초대를 보냅니다.

    잠시 후 역할 할당에 대한 알림과 초대에 대한 정보를 볼 수 있습니다.

    ![역할 할당 및 초대된 사용자 알림의 스크린샷](./media/role-assignments-external-users/invited-user-notification.png)

1. 게스트 사용자를 수동으로 초대하려면 알림에서 초대 링크를 마우스 오른쪽 단추로 클릭하고 복사합니다. (초대 링크는 클릭하지 마세요. 그러면 초대 프로세스가 시작됩니다.)

    초대 링크의 형식은 다음과 같습니다.

    `https://login.microsoftonline.com/redeem?rd=https%3a%2f%2finvitations.microsoft.com%2fredeem%2f%3ftenant%3d0000...`

1. 게스트 사용자에게 초대 링크를 보내 초대 프로세스를 완료합니다.

    초대 프로세스에 대한 자세한 내용은 [Azure Active Directory B2B 협업 초대 사용](../active-directory/external-identities/redemption-experience.md)을 참조하세요.

## <a name="remove-a-guest-user-from-your-directory"></a>디렉터리에서 게스트 사용자 제거

디렉터리에서 게스트 사용자를 제거하기 전에 먼저 해당 게스트 사용자에 대한 역할 할당을 제거해야 합니다. 게스트 사용자를 디렉터리에서 제거하려면 다음 단계를 따르세요.

1. 관리 그룹, 구독, 리소스 그룹 또는 리소스 등, 게스트 사용자에게 역할이 할당된 범위의 **액세스 제어(IAM)** 를 엽니다.

1. **역할 할당** 탭을 클릭하여 모든 역할 할당을 봅니다.

1. 역할 할당 목록에서 제거할 역할 할당이 있는 게스트 사용자 옆에 확인 표시를 추가합니다.

   ![제거할 선택된 역할 할당의 스크린샷](./media/role-assignments-external-users/remove-role-assignment-select.png)

1. **제거** 를 클릭합니다.

   ![역할 할당 제거 메시지의 스크린샷](./media/shared/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예** 를 클릭합니다.

1. 왼쪽 탐색 모음에서 **Azure Active Directory** > **사용자** 를 클릭합니다.

1. 제거하려는 게스트 사용자를 클릭합니다.

1. **삭제** 를 클릭합니다.

   ![게스트 사용자를 삭제하는 스크린샷](./media/role-assignments-external-users/delete-guest-user.png)

1. 표시되는 메시지 삭제에서 **예** 를 클릭합니다.

## <a name="troubleshoot"></a>문제 해결

### <a name="guest-user-cannot-browse-the-directory"></a>게스트 사용자가 디렉터리를 탐색할 수 없음

게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 예를 들어 게스트 사용자가 디렉터리를 탐색할 수 없으며 그룹 또는 애플리케이션을 검색할 수 없습니다. 자세한 내용은 [Azure Active Directory의 기본 사용자 권한이란?](../active-directory/fundamentals/users-default-permissions.md)을 참조하세요.

![게스트 사용자가 디렉터리에서 사용자를 찾아볼 수 없음을 보여 주는 스크린샷](./media/role-assignments-external-users/directory-no-users.png)

게스트 사용자에게 디렉터리의 추가 권한이 필요한 경우 게스트 사용자에게 Azure AD 역할을 할당할 수 있습니다. 게스트 사용자에게 디렉터리에 대한 전체 읽기 액세스 권한을 할당하려면 Azure AD의 [디렉터리 읽기 권한자](../active-directory/roles/permissions-reference.md#directory-readers) 역할에 게스트 사용자를 추가할 수 있습니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../active-directory/external-identities/add-users-administrator.md)를 참조하세요.

![디렉터리 읽기 권한자 역할 할당의 스크린샷](./media/role-assignments-external-users/directory-roles.png)

### <a name="guest-user-cannot-browse-users-groups-or-service-principals-to-assign-roles"></a>게스트 사용자가 역할을 할당할 사용자, 그룹 또는 서비스 주체를 탐색할 수 없음

게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 게스트 사용자가 특정 범위의 [소유자](built-in-roles.md#owner)인 경우에도 다른 사용자에게 액세스 권한을 부여하는 역할을 할당하려고 하면 사용자, 그룹 또는 서비스 주체 목록을 탐색할 수 없습니다.

![게스트 사용자가 역할을 할당할 보안 주체를 찾아볼 수 없음을 보여 주는 스크린샷](./media/role-assignments-external-users/directory-no-browse.png)

게스트 사용자가 디렉터리에서 사용자의 정확한 로그인 이름을 알고 있는 경우 액세스 권한을 부여할 수 있습니다. 게스트 사용자에게 디렉터리에 대한 전체 읽기 액세스 권한을 할당하려면 Azure AD의 [디렉터리 읽기 권한자](../active-directory/roles/permissions-reference.md#directory-readers) 역할에 게스트 사용자를 추가할 수 있습니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../active-directory/external-identities/add-users-administrator.md)를 참조하세요.

### <a name="guest-user-cannot-register-applications-or-create-service-principals"></a>게스트 사용자가 애플리케이션을 등록하거나 서비스 주체를 만들 수 없음

게스트 사용자에게는 제한된 디렉터리 권한이 있습니다. 게스트 사용자가 애플리케이션을 등록하거나 서비스 주체를 만들어야 하는 경우, Azure AD의 [애플리케이션 개발자](../active-directory/roles/permissions-reference.md#application-developer) 역할에 게스트 사용자를 추가할 수 있습니다. 자세한 내용은 [Azure Portal에서 Azure Active Directory B2B 협업 사용자 추가](../active-directory/external-identities/add-users-administrator.md)를 참조하세요.

![게스트 사용자가 애플리케이션을 등록할 수 없음을 보여 주는 스크린샷](./media/role-assignments-external-users/directory-access-denied.png)

### <a name="guest-user-does-not-see-the-new-directory"></a>게스트 사용자에게 새 디렉터리가 표시되지 않음

게스트 사용자에게 디렉터리에 대한 액세스 권한이 부여되었지만 **디렉터리** 페이지에서 전환하려고 할 때 Azure Portal에 나열된 새 디렉터리가 표시되지 않는 경우 게스트 사용자가 초대 프로세스를 완료했는지 확인합니다. 초대 프로세스에 대한 자세한 내용은 [Azure Active Directory B2B 협업 초대 사용](../active-directory/external-identities/redemption-experience.md)을 참조하세요.

### <a name="guest-user-does-not-see-resources"></a>게스트 사용자에게 리소스가 표시되지 않음

게스트 사용자에게 디렉터리에 대한 액세스 권한이 부여되었지만 Azure Portal에서 액세스 권한이 있는 리소스가 보이지 않을 경우 게스트 사용자가 올바른 디렉터리를 선택했는지 확인합니다. 게스트 사용자는 여러 디렉터리에 액세스할 수 있습니다. 디렉터리를 전환하려면 왼쪽 위에서 **설정** > **디렉터리** 를 클릭한 다음, 적절한 디렉터리를 클릭합니다.

![Azure Portal의 포털 설정 디렉터리 섹션 스크린샷](./media/role-assignments-external-users/directory-switch.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal에서 Azure Active Directory B2B Collaboration 사용자 추가](../active-directory/external-identities/add-users-administrator.md)
- [Azure Active Directory B2B 협업 사용자 속성](../active-directory/external-identities/user-properties.md)
- [Azure Active Directory B2B 협업 초대 이메일의 요소](../active-directory/external-identities/invitation-email-elements.md)
- [게스트 사용자를 공동 관리자로 추가](classic-administrators.md#add-a-guest-user-as-a-co-administrator)