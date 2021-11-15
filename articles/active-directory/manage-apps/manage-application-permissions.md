---
title: 애플리케이션에 부여된 사용 권한 검토
titleSuffix: Azure AD
description: Azure Active Directory에서 애플리케이션에 대한 권한을 검토하고 관리하는 방법을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.collection: M365-identity-device-management
ms.openlocfilehash: 038955a1c7b4a15b2b0ae630c95c2833f32a3eab
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131471177"
---
# <a name="review-permissions-granted-to-applications-in-azure-active-directory"></a>Azure Active Directory에서 애플리케이션에 부여된 사용 권한을 검토합니다.

이 문서에서는 Azure AD(Azure Active Directory) 테넌트의 애플리케이션에 부여된 권한을 검토하는 방법을 알아봅니다. 악의적인 애플리케이션을 검색했거나 애플리케이션에 필요한 것보다 더 많은 권한이 부여된 경우 사용 권한을 검토해야 할 수 있습니다.

이 문서의 단계는 사용자 또는 관리자 동의를 통해 Azure AD(Azure Active Directory) 테넌트에 추가된 모든 애플리케이션에 적용됩니다. 애플리케이션에 동의하는 방법에 대한 자세한 내용은 [Azure Active Directory 동의 프레임워크](../develop/consent-framework.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

애플리케이션에 부여된 사용 권한을 검토하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.

Azure AD 포털에 액세스하여 작업을 수행할 상황별 PowerShell 스크립트를 가져올 수 있습니다.

## <a name="review-application-permissions"></a>애플리케이션 권한 검토

애플리케이션 권한을 검토하려면 다음을 수행합니다.

1. 필수 구성 요소 섹션에 나열된 역할 중 하나를 사용하여 [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. **Azure Active Directory** 로 이동한 다음, **엔터프라이즈 애플리케이션** 을 선택합니다.
1. 액세스를 제한할 애플리케이션을 선택합니다.
1. **권한** 을 선택합니다. 명령 모음에서 **사용 권한 검토** 를 선택합니다.
![사용 권한 검토 창의 스크린샷.](./media/manage-application-permissions/review-permissions.png)
1. **이 애플리케이션에 대한 사용 권한을 검토하려는 이유는 무엇인가요?** 질문 뒤에 나열된 옵션을 선택하여 애플리케이션에 대한 사용 권한을 검토하려는 이유를 지정합니다.

각 옵션은 애플리케이션에 대한 사용자 액세스를 제어하고 애플리케이션에 부여된 권한을 검토할 수 있는 PowerShell 스크립트를 생성합니다. 애플리케이션에 대한 사용자 액세스를 제어하는 방법에 대한 자세한 내용은 [애플리케이션에 대한 사용자 액세스를 제거하는 방법](methods-for-removing-user-access.md)을 참조하세요.

## <a name="revoke-permissions-using-powershell-commands"></a>PowerShell 명령을 사용하여 사용 권한 취소

다음 PowerShell 스크립트를 사용하여 이 애플리케이션에 부여된 모든 사용 권한을 취소합니다.

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get all delegated permissions for the service principal
$spOAuth2PermissionsGrants = Get-AzureADOAuth2PermissionGrant -All $true| Where-Object { $_.clientId -eq $sp.ObjectId }

# Remove all delegated permissions
$spOAuth2PermissionsGrants | ForEach-Object {
    Remove-AzureADOAuth2PermissionGrant -ObjectId $_.ObjectId
}

# Get all application permissions for the service principal
$spApplicationPermissions = Get-AzureADServiceAppRoleAssignedTo -ObjectId $sp.ObjectId -All $true | Where-Object { $_.PrincipalType -eq "ServicePrincipal" }

# Remove all delegated permissions
$spApplicationPermissions | ForEach-Object {
    Remove-AzureADServiceAppRoleAssignment -ObjectId $_.PrincipalId -AppRoleAssignmentId $_.objectId
}
```

> [!NOTE]
> 현재 부여된 사용 권한을 취소하더라도 사용자는 애플리케이션에 다시 동의해야 합니다. 사용자 동의를 차단하려면 [사용자가 애플리케이션에 동의하는 방법 구성](configure-user-consent.md)을 참조하세요.

## <a name="invalidate-the-refresh-tokens"></a>새로 고침 토큰 무효화

```powershell
Connect-AzureAD

# Get Service Principal using objectId
$sp = Get-AzureADServicePrincipal -ObjectId "<ServicePrincipal objectID>"

# Get Azure AD App role assignments using objectID of the Service Principal
$assignments = Get-AzureADServiceAppRoleAssignment -ObjectId $sp.ObjectId -All $true | Where-Object {$_.PrincipalType -eq "User"}

# Revoke refresh token for all users assigned to the application
$assignments | ForEach-Object {
    Revoke-AzureADUserAllRefreshToken -ObjectId $_.PrincipalId
}
```

## <a name="next-steps"></a>다음 단계

- [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
