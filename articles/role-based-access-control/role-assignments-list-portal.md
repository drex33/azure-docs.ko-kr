---
title: Azure Portal을 사용하여 Azure 역할 할당 나열 - Azure RBAC
description: Azure Portal 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 액세스할 수 있는 사용자, 그룹, 서비스 주체 또는 관리 ID를 확인하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2021
ms.author: rolyon
ms.openlocfilehash: 667709d8f924556c43a741151c55f5469fb92639
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399962"
---
# <a name="list-azure-role-assignments-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당을 나열합니다.

[!INCLUDE [Azure RBAC definition list access](../../includes/role-based-access-control/definition-list.md)] 이 문서에서는 Azure Portal로 역할 할당을 나열하는 방법에 관해 설명합니다.

> [!NOTE]
> 조직에서 [Azure Lighthouse](../lighthouse/overview.md)를 사용하는 서비스 공급자에 대해 아웃소싱된 관리 기능을 사용하는 경우 해당 서비스 공급자가 승인한 역할 할당은 여기에 표시되지 않습니다.

## <a name="list-role-assignments-for-a-user-or-group"></a>사용자 또는 그룹에 대한 역할 할당 목록

구독에서 사용자 또는 그룹에 할당된 역할을 빠르게 확인하는 방법은 **Azure 역할 할당** 창을 사용하는 것입니다.

1. Azure Portal의 Azure Portal 메뉴에서 **모든 서비스** 를 선택합니다.

1. **Azure Active Directory** 를 선택한 다음, **사용자** 또는 **그룹** 을 선택합니다.

1. 역할 할당을 나열하려는 사용자 또는 그룹을 클릭 합니다.

1. **Azure 역할 할당** 을 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같이 다양한 범위에서 선택한 사용자 또는 그룹에 할당된 역할 목록이 표시됩니다. 이 목록에는 사용자가 읽기 권한을 가진 모든 역할 할당이 포함됩니다.

    ![사용자에 대한 역할 할당](./media/role-assignments-list-portal/azure-role-assignments-user.png)    

1. 구독을 변경하려면 **구독** 목록을 클릭합니다.

## <a name="list-owners-of-a-subscription"></a>구독 소유자 목록

구독에 대한 [소유자](built-in-roles.md#owner) 역할이 할당된 사용자는 구독의 모든 항목을 관리할 수 있습니다. 다음 단계를 수행하여 구독의 소유자를 나열합니다.

1. Azure Portal에서 **모든 서비스**, **구독** 을 차례로 클릭합니다.

1. 소유자를 나열하려는 구독을 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 구독의 모든 역할 할당을 봅니다.

1. **소유자** 섹션으로 스크롤하여 이 구독의 소유자 역할이 할당된 모든 사용자를 확인합니다.

   ![구독 액세스 제어 - 역할 할당 탭](./media/role-assignments-list-portal/sub-access-control-role-assignments-owners.png)

## <a name="list-role-assignments-at-a-scope"></a>특정 범위의 역할 할당 나열

1. Azure Portal에서 **모든 서비스** 를 클릭하고 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 이 범위의 모든 역할 할당을 봅니다.

   ![액세스 제어 - 역할 할당 탭](./media/role-assignments-list-portal/rg-access-control-role-assignments.png)

   역할 할당 탭에서 이 범위에서 액세스 권한이 있는 사용자를 볼 수 있습니다. 일부 역할의 범위는 **이 리소스** 이지만 나머지는 다른 범위에서 **(상속)** 됩니다. 액세스 권한은 이 리소스에 특정적으로 할당되거나 부모 범위에 대한 할당에서 상속됩니다.

## <a name="list-role-assignments-for-a-user-at-a-scope"></a>특정 범위에서 사용자의 역할 할당 나열

사용자, 그룹, 서비스 주체 또는 관리 ID의 액세스 권한을 나열하려면 역할 할당을 나열합니다. 다음 단계에 따라 특정한 범위에서 단일 사용자, 그룹, 서비스 주체 또는 관리 ID에 대한 역할 할당을 나열합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭하고 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **액세스 권한 확인** 탭을 클릭합니다.

    ![리소스 그룹 액세스 제어 - 액세스 권한 확인 탭](./media/role-assignments-list-portal/rg-access-control-check-access.png)

1. **찾기** 목록에서 액세스를 확인할 사용자, 그룹, 서비스 주체 또는 관리 ID를 선택합니다.

1. 검색 상자에서 표시 이름, 이메일 주소 또는 개체 ID에 대한 디렉터리를 검색할 문자열을 입력합니다.

    ![액세스 권한 확인 선택 목록](./media/shared/rg-check-access-select.png)

1. 보안 주체를 클릭하여 **할당** 창을 엽니다.

    이 창의 이 범위에서 선택한 보안 주체에 대한 액세스 권한을 볼 수 있으며 이 범위로 상속됩니다. 자식 범위의 할당은 나열되지 않습니다. 다음 할당이 표시됩니다.

    - Azure RBAC를 사용하여 역할 할당을 추가했습니다.
    - Azure Blueprints 또는 Azure 관리형 앱을 사용하여 추가된 할당을 거부합니다.
    - 클래식 배포를 위한 클래식 서비스 관리자 또는 공동 관리자 할당. 

    ![할당 창](./media/shared/rg-check-access-assignments-user.png)

## <a name="list-role-assignments-for-a-managed-identity"></a>관리 ID에 대한 역할 할당 나열

앞에서 설명한 것처럼 **액세스 제어(IAM)** 블레이드를 사용하여 특정 범위에서 시스템 할당 및 사용자 할당 관리 ID에 대한 역할 할당을 나열할 수 있습니다. 이 섹션에서는 관리 ID에 대해서만 역할 할당을 나열하는 방법을 설명합니다.

### <a name="system-assigned-managed-identity"></a>시스템 할당 관리 ID

1. Azure Portal에서 시스템 할당 관리 ID를 엽니다.

1. 왼쪽 메뉴에서 **ID** 를 클릭합니다.

    ![시스템 할당 관리 ID](./media/shared/identity-system-assigned.png)

1. **사용 권한** 아래에서 **Azure 역할 할당** 을 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같이 다양한 범위에서 선택한 시스템 할당 관리 ID에 할당된 역할 목록이 표시됩니다. 이 목록에는 사용자가 읽기 권한을 가진 모든 역할 할당이 포함됩니다.

    ![시스템 할당 관리 ID에 대한 역할 할당](./media/shared/role-assignments-system-assigned.png)

1. 구독을 변경하려면 **구독** 목록을 클릭합니다.

### <a name="user-assigned-managed-identity"></a>사용자 할당 관리 ID

1. Azure Portal에서 사용자 할당 관리 ID를 엽니다.

1. **Azure 역할 할당** 을 클릭합니다.

    관리 그룹, 구독, 리소스 그룹 또는 리소스와 같이 다양한 범위에서 선택한 사용자 할당 관리 ID에 할당된 역할 목록이 표시됩니다. 이 목록에는 사용자가 읽기 권한을 가진 모든 역할 할당이 포함됩니다.

    ![사용자 할당 관리 ID에 대한 역할 할당을 보여 주는 스크린샷.](./media/shared/role-assignments-user-assigned.png)

1. 구독을 변경하려면 **구독** 목록을 클릭합니다.

## <a name="list-number-of-role-assignments"></a>역할 할당 목록 수

각 구독에서 최대 **2000** 개의 역할 할당을 유지할 수 있습니다. 이 제한에는 구독, 리소스 그룹 및 리소스 범위의 역할 할당이 포함됩니다. 이 제한을 추적하는 데 도움이 되도록 **역할 할당** 탭에는 현재 구독에 대한 역할 할당 수를 나열하는 차트가 포함됩니다.

구독에 대한 역할 할당 제한은 현재 증가하고 있습니다. 자세한 내용은 [Azure RBAC 문제 해결](troubleshooting.md#azure-role-assignments-limit)을 참조하세요.

![액세스 제어 - 역할 할당 차트 수](./media/role-assignments-list-portal/access-control-role-assignments-chart.png)

최대 개수에 근접하고 더 많은 역할 할당을 추가하려면 **역할 할당 추가** 창에 경고가 표시됩니다. 역할 할당 수를 줄이는 방법은 [Azure RBAC 문제 해결](troubleshooting.md#azure-role-assignments-limit)을 참조하세요.

![액세스 제어 - 역할 할당 추가 경고](./media/role-assignments-list-portal/add-role-assignment-warning.png)

## <a name="download-role-assignments"></a>역할 할당 다운로드

CSV 또는 JSON 형식으로 특정한 범위에서 역할 할당을 다운로드할 수 있습니다. 이는 스프레드시트에서 목록을 검사하거나 구독을 마이그레이션할 때 인벤토리를 수행해야 하는 경우에 유용합니다.

역할 할당을 다운로드하는 경우 다음 조건을 염두에 두어야 합니다.

- 디렉터리 읽기 권한자 역할과 같은 디렉터리를 읽을 수 있는 권한이 없다면 DisplayName, SignInName 및 ObjectType 열이 비어 있게 됩니다.
- 보안 주체가 삭제된 역할 할당은 포함되지 않습니다.
- 클래식 관리자에게 부여된 액세스 권한은 포함되지 않습니다.

특정 범위에서 역할 할당을 다운로드하려면 다음 단계를 수행합니다.

1. Azure Portal에서 **모든 서비스** 를 클릭한 다음, 역할 할당을 다운로드하려는 범위를 선택합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 리소스를 선택할 수 있습니다.

1. 특정 리소스를 클릭합니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당 다운로드** 를 클릭하여 역할 할당 다운로드 창을 엽니다.

    ![액세스 제어 - 역할 할당 다운로드](./media/role-assignments-list-portal/download-role-assignments.png)

1. 확인란을 사용하여 다운로드한 파일에 포함하려는 역할 할당을 선택합니다.

    - **상속됨** - 현재 범위에 대해 상속된 역할 할당을 포함합니다.
    - **현재 범위에서** - 현재 범위에 대한 역할 할당을 포함합니다.
    - **자식** - 현재 범위 아래 수준에서 역할 할당을 포함합니다. 이 확인란은 관리 그룹 범위에서는 사용할 수 없습니다.

1. CSV(쉼표로 구분된 값) 또는 JSON(JavaScript Object Notation) 파일 형식을 선택합니다.

1. 파일 이름을 지정합니다.

1. **시작** 을 클릭하여 다운로드를 시작합니다.

    다음은 각 파일 형식에 대한 출력 예를 보여 줍니다.

    ![CSV로 역할 할당 다운로드](./media/role-assignments-list-portal/download-role-assignments-csv.png)

    ![JSON 형식으로 다운로드된 역할 할당의 스크린샷](./media/role-assignments-list-portal/download-role-assignments-json.png)

## <a name="next-steps"></a>다음 단계

- [Azure Portal을 사용하여 Azure 역할 할당](role-assignments-portal.md)
- [Azure RBAC 문제 해결](troubleshooting.md)
