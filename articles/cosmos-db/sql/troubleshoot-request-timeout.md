---
title: Azure Cosmos DB 서비스 요청 제한 시간 예외 문제 해결
description: Azure Cosmos DB 서비스 요청 시간 초과 예외를 진단하고 수정하는 방법을 알아봅니다.
author: j82w
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 8106466d60f81e45d09aafec118d6ddf1ccd5efb
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123116905"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Azure Cosmos DB 요청 시간 초과 예외 진단 및 문제 해결
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

Azure Cosmos DB가 HTTP 408 요청 시간 초과를 반환했습니다.

## <a name="troubleshooting-steps"></a>문제 해결 단계
다음 목록에는 요청 시간 초과 예외에 대한 알려진 원인과 솔루션이 포함되어 있습니다.

### <a name="check-the-sla"></a>SLA 확인
[Azure Cosmos DB 모니터링](../monitor-cosmos-db.md)을 확인하여 408 예외 수가 Azure Cosmos DB SLA를 위반하는지 확인합니다.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>솔루션 1: Azure Cosmos DB SLA를 위반하지 않았습니다.
애플리케이션은 이 시나리오를 처리하고 이러한 일시적인 오류를 다시 시도해야 합니다.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>솔루션 2: Azure Cosmos DB SLA를 위반했습니다.
[Azure 지원](https://aka.ms/azure-support)에 문의하세요.
 
### <a name="hot-partition-key"></a>핫 파티션 키
Azure Cosmos DB는 전체 프로비저닝된 처리량을 물리적 파티션에 고르게 분산합니다. 핫 파티션이 있는 경우 물리적 파티션에 있는 하나 이상의 논리적 파티션 키가 모든 물리적 파티션의 초당 요청 단위(RU/s)를 사용하고 있습니다. 동시에 다른 물리 분할의 RU는 사용되지 않습니다. 증상으로, 사용된 총 RU/s는 데이터베이스 또는 컨테이너에서 프로비저닝된 전체 RU/s보다 적습니다. 핫 논리적 파티션 키에 대한 요청에 대한 제한(429s)이 계속 표시됩니다. [정규화된 RU 소비 메트릭](../monitor-normalized-request-units.md)을 사용하여 워크로드에 핫 파티션이 발생하는지 확인합니다. 

#### <a name="solution"></a>해결책:
요청 볼륨과 스토리지를 고르게 분배하는 좋은 파티션 키를 선택합니다. [파티션 키 변경](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/) 방법에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계
* Azure Cosmos DB .NET SDK를 사용하고 있는 경우 문제를 [진단 및 해결](troubleshoot-dot-net-sdk.md)합니다.
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) 및 [.NET v2](performance-tips.md)의 성능 지침에 대해 알아봅니다.
* Azure Cosmos DB Java v4 SDK를 사용할 때 발생하는 문제를 [진단하고 해결](troubleshoot-java-sdk-v4-sql.md)합니다.
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md)의 성능 지침에 대해 알아봅니다.