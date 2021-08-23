---
title: Azure AD의 사용자 환경에서 엔터프라이즈 애플리케이션 숨기기
description: Azure Active Directory 액세스 패널 또는 Microsoft 365 시작 관리자의 사용자 환경에서 엔터프라이즈 애플리케이션을 숨기는 방법입니다.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 03/25/2020
ms.author: davidmu
ms.reviewer: lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5fb04026513953ff1e75bb481a7c3a5ae0bb7c9e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567286"
---
# <a name="hide-enterprise-applications-from-end-users-in-azure-active-directory"></a>Azure Active Directory의 최종 사용자로부터 엔터프라이즈 애플리케이션 숨기기

최종 사용자의 MyApps 패널 또는 Microsoft 365 시작 관리자에서 애플리케이션을 숨기는 방법에 대한 지침입니다. 애플리케이션을 숨겨도 애플리케이션에 대한 사용자의 권한은 계속 유지됩니다.

## <a name="prerequisites"></a>필수 구성 요소

MyApps 패널 및 Microsoft 365 시작 관리자에서 애플리케이션을 숨기려면 애플리케이션 관리자 권한이 필요합니다.

모든 Microsoft 365 애플리케이션을 숨기려면 글로벌 관리자 권한이 필요합니다.

## <a name="hide-an-application-from-the-end-user"></a>최종 사용자로부터 애플리케이션 숨기기

MyApps 패널 및 Microsoft 365 애플리케이션 시작 관리자에서 애플리케이션을 숨기려면 다음 단계를 사용합니다.

1. [Azure Portal](https://portal.azure.com)에 디렉터리에 대한 글로벌 관리자 권한으로 로그인합니다.
2. **Azure Active Directory** 를 선택합니다.
3. **Enterprise 애플리케이션** 을 선택합니다. **엔터프라이즈 애플리케이션 - 모든 애플리케이션** 블레이드를 엽니다.
4. 아직 선택하지 않은 경우 **애플리케이션 종류** 에서 **엔터프라이즈 애플리케이션** 을 선택합니다.
5. 숨기려는 애플리케이션을 검색하고 해당 애플리케이션을 클릭합니다.  애플리케이션의 개요가 열립니다.
6. **속성** 을 클릭합니다.
7. **사용자가 볼 수 있습니까?** 란 질문에 **아니요** 를 클릭합니다.
8. **저장** 을 클릭합니다.

> [!NOTE]
> 이러한 지침은 엔터프라이즈 애플리케이션에만 적용됩니다.

## <a name="use-azure-ad-powershell-to-hide-an-application"></a>Azure AD PowerShell을 사용하여 애플리케이션 숨기기

MyApps 패널에서 애플리케이션을 숨기려면 애플리케이션의 서비스 주체에 HideApp 태그를 수동으로 추가할 수 있습니다. 다음 [AzureAD PowerShell](/powershell/module/azuread/#service_principals) 명령을 실행하여 애플리케이션의 **사용자가 볼 수 있습니까?** 속성을 **아니요** 로 설정합니다.

```PowerShell
Connect-AzureAD

$objectId = "<objectId>"
$servicePrincipal = Get-AzureADServicePrincipal -ObjectId $objectId
$tags = $servicePrincipal.tags
$tags += "HideApp"
Set-AzureADServicePrincipal -ObjectId $objectId -Tags $tags
```

## <a name="hide-microsoft-365-applications-from-the-myapps-panel"></a>MyApps 패널에서 Microsoft 365 애플리케이션 숨기기

MyApps 패널에서 모든 Microsoft 365 애플리케이션을 숨기려면 다음 단계를 사용합니다. 애플리케이션은 Office 365 포털에 계속 표시됩니다.

1. [Azure Portal](https://portal.azure.com)에 디렉터리에 대한 글로벌 관리자 권한으로 로그인합니다.
2. **Azure Active Directory** 를 선택합니다.
3. **사용자** 를 선택합니다.
4. **사용자 설정** 을 선택합니다.
5. **엔터프라이즈 애플리케이션** 에서 **최종 사용자가 해당 애플리케이션을 시작하고 보는 방법 관리** 를 클릭합니다.
6. **사용자가 Office 365 포털에서 Office 365 앱만 볼 수 있음** 에 대해 **예** 를 클릭합니다.
7. **저장** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계

* [내 그룹 모두 보기](../fundamentals/active-directory-groups-view-azure-portal.md)
* [엔터프라이즈 앱에 사용자 또는 그룹 할당](assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱에서 사용자 또는 그룹 할당 제거](./assign-user-or-group-access-portal.md)
* [엔터프라이즈 앱의 이름 또는 로고 변경](./add-application-portal-configure.md)
