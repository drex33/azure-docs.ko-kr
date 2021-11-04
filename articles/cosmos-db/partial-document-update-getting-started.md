---
title: Azure Cosmos DB 부분 문서 업데이트를 시작 하는 중
description: 이 문서에서는 .NET, Java, Node Sdk에서 부분 문서 업데이트를 사용 하는 방법에 대 한 예제를 제공 합니다.
author: abhirockzz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 08/25/2021
ms.author: abhishgu
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3ceebe9f537dcf2f496594d6a6c63972fcb7af92
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434107"
---
# <a name="azure-cosmos-db-partial-document-update-getting-started"></a>Azure Cosmos DB 부분 문서 업데이트: 시작 하기
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

이 문서에서는 .NET, Java, Node Sdk와 함께 부분 문서 업데이트를 사용 하는 방법에 대 한 예제와 일반적인 오류가 발생할 수 있는 방법을 제공 합니다. 다음 시나리오에 대 한 코드 샘플이 제공 됩니다.

- 단일 패치 작업 실행
- 여러 패치 작업 결합
- 필터 조건자를 기반으로 하는 조건부 패치 구문
- 트랜잭션의 일부로 패치 작업 실행

## <a name="net"></a>.NET

[NuGet 갤러리](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/3.22.1-preview) 에서 Azure Cosmos DB .net SDK v3의 *3.22.1 preview* 버전을 다운로드할 수 있습니다.

> [!NOTE]
> 전체 부분 문서 업데이트 샘플은 GitHub의 [.net v3 샘플 리포지토리에서](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/3fa885fdd84e2f8852d2a1d5c75c56b642b5bba3/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement/Program.cs) 찾을 수 있습니다.

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

**필터 조건자를 기반으로 하는 조건부 패치 구문**

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

Patch API는 현재 4.20.0 버전의 Java SDK에서 미리 보기로 제공 됩니다. GA'ed를 사용 하는 경우 4.21.0 버전에서 사용할 수 있습니다. 패치 작업을 사용 하려면 [Azure Cosmos DB Java v4 SDK](sql-api-sdk-java-v4.md) 의 *4.20.0* 버전을의 종속성 목록에 추가 `pom.xml` 하거나 [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos)에서 직접 다운로드 합니다.

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-cosmos</artifactId>
    <version>4.20.0</version>
</dependency>
```

> [!NOTE]
> 전체 샘플은 GitHub의 [JAVA SDK v4 샘플 리포지토리에서](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/) 찾을 수 있습니다.

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

**필터 조건자를 기반으로 하는 조건부 패치 구문**

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

**필터 조건자를 기반으로 하는 조건부 패치 구문**

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

## <a name="support-for-server-side-programming"></a>Server-Side 프로그래밍에 대 한 지원

저장 프로시저, 트리거 및 사용자 정의 함수를 사용 하 여 [서버 쪽에서](stored-procedures-triggers-udfs.md) 부분 문서 업데이트 작업을 실행할 수도 있습니다.


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

다음은이 기능을 사용 하는 동안 발생할 수 있는 일반적인 오류 목록입니다.

| **오류 메시지** | **설명** |
| ------------ | -------- |
| 잘못 된 패치 요청: 패치 사양의 구문을 검사 합니다.| Patch 작업 구문이 잘못 되었습니다. [사양을](partial-document-update.md#partial-document-update-specification) 검토 하세요.
| 잘못 된 패치 요청: 시스템 속성을 패치할 수 없습니다 `SYSTEM_PROPERTY` . | ,,와 같은 시스템 생성 속성  `_id` 을 패치 하  `_ts`  `_etag`  `_rid` 는 것은 지원 되지 않습니다. 자세한 내용은 [부분 문서 업데이트 faq](partial-document-update-faq.yml#is-partial-document-update-supported-for-system-generated-properties-) 를 확인 하세요. 
| 패치 작업 수는 10 개를 초과할 수 없습니다. | 단일 패치 사양에 추가할 수 있는 패치 작업은 10 개로 제한 됩니다. 자세한 내용은 [부분 문서 업데이트 faq](partial-document-update-faq.yml#is-there-a-limit-to-the-number-of-partial-document-update-operations-) 를 확인 하세요.
| 작업 (): 작업에 대 한 `PATCH_OPERATION_INDEX` Index ( `ARRAY_INDEX` )가 배열 범위를 벗어났습니다. | 패치할 배열 요소의 인덱스가 범위를 벗어났습니다. 
| 작업 ( `PATCH_OPERATION_INDEX` ))의 경우: 바꿀 노드 ( `PATH` )는 트랜잭션에서 이전에 제거 되었습니다.| 패치 하려는 경로가 없습니다.
| Operation ( `PATCH_OPERATION_INDEX` ): 제거할 노드 ( `PATH` )가 없습니다. 참고: 트랜잭션에서 이전에 제거 되었을 수도 있습니다.  | 패치 하려는 경로가 없습니다.
| 작업 ( `PATCH_OPERATION_INDEX` ): 바꾸려는 노드 ( `PATH` )가 없습니다. | 패치 하려는 경로가 없습니다.
| Operation ( `PATCH_OPERATION_INDEX` ): Node ( `PATH` )가 숫자가 아닙니다.| 증분 연산은 integer 및 float에 대해서만 사용할 수 있습니다. 자세한 정보: [지원 되는 작업](partial-document-update.md#supported-operations)
| Operation ( `PATCH_OPERATION_INDEX` ): 추가 작업은 기존 노드 (배열 또는 개체)의 자식 개체만 만들 수 있으며, 경로를 재귀적으로 만들 수 없습니다. 경로를 찾을 수 `PATH` 없습니다. | 자식 경로를 개체 또는 배열 노드 형식에 추가할 수 있습니다. 또한 `n` 번째 자식을 만들려면 `n-1` 자식 자식이 있어야 합니다. 
| Operation ()의 경우 `PATCH_OPERATION_INDEX` 지정 된 작업은 기존 노드 (배열 또는 개체)의 자식 개체만 만들 수 있으며, 경로를 재귀적으로 만들 수 없습니다. 경로를 찾을 수 `PATH` 없습니다. | 자식 경로를 개체 또는 배열 노드 형식에 추가할 수 있습니다. 또한 `n` 번째 자식을 만들려면 `n-1` 자식 자식이 있어야 합니다. 

## <a name="next-steps"></a>다음 단계

- [부분 문서 업데이트](partial-document-update.md) 의 개념적 개요에 대해 자세히 알아보기
