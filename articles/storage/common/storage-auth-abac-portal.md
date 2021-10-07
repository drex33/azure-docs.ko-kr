---
title: '자습서: Azure Portal을 사용하여 BLOB에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기) - Azure ABAC'
titleSuffix: Azure Storage
description: Azure Portal 및 Azure ABAC(Azure 특성 기반 액세스 제어)를 사용하여 역할 할당 조건을 추가해 BLOB에 대한 액세스를 제한합니다.
services: storage
author: rolyon
ms.service: storage
ms.topic: tutorial
ms.author: rolyon
ms.reviewer: ''
ms.subservice: common
ms.date: 09/24/2021
ms.openlocfilehash: 0ab670b9ef6309b8db01f4a53f41c703dea69423
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129092284"
---
# <a name="tutorial-add-a-role-assignment-condition-to-restrict-access-to-blobs-using-the-azure-portal-preview"></a>자습서: Azure Portal을 사용하여 BLOB에 대한 액세스를 제한하는 역할 할당 조건 추가(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

대부분의 경우 역할 할당은 Azure 리소스에 필요한 사용 권한을 부여합니다. 그러나 경우에 따라 역할 할당 조건을 추가하여 보다 세분화된 액세스 제어를 제공하는 것이 좋습니다.

이 자습서에서는 다음 작업 방법을 알아봅니다.

> [!div class="checklist"]
> - 역할 할당에 조건 추가
> - BLOB 인덱스 태그를 기반으로 BLOB에 대한 액세스 제한

## <a name="prerequisites"></a>필수 구성 요소

역할 할당 조건을 추가하거나 편집하기 위한 사전 요구 사항은 [조건 및 사전 요구 사항](../../role-based-access-control/conditions-prerequisites.md)을 참조하세요.

## <a name="condition"></a>조건

이 자습서에서는 특정 태그를 사용하여 BLOB에 대한 액세스를 제한합니다. 예를 들어 Chandra에서 Project=Cascade 태그가 있는 파일만 읽을 수 있도록 역할 할당에 조건을 추가합니다.

![조건이 있는 역할 할당의 다이어그램.](./media/shared/condition-role-assignment-rg.png)

Chandra가 Project=Cascade 태그 없이 BLOB을 읽으려고 하면 액세스가 허용되지 않습니다.

![Project=Cascade 태그를 사용한 BLOB의 읽기 권한을 보여주는 다이어그램](./media/shared/condition-access.png)

코드에서 조건은 다음과 같습니다.

```
(
    (
        !(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'}
        AND
        @Request[subOperation] ForAnyOfAnyValues:StringEqualsIgnoreCase {'Blob.Read.WithTagConditions'})
    )
    OR
    (
        @Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$key_case_sensitive$>] StringEqualsIgnoreCase 'Cascade'
    )
)
```

## <a name="step-1-create-a-user"></a>1단계: 사용자 만들기

1. 구독 소유자로 Azure Portal에 로그인합니다.

1. **Azure Active Directory** 를 클릭합니다.

1. 사용자를 만들거나 기존 사용자를 찾습니다. 이 자습서에서는 Chandra를 예제로 사용합니다.

## <a name="step-2-set-up-storage"></a>2단계: 스토리지 설정

1. 현재 공개 미리 보기인 BLOB 인덱스 태그 기능과 호환되는 스토리지 계정을 만듭니다. 자세한 내용은 [BLOB 인덱스 태그를 사용하여 Azure BLOB 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md#regional-availability-and-storage-account-support)를 참조하세요.

1. 스토리지 계정에서 새 컨테이너를 만들고 공용 액세스 수준을 **프라이빗(익명 액세스 차단)** 으로 설정합니다.

1. 컨테이너에서 **업로드** 를 클릭하여 BLOB 업로드 창을 엽니다.

1. 업로드할 텍스트 파일을 찾습니다.

1. **고급** 을 클릭하여 창을 확장합니다.

1. **BLOB 인덱스 태그** 섹션에서 다음 BLOB 인덱스 태그를 텍스트 파일에 추가합니다.

    BLOB 인덱스 태그 섹션이 표시되지 않고 구독을 방금 등록한 경우 변경 내용이 전파될 때까지 몇 분 정도 기다려야 합니다. 자세한 내용은 [BLOB 인덱스 태그(미리 보기)를 사용하여 Azure Blob Storage 데이터 관리 및 찾기](../blobs/storage-blob-index-how-to.md)를 참조하세요.

    > [!NOTE]
    > BLOB은 임의의 사용자 정의 키 값 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 Blob 인덱스 태그와 유사하지만 조건으로 Blob 인덱스 태그를 사용해야 합니다.

    | 키 | 값 |
    | --- | --- |
    | 프로젝트  | 계단식 배열 |

   ![블로그 인덱스 태그 섹션이 있는 BLOB 업로드 창을 보여주는 스크린샷](./media/storage-auth-abac-portal/container-upload-blob.png)

1. **업로드** 단추를 클릭하여 파일을 업로드합니다.

1. 두 번째 텍스트 파일을 업로드합니다.

1. 다음 BLOB 인덱스 태그를 두 번째 텍스트 파일에 추가합니다.

    | 키 | 값 |
    | --- | --- |
    | 프로젝트  | Baker |

## <a name="step-3-assign-a-storage-blob-data-role"></a>3단계: 스토리지 BLOB 데이터 역할 할당

1. 리소스 그룹을 엽니다.

1. **액세스 제어(IAM)** 를 클릭합니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

1. **추가** > **역할 할당 추가(미리 보기)** 를 클릭합니다.

   ![추가 > 역할 할당 추가 메뉴 미리 보기 스크린샷](./media/storage-auth-abac-portal/add-role-assignment-menu-preview.png)

    역할 할당 추가 페이지가 열립니다.

1. **역할** 탭에서 [스토리지 BLOB 데이터 판독기](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader) 역할을 선택합니다.

    ![역할 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/storage-auth-abac-portal/roles.png)

1. **구성원** 탭에서 이전에 만든 사용자를 선택합니다.

   ![구성원 탭이 있는 역할 할당 추가 페이지의 스크린샷](./media/storage-auth-abac-portal/members.png)

1. (선택 사항) **설명** 상자에 **Read access to blobs with the tag Project=Cascade** 를 입력합니다.

1. **다음** 을 클릭합니다.

## <a name="step-4-add-a-condition"></a>4단계: 조건 추가

1. **조건(옵션)** 탭에서 **조건 추가** 를 클릭합니다.

    ![새 조건에 대한 역할 할당 조건 추가 페이지의 스크린샷](./media/storage-auth-abac-portal/condition-add-new.png)

    역할 할당 조건 추가 페이지가 표시됩니다.

1. 작업 추가 섹션에서 **작업 선택** 을 클릭합니다.

    작업 선택 창이 표시됩니다. 이 창은 조건 대상이 될 역할 할당을 기준으로 데이터 작업을 필터링한 목록입니다.

    ![선택한 작업이 있는 작업 선택 창의 스크린샷](./media/storage-auth-abac-portal/condition-actions-select.png)

1. 블로그 읽기에서 **태그 조건으로 BLOB의 콘텐츠 읽기** 를 클릭한 다음, **선택** 을 클릭합니다.

1. 식 작성 섹션에서 **식 추가** 를 클릭합니다.

    식 섹션이 확장됩니다.

1. 다음 식 설정을 지정합니다.

    | 설정 | 값 |
    | --- | --- |
    | 특성 원본 | 리소스 |
    | attribute | Blob 인덱스 태그 [키의 값] |
    | Key | Project |
    | 연산자 | StringEqualsIgnoreCase |
    | 값 | 계단식 배열 |

    ![BLOB 인덱스 태그의 식 작성 섹션의 스크린샷](./media/storage-auth-abac-portal/condition-expressions.png)

1. **편집기 형식** 으로 스크롤하고 **코드** 를 클릭합니다.

    조건은 코드로 표시됩니다. 이 코드 편집기에서 조건을 변경할 수 있습니다. 시각적 개체 편집기로 돌아가려면 **시각적 개체** 를 클릭합니다.

    ![코드 편집기에 표시된 조건의 스크린샷](./media/storage-auth-abac-portal/condition-code.png)

1. **저장** 을 클릭하여 조건을 추가하고 역할 할당 추가 페이지로 돌아갑니다.

1. **다음** 을 클릭합니다.

1. **검토 + 할당** 탭에서 **검토 + 할당** 을 클릭하여 조건으로 역할을 할당합니다.

    몇 분이 지나면 선택한 범위에서 보안 주체에 역할이 할당됩니다.

    ![역할을 할당한 후 역할 할당 목록의 스크린샷](./media/storage-auth-abac-portal/rg-role-assignments-condition.png)

## <a name="step-5-test-the-condition"></a>5단계: 조건 테스트

조건을 테스트하려면 Azure PowerShell을 사용해야 합니다.

1. PowerShell 창을 엽니다.

1. [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount)를 사용하여 Chandra로 로그인합니다.

    ```azurepowershell
    Connect-AzAccount
    ```

1. 사용한 이름으로 다음 변수를 초기화합니다.

    ```azurepowershell
    $storageAccountName = "<storageAccountName>"
    $containerName = "<containerName>"
    $blobNameBaker = "<blobNameBaker>"
    $blobNameCascade = "<blobNameCascade>"
    ```

1. [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext)를 사용하여 스토리지 계정에 보다 쉽게 액세스할 수 있는 특정 컨텍스트를 만듭니다.

    ```azurepowershell
    $bearerCtx = New-AzStorageContext -StorageAccountName $storageAccountName
    ```

1. [Get-AzStorageBlob](/powershell/module/az.storage/get-azstorageblob)을 사용하여 Baker 프로젝트의 파일을 읽습니다.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameBaker -Context $bearerCtx 
    ```

    다음은 출력 예제입니다. 추가한 조건으로 인해 파일을 읽을 수 **없습니다.**

    ```azurepowershell
    Get-AzStorageBlob: This request is not authorized to perform this operation using this permission. HTTP Status Code: 403 - HTTP Error Message: This request is not authorized to perform this operation using this permission.
    ErrorCode: AuthorizationPermissionMismatch
    ErrorMessage: This request is not authorized to perform this operation using this permission.
    RequestId: <requestId>
    Time: Sun, 13 Sep 2020 12:33:42 GMT
    ```

1. Cascade 프로젝트의 파일을 읽습니다.

    ```azurepowershell
    Get-AzStorageBlob -Container $containerName -Blob $blobNameCascade -Context $bearerCtx 
    ```

    다음은 출력 예제입니다. Project=Cascade 태그가 있으므로 파일을 읽을 수 있습니다.

    ```azurepowershell
       AccountName: <storageAccountName>, ContainerName: <containerName>

    Name                 BlobType  Length          ContentType                    LastModified         AccessTier SnapshotT
                                                                                                                  ime
    ----                 --------  ------          -----------                    ------------         ---------- ---------
    CascadeFile.txt      BlockBlob 7               text/plain                     2021-04-24 05:35:24Z Hot
    ```

## <a name="step-6-clean-up-resources"></a>6단계: 리소스 정리

1. 추가한 역할 할당을 제거합니다.

1. 만든 테스트 스토리지 계정을 삭제합니다.

1. 만든 사용자를 삭제합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 예제](storage-auth-abac-examples.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)
- [Azure 역할 할당 조건의 형식 및 구문](../../role-based-access-control/conditions-format.md)
