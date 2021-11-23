---
title: Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드 | Microsoft Docs
description: 리소스 관리자 템플릿을 사용 하 여 Azure Blob storage에서 Data Lake Storage로 업그레이드 하는 방법을 보여 줍니다.
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 10/12/2021
ms.author: normesta
ms.openlocfilehash: faf1147dd61d1fe436328a95780a8bdcf04cb6ff
ms.sourcegitcommit: 01b678462a4a390c30463c525432ffbbbe0195cf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132957073"
---
#  <a name="upgrade-azure-blob-storage-with-azure-data-lake-storage-gen2-capabilities"></a>Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드

이 문서는 파일 및 디렉터리 수준 보안과 같은 기능의 잠금을 해제 하는 데 도움이 됩니다. 이러한 기능은 빅 데이터 분석 워크 로드에서 널리 사용 되며 Azure Data Lake Storage Gen2로 통칭 됩니다. 

이러한 기능에 대해 자세히 알아보고 작업, 응용 프로그램, 비용, 서비스 통합, 도구, 기능 및 설명서에 대 한이 업그레이드의 영향을 평가 하려면 [Azure Data Lake Storage Gen2 기능으로 Azure Blob Storage 업그레이드](upgrade-to-data-lake-storage-gen2.md)를 참조 하세요.

> [!IMPORTANT]
> 업그레이드는 단방향입니다. 업그레이드를 수행한 후에는 계정을 되돌릴 방법이 없습니다. 비프로덕션 환경에서 업그레이드의 유효성을 검사 하는 것이 좋습니다.

## <a name="review-feature-support"></a>기능 지원 검토

계정이 사용 하도록 설정 된 Data Lake Storage Gen2 계정에서 아직 지원 되지 않는 기능을 사용 하도록 구성 되어 있을 수 있습니다. 계정에서 아직 지원 되지 않는 기능을 사용 하는 경우 업그레이드는 유효성 검사 단계를 통과 하지 않습니다. 

[Azure Storage 계정에서 Blob Storage 기능 지원](storage-feature-support-in-storage-accounts.md) 문서를 검토 하 여 지원 되지 않는 기능을 식별 합니다. 계정에서 지원 되지 않는 기능을 사용 하는 경우 업그레이드를 시작 하기 전에 사용 하지 않도록 설정 해야 합니다.

## <a name="perform-the-upgrade"></a>업그레이드 수행

### <a name="portal"></a>[포털](#tab/azure-portal)

1. [Azure Portal](https://portal.azure.com/)에 로그인하여 시작합니다.

2. 스토리지 계정을 찾아 계정 개요를 표시합니다.

3. **Data Lake Gen2 마이그레이션** 을 선택 합니다.

   **Azure Data Lake Gen2 기능 구성을 사용 하 여 Storage 계정으로 업그레이드** 페이지가 나타납니다.

   > [!div class="mx-imgBorder"]
   > ![구성 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-page.png)

4. **1 단계: 업그레이드 전에 계정 변경 내용 검토** 섹션을 확장 하 고 **검토 및 변경 내용에 동의를** 클릭 합니다.

5. **계정 변경 내용 검토** 페이지에서 확인란을 선택 하 고 **변경 내용에 동의를** 클릭 합니다.

6. **Step 2: 업그레이드 하기 전에 계정 유효성 검사** 섹션을 확장 하 고 **유효성 검사 시작** 을 클릭 합니다.

   유효성 검사에 실패 하면 페이지에 오류가 표시 됩니다. **오류 보기** 링크가 표시 되는 경우도 있습니다. 해당 링크가 표시 되 면 선택 합니다. 

   > [!div class="mx-imgBorder"]
   > ![오류 보기 링크](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-errors.png)

   그런 다음, **오류 json** 파일의 상황에 맞는 메뉴에서 **다운로드** 를 선택 합니다.

   > [!div class="mx-imgBorder"]
   > ![오류 json 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/error-json.png)

   다운로드 한 파일을 열어 계정이 유효성 검사 단계를 통과 하지 못한 이유를 확인 합니다. 

   다음 JSON은 계정에서 호환 되지 않는 기능을 사용할 수 있음을 나타냅니다. 이 경우 기능을 사용 하지 않도록 설정한 다음 유효성 검사 프로세스를 다시 시작 합니다.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

7. 계정이 성공적으로 확인 되 면 **3 단계: 업그레이드 계정** 섹션을 확장 하 고 **업그레이드 시작** 을 클릭 합니다.

   > [!IMPORTANT]
   > 계정을 업그레이드 하는 동안에는 쓰기 작업을 사용할 수 없습니다. 읽기 작업은 사용 하지 않도록 설정 되어 있지만 업그레이드 프로세스를 불안정 하 게 할 수 있으므로 읽기 작업을 일시 중단 하는 것이 좋습니다.

   마이그레이션이 성공적으로 완료 되 면 다음과 유사한 메시지가 나타납니다. 

   > [!div class="mx-imgBorder"]
   > ![마이그레이션 완료 페이지](./media/upgrade-to-data-lake-storage-gen2-how-to/upgrade-to-an-azure-data-lake-gen2-account-completed.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Windows PowerShell 명령 창을 엽니다.

2. 최신 Azure PowerShell 모듈이 있는지 확인합니다. [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요.

3. `Connect-AzAccount` 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다.

   ```powershell
   Connect-AzAccount
   ```

4. ID가 둘 이상의 구독과 연결되어 있으면 활성 구독을 설정합니다.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

6. 다음 명령을 사용 하 여 저장소 계정의 유효성을 검사 합니다.

   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Validation -AsJob
   ```

   - `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   계정 크기에 따라이 프로세스에 다소 시간이 걸릴 수 있습니다. 스위치를 사용 `asJob` 하 여 클라이언트가 차단 되지 않도록 백그라운드 작업에서 명령을 실행할 수 있습니다. 명령은 원격으로 실행 되지만 작업은 로컬 컴퓨터 또는 명령을 실행 하는 VM에 있습니다. 결과는 로컬 컴퓨터 또는 VM에 전송 됩니다.

7. 작업 상태를 확인 하 고 작업의 모든 속성을 목록으로 표시 하려면 반환 변수를 cmdlet으로 파이프 합니다 `Format-List` . 

   ```powershell
   $result | Format-List -Property *
   ```

   유효성 검사가 성공 하면 **State** 속성이 **Completed** 로 설정 됩니다.

   유효성 검사에 실패 하면 **State** 속성이 **Failed** 로 설정 되 고 **Error** 속성에 유효성 검사 오류가 표시 됩니다. 

   다음 출력은 계정에서 호환 되지 않는 기능을 사용할 수 있음을 나타냅니다. 이 경우 기능을 사용 하지 않도록 설정한 다음 유효성 검사 프로세스를 다시 시작 합니다.

   > [!div class="mx-imgBorder"]
   > ![유효성 검사 오류](./media/upgrade-to-data-lake-storage-gen2-how-to/validation-error-powershell.png)

   경우에 따라 **error** 속성은 이름이 file 인 파일에 대 한 경로를 제공 **합니다.** 해당 파일을 열어 계정에서 유효성 검사 단계를 통과 하지 못한 이유를 확인할 수 있습니다. 

   다음 JSON은 계정에서 호환 되지 않는 기능을 사용할 수 있음을 나타냅니다. 이 경우 기능을 사용 하지 않도록 설정한 다음 유효성 검사 프로세스를 다시 시작 합니다.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

8. 계정이 성공적으로 확인 되 면 다음 명령을 실행 하 여 업그레이드를 시작 합니다.
   
   ```powershell
   $result = Invoke-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName "<resource-group-name>" -Name "<storage-account-name>" -RequestType Upgrade -AsJob -Force
   ```

   위의 유효성 검사 예제와 같이이 예제에서는 스위치를 사용 `asJob` 하 여 백그라운드 작업에서 명령을 실행 합니다. `Force`스위치는 업그레이드를 확인 하는 메시지를 재정의 합니다.  스위치를 사용 하지 않는 경우 `AsJob` `Force` 프롬프트에 응답할 수 있으므로 스위치를 사용할 필요가 없습니다.

   > [!IMPORTANT]
   > 계정을 업그레이드 하는 동안에는 쓰기 작업을 사용할 수 없습니다. 읽기 작업은 사용 하지 않도록 설정 되어 있지만 업그레이드 프로세스를 불안정 하 게 할 수 있으므로 읽기 작업을 일시 중단 하는 것이 좋습니다.

   작업 상태를 확인 하려면 이전 단계에서 설명한 것과 동일한 방법을 사용 합니다. 프로세스가 실행 되 면 **State** 속성은 **실행 중** 으로 설정 됩니다.

   마이그레이션이 성공적으로 완료 되 면 **State** 속성이 **completed** 로 설정 되 고 **Error** 속성에 오류가 표시 되지 않습니다.
   
   > [!div class="mx-imgBorder"]
   > ![성공적으로 완료 한 출력](./media/upgrade-to-data-lake-storage-gen2-how-to/success-message-powershell.png)


### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. 먼저 [Azure Cloud Shell](../../cloud-shell/overview.md)을 열거나 Azure CLI를 로컬로 [설치](/cli/azure/install-azure-cli)한 경우 Windows PowerShell과 같은 명령 콘솔 애플리케이션을 엽니다.

2. 다음 명령을 사용하여 설치된 Azure CLI 버전이 `2.29.0` 이상인지 확인합니다.

   ```azurecli
    az --version
   ```

   Azure CLI 버전이 보다 낮은 경우 `2.29.0` 최신 버전을 설치 합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

2. ID가 둘 이상의 구독과 연결되어 있으면 활성 구독을 설정합니다.

   ```azurecli
      az account set --subscription <subscription-id>
   ```

   `<subscription-id>` 자리 표시자 값을 구독의 ID로 바꿉니다.

3. 다음 명령을 사용 하 여 저장소 계정의 유효성을 검사 합니다.

   ```azurecli
   az storage account hns-migration start --type validation -n <storage-account-name> -g <resource-group-name>
   ```

   - `<resource-group-name>` 자리 표시자 값을 리소스 그룹의 이름으로 바꿉니다.

   - `<storage-account-name>` 자리 표시자 값을 스토리지 계정 이름으로 바꿉니다.

   유효성 검사에 성공 하면 프로세스가 완료 되 고 오류가 표시 되지 않습니다.
   
   유효성 검사에 실패 하면 유효성 검사 오류가 콘솔에 표시 됩니다. 예를 들어이 오류는 `(IncompatibleValuesForAccountProperties) Values for account properties are incompatible: Versioning Enabled` 계정에 호환 되지 않는 기능 (버전 관리)이 사용 되 고 있음을 나타냅니다. 이 경우 기능을 사용 하지 않도록 설정한 다음 유효성 검사 프로세스를 다시 시작 합니다.

   경우에 따라 **오류. json** 파일에 대 한 경로가 콘솔에 표시 됩니다. 해당 파일을 열어 계정에서 유효성 검사 단계를 통과 하지 못한 이유를 확인할 수 있습니다. 

   다음 JSON은 계정에서 호환 되지 않는 기능을 사용할 수 있음을 나타냅니다. 이 경우 기능을 사용 하지 않도록 설정한 다음 유효성 검사 프로세스를 다시 시작 합니다.

   ```json
   {
    "startTime": "2021-08-04T18:40:31.8465320Z",
    "id": "45c84a6d-6746-4142-8130-5ae9cfe013a0",
    "incompatibleFeatures": [
        "Blob Delete Retention Enabled"
    ],
    "blobValidationErrors": [],
    "scannedBlobCount": 0,
    "invalidBlobCount": 0,
    "endTime": "2021-08-04T18:40:34.9371480Z"
   }
   ```

5. 계정이 성공적으로 확인 되 면 다음 명령을 실행 하 여 업그레이드를 시작 합니다.
   
   ```azurecli
   az storage account hns-migration start --type upgrade -n storage-account-name -g <resource-group-name>
   ```

   > [!IMPORTANT]
   > 계정을 업그레이드 하는 동안에는 쓰기 작업을 사용할 수 없습니다. 읽기 작업은 사용 하지 않도록 설정 되어 있지만 업그레이드 프로세스를 불안정 하 게 할 수 있으므로 읽기 작업을 일시 중단 하는 것이 좋습니다.

   마이그레이션이 성공 하면 프로세스가 완료 되 고 오류가 표시 되지 않습니다.

---

## <a name="stop-the-upgrade"></a>업그레이드 중지

마이그레이션을 완료 하기 전에 중지할 수 있습니다.

### <a name="portal"></a>[포털](#tab/azure-portal)

작업이 완료 되기 전에 업그레이드를 중지 하려면 업그레이드가 진행 되는 동안 **업그레이드 취소** 를 선택 합니다.

> [!div class="mx-imgBorder"]
> ![업그레이드 취소](./media/upgrade-to-data-lake-storage-gen2-how-to/cancel-the-upgrade.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

업그레이드가 완료되기 전에 중지하려면 `Stop-AzStorageAccountHierarchicalNamespaceUpgrade` 명령을 사용합니다.

```powershell
Stop-AzStorageAccountHierarchicalNamespaceUpgrade -ResourceGroupName <resource-group-name> -Name <storage-account-name>
```

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

업그레이드가 완료되기 전에 중지하려면 `az storage account hns-migration stop` 명령을 사용합니다.

```azurecli
az storage account hns-migration stop -n <storage-account-name> -g <resource-group-name>
```

---


## <a name="migrate-data-workloads-and-applications"></a>데이터, 워크로드 및 애플리케이션 마이그레이션 

1. **Blob service** 엔드포인트 또는 **Data Lake Storage** 엔드포인트를 가리키도록 [워크로드에서 서비스를](./data-lake-storage-supported-azure-services.md) 구성합니다.

   > [!div class="mx-imgBorder"]
   > ![계정 엔드포인트](./media/upgrade-to-data-lake-storage-gen2-how-to/storage-endpoints.png)
  
3. Windows Azure Storage Blob 드라이버 또는 [WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) 드라이버를 사용하는 Hadoop 워크로드의 경우 [ABFS(Azure Blob File System)](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html) 드라이버를 사용하도록 수정해야 합니다. **Blob service** 엔드포인트를 요청하는 WASB 드라이버와 달리 ABFS 드라이버는 계정의 Data Lake **Storage** 엔드포인트를 요청합니다.

2. 사용자 지정 애플리케이션을 테스트하여 업그레이드된 계정에서 예상대로 작동하는지 확인합니다. 

   [Data Lake Storage 대한 다중 프로토콜 액세스를](data-lake-storage-multi-protocol-access.md) 통해 대부분의 애플리케이션은 수정 없이 Blob API를 계속 사용할 수 있습니다. 문제가 발생하거나 API를 사용하여 디렉터리 작업 및 ACL을 사용하려는 경우 Data Lake Storage Gen2 API를 사용하도록 일부 코드를 이동하는 것이 좋습니다. [.NET,](data-lake-storage-directory-file-acl-dotnet.md) [Java,](data-lake-storage-directory-file-acl-java.md) [Python,](data-lake-storage-directory-file-acl-python.md) [Node.js](data-lake-storage-acl-javascript.md)및 [REST에](/rest/api/storageservices/data-lake-storage-gen2)대한 가이드를 참조하세요. 

3. 사용자 지정 스크립트를 테스트하여 업그레이드된 계정에서 예상대로 작동하는지 확인합니다. 

   Blob API의 경우와 마찬가지로 대부분의 스크립트는 수정할 필요 없이 작동합니다. 그러나 필요한 경우 Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md)및 Azure CLI 명령을 사용하도록 스크립트 파일을 업그레이드할 수 [있습니다.](data-lake-storage-directory-file-acl-cli.md)
 

## <a name="see-also"></a>참조

[Azure Data Lake Storage Gen2 소개](data-lake-storage-introduction.md)