---
title: '빠른 시작: Rust를 사용하여 Azure Database for PostgreSQL - 단일 서버에 연결'
description: 이 빠른 시작에서는 Azure Database for PostgreSQL - 단일 서버의 데이터를 연결하고 쿼리하는 데 사용할 수 있는 Rust 코드 샘플을 제공합니다.
author: abhirockzz
ms.author: abhishgu
ms.service: postgresql
ms.devlang: rust
ms.topic: quickstart
ms.date: 03/26/2021
ms.custom: mode-other
ms.openlocfilehash: 26eb883ef3f815a6c5331c14ccfeaca6f8131ea1
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133066169"
---
# <a name="quickstart-use-rust-to-connect-and-query-data-in-azure-database-for-postgresql---single-server"></a>빠른 시작: Rust를 사용하여 Azure Database for PostgreSQL - 단일 서버에서 데이터 연결 및 쿼리

이 문서에서는 샘플 코드에 구현된 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 탐색하여 [Rust용 PostgreSQL 드라이버](https://github.com/sfackler/rust-postgres)를 사용하여 Azure Database for PostgreSQL과 상호 작용하는 방법을 알아봅니다. 마지막으로 애플리케이션을 로컬에서 실행하여 작동 상태를 볼 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 빠른 시작에는 다음이 필요합니다.

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free).
- 최신 버전의 [Rust](https://www.rust-lang.org/tools/install)가 설치되어 있습니다.
- Azure Database for PostgreSQL 단일 서버 - [Azure Portal](./quickstart-create-server-database-portal.md) <br/> 또는 [Azure CLI](./quickstart-create-server-database-azure-cli.md)를 사용하여 만듭니다.
- 퍼블릭 또는 프라이빗 액세스를 사용하는지 여부에 따라 아래 작업 중 **하나** 를 완료하여 연결을 활성화합니다.

  |작업| 연결 방법|방법 가이드|
  |:--------- |:--------- |:--------- |
  | **방화벽 규칙 구성** | 공용 | [포털](./howto-manage-firewall-using-portal.md) <br/> [CLI](./howto-manage-firewall-using-cli.md)|
  | **서비스 엔드포인트 구성** | 공용 | [포털](./howto-manage-vnet-using-portal.md) <br/> [CLI](./howto-manage-vnet-using-cli.md)|
  | **프라이빗 링크 구성** | Private | [포털](./howto-configure-privatelink-portal.md) <br/> [CLI](./howto-configure-privatelink-cli.md) |

- [Git](https://git-scm.com/downloads)이 설치됨.

## <a name="get-database-connection-information"></a>데이터베이스 연결 정보 가져오기
Azure Database for PostgreSQL 데이터베이스에 연결하려면 정규화된 서버 이름 및 로그인 자격 증명이 필요합니다. Azure Portal에서 이 정보를 가져올 수 있습니다.

1. [Azure Portal](https://portal.azure.com/)에 로그인하고 Azure Database for PostgreSQL 서버 이름을 선택합니다.
1. 서버의 **개요** 페이지에서 정규화된 **서버 이름** 및 **관리자 사용자 이름** 을 복사합니다. 정규화된 **서버 이름** 은 항상 *\<my-server-name>my-server-name>.postgres.database.azure.com* 형식을 가지며, **관리 사용자 이름** 은 항상 *\<my-admin-username>@\<my-server-name>* 형식을 갖습니다.

## <a name="review-the-code-optional"></a>코드 검토(선택 사항)

코드의 작동 방식에 관심이 있으면 다음 코드 조각을 살펴보세요. 그렇지 않으면 [애플리케이션 실행](#run-the-application)으로 건너뛰어도 됩니다.

### <a name="connect"></a>연결

`main`함수는 Azure Database for PostgreSQL에 연결하여 시작되며 연결 정보 `POSTGRES_HOST`, `POSTGRES_USER`, `POSTGRES_PASSWORD` 및 `POSTGRES_DBNAME`에 대한 다음 환경 변수에 따라 달라집니다. 기본적으로 PostgreSQL 데이터베이스 서비스는 `TLS` 연결을 요구하도록 구성됩니다. `TLS`클라이언트 애플리케이션에서 연결을 지원하지 않는 경우`TLS` 요구를 사용하지 않도록 선택할 수 있습니다. 자세한 내용은 [Azure Database for PostgreSQL - 단일 서버에서 TLS 연결 구성](./concepts-ssl-connection-security.md)을 참조하세요.

이 문서의 샘플 애플리케이션에서는 [postgres-openssl crate](https://crates.io/crates/postgres-openssl/)로 TLS를 사용합니다. [postgres::Client::connect](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.connect) 함수는 연결을 시작하는 데 사용되며, 이 작업이 실패하는 경우 프로그램이 종료됩니다.

```rust
fn main() {
    let pg_host = std::env::var("POSTGRES_HOST").expect("missing environment variable POSTGRES_HOST");
    let pg_user = std::env::var("POSTGRES_USER").expect("missing environment variable POSTGRES_USER");
    let pg_password = std::env::var("POSTGRES_PASSWORD").expect("missing environment variable POSTGRES_PASSWORD");
    let pg_dbname = std::env::var("POSTGRES_DBNAME").unwrap_or("postgres".to_string());

    let builder = SslConnector::builder(SslMethod::tls()).unwrap();
    let tls_connector = MakeTlsConnector::new(builder.build());

    let url = format!(
        "host={} port=5432 user={} password={} dbname={} sslmode=require",
        pg_host, pg_user, pg_password, pg_dbname
    );
    let mut pg_client = postgres::Client::connect(&url, tls_connector).expect("failed to connect to postgres");
...
}
```

### <a name="drop-and-create-table"></a>테이블 삭제 및 만들기

예제 애플리케이션에서는 간단한 `inventory` 테이블을 사용하여 CRUD(만들기, 읽기, 업데이트, 삭제) 작업을 시연합니다.

```sql
CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
```

`drop_create_table` 함수는 처음에 새 테이블을 만들기 전에 `inventory` 테이블 `DROP`을 시도합니다. 이렇게 하면 항상 알려진(정상) 상태로 시작되므로 학습/실험이 쉬워집니다. [execute](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.execute) 메서드는 create 및 drop 작업에 사용됩니다. 

```rust
const CREATE_QUERY: &str =
    "CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);";

const DROP_TABLE: &str = "DROP TABLE inventory";

fn drop_create_table(pg_client: &mut postgres::Client) {
    let res = pg_client.execute(DROP_TABLE, &[]);
    match res {
        Ok(_) => println!("dropped table"),
        Err(e) => println!("failed to drop table {}", e),
    }
    pg_client
        .execute(CREATE_QUERY, &[])
        .expect("failed to create 'inventory' table");
}
```

### <a name="insert-data"></a>데이터 삽입

`insert_data`는 `inventory` 테이블에 항목을 추가합니다. [prepare](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare) 함수를 사용하여 [준비된 문](https://docs.rs/postgres/0.19.0/postgres/struct.Statement.html)을 만듭니다. 


```rust
const INSERT_QUERY: &str = "INSERT INTO inventory (name, quantity) VALUES ($1, $2) RETURNING id;";

fn insert_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare(&INSERT_QUERY)
        .expect("failed to create prepared statement");

    let row = pg_client
        .query_one(&prep_stmt, &[&"item-1", &42])
        .expect("insert failed");

    let id: i32 = row.get(0);
    println!("inserted item with id {}", id);
...
}
```

또한 쿼리 매개 변수의 형식을 명시적으로 지정할 수 있도록 하는 [prepare_typed](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.prepare_typed) 메서드 사용에 유의합니다.

```rust
...
let typed_prep_stmt = pg_client
        .prepare_typed(&INSERT_QUERY, &[Type::VARCHAR, Type::INT4])
        .expect("failed to create prepared statement");

let row = pg_client
        .query_one(&typed_prep_stmt, &[&"item-2", &43])
        .expect("insert failed");

let id: i32 = row.get(0);
println!("inserted item with id {}", id);
...
```

마지막으로 `for` 루프를 사용하여 `item-3`, `item-4` 및 `item-5` 각각에 대해 임의로 생성된 수량을 추가합니다.

```rust
...
    for n in 3..=5 {
        let row = pg_client
            .query_one(
                &typed_prep_stmt,
                &[
                    &("item-".to_owned() + &n.to_string()),
                    &rand::thread_rng().gen_range(10..=50),
                ],
            )
            .expect("insert failed");

        let id: i32 = row.get(0);
        println!("inserted item with id {} ", id);
    }
...
```

### <a name="query-data"></a>쿼리 데이터

`query_data` 함수는 `inventory` 테이블에서 데이터를 검색하는 방법을 시연합니다. [query_one](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query_one) 메서드를 사용하여 해당 `id`로 항목을 가져오는 데 사용됩니다. 

```rust
const SELECT_ALL_QUERY: &str = "SELECT * FROM inventory;";
const SELECT_BY_ID: &str = "SELECT name, quantity FROM inventory where id=$1;";

fn query_data(pg_client: &mut postgres::Client) {

    let prep_stmt = pg_client
        .prepare_typed(&SELECT_BY_ID, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item_id = 1;

    let c = pg_client
        .query_one(&prep_stmt, &[&item_id])
        .expect("failed to query item");

    let name: String = c.get(0);
    let quantity: i32 = c.get(1);
    println!("quantity for item {} = {}", name, quantity);
...
}
```

inventory 테이블의 모든 행은 [query](https://docs.rs/postgres/0.19.0/postgres/struct.Client.html#method.query) 메서드로 `select * from` 쿼리를 사용하여 페치됩니다. 반환된 행은 [get](https://docs.rs/postgres/0.19.0/postgres/row/struct.Row.html#method.get)을 사용하여 각 열에 대한 값을 추출하기 위해 반복됩니다. 

> [!TIP]
> `get`을 사용하여 행의 숫자 인덱스 또는 열 이름으로 열을 지정할 수 있는 방법을 확인합니다.

```rust
...
    let items = pg_client
        .query(SELECT_ALL_QUERY, &[])
        .expect("select all failed");

    println!("listing items...");

    for item in items {
        let id: i32 = item.get("id");
        let name: String = item.get("name");
        let quantity: i32 = item.get("quantity");
        println!(
            "item info: id = {}, name = {}, quantity = {} ",
            id, name, quantity
        );
    }
...
```

### <a name="update-data"></a>데이터 업데이트

`update_date` 함수는 모든 항목의 수량을 무작위로 업데이트합니다. `insert_data` 함수는 `5` 행을 추가했으므로 `for` 루프 `for n in 1..=5`에서 동일한 항목을 고려합니다.

> [!TIP]
> `id` 및 새로 생성된 `quantity`([RETURNING 절](https://www.postgresql.org/docs/current/dml-returning.html) 사용)를 다시 가져와야 하므로 `execute` 대신 `query`를 사용합니다.

```rust
const UPDATE_QUERY: &str = "UPDATE inventory SET quantity = $1 WHERE name = $2 RETURNING quantity;";

fn update_data(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&UPDATE_QUERY, &[Type::INT4, Type::VARCHAR])
        .expect("failed to create prepared statement");

    for id in 1..=5 {
        let row = pg_client
            .query_one(
                &stmt,
                &[
                    &rand::thread_rng().gen_range(10..=50),
                    &("item-".to_owned() + &id.to_string()),
                ],
            )
            .expect("update failed");
        
        let quantity: i32 = row.get("quantity");
        println!("updated item id {} to quantity = {}", id, quantity);
    }
}
```

### <a name="delete-data"></a>데이터 삭제

마지막으로 `delete` 함수는 해당 `id`별 `inventory` 테이블에서 항목을 제거하는 방법을 보여 줍니다. `id`는 임의로 선택됩니다. `insert_data` 함수가 시작할 `5` 행을 추가했으므로 `1` ~ `5`(`5` 포함) 사이의 임의 정수입니다. 

> [!TIP]
> 방금 삭제한 항목에 대한 정보를 다시 가져오려고 하므로([RETURNING 절](https://www.postgresql.org/docs/current/dml-returning.html) 사용) `execute` 대신 `query`를 사용합니다.

```rust
const DELETE_QUERY: &str = "DELETE FROM inventory WHERE id = $1 RETURNING id, name, quantity;";

fn delete(pg_client: &mut postgres::Client) {
    let stmt = pg_client
        .prepare_typed(&DELETE_QUERY, &[Type::INT4])
        .expect("failed to create prepared statement");

    let item = pg_client
        .query_one(&stmt, &[&rand::thread_rng().gen_range(1..=5)])
        .expect("delete failed");

    let id: i32 = item.get(0);
    let name: String = item.get(1);
    let quantity: i32 = item.get(2);
    println!(
        "deleted item info: id = {}, name = {}, quantity = {} ",
        id, name, quantity
    );
}
```

## <a name="run-the-application"></a>애플리케이션 실행

1. 시작하려면 다음 명령을 실행하여 샘플 리포지토리를 복제합니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-postgresql-rust-quickstart.git
    ```

2. Azure Portal에서 복사한 값을 사용하여 필요한 환경 변수를 설정합니다.

    ```bash
    export POSTGRES_HOST=<server name e.g. my-server.postgres.database.azure.com>
    export POSTGRES_USER=<admin username e.g. my-admin-user@my-server>
    export POSTGRES_PASSWORD=<admin password>
    export POSTGRES_DBNAME=<database name. it is optional and defaults to postgres>
    ```

3. 애플리케이션을 실행하려면 복제한 디렉터리로 변경하고 `cargo run`을 실행합니다.

    ```bash
    cd azure-postgresql-rust-quickstart
    cargo run
    ```

    다음과 비슷한 내용이 출력됩니다.

    ```bash
    dropped 'inventory' table
    inserted item with id 1
    inserted item with id 2
    inserted item with id 3 
    inserted item with id 4 
    inserted item with id 5 
    quantity for item item-1 = 42
    listing items...
    item info: id = 1, name = item-1, quantity = 42 
    item info: id = 2, name = item-2, quantity = 43 
    item info: id = 3, name = item-3, quantity = 11 
    item info: id = 4, name = item-4, quantity = 32 
    item info: id = 5, name = item-5, quantity = 24 
    updated item id 1 to quantity = 27
    updated item id 2 to quantity = 14
    updated item id 3 to quantity = 31
    updated item id 4 to quantity = 16
    updated item id 5 to quantity = 10
    deleted item info: id = 4, name = item-4, quantity = 16 
    ```

4. 확인하려면 [psql을 사용하는](./quickstart-create-server-database-portal.md#connect-to-the-server-with-psql) Azure Database for PostgreSQL에 연결하고 데이터베이스에 대한 쿼리를 실행할 수도 있습니다. 예를 들면 다음과 같습니다.

    ```sql
    select * from inventory;
    ```

[문제가 있나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 사용된 모든 리소스를 정리하려면 다음 명령을 사용하여 리소스 그룹을 삭제합니다.

```azurecli
az group delete \
    --name $AZ_RESOURCE_GROUP \
    --yes
```

## <a name="next-steps"></a>다음 단계
> [!div class="nextstepaction"]
> [포털을 사용하여 Azure Database for PostgreSQL 서버 관리](./howto-create-manage-server-portal.md)<br/>

> [!div class="nextstepaction"]
> [CLI를 사용하여 Azure Database for PostgreSQL 서버 관리](./how-to-manage-server-cli.md)<br/>

[원하는 항목을 찾을 수 없나요? 알려주세요.](https://aka.ms/postgres-doc-feedback)
