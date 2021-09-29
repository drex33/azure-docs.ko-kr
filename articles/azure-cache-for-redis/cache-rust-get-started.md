---
title: Rust에서 Azure Cache for Redis 사용
description: 이 빠른 시작에서는 Rust를 사용하여 Azure Cache for Redis와 상호 작용하는 방법을 알아봅니다.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: rust
ms.topic: quickstart
ms.date: 01/08/2021
ms.openlocfilehash: 23e1bdc895da7ba9941901832d7fc89cd9c6196a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128592625"
---
# <a name="quickstart-use-azure-cache-for-redis-with-rust"></a>빠른 시작: Rust에서 Azure Cache for Redis 사용

이 문서에서는 [Rust 프로그래밍 언어](https://www.rust-lang.org/)를 사용하여 [Azure Cache for Redis](./cache-overview.md)와 상호 작용하는 방법에 대해 알아봅니다. 또한 일반적으로 사용되는 Redis 데이터 구조에 대해서도 알아봅니다. 

* [String](https://redis.io/topics/data-types-intro#redis-strings) 
* [해시](https://redis.io/topics/data-types-intro#redis-hashes) 
* [목록](https://redis.io/topics/data-types-intro#redis-lists) 

이 샘플에서는 Redis용 [redis-rs](https://github.com/mitsuhiko/redis-rs) 라이브러리를 사용합니다. 이 클라이언트는 상위 수준 및 하위 수준 API를 모두 제공하며, 이러한 스타일의 작동 모습을 볼 수 있습니다.

## <a name="skip-to-the-code-on-github"></a>GitHub의 코드로 건너뛰기

코드로 바로 건너뛰려면 GitHub의 [Rust 빠른 시작](https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Rust](https://www.rust-lang.org/tools/install)(버전 1.39 이상)
- [Git](https://git-scm.com/downloads)

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 만들기
[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>코드 검토(선택 사항)

코드의 작동 방식에 관심이 있으면 다음 코드 조각을 살펴보세요. 그렇지 않으면 [애플리케이션 실행](#run-the-application)으로 건너뛰어도 됩니다.

`connect` 함수는 Azure Cache for Redis에 대한 연결을 설정하는 데 사용됩니다. 호스트 이름과 암호(액세스 키)는 각각 `REDIS_HOSTNAME` 및 `REDIS_PASSWORD` 환경 변수를 통해 전달되어야 합니다. 연결 URL의 형식은 `rediss://<username>:<password>@<hostname>`입니다. Azure Cache for Redis는 [최소 필수 버전인 TLS 1.2](cache-remove-tls-10-11.md)를 통한 보안 연결만 허용합니다.

[redis::Client::open](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.open)을 호출하면 기본 유효성 검사가 수행되고 [get_connection()](https://docs.rs/redis/0.19.0/redis/struct.Client.html#method.get_connection)이 실제로 연결을 시작합니다. 어떤 이유로 연결이 실패하면 프로그램이 중지됩니다. 한 가지 이유를 예로 들면 잘못된 암호가 있습니다.

```rust
fn connect() -> redis::Connection {
    let redis_host_name =
        env::var("REDIS_HOSTNAME").expect("missing environment variable REDIS_HOSTNAME");
    let redis_password =
        env::var("REDIS_PASSWORD").expect("missing environment variable REDIS_PASSWORD");
    let redis_conn_url = format!("rediss://:{}@{}", redis_password, redis_host_name);

    redis::Client::open(redis_conn_url)
        .expect("invalid connection URL")
        .get_connection()
        .expect("failed to connect to redis")
}
```

`basics` 함수는 [SET](https://redis.io/commands/set), [GET](https://redis.io/commands/get) 및 [INCR](https://redis.io/commands/incr) 명령을 포함합니다. 

하위 수준 API가 `foo`라는 키에 대한 값을 설정하고 검색하는 `SET` 및 `GET`에 사용됩니다. 

`INCRBY` 명령은 상위 수준 API를 사용하여 실행됩니다. 즉, [incr](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.incr)은 `counter`라는 키의 값을 `2`만큼 증가시킨 다음, [get](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.get)을 호출하여 이를 검색합니다.

```rust
fn basics() {
    let mut conn = connect();
    let _: () = redis::cmd("SET")
        .arg("foo")
        .arg("bar")
        .query(&mut conn)
        .expect("failed to execute SET for 'foo'");

    let bar: String = redis::cmd("GET")
        .arg("foo")
        .query(&mut conn)
        .expect("failed to execute GET for 'foo'");
    println!("value for 'foo' = {}", bar);

    let _: () = conn
        .incr("counter", 2)
        .expect("failed to execute INCR for 'counter'");
    let val: i32 = conn
        .get("counter")
        .expect("failed to execute GET for 'counter'");
    println!("counter = {}", val);
}
```

아래 코드 조각에서는 Redis `HASH` 데이터 구조의 기능을 보여 줍니다. Redis 드라이버(클라이언트)에 대한 정보(`name`, `version`, `repo`)를 저장하기 위해 하위 수준 API를 사용하여 [HSET](https://redis.io/commands/hset)를 호출합니다. 예를 들어 Rust 드라이버(이 샘플 코드에서 사용되는 항목)에 대한 세부 정보를 [BTreeMap](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html) 형식으로 캡처한 다음, 하위 수준 API에 전달합니다. 그런 다음, [HGETALL](https://redis.io/commands/hgetall)을 사용하여 해당 정보를 검색합니다.

또한 `HSET`는 튜플 배열을 허용하는 [hset_multiple](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hset_multiple)을 사용하여 상위 수준 API를 통해 실행할 수도 있습니다. 그런 다음, [hget](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.hget)을 실행하여 단일 특성(이 경우 `repo`)에 대한 값을 가져옵니다.

```rust
fn hash() {
    let mut conn = connect();

    let mut driver: BTreeMap<String, String> = BTreeMap::new();
    let prefix = "redis-driver";
    driver.insert(String::from("name"), String::from("redis-rs"));
    driver.insert(String::from("version"), String::from("0.19.0"));
    driver.insert(
        String::from("repo"),
        String::from("https://github.com/mitsuhiko/redis-rs"),
    );

    let _: () = redis::cmd("HSET")
        .arg(format!("{}:{}", prefix, "rust"))
        .arg(driver)
        .query(&mut conn)
        .expect("failed to execute HSET");

    let info: BTreeMap<String, String> = redis::cmd("HGETALL")
        .arg(format!("{}:{}", prefix, "rust"))
        .query(&mut conn)
        .expect("failed to execute HGETALL");
    println!("info for rust redis driver: {:?}", info);

    let _: () = conn
        .hset_multiple(
            format!("{}:{}", prefix, "go"),
            &[
                ("name", "go-redis"),
                ("version", "8.4.6"),
                ("repo", "https://github.com/go-redis/redis"),
            ],
        )
        .expect("failed to execute HSET");

    let repo_name: String = conn
        .hget(format!("{}:{}", prefix, "go"), "repo")
        .expect("HGET failed");
    println!("go redis driver repo name: {:?}", repo_name);
}
```

아래 함수에서는 `LIST` 데이터 구조를 사용하는 방법을 확인할 수 있습니다. [LPUSH](https://redis.io/commands/lpush)를 실행하여(하위 수준 API 사용) 항목을 목록에 추가하고, 상위 수준 [lpop](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lpop) 메서드를 사용하여 목록에서 해당 항목을 검색합니다. 그런 다음, [rpush](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.rpush) 메서드를 사용하여 두 개의 항목을 목록에 추가한 다음, 하위 수준 [lrange](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.lrange) 메서드를 사용하여 해당 항목을 가져옵니다.

```rust
fn list() {
    let mut conn = connect();
    let list_name = "items";

    let _: () = redis::cmd("LPUSH")
        .arg(list_name)
        .arg("item-1")
        .query(&mut conn)
        .expect("failed to execute LPUSH for 'items'");

    let item: String = conn
        .lpop(list_name)
        .expect("failed to execute LPOP for 'items'");
    println!("first item: {}", item);

    let _: () = conn.rpush(list_name, "item-2").expect("RPUSH failed");
    let _: () = conn.rpush(list_name, "item-3").expect("RPUSH failed");

    let len: isize = conn
        .llen(list_name)
        .expect("failed to execute LLEN for 'items'");
    println!("no. of items in list = {}", len);

    let items: Vec<String> = conn
        .lrange(list_name, 0, len - 1)
        .expect("failed to execute LRANGE for 'items'");

    println!("listing items in list");
    for item in items {
        println!("item: {}", item)
    }
}
```

여기서 `SET` 작업 중 일부를 확인할 수 있습니다. [sadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.sadd)(상위 수준 API) 메서드를 사용하여 몇 가지 항목을 `users`라는 `SET`에 추가합니다. 그런 다음, [SISMEMBER](https://redis.io/commands/hset)(하위 수준 API)를 실행하여 `user1`이 있는지 확인합니다. 마지막으로 [smembers](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.smembers)를 사용하여 모든 집합 항목을 벡터([Vec\<String\>](https://doc.rust-lang.org/std/vec/struct.Vec.html)) 형식으로 가져오고 반복합니다.

```rust
fn set() {
    let mut conn = connect();
    let set_name = "users";

    let _: () = conn
        .sadd(set_name, "user1")
        .expect("failed to execute SADD for 'users'");
    let _: () = conn
        .sadd(set_name, "user2")
        .expect("failed to execute SADD for 'users'");

    let ismember: bool = redis::cmd("SISMEMBER")
        .arg(set_name)
        .arg("user1")
        .query(&mut conn)
        .expect("failed to execute SISMEMBER for 'users'");
    println!("does user1 exist in the set? {}", ismember);

    let users: Vec<String> = conn.smembers(set_name).expect("failed to execute SMEMBERS");
    println!("listing users in set");

    for user in users {
        println!("user: {}", user)
    }
}
```

아래의 `sorted_set` 함수는 정렬된 집합 데이터 구조를 보여 줍니다. 하위 수준 API를 사용하여 [ZADD](https://redis.io/commands/zadd)가 호출되고 플레이어(`player-1`)에 대한 임의 정수 점수가 추가됩니다. 다음으로 [zadd](https://docs.rs/redis/0.19.0/redis/trait.Commands.html#method.zadd) 메서드(상위 수준 API)를 사용하여 더 많은 플레이어(`player-2`~`player-5`) 및 각 플레이어에 대한 점수(임의 생성)를 추가합니다. 정렬된 집합의 항목 수는 [ZCARD](https://redis.io/commands/zcard)를 사용하여 계산됩니다. 이는 [ZRANGE](https://redis.io/commands/zrange) 명령(하위 수준 API를 통해 호출)에 대한 제한으로 사용되어 플레이어를 점수와 함께 오름차순으로 나열합니다.

```rust
fn sorted_set() {
    let mut conn = connect();
    let sorted_set = "leaderboard";

    let _: () = redis::cmd("ZADD")
        .arg(sorted_set)
        .arg(rand::thread_rng().gen_range(1..10))
        .arg("player-1")
        .query(&mut conn)
        .expect("failed to execute ZADD for 'leaderboard'");

    for num in 2..=5 {
        let _: () = conn
            .zadd(
                sorted_set,
                String::from("player-") + &num.to_string(),
                rand::thread_rng().gen_range(1..10),
            )
            .expect("failed to execute ZADD for 'leaderboard'");
    }

    let count: isize = conn
        .zcard(sorted_set)
        .expect("failed to execute ZCARD for 'leaderboard'");

    let leaderboard: Vec<(String, isize)> = conn
        .zrange_withscores(sorted_set, 0, count - 1)
        .expect("ZRANGE failed");

    println!("listing players and scores in ascending order");

    for item in leaderboard {
        println!("{} = {}", item.0, item.1)
    }
}
```

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

GitHub에서 애플리케이션을 복제하여 시작합니다.

1. 명령 프롬프트를 열고 `git-samples`라는 새 폴더를 만듭니다.

    ```bash
    md "C:\git-samples"
    ```

1. git bash와 같은 git 터미널 창을 엽니다. `cd`를 사용하여 새 폴더로 변경하고 샘플 앱을 복제합니다.

    ```bash
    cd "C:\git-samples"
    ```

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-rust-quickstart.git
    ```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션은 환경 변수 형식의 연결 및 자격 증명을 허용합니다. 

1. [Azure Portal](https://portal.azure.com/)에서 Azure Cache for Redis 인스턴스에 대한 **호스트 이름** 및 **액세스 키** 를 가져옵니다(액세스 키를 통해 사용 가능).

1. 해당 환경 변수로 설정합니다.

    ```shell
    set REDIS_HOSTNAME=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. 터미널 창에서 올바른 폴더로 변경합니다. 예를 들면 다음과 같습니다.

    ```shell
    cd "C:\git-samples\azure-redis-cache-rust-quickstart"
    ```

1. 터미널에서 다음 명령을 실행하여 애플리케이션을 시작합니다.

    ```shell
    cargo run
    ```
    
    다음 출력이 표시됩니다.
    
    ```bash
    ******* Running SET, GET, INCR commands *******
    value for 'foo' = bar
    counter = 2
    ******* Running HASH commands *******
    info for rust redis driver: {"name": "redis-rs", "repo": "https://github.com/mitsuhiko/redis-rs", "version": "0.19.0"}
    go redis driver repo name: "https://github.com/go-redis/redis"
    ******* Running LIST commands *******
    first item: item-1
    no. of items in list = 2
    listing items in list
    item: item-2
    item: item-3
    ******* Running SET commands *******
    does user1 exist in the set? true
    listing users in set
    user: user2
    user: user1
    user: user3
    ******* Running SORTED SET commands *******
    listing players and scores
    player-2 = 2
    player-4 = 4
    player-1 = 7
    player-5 = 6
    player-3 = 8
    ```
    
    특정 함수를 실행하려면 `main` 함수에서 다른 함수를 주석으로 처리합니다.
    
    ```rust
    fn main() {
        basics();
        hash();
        list();
        set();
        sorted_set();
    }
    ```

## <a name="clean-up-resources"></a>리소스 정리

완료되면 리소스 그룹 및 리소스를 삭제할 수 있습니다. 이 빠른 시작에서 만든 내용을 삭제하면 요금이 청구되지 않습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 유지하려는 기존 리소스 그룹에 Azure Cache for Redis 인스턴스를 만든 경우 캐시 **개요** 페이지에서 **삭제** 를 선택하여 캐시만 삭제할 수 있습니다. 

Azure 인스턴스에 대한 리소스 그룹 및 해당 Redis Cache을 삭제하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 찾아 선택합니다.
1. **이름을 기준으로 필터링** 텍스트 상자에 캐시 인스턴스가 들어 있는 리소스 그룹의 이름을 입력합니다. 그런 다음, 검색 결과에서 선택합니다. 
1. 리소스 그룹 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. 리소스 그룹 이름을 입력한 다음, **삭제** 를 선택합니다.
   
   ![Azure Cache for Redis에 대한 리소스 그룹 삭제](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Redis용 Rust 드라이버를 사용하여 Azure Cache for Redis에서 작업을 연결하고 실행하는 방법을 알아보았습니다.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 간단한 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)
