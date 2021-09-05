---
title: Azure Cache for Redis 사용에서 TLS 1.0과 1.1 제거
description: Azure Cache for Redis와 통신할 때 애플리케이션에서 TLS 1.0과 1.1을 제거하는 방법 알아보기
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: f9c6f8cc3c973d3172166514815018979f744173
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536565"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Azure Cache for Redis 사용에서 TLS 1.0과 1.1 제거

업계 전반에서 TLS(전송 계층 보안) 버전 1.2 이상을 전용하자는 움직임이 있습니다. TLS 1.0과 1.1은 BEAST 및 POODLE과 같은 공격에 취약하며, 다른 CVE(Common Vulnerabilities and Exposures) 취약점이 있는 것으로 알려져 있습니다. 또한 PCI(Payment Card Industry) 규정 준수 표준에서 권장하는 최신 암호화 방법 및 암호화 제품군을 지원하지 않습니다. [TLS 보안 블로그](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/)에는 이러한 취약점 중 일부가 자세히 설명되어 있습니다.

이러한 노력의 일환으로 Azure Cache for Redis를 다음과 같이 변경할 예정입니다.

* **1단계:** 새로 만든 캐시 인스턴스(이전의 TLS 1.0)에 대해 기본 최소 TLS 버전을 1.2로 구성합니다. 이 시점에 기존 캐시 인스턴스는 업데이트되지 않습니다. 이전 버전과의 호환성을 위해 Azure Portal 또는 다른 관리 Api를 계속 사용하여 [최소 TLS 버전](cache-configure.md#access-ports)을 1.0 또는 1.1로 변경할 수 있습니다.
* **2단계:** TLS 1.1 및 TLS 1.0 지원은 중단됩니다. 이러한 변경 후 애플리케이션은 TLS 1.2 이상을 사용하여 캐시와 통신해야 합니다. Azure Cache for Redis 서비스는 TLS 1.2 이상만 지원하도록 마이그레이션하는 동안 사용할 수 있습니다.

  > [!NOTE]
  > 2단계는 코로나19로 인해 연기되었습니다. 지금 이 변경에 대한 계획을 시작하고 TLS 1.2 이상을 지원하도록 클라이언트를 사전에 업데이트하는 것이 좋습니다. 
  >

이 변경의 일환으로, 안전하지 않은 이전 암호 제품군에 대한 지원도 제거합니다. 캐시가 최소 TLS 1.2로 구성된 경우 지원되는 암호화 제품군은 다음 제품군으로 제한됩니다.

* TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384_P384
* TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256_P256

이 문서는 이전 TLS 버전에 대한 종속성을 감지하여 애플리케이션에서 제거하는 방법에 대한 일반적인 지침을 제공합니다.

이러한 변경 사항이 적용되는 날짜는 다음과 같습니다.

| 클라우드                | 1단계 시작 날짜 | 2단계 시작 날짜         |
|----------------------|--------------------|----------------------------|
| Azure(글로벌)       |  2020년 1월 13일  | 코로나19로 인해 연기 됨  |
| Azure Government     |  2020년 3월 13일    | 코로나19로 인해 연기 됨  |
| Azure Germany        |  2020년 3월 13일    | 코로나19로 인해 연기 됨  |
| Azure China 21Vianet |  2020년 3월 13일    | 코로나19로 인해 연기 됨  |

> [!NOTE]
> 2단계는 코로나19로 인해 연기되었습니다. 이 문서는 특정 날짜가 설정될 때 업데이트됩니다.
>

## <a name="check-whether-your-application-is-already-compliant"></a>애플리케이션이 이미 규격에 맞는지 확인

테스트 또는 스테이징 캐시에서 **최소 TLS 버전** 값을 TLS 1.2로 설정한 다음 테스트를 실행하여 애플리케이션이 TLS 1.2에서 작동하는지 확인할 수 있습니다. **최소 TLS 버전** 설정은 Azure Portal에서 캐시 인스턴스의 [고급 설정](cache-configure.md#advanced-settings)에 있습니다.  변경한 후에도 애플리케이션이 예상대로 계속 작동하면 아마도 규격에 맞는 것입니다. TLS 1.2가 Azure Cache for Redis에 연결할 수 있도록 애플리케이션에서 사용하는 Redis 클라이언트 라이브러리를 구성해야 할 수 있습니다.

## <a name="configure-your-application-to-use-tls-12"></a>TLS 1.2를 사용하도록 애플리케이션 구성

대부분의 애플리케이션은 Redis 클라이언트 라이브러리를 사용하여 캐시와의 통신을 처리합니다. 다음은 널리 사용되는 클라이언트 라이브러리 중 일부를, 다양한 프로그래밍 언어 및 프레임워크에서, TLS 1.2를 사용하도록 구성하는 지침입니다.

### <a name="net-framework"></a>.NET Framework

Redis .NET 클라이언트는 .NET Framework 4.5.2 또는 이전 버전에서 기본적으로 가장 오래된 TLS 버전을 사용하고 .NET Framework 4.6 또는 이상 버전에서 최신 TLS 버전을 사용합니다.클라이언트 컴퓨터 이전 버전의 .NET Framework를 사용하는 경우 TLS 1.2를 수동으로 사용하도록 설정합니다.

* **StackExchange.Redis:** 연결 문자열에서 `ssl=true`와 `sslprotocols=tls12`를 설정합니다.
* **ServiceStack.Redis:** [ServiceStack.Redis](https://github.com/ServiceStack/ServiceStack.Redis#servicestackredis-ssl-support) 지침을 따르고, 최소 ServiceStack.Redis v5.6 이상이 필요합니다.

### <a name="net-core"></a>.NET Core

Redis .NET Core 클라이언트는 OS 자체에 따라 달라지는 OS 기본 TLS 버전으로 기본 설정됩니다. 

OS 버전 및 적용된 패치에 따라 유효한 기본 TLS 버전은 다를 수 있습니다. 자세한 내용은 [여기](/dotnet/framework/network-programming/#support-for-tls-12)를 참조하세요.

그러나 이전 OS를 사용하고 있는 경우 클라이언트를 통해 기본 설정된 TLS 버전을 수동으로 구성하는 것이 좋습니다.


### <a name="java"></a>Java

Redis Java 클라이언트는 Java 6 또는 이전 버전에서 TLS 1.0을 사용합니다. 캐시에서 TLS 1.0이 비활성화되면 Jedis, Lettuce 및 Redisson이 Azure Cache for Redis에 연결할 수 없습니다. 새 TLS 버전을 사용하도록 Java 프레임워크를 업그레이드하십시오.

Java 7의 경우 Redis 클라이언트는 기본적으로 TLS 1.2를 사용하지 않지만 그에 맞게 구성할 수 있습니다. Jedis를 사용하면 다음 코드 조각을 사용하여 기본 TLS 설정을 지정할 수 있습니다.

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

Lettuce 및 Redisson 클라이언트는 아직 TLS 버전 지정을 지원하지 않습니다. 캐시에서 TLS 1.2 연결만 수락하는 경우에는 중단됩니다. 이러한 클라이언트에 대한 수정 사항을 검토 중이므로 해당 패키지에서 지원을 포함하는 업데이트된 버전이 있는지 확인하십시오.

Java 8에서는 TLS 1.2가 기본적으로 사용되며 대부분의 경우 클라이언트 구성을 업데이트하지 않아도 됩니다. 안전을 위해 애플리케이션을 테스트하십시오.

### <a name="nodejs"></a>Node.js

Node Redis 및 IORedis는 기본적으로 TLS 1.2를 사용합니다.

### <a name="php"></a>PHP

#### <a name="predis"></a>Predis
 
* PHP 7 이전 버전: Predis는 TLS 1.0만 지원합니다. 이 버전은 TLS 1.2에서 작동하지 않습니다. TLS 1.2를 사용하려면 업그레이드해야 합니다.
 
* PHP 7.0 ~ PHP 7.2.1: Predis는 기본적으로 TLS 1.0 또는 1.1만 사용합니다. 다음 해결 방법을 사용하여 TLS 1.2를 사용할 수 있습니다. 클라이언트 인스턴스를 만들 때 TLS 1.2를 지정합니다.

  ``` PHP
  $redis=newPredis\Client([
      'scheme'=>'tls',
      'host'=>'host',
      'port'=>6380,
      'password'=>'password',
      'ssl'=>[
          'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
      ],
  ]);
  ```

* PHP 7.3 이상 버전: Predis는 최신 TLS 버전을 사용합니다.

#### <a name="phpredis"></a>PhpRedis

PhpRedis는 PHP 버전에서 TLS를 지원하지 않습니다.

### <a name="python"></a>Python

Redis-py는 기본적으로 TLS 1.2를 사용합니다.

### <a name="go"></a>이동

Redigo는 기본적으로 TLS 1.2를 사용합니다.

## <a name="additional-information"></a>추가 정보

- [Azure Cache for Redis를 구성하는 방법](cache-configure.md)