---
title: Azure Cosmos DB에서 JavaScript 쿼리 API를 사용하여 저장 프로시저 및 트리거 작성
description: Azure Cosmos DB에서 JavaScript 쿼리 API를 사용하여 저장 프로시저 및 트리거를 작성하는 방법 알아보기
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/07/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: fd3e129cfa2b733a53ab9cee1a2008882b77bec3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116960"
---
# <a name="how-to-write-stored-procedures-and-triggers-in-azure-cosmos-db-by-using-the-javascript-query-api"></a>Azure Cosmos DB에서 JavaScript 쿼리 API를 사용하여 저장 프로시저 및 트리거를 작성하는 방법
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB를 사용하면 저장 프로시저 또는 트리거를 작성하는 데 사용할 수 있는 SQL 언어에 대한 지식 없이 유연한 JavaScript 인터페이스를 사용하여 최적화된 쿼리를 수행할 수 있습니다. Azure Cosmos DB에서 JavaScript 쿼리 API 지원에 대한 자세한 내용은 [Azure Cosmos DB에서 JavaScript 언어 통합 쿼리 API 작업](javascript-query-api.md) 문서를 참조하세요.

## <a name="stored-procedure-using-the-javascript-query-api"></a><a id="stored-procedures"></a>JavaScript 쿼리 API를 사용한 저장 프로시저

다음 코드 샘플은 저장 프로시저의 컨텍스트에서 JavaScript 쿼리 API를 사용하는 방법의 예제입니다. 저장 프로시저는 입력 매개 변수에서 지정된 Azure Cosmos 항목을 삽입하고, 입력 항목의 크기 속성에 따라 minSize, maxSize 및 totalSize가 있는 `__.filter()` 메서드를 사용하여 메타데이터 문서를 업데이트합니다.

> [!NOTE]
> JavaScript 쿼리 API를 사용하는 경우 `__`(이중 밑줄)은 `getContext().getCollection()`에 대한 별칭입니다.

```javascript
/**
 * Insert an item and update metadata doc: minSize, maxSize, totalSize based on item.size.
 */
function insertDocumentAndUpdateMetadata(item) {
  // HTTP error codes sent to our callback function by CosmosDB server.
  var ErrorCode = {
    RETRY_WITH: 449,
  }

  var isAccepted = __.createDocument(__.getSelfLink(), item, {}, function(err, item, options) {
    if (err) throw err;

    // Check the item (ignore items with invalid/zero size and metadata itself) and call updateMetadata.
    if (!item.isMetadata && item.size > 0) {
      // Get the metadata. We keep it in the same container. it's the only item that has .isMetadata = true.
      var result = __.filter(function(x) {
        return x.isMetadata === true
      }, function(err, feed, options) {
        if (err) throw err;

        // We assume that metadata item was pre-created and must exist when this script is called.
        if (!feed || !feed.length) throw new Error("Failed to find the metadata item.");

        // The metadata item.
        var metaItem = feed[0];

        // Update metaDoc.minSize:
        // for 1st document use doc.Size, for all the rest see if it's less than last min.
        if (metaItem.minSize == 0) metaItem.minSize = item.size;
        else metaItem.minSize = Math.min(metaItem.minSize, item.size);

        // Update metaItem.maxSize.
        metaItem.maxSize = Math.max(metaItem.maxSize, item.size);

        // Update metaItem.totalSize.
        metaItem.totalSize += item.size;

        // Update/replace the metadata item in the store.
        var isAccepted = __.replaceDocument(metaItem._self, metaItem, function(err) {
          if (err) throw err;
          // Note: in case concurrent updates causes conflict with ErrorCode.RETRY_WITH, we can't read the meta again
          //       and update again because due to Snapshot isolation we will read same exact version (we are in same transaction).
          //       We have to take care of that on the client side.
        });
        if (!isAccepted) throw new Error("replaceDocument(metaItem) returned false.");
      });
      if (!result.isAccepted) throw new Error("filter for metaItem returned false.");
    }
  });
  if (!isAccepted) throw new Error("createDocument(actual item) returned false.");
}
```

## <a name="next-steps"></a>다음 단계

Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Azure Cosmos DB에서 저장 프로시저, 트리거 및 사용자 정의 함수로 작업하는 방법](how-to-use-stored-procedures-triggers-udfs.md)

* [Azure Cosmos DB에서 저장 프로시저를 등록하고 사용하는 방법](how-to-use-stored-procedures-triggers-udfs.md#stored-procedures)

* Azure Cosmos DB에서 [사전 트리거](how-to-use-stored-procedures-triggers-udfs.md#pre-triggers) 및 [사후 트리거](how-to-use-stored-procedures-triggers-udfs.md#post-triggers)를 등록하고 사용하는 방법

* [Azure Cosmos DB에서 사용자 정의 함수를 등록하고 사용하는 방법](how-to-use-stored-procedures-triggers-udfs.md#udfs)

* [Azure Cosmos DB의 가상 파티션 키](synthetic-partition-keys.md)
