---
title: Windows VM용 Azure Disk Encryption 샘플 스크립트
description: 이 문서는 Windows VM용 Microsoft Azure Disk Encryption에 대한 부록입니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 27d29256f85d932727b9c859eb0dd0965596220a
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122698292"
---
# <a name="azure-disk-encryption-sample-scripts"></a>Azure Disk Encryption 샘플 스크립트

**적용 대상:** :heavy_check_mark: Windows VM 

이 문서에서는 미리 암호화된 VHD 및 기타 작업을 준비하기 위한 샘플 스크립트를 제공합니다.

> [!NOTE]
> 모든 스크립트는 달리 명시된 경우를 제외하면 AAD가 아닌 ADE 최신 버전을 참조합니다.

## <a name="sample-powershell-scripts-for-azure-disk-encryption"></a>Azure Disk Encryption용 샘플 PowerShell 스크립트


- **구독에서 암호화된 VM 모두 나열**

  [이 PowerShell 스크립트](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VM.ps1)를 사용하여 구독에 있는 리소스 그룹 전체에서 모든 ADE 암호화 VM 및 확장 버전을 찾을 수 있습니다.

  아니면 cmdlet이 모든 ADE 암호화 VM(확장 버전 제외)을 표시합니다.

    ```azurepowershell-interactive
    $osVolEncrypted = {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).OsVolumeEncrypted}
    $dataVolEncrypted= {(Get-AzVMDiskEncryptionStatus -ResourceGroupName $_.ResourceGroupName -VMName $_.Name).DataVolumesEncrypted}
    Get-AzVm | Format-Table @{Label="MachineName"; Expression={$_.Name}}, @{Label="OsVolumeEncrypted"; Expression=$osVolEncrypted}, @{Label="DataVolumesEncrypted"; Expression=$dataVolEncrypted}
    ```

- **구독에 있는 암호화된 VMSS 인스턴스 모두 나열**

    [이 PowerShell 스크립트](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/Find_1passAdeVersion_VMSS.ps1)를 사용하여 구독에 있는 모든 리소스 그룹에서 모든 ADE 암호화 VMSS 인스턴스 및 확장 버전을 찾을 수 있습니다.

- **키 자격 증명 모음에서 VM 암호화에 사용된 디스크 암호화 비밀 모두 나열**

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName $KeyVaultName | where {$_.Tags.ContainsKey('DiskEncryptionKeyFileName')} | format-table @{Label="MachineName"; Expression={$_.Tags['MachineName']}}, @{Label="VolumeLetter"; Expression={$_.Tags['VolumeLetter']}}, @{Label="EncryptionKeyURL"; Expression={$_.Id}}
```

### <a name="using-the-azure-disk-encryption-prerequisites-powershell-script"></a> Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트 사용

Azure Disk Encryption에 대한 필수 구성 요소에 이미 익숙한 경우 [Azure Disk Encryption 필수 구성 요소 PowerShell 스크립트](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 )를 사용할 수 있습니다. 이 PowerShell 스크립트 사용의 예는 [VM 암호화 빠른 시작](disk-encryption-powershell-quickstart.md)를 참조하세요. 줄 211에서 시작하는 스크립트의 섹션에서 주석을 제거하여 기존 리소스 그룹의 기존 VM에 대한 모든 디스크를 암호화할 수 있습니다.

다음 표는 PowerShell 스크립트에서 사용할 수 있는 매개 변수를 보여줍니다.

|매개 변수|Description|필수?|
|------|------|------|
|$resourceGroupName| KeyVault가 속해 있는 리소스 그룹의 이름입니다.  이 이름을 가진 새 리소스 그룹이 없는 경우 생성됩니다.| True|
|$keyVaultName|암호화 키가 배치된 KeyVault의 이름입니다. 이 이름을 가진 새 자격 증명 모음이 없는 경우 생성됩니다.| True|
|$location|KeyVault의 위치입니다. 암호화할 KeyVault 및 VM이 동일한 위치에 있는지 확인합니다. `Get-AzLocation`을 사용하여 위치 목록을 가져옵니다.|True|
|$subscriptionId|사용할 Azure 구독의 식별자입니다.  구독 ID는 `Get-AzSubscription`을 사용하여 가져올 수 있습니다.|True|
|$aadAppName|KeyVault에 비밀을 쓰는 데 사용할 Azure AD 애플리케이션의 이름입니다. 이 이름을 가진 새 애플리케이션이 없는 경우 생성됩니다. 이 앱이 이미 있는 경우 스크립트에 aadClientSecret 매개 변수를 전달합니다.|False|
|$aadClientSecret|이전에 만든 Azure AD 애플리케이션의 클라이언트 비밀입니다.|False|
|$keyEncryptionKeyName|KeyVault의 선택적 키 암호화 키의 이름입니다. 이 이름을 가진 새 키가 없는 경우 생성됩니다.|False|

## <a name="resource-manager-templates"></a>리소스 관리자 템플릿

### <a name="encrypt-or-decrypt-vms-without-an-azure-ad-app"></a>Azure AD 앱 없이 VM을 암호화 또는 암호 해독

- [기존 또는 실행 중인 Windows VM에서 디스크 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm-without-aad)
- [실행 중인 Windows VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-windows-vm-without-aad)

### <a name="encrypt-or-decrypt-vms-with-an-azure-ad-app-previous-release"></a>Azure AD 앱으로 VM을 암호화 또는 암호 해독(이전 릴리스)

- [기존 또는 실행 중인 Windows VM에서 디스크 암호화 사용](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm)
- [실행 중인 Windows VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-windows-vm)
- [미리 암호화된 VHD/스토리지 Blob에서 새로운 암호화된 관리 디스크 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/create-encrypted-managed-disk)
    - 미리 암호화된 VHD 및 해당 암호화 설정을 제공하는 새로운 암호화된 관리 디스크 만들기

## <a name="prepare-a-pre-encrypted-windows-vhd"></a>사전에 암호화된 Windows VHD 준비
Azure IaaS에서 암호화된 VHD로 배포용으로 사전에 암호화된 Windows VHD를 준비하려면 이어지는 섹션이 필요합니다. 이 정보를 사용하여 Azure Site Recovery 또는 Azure에서 최신 Windows VM(VHD)을 준비 및 부팅합니다. VHD를 준비하고 업로드하는 방법에 대한 자세한 내용은 [일반화된 VHD를 업로드하고 사용하여 Azure에서 새 VM 만들기](upload-generalized-managed.md)를 참조하세요.

### <a name="update-group-policy-to-allow-non-tpm-for-os-protection"></a>OS 보호를 위해 비-TPM을 허용하도록 그룹 정책 업데이트
**BitLocker 드라이브 암호화** 라는 BitLocker 그룹 정책 설정을 구성하는데, **로컬 컴퓨터 정책** > **컴퓨터 구성** > **관리 템플릿** > **Windows 구성 요소** 아래에 있습니다. 다음 그림처럼 이 설정을 **운영 체제 드라이브** > **시작 시 추가 인증 요구** > **호환되는 TPM 없이 BitLocker 허용** 으로 변경합니다.

![Azure의 Microsoft 맬웨어 방지](../media/disk-encryption/disk-encryption-fig8.png)

### <a name="install-bitlocker-feature-components"></a>BitLocker 기능 구성 요소 설치
Windows Server 2012 이상에서는 다음 명령을 사용합니다.

```console
dism /online /Enable-Feature /all /FeatureName:BitLocker /quiet /norestart
```

Windows Server 2008 R2에서는 다음 명령을 사용합니다.

```console
ServerManagerCmd -install BitLockers
```

### <a name="prepare-the-os-volume-for-bitlocker-by-using-bdehdcfg"></a>`bdehdcfg`를 사용하여 BitLocker에 대한 OS 볼륨 준비
OS 파티션을 압축하고 BitLocker용 머신을 준비하려면 필요한 경우 [bdehdcfg](/windows/security/information-protection/bitlocker/bitlocker-basic-deployment)를 실행합니다.

```console
bdehdcfg -target c: shrink -quiet
```

### <a name="protect-the-os-volume-by-using-bitlocker"></a>BitLocker를 사용하여 OS 볼륨 보호
[`manage-bde`](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/ff829849(v=ws.11)) 명령으로 외부 키 보호기를 사용하여 부팅 볼륨에서 암호화를 사용하도록 설정합니다. 도한 외부 드라이브 또는 볼륨에 외부 키(.bek 파일)를 배치합니다. 암호화는 다음 재부팅 후 시스템/부팅 볼륨에 사용하도록 설정됩니다.

```console
manage-bde -on %systemdrive% -sk [ExternalDriveOrVolume]
reboot
```

> [!NOTE]
> BitLocker를 사용하여 외부 키를 가져오기 위해서는 별도의 데이터/리소스 VHD로 VM을 준비합니다.

## <a name="upload-encrypted-vhd-to-an-azure-storage-account"></a>Azure Storage 계정에 암호화된 VHD 업로드
DM-Crypt 암호화를 사용하도록 설정한 후에는 로컬 암호화된 VHD를 스토리지 계정에 업로드해야 합니다.
```powershell
    Add-AzVhd [-Destination] <Uri> [-LocalFilePath] <FileInfo> [[-NumberOfUploaderThreads] <Int32> ] [[-BaseImageUriToPatch] <Uri> ] [[-OverWrite]] [ <CommonParameters>]
```

## <a name="upload-the-secret-for-the-pre-encrypted-vm-to-your-key-vault"></a> 미리 암호화된 VM에 대한 비밀을 키 자격 증명 모음에 업로드
이전에 가져온 디스크 암호화 비밀을 키 자격 증명 모음에 비밀 업로드해야 합니다.  이렇게 하려면 암호를 업로드할 계정에 대한 wrapkey 권한 및 set secret 권한을 부여해야 합니다.

```powershell
# Typically, account Id is the user principal name (in user@domain.com format)
$upn = (Get-AzureRmContext).Account.Id
Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# In cloud shell, the account ID is a managed service identity, so specify the username directly
# $upn = "user@domain.com"
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -UserPrincipalName $acctid -PermissionsToKeys wrapKey -PermissionsToSecrets set

# When running as a service principal, retrieve the service principal ID from the account ID, and set access policy to that
# $acctid = (Get-AzureRmContext).Account.Id
# $spoid = (Get-AzureRmADServicePrincipal -ServicePrincipalName $acctid).Id
# Set-AzKeyVaultAccessPolicy -VaultName $kvname -ObjectId $spoid -BypassObjectIdValidation -PermissionsToKeys wrapKey -PermissionsToSecrets set

```

### <a name="disk-encryption-secret-not-encrypted-with-a-kek"></a>KEK로 암호화되지 않은 디스크 암호화 암호
키 자격 증명 모음에서 비밀을 설정하려면 [Set-AzKeyVaultSecret](/powershell/module/az.keyvault/set-azkeyvaultsecret)을 사용합니다. 암호는 base64 문자열로 인코딩된 후 키 자격 증명 모음으로 업로드됩니다. 또한 Key Vault에서 비밀을 만들 때 다음 태그가 설정되었는지 확인합니다.

```powershell

 # This is the passphrase that was provided for encryption during the distribution installation
 $passphrase = "contoso-password"

 $tags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
 $secretName = [guid]::NewGuid().ToString()
 $secretValue = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($passphrase))
 $secureSecretValue = ConvertTo-SecureString $secretValue -AsPlainText -Force

 $secret = Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $secretName -SecretValue $secureSecretValue -tags $tags
 $secretUrl = $secret.Id
```


[KEK를 사용하지 않고 OS 디스크를 연결](#without-using-a-kek)하기 위해 다음 단계에서 `$secretUrl`을 사용합니다.

### <a name="disk-encryption-secret-encrypted-with-a-kek"></a>KEK로 암호화된 디스크 암호화 암호
비밀을 Key Vault에 업로드하기 전에 주요 암호화 키를 사용하여 선택적으로 암호화할 수 있습니다. 먼저 래핑 [API](/rest/api/keyvault/wrapkey)를 사용하여 주요 암호화 키로 비밀을 암호화합니다. 이 래핑 작업의 출력은 base64 URL 인코딩 문자열로, [`Set-AzKeyVaultSecret`](/powershell/module/az.keyvault/set-azkeyvaultsecret) cmdlet을 사용하여 비밀로 업로드할 수 있습니다.

```powershell
    # This is the passphrase that was provided for encryption during the distribution installation
    $passphrase = "contoso-password"

    Add-AzKeyVaultKey -VaultName $KeyVaultName -Name "keyencryptionkey" -Destination Software
    $KeyEncryptionKey = Get-AzKeyVaultKey -VaultName $KeyVault.OriginalVault.Name -Name "keyencryptionkey"

    $apiversion = "2015-06-01"

    ##############################
    # Get Auth URI
    ##############################

    $uri = $KeyVault.VaultUri + "/keys"
    $headers = @{}

    $response = try { Invoke-RestMethod -Method GET -Uri $uri -Headers $headers } catch { $_.Exception.Response }

    $authHeader = $response.Headers["www-authenticate"]
    $authUri = [regex]::match($authHeader, 'authorization="(.*?)"').Groups[1].Value

    Write-Host "Got Auth URI successfully"

    ##############################
    # Get Auth Token
    ##############################

    $uri = $authUri + "/oauth2/token"
    $body = "grant_type=client_credentials"
    $body += "&client_id=" + $AadClientId
    $body += "&client_secret=" + [Uri]::EscapeDataString($AadClientSecret)
    $body += "&resource=" + [Uri]::EscapeDataString("https://vault.azure.net")
    $headers = @{}

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $access_token = $response.access_token

    Write-Host "Got Auth Token successfully"

    ##############################
    # Get KEK info
    ##############################

    $uri = $KeyEncryptionKey.Id + "?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token}

    $response = Invoke-RestMethod -Method GET -Uri $uri -Headers $headers

    $keyid = $response.key.kid

    Write-Host "Got KEK info successfully"

    ##############################
    # Encrypt passphrase using KEK
    ##############################

    $passphraseB64 = [Convert]::ToBase64String([System.Text.Encoding]::ASCII.GetBytes($Passphrase))
    $uri = $keyid + "/encrypt?api-version=" + $apiversion
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"alg" = "RSA-OAEP"; "value" = $passphraseB64}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method POST -Uri $uri -Headers $headers -Body $body

    $wrappedSecret = $response.value

    Write-Host "Encrypted passphrase successfully"

    ##############################
    # Store secret
    ##############################

    $secretName = [guid]::NewGuid().ToString()
    $uri = $KeyVault.VaultUri + "/secrets/" + $secretName + "?api-version=" + $apiversion
    $secretAttributes = @{"enabled" = $true}
    $secretTags = @{"DiskEncryptionKeyEncryptionAlgorithm" = "RSA-OAEP"; "DiskEncryptionKeyFileName" = "LinuxPassPhraseFileName"}
    $headers = @{"Authorization" = "Bearer " + $access_token; "Content-Type" = "application/json"}
    $bodyObj = @{"value" = $wrappedSecret; "attributes" = $secretAttributes; "tags" = $secretTags}
    $body = $bodyObj | ConvertTo-Json

    $response = Invoke-RestMethod -Method PUT -Uri $uri -Headers $headers -Body $body

    Write-Host "Stored secret successfully"

    $secretUrl = $response.id
```

`$KeyEncryptionKey` 및 `$secretUrl`은 [KEK를 사용하여 OS 디스크를 연결](#using-a-kek)하기 위해 다음 단계에서 사용합니다.

##  <a name="specify-a-secret-url-when-you-attach-an-os-disk"></a>OS 디스크를 연결할 때 비밀 URL 지정

###  <a name="without-using-a-kek"></a>KEK 사용 안 함
OS 디스크를 연결하는 동안 `$secretUrl`을 전달해야 합니다. URL은 "KEK로 암호화되지 않은 디스크 암호화 비밀" 섹션에서 생성했습니다.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $VhdUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl
```
### <a name="using-a-kek"></a>KEK 사용
OS 디스크를 연결할 때 `$KeyEncryptionKey` 및 `$secretUrl`을 전달합니다. URL은 "KEK로 암호화된 디스크 암호화 비밀" 섹션에서 생성했습니다.
```powershell
    Set-AzVMOSDisk `
            -VM $VirtualMachine `
            -Name $OSDiskName `
            -SourceImageUri $CopiedTemplateBlobUri `
            -VhdUri $OSDiskUri `
            -Windows `
            -CreateOption FromImage `
            -DiskEncryptionKeyVaultId $KeyVault.ResourceId `
            -DiskEncryptionKeyUrl $SecretUrl `
            -KeyEncryptionKeyVaultId $KeyVault.ResourceId `
            -KeyEncryptionKeyURL $KeyEncryptionKey.Id
```
