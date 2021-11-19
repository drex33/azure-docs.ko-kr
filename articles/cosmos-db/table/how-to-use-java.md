---
title: Java용 Azure Tables 클라이언트 라이브러리 사용
description: Java용 Azure Tables 클라이언트 라이브러리를 사용하여 정형 데이터를 클라우드에 저장합니다.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: Java
ms.topic: sample
ms.date: 12/10/2020
author: ThomasWeiss
ms.author: thweiss
ms.custom: devx-track-java
ms.openlocfilehash: e5eab216c40245aef19a7fc9ef8fd1a5bd8bc029
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132487654"
---
# <a name="how-to-use-the-azure-tables-client-library-for-java"></a>Java용 Azure Tables 클라이언트 라이브러리를 사용하는 방법

[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

이 문서에서는 테이블을 만들고, 데이터를 저장하고, 해당 데이터에 대해 CRUD 작업을 수행하는 방법을 보여 줍니다. 샘플은 Java로 작성되었으며 [Java용 Azure Tables 클라이언트 라이브러리][Azure Tables client library for Java]를 사용합니다. 여기에서 다루는 시나리오에는 **creating**, **listing**, **deleting** 테이블과 테이블의 **inserting**, **querying**, **modifying**, **deleting** 엔터티가 포함됩니다. 테이블에 대한 자세한 내용은 [다음 단계](#next-steps) 섹션을 참조하십시오.

> [!IMPORTANT]
> Table Storage 및 Cosmos DB 테이블을 지원하는 Azure Tables 클라이언트 라이브러리의 최신 버전은 [12 이상][Azure Tables client library for Java]입니다.

## <a name="create-an-azure-service-account"></a>Azure 서비스 계정 만들기

[!INCLUDE [cosmos-db-create-azure-service-account](../includes/cosmos-db-create-azure-service-account.md)]

**Azure 스토리지 계정 만들기**

[!INCLUDE [cosmos-db-create-storage-account](../includes/cosmos-db-create-storage-account.md)]

**Azure Cosmos DB 계정 만들기**

[!INCLUDE [cosmos-db-create-tableapi-account](../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-java-application"></a>Java 애플리케이션 만들기

이 문서의 샘플을 사용하려면 다음을 수행합니다.
1. [JDK(Java Development Kit)](/azure/developer/java/fundamentals/java-support-on-azure#supported-java-versions-and-update-schedule)를 설치합니다.
2. Azure 구독에서 Azure 스토리지 계정 또는 Azure Cosmos DB 계정을 만듭니다.
3. 개발 시스템에서 GitHub의 [Java용 Azure Tables 클라이언트 라이브러리][Azure Tables client library for Java] 리포지토리에 나열된 최소 요구 사항 및 종속성을 충족하는지 확인합니다.
4. 지침에 따라 해당 리포지토리에서 Java용 Azure Storage 라이브러리를 다운로드하고 시스템에 설치합니다.
5. 이 문서의 예제를 사용하는 Java 앱을 만듭니다.

## <a name="configure-your-app-to-access-table-storage"></a>Table Storage에 액세스하도록 앱 구성

다음 항목을 *pom.xml* 파일의 `dependencies` 섹션에 추가합니다.

```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-data-tables</artifactId>
  <version>12.1.1</version>
</dependency>
```

그런 다음, Azure Tables API를 사용하여 테이블에 액세스하려는 Java 파일의 위쪽에 다음 `import` 문을 추가합니다.

```java
// Include the following imports to use table APIs
import com.azure.data.tables.TableClient;
import com.azure.data.tables.TableClientBuilder;
import com.azure.data.tables.TableServiceClient;
import com.azure.data.tables.TableServiceClientBuilder;
import com.azure.data.tables.models.ListEntitiesOptions;
import com.azure.data.tables.models.TableEntity;
import com.azure.data.tables.models.TableEntityUpdateMode;
import com.azure.data.tables.models.TableTransactionAction;
import com.azure.data.tables.models.TableTransactionActionType;
```

## <a name="add-your-connection-string"></a>연결 문자열 추가

Azure 스토리지 계정 또는 Azure Cosmos DB Table API 계정에 연결할 수 있습니다. 사용 중인 계정 유형에 따라 연결 문자열을 가져옵니다.

### <a name="add-an-azure-storage-connection-string"></a>Azure Storage 연결 문자열 추가

Azure Tables 클라이언트는 스토리지 연결 문자열을 사용하여 데이터 관리 서비스에 액세스하기 위한 엔드포인트 및 자격 증명을 저장할 수 있습니다. 클라이언트 앱에서 실행하는 경우 **AccountName** 및 **AccountKey** 값에 대해 [Azure Portal](https://portal.azure.com)에 나열된 Storage 계정의 Storage 계정 이름과 기본 액세스 키를 사용하여 다음 형식의 Storage 연결 문자열을 제공해야 합니다.

이 예제는 정적 필드가 연결 문자열을 포함할 수 있도록 선언하는 방법을 보여 줍니다.

```java
// Define the connection-string with your values.
public final String connectionString =
    "DefaultEndpointsProtocol=http;" +
    "AccountName=your_storage_account;" +
    "AccountKey=your_storage_account_key;" +
    "EndpointSuffix=core.windows.net;
```

### <a name="add-an-azure-cosmos-db-table-api-connection-string"></a>Azure Cosmos DB Table API 연결 문자열 추가

Azure Cosmos DB 계정은 연결 문자열을 사용하여 테이블 엔드포인트와 자격 증명을 저장합니다. 클라이언트 애플리케이션에서 실행할 경우, **AccountName** 과 **AccountKey** 값에 대해 [Azure Portal](https://portal.azure.com)에 나열된 Azure Cosmos DB 계정의 이름과 기본 액세스 키를 사용하여 다음 형식의 Azure Cosmos DB 연결 문자열을 제공해야 합니다.

이 예제에서는 고정 필드가 Azure Cosmos DB 연결 문자열을 보유하도록 선언하는 방법을 보여줍니다.

```java
public final String connectionString =
    "DefaultEndpointsProtocol=https;" + 
    "AccountName=your_cosmosdb_account;" + 
    "AccountKey=your_account_key;" + 
    "TableEndpoint=https://your_endpoint;" ;
```

Azure의 역할 내에서 실행되는 앱에서 이 문자열은 *ServiceConfiguration.cscfg* 서비스 구성 파일에 저장할 수 있습니다. 이는 `System.getenv` 메서드를 호출하여 액세스할 수 있습니다. 서비스 구성 파일의 *ConnectionString* 이라는 **Setting** 요소에서 연결 문자열을 가져오는 예제는 다음과 같습니다.

```java
// Retrieve storage account from connection-string.
String connectionString = System.getenv("ConnectionString");
```

프로젝트의 config.properties 파일에 연결 문자열을 저장할 수도 있습니다.

```java
connectionString = DefaultEndpointsProtocol=https;AccountName=your_account;AccountKey=your_account_key;TableEndpoint=https://your_table_endpoint/
```

다음 샘플에서는 스토리지 연결 문자열을 가져오기 위해 이러한 메서드 중 하나를 사용한 것으로 가정합니다.

## <a name="create-a-table"></a>테이블 만들기

`TableServiceClient` 개체를 사용하면 테이블을 만들고, 나열하고, 삭제하기 위해 Tables ​​서비스와 상호 작용할 수 있습니다. 다음 코드는 `TableServiceClient` 개체를 만들고, 이를 사용하여 `Employees`라는 테이블을 나타내는 새 `TableClient` 개체를 만듭니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Create the table if it not exists.
    TableClient tableClient = tableServiceClient.createTableIfNotExists(tableName);

}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="list-the-tables"></a>테이블 나열

테이블 목록을 가져오려면 `TableServiceClient.listTables` 메서드를 호출하여 반복 가능한 테이블 이름 목록을 검색합니다.

```java
try
{
    // Create a TableServiceClient with a connection string.
    TableServiceClient tableServiceClient = new TableServiceClientBuilder()
        .connectionString(connectionString)
        .buildClient();

    // Loop through a collection of table names.
    tableServiceClient.listTables().forEach(tableItem -> 
        System.out.printf(tableItem.getName())
    );
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가

다음 코드는 저장할 고객 데이터를 사용하여 `TableEntity` 클래스의 새 인스턴스를 만듭니다. 코드는 `TableClient` 개체에서 `upsertEntity` 메서드를 호출합니다. 이 메서드는 새 고객 엔터티를 `Employees` 테이블에 삽입하거나 엔터티가 이미 있는 경우 이를 대체합니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
     TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new employee TableEntity.
    String partitionKey = "Sales";
    String rowKey = "0001";
    Map<String, Object> personalInfo= new HashMap<>();
    personalInfo.put("FirstName", "Walter");
    personalInfo.put("LastName", "Harp");
    personalInfo.put("Email", "Walter@contoso.com");
    personalInfo.put("PhoneNumber", "425-555-0101");
    TableEntity employee = new TableEntity(partitionKey, rowKey).setProperties(personalInfo);
        
    // Upsert the entity into the table
    tableClient.upsertEntity(employee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-a-batch-of-entities"></a>엔터티 일괄 삽입

하나의 쓰기 작업으로 테이블 서비스에 엔터티를 일괄 삽입할 수 있습니다. 다음 코드는 `List<TableTransactionAction>` 개체를 만든 다음, 세 개의 upsert 작업을 이 개체에 추가합니다. 각 작업은 새 `TableEntity` 개체를 만들고, 해당 속성을 설정한 다음, `TableClient` 개체에서 `submitTransaction` 메서드를 호출하여 추가됩니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    String partitionKey = "Sales";
    List<TableTransactionAction> tableTransactionActions = new ArrayList<>();
    
    Map<String, Object> personalInfo1 = new HashMap<>();
    personalInfo1.put("FirstName", "Jeff");
    personalInfo1.put("LastName", "Smith");
    personalInfo1.put("Email", "Jeff@contoso.com");
    personalInfo1.put("PhoneNumber", "425-555-0104");
    
    // Create an entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0001")
            .setProperties(personalInfo1)
    ));
    
    Map<String, Object> personalInfo2 = new HashMap<>();
    personalInfo2.put("FirstName", "Ben");
    personalInfo2.put("LastName", "Johnson");
    personalInfo2.put("Email", "Ben@contoso.com");
    personalInfo2.put("PhoneNumber", "425-555-0102");
    
    // Create another entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0002")
            .setProperties(personalInfo2)
    ));
    
    Map<String, Object> personalInfo3 = new HashMap<>();
    personalInfo3.put("FirstName", "Denise");
    personalInfo3.put("LastName", "Rivers");
    personalInfo3.put("Email", "Denise@contoso.com");
    personalInfo3.put("PhoneNumber", "425-555-0103");
    
    // Create a third entity to add to the table.
    tableTransactionActions.add(new TableTransactionAction(
        TableTransactionActionType.UPSERT_MERGE,
        new TableEntity(partitionKey, "0003")
            .setProperties(personalInfo3)
    ));

    // Submit transaction on the "Employees" table.
    tableClient.submitTransaction(tableTransactionActions);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

일괄 작업에 대해 유의할 사항은 다음과 같습니다.

* 최대 100개의 삽입, 삭제, 병합, 바꾸기, 삽입 또는 병합 및 삽입 또는 바꾸기 작업을 임의로 조합하여 단일 일괄 작업으로 수행할 수 있습니다.
* 검색 작업이 일괄 작업의 유일한 작업이면 일괄 작업에 검색 작업이 포함될 수 있습니다.
* 단일 일괄 작업의 모든 엔터티에 동일한 파티션 키가 있어야 합니다.
* 일괄 작업은 4MB 데이터 페이로드로 제한됩니다.

## <a name="retrieve-all-entities-in-a-partition"></a>파티션의 모든 엔터티 검색

테이블에서 파티션의 엔터티를 쿼리하려는 경우 `ListEntitiesOptions`를 사용할 수 있습니다. 지정된 결과 유형을 반환하는 쿼리를 특정 테이블에 대해 만들려면 `ListEntitiesOptions.setFilter`을 호출합니다. 다음 코드는 `Sales`가 파티션 키인 엔터티에 대한 필터를 지정합니다. 쿼리가 `TableClient` 개체에서 `listEntities`을 호출하여 실행되면 `TableEntity`의 `Iterator`를 반환합니다. 그러면 "ForEach" 루프에서 반환된 `Iterator`를 사용하여 결과를 사용할 수 있습니다. 이 코드는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales'");

    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-range-of-entities-in-a-partition"></a>파티션의 엔터티 범위 검색

파티션의 모든 엔터티를 쿼리하지 않으려면 필터에서 비교 연산자를 사용하여 범위를 지정합니다. 다음 코드는 두 개의 필터를 결합하여 '0001'과 '0004' 사이의 행 키를 사용하여 `Sales` 파티션의 모든 엔터티를 가져옵니다. 그런 다음 쿼리 결과를 출력합니다. 이 가이드의 일괄 삽입 섹션에서 테이블에 추가된 엔터티를 사용하는 경우 이번에는 두 엔터티(Ben 및 Denise)만 반환됩니다.

```java
try
{
    // Define constants for filters.
    final String PARTITION_KEY = "PartitionKey";
    final String ROW_KEY = "RowKey";
    final String tableName = "Employees";

    // Create a TableServiceClient with a connection string.
    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition where the partition key is "Sales".
    ListEntitiesOptions options = new ListEntitiesOptions().setFilter(PARTITION_KEY + " eq 'Sales' AND " + ROW_KEY + " lt '0004' AND ROW_KEY + " gt '0001'");
    
    // Loop through the results, displaying information about the entities.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getPartitionKey() +
            " " + tableEntity.getRowKey() +
            "\t" + tableEntity.getProperty("FirstName") +
            "\t" + tableEntity.getProperty("LastName") +
            "\t" + tableEntity.getProperty("Email") +
            "\t" + tableEntity.getProperty("PhoneNumber"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="retrieve-a-single-entity"></a>단일 엔터티 검색

단일 특정 엔터티를 검색하는 쿼리를 작성할 수 있습니다. 다음 코드는 `ListEntitiesOptions`를 만들고 필터를 사용하여 동일한 작업을 수행하는 대신 파티션 키 및 행 키 매개 변수로 `TableClient.getEntity`를 호출하여 직원 "Jeff Smith"라는 직원에 대한 엔터티를 검색합니다. 코드가 실행되면 검색 작업은 컬렉션 대신 엔터티 1개만 반환합니다. 파티션과 행 키가 정확하게 일치하는 엔터티가 없는 경우 `null` 값이 반환됩니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 Table service에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Output the entity.
    if (specificEntity != null)
    {
        System.out.println(specificEntity.getPartitionKey() +
            " " + specificEntity.getRowKey() +
            "\t" + specificEntity.getProperty("FirstName") +
            "\t" + specificEntity.getProperty("LastName"));
            "\t" + specificEntity.getProperty("Email") +
            "\t" + specificEntity.getProperty("PhoneNumber"));
    }
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="modify-an-entity"></a>엔티티 수정

엔터티를 수정하려면 테이블 서비스에서 엔터티를 검색하고 엔터티 개체를 변경한 후, 바꾸기 또는 병합 작업으로 변경 사항을 테이블 서비스에 다시 저장하세요. 다음 코드에서는 기존 고객의 전화 번호를 변경합니다. 삽입하기 위해 수행한 것처럼 `tableClient.upsertEntity`를 호출하는 대신 이 코드는 `tableClient.updateEntity`를 호출합니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Get the specific entity.
    TableEntity specificEntity = tableClient.getEntity("Sales", "0001");

    // Specify a new phone number
    specificEntity.getProperties().put("PhoneNumber", "425-555-0105");

    // Update the specific entity
    tableClient.updateEntity(specificEntity, TableEntityUpdateMode.REPLACE);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="query-a-subset-of-entity-properties"></a>엔터티 속성 하위 집합 쿼리

테이블 쿼리에서는 엔터티에서 일부 속성만 검색할 수 있습니다. 프로젝션이라고 하는 이 기술은 특히 대역폭을 줄이며 큰 엔터티에 대한 쿼리 성능을 향상시킬 수 있습니다. 다음 코드의 쿼리는 `ListEntitiesOptions.setSelect` 메서드를 사용하여 테이블에 있는 엔터티의 이메일 주소만 반환합니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a filter condition that retrieves only the Email property.
    List<String> attributesToRetrieve = new ArrayList<>();
    attributesToRetrieve.add("Email");
    
    ListEntitiesOptions options = new ListEntitiesOptions().setSelect(attributesToRetrieve);

    // Loop through the results, displaying the Email values.
    tableClient.listEntities(options, null, null).forEach(tableEntity -> {
        System.out.println(tableEntity.getProperty("Email"));
    });
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="insert-or-replace-an-entity"></a>엔터티 삽입 또는 바꾸기

엔터티가 테이블에 이미 있는지 모르는 상태에서 테이블에 엔터티를 추가할 수 있습니다. insert-or-replace 작업을 통해 단일 요청을 수행할 수 있습니다. 해당 요청은 엔터티가 없는 경우 엔터티를 삽입하거나, 있는 경우 기존 엔터티를 바꿉니다. 이전의 예를 기반으로 하는 다음 코드에서는 'Walter Harp'에 대한 엔터티를 삽입하거나 바꿉니다. 새 엔터티가 만들어지면 이 코드는 `TableClient.upsertEntity` 메서드를 호출합니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Create a new table entity.
    Map<String, Object> properties = new HashMap<>();
    properties.put("FirstName", "Walter");
    properties.put("LastName", "Harp");
    properties.put("Email", "Walter@contoso.com");
    properties.put("PhoneNumber", "425-555-0101");
        
    TableEntity newEmployee = new TableEntity("Sales", "0004")
        .setProperties(properties);
        
    // Add the new customer to the Employees table.
    tableClient.upsertEntity(newEmployee);
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-an-entity"></a>엔터티 삭제

엔터티는 `TableClient.deleteEntity`을 통해 파티션 키와 행 키를 제공하여 삭제할 수 있습니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    Delete the entity for partition key 'Sales' and row key '0001' from the table.
    tableClient.deleteEntity("Sales", "0001");
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

## <a name="delete-a-table"></a>테이블 삭제

마지막으로 다음 코드는 계정에서 테이블을 삭제합니다. 테이블을 삭제한 후 약 40초 동안은 다시 만들 수 없습니다.

```java
try
{
    final String tableName = "Employees";

    // Create a TableClient with a connection string and a table name.
    TableClient tableClient = new TableClientBuilder()
        .connectionString(connectionString)
        .tableName(tableName)
        .buildClient();

    // Delete the table and all its data.
    tableClient.deleteTable();
}
catch (Exception e)
{
    // Output the stack trace.
    e.printStackTrace();
}
```

[!INCLUDE [storage-check-out-samples-java](../../../includes/storage-check-out-samples-java.md)]

## <a name="next-steps"></a>다음 단계

* [Java에서 Azure Table Service 시작](https://github.com/Azure-Samples/storage-table-java-getting-started)
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md)는 Windows, macOS 및 Linux에서 Azure Storage 데이터로 시각적으로 작업할 수 있도록 해주는 Microsoft의 독립 실행형 무료 앱입니다.
* [Java용 Azure Tables 클라이언트 라이브러리][Azure Tables client library for Java]
* [Azure Tables 클라이언트 라이브러리 참조][Azure Tables client library reference documentation]
* [Azure Tables REST API][Azure Tables REST API]
* [Azure Tables 팀 블로그][Azure Tables Team Blog]

자세한 내용은 [Java 개발자용 Azure](/java/azure)를 방문하세요.

[Azure SDK for Java]: https://go.microsoft.com/fwlink/?LinkID=525671
[Azure Tables client library for Java]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/tables/azure-data-tables
[Azure Tables client library reference documentation]: https://azure.github.io/azure-sdk-for-java/tables.html
[Azure Tables REST API]: /azure/storage/tables/table-storage-overview
[Azure Tables Team Blog]: https://blogs.msdn.microsoft.com/windowsazurestorage/
