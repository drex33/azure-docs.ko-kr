---
title: 사용 후 삭제 OS 디스크
description: Azure VM을 위한 임시 OS 디스크에 대해 자세히 알아봅니다.
author: Aarthi-Vijayaraghavan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/23/2020
ms.author: aarthiv
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6ab8c0d7d1e547d564ddc3329858ddc49d51185b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448978"
---
# <a name="ephemeral-os-disks-for-azure-vms"></a>Azure VM을 위한 임시 OS 디스크

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합

임시 OS 디스크는 로컬 가상 머신(VM) 스토리지에 생성되고 원격 Azure Storage에 저장되지 않습니다. 사용 후 삭제 OS 디스크는 애플리케이션이 개별 VM 오류를 용인하지만 VM 배포 시간 또는 개별 VM 인스턴스 이미지 다시 설치의 영향을 받는 상태 비저장 워크로드에 적합합니다. 임시 OS 디스크를 사용하면 OS 디스크에 대한 읽기/쓰기 대기 시간이 단축되고 VM 이미지로 다시 설치가 더 빨라집니다. 
 
임시 디스크의 주요 기능은 다음과 같습니다. 
- 상태 비저장 애플리케이션에 이상적입니다.
- Marketplace, 사용자 지정 이미지 및 [Azure Compute 갤러리(이전의](./shared-image-galleries.md) Shared Image Gallery)에서 지원됩니다.
- VM 및 확장 집합 인스턴스를 원래 부트 상태로 빠르게 초기화하거나 이미지로 다시 설치할 수 있습니다.  
- 임시 디스크와 비슷하게 대기 시간이 낮습니다. 
- 사용 후 삭제 OS 디스크는 무료이며 OS 디스크에 대한 스토리지 비용이 발생하지 않습니다.
- 모든 Azure 지역에서 사용 가능.  

 
영구 및 임시 OS 디스크 간 주요 차이점:

|                             | 임시 OS 디스크                          | 사용 후 삭제 OS 디스크                              |
|-----------------------------|---------------------------------------------|------------------------------------------------|
| **OS 디스크의 크기 제한**      | 2TiB                                                                                        | VM 크기 또는 2 TiB 중 더 작은 캐시 크기입니다. **GiB 단위의 캐시 크기** 에 대해서는 [DS](sizes-general.md), [ES](sizes-memory.md), [M](sizes-memory.md), [FS](sizes-compute.md), [GS](sizes-previous-gen.md#gs-series)를 참조하세요.              |
| **지원되는 VM 크기**          | 모두                                                                                          | DSv1, DSv2, DSv3, Esv3, Fs, FsV2, GS, M, Mdsv2,Bs, Dav4, Eav4와 같은 Premium 스토리지를 지원하는 VM 크기                                               |
| **데이터 형식 지원**           | 관리 및 비관리 OS 디스크                                                                | 관리 OS 디스크만                                                               |
| **지역 지원**              | 모든 지역                                                                                  | 모든 지역                              |
| **데이터 지속성**            | OS 디스크에 기록된 OS 디스크 데이터는 Azure Storage에 저장됩니다.                                  | OS 디스크에 기록된 데이터는 로컬 VM 스토리지에 저장되며 Azure Storage 유지되지 않습니다. |
| **중지-할당 취소됨 상태**      | VM 및 확장 집합 인스턴스는 중지-할당 취소됨 상태일 수 있고, 중지-할당 취소됨 상태에서 다시 시작될 수 있습니다. | VM 및 확장 집합 인스턴스는 중지-할당 취소됨 상태일 수 없습니다.                                  |
| **특수 OS 디스크 지원** | 예                                                                                          | 아니요                                                                                 |
| **OS 디스크 크기 조정**              | VM 만들기 중에 그리고 VM이 중지-할당 취소됨 상태인 후에 지원됩니다.                                | VM 만들기 중에만 지원됩니다.                                                  |
| **새 VM 크기로 크기 조정**   | OS 디스크 데이터가 유지됩니다.                                                                    | OS 디스크의 데이터가 삭제되고 OS가 다시 프로비전됩니다.       
| **페이지 파일 배치**   | Windows의 경우 페이지 파일이 리소스 디스크에 저장됩니다.                                              | Windows 경우 페이지 파일은 OS 디스크에 저장됩니다(OS 캐시 배치 및 임시 디스크 배치 모두에 대해).   |

## <a name="size-requirements"></a>크기 요구 사항

VM 캐시 또는 VM 임시 디스크에 임시 OS 디스크를 배포하도록 선택할 수 있습니다.
이미지 OS 디스크의 크기는 선택한 VM 크기의 임시/캐시 크기보다 크거나 같아야 합니다.

예를 들어 **OS 캐시 배치를** 선택하려는 경우: 마켓플레이스의 표준 Windows Server 이미지는 약 127 GiB입니다. 즉, 캐시가 127 GiB보다 크거나 같은 VM 크기가 필요합니다. Standard_DS3_v2 캐시 크기는 127 GiB이며, 이 크기는 충분히 큽다. 여기에서는 Standard_DS3_v2가 이 이미지에 사용할 수 있는 DSv2 시리즈에서 가장 작은 크기입니다. 

**임시 디스크 배치를** 선택하려는 경우: 마켓플레이스의 표준 Ubuntu 서버 이미지는 약 30 GiB입니다. 임시 OS 디스크를 임시로 사용하도록 설정하려면 임시 디스크 크기가 30 GiB보다 크거나 같아야 합니다. Standard_B4ms 임시 크기는 32 GiB이며 30 GiB OS 디스크에 맞을 수 있습니다. VM을 만들 때 임시 디스크 공간은 2 GiB입니다. 
> [!Important] 
> 임시 디스크 배치를 선택하는 경우 최종 임시 디스크 크기 = (초기 임시 디스크 크기 - OS 이미지 크기).

로 표시된 Marketplace의 기본 Linux 및 Windows Server 이미지는 `[smallsize]` 약 30 GiB이며 사용 가능한 VM 크기의 대부분을 사용할 수 있습니다.
또한 임시 디스크를 사용하려면 VM 크기가 **스토리지 Premium** 지원해야 합니다. 이 크기는 일반적으로(항상 그렇지는 않음) 이름에 DSv2 및 EsV3와 같은 `s`가 포함됩니다. Premium Storage를 지원하는 크기에 대한 자세한 내용은 [Azure VM 크기](sizes.md)를 참조하세요.

## <a name="ephemeral-os-disks-can-now-be-stored-on-tempresource-disks"></a>이제 임시 OS 디스크를 임시/리소스 디스크에 저장할 수 있습니다.
이제 임시 OS 디스크를 VM 캐시 디스크 또는 VM 임시/리소스 디스크에 저장할 수 있습니다. 이 기능을 사용하면 캐시가 없거나 캐시가 부족하지만(예: Dav3, Dav4, Eav4 및 Eav3) 임시 OS 디스크를 호스트하기에 충분한 임시 OS 디스크가 있는 모든 VM에 대해 임시 OS 디스크를 만들 수 있습니다.
[DiffDiskPlacement는](/rest/api/compute/virtualmachines/list#diffdiskplacement) 임시 OS 디스크를 배치할 위치를 지정하는 데 사용할 수 있는 새 속성입니다. 이 기능을 사용하면 Windows VM을 프로비전할 때 OS 디스크에 배치할 페이지 파일을 구성합니다.

## <a name="portal"></a>포털

Azure Portal 디스크 탭의 **고급** 섹션을 열어 가상 머신 또는 가상 머신 확장 집합을 배포할 때 임시 **디스크를** 사용하도록 선택할 수 있습니다. 사용 후 삭제 OS 디스크의 배치를 선택하려면 **OS 캐시 배치** 또는 임시 디스크 **배치** 를 선택합니다.

![임시 OS 디스크를 사용하도록 선택하는 라디오 단추를 보여주는 스크린샷](./media/virtual-machines-common-ephemeral/ephemeral-portal-temp.png)
 

임시 디스크 또는 OS 캐시 배치 또는 임시 디스크 배치를 사용하는 옵션이 회색으로 표시되면 OS 이미지보다 큰 캐시/임시 크기가 없거나 Premium 스토리지를 지원하지 않는 VM 크기를 선택했을 수 있습니다. **기본** 페이지로 돌아가서 다른 VM 크기를 선택해보세요.

## <a name="scale-set-template-deployment"></a>확장 집합 템플릿 배포  
임시 OS 디스크를 사용하는 확장 집합을 만들기 위해서는 템플릿의 `Microsoft.Compute/virtualMachineScaleSets/virtualMachineProfile` 리소스 유형에 `diffDiskSettings` 속성을 추가합니다. 또한 임시 OS 디스크에 대해 캐싱 정책을 `ReadOnly`로 설정해야 합니다. OS 캐시 디스크 배치를 위해 배치를 로 변경할 수 `CacheDisk` 있습니다.

```json
{ 
  "type": "Microsoft.Compute/virtualMachineScaleSets", 
  "name": "myScaleSet", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "sku": { 
    "name": "Standard_DS2_v2", 
    "capacity": "2" 
  }, 
  "properties": { 
    "upgradePolicy": { 
      "mode": "Automatic" 
    }, 
    "virtualMachineProfile": { 
       "storageProfile": { 
        "osDisk": { 
          "diffDiskSettings": { 
            "option": "Local" ,
            "placement": "ResourceDisk"
          }, 
          "caching": "ReadOnly", 
          "createOption": "FromImage" 
        }, 
        "imageReference":  { 
          "publisher": "Canonical", 
          "offer": "UbuntuServer", 
          "sku": "16.04-LTS", 
          "version": "latest" 
        } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvmss", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
  } 
}  
```

## <a name="vm-template-deployment"></a>VM 템플릿 배포 
템플릿을 사용하여 임시 OS 디스크로 VM을 배포할 수 있습니다. 임시 OS 디스크를 사용하는 VM을 만드는 프로세스는 `diffDiskSettings` 템플릿의 Microsoft.Compute/virtualMachines 리소스 종류에 속성을 추가하는 것입니다. 또한 임시 OS 디스크에 대해 캐싱 정책을 `ReadOnly`로 설정해야 합니다. OS 캐시 디스크 배치에 대한 배치 옵션을 로 변경할 수 `CacheDisk` 있습니다.

```json
{ 
  "type": "Microsoft.Compute/virtualMachines", 
  "name": "myVirtualMachine", 
  "location": "East US 2", 
  "apiVersion": "2019-12-01", 
  "properties": { 
       "storageProfile": { 
            "osDisk": { 
              "diffDiskSettings": { 
                "option": "Local" ,
                "placement": "ResourceDisk"
              }, 
              "caching": "ReadOnly", 
              "createOption": "FromImage" 
            }, 
            "imageReference": { 
                "publisher": "MicrosoftWindowsServer", 
                "offer": "WindowsServer", 
                "sku": "2016-Datacenter-smalldisk", 
                "version": "latest" 
            }, 
            "hardwareProfile": { 
                 "vmSize": "Standard_DS2_v2" 
             } 
      }, 
      "osProfile": { 
        "computerNamePrefix": "myvirtualmachine", 
        "adminUsername": "azureuser", 
        "adminPassword": "P@ssw0rd!" 
      } 
    } 
 } 
```

> [!NOTE] 
> 임시 OS 디스크 배치 옵션(VM 캐시 디스크 또는 VM 임시/리소스 디스크)은 PowerShell 및 CLI에서 곧 제공됩니다.

## <a name="powershell"></a>PowerShell
PowerShell VM 배포에 임시 디스크를 사용하려면 VM 구성에서 [Set-AzVMOSDisk](/powershell/module/az.compute/set-azvmosdisk)를 사용합니다. `-DiffDiskSetting`을 `Local`로 설정하고 `-Caching`을 `ReadOnly`로 설정합니다.     
```powershell
Set-AzVMOSDisk -DiffDiskSetting Local -Caching ReadOnly
```
확장 집합 배포의 경우 해당 구성에서 [Set-AzVmssStorageProfile](/powershell/module/az.compute/set-azvmssstorageprofile) cmdlet을 사용합니다. `-DiffDiskSetting`을 `Local`로 설정하고 `-Caching`을 `ReadOnly`로 설정합니다.

```powershell
Set-AzVmssStorageProfile -DiffDiskSetting Local -OsDiskCaching ReadOnly
```

## <a name="cli"></a>CLI

CLI VM 배포에 임시 디스크를 사용하려면 [az vm create](/cli/azure/vm#az_vm_create)의 `--ephemeral-os-disk` 매개 변수를 `true`로 설정하고 `--os-disk-caching` 매개 변수를 `ReadOnly`로 설정합니다.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --ephemeral-os-disk true \
  --os-disk-caching ReadOnly \
  --admin-username azureuser \
  --generate-ssh-keys
```

확장 집합의 경우 [az-vmss-create](/cli/azure/vmss#az_vmss_create)에 대해 동일한 `--ephemeral-os-disk true` 매개 변수를 사용하고 `--os-disk-caching` 매개 변수를 `ReadOnly`로 설정합니다.

## <a name="reimage-a-vm-using-rest"></a>REST를 사용하여 VM을 이미지로 다시 설치
아래에 설명된 대로 REST API 사용하고 VM의 개요 창으로 이동하면 Azure Portal 통해 임시 OS 디스크가 있는 Virtual Machine 인스턴스를 이미지로 다시 만들 수 있습니다. 확장 집합의 경우 이미 PowerShell, CLI 및 포털을 통해 이미지로 다시 탑재할 수 있습니다.

```
POST https://management.azure.com/subscriptions/{sub-
id}/resourceGroups/{rgName}/providers/Microsoft.Compute/VirtualMachines/{vmName}/reimage?a pi-version=2019-12-01" 
```
 
## <a name="frequently-asked-questions"></a>질문과 대답

**Q: 로컬 OS 디스크의 크기는 얼마나 되나요?**

A: 플랫폼, Shared Image Gallery 및 사용자 지정 이미지를 지원하며, OS 캐시 배치가 있는 VM 캐시 크기 및 임시 디스크 배치를 통한 임시 디스크 크기까지 지원합니다. 여기서 OS 디스크에 대한 모든 읽기/쓰기는 Virtual Machine과 동일한 노드에서 로컬로 설정됩니다. 

**Q: 임시 OS 디스크 크기를 조정할 수 있나요?**

A: 아니요, 임시 OS 디스크가 프로비전된 다음에는 OS 디스크의 크기를 조정할 수 없습니다. 

**Q: VM을 만든 후 임시 OS 디스크 배치를 수정할 수 있나요?**

A: 아니요, 사용 후 삭제 OS 디스크가 프로비전되면 OS 디스크 배치를 변경할 수 없습니다. 그러나 선택한 OS 디스크 배치를 업데이트하여 ARM 템플릿 배포/PowerShell/CLI를 통해 VM을 다시 만들 수 있습니다. 이로 인해 OS 디스크의 데이터가 삭제되고 OS가 다시 프로비전되는 VM이 다시 생성됩니다.

**Q: 이미지 크기가 선택한 VM 크기의 임시 디스크 크기와 같으면 임시 디스크가 만들어지나요?**

A: 아니요, 이 경우 임시 디스크 드라이브가 만들어지지 않습니다.

**Q: 사용 후 삭제 OS 디스크는 우선 순위가 낮은 VM 및 스폿 VM에서 지원하나요?**

A: 예. 임시 VM에 대한 Stop-Deallocate 옵션은 없습니다. 대신 사용자가 할당을 삭제하는 대신 삭제해야 합니다.

**Q: 관리 디스크를 임시 VM에 연결할 수 있나요?**

A: 예, 임시 OS 디스크를 사용하는 VM에 관리 데이터 디스크를 연결할 수 있습니다. 

**Q: 임시 OS 디스크에 대해 모든 VM 크기가 지원되나요?**

A: 아니요, 대부분의 Premium Storage VM 크기가 지원됩니다(DS, ES, FS, GS, M 등). 특정 VM 크기에서 임시 OS 디스크가 지원되는지 확인하려면 다음을 수행하세요.

`Get-AzComputeResourceSku`PowerShell cmdlet 호출
```azurepowershell-interactive
 
$vmSizes=Get-AzComputeResourceSku | where{$_.ResourceType -eq 'virtualMachines' -and $_.Locations.Contains('CentralUSEUAP')} 

foreach($vmSize in $vmSizes)
{
   foreach($capability in $vmSize.capabilities)
   {
       if($capability.Name -eq 'EphemeralOSDiskSupported' -and $capability.Value -eq 'true')
       {
           $vmSize
       }
   }
}
```
 
**Q: 임시 OS 디스크가 기존 VM 및 확장 집합에 적용될 수 있나요?**

A: 아니요, 임시 OS 디스크는 VM 및 확장 집합 만들기 중에만 사용될 수 있습니다. 

**Q: 임시 및 일반 OS 디스크를 확장 집합에 혼합할 수 있나요?**

A: 아니요, 동일한 확장 집합 내에서는 임시 및 영구 OS 디스크 인스턴스를 혼합할 수 없습니다. 

**Q: Powershell 또는 CLI를 사용하여 임시 OS 디스크를 만들 수 있나요?**

A: 예, REST, 템플릿, PowerShell 및 CLI를 사용하여 임시 OS 디스크가 있는 VM을 만들 수 있습니다.

**Q: 임시 OS 디스크에서 지원되지 않는 기능은 무엇인가요?**

A: 임시 디스크는 다음을 지원하지 않습니다.
- VM 이미지 캡처
- 디스크 스냅샷 
- Azure 디스크 암호화 
- Azure Backup
- Azure Site Recovery  
- OS 디스크 교체 

> [!NOTE]
> 
> 임시 디스크는 포털을 통해 액세스할 수 없습니다. 예상되는 사용 후 삭제 디스크에 액세스할 때 "리소스를 찾을 수 없습니다." 또는 "404" 오류가 표시됩니다.
> 
 
## <a name="next-steps"></a>다음 단계
[Azure CLI](/cli/azure/vm#az_vm_create)를 사용하여 임시 OS 디스크가 있는 VM을 만들 수 있습니다.