---
title: Cassandra용 Azure Cosmos DB API에 대한 속도 제한 오류를 방지합니다.
description: Cassandra 작업에 대한 Azure Cosmos DB API가 SSR(서버 쪽 다시 시도) 기능으로 속도 제한 오류에 도달하지 못하도록 방지
author: dileepraotv-github
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 10/11/2021
ms.author: turao
ms.openlocfilehash: 469dc3f87bb7c783f2c3138e2bcf592c85312006
ms.sourcegitcommit: af303268d0396c0887a21ec34c9f49106bb0c9c2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2021
ms.locfileid: "129760557"
---
# <a name="prevent-rate-limiting-errors-for-azure-cosmos-db-api-for-cassandra-operations"></a>Cassandra 작업에 대한 Azure Cosmos DB API에 대한 속도 제한 오류 방지
[!INCLUDE[appliesto-cassandra-api](../includes/appliesto-cassandra-api.md)]

모든 데이터베이스 작업의 비용은 Azure Cosmos DB에서 정규화되며 RU(요청 단위)로 표현됩니다. 요청 단위는 Azure Cosmos DB에서 지원하는 데이터베이스 작업을 수행하는 데 필요한 CPU, IOPS, 메모리와 같은 시스템 리소스를 추상화하는 성능 통화입니다.

Azure Cosmos DB Cassandra API 작업은 테이블의 처리량 제한(RU)을 초과하는 경우 속도 제한(OverloadedException/429) 오류로 실패할 수 있습니다. 이 문제는 Cassandra API 권장 사항 문서의 재시도 정책에 설명된 대로 클라이언트 쪽에서 처리할 수 [있습니다.](https://devblogs.microsoft.com/cosmosdb/cassandra-api-java/#retry-policy) 속도 제한 오류로 인해 오류를 처리하기 위해 클라이언트 재시도 정책을 구현할 수 없는 경우 짧은 지연 후에 테이블의 처리량 제한을 초과하는 작업이 자동으로 다시 시도되는 SSR(서버 쪽 다시 시도) 기능을 사용할 수 있습니다. 이 설정은 계정 수준 설정이며 계정의 모든 키 공간 및 테이블에 적용됩니다.

## <a name="use-the-azure-portal"></a>Azure Portal 사용

1. [Azure Portal](https://portal.azure.com/)에 로그인합니다.

2. Cassandra용 Azure Cosmos DB API 계정으로 이동합니다.

3. **설정** 섹션 아래의 **기능** 창으로 이동합니다.

4. **서버 쪽 다시 시도** 를 선택합니다.

5. 계정의 모든 컬렉션에 대해 이 기능을 사용하려면 **사용** 을 클릭합니다.

:::image type="content" source="./media/prevent-rate-limiting-errors/prevent-rate-limiting-errors.png" alt-text="Cassandra용 Azure Cosmos DB API의 서버 쪽 재시도 기능 스크린샷":::

## <a name="use-the-azure-cli"></a>Azure CLI 사용

1. 계정에 대해 SSR을 이미 사용하도록 설정되었는지 확인합니다.

   ```azurecli-interactive
   az cosmosdb show --name accountname --resource-group resourcegroupname
   ```

2. **사용** 데이터베이스 계정의 모든 테이블에 대한 SSR입니다. 이 변경 사항이 적용되려면 최대 15분이 소요될 수 있습니다.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra DisableRateLimitingResponses
   ```

3. 다음 명령은 기능 목록에서 를 제거하여 데이터베이스 계정의 모든 테이블에 대해 서버 쪽 재시도를 **사용하지 않도록 설정합니다.** `DisableRateLimitingResponses` 이 변경 사항이 적용되려면 최대 15분이 소요될 수 있습니다.

   ```azurecli-interactive
   az cosmosdb update --name accountname --resource-group resourcegroupname --capabilities EnableCassandra
   ```

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="how-are-requests-retried"></a>요청을 다시 시도하는 방법

60초의 시간 제한에 도달할 때까지 요청은 계속해서 다시 시도됩니다. 시간 제한에 도달하면 클라이언트는 그에 따라 읽기 또는 쓰기 시간 제한 오류를 수신합니다.

### <a name="when-is-ssr-most-beneficial"></a>SSR이 가장 유용한 시기는 언제인가요?

SSR(서버 쪽 재시도)은 제한 오류를 방지할 수 있는 1분 미만의 짧은 기간 동안 급격한 급증이 있을 때 가장 유용합니다. 작업 부하가 증가하고 지정된 RU 이상으로 지속적으로 유지되는 경우 SSR은 많은 도움이 되지 않습니다. RU를 적절하게 늘리는 것이 제안입니다.

### <a name="suggested-client-side-settings"></a>제안된 클라이언트 쪽 설정은 무엇인가요?

SSR을 사용하도록 설정한 후 클라이언트 앱은 서버 재시도 60초 설정을 초과하여 읽기 시간 초과를 늘려야 합니다. 90초가 더 안전한 쪽에 있는 것이 좋습니다.

SocketOptions setReadTimeoutMillis DefaultDriverOption.REQUEST_TIMEOUT


### <a name="how-can-i-monitor-the-effects-of-a-server-side-retry"></a>서버 쪽 다시 시도의 영향을 모니터링하려면 어떻게 해야 하나요?

Cosmos DB 메트릭 창에서 서버 쪽에서 다시 시도된 속도 제한 오류(429)를 볼 수 있습니다. 이러한 오류는 서버 쪽에서 처리되고 다시 시도되므로 SSR을 사용하도록 설정한 경우 클라이언트로 이동하지 않습니다.

[Cosmos DB 리소스 로그](../cosmosdb-monitor-resource-logs.md)에서 *estimatedDelayFromRateLimitingInMilliseconds* 가 포함된 로그 항목을 검색할 수 있습니다.

### <a name="will-server-side-retry-affect-my-consistency-level"></a>서버 쪽 다시 시도가 일관성 수준에 영향을 주나요?

서버 쪽 재시도는 일관성 수준에 영향을 주지 않습니다. 요청은 속도가 제한된 경우 서버 쪽에서 다시 시도됩니다(오류 429).

### <a name="does-server-side-retry-affect-any-type-of-error-that-my-client-might-receive"></a>서버 쪽 다시 시도는 클라이언트가 받을 수 있는 오류 형식에 영향을 주나요?

아니요, 서버 쪽 다시 시도는 서버 쪽에서 다시 시도하여 속도 제한 오류(429)에만 영향을 미칩니다. 이 기능을 통해 클라이언트 애플리케이션에서 속도 제한 오류를 처리할 필요가 없습니다. 모든 [기타 오류](troubleshoot-common-issues.md)는 클라이언트로 이동합니다.

## <a name="next-steps"></a>다음 단계

일반적인 오류 문제 해결에 대해 자세히 알아보려면 다음 문서를 참조하세요.

* [Cassandra용 Azure Cosmos DB API의 일반적인 문제 해결](troubleshoot-common-issues.md)


다음 문서를 참조하여 Azure Cosmos DB에서 처리량을 프로비저닝하는 방법을 알아보세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](../request-units.md)
* [컨테이너 및 데이터베이스의 처리량 프로비전](../how-to-provision-throughput-cassandra.md) 
* [파티션 키 모범 사례](../cassandra-partitioning.md)

