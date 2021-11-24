---
title: Go에서 Azure Cache for Redis 사용
description: 이 빠른 시작에서는 Azure Cache for Redis를 사용하는 Go 앱을 만드는 방법을 알아봅니다.
author: abhirockzz
ms.author: abhishgu
ms.service: cache
ms.devlang: go
ms.topic: quickstart
ms.date: 01/08/2021
ms.custom: mode-other
ms.openlocfilehash: e9b0915dde16a1eb1caa1d877fd71205c6ebaaf3
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133044979"
---
# <a name="quickstart-use-azure-cache-for-redis-with-go"></a>빠른 시작: Go에서 Azure Cache for Redis 사용

이 문서에서는 [Azure Cache for Redis](./cache-overview.md)에서 [HASH](https://redis.io/topics/data-types-intro#redis-hashes) 데이터 구조가 지원하는 사용자 정보를 저장하고 검색하는 REST API를 Go에서 빌드하는 방법을 알아봅니다.

## <a name="skip-to-the-code-on-github"></a>GitHub의 코드로 건너뛰기

코드로 바로 건너뛰려면 GitHub의 [Go 빠른 시작](https://github.com/Azure-Samples/azure-redis-cache-go-quickstart/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [Go](https://golang.org/doc/install)(버전 1.13 이상 권장)
- [Git](https://git-scm.com/downloads)
- HTTP 클라이언트(예: [curl](https://curl.se/))

## <a name="create-an-azure-cache-for-redis-instance"></a>Azure Cache for Redis 인스턴스 만들기

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-create](includes/redis-cache-access-keys.md)]

## <a name="review-the-code-optional"></a>코드 검토(선택 사항)

코드의 작동 방식에 관심이 있으면 다음 코드 조각을 살펴보세요. 그렇지 않으면 [애플리케이션 실행](#run-the-application)으로 건너뛰어도 됩니다.

오픈 소스 [go-redis](https://github.com/go-redis/redis) 라이브러리는 Azure Cache for Redis와 상호 작용하는 데 사용됩니다.

`main` 함수는 먼저 Azure Cache for Redis 인스턴스의 호스트 이름과 암호(액세스 키)부터 읽습니다.

```go
func main() {
    redisHost := os.Getenv("REDIS_HOST")
    redisPassword := os.Getenv("REDIS_PASSWORD")
...
```

그런 다음, Azure Cache for Redis와의 연결을 설정합니다. [tls.Config](https://golang.org/pkg/crypto/tls/#Config)를 사용합니다. Azure Cache for Redis는 [필요한 최소 버전인 TLS 1.2](cache-remove-tls-10-11.md)와의 보안 연결만 수락합니다.

```go
...
op := &redis.Options{Addr: redisHost, Password: redisPassword, TLSConfig: &tls.Config{MinVersion: tls.VersionTLS12}}
client := redis.NewClient(op)

ctx := context.Background()
err := client.Ping(ctx).Err()
if err != nil {
    log.Fatalf("failed to connect with redis instance at %s - %v", redisHost, err)
}
...
```

연결에 성공하면 [HTTP 처리기](https://golang.org/pkg/net/http/#HandleFunc)가 `POST` 및 `GET` 작업을 처리하도록 구성되고 HTTP 서버가 시작됩니다.

> [!NOTE]
> 라우팅에는 [gorilla mux 라이브러리](https://github.com/gorilla/mux)가 사용됩니다(이는 반드시 필요한 것은 아니며 이 샘플 애플리케이션에 대한 표준 라이브러리를 대신 사용할 수도 있음).
>

```go
uh := userHandler{client: client}

router := mux.NewRouter()
router.HandleFunc("/users/", uh.createUser).Methods(http.MethodPost)
router.HandleFunc("/users/{userid}", uh.getUser).Methods(http.MethodGet)

log.Fatal(http.ListenAndServe(":8080", router))
```

`userHandler` 구조체는 `createUser`, `getUser` 메서드에서 사용하는 [redis.Client](https://pkg.go.dev/github.com/go-redis/redis/v8#Client)를 캡슐화합니다. 간단한 설명을 위해 이러한 메서드에 대한 코드는 포함되지 않습니다.

- `createUser`: JSON 페이로드(사용자 정보 포함)를 수락하고 Azure Cache for Redis에 `HASH`로 저장합니다.
- `getUser`: `HASH`에서 사용자 정보를 가져오고, 찾을 수 없는 경우 HTTP `404` 응답을 반환합니다.

```go
type userHandler struct {
    client *redis.Client
}
...

func (uh userHandler) createUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
...

func (uh userHandler) getUser(rw http.ResponseWriter, r *http.Request) {
    // details omitted
}
```

## <a name="clone-the-sample-application"></a>샘플 애플리케이션 복제

GitHub에서 애플리케이션을 복제하여 시작합니다.

1. 명령 프롬프트를 열고 `git-samples`라는 새 폴더를 만듭니다.

    ```bash
    md "C:\git-samples"
    ```

1. git bash와 같은 git 터미널 창을 엽니다. `cd` 명령을 사용하여 샘플 앱을 복제하려는 새 폴더로 변경합니다.

    ```bash
    cd "C:\git-samples"
    ```

1. 다음 명령을 실행하여 샘플 리포지토리를 복제합니다. 이 명령은 컴퓨터에서 샘플 앱의 복사본을 만듭니다.

    ```bash
    git clone https://github.com/Azure-Samples/azure-redis-cache-go-quickstart.git
    ```

## <a name="run-the-application"></a>애플리케이션 실행

애플리케이션은 환경 변수 형식의 연결 및 자격 증명을 허용합니다.

1. [Azure Portal](https://portal.azure.com/)에서 Azure Cache for Redis 인스턴스의 **호스트 이름** 및 **액세스 키**(액세스 키를 통해 사용 가능)를 가져옵니다.

1. 해당 환경 변수로 설정합니다.

    ```console
    set REDIS_HOST=<Host name>:<port> (e.g. <name of cache>.redis.cache.windows.net:6380)
    set REDIS_PASSWORD=<Primary Access Key>
    ```

1. 터미널 창에서 올바른 폴더로 변경합니다. 예를 들면 다음과 같습니다.

    ```console
    cd "C:\git-samples\azure-redis-cache-go-quickstart"
    ```

1. 터미널에서 다음 명령을 실행하여 애플리케이션을 시작합니다.

    ```console
    go run main.go
    ```

HTTP 서버가 `8080` 포트에서 시작됩니다.

## <a name="test-the-application"></a>애플리케이션 테스트

1. 몇 가지 사용자 항목을 만듭니다. 아래 예제에서는 curl을 사용합니다.

    ```bash
    curl -i -X POST -d '{"id":"1","name":"foo1", "email":"foo1@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"2","name":"foo2", "email":"foo2@baz.com"}' localhost:8080/users/
    curl -i -X POST -d '{"id":"3","name":"foo3", "email":"foo3@baz.com"}' localhost:8080/users/
    ```

1. `id`를 사용하여 기존 사용자를 가져옵니다.

    ```bash
    curl -i localhost:8080/users/1
    ```

    다음과 같은 JSON 응답을 가져와야 합니다.

    ```json
    {
        "email": "foo1@bar",
        "id": "1",
        "name": "foo1"
    }
    ```

1. 존재하지 않는 사용자를 가져오려고 하면 HTTP `404`가 수신됩니다. 예를 들면 다음과 같습니다.

    ```bash
    curl -i localhost:8080/users/100
    
    #response

    HTTP/1.1 404 Not Found
    Date: Fri, 08 Jan 2021 13:43:39 GMT
    Content-Length: 0
    ```

## <a name="clean-up-resources"></a>리소스 정리

이 빠른 시작에서 만든 Azure 리소스 그룹 및 리소스 애플리케이션을 완료했으면 요금이 청구되지 않도록 삭제할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹을 삭제하면 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 유지하려는 기존 리소스 그룹에 Azure Cache for Redis 인스턴스를 만든 경우 캐시 **개요** 페이지에서 **삭제** 를 선택하여 캐시만 삭제할 수 있습니다.

Azure 인스턴스에 대한 리소스 그룹 및 해당 Redis Cache을 삭제하려면 다음을 수행합니다.

1. [Azure Portal](https://portal.azure.com)에서 **리소스 그룹** 을 찾아 선택합니다.
1. **이름으로 필터링** 텍스트 상자에 캐시 인스턴스를 포함하는 리소스 그룹의 이름을 입력한 다음, 검색 결과에서 선택합니다.
1. 리소스 그룹 페이지에서 **리소스 그룹 삭제** 를 선택합니다.
1. 리소스 그룹 이름을 입력한 다음, **삭제** 를 선택합니다.

   ![Azure Cache for Redis에 대한 리소스 그룹 삭제](./media/cache-python-get-started/delete-your-resource-group-for-azure-cache-for-redis.png)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Cache for Redis에서 Go 사용을 시작하는 방법을 알아보았습니다. Redis `HASH` 데이터 구조에서 지원하는 사용자 정보를 만들고 가져오는 간단한 REST API 기반 애플리케이션을 구성하고 실행했습니다.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 간단한 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)
