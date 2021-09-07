---
title: 관리 단위 추가 및 제거 - Azure Active Directory | Microsoft Docs
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
ms.openlocfilehash: 34a6a353593d733cf50325049cd88034fb16c9ca
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566233"
---
# <a name="manage-administrative-units-in-azure-active-directory"></a>Azure Active Directory의 관리 단위 관리

Azure Active Directory(Azure AD)의 보다 세부적인 관리 제어를 위해 하나 이상의 관리 단위로 제한되는 범위를 사용하여 Azure AD 역할에 사용자를 할당할 수 있습니다.


## <a name="prerequisites"></a>필수 구성 요소

- 각 관리 단위 관리자에 대한 Azure AD Premium P1 또는 P2 라이선스
- 관리 단위 구성원에 대한 Azure AD Free 라이선스
- 권한 있는 역할 관리자 또는 전역 관리자
- PowerShell을 사용하는 경우 AzureAD 모듈
- Microsoft Graph API용 Graph 탐색기 사용 시 관리자 동의

자세한 내용은 [PowerShell 또는 Graph 탐색기를 사용하기 위한 필수 구성 요소](prerequisites.md)를 참조하세요.

## <a name="add-an-administrative-unit"></a>관리 단위 추가

Azure Portal 또는 PowerShell을 사용하여 관리 단위를 추가할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **관리 단위** 를 선택합니다.

    ![Azure AD의 “관리 단위” 링크 스크린샷.](./media/admin-units-manage/nav-to-admin-units.png)

1. 창 상단에 있는 **추가** 단추를 선택한 다음 **이름** 란에 관리 단위의 이름을 입력합니다. 필요에 따라 관리 단위에 대한 설명을 추가합니다.

    ![추가 단추와 관리 단위의 이름을 입력하는 이름 란을 보여 주는 스크린샷.](./media/admin-units-manage/add-new-admin-unit.png)

1. 파란색 **추가** 단추를 선택하여 관리 단위를 확정합니다.

### <a name="powershell"></a>PowerShell

```powershell
Connect-AzureAD
New-AzureADMSAdministrativeUnit -Description "West Coast region" -DisplayName "West Coast"
```

필요에 따라 따옴표 안의 값을 수정할 수 있습니다.

### <a name="microsoft-graph-api"></a>Microsoft Graph API

요청

```http
POST /administrativeUnits
```

본문

```http
{
  "displayName": "North America Operations",
  "description": "North America Operations administration"
}
```

## <a name="remove-an-administrative-unit"></a>관리 단위 제거

Azure AD에서 관리 역할의 범위 단위로 더 이상 필요하지 않은 관리 단위를 제거할 수 있습니다.

### <a name="azure-portal"></a>Azure portal

1. [Azure Portal](https://portal.azure.com) 또는 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.

1. **Azure Active Directory** > **관리 단위** 를 선택합니다.
 
1. 삭제할 관리 단위를 선택하고 **삭제** 를 선택합니다.

1. 관리 단위 삭제를 확인하려면 **예** 를 선택합니다. 관리 단위가 삭제됩니다.

    ![관리 단위 삭제 단추 및 확인 창 스크린샷.](./media/admin-units-manage/select-admin-unit-to-delete.png)

### <a name="powershell"></a>PowerShell

```powershell
$adminUnitObj = Get-AzureADMSAdministrativeUnit -Filter "displayname eq 'DeleteMe Admin Unit'"
Remove-AzureADMSAdministrativeUnit -Id $adminUnitObj.Id
```

특정 환경의 필요에 따라 따옴표 안의 값을 수정할 수 있습니다.

### <a name="microsoft-graph-api"></a>Microsoft Graph API

요청

```http
DELETE /administrativeUnits/{admin-unit-id}
```

본문

```http
{}
```

## <a name="next-steps"></a>다음 단계

* [관리 단위에서 사용 관리](admin-units-add-manage-users.md)
* [관리 단위에 속한 그룹 관리](admin-units-add-manage-groups.md)
