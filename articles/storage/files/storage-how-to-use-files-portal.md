---
title: Azure 파일 공유를 관리하기 위한 빠른 시작
description: Azure Portal, Azure CLI 또는 Azure PowerShell 모듈을 사용하여 Azure 파일 공유를 만들고 관리하는 방법을 알아봅니다. 스토리지 계정을 만들고, Azure 파일 공유를 만들고, Azure 파일 공유를 사용합니다.
author: roygara
ms.service: storage
ms.topic: quickstart
ms.date: 09/17/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 24a84fb0bc64c4c44d056098e8c429660b88e0e0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603527"
---
# <a name="quickstart-create-and-manage-azure-file-shares"></a>빠른 시작: Azure 파일 공유 만들기 및 관리
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 Windows, Linux 및 macOS에 탑재할 수 있습니다. 이 가이드에서는 Azure Portal, Azure CLI 또는 Azure PowerShell 모듈을 사용하여 Azure 파일 공유 작업에 대한 기본 사항을 안내합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |

## <a name="pre-requisites"></a>필수 구성 요소

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

PowerShell을 로컬로 설치하고 사용하려는 경우 이 자습서에서는 Azure PowerShell 모듈 Az 버전 0.7 이상이 필요합니다. 실행 중인 Azure PowerShell 모듈의 버전을 알아보려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. PowerShell을 로컬로 실행하는 경우 `Login-AzAccount`도 실행하여 Azure 계정에 로그인해야 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.4 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.

- 기본적으로 Azure CLI 명령은 JSON(JavaScript Object Notation)을 반환합니다. JSON은 REST API에서 메시지를 보내고 받는 표준 방법입니다. JSON 응답으로 작업을 용이하게 하기 위해 이 문서의 일부 예제에서는 Azure CLI 명령에 *쿼리* 매개 변수를 사용합니다. 이 매개 변수는 JSON을 구문 분석하기 위해 [JMESPath 쿼리 언어](http://jmespath.org/)를 사용합니다. JMESPath 쿼리 언어를 따라 Azure CLI 명령의 결과를 사용하는 방법에 대해 자세히 알아보려면 [JMESPath 자습서](http://jmespath.org/tutorial.html)를 참조하세요.

---

## <a name="create-a-storage-account"></a>스토리지 계정 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)


### <a name="powershell---create-a-resource-group"></a>PowerShell - 리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure 리소스 그룹이 아직 없는 경우 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 사용하여 새 리소스 그룹을 만들 수 있습니다. 스토리지 계정을 만들려면 리소스 그룹이 필요합니다.

다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹을 미국 서부 2 지역에 만듭니다.

```azurepowershell-interactive
$resourceGroupName = "myResourceGroup"
$region = "westus2"

New-AzResourceGroup `
    -Name $resourceGroupName `
    -Location $region | Out-Null
```

### <a name="powershell---create-a-storage-account"></a>PowerShell - 스토리지 계정 만들기

스토리지 계정은 Azure 파일 공유를 배포하는 데 사용할 수 있는 스토리지의 공유 풀입니다.

이 예제에서는 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet을 사용하여 스토리지 계정을 만듭니다. 스토리지 계정의 이름은 *mystorageaccount\<random number>* 이며 이 스토리지 계정에 대한 참조는 **$storageAcct** 변수에 저장됩니다. 스토리지 계정 이름은 고유해야 합니다. 따라서 `Get-Random`를 사용하여 고유하게 만들 이름에 숫자를 추가합니다. 

```azurepowershell-interactive 
$storageAccountName = "mystorageacct$(Get-Random)"

$storageAcct = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $region `
    -Kind StorageV2 `
    -SkuName Standard_LRS `
    -EnableLargeFileShare
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

### <a name="cli---create-a-resource-group"></a>CLI - 리소스 그룹 만들기
리소스 그룹은 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. Azure 리소스 그룹이 아직 없는 경우 [az group create](/cli/azure/group) 명령을 사용하여 새 리소스 그룹을 만들 수 있습니다. 스토리지 계정을 만들려면 리소스 그룹이 필요합니다.

다음 예제에서는 *myResourceGroup* 이라는 리소스 그룹을 *미국 서부 2* 위치에 만듭니다.

```azurecli-interactive 
export resourceGroupName="myResourceGroup"
region="westus2"

az group create \
    --name $resourceGroupName \
    --location $region \
    --output none
```

### <a name="cli---create-a-storage-account"></a>CLI - 스토리지 계정 만들기
스토리지 계정은 Azure 파일 공유를 배포할 수 있는 스토리지의 공유 풀입니다.

다음 예제에서는 [az storage account create](/cli/azure/storage/account) 명령을 사용하여 스토리지 계정을 만듭니다. 스토리지 계정 이름은 고유해야 합니다. 따라서 `$RANDOM`를 사용하여 고유하게 만들 이름에 숫자를 추가합니다.

```azurecli-interactive 
export storageAccountName="mystorageacct$RANDOM"

az storage account create \
    --resource-group $resourceGroupName \
    --name $storageAccountName \
    --location $region \
    --kind StorageV2 \
    --sku Standard_LRS \
    --enable-large-file-share \
    --output none
```


---


## <a name="create-an-azure-file-share"></a>Azure 파일 공유 만들기


# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 파일 공유 만들려면:

1. 대시보드에서 스토리지 계정을 선택합니다.
1. 스토리지 계정 페이지의 **서비스** 섹션에서 **파일** 을 선택합니다.
    
    ![스토리지 계정의 데이터 스토리지 섹션에 대한 스크린샷: 파일 공유를 선택합니다.](media/storage-how-to-use-files-portal/create-file-share-1.png)

1. **파일 서비스** 페이지 위쪽의 메뉴에서 **파일 공유** 를 클릭합니다. **새 파일 공유** 페이지가 드롭다운 방식으로 펼쳐집니다.
1. **이름** 에서 *myshare* 를 입력하고, 할당량을 입력하고, **계층** 에 대해 **트랜잭션 최적화됨** 을 선택한 상태로 둡니다.
1. **만들기** 를 선택하여 Azure 파일 공유를 만듭니다.

공유 이름은 모두 소문자, 숫자 및 단일 하이픈이어야 하지만 하이픈으로 시작할 수 없습니다. 파일 공유 및 파일 이름 지정에 대한 자세한 내용은 [공유, 디렉터리, 파일 및 메타데이터 이름 지정 및 참조](/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata)를 참조하세요.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

이제 스토리지 계정을 만들었으므로 첫 번째 Azure 파일 공유를 만들 수 있습니다. [New-AzRmStorageShare](/powershell/module/az.storage/New-AzRmStorageShare) cmdlet을 사용하여 파일 공유를 만듭니다. 다음 예제에서는 **myshare** 라는 공유를 만듭니다.

```azurepowershell-interactive
$shareName = "myshare"

New-AzRmStorageShare `
    -StorageAccount $storageAcct `
    -Name $shareName `
    -EnabledProtocol SMB `
    -QuotaGiB 1024 | Out-Null
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

이제 스토리지 계정을 만들었으므로 첫 번째 Azure 파일 공유를 만들 수 있습니다. [az storage share-rm create](/cli/azure/storage/share-rm?view=azure-cli-latest&preserve-view=false#az_storage_share_rm_create) 명령을 사용하여 파일 공유를 만듭니다. 이 예제에서는 **myshare** 라는 Azure 파일 공유를 만듭니다. 

```azurecli-interactive
shareName="myshare"

az storage share-rm create \
    --resource-group $resourceGroupName \
    --storage-account $storageAccountName \
    --name $shareName \
    --quota 1024 \
    --enabled-protocols SMB \
    --output none
```

---

#### <a name="create-a-directory"></a>디렉터리 만들기

# <a name="portal"></a>[포털](#tab/azure-portal)

Azure 파일 공유의 루트에 *myDirectory* 라는 새 디렉터리를 만들려면,

1. **파일 서비스** 페이지에서 **myshare** 파일 공유를 선택합니다. 파일 공유 페이지가 열립니다.
1. 페이지 위쪽의 메뉴에서 **+ 디렉터리 추가** 를 선택합니다. **새 디렉터리** 페이지가 드롭다운 방식으로 펼쳐집니다.
1. *myDirectory* 를 입력한 다음, **확인** 을 클릭합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Azure 파일 공유의 루트에 **myDirectory** 라는 새 디렉터리를 만들려면 [New-AzStorageDirectory](/powershell/module/az.storage/New-AzStorageDirectory) cmdlet을 사용합니다.

```azurepowershell-interactive
New-AzStorageDirectory `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Path "myDirectory"
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure 파일 공유의 루트에 **myDirectory** 라는 새 디렉터리를 만들려면 [`az storage directory create`](/cli/azure/storage/directory) 명령을 사용합니다.

```azurecli-interactive
az storage directory create \
   --account-name $storageAccountName \
   --account-key $storageAccountKey \
   --share-name $shareName \
   --name "myDirectory" \
   --output none
```

---


#### <a name="upload-a-file"></a>파일 업로드 
# <a name="portal"></a>[포털](#tab/azure-portal)


파일 업로드를 실제로 보여 주려면 먼저 업로드할 파일을 만들거나 선택해야 합니다. 적절하다고 판단되는 어떤 방법으로든 이 작업을 수행할 수 있습니다. 업로드하려는 파일이 선택되면 다음을 수행합니다.

1. **myDirectory** 디렉터리를 선택합니다. **myDirectory** 패널이 열립니다.
1. 위쪽 메뉴에서 **업로드** 를 선택합니다. **파일 업로드** 패널이 열립니다.  
    
    ![파일 업로드 패널의 스크린샷](media/storage-how-to-use-files-portal/upload-file-1.png)

1. 폴더 아이콘을 선택하여 로컬 파일을 탐색할 수 있는 창을 엽니다. 
1. 파일을 선택한 다음, **열기** 를 선택합니다. 
1. **파일 업로드** 페이지에서 파일 이름을 확인한 다음, **업로드** 를 선택합니다.
1. 완료되면 파일이 **myDirectory** 페이지의 목록에 표시됩니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Set-AzStorageFileContent](/powershell/module/az.storage/Set-AzStorageFileContent) cmdlet을 사용하여 파일을 업로드하는 방법을 설명하려면 먼저 업로드할 PowerShell Cloud Shell의 임시 드라이브 내에 파일을 만들어야 합니다. 

이 예제에서는 임시 드라이브의 새 파일에 현재 날짜 및 시간을 저장한 다음, 파일 공유에 파일을 업로드합니다.

```azurepowershell-interactive
# this expression will put the current date and time into a new file on your scratch drive
cd "~/CloudDrive/"
Get-Date | Out-File -FilePath "SampleUpload.txt" -Force

# this expression will upload that newly created file to your Azure file share
Set-AzStorageFileContent `
   -Context $storageAcct.Context `
   -ShareName $shareName `
   -Source "SampleUpload.txt" `
   -Path "myDirectory\SampleUpload.txt"
```   

PowerShell을 로컬로 실행하는 경우 `~/CloudDrive/`를 컴퓨터에 있는 경로로 바꿉니다.

파일을 업로드한 후에 [Get-AzStorageFile](/powershell/module/Az.Storage/Get-AzStorageFile) cmdlet을 사용하여 파일이 Azure 파일 공유에 업로드되었는지 확인할 수 있습니다. 

```azurepowershell-interactive
Get-AzStorageFile `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\" 
```


# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[`az storage file upload`](/cli/azure/storage/file) 명령을 사용하여 파일을 업로드하는 방법을 보여주기 위해 먼저 Cloud Shell의 임시 드라이브에 업로드할 파일을 만듭니다. 다음 예제에서는 파일을 만든 다음, 업로드합니다.

```azurecli-interactive
cd ~/clouddrive/
date > SampleUpload.txt

az storage file upload \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --source "SampleUpload.txt" \
    --path "myDirectory/SampleUpload.txt"
```

Azure CLI를 로컬로 실행 중인 경우 `~/clouddrive`를 컴퓨터에 있는 경로로 대체합니다.

파일을 업로드한 후에 [`az storage file list`](/cli/azure/storage/file) 명령을 사용하여 파일이 Azure 파일 공유에 업로드되었는지 확인할 수 있습니다.

```azurecli-interactive
az storage file list \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory" \
    --output table
```


---

#### <a name="download-a-file"></a>파일 다운로드
# <a name="portal"></a>[포털](#tab/azure-portal)

파일을 마우스 오른쪽 단추로 클릭하여 업로드한 파일의 복사본을 다운로드할 수 있습니다. 다운로드 단추를 선택하면 표시되는 정확한 환경은 사용하는 운영 체제와 브라우저에 따라 다릅니다.


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

[Get-AzStorageFileContent](/powershell/module/az.storage/Get-AzStorageFilecontent) cmdlet을 사용하여 업로드한 파일의 복사본을 Cloud Shell의 스크래치 드라이브에 다운로드할 수 있습니다.

```azurepowershell-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists because you've run this example before.
Remove-Item `
    -Path "SampleDownload.txt" `
    -Force `
    -ErrorAction SilentlyContinue

Get-AzStorageFileContent `
    -Context $storageAcct.Context `
    -ShareName $shareName `
    -Path "myDirectory\SampleUpload.txt" `
    -Destination "SampleDownload.txt"
```

파일에 다운로드한 후에 `Get-ChildItem`을 사용하여 PowerShell Cloud Shell 임시 드라이브에 파일을 다운로드했는지 확인할 수 있습니다.

```azurepowershell-interactive
Get-ChildItem | Where-Object { $_.Name -eq "SampleDownload.txt" }
``` 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

[`az storage file download`](/cli/azure/storage/file) 명령을 사용하여 Cloud Shell의 임시 드라이브에 업로드한 파일의 복사본을 다운로드할 수 있습니다.

```azurecli-interactive
# Delete an existing file by the same name as SampleDownload.txt, if it exists, because you've run this example before
rm -f SampleDownload.txt

az storage file download \
    --account-name $storageAccountName \
    --account-key $storageAccountKey \
    --share-name $shareName \
    --path "myDirectory/SampleUpload.txt" \
    --dest "SampleDownload.txt" \
    --output none
```

---

## <a name="clean-up-resources"></a>리소스 정리
# <a name="portal"></a>[포털](#tab/azure-portal)

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

완료되면 [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) cmdlet을 사용하여 리소스 그룹 및 해당 리소스 그룹에 포함된 모든 리소스를 삭제할 수 있습니다. 

```azurepowershell-interactive
Remove-AzResourceGroup -Name myResourceGroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

완료되면 [`az group delete`](/cli/azure/group) 명령을 사용하여 리소스 그룹 및 해당 리소스 그룹에 포함된 모든 리소스를 삭제할 수 있습니다. 

```azurecli-interactive 
az group delete --name $resourceGroupName
```


---

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Files란?](storage-files-introduction.md)