---
title: Azure Lab Services의 랩에 소유자를 추가하는 방법
description: 이 문서에서는 관리자가 Azure Lab Services의 랩에 사용자를 소유자로 추가하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 08/03/2021
ms.custom: subject-rbac-steps
ms.openlocfilehash: 531910013284abbddd73c2247b5f65494a74ddf1
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130181011"
---
# <a name="how-to-add-additional-owners-to-an-existing-lab-in-azure-lab-services"></a>Azure Lab Services의 기존 랩에 소유자를 추가하는 방법
이 문서에서는 관리자가 기존 랩에 소유자를 추가하는 방법을 보여 줍니다.

## <a name="add-user-to-the-reader-role-for-the-lab-account"></a>랩 계정의 읽기 권한자 역할에 사용자 추가
1. **랩 계정** 페이지로 돌아가서 왼쪽 메뉴에서 **모든 랩** 을 선택합니다.
2. 사용자를 소유자로 추가할 **랩** 을 선택합니다. 

    ![랩 선택 ](./media/how-to-add-user-lab-owner/select-lab.png)  
1. 탐색 메뉴에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 선택합니다.

    ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **역할** 탭에서 **읽기 권한자** 역할을 선택합니다.

    ![역할 탭이 선택된 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **구성원** 탭에서 읽기 권한자 역할에 추가할 사용자를 선택합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.
## <a name="add-user-to-the-owner-role-for-the-lab"></a>랩의 소유자 역할에 사용자 추가

> [!NOTE]
> 사용자에게 랩에 대한 읽기 권한자 액세스 권한만 있는 경우 랩은 labs.azure.com에 표시되지 않습니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.


1. **랩 계정 페이지** 에서 **액세스 제어(IAM)** 를 선택합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 선택합니다.

    ![역할 할당 추가 메뉴가 열려 있는 액세스 제어(IAM) 페이지.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. **역할** 탭에서 **소유자** 역할을 선택합니다.

    ![역할 탭이 선택된 역할 할당 페이지를 추가합니다.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. **구성원** 탭에서 소유자 역할에 추가할 사용자를 선택합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 선택하여 역할을 할당합니다.


## <a name="next-steps"></a>다음 단계
사용자가 [랩 서비스 포털](https://labs.azure.com)에 로그인할 때 랩이 표시되는지 확인합니다.
