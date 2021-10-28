---
title: 토큰 수명 설정
titleSuffix: Microsoft identity platform
description: Microsoft ID 플랫폼에서 발급된 토큰의 수명을 설명하는 방법을 알아봅니다. 조직의 기본 정책을 관리하고, 웹 로그인에 관한 정책을 만들고, 웹 API를 호출하는 네이티브 앱에 관한 정책을 만들고, 고급 정책을 관리하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, content-perf, FY21Q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: ac1fa8db8d153365f990c4d3eff120a254ed8096
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129060634"
---
# <a name="configure-token-lifetime-policies-preview"></a>토큰 수명 정책 구성(미리 보기)
Microsoft ID 플랫폼에서 발급된 액세스, SAML 또는 ID 토큰의 수명을 지정할 수 있습니다. 조직의 모든 앱, 다중 테넌트(다중 조직) 애플리케이션 또는 조직의 특정 서비스 주체에 대해 토큰 수명을 구성할 수 있습니다. 자세한 내용은 [구성 가능한 토큰 수명](active-directory-configurable-token-lifetimes.md)을 참조하세요.

이 섹션에서는 토큰 수명의 새 규칙을 적용하는 데 도움이 되는 일반적인 정책 시나리오를 살펴봅니다. 이 예제에서는 사용자가 웹앱에서 더욱 자주 인증하도록 요구하는 정책을 만드는 방법을 알아봅니다.

## <a name="get-started"></a>시작

시작하려면 최신 [Azure AD PowerShell 모듈 퍼블릭 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드합니다.

다음으로, `Connect` 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

```powershell
Connect-AzureAD -Confirm
```

## <a name="create-a-policy-for-web-sign-in"></a>웹 로그인에 대한 정책 만들기

이 예에서는 사용자가 웹앱에 보다 자주 인증하도록 요구하는 정책을 만듭니다. 이 정책은 웹앱의 서비스 주체에 대한 액세스/ID 토큰의 수명을 설정합니다.

1. 토큰 수명 정책을 만듭니다.

    이 정책은 웹 로그인을 위한 액세스/ID 토큰 수명을 2시간으로 설정합니다.

    정책을 만들려면 [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행합니다.

    ```powershell
    $policy = New-AzureADPolicy -Definition @('{"TokenLifetimePolicy":{"Version":1,"AccessTokenLifetime":"02:00:00"}}') -DisplayName "WebPolicyScenario" -IsOrganizationDefault $false -Type "TokenLifetimePolicy"
    ```

    새 정책을 확인하고 정책 **ObjectId** 를 가져오려면 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행합니다.

    ```powershell
    Get-AzureADPolicy -Id $policy.Id
    ```

1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 **ObjectId** 도 가져와야 합니다.

    [Get-AzureADServicePrincipal](/powershell/module/azuread/get-azureadserviceprincipal) cmdlet을 확인하여 조직의 모든 서비스 주체 또는 단일 서비스 주체를 확인합니다.

    ```powershell
    # Get ID of the service principal
    $sp = Get-AzureADServicePrincipal -Filter "DisplayName eq '<service principal display name>'"
    ```

    서비스 주체가 있는 경우 [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행합니다.

    ```powershell
    # Assign policy to a service principal
    Add-AzureADServicePrincipalPolicy -Id $sp.ObjectId -RefObjectId $policy.Id
    ```

## <a name="view-existing-policies-in-a-tenant"></a>테넌트에서 기존 정책 보기

조직에서 만든 모든 정책을 확인하려면 [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행합니다.  위에 나열된 기본값과 다른 정의된 속성 값이 있는 결과는 사용 중지 범위에 포함됩니다.

```powershell
Get-AzureADPolicy -All $true
```

식별한 특정 정책에 연결된 앱 및 서비스 주체를 확인하려면 **1a37dad8-5da7-4cc8-87c7-efbc0326cf20** 을 정책 ID로 바꿔서 다음 [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) cmdlet을 실행합니다. 그런 다음, 조건부 액세스 로그인 빈도를 구성할지 아니면 Azure AD 기본값을 유지할지 결정할 수 있습니다.

```powershell
Get-AzureADPolicyAppliedObject -id 1a37dad8-5da7-4cc8-87c7-efbc0326cf20
```

테넌트에 새로 고침 및 세션 토큰 구성 속성의 사용자 지정 값을 정의하는 정책이 있는 경우 위에 설명된 기본값을 반영하는 값으로 해당 정책을 업데이트하는 것이 좋습니다. 변경 내용이 없는 경우 Azure AD는 자동으로 기본값을 적용합니다.

### <a name="troubleshooting"></a>문제 해결
일부 사용자가 `Get-AzureADPolicy` cmdlet을 실행한 후 `Get-AzureADPolicy : The term 'Get-AzureADPolicy' is not recognized` 오류를 보고했습니다. 해결 방법으로 다음을 실행하여 AzureAD 모듈을 제거/다시 설치한 다음, AzureADPreview 모듈을 설치합니다.

```powershell
# Uninstall the AzureAD Module
UnInstall-Module AzureAD

# Install the AzureAD Preview Module adding the -AllowClobber
Install-Module AzureADPreview -AllowClobber
Note: You cannot install both the preview and the GA version on the same computer at the same time. 

Connect-AzureAD
Get-AzureADPolicy -All $true
```

## <a name="next-steps"></a>다음 단계
Azure AD 조건부 액세스의 [인증 세션 관리 기능](../conditional-access/howto-conditional-access-session-lifetime.md)에 관해 알아봅니다.
