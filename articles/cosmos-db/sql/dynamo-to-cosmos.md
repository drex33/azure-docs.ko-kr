---
title: Amazon DynamoDB에서 Azure Cosmos DB로 애플리케이션 마이그레이션
description: Amazon DynamoDB에서 Azure Cosmos DB로 .NET 애플리케이션을 마이그레이션하는 방법 알아보기
author: manishmsfte
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 04/29/2020
ms.author: mansha
ms.openlocfilehash: f7f5f5cc775cb9673cbb6f3dd0355887463744c3
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117039"
---
# <a name="migrate-your-application-from-amazon-dynamodb-to-azure-cosmos-db"></a>Amazon DynamoDB에서 Azure Cosmos DB로 애플리케이션 마이그레이션
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB는 확장 가능하고 전 세계적으로 분산되고 완전히 관리되는 데이터베이스입니다. 데이터에 대한 짧은 대기 시간 액세스를 보장합니다. Azure Cosmos DB에 대한 자세한 내용은 [개요](../introduction.md) 문서를 참조하세요. 이 문서에서는 최소한의 코드 변경으로 DynamoDB에서 Azure Cosmos DB로 .NET 애플리케이션을 마이그레이션하는 방법을 설명합니다.

## <a name="conceptual-differences"></a>개념적 차이점

다음은 Azure Cosmos DB와 DynamoDB의 주요 개념적 차이점입니다.

|  DynamoDB | Azure Cosmos DB  |
|---|---|
|해당 없음|  데이터베이스 |
|테이블      |  컬렉션 |
|  항목 |  문서 |
|attribute|필드|
|보조 인덱스|보조 인덱스|
|기본 키 - 파티션 키|Partition Key|
|기본 키 - 정렬 키| 필수 아님 |
|STREAM|변경 피드|
|쓰기 컴퓨팅 단위|요청 단위(유연, 읽기 또는 쓰기에 사용할 수 있음)|
|읽기 컴퓨팅 단위    |요청 단위(유연, 읽기 또는 쓰기에 사용할 수 있음)|
|전역 테이블| 필요하지 않음. Azure Cosmos 계정을 프로비저닝하는 동안 지역을 직접 선택할 수 있습니다(나중에 지역을 변경할 수 있음).|

## <a name="structural-differences"></a>구조적 차이점

DynamoDB와 비교할 때 Azure Cosmos DB의 JSON 구조가 더 간단합니다. 다음 예제는 차이점을 보여 줍니다.

**DynamoDB**:

다음 JSON 개체는 DynamoDB의 데이터 형식을 나타냅니다.

```json
{
TableName: "Music",
KeySchema: [
{ 
  AttributeName: "Artist",
  KeyType: "HASH", //Partition key
},
{ 
  AttributeName: "SongTitle",
  KeyType: "RANGE" //Sort key
}
],
AttributeDefinitions: [
{ 
  AttributeName: "Artist",
  AttributeType: "S"
},
{ 
  AttributeName: "SongTitle",
  AttributeType: "S"
}
],
ProvisionedThroughput: {
  ReadCapacityUnits: 1,
  WriteCapacityUnits: 1
 }
}
 ```

**Azure Cosmos DB**:

다음 JSON 개체는 Azure Cosmos DB의 데이터 형식을 나타냅니다.

```json
{
"Artist": "",
"SongTitle": "",
"AlbumTitle": "",
"Year": 9999,
"Price": 0.0,
"Genre": "",
"Tags": ""
}
```

## <a name="migrate-your-data"></a>데이터 마이그레이션

데이터를 Azure Cosmos DB로 마이그레이션하는 데 사용할 수 있는 다양한 옵션이 있습니다. 자세한 내용은 [온-프레미스 또는 클라우드 데이터를 Azure Cosmos DB로 마이그레이션하는 옵션](../cosmosdb-migrationchoices.md) 문서를 참조하세요.

## <a name="migrate-your-code"></a>코드 마이그레이션

이 문서는 애플리케이션의 코드를 Azure Cosmos DB로 마이그레이션하는 과정을 다룹니다. 이 과정은 데이터베이스 마이그레이션의 중요한 측면입니다. 학습 곡선을 줄일 수 있도록 다음 섹션에서는 Amazon DynamoDB의 코드와 Azure Cosmos DB의 동일한 코드 조각을 나란히 비교합니다.

소스 코드를 다운로드하려면 다음 리포지토리를 복제합니다.

```bash
git clone https://github.com/Azure-Samples/DynamoDB-to-CosmosDB
```

### <a name="pre-requisites"></a>필수 구성 요소

- .NET Framework 4.7.2
- Visual Studio 2019
- Azure Cosmos DB SQL API 계정에 대한 액세스 권한
- Amazon DynamoDB의 로컬 설치
- Java 8
- 포트 8000에서 다운로드 가능한 Amazon DynamoDB 버전 실행(코드를 변경하고 구성할 수 있음)

### <a name="set-up-your-code"></a>코드 설정

다음 "NuGet 패키지"를 프로젝트에 추가합니다.

```bash
Install-Package Microsoft.Azure.Cosmos 
```

### <a name="establish-connection"></a>연결 설정

**DynamoDB**:

Amazon DynamoDB에서 다음 코드를 사용하여 연결합니다.

```csharp
    AmazonDynamoDBConfig addbConfig = new AmazonDynamoDBConfig() ;
        addbConfig.ServiceURL = "endpoint";
        try { aws_dynamodbclient = new AmazonDynamoDBClient( addbConfig ); }
```

**Azure Cosmos DB**:

Azure Cosmos DB 연결하려면 코드를 다음으로 업데이트합니다.

```csharp
client_documentDB = new CosmosClient("your connectionstring from the Azure portal");
```

**Azure Cosmos DB에서 연결 최적화**

Azure Cosmos DB를 사용하여 다음 옵션을 사용하여 연결을 최적화할 수 있습니다.

* **ConnectionMode** - 직접 연결 모드를 사용하여 Azure Cosmos DB 서비스의 데이터 노드에 연결합니다. 게이트웨이 모드를 사용하여 논리적 주소를 초기화 및 캐시하고 업데이트 시 새로 고칩니다. 자세한 내용은 [연결 모드](sql-sdk-connection-modes.md) 문서를 참조하세요.

* **ApplicationRegion** - 이 옵션은 Azure Cosmos DB와 상호 작용하는 데 기본적으로 사용되는 지역에서 복제된 지역을 설정하는 데 사용됩니다. 자세히 알아보려면 [글로벌 배포](../distribute-data-globally.md) 문서를 참조하세요.

* **ConsistencyLevel** - 이 옵션은 기본 일관성 수준을 재정의하는 데 사용됩니다. 자세히 알아보려면 [일관성 수준](../consistency-levels.md) 문서를 참조하세요.

* **BulkExecutionMode** - 이 옵션은 *AllowBulkExecution* 속성을 true로 설정하여 대량 작업을 실행하는 데 사용됩니다. 자세히 알아보려면 [대량 가져오기](tutorial-sql-api-dotnet-bulk-import.md) 문서를 참조하세요.

   ```csharp
   client_cosmosDB = new CosmosClient(" Your connection string ",new CosmosClientOptions()
   { 
    ConnectionMode=ConnectionMode.Direct,
    ApplicationRegion=Regions.EastUS2,
    ConsistencyLevel=ConsistencyLevel.Session,
    AllowBulkExecution=true  
   });
   ```

### <a name="provision-the-container"></a>컨테이너 프로비저닝

**DynamoDB**:

Amazon DynamoDB에 데이터를 저장하려면 먼저 테이블을 만들어야 합니다. 이 프로세스에서는 다음 코드와 같이 스키마, 키 유형 및 특성을 정의합니다.

```csharp
// movies_key_schema
public static List<KeySchemaElement> movies_key_schema
  = new List<KeySchemaElement>
{
  new KeySchemaElement
  {
    AttributeName = partition_key_name,
    KeyType = "HASH"
  },
  new KeySchemaElement
  {
    AttributeName = sort_key_name,
    KeyType = "RANGE"
  }
};

// key names for the Movies table
public const string partition_key_name = "year";
public const string sort_key_name      = "title";
  public const int readUnits=1, writeUnits=1; 

    // movie_items_attributes
    public static List<AttributeDefinition> movie_items_attributes
  = new List<AttributeDefinition>
{
  new AttributeDefinition
  {
    AttributeName = partition_key_name,
    AttributeType = "N"
  },
  new AttributeDefinition
  {
    AttributeName = sort_key_name,
    AttributeType = "S"
  }

CreateTableRequest  request;
CreateTableResponse response;

// Build the 'CreateTableRequest' structure for the new table
request = new CreateTableRequest
{
  TableName             = table_name,
  AttributeDefinitions  = table_attributes,
  KeySchema             = table_key_schema,
  // Provisioned-throughput settings are always required,
  // although the local test version of DynamoDB ignores them.
  ProvisionedThroughput = new ProvisionedThroughput( readUnits, writeUnits );
};
```

**Azure Cosmos DB**:

Amazon DynamoDB에서 읽기 컴퓨팅 단위 및 쓰기 컴퓨팅 단위를 프로비저닝해야 합니다. Azure Cosmos DB에서는 처리량을 동적으로 작업에 사용할 수 있는 [요청 단위(초당 RU)](../request-units.md)로 지정합니다. 데이터는 데이터베이스 --> 컨테이너 --> 항목으로 구성됩니다. 데이터베이스 수준이나 컬렉션 수준 또는 둘 다에서 처리량을 지정할 수 있습니다.

데이터베이스를 만들려면

```csharp
await client_cosmosDB.CreateDatabaseIfNotExistsAsync(movies_table_name);
```

컨테이너를 만들려면

```csharp
await cosmosDatabase.CreateContainerIfNotExistsAsync(new ContainerProperties() { PartitionKeyPath = "/" + partitionKey, Id = new_collection_name }, provisionedThroughput);
```

### <a name="load-the-data"></a>데이터 로드

**DynamoDB**:

다음 코드에서는 Amazon DynamoDB에서 데이터를 로드하는 방법을 보여 줍니다. moviesArray는 JSON 문서 목록으로 구성되며, JSON 문서를 반복하고 Amazon DynamoDB로 로드해야 합니다.

```csharp
int n = moviesArray.Count;
for( int i = 0, j = 99; i < n; i++ )
    {
  try
  {
    string itemJson = moviesArray[i].ToString();
    Document doc = Document.FromJson(itemJson);
    Task putItem = moviesTable.PutItemAsync(doc);
    if( i >= j )
    {
      j++;
      Console.Write( "{0,5:#,##0}, ", j );
      if( j % 1000 == 0 )
        Console.Write( "\n " );
      j += 99;
    }
    await putItem;
```

**Azure Cosmos DB**:

Azure Cosmos DB에서 `moviesContainer.CreateItemStreamAsync()`를 사용하는 스트리밍 및 쓰기를 선택할 수 있습니다. 그러나 이 샘플에서는 형식 캐스팅 기능을 보여 주기 위해 JSON이 *MovieModel* 형식으로 deserialize됩니다. 이 코드는 다중 스레드되어 Azure Cosmos DB의 분산 아키텍처를 사용하고 로드 속도를 높입니다.

```csharp
List<Task> concurrentTasks = new List<Task>();
for (int i = 0, j = 99; i < n; i++)
{
  try
  {
      MovieModel doc= JsonConvert.DeserializeObject<MovieModel>(moviesArray[i].ToString());
      doc.Id = Guid.NewGuid().ToString();
      concurrentTasks.Add(moviesContainer.CreateItemAsync(doc,new PartitionKey(doc.Year)));
      {
          j++;
          Console.Write("{0,5:#,##0}, ", j);
          if (j % 1000 == 0)
              Console.Write("\n               ");
          j += 99;
      }
      
  }
  catch (Exception ex)
  {
      Console.WriteLine("\n     ERROR: Could not write the movie record #{0:#,##0}, because:\n       {1}",
                          i, ex.Message);
      operationFailed = true;
      break;
  }
}
await Task.WhenAll(concurrentTasks);
```

### <a name="create-a-document"></a>문서 만들기

**DynamoDB**:

Amazon DynamoDB에서 새 문서를 작성하는 것은 형식 안전이 보장되지 않습니다. 다음 예제에서는 newItem를 문서 유형으로 사용합니다.

```csharp
Task<Document> writeNew = moviesTable.PutItemAsync(newItem, token);
await writeNew;
```

**Azure Cosmos DB**:

Azure Cosmos DB는 데이터 모델을 통해 형식 안전성을 제공합니다. 'MovieModel'이라는 데이터 모델을 사용합니다.

```csharp
public class MovieModel
{
    [JsonProperty("id")]
    public string Id { get; set; }
    [JsonProperty("title")]
    public string Title{ get; set; }
    [JsonProperty("year")]
    public int Year { get; set; }
    public MovieModel(string title, int year)
    {
        this.Title = title;
        this.Year = year;
    }
    public MovieModel()
    {

    }
    [JsonProperty("info")]
    public   MovieInfo MovieInfo { get; set; }

    internal string PrintInfo()
    {
        if(this.MovieInfo!=null)
        return            string.Format("\nMovie with title:{1}\n Year: {2}, Actors: {3}\n Directors:{4}\n Rating:{5}\n", this.Id, this.Title, this.Year, String.Join(",",this.MovieInfo.Actors), this.MovieInfo, this.MovieInfo.Rating);
        else
            return string.Format("\nMovie with  title:{0}\n Year: {1}\n",  this.Title, this.Year);
    }
}
```

Azure Cosmos DB에서 newItem은 MovieModel이 됩니다.

```csharp
 MovieModel movieModel = new MovieModel()
            {
                Id = Guid.NewGuid().ToString(),
                Title = "The Big New Movie",
                Year = 2018,
                MovieInfo = new MovieInfo() { Plot = "Nothing happens at all.", Rating = 0 }
            };
    var writeNew= moviesContainer.CreateItemAsync(movieModel, new Microsoft.Azure.Cosmos.PartitionKey(movieModel.Year));
    await writeNew;
```

### <a name="read-a-document"></a>문서 읽기

**DynamoDB**:

Amazon DynamoDB에서 읽으려면 기본 형식을 정의해야 합니다.

```csharp
// Create Primitives for the HASH and RANGE portions of the primary key
Primitive hash = new Primitive(year.ToString(), true);
Primitive range = new Primitive(title, false);

  Task<Document> readMovie = moviesTable.GetItemAsync(hash, range, token);
  movie_record = await readMovie;
```

**Azure Cosmos DB**:

그러나 Azure Cosmos DB를 사용하면 쿼리는 자연어 쿼리입니다(linq).

```csharp
IQueryable<MovieModel> movieQuery = moviesContainer.GetItemLinqQueryable<MovieModel>(true)
                        .Where(f => f.Year == year && f.Title == title);
// The query is executed synchronously here, but can also be executed asynchronously via the IDocumentQuery<T> interface
    foreach (MovieModel movie in movieQuery)
    {
      movie_record_cosmosdb = movie;
    }
```

위의 예제에서 문서 컬렉션은 다음과 같습니다.

- 형식 안전
- 자연어 쿼리 옵션을 제공합니다.

### <a name="update-an-item"></a>항목 업데이트

**DynamoDB**: Amazon DynamoDB에서 항목을 업데이트하려면

```csharp
updateResponse = await client.UpdateItemAsync( updateRequest );
````

**Azure Cosmos DB**:

Azure Cosmos DB에서 업데이트는 Upsert 작업으로 처리됩니다. 즉, 문서가 없는 경우 문서를 삽입합니다.

```csharp
await moviesContainer.UpsertItemAsync<MovieModel>(updatedMovieModel);
```

### <a name="delete-a-document"></a>문서 삭제

**DynamoDB**:

Amazon DynamoDB에서 항목을 삭제하려면 기본 형식을 사용해야 합니다.

```csharp
Primitive hash = new Primitive(year.ToString(), true);
      Primitive range = new Primitive(title, false);
      DeleteItemOperationConfig deleteConfig = new DeleteItemOperationConfig( );
      deleteConfig.ConditionalExpression = condition;
      deleteConfig.ReturnValues = ReturnValues.AllOldAttributes;
      
  Task<Document> delItem = table.DeleteItemAsync( hash, range, deleteConfig );
        deletedItem = await delItem;
```

**Azure Cosmos DB**:

Azure Cosmos DB에서 문서를 가져와 비동기식으로 삭제할 수 있습니다.

```csharp
var result= ReadingMovieItem_async_List_CosmosDB("select * from c where c.info.rating>7 AND c.year=2018 AND c.title='The Big New Movie'");
while (result.HasMoreResults)
{
  var resultModel = await result.ReadNextAsync();
  foreach (var movie in resultModel.ToList<MovieModel>())
  {
    await moviesContainer.DeleteItemAsync<MovieModel>(movie.Id, new PartitionKey(movie.Year));
  }
  }
```

### <a name="query-documents"></a>쿼리 문서

**DynamoDB**:

Amazon DynamoDB에서 데이터를 쿼리하려면 API 함수가 필요합니다.

```csharp
QueryOperationConfig config = new QueryOperationConfig( );
  config.Filter = new QueryFilter( );
  config.Filter.AddCondition( "year", QueryOperator.Equal, new DynamoDBEntry[ ] { 1992 } );
  config.Filter.AddCondition( "title", QueryOperator.Between, new DynamoDBEntry[ ] { "B", "Hzz" } );
  config.AttributesToGet = new List<string> { "year", "title", "info" };
  config.Select = SelectValues.SpecificAttributes;
  search = moviesTable.Query( config ); 
```

**Azure Cosmos DB**:

Azure Cosmos DB에서는 간단한 SQL 쿼리 내에서 프로젝션 및 필터링을 수행할 수 있습니다.

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.Year, c.Title, c.info from c where Year=1998 AND (CONTAINS(Title,'B') OR CONTAINS(Title,'Hzz'))");
```

범위 연산(예: 'between')의 경우 Amazon DynamoDB에서 검색을 수행해야 합니다.

```csharp
ScanRequest sRequest = new ScanRequest
{
  TableName = "Movies",
  ExpressionAttributeNames = new Dictionary<string, string>
  {
    { "#yr", "year" }
  },
  ExpressionAttributeValues = new Dictionary<string, AttributeValue>
  {
      { ":y_a", new AttributeValue { N = "1960" } },
      { ":y_z", new AttributeValue { N = "1969" } },
  },
  FilterExpression = "#yr between :y_a and :y_z",
  ProjectionExpression = "#yr, title, info.actors[0], info.directors, info.running_time_secs"
};

ClientScanning_async( sRequest ).Wait( );
```

Azure Cosmos DB에서 SQL 쿼리와 한 줄로 된 문을 사용할 수 있습니다.

```csharp
var result = moviesContainer.GetItemQueryIterator<MovieModel>( 
  "select c.title, c.info.actors[0], c.info.directors,c.info.running_time_secs from c where BETWEEN year 1960 AND 1969");
```

### <a name="delete-a-container"></a>컨테이너 삭제

**DynamoDB**:

Amazon DynamoDB에서 테이블을 삭제하려면 다음을 지정할 수 있습니다.

```csharp
client.DeleteTableAsync( tableName );
```

**Azure Cosmos DB**:

Azure Cosmos DB에서 컬렉션을 삭제하려면 다음을 지정할 수 있습니다.

```csharp
await moviesContainer.DeleteContainerAsync();
```
그런 다음, 필요에 따라 데이터베이스를 삭제합니다.

```csharp
await cosmosDatabase.DeleteAsync();
```

여기에서 볼 수 있듯이, Azure Cosmos DB는 자연어 쿼리(SQL)를 지원하며, 작업은 비동기식으로 훨씬 더 쉽게 진행됩니다. 마이그레이션 후에는 복잡한 코드를 더 간단한 Azure Cosmos DB로 쉽게 마이그레이션할 수 있습니다.

### <a name="next-steps"></a>다음 단계

- [성능 최적화](performance-tips.md)에 대해 알아보기
- [읽기 및 쓰기 최적화](../key-value-store-cost.md)에 대해 알아보기
- [Cosmos DB의 모니터링](../monitor-cosmos-db.md)에 대해 알아보기

