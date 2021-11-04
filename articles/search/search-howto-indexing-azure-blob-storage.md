---
title: Azure Blob Storage의 데이터 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색 작업과 정보 마이닝에 대한 Blob 콘텐츠 인덱싱을 자동화하도록 Azure Blob 인덱서를 설정합니다.
author: nitinme
ms.author: nitinme
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/14/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 13753918ba850dcb9e62c0d54c1f23259eb92ba4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010710"
---
# <a name="index-data-from-azure-blob-storage"></a>Azure Blob Storage의 데이터 인덱싱

이 문서에서는 콘텐츠를 추출하고 Azure Cognitive Search에서 검색할 수 있도록 Azure Blob 인덱서를 구성하는 방법을 보여 줍니다. 이 워크플로는 Azure Cognitive Search에 대한 검색 인덱스를 만들고 Azure Blob Storage에서 추출한 기존 콘텐츠 및 메타데이터와 함께 로드합니다.

Blob 인덱서는 [AI 보강](cognitive-search-concept-intro.md)에서 자주 사용됩니다. AI 보강에서 연결된 [기술 세트](cognitive-search-working-with-skillsets.md)는 이미지 및 자연어 처리를 추가하여 Blob 컨테이너의 검색할 수 없는 콘텐츠 유형에서 검색 가능한 콘텐츠를 만듭니다.

이 문서에서는 텍스트 중심 인덱싱을 위해 Azure Blob 인덱서를 구성하는 방법을 보여 줍니다. 다음 클라이언트를 사용하여 Azure Blob Storage 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

이 문서에서는 REST API를 사용합니다. 

## <a name="supported-access-tiers"></a>지원되는 액세스 계층

Blob Storage [액세스 계층](../storage/blobs/access-tiers-overview.md)에는 핫, 쿨 및 보관이 포함됩니다. 인덱서는 핫 및 쿨만 액세스할 수 있습니다. 

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>지원되는 문서 형식

Azure Cognitive Search Blob 인덱서는 다음 문서 형식에서 텍스트를 추출할 수 있습니다.

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>데이터 원본 정의

Blob 인덱서와 다른 인덱서 간의 주요 차이점은 인덱서에 할당된 데이터 원본 정의입니다. 데이터 원본 정의는 데이터 원본 유형("type": "azureblob") 뿐만 아니라 인덱싱할 콘텐츠에 대한 인증 및 연결의 기타 속성을 지정합니다.

Blob 데이터 원본 정의는 아래 예제와 유사합니다.

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"` 속성은 위의 예제와 같이 연결 문자열이거나 다음 섹션에서 설명하는 대체 방법 중 하나일 수 있습니다. `"container"` 속성은 Azure Storage 내에서의 콘텐츠 위치를 제공하며 `"query"`는 컨테이너에서 하위 폴더를 지정하는 데 사용됩니다. 데이터 원본 정의에 대한 자세한 내용은 [데이터 원본 만들기(REST)](/rest/api/searchservice/create-data-source)를 참조하세요.

<a name="Credentials"></a>

## <a name="credentials"></a>자격 증명

Blob 컨테이너에 대한 자격 증명을 제공하는 방법은 다음 중 하나입니다.

**관리 ID 연결 문자열**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

이 연결 문자열에는 계정 키가 필요하지 않지만 [관리 ID를 사용하여 Azure Storage 계정에 대한 연결 설정](search-howto-managed-identities-storage.md)의 지침을 따라야 합니다.

**전체 액세스 스토리지 계정 연결 문자열**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`.

Azure Portal에서 스토리지 계정 블레이드 > 설정 > 키(클래식 스토리지 계정) 또는 보안 + 네트워킹 > 액세스 키(Azure Resource Manager 스토리지 계정)로 이동하여 연결 문자열을 가져올 수 있습니다.

**스토리지 계정 공유 액세스 서명**(SAS) 연결 문자열: `{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

SAS에 컨테이너 및 개체(이 경우 Blob)에 대한 읽기 권한 및 목록이 있어야 합니다.

**컨테이너 공유 액세스 서명**`{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS에 컨테이너에 대한 읽기 권한 및 목록이 있어야 합니다. 스토리지 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 사용](../storage/common/storage-sas-overview.md)을 참조하세요.

> [!NOTE]
> SAS 자격 증명을 사용하는 경우 자격 증명이 만료되는 것을 방지하기 위해 갱신된 서명을 사용하여 데이터 원본 자격 증명을 주기적으로 업데이트해야 합니다. SAS 자격 증명이 만료되면 "연결 문자열에 제공된 사용자 자격 증명이 잘못되었거나 만료되었습니다."와 유사한 오류 메시지와 함께 인덱서가 실패합니다.  

## <a name="index-definitions"></a>인덱스 정의

인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다. 모든 인덱서에서는 검색 인덱스 정의를 대상으로 지정해야 합니다. 다음 예제에서는 [인덱스 만들기(REST API)](/rest/api/searchservice/create-index)를 사용하여 단순 인덱스를 만듭니다. 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

인덱스 정의에서는 `"fields"` 컬렉션에 문서 키 역할을 하는 필드가 하나 필요합니다. 인덱스 정의에는 콘텐츠 및 메타데이터에 대한 필드도 포함되어야 합니다.

**`content`** 필드는 Blob 콘텐츠에 공통적입니다. 여기에는 Blob에서 추출된 텍스트가 포함됩니다. 이 필드의 정의는 위와 유사할 수 있습니다. 이 이름을 사용할 필요는 없지만 암시적 필드 매핑을 활용할 수 있습니다. Blob 인덱서는 인덱스의 콘텐츠 Edm.String 필드에 Blob 콘텐츠를 보낼 수 있으며 필드 매핑은 필요하지 않습니다.

인덱스에 원하는 모든 Blob 메타데이터에 대한 필드를 추가할 수도 있습니다. 인덱서는 사용자 지정 메타데이터 속성, [표준 메타데이터](#indexing-blob-metadata) 속성 및 [콘텐츠 관련 메타데이터](search-blob-metadata-properties.md) 속성을 읽을 수 있습니다. 인덱스에 대한 자세한 내용은 [인덱스 만들기](search-what-is-an-index.md)를 참조하세요.

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>문서 키 및 필드 매핑 정의

검색 인덱스에서 문서 키는 각 문서를 고유하게 식별합니다. 선택한 필드는 `Edm.String` 형식이어야 합니다. Blob 콘텐츠의 경우 문서 키에 가장 적합한 후보는 Blob의 메타데이터 속성입니다.

+ **`metadata_storage_name`** - 이 속성은 하나의 후보이지만, 인덱싱할 모든 컨테이너 및 폴더에서 이름이 고유한 경우에만 사용할 수 있습니다. Blob 위치에 관계 없이 최종적으로 모든 콘텐츠가 인덱싱되면 문서 키(이름)가 검색 인덱스에서 고유해야 합니다. 

  스토리지 이름에 대한 또 다른 잠재적인 문제는 대시와 같이 문서 키에 유효하지 않은 문자를 포함할 수 있다는 것입니다. `base64Encode`[필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 잘못된 문자를 처리할 수 있습니다. 이 작업을 수행하는 경우 문서 키를 [조회 문서(REST)](/rest/api/searchservice/lookup-document)와 같은 API 호출로 전달하는 경우에도 문서 키를 인코딩해야 합니다. .NET에서는 [UrlTokenEncode 메서드](/dotnet/api/system.web.httpserverutility.urltokenencode)를 사용하여 문자를 인코딩할 수 있습니다.

+ **`metadata_storage_path`** - 전체 경로를 사용하여 고유성을 보장할 수 있지만 해당 경로에 [문서 키로 유효하지 않은](/rest/api/searchservice/naming-rules) `/` 문자가 분명히 포함될 수 있습니다. 위와 같이 `base64Encode` [함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 문자를 인코딩할 수 있습니다.

+ 세 번째 옵션은 Blob에 사용자 지정 메타데이터 속성을 추가하는 것입니다. 이 옵션에는 해당 메타데이터 속성을 모든 BLOB에 추가하는 BLOB 업로드 프로세스가 필요합니다. 키는 필수 속성이므로 값이 누락된 Blob은 인덱싱되지 않습니다.

> [!IMPORTANT]
> 인덱스의 키 필드에 대한 명시적인 매핑이 없는 경우 Azure Cognitive Search에서 자동으로 `metadata_storage_path`를 키로 사용하고 키 값을 base-64로 인코딩합니다(위 두 번째 옵션).
>
> 사용자 지정 메타데이터 속성을 키로 사용하는 경우 해당 속성을 변경하지 마세요. 키 속성이 변경되면 인덱서는 동일한 Blob에 대해 중복 문서를 추가합니다.

#### <a name="example"></a>예제

다음 예제에서는 `metadata_storage_name`을 문서 키로 보여 줍니다. 인덱스에 `key`라는 키 필드와 문서 크기를 저장하는 `fileSize`라는 또 다른 필드가 있다고 가정해보겠습니다. 인덱서 정의의 [필드 매핑](search-indexer-field-mappings.md)은 필드 연결을 설정하고 `metadata_storage_name`에는 지원되지 않는 문자를 처리하는 [`base64Encode` 필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)가 있습니다.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>인코딩된 필드를 "검색 가능"하게 만드는 방법

`metadata_storage_path`와 같은 필드의 인코딩된 버전을 키로 사용해야 하지만 검색 인덱스에서 해당 필드를 검색할 수 있어야(인코딩 없이) 하는 경우가 있습니다. 두 사용 사례를 모두 지원하기 위해 `metadata_storage_path`를 두 필드에 매핑할 수 있습니다. 하나는 키(인코딩)를 위한 것이고, 또 다른 하나는 인덱스 스키마에서 "검색 가능한" 특성으로 간주할 수 있는 경로 필드를 위한 것입니다. 아래 예제에서는 `metadata_storage_path`에 대한 두 개의 필드 매핑을 보여 줍니다.

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>인덱스 콘텐츠 및 메타데이터

Blob에는 콘텐츠와 메타데이터가 포함됩니다. `dataToExtract` 구성 매개 변수를 사용하여 Blob에서 인덱싱할 부분을 제어할 수 있습니다. 사용되는 값은 다음과 같습니다.

+ `contentAndMetadata` - Blob에서 추출한 모든 메타데이터 및 텍스트 콘텐츠가 인덱싱되도록 지정합니다. 이것은 기본값입니다.

+ `storageMetadata` - [표준 BLOB 속성 및 사용자가 지정한 메타데이터](../storage/blobs/storage-blob-container-properties-metadata.md)만 인덱싱되도록 지정합니다.

+ `allMetadata` - 표준 Blob 속성 및 [찾은 콘텐츠 형식에 대한 메타데이터](search-blob-metadata-properties.md)를 Blob 콘텐츠에서 추출하고 인덱싱하도록 지정합니다.

예를 들어 스토리지 메타데이터만 인덱싱하려면 다음을 사용합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>Blob 콘텐츠 인덱싱

기본적으로 JSON 또는 CSV와 같이 구조화된 콘텐츠를 포함하는 Blob은 단일 텍스트 청크로 인덱싱됩니다. 그러나 JSON 또는 CSV 문서에 내부 구조(구분 기호)가 있는 경우 구문 분석 모드를 할당하여 각 줄 또는 요소에 대한 개별 검색 문서를 생성할 수 있습니다. 자세한 내용은 [JSON Blob 인덱싱](search-howto-index-json-blobs.md) 및 [CSV Blob 인덱싱](search-howto-index-csv-blobs.md)을 참조하세요.

복합 또는 포함된 문서(예: ZIP 보관 파일, 첨부 파일이 있는 Outlook 메일이 포함된 Word 문서 또는 첨부 파일이 포함된 .MSG 파일)도 단일 문서로 인덱싱됩니다. 예를 들어 .MSG 첨부 파일에서 추출한 모든 이미지는 normalized_images 필드에 반환됩니다.

문서의 텍스트 콘텐츠가 `content`라는 문자열 필드로 추출됩니다.

  > [!NOTE]
  > Azure Cognitive Search는 가격 책정 계층에 따라 추출하는 텍스트의 양을 제한합니다. 현재 [서비스 제한](search-limits-quotas-capacity.md#indexer-limits)은 무료 계층의 경우 32,000, 기본 계층의 경우 64,000, 표준 계층의 경우 400만, 표준 S2 계층의 경우 800만, 표준 S3 계층의 경우 1,600만자입니다. 잘린 문서의 인덱서 상태 응답에는 경고가 포함되어 있습니다.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>Blob 메타데이터 인덱싱

인덱서는 Blob 메타데이터를 인덱싱할 수도 있습니다. 첫째, 사용자 지정 메타데이터 속성은 있는 그대로 추출할 수 있습니다. 값을 받으려면 Blob의 메타데이터 키와 이름이 같은 `Edm.String` 형식의 검색 인덱스에 필드를 정의해야 합니다. 예를 들어, Blob의 메타데이터 키 `Sensitivity`의 값이 `High`인 경우 검색 인덱스에 `Sensitivity`라는 필드를 정의하면 `High` 값으로 채워집니다.

둘째, 표준 Blob 메타데이터 속성은 아래 나열된 필드로 추출할 수 있습니다. Blob 인덱서는 이러한 Blob 메타데이터 속성에 대한 내부 필드 매핑을 자동으로 만듭니다. 인덱스 정의를 사용하려는 필드도 추가해야 하지만 인덱서에서 필드 매핑을 만드는 과정을 생략해도 됩니다.

  + **metadata_storage_name**(`Edm.String`) - Blob의 파일 이름입니다. 예를 들어 blob /my-container/my-folder/subfolder/resume.pdf를 포함하는 경우 이 필드의 값은 `resume.pdf`입니다.

  + **metadata_storage_path**(`Edm.String`) - 스토리지 계정을 포함하는 Blob의 전체 URI입니다. 예를 들어 `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type**(`Edm.String`) - Blob을 업로드하기 위해 사용한 코드에 지정된 콘텐츠 형식입니다. 예들 들어 `application/octet-stream`입니다.

  + **metadata_storage_last_modified**(`Edm.DateTimeOffset`) - Blob에 대해 마지막으로 수정된 타임스탬프입니다. Azure Cognitive Search는 이 타임스탬프로 변경된 BLOB을 식별하여 초기 인덱싱 후 모든 항목을 다시 인덱싱하는 것을 방지합니다.

  + **metadata_storage_size**(`Edm.Int64`) - Blob 크기(바이트)입니다.

  + **metadata_storage_content_md5**(`Edm.String`) - Blob 콘텐츠의 MD5 해시(사용 가능한 경우)입니다.

  + **metadata_storage_sas_token**(`Edm.String`) - [사용자 지정 기술](cognitive-search-custom-skill-interface.md)이 Blob에 액세스하는 데 사용할 수 있는 임시 SAS 토큰입니다. 이 토큰은 만료될 수 있으므로 나중에 사용하기 위해 저장할 필요가 없습니다.

마지막으로 인덱싱 중인 Blob의 문서 형식과 관련된 모든 메타데이터 속성을 인덱스 스키마에 표시할 수도 있습니다. 콘텐츠 관련 메타데이터에 대한 자세한 내용은 [콘텐츠 메타데이터 속성](search-blob-metadata-properties.md)을 참조하세요.

검색 인덱스에서 위의 모든 속성에 대한 필드를 정의하지 않아도 되는 경우 애플리케이션에 필요한 속성만 캡처하는 것이 중요합니다.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>인덱싱할 Blob을 제어하는 방법

Blob의 파일 형식에 따라 인덱싱할 Blob 및 건너뛸 Blob을 제어하거나, Blob 자체에서 해당 속성을 설정하여 인덱서에서 이러한 과정을 건너뛰기 할 수 있습니다.

### <a name="include-specific-file-extensions"></a>특정 파일 확장명 포함

`indexedFileNameExtensions`를 사용하여 인덱싱할 쉼표로 구분된 파일 확장명(선행 점 포함) 목록을 제공합니다. 예를 들어 .PDF 및 .DOCX Blob만을 인덱싱하려면 다음을 수행합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>특정 파일 확장명 제외

`excludedFileNameExtensions`를 사용하여 건너뛸 쉼표로 구분된 파일 확장명(선행 점 포함) 목록을 제공합니다. 예를 들어 .PNG 및 .JPEG 확장명을 가진 Blob을 제외한 모든 Blob을 인덱싱하려면 다음을 수행합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions` 및 `excludedFileNameExtensions` 매개 변수가 모두 있는 경우 인덱서는 먼저 `indexedFileNameExtensions`를 확인한 후 `excludedFileNameExtensions`를 찾습니다. 동일한 파일 확장명이 두 목록 모두에 있는 경우 인덱싱에서 제외됩니다.

### <a name="add-skip-metadata-the-blob"></a>Blob에 "skip" 메타데이터 추가

인덱서 구성 매개 변수는 컨테이너 또는 폴더의 모든 Blob에 적용됩니다. 어떤 때는 *개별 Blob* 이 인덱싱되는 방식을 제어해야 하는 경우도 있습니다. Blob 스토리지의 Blob에 다음 메타데이터 속성 및 값을 추가하여 이 작업을 수행할 수 있습니다. 인덱서는 이 속성을 발견할 경우 인덱싱 실행 시 Blob 또는 해당 콘텐츠를 건너뜁니다.

| 속성 이름 | 속성 값 | 설명 |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |BLOB 인덱서가 BLOB을 완전히 건너뛰도록 지시합니다. 메타데이터와 콘텐츠 추출이 모두 시도되지 않습니다. 특정 BLOB이 반복적으로 실패하고 인덱싱 프로세스가 중단되는 경우에 유용합니다. |
| `AzureSearch_SkipContent` |`"true"` |특정 Blob으로 범위가 지정된 [위에](#PartsOfBlobToIndex) 설명된 `"dataToExtract" : "allMetadata"` 설정과 동일합니다. |

## <a name="index-large-datasets"></a>큰 데이터 집합 인덱싱

BLOB 인덱싱은 시간이 오래 걸리는 프로세스입니다. 인덱싱할 Blob이 수백만 개인 경우에는 데이터를 분할하고 [데이터를 병렬로 처리](search-howto-large-index.md#parallel-indexing)하도록 여러 인덱서를 사용하여 인덱싱 속도를 높일 수 있습니다. 설정 방법은 다음과 같습니다.

+ 데이터를 여러 BLOB 컨테이너 또는 가상 폴더로 분할합니다.

+ 데이터 원본을 컨테이너 또는 폴더당 하나씩 여러 개 설정합니다. BLOB 폴더를 가리키려면 `query` 매개 변수를 사용합니다.

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ 각 데이터 소스에 해당하는 인덱서를 만듭니다. 모든 인덱서가 동일한 대상 검색 인덱스를 가리켜야 합니다.  

+ 서비스에서 하나의 검색 단위가 지정된 시점에 하나의 인덱서를 실행할 수 있습니다. 위에서 설명한 대로 여러 인덱서를 만들면 실제 병렬로 실행하는 경우에 유용합니다.

  동시에 여러 인덱서를 실행하려면 적절한 수의 파티션 및 복제본을 만들어서 검색 서비스를 확장합니다. 예를 들어 검색 서비스에 6개의 검색 단위(예: 2개 파티션x3개 복제본)가 있으면 6개의 인덱서가 동시에 실행될 수 있고 그로 인해 인덱싱 처리량이 6배 증가합니다. 스케일링 및 용량 계획에 대해 자세히 알아보려면 [Azure Cognitive Search 서비스의 용량 조정](search-capacity-planning.md)을 참조하세요.

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>오류 처리

인덱싱 중에 일반적으로 발생하는 오류에는 지원되지 않는 콘텐츠 형식, 누락된 콘텐츠 또는 크기가 너무 큰 Blob이 포함됩니다.

기본적으로 Blob 인덱서는 지원되지 않는 콘텐츠 형식(예: 이미지)을 포함하는 Blob을 발견하는 즉시 중지됩니다. `excludedFileNameExtensions` 매개 변수를 사용하여 특정 콘텐츠 형식을 건너뛸 수 있습니다. 그러나 오류가 발생한 경우에도 인덱싱을 진행하고, 나중에 개별 문서를 디버그하려고 할 수 있습니다. 인덱서 오류에 관한 자세한 내용은 [인덱서 문제 해결 지침](search-indexer-troubleshooting.md)과 [인덱서 오류 및 경고](cognitive-search-common-errors-warnings.md)를 참조하세요.

### <a name="respond-to-errors"></a>오류에 대응

오류가 발생할 때 인덱서의 응답을 제어하는 네 가지 인덱서 속성이 있습니다. 다음 예에서는 인덱서 정의에서 이러한 속성을 설정하는 방법을 보여 줍니다. 인덱서가 이미 있으면 포털에서 정의를 편집하여 이러한 속성을 추가할 수 있습니다.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` 및 `"maxFailedItemsPerBatch"`

또한 Blob을 구문 분석하거나 문서를 인덱스를 추가할 때 임의 처리 지점에서 오류가 발생하는 경우에도 인덱싱을 계속 진행합니다. 이러한 속성을 허용 가능한 오류 수로 설정합니다. `-1` 값을 사용하면 오류가 발생하는 횟수에 관계 없이 처리를 수행할 수 있습니다. 그렇지 않으면 이 값은 양의 정수입니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` 및 `"failOnUnprocessableDocument"` 

일부 문서의 경우 Azure Search는 콘텐츠 형식을 확인할 수 없거나 지원되는 다른 콘텐트 형식의 문서를 처리할 수 없습니다. 이러한 오류 조건을 무시하려면 구성 매개 변수를 `false`로 설정합니다.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>인덱서 제약 조건 완화

오류 조건이 있는지 여부를 효과적으로 확인하는 [Blob 구성 속성](/rest/api/searchservice/create-indexer#blob-configuration-parameters)을 설정할 수도 있습니다. 다음 속성은 제약 조건을 완화시켜 발생할 수 있는 오류를 억제할 수 있습니다.

+ `"indexStorageMetadataOnlyForOversizedDocuments"`: 너무 커서 처리할 수 없는 Blob 콘텐츠에 대한 스토리지 메타데이터를 인덱싱합니다. 너무 큰 Blob은 기본적으로 오류로 처리됩니다. Blob 크기에 제한은 [서비스 제한](search-limits-quotas-capacity.md)을 참조하세요.

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [인덱서 만들기](search-howto-create-indexers.md)
+ [Blob에 대한 AI 보강 개요](search-blob-ai-integration.md)
+ [Blob 검색 개요](search-blob-storage-integration.md)