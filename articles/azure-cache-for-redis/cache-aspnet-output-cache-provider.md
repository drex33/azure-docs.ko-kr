---
title: Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자
description: Azure Cache for Redis를 사용하여 ASP.NET 페이지 출력을 캐시하는 방법을 알아봅니다. Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 스토리지 메커니즘입니다.
author: curib
ms.author: cauribeg
ms.service: cache
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 04/22/2018
ms.openlocfilehash: 2f255014a477e2fe920be44a82d94f3002896205
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129620605"
---
# <a name="aspnet-output-cache-provider-for-azure-cache-for-redis"></a>Azure Cache for Redis에 대한 ASP.NET 출력 캐시 공급자

Redis 출력 캐시 공급자는 출력 캐시 데이터에 대한 out-of-process 스토리지 메커니즘입니다. 이 데이터는 완전한 HTTP 응답(페이지 출력 캐싱)에 특별히 사용됩니다. 공급자가 ASP.NET 4에 도입된 새로운 출력 캐시 공급자 확장 포인트에 연결됩니다. ASP.NET Core 애플리케이션의 경우 [ASP.NET Core에서 응답 캐싱](/aspnet/core/performance/caching/response)을 읽습니다.

Redis 출력 캐시 공급자를 사용하려면 먼저 캐시를 구성한 다음, Redis 출력 개시 공급자 NuGet 패키지를 사용하여 사용자의 ASP.NET 애플리케이션을 구성합니다. 이 문서에서는 Redis 출력 캐시 공급자를 사용할 수 있도록 애플리케이션을 구성하는 지침을 제공합니다. Azure Cache for Redis 인스턴스를 만들고 구성하는 방법에 대한 자세한 내용은 [캐시 만들기](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache)를 참조하세요.

## <a name="store-aspnet-page-output-in-the-cache"></a>ASP.NET 페이지 출력을 캐시에 저장

Azure Cache for Redis 세션 상태 NuGet 패키지를 사용하여 Visual Studio에서 클라이언트 애플리케이션을 구성하려면 **도구** 메뉴에서 **NuGet 패키지 관리자**, **패키지 관리자 콘솔** 을 차례로 클릭합니다.

`Package Manager Console` 창에서 다음 명령을 실행합니다.

```
Install-Package Microsoft.Web.RedisOutputCacheProvider
```

Redis 출력 캐시 공급자 NuGet 패키지는 Redis 패키지에 종속 되어 있습니다. Redis 패키지가 프로젝트에 없는 경우 설치 됩니다. Redis 출력 캐시 공급자 NuGet 패키지에 대한 자세한 내용은 [RedisOutputCacheProvider](https://www.nuget.org/packages/Microsoft.Web.RedisOutputCacheProvider/) NuGet 패키지를 참조하세요.

NuGet 패키지에서는 필수 어셈블리 참조를 다운로드하고 추가하며 web.config 파일에 다음 섹션을 추가합니다. 이 섹션에서는 Redis 출력 캐시 공급자를 사용하기 위해 ASP.NET 애플리케이션에 필수 구성을 포함합니다.

```xml
<caching>
  <outputCache defaultProvider="MyRedisOutputCache">
    <providers>
      <add name="MyRedisOutputCache" type="Microsoft.Web.Redis.RedisOutputCacheProvider"
           host=""
           accessKey=""
           ssl="true" />
    </providers>
  </outputCache>
</caching>
```

Microsoft Azure portal 캐시의 값을 사용하여 왼쪽의 특성을 구성합니다. 또한 원하는 다른 값을 구성합니다. 캐시 속성에 액세스하는 방법에 대한 지침은 [Azure Cache for Redis 설정 구성](cache-configure.md#configure-azure-cache-for-redis-settings)을 참조하세요.

| attribute | Type | 기본값 | 설명 |
| --------- | ---- | ------- | ----------- |
| *host* | 문자열 | "localhost" | Redis 서버 IP 주소 또는 호스트 이름 |
| *port* | 양의 정수 | 6379(비 TLS/SSL)<br/>6380(TLS/SSL) | Redis 서버 포트 |
| *accessKey* | 문자열 | "" | Redis 권한 부여를 사용하는 경우 Redis 서버 암호입니다. 기본적으로이 값은 빈 문자열입니다. 즉, 세션 상태 공급자가 Redis 서버에 연결할 때 암호를 사용 하지 않습니다. **Redis 서버가 Azure Cache for Redis와 같은 공개적으로 액세스할 수 있는 네트워크에 있는 경우 보안을 향상시키기 위해 Redis 권한 부여를 사용하도록 설정하고 보안 암호를 제공해야 합니다.** |
| *ssl* | boolean | **false** | TLS를 통해 Redis 서버에 연결할지 여부를 지정합니다. Redis는 기본값으로 TLS를 지원하지 않으므로 이 값은 기본적으로 **false** 입니다. **기본값으로 SSL을 지원하는 Azure Cache for Redis를 사용하는 경우 보안을 강화하려면 이 값을 true로 설정해야 합니다.**<br/><br/>비 TLS 포트는 기본적으로 새 캐시에 대해 사용하지 않도록 설정됩니다. 비 TLS 포트를 사용하려면 이 설정에 대해 **true** 를 지정합니다. 비-TLS 포트 사용 방법에 대한 자세한 내용은 [캐시 구성](cache-configure.md) 문서의 [액세스 포트](cache-configure.md#access-ports) 섹션을 참조하세요. |
| *databaseIdNumber* | 양의 정수 | 0 | *이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>사용할 Redis 데이터베이스를 지정합니다. |
| *connectionTimeoutInMilliseconds* | 양의 정수 | StackExchange.Redis에서 제공 | StackExchange.Redis.ConnectionMultiplexer를 만들 때 *ConnectTimeout* 을 설정하는 데 사용됩니다. |
| *operationTimeoutInMilliseconds* | 양의 정수 | StackExchange.Redis에서 제공 | StackExchange.Redis.ConnectionMultiplexer를 만들 때 *SyncTimeout* 을 설정하는 데 사용됩니다. |
| *connectionString*(올바른 StackExchange.Redis 연결 문자열) | 문자열 | *해당 없음* | AppSettings 또는 web.config에 대한 매개 변수 참조 또는 올바른 StackExchange.Redis 연결 문자열입니다. 이 특성은 *호스트*, *포트*, *accessKey*, *ssl* 및 기타 StackExchange.Redis 특성에 대한 값을 제공할 수 있습니다. *connectionString* 에 대해 자세히 살펴보려면 [특성 메모](#attribute-notes) 섹션에서 [connectionString 설정](#setting-connectionstring)을 참조하세요. |
| *settingsClassName*<br/>*settingsMethodName* | 문자열<br/>문자열 | *해당 없음* | *이러한 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용하여 연결 문자열을 제공합니다. *settingsClassName* 은 *settingsMethodName* 에 지정된 메서드를 포함하는 정규화된 어셈블리 클래스 이름이어야 합니다.<br/><br/>*settingsMethodName* 에 의해 지정된 메서드는 **문자열** 의 반환 형식과 함께 공개, 정적 및 무효(매개 변수를 허용하지 않음)여야 합니다. 이 메서드는 실제 연결 문자열을 반환합니다. |
| *loggingClassName*<br/>*loggingMethodName* | 문자열<br/>문자열 | *해당 없음* | *이러한 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>이러한 특성을 사용하여 StackExchange.Redis의 로그와 함께 세션 상태/출력 캐시의 로그를 제공하여 애플리케이션을 디버그할 수 있습니다. *loggingClassName* 은 *loggingMethodName* 에 지정된 메서드를 포함하는 정규화된 어셈블리 클래스 이름이어야 합니다.<br/><br/>*loggingMethodName* 에 의해 지정된 메서드는 **System.IO.TextWriter** 의 반환 형식과 함께 공개, 정적 및 무효(매개 변수를 사용하지 않음)여야 합니다. |
| *applicationName* | 문자열 | 현재 프로세스의 모듈 이름 또는 "/" | *SessionStateProvider에만 해당*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>Redis 캐시에서 사용할 앱 이름 접두사입니다. 고객은 다양한 용도로 동일한 Redis 캐시를 사용할 수 있습니다. 세션 키가 충돌하지 않도록 하기 위해 애플리케이션 이름 앞에 접두사를 붙일 수 있습니다. |
| *throwOnError* | boolean | true | *SessionStateProvider에만 해당*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>오류가 발생할 때 예외를 throw할지 여부를 결정합니다.<br/><br/>*throwOnError* 에 대한 자세한 내용은 [특성 정보](#attribute-notes) 섹션에서 [*throwOnError* 에 대한 정보](#notes-on-throwonerror)를 참조하세요. |
| *retryTimeoutInMilliseconds* | 양의 정수 | 5,000 | *SessionStateProvider에만 해당*<br/>*이 특성은 web.config 또는 AppSettings를 통해서만 지정할 수 있습니다.*<br/><br/>작업이 실패하는 경우 다시 시도하는 시간입니다. 이 값이 *operationTimeoutInMilliseconds* 보다 작은 경우 공급자는 다시 시도하지 않습니다.<br/><br/>*retryTimeoutInMilliseconds* 에 대한 자세한 내용은 [특성 정보](#attribute-notes) 섹션에서 [*retryTimeoutInMilliseconds* 에 대한 정보](#notes-on-retrytimeoutinmilliseconds)를 참조하세요. |
| *redisSerializerType* | 문자열 | *해당 없음* | Microsoft.Web.Redis를 구현하는 클래스의 정규화된 어셈블리 형식 이름을 지정합니다. 값을 직렬화 및 역직렬화하는 사용자 지정 논리를 포함하는 직렬 변환기입니다. 자세한 내용은 [특성 정보](#attribute-notes) 섹션의 [*redisSerializerType* 정보](#about-redisserializertype)를 참조하세요. |

## <a name="attribute-notes"></a>특성 정보

### <a name="setting-connectionstring"></a>*connectionString* 설정

*connectionString* 의 값은 이러한 문자열이 AppSettings에 있는 경우 AppSettings에서 실제 연결 문자열을 페치하는 키로 사용됩니다. AppSettings 내에서 찾을 수 없는 경우 *connectionString* 의 값은 web.config **ConnectionString** 섹션(해당 섹션이 있는 경우)에서 실제 연결 문자열을 페치하는 키로 사용됩니다. AppSettings 또는 web.config **ConnectionString** 섹션에 연결 문자열이 없는 경우 StackExchange.Redis.ConnectionMultiplexer를 만들 때 *connectionString* 의 리터럴 값이 연결 문자열로 사용됩니다.

다음 예제에서는 *connectionString* 이 사용되는 방법을 보여 줍니다.

#### <a name="example-1"></a>예제 1

```xml
<connectionStrings>
    <add name="MyRedisConnectionString" connectionString="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</connectionStrings>
```

`web.config`에서 실제 값 대신 위의 키를 매개 변수 값으로 사용합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-2"></a>예제 2

```xml
<appSettings>
    <add key="MyRedisConnectionString" value="mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False" />
</appSettings>
```

`web.config`에서 실제 값 대신 위의 키를 매개 변수 값으로 사용합니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "MyRedisConnectionString"/>
    </providers>
</sessionState>
```

#### <a name="example-3"></a>예제 3

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvide"
             name = "MySessionStateStore"
             connectionString = "mycache.redis.cache.windows.net:6380,password=actual access key,ssl=True,abortConnect=False"/>
    </providers>
</sessionState>
```

### <a name="notes-on-throwonerror"></a>*throwOnError* 에 대한 참고 사항

현재 세션 작업 중에 오류가 발생하는 경우 세션 상태 제공자가 예외를 발생시킵니다. 예외를 발생시키면 애플리케이션이 종료됩니다.

이 동작은 기존 ASP.NET 세션 상태 제공자 사용자의 기대 사항을 지원하는 방식으로 수정되었으며, 예외에 대한 작업 또한 수행할 수 있도록 합니다. 다른 ASP.NET 세션 상태 제공자와 일치하는 오류가 발생하는 경우 기본 동작은 여전히 예외를 발생시킵니다. 기존 코드는 이전과 동일하게 작동해야 합니다.

*throwOnError* 를 **false** 로 설정하는 경우 오류가 발생할 때 예외를 발생시키는 대신 자동으로 실패합니다. 오류가 있었는지 확인하고 오류가 있는 경우 예외를 확인하고, 정적 속성 *Microsoft.Web.Redis.RedisSessionStateProvider.LastException* 을 확인합니다.

### <a name="notes-on-retrytimeoutinmilliseconds"></a>*retryTimeoutInMilliseconds* 에 대한 참고 사항

*retryTimeoutInMilliseconds* 설정은 네트워크 결함 또는 다른 항목으로 인한 실패 시 세션 작업을 다시 시도해야 하는 경우를 간소화하는 몇 가지 논리를 제공합니다. *retryTimeoutInMilliseconds* 설정을 사용하여 재시도 시간 제한을 제어하거나 재시도를 완전히 옵트아웃할 수도 있습니다.

*retryTimeoutInMilliseconds* 를 숫자(예: 2000)로 설정하는 경우, 세션 작업이 실패하면 이를 오류로 처리하기 전에 2000밀리초 동안 다시 시도합니다. 따라서 세션 상태 제공자가 이 재시도 논리를 적용하도록 하려면 시간 제한을 구성하면 됩니다. 첫 번째 다시 시도는 20밀리초 후에 수행되며, 네트워크 결함이 발생하는 경우 대부분의 경우에서 충분합니다. 그런 다음 시간이 초과될 때까지 매초마다 재시도합니다. 시간 제한 직후에는 한 번 더 재시도하여 시간 제한을 최대 1초까지 단축하지 않도록 합니다.

다시 시도해야 한다고 생각되지 않거나 또는 다시 시도 논리를 직접 처리하려는 경우 *retryTimeoutInMilliseconds* 를 0으로 설정합니다. 예를 들어 애플리케이션과 동일한 머신에서 Redis 서버를 실행하는 경우 재시도를 원하지 않을 수 있습니다.

### <a name="about-redisserializertype"></a>*redisSerializerType* 정보

Redis에 값을 저장하는 serialization은 기본값으로 **BinaryFormatter** 클래스에서 제공하는 이진 형식으로 수행됩니다. *redisSerializerType* 을 사용하여 **Microsoft.Web.Redis.ISerializer** 를 구현하는 클래스의 정규화된 어셈블리 형식 이름을 지정하고, 값을 직렬화 및 역직렬화할 수 있는 사용자 지정 논리를 사용합니다. 예를 들어 다음은 JSON.NET을 사용하는 Json 직렬 변환기 클래스입니다.

```cs
namespace MyCompany.Redis
{
    public class JsonSerializer : ISerializer
    {
        private static JsonSerializerSettings _settings = new JsonSerializerSettings() { TypeNameHandling = TypeNameHandling.All };

        public byte[] Serialize(object data)
        {
            return Encoding.UTF8.GetBytes(JsonConvert.SerializeObject(data, _settings));
        }

        public object Deserialize(byte[] data)
        {
            if (data == null)
            {
                return null;
            }
            return JsonConvert.DeserializeObject(Encoding.UTF8.GetString(data), _settings);
        }
    }
}
```

이 클래스가 이름이 **MyCompanyDll** 인 어셈블리에 정의된 것으로 가정하여 *redisSerializerType* 매개 변수를 사용하도록 설정할 수 있습니다.

```xml
<sessionState mode="Custom" customProvider="MySessionStateStore">
    <providers>
        <add type = "Microsoft.Web.Redis.RedisSessionStateProvider"
             name = "MySessionStateStore"
             redisSerializerType = "MyCompany.Redis.JsonSerializer,MyCompanyDll"
             ... />
    </providers>
</sessionState>
```

## <a name="output-cache-directive"></a>출력 캐시 지시문

출력을 캐시하고자 하는 각 페이지에 OutputCache 지시문을 추가합니다.

```xml
<%@ OutputCache Duration="60" VaryByParam="*" %>
```

이전 예제에서는 캐시된 페이지 데이터가 캐시에 60초 동안 머물게 되며, 각 매개 변수 조합에 따라 페이지의 다른 버전이 캐시됩니다. 출력 캐시 지시문에 대한 자세한 내용은 [@OutputCache](/previous-versions/dotnet/netframework-4.0/hdxfb6cy(v=vs.100))을 참조하세요.

이 단계를 수행하고 나면, 애플리케이션은 Redis 출력 캐시 공급자를 사용하도록 구성됩니다.

## <a name="third-party-output-cache-providers"></a>타사 출력 캐시 공급자

* [NCache](https://www.alachisoft.com/blogs/how-to-use-a-distributed-cache-for-asp-net-output-cache/)
* [Apache Ignite](https://apacheignite-net.readme.io/docs/aspnet-output-caching)

## <a name="next-steps"></a>다음 단계

[Azure Cache for Redis에 대한 ASP.NET 세션 상태 제공자](cache-aspnet-session-state-provider.md)를 살펴봅니다.
