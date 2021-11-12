---
title: Service Fabric 관리형 클러스터 노드에 대해 디스크 암호화 사용
description: ARM 템플릿을 사용하여 Windows에서 Azure Service Fabric 관리 클러스터 노드에 디스크 암호화를 사용하도록 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 11/8/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: cd16cdea3a5c8fa4f235ff14ab75bdd07e348db0
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352696"
---
# <a name="enable-disk-encryption-for-service-fabric-managed-cluster-nodes"></a>Service Fabric 관리형 클러스터 노드에 디스크 암호화 사용

Service Fabric 관리형 클러스터는 조직의 보안 및 규정 준수 약정에 맞게 데이터를 보호하는 데 도움이 되는 두 가지 디스크 암호화 옵션을 지원합니다. 권장되는 옵션은 호스트의 암호화이지만 Azure Disk Encryption 지원합니다. 디스크 [암호화 옵션을](../virtual-machines/disk-encryption-overview.md) 검토하고 선택한 옵션이 요구 사항을 충족하는지 확인합니다.


## <a name="enable-encryption-at-host-preview"></a>호스트에서 암호화 사용(미리 보기)

이 암호화 방법은 [Azure Storage](how-to-managed-cluster-enable-disk-encryption.md) 서비스에서 데이터를 암호화하여 VM에 대해 사용자 지정 이미지를 비롯한 모든 OS 유형 및 이미지를 지원하여 Azure Disk Encryption 향상시킵니다. 이 방법은 VM CPU를 사용하지 않으며 워크로드가 사용 가능한 모든 VM SKU 리소스를 사용할 수 있도록 VM 성능에 영향을 주지 않습니다.

> [!Note]
> 기존 노드 형식에서는 를 사용하도록 설정할 수 없습니다. 새 노드 유형을 프로비전하고 워크로드를 마이그레이션해야 합니다.

> [!Note]
> Azure Security Center 호스트에서 암호화를 사용하는 경우 현재 디스크 암호화 상태가 비정상으로 표시됩니다.

다음 단계에 따라 이 [샘플 템플릿을](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-HostEncryption) 참조하여 호스트 암호화를 사용하도록 설정된 새 Service Fabric 관리형 클러스터를 배포합니다.

1. 다음 [제한 사항을](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#restrictions) 검토하여 요구 사항을 충족하는지 확인합니다.

2. 클러스터를 배포하기 전에 필요한 [필수 구성을](../virtual-machines/windows/disks-enable-host-based-encryption-powershell.md#prerequisites) 설정합니다.

3. 구성 된 `enableEncryptionAtHost` 각 노드 유형 디스크 암호화에 대 한 관리 되는 클러스터 템플릿에서 속성이 필요 합니다. 샘플은 미리 구성되어 있습니다.

   * Service Fabric 관리형 클러스터 리소스 apiVersion은 **2021-11-01-preview** 이상이어야 합니다.

   ```json
        {
               "apiVersion": "[variables('sfApiVersion')]",
               "type": "Microsoft.ServiceFabric/managedclusters/nodetypes",
               "name": "[concat(parameters('clusterName'), '/', parameters('nodeTypeName'))]",
               "location": "[resourcegroup().location]",
               "properties": {
                   "enableEncryptionAtHost": true
                   ...
               }
       }
   ```

4. 배포 및 확인

   호스트 암호화를 사용하도록 설정된 관리형 클러스터를 배포합니다.

   ```powershell
   $clusterName = "<clustername>" 
   $resourceGroupName = "<rg-name>"

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   `Get-AzVmssDiskEncryption` 명령을 사용하여 노드 유형의 기본 확장 집합에서 디스크 암호화 상태를 확인할 수 있습니다. 먼저 관리형 클러스터의 지원 리소스 그룹 이름(기본 가상 네트워크, 부하 분산 장치, 공용 IP, NSG, 확장 집합 및 스토리지 계정 포함)을 찾아야 합니다. `VmssName`을 확인하려는 클러스터 노드 유형 이름(배포 템플릿에 지정된 이름)으로 수정해야 합니다.

   ```powershell
   $NodeTypeName = "NT2"
   $clustername = <clustername>
   $resourceGroupName = "<rg-name>"
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   $VMSS = Get-AzVmss -ResourceGroupName $supportResourceGroupName -Name $NodeTypeName
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   ```

   반환 출력은 다음과 유사하게 표시됩니다.

   ```console
   $VMSS.VirtualMachineProfile.SecurityProfile.EncryptionAtHost
   True
   ```

## <a name="enable-azure-disk-encryption"></a>Azure Disk Encryption 사용
Azure Disk Encryption Linux의 DM-Crypt 기능 또는 Windows BitLocker 기능을 사용하여 Azure VM(가상 머신)의 OS 및 데이터 디스크에 볼륨 암호화를 제공합니다. ADE는 Azure Key Vault와 통합되어 디스크 암호화 키와 비밀을 제어하고 관리할 수 있습니다.

이 가이드에서는 ARM(Azure Resource Manager) 템플릿을 통해 [가상 머신 확장 집합](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)에 대한 [Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md) 기능을 사용하여 Windows에서 Service Fabric 관리 클러스터 노드에 디스크 암호화를 사용하도록 설정하는 방법을 알아봅니다.

1. Azure Disk Encryption 등록

   가상 머신 확장 집합용 디스크 암호화 미리 보기에는 자체 등록이 필요합니다. 다음 명령을 실행합니다.

   ```powershell
   Register-AzProviderFeature -FeatureName "UnifiedDiskEncryption" -ProviderNamespace "Microsoft.Compute"
   ```

   다음을 실행하여 등록 상태를 확인합니다.

   ```powershell
   Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -FeatureName "UnifiedDiskEncryption"
   ```

   상태가 '등록됨'으로 변경되면 진행할 준비가 된 것입니다.

2. 디스크 암호화용 키 자격 증명 모음 프로비전

   Azure Disk Encryption은 Azure Key Vault를 사용하여 키 디스크 암호화 키와 비밀을 제어하고 관리합니다. 키 자격 증명 모음과 Service Fabric 관리 클러스터는 동일한 Azure 지역 및 구독에 상주해야 합니다. 이러한 요구 사항이 충족되는 한 신규 또는 기존 키 자격 증명 모음을 디스크 암호화에 사용할 수 있습니다.

3. 디스크 암호화가 설정된 키 자격 증명 모음 만들기

   다음 명령을 실행하여 디스크 암호화를 위한 새 키 자격 증명 모음을 만듭니다. Key Vault 지역이 클러스터와 동일한 지역에 있는지 확인합니다.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   New-AzResourceGroup -Name $resourceGroupName -Location eastus2 
   New-AzKeyVault -ResourceGroupName $resourceGroupName -Name $keyvaultName -Location eastus2 -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   $resourceGroupName = "<rg-name>" 
   $keyvaultName = "<kv-name>" 

   az keyvault create --resource-group $resourceGroupName --name $keyvaultName --enabled-for-disk-encryption
   ```

   ---

4. 디스크 암호화를 사용하도록 기존 키 자격 증명 모음 업데이트

   다음 명령을 실행하여 기존 키 자격 증명 모음에 대해 디스크 암호화를 사용하도록 설정합니다.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Set-AzKeyVaultAccessPolicy -ResourceGroupName $resourceGroupName -VaultName $keyvaultName -EnabledForDiskEncryption
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```azurecli
   az keyvault update --name keyvaultName --enabled-for-disk-encryption 
   ```

   ---

### <a name="update-the-template-and-parameters-files-for-disk-encryption"></a>디스크 암호화를 위한 템플릿 및 매개 변수 파일 업데이트

다음 단계에서는 [기존 관리 클러스터](tutorial-managed-cluster-deploy.md)에서 디스크 암호화를 사용하도록 설정하는 데 필요한 템플릿 변경 내용을 안내합니다. 또는 https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption 템플릿을 사용하여 디스크 암호화가 설정된 새 Service Fabric 관리 클러스터를 배포할 수 있습니다.

1. 템플릿에 다음 매개 변수를 추가하여 `keyVaultResourceId` 아래에서 사용자 고유의 구독, 리소스 그룹 이름 및 자격 증명 모음 이름으로 대체합니다.

   ```json
   "parameters": {
    "keyVaultResourceId": { 
      "type": "string", 
      "defaultValue": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>", 
      "metadata": { 
      "description": "Full resource id of the Key Vault used for disk encryption." 
   } 
    },
    "volumeType": { 
     "type": "string", 
     "defaultValue": "All", 
     "metadata": { 
      "description": "Type of the volume OS or Data to perform encryption operation" 
   }
   }
   }, 
   ```

2. 그런 다음 템플릿의 관리 클러스터 노드 유형에 `AzureDiskEncryption` VM 확장을 추가합니다.

   ```json
   "properties": { 
   "vmExtensions": [ 
   { 
   "name": "AzureDiskEncryption", 
   "properties": { 
     "publisher": "Microsoft.Azure.Security", 
     "type": "AzureDiskEncryption", 
     "typeHandlerVersion": "2.2", 
     "autoUpgradeMinorVersion": true, 
     "settings": {      
           "EncryptionOperation": "EnableEncryption", 
           "KeyVaultURL": "[reference(parameters('keyVaultResourceId'),'2016-10-01').vaultUri]", 
        "KeyVaultResourceId": "[parameters('keyVaultResourceID')]",
        "VolumeType": "[parameters('volumeType')]" 
        } 
      } 
   } 
   ] 
   } 
   ```

3. 마지막으로 *keyVaultResourceId* 에서 사용자 고유의 구독, 리소스 그룹 및 키 자격 증명 모음 이름으로 대체하여 매개 변수 파일을 업데이트합니다.

   ```json
   "parameters": { 
   ...
    "keyVaultResourceId": { 
     "value": "/subscriptions/########-####-####-####-############/resourceGroups/<rg-name>/providers/Microsoft.KeyVault/vaults/<kv-name>" 
    },   
    "volumeType": { 
     "value": "All" 
    }    
   } 
   ```

4. 변경 내용 배포 및 확인

   준비가 되면 변경 내용을 배포하여 관리 클러스터에서 디스크 암호화를 사용하도록 설정합니다.

   ```powershell
   $clusterName = "<clustername>" 

   New-AzResourceGroupDeployment -Name $resourceGroupName -ResourceGroupName $resourceGroupName .\azuredeploy.json -TemplateParameterFile .\azuredeploy.parameters.json -Debug -Verbose 
   ```

   `Get-AzVmssDiskEncryption` 명령을 사용하여 노드 유형의 기본 확장 집합에서 디스크 암호화 상태를 확인할 수 있습니다. 먼저 관리 클러스터의 지원 리소스 그룹 이름(기본 가상 네트워크, 부하 분산 장치, 공용 IP, NSG, 확장 집합, 스토리지 계정을 포함)을 확인해야 합니다. `VmssName`을 확인하려는 클러스터 노드 유형 이름(배포 템플릿에 지정된 이름)으로 수정해야 합니다.

   ```powershell
   $VmssName = "NT1"
   $clustername = <clustername>
   $supportResourceGroupName = "SFC_" + (Get-AzServiceFabricManagedCluster -ResourceGroupName $resourceGroupName -Name $clustername).ClusterId
   Get-AzVmssDiskEncryption -ResourceGroupName $supportResourceGroupName -VMScaleSetName $VmssName
   ```

   출력은 다음과 비슷합니다.

   ```console
   ResourceGroupName            : SFC_########-####-####-####-############
   VmScaleSetName               : NT1
   EncryptionEnabled            : True
   EncryptionExtensionInstalled : True
   ```

## <a name="next-steps"></a>다음 단계

[샘플: 표준 SKU Service Fabric 관리형 클러스터, 디스크 암호화를 사용하도록 설정된 하나의 노드 유형](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-1-NT-DiskEncryption)

[Windows VM용 Azure Disk Encryption](../virtual-machines/windows/disk-encryption-overview.md)

[Azure Resource Manager를 사용한 가상 머신 확장 집합 암호화](../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)