---
title: Azure Data Box 및 Azure Data Box Heavy 디바이스에서 업로드할 때의 복사 오류 검토
description: Azure Data Box 또는 Azure Data Box Heavy 디바이스에서 파일을 업로드하지 못하게 하는 다시 시도할 수 없는 오류에 대한 검토 및 후속 작업을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: pod
ms.topic: troubleshooting
ms.date: 08/24/2021
ms.author: alkohli
ms.openlocfilehash: 471d5bff3fa3a4be092f1294f64b0bb8c3fdf9a8
ms.sourcegitcommit: d858083348844b7cf854b1a0f01e3a2583809649
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "129220408"
---
# <a name="review-copy-errors-in-uploads-from-azure-data-box-and-azure-data-box-heavy-devices"></a>Azure Data Box 및 Azure Data Box Heavy 디바이스의 업로드에서 복사 오류 검토

이 문서에서는 Azure Data Box 또는 Azure Data Box Heavy 디바이스에서 파일을 클라우드로 업로드하지 못하게 하는 다시 시도할 수 없는 오류에 대한 검토 및 후속 작업을 설명합니다.

> [!NOTE]
> 이 문서의 정보는 가져오기 주문에만 적용됩니다.

## <a name="upload-errors-notification"></a>업로드 오류 알림

디바이스에서 Azure로 데이터를 업로드할 때 재시도를 통해 확인할 수 없는 구성 오류 때문에 일부 파일 업로드가 실패하는 경우가 있습니다. 이 경우 알림이 제공되므로 오류를 검토하고 수정한 후 나중에 업로드할 수 있는 기회를 얻을 수 있습니다.

Azure Portal에서 다음과 같은 알림이 표시됩니다. **DATA COPY PATH** 를 사용하여 열 수 있는 데이터 복사 로그에 오류가 나열됩니다. 오류를 해결하는 방법에 대한 지침은 [다시 시도할 수 없는 업로드 오류 요약](#summary-of-non-retryable-upload-errors)을 참조하세요.

![업로드 중 오류 알림](media/data-box-troubleshoot-data-upload/copy-completed-with-errors-notification-01.png)

이러한 오류는 수정할 수 없습니다. 업로드는 오류를 나타내며 완료되었습니다. 이 알림을 통해 네트워크 전송 또는 새 가져오기 주문을 통해 다른 업로드를 시도하기 전에 해결해야 하는 구성 문제에 대해 알 수 있습니다.

오류를 검토하고 계속할 준비가 되었음을 확인하면 디바이스에서 데이터를 지워도 안전합니다. 알림에 응답하지 않으면 14일 후에 주문이 자동으로 완료됩니다. 단계별 지침은 [오류 검토 및 진행](#review-errors-and-proceed)을 참조하세요.


## <a name="review-errors-and-proceed"></a>오류 검토 및 진행

주문은 14일이 지나면 자동으로 완료됩니다. 알림에 대해 조치를 취하여 작업을 더 빠르게 진행할 수 있습니다.

[!INCLUDE [data-box-review-nonretryable-errors](../../includes/data-box-review-nonretryable-errors.md)]


## <a name="summary-of-non-retryable-upload-errors"></a>다시 시도할 수 없는 업로드 오류 요약

다시 시도할 수 없는 다음과 같은 오류가 발생하면 알림이 표시됩니다.

|오류 범주                    |오류 코드 |오류 메시지                                                                             |
|----------------------------------|-----------|------------------------------------------------------------------------------------------|
|UploadErrorCloudHttp              |400        |잘못된 요청(파일 이름이 잘못됨) [자세한 정보](#bad-request-file-name-not-valid)|
|UploadErrorCloudHttp              |400        |HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다. [자세한 정보](#the-value-for-one-of-the-http-headers-is-not-in-the-correct-format).|
|UploadErrorCloudHttp              |409        |정책으로 인해 Blob을 변경할 수 없으므로 이 작업을 수행할 수 없습니다. [자세한 정보](#this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy).|
|UploadErrorCloudHttp              |409        |공유의 프로비저닝된 총 용량은 계정 최대 크기 한도를 초과할 수 없습니다. [자세한 정보](#the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit).|
|UploadErrorCloudHttp              |409        |Blob 유형은 이 작업에 적합하지 않습니다. [자세한 정보](#the-blob-type-is-invalid-for-this-operation).|
|UploadErrorCloudHttp              |409        |현재 Blob에 임대가 있는데 요청에서 임대 ID가 지정되지 않았습니다. [자세한 정보](#there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request).|
|UploadErrorManagedConversionError |409        |가져오는 Blob의 크기가 잘못되었습니다. Blob 크기는 `<blob-size>`바이트입니다. 지원되는 크기는 20971520바이트와 8192GiB 사이입니다. [자세한 정보](#the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib)|
<!--Temporarily removed from table: Bad Request (file property failure for Azure Files)-->

데이터 복사 로그의 내용에 대한 자세한 내용은 [Azure Data Box에 대한 추적 및 이벤트 로깅 및 Azure Data Box Heavy 가져오기 주문](data-box-logs.md)을 참조하세요.

데이터를 업로드하는 동안 다른 REST API 오류가 발생할 수 있습니다. 자세한 내용은 [일반적인 REST API 오류 코드](/rest/api/storageservices/common-rest-api-error-codes)를 참조하세요.

> [!NOTE]
> 오류 설명의 **후속 작업** 섹션에서는 새 가져오기 주문을 진행하거나 네트워크 전송을 수행하기 전에 데이터 구성을 업데이트하는 방법을 설명합니다. 현재 업로드에서는 이러한 오류를 해결할 수 없습니다.


### <a name="bad-request-file-name-not-valid"></a>잘못된 요청(파일 이름이 잘못됨)

**오류 범주:** UploadErrorCloudHttp 

**오류 코드:** 400

**오류 설명:** 대부분의 파일 명명 이슈는 **배송 준비** 단계 중에 검색되거나 업로드 중에 자동으로 수정됩니다(**복사본(경고 포함)** 상태가 발생함). 잘못된 파일 이름이 검색되지 않으면 파일이 Azure로 업로드되지 않습니다.

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 주문을 시작하기 전에 Azure Files의 명명 요구 사항에 맞게 나열된 파일의 이름을 바꿉니다. 명명 요구 사항에 대해서는 [디렉터리 및 파일 이름](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata#directory-and-file-names)을 참조하세요.


<!--TEMPORARILY REMOVED. Product team may restore later. ### Bad Request (File property failure for Azure Files)

**Error category:** UploadErrorCloudHttp 

**Error code:** 400

**Error description:** Data import will fail if the upload of file properties fails for Azure Files.  

**Follow-up:** You can't fix this error in the current upload. The upload will complete with errors. Before you do a network transfer or start a new import order, *GET TROUBLESHOOTING*.-->


### <a name="the-value-for-one-of-the-http-headers-is-not-in-the-correct-format"></a>HTTP 헤더 중 하나에 대한 값 형식이 올바르지 않습니다.

**오류 범주:** UploadErrorCloudHttp 

**오류 코드:** 400

**오류 설명:** 나열된 Blob은 Azure Storage의 Blob에 대한 형식 또는 크기 요구 사항을 충족하지 않으므로 업로드할 수 없습니다.

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 가져오기 주문을 시작하기 전에 다음을 확인하세요.

- 나열된 페이지 Blob은 512바이트 페이지 경계에 맞춰집니다.

- 나열된 블록 Blob이 4.75TiB의 최대 크기를 초과하지 않습니다.


### <a name="this-operation-is-not-permitted-as-the-blob-is-immutable-due-to-policy"></a>정책으로 인해 Blob을 변경할 수 없으므로 이 작업을 수행할 수 없습니다.

**오류 범주:** UploadErrorCloudHttp 

**오류 코드:** 409

**오류 설명:** Blob Storage 컨테이너가 WORM(Write Once, Read Many)으로 구성된 경우 컨테이너에 이미 저장되어 있는 Blob의 업로드가 실패합니다.

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 가져오기 주문을 시작하기 전에 나열된 Blob이 변경할 수 없는 스토리지 컨테이너의 일부가 아닌지 확인합니다. 자세한 내용은 [변경이 불가능한 스토리지로 업무상 중요한 Blob 데이터 저장](../storage/blobs/immutable-storage-overview.md)을 참조하세요.


### <a name="the-total-provisioned-capacity-of-the-shares-cannot-exceed-the-account-maximum-size-limit"></a>공유의 프로비저닝된 총 용량은 계정 최대 크기 한도를 초과할 수 없음

**오류 범주:** UploadErrorCloudHttp 

**오류 코드:** 409

**오류 설명:** 데이터의 총 크기가 스토리지 계정 크기 제한을 초과하므로 업로드하지 못했습니다. 예를 들어, FileStorage 계정의 최대 용량은 100TiB입니다. 총 데이터 크기가 100TiB를 초과하면 업로드가 실패합니다.  

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 가져오기 순서를 시작하기 전에 스토리지 계정에 있는 모든 공유의 총 용량이 스토리지 계정의 크기 제한을 초과하지 않는지 확인합니다. 자세한 내용은 [Azure Storage 계정 크기 제한](data-box-limits.md#azure-storage-account-size-limits)을 참조하세요.


### <a name="the-blob-type-is-invalid-for-this-operation"></a>이 작업에 대한 Blob 유형이 올바르지 않음

**오류 범주:** UploadErrorCloudHttp 

**오류 코드:** 409

**오류 설명:** 대상 Blob의 데이터 또는 속성을 수정하는 경우 클라우드의 Blob에 대한 데이터 가져오기가 실패합니다.

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 가져오기 순서를 시작하기 전에 업로드하는 동안 나열된 Blob 또는 해당 속성을 동시에 수정하지 않아야 합니다.

### <a name="there-is-currently-a-lease-on-the-blob-and-no-lease-id-was-specified-in-the-request"></a>현재 Blob에 임대가 있으나 요청에서 임대 ID가 지정되지 않음

**오류 범주:** UploadErrorCloudHttp 

**오류 코드:** 409

**오류 설명:** 대상 Blob에 활성 임대가 있는 경우 클라우드의 Blob에 대한 데이터 가져오기가 실패합니다.

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 가져오기 순서를 시작하기 전에 나열된 Blob에 활성 임대가 없는지 확인합니다. 자세한 내용은 [Blob에 대한 비관적 동시성](../storage/blobs/concurrency-manage.md?tabs=dotnet#pessimistic-concurrency-for-blobs)을 참조하세요.


### <a name="the-size-of-the-blob-being-imported-is-invalid-the-blob-size-is-blob-size-bytes-supported-sizes-are-between-20971520-bytes-and-8192-gib"></a>가져오는 Blob의 크기가 잘못되었습니다. Blob 크기는 `<blob-size>`바이트입니다. 지원되는 크기는 20971520바이트와 8192GiB 사이입니다.

**오류 범주:** UploadErrorManagedConversionError

**오류 코드:** 409

**오류 설명:** 나열된 페이지 Blob은 Managed Disks로 변환될 수 있는 크기가 아니므로 업로드하지 못했습니다. Managed Disks로 변환하려면 페이지 Blob의 크기가 20MB(20,971,520바이트)에서 8192GbB여야 합니다.

**후속 작업:** 현재 업로드에서는 이 오류를 해결할 수 없습니다. 업로드가 오류와 함께 완료되었습니다. 네트워크 전송을 수행하거나 새 가져오기 순서를 시작하기 전에 나열된 각 Blob의 크기가 20MB~8192GiB인지 확인합니다.


## <a name="next-steps"></a>다음 단계

- [일반적인 REST API 오류 검토](/rest/api/storageservices/common-rest-api-error-codes)
- [Azure에 대한 데이터 업로드 확인](data-box-deploy-picked-up.md?tabs=in-us-canada-europe#verify-data-upload-to-azure-8)