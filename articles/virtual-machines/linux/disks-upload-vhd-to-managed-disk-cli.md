---
title: Azure에 VHD 업로드 또는 지역 간 디스크 복사 - Azure CLI
description: Azure 관리 디스크에 VHD를 업로드하고 Azure CLI를 사용하여 직접 업로드를 통해 지역 간에 관리 디스크를 복사하는 방법을 알아봅니다.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 09/07/2021
ms.topic: how-to
ms.service: storage
ms.subservice: disks
ms.openlocfilehash: bd1c741bcd12e7708ba37fdcd1190a8c76a6c2f2
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130072312"
---
# <a name="upload-a-vhd-to-azure-or-copy-a-managed-disk-to-another-region---azure-cli"></a>Azure에 VHD 업로드 또는 다른 지역에 관리 디스크 복사 - Azure CLI

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 

[!INCLUDE [disks-upload-vhd-to-disk-intro](../../../includes/disks-upload-vhd-to-disk-intro.md)]

## <a name="prerequisites"></a>사전 요구 사항

- 최신 [AzCopy v10 버전](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy)을 다운로드합니다.
- [Azure CLI를 설치합니다](/cli/azure/install-azure-cli).
- 온-프레미스에서 VHD를 업로드하려는 경우: [Azure를 위해 준비](../windows/prepare-for-upload-vhd-image.md)되어 로컬에 저장된 고정 크기의 VHD
- 또는 복사 작업을 수행하려는 경우 Azure의 관리 디스크

## <a name="getting-started"></a>시작

GUI를 통해 디스크를 업로드하려는 경우 Azure Storage Explorer를 사용하여 업로드할 수 있습니다. 자세한 내용은 [Azure Storage Explorer를 사용하여 Azure 관리 디스크 관리](../disks-use-storage-explorer-managed-disks.md)를 참조하세요.

Azure에 VHD를 업로드하려면 이 업로드 프로세스를 위해 구성된 빈 관리 디스크를 만들어야 합니다. 빈 관리 디스크를 만들기 전에 디스크에 대해 알고 있어야 하는 몇 가지 추가 정보가 있습니다.

이 종류의 관리 디스크에는 다음 두 가지 고유 상태가 있습니다.

- ReadyToUpload - 디스크가 업로드를 받을 준비가 되었지만 SAS([보안 액세스 서명](../../storage/common/storage-sas-overview.md))가 생성되지 않았습니다.
- ActiveUpload - 디스크가 업로드를 받을 준비가 되었으며 SAS가 생성되었습니다.

> [!NOTE]
> 두 상태 중 하나일 때는 실제 디스크 유형과 관계없이 [표준 HDD 가격 책정](https://azure.microsoft.com/pricing/details/managed-disks/)에 따라 관리 디스크 요금이 청구됩니다. 예를 들어 P10 요금은 S10으로 청구됩니다. 이 요금은 디스크를 VM에 연결하는 데 필요한 `revoke-access`가 관리 디스크에서 호출될 때까지 적용됩니다.

## <a name="create-an-empty-managed-disk"></a>빈 관리 디스크 만들기

업로드할 빈 표준 HDD를 만들려면 먼저 업로드할 VHD의 파일 크기(바이트)를 알아야 합니다. 파일 크기를 확인하려면 `wc -c <yourFileName>.vhd` 또는 `ls -al <yourFileName>.vhd`를 사용할 수 있습니다. 이 값은 **--upload-size-bytes** 매개 변수를 지정할 때 사용됩니다.

[disk create](/cli/azure/disk#az_disk_create) cmdlet에서 **-–for-upload** 매개 변수와 **--upload-size-bytes** 매개 변수를 둘 다 지정하여 업로드할 빈 표준 HDD를 만듭니다.

`<yourdiskname>`, `<yourresourcegroupname>`, `<yourregion>`을 선택한 값으로 바꿉니다. `--upload-size-bytes` 매개 변수에는 `34359738880`의 예제 값이 포함되어 있으므로 해당 값을 적절한 값으로 바꿉니다.

> [!TIP]
> OS 디스크를 만드는 경우 `az disk create`에 `--hyper-v-generation <yourGeneration>`을 추가합니다.

```azurecli
az disk create -n <yourdiskname> -g <yourresourcegroupname> -l <yourregion> --os-type Linux --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

프리미엄 SSD 또는 표준 SSD를 업로드하려면 **standard_lrs** 를 **premium_LRS** 또는 **standardssd_lrs** 로 바꿉니다. Ultra 디스크는 현재 지원되지 않습니다.

업로드 프로세스를 위해 구성된 빈 관리 디스크를 만들었으므로 이제 디스크에 VHD를 업로드할 수 있습니다. 디스크에 VHD를 업로드하려면 업로드할 대상으로 참조할 수 있도록 쓰기 가능한 SAS가 필요합니다.

빈 관리 디스크의 쓰기 가능한 SAS를 생성하려면 `<yourdiskname>` 및 `<yourresourcegroupname>`을 바꾼 후 다음 명령을 사용합니다.

```azurecli
az disk grant-access -n <yourdiskname> -g <yourresourcegroupname> --access-level Write --duration-in-seconds 86400
```

샘플 반환 값:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-a-vhd"></a>VHD 업로드

이제 빈 관리 디스크의 SAS가 있으므로 SAS를 사용하여 해당 관리 디스크를 업로드 명령의 대상으로 설정할 수 있습니다.

AzCopy v10에서 생성한 SAS URI를 지정하여 관리 디스크에 로컬 VHD 파일을 업로드합니다.

해당 업로드는 동등한 [표준 HDD](../disks-types.md#standard-hdds)와 처리량이 동일합니다. 예를 들어 S4와 크기가 같은 경우 처리량은 최대 60MiB/초입니다. 그러나 S70과 크기가 같은 경우 처리량은 최대 500MiB/초입니다.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

업로드가 완료되고 디스크에 더 이상 데이터를 쓸 필요가 없으면 SAS를 철회합니다. SAS를 철회하면 관리 디스크의 상태가 변경되고 해당 디스크를 VM에 연결할 수 있습니다.

`<yourdiskname>` 및 `<yourresourcegroupname>`을 바꾼 후 다음 명령을 사용하여 디스크를 사용할 수 있도록 합니다.

```azurecli
az disk revoke-access -n <yourdiskname> -g <yourresourcegroupname>
```

## <a name="copy-a-managed-disk"></a>관리 디스크 복사

또한 직접 업로드는 관리 디스크를 복사하는 프로세스를 간소화합니다. 동일한 지역 내에서 복사하거나 지역 간(다른 지역)에 복사할 수 있습니다.

다음 스크립트는 해당 작업을 자동으로 수행합니다. 프로세스는 앞에서 설명한 단계와 유사하지만 기존 디스크로 작업하므로 몇 가지 차이점이 있습니다.

> [!IMPORTANT]
> Azure에서 관리 디스크의 디스크 크기(바이트)를 지정하는 경우 오프셋 512를 더해야 합니다. Azure에서 디스크 크기를 반환할 때는 바닥글이 생략되기 때문입니다. 이렇게 하지 않으면 복사가 실패합니다. 다음 스크립트는 해당 작업을 자동으로 수행합니다.

`<sourceResourceGroupHere>`, `<sourceDiskNameHere>`, `<targetDiskNameHere>`, `<targetResourceGroupHere>`, `<yourTargetLocationHere>`(위치 값의 예: uswest2)를 해당 값으로 바꾼 후 관리 디스크를 복사하기 위해 다음 스크립트를 실행합니다.

> [!TIP]
> OS 디스크를 만드는 경우 `az disk create`에 `--hyper-v-generation <yourGeneration>`을 추가합니다.

```azurecli
sourceDiskName=<sourceDiskNameHere>
sourceRG=<sourceResourceGroupHere>
targetDiskName=<targetDiskNameHere>
targetRG=<targetResourceGroupHere>
targetLocation=<yourTargetLocationHere>
#Expected value for OS is either "Windows" or "Linux"
targetOS=<yourOSTypeHere>

sourceDiskSizeBytes=$(az disk show -g $sourceRG -n $sourceDiskName --query '[diskSizeBytes]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocation --os-type $targetOS --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI=$(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>다음 단계

이제 관리 디스크에 VHD를 업로드했으므로 디스크를 [기존 VM에 데이터 디스크](add-disk.md)로 연결하거나 [디스크를 VM에 OS 디스크로 연결](upload-vhd.md#create-the-vm)하여 새 VM을 만들 수 있습니다.
