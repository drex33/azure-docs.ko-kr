---
title: Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)
description: Blob 인덱스 태그를 사용하여 Blob 개체를 분류, 관리 및 쿼리하는 방법에 대해 알아봅니다.
author: normesta
ms.author: normesta
ms.date: 05/17/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: klaasl
ms.custom: references_regions, devx-track-azurepowershell
ms.openlocfilehash: bd1738c0a5d63ad9eacaa1500a6ce10268a93b04
ms.sourcegitcommit: df574710c692ba21b0467e3efeff9415d336a7e1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/28/2021
ms.locfileid: "110664882"
---
# <a name="manage-and-find-azure-blob-data-with-blob-index-tags-preview"></a>Blob 인덱스 태그를 사용하여 Azure Blob 데이터 관리 및 찾기(미리 보기)

데이터 세트가 커질수록 데이터의 바다에서 특정 개체를 찾기가 어려울 수 있습니다. Blob 인덱스 태그는 키-값 인덱스 태그 특성을 사용하여 데이터 관리 및 검색 기능을 제공합니다. 단일 컨테이너 내에서 또는 스토리지 계정의 모든 컨테이너에서 개체를 분류하고 찾을 수 있습니다. 데이터 요구 사항이 변경되면 인덱스 태그를 업데이트하여 개체를 동적으로 분류할 수 있습니다. 개체는 현재 컨테이너 조직에 그대로 남아 있을 수 있습니다.

Blob 인덱스 태그를 통해 다음을 수행할 수 있습니다.

- 키-값 인덱스 태그를 사용하여 Blob을 동적으로 분류
- 전체 스토리지 계정에서 태그가 지정된 특정 Blob 빠르게 찾기
- 인덱스 태그 평가를 기반으로 Blob API에 대한 조건부 동작 지정
- [Blob 수명 주기 관리](storage-lifecycle-management-concepts.md)와 같은 기능에 대한 고급 컨트롤을 위해 인덱스 태그 사용

스토리지 계정에 수백만 개의 Blob이 있고 여기에 다양한 애플리케이션이 액세스하는 시나리오를 생각해 보겠습니다. 단일 프로젝트에서 관련된 모든 데이터를 찾으려고 합니다. 데이터가 서로 다른 명명 규칙을 사용하여 여러 컨테이너에 분산되어 있을 수 있기 때문에 범위에 무엇이 있는지 확실하지 않습니다. 그런데 애플리케이션은 모든 데이터를 프로젝트에 기반한 태그와 함께 업로드합니다. 수백만 개의 Blob을 검색하고 이름과 속성을 비교하는 대신 `Project = Contoso`를 검색 기준으로 사용할 수 있습니다. Blob 인덱스는 전체 스토리지 계정에서 모든 컨테이너를 필터링하여 `Project = Contoso`에서 Blob 집합을 50개만 빠르게 찾아서 반환합니다.

> [!IMPORTANT]
> Blob 인덱스 태그는 현재 **미리 보기** 상태이며 모든 공용 지역에서 사용할 수 있습니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Blob 인덱스 사용 방법에 대한 예제를 시작하려면 [Blob 인덱스 태그를 사용하여 데이터 관리 및 찾기](storage-blob-index-how-to.md)를 참조하세요.

## <a name="blob-index-tags-and-data-management"></a>Blob 인덱스 태그 및 데이터 관리

컨테이너 및 Blob 이름 접두사는 1차원 분류입니다. Blob 인덱스 태그를 사용하면 [Blob 데이터 형식(차단, 추가 또는 페이지)](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs)에 대한 다차원 분류가 가능합니다. 다차원 분류는 Azure Blob Storage를 통해 기본적으로 인덱싱되기 때문에 데이터를 신속하게 찾을 수 있습니다.

스토리지 계정에서 다음 5가지 Blob을 살펴보겠습니다.

- *container1/transaction.csv*
- *container2/campaign.docx*
- *photos/bannerphoto.png*
- *archives/completed/2019review.pdf*
- *logs/2020/01/01/logfile.txt*

이러한 Blob은 *container/virtual folder/blob name* 접두사를 사용하여 구분됩니다. 5가지 Blob에 대한 인덱스 태그 특성을 `Project = Contoso`로 설정하면 현재 접두사 구성을 유지하면서 함께 범주화할 수 있습니다. 인덱스 태그를 추가하면 인덱스를 사용하여 데이터를 필터링하고 찾는 기능을 노출하여 데이터를 이동할 필요가 없습니다.

## <a name="setting-blob-index-tags"></a>Blob 인덱스 태그 설정

Blob 인덱스 태그는 스토리지 계정 내의 새 개체 또는 기존 개체에 적용할 수 있는 키-값 특성입니다. 인덱스 태그는 [Put Blob](/rest/api/storageservices/put-blob), [Put Block List](/rest/api/storageservices/put-block-list) 또는 [Copy Blob](/rest/api/storageservices/copy-blob) 작업 및 선택적 `x-ms-tags` 헤더를 사용하여 업로드 프로세스 중에 지정할 수 있습니다. 스토리지 계정에 이미 Blob이 있는 경우 요청 본문에 인덱스 태그와 함께 서식이 지정된 XML 문서를 전달하는 [Set Blob Tags](/rest/api/storageservices/set-blob-tags)을 호출합니다.

> [!IMPORTANT]
> Blob 인덱스 태그 설정은 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 및 Blob의 태그에 액세스할 수 있는 권한(`t` SAS 권한)이 있는 공유 액세스 서명이 있는 모든 사용자가 수행할 수 있습니다.
>
> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write` 권한이 있는 RBAC 사용자도 이 작업을 수행할 수 있습니다.

Blob에 단일 태그를 적용하여 데이터 처리가 완료된 시간을 설명할 수 있습니다.

> "processedDate" = '2020-01-01'

Blob에 여러 태그를 적용하여 데이터를 더 자세히 설명할 수 있습니다.

> "Project" = 'Contoso'  
> "Classified" = 'True'  
> "Status" = 'Unprocessed'  
> "Priority" = '01'

기존 인덱스 태그 특성을 수정하려면 기존 태그 특성을 검색하고, 태그 특성을 수정한 후 [Set Blob Tags](/rest/api/storageservices/set-blob-tags) 작업으로 바꿉니다. Blob에서 모든 인덱스 태그를 제거하려면 태그 속성을 지정하지 않고 `Set Blob Tags` 작업을 호출합니다. Blob 인덱스 태그는 Blob 데이터 콘텐츠에 대한 하위 리소스이므로 `Set Blob Tags`는 기본 콘텐츠를 수정하지 않으며 Blob의 마지막 수정 시간 또는 eTag를 변경하지 않습니다. 모든 현재 기본 Blob 및 이전 버전에 대한 인덱스 태그를 만들거나 수정할 수 있습니다. 단, 스냅샷 또는 일시 삭제된 Blob의 태그는 수정할 수 없습니다.

Blob 인덱스 태그에는 다음과 같은 제한이 적용됩니다.

- 각 Blob에는 최대 10개의 Blob 인덱스 태그가 있을 수 있습니다.
- 태그 키는 1 ~ 128자 사이여야 합니다.
- 태그 값은 0 ~ 256자 사이여야 합니다.
- 태그 키 및 값은 대/소문자를 구분합니다.
- 태그 키 및 값은 문자열 데이터 형식만 지원합니다. 숫자, 날짜, 시간 또는 특수 문자는 문자열로 저장됩니다.
- 태그 키 및 값은 다음 명명 규칙을 따라야 합니다.
  - 영숫자 문자:
    - **a**~**z**(소문자)
    - **A**~**Z**(대문자)
    - **0**~**9**(숫자)
  - 유효한 특수 문자: 공백, 더하기, 빼기, 마침표, 콜론, 같음, 밑줄, 슬래시(` +-.:=_/`)

## <a name="getting-and-listing-blob-index-tags"></a>Blob 인덱스 태그 가져오기 및 나열

Blob 인덱스 태그는 Blob 데이터와 함께 하위 리소스로 저장되며 기본 Blob 데이터 콘텐츠와 독립적으로 검색할 수 있습니다. 단일 Blob에 대한 Blob 인덱스 태그는 [Get Blob Tags](/rest/api/storageservices/get-blob-tags) 작업을 사용하여 검색할 수 있습니다. [List Blobs](/rest/api/storageservices/list-blobs) 작업에 `include:tags` 매개 변수를 사용하면 컨테이너 내의 모든 Blob이 Blob 인덱스 태그와 함께 반환합니다.

> [!IMPORTANT]
> Blob 인덱스 태그 가져오기 및 나열은 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 및 Blob의 태그에 액세스할 수 있는 권한(`t` SAS 권한)이 있는 공유 액세스 서명이 있는 모든 사용자가 수행할 수 있습니다.
>
> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read` 권한이 있는 RBAC 사용자도 이 작업을 수행할 수 있습니다.

Blob 인덱스 태그가 하나 이상 있는 Blob의 경우 `x-ms-tag-count`는 Blob의 인덱스 태그 수를 나타내는 [List Blobs](/rest/api/storageservices/list-blobs), [Get Blob](/rest/api/storageservices/get-blob) 및 [Get Blob Properties](/rest/api/storageservices/get-blob-properties) 작업에서 반환됩니다.

## <a name="finding-data-using-blob-index-tags"></a>Blob 인덱스 태그를 사용하여 데이터 찾기

인덱싱 엔진은 키-값 특성을 다차원 인덱스에 노출합니다. 인덱스 태그를 설정한 후 태그는 Blob에 존재하며 즉시 검색할 수 있습니다. Blob 인덱스가 업데이트되기까지 다소 시간이 걸릴 수 있습니다. Blob 인덱스가 업데이트된 후에는 Blob Storage에서 제공하는 네이티브 쿼리 및 검색 기능을 사용할 수 있습니다.

[Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags) 작업을 사용하면 인덱스 태그가 지정된 쿼리 식과 일치하는 필터링된 Blob 집합을 가져올 수 있습니다. `Find Blobs by Tags`는 스토리지 계정 내의 모든 컨테이너에 대한 필터링을 지원하거나 필터링 범위를 단일 컨테이너로 지정할 수 있습니다. 모든 인덱스 태그 키와 값은 문자열이기 때문에 관계 연산자는 사전식 정렬을 사용합니다.

> [!IMPORTANT]
> Blob 인덱스 태그를 사용하여 데이터 찾기는 [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 및 태그로 Blob을 찾을 수 있는 권한(`f` SAS 권한)이 있는 공유 액세스 서명이 있는 모든 사용자가 수행할 수 있습니다.
>
> `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action` 권한이 있는 RBAC 사용자도 이 작업을 수행할 수 있습니다.

Blob 인덱스 필터링에는 다음 기준이 적용됩니다.

- 태그 키는 큰따옴표(")로 묶어야 합니다.
- 태그 값과 컨테이너 이름은 작은따옴표(')로 묶어야 합니다.
- @ 문자는 특정 컨테이너 이름(예: `@container = 'ContainerName'`)에 대한 필터링에만 허용됩니다.
- 필터는 문자열에 사전식 정렬을 사용하여 적용됩니다.
- 동일한 키에 대한 동일 방향 범위 연산은 유효하지 않습니다(예: `"Rank" > '10' AND "Rank" >= '15'`).
- REST를 사용하여 필터 식을 만들 때 문자는 URI로 인코딩해야 합니다.
- 태그 쿼리는 단일 태그(예: StoreID = "100")를 사용하는 같음 일치에 대해 최적화됩니다.  >, >=, <, <=을 비롯한 단일 태그를 사용하는 범위 쿼리도 효율적입니다. 둘 이상의 태그와 함께 AND를 사용하는 쿼리는 효율적이지 않습니다.  예를 들어 Cost > "01" AND Cost <= "100"은 효율적입니다. Cost > "01 AND StoreID = "2"는 효율적이지 않습니다.

다음 표에는 `Find Blobs by Tags`에 유효한 모든 연산자가 나와 있습니다.

|  연산자  |  Description  | 예제 |
|------------|---------------|---------|
|     =      |     같음     | `"Status" = 'In Progress'` |
|     >      |  보다 큼 | `"Date" > '2018-06-18'` |
|     >=     |  크거나 같음 | `"Priority" >= '5'` |
|     <      |  보다 작음   | `"Age" < '32'` |
|     <=     |  작거나 같음  | `"Company" <= 'Contoso'` |
|    AND     |  논리적 AND  | `"Rank" >= '010' AND "Rank" < '100'` |
| @container | 특정 컨테이너에 대한 범위 | `@container = 'videofiles' AND "status" = 'done'` |

> [!NOTE]
> 태그를 설정하고 쿼리하는 경우 사전순 정렬에 익숙하면 좋습니다.
>
> - 숫자는 문자보다 앞에 정렬됩니다. 숫자는 첫 번째 숫자를 기준으로 정렬됩니다.
> - 대문자가 소문자보다 앞에 정렬됩니다.
> - 기호는 표준이 아닙니다. 일부 기호는 숫자 값보다 앞에 정렬됩니다. 다른 기호는 문자 앞 또는 뒤에 정렬됩니다.

## <a name="conditional-blob-operations-with-blob-index-tags"></a>Blob 인덱스 태그를 사용한 조건부 Blob 작업

REST 버전 2019-10-10 이상에서는 대부분의 [Blob service API](/rest/api/storageservices/operations-on-blobs)가 조건부 헤더인 `x-ms-if-tags`를 지원하기 때문에 지정된 Blob 인덱스 조건이 충족되는 경우에만 작업이 성공합니다. 조건이 충족되지 않으면 `error 412: The condition specified using HTTP conditional header(s) is not met` 오류가 발생합니다.

`x-ms-if-tags` 헤더는 기존의 다른 HTTP 조건부 헤더(If-Match, If-None-Match 등)와 결합될 수 있습니다. 요청에 조건부 헤더가 여러 개 제공되는 경우 작업이 성공하려면 모두 true로 평가되어야 합니다. 모든 조건부 헤더는 논리적 AND와 유효하게 결합됩니다.

아래 표는 조건부 연산에 유효한 연산자를 보여줍니다.

|  연산자  |  Description  | 예제 |
|------------|---------------|---------|
|     =      |     같음     | `"Status" = 'In Progress'` |
|     <>     |   같지 않음   | `"Status" <> 'Done'` |
|     >      |  보다 큼 | `"Date" > '2018-06-18'` |
|     >=     |  크거나 같음 | `"Priority" >= '5'` |
|     <      |  보다 작음   | `"Age" < '32'` |
|     <=     |  작거나 같음  | `"Company" <= 'Contoso'` |
|    AND     |  논리적 AND  | `"Rank" >= '010' AND "Rank" < '100'` |
|     또는     | 논리적 OR   | `"Status" = 'Done' OR "Priority" >= '05'` |

> [!NOTE]
> Blob 작업에 대한 조건부 `x-ms-if-tags` 헤더에 허용되지만 `Find Blobs by Tags` 작업에는 존재하지 않는 두 가지 추가 연산자(같지 않음 및 논리적 OR)가 있습니다.

## <a name="platform-integrations-with-blob-index-tags"></a>Blob 인덱스 태그를 사용한 플랫폼 통합

Blob 인덱스 태그는 Blob 데이터를 분류, 관리 및 검색하는 데 도움이 될 뿐만 아니라 [수명 주기 관리](storage-lifecycle-management-concepts.md)와 같은 다른 Blob Storage 기능과의 통합도 제공합니다.

### <a name="lifecycle-management"></a>수명 주기 관리

수명 주기 관리에서 `blobIndexMatch`를 규칙 필터로 사용하면 데이터를 쿨 계층으로 이동하거나 Blob에 적용된 인덱스 태그를 기반으로 데이터를 삭제할 수 있습니다. 규칙을 더 세분화할 수 있고 지정된 태그 기준과 일치하는 경우에만 Blob을 이동하거나 삭제할 수 있습니다.

Blob 인덱스 일치를 수명 주기 규칙의 독립 실행형 필터 집합으로 설정하여 태그가 지정된 데이터에 작업을 적용할 수 있습니다. 또는 접두사와 Blob 인덱스를 둘 다 결합하여 보다 구체적인 데이터 세트와 일치시킬 수 있습니다. 수명 주기 규칙에 여러 필터를 지정하면 논리적 AND 연산이 적용됩니다. 이 경우 모든 필터 조건이 일치하는 경우에만 동작이 적용됩니다.

다음 샘플 수명 주기 관리 규칙은 *videofiles* 라는 컨테이너의 블록 Blob에 적용됩니다. 이 규칙은 Blob 인덱스 태그 기준인 `"Status" == 'Processed' AND "Source" == 'RAW'`와 일치하는 경우에만 Blob을 보관 스토리지로 계층화합니다.

# <a name="portal"></a>[포털](#tab/azure-portal)

![Azure Portal의 수명 주기 관리에 대한 Blob 인덱스 일치 규칙 예제](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

# <a name="json"></a>[JSON](#tab/json)

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "ArchiveProcessedSourceVideos",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "tierToArchive": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Status",
                            "op": "==",
                            "value": "Processed"
                        },
                        {
                            "name": "Source",
                            "op": "==",
                            "value": "RAW"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ],
                    "prefixMatch": [
                        "videofiles/"
                    ]
                }
            }
        }
    ]
}
```

---

## <a name="permissions-and-authorization"></a>권한 및 권한 부여

Blob 인덱스 태그에 대한 액세스 권한은 다음 방식 중 하나를 사용하여 부여할 수 있습니다.

- Azure RBAC(역할 기반 액세스 제어)를 사용하여 Azure AD(Azure Active Directory) 보안 주체에 권한을 부여합니다. 탁월한 보안과 사용 편의성을 위해 Azure AD를 사용합니다. Blob 작업에서 Azure AD를 사용하는 방법에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Blob 및 큐에 대한 액세스 권한 부여](../common/storage-auth-aad.md)를 참조하세요.
- SAS(공유 액세스 서명)를 사용하여 Blob 인덱스에 대한 액세스를 위임합니다. 공유 액세스 서명에 대한 자세한 내용은 [SAS(공유 액세스 서명)를 사용하여 Azure Storage 리소스에 대한 제한된 액세스 권한 부여](../common/storage-sas-overview.md)를 참조하세요.
- 계정 액세스 키를 사용하여 공유 키로 작업에 권한을 부여합니다. 자세한 내용은 [공유 키를 사용하여 권한 부여](/rest/api/storageservices/authorize-with-shared-key)를 참조하세요.

Blob 인덱스 태그는 Blob 데이터에 대한 하위 리소스입니다. Blob에서 읽거나 쓸 수 있는 권한 또는 SAS 토큰이 있는 사용자에게 Blob 인덱스 태그에 대한 액세스 권한이 없을 수 있습니다.

### <a name="role-based-access-control"></a>역할 기반 액세스 제어

[Azure AD ID](../common/storage-auth-aad.md)를 사용하는 호출자에게는 Blob 인덱스 태그에 대해 다음과 같은 작업 권한이 부여될 수 있습니다.

| Blob 인덱스 태그 작업                                          | Azure RBAC 동작                                                             |
|--------------------------------------------------------------------|-------------------------------------------------------------------------------|
| [Set Blob Tags](/rest/api/storageservices/set-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/write    |
| [Get Blob Tags](/rest/api/storageservices/get-blob-tags)           | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags/read     |
| [Find Blobs by Tags](/rest/api/storageservices/find-blobs-by-tags) | Microsoft.Storage/storageAccounts/blobServices/containers/blobs/filter/action |

인덱스 태그 작업에는 기본 Blob 데이터와 별도로 추가 권한이 필요합니다. [Storage Blob 데이터 소유자](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner) 역할에는 세 가지 Blob 인덱스 태그 작업 모두에 대한 권한이 부여됩니다. [Storage Blob 데이터 읽기 권한자](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader)에게는 `Find Blobs by Tags` 및 `Get Blob Tags` 작업에 대한 권한만 부여됩니다.

### <a name="sas-permissions"></a>SAS 권한

[SAS(공유 액세스 서명)](../common/storage-sas-overview.md)를 사용하는 호출자에게는 Blob 인덱스 태그에 대해 작업할 수 있는 범위가 지정된 권한이 부여될 수 있습니다.

#### <a name="blob-sas"></a>Blob SAS

Blob 인덱스 태그에 대한 액세스를 허용하기 위해 Blob SAS에서 다음 권한이 부여될 수 있습니다. Blob 읽기 및 쓰기 권한만으로는 해당 인덱스 태그를 읽거나 쓰기에 충분하지 않습니다.

| 사용 권한 | URI 기호 | 허용되는 작업                |
|------------|------------|-----------------------------------|
| 인덱스 태그 |     t      | Blob에 대한 인덱스 태그 가져오기 및 설정 |

#### <a name="container-sas"></a>컨테이너 SAS

다음 권한은 Blob 태그에 대한 필터링을 허용하기 위해 컨테이너 SAS에서 부여할 수 있습니다. Blob `Blob List` 권한은 인덱스 태그를 기준으로 Blob을 필터링하기에 충분하지 않습니다.

| 사용 권한 | URI 기호 | 허용되는 작업         |
|------------|------------|----------------------------|
| 인덱스 태그 |     f      | 인덱스 태그가 있는 Blob 찾기 |

## <a name="choosing-between-metadata-and-blob-index-tags"></a>메타데이터와 Blob 인덱스 태그 중에서 선택

Blob 인덱스 태그와 메타데이터 모두 Blob 리소스와 함께 임의의 사용자 정의 키-값 속성을 저장하는 기능을 제공합니다. 두 가지 모두 Blob의 내용을 반환하거나 변경하지 않고 직접 검색하고 설정할 수 있습니다. 메타데이터와 인덱스 태그를 모두 사용할 수 있습니다.

인덱스 태그만 자동으로 인덱싱되고 네이티브 Blob Storage 서비스에서 검색할 수 있습니다. 메타데이터는 기본적으로 인덱싱하거나 검색할 수 없습니다. [Azure Search](../../search/search-blob-ai-integration.md)와 같은 별도의 서비스를 사용해야 합니다. Blob 인덱스 태그에는 기본 Blob 데이터와는 별도로 읽기, 필터링 및 쓰기에 대한 추가 권한이 있습니다. 메타데이터는 Blob과 동일한 권한을 사용하며 [Get Blob](/rest/api/storageservices/get-blob) 및 [Get Blob Properties](/rest/api/storageservices/get-blob-properties) 작업에 의해 HTTP 헤더로 반환됩니다. Blob 인덱스 태그는 [Microsoft 관리형 키](../common/storage-service-encryption.md)를 사용하여 미사용 시 암호화됩니다. 메타데이터는 Blob 데이터에 지정된 것과 동일한 암호화 키를 사용하여 미사용 시 암호화됩니다.

다음 표에는 메타데이터와 Blob 인덱스 태그의 차이점이 요약되어 있습니다.

|              |   메타데이터   |   Blob 인덱스 태그  |
|--------------|--------------|--------------------|
| **제한**      | 숫자 제한 없음, 총 8KB, 대/소문자 구분 안 함 | Blob당 태그 최대 10개, 태그당 768바이트, 대/소문자 구분 |
| **업데이트**    | 보관 계층에서 허용되지 않음, `Set Blob Metadata`는 기존 메타데이터를 모두 대체함, `Set Blob Metadata`는 Blob의 마지막 수정 시간을 변경함 | 모든 액세스 계층에 허용됨, `Set Blob Tags`는 기존 태그를 모두 대체함, `Set Blob Tags`는 Blob의 마지막 수정 시간을 변경하지 않음 |
| **스토리지**     | Blob 데이터와 함께 저장됨 | Blob 데이터의 하위 리소스 |
| **인덱싱 및 쿼리** | Azure Search와 같은 별도의 서비스를 사용해야 함 | 인덱싱 및 쿼리 기능이 Blob Storage에 기본 제공됨 |
| **암호화** | Blob 데이터에 사용되는 동일한 암호화 키를 사용하여 미사용 시 암호화됨 | Microsoft 관리형 암호화 키를 사용하여 미사용 시 암호화됨 |
| **가격** | 메타데이터의 크기가 Blob의 스토리지 비용에 포함됨 | 인덱스 태그당 고정 비용 |
| **헤더 응답** | `Get Blob` 및 `Get Blob Properties`에서 메타데이터가 헤더로 반환됨 | `Get Blob` 또는 `Get Blob Properties`에서는 태그 수가 반환됨, 태그는 `Get Blob Tags` 및 `List Blobs`에서만 반환됨 |
| **권한**  | Blob 데이터에 대한 읽기 또는 쓰기 권한이 메타데이터로 확장됨 | 인덱스 태그를 읽거나 필터링하거나 쓰려면 추가 권한이 필요함 |
| **이름 지정** | 메타데이터 이름은 C# 식별자에 대한 명명 규칙을 따라야 함 | Blob 인덱스 태그는 더 넓은 범위의 영숫자 문자를 지원함 |

## <a name="pricing"></a>가격 책정

Blob 인덱스 가격 책정은 공개 미리 보기 상태이며 일반 공급을 위해 변경될 수 있습니다. 스토리지 계정 내 월 평균 인덱스 태그 수에 대한 요금이 청구됩니다. 인덱싱 엔진에 대한 비용은 없습니다. 블로그 태그 설정, Blob 태그 얻기, Blob 태그 찾기에 대한 요청은 현재 해당 트랜잭션 요율로 청구됩니다. 태그로 Blob 찾기 트랜잭션을 수행하는 경우 사용하는 목록 트랜잭션 수는 요청의 절 수와 같습니다. 예를 들어 (StoreID = 100) 쿼리는 하나의 목록 트랜잭션입니다.  (StoreID = 100 AND SKU = 10010) 쿼리는 두 개의 목록 트랜잭션입니다. [자세한 내용은 블록 Blob 가격 책정](https://azure.microsoft.com/pricing/details/storage/blobs/)을 참조하세요.

## <a name="regional-availability-and-storage-account-support"></a>지역 가용성 및 스토리지 계정 지원

Blob 인덱스 태그는 HNS(계층 구조 네임스페이스)를 사용하지 않는 범용 v2 계정에서만 사용할 수 있습니다. 범용 v1 계정은 지원되지 않지만 범용 v1 계정을 범용 v2 계정으로 업그레이드할 수 있습니다.

프리미엄 스토리지 계정에서는 인덱스 태그가 지원되지 않습니다. 스토리지 계정에 대한 자세한 내용은 [Azure Storage 계정 개요](../common/storage-account-overview.md)를 참조하세요.

Blob 인덱스 태그는 현재 모든 공용 지역에서 사용할 수 있습니다.

시작하려면 [Blob 인덱스 태그를 사용하여 데이터 관리 및 찾기](storage-blob-index-how-to.md)를 참조하세요.

> [!IMPORTANT]
> 스토리지 계정에서 Blob 인덱스 미리 보기를 사용하려면 먼저 구독을 등록해야 합니다. 이 문서의 [조건 및 알려진 문제](#conditions-and-known-issues) 섹션을 참조하세요.

### <a name="register-your-subscription-preview"></a>구독 등록(미리 보기)

Blob 인덱스 태그는 공개 미리 보기 상태이므로 해당 기능을 사용하려면 먼저 구독을 등록해야 합니다. 요청을 제출하려면 다음 PowerShell 또는 CLI 명령을 실행합니다.

#### <a name="register-by-using-powershell"></a>PowerShell을 사용하여 등록

```powershell
Register-AzProviderFeature -FeatureName BlobIndex -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
```

#### <a name="register-by-using-azure-cli"></a>Azure CLI를 사용하여 등록

```azurecli
az feature register --namespace Microsoft.Storage --name BlobIndex
az provider register --namespace 'Microsoft.Storage'
```

## <a name="conditions-and-known-issues"></a>조건 및 알려진 문제

이 섹션에서는 Blob 인덱스 태그 공개 미리 보기의 알려진 문제 및 조건에 대해 설명합니다. 이 기능은 동작이 변경될 수 있으므로 GA(일반 공급)에 도달할 때까지 프로덕션 워크로드에 사용해서는 안 됩니다.

- 미리 보기의 경우 먼저 구독을 등록해야 미리 보기 지역에서 스토리지 계정에 Blob 인덱스를 사용할 수 있습니다.
- 범용 v2 계정만 미리 보기에서 지원됩니다. 프리미엄 블록 Blob, 레거시 Blob, 계층 구조 네임스페이스가 사용되는 계정은 지원되지 않습니다. 범용 v1 계정은 지원되지 않습니다.
- 인덱스 태그가 있는 페이지 Blob을 업로드하면 태그가 유지되지 않습니다. 페이지 Blob을 업로드한 후 태그를 설정하세요.
- 필터링 범위가 단일 컨테이너로 지정되면 필터 식의 모든 인덱스 태그가 같음 검사(키= 값)인 경우에만 `@container`를 전달할 수 있습니다.
- `AND` 조건과 함께 범위 연산자를 사용하는 경우에는 동일한 인덱스 태그 키 이름만 지정할 수 있습니다(`"Age" > '013' AND "Age" < '100'`).
- 버전 관리 및 Blob 인덱스는 지원되지 않습니다. Blob 인덱스 태그는 버전에 대해 유지되지만 Blob 인덱스 엔진으로 전달되지 않습니다.
- 인덱스 태그가 인덱싱되었는지 확인하는 API는 없습니다.
- 계정 장애 조치(failover)는 지원되지 않습니다. 장애 조치(failover) 후에는 Blob 인덱스가 제대로 업데이트되지 않을 수 있습니다.
- 수명 주기 관리는 Blob 인덱스 일치를 사용한 같음 검사만 지원합니다.
- `Copy Blob`은 원본 Blob에서 새 대상 Blob으로 Blob 인덱스 태그를 복사하지 않습니다. 대상 Blob에 적용할 태그는 복사 작업 중에 지정할 수 있습니다.
- 대상 Blob에 적용된 태그가 있는 다른 스토리지 계정에서 `Copy Blob`을 수행하면(비동기 복사) Blob 인덱스 엔진이 필터 집합의 Blob 및 해당 태그를 반환하지 않습니다. URL에서 `Copy Blob`(동기 복사)를 사용하세요.
- 태그는 스냅샷 생성 시 유지됩니다. 단, 스냅샷 승격은 지원되지 않으며, 실행하면 태그 집합이 비워질 수 있습니다.

## <a name="faq"></a>FAQ

**Blob 내의 콘텐츠를 필터링하고 쿼리하는 데 Blob 인덱스가 도움이 되나요?**

아니요, Blob 데이터 내에서 검색해야 하는 경우에는 쿼리 가속 또는 Azure Search를 사용하세요.

**인덱스 태그 값에 대한 요구 사항이 있나요?**

Blob 인덱스 태그는 문자열 데이터 형식만 지원하고 쿼리는 사전순으로 결과를 반환합니다. 숫자의 경우 숫자 앞에 영(0)을 추가합니다. 날짜 및 시간의 경우 ISO 8601 호환 형식으로 저장합니다.

**Blob 인덱스 태그와 Azure Resource Manager 태그가 관련이 있나요?**

아니요, Resource Manager 태그는 구독, 리소스 그룹 및 스토리지 계정 등의 컨트롤 플레인 리소스를 구성하는 데 도움이 됩니다. 인덱스 태그는 데이터 평면에서 Blob 관리 및 검색을 제공합니다.

## <a name="next-steps"></a>다음 단계

Blob 인덱스를 사용하는 방법의 예는 [Blob 인덱스를 사용하여 데이터 관리 및 찾기](storage-blob-index-how-to.md)를 참조하세요.

[수명 주기 관리](storage-lifecycle-management-concepts.md)에 대해 알아보고 Blob 인덱스 일치를 사용하여 규칙을 설정합니다.
