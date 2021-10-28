---
title: 게시된 앱에 대한 사용자 지정 홈페이지 - Azure Active Directory 애플리케이션 프록시
description: Azure Active Directory 애플리케이션 프록시 커넥터에 대한 기본 사항 제공
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: harshja
ms.openlocfilehash: 8a74bbffc487117aa38f541093961fa21450eaa4
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129990067"
---
# <a name="set-a-custom-home-page-for-published-apps-by-using-azure-active-directory-application-proxy"></a>Azure Active Directory 애플리케이션 프록시를 사용하여 게시된 앱에 대한 사용자 지정 홈페이지 설정

이 문서에서는 사용자를 사용자 지정 홈페이지에 연결하도록 앱을 구성하는 방법을 설명합니다. 애플리케이션 프록시를 사용하여 앱을 게시할 때 내부 URL을 설정하지만 사용자가 처음 보게 되는 페이지는 설정하지 않는 경우가 있습니다. 사용자가 앱에 액세스할 때 올바른 페이지가 표시되도록 사용자 지정 홈페이지를 설정합니다. Azure Active Directory 내 앱 또는 Office 365 앱 시작 관리자에서 앱에 액세스하는지 여부와 무관하게 설정한 사용자 지정 홈페이지가 사용자에게 표시됩니다.

사용자가 앱을 시작하면 기본적으로 게시된 앱의 루트 도메인 URL로 이동됩니다. 방문 페이지는 일반적으로 홈페이지 URL로 설정됩니다. 앱 사용자가 앱 내의 특정 페이지를 방문하게 하려는 경우 Azure AD PowerShell 모듈을 사용하여 사용자 지정 홈페이지 URL을 정의합니다.

회사에서 사용자 지정 홈페이지를 설정하는 이유를 설명하는 한 가지 시나리오입니다.

- 사용자가 회사 네트워크 내에서 `https://ExpenseApp/login/login.aspx`로 이동하여 로그인하고 앱에 액세스합니다.
- 애플리케이션 프록시가 폴더 구조의 최상위 수준에서 액세스해야 하는 이미지와 같은 다른 자산이 있으므로 `https://ExpenseApp`을 내부 URL로 사용하여 앱을 게시합니다.
- 기본 외부 URL은 `https://ExpenseApp-contoso.msappproxy.net`이며, 외부 사용자를 로그인 페이지로 이동하지 않습니다.
- `https://ExpenseApp-contoso.msappproxy.net/login/login.aspx`를 홈페이지 URL로 설정하여 외부 사용자에게 로그인 페이지를 먼저 표시하려고 합니다.

> [!NOTE]
> 사용자에게 게시된 응용 프로그램에 대한 액세스 권한을 제공하면 [내 앱](https://support.microsoft.com/account-billing/sign-in-and-start-apps-from-the-my-apps-portal-2f3b1bae-0e5a-4a86-a33e-876fbd2a4510) 및 [Office 365 앱 시작 관리자](https://www.microsoft.com/microsoft-365/blog/2016/09/27/introducing-the-new-office-365-app-launcher/)에 앱이 표시됩니다.

## <a name="before-you-start"></a>시작하기 전에

홈페이지 URL을 설정하기 전에 다음 요구 사항에 유의하세요.

- 사용자가 지정한 경로가 루트 도메인 URL의 하위 도메인 경로여야 합니다.

  예를 들어 루트 도메인 URL이 `https://apps.contoso.com/app1/`이면 구성하는 홈페이지 URL은 `https://apps.contoso.com/app1/`로 시작해야 합니다.

- 게시된 앱을 변경하는 경우 이로 인해 홈페이지 URL 값을 다시 설정할 수 있습니다. 나중에 앱을 업데이트할 경우 홈페이지 URL을 다시 확인하고 필요한 경우 업데이트해야 합니다.

Azure Portal을 통해 또는 PowerShell을 사용하여 홈페이지 URL을 설정할 수 있습니다.

## <a name="change-the-home-page-in-the-azure-portal"></a>Azure Portal에서 홈페이지 변경

Azure AD Portal을 통해 앱의 홈페이지 URL을 변경하려면 다음 단계를 수행합니다.

1. 관리자로 [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **Azure Active Directory** 를 선택한 다음, **앱 등록** 을 선택합니다. 등록된 앱의 목록이 표시됩니다.
1. 목록에서 앱을 선택합니다. 등록된 앱의 세부 정보를 보여 주는 페이지가 표시됩니다.
1. **관리** 에서 **브랜딩** 을 선택합니다.
1. **홈페이지 URL** 을 새 경로로 업데이트합니다.

   ![홈페이지 URL 필드를 표시하는 등록된 앱의 브랜딩 페이지](media/application-proxy-configure-custom-home-page/app-proxy-app-branding.png)

1. **저장** 을 선택합니다.

## <a name="change-the-home-page-with-powershell"></a>PowerShell을 사용하여 홈페이지 변경

PowerShell을 사용하여 앱의 홈페이지를 구성하려면 다음을 수행해야 합니다.

1. Azure AD PowerShell 모듈을 설치합니다.
1. 앱의 ObjectId 값을 찾습니다.
1. PowerShell 명령을 사용하여 앱의 홈페이지 URL을 업데이트합니다.

### <a name="install-the-azure-ad-powershell-module"></a>Azure AD PowerShell 모듈 설치

PowerShell을 사용하여 사용자 지정 홈페이지 URL을 정의하기 전에 Azure AD PowerShell 모듈을 설치합니다. Graph API 엔드포인트를 사용하는 [PowerShell 갤러리](https://www.powershellgallery.com/packages/AzureAD/2.0.2.16)에서 패키지를 다운로드할 수 있습니다.

패키지를 설치하려면 다음 단계를 따르세요.

1. 표준 PowerShell 창을 열고 다음 명령을 실행합니다.

   ```powershell
   Install-Module -Name AzureAD
   ```

    관리자가 아닌 사용자로 명령을 실행하는 경우 `-scope currentuser` 옵션을 사용합니다.

1. 설치 도중 Nuget.org에서 두 개의 패키지를 설치하려면 **Y** 를 선택합니다. 두 패키지 모두 필요합니다.

### <a name="find-the-objectid-of-the-app"></a>앱의 ObjectId 찾기

표시 이름 또는 홈페이지로 앱을 검색하여 앱의 ObjectId를 가져옵니다.

1. 동일한 PowerShell 창에서 Azure AD 모듈을 가져옵니다.

   ```powershell
   Import-Module AzureAD
   ```

1. 테넌트 관리자로 Azure AD 모듈에 로그인합니다.

   ```powershell
   Connect-AzureAD
   ```

1. 앱을 찾습니다. 이 예제에서는 PowerShell을 사용하여 표시 이름이 `SharePoint`인 앱을 검색함으로써 ObjectId를 찾습니다.

   ```powershell
   Get-AzureADApplication | Where-Object { $_.DisplayName -eq "SharePoint" } | Format-List DisplayName, Homepage, ObjectId
   ```

   여기에 표시된 것과 비슷한 결과가 표시되어야 합니다. 다음 섹션에서 사용할 ObjectID GUID를 복사합니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

   또는 모든 앱 목록을 끌어오고, 특정 표시 이름 또는 홈페이지가 있는 앱 목록을 검색하고, 앱이 발견되면 앱의 ObjectId를 복사할 수 있습니다.

   ```powershell
   Get-AzureADApplication | Format-List DisplayName, Homepage, ObjectId
   ```

### <a name="update-the-home-page-url"></a>홈페이지 URL 업데이트

홈페이지 URL을 만들고 해당 값으로 앱을 업데이트합니다. 동일한 PowerShell 창을 사용하여 계속하거나 새 PowerShell 창을 사용할 경우 다시 `Connect-AzureAD`를 사용하여 Azure AD 모듈에 로그인합니다. 그런 다음, 다음 단계를 수행합니다.

1. 이전 섹션에서 복사한 ObjectId 값을 보관할 변수를 만듭니다. (이 SharePoint 예제에서 사용된 ObjectId 값을 앱의 ObjectId 값으로 대체합니다.)

   ```powershell
   $objguid = "8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4"
   ```

1. 다음 명령을 실행하여 올바른 앱이 있는지 확인합니다. 출력은 이전 섹션([앱의 ObjectId 찾기](#find-the-objectid-of-the-app))에서 확인한 출력과 동일해야 합니다.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

1. 변경 내용을 저장하기 위해 비어 있는 애플리케이션 개체를 만듭니다.

   ```powershell
   $appnew = New-Object "Microsoft.Open.AzureAD.Model.Application"
   ```

1. 홈페이지 URL을 원하는 값으로 설정합니다. 값은 게시된 앱의 하위 도메인 경로여야 합니다. 예를 들어 홈페이지 URL을 `https://sharepoint-iddemo.msappproxy.net/`에서 `https://sharepoint-iddemo.msappproxy.net/hybrid/`로 변경하면 앱 사용자가 사용자 지정 홈페이지로 바로 이동합니다.

   ```powershell
   $homepage = "https://sharepoint-iddemo.msappproxy.net/hybrid/"
   ```

1. 홈페이지의 업데이트를 수행합니다.

   ```powershell
   Set-AzureADApplication -ObjectId $objguid -Homepage $homepage
   ```

1. 변경이 성공적으로 완료되었는지 확인하려면 2단계에서 다음 명령을 다시 실행합니다.

   ```powershell
   Get-AzureADApplication -ObjectId $objguid | Format-List DisplayName, Homepage, ObjectId
   ```

   이 예제에서는 이제 출력이 다음과 같이 표시됩니다.

   ```console
   DisplayName : SharePoint
   Homepage    : https://sharepoint-iddemo.msappproxy.net/hybrid/
   ObjectId    : 8af89bfa-eac6-40b0-8a13-c2c4e3ee22a4
   ```

1. 앱을 다시 시작하여 홈페이지가 예상한 대로 첫 번째 화면으로 표시되는지 확인합니다.

> [!NOTE]
> 앱에 대한 변경 내용으로 인해 홈페이지 URL을 다시 설정할 수도 있습니다. 홈 페이지 URL을 다시 설정하는 경우 이 섹션의 단계를 반복하여 재설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure AD 애플리케이션 프록시를 통해 SharePoint에 원격 액세스를 사용하도록 설정](./application-proxy-integrate-with-sharepoint-server.md)
- [자습서: Azure Active Directory에서 애플리케이션 프록시를 통한 원격 액세스를 위해 온-프레미스 애플리케이션 추가](application-proxy-add-on-premises-application.md)