---
title: 변경 및 삭제된 Blob
titleSuffix: Azure Cognitive Search
description: Azure Blob Storage에서 가져오는 초기 검색 인덱스 빌드 후에는 후속 인덱싱에서 변경되거나 삭제된 Blob만 선택할 수 있습니다. 이 문서에서는 자세한 내용을 설명합니다.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: ab13e8901d507f6499a0eeba336a3f7717795ee7
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892194"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Blob 인덱싱에서 변경 및 삭제 검색(Azure Cognitive Search)

초기 검색 인덱스를 만든 후에는 후속 인덱서 작업에서 새 문서와 변경된 문서만 선택하기를 원할 수 있습니다. Azure Blob Storage 또는 Azure Data Lake Storage Gen2에서 발생하는 검색 콘텐츠의 경우 일정을 사용하여 인덱싱을 트리거하면 변경 검색이 자동으로 실행됩니다. 기본적으로 이 서비스는 Blob의 `LastModified` 타임스탬프에 따라 결정된 대로 변경된 Blob만 다시 인덱싱합니다. 검색 인덱서에서 지원하는 다른 데이터 원본과 달리 Blob에는 항상 타임스탬프가 있으므로 변경 검색 정책을 수동으로 설정하지 않아도 됩니다.

변경 검색은 기본 제공되지만, 삭제 검색은 그렇지 않습니다. 삭제된 문서를 검색하려면 '일시 삭제' 방법을 사용해야 합니다. Blob을 완전히 삭제해도 해당 문서는 검색 인덱스에서 제거되지 않습니다.

일시 삭제 방법을 구현하는 방법에는 두 가지가 있습니다.

+ 네이티브 Blob 일시 삭제(다음 섹션에서 설명)
+ [사용자 지정 메타데이터를 사용하여 일시 삭제](#soft-delete-using-custom-metadata)

> [!NOTE] 
> Azure Data Lake Storage Gen2에서는 디렉터리의 이름을 바꿀 수 있습니다. 디렉터리의 이름을 바꾸면 해당 디렉터리의 Blob에 대한 타임스탬프가 업데이트되지 않습니다. 결과적으로 인덱서는 이러한 Blob을 다시 인덱싱하지 않습니다. 디렉터리 이름 바꾸기 후에 디렉터리의 Blob이 새 URL을 포함하여 인덱싱해야 하는 경우 나중에 실행되는 동안 인덱서가 다시 인덱싱하도록 디렉터리의 모든 Blob에 대한 `LastModified` 타임스탬프를 업데이트해야 합니다. Azure Blob Storage의 가상 디렉터리는 변경할 수 없으므로 이 문제가 없습니다.

## <a name="native-blob-soft-delete-preview"></a>네이티브 Blob 일시 삭제(미리 보기)

이 삭제 검색 방법의 경우 Cognitive Search가 Azure Blob Storage의 [네이티브 Blob 일시 삭제](../storage/blobs/soft-delete-blob-overview.md) 기능을 사용하여 Blob이 일시 삭제된 상태로 전환되었는지 여부를 확인합니다. Blob이 이 상태에서 검색되면 검색 인덱서는 이 정보를 사용하여 인덱스에서 해당 문서를 제거합니다.

> [!IMPORTANT]
> 네이티브 Blob 일시 삭제에 대한 지원은 미리 보기 상태입니다. 미리 보기 기능은 서비스 수준 계약 없이 제공되며, 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요. 이 기능은 [REST API 버전 2020-06-30-미리 보기](./search-api-preview.md)에서 제공됩니다. 현재 포털 또는 .NET SDK는 지원되지 않습니다.

### <a name="prerequisites"></a>필수 조건

+ [Blob에 일시 삭제를 사용하도록 설정합니다](../storage/blobs/soft-delete-blob-enable.md).
+ Blob은 Azure Blob Storage 컨테이너에 있어야 합니다. Cognitive Search 네이티브 Blob 일시 삭제 정책은 Azure Data Lake Storage Gen2의 Blob에서는 지원되지 않습니다.
+ 인덱스 내 문서에 대한 문서 키는 Blob 속성 또는 Blob 메타데이터 중 하나에 매핑되어야 합니다.
+ 미리 보기 REST API(`api-version=2020-06-30-Preview`)를 사용하여 일시 삭제에 대한 지원을 구성해야 합니다.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>네이티브 일시 삭제를 사용하여 삭제 검색을 구성하는 방법

1. Blob Storage에서 일시 삭제를 사용하도록 설정하는 경우 보존 정책을 인덱서 간격 일정보다 훨씬 더 높은 값으로 설정합니다. 이렇게 하면 인덱서를 실행하는 데 문제가 있거나 인덱싱할 문서 수가 많은 경우 인덱서가 일시 삭제된 Blob을 최종적으로 처리할 수 있는 시간이 많아집니다. Azure Cognitive Search 인덱서는 Blob이 일시 삭제된 상태에 있는 동안 Blob을 처리하는 경우에만 인덱스에서 문서를 삭제합니다.

1. Cognitive Search에서 데이터 원본에 대한 네이티브 Blob 일시 삭제 검색 정책을 설정합니다. 아래에 예제가 나와 있습니다. 이 기능은 미리 보기 상태이므로 미리 보기 REST API를 사용해야 합니다.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [인덱서를 실행](/rest/api/searchservice/run-indexer)하거나 인덱서를 [일정에 따라](search-howto-schedule-indexers.md) 실행하도록 설정합니다. 인덱서가 실행하여 일시 삭제 상태의 Blob을 처리하면 해당 검색 문서가 인덱스에서 제거됩니다.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>삭제 취소된 Blob 다시 인덱싱(네이티브 일시 삭제 정책 사용)

Blob Storage에서 일시 삭제된 Blob을 복원하는 경우 인덱서가 항상 다시 인덱싱하지는 않습니다. 이는 인덱서가 Blob의 `LastModified` 타임스탬프를 사용하여 인덱싱이 필요한지 여부를 결정하기 때문입니다. 일시 삭제된 Blob의 삭제를 취소하면 해당 `LastModified` 타임스탬프가 업데이트되지 않으므로 인덱서가 최신 `LastModified` 타임스탬프를 사용하여 Blob을 이미 처리한 경우에는 삭제 취소된 Blob을 다시 인덱싱하지 않습니다. 

삭제 취소된 Blob이 다시 인덱싱되도록 하려면 Blob의 `LastModified` 타임스탬프를 업데이트해야 합니다. 이 작업을 수행하는 한 가지 방법은 해당 Blob의 메타데이터를 다시 저장하는 것입니다. 메타데이터를 변경할 필요는 없지만, 메타데이터를 다시 저장하면 Blob의 `LastModified` 타임스탬프가 업데이트되므로 인덱서가 해당 Blob을 선택해야 함을 알 수 있습니다.

## <a name="soft-delete-using-custom-metadata"></a>사용자 지정 메타데이터를 사용하여 일시 삭제

이 방법은 Blob의 메타데이터를 사용하여 인덱스에서 검색 문서를 제거할지 여부를 결정합니다. 이 방법에는 인덱스에서 검색 문서를 삭제한 다음 Azure Storage에서 Blob을 삭제하는 두 개의 별도 작업이 필요합니다.

Blob Storage 및 Cognitive Search에서 수행해야 하는 단계가 있지만 다른 기능 종속성은 없습니다. 이 기능은 일반 공급 API에서 지원됩니다.

1. 논리적으로 삭제되었음을 Azure Cognitive Search에 나타내는 사용자 지정 메타데이터 키-값 쌍을 Blob에 추가합니다.

1. 데이터 원본에서 일시 삭제 사용자 지정 검색 정책을 구성합니다. 예를 들어 다음 정책은 `true` 값의 메타데이터 속성 `IsDeleted`가 있는 경우 Blob을 삭제해야 하는 것으로 간주합니다.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. 인덱서가 Blob을 처리하고 인덱스에서 해당 문서를 삭제한 후에는 Azure Blob Storage에서 Blob을 삭제할 수 있습니다.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>삭제 취소된 Blob 다시 인덱싱(사용자 지정 메타데이터를 사용)

인덱서가 삭제된 Blob을 처리하고 인덱스에서 해당 검색 문서를 제거한 후 나중에 복원하는 경우 Blob의 `LastModified` 타임스탬프가 마지막 인덱서 실행보다 오래된 경우 인덱서가 해당 Blob을 다시 방문하지 않습니다.

해당 문서를 다시 인덱싱하려는 경우 해당 Blob의 `"softDeleteMarkerValue" : "false"`를 변경하고 인덱서를 다시 실행합니다.

## <a name="next-steps"></a>다음 단계

+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Blob 인덱서 구성 방법](search-howto-indexing-azure-blob-storage.md)
+ [Blob 인덱싱 개요](search-blob-storage-integration.md)