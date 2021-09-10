---
title: Synapse Studio에서 Synapse RBAC 할당을 관리하는 방법
description: 이 문서에서는 AAD 보안 주체에 Synapse RBAC 역할을 할당하고 취소하는 방법을 설명합니다.
author: meenalsri
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/1/2020
ms.author: mesrivas
ms.reviewer: jrasnick
ms.openlocfilehash: 8bbb6185368b438376529d69409d301c0cdd9c97
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566565"
---
# <a name="how-to-manage-synapse-rbac-role-assignments-in-synapse-studio"></a>Synapse Studio에서 Synapse RBAC 역할 할당을 관리하는 방법

Synapse RBAC는 사용자, 그룹 및 기타 보안 주체에 Synapse 리소스 및 코드 아티팩트에 액세스하여 사용할 수 있는 권한을 할당하는 역할을 사용합니다.  [자세히 알아보기](./synapse-workspace-synapse-rbac.md)

이 문서에서는 Synapse RBAC 역할 할당을 추가하고 삭제하는 방법을 보여줍니다.

>[!Note]
>- Synapse RBAC 역할 할당을 관리하려면 작업 영역 또는 관리하려는 개체를 포함하는 하위 수준 범위에서 Synapse 관리자 역할이 있어야 합니다. 작업 영역에서 Synapse 관리자인 경우 작업 영역의 모든 개체에 대한 액세스 권한을 부여할 수 있습니다. 
>- 다른 AD 테넌트의 사용자의 **게스트 사용자** 도 Synapse 관리자 역할이 할당된 후 역할 할당을 보고 관리할 수 있습니다.
>- 할당되거나 사용할 수 있는 Synapse 관리자가 없는 경우 작업 영역에 대한 액세스 권한을 다시 얻기 위해, 작업 영역에 대한 **Azure RBAC** 역할 할당을 관리할 수 있는 권한이 있는 사용자가 **Synapse RBAC** 역할 할당을 관리할 수도 있습니다. 이를 통해 Synapse 관리자 또는 기타 Synapse 역할 할당을 추가할 수 있습니다.
>- SQL 풀에 대한 액세스는 SQL 권한을 사용하여 관리합니다.  Synapse 관리자 및 Synapse SQL 관리자 역할을 제외하고, Synapse RBAC 역할은 SQL 풀에 대한 액세스 권한을 부여하지 않습니다.

>[!important]
>- Synapse RBAC 역할 할당에 대한 변경 내용은 적용되는 데 2-5분 정도 소요될 수 있습니다. 
>- 보안 그룹의 멤버 자격을 수정하여 Synapse RBAC 권한을 관리하는 경우 멤버 자격에 대한 변경 내용은 Azure Active Directory를 사용하여 관리합니다.  그룹 멤버 자격에 대한 변경 내용은 적용되는 데 10-15분 이상 소요될 수 있습니다.

## <a name="open-synapse-studio"></a>Synapse Studio 열기  

사용자, 그룹, 서비스 주체 또는 관리 ID에 역할을 할당하려면 먼저 [Synapse Studio를 열고](https://web.azuresynapse.net/) 작업 영역에 로그인합니다. 

![작업 영역에 로그인](./media/common/login-workspace.png) 
 
 작업 영역을 연 후 왼쪽의 **보안** 섹션을 확장하고 **액세스 제어** 를 선택합니다. 

 ![왼쪽의 보안 섹션에서 액세스 제어 선택](./media/how-to-manage-synapse-rbac-role-assignments/left-nav-security-access-control.png)

액세스 제어 화면에는 현재 역할 할당이 나열됩니다.  보안 주체 이름 또는 이메일을 기준으로 목록을 필터링하고, 포함된 개체 유형, 역할 및 범위를 선택적으로 필터링할 수 있습니다. 이 화면에서 역할 할당을 추가하거나 제거할 수 있습니다.  

## <a name="add-a-synapse-role-assignment"></a>Synapse 역할 할당 추가

액세스 제어 화면에서 **+추가** 를 선택하여 새 역할 할당을 만듭니다.

![+추가를 클릭하여 새 역할 할당 만들기](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add.png)

역할 할당 추가 탭에서 작업 영역 범위 또는 작업 영역 항목 범위에서 역할 할당을 만들 수 있습니다. 

## <a name="add-workspace-scoped-role-assignment"></a>작업 영역 범위 역할 할당 추가

먼저 **작업 영역** 을 범위로 선택하고 **Synapse RBAC 역할** 을 선택합니다.  역할을 할당할 **보안 주체** 를 선택한 다음, 역할 할당을 만듭니다. 

![작업 영역 역할 할당 추가 - 역할 선택](./media/how-to-manage-synapse-rbac-role-assignments/access-control-workspace-role-assignment.png) 

할당된 역할은 작업 영역에서 적용 가능한 모든 개체에 적용됩니다.

## <a name="add-workspace-item-scoped-role-assignment"></a>작업 영역 항목 범위 역할 할당 추가

보다 세분화된 범위에서 역할을 할당하려면 **작업 영역 항목** 을 범위로 선택한 다음, 범위 지정 **항목 유형** 을 선택합니다.       

![작업 영역 항목 역할 할당 추가 - 항목 유형 선택](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-item-type.png) 

범위로 사용할 특정 **항목** 을 선택한 다음, 드롭다운에서 할당할 **역할** 을 선택합니다.  드롭다운 목록에는 선택한 항목 유형에 유효한 역할만 나열됩니다. [자세히 알아봅니다](./synapse-workspace-synapse-rbac.md).  

![작업 영역 항목 역할 할당 추가 - 역할 선택](./media/how-to-manage-synapse-rbac-role-assignments/access-control-add-workspace-item-assignment-select-role.png) 
 
그런 다음, 역할을 할당할 **보안 주체** 를 선택합니다.  여러 보안 주체를 반복적으로 선택할 수 있습니다.  **적용** 을 선택하여 역할 할당을 만듭니다.

## <a name="remove-a-synapse-rbac-role-assignment"></a>Synapse RBAC 역할 할당 제거

Synapse RBAC 액세스를 취소하려면 적절한 역할 할당을 제거합니다.  액세스 제어 화면에서 필터를 사용하여 제거할 역할 할당을 찾습니다.  역할 할당을 확인한 다음, **액세스 제거** 를 선택합니다.   

![역할 할당을 삭제하여 액세스 제거](./media/how-to-manage-synapse-rbac-role-assignments/access-control-remove-access.png)

역할 할당에 대한 변경 내용은 적용되는 데 2-5분이 소요됩니다.   

## <a name="next-steps"></a>다음 단계

[일반적인 작업을 수행하는 데 필요한 Synapse RBAC 역할 이해](./synapse-workspace-understand-what-role-you-need.md)