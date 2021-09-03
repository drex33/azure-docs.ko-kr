---
title: 관리 단위에서 사용자 추가, 제거 및 나열 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory의 관리 단위에서 사용자 및 역할 권한 관리
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
ms.openlocfilehash: 6f40b395bffabd089831a7a827a4ab81e216727c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528630"
---
# <a name="add-and-manage-users-in-an-administrative-unit-in-azure-active-directory"></a>Azure Active Directory의 관리 단위에서 사용자 추가 및 관리

Azure AD(Azure Active Directory)에서 관리 단위에 그룹을 추가하여 관리 범위를 더 상세하게 제어할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 각 관리 단위 관리자에 대한 Azure AD Premium P1 또는 P2 라이선스
- 관리 단위 구성원에 대한 Azure AD Free 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="add-users-to-an-administrative-unit"></a>관리 단위에 사용자 추가

### <a name="azure-portal"></a>Azure portal

관리 단위에 개별적으로 또는 대량 작업으로 사용자를 할당할 수 있습니다.

- 사용자 프로필에서 개별 사용자 할당:

   1. [Azure Portal](https://portal.azure.com)이나 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

   1. **Azure Active Directory** > **사용자** 를 선택한 다음, 사용자 프로필을 열려면 관리 단위에 할당할 사용자를 선택합니다.
   
   1. **관리 단위** 를 선택합니다. 
   
   1. 사용자를 하나 이상의 관리 단위에 할당하려면 **관리 단위에 할당** 을 선택한 다음, 오른쪽 창에서 사용자를 할당할 관리 단위를 선택합니다.

       ![관리 단위에 사용자를 할당하기 위한 "관리 단위" 창의 스크린샷.](./media/admin-units-add-manage-users/assign-users-individually.png)

- 관리 단위에서 개별 사용자 할당:

   1. [Azure Portal](https://portal.azure.com)이나 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

   1. **Azure Active Directory** > **관리 단위** 를 선택한 다음, 사용자를 할당할 관리 단위를 선택합니다.

   1. **모든 사용자** 를 선택하고, **멤버 추가** 를 선택한 다음, **멤버 추가** 창에서 관리 단위에 할당할 하나 이상의 사용자를 선택합니다.

        ![관리 단위에 사용자를 할당하기 위한 관리 단위 "사용자" 창의 스크린샷.](./media/admin-units-add-manage-users/assign-to-admin-unit.png)

- 사용자를 대량 작업으로 할당:

   1. [Azure Portal](https://portal.azure.com)이나 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

   1. **Azure Active Directory** > **관리 단위** 를 선택합니다.

   1. 사용자를 추가할 관리 단위를 선택합니다.

   1. **사용자** > **대량 작업** > **대량 멤버 추가** 를 선택합니다. 그런 다음, CSV(쉼표로 구분된 값) 템플릿을 다운로드하고 파일을 편집할 수 있습니다. 형식은 간단하며 각 줄에 단일 사용자 계정 이름을 추가해야 합니다. 파일이 준비된 이후 적절한 위치에 저장한 다음, 이 단계의 일부로 업로드합니다.

      ![사용자를 관리 단위에 대량 작업으로 할당하기 위한 "사용자" 창의 스크린샷.](./media/admin-units-add-manage-users/bulk-assign-to-admin-unit.png)

### <a name="powershell"></a>PowerShell

PowerShell에서 다음 예제의 `Add-AzureADAdministrativeUnitMember` cmdlet을 사용하여 사용자를 관리 단위에 추가합니다. 사용자를 추가하려는 관리 단위의 개체 ID와 추가하려는 사용자의 개체 ID를 인수로 가져옵니다. 강조 표시된 섹션을 특정 환경의 요구에 따라 변경합니다.

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'Test administrative unit 2'"
$userObj = Get-AzureADUser -Filter "UserPrincipalName eq 'bill@example.onmicrosoft.com'"
Add-AzureADMSAdministrativeUnitMember -Id $adminUnitObj.Id -RefObjectId $userObj.ObjectId
```


### <a name="microsoft-graph-api"></a>Microsoft Graph API

자리 표시자를 테스트 정보로 바꾸고 다음 명령을 실행합니다.

요청

```http
POST /administrativeUnits/{admin-unit-id}/members/$ref
```

본문

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/{user-id}"
}
```

예제

```http
{
  "@odata.id":"https://graph.microsoft.com/v1.0/users/john@example.com"
}
```

## <a name="view-a-list-of-administrative-units-for-a-user"></a>사용자에 대한 관리 단위 목록 보기

### <a name="azure-portal"></a>Azure portal

Azure Portal에서 다음을 수행하여 사용자 프로필을 열 수 있습니다.

1. **Azure AD** 로 이동한 다음, **사용자** 를 선택합니다.

1. 프로필을 보려는 사용자를 선택합니다.

1. **관리 단위** 를 선택하여 사용자가 할당된 관리 단위 목록을 표시합니다.

   ![사용자가 할당된 관리 단위의 스크린샷.](./media/admin-units-add-manage-users/list-user-admin-units.png)

### <a name="powershell"></a>PowerShell

다음 명령을 실행합니다.

```powershell
Get-AzureADMSAdministrativeUnit | where { Get-AzureADMSAdministrativeUnitMember -Id $_.ObjectId | where {$_.RefObjectId -eq $userObjId} }
```

> [!NOTE]
> 기본적으로 `Get-AzureADAdministrativeUnitMember`는 100개의 관리 단위 멤버만 반환합니다. 더 많은 멤버를 검색하기 위해 `"-All $true"`를 추가할 수 있습니다.

### <a name="microsoft-graph-api"></a>Microsoft Graph API

자리 표시자를 테스트 정보로 바꾸고 다음 명령을 실행합니다.

```http
https://graph.microsoft.com/v1.0/users/{user-id}/memberOf/$/Microsoft.Graph.AdministrativeUnit
```

## <a name="remove-a-single-user-from-an-administrative-unit"></a>관리 단위에서 단일 사용자 제거

### <a name="azure-portal"></a>Azure portal

다음 두 가지 방법 중 하나를 사용하여 관리 단위에서 사용자를 제거할 수 있습니다. 

* Azure Portal에서 **Azure AD** 로 이동한 다음, **관리 단위** 를 선택합니다. 
  1. 사용자를 선택하여 사용자의 프로필을 엽니다. 
  1. 사용자를 제거하려는 관리 단위를 선택한 다음, **관리 단위에서 제거** 를 선택합니다.

     ![사용자의 프로필 창에서 관리 단위에서 사용자를 제거하는 방법을 보여주는 스크린샷.](./media/admin-units-add-manage-users/user-remove-admin-units.png)

* Azure Portal에서 **Azure AD** 로 이동한 다음 **관리 단위** 를 선택합니다.
  1. 사용자를 제거할 관리 단위를 선택합니다. 
  1. 사용자를 선택한 다음, **멤버 제거** 를 선택합니다.
  
     ![관리 단위 수준에서 사용자를 제거하는 방법을 보여주는 스크린샷.](./media/admin-units-add-manage-users/admin-units-remove-user.png)

### <a name="powershell"></a>PowerShell

다음 명령을 실행합니다.

```powershell
Remove-AzureADMSAdministrativeUnitMember -Id $adminUnitId -MemberId $memberUserObjId
```

### <a name="microsoft-graph-api"></a>Microsoft Graph API

자리 표시자를 테스트 정보로 바꾸고 다음 명령을 실행합니다.

```http
https://graph.microsoft.com/v1.0/directory/administrativeUnits/{admin-unit-id}/members/{user-id}/$ref
```

## <a name="remove-multiple-users-as-a-bulk-operation"></a>대량 작업으로 여러 사용자 제거

관리 단위에서 여러 사용자를 제거하려면 다음을 수행합니다.

1. Azure Portal에서 **Azure AD** 로 이동합니다.

1. **관리 단위** 를 선택하고 사용자를 제거할 관리 단위를 선택합니다. 

1. **멤버 대량 제거** 를 선택하고 제거할 사용자를 나열하는 데 사용할 CSV 템플릿을 다운로드합니다.

   !["사용자" 창에서 "멤버 대량 제거" 링크를 보여주는 스크린샷.](./media/admin-units-add-manage-users/bulk-user-remove.png)

1. 다운로드한 CSV 템플릿을 관련 사용자 항목으로 편집합니다. 템플릿의 처음 2개 행을 제거하지 마세요. 각 행에 UPN(사용자 계정 이름)을 하나 추가합니다.

   ![대량으로 관리 단위에서 사용자를 제거하기 위한 편집된 CSV 파일의 스크린샷.](./media/admin-units-add-manage-users/bulk-user-entries.png)

1. 변경 내용을 저장하고 파일을 업로드한 다음, **제출** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [관리 단위에 역할 할당](admin-units-assign-roles.md)
- [관리 단위에 그룹 추가](admin-units-add-manage-groups.md)
