---
title: Azure Portal을 사용하여 Azure 역할 할당 - Azure RBAC
description: Azure Portal 및 Azure RBAC(Azure 역할 기반 액세스 제어)를 사용하여 사용자, 그룹, 서비스 주체 또는 관리 ID의 Azure 리소스에 대한 액세스 권한을 부여하는 방법을 알아봅니다.
services: active-directory
author: rolyon
manager: daveba
ms.service: role-based-access-control
ms.topic: how-to
ms.workload: identity
ms.date: 09/24/2021
ms.author: rolyon
ms.custom: contperf-fy21q3-portal,subject-rbac-steps
ms.openlocfilehash: 368a3480af60b4c1a9b819d3b4667f2212e62290
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094809"
---
# <a name="assign-azure-roles-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure 역할 할당

[!INCLUDE [Azure RBAC definition grant access](../../includes/role-based-access-control/definition-grant.md)] 이 문서에서는 Azure Portal을 사용하여 역할을 할당하는 방법을 설명합니다.

Azure Active Directory에서 관리자 역할을 할당해야 하는 경우 [사용자에게 Azure AD 역할 할당](../active-directory/roles/manage-roles-portal.md)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [Azure role assignment prerequisites](../../includes/role-based-access-control/prerequisites-role-assignments.md)]

## <a name="choose-experience"></a>환경 선택

Azure RBAC에는 Azure Portal에서 Azure 역할을 할당할 수 있는 새로운 환경이 있으며, 이 환경은 현재 공개 미리 보기에 있습니다. 이 새로운 환경을 사용해 보려면 **(미리 보기)** 탭의 단계를 따릅니다.

#### <a name="current"></a>[Current](#tab/current/)

## <a name="step-1-identify-the-needed-scope"></a>1단계: 필요한 범위 확인

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)] 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

![Azure RBAC에 대한 범위 수준을 보여 주는 다이어그램](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽의 검색 상자에서 액세스 권한을 부여할 범위를 검색합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 특정 리소스를 검색합니다.

1. 해당 범위에 대한 특정 리소스를 클릭합니다.

    다음은 예제 리소스 그룹을 보여줍니다.

    ![리소스 그룹 개요 페이지의 스크린샷](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-pane"></a>2단계: 역할 할당 추가 창 열기

**IAM**(액세스 제어)은 Azure 리소스에 대한 액세스 권한을 부여하기 위해 역할을 할당하는 데 일반적으로 사용하는 페이지입니다. ID 및 IAM이라고도 하며 Azure Portal의 여러 위치에 표시됩니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

    ![리소스 그룹에 대한 액세스 제어(IAM) 페이지의 스크린샷](./media/shared/rg-access-control.png)

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가** 를 클릭합니다.
   역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

   ![추가 > 역할 할당 추가 메뉴의 스크린샷](./media/shared/add-role-assignment-menu.png)

    역할 할당 추가 창이 열립니다.

   ![역할, 다음에 대한 액세스 할당 및 선택 옵션이 있는 역할 할당 추가 페이지의 스크린샷](../../includes/role-based-access-control/media/add-role-assignment-page.png)

## <a name="step-3-select-the-appropriate-role"></a>3단계: 적절한 역할 선택

1. **역할** 목록에서 검색하거나 스크롤하여 할당할 역할을 찾습니다.

    적절한 역할을 결정하는 데 도움이 되도록 정보 아이콘 위로 마우스를 가져가 역할에 대한 설명을 표시할 수 있습니다. 추가 정보는 [Azure 기본 제공 역할](built-in-roles.md) 문서를 볼 수 있습니다.

   ![역할 할당 추가에서 역할 목록 선택의 스크린샷](./media/role-assignments-portal/add-role-assignment-role.png)

1. 역할을 클릭하여 선택합니다.

## <a name="step-4-select-who-needs-access"></a>4단계: 액세스 이용자 선택

1. **액세스 할당 대상** 목록에서 액세스 권한을 할당할 보안 주체 유형을 선택합니다.

    | 형식 | 설명 |
    | --- | --- |
    | **사용자, 그룹 또는 서비스 주체** | 사용자, 그룹 또는 서비스 사용자(애플리케이션)에 역할을 할당하려면 이 유형을 선택합니다. |
    | **사용자 할당 관리 ID** | [사용자 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당하려면 이 형식을 선택합니다. |
    | *시스템 할당 관리 ID* | [시스템 할당 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)에 역할을 할당하려면 관리 ID가 있는 Azure 서비스 인스턴스를 선택합니다. |

   ![역할 할당 추가에서 보안 주체 선택의 스크린샷](./media/role-assignments-portal/add-role-assignment-type.png)

1. 사용자 할당 관리 ID 또는 시스템 할당 관리 ID를 선택한 경우 관리 ID가 있는 **구독** 을 선택합니다.

1. **선택** 섹션에서 문자열을 입력하거나 목록을 스크롤하여 보안 주체를 검색합니다.

   ![역할 할당 추가에서 사용자 선택의 스크린샷](./media/role-assignments-portal/add-role-assignment-user.png)

1. 보안 주체를 찾았으면 클릭하여 선택합니다.

## <a name="step-5-assign-role"></a>5단계: 역할 할당

1. 역할을 할당하려면 **저장** 을 클릭합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

1. **역할 할당** 탭에서 목록에 역할 할당이 표시되는지 확인합니다.

    ![역할을 할당한 후 역할 할당 목록의 스크린샷](./media/role-assignments-portal/rg-role-assignments.png)

#### <a name="preview"></a>[(미리 보기)](#tab/preview/)

## <a name="step-1-identify-the-needed-scope-preview"></a>1단계: 필요한 범위 확인(미리 보기)

[!INCLUDE [Scope for Azure RBAC introduction](../../includes/role-based-access-control/scope-intro.md)] 자세한 내용은 [범위 이해](scope-overview.md)를 참조하세요.

![미리 보기 환경의 Azure RBAC에 대한 범위 수준을 보여 주는 다이어그램](../../includes/role-based-access-control/media/scope-levels.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽의 검색 상자에서 액세스 권한을 부여할 범위를 검색합니다. 예를 들어 **관리 그룹**, **구독**, **리소스 그룹** 또는 특정 리소스를 검색합니다.

1. 해당 범위에 대한 특정 리소스를 클릭합니다.

    다음은 예제 리소스 그룹을 보여줍니다.

    ![미리 보기 환경에 대한 리소스 그룹 개요 페이지의 스크린샷](./media/shared/rg-overview.png)

## <a name="step-2-open-the-add-role-assignment-page-preview"></a>2단계: 역할 할당 추가 페이지 열기(미리 보기)

**IAM**(액세스 제어)은 Azure 리소스에 대한 액세스 권한을 부여하기 위해 역할을 할당하는 데 일반적으로 사용하는 페이지입니다. ID 및 IAM이라고도 하며 Azure Portal의 여러 위치에 표시됩니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

    다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

    ![미리 보기 환경의 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 스크린샷](./media/shared/rg-access-control.png)

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 클릭합니다.

    역할을 할당할 수 있는 권한이 없으면 역할 할당 추가 옵션이 비활성화됩니다.

    ![미리 보기 환경에 대한 추가 > 역할 할당 추가 메뉴의 스크린샷](./media/shared/add-role-assignment-menu-preview.png)

    역할 할당 추가 페이지가 열립니다.

## <a name="step-3-select-the-appropriate-role-preview"></a>3단계: 적절한 역할 선택(미리 보기)

1. **역할** 탭에서 사용하려는 역할을 선택합니다.

    역할은 이름 또는 설명을 기준으로 검색할 수 있습니다. 또한 역할은 유형 및 범주를 기준으로 필터링할 수도 있습니다.

   ![미리 보기 환경을 위한 역할 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/roles.png)

1. **세부 정보** 열에서 **보기** 를 클릭하여 역할에 대한 자세한 정보를 확인합니다.

   ![미리 보기 환경에 대한 권한 탭이 있는 역할 세부 정보 보기 창의 스크린샷](./media/role-assignments-portal/select-role-permissions.png)

1. **다음** 을 클릭합니다.

## <a name="step-4-select-who-needs-access-preview"></a>4단계: 액세스 권한이 필요한 사용자 선택(미리 보기)

1. **멤버** 탭에서 **사용자, 그룹 또는 서비스 주체** 를 선택하여 선택한 역할을 하나 이상의 Azure AD 사용자, 그룹 또는 서비스 주체(애플리케이션)에 할당합니다.

   ![미리 보기 환경을 위한 멤버 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/members.png)

1. **멤버 선택** 을 클릭합니다.

1. 사용자, 그룹 또는 서비스 주체를 찾아서 선택합니다.

    **선택** 상자에서 입력하여 디렉터리에서 표시 이름이나 메일 주소를 검색할 수 있습니다.

   ![미리 보기 환경의 멤버 선택 창 스크린샷](./media/shared/select-members.png)

1. **선택을 클릭하여** 사용자, 그룹 또는 서비스 주체를 멤버 목록에 추가합니다.

1. 선택한 역할을 하나 이상의 관리 ID에 할당하려면 **관리 ID** 를 선택합니다.

1. **멤버 선택** 을 클릭합니다.

1. 관리 **ID 선택** 창에서 유형이 [사용자 할당 관리 ID인지](../active-directory/managed-identities-azure-resources/overview.md) 또는 시스템 할당 관리 [ID인지를](../active-directory/managed-identities-azure-resources/overview.md)선택합니다.

1. 관리 ID를 찾아서 선택합니다.

    시스템 할당 관리 ID의 경우 Azure 서비스 인스턴스별로 관리 ID를 선택할 수 있습니다.

   ![미리 보기 환경을 위한 관리 ID 선택 창의 스크린샷.](./media/role-assignments-portal/select-managed-identity.png)

1. **선택** 을 클릭 하 여 관리 되는 Id를 구성원 목록에 추가 합니다.

1. **설명** 상자에서 이 역할 할당에 대한 설명을 입력합니다(선택 사항).

    이 설명은 나중에 역할 할당 목록에 표시될 수 있습니다.

1. **다음** 을 클릭합니다.

## <a name="step-5-optional-add-condition-preview"></a>5단계: (선택 사항) 조건 추가(미리 보기)

조건을 지 원하는 역할을 선택한 경우에는 조건 **(선택 사항)** 탭이 나타나고 역할 할당에 조건을 추가 하는 옵션이 있습니다. [조건](conditions-overview.md)은 필요에 따라 역할 할당에 추가하여 더 세분화된 액세스 제어를 제공할 수 있는 추가 검사입니다.

현재 [스토리지 Blob 데이터 작업](conditions-format.md#actions)을 포함하는 기본 제공 또는 사용자 지정 역할 할당에 조건을 추가할 수 있습니다. 여기에는 다음과 같은 기본 제공 역할이 포함됩니다.


- [Storage Blob 데이터 기여자](built-in-roles.md#storage-blob-data-contributor)
- [Storage Blob 데이터 소유자](built-in-roles.md#storage-blob-data-owner)
- [Storage Blob 데이터 읽기 권한자](built-in-roles.md#storage-blob-data-reader)

1. 스토리지 Blob 특성에 따라 역할 할당을 더 구체화하려면 **조건 추가** 를 클릭합니다. 자세한 내용은 [Azure 역할 할당 조건 추가 또는 편집](conditions-role-assignments-portal.md)을 참조하세요.

   ![미리 보기 환경에 대한 조건 추가 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/shared/condition.png)

1. **다음** 을 클릭합니다.

## <a name="step-6-assign-role-preview"></a>6단계: 역할 할당(미리 보기)

1. **검토 + 할당** 탭에서 역할 할당 설정을 검토합니다.

   ![미리 보기 환경에 대한 검토 + 할당 탭이 있는 역할 할당 페이지의 스크린샷](./media/role-assignments-portal/review-assign.png)

1. **검토 + 할당** 을 클릭하여 역할을 할당합니다.

   몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

    ![미리 보기 환경에 대한 역할을 할당한 후의 역할 할당 목록에 대한 스크린샷](./media/role-assignments-portal/rg-role-assignments.png)

1. 역할 할당에 대한 설명이 표시되지 않으면 **열 편집** 을 클릭하여 **설명** 열을 추가합니다.

---

## <a name="next-steps"></a>다음 단계

- [Azure 구독의 관리자로 사용자 할당](role-assignments-portal-subscription-admin.md)
- [Azure 역할 할당 제거](role-assignments-remove.md)
- [Azure RBAC 문제 해결](troubleshooting.md)
