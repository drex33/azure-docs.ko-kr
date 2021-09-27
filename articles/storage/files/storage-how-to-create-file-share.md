---
title: Azure 파일 공유 만들기
titleSuffix: Azure Files
description: Azure Portal, PowerShell, Azure CLI를 사용하여 Azure 파일 공유를 만드는 방법입니다.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/27/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurecli, references_regions, devx-track-azurepowershell
ms.openlocfilehash: ac4d4ba50b9da33040fc1da27775d72c0156cba2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128547770"
---
# <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기
Azure 파일 공유를 만들려면 파일 공유를 사용할 방법에 관한 다음 세 가지 질문에 답해야 합니다.

- **Azure 파일 공유의 성능 요구 사항은 무엇입니까?**  
    Azure Files는 하드 디스크 기반(HDD 기반) 하드웨어에 호스트되는 표준 파일 공유와 반도체 디스크 기반(SSD 기반) 하드웨어에 호스트되는 프리미엄 파일 공유를 제공합니다.

- **Azure 파일 공유의 중복성 요구 사항은 무엇입니까?**  
    표준 파일 공유는 LRS(로컬 중복), ZRS(영역 중복), GRS(지역 중복), GZRS(지역 영역 중복) 스토리지를 제공하지만 대용량 파일 공유 기능은 지역 중복 및 영역 중복 파일 공유에서만 지원됩니다. 프리미엄 파일 공유는 어떤 형태의 지역 중복도 지원하지 않습니다.

    프리미엄 파일 공유는 지역의 하위 집합에서 로컬 중복 및 영역 중복으로 사용할 수 있습니다. 지역에서 현재 프리미엄 파일 공유를 사용할 수 있는지 확인하려면 Azure의 [지역별 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=storage) 페이지를 참조하세요. ZRS를 지원하는 지역에 대한 자세한 내용은 [Azure Storage redundancy](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)(Azure Storage 중복성)를 참조하세요.

- **어떤 크기의 파일 공유가 필요합니까?**  
    로컬 및 영역 중복 스토리지 계정에서 Azure 파일 공유는 최대 100TiB까지 확장될 수 있지만 지역 및 지역 영역 중복 스토리지 계정에서는 Azure 파일 공유가 최대 5TiB까지만 확장될 수 있습니다. 

이러한 세 가지 선택 사항에 대한 자세한 내용은 [Planning for an Azure Files deployment](storage-files-planning.md)(Azure Files 배포 계획)를 참조하세요.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="prerequisites"></a>사전 요구 사항
- 이 문서에서는 독자들이 이미 Azure 구독을 만들었다고 가정합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.
- Azure PowerShell을 사용하려면 [최신 버전을 설치](/powershell/azure/install-az-ps)하세요.
- Azure CLI를 사용하려면 [최신 버전을 설치](/cli/azure/install-azure-cli)하세요.

## <a name="create-a-storage-account"></a>스토리지 계정 만들기
Azure 파일 공유는 공유 스토리지 풀을 나타내는 최상위 개체인 *스토리지 계정* 에 배포됩니다. 이 스토리지 풀을 사용하여 여러 파일 공유를 배포할 수 있습니다. 

Azure에서는 고객에게 있을 수 있는 다양한 스토리지 시나리오를 위한 여러 스토리지 계정 유형을 지원하지만 Azure Files의 스토리지 계정은 기본적으로 두 가지 유형입니다. 만들어야 하는 스토리지 계정 유형은 표준 파일 공유를 만들지, 프리미엄 파일 공유를 만들지에 따라 결정됩니다. 

- **범용 버전 2(GPv2) 스토리지 계정**: GPv2 스토리지 계정을 사용하면 표준/하드 디스크 기반(HDD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. GPv2 스토리지 계정은 Azure 파일 공유 저장 외에도 Blob 컨테이너, 큐 또는 테이블과 같은 다른 스토리지 리소스를 저장할 수 있습니다. 파일 공유는 트랜잭션 최적화(기본값), 핫, 쿨 계층에 배포할 수 있습니다.

- **FileStorage 스토리지 계정**: FileStorage 스토리지 계정을 사용하면 프리미엄/반도체 디스크 기반(SSD 기반) 하드웨어에 Azure 파일 공유를 배포할 수 있습니다. FileStorage 계정은 Azure 파일 공유를 저장하는 데만 사용할 수 있습니다. 다른 스토리지 리소스(Blob 컨테이너, 큐, 테이블 등)는 FileStorage 계정에 배포할 수 없습니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
Azure Portal을 통해 스토리지 계정을 만들려면 대시보드에서 **+ 리소스 만들기** 를 선택합니다. 나타나는 Azure Marketplace 검색 창에서 **스토리지 계정** 을 검색하고 표시되는 검색 결과를 선택합니다. 스토리지 계정의 개요 페이지가 표시되면, **만들기** 를 선택하여 스토리지 계정 만들기 마법사를 진행합니다.

![브라우저의 스토리지 계정 빠른 생성 옵션 스크린샷](media/storage-how-to-create-file-share/create-storage-account-0.png)

#### <a name="basics"></a>기본 사항
스토리지 계정을 만들기 위해 완료할 첫 번째 섹션은 **기본 사항** 입니다. 여기에는 스토리지 계정을 만드는 데 필요한 모든 필드가 있습니다. GPv2 스토리지 계정을 만들려면 **성능** 라디오 단추를 ‘표준’으로 설정하고 **계정 종류** 드롭다운 목록을 ‘StorageV2(범용 v2)’로 선택해야 합니다. 

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-standard.png" alt-text="표준이 선택된 성능 라디오 단추와 storagev2가 선택된 계정 종류의 스크린샷":::

FileStorage 스토리지 계정을 만들려면 **성능** 라디오 단추를 ‘프리미엄’으로 설정하고 **프리미엄 계정 유형** 드롭다운 목록에서 **Fileshares** 를 선택해야 합니다.

:::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-performance-premium.png" alt-text="프리미엄이 선택된 성능 라디오 단추와 FileStorage가 선택된 계정 종류의 스크린샷.":::

다른 기본 사항 필드는 스토리지 계정 선택과 별개입니다.
- **스토리지 계정 이름**: 만들 스토리지 계정 리소스의 이름입니다. 이 이름은 전역적으로 고유해야 하지만, 그 외에는 원하는 대로 지정할 수 있습니다. 스토리지 계정 이름은 SMB를 통해 Azure 파일 공유를 탑재할 때 서버 이름으로 사용됩니다.
- **위치**: 스토리지 계정을 배포할 지역입니다. 리소스 그룹과 연결된 지역이나 사용 가능한 기타 지역을 사용할 수 있습니다.
- **복제**: 이 필드는 레이블이 복제이지만 실제로 **중복** 을 의미합니다. 즉, LRS(로컬 중복), ZRS(영역 중복), GRS(지역 중복), GZRS(지역 영역 중복) 등 원하는 중복 수준입니다. 이 드롭다운 목록에는 Azure 파일 공유에 적용되지 않는 RA-GRS(읽기 액세스 지역 중복)와 RA-GZRS(읽기 액세스 지역 영역 중복)도 포함되어 있습니다. RA-GRS와 RA-GZRS를 선택한 상태에서 스토리지 계정에서 만든 파일 공유는 실제로 각각 지역 중복이나 지역 영역 중복이 됩니다. 

#### <a name="networking"></a>네트워킹
네트워킹 섹션에서는 네트워킹 옵션을 구성할 수 있습니다. 이러한 설정은 스토리지 계정을 만들 때 선택 사항이며 원할 경우 나중에 구성할 수 있습니다. 해당 옵션에 대한 자세한 내용은 [Azure Files 네트워킹 고려 사항](storage-files-networking-overview.md)을 참조하세요.

#### <a name="data-protection"></a>데이터 보호
데이터 보호 섹션에서는 스토리지 계정의 Azure 파일 공유에 대한 일시 삭제 정책을 구성할 수 있습니다. Blob의 일시 삭제, 컨테이너, 컨테이너의 특점 시점 복원, 버전 관리, 변경 피드와 관련된 기타 설정은 Azure Blob 스토리지에만 적용됩니다.

#### <a name="advanced"></a>고급
고급 섹션에는 Azure 파일 공유의 몇 가지 중요한 설정이 포함되어 있습니다.

- **보안 전송 필요**: 이 필드는 스토리지 계정에 대한 통신을 위해 전송 중 암호화가 스토리지 계정에 필요한지 여부를 나타냅니다. SMB 2.1 지원이 필요한 경우에는 이 필드를 사용하지 않도록 설정해야 합니다.

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-secure-transfer.png" alt-text="스토리지 계정의 고급 설정에서 보안 전송을 사용하도록 설정한 스크린샷":::

- **Large file shares**(대용량 파일 공유): 이 필드는 스토리지 계정에서 100TiB까지 확장되는 파일 공유를 사용하도록 설정합니다. 이 기능을 사용하도록 설정하면 스토리지 계정에서 로컬 중복 및 영역 중복 스토리지 옵션만 사용할 수 있습니다. GPv2 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정하면 대용량 파일 공유 기능을 사용하지 않도록 설정할 수 없습니다. 모든 프리미엄 파일 공유는 100TiB까지 스케일 업할 수 있으므로 FileStorage 스토리지 계정(프리미엄 파일 공유의 스토리지 계정)에는 이 옵션이 없습니다. 

    :::image type="content" source="media/storage-how-to-create-file-share/files-create-smb-share-large-file-shares.png" alt-text="스토리지 계정의 고급 블레이드에 있는 대용량 파일 공유 설정의 스크린샷":::

고급 탭에서 사용할 수 있는 기타 설정(Azure Data Lake Storage Gen 2의 계층 구조 네임스페이스, 기본 Blob 계층, Blob 스토리지의 NFSv3 등)은 Azure Files에 적용되지 않습니다.

> [!Important]  
> Blob 액세스 계층을 선택해도 파일 공유의 계층에 영향을 주지 않습니다.

#### <a name="tags"></a>태그
태그는 동일한 태그를 여러 개의 리소스 및 리소스 그룹에 적용하여 리소스를 범주화하고 통합된 청구를 볼 수 있는 이름/값 쌍입니다. 해당 항목은 선택사항이며 스토리지 계정이 생성된 후에 적용될 수 있습니다.

#### <a name="review--create"></a>검토 + 만들기
스토리지 계정을 만드는 마지막 단계로 **검토 + 만들기** 탭에서 **만들기** 단추를 선택합니다. 스토리지 계정의 필수 필드 중 입력하지 않은 필드가 있으면 이 단추를 사용할 수 없습니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
PowerShell을 사용하여 스토리지 계정을 만들려면 `New-AzStorageAccount` cmdlet을 사용합니다. 이 cmdlet에는 많은 옵션이 있지만, 필수 옵션만 표시됩니다. 고급 옵션에 대한 자세한 내용은 [`New-AzStorageAccount` cmdlet 설명서](/powershell/module/az.storage/new-azstorageaccount)를 참조하세요.

스토리지 계정과 이후 파일 공유를 쉽게 만들 수 있도록 하기 위해 여러 매개 변수를 변수에 저장합니다. 변수 내용을 원하는 값으로 바꿔도 되지만, 스토리지 계정 이름이 전역적으로 고유해야 하는 점에 유념하세요.

```powershell
$resourceGroupName = "myResourceGroup"
$storageAccountName = "mystorageacct$(Get-Random)"
$region = "westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `-SkuName` 매개 변수는 필요한 중복 유형과 관련이 있습니다. 지역 중복 또는 지역 영역 중복 스토리지 계정이 필요하면 `-EnableLargeFileShare` 매개 변수도 제거해야 합니다.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Standard_LRS `
    -Location $region `
    -Kind StorageV2 `
    -EnableLargeFileShare
```

프리미엄 Azure 파일 공유를 저장할 수 있는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `-SkuName` 매개 변수가 `Premium`과 필요한 중복 수준인 로컬 중복(`LRS`)을 모두 포함하도록 변경되었습니다. 프리미엄 파일 공유는 GPv2 스토리지 계정 대신 FileStorage 스토리지 계정에서 만들어야 하기 때문에 `-Kind` 매개 변수는 `StorageV2`가 아니라 `FileStorage`입니다.

```powershell
$storAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -SkuName Premium_LRS `
    -Location $region `
    -Kind FileStorage 
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
Azure CLI를 사용하여 스토리지 계정을 만들려면 az storage account create 명령을 사용합니다. 이 명령에는 많은 옵션이 있지만, 필수 옵션만 표시됩니다. 고급 옵션에 대한 자세한 내용은 [`az storage account create` 명령 설명서](/cli/azure/storage/account)를 참조하세요.

스토리지 계정과 이후 파일 공유를 쉽게 만들 수 있도록 하기 위해 여러 매개 변수를 변수에 저장합니다. 변수 내용을 원하는 값으로 바꿔도 되지만, 스토리지 계정 이름이 전역적으로 고유해야 하는 점에 유념하세요.

```azurecli
resourceGroupName="myResourceGroup"
storageAccountName="mystorageacct$RANDOM"
region="westus2"
```

표준 Azure 파일 공유를 저장할 수 있는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `--sku` 매개 변수는 필요한 중복 유형과 관련이 있습니다. 지역 중복 또는 지역 영역 중복 스토리지 계정이 필요하면 `--enable-large-file-share` 매개 변수도 제거해야 합니다.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind StorageV2 \
    --sku Standard_ZRS \
    --enable-large-file-share \
    --output none
```

프리미엄 Azure 파일 공유를 저장할 수 있는 스토리지 계정을 만들려면 다음 명령을 사용합니다. `--sku` 매개 변수가 `Premium`과 필요한 중복 수준인 로컬 중복(`LRS`)을 모두 포함하도록 변경되었습니다. 프리미엄 파일 공유는 GPv2 스토리지 계정 대신 FileStorage 스토리지 계정에서 만들어야 하기 때문에 `--kind` 매개 변수는 `StorageV2`가 아니라 `FileStorage`입니다.

```azurecli
az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --kind FileStorage \
    --sku Premium_LRS \
    --output none
```

---

### <a name="enable-large-files-shares-on-an-existing-account"></a>기존 계정에서 대용량 파일 공유 사용 설정
Azure 파일 공유가 아직 없는 경우 기존 계정에서 Azure 파일 공유를 만들기 전에 대용량 파일 공유를 위해 이 공유 기능을 사용하도록 설정할 수 있습니다. LRS와 ZRS 또는 ZRS가 있는 표준 스토리지 계정은 대용량 파일 공유를 지원하도록 업그레이드할 수 있습니다. GRS, GZRS, RA-GRS 또는 RA GZRS 계정이 있는 경우 계속하기 전에 LRS 계정으로 변환해야 합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
1. [Azure Portal](https://portal.azure.com)을 열고 대용량 파일 공유를 사용하도록 설정할 스토리지 계정으로 이동합니다.
1. 스토리지 계정을 열고 **파일 공유** 를 선택합니다.
1. **대용량 파일 공유** 에서 **사용** 을 선택한 다음 **저장** 을 선택합니다.
1. **개요** 를 선택하고 **새로 고침** 을 선택합니다.
1. **용량 공유** 를 선택하고 **100TiB** 를 선택한 다음, **저장** 을 선택합니다.

    :::image type="content" source="media/storage-files-how-to-create-large-file-share/files-enable-large-file-share-existing-account.png" alt-text="100TiB 공유가 강조 표시된 Storage 계정, 파일 공유 블레이드 스크린샷.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
기존 계정에서 대용량 파일 공유를 사용하도록 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`과 `<yourResourceGroup>`을 사용자 정보로 바꿉니다.

```powershell
Set-AzStorageAccount `
    -ResourceGroupName <yourResourceGroup> `
    -Name <yourStorageAccountName> `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
기존 계정에서 대용량 파일 공유를 사용하도록 설정하려면 다음 명령을 사용합니다. `<yourStorageAccountName>`과 `<yourResourceGroup>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

---

## <a name="create-a-file-share"></a>파일 공유 만들기
스토리지 계정을 만들었으면 이제 파일 공유를 만들기만 하면 됩니다. 해당 프로세스는 프리미엄 파일 공유나 표준 파일 공유나 무엇을 사용하든 대부분 동일합니다. 다음의 차이점은 고려해야 합니다.

표준 파일 공유는 트랜잭션 최적화(기본값), 핫, 쿨 등의 표준 계층 중 하나에 배포할 수 있습니다. 이는 스토리지 계정의 **Blob 액세스 계층** 의 영향을 받지 않는 파일 공유별 계층입니다. 해당 속성은 Azure Blob 스토리지와만 관련이 있고 Azure Files와는 전혀 관련이 없습니다. 공유의 계층은 배포 후 언제든지 변경할 수 있습니다. 프리미엄 파일 공유는 표준 계층으로 바로 변환할 수 없습니다.

> [!Important]  
> GPv2 스토리지 계정 유형(트랜잭션 최적화, 핫 및 쿨) 내의 계층 간에 공유를 이동할 수 있습니다. 계층 간 공유 이동으로 인해 트랜잭션이 발생합니다. 핫 계층에서 쿨 계층으로 이동하면 공유의 각 파일에 대해 쿨 계층의 쓰기 트랜잭션 요금이 발생하는 반면, 쿨 계층에서 핫 계층으로 이동하면 각 파일 공유에 대해 쿨 계층의 읽기 트랜잭션 요금이 발생합니다.

**할당량** 속성은 프리미엄 및 표준 파일 공유 간에 의미가 약간 다릅니다.

- 표준 파일 공유의 경우에는 최종 사용자가 초과할 수 없는 Azure 파일 공유의 상한입니다. 할당량을 지정하지 않으면 표준 파일 공유는 최대 100TiB(또는 스토리지 계정의 대용량 파일 공유 속성을 설정하지 않은 경우 5TiB)까지 확장될 수 있습니다. 대용량 파일 공유를 사용하도록 설정한 스토리지 계정을 만들지 않은 경우 100TiB 파일 공유를 사용하도록 설정하는 방법은 [기존 계정에서 대용량 파일 공유 사용](#enable-large-files-shares-on-an-existing-account)을 참조하세요. 

- 프리미엄 파일 공유의 경우 할당량은 **프로비저닝된 크기** 를 의미합니다. 프로비저닝된 크기는 실제 사용량과 관계없이 요금이 청구되는 양입니다. 프리미엄 파일 공유에서 사용할 수 있는 IOPS 및 처리량은 프로비전된 크기를 기반으로 합니다. 프리미엄 파일 공유를 계획하는 방법에 대한 자세한 내용은 [provisioning premium file shares](understanding-billing.md#provisioned-model)(프리미엄 파일 공유 프로비저닝)를 참조하세요.

# <a name="portal"></a>[포털](#tab/azure-portal)
이제 막 스토리지 계정을 만들었으면 **리소스로 이동** 을 선택하여 배포 화면에서 해당 계정으로 이동할 수 있습니다. 스토리지 계정으로 이동했으면 스토리지 계정의 목차에서 **파일 공유** 를 선택합니다.

파일 공유 목록에 앞에서 해당 스토리지 계정에서 계정에 만든 모든 파일 공유가 표시됩니다. 아직 파일 공유를 만들지 않은 경우 빈 테이블이 표시됩니다. **+ 파일 공유** 를 선택하여 새 파일 공유를 만듭니다.

새 파일 공유 블레이드가 화면에 표시됩니다. 새 파일 공유 블레이드의 필드를 작성하여 파일 공유를 만듭니다.

- **이름**: 만들 파일 공유의 이름입니다.
- **할당량**: 표준 파일 공유의 경우 파일 공유의 할당량, 프리미엄 파일 공유의 경우 파일 공유의 프로비저닝된 크기입니다. 표준 파일 공유의 경우 할당량에 따라서도 수신 성능이 결정됩니다.
- **계층**: 파일 공유에 대해 선택한 계층입니다. 이 필드는 **범용(GPv2) 스토리지 계정** 에서만 사용할 수 있습니다. 트랜잭션 최적화, 핫, 쿨을 선택할 수 있습니다. 공유의 계층은 언제든지 변경할 수 있습니다. 마이그레이션 중에는 가능한 가장 많이 사용되는 계층을 선택하여 트랜잭션 비용을 최소화하고 마이그레이션이 완료된 후 필요하면 낮은 계층으로 전환하는 것이 좋습니다.

**만들기** 를 선택하여 새 공유 만들기를 완료합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
[`New-AzRmStorageShare`](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet을 사용하여 Azure 파일 공유를 만들 수 있습니다. 다음 PowerShell 명령에서는 위의 Azure PowerShell을 사용하여 스토리지 계정 만들기 섹션에 정의된 대로 `$resourceGroupName` 및 `$storageAccountName` 변수를 설정했다고 가정합니다. 

다음 예제에서는 `-AccessTier` 매개 변수를 사용하여 명시적 계층으로 파일 공유를 만드는 방법을 보여 줍니다. 계층을 지정하지 않으면 표준 파일 공유에 대한 기본 계층의 트랜잭션이 최적화됩니다.

> [!Important]  
> 프리미엄 파일 공유의 경우 `-QuotaGiB` 매개 변수는 파일 공유의 프로비저닝된 크기를 나타냅니다. 파일 공유의 프로비저닝된 크기는 사용량과 관계없이 요금이 청구되는 양입니다. 표준 파일 공유는 프로비저닝된 크기가 아니라 사용량을 기준으로 요금이 청구됩니다.

```powershell
# Assuming $resourceGroupName and $storageAccountName from earlier in this document have already
# been populated. The access tier parameter may be TransactionOptimized, Hot, or Cool for GPv2 
# storage accounts. Standard tiers are only available in standard storage accounts. 
$shareName = "myshare"

New-AzRmStorageShare `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $storageAccountName `
        -Name $shareName `
        -AccessTier TransactionOptimized `
        -QuotaGiB 1024 | `
    Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
[`az storage share-rm create`](/cli/azure/storage/share-rm#az_storage_share_rm_create) 명령을 사용하여 Azure 파일 공유를 만들 수 있습니다. 다음 Azure CLI 명령에서는 위의 Azure CLI를 사용하여 스토리지 계정 만들기 섹션에 정의된 대로 `$resourceGroupName` 및 `$storageAccountName` 변수를 설정했다고 가정합니다.

> [!Important]  
> 프리미엄 파일 공유의 경우 `--quota` 매개 변수는 파일 공유의 프로비저닝된 크기를 나타냅니다. 파일 공유의 프로비저닝된 크기는 사용량과 관계없이 요금이 청구되는 양입니다. 표준 파일 공유는 프로비저닝된 크기가 아니라 사용량을 기준으로 요금이 청구됩니다.

```azurecli
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "TransactionOptimized" \
    --quota 1024 \
    --output none
```

---

> [!Note]  
> 파일 공유의 이름은 모두 소문자여야 합니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

### <a name="change-the-tier-of-an-azure-file-share"></a>Azure 파일 공유의 계층 변경
**범용 v2(GPv2) 스토리지 계정** 에 배포된 파일 공유는 트랜잭션 최적화, 핫, 쿨 계층에 있을 수 있습니다. 위에서 설명한 대로 Azure 파일 공유의 계층은 언제든 변경할 수 있으며, 변경 시 트랜잭션 비용이 적용됩니다.

# <a name="portal"></a>[포털](#tab/azure-portal)
기본 스토리지 계정 페이지에서 **파일 공유** 를 선택하고 **파일 공유** 라는 타일을 선택합니다. 스토리지 계정의 목차를 통해 **파일 공유** 로 이동할 수도 있습니다.

:::image type="content" source="media/storage-files-quick-create-use-windows/click-files.png" alt-text="파일 공유가 선택되어 있는 스토리지 계정 블레이드의 스크린샷":::

파일 공유의 테이블 목록에서 계층을 변경할 파일 공유를 선택합니다. 파일 공유 개요 페이지의 메뉴에서 **계층 변경** 을 선택합니다.

![계층 변경 단추가 강조 표시된 파일 공유 개요 페이지의 스크린샷](media/storage-how-to-create-file-share/change-tier-0.png)

나타나는 대화 상자에서 원하는 계층(트랜잭션 최적화, 핫, 쿨)을 선택합니다.

![계층 변경 대화 상자의 스크린샷](media/storage-how-to-create-file-share/change-tier-1.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
다음 PowerShell cmdlet에서는 이 문서의 이전 섹션에 설명된 대로 `$resourceGroupName`, `$storageAccountName`, `$shareName` 변수를 설정했다고 가정합니다.

```PowerShell
Update-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -AccessTier Cool
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
다음 Azure CLI 명령에서는 이 문서의 이전 섹션에 설명된 대로 `$resourceGroupName`, `$storageAccountName`, `$shareName` 변수를 설정했다고 가정합니다.

```azurecli
az storage share-rm update \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --access-tier "Cool"
```

---

### <a name="expand-existing-file-shares"></a>기존 파일 공유 확장
기존 스토리지 계정에서 대용량 파일 공유를 사용하도록 설정한 경우 해당 스토리지 계정에서 기존 파일 공유를 확장하여 늘어난 용량과 규모를 활용해야 합니다. 

# <a name="portal"></a>[포털](#tab/azure-portal)
1. 스토리지 계정에서 **파일 공유** 를 선택합니다.
1. 파일 공유를 마우스 오른쪽 단추로 클릭한 다음 **할당량** 을 선택합니다.
1. 원하는 새 크기를 입력하고 **확인** 을 선택합니다.

![기존 파일 공유의 할당량이 표시된 Azure Portal UI](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<YourResourceGroupName>`, `<YourStorageAccountName>`, `<YourStorageAccountFileShareName>`을 사용자 정보로 바꿉니다.

```powershell
$resourceGroupName = "<YourResourceGroupName>"
$storageAccountName = "<YourStorageAccountName>"
$shareName="<YourStorageAccountFileShareName>"

# update quota
Set-AzRmStorageShare `
    -ResourceGroupName $resourceGroupName `
    -StorageAccountName $storageAccountName `
    -Name $shareName `
    -QuotaGiB 102400
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
할당량을 최대 크기로 설정하려면 다음 명령을 사용합니다. `<yourResourceGroupName>`, `<yourStorageAccountName>`, `<yourFileShareName>`을 사용자 정보로 바꿉니다.

```azurecli-interactive
az storage share-rm update \
    --resource-group <yourResourceGroupName> \
    --storage-account <yourStorageAccountName> \
    --name <yourFileShareName> \
    --quota 102400
```

---

## <a name="next-steps"></a>다음 단계
- [Azure Files의 배포 계획](storage-files-planning.md) 또는 [Azure 파일 동기화의 배포 계획](../file-sync/file-sync-planning.md). 
- [네트워킹 개요](storage-files-networking-overview.md)
- [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), [Linux](storage-how-to-use-files-linux.md)에서 파일 공유 연결 및 탑재
