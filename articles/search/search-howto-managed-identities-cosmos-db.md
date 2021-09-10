---
title: 관리 ID를 사용하여 Cosmos DB 계정 연결 설정
titleSuffix: Azure Cognitive Search
description: 관리 ID를 사용하여 Cosmos DB 계정에 인덱서 연결을 설정하는 방법 알아보기
author: markheff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/02/2021
ms.openlocfilehash: 38709d7799d09e38fdebc8eebd7484504ce4ebd2
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122537755"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity"></a>관리 ID를 사용하여 Cosmos DB 데이터베이스에 인덱서 연결 설정

이 페이지에서는 데이터 원본 개체 연결 문자열에 자격 증명을 제공하는 대신 관리 ID를 사용하여 Azure Cosmos DB 데이터베이스에 인덱서 연결을 설정하는 방법을 설명 합니다.

시스템이 할당한 관리 ID나 사용자가 할당한 관리 ID(미리 보기)를 사용할 수 있습니다.

이 기능을 학습하기 전에 인덱서가 무엇인지와 데이터 원본에 대해 인덱서를 설정하는 방법을 이해하는 것이 좋습니다. 자세한 내용은 다음 링크에서 확인할 수 있습니다.

* [인덱서 개요](search-indexer-overview.md)
* [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)

## <a name="1---set-up-a-managed-identity"></a>1 - 관리 ID 설정

다음 옵션 중 하나를 사용하여 [관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 설정합니다.

### <a name="option-1---turn-on-system-assigned-managed-identity"></a>옵션 1 - 시스템이 할당한 관리 ID 켜기

시스템 할당 관리 ID 사용이 설정되면 Azure는 동일한 테넌트 및 구독 내에서 다른 Azure 서비스에 인증하는 데 사용할 수 있는 검색 서비스 ID를 만듭니다. 그런 다음, 인덱싱 중에 데이터 액세스를 허용하는 Azure RBAC(Azure 역할 기반 액세스 제어) 할당에서 이 ID를 사용할 수 있습니다.

![시스템 할당 관리 ID 켜기](./media/search-managed-identities/turn-on-system-assigned-identity.png "시스템 할당 관리 ID 켜기")

**저장** 을 선택한 후 검색 서비스에 할당된 개체 ID가 표시됩니다.

![개체 ID](./media/search-managed-identities/system-assigned-identity-object-id.png "개체 ID입니다.")
 
### <a name="option-2---assign-a-user-assigned-managed-identity-to-the-search-service-preview"></a>옵션 2 - 사용자가 할당한 관리 ID를 검색 서비스(미리 보기)에 할당

사용자가 할당한 관리 ID를 아직 만들지 않은 경우 새로 만들어야 합니다. 사용자가 할당한 관리 ID는 Azure 리소스입니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.
1. **+ 리소스 만들기** 를 선택합니다.
1. “검색 서비스 및 마켓플레이스” 검색 창에서 “사용자가 할당한 관리 ID”를 검색하고 **만들기** 를 선택합니다.
1. ID에 설명이 포함된 이름을 지정합니다.

그런 다음, 사용자가 할당한 관리 ID를 검색 서비스에 할당합니다. 이 작업은 [2021-04-01-preview 관리 API](/rest/api/searchmanagement/2021-04-01-preview/services/create-or-update)를 사용하여 수행할 수 있습니다.

ID 속성은 유형과 하나 이상의 정규화된 사용자 할당 ID를 사용합니다.

* **type** 은 ID 유형입니다. 유효한 값은 “SystemAssigned”, “UserAssigned” 또는 둘 다 사용하려는 경우 “SystemAssigned, UserAssigned”입니다. “None” 값은 검색 서비스에서 이전에 할당한 ID를 모두 지웁니다.
* **userAssignedIdentities** 에는 사용자가 할당한 관리 ID의 세부 정보가 포함됩니다.
    * 사용자가 할당한 관리 ID 형식: 
        * /subscriptions/**구독 ID**/resourcegroups/**리소스 그룹 이름**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**관리 ID 이름**

사용자가 할당한 관리 ID를 검색 서비스에 할당하는 방법 예제:

```http
PUT https://management.azure.com/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.Search/searchServices/[search service name]?api-version=2021-04-01-preview
Content-Type: application/json

{
  "location": "[region]",
  "sku": {
    "name": "[sku]"
  },
  "properties": {
    "replicaCount": [replica count],
    "partitionCount": [partition count],
    "hostingMode": "default"
  },
  "identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[name of managed identity]": {}
    }
  }
} 
```
 
## <a name="2---add-a-role-assignment"></a>2 - 역할 할당 추가

이 단계에서는 Cosmos DB 데이터베이스에서 데이터를 읽는 권한을 Azure Cognitive Search 서비스나 사용자가 할당한 관리 ID에 부여합니다.

1. Azure Portal에서 인덱싱할 데이터가 포함된 Cosmos DB 계정으로 이동합니다.
2. **액세스 제어(IAM)** 를 선택합니다.
3. **추가** 를 선택한 다음 **역할 할당 추가** 를 선택합니다.

    ![역할 할당 추가](./media/search-managed-identities/add-role-assignment-cosmos-db.png "역할 할당 추가")

4. **Cosmos DB 계정 독자 역할** 을 선택합니다.
5. **액세스 할당** 을 **Azure AD 사용자, 그룹 또는 서비스 보안 주체** 로 둡니다.
6. 시스템이 할당한 관리 ID를 사용하는 경우 검색 서비스를 검색하여 선택합니다. 사용자가 할당한 관리 ID를 사용하는 경우 사용자가 할당한 관리 ID의 이름을 검색하여 선택합니다. **저장** 을 선택합니다.

    시스템이 할당한 관리 ID를 사용하는 Cosmos DB 예제:

    ![읽기 및 데이터 액세스 역할 할당 추가](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "읽기 및 데이터 액세스 역할 할당 추가")

## <a name="3---create-the-data-source"></a>3 - 데이터 원본 만들기

데이터 원본을 만들고 시스템이 할당한 관리 ID나 사용자가 할당한 관리 ID(미리 보기)를 제공합니다. 아래 단계에서는 더 이상 관리 REST API를 사용하지 않습니다.

### <a name="option-1---create-the-data-source-with-a-system-assigned-managed-identity"></a>옵션 1 - 시스템이 할당한 관리 ID를 사용하여 데이터 원본 만들기

[REST API](/rest/api/searchservice/create-data-source), Azure Portal, [.NET SDK](/dotnet/api/azure.search.documents.indexes.models.searchindexerdatasourcetype)는 시스템이 할당한 관리 ID 사용을 지원합니다. 다음은 [REST API](/rest/api/searchservice/create-data-source) 및 관리 ID 연결 문자열을 사용하여 Cosmos DB에서 데이터를 인덱싱하는 데이터 원본을 만드는 방법에 대한 예입니다. 관리 ID 연결 문자열 형식은 REST API, .NET SDK 및 Azure Portal에 대해 동일합니다.

관리 ID를 사용하여 인증을 받는 경우 **자격 증명** 에 계정 키가 포함되지 않습니다.

```http
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

| 필드   | 설명 |
|---------|-------------|
| **name** | 필수 사항입니다. 데이터 원본 개체를 나타낼 이름을 선택합니다. |
|**type**| 필수 사항입니다. `cosmosdb`이어야 합니다. |
|**credentials** | 필수 사항입니다. <br/><br/>관리 ID를 사용하여 연결하는 경우 **자격 증명** 형식은 다음과 같아야 합니다. *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId 형식: *ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL 컬렉션의 경우 연결 문자열에 ApiKind가 필요하지 않습니다.<br/><br/>MongoDB 컬렉션의 경우 **ApiKind=MongoDb** 를 연결 문자열에 추가합니다. <br/><br/>Gremlin 그래프의 경우 [제어된 인덱서 미리 보기](https://aka.ms/azure-cognitive-search/indexer-preview)에 등록하여 미리 보기 액세스와 자격 증명 형식 지정 방법에 대한 정보를 얻습니다.<br/>|
| **container** | 다음과 같은 요소가 있습니다. <br/>**name**: 필수 사항입니다. 인덱싱할 데이터베이스 컬렉션의 ID를 지정합니다.<br/>**query**: (선택 사항) 추상 JSON 문서를 Azure Cognitive Search가 인덱싱할 수 있는 평면 스키마로 평면화하는 쿼리를 지정할 수 있습니다.<br/>MongoDB API와 Gremlin API에서는 쿼리가 지원되지 않습니다. |
| **dataChangeDetectionPolicy** | 권장 |
|**dataDeletionDetectionPolicy** | 옵션 |

### <a name="option-2---create-the-data-source-with-a-user-assigned-managed-identity"></a>옵션 2 - 사용자가 할당한 관리 ID를 사용하여 데이터 원본 만들기

2021-04-30-preview REST API는 사용자가 할당한 관리 ID를 지원합니다. 다음은 [REST API](/rest/api/searchservice/create-data-source), 관리 ID 연결 문자열, 사용자가 할당한 관리 ID를 사용하여 스토리지 계정의 데이터를 인덱싱할 데이터 원본을 만드는 방법의 예제입니다.

```http
POST https://[service name].search.windows.net/datasources?api-version=2021-04-30-preview
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/[subscription ID]/resourceGroups/[resource group name]/providers/Microsoft.DocumentDB/databaseAccounts/[Cosmos DB account name]/;"
    },
    "container": { 
        "name": "myCollection", "query": null 
    },
    "identity" : { 
        "@odata.type": "#Microsoft.Azure.Search.DataUserAssignedIdentity",
        "userAssignedIdentity" : "/subscriptions/[subscription ID]/resourcegroups/[resource group name]/providers/Microsoft.ManagedIdentity/userAssignedIdentities/[managed identity name]" 
    },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

요청 본문에는 다음 필드를 포함해야 하는 데이터 소스 정의가 포함됩니다.

| 필드   | 설명 |
|---------|-------------|
| **name** | 필수 사항입니다. 데이터 원본 개체를 나타낼 이름을 선택합니다. |
|**type**| 필수 사항입니다. `cosmosdb`이어야 합니다. |
|**credentials** | 필수 사항입니다. <br/><br/>관리 ID를 사용하여 연결하는 경우 **자격 증명** 형식은 다음과 같아야 합니다. *Database=[database-name];ResourceId=[resource-id-string];(ApiKind=[api-kind];)*<br/> <br/>ResourceId 형식: *ResourceId=/subscriptions/**your subscription ID**/resourceGroups/**your resource group name**/providers/Microsoft.DocumentDB/databaseAccounts/**your cosmos db account name**/;*<br/><br/>SQL 컬렉션의 경우 연결 문자열에 ApiKind가 필요하지 않습니다.<br/><br/>MongoDB 컬렉션의 경우 **ApiKind=MongoDb** 를 연결 문자열에 추가합니다. <br/><br/>Gremlin 그래프의 경우 [제어된 인덱서 미리 보기](https://aka.ms/azure-cognitive-search/indexer-preview)에 등록하여 미리 보기 액세스와 자격 증명 형식 지정 방법에 대한 정보를 얻습니다.<br/>|
| **container** | 다음과 같은 요소가 있습니다. <br/>**name**: 필수 사항입니다. 인덱싱할 데이터베이스 컬렉션의 ID를 지정합니다.<br/>**query**: (선택 사항) 추상 JSON 문서를 Azure Cognitive Search가 인덱싱할 수 있는 평면 스키마로 평면화하는 쿼리를 지정할 수 있습니다.<br/>MongoDB API와 Gremlin API에서는 쿼리가 지원되지 않습니다. |
| **identity** | 사용자가 할당한 관리 ID 모음을 포함합니다. 데이터 원본을 만들 때는 사용자가 할당한 관리 ID를 하나만 제공해야 합니다. 다음과 같은 요소가 있습니다. <br/>**userAssignedIdentities** 에는 사용자가 할당한 관리 ID의 세부 정보가 포함됩니다.<br/><br/>사용자가 할당한 관리 ID 형식: /subscriptions/**구독 ID**/resourcegroups/**리소스 그룹 이름**/providers/Microsoft.ManagedIdentity/userAssignedIdentities/**관리 ID 이름**|
| **dataChangeDetectionPolicy** | 권장 |
|**dataDeletionDetectionPolicy** | 옵션 |

## <a name="4---create-the-index"></a>4 - 인덱스 만들기

인덱스는 문서의 필드, 특성 및 검색 경험을 형성하는 기타 항목을 지정합니다.

검색 가능한 `booktitle` 필드를 사용하여 인덱스를 만드는 방법은 다음과 같습니다.

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

인덱스 만들기에 자세한 내용은 [인덱스 만들기](/rest/api/searchservice/create-index)를 참조하세요.

## <a name="5---create-the-indexer"></a>5 - 인덱서 만들기

인덱서는 데이터 원본을 대상 검색 인덱스와 연결하고 데이터 새로 고침을 자동화하는 일정을 제공합니다.

인덱스와 데이터 원본이 만들어지면 인덱서를 만들 준비가 된 것입니다.

예제 인덱서 정의:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

이 인덱서는 2시간 간격으로 실행됩니다(일정 간격이 "PT2H"로 설정됨). 인덱서를 30분 간격으로 실행하려면 간격을 "PT30M"으로 설정합니다. 지원되는 가장 짧은 간격은 5분입니다. 일정은 선택 사항입니다. 생략하는 경우 인덱서는 만들어질 때 한 번만 실행됩니다. 그러나 언제든지 필요할 때 인덱서를 실행할 수 있습니다.   

인덱서 만들기 API에 대한 자세한 내용은 [인덱서 만들기](/rest/api/searchservice/create-indexer)를 확인하세요.

인덱서 일정을 정의하는 방법에 대한 자세한 내용은 [Azure Cognitive Search에 대한 인덱서를 예약하는 방법](search-howto-schedule-indexers.md)을 참조하세요.

## <a name="troubleshooting"></a>문제 해결

Cosmos DB에서 데이터를 인덱싱할 수 없는 경우 다음 사항을 고려하세요.

1. 최근에 Cosmos DB 계정 키를 순환한 경우 관리 ID 연결 문자열이 작동하는 데 최대 15분이 걸립니다.

1. Cosmos DB 계정의 액세스 권한이 선택한 네트워크로 제한되어 있는지 확인합니다. 이 경우 [Azure 네트워크 보안 기능으로 보호되는 콘텐츠에 대한 인덱서 액세스](search-indexer-securing-resources.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [Azure Cosmos DB 인덱서](search-howto-index-cosmosdb.md)