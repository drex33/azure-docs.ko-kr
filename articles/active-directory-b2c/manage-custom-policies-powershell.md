---
title: PowerShell을 사용하여 사용자 지정 정책 관리
titleSuffix: Azure AD B2C
description: Azure AD B2C 사용자 지정 정책을 프로그래밍 방식으로 관리하려면 Azure AD(Azure Active Directory) PowerShell cmdlet을 사용합니다. PowerShell을 사용하여 사용자 지정 정책을 만들고, 읽고, 업데이트하고, 삭제합니다.
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: kengaderdus
ms.subservice: B2C
ms.openlocfilehash: 4a10b8f2d8c06056a986e99852ba5b822dcb3df9
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130041750"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Azure PowerShell을 사용하여 Azure AD B2C 사용자 지정 정책 관리

Azure PowerShell은 Azure AD B2C 테넌트에서 명령줄 및 스크립트 기반 사용자 지정 정책 관리를 위한 몇 가지 cmdlet을 제공합니다. Azure AD PowerShell 모듈을 사용하여 다음을 수행하는 방법을 알아봅니다.

* Azure AD B2C 테넌트의 사용자 지정 정책 나열
* 테넌트에서 정책 다운로드
* 콘텐츠를 덮어써서 기존 정책 업데이트
* Azure AD B2C 테넌트에 새 정책 업로드
* 테넌트에서 사용자 지정 정책 삭제

## <a name="prerequisites"></a>필수 구성 요소

* [Azure AD B2C 테넌트](tutorial-create-tenant.md) 및 [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 역할을 가진 디렉터리의 사용자에 대한 자격 증명
* 테넌트에 업로드된 [사용자 지정 정책](tutorial-create-user-flows.md?pivots=b2c-custom-policy)
* [Azure AD PowerShell for Graph **미리 보기 모듈**](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>B2C 테넌트에 PowerShell 세션 연결

Azure AD B2C 테넌트에서 사용자 지정 정책에 대한 작업을 수행하려면 먼저 [Connect-AzureAD][Connect-AzureAD] 명령을 사용하여 PowerShell 세션을 테넌트에 연결해야 합니다.

`{b2c-tenant-name}`을 Azure AD B2C 테넌트의 이름으로 대체하여 다음 명령을 실행합니다. 디렉터리에서 [B2C IEF 정책 관리자](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) 역할이 할당된 계정으로 로그인합니다.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

성공적인 로그인을 보여 주는 명령 출력의 예:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>테넌트의 모든 사용자 지정 정책 나열

사용자 지정 정책을 검색하면 Azure AD B2C 관리자가 비즈니스 논리를 검토, 관리 및 해당 작업에 추가할 수 있습니다. [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 Azure AD B2C 테넌트의 사용자 지정 정책 ID 목록을 반환합니다.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

명령 출력의 예:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>정책 다운로드

정책 ID의 목록을 검토한 후에는 [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy]를 사용하여 특정 정책을 대상으로 지정하여 해당 콘텐츠를 다운로드할 수 있습니다.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

이 예에서는 ID가 *B2C_1A_signup_signin* 인 정책이 다운로드됩니다.

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

정책 콘텐츠를 로컬로 편집하려면 `-OutputFilePath` 인수를 사용하여 명령 출력을 파일로 파이프한 다음 원하는 편집기에서 파일을 엽니다.

파일에 출력을 보내는 명령의 예:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>기존 정책 업데이트

만들거나 다운로드한 정책 파일을 편집한 후에는 [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 Azure AD B2C에 업데이트된 정책을 게시할 수 있습니다.

Azure AD B2C 테넌트에 이미 있는 정책의 ID를 사용하여 `Set-AzureADMSTrustFrameworkPolicy` 명령을 실행하는 경우 해당 정책의 콘텐츠를 덮어씁니다.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

예제 명령:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

추가 예제는 [Set-AzureADMSTrustFrameworkPolicy][Set-AzureADMSTrustFrameworkPolicy] 명령 참조를 참조하세요.

## <a name="upload-a-new-policy"></a>새 정책 업로드

프로덕션에서 실행되는 사용자 지정 정책을 변경하는 경우 대체 또는 A/B 테스트 시나리오에 대한 여러 버전의 정책을 게시할 수 있습니다. 또는 기존 정책의 복사본을 만들고 몇 개의 작은 변경 내용을 수정한 다음 다른 애플리케이션에서 사용하기 위한 새 정책으로 업로드할 수 있습니다.

[New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 새 정책을 업로드합니다.

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

예제 명령:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>사용자 지정 정책 삭제

정상적인 작업 수명 주기를 유지하려면 사용하지 않는 사용자 지정 정책을 정기적으로 제거하는 것이 좋습니다. 예를 들어 새 정책 집합에 대한 마이그레이션을 수행하고 새 정책의 기능을 확인한 후 이전 정책 버전을 제거하는 것이 좋습니다. 또한 사용자 지정 정책 집합을 게시하고 오류를 수신하려는 경우 실패한 릴리스의 일부로 생성된 정책을 제거하는 것이 적합할 수 있습니다.

[Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] 명령을 사용하여 테넌트에서 정책을 삭제합니다.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

예제 명령:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>정책 업로드 문제 해결

새 사용자 지정 정책을 게시하거나 기존 정책을 업데이트하는 경우 정책 파일 상속 체인에서 잘못된 XML 형식 지정 및 오류가 발생하면 유효성 검사가 실패할 수 있습니다.

예를 들어 다음은 잘못된 XML이 포함된 콘텐츠를 사용하여 정책을 업데이트하려고 시도하는 경우입니다. 출력은 간결하게 하기 위해 잘립니다.

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

사용자 지정 정책 문제 해결에 대한 자세한 내용은 [Azure AD B2C 사용자 지정 정책 및 Identity Experience Framework 문제 해결](./troubleshoot-custom-policies.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

PowerShell을 사용하여 CI/CD(연속 통합/지속적인 업데이트) 파이프라인의 일부로 사용자 지정 정책을 배포하는 방법에 대한 자세한 내용은 [Azure DevOps 파이프라인에서 사용자 지정 정책 배포](deploy-custom-policies-devops.md)를 참조하세요.

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
