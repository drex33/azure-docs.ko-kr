---
title: Azure 파일 공유에 대한 AD DS 인증 사용
description: Azure 파일 공유에 대해 SMB를 통한 Active Directory Domain Services 인증을 사용하도록 설정하는 방법을 알아봅니다. 그러면 도메인 가입 Windows 가상 머신이 AD DS 자격 증명을 사용하여 Azure 파일 공유에 액세스할 수 있습니다.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 10/05/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9012f74f29c1e3ed768a32a6988c7aae527b44ce
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999153"
---
# <a name="part-one-enable-ad-ds-authentication-for-your-azure-file-shares"></a>1부: Azure 파일 공유에 대한 AD DS 인증 사용 설정 

AD DS(Active Directory Domain Services) 인증을 사용하도록 설정하기 전에 [개요 문서](storage-files-identity-auth-active-directory-enable.md)를 확인하여 지원되는 시나리오와 요구 사항을 이해해야 합니다.

이 문서에서는 스토리지 계정에서 AD DS(Active Directory Domain Services) 인증을 사용하도록 설정하는 데 필요한 프로세스를 설명합니다. 해당 기능을 사용하도록 설정한 후에는 Azure 파일 공유에 대한 인증에 AD DS 자격 증명을 사용하도록 스토리지 계정과 AD DS를 구성해야 합니다. Azure 파일 공유에 대해 SMB를 통한 AD DS 인증을 사용하도록 설정하려면 AD DS에 스토리지 계정을 등록한 후 스토리지 계정에 필요한 도메인 속성을 설정해야 합니다.

스토리지 계정을 AD DS에 등록하려면 AD DS에서 해당 계정을 나타내는 계정을 만듭니다. 이 프로세스는 AD DS에서 온-프레미스 Windows 파일 서버를 나타내는 계정을 만드는 것과 같다고 볼 수 있습니다. 스토리지 계정에서 해당 기능을 사용하도록 설정하면 계정에 있는 모든 신규 및 기존 파일 공유에 적용됩니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="option-one-recommended-use-azfileshybrid-powershell-module"></a>옵션 1(권장): AzFilesHybrid PowerShell 모듈 사용

AzFilesHybrid PowerShell 모듈의 cmdlet은 필요한 수정 작업을 수행하고 기능을 사용하도록 설정합니다. cmdlet의 일부는 온-프레미스 AD DS와 상호 작용하여 cmdlet이 수행하는 작업을 설명하기 때문에 변경 내용이 규정 준수 및 보안 정책과 일치하는지 확인하고 cmdlet을 실행할 수 있는 적절한 권한이 있는지 확인할 수 있습니다. AzFilesHybrid 모듈을 사용하는 것이 좋지만, 모듈을 사용할 수 없는 경우 수동으로 수행할 수 있도록 단계를 제공합니다.

### <a name="download-azfileshybrid-module"></a>AzFilesHybrid 모듈 다운로드

- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download/dotnet-framework/net472) 가 설치 되어 있지 않은 경우 지금 설치 합니다. 모듈을 성공적으로 가져오기 위해 필요합니다.
- [AzFilesHybrid 모듈(GA 모듈: v0.2.0+)을 다운로드하고 압축을 풉니다.](https://github.com/Azure-Samples/azure-files-samples/releases) 참고로 AES 256 Kerberos 암호화는 v0.2.2 이상에서만 지원됩니다. v0.2.2 미만의 AzFilesHybrid 버전에서 기능을 사용하도록 설정하고 AES 256 Kerberos 암호화를 지원하도록 업데이트하려는 경우 [이 문서](./storage-troubleshoot-windows-file-connection-problems.md#azure-files-on-premises-ad-ds-authentication-support-for-aes-256-kerberos-encryption)를 참조하세요.
- 대상 AD에서 서비스 로그온 계정 또는 컴퓨터 계정을 만들 수 있는 권한이 있는 AD DS 자격 증명을 사용하여 온-프레미스 AD DS에 도메인 가입된 디바이스에 모듈을 설치하고 실행합니다.
-  Azure AD에 동기화된 온-프레미스 AD DS 자격 증명을 사용하여 스크립트를 실행합니다. 온-프레미스 AD DS 자격 증명에는 스토리지 계정에 **소유자** 또는 **기여자** Azure 역할이 있어야 합니다.

### <a name="run-join-azstorageaccountforauth"></a>Join-AzStorageAccountForAuth 실행

`Join-AzStorageAccountForAuth` cmdlet은 지정된 스토리지 계정을 대신하여 오프라인 도메인 가입과 동일한 기능을 수행합니다. 스크립트는 cmdlet을 사용하여 AD 도메인에 [컴퓨터 계정](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)을 만듭니다. 컴퓨터 계정을 사용할 수 없는 경우에는 스크립트를 변경하여 [서비스 로그온 계정](/windows/win32/ad/about-service-logon-accounts)을 만들 수 있습니다. 명령을 수동으로 실행하도록 선택하는 경우 사용자 환경에 가장 적합한 계정을 선택해야 합니다.

cmdlet에서 만든 AD DS 계정은 스토리지 계정을 나타냅니다. 암호 만료를 적용하는 OU(조직 구성 단위)에서 AD DS 계정을 만든 경우 최대 암호 사용 기간 전에 암호를 업데이트해야 합니다. 해당 날짜 이전에 계정 암호를 업데이트하지 않으면 Azure 파일 공유에 액세스할 때 인증 오류가 발생합니다. 암호를 업데이트하는 방법을 알아보려면 [AD DS 계정 암호 업데이트](storage-files-identity-ad-ds-update-password.md)를 참조하세요.

PowerShell에서 실행하기 전에 아래 매개 변수에서 자리 표시자 값을 고유한 값으로 바꿉니다.
> [!IMPORTANT]
> 도메인 가입 cmdlet은 AD의 스토리지 계정(파일 공유)을 나타내는 AD 계정을 만듭니다. 컴퓨터 계정 또는 서비스 로그온 계정으로 등록하도록 선택할 수 있습니다. 자세한 내용은 [FAQ](./storage-files-faq.md#security-authentication-and-access-control)를 참조하세요. 컴퓨터 계정의 경우 AD에 기본 암호 만료 기간이 30일로 설정되어 있습니다. 마찬가지로, 서비스 로그온 계정에는 AD 도메인 또는 OU(조직 구성 단위)에 대한 기본 암호 만료 기간이 설정되어 있을 수 있습니다.
> 두 계정 유형 모두 AD 환경에 구성된 암호 만료 기간을 확인하고 최대 암호 사용 기간 전에 AD 계정의 [스토리지 계정 ID 암호를 업데이트](storage-files-identity-ad-ds-update-password.md)하도록 계획하는 것이 좋습니다. [AD에서 새 AD OU(조직 구성 단위)를 만들고](/powershell/module/activedirectory/new-adorganizationalunit) 이에 따라 [컴퓨터 계정](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/jj852252(v=ws.11)) 또는 서비스 로그온 계정에 대한 암호 만료 정책을 사용하지 않도록 설정할 수 있습니다. 

```PowerShell
# Change the execution policy to unblock importing AzFilesHybrid.psm1 module
Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser

# Navigate to where AzFilesHybrid is unzipped and stored and run to copy the files into your path
.\CopyToPSPath.ps1 

# Import AzFilesHybrid module
Import-Module -Name AzFilesHybrid

# Login with an Azure AD credential that has either storage account owner or contributer Azure role assignment
# If you are logging into an Azure environment other than Public (ex. AzureUSGovernment) you will need to specify that.
# See https://docs.microsoft.com/azure/azure-government/documentation-government-get-started-connect-with-ps
# for more information.
Connect-AzAccount

# Define parameters, $StorageAccountName currently has a maximum limit of 15 characters
$SubscriptionId = "<your-subscription-id-here>"
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"
$DomainAccountType = "<ComputerAccount|ServiceLogonAccount>" # Default is set as ComputerAccount
# If you don't provide the OU name as an input parameter, the AD identity that represents the storage account is created under the root directory.
$OuDistinguishedName = "<ou-distinguishedname-here>"
# Specify the encryption agorithm used for Kerberos authentication. Default is configured as "'RC4','AES256'" which supports both 'RC4' and 'AES256' encryption.
$EncryptionType = "<AES256|RC4|AES256,RC4>"

# Select the target subscription for the current session
Select-AzSubscription -SubscriptionId $SubscriptionId 

# Register the target storage account with your active directory environment under the target OU (for example: specify the OU with Name as "UserAccounts" or DistinguishedName as "OU=UserAccounts,DC=CONTOSO,DC=COM"). 
# You can use to this PowerShell cmdlet: Get-ADOrganizationalUnit to find the Name and DistinguishedName of your target OU. If you are using the OU Name, specify it with -OrganizationalUnitName as shown below. If you are using the OU DistinguishedName, you can set it with -OrganizationalUnitDistinguishedName. You can choose to provide one of the two names to specify the target OU.
# You can choose to create the identity that represents the storage account as either a Service Logon Account or Computer Account (default parameter value), depends on the AD permission you have and preference. 
# Run Get-Help Join-AzStorageAccountForAuth for more details on this cmdlet.

Join-AzStorageAccountForAuth `
        -ResourceGroupName $ResourceGroupName `
        -StorageAccountName $StorageAccountName `
        -DomainAccountType $DomainAccountType `
        -OrganizationalUnitDistinguishedName $OuDistinguishedName `
        -EncryptionType $EncryptionType

#Run the command below if you want to enable AES 256 authentication. If you plan to use RC4, you can skip this step.
Update-AzStorageAccountAuthForAES256 -ResourceGroupName $ResourceGroupName -StorageAccountName $StorageAccountName

#You can run the Debug-AzStorageAccountAuth cmdlet to conduct a set of basic checks on your AD configuration with the logged on AD user. This cmdlet is supported on AzFilesHybrid v0.1.2+ version. For more details on the checks performed in this cmdlet, see Azure Files Windows troubleshooting guide.
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="option-two-manually-perform-the-enablement-actions"></a>옵션 2: 수동으로 사용 설정 작업 수행

위의 `Join-AzStorageAccountForAuth` 스크립트를 이미 실행한 경우 [기능을 사용하도록 설정되었는지 확인](#confirm-the-feature-is-enabled) 섹션으로 이동합니다. 이 경우 다음 수동 단계를 수행할 필요가 없습니다.

### <a name="check-the-environment"></a>환경 확인

먼저, 환경의 상태를 확인해야 합니다. 특히 [Active Directory PowerShell](/powershell/module/activedirectory/)이 설치되어 있고 셸이 관리자 권한으로 실행되고 있는지 확인해야 합니다. 그런 다음, [Az.Storage 2.0 모듈(또는 이상)](https://www.powershellgallery.com/packages/Az.Storage/2.0.0)이 설치되어 있는지 확인하고, 그렇지 않으면 설치합니다. 해당 검사를 완료한 후 AD DS를 확인하여 이미 SPN/UPN을 사용하여 “cifs/your-storage-account-name-here.file.core.windows.net”으로 생성된 [컴퓨터 계정](/windows/security/identity-protection/access-control/active-directory-accounts#manage-default-local-accounts-in-active-directory)(기본값) 또는 [서비스 로그온 계정](/windows/win32/ad/about-service-logon-accounts)이 있는지 확인합니다. 계정이 존재하지 않는 경우 다음 섹션에 설명된 대로 계정을 만듭니다.

### <a name="create-an-identity-representing-the-storage-account-in-your-ad-manually"></a>수동으로 AD의 저장소 계정을 나타내는 id 만들기

수동으로 해당 계정을 만들려면 스토리지 계정에 대한 새 Kerberos 키를 만듭니다. 그런 다음 아래의 PowerShell cmdlet을 사용하여 해당 Kerberos 키를 계정의 암호로 사용합니다. 해당 키는 설치 중에만 사용되며 스토리지 계정에 대한 모든 제어 또는 데이터 평면 작업에는 사용할 수 없습니다. 

```PowerShell
# Create the Kerberos key on the storage account and get the Kerb1 key as the password for the AD identity to represent the storage account
$ResourceGroupName = "<resource-group-name-here>"
$StorageAccountName = "<storage-account-name-here>"

New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName kerb1
Get-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -ListKerbKey | where-object{$_.Keyname -contains "kerb1"}
```

해당 키를 만들었으면 다음 사양을 사용해서 OU에서 서비스 또는 컴퓨터 계정을 만듭니다. 예제 텍스트를 스토리지 계정 이름으로 바꿔야 합니다.

SPN: “cifs/your-storage-account-name-here.file.core.windows.net” 암호: 스토리지 계정에 대한 Kerberos 키

OU에서 암호 만료를 적용하는 경우 Azure 파일 공유에 액세스할 때 인증 오류가 발생하지 않도록 최대 암호 사용 기간 전에 암호를 업데이트해야 합니다. 자세한 내용은 [AD에서 스토리지 계정 ID의 암호 업데이트](storage-files-identity-ad-ds-update-password.md)를 참조하세요.

새로 만든 ID의 SID를 유지합니다. SID는 다음 단계에 필요합니다. 스토리지 계정을 나타내는 ID를 Azure AD에 동기화할 필요는 없습니다.

#### <a name="optional-enable-aes256-encryption"></a>필드 AES256 암호화 사용

AES 256 암호화를 사용 하려면이 섹션의 단계를 따르세요. RC4를 사용할 계획인 경우이 섹션을 건너뛸 수 있습니다.

저장소 계정을 나타내는 도메인 개체는 다음 요구 사항을 충족 해야 합니다.
- 저장소 계정 이름은 15 자를 초과할 수 없습니다.
- 도메인 개체를 온-프레미스 AD 도메인에 컴퓨터 개체로 만들어야 합니다.
- 후행 ' $ '를 제외 하 고 저장소 계정 이름은 컴퓨터 개체의 SamAccountName과 동일 해야 합니다.

도메인 개체가 이러한 요구 사항을 충족 하지 않는 경우 삭제 하 고 수행 하는 새 도메인 개체를 만듭니다.

및을 사용자의 값으로 바꾼 `<domain-object-identity>` `<domain-name>` 후 다음 명령을 사용 하 여 AES256 지원을 구성 합니다. 

```powershell
Set-ADComputer -Identity <domain-object-identity> -Server <domain-name> -KerberosEncryptionType "AES256"
```

이 명령을 실행 한 후 `<domain-object-identity>` 다음 스크립트의를 값으로 바꾸고 스크립트를 실행 하 여 도메인 개체 암호를 새로 고칩니다.

```powershell
$KeyName = "kerb1" # Could be either the first or second kerberos key, this script assumes we're refreshing the first
$KerbKeys = New-AzStorageAccountKey -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -KeyName $KeyName
$KerbKey = $KerbKeys | Where-Object {$_.KeyName -eq $KeyName} | Select-Object -ExpandProperty Value
$NewPassword = ConvertTo-SecureString -String $KerbKey -AsPlainText -Force

Set-ADAccountPassword -Identity <domain-object-identity> -Reset -NewPassword $NewPassword
```

### <a name="enable-the-feature-on-your-storage-account"></a>스토리지 계정에서 기능을 사용하도록 설정

이제 스토리지 계정에서 기능을 사용하도록 설정할 수 있습니다. 다음 명령에서 도메인 속성에 대한 구성 세부 정보를 제공하고 실행합니다. 다음 명령에 필요한 스토리지 계정 SID는 [이전 섹션](#create-an-identity-representing-the-storage-account-in-your-ad-manually)의 AD DS에서 만든 ID의 SID입니다.

```PowerShell
# Set the feature flag on the target storage account and provide the required AD domain information
Set-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>" `
        -EnableActiveDirectoryDomainServicesForFile $true `
        -ActiveDirectoryDomainName "<your-domain-name-here>" `
        -ActiveDirectoryNetBiosDomainName "<your-netbios-domain-name-here>" `
        -ActiveDirectoryForestName "<your-forest-name-here>" `
        -ActiveDirectoryDomainGuid "<your-guid-here>" `
        -ActiveDirectoryDomainsid "<your-domain-sid-here>" `
        -ActiveDirectoryAzureStorageSid "<your-storage-account-sid>"
```

### <a name="debugging"></a>디버깅

Debug-AzStorageAccountAuth cmdlet을 실행하여 로그온한 AD 사용자로 AD 구성에 대한 기본 검사 집합을 수행할 수 있습니다. 이 cmdlet은 AzFilesHybrid v0.1.2 이상 버전에서 지원됩니다. 해당 cmdlet에서 수행하는 검사에 대한 자세한 내용은 Windows 문제 해결 가이드의 [AD 자격 증명을 사용하여 Azure Files를 탑재할 수 없음](storage-troubleshoot-windows-file-connection-problems.md#unable-to-mount-azure-files-with-ad-credentials)을 참조하세요.

```PowerShell
Debug-AzStorageAccountAuth -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName -Verbose
```

## <a name="confirm-the-feature-is-enabled"></a>기능을 사용하도록 설정했는지 확인

다음 스크립트를 사용하여 스토리지 계정에서 기능을 사용하도록 설정되었는지 확인할 수 있습니다.

```PowerShell
# Get the target storage account
$storageaccount = Get-AzStorageAccount `
        -ResourceGroupName "<your-resource-group-name-here>" `
        -Name "<your-storage-account-name-here>"

# List the directory service of the selected service account
$storageAccount.AzureFilesIdentityBasedAuth.DirectoryServiceOptions

# List the directory domain information if the storage account has enabled AD DS authentication for file shares
$storageAccount.AzureFilesIdentityBasedAuth.ActiveDirectoryProperties
```

성공한 경우 출력은 다음과 같습니다.

```PowerShell
DomainName:<yourDomainHere>
NetBiosDomainName:<yourNetBiosDomainNameHere>
ForestName:<yourForestNameHere>
DomainGuid:<yourGUIDHere>
DomainSid:<yourSIDHere>
AzureStorageID:<yourStorageSIDHere>
```
## <a name="next-steps"></a>다음 단계

이제 스토리지 계정에서 해당 기능을 사용하도록 설정했습니다. 기능을 사용하려면 공유 수준 권한을 할당해야 합니다. 다음 섹션을 계속합니다.

[2부: ID에 공유 수준 권한 할당](storage-files-identity-ad-ds-assign-permissions.md)
