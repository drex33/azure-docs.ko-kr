---
title: Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)
titleSuffix: Azure Storage
description: Azure Storage에서 Azure 역할 할당 조건 및 Azure ABAC(Azure 특성 기반 액세스 제어)에 대해 지원되는 작업 및 특성입니다.
services: storage
author: santoshc
ms.service: storage
ms.topic: conceptual
ms.date: 05/06/2021
ms.author: santoshc
ms.reviewer: jiacfan
ms.subservice: common
ms.openlocfilehash: 791e533cc1f0dafa8724faa8cc3a46d1f8c770d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666006"
---
# <a name="actions-and-attributes-for-azure-role-assignment-conditions-in-azure-storage-preview"></a>Azure Storage에서 Azure 역할 할당 조건에 대한 작업 및 특성(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

이 문서에서는 각 Azure Storage [DataAction](../../role-based-access-control/role-definitions.md#dataactions)에 대한 Azure 역할 할당 조건에서 사용할 수 있는 지원되는 특성 사전에 대해 설명합니다. 특정 권한 또는 DataAction의 영향을 받는 Blob 서비스 작업 목록은 [Blob 서비스 작업에 대한 권한](/rest/api/storageservices/authorize-with-azure-active-directory#permissions-for-blob-service-operations)을 참조하세요.

역할 할당 조건 형식을 이해하려면 [Azure 역할 할당 조건 형식 및 구문](../../role-based-access-control/conditions-format.md)을 참조하세요.

## <a name="suboperations"></a>하위 작업

여러 스토리지 서비스 작업을 단일 권한 또는 DataAction과 연결될 수 있습니다. 그러나 동일한 사용 권한과 연결된 이러한 각 작업은 서로 다른 매개 변수를 지원할 수 있습니다. *하위 작업* 을 사용하면 동일한 권한이 필요하지만 조건에 대해 서로 다른 속성 집합을 지원하는 서비스 작업을 구별할 수 있습니다. 따라서 하위 작업을 사용하여 지정된 매개 변수를 지원하는 작업의 하위 집합에 액세스하기 위한 하나의 조건을 지정할 수 있습니다. 그런 다음 해당 매개 변수를 지원하지 않는 동일한 작업을 사용하여 작업에 다른 액세스 조건을 사용할 수 있습니다.

예를 들어, `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` 작업은 12개가 넘는 서비스 작업에 필요합니다. 이러한 작업 중 일부는 Blob 인덱스 태그를 요청 매개 변수로 허용할 수 있지만 다른 작업은 허용하지 않습니다. Blob 인덱스 태그를 매개 변수로 허용하는 작업의 경우 요청 조건에서 Blob 인덱스 태그를 사용할 수 있습니다. 그러나 이러한 조건이 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` 작업에 정의된 경우 태그를 요청 매개 변수로 허용하지 않는 모든 작업은 이 조건을 평가할 수 없으며 권한 부여 액세스 검사에 실패합니다.

이 경우 선택적 하위 작업 `Blob.Write.WithTagHeaders`를 사용하여 Blob 인덱스 태그를 요청 매개 변수로 지원하는 작업에만 조건을 적용할 수 있습니다.

마찬가지로 `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` 작업에 대한 선택 작업만 액세스를 위한 사전 조건으로 Blob 인덱스 태그를 지원할 수 있습니다. 이 작업의 하위 집합은 `Blob.Read.WithTagConditions` 하위 작업으로 식별됩니다.

> [!NOTE]
> BLOB은 임의의 사용자 정의 키 값 메타데이터를 저장하는 기능을 지원합니다. 메타데이터는 BLOB 인덱스 태그와 유사하지만 조건으로 BLOB 인덱스 태그를 사용해야 합니다. 자세한 내용은 [Blob 인덱스를 사용하여 Azure Blob Storage에서 데이터 관리 및 찾기(미리 보기)](../blobs/storage-manage-find-blobs.md)를 참조하세요.

이 미리 보기에서 스토리지 계정은 다음과 같은 하위 작업을 지원합니다.

> [!div class="mx-tableFixed"]
> | DataAction | 하위 작업 | 표시 이름 | Description |
> | :--- | :--- | :--- | :--- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `Blob.Read.WithTagConditions` | 태그에 대한 조건을 지원하는 Blob 읽기 작업 | REST 작업에 Blob 가져오기, Blob 메타데이터 가져오기, Blob 속성 가져오기, 블록 목록 가져오기, 페이지 범위 가져오기, Blob 콘텐츠 쿼리가 포함됩니다. |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` <br/> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `Blob.Write.WithTagHeaders` | 선택적 태그가 있는 콘텐츠에 대한 Blob 쓰기 | REST 작업에 Blob 배치, Blob 복사, URL에서 Blob 복사 및 블록 목록 배치가 포함됩니다. |

## <a name="actions-and-suboperations"></a>작업 및 하위 작업

다음 표에서는 Azure Storage 조건에 대해 지원되는 작업 및 하위 작업을 나열합니다.

> [!div class="mx-tableFixed"]
> | 표시 이름 | Description | DataAction |
> | --- | --- | --- |
> | Blob 삭제 | Blob을 삭제하기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` |
> | Blob 읽기 | Blob을 읽기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
> | 태그 조건이 있는 Blob에서 콘텐츠 읽기  | REST 작업: Blob 가져오기, Blob 메타데이터 가져오기, Blob 속성 가져오기, 블록 목록 가져오기, 페이지 범위 가져오기 및 Blob 콘텐츠 쿼리 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**하위 작업**<br/>`Blob.Read.WithTagConditions` |
> | Blob에 쓰기 | Blob에 쓰기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` |
> | Blob 인덱스 태그를 사용하여 Blob에 쓰기 | REST 작업: Blob 배치, 블록 목록 배치, Blob 복사 및 URL에서 Blob 복사 |`Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**하위 작업**<br/>`Blob.Write.WithTagHeaders` |
> | Blob 또는 스냅샷을 만들거나 데이터를 추가합니다. | Blob을 만들기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` |
> | Blob 인덱스 태그를 사용하여 Blob에 콘텐츠 쓰기 | REST 작업: Blob 배치, 블록 목록 배치, Blob 복사 및 URL에서 Blob 복사 | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**하위 작업**<br/>`Blob.Write.WithTagHeaders` |
> | Blob 버전 삭제 | Blob 버전을 삭제하기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` |
> | Blob의 소유권 변경 | Blob의 소유권을 변경하기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` |
> | Blob의 권한 수정 | Blob의 사용 권한을 수정하기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` |
> | 파일 또는 디렉터리 이름 바꾸기 | 파일 또는 디렉터리 이름을 바꾸기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` |
> | 일시 삭제를 재정의하는 Blob을 영구적으로 삭제 | 일시 삭제를 재정의하는 Blob을 영구적으로 삭제하기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` |
> | HNS가 있는 계정에 대한 모든 데이터 작업 | HNS를 통해 스토리지 계정에 대한 모든 데이터 작업을 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` |
> | Blob 인덱스 태그 읽기 | Blob 인덱스 태그를 읽기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` |
> | Blob 인덱스 태그 쓰기 | Blob 인덱스 태그를 쓰기 위한 DataAction입니다. | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` |

## <a name="attributes"></a>특성

다음 표에서는 Azure Storage 조건에 대해 지원되는 특성에 대한 설명을 나열합니다.

> [!div class="mx-tableFixed"]
> | 표시 이름 | Description | attribute |
> | --- | --- | --- |
> | 컨테이너 이름| 스토리지 컨테이너 또는 파일 시스템의 이름입니다. 컨테이너 이름을 확인하려는 경우 사용합니다. | `containers:name` |
> | Blob 경로 | 가상 디렉터리, Blob, 폴더 또는 파일 리소스의 경로입니다. Blob 경로에서 Blob 이름 또는 폴더를 확인하려는 경우 사용합니다. | `blobs:path` |
> | Blob 인덱스 태그 [키] | Blob 리소스의 인덱스 태그입니다. Blob 리소스와 함께 저장할 수 있는 임의의 사용자 정의 키-값 속성입니다. Blob 인덱스 태그에서 키를 확인하려는 경우 사용합니다. | `tags&$keys$&` |
> | Blob 인덱스 태그 [키의 값] | Blob 리소스의 인덱스 태그입니다. Blob 리소스와 함께 저장할 수 있는 임의의 사용자 정의 키-값 속성입니다. Blob 인덱스 태그에서 키(대/소문자 구분) 및 값을 모두 확인하려는 경우 사용합니다. | `tags:`*keyname*`<$key_case_sensitive$>` |

> [!NOTE]
> 나열된 특성 및 값은 달리 명시되지 않는 한 대/소문자를 구분하지 않는 것으로 간주됩니다.

> [!NOTE]
> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs:path` 특성에 대한 조건을 지정할 때 값에 컨테이너 이름이나 선행 '/' 문자가 포함되지 않아야 합니다. URL 인코딩 없이 경로 문자를 사용합니다.

> [!NOTE]
> Blob 인덱스 태그는 HNS[(계층 구조 네임스페이스)](../blobs/data-lake-storage-namespace.md)가 있는 Data Lake Storage Gen2 스토리지 계정에 대해 지원되지 않습니다. HNS를 사용하도록 설정된 스토리지 계정에서 인덱스 태그를 사용하여 역할 할당 조건을 작성해서는 안됩니다.

## <a name="attributes-available-for-each-action"></a>각 작업에 사용할 수 있는 특성

다음 표에서는 대상 작업에 따라 조건 식에서 사용할 수 있는 특성을 나열합니다. 단일 조건에 대해 여러 작업을 선택하는 경우 선택한 작업에서 특성을 사용할 수 있어야 하므로 조건에 대해 선택할 특성이 적을 수 있습니다.

> [!div class="mx-tableFixed"]
> | DataAction | attribute | Type | 적용 대상 |
> | --- | --- | --- | --- |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/delete` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read`<br/>**하위 작업**<br/>`Blob.Read.WithTagConditions` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/write`<br/>**하위 작업**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/add/action`<br/>**하위 작업**<br/>`Blob.Write.WithTagHeaders` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/deleteBlobVersion/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/manageOwnership/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/modifyPermissions/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/move/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/permanentDelete/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/runAsSuperUser/action` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | ResourceAttributeOnly |
> | `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` | `containers:name` | 문자열 | ResourceAttributeOnly |
> |  | `blobs:path` | 문자열 | ResourceAttributeOnly |
> |  | `tags` | dictionaryOfString | RequestAttributeOnly |

## <a name="see-also"></a>참고 항목

- [Azure 역할 할당 조건 예(미리 보기)](storage-auth-abac-examples.md)
- [Azure 역할 할당 조건 형식 및 구문(미리 보기)](../../role-based-access-control/conditions-format.md)
- [Azure ABAC(Azure 특성 기반 액세스 제어)란 무엇인가요?(미리 보기)](../../role-based-access-control/conditions-overview.md)
