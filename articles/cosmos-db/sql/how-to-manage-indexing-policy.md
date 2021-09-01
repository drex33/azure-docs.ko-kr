---
title: Azure Cosmos DB의 인덱싱 정책 관리
description: 인덱싱 정책을 관리하고, 인덱싱에서 속성을 포함하거나 제외하는 방법과 다른 Azure Cosmos DB SDK를 사용하여 인덱싱을 정의하는 방법을 알아봅니다
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/25/2021
ms.author: tisande
ms.custom: devx-track-python, devx-track-js, devx-track-csharp
ms.openlocfilehash: 5b7be5bd7851321e9a66513da9e995f5de134049
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117187"
---
# <a name="manage-indexing-policies-in-azure-cosmos-db"></a>Azure Cosmos DB의 인덱싱 정책 관리
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB에서 데이터는 각 컨테이너에 대해 정의된 [인덱싱 정책](../index-policy.md)에 따라 인덱싱됩니다. 새로 만든 컨테이너에 대한 기본 인덱싱 정책은 모든 문자열 또는 숫자에 대해 범위 인덱스를 적용합니다. 이 정책은 사용자 지정 인덱싱 정책으로 재정의할 수 있습니다.

> [!NOTE]
> 이 문서에서 설명하는 인덱싱 정책 업데이트 방법은 Azure Cosmos DB의 SQL(Core) API에만 적용됩니다. [Azure Cosmos DB API for MongoDB](../mongodb/mongodb-indexing.md)의 인덱싱 및 [Azure Cosmos DB Cassandra API의 보조 인덱싱](../cassandra/secondary-indexing.md)에 대해 알아봅니다.

## <a name="indexing-policy-examples"></a>인덱싱 정책 예제

다음은 [JSON 형식](../index-policy.md#include-exclude-paths)으로 정책이 Azure Portal에 표시된 인덱싱 정책의 일부 예입니다. Azure CLI 또는 임의의 SDK를 통해 같은 매개 변수를 설정할 수 있습니다.

### <a name="opt-out-policy-to-selectively-exclude-some-property-paths"></a><a id="range-index"></a>일부 속성 경로를 선택적으로 제외하는 옵트아웃 정책

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

이 인덱싱 정책은 ```kind```, ```dataType```, ```precision```를 기본값으로 수동으로 설정하는 아래와 동일합니다. 이러한 속성은 명시적으로 설정할 필요가 없으며 위의 예제와 같이 인덱싱 정책에서 완전히 생략해야 합니다. 이러한 속성을 설정하려고 시도하는 경우 인덱싱 정책에서 자동으로 제거됩니다.


```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number",
                        "precision": -1
                    },
                    {
                        "kind": "Range",
                        "dataType": "String",
                        "precision": -1
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/path/to/single/excluded/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/excluded/properties/*"
            }
        ]
    }
```

### <a name="opt-in-policy-to-selectively-include-some-property-paths"></a>일부 속성 경로를 선택적으로 포함하는 옵트인 정책

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?"
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*"
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

이 인덱싱 정책은 ```kind```, ```dataType```, ```precision```를 기본값으로 수동으로 설정하는 아래와 동일합니다. 이러한 속성은 명시적으로 설정할 필요가 없으며 위의 예제와 같이 인덱싱 정책에서 완전히 생략해야 합니다. 이러한 속성을 설정하려고 시도하는 경우 인덱싱 정책에서 자동으로 제거됩니다.


```json
    {
        "indexingMode": "consistent",
        "includedPaths": [
            {
                "path": "/path/to/included/property/?",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            },
            {
                "path": "/path/to/root/of/multiple/included/properties/*",
                "indexes": [
                    {
                        "kind": "Range",
                        "dataType": "Number"
                    },
                    {
                        "kind": "Range",
                        "dataType": "String"
                    }
                ]
            }
        ],
        "excludedPaths": [
            {
                "path": "/*"
            }
        ]
    }
```

> [!NOTE]
> 일반적으로 **옵트아웃** 인덱싱 정책을 사용하여 Azure Cosmos DB가 데이터 모델에 추가할 수 있는 새 속성을 사전에 인덱싱하도록 하는 방법이 권장됩니다.

### <a name="using-a-spatial-index-on-a-specific-property-path-only"></a><a id="spatial-index"></a>특정 속성 경로에 대해서만 공간 인덱스 사용

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/_etag/?"
        }
    ],
    "spatialIndexes": [
        {
            "path": "/path/to/geojson/property/?",
            "types": [
                "Point",
                "Polygon",
                "MultiPolygon",
                "LineString"
            ]
        }
    ]
}
```

## <a name="composite-indexing-policy-examples"></a><a id="composite-index"></a>복합 인덱싱 정책 예제

개별 속성에 대한 경로를 포함 또는 제외하는 것 외에, 복합 인덱스를 지정할 수도 있습니다. 여러 속성에 대해 `ORDER BY` 절이 있는 쿼리를 수행하려면 해당 속성에 [복합 인덱스](../index-policy.md#composite-indexes)가 필요합니다. 또한 복합 인덱스는 필터가 여러 개 있거나 필터와 ORDER BY 절이 둘 다 있는 쿼리에 대한 성능 혜택이 있습니다.

> [!NOTE]
> 복합 경로는 해당 `/?` 경로의 스칼라 값만 인덱싱되므로 암시적입니다. `/*` 와일드카드는 복합 경로에서 지원되지 않습니다. `/?` 또는 `/*` 를 복합 경로에 지정하면 안 됩니다.

### <a name="composite-index-defined-for-name-asc-age-desc"></a>(name asc, age desc)에 대해 정의된 복합 인덱스:

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

이름 및 연령에 대한 복합 인덱스는 쿼리 #1 및 쿼리 #2에 필요합니다.

쿼리 #1:

```sql
    SELECT *
    FROM c
    ORDER BY c.name ASC, c.age DESC
```

쿼리 #2:

```sql
    SELECT *
    FROM c
    ORDER BY c.name DESC, c.age ASC
```

이 복합 인덱스는 쿼리 #3 및 쿼리 #4에 혜택을 주고 필터를 최적화합니다.

쿼리 #3:

```sql
SELECT *
FROM c
WHERE c.name = "Tim"
ORDER BY c.name DESC, c.age ASC
```

쿼리 #4:

```sql
SELECT *
FROM c
WHERE c.name = "Tim" AND c.age > 18
```

### <a name="composite-index-defined-for-name-asc-age-asc-and-name-asc-age-desc"></a>(name ASC, age ASC) 및 (name ASC, age DESC)에 대해 정의된 복합 인덱스:

동일한 인덱싱 정책 내에서 여러 다른 복합 인덱스를 정의할 수 있습니다.

```json
    {  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"ascending"
                }
            ],
            [  
                {  
                    "path":"/name",
                    "order":"ascending"
                },
                {  
                    "path":"/age",
                    "order":"descending"
                }
            ]
        ]
    }
```

### <a name="composite-index-defined-for-name-asc-age-asc"></a>(name ASC, age ASC)에 대해 정의된 복합 인덱스:

순서를 지정하는 선택 사항입니다. 지정하지 않으면 순서는 오름차순입니다.

```json
{  
        "automatic":true,
        "indexingMode":"Consistent",
        "includedPaths":[  
            {  
                "path":"/*"
            }
        ],
        "excludedPaths":[],
        "compositeIndexes":[  
            [  
                {  
                    "path":"/name",
                },
                {  
                    "path":"/age",
                }
            ]
        ]
}
```

### <a name="excluding-all-property-paths-but-keeping-indexing-active"></a>모든 속성 경로를 제외하되 인덱싱을 활성 상태로 유지

이 정책은 [TTL(Time-to-Live) 기능](time-to-live.md)이 활성 상태이지만 보조 인덱스가 필요한 상황(Azure Cosmos DB를 순수 키 값 저장소로 사용하기 위해)에 사용할 수 있습니다.

```json
    {
        "indexingMode": "consistent",
        "includedPaths": [],
        "excludedPaths": [{
            "path": "/*"
        }]
    }
```

### <a name="no-indexing"></a>인덱싱 안 함

이 정책은 인덱싱을 비활성화합니다. 만일 `indexingMode` 가 `none`로 설정된 경우 컨테이너에 TTL을 설정할 수 없습니다.

```json
    {
        "indexingMode": "none"
    }
```

## <a name="updating-indexing-policy"></a>인덱싱 정책 업데이트

Azure Cosmos DB에서 아래 방법 중 일부를 사용하여 인덱싱 정책을 업데이트할 수 있습니다.

- Azure Portal에서
- Azure CLI를 사용하여
- PowerShell 사용
- SDK 중 하나를 사용하여

[인덱싱 정책 업데이트](../index-policy.md#modifying-the-indexing-policy)는 인덱스 변환을 트리거합니다. 이 변환의 진행률을 SDK에서 추적할 수도 있습니다.

> [!NOTE]
> 인덱싱 정책을 업데이트할 때 Azure Cosmos DB 쓰기는 중단되지 않습니다. 자세한 정보는 [인덱싱 변환](../index-policy.md#modifying-the-indexing-policy)을 참조하세요.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

Azure Cosmos 컨테이너는 자체의 인덱싱 정책을 Azure Portal에서 직접 편집할 수 있는 JSON 문서로 저장합니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

1. 새 Azure Cosmos 계정을 만들거나 기존 계정을 선택합니다.

1. **Data Explorer** 창을 열고 작업할 컨테이너를 선택합니다.

1. **크기 조정 및 설정** 을 클릭합니다.

1. 인덱싱 정책 JSON 문서를 수정합니다([아래](#indexing-policy-examples) 예제 참조).

1. 완료되면 **저장** 을 클릭합니다.

:::image type="content" source="./media/how-to-manage-indexing-policy/indexing-policy-portal.png" alt-text="Azure Portal을 사용하여 인덱싱 관리":::

## <a name="use-the-azure-cli"></a>Azure CLI 사용

사용자 지정 인덱싱 정책을 사용하여 컨테이너를 만들려면 [CLI를 사용하여 사용자 지정 인덱스 정책을 사용한 컨테이너 만들기](manage-with-cli.md#create-a-container-with-a-custom-index-policy)를 참조하세요.

## <a name="use-powershell"></a>PowerShell 사용

사용자 지정 인덱싱 정책을 사용하여 컨테이너를 만들려면 [PowerShell을 사용하여 사용자 지정 인덱스 정책을 사용한 컨테이너 만들기](manage-with-powershell.md#create-container-custom-index)를 참조하세요.

## <a name="use-the-net-sdk"></a><a id="dotnet-sdk"></a> .NET SDK 사용

# <a name="net-sdk-v2"></a>[.NET SDK V2](#tab/dotnetv2)

`DocumentCollection` 개체는 [.NET SDK v2](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)에서 `IndexingPolicy` 속성을 노출해 `IndexingMode`를 변경하고 `IncludedPaths` 및 `ExcludedPaths`을 추가하거나 제거할 수 있게 합니다.

```csharp
// Retrieve the container's details
ResourceResponse<DocumentCollection> containerResponse = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"));
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(new SpatialSpec() { Path = "/locations/*", SpatialTypes = new Collection<SpatialType>() { SpatialType.Point } } );
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> {new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending }});
// Update container with changes
await client.ReplaceDocumentCollectionAsync(containerResponse.Resource);
```

인덱스 변환 진행률을 추적하려면 `PopulateQuotaInfo` 속성을 `true`로 설정하는 `RequestOptions` 개체를 전달합니다.

```csharp
// retrieve the container's details
ResourceResponse<DocumentCollection> container = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("database", "container"), new RequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = container.IndexTransformationProgress;
```

# <a name="net-sdk-v3"></a>[.NET SDK V3](#tab/dotnetv3)

`ContainerProperties` 개체는 [.NET SDK v3](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)(사용량 관련 [빠른 시작](create-sql-api-dotnet.md) 참조)에서 `IndexingPolicy` 속성을 드러내 `IndexingMode`을 변경하고 `IncludedPaths` 및 `ExcludedPaths`를 추가하거나 제거할 수 있게 합니다.

```csharp
// Retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync();
// Set the indexing mode to consistent
containerResponse.Resource.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
// Add an included path
containerResponse.Resource.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
// Add an excluded path
containerResponse.Resource.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/name/*" });
// Add a spatial index
SpatialPath spatialPath = new SpatialPath
{
    Path = "/locations/*"
};
spatialPath.SpatialTypes.Add(SpatialType.Point);
containerResponse.Resource.IndexingPolicy.SpatialIndexes.Add(spatialPath);
// Add a composite index
containerResponse.Resource.IndexingPolicy.CompositeIndexes.Add(new Collection<CompositePath> { new CompositePath() { Path = "/name", Order = CompositePathSortOrder.Ascending }, new CompositePath() { Path = "/age", Order = CompositePathSortOrder.Descending } });
// Update container with changes
await client.GetContainer("database", "container").ReplaceContainerAsync(containerResponse.Resource);
```

컨테이너에 대한 인덱스 변환 진행률을 추적하려면 `RequestOptions` 개체의 `PopulateQuotaInfo` 속성을 `true`으로 설정해 전달한 다음, `x-ms-documentdb-collection-index-transformation-progress` 응답 헤더에서 값을 검색합니다.

```csharp
// retrieve the container's details
ContainerResponse containerResponse = await client.GetContainer("database", "container").ReadContainerAsync(new ContainerRequestOptions { PopulateQuotaInfo = true });
// retrieve the index transformation progress from the result
long indexTransformationProgress = long.Parse(containerResponse.Headers["x-ms-documentdb-collection-index-transformation-progress"]);
```

새 컨테이너를 만드는 동안 사용자 지정 인덱싱 정책을 정의할 때 SDK V3의 흐름 API를 사용하여 간결하고 효율적인 방식으로 이 정의를 작성할 수 있습니다.

```csharp
await client.GetDatabase("database").DefineContainer(name: "container", partitionKeyPath: "/myPartitionKey")
    .WithIndexingPolicy()
        .WithIncludedPaths()
            .Path("/*")
        .Attach()
        .WithExcludedPaths()
            .Path("/name/*")
        .Attach()
        .WithSpatialIndex()
            .Path("/locations/*", SpatialType.Point)
        .Attach()
        .WithCompositeIndex()
            .Path("/name", CompositePathSortOrder.Ascending)
            .Path("/age", CompositePathSortOrder.Descending)
        .Attach()
    .Attach()
    .CreateIfNotExistsAsync();
```
---

## <a name="use-the-java-sdk"></a>Java SDK 사용

[Java SDK](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)(해당 사용법에 관한 [이 빠른 시작](create-sql-api-java.md) 참조)의 `DocumentCollection` 개체는 `getIndexingPolicy()` 및 `setIndexingPolicy()` 메서드를 표시합니다. 해당 메서드가 조작하는 `IndexingPolicy` 개체를 사용하여 인덱싱 모드를 변경하고 포함된 경로 및 제외된 경로를 추가 또는 제거할 수 있습니다.

```java
// Retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), null);
containerResponse.subscribe(result -> {
DocumentCollection container = result.getResource();
IndexingPolicy indexingPolicy = container.getIndexingPolicy();

// Set the indexing mode to consistent
indexingPolicy.setIndexingMode(IndexingMode.Consistent);

// Add an included path

Collection<IncludedPath> includedPaths = new ArrayList<>();
IncludedPath includedPath = new IncludedPath();
includedPath.setPath("/*");
includedPaths.add(includedPath);
indexingPolicy.setIncludedPaths(includedPaths);

// Add an excluded path

Collection<ExcludedPath> excludedPaths = new ArrayList<>();
ExcludedPath excludedPath = new ExcludedPath();
excludedPath.setPath("/name/*");
excludedPaths.add(excludedPath);
indexingPolicy.setExcludedPaths(excludedPaths);

// Add a spatial index

Collection<SpatialSpec> spatialIndexes = new ArrayList<SpatialSpec>();
Collection<SpatialType> collectionOfSpatialTypes = new ArrayList<SpatialType>();

SpatialSpec spec = new SpatialSpec();
spec.setPath("/locations/*");
collectionOfSpatialTypes.add(SpatialType.Point);
spec.setSpatialTypes(collectionOfSpatialTypes);
spatialIndexes.add(spec);

indexingPolicy.setSpatialIndexes(spatialIndexes);

// Add a composite index

Collection<ArrayList<CompositePath>> compositeIndexes = new ArrayList<>();
ArrayList<CompositePath> compositePaths = new ArrayList<>();

CompositePath nameCompositePath = new CompositePath();
nameCompositePath.setPath("/name");
nameCompositePath.setOrder(CompositePathSortOrder.Ascending);

CompositePath ageCompositePath = new CompositePath();
ageCompositePath.setPath("/age");
ageCompositePath.setOrder(CompositePathSortOrder.Descending);

compositePaths.add(ageCompositePath);
compositePaths.add(nameCompositePath);

compositeIndexes.add(compositePaths);
indexingPolicy.setCompositeIndexes(compositeIndexes);

// Update the container with changes

 client.replaceCollection(container, null);
});
```

컨테이너에 대한 인덱스 변환 진행률을 추적하려면 채워야 할 할당량 정보를 요청하는 `RequestOptions` 개체를 전달한 다음, `x-ms-documentdb-collection-index-transformation-progress` 응답 헤더에서 값을 검색합니다.

```java
// set the RequestOptions object
RequestOptions requestOptions = new RequestOptions();
requestOptions.setPopulateQuotaInfo(true);
// retrieve the container's details
Observable<ResourceResponse<DocumentCollection>> containerResponse = client.readCollection(String.format("/dbs/%s/colls/%s", "database", "container"), requestOptions);
containerResponse.subscribe(result -> {
    // retrieve the index transformation progress from the response headers
    String indexTransformationProgress = result.getResponseHeaders().get("x-ms-documentdb-collection-index-transformation-progress");
});
```

## <a name="use-the-nodejs-sdk"></a>Node.js SDK 사용

[Node.js SDK](https://www.npmjs.com/package/@azure/cosmos)(해당 사용법에 관한 [이 빠른 시작](create-sql-api-nodejs.md) 참조)의 `ContainerDefinition` 인터페이스는 `indexingMode`를 변경하고 `includedPaths` 및 `excludedPaths`를 제거할 수 있는 `indexingPolicy` 개체를 표시합니다.

컨테이너의 세부 정보 검색

```javascript
const containerResponse = await client.database('database').container('container').read();
```

인덱싱 모드를 일관되게 설정

```javascript
containerResponse.body.indexingPolicy.indexingMode = "consistent";
```

공간 인덱스를 포함하여 포함된 경로 추가

```javascript
containerResponse.body.indexingPolicy.includedPaths.push({
    includedPaths: [
      {
        path: "/age/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.String
          },
          {
            kind: cosmos.DocumentBase.IndexKind.Range,
            dataType: cosmos.DocumentBase.DataType.Number
          }
        ]
      },
      {
        path: "/locations/*",
        indexes: [
          {
            kind: cosmos.DocumentBase.IndexKind.Spatial,
            dataType: cosmos.DocumentBase.DataType.Point
          }
        ]
      }
    ]
  });
```

제외된 경로 추가

```javascript
containerResponse.body.indexingPolicy.excludedPaths.push({ path: '/name/*' });
```

변경 내용으로 컨테이너 업데이트

```javascript
const replaceResponse = await client.database('database').container('container').replace(containerResponse.body);
```

컨테이너에 대한 인덱스 변환 진행률을 추적하려면 `populateQuotaInfo` 속성을 `true`로 설정하는 `RequestOptions` 개체를 전달한 다음, `x-ms-documentdb-collection-index-transformation-progress` 응답 헤더에서 값을 검색합니다.

```javascript
// retrieve the container's details
const containerResponse = await client.database('database').container('container').read({
    populateQuotaInfo: true
});
// retrieve the index transformation progress from the response headers
const indexTransformationProgress = replaceResponse.headers['x-ms-documentdb-collection-index-transformation-progress'];
```

## <a name="use-the-python-sdk"></a>Python SDK 사용

# <a name="python-sdk-v3"></a>[Python SDK V3](#tab/pythonv3)

[Python SDK V3](https://pypi.org/project/azure-cosmos/)(사용량과 관련하여 [빠른 시작](create-sql-api-python.md) 참조)를 사용하는 경우 컨테이너 구성이 사전으로 관리됩니다. 이 사전에서 인덱싱 정책 및 해당 정책의 모든 특성에 액세스할 수 있습니다.

컨테이너의 세부 정보 검색

```python
containerPath = 'dbs/database/colls/collection'
container = client.ReadContainer(containerPath)
```

인덱싱 모드를 일관되게 설정

```python
container['indexingPolicy']['indexingMode'] = 'consistent'
```

포함된 경로 및 공간 인덱스를 통해 인덱싱 정책 정의

```python
container["indexingPolicy"] = {

    "indexingMode":"consistent",
    "spatialIndexes":[
                {"path":"/location/*","types":["Point"]}
             ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

제외된 경로를 사용하여 인덱싱 정책 정의

```python
container["indexingPolicy"] = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

복합 인덱스 추가

```python
container['indexingPolicy']['compositeIndexes'] = [
                [
                    {
                        "path": "/name",
                        "order": "ascending"
                    },
                    {
                        "path": "/age",
                        "order": "descending"
                    }
                ]
                ]
```

변경 내용으로 컨테이너 업데이트

```python
response = client.ReplaceContainer(containerPath, container)
```

# <a name="python-sdk-v4"></a>[Python SDK V4](#tab/pythonv4)

[Python SDK V4](https://pypi.org/project/azure-cosmos/)를 사용하는 경우 컨테이너 구성이 사전으로서 관리됩니다. 이 사전에서 인덱싱 정책 및 해당 정책의 모든 특성에 액세스할 수 있습니다.

컨테이너의 세부 정보 검색

```python
database_client = cosmos_client.get_database_client('database')
container_client = database_client.get_container_client('container')
container = container_client.read()
```

인덱싱 모드를 일관되게 설정

```python
indexingPolicy = {
    'indexingMode': 'consistent'
}
```

포함된 경로 및 공간 인덱스를 통해 인덱싱 정책 정의

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "spatialIndexes":[
        {"path":"/location/*","types":["Point"]}
    ],
    "includedPaths":[{"path":"/age/*","indexes":[]}],
    "excludedPaths":[{"path":"/*"}]
}
```

제외된 경로를 사용하여 인덱싱 정책 정의

```python
indexingPolicy = {
    "indexingMode":"consistent",
    "includedPaths":[{"path":"/*","indexes":[]}],
    "excludedPaths":[{"path":"/name/*"}]
}
```

복합 인덱스 추가

```python
indexingPolicy['compositeIndexes'] = [
    [
        {
            "path": "/name",
            "order": "ascending"
        },
        {
            "path": "/age",
            "order": "descending"
        }
    ]
]
```

변경 내용으로 컨테이너 업데이트

```python
response = database_client.replace_container(container_client, container['partitionKey'], indexingPolicy)
```

응답 헤더에서 인덱스 변환 진행률 검색
```python
container_client.read(populate_quota_info = True,
                      response_hook = lambda h,p: print(h['x-ms-documentdb-collection-index-transformation-progress']))
```

---

## <a name="next-steps"></a>다음 단계

다음 문서에서 인덱싱에 대해 자세히 알아보세요.

- [인덱싱 개요](../index-overview.md)
- [인덱싱 정책](../index-policy.md)
