---
title: Azure Import/Export를 사용하여 Azure Blob 스토리지에서 데이터를 내보내기 위한 자습서 | Microsoft Docs
description: Azure Portal에서 내보내기 작업을 만들어 Azure Blob에서 데이터를 전송하는 방법을 알아봅니다.
author: alkohli
services: storage
ms.service: storage
ms.topic: tutorial
ms.date: 10/01/2021
ms.author: alkohli
ms.subservice: common
ms.custom: tutorial, devx-track-azurepowershell, devx-track-azurecli, contperf-fy21q3
ms.openlocfilehash: ccda14f4046efe32370b206577807dca558d9f25
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129709656"
---
# <a name="tutorial-export-data-from-azure-blob-storage-with-azure-importexport"></a>자습서: Azure Import/Export를 사용하여 Azure Blob 스토리지에서 데이터 내보내기

이 문서에서는 Azure Import/Export 서비스를 사용하여 Azure Blob Storage에서 많은 양의 데이터를 안전하게 내보내는 방법에 대한 단계별 지침을 제공합니다. 서비스를 사용하려면 빈 드라이브를 Azure 데이터 센터에 배송해야 합니다. 서비스에서 스토리지 계정의 데이터를 드라이브로 내보낸 다음, 드라이브를 다시 배송합니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * Azure Import/Export를 사용하여 Azure Blob 스토리지에서 데이터를 내보내기 위한 필수 조건
> * 1단계: 내보내기 작업 만들기
> * 2단계: 드라이브 배송
> * 3단계: 추적 정보를 사용하여 작업 업데이트
> * 4단계: 디스크 받기
> * 5단계: 디스크 잠금 해제

## <a name="prerequisites"></a>사전 요구 사항

내보내기 작업을 만들어 Azure Blob Storage에서 데이터를 전송하기 전에 이 서비스에 대한 다음 필수 조건 목록을 신중하게 검토하고 완료해야 합니다.
다음이 필요합니다.

- Import/Export 서비스에 사용할 수 있는 활성 Azure 구독이 있어야 합니다.
- Azure Storage 계정이 하나 이상 있어야 합니다. [Import/Export 서비스에 지원되는 스토리지 계정 및 스토리지 유형](storage-import-export-requirements.md) 목록을 참조하세요. 새 Storage 계정 만들기에 대한 자세한 내용은 [Storage 계정을 만드는 방법](../storage/common/storage-account-create.md)(영문)을 참조하세요.
- [지원되는 형식](storage-import-export-requirements.md#supported-disks)에 속한 적절한 개수의 디스크가 있어야 합니다. Azure Import/Export 도구를 사용하여 제공할 디스크 수를 결정할 수 있습니다. 단계는 [사용할 드라이브 결정](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need)을 참조하세요.
- FedEx/DHL 계정이 있습니다. FedEx/DHL 이외의 운송업체를 사용하려면 `adbops@microsoft.com`에 있는 Azure Data Box 운영 팀에 문의하세요.
  - 계정은 유효해야 하고, 잔액이 있어야 하며, 반품 기능이 있어야 합니다.
  - 내보내기 작업의 추적 번호를 생성합니다.
  - 모든 작업에는 별도의 추적 번호가 있어야 합니다. 추적 번호가 동일한 여러 작업은 지원되지 않습니다.
  - 운송업체 계정이 없는 경우, 다음으로 이동합니다.
    - [FedEx 계정 만들기](https://www.fedex.com/en-us/create-account.html) 또는
    - [DHL 계정 만들기](http://www.dhl-usa.com/en/express/shipping/open_account.html).

## <a name="step-1-create-an-export-job"></a>1단계: 내보내기 작업 만들기

### <a name="portal"></a>[포털](#tab/azure-portal)

다음 단계를 수행하여 Azure Portal에서 내보내기 작업을 만듭니다.

1. <https://portal.azure.com/>에 로그온합니다.
2. **가져오기/내보내기 작업** 을 검색합니다.

    ![가져오기/내보내기 작업 검색](./media/storage-import-export-data-to-blobs/import-to-blob-1.png)

3. **+새로 만들기** 를 선택합니다.

    ![\+ 새로 만들기를 선택하여 새로 만듭니다. ](./media/storage-import-export-data-to-blobs/import-to-blob-2.png)

4. **기본 사항** 에서

   1. 구독을 선택합니다.
   1. 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하고 새 리소스 그룹을 만듭니다.
   1. 가져오기 작업을 설명하는 이름을 입력합니다. 이름을 사용하여 작업 진행 상황을 추적합니다.
       * 이름에는 소문자, 숫자 및 하이픈만 포함할 수 있습니다.
       * 이름은 문자로 시작해야 하며, 공백은 포함할 수 없습니다.

   1. **Azure에서 내보내기** 를 선택합니다.

    ![내보내기 주문의 기본 옵션](./media/storage-import-export-data-from-blobs/export-from-blob-3.png)

    **다음: 작업 세부 정보 >** 를 선택하여 계속 진행합니다.

5. **작업 세부 정보** 에서:

   1. 현재 데이터가 있는 Azure 지역을 선택합니다.
   1. 데이터를 가져올 스토리지 계정을 선택합니다. 사용자의 위치에 가까운 스토리지 계정을 사용합니다.

      자동 전송 위치는 선택한 스토리지 계정의 지역에 따라 자동으로 채워집니다.

   1. 스토리지 계정에서 빈 드라이브로 내보낼 Blob 데이터를 지정합니다. 다음 세 가지 방법 중 하나를 선택합니다.

      - 스토리지 계정의 Blob 데이터를 모두 내보내도록 선택합니다(**모두 내보내기**).

        ![모두 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob-4.png)

      - **선택한 컨테이너 및 Blob** 을 선택하고 내보낼 컨테이너와 Blob을 지정합니다. 여러 선택 방법 중 둘 이상을 사용할 수 있습니다. **추가** 옵션을 선택하면 오른쪽에 선택 문자열을 추가할 수 있는 패널이 열립니다.

        |옵션|Description|
        |------|-----------|      
        |**컨테이너 추가**|컨테이너의 모든 Blob을 내보냅니다.<br>**컨테이너 추가** 를 선택하고 각 컨테이너 이름을 입력합니다.|
        |**Blob 추가**|내보낼 개별 Blob을 지정합니다.<br>**Blob 추가** 를 선택합니다. 그런 다음 컨테이너 이름으로 시작하는 Blob에 대한 상대 경로를 지정합니다. 루트 컨테이너를 지정하려면 *$root* 를 사용합니다.<br>처리 중에 오류가 발생하지 않도록 방지하려면 다음 스크린샷과 같이 유효한 형식의 Blob 경로를 제공해야 합니다. 자세한 내용은 [유효한 Blob 경로의 예](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths)를 참조하세요.|
        |**접두사 추가**|접두사를 사용하여 유사한 이름의 컨테이너 집합 또는 컨테이너의 유사한 이름의 Blob 집합을 선택합니다. 접두사는 컨테이너 이름의 접두사, 완전한 컨테이너 이름 또는 Blob 이름 접두사가 뒤에 오는 완전한 컨테이너 이름일 수 있습니다. |

        ![선택한 컨테이너 및 Blob 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob-5.png)

    - **Blob 목록 파일(XML 형식)에서 내보내기** 를 선택하고 스토리지 계정에서 내보낼 Blob의 경로 및 접두사 목록이 포함된 XML 파일을 선택합니다. XML 파일을 생성하고 스토리지 계정에 대한 컨테이너에 저장해야 합니다. 파일을 비워 둘 수 없습니다.

      > [!IMPORTANT]
      > XML 파일을 사용하여 내보낼 Blob을 선택하는 경우 XML에 유효한 경로 및/또는 접두사가 포함되어 있는지 확인합니다. 파일이 잘못되었거나 지정된 경로와 일치하는 데이터가 없는 경우에는 주문이 부분 데이터로 종료되거나 데이터를 내보내지 않습니다.

       컨테이너에 XML 파일을 추가하는 방법에 대한 자세한 내용은 [XML 파일을 사용하여 주문 내보내기](../databox/data-box-deploy-export-ordered.md#export-order-using-xml-file)를 참조하세요.

      ![Blob 목록 파일에서 내보내기](./media/storage-import-export-data-from-blobs/export-from-blob-6.png)

   > [!NOTE]
   > 내보낼 Blob이 데이터 복사 중에 사용되는 경우 Azure Import/Export 서비스는 Blob의 스냅샷을 가져와 해당 스냅샷을 복사합니다.

   **다음: 배송 >** 을 선택하여 계속 진행합니다.

6. **배송** 에서:

    - 드롭다운 목록에서 운송업체를 선택합니다. FedEx/DHL 이외의 운송업체를 사용하려면 드롭다운에서 기존 옵션을 선택합니다. 사용하려는 운송업체에 대한 정보를 사용하여 `adbops@microsoft.com`에서 Azure Data Box 운영 팀에 문의하세요.
    - 운송업체에서 만든 유효한 운송업체 계정 번호를 입력합니다. 내보내기 작업이 완료되면 Microsoft는 이 계정을 사용하여 사용자에게 드라이브를 배송합니다.
    - 완전하고 유효한 연락처 이름, 전화 번호, 이메일, 주소, 구/군/시, 우편 번호, 시/도 및 국가/지역을 제공합니다.

        > [!TIP]
        > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

    **검토 + 만들기** 를 선택하여 계속 진행합니다.

7. **검토 + 만들기** 에서:

   1. 작업에 대한 세부 정보를 검토합니다.
   1. 작업 이름과 Azure로의 배송 디스크에 제공되는 Azure 데이터 센터 배송 주소를 기록합니다.

      > [!NOTE]
      > 항상 Azure Portal에 기록된 데이터 센터로 디스크를 보냅니다. 디스크가 잘못된 데이터 센터로 배송되면 작업이 처리되지 않습니다.

   1. 개인 정보 및 원본 데이터 삭제에 대한 주문 **약관** 을 검토합니다. 약관에 동의하면 약관 아래의 확인란을 선택합니다. 주문에 대한 유효성 검사가 시작됩니다.

   ![내보내기 주문을 검토하고 만듭니다.](./media/storage-import-export-data-from-blobs/export-from-blob-6-a.png)

 1. 유효성 검사를 통과하면 **만들기** 를 선택합니다.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

다음 단계를 사용하여 Azure Portal에서 내보내기 작업을 만듭니다.

[!INCLUDE [azure-cli-prepare-your-environment-h3.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

### <a name="create-a-job"></a>작업 만들기

1. [az extension add](/cli/azure/extension#az_extension_add) 명령을 사용하여 [az import-export](/cli/azure/import-export) 확장을 추가합니다.

    ```azurecli
    az extension add --name import-export
    ```

1. 디스크를 받을 수 있는 위치 목록을 가져오려면 [az import-export location list](/cli/azure/import-export/location#az_import_export_location_list) 명령을 사용합니다.

    ```azurecli
    az import-export location list
    ```

1. 다음 [az import-export create](/cli/azure/import-export#az_import_export_create) 명령을 실행하여 기존 스토리지 계정을 사용하는 내보내기 작업을 만듭니다.

    ```azurecli
    az import-export create \
        --resource-group myierg \
        --name Myexportjob1 \
        --location "West US" \
        --backup-drive-manifest true \
        --diagnostics-path waimportexport \
        --export blob-path=/ \
        --type Export \
        --log-level Verbose \
        --shipping-information recipient-name="Microsoft Azure Import/Export Service" \
            street-address1="3020 Coronado" city="Santa Clara" state-or-province=CA postal-code=98054 \
            country-or-region=USA phone=4083527600 \
        --return-address recipient-name="Gus Poland" street-address1="1020 Enterprise way" \
            city=Sunnyvale country-or-region=USA state-or-province=CA postal-code=94089 \
            email=gus@contoso.com phone=4085555555" \
        --storage-account myssdocsstorage
    ```

    > [!TIP]
    > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

   이 작업은 스토리지 계정의 모든 Blob을 내보냅니다. **--export** 에 대해 이 값을 바꿔서 내보낼 Blob을 지정할 수 있습니다.

    ```azurecli
    --export blob-path=$root/logo.bmp
    ```

   이 매개 변수 값은 루트 컨테이너의 *logo.bmp* 라는 Blob을 내보냅니다.

   또한 접두사를 사용하여 컨테이너의 모든 Blob을 선택하는 옵션도 있습니다. **--export** 에 대해 이 값을 바꿉니다.

    ```azurecli
    blob-path-prefix=/myiecontainer
    ```

   자세한 내용은 [유효한 Blob 경로의 예](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths)를 참조하세요.

   > [!NOTE]
   > 내보낼 Blob가 데이터 복사 중에 사용되는 경우 Azure Import/Export 서비스는 Blob의 스냅샷을 가져와 해당 스냅샷을 복사합니다.

1. [az import-export list](/cli/azure/import-export#az_import_export_list) 명령을 사용하여 myierg 리소스 그룹에 대한 모든 작업을 확인합니다.

    ```azurecli
    az import-export list --resource-group myierg
    ```

1. 작업을 업데이트하거나 취소하려면 [az import-export update](/cli/azure/import-export#az_import_export_update) 명령을 실행합니다.

    ```azurecli
    az import-export update --resource-group myierg --name MyIEjob1 --cancel-requested true
    ```

### <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

다음 단계를 사용하여 Azure PowerShell에서 내보내기 작업을 만듭니다.

[!INCLUDE [azure-powershell-requirements-h3.md](../../includes/azure-powershell-requirements-h3.md)]

> [!IMPORTANT]
> **Az.ImportExport** PowerShell 모듈이 미리 보기로 제공되는 동안 `Install-Module` cmdlet을 사용하여 별도로 설치해야 합니다. 이 PowerShell 모듈이 일반 공급되면 이후 Az PowerShell 모듈 릴리스의 일부가 되며 기본적으로 Azure Cloud Shell 내에서 사용할 수 있습니다.

```azurepowershell-interactive
Install-Module -Name Az.ImportExport
```

### <a name="create-a-job"></a>작업 만들기

1. 디스크를 받을 수 있는 위치 목록을 가져오려면 [Get-AzImportExportLocation](/powershell/module/az.importexport/get-azimportexportlocation) cmdlet을 사용합니다.

   ```azurepowershell-interactive
   Get-AzImportExportLocation
   ```

1. 다음 [New-AzImportExport](/powershell/module/az.importexport/new-azimportexport) 예를 실행하여 기존 스토리지 계정을 사용하는 내보내기 작업을 만듭니다.

   ```azurepowershell-interactive
   $Params = @{
      ResourceGroupName = 'myierg'
      Name = 'Myexportjob1'
      Location = 'westus'
      BackupDriveManifest = $true
      DiagnosticsPath = 'waimportexport'
      ExportBlobListblobPath = '\'
      JobType = 'Export'
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
      StorageAccountId = '/subscriptions/<SubscriptionId>/resourceGroups/myierg/providers/Microsoft.Storage/storageAccounts/myssdocsstorage'
   }
   New-AzImportExport @Params
   ```

    > [!TIP]
    > 단일 사용자의 메일 주소를 지정하는 대신 그룹 메일을 제공합니다. 이렇게 하면 관리자가 자리를 비운 경우에도 알림을 받을 수 있습니다.

   이 작업은 스토리지 계정의 모든 Blob을 내보냅니다. **-ExportBlobListblobPath** 에 대해 이 값을 바꿔서 내보낼 Blob을 지정할 수 있습니다.

   ```azurepowershell-interactive
   -ExportBlobListblobPath $root\logo.bmp
   ```

   이 매개 변수 값은 루트 컨테이너의 *logo.bmp* 라는 Blob을 내보냅니다.

   또한 접두사를 사용하여 컨테이너의 모든 Blob을 선택하는 옵션도 있습니다. **-ExportBlobListblobPath** 에 대해 이 값을 바꿉니다.

   ```azurepowershell-interactive
   -ExportBlobListblobPath '/myiecontainer'
   ```

   자세한 내용은 [유효한 Blob 경로의 예](storage-import-export-determine-drives-for-export.md#examples-of-valid-blob-paths)를 참조하세요.

   > [!NOTE]
   > 내보낼 Blob가 데이터 복사 중에 사용되는 경우 Azure Import/Export 서비스는 Blob의 스냅샷을 가져와 해당 스냅샷을 복사합니다.

1. [Get-AzImportExport](/powershell/module/az.importexport/get-azimportexport) cmdlet을 사용하여 myierg 리소스 그룹에 대한 모든 작업을 볼 수 있습니다.

   ```azurepowershell-interactive
   Get-AzImportExport -ResourceGroupName myierg
   ```

1. 작업을 업데이트하거나 취소하려면 [Update-AzImportExport](/powershell/module/az.importexport/update-azimportexport) cmdlet을 실행합니다.

   ```azurepowershell-interactive
   Update-AzImportExport -Name MyIEjob1 -ResourceGroupName myierg -CancelRequested
   ```

---

## <a name="step-2-ship-the-drives"></a>2단계: 드라이브 배송

필요한 드라이브 수를 모르는 경우 [필요한 드라이브 수 결정](storage-import-export-determine-drives-for-export.md#determine-how-many-drives-you-need)을 참조하세요. 드라이브 수를 알고 있으면 드라이브 배송을 계속 진행합니다.

[!INCLUDE [storage-import-export-ship-drives](../../includes/storage-import-export-ship-drives.md)]

## <a name="step-3-update-the-job-with-tracking-information"></a>3단계: 추적 정보를 사용하여 작업 업데이트

[!INCLUDE [storage-import-export-update-job-tracking](../../includes/storage-import-export-update-job-tracking.md)]

## <a name="step-4-receive-the-disks"></a>4단계: 디스크 받기

대시보드에서 작업이 완료되었다고 보고하면 디스크가 배송되고 포털에서 배송 추적 번호를 사용할 수 있습니다.

1. 내보낸 데이터가 있는 드라이브를 받은 후에는 BitLocker 키를 가져와서 드라이브의 잠금을 해제해야 합니다. Azure Portal에서 내보내기 작업으로 이동합니다. **가져오기/내보내기** 탭을 클릭합니다.
2. 목록에서 내보내기 작업을 선택하고 클릭합니다. **암호화** 로 이동하여 키를 복사합니다.

   ![내보내기 작업의 BitLocker 키 보기](./media/storage-import-export-data-from-blobs/export-from-blob-7.png)

3. BitLocker 키를 사용하여 디스크의 잠금을 해제합니다.

내보내기가 완료되었습니다.

## <a name="step-5-unlock-the-disks"></a>5단계: 디스크 잠금 해제

다음 명령을 사용하여 드라이브 잠금을 해제합니다.

   `WAImportExport Unlock /bk:<BitLocker key (base 64 string) copied from Encryption blade in Azure portal> /driveLetter:<Drive letter>`

다음은 샘플 입력의 예입니다.

   `WAImportExport.exe Unlock /bk:CAAcwBoAG8AdQBsAGQAIABiAGUAIABoAGkAZABkAGUAbgA= /driveLetter:e`

이때 작업을 삭제하거나 유지할 수 있습니다. 작업은 90일 후 자동으로 삭제됩니다.

## <a name="next-steps"></a>다음 단계

* [작업 및 드라이브 상태 보기](storage-import-export-view-drive-status.md)
* [Import/Export 복사본 로그 검토](storage-import-export-tool-reviewing-job-status-v1.md)
