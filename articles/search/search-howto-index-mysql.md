---
title: Azure MySQL에서 데이터 인덱싱(미리 보기)
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search에서 전체 텍스트 검색을 위해 Azure MySQL에 저장된 데이터를 인덱싱하도록 검색 인덱서를 설정합니다.
author: gmndrg
ms.author: gimondra
manager: nitinme
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/17/2021
ms.openlocfilehash: aaf8597887eb7d5f7f416fc2c9aa8731b5b8ce1e
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/06/2021
ms.locfileid: "131892991"
---
# <a name="index-data-from-azure-mysql"></a>Azure MySQL에서 데이터 인덱싱

> [!IMPORTANT] 
> MySQL 지원은 현재 [추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에 따라 공개 미리 보기로 제공됩니다. 이 기능에 대한 [액세스를 요청](https://aka.ms/azure-cognitive-search/indexer-preview)하고 액세스가 활성화된 후 [미리 보기 REST API(2020-06-30-preview 이상)](search-api-preview.md)를 사용하여 콘텐츠를 인덱싱합니다. 현재 SDK 지원 및 포털 지원이 없습니다.

MySQL용 Azure Cognitive Search 인덱서는 Azure에서 MySQL 데이터베이스를 크롤링하고, 검색 가능한 데이터를 추출하고, Azure Cognitive Search에서 인덱싱합니다. 인덱서는 MySQL 데이터베이스에 대한 모든 변경 사항, 업로드 및 삭제를 수행하고 이러한 변경 사항을 검색 인덱스에 반영합니다.

다음 클라이언트를 중 하나를 사용하여 Azure MySQL 인덱서를 설정할 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexer)

이 문서에서는 REST API를 사용합니다. 

## <a name="create-an-azure-mysql-indexer"></a>Azure MySQL 인덱서 만들기

Azure에서 MySQL을 인덱싱하려면 아래 단계를 수행합니다.

### <a name="step-1-create-a-data-source"></a>1단계: 데이터 소스 만들기

데이터 원본을 만들려면 다음 요청을 보냅니다.

```http

    POST https://[search service name].search.windows.net/datasources?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {   
        "name" : "[Data source name]"
        "description" : "[Description of MySQL data source]",
        "type" : "mysql",
        "credentials" : { 
            "connectionString" : 
                "Server=[MySQLServerName].MySQL.database.azure.com; Port=3306; Database=[DatabaseName]; Uid=[UserName]; Pwd=[Password]; SslMode=Preferred;" 
        },
        "container" : { 
            "name" : "[TableName]" 
        },
        "dataChangeDetectionPolicy" : { 
            "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
            "highWaterMarkColumnName": "[HighWaterMarkColumn]"
        }
    }

```

### <a name="step-2-create-an-index"></a>2단계: 인덱스 만들기

대상 Azure Cognitive Search 인덱스가 아직 없으면 만듭니다.

```http

    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
       "name": "[Index name]",
       "fields": [{
         "name": "id",
         "type": "Edm.String",
         "key": true,
         "searchable": false
       }, {
         "name": "description",
         "type": "Edm.String",
         "filterable": false,
         "searchable": true,
         "sortable": false,
         "facetable": false
       }]
    }

```

### <a name="step-3-create-the-indexer"></a>3댠계 - 인덱서 만들기

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

```http

    POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    
    {
        "name" : "[Indexer name]"
        "description" : "[Description of MySQL indexer]",
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]"
    }

```

## <a name="run-indexers-on-a-schedule"></a>일정에 따라 인덱서 실행
일정에 따라 주기적으로 실행되도록 인덱서를 정렬할 수도 있습니다. 이렇게 하려면 인덱서를 만들거나 업데이트할 때 **schedule** 속성을 추가합니다. 아래 예제에서는 인덱서를 업데이트하는 PUT 요청을 보여 줍니다.

```http
    PUT https://[search service name].search.windows.net/indexers/[Indexer name]?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin-key]

    {
        "dataSourceName" : "[Data source name]",
        "targetIndexName" : "[Index name]",
        "schedule" : { 
            "interval" : "PT10M", 
            "startTime" : "2021-01-01T00:00:00Z"
        }
    }
```

**interval** 매개 변수는 필수 사항입니다. 두 개의 연속된 인덱서 실행 간의 시작 시간 간격을 나타냅니다. 허용되는 가장 작은 간격은 5분이고 가장 긴 간격은 1일입니다. 형식은 XSD "dayTimeDuration" 값( [ISO 8601 기간](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) 값의 제한된 하위 집합)이어야 합니다. 해당 패턴은 `P(nD)(T(nH)(nM))`입니다. 예를 들어 15분 간격이면 `PT15M`, 2시간 간격이면 `PT2H`입니다.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서 일정 지정 방법](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="capture-new-changed-and-deleted-rows"></a>새 행, 변경된 행 및 삭제된 행 캡처

Azure Cognitive Search는 **증분 인덱싱** 을 사용하여 전체 테이블을 다시 인덱싱하거나 인덱서가 실행될 때마다 표시될 필요가 없도록 합니다.

<a name="HighWaterMarkPolicy"></a>

### <a name="high-water-mark-change-detection-policy"></a>상위 워터마크 변경 검색 정책

이 변경 검색 정책은 행이 마지막으로 업데이트된 버전 또는 시간을 캡처하는 “상위 워터 마크” 열을 사용합니다. 뷰를 사용하는 경우 상위 워터 마크 정책을 사용해야 합니다. 상위 워터 마크 열은 다음 요구 사항을 충족해야 합니다.

#### <a name="requirements"></a>요구 사항 

* 모든 삽입 시 열의 값을 지정합니다.
* 항목에 대한 모든 업데이트는 열의 값도 변경합니다.
* 삽입 또는 업데이트할 때마다 이 열의 값이 증가합니다.
* 다음 WHERE 및 ORDER BY 절이 포함된 쿼리를 효율적으로 실행할 수 있습니다. `WHERE [High Water Mark Column] > [Current High Water Mark Value] ORDER BY [High Water Mark Column]`

#### <a name="usage"></a>사용량

높은 워터 마크 정책을 사용하려면 다음과 같이 데이터 원본을 만들거나 업데이트합니다.

```http
    {
        "name" : "[Data source name]",
        "type" : "mysql",
        "credentials" : { "connectionString" : "[connection string]" },
        "container" : { "name" : "[table or view name]" },
        "dataChangeDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
           "highWaterMarkColumnName" : "[last_updated column name]"
      }
    }
```

> [!WARNING]
> 원본 테이블의 상위 워터 마크 열에 인덱스가 없는 경우 MySQL 인덱서에서 사용하는 쿼리는 시간 초과될 수 있습니다. 특히 테이블에 행이 많은 경우 `ORDER BY [High Water Mark Column]` 절에 따라 인덱스가 효율적으로 실행돼야 합니다.

### <a name="soft-delete-column-deletion-detection-policy"></a>Soft Delete 열 삭제 검색 정책
원본 테이블에서 행이 삭제된 경우 검색 인덱스에서도 해당 행을 삭제할 수 있습니다. 행이 테이블에서 실제로 제거된 경우 Azure Cognitive Search가 더 이상 존재하지 않는 레코드의 현재 상태를 유추할 방법이 없습니다.  그러나 "일시 삭제" 기술을 사용하여 테이블에서 제거하지 않고 논리적으로 행을 삭제할 수 있습니다. 테이블 또는 뷰에 열을 추가하고 이 열을 사용하여 행을 삭제된 것으로 표시합니다.

일시 삭제 기술을 사용하는 경우 데이터 원본을 만들거나 업데이트할 때 다음과 같이 일시 삭제 정책을 지정할 수 있습니다.

```http
    {
        …,
        "dataDeletionDetectionPolicy" : {
           "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
           "softDeleteColumnName" : "[a column name]",
           "softDeleteMarkerValue" : "[the value that indicates that a row is deleted]"
        }
    }
```

**softDeleteMarkerValue** 는 문자열이어야 합니다. 실제 값의 문자열 표현을 사용하세요. 예를 들어 삭제된 행이 값 1로 표시된 정수 열이 있는 경우 `"1"`을 사용합니다. 삭제된 행이 부울 true 값으로 표시된 BIT 열이 있는 경우 문자열 리터럴 `True` 또는 `true`를 사용합니다. 대소문자는 상관 없습니다.

<a name="TypeMapping"></a>

## <a name="mapping-between-mysql-and-azure-cognitive-search-data-types"></a>MySQL과 Azure Cognitive Search 데이터 형식 사이의 매핑

> [!NOTE]
> 미리 보기는 기하 도형 형식 및 Blob을 지원하지 않습니다.

| MySQL 데이터 형식 | 허용되는 대상 인덱스 필드 유형 |
| --- | --- |
| bool, 부울 | Edm.Boolean, Edm.String |
| tinyint, smallint, mediumint, int, integer, year | Edm.Int32, Edm.Int64, Edm.String |
| bigint | Edm.Int64, Edm.Int64, Edm.String |
| float, double, real | Edm.Double, Edm.String |
| date, datetime, timestamp | Edm.DateTimeOffset, Edm.String |
| char, varchar, tinytext, mediumtext, text, longtext, enum, set, time | Edm.String |
| unsigned numerical data, serial, decimal, dec, bit, blob, binary, geometry | 해당 없음 |


## <a name="next-steps"></a>다음 단계

축하합니다! 인덱서를 사용하여 MySQL을 Azure Cognitive Search와 통합하는 방법을 알아보았습니다.

+ 인덱서에 대한 자세한 내용은 [Azure Cognitive Search에서 인덱서 만들기](search-howto-create-indexers.md)를 참조하세요.
