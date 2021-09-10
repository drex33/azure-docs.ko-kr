---
title: Java v4 SDK를 사용하여 Azure Cosmos DB 서비스를 사용할 수 없음 예외 문제 해결
description: Java v4 SDK를 사용하여 Azure Cosmos DB 서비스를 사용할 수 없음 예외를 진단하고 수정하는 방법을 알아봅니다.
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 162b311879a5096966f1bd7f88313b93da809462
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123036248"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Azure Cosmos DB Java v4 SDK 서비스 사용할 수 없음 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Java v4 SDK가 Azure Cosmos DB에 연결할 수 없습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 서비스를 사용할 수 없음 예외에 대한 알려진 원인과 솔루션이 나와 있습니다.

### <a name="the-required-ports-are-being-blocked"></a>필수 포트가 차단되어 있음
모든 [필수 포트](sql-sdk-connection-modes.md#service-port-ranges)가 사용하도록 설정되어 있는지 확인합니다.

### <a name="client-initialization-failure"></a>클라이언트 초기화 실패
SDK가 Cosmos DB 인스턴스와 대화할 수 없는 경우 다음 예외가 적중합니다. 이는 일반적으로 방화벽 규칙이 요청을 차단하는 것과 같은 일부 보안 프로토콜을 가리킵니다.

```java
 java.lang.RuntimeException: Client initialization failed. Check if the endpoint is reachable and if your auth token is valid
```

SDK가 Cosmos DB 계정과 통신할 수 있는지 확인하려면 애플리케이션이 호스팅되는 위치에서 다음 명령을 실행합니다. 실패하는 경우 요청을 차단하는 방화벽 규칙 또는 기타 보안 기능을 가리킵니다. 성공하는 경우 SDK는 Cosmos DB 계정과 통신할 수 있어야 합니다.
```
telnet myCosmosDbAccountName.documents.azure.com 443
```

### <a name="client-side-transient-connectivity-issues"></a>클라이언트 측 임시 연결 문제
서비스를 사용할 수 없음 예외는 시간 초과를 일으키는 임시적인 연결 문제가 있는 경우에 발생할 수 있습니다. 일반적으로 이 시나리오와 관련된 스택 추적에는 진단 세부 정보와 함께 `ServiceUnavailableException` 오류가 포함됩니다. 예를 들면 다음과 같습니다.

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

문제를 해결하려면 [요청 시간 초과 문제 해결 단계](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps)를 따릅니다.

### <a name="service-outage"></a>서비스 중단
[Azure 상태](https://status.azure.com/status)를 확인하여 진행 중인 문제가 있는지 확인합니다.


## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.