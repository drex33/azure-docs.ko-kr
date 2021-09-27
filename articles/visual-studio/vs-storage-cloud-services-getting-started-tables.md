---
title: Visual Studio를 사용하여 Table Storage 시작하기(클라우드 서비스)
description: Visual Studio 연결 서비스를 사용하여 스토리지 계정에 연결한 후 Visual Studio 클라우드 서비스 프로젝트에서 Azure Table Storage 사용을 시작하는 방법입니다.
services: storage
author: ghogen
manager: jillfra
ms.assetid: a3a11ed8-ba7f-4193-912b-e555f5b72184
ms.prod: visual-studio-dev15
ms.technology: vs-azure
ms.custom: vs-azure, devx-track-csharp
ms.workload: azure-vs
ms.topic: conceptual
ms.date: 12/02/2016
ms.author: ghogen
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 3cad09a5359bd5d9bd2041eb92d0e994adf80080
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124823082"
---
# <a name="getting-started-with-azure-table-storage-and-visual-studio-connected-services-cloud-services-projects"></a>Azure Table Storage 및 Visual Studio 연결 서비스 시작(클라우드 서비스 프로젝트)
[!INCLUDE [storage-try-azure-tools-tables](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>개요

[!INCLUDE [Cloud Services (classic) deprecation announcement](../cloud-services/includes/deprecation-announcement.md)]


이 문서에서는 Visual Studio **연결된 서비스 추가** 대화 상자를 사용하여 클라우드 서비스 프로젝트에서 Azure 스토리지 계정을 만들거나 참조한 후 Visual Studio에서 Azure Table Storage를 사용하는 방법을 설명합니다. **연결된 서비스 추가** 작업은 프로젝트의 Azure Storage에 액세스하는 데 적합한 NuGet 패키지를 설치하고 프로젝트 구성 파일에 스토리지 계정에 대한 연결 문자열을 추가합니다.

Azure Table Storage 서비스를 사용하면 많은 양의 구조화된 데이터를 저장할 수 있습니다. 이 서비스는 Azure 클라우드 내부 및 외부에서 인증된 호출을 수락하는 NoSQL 데이터 저장소입니다. Azure 테이블은 구조화된 비관계형 데이터를 저장하는 데 적합합니다.

시작하려면 먼저 스토리지 계정에서 테이블을 만들어야 합니다. 코드에서 Azure 테이블을 만드는 방법과 기본 테이블 및 테이블 엔터티 추가, 수정, 읽기와 같은 엔터티 작업을 수행하는 방법을 살펴보겠습니다. 샘플은 C\# 코드로 작성되었으며, [.NET용 Microsoft Azure Storage 클라이언트 라이브러리](/previous-versions/azure/dn261237(v=azure.100))를 사용합니다.

**참고:** Azure Storage에 대한 호출을 수행하는 일부 API는 비동기적입니다. 자세한 내용은 [Async 및 Await를 사용한 비동기 프로그래밍](/previous-versions/hh191443(v=vs.140)) 을 참조하세요. 아래 코드에서는 비동기 프로그래밍 메서드를 사용한다고 가정합니다.

* 테이블을 프로그래밍 방식으로 조작하는 방법에 대한 자세한 내용은 [.NET을 사용하여 Azure Table Storage 시작](../cosmos-db/tutorial-develop-table-dotnet.md) 을 참조하세요.
* Azure Storage에 대한 일반적인 내용은 [스토리지 설명서](https://azure.microsoft.com/documentation/services/storage/)를 참조하세요.
* Azure Cloud Services에 대한 일반적인 내용은 [Cloud Services 설명서](https://azure.microsoft.com/documentation/services/cloud-services/) 를 참조하세요.
* ASP.NET 애플리케이션을 프로그래밍하는 방법에 대한 자세한 내용은 [ASP.NET](https://www.asp.net) 을 참조하세요.

## <a name="access-tables-in-code"></a>코드에서 테이블 액세스하기
클라우드 서비스 프로젝트의 테이블에 액세스하려면 Azure Table Storage에 액세스하는 C# 소스 파일에 다음 항목을 포함해야 합니다.

1. C# 파일 맨 위의 네임스페이스 선언에 이러한 **using** 문이 포함되어 있는지 확인합니다.
   
    ```csharp
    using Microsoft.Framework.Configuration;
    using Azure.Data.Table;
    using System.Collections.Generic
    using System.Threading.Tasks;
    using LogLevel = Microsoft.Framework.Logging.LogLevel;
    ```
2. **AzureStorageConnectionString** 개체를 가져와 테이블 만들기 및 삭제와 같은 계정 수준 작업을 수행 하는 **TableServiceClient** 를 만듭니다.
   
    ```csharp
    string storageConnString = "_AzureStorageConnectionString"
    ```

   > [!NOTE]
   > 다음 샘플의 코드 앞에 위의 코드를 모두 사용합니다.
   
3. 저장소 계정의 테이블 개체를 참조 하는 **TableServiceClient** 개체를 가져옵니다.
   
    ```csharp
    // Create the table service client.
    TableServiceClient tableServiceClient = new TableServiceClient(storageConnString);
    ```

4. 특정 테이블 및 엔터티를 참조 하는 **TableClient** reference 개체를 가져옵니다.
   
    ```csharp
    // Get a reference to a table named "peopleTable".
    TableClient peopleTable = tableServiceClient.GetTableClient("peopleTable");
    ```

## <a name="create-a-table-in-code"></a>코드에서 테이블 만들기
Azure 테이블을 만들려면 "코드에서 테이블 액세스" 섹션에 설명 된 대로 **TableClient** 개체를 가져온 후에 **CreateIfNotExistsAsync** 에 대 한 호출을에 추가 하면 됩니다.

```csharp
// Create the TableClient if it does not exist.
await peopleTable.CreateIfNotExistsAsync();
```

## <a name="add-an-entity-to-a-table"></a>테이블에 엔터티 추가
테이블에 엔터티를 추가하려면 엔터티의 속성을 정의하는 클래스를 만듭니다. 다음 코드에서는 고객의 이름을 행 키로 사용하고 성을 파티션 키로 사용하는 **CustomerEntity** 라는 엔터티 클래스를 정의합니다.

```csharp
public class CustomerEntity : ITableEntity
{
    public CustomerEntity(string lastName, string firstName)
    {
        this.PartitionKey = lastName;
        this.RowKey = firstName;
    }

    public CustomerEntity() { }

    public string Email { get; set; }

    public string PhoneNumber { get; set; }
}
```

엔터티와 관련 된 AddEntity 작업은 이전에 "코드에서 테이블 액세스"에서 만든 **TableClient** 개체를 사용 하 여 수행 됩니다. **Addentity** 메서드는 수행할 작업을 나타냅니다. 다음 코드 예제에서는 **TableClient** 개체와 **customerentity** 개체를 만드는 방법을 보여 줍니다. 작업을 준비 하기 위해 **Addentity** 는 customer 엔터티를 테이블에 삽입 합니다.

```csharp
// Create a new customer entity.
CustomerEntity customer1 = new CustomerEntity("Harp", "Walter");
customer1.Email = "Walter@contoso.com";
customer1.PhoneNumber = "425-555-0101";

// Inserts the customer entity.
peopleTable.AddEntity(customer1)
```


## <a name="insert-a-batch-of-entities"></a>엔터티 일괄 삽입
하나의 쓰기 작업으로 테이블에 여러 엔터티를 삽입할 수 있습니다. 다음 코드 예제에서는 두 엔터티 개체 ("Jeff Smith" 및 "이혜준 씨")를 만들고 AddRange 메서드를 사용 하 여 **Addentitiesbatch** 개체에 추가한 다음 **TableClient 비동기** 작업을 호출 하 여 작업을 시작 합니다.

```csharp
// Create a list of 2 entities with the same partition key.
List<CustomerEntity> entityList = new List<CustomerEntity>
{
    new CustomerEntity("Smith", "Jeff")
    {
        { "Email", "Jeff@contoso.com" },
        { "PhoneNumber", "425-555-0104" }
    },
    new CustomerEntity("Smith", "Ben")
    {
        { "Email", "Ben@contoso.com" },
        { "PhoneNumber", "425-555-0102" }
    },
};

// Create the batch.
List<TableTransactionAction> addEntitiesBatch = new List<TableTransactionAction>();

// Add the entities to be added to the batch.
addEntitiesBatch.AddRange(entityList.Select(e => new TableTransactionAction(TableTransactionActionType.Add, e)));

// Submit the batch.
Response<IReadOnlyList<Response>> response = await peopleTable.SubmitTransactionAsync(addEntitiesBatch).ConfigureAwait(false);
```

## <a name="get-all-of-the-entities-in-a-partition"></a>파티션의 모든 엔터티 가져오기
테이블에서 파티션의 모든 엔터티를 쿼리하려면 **쿼리** 메서드를 사용 합니다. 다음 코드 예제에서는 'Smith'가 파티션 키인 엔터티에 대한 필터를 지정합니다. 이 예제에서는 쿼리 결과에 있는 각 엔터티의 필드를 콘솔에 출력합니다.

```csharp
Pageable<CustomerEntity> queryResultsFilter = peopleTable.Query<CustomerEntity>(filter: "PartitionKey eq 'Smith'");

// Print the fields for each customer.
foreach (CustomerEntity qEntity in queryResultsFilter)
{
    Console.WriteLine("{0}, {1}\t{2}\t{3}", qEntity.PartitionKey, qEntity.RowKey, qEntity.Email, qEntity.PhoneNumber);
}
```


## <a name="get-a-single-entity"></a>단일 엔터티 가져오기
단일 특정 엔터티를 가져오는 쿼리를 작성할 수 있습니다. 다음 코드에서는 **Getentityasync** 메서드를 사용 하 여 이름이 ' 이혜준 Smith ' 인 고객을 지정 합니다. 이 메서드는 컬렉션 대신 하나의 엔터티를 반환 하 고, **Getentityasync** 에서 반환 된 값을 반환 합니다. Result는 **customerentity** 개체입니다. 쿼리에 파티션과 행 키를 모두 지정하는 것이 **테이블** 서비스에서 단일 엔터티를 검색하는 가장 빠른 방법입니다.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

// Print the phone number of the result.
if (singleResult.Result != null)
    Console.WriteLine(((CustomerEntity)singleResult.Result).PhoneNumber);
else
    Console.WriteLine("The phone number could not be retrieved.");
```

## <a name="delete-an-entity"></a>엔터티 삭제
엔터티를 찾은 후 삭제할 수 있습니다. 다음 코드에서는 "Ben Smith"라는 고객 엔터티를 검색하고, 찾으면 삭제합니다.

```csharp
var singleResult = peopleTable.GetEntityAsync<CustomerEntity>("Smith", "Ben");

CustomerEntity deleteEntity = (CustomerEntity)singleResult.Result;

// Delete the entity given the partition and row key.
if (deleteEntity != null)
{
    await peopleTable.DeleteEntity(deleteEntity.PartitionKey, deleteEntity.RowKey);

    Console.WriteLine("Entity deleted.");
}

else
    Console.WriteLine("Couldn't delete the entity.");
```

## <a name="next-steps"></a>다음 단계
[!INCLUDE [vs-storage-dotnet-tables-next-steps](../../includes/vs-storage-dotnet-tables-next-steps.md)]
