---
title: '빠른 시작: Table API와 Node.js - Azure Cosmos DB'
description: 이 빠른 시작은 Azure Cosmos DB Table API를 사용하여 Azure Portal과 Node.js를 통해 애플리케이션을 만드는 방법을 보여 줍니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: devx-track-js, mode-other
ms.openlocfilehash: 1ae06f4a74151647af4c882b045bcbf2e529ff99
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056228"
---
# <a name="quickstart-build-a-table-api-app-with-nodejs-and-azure-cosmos-db"></a>빠른 시작: Node.js 및 Azure Cosmos DB를 사용하여 Table API 앱 빌드
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.JS](create-table-nodejs.md)
> * [Python](how-to-use-python.md)
> 

이 빠른 시작에서는 Azure Cosmos DB Table API 계정을 만들고 GitHub에서 복제된 Data Explorer 및 Node.js 앱을 사용하여 테이블과 엔터티를 만듭니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. URI가 `https://localhost:8081`이고 키가 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`인 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.
- [Node.js 0.10.29 이상](https://nodejs.org/).
- [Git](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

> [!IMPORTANT] 
> 일반 공급 Table API SDK를 사용하려면 새 Table API 계정을 만들어야 합니다. 미리 보기 중에 만들어진 Table API 계정은 일반 공급 SDK에서 지원되지 않습니다.
>

[!INCLUDE [cosmos-db-create-dbaccount-table](../includes/cosmos-db-create-dbaccount-table.md)]

## <a name="add-a-table"></a>테이블 추가

[!INCLUDE [cosmos-db-create-table](../includes/cosmos-db-create-table.md)]

## <a name="add-sample-data"></a>샘플 데이터 추가

[!INCLUDE [cosmos-db-create-table-add-sample-data](../includes/cosmos-db-create-table-add-sample-data.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

이제 GitHub에서 Table 앱을 복제하고 연결 문자열을 설정한 다음 실행해 보겠습니다. 프로그래밍 방식으로 데이터를 사용하여 얼마나 쉽게 작업할 수 있는지 알게 될 것입니다.

1. 명령 프롬프트를 열고, git-samples라는 새 폴더를 만든 다음 명령 프롬프트를 닫습니다.

    ```bash
    md "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/storage-table-node-getting-started.git
    ```

> [!TIP]
> 비슷한 코드에 대한 자세한 내용은 [Cosmos DB Table API 샘플](how-to-use-nodejs.md) 문서를 참조하세요. 

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 이 문서의 [연결 문자열 업데이트](#update-your-connection-string) 섹션으로 건너뛸 수 있습니다.

* 다음 코드에서는 Azure Storage 내에서 테이블을 만드는 방법을 보여줍니다.

  ```javascript
  storageClient.createTableIfNotExists(tableName, function (error, createResult) {
    if (error) return callback(error);

    if (createResult.isSuccessful) {
      console.log("1. Create Table operation executed successfully for: ", tableName);
    }
  }

  ```

* 다음 코드에서는 테이블에 데이터를 삽입하는 방법을 보여줍니다.

  ```javascript
  var customer = createCustomerEntityDescriptor("Harp", "Walter", "Walter@contoso.com", "425-555-0101");

  storageClient.insertOrMergeEntity(tableName, customer, function (error, result, response) {
    if (error) return callback(error);

    console.log("   insertOrMergeEntity succeeded.");
  }
  ```

* 다음 코드에서는 테이블에서 데이터를 쿼리하는 방법을 보여줍니다.

  ```javascript
  console.log("6. Retrieving entities with surname of Smith and first names > 1 and <= 75");

  var storageTableQuery = storage.TableQuery;
  var segmentSize = 10;

  // Demonstrate a partition range query whereby we are searching within a partition for a set of entities that are within a specific range. 
  var tableQuery = new storageTableQuery()
      .top(segmentSize)
      .where('PartitionKey eq ?', lastName)
      .and('RowKey gt ?', "0001").and('RowKey le ?', "0075");
  
  runPageQuery(tableQuery, null, function (error, result) {
  
      if (error) return callback(error);
  
  ```

* 다음 코드에서는 테이블에서 데이터를 삭제하는 방법을 보여줍니다.

  ```javascript
  storageClient.deleteEntity(tableName, customer, function entitiesQueried(error, result) {
      if (error) return callback(error);
  
      console.log("   deleteEntity succeeded.");
  }
  ```
  
## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다. 

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

    :::image type="content" source="./media/create-table-nodejs/connection-string.png" alt-text="연결 문자열 창에서 필요한 연결 문자열 정보 보기 및 복사":::

2. 오른쪽의 복사 단추를 사용하여 기본 연결 문자열을 복사합니다.

3. *app.config* 파일을 열고 세 번째 줄의 connectionString에 값을 붙여넣습니다. 

    > [!IMPORTANT]
    > 엔드포인트에서 documents.azure.com을 사용하면 미리 보기 계정이 있다는 것을 의미하고 일반 공급 Table API SDK를 사용하려면 [새 Table API 계정](#create-a-database-account)을 만들어야 합니다.
    >

3. *app.config* 파일을 저장합니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="run-the-app"></a>앱 실행

1. git 터미널 창에서 storage-table-java-getting-started 폴더로 `cd`를 실행합니다.

    ```
    cd "C:\git-samples\storage-table-node-getting-started"
    ```

2. 다음 명령을 실행하여 [azure], [node-uuid], [nconf] 및 [async] 모듈을 로컬에 설치하고 해당 모듈의 항목을 *package.json* 파일에 저장합니다.

   ```
   npm install azure-storage node-uuid async nconf --save
   ```

2. git 터미널 창에서 다음 명령을 실행하여 Node.js 애플리케이션을 실행합니다.

    ```
    node ./tableSample.js 
    ```

    Azure Cosmos DB에서 새 테이블 데이터베이스에 추가되는 테이블 데이터가 콘솔 창에 표시됩니다.

    이제 데이터 탐색기로 돌아가서 이 새 데이터를 보고, 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB 계정을 만들고, Data Explorer를 사용하여 테이블을 만들고, Node.js 앱을 실행하여 테이블 데이터를 추가하는 방법을 알아보았습니다.  이제 테이블 API를 사용하여 데이터를 쿼리할 수 있습니다.  

> [!div class="nextstepaction"]
> [Table API로 테이블 데이터 가져오기](table-import.md)
