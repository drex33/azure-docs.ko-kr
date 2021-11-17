---
title: 사용자가 로그인하는 방법 사용 안 함
titleSuffix: Azure AD
description: Azure Active Directory에서 사용자가 로그인하지 않도록 엔터프라이즈 애플리케이션을 비활성화하는 방법
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e1c07d650ee3b04099a4e4e41cdd7ee7db200bff
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061811"
---
# <a name="disable-how-a-user-signs-in-for-an-application-in-azure-active-directory"></a>사용자가 Azure Active Directory에서 애플리케이션에 로그인하는 방법 사용 안 함

이 문서에서는 사용자가 Azure Active Directory의 애플리케이션에 로그인하는 방법을 사용하지 않도록 설정합니다.

## <a name="prerequisites"></a>사전 요구 사항

사용자가 로그인하는 방법을 사용하지 않도록 설정하려면 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- 다음 역할 중 하나: 전역 관리자, 클라우드 애플리케이션 관리자, 애플리케이션 관리자 또는 서비스 주체의 소유자.

## <a name="disable-how-a-user-signs-in"></a>사용자가 로그인하는 방법 사용 안 함

1. [Azure Portal](https://portal.azure.com)에 디렉터리에 대한 글로벌 관리자 권한으로 로그인합니다.
1. **Azure Active Directory** 를 검색하고 선택합니다.
1. **Enterprise 애플리케이션** 을 선택합니다.
1. 사용자가 로그인하지 못하도록 설정할 애플리케이션을 검색하고 해당 애플리케이션을 선택합니다.
1. **속성** 을 선택합니다.
1. **사용자가 로그인할 수 있도록 설정하시겠습니까?** 를 **아니요** 로 설정합니다.
1. **저장** 을 선택합니다.

## <a name="use-azure-ad-powershell-to-disable-an-unlisted-app"></a>Azure AD PowerShell을 사용하여 목록에 없는 앱을 사용하지 않도록 설정

AzureAD 모듈을 설치했는지 확인합니다(Install-Module -Name AzureAD 명령 사용). NuGet 모듈 또는 새 Azure Active Directory V2 PowerShell 모듈을 설치하라는 메시지가 표시되면 Y를 입력하고 ENTER를 누릅니다.

엔터프라이즈 앱 목록에 표시되지 않는 앱의 AppId를 알고 있는 경우(예: 앱을 삭제했거나 Microsoft에서 사전 인증되는 앱으로 인해 서비스 주체를 아직 만들지 않은 경우) 앱에 대한 서비스 주체를 수동으로 만든 다음, [AzureAD PowerShell cmdlet](/powershell/module/azuread/New-AzureADServicePrincipal)을 사용하여 사용하지 않도록 설정할 수 있습니다.

```PowerShell
# The AppId of the app to be disabled
$appId = "{AppId}"

# Check if a service principal already exists for the app
$servicePrincipal = Get-AzureADServicePrincipal -Filter "appId eq '$appId'"
if ($servicePrincipal) {
    # Service principal exists already, disable it
    Set-AzureADServicePrincipal -ObjectId $servicePrincipal.ObjectId -AccountEnabled $false
} else {
    # Service principal does not yet exist, create it and disable it at the same time
    $servicePrincipal = New-AzureADServicePrincipal -AppId $appId -AccountEnabled $false
}
```

## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](./assign-user-or-group-access-portal.md)
