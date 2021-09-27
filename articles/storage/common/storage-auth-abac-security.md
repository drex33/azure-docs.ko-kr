---
title: Azure Storage의 Azure 역할 할당 조건에 대한 보안 고려 사항(미리 보기)
titleSuffix: Azure Storage
description: Azure 역할 할당 조건 및 Azure ABAC(Azure 특성 기반 액세스 제어)에 대한 보안 고려 사항입니다.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 46be6327052aa280b9d23bc05764550a05739c5e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128627922"
---
# <a name="security-considerations-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure Storage의 Azure 역할 할당 조건에 대한 보안 고려 사항(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure ABAC(Azure Attribute-based Access Control)](storage-auth-abac.md)를 사용하여 리소스를 완전히 보호하려면 [Azure 역할 할당 조건](../../role-based-access-control/conditions-format.md)에 사용되는 [특성](storage-auth-abac-attributes.md)도 보호해야 합니다. 예를 들어 조건이 파일 경로를 기반으로 하는 경우 보안 주체에 파일 경로 이름을 바꿀 수 있는 무제한 권한이 있으면 액세스가 손상될 수 있음을 주의해야 합니다.

이 문서에서는 역할 할당 조건에 고려해야 하는 보안 고려 사항에 대해 설명합니다.

## <a name="use-of-other-authorization-mechanisms"></a>다른 권한 부여 메커니즘 사용

역할 할당 조건은 권한 부여를 위해 Azure RBAC를 사용하는 경우에만 평가됩니다. 대체 권한 부여 방법을 사용하여 액세스를 허용하는 경우 이러한 조건을 바이패스할 수 있습니다.
- [공유 키](/rest/api/storageservices/authorize-with-shared-key) 권한 부여
- [계정 SAS(공유 액세스 서명)](/rest/api/storageservices/create-account-sas)
- [서비스 SAS](/rest/api/storageservices/create-service-sas),

마찬가지로 [HNS(계층 구조 네임스페이스)](../blobs/data-lake-storage-namespace.md)가 있는 스토리지 계정에서 [ACL(액세스 제어 목록)](../blobs/data-lake-storage-access-control.md)을 사용하여 액세스 권한이 부여되면 조건이 평가되지 않습니다.

스토리지 계정에 대해 [공유 키 인증을 사용 중지](shared-key-authorization-prevent.md)하여 공유 키, 계정 수준 SAS 및 서비스 수준 SAS 인증을 방지할 수 있습니다. 사용자 위임 SAS는 Azure RBAC에 따라 다르기 때문에 이 권한 부여 방법을 사용할 때 역할 할당 조건이 평가됩니다.

> [!NOTE]
> Data Lake Storage Gen2 ACL을 사용하여 액세스 권한을 부여하면 역할 할당 조건이 평가되지 않습니다. 이 경우 ACL을 통해 부여된 액세스 범위와 겹치지 않도록 액세스 범위를 계획해야 합니다.

## <a name="securing-storage-attributes-used-in-conditions"></a>조건에 사용되는 스토리지 특성 보안

### <a name="blob-path"></a>Blob 경로

조건에 대한 *@Resource* 특성으로 Blob 경로를 사용하는 경우 계층 구조 네임스페이스가 있는 계정을 사용할 때 파일에 액세스하기 위해 사용자가 Blob의 이름을 바꾸지 못하도록 해야 합니다. 예를 들어 Blob 경로를 기반으로 조건을 작성하려는 경우 다음 작업에 대한 사용자의 액세스 권한도 제한해야 합니다.

| 작업 | 설명 |
| :--- | :--- |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | 이 작업을 통해 고객은 경로 만들기 API를 사용하여 파일 이름을 바꿀 수 있습니다. |
| `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | 이 작업을 통해 다양한 파일 시스템 및 경로 작업에 액세스할 수 있습니다. |

### <a name="blob-index-tags"></a>Blob 인덱스 태그

[Blob 인덱스 태그](../blobs/storage-manage-find-blobs.md)는 스토리지의 조건에 대한 자유 형식 특성으로 사용됩니다. 이러한 태그를 사용하여 액세스 조건을 작성하는 경우에도 태그를 보호해야 합니다. 특히 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` DataAction을 사용하면 사용자가 스토리지 개체의 태그를 수정할 수 있습니다. 권한 없는 개체에 대한 액세스를 얻기 위해 사용자가 태그 키 또는 값을 조작하지 못하도록 이 작업을 제한할 수 있습니다.

또한 조건에 Blob 인덱스 태그를 사용하는 경우 데이터와 관련 인덱스 태그가 별도의 작업에서 업데이트되는 경우 데이터가 취약할 수 있습니다. Blob 쓰기 작업에서 `@Request` 조건을 사용하여 동일한 업데이트 작업에서 인덱스 태그를 설정하도록 요구할 수 있습니다. 이 접근 방식은 스토리지에 기록된 순간부터 데이터를 보호하는 데 도움이 될 수 있습니다.

#### <a name="tags-on-copied-blobs"></a>복사한 Blob의 태그

기본적으로 Blob 인덱스 태그는 [Blob 복사](/rest/api/storageservices/Copy-Blob) API 또는 그 변형을 사용할 때 원본 Blob에서 대상으로 복사되지 않습니다. 복사할 때 Blob에 대한 액세스 범위를 유지하려면 태그도 복사해야 합니다.

#### <a name="tags-on-snapshots"></a>스냅샷의 태그

Blob 스냅샷의 태그는 수정할 수 없습니다. 이는 스냅샷을 만들기 전에 Blob의 태그를 업데이트해야 함을 의미합니다. 기본 Blob에서 태그를 수정하는 경우 스냅샷의 태그는 계속 이전 값을 갖게 됩니다.

스냅샷이 생성된 후 기본 Blob의 태그가 수정된 경우 기본 Blob 및 스냅샷에 대한 액세스 범위가 다를 수 있습니다.

#### <a name="tags-on-blob-versions"></a>Blob 버전의 태그

Blob 인덱스 태그는 [Blob 놓기](/rest/api/storageservices/put-blob), [블록 목록 놓기](/rest/api/storageservices/put-block-list) 또는 [Blob 복사](/rest/api/storageservices/Copy-Blob) API를 통해 Blob 버전이 생성될 때 복사되지 않습니다. 이러한 API에 대한 헤더를 통해 태그를 지정할 수 있습니다.

태그는 현재 기본 Blob 및 각 Blob 버전에 대해 개별적으로 설정할 수 있습니다. 기본 Blob에서 태그를 수정하면 이전 버전의 태그가 업데이트되지 않습니다. 태그를 사용하여 Blob 및 모든 버전에 대한 액세스 범위를 변경하려면 각 버전에서 태그를 업데이트해야 합니다.

#### <a name="querying-and-filtering-limitations-for-versions-and-snapshots"></a>버전 및 스냅샷에 대한 쿼리 및 필터링 제한 사항

태그를 사용하여 컨테이너의 Blob을 쿼리하고 필터링하는 경우 기본 Blob만 응답에 포함됩니다. 요청된 키와 값이 있는 Blob 버전 또는 스냅샷은 포함되지 않습니다.

## <a name="roles-and-permissions"></a>역할 및 권한

[Azure 기본 제공](../../role-based-access-control/built-in-roles.md)역할에 대 한 역할 할당 조건을 사용 하는 경우 역할에서 보안 주체에 부여 하는 모든 권한을 신중 하 게 검토 해야 합니다.

### <a name="inherited-role-assignments"></a>상속된 역할 할당

역할 할당은 관리 그룹, 구독, 리소스 그룹, 스토리지 계정 또는 컨테이너에 대해 구성할 수 있으며 지정된 순서로 각 수준에서 상속됩니다. Azure RBAC에는 추가 모델이 있으므로 유효 권한은 각 수준의 역할 할당의 합계입니다. 보안 주체가 여러 역할 할당을 통해 동일한 권한을 할당한 경우 해당 권한을 사용하는 작업에 대한 액세스는 모든 수준의 각 할당에 대해 개별적으로 평가됩니다.

조건은 역할 할당에 대한 조건으로 구현되므로 조건부 역할 할당을 통해 사용자가 조건을 무시할 수 있습니다. 스토리지 계정 및 구독에 대한 사용자에게 *스토리지 Blob Data 기여자* 역할을 할당하고 스토리지 계정에 대한 할당에만 조건을 추가한다고 가정해 보겠습니다. 이 경우 사용자는 구독 수준에서 역할 할당을 통해 스토리지 계정에 무제한으로 액세스할 수 있습니다.

따라서 리소스 계층 구조 전체의 모든 역할 할당에 대해 조건을 일관되게 적용해야 합니다.

## <a name="other-considerations"></a>기타 고려 사항

### <a name="condition-operations-that-write-blobs"></a>Blob을 작성하는 조건 작업

Blob을 작성하는 많은 작업에는 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` 또는 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` 권한이 필요합니다. [스토리지 Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 및 [스토리지 Blob 데이터 기여자](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor)와 같은 기본 제공 역할은 보안 주체에 두 권한을 모두 부여합니다.

이러한 역할에 대한 역할 할당 조건을 정의할 때는 이러한 두 권한에 대해 동일한 조건을 사용하여 쓰기 작업에 대한 일관된 액세스 제한을 보장해야 합니다.

### <a name="behavior-for-copy-blob-and-copy-blob-from-url"></a>URL에서 Blob 복사 및 Blob 복사에 대한 동작

[Blob 복사](/rest/api/storageservices/Copy-Blob) 및 [URL에서 Blob 복사](/rest/api/storageservices/copy-blob-from-url) 작업의 경우 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 작업 및 해당 하위 작업의 특성으로 Blob 경로를 사용하는 `@Request` 조건은 대상 Blob에 대해서만 평가됩니다.

소스 Blob에 대한 조건의 경우 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 작업에 대한 `@Resource` 조건이 평가됩니다.

### <a name="behavior-for-get-page-ranges"></a>페이지 범위 가져오기 동작

[페이지 범위 가져오기](/rest/api/storageservices/get-page-ranges) 작업의 경우 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 작업 및 하위 작업의 특성으로 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags`를 사용하는 `@Resource` 조건은 대상 Blob에 대해서만 평가됩니다.

API의 `prevsnapshot` URI 매개 변수로 지정된 Blob에 대한 액세스에는 조건이 적용되지 않습니다.

## <a name="see-also"></a>참고 항목

- [Azure 역할 할당 조건을 사용하여 Blob에 대한 액세스 권한 부여(미리 보기)](storage-auth-abac.md)
- [Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)](storage-auth-abac-attributes.md)
- [Azure ABAC(Azure 특성 기반 액세스 제어)란 무엇인가요?(미리 보기)](../../role-based-access-control/conditions-overview.md)
