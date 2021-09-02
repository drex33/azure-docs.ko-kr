---
title: Azure Cosmos DB 서비스를 사용할 수 없음 예외 문제 해결
description: Azure Cosmos DB 서비스를 사용할 수 없음 예외를 진단하고 해결하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 5b734c9d1ab4a64a15e8b73d81d02a6066beb35e
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117196"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Azure Cosmos DB 서비스를 사용할 수 없음 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

SDK가 Azure Cosmos DB에 연결할 수 없습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 서비스를 사용할 수 없음 예외에 대한 알려진 원인과 솔루션이 나와 있습니다.

### <a name="the-required-ports-are-being-blocked"></a>필수 포트가 차단되어 있음
모든 [필수 포트](sql-sdk-connection-modes.md#service-port-ranges)가 사용하도록 설정되어 있는지 확인합니다.

### <a name="client-side-transient-connectivity-issues"></a>클라이언트 측 임시 연결 문제
서비스를 사용할 수 없음 예외는 시간 초과를 일으키는 임시적인 연결 문제가 있는 경우에 발생할 수 있습니다. 일반적으로 이러한 시나리오와 관련된 스택 추적에는 `TransportException` 오류가 포함됩니다. 예를 들면 다음과 같습니다.

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

문제를 해결하려면 [요청 시간 초과 문제 해결 단계](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)를 따릅니다.

### <a name="service-outage"></a>서비스 중단
[Azure 상태](https://status.azure.com/status)를 확인하여 진행 중인 문제가 있는지 확인합니다.


## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.