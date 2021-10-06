---
title: '빠른 시작: Node.js에서 Azure Cache for Redis 사용'
description: 이 빠른 시작에서는 Node.js 및 node_redis와 함께 Azure Cache for Redis를 사용하는 방법을 알아봅니다.
author: curib
ms.service: cache
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/21/2018
ms.author: cauribeg
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019, devx-track-js
ms.openlocfilehash: c975794d09cdd9f7f9de538fdf2debfb37bea2d3
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129537837"
---
# <a name="quickstart-use-azure-cache-for-redis-in-nodejs"></a>빠른 시작: Node.js에서 Azure Cache for Redis 사용

이 빠른 시작에서는 Azure 내 모든 애플리케이션에서 액세스할 수 있는 안전한 전용 캐시에 액세스할 수 있도록 Azure Cache for Redis를 Node.js 앱에 통합합니다.

## <a name="skip-to-the-code-on-github"></a>GitHub의 코드로 건너뛰기

코드로 바로 건너뛰려면 GitHub의 [Node.js 빠른 시작](https://github.com/Azure-Samples/azure-cache-redis-samples/tree/main/quickstart/nodejs)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/)
- [node_redis](https://github.com/mranney/node_redis) - `npm install redis` 명령을 사용하여 설치할 수 있습니다.

다른 Node.js 클라이언트를 사용한 예는 [Node.js Redis 클라이언트](https://redis.io/clients#nodejs)에 나열된 Node.js 클라이언트의 개별 설명서를 참조하세요.

## <a name="create-a-cache"></a>캐시 만들기

[!INCLUDE [redis-cache-create](includes/redis-cache-create.md)]

[!INCLUDE [redis-cache-access-keys](includes/redis-cache-access-keys.md)]

**호스트 이름** 및 **기본** 액세스 키에 대한 환경 변수를 추가합니다. 코드에 직접 중요한 정보를 포함하는 대신 사용자 코드에서 이러한 변수를 사용합니다.

```powershell
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

```powershell
set REDISCACHEHOSTNAME=contosoCache.redis.cache.windows.net
set REDISCACHEKEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

## <a name="connect-to-the-cache"></a>캐시에 연결

[node_redis](https://github.com/mranney/node_redis)의 최신 빌드는 TLS를 사용하여 Azure Cache for Redis에 연결을 지원합니다. 다음 예제에서는 6380 TLS 엔드포인트를 사용하여 Azure Cache for Redis에 연결하는 방법을 보여 줍니다.

```js
var redis = require("redis");

// Add your cache name and access key.
var client = redis.createClient(6380, process.env.REDISCACHEHOSTNAME,
    {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
```

코드에서 각 작업에 대해 새 연결을 만들지 마세요. 대신 가능한 한 연결을 다시 사용합니다.

## <a name="create-a-new-nodejs-app"></a>새 Node.js 앱 만들기

*redistest.js* 라는 새 스크립트 파일을 만듭니다. `npm install redis bluebird` 명령을 사용하여 필요한 패키지를 설치합니다.

다음 예제 JavaScript를 파일에 추가합니다. 이 코드는 캐시 호스트 이름 및 키 환경 변수를 사용하여 Azure Cache for Redis 인스턴스에 연결하는 방법을 보여 줍니다. 코드는 또한 캐시에 문자열 값을 저장하고 검색합니다. 또한 `PING` 및 `CLIENT LIST` 명령을 실행했습니다. [node_redis](https://github.com/mranney/node_redis) 클라이언트가 포함된 Redis 사용에 관한 더 많은 예는 [https://redis.js.org/](https://redis.js.org/)를 참조하세요.

```js
var redis = require("redis");
var bluebird = require("bluebird");

// Convert Redis client API to use promises, to make it usable with async/await syntax
bluebird.promisifyAll(redis.RedisClient.prototype);
bluebird.promisifyAll(redis.Multi.prototype);

async function testCache() {

    // Connect to the Azure Cache for Redis over the TLS port using the key.
    var cacheConnection = redis.createClient(6380, process.env.REDISCACHEHOSTNAME, 
        {auth_pass: process.env.REDISCACHEKEY, tls: {servername: process.env.REDISCACHEHOSTNAME}});
        
    // Perform cache operations using the cache connection object...

    // Simple PING command
    console.log("\nCache command: PING");
    console.log("Cache response : " + await cacheConnection.pingAsync());

    // Simple get and put of integral data types into the cache
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    console.log("\nCache command: SET Message");
    console.log("Cache response : " + await cacheConnection.setAsync("Message",
        "Hello! The cache is working from Node.js!"));    

    // Demonstrate "SET Message" executed as expected...
    console.log("\nCache command: GET Message");
    console.log("Cache response : " + await cacheConnection.getAsync("Message"));    

    // Get the client list, useful to see if connection list is growing...
    console.log("\nCache command: CLIENT LIST");
    console.log("Cache response : " + await cacheConnection.clientAsync("LIST"));    
}

testCache();
```

Node.js를 사용하여 스크립트를 실행합니다.

```powershell
node redistest.js
```

아래 예제에서는 이전에 Azure Portal에서 Redis 콘솔을 사용하여 설정된 캐시된 값이 있는 `Message` 키를 볼 수 있습니다. 앱에서 캐시된 값을 업데이트했습니다. 또한 앱에서 `PING` 및 `CLIENT LIST` 명령을 실행했습니다.

![Redis Cache 앱 완료](./media/cache-nodejs-get-started/redis-cache-app-complete.png)

## <a name="clean-up-resources"></a>리소스 정리

다음 자습서로 계속 진행하려면 이 빠른 시작에서 만든 리소스를 유지하여 다시 사용할 수 있습니다.

그렇지 않고, 빠른 시작 샘플 애플리케이션 사용이 끝나면 이 빠른 시작에서 만든 Azure 리소스를 삭제하여 요금이 청구되는 것을 방지할 수 있습니다.

> [!IMPORTANT]
> 리소스 그룹 삭제는 취소할 수 없으며 해당 리소스 그룹 및 해당 그룹 안에 있는 모든 리소스는 영구적으로 삭제됩니다. 잘못된 리소스 그룹 또는 리소스를 자동으로 삭제하지 않도록 해야 합니다. 유지하려는 리소스가 포함된 기존 리소스 그룹 내서에 이 샘플을 호스팅하는 리소스를 만든 경우 리소스 그룹을 삭제하는 대신, 완쪽에서 각 리소스를 개별적으로 삭제할 수 있습니다.
>

[Azure Portal](https://portal.azure.com)에 로그인하고 **리소스 그룹** 을 선택합니다.

**이름으로 필터링** 텍스트 상자에서 리소스 그룹의 이름을 입력합니다. 이 문서의 지침에서는 *TestResources* 라는 리소스 그룹을 사용했습니다. 결과 목록의 리소스 그룹에서 **...** 를 선택한 다음, **리소스 그룹 삭제** 를 선택합니다.

![Azure Resource 그룹 삭제](./media/cache-nodejs-get-started/redis-cache-delete-resource-group.png)

리소스 그룹을 삭제할지 확인하는 메시지가 표시됩니다. 리소스 그룹의 이름을 입력하여 확인하고 **삭제** 를 선택합니다.

잠시 후, 리소스 그룹 및 해당 그룹에 포함된 모든 리소스가 삭제됩니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Node.js 애플리케이션에서 Azure Cache for Redis를 사용하는 방법을 알아보았습니다. ASP.NET 웹앱에서 Azure Cache for Redis를 사용하려면 다음 빠른 시작으로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure Cache for Redis를 사용하는 ASP.NET 웹앱 만들기](./cache-web-app-howto.md)
