---
title: 새 구독 또는 리소스 그룹으로 Azure VM 이동
description: Azure Resource Manager를 사용하여 Virtual Machines를 새 리소스 그룹 또는 구독으로 이동합니다.
ms.topic: conceptual
ms.date: 04/23/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 028536e541d8129490ccc07be0e6f67e51234a45
ms.sourcegitcommit: 3a063c59bb9396ce1d4b9a3565b194edf30393a2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132964019"
---
# <a name="move-guidance-for-virtual-machines"></a>Virtual Machines 이동 지침

이 문서에서는 현재 지원되지 않는 시나리오와 백업을 사용하여 Virtual Machines를 이동하는 단계를 설명합니다.

## <a name="scenarios-not-supported"></a>지원되지 않는 시나리오

다음 시나리오는 아직 지원되지 않습니다.

* 표준 SKU Load Balancer 또는 표준 SKU 공용 IP를 사용하는 Virtual Machine Scale Sets는 이동할 수 없습니다.
* 가상 네트워크의 모든 리소스를 이동하지 않는 경우 기존 가상 네트워크의 Virtual Machines는 새 구독으로 이동할 수 없습니다.
* 연결된 플랜을 사용하는 Marketplace 리소스에서 만든 Virtual Machines는 구독 간에 이동할 수 없습니다. 잠재적 해결 방법은 [Marketplace 플랜을 사용하는 Virtual Machines](#virtual-machines-with-marketplace-plans)를 참조하세요.
* 우선 순위가 낮은 Virtual Machines 및 우선 순위가 낮은 Virtual Machine Scale Sets는 리소스 그룹 또는 구독 간에 이동할 수 없습니다.
* 가용성 집합의 Virtual Machines는 개별적으로 이동할 수 없습니다.

## <a name="azure-disk-encryption"></a>Azure Disk Encryption

키 자격 증명 모음과 통합된 가상 머신을 이동하여 [Linux VM용 Azure Disk Encryption](../../../virtual-machines/linux/disk-encryption-overview.md) 또는 [Windows VM용 Azure Disk Encryption](../../../virtual-machines/windows/disk-encryption-overview.md)을 구현할 수 없습니다. VM을 이동하려면 암호화를 사용하지 않도록 설정해야 합니다.

```azurecli-interactive
az vm encryption disable --resource-group demoRG --name myVm1 --volume-type all
```

```azurepowershell-interactive
Disable-AzVMDiskEncryption -ResourceGroupName demoRG -VMName myVm1 -VolumeType all
```

## <a name="virtual-machines-with-marketplace-plans"></a>Marketplace 플랜을 사용하는 Virtual Machines

연결된 플랜을 사용하는 Marketplace 리소스에서 만든 Virtual Machines는 구독 간에 이동할 수 없습니다. 이 제한 사항을 해결하기 위해 현재 구독에서 가상 머신을 프로비전 해제하고 새 구독에서 다시 배포할 수 있습니다. 다음 단계를 통해 새 구독에서 가상 머신을 다시 만들 수 있습니다. 그러나 일부 시나리오에서는 작동하지 않을 수 있습니다. Marketplace에서 플랜을 더 이상 사용할 수 없는 경우 이러한 단계가 작동하지 않습니다.

1. 플랜에 대한 정보를 복사합니다.

1. OS 디스크를 대상 구독에 복제하거나 원본 구독에서 가상 머신을 삭제한 후 원래 디스크를 이동합니다.

1. 대상 구독에서 플랜에 대한 Marketplace 약관에 동의합니다. 다음 PowerShell 명령을 실행하여 약관에 동의할 수 있습니다.

   ```azurepowershell
   Get-AzMarketplaceTerms -Publisher {publisher} -Product {product/offer} -Name {name/SKU} | Set-AzMarketplaceTerms -Accept
   ```

   또는 포털을 통해 플랜을 사용하여 가상 머신의 새 인스턴스를 만들 수 있습니다. 새 구독의 약관에 동의한 후 가상 머신을 삭제할 수 있습니다.

1. 대상 구독에서 PowerShell, CLI 또는 Azure Resource Manager 템플릿을 사용하여 복제된 OS 디스크에서 가상 머신을 다시 만듭니다. 디스크에 연결된 Marketplace 플랜을 포함합니다. 플랜에 대한 정보는 새 구독에서 구매한 플랜과 일치해야 합니다.

## <a name="virtual-machines-with-azure-backup"></a>Azure Backup을 사용하는 Virtual Machines

Azure Backup으로 구성된 가상 머신을 이동하려면 자격 증명 모음에서 복원 지점 컬렉션(스냅샷)을 삭제해야 합니다. 자격 증명 모음에 이미 복사된 복원 지점을 유지하고 이동할 수 있습니다.

가상 머신에 대해 [일시 삭제](../../../backup/soft-delete-virtual-machines.md)가 사용하도록 설정된 경우 해당 복원 지점이 유지되는 동안에는 가상 머신을 이동할 수 없습니다. [일시 삭제를 사용하지 않도록 설정](../../../backup/backup-azure-security-feature-cloud.md#enabling-and-disabling-soft-delete)하거나 복원 지점이 삭제된 후 14일간 기다립니다.

### <a name="portal"></a>포털

1. 일시적으로 백업을 중지하고 백업 데이터를 보존합니다.
2. Azure Backup을 사용하여 구성된 Virtual Machines를 이동하려면 다음 단계를 수행합니다.

   1. 가상 머신의 위치를 찾습니다.
   2. 이름 지정 패턴 `AzureBackupRG_<VM location>_1`을 사용하는 리소스 그룹을 찾습니다. 예를 들어 이름은 *AzureBackupRG_westus2_1* 형식입니다.
   3. Azure Portal에서 **숨겨진 유형 표시** 를 선택합니다.
   4. 이름 지정 패턴이 `AzureBackup_<VM name>_###########`인 **Microsoft.Compute/restorePointCollections** 유형의 리소스를 찾습니다.
   5. 이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.
   6. 삭제 작업을 완료한 후 가상 머신을 이동할 수 있습니다.

3. VM을 대상 리소스 그룹으로 이동합니다.
4. 백업을 다시 구성합니다.

### <a name="powershell"></a>PowerShell

1. 가상 머신의 위치를 찾습니다.

1. 이름 지정 패턴 `AzureBackupRG_<VM location>_1`을 사용하는 리소스 그룹을 찾습니다. 예를 들어 이름은 `AzureBackupRG_westus2_1`이 될 수 있습니다.

1. 가상 머신을 하나만 이동하는 경우 해당 가상 머신에 대한 복원 지점 컬렉션을 가져옵니다.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -name AzureBackup_<VM name>* -ResourceType Microsoft.Compute/restorePointCollections
   ```

   이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

   ```azurepowershell-interactive
   Remove-AzResource -ResourceId $restorePointCollection.ResourceId -Force
   ```

1. 이 위치에서 백업을 포함하는 모든 Virtual Machines를 이동하는 경우 해당 Virtual Machines에 대한 복원 지점 컬렉션을 가져옵니다.

   ```azurepowershell-interactive
   $restorePointCollection = Get-AzResource -ResourceGroupName AzureBackupRG_<VM location>_1 -ResourceType Microsoft.Compute/restorePointCollections
   ```

   각 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

   ```azurepowershell-interactive
   foreach ($restorePoint in $restorePointCollection)
   {
     Remove-AzResource -ResourceId $restorePoint.ResourceId -Force
   }
   ```

### <a name="azure-cli"></a>Azure CLI

1. 가상 머신의 위치를 찾습니다.

1. 이름 지정 패턴 `AzureBackupRG_<VM location>_1`을 사용하는 리소스 그룹을 찾습니다. 예를 들어 이름은 `AzureBackupRG_westus2_1`이 될 수 있습니다.

1. 가상 머신을 하나만 이동하는 경우 해당 가상 머신에 대한 복원 지점 컬렉션을 가져옵니다.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections --query "[?starts_with(name, 'AzureBackup_<VM name>')].id" --output tsv)
   ```

   이 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

1. 이 위치에서 백업을 포함하는 모든 Virtual Machines를 이동하는 경우 해당 Virtual Machines에 대한 복원 지점 컬렉션을 가져옵니다.

   ```azurecli-interactive
   RESTOREPOINTCOL=$(az resource list -g AzureBackupRG_<VM location>_1 --resource-type Microsoft.Compute/restorePointCollections)
   ```

   각 리소스를 삭제합니다. 이 작업은 자격 증명 모음의 백업된 데이터가 아니라 인스턴트 복구 지점만 삭제합니다.

   ```azurecli-interactive
   az resource delete --ids $RESTOREPOINTCOL
   ```

## <a name="next-steps"></a>다음 단계

* 리소스를 이동하는 명령은 [새 리소스 그룹 또는 구독으로 리소스 이동](../move-resource-group-and-subscription.md)을 참조하세요.

* 백업용 Recovery Service 자격 증명 모음 이동에 대한 내용은 [Recovery Services 제한 사항](../../../backup/backup-azure-move-recovery-services-vault.md?toc=/azure/azure-resource-manager/toc.json)을 참조하세요.
