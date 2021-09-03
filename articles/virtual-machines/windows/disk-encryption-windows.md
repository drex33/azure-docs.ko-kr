---
title: Windows VM에 대한 Azure Disk Encryption 시나리오
description: 이 문서에서는 다양한 시나리오를 위한 Windows VM용 Microsoft Azure Disk Encryption을 사용하는 방법에 대한 지침을 제공합니다.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: windows
ms.topic: how-to
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f2aff30007da50ff1670f239a83f20fe6d8cfa33
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597177"
---
# <a name="azure-disk-encryption-scenarios-on-windows-vms"></a>Windows VM에 대한 Azure Disk Encryption 시나리오

Windows VM용 Azure Disk Encryption은 Windows의 BitLocker 기능을 사용하여 OS 디스크 및 데이터 디스크에 대한 전체 디스크 암호화를 제공합니다. 또한 VolumeType 매개 변수가 All일 때 임시 디스크의 암호화를 제공합니다.

Azure Disk Encryption은 [Azure Key Vault와 통합](disk-encryption-key-vault.md)되어 디스크 암호화 키와 비밀을 제어하고 관리하는 작업을 지원합니다. 서비스에 대한 개요는 [Windows VM용 Azure Disk Encryption](disk-encryption-overview.md)을 참조하세요.

디스크 암호화는 지원되는 VM [크기와 운영 체제](disk-encryption-overview.md#supported-vms-and-operating-systems)의 가상 머신에만 적용할 수 있습니다. 또한 다음과 같은 필수 조건도 충족해야 합니다.

- [네트워킹 요구 사항](disk-encryption-overview.md#networking-requirements)
- [그룹 정책 요구 사항](disk-encryption-overview.md#group-policy-requirements)
- [암호화 키 스토리지 요구 사항](disk-encryption-overview.md#encryption-key-storage-requirements)

>[!IMPORTANT]
> - 이전에 VM을 암호화하기 위해 Azure AD에서 Azure Disk Encryption을 사용한 적이 있다면 VM을 암호화하는 데 이 옵션을 계속 사용해야 합니다. 자세한 내용은 [Azure AD(이전 릴리스)를 포함한 Azure Disk Encryption](disk-encryption-overview-aad.md)을 참조하세요.
>
> - 디스크를 암호화하기 전에 [스냅샷 만들기](snapshot-copy-managed-disk.md) 및/또는 백업을 수행해야 합니다. 백업은 암호화 도중에 예기치 않은 오류가 발생할 경우 복구 옵션을 사용할 수 있습니다. 암호화가 수행되기 전에 관리 디스크가 있는 VM은 백업해야 합니다. 백업이 완료되면 [Set-AzVMDiskEncryptionExtension cmdlet](/powershell/module/az.compute/set-azvmdiskencryptionextension)을 사용하여 -skipVmBackup 매개 변수를 지정함으로써 관리 디스크를 암호화할 수 있습니다. 암호화된 VM을 백업하고 복원하는 방법에 대한 자세한 내용은 [암호화된 Azure VM 백업 및 복원](../../backup/backup-azure-vms-encryption.md)을 참조하세요.
>
> - 암호화하거나 암호화를 사용하지 않도록 설정하면 VM이 다시 부팅될 수 있습니다.

## <a name="install-tools-and-connect-to-azure"></a>도구 설치 및 Azure에 연결

[!INCLUDE [disk-encryption-install-cli-powershell](../../../includes/disk-encryption-install-cli-powershell.md)]

## <a name="enable-encryption-on-an-existing-or-running-windows-vm"></a>기존 또는 실행 중인 Windows VM에서 암호화 사용
이 시나리오에서는 Resource Manager 템플릿, PowerShell cmdlet 또는 CLI 명령을 사용하여 암호화를 사용하도록 설정할 수 있습니다. 가상 머신 확장에 대한 스키마 정보가 필요한 경우 [Windows용 Azure Disk Encryption 확장](../extensions/azure-disk-enc-windows.md) 문서를 참조하세요.

### <a name="enable-encryption-on-existing-or-running-vms-with-azure-powershell"></a> Azure PowerShell을 통해 기존 또는 실행 중인 VM에서 암호화 사용
[Set-AzVMDiskEncryptionExtension](/powershell/module/az.compute/set-azvmdiskencryptionextension) cmdlet을 사용하여 Azure에서 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다.

-  **실행 중인 VM 암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId;
    ```
- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;

     ```

   >[!NOTE]
   > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br>
key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다.

- **디스크가 암호화되어 있는지 확인:** IaaS VM의 암호화 상태를 확인하려면 [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) cmdlet을 사용합니다.
     ```azurepowershell-interactive
     Get-AzVmDiskEncryptionStatus -ResourceGroupName 'MyVirtualMachineResourceGroup' -VMName 'MySecureVM'
     ```

암호화를 사용하지 않도록 설정하려면 [암호화 사용 안 함 및 암호화 확장 제거](#disable-encryption-and-remove-the-encryption-extension)를 참조하세요.

### <a name="enable-encryption-on-existing-or-running-vms-with-the-azure-cli"></a>Azure CLI를 통해 기존 또는 실행 중인 VM에서 암호화 사용

Azure에서 [az vm encryption enable](/cli/azure/vm/encryption#az_vm_encryption_enable) 명령을 사용하여 실행 중인 IaaS 가상 머신에서 암호화를 사용하도록 설정합니다.

- **실행 중인 VM 암호화:**

    ```azurecli-interactive
    az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type [All|OS|Data]
    ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type [All|OS|Data]
     ```

     >[!NOTE]
     > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다. </br>
  key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다.

- **디스크가 암호화되어 있는지 확인:** IaaS VM의 암호화 상태를 확인하려면 [az vm encryption show](/cli/azure/vm/encryption#az_vm_encryption_show) 명령을 사용합니다.

     ```azurecli-interactive
     az vm encryption show --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup"
     ```

암호화를 사용하지 않도록 설정하려면 [암호화 사용 안 함 및 암호화 확장 제거](#disable-encryption-and-remove-the-encryption-extension)를 참조하세요.

### <a name="using-the-resource-manager-template"></a>Resource Manager 템플릿 사용

[실행 중인 Windows VM을 암호화하기 위해 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/encrypt-running-windows-vm-without-aad)을 사용하여 Azure에서 기존 또는 실행 중인 IaaS Windows VM에 디스크 암호화를 사용하도록 설정할 수 있습니다.


1. Azure 빠른 시작 템플릿에서 **Azure에 배포** 를 클릭합니다.

2. 구독, 리소스 그룹, 위치, 설정, 약관 및 규약을 선택합니다. **구매** 를 클릭하여 기존 또는 실행 중인 IaaS VM에서 암호화를 사용하도록 설정합니다.

다음 표에서는 기존 또는 실행 중인 VM에 대한 Resource Manager 템플릿 매개 변수를 나열합니다.

| 매개 변수 | Description |
| --- | --- |
| vmName | 암호화 작업을 실행할 VM의 이름. |
| keyVaultName | BitLocker 키가 업로드될 Key Vault의 이름. cmdlet `(Get-AzKeyVault -ResourceGroupName <MyKeyVaultResourceGroupName>). Vaultname` 또는 Azure CLI 명령 `az keyvault list --resource-group "MyKeyVaultResourceGroup"`을 사용하여 가져올 수 있습니다.|
| keyVaultResourceGroup | 키 자격 증명 모음을 포함하는 리소스 그룹의 이름|
|  keyEncryptionKeyURL | https://&lt;keyvault-name&gt;.vault.azure.net/key/&lt;key-name&gt; 형식으로 된 키 암호화 키의 URL. KEK을 사용하지 않으려면 이 필드를 비워 둡니다. |
| volumeType | 암호화 작업을 수행할 볼륨의 유형. 유효한 값은 _OS_, _Data_ 및 _All_ 입니다.
| forceUpdateTag | 작업을 강제로 실행해야 할 때마다 GUID 같은 고유한 값으로 전달합니다. |
| resizeOSDisk | 시스템 볼륨을 분할하기 전에 전체 OS VHD를 채우려면 OS 파티션 크기를 조정해야 합니다. |
| 위치 | 모든 리소스에 대한 위치. |

## <a name="enable-encryption-on-nvme-disks-for-lsv2-vms"></a>Lsv2 VM의 NVMe 디스크에서 암호화 사용

이 시나리오에서는 Lsv2 시리즈 VM에 대해 NVMe 디스크에서 Azure Disk Encryption을 사용 하도록 설정하는 방법을 설명합니다.  Lsv2 시리즈는 로컬 NVMe 스토리지를 특징으로 합니다. 로컬 NVMe 디스크는 임시 디스크이며 VM을 중지/할당 취소할 경우 이러한 디스크의 데이터가 손실됩니다([Lsv2-series](../lsv2-series.md) 참조).

NVMe 디스크에서 암호화를 사용 하도록 설정 하려면:

1. NVMe 디스크를 초기화하고 NTFS 볼륨을 만듭니다.
1. VolumeType 매개 변수를 All로 설정하여 VM에서 암호화를 사용하도록 설정합니다. 그러면 NVMe 디스크에서 지원하는 볼륨을 포함하여 모든 OS 및 데이터 디스크에 대해 암호화가 사용됩니다. 자세한 내용은 [기존 또는 실행 중인 Windows VM에서 암호화 사용](#enable-encryption-on-an-existing-or-running-windows-vm)을 참조하세요.

다음 시나리오에서는 NVMe 디스크에서 암호화가 유지됩니다.
- VM 재부팅
- 가상 머신 확장 집합을 이미지로 다시 설치
- OS 교환

다음 시나리오에서는 NVMe 디스크가 초기화되지 않습니다.

- 할당 취소 후 VM 시작
- 서비스 복구
- Backup

이러한 시나리오에서는 VM이 시작 된 후 NVMe 디스크를 초기화해야 합니다. NVMe 디스크에서 암호화를 사용하도록 설정하려면 명령을 실행하여 NVMe 디스크가 초기화된 후 Azure Disk Encryption를 다시 사용하도록 설정합니다.

[지원되지 않는 시나리오](#unsupported-scenarios) 섹션에 나열된 시나리오 외에도, 다음에 대해 NVMe 디스크의 암호화가 지원되지 않습니다.

- AAD와 함께 Azure Disk Encryption으로 암호화된 VM(이전 릴리스)
- 스토리지 공간이 있는 NVMe 디스크
- NVMe 디스크를 사용하는 SKU의 Azure Site Recovery([Azure 지역 간 Azure VM 재해 복구를 위한 지원 매트릭스: 복제된 머신 - 스토리지](../../site-recovery/azure-to-azure-support-matrix.md#replicated-machines---storage) 참조).

## <a name="new-iaas-vms-created-from-customer-encrypted-vhd-and-encryption-keys"></a> 고객 암호화 VHD 및 암호화 키로 만든 새 IaaS VM

이 시나리오에서는 PowerShell cmdlet 또는 CLI 명령을 사용하여 미리 암호화된 VHD와 연결된 암호화 키에서 새 VM을 만들 수 있습니다.

[미리 암호화된 Windows VHD 준비](disk-encryption-sample-scripts.md#prepare-a-pre-encrypted-windows-vhd)의 지침을 사용합니다. 이미지를 만든 후 다음 섹션의 단계를 사용하여 암호화된 Azure VM을 만들 수 있습니다.


### <a name="encrypt-vms-with-pre-encrypted-vhds-with-azure-powershell"></a> Azure PowerShell을 통해 미리 암호화된 VHD가 있는 VM 암호화
[Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk#examples) PowerShell cmdlet을 사용하여 암호화된 VHD에서 디스크 암호화를 사용하도록 설정할 수 있습니다. 아래 예제에서는 몇 가지 공통 매개 변수를 제공합니다.

```azurepowershell
$VirtualMachine = New-AzVMConfig -VMName "MySecureVM" -VMSize "Standard_A1"
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -Name "SecureOSDisk" -VhdUri "os.vhd" Caching ReadWrite -Windows -CreateOption "Attach" -DiskEncryptionKeyUrl "https://mytestvault.vault.azure.net/secrets/Test1/514ceb769c984379a7e0230bddaaaaaa" -DiskEncryptionKeyVaultId "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myKVresourcegroup/providers/Microsoft.KeyVault/vaults/mytestvault"
New-AzVM -VM $VirtualMachine -ResourceGroupName "MyVirtualMachineResourceGroup"
```

## <a name="enable-encryption-on-a-newly-added-data-disk"></a>새로 추가된 데이터 디스크에서 암호화 사용
[PowerShell을 사용하여 새 디스크를 Windows에 추가](attach-disk-ps.md)하거나 [Azure Portal을 통해](attach-managed-disk-portal.md) 추가할 수 있습니다.

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-powershell"></a>Azure PowerShell을 사용하여 새로 추가된 디스크에서 암호화 사용
 PowerShell을 사용하여 Windows VM용 새 디스크를 암호화하는 경우 새 시퀀스 버전을 지정해야 합니다. 시퀀스 버전은 고유해야 합니다. 아래 스크립트는 시퀀스 버전에 대한 GUID를 생성합니다. 경우에 따라 새로 추가된 데이터 디스크가 Azure Disk Encryption 확장에 의해 자동으로 암호화될 수 있습니다. 자동 암호화는 일반적으로 새 디스크가 온라인 상태가 된 후에 VM이 다시 부팅되는 경우에 발생합니다. 일반적으로 이전에 VM에서 디스크 암호화를 실행했을 때 볼륨 유형으로 “모두”를 지정했기 때문에 발생합니다. 새로 추가된 데이터 디스크에서 자동 암호화가 발생하는 경우 새 시퀀스 버전으로 Set-AzVmDiskEncryptionExtension cmdlet을 다시 실행하는 것이 좋습니다. 새 데이터 디스크가 자동으로 암호화되는 경우 암호화하지 않으려면, 먼저 모든 드라이브를 암호 해독한 다음, 볼륨 유형으로 OS를 지정하여 새 시퀀스 버전으로 다시 암호화합니다.



-  **실행 중인 VM 암호화:** 아래 스크립트는 변수를 초기화하고 Set-AzVMDiskEncryptionExtension cmdlet을 실행합니다. 리소스 그룹, VM 및 키 자격 증명 모음은 필수 구성 요소로 이미 만들어져 있어야 합니다. MyKeyVaultResourceGroup, MyVirtualMachineResourceGroup, MySecureVM 및 MySecureVault를 사용자 값으로 바꿉니다. 이 예제에서는 -VolumeType 매개 변수에 OS 및 데이터 볼륨을 모두 포함하는 "All"을 사용합니다. OS 볼륨만 암호화하려면 -VolumeType 매개 변수에 "OS"를 사용합니다.

     ```azurepowershell
      $KVRGname = 'MyKeyVaultResourceGroup';
      $VMRGName = 'MyVirtualMachineResourceGroup';
      $vmName = 'MySecureVM';
      $KeyVaultName = 'MySecureVault';
      $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
      $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
      $KeyVaultResourceId = $KeyVault.ResourceId;
      $sequenceVersion = [Guid]::NewGuid();

      Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;
    ```
- **KEK를 사용하여 실행 중인 VM 암호화:** 이 예제에서는 -VolumeType 매개 변수에 "All"을 사용하며 여기에는 OS 및 데이터 볼륨이 모두 포함됩니다. OS 볼륨만 암호화하려면 -VolumeType 매개 변수에 "OS"를 사용합니다.

     ```azurepowershell
     $KVRGname = 'MyKeyVaultResourceGroup';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     $vmName = 'MyExtraSecureVM';
     $KeyVaultName = 'MySecureVault';
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $diskEncryptionKeyVaultUrl = $KeyVault.VaultUri;
     $KeyVaultResourceId = $KeyVault.ResourceId;
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     $sequenceVersion = [Guid]::NewGuid();

     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGname -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId -VolumeType "All" –SequenceVersion $sequenceVersion;

     ```

    >[!NOTE]
    > disk-encryption-keyvault 매개 변수의 값 구문은 전체 식별자 문자열, 즉 /subscriptions/[subscription-id-guid]/resourceGroups/[resource-group-name]/providers/Microsoft.KeyVault/vaults/[keyvault-name]입니다.</br>
key-encryption-key 매개변수의 값 구문은 KEK의 전체 URI, 즉 https://[keyvault-name].vault.azure.net/keys/[kekname]/[kek-unique-id]입니다.

### <a name="enable-encryption-on-a-newly-added-disk-with-azure-cli"></a>Azure CLI를 사용하여 새로 추가된 디스크에서 암호화 사용

 암호화를 사용하도록 설정하는 명령을 실행하는 경우 Azure CLI 명령은 자동으로 새 순서 버전을 제공합니다. 이 예제에서는 volume-type 매개 변수에 "All"을 사용합니다. OS 디스크만 암호화하는 경우에는 volume-type 매개 변수를 OS로 변경해야 합니다. PowerShell 구문과 달리 CLI에서는 사용자가 암호화를 사용하도록 설정할 때 고유 시퀀스 버전을 제공하지 않아도 됩니다. CLI는 고유 시퀀스 버전 값을 자동으로 생성하여 사용합니다.

-  **실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault "MySecureVault" --volume-type "All"
     ```

- **KEK를 사용하여 실행 중인 VM 암호화:**

     ```azurecli-interactive
     az vm encryption enable --resource-group "MyVirtualMachineResourceGroup" --name "MySecureVM" --disk-encryption-keyvault  "MySecureVault" --key-encryption-key "MyKEK_URI" --key-encryption-keyvault "MySecureVaultContainingTheKEK" --volume-type "All"
     ```

## <a name="disable-encryption-and-remove-the-encryption-extension"></a>암호화 사용 안 함 및 암호화 확장 제거

Azure Disk Encryption 확장을 사용하지 않도록 설정하고 Azure Disk Encryption 확장을 제거할 수 있습니다. 이는 두 가지 별개의 작업입니다. 

ADE를 제거하려면 먼저 암호화를 사용하지 않도록 설정한 다음 확장을 제거하는 것이 좋습니다. 사용하지 않도록 설정하지 않고 암호화 확장을 제거하면 디스크가 계속 암호화됩니다. 확장을 제거한 **후** 암호화를 사용하지 않도록 설정하면 암호 해독 작업을 수행하기 위해 확장이 다시 설치되고 두 번째 제거되어야 합니다.

### <a name="disable-encryption"></a>암호화 사용 안 함

Azure PowerShell, Azure CLI 또는 Resource Manager 템플릿을 사용하여 암호화를 사용하지 않도록 설정할 수 있습니다. 암호화를 사용하지 않도록 설정해도 확장은 제거되지 **않습니다**([암호화 확장 제거](#remove-the-encryption-extension) 참조).

> [!WARNING]
> OS와 데이터 디스크가 모두 암호화된 경우 데이터 디스크 암호화를 사용하지 않도록 설정하면 예기치 않은 결과가 발생할 수 있습니다. 대신 모든 디스크에 암호화를 사용하지 않도록 설정하십시오.
>
> 암호화를 사용하지 않도록 설정하면 BitLocker의 백그라운드 프로세스가 시작되어 디스크의 암호를 해독합니다. 이 프로세스는 암호화를 다시 사용하도록 설정하기 전에 완료할 수 있는 충분한 시간이 주어져야 합니다.  

- **Azure PowerShell을 사용하여 디스크 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) cmdlet을 사용합니다.

     ```azurepowershell-interactive
     Disable-AzVMDiskEncryption -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "MySecureVM" -VolumeType "all"
     ```

- **Azure CLI를 사용하여 암호화 사용 안 함:** 암호화를 사용하지 않도록 설정하려면 [az vm encryption disable](/cli/azure/vm/encryption#az_vm_encryption_disable) 명령을 사용합니다. 

     ```azurecli-interactive
     az vm encryption disable --name "MySecureVM" --resource-group "MyVirtualMachineResourceGroup" --volume-type "all"
     ```

- **Resource Manager 템플릿으로 암호화를 사용하지 않도록 설정:** 

    1. [실행중인 Windows VM에서 디스크 암호화 사용 안 함](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.compute/decrypt-running-windows-vm-without-aad) 템플릿에서 **Azure에 배포** 를 클릭합니다.
    2. 구독, 리소스 그룹, 위치, VM, 볼륨 유형, 약관 및 규약을 선택합니다.
    3.  **구매** 를 클릭하여 실행 중인 Windows VM에서 디스크 암호화를 사용하지 않도록 설정합니다.

### <a name="remove-the-encryption-extension"></a>암호화 확장 제거

디스크의 암호를 해독하고 암호화 확장을 제거하려면 확장을 제거하기 **전에** 암호화를 사용하지 않도록 설정해야 합니다. [암호화 사용 안 함](#disable-encryption)을 참조하세요.

Azure PowerShell 또는 Azure CLI를 사용하여 암호화 확장을 제거할 수 있습니다. 

- **Azure PowerShell로 디스크 암호화 사용 안 함:** 암호화를 제거하려면 [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension) cmdlet을 사용합니다.

     ```azurepowershell-interactive
     Remove-AzVMDiskEncryptionExtension -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "MySecureVM"
     ```

- **Azure CLI로 암호화 사용 안 함:** 암호화를 제거하려면 [az vm extension delete](/cli/azure/vm/extension#az_vm_extension_delete) 명령을 사용합니다.

     ```azurecli-interactive
     az vm extension delete -g "MyVirtualMachineResourceGroup" --vm-name "MySecureVM" -n "AzureDiskEncryptionForWindows"
     ```

## <a name="unsupported-scenarios"></a>지원되지 않는 시나리오

Azure Disk Encryption은 다음과 같은 시나리오, 기능 및 기술에 대해 작동하지 않습니다.

- 클래식 VM 만들기 방법을 통해 만든 VM 또는 기본 계층 VM을 암호화
- 소프트웨어 기반 RAID 시스템으로 구성된 VM 암호화
- S2D(스토리지 공간 다이렉트) 또는 Windows 스토리지 공간으로 구성된 2016 이전 Windows Server 버전을 사용하여 구성된 VM 암호화
- 온-프레미스 키 관리 시스템과의 통합
- Azure 파일(공유 파일 시스템)
- NFS(네트워크 파일 시스템)
- 동적 볼륨
- 각 컨테이너에 대해 동적 볼륨을 만드는 Windows Server 컨테이너
- 사용 후 삭제 OS 디스크
- DFS, GFS, DRDB, CephFS와 같은 공유/분산 파일 시스템의 암호화
- 암호화된 VM을 다른 구독 또는 지역으로 이동
- 암호화된 VM의 이미지 또는 스냅샷을 만들어 추가 VM을 배포하는 데 사용
- 쓰기 가속기 디스크가 있는 M 시리즈 VM
- [고객 관리형 키를 사용한 서버 쪽 암호화](../disk-encryption.md)(SSE + CMK)로 암호화된 디스크가 있는 VM에 ADE 적용. ADE로 암호화된 VM의 데이터 디스크에 SSE + CMK를 적용하는 것도 지원되지 않는 시나리오입니다.
- ADE로 암호화되었거나 ADE로 암호화된 **적이** 있는 VM을 [고객 관리형 키를 사용한 서버 쪽 암호화](../disk-encryption.md)로 마이그레이션
- 장애 조치(failover) 클러스터에서 VM 암호화
- [Azure 울트라 디스크](../disks-enable-ultra-ssd.md) 암호화

## <a name="next-steps"></a>다음 단계

- [Azure Disk Encryption 개요](disk-encryption-overview.md)
- [Azure Disk Encryption 샘플 스크립트](disk-encryption-sample-scripts.md)
- [Azure Disk Encryption 문제 해결](disk-encryption-troubleshooting.md)
