---
title: Go 애플리케이션을 Azure Cosmos DB의 API for MongoDB에 연결
description: 이 빠른 시작에서는 기존 Go 애플리케이션을 Azure Cosmos DB의 API for MongoDB에 연결하는 방법을 보여줍니다.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: go
ms.topic: quickstart
ms.date: 08/26/2021
ms.custom: mode-other
ms.openlocfilehash: 0f224b51a171d1d78f61a23129610dd4d0715926
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133042077"
---
# <a name="quickstart-connect-a-go-application-to-azure-cosmos-dbs-api-for-mongodb"></a>빠른 시작: Go 애플리케이션을 Azure Cosmos DB의 API for MongoDB에 연결
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다. 이 빠른 시작에서는 Azure Cloud Shell를 사용하여 Azure Cosmos DB 계정을 생성 및 관리하고, GitHub에서 기존 샘플 애플리케이션을 복제하고, Azure Cosmos DB에서 작동하도록 구성합니다. 

샘플 애플리케이션은 Go로 작성된 명령줄 기반의 `todo` 관리 도구입니다. Azure Cosmos DB의 API for MongoDB는 [MongoDB 유선 프로토콜과 호환](./mongodb-introduction.md)되므로 모든 MongoDB 클라이언트 드라이버에 연결할 수 있습니다. 이 애플리케이션은 애플리케이션에 투명한 방식으로 [MongoDB용 Go 드라이버](https://github.com/mongodb/mongo-go-driver)를 사용하기 때문에 데이터가 Azure Cosmos DB 데이터베이스에 저장됩니다.

## <a name="prerequisites"></a>사전 요구 사항
- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. 연결 문자열 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`와 함께 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.
- 컴퓨터에 설치된 [Go](https://golang.org/) 및 Go에 대한 실무 지식.
- [Git](https://git-scm.com/downloads)
[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

1. 명령 프롬프트를 열고 `git-samples`라는 새 폴더를 만든 다음, 명령 프롬프트를 닫습니다.

    ```bash
    mkdir "C:\git-samples"
    ```

2. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-go-mongodb-quickstart
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 애플리케이션의 작동 방식에 관심이 있으면 다음 코드 조각을 살펴보세요. 그렇지 않다면 [애플리케이션 실행](#run-the-application)으로 건너뛰어도 됩니다. 애플리케이션 레이아웃은 다음과 같습니다.

```bash
.
├── go.mod
├── go.sum
└── todo.go
```

다음 코드 조각은 모두 `todo.go` 파일에서 가져옵니다.

### <a name="connecting-the-go-app-to-azure-cosmos-db"></a>Go 앱을 Azure Cosmos DB에 연결

[`clientOptions`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions)는 환경 변수를 사용하여 전달되는 Azure Cosmos DB의 연결 문자열을 캡슐화합니다(자세한 내용은 새로 추가될 섹션에서 설명). `clientOptions` 인스턴스가 전달되는 [`mongo.NewClient`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#NewClient)를 사용하여 연결이 초기화됩니다. 성공적으로 연결되었는지 확인하기 위해 [`Ping` 함수](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Client.Ping)가 호출됩니다(페일 패스트(Fail-fast) 전략).

```go
    ctx, cancel := context.WithTimeout(context.Background(), time.Second*10)
    defer cancel()

    clientOptions := options.Client().ApplyURI(mongoDBConnectionString).SetDirect(true)
    
    c, err := mongo.NewClient(clientOptions)
    err = c.Connect(ctx)
    if err != nil {
        log.Fatalf("unable to initialize connection %v", err)
    }

    err = c.Ping(ctx, nil)
    if err != nil {
        log.Fatalf("unable to connect %v", err)
    }
```

> [!NOTE] 
> [`SetDirect(true)`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo/options?tab=doc#ClientOptions.SetDirect) 구성을 사용하는 것이 중요합니다. 이 구성을 사용하지 않으면 `unable to connect connection(cdb-ms-prod-<azure-region>-cm1.documents.azure.com:10255[-4]) connection is closed` 연결 오류가 발생합니다.
>

### <a name="create-a-todo-item"></a>`todo` 항목 만들기

`todo`를 만들기 위해 [`mongo.Collection`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection)에 대한 핸들을 가져오고 [`InsertOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.InsertOne)함수를 호출합니다. 

```go
func create(desc string) {
    c := connect()
    ctx := context.Background()
    defer c.Disconnect(ctx)

    todoCollection := c.Database(database).Collection(collection)
    r, err := todoCollection.InsertOne(ctx, Todo{Description: desc, Status: statusPending})
    if err != nil {
        log.Fatalf("failed to add todo %v", err)
    }
```

설명과 상태(처음에는 `pending`으로 설정됨)가 포함된 `Todo` 구조체를 전달합니다.

```go
type Todo struct {
    ID          primitive.ObjectID `bson:"_id,omitempty"`
    Description string             `bson:"description"`
    Status      string             `bson:"status"`
}
```
### <a name="list-todo-items"></a>`todo` 항목 나열

조건에 따라 TODO를 나열할 수 있습니다. 필터 조건을 캡슐화하기 위해 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D)가 생성됩니다.

```go
func list(status string) {
    .....
    var filter interface{}
    switch status {
    case listAllCriteria:
        filter = bson.D{}
    case statusCompleted:
        filter = bson.D{{statusAttribute, statusCompleted}}
    case statusPending:
        filter = bson.D{{statusAttribute, statusPending}}
    default:
        log.Fatal("invalid criteria for listing todo(s)")
    }
```

[`Find`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.Find)는 필터를 기반으로 문서를 검색하는 데 사용되며 결과는 `Todo` 조각으로 변환됩니다.

```go
    todoCollection := c.Database(database).Collection(collection)
    rs, err := todoCollection.Find(ctx, filter)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
    var todos []Todo
    err = rs.All(ctx, &todos)
    if err != nil {
        log.Fatalf("failed to list todo(s) %v", err)
    }
```

마지막으로 정보가 테이블 형식으로 렌더링됩니다.

```go
    todoTable := [][]string{}

    for _, todo := range todos {
        s, _ := todo.ID.MarshalJSON()
        todoTable = append(todoTable, []string{string(s), todo.Description, todo.Status})
    }

    table := tablewriter.NewWriter(os.Stdout)
    table.SetHeader([]string{"ID", "Description", "Status"})

    for _, v := range todoTable {
        table.Append(v)
    }
    table.Render()
```

### <a name="update-a-todo-item"></a>`todo` 항목 업데이트

`_id`에 따라 `todo`를 업데이트할 수 있습니다. `_id`를 기반으로 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 필터가 생성되고, 업데이트된 정보에 대한 다른 필터가 하나 더 생성되는데, 여기서는 새로운 상태(`completed` 또는 `pending`)입니다. 마지막으로 필터와 업데이트된 문서를 사용하여 [`UpdateOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.UpdateOne) 함수가 호출됩니다.

```go
func update(todoid, newStatus string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
    filter := bson.D{{"_id", oid}}
    update := bson.D{{"$set", bson.D{{statusAttribute, newStatus}}}}
    _, err = todoCollection.UpdateOne(ctx, filter, update)
    if err != nil {
        log.Fatalf("failed to update todo %v", err)
    }
```

### <a name="delete-a-todo"></a>`todo` 삭제

`todo`는 `_id`에 따라 삭제되고 [`bson.D`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/bson?tab=doc#D) 인스턴스의 형태로 캡슐화됩니다. 문서를 삭제하기 위해 [`DeleteOne`](https://pkg.go.dev/go.mongodb.org/mongo-driver@v1.3.2/mongo?tab=doc#Collection.DeleteOne)이 호출됩니다.

```go
func delete(todoid string) {
....
    todoCollection := c.Database(database).Collection(collection)
    oid, err := primitive.ObjectIDFromHex(todoid)
    if err != nil {
        log.Fatalf("invalid todo ID %v", err)
    }
    filter := bson.D{{"_id", oid}}
    _, err = todoCollection.DeleteOne(ctx, filter)
    if err != nil {
        log.Fatalf("failed to delete todo %v", err)
    }
}
```

## <a name="build-the-application"></a>애플리케이션 빌드

애플리케이션을 복제하고 빌드한(`go build`를 사용하여) 디렉터리로 변경합니다.

```bash
cd monogdb-go-quickstart
go build -o todo
```

애플리케이션이 올바르게 빌드되었는지 확인합니다.

```bash
./todo --help
```

## <a name="setup-azure-cosmos-db"></a>Azure Cosmos DB 설정

### <a name="sign-in-to-azure"></a>Azure에 로그인

CLI를 로컬로 설치하여 사용하도록 선택한 경우 이 항목에서 Azure CLI 버전 2.0 이상을 실행해야 합니다. `az --version`을 실행하여 버전을 찾습니다. 설치 또는 업그레이드해야 하는 경우 [Azure CLI 설치]를 참조하세요. 

설치된 Azure CLI를 사용하는 경우 [az login](/cli/azure/reference-index#az_login) 명령을 사용하여 Azure 구독에 로그인하고 화면의 지시를 따릅니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

```azurecli
az login 
``` 
   
### <a name="add-the-azure-cosmos-db-module"></a>Azure Cosmos DB 모듈 추가

설치된 Azure CLI를 사용하는 경우 `az` 명령을 실행하여 `cosmosdb` 구성 요소가 이미 설치되어 있는지 확인합니다. 기본 명령 목록에 `cosmosdb`가 있으면 다음 명령으로 진행합니다. Azure Cloud Shell을 사용하는 경우 이 단계를 건너뛸 수 있습니다.

`cosmosdb`가 기본 명령 목록에 없으면 [Azure CLI](/cli/azure/install-azure-cli)를 다시 설치합니다.

### <a name="create-a-resource-group"></a>리소스 그룹 만들기

[az group create](/cli/azure/group#az_group_create)를 사용하여 [리소스 그룹](../../azure-resource-manager/management/overview.md)을 만듭니다. Azure 리소스 그룹은 웹앱, 데이터베이스, 스토리지 계정이 관리되었는지 등 Azure 리소스가 배포 및 관리되는 논리적 컨테이너입니다. 

다음 예제에서는 서유럽 지역의 리소스 그룹을 만듭니다. 리소스 그룹에 고유한 이름을 선택합니다.

Azure Cloud Shell을 사용하는 경우 **시도** 를 선택하고, 화면의 지시에 따라 로그인한 다음, 명령 프롬프트에 명령을 복사합니다.

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

### <a name="create-an-azure-cosmos-db-account"></a>Azure Cosmos DB 계정 만들기

[az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) 명령을 사용하여 Cosmos 계정을 만듭니다.

다음 명령에서 `<cosmosdb-name>` 자리 표시자를 표시하는 고유한 Cosmos 계정 이름을 바꿉니다. 이 고유한 이름은 Cosmos DB 엔드포인트(`https://<cosmosdb-name>.documents.azure.com/`)의 일부로 사용되므로, Azure의 모든 Cosmos 계정에서 고유해야 합니다. 

```azurecli-interactive
az cosmosdb create --name <cosmosdb-name> --resource-group myResourceGroup --kind MongoDB
```

`--kind MongoDB` 매개 변수는 MongoDB 클라이언트 연결을 사용하도록 설정합니다.

Azure Cosmos DB 계정을 만든 경우 Azure CLI는 다음 예와 비슷한 정보를 표시합니다. 

> [!NOTE]
> 이 예서는 Azure CLI 출력 형식으로 JSON을 사용합니다(기본값). 다른 출력 형식을 사용하려면 [Azure CLI 명령에 대한 출력 형식](/cli/azure/format-output-azure-cli)을 참조하세요. 

```json
{
  "databaseAccountOfferType": "Standard",
  "documentEndpoint": "https://<cosmosdb-name>.documents.azure.com:443/",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Document
DB/databaseAccounts/<cosmosdb-name>",
  "kind": "MongoDB",
  "location": "West Europe",
  "name": "<cosmosdb-name>",
  "readLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ],
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "writeLocations": [
    {
      "documentEndpoint": "https://<cosmosdb-name>-westeurope.documents.azure.com:443/",
      "failoverPriority": 0,
      "id": "<cosmosdb-name>-westeurope",
      "locationName": "West Europe",
      "provisioningState": "Succeeded"
    }
  ]
} 
```

### <a name="retrieve-the-database-key"></a>데이터베이스 키 검색

Cosmos 데이터베이스에 연결하려면 데이터베이스 키가 필요합니다. [az cosmosdb keys list](/cli/azure/cosmosdb/keys#az_cosmosdb_keys_list) 명령을 사용하여 기본 키를 검색합니다.

```azurecli-interactive
az cosmosdb keys list --name <cosmosdb-name> --resource-group myResourceGroup --query "primaryMasterKey"
```

Azure CLI는 다음 예와 유사한 정보를 출력합니다. 

```json
"RUayjYjixJDWG5xTqIiXjC..."
```

## <a name="configure-the-application"></a>애플리케이션 구성 

<a name="devconfig"></a>
### <a name="export-the-connection-string-mongodb-database-and-collection-names-as-environment-variables"></a>연결 문자열, MongoDB 데이터베이스 및 컬렉션 이름을 환경 변수로 내보냅니다. 

```bash
export MONGODB_CONNECTION_STRING="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Cosmos DB 요구 사항으로 인해 `ssl=true` 옵션이 중요합니다. 자세한 내용은 [연결 문자열 요구 사항](connect-mongodb-account.md#connection-string-requirements)을 참조하세요.
>

`MONGODB_CONNECTION_STRING` 환경 변수의 경우 `<COSMOSDB_ACCOUNT_NAME>` 및 `<COSMOSDB_PASSWORD>` 자리 표시자를 바꿉니다.

1. `<COSMOSDB_ACCOUNT_NAME>`: 앞에서 만든 Azure Cosmos DB 계정의 이름
2. `<COSMOSDB_PASSWORD>`: 이전 단계에서 추출한 데이터베이스 키

```bash
export MONGODB_DATABASE=todo-db
export MONGODB_COLLECTION=todos
```

`MONGODB_DATABASE` 및 `MONGODB_COLLECTION`에 원하는 값을 선택해도 되고 그대로 두어도 됩니다.

## <a name="run-the-application"></a>애플리케이션 실행

`todo` 만들기

```bash
./todo --create "Create an Azure Cosmos DB database account"
```

성공하면 다음과 같이 새로 만든 문서의 MongoDB `_id`가 포함된 출력이 표시됩니다.

```bash
added todo ObjectID("5e9fd6befd2f076d1f03bd8a")
```

또 다른 `todo` 만들기

```bash
./todo --create "Get the MongoDB connection string using the Azure CLI"
```

모든 `todo` 나열

```bash
./todo --list all
```

방금 테이블 형식으로 추가한 항목이 표시됩니다.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | pending   |
|                            | database account               |           |
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

`todo` 상태를 업데이트하려면(예: `completed` 상태로 변경) `todo` ID를 사용합니다.

```bash
./todo --update 5e9fd6b1bcd2fa6bd267d4c4,completed
```

완료된 `todo`만 나열

```bash
./todo --list completed
```

방금 업데이트한 항목이 표시됩니다.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6b1bcd2fa6bd267d4c4" | Create an Azure Cosmos DB      | completed |
|                            | database account               |           |
+----------------------------+--------------------------------+-----------+
```

### <a name="view-data-in-data-explorer"></a>데이터 탐색기에서 데이터 보기

Azure Cosmos DB에 저장된 데이터는 Azure Portal에서 살펴보고 쿼리할 수 있습니다.

이전 단계에서 만든 사용자 데이터를 보고 쿼리하고 사용하려면 웹 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

맨 위 검색 상자에 **Azure Cosmos DB** 를 입력합니다. Cosmos 계정 블레이드가 열리면 Cosmos 계정을 선택합니다. 왼쪽 탐색에서 **데이터 탐색기** 를 선택합니다. 컬렉션 창에서 컬렉션을 확장하면 컬렉션에서 문서를 보고, 데이터를 쿼리하고 저장된 프로시저, 트리거 및 UDF를 만들고 실행합니다. 

:::image type="content" source="./media/create-mongodb-go/go-cosmos-db-data-explorer.png" alt-text="새로 만든 문서를 표시하는 데이터 탐색기":::


ID를 사용하여 `todo` 삭제

```bash
./todo --delete 5e9fd6b1bcd2fa6bd267d4c4,completed
```

확인할 `todo` 나열

```bash
./todo --list all
```

방금 삭제한 `todo`가 표시되지 않아야 합니다.

```bash
+----------------------------+--------------------------------+-----------+
|             ID             |          DESCRIPTION           |  STATUS   |
+----------------------------+--------------------------------+-----------+
| "5e9fd6befd2f076d1f03bd8a" | Get the MongoDB connection     | pending   |
|                            | string using the Azure CLI     |           |
+----------------------------+--------------------------------+-----------+
```

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cloud Shell을 사용하여 Azure Cosmos DB MongoDB API 계정을 만들고, Go 명령줄 앱을 만들고 실행하여 `todo`를 관리하는 방법을 알아보았습니다. 이제 사용자의 Azure Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다.

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
