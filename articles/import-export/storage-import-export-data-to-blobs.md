---
title: Azure Import/Export 서비스를 사용하여 Azure Blob Storage로 데이터를 가져오는 자습서 | Microsoft Docs
description: Azure Portal에서 가져오기 및 내보내기 작업을 만들어 Azure Blob 간에 데이터를 전송하는 방법을 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/04/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 10f2103049b47366a267fdf0412a7e3fb95a95cd
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709635"
---
# <a name="tutorial-import-data-to-blob-storage-with-azure-importexport-service"></a>자습서: Azure Import/Export 서비스를 사용하여 Blob Storage로 데이터 가져오기

이 문서에서는 Azure Import/Export 서비스를 사용하여 Azure Blob Storage로 많은 양의 데이터를 안전하게 가져오는 방법에 대한 단계별 지침을 제공합니다. 데이터를 Azure Blob으로 가져오려면 서비스를 사용하여 데이터가 포함된 암호화된 디스크 드라이브를 Azure 데이터 센터로 배송해야 합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Azure Blob 스토리지로 데이터를 가져오기 위한 필수 조건
> * 1단계: 드라이브 준비
> * 2단계: 가져오기 작업 만들기
> * 3단계: 고객 관리형 키 구성(선택 사항)
> * 4단계: 드라이브 배송
> * 5단계: 추적 정보를 사용하여 작업 업데이트
> * 6단계: Azure에 대한 데이터 업로드 확인

## <a name="prerequisites"></a>사전 요구 사항

가져오기 작업을 만들어 Azure Blob Storage로 데이터를 전송하기 전에 이 서비스에 대한 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다.
다음이 필요합니다.

* Import/Export 서비스에 사용할 수 있는 활성 Azure 구독이 있어야 합니다.
* 스토리지 컨테이너가 있는 Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 스토리지 계정 및 스토리지 유형](storage-import-export-requirements.md) 목록을 참조하세요.
  * 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](../storage/common/storage-account-create.md)(영문)을 참조하세요.
  * 스토리지 컨테이너 생성에 대한 자세한 내용은 [스토리지 컨테이너 만들기](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)로 이동하세요.
* [지원되는 형식](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다.
* [지원되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행하는 Windows 시스템이 있어야 합니다.
* Windows 시스템에서 BitLocker를 사용하도록 설정합니다. [BitLocker를 사용하도록 설정하는 방법](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)을 참조하세요.
* Windows 시스템에서 Blob용 Azure Import/Export 버전 1 도구의 현재 릴리스를 다운로드합니다.
  1. [WAImportExport 버전 1을 다운로드](https://www.microsoft.com/download/details.aspx?id=42659)합니다. 현재 버전은 1.5.0.300입니다.
  1. `WaImportExportV1` 기본 폴더에 압축을 풉니다. 예들 들어 `C:\WaImportExportV1`입니다.
* FedEx/DHL 계정이 있습니다. FedEx/DHL 이외의 운송업체를 사용하려면 `adbops@microsoft.com`에 있는 Azure Data Box 운영 팀에 문의하세요.
  * 계정은 유효해야 하고, 잔액이 있어야 하며, 반품 기능이 있어야 합니다.
  * 내보내기 작업의 추적 번호를 생성합니다.
  * 모든 작업에는 별도의 추적 번호가 있어야 합니다. 추적 번호가 동일한 여러 작업은 지원되지 않습니다.
  * 운송업체 계정이 없는 경우, 다음으로 이동합니다.
    * [FedEx 계정 만들기](https://www.fedex.com/en-us/create-account.html) 또는
    * [DHL 계정 만들기](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-prepare-the-drives"></a>1단계: 드라이브 준비

이 단계에서는 업무 일지 파일을 생성합니다. 업무 일지 파일에는 드라이브 일련 번호, 암호화 키 및 스토리지 계정 세부 정보와 같은 기본 정보가 저장됩니다.

다음 단계를 수행하여 드라이브를 준비합니다.

1. SATA 커넥터를 통해 디스크 드라이브를 Windows 시스템에 연결합니다.
2. 각 드라이브에 단일 NTFS 볼륨을 만듭니다. 볼륨에 드라이브 문자를 할당합니다. 탑재 지점은 사용하지 마세요.
3. NTFS 볼륨에서 BitLocker 암호화를 사용하도록 설정합니다. Windows Server 시스템을 사용하는 경우 [Windows Server 2012 R2에서 BitLocker를 사용하도록 설정하는 방법](https://thesolving.com/storage/how-to-enable-bitlocker-on-windows-server-2012-r2/)의 지침을 따르세요.
4. 데이터를 암호화된 볼륨으로 복사합니다. 끌어서 놓기, Robocopy 또는 이러한 복사 도구를 사용합니다. 저널( *.jrn*) 파일이 도구를 실행한 폴더와 동일한 폴더에 만들어집니다.

   드라이브가 잠겨 있고 이를 잠금 해제해야 하는 경우 사용 사례에 따라 잠금 해제 단계가 다를 수 있습니다.

   * 미리 암호화된 드라이브에 데이터를 추가한 경우(암호화에 WAImportExport 도구가 사용되지 않았음) 팝업에서 BitLocker 키(지정한 숫자 암호)를 사용하여 드라이브 잠금을 해제합니다.

   * WAImportExport 도구를 사용하여 암호화된 드라이브에 데이터를 추가한 경우 드라이브 잠금을 해제 하려면 다음 명령을 사용합니다.

        `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from journal (*.jrn*) file>`

5. 관리 권한이 있는 PowerShell 또는 명령줄 창을 엽니다. 압축을 푼 폴더로 디렉터리를 변경하려면 다음 명령을 실행합니다.

    `cd C:\WaImportExportV1`
6. 드라이브의 BitLocker 키를 가져오려면 다음 명령을 실행합니다.

    `manage-bde -protectors -get <DriveLetter>:`
7. 디스크를 준비하려면 다음 명령을 실행합니다. **데이터 크기에 따라 디스크 준비 작업이 몇 시간에서 며칠이 걸릴 수 있습니다.**

    ```powershell
    ./WAImportExport.exe PrepImport /j:<journal file name> /id:session<session number> /t:<Drive letter> /bk:<BitLocker key> /srcdir:<Drive letter>:\ /dstdir:<Container name>/ /blobtype:<BlockBlob or PageBlob> /skipwrite
    ```
    
    저널 파일이 도구를 실행한 폴더와 동일한 폴더에 만들어집니다. *.xml* 파일(도구를 실행하는 폴더)과 *drive-manifest.xml* 파일(데이터가 있는 폴더)의 두 개의 다른 파일도 만들어집니다.

    다음 표에는 사용되는 매개 변수가 나와 있습니다.

    |옵션  |설명  |
    |---------|---------|
    |/j:     |확장명이 .jrn인 저널 파일의 이름입니다. 저널 파일은 드라이브마다 생성됩니다. 디스크 일련 번호를 저널 파일 이름으로 사용하는 것이 좋습니다.         |
    |/id:     |세션 ID. 명령의 각 인스턴스마다 고유한 세션 번호를 사용합니다.      |
    |/t:     |배송할 디스크의 드라이브 문자입니다. 예: `D` 드라이브         |
    |/bk:     |드라이브의 BitLocker 키입니다. `manage-bde -protectors -get D:` 출력의 숫자 암호입니다.      |
    |/srcdir:     |`:\` 다음에 나오는 배송될 디스크의 드라이브 문자입니다. 예들 들어 `D:\`입니다.         |
    |/dstdir:     |Azure Storage에 있는 대상 컨테이너 이름입니다.         |
    |/blobtype:     |이 옵션은 데이터를 가져올 BLOB의 형식을 지정합니다. 블록 BLOB의 경우 BLOB 형식은 `BlockBlob`이고, 페이지 BLOB의 경우에는 `PageBlob`입니다.         |
    |/skipwrite:     | 복사하는 데 필요한 새 데이터가 없고 디스크의 기존 데이터를 준비하도록 지정합니다.          |
    |/enablecontentmd5:     |사용 설정된 경우 MD5가 계산되고 각 BLOB에 대해 `Content-md5` 속성으로 설정됩니다. 데이터가 Azure에 업로드된 이후 `Content-md5` 필드를 사용하려는 경우에만 이 옵션을 사용합니다. <br> 이 옵션은 기본적으로 이루어지는 데이터 무결성 검사에는 영향을 미치지 않습니다. 이 설정은 클라우드로 데이터를 업로드하는 데 걸리는 시간을 늘립니다.          |

    > [!NOTE]
    > 대상 컨테이너의 기존 Blob과 동일한 이름의 Blob을 가져오는 경우 가져온 Blob은 기존 Blob을 덮어쓰게 됩니다. 이전 도구 버전(1.5.0.300 이전)에서는 가져온 Blob의 이름이 기본적으로 바뀌었으며 \Disposition 매개 변수를 사용하여 가져오기에서 Blob의 이름을 바꾸거나, 덮어쓰거나, 무시할지 여부를 지정할 수 있습니다.

8. 배송해야 하는 각 디스크에 대해 이전 단계를 반복합니다. 

   명령줄을 실행할 때마다 제공된 이름의 저널 파일이 만들어집니다. 

   저널 파일과 함께 `<Journal file name>_DriveInfo_<Drive serial ID>.xml` 파일도 도구가 있는 폴더와 동일한 폴더에 만들어집니다. 저널 파일이 너무 큰 경우 작업을 만들 때는 .xml 파일이 저널 파일 대신 사용됩니다.

> [!IMPORTANT]
> * 디스크 준비를 완료한 후에는 저널 파일이나 디스크 드라이브의 데이터를 수정하지 말고 디스크를 다시 포맷하지 마세요.
> * 포털에서 허용하는 저널 파일의 최대 크기는 2MB입니다. 저널 파일이 이 제한을 초과하면 오류가 반환됩니다.

## <a name="step-2-create-an-import-job"></a>2단계: 가져오기 작업 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

다음 단계를 수행하여 Azure Portal에서 가져오기 작업을 만듭니다.

1. https://portal.azure.com/에 로그온합니다.
2. **가져오기/내보내기 작업** 을 검색합니다.

   ![가져오기/내보내기 작업 검색](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **+새로 만들기** 를 선택합니다.

   ![새로 만들기를 선택하여 새로 만들기 ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **기본 사항** 에서

   1. 구독을 선택합니다.
   1. 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하고 새 리소스 그룹을 만듭니다.
   1. 가져오기 작업을 설명하는 이름을 입력합니다. 이름을 사용하여 작업 진행 상황을 추적합니다.
      * 이름에는 소문자, 숫자 및 하이픈만 포함할 수 있습니다.
      * 이름은 문자로 시작해야 하며, 공백은 포함할 수 없습니다.

   1. **Azure로 가져오기** 를 선택합니다.

    ![가져오기 작업 만들기 - 1단계](./media/storage-import-export-data-to-blobs/import-to-blob-3.png)

    **다음: 작업 세부 정보 >** 를 선택하여 계속 진행합니다.

5. **작업 세부 정보** 에서:

   1. 이전 [1단계: 드라이브 준비](#step-1-prepare-the-drives) 중에 만든 업무 일지 파일을 업로드합니다. `waimportexport.exe version1`이 사용된 경우 준비한 각 드라이브에 대해 파일을 하나씩 업로드합니다. 저널 파일 크기가 2MB를 초과하면 저널 파일을 사용하여 만든 `<Journal file name>_DriveInfo_<Drive serial ID>.xml`도 사용할 수 있습니다.
   1. 주문의 대상 Azure 지역을 선택합니다.
   1. 가져오기에 대한 스토리지 계정을 선택합니다.
      
      하차 위치는 선택한 스토리지 계정의 지역을 기반으로 자동으로 채워집니다.
   1. 자세한 정보 표시 로그를 저장하지 않으려면 **'waimportexport' Blob 컨테이너에 자세한 정보 표시 로그 저장** 옵션을 선택 취소합니다.

   ![가져오기 작업 만들기 - 2단계](./media/storage-import-export-data-to-blobs/import-to-blob-4.png).

   **다음: 배송 >** 을 선택하여 계속 진행합니다.

6. **배송** 에서:

   1. 드롭다운 목록에서 운송업체를 선택합니다. FedEx/DHL 이외의 운송업체를 사용하려면 드롭다운에서 기존 옵션을 선택합니다. 사용하려는 운송업체에 대한 정보를 사용하여 `adbops@microsoft.com`에서 Azure Data Box 운영 팀에 문의하세요.
   1. 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다. 계정 번호가 없는 경우 [FedEx](https://www.fedex.com/us/oadr/) 또는 [DHL](https://www.dhl.com/) 운송업체 계정을 만듭니다.
   1.  완전하고 유효한 연락처 이름, 전화 번호, 이메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공합니다.

       > [!TIP]
       > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

   ![가져오기 작업 만들기 - 3단계](./media/storage-import-export-data-to-blobs/import-to-blob-5.png)

   **검토 + 만들기** 를 선택하여 계속 진행합니다.

7. 주문 요약에서:

   1. **약관** 을 검토한 다음, “입력한 모든 정보가 올바르며 사용 약관에 동의한다는 사실을 인지합니다.”를 선택합니다. 유효성 검사가 수행됩니다.
   1. 요약에 제공된 직업 정보를 검토합니다. 작업 이름과 디스크를 Azure로 반송할 Azure 데이터 센터 배송 주소를 적어 둡니다. 이 정보는 나중에 운송 레이블에 사용됩니다.
   1. **만들기** 를 선택합니다.

     ![가져오기 작업 만들기 - 4단계](./media/storage-import-export-data-to-blobs/import-to-blob-6.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 단계를 사용하여 Azure CLI에서 가져오기 작업을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>작업 만들기

1. [az extension add](/cli/azure/extension#az_extension_add) 명령을 사용하여 [az import-export](/cli/azure/import-export) 확장을 추가합니다.

    ```azurecli
    az extension add --name import-export
    ```

1. 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들려면 [az group create](/cli/azure/group#az_group_create) 명령을 실행합니다.

    ```azurecli
    az group create --name myierg --location "West US"
    ```

1. 기존 스토리지 계정을 사용하거나 새로 만들 수 있습니다. 스토리지 계정을 만들려면 [az storage account create](/cli/azure/storage/account#az_storage_account_create) 명령을 실행합니다.

    ```azurecli
    az storage account create --resource-group myierg --name myssdocsstorage --https-only
    ```

1. 디스크를 제공할 수 있는 위치 목록을 가져오려면 [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) 명령을 사용합니다.

    ```azurecli
    az import-export location list
    ```

1. [az import-export location show](/cli/azure/import-export/location#az_import_export_location_show) 명령을 사용하여 해당 지역의 위치를 가져옵니다.

    ```azurecli
    az import-export location show --location "West US"
    ```

1. 다음 [az import-export create](/cli/azure/import-export#az_import_export_create) 명령을 실행하여 가져오기 작업을 만듭니다.

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name MyIEjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --drive-list bit-locker-key=439675-460165-128202-905124-487224-524332-851649-442187 \
            drive-header-hash= drive-id=AZ31BGB1 manifest-file=\\DriveManifest.xml \
            manifest-hash=69512026C1E8D4401816A2E5B8D7420D \
        --type Import \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --return-shipping carrier-name=FedEx carrier-account-number=123456789 \
        --storage-account myssdocsstorage
    ```

   > [!TIP]
   > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

1. [az import-export list](/cli/azure/import-export#az_import_export_list) 명령을 사용하여 myierg 리소스 그룹에 대한 모든 작업을 확인합니다.

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. 작업을 업데이트하거나 취소하려면 [az import-export update](/cli/azure/import-export#az_import_export_update) 명령을 실행합니다.

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음 단계를 사용하여 Azure PowerShell에서 가져오기 작업을 만듭니다.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az.ImportExport** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>작업 만들기

1. 기존 리소스 그룹을 사용하거나 새 리소스 그룹을 만들 수 있습니다. 리소스 그룹을 만들려면 [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) cmdlet을 실행합니다.

   ```azurepowershell-interactive
   New-AzResourceGroup -Name myierg -Location westus
   ```

1. 기존 스토리지 계정을 사용하거나 새로 만들 수 있습니다. 스토리지 계정을 만들려면 [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) cmdlet을 실행합니다.

   ```azurepowershell-interactive
   New-AzStorageAccount -ResourceGroupName myierg -AccountName myssdocsstorage -SkuName Standard_RAGRS -Location westus -EnableHttpsTrafficOnly $true
   ```

1. 디스크를 제공할 수 있는 위치 목록을 가져오려면 [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) cmdlet을 사용합니다.

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. `Name` 매개 변수와 함께 `Get-AzImportExportLocation` cmdlet을 사용하여 해당 지역의 위치를 가져옵니다.

   ```azurepowershell-interactive
   Get-AzImportExportLocation -Name westus
   ```

1. 다음 [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) 예제를 실행하여 가져오기 작업을 만듭니다.

   ```azurepowershell-interactive
   $driveList = @(@{
     DriveId = '9CA995BA'
     BitLockerKey = '439675-460165-128202-905124-487224-524332-851649-442187'
     ManifestFile = '\\DriveManifest.xml'
     ManifestHash = '69512026C1E8D4401816A2E5B8D7420D'
     DriveHeaderHash = 'AZ31BGB1'
   })

   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'MyIEjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      DriveList = $driveList
      JobType = 'Import'
      LogLevel = 'Verbose'
      ShippingInformationRecipientName = 'Microsoft Azure Import/Export Service'
      ShippingInformationStreetAddress1 = '3020 Coronado'
      ShippingInformationCity = 'Santa Clara'
      ShippingInformationStateOrProvince = 'CA'
      ShippingInformationPostalCode = '98054'
      ShippingInformationCountryOrRegion = 'USA'
      ShippingInformationPhone = '4083527600'
      ReturnAddressRecipientName = 'Gus Poland'
      ReturnAddressStreetAddress1 = '1020 Enterprise way'
      ReturnAddressCity = 'Sunnyvale'
      ReturnAddressStateOrProvince = 'CA'
      ReturnAddressPostalCode = '94089'
      ReturnAddressCountryOrRegion = 'USA'
      ReturnAddressPhone = '4085555555'
      ReturnAddressEmail = 'gus@contoso.com'
      ReturnShippingCarrierName = 'FedEx'
      ReturnShippingCarrierAccountNumber = '123456789'
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

   > [!TIP]
   > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

1. [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) cmdlet을 사용하여 myierg 리소스 그룹에 대한 모든 작업을 볼 수 있습니다.

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. 작업을 업데이트하거나 취소하려면 [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) cmdlet을 실행합니다.

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-3-optional-configure-customer-managed-key"></a>3단계(선택 사항): 고객 관리형 키 구성

Microsoft 관리 키를 사용하여 드라이브에 대한 BitLocker 키를 보호하려는 경우 이 단계를 건너뛰고 다음 단계로 이동합니다. BitLocker 키를 보호하기 위해 자체 키를 구성하려면 [Azure Portal에서 Azure Import/Export에 대한 Azure Key Vault를 사용하여 고객 관리형 키 구성](storage-import-export-encryption-key-portal.md)의 지침을 따릅니다.

## <a name="step-4-ship-the-drives"></a>4단계: 드라이브 배송

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-5-update-the-job-with-tracking-information"></a>5단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-6-verify-data-upload-to-azure"></a>6단계: Azure에 대한 데이터 업로드 확인

완료될 때까지 작업을 추적합니다. 작업이 완료되면 데이터가 Azure에 업로드되었는지 확인합니다. 업로드가 성공했음을 확인한 후에만 온-프레미스 데이터를 삭제합니다. 자세한 내용은 [Import/Export 복사본 로그 검토](storage-import-export-tool-reviewing-job-status-v1.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
* [Import/Export 복사본 로그 검토](storage-import-export-tool-reviewing-job-status-v1.md)