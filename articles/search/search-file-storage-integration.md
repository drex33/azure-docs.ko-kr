---
title: Azure Files 데이터 인덱싱(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search 파일 공유의 인덱싱을 자동화하도록 Azure Files 인덱서를 설정합니다.
manager: nitinme
author: mattmsft
ms.author: magottei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 94f1a1760c02401bbca04ac01af5dd9b10093311
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511685"
---
# <a name="index-data-from-azure-files"></a>Azure Files 데이터 인덱싱

> [!IMPORTANT] 
> Azure Files 현재 추가 사용 약관 아래에 공개 미리 [보기로 제공됩니다.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) 미리 [보기 REST API(2021-04-30-preview 이상)를](search-api-preview.md) 사용하여 콘텐츠를 인덱싱합니다. 현재는 포털 지원이 제한적이며 .NET SDK를 지원하지 않습니다.

이 문서에서는 Azure 파일 공유에서 콘텐츠 및 메타데이터를 추출하고 Azure Cognitive Search 검색 인덱스로 보내는 기본 워크플로를 검토합니다. 전체 텍스트 검색을 사용하여 결과 인덱스를 쿼리할 수 있습니다.

> [!NOTE]
> 워크플로 및 컴퍼지션을 이미 잘 알고 있나요? [파일 인덱서를 구성하는 방법은](#configure) 다음 단계입니다.

## <a name="functionality"></a>기능

Azure Cognitive Search의 인덱서는 데이터 원본에서 검색 가능한 데이터 및 메타데이터를 추출하는 크롤러입니다. Azure Files 인덱서가 Azure 파일 공유 및 인덱스 파일에 연결됩니다. 인덱서는 다음과 같은 기능을 제공합니다.

+ Azure 파일 공유의 콘텐츠를 인덱싱합니다.
+ 인덱서가 증분 인덱싱을 지원합니다. 즉, Azure 파일 공유에서 변경된 콘텐츠를 식별하고 이후 인덱싱 실행 시 업데이트된 콘텐츠만 인덱싱합니다. 예를 들어, 처음에 5개의 PDF가 인덱서에서 인덱싱되는 경우 1개가 업데이트되면 인덱서가 다시 실행된 후 업데이트된 1개의 PDF만 인덱싱합니다.
+ 텍스트 및 정규화된 이미지는 기본적으로 인덱싱된 파일에서 추출됩니다. 필요에 따라 추가 콘텐츠 보강을 위해 파이프라인에 기술 세트를 추가할 수 있습니다. 기술 세트에 대한 자세한 내용은 [Azure Cognitive Search의 기술 세트 개념](cognitive-search-working-with-skillsets.md) 문서를 참조하세요.

## <a name="supported-document-formats"></a>지원되는 문서 형식

Azure Cognitive Search Azure Files 인덱서가 다음 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-document-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="required-resources"></a>필요한 리소스

Azure Cognitive Search 및 [Azure Files](https://azure.microsoft.com/services/storage/files/)모두 필요합니다. Azure Files 내에서 원본 콘텐츠를 제공하는 파일 공유가 필요합니다.

<a name="configure"></a>

## <a name="configuring-a-file-indexer"></a>파일 인덱서 구성

Azure 파일 인덱서 는 [Azure Blob 인덱서](search-howto-indexing-azure-blob-storage.md)와 많은 일반적인 구성 옵션을 공유합니다. 예를 들어 Azure 파일 인덱서에서는 단일 파일, [일반 텍스트 파일,](search-howto-index-plaintext-blobs.md) [JSON](search-howto-index-json-blobs.md)파일 및 [암호화된](search-howto-index-encrypted-blobs.md) [파일 에서 여러 검색 문서를 생성할](search-howto-index-one-to-many-blobs.md)수 있습니다. 동일한 구성 [옵션도](search-howto-indexing-azure-blob-storage.md) 대부분 적용됩니다. 중요한 차이점은 아래에 강조 표시되어 있습니다.

## <a name="data-source-definitions"></a>데이터 원본 정의

파일 인덱서와 다른 인덱서 간의 주요 차이점은 인덱서에 할당된 데이터 원본 정의입니다. 데이터 원본 정의는 인덱싱할 콘텐츠에 대한 인증 및 연결에 대한 데이터 원본 형식("type": "azurefile") 및 기타 속성을 지정합니다.

파일 데이터 원본 정의는 아래 예제와 유사합니다.

```http
{
    "name" : "my-file-datasource",
    "type" : "azurefile",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-file", "query" : "<optional-directory-name>" }
}
```

`"credentials"` 속성은 위의 예제와 같이 연결 문자열이거나 다음 섹션에서 설명하는 대체 방법 중 하나일 수 있습니다. `"container"`속성은 Azure Storage 내에서 파일 공유를 제공하며 `"query"` 공유에서 하위 폴더를 지정하는 데 사용됩니다. 데이터 원본 정의에 대한 자세한 내용은 [데이터 원본 만들기(REST)](/rest/api/searchservice/create-data-source)를 참조하세요.

<a name="Credentials"></a>

## <a name="credentials"></a>자격 증명

다음 방법 중 하나로 파일 공유에 대한 자격 증명을 제공할 수 있습니다.

**관리 ID 연결 문자열**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

이 연결 문자열에는 계정 키가 필요하지 않지만 [관리 ID를 사용하여 Azure Storage 계정에 대한 연결 설정](search-howto-managed-identities-storage.md)의 지침을 따라야 합니다.

**전체 액세스 스토리지 계정 연결 문자열**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`.

Azure Portal에서 스토리지 계정 블레이드 > 설정 > 키(클래식 스토리지 계정) 또는 보안 + 네트워킹 > 액세스 키(Azure Resource Manager 스토리지 계정)로 이동하여 연결 문자열을 가져올 수 있습니다.

**스토리지 계정 공유 액세스 서명**(SAS) 연결 문자열: `{ "connectionString" : "BlobEndpoint=https://<your account>.file.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&sp=rl&sr=s;" }`

SAS에는 파일 공유에 대한 목록 및 읽기 권한이 있어야 합니다.

**컨테이너 공유 액세스 서명**`{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.file.core.windows.net/<share name>?sv=2016-05-31&sr=s&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS에는 파일 공유에 대한 목록 및 읽기 권한이 있어야 합니다. 스토리지 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 사용](../storage/common/storage-sas-overview.md)을 참조하세요.

> [!NOTE]
> SAS 자격 증명을 사용하는 경우 자격 증명이 만료되는 것을 방지하기 위해 갱신된 서명을 사용하여 데이터 원본 자격 증명을 주기적으로 업데이트해야 합니다. SAS 자격 증명이 만료되면 "연결 문자열에 제공된 사용자 자격 증명이 잘못되었거나 만료되었습니다."와 유사한 오류 메시지와 함께 인덱서가 실패합니다.

## <a name="indexing-file-metadata"></a>파일 메타데이터 인덱싱

모든 콘텐츠 형식의 파일을 쉽게 정렬할 수 있는 일반적인 시나리오는 각 파일에 대한 사용자 지정 메타데이터 및 시스템 속성을 모두 인덱싱하는 것입니다. 이러한 방식으로 모든 파일에 대한 정보는 문서 유형에 관계없이 인덱싱되고 검색 서비스의 인덱스에 저장됩니다. 그런 다음 새 인덱스 를 사용하여 모든 File Storage 콘텐츠에서 정렬, 필터링 및 facet를 진행할 수 있습니다.

표준 파일 메타데이터 속성은 아래에 나열된 필드로 추출할 수 있습니다. 파일 인덱서가 이러한 파일 메타데이터 속성에 대한 내부 필드 매핑을 자동으로 만듭니다. 인덱스 정의를 사용하려는 필드도 추가해야 하지만 인덱서에서 필드 매핑을 만드는 과정을 생략해도 됩니다.

+ **metadata_storage_name(** ) `Edm.String` - 파일 이름입니다. 예를 들어 /my-share/my-folder/subfolder/resume.pdf 파일이 있는 경우 이 필드의 값은 `resume.pdf` 입니다.
+ **metadata_storage_path(** ) `Edm.String` - 스토리지 계정을 포함한 파일의 전체 URI입니다. 예를 들어 `https://myaccount.file.core.windows.net/my-share/my-folder/subfolder/resume.pdf`
+ **metadata_storage_content_type(** ) `Edm.String` - 파일을 업로드하는 데 사용한 코드에 지정된 콘텐츠 형식입니다. 예들 들어 `application/octet-stream`입니다.
+ **metadata_storage_last_modified(** ) `Edm.DateTimeOffset` - 파일에 대해 마지막으로 수정된 타임스탬프입니다. Azure Cognitive Search 초기 인덱싱 후 모든 것을 다시 인덱싱하지 않도록 이 타임스탬프를 사용하여 변경된 파일을 식별합니다.
+ **metadata_storage_size(** ) `Edm.Int64` - 파일 크기(바이트)입니다.
+ **metadata_storage_content_md5(** ) `Edm.String` - 파일 콘텐츠의 MD5 해시(사용 가능한 경우)입니다.
+ **metadata_storage_sas_token(** ) `Edm.String` - [사용자 지정 기술에서](cognitive-search-custom-skill-interface.md) 파일에 액세스하는 데 사용할 수 있는 임시 SAS 토큰입니다. 이 토큰은 만료될 수 있기 때문에 나중에 사용할 수 있도록 저장해서는 안 됩니다.

## <a name="index-by-file-type"></a>파일 형식별 인덱싱

인덱싱할 문서와 건너뛸 문서를 제어할 수 있습니다.

### <a name="include-documents-having-specific-file-extensions"></a>특정 파일 확장명의 문서 포함

`indexedFileNameExtensions` 인덱서 구성 매개 변수를 사용하여 지정한 파일 이름 확장명을 가진 문서만 인덱싱할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PDF 및 .DOCX 문서만 인덱싱하려면 다음을 수행합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-documents-having-specific-file-extensions"></a>특정 파일 확장명의 문서 제외

`excludedFileNameExtensions` 구성 매개 변수를 사용하여 인덱싱에서 특정 파일 이름 확장명으로 문서를 제외할 수 있습니다. 값은 파일 확장명의 쉼표로 구분된 목록을 포함하는 문자열입니다(선행 점 포함). 예를 들어 .PNG 및 .JPEG 확장명을 가진 Blob을 제외한 모든 문서를 인덱싱하려면 다음을 수행합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [admin key]

{
    ... other parts of indexer definition
    "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions` 및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 Azure Cognitive Search는 먼저 `indexedFileNameExtensions`를 확인한 후 `excludedFileNameExtensions`를 찾습니다. 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외되는 것을 의미합니다.

<a name="deleted-files"></a>

## <a name="detecting-deleted-files"></a>삭제된 파일 검색

초기 검색 인덱스를 만든 후에는 후속 인덱서 작업에서 새 문서와 변경된 문서만 선택하기를 원할 수 있습니다. Azure File Storage 시작된 검색 콘텐츠의 경우 일정을 사용하여 인덱싱을 트리거할 때 변경 내용 검색이 자동으로 발생합니다. 기본적으로 서비스는 파일의 타임스탬프에 따라 결정된 대로 변경된 파일만 다시 인덱스합니다. `LastModified` 검색 인덱서에서 지원하는 다른 데이터 원본과 달리 파일에는 항상 타임스탬프가 있어 변경 검색 정책을 수동으로 설정할 필요가 없습니다.

변경 검색은 기본 제공되지만, 삭제 검색은 그렇지 않습니다. 삭제된 파일을 검색하려면 "일시 삭제" 방법을 사용해야 합니다. 파일을 완전히 삭제하면 해당 문서가 검색 인덱스에서 제거되지 않습니다.

## <a name="soft-delete-using-custom-metadata"></a>사용자 지정 메타데이터를 사용하여 일시 삭제

이 메서드는 파일의 메타데이터를 사용하여 검색 문서를 인덱스에서 제거해야 하는지 여부를 결정합니다. 이 메서드에는 인덱스에서 검색 문서를 삭제한 다음 Azure Storage 파일을 삭제하는 별도의 두 가지 작업이 필요합니다.

File Storage 및 Cognitive Search 따라야 하는 단계가 있지만 다른 기능 의존성도 없습니다. 이 기능은 일반 공급 API에서 지원됩니다.

1. 파일에 사용자 지정 메타데이터 키-값 쌍을 추가하여 논리적으로 삭제되었음을 Azure Cognitive Search.

1. 데이터 원본에서 일시 삭제 사용자 지정 검색 정책을 구성합니다. 예를 들어 다음 정책은 값이 인 메타데이터 속성이 있는 경우 파일을 삭제하는 것으로 간주합니다. `IsDeleted` `true`

    ```http
    PUT https://[service name].search.windows.net/datasources/file-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "file-datasource",
        "type" : "azurefile",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-share", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. 인덱서가 Blob을 처리하고 인덱스에서 해당 문서를 삭제한 후에는 Azure Blob Storage에서 Blob을 삭제할 수 있습니다.

### <a name="reindexing-undeleted-files-using-custom-metadata"></a>삭제되지 않은 파일 다시 인덱싱(사용자 지정 메타데이터 사용)

인덱서가 삭제된 파일을 처리하고 인덱스에서 해당 검색 문서를 제거한 후에 Blob의 `LastModified` 타임스탬프가 마지막 인덱서 실행보다 오래된 경우 나중에 복원하는 경우 해당 파일을 다시 방문하지 않습니다.

해당 문서를 다시 인덱싱하려면 해당 파일에 대한 를 `"softDeleteMarkerValue" : "false"` 변경하고 인덱서를 다시 실행합니다.

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [Azure Files란?](../storage/files/storage-files-introduction.md)
