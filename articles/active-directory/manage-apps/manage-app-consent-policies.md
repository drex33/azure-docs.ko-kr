---
title: 앱 동의 정책 관리
description: 기본 제공 및 사용자 지정 앱 동의 정책을 관리하여 동의를 제공할 수 있는 시점을 제어하는 방법을 알아봅니다.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: f9fb2c01a3e6cffd4091049579e570d77cdb35e2
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131459390"
---
# <a name="manage-app-consent-policies-in-azure-active-directory"></a>Azure Active Directory에서 앱 동의 정책 관리

Azure AD PowerShell을 사용하면 앱 동의 정책을 보고 관리할 수 있습니다.

앱 동의 정책은 하나 이상의 ‘포함’ 조건 세트와 0개 이상의 ‘제외’ 조건 세트로 구성됩니다. 앱 동의 정책에서 이벤트를 고려하려면 이벤트가 하나 ‘이상’의 ‘포함’ 조건 세트에 부합해야 하며 ‘어떤’ ‘제외’ 조건 세트에도 부합하지 않아야 합니다. 

각 조건 세트는 여러 조건으로 구성됩니다. 조건 세트에 부합하는 이벤트는 조건 세트의 ‘모든’ 조건에 부합해야 합니다.

ID가 ‘microsoft-’로 시작하는 앱 동의 정책은 기본 제공 정책입니다. 이러한 기본 제공 정책 중 일부는 기존 기본 제공 디렉터리 역할에 사용됩니다. 예를 들어 `microsoft-application-admin` 앱 동의 정책은 애플리케이션 관리자 및 클라우드 애플리케이션 관리자 역할이 테넌트 전체 관리자 동의를 제공할 수 있는 조건을 설명합니다. 기본 제공 정책은 사용자 지정 디렉터리 역할에서 사용하고 사용자 동의 설정을 구성하는 데 사용할 수 있지만 편집하거나 삭제할 수는 없습니다.

## <a name="pre-requisites"></a>필수 구성 요소

1. [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 모듈을 사용하고 있는지 확인하세요. [AzureAD](/powershell/module/azuread/) 모듈 및 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 모듈을 모두 설치한 경우 이 단계가 중요합니다.

    ```powershell
    Remove-Module AzureAD -ErrorAction SilentlyContinue
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell에 연결합니다.

   ```powershell
   Connect-AzureAD
   ```

## <a name="list-existing-app-consent-policies"></a>기존 앱 동의 정책 목록

조직의 기존 앱 동의 정책을 숙지하는 것이 좋습니다.

1. 모든 앱 동의 정책을 나열합니다.

   ```powershell
   Get-AzureADMSPermissionGrantPolicy | ft Id, DisplayName, Description
   ```

1. 정책의 ‘포함’ 조건 세트를 봅니다.

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "includes"
    ```

1. ‘제외’ 조건 세트를 봅니다.

    ```powershell
    Get-AzureADMSPermissionGrantConditionSet -PolicyId "microsoft-application-admin" `
                                             -ConditionSetType "excludes"
    ```

## <a name="create-a-custom-app-consent-policy"></a>사용자 지정 앱 동의 정책 만들기

사용자 지정 앱 동의 정책을 만들려면 다음 단계를 따르세요.

1. 비어 있는 새 앱 동의 정책을 만듭니다.

   ```powershell
   New-AzureADMSPermissionGrantPolicy `
       -Id "my-custom-policy" `
       -DisplayName "My first custom consent policy" `
       -Description "This is a sample custom app consent policy."
   ```

1. ‘포함’ 조건 세트를 추가합니다.

   ```powershell
   # Include delegated permissions classified "low", for apps from verified publishers
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "includes" `
       -PermissionType "delegated" `
       -PermissionClassification "low" `
       -ClientApplicationsFromVerifiedPublisherOnly $true
   ```

   이 단계를 반복하여 ‘포함’ 조건 세트를 추가합니다.

1. 필요에 따라 ‘제외’ 조건 세트를 추가합니다.

   ```powershell
   # Retrieve the service principal for the Azure Management API
   $azureApi = Get-AzureADServicePrincipal -Filter "servicePrincipalNames/any(n:n eq 'https://management.azure.com/')"

   # Exclude delegated permissions for the Azure Management API
   New-AzureADMSPermissionGrantConditionSet `
       -PolicyId "my-custom-policy" `
       -ConditionSetType "excludes" `
       -PermissionType "delegated" `
       -ResourceApplication $azureApi.AppId
   ```

   이 단계를 반복하여 ‘제외’ 조건 세트를 추가합니다.

앱 동의 정책을 만든 후에는 이 정책에 따라 [사용자 동의를 허용](configure-user-consent.md?tabs=azure-powershell#allow-user-consent-subject-to-an-app-consent-policy)할 수 있습니다.

## <a name="delete-a-custom-app-consent-policy"></a>사용자 지정 앱 동의 정책 삭제

1. 다음은 사용자 지정 앱 동의 정책을 삭제하는 방법을 보여줍니다. **이 작업은 취소할 수 없습니다.**

   ```powershell
   Remove-AzureADMSPermissionGrantPolicy -Id "my-custom-policy"
   ```

> [!WARNING]
> 삭제된 앱 동의 정책은 복원할 수 없습니다. 사용자 지정 앱 동의 정책을 실수로 삭제한 경우에는 정책을 다시 만들어야 합니다.

---

### <a name="supported-conditions"></a>지원되는 조건

다음 표에서는 앱 동의 정책에 대해 지원되는 조건 목록을 보여줍니다.

| 조건 | Description|
|:---------------|:----------|
| PermissionClassification | 부여되는 권한에 대한 [권한 분류](configure-permission-classifications.md) 또는 권한 분류에 부합하는 ‘전체’ 권한(분류되지 않은 권한 포함)입니다. 기본값은 ‘전체’입니다. |
| PermissionType | 부여되는 권한의 권한 유형입니다. 애플리케이션 권한(예: 앱 역할)에는 ‘애플리케이션’을, 위임된 권한에는 ‘위임’을 사용합니다. <br><br>**참고**: ‘delegatedUserConsentable’ 값은 API 게시자가 관리자 동의가 필요하도록 구성하지 않은 위임된 권한을 나타냅니다. 이 값은 기본 제공 권한 부여 정책에서는 사용할 수 있지만 사용자 지정 권한 부여 정책에서는 사용할 수 없습니다. 필수 요소. |
| ResourceApplication | 권한이 부여되는 리소스 애플리케이션(예: API)의 **AppId** 또는 리소스 애플리케이션이나 API와 일치하는 ‘한 개 이상’ 애플리케이션입니다. 기본값은 ‘한 개 이상’입니다. |
| 사용 권한 | 일치하는 특정 권한에 대한 권한 ID 목록 또는 한 개 이상의 권한과 일치하는 단일 값 ‘전체’를 포함하는 목록입니다. 기본값은 단일 값 ‘전체’입니다. <ul><li>위임된 권한 ID는 API ServicePrincipal 개체의 **OAuth2Permissions** 속성에서 확인할 수 있습니다.</li><li>애플리케이션 권한 ID는 API ServicePrincipal 개체의 **AppRoles** 속성에서 확인할 수 있습니다.</li></ol> |
| ClientApplicationIds | 일치하는 클라이언트 애플리케이션에 대한 **AppId** 값의 목록 또는 한 개 이상의 클라이언트 애플리케이션과 일치하는 단일 값 ‘전체’를 포함하는 목록입니다. 기본값은 단일 값 ‘전체’입니다. |
| ClientApplicationTenantIds | 클라이언트 애플리케이션이 등록된 Azure Active Directory 테넌트 ID의 목록 또는 한 개 이상의 테넌트에 등록된 클라이언트 앱과 일치하는 단일 값 ‘전체’를 포함하는 목록입니다. 기본값은 단일 값 ‘전체’입니다. |
| ClientApplicationPublisherIds | 클라이언트 애플리케이션의 [확인된 게시자](../develop/publisher-verification-overview.md)에 대한 Microsoft 파트너 네트워크(MPN) ID 목록 또는 한 개 이상의 게시자의 클라이언트 앱과 일치하는 단일 값 ‘전체’가 포함된 목록입니다. 기본값은 단일 값 ‘전체’입니다. |
| ClientApplicationsFromVerifiedPublisherOnly | `$true`클라이언트 애플리케이션에서 [확인된 게시자](../develop/publisher-verification-overview.md)와만 일치하도록 설정합니다. 확인된 게시자가 없는 경우에도 모든 클라이언트 앱에서 일치하도록 `$false`로 설정합니다. 기본값은 `$false`입니다. |

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 수행합니다.

* [사용자 동의 설정 구성](configure-user-consent.md)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [애플리케이션에 대한 동의를 관리하고 동의 요청을 평가하는 방법 알아보기](manage-consent-requests.md)
* [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)

도움말을 얻거나 질문에 대한 답변을 찾으려면 다음을 수행합니다.

* [Microsoft Azure AD Q&A](/answers/products/)
