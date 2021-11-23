---
title: Azure Cosmos DB 부분 문서 업데이트 시작
description: 이 문서에서는 .NET, Java, Node SDK에서 부분 문서 업데이트를 사용하는 방법에 대한 예제를 제공합니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/25/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 79d642e658d79d3ec754a61f2faf47cbf5c6d1d4
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132941152"
---
# <a name="azure-cosmos-db-partial-document-update-getting-started"></a>Azure Cosmos DB 부분 문서 업데이트: 시작
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 발생할 수 있는 일반적인 오류와 함께 .NET, Java, Node SDK에서 부분 문서 업데이트를 사용하는 방법에 대한 예제를 제공합니다. 다음 시나리오에 대한 코드 샘플이 제공되었습니다.

- 단일 패치 작업 실행
- 여러 패치 작업 결합
- 필터 조건자 기반 조건부 패치 구문
- 트랜잭션의 일부로 패치 작업 실행

## <a name="net"></a>.NET

[Azure Cosmos DB .NET v3 SDK의](sql/sql-api-sdk-dotnet-standard.md) 부분 문서 업데이트(패치 API) 지원은 버전 *3.23.0부터* 사용할 수 있습니다. [NuGet 갤러리에서](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.23.0) 다운로드할 수 있습니다.

> [!NOTE]
> 전체 부분 문서 업데이트 샘플은 GitHub [.NET v3 샘플 리포지토리에서](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs) 찾을 수 있습니다.

**단일 패치 작업 실행**

```csharp
ItemResponse<SalesOrder> response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/TotalDue", 0) });

SalesOrder updated = response.Resource;
```

**여러 패치 작업 결합**

```csharp
List<PatchOperation> patchOperations = new List<PatchOperation>();
patchOperations.Add(PatchOperation.Add("/nonExistentParent/Child", "bar"));
patchOperations.Add(PatchOperation.Remove("/cost"));
patchOperations.Add(PatchOperation.Increment("/taskNum", 6));
patchOperations.Add(patchOperation.Set("/existingPath/newproperty",value));

container.PatchItemAsync<item>(
                id: 5,
                partitionKey: new PartitionKey("task6"),
                patchOperations: patchOperations );
```

**필터 조건자 기반 조건부 패치 구문**

```csharp
PatchItemRequestOptions patchItemRequestOptions = new PatchItemRequestOptions
{
    FilterPredicate = "from c where (c.TotalDue = 0 OR NOT IS_DEFINED(c.TotalDue))"
};
response = await container.PatchItemAsync<SalesOrder>(
    id: order.Id,
    partitionKey: new PartitionKey(order.AccountNumber),
    patchOperations: new[] { PatchOperation.Replace("/ShippedDate", DateTime.UtcNow) },
    patchItemRequestOptions);

SalesOrder updated = response.Resource;
```

**트랜잭션의 일부로 패치 작업 실행**


```csharp
List<PatchOperation> patchOperationsUpdateTask = new List<PatchOperation>()
            {
                PatchOperation.Add("/children/1/pk", "patched"),
                PatchOperation.Remove("/description"),
                PatchOperation.Add("/taskNum", 8),
                PatchOperation.Replace("/taskNum", 12)
            };

TransactionalBatchPatchItemRequestOptions requestOptionsFalse = new TransactionalBatchPatchItemRequestOptions()
            {
                FilterPredicate = "from c where c.taskNum = 3"
            };

TransactionalBatchInternal transactionalBatchInternalFalse = (TransactionalBatchInternal)containerInternal.CreateTransactionalBatch(new Cosmos.PartitionKey(testItem.pk));
transactionalBatchInternalFalse.PatchItem(id: testItem1.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.PatchItem(id: testItem2.id, patchOperationsUpdateTaskNum12, requestOptionsFalse);
transactionalBatchInternalFalse.ExecuteAsync());
```

## <a name="java"></a>Java

[Azure Cosmos DB Java v4 SDK의](sql/sql-api-sdk-java-v4.md) 부분 문서 업데이트(패치 API)에 대한 지원은 버전 *4.21.0부터* 사용할 수 있습니다. 의 dependencies 목록에 `pom.xml` 추가하거나 [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)에서 직접 다운로드할 수 있습니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.21.0</version>
</dependency>
```

> [!NOTE]
> 전체 샘플은 GitHub [Java SDK v4 샘플 리포지토리에서](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/tree/main/src/main/java/com/azure/cosmos/examples/patch/sync) 찾을 수 있습니다.

**단일 패치 작업 실행**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true);

CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**여러 패치 작업 결합**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
cosmosPatchOperations.add("/registered", true)
                     .replace("/parents/0/familyName", "Doe");
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**필터 조건자 기반 조건부 패치 구문**

```java
CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create();
                                                                   .add("/vaccinated", true);
CosmosPatchItemRequestOptions options = new CosmosPatchItemRequestOptions();
options.setFilterPredicate("from f where f.registered = true");

CosmosItemResponse<Family> response = this.container.patchItem(id, new PartitionKey(partitionKey),
                                      cosmosPatchOperations, options, Family.class);
```

**트랜잭션의 일부로 패치 작업 실행**

```java
CosmosBatch batch = CosmosBatch.createCosmosBatch(new PartitionKey(family.getLastName()));
batch.createItemOperation(family);

CosmosPatchOperations cosmosPatchOperations = CosmosPatchOperations.create().add("/registered", false);
batch.patchItemOperation(family.getId(), cosmosPatchOperations);

CosmosBatchResponse response = container.executeCosmosBatch(batch);
if (response.isSuccessStatusCode()) {
    // if transactional batch succeeds   
}
```

## <a name="node"></a>노드

**단일 패치 작업 실행**

```javascript
const patchSource = itemDefList[1];

const replaceOperation: PatchOperation[] = 
    [{ 
      op: "replace", 
      path: "/lastName", 
      value: "Martin" 
    }];

const { resource: patchSource1 } = await container.item(patchSource.lastName).patch(replaceOperation); 
console.log(`Patched ${patchSource1.lastName} to new ${patchSource1.lastName}.`); 
```

**여러 패치 작업 결합**

```javascript
const multipleOperations: PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/aka", 
      value: "MeFamily" 
    }, 
    { 
      op: "add", 
      path: "/years", 
      value: 12 
    }, 
    { 
      op: "replace", 
      path: "/lastName", 
      value: "Jose" 
    }, 
    { 
      op: "remove", 
      path: "/parents" 
    }, 
    { 
      op: "set", 
      path: "/children/firstName", 
      value: "Anderson" 
    }, 
    { 
      op: "incr", 
      path: "/years", 
      value: 5 
    } 
  ]; 

const { resource: patchSource2  } = await container.item(patchSource.id).patch(multipleOperations); 
 ```

**필터 조건자 기반 조건부 패치 구문**

```javascript
const operations : PatchOperation[] = [ 
    { 
      op: "add", 
      path: "/newImproved", 
      value: "it works" 
    } 
  ]; 

const condition = "from c where NOT IS_DEFINED(c.newImproved)"; 

const { resource: patchSource3 } = await container 
    .item(patchSource.id) 
    .patch({ condition, operations }); 

console.log(`Patched ${patchSource3} to new ${patchSource3}.`); 
```

## <a name="support-for-server-side-programming"></a>Server-Side 프로그래밍 지원

부분 문서 업데이트 작업은 저장 프로시저, 트리거 및 사용자 정의 함수를 사용하여 [서버 쪽에서도 실행할](stored-procedures-triggers-udfs.md) 수 있습니다.


```javascript
        this.patchDocument = function (documentLink, patchSpec, options, callback) { 
                if (arguments.length < 2) { 
                    throw new Error(ErrorCodes.BadRequest, sprintf(errorMessages.invalidFunctionCall, 'patchDocument', 2, arguments.length)); 
                }
                if (patchSpec === null || !(typeof patchSpec === "object" || Array.isArray(patchSpec))) { 
                    throw new Error(ErrorCodes.BadRequest, errorMessages.patchSpecMustBeObjectOrArray); 
                } 

                var documentIdTuple = validateDocumentLink(documentLink, false); 
                var collectionRid = documentIdTuple.collId; 
                var documentResourceIdentifier = documentIdTuple.docId; 
                var isNameRouted = documentIdTuple.isNameRouted; 

                patchSpec = JSON.stringify(patchSpec); 
                var optionsCallbackTuple = validateOptionsAndCallback(options, callback); 

                options = optionsCallbackTuple.options; 
                callback = optionsCallbackTuple.callback; 

                var etag = options.etag || ''; 
                var indexAction = options.indexAction || ''; 

                return collectionObjRaw.patch( 
                    collectionRid, 
                    documentResourceIdentifier, 
                    isNameRouted, 
                    patchSpec, 
                    etag, 
                    indexAction, 
                    function (err, response) { 
                        if (callback) { 
                            if (err) { 
                                callback(err); 
                            } else { 
                                callback(undefined, JSON.parse(response.body), response.options); 
                            } 
                        } else { 
                            if (err) { 
                                throw err; 
                            } 
                        } 
                    } 
                ); 
            }; 
```

## <a name="troubleshooting"></a>문제 해결

다음은 이 기능을 사용하는 동안 발생할 수 있는 일반적인 오류 목록입니다.

| **오류 메시지** | **설명** |
| ------------ | -------- |
| 잘못된 패치 요청: 패치 사양의 구문을 확인합니다.| 패치 작업 구문이 잘못되었습니다. [사양을 검토하세요.](partial-document-update.md#rest-api-reference-for-partial-document-update)
| 잘못된 패치 요청: 시스템 속성을 패치할 수 `SYSTEM_PROPERTY` 없습니다. | , 같은 시스템 생성 속성의  `_id`  `_ts`  `_etag`  `_rid` 패치는 지원되지 않습니다. 자세한 정보: [부분 문서 업데이트 FAQ](partial-document-update-faq.yml#is-partial-document-update-supported-for-system-generated-properties-) 
| 패치 작업 수는 10을 초과할 수 없습니다. | 단일 패치 사양에 추가할 수 있는 패치 작업은 10개로 제한됩니다. 자세한 정보: [부분 문서 업데이트 FAQ](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-)
| Operation()의 경우 `PATCH_OPERATION_INDEX` : 에서 작동할 Index( `ARRAY_INDEX` )가 배열 범위를 벗어났습니다. | 패치할 배열 요소의 인덱스가 범위를 벗어났습니다. 
| 작업( `PATCH_OPERATION_INDEX` )의 경우: 바꿀 Node( `PATH` )가 트랜잭션의 앞부분에서 제거되었습니다.| 패치하려는 경로가 없습니다.
| 작업()의 경우 `PATCH_OPERATION_INDEX` 제거할 Node( `PATH` )가 없습니다. 참고: 트랜잭션의 앞부분에서 제거되었을 수도 있습니다.  | 패치하려는 경로가 없습니다.
| Operation( 의 경우 `PATCH_OPERATION_INDEX` ): 바꿀 Node( `PATH` )가 없습니다. | 패치하려는 경로가 없습니다.
| `PATCH_OPERATION_INDEX`Operation(의 경우): Node( `PATH` )는 숫자가 아닙니다.| 증분 작업은 정수 및 float에서만 작동할 수 있습니다. 자세한 정보: [지원되는 작업](partial-document-update.md#supported-operations)
| Operation()의 경우 `PATCH_OPERATION_INDEX` : 작업 추가는 기존 노드(배열 또는 개체)의 자식 개체만 만들 수 있으며 경로를 재귀적으로 만들 수 없습니다. 그 외의 경로는 `PATH` 없습니다. | 자식 경로를 개체 또는 배열 노드 형식에 추가할 수 있습니다. 또한 자식 을 만들려면 `n` `n-1` 자식이 있어야 합니다. 
| Operation()의 경우 `PATCH_OPERATION_INDEX` : 지정된 작업은 기존 노드(배열 또는 개체)의 자식 개체만 만들 수 있으며 경로를 재귀적으로 만들 수 없으며 다음 이상의 경로를 찾을 수 `PATH` 없습니다. | 자식 경로를 개체 또는 배열 노드 형식에 추가할 수 있습니다. 또한 자식 을 만들려면 `n` `n-1` 자식이 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

- [부분 문서 업데이트의](partial-document-update.md) 개념적 개요에 대해 자세히 알아보기
