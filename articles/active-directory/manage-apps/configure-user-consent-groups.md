---
title: Azure AD를 사용해 그룹 데이터에 액세스하는 앱에 대한 그룹 소유자 동의 구성
description: 그룹 및 팀 소유자가 그룹 또는 팀의 데이터에 액세스할 수 있는 애플리케이션에 동의할 수 있는지를 관리하는 방법을 알아봅니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 05/19/2020
ms.author: davidmu
ms.reviewer: arvindh, luleon, phsignor
ms.custom: contperf-fy21q2
ms.openlocfilehash: 1ce7254f92c4cc3d2a7dcf77885be88eda37099b
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567993"
---
# <a name="configure-group-owner-consent-to-apps-accessing-group-data"></a>그룹 데이터에 액세스하는 앱에 대한 그룹 소유자 동의 구성

그룹 및 팀 소유자는 타사 공급업체에서 게시한 애플리케이션과 같은 애플리케이션에 권한을 부여하여 그룹과 연결된 조직의 데이터에 액세스하도록 할 수 있습니다. 예를 들어 Microsoft 팀 소유자는 앱이 팀의 모든 팀 메시지를 읽거나 그룹 멤버의 기본 프로필을 나열하도록 허용할 수 있습니다. 자세히 알아보려면 [Microsoft Teams의 리소스 관련 동의](/microsoftteams/resource-specific-consent)를 참조하세요.

## <a name="manage-group-owner-consent-to-apps"></a>앱에 대한 그룹 소유자의 동의 관리

해당 그룹 또는 팀의 데이터에 액세스하는 앱에 동의할 수 있는 사용자를 구성하거나 모든 사용자가 이 기능을 사용하지 않도록 설정할 수 있습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

그룹 데이터에 액세스하는 앱에 대한 그룹 소유자의 동의를 관리하려면 다음 단계를 따르세요.

1. [전역 관리자](../roles/permissions-reference.md#global-administrator)로 [Azure Portal](https://portal.azure.com)에 로그인합니다.
2. **Azure Active Directory** > **엔터프라이즈 애플리케이션** > **동의 및 권한** > **사용자 동의 설정** 을 선택합니다.
3. **데이터에 액세스하는 앱에 대한 그룹 소유자 동의** 아래에서 사용하도록 설정하려는 옵션을 선택합니다.
4. **저장** 을 선택하여 설정을 저장합니다.

이 예제에서 모든 그룹 소유자는 해당 그룹의 데이터에 액세스하는 앱에 동의할 수 있습니다.

:::image type="content" source="media/configure-user-consent-groups/group-owner-consent.png" alt-text="그룹 소유자 동의 설정":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure AD PowerShell Preview 모듈 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview)를 사용하여 소유하고 있는 그룹의 조직 데이터에 액세스하는 애플리케이션에 동의하는 그룹 소유자의 권한을 허용하거나 허용하지 않을 수 있습니다.

1. [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 모듈을 사용하고 있는지 확인하세요. [AzureAD](/powershell/module/azuread/) 모듈 및 [AzureADPreview](/powershell/module/azuread/?preserve-view=true&view=azureadps-2.0-preview) 모듈을 모두 설치한 경우 이 단계가 중요합니다.

    ```powershell
    Remove-Module AzureAD
    Import-Module AzureADPreview
    ```

1. Azure AD PowerShell에 연결합니다.

   ```powershell
   Connect-AzureAD
   ```

1. 테넌트에서 **동의 정책 설정** 디렉터리 설정의 현재 값을 검색합니다. 그러기 위해서는 이 기능의 디렉터리 설정이 생성되었는지 확인하고, 생성되지 않았다면 해당하는 디렉터리 설정 템플릿의 값을 사용해야 합니다.

    ```powershell
    $consentSettingsTemplateId = "dffd5d46-495d-40a9-8e21-954ff55e198a" # Consent Policy Settings
    $settings = Get-AzureADDirectorySetting -All $true | Where-Object { $_.TemplateId -eq $consentSettingsTemplateId }

    if (-not $settings) {
        $template = Get-AzureADDirectorySettingTemplate -Id $consentSettingsTemplateId
        $settings = $template.CreateDirectorySetting()
    }

    $enabledValue = $settings.Values | ? { $_.Name -eq "EnableGroupSpecificConsent" }
    $limitedToValue = $settings.Values | ? { $_.Name -eq "ConstrainGroupSpecificConsentToMembersOfGroupId" }
    ```

1. 설정 값을 이해합니다. 앱에서 해당 그룹의 데이터에 액세스하도록 허용할 수 있는 사용자를 정의하는 두 가지 설정 값이 있습니다.

    | 설정       | 유형         | Description  |
    | ------------- | ------------ | ------------ |
    | _EnableGroupSpecificConsent_   | 부울 | 그룹 소유자가 그룹 관련 권한을 부여할 수 있는지 여부를 나타내는 플래그입니다. |
    | _ConstrainGroupSpecificConsentToMembersOfGroupId_ | Guid | _EnableGroupSpecificConsent_ 를 "True"로 설정하고 이 값을 그룹의 개체 ID로 설정하면 식별된 그룹의 멤버가 자신이 소유한 그룹에 대한 그룹 관련 권한을 부여할 수 있습니다. |

1. 원하는 구성의 설정 값을 업데이트합니다.

    ```powershell
    # Disable group-specific consent entirely
    $enabledValue.Value = "False"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for all users
    $enabledValue.Value = "True"
    $limitedToValue.Value = ""
    ```

    ```powershell
    # Enable group-specific consent for users in a given group
    $enabledValue.Value = "True"
    $limitedToValue.Value = "{group-object-id}"
    ```

1. 설정을 저장합니다.

    ```powershell
    if ($settings.Id) {
        # Update an existing directory settings
        Set-AzureADDirectorySetting -Id $settings.Id -DirectorySetting $settings
    } else {
        # Create a new directory settings to override the default setting 
        New-AzureADDirectorySetting -DirectorySetting $settings
    }
    ```

---

> [!NOTE]
> "사용자를 대신하여 회사 데이터에 액세스하는 앱에 동의할 수 있습니다." 설정을 끄면 "사용자가 소유한 그룹의 회사 데이터에 액세스하는 앱에 동의할 수 있습니다." 옵션을 사용하지 않도록 설정합니다.

## <a name="next-steps"></a>다음 단계

자세히 알아보려면 다음을 수행합니다.

* [사용자 동의 설정 구성](configure-user-consent.md)
* [관리자 동의 워크플로 구성](configure-admin-consent-workflow.md)
* [애플리케이션에 대한 동의를 관리하고 동의 요청을 평가하는 방법 알아보기](manage-consent-requests.md)
* [애플리케이션에 대한 테넌트 전체 관리자 동의 부여](grant-admin-consent.md)
* [Microsoft ID 플랫폼의 권한 및 동의](../develop/v2-permissions-and-consent.md)

도움말을 얻거나 질문에 대한 답변을 찾으려면 다음을 수행합니다.

* [Microsoft Azure AD Q&A](/answers/topics/azure-active-directory.html)
