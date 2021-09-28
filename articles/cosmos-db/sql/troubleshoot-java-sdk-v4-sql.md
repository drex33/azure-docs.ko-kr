---
title: Azure Cosmos DB Java SDK v4 진단 및 문제 해결
description: 클라이언트 쪽 로깅 기타 타사 도구 등의 기능을 사용하여 Java SDK v4의 Azure Cosmos DB 문제를 파악, 진단 및 해결합니다.
author: anfeldma-ms
ms.service: cosmos-db
ms.date: 06/11/2020
ms.author: anfeldma
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.custom: devx-track-java
ms.openlocfilehash: 54f0796d52d150db272e00c5cb66aa0c68a2e51c
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/28/2021
ms.locfileid: "129090992"
---
# <a name="troubleshoot-issues-when-you-use-azure-cosmos-db-java-sdk-v4-with-sql-api-accounts"></a>SQL API 계정으로 Azure Cosmos DB Java SDK v4를 사용하는 경우 발생하는 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

> [!div class="op_single_selector"]
> * [Java SDK v4](troubleshoot-java-sdk-v4-sql.md)
> * [Async Java SDK v2](troubleshoot-java-async-sdk.md)
> * [.NET](troubleshoot-dot-net-sdk.md)
> 

> [!IMPORTANT]
> 이 문서에서는 Azure Cosmos DB Java SDK v4에 대한 문제 해결만 다룹니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 [릴리스 정보](sql-api-sdk-java-v4.md), [Maven 리포지토리](https://mvnrepository.com/artifact/com.azure/azure-cosmos) 및 [성능 팁](performance-tips-java-sdk-v4-sql.md)을 참조하세요. 현재 v4보다 이전 버전을 사용하는 경우 v4로 업그레이드하는 데 도움이 필요하면 [Azure Cosmos DB Java SDK v4로 마이그레이션](migrate-java-v4-sdk.md) 가이드를 참조하세요.
>

이 문서에서는 Azure Cosmos DB SQL API 계정으로 Azure Cosmos DB Java SDK v4를 사용할 때 일반적인 문제, 해결, 진단 단계 및 도구를 설명합니다.
Azure Cosmos DB Java SDK v4는 Azure Cosmos DB SQL API에 액세스하기 위한 클라이언트 쪽 논리적 표현을 제공합니다. 이 문서에서는 문제가 발생하는 경우 사용자에게 도움이 되는 도구 및 방법을 설명합니다.

이 목록을 사용하여 시작합니다.

* 이 문서의 [일반적인 문제 및 해결 방법] 섹션을 살펴봅니다.
* [GitHub의 오픈 소스](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos)에서 제공되는 Azure Cosmos DB 중앙 리포지토리에서 Java SDK를 살펴봅니다. 여기에는 능동적으로 모니터링되는 [문제 섹션](https://github.com/Azure/azure-sdk-for-java/issues)이 있습니다. 해결 방법이 있는 유사한 문제가 이미 제출되었는지 확인합니다. 한 가지 유용한 팁은 *cosmos:v4-item* 태그를 기준으로 문제를 필터링하는 것입니다.
* Azure Cosmos DB Java SDK v4에 대한 [성능 팁](performance-tips-java-sdk-v4-sql.md)을 검토하고 제안된 사례를 따릅니다.
* 솔루션을 찾지 못한 경우 이 문서의 나머지 부분을 읽어봅니다. 그런 후, [GitHub 문제](https://github.com/Azure/azure-sdk-for-java/issues)를 제출합니다. GitHub 문제에 태그를 추가하는 옵션이 있는 경우 *cosmos:v4-item* 태그를 추가합니다.

### <a name="retry-logic"></a>재시도 논리 <a id="retry-logics"></a>
IO 실패 시 Cosmos DB SDK는 SDK에서 다시 시도할 수 있는 경우 실패한 작업을 다시 시도합니다. 모든 실패 작업에 대해 다시 시도를 수행하는 것이 좋습니다. 특히 쓰기 실패는 반드시 처리/다시 시도해야 합니다. 재시도 논리는 지속적으로 개선되므로 최신 SDK를 사용하는 것이 좋습니다.

1. 읽기 및 쿼리 IO 오류의 경우 최종 사용자에게 표시하지 않고 SDK에서 다시 시도합니다.
2. 쓰기(Create, Upsert, Replace, Delete)는 결과가 '달라지므로' SDK에서 실패한 쓰기 작업을 무조건 다시 시도할 수는 없습니다. 사용자의 애플리케이션 논리에서 오류를 처리하고 다시 시도해야 합니다.
3. [SDK 가용성 문제 해결](troubleshoot-sdk-availability.md)에서는 다중 지역 Cosmos DB 계정의 재시도에 대해 설명합니다.

### <a name="retry-design"></a>다시 시도 디자인

응용 프로그램은 재시도가 도움이 되지 않는 알려진 문제가 아닌 한 모든 예외에서 재시도하도록 설계해야 합니다. 예를 들어, 애플리케이션은 408 요청 시간 초과 시 재시도해야 합니다. 이 시간 초과는 일시적일 수 있으므로 재시도가 성공할 수 있습니다. 애플리케이션은 400에서 재시도하지 않아야 합니다. 이는 일반적으로 먼저 해결해야 하는 요청에 문제가 있음을 의미합니다. 400에서 재시도해도 문제가 해결되지 않으며 다시 시도하면 동일한 오류가 발생합니다. 아래 표는 알려진 실패와 재시도할 실패를 보여줍니다.

## <a name="common-error-status-codes"></a>일반적인 오류 상태 코드 <a id="error-codes"></a>

| 상태 코드 | 다시 시도 가능 | 설명 | 
|----------|-------------|-------------|
| 400 | No | 잘못된 요청(예: 잘못된 json, 잘못된 헤더, 헤더의 잘못된 파티션 키)| 
| 401 | No | [권한 없음](troubleshoot-unauthorized.md) | 
| 403 | No | [Forbidden](troubleshoot-forbidden.md) |
| 404 | No | [리소스를 찾을 수 없습니다](troubleshoot-not-found.md) |
| 408 | Yes | [요청 시간이 초과되었습니다](troubleshoot-request-timeout-java-sdk-v4-sql.md) |
| 409 | No | 충돌 실패는 쓰기 작업에서 리소스에 제공된 ID가 기존 리소스에 의해 사용된 경우입니다. ID는 동일한 파티션 키 값을 가진 모든 문서 내에서 고유해야 하므로 이 문제를 해결하려면 리소스에 다른 ID를 사용하십시오. |
| 410 | Yes | 사라진 예외(SLA를 위반해서는 안 되는 일시적인 오류) |
| 412 | No | 필수 조건 실패는 작업이 서버에서 사용 가능한 버전과 다른 eTag를 지정한 경우입니다. 낙관적 동시성 오류입니다. 리소스의 최신 버전을 확인하고 요청의 eTag를 업데이트한 후에 요청을 다시 시도해야 합니다.
| 413 | No | [요청 엔터티가 너무 큼](../concepts-limits.md#per-item-limits) |
| 429 | Yes | 429에서 다시 시도하는 것이 안전합니다. 이는 [요청이 너무 많은 경우](troubleshoot-request-rate-too-large.md) 링크를 따라가면 피할 수 있습니다.|
| 449 | Yes | 쓰기 작업에서만 발생하는 일시적인 오류이며 다시 시도하는 것이 안전합니다. 이는 너무 많은 동시 작업이 Cosmos DB에서 동일한 개체를 업데이트하려고 하는 디자인 문제를 나타낼 수 있습니다. |
| 500 | Yes | 예기치 않은 서비스 오류로 인해 작업이 실패했습니다. [Azure 지원 문제](https://aka.ms/azure-support)를 제출하여 지원팀에 문의하세요. |
| 503 | Yes | [서비스를 사용할 수 없음](troubleshoot-service-unavailable-java-sdk-v4-sql.md) |

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>일반적인 문제 및 해결 방법

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>네트워크 문제, Netty 읽기 시간 제한 오류, 낮은 처리량, 높은 대기 시간

#### <a name="general-suggestions"></a>일반 제안
최상의 성능을 얻으려면:
* 앱이 Azure Cosmos DB 계정과 동일한 지역에서 실행되고 있는지 확인합니다. 
* 앱이 실행되는 호스트에서 CPU 사용량을 확인합니다. CPU 사용량이 50% 이상인 경우 더 높은 구성을 사용해서 호스트에서 앱을 실행합니다. 또는 더 많은 컴퓨터에서 부하를 분산할 수 있습니다.
    * Azure Kubernetes Service에서 애플리케이션이 실행 중인 경우 [Azure Monitor를 사용하여 CPU 사용률을 모니터링](../../azure-monitor/containers/container-insights-analyze.md)할 수 있습니다.

#### <a name="connection-throttling"></a>연결 제한
연결 제한은 [호스트 컴퓨터의 연결 제한] 또는 [Azure SNAT(PAT) 포트 고갈] 중 하나로 인해 발생할 수 있습니다.

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>호스트 컴퓨터의 연결 제한
일부 Linux 시스템(예: 'Red Hat')에는 열려 있는 파일의 총 수에 상한이 있습니다. Linux의 소켓은 파일로 구현되므로 이 숫자는 총 연결 수도 제한합니다.
다음 명령을 실행합니다.

```bash
ulimit -a
```
"nofile"로 식별되는 허용되는 열린 파일의 최대 수는 연결 풀 크기의 두 배 이상이어야 합니다. 자세한 내용은 Azure Cosmos DB Java SDK v4 [성능 팁](performance-tips-java-sdk-v4-sql.md)을 참조하세요.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT(PAT) 포트 고갈

공용 IP 주소 없이 앱이 Azure Virtual Machines에 배포되는 경우 기본적으로 [Azure SNAT 포트](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports)는 VM 외부의 모든 엔드포인트에 대한 연결을 설정하는 데 사용됩니다. VM에서 Azure Cosmos DB 엔드포인트로 허용되는 연결 수는 [Azure SNAT 구성](../../load-balancer/load-balancer-outbound-connections.md#preallocatedports)으로 제한됩니다.

 Azure SNAT 포트는 VM에 개인 IP 주소 및 VM에서 공용 IP 주소에 연결하려고 하는 프로세스가 있는 경우에만 사용됩니다. Azure SNAT 제한을 피하는 두 가지 해결 방법이 있습니다.

* Azure Virtual Machines 가상 네트워크의 서브넷에 Azure Cosmos DB 서비스 엔드포인트를 추가합니다. 자세한 내용은 [Azure Virtual Network 서비스 엔드포인트](../../virtual-network/virtual-network-service-endpoints-overview.md)를 참조하세요. 

    서비스 엔드포인트를 사용하도록 설정한 경우 요청이 더 이상 공용 IP에서 Azure Cosmos DB로 전송되지 않습니다. 대신 가상 네트워크 및 서브넷 ID가 전송됩니다. 공용 IP만 허용되는 경우 이 변경 내용으로 인해 방화벽이 삭제될 수 있습니다. 방화벽을 사용하는 경우 서비스 엔드포인트를 사용하도록 설정하면 [Virtual Network ACL](/previous-versions/azure/virtual-network/virtual-networks-acl)을 사용하여 방화벽에 서브넷을 추가합니다.
* Azure VM에 공용 IP를 할당합니다.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>서비스에 연결할 수 없음 - 방화벽
``ConnectTimeoutException``은 SDK에서 서비스에 연결할 수 없음을 나타냅니다.
직접 모드를 사용하는 경우 다음과 유사한 오류가 발생할 수 있습니다.
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

앱 컴퓨터에서 방화벽이 실행 중인 경우 직접 모드에서 사용하는 10,000 ~ 20,000 포트 범위를 엽니다.
또한 [호스트 컴퓨터의 연결 제한](#connection-limit-on-host)을 따릅니다.

#### <a name="http-proxy"></a>HTTP 프록시

HTTP 프록시를 사용하는 경우 SDK `ConnectionPolicy`에서 구성된 연결 수를 지원할 수 있는지 확인합니다.
그렇지 않으면 연결 문제가 발생할 수 있습니다.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>잘못된 코딩 패턴: Netty IO 스레드 차단

SDK는 [Netty](https://netty.io/) IO 라이브러리를 사용하여 Azure Cosmos DB와 통신합니다. SDK는 Async API를 포함하며 Netty의 비차단 IO API를 사용합니다. SDK의 IO 작업은 IO Netty 스레드에서 수행됩니다. IO Netty 스레드의 수는 앱 머신의 CPU 코어 수와 동일하게 구성됩니다. 

Netty IO 스레드는 비차단 Netty IO 작업에만 사용해야 합니다. SDK는 앱의 코드에 대한 Netty IO 스레드 중 하나에서 API 호출 결과를 반환합니다. 앱이 Netty 스레드에서 결과를 수신한 후 오래 지속되는 작업을 수행하는 경우 SDK는 해당 내부 IO 작업을 수행하기에 충분한 IO 스레드가 없을 수도 있습니다. 이러한 앱 코딩으로 인해 낮은 처리량, 높은 대기 시간 및 `io.netty.handler.timeout.ReadTimeoutException` 실패가 발생할 수 있습니다. 해결 방법은 작업에 시간이 걸리는 것을 알고 있는 경우 스레드를 전환하는 것입니다.

예를 들어 컨테이너에 항목을 추가하는 다음 코드 조각을 살펴봅니다(데이터베이스 및 컨테이너 설정에 대한 지침은 [여기](create-sql-api-java.md) 참조). Netty 스레드에서 수 밀리초 이상 소요되는 오래 지속되는 작업을 수행할 수 있습니다. 이 경우 결과적으로 IO 작업을 처리하기 위한 Netty IO 스레드가 없는 상태가 될 수 있습니다. 따라서 ReadTimeoutException 오류가 발생합니다.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-readtimeout"></a>Java SDK V4(Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootNeedsSchedulerAsync)]

해결 방법은 시간이 걸리는 작업을 수행하는 스레드를 변경하는 것입니다. 앱에 대한 스케줄러의 싱글톤 인스턴스를 정의합니다.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-scheduler"></a>Java SDK V4(Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootCustomSchedulerAsync)]

계산이 많은 작업 또는 차단 IO 작업과 같이 시간이 걸리는 작업을 수행해야 할 수 있습니다. 이 경우 `.publishOn(customScheduler)` API를 사용하여 `customScheduler`가 제공하는 작업자로 스레드를 전환합니다.

### <a name="java-sdk-v4-maven-comazureazure-cosmos-async-api"></a><a id="java4-apply-custom-scheduler"></a>Java SDK V4(Maven com.azure::azure-cosmos) Async API

[!code-java[](~/azure-cosmos-java-sql-api-samples/src/main/java/com/azure/cosmos/examples/documentationsnippets/async/SampleDocumentationSnippetsAsync.java?name=TroubleshootPublishOnSchedulerAsync)]

`publishOn(customScheduler)`을 사용하여 Netty IO 스레드를 해제하고 사용자 지정 스케줄러에서 제공한 사용자 고유의 사용자 지정 스레드로 전환합니다. 이와 같이 수정하면 문제가 해결됩니다. 이제 더 이상 `io.netty.handler.timeout.ReadTimeoutException` 오류가 발생하지 않습니다.

### <a name="request-rate-too-large"></a>너무 많은 요청 빈도
이 오류는 서버 쪽 오류입니다. 프로비전된 처리량을 다 사용했음을 나타냅니다. 나중에 다시 시도하세요. 이 오류가 자주 발생하는 경우 컬렉션 처리량을 늘리는 것이 좋습니다.

* **getRetryAfterInMilliseconds 간격으로 백오프 구현**

    성능 테스트 중에는 작은 비율의 요청이 제한될 때까지 로드를 늘려야 합니다. 제한될 경우 클라이언트 애플리케이션은 서버에서 지정한 재시도 간격 제한을 백오프해야 합니다. 백오프를 통해 재시도 간 기간을 최소화할 수 있습니다.

### <a name="error-handling-from-java-sdk-reactive-chain"></a>Java SDK 사후 체인의 오류 처리

Cosmos DB Java SDK의 오류 처리는 클라이언트의 응용 프로그램 논리에 제공 되는 경우에 중요 합니다. 여러 시나리오에서 사용할 수 있는 [재 행위자-핵심 프레임 워크](https://projectreactor.io/docs/core/release/reference/#error.handling) 에서 제공 하는 다양 한 오류 처리 메커니즘이 있습니다. 고객은 이러한 오류 처리 연산자를 자세히 이해 하 고 재시도 논리 시나리오에 적합 한 방법을 사용 하는 것이 좋습니다.

> [!IMPORTANT]
> [`onErrorContinue()`](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#onErrorContinue-java.util.function.BiConsumer-)연산자는 모든 시나리오에서 지원 되지 않으므로 사용 하지 않는 것이 좋습니다.
> 는 `onErrorContinue()` 반응 체인의 동작을 명확 하 게 만들 수 있는 전문 운영자입니다. 다운스트림 연산자가 아닌 업스트림에서 작동 하 고, 특정 연산자 지원이 작동 해야 하며, 범위는 예기치 않은 동작을 예상 하지 않는 라이브러리 코드로 쉽게 업스트림을 전파할 수 있습니다 (의도 하지 않은 동작이 발생 함). [](https://projectreactor.io/docs/core/release/api/reactor/core/publisher/Flux.html#onErrorContinue-java.util.function.BiConsumer-) `onErrorContinue()` 이 특수 연산자에 대 한 자세한 내용은 설명서를 참조 하십시오.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Azure Cosmos DB 에뮬레이터 연결 오류

Azure Cosmos DB 에뮬레이터 HTTPS 인증서는 자체 서명입니다. SDK를 에뮬레이터와 함께 사용하려면 에뮬레이터 인증서를 Java TrustStore로 가져와야 합니다. 자세한 내용은 [Azure Cosmos DB 에뮬레이터 인증서 내보내기](../local-emulator-export-ssl-certificates.md)를 참조하세요.

### <a name="dependency-conflict-issues"></a>종속성 충돌 문제

Azure Cosmos DB Java SDK는 많은 종속성을 가져옵니다. 일반적으로 프로젝트 종속성 트리에 Azure Cosmos DB Java SDK가 종속된 이전 버전의 아티팩트가 포함된 경우 애플리케이션을 실행할 때 예기치 않은 오류가 발생할 수 있습니다. 애플리케이션에서 왜 예기치 않게 예외가 throw되는지를 디버깅하는 경우 종속성 트리가 실수로 이전 버전의 Azure Cosmos DB Java SDK 종속성을 하나 이상 가져오지 않는지 다시 한 번 확인하는 것이 좋습니다.

이러한 문제에 대한 해결 방법은 이전 버전을 가져오는 프로젝트 종속성을 식별하고 이전 버전의 전이적 종속성을 제외하고 Azure Cosmos DB Java SDK가 최신 버전을 가져올 수 있도록 하는 것입니다.

Azure Cosmos DB Java SDK가 종속된 이전 버전을 가져오는 프로젝트 종속성을 식별하려면 프로젝트 pom.xml 파일에 대해 다음 명령을 실행합니다.
```bash
mvn dependency:tree
```
자세한 내용은 [maven 종속성 트리 가이드](https://maven.apache.org/plugins-archives/maven-dependency-plugin-2.10/examples/resolving-conflicts-using-the-dependency-tree.html)를 참조하세요.

이전 버전에 종속된 프로젝트 종속성을 아는 경우, 아래 예와 같이 pom 파일에서 해당 lib에 대한 종속성을 수정하고 전이적 종속성을 제외할 수 있습니다(*reactor-core* 는 오래된 종속성이라고 가정).

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-reactor}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-reactor}</artifactId>
  <version>${version-of-lib-which-brings-in-reactor}</version>
  <exclusions>
    <exclusion>
      <groupId>io.projectreactor</groupId>
      <artifactId>reactor-core</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

자세한 내용은 [전이적 종속성 제외 가이드](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html)를 참조하세요.


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>클라이언트 SDK 로깅 사용

Azure Cosmos DB Java SDK v4는 log4j 및 logback과 같은 인기 있는 로깅 프레임워크로 로깅을 지원하는 로깅 외관으로 SLF4j를 사용합니다.

예를 들어 로깅 프레임워크로 log4j를 사용하려는 경우 다음 라이브러리를 Java 클래스 경로에 추가합니다.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

log4j 구성도 추가합니다.
```
# this is a sample log4j configuration

# Set root logger level to INFO and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.azure.cosmos=INFO
#log4j.category.io.netty=OFF
#log4j.category.io.projectreactor=OFF
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

자세한 내용은 [sfl4j 로깅 설명서](https://www.slf4j.org/manual.html)를 참조하세요.

## <a name="os-network-statistics"></a><a name="netstats"></a>OS 네트워크 통계
netstat 명령을 실행하여 `ESTABLISHED` 및 `CLOSE_WAIT`와 같은 상태에 있는 연결 수를 확인합니다.

Linux에서 다음 명령을 실행할 수 있습니다.
```bash
netstat -nap
```

Windows에서는 서로 다른 인수 플래그를 사용하여 동일한 명령을 실행할 수 있습니다.
```bash
netstat -abn
```

Azure Cosmos DB 엔드포인트에 대한 연결로만 결과를 필터링합니다.

`ESTABLISHED` 상태에서 Azure Cosmos DB 엔드포인트에 대한 연결 수는 구성된 연결 풀 크기보다 크지 않아야 합니다.

Azure Cosmos DB 엔드포인트에 대한 많은 연결이 `CLOSE_WAIT` 상태일 수 있습니다. 1,000개가 넘을 수 있습니다. 이렇게 숫자가 높으면 연결이 설정되었다가 빠르게 삭제되는 것을 나타냅니다. 이 경우 잠재적으로 문제가 발생할 수 있습니다. 자세한 내용은 [일반적인 문제 및 해결 방법] 섹션을 참조하세요.

 <!--Anchors-->
[일반적인 문제 및 해결 방법]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[호스트 컴퓨터의 연결 제한]: #connection-limit-on-host
[Azure SNAT(PAT) 포트 고갈]: #snat
