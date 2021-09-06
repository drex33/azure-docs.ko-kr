---
title: Azure Table Storage에서 데이터 인덱싱
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 Azure Table Storage에 저장된 데이터를 인덱싱하도록 검색 인덱서를 설정합니다.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/26/2021
ms.openlocfilehash: 8469716c5cc6b3b7c70d5eeb5868f81ae6b40198
ms.sourcegitcommit: 7c44970b9caf9d26ab8174c75480f5b09ae7c3d7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/27/2021
ms.locfileid: "112982963"
---
# <a name="index-data-from-azure-table-storage"></a>Azure Table Storage에서 데이터 인덱싱

이 문서에서는 콘텐츠를 추출하고 Azure Cognitive Search에서 검색할 수 있도록 Azure 테이블 인덱서를 구성하는 방법을 보여 줍니다. 이 워크플로는 Azure Cognitive Search에서 검색 인덱스를 만들고 Azure Table Storage에서 추출한 기존 콘텐츠와 함께 로드합니다.

다음 클라이언트를 사용하여 Azure Table Storage 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

이 문서에서는 REST API를 사용합니다. 

## <a name="configure-an-indexer"></a>인덱서 구성

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

[데이터 원본 만들기](/rest/api/searchservice/create-data-source)는 인덱싱할 데이터, 데이터에 액세스하는 데 필요한 자격 증명 및 Azure Cognitional Search가 데이터의 변경 내용을 효율적으로 식별할 수 있도록 하는 정책을 지정합니다.

테이블 인덱싱의 경우 데이터 원본에 다음과 같은 속성이 있어야 합니다.

- **name** 은 검색 서비스 내 데이터 원본의 고유 이름입니다.
- **type** 은 `azuretable`여야 합니다.
- **credentials** 매개 변수는 스토리지 계정 연결 문자열을 포함합니다. 자세한 내용은 [자격 증명 지정](#Credentials) 섹션을 참조하세요.
- **컨테이너** 는 테이블 이름 및 선택적 쿼리를 설정합니다.
    - `name` 매개 변수를 사용하여 테이블 이름을 지정합니다.
    - 필요에 따라 `query` 매개 변수를 사용하여 쿼리를 지정합니다. 

> [!IMPORTANT] 
> 가능한 경우 성능 향상을 위해 PartitionKey에 대한 필터를 사용합니다. 다른 쿼리는 전체 테이블 검색을 수행하므로 대형 테이블에서 성능 저하가 발생합니다. [성능 고려 사항](#Performance) 섹션을 참조하세요.

데이터 원본을 만들려면 다음 요청을 보냅니다.

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "table-datasource",
    "type" : "azuretable",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
}   
```

<a name="Credentials"></a>

#### <a name="ways-to-specify-credentials"></a>자격 증명을 지정하는 방법 ####

테이블에 대한 자격 증명을 제공하는 방법은 다음 중 하나입니다. 

- **관리 ID 연결 문자열**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` 이 연결 문자열에는 계정 키가 필요하지 않지만 [관리 ID를 사용하여 Azure Storage 계정에 연결 설정](search-howto-managed-identities-storage.md) 지침을 수행해야 합니다.
- **전체 액세스 Storage 계정 연결 문자열**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Azure Portal에서 **Storage 계정 블레이드** > **설정** > **키**(클래식 Storage 계정) 또는 **설정** > **액세스 키**(Azure Resource Manager Storage 계정)로 이동하여 연결 문자열을 가져올 수 있습니다.
- **Storage 계정 공유 액세스 서명 연결 문자열**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` 공유 액세스 서명에 컨테이너(이 경우 테이블) 및 개체(테이블 행)에 대한 읽기 권한 및 목록이 있어야 합니다.
-  **테이블 공유 액세스 서명**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` 공유 액세스 서명에는 테이블에 대한 쿼리(읽기) 권한이 있어야 합니다.

스토리지 공유 액세스 서명에 대한 자세한 내용은 [공유 액세스 서명 사용](../storage/common/storage-sas-overview.md)을 참조하세요.

> [!NOTE]
> 공유 액세스 서명 자격 증명을 사용하는 경우 자격 증명이 만료되는 것을 방지하기 위해 갱신된 서명을 사용하여 데이터 원본 자격 증명을 주기적으로 업데이트해야 합니다. 공유 액세스 서명 자격 증명이 만료되면 "연결 문자열에 제공된 사용자 자격 증명이 잘못되었거나 만료되었습니다."와 유사한 오류 메시지와 함께 인덱서가 실패합니다.  

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기

[인덱스 만들기](/rest/api/searchservice/create-index)는 문서의 필드, 특성 및 검색 환경을 형성하는 기타 구성을 지정합니다.

인덱스를 만들려면 다음 요청을 보냅니다.

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
        "name" : "my-target-index",
        "fields": [
        { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
        ]
}
```

### <a name="step-3-create-an-indexer"></a>3단계: 인덱서 만들기

[인덱서 만들기](/rest/api/searchservice/create-indexer)는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다. 

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

```http
POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "table-indexer",
    "dataSourceName" : "table-datasource",
    "targetIndexName" : "my-target-index",
    "schedule" : { "interval" : "PT2H" }
}
```

이 인덱서는 2시간 간격으로 실행됩니다 (일전 간격이 "PT2H"로 설정됩니다.) 30분마다 인덱서를 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다. 인덱서 일정 정의에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 예약](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="handle-field-name-discrepancies"></a>필드 이름 불일치 처리

기존 인덱스의 필드 이름이 테이블의 속성 이름과 달라지는 경우가 종종 있습니다. 필드 매핑을 사용하여 테이블의 속성 이름을 검색 인덱스의 필드 이름에 매핑할 수 있습니다. 필드 매핑에 대해 자세히 알아보려면 [Azure Cognitive Search 인덱서 필드 매핑으로 데이터 원본과 검색 인덱스 간의 차이점 연결](search-indexer-field-mappings.md)을 참조하세요.

## <a name="handle-document-keys"></a>문서 키 처리

Azure Cognitive Search에서 문서 키가 문서를 고유하게 식별합니다. 모든 검색 인덱스는 `Edm.String`형식의 키 필드를 정확히 하나만 포함해야 합니다. 인덱스에 추가할 각 문서에는 키 필드가 필요합니다. (실제로 유일한 필수 필드임)

테이블 행에는 복합 키가 있기 때문에 Azure Cognitive Search는 파티션 키와 행 키 값의 결합인 `Key`라는 합성 필드를 생성합니다. 예를 들어 행의 PartitionKey가 `PK1`이고 RowKey가 `RK1`인 경우 `Key` 필드의 값은 `PK1RK1`이 됩니다.

> [!NOTE]
> `Key` 값은 문서 키의 유효하지 않은 문자(예: 대시)를 포함할 수 있습니다. `base64Encode` [필드 매핑 함수](search-indexer-field-mappings.md#base64EncodeFunction)를 사용하여 잘못된 문자를 처리할 수 있습니다. 또한 이 작업을 수행하는 경우 조회 같은 API 호출에서 문서 키 전달 시 URL 안전 Base64 인코딩을 사용해야 합니다.
>

## <a name="incremental-indexing-and-deletion-detection"></a>증분 인덱싱 및 삭제 감지

일정에 따라 실행되는 테이블 인덱서를 설정한 경우 이 인덱서는 행의 `Timestamp` 값에 따라 새 행 또는 업데이트된 행만 다시 인덱싱합니다. 변경 감지 정책을 지정하지 않아도 됩니다. 증분 인덱싱이 자동으로 사용됩니다.

특정 문서를 인덱스에서 제거해야 함을 나타내려면 일시 삭제 전략을 사용할 수 있습니다. 행을 삭제하는 대신, 행이 삭제됨을 나타내는 속성을 추가하고 데이터 원본에 대해 일시 삭제 감지 정책을 설정합니다. 예를 들어 다음 정책은 행이 값이 `"true"`인 속성 `IsDeleted`를 갖는 경우 삭제되는 행을 고려합니다.

```http
PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-table-datasource",
    "type" : "azuretable",
    "credentials" : { "connectionString" : "<your storage connection string>" },
    "container" : { "name" : "table name", "query" : "<query>" },
    "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
}   
```

<a name="Performance"></a>

## <a name="performance-considerations"></a>성능 고려 사항

기본적으로 Azure Cognitive Search는 쿼리 필터 `Timestamp >= HighWaterMarkValue`를 사용합니다. Azure 테이블의 `Timestamp` 필드에는 보조 인덱스가 없으므로 이러한 유형의 쿼리는 전체 테이블 검색을 요구합니다. 따라서 대형 테이블에서 속도가 느려집니다.

다음은 테이블 인덱싱 성능을 향상시킬 수 있는 두 가지 가능한 방법입니다. 이러한 두 방법 모두 테이블 파티션을 사용합니다. 

- 데이터가 기본적으로 여러 파티션 범위로 분할될 수 있으면 각 파티션 범위에 대한 데이터 원본 및 해당 인덱서를 만듭니다. 이제 각 인덱서는 특정 파티션 범위만 처리하면 되므로 쿼리 성능이 개선됩니다. 인덱싱해야 하는 데이터에 작은 수의 고정 파티션이 있으면 각 인덱서는 파티션 검색만 수행하면 됩니다. 예를 들어 `000`에서 `100` 사이의 키를 갖는 파티션 범위를 처리하는 데이터 원본을 만들려면 다음과 같은 쿼리를 사용합니다. 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- 데이터는 시간별으로 분할하는 경우(예를 들어 새 파티션을 매일 또는 매주 만드는 경우) 다음과 같은 접근 방식을 고려해 보세요. 
    - `(PartitionKey ge <TimeStamp>) and (other filters)` 형식의 쿼리를 사용합니다. 
    - [인덱서 상태 가져오기 API](/rest/api/searchservice/get-indexer-status)를 사용하여 인덱서 진행률을 모니터링하고 최근의 성공적인 상위 워터 마크 값을 기준으로 쿼리의 `<TimeStamp>` 상태를 정기적으로 업데이트합니다. 
    - 이 방법을 사용할 경우 전체 다시 인덱싱을 트리거해야 할 때 인덱서를 다시 설정하는 것 외에도 데이터 원본 쿼리를 다시 설정해야 합니다. 

## <a name="see-also"></a>참고 항목

+ [Azure Cognitive Search의 인덱서](search-indexer-overview.md)
+ [인덱서 만들기](search-howto-create-indexers.md)