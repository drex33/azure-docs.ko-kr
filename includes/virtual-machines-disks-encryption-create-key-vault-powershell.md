---
title: 포함 파일
description: 포함 파일
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 10/25/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: f74228b918b3d87ab77b75132501327b08b25668
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131022052"
---
1. 최신 [Azure PowerShell 버전](/powershell/azure/install-az-ps)을 설치했으며 Connect-AzAccount를 사용하여 Azure 계정에 로그인했는지 확인합니다.

1. Azure Key Vault 및 암호화 키의 인스턴스를 만듭니다.

    Key Vault 인스턴스를 만드는 경우 제거 보호를 사용하도록 설정해야 합니다. 제거 보호를 사용하면 보존 기간이 지날 때까지 삭제된 키를 영구 삭제할 수 없습니다. 이러한 설정은 실수로 삭제하여 데이터가 손실되지 않도록 보호합니다. 이러한 설정은 관리 디스크를 암호화하기 위해 Key Vault를 사용하는 경우 필수입니다.
    
    ```powershell
    $ResourceGroupName="yourResourceGroupName"
    $LocationName="westcentralus"
    $keyVaultName="yourKeyVaultName"
    $keyName="yourKeyName"
    $keyDestination="Software"
    $diskEncryptionSetName="yourDiskEncryptionSetName"

    $keyVault = New-AzKeyVault -Name $keyVaultName `
    -ResourceGroupName $ResourceGroupName `
    -Location $LocationName `
    -EnablePurgeProtection

    $key = Add-AzKeyVaultKey -VaultName $keyVaultName `
          -Name $keyName `
          -Destination $keyDestination 
    ```

1.    DiskEncryptionSet의 인스턴스를 만듭니다. RotationToLatestKeyVersionEnabled를 $true와 같게 설정하여 키 자동 순환을 사용할 수 있습니다. 자동 순환을 사용하는 경우 시스템은 디스크 암호화 집합을 참조하는 모든 관리 디스크, 스냅샷, 이미지가 1시간 이내에 새 버전의 키를 사용하도록 자동으로 업데이트합니다.  
    
        ```powershell
      $desConfig=New-AzDiskEncryptionSetConfig -Location $LocationName `
            -SourceVaultId $keyVault.ResourceId `
            -KeyUrl $key.Key.Kid `
            -IdentityType SystemAssigned `
            -RotationToLatestKeyVersionEnabled $false

       $des=New-AzDiskEncryptionSet -Name $diskEncryptionSetName `
               -ResourceGroupName $ResourceGroupName `
               -InputObject $desConfig
        ```

1.    Key Vault에 대해 DiskEncryptionSet 리소스 액세스 권한을 부여합니다.

        > [!NOTE]
        > Azure가 Azure Active Directory에서 DiskEncryptionSet의 ID를 만드는 데는 몇 분 정도 걸릴 수 있습니다. 다음 명령을 실행할 때 "Active Directory 개체를 찾을 수 없습니다"와 같은 오류가 발생하면 몇 분 정도 기다린 후 다시 시도하세요.
        
        ```powershell  
        Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $des.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
        ```

### <a name="use-a-key-vault-in-a-different-subscription"></a>다른 구독에서 키 자격 증명 모음 사용

또는 단일 구독에서 Azure Key Vault를 중앙에서 관리하고 Key Vault 저장된 키를 사용하여 조직의 다른 구독에서 관리 디스크 및 스냅샷을 암호화할 수 있습니다. 이렇게 하면 보안 팀이 단일 구독에 강력한 보안 정책을 적용하고 쉽게 관리할 수 있습니다.

> [!IMPORTANT]
> 이 구성의 경우 Key Vault 및 디스크 암호화 집합이 모두 동일한 지역에 있어야 하며 동일한 테넌트를 사용해야 합니다.

다음 스크립트는 다른 구독이지만 동일한 지역에 있는 Key Vault 키를 사용하도록 디스크 암호화 집합을 구성하는 방법의 예입니다.

```azurepowershell
$sourceSubscriptionId="<sourceSubID>"
$sourceKeyVaultName="<sourceKVName>"
$sourceKeyName="<sourceKeyName>"

$targetSubscriptionId="<targetSubID>"
$targetResourceGroupName="<targetRGName>"
$targetDiskEncryptionSetName="<targetDiskEncSetName>"
$location="<targetRegion>"

Set-AzContext -Subscription $sourceSubscriptionId

$key = Get-AzKeyVaultKey -VaultName $sourceKeyVaultName -Name $sourceKeyName

Set-AzContext -Subscription $targetSubscriptionId

$desConfig=New-AzDiskEncryptionSetConfig -Location $location `
-KeyUrl $key.Key.Kid `
-IdentityType SystemAssigned `
-RotationToLatestKeyVersionEnabled $false

$des=New-AzDiskEncryptionSet -Name $targetDiskEncryptionSetName `
-ResourceGroupName $targetResourceGroupName `
-InputObject $desConfig
```