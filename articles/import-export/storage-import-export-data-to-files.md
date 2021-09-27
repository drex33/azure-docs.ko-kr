---
title: Azure Import/Export를 사용하여 Azure Files로 데이터 전송 | Microsoft Docs
description: Azure Portal에서 가져오기 작업을 만들어 Azure Files로 데이터를 전송하는 방법에 대해 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 09/03/2021
ms.author: alkohli
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: 344d513f823c3eb04e869c66ca79bfb611c3eb6a
ms.sourcegitcommit: 10029520c69258ad4be29146ffc139ae62ccddc7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/27/2021
ms.locfileid: "129079754"
---
# <a name="use-azure-importexport-service-to-import-data-to-azure-files"></a>Azure Import/Export 서비스를 사용하여 Azure Files로 데이터 가져오기

이 아티클에서는 Azure Import/Export 서비스를 사용하여 Azure Files로 많은 양의 데이터를 안전하게 가져오는 방법에 대한 단계별 지침을 제공합니다. 데이터를 가져오려면 서비스를 사용하여 데이터가 포함된 지원되는 디스크 드라이브를 Azure 데이터 센터로 운송해야 합니다.

Import/Export 서비스는 Azure Storage로 Azure Files의 가져오기만을 지원합니다. Azure Files의 내보내기는 지원되지 않습니다.

## <a name="prerequisites"></a>사전 요구 사항

가져오기 작업을 만들어 Azure Files로 데이터를 전송하기 전에 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다. 다음이 필요합니다.

- Import/Export 서비스에 사용할 활성 Azure 구독이 있어야 합니다.
- Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 스토리지 계정 및 스토리지 유형](storage-import-export-requirements.md) 목록을 참조하세요. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](../storage/common/storage-account-create.md)(영문)을 참조하세요.
- [지원되는 형식](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다.
- [지원되는 OS 버전](storage-import-export-requirements.md#supported-operating-systems)을 실행하는 Windows 시스템이 있어야 합니다.
- Windows 시스템에서 [WAImportExport 버전 2를 다운로드](https://aka.ms/waiev2)합니다. `waimportexport` 기본 폴더에 압축을 풉니다. 예들 들어 `C:\WaImportExport`입니다.
- FedEx/DHL 계정이 있습니다. FedEx/DHL 이외의 운송업체를 사용하려면 `adbops@microsoft.com`에 있는 Azure Data Box 운영 팀에 문의하세요.
    - 계정은 유효해야 하고, 잔액이 있어야 하며, 반품 기능이 있어야 합니다.
    - 내보내기 작업의 추적 번호를 생성합니다.
    - 모든 작업에는 별도의 추적 번호가 있어야 합니다. 추적 번호가 동일한 여러 작업은 지원되지 않습니다.
    - 운송업체 계정이 없는 경우, 다음으로 이동합니다.
        - [FedEx 계정 만들기](https://www.fedex.com/en-us/create-account.html) 또는
        - [DHL 계정 만들기](http://www.dhl-usa.com/en/express/shipping/open_account.html).


## <a name="step-1-prepare-the-drives"></a>1단계: 드라이브 준비

이 단계에서는 업무 일지 파일을 생성합니다. 업무 일지 파일에는 드라이브 일련 번호, 암호화 키 및 스토리지 계정 세부 정보와 같은 기본 정보가 저장됩니다.

다음 단계를 수행하여 드라이브를 준비합니다.

1. SATA 커넥터를 통해 디스크 드라이브를 Windows 시스템에 연결합니다.
2. 각 드라이브에 단일 NTFS 볼륨을 만듭니다. 볼륨에 드라이브 문자를 할당합니다. 탑재 지점은 사용하지 마세요.
3. 도구가 있는 루트 폴더에서 *dataset.csv* 파일을 수정합니다. 파일 또는 폴더 중 하나 또는 둘 다를 가져올지에 따라 다음 예제와 비슷한 *dataset.csv* 파일에 항목을 추가합니다.

   - **파일을 가져오려면**: 다음 예에서 F: 드라이브에 복사할 데이터가 있습니다. *MyFile1.txt* 파일을 *MyAzureFileshare1* 의 루트에 복사합니다. *MyAzureFileshare1* 이 존재하지 않는 경우 Azure Storage 계정에 생성됩니다. 폴더 구조는 유지됩니다.

       ```
           BasePath,DstItemPathOrPrefix,ItemType,Disposition,MetadataFile,PropertiesFile
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None

       ```
   - **폴더를 가져오려면**: *MyFolder2* 아래의 모든 파일과 폴더가 fileshare에 반복적으로 복사됩니다. 폴더 구조는 유지됩니다.

       ```
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     가져온 폴더 또는 파일에 해당하는 같은 파일에 여러 항목을 만들 수 있습니다.

       ```
           "F:\MyFolder1\MyFile1.txt","MyAzureFileshare1/MyFile1.txt",file,rename,"None",None
           "F:\MyFolder2\","MyAzureFileshare1/",file,rename,"None",None

       ```
     [데이터 세트 CSV 파일 준비](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)에 대해 자세히 알아보세요.


4. 도구가 있는 루트 폴더에서 *driveset.csv* 파일을 수정합니다. 다음 예제와 비슷한 *driveset.csv* 파일에 항목을 추가합니다. 드라이브 집합 파일에는 디스크 및 해당하는 드라이브 문자 목록이 있으므로 도구는 준비해야 할 디스크 목록을 올바르게 선택할 수 있습니다.

    이 예제에서는 두 개의 디스크가 연결되어 있고 기본 NTFS 볼륨 G:\ 및 H:\가 생성되었다고 가정합니다. G:가 이미 암호화된 반면 H:\는 암호화되지 않았습니다. 도구는 H:\만을 호스트하는 디스크를 포맷하고 암호화합니다(G:\) 제외).

   - **암호화되지 않은 디스크의 경우**: *암호화* 를 지정하여 디스크에서 BitLocker 암호화를 사용합니다.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       H,Format,SilentMode,Encrypt,
       ```

   - **이미 암호화되어 있는 디스크의 경우**: *AlreadyEncrypted* 를 지정하고 BitLocker 키를 제공합니다.

       ```
       DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
       G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
       ```

     여러 드라이브에 해당하는 같은 파일에 여러 항목을 만들 수 있습니다. [드라이브 집합 CSV 파일 준비](/previous-versions/azure/storage/common/storage-import-export-tool-preparing-hard-drives-import)에 대해 자세히 알아보세요.

5. `PrepImport` 옵션을 사용하여 디스크 드라이브에 대한 데이터를 복사하고 준비합니다. 새 복사 세션을 사용하여 디렉터리 및/또는 파일을 복사하는 첫 번째 복사 세션의 경우 다음과 같은 명령을 실행합니다.

    ```cmd
    .\WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/silentmode] [/InitialDriveSet:<driveset.csv>]/DataSet:<dataset.csv>
    ```

   아래에 가져오기 예제가 나와 있습니다.

    ```cmd
    .\WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1 /InitialDriveSet:driveset.csv /DataSet:dataset.csv /logdir:C:\logs
    ```

6. 명령줄을 실행할 때마다 `/j:` 매개 변수와 함께 제공된 이름의 업무 일지 파일이 만들어집니다. 준비한 각 드라이브에는 가져오기 작업을 만들 때 업로드해야 하는 업무 일지 파일이 있습니다. 업무 일지 파일이 없는 드라이브는 처리되지 않습니다.

    > [!IMPORTANT]
    > 디스크 준비를 완료한 후 디스크 드라이브의 저널 파일 또는 데이터를 수정하지 말고 디스크를 다시 포맷하지 마세요.

추가 예제는 [업무 일지 파일에 대한 샘플](#samples-for-journal-files)로 이동합니다.

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

   1. 이전 [1단계: 드라이브 준비](#step-1-prepare-the-drives) 중에 만든 업무 일지 파일을 업로드합니다.
   1. 주문의 대상 Azure 지역을 선택합니다.
   1. 가져오기에 대한 스토리지 계정을 선택합니다.

      하차 위치는 선택한 스토리지 계정의 지역을 기반으로 자동으로 채워집니다.

   1. 자세한 정보 표시 로그를 저장하지 않으려면 **'waimportexport' Blob 컨테이너에 자세한 정보 표시 로그 저장** 옵션을 선택 취소합니다.


   ![가져오기 작업 만들기 - 2단계](./media/storage-import-export-data-to-blobs/import-to-blob-4.png)

   **다음: 배송 >** 을 선택하여 계속 진행합니다.

6. **배송** 에서:

    1. 드롭다운 목록에서 운송업체를 선택합니다. FedEx/DHL 이외의 운송업체를 사용하려면 드롭다운에서 기존 옵션을 선택합니다. 사용하려는 운송업체에 대한 정보를 사용하여 `adbops@microsoft.com`에서 Azure Data Box 운영 팀에 문의하세요.
    1. 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 가져오기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다.
    1. 완전하고 유효한 연락처 이름, 전화 번호, 이메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공합니다.

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
    az storage account create -resource-group myierg -name myssdocsstorage --https-only
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

## <a name="step-3-ship-the-drives-to-the-azure-datacenter"></a>3단계: Azure 데이터 센터에 드라이브 운송

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-4-update-the-job-with-tracking-information"></a>4단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-5-verify-data-upload-to-azure"></a>5단계: Azure에 대한 데이터 업로드 확인

완료될 때까지 작업을 추적합니다. 작업이 완료되면 데이터가 Azure에 업로드되었는지 확인합니다. 업로드가 성공했음을 확인한 후에만 온-프레미스 데이터를 삭제합니다.

## <a name="samples-for-journal-files"></a>업무 일지 파일에 대한 샘플

**드라이브를 추가** 하려면 새 드라이브 집합 파일을 만들고 아래 명령을 실행합니다.

*InitialDriveset .csv* 파일에 지정된 것과 다른 디스크 드라이브에 대한 후속 복사 세션의 경우 새 드라이브 집합 *.csv* 파일을 지정하고 `AdditionalDriveSet` 매개 변수에 값으로 제공합니다. **동일한 저널 파일** 이름을 사용하고 **새 세션 ID** 를 제공합니다. AdditionalDriveset CSV 파일의 형식은 InitialDriveSet 형식과 같습니다.

```cmd
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AdditionalDriveSet:<driveset.csv>
```

아래에 가져오기 예제가 나와 있습니다.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#3  /AdditionalDriveSet:driveset-2.csv
```


추가 데이터를 동일한 드라이브 집합에 추가하려면 후속 복사 세션에 PrepImport 명령을 사용하여 추가 파일/디렉터리를 복사합니다.

*InitialDriveset.csv* 파일에서 지정된 동일한 하드 디스크 드라이브에 대한 후속 복사 세션의 경우 **동일한 업무 일지 파일** 이름을 지정하고, **새 세션 ID** 를 제공합니다. 스토리지 계정 키를 제공하지 않아도 됩니다.

```cmd
WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] DataSet:<dataset.csv>
```

아래에 가져오기 예제가 나와 있습니다.

```cmd
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

## <a name="next-steps"></a>다음 단계

* [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
* [Import/Export 요구 사항 검토](storage-import-export-requirements.md)
