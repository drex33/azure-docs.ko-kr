---
title: gocql 클라이언트를 통해 Azure Cosmos DB Cassandra API를 사용하여 Go 앱 빌드
description: 이 빠른 시작에서는 Go 클라이언트를 사용하여 Azure Cosmos DB Cassandra API와 상호 작용하는 방법을 보여줍니다.
ms.service: cosmos-db
author: abhirockzz
ms.author: abhishgu
ms.subservice: cosmosdb-cassandra
ms.devlang: go
ms.topic: quickstart
ms.date: 07/14/2020
ms.custom: mode-other
ms.openlocfilehash: fceda1b94ac9975ea53864ac93d642c443b41788
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133056359"
---
# <a name="quickstart-build-a-go-app-with-the-gocql-client-to-manage-azure-cosmos-db-cassandra-api-data"></a>빠른 시작: `gocql` 클라이언트를 통해 Go 앱을 빌드하여 Azure Cosmos DB Cassandra API 데이터 관리
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](manage-data-dotnet.md)
> * [.NET Core](manage-data-dotnet-core.md)
> * [Java v3](manage-data-java.md)
> * [Java v4](manage-data-java-v4-sdk.md)
> * [Node.JS](manage-data-nodejs.md)
> * [Python](manage-data-python.md)
> * [Golang](manage-data-go.md)
>  

Azure Cosmos DB는 글로벌 배포 및 수평적 크기 조정 기능을 사용하여 문서, 테이블, 키 값 및 그래프 데이터베이스를 빠르게 만들고 쿼리할 수 있는 다중 모델 데이터베이스 서비스입니다. 이 빠른 시작에서는 Azure Cosmos DB Cassandra API 계정을 만들어 시작합니다. 그런 다음, Go 애플리케이션을 실행하여 Cassandra 키스페이스, 테이블을 만들고 몇 가지 작업을 실행합니다. 이 Go 앱은 Go 언어용 Cassandra 클라이언트인 [gocql](https://github.com/gocql/gocql)을 사용합니다. 

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정 만들기](https://azure.microsoft.com/free/?WT.mc_id=cassandrago-docs-abhishgu) 또는 Azure 구독 없이 [무료로 Azure Cosmos DB를 사용](https://azure.microsoft.com/try/cosmosdb/?WT.mc_id=cassandrago-docs-abhishgu)할 수 있습니다.
- 컴퓨터에 설치된 [Go](https://golang.org/) 및 Go에 대한 실무 지식.
- [Git](https://git-scm.com/downloads)

## <a name="create-a-database-account"></a>데이터베이스 계정 만들기

데이터베이스를 만들려면 먼저 Azure Cosmos DB로 Cassandra 계정을 만들어야 합니다.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

GitHub에서 애플리케이션을 복제하여 시작합니다.

1. 명령 프롬프트를 열고 `git-samples`라는 새 폴더를 만듭니다.

    ```bash
    md "C:\git-samples"
    ```

2. git bash와 같은 git 터미널 창을 엽니다. `cd` 명령을 사용하여 새 폴더로 변경하고 샘플 앱을 설치합니다.

    ```bash
    cd "C:\git-samples"
    ```

3. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-go-getting-started.git
    ```

## <a name="review-the-code"></a>코드 검토

이 단계는 선택 사항입니다. 코드로 데이터베이스 리소스를 만드는 방법을 알아보려는 경우 다음 코드 조각을 검토할 수 있습니다. 그렇지 않으면 [애플리케이션 실행](#run-the-application)으로 건너뛸 수 있습니다.

`GetSession` 함수(`utils\utils.go`의 일부)는 삽입, 찾기 등의 클러스터 작업을 실행하는 데 사용되는 [`*gocql.Session`](https://godoc.org/github.com/gocql/gocql#Session)을 반환합니다.

```go
func GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword string) *gocql.Session {
    clusterConfig := gocql.NewCluster(cosmosCassandraContactPoint)
    port, err := strconv.Atoi(cosmosCassandraPort)
    
    clusterConfig.Authenticator = gocql.PasswordAuthenticator{Username: cosmosCassandraUser, Password: cosmosCassandraPassword}
    clusterConfig.Port = port
    clusterConfig.SslOpts = &gocql.SslOptions{Config: &tls.Config{MinVersion: tls.VersionTLS12}}
    clusterConfig.ProtoVersion = 4
    
    session, err := clusterConfig.CreateSession()
    ...
    return session
}
```

Azure Cosmos DB Cassandra 호스트는 [`gocql.NewCluster`](https://godoc.org/github.com/gocql/gocql#NewCluster) 함수에 전달되어 [`*gocql.ClusterConfig`](https://godoc.org/github.com/gocql/gocql#ClusterConfig) 구조체를 가져온 다음, 사용자 이름, 암호, 포트 및 적절한 TLS 버전([HTTPS/SSL/TLS 암호화 보안 요구 사항](../database-security.md?WT.mc_id=cassandrago-docs-abhishgu#how-does-azure-cosmos-db-secure-my-database))을 사용하도록 구성됩니다.

그런 다음, `main` 함수(`main.go`)에서 `GetSession` 함수를 호출합니다.

```go
func main() {
    session := utils.GetSession(cosmosCassandraContactPoint, cosmosCassandraPort, cosmosCassandraUser, cosmosCassandraPassword)
    defer session.Close()
    ...
}
```

연결 정보 및 자격 증명은 환경 변수 형식으로 수락됩니다(`init` 메서드에서 확인됨).

```go
func init() {
    cosmosCassandraContactPoint = os.Getenv("COSMOSDB_CASSANDRA_CONTACT_POINT")
    cosmosCassandraPort = os.Getenv("COSMOSDB_CASSANDRA_PORT")
    cosmosCassandraUser = os.Getenv("COSMOSDB_CASSANDRA_USER")
    cosmosCassandraPassword = os.Getenv("COSMOSDB_CASSANDRA_PASSWORD")

    if cosmosCassandraContactPoint == "" || cosmosCassandraUser == "" || cosmosCassandraPassword == "" {
        log.Fatal("missing mandatory environment variables")
    }
}
```

그런 다음, `keyspace` 및 `table` 생성으로 시작하는 Azure Cosmos DB에서 다양한 작업(`operations\setup.go`의 일부)을 실행하는 데 사용됩니다.

이름에서 알 수 있듯이 `DropKeySpaceIfExists` 함수는 존재하는 경우에만 `keyspace`를 삭제합니다.

```go
const dropKeyspace = "DROP KEYSPACE IF EXISTS %s"

func DropKeySpaceIfExists(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(dropKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to drop keyspace", err)
    }
    log.Println("Keyspace dropped")
}
```

`CreateKeySpace` 함수는 `keyspace`(`user_profile`)를 만드는 데 사용됩니다.

```go
const createKeyspace = "CREATE KEYSPACE %s WITH REPLICATION = { 'class' : 'NetworkTopologyStrategy', 'datacenter1' : 1 }"

func CreateKeySpace(keyspace string, session *gocql.Session) {
    err := utils.ExecuteQuery(fmt.Sprintf(createKeyspace, keyspace), session)
    if err != nil {
        log.Fatal("Failed to create keyspace", err)
    }
    log.Println("Keyspace created")
}
```

그러면 `CreateUserTable` 함수를 처리하는 테이블 생성(`user`)이 수행됩니다.

```go
const createTable = "CREATE TABLE %s.%s (user_id int PRIMARY KEY, user_name text, user_bcity text)"

func CreateUserTable(keyspace, table string, session *gocql.Session) {
    err := session.Query(fmt.Sprintf(createTable, keyspace, table)).Exec()
    if err != nil {
        log.Fatal("failed to create table ", err)
    }
    log.Println("Table created")
}
```

키스페이스 및 테이블이 만들어지면 CRUD 작업(`operations\crud.go`의 일부)을 호출합니다. 

`InsertUser`는 `User`를 만드는 데 사용됩니다. [`Bind`](https://godoc.org/github.com/gocql/gocql#Query.Bind)를 사용하여 사용자 정보(ID, 이름 및 도시)를 쿼리 인수로 설정합니다.

```go
const createQuery = "INSERT INTO %s.%s (user_id, user_name , user_bcity) VALUES (?,?,?)"

func InsertUser(keyspace, table string, session *gocql.Session, user model.User) {
    err := session.Query(fmt.Sprintf(createQuery, keyspace, table)).Bind(user.ID, user.Name, user.City).Exec()
    if err != nil {
        log.Fatal("Failed to create user", err)
    }
    log.Println("User created")
}
```

`FindUser`는 특정 사용자 ID를 사용하여 사용자(`model\user.go`)를 검색하는 데 사용하는 반면 [`Scan`](https://godoc.org/github.com/gocql/gocql#Iter.Scan)이 사용자 특성(Cassandra에서 반환)을 개별 변수(`userid`, `name`, `city`)에 바인딩하는 것은 검색 쿼리 결과로 얻은 결과를 사용할 수 있는 방법 중 하나일 뿐입니다.

```go
const selectQuery = "SELECT * FROM %s.%s where user_id = ?"

func FindUser(keyspace, table string, id int, session *gocql.Session) model.User {
    var userid int
    var name, city string
    err := session.Query(fmt.Sprintf(selectQuery, keyspace, table)).Bind(id).Scan(&userid, &name, &city)

    if err != nil {
        if err == gocql.ErrNotFound {
            log.Printf("User with id %v does not exist\n", id)
        } else {
            log.Printf("Failed to find user with id %v - %v\n", id, err)
        }
    }
    return model.User{ID: userid, Name: name, City: city}
}
```

`FindAllUsers`는 모든 사용자를 페치하는 데 사용됩니다. [`SliceMap`](https://godoc.org/github.com/gocql/gocql#Iter.SliceMap)은 `map`의 조각 형식으로 모든 사용자 정보를 가져오는 약어로 사용됩니다. 각 `map`을 키-값 쌍으로 간주합니다. 여기서 열 이름(예: `user_id`)은 해당 값과 함께 키입니다.

```go
const findAllUsersQuery = "SELECT * FROM %s.%s"

func FindAllUsers(keyspace, table string, session *gocql.Session) []model.User {
    var users []model.User
    results, _ := session.Query(fmt.Sprintf(findAllUsersQuery, keyspace, table)).Iter().SliceMap()

    for _, u := range results {
        users = append(users, mapToUser(u))
    }
    return users
}
```

사용자 정보의 각 `map`은 해당 열에서 값을 추출하고 이를 사용하여 `User` 구조체의 인스턴스를 만드는 `mapToUser` 함수를 사용하여 `User`로 변환됩니다.

```go
func mapToUser(m map[string]interface{}) model.User {
    id, _ := m["user_id"].(int)
    name, _ := m["user_name"].(string)
    city, _ := m["user_bcity"].(string)

    return model.User{ID: id, Name: name, City: city}
}
```

## <a name="run-the-application"></a>애플리케이션 실행

앞에서 설명한 것처럼 애플리케이션은 환경 변수 형식으로 연결 및 자격 증명을 허용합니다. 

1. [Azure Portal](https://portal.azure.com/)의 Azure Cosmos DB 계정에서 **연결 문자열** 을 선택합니다. 

    :::image type="content" source="./media/manage-data-go/copy-username-connection-string-azure-portal.png" alt-text="Azure Portal의 연결 문자열 페이지에서 세부 정보 보기 및 복사":::

다음 특성(`CONTACT POINT`, `PORT`, `USERNAME` 및 `PRIMARY PASSWORD`)의 값을 복사하여 해당 환경 변수로 설정합니다.

```shell
set COSMOSDB_CASSANDRA_CONTACT_POINT=<value for "CONTACT POINT">
set COSMOSDB_CASSANDRA_PORT=<value for "PORT">
set COSMOSDB_CASSANDRA_USER=<value for "USERNAME">
set COSMOSDB_CASSANDRA_PASSWORD=<value for "PRIMARY PASSWORD">
```

터미널 창에서 올바른 폴더로 변경합니다. 예를 들면 다음과 같습니다.

```shell
cd "C:\git-samples\azure-cosmosdb-cassandra-go-getting-started"
```

2. 터미널에서 다음 명령을 실행하여 애플리케이션을 시작합니다.

```shell
go run main.go
```

3. 터미널 창에는 키스페이스 및 테이블 설정, 사용자 만들기 등의 다양한 작업에 대한 알림이 표시됩니다.

4. Azure Portal에서 **데이터 탐색기** 를 열어 이 새 데이터를 쿼리/수정/사용합니다. 

    :::image type="content" source="./media/manage-data-go/view-data-explorer-go-app.png" alt-text="데이터 탐색기에서 데이터 보기 - Azure Cosmos DB":::

## <a name="review-slas-in-the-azure-portal"></a>Azure Portal에서 SLA 검토

[!INCLUDE [cosmosdb-tutorial-review-slas](../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [cosmosdb-delete-resource-group](../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Cassandra API를 사용하여 Azure Cosmos DB 계정을 만들고 Cassandra 데이터베이스 및 컨테이너를 만드는 Go 앱을 실행하는 방법을 알아보았습니다. 이제 Azure Cosmos DB 계정으로 추가 데이터를 가져올 수 있습니다. 

> [!div class="nextstepaction"]
> [Azure Cosmos DB로 Cassandra 데이터 가져오기](migrate-data.md)
