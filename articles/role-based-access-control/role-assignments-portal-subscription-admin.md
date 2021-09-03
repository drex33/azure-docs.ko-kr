---
title: Azure 구독의 관리자로 사용자 할당 - Azure RBAC
description: Azure Portal 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자를 Azure 구독 관리자로 만드는 방법 알아보기.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 06/25/2021
ms.author: rolyon
ms.custom: subject-rbac-steps
ms.openlocfilehash: af88edbb788afa3576d3317d4e1cd54dea6f4deb
ms.sourcegitcommit: 1c12bbaba1842214c6578d914fa758f521d7d485
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/28/2021
ms.locfileid: "112988070"
---
# <a name="assign-a-user-as-an-administrator-of-an-azure-subscription"></a>Azure 구독의 관리자로 사용자 할당

특정 사용자를 Azure 구독의 관리자로 지정하려면 해당 사용자에게 구독 범위에서 [소유자](built-in-roles.md#owner) 역할을 할당합니다. Owner 역할은 다른 사용자에게 액세스 권한을 부여할 수 있는 권한을 포함하여 구독에 있는 모든 리소스에 대한 모든 액세스 권한을 사용자에게 부여합니다. 이러한 단계는 다른 역할 할당과 동일합니다.

## <a name="prerequisites"></a>사전 요구 사항

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="step-1-open-the-subscription"></a>1단계: 구독 열기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 최상단의 검색 상자에서 구독을 검색합니다.

1. 사용할 구독을 클릭합니다.

    다음은 예제 구독입니다.

    ![구독 개요의 스크린샷](./media/shared/sub-overview.png)

## <a name="step-2-open-the-add-role-assignment-page"></a>2단계: 역할 할당 추가 페이지 열기

**IAM**(액세스 제어)은 Azure 리소스에 대한 액세스 권한을 부여하기 위해 역할을 할당하는 데 일반적으로 사용하는 페이지입니다. ID 및 IAM이라고도 하며 Azure Portal의 여러 위치에 표시됩니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음은 구독에 대한 IAM 페이지의 예를 보여 줍니다.

    ![구독에 대한 액세스 제어(IAM) 페이지의 스크린샷](./media/shared/sub-access-control.png)

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 클릭합니다.

   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

    ![미리 보기 환경에 대한 추가 > 역할 할당 추가 메뉴의 스크린샷](./media/shared/add-role-assignment-menu-preview.png)

    역할 할당 추가 페이지가 열립니다.

## <a name="step-3-select-the-owner-role"></a>3단계: Owner 역할 선택

[Owner](built-in-roles.md#owner) 역할은 Azure RBAC에서 역할을 할당하는 기능을 포함하여 모든 리소스를 관리하기 위한 모든 권한을 부여합니다. 손상된 소유자에 의한 위반 가능성을 줄이려면 최대 3개의 구독 소유자가 있어야 합니다.

1. **역할** 탭에서 **소유자** 역할을 선택합니다.

    역할은 이름 또는 설명을 기준으로 검색할 수 있습니다. 또한 역할은 유형 및 범주를 기준으로 필터링할 수도 있습니다.

   ![미리 보기 환경을 위한 역할 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/roles.png)

1. **다음** 을 클릭합니다.

## <a name="step-4-select-who-needs-access"></a>4단계: 액세스 이용자 선택

1. **멤버** 탭에서 **사용자, 그룹 또는 서비스 주체** 를 선택합니다.

   ![미리 보기 환경에 대한 멤버 추가 탭이 있는 역할 할당 추가 페이지의 스크린샷.](./media/shared/members.png)

1. **멤버 선택** 을 클릭합니다.

1. 사용자를 찾고 선택합니다.

    **선택** 상자에서 입력하여 디렉터리에서 표시 이름이나 메일 주소를 검색할 수 있습니다.

   ![미리 보기 환경의 멤버 선택 창 스크린샷](./media/shared/select-members.png)

1. **저장** 을 클릭하여 사용자를 멤버 목록에 추가합니다.

1. **설명** 상자에서 이 역할 할당에 대한 설명을 입력합니다(선택 사항).

    이 설명은 나중에 역할 할당 목록에 표시될 수 있습니다.

1. **다음** 을 클릭합니다.

## <a name="step-5-assign-role"></a>5단계: 역할 할당

1. **검토 + 할당** 탭에서 역할 할당 설정을 검토합니다.

1. **검토 + 할당** 을 클릭하여 역할을 할당합니다.

   몇 분 후에 사용자에게 구독의 소유자 역할이 할당됩니다.

    ![미리 보기 환경에 대한 역할을 할당한 후의 역할 할당 목록에 대한 스크린샷](./media/role-assignments-portal-subscription-admin/sub-role-assignments-owner.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)
- [Azure Portal을 사용하여 Azure 역할 할당을 나열](role-assignments-list-portal.md)합니다.
- [Azure 관리 그룹으로 리소스 구성](../governance/management-groups/overview.md)
