---
title: NFS 공유 만들기(미리 보기) - Azure Files
description: 네트워크 파일 시스템 프로토콜을 사용하여 탑재할 수 있는 Azure 파일 공유를 만드는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/25/2021
ms.author: rogarana
ms.subservice: files
ms.custom: references_regions, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: f146d51cdd43b8c4a52285476e47d0c6237efe0f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131046606"
---
# <a name="how-to-create-an-nfs-share-preview"></a>NFS 공유를 만드는 방법(미리 보기)
Azure 파일 공유는 클라우드에 있는 완전 관리형 파일 공유입니다. 이 문서에서는 NFS 프로토콜(미리 보기)을 사용하는 파일 공유를 만드는 방법을 설명합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![아니요](../media/icons/no-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![아니요](../media/icons/no-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="limitations"></a>제한 사항
[!INCLUDE [files-nfs-limitations](../../../includes/files-nfs-limitations.md)]


### <a name="regional-availability"></a>국가별 가용성
[!INCLUDE [files-nfs-regional-availability](../../../includes/files-nfs-regional-availability.md)]

## <a name="prerequisites"></a>필수 구성 요소
- NFS 공유는 숫자 UID/GID만 허용합니다. 클라이언트에서 영숫자 UID/GID를 전송하지 않도록 하려면 ID 매핑을 사용하지 않도록 설정합니다.
- NFS 공유는 신뢰할 수 있는 네트워크에서만 액세스할 수 있습니다. NFS 공유에 대한 연결은 다음 원본 중 하나에서 시작해야 합니다.
    - [프라이빗 엔드포인트 만들기](storage-files-networking-endpoints.md#create-a-private-endpoint)(권장) 또는 [퍼블릭 엔드포인트에 대한 액세스 제한](storage-files-networking-endpoints.md#restrict-public-endpoint-access).
    - [Azure Files에서 사용할 P2S(지점 및 사이트 간) VPN을 Linux에 구성](storage-files-configure-p2s-vpn-linux.md).
    - [Azure Files에서 사용할 사이트 간 VPN 구성](storage-files-configure-s2s-vpn.md).
    - [ExpressRoute](../../expressroute/expressroute-introduction.md) 구성.

- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.

## <a name="register-the-nfs-41-protocol"></a>NFS 4.1 프로토콜 등록

NFS Azure 파일 공유를 만들려면 먼저 기능에 등록해야 합니다. 등록하기 전에 만든 스토리지 계정에는 NFS 공유를 만들 수 없습니다.

Azure PowerShell 모듈 또는 Azure CLI를 사용 중인 경우, 다음 명령을 사용하여 기능을 등록합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure PowerShell 또는 Azure CLI를 사용하여 Azure Files에 대해 NFS 4.1 기능을 등록합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
# Connect your PowerShell session to your Azure account, if you have not already done so.
Connect-AzAccount
# Set the actively selected subscription, if you have not already done so.
$subscriptionId = "<yourSubscriptionIDHere>"
$context = Get-AzSubscription -SubscriptionId $subscriptionId
Set-AzContext $context
# Register the NFS 4.1 feature with Azure Files to enable the preview.
Register-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares 
    
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
# Connect your Azure CLI to your Azure account, if you have not already done so.
az login
# Provide the subscription ID for the subscription where you would like to 
# register the feature
subscriptionId="<yourSubscriptionIDHere>"
az feature register \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
az provider register \
    --namespace Microsoft.Storage
```

---

등록이 승인되기까지 최대 1시간 정도 걸릴 수 있습니다. 등록이 완료되었는지 확인하려면 다음 명령을 사용합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure PowerShell 또는 Azure CLI를 사용하여 Azure Files에 대해 NFS 4.1 기능이 등록되었는지 확인합니다. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```azurepowershell
Get-AzProviderFeature `
    -ProviderNamespace Microsoft.Storage `
    -FeatureName AllowNfsFileShares
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az feature show \
    --name AllowNfsFileShares \
    --namespace Microsoft.Storage \
    --subscription $subscriptionId
```
---

## <a name="create-a-filestorage-storage-account"></a>FileStorage 스토리지 계정 만들기
현재 NFS 4.1 공유는 프리미엄 파일 공유로만 사용할 수 있습니다. NFS 4.1 프로토콜 지원을 사용하여 프리미엄 파일 공유를 배포하려면 먼저 FileStorage 스토리지 계정을 만들어야 합니다. 스토리지 계정은 Azure의 최상위 수준 개체로, 여러 Azure 파일 공유를 배포하는 데 사용할 수 있는 공유 스토리지 풀을 나타냅니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
FileStorage 스토리지 계정을 만들려면 Azure Portal로 이동합니다.

1. [Azure Portal](https://portal.azure.com/)의 왼쪽 메뉴에서 **Storage 계정** 을 선택 합니다.

    ![Azure Portal 기본 페이지에서 스토리지 계정을 선택합니다.](media/storage-how-to-create-premium-fileshare/azure-portal-storage-accounts.png)

1. 나타나는 **Storage 계정** 창에서 **추가** 를 선택합니다.
1. 스토리지 계정을 만들 구독을 선택합니다.
1. 스토리지 계정을 만들 리소스 그룹을 선택합니다.
1. 그런 다음, 스토리지 계정의 이름을 입력합니다. 선택하는 이름이 Azure에서 고유해야 합니다. 또한 이름의 길이가 3~24자여야 하고, 숫자 및 소문자만 포함할 수 있습니다.
1. 스토리지 계정의 위치를 선택하거나 기본 위치를 사용합니다.
1. **성능** 으로 **프리미엄** 을 선택합니다.

    **계정 종류** 드롭다운에서 **Fileshares** 가 옵션으로 표시되도록 하려면 **프리미엄** 을 선택해야 합니다.

1. **프리미엄 계정 유형** 으로 **Fileshares** 를 선택합니다.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="프리미엄 성능이 선택된 스크린샷.":::

1. **복제** 세트를 기본값인 **LRS(로컬 중복 스토리지)** 로 둡니다.
1. **검토 + 만들기** 를 선택하여 스토리지 계정 설정을 검토하고 계정을 만듭니다.
1. **만들기** 를 선택합니다.

스토리지 계정 리소스가 생성되면 해당 리소스로 이동합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
FileStorage 스토리지 계정을 만들려면 PowerShell 프롬프트를 열고 다음 명령을 실행합니다. 이때 `<resource-group>`과 `<storage-account>`를 환경에 맞는 값으로 바꾸어야 합니다.

```powershell
$resourceGroupName = "<resource-group>"
$storageAccountName = "<storage-account>"
$location = "westus2"

$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $location `
    -Kind FileStorage
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
FileStorage 스토리지 계정을 만들려면 터미널을 열고 다음 명령을 실행합니다. 이때 `<resource-group>`과 `<storage-account>`를 환경에 맞는 값으로 바꾸어야 합니다.

```azurecli-interactive
resourceGroup="<resource-group>"
storageAccount="<storage-account>"
location="westus2"

az storage account create \
    --resource-group $resourceGroup \
    --name $storageAccount \
    --location $location \
    --sku Premium_LRS \
    --kind FileStorage
```
---

## <a name="disable-secure-transfer"></a>보안 전송을 사용하지 않도록 설정

보안 전송을 사용 하지 않도록 설정 하지 않으면 NFS 파일 공유를 탑재할 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

1. 만든 저장소 계정으로 이동 합니다.
1. **Configuration(구성)** 을 선택합니다.
1. **보안 전송 필요** 로 **사용 안 함** 을 선택합니다.
1. **저장** 을 선택합니다.

    :::image type="content" source="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png" alt-text="보안 전송이 사용하지 않도록 설정된 스토리지 계정 구성 화면 스크린샷." lightbox="media/storage-files-how-to-mount-nfs-shares/disable-secure-transfer.png":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $False
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only false
```
---

## <a name="create-an-nfs-share"></a>NFS 공유 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

FileStorage 계정을 만들고 네트워킹을 구성했으니 이제 NFS 파일 공유를 만들 수 있습니다. NFS 파일 공유를 만드는 과정은 SMB 공유를 만드는 과정과 비슷하나, 공유를 만들 때 **SMB** 대신 **NFS** 를 선택해야 합니다.

1. 스토리지 계정으로 이동하여 **파일 공유** 를 선택합니다.
1. **+ 파일 공유** 를 선택하여 새 파일 공유를 만듭니다.
1. 파일 공유의 이름을 지정하고 프로비저닝된 용량을 선택합니다.
1. **프로토콜** 에 대해 **NFS** 를 선택합니다.
1. **루트 Squash** 에 대해 원하는 옵션을 선택합니다.

    - 루트 Squash(기본값) - 원격 슈퍼 사용자(루트)를 위한 액세스가 UID(65534) 및 GID(65534)에 매핑됩니다.
    - 루트 Squash 없음 - 원격 슈퍼 사용자(root)에게 루트로서의 액세스 권한이 부여됩니다.
    - 모든 Squash - 모든 사용자 액세스가 UID(65534) 및 GID(65534)에 매핑됩니다.
    
1. **만들기** 를 선택합니다.

    :::image type="content" source="media/storage-files-how-to-create-mount-nfs-shares/files-nfs-create-share.png" alt-text="파일 공유 만들기 블레이드의 스크린샷.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Azure PowerShell 모듈을 사용하여 프리미엄 파일 공유를 만들려면 [New-AzRmStorageShare](/powershell/module/az.storage/new-azrmstorageshare) cmdlet을 사용합니다.

    > [!NOTE]
    > 프리미엄 파일 공유는 프로비저닝된 모델을 사용하여 요금이 청구됩니다. 공유의 프로비저닝된 크기는 아래의 `QuotaGiB`로 지정됩니다. 자세한 내용은 [프로비저닝된 모델 이해](understanding-billing.md#provisioned-model) 및 [Azure Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

    ```powershell
    New-AzRmStorageShare `
        -StorageAccount $storageAccount `
        -Name myshare `
        -EnabledProtocol NFS `
        -RootSquash RootSquash `
        -QuotaGiB 1024
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용하여 프리미엄 파일 공유를 만들려면 [az storage share create](/cli/azure/storage/share-rm) 명령을 사용합니다.

> [!NOTE]
> 프리미엄 파일 공유는 프로비저닝된 모델을 사용하여 요금이 청구됩니다. 공유의 프로비저닝된 크기는 아래의 `quota`로 지정됩니다. 자세한 내용은 [프로비저닝된 모델 이해](understanding-billing.md#provisioned-model) 및 [Azure Files 가격 책정 페이지](https://azure.microsoft.com/pricing/details/storage/files/)를 참조하세요.

```azurecli-interactive
az storage share-rm create \
    --resource-group $resourceGroup \
    --storage-account $storageAccount \
    --name "myshare" \
    --enabled-protocol NFS \
    --root-squash RootSquash \
    --quota 1024
```
---

## <a name="next-steps"></a>다음 단계
NFS 공유를 만들었으니 이를 사용하려면 Linux 클라이언트에 탑재해야 합니다. 자세한 내용은 [NFS 공유를 탑재하는 방법](storage-files-how-to-mount-nfs-shares.md)을 참조하세요.

문제가 발생하면 [Azure NFS 파일 공유 문제 해결](storage-troubleshooting-files-nfs.md)을 참조하세요.
