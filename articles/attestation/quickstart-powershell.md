---
title: Azure PowerShell을 사용하여 Azure Attestation 설정
description: Azure PowerShell을 사용하여 증명 공급자를 설정하고 구성하는 방법입니다.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c7acaadefc3e249971f69bc833831bf14246ba88
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130044576"
---
# <a name="quickstart-set-up-azure-attestation-with-azure-powershell"></a>빠른 시작: Azure PowerShell을 사용하여 Azure Attestation 설정

다음 단계에 따라 Azure PowerShell을 사용하여 증명 공급자를 만들고 구성합니다. Azure PowerShell을 설치하고 실행하는 방법에 대한 자세한 내용은 [Azure PowerShell 개요](/powershell/azure/)를 참조하세요.

PowerShell 갤러리에는 더 이상 사용되지 않는 TLS(전송 계층 보안) 버전 1.0 및 1.1이 있습니다. TLS 버전 1.2 이상을 사용하는 것이 좋습니다. 따라서 다음과 같은 오류가 발생할 수 있습니다.

- 경고: 패키지 원본 'https://www.powershellgallery.com/api/v2 '를 확인할 수 없습니다.
- PackageManagement\Install-Package: 지정한 검색 조건 및 모듈 이름과 일치하는 항목이 없습니다. 

PowerShell 갤러리와 계속 상호 작용하려면 Install-Module 명령을 실행하기 전에 먼저 다음 명령을 실행합니다.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
```

## <a name="install-azattestation-powershell-module"></a>Az.Attestation PowerShell 모듈 설치

Azure PowerShell이 있는 컴퓨터에 Azure Attestation에 대한 cmdlet이 포함된 Az.Attestation PowerShell 모듈을 설치합니다.  

### <a name="initial-installation"></a>초기 설치

기존의 모든 PowerShell 창을 종료합니다.

"현재 사용자"용으로 설치하려면 관리자가 아닌 권한으로 PowerShell 창을 시작하고 다음을 실행합니다.

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope CurrentUser
```

"모든 사용자"용으로 설치하려면 관리자 권한으로 PowerShell 창을 시작하고 다음을 실행합니다.

```powershell
Install-Module -Name Az.Attestation -AllowClobber -Scope AllUsers
```

관리자 권한으로 PowerShell 콘솔을 닫습니다.

### <a name="update-the-installation"></a>설치 업데이트

기존의 모든 PowerShell 창을 종료합니다.

"현재 사용자"용으로 업데이트하려면 관리자가 아닌 권한으로 PowerShell 창을 시작하고 다음을 실행합니다.

```powershell
Update-Module -Name Az.Attestation
```

"모든 사용자"용으로 업데이트하려면 관리자 권한으로 PowerShell 창을 시작하고 다음을 실행합니다.

```powershell
Update-Module -Name Az.Attestation
```

관리자 권한으로 PowerShell 콘솔을 닫습니다.

### <a name="get-installed-modules"></a>설치된 모듈 가져오기

증명 작업을 지원하는 데 필요한 Az 모듈의 최소 버전은 다음과 같습니다.
- Az 4.5.0
- Az.Accounts 1.9.2
- Az.Attestation 1.0.0

아래 명령을 실행하여 모든 Az 모듈의 설치된 버전을 확인합니다. 

```powershell
Get-InstalledModule
```
버전이 최소 요구 사항과 일치하지 않으면 Update-Module 명령을 실행합니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

PowerShell 콘솔에서 관리자가 아닌 액세스 권한으로 Azure에 로그인합니다.

```powershell
Connect-AzAccount
```

필요한 경우 Azure Attestation에 사용할 구독으로 전환합니다.

```powershell
Set-AzContext -Subscription <subscription id>  
```

## <a name="register-microsoftattestation-resource-provider"></a>Microsoft.Attestation 리소스 공급자 등록

Microsoft.Attestation 리소스 공급자를 구독에 등록합니다. Azure 리소스 공급자 및 이러한 리소스 공급자를 구성하고 관리하는 방법에 대한 자세한 내용은 [Azure 리소스 공급자 및 유형](../azure-resource-manager/management/resource-providers-and-types.md)을 참조하세요. 리소스 공급자는 구독에 대해 한 번만 등록해야 합니다.

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Attestation
```
## <a name="regional-availability-of-azure-attestation"></a>지역별 Azure Attestation 가용성

```powershell
(Get-AzResourceProvider -ProviderNamespace Microsoft.Attestation)[0].Locations
```

## <a name="create-an-azure-resource-group"></a>Azure 리소스 그룹 만들기

증명 공급자에 대한 리소스 그룹을 만듭니다. 다른 Azure 리소스(클라이언트 애플리케이션 인스턴스가 있는 가상 머신 포함)를 동일한 리소스 그룹에 배치할 수 있습니다.

```powershell
$location = "uksouth" 
$attestationResourceGroup = "<attestation provider resource group name>"
New-AzResourceGroup -Name $attestationResourceGroup -Location $location 
```

 > [!NOTE]
   > 이 리소스 그룹에 증명 공급자가 생성되면 Azure AD 사용자에게 정책 구성/정책 서명자 인증서 관리와 같은 작업을 수행하는 공급자에 대한 **증명 기여자** 역할이 있어야 합니다. 이러한 권한은 구독/리소스 그룹에 대한 **소유자**(와일드카드 권한)/ **기여자**(와일드카드 권한)와 같은 역할로 상속될 수도 있습니다.  


## <a name="create-and-manage-an-attestation-provider"></a>증명 공급자 만들기 및 관리

New-AzAttestation은 증명 공급자를 만듭니다.

```powershell
$attestationProvider = "<attestation provider name>" 
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location
```

PolicySignerCertificateFile은 신뢰할 수 있는 서명 키 세트를 지정하는 파일입니다. PolicySignerCertificateFile 매개 변수에 대한 파일 이름이 지정되면 서명된 JWT 형식의 정책으로만 증명 공급자를 구성할 수 있습니다. 그 밖의 다른 정책은 텍스트 또는 서명되지 않은 JWT 형식으로 구성할 수 있습니다.

```powershell
New-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Location $location -PolicySignersCertificateFile "C:\test\policySignersCertificates.pem"
```

PolicySignersCertificateFile 샘플은 [정책 서명자 인증서 예제](policy-signer-examples.md)를 참조하세요.

Get-AzAttestation은 status 및 AttestURI와 같은 증명 공급자 속성을 검색합니다. 나중에 필요하므로 AttestURI를 적어 둡니다.

```azurepowershell
Get-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup  
```

위 명령은 아래와 같은 출력을 생성해야 합니다. 

```
Id:/subscriptions/MySubscriptionID/resourceGroups/MyResourceGroup/providers/Microsoft.Attestation/attestationProviders/MyAttestationProvider
Location: MyLocation
ResourceGroupName: MyResourceGroup
Name: MyAttestationProvider
Status: Ready
TrustModel: AAD
AttestUri: https://MyAttestationProvider.us.attest.azure.net 
Tags: 
TagsTable: 
```

증명 공급자는 Remove-AzAttestation cmdlet을 사용하여 삭제할 수 있습니다.  

```powershell
Remove-AzAttestation -Name $attestationProvider -ResourceGroupName $attestationResourceGroup
```

## <a name="policy-management"></a>정책 관리

정책을 관리하려면 Azure AD 사용자에게 "작업"에 대한 다음 권한이 필요합니다.
- Microsoft.Attestation/attestationProviders/attestation/read
- Microsoft.Attestation/attestationProviders/attestation/write
- Microsoft.Attestation/attestationProviders/attestation/delete

 이러한 작업을 수행하려면 Azure AD 사용자에게 증명 공급자에 대한 **증명 기여자** 역할이 있어야 합니다. 이러한 권한은 구독/리소스 그룹에 대한 **소유자**(와일드카드 권한)/ **기여자**(와일드카드 권한)와 같은 역할로 상속될 수도 있습니다.  

정책을 읽으려면 Azure AD 사용자에게 "작업"에 대한 다음 권한이 필요합니다.
- Microsoft.Attestation/attestationProviders/attestation/read

 이 작업을 수행하려면 Azure AD 사용자에게 증명 공급자에 대한 **증명 리더** 역할이 있어야 합니다. 읽기 권한은 구독/리소스 그룹에 대한 **리더**(와일드카드 권한)와 같은 역할로 상속될 수도 있습니다.  

아래의 PowerShell cmdlet은 증명 공급자에 대한 정책 관리를 제공합니다(한 번에 하나의 TEE).

Get-AzAttestationPolicy는 지정된 TEE에 대한 현재 정책을 반환합니다. Cmdlet은 정책의 텍스트 및 JWT 형식 모두에 정책을 표시합니다.

```powershell
$teeType = "<tee Type>"
Get-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

지원되는 TEE 형식은 "SgxEnclave", "OpenEnclave" 및 "VbsEnclave"입니다.

Set-AttestationPolicy는 지정된 TEE에 대한 새 정책을 설정합니다. cmdlet은 텍스트 또는 JWT 형식의 정책을 허용하며 PolicyFormat 매개 변수로 제어됩니다. "Text"는 PolicyFormat의 기본값입니다. 

```powershell
$policyFormat = "<policy format>"
$policy=Get-Content -path "C:\test\policy.txt" -Raw
Set-AzAttestationPolicy   -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType -Policy $policy -PolicyFormat $policyFormat 
```

증명 공급자를 만드는 동안 PolicySignerCertificateFile이 제공되면 정책을 서명된 JWT 형식으로만 구성할 수 있습니다. 그 밖의 다른 정책은 텍스트 또는 서명되지 않은 JWT 형식으로 구성할 수 있습니다.

JWT 형식의 증명 정책에는 "AttestationPolicy"라는 클레임이 포함되어야 합니다. 서명된 정책의 경우 JWT는 기존 정책 서명자 인증서에 해당하는 프라이빗 키로 서명해야 합니다.

정책 샘플은 [증명 정책 예제](policy-examples.md)를 참조하세요.

Reset-AzAttestationPolicy는 지정된 TEE에 대한 정책을 기본값으로 다시 설정합니다.

```powershell
Reset-AzAttestationPolicy -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Tee $teeType 
```

## <a name="policy-signer-certificates-management"></a>정책 서명자 인증서 관리

아래의 PowerShell cmdlet은 증명 공급자에 대한 정책 서명자 인증서 관리를 제공합니다.

```powershell
Get-AzAttestationPolicySigners -Name $attestationProvider -ResourceGroupName $attestationResourceGroup

Add-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>

Remove-AzAttestationPolicySigner -Name $attestationProvider -ResourceGroupName $attestationResourceGroup -Signer <signer>
```

정책 서명자 인증서는 "maa-policyCertificate"라는 클레임이 있는 서명된 JWT입니다. 클레임 값은 추가할 신뢰할 수 있는 서명 키를 포함하는 JWK입니다. JWT는 기존 정책 서명자 인증서에 해당하는 프라이빗 키로 서명해야 합니다.

정책 서명자 인증서의 모든 의미 체계 조작은 PowerShell 외부에서 수행해야 합니다. PowerShell과 관련하여 이는 간단한 문자열입니다.

정책 서명자 인증서 샘플은 [정책 서명자 인증서 예제](policy-signer-examples.md)를 참조하세요.

cmdlet 및 해당 매개 변수에 대한 자세한 내용은 [Azure Attestation PowerShell cmdlet](/powershell/module/az.attestation/#attestation)을 참조하세요. 

## <a name="next-steps"></a>다음 단계

- [증명 정책을 작성하고 서명하는 방법](author-sign-policy.md)
- [코드 샘플을 사용하여 SGX enclave 증명](/samples/browse/?expanded=azure&terms=attestation)
