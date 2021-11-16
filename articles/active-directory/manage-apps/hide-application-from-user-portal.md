---
title: 엔터프라이즈 애플리케이션 숨기기
titleSuffix: Azure AD
description: Azure Active Directory 액세스 포털 또는 Microsoft 365 시작 관리자의 사용자 환경에서 엔터프라이즈 애플리케이션을 숨기는 방법입니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 09/23/2021
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c76498e27d7cba32e7129b1fcca57e20ab11cfb
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/26/2021
ms.locfileid: "129061488"
---
# <a name="hide-an-enterprise-application-in-azure-active-directory"></a>Azure Active Directory에서 엔터프라이즈 애플리케이션 숨기기

Azure Active Directory에서 엔터프라이즈 애플리케이션을 숨기는 방법을 알아봅니다. 애플리케이션을 숨겨도 애플리케이션에 대한 사용자의 권한은 계속 유지됩니다.

## <a name="prerequisites"></a>사전 요구 사항

- 내 앱 포털 및 Microsoft 365 시작 관리자에서 애플리케이션을 숨기려면 애플리케이션 관리자 권한이 필요합니다.

- 모든 Microsoft 365 애플리케이션을 숨기려면 글로벌 관리자 권한이 필요합니다.

## <a name="hide-an-application-from-the-end-user"></a>최종 사용자로부터 애플리케이션 숨기기

내 앱 포털 및 Microsoft 365 애플리케이션 시작 관리자에서 애플리케이션을 숨기려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 디렉터리에 대한 글로벌 관리자 권한으로 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
1. **Enterprise 애플리케이션** 을 선택합니다.
1. 아직 선택하지 않은 경우 **애플리케이션 종류** 에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
1. 숨기려는 애플리케이션을 검색하고 해당 애플리케이션을 선택합니다.
1. **사용자가 볼 수 있습니까?** 질문에 **아니요** 를 선택합니다.
1. **저장** 을 선택합니다.

> [!NOTE]
> 이러한 지침은 엔터프라이즈 애플리케이션에만 적용됩니다.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell을 사용하여 애플리케이션 숨기기

내 앱 패널에서 애플리케이션을 숨기려면 애플리케이션의 서비스 주체에 HideApp 태그를 수동으로 추가할 수 있습니다. 다음 [AzureAD PowerShell](/powershell/module/azuread/#service_principals) 명령을 실행하여 애플리케이션의 **사용자가 볼 수 있습니까?** 속성을 **아니요** 로 설정합니다.

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-my-apps-portal"></a>내 앱 포털에서 Microsoft 365 애플리케이션 숨기기

내 앱 포털에서 모든 Microsoft 365 애플리케이션을 숨기려면 다음 단계를 사용합니다. 애플리케이션은 Office 365 포털에 계속 표시됩니다.

1. [Azure Portal](https://portal.azure.com)에 디렉터리에 대한 글로벌 관리자 권한으로 로그인합니다.
1. **Azure Active Directory** 를 선택합니다.
1. **사용자** 를 선택합니다.
1. **사용자 설정** 을 선택합니다.
1. **엔터프라이즈 애플리케이션** 에서 **최종 사용자가 해당 애플리케이션을 시작하고 보는 방법 관리** 를 선택합니다.
1. **사용자가 Office 365 포털에서 Office 365 앱만 볼 수 있음** 에 대해 **예** 를 선택합니다.
1. **저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

- [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](./assign-user-or-group-access-portal.md)
