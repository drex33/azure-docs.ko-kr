---
title: Azure Active Directory 애플리케이션 프록시 커넥터 자동 설치
description: Azure Active Directory 애플리케이션 프록시 커넥터를 무인으로 설치하여 온-프레미스 앱에 대한 보안된 원격 액세스를 제공하는 방법에 대해 설명합니다.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-proxy
ms.workload: identity
ms.topic: how-to
ms.date: 04/27/2021
ms.author: kenwith
ms.reviewer: ashishj
ms.openlocfilehash: 657588c9f74bd2a7b4da8acb73c4dc40aa987baf
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129988927"
---
# <a name="create-an-unattended-installation-script-for-the-azure-active-directory-application-proxy-connector"></a>Azure Active Directory 애플리케이션 프록시 커넥터에 대한 무인 설치 스크립트 만들기

이 토픽은 Azure AD 애플리케이션 프록시 커넥터에 대한 무인 설치 및 등록을 수행할 수 있도록 Windows PowerShell 스크립트를 만드는 데 도움이 됩니다.

이 기능은 다음을 수행하는 데 유용 합니다.

* 사용자 인터페이스를 사용하도록 설정하지 않은 Windows 서버에 커넥터를 설치하거나 원격 데스크톱을 사용하여 액세스할 수 없습니다.
* 한 번에 여러 커넥터를 설치하고 등록합니다.
* 커넥터 설치 및 등록을 다른 절차의 일부분으로 통합합니다.
* 커넥터 비트를 포함하지만 등록되지 않은 표준 서버 이미지를 만듭니다.

[애플리케이션 프록시 커넥터](application-proxy-connectors.md)가 작동하려면 애플리케이션 관리자 및 암호를 사용하여 Azure AD 디렉터리에 등록되어야 합니다. 일반적으로 이러한 정보는 커넥터 설치 중에 팝업 대화 상자에서 입력되지만, 대신 PowerShell을 사용하여 이 프로세스를 자동화할 수도 있습니다.

무인 설치를 위한 두 단계가 있습니다. 먼저 커넥터를 설치합니다. 두 번째, Azure AD에 커넥터를 등록합니다.

> [!IMPORTANT]
> Azure Government 클라우드용 커넥터를 설치하는 경우 [필수 구성 요소](../hybrid/reference-connect-government-cloud.md#allow-access-to-urls) 및 [설치 단계](../hybrid/reference-connect-government-cloud.md#install-the-agent-for-the-azure-government-cloud)를 검토합니다. 이렇게 하려면 다른 URL 세트에 액세스하도록 설정하고 설치를 실행하기 위한 추가 매개 변수가 필요합니다.

## <a name="install-the-connector"></a>커넥터 설치
등록 없이 커넥터를 설치하려면 다음 단계를 사용합니다.

1. 명령 프롬프트를 엽니다.
2. 다음 명령을 실행합니다. /q는 자동 설치를 의미합니다. 자동 설치는 최종 사용자 사용권 계약에 동의할지 묻는 메시지가 표시되지 않습니다.

   ```
   AADApplicationProxyConnectorInstaller.exe REGISTERCONNECTOR="false" /q
   ```

## <a name="register-the-connector-with-azure-ad"></a>Azure AD에 커넥터 등록
커넥터를 등록하는 데 사용할 수 있는 두 가지 방법이 있습니다.

* Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
* 오프라인에서 만든 토큰을 사용하여 커넥터 등록

### <a name="register-the-connector-using-a-windows-powershell-credential-object"></a>Windows PowerShell 자격 증명 개체를 사용하여 커넥터 등록
1. 디렉터리에 대한 관리 사용자 이름 및 암호를 포함하는 Windows PowerShell 자격 증명 개체 `$cred`를 만듭니다. *\<username\>* 과 *\<password\>* 를 바꿔 다음 명령을 실행합니다.

   ```powershell
   $User = "<username>"
   $PlainPassword = '<password>'
   $SecurePassword = $PlainPassword | ConvertTo-SecureString -AsPlainText -Force
   $cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $User, $SecurePassword
   ```
2. **C:\Program Files\Microsoft AAD App Proxy Connector** 로 이동하여 사용자가 만든 `$cred` 개체를 사용하여 다음 스크립트를 실행합니다.

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Credentials -Usercredentials $cred -Feature ApplicationProxy
   ```

### <a name="register-the-connector-using-a-token-created-offline"></a>오프라인에서 만든 토큰을 사용하여 커넥터 등록
1. 이 코드 조각의 값 또는 아래의 PowerShell cmdlet을 이용하여 AuthenticationContext 클래스를 사용하는 오프라인 토큰을 만듭니다.

   **C# 사용:**

   ```csharp
   using System;
   using System.Linq;
   using System.Collections.Generic;
   using Microsoft.Identity.Client;

   class Program
   {
   #region constants
   /// <summary>
   /// The AAD authentication endpoint uri
   /// </summary>
   static readonly string AadAuthenticationEndpoint = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize";

   /// <summary>
   /// The application ID of the connector in AAD
   /// </summary>
   static readonly string ConnectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";
 
   /// <summary>
   /// The AppIdUri of the registration service in AAD
   /// </summary>
   static readonly string RegistrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation";

   #endregion

   #region private members
   private string token;
   private string tenantID;
   #endregion

   public void GetAuthenticationToken()
   {
    
   IPublicClientApplication clientApp = PublicClientApplicationBuilder
      .Create(ConnectorAppId)
      .WithDefaultRedirectUri() // will automatically use the default Uri for native app
      .WithAuthority(AadAuthenticationEndpoint)
      .Build();

      AuthenticationResult authResult = null;
            
      IAccount account = null;

      IEnumerable<string> scopes = new string[] { RegistrationServiceAppIdUri };

      try
      {
       authResult = await clientApp.AcquireTokenSilent(scopes, account).ExecuteAsync();
      }
       catch (MsalUiRequiredException ex)
      {
       authResult = await clientApp.AcquireTokenInteractive(scopes).ExecuteAsync();
      }


      if (authResult == null || string.IsNullOrEmpty(authResult.AccessToken) || string.IsNullOrEmpty(authResult.TenantId))
      {
       Trace.TraceError("Authentication result, token or tenant id returned are null");
       throw new InvalidOperationException("Authentication result, token or tenant id returned are null");
      }

      token = authResult.AccessToken;
      tenantID = authResult.TenantId;
      }
      ```

   **PowerShell 사용:**

   ```powershell
   # Load MSAL (Tested with version 4.7.1) 

   Add-Type -Path "..\MSAL\Microsoft.Identity.Client.dll" 
        
   # The AAD authentication endpoint uri
        
   $authority = "https://login.microsoftonline.com/common/oauth2/v2.0/authorize"

   #The application ID of the connector in AAD

   $connectorAppId = "55747057-9b5d-4bd4-b387-abf52a8bd489";

   #The AppIdUri of the registration service in AAD
   $registrationServiceAppIdUri = "https://proxy.cloudwebappproxy.net/registerapp/user_impersonation"

   # Define the resources and scopes you want to call 

   $scopes = New-Object System.Collections.ObjectModel.Collection["string"] 

   $scopes.Add($registrationServiceAppIdUri)

   $app = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($connectorAppId).WithAuthority($authority).WithDefaultRedirectUri().Build()

   [Microsoft.Identity.Client.IAccount] $account = $null

   # Acquiring the token 

   $authResult = $null

   $authResult = $app.AcquireTokenInteractive($scopes).WithAccount($account).ExecuteAsync().ConfigureAwait($false).GetAwaiter().GetResult()

   # Check AuthN result
   If (($authResult) -and ($authResult.AccessToken) -and ($authResult.TenantId)) {
        
   $token = $authResult.AccessToken
   $tenantId = $authResult.TenantId

   Write-Output "Success: Authentication result returned."
        
   }
   Else {
         
   Write-Output "Error: Authentication result, token or tenant id returned with null."
        
   } 
   ```

2. 토큰을 만들었으면 토큰을 사용하여 SecureString을 만듭니다.

   ```powershell
   $SecureToken = $Token | ConvertTo-SecureString -AsPlainText -Force
   ```

3. 다음 Windows PowerShell 명령을 실행하여 \<tenant GUID\>를 디렉터리 ID로 바꿉니다.

   ```powershell
   .\RegisterConnector.ps1 -modulePath "C:\Program Files\Microsoft AAD App Proxy Connector\Modules\" -moduleName "AppProxyPSModule" -Authenticationmode Token -Token $SecureToken -TenantId <tenant GUID> -Feature ApplicationProxy
   ```

## <a name="next-steps"></a>다음 단계
* [고유한 도메인 이름을 사용하여 애플리케이션 게시](application-proxy-configure-custom-domain.md)
* [Single Sign-On 사용](application-proxy-configure-single-sign-on-with-kcd.md)
* [애플리케이션 프록시에서 발생한 문제 해결](application-proxy-troubleshoot.md)