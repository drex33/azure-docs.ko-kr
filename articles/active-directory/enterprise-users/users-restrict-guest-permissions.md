---
title: 게스트 사용자 액세스 권한 제한 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory에서 Azure Portal, PowerShell 또는 Microsoft Graph를 사용하여 게스트 사용자 액세스 권한 제한
services: active-directory
author: curtand
ms.author: curtand
manager: KarenH444
ms.date: 06/01/2021
ms.topic: how-to
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.custom: it-pro
ms.reviewer: krbain
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ca9c66216f56c46e4232ef688641941e47f1072
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129985241"
---
# <a name="restrict-guest-access-permissions-in-azure-active-directory"></a>Azure Active Directory에서 게스트 액세스 권한 제한

Azure AD(Azure Active Directory)를 사용하면 외부 게스트 사용자가 Azure AD의 조직에서 볼 수 있는 항목을 제한할 수 있습니다. 게스트 사용자는 기본적으로 Azure AD에서 제한된 권한 수준으로 설정되지만 멤버 사용자의 기본값은 전체 사용자 권한 세트입니다. 이는 훨씬 더 제한된 액세스를 위한 Azure AD 조직의 외부 협업 설정에 있는 새 게스트 사용자 권한 수준이므로 게스트 액세스 수준은 다음과 같습니다.

권한 수준         | 액세스 수준 | 값
----------------         | ------------ | -----
멤버 사용자와 동일     | 게스트가 Azure AD 리소스에 대해 멤버 사용자와 동일한 액세스 권한을 가집니다. | a0b1b346-4d3e-4e8b-98f8-753987be4970
제한된 액세스(기본값) | 게스트가 숨겨지지 않은 모든 그룹의 멤버 자격을 볼 수 있습니다. | 10dae51f-b6af-4016-8d66-8c2a99b929b3
**제한된 액세스(신규)**  | **게스트가 그룹의 멤버 자격을 볼 수 없습니다.** | **2af84b1e-32c8-42b7-82bc-daa82404023b**

게스트 액세스가 제한된 경우 게스트는 자신의 사용자 프로필만 볼 수 있습니다. 게스트가 사용자 계정 이름 또는 objectId를 기준으로 검색하는 경우에도 다른 사용자를 볼 권한이 허용되지 않습니다. 또한 제한된 액세스는 게스트 사용자가 속해 있는 그룹의 멤버 자격을 보는 것을 제한합니다. 게스트 사용자 권한을 비롯한 전체 기본 사용자 권한에 관한 자세한 내용은 [Azure Active Directory의 기본 사용자 권한은 무엇인가요?](../fundamentals/users-default-permissions.md)를 참조하세요.

## <a name="permissions-and-licenses"></a>권한 및 라이선스

게스트 사용자 액세스를 구성하려면 전역 관리자 또는 권한 있는 역할 관리자 역할에 속해야 합니다. 게스트 액세스를 제한하기 위한 추가 라이선스 요구 사항은 없습니다.

## <a name="update-in-the-azure-portal"></a>Azure Portal에서 업데이트

기존 Azure Portal 컨트롤의 게스트 사용자 권한을 변경했습니다.

1. 전역 관리자 권한으로 [Azure AD 관리 센터](https://aad.portal.azure.com)에 로그인합니다.
1. 조직의 **Azure Active Directory** 개요 페이지에서 **사용자 설정** 을 선택합니다.
1. **외부 사용자** 에서 **외부 협업 설정 관리** 를 선택합니다.
1. **외부 협업 설정** 페이지에서 **게스트 사용자 액세스는 고유한 디렉터리 개체의 속성 및 멤버 자격으로 제한됩니다.** 옵션을 선택합니다.

    ![Azure AD 외부 협업 설정 페이지](./media/users-restrict-guest-permissions/external-collaboration-settings.png)

1. **저장** 을 선택합니다. 변경 내용이 게스트 사용자에게 적용되는 데 최대 15분이 걸릴 수 있습니다.

## <a name="update-with-the-microsoft-graph-api"></a>Microsoft Graph API를 사용하여 업데이트

Azure AD 조직에서 게스트 권한을 구성하기 위해 새로운 Microsoft Graph API를 추가했습니다. 다음 API 호출을 사용하여 모든 권한 수준을 할당할 수 있습니다. 여기에 사용되는 guestUserRoleId 값은 가장 제한된 게스트 사용자 설정을 설명하는 것입니다. Microsoft Graph 사용하여 게스트 권한을 설정하는 방법에 관한 자세한 내용은 [authorizationPolicy 리소스 종류](/graph/api/resources/authorizationpolicy)를 참조하세요.

### <a name="configuring-for-the-first-time"></a>처음으로 구성

````PowerShell
POST https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

응답은 성공 204이어야 합니다.

### <a name="updating-the-existing-value"></a>기존 값 업데이트

````PowerShell
PATCH https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy

{
  "guestUserRoleId": "2af84b1e-32c8-42b7-82bc-daa82404023b"
}
````

응답은 성공 204이어야 합니다.

### <a name="view-the-current-value"></a>현재 값 보기

````PowerShell
GET https://graph.microsoft.com/beta/policies/authorizationPolicy/authorizationPolicy
````

예제 응답:

````PowerShell
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#policies/authorizationPolicy/$entity",
    "id": "authorizationPolicy",
    "displayName": "Authorization Policy",
    "description": "Used to manage authorization related settings across the company.",
    "enabledPreviewFeatures": [],
    "guestUserRoleId": "10dae51f-b6af-4016-8d66-8c2a99b929b3",
    "permissionGrantPolicyIdsAssignedToDefaultUserRole": [
        "user-default-legacy"
    ]
}
````

## <a name="update-with-powershell-cmdlets"></a>PowerShell cmdlet을 사용하여 업데이트

이 기능을 사용하여 PowerShell v2 cmdlet을 통해 제한된 권한을 구성하는 기능을 추가했습니다. Get 및 Set PowerShell cmdlet이 버전 2.0.2.85에 게시되었습니다.

### <a name="get-command-get-azureadmsauthorizationpolicy"></a>Get 명령: Get-AzureADMSAuthorizationPolicy

예제:

````PowerShell
PS C:\WINDOWS\system32> Get-AzureADMSAuthorizationPolicy

Id                                                : authorizationPolicy
OdataType                                         :
Description                                       : Used to manage authorization related settings across the company.
DisplayName                                       : Authorization Policy
EnabledPreviewFeatures                            : {}
GuestUserRoleId                                   : 10dae51f-b6af-4016-8d66-8c2a99b929b3
PermissionGrantPolicyIdsAssignedToDefaultUserRole : {user-default-legacy}
````

### <a name="set-command-set-azureadmsauthorizationpolicy"></a>Set 명령: Set-AzureADMSAuthorizationPolicy

예제:

````PowerShell
PS C:\WINDOWS\system32> Set-AzureADMSAuthorizationPolicy -GuestUserRoleId '2af84b1e-32c8-42b7-82bc-daa82404023b'
````

> [!NOTE]
> 요청될 때 authorizationPolicy를 ID로 입력해야 합니다.

## <a name="supported-microsoft-365-services"></a>지원되는 Microsoft 365 서비스

### <a name="supported-services"></a>지원되는 서비스

지원된다는 것은 환경이 예상대로 작동하며, 특히 현재 게스트 환경과 동일함을 의미합니다.

- Teams
- Outlook(OWA)
- SharePoint
- Teams의 Planner
- Planner 웹앱

### <a name="services-currently-not-supported"></a>현재 지원되지 않는 서비스

현재 지원되지 않는 서비스에는 새 게스트 제한 설정에 관한 호환성 문제가 있을 수 있습니다.

- 양식
- Planner 모바일 앱
- Project
- Yammer

## <a name="frequently-asked-questions-faq"></a>질문과 대답(FAQ)

질문 | Answer
-------- | ------
이 권한은 어디에 적용되나요? | 이 디렉터리 수준 권한은 Microsoft Graph, PowerShell v2, Azure Portal 및 My Apps 포털을 포함하여 Azure AD 서비스 및 포털 전체에 적용됩니다. 협업 시나리오에 Microsoft 365 그룹을 이용하는 Microsoft 365 서비스, 특히 Outlook, Microsoft Teams 및 SharePoint에도 영향을 줍니다.
제한된 권한은 게스트가 볼 수 있는 그룹에 어떻게 영향을 주나요? | 기본 또는 제한된 게스트 권한에 관계없이 게스트는 그룹 또는 사용자 목록을 열거할 수 없습니다. 게스트는 권한에 따라 Azure Portal 및 My Apps 포털에서 둘 다 멤버로 속한 그룹을 볼 수 있습니다.<li>**기본 권한**: Azure Portal에서 멤버로 속한 그룹을 찾으려면 게스트는 **모든 사용자** 목록에서 개체 ID를 검색한 다음, **그룹** 을 선택해야 합니다. 여기에서 게스트는 이름, 메일 등과 같은 모든 그룹 세부 정보를 포함하여 멤버로 속한 그룹의 목록을 볼 수 있습니다. 내 앱 포털에서 게스트는 소유하고 있는 그룹과 멤버로 속한 그룹의 목록을 볼 수 있습니다.</li><li>**제한된 게스트 권한**: Azure Portal에서 게스트는 모든 사용자 목록에서 개체 ID를 검색하여 멤버로 속한 그룹 목록을 찾은 다음, 그룹을 선택할 수 있습니다. 그룹에 관한 매우 제한된 세부 정보, 특히 개체 ID만 볼 수 있습니다. 기본적으로 이름 및 메일 열은 비어 있고 그룹 유형은 인식할 수 없음입니다. 내 앱 포털에서 소유하고 있는 그룹 또는 멤버로 속한 그룹의 목록에 액세스할 수 없습니다.</li><br>Graph API에서 제공하는 디렉터리 권한의 더 자세한 비교는 [기본 사용자 권한](../fundamentals/users-default-permissions.md#member-and-guest-users)을 참조하세요.
이 기능이 영향을 주는 내 앱 포털의 부분은 무엇인가요? | 내 앱 포털의 그룹 기능에는 이러한 새 권한이 적용됩니다. 여기에는 내 앱의 그룹 목록 및 그룹 멤버 자격을 볼 수 있는 모든 경로가 포함됩니다. 그룹 타일 가용성은 변경되지 않았습니다. 그룹 타일 가용성은 Azure Portal의 기존 그룹 설정에서 제어됩니다.
해당 권한이 SharePoint 또는 Microsoft Teams 게스트 설정을 재정의하나요? | 아니요. 기존 설정은 여전히 해당 애플리케이션의 환경 및 액세스를 제어합니다. 예를 들어, SharePoint에서 문제를 확인하는 경우 외부 공유 설정을 다시 확인합니다.
Planner 및 Yammer에서 알려진 호환성 문제는 무엇인가요? | <li>권한이 ‘제한됨’으로 설정되면 Planner 모바일 앱에 로그인한 게스트는 해당 플랜 또는 작업에 액세스할 수 없습니다.<li>권한이 ‘제한됨’으로 설정되면 Yammer에 로그인한 게스트는 그룹을 떠날 수 없습니다.
내 테넌트에서 기존 게스트 권한이 변경되나요? | 현재 설정은 변경되지 않았습니다. 기존 설정과 이전 버전과의 호환성을 유지합니다. 변경하려는 시기를 결정합니다.
이 권한이 기본적으로 설정되나요? | 아니요. 기존 기본 권한은 변경되지 않습니다. 필요에 따라 권한을 더 제한적으로 설정할 수 있습니다.
이 기능에 대한 라이선스 요구 사항이 있나요? | 아니요. 이 기능과 관련된 새로운 라이선스 요구 사항은 없습니다.

## <a name="next-steps"></a>다음 단계

- Azure AD의 기존 게스트 권한에 관해 자세히 알아보려면 [Azure Active Directory의 기본 사용자 권한은 무엇인가요?](../fundamentals/users-default-permissions.md)를 참조하세요.
- 게스트 액세스를 제한하기 위한 Microsoft Graph API 메서드를 보려면 [authorizationPolicy 리소스 종류](/graph/api/resources/authorizationpolicy)를 참조하세요.
- 사용자의 모든 액세스를 철회하려면 [Azure AD에서 사용자 액세스 철회](users-revoke-access.md)를 참조하세요.
