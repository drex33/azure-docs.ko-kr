---
title: CouchBase에서 Azure Cosmos DB SQL API로 마이그레이션
description: CouchBase에서 Azure Cosmos DB SQL API로 마이그레이션하기 위한 단계별 지침
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 02/11/2020
ms.author: mansha
author: manishmsfte
ms.custom: devx-track-java
ms.openlocfilehash: 7f4867727154c774ddd3bcfd65fbe4769c9e25ca
ms.sourcegitcommit: 331a5c3ad498061511383b80760349ff2a966bcf
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/29/2021
ms.locfileid: "133218334"
---
# <a name="migrate-from-couchbase-to-azure-cosmos-db-sql-api"></a>CouchBase에서 Azure Cosmos DB SQL API로 마이그레이션
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 확장 가능하고 전 세계적으로 분산되고 완전히 관리되는 데이터베이스입니다. 데이터에 대한 짧은 대기 시간 액세스를 보장합니다. Azure Cosmos DB에 대한 자세한 내용은 [개요](../introduction.md) 문서를 참조하세요. 이 문서에서는 Couchbase에 연결된 Java 애플리케이션을 Azure Cosmos DB의 SQL API 계정으로 마이그레이션하는 방법에 대한 지침을 제공합니다.

## <a name="differences-in-nomenclature"></a>명명법 차이

다음은 Couchbase와 비교할 때 Azure Cosmos DB에서는 다르게 작동하는 주요 기능입니다.

| Couchbase | Azure Cosmos DB |
|--|--|
| Couchbase 서버 | 계정 |
| 버킷 | 데이터베이스 |
| 버킷 | 컨테이너/컬렉션 |
| JSON 문서 | 항목/문서 |

## <a name="key-differences"></a>주요 차이점

* Azure Cosmos DB에는 문서 내에 "ID" 필드가 있고, Couchbase에서는 이 ID가 버킷의 일부입니다. "ID" 필드는 파티션 전체에서 고유합니다.

* Azure Cosmos DB는 분할 기술을 사용하여 크기를 조정합니다. 즉, 데이터를 여러 분할 영역/파티션으로 분할합니다. 이러한 파티션/분할 영역은 사용자가 제공하는 파티션 키 속성을 기반으로 생성됩니다. 파티션 키를 선택하여 읽기 작업뿐 아니라 쓰기 작업 또는 읽기/쓰기도 최적화할 수 있습니다. 자세히 알아보려면 [분할](../partitioning-overview.md) 문서를 참조하세요.

* Azure Cosmos DB에서는 컬렉션 이름이 이미 존재하기 때문에 최상위 계층 구조에서 컬렉션을 표시할 필요가 없습니다. 이 기능 덕분에 JSON 구조가 훨씬 간단해집니다. 다음은 데이터 모델에서 Couchbase와 Azure Cosmos DB 간 차이점을 보여 주는 예제입니다.

   **Couchbase**: 문서 ID =  "99FF4444"

    ```json
    {
      "TravelDocument":
      {
       "Country":"India",
      "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    }
   ```

   **Azure Cosmos DB**: 아래와 같이 문서 내에서 "ID"를 참조합니다.

    ```json
    {
      "id" : "99FF4444",
    
      "Country":"India",
       "Validity" : "2022-09-01",
        "Person":
        {
          "Name": "Manish",
          "Address": "AB Road, City-z"
        },
        "Visas":
        [
          {
          "Country":"India",
          "Type":"Multi-Entry",
          "Validity":"2022-09-01"
          },
          {
          "Country":"US",
          "Type":"Single-Entry",
          "Validity":"2022-08-01"
          }
        ]
      }
    
    ```
         
## <a name="java-sdk-support"></a>Java SDK 지원

Azure Cosmos DB에는 다양한 Java 프레임워크를 지원하는 다음과 같은 SDK가 있습니다.

* Async SDK
* Spring Boot SDK

다음 섹션에서는 이러한 SDK를 사용하는 경우를 설명합니다. 3가지 유형의 워크로드가 있는 예를 살펴보겠습니다.

## <a name="couchbase-as-document-repository--spring-data-based-custom-queries"></a>문서 리포지토리로서의 Couchbase 및 Spring 데이터 기반 사용자 지정 쿼리

마이그레이션하려는 워크로드가 Spring Boot Based SDK를 기반으로 하는 경우 다음 단계를 사용할 수 있습니다.

1. POM.xml 파일에 부모를 추가합니다.

   ```java
   <parent>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-parent</artifactId>
      <version>2.1.5.RELEASE</version>
      <relativePath/>
   </parent>
   ```

1. POM.xml 파일에 속성을 추가합니다.

   ```java
   <azure.version>2.1.6</azure.version>
   ```

1. POM.xml 파일에 종속성을 추가합니다.

   ```java
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
       <version>2.1.6</version>
   </dependency>
   ```

1. 리소스 아래에 애플리케이션 속성을 추가하고 다음을 지정합니다. URL, 키 및 데이터베이스 이름 매개 변수를 바꿔야 합니다.

   ```java
      azure.cosmosdb.uri=<your-cosmosDB-URL>
      azure.cosmosdb.key=<your-cosmosDB-key>
      azure.cosmosdb.database=<your-cosmosDB-dbName>
   ```

1. 모델에서 컬렉션의 이름을 정의합니다. 추가 주석을 지정할 수도 있습니다. 예를 들어 ID, 파티션 키를 명시적으로 표시합니다.

   ```java
   @Document(collection = "mycollection")
       public class User {
           @id
           private String id;
           private String firstName;
           @PartitionKey
           private String lastName;
       }
   ```

다음은 CRUD 작업을 위한 코드 조각입니다.

### <a name="insert-and-update-operations"></a>삽입 및 업데이트 작업

여기서 *_repo* 는 리포지토리의 개체이고 *doc* 은 POJO 클래스의 개체입니다. `.save`를 사용하여 삽입하거나 upsert할 수 있습니다(지정된 ID의 문서가 있는 경우). 다음 코드 조각에서는 doc 개체를 삽입하거나 업데이트하는 방법을 보여 줍니다.

`_repo.save(doc);`

### <a name="delete-operation"></a>삭제 작업

다음 코드 조각을 고려합니다. 여기서 doc 개체가 ID 및 파티션 키를 가져야 해당 개체를 찾아서 삭제할 수 있습니다.

`_repo.delete(doc);`

### <a name="read-operation"></a>읽기 작업

파티션 키를 지정하거나 지정하지 않고 문서를 읽을 수 있습니다. 파티션 키를 지정하지 않으면 파티션 키가 파티션 간 쿼리로 처리됩니다. 다음 코드 샘플을 살펴보겠습니다. 첫 번째 샘플은 ID 및 파티션 키 필드를 사용하여 작업을 수행합니다. 두 번째 샘플은 일반 필드를 사용하고 파티션 키 필드를 지정하지 않습니다.

* ```_repo.findByIdAndName(objDoc.getId(),objDoc.getName());```
* ```_repo.findAllByStatus(objDoc.getStatus());```

이것으로 끝입니다. 이제 Azure Cosmos DB에서 애플리케이션을 사용할 수 있습니다. 이 문서에서 설명하는 예제에 대한 전체 코드 샘플은 [CouchbaseToCosmosDB-SpringCosmos](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/SpringCosmos) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="couchbase-as-a-document-repository--using-n1ql-queries"></a>문서 리포지토리로서의 Couchbase 및 N1QL 쿼리 사용

N1QL 쿼리는 Couchbase에서 쿼리를 정의하는 방법입니다.

|N1QL 쿼리 | Azure Cosmos DB 쿼리|
|-------------------|-------------------|
|SELECT META(`TravelDocument`).id AS id, `TravelDocument`.* FROM `TravelDocument` WHERE `_type` = "com.xx.xx.xx.xxx.xxx.xxxx " and country = 'India’ and ANY m in Visas SATISFIES m.type == 'Multi-Entry' and m.Country IN ['India', Bhutan’] ORDER BY ` Validity` DESC LIMIT 25 OFFSET 0 | SELECT c.id,c FROM c JOIN m in  c.country=’India’ WHERE c._type = " com.xx.xx.xx.xxx.xxx.xxxx" and c.country = 'India' and m.type = 'Multi-Entry' and m.Country IN ('India', 'Bhutan') ORDER BY c.Validity DESC OFFSET 0 LIMIT 25 |

N1QL 쿼리에서 다음과 같이 변경된 내용을 확인할 수 있습니다.

* META 키워드를 사용하거나 첫 번째 수준 문서를 참조할 필요는 없습니다. 대신, 컨테이너에 대한 사용자 고유의 참조를 만들 수 있습니다. 이 예제에서는 "c"입니다(어떤 것도 상관없음). 이 참조는 모든 첫 번째 수준 필드의 접두사로 사용됩니다. 예: c.id, c.country 등.

* 이제 "ANY" 대신 하위 문서에 대한 조인을 수행하고 "m"과 같은 전용 별칭으로 참조할 수 있습니다. 하위 문서에 대한 별칭을 만든 후에는 별칭을 사용해야 합니다. 예: m.Country.

* Azure Cosmos DB 쿼리에서는 OFFSET의 시퀀스가 다릅니다. 먼저 OFFSET을 지정한 다음 LIMIT를 지정해야 합니다. Azure Cosmos DB에 쿼리를 전달하는 동안 클라이언트 쪽에서 불필요한 오버헤드가 발생할 수 있으므로 최대한으로 사용자 지정된 쿼리를 사용하는 경우에는 Spring Data SDK를 사용하지 않는 것이 좋습니다. 대신, 이 경우에 훨씬 효율적으로 활용할 수 있는 직접 Async Java SDK가 있습니다.

### <a name="read-operation"></a>읽기 작업

다음 단계를 수행하여 Async Java SDK를 사용합니다.

1. POM.xml 파일에 대해 다음 종속성을 구성합니다.

   ```java
   <!-- https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb -->
   <dependency>
       <groupId>com.microsoft.azure</groupId>
       <artifactId>azure-cosmos</artifactId>
       <version>3.0.0</version>
   </dependency>
   ```

1. 다음 예제와 같이 `ConnectionBuilder` 메서드를 사용하여 Azure Cosmos DB용 연결 개체를 만듭니다. 다음 코드를 한 번만 실행하려면 이 선언을 bean에 배치해야 합니다.

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
    
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
   
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

1. 쿼리를 실행하려면 다음 코드 조각을 실행해야 합니다.

   ```java
   Flux<FeedResponse<CosmosItemProperties>> objFlux= container.queryItems(query, fo);
   ```

이제 위 방법을 사용하여 여러 쿼리를 쉽게 전달하고 실행할 수 있습니다. 여러 쿼리로 분할될 수 있는 하나의 대규모 쿼리를 실행해야 하는 경우에는 이전 코드 조각 대신 다음 코드 조각을 시도해 보세요.

```java
for(SqlQuerySpec query:queries)
{
   objFlux= container.queryItems(query, fo);
   objFlux .publishOn(Schedulers.elastic())
         .subscribe(feedResponse->
            {
               if(feedResponse.results().size()>0)
               {
                  _docs.addAll(feedResponse.results());
               }
            
            },
            Throwable::printStackTrace,latch::countDown);
   lstFlux.add(objFlux);
}
                  
      Flux.merge(lstFlux);
      latch.await();
}
```

위 코드를 사용하여 쿼리를 병렬로 실행하고 최적화를 위해 분산 실행을 늘릴 수 있습니다. 또한 삽입 및 업데이트 작업도 실행할 수 있습니다.

### <a name="insert-operation"></a>삽입 작업

문서를 삽입하려면 다음 코드를 실행합니다.

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

그런 다음 Mono를 구독합니다.

```java
CountDownLatch latch=new CountDownLatch(1);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.statusCode()!=successStatus)
              {
                 throw new RuntimeException(resourceResponse.toString());
              }
           },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert 작업

Upsert 작업을 수행하려면 업데이트할 문서를 지정해야 합니다. 전체 문서를 페치하려면 읽기 작업 항목에서 언급된 코드 조각을 사용하고 필요한 필드를 수정할 수 있습니다. 다음 코드 조각은 문서를 upsert합니다.

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
그런 다음 Mono를 구독합니다. 삽입 작업에서 Mono 구독 코드 조각을 참조하세요.

### <a name="delete-operation"></a>삭제 작업

다음 코드 조각은 삭제 작업을 수행합니다.

```java
CosmosItem objItem= container.getItem(doc.Id, doc.Tenant);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

그런 다음 Mono를 구독합니다. 삽입 작업에서 Mono 구독 코드 조각을 참조하세요. 전체 코드 샘플은 [CouchbaseToCosmosDB-AsyncInSpring](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncInSpring) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="couchbase-as-a-keyvalue-pair"></a>키/값 쌍으로서의 Couchbase

쿼리 대신 조회를 수행할 수 있는 간단한 유형의 워크로드입니다. 키/값 쌍에는 다음 단계를 사용합니다.

1. "/ID"를 기본 키로 사용하는 것이 좋습니다. 이렇게 하면 특정 파티션에서 직접 조회 작업을 수행할 수 있습니다. 컬렉션을 만들고 "/ID"를 파티션 키로 지정합니다.

1. 인덱싱을 완전히 해제합니다. 조회 작업을 실행할 것이므로 인덱싱 오버헤드를 전달할 필요가 없습니다. 인덱싱을 끄려면 Azure Portal에 로그인하여 Azure Cosmos DB 계정으로 이동합니다. **데이터 탐색기** 를 열고 **데이터베이스** 및 **컨테이너** 를 선택합니다. **크기 조정 및 설정** 탭을 열고 **인덱싱 정책** 을 선택합니다. 현재 인덱싱 정책은 다음과 같습니다.
    
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
            "path": "/\"_etag\"/?"
        }
    ]
    }
   ````

   위의 인덱싱 정책을 다음 정책으로 바꿉니다.

   ```json
   {
    "indexingMode": "none",
    "automatic": false,
    "includedPaths": [],
    "excludedPaths": []
    }
   ```

1. 다음 코드 조각을 사용하여 연결 개체를 만듭니다. 연결 개체(@Bean에 배치하거나 정적으로 설정):

   ```java
   ConnectionPolicy cp=new ConnectionPolicy();
   cp.connectionMode(ConnectionMode.DIRECT);
   
   if(client==null)
      client= CosmosClient.builder()
         .endpoint(Host)//(Host, PrimaryKey, dbName, collName).Builder()
          .connectionPolicy(cp)
          .key(PrimaryKey)
          .consistencyLevel(ConsistencyLevel.EVENTUAL)
          .build();
    
   container = client.getDatabase(_dbName).getContainer(_collName);
   ```

이제 다음과 같이 CRUD 작업을 실행할 수 있습니다.

### <a name="read-operation"></a>읽기 작업

항목을 읽으려면 다음 코드 조각을 사용합니다.

```java        
CosmosItemRequestOptions ro=new CosmosItemRequestOptions();
ro.partitionKey(new PartitionKey(documentId));
CountDownLatch latch=new CountDownLatch(1);
      
var objCosmosItem= container.getItem(documentId, documentId);
Mono<CosmosItemResponse> objMono = objCosmosItem.read(ro);
objMono .subscribeOn(Schedulers.elastic())
        .subscribe(resourceResponse->
        {
           if(resourceResponse.item()!=null)
           {
              doc= resourceResponse.properties().toObject(UserModel.class);
           }
        },
        Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="insert-operation"></a>삽입 작업

항목을 삽입하려면 다음 코드를 실행할 수 있습니다.

```java
Mono<CosmosItemResponse> objMono= container.createItem(doc,ro);
```

그런 다음 Mono를 구독합니다.

```java
CountDownLatch latch=new CountDownLatch(1);
objMono.subscribeOn(Schedulers.elastic())
      .subscribe(resourceResponse->
      {
         if(resourceResponse.statusCode()!=successStatus)
            {
               throw new RuntimeException(resourceResponse.toString());
            }
         },
      Throwable::printStackTrace,latch::countDown);
latch.await();
```

### <a name="upsert-operation"></a>Upsert 작업

항목의 값을 업데이트하려면 아래 코드 조각을 참조하세요.

```java
Mono<CosmosItemResponse> obs= container.upsertItem(doc, ro);
```
그런 다음 Mono를 구독합니다. 삽입 작업에서 Mono 구독 코드 조각을 참조하세요.

### <a name="delete-operation"></a>삭제 작업

삭제 작업을 실행하려면 다음 코드 조각을 사용합니다.

```java
CosmosItem objItem= container.getItem(id, id);
Mono<CosmosItemResponse> objMono = objItem.delete(ro);
```

그런 다음 Mono를 구독합니다. 삽입 작업에서 Mono 구독 코드 조각을 참조하세요. 전체 코드 샘플은 [CouchbaseToCosmosDB-AsyncKeyValue](https://github.com/Azure-Samples/couchbaseTocosmosdb/tree/main/AsyncKeyValue) GitHub 리포지토리에서 사용할 수 있습니다.

## <a name="data-migration"></a>데이터 마이그레이션

데이터를 마이그레이션하는 방법은 두 가지입니다.

* **Azure Data Factory 사용:** 데이터를 마이그레이션하는 가장 권장되는 방법입니다. 원본을 Couchbase로 구성하고 싱크를 Azure Cosmos DB SQL API로 구성합니다. 자세한 단계는 Azure [Cosmos DB Data Factory 커넥터](../../data-factory/connector-azure-cosmos-db.md) 문서를 참조하세요.

* **Azure Cosmos DB 데이터 가져오기 도구 사용:** 이 옵션은 데이터 양이 더 적은 VM을 사용하여 마이그레이션하는 경우 권장됩니다. 자세한 단계는 [데이터 가져오기](../import-data.md) 문서를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 성능 테스트를 수행하려면 [Azure Cosmos DB를 사용하여 성능 및 확장성 테스트](./performance-testing.md) 문서를 참조하세요.
* 코드를 최적화하려면 [Azure Cosmos DB 성능 팁](./performance-tips-async-java.md) 문서를 참조하세요.
* Java Async V3 SDK, [SDK 참조](https://github.com/Azure/azure-cosmosdb-java/tree/v3) GitHub 리포지토리를 살펴보세요.
