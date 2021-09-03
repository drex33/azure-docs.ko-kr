---
title: 관리 단위에서 그룹 추가, 제거 및 나열 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 관리 단위에서 그룹과 해당 역할 권한을 관리합니다.
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
ms.openlocfilehash: c824ee3a8365261fa6409bf79904cde1b409fc26
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536050"
---
# <a name="add-and-manage-groups-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory의 관리 단위에서 그룹 추가 및 관리

Azure AD(Azure Active Directory)에서 관리 단위에 그룹을 추가하여 관리 범위를 더 상세하게 제어할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 각 관리 단위 관리자에 대한 Azure AD Premium P1 또는 P2 라이선스
- 관리 단위 구성원에 대한 Azure AD Free 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="add-groups-to-an-administrative-unit"></a>관리 단위에 그룹 추가

Azure Portal, PowerShell 또는 Microsoft Graph를 사용하여 관리 단위에 그룹을 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

관리 단위에는 개별 그룹만 할당할 수 있습니다. 그룹을 대량 작업으로 할당할 수 있는 옵션은 없습니다. Azure Portal에서 다음 두 가지 방법 중 하나로 관리 단위에 그룹을 할당할 수 있습니다.

* **그룹** 창에서 다음을 수행합니다.

  1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

  1. **Azure Active Directory** > **그룹** 을 선택합니다.

  1. 관리 단위에 할당할 그룹을 선택합니다.

  1. 왼쪽 창에서 **관리 단위** 를 선택하여 그룹이 할당된 관리 단위 목록을 표시합니다. 

     !["관리 단위" 창에 있는 "관리 단위에 할당" 링크의 스크린샷](./media/admin-units-add-manage-groups/assign-to-group-1.png)

  1. **관리 단위에 할당** 을 선택합니다.

  1. 오른쪽 창에서 관리 단위를 선택합니다.

* **관리 단위** > **그룹** 창에서 다음을 수행합니다.

  1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

  1. **Azure Active Directory** > **관리 단위** 를 선택합니다.
  
  1. 그룹을 추가할 관리 단위를 선택합니다.
  
  1. **그룹** 을 선택합니다. 관리 단위에 이미 할당된 모든 그룹은 오른쪽 창에 표시됩니다. 

  1. **그룹** 창에서 **추가** 를 선택합니다.
    오른쪽 창에는 Azure AD 조직에서 사용 가능한 모든 그룹이 나열됩니다. 

     ![관리 단위에 그룹을 추가하기 위한 "추가" 단추의 스크린샷](./media/admin-units-add-manage-groups/assign-to-admin-unit.png)

  1. 관리 단위에 할당할 그룹을 하나 이상 선택한 다음, **선택** 단추를 선택합니다.

### <a name="powershell"></a>PowerShell

다음 예제에서 `Add-AzureADMSAdministrativeUnitMember` cmdlet을 사용하여 그룹을 관리 단위에 추가합니다. 관리 단위의 개체 ID와 추가할 그룹의 개체 ID를 인수로 사용합니다. 강조 표시된 섹션을 특정 환경의 요구에 따라 변경합니다.


```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$GroupObj = Get-AzureADGroup -Filter "displayname eq 'TestGroup'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $GroupObj.ObjectId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 명령을 실행합니다.

요청

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

본문

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/{group-id}"
}
```

예제

```http
{
"@odata.id":"https://graph.microsoft.com/v1.0/groups/871d21ab-6b4e-4d56-b257-ba27827628f3"
}
```

## <a name="view-a-list-of-groups-in-an-administrative-unit"></a>관리 단위의 그룹 목록 보기

### <a name="azure-portal"></a>Azure portal

1. Azure Portal에서 **Azure AD** 로 이동합니다.

1. 왼쪽 창에서 **관리 단위** 를 선택한 다음, 확인할 그룹에 대한 관리 단위를 선택합니다. 기본적으로 왼쪽 패널에 **모든 사용자** 가 선택되어 있습니다. 

1. 왼쪽 창에서 **그룹** 을 선택합니다. 오른쪽 창에는 선택한 관리 단위의 구성원인 그룹 목록이 표시됩니다.

   ![관리 단위에 그룹 목록을 표시하는 "그룹" 창의 스크린샷](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

관리 단위의 모든 구성원 목록을 표시하려면 다음 명령을 실행합니다. 

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id
```

관리 단위의 구성원인 모든 그룹을 표시하려면 다음 코드 조각을 사용합니다.

```powershell
foreach ($member in (Get-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id)) 
{
if($member.ObjectType -eq "Group")
{
Get-AzureADGroup -ObjectId $member.ObjectId
}
}
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 명령을 실행합니다.

요청

```http
GET /directory/administrativeUnits/{admin-unit-id}/members/$/microsoft.graph.group
```

본문

```http
{}
```

## <a name="view-a-list-of-administrative-units-for-a-group"></a>그룹에 대한 관리 단위 목록 보기

### <a name="azure-portal"></a>Azure portal

1. Azure Portal에서 **Azure AD** 로 이동합니다.

1. 왼쪽 창에서 **그룹** 을 선택하여 그룹 목록을 표시합니다.

1. 그룹의 프로필을 열 그룹을 선택합니다. 

1. 왼쪽 패널에서 **관리 단위** 를 선택하여 그룹이 구성원인 모든 관리 단위를 나열합니다.

   ![그룹이 할당된 목록 관리 단위를 표시하는 "관리 단위" 창의 스크린샷](./media/admin-units-add-manage-groups/list-group-au.png)

### <a name="powershell"></a>PowerShell

다음 명령을 실행합니다.

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -ObjectId $_.ObjectId | where {$_.ObjectId -eq $groupObjId} }
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 명령을 실행합니다.

```http
https://graph.microsoft.com/v1.0/groups/{group-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-group-from-an-administrative-unit"></a>관리 단위에서 그룹 제거

### <a name="azure-portal"></a>Azure portal

다음 두 가지 방법 중 하나로 Azure Portal의 관리 단위에서 그룹을 제거할 수 있습니다.

- 그룹 개요에서 제거:

  1. Azure Portal에서 **Azure AD** 로 이동합니다.
  1. 왼쪽 창에서 **그룹** 을 선택한 다음, 관리 단위에서 제거할 그룹의 프로필을 엽니다.
  1. 왼쪽 패널에서 **관리 단위** 를 선택하여 그룹이 할당된 모든 관리 단위를 나열합니다. 
  1. 그룹에서 제거하려는 관리 단위를 선택한 다음, **관리 단위에서 제거** 를 선택합니다.

     ![선택된 관리 단위에 할당된 그룹 목록을 표시하는 "관리 단위" 창의 스크린샷](./media/admin-units-add-manage-groups/group-au-remove.png)

- 관리 단위에서 제거:

  1. Azure Portal에서 **Azure AD** 로 이동합니다.
  1. 왼쪽 창에서 **관리 단위** 를 선택한 다음, 그룹이 할당된 관리 단위를 선택합니다.
  1. 왼쪽 창에서 **그룹** 을 선택하여 관리 단위에 할당된 모든 그룹을 나열합니다.
  1. 제거할 그룹을 선택한 다음, **그룹 제거** 를 선택합니다.

    ![관리 단위에 그룹 목록을 표시하는 "그룹" 창의 스크린샷](./media/admin-units-add-manage-groups/list-groups-in-admin-units.png)

### <a name="powershell"></a>PowerShell

다음 명령을 실행합니다.

```powershell
Remove-AzureADMSAdministrativeUnitMember -ObjectId $adminUnitId -MemberId $memberGroupObjId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

다음 명령을 실행합니다.

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{group-id}/$ref
```

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](admin-units-assign-roles.md)
- [관리 단위에서 사용 관리](admin-units-add-manage-users.md)
