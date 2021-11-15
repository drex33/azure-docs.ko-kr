---
title: Azure AD 권한 관리에서 액세스 패키지에 대한 할당 보기, 추가, 제거 - Azure Active Directory
description: Azure Active Directory 권한 관리에서 액세스 패키지에 대한 할당을 확인, 추가, 제거하는 방법을 알아봅니다.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 10/05/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f944caecae6d35e680f5c5beb1a6e23fc422e698
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618096"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Azure AD 권한 관리에서 액세스 패키지에 대한 할당 보기, 추가, 제거

Azure AD 권한 관리에서 액세스 패키지에 할당된 사용자, 해당 정책, 상태를 확인할 수 있습니다. 액세스 패키지에 적절한 정책이 있는 경우 액세스 패키지에 직접 사용자를 할당할 수도 있습니다. 이 문서에서는 액세스 패키지에 대한 할당을 확인, 추가, 제거하는 방법을 설명합니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure AD 권한 관리를 사용하고 액세스 패키지에 사용자를 할당하려면 다음 라이선스 중 하나가 있어야 합니다.

- Azure AD Premium P2
- EMS(Enterprise Mobility + Security) E5 라이선스

## <a name="view-who-has-an-assignment"></a>할당이 있는 사용자 보기

**필수 역할:** 전역 관리자, Identity Governance 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. **할당** 을 클릭하여 활성 할당 목록을 확인합니다.

    ![액세스 패키지에 대한 할당 목록](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. 특정 할당을 클릭하여 추가 세부 정보를 확인합니다.

1. 모든 리소스 역할이 제대로 프로비저닝되지 않은 할당 목록을 보려면 필터 상태를 클릭하고 **배달 중** 을 선택합니다.

    **요청** 페이지에서 사용자의 해당 요청을 찾아 배달 오류에 대한 추가 세부 정보를 확인할 수 있습니다.

1. 만료된 할당을 보려면 필터 상태를 클릭하고 **만료됨** 을 선택합니다.

1. 필터링된 목록의 CSV 파일을 다운로드하려면 **다운로드** 를 클릭합니다.

## <a name="view-assignments-programmatically"></a>프로그래밍 방식으로 할당 보기
### <a name="view-assignments-with-microsoft-graph"></a>Microsoft Graph로 할당 보기
Microsoft Graph를 사용하여 액세스 패키지에서 할당을 검색할 수도 있습니다.  위임된 `EntitlementManagement.Read.All` 또는 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션의 적절한 역할 사용자는 API를 호출하여 [accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true)를 나열할 수 있습니다. ID 거버넌스 관리자가 여러 카탈로그에서 액세스 패키지를 검색하는 중에, 사용자가 카탈로그별로 위임된 관리 역할에만 할당된 경우 요청에서 `$filter=accessPackage/id eq 'a914b616-e04e-476b-aa37-91038f0b165b'`처럼 특정 액세스 패키지를 표시하는 필터를 제공해야 합니다. 애플리케이션 권한 `EntitlementManagement.Read.All` 또는 `EntitlementManagement.ReadWrite.All`이 있는 애플리케이션도 이 API를 사용할 수 있습니다.

### <a name="view-assignments-with-powershell"></a>PowerShell로 할당 보기

[Identity Governance용 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 모듈 버전 1.6.0 이상에서 `Get-MgEntitlementManagementAccessPackageAssignment` cmdlet을 사용하여 PowerShell에서 이 쿼리를 수행할 수 있습니다. 이 cmdlet은 액세스 패키지 ID 매개 변수를 사용하는데, `Get-MgEntitlementManagementAccessPackage` cmdlet의 응답에 포함되어 있습니다.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -ExpandProperty target -All -ErrorAction Stop
$assignments | ft Id,AssignmentState,TargetId,{$_.Target.DisplayName}
```

## <a name="directly-assign-a-user"></a>사용자 직접 할당 

경우에 따라 사용자가 액세스 패키지를 요청하는 프로세스를 진행할 필요가 없도록 특정 사용자를 액세스 패키지에 직접 할당할 수 있습니다. 사용자를 직접 할당하려면 액세스 패키지에 관리자 직접 할당을 허용하는 정책이 있어야 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭합니다.

1. **새 할당** 을 클릭하여 액세스 패키지에 사용자 추가를 엽니다.

    ![할당 - 액세스 패키지에 사용자 추가](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1.  **정책 선택** 목록에서 사용자의 향후 요청 및 수명 주기를 관리하고 추적하는 데 사용할 정책을 선택합니다. 선택한 사용자에게 다른 정책 설정을 사용하려는 경우 **새 정책 만들기** 를 클릭하여 새 정책을 추가할 수 있습니다.

1.  정책을 선택한 후에는 사용자를 추가하여, 선택한 정책에서 이 액세스 패키지를 할당할 사용자를 선택할 수 있습니다.

    > [!NOTE]
    > 질문을 포함한 정책을 선택할 경우 한 번에 한 사용자만 할당할 수 있습니다.

1. 선택한 사용자의 할당을 시작하고 종료할 날짜 및 시간을 설정합니다. 종료 날짜를 지정하지 않으면 정책의 수명 주기 설정이 사용됩니다.

1.  필요에 따라 레코드 보관을 위해 직접 할당의 사유를 제공합니다.

1.  선택한 정책이 추가적인 요청자 정보를 포함할 경우 **질문 보기** 를 클릭하여 사용자를 대신에 질문에 답한 다음, **저장** 을 클릭합니다.  

     ![할당 - 질문 보기 클릭](./media/entitlement-management-access-package-assignments/assignments-view-questions.png)

    ![할당 - 질문 창](./media/entitlement-management-access-package-assignments/assignments-questions-pane.png)

1. **추가** 를 클릭하여 선택한 사용자를 액세스 패키지에 직접 할당합니다.

    잠시 후에 **새로 고침** 을 클릭하여 할당 목록에서 사용자를 확인합니다.
    
> [!NOTE]
> 사용자를 액세스 패키지에 할당하는 경우 관리자는 기존 정책 요구 사항에 따라 사용자가 해당 액세스 패키지에 적합한지 확인해야 합니다. 그렇지 않으면 사용자가 액세스 패키지에 성공적으로 할당되지 않습니다. 액세스 패키지가 사용자 요청 승인을 요구하는 정책을 포함하는 경우 사용자는 지정된 승인자로부터 필요한 승인 없이 패키지에 직접 할당될 수 없습니다.

## <a name="directly-assign-any-user-preview"></a>모든 사용자 직접 할당(미리 보기)
Azure AD 권한 관리를 사용하면 외부 사용자를 액세스 패키지에 직접 할당하여 파트너와 더 쉽게 협업할 수도 있습니다. 이렇게 하려면 액세스 패키지에 아직 디렉터리에 없는 사용자가 액세스를 요청할 수 있는 정책이 있어야 합니다.

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1.  Azure Portal에서 **Azure Active Directory** 를 선택한 다음, **ID 거버넌스** 를 선택합니다.

1.  왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음, 사용자를 추가할 액세스 패키지를 엽니다.

1.  왼쪽 메뉴에서 **할당** 을 클릭합니다.

1.  **새 할당** 을 선택하여 **액세스 패키지에 사용자 추가** 를 엽니다.

1.  **정책 선택** 목록에서 **디렉터리에 없는 사용자용** 으로 설정된 정책을 선택합니다.

1. **모든 사용자** 를 선택합니다. 이 액세스 패키지에 할당할 사용자를 지정할 수 있습니다.
    ![할당 - 액세스 패키지에 사용자 추가](./media/entitlement-management-access-package-assignments/assignments-add-any-user.png)

1. 사용자의 **이름**(선택 사항) 및 사용자의 **이메일 주소**(필수)를 입력합니다.

    > [!NOTE]
    > - 추가하려는 사용자는 정책 범위 내에 있어야 합니다. 예를 들어 정책이 **연결된 특정 조직** 으로 설정된 경우 사용자의 이메일 주소는 선택한 조직의 도메인에 있어야 합니다. 추가하려는 사용자에게 jen@*foo.com* 이메일 주소가 있지만 선택한 조직의 도메인이 *bar.com* 인 경우 해당 사용자를 액세스 패키지에 추가할 수 없습니다.
    > - 마찬가지로 **구성된 모든 연결된 조직** 을 포함하도록 정책을 설정한 경우 사용자의 이메일 주소는 구성된 연결 조직 중 하나에서 가져온 주소여야 합니다. 그렇지 않으면 사용자가 액세스 패키지에 추가되지 않습니다.
    > - 액세스 패키지에 사용자를 추가하려는 경우 정책을 구성할 때 **모든 사용자(연결된 모든 조직 + 외부 사용자)** 를 선택해야 합니다.

1.  선택한 사용자의 할당을 시작하고 종료할 날짜 및 시간을 설정합니다. 종료 날짜를 지정하지 않으면 정책의 수명 주기 설정이 사용됩니다.
1.  **추가** 를 클릭하여 선택한 사용자를 액세스 패키지에 직접 할당합니다.
1.  잠시 후에 **새로 고침** 을 클릭하여 할당 목록에서 사용자를 확인합니다.

## <a name="directly-assigning-users-programmatically"></a>프로그래밍 방식으로 사용자 직접 할당
### <a name="assign-a-user-to-an-access-package-with-microsoft-graph"></a>Microsoft Graph로 액세스 패키지에 사용자 할당
Microsoft Graph를 사용하여 액세스 패키지에 사용자를 직접 할당할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션 또는 해당 애플리케이션 권한이 있는 애플리케이션에서 적절한 역할이 있는 사용자는 API를 호출하여 [accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)를 만들 수 있습니다. 이 요청에서 `requestType` 속성의 값은 `AdminAdd`고, `accessPackageAssignment` 속성은 할당될 사용자의 `targetId`를 포함하는 구조입니다.

### <a name="assign-a-user-to-an-access-package-with-powershell"></a>PowerShell로 액세스 패키지에 사용자 할당

[Identity Governance용 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 모듈 버전 1.6.0 이상에서 `New-MgEntitlementManagementAccessPackageAssignmentRequest` cmdlet을 사용하여 PowerShell에서 액세스 패키지에 사용자를 할당할 수 있습니다. 이 cmdlet은 다음과 같은 매개 변수를 사용합니다.
* `Get-MgEntitlementManagementAccessPackage` cmdlet의 응답에 포함된 액세스 패키지 ID
* `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` cmdlet의 응답에 포함된 액세스 패키지 할당 정책 ID
* 대상 사용자의 개체 ID.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -TargetId "a43ee6df-3cc5-491a-ad9d-ea964ef8e464"
```

[Identity Governance용 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 모듈 버전 1.6.1 이상에서 `New-MgEntitlementManagementAccessPackageAssignment` cmdlet을 사용하여 PowerShell에서 액세스 패키지에 여러 사용자를 할당할 수도 있습니다. 이 cmdlet은 다음과 같은 매개 변수를 사용합니다.
* `Get-MgEntitlementManagementAccessPackage` cmdlet의 응답에 포함된 액세스 패키지 ID
* `Get-MgEntitlementManagementAccessPackageAssignmentPolicy` cmdlet의 응답에 포함된 액세스 패키지 할당 정책 ID
* 대상 사용자의 개체 ID로, 문자열 배열 또는 `Get-MgGroupMember` cmdlet에서 반환한 사용자 멤버 목록 형태입니다.

예를 들어 현재 그룹 멤버인 모든 사용자가 액세스 패키지도 할당 받게 하려면 이 cmdlet을 사용하여 현재 할당이 없는 사용자에 대한 요청을 만들 수 있습니다.  이 cmdlet은 할당을 만들기만 하며 제거하지는 않습니다.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All,Directory.Read.All"
Select-MgProfile -Name "beta"
$members = Get-MgGroupMember -GroupId "a34abd69-6bf8-4abd-ab6b-78218b77dc15"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -RequiredGroupMember $members
```

## <a name="remove-an-assignment"></a>할당 제거

**필수 역할:** 전역 관리자, 사용자 관리자, 카탈로그 소유자, 액세스 패키지 관리자 또는 액세스 패키지 할당 관리자

1. Azure Portal에서 **Azure Active Directory** 를 클릭한 다음, **Identity Governance** 를 클릭합니다.

1. 왼쪽 메뉴에서 **액세스 패키지** 를 클릭한 다음 액세스 패키지를 엽니다.

1. 왼쪽 메뉴에서 **할당** 을 클릭합니다.
 
1. 액세스 패키지에서 할당을 제거할 사용자 옆에 있는 확인란을 클릭합니다. 

1. 왼쪽 창의 위쪽에 있는 **제거** 단추를 클릭합니다. 
 
    ![할당 - 액세스 패키지에서 사용자 제거](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    할당이 제거되었다는 알림이 표시됩니다. 

## <a name="remove-an-assignment-programmatically"></a>프로그래밍 방식으로 할당 제거
### <a name="remove-an-assignment-with-microsoft-graph"></a>Microsoft Graph로 할당 제거
Microsoft Graph를 사용하여 액세스 패키지에 대한 사용자 할당을 제거할 수도 있습니다.  위임된 `EntitlementManagement.ReadWrite.All` 권한이 있는 애플리케이션 또는 해당 애플리케이션 권한이 있는 애플리케이션에서 적절한 역할이 있는 사용자는 API를 호출하여 [accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true)를 만들 수 있습니다.  이 요청에서 `requestType` 속성의 값은 `AdminRemove`고, `accessPackageAssignment` 속성은 `accessPackageAssignment` 제거를 식별하는 `id` 속성을 포함하는 구조입니다.

### <a name="remove-an-assignment-with-powershell"></a>PowerShell로 할당 제거

[Identity Governance용 Microsoft Graph PowerShell cmdlet](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) 모듈 버전 1.6.0 이상에서 `New-MgEntitlementManagementAccessPackageAssignmentRequest` cmdlet을 사용하여 PowerShell에서 사용자의 할당을 제거할 수 있습니다.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -Filter "accessPackageId eq '9f573551-f8e2-48f4-bf48-06efbb37c7b8' and assignmentState eq 'Delivered'" -All -ErrorAction Stop
$toRemove = $assignments | Where-Object {$_.targetId -eq '76fd6e6a-c390-42f0-879e-93ca093321e7'}
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageAssignmentId $toRemove.Id -RequestType "AdminRemove"
```

## <a name="next-steps"></a>다음 단계

- [액세스 패키지의 요청 및 설정 변경](entitlement-management-access-package-request-policy.md)
- [보고서 및 로그 보기](entitlement-management-reports.md)
