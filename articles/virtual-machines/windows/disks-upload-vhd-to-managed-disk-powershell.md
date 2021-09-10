---
title: VHD를 Azure에 업로드 또는 지역 간 디스크 복사 - Azure PowerShell
description: Azure 관리 디스크에 VHD를 업로드하고 직접 업로드를 통해 Azure PowerShell을 사용하여 여러 지역에 관리 디스크를 복사하는 방법에 대해 알아봅니다.
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: how-to
ms.service: storage
ms.tgt_pltfrm: linux
ms.subservice: disks
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2260603ce4b2baa969734d87d91aa4c949e875f2
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692491"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-powershell"></a>Azure에 VHD를 업로드하거나 관리 디스크를 다른 지역에 복사 - Azure PowerShell

**적용 대상:** :heavy_check_mark: Windows VM 

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 최신 [AzCopy v10 버전](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)을 다운로드합니다.
- [Azure PowerShell 모듈을 설치합니다](/powershell/azure/install-Az-ps).
- 온-프레미스에서 VHD를 업로드하려는 경우: [Azure를 위해 준비](prepare-for-upload-vhd-image.md)된 고정된 크기의 VHD가 로컬에 저장됩니다.
- 또는 복사 작업을 수행하려는 경우 Azure의 관리 디스크

## <a name="getting-started"></a>시작

GUI를 통해 디스크를 업로드하려는 경우 Azure Storage Explorer를 사용하여 업로드할 수 있습니다. 자세한 내용은 [Azure Storage Explorer를 사용하여 Azure 관리 디스크 관리](../disks-use-storage-explorer-managed-disks.md)를 참조하세요.

Azure에 VHD를 업로드하려면 이 업로드 프로세스를 위해 구성된 빈 관리 디스크를 만들어야 합니다. 빈 관리 디스크를 만들기 전에 디스크에 대해 알고 있어야 하는 몇 가지 추가 정보가 있습니다.

이러한 종류의 관리 디스크에는 두 가지 고유한 상태가 있습니다.

- ReadyToUpload는 디스크가 업로드를 받을 준비가 되었지만 SAS([보안 액세스 서명](../../storage/common/storage-sas-overview.md))가 생성되지 않았음을 의미합니다.
- ActiveUpload는 디스크가 업로드를 받을 준비가 되었으며 SAS가 생성되었음을 의미합니다.

> [!NOTE]
> 두 상태 중 하나일 때는 실제 디스크 유형과 관계없이 [표준 HDD 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)에 따라 관리 디스크 요금이 청구됩니다. 예를 들어 P10 요금은 S10으로 청구됩니다. 이 요금은 디스크를 VM에 연결하는 데 필요한 `revoke-access`가 관리 디스크에서 호출될 때까지 적용됩니다.

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

업로드할 빈 표준 HDD를 만들려면 먼저 업로드할 VHD의 파일 크기(바이트)가 필요합니다. 예제 코드는 사용자를 위한 것이지만 직접 수행하려면 `$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length`를 사용할 수 있습니다. 이 값은 **-UploadSizeInBytes** 매개 변수를 지정할 때 사용됩니다.

이제 로컬 셸에서 **-createoption** 매개 변수의 **업로드** 설정과 [New-AzDiskConfig](/powershell/module/az.compute/new-azdiskconfig) cmdlet의 **-UploadSizeInBytes** 매개 변수를 지정하여 업로드할 빈 표준 HDD를 만듭니다. 그런 다음 [New-AzDisk](/powershell/module/az.compute/new-azdisk)를 호출하여 디스크를 만듭니다.

`<yourdiskname>`, `<yourresourcegroupname>`, `<yourregion>`을 바꾸고 다음 명령을 실행합니다.

> [!TIP]
> OS 디스크를 만드는 경우 `New-AzDiskConfig`에 `-HyperVGeneration '<yourGeneration>'`을 추가합니다.

```powershell
$vhdSizeBytes = (Get-Item "<fullFilePathHere>").length

$diskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -OsType 'Windows' -UploadSizeInBytes $vhdSizeBytes -Location '<yourregion>' -CreateOption 'Upload'

New-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -Disk $diskconfig
```

프리미엄 SSD 또는 표준 SSD를 업로드하려면 **Standard_LRS** 를 **Premium_LRS** 또는 **StandardSSD_LRS** 로 바꿉니다. Ultra 디스크는 아직 지원되지 않습니다.

업로드 프로세스를 위해 구성된 빈 관리 디스크를 만들었으므로 이제 여기에 VHD를 업로드할 수 있습니다. 디스크에 VHD를 업로드하려면 업로드할 대상으로 참조할 수 있도록 쓰기 가능한 SAS가 있어야 합니다.

빈 관리 디스크의 쓰기 가능한 SAS를 생성하려면 `<yourdiskname>` 및 `<yourresourcegroupname>`을 바꾸고 다음 명령을 사용합니다.

```powershell
$diskSas = Grant-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>' -DurationInSecond 86400 -Access 'Write'

$disk = Get-AzDisk -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="upload-a-vhd"></a>VHD 업로드

이제 빈 관리 디스크의 SAS가 있으므로 SAS를 사용하여 해당 관리 디스크를 업로드 명령의 대상으로 설정할 수 있습니다.

AzCopy v10에서 생성한 SAS URI를 지정하여 관리 디스크에 로컬 VHD 파일을 업로드합니다.

해당 업로드는 동등한 [표준 HDD](../disks-types.md#standard-hdd)와 처리량이 동일합니다. 예를 들어 S4와 크기가 같은 경우 처리량은 최대 60MiB/초입니다. 그러나 S70과 크기가 같은 경우 처리량은 최대 500MiB/초입니다.

```
AzCopy.exe copy "c:\somewhere\mydisk.vhd" $diskSas.AccessSAS --blob-type PageBlob
```

업로드가 완료되고 디스크에 더 이상 데이터를 쓸 필요가 없으면 SAS를 철회합니다. SAS를 철회하면 관리 디스크의 상태가 변경되고 해당 디스크를 VM에 연결할 수 있습니다.

`<yourdiskname>` 및 `<yourresourcegroupname>`을 바꾸고 다음 명령을 실행합니다.

```powershell
Revoke-AzDiskAccess -ResourceGroupName '<yourresourcegroupname>' -DiskName '<yourdiskname>'
```

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

직접 업로드는 또한 관리 디스크를 복사하는 프로세스를 간소화합니다. 관리 디스크를 동일한 지역 내에서 복사하거나 다른 지역에 복사할 수 있습니다.

다음 스크립트는 해당 작업을 수행합니다. 이 프로세스는 기존 디스크로 작업하므로 이전에 설명한 단계와 유사하지만 몇 가지 차이점이 있습니다.

> [!IMPORTANT]
> Azure에서 관리 디스크의 디스크 크기(바이트)를 제공하는 경우 512의 오프셋을 추가해야 합니다. Azure에서 디스크 크기를 반환할 때는 바닥글이 생략되기 때문입니다. 이렇게 하지 않으면 복사에 실패합니다. 다음 스크립트는 사용자를 위해 해당 작업을 수행합니다.

`<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourOSTypeHere>`, `<yourTargetLocationHere>`(위치 값이 uswest2인 예)를 사용자의 값으로 바꾼 다음 관리 디스크를 복사하기 위한 다음 스크립트를 실행합니다.

> [!TIP]
> OS 디스크를 만드는 경우 `New-AzDiskConfig`에 `-HyperVGeneration '<yourGeneration>'`을 추가합니다.

```powershell

$sourceRG = <sourceResourceGroupHere>
$sourceDiskName = <sourceDiskNameHere>
$targetDiskName = <targetDiskNameHere>
$targetRG = <targetResourceGroupHere>
$targetLocate = <yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
$targetOS = <yourOSTypeHere>

$sourceDisk = Get-AzDisk -ResourceGroupName $sourceRG -DiskName $sourceDiskName

# Adding the sizeInBytes with the 512 offset, and the -Upload flag
$targetDiskconfig = New-AzDiskConfig -SkuName 'Standard_LRS' -osType $targetOS -UploadSizeInBytes $($sourceDisk.DiskSizeBytes+512) -Location $targetLocate -CreateOption 'Upload'

$targetDisk = New-AzDisk -ResourceGroupName $targetRG -DiskName $targetDiskName -Disk $targetDiskconfig

$sourceDiskSas = Grant-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName -DurationInSecond 86400 -Access 'Read'

$targetDiskSas = Grant-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName -DurationInSecond 86400 -Access 'Write'

azcopy copy $sourceDiskSas.AccessSAS $targetDiskSas.AccessSAS --blob-type PageBlob

Revoke-AzDiskAccess -ResourceGroupName $sourceRG -DiskName $sourceDiskName

Revoke-AzDiskAccess -ResourceGroupName $targetRG -DiskName $targetDiskName 
```

## <a name="next-steps"></a>다음 단계

이제 관리 디스크에 VHD를 성공적으로 업로드했으므로 VM에 디스크를 연결하고 사용하기 시작할 수 있습니다.

VM에 데이터 디스크를 연결하는 방법을 알아보려면 [PowerShell을 사용하여 Windows VM에 데이터 디스크 연결](attach-disk-ps.md)에 대한 문서를 참조하세요. 디스크를 OS 디스크로 사용하려면 [특수 디스크에서 Windows VM 만들기](create-vm-specialized.md#create-the-new-vm)를 참조하세요.
