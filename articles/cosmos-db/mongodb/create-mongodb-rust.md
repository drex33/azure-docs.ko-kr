---
title: Rust 애플리케이션을 Azure Cosmos DB의 API for MongoDB에 연결
description: 이 빠른 시작에서는 Azure Cosmos DB의 API for MongoDB에서 지원되는 Rust 애플리케이션을 빌드하는 방법을 보여줍니다.
author: abhirockzz
ms.author: abhishgu
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: rust
ms.topic: quickstart
ms.date: 08/26/2021
ms.openlocfilehash: 4831c5f695baf923b5e687c3e3c0649262a9e361
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130233002"
---
# <a name="quickstart-connect-a-rust-application-to-azure-cosmos-dbs-api-for-mongodb"></a>빠른 시작: Rust 애플리케이션을 Azure Cosmos DB의 API for MongoDB에 연결
[!INCLUDE[appliesto-mongodb-api](../includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Python](create-mongodb-python.md)
> * [Java](create-mongodb-java.md)
> * [Node.JS](create-mongodb-nodejs.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
> * [Rust](create-mongodb-rust.md)
>

Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다. 이 문서에 제시된 샘플은 [MongoDB용 Rust 드라이버](https://github.com/mongodb/mongo-rust-driver)를 사용하는 간단한 명령줄 기반 애플리케이션입니다. Azure Cosmos DB의 API for MongoDB는 [MongoDB 유선 프로토콜과 호환](./mongodb-introduction.md)되므로 모든 MongoDB 클라이언트 드라이버에 연결할 수 있습니다.

샘플 코드에 구현된 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 탐색하여 MongoDB Rust 드라이버를 사용하여 Azure Cosmos DB의 API for MongoDB와 상호 작용하는 방법을 알아봅니다. 마지막으로 애플리케이션을 로컬에서 실행하여 작동 상태를 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/)할 수 있습니다. 연결 문자열 `.mongodb://localhost:C2y6yDjf5/R+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw/Jw==@localhost:10255/admin?ssl=true`와 함께 [Azure Cosmos DB 에뮬레이터](https://aka.ms/cosmosdb-emulator)를 사용할 수도 있습니다.
- [Rust](https://www.rust-lang.org/tools/install)(버전 1.39 이상)
- [Git](https://git-scm.com/downloads)

## <a name="set-up-azure-cosmos-db"></a>Azure Cosmos DB 설정

Azure Cosmos DB 계정을 설정하려면 [여기의 지침](create-mongodb-dotnet.md)을 따르세요. 애플리케이션에는 Azure Portal을 사용하여 가져올 수 있는 MongoDB 연결 문자열이 필요합니다. 자세한 내용은 [사용자 지정할 MongoDB 연결 문자열 가져오기](connect-mongodb-account.md#get-the-mongodb-connection-string-to-customize)를 참조하세요.

## <a name="run-the-application"></a>애플리케이션 실행

### <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

1. 명령 프롬프트를 열고 `git-samples`라는 새 폴더를 만든 다음, 명령 프롬프트를 닫습니다.

    ```bash
    mkdir "C:\git-samples"
    ```

1. Git Bash와 같은 Git 터미널 창을 열고, `cd` 명령을 사용하여 샘플 앱을 설치할 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다. 

    ```bash
    git clone https://github.com/Azure-Samples/cosmosdb-rust-mongodb-quickstart
    ```

### <a name="build-the-application"></a>애플리케이션 빌드

이진 파일을 빌드하려면 다음을 수행합니다.

```bash
cargo build --release
```

### <a name="configure-the-application"></a>애플리케이션 구성 

연결 문자열, MongoDB 데이터베이스 및 컬렉션 이름을 환경 변수로 내보냅니다. 

```bash
export MONGODB_URL="mongodb://<COSMOSDB_ACCOUNT_NAME>:<COSMOSDB_PASSWORD>@<COSMOSDB_ACCOUNT_NAME>.mongo.cosmos.azure.com:10255/?ssl=true&replicaSet=globaldb&maxIdleTimeMS=120000&appName=@<COSMOSDB_ACCOUNT_NAME>@"
```

> [!NOTE] 
> Cosmos DB 요구 사항으로 인해 `ssl=true` 옵션이 중요합니다. 자세한 내용은 [연결 문자열 요구 사항](connect-mongodb-account.md#connection-string-requirements)을 참조하세요.
>

`MONGODB_URL` 환경 변수의 경우 `<COSMOSDB_ACCOUNT_NAME>` 및 `<COSMOSDB_PASSWORD>` 자리 표시자를 바꿉니다.

- `<COSMOSDB_ACCOUNT_NAME>`: 앞에서 만든 Azure Cosmos DB 계정의 이름
- `<COSMOSDB_PASSWORD>`: 이전 단계에서 추출한 데이터베이스 키

```bash
export MONGODB_DATABASE=todos_db
export MONGODB_COLLECTION=todos
```

`MONGODB_DATABASE` 및 `MONGODB_COLLECTION`에 원하는 값을 선택해도 되고 그대로 두어도 됩니다.

애플리케이션을 실행하려면 올바른 폴더(애플리케이션 이진 파일이 있는 위치)로 변경합니다.

```bash
cd target/release
```

`todo` 만들기

```bash
./todo create "Create an Azure Cosmos DB database account"
```

성공하면 다음과 같이 새로 만든 문서의 MongoDB `_id`가 포함된 출력이 표시됩니다.

```bash
inserted todo with id = ObjectId("5ffd1ca3004cc935004a0959")
```

또 다른 `todo` 만들기

```bash
./todo create "Get the MongoDB connection string using the Azure CLI"
```

모든 `todo` 나열

```bash
./todo list all
```

방금 추가한 항목이 표시됩니다.

```bash
todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: pending
todo_id: 5ffd1cbe003bcec40022c81c | description: Get the MongoDB connection string using the Azure CLI | status: pending
```

`todo` 상태를 업데이트하려면(예: `completed` 상태로 변경) 다음과 같이 `todo` ID를 사용합니다.

```bash
./todo update 5ffd1ca3004cc935004a0959 completed

#output
updating todo_id 5ffd1ca3004cc935004a0959 status to completed
updated status for todo id 5ffd1ca3004cc935004a0959
```

완료된 `todo`만 나열

```bash
./todo list completed
```

방금 업데이트한 항목이 표시됩니다.

```bash
listing 'completed' todos

todo_id: 5ffd1ca3004cc935004a0959 | description: Create an Azure Cosmos DB database account | status: completed
```

ID를 사용하여 `todo` 삭제

```bash
./todo delete 5ffd1ca3004cc935004a0959
```

확인할 `todo` 나열

```bash
./todo list all
```

방금 삭제한 `todo`가 있으면 안 됩니다.

### <a name="view-data-in-data-explorer"></a>데이터 탐색기에서 데이터 보기

Azure Cosmos DB에 저장된 데이터는 Azure Portal에서 살펴보고 쿼리할 수 있습니다.

이전 단계에서 만든 사용자 데이터를 보고 쿼리하고 사용하려면 웹 브라우저에서 [Azure Portal](https://portal.azure.com)에 로그인합니다.

맨 위 검색 상자에 **Azure Cosmos DB** 를 입력합니다. Cosmos 계정 블레이드가 열리면 Cosmos 계정을 선택합니다. 왼쪽 탐색에서 **데이터 탐색기** 를 선택합니다. 컬렉션 창에서 컬렉션을 확장하면 컬렉션에서 문서를 보고, 데이터를 쿼리하고 저장된 프로시저, 트리거 및 UDF를 만들고 실행합니다.

## <a name="review-the-code-optional"></a>코드 검토(선택 사항)

애플리케이션의 작동 방식을 알고 싶으면 이 섹션의 코드 조각을 검토할 수 있습니다. 다음 코드 조각은 모두 `src/main.rs` 파일에서 가져옵니다.

`main` 함수는 `todo` 애플리케이션의 진입점입니다. Azure Cosmos DB의 API for MongoDB에 대한 연결 URL은 `MONGODB_URL` 환경 변수에 의해 제공될 것으로 예상됩니다. `TodoManager`의 새 인스턴스가 생성된 후 사용자가 선택한 작업(`create`, `update`, `list` 또는 `delete`)에 따라 적절한 `TodoManager` 메서드를 위임하는 [`match` 식](https://doc.rust-lang.org/book/ch06-02-match.html)이 생성됩니다.

```rust
fn main() {
    let conn_string = std::env::var_os("MONGODB_URL").expect("missing environment variable MONGODB_URL").to_str().expect("failed to get MONGODB_URL").to_owned();
    let todos_db_name = std::env::var_os("MONGODB_DATABASE").expect("missing environment variable MONGODB_DATABASE").to_str().expect("failed to get MONGODB_DATABASE").to_owned();
    let todos_collection_name = std::env::var_os("MONGODB_COLLECTION").expect("missing environment variable MONGODB_COLLECTION").to_str().expect("failed to get MONGODB_COLLECTION").to_owned();

    let tm = TodoManager::new(conn_string,todos_db_name.as_str(), todos_collection_name.as_str());
      
    let ops: Vec<String> = std::env::args().collect();
    let op = ops[1].as_str();

    match op {
        CREATE_OPERATION_NAME => tm.add_todo(ops[2].as_str()),
        LIST_OPERATION_NAME => tm.list_todos(ops[2].as_str()),
        UPDATE_OPERATION_NAME => tm.update_todo_status(ops[2].as_str(), ops[3].as_str()),
        DELETE_OPERATION_NAME => tm.delete_todo(ops[2].as_str()),
        _ => panic!(INVALID_OP_ERR_MSG)
    }
}
```

`TodoManager`는 [mongodb::sync::Collection](https://docs.rs/mongodb/1.1.1/mongodb/sync/struct.Collection.html)을 캡슐화하는 `struct`입니다. `new` 함수를 사용하여 `TodoManager`를 인스턴스화하려면 Azure Cosmos DB의 API for MongoDB에 대한 연결이 시작됩니다.

```rust
struct TodoManager {
    coll: Collection
}
....
impl TodoManager{
    fn new(conn_string: String, db_name: &str, coll_name: &str) -> Self{
        let mongo_client = Client::with_uri_str(&*conn_string).expect("failed to create client");
        let todo_coll = mongo_client.database(db_name).collection(coll_name);
            
        TodoManager{coll: todo_coll}
    }
....
```

가장 중요한 것은 `TodoManager`에 `todo`를 관리하는 데 도움이 되는 메서드가 있습니다. 하나씩 살펴보겠습니다.

`add_todo` 메서드는 사용자가 제공한 `todo` 설명을 가져와서 아래와 같이 `Todo` 구조체의 인스턴스를 만듭니다. [serde](https://github.com/serde-rs/serde) 프레임워크는 BSON 데이터를 `Todo` 구조체의 인스턴스로 매핑(직렬화/역직렬화)하는 데 사용됩니다. 직렬화/역직렬화 프로세스를 사용자 지정하기 위해 `serde` 필드 속성이 어떻게 사용되는지 확인합니다. 예를 들어, Todo `struct`의 `todo_id` 필드는 `ObjectId`이고 MongoDB에 `_id`로 저장됩니다.

```rust
#[derive(Serialize, Deserialize)]
struct Todo {
    #[serde(rename = "_id", skip_serializing_if = "Option::is_none")]
    todo_id: Option<bson::oid::ObjectId>,
    #[serde(rename = "description")]
    desc: String,
    status: String,
}
```

[Collection.insert_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.insert_one)은 추가할 `todo` 세부 정보를 나타내는 [문서](https://docs.rs/bson/1.1.0/bson/document/struct.Document.html)를 수락합니다. `Todo`에서 `Document`로의 변환은 [to_bson](https://docs.rs/bson/1.1.0/bson/ser/fn.to_bson.html) 및 [as_document](https://docs.rs/bson/1.1.0/bson/enum.Bson.html#method.as_document)의 조합을 사용하여 수행되는 2단계 프로세스입니다.

```rust
fn add_todo(self, desc: &str) {
    let new_todo = Todo {
        todo_id: None,
        desc: String::from(desc),
        status: String::from(TODO_PENDING_STATUS),
    };
    
    let todo_doc = mongodb::bson::to_bson(&new_todo).expect("struct to BSON conversion failed").as_document().expect("BSON to Document conversion failed").to_owned();
        
    let r = self.coll.insert_one(todo_doc, None).expect("failed to add todo");    
    println!("inserted todo with id = {}", r.inserted_id);
}
```

[Collection.find](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.find)는 `todo`를 *모두* 검색하거나 사용자가 제공한 상태(`pending` 또는 `completed`)에 따라 필터링하는 데 사용됩니다. `while` 루프에서 검색 결과로 얻은 각 `Document`가 [bson::from_bson](https://docs.rs/bson/1.1.0/bson/de/fn.from_bson.html)을 사용하여 `Todo` 구조체로 변환되는 방법에 유의하세요. 이는 `add_todo` 메서드에서 수행된 것과는 반대입니다.

```rust
fn list_todos(self, status_filter: &str) {
    let mut filter = doc!{};
    if status_filter == TODO_PENDING_STATUS ||  status_filter == TODO_COMPLETED_STATUS{
        println!("listing '{}' todos",status_filter);
        filter = doc!{"status": status_filter}
    } else if status_filter != "all" {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    let mut todos = self.coll.find(filter, None).expect("failed to find todos");
    
    while let Some(result) = todos.next() {
        let todo_doc = result.expect("todo not present");
        let todo: Todo = bson::from_bson(Bson::Document(todo_doc)).expect("BSON to struct conversion failed");
        println!("todo_id: {} | description: {} | status: {}", todo.todo_id.expect("todo id missing"), todo.desc, todo.status);
    }
}
```

`todo` 상태를 업데이트할 수 있습니다(`pending`에서 `completed`로 또는 그 반대로). `todo`는 [bson::oid::ObjectId](https://docs.rs/bson/1.1.0/bson/oid/struct.ObjectId.html)로 변환된 후 [Collection.update_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.update_one) 메서드에서 업데이트해야 하는 문서를 찾는 데 사용됩니다.

```rust
fn update_todo_status(self, todo_id: &str, status: &str) {

    if status != TODO_COMPLETED_STATUS && status != TODO_PENDING_STATUS {
        panic!(INVALID_FILTER_ERR_MSG)
    }

    println!("updating todo_id {} status to {}", todo_id, status);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    let r = self.coll.update_one(id_filter, doc! {"$set": { "status": status }}, None).expect("update failed");
    if r.modified_count == 1 {
        println!("updated status for todo id {}",todo_id);
    } else if r.matched_count == 0 {
        println!("could not update. check todo id {}",todo_id);
    }
}
```

[Collection.delete_one](https://docs.rs/mongodb/1.1.1/mongodb/struct.Collection.html#method.delete_one) 메서드를 사용하면 `todo`를 삭제하는 것이 간단합니다.


```rust
fn delete_todo(self, todo_id: &str) {
    println!("deleting todo {}", todo_id);
    
    let id_filter = doc! {"_id": bson::oid::ObjectId::with_string(todo_id).expect("todo_id is not valid ObjectID")};

    self.coll.delete_one(id_filter, None).expect("delete failed").deleted_count;
}
``` 

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cloud Shell을 사용하여 Azure Cosmos DB MongoDB API 계정을 만들고, Rust 명령줄 앱을 만들고 실행하여 `todo`를 관리하는 방법을 알아보았습니다. 이제 사용자의 Azure Cosmos DB 계정에 추가 데이터를 가져올 수 있습니다.

Azure Cosmos DB로 마이그레이션하기 위한 용량 계획을 수행하려고 하나요? 용량 계획을 위해 기존 데이터베이스 클러스터에 대한 정보를 사용할 수 있습니다.
* 기존 데이터베이스 클러스터의 vCore 및 서버 수만 알고 있는 경우 [vCore 또는 vCPU를 사용하여 요청 단위 예측](../convert-vcore-to-request-unit.md)에 대해 읽어보세요. 
* 현재 데이터베이스 워크로드에 대한 일반적인 요청 비율을 알고 있는 경우 [Azure Cosmos DB 용량 계획 도구를 사용하여 요청 단위 예측](estimate-ru-capacity-planner.md)에 대해 읽어보세요.

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 MongoDB 데이터 가져오기](../../dms/tutorial-mongodb-cosmos-db.md?toc=%2fazure%2fcosmos-db%2ftoc.json%253ftoc%253d%2fazure%2fcosmos-db%2ftoc.json)
