---
title: Table API 및 Java를 사용하여 앱 빌드 - Azure Cosmos DB
description: 이 빠른 시작은 Azure Portal 및 Java와 함께 Azure Cosmos DB 테이블 API를 사용하여 애플리케이션을 만드는 방법을 보여줍니다.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: java
ms.topic: quickstart
ms.date: 05/28/2020
ms.author: sngun
ms.custom: seo-java-august2019, seo-java-september2019, devx-track-java, mode-other
ms.openlocfilehash: 547ac42af0ea4a9a6f5392b1d13ad02916fefdbe
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056241"
---
# <a name="quickstart-build-a-java-app-to-manage-azure-cosmos-db-table-api-data"></a>빠른 시작: Azure Cosmos DB Table API 데이터를 관리하는 Java 앱 빌드
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-table-dotnet.md)
> * [Java](create-table-java.md)
> * [Node.JS](create-table-nodejs.md)
> * [Python](how-to-use-python.md)
> 

이 빠른 시작에서는 Azure Cosmos DB Table API 계정을 만들고 GitHub에서 복제된 Data Explorer 및 Java 앱을 사용하여 테이블과 엔터티를 만듭니다. Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다.

## <a name="prerequisites"></a>사전 요구 사항

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. URI가 `https://localhost:8081`이고 키가 `C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==`인 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.
- [JDK(Java Development Kit) 8](https://www.azul.com/downloads/azure-only/zulu/?&version=java-8-lts&architecture=x86-64-bit&package=jdk). `JAVA_HOME` 환경 변수를 JDK가 설치된 폴더로 지정합니다.
- [Maven 이진 보관 파일](https://maven.apache.org/download.cgi). 
- [Git](https://www.git-scm.com/downloads) 

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
    git clone https://github.com/Azure-Samples/storage-table-java-getting-started.git 
    ```

> [!TIP]
> 비슷한 코드에 대한 자세한 내용은 [Cosmos DB Table API 샘플](how-to-use-java.md) 문서를 참조하세요. 

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 데이터베이스 리소스를 코드로 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 이 문서의 [연결 문자열 업데이트](#update-your-connection-string) 섹션으로 건너뛸 수 있습니다.

* 다음 코드에서는 Azure Storage 내에서 테이블을 만드는 방법을 보여줍니다.

  ```java
  private static CloudTable createTable(CloudTableClient tableClient, String tableName) throws StorageException, RuntimeException, IOException, InvalidKeyException,   IllegalArgumentException, URISyntaxException, IllegalStateException {
  
    // Create a new table
    CloudTable table = tableClient.getTableReference(tableName);
    try {
        if (table.createIfNotExists() == false) {
            throw new IllegalStateException(String.format("Table with name \"%s\" already exists.", tableName));
        }
    }
    catch (StorageException s) {
        if (s.getCause() instanceof java.net.ConnectException) {
            System.out.println("Caught connection exception from the client. If running with the default configuration please make sure you have started the storage emulator.");
        }
        throw s;
    }

    return table;
  }
  ```

* 다음 코드에서는 테이블에 데이터를 삽입하는 방법을 보여줍니다.

  ```javascript
  private static void batchInsertOfCustomerEntities(CloudTable table) throws StorageException {
  
  // Create the batch operation
  TableBatchOperation batchOperation1 = new TableBatchOperation();
  for (int i = 1; i <= 50; i++) {
      CustomerEntity entity = new CustomerEntity("Smith", String.format("%04d", i));
      entity.setEmail(String.format("smith%04d@contoso.com", i));
      entity.setHomePhoneNumber(String.format("425-555-%04d", i));
      entity.setWorkPhoneNumber(String.format("425-556-%04d", i));
      batchOperation1.insertOrMerge(entity);
  }
  
  // Execute the batch operation
  table.execute(batchOperation1);
  }
  ```

* 다음 코드에서는 테이블에서 데이터를 쿼리하는 방법을 보여줍니다.

  ```java
  private static void partitionScan(CloudTable table, String partitionKey) throws StorageException {
  
      // Create the partition scan query
      TableQuery<CustomerEntity> partitionScanQuery = TableQuery.from(CustomerEntity.class).where(
          (TableQuery.generateFilterCondition("PartitionKey", QueryComparisons.EQUAL, partitionKey)));
  
      // Iterate through the results
      for (CustomerEntity entity : table.execute(partitionScanQuery)) {
          System.out.println(String.format("\tCustomer: %s,%s\t%s\t%s\t%s", entity.getPartitionKey(), entity.getRowKey(), entity.getEmail(), entity.getHomePhoneNumber(), entity.  getWorkPhoneNumber()));
      }
  }
  ```

* 다음 코드에서는 테이블에서 데이터를 삭제하는 방법을 보여줍니다.

  ```java
  
  System.out.print("\nDelete any tables that were created.");
  
  if (table1 != null && table1.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table1.getName()));
  }
  
  if (table2 != null && table2.deleteIfExists() == true) {
      System.out.println(String.format("\tSuccessfully deleted the table: %s", table2.getName()));
  }
  ```

## <a name="update-your-connection-string"></a>연결 문자열 업데이트

이제 Azure Portal로 다시 이동하여 연결 문자열 정보를 가져와서 앱에 복사합니다. 이를 통해 앱이 호스팅된 데이터베이스와 통신할 수 있게 됩니다. 

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

   :::image type="content" source="./media/create-table-java/cosmos-db-quickstart-connection-string.png" alt-text="연결 문자열 창에서 연결 문자열 정보 보기":::

2. 오른쪽의 복사 단추를 사용하여 기본 연결 문자열을 복사합니다.

3. *C:\git-samples\storage-table-java-getting-started\src\main\resources* 폴더에서 *config.properties* 를 엽니다. 

5. 첫 번째 줄을 주석으로 처리하고 두 번째 줄의 주석 처리를 제거합니다. 이제 처음 두 줄은 아래와 같은 모양입니다.

    ```xml
    #StorageConnectionString = UseDevelopmentStorage=true
    StorageConnectionString = DefaultEndpointsProtocol=https;AccountName=[ACCOUNTNAME];AccountKey=[ACCOUNTKEY]
    ```

6. 포털의 기본 연결 문자열을 2번째 줄의 StorageConnectionString 값에 붙여 넣습니다. 

    > [!IMPORTANT]
    > 엔드포인트에서 documents.azure.com을 사용하면 미리 보기 계정이 있다는 것을 의미하고 일반 공급 Table API SDK를 사용하려면 [새 Table API 계정](#create-a-database-account)을 만들어야 합니다.
    >

7. *config.properties* 파일을 저장합니다.

이제 Azure Cosmos DB와 통신하는 데 필요한 모든 정보로 앱이 업데이트되었습니다. 

## <a name="run-the-app"></a>앱 실행

1. git 터미널 창에서 storage-table-java-getting-started 폴더로 `cd`를 실행합니다.

    ```git
    cd "C:\git-samples\storage-table-java-getting-started"
    ```

2. git 터미널 창에서 다음 명령을 실행하여 Java 애플리케이션을 실행합니다.

    ```git
    mvn compile exec:java 
    ```

    Azure Cosmos DB에서 새 테이블 데이터베이스에 추가되는 테이블 데이터가 콘솔 창에 표시됩니다.

    이제 데이터 탐색기로 돌아가서 이 새 데이터를 보고, 쿼리 및 수정하고 작업에 사용할 수 있습니다. 

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cosmos DB 계정을 만들고, Data Explorer를 사용하여 테이블을 만들고, Java 앱을 실행하여 테이블 데이터를 추가하는 방법을 알아보았습니다.  이제 테이블 API를 사용하여 데이터를 쿼리할 수 있습니다.  

> [!div class="nextstepaction"]
> [Table API로 테이블 데이터 가져오기](table-import.md)
