---
title: 관리 단위 범위를 사용하여 역할 할당 및 나열 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 관리 단위를 사용하여 역할 권한 범위를 제한할 수 있습니다.
services: active-directory
documentationcenter: ''
author: rolyon
manager: daveba
ms.service: active-directory
ms.topic: how-to
ms.subservice: roles
ms.workload: identity
ms.date: 05/14/2021
ms.author: rolyon
ms.reviewer: anandy
ms.custom: oldportal;it-pro;
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b34eafac248bc0fd06076550e784a061573a712
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536048"
---
# <a name="assign-scoped-roles-to-an-administrative-unit"></a>관리 단위에 범위가 지정된 역할 할당

Azure AD(Azure Active Directory)에서 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위로 제한되는 범위를 사용하여 Azure AD 역할에 사용자를 할당할 수 있습니다.

## <a name="prerequisites"></a>필수 조건

- 각 관리 단위 관리자에 대한 Azure AD Premium P1 또는 P2 라이선스
- 관리 단위 구성원에 대한 Azure AD Free 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.


## <a name="available-roles"></a>사용 가능한 역할

역할  |  Description
----- |  -----------
인증 관리자  |  할당된 관리 단위에서만 관리 사용자가 아닌 사용자의 인증 방법 정보를 보고, 설정하고, 재설정하기 위해 액세스할 수 있습니다.
그룹 관리자  |  할당된 관리 단위에서만 그룹과 이름 지정 및 만료 정책 같은 그룹 설정의 모든 측면을 관리할 수 있습니다.
기술 지원팀 관리자  |  할당된 관리 단위에서만 비관리자와 기술 지원팀 관리자의 암호를 재설정할 수 있습니다.
라이선스 관리자  |  관리 단위 내에서만 라이선스 할당을 할당, 제거, 업데이트할 수 있습니다.
암호 관리자  |  할당된 관리 단위 내에서만 비관리자 및 암호 관리자의 암호를 재설정할 수 있습니다.
사용자 관리자  |  할당된 관리 단위 내에서만 제한된 관리자의 암호 재설정을 비롯하여 사용자 및 그룹의 모든 측면을 관리할 수 있습니다.

## <a name="security-principals-that-can-be-assigned-to-a-scoped-role"></a>범위가 지정된 역할에 할당할 수 있는 보안 주체

관리 단위 범위가 있는 역할에는 다음 보안 주체를 할당할 수 있습니다.

* 사용자
* 역할 할당 Azure AD 그룹
* SPN(서비스 주체 이름)

## <a name="assign-a-scoped-role"></a>범위 지정 역할 할당

Azure Portal, PowerShell 또는 Microsoft Graph를 사용하여 범위 지정 역할을 할당할 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **관리 단위** 를 선택한 다음, 사용자 역할 범위를 할당하려는 관리 단위를 선택합니다. 

1. 왼쪽 창에서 **역할 및 관리자** 를 선택하여 사용 가능한 모든 역할을 나열합니다.

   ![역할 범위를 할당하려는 관리 단위를 선택하기 위한 “역할 및 관리자” 창의 스크린샷.](./media/admin-units-assign-roles/select-role-to-scope.png)

1. 할당할 역할을 선택하고 **할당 추가** 를 선택합니다. 

1. **할당 추가** 창에서 역할에 할당할 사용자를 하나 이상 선택합니다.

   ![범위를 지정할 역할을 선택하고 할당 추가 선택](./media/admin-units-assign-roles/select-add-assignment.png)

> [!Note]
> Azure AD PIM(Privileged Identity Management)을 사용하여 관리 단위에 역할을 할당하려면 [PIM에서 Azure AD 역할 할당](../privileged-identity-management/pim-how-to-add-role-to-user.md?tabs=new#assign-a-role-with-restricted-scope)을 참조하세요.

### <a name="powershell"></a>PowerShell

```powershell
$adminUser = Get-AzureADUser -ObjectId "Use the user's UPN, who would be an admin on this unit"
$role = Get-AzureADDirectoryRole | Where-Object -Property DisplayName -EQ -Value "User Administrator"
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
$roleMember = New-Object -TypeName Microsoft.Open.MSGraph.Model.MsRoleMemberInfo
$roleMember.Id = $adminUser.ObjectId
Add-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id -RoleId $role.ObjectId -RoleMemberInfo $roleMember
```

강조 표시된 섹션을 특정 환경의 요구에 따라 변경할 수 있습니다.

### <a name="microsoft-graph-api"></a>Microsoft Graph API

요청

```http
POST /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```
    
본문

```http
{
  "roleId": "roleId-value",
  "roleMemberInfo": {
    "id": "id-value"
  }
}
```

## <a name="view-a-list-of-the-scoped-admins-in-an-administrative-unit"></a>관리 단위에서 범위가 지정된 관리자 목록 보기

Azure Portal, PowerShell 또는 Microsoft Graph를 사용하여 범위가 지정된 관리자를 볼 수 있습니다.

### <a name="azure-portal"></a>Azure Portal

[Azure AD의 관리 단위 섹션](https://ms.portal.azure.com/?microsoft_aad_iam_adminunitprivatepreview=true&microsoft_aad_iam_rbacv2=true#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/AdminUnit)에서 관리 단위 범위를 사용하여 만든 모든 역할 할당을 볼 수 있습니다. 

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **관리 단위** 를 선택한 다음, 확인하려는 역할 할당 목록에 대한 관리 단위를 선택합니다. 

1. **역할 및 관리자** 를 선택한 다음 역할을 열어 관리 단위의 할당을 확인합니다.

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'The display name of the unit'"
Get-AzureADMSScopedRoleMembership -Id $adminUnitObj.Id | fl *
```

강조 표시된 섹션을 특정 환경의 요구에 따라 변경할 수 있습니다.

### <a name="microsoft-graph-api"></a>Microsoft Graph API

요청

```http
GET /directory/administrativeUnits/{admin-unit-id}/scopedRoleMembers
```

본문

```http
{}
```

## <a name="next-steps"></a>다음 단계

- [Azure AD 그룹을 사용하여 역할 할당 관리](groups-concept.md)
- [그룹에 할당된 Azure AD 역할 문제 해결](groups-faq-troubleshooting.yml)
