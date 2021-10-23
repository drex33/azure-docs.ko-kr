---
title: '자습서: Azure Portal을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여 - Azure RBAC'
description: 이 자습서에서는 Azure Portal 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 Azure 리소스에 대한 사용자 액세스 권한을 부여하는 방법을 알아봅니다.
services: role-based-access-control
documentationCenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: role-based-access-control
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/15/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: 96064811bf0d82ab37a338adf3ed2ac4568c57f1
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130069175"
---
# <a name="tutorial-grant-a-user-access-to-azure-resources-using-the-azure-portal"></a>자습서: Azure Portal을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여

[Azure RBAC(Azure 역할 기반 액세스 제어)](overview.md)는 Azure 리소스에 대한 액세스를 관리하는 방법입니다. 이 자습서에서는 리소스 그룹에서 가상 머신을 만들고 관리할 수 있는 액세스 권한을 사용자에게 부여합니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> * 리소스 그룹 범위에 속한 사용자에게 액세스 권한 부여
> * 액세스 권한 제거

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[https://portal.azure.com](https://portal.azure.com ) 에서 Azure Portal에 로그인합니다.

## <a name="create-a-resource-group"></a>리소스 그룹 만들기

1. 탐색 목록에서 **리소스 그룹** 을 클릭합니다.

1. **새로 만들기** 를 클릭하여 **리소스 그룹 만들기** 페이지를 엽니다.

   ![새 리소스 그룹 페이지를 만듭니다.](./media/quickstart-assign-role-user-portal/resource-group.png)

1. 구독을 선택합니다.

1. **리소스 그룹** 이름에 **example-group** 또는 다른 이름을 입력합니다.

1. **검토 + 만들기** 를 클릭한 다음, **만들기** 를 클릭하여 리소스 그룹을 만듭니다.

1. **새로 고침** 을 클릭하여 리소스 그룹 목록을 새로 고칩니다.

   새 리소스 그룹이 리소스 그룹 목록에 나타납니다.

## <a name="grant-access"></a>액세스 권한 부여

Azure RBAC에서 액세스 권한을 부여하기 위해 Azure 역할을 할당합니다.

1. **리소스 그룹** 목록에서 새 **example-group** 리소스 그룹을 엽니다.

1. 탐색 메뉴에서 **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 현재의 역할 할당 목록을 봅니다.

   ![리소스 그룹에 대한 액세스 제어(IAM) 페이지.](./media/shared/rg-role-assignments.png)


1. **추가** > **역할 할당 추가** 를 클릭합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **역할** 탭에서 **Virtual Machine 기여자** 역할을 선택합니다.

    ![역할 탭이 선택된 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **멤버** 탭에서 자신 또는 다른 사용자를 선택합니다.

1. **검토 + 할당** 탭에서 역할 할당 설정을 검토합니다.

1. **검토 + 할당** 을 클릭하여 역할을 할당합니다.

   몇 분 후에 example-group 리소스 그룹 범위에 속한 사용자에게 Virtual Machine 기여자 역할이 할당됩니다.

   ![Virtual Machine 기여자 역할 할당.](./media/quickstart-assign-role-user-portal/vm-contributor-assignment.png)

## <a name="remove-access"></a>액세스 권한 제거

Azure RBAC에서 액세스 권한을 제거하려면 역할 할당을 제거해야 합니다.

1. 역할 할당 목록에서 Virtual Machine 기여자 역할이 있는 사용자 옆에 확인 표시를 추가합니다.

1. **제거** 를 클릭합니다.

   ![역할 할당 메시지를 제거합니다.](./media/quickstart-assign-role-user-portal/remove-role-assignment.png)

1. 표시되는 역할 할당 제거 메시지에서 **예** 를 클릭합니다.

## <a name="clean-up"></a>정리

1. 탐색 목록에서 **리소스 그룹** 을 클릭합니다.

1. **example-group** 을 클릭하여 리소스 그룹을 엽니다.

1. **리소스 그룹 삭제** 를 클릭하여 리소스 그룹을 삭제합니다.

1. **삭제하시겠습니까?** 창에서 리소스 그룹 이름을 입력한 다음, **삭제** 를 클릭합니다.

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [자습서: Azure PowerShell을 사용하여 Azure 리소스에 대한 사용자 액세스 권한 부여](tutorial-role-assignments-user-powershell.md)
