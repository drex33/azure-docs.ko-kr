---
title: Azure AD 테넌트 앱 클레임 사용자 지정(PowerShell)
titleSuffix: Microsoft identity platform
description: 특정 Azure Active Directory 테넌트의 애플리케이션에 대한 토큰에서 내보낸 클레임을 사용자 지정하는 방법을 알아봅니다.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 06/16/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 905a73a28e9a41370e80ef63d8212b6c323e46ed
ms.sourcegitcommit: f0168d80eb396ce27032aa02fe9da5a0c10b5af3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/23/2021
ms.locfileid: "112553345"
---
# <a name="customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant"></a>테넌트의 특정 앱용 토큰에 내보내는 클레임 사용자 지정

‘클레임’은 해당 사용자에 대해 발급하는 토큰 내에서 ID 공급자가 사용자에 대해 나타내는 정보입니다. 클레임 사용자 지정은 테넌트 관리자가 테넌트의 특정 애플리케이션에 대한 토큰에 내보내는 클레임을 사용자 지정하는 데 사용됩니다. 클레임 매핑 정책을 사용하면 다음과 같은 작업을 수행할 수 있습니다.

- 토큰에 포함할 클레임을 선택합니다.
- 아직 존재하지 않는 클레임 형식을 만듭니다.
- 특정 클레임에 내보내는 데이터 원본을 선택하거나 변경합니다.

클레임 사용자 지정은 WS-Fed, SAML, OAuth 및 OpenID Connect 프로토콜용 클레임 매핑 정책 구성을 지원합니다.

> [!NOTE]
> 이 기능은 Azure Portal을 통해 제공되는 [클레임 사용자 지정](active-directory-saml-claims-customization.md)을 바꾸고 대체합니다. 이 문서에 자세히 설명된 Microsoft Graph/PowerShell 방법과 함께 포털을 사용하여 동일한 애플리케이션에서 클레임을 사용자 지정하는 경우 해당 애플리케이션용으로 발급된 토큰은 포털의 구성을 무시합니다. 이 문서에 설명된 방법을 통해 만들어진 구성은 포털에서 반영되지 않습니다.

이 문서에서는 [클레임 매핑 정책 유형](reference-claims-mapping-policy-type.md)을 사용하는 방법을 이해하는 데 도움이 되는 몇 가지 일반적인 시나리오를 설명합니다.

## <a name="get-started"></a>시작

다음 예제에서는 서비스 주체에 대한 정책을 만들고, 업데이트, 연결 및 삭제합니다. 클레임 매핑 정책은 서비스 주체 개체에만 할당할 수 있습니다. Azure AD를 처음 접하는 분들은 [Azure AD 테넌트를 가져오는 방법](quickstart-create-new-tenant.md)을 살펴본 후 예제를 진행하는 것이 좋습니다.

클레임 매핑 정책을 만들 때 토큰의 디렉터리 스키마 확장 특성에서 클레임을 내보낼 수도 있습니다. `ClaimsSchema` 요소에서 *ID* 대신 확장의 *ExtensionID* 를 사용합니다.  확장 특성에 대한 자세한 내용은 [디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조하세요.

> [!NOTE]
> 클레임 매핑 정책을 구성하려면 [Azure AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)가 필요합니다. PowerShell 모듈은 미리 보기 상태이지만, Azure에서 클레임 매핑과 토큰 생성 런타임은 일반 공급됩니다. 미리 보기 PowerShell 모듈로 업데이트하려면 구성 스크립트를 업데이트하거나 변경해야 할 수 있습니다. 

시작하려면 다음 단계 중 하나를 수행합니다.

1. 최신 [Azure AD PowerShell 모듈 공개 미리 보기 릴리스](https://www.powershellgallery.com/packages/AzureADPreview)를 다운로드합니다.
1. [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0-preview) 명령을 실행하여 Azure AD 관리자 계정에 로그인합니다. 새 세션을 시작할 때마다 이 명령을 실행합니다.

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 조직에서 만든 모든 정책을 확인하려면 다음 명령을 실행합니다. 다음 시나리오에서 대부분의 작업 후에 이 명령을 실행하여 정책이 예상대로 생성되는지 확인하는 것이 좋습니다.

   ``` powershell
   Get-AzureADPolicy
   ```

다음으로, 클레임 매핑 정책을 만들고 서비스 주체에 할당합니다.  일반적인 시나리오는 다음 예제를 참조하세요.
- [토큰에서 기본 클레임 생략](#omit-the-basic-claims-from-tokens)
- [토큰에 EmployeeID 및 TenantCountry를 클레임으로 포함](#include-the-employeeid-and-tenantcountry-as-claims-in-tokens)
- [토큰에서 클레임 변환 사용](#use-a-claims-transformation-in-tokens)

클레임 매핑 정책을 만든 후에는 해당 토큰에 사용자 지정된 클레임이 포함됨을 애플리케이션이 인식하도록 구성합니다.  자세한 내용은 [보안 고려 사항](#security-considerations)을 참조하세요.

## <a name="omit-the-basic-claims-from-tokens"></a>토큰에서 기본 클레임 생략

이 예제에서는 연결된 서비스 주체에 발급된 토큰에서 [기본 클레임 집합](reference-claims-mapping-policy-type.md#claim-sets)을 제거하는 정책을 만듭니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에서 기본 클레임 집합을 제거합니다.
   1. 정책을 만들려면 이 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="include-the-employeeid-and-tenantcountry-as-claims-in-tokens"></a>토큰에 EmployeeID 및 TenantCountry를 클레임으로 포함

이 예제에서는 연결된 서비스 주체에 발급된 토큰에 EmployeeID 및 TenantCountry를 추가하는 정책을 만듭니다. EmployeeID는 SAML 토큰 및 JWT 둘 다에서 이름 클레임 형식으로 내보내집니다. TenantCountry는 SAML 토큰 및 JWT 둘 다에서 국가/지역 클레임 형식으로 내보내집니다. 이 예제에서는 토큰에 기본 클레임 집합을 계속 포함합니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에 EmployeeID 및 TenantCountry 클레임을 추가합니다.
   1. 정책을 만들려면 다음 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

      > [!WARNING]
      > 디렉터리 확장 특성의 클레임 매핑 정책을 정의하는 경우 `ClaimsSchema` 배열의 본문 내에서 `ID` 속성 대신 `ExtensionID` 속성을 사용합니다.

   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="use-a-claims-transformation-in-tokens"></a>토큰에서 클레임 변환 사용

이 예제에서는 연결된 서비스 주체에 발급된 JWT에 사용자 지정 클레임 “JoinedData”를 내보내는 정책을 만듭니다. 이 클레임에는 사용자 개체의 extensionattribute1 특성에 저장된 데이터와 “.sandbox”를 조인하여 만든 값이 포함됩니다. 이 예제에서는 토큰에 기본 클레임 집합을 제외합니다.

1. 클레임 매핑 정책을 만듭니다. 특정 서비스 주체에 연결되는 이 정책은 토큰에 EmployeeID 및 TenantCountry 클레임을 추가합니다.
   1. 정책을 만들려면 다음 명령을 실행합니다.

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 새 정책을 보고 정책 ObjectId를 가져오려면 다음 명령을 실행합니다.

      ``` powershell
      Get-AzureADPolicy
      ```
1. 서비스 주체에게 정책을 할당합니다. 서비스 주체의 ObjectId도 가져와야 합니다.
   1. 조직의 모든 서비스 주체를 보려면 [Microsoft Graph API를 쿼리](/graph/traverse-the-graph)하면 됩니다. 또는 [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)에서 Azure AD 계정으로 로그인합니다.
   2. 서비스 주체의 ObjectId가 있으면 다음 명령을 실행합니다.

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="security-considerations"></a>보안 고려 사항

토큰을 수신하는 애플리케이션은 클레임 값이 Azure AD에서 정식으로 발행되었고 변조될 수 없다는 사실을 바탕으로 합니다. 그러나 클레임 매핑 정책을 통해 토큰 콘텐츠를 수정하면 이러한 가정이 올바르지 않게 될 수도 있습니다. 악의적인 행위자가 만든 클레임 매핑 정책으로부터 보호받으려면, 애플리케이션은 클레임 매핑 정책의 생성자가 토큰을 수정했음을 명시적으로 인정해야 합니다. 이 작업은 다음 방법 중 하나로 수행할 수 있습니다.

- [사용자 지정 서명 키 구성](#configure-a-custom-signing-key)
- 또는 매핑된 클레임을 허용하도록 [애플리케이션 매니페스트를 업데이트](#update-the-application-manifest)합니다.
 
업데이트하지 않으면 Azure AD에서는 [`AADSTS50146` 오류 코드](reference-aadsts-error-codes.md#aadsts-error-codes)를 반환합니다.

### <a name="configure-a-custom-signing-key"></a>사용자 지정 서명 키 구성

다중 테넌트 앱의 경우 사용자 지정 서명 키를 사용해야 합니다.  앱 매니페스트에서 `acceptMappedClaims`를 설정하지 마세요. Azure Portal에서 앱을 설정하는 경우 테넌트에서 앱 등록 개체와 서비스 주체를 가져옵니다.  해당 앱은 토큰에서 클레임을 사용자 지정하는 데 사용할 수 없는 Azure 전역 로그인 키를 사용합니다.  토큰에서 사용자 지정 클레임을 가져오려면 인증서에서 사용자 지정 로그인 키를 만들고 서비스 주체에 추가합니다.  테스트용으로 자체 서명된 인증서를 만들 수 있습니다. 사용자 지정 서명 키를 구성한 후 애플리케이션 코드는 [토큰 서명 키의 유효성을 검사](#validate-token-signing-key)해야 합니다.

서비스 주체에게 다음 정보를 추가합니다.

- 프라이빗 키([키 자격 증명](/graph/api/resources/keycredential)으로)
- 암호([암호 자격 증명](/graph/api/resources/passwordcredential)으로)
- 퍼블릭 키([키 자격 증명](/graph/api/resources/keycredential)으로)

인증서의 PFX 파일 내보내기에서 base-64로 인코딩된 프라이빗 및 퍼블릭 키를 추출합니다. “Sign”에 사용되는 `keyCredential`의 `keyId`가 `passwordCredential`의 `keyId`와 일치하는지 확인합니다. 인증서 지문의 해시를 가져와서 `customkeyIdentifier`를 생성할 수 있습니다.

#### <a name="request"></a>요청

다음은 서비스 주체에 사용자 지정 서명 키를 추가하는 HTTP PATCH 요청의 형식을 보여 줍니다.  `keyCredentials` 속성의 “key” 값은 가독성을 위해 짧게 표시되었습니다. 값은 base-64로 인코딩됩니다. 프라이빗 키의 경우 속성 사용은 “Sign”입니다. 퍼블릭 키의 경우 속성 사용은 “Verify”입니다.

```
PATCH https://graph.microsoft.com/v1.0/servicePrincipals/f47a6776-bca7-4f2e-bc6c-eec59d058e3e

Content-type: servicePrincipals/json
Authorization: Bearer {token}

{
    "keyCredentials":[
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "X509CertAndPassword",
            "usage": "Sign",
            "key":"MIIKIAIBAz.....HBgUrDgMCERE20nuTptI9MEFCh2Ih2jaaLZBZGeZBRFVNXeZmAAgIH0A==",
            "displayName": "CN=contoso"
        },
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "endDateTime": "2021-04-22T22:10:13Z",
            "keyId": "e35a7d11-fef0-49ad-9f3e-aacbe0a42c42",
            "startDateTime": "2020-04-22T21:50:13Z",
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "key": "MIIDJzCCAg+gAw......CTxQvJ/zN3bafeesMSueR83hlCSyg==",
            "displayName": "CN=contoso"
        }

    ],
    "passwordCredentials": [
        {
            "customKeyIdentifier": "lY85bR8r6yWTW6jnciNEONwlVhDyiQjdVLgPDnkI5mA=",
            "keyId": "4c266507-3e74-4b91-aeba-18a25b450f6e",
            "endDateTime": "2022-01-27T19:40:33Z",
            "startDateTime": "2020-04-20T19:40:33Z",
            "secretText": "mypassword"
        }
    ]
}
```

#### <a name="configure-a-custom-signing-key-using-powershell"></a>PowerShell을 사용하여 사용자 지정 서명 키 구성

PowerShell을 사용하여 [MSAL 퍼블릭 클라이언트 애플리케이션을 인스턴스화](msal-net-initializing-client-applications.md#initializing-a-public-client-application-from-code)하고 [인증 코드 부여](v2-oauth2-auth-code-flow.md) 흐름을 사용하여 Microsoft Graph 대한 위임된 권한 액세스 토큰을 가져옵니다. 액세스 토큰을 사용하여 Microsoft Graph를 호출하고 서비스 주체에 대한 사용자 지정 서명 키를 구성합니다. 사용자 지정 서명 키를 구성한 후 애플리케이션 코드는 [토큰 서명 키의 유효성을 검사](#validate-token-signing-key)해야 합니다.

이 스크립트를 실행하려면 다음이 필요합니다.
1. Azure Portal에서 [엔터프라이즈 애플리케이션](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/)의 애플리케이션 항목 **개요** 블레이드에 있는 애플리케이션 서비스 주체의 개체 ID.
2. 사용자를 로그인시키고 Microsoft Graph를 호출하는 액세스 토큰을 가져오기 위한 앱 등록. Azure Portal, [앱 등록](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)의 애플리케이션 항목 **개요** 블레이드에서 이 앱의 애플리케이션(클라이언트) ID를 가져옵니다. 앱 등록에는 다음 구성이 있어야 합니다.
    - **모바일 및 데스크톱 애플리케이션** 플랫폼 구성에 나열된 “http://localhost”의 리디렉션 URI
    - **API 권한** 에서 Microsoft Graph 위임된 권한 **Application.ReadWrite.All** 및 **User.Read**(관리자에게 이 권한에 대한 동의를 부여해야 함)
3. Microsoft Graph 액세스 토큰을 가져오기 위해 로그인하는 사용자. 사용자는 다음 Azure AD 관리 역할 중 하나여야 합니다(서비스 주체를 업데이트하는 데 필요).
    - 클라우드 애플리케이션 관리자
    - 애플리케이션 관리자
    - 전역 관리자
4. 애플리케이션의 사용자 지정 서명 키로 구성할 인증서. 자체 서명된 인증서를 만들거나 신뢰할 수 있는 인증 기관에서 인증서를 가져올 수 있습니다. 스크립트에서 사용되는 인증서 구성 요소는 다음과 같습니다.
    - 퍼블릭 키(일반적으로 .cer 파일)
    - PKCS#12 형식의 프라이빗 키(.pfx 파일)
    - 프라이빗 키의 암호(pfx 파일)

> [!IMPORTANT]
> Azure AD는 다른 형식 종류를 지원하지 않으므로 프라이빗 키는 PKCS#12 형식이어야 합니다. Microsoft Graph를 사용하여 인증서 정보가 포함된 `keyCredentials`로 서비스 주체를 패치하는 경우 잘못된 형식을 사용하면 “잘못된 인증서: 키 값이 잘못된 인증서입니다.”라는 오류가 발생할 수 있습니다.

```powershell

$fqdn="fourthcoffeetest.onmicrosoft.com" # this is used for the 'issued to' and 'issued by' field of the certificate
$pwd="mypassword" # password for exporting the certificate private key
$location="C:\\temp" # path to folder where both the pfx and cer file will be written to

# Create a self-signed cert
$cert = New-SelfSignedCertificate -certstorelocation cert:\currentuser\my -DnsName $fqdn
$pwdSecure = ConvertTo-SecureString -String $pwd -Force -AsPlainText
$path = 'cert:\currentuser\my\' + $cert.Thumbprint
$cerFile = $location + "\\" + $fqdn + ".cer"
$pfxFile = $location + "\\" + $fqdn + ".pfx"
 
# Export the public and private keys
Export-PfxCertificate -cert $path -FilePath $pfxFile -Password $pwdSecure
Export-Certificate -cert $path -FilePath $cerFile

$ClientID = "<app-id>"
$loginURL       = "https://login.microsoftonline.com"
$tenantdomain   = "fourthcoffeetest.onmicrosoft.com"
$redirectURL = "http://localhost" # this reply URL is needed for PowerShell Core 
[string[]] $Scopes = "https://graph.microsoft.com/.default"
$pfxpath = $pfxFile # path to pfx file
$cerpath = $cerFile # path to cer file
$SPOID = "<service-principal-id>"
$graphuri = "https://graph.microsoft.com/v1.0/serviceprincipals/$SPOID"
$password = $pwd  # password for the pfx file
 
 
# choose the correct folder name for MSAL based on PowerShell version 5.1 (.Net) or PowerShell Core (.Net Core)
 
if ($PSVersionTable.PSVersion.Major -gt 5)
    { 
        $core = $true
        $foldername =  "netcoreapp2.1"
    }
else
    { 
        $core = $false
        $foldername = "net45"
    }
 
# Load the MSAL/microsoft.identity/client assembly -- needed once per PowerShell session
[System.Reflection.Assembly]::LoadFrom((Get-ChildItem C:/Users/<username>/.nuget/packages/microsoft.identity.client/4.32.1/lib/$foldername/Microsoft.Identity.Client.dll).fullname) | out-null
  
$global:app = $null
  
$ClientApplicationBuilder = [Microsoft.Identity.Client.PublicClientApplicationBuilder]::Create($ClientID)
[void]$ClientApplicationBuilder.WithAuthority($("$loginURL/$tenantdomain"))
[void]$ClientApplicationBuilder.WithRedirectUri($redirectURL)
 
$global:app = $ClientApplicationBuilder.Build()
  
Function Get-GraphAccessTokenFromMSAL {
    [Microsoft.Identity.Client.AuthenticationResult] $authResult  = $null
    $AquireTokenParameters = $global:app.AcquireTokenInteractive($Scopes)
    [IntPtr] $ParentWindow = [System.Diagnostics.Process]::GetCurrentProcess().MainWindowHandle
    if ($ParentWindow)
    {
        [void]$AquireTokenParameters.WithParentActivityOrWindow($ParentWindow)
    }
    try {
        $authResult = $AquireTokenParameters.ExecuteAsync().GetAwaiter().GetResult()
    }
    catch {
        $ErrorMessage = $_.Exception.Message
        Write-Host $ErrorMessage
    }
     
    return $authResult
}
  
$myvar = Get-GraphAccessTokenFromMSAL
if ($myvar)
{
    $GraphAccessToken = $myvar.AccessToken
    Write-Host "Access Token: " $myvar.AccessToken
    #$GraphAccessToken = "eyJ0eXAiOiJKV1QiL ... iPxstltKQ"
    
 
    #  this is for PowerShell Core
    $Secure_String_Pwd = ConvertTo-SecureString $password -AsPlainText -Force
 
    # reading certificate files and creating Certificate Object
    if ($core)
    {
        $pfx_cert = get-content $pfxpath -AsByteStream -Raw
        $cer_cert = get-content $cerpath -AsByteStream -Raw
        $cert = Get-PfxCertificate -FilePath $pfxpath -Password $Secure_String_Pwd
    }
    else
    {
        $pfx_cert = get-content $pfxpath -Encoding Byte
        $cer_cert = get-content $cerpath -Encoding Byte
        # Write-Host "Enter password for the pfx file..."
        # calling Get-PfxCertificate in PowerShell 5.1 prompts for password
        # $cert = Get-PfxCertificate -FilePath $pfxpath
        $cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]::new($pfxpath, $password)
    }
 
    # base 64 encode the private key and public key
    $base64pfx = [System.Convert]::ToBase64String($pfx_cert)
    $base64cer = [System.Convert]::ToBase64String($cer_cert)
 
    # getting id for the keyCredential object
    $guid1 = New-Guid
    $guid2 = New-Guid
 
    # get the custom key identifier from the certificate thumbprint:
    $hasher = [System.Security.Cryptography.HashAlgorithm]::Create('sha256')
    $hash = $hasher.ComputeHash([System.Text.Encoding]::UTF8.GetBytes($cert.Thumbprint))
    $customKeyIdentifier = [System.Convert]::ToBase64String($hash)
 
    # get end date and start date for our keycredentials
    $endDateTime = ($cert.NotAfter).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
    $startDateTime = ($cert.NotBefore).ToUniversalTime().ToString( "yyyy-MM-ddTHH:mm:ssZ" )
 
    # building our json payload
    $object = [ordered]@{    
    keyCredentials = @(       
         [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid1
            startDateTime = $startDateTime 
            type = "X509CertAndPassword"
            usage = "Sign"
            key = $base64pfx
            displayName = "CN=fourthcoffeetest" 
        },
        [ordered]@{            
            customKeyIdentifier = $customKeyIdentifier
            endDateTime = $endDateTime
            keyId = $guid2
            startDateTime = $startDateTime 
            type = "AsymmetricX509Cert"
            usage = "Verify"
            key = $base64cer
            displayName = "CN=fourthcoffeetest"   
        }
        )  
    passwordCredentials = @(
        [ordered]@{
            customKeyIdentifier = $customKeyIdentifier
            keyId = $guid1           
            endDateTime = $endDateTime
            startDateTime = $startDateTime
            secretText = $password
        }
    )
    }
 
    $json = $object | ConvertTo-Json -Depth 99
    Write-Host "JSON Payload:"
    Write-Output $json
 
    # Request Header
    $Header = @{}
    $Header.Add("Authorization","Bearer $($GraphAccessToken)")
    $Header.Add("Content-Type","application/json")
 
    try 
    {
        Invoke-RestMethod -Uri $graphuri -Method "PATCH" -Headers $Header -Body $json
    } 
    catch 
    {
        # Dig into the exception to get the Response details.
        # Note that value__ is not a typo.
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__ 
        Write-Host "StatusDescription:" $_.Exception.Response.StatusDescription
    }
 
    Write-Host "Complete Request"
}
else
{
    Write-Host "Fail to get Access Token"
}
```

#### <a name="validate-token-signing-key"></a>토큰 서명 키 유효성 검사
클레임 매핑을 사용하도록 설정된 앱은 [OpenID Connect 메타데이터 요청](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)에 `appid={client_id}`를 추가하여 토큰 서명 키의 유효성을 검사해야 합니다. 사용해야 하는 OpenID Connect 메타데이터 문서의 형식은 다음과 같습니다.

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="update-the-application-manifest"></a>애플리케이션 매니페스트 업데이트

단일 테넌트 앱의 경우 [애플리케이션 매니페스트](reference-app-manifest.md)에서 `acceptMappedClaims` 속성을 `true`로 설정할 수 있습니다.  [Apiapplication 리소스 형식](/graph/api/resources/apiapplication#properties)에서 설명하는 것처럼, 애플리케이션은 사용자 지정 서명 키를 지정하지 않고도 클레임 매핑을 사용할 수 있습니다. 

> [!WARNING]
> 악의적인 행위자가 앱에 대한 클레임 매핑 정책을 만들 수 있는 다중 테넌트 앱의 경우 `acceptMappedClaims` 속성을 `true`로 설정하지 마세요.

이렇게 하려면 요청받은 토큰 사용자가 Azure AD 테넌트의 확인된 도메인 이름을 사용해야 합니다. 따라서 사용자는 (애플리케이션 매니페스트에서 `identifierUris`로 표시하는) `Application ID URI`를 `https://contoso.com/my-api`로 설정해야 합니다(또는 기본 테넌트 이름을 사용해야 합니다)`https://contoso.onmicrosoft.com/my-api`.

확인된 도메인을 사용하지 않는 경우 Azure AD는 "AcceptMappedClaims는 애플리케이션 GUID가 일치하는 토큰 사용자나 테넌트의 확인된 도메인에 있는 사용자에만 지원됩니다. 리소스 식별자를 변경하거나 애플리케이션별 서명 키를 사용하세요."라는 메시지를 포함하는 `AADSTS501461` 오류를 반환합니다.

## <a name="next-steps"></a>다음 단계

- 자세한 내용은 [클레임 매핑 정책 유형](reference-claims-mapping-policy-type.md) 참조 문서를 참조하세요.
- Azure Portal을 통해 SAML 토큰에서 발급된 클레임을 사용자 지정하는 방법에 대한 자세한 내용은 [방법: 엔터프라이즈 애플리케이션에 대한 SAML 토큰에 발급된 클레임 사용자 지정](active-directory-saml-claims-customization.md)을 참조하세요.
- 확장 특성에 대해 자세히 알아보려면 [클레임에서 디렉터리 스키마 확장 특성 사용](active-directory-schema-extensions.md)을 참조하세요.
